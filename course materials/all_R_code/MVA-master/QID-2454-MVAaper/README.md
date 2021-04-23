[<img src="https://github.com/QuantLet/Styleguide-and-FAQ/blob/master/pictures/banner.png" width="888" alt="Visit QuantNet">](http://quantlet.de/)

## [<img src="https://github.com/QuantLet/Styleguide-and-FAQ/blob/master/pictures/qloqo.png" alt="Visit QuantNet">](http://quantlet.de/) **MVAaper** [<img src="https://github.com/QuantLet/Styleguide-and-FAQ/blob/master/pictures/QN2.png" width="60" alt="Visit QuantNet 2.0">](http://quantlet.de/)

```yaml

Name of QuantLet: MVAaper

Published in: Applied Multivariate Statistical Analysis

Description: Demonstrates maximum likelihood discrimination rule (ML rule) and calculates the apparent error rate for Swiss banknotes.

Keywords: apparent-error-rate, discrimination, estimation, discriminant-analysis, maximum-likelihood, financial, sas

See also: MVAaer, MVAaerbh, MVAdiscbh, MVAdisfbank, MVAdisnorm

Author: Wolfgang K. Haerdle, Vladimir Georgescu, Song Song, Awdesch Melzer
Author[SAS]: Svetlana Bykovskaya
Author[Python]: Matthias Fengler, Liudmila Gorkun-Voevoda

Submitted: Thu, June 05 2014 by Sergey Nasekin
Submitted[SAS]: Wen, April 6 2016 by Svetlana Bykovskaya
Submitted[Python]: Wed, September 9 2020 by Liudmila Gorkun-Voevoda

Datafile: bank2.dat

```

### PYTHON Code
```python

import pandas as pd
import numpy as np

x = pd.read_csv("bank2.dat", sep = "\s+", header=None)
xg = x[:100]
xf = x[100:200]

mg = xg.mean(axis = 0)
mf = xf.mean(axis = 0)
m = (mf + mg)/2
s = (99 * xg.cov() + 99 * xf.cov())/198
alpha = np.linalg.inv(s) @ (mg - mf)

miss1 = 0
for i in range(0, len(xg.iloc[:, 0])):
    if ((xg.iloc[i,:] - m) @ alpha) < 0:
        miss1 += 1

miss2 = 0
for i in range(0, len(xf.iloc[:, 0])):
    if ((xf.iloc[i,:] - m) @ alpha) > 0:
        miss2 += 1

aper = (miss1 + miss2)/len(x.iloc[:, 0])

print("Genuine banknotes classified as forged: {}".format(miss1))
print("Forged banknotes classified as genuine: {}".format(miss2))
print("APER (apparent error rate): {}".format(aper))
```

automatically created on 2020-09-10

### R Code
```r


# clear all variables
rm(list = ls(all = TRUE))
graphics.off()

# load data
x       = read.table("bank2.dat")
xg      = x[1:100, ]
xf      = x[101:200, ]

mg      = apply(xg, 2, mean)    # mean forged
mf      = apply(xf, 2, mean)    # mean genuine
m       = (mf + mg)/2           # total mean
s       = (99 * cov(xg) + 99 * cov(xf))/198  # sd
alpha   = solve(s) %*% (mg - mf)

miss1   = 0
for (i in 1:length(xg[, 1])) {
    if (as.numeric(xg[i, ] - m) %*% alpha < 0) {
        miss1 = miss1 + 1
    }
}

miss2   = 0
for (i in 1:length(xf[, 1])) {
    if (as.numeric(xf[i, ] - m) %*% alpha > 0) {
        miss2 = miss2 + 1
    }
}

print("Genuine banknotes classified as forged:")
print(miss1)
print("Forged banknotes classified as genuine:")
print(miss2)
print("APER (apparent error rate):")
aper = (miss1 + miss2)/length(x[, 1])
print(aper)

```

automatically created on 2020-09-10

### SAS Code
```sas


* Import the data;
data b2;
  infile '/folders/myfolders/Sas-work/data/bank2.dat';
  input x1-x6; 
run;

proc iml;
  * Read standardized data into a matrix;
  use b2;
    read all var _ALL_ into x; 
  close b2;
  
  xg = x[1:100, ];
  xf = x[101:200, ];

  mg    = xg[:,];                              * mean forged;
  mf    = xf[:,];                              * mean genuine;
  m     = (mf + mg)/2;                         * total mean;
  s     = (99 * cov(xg) + 99 * cov(xf))/198;   * sd;
  alpha = inv(s) * (mg - mf)`;
  
  miss1 = 0;
  do i = 1 to nrow(xg[, 1]);
    if ((xg[i, ] - m) * alpha < 0) then
      miss1 = miss1 + 1;
  end;
  
  miss2 = 0;
  do i = 1 to nrow(xf[, 1]);
    if ((xf[i, ] - m) * alpha > 0) then
      miss2 = miss2 + 1;
  end;
  
  aper = (miss1 + miss2)/nrow(x[, 1]);
  
  print 'Genuine banknotes classified as forged:', miss1;
  print 'Forged banknotes classified as genuine:', miss2;
  print 'APER (apparent error rate):', aper; 
quit;
```

automatically created on 2020-09-10