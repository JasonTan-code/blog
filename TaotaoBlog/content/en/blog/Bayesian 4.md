---
date: 2021-06-28T10:59:08-04:00
description: "Bayesian"
tags: ["Bayesian", "Inferential Statistics", "Monte Carlo"]
mathjax: true
title: "Dive into Bayesian statistics (4): Posterior predictive distribution"
---

In the last few posts, we tried three methods ([Integration, Conjugate Prior](https://jasontan-code.github.io/blog/bayesian-2/) and [MCMC](https://jasontan-code.github.io/blog/bayesian-3/)) to infer the posterior distribution $P(\lambda | \text{data})$, which gave us 

$$\lambda \sim \text{Gamma}(\alpha = 20, \beta = 6)$$

Now you may ask: Cool, we have our posterior distribution, but SO WHAT?

In this post, we are going to see why studying the posterior distribution is advantageous and interesting, and show you the internal relationship between a Poisson distribution, a Gamma distribution and a Negative binomial distribution.

&nbsp;  
&nbsp; 
&nbsp;  

## Question: 

Here is the data we have worked so far:

| Time  | Number of visitors |
|:---------|--------:|
| 8:am - 9:am     | 5   |
| 9:am - 10:am     | 3   |
| 10:am - 11:am | 4  |
| 11:am - 12:pm | 6  |

In this post, we will try to answer a question:  **What is the probability that my website has no visitors in the next hour?**

The question might look a little strange at the first glance. How am I supposed to know the number of visitors in the future? In this post, we are going to address this question in two different manners: **a frequentist framework**, and **a Bayesian framework**


&nbsp;  
&nbsp;  
&nbsp; 
&nbsp;  

## A Frequentist Framework:

As what I have discussed in [this post](https://jasontan-code.github.io/blog/mle/), we assumed that the data has a Poisson distribution, and used Maximum Likelihood Estimation to figure out the most likely $\lambda = 4.5$. Now, we can simply plug $\lambda = 4.5$ into the Poisson distribution formula, which becomes our inferred population now:
$$
f(x) = \frac{4.5^x e^{-4.5}}{x!}
$$

To answer the question above, we simply plug in $x = 0$, and get:
$$
f(0) = \frac{4.5^0 e^{-4.5}}{0!} = 0.011
$$


So, under a frequentist framework, our answer is 0.011



&nbsp;  
&nbsp;  
&nbsp; 
&nbsp;  

## A Bayesian Framework:

Now let's try to answer the question using a Bayesian framework. We have already derived the posterior distribution:


$$\lambda \sim \text{Gamma}(\alpha = 20, \beta = 6)$$

Then we also have the distribution of the data: 

$$x \sim \text{Poisson}(\lambda)$$

Let's try to concatenate the gamma distribution (which is the pdf of $\lambda$), and the poisson distribution (which is the pdf of data). This should gives us the **probability distribution of the predictive data**.


$$
\begin{equation} \label{eq1}
\begin{split}
f(x; \alpha = 20, \beta = 6) & = \int_0^{+ \infty} \frac{\lambda^x e^{-\lambda}}{x!} \cdot \frac{6^{20}}{19!} \lambda^{19} e^{-6\lambda} \cdot d\lambda \\\\\\
& = \frac{6^{20}}{19! \cdot x!} \int_{0}^{+\infty} \lambda^{19 + x} e^{-7 \lambda} d\lambda \\\\\\
& \stackrel{\dagger}{=} \frac{6^{20}}{19! \cdot x!}  \cdot \frac{\Gamma{(20 + x)}}{7^{20 + x}} \\\\\\
& = \frac{\Gamma (20 + x)}{19 ! \cdot x!} \cdot (\frac{6}{7})^{20} \cdot (\frac{1}{7})^x \\\\\\
& = \frac{\Gamma (20 + x)}{\Gamma (20) \cdot \Gamma (x + 1)} \cdot (\frac{6}{7})^{20} \cdot (\frac{1}{7})^x \\\\\\ 
& =  {x + 19 \choose x}\cdot (\frac{6}{7})^{20} \cdot (\frac{1}{7})^x
\end{split}
\end{equation}
$$

Note: step $\dagger$ holds because 
$$
\int_0^{+\infty} x^a e^{-bx} dx = \frac{ \Gamma(a + 1)}{b ^{a + 1}} \text{      ,where } a, b \text{ are integers}
$$


**Therefore, we can make the conclusion that the posterior predictive distribution has a negative binomial distribution with $p = 6/7, r = 20$**


From the posterior predictive distribution, we can easily calculate the probability of having no visitors in the next hour:

$$
f(0) = {19 \choose 0}\cdot (\frac{6}{7})^{20} \cdot (\frac{1}{7})^0 = 0.046
$$


&nbsp; 
&nbsp;  
&nbsp; 
&nbsp;  
&nbsp; 

## Sampling of posterior predictive

Lastly, let's do a little bit of sampling to verify our result. Again, there are two ways: 
&nbsp;  
&nbsp; 
#### Method 1: 

Draw $\lambda$ from a gamma distribution, then use this lambda in a Poisson distribution:
```r
library(ggplot2)
bag1 = c()

for (i in 1:100000){
  lambda = rgamma(1, shape = 20, rate = 6)
  bag1[i] = rpois(1, lambda = lambda)
}
# draw a histogram
ggplot(data.frame(bag1), aes(bag1)) + geom_bar()
```
This gave us 
![bayesian4](/images/Bayesian4/bag1.png)


&nbsp; 
&nbsp;  

#### Method 2: 

Draw samples from the negative binomial distribution directly:
```r
bag2 = rnbinom(n = 100000, size = 20, prob = 6/7)
# draw a histogram
ggplot(data.frame(bag2), aes(bag2)) + geom_bar()
```
This gave us 
![bayesian4](/images/Bayesian4/bag2.png)

,which is almost identical as method 1

&nbsp; 
&nbsp;  
&nbsp; 
&nbsp;  
&nbsp; 
&nbsp;  

## Summary:

In this post, we saw two completely different frameworks of inferring the data. 

&nbsp; 
&nbsp;  

The frequentist approach assumes the **parameters are fixed** and **data are random**, and we use MLE to estimate the most likely parameters. Here is how it works:

![bayesian4](/images/Bayesian4/freq.png)

&nbsp; 
&nbsp;  
&nbsp; 

Whereas under the Bayesian framework, not only the **data are random**, but the **parameters are also random**. By adding a prior distribution, we can infer a parameter distribution. To generate data, we will first draw random parameters, and from those random parameters, we then draw random data. Here is a graphic demonstration:
![bayesian4](/images/Bayesian4/bayesian.png)

&nbsp; 
&nbsp;  
&nbsp; 

Besides all that, we also learned that the concatenation of a Gamma distribution and a Poisson distribution is a Negative Binomial distribution.


&nbsp;  
&nbsp; 

Thanks for reading this post. If like the content, don't hesitate to leave comments and drop a thumb up.

&nbsp;  
&nbsp; 
&nbsp;  
&nbsp;  
&nbsp; 
&nbsp;  
&nbsp; 
&nbsp;  


## Reference:
Angina Seng @ Stack Exchange: https://math.stackexchange.com/a/3407923/938478

Wiki Negative binomial: https://en.wikipedia.org/wiki/Negative_binomial_distribution#Gamma%E2%80%93Poisson_mixture


Wiki Conjugate prior: https://en.wikipedia.org/wiki/Conjugate_prior#Table_of_conjugate_distributions

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













