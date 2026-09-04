# R4SUB - Ready for Submission

A quantitative framework for asking one deceptively simple question about a clinical data package: *is it actually ready to submit?*

## The problem this solves

Validation tools tell you what's wrong. Pinnacle 21, define.xml checks, CDISC conformance rules, analysis diagnostics - they all produce findings. But a stack of findings isn't an answer. Someone still has to look across all of it and make a judgment call: are we ready, or aren't we?

That judgment usually lives in people's heads. It's hard to reproduce, hard to defend, and it changes depending on who's asking. R4SUB tries to make it explicit.

It takes the scattered outputs from your existing validation and metadata checks and rolls them into a single, explainable number: the **Submission Confidence Index (SCI)**, scored 0–100. Every point in that score traces back to a concrete piece of evidence, so you can always answer "why."

One thing worth being clear about: R4SUB is not a validator and doesn't try to be. It's a layer that sits on top of the tools you already run.

## The packages

R4SUB is nine R packages rather than one monolith. Each is independently tested, documented, and published, so you can pull in only what you need - or grab everything at once through the meta-package.

| Package | What it does | CRAN | Docs |
|---|---|---|---|
| [r4subcore](https://github.com/R4SUB/r4subcore) | Evidence schema, parsers, scoring primitives | [![CRAN](https://www.r-pkg.org/badges/version/r4subcore)](https://CRAN.R-project.org/package=r4subcore) | [site](https://r4sub.github.io/r4subcore/) |
| [r4subtrace](https://github.com/R4SUB/r4subtrace) | ADaM ↔ SDTM traceability engine | [![CRAN](https://www.r-pkg.org/badges/version/r4subtrace)](https://CRAN.R-project.org/package=r4subtrace) | [site](https://r4sub.github.io/r4subtrace/) |
| [r4subscore](https://github.com/R4SUB/r4subscore) | SCI scoring engine | [![CRAN](https://www.r-pkg.org/badges/version/r4subscore)](https://CRAN.R-project.org/package=r4subscore) | [site](https://r4sub.github.io/r4subscore/) |
| [r4subrisk](https://github.com/R4SUB/r4subrisk) | FMEA-based risk quantification | [![CRAN](https://www.r-pkg.org/badges/version/r4subrisk)](https://CRAN.R-project.org/package=r4subrisk) | [site](https://r4sub.github.io/r4subrisk/) |
| [r4subprofile](https://github.com/R4SUB/r4subprofile) | Regulatory authority profiles (FDA, EMA, PMDA, MHRA, HC, TGA) | [![CRAN](https://www.r-pkg.org/badges/version/r4subprofile)](https://CRAN.R-project.org/package=r4subprofile) | [site](https://r4sub.github.io/r4subprofile/) |
| [r4subusability](https://github.com/R4SUB/r4subusability) | Reviewer usability indicators | [![CRAN](https://www.r-pkg.org/badges/version/r4subusability)](https://CRAN.R-project.org/package=r4subusability) | [site](https://r4sub.github.io/r4subusability/) |
| [r4subdata](https://github.com/R4SUB/r4subdata) | Synthetic example datasets | [![CRAN](https://www.r-pkg.org/badges/version/r4subdata)](https://CRAN.R-project.org/package=r4subdata) | [site](https://r4sub.github.io/r4subdata/) |
| [r4subui](https://github.com/R4SUB/r4subui) | Interactive Shiny dashboard | [![CRAN](https://www.r-pkg.org/badges/version/r4subui)](https://CRAN.R-project.org/package=r4subui) | [site](https://r4sub.github.io/r4subui/) |
| [r4sub](https://github.com/R4SUB/r4sub) | Meta-package: one install pulls in the whole ecosystem | [![CRAN](https://www.r-pkg.org/badges/version/r4sub)](https://CRAN.R-project.org/package=r4sub) | [site](https://r4sub.github.io/r4sub/) |

The easiest way in:

```r
install.packages("r4sub")   # installs and attaches everything
```

## How readiness gets measured

Readiness isn't one thing, so R4SUB breaks it into four pillars that don't overlap much in practice:

- **Quality** (`r4subcore`) - CDISC compliance, controlled terminology, Define-XML integrity, and how severe the validation findings are.
- **Traceability** (`r4subtrace`) - whether SDTM→ADaM derivations actually line up: mapping completeness, derivation lineage, orphan variables.
- **Risk** (`r4subrisk`) - an FMEA view (probability × impact × detectability), grouped into RPN bands, with mitigation tracking.
- **Usability** (`r4subusability`) - the stuff a reviewer notices: variable label quality, Define-XML completeness, annotation coverage, the reviewer guide.

Each pillar produces its own score. The SCI combines them.

## Reading the SCI

The SCI is a weighted composite from 0 to 100. The weights aren't fixed - they're calibrated to whichever regulatory authority you're targeting. The band tells you roughly where you stand:

| SCI | Band | What it means |
|---|---|---|
| 85–100 | `ready` | Meets regulatory expectations |
| 70–84 | `minor_gaps` | Small issues; fine to proceed with documented remediation |
| 50–69 | `conditional` | Real gaps; fix them before you submit |
| 0–49 | `high_risk` | Major deficiencies; needs a full review |

Because the score is fully decomposable, you can always drill from a number down to the specific evidence rows behind it. No black boxes.

## Regulatory profiles

Different authorities care about different things and weight them differently. `r4subprofile` encodes that so the SCI means the same thing regardless of where you're filing.

| Authority | Region | Submission types |
|---|---|---|
| FDA | United States | IND, NDA, BLA, ANDA, 505b2 |
| EMA | European Union | CTA, MAA, variation |
| PMDA | Japan | CTN, NDA_JP |
| Health Canada | Canada | CTA_CA, NDS |
| TGA | Australia | CTN_AU, registration |
| MHRA | United Kingdom | CTA_UK, MAA_UK |

## How it fits together

```
Clinical Data Assets (SDTM, ADaM, TLFs, Define.xml)
                         │
                         ▼
              ┌──────────────────────┐
              │     r4subcore        │  Evidence schema + parsers
              └──────────┬───────────┘
          ┌──────────────┼──────────────┬──────────────┐
          ▼              ▼              ▼              ▼
    r4subtrace      r4subrisk     r4subscore    r4subusability
    Traceability    Risk FMEA     SCI Engine    Usability checks
          └──────────────┴──────────────┴──────────────┘
                         │
                         ▼
              ┌──────────────────────┐
              │    r4subprofile      │  Authority calibration
              └──────────┬───────────┘
                         │
                         ▼
              ┌──────────────────────┐
              │      r4subui         │  Shiny dashboard
              └──────────────────────┘
```

## A quick example

```r
library(r4sub)

# Grab some example data (ships with r4subdata)
data(evidence_pharma)

# Score it
scores  <- compute_indicator_scores(evidence_pharma)
pillars <- compute_pillar_scores(evidence_pharma)
sci     <- compute_sci(pillars)

sci$SCI    # the 0–100 number
sci$band   # "ready" / "minor_gaps" / "conditional" / "high_risk"

# Now check it against a specific authority
prof <- submission_profile("FDA", "NDA")
val  <- validate_against_profile(evidence_pharma, prof)

val$is_compliant
val$missing_indicators

# Or just open the dashboard
r4sub_app(evidence = evidence_pharma)
```

## Where things stand (March 2026)

Being honest about what's done and what isn't:

| Area | Status |
|---|---|
| Package architecture | Done |
| CRAN | 6 of 9 published; r4subusability, r4subui, and r4sub still in review |
| CI / R CMD check | Passing everywhere |
| pkgdown sites | Live for all 9 packages |
| Vignettes | One per package |
| Regulatory profiles | 6 authorities implemented |
| Example datasets | 8 synthetic datasets (pharma + oncology) |
| End-to-end demos | Not yet - this is the biggest gap right now |
| Dashboard screenshots | Not yet |
| PHUSE / CDISC outreach | Not yet |
| Outside contributors | Not yet |

If you're looking for a good place to jump in, the end-to-end demos are where help would matter most.

## What guides the design

A few principles the whole project tries to stick to:

- **Regulator-aligned.** FDA, EMA, and PMDA expectations are encoded as things you can actually measure, not vibes.
- **Quantitative, not binary.** Weighted scoring instead of a single pass/fail stamp.
- **Explainable.** Every score points back to real evidence.
- **Modular.** Small, composable packages you can adopt piecemeal.
- **Human-in-the-loop.** This supports expert judgment; it doesn't try to replace it.
- **Open.** MIT-licensed, vendor-neutral, and no real patient data anywhere.

## Who it's for

Clinical programmers, biostatisticians, regulatory data standards teams, QA, and submission operations - basically anyone who has to sign off on a package before it goes out the door.

## Contributing

Contributions are genuinely welcome. Some things that would help:

- New readiness indicators and scoring rules
- More regulatory authority profiles
- Traceability parsers for source formats we don't handle yet
- End-to-end workflow examples and demos
- Bug reports and feature requests

Open an issue or start a discussion in the relevant repo and we'll take it from there.

## About

R4SUB is developed and maintained as part of the open-source work of
[TechWorksLab](https://techworkslab.com) - clinical SAS & R programming, CDISC
SDTM/ADaM, and defensible FDA/EMA submissions for sponsors and CROs.
Maintainer: Pawan Rama Mali.

---

*R4SUB exists because passing validation and being ready to submit are not the same thing.*
