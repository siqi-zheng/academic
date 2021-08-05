---
title: 'Modelling the dynamics of a Chalmydia infection'
draft: false
authors: 
  - Siqi Zheng
event: ''
location: Blog
abstract: ""
summary: "A simple Bayesian approach to model the dynamics of a Chalmydia infection."

date: "2021-08-04T01:00:00Z"
links:
- icon: images
  icon_pack: fas
  name: assignment
  url: files/365a1.pdf
categories:
  - Project Demonstration
tags:
  - Biostatistics
  - Bayesian Statistics
featured: yes
projects: []
---

The assignment requirements can be found here above by clicking the 'assignment' icon.

- [Task 1](#task-1)
- [Task 2](#task-2)
  - [A note on selection of priors](#a-note-on-selection-of-priors)
  - [Dose 1](#dose-1)
    - [Prior Predictive Check for Model on Dose 1 Data](#prior-predictive-check-for-model-on-dose-1-data)
    - [Posterior Predictive Check for Model on Dose 1 Data](#posterior-predictive-check-for-model-on-dose-1-data)
    - [Leave one out cross validation for dose 1](#leave-one-out-cross-validation-for-dose-1)
  - [Dose 2](#dose-2)
    - [Prior Predictive Check for Model on Dose 2 Data](#prior-predictive-check-for-model-on-dose-2-data)
    - [Posterior Predictive Check for Model on Dose 2 Data](#posterior-predictive-check-for-model-on-dose-2-data)
    - [Leave one out cross validation for dose 2](#leave-one-out-cross-validation-for-dose-2)
  - [Dose 3](#dose-3)
    - [Prior Predictive Check for Model on Dose 3 Data](#prior-predictive-check-for-model-on-dose-3-data)
    - [Posterior Predictive Check for Model on Dose 3 Data](#posterior-predictive-check-for-model-on-dose-3-data)
    - [Leave one out cross validation for dose 3](#leave-one-out-cross-validation-for-dose-3)
  - [Dose 4](#dose-4)
    - [Prior Predictive Check for Model on Dose 4 Data](#prior-predictive-check-for-model-on-dose-4-data)
    - [Posterior Predictive Check for Model on Dose 4 Data](#posterior-predictive-check-for-model-on-dose-4-data)
    - [Leave one out cross validation for dose 4](#leave-one-out-cross-validation-for-dose-4)
  - [Remarks](#remarks)
- [Task 3](#task-3)

## Task 1

We have
$$  \frac{dE}{dt} = 0.004 - 2  E(t) - \kappa\_1C(t)  E(t) $$
$$ \frac{dC}{dt} = P  \kappa\_2  I(t) - \mu  C(t) - \kappa\_1 C(t) E(t) $$
$$ \frac{dI}{dt} = \kappa\_1 C(t)  E(t) - \gamma  I(t) - \kappa\_2  I(t) $$

The following codes load the data. Note that dose 1 to dose 4 correspond to dose 10 to 10<sup>4</sup>.

    data = readRDS("rank_et_al_2003_data.RDS")
    dose1 <- data[1:10,]
    dose2 <- data[11:20,]
    dose3 <- data[21:30,]
    dose4 <- data[31:40,]
    dose5 <- data[41:50,]

The following codes examine whether the model is written correctly.

    model <- function (t, y, parms) {
      dy1 <- (40 * 10 ^ (-4)) - 2 * y[1] - params[1] * y[2] * y[1]
      dy2 <- params[2] * params[3] * y[3] - params[4] * y[2] - params[1] * y[2] * y[1]
      dy3 <- params[1] * y[2] * y[1] - params[5] * y[3] - params[3] * y[3]
      list(c(dy1, dy2, dy3))
    }

    yini <- c(E = 0.96, C = 0.001, I = 0)
    params <- c(
    kappa1 = 1000,
    P = 1000,
    kappa2 = 1.3, #0.4-1.3
    C = 1.2, 
    gamma = 1.2
    )
    out <- ode(y=yini, t=seq(0,30,0.1), model, parameters)
    df_out <- as.data.frame(out)
    # An estimate of the differential equations
    summary(df_out$C)

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##   0.001   1.728   1.728  15.164   1.832 248.361

    # An overview of the data
    head(data)

    ##    t      C dose number
    ## 1  3  0.000 10^1      2
    ## 2  6 46.829 10^1      2
    ## 3  9  9.106 10^1      2
    ## 4 12 18.862 10^1      2
    ## 5 15 25.366 10^1      2
    ## 6 18 21.463 10^1      2

The following codes are for r2.stan (model from the differential equations.)

    functions {
      vector rhs(real t, vector y, 
                 real P, real kappa1, real kappa2, real gamma, real mu) {
      vector[3] dydt;
      dydt[1] = (40 * 1e-4) - 2 * y[1] - kappa1 * y[2] * y[1];
      dydt[2] = P * kappa2 * y[3] - mu * y[2] - kappa1 * y[2] * y[1];
      dydt[3] = kappa1 * y[2] * y[1] - gamma * y[3] - kappa2 * y[3];
      return dydt;
      }
    }

    data {
      int<lower=0> N;
      vector [N] y;
      real t[N]; // This must be an array!
        
      // Control
      int<lower=0, upper = 1> only_prior;
    }

    parameters {
      real<lower = 0, upper = 2000> P;
      real<lower = 0, upper = 2000> kappa1;
      real<lower = 0.01, upper = 1.5> kappa2;
      real<lower = 0.01, upper = 1.5> gamma;
      real<lower = 0.01, upper = 1.5> mu;
      real<lower = 0> c;
      }

    transformed parameters {
      // [a, b] makes a row_vector, [a, b]' makes a column vector
      // 0 is an int! 0.0 is a real!
      vector[N] C; // Outputted
      { // Local computation - isn't saved or outputted!
        vector[3] solution[N] = ode_bdf(rhs, [0.96, c, 0.0]', 0, t, P, kappa1, kappa2, gamma, mu);
        for(i in 1:N){
          C[i] = solution[i,2];
        }
      } 
    }

    model{
      c ~ uniform(0,1)
      P ~ uniform(0, 2000);
      kappa1 ~ uniform(0, 2000);
      kappa2 ~ uniform(0.01, 1.5);
      gamma ~ uniform(0.01, 1.5);
      mu ~ uniform(0.01, 1.5);
      if(only_prior == 0) {
        y ~ normal(C, 50);
      }
    }
    generated quantities {
      vector<lower=0>[N] y_pred;
      vector<lower=0>[N] log_lik;
      for (i in 1:N) {
        y_pred[i] = abs(normal_rng(C[i], 50)); // abs because minnimum is 0 and 30 is chosen because the standard deviation of the dataset is around 50, 
        // that is, if we are only interested in values larger than zero, then
        log_lik[i] = abs(normal_lpdf(y[i] |   C[i], 50));
      }
    }

Since when 0.4 < *κ*<sub>2</sub> < 1.4, the max of C(t) is between 100 < *C*(*t*) < 250 as 0.01 < *C*(0) < 1. Hence we may assume that the mean of *κ*<sub>2</sub> is 0.9 and the standard deviation of it is 0.3 so that it is highly probable to take values in the range of $[0.3,1.5]$. A uniform distribution is used because we do not know the exact distribution and it is hard to estimate the standard deviation of each parameter.

Therefore, we assume a weakly informative prior for other parameters in stan as well. In particular, uniform distribution that covers the value given as an example. This is largely due to two reasons. First, most parameters that require priors do not have sufficient information for us to set up a good distribution. Furthermore, since there are five different doses in the dataset, a distribution that can take values in a large range is preferred. As you will see later on, this choice provides a fair estimate of the parameters.

We assume the parameters in the 10<sup>3</sup> scale to be from 0 to 2000. The lower bound 0 is pretty self-explanatory, the upper bound is taken by 1000 + (1000 - lower bound). Similarly, we take 1.5 as upper bound for other parameters because of the range of *κ*<sub>2</sub>.

To estimate the rate of change of C at t = 0, we use the data for average C on day 3 and then divided by 3 (3 days) for each dose. This assumes that the rate of C is increasing so that C(0) is not underestimated, which is reasonable given that C increases until the dose takes effect. However, this could have some limitations. Hence we will further address this issue in task 3. C(0) will therefore be an average of all estimates of C(0) from 5 doses.

    mod <- cmdstan_model("r2.stan")

## Task 2

### A note on selection of priors

I have attempted different range of values for the uniform distribution, however, the posterior predictive check shows that the noise is higher than expected and the model does not fit very well. I have tried different models including normal distribution and uniform distribution with other parameters, but the actual result is not better than this. Hence uniform distribution is used. However, I would still suggest future researchers to collect more data (1 data per day) or apply other models specifically for this question.

### Dose 1

#### Prior Predictive Check for Model on Dose 1 Data

    mcmc_hist(fit$draws("C"))

![](img/dose1-prior-predictive-check-1.png)

    mcmc_hist(fit$draws("y_pred"))

![](img/dose1-prior-predictive-check-2.png)

#### Posterior Predictive Check for Model on Dose 1 Data

    yrep = fit$draws() %>% reshape2::melt() %>% filter(str_detect(variable, "y_pred")) %>%
      extract(col = variable, into = "ind", 
              regex = "y_pred\\[([0-9]*)\\]", 
              convert = TRUE) %>%
      pivot_wider(id_cols = c("chain","iteration"),
                  names_from = "ind") %>%
      select(-c("chain", "iteration")) %>% as.matrix

    ppc_stat(dose1$C, yrep, stat = "min")

![](img/dose1-posterior-predictive-check-1.png)

#### Leave one out cross validation for dose 1

    ## By default, it looks for something called "log_lik", but you can override this
    ## with the variables = argument. Eg if you called your log-likelihood "ll", 
    ## you could run  loo1 <- fit$loo(save_psis=TRUE, variable = "ll")
    loo1 <- fit$loo(save_psis=TRUE)

    print(loo1)

    ## 
    ## Computed from 4000 by 10 log-likelihood matrix
    ## 
    ##          Estimate  SE
    ## elpd_loo     49.1 0.4
    ## p_loo         0.3 0.3
    ## looic       -98.3 0.7
    ## ------
    ## Monte Carlo SE of elpd_loo is NA.
    ## 
    ## Pareto k diagnostic values:
    ##                          Count Pct.    Min. n_eff
    ## (-Inf, 0.5]   (good)     6     60.0%   2223      
    ##  (0.5, 0.7]   (ok)       0      0.0%   <NA>      
    ##    (0.7, 1]   (bad)      0      0.0%   <NA>      
    ##    (1, Inf)   (very bad) 4     40.0%   2420      
    ## See help('pareto-k-diagnostic') for details.

    plot(loo1)

![](img/loo1-1.png)

All except five of our points are good from the leave one out cross validation for model. This is not very great, but we may take a look at the estimate of C(0) to determine if we really identify C(0) fairly.

### Dose 2

#### Prior Predictive Check for Model on Dose 2 Data

    mcmc_hist(fit2$draws("C"))

![](img/dose2-prior-predictive-check-1.png)

    mcmc_hist(fit2$draws("y_pred"))

![](img/dose2-prior-predictive-check-2.png)

#### Posterior Predictive Check for Model on Dose 2 Data

    yrep2 = fit2$draws() %>% reshape2::melt() %>% filter(str_detect(variable, "y_pred")) %>%
      extract(col = variable, into = "ind", 
              regex = "y_pred\\[([0-9]*)\\]", 
              convert = TRUE) %>%
      pivot_wider(id_cols = c("chain","iteration"),
                  names_from = "ind") %>%
      select(-c("chain", "iteration")) %>% as.matrix

    ppc_stat(dose2$C, yrep2, stat = "min")

![](img/dose2-posterior-predictive-check-1.png)

#### Leave one out cross validation for dose 2

    loo2 <- fit2$loo(save_psis=TRUE)

    print(loo2)

    ## 
    ## Computed from 4000 by 10 log-likelihood matrix
    ## 
    ##          Estimate  SE
    ## elpd_loo     49.7 0.7
    ## p_loo         0.5 0.4
    ## looic       -99.4 1.4
    ## ------
    ## Monte Carlo SE of elpd_loo is NA.
    ## 
    ## Pareto k diagnostic values:
    ##                          Count Pct.    Min. n_eff
    ## (-Inf, 0.5]   (good)     6     60.0%   2138      
    ##  (0.5, 0.7]   (ok)       0      0.0%   <NA>      
    ##    (0.7, 1]   (bad)      0      0.0%   <NA>      
    ##    (1, Inf)   (very bad) 4     40.0%   1931      
    ## See help('pareto-k-diagnostic') for details.

    plot(loo2)

![](img/loo2-1.png)

### Dose 3

#### Prior Predictive Check for Model on Dose 3 Data

    mcmc_hist(fit3$draws("C"))

![](img/dose3-prior-predictive-check-1.png)

    mcmc_hist(fit3$draws("y_pred"))

![](img/dose3-prior-predictive-check-2.png)

#### Posterior Predictive Check for Model on Dose 3 Data

    yrep3 = fit3$draws() %>% reshape2::melt() %>% filter(str_detect(variable, "y_pred")) %>%
      extract(col = variable, into = "ind", 
              regex = "y_pred\\[([0-9]*)\\]", 
              convert = TRUE) %>%
      pivot_wider(id_cols = c("chain","iteration"),
                  names_from = "ind") %>%
      select(-c("chain", "iteration")) %>% as.matrix

    ppc_stat(dose3$C, yrep3, stat = "min")

![](img/dose3-posterior-predictive-check-1.png)

#### Leave one out cross validation for dose 3

    loo3 <- fit3$loo(save_psis=TRUE)

    print(loo3)

    ## 
    ## Computed from 4000 by 10 log-likelihood matrix
    ## 
    ##          Estimate  SE
    ## elpd_loo     51.0 1.3
    ## p_loo         3.7 2.1
    ## looic      -101.9 2.5
    ## ------
    ## Monte Carlo SE of elpd_loo is 0.0.
    ## 
    ## All Pareto k estimates are good (k < 0.5).
    ## See help('pareto-k-diagnostic') for details.

    plot(loo3)

![](img/loo3-1.png)

### Dose 4

#### Prior Predictive Check for Model on Dose 4 Data

    mcmc_hist(fit4$draws("C"))

![](img/dose4-prior-predictive-check-1.png)

    mcmc_hist(fit4$draws("y_pred"))

![](img/dose4-prior-predictive-check-2.png)

#### Posterior Predictive Check for Model on Dose 4 Data

    yrep4 = fit4$draws() %>% reshape2::melt() %>% filter(str_detect(variable, "y_pred")) %>%
      extract(col = variable, into = "ind", 
              regex = "y_pred\\[([0-9]*)\\]", 
              convert = TRUE) %>%
      pivot_wider(id_cols = c("chain","iteration"),
                  names_from = "ind") %>%
      select(-c("chain", "iteration")) %>% as.matrix

    ppc_stat(dose4$C, yrep4, stat = "min")

![](img/dose4-posterior-predictive-check-1.png)

#### Leave one out cross validation for dose 4

    loo4 <- fit4$loo(save_psis=TRUE)

    print(loo4)

    ## 
    ## Computed from 4000 by 10 log-likelihood matrix
    ## 
    ##          Estimate  SE
    ## elpd_loo     52.5 2.9
    ## p_loo         3.3 2.2
    ## looic      -105.0 5.9
    ## ------
    ## Monte Carlo SE of elpd_loo is 0.0.
    ## 
    ## All Pareto k estimates are good (k < 0.5).
    ## See help('pareto-k-diagnostic') for details.

    plot(loo4)

![](img/loo4-1.png)

### Remarks

From the prior predictive check, we can see the estimated distribution is more heavy-tailed than the actual distribution. From the posterior predictive check, T(y) is the skewness. The model captures the observed statistic to some extent for 4 doses, but leave one out cross validation shows that the model fits the the data for dose 3 and 4 better as all points from dose 3 and 4 are good.

## Task 3

    vec_c0 <- unlist(c0_estimate)
    hist(vec_c0, breaks=20)

![](img/unnamed-chunk-2-1.png) 
The advantage about estimating C(0) here is that this approach uses all available data and keep the statistical power of all data, since we are conditioning on all data; however, it takes a long time to produce the results. For instance, a laptop with one core will need more than an hour for this task.
