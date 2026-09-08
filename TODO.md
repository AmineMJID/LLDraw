# TODO LLDraw — Analyser A→Z et couvrir le dossier LLD cible (15 chapitres)

> Objectif : que le **Document LLD (PDF)** généré par LLDraw produise le dossier
> complet avec **exactement la structure cible** (15 chapitres), alimenté par les
> données saisies dans l'application (racks, devices, ports, câbles, sites, flux…).
>
> Méthode de travail convenue : implémenter **lot par lot**, l'utilisateur teste
> après chaque lot, puis on continue.

---

## A. État des lieux — ce qui existe déjà (analyse A→Z)

### A.1 Architecture technique

| Fichier | Rôle | État |
|---|---|---|
| `index.html` (415 l.) | Structure UI : topbar, sidebar, board, modales (device, LLD), popovers | ✅ complet |
| `app.js` (4 500 l.) | Toute la logique : état, racks, devices, ports, câblage, topologie, exports, persistance | ✅ complet, monolithique mais organisé en sections commentées |
| `styles.css` (1 664 l.) | Thème sombre, layout, racks, popovers, topologie | ✅ complet |
| `server.py` (148 l.) | Serveur Python stdlib : statique + API `/api/state` (GET/PUT) → `data/state.json` | ✅ fonctionnel |
| `.github/workflows/pages.yml` | Déploiement GitHub Pages (mode 💾 localStorage) | ✅ fonctionnel |
| `assets/` | Logo, favicons, photos WatchGuard, icône RJ45 | ✅ |

### A.2 Fonctionnalités déjà implémentées

1. **Workspaces** : écran d'accueil, création/suppression, historique trié,
   board indépendant par workspace, bibliothèque de devices **partagée**.
2. **Racks** : 6U→42U, déplacement, renommage, changement de taille, métriques
   live (occupation U, puissance totale + budget W, poids total + charge max kg).
