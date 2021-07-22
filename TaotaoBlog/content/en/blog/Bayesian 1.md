---
date: 2021-06-20T10:58:08-04:00
description: "Bayesian"
tags: ["Bayesian", "Inferential Statistics"]
mathjax: true
title: "Dive into Bayesian statistics (1): Maximum A Posteriori"
---


I know, this is an exciting & scary topic. It literally took me months to understand, and I hope this post will make your life easier.


Before you read this post, I assume you are already familiar with basic probability theories, maximum likelihood estimation and bayes theorem. I encourage you to read my previous post that discussed [MLE](https://jasontan-code.github.io/blog/mle/), and we are going to use the same dataset in this post.

Okay, let's get started.

&nbsp;  
&nbsp; 
&nbsp;  
&nbsp; 

## 1. Bayes theorem


In inferential statistics, our goal is to **infer the population parameters**. That is, we observe the data, and from the data we guess the most likely population. There are, in general, two ways to approach this. 

> ### Frequentist approach: 
>
> A typical method applied by frequentist is maximum likelihood estimation, where we define the likelihood function as $P( \text{data} | \text{params})$. Our goal is to find a set of parameters that best fit our data.
>
>In my previous [MLE](https://jasontan-code.github.io/blog/mle/) post, we observe the number of visitors (samples) per hour, and we are trying to estimate the population (estimate $\lambda$). From the data we have collected, the most likely $\lambda = 4.5$, which is a fixed number.

> ### Bayesian approach:
> Bayesian statisticians treat unknown parameters as a random variables. That means, the population parameter $\lambda$ could be any number. If we use the Bayesian framework to analyze the same problem above, we will end up with a **probability distribution** of $\lambda$, instead of a point estimate ($\lambda = 4.5$, in our case).
>
> p.s. This is true in general, but in this post we are going to discuss Maximum A Posteriori, and this is an exception.
> 

Okay, that's enough dry words. Let's look at the math.  

Bayes theorem states that: 
$$
P(A | B) = \frac{P(B | A) \cdot P(A)}{P(B)}
$$

This theorem was adapted to solve inferential statistics problems, where we have:
$$
P(\text{params} | \text{data}) = \frac{P(\text{data} | \text{params}) \cdot P(\text{params})}{P(\text{data})}
$$

Before we move forward, I want to clarify some terminologies:  
> $P(\text{params} | \text{data})$  
> Posterior distribution, This is what we are aiming to solve
  
> $P(\text{data} | \text{params})$  
> Likelihood function. We have already discussed the likelihood function [here](https://jasontan-code.github.io/blog/mle/). Briefly, it means the the probability of observing the data, given a set of parameters.

> $P(\text{params})$  
> Prior distribution. We need to define this beforehand, based on our prior knowledge. This is what makes Bayesian statistics powerful and controversial.

> $P(\text{data})$  
> A scaling factor, also called marginal likelihood. This quantity is to make sure that $\int_{- \infty}^{\infty} P(\text{params} | \text{data}) = 1$. Many times we can ignore it.



&nbsp;  
&nbsp; 
&nbsp;  
&nbsp; 


## 2. Work with an example

We will use the same dataset I used in [MLE](https://jasontan-code.github.io/blog/mle/). Here is how it looks like:

| Time  | Number of visitors |
|:---------|--------:|
| 8:am - 9:am     | 5   |
| 9:am - 10:am     | 3   |
| 10:am - 11:am | 4  |
| 11:am - 12:am | 6  |


Again, we are going to model the data with a poisson distribution. But, we will add a prior distribution, since we are doing Bayesian inference.

Choosing prior distribution is somewhat subjective. Here I decided to use a [gamma distribution](https://en.wikipedia.org/wiki/Gamma_distribution) with $\alpha = 2, \beta = 2$. 
$$
\lambda \sim Gamma (2, 2)
$$

Therefore, our Bayes formula becomes: 
$$
P(\lambda | \text{data}) = \frac{\text{Poisson}( \text{data}| \lambda) \cdot  \text{Gamma(2,2)}}{P(\text{data})}
$$

For simplicity, I will skip the calculation here and only show you the result. But you can find all the steps in the appendix.

$$
P(\lambda | \text{data}) = c \cdot \lambda^{19} e^{- 6 \lambda} \quad \text{, where $c$ is a constant.}
$$

&nbsp; 

The last step is try to find $\lambda_0$, so that $P(\lambda_0 | \text{data})$ reaches its maximum.


Again, I drew a picture to show you the shape of  $P(\lambda | \text{data})$

![bayesian1](/images/Bayesian1/Bayesian1.png)

Note: The prior and likelihood were rescaled for plotting.

&nbsp; 

As you can see, the prior Gamma distribution has a peak when $\lambda \approx 0.5$. The likelihood reaches its peak when $\lambda = 4.5$. After combining prior and likelihood function, our posterior reaches its peak when $\lambda \approx 3.17$. 

&nbsp; 

This is why sometimes Bayesian inference is also called **shrinkage method**. Using MLE, we would have got our result $\lambda = 4.5$. But adding a prior distribution enforces $\lambda$ to shift toward the prior, and the posterior distribution eventually sits somewhere between the prior and the likelihood.

&nbsp; 

That's the end of this blog. Thanks for reading!


&nbsp;  
&nbsp; 
&nbsp;  
&nbsp; 
&nbsp;  
&nbsp; 
&nbsp;  
&nbsp; 

## Appendix

#### Prior distribution 
Gamma distribution is defined as 
$$
f(\lambda) = \frac{\beta^{\alpha}}{ (\alpha - 1)! } \cdot \lambda^{\alpha - 1} \cdot e^{- \beta \lambda} 
$$

Plug $\alpha = 2, \beta = 2$, we get our prior distribution:

$$
f(\lambda) =4  \lambda \cdot e^{- 2 \lambda} 
$$

&nbsp;  
&nbsp; 

#### Likelihood function

We have already calculated the likelihood function [here](https://jasontan-code.github.io/blog/mle/):

$$
P(\text{data} | \lambda) = \frac{\lambda^5 e^{- \lambda}}{5 !} \times \frac{\lambda^3 e^{- \lambda}}{3 !} \times \frac{\lambda^4 e^{- \lambda}}{4 !} \times \frac{\lambda^6 e^{- \lambda}}{6 !}
$$

&nbsp;  
&nbsp; 

#### Combine prior and likelihood

$$
\begin{equation} \label{eq1}
\begin{split}
P(\text{data} | \lambda) \cdot P(\lambda) & =  \frac{\lambda^5 e^{- \lambda}}{5 !} \times \frac{\lambda^3 e^{- \lambda}}{3 !} \times \frac{\lambda^4 e^{- \lambda}}{4 !} \times \frac{\lambda^6 e^{- \lambda}}{6 !} \times  4  \lambda \cdot e^{- 2 \lambda}  \\\\\\
& = \frac{4}{5! \cdot 3! \cdot 4! \cdot 6! } \cdot \lambda^{19} e^{- 6 \lambda}
\end{split}
\end{equation}
$$


As I mentioned before, the denominator of the equation is a scaling factor/constant, therefore we can write our posterior probability as:

$$
P(\lambda | \text{data}) = c \cdot \lambda^{19} e^{- 6 \lambda}
$$




&nbsp;  
&nbsp; 
&nbsp;  
&nbsp; 
&nbsp;  
&nbsp; 
&nbsp;  
&nbsp; 

## Reference

Wiki-Conjugate_prior : https://en.wikipedia.org/wiki/Conjugate_prior

Wiki-Gamma_distribution: https://en.wikipedia.org/wiki/Gamma_distribution

Wiki-Poisson_distribution: https://en.wikipedia.org/wiki/Poisson_distribution

&nbsp; 
&nbsp;  
&nbsp; 
&nbsp; 
&nbsp;  
&nbsp; 


{{< rawhtml >}}
<div id="disqus_thread"></div>
<script>
    /**
    *  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
    *  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables    */
    /*
    var disqus_config = function () {
    this.page.url = PAGE_URL;  // Replace PAGE_URL with your page's canonical URL variable
    this.page.identifier = PAGE_IDENTIFIER; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
    };
    */
    (function() { // DON'T EDIT BELOW THIS LINE
    var d = document, s = d.createElement('script');
    s.src = 'https://taotaotancomments.disqus.com/embed.js';
    s.setAttribute('data-timestamp', +new Date());
    (d.head || d.body).appendChild(s);
    })();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
{{< /rawhtml >}}




