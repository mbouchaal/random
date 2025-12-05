FICHIER: SuiviFluxPage.java
🎯 MÉTHODE 1 : getRowValuesByColumnValue
/**
 * Recherche une ligne du tableau en fonction de la valeur d'une colonne donnée.
 * 
 * @param columnIndex Index de la colonne (0-11)
 * @param expectedValue Valeur attendue dans cette colonne
 * @return Liste de 12 chaînes (valeurs de la ligne) ou null si non trouvée
 * 
 * @example
 * // Trouver la ligne où la colonne "Receveur" (index 2) = "TIPS"
 * List<String> row = page.getRowValuesByColumnValue(2, "TIPS");
 * if (row != null) {
 *     System.out.println("Identifiant: " + row.get(0));
 *     System.out.println("Emetteur: " + row.get(1));
 * }
 */
public List<String> getRowValuesByColumnValue(int columnIndex, String expectedValue) {
    // Récupérer toutes les cellules via la méthode existante
    List<WebElement> cells = get_ListGridCellsValues();
    
    // Calculer le nombre de lignes (12 colonnes par ligne)
    int totalRows = cells.size() / 12;
    
    // Parcourir chaque ligne
    for (int row = 0; row < totalRows; row++) {
        int cellIndex = (row * 12) + columnIndex;
        
        if (cellIndex < cells.size()) {
            String cellValue = cells.get(cellIndex).getText().trim();
            
            // Si la valeur correspond
            if (cellValue.equals(expectedValue)) {
                List<String> rowData = new ArrayList<>();
                int startIndex = row * 12;
                
                // Récupérer les 12 cellules de cette ligne
                for (int col = 0; col < 12 && (startIndex + col) < cells.size(); col++) {
                    rowData.add(cells.get(startIndex + col).getText().trim());
                }
                
                System.out.println("✅ Ligne trouvée: " + EXPECTED_HEADERS.get(columnIndex) + "=" + expectedValue);
                return rowData;
            }
        }
    }
    
    System.err.println("❌ Aucune ligne avec " + EXPECTED_HEADERS.get(columnIndex) + "=" + expectedValue);
    return null;
}

MÉTHODE 2 : getResultTableRowsValues
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
UTILISATION
// 1️⃣ Trouver une ligne spécifique par valeur de colonne
List<String> row = RUNNER.pom.getSuiviFluxPage().getRowValuesByColumnValue(1, "CORE1FR");

if (row != null) {
    softAssert.assertEquals(row.get(0), "expectedID", "Identifiant Fichier");
    softAssert.assertEquals(row.get(2), "T2_RTGS", "Receveur");
    softAssert.assertEquals(row.get(6), "5000_ROUTED_AR", "Statut");
}

// 2️⃣ Récupérer toutes les lignes et parcourir
List<List<String>> allRows = RUNNER.pom.getSuiviFluxPage().getResultTableRowsValues();

System.out.println("Total lignes: " + allRows.size());

for (List<String> r : allRows) {
    System.out.println("Emetteur: " + r.get(1) + ", Receveur: " + r.get(2));
}

// 3️⃣ Vérifier qu'une valeur existe
boolean found = allRows.stream()
    .anyMatch(r -> r.get(1).equals("IPEU"));

softAssert.assertTrue(found, "IPEU doit exister dans la colonne Emetteur");

// 4️⃣ Compter les lignes avec un critère
long countTIPS = allRows.stream()
    .filter(r -> r.get(2).equals("TIPS"))
    .count();

System.out.println("Nombre de lignes avec TIPS: " + countTIPS);



