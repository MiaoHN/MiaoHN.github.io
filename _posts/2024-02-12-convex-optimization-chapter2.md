---
title: "Notes | Convex Optimization | Chapter2 Convex sets"
date: 2024-02-12



categories: ["notes"]
math: true
tags: ["notes", "convex-optimization"]

---

## 2.1 Affine and convex sets

### 2.1.1 Lines and line segments

Suppose $x_1\neq x_2$ are two points in $\mathbf{R}^n$. Points of the form

$$
y=\theta x_1+(1-\theta)x_2
$$

where $\theta\in\mathbf{R}$, form the _line_ passing through $x_1$ and $x_2$. Expressing $y$ in the form

$$
y=x_2+\theta(x_1-x_2)
$$

gives another interpretation: $y$ is the sum of the base point $x_2$ (corresponding to $\theta=0$) and the direction $x_1−x_2$ (which points from $x_2$ to $x_1$) scaled by the parameter $\theta$.

### 2.1.2 Affine sets

A set $C\subseteq\mathbf{R}^n$ is _affine_ if the line through any two distinct points in $C$ lies in $C$. We refer to a point of the form $\theta_1x_1+\cdots+\theta_kx_k$, where $\theta_1+\cdots+\theta_k=1$, as an _affine combination_ of the points $x_1,\dots,x_k$.

> If $C$ is an affine set, $x_1,\dots,x_k\in C$, and $\theta_1+\cdots+\theta_k=1$, then the point $\theta_1x_1+\cdots+\theta_kx_k$ also belongs to $C$.
{: .prompt-info }

If $C$ is an affine set and $x_0\in C$, then the set

$$
V=C-x_0=\\{x-x_0|x\in C\\}
$$

is a subspace. Suppose $v_1,v_2\in V$ and $\alpha,\beta\in\mathbf{R}$. Then we have $v_1+x_0\in C$ and $v_2+x_0\in C$, and so

$$
\alpha v_1+\beta v_2+x_0=\alpha(v_1+x_0)+\beta(v_2+x_0)+(1-\alpha-\beta)x_0\in C
$$

since $C$ is affine, and $\alpha+\beta+(1-\alpha-\beta)=1$. we conclude that $\alpha v_1+\beta v_2\in V$, since $\alpha v_1+\beta v_2 + x_0\in C$.

Thus, the affine set $C$ can be expressed as

$$
C=V+x_0=\\{v+x_0|v\in V\\}
$$

as a subspace plus an offset.

> Every affine set can be expressed as the solution set of a system of linear equation. (See **Example2.1**)
{: .prompt-tip }

The set of all affine combinations of points in some set $C\subseteq\mathbf{R}^n$ is called the _affine hull_ if $C$, and denoted $\mathbf{aff}C$:

$$
\mathbf{aff}C=\\{\theta_1x_1+\cdots+\theta_kx_k|x_1,\dots,x_k\in C,\theta_1+\cdots+\theta_k=1\\}
$$

> The affine hull is smallest affine set that contains $C$, in the following sense: if $S$ is any affine set with $C\subseteq S$, then $\mathbf{aff}C\subseteq S$
{: .prompt-info }

### 2.1.3 Affine dimension and relative interior

We define the _affine dimension_ of a set $C$ as the dimension of its affine hull.

If the affine dimension of a set $C\subseteq\mathbf{R}^n$ is less than $n$, then the set lies in the affine set $\mathbf{aff}C\neq\mathbf{R}^n$. We define the _relative interior_ of the set $C$, denoted $\mathbf{relint}C$ , as its interior relative to $\mathbf{aff}C$:

$$
\mathbf{relint}C=\\{x\in C|B(x,r)\cap\mathbf{aff}C\subseteq C \text{ for some } r>0\\}
$$

where $B(x,r)=\\{y|\lVert y-x\rVert\leq r\\}$, the ball of radius $r$ and center $x$ in the norm $\lVert\cdot\rVert$. We can then define the _relative boundary_ of a set $C$ as $\mathbf{cl}C\backslash\mathbf{relint}C$, where $\mathbf{cl}C$ is the closure of $C$.

