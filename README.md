# DEPLOY.md

## Déploiement de l'application Safebase avec GitHub Actions

Ce document décrit la procédure de déploiement de l'application Symfony **Safebase** via une chaîne CI/CD automatisée définie dans GitHub Actions.

---

## 1. Déclenchement automatique

Le déploiement s’enclenche automatiquement lors d’un `push` sur la branche `main` du dépôt principal.

Un événement `repository_dispatch` déclenche à son tour le workflow dans un dépôt secondaire dédié (`ci-cd`) assurant la découpe claire des responsabilités.

---

## 2. Exécution des tests

Le workflow dans le dépôt `ci-cd` réalise les opérations suivantes :

- Clonage du dépôt Safebase
- Installation des dépendances PHP avec Composer
- Préparation de l’environnement avec un fichier `.env.test.local` adapté aux tests
- Création d’une base SQLite locale pour les tests d’intégration
- Exécution automatisée des tests unitaires et d’intégration via PHPUnit
- Analyse statique de code avec PHPStan pour garantir la qualité et la robustesse du code

Les résultats des tests sont consultables dans GitHub Actions et conditionnent la poursuite du déploiement.

---

## 3. Construction et publication de l’image Docker

Si et seulement si tous les tests sont validés :

- Construction de l’image Docker basée sur le `Dockerfile` contenu dans Safebase
- Tag de l’image basé sur la branche Git ou le tag Git (`latest` ou version spécifique)
- Push de l’image vers le registre [Docker Hub](https://hub.docker.com/), par exemple `githull/safebase:latest`
- Préparation de l’image pour déploiement en production ou autres environnements

---

## 4. Notification & Reporting

À la fin du pipeline, une notification automatique est envoyée via Google Chat, indiquant le succès ou l’échec du déploiement, facilitant la réactivité de l’équipe.

---

## 5. Définition des environnements de tests

- **SIT (System Integration Testing)** : environnement CI/CD localisé dans GitHub Actions pour l'exécution des tests unitaires et d’intégration sur base SQLite.
- **UAT (User Acceptance Testing)** : environnement prévu pour une validation fonctionnelle par les utilisateurs finaux, manuellement ou automatisé via future extension de la pipeline.
- **Production** : cible finale où l’image Docker validée est déployée, préparée sur l’infrastructure adaptée.

---

## 6. Procédure de déploiement

Le déploiement automatisé suit la procédure suivante :

1. Déclenchement automatique ou manuel du workflow.
2. Validation des tests automatisés garantissant l’intégrité du code.
3. Construction et push de l’image Docker.
4. Si applicable, exécution de scripts d’évolution (ex : migrations de base de données, nettoyage de cache).
5. Notification finale envoyée à l’équipe.

*Remarque* : Les migrations et autres commandes de gestion spécifiques à Symfony (ex: `bin/console doctrine:migrations:migrate`, `bin/console cache:clear`) peuvent être ajoutées en fonction des besoins du projet.

---

## 7. Secrets requis

Le pipeline utilise les secrets suivants :

- `REPO_ACCESS_TOKEN` : Token pour accéder au dépôt distant
- `DOCKER_USERNAME` / `DOCKER_PASSWORD` : Identifiants Docker Hub
- `GOOGLE_CHAT_WEBHOOK_URL` : Webhook pour la notification Google Chat

---

## 8. Veille et sécurité

Un job de veille automatique est défini pour :

- Vérifier les vulnérabilités Composer (`composer audit`)
- Suivre régulièrement les mises à jour et bonnes pratiques liées à Symfony, PHP, Docker, et la sécurité via des sources officielles et communautés.

---

## 9. Exécution manuelle

Si nécessaire, le pipeline peut être déclenché manuellement via un `repository_dispatch` en ligne de commande ou avec l’API GitHub.

---

## 📝 Auteurs

CI/CD écrit et maintenu par [Axel Vair](https://github.com/axel-vair)
