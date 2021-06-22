---
date: 2021-06-14T10:58:08-04:00
description: "Cool Monte Carlo method"
tags: ["Monte Carlo", "Sampling"]
mathjax: true
title: "But how to draw samples from a strange distribution?"
---

In this post, I am going to show you two methods to draw samples from a "strange" distribution.  

But before we get started, we should define what do I mean "strange" distribution


$$
\begin{equation}
  f(x) =
    \begin{cases}
      0 & \text{if $x$ < 0}\\\\\\
      c \cdot \sqrt{x} & \text{if  $0 < x < 1 $}\\\\\\
      0 & \text{if $x > 1$}
    \end{cases}       
\end{equation}
$$  

First, let's take a look at the probability density function (pdf):  
&nbsp;  

![pdf](/images/Monte_Carlo/pdf2.png)
&nbsp;  

### Remember, our goal is to draw samples from a non-typical distribution (no Gaussian, no Poisson, etc...)

&nbsp;  
&nbsp; 
&nbsp;  
&nbsp; 
&nbsp;  
&nbsp; 

## Method 1: inverse transform sampling

You may have noticed that our function has a constant $c$. In order for this curve to be a legit probability density function, we have to make sure that the area under the curve is 1. In mathematical notation, we have 
$$
\int_{0}^{1} c \sqrt{x} = 1
$$ 

Finding the integral isn't difficult. We have 
$$
F = \frac{2c}{3} \cdot x^{1.5}
$$

We have to make sure $F |_{0}^{1} = 1$, therefore we solve $c = 1.5$

&nbsp; 

Plug $c = 1.5$ into our pdf and cdf, we pdf:

$$
\begin{equation}
  pdf(x) =
    \begin{cases}
      0 & \text{if $x$ < 0}\\\\\\
      1.5 \cdot \sqrt{x} & \text{if  $0 < x < 1 $}\\\\\\
      0 & \text{if $x > 1$}
    \end{cases}       
\end{equation}
$$  

And we have cdf:

$$
\begin{equation}
  cdf(x) =
    \begin{cases}
      0 & \text{if $x$ < 0}\\\\\\
      x^{1.5} & \text{if  $0 < x < 1 $}\\\\\\
      1 & \text{if $x > 1$}
    \end{cases}       
\end{equation}
$$

&nbsp; 
&nbsp; 

The steps I described above are pretty standard and boring, but here comes to the interesting part. Please follow me:

> #### Inverse the cdf  
>  We first find the inverse of cumulative density function.
 $$
  x = y^{2/3}
 $$

 
 
 >  #### Sampling
 > We draw sample y from a uniform distribution, then raise this sample to the power of $2/3$, and we will get a customized sample generator!
 >
 > Let $y \sim \text{Unif }(0 ,1)$, then $x \sim \text{Strange dist}$
 >
Let's demonstrate this idea with come R code.
```
>  Sampler <- function(n = 1){
>      y = runif(n, min = 0, max = 1)
>      x = y ^(2/3)
>      return(x)
>  }

# draw one sample 
> Sampler()


# draw 1000 samples
> Sampler(1000)
```
&nbsp; 

After I drew 100000 samples and plot them in a histogram, we get this. 
 
 ![hist](/images/Monte_Carlo/hist.png)
 
 &nbsp;  
 Perfect! The red line represents the theoretical pdf, and it perfectly matches this histogram! 
 




&nbsp;  
&nbsp; 
&nbsp;  
&nbsp; 


## Method 2: accept-reject sampling.

What if I tell you that you don't even need to calculate $c$, to draw samples from our "strange" distribution? 

That sounds crazy, but it is do-able using a Monte Carlo technique called accept-reject sampling.


Here are the steps involved:

> Find an easy-to-sample distribution $g(x)$ (normal, uniform, etc) and multiply by a constant $k$, so that $k \times g(x)$ is always greater than our customized distribution $f(x)$.

> Draw a sample from $g(x)$

> Calculate the probability of acceptance: 
$$
p (accept) = \frac{f(x)}{k \cdot g(x)}
$$

> Collect samples. The bag of your collected samples will have a distribution of $f(x)$.

**Notice that you don't need the full format of f(x). In our case, you can ignore $c$, and set $f(x) = \sqrt x$**

Here is the code:


```
# our collecting bag
> accept = c()

# draw 100000 samples from a uniform distribution
> samples = runif(100000, 0, 1)

# iterate all the samples
> for (sample in samples){
>    accpt_prob = sqrt(sample)/(2 * 1)           # calculate the probability of acceptance
>     if (rbinom(n = 1, size = 1, prob = accpt_prob) == 1){                # flip a unfair coin with probability we just calculated. If head up, we accept.
>         accept = c(accept, sample)               # add the accepted samples to our bag
>     }
> }
```

Notice here, I chose the constant $k = 2$. We are sure that $2 \times U(0, 1)$ is always larger than our customized pdf. 

Also, notice in my code, I use $f(x) = \sqrt x$, but not $f(x) = 1.5 \sqrt x$, although either way works. 

Take a look at the histogram:

![pdf](/images/Monte_Carlo/hist2.png)

&nbsp;  

You might find that I drew 100000 samples, but only 49802 samples are accepted. This is one of a big disadvantage of accept-reject sampling!


**Being able to draw samples without knowing its full format is very advantageous. This accept-reject sampling is a core foundation of MCMC sampling, which I will demonstrate in the next few posts**


&nbsp;  
&nbsp; 
&nbsp;  
&nbsp; 

## Summary

In this post, we learned two ways of drawing samples form a customized distribution. Either one has its pros and cons.

| Method      | Advantage  | Disadvantage  |
| :----------- | :----------- | :----------- |
| Inverse Transform Sampling:       |  Utilizes all the samples        | Need to calculate cdf; Inverse cdf sometimes could be very difficult
| Accept Reject Sampling:   |  Do not require full format of pdf, easy to implement        | Waste samples



&nbsp;  
&nbsp; 
&nbsp;  
&nbsp; 
&nbsp;  
&nbsp; 
&nbsp;  
&nbsp; 
&nbsp;  
&nbsp; 
&nbsp;  
&nbsp; 
&nbsp;  
&nbsp; 
&nbsp;  
&nbsp; 



### Reference:


ritvikmath: https://www.youtube.com/watch?v=OXDqjdVVePY  
Ben Lambert: https://www.youtube.com/watch?v=rnBbYsysPaU



