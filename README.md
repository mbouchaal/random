# 🎯 ANALYSE DU BESOIN - GDD 854 (Release T2 R2026.JUN)

-----

## 📋 SYNTHÈSE DU BESOIN

**Contexte général** : T2 déploie une stratégie “T2 Unfreeze Strategy” pour faire évoluer progressivement tous les messages T2 vers la dernière version ISO 20022 (ISO MR 2020-2024), avec suivi via versions de maintenance annuelles.

**Périmètre Release R2026.JUN** : Change Request T2-0132-SYS impose l’alignement d’une liste de messages sur la version ISO MR 2020-2024.

-----

### 🔍 **3 User Stories identifiées** :

|**US**       |**Titre**                                       |**Besoin**                                                                                |**Statut**  |
|-------------|------------------------------------------------|------------------------------------------------------------------------------------------|------------|
|**SABRM-995**|Mise à niveau camt.025 vers ISO MR 20-24        |Traiter correctement les messages **camt.025 Receipt version 07** (Compte Rendu Technique)|✅ Done      |
|**SABRM-996**|Mise à niveau camt.053 vers ISO MR 20-24        |Traiter correctement les messages **camt.053 version 12** (Relevé de compte)              |❌ Unresolved|
|**SABRM-997**|Mise à niveau BAH pour T2-RTGS vers ISO MR 20-24|Appliquer le **BAH head.001 version 04** sur tous les messages vers T2-RTGS/ECONS_T2-RTGS |❌ Unresolved|

-----

## 📊 DÉTAIL PAR USER STORY

### **US SABRM-995 : camt.025 Receipt (DONE)**

**Type de message impacté** :

- **Réponse CSM FGC** (SE→AR) : `camt_025_Receipt_camt_025_001_05.xsd` → **camt.025.001.07**
- **Compte Rendu Technique sur Réponse FGC** (AR→SE) : `camt_025_Receipt_camt_025_001_05.xsd` → **camt.025.001.07**

**Critères d’acceptance** :

1. SABr génère une **réponse positive** FGC avec camt.025 v07
1. SABr génère une **réponse négative** FGC avec camt.025 v07
1. SABr traite correctement un **compte-rendu technique** reçu de T2-RTGS (Receipt camt.025 v07) sur une réponse FGC émise par SABr

**Catégorie** : Version (impact uniquement paramétrage opérationnel - MAJ RFM avec nouvelle version XSD)

-----

### **US SABRM-996 : camt.053 BankToCustomerStatement (UNRESOLVED)**

**Type de message impacté** :

- **Relevé de compte AR-DRCPT** (AR→SE) : `camt_053_BankToCustomerStatement_camt_053_001_08.xsd` → **camt.053.001.12**

**Critère d’acceptance** :

1. SABr doit traiter correctement un **relevé de compte camt.053 version 12** reçu de T2-RTGS

**Catégorie** : Version (impact uniquement paramétrage opérationnel - MAJ RFM avec nouvelle version XSD)

-----

### **US SABRM-997 : BAH (Business Application Header) pour T2-RTGS (UNRESOLVED)**

**Exigence fonctionnelle** :
Pour l’encapsulation avec le **Business Application Header (BAH)** de tout message à destination de l’**AR T2-RTGS** ou **ECONS_T2-RTGS**, il faut passer du XSD :

- `head.001.001.01 (V01)` → **head.001.001.04 (V04)**

**Critère d’acceptance** :

1. SABr doit appliquer sur tous les messages à destination de T2-RTGS, le **BAH head.001 version 04**

**Catégorie** : Version (impact uniquement paramétrage opérationnel)

-----

## 🧒 EXPLICATION POUR DÉBUTANT

### **C’est quoi le problème ?**

Imagine que SABr est un **traducteur universel** entre différentes banques qui parlent des “dialectes” différents (T2, TIPS, RTGS).

**Le problème** : Les règles de grammaire (= versions ISO 20022) ont changé côté T2-RTGS. SABr doit donc mettre à jour son “dictionnaire” pour :

1. **camt.025** (= “accusé de réception”) : Passer de la version 05 à la version **07**
1. **camt.053** (= “relevé de compte”) : Passer de la version 08 à la version **12**
1. **BAH** (= “enveloppe du message”) : Passer de la version 01 à la version **04** pour tous les messages allant vers T2-RTGS

### **Pourquoi c’est important ?**

Si SABr n’est pas à jour :

