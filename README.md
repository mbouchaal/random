public List<List<String>> getResultTableRowsValues() {
    List<WebElement> cells = get_ListGridCellsValues();
    List<List<String>> tableData = new ArrayList<>();
    
    System.out.println("DEBUG - Nombre total de cellules: " + cells.size());
    
    // Construire les lignes
    for (int i = 0; i < cells.size(); i += 12) {
        List<String> rowData = new ArrayList<>();
        
        // Récupérer 12 cellules
        for (int col = 0; col < 12 && (i + col) < cells.size(); col++) {
            String cellValue = cells.get(i + col).getText().trim();
            rowData.add(cellValue);
        }
        
        // Vérifier qu'on a bien 12 cellules ET que la ligne n'est pas vide
        if (rowData.size() == 12) {
            boolean isEmptyRow = rowData.stream().allMatch(String::isEmpty);
            
            if (!isEmptyRow) {
                tableData.add(rowData);
                System.out.println("DEBUG - Ligne " + tableData.size() + " valide: [" + rowData.get(0) + " | " + rowData.get(1) + " | " + rowData.get(2) + " | ...]");
            }
        } else {
            System.out.println("DEBUG - Ligne ignorée (seulement " + rowData.size() + " cellules)");
        }
    }
    
    System.out.println("DEBUG - Nombre de lignes valides: " + tableData.size());
    
    return tableData;
}