# Handling missing data with Amelia in python

_Without this project, many python users might not otherwise be familiar with, perhaps interested in, the best possible way to support models running on longitudinal or historical data._

Data engineering/carpentry is decisive in determining the [accuracy and confidence](http://www.science.smith.edu/~nhorton/muchado.pdf) of ML, AI, and statitical models where extracted data contains missing values. Garbage data into models means garbage predictions out of models. 

Missing data values are best handled with "multiple imputation". Helping that since 2001 some kind folks at Harvard have offered the software [Amelia](https://github.com/IQSS/amelia). [Background](background.md) compares Amelia with alternatives. Suffice to say that Amelia __uniquely__ models data structures like cross-sections, time-series, and ultimately longitudinal data; what IT folks call historical data.

Yet, Amelia is made for R, not python. Many python users are happy with this language and are not familiar with R. That is why I thought it might be useful to show how Amelia may be implemented in Python using rpy2. I am aware that this implementation may decrease Amelia's performance (speed) and that data will never be perfect. But Amelia, through rpy2, enables python users to support longitudinal data in science and in industry in a more optimal way.

In the face of missing values, python users with the help of Amelia and rpy2 are able to estimate accurate and appropriately confident model predictions, providing more actionable data for decision making!

# Get Started

Please just see the [how-to](how-to.ipynb).

TODO: containerize it and make available an image that runs anywhere!

TODO: convert to github gist

# Outlook: project and maintainance

If anybody is interested in taking this project up a nudge please do not hesistate to reach out. That might be:

- developing ways of automating the analysis of imputed datasets
- comparing performance in (a) R, (b) python with embedded R code, and (c) python with translated code.
- 

# Credits

Thanks to the developers of Amelia and rpy2!!!