Plots - tool function experiment
================
Lisa Schunk
2020-09-03 09:49:08

  - [Goal of the script](#goal-of-the-script)
      - [The knit directory for this script is the project
        directory.](#the-knit-directory-for-this-script-is-the-project-directory.)
  - [Load packages](#load-packages)
  - [Get name, path and information of the
    file](#get-name-path-and-information-of-the-file)
  - [Load data into R object](#load-data-into-r-object)
  - [Prepare variables](#prepare-variables)
      - [Define numeric variables](#define-numeric-variables)
  - [Plot each of the selected numeric
    variable](#plot-each-of-the-selected-numeric-variable)
      - [(facet plot = 1 plot for flint, 1 plot for
        lydite)](#facet-plot-1-plot-for-flint-1-plot-for-lydite)
  - [Show plot files information](#show-plot-files-information)
  - [sessionInfo() and RStudio
    version](#sessioninfo-and-rstudio-version)

-----

# Goal of the script

This script plots all variables to see which ones should be used for
further analysis.  
Scatterplot of each variable will be plotted.

``` r
dir_in <- "analysis/derived_data/"
dir_out <- "analysis/plots"
```

Raw data must be located in \~/analysis/derived\_data/.  
Formatted data will be saved in \~/analysis/plots.

## The knit directory for this script is the project directory.

# Load packages

``` r
library(R.utils)
library(ggplot2)
library(tools)
library(tidyverse)
```

-----

# Get name, path and information of the file

``` r
data_file <- list.files(dir_in, pattern = "\\.Rbin$", full.names = TRUE)
md5_in <- md5sum(data_file)
info_in <- data.frame(file = basename(names(md5_in)), checksum = md5_in, row.names = NULL)
```

The checksum (MD5 hashes) of the imported file is:

``` 
               file                         checksum
1 TFE_use-wear.Rbin 433a3db62e03eee5450b37da5616b1a7
```

# Load data into R object

``` r
imp_data <- loadObject(data_file)
```

The imported file is: “\~/analysis/derived\_data/TFE\_use-wear.Rbin”

-----

# Prepare variables

## Define numeric variables

``` r
num.var <- 24:length(imp_data)
```

The following variables will be used:

    [24] Sq
    [25] Ssk
    [26] Sku
    [27] Sp
    [28] Sv
    [29] Sz
    [30] Sa
    [31] Smr
    [32] Smc
    [33] Sxp
    [34] Sal
    [35] Str
    [36] Std
    [37] Sdq
    [38] Sdr
    [39] Vm
    [40] Vv
    [41] Vmp
    [42] Vmc
    [43] Vvc
    [44] Vvv
    [45] Maximum.depth.of.furrows
    [46] Mean.depth.of.furrows
    [47] Mean.density.of.furrows
    [48] First.direction
    [49] Second.direction
    [50] Third.direction
    [51] Isotropy
    [52] epLsar
    [53] NewEplsar
    [54] Asfc
    [55] Smfc
    [56] HAsfc9
    [57] HAsfc81

-----

# Plot each of the selected numeric variable

## (facet plot = 1 plot for flint, 1 plot for lydite)

``` r
for (i in num.var){
  #plot
  range_var <- range(imp_data[[i]]) # gets the min/max range of the data set 
  
    p <- ggplot(data=imp_data, aes_string(x="Task", y=names(imp_data)[i], colour="Edge.angle")) + 
         # avoids overplotting
         geom_jitter(size = 3,  position = position_jitter(width = 0.4, seed = 1)) + 
         coord_cartesian(ylim = range_var) + 
         theme_classic() + 
         # removes the "." betwenn "Edge.angle" in the ledgend 
         labs(colour = gsub("\\.", " ", "Edge.angle")) + 
       # combines the flint and the lydite plot into one    
         facet_wrap(~Raw.material) 
    print(p)


    #save to PDF
    file_out <- paste0(file_path_sans_ext(info_in[["file"]]), "_plot_", 
                       names(imp_data)[i], ".pdf")
    ggsave(filename = file_out, plot = p, path = dir_out, device = "pdf")
}
```

![](TFE_3_plots_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->![](TFE_3_plots_files/figure-gfm/unnamed-chunk-8-2.png)<!-- -->![](TFE_3_plots_files/figure-gfm/unnamed-chunk-8-3.png)<!-- -->![](TFE_3_plots_files/figure-gfm/unnamed-chunk-8-4.png)<!-- -->![](TFE_3_plots_files/figure-gfm/unnamed-chunk-8-5.png)<!-- -->![](TFE_3_plots_files/figure-gfm/unnamed-chunk-8-6.png)<!-- -->![](TFE_3_plots_files/figure-gfm/unnamed-chunk-8-7.png)<!-- -->![](TFE_3_plots_files/figure-gfm/unnamed-chunk-8-8.png)<!-- -->![](TFE_3_plots_files/figure-gfm/unnamed-chunk-8-9.png)<!-- -->![](TFE_3_plots_files/figure-gfm/unnamed-chunk-8-10.png)<!-- -->![](TFE_3_plots_files/figure-gfm/unnamed-chunk-8-11.png)<!-- -->

    Warning: Removed 3 rows containing missing values (geom_point).
    
    Warning: Removed 3 rows containing missing values (geom_point).

![](TFE_3_plots_files/figure-gfm/unnamed-chunk-8-12.png)<!-- -->![](TFE_3_plots_files/figure-gfm/unnamed-chunk-8-13.png)<!-- -->![](TFE_3_plots_files/figure-gfm/unnamed-chunk-8-14.png)<!-- -->![](TFE_3_plots_files/figure-gfm/unnamed-chunk-8-15.png)<!-- -->![](TFE_3_plots_files/figure-gfm/unnamed-chunk-8-16.png)<!-- -->![](TFE_3_plots_files/figure-gfm/unnamed-chunk-8-17.png)<!-- -->![](TFE_3_plots_files/figure-gfm/unnamed-chunk-8-18.png)<!-- -->![](TFE_3_plots_files/figure-gfm/unnamed-chunk-8-19.png)<!-- -->![](TFE_3_plots_files/figure-gfm/unnamed-chunk-8-20.png)<!-- -->![](TFE_3_plots_files/figure-gfm/unnamed-chunk-8-21.png)<!-- -->![](TFE_3_plots_files/figure-gfm/unnamed-chunk-8-22.png)<!-- -->![](TFE_3_plots_files/figure-gfm/unnamed-chunk-8-23.png)<!-- -->![](TFE_3_plots_files/figure-gfm/unnamed-chunk-8-24.png)<!-- -->![](TFE_3_plots_files/figure-gfm/unnamed-chunk-8-25.png)<!-- -->![](TFE_3_plots_files/figure-gfm/unnamed-chunk-8-26.png)<!-- -->![](TFE_3_plots_files/figure-gfm/unnamed-chunk-8-27.png)<!-- -->

    Warning: Removed 3 rows containing missing values (geom_point).
    
    Warning: Removed 3 rows containing missing values (geom_point).

![](TFE_3_plots_files/figure-gfm/unnamed-chunk-8-28.png)<!-- -->![](TFE_3_plots_files/figure-gfm/unnamed-chunk-8-29.png)<!-- -->![](TFE_3_plots_files/figure-gfm/unnamed-chunk-8-30.png)<!-- -->![](TFE_3_plots_files/figure-gfm/unnamed-chunk-8-31.png)<!-- -->![](TFE_3_plots_files/figure-gfm/unnamed-chunk-8-32.png)<!-- -->![](TFE_3_plots_files/figure-gfm/unnamed-chunk-8-33.png)<!-- -->![](TFE_3_plots_files/figure-gfm/unnamed-chunk-8-34.png)<!-- -->
The files will be saved as “\~/analysis/plots.\[ext\]”.

-----

# Show plot files information

``` r
info_out <- list.files(path = dir_out, pattern = "\\.pdf$", full.names = TRUE) %>% 
            md5sum()
```

The checksum (MD5 hashes) of the exported files are:

``` 
                    analysis/plots/TFE_use-wear_plot_Asfc.pdf 
                           "908f2b0d4ba9c60189076b5dafaa4f74" 
                  analysis/plots/TFE_use-wear_plot_epLsar.pdf 
                           "e918318213fb7eebe4d6cb693a6791c8" 
         analysis/plots/TFE_use-wear_plot_First.direction.pdf 
                           "0bcb594b79f980b6e270b7f525df0bb0" 
                 analysis/plots/TFE_use-wear_plot_HAsfc81.pdf 
                           "cfc8c5b253cc1274adadfbd71a81d12b" 
                  analysis/plots/TFE_use-wear_plot_HAsfc9.pdf 
                           "922c7ea59e516db5aafaf6650edea630" 
                analysis/plots/TFE_use-wear_plot_Isotropy.pdf 
                           "32fb7b4a17b07e5e80965ccc80eea131" 
analysis/plots/TFE_use-wear_plot_Maximum.depth.of.furrows.pdf 
                           "ff71c84e5cb1487b5f5162b50e868ee4" 
 analysis/plots/TFE_use-wear_plot_Mean.density.of.furrows.pdf 
                           "d77b1bfe4604f8c0fc17ff0dc53e9e94" 
   analysis/plots/TFE_use-wear_plot_Mean.depth.of.furrows.pdf 
                           "4562fc116dc97b5664e9b6f9b697db40" 
               analysis/plots/TFE_use-wear_plot_NewEplsar.pdf 
                           "bb1746072b913c36d15c939ec8be0137" 
                      analysis/plots/TFE_use-wear_plot_Sa.pdf 
                           "aa458168d5beebea6cd9ab34364055ed" 
                     analysis/plots/TFE_use-wear_plot_Sal.pdf 
                           "1c6443fa3470ab77d9d0a47e5bf38501" 
                     analysis/plots/TFE_use-wear_plot_Sdq.pdf 
                           "0c80741de47549617ba2b97010d576a7" 
                     analysis/plots/TFE_use-wear_plot_Sdr.pdf 
                           "eb733ea32d1411f17fa50c0d65b1ae66" 
        analysis/plots/TFE_use-wear_plot_Second.direction.pdf 
                           "34250f1b2a4166c785423f0fd20bae10" 
                     analysis/plots/TFE_use-wear_plot_Sku.pdf 
                           "ae17f50906363651b3ef9c666a7d651a" 
                     analysis/plots/TFE_use-wear_plot_Smc.pdf 
                           "1796dcc744507205376925cdda7bf143" 
                    analysis/plots/TFE_use-wear_plot_Smfc.pdf 
                           "8ff479a647dc15154537da430b225352" 
                     analysis/plots/TFE_use-wear_plot_Smr.pdf 
                           "c1b5ec4aedf07bae53f69dede626fc5a" 
                      analysis/plots/TFE_use-wear_plot_Sp.pdf 
                           "4b1cefd060038694ee7e4318649074b2" 
                      analysis/plots/TFE_use-wear_plot_Sq.pdf 
                           "6b37f390bc3fef7712de4a1950093502" 
                     analysis/plots/TFE_use-wear_plot_Ssk.pdf 
                           "dc90ff2354f7874037084a0302b8f20e" 
                     analysis/plots/TFE_use-wear_plot_Std.pdf 
                           "31ffad0f3ad637bb155333e48efbe209" 
                     analysis/plots/TFE_use-wear_plot_Str.pdf 
                           "18818d3c1388e3495c537d101dded7d9" 
                      analysis/plots/TFE_use-wear_plot_Sv.pdf 
                           "3901682ec815c140d40a630523d530c4" 
                     analysis/plots/TFE_use-wear_plot_Sxp.pdf 
                           "5ff980a8f007ac82bb0bc735a8b3c217" 
                      analysis/plots/TFE_use-wear_plot_Sz.pdf 
                           "8e81b5848853f061abbcf73209c9548e" 
         analysis/plots/TFE_use-wear_plot_Third.direction.pdf 
                           "2bc586e069449f529c92df493c6dea6d" 
                      analysis/plots/TFE_use-wear_plot_Vm.pdf 
                           "b65a047f175ddd72a2191e68671efdcd" 
                     analysis/plots/TFE_use-wear_plot_Vmc.pdf 
                           "5068eba6b8d095f2518eb321b46897c4" 
                     analysis/plots/TFE_use-wear_plot_Vmp.pdf 
                           "6e54fdaa4ce505830272af440ed44c4f" 
                      analysis/plots/TFE_use-wear_plot_Vv.pdf 
                           "4357b8fa26caed9d579d9f848e2939cc" 
                     analysis/plots/TFE_use-wear_plot_Vvc.pdf 
                           "4ffb9118a004755516b54e67b773ae3a" 
                     analysis/plots/TFE_use-wear_plot_Vvv.pdf 
                           "ed274d6d6f270ae6147516a3194e8a3c" 
```

-----

# sessionInfo() and RStudio version

``` r
sessionInfo()
```

``` 
R version 4.0.2 (2020-06-22)
Platform: x86_64-w64-mingw32/x64 (64-bit)
Running under: Windows 10 x64 (build 18362)

Matrix products: default

locale:
[1] LC_COLLATE=German_Germany.1252  LC_CTYPE=German_Germany.1252   
[3] LC_MONETARY=German_Germany.1252 LC_NUMERIC=C                   
[5] LC_TIME=German_Germany.1252    

attached base packages:
[1] tools     stats     graphics  grDevices utils     datasets  methods  
[8] base     

other attached packages:
 [1] forcats_0.5.0     stringr_1.4.0     dplyr_1.0.0       purrr_0.3.4      
 [5] readr_1.3.1       tidyr_1.1.0       tibble_3.0.1      tidyverse_1.3.0  
 [9] ggplot2_3.3.2     R.utils_2.9.2     R.oo_1.23.0       R.methodsS3_1.8.0

loaded via a namespace (and not attached):
 [1] tidyselect_1.1.0 xfun_0.15        haven_2.3.1      lattice_0.20-41 
 [5] colorspace_1.4-1 vctrs_0.3.1      generics_0.0.2   htmltools_0.5.0 
 [9] yaml_2.2.1       blob_1.2.1       rlang_0.4.6      pillar_1.4.4    
[13] glue_1.4.1       withr_2.2.0      DBI_1.1.0        dbplyr_1.4.4    
[17] modelr_0.1.8     readxl_1.3.1     lifecycle_0.2.0  munsell_0.5.0   
[21] gtable_0.3.0     cellranger_1.1.0 rvest_0.3.5      evaluate_0.14   
[25] labeling_0.3     knitr_1.29       fansi_0.4.1      broom_0.5.6     
[29] Rcpp_1.0.4.6     scales_1.1.1     backports_1.1.7  jsonlite_1.7.0  
[33] farver_2.0.3     fs_1.4.2         hms_0.5.3        digest_0.6.25   
[37] stringi_1.4.6    grid_4.0.2       rprojroot_1.3-2  cli_2.0.2       
[41] magrittr_1.5     crayon_1.3.4     pkgconfig_2.0.3  ellipsis_0.3.1  
[45] xml2_1.3.2       reprex_0.3.0     lubridate_1.7.9  rstudioapi_0.11 
[49] assertthat_0.2.1 rmarkdown_2.3    httr_1.4.1       R6_2.4.1        
[53] nlme_3.1-148     compiler_4.0.2  
```

RStudio version 1.3.1073.

-----

END OF SCRIPT
