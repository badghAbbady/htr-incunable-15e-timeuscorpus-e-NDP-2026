# Traitement automatique de manuscrits anciens : un pipeline de reconnaissance d'écriture manuscrite (HTR) pour le vieux et moyen français

*Master Data/IA, module Vision par ordinateur, MD5 2026 — Benzouine Rime, Bouchhar Fatima Ezzahra, El Abaddy Badr. Établissement à compléter.*

---

## Résumé

*[À rédiger en dernier, une fois les résultats disponibles — env. 200 mots. Doit couvrir : problème adressé, approche, résultats quantitatifs principaux (CER, WER), conclusion.]*

---

## 1. Introduction

La numérisation massive des fonds patrimoniaux a transformé l'accès aux manuscrits anciens, mais elle n'a pas résolu le problème central de leur exploitation scientifique : une image n'est pas un texte. Sans transcription, ces documents restent largement inaccessibles à la recherche plein texte, à l'extraction d'entités nommées ou à toute analyse statistique. La transcription manuelle par des paléographes, bien qu'irremplaçable en termes de qualité, ne peut pas être réalisée à l'échelle des volumes aujourd'hui numérisés : son coût horaire et le temps nécessaire par page la rendent incompatible avec des corpus de plusieurs milliers de pages.

C'est dans ce contexte qu'émerge le besoin de pipelines de reconnaissance automatique de l'écriture manuscrite (HTR, *Handwritten Text Recognition*) capables de produire des transcriptions exploitables à partir d'images de pages manuscrites. Des initiatives comme CREMMA, CATMuS ou HTR-United ont montré qu'il est possible de constituer des corpus annotés de qualité et d'entraîner des modèles performants sur des écritures médiévales spécifiques. Ces avancées restent toutefois limitées par la rareté des données d'entraînement disponibles pour chaque type de manuscrit : chaque copiste, chaque siècle, chaque région dialectale présente des particularités graphiques qui nécessitent une adaptation fine des modèles génériques.

Ce projet a pour objectif de construire, documenter et évaluer un pipeline complet de transcription automatique, depuis l'image brute jusqu'à un jeu de données structuré exploitable par une chaîne de traitement linguistique en aval. Les contributions de ce travail sont les suivantes :

- la constitution d'un corpus de travail à partir du jeu de données **e-NDP** (registres du chapitre de Notre-Dame de Paris, 1326-1504), distribué sous licence libre ;
- l'implémentation d'une chaîne de prétraitement et de segmentation reproductible, produisant à la fois des transcriptions et leurs polygones de localisation ;
- le fine-tuning et l'évaluation comparée de modèles HTR (TrOCR, Kraken) sur ce corpus, avec mesure du CER, du WER et des intervalles de confiance associés ;
- une réflexion sur les biais de représentation du corpus utilisé et les limites du pipeline obtenu.

