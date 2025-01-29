# [Projet TDD](../../readme.md)
## [Tests](../test.md)
### Entité
- [Grille](#grille)
- [Place](#place)
- [Utilisateur](#utilisateur)

#### Demineur
Créer le fichier de test `php bin/console make:test TestCase Entity\Demineur`
- la valeur par défaut de la propriété `etat` doit être `En cours`.

#### Grille
Créer le fichier de test `php bin/console make:test TestCase Entity\Grille`

Le test devra vérifier :
- le nombre minimal de bombes.
- le nombre minimal de lignes.
- le nombre maximal de lignes.
- le nombre minimal de colonnes.
- le nombre maximal de colonnes.

#### Place
Créer le fichier de test `php bin/console make:test TestCase Entity\Place`

Le test devra vérifier :
- la valeur par défaut de la propriété `isBombe` doit être `false`.
- la valeur par défaut de la propriété `isOpen` doit être `false`.



#### Utilisateur
Créer le fichier de test `php bin/console make:test TestCase Entity\Grille`

Le test devra vérifier :
- le nombre maximal de caractères pour le nom.
- le nombre minimal de caractères pour le nom.
- le format de l'email.
