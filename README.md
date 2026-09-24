# Sitering-metoden

An open protocol for measuring how often a firm is cited by generative AI engines when a buyer asks for a supplier recommendation.

Maintained by [Sitera](https://sitera.no), Oslo. Author: Emmanuel Philis (org.nr 937 705 794).
Revision: 23.09.2026 (previous: 19.09.2026).

---

## In six lines

Five engines — ChatGPT, Gemini, Claude, Perplexity, Copilot — for every series opened from 19.09.2026; a series opened earlier keeps its four. A frozen set of buyer questions, asked one per fresh session, without personalisation, from the market's own geography. Two draws on separate days, minimum. One row per question × engine × draw, in a CSV, with the model of the day recorded per cell. A firm enters the published ranking only if cited by two engines or in both draws; anything below that threshold is counted but not named. The result is a frequency — `X av N` — never a score.

---

## What this measures

Whether an engine **names a firm** in response to a buyer question — not whether it describes the firm correctly once you give it the name.

The distinction is the point of the protocol. A firm with a clean website is usually described accurately when named in the prompt, and absent when it is not. Norwegian shorthand: *gjenkjent, ikke anbefalt* — recognised, not recommended.

The protocol measures **exposure on a date**, not the effect of any action. A change between two draws is what a buyer saw on each day. It is reported; it is never explained as attribution.

Two terms are used throughout:

- **Sitering-metoden** — the protocol in this repository.
- **Siteringsgrad** — the resulting figure, always expressed as a frequency: `X av N` (X out of N question–engine cells **in one draw**). Never a percentage, never a score out of 100.

---

## Protocol

### 1. Engines

Five engines for every series opened from 19.09.2026:

| Engine | Access used for measurement |
|---|---|
| ChatGPT | no account, private window |
| Gemini | no account, private window |
| Claude | Incognito chat |
| Perplexity | dedicated measurement account, no history, no memory |
| Copilot | copilot.com (consumer), *Midlertidig chat*, personal account without history — never Microsoft 365 Copilot, which is grounded on a tenant |

A series opened before 19.09.2026 keeps its four engines (ChatGPT, Gemini, Claude, Perplexity) until it ends. **A follow-up draw always uses the panel of its first draw.** Results on four engines (`X av 20`) and on five (`X av 25`) are not comparable, and are never presented side by side as a trend.

**Model.** The model measured is the **free default of each engine on the day of the draw**. No pinning, no replay on a deprecated model, no retrospective correction. The model is recorded per cell (`modell`). When it differs between two draws, that is a property of the market on those dates and is reported next to the figures — it is not a reason to void or adjust cells.

### 2. Session conditions

- Browser in private/incognito mode, one fresh session per question.
- **No personalisation**: no account, or an account that carries no history capable of shaping the answer (see table above). "Logged out" is not the criterion — two engines require an account.
- Geography = the market measured. VPN exit in Oslo for a Norwegian panel; the exit country is recorded.
- Browser language = the market's language.
- Time of the draw recorded.

A session with history returns results shaped by the operator. It measures the operator, not the market.

### 3. Question set

- Written and frozen before the first draw. Published in full with the results.
- Phrased as a buyer asks, not as a vendor writes. Five forms are used per panel: a generic "best in <place>", a situation ("we need to…"), a criteria question ("what should we look for…"), an SME variant, and a specialist/small-firm variant.
- A follow-up draw repeats the questions **word for word**.
- Adding or changing a question does not extend a series — it starts a new T1.
- Each panel states its **scope**: registered activity codes, geography, and any exclusions (e.g. operations/MSP or staffing held outside an IT-consulting panel), dated and marked as reversible.

**Criteria questions are control cells.** Question phrasing decides the result more than any other variable. Supplier-form questions return named firms. Criteria-form questions ("what should we look for when choosing…") have returned no firm at all on every legal panel measured so far, on every engine. These cells stay in N — a wall is a finding — but the result line must state how many cells of N are control cells, so that `8 av 20` is read against the 16 cells where a name is possible.

### 4. Draws

- **Minimum two draws** on separate days before any result is treated as a fact.
- A single draw is noise. The same question two days apart returns different firms.
- Each draw is dated in the matrix.
- **The published figure is the most recent draw.** The first draw is the stability test and the entry criterion. Both matrices are kept.
- Alongside the figure, the number of **stable cells** (named in both draws) is reported per firm.

### 5. Publication threshold

A firm enters the published ranking only if it is cited **cross-engine** (named by at least two engines of the panel) **or cross-draw** (named in both T1 and T2).

**A firm at zero in the published draw is not named**, even if it met the threshold through the first draw.

Cited once, by one engine, in one draw → aggregated anonymously. It is not named and not discarded: the citation counts toward the totals, the firm does not appear in the list.

A firm absent from the engines is never named publicly.

Tables are sorted by frequency and are not a ranking. Two firms with the same figure are equal; a difference of one or two cells between two firms is inside the day-to-day movement observed between draws and is not a position.

### 6. Entity verification

Every named firm is resolved against the Norwegian business register (Enhetsregisteret) before publication: registered name, org.nr, and registered location. Names the engines return that resolve to no registered entity are reported as such. A lawyer named without a firm is counted on the firm they work for; a sole practitioner is a firm of their own.

---

## Matrix schema

One row per question × engine × draw. Semicolon-delimited CSV, UTF-8.

```
dato;tid;trekning;sektor;sporsmal;motor;modell;geo;alias_capte;entitet;orgnr;kategori;kilde;note
```

Matrices measured before 23.09.2026 follow the earlier schema: `dato;trekning;sektor;sporsmal;motor;modell;alias_capte;entitet;kategori;kilde;note` (no `tid`, `geo` or `orgnr`).

| Field | Content |
|---|---|
| `dato` | Date of the draw (DD.MM.YYYY) |
| `tid` | Time of the cell (HH:MM, local) |
| `trekning` | `T1`, `T2`, … |
| `sektor` | Sector of the panel |
| `sporsmal` | Question ID from the frozen set |
| `motor` | Engine |
| `modell` | Model as displayed by the engine on the day (e.g. `Flash-Lite`, `Sonnet 5`, `Automatisk`) |
| `geo` | VPN exit (e.g. `Oslo`) |
| `alias_capte` | The name exactly as the engine wrote it |
| `entitet` | The resolved registered entity |
| `orgnr` | Registered org.nr, empty if unresolved |
| `kategori` | Entity class — see below |
| `kilde` | `sok` (the engine ran a web search) or `minne` (answered from memory) |
| `note` | Free text — truncation, partial answer, A/B answer, entity resolution |

`kategori` separates the panel from everything else that comes back in the same answer. The panel class is sector-specific (`byraa`, `bemanning`, …). Four further classes have held across every panel measured so far:

- `programvare` — software and platforms
- `internasjonal` — foreign entity, pending verification of a local office
- `hors_panel` — outside the panel's stated scope (historical code, kept as is for comparability across editions)
- `ingen` — the cell returned no entity

`alias_capte` and `entitet` are kept separate on purpose. Engines return trade names, misspellings, and merged former names. The alias is recorded raw; the resolution goes in `entitet`/`orgnr`.

---

## Coding rules

Applied identically to every cell, every draw.

1. **A source badge alone is not a citation.** The entity must appear in the body of the answer as a concrete option. A link card, a citation pill, or a footnote without the name in the prose does not count.
2. **A map widget is not a citation.** An entity surfaced only in an embedded map result is not coded as named.
3. **Counter-examples are not citations.** An entity named to be dismissed is not coded as named.
4. **Software goes in its own class.** Platforms and tools are never counted among the service providers.
5. **Merged aliases are split before counting.** "Aider / VIEW Group" produces two entity rows.
6. **Every empty cell gets an explicit `INGEN` row.** The matrix holds exactly N rows per draw.
7. **Entity resolution precedes publication.** An entity is never excluded as foreign on intuition; the register decides.
8. **A/B answers.** When an engine serves two alternative answers for one question, an entity counts only if it appears in both. If the A/B test reappears on a fresh session, answer A alone is coded and the note says so.
9. **Gemini model drift.** The model selector resets on every new chat; the model is checked before each question. A cell taken on a different model than the rest of the draw is voided and redone on the draw's model.

### Completeness check

Before a matrix is closed: named citations + single-citation entities must equal the total citations recorded. If the equality does not hold, the matrix is not closed and nothing is published from it. The total is read from the matrix, never from a running count kept alongside it.

The number of silent cells out of N is reported with the result, control cells identified.

---

## Reproducibility

Everything a third party needs to repeat a draw is published with each barometer: the question set word for word, the engines and models of the day, the session conditions, the dates. The cell-level matrix is released in [geo-barometer-norge](https://github.com/sitera-no/geo-barometer-norge) **for every barometer edition measured from 23.09.2026 onward**, at the same time as the page, with entities below the publication threshold pseudonymised (a salted hash per entity), so that totals and walls are verifiable without naming a firm the threshold excludes. Earlier editions have no cell-level matrix in the repository; the matrix of any earlier edition still on file is available on request, under the same pseudonymisation.

A single operator measures and codes. Screenshots of each cell are kept and can be re-coded by a third party on request, on a sample.

An independent third draw run in the blind on a published panel, compared cell by cell to the published T2, is the intended test of this protocol. Sitera publishes the outcome of any such draw it is sent.

---

## What this protocol excludes

**Scanner and API output are not publishable.** Querying a model through an API returns a different distribution than a browser session, and neither an operator nor a client can reproduce it. API results are internal working material only.

**No absolute claims.** A result is `X av N`, dated, on a named question set. Never "invisible", never a visibility score out of 100.

**No aggregate across sectors.** Panels are not comparable to each other.

**No trend across editions with different questions, panels or N.** An edition is compared to nothing; it is dated.

---

## Known limits

- Five engines is a choice, not a census. The panel was extended from four to five on 19.09.2026 (Copilot: 20 % weekly use in Norway, Ipsos SoMe Tracker Q2 2026; access in 67 % of Norwegian companies, Norstat/Computas 2026). Series opened before that date keep four.
- Proprietary indexes persist. A correction published on a site stayed in circulation for over six weeks in one measured case. A draw run shortly after a change measures the state before the change.
- Two draws is a floor, not a guarantee of stability. Between two consecutive days, firms have moved by two to three cells with nothing changed on their websites.
- The operator sells reports in the sectors measured. The protocol is the same whoever is named; Sitera is never included in its own rankings.

---

## Changelog

- **23.09.2026** — Panel to five engines for new series (Copilot protocol added); model of the day recorded per cell instead of voiding cells; "logged out" replaced by "no personalisation" with the per-engine standard; geography, time, `modell`, `geo`, `orgnr` added to the schema; published figure = most recent draw, zero-in-published-draw rule, stable cells, control cells, panel scope line, A/B and Gemini rules, reproducibility section.
- **19.09.2026** — Copilot added to the panel for series opened from 19.09.2026 (commit 9814792).
- **18.09.2026** — First public revision.

---

## Licence

MIT. Use it, fork it, run it on your own market. If you publish results produced with this protocol, cite it (see `CITATION.cff`).

Measurements produced with this protocol for the Norwegian B2B market are published at [sitera.no](https://sitera.no).
