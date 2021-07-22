---
date: 2021-07-10T10:58:08-04:00
description: "Calculate PCA by hand"
tags: ["PCA"]
mathjax: true
title: "Calculate SVD by hand (and decompose Spongebob)"
---

In my [previous post](https://jasontan-code.github.io/blog/calculate_pca/), I have manually implemented PCA by finding the eigenvectors and eigenvalues of a covariance matrix. Today, let's try to perform PCA using a different approach called Singular Value Decomposition. Then we are going to decompose SPONGEBOB!

Note: you might find this [post](https://jasontan-code.github.io/blog/calculate_pca/) to be useful, if you are new to PCA. 


&nbsp;  
&nbsp;  
&nbsp;  
&nbsp;  
&nbsp;  


## Algorithm

Again, we are going to use the same dataset we have used before.


$$
\mathbf{ M} = 
\begin{bmatrix}
1 & 0 \\\\\\  
0 & 1 \\\\\\  
-1 & -1
\end{bmatrix}$$ 


The formula of SVD stands: 
$$
\mathbf{ M}  = \mathbf{ U \cdot \Sigma \cdot V ^{T}} 
$$


$\mathbf{ U }$ is the eigenvectors of $ \mathbf{ M \cdot M^T}$, 

$\mathbf{ V }$ is the eigenvectors of $ \mathbf{ M^T \cdot M}$.

$\mathbf{ \Sigma }$ is a diagonal matrix with singular values (a.k.a square root of eigenvalues) of $ \mathbf{ M^T \cdot M}$.

&nbsp;  
&nbsp;  
&nbsp;  
&nbsp;  
&nbsp;  

## Calculation:

(Note: I have discussed how to find eigenvectors and eigenvalues for a matrix in this [post](https://jasontan-code.github.io/blog/calculate_pca/), and I will skip the steps for simplicity)

&nbsp;  

#### 1. Calculate $\mathbf{ U }$

>$$
\mathbf{ M \cdot M^T} = 
\begin{bmatrix}
1 & 0 \\\\\\  
0 & 1 \\\\\\
-1 & -1 
\end{bmatrix} \cdot \begin{bmatrix}
1 & 0 & -1 \\\\\\  
0 & 1 & -1
\end{bmatrix} =   \begin{bmatrix}
1 & 0 & -1 \\\\\\  
0 & 1 & -1 \\\\\\
-1 & -1 & 2
\end{bmatrix}$$ 
>
> We then find the eigenvectors of $ \mathbf{ M \cdot M^T}$, and get
>
> 
> $$
\lambda_1 = 3, \mathbf{v_1} = [-0.408, -0.408, 0.816] \\\\\\
\lambda_2 = 1, \mathbf{v_2} = [0.707, -0.707, 0] \\\\\\
\lambda_3 = 0, \mathbf{v_3} = [0.577, 0.577, 0.577]
$$
> 
> We write the eigenvectors into a matrix, with each column to be a eigenvector. Therefore, we have $$
\mathbf{U} = \begin{bmatrix}
-0.408 & 0.707 & 0.577 \\\\\\  
-0.408 & -0.707 & 0.577 \\\\\\
0.816 & 0 & 0.577
\end{bmatrix}
$$
>

&nbsp;  


#### 2. Calculate $\mathbf{ V }$
> The calculation is almost identical, we simply find the eigenvectors and eigenvalues of $ \mathbf{ M^T \cdot M}$
>
> $$
\mathbf{ M^T \cdot M} = 
\begin{bmatrix}
1 & 0 & -1 \\\\\\  
0 & 1 & -1
\end{bmatrix} \cdot \begin{bmatrix}
1 & 0 \\\\\\  
0 & 1 \\\\\\
-1 & -1 \end{bmatrix}  = \begin{bmatrix}
2 & 1 \\\\\\  
1 & 2 \end{bmatrix}
$$
> 
> Find the eigenvectors of $ \mathbf{ M^T \cdot M}$, we have 
>
> $$
\lambda_1 = 3, \mathbf{v_1} = [-0.707, -0.707]  \\\\\\
\lambda_2 = 1, \mathbf{v_1} = [0.707, -0.707] 
$$
> 
> Again, we write the eigenvectors into a matrix, which gives us $\mathbf{V}$:
> $$
\mathbf{V} = \begin{bmatrix}
-0.707 & 0.707  \\\\\\  
-0.707 & -0.707 
\end{bmatrix}
$$


&nbsp;  


#### 3. Calculate $\mathbf{ \Sigma }$

> In fact, we have already solved $\mathbf{ \Sigma }$. As I said, $\mathbf{ \Sigma }$ is a diagonal matrix with values to be the square root of eigenvalues we have already calculated.
>
> Also, you might have noticed that we ended up with the same list of eigenvalues from $\mathbf{ M^T \cdot M}$ and $\mathbf{ M \cdot M^T}$, except $ \mathbf{ M^T \cdot M}$ has an additional eigenvector $\lambda_3 = 0$. This has been proved to be true in general, since [singular matrices](https://en.wikipedia.org/wiki/Invertible_matrix) must have at least one eigenvalue to be 0.
>
> Therefore, we have $$
\mathbf{\Sigma} = \begin{bmatrix}
\sqrt{3} & 0  \\\\\\  
0 & \sqrt{1}
\end{bmatrix} = \begin{bmatrix}
1.732 & 0  \\\\\\  
0 & 1
\end{bmatrix}
$$
> 
> Since $ \mathbf{ U}$ is a $3 \times 3$ matrix and $ \mathbf{ V^T}$ is a $2 \times 2$ matrix, we have to make $ \mathbf{ \Sigma}$ to be a $3 \times 2$ matrix so that we can conduct the matrix multiplication. To do this, we simply append 0 to the last row of $\mathbf{\Sigma}$, which gives us 
>
> $$\mathbf{\Sigma} = 
\begin{bmatrix}
1.732 & 0  \\\\\\  
0 & 1 \\\\\\
0 & 0
\end{bmatrix}
$$

&nbsp;  
&nbsp;  
&nbsp;  
&nbsp;  
&nbsp;  
&nbsp;  

## Put them together

Okay, we have find all the ingredients of SVD, now it's the time to check if the following equation actually holds.

$$
\mathbf{ M}  = \mathbf{ U \cdot \Sigma \cdot V ^{T}} 
$$ 

On the right hand side, we have 
$$
\begin{equation} \label{eq1}
\begin{split}
\mathbf{ U \cdot \Sigma \cdot V ^{T}} & = \begin{bmatrix}
-0.408 & 0.707 & 0.577 \\\\\\  
-0.408 & -0.707 & 0.577 \\\\\\
0.816 & 0 & 0.577
\end{bmatrix} \cdot \begin{bmatrix}
1.732 & 0  \\\\\\  
0 & 1 \\\\\\
0 & 0
\end{bmatrix} \cdot \begin{bmatrix}
-0.707 & 0.707  \\\\\\  
-0.707 & -0.707 
\end{bmatrix}^T \\\\\\
& = \begin{bmatrix}
-0.707 & 0.707 \\\\\\  
-0.707 & -0.707 \\\\\\  
1.414 & 0
\end{bmatrix} \cdot \begin{bmatrix}
-0.707 & -0.707  \\\\\\  
0.707 & -0.707 
\end{bmatrix} \\\\\\
& = \begin{bmatrix}
1 & 0 \\\\\\  
0 & 1 \\\\\\  
-1 & -1
\end{bmatrix}
\end{split}
\end{equation}
$$

Incredible! We have proved that SVD really works!

(Note: there are some really interesting geometric interpretations. But for the sake of conciseness, I won't discuss here)

&nbsp;  
&nbsp;  
&nbsp;  
&nbsp;  
&nbsp;  

## Reduced SVD

It's worth mentioning that in practice, we tend not to calculate the full format of $\mathbf{U}$. In our case, it is sufficient to only calculate the first two columns of $\mathbf{U}$:

$$
\mathbf{U} = \begin{bmatrix}
-0.408 & 0.707  \\\\\\  
-0.408 & -0.707  \\\\\\
0.816 & 0 
\end{bmatrix}
$$


Correspondingly, we no longer need to append an additional row to $\mathbf{\Sigma}$, and we have 
$$
\mathbf{\Sigma} =  \begin{bmatrix}
1.732 & 0  \\\\\\  
0 & 1
\end{bmatrix}
$$

It turns out that the result is exactly the same as what we have calculated before.

$$
\begin{equation} \label{eq2}
\begin{split}
\mathbf{ U \cdot \Sigma \cdot V ^{T}} & = \begin{bmatrix}
-0.408 & 0.707 \\\\\\  
-0.408 & -0.707  \\\\\\
0.816 & 0 
\end{bmatrix} \cdot \begin{bmatrix}
1.732 & 0  \\\\\\  
0 & 1 
\end{bmatrix} \cdot \begin{bmatrix}
-0.707 & 0.707  \\\\\\  
-0.707 & -0.707 
\end{bmatrix}^T \\\\\\
& = \begin{bmatrix}
-0.707 & 0.707 \\\\\\  
-0.707 & -0.707 \\\\\\  
1.414 & 0
\end{bmatrix} \cdot \begin{bmatrix}
-0.707 & -0.707  \\\\\\  
0.707 & -0.707 
\end{bmatrix} \\\\\\
& = \begin{bmatrix}
1 & 0 \\\\\\  
0 & 1 \\\\\\  
-1 & -1
\end{bmatrix}
\end{split}
\end{equation}
$$

&nbsp;  
&nbsp;  

In fact, one can re-construct the original matrix by selecting the first n dimensions. With n gets larger, the reconstructed matrix tends to be more similar to the original matrix. Here, if we use only one dimension to approximate the original matrix, we will have: 

$$
\begin{equation} \label{eq4}
\begin{split}
\mathbf{ U' \cdot \Sigma' \cdot V' ^{T}} & = \begin{bmatrix}
-0.408  \\\\\\  
-0.408   \\\\\\
0.816 
\end{bmatrix} \cdot \begin{bmatrix}
1.732 
\end{bmatrix} \cdot \begin{bmatrix}
-0.707  \\\\\\  
-0.707 
\end{bmatrix}^T \\\\\\
& = \begin{bmatrix}
0.5 & 0.5 \\\\\\  
0.5 & 0.5 \\\\\\  
-1 & -1
\end{bmatrix}
\end{split}
\end{equation}
$$

This reconstruction process will be more clear later in this post, as we are going to reconstruct Spongebob.



&nbsp;  
&nbsp;  
&nbsp;  
&nbsp;  
&nbsp;  
&nbsp;  


## Relationship with PCA 

In [the PCA post](https://jasontan-code.github.io/blog/calculate_pca/), we discussed how to project the original dataset onto the PC space. The calculation is trivial. We simply do a matrix multiplication between the original dataset and PCs. 

It turns out SVD is even simpler! To get the transformed dataset, we simply do $\mathbf{U \cdot \Sigma}$

$$
\begin{equation} \label{eq3}
\begin{split}
\mathbf{ U \cdot \Sigma } & = \begin{bmatrix}
-0.408 & 0.707 \\\\\\  
-0.408 & -0.707  \\\\\\
0.816 & 0 
\end{bmatrix} \cdot \begin{bmatrix}
1.732 & 0  \\\\\\  
0 & 1 
\end{bmatrix} \\\\\\
& = \begin{bmatrix}
-0.707 & 0.707 \\\\\\  
-0.707 & -0.707 \\\\\\  
1.414 & 0
\end{bmatrix}
\end{split}
\end{equation}
$$

The result is slightly different from what we have get in [this post](https://jasontan-code.github.io/blog/calculate_pca/). This is because the choice of eigenvector directions is arbitrary. However, the relative distances between each datapoint should be identical.


&nbsp;  
&nbsp;  

It's also possible to re-construct the covariance matrix from SVD. The formula states that 
$$
\mathbf{Cov} = \mathbf{V} \cdot \frac{ \mathbf{S^2}}{n -1 } \cdot  \mathbf{V^T} 
$$

Let's try that
$$
\begin{equation} \label{eq5}
\begin{split}
\mathbf{V} \cdot \frac{ \mathbf{S^2}}{n -1 } \cdot  \mathbf{V^T} & = \begin{bmatrix}
-0.707 & 0.707  \\\\\\  
-0.707 & -0.707 
\end{bmatrix} \cdot \Bigg( \frac{1}{3 - 1} \cdot
\begin{bmatrix}
1.732^2 & 0  \\\\\\  
0 & 1^2
\end{bmatrix} \Bigg) \cdot \begin{bmatrix}
-0.707 & -0.707  \\\\\\  
0.707 & -0.707 
\end{bmatrix} \\\\\\
& = \begin{bmatrix}
-0.707 & 0.707  \\\\\\  
-0.707 & -0.707 
\end{bmatrix} \cdot 
\begin{bmatrix}
1.5 & 0  \\\\\\  
0 & 0.5
\end{bmatrix}  \cdot \begin{bmatrix}
-0.707 & -0.707  \\\\\\  
0.707 & -0.707 
\end{bmatrix} \\\\\\
& = \begin{bmatrix}
1 & 0.5  \\\\\\  
0.5 & 1
\end{bmatrix}
\end{split}
\end{equation}
$$

Here you go! This is exactly the same as what [we have calculated before](https://jasontan-code.github.io/blog/calculate_pca/)!




&nbsp;  
&nbsp;  
&nbsp;  
&nbsp;  

## Decompose SPONGEBOB

Okay, plenty of dirty math. It's time to decompose our favorite Spongebob and Patrick. 

![](/images/SVD_by_hand/spongebob.jpeg)

[Source]( https://www.buzzfeed.com/staceygrant91/15-stages-of-waiting-in-line-for-a-roller-coaster-xpfm?utm_term=.laeDM4YjN&epik=dj0yJnU9ejBTaU1veGRvNW91RE9iM3ljaWRDOUY2TGJMVFV2SHUmcD0wJm49dGRGY2hRR3NkZjNMaUdidkVlc2dOdyZ0PUFBQUFBR0RySWJR#.ol5J4vZW0)


The first thing we need to do is to decolorize this picture, since a black-and-white picture could be represented as a matrix of numbers. Each number represents brightness of a pixel.



```r
# load imager for plot pictures 
library(imager)

# load the picture, you may right click and save the picture to your local disc, if you want to follow the process
spongebob = load.image("spongebob.jpeg") 

# make spongebob grey
greyspongebob = grayscale(spongebob)

plot(greyspongebob)
```
![](/images/SVD_by_hand/grepspongebob.png)

&nbsp;  

Now we can convert the grey picture into a matrix, which has a dimension of 625 * 415

```r
greyspongebob_mat = as.matrix(greyspongebob)
dim(greyspongebob_mat)
```

&nbsp;  

Time to decompose! 

```r
# use built-in function svd()
greyspongebob_svd = svd(greyspongebob_mat)
```

The result is a list with three elements:

 `greyspongebob_svd$u` contains the matrix $\mathbf{U}$
 
  `greyspongebob_svd$v` contains the matrix $\mathbf{V}$
  
  `greyspongebob_svd$d` contains a vector of singular values, but you can easily convert them to $\mathbf{\Sigma}$ by calling the function `diag(greyspongebob_svd$d)`
  
  
&nbsp;  
&nbsp;  

Now let's re-construct the picture by selecting top-n dimensions. We write a function to make the job easier.

```r
Reconstruct_Spongebob = function(dim){
  # reconstruct the matrix with top n dimentions
  mat = greyspongebob_svd$u[,1:dim] %*% diag(greyspongebob_svd$d[1:dim]) %*% t(greyspongebob_svd$v[,1:dim])
  
  # use imager package to redraw the picture
  mat %>% as.cimg(dim=dim(mat)) %>% plot(main=paste(dim, "Dimentions"))
}
```
&nbsp;  

Plot!
```r
par(mfrow=c(2,2))
Reconstruct_Spongebob(2)
Reconstruct_Spongebob(5)
Reconstruct_Spongebob(20)
plot(greyspongebob, main = "Original picture")
```


![](/images/SVD_by_hand/recreate.png)


As you can see, with 20 dimensions, we can reconstruct the picture reasonably well. 

But think about how many space we need to use. 
$$
dim( \mathbf{M}) = 625 \times 20 = 12500 \\\\\\
dim( \mathbf{\Sigma}) = 20 \times 1 = 20  \\\\\\
dim( \mathbf{M}) = 415 \times 20 = 8300$$

In total, we need $12500 + 20 + 8300 = 20820$ numbers to represent the reduced picture. 

However, we need $625 \times 415 = 259375$ numbers to represent the original picture! This is $10\times$ more than the reduced picture!



&nbsp;  
&nbsp;  
&nbsp;  
&nbsp;  
&nbsp;  
&nbsp;  

Okay, hope you find this post to be useful. If so, please don't hesitate to give it a thumb up!



&nbsp;  
&nbsp;  
&nbsp;  
&nbsp;  
&nbsp;  
&nbsp;  

## Reference:

Spongebob picture: https://www.pinterest.com/pin/848998967250483701/

SVD wiki: https://en.wikipedia.org/wiki/Singular_value_decomposition

Stack Exchange @amoeba https://stats.stackexchange.com/questions/134282/relationship-between-svd-and-pca-how-to-use-svd-to-perform-pca



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












