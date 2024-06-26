# Setup

## Installation

rpy2 must be able to locate Amelia to run.[<sup>2</sup>](https://stackoverflow.com/a/47003754/3755989) What directory is rpy2 set to enable? What R Library contains Amelia? A few things determine these settings:

- R uses `install.packages()` to manage the [Library](https://www.r-bloggers.com/2020/10/customizing-your-package-library-location/), but users may prefer `pacman`, which also manages dependencies.[<sup>3</sup>](https://rdocumentation.org/packages/pacman/versions/0.5.1)

- Python packages and project environments are often managed by `pip`, `conda`, [virtualenv](https://pypi.org/project/virtualenv/), or containers like [Docker](https://github.com/rpy2/rpy2-docker).[<sup>4,</sup>](https://packaging.python.org/en/latest/guides/tool-recommendations/)[<sup>5</sup>](https://packaging.python.org/en/latest/guides/installing-using-pip-and-virtual-environments/)

- Operating Systems orgainze software like R, RStudio, and python differently. Linux distributions use different software managers such as `apt`, and Windows users may implement [scoop](https://scoop.sh/) or [SDKMAN](https://sdkman.io/).

#### _Debian_
- Packages installed from within R take precedence over apt.[<sup>6</sup>](https://cran.r-project.org/bin/linux/debian/#admihttps://cran.r-project.org/bin/linux/debian/#administration-and-maintenancenistration-and-maintenance)
- Python packages installed via apt take precedence over pip, because pip will know packages available at pipy are installed in _/usr/lib/python?/dist-package_. apt installs python packages in dist-package seperate from site-package.[<sup>7</sup>](https://wiki.debian.org/Python#Deviations_from_upstream) When pip is itself an apt installed dist-package it also installs packages from pipy as dist-packages, however, at _local/lib_.
- pip running from an virtual environment "venv" initiated by `virtualenv` locates packages in _./venv/lib/python?/site-package_. When rpy2 is installed here, it initializes R engines in the R default environment/Library.



<p style="text-align: center;">1. <b>R and RStudio</b>: What R Library contains Amelia?</p>

```bash
# bash

# install R and RStudio https://cran.r-project.org/bin/linux/debian/
$ sudo apt install r-base r-base-dev
$ apt list r-base # R version

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
> libPaths() # https://www.rdocumentation.org/packages/base/versions/3.6.2/topics/libPaths
> pacman::p_path() # https://www.rdocumentation.org/packages/pacman/versions/0.5.1/topics/p_path
> pacman::p_path(package = "Amelia")


# python
$ python3

>>> import rpy2.robjects.packages as rpacks  # requires rpy2 is installed as described below
>>> rutils = rpacks.importr('utils')
>>> utils.chooseCRANmirror(ind=1)
>>> from rpy2.robjects.vectors import rStrVector
>>> rutils.install_packages(rStrVector('Amelia'))
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
>>> print(rpy2.robjects.r) # R version
>>> print(rpy2.__version__) # rpy2 version
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
$ pip install rpy2==3.4.2 # Debian bullseye version
$ pip show rpy2 # ./venv/lib/python3.9/site-packages

# test rpy2
$ python3
>>> import rpy2
>>> from rpy2.robjects.packages import importr
>>> print(rpy2.robjects.r) # R version
>>> print(rpy2.__version__) # rpy2 version
>>> print(rpy2.situation) # R environment
>>> for row in rpy2.situation.iter_info():  # inspect R environment
...     print(row)
...

# check R Library directories in python
>>> base = importr('base')
>>> print(base._libPaths())

```

## Using Amelia in python
```python
# python

# import
import rpy2

from rpy2.robjects.packages import importr # https://rpy2.github.io/doc/v2.9.x/html/robjects_rpackages.html
from rpy2.robjects import packages as rpacks

from rpy2.robjects import r # to run R as str input

import pandas as pd
from rpy2.robjects import pandas2ri # requires pandas


# import Amelia
Amelia = importr('Amelia')

# Documentation: https://r.iq.harvard.edu/docs/amelia/amelia.pdf


# get R help
Amelia
# r_help = r_utils.help('help') # help within utils
# str(r_help)
# r_help_where = r_utils.help_search('help') # search help within utils











dir(Amelia)
Amelia.

# pass python code to the embedded R engine using `rpy2.rinterface` https://rpy2.github.io/doc/latest/html/rinterface.html#calling-python-functions-from-r


```