### 2.1.4 Convex sets

A set $C$ is _convex_ if the line segment between any two point in $C$ lies in $C$. If for any $x_1,x_2\in C$ and any $\theta$ with $0\leq\theta\leq1$, we have

$$
\theta x_1+(1-\theta)x_2\in C
$$

We call a point of the form $\theta_1x_1+\cdots+\theta_kx_k$, where $\theta_1+\cdots+\theta_k=1$ and $\theta_i\geq0,i=1,\dots,k$, a _convex combination_ of the points $x_1,\dots,x_k$. A convex combination of points can be thought of as a _mixture or weighted average_ of the points, with $\theta_i$ the fraction of $x_i$ in the mixture.

The _convex hull_ of a set $C$, denoted $\mathbf{conv}C$, is the set of all convex combinations of points in $C$:

$$
\mathbf{conv}C=\\{\theta_1x_1+\cdots+\theta_kx_k|x_i\in C,\theta_i\geq 0,i=1,\dots,k,\theta_1+\cdots+\theta_k=1\\}
$$

> If $B$ is any convex set that contains $C$, then $\mathbf{conv}C\subseteq B$
{: .prompt-tip }

### 2.1.5 Cones

A set $C$ is called a _cone_, or _nonnegative homogeneous_, if for every $x\in C$ and $\theta\geq 0$ we have $\theta x\in C$. A set $C$ is a _convex cone_ if it is convex and a cone, which means that for any $x_1, x_2\in C$ and $\theta_1, \theta_2\geq0$, we have

$$
\theta_1x_1+\theta_2x_2\in C
$$

A point of the form $\theta_1x_1+\cdots+\theta_kx_k$ with $\theta_1,\dots,\theta_k\geq0$ is called a _conic combination_ (or a _nonnegative linear combination_) of $x_1,\dots,x_k$.

> A set $C$ is a convex cone if and only if it contains all conic combinations of its elements.
{: .prompt-tip }

The _conic hull_ of a set $C$ is the set of all conic combinations of points in $C$

$$
\\{\theta_1x_1\cdots+\theta_kx_k|x_i\in C,\theta_i\geq 0, i=1,\dots,k\\}
$$

which is also the smallest convex cone that contains $C$

## 2.2 Some important examples

- The empty set $\emptyset$, any single point (i.e., singleton) $\\{x_0\\}$, and the whole space $\mathbf{R}^n$ are affine (hence, convex) subsets of $\mathbf{R}^n$.
- Any line is affine. If it passes through zero, it is a subspace, hence also a convex cone.
- A line segment is convex, but not affine (unless it reduces to a point).
- A _ray_, which has the form $\\{x_0 +\theta_v|\theta\geq0\\}$, where $v\neq0$, is convex, but not affine. It is a convex cone if its base $x_0$ is 0.
- Any subspace is affine, and a convex cone (hence convex)

### 2.2.1 Hyperplanes and halfspaces

A _hyperplane_ is as set of the form

$$
\\{x|a^Tx=b\\}
$$

where $a\in\mathbf{R}^n, a\neq0$, and $b\in\mathbf{R}$.

A hyperplane divides $\mathbf{R}^n$ into two _halfspaces_. A (closed) halfspace is a set of the form

$$
\\{x|a^Tx\leq b\\}
$$

where $a\neq 0$, the solution set of one (nontrivial) linear inequality.

> Halfspaces are convex, but not affine.
{: .prompt-info }

The halfspace can also be expressed as

$$
\\{x|a^T(x-x_0)\leq 0\\}
$$

where $x_0$ is any point on the associated hyperplane, satisfies $a^Tx_0=b$.

The boundary of the halfspace is the hyperplane $\\{x|a^Tx=b\\}$. The set $\\{x|a^Tx<b\\}$, which is the interior of the halfspace $\\{x|a^Tx\leq b\\}$, is called an _open halfspace_.

### 2.2.2 Euclidean balls and ellipsoids

