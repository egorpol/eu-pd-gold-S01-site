# Annotation rules

## Composers

| Column | Values | Meaning |
|---|---|---|
| `wikidata_same_person` | `yes` / `no` / `unsure` | Is the Wikidata item the composer the Wikipedia list means? Compare the Wikipedia article (`wikipedia_url`) with the item: name, dates, occupation, nationality. A namesake (painter, politician, father) is `no` |
| `imslp_check` | row has `imslp_url`: `same` / `different` / `unsure` | Does the IMSLP category belong to this composer? Compare IMSLP's life dates, links and works with the article |
| | row has no `imslp_url`: `found` / `none` / `unsure` | Does IMSLP have a composer category for this person? Search IMSLP for the name and its variants (transliterations, maiden names). `found` needs the URL in `imslp_found_url` |
| `imslp_found_url` | URL | Only with `found` |
| `death_status` | `dead` / `living` / `unknown` | `living` needs positive evidence of life after 2020 (recent activity, an institutional page, an interview). If you cannot tell, use `unknown` |
| `death_year` | `YYYY` | With `dead`. Leave empty if the person died but no source gives the year |
| `death_source` | GND number, or short name + URL or id | The source you relied on (see the hierarchy). A bare number is a GND id (`118776312`, written to the sheet as `GND 118776312`); name any other source (`LoC n92085993`, `Wikidata Q10274029`, `Grove https://…`) |
| `styles` (v2) | 1–3 style tags joined by `\|`, or `unknown` | The composer's style, most characteristic tag first ([Style](#style-gold-v2)) |
| `primary_period` (v2) | a period tag, or `unknown` | The period where the bulk of the output sits |
| `style_source` (v2) | short name + URL | The source you relied on for the style |
| `notes` | free text | Conflicts between sources, doubts, page changes |

### Source hierarchy for life dates

The sheet's `gnd_id` and `viaf_id` come from the Wikidata item (a few GND ids from a lobid.org name match). If the item is the wrong person, so are those records: check their dates and occupation against the article, and search by name when they do not match.

Use the highest level that has the information, and two independent sources where they are quick to find.

1. National authority files: GND (Deutsche Nationalbibliothek), BnF, Library of Congress Name Authority (id.loc.gov), or their VIAF cluster.
2. Reference works: Grove Music Online / Oxford Music Online, MGG, Baker's, national biographical dictionaries.
3. Institutional or press sources: music information centres, publishers' composer pages, obituaries in reputable newspapers.
4. Wikipedia or Wikidata, only when nothing above has the date. Wikidata is the dump's own source, so record it honestly in `death_source`.

When sources disagree, take the year most independent authorities give, and write the conflict in `notes`.

Decided cases:

- **The list row links the wrong article** (a namesake's page, such as a physicist's): `wikidata_same_person` = `no`. Answer the IMSLP and death columns for the composer the list row means (its nationality and dates identify him), and write the mix-up in `notes`. The scorer then counts the identity error and the death-year or PD errors it causes.

### Style (gold v2)

Style describes the composer's music as a whole, in the same terms the dataset's LLM labellers used, so their labels can be checked. The tags are the dataset's style vocabulary: the IMSLP periods plus `late_romantic`, and 15 movements and traditions from Wikidata and IMSLP. Each is defined, with its dates, typical composers and nearest neighbour, in [style_definitions.tsv](style_definitions.tsv) (contributors find the same table at the end of `RULES.md` and in the form).

- **`styles`**: up to 3 tags, the most characteristic first. A period tag and movement tags combine (`romantic|national_folk`). Give a movement tag only when a source places the composer in that movement, or the music clearly uses its technique; otherwise give the period tag alone. When the style changed over a career, tag the phases that are best known, in order of weight (`neoclassicism|national_folk|serialism`).
- **`primary_period`**: one period tag (`ancient` … `modern`), where the bulk of the output sits. Judge by musical language more than by date: a composer writing in a post-Wagnerian idiom in the 1930s is `late_romantic`. Language decides both ways: a tonal, lyrical idiom without post-Wagnerian chromaticism or very large forms stays `romantic` after 1880, and a composer who keeps a plainly 19th-century idiom after 1900 is `romantic` or `late_romantic`, in `styles` and here. When the language does not decide, use IMSLP's dates (Romantic 1815–1910, Early 20th century 1900–1945, Modern from 1945).
- **`unknown`**: only when no source lets you place the composer at all; then `primary_period` is `unknown` too. If you can name the period, its tag is the style.
- **`early_20th_century` and `modern`** are fallbacks for 1900–1945 and post-1945 music in a 20th-century idiom that no movement tag fits.
- **Ragtime is not `jazz`**: tag its period, mostly `early_20th_century`. `jazz` is for stride and jazz as a main part of the output.

