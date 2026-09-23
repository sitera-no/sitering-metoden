# Sitering-metoden

An open protocol for measuring how often a firm is cited by generative AI engines when a buyer asks for a supplier recommendation.

Maintained by [Sitera](https://sitera.no), Oslo. Author: Emmanuel Philis (org.nr 937 705 794).
Revision: 23.09.2026.

---

## In six lines

Five engines — ChatGPT, Gemini, Claude, Perplexity, Copilot — for every series opened from 19.09.2026; a series opened on four engines stays on four, and the two are not compared. A frozen set of buyer questions, asked in a logged-out browser session. Two draws on separate days, minimum. One row per question × engine × draw, in a CSV. A firm enters the published ranking only if cited by two engines or in both draws; anything below that threshold is counted but not named. The result is a frequency — `X av N` — never a score.

---

## What this measures

Whether an engine **names a firm** in response to a buyer question — not whether it describes the firm correctly once you give it the name.

The distinction is the point of the protocol. A firm with a clean website is usually described accurately when named in the prompt, and absent when it is not. Norwegian shorthand: *gjenkjent, ikke anbefalt* — recognised, not recommended.

Two terms are used throughout:

- **Sitering-metoden** — the protocol in this repository.
- **Siteringsgrad** — the resulting score, always expressed as a frequency: `X av N` (X out of N question–engine cells). Never a percentage, never a score out of 100.

---

## Protocol

### 1. Engines

Five engines for every series opened from 19.09.2026:

| Engine | Model recorded per cell |
|---|---|
| ChatGPT | yes |
| Gemini | yes |
| Claude | yes |
| Perplexity | yes |
| Copilot | yes |

A new T1 is measured on all five engines. A T2 keeps the panel of its T1: a series opened on four engines before 19.09.2026 is continued on the same four.

The model variant is recorded in its own column. A draw run on a different variant than the previous one is not comparable and its cells are voided, not adjusted.

### 2. Session conditions

- Browser in private/incognito mode.
- **Logged out**, or a measurement account with no history.
- No personalisation, no prior turns in the same conversation.
- One question per fresh session.

A logged-in session returns results shaped by the operator's own history. It measures the operator, not the market.

### 3. Question set

- Written and frozen before the first draw.
- Phrased as a buyer asks, not as a vendor writes.
- A follow-up draw repeats the questions **word for word**.
- Adding a question does not extend a series — it starts a new T1.

Question phrasing decides the result more than any other variable. Supplier-form questions ("who can help us with X") return named firms. Problem-form and criteria-form questions ("we don't show up when…", "what should we look for when choosing…") return no firm at all, across every engine measured.

### 4. Draws

- **Minimum two draws** on separate days before any result is treated as a fact.
- A single draw is noise. The same question two days apart returns different firms.
- Each draw is dated in the matrix.

### 5. Publication threshold

A firm enters the published ranking only if it is cited **cross-engine** (named by at least two engines of the panel) **or cross-draw** (named in both T1 and T2).

Cited once, by one engine, in one draw → aggregated anonymously. It is not named and not discarded: the citation counts toward the totals, the firm does not appear in the list.

A firm absent from the engines is never named publicly.

### 6. Entity verification

Every named firm is resolved against the Norwegian business register (Enhetsregisteret) before publication: registered name, org.nr, and registered location. Names the engines return that resolve to no registered entity are reported as such.

---

## Matrix schema

One row per question × engine × draw. Semicolon-delimited CSV, UTF-8.

```
dato;trekning;sektor;sporsmal;motor;alias_capte;entitet;kategori;source;note
```

| Field | Content |
|---|---|
| `dato` | Date of the draw (DD.MM.YYYY) |
| `trekning` | `T1`, `T2`, … |
| `sektor` | Sector of the panel |
| `sporsmal` | Question ID from the frozen set |
| `motor` | Engine |
| `alias_capte` | The name exactly as the engine wrote it |
| `entitet` | The resolved registered entity |
| `kategori` | Entity class — see below |
| `source` | `sok` (the engine ran a web search) or `minne` (answered from memory) |
| `note` | Free text — truncation, partial answer, model discrepancy, entity resolution |

`kategori` separates the panel from everything else that comes back in the same answer. The panel class is sector-specific (`byraa` for agencies, `bemanning` for staffing, and so on). Four further classes have held across every panel measured so far, and are the recommended starting set rather than a closed list:

- `programvare` — software and platforms
- `internasjonal` — foreign entity, pending verification of a local office
- `hors_panel` — outside the panel's scope
- `ingen` — the cell returned no entity

`alias_capte` and `entitet` are kept separate on purpose. Engines return trade names, misspellings, and merged former names. Collapsing the two at capture time destroys the evidence: the alias is recorded raw, the resolution goes in the note.

---

## Coding rules

Applied identically to every cell, every draw.

**1. A source badge alone is not a citation.** The entity must appear in the body of the answer as a concrete option. A link card, a citation pill, or a footnote without the name in the prose does not count.

**2. A map widget is not a citation.** An entity surfaced only in an embedded map result is not coded as named.

**3. Counter-examples are not citations.** An entity named to be dismissed ("avoid X", "X is not suitable here") is not coded as named.

**4. Software goes in its own class.** Platforms and tools are never counted among the service providers, even when the engine lists them side by side in answer to a supplier question. Mixing the two inflates the panel and misrepresents the market.

**5. Merged aliases are split before counting.** An engine returning "Aider / VIEW Group" produces two entity rows, not one.

**6. Every empty cell gets an explicit `INGEN` row.** This is what separates a wall — the engine answered and named nobody — from a cell that was never measured. It also guarantees the matrix holds exactly N rows.

**7. Entity resolution precedes publication.** Every named entity is resolved to an org.nr before it can enter a ranking. An entity is never excluded as foreign on intuition: a firm with a foreign parent may hold a registered local office, and the register decides, not the reader.

### Completeness check

Before a matrix is closed: named citations + single-citation entities must equal the total citations recorded. If the equality does not hold, the matrix is not closed and nothing is published from it. The total is read from the matrix, never from a running count kept alongside it.

The number of silent cells out of N is reported with the result. A panel with many walls is a finding, not a failed measurement.

---

## What this protocol excludes

**Scanner and API output are not publishable.** Querying a model through an API returns a different distribution than a browser session, and neither an operator nor a client can reproduce it. API results are internal working material only. Every published figure in a Sitera barometer comes from a browser session a reader could repeat.

**No absolute claims.** A result is `X av N`, dated, on a named question set. Never "invisible", never a visibility score out of 100.

**No aggregate across sectors.** Panels are not comparable to each other.

---

## Known limits

- Microsoft Copilot reached 20% weekly use in Norway (Ipsos SoMe Tracker, Q2 2026) and was added to the panel on 19.09.2026 for that reason. Adding an engine changes the denominator: four-engine and five-engine series have different N and are not compared with each other.
- Proprietary indexes persist. A correction published on a site stayed in circulation for over six weeks in one measured case. A draw run shortly after a change measures the state before the change.
- Two draws is a floor, not a guarantee of stability.

---

## Licence

MIT. Use it, fork it, run it on your own market. If you publish results produced with this protocol, cite it.

Measurements produced with this protocol for the Norwegian B2B market are published at [sitera.no](https://sitera.no).