A (_Euclidean_) _ball_ (or just ball) in $\mathbf{R}^n$ has the form

$$
B(x_c,r)=\\{x|\lVert x-x_c\rVert_2\leq r\\}=\\{x|(x-x_c)^T(x-x_c)\leq r^2\\}
$$

where $r>0$, and $\lVert\cdot\rVert_2$ denotes the Euclidean norm. The vector $x_c$ is the _center_ of the ball and the scalar $r$ is its _radius_; $B(x_c,r)$ consists of all points within a distance _r_ of center $x_c$. Another common representation for the Euclidean ball is

$$
B(x_c, r)=\\{x_c+ru|\lVert u\rVert_2\leq 1\\}
$$

A related family of convex sets is the _ellipsoids_, which have the form

$$
\varepsilon=\\{x|(x-x_c)^TP^{-1}(x-x_c)\leq 1\\}
$$

where $P=P^T\succ0$, $P$ is symmetric and positive definite. the vector $x_c\in\mathbf{R}^n$ is the _center_ of the ellipsoid. The matrix $P$ determines how far the ellipsoid extends in every direction from $x_c$; the lengths of the semi-axes of $\varepsilon$ are give by $\sqrt{\lambda_i}$, where $\lambda_i$ are the eigenvalue of $P$. A ball is an ellipsoid with $P=r^2I$.

Another common representation of an ellipsoid is

$$
\varepsilon=\\{x_c+Au|\lVert u\rVert_2\leq1\\}
$$

where A is square and nonsingular. In this representation we can assume without loss of generality that $A$ is symmetric and positive definite. When the matrix $A$ is symmetric positive semidefinite but singular, the set is called a _degenerate ellipsoid_; its affine dimension is equa to the rank of $A$. Degenerate ellipsoids are also convex.

### 2.2.3 Norm balls and norm cones

From the general properties of norms it can be shown that a _norm ball_ of radius $r$ and center $x_c$, given by $\\{x|\lVert x-x_c\rVert\leq r\\}$ is convex. The _norm cone_ associated with the norm $\lVert\cdot\rVert$ is the set

$$
C=\\{(x,t)|\lVert x\rVert\leq t\\}\subseteq\mathbf{R}^{n+1}
$$

It is (as the name suggests) a convex cone.

The _second-order cone_ is the norm cone for the Euclidean norm,

$$
\begin{aligned}
C=&\\{(x,t)\in\mathbf{R}^{n+1}|\lVert x\rVert_2\leq t\\}\\\\
=&\left\\{\begin{bmatrix}x\\\\t\end{bmatrix}\bigg|\begin{bmatrix}x\\\\t\end{bmatrix}^T\begin{bmatrix}I&0\\\\0&-1\end{bmatrix}\begin{bmatrix}x\\\\t\end{bmatrix}\leq 0,t\geq 0\right\\}
\end{aligned}
$$

The second-order cone is also known by several other names. It is called the _quadratic cone_, since it is defined by a quadratic inequality. It is also called the _Lorentz cone_ or _ice-cream cone_.

### 2.2.4 Polyhedra

A _polyhedron_ is defined as the solution set of a finite number of line equalities and inequalities:

$$
\mathcal{P}=\\{x|a_j^Tx\leq b_j,j=1,\dots,m,c_j^Tx=d_j,j=1,\dots,p\\}
$$

A polyhedron is thus the intersection of a finite number of halfspaces and hyperplanes.

> Affine sets (e.g., subspaces, hyperplanes, lines), rays, line segments, and halfspaces are all polyhedra.
{: .prompt-tip }

A bounded polyhedron is sometimes called a _polytope_, but sume authors use the opposite convention (i.e., polytope for any set of the preceding form, and polyhedron when it is bounded).

It will be convenient to use the compact notation

$$
\mathcal{P}=\\{x|Ax\preceq b,Cx=d\\}
$$

where

$$
A=\begin{bmatrix}a_1^T\\\\\vdots\\\\a_m^T\end{bmatrix},\quad C=\begin{bmatrix}c_1^T\\\\\vdots\\\\c_p^T\end{bmatrix}
$$