**Sources for style**, highest first: (1) reference works: Grove / Oxford Music Online, MGG, Baker's, national music encyclopaedias and music information centres; (2) scholarly writing, publishers' composer pages, signed programme or liner notes; (3) the scores on IMSLP, to confirm a technique (a twelve-tone row, tape parts); (4) Wikipedia, only when nothing above covers the composer, and then say so in `style_source`. **Do not use** Wikidata's movement or genre statements, IMSLP's *Piece Style* line or the project's viewer: they are the labels under test.

Spend about 5 minutes on style. If that is not enough to place the composer in a movement, give the period tag and say why in `notes`.

## Works

| Column | Values | Meaning |
|---|---|---|
| `force_family` | a family below, or `unsure` | The instrumentation of the composer's **original** version |
| `genre_form` | a form below, `none`, or `unsure` | The form of the composer's original work |
| `has_files` | `yes` / `no` / `unsure` | Does the work page list at least one score, part or recording file? Files hosted on IMSLP's other servers count; cover images do not |
| `rights_basis` | a basis below, or `unsure` | IMSLP's own rights category, from the category list at the bottom of the work page |
| `notes` | free text | Doubts, several original versions, a page that changed |

### Instrumentation families

Judge the original scoring, ignoring arrangements by others. If the composer made several versions, choose the one that comes **later** in this list (the dataset uses the same rule when several labels apply).

| Family | Use for |
|---|---|
| `piano_solo` | one pianist (including left hand alone) |
| `piano_ensemble` | two or more pianists or pianos, or piano with other keyboards, no other instruments |
| `organ` | organ alone |
| `keyboard_other` | harpsichord, clavichord, celesta, harmonium and similar, alone |
| `solo_instrument` | one non-keyboard instrument, alone or with one keyboard (e.g. violin and piano) |
| `guitar` | anything with guitar or lute-family instruments and no voices, orchestra, band or electronics |
| `chamber` | two or more instrumentalists otherwise, no voices and no orchestra |
| `orchestral` | orchestra or string orchestra without soloists |
| `concerto` | soloist(s) with orchestra |
| `wind_band` | wind band, brass band, wind or brass ensemble |
| `solo_voice` | one or two voices with any accompaniment (including orchestra), or one or two voices alone |
| `choral` | any chorus, or three or more voices alone |
| `stage_opera` | opera, operetta, musical, staged vocal work |
| `stage_ballet` | ballet |
| `film_media` | film, television or radio music |
| `electronic` | with electronics or tape, and no voices, chorus, orchestra or band |
| `pedagogical` | methods, exercises, studies written as teaching material |
| `other` | none of the above (for example, unspecified instrumentation) |

Decided cases:

- **An instrument named next to an orchestra or string orchestra** (harp and string orchestra, 2 violins and small orchestra) is a soloist: `concerto`. It is `orchestral` only when the score treats it as an ordinary orchestral part, with no solo marking or separate solo staff.
- **Files only for an arrangement** still give `has_files` = `yes`; the family still follows the original.
- **IMSLP's "For …" categories and tags** are a starting point, not the answer. The dataset derives many labels from them, so check them against the work page's Instrumentation line and the score.

### Forms

Judge the composer's original; an arrangement takes the original's form. A piece drawn from a larger work (an overture, aria or suite from an opera or ballet) takes the larger work's form. If several apply, pick the first in this list (the dataset uses the same order).

| Form | Use for |
|---|---|
| `opera` | opera, operetta, singspiel, zarzuela or other staged sung drama |
| `ballet` | ballet or other staged dance work |
| `concerto` | called concerto, concertino, concerto grosso or sinfonia concertante |
| `symphony` | symphony, sinfonietta, or a sinfonia in several movements; a symphonic poem is not |
| `sonata` | sonata, sonatina or trio sonata, any instrumentation |
| `suite` | suite or partita: contrasting movements published as one work |
| `variations` | variations on a theme, under any title |
| `etude` | étude or study, alone or in a set; a method or exercise book is not |
| `prelude_fugue` | prelude, fugue or fughetta, alone or paired; chorale preludes count |
| `string_quartet` | for exactly 2 violins, viola and cello; other quartets are not |
| `dance` | dance or set of dances (waltz, mazurka, polonaise, minuet…); marches count |
| `mass_requiem` | Mass or Requiem, or a single movement or Proper text of one |
| `oratorio_cantata` | oratorio, cantata or Passion: unstaged, voices and instruments, several numbers |
| `overture_tone_poem` | concert overture, symphonic poem or tone poem |
| `song_cycle` | songs the composer published as one connected cycle |
| `song` | a song, Lied, mélodie, part-song or set of songs that is not a cycle |
| `none` | none of these applies (a nocturne, fantasia, motet, caprice, divertimento, serenade…) |

