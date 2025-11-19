# 🔍 ANALYSE COMPLÈTE

**Architecture identifiée :**
1. **Filtre** → Shadow DOM (stet-iconset-svg > iron-icon)
2. **Date Input** → Material Angular (mat-form-field > input)
3. **Calendrier** → CDK Overlay (mat-calendar)

---

# 💡 SOLUTION

## 📍 1️⃣ CRÉER SuiviFluxFiltrePage.java

**EMPLACEMENT :** Dans `com.samanger.pagesObject`

```java
package com.samanger.pagesObject;

import com.samanger.seleniumRobot.SeleniumBase;
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.support.FindBy;
import org.openqa.selenium.support.PageFactory;
import org.openqa.selenium.support.ui.ExpectedConditions;
import org.openqa.selenium.support.ui.WebDriverWait;
import java.time.Duration;

public class SuiviFluxFiltrePage {

    private WebDriver driver;
    private WebDriverWait wait;

    // ========== LOCATORS ==========
    
    @FindBy(css = ".toggle-button")
    private WebElement filterToggleButton;

    @FindBy(css = "mat-form-field#date")
    private WebElement dateFormField;

    @FindBy(css = "input[formcontrolname='date']")
    private WebElement dateInput;

    // ========== CONSTRUCTOR ==========
    
    public SuiviFluxFiltrePage(WebDriver driver) {
        this.driver = driver;
        this.wait = new WebDriverWait(driver, Duration.ofSeconds(10));
        PageFactory.initElements(driver, this);
    }

    // ========== MÉTHODES FILTRE ==========
    
    /**
     * Vérifie la présence du bouton filtre et clique dessus
     * @return true si clic réussi, false sinon
     */
    public boolean checkFilterPresenceAndClick() {
        try {
            // Attendre la présence du toggle button
            wait.until(ExpectedConditions.visibilityOf(filterToggleButton));
            
            if (!SeleniumBase.isElementPresent(filterToggleButton)) {
                System.err.println("❌ Bouton filtre non présent");
                return false;
            }
            
            // Accéder à l'icône dans shadow-root et cliquer
            WebElement filterIcon = SeleniumBase.getShadowRoot_ChildByCssSelector(
                filterToggleButton,
                "iron-icon[id='stet-icons:filter']"
            );
            
            if (filterIcon == null) {
                System.err.println("❌ Icône filtre introuvable dans shadow-root");
                return false;
            }
            
            filterIcon.click();
            System.out.println("✅ Clic sur bouton filtre effectué");
            return true;
            
        } catch (Exception e) {
            System.err.println("❌ Erreur clic filtre: " + e.getMessage());
            return false;
        }
    }

    // ========== MÉTHODES CALENDRIER ==========
    
    /**
     * Vérifie la présence du champ date, clique dessus et vérifie l'ouverture du calendrier
     * @return true si calendrier ouvert, false sinon
     */
    public boolean checkDateFieldAndOpenCalendar() {
        try {
            // 1️⃣ Vérifier présence du champ date
            wait.until(ExpectedConditions.visibilityOf(dateFormField));
            
            if (!SeleniumBase.isElementPresent(dateInput)) {
                System.err.println("❌ Champ date non présent");
                return false;
            }
            
            System.out.println("✅ Champ date présent");
            
            // 2️⃣ Cliquer sur le champ date
            dateInput.click();
            System.out.println("✅ Clic sur champ date effectué");
            
            // 3️⃣ Attendre et vérifier l'ouverture du calendrier
            WebElement calendar = wait.until(
                ExpectedConditions.visibilityOfElementLocated(
                    By.cssSelector("mat-calendar")
                )
            );
            
            if (calendar != null && calendar.isDisplayed()) {
                System.out.println("✅ Calendrier ouvert et visible");
                return true;
            } else {
                System.err.println("❌ Calendrier non visible après clic");
                return false;
            }
            
        } catch (Exception e) {
            System.err.println("❌ Erreur ouverture calendrier: " + e.getMessage());
            return false;
        }
    }

    /**
     * Vérifie si le calendrier est actuellement ouvert
     * @return true si ouvert, false sinon
     */
    public boolean isCalendarOpen() {
        try {
            WebElement calendar = driver.findElement(By.cssSelector("mat-calendar"));
            return calendar != null && calendar.isDisplayed();
        } catch (Exception e) {
            return false;
        }
    }
}
```

---

## 📍 2️⃣ AJOUTER DANS PageFactory (si existant)

**EMPLACEMENT :** Dans votre classe PageFactory/POM

```java
private SuiviFluxFiltrePage suiviFluxFiltrePage;

public SuiviFluxFiltrePage getSuiviFluxFiltrePage() {
    if (suiviFluxFiltrePage == null) {
        suiviFluxFiltrePage = new SuiviFluxFiltrePage(driver);
    }
    return suiviFluxFiltrePage;
}
```

---

## 📍 3️⃣ UTILISATION DANS SABRIHM_70.java

**EMPLACEMENT :** Dans votre test, après fermeture popup

```java
@Test(priority=2)
void tableauDeBordValidationElementCommuns() {
    
    // Fermeture popup
    boolean popupClosed = RUNNER.pom.getHomePage().closePaperDialogIfPresent();
    if (popupClosed) {
        StepStatus.setInfoResult(RUNNER.extentnode, "✅ Popup fermé");
    }
    
    // Navigation
    RUNNER.pom.getHomePage().get_SuiviFluxMenu();
    
    // ========== TESTS FILTRE ==========
    
    // Vérifier et cliquer sur filtre
    boolean filterClicked = RUNNER.pom.getSuiviFluxFiltrePage().checkFilterPresenceAndClick();
    softAssert.assertTrue(filterClicked, "Le bouton filtre doit être présent et cliquable");
    StepStatus.setInfoResult(RUNNER.extentnode, "✅ Filtre cliqué avec succès");
    
    // ========== TESTS CALENDRIER ==========
    
    // Vérifier présence date et ouvrir calendrier
    boolean calendarOpened = RUNNER.pom.getSuiviFluxFiltrePage().checkDateFieldAndOpenCalendar();
    softAssert.assertTrue(calendarOpened, "Le calendrier doit s'ouvrir après clic sur date");
    StepStatus.setInfoResult(RUNNER.extentnode, "✅ Calendrier ouvert avec succès");
    
    // Vérifier que le calendrier est bien ouvert
    boolean isOpen = RUNNER.pom.getSuiviFluxFiltrePage().isCalendarOpen();
    softAssert.assertTrue(isOpen, "Le calendrier doit rester visible");
    
    // ... suite des tests ...
    
    softAssert.assertAll();
}
```

---

# ⚡ POINTS CLÉS

1. ✅ **SuiviFluxFiltrePage.java** créée (page dédiée filtre)
2. ✅ **checkFilterPresenceAndClick()** gère Shadow DOM
3. ✅ **checkDateFieldAndOpenCalendar()** vérifie + ouvre + confirme
4. ✅ **isCalendarOpen()** vérification supplémentaire
5. ✅ Gestion d'erreurs robuste (try-catch)
6. ✅ Logs détaillés pour debugging

---

# 🚀 PRÊT POUR LA SUITE

**En attente des sélecteurs pour :**
- Navigation mois/année
- Sélection date début
- Sélection date fin
- Validation plage de dates

**Envoyez les éléments suivants !** 📸
