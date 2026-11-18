# Project Proposal: Permitless Concealed Carry and Firearm Mortality

**To: BST260 Teaching Team, Fall 2026**  
**From: Jingyi Wang**  
**Repository:** <https://github.com/JingyiWang6/finalproject>  
**Project website:** <https://jingyiwang6.github.io/finalproject/>

## Research question and objectives

**Among the 50 U.S. states during 2014–2024, is allowing permitless
concealed carry associated with changes in the annual age-adjusted
firearm mortality rate?**

I will combine the course-provided RAND firearm legislation data
(`gunleg`) with CDC firearm mortality data (`cdcfiremort`). My
objectives are to describe when states changed their concealed-carry
policies, summarize geographic and temporal variation in firearm
mortality, and explore whether mortality patterns differ by policy
status. I will investigate the association without assuming its
direction or interpreting it as a causal effect.

## Data sources, population, and feasibility

The population of interest is residents of the 50 U.S. states. The main
unit of analysis will be a **state–calendar year**; individual
legislation records will be the starting unit for the policy-data
vignette.

| Dataset | Original source and provenance | Role in the project |
|:---|:---|:---|
| `gunleg` | [RAND State Firearm Law Database and supporting documentation](https://www.rand.org/pubs/tools/TLA243-2-v4.html); distributed in the course template and retained in `finalproject` | Identify concealed-carry policy categories, effective dates, supersession dates, and relevant statutory notes |
| `cdcfiremort` | [CDC/NCHS Stats of the States: Firearm Mortality](https://www.cdc.gov/nchs/state-stats/deaths/firearms.html), based on National Vital Statistics System mortality data; distributed in the course template and retained in `finalproject` | Annual state firearm death counts and age-adjusted death rates |

A preliminary structural check of the bundled data found 1,902
legislation records and 612 mortality records. The mortality file
contains 51 jurisdictions for 2005 and each year from 2014 through 2024.
I will restrict the main analysis to the continuous 2014–2024 period and
exclude the District of Columbia, giving at most 550 state–years before
policy-coding exclusions. I will not interpolate the missing 2006–2013
period. These checks establish feasibility; they are not findings about
the research question.

Both datasets are already included among the course examples. I will use
the bundled snapshots rather than silently substitute newer downloads.
The final vignettes will record the package version, repository commit,
file checksums, access date, original source links, and cleaning
decisions. The RAND documentation page describes an evolving database; I
will verify the bundled extract’s version and report any uncertainty
rather than infer its version from the current webpage.

## Variables and data preparation

**Exposure:** a binary indicator of permitless concealed-carry policy,
based on the `Law.Class` category `carrying a concealed weapon (ccw)`
and the `Law.Class.Subtype` label `shall issue (permit not required)`. I
will inspect `Content`, `Effective.Date.Note`, effective-date fields,
and supersession fields to check coverage and resolve overlapping
records. This measures the RAND policy category, not unrestricted
firearm carrying by every resident.

For the primary coding, a state–year will be exposed (`1`) only when a
verified qualifying policy covers the entire calendar year. A verified
year before adoption will be coded `0`. Years containing a policy
transition will be excluded from the primary comparison to avoid mixing
exposed and unexposed months. Unresolved histories or ambiguous dates
will be coded missing, not automatically treated as unexposed. Absence
of a qualifying record alone will not establish policy absence. A
sensitivity analysis will use year-end policy status when dates permit
reliable classification.

**Outcome:** `Death.Rate`, a continuous age-adjusted firearm mortality
rate per 100,000 population. `Deaths.` will provide descriptive counts.
I will not divide counts by an age-adjusted rate to infer population
denominators, or describe this outcome as firearm homicide: it covers
firearm deaths across intents.

I will standardize state identifiers, check uniqueness of state–year
keys, and join the two datasets by state and year. Missing or suppressed
outcomes will remain missing. I will tabulate missingness, unmatched
records, and exclusions, without imputing policy histories or mortality
rates.

## Two planned analysis vignettes

### 1. Concealed-carry policy changes across states

`vignettes/policy-history.qmd` will analyze `gunleg`. It will document
the source and coding rules, summarize law classes and date
completeness, and describe variation in concealed-carry policy
histories. Planned outputs include a table of adoption years, annual
numbers of states in each verified policy category, and a state-by-year
policy timeline. Reusable R functions will convert validated policy
histories into state–year indicators and flag uncertain records. This
vignette will explain what the legislation data can and cannot measure.

### 2. Firearm mortality trends and policy associations

`vignettes/firearm-mortality.qmd` will analyze `cdcfiremort` and link it
to the policy indicators. It will report mortality distributions,
state-specific changes, and the number of observed state–years by
exposure group. Planned figures include state trend lines, a
state-by-year mortality heatmap, and descriptive comparisons by policy
status. Unweighted summaries across states will be labeled as
state-level summaries, not national population mortality rates.

The main results will be descriptive. If the verified panel has
sufficient within-state policy changes and usable observations, I will
also fit an exploratory linear model of mortality rate on policy status
with state and calendar-year fixed effects. I will report the
coefficient in deaths per 100,000, uncertainty intervals with standard
errors clustered by state, and the number of states and transitions
contributing to the estimate. If the data do not support that model, I
will retain the descriptive analysis and explain why.

## Confounding, sensitivity analyses, and limitations

Before examining exposure–outcome associations, I identify longstanding
state differences and common calendar-year shocks as adjustment
priorities. State fixed effects will account for time-invariant state
characteristics; year fixed effects will account for shared annual
changes. Age adjustment is already part of the CDC outcome. Poverty,
urbanization, firearm ownership, other concurrent policies, and
state-specific pandemic impacts are plausible time-varying confounders
not adequately measured in these two datasets. I will explicitly
identify them as unadjusted limitations rather than claim that fixed
effects eliminate confounding.

Sensitivity analyses will compare full-year and year-end exposure
coding, omit 2020–2021, and examine whether conclusions depend heavily
on any one state. I will distinguish planned analyses from any
exploratory additions and report unstable or null associations.

Policy adoption is not random. Staggered adoption, potentially different
effects across states and time, short follow-up for recent adopters,
policy exceptions, and uncertain dates limit interpretation of a pooled
fixed-effects coefficient. Overall firearm mortality also combines
different intents, potentially masking different patterns. These are
aggregate data, so state-level associations cannot establish individual
risk or a policy’s causal effect. Findings and limitations will be
reported together in each analysis vignette.

## Deliverables and workflow

The final deliverable will remain a valid R package and a public pkgdown
website. In addition to this proposal, it will contain the two
substantive analysis vignettes, documented helper functions,
reproducible tables and figures, and R session information. I will first
validate and document the data, then produce descriptive analyses, and
finally assess whether the exploratory model is supportable. The README
will introduce the project and link to its reports. No results will be
presented as established before the analyses are completed.