Decided cases:

- **Arrangements**: judge the original; `arrangement` is not an answer (the scorer counts the dataset's `arrangement` label as its own error class).
- **A piece drawn from a larger work** takes the larger work's form, as in Library of Congress practice ("Operas -- Excerpts"): an opera's overture is `opera`, a ballet suite is `ballet`.
- **`concerto`** only for works called concerto (concertino, concerto grosso, sinfonia concertante). Other works for soloist and orchestra take their own form or `none`; their family can still be `concerto`.
- **`string_quartet`** only for 2 violins, viola and cello; IMSLP's Quartets category also holds piano quartets.
- **`song`** is the dataset's `song` (IMSLP Songs/Lieder that are not song cycles; dumps before r019 call it `other`, which the scorer reads as `song`). The scorer reads an empty cell as `none`.

### Rights basis

First match in this order, from the categories at the bottom of the work page:

| Basis | IMSLP categories |
|---|---|
| `eu_warning` | WorkNonPD-EU, WorkNonPD-USandEU |
| `us_only` | WorkPD-USonly (public domain in the US only; the opposite of a "Non-PD US" warning) |
| `licensed` | Works not in public domain, FileNonPD-PermissionGranted, Works Licensed through BMI / ASCAP / GEMA |
| `wima` | WIMA files |
| `us_routes` | Work-PD-US-notrenewed, PD-US-no notice |
| `none` | none of these |

WorkNonPD-US alone is a US warning, not a basis: it gives `none`.

The **Copyright** line in a file's box ("Public Domain - Non-PD US, Non-PD EU") is IMSLP's status for that file: Public Domain means in Canada, and the rest are warnings. It does not set the basis; only the categories do. When the file line and the categories disagree, answer from the categories and write the difference in `notes`.

## Style definitions

| Tag | Period | Definition | Typical composers | Not this |
|---|---|---|---|---|
| `ancient` | before 476 | Music of antiquity: Greek, Roman and other ancient traditions, known from fragments and treatises. | Mesomedes; the Seikilos epitaph | Modern reconstructions, or later works on ancient subjects |
| `medieval` | 476–1400 | European sacred and secular music before about 1400: chant, troubadours and trouvères, Ars antiqua, Ars nova. | Hildegard of Bingen, Pérotin, Machaut | Later chant-based or neo-medieval works (tag their own period) |
| `renaissance` | 1400–1600 | Vocal and instrumental polyphony of the 15th and 16th centuries: Franco-Flemish, Roman and Venetian schools, the madrigal. | Josquin, Palestrina, Lassus | Later pieces in the old style (stile antico) |
| `baroque` | 1600–1760 | Basso continuo, concertato writing and contrapuntal forms of about 1600–1750. | Monteverdi, Corelli, Bach, Handel | 20th-century pastiche of Baroque forms (neoclassicism) |
| `classical` | 1730–1820 | Galant and High Classical style: periodic phrasing, sonata form, mainly homophonic textures. | Haydn, Mozart, early Beethoven | 'Classical music' in the broad sense; 20th-century neoclassicism |
| `romantic` | 1815–1910 | Nineteenth-century Romantic language: expanded tonal harmony, lyrical melody, character pieces, programme music. Judged by language: a composer who keeps this idiom into the 20th century stays romantic. | Schumann, Chopin, Brahms, Tchaikovsky | Post-Wagnerian fin-de-siècle idiom (late_romantic) |
| `late_romantic` | about 1880–1930, by language | Fin-de-siècle and post-Romantic language: post-Wagnerian chromaticism, very large forms and forces, often carried well into the 20th century. Activity after 1880 alone is not enough. | Mahler, Richard Strauss, Reger, Rachmaninoff, Elgar | Earlier 19th-century romantic; impressionism; atonal expressionism |
| `early_20th_century` | 1900–1945 | Music written mainly in 1900–1945 in a 20th-century idiom that no finer tag fits: extended tonality, modality, new rhythmic idioms. | A fallback: prefer a movement tag when a source names one | Post-1945 output (modern); a plainly 19th-century idiom (romantic or late_romantic) |
| `impressionism` | about 1890–1930 | Harmony and timbre used for colour: parallel chords, whole-tone and modal scales, static harmony, evocative titles. | Debussy, Ravel, Griffes | Expressionism; late-Romantic chromaticism |
| `expressionism` | about 1905–1930 | Extreme expressive intensity, often atonal or hyper-chromatic, with fragmented forms. | Schoenberg (1908–1921), Berg (Wozzeck), early Webern | Twelve-tone works (serialism); atonality without that intensity (atonal_modernism) |
| `national_folk` | mainly 1840–1950 | Art music built on a national or folk idiom: folk tunes, dances, modes, national subjects and genres. Folk-song arrangements count here when the composer also writes original works in the idiom. | Smetana, Dvořák, Grieg, Albéniz, Bartók, Kodály | Traditional music itself (traditional_folk); a single folk-song arrangement in an otherwise different output |
| `neoclassicism` | about 1918–1960 | A 20th-century return to pre-Romantic forms and textures: clarity, motor rhythms, Baroque or Classical models, often with ironic harmony. | Stravinsky (1920–1951), Poulenc, Hindemith, Prokofiev's Classical Symphony | Baroque or Classical music itself |
| `atonal_modernism` | from about 1910 | Free atonal or modernist pitch language without strict serial technique. | Ruggles, Wolpe, Carter | Twelve-tone or serial works (serialism); expressionism |
| `serialism` | from about 1921 | Twelve-tone technique, or serial organisation of pitch and other parameters, including post-serial practice. | Schoenberg (from 1923), Webern, Dallapiccola, Boulez, Babbitt | Free atonality (atonal_modernism, expressionism) |
| `modern` | 1945 onwards | Post-1945 classical music in a 20th-century idiom that no finer tag fits: modal, extended-tonal or freely chromatic contemporary writing. | A fallback: prefer a movement tag when a source names one | Output mainly before 1945 (early_20th_century); a plainly 19th-century idiom (romantic or late_romantic) |
| `avant_garde` | from about 1950 | Experimental and radical practice: indeterminacy, graphic scores, extended techniques, music theatre, happenings, Fluxus. | Cage, Kagel, Cardew | Music that is only dissonant (atonal_modernism) |
| `electroacoustic` | from 1948 | Tape music, musique concrète, live electronics, fixed-media or computer music as a main part of the output. List it first when it is the main output; add avant_garde second only when indeterminacy, graphic scores and the like are also central. | Pierre Schaeffer, Pierre Henry, Daphne Oram | One or two works with tape in an otherwise instrumental output |
| `minimalism` | from the 1960s | Steady pulse, repetition and gradual process over static or slowly changing, mostly consonant harmony. | Riley, Reich, Glass | Simple or tonal music without repetitive process |
| `postminimalism` | from the 1980s | Builds on minimalism's pulse and repetition with more harmonic motion, melody or formal contrast. | John Adams, Michael Torke, David Lang | Classic process minimalism (minimalism) |
| `spectralism` | from the 1970s | Harmony and form derived from acoustic spectra: the harmonic series, timbre analysis, slow transformations. | Grisey, Murail, Dufourt, Rădulescu | Colouristic harmony in general (impressionism) |
| `polystylism` | from the 1960s | Deliberate collage or juxtaposition of historical styles and quotations within a work. | Schnittke, Berio (Sinfonia), Bernd Alois Zimmermann | Writing in one older style (neoclassicism) |
| `jazz` | from about 1900 | Jazz, stride or other jazz-idiom composition as a main part of the output. | Jelly Roll Morton, James P. Johnson, Duke Ellington | Ragtime itself (tag its period, mostly early_20th_century); concert works with jazz influence (Ravel, Milhaud: tag their own style) |
| `traditional_folk` | any | Traditional or folk repertoire itself: anonymous songs and dances, or a collector's editions of them. Only when the output is mainly such collections or editions. | Folk-song collectors whose output is mainly such collections | Art music in a national idiom (national_folk) |
| `non_western_classical` | any | A classical tradition outside the Western one: Hindustani, Carnatic, Arabic or Persian art music, East Asian court music. | Tyagaraja, Muthuswami Dikshitar | Western art music on non-Western subjects |
