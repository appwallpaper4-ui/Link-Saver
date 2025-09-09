<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Link Saver</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
        }
        /* Simple animation for link cards appearing */
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(-10px); }
            to { opacity: 1; transform: translateY(0); }
        }
        .link-card {
            animation: fadeIn 0.3s ease-out;
        }
        /* Custom scrollbar for a cleaner look */
        ::-webkit-scrollbar {
            width: 8px;
        }
        ::-webkit-scrollbar-track {
            background: #f1f5f9;
        }
        ::-webkit-scrollbar-thumb {
            background: #94a3b8;
            border-radius: 10px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #64748b;
        }
    </style>
</head>
<body class="bg-slate-100 text-slate-800 min-h-screen flex items-center justify-center p-4">

    <main class="w-full max-w-2xl mx-auto">
        <div class="bg-white rounded-2xl shadow-xl p-6 sm:p-8 relative">
            
            <header class="mb-8 text-center">
                <div class="inline-flex items-center justify-center bg-blue-100 text-blue-600 rounded-full w-16 h-16 mb-4">
                    <svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                        <path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.72"></path>
                        <path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.72-1.72"></path>
                    </svg>
                </div>
                <h1 class="text-3xl sm:text-4xl font-bold text-slate-900">Link Saver</h1>
                <p class="text-slate-500 mt-2">Your personal bookmark manager.</p>
            </header>

            <form id="link-form" class="grid grid-cols-1 sm:grid-cols-2 gap-4 mb-8">
                <div class="sm:col-span-2">
                    <label for="link-name" class="block text-sm font-medium text-slate-600 mb-1">Link Name</label>
                    <input type="text" id="link-name" placeholder="e.g., Awesome Design Tools" required class="w-full px-4 py-2 bg-slate-50 border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 transition-shadow">
                </div>
                <div class="sm:col-span-2">
                    <label for="link-url" class="block text-sm font-medium text-slate-600 mb-1">URL</label>
                    <input type="url" id="link-url" placeholder="e.g., https://example.com" required class="w-full px-4 py-2 bg-slate-50 border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 transition-shadow">
                </div>
                <button type="submit" class="sm:col-span-2 w-full bg-blue-600 text-white font-semibold py-3 px-4 rounded-lg hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-blue-500 transition-transform transform hover:scale-105">
                    Add Link
                </button>
            </form>

            <hr class="border-slate-200 mb-6">
            
            <div id="links-container" class="space-y-4 max-h-[40vh] overflow-y-auto pr-2">
                <div id="empty-state" class="text-center py-8 hidden">
                    <p class="text-slate-500">No links saved yet. Add one above!</p>
                </div>
            </div>
        </div>
        
        <footer class="text-center mt-6 text-slate-500 text-sm">
            <p>&copy; 2025 Link Saver. Built with ❤️ and Tailwind CSS.</p>
        </footer>
    </main>
    
    <div id="toast" class="fixed bottom-5 right-5 bg-green-500 text-white py-2 px-5 rounded-lg shadow-lg opacity-0 translate-y-3 transition-all duration-300">
        Link added successfully!
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', () => {
            const linkForm = document.getElementById('link-form');
            const linkNameInput = document.getElementById('link-name');
            const linkUrlInput = document.getElementById('link-url');
            const linksContainer = document.getElementById('links-container');
            const emptyState = document.getElementById('empty-state');
            const toast = document.getElementById('toast');

            // --- LOCAL STORAGE & STATE MANAGEMENT ---
            
            // Fetches links from localStorage or returns an empty array
            const getLinks = () => {
                const linksJson = localStorage.getItem('savedLinks');
                try {
                    return linksJson ? JSON.parse(linksJson) : [];
                } catch (e) {
                    console.error("Error parsing links from localStorage", e);
                    return [];
                }
            };

            // Saves the links array to localStorage
            const saveLinks = (links) => {
                localStorage.setItem('savedLinks', JSON.stringify(links));
            };

            // --- UI RENDERING ---

            // Toggles the visibility of the "empty" message
            const updateEmptyState = () => {
                const links = getLinks();
                if (links.length === 0) {
                    emptyState.classList.remove('hidden');
                } else {
                    emptyState.classList.add('hidden');
                }
            };
            
            // Renders all links to the DOM
            const renderLinks = () => {
                const links = getLinks();
                linksContainer.innerHTML = ''; // Clear existing links
                
                links.forEach(link => {
                    const linkCard = createLinkCard(link);
                    linksContainer.appendChild(linkCard);
                });
                
                // Append the empty state element back after clearing
                linksContainer.appendChild(emptyState); 
                updateEmptyState();
            };

            // Creates a single link card element
            const createLinkCard = (link) => {
                const div = document.createElement('div');
                div.className = 'link-card flex items-center justify-between bg-slate-50 p-4 rounded-lg border border-slate-200';
                div.dataset.id = link.id;

                const faviconUrl = `https://www.google.com/s2/favicons?domain=${link.url}&sz=32`;
                
                div.innerHTML = `
                    <div class="flex items-center gap-4 overflow-hidden">
                        <img src="${faviconUrl}" alt="favicon" class="w-8 h-8 rounded-full bg-white p-1 object-contain flex-shrink-0" onerror="this.src='https://placehold.co/32x32/e2e8f0/64748b?text=L'; this.onerror=null;">
                        <div class="flex-1 min-w-0">
                            <p class="font-semibold text-slate-800 truncate">${link.name}</p>
                            <a href="${link.url}" target="_blank" class="text-sm text-slate-500 hover:text-blue-600 truncate block">${link.url}</a>
                        </div>
                    </div>
                    <div class="flex items-center gap-2 flex-shrink-0 ml-4">
                        <a href="${link.url}" target="_blank" class="visit-btn p-2 text-slate-500 hover:bg-blue-100 hover:text-blue-600 rounded-full transition-colors" title="Visit Link">
                            <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M18 13v6a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2V8a2 2 0 0 1 2-2h6"></path><polyline points="15 3 21 3 21 9"></polyline><line x1="10" y1="14" x2="21" y2="3"></line></svg>
                        </a>
                        <button class="copy-btn p-2 text-slate-500 hover:bg-blue-100 hover:text-blue-600 rounded-full transition-colors" title="Copy Link">
                            <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><rect x="9" y="9" width="13" height="13" rx="2" ry="2"></rect><path d="M5 15H4a2 2 0 0 1-2-2V4a2 2 0 0 1 2-2h9a2 2 0 0 1 2 2v1"></path></svg>
                        </button>
                        <button class="delete-btn p-2 text-slate-500 hover:bg-red-100 hover:text-red-600 rounded-full transition-colors" title="Delete Link">
                            <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><polyline points="3 6 5 6 21 6"></polyline><path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"></path><line x1="10" y1="11" x2="10" y2="17"></line><line x1="14" y1="11" x2="14" y2="17"></line></svg>
                        </button>
                    </div>
                `;
                return div;
            };
            
            // Shows a toast notification
            const showToast = (message, type = 'success') => {
                toast.textContent = message;
                // Reset classes
                toast.className = 'fixed bottom-5 right-5 text-white py-2 px-5 rounded-lg shadow-lg transition-all duration-300';
                
                if (type === 'success') {
                    toast.classList.add('bg-green-500');
                } else if (type === 'error') {
                    toast.classList.add('bg-red-500');
                }

                toast.classList.remove('opacity-0', 'translate-y-3');
                setTimeout(() => {
                    toast.classList.add('opacity-0', 'translate-y-3');
                }, 3000);
            };

            // --- EVENT HANDLERS ---
            
            // Handles form submission to add a new link
            const handleAddLink = (e) => {
                e.preventDefault();
                const name = linkNameInput.value.trim();
                let url = linkUrlInput.value.trim();

                if (!name || !url) {
                    alert('Please fill in both fields.');
                    return;
                }
                
                // Automatically add https:// if missing
                if (!url.match(/^https?:\/\//i)) {
                    url = 'https://' + url;
                }

                const newLink = {
                    id: Date.now(),
                    name: name,
                    url: url,
                };

                const links = getLinks();
                links.unshift(newLink); // Add to the beginning of the array
                saveLinks(links);
                
                renderLinks();
                linkForm.reset();
                linkNameInput.focus();
                showToast('Link added successfully!');
            };

            // Handles clicks on action buttons within a link card (copy, delete)
            const handleLinkCardAction = (e) => {
                const target = e.target;
                
                // Handle Copy
                const copyButton = target.closest('.copy-btn');
                if (copyButton) {
                    const linkCard = copyButton.closest('.link-card');
                    const linkUrl = linkCard.querySelector('a[href]').href;

                    // Use a temporary textarea to perform the copy command
                    const textArea = document.createElement('textarea');
                    textArea.value = linkUrl;
                    document.body.appendChild(textArea);
                    textArea.select();
                    try {
                        document.execCommand('copy');
                        showToast('Link copied to clipboard!');
                    } catch (err) {
                        console.error('Failed to copy: ', err);
                        showToast('Failed to copy link.', 'error');
                    }
                    document.body.removeChild(textArea);
                    return;
                }

                // Handle Delete
                const deleteButton = target.closest('.delete-btn');
                if (deleteButton) {
                    const linkCard = deleteButton.closest('.link-card');
                    const linkId = Number(linkCard.dataset.id);

                    let links = getLinks();
                    links = links.filter(link => link.id !== linkId);
                    saveLinks(links);

                    // Add a simple fade-out animation before removing
                    linkCard.style.transition = 'opacity 0.3s, transform 0.3s';
                    linkCard.style.opacity = '0';
                    linkCard.style.transform = 'translateX(-20px)';
                    setTimeout(() => {
                        renderLinks();
                    }, 300);
                    return;
                }
            };
            
            // --- INITIALIZATION ---
            
            linkForm.addEventListener('submit', handleAddLink);
            linksContainer.addEventListener('click', handleLinkCardAction);

            // Initial render on page load
            renderLinks();
        });
    </script>

</body>
</html>
