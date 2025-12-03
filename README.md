function getSorters(root) {
  let result = [];
  let walker = document.createTreeWalker(
    root,
    NodeFilter.SHOW_ELEMENT,
    null
  );
  while (walker.nextNode()) {
    if (walker.currentNode.localName === 'vaadin-grid-sorter') {
      result.push(walker.currentNode);
    }
  }
  return result;
}

let sorters = getSorters($0.getRootNode());
console.log(sorters.map(x => x.shadowRoot.textContent.trim()));

*******************
let grid = document
    .querySelector('stet-vaadin-grid')
    .shadowRoot.querySelector('vaadin-grid');

let proto = Object.getPrototypeOf(grid);
console.log(proto);

console.log(proto._columnTree);
****via api 
let grid = document
  .querySelector('stet-vaadin-grid')
  .shadowRoot.querySelector('vaadin-grid');

let proto = Object.getPrototypeOf(grid);
let tree = proto._columnTree;

let headers = tree[0].map(col => {
    let cell = col._headerCell; 
    if (!cell) return null;

    let sorter = cell._content && cell._content.firstElementChild;
    if (sorter && sorter.shadowRoot) {
        return sorter.shadowRoot.textContent.trim();
    }
    return col.header || null;
});

console.log(headers);


******
let proto = Object.getPrototypeOf(
    document.querySelector('stet-vaadin-grid')
    .shadowRoot.querySelector('vaadin-grid')
);

console.log(proto);

