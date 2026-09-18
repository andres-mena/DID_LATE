# Replication Package

This directory contains the scripts and frozen exhibits used by the DID_LATE
paper. The scripts are kept separate from the paper source so the manuscript
folder stays easy to compile and review.

## Directory Layout

```text
replication/
|-- README.md
|-- sim/                  # Monte Carlo simulations
|-- empirical/            # INPRES application
|-- shared/               # estimator implementations used by both pipelines
|-- paper_tables/         # frozen .tex files from the paper
`-- paper_figures/        # frozen .pdf files from the paper
```

## Paper Output Map

| Paper exhibit | Frozen file | Producer script |
|---|---|---|
| Main simulation results | `paper_tables/sim_main.tex` | `sim/04_tables.R` |
| ML-learner comparison | `paper_tables/sim_ml_comparison.tex` | `sim/07_ml_comparison_table.R` |
| Trimming-rule comparison | `paper_tables/sim_trim_rule_summary.tex` | `sim/trim_rule_summary.R` |
| DML-Wald trimming figure | `paper_figures/sim_trimming_wald.pdf` | `sim/06_trimming_analysis.R` |
| INPRES returns table | `paper_tables/empirical_duflo.tex` | `empirical/04_tables.R` |
| Balance table | `paper_tables/empirical_balance.tex` | `empirical/06_balance_table.R` |
| Four-learner robustness | `paper_tables/empirical_4learner.tex` | `empirical/four_learner_table.R` |
| Classifier comparison | `paper_tables/empirical_classifier.tex` | static table |
| Lasso propensity figure | `paper_figures/empirical_propensity_lasso.pdf` | `empirical/07_figures_appendix.R` |
| Ridge propensity figure | `paper_figures/empirical_propensity_ridge.pdf` | `empirical/07_figures_appendix.R` |
| Primary-margin sensitivity | `paper_figures/empirical_sensitivity_primary.pdf` | `empirical/08_figures_sensitivity.R` |
| High-school sensitivity | `paper_figures/empirical_sensitivity_highschool.pdf` | `empirical/08_figures_sensitivity.R` |

## Running From Repo Root

Simulation scripts:

```bash
Rscript replication/sim/01_dgp.R
Rscript replication/sim/02_nuisance.R --method lasso
Rscript replication/sim/03_estimators.R
Rscript replication/sim/04_tables.R
Rscript replication/sim/05_figures.R
Rscript replication/sim/06_trimming_analysis.R
Rscript replication/sim/07_ml_comparison_table.R
Rscript replication/sim/trim_rule_summary.R
```

Empirical scripts:

```bash
Rscript replication/empirical/01_data_duflo.R
Rscript replication/empirical/02_nuisance.R --duflo
Rscript replication/empirical/03_estimators.R --duflo
Rscript replication/empirical/04_tables.R
Rscript replication/empirical/06_balance_table.R
Rscript replication/empirical/07_figures_appendix.R
Rscript replication/empirical/08_figures_sensitivity.R
```

The simulation pipeline needs no external data. The empirical pipeline expects
`data/raw/inpresdata.dta`, the public Duflo/Roodman SUPAS extract from
`github.com/droodman/Duflo-2001/tree/master/data`.

The expensive stages are simulation DGP generation and nuisance estimation.
Frozen paper tables and figures are committed so the paper can be compiled
without rerunning the full pipeline.
