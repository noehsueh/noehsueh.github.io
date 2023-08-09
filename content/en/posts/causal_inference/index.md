---
title: "Causal Inference and Gender Pay Gap: A Bayesian Stats Exploration"
date: 2023-07-04
draft: false
---


This note was taken for a presentation I had to give for my Bayesian Stat class. 

## Introduction

Causal inference can be used to tries to answer questions of this sort: “What would happen if…?”

- Does smoking cause lung cancer?
- What would be the effect of increasing the minimum wage on employment rates?
- Does access to education have a causal impact on income levels?

It allows us to establish causal effects rather than just mere statistical association. 

It’s very common to use DAG to do modelling before doing causal inference. In simple terms, we define: $X, Y$ to r.v. and we say $X$ causes $Y$ if 

$$
X= f(U_X)\\\\
Y = f(U_Y, X)\\
$$

Or graphically,

```goat
                    .-.       .-.               
                   | X +---->| Y |                     
                    '-'       '-'           
```

Using a graphical model assumption will allows us to perform inference tasks on this model. 

## Case study: Gender Pay Gap (Brecha salarial de género)

In this case, we will use Causal Inference to study a simulated Gender Pay Gap example. This case was motivated by an example given in [The Mixtape](https://mixtape.scunning.com/03-directed_acyclical_graphs#discrimination-and-collider-bias). In 2019, Google found that they underpaid male worker after segmenting for job characteristics, in short, they run multiple regression for males and females worker for different groups based on job characteristics and found that they actually pay less to their males counterpart. 

![Headline for the news article.](resources/Screenshot_2023-07-02_at_17.11.00.png)



The issue here is that by looking the observable data, there’s a multitude of analysis that can be made and we still could find no evidence that women are being paid less, even at large organisations like Google. The way that we could account for this is by using causal modelling based on domain expertise. We know that job level $O$ affects earning $Y$ and if we consider $D$ discrimination as the treatment effect, it also affects $O$ and $Y$. But something that cannot be observable from the data is the individual skills $A$, which has an effect on both job level and earning. The DAG that we could use to model these causal relationships are the following:

![Untitled](resources/Untitled.png#center)

Now that we have our model, lets simulated our data with:

```r
N <- 10000
tb <- tibble(
  female = ifelse(runif(N)>=0.5,1,0),
  ability = rnorm(N),
  discrimination = female,
  occupation = 1 + 2*ability - 2*discrimination + rnorm(N),
  wage = 1 - 1*discrimination + 1*occupation + 2*ability + rnorm(N) 
)
```

The idea behind is that nodes in our DAG are r.v., so we could model our data generation process as follow:

$$
G_i \sim\mathcal{Ber}(0.5)\\\\
A_i \sim\mathcal{N(0,1)} \\\\
D_i = G_i \\\\
O_i = 1 + 2A_i -2D_i + \varepsilon_i \quad \varepsilon_i \sim \mathcal N(0,1)\\\\
Y_i = 1-1D_i+1O_i + 2A_i + \varepsilon_i \quad \varepsilon_i \sim \mathcal N(0,1)
$$

> This simulation hard-codes the data-generating process represented by the previous DAG. Notice that ability is a random draw from the standard normal distribution. Therefore it is independent of female preferences. And then we have our last two generated variables: the heterogeneous occupations and their corresponding wages. Occupations are increasing in unobserved ability but decreasing in discrimination. Wages are decreasing in discrimination but increasing in higher-quality jobs and higher ability. Thus, we know that discrimination exists in this simulation because we are hard-coding it that way with the negative coefficients both the occupation and wage processes.
> [1]

In this case, we are interested in the path from $D$ to $Y$; that is, we are interested in knowing the causal effect of how discrimination affects earning. We should note that there are multiple paths between these variables

- $D→Y$
- $D→O→Y$
- $D→O\leftarrow A→Y$

The first path indicate the casual effect of D on Y. Te second path models the path whereby Y is determined by job level, but before there’s the discrimination that has effect on the job level someone could get. Here’s $O$ is a mediator between $D$ and $Y$. While the third path is more complicated, as we have $A$ whose effects are unobserved but has influenced on job placement and earning. 

> The [second] path is not a backdoor path; rather, it is a path whereby discrimination is mediated by occupation before discrimination has an effect on earnings. This would imply that women are discriminated against, which in turn affects which jobs they hold, and as a result of holding marginally worse jobs, women are paid less. The [third] path relates to that channel but is slightly more complicated. In this path, unobserved ability affects both which jobs people get and their earnings.
> [2]

Considering our DAG model, we could perform various statistical analysis. First, if we simply regress $Y$ onto $D$.  This yields the total effect of discrimination as the weighted sum of both the direct effect of discrimination on earnings and the mediated effect of discrimination on earnings through occupational sorting. In short, we are not taking into account the effect that occupation could have on our models, the coefficient in this case would be grater than it actually is. 

By using simple linear regression with `lm`, we get:

```r
Coefficients:
(Intercept)  female        
1.947        -2.894
```

So, let’s say we decide to condition on $O$, which would be a proxy for modelling job characteristics. Note that this was sort of the analysis that Google made in order to come up with their results. In this case, we would be assuming that our model is the following:

{{< figure src="resources/graph_(10).png" align="center" width= "50%">}}
What do we mean by conditioning on a DAG? Any DAG may imply that some variables are independent of others under certain conditions. Informally, conditioning on a variable $Z$ means learning its value and then asking if $X$ adds any additional information about $Y$. If learning $X$ doesn’t give you any more information about $Y$, then we might say that $Y$ is independent of $X$ conditional on $Z$. This conditioning statement is sometimes written as: $Y ⊥ X|Z$. But then, how can we condition? One wat to hold this variable constant is by using regression, so in this case we can just consider the variable $O$ in our regression modelling. 

![Untitled](resources/Untitled%201.png)

Let’s then consider our Bayesian Regression model using weakly informative prior (so that we can later rediscover our original slopes):

$$
\begin{equation}
\begin{array}{lcrl} 
\text{data:} & \hspace{.05in} & Y_i|\beta_0,\beta_1,\beta_2,\sigma & \stackrel{ind}{\sim} N(\mu_i, \\, \sigma^2)  \text{ with } \\;\\; \mu_i = \beta_0 + \beta_1 O_{i} + \beta_2D_{i} \\\\
\text{priors:} & & \beta_{0c}  &  \sim N\left(0, 1 \right)  \\\\
                     & & \beta_1  & \sim N\left(0, 1 \right) \\\\
                     & & \beta_2  & \sim N\left(0, 1 \right) \\\\
                     & & \sigma & \sim \text{Exp}(1)  .\\
\end{array}
\end{equation}
$$

```r
fit1 <- stan_glm(  
          data = as.data.frame(tb), 
          wage ~ 1 + occupation + female,  
          family = gaussian,  
          prior_intercept = normal(0, 1),  
          prior = normal(0, 1, autoscale = TRUE),   
          prior_aux = exponential(1, autoscale = TRUE),  
          chains = 4, iter = 5000*2, seed = 84735)

tidy(fit1, effects = c("fixed", "aux"),     
	conf.int = TRUE, conf.level = 0.80)

```

Regression results:

| term | estimate | std error | conf.low | conf.high |
| --- | --- | --- | --- | --- |
| Intercept | 0.197  | 0.0626  | 0.116  | 0.277 |
| occupation | 1.7601565 | 0.01798315 | 1.7374049 | 1.7831683 |
| female1 | 0.5971983 | 0.09182541 | 0.4792988 | 0.7146362 |
| sigma | 1.3464180 | 0.02978535 | 1.3092771 | 1.3860035 |
| mean_PPD | 0.3957330 | 0.06052916 | 0.3184863 | 0.4728555 |

Then, let’s take at look at the intercept for the Gender/Discrimination variable, in this case, we see that the credibility interval falls on the positive side, even though we **know** that the coefficient for this variable should be negative. 

![Untitled](resources/Untitled%202.png)

The problem here is that we are not taking into consideration the persons ability $A$. We have in this case a variable that’s not observable but influences both $O$ and $Y$. Controlling for occupation in the regression closes down the mediation channel, but it then opens up the second channel $D \rightarrow O \leftarrow A \rightarrow Y$, here we have something called backdoor paths.

![Screenshot 2023-07-04 at 01.00.38.png](resources/Screenshot_2023-07-04_at_01.00.38.png#center)
> A backdoor path is a non-causal path from A to Y. This is a path that would remain if we were to remove any arrows pointing out of A (these are the potentially causal paths from A, sometimes called frontdoor paths). They are “backdoor” paths because they flow backwards out of A: all of these paths point into A. [4]


It had been closed because colliders close backdoor paths, but since we conditioned on it, we actually opened it instead. This is the reason we cannot merely control for occupation. Such a control ironically introduces new patterns of bias: we create a bias that stems from the fact that employees with higher skills receive higher salaries.

The intuition here is that women have more obstacles to overcome to make it to higher-level positions. Those women that make it nonetheless are often a specifically selected group with likely higher skills than average. This higher skill level pushes their annual pay.

## Conclusion

> [DAG] make assumptions transparent and easier to critique. And if nothing else, they highlight the danger of using multiple regression as a substitute for theory. But DAGs are not a destination. Once you have a dynamical model of your system, you don’t need a DAG. In fact, many dynamical systems have complex behavior that is sensitive to initial conditions, and so cannot be use- fully represented by DAGs.96 But these models can still be analyzed and causal interventions designed from them. In fact, domain specific structural causal models can make causal inference possible even when a DAG with the same structure cannot decide how to proceed. Additional assumptions, when accurate, give us power.
> The fact that DAGs are not useful for everything is no argument against them. All theory tools have limitations. I have yet to see a better tool than DAGs for teaching the foundations of and obstacles to causal inference. And general tools like DAGs have added value in abstracting away from specific details and teaching us general principles. [3]



## References

[1] Cunningham, Scott. Causal Inference: The Mixtape. [https://mixtape.scunning.com/](https://mixtape.scunning.com/)

[2] Paul Hünermund. “This is my favorite teaching example for showing the importance of [#CausalInference ...](https://twitter.com/hashtag/CausalInference?src=hashtag_click)” Twitter, 24, Jun 2022, [https://twitter.com/PHuenermund/status/1540262938610638848](https://twitter.com/PHuenermund/status/1540262938610638848).

[3] McElreath, Richard. Statistical Rethinking. 2nd ed., CRC Press, 2022.

[4] Blackwell, Matthew. [Observational Studies and Confounding](https://www.mattblackwell.org/files/teaching/s06-observational.pdf)
