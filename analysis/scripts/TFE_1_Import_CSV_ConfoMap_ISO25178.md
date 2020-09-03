Import CSV from ConfoMap ISO25178 - tool function experiment
================
Lisa Schunk
2020-09-03 09:32:34

  - [Goal of the script](#goal-of-the-script)
  - [Load packages](#load-packages)
  - [Get names, path and information of the
    file](#get-names-path-and-information-of-the-file)
  - [Read in original CSV-file](#read-in-original-csv-file)
  - [Format data](#format-data)
      - [Keep only interesting columns and
        rows](#keep-only-interesting-columns-and-rows)
      - [Add headers](#add-headers)
      - [Identify results using frame
        numbers](#identify-results-using-frame-numbers)
      - [Shorten the names for
        parameters](#shorten-the-names-for-parameters)
      - [Save units](#save-units)
      - [Convert to numeric](#convert-to-numeric)
      - [Split the column ‘Name’ into several
        columns](#split-the-column-name-into-several-columns)
      - [Format date and time columns](#format-date-and-time-columns)
      - [Add columns about site, contact material, the task and the edge
        angle](#add-columns-about-site-contact-material-the-task-and-the-edge-angle)
      - [Ignore some columns and reorder
        columns](#ignore-some-columns-and-reorder-columns)
      - [Add units as comment()](#add-units-as-comment)
      - [Check the result](#check-the-result)
  - [Save data](#save-data)
      - [Format name of output file](#format-name-of-output-file)
      - [Write to XLSX](#write-to-xlsx)
      - [Save R object](#save-r-object)
      - [Show files information](#show-files-information)
  - [sessionInfo() and RStudio
    version](#sessioninfo-and-rstudio-version)

-----

# Goal of the script

This script formats the output of the resulting CSV-file from applying a
template computing ISO25178 parameters in ConfoMap. The script will:

1.  Read in the original CSV-file (step \#3)  
2.  Format the data (step \#4)  
3.  Write an XLSX-file and save an R object ready for further analysis
    in R (step \#5)

<!-- end list -->

``` r
dir_in <- "analysis/raw_data"
dir_out <- "analysis/derived_data/"
```

Raw data must be located in \~/analysis/raw\_data.  
Formatted data will be saved in \~/analysis/derived\_data/.

The knit directory for this script is the project directory.

-----

# Load packages

``` r
library(openxlsx)
library(tools)
library(R.utils)
library(chron)
```

-----

# Get names, path and information of the file

``` r
data_file <- list.files(dir_in, pattern = "\\.csv$", full.names = TRUE)
md5_in <- md5sum(data_file)
info_in <- data.frame(file = basename(names(md5_in)), checksum = md5_in, row.names = NULL)
```

The checksum (MD5 hashes) of the imported file are:

``` 
         file                         checksum
1 TFE_pro.csv aca5a03e94d1efdc6197b198b5c68147
```

-----

# Read in original CSV-file

``` r
imp_data <- read.csv(data_file, header = FALSE, stringsAsFactors = FALSE, na.strings = "*****")

str(imp_data)
```

    'data.frame':   27 obs. of  100 variables:
     $ V1  : chr  "#" "#" "#" "27.08.2020" ...
     $ V2  : chr  "#" "#" "#" "15:01:32" ...
     $ V3  : chr  "#" "#" "#" "C:\\Users\\schunk\\Documents\\USE-WEAR\\experiment\\tool_function-experiment\\ConfoMap\\TFE_pro --- TFE_50x_res"| __truncated__ ...
     $ V4  : chr  "OPERATOR:1" "X-axis rotation angle" "°" "-0.473512322" ...
     $ V5  : chr  "OPERATOR:1" "Y-axis rotation angle" "°" "1.731683477" ...
     $ V6  : chr  "OPERATOR:2" "a0" "nm" "-370.3850221" ...
     $ V7  : chr  "OPERATOR:2" "ax" "nm" "12.47840143" ...
     $ V8  : chr  "OPERATOR:2" "ax2" "nm" "-0.01786267" ...
     $ V9  : chr  "OPERATOR:2" "ax3" "nm" "-3.63E-06" ...
     $ V10 : chr  "OPERATOR:2" "ay" "nm" "0.843379272" ...
     $ V11 : chr  "OPERATOR:2" "axy" "nm" "0.010205056" ...
     $ V12 : chr  "OPERATOR:2" "ax2y" "nm" "2.72E-05" ...
     $ V13 : chr  "OPERATOR:2" "ay2" "nm" "-0.013967608" ...
     $ V14 : chr  "OPERATOR:2" "axy2" "nm" "-2.51E-05" ...
     $ V15 : chr  "OPERATOR:2" "ay3" "nm" "1.15E-05" ...
     $ V16 : chr  "6" "Name" "<no unit>" "TFE_50x_res --- FLT8-10 - FLT8-10_2000_C1-01-a_50x095_LSM_Topo" ...
     $ V17 : chr  "6" "Created on" "<no unit>" "8/27/2020 11:10:06 AM" ...
     $ V18 : chr  "6" "Studiable type" "<no unit>" "Surface" ...
     $ V19 : chr  "6" "Axis name - X" "<no unit>" "X" ...
     $ V20 : chr  "6" "Axis length - X" "µm" "255.4748056" ...
     $ V21 : chr  "6" "Axis size - X" "points" "1198" ...
     $ V22 : chr  "6" "Axis spacing - X" "µm" "0.213429245" ...
     $ V23 : chr  "6" "Axis name - Y" "<no unit>" "Y" ...
     $ V24 : chr  "6" "Axis length - Y" "µm" "255.4748056" ...
     $ V25 : chr  "6" "Axis size - Y" "points" "1198" ...
     $ V26 : chr  "6" "Axis spacing - Y" "µm" "0.213429245" ...
     $ V27 : chr  "6" "Axis name - Z" "<no unit>" "Z" ...
     $ V28 : chr  "6" "Layer type - Z" "<no unit>" "Topography" ...
     $ V29 : chr  "6" "Axis length - Z" "nm" "34010.19716" ...
     $ V30 : chr  "6" "Axis size - Z" "digits" "73180" ...
     $ V31 : chr  "6" "Axis spacing - Z" "nm" "0.46474716" ...
     $ V32 : chr  "6" "NM-points ratio - Z" "%" "0" ...
     $ V33 : chr  "8" "Name" "<no unit>" "TFE_50x_res --- FLT8-10 - FLT8-10-2000s-C1-01-a_50x095_LSM_Topo > Leveled (LS-plane)" ...
     $ V34 : chr  "8" "Created on" "<no unit>" "8/27/2020 11:10:06 AM" ...
     $ V35 : chr  "8" "Studiable type" "<no unit>" "Surface" ...
     $ V36 : chr  "8" "Axis name - X" "<no unit>" "X" ...
     $ V37 : chr  "8" "Axis length - X" "µm" "255.4748056" ...
     $ V38 : chr  "8" "Axis size - X" "points" "1198" ...
     $ V39 : chr  "8" "Axis spacing - X" "µm" "0.213429245" ...
     $ V40 : chr  "8" "Axis name - Y" "<no unit>" "Y" ...
     $ V41 : chr  "8" "Axis length - Y" "µm" "255.4748056" ...
     $ V42 : chr  "8" "Axis size - Y" "points" "1198" ...
     $ V43 : chr  "8" "Axis spacing - Y" "µm" "0.213429245" ...
     $ V44 : chr  "8" "Axis name - Z" "<no unit>" "Z" ...
     $ V45 : chr  "8" "Layer type - Z" "<no unit>" "Topography" ...
     $ V46 : chr  "8" "Axis length - Z" "nm" "34296.48141" ...
     $ V47 : chr  "8" "Axis size - Z" "digits" "73796" ...
     $ V48 : chr  "8" "Axis spacing - Z" "nm" "0.46474716" ...
     $ V49 : chr  "8" "NM-points ratio - Z" "%" "0" ...
     $ V50 : chr  "15" "Name" "<no unit>" "TFE_50x_res --- FLT8-10 - FLT8-10-2000s-C1-01-a_50x095_LSM_Topo > Leveled (LS-plane) > Form removed (LS-poly 3)"| __truncated__ ...
     $ V51 : chr  "15" "Created on" "<no unit>" "8/27/2020 11:10:06 AM" ...
     $ V52 : chr  "15" "Studiable type" "<no unit>" "Surface" ...
     $ V53 : chr  "15" "Axis name - X" "<no unit>" "X" ...
     $ V54 : chr  "15" "Axis length - X" "µm" "255.4748056" ...
     $ V55 : chr  "15" "Axis size - X" "points" "1198" ...
     $ V56 : chr  "15" "Axis spacing - X" "µm" "0.213429245" ...
     $ V57 : chr  "15" "Axis name - Y" "<no unit>" "Y" ...
     $ V58 : chr  "15" "Axis length - Y" "µm" "255.4748056" ...
     $ V59 : chr  "15" "Axis size - Y" "points" "1198" ...
     $ V60 : chr  "15" "Axis spacing - Y" "µm" "0.213429245" ...
     $ V61 : chr  "15" "Axis name - Z" "<no unit>" "Z" ...
     $ V62 : chr  "15" "Layer type - Z" "<no unit>" "Topography" ...
     $ V63 : chr  "15" "Axis length - Z" "nm" "12956.17485" ...
     $ V64 : chr  "15" "Axis size - Z" "digits" "278779" ...
     $ V65 : chr  "15" "Axis spacing - Z" "nm" "0.046474716" ...
     $ V66 : chr  "15" "NM-points ratio - Z" "%" "0" ...
     $ V67 : chr  "17" "Sq" "nm" "1639.824789" ...
     $ V68 : chr  "17" "Ssk" "<no unit>" "-0.625520875" ...
     $ V69 : chr  "17" "Sku" "<no unit>" "7.122443946" ...
     $ V70 : chr  "17" "Sp" "nm" "5602.619962" ...
     $ V71 : chr  "17" "Sv" "nm" "7353.554886" ...
     $ V72 : chr  "17" "Sz" "nm" "12956.17485" ...
     $ V73 : chr  "17" "Sa" "nm" "1080.425967" ...
     $ V74 : chr  "17" "Smr (c = 1000 nm below highest peak)" "%" "0.88718972" ...
     $ V75 : chr  "17" "Smc (p = 10.00%)" "nm" "1669.034734" ...
     $ V76 : chr  "17" "Sxp (p = 50.00%  q = 97.50%)" "nm" "4453.800909" ...
     $ V77 : chr  "17" "Sal (s = 0.2000)" "µm" "19.27134811" ...
     $ V78 : chr  "17" "Str (s = 0.2000)" "<no unit>" "0.259543727" ...
     $ V79 : chr  "17" "Std (Reference angle = 0.000°)" "°" "159.2409342" ...
     $ V80 : chr  "17" "Sdq" "<no unit>" "0.269035059" ...
     $ V81 : chr  "17" "Sdr" "%" "3.006301428" ...
     $ V82 : chr  "17" "Vm (p = 10.00%)" "µm³/µm²" "0.135390838" ...
     $ V83 : chr  "17" "Vv (p = 10.00%)" "µm³/µm²" "1.804402664" ...
     $ V84 : chr  "17" "Vmp (p = 10.00%)" "µm³/µm²" "0.135390838" ...
     $ V85 : chr  "17" "Vmc (p = 10.00%  q = 80.00%)" "µm³/µm²" "0.927873972" ...
     $ V86 : chr  "17" "Vvc (p = 10.00%  q = 80.00%)" "µm³/µm²" "1.553178741" ...
     $ V87 : chr  "17" "Vvv (p = 80.00%)" "µm³/µm²" "0.251223923" ...
     $ V88 : chr  "18" "Maximum depth of furrows" "nm" "7250.562268" ...
     $ V89 : chr  "18" "Mean depth of furrows" "nm" "1161.089383" ...
     $ V90 : chr  "18" "Mean density of furrows" "cm/cm2" "2724.29728" ...
     $ V91 : chr  "19" "First direction" "°" "89.98041753" ...
     $ V92 : chr  "19" "Second direction" "°" "135.0199217" ...
     $ V93 : chr  "19" "Third direction" "°" "0.001700073" ...
     $ V94 : chr  "20" "Isotropy" "%" "18.27056476" ...
     $ V95 : chr  "21" "Length-scale anisotropy (Sfrax) (epLsar)" "<no unit>" "0.002336563" ...
     $ V96 : chr  "21" "Length-scale anisotropy (NewEplsar)" "<no unit>" "0.018492557" ...
     $ V97 : chr  "22" "Fractal complexity (Asfc)" "<no unit>" "4.309128563" ...
     $ V98 : chr  "22" "Scale of max complexity (Smfc)" "µm²" "4.931042371" ...
     $ V99 : chr  "22" "HAsfc9 (HAsfc9)" "<no unit>" "0.662829456" ...
      [list output truncated]

-----

# Format data

## Keep only interesting columns and rows

``` r
# keeps only the columns and rows of interest for the analysis 
data_keep_col <- c(1:2, 16:17, 20:22, 24:26, 29:32, 67:100)
data_keep_rows <- which(imp_data[[1]] != "#")
data_keep <- imp_data[data_keep_rows, data_keep_col]
```

## Add headers

``` r
head_data_keep <- unlist(imp_data[2, data_keep_col]) 
colnames(data_keep) <- gsub("\\.+", "\\.", make.names(head_data_keep))
colnames(data_keep) <- gsub("\\.$", "", colnames(data_keep)) 
```

## Identify results using frame numbers

``` r
# combines the results from the different analysis based on the column numbers (ID from MountainsMAp)
frames <- as.numeric(unlist(imp_data[1, data_keep_col]))
```

    Warning: NAs durch Umwandlung erzeugt

``` r
ID <- which(frames == 6)[-(1:2)]
ISO <- which(frames == 17)
furrow <- which(frames == 18)
diriso <- which(frames %in% 19:20)
SSFA <- which(frames %in% 21:22)
```

## Shorten the names for parameters

``` r
# keeps only the important information of the headers 
colnames(data_keep)[ISO] <- sapply(strsplit(names(data_keep)[ISO], ".", fixed = TRUE), `[[`, 1)
colnames(data_keep)[SSFA] <- gsub("^([A-Za-z0-9]+\\.)+", "", colnames(data_keep)[SSFA])
```

## Save units

``` r
# takes the units which were part of the headers and separates them; creates a data frame
var_num <- c(ID, ISO, furrow, diriso, SSFA)
# extracts 'unit' line for considered columns
units_var <- unlist(imp_data[3, data_keep_col])[var_num] 
# gets names associated to the units
names(units_var) <- head_data_keep[var_num] 
# puts all of it into a data.frame
units_var_table <- data.frame(variable = names(units_var), unit = units_var) 
```

## Convert to numeric

``` r
for (i in var_num) data_keep[[i]] <- as.numeric(data_keep[[i]])
```

## Split the column ‘Name’ into several columns

``` r
# these lines extract the artefact ID out of the path name
stud_name <- gsub("^([A-Za-z0-9_]+( --- ))+", "", data_keep[["Name"]])
stud_name <- gsub("([A-Za-z0-9_-]*( - ))+", "", stud_name)
split_name <- do.call(rbind, strsplit(stud_name, "_"))[, 1:4]
split_loc <- do.call(rbind, strsplit(split_name[, 3], "-"))

# splits the ID in the separat information 
data_final <- data.frame(split_name[, -3], split_loc, data_keep[-3], stringsAsFactors = FALSE)
colnames(data_final)[1:9] <- c("Sample", "Cycle","Objective", 
                               "Location", "Area", "Spot", "Analysis.date", "Analysis.time", 
                               "Acquisition.date.time")
```

## Format date and time columns

``` r
data_final[["Analysis.date"]] <- as.Date(data_final[["Analysis.date"]], format = "%d.%m.%Y")
data_final[["Analysis.time"]] <- times(data_final[["Analysis.time"]])
```

The column `data_final[["Acquisition.date.time"]]` includes several
formats and is therefore left as `character` without convertion to
`POSIXct`.

## Add columns about site, contact material, the task and the edge angle

``` r
# extracts the raw material based on the ID
data_final[grep("FLT8-", data_final[["Sample"]]), "Raw.material"] <- "flint"
data_final[grep("LYDIT5-", data_final[["Sample"]]), "Raw.material"] <- "lydite"
data_final[["Raw.material"]] <- factor(data_final[["Raw.material"]])

# adds column about the contact material
data_final[grep("LYDIT5-", data_final[["Sample"]]), "Contact.material"] <- "bone plate"
data_final[grep("FLT8-", data_final[["Sample"]]), "Contact.material"] <- "bone plate"
data_final[["Contact.material"]] <- factor(data_final[["Contact.material"]])

# adds column about the task/movement 
data_final[grep("FLT8-2", data_final[["Sample"]]), "Task"] <- "cutting"
data_final[grep("LYDIT5-2", data_final[["Sample"]]), "Task"] <- "cutting"
data_final[grep("FLT8-5", data_final[["Sample"]]), "Task"] <- "cutting"
data_final[grep("LYDIT5-7", data_final[["Sample"]]), "Task"] <- "cutting"

data_final[grep("FLT8-10", data_final[["Sample"]]), "Task"] <- "carving"
data_final[grep("LYDIT5-8", data_final[["Sample"]]), "Task"] <- "carving"
data_final[grep("FLT8-9", data_final[["Sample"]]), "Task"] <- "carving"
data_final[grep("LYDIT5-12", data_final[["Sample"]]), "Task"] <- "carving"
data_final[["Task"]] <- factor(data_final[["Task"]])

# adds column about the edge angle 
data_final[grep("FLT8-2", data_final[["Sample"]]), "Edge.angle"] <- "45°"
data_final[grep("LYDIT5-2", data_final[["Sample"]]), "Edge.angle"] <- "45°"
data_final[grep("FLT8-5", data_final[["Sample"]]), "Edge.angle"] <- "35°"
data_final[grep("LYDIT5-7", data_final[["Sample"]]), "Edge.angle"] <- "35°"

data_final[grep("FLT8-10", data_final[["Sample"]]), "Edge.angle"] <- "45°"
data_final[grep("LYDIT5-8", data_final[["Sample"]]), "Edge.angle"] <- "45°"
data_final[grep("FLT8-9", data_final[["Sample"]]), "Edge.angle"] <- "35°"
data_final[grep("LYDIT5-12", data_final[["Sample"]]), "Edge.angle"] <- "35°"
data_final[["Edge.angle"]] <- factor(data_final[["Edge.angle"]])
```

## Ignore some columns and reorder columns

``` r
data_final <- data_final[c(1:2, 4:6, 3, 54:57, 7:9, 10:53)]
```

## Add units as comment()

``` r
comment(data_final) <- units_var
```

Type `comment(data_final)` to check the units of the columns.

## Check the result

``` r
str(data_final)
```

    'data.frame':   24 obs. of  57 variables:
     $ Sample                  : chr  "FLT8-10" "FLT8-10" "FLT8-10" "FLT8-2" ...
     $ Cycle                   : chr  "2000" "2000" "2000" "2000" ...
     $ Location                : chr  "C1" "C1" "C1" "D1" ...
     $ Area                    : chr  "01" "01" "01" "01" ...
     $ Spot                    : chr  "a" "b" "c" "a" ...
     $ Objective               : chr  "50x095" "50x095" "50x095" "50x095" ...
     $ Raw.material            : Factor w/ 2 levels "flint","lydite": 1 1 1 1 1 1 1 1 1 1 ...
     $ Contact.material        : Factor w/ 1 level "bone plate": 1 1 1 1 1 1 1 1 1 1 ...
     $ Task                    : Factor w/ 2 levels "carving","cutting": 1 1 1 2 2 2 2 2 2 1 ...
     $ Edge.angle              : Factor w/ 2 levels "35°","45°": 2 2 2 2 2 2 1 1 1 1 ...
     $ Analysis.date           : Date, format: "2020-08-27" "2020-08-27" ...
     $ Analysis.time           : 'times' num  15:01:32 15:02:11 15:02:48 15:03:28 15:04:06 ...
      ..- attr(*, "format")= chr "h:m:s"
     $ Acquisition.date.time   : chr  "8/27/2020 11:10:06 AM" "8/27/2020 11:41:47 AM" "8/27/2020 11:54:14 AM" "8/26/2020 4:10:04 PM" ...
     $ Axis.length.X           : num  255 255 255 255 255 ...
     $ Axis.size.X             : num  1198 1198 1198 1198 1198 ...
     $ Axis.spacing.X          : num  0.213 0.213 0.213 0.213 0.213 ...
     $ Axis.length.Y           : num  255 255 255 255 255 ...
     $ Axis.size.Y             : num  1198 1198 1198 1198 1198 ...
     $ Axis.spacing.Y          : num  0.213 0.213 0.213 0.213 0.213 ...
     $ Axis.length.Z           : num  34010 95224 7771 60579 60641 ...
     $ Axis.size.Z             : num  73180 61441 61664 52314 57436 ...
     $ Axis.spacing.Z          : num  0.465 1.55 0.126 1.158 1.056 ...
     $ NM.points.ratio.Z       : num  0 0 0 0 0 0 0 0 0 0 ...
     $ Sq                      : num  1640 7217 315 2525 1718 ...
     $ Ssk                     : num  -0.626 0.517 -1.202 -0.51 -1.432 ...
     $ Sku                     : num  7.12 4.34 6.34 6.94 12.01 ...
     $ Sp                      : num  5603 24832 716 8900 5842 ...
     $ Sv                      : num  7354 24749 1929 12958 11581 ...
     $ Sz                      : num  12956 49581 2645 21858 17423 ...
     $ Sa                      : num  1080 5359 239 1814 1117 ...
     $ Smr                     : num  0.887 0.151 84.156 0.519 0.768 ...
     $ Smc                     : num  1669 8453 350 2620 1633 ...
     $ Sxp                     : num  4454 13827 770 5202 3974 ...
     $ Sal                     : num  19.3 18.3 11.3 20.1 17.7 ...
     $ Str                     : num  0.26 NA 0.286 NA 0.154 ...
     $ Std                     : num  159 169 156 148 148 ...
     $ Sdq                     : num  0.269 1.165 0.166 0.336 0.294 ...
     $ Sdr                     : num  3.01 19.2 1.35 4.62 3.61 ...
     $ Vm                      : num  0.1354 0.6714 0.0093 0.1819 0.1149 ...
     $ Vv                      : num  1.804 9.125 0.359 2.802 1.748 ...
     $ Vmp                     : num  0.1354 0.6714 0.0093 0.1819 0.1149 ...
     $ Vmc                     : num  0.928 5.19 0.266 1.856 1.034 ...
     $ Vvc                     : num  1.553 8.34 0.309 2.488 1.485 ...
     $ Vvv                     : num  0.2512 0.7845 0.0502 0.314 0.2637 ...
     $ Maximum.depth.of.furrows: num  7251 27509 2061 7182 6417 ...
     $ Mean.depth.of.furrows   : num  1161 8568 433 2115 1408 ...
     $ Mean.density.of.furrows : num  2724 2613 3036 2486 2519 ...
     $ First.direction         : num  90 169 90 135 135 ...
     $ Second.direction        : num  135 174 154 154 154 ...
     $ Third.direction         : num  0.0017 179.9953 135.0327 0.0028 161.4949 ...
     $ Isotropy                : num  18.3 NA 38.2 NA 35.8 ...
     $ epLsar                  : num  0.00234 0.00814 0.00109 0.00501 0.00521 ...
     $ NewEplsar               : num  0.0185 0.021 0.0178 0.0199 0.02 ...
     $ Asfc                    : num  4.31 37.84 2.9 5.11 4.67 ...
     $ Smfc                    : num  4.93 759.24 3.3 32.33 6.9 ...
     $ HAsfc9                  : num  0.6628 6.9889 0.0727 0.5633 0.4985 ...
     $ HAsfc81                 : num  0.708 6.728 0.198 1.53 1.352 ...
     - attr(*, "comment")= Named chr [1:44] "µm" "points" "µm" "µm" ...
      ..- attr(*, "names")= chr [1:44] "Axis length - X" "Axis size - X" "Axis spacing - X" "Axis length - Y" ...

``` r
head(data_final)
```

``` 
   Sample Cycle Location Area Spot Objective Raw.material Contact.material
4 FLT8-10  2000       C1   01    a    50x095        flint       bone plate
5 FLT8-10  2000       C1   01    b    50x095        flint       bone plate
6 FLT8-10  2000       C1   01    c    50x095        flint       bone plate
7  FLT8-2  2000       D1   01    a    50x095        flint       bone plate
8  FLT8-2  2000       D1   01    b    50x095        flint       bone plate
9  FLT8-2  2000       D1   01    c    50x095        flint       bone plate
     Task Edge.angle Analysis.date Analysis.time Acquisition.date.time
4 carving        45°    2020-08-27      15:01:32 8/27/2020 11:10:06 AM
5 carving        45°    2020-08-27      15:02:11 8/27/2020 11:41:47 AM
6 carving        45°    2020-08-27      15:02:48 8/27/2020 11:54:14 AM
7 cutting        45°    2020-08-27      15:03:28  8/26/2020 4:10:04 PM
8 cutting        45°    2020-08-27      15:04:06  8/26/2020 3:41:55 PM
9 cutting        45°    2020-08-27      15:04:42  8/26/2020 3:12:27 PM
  Axis.length.X Axis.size.X Axis.spacing.X Axis.length.Y Axis.size.Y
4      255.4748        1198      0.2134292      255.4748        1198
5      255.4748        1198      0.2134292      255.4748        1198
6      255.4748        1198      0.2134292      255.4748        1198
7      255.4748        1198      0.2134292      255.4748        1198
8      255.4748        1198      0.2134292      255.4748        1198
9      255.4748        1198      0.2134292      255.4748        1198
  Axis.spacing.Y Axis.length.Z Axis.size.Z Axis.spacing.Z NM.points.ratio.Z
4      0.2134292     34010.197       73180      0.4647472                 0
5      0.2134292     95223.581       61441      1.5498377                 0
6      0.2134292      7771.318       61664      0.1260268                 0
7      0.2134292     60578.579       52314      1.1579803                 0
8      0.2134292     60640.746       57436      1.0557968                 0
9      0.2134292     47752.177       64939      0.7353390                 0
         Sq        Ssk       Sku         Sp        Sv        Sz        Sa
4 1639.8248 -0.6255209  7.122444  5602.6200  7353.555 12956.175 1080.4260
5 7217.0443  0.5167268  4.344765 24832.4303 24748.739 49581.169 5359.1126
6  315.3104 -1.2015183  6.336598   715.8449  1928.929  2644.774  238.6203
7 2524.8750 -0.5100466  6.937178  8900.2362 12958.262 21858.498 1813.9692
8 1718.2963 -1.4319687 12.005263  5841.7238 11581.352 17423.076 1117.1372
9 2545.8971 -0.7719253  8.618212  8665.1609 14245.575 22910.736 1769.3464
         Smr       Smc        Sxp      Sal       Str      Std       Sdq
4  0.8871897 1669.0347  4453.8009 19.27135 0.2595437 159.2409 0.2690351
5  0.1508375 8453.3226 13827.0922 18.33296        NA 169.0020 1.1647737
6 84.1558025  349.5086   770.2046 11.34748 0.2859776 156.2535 0.1656981
7  0.5191462 2619.7487  5201.6690 20.11496        NA 148.4946 0.3357202
8  0.7680698 1633.4928  3973.5568 17.69425 0.1541349 148.4927 0.2936511
9  0.4101862 2736.5890  4848.5961 23.58091 0.5143067 140.5092 0.3499047
        Sdr          Vm        Vv         Vmp       Vmc      Vvc        Vvv
4  3.006301 0.135390838 1.8044027 0.135390838 0.9278740 1.553179 0.25122392
5 19.198216 0.671390167 9.1247611 0.671390167 5.1904172 8.340286 0.78447482
6  1.348053 0.009299266 0.3588037 0.009299266 0.2655201 0.308595 0.05020862
7  4.622614 0.181895348 2.8016366 0.181895348 1.8557557 2.487597 0.31403995
8  3.607732 0.114859889 1.7483200 0.114859889 1.0337459 1.484646 0.26367368
9  4.759987 0.184034352 2.9206306 0.184034352 1.6632884 2.589177 0.33145377
  Maximum.depth.of.furrows Mean.depth.of.furrows Mean.density.of.furrows
4                 7250.562             1161.0894                2724.297
5                27509.465             8568.3982                2612.809
6                 2061.349              433.1047                3035.797
7                 7181.678             2114.8766                2486.011
8                 6416.840             1408.2987                2519.403
9                 7852.023             1610.6934                2486.264
  First.direction Second.direction Third.direction Isotropy      epLsar
4        89.98042         135.0199    1.700073e-03 18.27056 0.002336563
5       168.71931         173.7408    1.799953e+02       NA 0.008143085
6        89.99392         153.5202    1.350327e+02 38.23035 0.001092762
7       134.98947         153.5122    2.795585e-03       NA 0.005005935
8       134.99394         153.5137    1.614949e+02 35.83259 0.005207355
9       179.99879         134.9911    1.535509e+02 22.87662 0.001825272
   NewEplsar      Asfc       Smfc     HAsfc9   HAsfc81
4 0.01849256  4.309129   4.931042 0.66282946 0.7077260
5 0.02100014 37.843449 759.238285 6.98892403 6.7282198
6 0.01777594  2.897573   3.295669 0.07267422 0.1976211
7 0.01986757  5.112870  32.328292 0.56331153 1.5301426
8 0.01996242  4.673498   6.898712 0.49849387 1.3522344
9 0.01807974  6.785818  11.805786 0.72980153 0.9899596
```

-----

# Save data

## Format name of output file

``` r
file_out <- "TFE_use-wear"
```

The files will be saved as “\~/TFE\_use-wear.\[ext\]”.

## Write to XLSX

``` r
write.xlsx(list(data = data_final, units = units_var_table), 
           file = paste0(dir_out, file_out, ".xlsx"))
```

## Save R object

``` r
saveObject(data_final, file = paste0(dir_out, file_out, ".Rbin"))
```

## Show files information

``` r
files_out <- c(paste0(dir_out, file_out, ".xlsx"), paste0(dir_out, file_out, ".Rbin"))
md5_out <- md5sum(files_out)
info_out <- data.frame(files = basename(names(md5_out)), checksum = md5_out, row.names = NULL)
```

The checksum (MD5 hashes) of the exported files are:

``` 
              files                         checksum
1 TFE_use-wear.xlsx 4a7aed88b12199e5d33e05ad967d4d26
2 TFE_use-wear.Rbin 433a3db62e03eee5450b37da5616b1a7
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
[1] chron_2.3-55      R.utils_2.9.2     R.oo_1.23.0       R.methodsS3_1.8.0
[5] openxlsx_4.1.5   

loaded via a namespace (and not attached):
 [1] Rcpp_1.0.4.6    digest_0.6.25   rprojroot_1.3-2 backports_1.1.7
 [5] magrittr_1.5    evaluate_0.14   zip_2.0.4       rlang_0.4.6    
 [9] stringi_1.4.6   rmarkdown_2.3   stringr_1.4.0   xfun_0.15      
[13] yaml_2.2.1      compiler_4.0.2  htmltools_0.5.0 knitr_1.29     
```

RStudio version 1.3.1073.

-----

END OF SCRIPT
