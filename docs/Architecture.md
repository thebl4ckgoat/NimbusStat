# Architecture — NimbusStat

Documentation technique du modèle, des mesures, de la sécurité et des pages. Sert de référence unique pour le développement et la maintenance.

## 1. Source & approche

- **Base** : modèle Power BI **Nimbus** (~40 tables, données de téléphonie Luware/Nimbus).
- **Logique** : transformation et simplification du modèle existant, pas de création.
- **Format** : projet **PBIP** (modèle en TMDL, rapport en JSON) versionné dans Git.

## 2. Tables clés

| Table | Rôle | Champs notables |
|---|---|---|
| `ServiceSessions` | Appels (grain service) | `TotalQueueTime` (s), `TotalConnectedTime`, `Hours`, `Fixed15Min`, `Date`, `ServiceId`, `OutcomeId`, `InitialCallerId` |
| `UserSessions` | Legs agents | `ConnectedTime`, `UserId`, `ServiceSessionId` |
| `UserStates` | États agents (s) | `ΣConnectedSeconds`, `ΣACWSeconds`, `ΣSelectableSeconds`, `Date` |
| `Users` | Agents | `User Name`, **`Groupe`** (colonne ajoutée) |
| `Services` | Files / IVR | `Name`, `ServiceName`, **`Groupe Téléphonie`**, **`Type file`** (colonnes ajoutées) |
| `Callers` | Appelants | `Caller` (n°/e-mail), `OriginType` (PSTN/INTL) |
| `SessionDates` | Dimension dates (jour) | `Date`, `DayOfWeekName`, `DayInWeek` |
| `ServiceSessionOutcomes` | Issues d'appel | `Outcome`, `OutcomeGroup` |

> ⚠️ `SessionDates` est une dimension **journalière** : l'heure de la journée vient de `ServiceSessions[Hours]` / `Fixed15Min`, pas de `SessionDates`.
> ⚠️ `UserStates` est reliée à `UserSessionDates`, **pas** à `SessionDates` (d'où l'usage de `TREATAS` pour l'occupation).

## 3. Colonnes calculées ajoutées

### `Services[Groupe Téléphonie]`
Regroupe les 25 files réelles en 5 groupes métier (depuis `Services[Name]`) : Equipe Téléphonie, GRH, Coordination Charleroi, Coordination Lalouvière, Coordination Lobbes, plus « Autre » (tests, Personnel - General).

### `Services[Type file]`
Classe la file en Client / Personnel / Coordination / Autre (axe indépendant du type d'appelant).

### `Users[Groupe]`
Équipe de l'agent, via `LOOKUPVALUE` sur la table **`MappingAgents`** (correspondance agent → groupe).

## 4. Mesures DAX créées

| Mesure | Définition (résumé) |
|---|---|
| `% Taux d'abandon` | Sessions `OutcomeGroup = "Hangup by Customer"` ÷ total |
| `Abandons` | Nombre de sessions abandonnées |
| `Nb appelants récurrents` | Appelants avec ≥ 3 appels |
| `% Appels répétés` | Part des appels venant d'appelants récurrents |
| `% Taux d'occupation (avec ACW)` | `(ΣConnectedSeconds + ΣACWSeconds) / (… + ΣSelectableSeconds)`, avec `TREATAS` sur date + groupe |
| `Pic de charge (heure)` | Heure au volume d'appels maximum |
| `Moyenne appels / heure` | Total ÷ heures actives (date × heure distinctes) |
| `Moyenne appels / jour` | Total ÷ `DISTINCTCOUNT(ServiceSessions[Date])` |
| `Objectif décroché` / `Objectif attente (s)` / `Objectif abandon` | Cibles pour les visuels KPI |

> Pièges connus : libellé exact **« Hangup by Customer »** ; `%Handled Rate` stocké en **points** (objectif = 90, pas 0,90) ; objectifs et valeurs doivent partager la **même échelle**.

## 5. Sécurité

### Niveau données — RLS
Un rôle par groupe, filtrant **deux** tables :

| Rôle | `Services[Groupe Téléphonie]` | `Users[Groupe]` |
|---|---|---|
| Equipe Téléphonie | = "Equipe Téléphonie" | = "Equipe Téléphonie" |
| GRH | = "GRH" | = "GRH" |
| Coordination Charleroi | = "Coordination Charleroi" | = "Coordination Charleroi" |
| Coordination Lalouvière | = "Coordination Lalouvière" | = "Coordination Lalouvière" |
| Coordination Lobbes | = "Coordination Lobbes" | = "Coordination Lobbes" |
| Direction | *(aucun filtre)* | *(aucun filtre)* |

Chaque lecteur doit être affecté à **au moins un rôle** (RLS = interdit par défaut).

### Niveau pages — Audiences d'application
- **Collaborateurs** → page synthétique uniquement.
- **Direction** → toutes les pages.

Si la visibilité par page n'est pas disponible dans le tenant, utiliser **deux rapports sur un même modèle sémantique** (un par audience).

## 6. Pages

- **Pilotage (Direction)** — synthèse, performance par agent, charge & dimensionnement.
- **Collaborateurs** — indicateurs synthétiques (sans données nominatives).
- **Détail appelant** — recherche d'un numéro et historique (Direction, RGPD).

## 7. Points RGPD

`Callers[Caller]` = donnée personnelle (numéro ou e-mail). La page Détail appelant et les données nominatives d'agents restent réservées à la Direction. Aucune donnée réelle n'est versionnée dans Git.