3. **Devices** : photo de face avant, détection automatique des ports (analyse
   d'image), fiche d'inventaire (marque, modèle, réf., n° série, IP mgmt, VLAN,
   puissance, poids), édition au survol (double-clic), device WatchGuard permanent.
4. **Ports** : création/édition (nom, étiquette, IP, VLAN, taille 20–250 %),
   drag & drop, infobulle.
5. **Câblage physique** : mode dédié, cordons Bézier réalistes, ID (CAB-001),
   couleurs, panneau Connexions, suppression individuelle/globale.
6. **Topologie logique** : vue dédiée, nœuds générés depuis les racks, liens
   (nom, débit, VLAN, style, couleur), import des câbles physiques.
7. **Infos dossier LLD** (modale 📘) : client, auteur, version,
   historique des révisions, **registre VLANs & subnets** (+ détection auto
   depuis les ports/liens).
8. **Exports** : PNG, Plan PDF 1 page, **Document LLD PDF** (7 sections),
   XLSX (4 feuilles : Inventaire, Câblage, Ports, Racks), 3 CSV (inventaire,
   câblage, ports).
9. **Transverse** : recherche globale multi-workspaces, annuler/rétablir
   (40 niveaux), sauvegarde serveur + secours localStorage, zoom/pan infini.

### A.3 Structure actuelle du PDF LLD généré

```
Page de garde (client, auteur, version, date, stats, révisions)
1. Synthèse des racks (capacités)
2. Inventaire des devices
3. Plan d'adressage & ports
4. Tableau de câblage
5. Registre VLANs & subnets
6. Topologie logique (image)
7. Élévations des racks (image)
```

➡️ **7 sections génériques** : rien sur les sites, le FAI, l'interconnexion,
les catégories d'équipements, ni les flux réseau.

---

## B. Analyse des écarts — chapitre par chapitre (structure cible)

Légende : ✅ existant · 🟡 partiel · ❌ absent

| # | Chapitre cible | État | Ce qui manque |
|---|---|---|---|
| 1 | **Objectif du document** | ❌ | Champ texte « objectif » éditable + rendu PDF |
| 2.1 | **Aperçu — Informations sur le site** | ❌ | Concept de **site** inexistant : fiche site (nom, adresse, contacts, description) + rattachement des racks à un site |
| 2.2 | **Aperçu — Infrastructure existante** | ❌ | Texte structuré/à champs libres sur l'existant |
| 3 | **Architecture cible** | 🟡 | Texte d'architecture + tableau récapitulatif des équipements → nécessite les **catégories de devices** |
| 4 | **Nomenclature & Adressage IP Global** | 🟡 | Registre VLANs existant mais : pas de table **nomenclature** (règles de nommage) ni de colonne **site** dans l'adressage |
| 5.1 | **FAI — Informations & Configuration** | ❌ | Bloc FAI : opérateur, offre, type de lien, débit, IP publiques, CPE, notes de config |
| 5.2 | **FAI — Câblage** | ❌ | Table de câblage **filtrée** par chapitre/domaine |
| 6.1 | **Interconnexion site 2 site — Infos & Config** | ❌ | Bloc interco : techno (IPsec/MPLS/SD-WAN…), endpoints publics, subnets locaux/distansts, routage, chiffrement |
| 6.2 | **Interconnexion — Câblage** | ❌ | Idem 5.2 |
| 7 | **Firewall** | 🟡 | Le device WatchGuard existe comme device photo, mais pas de **catégorie** ni de chapitre dédié (équipements + config + câblage) |
| 8.1–8.5 | **Switching** (INFRA, LAN Site B, Aruba AP Site A, Aruba AP Site B, LAN Site A) | ❌ | Sous-chapitres par **zone/site** : catégories Switch + AP, rattachement site/zone, notes de config par sous-chapitre |
| 9 | **Serveurs** | ❌ | Catégorie « Serveur » + chapitre auto (équipements, IP, config) |
| 10 | **Stockage** | ❌ | Catégorie « Stockage » (NAS/SAN) + chapitre auto |
| 11 | **Intrusion (IDS)** | ❌ | Catégorie « IDS » + chapitre auto |
| 12 | **CCTV** | ❌ | Catégorie « CCTV » + chapitre auto |
| 13 | **Pointage (SPO)** | ❌ | Catégorie « Pointage » + chapitre auto |
| 14 | **Flux réseau et diagram** | 🟡 | Topologie logique existante (image) mais pas de **matrice de flux** (source, destination, protocole/port, sens, usage) |
| 15 | **Cablage/Rack** | 🟡 | Tableau de câblage + élévations existent, mais à renuméroter en ch. 15 et enrichir (câblage global + synthèse racks + élévations) |

### B.1 Les 3 fondations manquantes (briques transverses)

1. **Catégories de devices** — indispensable pour générer automatiquement les
   chapitres 7 à 13 (Firewall, Switching, Serveurs, Stockage, IDS, CCTV,
   Pointage…). Aujourd'hui un device n'a aucun rôle métier.
2. **Sites** (Site A / Site B…) — indispensable pour les chapitres 2, 6, 8.x
   et pour filtrer/adresser par site. Aujourd'hui un rack n'appartient à aucun site.
3. **Champs documentaires par chapitre** — l'application ne sait stocker que
   client/auteur/version/révisions/VLANs. Il faut des zones de texte et des
   tables dédiées à chaque chapitre (objectif, existant, architecture,
   nomenclature, FAI, interco, notes de config par catégorie, flux…).

---

## C. TO-DO — lots d'implémentation incrémentale

> ⚠️ Chaque lot = une session de test. On n'attaque le suivant qu'après
> validation. Rétro-compatibilité garantie à chaque lot (normalisation de
> l'état chargé + undo/redo).

### ✅ Lot 1 — Squelette documentaire (ch. 1, 2.2, 3, 4) — **FAIT**
- [x] Modale 📘 réorganisée en onglets : *Document / Réseau* (+ Sites et
      Chapitres visibles mais désactivés, activés aux lots 2 et 5)
- [x] Onglet Document : **Objectif du document**, **Infrastructure
      existante**, **Architecture cible** (textes multi-lignes) + client /
      auteur / version / révisions
- [x] Table **Nomenclature** éditable (type d'objet → préfixe → exemple →
      règle) + bouton « Générer depuis les devices » (détecte les préfixes
      utilisés, type deviné : FW, SW, SRV, NAS, IDS, CAM…)
- [x] Registre VLANs devient **Adressage IP Global** : colonne **Site** ajoutée
- [x] **Générateur PDF LLD restructuré** : **sommaire** (page 2, 27 entrées
      avec numéros de page exacts) et squelette des **15 chapitres** dans
      l'ordre cible ; ch. 1, 2.2, 3 (textes) et 4 (nomenclature + registre
      VLANs + plan d'adressage & ports) remplis ; ch. 3.1 = inventaire ;
      ch. 5→13 affichent « Section à compléter » ; ch. 14 = topologie ;
      ch. 15 = synthèse racks + câblage + élévations
- [x] Sauvegarde/undo/redo des nouveaux champs (via `pushHistory` + normalisation)
- [x] Bonus : correction d'un bug PDF préexistant (double définition `/XObject`
      quand le document contient topologie **et** élévations — l'image
      d'élévations pouvait être ignorée par certains lecteurs PDF)

### ✅ Lot 2 — Sites (ch. 2.1 + base des ch. 6 et 8) — **FAIT**
- [x] Onglet **🏢 Sites** dans la modale 📘 : cartes éditables (nom, adresse,
      contacts, description), ajout/suppression ; **Site A + Site B créés par
      défaut** (workspaces nouveaux et anciens) ; la suppression d'un site
      détache les racks rattachés (message + Ctrl+Z)
- [x] **Rattachement des racks à un site** : sélecteur dans l'en-tête du rack
      + **pastille colorée** (8 couleurs par position)
- [x] **Filtre par site** dans le panneau de gauche (racks des autres sites
      atténués), options rafraîchies à l'ouverture d'un workspace et par undo
- [x] PDF ch. 2.1 : tableau des sites + racks par site (taille, occupation,
      devices, puissance, poids) ; racks sans site « — »
- [x] Colonne **Site** dans Inventaire / Ports / Synthèse racks (CSV, XLSX,
      PDF) + pastille et nom du site sur les exports PNG / PDF 1 page
- [x] Correctif rétroactif Lot 1 : la sauvegarde de la modale 📘 ne persistait
      pas Objectif / Existant / Architecture / Nomenclature — corrigé

### ⬜ Lot 3 — Catégories de devices (prépare ch. 3.1, 7→13)
- [ ] Champ **Catégorie** sur les devices (bibliothèque + exemplaires) :
      Routeur/FAI, Firewall, Switch, AP WiFi, Serveur, Stockage, IDS, CCTV,
      Pointage (SPO), Onduleur, Panneau de brassage, Autre
- [ ] UI : sélecteur dans la modale device, badge dans les cartes
      bibliothèque, badge dans la fiche popover, filtre par catégorie dans
      la sidebar
- [ ] Catégorie visible sur les nœuds de la topologie
- [ ] PDF ch. 3.1 « Équipements » : tableau récapitulatif par catégorie
      (nb, modèles, sites) + inventaire complet

### ⬜ Lot 4 — FAI & Interconnexion site 2 site (ch. 5, 6)
- [ ] Onglet Réseau (modale 📘) — bloc **FAI** : opérateur, offre, type de
      lien (FTTH/FTTO/EoC…), débit montant/descendant, bloc IP publiques,
      équipement CPE (modèle, IP), notes de configuration
- [ ] Bloc **Interconnexion** : technologie (IPsec, MPLS, SD-WAN, LAN-to-LAN…),
      endpoints publics A/B, subnets locaux/distansts, protocole de routage,
      chiffrement, notes de configuration
- [ ] **Câblage par chapitre** : permettre d'étiqueter un câble avec un
      « domaine » (FAI, Interco, Switching…) pour filtrer les tables 5.2 / 6.2
- [ ] PDF ch. 5 et 6 complets (infos + config + table de câblage filtrée)

### ⬜ Lot 5 — Chapitres par catégorie (ch. 7 → 13)
- [ ] Notes de configuration éditables **par catégorie** (onglet Chapitres)
- [ ] Sous-chapitres **Switching par zone/site** : zones personnalisables
      (valeurs par défaut reproduisant la cible : INFRA, LAN Site A, LAN Site B,
      Aruba AP Site A, Aruba AP Site B) — rattachement des devices
      Switch/AP à une zone
- [ ] Génération automatique des chapitres 7→13 : pour chaque catégorie
      présente → tableau des équipements (rack, site, modèle, IP mgmt, VLAN,
      ports) + ports/IP + câblage concerné + notes de config
- [ ] Chapitres vides : soit masqués, soit « aucun équipement » (option)

### ⬜ Lot 6 — Flux réseau (ch. 14)
- [ ] Éditeur de **matrice de flux** : nom, source, destination,
      protocole/ports, sens (unidirectionnel/bidirectionnel), usage
- [ ] PDF ch. 14 : tableau des flux + rappel du diagramme de topologie
- [ ] (Bonus) Surcouche « flux » dans la vue Topologie : mise en évidence
      des liens concernés par un flux sélectionné

### ⬜ Lot 7 — Câblage/Rack + exports & finitions (ch. 15)
- [ ] PDF ch. 15 : tableau de câblage global + synthèse racks + élévations
      (reprend les sections actuelles, renumérotées)
- [ ] XLSX : nouvelles feuilles — *Flux*, *Sites*, *Nomenclature*,
      *Adressage IP*, (+ colonne Site/Catégorie partout)
- [ ] CSV : exports des nouvelles tables (flux, adressage)
- [ ] Recherche globale étendue aux sites et aux flux
- [ ] README mis à jour + incrément du n° de version de cache (`?v=`)
- [ ] Vérification PDF complet vs sommaire (numéros de pages corrects)

---

## D. Décisions à valider au fil des lots

1. **Switching 8.1→8.5** : sous-chapitres **fixes** (comme la cible) ou
   **dynamiques** (générés selon les sites/zones déclarés) ? → proposition :
   dynamiques avec jeu par défaut = cible exacte.
2. **Chapitres vides** (ex : pas de CCTV) : masqués ou affichés « néant » ?
   → proposition : affichés avec mention, pour garder une numérotation stable.
3. **Multi-sites par workspace** : un workspace = un dossier LLD complet
   (2 sites), ou un workspace par site ? → proposition : dossier complet
   multi-sites dans un workspace (cohérent avec la cible).
4. **Règles de pare-feu** (politiques NAT/filtering détaillées) : hors périmètre
   pour l'instant, ou table éditable dans le ch. 7 au lot 5 ?

---

*Document créé le 2026-09-08 — à cocher au fur et à mesure des lots.*
