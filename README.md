# Tarot Card Dataset 🔮

Card dataset for the 1909 Rider-Waite-Smith public-domain tarot collection.

<br>

## 🚀 Quick Start

You need Python 3.10 or newer.

```bash
git clone https://github.com/yeahben/cardcarp-tarot.git
cd tarot
pip install .
cardcarp-compile
```

The compiled dataset is written to `dist/`. `dist/database.json` has every card joined with its oracle, set, collection and format legality.

This repo contains only data and schemas. The build comes from [yeahben/cardcarp-compile](https://github.com/yeahben/cardcarp-compile), a pipeline to create similar tabletop-card-game datasets.

<br>

## 🕸️ Structure

Each `data` folder contains individual YAML files. Build combines them into flat JSON records.

| Folder | Holds |
| --- | --- |
| `data/card/` | **Physical printings.** Properties of one exact card: artist, the title as lettered, and its position in the set |
| `data/core/` | **Meaning shared by every edition.** Arcana, suit, rank or numeral, element, astrological attribution, and divinatory keywords upright and reversed |
| `data/set/` | **One numbering sequence.** The twenty-two trumps, and each of the four suits, since every suit restarts at its Ace ([why](CONTRIBUTING.md#-what-counts-as-a-set)) |
| `data/collection/` | **One deck design.** *Rider-Waite-Smith* groups its five sets; a later edition of the same design joins the same collection |
| `data/format/` | **Ways of reading.** The full pack, the trumps alone, or the suits alone, each with its card count |
| `data/deck/` | **Pre-built lists.** The pack as sold and its two halves. Each entry points to a *specific printing* ([why](CONTRIBUTING.md#oracle-ids-vs-card-ids)) |
| `schema/` | **Validation rules.** One schema per record type, plus `dataset.yml`, the tarot-specific vocabulary the shared build needs |
| `dist/` | **Build output.** Git-ignored. Run `cardcarp-compile` to produce it |

<br>

### Folder cascade

A file's path shows where the card sits in the dataset:

```
data/card/divinatory/01-rider-waite-smith/01-major-arcana/00-the-fool.yml
└ type
  └ collection
    └ set
      └ card

data/oracle/divinatory/01-rider-waite-smith/01-major-arcana/00-the-fool.yml
data/set/divinatory/01-rider-waite-smith/01-major-arcana.yml
data/collection/01-rider-waite-smith.yml
```

Collection type is the top level: `divinatory` for decks drawn to be read, `playing` for game-pattern tarots such as the Marseille, `extra` for supplements. 

Number prefixes put each level in order and are only used to help editors find files, **not part of any ID**.

Records refer to each other by readable names (`set: Wands`, `collection: Rider-Waite-Smith`).

`ID` is generated during build. 

Read [CONTRIBUTING](CONTRIBUTING.md#-how-ids-work) before adding records.

<br>

## 🐍 Commands

Run every command from the root of this repo.

| Command | What it does |
| --- | --- |
| `cardcarp-compile` | **The build.** Validates every file, checks every reference, and writes `dist/` |
| `cardcarp-split` | Regenerates `data/card`, `data/oracle`, `data/set` and `data/collection` from `dist/`, for bulk edits and formatting cleanup |
| `cardcarp-sets` | Writes `dist/sets.json`, the tree of collection types, collections and sets |
| `cardcarp-manifest` | Writes `dist/manifest.json`, all cards and decks keyed by id |
| `cardcarp-properties` | Writes `dist/properties.json`, the distinct values available for filtering |

The [compile README](https://github.com/yeahben/cardcarp-compile#-commands) covers each command in detail, including how `ID` and `legality` are generated.

<br>

## 🐝 Community

Found a typo in a meaning, or want to add another public-domain deck? Contributions are welcome. Start with [CONTRIBUTING](CONTRIBUTING.md).

- **[Join Discord](https://chat.yeahben.com):** discuss structure and data accuracy, or share something you've built with this data.<br>
- **[CardCarp](https://cardcarp.com/tarot):** a demo web app built on this dataset.<br>
- **[Support the project](https://patronage.cardcarp.com):** help keep the dataset maintained.

<br>

## 📜 License

The files in this repo are released under [MIT No Attribution](LICENSE.md).

<sub>The Rider-Waite-Smith tarot was published by William Rider & Son in 1909, designed by Arthur Edward Waite and drawn by Pamela Colman Smith. The artwork is in the public domain.</sub>

<sub>CardCarp is an independent, community-driven project.</sub>
