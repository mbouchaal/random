@Test
public void suiviFlux_FilterReceiver() {
    // Navigation et filtres...
    RUNNER.pom.getSuiviFluxPage().searchByReceiver("receiver");
    RUNNER.pom.getSuiviFluxPage().clickOnSearchBtn();
    
    // 👇 METS TON CODE ICI 👇
    
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
    
    softAssert.assertAll();
}
