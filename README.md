# Handling missing data with Amelia in python


Data carpentry/engineering is decisive in determining the [accuracy and confidence](http://www.science.smith.edu/~nhorton/muchado.pdf) of ML, AI, and statitical models as extracted data contains missing values. Unlike most human beings, data has a tendency to "go missing". Garbage data into models means garbage predictions out of models. Missing data values are best handled with _multiple imputation_. Helping that since 2001 some kind folks at Harvard have offered the software [Amelia](https://github.com/IQSS/amelia):

> The package implements a new expectation-maximization with bootstrapping algorithm that **works faster, with larger numbers of variables**, and is far easier to use, than various Markov chain Monte Carlo approaches. The program also improves imputation models by allowing researchers to put Bayesian priors on individual cell values [...] It also includes features to **accurately impute cross-sectional datasets, individual time series, or sets of time series for different cross-sections**. A full set of graphical diagnostics are also available. The program is easy to use[...]; both a simple command line and extensive graphical user interface are included.[<sup>1: Journal of Statistical Software</sup>](https://www.jstatsoft.org/article/view/v045i07)

`Amelia` has its home in `R` as the typical language for applied [statistical science](https://stats.stackexchange.com/q/6/207649). The language for machine learning, especially in business, is often `python` (think about TensorFlow, SciPy, Scikit-learn, and PyTorch). Rather than being lost in translation, python machine learners can use [rpy2](https://github.com/rpy2/rpy2). `rpy2` may require a bit of attention to be setup for python to be able to catch Amelia.

### Alternatives
Before continuing to setup Amelia with rpy2, rest assured that alternatives exist. In python, [`miceforest`](https://github.com/AnotherSamWilson/miceforest) appears to be the best documented package, building upon a [book](https://stefvanbuuren.name/fimd/) and its associated R package [mice](https://www.jstatsoft.org/article/view/v045i03) using chained equations with predictive _"mean matching"_. [An unpublished book compares the two packages](https://bookdown.org/mike/data_analysis/application-7.html#amelia): 

> - Amelia use bootstrap based EMB algorithm (**faster and robust** to impute many variables including cross sectional, time series data etc)
> - Amelia use parallel imputation feature using **multicore CPUs**.
> - With Amelia, all variables follow Multivariate Normal Distribution (MVN). Hence, this package works best when data is MVN, or transformation to normality.
> 
> - MICE imputes data on variable by variable basis whereas MVN (Amelia) uses a joint modeling approach based on multivariate normal distribution.
> - MICE can handle different types of variables while the variables in MVN (Amelia) need to be normally distributed or transformed to approximate normality.
> - MICE can manage imputation of variables defined on a subset of data whereas MVN (Amelia) cannot.

A review in [the journal of statistical software compared](http://www.science.smith.edu/~nhorton/muchado.pdf) the official R mice package (and other available software) with Amelia. A [comparison](https://www.researchgate.net/publication/270447507_Using_Multiple_Imputation_for_Vote_Choice_Data_A_Comparison_across_Multiple_Imputation_Tools) of the two also exists for election results. Another yet unpublished comparison of the two stating that ["MICE PMM (predictive mean matching) is worryingly overconfident"](https://faculty.washington.edu/cadolph/mle/topic11.p.pdf).

<small><small>Otherwise, the support for multiple imputation in python is wobly: `sklearn`'s [InteractiveImputer](https://scikit-learn.org/stable/modules/generated/sklearn.impute.IterativeImputer.html) "is still experimental". Building on the R package [missforest](https://cran.r-project.org/web/packages/missForest/missForest.pdf), [`missingpy`](https://github.com/epsilon-machine/missingpy) supports knn and random forest impution. Despite the commitment to "plan to add other imputation tools in the future" it was last maintained in 2018. The [Python Data Science Handbook](https://github.com/jakevdp/PythonDataScienceHandbook/blob/master/notebooks/03.04-Missing-Values.ipynb) leans mostly on NumPy and Pandas. Ongoing projects like [impyute](https://github.com/eltonlaw/impyute), [pypots](https://github.com/WenjieDu/PyPOTS), and [ycimpute](https://github.com/OpenIDEA-YunanUniversity/ycimpute) could benefit from documenting the advanced features that enable valid and reliable predictions. [`mipy`](https://github.com/bcb/mipy) v.0.0.1 was last maintained in 2016.</small></small>

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
