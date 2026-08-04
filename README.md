# inflect - Ecko Std Lib Package

URL slugs, English pluralisation, ordinals and identifier case conversion.

The shapes a CMS needs between a human title and a machine name.

Pure computation - no capabilities.

## Install

```bash
ecko get github.com/ecko-lang/inflect
```

```ecko
import inflect
```

Needs Ecko 0.10.0 or later.

## Usage

```ecko
inflect.slug("Introducing Ecko 0.10!")   # "introducing-ecko-0-10"
inflect.slug("Café Münster")             # "cafe-munster"

inflect.plural("post")                    # "posts"
inflect.singular("people")                # "person"
inflect.pluralize(3, "person")            # "3 people"

inflect.ordinal(21)                       # "21st"

inflect.snake("XMLHttpRequest")           # "xml_http_request"
inflect.kebab("Hello World")              # "hello-world"
```

Routes, which is the case the package was written for:

```ecko
name = inflect.snake("Post")
"/" + inflect.plural(name)     # "/posts"
"/" + name + "/:id"            # "/post/:id"
```

## API

### Slugs

| function | what it does |
|---|---|
| `slug(text, opts?)` | URL slug; `opts` takes `separator` (default `"-"`) and `max_length` (default `0`, no limit) |
| `transliterate(text)` | accented Latin to ASCII, on its own |
| `translit_table()` | the character map used |

### English

| function | what it does |
|---|---|
| `plural(word)` | the plural, preserving case |
| `singular(word)` | the singular, preserving case |
| `pluralize(count, word)` | `"3 people"` - number included |
| `word_for(count, word)` | just the word for that count |
| `ordinal(n)` | `21` becomes `"21st"` |
| `uncountable()`, `irregular()`, `keeps_f()`, `o_takes_es()` | the exception tables |

### Cases

| function | what it does |
|---|---|
| `snake`, `kebab`, `camel`, `pascal`, `title` | the five shapes |
| `to_case(text, name)` | pick one by name; an unknown name raises |
| `case_names()` | the names `to_case` accepts |
| `words(text)` | split any shape into lowercase words |
| `capitalize(word)` | capitalise the first character |

## Notes

**Slugs transliterate rather than strip.** `"Café Münster"` becomes
`cafe-munster`, not `caf-mnster`. Dropping the accented characters produces a
URL nobody can read and one that collides with unrelated titles. Expansions are
handled too: `ß` becomes `ss`, `æ` becomes `ae`.

**A length limit cuts at a word boundary.** Cutting mid-word would produce a slug
that reads as a different word, so `max_length: 10` on "the quick brown fox"
gives `the-quick` rather than `the-quick-`.

**Pluralisation is rules plus exceptions, and the exceptions win.** The tables
are exported so you can see exactly what is known: `irregular()` for
person/people, `uncountable()` for the words with no separate plural,
`keeps_f()` for roofs-not-rooves, `o_takes_es()` for heroes-not-heros. This is
English, so the tables are the honest part and the rules are the guess. If a word
matters to you, check it.

**Case is preserved.** `plural("Post")` is `"Posts"`, not `"posts"` - a CMS
pluralises headings and route names, and losing the capital there is a visible
bug.

**Ordinals are not "look at the last digit".** 11, 12 and 13 take `th` despite
ending in 1, 2 and 3. That trap is most of why the function exists.

**Acronyms are not preserved in case conversion.** `pascal("XMLHttpRequest")`
gives `XmlHttpRequest`, because `words` lowercases everything before rebuilding.
That matches what Rails does without an acronym list configured. If you need
`XMLHttpRequest` back, keep your own map.

**English only.** Nothing here knows about other languages, and pluralising
Polish or Arabic with these rules would produce nonsense.

## Testing

```bash
ecko test
```

Offline and deterministic.

## License

MIT