and the symbol $\preceq$ denotes _vector inequality_ or _componentwise inequality_ in $\mathbf{R}^{m}$: $u\preceq v$ means $u_i\leq v_i$ for $i=1,\dots,m$.

**Simplexes**:

_Simplexes_ is another important family of polyhedra. Suppose the $k+1$ points $v_0,\dots,v_k\in\mathbf{R}^n$ are _affinely independent_, which means $v_1-v_0,\dots,v_k-v_0$ are linearly independent. The simplex determined by them is given by

$$
C=\mathbf{conv}\\{v_0,\dots,v_k\\}=\\{\theta_0v_0+\cdots+\theta_kv_k|\theta\preceq0,\mathbf{1}^T\theta=1\\}
$$

The affine dimension of this simplex is $k$, so it is sometimes referred to as a k-dimensional simplex in $\mathbf{R}^n$.

_Some common simplexes_. A 1-dimensional simplex is a line segment; a 2-dimensional simplex is a triangle (including its interior); and a 3-dimensional simplex is a tetrahedron.

**Convex hull description of polyhedra**:

The convex hull of the finite set $\\{v_1,\dots,v_k\\}$ is

$$
\mathbf{conv}\\{v_1,\dots,v_k\\}=\\{\theta_1v_1+\cdots+\theta_kv_k|\theta\preceq 0,\mathbf{1}^T\theta=1\\}
$$

This set is a polyhedron, and bounded, but (except is special cases, e.g., a simplex) it is not simple to express it in the form by a set of linear equalities and inequalities.

### 2.2.5 The positive semidefinite cone

We use the notation $\mathbf{S}^n$ to denote the set of symmetric $n\times n$ matrices,

$$
\mathbf{S}^n=\\{X\in\mathbf{R}^{n\times n}|X=X^T\\}
$$

which is a vector space with dimension $n(n+1)/2$. We use the notation $\mathbf{S}_+^n$ to denote the set of symmetric positive semidefinite matrices:

$$
\mathbf{S}_+^n=\\{X\in\mathbf{S}^{n}|X\succeq 0\\}
$$

and the notation $\mathbf{S}_{++}^n$ to denote the set of symmetric positive definite matrices:

$$
\mathbf{S}_{++}^n=\\{X\in\mathbf{S}^{n}|X\succ 0\\}
$$

The set $\mathbf{S}_+^n$ is a convex cone: if $\theta _1,\theta _2\geq 0$ and $A,B\in\mathbf{S} _+^n$, then $\theta _1A+\theta _2B\in\mathbf{S} _+^n$. This can be seen directly from the definition of positive semidefiniteness: for any $x\in\mathbf{R} ^n$, we have

$$
x^T(\theta _1 A+\theta _2 B)x=\theta _1 x ^T Ax+\theta _2x^TBx\geq 0
$$

if $A\succeq0,B\succeq0$ and $\theta _1, \theta _2\geq 0$.

## 2.3 Operations that preserve convexity

### 2.3.1 Intersection

Convexity is preserved under intersection: if $S_1$ and $S_2$ are convex, then $S_1\cap S_2$ is convex. This property extends to the intersection of an infinite number of sets: if $S_\alpha$ is convex for every $\alpha\in\mathbf{A}$, then $\bigcap_{\alpha\in\mathbf{A}}S_\alpha$ is convex.

> _every_ closed convex set $S$ is a (usually infinite) intersection of halfspaces. In fact, a closed convex set $S$ is the intersection of all halfspaces that contain it:
>
> $$
> S=\bigcap\\{\mathcal{H}|\mathcal{H}\ \text{halfspace}, S\subseteq\mathcal{H}\\}
> $$
{: .prompt-tip }

### 2.3.2 Affine functions

