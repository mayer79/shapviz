# {shapviz} <a href='https://github.com/ModelOriented/shapviz'><img src='man/figures/logo.png' align="right" height="139" /></a>

<!-- badges: start -->

[![R-CMD-check](https://github.com/ModelOriented/shapviz/actions/workflows/R-CMD-check.yaml/badge.svg)](https://github.com/ModelOriented/shapviz/actions/workflows/R-CMD-check.yaml)
[![Codecov test coverage](https://codecov.io/gh/ModelOriented/shapviz/graph/badge.svg)](https://app.codecov.io/gh/ModelOriented/shapviz?branch=main)
[![CRAN_Status_Badge](https://www.r-pkg.org/badges/version/shapviz)](https://cran.r-project.org/package=shapviz)

[![](https://cranlogs.r-pkg.org/badges/shapviz)](https://cran.r-project.org/package=shapviz) 
[![](https://cranlogs.r-pkg.org/badges/grand-total/shapviz?color=orange)](https://cran.r-project.org/package=shapviz)

<!-- badges: end -->

## Overview

{shapviz} provides typical SHAP plots:

- `sv_importance()`: Importance plot (bar/beeswarm).
- `sv_dependence()` and `sv_dependence2D()`: Dependence plots to study feature effects and interactions.
- `sv_interaction()`: Interaction plot (beeswarm).
- `sv_waterfall()`: Waterfall plot to study single or average predictions.
- `sv_force()`: Force plot as alternative to waterfall plot.

SHAP and feature values are stored in a "shapviz" object that is built from:

1. Models that know how to calculate SHAP values: XGBoost, LightGBM, H2O (boosted trees).
2. SHAP crunchers like {fastshap}, {kernelshap}, {treeshap}, {fastr}, and {DALEX}.
3. SHAP matrix and corresponding feature values.

We use {patchwork} to glue together multiple plots with (potentially) inconsistent x and/or color scale.

## Installation

``` r
# From CRAN
install.packages("shapviz")

# Or the newest version from GitHub:
# install.packages("devtools")
devtools::install_github("ModelOriented/shapviz")
```

## Usage

Shiny diamonds... let's use XGBoost to model their prices by the four "C" variables:

```r
library(shapviz)
library(ggplot2)
library(xgboost)

set.seed(10)

# Build model
x <- c("carat", "cut", "color", "clarity")
dtrain <- xgb.DMatrix(data.matrix(diamonds[x]), label = diamonds$price)
fit <- xgb.train(params = list(learning_rate = 0.1), data = dtrain, nrounds = 65)

# SHAP analysis: X can even contain factors
dia_2000 <- diamonds[sample(nrow(diamonds), 2000), x]
shp <- shapviz(fit, X_pred = data.matrix(dia_2000), X = dia_2000)

sv_importance(shp, show_numbers = TRUE)
sv_importance(shp, kind = "bee")
sv_dependence(shp, v = x)  # patchwork
```

![](man/figures/README-imp.svg)

<img src="man/figures/README-bee.png" alt="beeswarm" width="50%"/>

<img src="man/figures/README-dep.png" alt="scatter" width="90%"/>


Decompositions of individual predictions can be visualized as waterfall or force plot:

```r
sv_waterfall(shp, row_id = 1)
sv_force(shp, row_id = 1)
```

![](man/figures/README-waterfall.svg)

![](man/figures/README-force.svg)

## More to Discover

Check-out the vignettes for topics like:

- Basic use (includes working with other packages and SHAP interactions).
- Multiple models, multi-output models, and subgroup analyses.
- Plotting geographic effects.
- Working with Tidymodels.

## References

[1] Scott M. Lundberg and Su-In Lee. A Unified Approach to Interpreting Model Predictions. Advances in Neural Information Processing Systems 30 (2017).
