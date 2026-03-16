# R4SUB — R for Regulatory Submission

**A quantitative framework to assess clinical data readiness for regulatory submission.**

---

## What is R4SUB?

R4SUB answers a single question in a reproducible, regulator-aligned way:

> **Is this clinical data package ready for regulatory submission?**

The framework transforms fragmented validation outputs, metadata checks, and analysis diagnostics into a unified, explainable, quantitative readiness signal — the **Submission Confidence Index (SCI)**.

R4SUB is not a replacement for validation tools. It is a **readiness layer** that builds on their outputs.

---

## Package Ecosystem

9 modular R packages — each independently testable, documented, and published.

| Package | Purpose | CRAN | Docs |
|---|---|---|---|
| [**r4subcore**](https://github.com/R4SUB/r4subcore) | Evidence schema, parsers, scoring primitives | [![CRAN](https://www.r-pkg.org/badges/version/r4subcore)](https://CRAN.R-project.org/package=r4subcore) | [site](https://r4sub.github.io/r4subcore/) |
| [**r4subtrace**](https://github.com/R4SUB/r4subtrace) | ADaM↔SDTM traceability engine | [![CRAN](https://www.r-pkg.org/badges/version/r4subtrace)](https://CRAN.R-project.org/package=r4subtrace) | [site](https://r4sub.github.io/r4subtrace/) |
| [**r4subscore**](https://github.com/R4SUB/r4subscore) | SCI scoring engine | [![CRAN](https://www.r-pkg.org/badges/version/r4subscore)](https://CRAN.R-project.org/package=r4subscore) | [site](https://r4sub.github.io/r4subscore/) |
| [**r4subrisk**](https://github.com/R4SUB/r4subrisk) | FMEA-based risk quantification | [![CRAN](https://www.r-pkg.org/badges/version/r4subrisk)](https://CRAN.R-project.org/package=r4subrisk) | [site](https://r4sub.github.io/r4subrisk/) |
| [**r4subprofile**](https://github.com/R4SUB/r4subprofile) | Regulatory authority profiles (FDA, EMA, PMDA, MHRA, HC, TGA) | [![CRAN](https://www.r-pkg.org/badges/version/r4subprofile)](https://CRAN.R-project.org/package=r4subprofile) | [site](https://r4sub.github.io/r4subprofile/) |
| [**r4subusability**](https://github.com/R4SUB/r4subusability) | Reviewer usability indicators | [![CRAN](https://www.r-pkg.org/badges/version/r4subusability)](https://CRAN.R-project.org/package=r4subusability) | [site](https://r4sub.github.io/r4subusability/) |
| [**r4subdata**](https://github.com/R4SUB/r4subdata) | Synthetic example datasets | [![CRAN](https://www.r-pkg.org/badges/version/r4subdata)](https://CRAN.R-project.org/package=r4subdata) | [site](https://r4sub.github.io/r4subdata/) |
| [**r4subui**](https://github.com/R4SUB/r4subui) | Interactive Shiny dashboard | [![CRAN](https://www.r-pkg.org/badges/version/r4subui)](https://CRAN.R-project.org/package=r4subui) | [site](https://r4sub.github.io/r4subui/) |
| [**r4sub**](https://github.com/R4SUB/r4sub) | Meta-package — one install loads the full ecosystem | [![CRAN](https://www.r-pkg.org/badges/version/r4sub)](https://CRAN.R-project.org/package=r4sub) | [site](https://r4sub.github.io/r4sub/) |

### Install

```r
install.packages("r4sub")   # installs and attaches the full ecosystem
```

---

## Four Evaluation Pillars

R4SUB measures submission readiness across four orthogonal dimensions:

| Pillar | Package | What It Measures |
|---|---|---|
| **Quality** | r4subcore | CDISC compliance, controlled terminology, Define-XML integrity, validation severity |
| **Traceability** | r4subtrace | SDTM→ADaM derivation lineage, mapping completeness, orphan variables |
| **Risk** | r4subrisk | FMEA probability × impact × detectability, RPN bands, mitigation tracking |
| **Usability** | r4subusability | Variable label quality, Define-XML completeness, annotation coverage, reviewer guide |

---

## Submission Confidence Index (SCI)

The SCI is a weighted composite score (0–100) across all four pillars, calibrated per regulatory authority.

| SCI | Band | Interpretation |
|---|---|---|
| 85–100 | `ready` | Data package meets regulatory expectations |
| 70–84 | `minor_gaps` | Minor issues; proceed with documented remediation |
| 50–69 | `conditional` | Significant gaps; remediation required before submission |
| 0–49 | `high_risk` | Major deficiencies; comprehensive review needed |

The SCI is fully decomposable — every score traces back to concrete evidence rows.

---

## Regulatory Profiles

`r4subprofile` calibrates SCI weights and required indicators per authority:

| Authority | Region | Submission Types |
|---|---|---|
| FDA | United States | IND, NDA, BLA, ANDA, 505b2 |
| EMA | European Union | CTA, MAA, variation |
| PMDA | Japan | CTN, NDA_JP |
| Health Canada | Canada | CTA_CA, NDS |
| TGA | Australia | CTN_AU, registration |
| MHRA | United Kingdom | CTA_UK, MAA_UK |

---

## Architecture

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

---

## Quick Start

```r
library(r4sub)

# Load example data
data(evidence_pharma)          # from r4subdata

# Score
scores  <- compute_indicator_scores(evidence_pharma)
pillars <- compute_pillar_scores(evidence_pharma)
sci     <- compute_sci(pillars)
sci$SCI   # 0–100
sci$band  # "ready" / "minor_gaps" / "conditional" / "high_risk"

# Apply an authority profile
prof <- submission_profile("FDA", "NDA")
val  <- validate_against_profile(evidence_pharma, prof)
val$is_compliant
val$missing_indicators

# Launch the dashboard
r4sub_app(evidence = evidence_pharma)
```

---

## Current Status (March 2026)

| Area | Status |
|---|---|
| Package architecture | Complete |
| CRAN submission | 6 of 9 packages on CRAN (r4subusability, r4subui, r4sub in review) |
| CI / R-CMD-check | Passing on all packages |
| pkgdown documentation sites | Live for all 9 packages |
| Vignettes | One per package |
| Regulatory profiles | 6 authorities implemented |
| Example datasets | 8 synthetic datasets (pharma + oncology) |
| End-to-end demos | Not yet — highest priority gap |
| Shiny dashboard screenshots | Not yet |
| PHUSE / CDISC outreach | Not yet |
| Community contributors | Not yet |

---

## Design Principles

| Principle | Description |
|---|---|
| **Regulator-aligned** | FDA, EMA, PMDA expectations encoded as measurable indicators |
| **Quantitative** | Weighted scoring beyond binary pass/fail |
| **Explainable** | Every score traces to concrete evidence |
| **Modular** | Independent, composable R packages |
| **Human-in-the-loop** | Augments expert judgment; does not replace it |
| **Open source** | MIT license, vendor-neutral, no real patient data |

---

## Intended Audience

Clinical Programmers · Biostatisticians · Regulatory Data Standards Teams · Quality Assurance · Submission Operations

---

## Contributing

We welcome contributions:

- New readiness indicators and scoring rules
- Additional regulatory authority profiles
- Traceability parsers for new source formats
- End-to-end workflow examples and demos
- Bug reports and feature requests

Open an issue or discussion in the relevant repository.

---

> **R4SUB — Because compliance is not the same as readiness.**
