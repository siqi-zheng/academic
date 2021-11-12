---
title: 'Outline for the research article'
draft: false
authors: 
  - Siqi Zheng
event: ''
location: Blog
abstract: ""
summary: ""

date: "2021-11-12T01:00:00Z"
categories:
  - Research (Open Source)
tags:
  - Mathematics
  - Research
featured: yes
projects: []
---

A comparison of reproducibility theories: a case study in Education from a Bayesian perspective

Introduction
Reproducibility of scientific theories is the core in scientific research (Holm Tetens, 2016). Scientists have proposed recommendations () and suggested criterion () for better scientific reproducibility, but the reproduction of an actual scientific research may still be very challenging even if the research article meets the expectations of these theories. In this article, the author demonstrates the challenges in reproducing a research article in Education from a junior statistician’s perspective, even when code and data are shared openly. The author investigated the implicit presumptions in the reproduction and how such presumptions may obstacle the reproduction of scientific research. From the negative results and failed attempts obtained by the author, this article may shed light on the design of reproducibility criterion.
1.1	Background of the research article
1.2	The theories of reproducibility and why the article meets theoretical expectations
1.2.1	https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=5562471:
When publishing computational results, including statistical analyses and simulation, provide links to the source-code (or script) version and the data used to generate the results to the extent that hosting space permits; (Full codes with limited data due to copyrights reasons. Make file is also provided)
Assign a unique ID to each version of released code, and update this ID whenever the code and data change. For example, researchers could use a version-control system for code and a unique identifier such as the Universal Numerical Fingerprint (http://thedata.org/book/ unf-implementation) for data. Such an identifier facilitates version tracking and encourages citation. (Dates are highlighted)
Include a statement describing the computing environment and software version used in the publication, with stable links to the accompanying code and data. Researchers might also include a virtual machine. A VM image with compiled code, sources, and data that can reproduce published tables and figures would let others explore the parameters round the publication point, examine the algorithms used, and build on that work in their own new research. (Computing environment is documented; software version is not available, but using many software means the version may not be helpful for reproduction and verification; Missing VM)
Use open licensing for code to facilitate reuse, as suggested by the Reproducible Research Standard. (Yes)
To encourage both wide reuse and coalescence on broad standards, publish data and code in nonproprietary formats whenever reasonably concordant with established research practices, opting for formats that are likely to be readable well into the future when possible. (Yes, comments are especially helpful)
1.2.2	https://www.federalreserve.gov/econresdata/feds/2015/files/2015083pap.pdf
system-software version; expected running time; set seed is included; Readme files should clearly delineate which files should be executed in what order to produce desired results; raw data and transformed series (not included due to copyright reasons, but what is the data cleaning files do not work? We may consider pseudo-data: how?) Programs that replicate estimation results should carry out the estimation.

1.3	The failed attempts and implicit prerequisite:
Biggest takeaway: how to install Linux on Win (Ubuntu). This seems like a painful process, but this actually takes less time that one may expect. Why change of system? Because GNU make does not work well without the Windows subsystem. An implicit prerequisite: to what extent do we expect scholars to be familiar with different operating systems?

After installing all packages and software applications, `make` still keep failing. Here are some suggestions for this situation.

1.	Details matter: `dependencies = True` is important in installing packages, otherwise users may fail to install `devtools`, `V8` and `rstan`. I failed a couple of times when dealing with installing `R` packages. 
a.	In deed, Windows R automatically installs the dependencies, but this is not true for Linux. For some of tools, one have to install them in R manually. 

2.	`unar` is not necessary for `Linux` and `Windows` to unzip the files. `Ubuntu`, the sub system, has its built-in application for unzipping files while `Windows` has even more options for manually unzipping files. However, you choose not to install it, the `Bash` script will not work since it has no alternative unzipping options. Therefore `unar` becomes necessary (though a bit redundant). Ideally, we can have a few extra lines to look for possible unzipping options on different systems. i.e. if the file can look for folder containing the expected files.

3. Then the next challenge is to solve the problem of downloading data. 

State_by_State_Wave_Charts_FY15_0.xlsx is missing from the original website and the new url has no file extension. Even worse, the computer stopped downloading some files for unknown reasons (maybe because I tried too many times today) and so the file was broken. The way I did was to save a back-up folder for all files. After I cleaned the `make` remainders, I could just create the data folders with the codes below:

``` shell
cp -a my_backup_destination/data/. /home/<user_name>/oa_online_broadband_rep/data/
```

For individual dataset backup and copy-and-paste, you can also do the following:

```shell
sudo cp -i /mnt/my_backup_destination/State_by_State_Wave_Charts_FY15_0.xlsx /home/<user_name>/oa_online_broadband_rep/data/sheeo/```
```

```shell
sudo cp -i /mnt/my_backup_destination/All-NBM-CSV-June-2014.zip /home/<user_name>/oa_online_broadband_rep/data/broadband/zip```
```

Deal with data copyright. A tricky question.

5. Important! `cut` is problematic because `Linux` does not allow `cut -f4 -f5` but `cut -f4,5` while `Mac OS` allows `cut -f4 -f5`. Therefore it will throw an error when running the `Bash` file. This is pretty subtle because to what extent should statisticians be familiar with cut function in different operating systems?
 
6. `Linux` sub system does not have Latex, this is also required to be installed before running `make`. `Mac OS` has built-in `framed.sty` for Latex while `Linux` does not. Therefore at the very end, an error will occur unless you install `sudo apt-get install texlive-latex-extra` this first. To ensure everything is fine, one may consider this `sudo apt-get install texlive-full` which takes up around 4GB of the disk space. R and MiKTeX have a hard time compiling .Rnw to pdf. Tex file is not working properly as well. It is a well-known problem without satisfactory answer yet. What to do? The ultimate solution is to use Knitr written for producing documents. It converts Sweave Tex to Tex first and then knits the file to pdf.

An attempt to integrate the files into reproducible Rmd 
1.	The second approach is step-by-step approach. Note that the datasets should be downloaded prior to statistical analysis.
2.	People who wish to reproduce the article should be somewhat familiar with the syntax of **Bash**, because the data cleaning process relies on Bash files.
3.	**SQLite** is another implicit prerequisite because broadband data are large and the author stored the data in SQL. However, I do not have SQLite on my computer. I took a closer look at the Bash files and realized that this can be done via R. Hence I analyzed the data with a programming language I am more comfortable with R. 
4.	The author used SQL to store and clean the data, and then used R to clean the broadband data again. This process may not be necessary and can be done solely based on R.

