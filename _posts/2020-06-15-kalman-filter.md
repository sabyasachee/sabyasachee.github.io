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
  - name: Proof

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

## Proof

We find $$\prex$$ using the Kalman Filter state equation.

$$
\begin{aligned}
    \prex &= E[x_{k+1} | Z_k] \\
    &= E[\F \x + \G u_k + w_k | Z_k] \qquad \text{(using Eq \ref{eq:state})} \\
    &= \F E[\x | Z_k] + \G u_k + E[w_k | Z_k] \qquad \text{($$u_k$$ is not random)} \\
    &= \F E[x_k | Z_k] + \G u_k \qquad (E[w_k] = 0) \\
    &= \F \hat{x}_{k|k} + \G u_k \qquad \text{(definition of $$\hat{x}_{k|k}$$)} \label{eq:prex}
\end{aligned}
$$

We substitute $$\prex$$ in the definition of $$\preP$$ using equation \ref{eq:prex}.

$$
\begin{aligned}
    \preP &= \cvar{\xx - \prex}{Z_k} \\
    &= \cvar{\F \x + \G u_k + w_k - \F \hat{x}_{k|k} - \G u_k}{Z_k} \\
    &\qquad \text{(substituting $$\xx$$ and $$\prex$$ using Eq \ref{eq:state} and Eq \ref{eq:prex} resp.)} \\
    &= \cvar{\F (\x - \hat{x}_{k|k}) + w_k}{Z_k}
\end{aligned}
$$

$$\hat{x}_{k|k} = g(Z_k)$$ for some function $$g$$, and $$w_k$$ is uncorrelated with $$Z_k$$ and $$x_k$$. 
Therefore $$w_k$$ is uncorrelated with $$\F (\x - \hat{x}_{k|k})$$.
$$E[w_k] = 0$$.
Therefore, we can use Lemma 1.

$$
\begin{aligned}
    \quad \preP &= \cvar{\F (\x - \hat{x}_{k|k}) + w_k}{Z_k} \\
    &= \F \cvar{\x - \hat{x}_{k|k}}{Z_k} \F^T + E[w_k w_k^T | Z_k] \quad \text{(using Lemma 1)} \\
    &= \F P_{k|k} \F^T + \Q \qquad \text{(definition of $$P_{k|k}$$ and $$\Q$$)}
\end{aligned}
$$

Kalman filter is an unbiased linear optimal estimator. 
\begin{enumerate}
    \item \textbf{Unbiased} 
    The expected value of an unbiased estimator equals the expected value of the parameter.
    Therefore, $$E[\postx] = E[\xx]$$ and $$E[\hat{x}_{k|k}] = E[\x]$$.
    
    \item \textbf{Linear}
    A linear estimator means it is a linear combination of the observations, which in this case is $$Z_{k+1}$$.
    $$\prex$$ is a function of $$Z_k$$. 
    Therefore let $$\postx = \KK\prex + \K \zz$$, for some matrix $$\KK$$ and $$\K$$.
    
    \item \textbf{Optimal}
    The estimator minimizes the mean squared error. 
    Therefore, $$\postx = argmin \; E[(\xx - \postx)^T(\xx - \postx) | \ZZ]$$.
\end{enumerate}

We use these three conditions to find $$\K$$ and $$\KK$$.

$$
\begin{aligned}
    E[\xx] &= E[\postx] \qquad \text{($$\postx$$ is unbiased)} \\
    &= E[\KK\prex + \K \zz] \qquad \text{($$\postx$$ is a linear estimator)} \\
    &= \KK E[\F \hat{x}_{k|k} + \G u_k] + \K E[\H \xx + v_{k+1}] \\
    &\text{(substituting $$\zz$$ and $$\prex$$ using Eq \ref{eq:sensor} and Eq \ref{eq:prex} resp.)} \\
    &= \KK (\F E[\hat{x}_{k|k}] + \G u_k) + \K \H E[\xx] \qquad (E[v_{k+1}] = 0) \\
    &= \KK (\F E[\x] + \G u_k) + \K \H E[\xx]  \qquad \text{($$\hat{x}_{k|k}$$ is unbiased)} \\
    &= \KK E[\F x_k + \G u_k + w_k] + \K \H E[\xx] \qquad (E[w_k] = 0) \\
    &= \KK E[\xx] + \K \H E[\xx] \qquad \text{(using Eq \ref{eq:state})} \\
    &= (\KK + \K \H) E[\xx] \\
    \quad I &= \KK + \K \H \\
    \KK &= I \minus \K \H \label{eq:kdash}
\end{aligned}
$$

Substituting $$\KK$$ in the expression of $$\postx$$,

$$
\begin{aligned}
    \postx &= \KK \prex + \K \zz \\
    &= (I \minus \K \H) \prex + \K \zz \qquad \text{(using Eq \ref{eq:kdash})} \\
    &= \prex + \K (\zz \minus \H \prex) \label{eq:postx}
\end{aligned}
$$

We substitute $$\postx$$ in the definition of $$\postP$$ using equation \ref{eq:postx},

$$
\begin{aligned}
    \postP &= \cvar{\xx - \postx}{Z_{k+1}} \\
    &= \cvarbreaktwo{\xx - \prex - \K (\zz - \H \prex)}{\ZZ} \\
    &\qquad \text{(substituting $$\xx$$ using Eq \ref{eq:postx})} \\
    &= \cvarbreaktwo{\xx - \prex - \K (\H \xx + v_{k+1} - \H \prex)}{\ZZ} \\
    &\qquad \text{(substituting $$\zz$$ using Eq \ref{eq:sensor})} \\
    &= \cvarbreak{(I - \K \H) (\xx - \prex) - \K v_{k+1}}{\ZZ}
