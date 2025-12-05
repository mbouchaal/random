public List<List<String>> getResultTableRowsValues() {
    List<WebElement> cells = get_ListGridCellsValues();
    List<List<String>> tableData = new ArrayList<>();
    
    System.out.println("DEBUG - Total cellules brutes: " + cells.size());
    
    // ÉTAPE 1 : Filtrer les cellules vides du début
    List<String> allCells = new ArrayList<>();
    boolean startedData = false;
    
    for (WebElement cell : cells) {
        String text = cell.getText().trim();
        
        // Commencer à collecter dès qu'on trouve la première cellule non vide
        if (!startedData && !text.isEmpty()) {
            startedData = true;
        }
        
        if (startedData) {
            allCells.add(text);
        }
    }
    
    System.out.println("DEBUG - Cellules après filtrage: " + allCells.size());
    
    // ÉTAPE 2 : Construire les lignes (12 cellules par ligne)
    for (int i = 0; i < allCells.size(); i += 12) {
        List<String> rowData = new ArrayList<>();
        
        for (int col = 0; col < 12 && (i + col) < allCells.size(); col++) {
            rowData.add(allCells.get(i + col));
        }
        
        // Ne garder que les lignes complètes (12 cellules)
        if (rowData.size() == 12) {
            tableData.add(rowData);
            System.out.println("DEBUG - Ligne " + tableData.size() + ": [" + rowData.get(0) + " | " + rowData.get(1) + " | " + rowData.get(2) + " | ...]");
        }
    }
    
    System.out.println("DEBUG - Lignes valides: " + tableData.size());
    
    return tableData;
}
