# Contribuer à NimbusStat

Ce document décrit la façon de travailler sur le dépôt : stratégie de branches, nommage des commits et processus de pull request. L'objectif est de garder un historique clair et un `main` toujours déployable.

## 1. Stratégie de branches (GitHub Flow)

Modèle simple, adapté aux projets PBIP et au travail en petite équipe.

- **`main`** — branche de référence, **toujours déployable**. Protégée : pas de push direct, fusion uniquement via pull request.
- **Branches de travail** — créées à partir de `main`, courtes, une par sujet :
  - `feature/<sujet>` — nouvelle fonctionnalité (ex. `feature/page-collaborateurs`)
  - `fix/<sujet>` — correction (ex. `fix/taux-abandon-libelle`)
  - `docs/<sujet>` — documentation seule (ex. `docs/architecture-rls`)
  - `chore/<sujet>` — maintenance, configuration

Cycle : `main` → créer une branche → développer → ouvrir une PR → revue + CI → fusion dans `main` → suppression de la branche.

```bash
git switch main && git pull
git switch -c feature/page-collaborateurs
# ... travail ...
git push -u origin feature/page-collaborateurs
# ouvrir la PR sur GitHub
```

> Alternative plus lourde (Git Flow avec `develop` + branches `release/*`) : utile seulement si plusieurs versions cohabitent. Pour ce projet, GitHub Flow suffit.

## 2. Nommage des commits (Conventional Commits)

Format : `type(portée): description courte à l'impératif`

```
feat(report): ajoute la page Collaborateurs synthétique
fix(model): corrige le libellé "Hangup by Customer" du taux d'abandon
docs(architecture): documente les rôles RLS
refactor(measure): simplifie le calcul du taux d'occupation
chore(ci): ajoute la validation JSON
```

**Types** : `feat` (fonctionnalité), `fix` (correction), `docs`, `refactor`, `perf`, `chore`, `ci`.

**Portées suggérées pour Power BI** : `model` (modèle/TMDL), `measure` (mesure DAX), `report` (pages/visuels), `rls` (sécurité), `docs`.

Règles : description à l'impératif présent, en minuscule, sans point final, ≤ 72 caractères. Détails éventuels dans le corps du message (séparé par une ligne vide).

### Modèle de message (optionnel)

Un fichier `.gitmessage` est fourni ; pour l'activer localement :

```bash
git config commit.template .gitmessage
```

## 3. Pull requests

- Une PR = un sujet. Titre au format Conventional Commit.
- Remplir le gabarit de PR (description, impact, captures, points de vigilance RGPD/RLS).
- La CI (voir `.github/workflows/ci.yml`) doit être verte.
- Au moins **1 relecture** avant fusion ; préférer **Squash and merge** pour garder un historique propre.

## 4. Versions

Les versions sont taguées sur `main` au format **SemVer** : `v<MAJEUR>.<MINEUR>.<CORRECTIF>` (ex. `v1.2.0`). Chaque version est consignée dans [docs/Changelog.md](docs/Changelog.md).

```bash
git tag -a v1.0.0 -m "Première version validée"
git push origin v1.0.0
```
