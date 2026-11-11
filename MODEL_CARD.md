# Model Card - fraude (version servie par l'alias production)

## Modele
Nom et version dans le registre MLflow : fraude, version 1 (alias production)
Famille et reglages : RandomForestClassifier, n_estimators=500, max_depth=16, scikit-learn 1.9.1
Run d'origine (MLflow) : a2005c98 (identifiant complet : a2005c98e9134a0f9c9971c624ef21b0)

## Objectif et usage prevu
Detecter les paiements potentiellement frauduleux a partir de 4 variables connues au moment de la decision (montant, anciennete du client, nombre de transactions sur 24 h, paiement de nuit) et alerter pour une verification.

## Usages exclus
Bloquer definitivement un compte ou refuser un paiement sans revue humaine. Ne pas utiliser sur des donnees reelles ni sur une population differente de celle d'entrainement sans re-evaluation.

## Donnees
Fichier data/transactions.csv, 5000 lignes, 568 fraudes (11,4 %), empreinte md5 (tag donnees_md5) : cf9f6f86c141e7df32c61715c493483f
Origine des donnees : synthetiques (generees par un script a graine fixe, donnees.py du TP2), non reelles.

## Performances (jeu de test, 1 250 transactions)
F1 : 0,8669   precision : 0,8837   rappel : 0,8507   AUC : 0,9872
Matrice de confusion : [[1101, 15], [20, 114]] (114 fraudes detectees sur 134, 15 faux positifs, 20 fraudes manquees)

## Methodes d'explicabilite
Globale : importance par permutation (F1, 10 repetitions) et SHAP TreeExplainer sur 400 transactions de test.
Locale : SHAP (waterfall) et LIME (2 000 voisins, graine 0) sur la transaction de Mme Benali.
Variables les plus influentes : montant (permutation 0,593 ; SHAP moyen 0,1157), puis anciennete, freq_24h et nuit, tres proches entre elles.

## Comparaison avec un modele interpretable
Regression logistique avec log10(montant) : F1 0,8593, 113 fraudes detectees sur 134 (contre 0,8669 et 114 pour la foret). Avec le montant brut : F1 0,7407. Arbre de profondeur 3 : F1 0,5737, 72 fraudes detectees.

## Limites et risques connus
Donnees synthetiques : les performances ne se transposent pas a des donnees reelles. Jeu de test de 134 fraudes seulement : un ecart de quelques fraudes est du bruit. LIME est instable (le signe de freq_24h change selon la graine) ; SHAP decrit le modele, pas une cause reelle (correlation, pas causalite). Effet de la variable nuit a surveiller (poids tres different entre explications locales).

## Recommandations d'utilisation et de surveillance
Utiliser le score comme aide a la decision avec revue humaine. Surveiller le rappel et la precision dans le temps, ainsi que la derive du montant et de la frequence. Relancer les explications a chaque changement d'alias ou de donnees.

## Tracabilite
Run des explications (experience tp7-explicabilite) : xai-fraude-v1, a1f05a52ebf9417c9aed1adacd900d53
Auteurs : Fatima Zahra Boussaidi (+ membres du groupe)   Date : 2026-11-11   Prochaine revue : 2027-02-11 (ou a chaque changement d'alias ou de donnees)