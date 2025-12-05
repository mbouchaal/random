/**
 * Récupère toutes les lignes du tableau sous forme de liste de listes.
 * Chaque ligne = liste de 12 chaînes (valeurs des colonnes).
 * Filtre les lignes vides (où toutes les cellules sont vides).
 * 
 * @return Liste de lignes, où chaque ligne contient 12 valeurs
 * 
 * @example
 * List<List<String>> rows = page.getResultTableRowsValues();
 * for (List<String> row : rows) {
 *     System.out.println("Ligne " + row.get(0) + " - Emetteur: " + row.get(1) + ", Receveur: " + row.get(2));
 * }
 */
public List<List<String>> getResultTableRowsValues() {
    // Récupérer toutes les cellules via la méthode existante
    List<WebElement> cells = get_ListGridCellsValues();
    
    List<List<String>> tableData = new ArrayList<>();
    
    // Calculer le nombre de lignes potentielles (12 colonnes par ligne)
    int totalRows = cells.size() / 12;
    
    System.out.println("DEBUG - Nombre total de cellules: " + cells.size());
    System.out.println("DEBUG - Lignes potentielles (size/12): " + totalRows);
    
    // Construire chaque ligne
    for (int row = 0; row < totalRows; row++) {
        List<String> rowData = new ArrayList<>();
        int startIndex = row * 12;
        
        // Récupérer les 12 cellules de cette ligne
        for (int col = 0; col < 12 && (startIndex + col) < cells.size(); col++) {
            String cellValue = cells.get(startIndex + col).getText().trim();
            rowData.add(cellValue);
        }
        
        // Vérifier si la ligne n'est pas vide (au moins une cellule non vide)
        boolean isEmptyRow = rowData.stream().allMatch(String::isEmpty);
        
        if (!isEmptyRow) {
            tableData.add(rowData);
            System.out.println("DEBUG - Ligne " + (tableData.size()) + " ajoutée: première cellule = '" + rowData.get(0) + "'");
        } else {
            System.out.println("DEBUG - Ligne vide ignorée à l'index " + row);
        }
    }
    
    System.out.println("DEBUG - Nombre de lignes valides récupérées: " + tableData.size());
    
    return tableData;
}

//---------usage--------//
List<List<String>> rows = RUNNER.pom.getSuiviFluxPage().getResultTableRowsValues();

System.out.println("\n=== TABLEAU RÉCUPÉRÉ ===");
for (int i = 0; i < rows.size(); i++) {
    List<String> row = rows.get(i);
    System.out.println("Ligne " + (i+1) + ": [" + String.join(" | ", row) + "]");
}

// Vérifier que la première ligne commence par "1"
if (!rows.isEmpty()) {
    softAssert.assertEquals(rows.get(0).get(0), "1", "La première ligne doit commencer par 1");
}

// Vérifier qu'on a au maximum 20 lignes
softAssert.assertTrue(rows.size() <= 20, "Maximum 20 lignes par page");


List<List<String>> rows = RUNNER.pom.getSuiviFluxPage().getResultTableRowsValues();

System.out.println("\n=== TABLEAU RÉCUPÉRÉ ===");
for (int i = 0; i < rows.size(); i++) {
    List<String> row = rows.get(i);
    System.out.println("Ligne " + (i+1) + ": [" + String.join(" | ", row) + "]");
}

// Vérifier que la première ligne commence par "1"
if (!rows.isEmpty()) {
    softAssert.assertEquals(rows.get(0).get(0), "1", "La première ligne doit commencer par 1");
}

// Vérifier qu'on a au maximum 20 lignes
softAssert.assertTrue(rows.size() <= 20, "Maximum 20 lignes par page");
