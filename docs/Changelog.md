# Changelog

Toutes les modifications notables de NimbusStat sont consignées ici.
Format inspiré de « Keep a Changelog » ; versions en SemVer (`vMAJEUR.MINEUR.CORRECTIF`).

## [Non publié]

### Ajouté
- Page Collaborateurs synthétique (sans données nominatives).
- Page Détail appelant (recherche par numéro + historique).
- Mesures : taux d'abandon, appels répétés, occupation (avec ACW), pic de charge, moyennes par heure/jour.
- Colonnes `Services[Groupe Téléphonie]`, `Services[Type file]`, `Users[Groupe]`.
- Table `MappingAgents` (agent → groupe).
- Rôles RLS par groupe + Direction.

### À faire
- Visuels Transferts (taux + destinations).
- Entrants vs sortants, Motifs IVR.
- Finaliser les audiences d'application.

## [v0.1.0] — 2026-06-XX

### Ajouté
- Initialisation du projet PBIP NimbusStat (modèle Direction + rapport).
- Maquette HTML, document d'analyse et de conception technique.
