# Contributing

Thank you for helping maintain this data!

Before jumping in, please review these community resources:

*   **[Join Discord](https://chat.cardcarp.com):** Discuss structure and data accuracy.

## 🌭 Local Setup

Before submitting a pull-request, please test your changes locally to ensure they compile correctly.

1. Clone repository.
2. Install required Python dependencies: `pip install .`
3. Run the compile script to verify your YAML syntax and schema compliance: `cardcarp-compile`

## 🪲 Automated Validation

Every pull-request is automatically validated against the schema via GitHub Actions. If the pipeline fails, your pull-request cannot be merged. You can view the exact line causing the error by clicking "Details" on the failed GitHub-Action check.

<br>

## 🔑 How IDs Work

**Never write an ID into a YAML file.** Every ID is derived from the record's own properties at build time, and cross-references are written as human-readable names.

| Record | ID is derived from | Example |
| --- | --- | --- |
| Collection | `name` | `Rider-Waite-Smith` → `rider-waite-smith` |
| Set | `name` | `Major Arcana` → `major-arcana` |
| Card | `set` + `index` | `Wands` + `01` → `wands-01` |
| Oracle | `name` + `origin` | `The Fool` + `major-arcana-00` → `the-fool-major-arcana-00` |
| Deck | `group` + `name` | `Rider-Waite-Smith` + `Full Deck` → `rider-waite-smith-full-deck` |

So a card references its set as `set: Wands`, not `set: wands`. The build kebab-cases it for you.

**`oracle` is the one reference written in kebab-case**, because an oracle has no single human name that identifies it: `oracle: the-fool-major-arcana-00`, not `The Fool`. See below for why an oracle needs the extra anchor.

Filenames exist purely so editors can find things. The number prefix (`00-the-fool.yml`) is navigation, not identity. Renaming a file changes nothing about the data. If two records derive the same ID the build **fails and names both files**, rather than silently overwriting one.

### `origin` on oracles

Every edition of this deck has a Fool, and a Marseille deck has one too, so a name alone cannot identify an oracle. `origin` anchors it to the printing the card first appeared in (`major-arcana-00`). It is anchored to the *earliest* printing so that adding a later edition never changes an existing oracle's ID.

### Oracle IDs vs card IDs

The two ID shapes answer different questions, and both are needed:

| | identifies | shape | example |
| --- | --- | --- | --- |
| **Oracle** | a card's meaning, shared by every printing of it | `name` + `origin` | `the-fool-major-arcana-00` |
| **Card** | one specific printing | `set` + `index` | `major-arcana-00` |

An oracle answers *"what does this card mean?"* A card answers *"which printing is this?"*: its artist, its lettering, and the set it sits in.

**Card IDs are not merely internal plumbing.** Deck lists reference them, because a list has to name a specific printing rather than a meaning:

```yaml
highlight: major-arcana-00     # The Fool

list:
  main:
    major-arcana-00: 1         # The Fool
    wands-01: 1                # Ace of Wands
```

<br>

## 📇 Card `index`

`index` is the card's position in its set's own sequence, zero-padded to two digits so it sorts correctly and stays unique within the set:

- A trump's index is its numeral: the Fool is `00`, the Magician `01`, the World `21`
- A suit runs from the Ace at `01` through the Ten at `10`, then Page `11`, Knight `12`, Queen `13`, King `14`

Waite's deck numbers Strength `VIII` and Justice `XI`, swapping the older Marseille order. The index follows what is printed on the card, so Strength is `major-arcana-08` here. A deck that keeps the Marseille order would have Justice at `08` in *its* trumps set.

<br>

## 🎴 What Counts as a Set

**A set is a group of cards sharing one numbering sequence, not a retail product.** A tarot deck is sold as one pack but holds five sequences: the trumps run 0 to XXI, and each suit restarts at its Ace. So the Rider-Waite-Smith collection has five sets:

```
data/set/divinatory/01-rider-waite-smith/01-major-arcana.yml   22 cards
data/set/divinatory/01-rider-waite-smith/02-wands.yml          14 cards
data/set/divinatory/01-rider-waite-smith/03-cups.yml           14 cards
data/set/divinatory/01-rider-waite-smith/04-swords.yml         14 cards
data/set/divinatory/01-rider-waite-smith/05-pentacles.yml      14 cards
```

### Why the rule is load-bearing

Card IDs are `set` + `index`. The Ace of Wands and the Ace of Cups are both index `01`, so if the suits were one set the build would reject `rider-waite-smith-01` as a duplicate ID rather than silently drop one. Splitting by sequence is what makes every index unambiguous.

### Set names and a second deck

Set IDs are derived from the set name alone, so `wands` currently means *this* deck's Wands. The first collection to add a second deck should qualify its set names (`Marseille Wands`, or `Bâtons` for a French-titled pack) the way the Pokémon dataset names *Crown Zenith Galarian Gallery*. The Rider-Waite-Smith sets keep their plain names as the reference deck.

<br>

## 🧬 Curation Decisions

Where a judgement call was made, it is recorded here. **Please do not "correct" these back without raising it on Discord first.**

### Meanings are keywords, not Waite's text

Each oracle carries short upright and reversed keywords rather than passages from *The Pictorial Key to the Tarot*. Waite's book is public domain, but it is long, discursive and often contradicts itself; the keywords are the consensus reading a modern little white book would give. Keep them to three or four short phrases each.

### Attributions follow the Golden Dawn

`astrology` holds the planetary or zodiacal attribution the Hermetic Order of the Golden Dawn assigned each trump, which Waite's deck follows. The three elemental trumps (the Fool, the Hanged Man and Judgement) carry an `element` instead, and the World carries both Saturn and Earth. Each suit's `element` is Fire, Water, Air and Earth for Wands, Cups, Swords and Pentacles.

### The 1909 date

The Rider deck was launched in December 1909. The day is not recorded, so the set `date` is `1909-12-01`: the schema wants a full date, and the first of the month is the least misleading choice.

### `title` is what is lettered on the card

Trumps, aces and court cards carry a title, and pips carry only their numeral. `title` records exactly that, in the case used on the card (`PAGE of WANDS`, with its small *of*) and without the terminal full stop, so a later edition that re-letters a card (`JUDGMENT` for `JUDGEMENT`, or a non-English pack) can say so without touching the oracle.
