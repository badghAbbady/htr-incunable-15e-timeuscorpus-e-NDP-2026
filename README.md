# htr-incunable-15e-timeuscorpus-e-NDP


Pipeline de reconnaissance automatique de l'écriture manuscrite (HTR) appliqué aux registres médiévaux du chapitre de Notre-Dame de Paris (corpus e-NDP, 1326-1504), développé dans le cadre du module **« Vision par ordinateur »** — Master Data/IA, MD5 2026, **HETIC**.

> **Volet 1/2** : ce dépôt couvre le pipeline Computer Vision & HTR. Les transcriptions produites alimentent le Volet 2 (analyse linguistique).
>
> **Note sur le nom** : le corpus e-NDP est constitué de **manuscrits** (registres administratifs manuscrits, écriture cursive). Le terme *incunable* désigne des ouvrages **imprimés** antérieurs à 1501 et ne s'applique pas à ce corpus — il a donc été évité dans le nom du dépôt pour rester cohérent avec la convention `htr-[corpus]-[année]` du brief.

## Équipe

| Membre | Rôle |
|---|---|
| Benzouine Rime | Responsable technique (merge requests, revue de code) |
| Bouchhar Fatima Ezzahra | Responsable documentation (article, README, model card) |
| El Abaddy Badr | Responsable données & expérimentation (corpus, licences, journal d'essais) |

*Répartition à 3 personnes sur 4 rôles recommandés : El Abaddy Badr cumule les rôles données et expérimentation. Cette attribution est une proposition par défaut, ajustable en équipe.*

## Contexte du projet

La numérisation massive des fonds patrimoniaux (Gallica, IIIF) a rendu des millions de pages de manuscrits accessibles en image, sans pour autant les rendre exploitables : une image n'est pas un texte. La transcription manuelle par des paléographes, bien que fiable, ne peut pas être réalisée à l'échelle de ces volumes. Ce projet construit un pipeline de reconnaissance automatique de l'écriture manuscrite (HTR) — du scan brut jusqu'à un jeu de transcriptions structuré et validé — appliqué aux registres administratifs manuscrits du chapitre de Notre-Dame de Paris (corpus e-NDP, 1326-1504). Il s'inscrit dans le Volet 1 d'un projet en deux parties : les transcriptions produites ici alimenteront un module d'analyse linguistique (Volet 2).

## Structure du dépôt

```
.
├── README.md
├── pyproject.toml              # dépendances figées
├── src/                        # code source (prétraitement, segmentation, HTR, agrégation)
├── tests/                       # suite de tests pytest
├── experiments/
│   └── journal.jsonl            # journal de toutes les expériences menées
├── dataset_nlp/                 # JSON de transcriptions validé par le data contract
├── segmentations/                # PAGE XML ou JSON de polygones (réutilise le PAGE XML e-NDP)
├── MODEL_CARD.md
├── CONVENTIONS_TRANSCRIPTION.md
├── DATA_SOURCES.md
└── article_draft.md / paper/    # article scientifique
```

## Données

- **e-NDP** — registres administratifs (« conclusions ») du chapitre de la cathédrale Notre-Dame de Paris, cotes AN LL105-LL128, période 1326-1504. 512 pages, 34 231 lignes annotées, ~98 % latin / reste français, écriture cursive (~18 mains). Licence **CC-BY 4.0**. DOI : [10.5281/zenodo.7575693](https://doi.org/10.5281/zenodo.7575693).
- Transcriptions déjà fournies au format **PAGE XML**, avec annotation de layout sur 364 pages (5 régions : block, liste, entrée, date, numérotation) — directement réutilisable pour la contrainte de réutilisabilité des données de segmentation.
- Un modèle de référence publié par le projet e-NDP atteint un CER de 9,7 % sur ce corpus (point de comparaison, pas notre baseline).

Détail complet des sources, volumes et licences : voir [`DATA_SOURCES.md`](./DATA_SOURCES.md).
Conventions éditoriales de transcription : voir [`CONVENTIONS_TRANSCRIPTION.md`](./CONVENTIONS_TRANSCRIPTION.md).

## Installation

```bash
git clone <url-du-depot>
cd htr-cremma-catmus-medieval-2026
pip install -r requirements.txt   # ou: poetry install
```

*[À compléter une fois l'environnement figé : version Python, dépendances clés (transformers, peft, kraken, opencv-python, jsonschema...).]*

## Reproduire les résultats

1. Télécharger les données : `[commande à compléter]`
2. Vérifier l'intégrité du split de test : `[commande à compléter, hash SHA-256]`
3. Lancer le prétraitement : `[commande à compléter]`
4. Lancer l'entraînement / fine-tuning : `[commande à compléter]`
5. Lancer l'évaluation sur le test scellé : `[commande à compléter]`

Un seed fixe (`42`) est appliqué à toutes les sources d'aléatoire via une fonction unique `fixer_seeds()`, appelée en début de chaque script.

## Résultats attendus

| Métrique | Seuil de validation | Seuil d'excellence | Résultat obtenu |
|---|---|---|---|
| CER global | < 15 % | < 8 % | *[à compléter]* |
| WER global | < 25 % | < 15 % | *[à compléter]* |
| Taux needs_review | < 30 % | < 20 % | *[à compléter]* |
| IoU segmentation | > 0,75 | > 0,85 | *[à compléter]* |

Détails complets (IC bootstrap, comparaison de variantes) : voir [`MODEL_CARD.md`](./MODEL_CARD.md) et l'article scientifique.

## Tests

```bash
pytest tests/
```

La suite couvre : les transformations de prétraitement (formes, types, plages de valeurs), la validation du schéma JSON du data contract, et un test de non-régression sur le CER.

## Organisation de l'équipe

*[Décrire ici la répartition effective des tâches au fil du projet : qui a fait quoi, comment les revues de code ont été menées (1 reviewer minimum par merge request), rythme des stand-ups/rétrospectives.]*

## Licence du code

*[À préciser — ex. MIT pour le code source ; les données suivent leurs licences d'origine, voir DATA_SOURCES.md.]*
