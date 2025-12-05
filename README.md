/**
 * Récupère toutes les lignes du tableau sous forme de liste de listes.
 * Chaque ligne = liste de 12 chaînes (valeurs des colonnes).
 * 
 * @return Liste de lignes, où chaque ligne contient 12 valeurs
 * 
 * @example
 * List<List<String>> rows = page.getResultTableRowsValues();
 * for (List<String> row : rows) {
 *     System.out.println("Emetteur: " + row.get(1) + ", Receveur: " + row.get(2));
 * }
 */
public List<List<String>> getResultTableRowsValues() {
    // Récupérer toutes les cellules via la méthode existante
    List<WebElement> cells = get_ListGridCellsValues();
    
    List<List<String>> tableData = new ArrayList<>();
    
    // Calculer le nombre de lignes (12 colonnes par ligne)
    int totalRows = cells.size() / 12;
    
    // Construire chaque ligne
    for (int row = 0; row < totalRows; row++) {
        List<String> rowData = new ArrayList<>();
        int startIndex = row * 12;
        
        // Récupérer les 12 cellules de cette ligne
        for (int col = 0; col < 12 && (startIndex + col) < cells.size(); col++) {
            rowData.add(cells.get(startIndex + col).getText().trim());
        }
        
        tableData.add(rowData);
    }
    
    System.out.println("DEBUG - Nombre de lignes récupérées: " + tableData.size());
    
    return tableData;
}
