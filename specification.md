# PlayShakespeare.com XML Specification

**Version:** 0323
**Source authority:** `PlayShakespeare XML Specification.xlsx` (Elements and Special Glyphs sheets), cross-referenced with actual XML files in `/plays/`.

---

## Table of Contents

1. [Overview and File Types](#1-overview-and-file-types)
2. [Document Structure](#2-document-structure)
3. [Root Elements](#3-root-elements)
4. [Title and Metadata Block](#4-title-and-metadata-block)
5. [Editions Block](#5-editions-block)
6. [Performances Block](#6-performances-block)
7. [Statistics Block](#7-statistics-block)
8. [Locations Block](#8-locations-block)
9. [Songs and Soliloquies Indexes](#9-songs-and-soliloquies-indexes)
10. [Sources (listBibl)](#10-sources-listbibl)
11. [File Description (fileDesc)](#11-file-description-filedesc)
12. [Cast List (castList)](#12-cast-list-castlist)
13. [Play Structure: Acts, Scenes, Prologues, Epilogues](#13-play-structure-acts-scenes-prologues-epilogues)
14. [Dialogue: Speeches and Lines](#14-dialogue-speeches-and-lines)
15. [Stage Directions](#15-stage-directions)
16. [Poem Structure](#16-poem-structure)
17. [Sonnet Structure](#17-sonnet-structure)
18. [Inline Markup and Formatting](#18-inline-markup-and-formatting)
19. [Foreign Language Tags](#19-foreign-language-tags)
20. [First Folio / Quarto / Historical Editions Format](#20-first-folio--quarto--historical-editions-format)
21. [Character Relationships (listRelation)](#21-character-relationships-listrelation)
22. [Special Glyphs and Entities](#22-special-glyphs-and-entities)
23. [Known Enumeration Values Summary](#23-known-enumeration-values-summary)

---

## 1. Overview and File Types

The PlayShakespeare XML format encodes Shakespeare's plays, poems, sonnets, and apocryphal works for the PlayShakespeare.com website. There are two structurally distinct formats:

### 1.1 Standard PS Editions (plays, poems, sonnets)

Located in `/plays/<work_name>/<work_name>.xml`. These files use a rich encoding that includes cast statistics, scene descriptions, character data, modern translations, glossary links, and relationship graphs. The root element is `<play>` or `<poem>`.

### 1.2 Historical Editions (First Folio, Quartos, Manuscripts)

Located in `/plays/first_folio_editions/`. These files use a simplified structure that prioritizes faithful transcription of the original text over modern analysis. The root element is `<play>` with a different `variant` value.

**Key structural differences between standard and historical editions:**

| Feature | Standard (PS) | Historical (FF/Q) |
|---|---|---|
| Root element | `<play variant="ps">` | `<play variant="first-folio">` etc. |
| `<titleStmt>` | Full with translations | Abbreviated |
| Cast list | Rich (with stats, descriptions, scenes) | Minimal (role names and aliases only) |
| Lines (`<l>`) | Inside `<lg>` groups, with modern variant | Direct inside `<sp>`, no modern variant |
| Stage directions | Structured `<stage>/<dir>/<action>` | Plain `<stage>` text element |
| Speaker | `<speaker long="...">` | `<speaker short="...">` |
| Scene alignment | `<actref>` / `<sceneref>` for mismatches | `corresp` attribute for cross-referencing |
| End matter | None | Optional `<endpersonae>`, `<finis>` |
| `<dropcap>` | Not used | Used for first letter of speeches |

---

## 2. Document Structure

### 2.1 Standard Play File Top-Level Structure

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<?xml-stylesheet type="text/xsl" href="../xsl/plays.xsl"?>

<play variant="ps" unique="hamlet">
  <titleStmt>...</titleStmt>
  <authors>...</authors>
  <editions>...</editions>
  <performances>...</performances>
  <synopsis>...</synopsis>
  <statistics>...</statistics>
  <locations>...</locations>
  <songs>...</songs>             <!-- plays only, if applicable -->
  <soliloquies>...</soliloquies> <!-- plays only, if applicable -->
  <longestspeech>...</longestspeech>
  <listRelation>...</listRelation>
  <listBibl type="sources">...</listBibl>
  <fileDesc>...</fileDesc>
  <castList>...</castList>
  <act num="1">
    <prologue> or <scene>
    ...
  </act>
  ...
  <epilogue>...</epilogue>   <!-- if present -->
</play>
```

### 2.2 Standard Poem File Top-Level Structure

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<?xml-stylesheet type="text/xsl" href="../xsl/poems.xsl"?>

<poem variant="ps-poems" unique="venus-and-adonis" numberOfLines="1194">
  <titleStmt>...</titleStmt>
  <authors>...</authors>
  <editions>...</editions>
  <performances>...</performances>  <!-- may contain only <media> -->
  <listBibl type="sources">...</listBibl>
  <fileDesc>...</fileDesc>
  <poemdescription>...</poemdescription>
  <poemintro>...</poemintro>
  <poembody>
    <desc>...</desc>
    <lg type="stanza" num="1">...</lg>
    ...
  </poembody>
</poem>
```

### 2.3 Sonnet File Top-Level Structure

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<?xml-stylesheet type="text/xsl" href="../xsl/sonnets.xsl"?>

<poem variant="ps-poems" unique="sonnets">
  <titleStmt>...</titleStmt>
  <authors>...</authors>
  <editions>...</editions>
  <listBibl type="sources">...</listBibl>
  <fileDesc>...</fileDesc>
  <sonnets>
    <sonnet num="1" ...>
      <desc>...</desc>
      <themes>...</themes>
      <lg type="quatrain" num="1">...</lg>
      <lg type="quatrain" num="2">...</lg>
      <lg type="quatrain" num="3">...</lg>
      <lg type="couplet">...</lg>
    </sonnet>
    ...
  </sonnets>
</poem>
```

### 2.4 Historical Edition File Top-Level Structure

```xml
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="../xsl/folios-and-quartos.xsl"?>

<play variant="first-folio" unique="hamlet">
  <titleStmt>...</titleStmt>
  <authors>...</authors>
  <edition>First Folio of 1623</edition>
  <fileDesc>...</fileDesc>
  <castList>...</castList>
  <act num="1" corresp="act-1">
    <acttitle>...</acttitle>
    <scene actnum="1" num="1" corresp="scene-1-1">...</scene>
    <sceneref actnum="1" num="4" corresp="scene-1-4">...</sceneref>
  </act>
  <actref num="3" corresp="act-3">
    <sceneref>...</sceneref>
  </actref>
  <finis>...</finis>
  <endpersonae>...</endpersonae>  <!-- if present -->
</play>
```

---

## 3. Root Elements

### `<play>`

The root element for all play files (both standard PS and historical editions).

| Attribute | Required | Type | Description |
|---|---|---|---|
| `variant` | Required | Enum | Edition type. See [Known Variant Values](#231-variant-values). |
| `unique` | Optional | String | Unique slug identifier for the work (e.g., `"hamlet"`, `"midsummer-nights-dream"`). |
| `trans` | Optional | Enum (`yes`/`no`) | Whether translations are available. Found on apocrypha plays. |

**Example:**
```xml
<play variant="ps" unique="hamlet">
<play variant="ps-apocrypha" unique="arden-of-faversham" trans="no">
<play variant="first-folio" unique="hamlet">
<play variant="manuscript" unique="second-maidens-tragedy">
```

### `<poem>`

The root element for all poem and sonnet files.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `variant` | Required | Enum | Always `"ps-poems"` for standard editions. |
| `unique` | Optional | String | Unique slug identifier (e.g., `"sonnets"`, `"venus-and-adonis"`). |
| `numberOfLines` | Optional | Integer | Total line count for the poem. |

---

## 4. Title and Metadata Block

### `<titleStmt>`

Container for all title elements. Immediately follows the root element.

### `<title>`

A title entry. Multiple `<title>` elements appear within `<titleStmt>`.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `type` | Required | Enum | Type of title. Values: `common`, `main`, `short`, `abbr`, `translated`, `register`. |
| `xml:lang` | Conditional | BCP 47 language tag | Required when `type="translated"`. |

**Observed `type` values and their meanings:**

| Value | Meaning |
|---|---|
| `common` | The common, familiar title (e.g., "Hamlet") |
| `main` | The full formal title (e.g., "The Tragedy of Hamlet, Prince of Denmark") |
| `short` | Short display title |
| `abbr` | Standard abbreviation (e.g., `Ham`, `MND`, `Mac`) |
| `translated` | Title in another language; requires `xml:lang` attribute |
| `register` | The title as it appears in the Stationers' Register entry |

**Example:**
```xml
<titleStmt>
  <title type="common">Hamlet</title>
  <title type="main">The Tragedy of Hamlet, Prince of Denmark</title>
  <title type="short">Hamlet</title>
  <title type="abbr">Ham</title>
  <title type="translated" xml:lang="fr">Hamlet</title>
  <title type="translated" xml:lang="de">Hamlet: Prinz von Dänemark</title>
</titleStmt>
```

**Observed translation languages:** `ar`, `zh`, `cs`, `da`, `nl`, `fr`, `de`, `el`, `hi`, `hu`, `it`, `ja`, `lv`, `pt`, `sr`, `sk`, `sw`, `es`, `uk`.

In historical editions, `<titleStmt>` contains only `type="short"` and `type="main"`, and the `<title type="main">` may contain `<lb />` for line breaks in the original title page.

### `<subtitle>`

Used for secondary titles in poems and historical edition front matter.

**Example:** `<title type="sub">The Moor of Venice</title>`

### `<synopsis>`

A brief prose synopsis of the work. Plain text, no child elements. Present in all standard play editions.

### `<authors>`

Container for one or more `<author>` elements.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `status` | Optional | Enum | Used on apocrypha works: `"apocrypha"`. |

### `<author>`

The name of an author. Text content only. Multiple `<author>` elements appear when co-authorship is established or likely.

**Example:**
```xml
<authors status="apocrypha">
  <author>William Shakespeare</author>
  <author>Thomas Kyd</author>
  <author>Christopher Marlowe</author>
  <note when="2023-08-18">There is strong evidence...</note>
</authors>
```

### `<note>` (within `<authors>`)

A scholarly note about attribution. May include a `when` attribute with an ISO date for when the note was added.

---

## 5. Editions Block

### `<editions>`

Container for publication history data.

### `<written>`

Approximate year or date range when the work was written. Text content (e.g., `"1600-3"`, `"1606"`).

| Attribute | Required | Type | Description |
|---|---|---|---|
| `rend` | Optional | Enum | `"long"` to display the full date range. |

### `<register>`

The Stationers' Register entry date.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `when` | Optional | ISO date | Date in `YYYY-MM-DD` format. |
| `name` | Optional | String | Name of the registrant. |
| `type` | Optional | Enum | `"transfer"` if this was a rights transfer rather than initial registration. |

Following `<register>`, there may be a `<title type="register">` giving the title as it appeared in the register entry.

### `<edition>`

Describes a single edition of the work.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `format` | Conditional | Enum | Physical format: `Quarto`, `Folio`, `Octavo`. Required unless `variant` or `glossary` is set. |
| `n` | Optional | Integer | Sequential edition number within that format (e.g., `n="1"` for First Quarto). |
| `pssource` | Required | Enum (`yes`/`no`) | Whether this edition was used as a source for the PlayShakespeare.com text. |
| `order` | Optional | Integer | Order within the First Folio (applies to `format="Folio" n="1"`). |
| `authorized` | Optional | Enum | `"bad"` for unauthorized/pirated editions, `"illegal"` for unlicensed prints. |
| `variant` | Optional | Enum | `"modern"` for the modern PS edition entry, or other variant markers. |
| `glossary` | Optional | Enum (`yes`/`no`) | Marks the glossary edition entry. |

Child elements of `<edition>`:

| Element | Description |
|---|---|
| `<date>` | Year or date of publication. May have `when` attribute in `YYYY-MM-DD` format. Text content is the human-readable date. |
| `<title>` | Title as it appeared on the title page. |
| `<printer>` | Name of the printer. |
| `<publisher>` | Name of the publisher. |
| `<note>` | Short note about this edition. |
| `<leaves>` | Number of leaves in the edition. |

**Example:**
```xml
<editions>
  <written>1600-3</written>
  <register when="1602-07-26" name="James Robertes">July 26, 1602</register>
  <title type="register">the Revenge of Hamlett Prince Denmarke...</title>
  <edition format="Quarto" n="1" authorized="bad" pssource="yes">
    <date>1603</date>
    <title>The Tragicall Historie of Hamlet Prince of Denmarke.</title>
    <printer>Valentine Simmes</printer>
    <publisher>Nicholas Ling and John Trundell</publisher>
  </edition>
  <edition format="Folio" n="1" order="32" pssource="yes">
    <date when="1623-12-05">December 5, 1623</date>
    <title>The Tragedy of Hamlet Prince of Denmarke.</title>
    <printer>Isaac Jaggard and William Jaggard</printer>
    <publisher>John Heminges and Henry Condell</publisher>
  </edition>
  <edition variant="modern" pssource="no" />
  <edition glossary="yes" pssource="no" />
</editions>
```

---

## 6. Performances Block

### `<performances>`

Container for performance records and media links.

### `<performance>`

A single documented performance.

Child elements:

| Element | Description |
|---|---|
| `<date>` | Year of performance (text content). Optional `when` attribute for precise ISO date. |
| `<company>` | Name of the acting company. |
| `<location>` | Venue or location of the performance. |
| `<cast>` | Container for cast members in this performance. Contains `<actor>` elements. |

### `<actor>` (within `<performances>/<performance>/<cast>`)

A performer in a performance.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `role` | Optional | String | The character short name this actor played (e.g., `"HAM."`). |

Text content: The actor's real name.

### `<media>`

A link to a media resource (e.g., YouTube video).

| Attribute | Required | Type | Description |
|---|---|---|---|
| `url` | Required | URL | The URL of the media resource. |

Child element: `<desc>` — a short text description (e.g., "Watch on YouTube").

---

## 7. Statistics Block

### `<statistics>`

Contains pre-computed statistics for the play. Appears at the play level and also within individual `<castItem>` elements.

### `<num>`

A single statistical value. Self-closing element.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `commodity` | Required | Enum | What is being counted. See [Known Commodity Values](#237-commodity-values). |
| `type` | Required | Enum | Format: `"cardinal"` for counts, `"percentage"` for percentages. |
| `value` | Required | String/Number | The numeric value. |
| `rank` | Optional | Integer | The relative rank of this value among all plays. |

**Example:**
```xml
<statistics>
  <num commodity="minutesRunning" type="cardinal" value="238" rank="1" />
  <num commodity="numberOfWords" type="cardinal" value="29761" />
  <num commodity="numberOfScenes" type="cardinal" value="20" />
  <num commodity="numberOfCharacters" type="cardinal" value="33" />
  <num commodity="numberOfLines" type="cardinal" value="3436" />
  <num commodity="numberOfUniqueWords" type="cardinal" value="4173" rank="1" />
  <num commodity="verse" type="percentage" value="72" />
  <num commodity="prose" type="percentage" value="28" />
  <category type="genre">Tragedy</category>
  <category subtype="period">Elizabethan</category>
  <common>
    <words type="verb">go, know, love</words>
    <words type="noun">father, man, heaven</words>
    <words type="adjective">dead, sweet, fat</words>
  </common>
</statistics>
```

### `<category>` (within `<statistics>`)

A categorical classification for the work.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `type` | Conditional | Enum | `"genre"` — primary genre of the work. |
| `subtype` | Conditional | Enum | Secondary classification type: `"period"` or `"subgenre"`. |

**Known `type="genre"` values:** `Comedy`, `Tragedy`, `History`

**Known `subtype="period"` values:** `Elizabethan`, `Jacobean`, `Roman`

**Known `subtype="subgenre"` values:** `Late Romance`, `Problem`

### `<common>`

Contains lists of commonly used words in this play.

### `<words>`

A comma-separated list of common words of a given part of speech.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `type` | Required | Enum | Part of speech: `"verb"`, `"noun"`, `"adjective"`. |

---

## 8. Locations Block

### `<locations>`

A summary list of distinct scene locations used in the play.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `numberOfLocations` | Optional | Integer | Count of distinct locations. |

### `<placename>` (within `<locations>`)

A single location.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `numberOfWords` | Optional | Integer | Total word count spoken in scenes set at this location. |

Text content: The location description as it appears in scene headings.

---

## 9. Songs and Soliloquies Indexes

These elements appear at the play level (after `<statistics>`) and provide index data for songs and soliloquies within the play. They are not present in poem files.

### `<songs>`

Index of songs in the play.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `numberOfSongs` | Optional | Integer | Total number of songs. |
| `male` | Optional | Integer | Number of songs sung by male characters. |
| `female` | Optional | Integer | Number of songs sung by female characters. |

### `<song>` (within `<songs>`)

A single song entry.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `act` | Optional | Integer | Act number where the song occurs. |
| `scene` | Optional | Integer | Scene number where the song occurs. |

Child elements:
- `<name type="song">` — The title or first line of the song.
- `<castItem short="...">` — The character who sings it; `short` is the abbreviated character name. Text content is the full character name.

### `<soliloquies>`

Index of soliloquies in the play.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `numberOfSoliloquies` | Optional | Integer | Total number of soliloquies. |
| `male` | Optional | Integer | Count of male soliloquies. |
| `female` | Optional | Integer | Count of female soliloquies. |

### `<soliloquy>` (within `<soliloquies>`)

A single soliloquy entry.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `act` | Optional | Integer | Act number. |
| `scene` | Optional | Integer | Scene number. |
| `l` | Optional | Integer | Scene-level line number where the soliloquy begins. |
| `soliloquyNumberOfLines` | Optional | Integer | Number of lines in the soliloquy. |
| `form` | Optional | Enum | Verse form: `"verse"`, `"prose"`. |

Child elements:
- `<l>` — The first line of the soliloquy (text content only, no attributes).
- `<castItem short="...">` — The speaker; `short` is the abbreviated name. Text content is the full name.

### `<longestspeech>`

A reference to the longest speech in the play.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `act` | Optional | Integer | Act number. |
| `scene` | Optional | Integer | Scene number. |
| `longNumberOfWords` | Optional | Integer | Word count of the longest speech. |
| `form` | Optional | Enum | Verse form: `"verse"`, `"prose"`. |

Child elements:
- `<l>` — The first line of the speech.
- `<castItem short="...">` — The speaker.

---

## 10. Sources (listBibl)

### `<listBibl>`

Container for bibliographic sources used by Shakespeare in writing the work.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `type` | Required | Enum | Always `"sources"`. |

### `<bibl>`

A single bibliographic source.

Child elements:

| Element | Description |
|---|---|
| `<title>` | Title of the source work. |
| `<author>` | Container for the author's name. Contains `<name>` element. |
| `<name>` (within `<author>`) | The author's name (text content). |
| `<date>` | Date of the source. Optional `when` attribute. Text content is the human-readable date. |

---

## 11. File Description (fileDesc)

### `<fileDesc>`

Container for publication, licensing, and version information for the XML file itself.

### `<publicationStmt>`

Container for publisher and editorial information.

Child elements:

| Element | Description |
|---|---|
| `<publisher>` | The publisher name (e.g., "PlayShakespeare.com"). May have a `ref` attribute with URL. |
| `<name type="person" role="editor">` | Editor's name. |
| `<email>` | Contact email address. |
| `<date from="..." to="...">` | Copyright date range. Text content is human-readable. |
| `<availability>` | Contains `<license>` with text of the copyright/license statement. Has `status` attribute (e.g., `"restricted"`). |
| `<note type="editorial">` | Optional editorial note. Used in historical editions to describe the transcription methodology. |

### `<editionStmt>`

Contains file version information.

Child element: `<edition n="...">` — the file version number (e.g., `n="5.4"`).

### `<idno>`

An identifier for this work.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `type` | Required | Enum | `"pscatid"` — the PlayShakespeare.com category ID; `"psid"` — the PlayShakespeare.com item ID. |

Text content: The numeric ID.

---

## 12. Cast List (castList)

The cast list (`<castList>`) contains the dramatis personae. Its structure differs significantly between standard PS editions and historical editions.

### `<castList>`

Container for all cast entries.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `playtitle` | Optional | String | The play title, for display in the cast list heading. |

### 12.1 Standard PS Edition Cast Items

### `<castItem>`

Represents a single character in the cast. In standard editions, this contains rich metadata.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `gender` | Required | Enum | Character gender. Values: `male`, `female`, `horse`, `dog`, `bear`, `lion`, `snake`, `asps`, `crocodile`. |
| `id` | Required | UUID | A unique UUID for this character, consistent across editions of the same play. |
| `category` | Required | Enum | Role size category. Values: `major`, `supporting`, `minor`, `ensemble`, `extra`, `animal`. |
| `archetype` | Optional | Enum | Narrative archetype. Values: `hero`, `villain`, `clown`, `lover`. |
| `death` | Optional | Enum | `"yes"` if this character dies in the play. |

Child elements of `<castItem>` (standard edition):

| Element | Description |
|---|---|
| `<role>` | Character name and short identifier. |
| `<persaliases>` | Container for alternate names/identities. |
| `<roleDesc>` | Character description. May contain `<note>` for extended text visible only to registered users. |
| `<languages>` | Languages spoken by the character. Contains `<language>` elements. |
| `<statistics>` | Character-level statistics. |
| `<persscenes>` | List of scenes the character appears in. |
| `<performers>` | Known historical performers of this role. |

### `<role>`

The character's name. Used both in `<castItem>` and in `<persaliases>`.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `short` | Required | String | The abbreviated speaker tag used in `<speaker>` elements (e.g., `"HAM."`, `"1. CLO."`). |
| `numberOfLines` | Optional | Integer | Total lines for this character. |
| `numberOfVerseLines` | Optional | Integer | Verse lines. |
| `numberOfProseLines` | Optional | Integer | Prose lines. |
| `numberOfLyricsLines` | Optional | Integer | Lyric lines. |
| `nameChange` | Optional | Enum | Reason for alias. Values: `"disguise"`, `"ghost"`, `"accession"`. |

Text content: The full character name (e.g., "Hamlet", "Claudius, King of Denmark").

### `<persaliases>`

Container for alternate names or identities for a character (disguises, multiple characters speaking together, title changes). Contains one or more `<role>` elements.

### `<roleDesc>`

A prose description of the character. May contain:
- Plain text
- `<note>` — Extended description visible only to Pro/Registered users (inline within the `<roleDesc>` text).

### `<language>` (within `<languages>`)

A language spoken by the character.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `short` | Required | String | BCP 47 language code (e.g., `"en"`, `"la"`, `"fr"`). |

Text content: The full language name (e.g., "English", "Latin").

### Character-Level `<statistics>`

Within `<castItem>`, the `<statistics>` element contains:

```xml
<statistics>
  <lines numberOfLines="1099" numberOfVerseLines="885"
         numberOfProseLines="214" numberOfLyricsLines="0" />
  <words>
    <word totalCharacter="239" totalPlay="547">I</word>
    ...
  </words>
  <speeches>
    <longestspeech scene="2.2" line="361">466</longestspeech>
  </speeches>
</statistics>
```

**`<lines>`** (self-closing): Counts of lines by form.

| Attribute | Description |
|---|---|
| `numberOfLines` | Total lines. |
| `numberOfVerseLines` | Verse lines. |
| `numberOfProseLines` | Prose lines. |
| `numberOfLyricsLines` | Lyric/song lines. |

**`<word>`**: A frequently used word for this character.

| Attribute | Description |
|---|---|
| `totalCharacter` | Number of times this word appears in this character's speeches. |
| `totalPlay` | Number of times this word appears in the entire play. |

Text content: The word itself.

**`<longestspeech>`** (within `<speeches>`): Location of the character's longest speech.

| Attribute | Description |
|---|---|
| `scene` | Act.scene notation (e.g., `"2.2"`). |
| `line` | Scene-level line number where speech begins. |

Text content: Word count of the longest speech.

### `<persscenes>`

Container listing all scenes in which the character appears.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `numberOfScenes` | Optional | Integer | Total count of scenes. |

### `<persscene>`

A single scene appearance entry.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `numberOfLines` | Optional | Integer | Lines spoken in this scene. |
| `numberOfVerseLines` | Optional | Integer | Verse lines in this scene. |
| `numberOfProseLines` | Optional | Integer | Prose lines in this scene. |
| `numberOfLyricsLines` | Optional | Integer | Lyric lines in this scene. |
| `numberOfAsides` | Optional | Integer | Number of asides in this scene. |
| `numberOfSoliloquies` | Optional | Integer | Number of soliloquies in this scene. |
| `numberOfWords` | Optional | Integer | Word count in this scene. |
| `nameChange` | Optional | Enum | `"disguise"`, `"ghost"`, or `"accession"` if character appears under a different name in this scene. |
| `death` | Optional | String | Description of how the character dies in this scene (e.g., `"stabbed and poisoned"`). |

Text content: Act.scene notation (e.g., `"1.2"`, `"3.4"`). May also be a textual title like a prologue scene name.

### `<performers>`

Contains historical performance records for this character role.

### `<performer>`

A single documented performer.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `evidence` | Optional | Enum | `"documented"` or `"likely"`. |

Child elements: `<actor>` (performer name), `<company>` (company name), `<theatre>` (venue name).

### 12.2 Historical Edition Cast Items

In historical editions (`first-folio`, `first-quarto`, etc.), `<castItem>` has only:
- `gender` attribute
- `category` attribute (always `"minor"` in observed data)
- `id` attribute (UUID, matching the standard edition)
- `<role short="...">` — character name and abbreviated tag
- `<persaliases>` — containing `<role short="...">` elements with no text content, listing all spelling variants for the character's abbreviated name in that edition

**Example (historical edition):**
```xml
<castItem gender="male" category="minor" id="91d29aa8-e85f-4d04-9a40-bb85c88d1e35">
  <role short="HAM.">Hamlet</role>
  <persaliases>
    <role short="Ham"/>
    <role short="Ha."/>
    <role short="Ham."/>
    <role short="Hamlet."/>
  </persaliases>
</castItem>
```

---

## 13. Play Structure: Acts, Scenes, Prologues, Epilogues

### `<act>`

One act of the play.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `num` | Required | Integer | Act number (1–5 typically). |
| `numberOfWords` | Optional | Integer | Total word count for this act. |
| `corresp` | Optional | String | Cross-reference ID for historical editions (e.g., `"act-1"`). |

### `<acttitle>`

The display title for the act (e.g., "Act 1", or the original Latin title in historical editions).

| Attribute | Required | Type | Description |
|---|---|---|---|
| `id` | Optional | String | Used in folios/quartos for menu navigation (e.g., `"scene1_1"`). |
| `rend` | Optional | Enum | `"borders"` to render with decorative border lines; `"word"` to show only the text without borders. |

### `<scene>`

One scene within an act.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `actnum` | Optional | Integer | The act number this scene belongs to (same as the parent `<act>` `num` value). |
| `num` | Optional | Integer | Scene number within the act. |
| `minutesRunning` | Optional | Integer | Estimated running time in minutes. |
| `numberOfProseWords` | Optional | Integer | Prose word count. |
| `numberOfVerseWords` | Optional | Integer | Verse word count. |
| `numberOfLines` | Optional | Integer | Total line count. |
| `corresp` | Optional | String | Cross-reference ID for historical editions (e.g., `"scene-1-1"`). |

Child elements within `<scene>` (standard editions):

| Element | Description |
|---|---|
| `<scenetitle>` | Display title of the scene. |
| `<scenelocation>` | Location description for the scene. |
| `<scenetime>` | Time setting (e.g., "At midnight."). |
| `<scenepersonae>` | List of characters in this scene. |
| `<scenelanguage>` | Languages used in this scene (with nested `<language>` and `<statistics>`). |
| `<desc>` | Prose description/synopsis of the scene's action. |
| `<stage>` | Stage direction. |
| `<sp>` | A character speech. |

### `<scenetitle>`

| Attribute | Required | Type | Description |
|---|---|---|---|
| `type` | Optional | Enum | `"act-title"` for folio/quarto titles that double as act and scene markers; `"prologue"` for prologue scene titles; `"epilogue"` for epilogue scene titles. |
| `idName` | Optional | String | Identifier for prologue scenes: `"Prologue Scene 1"`, `"Prologue Scene 2"`, etc. |

### `<scenepersonae>`

Container for character entries within a scene. Must contain at least one `<scenepersona>`.

### `<scenepersona>`

One character in a scene.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `short` | Optional | String | The abbreviated character tag (e.g., `"KING."`, `"1. GENT."`). |

Text content: Full character name.

### `<scenelanguage>`

Languages present in the scene. Contains:
- `<language short="...">` elements (same structure as in `<castItem>`)
- `<statistics>` containing a `<words>` block of most common words in this scene

Word statistics within `<scenelanguage>/<statistics>/<words>`:
```xml
<word totalScene="35" totalPlay="505">it</word>
```

| Attribute | Description |
|---|---|
| `totalScene` | Occurrences in this scene. |
| `totalPlay` | Occurrences in the entire play. |

### `<prologue>`

A prologue speech block, appearing within `<act>` at the same level as `<scene>`. Attributes are identical to `<scene>`:

| Attribute | Description |
|---|---|
| `actnum` | Act number this prologue introduces. |
| `num` | Always `0`. |
| `minutesRunning` | Estimated running time. |
| `numberOfProseWords` | Prose word count. |
| `numberOfVerseWords` | Verse word count. |
| `numberOfLines` | Total line count. |

Contains same child elements as `<scene>`: `<scenetitle type="prologue">`, `<scenepersonae>`, `<scenelanguage>`, `<desc>`, `<stage>`, `<sp>`.

### `<epilogue>`

An epilogue speech block, appearing after all `<act>` elements at the top level of `<play>`. Attributes same as `<prologue>`.

Contains `<scenetitle type="epilogue">`, `<scenepersonae>`, `<scenelanguage>`, `<desc>`, `<stage>`, `<sp>`.

### `<finis>`

Container for the "FINIS" closing statement at the end of a work. Used in historical editions and occasionally in standard editions.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `rend` | Optional | Enum | `"word"` to display only the word without border lines. |

Contains `<finistitle>` — the finis text (e.g., "FINIS."). May contain `<emph>` for italic formatting.

### `<actfinis>`

Used within an `<act>` to indicate a closing statement at the end of an individual act (in historical editions).

---

## 14. Dialogue: Speeches and Lines

### `<sp>` (Speech)

The wrapper for a single character's speech. Contains one `<speaker>` and one or more `<lg>` groups.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `id` | Optional | UUID | Unique identifier for this speech, consistent across editions. |
| `type` | Optional | Enum | `"soliloquy"` or `"aside"`. |
| `alias` | Optional | String | The alternate abbreviated name used by a character speaking under a different identity or when two characters speak simultaneously. |
| `rend` | Optional | Enum | `"parens"` to wrap the entire speech in parentheses. |

**Note:** In historical editions, `<sp>` contains `<l>` elements directly (no `<lg>` wrapper).

### `<speaker>`

The abbreviated speaker name.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `long` | Optional | String | The full character name (e.g., `"Barnardo"`). |
| `alias` | Optional | String | Former name of the character (for name changes), or abbreviation when multiple characters speak simultaneously. |
| `short` | Optional | String | Used in historical editions in place of `long`. The abbreviated form matching the `<role short>` value. |
| `rend` | Optional | Enum | `"hidden"` to hide the name (folios/quartos where it doesn't appear in the original); `"smallcaps"` to render in small caps; `"center"` to center-align (historical editions). |

Text content: The abbreviated speaker name as it appears in the text (e.g., `"BAR."`, `"KING."`, `"1. CLO."`).

### `<lg>` (Line Group)

A grouping of lines. In standard PS editions, each `<l>` is wrapped in its own `<lg>` to allow pairing with a `<l variant="modern">` translation. In sonnets and poems, `<lg>` represents structural units (quatrain, couplet, stanza).

| Attribute | Required | Type | Description |
|---|---|---|---|
| `type` | Optional | Enum | In poems/sonnets: `"quatrain"`, `"couplet"`, `"stanza"`. |
| `num` | Optional | Integer | Sequential number of the unit (for quatrains in sonnets). |
| `id` | Optional | UUID | Unique identifier (used in poem stanzas). |

**Standard play structure:**
```xml
<sp id="...">
  <speaker long="Barnardo">BAR.</speaker>
  <lg>
    <l gn="1" n="1" form="verse">Who's there?</l>
    <l variant="modern">Who goes there?</l>
  </lg>
</sp>
```

**Song/lyric structure:**
```xml
<sp id="...">
  <speaker long="Ariel">ARI.</speaker>
  <lg>
    <l gn="478" n="376" form="lyric" rend="indent(1)">Come unto these yellow sands,</l>
    <l variant="modern">Come to these yellow shores,</l>
  </lg>
</sp>
```

### `<l>` (Line)

A single line of text (verse, prose, lyric, or rhyme).

| Attribute | Required | Type | Description |
|---|---|---|---|
| `gn` | Optional | Integer or Decimal | Global line number for this line within the entire play. Unique per play. Stage direction global numbers use decimal notation (e.g., `"1322.01"`). |
| `n` | Optional | Integer or Decimal | Scene-level line number. Shared lines use the same integer with different iamb/rend values. |
| `form` | Optional | Enum | Line form. Values: `"verse"`, `"prose"`, `"rhyme"`, `"lyric"`. |
| `rend` | Optional | String | Visual rendering hint. `"indent(N)"` where N is 0–9 indicating syllable offset for shared lines; `"indent"` for a single paragraph indent. |
| `iamb` | Optional | Integer | Syllable count value for verse lines, indicating how many syllables precede this line in a shared line (used with `rend="indent(N)"`). |
| `order` | Optional | Enum | Position for dropcap formatting: `"first"`, `"second"`, `"third"`, `"fourth"`. |
| `variant` | Optional | Enum | `"modern"` for modern English translation lines. |
| `lb` | Optional | Integer | Line break count (used for long prose passages that span multiple print lines). |
| `rhyme` | Optional | String | Rhyme scheme letter (e.g., `"a"`, `"b"`, `"g"`). Used in sonnets and poems. |
| `lyric` | Optional | String | Identifies when a character is singing or incanting (see also `form="lyric"`). |
| `part` | Optional | Enum | `"m"` for middle part of a shared line (TEI-compatible). |

**Modern translation lines** appear immediately after the original line within the same `<lg>`:
```xml
<lg>
  <l gn="1" n="1" form="verse">Who's there?</l>
  <l variant="modern">Who goes there?</l>
</lg>
```

### `<recite>`

Identifies a line or passage where a character is reading aloud or reciting text. Wraps the read text within a `<l>` element.

**Example:**
```xml
<l gn="324" n="1" form="prose" lb="2">
  <recite>"They met me in the day of success..."</recite>
</l>
```

### `<song>` (within a speech)

The wrapper for song lines within a speech. Same structure as `<sp>` but indicates singing. Contains `<speaker>` and `<lg>/<l>` elements with `form="lyric"`.

---

## 15. Stage Directions

Stage directions encode character action, movement, and production notes.

### 15.1 Standard Edition Stage Directions

### `<stage>`

Container for a stage direction. Must contain a `<dir>` element and typically an `<action>` element.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `sgn` | Optional | Decimal | Global stage direction number (decimal notation: previous line's `gn` + two-digit sequential suffix, e.g., `"1322.01"`). |
| `sn` | Optional | Decimal | Scene-level stage direction number. |
| `rend` | Optional | Enum | Visual alignment: `"center"`, `"alignright"`, `"coronation"`. |

Stage directions can appear:
- Between speeches (at scene level)
- Within a `<sp>` between `<speaker>` and `<lg>` elements (e.g., for read actions)

### `<dir>`

The actual stage direction text. May contain inline markup such as `<term>`, `<foreign>`, and `<name>`.

### `<action>`

Structured encoding of a stage direction action.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `type` | Required | Enum | Type of action. See [Known Action Types](#235-action-types). |
| `offstage` | Optional | Enum | `"true"` if the action occurs offstage. |

Contains `<actor>` and/or `<recipient>` elements.

### `<actor>` (within `<stage>/<action>`)

The character performing the action.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `short` | Optional | String | Abbreviated character name. |
| `long` | Optional | String | Full character name. |

Text content: The abbreviated character name.

### `<recipient>` (within `<stage>/<action>`)

The character receiving or being acted upon.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `short` | Optional | String | Abbreviated character name. |
| `long` | Optional | String | Full character name. |

Text content: The abbreviated character name.

### `<death>` (within `<action>`)

When `<action type="death">` is used, a `<death>` element can specify the manner of death.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `cause` | Optional | String | Cause or manner contributing to death (e.g., `"treason"`, `"suicide"`). |

Text content: Method of death (e.g., `"stabbing"`, `"poisoning"`, `"drowning"`).

**Known death methods (from spec):** poisoning, stabbing, suicide, grief, hanging, beheading, illness, eaten by a bear, in battle, old age, treason, drowning, suffocation, burned alive, burned at the stake, struck by lightning, jump from castle, insanity, murdered, murdered in sleep, axe, lynching, buried up to his neck, limb amputation, cannonball in face, concussion, arrow in the neck, thrown from a horse.

**Stage direction example:**
```xml
<stage sgn="0.01" sn="0.01">
  <dir>Enter Barnardo and Francisco, two sentinels, meeting.</dir>
  <action type="enter">
    <actor>BAR.</actor>
    <actor>FRAN.</actor>
  </action>
</stage>
```

**Death example:**
```xml
<stage sgn="3436.01" sn="320.01">
  <dir>King dies.</dir>
  <action type="death">
    <actor>KING.</actor>
    <death cause="poisoning">stabbing</death>
  </action>
</stage>
```

### 15.2 Historical Edition Stage Directions

In historical (folio/quarto) editions, `<stage>` is a simple text element:

```xml
<stage sgn="0.01" sn="0.01" rend="center">Enter Barnardo and Francisco two Centinels.</stage>
<stage sgn="19.01" sn="19.01" rend="right">Exit Fran.</stage>
```

No `<dir>` or `<action>` child elements are used in historical editions.

---

## 16. Poem Structure

### `<poemdescription>`

An introductory description of the poem (not to be confused with per-stanza `<desc>` in `<poembody>`). Contains `<title>` and `<l>` elements.

### `<poemintro>`

The front matter of a poem (e.g., dedication, epigraph). Contains:
- `<intro>` — an introductory phrase (e.g., Latin epigraph). Contains `<l>` elements.
- `<dedication>` — the dedication text. Contains `<l>` elements and `<dedauthor>`.

### `<intro>`

A subsection of `<poemintro>` for introductory text such as an epigraph. Contains `<l>` elements with `gn` attributes.

### `<dedication>`

A subsection of `<poemintro>` for the dedication. Contains `<l>` elements with `gn` attributes and a `<dedauthor>` element.

### `<dedauthor>`

The author signature at the end of a dedication.

### `<poembody>`

The main body of a poem, containing the poem's stanzas.

Child elements:
- `<desc>` — a prose description with optional `corresp` attribute (e.g., `corresp="stanzas-1-10"`) and optional `<lineref>` inline.
- `<lg type="stanza" num="N" id="...">` — a stanza.

### `<desc>` (within `<poembody>` or `<scene>`)

A prose description or synopsis. In `<poembody>`, may have:

| Attribute | Required | Type | Description |
|---|---|---|---|
| `corresp` | Optional | String | Identifies which stanzas this description covers (e.g., `"stanzas-1-10"`). |

May contain `<lineref>` inline.

### `<lineref>`

Used within `<desc>` to indicate line reference labels (e.g., "Stanzas 1-10 (Lines 1-60):").

### Poem `<lg>` Structure

```xml
<lg type="stanza" num="1" id="63f08700-...">
  <lg>
    <l gn="7" n="1" rhyme="a" form="verse">Even as the sun...</l>
    <l variant="modern">Just as the sun...</l>
  </lg>
  <lg>
    <l gn="8" n="2" rhyme="b" form="verse">Had ta'en his last leave...</l>
    <l variant="modern">Bid its final farewell...</l>
  </lg>
  <lg type="couplet">
    <lg>
      <l gn="11" n="5" rhyme="c" rend="indent(2)" form="verse">Sick-thoughted Venus...</l>
      <l variant="modern">Love-sick Venus...</l>
    </lg>
  </lg>
</lg>
```

---

## 17. Sonnet Structure

### `<sonnets>`

Container for all sonnets in the sonnets file.

### `<sonnet>`

A single sonnet.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `num` | Required | Integer | Sequential number (1–154). |
| `chronnum` | Optional | Integer | Chronological ordering number. |
| `id` | Optional | UUID | Unique identifier for this sonnet. |

Child elements:
- `<desc>` — Prose description of the sonnet's theme/argument.
- `<themes>` — Container for `<theme>` elements.
- `<lg type="quatrain" num="1">` — First quatrain.
- `<lg type="quatrain" num="2">` — Second quatrain.
- `<lg type="quatrain" num="3">` — Third quatrain.
- `<lg type="couplet">` — Closing couplet.

### `<themes>` / `<theme>`

`<themes>` contains one or more `<theme>` elements (text content only, e.g., "death", "fair youth", "procreation").

### Sonnet `<lg>` Internal Structure

Each quatrain/couplet `<lg>` contains nested `<lg>` elements, each wrapping one original line and its modern translation:

```xml
<lg type="quatrain" num="1">
  <lg>
    <l gn="1" n="1" rhyme="a" form="verse" iamb="10">From fairest creatures...</l>
    <l variant="modern">We want beautiful people to reproduce,</l>
  </lg>
  <lg>
    <l gn="2" n="2" rhyme="b" form="verse" iamb="10">That thereby beauty's rose...</l>
    <l variant="modern">So beauty will never die,</l>
  </lg>
  ...
</lg>
<lg type="couplet">
  <lg>
    <l gn="13" n="13" rend="indent(2)" rhyme="g" form="verse" iamb="10">Pity the world...</l>
    <l variant="modern">Take pity on the world...</l>
  </lg>
  ...
</lg>
```

Note: Couplet lines conventionally use `rend="indent(2)"`.

---

## 18. Inline Markup and Formatting

These elements appear within `<l>`, `<dir>`, `<roleDesc>`, `<desc>`, and similar text-bearing elements.

### `<term>`

A word linked to the PlayShakespeare.com glossary ("Shakespeare's Words").

| Attribute | Required | Type | Description |
|---|---|---|---|
| `id` | Required | Integer | Primary glossary entry ID. |
| `id2` | Optional | Integer | Secondary glossary entry ID. |
| `id3` | Optional | Integer | Tertiary glossary entry ID. |
| `word` | Required | String | Display version of the term. |
| `word2` | Optional | String | Display version of secondary term. |
| `word3` | Optional | String | Display version of tertiary term. |

Text content: The word as it appears in the text.

**Example:** `<term id="47947" word="unfold">unfold</term>`

### `<name>`

Marks a named entity in the text.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `type` | Required | Enum | Entity type. Values: `"title"`, `"person"`, `"deity"`, `"religious"`, `"historical"`, `"place"`, `"song"`. |
| `subtype` | Optional | String | Further classification. For `"deity"`: `"greek-roman"`, `"Roman"`. For `"religious"`: `"biblical"`. For `"historical"`: `"folklore"`. |
| `rend` | Optional | Enum | `"noitalic"` to suppress italic formatting. |
| `role` | Optional | String | In `<publicationStmt>`, used to mark the editor's role (e.g., `role="editor"`). |

**Examples:**
```xml
<name type="title">The Passionate Pilgrim</name>
<name type="person">Barnardo</name>
<name type="deity" subtype="greek-roman">Zeus</name>
<name type="deity" subtype="Roman">Goddess of Love</name>
<name type="religious" subtype="biblical">Moses</name>
<name type="historical">Hostilius</name>
<name type="historical" subtype="folklore">Merlin</name>
<name type="place">Ireland</name>
<name type="song">Come Away</name>
```

### `<date>` (inline)

A date reference within spoken text.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `type` | Required | Enum | `"holiday"` for holiday references. |
| `when` | Optional | ISO date | Date in `YYYY-MM-DD` or `--MM-DD` (omitting year) format. |
| `subtype` | Optional | Enum | `"Christian"` or `"Roman"`. |

**Example:**
```xml
<date type="holiday" subtype="Christian" when="--09-29">Michaelmas</date>
```

### `<placeName>`

A place name reference within spoken text.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `type` | Required | String | Type of place: `"city"`, `"country"`, `"monument"`, `"residence"`, etc. |
| `xml:lang` | Optional | BCP 47 | Language of the place name. |

**Example:** `<placeName type="city" xml:lang="it">Cimitero Acattolico</placeName>`

### `<foreign>`

Foreign language text within the dialogue or stage directions.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `xml:lang` | Required | BCP 47 | Language code. |

**Supported language codes:** `la` (Latin), `fr` (French), `it` (Italian), `nl` (Dutch), `es` (Spanish), `el` (Greek), `cy` (Welsh), `gd` (Scottish Gaelic), `de` (German), `ga` (Irish/Irish Gaelic), `art` (Gibberish/custom).

### `<emph>`

Apply or remove italic formatting.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `rend` | Optional | Enum | `"noitalic"` to render as normal (removes default italic). `"ls8"` and similar letter-spacing values may appear in historical editions. |

Default behavior (no `rend`): Renders in italics.

### `<bold>`

Apply bold formatting.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `rend` | Optional | Enum | `"nobold"` to remove bold. |

### `<lb />`

A manual line break, used where the source material indicates a line break. Self-closing. No attributes.

### `<nolb>`

Prevent a line or word break for a specific word or phrase. No attributes.

### `<dropcap>`

An initial drop capital letter at the beginning of a speech (used primarily in historical editions).

| Attribute | Required | Type | Description |
|---|---|---|---|
| `type` | Required | Enum | Style: `"floral"` or `"regular"`. |
| `size` | Optional | Enum | Size class. For `"regular"`: `"small"` (2 lines), `"medium"` (3 lines). For `"floral"`: `"xsmall"` (3 lines), `"small"` (4 lines), `"medium"` (5 lines), `"large"` (7 lines). |
| `render` | Optional | Enum | Extra spacing below: `"sp1"` (1 line), `"sp2"` (2 lines), `"sp3"` (3 lines), `"reflowable"`. |

### `<char>`

An alternative character form using a special ligature or glyph.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `value` | Required | String | The ASCII equivalent letter (e.g., `"E"`). |

Text content: The special glyph entity (e.g., `&#57358;`).

### `<swash>`

A swash glyph alternative for letters (used in historical editions for decorative letterforms).

| Attribute | Required | Type | Description |
|---|---|---|---|
| `rend` | Optional | String | HTML entity for the alternate glyph to show instead of the regular letter (e.g., `rend="&#57359;"`). |

Text content: The regular letter character.

### `<choice>` / `<orig>` / `<reg>`

Used to encode original vs. regularized forms of special characters (primarily in historical editions).

```xml
<choice>
  <orig>&#57358;</orig>
  <reg type="unicode">E</reg>
  <reg type="ascii">E</reg>
</choice>
```

`<reg>` has a `type` attribute: `"unicode"` or `"ascii"`.

### `<missing>`

Identifies content that is presumed missing from the original text. No attributes documented.

---

## 19. Foreign Language Tags

The `<foreign>` element uses BCP 47 language codes:

| Code | Language |
|---|---|
| `la` | Latin |
| `fr` | French |
| `it` | Italian |
| `nl` | Dutch |
| `es` | Spanish |
| `el` | Greek |
| `cy` | Welsh |
| `gd` | Scottish Gaelic |
| `de` | German |
| `ga` | Irish / Irish Gaelic |
| `art` | Gibberish (custom/artificial) |

---

## 20. First Folio / Quarto / Historical Editions Format

Historical edition files use a simplified structure. Key differences are noted throughout this document; this section consolidates the elements unique to historical editions.

### `<edition>` (top-level, singular)

In historical edition files (not inside `<editions>`), a top-level `<edition>` element gives the edition description as plain text:

```xml
<edition>First Folio of 1623</edition>
<edition>Manuscript c. 1611</edition>
```

### `<actref>`

Used in historical editions where the modern PS edition has act divisions but the historical source does not. Marks the span corresponding to a PS act without adding visible act structure.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `num` | Required | Integer | The corresponding PS act number. |
| `corresp` | Optional | String | Cross-reference string (e.g., `"act-3"`). |

Contains `<sceneref>` elements (not `<scene>` elements).

### `<sceneref>`

Used in historical editions where the modern PS edition has scene divisions but the historical source does not, or where scene boundaries do not align. Prevents line numbering from resetting.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `actnum` | Optional | Integer | The corresponding PS act number. |
| `num` | Optional | Integer | The corresponding PS scene number. |
| `corresp` | Optional | String | Cross-reference string (e.g., `"scene-1-4"`). |

Contains direct dialogue (`<stage>`, `<sp>`) just like a `<scene>` element.

### `<milestone>`

A marker for scene transitions in historical editions when scenes in editions overlap or don't align cleanly with PS scene breaks.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `type` | Required | Enum | `"start"` or `"end"`. |
| `actnum` | Required | Integer | Act number. |
| `num` | Required | Integer | Scene number. |

Self-closing element.

### `<endpersonae>`

The dramatis personae as it appears at the end of a folio/quarto edition (rather than at the beginning). Contains:
- `<persloc>` — Location description (e.g., "The Scene, an un-inhabited Island").
- `<persnames>` — Title for the names section (e.g., "Names of the Actors.").
- `<castItem>` — Simple text entries for each character (no attributes in this context).
- `<castItemgroup>` — A grouped subset of characters.

### `<castItemgroup>`

A group of characters in the `<endpersonae>` with a shared title.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `title` | Required | String | Group title (e.g., "Spirits"). |
| `items` | Optional | Integer | Number of items in the group. |

**Example:**
```xml
<endpersonae>
  <persloc>The Scene, an vn-inhabited Island</persloc>
  <persnames>Names of the Actors.</persnames>
  <castItem>Alonso, K. of Naples</castItem>
  <castItem>Prospero, the right Duke of Millaine.</castItem>
  <castItemgroup title="Spirits" items="5">
    <castItem>Iris</castItem>
    <castItem>Ceres</castItem>
    <castItem>Juno</castItem>
  </castItemgroup>
</endpersonae>
```

### `<personagroup>`

A named group of characters within a dramatis personae list (also used in some PS editions for grouped cast entries).

| Attribute | Required | Type | Description |
|---|---|---|---|
| `title` | Required | String | Title describing this group. |
| `items` | Optional | Integer | Number of items in the group. |

### `<manuscript>`

Used in manuscript editions. Container for manuscript description and scan records.

Child elements:
- `<description>` — Text description of the manuscript.
- `<page>` — A page entry containing scan references.

### `<page>` (within `<manuscript>`)

| Attribute | Required | Type | Description |
|---|---|---|---|
| `id` | Optional | UUID | Unique identifier for this page entry. |

Child elements: `<title>`, `<leaf>`, `<text>`, `<desc>`, `<scan>`.

### `<scan>`

A reference to a scan/image of a manuscript or folio page.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `image` | Required | UUID | Image identifier/UUID. |
| `folio` | Optional | Integer | Folio number. |
| `position` | Optional | Enum | `"recto"` or `"verso"`. |
| `desc` | Optional | String | Description of what the scan shows. |

Self-closing element.

### Historical Edition Speech Structure

In historical editions, speeches use direct `<l>` elements without `<lg>` wrappers:

```xml
<sp id="...">
  <speaker short="BAR.">Barnardo.</speaker>
  <l gn="1" n="1" order="first">
    <dropcap type="floral" size="small" render="sp1">W</dropcap>ho's there?
  </l>
</sp>

<sp id="...">
  <speaker short="FRAN.">Fran.</speaker>
  <l gn="2" n="2">Nay answer me: Stand &amp; vnfold your selfe.</l>
  <l gn="3" n="3">Another line in the same speech.</l>
</sp>
```

Historical editions do not include `<l variant="modern">` translations.

---

## 21. Character Relationships (listRelation)

### `<listRelation>`

Container for character relationship data. Appears at the play level after `<longestspeech>`.

### `<relation>`

A directed relationship from one character to another.

| Attribute | Required | Type | Description |
|---|---|---|---|
| `type` | Required | String | Relationship type. See [Known Relation Types](#238-relation-types). |
| `target` | Required | String | The abbreviated name (`short` value) of the character being related to. |
| `note` | Optional | String | A short narrative description of the relationship. |

Text content: The abbreviated name (`short` value) of the character holding the relationship.

**Example:**
```xml
<listRelation>
  <relation type="love_interest" target="HAM."
    note="Ophelia is in love with Hamlet...">OPH.</relation>
  <relation type="friend" target="HAM."
    note="Horatio is Hamlet's closest friend.">HOR.</relation>
  <relation type="parent" target="HAM."
    note="Gertrude loves her son Hamlet.">QUEEN.</relation>
</listRelation>
```

---

## 22. Special Glyphs and Entities

The Special Glyphs sheet documents the HTML entities and Unicode characters used in the XML files. These entities appear as numeric HTML entity references (e.g., `&#8217;` for a right curly apostrophe).

### Standard Punctuation Entities

| Entity | Glyph | Description |
|---|---|---|
| `&#160;` | (NBSP) | Non-breaking space |
| `&#8211;` | – | En dash / Hyphen |
| `&#8212;` | — | Em dash |
| `&#8213;` | ― | Horizontal bar |
| `&#8216;` | ' | Left curly apostrophe (single) |
| `&#8217;` | ' | Right curly apostrophe (single) |
| `&#8220;` | " | Left curly quotation mark |
| `&#8221;` | " | Right curly quotation mark |
| `&#8222;` | „ | Right low quotation mark |
| `&#8288;` | (WJ) | Word joiner (zero-width non-breaking space; used before hyphens) |

### Accented Characters (selection)

| Entity | Glyph | Description |
|---|---|---|
| `&#198;` | Æ | AE diphthong (uppercase) |
| `&#230;` | æ | ae diphthong (lowercase) |
| `&#338;` | Œ | OE diphthong (uppercase) |
| `&#339;` | œ | oe diphthong (lowercase) |
| `&#383;` | ſ | Long s (looks like italic f) |
| `&#253;` | ý | y with acute (represents "th'") |

### Typographic Ligatures and Special Letterforms

These private-use area characters are used in historical editions to represent specialized printing glyphs:

| Entity | Fallback | Description |
|---|---|---|
| `&#57348;` | ct | ct ligature |
| `&#57352;` | sh | Long s + h ligature (ſh) |
| `&#57356;` | Q | Alternate Q with swash (italic) |
| `&#57357;` | Q | Alternate cursive Q (italic) |
| `&#57358;` | E | Alternate E with rounded sides (italic) |
| `&#57359;` | N | Alternate N with swash (italic) |
| `&#57360;` | P | Alternate P with swash (italic) |
| `&#57361;` | A | Alternate A with swash (italic) |
| `&#57362;` | M | Alternate M with swash (italic) |
| `&#64256;` | ss | ff ligature (double long S) |
| `&#64257;` | fi | fi ligature |
| `&#64258;` | fl | fl ligature |
| `&#64259;` | ffi | ffi ligature |
| `&#64260;` | ffl | ffl ligature |
| `&#64261;` | ft | ft ligature |
| `&#64262;` | st | st ligature |

### Encoding Pattern for Special Characters

When a special glyph character is used in historical edition text, it is typically encoded using the `<choice>` element:

```xml
<choice>
  <orig>&#57358;</orig>
  <reg type="unicode">E</reg>
  <reg type="ascii">E</reg>
</choice>
```

---

## 23. Known Enumeration Values Summary

### 23.1 Variant Values

Values for the `variant` attribute on `<play>` and `<poem>`:

| Value | Description |
|---|---|
| `ps` | Standard PlayShakespeare.com edition (canonical plays) |
| `ps-poems` | PlayShakespeare.com poems and sonnets |
| `ps-apocrypha` | Apocrypha plays and poems |
| `first-folio` | First Folio of 1623 edition |
| `first-quarto` | First Quarto edition |
| `second-quarto` | Second Quarto edition |
| `fourth-quarto` | Fourth Quarto edition |
| `first-octavo` | First Octavo edition |
| `second-octavo` | Second Octavo edition |
| `third-folio` | Third Folio edition |
| `manuscript` | Manuscript edition |
| `modern` | Modern edition (used inside `<edition>` element, not as root attribute) |

### 23.2 Character Gender Values

Values for `gender` on `<castItem>`:
`male`, `female`, `horse`, `dog`, `bear`, `lion`, `snake`, `asps`, `crocodile`

### 23.3 Character Category Values

Values for `category` on `<castItem>`:

| Value | Description |
|---|---|
| `major` | Characters with substantial lines and plot importance |
| `supporting` | Secondary characters with significant roles |
| `minor` | Named characters with few lines but distinct functions |
| `ensemble` | Named characters with no lines |
| `extra` | Unnamed background characters |
| `animal` | Non-human characters |

### 23.4 Character Archetype Values

Values for `archetype` on `<castItem>`:
`hero`, `villain`, `clown`, `lover`

### 23.5 Action Types

Values for `type` on `<action>`:

| Value | Description |
|---|---|
| `enter` | Character(s) enter the scene |
| `exit` | Character(s) exit the scene (individual or group) |
| `action` | Generic physical action |
| `aside` | Aside action indication |
| `read` | Character reads aloud |
| `sound` | Sound effect or music |
| `fight` | Fight or combat action |
| `speak` | Character speaks (offstage or otherwise) |
| `song` | Character sings |
| `death` | Character dies; should contain `<death>` child element |
| `dance` | Characters dance |
| `dumbshow` | A dumbshow (mime sequence) |
| `see` | Character observes something |

**Note:** The spec documentation also mentions `exeunt` as a conceptual action type, but in actual XML files all group exits use `type="exit"`.

### 23.6 Name Change (alias) Values

Values for `nameChange` on `<role>` and `<persscene>`:

| Value | Description |
|---|---|
| `disguise` | Character appears under a disguise name |
| `ghost` | Character appears as a ghost |
| `accession` | Character has changed title/name due to succession or office |

### 23.7 Commodity Values

Values for `commodity` on `<num>` within `<statistics>`:

| Value | Description |
|---|---|
| `minutesRunning` | Estimated running time in minutes |
| `numberOfWords` | Total word count |
| `numberOfScenes` | Total scene count |
| `numberOfCharacters` | Total character count |
| `numberOfLines` | Total line count |
| `numberOfUniqueWords` | Count of unique words |
| `verse` | Percentage of verse lines |
| `prose` | Percentage of prose lines |

### 23.8 Relation Types

The `type` attribute of `<relation>` supports an extensive vocabulary. The following are defined in the specification; additional values appear in actual files reflecting the breadth of character interactions:

**Familial:** `parent`, `child`, `sibling`, `spouse`, `guardian`, `step-parent`, `step-child`, `cousin`, `father`, `mother`, `son`, `daughter`, `sister`, `brother`, `uncle`, `nephew`, `niece`, `grandmother`, `grandson`, `godparent`, `godfather`, `godmother`

**Romantic:** `love_interest`, `admirer`, `betrothed`, `suitor`, `courting`

**Social:** `friend`, `mentor`, `servant`, `master`, `confidant`, `protector`, `ward`, `acquaintance`, `follower`, `companion`, `colleague`, `associate`

**Political:** `subject`, `sovereign`, `rival`, `commander`, `subordinate`, `ambassador`, `heir`, `successor`, `ally`, `rebel`, `senator`, `leader`

**Antagonistic:** `enemy`, `betrayer`, `traitor`, `opponent`, `adversary`, `murderer`, `assassin`, `victim`, `usurper`

**Other (observed):** `spy`, `guard`, `gravedigger`, `messenger`, `actor`, `soldier`, `captain`, `physician`, `diplomat`, `neutral`, `confidant`, `ally`, and many more derived from specific play relationships.

### 23.9 Line Form Values

Values for `form` on `<l>`:

| Value | Description |
|---|---|
| `verse` | Blank verse (iambic pentameter or other metrical verse) |
| `prose` | Prose |
| `rhyme` | Rhyming verse |
| `lyric` | Lyric/song verse (sung lines) |

### 23.10 Genre Values

Values for `<category type="genre">`:
`Comedy`, `Tragedy`, `History`

### 23.11 Period/Subgenre Values

Values for `<category subtype="period">`:
`Elizabethan`, `Jacobean`, `Roman`

Values for `<category subtype="subgenre">`:
`Late Romance`, `Problem`

### 23.12 Speaker/Stage Rend Values

| Value | Context | Description |
|---|---|---|
| `center` | `<stage>`, `<speaker>`, `<acttitle>` | Center-align (historical editions) |
| `alignright` | `<stage>` | Right-align |
| `right` | `<stage>` | Right-align |
| `borders` | `<acttitle>` | Render with decorative horizontal border lines |
| `word` | `<acttitle>`, `<finis>` | Show only the word, without border lines |
| `hidden` | `<speaker>` | Hide the character name |
| `smallcaps` | `<speaker>` | Display in small caps |
| `coronation` | `<stage>` | Special coronation scene formatting |
| `parens` | `<sp>` | Wrap speech in parentheses |
| `indent(N)` | `<l>` | Indent by N syllables (shared verse lines) |
| `indent` | `<l>` | Single paragraph indent |
| `noitalic` | `<name>`, `<emph>` | Remove default italic formatting |
| `nobold` | `<bold>` | Remove default bold formatting |
| `ls8` etc. | `<emph>` | Letter-spacing value (historical editions) |

---

*End of PlayShakespeare.com XML Specification*