- ❌ T2-RTGS va **rejeter** les messages (mauvaise version)
- ❌ SABr ne pourra pas **lire** correctement les réponses de T2-RTGS
- ❌ Risque de **blocage des opérations** de liquidité critiques (funding/recours FGC)

### **Qu’est-ce qui change concrètement ?**

|**Élément**                              |**Avant**      |**Après**          |
|-----------------------------------------|---------------|-------------------|
|camt.025 (Receipt/Compte Rendu Technique)|Version 05     |Version **07**     |
|camt.053 (Relevé de compte)              |Version 08     |Version **12**     |
|BAH (enveloppe T2-RTGS)                  |head.001.001.01|head.001.001.**04**|

**Impact** : Uniquement le **paramétrage opérationnel** (fichiers XSD dans le RFM - Référentiel des Formats de Messages). Pas de changement dans la logique métier ou les workflows.

-----

## 🧪 STRATÉGIE DE TESTS

### **📌 Matrice de Couverture**

|**ID** |**Scénario**                                             |**Priorité**|**Type**      |**US concernée**|
|-------|---------------------------------------------------------|------------|--------------|----------------|
|**T01**|Génération réponse FGC positive avec camt.025 v07        |**P0**      |Nominal       |SABRM-995       |
|**T02**|Génération réponse FGC négative avec camt.025 v07        |**P0**      |Nominal       |SABRM-995       |
|**T03**|Réception compte-rendu technique T2-RTGS camt.025 v07    |**P0**      |Nominal       |SABRM-995       |
|**T04**|Réception relevé de compte T2-RTGS camt.053 v12          |**P0**      |Nominal       |SABRM-996       |
|**T05**|Encapsulation BAH v04 sur ASTI pain.998 vers T2-RTGS     |**P0**      |Nominal       |SABRM-997       |
|**T06**|Encapsulation BAH v04 sur ASIS pain.998 vers T2-RTGS     |**P0**      |Nominal       |SABRM-997       |
|**T07**|Encapsulation BAH v04 sur camt.050 vers T2-RTGS          |**P0**      |Nominal       |SABRM-997       |
|**T08**|Non-régression : camt.025 v07 vers TIPS (pas d’impact)   |**P1**      |Non-régression|SABRM-995       |
|**T09**|Non-régression : ASTI vers ECONS_T2-RTGS avec BAH v04    |**P1**      |Non-régression|SABRM-997       |
|**T10**|Rejet message camt.025 v05 par T2-RTGS (version obsolète)|**P2**      |Erreur        |SABRM-995       |
|**T11**|Validation XSD camt.053 v12 sur champs optionnels        |**P2**      |Limite        |SABRM-996       |
|**T12**|Vérification logs : BAH head.001.001.04 présent          |**P2**      |Contrôle      |SABRM-997       |

-----

### **📝 DÉTAIL DES SCÉNARIOS CRITIQUES (P0)**

#### **T01 : Génération réponse FGC positive avec camt.025 v07**

**Contexte** : PILBAN demande un recours au FGC (Fond de Garantie Commun) pour un règlement de compensation CORE1.

**Préconditions** :

- PILBAN émet un fichier CORE1 via CORE1FR
- SABr transforme en ASTI pain.998 format T2 et transmet
- Recours FGC accepté par T2-RTGS

**Données d’entrée** :

- Montant : 50 000 000 EUR
- Type fichier : CORE1
- Emetteur : PILBAN (BIC : FR76XXXXX)
- DCA PILBAN : FR123456789

**Actions** :

1. SABr reçoit la demande FGC
1. SABr transforme au format RTGS
1. T2-RTGS répond positivement
1. **SABr génère un Receipt camt.025 version 07** (et non v05)

**Résultats attendus** :

- ✅ Message camt.025 généré avec XSD `camt.025.001.07`
- ✅ Champ `<Hdr><MsgDefIdr>` = `camt.025.001.07`
- ✅ Réception confirmée par système destinataire (CORE1FR/XFB privé)
- ✅ Logs SABr : “Receipt camt.025 v07 generated successfully”

**Flux impacté** : Diagramme **“Règlement de compensation avec recours FGC”** (Image 5) - Étapes 19-28

-----

#### **T04 : Réception relevé de compte T2-RTGS camt.053 v12**

**Contexte** : T2-RTGS envoie un relevé de compte (notification de débit/crédit) suite à une opération de liquidité.

**Préconditions** :

- Un ajout de liquidité a été effectué depuis TIPS vers T2-RTGS
- SABr attend le relevé de compte AR-DRCPT

**Données d’entrée** :

