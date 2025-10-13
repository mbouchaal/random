# random

🧩 Exemple concret (Gherkin prêt à copier)
@ui @header @low
Feature: Bandeau haut - éléments généraux et langues
  Background:
    Given l'utilisateur est authentifié
    And l'application "Gestion des agents de règlement - DEV" est ouverte

  Scenario: Présence des éléments du bandeau haut
    Then le logo "stet" est visible
    And le titre de l'application est "Gestion des agents de règlement - DEV"
    And le sélecteur de langue affiche les options "FR" et "EN"
    And les icônes "utilisateur" et "notifications" sont visibles

  @medium
  Scenario Outline: Commutation de langue du bandeau haut
    When je sélectionne la langue "<lang>"
    Then le libellé du menu "<libelle_menu>" est affiché
    And le titre de la page est traduit pour "<lang>"

    Examples:
      | lang | libelle_menu  |
      | FR   | TABLEAU DE BORD |
      | EN   | DASHBOARD      |

----------------------------------------------------------------------------

@ui @page @notifications
Feature: Notifications - Présentation, filtres et liste
  Background:
    Given je suis sur la page "NOTIFICATIONS"

  @low
  Scenario: Titre et horodatage
    Then le titre de section est "NOTIFICATIONS"
    And un horodatage de page est affiché au format "dd/MM/yyyy HH:mm:ss"

  @low @buttons
  Scenario: Boutons de recherche
    Then le bouton "RÉINIT" est visible et cliquable
    And le bouton "RECHERCHE" est visible et cliquable

  @low @table
  Scenario: Structure du tableau
    Then le tableau "notifications" est visible
    And les colonnes sont:
      | Identifiant fichier |
      | Libellé de la notification |
      | Origine de la notification |
      | Utilisateur |
      | État de la notification |
      | Date de notification |
    And une pagination est visible

  @medium @filters
  Scenario Outline: Filtre simple par Type de notification
    Given je sélectionne "Type de notification" = "<type>"
    And je clique sur "RECHERCHE"
    Then le tableau affiche au moins 1 ligne
    And chaque ligne contient "<type>" dans "Libellé de la notification"

    Examples:
      | type    |
      | MSG0503 |
      | POP0121 |
      | POP0105 |

  @medium @filters
  Scenario Outline: Filtre par période
    Given je saisis la période du "<debut>" au "<fin>"
    And je clique sur "RECHERCHE"
    Then toutes les dates de la colonne "Date de notification" sont comprises entre "<debut>" et "<fin>"

    Examples:
      | debut              | fin                |
      | 09/10/2025 00:00:00| 09/10/2025 23:59:00|

  @high @filters @cross
  Scenario Outline: Croisement Type + Statut + Origine
    Given je sélectionne "Type de notification" = "<type>"
    And je sélectionne "Statut de la notification" = "<statut>"
    And je sélectionne "Origine de la notification" = "<origine>"
    When je clique sur "RECHERCHE"
    Then le tableau affiche au moins 1 ligne
    And chaque ligne contient "<type>" et "<statut>" et "<origine>"

    Examples:
      | type    | statut         | origine |
      | POP0121 | Non acquittée  | GCB     |
      | MSG0503 | Non acquittée  | CORE1FR |

  @low @footer
  Scenario: Bandeau bas - bouton Supprimer
    Then le bouton "SUPPRIMER" est visible
    And le bouton "SUPPRIMER" est désactivé tant qu’aucune ligne n’est cochée

  @medium @footer
  Scenario: Suppression d’une notification sélectionnée
    Given je coche la première ligne du tableau
    When je clique sur "SUPPRIMER"
    Then une fenêtre de confirmation s'affiche
    And en confirmant, la ligne sélectionnée disparaît du tableau

-----------------------------------------------------------------------------------------------

