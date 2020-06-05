
<!-- README.md is generated from README.Rmd. Please edit that file -->

# Word Vectors in R

<!-- badges: start -->

<!-- badges: end -->

The goal of this repository is to showcase how to pretrain word vectors
and how to acces them in R. This repository will not show you why you
should use each of the methods or how they work. Only how they are being
used within the R language. Examples will be focused on getting word
vectors that will work well with
[textrecipes](https://textrecipes.tidymodels.org/)’s
[step\_word\_embeddings](https://textrecipes.tidymodels.org/reference/step_word_embeddings.html)
step which requires a tibble where the first column contains tokens, and
additional columns should contain embeddings vectors.

For more information about read:

  - [Dive into Deep Learning: Natural Language Processing:
    Pretraining](http://d2l.ai/chapter_natural-language-processing-pretraining/index.html)

## Data and packages

We will be using the
[hcandersenr](https://github.com/EmilHvitfeldt/hcandersenr) package to
provide data for the examples. In addition will be load
[tidyverse](https://www.tidyverse.org/) and
[tokenizers](https://github.com/ropensci/tokenizers). These two packages
are not essencial for this to work but provides a little ease of use.

``` r
library(hcandersenr)
library(tidyverse)
library(tokenizers)
```

We will start by collapsing the text from hcandersenr such that we have
1 fairy tale per string.

``` r
fairy_tales <- hcandersen_en %>%
  nest(data = c(text)) %>%
  mutate(text = map_chr(data, ~ paste(.x$text, collapse = " "))) %>%
  pull(text)

str(fairy_tales)
#>  chr [1:156] "A soldier came marching along the high road: \"Left, right - left, right.\" He had his knapsack on his back, an"| __truncated__ ...
```

Taking a look reveals we have a little under half a million tokens. This
is pretty good but properly on the low end for building good word
vectors.

``` r
fairy_tales %>% 
  count_words() %>% 
  sum()
#> [1] 416311
```

## text2vec

### Package

<https://github.com/bnosac/word2vec>

### Fitting the model

``` r
library(word2vec)

model <- word2vec(x = fairy_tales, type = "cbow", dim = 100, window = 5, iter = 20)
```

### Extraction word vectors

``` r
word_vectors <- as.matrix(model) %>%
  as.data.frame() %>%
  rownames_to_column("tokens") %>%
  as_tibble()

word_vectors
#> # A tibble: 5,345 x 101
#>    tokens      V1     V2      V3      V4     V5      V6     V7     V8      V9
#>    <chr>    <dbl>  <dbl>   <dbl>   <dbl>  <dbl>   <dbl>  <dbl>  <dbl>   <dbl>
#>  1 once    0.507  -1.11  -0.159  -1.48    1.33  -1.14   -0.547  0.884  0.0664
#>  2 so      0.487   0.948  1.75   -0.291  -1.27  -1.22    0.482 -1.07  -0.731 
#>  3 ballo…  0.467   0.183 -0.366   0.674  -0.233  0.259  -0.251  0.205 -0.603 
#>  4 proce…  0.130   0.298 -0.298  -0.489  -0.870 -0.0532 -0.146 -0.364  0.237 
#>  5 Two    -1.28   -0.699  0.649   0.359   0.746  0.0581  0.435 -0.363  0.451 
#>  6 swing   0.334   0.146  0.580  -0.326  -0.153  0.135  -0.154  0.105 -0.377 
#>  7 night  -1.95    0.847  0.0762 -2.44   -1.48   1.07   -1.51   0.634 -1.14  
#>  8 meal   -0.0896 -0.613  0.0918 -0.0634 -0.240 -0.461  -0.255  0.100  0.219 
#>  9 north…  0.204   0.983 -0.589  -0.588  -0.510 -0.122   0.304  0.371  0.220 
#> 10 frien…  0.0249 -0.860  1.03   -0.201  -0.199  0.156  -0.711 -0.741 -0.354 
#> # … with 5,335 more rows, and 91 more variables: V10 <dbl>, V11 <dbl>,
#> #   V12 <dbl>, V13 <dbl>, V14 <dbl>, V15 <dbl>, V16 <dbl>, V17 <dbl>,
#> #   V18 <dbl>, V19 <dbl>, V20 <dbl>, V21 <dbl>, V22 <dbl>, V23 <dbl>,
#> #   V24 <dbl>, V25 <dbl>, V26 <dbl>, V27 <dbl>, V28 <dbl>, V29 <dbl>,
#> #   V30 <dbl>, V31 <dbl>, V32 <dbl>, V33 <dbl>, V34 <dbl>, V35 <dbl>,
#> #   V36 <dbl>, V37 <dbl>, V38 <dbl>, V39 <dbl>, V40 <dbl>, V41 <dbl>,
#> #   V42 <dbl>, V43 <dbl>, V44 <dbl>, V45 <dbl>, V46 <dbl>, V47 <dbl>,
#> #   V48 <dbl>, V49 <dbl>, V50 <dbl>, V51 <dbl>, V52 <dbl>, V53 <dbl>,
#> #   V54 <dbl>, V55 <dbl>, V56 <dbl>, V57 <dbl>, V58 <dbl>, V59 <dbl>,
#> #   V60 <dbl>, V61 <dbl>, V62 <dbl>, V63 <dbl>, V64 <dbl>, V65 <dbl>,
#> #   V66 <dbl>, V67 <dbl>, V68 <dbl>, V69 <dbl>, V70 <dbl>, V71 <dbl>,
#> #   V72 <dbl>, V73 <dbl>, V74 <dbl>, V75 <dbl>, V76 <dbl>, V77 <dbl>,
#> #   V78 <dbl>, V79 <dbl>, V80 <dbl>, V81 <dbl>, V82 <dbl>, V83 <dbl>,
#> #   V84 <dbl>, V85 <dbl>, V86 <dbl>, V87 <dbl>, V88 <dbl>, V89 <dbl>,
#> #   V90 <dbl>, V91 <dbl>, V92 <dbl>, V93 <dbl>, V94 <dbl>, V95 <dbl>,
#> #   V96 <dbl>, V97 <dbl>, V98 <dbl>, V99 <dbl>, V100 <dbl>
```

## Glove

### Package

<https://github.com/dselivanov/text2vec>

### Fitting the model

``` r
library(text2vec)

tokens <- space_tokenizer(fairy_tales)
it <- itoken(tokens, progressbar = FALSE)
vocab <- create_vocabulary(it)
vocab <- prune_vocabulary(vocab, term_count_min = 5L)
vectorizer <- vocab_vectorizer(vocab)
tcm <- create_tcm(it, vectorizer, skip_grams_window = 5L)
glove <- GlobalVectors$new(rank = 100, x_max = 10)
wv_main <- glove$fit_transform(tcm, n_iter = 10, convergence_tol = 0.01)
```

### Extraction word vectors

``` r
wv_context <- glove$components
word_vectors <- wv_main + t(wv_context)

word_vectors <- word_vectors %>%
  as.data.frame() %>%
  rownames_to_column("tokens") %>%
  as_tibble()

word_vectors
#> # A tibble: 6,808 x 101
#>    tokens      V1      V2     V3      V4      V5      V6     V7      V8       V9
#>    <chr>    <dbl>   <dbl>  <dbl>   <dbl>   <dbl>   <dbl>  <dbl>   <dbl>    <dbl>
#>  1 "\"Af…  0.144  -0.411   0.340  0.0137  0.295  -0.643   0.273  0.238  -0.477  
#>  2 "\"Aw… -0.353  -0.191  -0.168  0.224  -0.213  -0.0696  0.795  0.106  -0.339  
#>  3 "\"Aw…  0.344   0.462  -0.760  0.509  -0.558  -0.244   0.462 -0.201   0.0112 
#>  4 "\"Bu…  0.452  -0.219  -0.113 -0.701   0.0971 -0.438  -0.297  0.173   0.0432 
#>  5 "\"By"  0.0822 -0.0111 -0.589 -0.214   0.213  -0.278   0.297  0.136  -0.236  
#>  6 "\"Di…  0.0372 -0.743   0.229 -0.282   0.351   0.313   0.614 -0.377   0.00363
#>  7 "\"Ea…  0.341  -0.568   0.126  0.410   0.320   0.0255 -0.453 -0.324  -0.666  
#>  8 "\"Ev… -0.129  -0.0974 -0.343  0.500   0.606   0.230  -0.408  0.287   0.310  
#>  9 "\"Ge…  0.829  -0.487   0.395 -0.0428 -0.378  -0.243   0.328  0.0278  0.298  
#> 10 "\"Hu…  0.129   0.285   0.108 -0.205   0.740   0.769   0.143 -0.136   0.362  
#> # … with 6,798 more rows, and 91 more variables: V10 <dbl>, V11 <dbl>,
#> #   V12 <dbl>, V13 <dbl>, V14 <dbl>, V15 <dbl>, V16 <dbl>, V17 <dbl>,
#> #   V18 <dbl>, V19 <dbl>, V20 <dbl>, V21 <dbl>, V22 <dbl>, V23 <dbl>,
#> #   V24 <dbl>, V25 <dbl>, V26 <dbl>, V27 <dbl>, V28 <dbl>, V29 <dbl>,
#> #   V30 <dbl>, V31 <dbl>, V32 <dbl>, V33 <dbl>, V34 <dbl>, V35 <dbl>,
#> #   V36 <dbl>, V37 <dbl>, V38 <dbl>, V39 <dbl>, V40 <dbl>, V41 <dbl>,
#> #   V42 <dbl>, V43 <dbl>, V44 <dbl>, V45 <dbl>, V46 <dbl>, V47 <dbl>,
#> #   V48 <dbl>, V49 <dbl>, V50 <dbl>, V51 <dbl>, V52 <dbl>, V53 <dbl>,
#> #   V54 <dbl>, V55 <dbl>, V56 <dbl>, V57 <dbl>, V58 <dbl>, V59 <dbl>,
#> #   V60 <dbl>, V61 <dbl>, V62 <dbl>, V63 <dbl>, V64 <dbl>, V65 <dbl>,
#> #   V66 <dbl>, V67 <dbl>, V68 <dbl>, V69 <dbl>, V70 <dbl>, V71 <dbl>,
#> #   V72 <dbl>, V73 <dbl>, V74 <dbl>, V75 <dbl>, V76 <dbl>, V77 <dbl>,
#> #   V78 <dbl>, V79 <dbl>, V80 <dbl>, V81 <dbl>, V82 <dbl>, V83 <dbl>,
#> #   V84 <dbl>, V85 <dbl>, V86 <dbl>, V87 <dbl>, V88 <dbl>, V89 <dbl>,
#> #   V90 <dbl>, V91 <dbl>, V92 <dbl>, V93 <dbl>, V94 <dbl>, V95 <dbl>,
#> #   V96 <dbl>, V97 <dbl>, V98 <dbl>, V99 <dbl>, V100 <dbl>
```

## Fasttext

### Package

<https://github.com/pommedeterresautee/fastrtext>

### Fitting the model

``` r
library(fastrtext)

texts <- tolower(fairy_tales)
tmp_file_txt <- tempfile()
tmp_file_model <- tempfile()
writeLines(text = texts, con = tmp_file_txt)
execute(commands = c("skipgram", "-input", tmp_file_txt, "-output", tmp_file_model, 
                     "-verbose", 1, "-dim", 100))
#> Read 0M words
#> Number of words:  6581
#> Number of labels: 0
#> Progress: 100.0% words/sec/thread:   81079 lr:  0.000000 avg.loss:  2.510568 ETA:   0h 0m 0s

model <- load_model(tmp_file_model)
#> add .bin extension to the path
```

### Extraction word vectors

``` r
word_vectors <- get_word_vectors(model) %>%
  as.data.frame() %>%
  rownames_to_column("tokens") %>%
  as_tibble()

word_vectors
#> # A tibble: 6,581 x 101
#>    tokens     V1       V2       V3       V4     V5      V6       V7     V8    V9
#>    <chr>   <dbl>    <dbl>    <dbl>    <dbl>  <dbl>   <dbl>    <dbl>  <dbl> <dbl>
#>  1 the    0.0642 -0.0435  -0.334   -0.0672  0.0632 -0.0784  0.0752  -0.227 0.169
#>  2 and    0.269  -0.0493  -0.357    0.0549  0.272  -0.0423 -0.00170 -0.403 0.233
#>  3 of     0.179   0.00357 -0.264   -0.0281  0.317  -0.0121  0.133   -0.136 0.291
#>  4 a      0.395   0.0382  -0.272   -0.228   0.103  -0.119   0.193   -0.311 0.207
#>  5 to     0.237  -0.139    0.00697 -0.00440 0.378   0.108  -0.00690 -0.458 0.457
#>  6 in     0.271  -0.0189  -0.134   -0.209   0.427  -0.0160  0.137   -0.496 0.446
#>  7 was    0.377  -0.104   -0.402    0.0389  0.0650 -0.0947  0.0612  -0.344 0.263
#>  8 he     0.266  -0.201   -0.260   -0.125   0.0429 -0.0611  0.0537  -0.358 0.301
#>  9 it     0.145   0.0356  -0.240   -0.0251  0.226  -0.221   0.0386  -0.720 0.150
#> 10 that   0.324  -0.0163  -0.176   -0.0115  0.298  -0.201  -0.00475 -0.490 0.291
#> # … with 6,571 more rows, and 91 more variables: V10 <dbl>, V11 <dbl>,
#> #   V12 <dbl>, V13 <dbl>, V14 <dbl>, V15 <dbl>, V16 <dbl>, V17 <dbl>,
#> #   V18 <dbl>, V19 <dbl>, V20 <dbl>, V21 <dbl>, V22 <dbl>, V23 <dbl>,
#> #   V24 <dbl>, V25 <dbl>, V26 <dbl>, V27 <dbl>, V28 <dbl>, V29 <dbl>,
#> #   V30 <dbl>, V31 <dbl>, V32 <dbl>, V33 <dbl>, V34 <dbl>, V35 <dbl>,
#> #   V36 <dbl>, V37 <dbl>, V38 <dbl>, V39 <dbl>, V40 <dbl>, V41 <dbl>,
#> #   V42 <dbl>, V43 <dbl>, V44 <dbl>, V45 <dbl>, V46 <dbl>, V47 <dbl>,
#> #   V48 <dbl>, V49 <dbl>, V50 <dbl>, V51 <dbl>, V52 <dbl>, V53 <dbl>,
#> #   V54 <dbl>, V55 <dbl>, V56 <dbl>, V57 <dbl>, V58 <dbl>, V59 <dbl>,
#> #   V60 <dbl>, V61 <dbl>, V62 <dbl>, V63 <dbl>, V64 <dbl>, V65 <dbl>,
#> #   V66 <dbl>, V67 <dbl>, V68 <dbl>, V69 <dbl>, V70 <dbl>, V71 <dbl>,
#> #   V72 <dbl>, V73 <dbl>, V74 <dbl>, V75 <dbl>, V76 <dbl>, V77 <dbl>,
#> #   V78 <dbl>, V79 <dbl>, V80 <dbl>, V81 <dbl>, V82 <dbl>, V83 <dbl>,
#> #   V84 <dbl>, V85 <dbl>, V86 <dbl>, V87 <dbl>, V88 <dbl>, V89 <dbl>,
#> #   V90 <dbl>, V91 <dbl>, V92 <dbl>, V93 <dbl>, V94 <dbl>, V95 <dbl>,
#> #   V96 <dbl>, V97 <dbl>, V98 <dbl>, V99 <dbl>, V100 <dbl>
```
