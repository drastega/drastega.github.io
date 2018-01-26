---
title: "Reduction of speckle interferometric binary stars"
author: "Denis Rastegaev"
date: "2018-01-26"
output: pdf_document
vignette: >
  %\VignetteIndexEntry{Reduction of binary stars}
  %\VignetteEngine{knitr::rmarkdown}
  %\VignetteEncoding{UTF-8}
---



## Workflow for reduction of speckle interferometric binary stars

### Short way
To obtain positional paramaters and magnitude difference between components of binary star just type


```r
obj_filename <- file.choose() # Choose file with binary star
dark_filename <- file.choose() # Choose dark file
flat_filename <- file.choose() # Choose flat field file
rho_theta_dm <- speckle_binary(object = obj_filename, dark = dark_filename, flat = flat_filename)
print(rho_theta_dm)
```

### Long way
Calculate statistics for object file

```r
obj_filename <- file.choose() # Choose file with binary star
obj_stat <- speckle_stat(obj_filename)
```

Calculate statistics for dark file

```r
dark_filename <- file.choose() # Choose file with dark frames
dark_stat <- speckle_stat(dark_filename)
```

Calculate average dark frame

```r
mid_dark <- middle_frame(filename = dark_filename, stat = dark_stat)
```

Calculate average flat field frame with average dark subtraction

```r
mid_flat <- middle_frame(filename = file.choose() , dark = mid_dark) # Choose file with flat fields
```

Calculate power spectrum with subtraction of neighboring frame

```r
ps_diff <- ps(obj_filename, dark = NULL, flat = NULL, diff = 1)
```

Calculate acf

```r
acf <- speckle_acf(ps_diff)
```

Find secondary peaks on acf

```r
rho_theta_first <- find_peak(acf)
```

Calculate power spectrum

```r
ps <- ps(obj_filename, dark = mid_dark, flat = mid_flat, diff = 0) # include stats
```

Power spectrum cosmetic operation

```r
ps_cosm <- ps_cosmetic(ps)
```

Calculate rho, theta and dm using algorithm described in Pluzhnik (2005)

```r
rho_theta_dm_ccd <- NONAME_FUNC(ps_cosm)
```

Find real rho and theta

```r
rho_theta_dm <- zqt(rho_theta_dm_ccd)
```

![Power spectrum](pics/Power_spectrum.png)