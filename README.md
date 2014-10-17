SAEM_GFA
========
Gibbs Item Factor Analysis with Stochastic Approximation Expectation Maximization algorithm
==============================================================================================

1) First, change your working directory as necessary, then source the file "InitializeCode.R"

2) Next, you can run the following analysis options:

###Read in a list of the following settings for generating a response pattern...
structure=list(icc="ogive",          # Item Char Curve; "ogive" or "logistic" 
               Adist="unif",         # prior distribution of A's/loadings
               Aparams=c(0.3,1.7),   # parameters of A's/loadings' prior distribution
               Adim=1,               # 1 (univariate) or 2, 3, etc. multiple dimensions for multivariate
               bdist="norm",         # distribution of B/intercept
               bparams=c(0,0.8),     # parameters of B
               guess=FALSE,          # guessing ? TRUE/FALSE
               cdist="unif",         # if TRUE, guessing parameter distribution for 3PNO or 3PL
               cparams=c(0.05,0.3),  # bounds
               tmu=0,                # Theta Prior... e.g. 0, or multivariate c(0,0) # can be multidimensional
               tsigma=1,             # Theta Prior Variance, e.g. 1, or matrix(c(1,0,0,1),nrow=2,ncol=2), # can be multidimensional
               simfile="SimTest")    # Save to an ".rda" file with this name

### Then Run:
Gen<-GenerateTestData(j=30,n=1000,structure=structure)
# NOTE: j is the number of Items, n is the number of examinees.
# Gen will be a list of the parameters (called XI), a response pattern(RP), abilities (THETA), and the settings (structure).
# A file called "SimTest.rda" will also be created.

### When fitting a model, use the following settings:
settings=list(model="gifa",    # "gifa" (SAEM) or "irt" (EM)
              icc="ogive",     # Item Characteristic Curve "logistic" or "ogive" . GIFA only works for ogive 
              Adim=1,          # 1 = Univariate; or 2, 3, 4, etc. Multidimensional?
              guess=FALSE,     # Guessing? TRUE/FALSE
              fm="camilli",    # Factor analysis procedure: fa() methods. Choose from "pca","ml","minres","wls","gls" or "camilli", or "old"... for original method from Spring 2014.
              est="rm",       # Estimation method. For model("gifa") -> "off"=mean, "rm"=robbinsmonro, "sa"=simannealing;  For model("irt") -> "anr"=analytical newton-raphson, "nnr"=numerical newton-raphson
              estgain=1,       # For model("irt"): constant to slow down(decrease to decimal)/speed up(increase) newton cycles; for model("gifa") and est("rm"): exponent on denominator in Robbins-Monro squeeze
              burnin=150,      # MCMC Burn-In... Might implement other convergence criteria, acf? or Euclidean distance?
              quad="manual",   # "manual", or "gauss-hermite"
              nodes=15,        # nodes for quadrature, regardless of choice.
              gridbounds=c(-3.5,3.5), # "manual" quadrature bounds
              tmu=0,           # Theta Prior mean, can be multivariate, e.g. c(0,0)
              tsigma=1,        # Theta Prior sigma, can be multivariate, e.g. covariance matrix(Adim x Adim)
              eps=1e-4,        # Convergence criterion. max(abs(A(i+1)-A(i)))<"1e-4"
              esttheta="mcmc", # imputation = "mcmc", MAP = "map", EAP = "eap" 
              nesttheta=10,    # for esttheta("mcmc"), how many imputations?
              impute=FALSE,    # Not implemented yet, but hope to Impute missing data TRUE/FALSE
              chains=1,        # Not implemented yet, but multiple chains for convergence
              initialize="best", # Initialize parameter values randomly = "random", or using best guess = "best"
              zfirst=TRUE,     # For model("gifa"), Randomly sample Z first (TRUE) or Theta first (FALSE)
              record="on",     # Record the MCMC iterations; "on" or "off". Saved in estfile
              simfile="SimTest", # If comparing to Generated parameters, give the name of an ".rda" file
              estfile="ProcTest") # Save the Fit [(if record("on")) and MCMC] to an ".rda" file with the name given here.

### When running a fit, use:
Fit<-AnalyzeTestData(RP=Gen$RP,settings=settings)
# Algorithm is run on any N x J binary response pattern.
# Fit will be a list of fitted parameters (xi), parameter variances (xiError), theta fits (THAT), and a few other useful diagnostic items. More objects will be saved in an rdata file, e.g. "ProcTest.rda" 