*[À compléter : un paragraphe final annonçant le plan de l'article une fois la structure finale stabilisée.]*

---

## 2. État de l'art

**Architectures pour la reconnaissance d'écriture manuscrite.** Les systèmes HTR reposent historiquement sur des architectures combinant des réseaux convolutifs et récurrents (CRNN), entraînés avec une fonction de perte CTC (*Connectionist Temporal Classification*). Le moteur **Kraken**, développé dans la continuité de Calfa/Kraken et largement utilisé dans l'écosystème **eScriptorium**, s'appuie sur ce paradigme et propose un module dédié de segmentation de ligne de base, **BLLA** (*Baseline Layout Analysis*), permettant d'extraire à la fois les lignes de texte et leurs polygones. Plus récemment, l'arrivée des architectures *transformer* a donné naissance à des modèles encodeur-décodeur visuels comme **TrOCR** (Microsoft), qui traite l'image de la ligne de texte comme une séquence de patches visuels et génère la transcription token par token, sans recourir à un alignement CTC explicite. Ces deux familles d'architectures, bien que conceptuellement différentes, sont aujourd'hui utilisées de façon complémentaire dans les pipelines HTR de recherche, notamment pour la comparaison de performances par validation croisée.

**Segmentation de structure de page.** En amont de la reconnaissance proprement dite, la segmentation de la page en régions (texte, illustration, marge) puis en lignes de texte conditionne fortement la qualité finale des transcriptions. Des approches comme **dhSegment** ou, plus récemment, **SAM** (*Segment Anything Model*, Meta), permettent une segmentation générique de zones sans nécessiter un entraînement spécifique au domaine, tandis que **Kraken BLLA** reste la référence pour la segmentation fine de lignes de base sur manuscrits médiévaux.

**Jeux de données médiévaux.** Le projet **CREMMA** (*Corpus pour la Recherche d'Écritures Manuscrites Médiévales en Ancien français*) a constitué un corpus annoté couvrant une quinzaine de manuscrits du XIIIe au XVe siècle. Le projet **CATMuS** (*Consistent Approach to Transcribing ManuScript*) a fédéré plusieurs initiatives (CREMMA, GalliCorpora, HTRomance, DEEDS) pour produire un corpus normalisé de plus de 160 000 lignes annotées, couvrant environ 200 manuscrits du VIIIe au XVIIe siècle dans une dizaine de langues. Le projet **e-NDP**, mené par le LaMOP en partenariat avec les Archives nationales et la BnF, a quant à lui produit une vérité terrain de plus de 34 000 lignes sur les registres administratifs (« conclusions ») du chapitre de la cathédrale Notre-Dame de Paris (1326-1504), avec des annotations de structure de page directement fournies au format PAGE XML — c'est ce corpus que nous retenons pour ce travail (voir section 3). Le catalogue **HTR-United** référence aujourd'hui plusieurs dizaines de jeux de données HTR sous licence ouverte, facilitant leur réutilisation pour l'entraînement de nouveaux modèles.

**Métriques d'évaluation.** L'évaluation des systèmes HTR repose principalement sur le **CER** (*Character Error Rate*, distance de Levenshtein normalisée par la longueur de la référence) et le **WER** (*Word Error Rate*), ce dernier étant plus sensible aux erreurs lexicales complètes. La robustesse statistique de ces mesures est généralement renforcée par un calcul d'intervalle de confiance par ré-échantillonnage bootstrap, et la comparaison de deux variantes de modèle peut être validée par un test de McNemar. Enfin, l'accord inter-annotateurs (**IAA**, *Inter-Annotator Agreement*), mesuré en CER entre transcriptions humaines indépendantes sur un même passage, constitue un plafond de performance réaliste au-delà duquel un modèle ne peut raisonnablement pas être évalué comme « meilleur » que l'humain.

*[À compléter : ajouter les références bibliographiques complètes (BibTeX) au fil de la rédaction.]*

---

## 3. Données

**Corpus retenu.** Le corpus de travail est constitué à partir du jeu de données **e-NDP** (DOI : 10.5281/zenodo.7575693), produit par le LaMOP (Université Paris 1 Panthéon-Sorbonne) en partenariat avec les Archives nationales, la BnF, l'École nationale des chartes et la Bibliothèque Mazarine, dans le cadre du projet ANR *« Notre-Dame de Paris and its cloister: places, people, life »*. Il s'agit de registres administratifs (« conclusions ») du chapitre de la cathédrale Notre-Dame de Paris, conservés aux Archives nationales sous les cotes LL105 à LL128. Ces documents relèvent de la catégorie des manuscrits documentaires (par opposition aux manuscrits littéraires ou liturgiques) : il s'agit de comptes-rendus de réunions administratives, et non d'imprimés — le corpus ne doit pas être confondu avec des incunables, qui désignent des ouvrages imprimés antérieurs à 1501.

**Caractéristiques du corpus.** Le jeu de vérité terrain disponible comprend 512 pages extraites des 26 registres couvrant la période 1326-1504, soit 34 231 lignes de texte annotées, 205 083 tokens et environ 3,32 millions de caractères. L'écriture relève d'une cursive documentaire (fin XIIIe-XVIe siècle), produite par au moins 18 mains différentes identifiées. Le texte est rédigé à 98 % en latin, le reste en français, principalement dans des formules ou notes marginales — une caractéristique multilingue à prendre en compte dans le choix et l'évaluation du modèle HTR. Les transcriptions ont été produites en deux campagnes par 12 contributeurs (historiens et paléographes) entre 2021 et 2022, sous eScriptorium.

**Annotations de structure disponibles.** Le corpus fournit déjà des transcriptions au format **PAGE XML**, directement réutilisables dans notre pipeline pour la contrainte de réutilisabilité des données de segmentation. Une annotation de layout est en outre disponible sur 364 pages, avec un vocabulaire de 5 régions (*block*, *liste*, *entrée*, *date*, *numérotation*), ce qui permettra d'évaluer notre propre segmentation de structure de page par comparaison directe.

**Licence.** Le corpus est distribué sous licence **CC-BY 4.0**, compatible avec la contrainte de licence libre imposée par le projet. L'attribution complète (auteurs, DOI, financement ANR) sera consignée dans `DATA_SOURCES.md`.

**Point de comparaison existant.** Le projet e-NDP rapporte un CER de référence de 9,7 % obtenu avec son propre modèle Kraken entraîné sur l'ensemble du corpus. Cette valeur ne constitue pas notre baseline (qui sera mesurée sur notre propre split sans fine-tuning), mais offre un ordre de grandeur réaliste pour situer nos résultats.

**Constitution des splits.** Le corpus sera partitionné en ensembles d'entraînement, de validation et de test avant tout développement de modèle, en stratifiant par registre (et non par ligne) afin d'éviter toute fuite d'information liée à la similarité d'écriture d'une même main sur un même volume. Le jeu de test sera scellé et son intégrité garantie par un hachage SHA-256, calculé dès sa constitution et jamais recalculé avant l'évaluation finale.

**Conventions de transcription.** Le corpus e-NDP applique déjà des conventions explicites (résolution des abréviations, casse des entités nommées, transcription des i/u consonantiques en j/v, marquage des corrections par `$...$`). Notre équipe documentera dans `CONVENTIONS_TRANSCRIPTION.md` si ces conventions sont conservées telles quelles ou adaptées pour le pipeline.

---

## 4. Méthodes
*[À rédiger — pipeline complet : prétraitement, segmentation, HTR, agrégation, choix d'architecture justifiés, hyperparamètres.]*

## 5. Résultats
*[À rédiger — CER/WER sur le test scellé, IC bootstrap, courbes d'apprentissage, ablation, analyse des erreurs.]*

## 6. Discussion
*[À rédiger — biais de représentation du corpus, limitations, perspectives.]*

## 7. Conclusion et travaux futurs
*[À rédiger.]*

## Références
*[À compléter au format APA ou BibTeX.]*

## Annexes
*[À compléter — exemples de transcriptions, courbes de calibration, data contract complet.]*
