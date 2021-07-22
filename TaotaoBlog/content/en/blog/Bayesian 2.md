---
date: 2021-06-25T10:58:08-04:00
description: "Bayesian"
tags: ["Bayesian", "Inferential Statistics"]
mathjax: true
title: "Dive into Bayesian statistics (2): Solve the nasty denominator!"
---


In the [last post](https://jasontan-code.github.io/blog/bayesian-1/), we tried to use a Bayesian framework to model the number of visitors per hour. After concatenating a Poisson distribution with a Gamma prior, we get something like: 
$$
P(\lambda | \text{data}) = c \cdot \lambda^{19} e^{-6\lambda}
$$

Since we are interested to find $\lambda_0$ that gives the maximum value of $P(\lambda | \text{data})$ (a.k.a. **Maximum A Posteriori**), we don't need to worry too much about a constant $c$. But in this post, we are going to solve $c$, and consolidate our understanding of Bayesian inference.

&nbsp;  
&nbsp;  
&nbsp;  


## Interpretation the denominator

Recall our Bayesian framework states:
$$
P(\lambda | \text{data}) = \frac{P(\text{data} | \lambda) \cdot P(\lambda)}{P(\text{data})}
$$

I wrote detailed explanations of the $P(\text{data} | \text{params})$ and $P(\text{params})$ in the [last post](https://jasontan-code.github.io/blog/bayesian-1/), but the denominator $P(\text{data})$ is still unclear. How can we interpret the probability of data? What does that even mean?

&nbsp;  
&nbsp;  

Well, let's first take look at a bivariate discrete random variables:

$$
\begin{array}{c|lcr}& X_1 & X_2 & X_3 
\\\\\\hline Y_1 & 0.3 & 0.2 & 0.1  \\\\\\
Y_2 & 0.1 & 0.1 & 0.2  \end{array} 
$$

The question is: what is $P(Y_1)$? 

The answer is simple, we simply have $P(Y_1) = 0.3 + 0.2 + 0.1 = 0.6$

But the point is, we can write

$$
\begin{equation} \label{eq1}
\begin{split}
P(Y_1) & =  P(X_1 \cap Y_1) + P(X_2 \cap Y_1) + P(X_3 \cap Y_1) \\\\\\
 & = P(Y_1 | X_1)\cdot P(X_1) + P(Y_1 | X_2)\cdot P(X_2)  + P(Y_1 | X_3)\cdot P(X_3) 
\end{split}
\end{equation}
$$

&nbsp;  
&nbsp;  

Now, we can apply this idea of calculating the marginal probability to our Bayesian framework, where we have 
$$
\begin{equation} \label{eq2}
\begin{split}
P( \text{data}) & = P( \text{data} \cap \lambda_1) + P( \text{data} \cap \lambda_2) + ... \\\\\\
 & = P( \text{data} | \lambda_1) \cdot P(\lambda_1) + P( \text{data} | \lambda_2) \cdot P(\lambda_2) ...
\end{split}
\end{equation}
$$



Since $\lambda$ is a continuous variable, it's more appropriate to write the above formula as:

$$
P( \text{data}) = \int P( \text{data} | \lambda) P(\lambda)  \cdot  d \lambda
$$


&nbsp;  
&nbsp;  

Now, let's plug the denominator into the Bayes theorem: 

$$
P(\lambda | \text{data}) = \frac{P(\text{data} | \lambda) P(\lambda)}{\int P( \text{data} | \lambda) P(\lambda)  \cdot  d \lambda}
$$

**Notice that the denominator is just the integral of the numerator.**

&nbsp;  
&nbsp;  
&nbsp;  
&nbsp;  






## Back to our problem

In the appendix of my [last post](https://jasontan-code.github.io/blog/bayesian-1/), we solved the numerator, which is 

$$
P(\text{data} | \lambda) \cdot P(\lambda)  =  \frac{4}{5! \cdot 3! \cdot 4! \cdot 6! } \cdot \lambda^{19} e^{- 6 \lambda}
$$

As we discussed, the denominator is just the integral of the numerator. We have 
$$
\begin{equation} \label{eq3}
\begin{split}
P( \text{data}) & =  \int P( \text{data} | \lambda) P(\lambda)  \cdot  d \lambda\\\\\\
 & = \int_0^{\infty} \frac{4}{5! \cdot 3! \cdot 4! \cdot 6! } \cdot \lambda^{19} e^{- 6 \lambda} d \lambda \\\\\\
 & = 1.07 \times 10^{-5}
\end{split}
\end{equation}
$$


Now we can finalize our calculation by plug in the numerator and the denominator:

$$
\begin{equation} \label{eq4}
\begin{split}
P( \lambda  |\text{data}) & =  \frac{P(\text{data} | \lambda) \cdot P(\lambda)}{P(\text{data})}\\\\\\
 & = 0.03 \cdot \lambda^{19} e^{- 6 \lambda}
\end{split}
\end{equation}
$$


**We finally solved our posterior distribudtion!!!**


&nbsp;  
&nbsp;  
&nbsp;  
&nbsp;  
&nbsp;  
&nbsp;  

## Conjugate prior

Now, let me introduce a concept called conjugate prior. If you choose a prior distribution carefully, you may get a posterior distribution that is in the same distribution family as the prior distribution. 


In our case, gamma distribution is indeed a conjugate prior of poisson distribution. From [Wikipedias](https://en.wikipedia.org/wiki/Gamma_distribution), The posterior distribution is 
$$\text{Gamma}(\alpha + \sum_i x_i , \beta + n)$$

In our case, we have 
$$
\begin{equation} \label{eq6}
\begin{split}
P(\lambda |\text{data}) & \sim \text{Gamma}(2+ 5 + 3 + 4 + 6 , 2 + 4) \\\\\\
 & =\text{Gamma}(20, 6)
\end{split}
\end{equation}
$$

&nbsp;  

Let's plug $\alpha' = 20, \beta' = 6$ into the formula of Gamma distribution:
$$
\begin{equation} \label{eq7}
\begin{split}
f(\lambda)_{\alpha' = 20, \beta' = 6} & = \frac{6^{20}}{19!}\lambda^{19}e^{-6\lambda} \\\\\\
& = 0.03 \cdot \lambda^{19} e^{- 6 \lambda}
\end{split}
\end{equation}
$$

, which is exactly the same as the result achieved by integration.



&nbsp;  
&nbsp;  
&nbsp;  
&nbsp;  
&nbsp;  
&nbsp;  
&nbsp;  
&nbsp;  

## Summary

In summary, we showed two ways of calculating the posterior probability (a.k.a. find the denominator) in this post. 

> **Method 1: Integrate the numerator** 
> 
> This method, in principle, can solve all the problems. But as you can see in this post, solving a one dimensional problem isn't easy. In real world application, you can easily encounter $> 5$ dimension problems, and the integration would be a nightmare for any mathematicians. Imagine you need to solve something like $$\int \int \int \int \int ... dx \cdot dy \cdot dz \cdot dw \cdot dq$$
> Solving this integration might be the worst thing of the universe :( 


> **Method 2: Conjugate prior**
>
> The calculation is easy enough. In our case, you just need to follow the formula and plug in the numbers, and you will get a beautiful posterior distribution. But the drawback is that it's not guaranteed that your prior is always conjugate with your likelihood function. Therefore, you need to carefully choose the prior distribution according to [this table](https://en.wikipedia.org/wiki/Conjugate_prior#Table_of_conjugate_distributions), if you want to utilize this nice property.


&nbsp;  
&nbsp;  

All right, thanks for reading this post. In the next few posts, I will introduce Markov chain Monte Carlo (MCMC), which solve the drawbacks we talked about. Stay tuned!

&nbsp;  
&nbsp;  
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















