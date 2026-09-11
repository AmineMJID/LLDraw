# 🎬 Datacenter de démonstration

Workspace **« Datacenter Démo »** prêt pour une présentation : un datacenter
réaliste, complet et « bien rempli », construit pour montrer toutes les
capacités de LLDraw en une seule session.

## Contenu

| | |
|---|---|
| **Baies** | RACK-A 18U (siège Casablanca) · RACK-B 16U (agence Rabat) |
| **Pare-feu** | 2 × WatchGuard Firebox M390 (FW-01, FW-02) |
| **Routeurs** | 2 × Peplink Balance 20X SD-WAN (RTR-01, RTR-02) |
| **Hyperconvergence** | 3 × Nutanix NX-3155-G6 (NX-01 à NX-03, cluster AHV) |
| **Serveur physique** | 1 × Dell PowerEdge R740 (SRV-DELL-01) |
| **Stockage** | 2 × Synology RS1221+ (NAS-01 siège, NAS-02 réplication) |
| **WiFi** | 2 × Aruba AP-515 (AP-ARUBA-A / B, zones de switching dédiées) |
| **Environnement** | 2 × AKCP sensorProbe2+ (température / hygrométrie) |
| **Brassage** | 5 panneaux 24×RJ45 CAT 6A + 3 passe-câbles à brosse |
| **Câblage** | 26 cordons, tous raccordés (domaines FAI / LAN / SAN / Mgmt / Firewall) |
| **Topologie** | 14 nœuds / 14 liens, tunnel SD-WAN SpeedFusion entre sites |
| **Flux** | 6 flux documentés (NAT, DMZ, réplication NAS, sauvegarde, supervision, SD-WAN) |
| **Garanties** | 14 garanties suivies — SRV-DELL-01 **expirée**, FW-01 **à renouveler** (< 90 j), le reste actif |
| **Dossier LLD** | Client, FAI (MT/Inwi), interco, 9 règles de nomenclature, 6 VLANs, 2 sites, notes par chapitre |

## Parcours de présentation suggéré (~10 min)

1. **Écran d'accueil** — ouvrir le workspace « Datacenter Démo ».
2. **Vue Baies** — faces avant réalistes, 2 baies reliées à leurs sites
   (pastilles colorées), ports avec IP/VLAN au survol.
3. **Mode Câblage** 🎯 — 26 cordons colorés par domaine : orange FAI,
   bleu LAN, vert 10G, violet management, rose stockage.
4. **Vue Topologie** 🌐 — tunnel SD-WAN en pointillés entre les deux sites,
   sélecteur de flux ( choisir « Réplication NAS » pour illuminer NAS-01/NAS-02).
5. **Recherche globale** 🔍 — taper « NAS », « VLAN 30 » ou « Rabat » : résultats
   devices / ports / sites / flux.
6. **Fiche LLD** 📘 — tous les onglets remplis : Document, Réseau (FAI,
   interco, nomenclature, VLANs), Sites, Flux, Chapitres (notes + zones).
7. **Garanties** 🛡️ — survoler les baies : pastilles vertes sur les
   équipements sous garantie, **orange** sur le FW-01 (UTM à renouveler) et
   **rouge** sur le SRV-DELL-01 (garantie expirée) ; double-clic sur
   « Fin de garantie » dans la fiche pour ajuster la date.
8. **Exports** 📤 — PDF LLD (17 pages, avec le tableau *Suivi des garanties* au
   ch. 3.1), XLSX 9 feuilles, CSV (inventaire, câblage, ports, sites,
   garanties, nomenclature, flux).

## Fichiers

- `Datacenter-Demo-LLD.pdf` — dossier LLD complet généré depuis la démo
- `plan-baies.jpg` / `topologie.jpg` — rendus du plan et du diagramme
  (réutilisables dans des slides)
- `../demo_datacenter.py` — script qui (re)construit le workspace démo dans
  `data/state.json` et régénère les images (nécessite Pillow :
  `pip install pillow`)

## Régénérer la démo (Windows, Linux, macOS)

```bash
python demo_datacenter.py      # ou python3 selon la machine
```

- Nécessite Pillow : `pip install pillow`
- Ajoute (ou remplace) le workspace « demo-dc » dans `data/state.json` et
  rafraîchit `demo/plan-baies.jpg` + `demo/topologie.jpg`
- Le workspace existant n'est pas touché ; une sauvegarde de l'état est créée
  (`data/state.json.bak-demo`) si un état existait déjà

⚠️ **La démo vit dans `data/state.json`, non versionné** : elle ne suit pas Git.
Sur une nouvelle machine (ou après un clone frais), lancer le script une fois
pour faire apparaître le workspace, puis **recharger la page (F5)** — le
serveur est la source de vérité. Le PDF LLD, lui, se génère depuis
l'application : menu **Exporter → 📕 Document LLD**.
