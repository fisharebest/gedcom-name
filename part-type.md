This document describes a single idea, but with three variants: an extension to 7.0, an addition to 7.1, and a change to 8.0. Each has the same underlying design, but the implementations differ in some details based on the case.

The proposed new structure of a personal name consists of the following concepts:

1. An individual can have any number of names.
2. A name can have any number of parts. 
    a. A name part can have any number of types, taken from an extensible enumeration set.
3. A name can have any number of forms. 
4. A name can have any number of types, taken from an extensible enumeration set.

There are aspects of some names that this structure cannot express:

- It cannot express a hierarchy of parts.
    
    For example "Mary Villiers, Countess of Buckingham" has (among other parts)
    
    - peerage title "Countess of Buckingham"
    - noble rank "Countess"
    - gendered suffix "ess"
    
    Conceptually these are nested: the gendered suffix is part of the noble rank which is part of the peerage title which is part of the name. However, this proposal does not include nested part structures; it can include all of these parts and in some cases nesting might be inferred from shared substrings, but it cannot directly indicate the nesting.

- It cannot express derivation relationships between names or name parts.
    
    It can indicate that the "Peggy" in "Margaret Peggy Shippen" is a diminutive nickname derived from another name part, but cannot indicate that the name part it is derived from is "Margaret".
    
    It can indicate that "Curly Joe" is a stage name but cannot indicate that "Joe" is derived from another name of the same individual (Joe Besser) while "Curly" is derived from the stage name of a different individual (Jerome Howard).

There are two design elements on which I am not yet settled:

- What (if anything) should name part ordering indicate in 7.1 and beyond?
    
    - The specification's general "user preference" meaning of ordering seems a bit odd when applies to name parts.
    
    - Option: writing order (a long name form might be created by concatenating name parts in order).
    
    - Option: alphabetizing order (sort by the first name part first).
    
    - Option: explicitly state that order doesn't matter.

- In how many places should DATE, SOUR, and NOTE structures appear?

    - Simplest to have them only for the NAME as a whole, not any parts of forms.

    - Some sources provide only a FORM; others require splitting into a few parts; very few provide the entire structure of the name, which is often derived from local knowledge about naming patterns rather than from a source about the structure of the specific name itself.

# Clean design (draft for 8.0)

```gedstruct
n @XREF:INDI@ INDI              {1:1}
  +1 NAME                       {0:M}
     +2 TYPE <List:Enum>        {0:1}
     +2 PART <Text>             {0:M}
        +3 TYPE <List:Enum>     {0:1}
     +2 FORM <Text>             {1:M}
        +3 TYPE <List:Enum>     {0:1}
        +3 DATE <DateValue>     {0:M}
        +3 <<SOURCE_CITATION>>  {0:M}
        +3 <<NOTE_STRUCTURE>>   {0:M}
     +2 DATE <DateValue>        {0:M}
     +2 <<SOURCE_CITATION>>     {0:M}
     +2 <<NOTE_STRUCTURE>>      {0:M}
```

`PART` is a piece of a name. It needn't appear in any name `FORM`; for example, Alfred Kowalski might include a `2 PART Kowalska`, the feminine form of the Kowalski family name, to better facilitate indexing and searching despite Alfred not using the feminine name in any `FORM` of his name.

`PART`s need not be mutually exclusive. Overlapping `PART` payloads can help represent names that have a more complicated structure than a simple sequence of parts.

`FORM` is a way the name appears in print, speech, or other communication. A `FORM` may include characters that do not appear in any `PART`; common examples are spaces between parts and abbreviations, but it is not limited to this. A name with one or more `FORM`s but no `PART`s is permitted, though not encouraged because `PART`s support useful indexing, searching, and displaying functionality for many applications.


## Types

Names may have types, and name parts may have types. Both come from enumeration sets. Many fields are shared between the two sets.

NAME-TYPE only:

| Tag | From | Meaning |
|-----|------|---------|
| AKA | v7.0 | Also known as, alias, etc. |
| BIRTH | v7.0 | Name given at or near birth. |

PART-TYPE only:

