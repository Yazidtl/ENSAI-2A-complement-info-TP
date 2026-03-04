````markdown
# projet-conception-logicielle

## :arrow_forward: Présentation

Cette application permet de chercher des recettes de cuisine, de planifier ses repas et de générer une liste de courses, le tout avec un système utilisateur. Il s'agit d'une application backend exposant une API REST.

## :arrow_forward: Architecture du code

### Backend (`./backend`)

L'application met en œuvre :
- Git avec utilisation de branches et merge requests
- Architecture applicative (couches, IoC, SRP)
- Linting et qualité du code (avec pipeline automatisée)
- Tests unitaires utilisant Pytest (avec pipeline automatisée)
- CI/CD
- Design patterns et bonnes pratiques
- Portabilité avec Docker, Kubernetes
- Authentification sécurisée
- Utilisation des API Spoonacular et Open Food Facts
- Documentation (dépendances, quickstart, utilisation...)

L'application suit une architecture en couches claire :

Router (`web/`)
   ↓  
Service (`services/`)
   ↓  
DAO (`dao/`)
   ↓  
Base de données

## Aperçu des fichiers backend

| Fichier / Dossier  | Description |
| ------------------ | ----------- |
| `main.py`          | **Point d'entrée de l'API FastAPI**. Initialise l'application, enregistre les routeurs et configure les middlewares (CORS, etc.). |
| `business_object/` | Contient les **objets métier** (ex : `Utilisateur`). Représentation purement métier des entités, indépendante de FastAPI et de la base de données. |
| `web/`             | Contient les **routeurs FastAPI** (ex : `utilisateur_router.py`). Définit les endpoints HTTP et la gestion des réponses. |
| `services/`        | Couche **logique métier**. Intermédiaire entre les routeurs et les DAO. Gère les règles métier (authentification, rôles, permissions…). |
| `dao/`             | Couche **accès aux données**. Contient les classes responsables des requêtes SQL et des interactions avec la base de données. |
| `dto/`             | Contient les **Data Transfer Objects** (Pydantic). Définit les modèles utilisés pour les requêtes et réponses API. |
| `config/`          | Configuration technique de l'application (ex : CORS Middleware, paramètres globaux). |
| `utils/`           | Fonctions utilitaires transverses (ex : `securite` pour hash/salt des mots de passe, `reset_database`). |
| `data/`            | Scripts SQL d'initialisation et de structure de la base de données. |
| `tests/`           | Tests unitaires et/ou d'intégration (DAO, services, routes…). |
| `.env.template`    | Modèle de fichier d'environnement (variables de configuration : BDD, ports…). |
| `pyproject.toml`   | Configuration du projet Python (dépendances, outils, build). |
| `uv.lock`          | Fichier de verrouillage des dépendances (gestion via `uv`). |
| `ruff.toml`        | Configuration du linter **Ruff**. |
| `Dockerfile`       | Fichier de conteneurisation pour déployer l'application dans Docker. |
| `.python-version`  | Version Python utilisée par le projet (pyenv ou équivalent). |

### Déploiement Cloud (`./kubernetes`)
Contient les manifestes Kubernetes nécessaires au déploiement de l’application.

---

## :arrow_forward: Quickstart pour un lancement avec VSCode

### Prérequis
- Installer `uv`
- Cloner le dépôt
- Créer une base de données PostgreSQL (par exemple via un Datalab)

### Variables d'environnement
Vous devez définir les variables pour connecter l'application à votre base PostgreSQL ainsi qu'à l'API Spoonacular.

- Dans le dossier `backend`, créez une copie du fichier `.env.template` que vous nommerez `.env`
- Adaptez son contenu selon la configuration de votre base de données et les clés dont vous disposez

### Initialisation de la base de données
Dans un terminal à la racine du projet :

```bash
cd backend
uv run -m utils.reset_database.py
````

### Lancer l'application

Dans un terminal à la racine du projet :

```bash
cd backend
uv run main.py
```

L'API sera accessible sur [http://localhost:8000](http://localhost:8000).
(Si vous utilisez un service comme le VSCode d'un Datalab, pensez à ouvrir le port 8000 et à utiliser le lien fourni par le service plutôt que `localhost`.)

Documentation de l'API :

* Swagger UI : [http://localhost:8000/docs](http://localhost:8000/docs)

---

## :test_tube: Lancer les tests

Dans un terminal :

```bash
cd backend
uv run -m pytest
```

---

## :whale: Docker

Construire l'image (depuis `./backend`) :

```bash
cd backend
docker build -t <dockerhub_user>/<nom_image>:latest .
```

Lancer en local :

```bash
docker run -p 8000:8000 <dockerhub_user>/<nom_image>:latest
```

Swagger : [http://localhost:8000/docs](http://localhost:8000/docs)

---

## :kubernetes: Kubernetes (SSPCloud)

Les manifestes Kubernetes se trouvent dans `kubernetes/app/`.

Déployer :

```bash
kubectl apply -f kubernetes/app
```

Redémarrer après mise à jour de l'image Docker :

```bash
kubectl rollout restart deployment/application
```

Accès au déploiement SSPCloud :

* Swagger UI : [http://projet-conception-2as2.kub.sspcloud.fr/docs](http://projet-conception-2as2.kub.sspcloud.fr/docs)
* Exemple endpoint (Panier) : [http://projet-conception-2as2.kub.sspcloud.fr/docs#/Panier/generer_liste_courses_liste_courses_post](http://projet-conception-2as2.kub.sspcloud.fr/docs#/Panier/generer_liste_courses_liste_courses_post)

---

## :bookmark: Scénario d'utilisation

Dans le Swagger :

1. Créer un utilisateur avec `POST /utilisateurs/ Creer Utilisateur`

2. Se connecter :

* Soit en cliquant sur **Authorize** en haut à droite du Swagger
* Soit en utilisant une requête qui nécessite d'être connecté comme `GET /utilisateurs/ Lister Utilisateurs`

La requête pour supprimer un utilisateur est réservée aux admins.

Les requêtes concernant la planification de repas et la liste de courses sont intuitives : suivre les instructions indiquées dans le Swagger.

```
```
