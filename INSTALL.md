# Notes by WRC:
# ------------
# Last Update: 05/28/2026
# What has been done?

- The mantis python package was updated to support GPUs & CPUs.
- The GPU version of mantis supports different CUDA torch builds
- The new R code is a wrapper for the new Python code.
- The R code has been converted to a new package rmantismod
  which has a new function mantis_checkcuda()
- There is a help for new R package.

IMPORTANT:
REMOVE the ~/.Renviron file. The functionality reticulate is now completely incorporated in the code.

# ======================================================================================================

# I launched a job on a kingspeak node with GPU support
[u0253283@granite2:~]$ salloc --nodes=1 --ntasks=6 --mem=40G --partition=rai-gpu-grn --qos=rai-gpu-grn --account=rai --time=04:00:00 --gres=gpu:1
salloc: Pending job allocation 1361948
salloc: job 1361948 queued and waiting for resources
salloc: job 1361948 has been allocated resources
salloc: Granted job allocation 1361948
Gsalloc: Waiting for resource configuration
salloc: Nodes grn025 are ready for job


# Let's assume you want to install the mantis software 
# in the directory $BASEDIR
e.g.
export BASEDIR=$HOME/trial


# PART 1: PYTHON packages:
# #######################
# A.Installation of Python proper:
#   =============================
cd $BASEDIR
module load uv
uv init mypy      
cd mypy
uv python install 3.13 -v
uv python pin 3.13 -v 

# If you want to install PyTorch with CUDA 12.8 support
uv add "mantis[cu128] @ git+https://github.com/wcardoen/rproj.git#subdirectory=mantis"
uv add pandas

The python executable is to be found here:
echo $(which $BASEDIR/mypy/.venv/bin/python3)
export PYTHONEXE=$(which $BASEDIR/mypy/.venv/bin/python3)
echo $PYTHONEXE


# B.Checking the Python installaton:
#   ===============================
cd $BASEDIR/mypy

[u0253283@grn025:mypy]$ uv run python
Python 3.13.12 (main, Mar  3 2026, 15:01:51) [Clang 21.1.4 ] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import torch
>>> torch.cuda.is_available()
True
>>> torch.cuda.device_count()
1
>>> torch.cuda.get_device_name(0)
'NVIDIA H200 MIG 1g.18gb'
>>> torch.__version__
'2.11.0+cu128'
>>> import mantis
>>> mantis.__version__
'0.1.0'



# PART 2: R packages:
# ##################
[u0253283@grn025:~]$ which R
/usr/bin/R

# A.Install REQUIRED packages:
#   =========================
# Check whether devtools, and reticulate are installed.
# If not, install them
R>install.packages(pkgs=c("devtools,"reticulate"), verbose=T)


# B.Install the rmantismod package:
#   ==============================
>library(devtools)
>install_github("wcardoen/rproj", subdir = "rmantismod")
>library(rmantismod)

# You can now use the help for rmantismod
# help(rmantismod)


# PART 3: Check :
# ##############
# A.Download the tests:
    ==================
cd $BASEDIR
git clone https://github.com/wcardoen/mantismod_tests.git mantis_tests


# B.Download the mantis models:
    ==========================
cd $BASEDIR/mantis_tests
mkdir models
R
>library(rmantismod)
>mantis_download_weights(use_covariate = T, dest_dir = "./models") 
>mantis_download_weights(use_covariate = F, dest_dir = "./models")

[u0253283@granite1:mantis_tests]$ ls -al models
total 2415794
drwxr-xr-x 2 u0253283 chpc       4096 May 28 18:18 .
drwxr-xr-x 2 u0253283 chpc       4096 May 28 18:17 ..
-rw-r--r-- 1 u0253283 chpc 1236914662 May 28 18:18 mantis_4w_cov.pt
-rw-r--r-- 1 u0253283 chpc 1236858332 May 28 18:18 mantis_4w_nocov.pt


# C.Run the tests:
    =============
    cd rtests
    # Edit the file PYTHONEXE
    Rscript runtest.R

NOTE: The Python tests are in the tests subdirectory
   


