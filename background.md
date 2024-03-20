
# Amelia

> The package implements a new expectation-maximization with bootstrapping algorithm that **works faster, with larger numbers of variables**, and is far easier to use, than various Markov chain Monte Carlo approaches. The program also improves imputation models by allowing researchers to put Bayesian priors on individual cell values [...] It also includes features to **accurately impute cross-sectional datasets, individual time series, or sets of time series for different cross-sections**. A full set of graphical diagnostics are also available. The program is easy to use[...]; both a simple command line and extensive graphical user interface are included.[<sup>1: Journal of Statistical Software</sup>](https://gking.harvard.edu/files/gking/files/amelia_jss.pdf)

`Amelia` has its home in `R` as the typical language for applied [statistical science](https://stats.stackexchange.com/q/6/207649). The language for machine learning, especially in business, is often `python` (think about TensorFlow, SciPy, Scikit-learn, and PyTorch). Rather than being lost in translation, python machine learners can use [rpy2](https://github.com/rpy2/rpy2). `rpy2` may require a bit of attention to be setup for python to be able to catch Amelia.

# Alternatives
When it comes to software for missing imputation in python, [`miceforest`](https://github.com/AnotherSamWilson/miceforest) appears to be the best documented package, building upon a [book](https://stefvanbuuren.name/fimd/) and its associated R package [mice](https://www.jstatsoft.org/article/view/v045i03). it uses chained equations with predictive _"mean matching"_. [An unpublished book compares the two packages](https://bookdown.org/mike/data_analysis/application-7.html#amelia): 

> - Amelia use bootstrap based EMB algorithm (**faster and robust** to impute many variables including cross sectional, time series data etc)
> - Amelia use parallel imputation feature using **multicore CPUs**.
> - With Amelia, all variables follow Multivariate Normal Distribution (MVN). Hence, this package works best when data is MVN, or transformation to normality.
> 
> - MICE imputes data on variable by variable basis whereas MVN (Amelia) uses a joint modeling approach based on multivariate normal distribution.
> - MICE can handle different types of variables while the variables in MVN (Amelia) need to be normally distributed or transformed to approximate normality.
> - MICE can manage imputation of variables defined on a subset of data whereas MVN (Amelia) cannot.

A review in [the journal of statistical software compared](http://www.science.smith.edu/~nhorton/muchado.pdf) the official R mice package (and other available software) with Amelia. A [comparison](https://www.researchgate.net/publication/270447507_Using_Multiple_Imputation_for_Vote_Choice_Data_A_Comparison_across_Multiple_Imputation_Tools) of the two also exists for election results. Another yet unpublished comparison of the two stating that ["MICE PMM (predictive mean matching) is worryingly overconfident"](https://faculty.washington.edu/cadolph/mle/topic11.p.pdf).

<small><small>Otherwise, the support for multiple imputation in python is wobly: `sklearn`'s [InteractiveImputer](https://scikit-learn.org/stable/modules/generated/sklearn.impute.IterativeImputer.html) "is still experimental". Building on the R package [missforest](https://cran.r-project.org/web/packages/missForest/missForest.pdf), [`missingpy`](https://github.com/epsilon-machine/missingpy) supports knn and random forest impution. Despite the commitment to "plan to add other imputation tools in the future" it was last maintained in 2018. The [Python Data Science Handbook](https://github.com/jakevdp/PythonDataScienceHandbook/blob/master/notebooks/03.04-Missing-Values.ipynb) leans mostly on NumPy and Pandas. Ongoing projects like [impyute](https://github.com/eltonlaw/impyute), [pypots](https://github.com/WenjieDu/PyPOTS), and [ycimpute](https://github.com/OpenIDEA-YunanUniversity/ycimpute) could benefit from documenting the advanced features that enable valid and reliable predictions. [`mipy`](https://github.com/bcb/mipy) v.0.0.1 was last maintained in 2016.</small></small>
