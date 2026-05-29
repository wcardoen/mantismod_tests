# mantistests

#### Notes by WRC:

* Update: 05/28/2026
  - The `mantis` python package was updated to support GPUs & CPUs.
    It is now called `mantismod`.
  - The `R` code has been converted to a new package rmantismod
    which has a new function `mantis_checkcuda()`
  - The `R` package has now a `help`.
  - **REMOVE** the `~/.Renviron` file.<br> 
    The `reticulate` package is now completely incorporated in the code.

* Update: 05/29/2026
  - `rproj`: renamed into `mantisproj`
  - `mantis`: renamed into `mantismod`


#### Requirements

##### 1. `mantismod` (Python)

The installation of the `mantismod` package is described [here](https://github.com/wcardoen/mantisproj/blob/main/mantismod/README.md).

##### 2. `rmantismod` (R)

The installation of the `rmantismod` package can ge found [here](https://github.com/wcardoen/mantisproj/blob/main/README.md).

#### Testing

* Download the `mantistests` repo in the directory `$TESTDIR`
  ```bash
  cd $TESTDIR
  git clone https://github.com/wcardoen/mantistests.git
  ```

* Download the `mantis` foundation models
  ```bash
  cd $TESTDIR/mantistests
  mkdir models
  R
  >library(rmantismod)
  >mantis_download_weights(use_covariate = T, dest_dir = "./models")
  >mantis_download_weights(use_covariate = F, dest_dir = "./models")
  ```

* Run the `Python` tests
  
  Make sure that the `python` installation which contains `mantismod`
  is prepended to `PATH`.

  ```bash
  echo $(which python3)  # Make sure this is the python3 installed in uv
  cd $TESTDIR/mantistests/tests
  python3 check_python.py >& check_python.out
  python3 check_mantis_bench.py >& check_mantis_bench.out
  ```

* Run the `R` tests

  - Make sure that you invoke `R` which contains the `rmantismod` package.
  - Make sure that `PYTHONEXE` is set correctly.

  ```bash
  cd $TESTDIR/mantistests/rtests
  Rscript runtest.R >& runtest.out
  ```

* Compare the results

  Comapre the results from `check_mantis_bench.out` with `runtest.out`