Recall that a function $f:\mathbf{R}^n\leftarrow\mathbf{R}^m$ is affine if it is a sum of a linear function and a constant, i.e., if it has the form $f(x)=Ax+b$, where $A\in\mathbf{R}^{m\times n}$ and $b\in\mathbf{R}^m$. Suppose $S\subseteq\mathbf{R}^n$ is convex and $f:\mathbf{R}^n\leftarrow\mathbf{R}^m$ is an affine function. Then the image of $S$ under $f$,

$$
f(S)=\\{f(x)|x\in S\\}
$$

is convex.

### 2.3.3 Linear-fractional and perspective functions

**The perspective function**:

We define the _perspective function_ $P:\mathbf{R}^{n+1}\rightarrow\mathbf{R}^n$, with domain $\mathbf{dom}P=\mathbf{R}^n\times\mathbf{R}_{++}$, as $P(z,t)=z/t$. The perspective function scales or normalizes vectors so the last component is one, and then drops the last component

**Remark.** We can interpret the perspective function as the action of a _pin-hole_ camera.

**Linear-fractional functions**:

A _linear-fractional function_ is formed by composing the perspective function with an affine function. Suppose $g:\mathbf{R}^n\rightarrow\mathbf{R}^{m+1}$ is affine, i.e.,

$$
g(x)=\begin{bmatrix}A\\\\c^T\end{bmatrix}x+\begin{bmatrix}b\\\\d\end{bmatrix}
$$

where $A\in\mathbf{R}^{m\times n},b\in\mathbf{R}^m,c\in\mathbf{R}^n$, and $d\in\mathbf{R}$. The function $f:\mathbf{R}^n\rightarrow\mathbf{R}^m$ given by $f=P\circ g$, i.e.,

$$
f(x)=(Ax+b)/(c^Tx+d),\qquad\mathbf{dom}f=\\{x|c^Tx+d>0\\}
$$

is called a _linear-fractional_ (or _projective_) function. If $c=0$ and $d>0$, the domain of $f$ is $\mathbf{R}^n$, and $f$ is an affine function. So we can think of affine and linear functions as special cases of linear-fractional functions.

## 2.4 Generalized inequalities

### 2.4.1 Proper cones and generalized inequalities

A cone $K\subseteq\mathbf{R}^n$ is called a _proper cone_ if it satisfies the followings:

- $K$ is convex.
- $K$ is closed.
- $K$ is _solid_, which means it has nonempty interior.
- $K$ is _pointed_, which means that it contains no line (or equivalently, $x\in K,-x\in K\Rightarrow x=0$)

A proper cone $K$ can be used to define a _generalized inequality_, which is a partial ordering on $\mathbf{R}^n$ that has many of the properties of the standard ordering on $\mathbf{R}$.

_Cone of polynomials nonnegative on_ $[0,1]$. Let $K$ be defined as

$$
K=\\{c\in\mathbf{R}^n|c_1+c_2t+\cdots+c_nt^{n-1}\geq 0\text{ for } t\in[0,1]\\}
$$

i.e., $K$ is the cone of (coefficients of) polynomials of degree $n-1$ that are nonnegative on the interval $[0,1]$.

**Properties of generalized inequalities**:

A generalized inequality $\preceq _K$ satisfies many properties, such as

- $\preceq _K$ _is preserved under addition_: if $x\preceq _Ky$ and $y\preceq _Kv$, then $x+u\preceq _Ky+v$.
- $\preceq _K$ _is transitive_: if $x\preceq _Ky$ and $y\preceq _Kz$, then $x\preceq _Kz$.
- $\preceq _K$ _is preserved under nonnegative scaling_: if $x\preceq _Ky$ and $\alpha\geq 0$, then $\alpha x\preceq _K\alpha y$.
- $\preceq _K$ _is reflexive_: $x\preceq _Kx$.
- $\preceq _K$ _is antisymmetric_: if $x\preceq _Ky$ and $y\preceq _Kx$, then $x=y$.
- $\preceq _K$ _is preserved under limits_: if $x_i\preceq _Ky_i$ for $i=1,2,\dots,x_i\rightarrow x$ and $y_i\rightarrow y$ as $i\rightarrow\infin$, then $x\preceq _Ky$.

