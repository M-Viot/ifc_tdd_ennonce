# [Projet TDD](../../readme.md)
## [Tests](../test.md)
### Controller
- [Home](#home)

#### Home
Créer le fichier de test `php bin/console make:test WebTestCase Controller\HomeController`

Méthodes à tester :
- `index` : 
  - doit retourner une réponse HTTP 200.
  - doit afficher le titre `Bienvenue sur le démineur.`.
  - doit afficher le tableau des scrores
  - doit afficher le formulaire de création de partie.
- `grille` :
  - doit retourner une réponse HTTP 200.
  - ne doit pas sauvegarder en base si formulaire ko
  - doit valider le formulaire de création de partie.
  - doit sauvegarder en base si formulaire ok
  - doit afficher le titre `Démineur.`.
  - doit afficher le compteur de bombes.
  - doit afficher le la grille de jeu.
- `ajax` :
  - doit retourner une réponse HTTP 200.
  - doit valider le formulaire de création de partie.
  - doit afficher le titre `Démineur.`.
  - doit afficher le compteur de bombes.
  - doit afficher le la grille de jeu.
