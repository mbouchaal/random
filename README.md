# 🎯 SOLUTION COMPLÈTE - VALIDATION TABLEAU

## 📋 ARCHITECTURE PROPOSÉE

**JSON pour les données de test** (plus flexible que static list)

-----

## 1️⃣ STRUCTURE JSON

**FICHIER:** `src/test/resources/testdata/suiviFlux_expectedData.json`

```json
{
  "testCase1_FilterReceiver_TIPS": [
    {
      "Identifiant Fichier": "RAG2AYPv5JDgriR7l4T32DBYO9HjTjhL8",
      "Emetteur": "CORE1FR",
      "Receveur": "TIPS",
      "Nombre": "1",
      "Control Sum": "700886.00",
      "AS Procedure": "5000_ROUTED_AR",
      "Statut": "Pending",
      "Statut de règlement": "",
      "Notification": "",
      "Horedatage de traitement": "2025/11/26 20:57:14.350"
    },
    {
      "Identifiant Fichier": "4Q3tEvwaaXFQUC5rLqMT2DMHR7DvtHhrTH",
      "Emetteur": "CORE1FR",
      "Receveur": "TIPS",
      "Nombre": "1",
      "Control Sum": "744879.00",
      "AS Procedure": "5000_ROUTED_AR",
      "Statut": "Pending",
      "Statut de règlement": "",
      "Notification": "",
      "Horedatage de traitement": "2025/11/26 20:54:38.547"
    }
  ],
  "testCase2_FilterEmetteur_CORE1FR": [
    {
      "Emetteur": "CORE1FR",
      "Receveur": "T2_RTGS"
    }
  ]
}
```

-----

## 2️⃣ CLASSE UTILITAIRE JSON

**FICHIER:** `src/test/java/com/robotCore/utils/JsonDataReader.java`

```java
package com.robotCore.utils;

import com.fasterxml.jackson.databind.JsonNode;
import com.fasterxml.jackson.databind.ObjectMapper;
import java.io.File;
import java.io.IOException;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class JsonDataReader {
    
    private static final ObjectMapper mapper = new ObjectMapper();
    
    /**
     * Charge les données attendues depuis JSON
     * @param testCaseName Nom du test case dans le JSON
     * @return Liste de Map (chaque Map = une ligne attendue)
     */
    public static List<Map<String, String>> loadExpectedData(String testCaseName) {
        List<Map<String, String>> expectedData = new ArrayList<>();
        
        try {
            File jsonFile = new File("src/test/resources/testdata/suiviFlux_expectedData.json");
            JsonNode root = mapper.readTree(jsonFile);
            JsonNode testCase = root.get(testCaseName);
            
            if (testCase != null && testCase.isArray()) {
                for (JsonNode row : testCase) {
                    Map<String, String> rowData = new HashMap<>();
                    row.fields().forEachRemaining(field -> 
                        rowData.put(field.getKey(), field.getValue().asText())
                    );
                    expectedData.add(rowData);
                }
            }
        } catch (IOException e) {
            System.err.println("Erreur lecture JSON: " + e.getMessage());
        }
        
        return expectedData;
    }
}
```

-----

## 3️⃣ MÉTHODE DE VÉRIFICATION LIGNE PAR LIGNE

**FICHIER:** `SuiviFluxPage.java`

```java
/**
 * Vérifie que les lignes du tableau correspondent aux données attendues
 * @param expectedData Liste de Map (données attendues depuis JSON)
 * @return true si toutes les lignes correspondent
 */
public boolean verifyTableData(List<Map<String, String>> expectedData) {
    List<List<String>> actualRows = getResultTableRowsValues();
    
    if (actualRows.size() != expectedData.size()) {
        System.err.println("❌ Nombre lignes différent: actual=" + actualRows.size() + ", expected=" + expectedData.size());
        return false;
    }
    
    for (int i = 0; i < expectedData.size(); i++) {
        Map<String, String> expectedRow = expectedData.get(i);
        List<String> actualRow = actualRows.get(i);
        
        // Parcourir chaque colonne attendue
        for (Map.Entry<String, String> entry : expectedRow.entrySet()) {
            String columnName = entry.getKey();
            String expectedValue = entry.getValue();
            
            // Trouver l'index de la colonne
            int columnIndex = EXPECTED_HEADERS.indexOf(columnName);
            
            if (columnIndex == -1) {
                System.err.println("❌ Colonne inconnue: " + columnName);
                return false;
            }
            
            String actualValue = actualRow.get(columnIndex);
            
            if (!expectedValue.equals(actualValue)) {
                System.err.println("❌ Ligne " + (i+1) + ", colonne '" + columnName + "': expected='" + expectedValue + "', actual='" + actualValue + "'");
                return false;
            }
        }
        
        System.out.println("✅ Ligne " + (i+1) + " valide");
    }
    
    System.out.println("✅ Toutes les lignes sont valides");
    return true;
}
```

