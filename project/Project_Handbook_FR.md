# MASTER · MANAGEMENT DE L'INTELLIGENCE ARTIFICIELLE EN SANTÉ
# Le Projet Longitudinal
## Barcelone : tarification et conformité sur le marché de la location de courte durée

Il s'agit du projet unique qui s'étend sur l'ensemble des douze séances. Vous ne ferez pas douze exercices déconnectés. Vous construirez une seule et même analyse, et à la fin, vous la soutiendrez devant la classe.

> **Conservez précieusement ce document.** Il contient le cahier des charges, chaque livrable attendu et la grille de critères selon laquelle chacun est évalué. Aucun élément de l'évaluation de ce projet ne vous est caché.
>
> *Cours : Data Science, Machine Learning et Deep Learning · 12 séances · ~4 heures de travail personnel hebdomadaire.*

---

## Sommaire

| Partie | Contenu |
|---|---|
| **Partie 1** | **Le Cahier des Charges :** la situation, les données, les deux questions, la règle d'or, le barème d'évaluation, l'éthique et les pièges identifiés à l'avance |
| **Partie 2** | **Chaque jalon de M0 à M10 :** les livrables à rendre et la grille d'évaluation associée |
| **Partie 3** | **Les autres épreuves évaluées :** examen pratique de mi-parcours, rapport final, soutenance orale, test de raisonnement diagnostique, évaluation par les pairs |

---

# PARTIE 1 · LE CAHIER DES CHARGES

## 1. La situation

Barcelone possède le marché de la location de courte durée le plus disputé d'Europe. En 2024, la municipalité a annoncé la suppression totale de l'ensemble des licences d'appartements touristiques d'ici 2028, une décision politique sans précédent à cette échelle. Autour de cette décision gravitent deux organisations qui attendent des choses très différentes des mêmes données :

### Client A · Direcció de Turisme, Ajuntament de Barcelona (le régulateur)
La capacité de contrôle est limitée : les inspecteurs ne peuvent visiter que quelques centaines d'annonces par mois sur plus de quinze mille logements actifs. Ils souhaitent savoir quelles annonces opèrent **sans licence touristique valide**, afin que les contrôles sur le terrain soient ciblés plutôt qu'aléatoires. Ils doivent également pouvoir justifier publiquement ce ciblage : si le modèle concentre les contrôles dans les quartiers les plus défavorisés, cela devient un problème politique et juridique majeur, et pas seulement technique.

### Client B · Une société de gestion immobilière opérant dans la ville
Elle gère un portefeuille en pleine expansion et souhaite disposer d'un **outil de tarification** : étant donné les caractéristiques d'un nouvel appartement, à quel prix par nuit doit-il être proposé ? Son processus actuel repose uniquement sur l'estimation intuitive d'un gestionnaire qui compare à l'œil trois annonces jugées similaires.

> Vous travaillerez pour les deux clients. Leurs intérêts ne convergeront pas toujours, et une partie essentielle de votre travail consistera à identifier ces divergences.

---

## 2. Les données

*Inside Airbnb, Barcelone, instantané (*snapshot*) du 24 juin 2026. 15 293 annonces réparties sur 90 colonnes, stockées dans le dépôt sous `data/raw/`.*

Inside Airbnb est un projet militant qui extrait (*scrape*) les pages publiques d'annonces Airbnb afin d'alimenter le débat sur les politiques du logement. Cette origine a son importance : les données n'ont pas été collectées pour votre usage spécifique, n'offrent aucun contrat de documentation garanti et contiennent des artefacts liés au fonctionnement du robot d'extraction. Comprendre ce que mesure réellement chaque colonne fait partie intégrante du travail.

> **Ne téléchargez pas à nouveau les données.** L'instantané est gelé (*pinned*). Inside Airbnb renouvelle ses données chaque trimestre ; vos résultats ne seraient plus comparables à ceux des autres étudiants ni aux chiffres du cours.

### Vue d'ensemble des variables