The correspoinding strict generalized inequality $\prec_K$ satisfies, for example,

- if $x\prec_Ky$ then $x\preceq_Ky$.
- if $x\prec_Ky$ and $u\preceq_Kv$ then $x+u\prec_Ky+v$.
- if $x\prec_Ky$ and $\alpha>0$ then $\alpha x\prec_K\alpha y$.
- $x\nprec_Kx$.
- if $x\prec_Ky$, then for $u$ and $v$ small enough, $x+u\preceq_Ky+v$.

### 2.4.2 Minimum and minimal elements

> The most difference is that $\leq$ on $\mathbf{R}$ is a _linear ordering_: any two points are _comparable_, meaning either $x\leq y$ or $y\leq x$. This property does not hold for other generalized inequalities. One implication is that **concepts like minimum and maximum are more complicated in the context of generalized inequalities**.
{: .prompt-info }

We can describe minimum and minimal elements using simple set notation. A point $x\in S$ is the minimum element of $S$ if and only if

$$
S\subseteq x+K
$$

Here $x+K$ denotes all the points that are comparable to $x$ and greater than or equal to $x$ (according to $\preceq_K$). A point $x\in S$ is a minimal element if and only if

$$
(x-K)\cap S=\\{x\\}
$$

Here $x-K$ denotes all the points that are comparable to $x$ and less than or equal to $x$ (according to $\preceq_K$); the only point in common with $S$ is $x$.

![example](/assets/images/2024-02-12-convex-optimization-chapter2/2024-02-09-09-56-50.png)

- _Left_. The set $S_1$ has a minimum element $x_1$ with respect to componentwise inequality in $\mathbf{R}^2$. The set $x_1+K$ is shaded lightly; $x_1$ is the minimum element of $S_1$ since $S_1\subseteq x_1+K$.
- _Right_. The point $x_2$ is a minimal point of $S_2$. The set $x_2−K$ is shown lightly shaded. The point $x_2$ is minimal because $x_2−K$ and $S_2$ intersect only at $x_2$.

## 2.5 Separating and supporting hyperplanes

### 2.5.1 Separating hyperplane theorem

_separating hyperplane theorem_: Suppose $C$ and $D$ are nonempty disjoint convex sets, i.e., $C\cap D=\emptyset$. Then there exist $a\neq0$ and $b$ such that $a^Tx\leq b$ for all $x\in C$ and $a^Tx\geq b$ for all $x\in D$.In other words, the affine function $a^Tx-b$ is nonpositive on $C$ and nonnegative on $D$. The hyperplane $\\{x|a^Tx=b\\}$ is call a _separating hyperplane_ for the sets $C$ and $D$, or is said to _separate_ the sets $C$ and $D$.

**Proof of separating hyperplane theorem**:

Here we consider a special case, we assume that the (Euclidean) _distance_ between $C$ and $D$, defined as

$$
\mathbf{dist}(C,D)=\text{inf}\\{\lVert u-v\rVert _2|u\in C,v\in D\\}
$$

is positive, and that there exist points $c\in C$ and $d\in D$ that achieve the minimum distance, i.e., $\lVert c-d\rVert _2=\mathbf{dist}(C,D)$. (These conditions are satisfied, for example, when $C$ and $D$ are closed and one set is bounded.)

Define

$$
a=d-c,\quad b=\frac{\lVert d\rVert _2^2-\lVert c\rVert _2^2}{2}
$$

We will show that the affine function

$$
f(x)=a^Tx-b=(d-c)^T(x-(1/2)(d+c))
$$

is nonpositive on $C$ and nonnegative on $D$, i.e., that the hyperplane $\\{x|a^Tx=b\\}$ separates $C$ and $D$.

This hyperplane is perpendicular to the line segment between $c$ and $d$, and passes through its midpoint.

![Fig. 1. separating hyperplane](/assets/images/2024-02-12-convex-optimization-chapter2/2024-02-10-09-38-15.png)

We first show that $f$ is nonnegative on $D$. The proof that $f$ is nonpositive on $C$ is similar (or follows by swapping $C$ and $D$ and considering $-f$). Suppose there were a point $u\in D$ for which

