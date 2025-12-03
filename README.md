(function () {
    // DFS qui traverse DOM + shadow DOM
    function collectSorters(root) {
        const sorters = [];

        function visit(node) {
            if (!node) return;

            // Si c'est un <vaadin-grid-sorter>, on garde
            if (node.nodeType === Node.ELEMENT_NODE &&
                node.localName === 'vaadin-grid-sorter') {
                sorters.push(node);
            }

            // Descendre dans le shadowRoot s'il existe
            if (node.shadowRoot) {
                visit(node.shadowRoot);
            }

            // Descendre dans les enfants classiques
            for (let child = node.firstElementChild; child; child = child.nextElementSibling) {
                visit(child);
            }
        }

        visit(root);
        return sorters;
    }

    // On limite la recherche au composant qui t’intéresse
    const host = document.querySelector('stet-vaadin-grid#grid')
              || document.querySelector('stet-vaadin-grid')
              || document;

    const sorters = collectSorters(host);

    console.log("Nb de sorters trouvés :", sorters.length);
    
    // Récupérer le texte uniquement dans la partie "content" du sorter
    const headers = sorters
        .map(s => {
            if (!s.shadowRoot) return '';
            const content = s.shadowRoot.querySelector('[part="content"]');
            return content ? content.textContent.trim() : '';
        })
        .filter(t => t); // enlever les vides

    console.log("Headers :", headers);
    return headers;
})();
