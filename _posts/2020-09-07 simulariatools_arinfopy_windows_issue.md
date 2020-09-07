---
layout: post
title:  "Solving `importADSOBIN()` Python dependency error"
date:   2020-09-07 18:05:00 +0200
categories: python R arinfopy simulariatools
---


The [`simulariatools`](https://github.com/Simularia/simulariatools) R package includes the `importADSOBIN()` function to read **ADSO/BIN** files.
This function requires the Python package [`arinfopy`](https://github.com/Simularia/arinfopy). Given that the module is (still) not available through PyPI, some dependency issues between R and Python may arise, especially in Windows.

The most common problem occurs when the `arinfopy` package is not found in the active Python environment. In this case, the following error message is shown:

```
> library(simulariatools)
> test <- importADSOBIN("conc_ave_NOX", variable = "REL")
Error in py_module_import(module, convert = convert) : 
  ModuleNotFoundError: No module named 'arinfopy'
```

In order to solve it, we currently have to instruct `reticulate` to use the Python environment where `arinfopy` has been previously installed.

In the notes below, we assume to be working in Windows 10 with an active miniconda Python installation where `arinfopy` has been installed through `pip` as instructed in the [repository](https://github.com/Simularia/arinfopy) README file.

Firstly we need to restart R and load the `reticulate` library:
```
> library(reticulate)
```
Then, by using `conda_list()`, we list all the available Python environment. The function will return a data frame with the names and paths to the respective Python binaries of available environments. In this example we will get two environments:
```
> conda_list()
          name
1  r-miniconda
2 r-reticulate
                                                                          python
1                     C:\\Users\\TestUser\\AppData\\Local\\r-miniconda\\python.exe
2 C:\\Users\\TestUser\\AppData\\Local\\r-miniconda\\envs\\r-reticulate\\python.exe
```

The first row corresponds to the `base` environment available to conda, while the second one is the `reticulate` specific one. The number of available environments, depends on the specific active Python installation.

We now have to select the correct environment, with a working version of `arinfopy`:

```
> use_condaenv(condaenv = "r-miniconda", required = T)
```

At this point, `reticulate` will be able to select the required modules and everything should be working as expected. 

We can now test again if the data are correctly read by `importADSOBIN()`:
```
> library(simulariatools)
> test <- importADSOBIN("conc_ave_NOX", variable = "REL", verbose = TRUE)
> head(test)
```
