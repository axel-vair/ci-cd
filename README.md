# DEPLOY.md

## 📦 Déploiement de l'application Safebase avec GitHub Actions

Ce document décrit la procédure de déploiement de l'application Symfony `safebase` à l'aide d'une chaîne CI/CD définie dans GitHub Actions.

---

## 🧪 Étapes de CI/CD

### 1. **Déclenchement automatique**

Le déploiement est déclenché automatiquement lors d’un `push` sur la branche `main` du dépôt principal.

Un événement `repository_dispatch` est ensuite utilisé pour déclencher le workflow dans un dépôt secondaire (`ci-cd`).

---

### 2. **Exécution des tests**

Dans le dépôt `ci-cd` :
- Clonage du projet `safebase`
- Installation des dépendances PHP (via Composer)
- Exécution des tests via PHPUnit

Les tests couvrent :
- Tests unitaires
- Tests d'intégration

---

### 3. **Construction et publication de l’image Docker**

Si les tests passent :
- Construction de l’image Docker à partir du `Dockerfile` dans `safebase`
- Push de l’image vers [Docker Hub](https://hub.docker.com/)
  - Tag basé sur la branche ou le tag Git
  - Exemple : `githull/safebase:latest`

---

### 4. **Notification**

Une notification Google Chat est envoyée pour indiquer le succès ou l’échec du pipeline.

---

## ⚙️ Secrets requis

Le pipeline utilise les secrets suivants :
- `REPO_ACCESS_TOKEN` : Token pour accéder au dépôt distant
- `DOCKER_USERNAME` / `DOCKER_PASSWORD` : Identifiants Docker Hub
- `GOOGLE_CHAT_WEBHOOK_URL` : Webhook pour la notification Google Chat

---

## 🔐 Veille et sécurité

Un job de veille automatique est défini pour :
- Vérifier les vulnérabilités Composer (`composer audit`)
- Scanner l’image Docker (`docker scan`)

---

## 👨‍💻 Exécution manuelle

Si nécessaire, le pipeline peut être déclenché manuellement via un `repository_dispatch` en ligne de commande ou avec l’API GitHub.

---

## 📝 Auteurs

CI/CD écrit et maintenu par [Axel Vair](https://github.com/axel-vair)
