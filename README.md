# DID_LATE

This repository contains the paper source, frozen exhibits, and replication notes for:

> Double Debiased Machine Learning for Difference-in-Differences under Imperfect Compliance
> 
> **Abstract:**
This paper develops debiased machine learning estimators for difference-in-differences designs with imperfect compliance. I derive closed-form orthogonal scores for the Wald-DID and time-corrected estimands of Chaisemartin and D'Haultfoeuille (2018) and construct cross-fitted GMM estimators that are $\sqrt{n}$-consistent and asymptotically normal under standard rate conditions on the nuisance functions. I also propose a data-driven trimming rule that targets a sample analog of the estimator's asymptotic variance, excludes only observations whose probability of belonging to the treated group is close to one, and does not require knowledge of the treatment effect. Simulations suggest that the estimators achieve nominal coverage at moderate sample sizes. An application to the INPRES school construction program illustrates the method.


The R package lives separately at:

```text
https://github.com/andres-mena/didml
```

Use this repository to read and build the paper. Use the `didml` repository to install the software.

## Current Status

- The fuzzy DID setup follows de Chaisemartin and D'Haultfoeuille (2018).
- The Wald-DID and time-corrected estimands are written as unconditional moment restrictions.
- Closed-form first-step influence function corrections produce Neyman-orthogonal scores.
- Cross-fitted GMM estimators, DML-Wald and DML-TC, are implemented in the separate `didml` package.
- The paper source compiles from this repository with frozen tables and figures.

AI evaluation of the project:

- Theory: satisfactory enough for detailed coauthor review. The orthogonal-score construction, equivalence to the CDH efficient influence functions, and rate statement are the backbone.
- Simulations: useful and directionally reassuring, but still need a final audit of which DGPs belong in the paper.
- Empirical application: good as an illustration. The INPRES/Duflo application is also supported by the external `didml` package.
- Software: lives in `andres-mena/didml`, which is public and installable. This repo only references it.

## What Is Solved

1. Identification target: switcher LATE in a fuzzy 2x2 DID repeated cross-section.
2. Estimator construction: locally robust scores for Wald-DID and time-corrected fuzzy DID.
3. Orthogonality: first-step influence function corrections remove the leading regularization bias from ML nuisance estimation.
4. Inference: asymptotic normality under product-rate conditions, with analytical and clustered standard errors handled in the package.
5. Trimming: a one-sided, data-driven propensity trimming rule for the Wald score, motivated by Riesz-weight variance.
6. Paper package split: manuscript and exhibits here; installable package in `andres-mena/didml`.

## Open Questions

- **Positioning**: should the paper sell itself primarily an the DML extension of CDH2017, or extend it as a general DID-LATE paper?. More boradly, can it also be seen as a general recipe for orthogonalizing ratio estimands?
- **Trimming**: the one-sided rule is empirically useful, but the current formal statement still has a conjectural flavor for the estimated trimming measure. Need more theory on that or even removing it completely to make the main argument more clear.
- **TC vs Wald vs alternative**: DML-TC is often the more stable operational estimator when the first stage is weak. However, the identification assumptions are contestable and the explanability of the estimand it's too obscure. The paper should decide how strongly to recommend it.
- **Empirical application**: INPRES is clean and canonical in Dufflo and CDH2017, works fine to make an argument for the TC estimator but we should decide whether it is good for high-dimensional covariates which is the main contribution of this estimator.
- **Multi-period extension**: the package interface anticipates it, but the paper currently focuses on the 2x2 case.
- **Panel Data**: the current version covers repeated cross section only
- **Cluster dependence**: the paper currently introduce a basic correction into the variance estimator accounting for cluster dependance, but no guide for sampling during cross-fitting is implemented. It's a problem that deserves carefull attention.

## Notation

The paper uses repeated cross-sections with observations

```text
W_i = (Y_i, D_i, G_i, T_i, X_i).
```

Main objects:

- `Y`: outcome.
- `D`: treatment received.
- `G`: treatment group indicator.
- `T`: post-period indicator.
- `X`: covariates, possibly high-dimensional.
- `S`: switcher event, with `D_0 < D_1` in the treatment group.
- `Delta`: LATE for treatment-group switchers.

Conditional means:

```text
m^R_gt(x) = E[R | G = g, T = t, X = x],  R in {Y, D}.
DID_R(X) = R - m^R_10(X) - m^R_01(X) + m^R_00(X).
```

The Wald-DID target is

```text
W^DID = E[DID_Y(X) | G = 1, T = 1] /
        E[DID_D(X) | G = 1, T = 1].
```

The time-corrected target, let $\delta_d(X) = E[Y \mid D=d, G=0, T=1, X] - E[Y \mid D=d, G=0, T=0, X]$ be the trend in the G=0 group, for each for the treatment status D=d:

```text
W^TC= E[Y - m^Y_{10}(X) - m^D_{10}(X)\delta_1(X) - (1-m^D_{10}(X))\delta_0(X)\mid G=1, T=1]/
E[D - m^D_{10}(X)\mid G=1, T=1]
```
The TC numerator subtracts a counterfactual outcome that combines two control-group time trends: $\delta_1(X)$ for units with baseline treatment status $D(0)=1$ and $\delta_0(X)$ for units with $D(0)=0$, weighted by the baseline treated share $m^D_{10}(X)$. The denominator is the corresponding adjusted change in treatment take-up.


The orthogonal score has the form

```text
psi(W, gamma, alpha, theta) =
  g(W, gamma, theta) + alpha(W)'(R - gamma(X)).
```

Here `g` is the identifying moment, `gamma` is the vector of nuisance functions, and `alpha` is the vector of Riesz representers from the first-step influence function calculation.

## Repository Layout

```text
.
|-- paper/             # latest LaTeX source and PDF snapshot
|-- output/tables/     # frozen tables referenced by paper/main.tex
|-- output/figures/    # frozen figures referenced by paper/main.tex
|-- replication/       # paper replication scripts and frozen package-style exhibits
|-- notes/             # coauthor notes and open questions
|-- LICENSE            # MIT license for repository materials
`-- README.md          # this coauthor-facing overview
```

The latest shareable PDF snapshot is:

```text
paper/DID_LATE_latest.pdf
```

The paper source compiles from `paper/main.tex`. The LaTeX source expects frozen tables and figures in `../output/`.

## Installing the Package

The R package is `didml` and lives in a separate public repository:

```text
https://github.com/andres-mena/didml
```

Install it with:

```r
install.packages("remotes")
remotes::install_github("andres-mena/didml")
```

Example:

```r
library(didml)

data(duflo)

fit <- didml(
  Y = duflo$Y,
  D = duflo$D,
  G = duflo$G,
  Ti = duflo$Ti,
  X = duflo$X,
  iv = TRUE,
  dml = TRUE,
  estimator = "both",
  method = "lasso",
  K = 5,
  cluster = duflo$cluster
)

summary(fit)
```

## Reproducing Paper Assets

Frozen paper outputs are committed so the paper can compile immediately. Full simulations are intentionally not stored because the raw Monte Carlo output is large and regenerated locally.

Replication scripts live under:

```text
replication/
```

Compile the paper:

```bash
cd paper
latexmk -pdf main.tex
```

## License

MIT.