- Message : `camt.053.001.12` (BankToCustomerStatement)
- Emetteur : T2-RTGS (via E-SMG)
- DCA concerné : FR987654321
- Montant : +10 000 000 EUR (crédit)

**Actions** :

1. T2-RTGS émet camt.053 v12
1. E-SMG route vers SABr
1. **SABr parse et valide le XSD camt.053.001.12**
1. SABr transforme au format attendu par l’IPCSM (format T2 ou CSM selon SE cible)

**Résultats attendus** :

- ✅ Parsing XSD camt.053.001.12 réussi (pas d’erreur de validation)
- ✅ Identification correcte du SE cible (étape 12 du workflow - identification)
- ✅ Transformation réussie vers format SE
- ✅ Publication vers dépôt sécurisé ou émission vers IPCSM
- ✅ Logs SABr : “camt.053 v12 processed successfully for AR-DRCPT”

**Flux impacté** : Diagramme **“Ajout de liquidité depuis l’AR TIPS”** (Image 12) - Étape 5 (Traitement SABr)

-----

#### **T05 : Encapsulation BAH v04 sur ASTI pain.998 vers T2-RTGS**

**Contexte** : SABr émet un fichier CORE1 encapsulé pour T2-RTGS.

**Préconditions** :

- PILBAN a émis un fichier CORE1 via CORE1FR
- SABr a transformé au format RTGS

**Données d’entrée** :

- Message : ASTI pain.998 format RTGS
- Destination : T2-RTGS (via E-SMG)
- Montant : 75 000 000 EUR

**Actions** :

1. SABr transforme ASTI pain.998 format T2 → format RTGS (étape 3)
1. **SABr encapsule avec BAH head.001.001.04** (et non v01)
1. SABr émet vers T2-RTGS via E-SMG

**Résultats attendus** :

- ✅ En-tête BAH présent avec `<head.001.001.04>`
- ✅ Champs BAH requis renseignés :
  - `<Fr>` (From) : SABr BIC
  - `<To>` (To) : T2-RTGS BIC
  - `<BizMsgIdr>` (Business Message Identifier) : UUID unique
  - `<MsgDefIdr>` (Message Definition Identifier) : `pain.998.001.xx`
  - `<CreDt>` (Creation Date) : Timestamp ISO
- ✅ Message accepté par T2-RTGS (pas de rejet pour version BAH incorrecte)
- ✅ Logs SABr : “BAH head.001.001.04 applied for T2-RTGS destination”

**Flux impacté** : Diagramme **“Règlement fichier de Compensation Core 1”** (Image 13) - Étapes 3-10

-----

### **⚠️ SCÉNARIOS DE NON-RÉGRESSION (P1)**

#### **T08 : camt.025 v07 vers TIPS (pas d’impact)**

**Objectif** : S’assurer que le changement de version camt.025 ne perturbe **PAS** les flux vers TIPS.

**Test** :

- Effectuer un ajout de liquidité depuis IHM IP vers TIPS
- Vérifier que le camt.025 généré reste en **version cohérente avec TIPS** (pas forcément v07 si TIPS attend v05)
- ✅ Confirmer que TIPS accepte toujours les messages

**Diagramme concerné** : “Ajout de liquidité automatique ou via IHM IP” (Image 2)

-----

#### **T09 : ASTI vers ECONS_T2-RTGS avec BAH v04**

**Objectif** : Vérifier le cas de **connexion SWIFT rompue** (use case ECONS).

**Test** :

- Simuler une rupture de connexion SWIFT
- SABr doit encapsuler les messages CORE1 vers ECONS_T2-RTGS avec **BAH v04**
- ✅ Vérifier que le traitement de secours fonctionne avec la nouvelle version BAH

**Diagramme concerné** : “Use case ECONS (connexion avec SWIFT rompue)” (Image 10)

-----

### **❌ SCÉNARIOS D’ERREUR (P2)**

#### **T10 : Rejet message camt.025 v05 par T2-RTGS**

**Objectif** : Tester la résilience de SABr face à un rejet.

**Test** :

- Forcer l’émission d’un camt.025 v05 (ancienne version) vers T2-RTGS
- ✅ T2-RTGS doit rejeter avec code erreur explicite
- ✅ SABr doit loguer l’erreur et positionner le fichier AR en état “incident émission”

-----

## 🔄 DIAGRAMMES IMPACTÉS PAR LE BESOIN

