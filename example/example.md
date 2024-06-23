---
marp: true
theme: neobeam
paginate: true
math: katex
footer: '**Fabio Scielzo Ortiz**
         **Robust clustering for multivariate mixed data**
         **10/07/24**'
---
<!-- _class: title -->
# Robust clustering for multivariate mixed data

## Fabio Scielzo Ortiz

> ## Master in Big Data Analytics
> Universidad Carlos III de Madrid

### 10/07/24

![logo GitHub Logo](../assets/logo_uc3m_2.png)

---
<!-- header: 'Table of contents' -->
1. Motivation: clustering issues with mixed, weighted, and large-volume data
2. Classical Gower distance for mixed data: decomposition into three metrics
3. Presentation of robust metrics: robust G-Gower and robust RelMS
4. Presentation of Fast k-medoids, k-Fold Fast-k-medoids algorithms
5. Simulation scenarios to test the algorithms
6. Results of the simulations
7. Application to real data
8. Developed Python packages
---
<!-- header: 'Normal text' -->

- Traditional clustering algorithms like $k$-means are based in classical statistical distances, 
such as Euclidean, that are not suitable for multivariate mixed data-sets.
    - There is a necessity of metrics capable to deal with multivariate heterogeneous data properly, to be use in clustering problems, among other possible applications.
    
    
- There are clustering algorithms like $k$-medoids that are able to use any kind of distance, 
but with a prohibitive computational cost in big data scenarios.
    - Providing an efficient version of $k$-medoids, to cluster heterogeneous multivariate data efficiently, using suitable distances, and even in big data environments is an relevant challenge.

---


- Applying clustering algorithms to weighted data, which are typical from official statistics 
studies, is another promising goal.

- Developing a methodology to apply clustering methods in supervised problems is another highlighted field.

- All these issues have been tackled along this master's thesis


<!---
This is a hidden note
--->

---
<!-- header: 'Code blocks' -->
We can define ``variables`` inline, and code in blocks (with syntax highlighting!!!):
```java
    if (marp) {
        apply.neobeam();
    }
```
---
<!-- header: 'Mathematics corner' -->
## Formulas
> The length of a vector can be computed by the following formula
> $$
||\overline v|| = \sqrt{a^2 + b^2} \\
\text{where } \overline v = (a,b)
$$
## Definitions
Blockquotes with level 4 (####) headings inside get translated to definitions like:

> #### Vector
> A collection of numbers

---
<!-- header: 'Data' -->
Tables are also decorated in this theme!
| Left Row    | Center Row  | Right row     |
| :---        |    :----:   |          ---: |
| Some        |  Cool       | Data          |
| Some        |  Cool       | Data          |
| Some        |  Cool       | Data          |
| Some        |  Cool       | Data          |
---
<!-- header: 'Images' -->

![Photo by Joshua J. Cotten on Unsplash](https://images.unsplash.com/photo-1601247387326-f8bcb5a234d4?q=80&w=2071&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D) 

Images can be left-aligned, centered, and right-aligned!

---
<!-- header: 'HTML wonderland' -->
<!-- This slide only works with HTML enabled -->

<h2>These are all the HTML elements I've styled!</h2>

Text can be <mark>marked</mark>, <abbr title="abbreviated">abbr</abbr>, and be defined as <var>variable</var>. You can also do sample code outputs <samp>sample</samp> and quote things inline <q>like this</q>.

You can embed audio like:
<audio controls src="http://codeskulptor-demos.commondatastorage.googleapis.com/pang/pop.mp3" type="audio/mp3">
</audio> 
---