@ui @page @suivi_flux
Feature: Suivi des flux - Présentation, filtres, tableau et actions
  Background:
    Given je suis sur la page "SUIVI DES FLUX"

  @low
  Scenario: Titre de section
    Then le titre de section est "SUIVI DES FLUX"

  @low @filters
  Scenario: Présence des filtres et boutons
    Then les champs filtres sont visibles:
      | Identifiant Fichier |
      | Emetteur |
      | Receveur |
      | Statut |
      | Début d'horodatage |
      | Fin d'horodatage |
    And les boutons "RÉINIT" et "RECHERCHE" sont visibles et cliquables
    And l’icône de filtre avancé est visible

  @low @table
  Scenario: Structure du tableau principal
    Then le tableau "flux" est visible
    And les colonnes sont:
      | Identifiant Fichier |
      | Emetteur |
      | Receveur |
      | Nombre |
      | Control Sum |
      | AS Procedure |
      | Statut |
      | Statut de règlement |
      | Notification |
      | Horodatage de traitement |
      | Vue détaillée |
    And une pagination est visible

  @medium @table
  Scenario: Affichage des lignes détaillées
    Given la première ligne du tableau possède un indicateur de détail
    When j’ouvre la vue détaillée
    Then un sous-tableau "Suivi de l'échange" est visible avec colonnes "Type Fichier", "Horodatage de traitement", "Statut d'action", "Validateur"

  @medium @filters
  Scenario Outline: Filtre simple par Emetteur
    Given je sélectionne "Emetteur" = "<emetteur>"
    And je clique sur "RECHERCHE"
    Then chaque ligne du tableau affiche "<emetteur>" dans la colonne "Emetteur"

    Examples:
      | emetteur |
      | T2_RTGS  |
      | GCB      |
      | IPBE     |
      | IPEU     |

  @medium @filters
  Scenario Outline: Filtre simple par Statut
    Given je sélectionne "Statut" = "<statut>"
    And je clique sur "RECHERCHE"
    Then chaque ligne du tableau affiche "<statut>" dans la colonne "Statut"

    Examples:
      | statut        |
      | PUBLISHED_SE  |
      | ROUTED_AR     |
      | EMISSION_INCIDENT INVL |
      | EMISSION_INCIDENT ACSC |

  @high @filters @cross
  Scenario Outline: Croisement Emetteur + Receveur + Statut + Période
    Given je sélectionne "Emetteur" = "<emetteur>"
    And je sélectionne "Receveur" = "<receveur>"
    And je sélectionne "Statut" = "<statut>"
    And je saisis la période du "<debut>" au "<fin>"
    When je clique sur "RECHERCHE"
    Then au moins 1 ligne est affichée
    And chaque ligne correspond à "<emetteur>", "<receveur>" et "<statut>"
    And la colonne "Horodatage de traitement" est comprise entre "<debut>" et "<fin>"

    Examples:
      | emetteur | receveur | statut       | debut              | fin                |
      | T2_RTGS  | GCB      | PUBLISHED_SE | 01/10/2025 00:00:00| 09/10/2025 23:59:00|
      | GCB      | T2_RTGS  | ROUTED_AR    | 01/10/2025 00:00:00| 09/10/2025 23:59:00|

  @low @footer
  Scenario: Bandeau bas - présence des actions
    Then les boutons suivants sont visibles:
      | VALIDER |
      | DÉBLOQUER |
      | ABANDONNER |
      | RECYCLER |
      | GÉNÉRER LES FICHIERS |
      | EXPORTER |

  @medium @footer
  Scenario: Activation contextuelle des actions
    Then tous les boutons sont désactivés sans sélection de ligne
    When je coche une ligne
    Then les boutons "VALIDER", "DÉBLOQUER", "ABANDONNER", "RECYCLER" deviennent activables

  @medium @footer
  Scenario: Export des résultats
    Given au moins 1 ligne est affichée
    When je clique sur "EXPORTER"
    Then un fichier d’export est généré et téléchargé

-----------------------------------------------------------------------------------------------

@ui @page @dashboard
Feature: Tableau de bord - Plages d’activations
  Background:
    Given je suis sur la page "TABLEAU DE BORD - Plages d’activations"

  @low
  Scenario: Présence du titre et des colonnes de canaux
    Then le titre de section contient "TABLEAU DE BORD - Plages d’activations"
    And les colonnes de canaux sont visibles:
      | T2_RTGS |
      | TIPS    |

  @low
  Scenario Outline: Tuiles SE affichées avec état
    Then la ligne "<se>" est visible
    And la tuile "<se>" pour "T2_RTGS" affiche un état parmi "Connecté","Nominal"
    And les icônes "i" et "power" sont visibles dans la tuile

    Examples:
      | se     |
      | CORE1BE|
      | IPBE   |
      | GCB    |
      | IPEU   |
      | CORE1FR|

  @medium
  Scenario: Couleur d’état nominal
    Then toutes les tuiles en état "Connecté" ou "Nominal" sont colorées en vert

  @medium
  Scenario: Info tuile
    When je clique sur l’icône "i" d’une tuile
    Then une info-bulle ou un panneau d’information s’affiche avec des détails de connexion

