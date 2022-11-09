Names are hard, as summarized in the oft-quoted article [Falsehoods programmers believe about names](https://www.kalzumeus.com/2010/06/17/falsehoods-programmers-believe-about-names/).

The [GEDCOM 5.5.1](https://gedcom.io/specifications/ged551.pdf#%5B%7B%22num%22%3A94%2C%22gen%22%3A0%7D%2C%7B%22name%22%3A%22FitR%22%7D%2C-83%2C311%2C695%2C797%5D)
standard defined a `PERSONAL_NAME_STRUCTURE`.
This was refined slightly in [GEDCOM 7.0](https://gedcom.io/specifications/FamilySearchGEDCOMv7.html#PERSONAL_NAME_STRUCTURE).
It works well for most North-American and some European names, but it unable to properly represent names from many other cultures.

This project has been created to develop the `PERSONAL_NAME_STRUCTURE`,
with aims of supporting a much wider range of human names and
of supporting applications that process them.

The following tasks have been identified.  More may be added as the project develops.

1) Document commonly used naming structures for various cultures.
2) Decide the purpose/functionality of `NAME` records.  This might include: display, sorting, searching, grouping, statistics, etc.
3) For individuals with multiple `NAME` records, consider how we might sort them, for example using `NAME.DATE`.
4) For individuals with multiple `NAME` records, consider how to identify a "preferred" name, for example by selecting the first one to occur in the GEDCOM record.
5) Consider any special handling required for names with spaces, e.g. "St John".
6) Review existing extensions, such as `_RUFNAME`
7) Develop a new `PERSONAL_NAME` structure and a roadmap to reach it, taking into account the constraints of semantic versioning.

Contributions are welcome.  Please use
the [discussions](https://github.com/fisharebest/gedcom-name/discussions),
the [issue-tracker](https://github.com/fisharebest/gedcom-name/issues)
or create [pull-requests](https://github.com/fisharebest/gedcom-name/pulls) as appropriate.
