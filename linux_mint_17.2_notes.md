---
title: "Linux Mint 17.2 Notes"
author: "Kwan Lowe"
date: "July 10, 2015"
output: html_document
---


These are my notes on installing the required software on Linux Mint 17.2 for the *Getting and Cleaning Data* class. They worked for me. YMMV. I'll post one for CentOS/RHEL in a short while.

**Upgrade R to 3.2.1**

Linux Mint 17.2 ships with R 3.0.2. This is not recent enough to install some of the required libraries:

```
sudo apt-get remove r-base-core
sudo echo "deb http://cran.rstudio.com/bin/linux/ubuntu trusty/" >> /etc/apt/sources.list
gpg --keyserver keyserver.ubuntu.com --recv-key E084DAB9
gpg -a --export E084DAB9 | sudo apt-key add -
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install r-base
```

**Baseline**

The gcc toolchain (including C and C++ compilers) is needed to build many of the R packages:

```
sudo apt-get install gcc g++
```

The git utilities (needed for RStudio version control). Check the References below for instructions on using Git/Rstudio:

```
sudo apt-get install git-core
```

**Install R Packages**

**Install RStudio**

Go to https://www.rstudio.com/products/rstudio/download/
Download the Installer package for Ubuntu 12.04+
Double click the *.deb package to kick off the installer.


**XML**
From the Linux shell prompt:

```
sudo apt-get install libxml2-dev
```

From an R session:
```{r}
install.packages("XML")
```

**xlsx**

From the Linux shell prompt:
```
sudo apt-get install openjdk-7-jdk libpcre3-dev liblzma-dev libbz2-dev
```

From an R session:
```{r}
install.packages("Rjava")
install.packages("xlsx")
```

*CAVEAT: If you really, really, really want reproducibility of results, please note that there are differences in Java implementations. For example, Java 8 vs Java 7 floating point implementations can lead to rounding differences. Implementations on different CPUs may also lead to differing results.*


**dplyr**

From an R session:
```{r}
install.packages("dplyr")
```

**data.table**

From an R session:
```{r}
install.packages("data.table")
```

**jsonlite**

From an R session:
```{r}
install.packages("jsonlite")
```

**swirl**

From an R session.
```{r}
install.packages("swirl")
library(swirl)
install_from_swirl("Getting and Cleaning Data")
swirl()
```

**Proxy Considerations**

If you are running behind an NTLM firewall/proxy, the CNTLM package appears to be the cleanest way to get packages installed:
From the Linux shell prompt:

```
sudo apt-get install cntlm
cntlm -h
```

This will prompt for a username/password. Once it completes it will output the password hash. Copy the output to the clipboard. Next, edit /etc/cntlm.conf and find the password stanza:

```
sudo vi /etc/cntlm.conf
```

It will look something like this:
```
Username        klowe
Domain          IT-DEV
PassLM          7C304268F39ED223EBDAAB456798C01F
PassNT          B078EBB4EA6C2D694E963043EFAF5B4F
PassNTLMv2      7DF0FAF50C7C92BE9BD3F3DAA1ECA4CB 
```

Replace those lines with the output from the cntlm -h command. Start the cntlm proxy with:
```
sudo service cntlm start
```

From an R session:
```{r}
sys.setenv(http_proxy="http://localhost:3128")
```

If all else fails, you can install from local packages. This can be a painful process as dependencies are not automatically satisfied, but will work if you just don't have network access. This requires that you minimally have the devtools package installed. 
```
install.packages("devtools")
```

Once that is complete, download the package tarball from CRAN and extract to a local directory. Then in an R session:
```{r}
library(devtools)
install('/path/to/r/package')
```

***References:***

http://sysads.co.uk/2014/06/install-r-base-3-1-0-ubuntu-14-04/

http://stackoverflow.com/questions/5339796/loading-an-r-package-from-a-custom-directory

http://r-pkgs.had.co.nz/git.html

https://support.rstudio.com/hc/en-us/articles/200532077-Version-Control-with-Git-and-SVN

http://www.lepem.ufc.br/jaa/r-plugin.html#r-plugin-use

http://stackoverflow.com/questions/22797964/is-inconsistency-in-rounding-between-java-7-and-java-8-a-bug


