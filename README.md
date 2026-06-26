# NimbusStat — Reporting Statistiques Téléphonie (ASD HO)

Reporting Power BI des statistiques de téléphonie d'Aide & Soins à Domicile Hainaut Oriental, construit par transformation du modèle **Nimbus**. Le projet est versionné au format **PBIP** (Power BI Project), donc lisible et diffable dans Git.

## Aperçu

Le rapport répond à la demande de suivi du service téléphonie : volume d'appels, qualité de service (décroché, attente, abandons), charge horaire, comportement des appelants et performance par agent. Les données sont **segmentées par groupe** (Equipe Téléphonie, GRH, Coordination Charleroi / Lalouvière / Lobbes) et **sécurisées** (RLS + audiences d'application).

## Contenu du dépôt

```
NimbusStat/
├── docs/
│   ├── README.md            → index de la documentation
│   ├── Architecture.md      → modèle de données, mesures, RLS, pages
│   └── Changelog.md         → journal des versions
├── .github/
│   ├── workflows/ci.yml     → vérifications automatiques (CI)
│   └── pull_request_template.md
├── .gitignore
├── CONTRIBUTING.md          → conventions de branches et de commits
├── Statistiques Téléphonie - Direction.pbip
├── Statistiques Téléphonie - Direction.Report/
└── Statistiques Téléphonie - Direction.SemanticModel/
```

## Prérequis

- **Power BI Desktop** (version récente, format PBIP activé).
- Accès au modèle Nimbus / au service Power BI ASD HO.
- Pour cloner : Git installé.

## Démarrer

```bash
git clone https://github.com/thebl4ckgoat/NimbusStat.git
cd NimbusStat
```

Ouvre le fichier `Statistiques Téléphonie - Direction.pbip` dans Power BI Desktop.

## Sécurité des données

Le modèle applique une sécurité au niveau des lignes (**RLS**) : chaque groupe ne voit que ses propres données ; la Direction voit l'ensemble. L'accès aux pages est géré par les **audiences** de l'application Power BI. Voir [docs/Architecture.md](docs/Architecture.md).

## Documentation

- [Architecture](docs/Architecture.md) — modèle, mesures DAX, sécurité, pages.
- [Changelog](docs/Changelog.md) — historique des versions.
- [Contribuer](CONTRIBUTING.md) — branches, commits, pull requests.

## Licence / confidentialité

Dépôt interne ASD HO. Les données réelles ne sont **pas** versionnées (seul le modèle/rapport l'est). Données personnelles (numéros, agents) soumises au RGPD.