| Tag | From | Defined as | Notes |
|-----|------|------------|-------|
| NPFX | v7.0 | Text that appears on a name line before the given and surname parts of a name. | Implication: the person attaches this part to their name, but does not consider it part of the name itself. |
| NSFX | v7.0 | Text which appears on a name line after or behind the given and surname parts of a name. | Implication: the person attaches this part to their name, but does not consider it part of the name itself. |
| SPFX | v7.0 | A name piece used as a non-indexing pre-part of a surname. | Developer alert: show as part of surname, but skip when sorting by surname. |
| GIVN | v7.0 | A given or earned name used for official identification of a person. | I think this definition is not how the tag is used. I think it's used for what wikipedia defines as "the part of a personal name that identifies a person and differentiates that person from the other members of a group who have a common surname." Incompatible with SURN. |
| SURN | v7.0 | A family name passed on or used by members of a family. | This definition is vague enough to make some think it includes patronymics and others to disagree. Incompatible with GIVN. |
| PRIMARY | GEDCOM-X | The name of most prominent in importance among the names of that type. Requires GIVN, SURN, NPFX, or NSFX. |  |
| ESTATE | #353 | House name, farm name, name after moving into or marrying into a house/farm. Implies LOCATION. Incompatible with SURN. | |
| UNIFIED | #353 | Unified spelling for a family name | Implication: not part of the written/spoken version of the name for this individual. |
| ROEPNAAM | #472 | A name provided at birth for use in all situations except legal documents. Implies GIVN and BIRTH. | The name of this tag comes from Dutch instead of English because no suitable English word was found; the tag does not imply Dutch culture or ancestry. |
| RUFNAME | #472 | A given name underlined or otherwise indicated on documents as one not to be omitted when only one given name is used. Implies GIVN and PRIMARY. | The name of this tag comes from German instead of English because no suitable English word was found; the tag does not imply German culture or ancestry. |
| LOCATION | #472 | A name indicating a location of note, such as a city associated with the person. Often includes "of" or "from" type particles. Incompatible with SURN. |
| GENERATIONAL | #472 | A name part shared by particular generation of a family (i.e. siblings or first cousins, but not their parents or children). Implies a cultural pattern of sharing this part, not just a particular family's aesthetic naming patterns. | |
| PATRONYMIC | #472 | A name of the individual's father, possibly with a patronymic modifier like the prefix "bar" or suffix "sen" or "dotter". | |
| MATRONYMIC | #472 | A name of the individual's mother, possibly with a matronymic modifier. | Added for parallelism with PATRONYMIC, but no examples were included in #472. |
| PATERNAL | #472 | A name inherited from the individuals' father's family. Implies SURN. | |
| MATERNAL | #472 | A name inherited from the individuals' mother's family. Implies SURN. | |


NAME-TYPE or PART-TYPE depending on person:

| Tag | From | Defined as | Notes |
|-----|------|------------|-------|
| NICK | v7.0 | A descriptive or familiar name that is used instead of, or in addition to, one’s proper name. | This definition is known to be understood differently by different people; see #472 for examples. |
| IMMIGRANT | v7.0 | Name assumed at the time of immigration. |
| MAIDEN | v7.0 | Maiden name, name before first marriage. |
| MARRIED | v7.0 | Married name, assumed as part of marriage. |
| PROFESSIONAL | v7.0 | Name used professionally (pen, screen, stage name). |
| OTHER | v7.0 | A value not listed here; should have a PHRASE substructure |
| ADOPTED | #355 | Given as part of being adopted into a family |
| DIVORCED | #353 | Name after a divorce |
| RELIGIOUS | #353 | Religious name, name adopted after joining a religious order |
| VARIANT | #353 | Different spelling for a name, also spellings based on other languages such as Latin, French |
| LEGAL | #472 | A name used for legal and official documents, but not in daily use. |
| FORMAL | #472 | A name only used official, formal settings. |
| INFORMAL | #472 | A name only used in casual, intimate, or informal settings. |



# Backwards-compatible design (draft for 7.1)

1. Add `PART` (or `OTHER`?) to the list of `PERSONAL_NAME_PIECES`.
2. Define the existing `PERSONAL_NAME_PIECES` types (`NPFX`, `GIVN`, `NICK`, `SPFX`, `SURN`, and `NSFX`) as synonyms for `PART` with the structure type moved to a `TYPE` substructure. Note: this means that the order of `PART`s is not preserved in general (some of them can be changed to other structure types, re-ordered, and then changed back) and thus has no meaning.
3. Define the `NAME` structure's payload, minus the slashes around a `SURN` part, as a synonym for a most-preferred (first) `FORM`.

For example, these two names would be synonymous and could be freely converted between:

```gedcom
1 NAME Albert /Emmerich/
2 GIVN Albert
3 TYPE RUFNAME
2 SURN Emmerich
2 FORM Albert
```

```gedcom
1 NAME
2 PART Albert
3 TYPE GIVN, RUFNAME
2 PART Emmerich
3 TYPE SURN
2 FORM Albert Emmerich
2 FORM Albert
```

# Extension-only design

1. Add a `_PART` extension type to the list of `PERSONAL_NAME_PIECES`.
2. Add a `_TYPE` substructure to all `PERSONAL_NAME_PIECES`; do not include those parts types that are synonymous with the existing `PERSONAL_NAME_PIECES` structure types to avoid creating extensions that duplicate standard structures.
3. Add `_TYPE` to `PERSONAL_NAME` for storing name types beyond the first
4. Extend the `NAME`.`TYPE` set.
5. Add a `_FORM` extension type; define the most-preferred form to appear as the `NAME` payload, not a `_FORM`, with `_FORM` only for additional name forms to avoid creating extensions that duplicate standard structures.
6. Add `DATE`, `NOTE`, and `SOUR` relocated standard structures to the name.
