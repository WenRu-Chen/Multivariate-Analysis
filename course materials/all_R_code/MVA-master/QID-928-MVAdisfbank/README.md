[<img src="https://github.com/QuantLet/Styleguide-and-FAQ/blob/master/pictures/banner.png" width="888" alt="Visit QuantNet">](http://quantlet.de/)

## [<img src="https://github.com/QuantLet/Styleguide-and-FAQ/blob/master/pictures/qloqo.png" alt="Visit QuantNet">](http://quantlet.de/) **MVAdisfbank** [<img src="https://github.com/QuantLet/Styleguide-and-FAQ/blob/master/pictures/QN2.png" width="60" alt="Visit QuantNet 2.0">](http://quantlet.de/)

```yaml

Name of QuantLet: MVAdisfbank

Published in: Applied Multivariate Statistical Analysis

Description: Performs a Fisher discrimination analysis of the Swiss bank notes, computes the misclassification rates for the whole dataset and displays nonparametric density estimates of the projected data.

Keywords: discrimination, estimation, discriminant-analysis, Fisher, density, projection, Fisher LDA projection, nonparametric, plot, graphical representation, financial, sas

See also: MVAaer, MVAaper, MVAaerbh, MVAdiscbh, MVAdisnorm

Author: Zografia Anastasiadou
Author[SAS]: Svetlana Bykovskaya
Author[Python]: Matthias Fengler, Liudmila Gorkun-Voevoda

Submitted: Tue, January 11 2011 by Zografia Anastasiadou
Submitted[SAS]: Wen, March 30 2016 by Svetlana Bykovskaya
Submitted[Python]: Wed, September 9 2020 by Liudmila Gorkun-Voevoda

Datafile: bank2.dat

Example: Densities of projections of genuine and counterfeit bank notes by Fisher's discrimination rule.

```

![Picture1](MVAdisfbank.png)

![Picture2](MVAdisfbank_python.png)

![Picture3](MVAdisfbank_sas.png)

### PYTHON Code
```python

import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from KDEpy import FFTKDE

x = pd.read_csv("bank2.dat", sep = "\s+", header=None)
xg = x[:100]
xf = x[100:200]
mg = xg.mean(axis = 0)
mf = xf.mean(axis = 0)
m = (mf + mg)/2
w = 100 * (xg.cov() + xf.cov())
d = mg - mf
a = np.linalg.inv(w) @ d

yg = (xg - np.array([m]*100)) @ a
yf = (xf - np.array([m]*100)) @ a

xgtest = yg
sg = sum(xgtest < 0)            # Number of misclassified genuine notes

xftest = yf                     # Number of misclassified forged notes
sf = sum(xftest > 0)

fg_x, fg_y = FFTKDE(bw="silverman", kernel='gaussian').fit(np.array(yg)).evaluate()
ff_x, ff_y = FFTKDE(bw="silverman", kernel='gaussian').fit(np.array(yf)).evaluate()
                    
fig, ax = plt.subplots()
ax.plot(fg_x, fg_y, linestyle = "dashed")
ax.plot(ff_x, ff_y, c = "r")
ax.text(yf.mean()-0.03, 3.72, "Forged", color = "r")
ax.text(yg.mean()-0.03, 2.72, "Genuine", color = "blue")
plt.title("Densities of Projections of Swiss bank notes")
plt.ylabel("Densities of Projections")

plt.show()

```

automatically created on 2020-09-10

### R Code
```r


# clear all variables
rm(list = ls(all = TRUE))
graphics.off()

# reads the bank data
x = read.table("bank2.dat")

xg  = x[1:100, ]                # Group first 100 observations    
xf  = x[101:200, ]              # Group second 100 observations
mg  = colMeans(xg)              # Determine the mean for the seperate groups and overall sample
mf  = colMeans(xf)
m   = (mg + mf)/2
w   = 100 * (cov(xg) + cov(xf)) # matrix w for within sum-of-squares
d   = mg - mf                   # Difference in means
a   = solve(w) %*% d            # Determine the factors for linear combinations

yg = as.matrix(xg - matrix(m, nrow = 100, ncol = 6, byrow = T)) %*% a  # Discriminant rule for genuine notes
yf = as.matrix(xf - matrix(m, nrow = 100, ncol = 6, byrow = T)) %*% a  # Discriminant rule for forged notes

xgtest = yg
sg = sum(xgtest < 0)            # Number of misclassified genuine notes

xftest = yf                     # Number of misclassified forged notes
sf = sum(xftest > 0)

fg = density(yg)                # density of projection of genuine notes
ff = density(yf)                # density of projection of forged notes

# plot
plot(ff, lwd = 3, col = "red", xlab = "", ylab = "Densities of Projections", main = "Densities of Projections of Swiss bank notes", 
    xlim = c(-0.2, 0.2), cex.lab = 1.2, cex.axis = 1.2, cex.main = 1.8)
lines(fg, lwd = 3, col = "blue", lty = 2)
text(mean(yf), 3.72, "Forged", col = "red")
text(mean(yg), 2.72, "Genuine", col = "blue")

```

automatically created on 2020-09-10

### SAS Code
```sas


* Import the data;
data bank2;
  infile '/folders/myfolders/data/bank2.dat';
  input temp1-temp6;
run;

proc iml;
  * Read data into a matrix;
  use bank2;
    read all var _ALL_ into x; 
  close bank2;
  
  xg = x[1:100, ];   * Group first  100 observations;
  xf = x[101:200, ]; * Group second 100 observations;
  mg = xg(|:,|);
  mf = xf(|:,|);
  m  = (mg + mf)/2;
  w  = 100 * (cov(xg) + cov(xf));    * matrix w for within sum-of-squares;
  d  = mg - mf;                      * Difference in means;
  a  = inv(w) * d`;                  * Determine the factors for linear combinations;
  yg = (xg - repeat(m, 100, 1)) * a; * Discriminant rule for genuine notes;
  yf = (xf - repeat(m, 100, 1)) * a; * Discriminant rule for forged notes;
  
  xgtest = yg;
  sg = sum(xgtest < 0);  * Number of misclassified genuine notes;
  
  xftest = yf;
  sf = sum(xftest > 0);  * Number of misclassified forged notes;

  create plot var {"yg" "yf"};
    append;
  close plot;
quit;

data plot;
  set plot;
  rename yg = Genuine yf = Forged;
  ods graphics on;
  proc kde data=plot;
    title 'Densities of Projections of Swiss bank notes';
    univar Genuine Forged / plots = (densityoverlay) noprint;
  run;
  ods graphics off;
quit;




```

automatically created on 2020-09-10