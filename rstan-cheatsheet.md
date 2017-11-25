# Stan/RStan cheat sheets

[Stan](#Stan) | [RStan](#RStan)

- - - - 

# Stan

[Stan reference](http://mc-stan.org/users/documentation) 

### Code blocks

- `functions` | `data` | `transformed data` | `parameters` | `transformed parameters` | `model` | `generated quantities`
- semicolon after statements (variable assignments, sampling statements) 
- variable definitions at the beginning of blocks; new local variables can be defined at the beginning of a new `{}`-block 
- dont use dots in variable names


### Containers

- `matrix[M, N] x` | `real x[M, N]` | `vector[M] v` | `int<lower=1> R[N]` (and more)
- a `vector[K] x[N]` defines a collection of `N` vectors, each of length `K`; in R, store as `matrix(..., nrow=N, ncol=K)` or `array(..., dim=c(N,K))`


### Loops, conditionals

- `for (i in 1:N) { ...  }`
- `while (i <= N) { ... }`
- `if (i == 1) { ... } else if (i == 2) { ... } else { ... }`


### Sampling statements

- `x ~ normal(mu, sigma)`
- `target += normal_lpdf(x | mu, sigma)`

- - - - 

# RStan

[RStan reference](https://cran.r-project.org/web/packages/rstan/rstan.pdf) |
[Vignette](https://cran.r-project.org/web/packages/rstan/vignettes/rstan.html)

### Compile model 

- `model = stan_model(file='model.stan')` (model code in file)
- `model = stan_model(model_code=code)` (model code in string variable)


### Sample from a model

- data is reused from the workspace, argument `data=list(...)` is optional
- `sample = sampling(model, ...)` (sample, using compiled object from `stan_model`)
- `sample = sampling(model, pars='mu')` (sample, only save parameter `mu`)
- `sample = stan(file=...)` (compile from file and sample)
- `sample = stan(model_code=...)` (compile from string and sample)
- `sample = stan(fit=...)` (sample, using an object returned by previous call of `stan`)
- `sample = stan(..., data=list(...))` (pass data explicitly)


### Reproducibility

- `stan(... , init=init, seed=123)`
- where `init = list(list(mu=...,sigma=...), list(mu=..., sigma=...), ...)` where length of list = number of chains, or `init = function(chain_id) list(mu=..., sigma=...)`


### Parallel sampling

- `stan(... , chains=8, cores=8)`
- `sflist2stanfit(parallel::mclapply(1:4, mc.cores=4, function(i) stan(..., chains=1, chain_id=i)))`


### Chain summary statistics

- `traceplot(sample, pars=c('mu', 'sigma'))`
- `stan_diag(sample)` (acceptance rate, log-posterior)
- `stan_scat(sample, c('mu', 'sigma'))` (scatter plot)
- `stan_rhat(sample)` (Rhat statistic)
- `stan_mcse(sample)` (Monte-Carlo std error / posterior sd)
- `stan_ess(sample)` (effective sample size / sample size)
- `stan_ac(sample)` (autocorrelation)
- `pairs(sample)` (scatter plot matrix)


### Summary/print

- `print(sample)`
- `summary(sample)` (like `print` for each chain)
- `show(sample)`
- `plot(sample, pars=c('mu', 'sigma'))` (interval plots)
- `stan_hist(sample, pars=...)` (histograms)
- `stan_dens(sample, pars=...)` (density plots)


### Extracting draws

- `extract(sample, pars=c('mu', 'sigma'))` (returns list, not including warmup)
- `extract(sample, permuted=FALSE)` (returns 3d array (step, chain, variable), w/o warmup)
- `extract(sample, permuted=FALSE, inc_warmup=TRUE)`


### Misc

- leave-one-out cross validation: R package [`loo`](https://cran.r-project.org/web/packages/loo/vignettes/loo-example.html) 
- to sample from the prior, set length of data `N=0` and data `x=numeric()`
- suppress C++ compiler warnings: `withr::with_makevars(c(CXXFLAGS='-w'), {fit = stan(...)})`

