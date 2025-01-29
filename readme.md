# Projet TDD
## [Documentation](doc/symfony-testing-guide.md)
## Description
**Démineur sur navigateur web.**

La page home affichera le tableau des 10 meilleurs scores (nombre de bombes / grille gagnée), et un formulaire pour créer une partie.

Le formulaire comprendra :
- un champ pour le nom du joueur
- un champ pour l'email du joueur
- un champ pour le nombre de lignes de la grille
- un champ pour le nombre de colonnes de la grille
- un champ pour le niveau de difficultée (nombre de bombes calculé en fonction)

Une fois le formulaire validé, une nouvelle page s'affichera avec la grille de jeu avec un compteur de bombe restante.

Le joueur pourra cliquer sur une case pour la découvrir, ou cliquer droit pour la marquer comme bombe.

À chaque bombe localisée, le nombre de bombes restantes sera affiché.

Le joueur gagne s'il découvre toutes les cases sans bombe.

Le joueur perd s'il découvre une bombe.

Après chaque ouverture de case, son état est modifié en base de donnée.

À la fin d'une partie, l'état change en fonction du résultat, et le joueur pourra recommencer ou retourner sur la page home.



## [Structure](doc/structure.md)
## [Tests](doc/test.md)
