---
marp: true
theme: neobeam
paginate: true
math: katex
footer: '**Fabio Scielzo Ortiz**
         **Robust clustering in large multivariate mixed data**
         **10/07/24**'
---
<!-- _class: title -->
#  Robust clustering in large datasets of weighted multivariate heterogeneous data

## Fabio Scielzo Ortiz

> ## Master in Big Data Analytics
> Universidad Carlos III de Madrid

### Supervisor: Aurea Grané Chavéz

### Madrid, 10/07/24

![logo GitHub Logo](../assets/logo_uc3m_2.png)

---
<!-- header: 'Table of contents' -->
1. Motivation: clustering issues with mixed, weighted, and large-volume data
2. Classical Gower distance for mixed data: decomposition into three metrics
3. Presentation of robust metrics: robust G-Gower and robust RelMS
4. The k-medoids algorithm
5. Presentation of Fast k-medoids, k-Fold Fast-k-medoids algorithms
6. Simulation scenarios to test the algorithms
7. Results of the simulations
8. Application to real data
9. Developed Python packages

---

<!-- header: 'Motivation: clustering issues with mixed, weighted, and large-volume data' -->

- Traditional clustering algorithms like $k$-means are based in classical statistical distances, 
such as Euclidean, that are not suitable for multivariate mixed data-sets.
    - There is a necessity of metrics capable to deal with multivariate heterogeneous data 
    properly, to be use in clustering problems, among other possible applications.
    
    
- There are clustering algorithms like $k$-medoids that are able to use any kind of distance, 
but with a prohibitive computational cost in big data scenarios.
    - Providing an efficient version of $k$-medoids, to cluster heterogeneous multivariate data efficiently, using suitable distances, and even in big data environments is an relevant challenge.


- Applying clustering algorithms to weighted data, which are typical from official statistics 
studies, is another promising goal.

- Developing a methodology to apply clustering methods in supervised problems is another highlighted field.

- All these issues have been tackled along this master's thesis


---
<!-- header: 'Classical Gower distance for mixed data: decomposition into three metrics' -->

Considering a mixed-type data matrix $\mathbf{X}$, whose first $p_1$ columns contain the observations of quantitative variables, the following $p_2$ are of binary type and the last $p_3$ are of multi-class type, with $p=p_1 + p_2 + p_3$. 

-  The **Gower's similarity** coefficient between the pair of observations $\mathbf{x}_i,\mathbf{x}_r$ is defined in *Gower (1971)* as:
    $$
    s(\mathbf{x}_i,\mathbf{x}_r)_{Gower} = \frac{ \sum _{k=1}^{p_1} \left(1- |x_{ik} - x_{rk} |/G_k \right) + a(\mathbf{x}_i^B , \mathbf{x}_r^B)  + \phi(\mathbf{x}_i^M , \mathbf{x}_r^M) }{p  -  d(\mathbf{x}_i^B , \mathbf{x}_r^B) }
    $$

 
   where $G_k=\text{range}(X_k)=\max{(X_k)}-\min{(X_k)}$ is the range of the $k$-th quantitative variable, $a(\mathbf{x}_i^B, \mathbf{x}_r^B)$ and  $d(\mathbf{x}_i^B, \mathbf{x}_r^B)$ are, respectively, number of $(1,1)$-matches and $(0,0)$-matches between units $i$ and $r$ among the binary variables and
   $\phi(\mathbf{x}_i^M, \mathbf{x}_r^M)$ is the number of matches between units $i$ and $r$ among the multi-class categorical variables.
 

- Following to *Gower (1971)*, the **Gower's distance** between the pair of observations $(\mathbf{x}_i, \mathbf{x}_r)$  is defined as:
$$
\delta^2(\mathbf{x}_i, \mathbf{x}_r)_{Gower}= 1 - s(\mathbf{x}_i, \mathbf{x}_r)_{Gower} \hspace{0.1cm }
$$

---

**Properties:**

1. Gower similarity is the sum of different similarity coefficients, each of which is appropriate for a variable type (quantitative, binary and multi-class).

2. If we consider a set of quantitative variables, Gower similarity is the transformation to similarity of the range normalized Manhattan distance, so that it takes values in $[0,1]$ :
$$
\frac{1}{p} \, \sum _{k=1}^{p} \left(1- \frac{\mid x_{ik} - x_{jk} \mid}{G_k} \right)
$$

3. If we consider a set of binary variables, Gower's similarity coincides with Jaccard's.

4. If we consider a set of multi-class categorical variables, Gower similarity coincides with simple matching similarity coefficient.

---
<!-- header: 'Presentation of robust metrics: robust G-Gower and robust RelMS' -->

Following to *Grané (2021b)*, **Generalized Gower** distance (G-Gower) is defined as follows: 
 
1. Consider the data matrix partitioned in three sub-matrices $\mathbf{X}=[\mathbf{X}_Q\; \mathbf{X}_B \; \mathbf{X}_{M}]$, each of which corresponds
 to a variable type.

2. For each sub-matrix $\mathbf{X}_h$, $h=Q,B,M$, a distance matrix is calculated, using a proper distance function for each variable type:  $\hspace{0.1cm}\mathbf{D}_h = \left(  \delta_h(\mathbf{x}_i^h,\mathbf{x}_r^h) \right) _{  \{1\leq i,r\leq n\}}.$

3. For each $\mathbf{D}_h$, its geometric variability is calculated:
$$
VG_h = \frac{1}{2\,n^2} \sum _{i=1}^n \sum_{r=1}^n \delta_h^2 (\mathbf{x}_i^h , \mathbf{x}_r^h).
$$  

