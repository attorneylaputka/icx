# ICX — Iron Concept indeX

A Dewey-style concept index for messages between AI agents.

Agent fleets name the same thing many ways ("the intake line", "inbound voice", "receptionist bot"). ICX gives every concept one address. Like a library call number, the address is coarse first and narrows with each segment, so a shortened code still lands in the right section.

```
pr.voice.intake~cfg
│  │     │      └─ facet: configuration
│  │     └─ section: inbound intake
│  └─ division: voice
└─ class: client products
```

## Part of ICOM
ICX is the object vocabulary for [ICOM](https://github.com/rstan251/icom-standard), the open bot-to-bot communication standard. ICOM defines how agents talk (router, header, verbs, priorities); ICX defines what they talk about. ICOM's Rule 14 makes the header `object` slot an ICX code.

## How it maps to Dewey

| Dewey Decimal | ICX |
|---|---|
| 10 main classes | 10 top classes |
| Divisions, sections, decimals | Dot segments; each one narrows its parent |
| Cutter (author) number | The message's own client/account field. The client is never repeated inside the code. |
| Standard subdivisions (Table 1) | Facets after `~`, valid on any class |
| Geographic table (Table 2) | `~st.xx` jurisdiction facet |
| Relative index | `index.yaml`: plain-English term → code |
| Call number on the spine | The `object` field of a message header |
| Shelf order | Sort order for logs and digests |
| Weeding and relocation | Tombstones: `old → new`; codes are never reused |

## Rules

1. Segments are lower case, 1–6 characters, letters and digits, starting with a letter. Max depth 4, plus facets.
2. Every prefix of a code is itself a valid code. Shortening a code widens it; it never breaks it.
3. Account or client identity lives in its own message field, never in the code. Each part carries only what the other missed.
4. Route by prefix: the owner of the longest registered prefix receives the message unless it is addressed explicitly.
5. Exact values (IDs, amounts, dates, phone numbers, paths) are never encoded. Carry them verbatim as `key=value`.
6. Append-only. A retired code becomes a tombstone pointing to its replacement and is never reused.
7. Unknown concept: send it as `x.<word>`. The editor places it in the tree on the next review.
8. One editor (a person or a single designated agent) owns the codebook and index.

## Example message (ICOM header)

Plain English:
> Please change the inbound intake configuration for client ACME; the next step is the platform owner reviewing the worker.

With ICX as the object:
```
@ENG>ICOM>OPS P1 ACME #T-1182 ASK pr.voice.intake~cfg nx=PLATFORM:sys.wkr
```

ICX is about precision and routing, not compression. Word segments cost about the same tokens as numbers (`voice.intake.book` is 4 tokens and `2.31.4` is 5 in `o200k_base`), and words can be read without opening the codebook.

## Files

| Path | What |
|---|---|
| `examples/codebook.yaml` | Firm codebook: classes, codes, facets, tombstones |
| `examples/index.yaml` | Firm relative index: plain-English term to code |
| `tools/icx_validate.py` | Validator: segment rules, depth, parent prefixes, facets, tombstones, index targets |

```bash
pip install pyyaml
python3 tools/icx_validate.py examples/codebook.yaml examples/index.yaml
```

## This fork (Laputka Law Office)

The files in `examples/` are the firm's book. The ten class codes are unchanged. The generic sample that shipped upstream was not a live firm codebook, so those sample codes are replaced here rather than tombstoned.

**Editor.** ICOM (`ICOM`) owns edits to the codebook and the index.
**Approver.** Maria G (`MARIAG`), chief of staff, approves changes. She is the only assistant who speaks with the attorney.

An owner id on a code is a routing address: uppercase, and six characters or fewer. Topic segments stay lowercase.

| Id | Who |
|---|---|
| MARIAG | Maria G, chief of staff, codebook approver |
| JEN | Jen, calendar and email |
| GAUNT | Gauntlet, accuracy and completeness review |
| POLL | Inbox Puller, attorney Outlook inbox |
| ICOM | Internal router and codebook editor |
| CTO | Technology |
| TEAMS | Microsoft Teams |
| CFO | Finance lead |
| CLAP, CILL, CCLR, CWAT, CMON, CPRE, CBIG, CFUN, CWES | Comptrollers, one per company, reporting to CFO. The company name stays out of the code. |
| TRAV | Travel lead |
| FLT, HTL, CAR, REST, TRV | Flight, hotel, rental car, restaurant, travel review |

Clients, matters, and companies stay in their own message fields. Exact values (dates, amounts, phone numbers, IDs) stay `key=value`. A topic that has no code yet is sent as `x.<word>` until ICOM files it and Maria G approves. No payment moves without attorney approval (`fin.paybl.apprv`), which Maria G carries.

## Credits

ICX is an original design by Iron Noodle (2026). It draws on:

- **Dewey Decimal Classification** (Melvil Dewey, 1876): the class hierarchy, author numbers, standard subdivisions and relative index.
- **Next Concept Modeling (NCM)**, Stanford preprint, October 2026: coarse-to-fine generation where each level carries only what the level above missed.

ICX is not affiliated with OCLC, the Dewey Decimal Classification, or the NCM authors.

## License

MIT
