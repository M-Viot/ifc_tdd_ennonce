# [Projet TDD](../../readme.md)
## [Tests](../test.md)
### Note
Les tests devront être indépendants, et "mocker" les services nécessaires.
### Services
- [Demineur](#demineur)
- [Grille](#grille)
- [Place](#place)

#### Demineur
Créer le fichier de test `php bin/console make:test WebTestCase Service/DemineurTest`

Méthodes à tester :
- `demarrer` :
  - les champs `nom` et `email` doivent être renseignés.
  - les champs `nLignes`, `nColonnes` et `nBombes` doivent être supérieurs à 0.
  - les champs `nLignes`, `nColonnes` doivent être inférieurs à 50.
  - le nombre maximal de bombes ne doit pas dépasser `nLignes` * `nColonnes`.
  
#### Grille
Créer le fichier de test `php bin/console make:test WebTestCase Service\Grille`

Méthodes à tester :
- `creer` :
  - la propriété `nBombeLocalise` doit être à 0 à l'initialisation.
  - la grille doit etre sauvegardéee en base de données.
- `placerBombes` :
  - le nombre de places avec bombe doit être égal à `nBombe`.
- `compterBombeVoisine` :
  - le nombre de bombes voisines doit être égal au nombre de bombes autour de la place.
- `verifierVictoire` :
  - doit retourner `true` si toutes les places sans bombe sont ouvertes.
  - doit retourner `false` si toutes les places

#### Place
Créer le fichier de test `php bin/console make:test WebTestCase Service\Place`

Méthodes à tester :
- `initPlace` :
  - le numéro de la ligne ne doit pas dépassé le nombre de lignes de la grille.
  - le numéro de la ligne ne doit pas être inférieur à 0.
  - le numéro de la colonne ne doit pas dépassé le nombre de colonnes de la grille.
  - le numéro de la colonne ne doit pas être inférieur à 0.
- `placer` :
  - la propriété `isBombe` doit être à `true`.
- `ouvrir` :
  - doit retourner `true` s'il y a une bombe.
  - doit retourner `false` s'il n'y a pas de bombe.
  - isOpened doit être à `true` si case déja ouverte.