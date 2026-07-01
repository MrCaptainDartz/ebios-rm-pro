# 🛡️ EBIOS RM Pro v6.0

**Application web d'analyse de risques selon la méthodologie EBIOS Risk Manager de l'ANSSI**

![Version](https://img.shields.io/badge/version-6.0-blue)
![License](https://img.shields.io/badge/license-MIT-green)
![Client-side](https://img.shields.io/badge/100%25%20client--side-brightgreen)
![Offline](https://img.shields.io/badge/offline--ready-orange)

## 📋 Description

EBIOS RM Pro est un outil **100 % client-side** (aucun serveur requis, aucune donnée transmise) permettant de réaliser des analyses de risques complètes selon la méthodologie **EBIOS Risk Manager de l'ANSSI**, enrichie d'une intégration du référentiel **MITRE ATT&CK**.

Cette version **v6.0** est une évolution majeure d'un fork de la version v5 : refonte méthodologique (gravité des scénarios stratégiques auto-calculée, décision de traitement explicite, vérification de cohérence de l'analyse, graphes d'attaque Mermaid) et autonomie complète hors-ligne. Voir ci-dessous la section « Évolutions par rapport à la version d'origine » pour le détail des écarts.

### Fonctionnalités principales

- ✅ **5 Ateliers EBIOS RM** complets (Cadrage → Sources de risque → Scénarios stratégiques → Scénarios opérationnels → Traitement)
- 🗺️ **Graphes d'attaque Mermaid** (chemins stratégiques et opérationnels), rendus également dans l'export Word
- 📊 **Dashboard** avec indicateurs visuels, tendance de criticité par jalon et tableau de **cohérence / traçabilité** de l'analyse
- 📝 **Export Word** professionnel (6 pages, graphes Mermaid en PNG, barres de progression)
- 💾 **Import/Export JSON** avec validation par schéma et détection d'injections
- 🎯 **Base de connaissances ANSSI** intégrée (sources, objectifs, référentiels, échelles)
- ⚔️ **Référentiel MITRE ATT&CK** (tactiques/techniques) organisé en 4 phases de mode opératoire
- 📉 **Risques résiduels multi-jalons** avec cartographie avant/après et décision de traitement
- 🔌 **Fonctionnement hors-ligne** (libs locales avec repli CDN)

## 🚀 Utilisation

1. Téléchargez `EBIOS-RM-Pro.html` **et** le dossier `libs/` (à placer à côté du HTML)
2. Ouvrez `EBIOS-RM-Pro.html` dans votre navigateur (Chrome, Firefox, Edge)
3. C'est tout — fonctionne même sans connexion internet 🎉

> Sans le dossier `libs/`, l'application retombe automatiquement sur les CDN (une connexion internet est alors requise).

## 📚 Les 5 Ateliers

| Atelier | Description |
|---------|-------------|
| **1. Cadrage** | Périmètre, missions, valeurs métier (biens supports), événements redoutés (gravité + impact), socle de sécurité |
| **2. Sources de Risque** | Sources de risque, objectifs visés, couples SR/OV (motivation × ressources → pertinence), liaison ER↔couples |
| **3. Scénarios Stratégiques** | Parties prenantes (menace = D×P/M×C, radar écosystème), scénarios stratégiques (gravité = max des ER liés), graphe d'attaque |
| **4. Scénarios Opérationnels** | Modes opératoires en 4 phases (Connaître → Rentrer → Trouver → Exploiter), MITRE ATT&CK, bien support par action, vraisemblance, graphe d'attaque |
| **5. Traitement** | Cartographie G×V, plan PACS (mesures par jalon), risques résiduels multi-jalons, décision de traitement (accepter/éviter/transférer/réduire), comparaison avant/après |

## 🔄 Évolutions par rapport à la version d'origine (v5 → v6)

Ce dépôt est un fork de la version v5 (fichier d'origine `EBIOS-RM-Pro-v5.html`). La v6 reprend l'intégralité de la méthode EBIOS RM et l'enrichit. Résumé des évolutions majeures.

### 📐 Méthodologiques (méthode EBIOS RM)

| Évolution | v5 (origine) | v6 (cette version) |
|-----------|--------------|---------------------|
| **Gravité des scénarios stratégiques** | Saisie libre (opinion de l'analyste) | **Auto-calculée** = gravité max des événements redoutés liés ; recalculée automatiquement à chaque modification/suppression d'ER (`calcSSGravity`, `recomputeSSGravity`) |
| **Décision de traitement par risque** | Aucune (évaluation résiduelle seule) | Champ `decision` : **accepté / éviter / transféré / réduit** + `transferTarget` (cible de transfert obligatoire si transfert) |
| **Cohérence vraisemblance ↔ menace** | Vraisemblance saisie libre, isolée | Alerte non bloquante si une partie prenante liée est en zone « Danger » (menace ≥ 2) mais vraisemblance faible (V ≤ 2) — relie l'atelier 3 à l'atelier 4 (`checkPPThreat`) |
| **Modes opératoires** | « Connaître → Rentrer → Trouver → Exploiter » en simple label | Mapping `mitrePhase` rattachant chaque tactique MITRE à l'une des 4 phases ; grille et tableaux organisés par phase |
| **Bien support par action MITRE** | Non tracé | Champ `bs` (bien support) sur chaque action → chaîne d'attaque complète jusqu'au bien support atteint |
| **Risques résiduels** | Une évaluation résiduelle par risque | **Évaluations multi-jalons** par risque (J1-J4) ; `latestRR` sélectionne l'évaluation au jalon le plus élevé + détection des doublons |
| **Cohérence / traçabilité** | Aucun contrôle de chaîne | Tableau de cohérence vérifiant VM → ER → couples → SS → SO → risque → mesure ; signale couples non couverts, SS sans SO, ER non liés, risques critiques sans mesure/décision, etc. + nettoyage des orphelins (`cleanOrphans`) |
| **Graphes d'attaque** | Radar écosystème seul (HTML/CSS) | Graphes **Mermaid** : `genGraphSS` (SR → PP → SS → bien support → VM → ER) et `genGraph` (SR → PP → actions MITRE → ER) |
| **Jalons PACS** | `meta.jalons` configurable (1-4) mais 4 jalons toujours affichés (paramètre sous-utilisé/incohérent) | 4 jalons fixes J1-J4 (simplification) ; logique multi-jalon rigoureuse |

### ⚙️ Techniques majeures

| Évolution | v5 (origine) | v6 (cette version) |
|-----------|--------------|---------------------|
| **Dépendances** | 100 % CDN (tailwind, docx, FileSaver, Chart.js) → internet requis | **Hybride** : `libs/` locales chargées en premier, repli CDN automatique → **fonctionne hors-ligne** |
| **Mermaid** | Absent | Intégré (graphes d'attaque dans l'UI + rendu PNG dans l'export Word) |
| **Dashboard / graphiques** | 2 graphiques Chart.js (risques, PACS) | 4 graphiques (risques initiaux, **tendance de criticité par jalon**, résiduels, PACS) avec gestion du cycle de vie des charts |
| **Export Word** | 6 pages, barres de progression texte | 6 pages + **graphes Mermaid en PNG** intégrés (canvas ×2 → `ImageRun`), décisions de traitement, couverture/traçabilité |
| **Robustesse import / migration** | Validation basique | Schéma JSON avec champs obligatoires par tableau, `recomputeSSGravity` au chargement, **compatibilité arrière** des anciens schémas (`s.pp`→`ppIds`, `s.fe`→`erIds`, `m.risk`→`riskIds`) |
| **Nettoyage** | — | `cleanOrphans` : suppression des références mortes (résiduels, mesures, liens, SS) |
| **Sécurité** | CSP, échappement, sanitization, détection d'injection, validation d'import ; pas de SRI | Identique dans la conception ; reste un outil **interne 100 % client-side** |

> **Diverses corrections de bugs et renforcement de la robustesse** ont également été apportées : recalcul automatique des gravités de scénarios stratégiques (`recomputeSSGravity`, qui rafraîchit les gravités figées sur des données importées/antérieures), nettoyage des références mortes (`cleanOrphans`), gestion du cycle de vie des graphiques Chart.js (`destroy()` pour éviter les rendus en double / fuites mémoire), détection des doublons d'évaluation résiduelle par jalon, suppression du paramètre `meta.jalons` incohérent (configurable mais 4 jalons toujours affichés), et ajout du champ de saisie `scope` (présent dans le schéma de validation d'import mais sans champ de saisie en v5).

> **Inchangé entre v5 et v6** : modèle général à 5 ateliers, échelles EBIOS (G1-G4, V1-V4, D/P/M/C, motivation/ressources), base de connaissances ANSSI et MITRE ATT&CK en constantes, localStorage (clé `ebios5`), principe single-file SPA. L'atelier 5 ne comporte toujours **pas de champ « validé par »** (choix méthodologique assumé).

## 🔐 Sécurité

L'application est un outil **interne, 100 % client-side** (aucun serveur, aucune donnée transmise). Protections en place :

- **Échappement HTML** des données utilisateur (`esc()`)
- **Validation des imports JSON** par schéma (`validateJSON` / `SCHEMA`)
- **Sanitization** des entrées (longueur max, caractères filtrés)
- **Détection d'injections** (XSS, `javascript:`, `data:`, gestionnaires `on*`)
- **Content Security Policy** en place (note : `unsafe-inline` / `unsafe-eval` requis par la conception SPA inline + Mermaid)

> Outil à usage interne d'aide à l'analyse de risques ; n'a pas vocation à être exposé publiquement. Aucun SRI sur les CDN de repli.

## 💾 Stockage des données

- Données stockées dans le **localStorage** du navigateur (clé `ebios5`)
- **Aucune donnée transmise** à un serveur
- **Export JSON** pour sauvegarde et partage (avec validation à l'import)
- **Export Word** pour les rapports officiels

## 📦 Dépendances

Chargement hybride : version locale dans `libs/` d'abord, repli CDN si indisponible.

| Bibliothèque | Usage | Locale | Repli CDN |
|--------------|-------|--------|-----------|
| [Tailwind CSS](https://tailwindcss.com/) | Framework CSS | `libs/tailwindcss.js` | cdn.tailwindcss.com |
| [docx](https://docx.js.org/) | Génération de documents Word | `libs/docx.umd.min.js` | jsdelivr (docx@8.5.0) |
| [FileSaver.js](https://github.com/eligrey/FileSaver.js/) | Téléchargement de fichiers | `libs/FileSaver.min.js` | jsdelivr (file-saver@2.0.5) |
| [Chart.js](https://www.chartjs.org/) | Graphiques | `libs/chart.js` | jsdelivr (chart.js) |
| [Mermaid](https://mermaid.js.org/) | Graphes d'attaque | `libs/mermaid.min.js` | jsdelivr (mermaid@10.6.1) |

## 📁 Structure du projet

```
ebios-rm-pro/
├── EBIOS-RM-Pro.html                                  # Application principale (v6, single-file SPA)
├── libs/                                               # Dépendances locales (hors-ligne)
│   ├── tailwindcss.js
│   ├── docx.umd.min.js
│   ├── FileSaver.min.js
│   ├── chart.js
│   └── mermaid.min.js
├── EBIOS_Exemple_PME_Industrielle_2026-01-01.json      # Exemple de données
└── README.md                                           # Documentation
```

## 📄 License

MIT License - Libre d'utilisation, modification et distribution.

## 🙏 Crédits

- Méthodologie **EBIOS Risk Manager** par l'[ANSSI](https://www.ssi.gouv.fr/)
- Référentiel **MITRE ATT&CK** par [MITRE](https://attack.mitre.org/)
- Version d'origine (v5) : projet forké [`Cyber-Autopsie/ebios-rm-pro`](https://github.com/Cyber-Autopsie/ebios-rm-pro)

## 🤝 Contribution

Les contributions sont les bienvenues ! N'hésitez pas à ouvrir une issue ou une pull request.

## 📧 Contact

Pour signaler un bug ou proposer une amélioration, ouvrez une issue sur GitHub.

---

*Développé avec ❤️ pour la communauté cybersécurité française*