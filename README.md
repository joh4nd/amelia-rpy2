# Handling missing data with Amelia in python


Data carpentry/engineering is decisive in determining the [accuracy and confidence](http://www.science.smith.edu/~nhorton/muchado.pdf) of ML, AI, and statitical models as extracted data contains missing values. Unlike human beings, data has a tendency to "go missing". Garbage data into models means garbage predictions out of models. Missing data values are best handled with _multiple imputation_. Helping that since 2001 some kind folks at Harvard have offered the software [Amelia](https://github.com/IQSS/amelia):

> The package [...] works faster, with larger numbers of variables, and is far easier to use, than various Markov chain Monte Carlo approaches. [...] It also includes features to accurately impute cross-sectional datasets, individual time series, or sets of time series for different cross-sections. A full set of graphical diagnostics are also available. The program is easy to use[...]; both a simple command line and extensive graphical user interface are included.[<sup>1: Journal of Statistical Software</sup>](https://www.jstatsoft.org/article/view/v045i07)

`Amelia` has its home in `R` as the typical language for applied [statistical science](https://stats.stackexchange.com/q/6/207649). The language for machine learning, especially in business, is often `python` (think about TensorFlow, SciPy, Scikit-learn, and PyTorch). Rather than being lost in translation, python machine learners can use [rpy2](https://github.com/rpy2/). `rpy2` may require a bit of attention to be setup for python to be able to catch Amelia.

###### There is modest support for multiple imputation in python: `sklearn`'s [InterativeImputer](https://scikit-learn.org/stable/modules/generated/sklearn.impute.IterativeImputer.html) "is still experimental". The [Python Data Science Handbook](https://github.com/jakevdp/PythonDataScienceHandbook/blob/master/notebooks/03.04-Missing-Values.ipynb) leans mostly on NumPy and Pandas. Ongoing projects like [impyute](https://github.com/eltonlaw/impyute), [pypots](https://github.com/WenjieDu/PyPOTS), and [ycimpute](https://github.com/OpenIDEA-YunanUniversity/ycimpute) could benefit from documenting the advanced features that enable valid and reliable predictions. [`mipy`](https://github.com/bcb/mipy) v.0.0.1 was last maintained in 2016.

## Installation

rpy2 must be able to locate Amelia to run.[<sup>1</sup>](https://stackoverflow.com/a/47003754/3755989) What directory is rpy2 set to enable? What R Library contains Amelia? A few things determine these settings:

- R uses `install.packages()` to manage the [Library](https://www.r-bloggers.com/2020/10/customizing-your-package-library-location/), but users may prefer `pacman`, which also manages dependencies.[<sup>2</sup>](https://rdocumentation.org/packages/pacman/versions/0.5.1)

- Python packages and project environments are often managed by `pip`, `conda`, [virtualenv](https://pypi.org/project/virtualenv/), or containers like [Docker](https://github.com/rpy2/rpy2-docker).[<sup>3,</sup>](https://packaging.python.org/en/latest/guides/tool-recommendations/)[<sup>4</sup>](https://packaging.python.org/en/latest/guides/installing-using-pip-and-virtual-environments/)

- Operating Systems orgainze software like R, RStudio, and python differently. Linux distributions use different software managers such as `apt`, and Windows users may implement [scoop](https://scoop.sh/) or [SDKMAN](https://sdkman.io/).

#### _Debian_
- Packages installed from within R take precedence over apt.[<sup>5</sup>](https://cran.r-project.org/bin/linux/debian/#admihttps://cran.r-project.org/bin/linux/debian/#administration-and-maintenancenistration-and-maintenance)
- Python packages installed via apt take precedence over pip, because pip will know packages available at pipy are installed in _/usr/lib/python?/dist-package_. apt installs python packages in dist-package seperate from site-package.[<sup>6</sup>](https://wiki.debian.org/Python#Deviations_from_upstream) When pip is itself an apt installed dist-package it also installs packages from pipy as dist-packages, however, at _local/lib_.
- pip running from an virtual environment "venv" initiated by `virtualenv` locates packages in _./venv/lib/python?/site-package_.



<p style="text-align: center;">1. <b>R and RStudio</b>: What R Library contains Amelia?</p>

```bash
# bash

# install R and RStudio https://cran.r-project.org/bin/linux/debian/
$ sudo apt install r-base r-base-dev
$ apt list r-base # version 4.0.4-1

# check R environment variable to Library
$ cat /etc/R/Renviron | grep LIB
$ cat /usr/lib/R/etc/Renviron | grep LIB

# install Amelia
$ sudo apt install r-cran-amelia
$ apt show r-cran-amelia # Version: 1.7.6-1+b1
$ cd /usr/lib/R/site-library/ # https://cran.r-project.org/bin/linux/debian/#pathways-to-r-packages


# R
$ R 

# install
> install.packages('Amelia')
> install.packages('pacman')
> pacman::p_install("Amelia")

# check Library directories
> .libPaths() # https://www.rdocumentation.org/packages/base/versions/3.6.2/topics/libPaths
> pacman::p_path() # https://www.rdocumentation.org/packages/pacman/versions/0.5.1/topics/p_path
> pacman::p_path(package = "Amelia")

```

<p style="text-align: center;">2. <b>python</b>: What directory is rpy2 set to enable?</p>

```bash
# bash

# rpy2 via apt https://packages.debian.org/bullseye/python3-rpy2
$ sudo apt install python3-rpy2
$ apt list --installed python3-rpy2 # version
$ dpkg -L python3-rpy2 # directories /usr/lib/python3/dist-packages/

# rpy2 via pip
$ pip install rpy2 # Requirement already satisfied
$ pip show rpy2 # version 3.4.2, Location: /usr/lib/python3/dist-packages

# Amelia https://packages.debian.org/bullseye/r-cran-amelia
$ sudo apt install r-cran-amelia
$ apt list --installed r-cran-amelia
$ dpkg -L r-cran-amelia


## python default environment
$ python3

# test rpy2 https://rpy2.github.io/doc/latest/html/introduction.html
>>> import rpy2
>>> from rpy2.robjects.packages import importr
>>> print(rpy2.robjects.r) # R version 4.0.4 ~
>>> print(rpy2.__version__) # rpy2 version 3.4.2
>>> print(rpy2.situation) # R environment
>>> for row in rpy2.situation.iter_info():  # inspect R environment
...     print(row)
...
>>> # print(rpy2.robjects.r('1+2')) # run R code as string

# check R Library directories in python
>>> base = importr('base')
>>> print(base._libPaths())
>>> print(base._Library)
>>> print(base._Library_site)



## python in virtual environment
$ pip install virtualenv
$ cd # to desired directory of virtual environment
$ virtualenv venv
$ source ./venv/bin/activate
$ pip install 'rpy2==3.4.2' # same version as default env
$ pip show rpy2 # ./venv/lib/python3.9/site-packages

# test rpy2 https://rpy2.github.io/doc/latest/html/introduction.html
$ python3
>>> import rpy2
>>> from rpy2.robjects.packages import importr
>>> print(rpy2.robjects.r) # R version 4.0.4
>>> print(rpy2.__version__) # rpy2 version 3.5.11
>>> print(rpy2.situation) # R environment
>>> for row in rpy2.situation.iter_info():  # inspect R environment
...     print(row)
...

# check R Library directories in python
>>> base = importr('base')
>>> print(base._libPaths())
>>> print(base._Library)
>>> print(base._Library_site)

```

## Using Amelia in python
```python
# python

# import
import rpy2
import pandas as pd
from rpy2.robjects.packages import importr
from rpy2.robjects import r
from rpy2.robjects import pandas2ri # requires pandas

r_utils = importr('utils')
r_help = r_utils.help('help') # help within utils
str(r_help)
r_help_where = r_utils.help_search('help') # search help within utils

# run
Amelia = importr('Amelia')
dir(Amelia)
Amelia.

# pass python code to the embedded R engine using `rpy2.rinterface` https://rpy2.github.io/doc/latest/html/rinterface.html#calling-python-functions-from-r


```