\end{aligned}
$$

$$\postx = g(Z_{k+1})$$ for some function $$g$$, and $$v_{k+1}$$ is uncorrelated with $$Z_{k+1}$$ and $$x_{k+1}$$. 
Therefore $$\K w_{k+1}$$ is uncorrelated with $$(I - \K \H) (\xx - \prex)$$.
$$E[v_{k+1}] = 0$$.
Therefore we can use Lemma 1.

$$
\begin{aligned}
    \postP &= \cvarbreak{(I - \K \H) (\xx - \prex) - \K v_{k+1}}{\ZZ} \\
    &= (I - \K \H) \cvar{\xx - \prex}{\ZZ} \\
    &\quad\quad (I - \K \H)^T + \K E[v_{k+1} v_{k+1}^T | \ZZ] \K^T \qquad \text{(using Lemma 1)} \\
    &= (I - \K \H) \preP (I - \K \H)^T + \K \R \K^T \\
    &\qquad \text{(definition of $$\preP$$ and $$\R$$)} \label{eq:postP}
\end{aligned}
$$

We find $$\K$$ by minimizing the conditional mean squared error of $$\postx$$, $$L = E[(\xx - \postx)^T(\xx - \postx)|\ZZ]$$.
We express $$L$$ as a trace of $$\postP$$, and use equation \ref{eq:postP} to write it in terms of $$\K$$.
We then minimize $$L$$ and find $$\K$$.

$$
\begin{aligned}
    L &= E[(\xx - \postx)^T(\xx - \postx) | \ZZ] \\
    &= \mathbf{Tr}(\cvar{\xx - \postx}{\ZZ}) \\
    &= \mathbf{Tr}(\postP) \qquad \text{(definition of $$\postP$$)} \\
    &= \mathbf{Tr}((I - \K \H) \preP (I - \K \H)^T + \K \R K^T) \quad \text{(using Eq \ref{eq:postP})} \\
    &= \mathbf{Tr}(\; \preP  - \preP \H^T \K^T - \K \H \preP \\
    &\qquad + \K \H \preP \H^T \K^T + \K \R \K^T) \\
    &= \mathbf{Tr}(\preP) \minus \mathbf{Tr}(\preP \H^T \K^T) \minus \mathbf{Tr}(\K \H \preP) \\
    &\qquad + \mathbf{Tr}(\; \K (\H \preP \H^T + \R) \K^T) \qquad \text{($$\mathbf{Tr}$$ is a linear operator)} \\
    &= \mathbf{Tr}(\preP) \minus \mathbf{Tr}(\K \H \preP) \minus \mathbf{Tr}(\K \H \preP) \\
    &\qquad + \mathbf{Tr}(\; \K (\H \preP \H^T + \R) \K^T) \qquad (\mathbf{Tr}(A) = \mathbf{Tr}(A^T)) \\
    &= \mathbf{Tr}(\preP) - 2 \; \mathbf{Tr}(\K \H \preP) + \mathbf{Tr}(\; \K (\H \preP \H^T + \R) \K^T) \label{eq:loss}
\end{aligned}
$$

Taking the gradient of $$L$$ with respect to $$\K$$ and setting it equal to 0,

$$
\begin{aligned}
    \nabla_{\K} L &= 0 \\
    0 &= \nabla_{\K}\; (\mathbf{Tr}(\K (\H \preP \H^T + \R) \K^T) \\
    &\qquad \minus 2\; \mathbf{Tr}(\K \H \preP) + \mathbf{Tr}(\preP)) \qquad \text{(using Eq \ref{eq:loss})} \\
    &= \nabla_{\K}\; (\mathbf{Tr}(\K (\H \preP \H^T + \R) \K^T) \minus 2\; \mathbf{Tr}(\K \H \preP)) \\
    &= 2\;\K (\H \preP \H^T + \R) \minus 2\; \preP \H^T \quad \text{(using Lemma 2 and 3)} \\
    \therefore\quad \K &= \preP \H^T (\H \preP \H^T + \R)^{-1} \label{eq:kgain}
\end{aligned}
$$

We can simplify the expression of $$\postP$$ in equation \ref{eq:postP} using equation \ref{eq:kgain}.

$$
\begin{aligned}
    \postP &= (I - \K \H) \preP (I - \K \H)^T + \K \R \K^T \\
    &= \preP \minus \preP \H^T \K^T \minus \K \H \preP \\
    &\quad + \K \H \preP \H^T \K^T + \K \R \K^T \\
    &= \preP \minus \preP \H^T \K^T \minus \K \H \preP \\
    &\quad + \K (\H \preP \H^T + \R) \K^T \\
    &= \preP \minus \preP \H^T \K^T \minus \K \H \preP \\
    &\quad + \preP \H^T (\H \preP \H^T + \R)^{-1} (\H \preP \H^T + \R) \K^T \\
    &\qquad \text{(substituing $$\K$$ using Eq \ref{eq:kgain})} \\
    &= \preP \minus \preP \H^T \K^T \minus \K \H \preP + \preP \H^T \K^T \\
    &= \preP \minus \K \H \preP \\
    &= (I - \K \H) \preP
\end{aligned}
$$

Arranging all results together,

$$
\begin{aligned}
    \prex &= \F \hat{x}_{k|k} + \G u_k  \\
    \preP &= \F P_{k|k} \F^T + \Q  \\
    \K &= \preP \H^T (\H \preP \H^T + \R)^{-1} \\
    \postx &= \prex + \K (\zz \minus \H \prex) \\
    \postP &= (I \minus \K \H) \preP
\end{aligned}
$$