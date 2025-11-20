Scénario A : Cas nominal simple
Condition : BIC et DCA STET en Créditeur
Action    : SABr identifie l'IPCSM comme SE destinataire
Résultat  : Le BIC et DCA du PDI en Débiteur = cas nominal
Scénario B : BIC STET en Débiteur
Condition : BIC STET en Débiteur, DCA STET en Créditeur
Action    : SABr identifie l'IPCSM en se basant sur le DCA
Résultat  : Le BIC du PDI en Créditeur et compte du PDI en Débiteur
            (BIC inversé et DCA corrects)
Scénario C : BIC STET obsolète
Condition : BIC STET en Créditeur MAIS DCA d'un autre SE en Créditeur
Action    : SABr identifie l'IPCSM via le DCA
Résultat  : Le BIC STET obsolète et DCA du PDI en Débiteur
            (BIC STET obsolète et DCA corrects)
Scénario D : Messages avec erreur DCA
Condition : Message AR-ALQDT de TIPS avec DCA Débiteur ou Créditeur erroné
Action    : L'identification du SE est en erreur
Note      : L'identification du SE pour T2-RTGS n'est PAS impactée (pas de régression)

===================================================================================================

SCÉNARIOS DE TESTS À PRÉVOIR
Test 1 : Cas nominal (BIC et DCA corrects)
Donnée d'entrée :
- Message AR-ALQDT (camt.054) de TIPS
- BIC STET en Créditeur
- DCA STET en Créditeur

Résultat attendu :
✅ SABr identifie correctement l'IPCSM
✅ BIC et DCA du PDI en Débiteur
✅ Message ASTN routé vers le bon participant
Test 2 : BIC inversé mais DCA correct
Donnée d'entrée :
- Message AR-ALQDT (camt.054) de TIPS
- BIC STET en Débiteur (erreur de saisie)
- DCA STET en Créditeur (correct)

Résultat attendu :
✅ SABr identifie l'IPCSM via le DCA
✅ Correction : BIC du PDI en Créditeur
✅ Compte du PDI en Débiteur
✅ Message correctement routé malgré l'erreur de BIC
Test 3 : BIC obsolète mais DCA valide d'un autre SE
Donnée d'entrée :
- Message AR-ALQDT (camt.054) de TIPS
- BIC STET obsolète en Créditeur
- DCA d'un autre SE (ex: PDI) en Créditeur

Résultat attendu :
✅ SABr identifie le SE via le DCA
✅ Ignore le BIC STET obsolète
✅ DCA du PDI en Débiteur
✅ Routage vers le bon SE
Test 4 : DCA valide lié à 1 seul BIC
Donnée d'entrée :
- DCA existant dans la base
- Lié à 1 seul BIC de règlement

Résultat attendu :
✅ Conversion réussie
✅ Identification du SE correcte
Test 5 : DCA valide lié à 2 BIC différents de 2 CSM différents
Donnée d'entrée :
- DCA existant
- Lié à 2 BIC de 2 CSM différents
- CSM final déjà identifié dans le workflow

Résultat attendu :
✅ SABr prend le BIC correspondant au CSM identifié en amont
✅ Conversion réussie
Test 6 : DCA valide lié à 2 BIC du même CSM (cas d'erreur)
Donnée d'entrée :
- DCA existant
- Lié à 2 BIC différents du MÊME CSM

Résultat attendu :
❌ Erreur générée par SABr
❌ Message d'erreur explicite
❌ Pas de routage
Test 7 : DCA invalide/obsolète
Donnée d'entrée :
- DCA qui n'existe plus dans la base
- ou DCA désactivé

Résultat attendu :
❌ Erreur générée par SABr
❌ Message d'erreur "DCA n'est plus valide"
❌ Pas de routage
Test 8 : DCA Créditeur erroné
Donnée d'entrée :
- Message AR-ALQDT de TIPS
- DCA Créditeur erroné (n'appartient pas au participant)

Résultat attendu :
❌ Identification du SE en erreur
❌ Message d'erreur
Test 9 : DCA Débiteur erroné
Donnée d'entrée :
- Message AR-ALQDT de TIPS
- DCA Débiteur erroné

Résultat attendu :
❌ Identification du SE en erreur
❌ Message d'erreur
Test 10 : Non-régression T2-RTGS
Donnée d'entrée :
- Messages issus de T2-RTGS (non TIPS)

Résultat attendu :
✅ Comportement inchangé
✅ Identification SE toujours basée sur BIC
✅ Pas d'impact de la modification GDD 849