$$
f(u)=(d-c)^T(u-(1/2)(d+c))<0
$$

We can express $f(u)$ as

$$
f(u)=(d-c)^T(u-d+(1/2)(d-c))=(d-c)^T(u-d)+(1/2)\lVert d-c\rVert _2^2
$$

We see that $(d-c)^T(u-d)<0$. Now we observe that

$$
\frac{d}{dt}\lVert d+t(u-d)-c\rVert _2^2\bigg| _{t=0}=2(d-c)^T(u-d)<0
$$

so for some small $t>0$, with $t\leq 1$, we have

$$
\lVert d+t(u-d)-c\rVert _2<\lVert d-c\rVert _2
$$

i.e., the point $d+t(u-d)$ is closer to $c$ than $d$ is. Since $D$ is convex and contains $d$ and $u$, we have $d+t(u-d)\in D$. But this is impossible, since $d$ is assumed to be the point in $D$ that is closest to $C$.

**Strict separation**:

The separation hyperplane we constructed above satisfies the stronger condition that $a^Tx<b$ for all $x\in C$ and $a^Tx>b$ for all $x\in D$. This is called _strict separation_ of the sets $C$ and $D$.

**Converse separating hyperplane theorems**:

Any two convex sets $C$ and $D$, at least one of which is open, are disjoint if and only if there exists a separating hyperplane.

### 2.5.2 Supporting hyperplanes

Suppose $C\in\mathbf{R}^n$, and $x_0$ is a point in its boundary $\mathbf{bd}C$, i.e.,

$$
x_0\in\mathbf{bd}C=\mathbf{cl}C\backslash\mathbf{int}C
$$

If $a\neq 0$ satisfies $a^Tx\leq a^Tx_0$ for all $x\in C$, then the hyperplane $\\{x|a^Tx=a^Tx_0\\}$ is called a _supporting hyperplane_ to $C$ at the point $x_0$. This is equivalent to saying that the point $x_0$ and the set $C$ are separated by the hyperplane $\\{x|a^Tx=a^Tx_0\\}$. The geometric interpretation is that the hyperplane $\\{x|a^Tx=a^Tx_0\\}$ is tangent to $C$ at $x_0$, and the halfspace $\\{x|a^Tx\leq a^Tx_0\\}$ contains $C$.

![Fig. 2. The hyperplane $\{x|a^Tx=a^Tx_0\}$ supports $C$ at $x_0$](/assets/images/2024-02-12-convex-optimization-chapter2/2024-02-11-09-30-17.png)

A basic result, called the _supporting hyperplane theorem_, states that for any nonempty convex set $C$, and any $x_0\in\mathbf{bd}C$, there exists a supporting hyperplane to $C$ at $x_0$.

> There is also a partial converse of the supporting hyperplane theorem: If a set is closed, has nonempty interior, and has a supporting hyperplane at every point in its boundary, then it is convex.
{: .prompt-tip }

## 2.6 Dual cones and generalized inequalities

### 2.6.1 Dual cones

Let $K$ be a cone. The set

$$
K^*=\\{y|x^Ty\geq 0\text{ for all }x\in K\\}
$$

is called the _dual cone_ of $K$. As the name suggests, $K^*$ is a cone, and is always convex, even when the original cone $K$ is not.

Geometrically, $y\in K^*$ if and only if $-y$ is the normal of a hyperplane that supports $K$ at the origin.

![Fig. 3. Left: The halfspace with inward normal $y$ contains the cone $K$, so $y\in K^*$. Right: The halfspace with inward normal $z$ does not contain $K$, so $z\notin K^*$.](/assets/images/2024-02-12-convex-optimization-chapter2/2024-02-11-09-45-49.png)

_Subspace_. The dual cone of a subspace $V\subseteq\mathbf{R}^n$ (which is a cone) is its orthogonal complement $V^{\perp}=\\{y|v^Ty=0\text{ for all }v\in V\\}$.