4. The corresponding sub-matrices of squared distances $\mathbf{D}_h^{(2)}$ are standardized to geometric variability one:
$$
\mathbf{D}_h^{(2)'} = \frac{1}{VG_h} \, \mathbf{D}_h ^{(2)}.
$$

5. Finally, the standardized sub-matrices of squared distances are combined to obtain **Generalized Gower's** distance:
$$
\mathbf{D}_{GG}^{(2)} = \mathbf{D}_Q^{(2)'} + \mathbf{D}_B^{(2)'} + \mathbf{D}_M^{(2)'}.
$$

<!---
**Why do we standardize using geometric variability?**

So that all the distance matrices that are combined are directly comparable, that is, that they all have the same dispersion.
This standardization was proposed in *Cuadras (1995)*, and is the optimal way to compare distance matrices.

**Remark:**

It should be noted that the previous algorithm requires that the similarity measures used for the binary and multi-class variables be transformed into distance measures, since the algorithm considers distance matrices from the start. This transformation of similarities to distances can be seen as a prior step to the application of the algorithm in the case of binary and multi-class variables.
--->

---

The methodology called **Related Metric Scaling** (RelMS) was proposed in *Cuadras (1998)* to obtain joint metrics that meet several criteria that allow eliminating redundant information. In *Albarran (2015)* was the first time that this technique was applied to mixed type data.

Assuming four first steps of the G-Gower distance are already followed, the RelMS is computed as follows: 

1. For each sub-matrix of squared distances the Gram matrix is computed: 
$$
\mathbf{G}_h = -\frac{1}{2}\,\mathbf{H}\, \mathbf{D} _h^{(2)'}\, \mathbf{H},
$$
where $\mathbf{H}= \mathbf{I} - \frac{1}{n}\, \mathbf{1}\, \mathbf{1}^\prime$ is the centering matrix, $\mathbf{I}$ is the identity matrix of size $n$ and $\mathbf{1}$ is a $n\times 1$ vector of ones. 

2. The Gram matrix of the joint metric is calculated by combining matrices $\mathbf{G}_h$,  for $h=Q,B,M$:
$$
\mathbf{G} = \sum_{h=Q,B,M} \mathbf{G}_h -\frac{1}{3} \sum _{k\neq h = Q,B,M} \mathbf{G}_k^{1/2} \, \mathbf{G}_h^{1/2}
$$
3. Finally, the joint metric is obtained as follows:
    $$
    \mathbf{D}^{(2)}_{RelMS} = \mathbf{g} \, \mathbf{1}^\prime + \mathbf{1 }\, \mathbf{g}^\prime - 2\, \mathbf{G},
    $$
   where $\mathbf{g}=\text{diag}(\mathbf{G})$ is a $n\times 1$ vector containing the diagonal elements of $\mathbf{G}$.

<!---
textbf{Why related metric scaling?}

This procedure allows eliminating redundancy of information between groups of variables, as proven in \cite{Grane2018}, \cite{Grane2021a}
    
\textbf{How to calculate the square root of $\mathbf{G}_h$?}

The square root of a matrix, and in this case of $\mathbf{G}_h$, can be calculated following different procedures, two of the most notable are the spectral decomposition and the singular value decomposition (SVD), as can be seen in \cite{meyer2000} and \cite{pena2002}.

Only if $\mathbf{G}_h$ is square, symmetric and positive semidefinite, the spectral decomposition of $\mathbf{G}_h$ coincides with the SV, and only in that case the square root of $\mathbf{G} _h$ is unique, and therefore the same square root of $\mathbf{G}_h$ is obtained when calculated by both SVD and spectral decomposition.

It is of interest to obtain a unique square root of $\mathbf{G}_h$, regardless of whether it is calculated by SVD or by spectral decomposition.
In the specific case of $\mathbf{G}_h$ it is true that it is symmetric and square by construction, as the distance matrices are symmetric and square, but it is not necessarily positive semi-definite. Therefore, in order to calculate $\mathbf{G}_h^{1/2}$ and for it to be unique, it must be required that $\mathbf{G}_h$ be positive semi-definite, that is, that it does not have negative eigenvalues. $\\$

The procedure to calculate $\mathbf{G}_h^{1/2}$ will be as follows:

\begin{itemize}
\item If $\mathbf{G}_h$ is SDP (positive semi-definite), there is no problem, and the square root of $\mathbf{G}_h$ can be calculated by SVD or spectral decomposition interchangeably, arriving at the same result, since it will be unique.

\item If $\mathbf{G}_h$ is not SDP, that is, it has some negative eigenvalue, then a relevant transformation must be applied on $\mathbf{D}_h^{(2)}$, and subsequently recalculate $ \mathbf{G}_h$, which will be SDP after applying the transformation.

Once $\mathbf{G}_h$ is SDP, either because it was from the start, or because it was not SDP but a relevant transformation has been applied to it, the square root of $\mathbf{G}_h can be calculated. $ for SVD or spectral decomposition interchangeably, and this will be unique.
\end{itemize}

\textbf{Remark:}

In the empirical tests carried out in Python, the root of $\mathbf{G}_h$ is calculated, after making the relevant checks and transformations, using SVD, since it has been proven to be computationally more efficient than spectral decomposition.

 
\textbf{Transformation to force positive semi-definiteness}

If $\mathbf{G}_h$ has negative eigenvalues, and therefore is not positive semidefinite, $\mathbf{G}_h^{1/2}$ cannot be calculated through the spectral decomposition, and despite that if we could calculate it by SVD, it would not be unique. As mentioned in the previous point, it is of interest that $\mathbf{G}_h$ is SDP so that its square root is unique. The following theorem can be seen in \cite{borg2005} and allows transforming $\mathbf{G}_h$ in such a way that its transformed version is SDP.

If $\mathbf{G}_h$ is not positive semidefinite, then the following Gram matrix:
$$
  \mathbf{\widetilde{G}}_h = - \frac{1}{2}\, \mathbf{H} \, \mathbf{\widetilde{D}}_h^{(2)'} \, \mathbf{H}
$$
is positive semi-definite, where 
\[
\mathbf{\widetilde{D}}_h^{(2)'} = \mathbf{D}_h^{(2)'} + \omega \, \mathbf{1} \, \mathbf{1}^\prime -\omega \, \mathbf{I},
\]

with $\omega \geq 2 \, | \lambda _{min} | $, with $\lambda _{min} $ being the smallest negative eigenvalue of $\mathbf{G}_h$ and $\mathbf{H}$, $\mathbf{I}$ and $\mathbf{1}$ as before.

}

--->

---

$\\$
Given a data matrix $\mathbf{X}=(X_1,...,X_p)$ of quantitative variables, a **robust Mahalanobis** (squared) distance between the pair of observations $(\textbf{x}_i, \textbf{x}_r)$ can be defined as:

$$
  \delta^2(\textbf{x}_i,\textbf{x}_r)_{RMaha}  =   (\textbf{x}_i - \textbf{x}_r ) ^\prime \, \mathbf{S}^{-1}_R \, (\textbf{x}_i - \textbf{x}_r )
$$

where $\mathbf{S}_R$ is a robust estimation of the covariance matrix of $\mathbf{X}$.

For obtaining the robust covariance matrix \cite{gnanadesikan1997} was followed.

$\\$
**Proposed metrics**

- For a context with heterogeneous multivariate data with outliers, the Generalized Gower distance or the Related Metric Scaling will be proposed, using the Robust Mahalanobis distance for the quantitative variables, for the binary variables Jaccard or Sokal, and for multi-classes the simple matching coefficient. 

- However, in a heterogeneous multivariate context without outliers, our proposal would be the Generalized Gower distance or the Related Metric Scaling, using Mahalanobis for the quantitative ones, for the binary ones Jaccard or Sokal, and for the multi-class ones the simple coefficient matching.
<!---

\note{

\subsection{ Robust variance estimation }

Let $X_j$ be a variable of quantitative type.

\subsubsection{ Median absolute deviation }

   Following this proposal, a robust estimation of the variance of ${X}_j$ is defined as:
 
  $$\sigma^{ 2}_R(X_j)   =   MAD(X_j)^{ 2} \hspace{0.05cm } =   Me  \Bigl[   \Bigl(   \left|   x_{ij} - Me(X_j )   \right|   :   i = 1,...,n   \Bigr)   \Bigr] ^{ 2}$$

  
 
  \subsubsection{ Trimmed variance }


  The $\alpha$-trimmed version of variable $X_j$ is defined as:

$$X_j^{ \alpha}  =  \Bigl(  x_{ij}  \hspace{0.1cm}\mathbf{: }\hspace{0.1cm}  i\in \lbrace 1,\dots,n \rbrace   ,  \hspace{0.1cm } x_{ij} \in \big[  Q(\alpha/2, X_j)  ,  \hspace{0.1cm } Q(1-\alpha/2 , X_j)\big]   \Bigr)$$

where $Q(z, X_j)$ is the quantile $z\in [0,1]$ of $X_j$, that is, the value such that the proportion of values of $X_j$ that are below it is $ z$.

Following this proposal, a robust estimation of the variance of  $X_j$ is defined as:
  $$\sigma^{ 2}_R(X_j)   =   \sigma^{ 2}(X_j^\alpha)   =   \dfrac{1}{n} \, \sum _{i=1}^n   \Bigl(   x_{ij} - \overline{X_j^\alpha}   \Bigr)^2$$

where 
$$\overline{X_j^\alpha} = \dfrac{1}{n}\, \sum _{i\in \mathcal{I}(X_j^\alpha)} x_{ij},
$$
being $\mathcal{I}(X_j^\alpha)= \Bigl\{   i \in \lbrace 1,\dots , n \rbrace  \hspace{0.1cm } :\hspace{0.1cm }   x_{ij} \in X_j^\alpha   \Bigr\}  $ the set of indices of the observations of $X_j^\alpha$ .

\vspace{0.2cm}

\textbf{Remark:}

$\alpha$ is a parameter that modulates the number of observations of the original variable that will be trimmed for the calculation of the robust variance. $\alpha \in [0,1]$ and the larger it is, the more observations of the original variable will be trimmed.

%\newpage

\subsubsection{Winsorized variance }

 
  The $\alpha$-winsorized version of the variable $X_j$ is defined as:

     $$X_j^{\alpha}  =  \Bigl(  h(x)  \hspace{0.1cm }\mathbf{:} \hspace{0.1cm } x \in X_j   \Bigr)$$
    
where function $h$ is defined as follows:
$$
h(x)   =   \begin{cases}
      a(\alpha )  , \hspace{0.1cm }\text{if } \hspace{0.1cm }x \in A(\alpha), \\
      b(\alpha )  , \hspace{0.1cm }\text{if}\hspace{0.1cm } x \in B(\alpha), \\
      x  , \hspace{0.1cm }\text{if } \hspace{0.1cm } x \in X_j   \hspace{0.1cm }\text{but}\hspace{0.1cm }   x \not\in A(\alpha)   \hspace{0.1cm }\text{nor}\hspace{0.1cm }   B(\alpha), \end{cases}
$$
where $a(\alpha)$ is the value of $X_j$ that is immediately greater than $ Q(\alpha/2,X_j)$, $b(\alpha)$ is the value of $X_j$ that is immediately less than $ Q(1-\alpha/2, X_j )$
and sets $A(\alpha)  =  \bigl\{   x_{ij}  :  x_{ij }  \leq  Q(\alpha/2  ,  X_j)   \bigr\}$, $B(\alpha)  =  \lbrace   x_{ij}  :  x_{ij}  \geq   Q(1-\alpha/2  ,  X_j)   \bigr\}$.

Following this proposal, a robust estimation of the variance of $X_j$ is defined as:
  $$\sigma^{ 2}_R(X_j)   =   \sigma^{ 2}(X_j^{\alpha} )   =   \dfrac{1}{n} \cdot \sum _{i=1}^n   \Bigl(   x_{ ij} - \overline{X_j^{\alpha}}   \Bigr)^2$$

where
$\overline{X_j^\alpha} = \frac{1}{n}\cdot \sum _{i\in \mathcal{I}(X_j^\alpha)} x_{ij}$
and $\mathcal{I}(X_j^\alpha)= \Bigl\{ i \in \lbrace 1,\dots , n \rbrace :  x_{ij} \in X_j^\alpha   \Bigr\}$ is the set of indices of the observations of $X_j^\alpha$ .


\vspace{0.2cm}

\textbf{Remark:}

$\alpha$ is the parameter that modulates the winsorization volume. $\alpha \in [0,1]$ and the larger it is, the more observations will be winsorized, since more observations will be included in $A(\alpha)$ and $B(\alpha)$, and will be set as $a (\alpha)$ or $b(\alpha)$, respectively, that is, they will be winsorized.


\subsection{ Robust correlation estimation }

Let $X_j$ and $X_k$ be variables of quantitative type, and let $Z_j$ be the auxiliary variable defined as $Z_j = \dfrac{X_j}{ \sqrt{\sigma^2_R(X_j)} } $. Then, a robust estimation of Pearson's correlation between ${X}_k $ and $ {X}_j$ is defined as:

\begin{equation}
  \label{eq.coeff.correl}
r_{kj}^{ R}  =  \dfrac{ \sigma^{ 2}_1 - \sigma^{ 2}_2 }{\sigma_1^{ 2}\hspace{0.1cm }+ \sigma_2^{ 2}}
\end{equation}

$\vspace{0.05cm}$

where $ \sigma_1^{ 2} = \sigma^{ 2}_R(Z_k+Z_j) $ and $ \sigma_2^{ 2} = \sigma^{ 2}_R(Z_k+Z_j)  $.




\subsection{ Robust covariance estimation }

Using the formula (\ref{eq.coeff.correl}) and the relationship between Pearson's correlation and covariance, we have a robust estimation for the covariance between $X_j$ and $\hspace {0.02cm}X_k$:


\begin{equation}
  \label{eq.coeff.cov}
  s_{kj}^{ R}  =  r_{kj}^{ R} \, \sqrt{  \sigma_R^{ 2}(X_j) \, \sigma_R^{ 2}(X_k)  }
\end{equation}

  \subsection{ Robust estimation of the correlation matrix }

A robust estimation of the correlation matrix of $\mathbf{X}$ is:

$$\mathbf{R}_R  =  \Bigl(r_{kj}^{ R}   \Bigr)_{k,j = 1,\dots ,p}$$

where $  r_{kj}^{ R} $ is a robust estimation of Pearson's correlation between $  {X}_k\hspace{0.03cm }$ and $  {X}_j $, defined  in (\ref{eq.coeff.correl}).

 
 \subsection{ Robust estimation of the covariance matrix }

A robust estimation of the covariance matrix of $\mathbf{X}$ is:

$$\mathbf{S}_R  =  \Bigl(   s_{kj}^{ R}   \Bigr)_{k,j = 1,\dots ,p}$$


where $  s_{kj}^{ R} $ is a robust estimation of the covariance between $ {X}_k\hspace {0.03cm}$ and $ {X}_j $, defined in (\ref{eq.coeff.cov}).

%\newpage

\subsection{ Inverse of the robust estimation of the covariance matrix }

To calculate the robust Mahalanobis distance it is necessary to calculate the inverse of $\mathbf{S}_R$.

\begin{itemize}
\item If $ \mathbf{S}_R $ is positive definite, then its inverse can be calculated.

\item If $\mathbf{S}_R$ is positive semidefinite, then it has no inverse, but its Moore-Penrose pseudo-inverse could be calculated.

\item If $\mathbf{S}_R$ is positive non-semidefinite, then it can be invertible (if it has no null eigenvalues) or non-invertible (if it has null eigenvalues). But even in the first case, even though it had an inverse, when using it with the Mahalanobis distance negative or even imaginary distances could be obtained, which would not make sense. Therefore, this is the most problematic case.
\end{itemize}

\textbf{Remarks:}
\begin{enumerate}
\item The condition for a matrix to be invertible is that its determinant is different from zero, and since the determinant of a matrix can be decomposed into the product of its eigenvalues, then, a matrix is invertible if and only if all its eigenvalues are different from zero.
\begin{gather*}
\vspace{-1cm}
\exists  \hspace{0.1cm } S_R^{-1}  \Leftrightarrow det(S_R) \hspace{0.086cm}=  \lambda_1 \cdot . .. \cdot \lambda_n  \neq  0 \\
 \Leftrightarrow  S_R  \hspace{0.1cm }\text{does not have eigenvalues equal to zero.}
\end{gather*}


\item That $\mathbf{R}_R$ is positive definite means that $\mathbf{S}_R$ is also positive.

\end{enumerate}



\subsubsection{Delvin transformation to obtain $\mathbf{S}_R$ positive definite }

As seen in the previous section, it is important that $\mathbf{S}_R$ is positive definite in order to calculate its inverse and not encounter problems in calculating the Mahalanobis distance.

\cite{delvin1975} proposed a transformation such that, when $\mathbf{S}_R$ is not positive definite, a transformed version is obtained that is positive definite or at least closer to being positive definite.

The proposed transformation $\mathcal{G}$ of $\mathbf{R}_R$ is as follows:

$$\mathcal{G}(\mathbf{R}_R) = \Bigl[   g   \bigl( \hspace{0.05 cm} r_{kj}^{ R} \hspace{0.05 cm} \bigr)   \Bigr]_{k,j=1,\dots ,p}$$
    
    
where:

$$
g   \bigl( \hspace{0.05 cm} r_{kj}^{ R} \hspace{0.05 cm} \bigr)   =   \begin{cases}
     0  ,  \text{ if }  \left|   r_{kj}^R   \right|  \leq  z(\epsilon), \\
    z^{-1}\left(  z( r_{kj}^{ R} )  +  \epsilon \right)  ,  \text{ if }  r_{ kj}^{ R}  <  -z(\epsilon), \\
    z^{-1}\left(  z( r_{kj}^{ R} )  -  \epsilon \right)  ,  \text{ if }  r_{ kj}^{ R}   >   z(\epsilon), \end{cases}
$$

where $\epsilon$ is a small positive number, for example, $\epsilon=0.05$, $z (x) = \arctan{h (x)} = \frac{1}{2}\,\log\left(\frac{1+x}{1-x}\right)$, $z^{-1} (x)  =  \tan{h(x)}$, and $z(\epsilon)= z(0.05) \approx 0.05$.


 
\textbf{How does the $g$ transformation work?}

\begin{itemize}
     \item If $\hspace{0.05 cm}r_{kj}>0.05\hspace{0.05 cm}$, then, $\hspace{0.03 cm} g(r_{kj}) \hspace{0.05 cm}=\hspace{ 0.05 cm} r_{kj} - \varepsilon\hspace{0.05 cm}$, with $\hspace{0.05 cm}\varepsilon >0\hspace{0.05 cm}$ small. That is, $\hspace{0.05 cm}g\hspace{0.03 cm}$ transforms $\hspace{0.05 cm}r_{kj}\hspace{0.05 cm}$ into a slightly lower correlation.


     \item If $\hspace{0.05 cm}r_{kj}<-0.05\hspace{0.05 cm}$, then, $\hspace{0.03 cm}g(r_{jk})\hspace{0.05 cm} =\hspace {0.05 cm} r_{jk} + \varepsilon\hspace{0.03 cm}$, with $\hspace{0.03 cm}\varepsilon >0\hspace{0.03 cm}$ small. That is, $\hspace{0.03 cm}g\hspace{0.03 cm}$ transforms $\hspace{0.05 cm}r_{kj}\hspace{0.05 cm}$ into a slightly higher correlation.


    \item If $\hspace{0.05 cm}r_{kj} \in \left[-0.05,   0.05 \right]\hspace{0.05 cm}$, then, $g(r_{kj}) \hspace{0.05 cm}=\hspace{0.05 cm}0$. That is, $\hspace{0.03 cm}g\hspace{0.03 cm}$ transforms $\hspace{0.05 cm}r_{kj}\hspace{0.05 cm}$ into zero.
\end{itemize}

 


  \subsubsection{ Delvin algorithm to obtain positive definite $\mathbf{S}_R$ }

The following algorithm uses the Delvin transformation to, given the matrix $\mathbf{S}_R$, in the case that it is not positive definite, obtain a transformation that is positive definite, and therefore invertible, and that therefore can be used without problems for the calculation of the robust Mahalanobis distance.

\begin{itemize}
\item $\mathcal{G}(\mathbf{R}_R)$ is calculated.

\item Is checked whether $\mathcal{G}(\mathbf{R}_R)$ is positive definite.

    \begin{itemize}
    \item If $G(\mathbf{R}_R)$ is positive definite, then:
       \begin{itemize}
        \item The positive definite transformation of $\hspace{0.06cm}\mathbf{S}_R\hspace{0.06cm}$ is obtained as $\hspace{0.06cm}\mathbf{T}\cdot \mathcal{G}( \mathbf{R}_R) \cdot \mathbf{ T}$ , where $\hspace{0.06cm}\mathbf{T}=diag\bigl(  \sigma_R(X_1),..., \sigma_R(X_p) \bigr).$
        
         \item The algorithm stops.
         \end{itemize}
    \item If $\mathcal{G}(\mathbf{R}_R)$ is not positive definite, then:
         \begin{itemize}
         \item $\mathbf{R}_R$ is updated: $ \mathbf{R}_R \leftarrow \mathcal{G}(\mathbf{R}_R)$
        
         \item Returns to the initial step of the algorithm.
        \end{itemize}
     \end{itemize}
\end{itemize}
 
\textbf{Remark:}

Although in this work we have presented the transformation and the Delvin algorithm linked to the context at hand, both can be applied to any matrix, with the objective of obtaining positive definite matrices.

}
--->

---
<!-- header: 'k-medoids algorithm
' -->

**$k$-medoids**

The $k$-medoids algorithm, proposed by Kaufman (1990) and refined by Park et al. (2009), partitions a dataset into $k$ clusters, with each cluster represented by a medoid—the most central point according to a distance metric. The algorithm starts by defining the initial $k$ clusters based on a chosen distance metric and ordering observations by their calculated distances. Observations are assigned to the nearest medoid to form initial clusters.

Next, the sum of intra-cluster variances is calculated to measure cluster similarity. Observations are iteratively reassigned to the closest clusters, and new medoids are recalculated. This iterative process continues until a stopping criterion is met, such as a stable sum of intra-cluster variances over several iterations, or until a maximum number of iterations is reached.

The final cluster configuration is the one that minimizes locally the sum of intra-cluster variances, either when the stopping criterion is met or at the last iteration. This configuration provides a vector indicating the cluster membership of each observation, which can be interpreted as predictions of an underlying response variable.

---
<!-- header: 'Presentation of Fast k-medoids and k-Fold Fast-k-medoids algorithms
' -->

**Fast $k$-medoids**

The basic idea of Fast $k$-medoids algorithm is to apply $k$-medoids on a random sample of the data matrix $\mathbf{X}$, clustering those observations with the algorithm, and clustering the remaining observations applying the rule of assigning each 
out of sample observation to its nearest cluster, which means to the cluster with the nearest medoid.

The steps of the Fast $k$-medoids algorithm are the following:

  1. A random sample $\mathbf{X_S}$ of size $n_S$ of $\mathbf{X}$ is taken.
  2. $k$-medoids is applied on  $\mathbf{X_S}$, therefore the sample observations are clustered leading to the sample clusters $C_1^S,\dots , C_k^S$.
  3. The out of sample observations $\mathbf{X_{\overline{S}}}$ are assigned to its nearest cluster, that is, $x_i \in\mathbf{X_{\overline{S}}}$ is assigned to cluster $C_{r^*}^S$ 
  where $r^* = arg  \underset{r\in\lbrace 1,\dots,k\rbrace}{Min}   \delta (\textbf{x}_i , \overline{\textbf{x}}_{C_r^S} )$, being  $\overline{\textbf{x}}_{C_r^S}$ the medoid of cluster $C_r^S$.
  4. After all the observation have been clustered, a vector indicating the clusters to which each observation belong 
  can be build and interpreted as the predictions of an underlying or hidden response variable:
$$\hat{y}_i = r  \Leftrightarrow  x_i \in C_r^S , \quad \forall i=1,\dots,n; r=1,\dots,k$$


<!---
**Key parameters**

The key parameters of Fast $k$-medoids algorithm are those already key in $k$-medoids, namely the \textit{distance} and the \textit{number of clusters}, but now there is a new one that is specially important, that is the \textit{size of the sample} that is taken of the original data an is clustered with classic $k$-medoids, and works as the base of the Fast $k$-medoids clustering procedure. See also Figure~\ref{fig:fast.k.medoids}.
--->

---

$\\$ 
**$k$-Fold Fast $k$-medoids**

This algorithm has been developed after observing that Fast $k$-medoids suffered a lose of accuracy when the data
 becomes bigger. The basic idea of $k$-Fold Fast $k$-medoids is to split the data in folds and apply Fast $k$-medoids to each 
 fold, then build a data matrix with the resulting medoids of each fold and apply Fast $k$-medoids again on this medoids 
 data matrix, then assign the original observations to the one of the final clustering by assigning each observation to its medoid.

The steps of the $k$-Fold Fast $k$-medoids algorithm are the following:

1. The data matrix $\mathbf{X}$ is split in $q$ folds $\mathbf{X}_{F_1},\dots, \mathbf{X}_{F_{q}}$
2. Fast $k$-medoids is applied on each fold $\mathbf{X}_{F_j}$, leading to the clusters $C_1^{F_j},\dots, C_k^{F_j}$, and to the medoids $\overline{\textbf{x}}_{1^{F_j}}, \dots, \overline{\textbf{x}}_{k^{F_j}}$, 
for $j=1,\dots, k_F$.
3. A new data matrix is build concatenating the medoids of each fold by rows: $\mathbf{X_M}$
4. Fast $k$-medoids is applied on $X_M$ leading to clusters $C_1^{\mathbf{X_M}}, \dots, C_k^{\mathbf{X_M}}$ made up by the previous medoids.
5. Final clusters rule: the original observation $x_i$ is assigned to the step 4 cluster that contains the medoid of the 
step 2 cluster to which $x_i$ belongs, and this for all $i=1,\ldots , n$. 
   For all $i=1,\dots,n, j=1,\dots,k_F$ and $h,r=1,\dots,k$, if $\mathbf{x}_i\in C_h^{F_j}$, then,  $\mathbf{x}_i$ is assigned to $C_r^{\mathbf{X_M}}$ $\Leftrightarrow$ $\overline{\textbf{x}}_{h^{F_j}} \in C_r^{\mathbf{X_M}}$.
  
<!---
**Key parameters:**

The key parameters of this algorithm are the ones already key in Fast $k$-Medoids, namely the \textit{distance}, the \textit{number of clusters}, and the \textit{sample size},  but now there is a new one that is specially important, that is the \textit{number of folds (splits)} in wich the data is divided before clustering, as was explained above and in Figure~\ref{fig:k.fold.fast.k.medoids}.
--->

---
<!-- header: 'Simulation scenarios to test the algorithms' -->


Eight simulated datasets were generated using the *make_blobs* function from *scikit-learn*. This function creates isotropic Gaussian blobs for clustering, allowing control over the number of clusters, standard deviation of the clusters, number of variables, and number of observations.

The advantage of these simulations is that they are specifically designed for clustering algorithms, with known real clusters, allowing performance to be measured in a similar way to supervised classification. This simulations served for robust testing and evaluation of clustering algorithms.

Initially, all variables are quantitative, but some were converted to categorical for analysis.

Outliers were artificially introduced in some of the quantitative variables by mean of  *outlier_contamination* function, which modified the data by injecting extreme values.

---

- Shared parameters of the simulations:

  - *n_features*: $8$ ; $\hspace{0.1cm}$ Quantitative: $X_1, X_2, X_3, X_4$; $\hspace{0.1cm}$ Binary: $X_5, X_6$; $\hspace{0.1cm}$ Multi-class: $X_7, X_8$

- Particular parameters of the simulations:

  | simulation  | *n_samples* | *centers* | *clusters_std* | *outlier_contaminations* |
  |:-------------:|:----------:|:----------:|:----------:|:----------:|
  |     1       |   35k    |   4      |  [2,2,2,3]   |  True  ($X_1$: *prop_above*$=0.05$, $X_2$: *prop_below*$=0.05$)  |
  |     2       | 35k  |  4  | [2,2,2,3]   |  False   |
  |     3       | 100k  | 4   | [2,2,2,3]   |  True ($X_1$: *prop_above*$=0.05$, $X_2$: *prop_below*=0.05) |
  |     4       | 300k  | 3   |  [2,2,3]  |  True ($X_1$: *prop_above*$=0.05$, $X_2$: *prop_below*$=0.05$)  |
  |     5       | 600k  | 3   |  [2,2,3]  |  True ($X_1$: *prop_above*$=0.05$, $X_2$: *prop_below*$=0.05$) |
  |     6       | 1M  | 3   |  [2,2,3]  |  True ($X_1$: *prop_above*$=0.05$, $X_2$: *prop_below*$=0.05$) |
  |     7       | 300k  |  3  |  [2,2,3]  |  False  |
  |     8       | 300k  | 3   |  [2,2,3]  |  True ($X_1$: *prop_above*$=0.085$, $X_2, X_3$: *prop_below*$=0.1, 0.06$) |

---
<!-- header: 'Results of the simulations' -->
$\\[0.3cm]$
#### Fast $k$-medoids vs $k$-medoids


<div style="display: flex; align-items: flex-start; padding-left: 20px;">
    <img src="https://raw.githubusercontent.com/FabioScielzoOrtiz/TFM-Presentation/main/assets/kmedoids_vs_fast_kmedoids.jpg" alt="Simulation 3" width="770" style="margin-right: 5px;" />
    <div style="font-size: 16px; line-height: 1.35; margin-right: 20px;">
        <p><strong>Predictive Performance:</strong></p>
        <ul>
            <li>Fast <i>k</i>-medoids is much more accurate for all the sample sizes than <i>k</i>-medoids with Euclidean distance.</li>
            <li>These big difference could be due to the distances  instead of the clustering algorithm.</li>
            <li>The differences are much smaller when <i>k</i>-medoids uses Generalized Gower, although Fast <i>k</i>-medoids is still better for all the sample sizes, except for the smallest one.</li>
        </ul>
        <p><strong>Computational Performance:</strong></p>
        <ul>
            <li>Fast <i>k</i>-medoids is indeed much faster than <i>k</i>-medoids, even when it uses the Euclidean, which is one of the fastest distances.</li>
            <li>When data becomes sufficiently large, applying <i>k</i>-medoids is in the best case not practical  because of the huge amount of time it requires to be run, and in the worst case not feasible due to memory errors.</li>
        </ul>
        <p><strong>Summing up:</strong></p>
        <ul>
            <li>Fast <i>k</i>-medoids is much faster an equally or even more accurate than <i>k</i>-medoids.</li>
        </ul>
    </div>
</div>

---

$\\[3.2cm]$
#### Simulation 3

- *n=100k, k=4, outlier contamination.* 
<div style="display: flex; align-items: flex-start; padding-left: 35px;">
    <img src="https://raw.githubusercontent.com/FabioScielzoOrtiz/TFM-Presentation/main/assets/kmedoids_simulation_3_comparison.jpg" alt="Simulation 3" width="750" style="margin-right: 10px;" />
    <div style="font-size: 16px; line-height: 1.6; margin-right: 25px;">
        <p><strong>Predictive Performance:</strong></p>
        <ul>
            <li>Fast <i>k</i>-medoids excels in accuracy and Rand Index.</li>
            <li>Proposed algorithms outperform significantly all other tested methods.</li>
            <li>Big accuracy difference: best proposed method (0.92) vs. best others (0.70).</li>
            <li>Top six algorithms use Generalized Gower with Robust Mahalanobis distance.</li>
        </ul>
        <p><strong>Computational Performance:</strong></p>
        <ul>
            <li>Most expensive within proposed method: 58.78 seconds.</li>
            <li>Some algorithms (e.g., <i>k</i>-medoids, Diana) are not feasible.</li>
            <li>Proposed methods are generally more expensive.</li>
            <li>Best proposed method: 45.87 seconds.</li>
        </ul>
    </div>
</div>

---

<div style="display: flex; align-items: flex-start; padding-left: 35px;">
    <img src="https://raw.githubusercontent.com/FabioScielzoOrtiz/TFM-Presentation/main/assets/mds_plot_simulation_3.jpg" alt="Simulation 3" width="750" style="margin-right: 10px;" />
    <div style="font-size: 16px; line-height: 1.6; margin-right: 25px;">
        <p>TO DO</p>
    </div>
</div>

---

<!---
$\\[1.6cm]$

#### Simulation 5
- *n=600k, k=3, outlier contamination.* 
<div style="display: flex; align-items: flex-start; padding-left: 35px;">
    <img src="https://raw.githubusercontent.com/FabioScielzoOrtiz/TFM-Presentation/main/assets/kmedoids_simulation_5_comparison.jpg" alt="Simulation 3" width="750" style="margin-right: 5px;" />
    <div style="font-size: 16px; line-height: 1.6; margin-right: 25px;">
      <p><strong>Predictive Performance:</strong></p>
      <ul>
          <li>Fast <i>k</i>-medoids is the algorithm with the best performance.</li>
          <li>All of our proposed clustering algorithms are better than all the other tested clustering methods, except MiniBatch <i>k</i>-means, which is the fourth best algorithm.</li>
          <li>The difference between the accuracy of our best method (0.93) and the best of the others (0.916) is tiny, but with respect to the second (0.65) is much more significant.</li>
      </ul>
      <p><strong>Computational Performance:</strong></p>
      <ul>
          <li>The computationally most expensive method is one of our proposals and takes 238 seconds.</li>
          <li>But the top five of our method take between 40 and 63 seconds.</li>
          <li>There are algorithms that are directly not feasible.</li>
          <li>Our proposed algorithms are, in general, more expensive than the other feasible methods tested.</li>
      </ul>
    </div>
</div>

--->

$\\[1.5cm]$

#### Simulation 6
- *n=1M, k=3, outlier contamination.* 
<div style="display: flex; align-items: flex-start; padding-left: 35px;">
    <img src="https://raw.githubusercontent.com/FabioScielzoOrtiz/TFM-Presentation/main/assets/kmedoids_simulation_6_comparison.jpg" alt="Simulation 3" width="750" style="margin-right: 10px;" />
    <div style="font-size: 16px; line-height: 1.7; margin-right: 25px;">
      <p><strong>Predictive Performance:</strong></p>
      <ul>
          <li>$k$-Folds Fast $k$-medoids is the algorithm with the best performance</li>
          <li>All of our proposed clustering algorithms are better than all the other tested clustering methods, except MiniBatch $k$-means, which is the second best algorithm.</li>
          <li>The difference between the accuracy of our best method (0.925) and the best of the others (0.918) is tiny, but with respect to the second (0.65), it is much more significant.</li>
      </ul>
      <p><strong>Computational Performance:</strong></p>
      <ul>
          <li>The computationally most expensive method is one of our proposals and takes 356 seconds to be executed.</li>
          <li>But our two best methods take less than 84 seconds.</li>
          <li>There are algorithms that are directly not feasible.</li>
          <li>Our proposed algorithms are, in general, more expensive than the other feasible methods tested.</li>
      </ul>
    </div>
</div>


---

$\\[3.2cm]$

#### Simulation 7
- *n=300k, k=3, without outlier contamination.* 
<div style="display: flex; align-items: flex-start; padding-left: 35px;">
    <img src="https://raw.githubusercontent.com/FabioScielzoOrtiz/TFM-Presentation/main/assets/kmedoids_simulation_7_comparison.jpg" alt="Simulation 3" width="750" style="margin-right: 10px;" />
    <div style="font-size: 16px; line-height: 2; margin-right: 25px;">
        <p>TO DO</p>
    </div>
</div>

---
<!-- header: 'Application to real data' -->
$\\$
The main aim was apply clustering for interpreting SHARE data using Fast $k$-medoids, incorporating sample weights. 

SHARE stands for urvey of Health, Ageing and Retirement in Europe (SHARE), which is the largest
cross-European social science panel study dataset covering insights into European individuals’ public health and socioeconomic living conditions.

The first wave is clustered using quantitative $(\texttt{health}, \texttt{dependency}, \texttt{selfperceived}, \texttt{socio\_economic})$ and categorical $(\texttt{gender}, \texttt{agegroup}, \texttt{marriage})$ variables, with $k=2$ groups and Generalized Gower distance. The two groups are analyzed longitudinally across waves $(2015, 2017, 2020)$ using relevant SHARE variables to reveal trends over time.


- **Cluster 0:** consists predominantly of older females, many of whom live alone and are from Southern Europe. This group exhibits worse dependency, self-perceived, and global indicators across all waves. They also show a worse socio-economic indicator in the first wave, with no significant differences in subsequent waves. However, they have better health status in the first and second waves, with no significant differences in the last wave.


- **Cluster 1:** is characterized by younger males, many of whom are married and from Northern Europe. This cluster has better dependency, self-perceived, and global indicators throughout all waves. They also exhibit a better socio-economic indicator in the first wave, with no significant differences in later waves. Conversely, they have worse health status in the first and second waves, but there are no significant differences in the last wave.

---

$\\[1.5cm]$

<div style="display: flex; align-items: flex-start; padding-left: 55px;">
    <img src="https://raw.githubusercontent.com/FabioScielzoOrtiz/TFM-Presentation/main/assets/no_wave_variables.png" alt="Simulation 3" width="900" style="margin-left: 120px;" />
</div>

---

$\\[3.5cm]$

<div style="display: flex; align-items: flex-start; padding-left: 100px;">
    <img src="https://raw.githubusercontent.com/FabioScielzoOrtiz/TFM-Presentation/main/assets/clustering_interpretation_quant.jpg" alt="Simulation 3" width="750" style="margin-left: 160px;" />
</div>

---
<!-- header: 'Developed Python packages' -->


---



---



---



