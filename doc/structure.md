# [Projet TDD](../readme.md)

## Structure
Démineur composé de 4 classes :
- `Demineur` : classe principale du jeu
- `Grille` : représente la grille de jeu
- `Place` : représente une place de la grille
- `Utilisateur` : Information sur le joueur

### Demineur
Propiétés :
- `grille` : Grille
- `utilisateur` : Utilisateur
- `etat` : Enum (DemineurEtat)

Méthodes :
- `demarrer` : Demineur

### Grille
Propiétés :
- `nLignes` : int (entre 6 et 50)
- `nColonnes` : int (entre 6 et 50)
- `nBombe` : int (supérieur à 0)
- `nBombeLocalise` : int (supérieur à 0)
- `places` : Place[]

Méthodes :
- `creer` : Grille
- `placerBombes` : void
- `compterBombeVoisine` : int
- `verifierVictoire` : boolean

### Place
Propiétés :
- `isBombe` : boolean
- `isOpen` : boolean
- `ligne` : int
- `colonne` : int
- `grille` : Grille

Méthodes :
- `initPlace` : void
- `ouvrir` : bool
- `placer` : void

### Utilisateur
Propiétés :
- `nom` : string (entre 3 et 50 caractères)
- `email` : string
