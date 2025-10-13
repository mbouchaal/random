🧭 0. Bandeau haut commun (Header)
N°	Description du test	Complexité	Tag
H01	Vérifier la présence du logo "stet"	@low	@header
H02	Vérifier le titre de l’application "Gestion des agents de règlement - DEV"	@low	@header
H03	Vérifier la présence du sélecteur de langue avec les options "FR" et "EN"	@low	@header
H04	Vérifier la présence des icônes "utilisateur" et "notifications"	@low	@header
H05	Changer la langue en "FR" → vérifier que les libellés sont traduits	@medium	@header
H06	Changer la langue en "EN" → vérifier que les libellés sont traduits	@medium	@header
📬 1. Page Notifications
🔹 Présentation générale
N°	Description du test	Complexité	Tag
N01	Vérifier le titre "NOTIFICATIONS" et l’horodatage affiché	@low	@page
N02	Vérifier la présence et le clic des boutons "RÉINIT" et "RECHERCHE"	@low	@buttons
N03	Vérifier la structure du tableau et la présence des colonnes attendues	@low	@table
N04	Vérifier la présence de la pagination du tableau	@low	@table
🔹 Filtres
N°	Description du test	Complexité	Tag
N05	Vérifier la présence des filtres "Identifiant fichier", "Type", "Origine", "Statut", "Période"	@low	@filters
N06	Test de filtre simple par Type de notification (MSG0503, POP0121, POP0105)	@medium	@filters
N07	Test de filtre simple par période de date/heure	@medium	@filters
N08	Test de croisement Type + Statut + Origine	@high	@filters @cross
N09	Vérifier que le bouton "RÉINIT" remet les filtres à vide	@low	@filters
🔹 Tableau
N°	Description du test	Complexité	Tag
N10	Vérifier que chaque ligne affiche bien les champs : Identifiant, Libellé, Origine, Utilisateur, État, Date	@low	@table
N11	Vérifier que les états de notification sont bien rendus (“Non acquittée”, “Acquittée”)	@low	@table
🔹 Bandeau bas
N°	Description du test	Complexité	Tag
N12	Vérifier la présence du bouton “SUPPRIMER”	@low	@footer
N13	Vérifier que “SUPPRIMER” est désactivé sans sélection	@low	@footer
N14	Sélection d’une ligne et suppression avec confirmation	@medium	@footer
🔁 2. Page Suivi des Flux
🔹 Présentation générale
N°	Description du test	Complexité	Tag
F01	Vérifier le titre "SUIVI DES FLUX"	@low	@page
F02	Vérifier la présence des champs filtres (Identifiant Fichier, Émetteur, Receveur, Statut, Période)	@low	@filters
F03	Vérifier la présence et le clic des boutons "RÉINIT" et "RECHERCHE"	@low	@buttons
F04	Vérifier la présence de l’icône filtre avancé (entonnoir)	@low	@filters
🔹 Tableau principal
N°	Description du test	Complexité	Tag
F05	Vérifier la structure du tableau (colonnes principales)	@low	@table
F06	Vérifier la pagination (flèches / numéros de page)	@low	@table
F07	Vérifier qu’une ligne peut être développée pour afficher le sous-tableau “Suivi de l’échange”	@medium	@table
F08	Vérifier la structure du sous-tableau “Suivi de l’échange”	@medium	@table
🔹 Filtres et affichage
N°	Description du test	Complexité	Tag
F09	Test de filtre simple par Émetteur	@medium	@filters
F10	Test de filtre simple par Receveur	@medium	@filters
F11	Test de filtre simple par Statut	@medium	@filters
F12	Test de filtre simple par période	@medium	@filters
F13	Test de croisement Émetteur + Receveur + Statut + Période	@high	@filters @cross
🔹 Bandeau bas (actions)
N°	Description du test	Complexité	Tag
F14	Vérifier la présence des boutons (VALIDER, DÉBLOQUER, ABANDONNER, RECYCLER, GÉNÉRER LES FICHIERS, EXPORTER)	@low	@footer
F15	Vérifier que tous les boutons sont désactivés sans sélection	@low	@footer
F16	Vérifier que les boutons s’activent quand une ligne est cochée	@medium	@footer
F17	Vérifier le clic sur “EXPORTER” génère un fichier de sortie	@medium	@footer
📊 3. Tableau de Bord (Plages d’activations)
🔹 Présentation
N°	Description du test	Complexité	Tag
D01	Vérifier le titre “TABLEAU DE BORD – Plages d’activations”	@low	@page
D02	Vérifier la présence des colonnes de canaux (T2_RTGS, TIPS)	@low	@table
🔹 Tuiles SE
N°	Description du test	Complexité	Tag
D03	Vérifier la présence des lignes SE (CORE1BE, IPBE, GCB, IPEU, CORE1FR)	@low	@table
D04	Vérifier que chaque tuile affiche un état “Connecté” / “Nominal”	@low	@table
D05	Vérifier la couleur verte pour les états “Connecté” / “Nominal”	@medium	@ui
D06	Vérifier la présence des icônes “i” et “power” sur chaque tuile	@low	@ui
D07	Cliquer sur l’icône “i” et vérifier l’affichage du panneau d’information	@medium	@ui
⚙️ 4. Bandeau bas (Footer commun)

(certains écrans n’ont pas de footer spécifique, mais pour homogénéité UI on isole les tests de rendu global)

N°	Description du test	Complexité	Tag
B01	Vérifier la cohérence du style (couleur, alignement, taille) du bandeau bas sur chaque page	@low	@footer
B02	Vérifier que le footer reste fixe au scroll	@medium	@footer
📋 Résumé global de couverture
Catégorie	Nombre de tests	Répartition
Bandeau haut commun	6	10%
Notifications	14	25%
Suivi des flux	17	35%
Tableau de bord	7	15%
Footer commun	2	5%
Total global	46 tests	100% couverture fonctionnelle UI statique