|**Diagramme (Use Case)**                                    |**Étape(s) impactée(s)**                                               |**Nature de l’impact**                                  |
|------------------------------------------------------------|-----------------------------------------------------------------------|--------------------------------------------------------|
|**Ajout de liquidité automatique ou via IHM IP** (Image 2)  |Étape 12 : Traitement SABr (transformation/routage)                    |Encapsulation BAH v04 pour messages vers T2-RTGS        |
|**Retrait de liquidité automatique ou via IHM IP** (Image 4)|Étape 12 : Traitement SABr                                             |Encapsulation BAH v04 pour messages vers T2-RTGS        |
|**Ajout de liquidité depuis l’AR TIPS** (Image 12)          |Étape 5 : Traitement SABr (réception camt.054 et transformation)       |Parsing camt.053 v12 si relevé de compte reçu de T2-RTGS|
|**Règlement fichier de Compensation Core 1** (Image 13)     |Étape 3 : Transformation RTGS<br>Étape 10 : Émission SWIFT vers T2-RTGS|Encapsulation BAH v04 sur ASTI pain.998 RTGS            |
|**Règlement de compensation avec recours FGC** (Image 5)    |Étape 19-28 : Interaction avec FGC (T2)                                |Génération camt.025 v07 sur réponse FGC                 |
|**Règlement fichier GCB (Commissions)** (Image 8)           |Étapes 3-10 : Transformation et émission RTGS                          |Encapsulation BAH v04 sur ASTI pain.998 RTGS            |
|**Use case ECONS (connexion SWIFT rompue)** (Image 10)      |Étape 3-10 : Encapsulation et émission vers ECONS_T2-RTGS              |Encapsulation BAH v04 sur messages de secours           |
|**Déversement des intérêts IP** (Image 11)                  |Étapes 6-10 : Émission vers T2-RTGS                                    |Encapsulation BAH v04 sur ASTI pain.998 RTGS            |

-----

## ❓ QUESTIONS À POSER AUX PARTIES PRENANTES

### **Questions Critiques (Bloquantes)** :

1. **Référentiel XSD** : Où sont stockés les nouveaux fichiers XSD (camt.025.001.07, camt.053.001.12, head.001.001.04) ? Quel est le processus de déploiement dans le RFM ?
1. **Stratégie de migration** :

- Bascule brutale (big bang) ou progressive ?
- Période de coexistence v05/v07 pour camt.025 ?
- Date de MEP (Mise En Production) ?

1. **Compatibilité TIPS** :

- TIPS supporte-t-il déjà camt.025 v07 ou reste-t-il en v05 ?
- Faut-il avoir une **logique différenciée** selon la destination (T2-RTGS vs TIPS) ?

1. **Comportement en cas d’erreur** :

- Si T2-RTGS rejette un message pour mauvaise version, quel est le workflow de compensation ?
- Alerte opérationnelle ? Retry automatique ? File d’attente d’incidents ?

1. **Tests d’acceptance** :

- Environnement de test disponible avec T2-RTGS en version R2026.JUN ?
- Jeux de données de test fournis par T2 (exemples de camt.053 v12, BAH v04) ?

### **Questions Complémentaires (Important)** :

1. **Volumétrie** : Combien de messages camt.025/camt.053 sont traités quotidiennement ? Impact performance ?
1. **Logging** : Nouveaux champs à loguer pour tracer les versions de messages (v05 vs v07, BAH v01 vs v04) ?
1. **Documentation** : Mise à jour des SPEC fonctionnelles, guide d’exploitation, documentation technique ?
1. **Rollback** : Stratégie de retour arrière si problème critique en production ?
1. **Dépendances** : Y a-t-il d’autres systèmes en aval (IPCSM, IHM IP, etc.) à mettre à jour pour supporter les nouvelles versions ?

-----

## 📌 CONCLUSION

**Besoin = Mise à niveau technique** des versions de messages ISO 20022 pour assurer la compatibilité avec la release **T2 R2026.JUN**.

**Impact = FAIBLE** sur le plan fonctionnel (pas de nouveau use case, pas de changement de logique métier) mais **CRITIQUE** sur le plan opérationnel (messages rejetés si versions incorrectes).

**Tests prioritaires** :

1. ✅ Génération camt.025 v07 (réponse FGC)
1. ✅ Parsing camt.053 v12 (relevé de compte)
1. ✅ Encapsulation BAH v04 (tous messages T2-RTGS)
1. ✅ Non-régression TIPS et ECONS

**Diagrammes à mettre à jour** : Tous les use cases émettant vers T2-RTGS (7 diagrammes identifiés).​​​​​​​​​​​​​​​​