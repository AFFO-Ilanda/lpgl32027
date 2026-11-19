# Séance 1 : Culture DevOps et Git

> Module DevOps · Licence 3 Génie Logiciel · TP1 : Git flow et Pull Request

---

## 1. La culture DevOps

### Le problème de départ

- **Les Dev** veulent livrer vite : leur objectif est le **changement**.
- **Les Ops** veulent une production qui ne tombe jamais : leur objectif est la **stabilité**.

C'est le **« mur de la confusion »** : le Dev livre son code (« ça marche sur ma machine ») et l'Ops doit se débrouiller pour le faire tourner. Les livraisons sont rares, risquées et douloureuses.

### La réponse DevOps

Le DevOps (terme apparu vers 2009, Patrick Debois) est une **culture** et un ensemble de pratiques, pas un outil. Une même équipe est responsable du code **et** de son fonctionnement en production : *« you build it, you run it »*.

### Le modèle CALMS

| Lettre | Pilier | Signification |
|---|---|---|
| **C** | Culture | Collaboration, responsabilité partagée, pas de recherche de coupable |
| **A** | Automation | Automatiser build, tests, déploiement, infrastructure |
| **L** | Lean | Petits lots, éliminer le gaspillage, livrer souvent |
| **M** | Measurement | Mesurer pour s'améliorer |
| **S** | Sharing | Partager le savoir, les outils, les retours d'expérience |

### La boucle DevOps

**Plan → Code → Build → Test → Release → Deploy → Operate → Monitor**, puis retour à Plan.

| Étape | Outil du module |
|---|---|
| Code | Git (S1) |
| Build | Docker / Podman (S2) |
| Test / Release | GitHub Actions (S3, S4) |
| Deploy / Operate | Kubernetes, Terraform (S5, S6) |
| Monitor | Prometheus, Grafana (S7) |

---

## 2. L'automatisation

L'automatisation consiste à **confier à une machine les tâches répétitives** faites à la main à chaque livraison.

**Sans automatisation**, mettre en production demande : pull, compilation, tests, image Docker, envoi sur un registre, connexion au serveur, arrêt de l'ancienne version, lancement de la nouvelle, vérification. Soit 30 à 60 minutes à chaque fois, avec un risque d'erreur humaine.

**Avec automatisation**, un simple `git push` déclenche un **pipeline** qui fait tout, toujours de la même façon.

À quoi ça sert :

- **gagner du temps** ;
- **éviter les erreurs humaines** ;
- **livrer plus souvent** ;
- **avoir un feedback rapide** ;
- **rendre les choses reproductibles** (fini le « ça marche sur ma machine »).

> Règle : si tu fais une tâche plus de deux fois à la main, automatise-la.

---

## 3. La mesure

On ne peut pas améliorer ce qu'on ne mesure pas.

### Les métriques DORA (performance de l'équipe)

| Métrique | Question | Mesure |
|---|---|---|
| **Deployment Frequency** | À quelle fréquence on déploie ? | Vitesse |
| **Lead Time for Changes** | Combien de temps entre un commit et la production ? | Vitesse |
| **Change Failure Rate** | Quel % de déploiements cause un incident ? | Stabilité |
| **Time to Restore (MTTR)** | Combien de temps pour réparer une panne ? | Stabilité |

Les meilleures équipes sont bonnes **sur les deux à la fois** : en livrant souvent, chaque changement est petit, donc moins risqué.

### Les métriques techniques (santé de l'application, S7)

Les **4 Golden Signals** : **latence**, **trafic**, **erreurs**, **saturation**.

### SLI / SLO / SLA

- **SLI** : ce qu'on mesure (ex. % de requêtes réussies).
- **SLO** : l'objectif interne (ex. 99,9 % sur un mois).
- **SLA** : l'engagement contractuel avec pénalités (ex. 99,5 %).

---

## 4. Git

### Origine : le lien avec Linux

- **1991** : Linus Torvalds crée le noyau **Linux**.
- **2002-2005** : le noyau est géré avec **BitKeeper**, un outil propriétaire offert gratuitement.
- **2005** : la gratuité est retirée. Linus écrit **Git** en une dizaine de jours pour gérer le noyau Linux.
- La maintenance est ensuite confiée à **Junio Hamano**.

Git a été pensé pour les besoins de Linux : **distribué**, **rapide**, **branches légères**, **intégrité** (chaque commit est identifié par un hash). Il est libre (GPL v2).

**Git** est l'outil. **GitHub / GitLab** sont des plateformes qui hébergent des dépôts Git.

### Alternatives à Git

| | Git | SVN (Subversion) | Mercurial |
|---|---|---|---|
| Année | 2005 | 2000 | 2005 |
| Type | Distribué | **Centralisé** | Distribué |
| Hors ligne | Oui | Non | Oui |
| Usage aujourd'hui | Standard | Projets anciens | Rare |

- **Centralisé** : un serveur unique détient l'historique (point unique de défaillance).
- **Distribué** : chaque développeur a une copie complète de l'historique.

### Travailler à plusieurs sans Git

| Méthode | Problème |
|---|---|
| S'envoyer le fichier | Une seule personne travaille à la fois |
| Dossier partagé | Le dernier qui enregistre écrase l'autre |
| Copies `_v2`, `_FINAL` | On ne sait plus quelle est la bonne version |

Git règle tout ça : chacun travaille sur sa **branche**, la fusion est automatique, les **conflits** sont signalés, et tout l'**historique** est conservé.

### Les 3 zones

```
Working directory  --git add-->  Staging area  --git commit-->  Repository (.git)
```

Vers le serveur distant : `git push` pour envoyer, `git pull` pour récupérer.

---

