# STAN/rstan cheat sheet

- **STAN** [Reference](http://mc-stan.org/documentation/) | **rstan** [Reference](https://cran.r-project.org/web/packages/rstan/rstan.pdf) [Vignette](https://cran.r-project.org/web/packages/rstan/vignettes/rstan_vignette.pdf) 

## STAN code blocks

- `functions` | `data` | `transformed data` | `parameters` | `transformed parameters` | `model` | `generated quantities`
- any variable definitions should be at the beginning of the block
- end all lines with semicolon, dont use dots in variable names

## STAN containers

- `matrix[M, N] x` | `real x[M, N]` | `vector[M] v` | `int<lower=1> R[N]`

## STAN loops

`for (i in 1:N) { ...  }`

## STAN sampling statements

- `x ~ normal(mu, sigma)`
- (is the same as: `increment_log_prob(normal_log(x, mu, sigma))`)
- ...

## compile model 

- `model <- stan_model(file=...)` # code in file
- `model <- stan_model(model_code=...)` # code in string variable
- compiled models are hashed and reused; the same model is not compiled twice

## sample

- data is reused from the workspace, argument `data=list(...)` not needed
- `sampls <- stan(file=...)` # compile from file and sample
- `sampls <- stan(model_code=...)` # compile from string and sample
- `sampls <- stan(fit=...)` # sample, using an object returned by previous call of `stan`
- `sampls <- stan(fit=..., data=list(...))` # pass data explicitly
- `sampls <- sampling(model, ...)` # sample, using compiled object from `stan_model`
- `sampls <- sampling(model, pars="mu")` # sample, only save parameter mu

## reproducibility

- `set.seed(2343); stan(... , init=init, seed=123)`
- where `init <- list(list(mu=...,sigma=...), list(mu=..., sigma=...), ...)`
- or `init <- function(chain_id) list(mu=..., sigma=...)`


## parallel sampling

- `stan(... , chains=8, cores=8)`
- `sflist2stanfit(parallel::mclapply(1:4, mc.cores=4, function(i) stan(..., chains=1, chain_id=i)))`


## chain diagnostics

- `traceplot(sampls, pars=c("mu", "sigma"))`
- `stan_diag(sampls)` # acceptance rate, log-posterior
- `stan_scat(sampls, c("mu", "sigma"))` # scatter plot
- `stan_rhat(sampls)` # rhat statistic
- `stan_mcse(sampls)` # monte-carlo SE / posterior SD
- `stan_ess(sampls)` # effective sample size / sample size
- `stan_ac(sampls)` # autocorrelation
- `pairs(sampls)` # scatter plot matrix


## summaries

- `print(sampls)`
- `summary(sampls)` # `print` but for each chain
- `show(sampls)`
- `plot(sampls, pars=c("mu", "sigma"))` # interval plots
- `stan_hist(sampls, pars=...)` # histograms
- `stan_dens(sampls, pars=...)` # density plots


## extracting samples

- `extract(sampls, pars=c("mu", "sigma"))` # list("mu", "sigma) not including warmup
- `extract(sampls, permuted=FALSE)` # array of dim (step, chain, variable), w/o warmup
- `extract(sampls, permuted=FALSE, inc_warmup=TRUE)` # w/ warmup


## assessing and comparing model fit

- R package `loo` provides functions `waic`, `loo`, also `compare`
- STAN code needs a `generated quantities` block that calculates variable `log_lik`
- `library(loo); log_lik <- extract_log_lik(fit); loo(log_lik)`

