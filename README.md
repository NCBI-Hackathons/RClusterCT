
# clustifyr <img src="man/figures/logo.png" align="right">

[![Build
Status](https://travis-ci.org/rnabioco/clustifyr.svg?branch=master)](https://travis-ci.org/rnabioco/clustifyr)
[![AppVeyor build
status](https://ci.appveyor.com/api/projects/status/github/rnabioco/clustifyr?branch=master&svg=true)](https://ci.appveyor.com/project/rnabioco/clustifyr)
[![Coverage
status](https://codecov.io/gh/rnabioco/clustifyr/branch/master/graph/badge.svg)](https://codecov.io/github/rnabioco/clustifyr?branch=master)

clustifyr classifies cells and clusters in single-cell RNA sequencing
experiments using reference bulk RNA-seq data sets, sorted microarray
expression data, single-cell gene signatures, or marker genes.

Single cell transcriptomes are difficult to annotate without extensive
knowledge of the underlying biology of the system in question. Even with
this knowledge, accurate identification can be challenging due to the
lack of detectable expression of common marker genes. ClustifyR solves
this problem by automatically annotating single cells or clusters using
scRNA-seq, bulk RNA-seq data, microarray or marker gene lists.
Additional functions allow for exploratory analysis of similarities
between single cell RNA-seq datasets and reference data.

## Installation

``` r
# install.packages("devtools")
devtools::install_github("rnabioco/clustifyr")
```

## Example usage

In this example we use the following built-in input data:

  - an expression matrix of single cell RNA-seq data (`pbmc4k_matrix`)
  - a metadata data.frame (`pbmc4k_meta`)
  - a vector of variable genes (`pbmc4k_vargenes`)-
  - a matrix of bulk RNA-seq read counts (`pbmc_bulk_matrix`):

We then calculate correlation coefficients and plot them on a
pre-calculated tSNE projection (stored in `pbmc4k_meta`).

``` r
library(clustifyr)
full_pbmc4k_matrix <- clustifyrdata::pbmc4k_matrix
full_pbmc4k_meta <- clustifyrdata::pbmc4k_meta

res <- clustify(
  input = full_pbmc4k_matrix,
  metadata = full_pbmc4k_meta$cluster,
  ref_mat = pbmc_bulk_matrix,
  query_genes = pbmc4k_vargenes
)

plot_cor(
  res,
  full_pbmc4k_meta,
  colnames(res)[1],
  cluster_col = "cluster"
)
#> [[1]]
```

![](man/figures/example-1.png)<!-- -->

``` r

plot_best_call(res, full_pbmc4k_meta)
```

![](man/figures/example-2.png)<!-- -->

Alternatively, `clustify` can take a clustered `seurat` object (both v2
and v3) and assign identities. New reference matrix can be made directly
from `seurat` object as well.

``` r
res <- clustify(
  input = s_small,
  cluster_col = "res.1",
  ref_mat = pbmc_bulk_matrix,
  seurat_out = T
)

new_ref_matrix <- use_seurat_comp(
  s_small,
  cluster_col = "res.1",
  var.genes_only = FALSE
)
```

Similarly, `clustify_lists` can also handle identity assignment of
matrix or `seurat` object based on marker gene lists.

``` r
res <- clustify_lists(
  pbmc4k_matrix,
  per_cell = FALSE,
  cluster_info = pbmc4k_meta,
  cluster_col = "cluster",
  marker = pbmc4k_markers,
  marker_inmatrix = FALSE,
  metric = "hyper"
)

res <- clustify_lists(
  s_small,
  per_cell = FALSE,
  marker = pbmc4k_markers,
  marker_inmatrix = FALSE,
  cluster_col = "res.1",
  seurat_out = TRUE
)
```

## Additional reference data

More reference data (including tabula muris, immgen, etc) is available
at <https://github.com/rnabioco/clustifyrdata>.
