# Writing sample - date and time formatting style guide

> I wrote this internal style guide to cover a variety of date and time scenarios in both documentation and application interfaces.

# Date and time

Format date and time using either a [long formal](#long-formal-style) or [condensed](#condensed-style) style.

## General guidelines

The following applies to both long formal and condensed styles:

### Date

* By default, all dates must use the US standard of date ordering: `Month Day, Year`.
  * Ideally, the user should have the ability to set their preferred date order, but the US standard applies when this customization is not possible.
* Show the day as a numeral. Only use 2 numerals if you need them. Don't prepend the first 9 days with an extra `0`.
* Don't append the day with an ordinal abbreviation like `st`, `nd`, `rd`, `th`. Ordinal abbreviations in this context do not improve a date's readability.
* Follow the `Day` portion with a comma and a space.
* Show the full year with all 4 numerals. Don't shorten the year to the final 2 numerals, such as `'89`, `'95`, `'21`, etc.

### Time

* When the date and time must be shown together, the time must follow the date.
* Write the time in numerals with the hours and minutes separated by a colon `:`.
* Use the 12-hour clock (unless you're referring to something that uses a 24-hour clock exclusively).
  * When using the 12-hour clock, append the time with a space and `AM` or `PM` to indicate morning and afternoon respectively.
  * Don't prepend the hours position of the 12-hour clock with a `0` for hours 1 through 9. This extra character impacts readability.

**What about ISO-8601?** While [ISO-8601](https://en.wikipedia.org/wiki/ISO_8601) is the all-encompassing international standard for date formatting, it's not easily consumable in the contexts of interfaces or documentation. The style suggested here is designed to emphasize easy readability above all. However, there are cases for using ISO-8601 in non-interface, non-documentation contexts, such as data exports. Customers often use these exports to parse data programmatically, and ISO-8601 provides a consistent format to engineer around.

## Long formal style

Use the long formal style when writing dates and times **in the context of a sentence**. Instances include:

* Product documentation
* In-app overlays, such as banner or lightbox announcements.
* Email communications

**What about showing dates and times in product interfaces?** Don't use the long formal style for in-app data visualizations like tables, dashboards, cards, labels, etc **unless the situation calls for it in the context of a sentence**. Use the [condensed style](#condensed-style) for these cases.

Dates in the long formal style must fully spell out the name of the month.

Examples:

_As of September 23, 2021, our online platform will support a new authentication method._

_On December 4, 2020, our security researchers published an assessment of a critical remote code execution vulnerability._

### Long formal style with time

To indicate the time with a long formal style date, follow these rules:

* Make sure the time follows the `at` preposition.
* Always include a reference to the time zone using its full initialism. Take care to use the correct initialism for Standard or Daylight time.

| Time zone | Initialism to use |
| --- | --- |
| Pacific Standard Time, Pacific Daylight Time | PST, PDT |
| Mountain Standard Time, Mountain Daylight Time | MST, MDT |
| Central Standard Time, Central Daylight Time | CST, CDT |
| Eastern Standard Time, Eastern Daylight Time | EST, EDT |
| Coordinated Universal Time | UTC |

Examples:

_As of September 23, 2021 at 9:00 AM EDT, our online platform will support a new authentication method._

_On December 4, 2020 at 10:30 PM EST, our security researchers published an assessment of a critical remote code execution vulnerability._

### Long formal style with the day of the week

If including the day of the week would be useful, follow these rules:

* Locate the day of the week first in the order: `Day name`, `Month` `Day numeral`, `Year`.
* Fully spell out and capitalize the day name. Don't use abbreviations.

Examples:

_As of Thursday, September 23, 2021 at 9:00 AM EDT, our online platform will support a new authentication method._

_On Friday, December 4, 2020 at 10:30 PM EST, our security researchers published an assessment of a critical remote code execution vulnerability._

## Condensed style

Use the condensed style when writing dates and times **in the context of consuming data**. Instances include:

* Table cells in product documentation and applications
* Dashboard cards
* Graphs
* Labels
* Fields
* Calendars

Dates in the condensed style must have the following:

* Months must be shown in abbreviated name format, as listed here:
  * `Jan`, `Feb`, `Mar`, `Apr`, `May`, `Jun`, `Jul`, `Aug`, `Sep`, `Oct`, `Nov`, and `Dec`.
* Don't indicate the named day of the week (full or abbreviated) as part of the date, such as `Monday`, `Tuesday`, etc. This information is not relevant in a condensed format.

Examples:

_Sep 23, 2021_

_Dec 4, 2020_

**Can I drop parts of the condensed date if I need the space?** Yes, but only if the interface context justifies doing so. For example, a graph designed to show a rolling snapshot of 1 year does not need to repeatedly show the same year for each point along the x-axis. Only omit portions of the date if the portion being omitted is evident elsewhere in the interface object the user is working with.

### Condensed style with time

To indicate time with a condensed style date, follow these rules:

* Make sure the time follows the `at` preposition.
* If you need to indicate a seconds portion of the time, follow the `H:MM:SS` format.
* Always include a [reference to the time zone](#long-formal-style-with-time) using its full initialism. Take care to use the correct initialism for Standard or Daylight time.

Examples:

_Sep 23, 2021 at 4:00 PM UTC_

_Dec 5, 2020 at 5:30 AM PDT_

_Sep 23, 2021 at 9:00:41 AM UTC_

_Dec 4, 2020 at 10:30:17 PM PDT_

#### Table cell alignment

When showing dates and times in table cells, the interface should support compartmentalized alignment so dates are easily readable row to row:

* When showing the date alone, allow whitespace to right-align the table data according to the `Day,` portion of the cell.
* When showing the time alone, allow whitespace to right-align the table data according to the `Hours:` portion of the cell.
* When showing both, allow whitespace for both alignments to take place.

These monospaced examples illustrate this table cell alignment standard:

`Jan  1, 1991 at  1:25 AM UTC`
`Feb  5, 1999 at  2:15 PM UTC`
`Mar  9, 2002 at  3:41 AM UTC`
`Apr 11, 2004 at  4:11 PM UTC`
`May 15, 2009 at  5:03 AM UTC`
`Jun 17, 2010 at  6:58 PM UTC`
`Jul 19, 2012 at  7:34 AM UTC`
`Aug 20, 2015 at  8:39 PM UTC`
`Sep 21, 2016 at  9:02 AM UTC`
`Oct 24, 2019 at 10:45 PM UTC`
`Nov 26, 2020 at 11:27 AM UTC`
`Dec 31, 2021 at 12:09 PM UTC`