| Groupe | Colonnes |
|---|---|
| **Caractéristiques physiques** | Capacité d'accueil (`accommodates`), chambres (`bedrooms`), lits (`beds`), salles de bain (`bathrooms`), type de propriété et type de chambre (`room_type`) |
| **Géographie** | Latitude, longitude, 69 quartiers (`neighbourhood_cleansed`) répartis dans 10 districts |
| **Hôte** | Identifiant (`host_id`), ancienneté, taille du portefeuille d'annonces |
| **Commercial** | Prix affiché (`price`), calendrier de disponibilité (`availability_365`), durée minimale de séjour (`minimum_nights`), estimations d'occupation et de revenus |
| **Réputation** | Nombre d'avis (`number_of_reviews`) et sept dimensions de notes d'évaluation (`review_scores_*`) |
| **Réglementation** | Champ de licence (`license`) en texte libre |
| **Données textuelles** | Nom de l'annonce, description, biographie de l'hôte, liste JSON des équipements (`amenities`) |
| **Images** | URLs des photos (extension optionnelle) |

### Deux avertissements en toute franchise
1. **Certaines colonnes sont totalement vides ou constantes.** Ce n'est pas une anomalie du fichier : le schéma de données a évolué. Identifiez-les et ne supposez jamais qu'une colonne contient de l'information simplement parce qu'elle a un nom.
2. **Certaines colonnes donneront à votre modèle une apparence de performance spectaculaire.** Méfiez-vous des bonnes nouvelles trop flatteuses. Si un résultat vous surprend agréablement, c'est le signal d'une enquête à mener, non d'une victoire à célébrer. Cela vous arrivera plus d'une fois, à dessein.

---

## 3. Les deux questions

### Q1 · Régression, pour le Client B : quel prix une annonce doit-elle facturer par nuit ?
* **Cible :** Prix à la nuitée.
* Vous devrez décider de ce que signifie réellement « le prix à la nuitée » dans ce jeu de données avant de pouvoir le modéliser. Cette décision fait partie de l'évaluation, ce n'est pas une simple formalité, et elle modifie profondément la réponse.

### Q2 · Classification, pour le Client A : cette annonce opère-t-elle avec une licence valide ?
* **Cible :** Construite par vos soins à partir de la colonne `license`.
* Ce champ est en texte libre, présente plusieurs formats hétérogènes et contient de nombreuses valeurs manquantes. Dériver une cible binaire défendable constitue la première véritable tâche d'analyse, et des personnes raisonnables aboutiront à des définitions légèrement distinctes. Documentez rigoureusement la vôtre.