-----

## 4️⃣ MÉTHODE GÉNÉRIQUE - VÉRIFIER COLONNE UNIFORME

**FICHIER:** `SuiviFluxPage.java`

```java
/**
 * Vérifie qu'une colonne contient uniquement la valeur attendue (pas de valeur intruse)
 * @param columnIndex Index de la colonne (0-11)
 * @param expectedValue Valeur attendue (ex: "TIPS")
 * @return true si toutes les lignes ont cette valeur, false si intruse détectée
 */
public boolean verifyColumnUniformValue(int columnIndex, String expectedValue) {
    List<List<String>> rows = getResultTableRowsValues();
    List<String> intruders = new ArrayList<>();
    
    System.out.println("🔍 Vérification colonne [" + EXPECTED_HEADERS.get(columnIndex) + "] = '" + expectedValue + "'");
    
    for (int i = 0; i < rows.size(); i++) {
        List<String> row = rows.get(i);
        String actualValue = row.get(columnIndex);
        
        if (!actualValue.equals(expectedValue)) {
            intruders.add("Ligne " + (i+1) + ": '" + actualValue + "'");
            System.err.println("❌ INTRUSE détectée - Ligne " + (i+1) + ": expected='" + expectedValue + "', actual='" + actualValue + "'");
        }
    }
    
    if (intruders.isEmpty()) {
        System.out.println("✅ Colonne [" + EXPECTED_HEADERS.get(columnIndex) + "] uniforme: toutes les valeurs = '" + expectedValue + "'");
        return true;
    } else {
        System.err.println("❌ " + intruders.size() + " valeur(s) intruse(s) détectée(s):");
        intruders.forEach(System.err::println);
        return false;
    }
}
```

-----

## 5️⃣ UTILISATION DANS TEST

```java
@Test
public void test_VerifyTableWithJSON() {
    // Navigation et filtre
    RUNNER.pom.getSuiviFluxPage().searchByReceiver("TIPS");
    RUNNER.pom.getSuiviFluxPage().clickOnSearchBtn();
    SeleniumBase.waitFor(2000);
    
    // 1️⃣ Charger les données attendues depuis JSON
    List<Map<String, String>> expectedData = JsonDataReader.loadExpectedData("testCase1_FilterReceiver_TIPS");
    
    // 2️⃣ Vérifier ligne par ligne
    boolean dataValid = RUNNER.pom.getSuiviFluxPage().verifyTableData(expectedData);
    softAssert.assertTrue(dataValid, "Les données du tableau doivent correspondre au JSON");
    
    // 3️⃣ Vérifier que la colonne "Receveur" (index 2) contient UNIQUEMENT "TIPS"
    boolean columnValid = RUNNER.pom.getSuiviFluxPage().verifyColumnUniformValue(2, "TIPS");
    softAssert.assertTrue(columnValid, "La colonne Receveur ne doit contenir que TIPS");
    
    softAssert.assertAll();
}

@Test
public void test_DetectIntruderInColumn() {
    // Cas où on s'attend à trouver une intruse
    RUNNER.pom.getSuiviFluxPage().searchByEmetteur("CORE1FR");
    RUNNER.pom.getSuiviFluxPage().clickOnSearchBtn();
    SeleniumBase.waitFor(2000);
    
    // Vérifier colonne Emetteur (index 1) = "CORE1FR"
    boolean valid = RUNNER.pom.getSuiviFluxPage().verifyColumnUniformValue(1, "CORE1FR");
    
    softAssert.assertTrue(valid, "Aucune valeur intruse ne doit être présente dans la colonne Emetteur");
    
    softAssert.assertAll();
}
```

-----

## 📌 AVANTAGES DE CETTE SOLUTION

✅ **JSON flexible** : Ajouter/modifier cas de test sans recompiler  
✅ **Vérification précise** : Compare colonne par colonne  
✅ **Détection intruses** : Chasse les valeurs non filtrées  
✅ **Logs clairs** : Messages explicites sur les différences  
✅ **Générique** : Fonctionne pour n’importe quelle colonne

-----

**Teste et dis-moi si ça marche du premier coup !** 🚀​​​​​​​​​​​​​​​​