_Nonnegative orthant_. The cone $\mathbf{R}_+^n$ is its own dual:

$$
x^Ty\geq 0\text{ for all } x\succeq 0 \Leftrightarrow y\succeq 0
$$

We call such a cone _self-dual_.

Duyal cones satisfy several properties, such as:

- $K^*$ is closed and convex.
- $K _1\subseteq K _2$ implies $K _2^\*\subseteq K _1^\*$.
- If $K$ has nonempty interior, then $K^*$ is pointed.
- If the closure of $K$ is pointed then $K^*$ has nonempty interior.
- $K^{\*\*}$ is the closure of the convex hull of $K$. (Hence if $K$ is convex and closed, $K^{\*\*}=K$.)

These properties show that if $K$ is a proper cone, then so is its dual $K^\*$, and moreover, that $K^{\*\*}=K$.

### 2.6.2 Dual generalized inequalities

Now suppose that the convex cone $K$ is proper, so it induces a generalized inequality $\preceq_K$. Then its dual cone $K^\*$ is also proper, and therefore induces a generalized inequality. We refer to the generalized inequality $\preceq_{K^\*}$ as the _dual_ of the generalized inequality $\preceq_K$.

Some important properties relating a generalized inequality and its dual are:

- $x\preceq_Ky$ if and only if $\lambda^Tv\leq\lambda^Ty$ for all $\lambda\succeq_{K^*}0$.
- $x\prec_Ky$ if and only if $\lambda^Tv<\lambda^Ty$ for all $\lambda\succeq_{K^*}0,\lambda\neq 0$.

Since $K=K^{\*\*}$, the dual generalized inequality associated with $\preceq_{K^\*}$ is $\preceq_{K}$, so these properties hold if the generalized inequality and its dual are swapped. As a specific example, we have $\lambda\preceq_{K\*}\mu$ if and only if $\lambda^Tx\leq\mu^Tx$ for all $x\succeq_K 0$.

### 2.6.3 Minimum and minimal elements via dual inequalities

**Dual characterization of minimum element**:

We first consider a characterization of the _minimum_ element: $x$ is the minimum element of $S$, with respect to the generalized inequality $\succeq_K$, if and only if for all $\lambda\prec_{K\*}0$,$x$ is the unique minimizer of $\lambda^Tx$ over $z\in S$. Geometrically, this means that for any $\lambda\prec_{K\*}0$, the hyperplane

$$
\\{z|\lambda^T(z-x)=0\\}
$$

is a strict support hyperplane to $S$ at $x$. Note that convexity of the set $S$ is _not_ required.

![Fig. 4. Dual characterization of minimum element.](/assets/images/2024-02-12-convex-optimization-chapter2/2024-02-12-17-22-32.png)

**Dual characterization of minimal element**:

We now turn to a similar characterization of _minimal elements_. Here there is a gap between the necessary and sufficient conditions. If $\lambda\prec_{K\*}0$ and $x$ minimizes $\lambda^Tz$ over $z\in S$, then $x$ is minimal.

![Fig. 5. A set $S\in\mathbf{R}^2$.](/assets/images/2024-02-12-convex-optimization-chapter2/2024-02-12-17-32-09.png)

The converse is in general false: a point $x$ can be minimal in $S$, but not a minimizer of $\lambda^Tz$ over $z\in S$, for any $\lambda$.

![Fig. 6. The point $x$ is a minimal element but not minimum.](/assets/images/2024-02-12-convex-optimization-chapter2/2024-02-12-17-33-59.png)

Provided the set $S$ is convex, we can say that for any minimal element $x$ there exists a nonzero $\lambda\succeq_{K^\*}0$ such that $x$ minimizes $\lambda^Tz$ over $z\in S$.

> This converse theorem cannot be strengthened to $\lambda\succ_{K\*}0$.
>
> ![Fig. 7. Left. Minimal but not minimum. Right. minimum but not minimal.](/assets/images/2024-02-12-convex-optimization-chapter2/2024-02-12-17-40-55.png)
{: .prompt-warning }
