---
layout: distill
title: Kalman Filter
description: Proof of Kalman Filter
tags: kalman-filter
giscus_comments: false
date: 2020-06-15
featured: false

toc:
  - name: Linear System
  - name: Notations
  - name: Assumptions
  - name: Kalman Filter
  - name: Lemmas

authors:
  - name: Sabyasachee Baruah
    affiliations:
      name: University of Southern California
---

<div style="display:none">
$$
\usepackage{amsmath}
\usepackage{amssymb}
\newcommand{\x}{x_{k}}
\newcommand{\xx}{x_{k+1}}
\newcommand{\zz}{z_{k+1}}
\newcommand{\Q}{Q_{k}}
\newcommand{\R}{R_{k+1}}
\newcommand{\F}{F_{k}}
\newcommand{\G}{G_{k}}
\newcommand{\H}{H_{k+1}}
\newcommand{\postx}{\hat{x}_{k+1|k+1}}
\newcommand{\prex}{\hat{x}_{k+1|k}}
\newcommand{\postP}{P_{k+1|k+1}}
\newcommand{\preP}{P_{k+1|k}}
\newcommand{\Z}{Z_{k}}
\newcommand{\ZZ}{Z_{k+1}}
\newcommand{\K}{K_{k+1}}
\newcommand{\KK}{K'_{k+1}}
\newcommand{\var}[1]{E[(#1)(#1)^T]}
\newcommand{\cvar}[2]{E[(#1)(#1)^T|#2]}
\newcommand{\aequal}{\quad &= \quad}
\newcommand{\equal}{\quad = \quad}
\newcommand{\plus}{\; + \;}
\newcommand{\minus}{\; - \;}
\newcommand{\argmin}[1]{\underset{#1}{\textit{argmin}}\quad}
\newcommand{\part}{\frac{\partial}{\partial a_{ij}}}
$$
</div>

## Linear System

The figure below shows a discrete-time linear system.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/posts/2020-06-15-kalman-filter/linear-system.jpg" 
        class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Discrete-time linear system
</div>

$$k$$ represents the discrete time instants. 
$$\x$$, $$u_k$$ and $$z_k$$ represent the state of the system, input to the system and measurement of the system at time 
$$k$$ respectively.
The system is governed by the following equations -

$$
\begin{aligned}
\xx &= \F \x \plus \G u_k \plus w_k \\
\zz &= \H \xx \plus v_{k+1}
\end{aligned}
$$

$$w_k$$ is the modeling noise of the state and $$v_k$$ is the measurement noise of the sensors.
$$x$$, $$z$$, $$w$$ and $$v$$ are random variables, and $$u$$, $$F$$, $$G$$ and $$H$$ are deterministic.
The task of filtering is to obtain an estimate of state $$\x$$, given observations $$z_1, z_2, ..., z_k$$. 
The Kalman filter is a linear unbiased estimator which minimizes the mean squared error.
We present the proof of Kalman filter here for a discrete-time linear system.

## Notations

We use the following notations:

$$
\begin{aligned}
Z_k &= z_1, z_2, ..., z_k\\
\hat{x}_{k|j} &= E[x_k | Z_j]\\
P_{k|j} &= Var[x_k|Z_j] = \cvar{x_k - \hat{x}_{k|j}}{Z_j}
\end{aligned}
$$

## Assumptions

We assume the following:

1.  $$w_k$$ and $$v_k$$ are zero-mean white noise.

    $$
    \begin{aligned}
        E[w_k] &= \mathbf{0}\\
        E[v_k] &= \mathbf{0}\\
        Cov[w_k,w_l] = E[w_k w_l^T] &=
            \begin{cases}
                \Q & k = l \\
                \mathbf{0} & k \neq l
            \end{cases}\\
        Cov[v_k,v_l] = E[v_k v_l^T] &=
            \begin{cases}
                R_k & k = l \\
                \mathbf{0} & k \neq l
            \end{cases}
    \end{aligned}
    $$

    $$\Q$$ and $$R_k$$ are symmetric positive-definite matrices.
    
2.  $$w_k$$ and $$v_k$$ are uncorrelated with $$\x$$ and $$Z_k$$.

3.  $$E[x_0] = x_{0|0}$$ and $$Var[x_0] = \var{x_0 - x_{0|0}} = P_{0|0}$$ are the given initial state conditions. 
    $$x_0$$ is uncorrelated with $$w_0$$ and $$v_0$$.

## Kalman Filter

Kalman filter gives a recursive formula for the estimates in prediction and update steps.

**Prediction**

$$
\begin{aligned}
    \prex &= \F \hat{x}_{k|k} + \G u_k \\
    \preP &= \F P_{k|k} \F^T + \Q
\end{aligned}
$$

**Update**

$$
\begin{aligned}
    \K &= \preP \H^T (\H \preP \H^T + \R)^{-1}\\
    \postx &= \prex + \K (\zz - \H \prex)\\
    \postP &= (I - \K \H) \preP
\end{aligned}
$$

The Kalman filter propagates the conditional mean and variance of the state, by expressing $$\postx$$ and $$\postP$$ 
in terms of $$\hat{x}_{k|k}$$ and $$P_{k|k}$$.

$$\prex$$ is the prior estimate of $$\xx$$ before observing $$\zz$$, and $$\postx$$ is the posterior estimate of 
$$\xx$$ after the measurement $$\zz$$ of the state.
Similarly $$\preP$$ and $$\postP$$ are the prior and posterior estimates of the variance of the state.

$$\K$$ is called the Kalman Gain at time $$k + 1$$, because it weighs the information gained from the measurement 
$$\zz$$ by taking its difference with the predicted measurement $$\H \prex$$.

We prove three lemmas in the next section, two of which involve taking the gradient of trace operations and the other 
concerns with uncorrelated random variables. 
We use those to prove the Kalman Filter equations in section.

## Lemmas

### Lemma 1

$$E[(X + Y)(X + Y)^T] = E[XX^T] + E[YY^T]$$, if $$X$$ and $$Y$$ are uncorrelated, and one of $$E[X]$$ or $$E[Y]$$ is 0.

**Proof** 

$$X$$ and $$Y$$ are uncorrelated, therefore $$E[XY^T] = E[X]E[Y^T]$$.
One of $$E[X]$$ or $$E[Y]$$ equals 0, therefore $$E[XY^T] = 0$$.

Similarly, $$E[YX^T] = 0$$.

Expanding $$E[(X + Y)(X + Y)^T]$$,

$$
\begin{aligned}
    E[(X + Y)(X + Y)^T] &= E[XX^T] + E[YY^T] + E[XY^T] + E[YX^T] \\
    &= E[XX^T] + E[YY^T] \qquad (E[XY^T] = E[YX^T] = 0)
\end{aligned}
$$

Therefore $$E[(X + Y)(X + Y)^T] = E[XX^T] + E[YY^T]$$.

### Lemma 2

$$\nabla_A \mathbf{Tr}(AB) = B^T$$

**Proof**

Let $$A = [a]_{n \times p}$$ and $$B = [b]_{p \times n}$$.

$$
\begin{aligned}
    \mathbf{Tr}(AB) &= \sum_{k=1}^{n} (AB)_{kk} = \sum_{k=1}^{n} \sum_{l=1}^{p} a_{kl} b_{lk}
\end{aligned}
$$

Taking gradient with respect to $$a_{ij}$$,

$$
\begin{aligned}
    \part \mathbf{Tr}(AB) &= \part \sum_{k=1}^{n} \sum_{l=1}^{p} a_{kl} b_{lk} 
                          = \sum_{k=1}^{n} \sum_{l=1}^{p} \part a_{kl} b_{lk} \\
                          &= \part a_{ij} b_{ji} = b_{ji}
\end{aligned}
$$

Therefore $$\nabla_A \mathbf{Tr}(AB) = B^T$$.

### Lemma 3

$$\nabla_A \mathbf{Tr}(ABA^T) = A(B + B^T) \overset{B = B^T}{=\joinrel=} 2AB$$

**Proof**

Let $$A = [a]_{n \times p}$$ and $$B = [b]_{p \times p}$$.

$$
\begin{aligned}
    \mathbf{Tr}(ABA^T)  &= \sum_{k=1}^{n} (ABA^T)_{kk} \\
                        &= \sum_{k=1}^{n} \sum_{l=1}^{p} a_{kl} (BA^T)_{lk}\\
                        &= \sum_{k=1}^{n} \sum_{l=1}^{p} a_{kl} \sum_{r=1}^{p} b_{lr} (A^T)_{rk}\\
                        &= \sum_{k=1}^{n} \sum_{l=1}^p \sum_{r=1}^p a_{kl} b_{lr} a_{kr}
\end{aligned}
$$

Taking gradient with respect to $$a_{ij}$$,

$$
\begin{aligned}
    \part \mathbf{Tr} (ABA^T) &= \part \sum_{k=1}^{n} \sum_{l=1}^p \sum_{r=1}^p a_{kl} b_{lr} a_{kr} \\
                              &= \sum_{k=1}^{n} \sum_{l=1}^p \sum_{r=1}^p \part a_{kl} b_{lr} a_{kr} \\
                              &= \sum_{l=1}^p \sum_{r=1}^p \part a_{il} b_{lr} a_{ir} \\
                              &= \part a_{ij}^2 b_{jj} + \sum_{l=1, l \neq j}^p \part a_{il} b_{lj} a_{ij}
                                  + \sum_{r=1, r \neq j}^p \part a_{ij} b_{jr} a_{ir} \\
                              &= 2 a_{ij} b_{ij} + \sum_{l=1, l \neq j}^p a_{il}b_{lj} 
                                  + \sum_{r=1,r \neq j}^p b_{jr} a_{ir} \\
                              &= \sum_{l=1}^p a_{il} b_{lj} + \sum_{r=1}^p a_{ir}b_{jr}
\end{aligned}
$$

Therefore,

$$
\begin{aligned}
    \nabla_A \mathbf{Tr} (ABA^T)  &= AB + AB^T \\
                                  &= 2AB \quad \text{if } B = B^T
\end{aligned}
$$