*(Extension optionnelle : prédire le taux d'occupation. Plus complexe qu'il n'y paraît : un tiers des annonces est à zéro et les variables les plus évidentes sont contaminées).*

---

## 4. Vos livrables séance par séance

| Séance | Jalon | Intitulé | Évaluation |
|:---:|:---:|---|:---:|
| S1 | **M0** | Fiche d'identité des données (*Dataset fact sheet*) et énoncé du problème | Formatif (0%) |
| S2 | **M1** | Pipeline de nettoyage, journal de décision, jeu de test scellé | **Noté (10%)** |
| S3 | **M2** | Pipeline de prétraitement et audit des fuites de données | **Noté (10%)** |
| S4 | **M3** | Régression : baseline naïve, modèle linéaire, modèle régularisé | Formatif (0%) |
| S5 | **M4** | Classification : comparaison de trois modèles et recommandation de seuil | Formatif (0%) |
| S6 | **M5** | Modèle validé et réglé avec intervalles d'incertitude | Formatif (0%) |
| S7 | **M6** | Modèle champion d'ensemble et Fiche de Modèle v1 (*Model Card v1*) | **Noté (10%)** |
| S8 | **M7** | Réseau de neurones codé de zéro en NumPy et son pendant PyTorch | Formatif (0%) |
| S9 | **M8** | Perceptron multicouche (MLP) PyTorch et comparaison honnête contre M6 | Formatif (0%) |
| S10 | **M9** | Étude d'ablation de régularisation | **Noté (10%)** |
| S11 | **M10** | Modèle texte, modèle multimodal par fusion et Fiche de Modèle Finale | Formatif (0%) |
| S12 | **-** | **Rapport final et soutenance orale** | **Noté (30%)** |

> *« Formatif » ne signifie pas facultatif.* Ces jalons ne portent pas de note directe, mais ils reçoivent un retour écrit détaillé, et chacun d'eux alimente directement le jalon noté qui lui succède. En sauter un vous pénalisera plus tard.

---

## 5. La règle d'or

> **Vous séparerez les données dès la Séance 2, et vous ne toucherez plus au jeu de test jusqu'à la Séance 12, en classe, devant tout le monde.**
>
> Pas de « j'essaie de ne pas trop le regarder ». Pas de « j'évite juste de l'utiliser pour régler les hyperparamètres ». Vous l'écrirez sur le disque, et vous **n'ouvrirez plus jamais ce fichier**. Chaque chiffre honnête que vous rapporterez pendant onze semaines proviendra exclusivement de la validation croisée sur vos données d'entraînement.
>
> Lors de la Séance 12, vous énoncerez à voix haute votre performance attendue, et seulement ensuite le jeu de test sera descellé. L'écart entre ces deux chiffres sera le résultat le plus instructif de votre semestre, et il n'aura de valeur que si le sceau a été respecté.

---

## 6. Modalités de notation

Lisez ceci avec la plus grande attention, car le fonctionnement diffère de la plupart des cours classiques :

* **Un score élevé ne garantit pas une bonne note.** La performance brute du modèle est plafonnée à **environ 20%** du barème de chaque jalon.
* **Les 80% restants évaluent :** la décision était-elle défendable ? Saviez-vous pourquoi vous l'avez prise ? Avez-vous vérifié l'élément susceptible de prouver que vous aviez tort ? Êtes-vous capable d'expliquer le résultat à un interlocuteur qui ignore ce qu'est un $R^2$ ?
* Un étudiant rapportant $R^2 = 0,61$ avec un protocole irréprochable et un compte-rendu lucide de ses limites obtiendra une bien meilleure note qu'un étudiant rapportant $0,87$ sans être capable d'expliquer d'où vient ce chiffre. Sur ce jeu de données, le second étudiant commet presque certainement une erreur d'évaluation, et apprendre à faire la différence fait partie du cours.
* Il arrivera que vous ne battiez qu'à peine une baseline simple. C'est un résultat scientifique parfaitement recevable, et le rapporter clairement a bien plus de valeur que de fabriquer un gain artificiel.

---

## 7. Modalités de travail

* **Jalons M1 et M2 :** Travail en binômes assignés.
* **À partir de M3 :** Nouveaux binômes.
* **Rapport final et soutenance :** Travail individuel. Chaque étudiant doit être en mesure de défendre chaque ligne de code de son propre notebook.
* La discussion et le partage d'idées entre binômes sont vivement encouragés. Le copier-coller de notebooks est proscrit et vous serez interrogé individuellement sur votre code lors de la soutenance.
* Prévoyez environ 4 heures de travail personnel par semaine en dehors des cours.

---

## 8. L'éthique, au cœur du projet et non comme annexe

Ce projet traite d'un sujet réel qui emporte des conséquences concrètes :

* **Le modèle de régulation cible des personnes physiques.** Un faux positif correspond au contrôle injustifié d'un hôte en règle : c'est un coût financier et moral imposé à un citoyen qui n'a rien à se reprocher. Qui supporte ce coût ?
* **La conformité n'est pas uniformément répartie dans l'espace urbain.** Lorsque vous identifiez un motif géographique, vous devez vous interroger sur sa signification : est-il défendable pour un algorithme d'exploiter cette corrélation ? La géographie reflète les disparités de revenus, et les revenus portent d'autres réalités sociales.
* **Le modèle de tarification produit des effets macroscopiques agrégés.** Un outil qui permet à chaque exploitant de maximiser son tarif transforme la tension locative globale de la ville. Savoir si cela relève de votre responsabilité professionnelle est une question légitime qu'il vous appartient d'argumenter.
* **Les données décrivent des individus identifiables.** Les noms des hôtes et leurs photos ont été retirés de la copie que vous recevez. Demandez-vous si l'analyse que vous conduisez est une analyse que les hôtes reconnaîtraient comme équitable.

> *Vous ne serez pas noté sur l'adoption d'une conclusion morale prédéterminée, mais sur votre capacité à repérer ces questionnements et à raisonner rigoureusement autour d'eux.*

---

## 9. Livrables finaux

* Des **notebooks reproductibles** qui s'exécutent de bout en bout sur une installation neuve, avec graines fixées et environnement verrouillé.
* Un **rapport écrit de six pages maximum** : problème, données, méthode, résultats assortis de leur incertitude, interprétation, limites et recommandations opérationnelles pour chaque client.
* Une **Fiche de Modèle Finale (*Final Model Card*)** : ce que fait le modèle, son niveau de performance, les sous-groupes pour lesquels il échoue et ses conditions d'exclusion.
* Une **soutenance orale** : 12 minutes de présentation, 6 minutes de questions, ouverture en direct du jeu de test scellé.

---

## 10. Les pièges méthodologiques annoncés à l'avance

Vous avez été prévenus contre chacun de ces pièges. Se faire surprendre par l'un d'eux au début est normal ; ne pas s'en apercevoir et persister est ce qui coûte des points.

| N° | Piège méthodologique |
|:---:|---|
| **1** | Nettoyer ou explorer les données avant d'avoir isolé le jeu de test (*Split First*) |
| **2** | Ajuster (*fit*) un transformateur en incluant le pli de validation croisée |
| **3** | Traiter une variable anormalement puissante comme un coup de chance |
| **4** | Rapporter une différence de score inférieure à la dispersion observée entre les plis |
| **5** | Appliquer un découpage `KFold` aléatoire sur des données présentant une structure de groupe évidente |
| **6** | Comparer un modèle dont les hyperparamètres ont été réglés (*tuned*) à une baseline non réglée |
| **7** | Imputer une valeur qui ne peut pas exister dans la réalité |
| **8** | Supprimer des lignes sans vérifier ce qu'elles avaient en commun |
| **9** | Choisir 0,5 comme seuil de classification sous prétexte qu'il s'agit de la valeur par défaut |
| **10** | Présenter l'importance d'une variable comme la preuve d'un lien de causalité |
| **11** | Conclure que « le deep learning est meilleur » ou « le deep learning est inutile » sans comparaison contrôlée |

---

# PARTIE 2 · LE DÉTAIL DES JALONS (M0 À M10)

## Répartition globale de la note

| Instrument | Type | Poids |
|---|:---:|:---:|
| Contrôles de connaissances en séance (*Knowledge checks*, 9 meilleurs sur 11) | Noté | 10% |
| **M1** : Nettoyage et journal de décision | Noté | 10% |
| **M2** : Pipeline et audit des fuites de données | Noté | 10% |
| **A5** : Épreuve pratique de mi-parcours (jeu de données inédit) | Noté | 15% |
| **M6** : Modèle champion d'ensemble et Fiche de Modèle v1 | Noté | 10% |
| **M9** : Étude d'ablation de régularisation | Noté | 10% |
| **Rapport final et notebook** | Noté | 20% |
| **Soutenance orale** | Noté | 10% |
| **Test de raisonnement diagnostique** | Noté | 5% |
| **M0, M3, M4, M5, M7, M8, M10** | Formatif | 0% |

> **Règle universelle :** Dans chaque grille d'évaluation, la performance prédictive brute compte pour **20% maximum**. Le raisonnement, la rigueur du protocole et la lucidité méthodologique apportent les 80% restants.

---

## M0 · Fiche d'identité des données (*Dataset fact sheet*)
*Formatif, après la Séance 1.*

* **À rendre :** Un notebook contenant :
  1. Les dimensions (*shape*) et les types (*dtypes*).
  2. La liste des colonnes intégralement vides ou constantes.
  3. Trois questions auxquelles les données peuvent répondre.
  4. Une question à laquelle elles ne peuvent pas répondre, avec la justification.
  5. Un paragraphe d'énoncé du problème nommant le client, l'unité d'observation, la variable cible et un critère de succès vérifiable par un non-technicien.
* **Ce qui sera évalué :** Si vous avez nommé l'unité d'observation avec précision, et si vous avez quantifié le nombre d'unités véritablement indépendantes. Ces deux chiffres ne sont pas identiques, et remarquer cet écart constitue le cœur de l'exercice.

---

## M1 · Pipeline de nettoyage et journal de décision
**Noté, 10%** · *Après la Séance 2.*

* **À rendre :** Notebook, tableau du journal de décision (*decision log*), et fichier `test.parquet` généré et scellé sur disque sans jamais être réouvert. Le journal de décision comporte une ligne par choix de nettoyage, structurée en quatre colonnes :
  * **Ce que j'ai fait** (*what I did*)
  * **Pourquoi** (*why*)
  * **Ce que j'aurais perdu sinon** (*what I would have lost otherwise*)
  * **Ce que cela pourrait biaiser** (*what this could bias*)

### Grille d'évaluation M1

| Critère | Poids | Excellent | Satisfaisant | Insuffisant |
|---|:---:|---|---|---|
| **Discipline du découpage (*Split discipline*)** | 20% | Séparation effectuée avant tout nettoyage, sur le bon groupement d'hôtes, justifiée par la mesure de la structure de groupe. | Séparation avant nettoyage, mais aléatoire simple sans prise en compte des groupes. | Séparation effectuée après nettoyage, ou jeu de test non scellé. |
| **Identification des défauts** | 20% | Repère toutes les anomalies majeures, y compris les doublons trompeurs, colonnes vides et conversions de types. | Identifie les anomalies évidentes (types, valeurs manquantes de base). | Omet les conversions de types indispensables ou le traitement des valeurs manquantes. |
| **Raisonnement sur les valeurs manquantes** | 25% | Identifie le mécanisme sous-jacent par colonne, quantifie le biais causé par une suppression, aligne la stratégie d'imputation sur ce mécanisme. | Constate les proportions de manquants et impute de manière plausible. | Applique un simple `dropna()` global ou impute la moyenne sans analyse préalable. |
| **Qualité du journal de décision** | 25% | Chaque décision est assortie d'une argumentation solide et d'un risque de biais explicite. | La plupart des décisions sont justifiées. | Le journal n'est qu'une simple liste de commandes exécutées. |
| **Reproductibilité** | 10% | Le code s'exécute impeccablement de bout en bout avec graines fixées. | S'exécute avec des retouches mineures. | Ne s'exécute pas. |

> *Erreurs fréquentes :* `dropna()` sur l'ensemble du DataFrame ; imputer les notes d'avis par la moyenne alors que le logement n'a jamais reçu de réservation ; `drop_duplicates()` supprimant les multi-propriétaires légitimes ; nettoyer avant de séparer sans s'en rendre compte.

---

## M2 · Pipeline de prétraitement et audit des fuites de données
**Noté, 10%** · *Après la Séance 3.*

* **À rendre :** Un objet `ColumnTransformer` et `Pipeline`, accompagné d'un audit écrit des fuites de données (*leakage audit*). L'audit explicite pour chaque colonne suspecte son mécanisme de fuite, l'inflation mesurée qu'elle provoque sur le score, et la décision adoptée.

### Grille d'évaluation M2

| Critère | Poids | Excellent | Satisfaisant | Insuffisant |
|---|:---:|---|---|---|
| **Rigueur de la Pipeline** | 25% | Toutes les transformations ajustées (*fitted*) sont encapsulées à l'intérieur de la pipeline ; aucune fuite sur les données de validation. | Pipeline utilisée, mais une transformation est ajustée en dehors. | Prétraitements manuels appliqués en amont sur l'ensemble de la table. |
| **Détection des pièges** | 25% | Découvre au-delà de la fuite évidente et explicite le mécanisme exact de chaque piège. | Repère uniquement la fuite la plus directe. | Ne détecte aucune fuite et rapporte un score gonflé comme un résultat légitime. |
| **Quantification de l'inflation** | 15% | Mesure et compare les performances avant et après élimination selon un protocole rigoureux. | Se contente de mentionner que la performance s'est « améliorée ». | Aucune mesure d'impact chiffrée. |
| **Choix d'encodage** | 20% | Prend en compte la forte cardinalité ; gère le target-encoding de façon étanche par pli ou le rejette avec arguments. | Encodage One-Hot classique appliqué de manière globale. | Encode 69 catégories sans précaution, ou crée une fuite par target encoding non étanche. |
| **Justification des variables dérivées** | 15% | Au moins trois variables issues du *feature engineering* sont argumentées sur des bases métier. | Variables créées, mais argumentation superficielle. | Variables créées sans justification métier. |

> *Erreurs fréquentes :* Ajuster un scaler avant la séparation ; encoder une variable à forte cardinalité sur l'ensemble d'entraînement complet ; déclarer une colonne comme fuite sans en mesurer l'inflation ; supposer que l'unique problème est la colonne dont le nom ressemble à la cible.

---

## M3 · Régression (Client B)
*Formatif, après la Séance 4.*

* **À rendre :** Modèle de référence passif (*baseline*), puis régression linéaire, puis régression régularisée, accompagnés des graphiques de résidus à chaque étape et d'une justification du choix de vos métriques.
* **Ce qui sera évalué :** Avez-vous établi la baseline passive avant de modéliser ? Avez-vous justifié la transformation de la variable cible au lieu de vous contenter de la recopier ?

---

## M4 · Classification (Client A)
*Formatif, après la Séance 5.*

* **À rendre :** Trois modèles évalués sous un même protocole unifié, un tableau comparatif complet et une recommandation de seuil décisionnel intégrant des hypothèses explicites de coût.
* **Ce qui sera évalué :** Le seuil opérationnel est-il déduit d'un ratio de coût assumé ou d'une contrainte métier, ou s'agit-il du seuil arbitraire de 0,5 assorti d'un paragraphe ? L'accuracy est-elle encore présentée comme métrique principale sur une cible fortement déséquilibrée ?

---

## M5 · Modèle validé, calibré et incertitude
*Formatif, après la Séance 6. Le jalon charnière.*

* **À rendre :** Un modèle réglé, un rapport de validation croisée annoté d'intervalles d'incertitude, une défense écrite de votre méthodologie et toute décision argumentée concernant **la signification réelle de votre variable cible**.
* **Ce qui sera évalué (par ordre de priorité) :**
  1. Avez-vous conclu que deux modèles se situent dans la marge de bruit statistique lorsque leurs intervalles se chevauchent, au lieu de désigner naïvement le score le plus haut ?
  2. La structure de groupe par hôte a-t-elle été intégrée à votre stratégie de validation croisée ?
  3. Chaque décision relative à la cible est-elle explicitement reliée au client concerné ?

---

## M6 · Modèle champion d'ensemble et Fiche de Modèle v1
**Noté, 10%** · *Après la Séance 7.*

* **À rendre :** Un notebook de benchmark comparatif et la Fiche de Modèle v1 (*Model Card v1*).
* **Contenu obligatoire de la Model Card v1 :**
  1. L'usage prévu et le client auquel le modèle s'adresse.
  2. Les performances assorties d'intervalles d'incertitude et le protocole de validation associé.
  3. Le classement des variables clés par importance de permutation, accompagné d'une mise en garde sur ce que cette mesure ne prouve pas.
  4. La performance par sous-groupes, notamment par district urbain.
  5. Les modes de défaillance connus et les conditions d'exclusion dans lesquelles le modèle ne doit pas être utilisé.

### Grille d'évaluation M6

| Critère | Poids | Excellent | Satisfaisant | Insuffisant |
|---|:---:|---|---|---|
| **Compréhension des mécanismes** | 20% | Explique rigoureusement pourquoi le bagging et le boosting réduisent l'erreur selon des dynamiques distinctes (variance vs biais) ; prédictions vérifiées en pratique. | Décrit correctement les deux familles. | Traite les deux approches comme interchangeables sous l'étiquette « meilleurs modèles ». |
| **Rigueur du protocole** | 20% | Protocole d'évaluation identique entre tous les modèles ; réglage interne aux plis ; arrêt anticipé (*early stopping*) maîtrisé. | Protocole globalement cohérent. | Compare des modèles réglés à des baselines non réglées. |
| **Honnêteté de la comparaison** | 20% | Identifie et documente les cas où le modèle d'ensemble ne surpasse pas significativement le modèle linéaire, intervalles à l'appui. | Constate des différences avec un certain degré d'incertitude. | Classe les algorithmes sur de simples estimations ponctuelles moyennes. |
| **Interprétation** | 20% | Importance par permutation lue correctement ; mentionne explicitement l'absence de preuve de causalité. | Produit et décrit les graphiques d'importance. | Présente les variables prédictives comme des causes réelles. |
| **Équité spatiale et sous-groupes** | 20% | Détecte et chiffre les disparités entre quartiers, puis argumente sur les conséquences concrètes pour le ciblage des contrôles. | Constate l'existence d'écarts entre districts. | Aucune analyse par sous-groupes. |

> *Erreurs fréquentes :* Se bloquer devant une erreur de format creux vs dense (`TypeError`) au lieu de lire la trace ; rapporter l'importance basée sur la pureté (*impurity importance*) au lieu de la permutation ; prétendre qu'une forêt aléatoire ne peut pas surapprendre ; benchmarker sur un découpage train/test unique.

---

## M7 · Réseau de neurones codé de zéro (*from scratch*)
*Formatif, après la Séance 8.*

* **À rendre :** L'implémentation du réseau de neurones en pur NumPy et son pendant en PyTorch, avec des sorties strictement concordantes, accompagnés de votre feuille de calculs manuels des gradients.
* **Ce qui sera évalué :** Vos gradients calculés à la main correspondent-ils exactement à la dérivation automatique (*autograd*) ? Si ce n'est pas le cas, le débogage de cet écart constitue l'apprentissage le plus précieux du bloc.

---

## M8 · Perceptron multicouche (MLP) PyTorch et comparaison honnête
*Formatif, après la Séance 9.*

* **À rendre :** Une pipeline fonctionnelle, une galerie de courbes d'apprentissage commentées avec diagnostic d'erreur, et une comparaison documentée face à votre champion M6 sous le même protocole.
* **Ce qui sera évalué :** La comparaison est-elle strictement équitable (*like-for-like*) ? Et si le modèle classique surpasse le réseau de neurones, expliquez-vous rationnellement ce résultat au lieu de chercher à vous en excuser ?

---

## M9 · Étude d'ablation de régularisation
**Noté, 10%** · *Après la Séance 10.*

* **À rendre :** Un tableau d'ablation complet testant isolément : baseline, arrêt anticipé (*early stopping*), déclin de poids (*weight decay*), dropout, normalisation par lot (*batch normalization*) et leur combinaison finale. Chaque configuration est exécutée sur **trois graines aléatoires distinctes** avec moyenne et dispersion, assortie d'une recommandation rédigée.

### Grille d'évaluation M9

| Critère | Poids | Excellent | Satisfaisant | Insuffisant |
|---|:---:|---|---|---|
| **Contrôle expérimental** | 30% | Un seul facteur varie à la fois ; tous les autres paramètres demeurent strictement constants ; graines rapportées. | Expérimentation globalement contrôlée. | Plusieurs modifications simultanées par ligne de test. |
| **Discipline des graines (*seeds*)** | 20% | Trois graines testées par configuration, dispersion mesurée et exploitée dans l'argumentation. | Plusieurs graines testées et rapportées. | Un seul tirage par configuration. |
| **Qualité du diagnostic** | 20% | La courbe d'apprentissage de chaque configuration est classée avec justesse (sous-apprentissage, surapprentissage, etc.). | Majorité des courbes bien diagnostiquées. | Les courbes d'entraînement ne sont pas examinées. |
| **Lucidité des conclusions** | 20% | Reconnaît sans détour les cas où une intervention n'a produit aucun effet au-delà de la variabilité statistique. | Formule des réserves modérées. | Déclare chaque changement comme une amélioration. |
| **Explication des mécanismes** | 10% | Chaque technique de régularisation est explicitement reliée au défaut structurel qu'elle est censée corriger. | Techniques décrites. | Simple récapitulatif sans justification mécanique. |

> *Erreurs fréquentes :* Utiliser une seule graine ; conclure qu'un gain de +0,004 est significatif ; oublier d'activer `model.eval()` lors de l'évaluation ; cumuler d'emblée toutes les régularisations sans isoler les effets individuels.

---

## M10 · Modèle texte, modèle fusion et Fiche de Modèle Finale
*Formatif, après la Séance 11.*

* **À rendre :** Une baseline simple de traitement de texte (sac de mots / TF-IDF), puis un modèle basé sur un encodeur (embeddings), puis un modèle de fusion multimodal ; un grand tableau comparatif récapitulant les onze semaines ; et votre Fiche de Modèle Finale (*Final Model Card*).
* **Ce qui sera évalué :** La baseline textuelle simple a-t-elle été construite en premier ? Avez-vous vérifié si le texte introduit ses propres fuites de données et comment les avez-vous traitées ? Le tableau récapitulatif porte-t-il une véritable thèse argumentée, ou n'est-il qu'un alignement passif de chiffres ?

---

# PARTIE 3 · LES AUTRES ÉPREUVES ÉVALUÉES

## A5 · Épreuve pratique de mi-parcours
**Noté, 15%** · *À la maison, durée d'une semaine, après la Séance 6.*

* **Format :** Budget de travail estimé à cinq heures, sur un petit jeu de données totalement inédit : un domaine d'application différent, quelques milliers de lignes, fourni par l'enseignant.
* *C'est l'unique épreuve évaluant votre capacité de transfert.* Tout le reste du cours est mesuré sur un jeu de données que vous fréquentez pendant des semaines ; celle-ci teste vos réflexes en terrain inconnu.
* **À rendre :** Un notebook unique comprenant le cadrage du problème, le découpage approprié, l'analyse exploratoire, le prétraitement, une baseline, au moins deux modèles comparés, la validation avec quantification de l'incertitude et une recommandation finale.

### Grille d'évaluation A5

| Critère | Poids |
|---|:---:|
| Complétude et ordonnancement logique du workflow | 25% |
| Stratégie de découpage et de validation adaptée à la structure des données | 20% |
| Identification et traitement des fuites de données | 20% |
| Choix des métriques justifié selon le contexte opérationnel | 15% |
| Incertitude mesurée et intégrée aux conclusions finales | 10% |
| Performance relative face à une baseline de référence sensée | 10% |

> *Erreurs fréquentes :* Recopier à l'identique le workflow de Barcelone sans vérifier si le nouveau jeu partage la même structure ; manquer une fuite car elle opère par un mécanisme différent ; absence de baseline.

---

## Rapport final et notebook
**Noté, 20%** · *Fin de semestre.*

* **À rendre :** Des notebooks reproductibles et un rapport de synthèse de **six pages maximum**.

### Grille d'évaluation du rapport

| Critère | Poids | Éléments évalués |
|---|:---:|---|
| **Cadrage du problème et définition de la cible** | 15% | Intègre toute décision relative à ce que mesure la cible, et au client auquel elle répond. |
| **Rigueur méthodologique** | 25% | Intégrité absolue des découpages, validation honnête, élimination des fuites, cohérence du protocole. |
| **Résultats assortis d'incertitude** | 15% | Intervalles de confiance et distributions de plis, jamais de simples moyennes isolées. |
| **Comparaison globale à travers les trois blocs** | 15% | Confrontation argumentée entre modèles classiques et approches deep learning sur vos propres résultats. |
| **Interprétation et limites** | 15% | Analyse de performance par sous-groupes démographiques/géographiques et réflexion éthique. |
| **Communication adaptée aux non-techniciens** | 10% | Le Client A et le Client B doivent recevoir des conclusions distinctes et intelligibles. |
| **Reproductibilité** | 5% | Exécution complète sans erreur sur un environnement neuf, graines déclarées. |

> **Plafonnement automatique de la note à C (échec de validation) :** si le jeu de test a été utilisé avant la Séance 12, ou si l'un des résultats clés rapportés ne peut pas être reproduit en réexécutant votre notebook.

---

## Soutenance orale
**Noté, 10%** · *Séance 12.*

* **Format :** 12 minutes de présentation et 6 minutes de questions-réponses. Avant que le jeu de test scellé ne soit ouvert, vous énoncez publiquement votre performance attendue.

### Grille d'évaluation de la soutenance

| Critère | Poids |
|---|:---:|
| Capacité à défendre des choix techniques spécifiques face aux questions | 30% |
| Précision de la prédiction avant ouverture du test, et qualité d'explication de l'écart constaté | 20% |
| Présentation spontanée des limites de votre travail sans attendre d'y être poussé | 20% |
| Communication orientée vers le client commanditaire plutôt que vers le professeur | 15% |
| Capacité à réagir à une remise en cause d'un choix, en le révisant ou en le défendant avec arguments | 15% |

> *Concernant les 20% sur la prédiction de performance :* vous êtes récompensé pour la finesse de votre explication de l'écart, et non pas uniquement pour un écart minime. Un étudiant ayant prédit $0,62$, obtenant $0,57$, et qui attribue avec pertinence cet écart à la variance d'échantillonnage entre hôtes obtiendra une meilleure note qu'un étudiant ayant prédit $0,62$, obtenant $0,62$, sans être capable d'expliquer pourquoi.

---

## Test de raisonnement diagnostique
**Noté, 5%** · *En classe, sans documents, sans programmation de zéro.*

On vous remettra une analyse inconnue et vous devrez déterminer si ses conclusions sont fiables.

| Partie | Tâche | Points |
|:---:|---|:---:|
| **1** | **Trouver les erreurs :** un notebook fourni contient des défauts méthodologiques. Nommer chaque défaut, expliciter sa conséquence et énoncer la correction. | 20 pts |
| **2** | **Quel modèle déployer ?** Deux modèles avec métriques complètes et une contrainte métier. Choisir et argumenter. | 10 pts |
| **3** | **Lire la courbe :** une courbe d'apprentissage sans étiquettes. Diagnostiquer l'anomalie et prescrire le remède. | 10 pts |
| **4** | Questions conceptuelles courtes balayant l'ensemble des trois blocs du cours. | 10 pts |

---

## Évaluation par les pairs (*Peer review*)

À chaque jalon noté, vous examinez le travail d'un camarade selon la grille officielle (environ trente minutes). Les évaluations sont rendues et comptent dans la note des contrôles de connaissances. Appliquer la grille au travail d'autrui est le moyen le plus efficace d'en intégrer les exigences, tout en désamorçant l'idée reçue selon laquelle seul un score élevé constitue l'objectif.

---

> ### La règle suprême à retenir
>
> **Si vous ne devez retenir qu'une seule phrase de ce document :**  
> *Vous n'êtes pas noté sur l'ampleur de vos scores. Vous êtes noté sur la confiance que l'on peut accorder aux chiffres que vous avancez.*
