# Tarot Card Dataset 🔮

A flat-file dataset for tarot, starting with the 1909 Rider-Waite-Smith deck.

<br>

> [!IMPORTANT]
> Every card has its own file, split into the printing and the meaning it shares with every edition.<br>
> The deck, its artwork and the meanings are all in the public domain, so this dataset doubles as a demo of the CardCarp pipeline on a franchise nobody licenses.<br>
> Editors are actively working on this data and welcome community contributions.

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

This repo contains only data and schemas. The build comes from [yeahben/cardcarp-compile](https://github.com/yeahben/cardcarp-compile), a pipeline shared by the CardCarp datasets, and `pip install .` installs it.

<br>

## 🕸️ Structure

The build combines the repo's individual YAML files into flat JSON records.

| Folder | Holds |
| --- | --- |
| `data/card/` | **Physical printings.** Properties of one exact card: artist, the title as lettered, and its position in the set |
| `data/oracle/` | **Meaning shared by every edition.** Arcana, suit, rank or numeral, element, astrological attribution, and divinatory keywords upright and reversed |
| `data/set/` | **One numbering sequence.** The twenty-two trumps, and each of the four suits, since every suit restarts at its Ace ([why](CONTRIBUTING.md#-what-counts-as-a-set)) |
| `data/collection/` | **One deck design.** *Rider-Waite-Smith* groups its five sets; a later edition of the same design joins the same collection |
| `data/format/` | **Ways of reading.** The full pack, the trumps alone, or the suits alone, each with its card count |
| `data/deck/` | **Pre-built lists.** The pack as sold and its two halves. Each entry points to a *specific printing* ([why](CONTRIBUTING.md#oracle-ids-vs-card-ids)) |
| `schema/` | **Validation rules.** One schema per record type, plus `dataset.yml`, the tarot-specific vocabulary the shared build needs |
| `source/image/` | **Card scans.** Git-ignored. Mirrors `data/card/`, one hi-res PNG per card, plus the two card backs |
| `dist/` | **Build output.** Git-ignored. Run `cardcarp-compile` to produce it |

<br>

### Folder cascade

Cards, oracles and sets all nest the same way, so a file's path shows where the card sits in the deck:

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

Collection type is the top level: `divinatory` for decks drawn to be read, `playing` for game-pattern tarots such as the Marseille, `extra` for supplements. Number prefixes put each level in order. They only help editors find files, and **they are not part of any ID**.

Records refer to each other by readable names (`set: Wands`, `collection: Rider-Waite-Smith`), and every ID is generated during the build. Read [CONTRIBUTING](CONTRIBUTING.md#-how-ids-work) before adding records.

<br>

## 🖼️ Images

`source/image/` holds the scans the card art is published from, laid out exactly as `data/card/` is, so a card's `dir` in the manifest is also its image path:

```
source/image/divinatory/01-rider-waite-smith/01-major-arcana/00-the-fool.png
source/image/back.png                     the crackle back
source/image/back-roses-and-lilies.png    the alternative back Rider also printed
```

The scans are of the original 1909 Rider edition, from [steve-p.org](https://steve-p.org/cards/RWSa.html), at 1086 × 1810. Pamela Colman Smith's artwork is in the public domain. The folder is git-ignored because of its size.

Each PNG has an AVIF beside it at the same path and resolution, which is the file the [manifest contract](https://github.com/yeahben/cardcarp-compile/blob/main/docs/manifest.md) publishes. They are encoded with `avifenc -s 6 -q 60`, the same baseline the Magic dataset uses, and land around 250KB each:

```bash
find source/image -name '*.png' -exec sh -c 'avifenc -s 6 -q 60 "$1" "${1%.png}.avif"' _ {} \;
```

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

The [compile README](https://github.com/yeahben/cardcarp-compile#-commands) covers each command in detail, including how IDs and legality are calculated.

<br>

## 🐝 Community

Found a typo in a meaning, or want to add another public-domain deck? Contributions are welcome. Start with [CONTRIBUTING](CONTRIBUTING.md).

- **[Join Discord](https://chat.cardcarp.com):** discuss structure and data accuracy, or share something you've built with this data.<br>
- **[CardCarp](https://cardcarp.com):** the web app built on this dataset.<br>
- **[Support the project](https://patronage.cardcarp.com):** help keep the dataset maintained.

<br>

## 📜 License

The files in this repo are released under [MIT No Attribution](LICENSE.md).

<sub>The Rider-Waite-Smith tarot was published by William Rider & Son in 1909, designed by Arthur Edward Waite and drawn by Pamela Colman Smith. The artwork is in the public domain. CardCarp is an independent, community-driven project.</sub>