## 5. Les commits

Un commit est une **photo (snapshot) du projet** à un instant donné. Il contient :

- un **hash** unique (ex. `a3f5c2e`) ;
- l'**auteur** et la **date** ;
- un **message** ;
- un lien vers son **commit parent**.

```
A ← B ← C ← D   (main)
```

### Conventional Commits

| Type | Usage |
|---|---|
| `feat` | Nouvelle fonctionnalité |
| `fix` | Correction de bug |
| `docs` | Documentation |
| `refactor` | Restructuration du code |
| `test` | Tests |
| `chore` | Maintenance |
| `ci` | Pipeline CI/CD |

Exemple : `feat: ajoute la pagination sur GET /products`

### Bonnes pratiques

1. Un commit = un seul changement logique.
2. Commiter souvent, par petits morceaux.
3. Ne commiter que du code qui compile.
4. Jamais de secrets (`.env`, mots de passe) ni de fichiers générés (`target/`).

---

## 6. Les commandes Git

### Configuration

```bash
git config --global user.name "Ton Nom"
git config --global user.email "toi@mail.com"
git config --global init.defaultBranch main
```

### Cycle quotidien

```bash
git init / git clone <url>
git status
git add fichier.java        # ou git add .
git commit -m "feat: ..."
git push
git pull                    # = fetch + merge
git fetch                   # récupérer sans fusionner
```

### Historique

```bash
git log --oneline --graph --all
git show <hash>
git diff
git diff --staged
```

### Branches et fusion

```bash
git switch -c feature/login
git switch main
git merge feature/login
git rebase main
git branch -d feature/login
```

### Annuler

| Situation | Commande |
|---|---|
| Corriger le dernier commit | `git commit --amend` |
| Annuler un commit déjà poussé | `git revert <hash>` ✅ |
| Supprimer le dernier commit en local | `git reset --soft HEAD~1` |
| Tout supprimer (dangereux) | `git reset --hard HEAD~1` ⚠️ |

> Règle d'or : on ne réécrit jamais un historique déjà partagé (pas de `rebase`, `amend` ou `reset` sur une branche poussée).

### Mettre de côté et versionner

```bash
git stash / git stash pop
git tag -a v1.0.0 -m "Version 1"
git push origin v1.0.0
```

### Résoudre un conflit

```
<<<<<<< HEAD
return "Bonjour";
=======
return "Hello";
>>>>>>> feature/i18n
```

Garder la bonne version, supprimer les marqueurs, puis `git add` et `git commit`.

---

## 7. Stratégies de branches

### GitFlow

| Branche | Rôle |
|---|---|
| `main` | Production, toujours stable |
| `develop` | Intégration des fonctionnalités |
| `feature/*` | Une fonctionnalité (part de `develop`) |
| `release/*` | Préparation d'une version |
| `hotfix/*` | Correctif urgent (part de `main`) |

### GitHub Flow / Trunk-based

1. `main` toujours déployable.
2. Branche courte `feature/xxx`.
3. Pull Request.
4. Revue + tests automatiques.
5. Merge et déploiement.

### La Pull Request

Demande de fusion d'une branche vers une autre. Elle permet la **revue de code**, le lancement des **tests automatiques** et la **traçabilité**. On protège `main` avec les *Branch protection rules*.

---

## 8. Les permissions Linux

```bash
-rwxr-xr--  1 fatou devs 1024 nov 19 script.sh
```

```
 -    rwx    r-x    r--
type  user   group  others
```

| Droit | Fichier | Dossier | Valeur |
|---|---|---|---|
| **r** | Lire | Lister | 4 |
| **w** | Modifier | Créer / supprimer dedans | 2 |
| **x** | Exécuter | Entrer (`cd`) | 1 |

| Chiffre | Lettres | Usage |
|---|---|---|
| 755 | `rwxr-xr-x` | Scripts, dossiers |
| 644 | `rw-r--r--` | Fichiers normaux |
| 600 | `rw-------` | Secrets, clés SSH |
| 777 | `rwxrwxrwx` | ⚠️ à éviter |

### Changer les droits

```bash
chmod +x script.sh           # rendre exécutable
chmod u+x,g-w fichier        # méthode lettres (u/g/o/a, +/-/=)
chmod 755 script.sh          # méthode chiffres
chmod -R 755 dossier/        # récursif
sudo chown user:groupe f     # changer le propriétaire
```

Cas fréquent en TP :

```bash
chmod +x mvnw
git update-index --chmod=+x mvnw
```

---

## 9. TP1 : Git flow et Pull Request

1. Créer le dépôt GitHub du projet fil rouge (API Spring Boot).
2. Créer la branche `develop`.
3. Développer une fonctionnalité sur `feature/...`.
4. Pousser la branche et ouvrir une PR vers `develop`.
5. Faire relire la PR par un binôme, puis merger.
6. Provoquer et résoudre un conflit.
7. Protéger la branche `main`.

```bash
git clone <url>
git switch develop
git switch -c feature/hello
git add .
git commit -m "feat: ajoute l'endpoint /hello"
git push -u origin feature/hello
# PR sur GitHub, puis après le merge :
git switch develop
git pull
git branch -d feature/hello
```

---

## 10. Questions de révision

1. Quels sont les objectifs opposés des Dev et des Ops ?
2. Que signifie CALMS ?
3. Cite les 4 métriques DORA.
4. Différence entre `git fetch` et `git pull` ?
5. Pourquoi ne pas rebaser une branche partagée ?
6. Différence entre GitFlow et GitHub Flow ?
7. À quoi sert une Pull Request ?
8. Différence entre un système centralisé et distribué ?
9. Que signifie `chmod 754` ?
