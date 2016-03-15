# rstan cheat sheet

## STAN code blocks

- functions
- data
- transformed data
- parameters 
- transformed parameters
- model
- generated quantities
- any variable definitions must be at the beginning of the block

## STAN containers

- matrix[M, N] x
- real x[M, N]
- vector[M] v
- int<lower=1>[N] R

## STAN loops

for (i in 1:N) {
  ...
}

## STAN sampling statements

- x ~ normal(mu, sigma)
- (is the same as: increment_log_prob(normal_log(x, mu, sigma)))
- ...

## compile model 

- model <- rstan::stan_model(file=...)
- model <- rstan::stan_model(model_code=...)

## sample

- data is reused from the workspace, argument data=list(...) not needed
- sampls <- stan(file=...) # compile and fit
- sampls <- stan(model_code=...) # compile and fit
- sampls <- stan(fit=...) # fit, using a previously compiled model
- sampls <- stan(fit=..., data=list(...)) # pass data explicitly
- sampls <- sampling(model, ...)

## initialisation

- stan(... , init=init, seed=seed)
- init <- "random" 
- init <- function(chain_id) list(mu=..., sigma=...)
- init <- list(list(mu=...,sigma=...), list(mu=..., sigma=...), ...)


## parallel sampling

- stan(... , chains=8, cores=8)
- sflist2stanfit(parallel::mclapply(1:4, mc.cores=4, function(i) stan(..., chains=1, chain_id=i)))


## chain diagnostics

- traceplot(sampls, pars=c("mu", "sigma"))
- stan_diag(sampls) # acceptance rate, log-posterior
- stan_scat(sampls, c("mu", "sigma")) # scatter plot
- stan_rhat(sampls) # rhat statistic
- stan_mcse(sampls) # monte-carlo SE / posterior SD
- stan_ess(sampls) # effective sample size / sample size
- stan_ac(sampls) # autocorrelation
- pairs(sampls) # scatter plot matrix


## summaries

- print(sampls)
- summary(sampls)
- show(sampls)
- plot(sampls, pars=c("mu", "sigma")) # interval plots
- stan_hist(sampls, pars=...) # histograms
- stan_dens(sampls, pars=...) # density plots


## extracting samples

- extract(sampls, pars=c("mu", "sigma")) # list("mu", "sigma) not including warmup
- extract(sampls, permuted=FALSE) # array of dim (step, chain, variable), w/o warmup
- extract(sampls, permuted=FALSE, inc_warmup=TRUE) # w/ warmup


