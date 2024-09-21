---
title: "Notes | Convex Optimization | Chapter1 Introduction"
date: 2024-02-06



categories: ["Notes", "Convex Optimization"]
math: true
tags: ["notes", "convex-optimization"]

---

## 1.1 Mathematical optimization

A _mathematical optimization problem_, or just _optimization problem_, has the form

$$
\begin{aligned}
&\text{minimize}\quad f_0(x)\\\\
&\text{subject to}\quad f_i(x)\leq b_i,\quad i=1,\dots,m.
\end{aligned}
$$

Here the vector $x=(x_1,\dots,x_n)$ is the _optimization variable_ of the problem, the function $f_0:R^n\rightarrow R$ is the _objective_ function, the functions $f_i:R^n\rightarrow R,i=1,\dots,m$, are the (inequality) _constraint functions_, and the constraints $b_1,\dots,b_m$ are the limits, or bounds, for the constraints.

A _convex optimization_ problem is one in which the objective and constraint functions are convex, which means they satisfy the inequality

$$
f_i(\alpha x+\beta y)\leq\alpha f_i(x)+\beta f_i(y)
$$

for all $x,y\in R^n$ and all $\alpha, \beta\in R$ with $\alpha+\beta=1,\alpha\geq 0,\beta\geq 0$.

### 1.1.1 Applications

- portfolio optimization
- device sizing
- data fitting
- embedded optimization
- ...

For most of these applications, mathematical optimization is used as an aid to a human decision maker, system designer, or system operator, who supervises the process, checks the results, and modifies the problem (or the solution approach) when necessary. This human decision maker also carries out any actions suggested by the optimization problem, e.g., buying or selling assets to achieve the optimal portfolio.

### 1.1.2 Solving optimization problems

## 1.2 Least-squares and linear programming

### 1.2.1 Least-squares problems

A _least-squares_ problem is an optimization problem with no constraints (i.e., $m=0$) and an objective which is a sum of squares of terms of the form $a_i^Tx-b_i$:

$$
\text{minimize}\quad f_0(x)=\lVert Ax-b\rVert_2^2=\sum_{i=1}^k(a_i^Tx-b_i)^2
$$

Here $A\in\mathbf{R}^{k\times n}$ (with $k\geq n$), $a_i^T$ are the rows of $A$, and vector $x\in\mathbf{R}^n$ is the optimization variable.

**Solving least-squares problems**:

The solution of a least-squares problem can be reduced to solving a set of linear equations,

$$
(A^TA)x=A^Tb
$$

so we have the analytical solution $x=(A^TA)^{-1}A^Tb$. The least-squares problem can be solved in a time approximately proportional to $n^2k$, with a known constant. Algorithms and software for solving least-squares problems are reliable enough for embedded optimization.

In many cases we can solve even larger least-squares problems, by exploiting some special structure in the coefficient matrix $A$. Suppose, for example, that the matrix $A$ is sparse, which means that it has far fewer than kn nonzero entries. By exploiting sparsity, we can usually solve the least-squares problem much faster than order $n^2k$.

For extremely large problems (say, with millions of variables), or for problems with exacting real-time computing requirements, solving a least-squares problem can be a challenge. But in the vast majority of cases, we can say that existing methods are very effective, and extremely reliable.

**Using least-squares**:

Recognizing an optimization problem as a least-squares problem is straightforward; we only need to verify that the objective is a quadratic function (and then test whether the associated quadratic form is positive semidefinite).

In _weighted least-squares_, the weighted least-squares cost

$$
\sum_{i=1}^kw_i(a_i^Tx-b_i)^2
$$

where $w_1,\dots,w_k$ are positive, is minimized.

Another technique in least-squares is _regularization_, in which extra terms are added to the cost function. In the simplest case, a positive multiple of the sum of squares of the variables is added to the cost function:

$$
\sum_{i=1}^k(a_i^Tx-b_i)^2+\rho\sum_{i=1}^nx_i^2
$$

where $\rho>0$. Regularization comes up in statistical estimation when the vector $x$ to be estimated is given a prior distribution.

### 1.2.2 Linear programming

Another important class of optimization problems is _linear programming_, in which the objective and all constraint functions are linear:

$$
\begin{aligned}
&\text{minimize}\quad c^Tx\\\\
&\text{subject to}\quad a_i^Tx\leq b_i,\quad i=1,\dots,m.
\end{aligned}
$$

Here the vectors $c,a_1,\dots,a_m\in\mathbf{R}^n$ and scalars $b_1,\dots,b_m\in\mathbf{R}$ are problem parameters that specify the objective and constraint functions.

## 1.3 Convex optimization

A convex optimization problem is one of the form

$$
\begin{aligned}
&\text{minimize}\quad f_0(x)\\\\
&\text{subject to}\quad f_i(x)\leq b_i,\quad i=1,\dots,m.
\end{aligned}
$$

where the functions $f_0,\dots,f_m:\mathbf{R}^n\rightarrow\mathbf{R}$ are convex, i.e., satisfy

$$
f_i(\alpha x+\beta y)\leq\alpha f_i(x)+\beta f_i(y)
$$

for all $x,y\in\mathbf{R}^n$ and all $\alpha,\beta\in\mathbf{R}$ with $\alpha+\beta=1,\alpha\geq 0,\beta\geq 0$.

> The least-squares problem and linear programming problem are both special cases of the general convex optimization problem.
{: .prompt-tip }

### 1.3.1 Solving convex optimization problems

There is in general no analytical formula for the solution of convex optimization problems, but (as with linear programming problems) there are very effective methods for solving them. Interior-point methods work very well in practice, and in some cases can be proved to solve the problem to a specified accuracy with a number of operations that does not exceed a polynomial of the problem dimensions.

We cannot yet claim that solving general convex optimization problems is a mature technology, like solving least-squares or linear programming problems. Research on interior-point methods for general nonlinear convex optimization is still a very active research area, and no consensus has emerged yet as to what the best method or methods are. But it is reasonable to expect that solving general convex optimization problems will become a technology within a few years. And for some subclasses of convex optimization problems, for example second-order cone programming or geometric programming, it is fair to say that interior-point methods are approaching a technology.

### 1.3.2 Using convex optimization

Using convex optimization is, at least conceptually, very much like using leastsquares or linear programming. If we can formulate a problem as a convex optimization problem, then we can solve it efficiently, just as we can solve a least-squares problem efficiently. With only a bit of exaggeration, we can say that, if you formulate a practical problem as a convex optimization problem, then you have solved the original problem.

## 1.4 Nonlinear optimization

_Nonlinear optimization_ (or nonlinear programming) is the term used to describe an optimization problem when the objective or constraint functions are not linear, but not known to be convex. Sadly, there are no effective methods for solving the general nonlinear programming problem

### 1.4.1 Local optimization

In _local optimization_, the compromise is to give up seeking the optimal x, which minimizes the objective over all feasible points. Instead we seek a point that is only locally optimal, which means that it minimizes the objective function among feasible points that are near it, but is not guaranteed to have a lower objective value than all other feasible points.

Local optimization methods are widely used in applications where there is value in finding a good point, if not the very best.

**Limitation**:

1. The initial guess or starting point is critical, and can greatly affect the objective value of the local solution obtained.
2. Local optimization methods are often sensitive to algorithm parameter values, which may need to be adjusted for a particular problem, or family of problems.

> Roughly speaking, local optimization methods are more art than technology.
{: .prompt-tip }

### 1.4.2 Global optimization

In _global optimization_, the true global solution of the optimization problem is found; the compromise is efficiency.

> Trade-off between precise and efficiency.
{: .prompt-tip }

**Comparison with local optimization**:

- If a local optimization method finds parameter values that yield unacceptable performance, it has succeeded in determining that the system is not reliable. But a local optimization method cannot certify the system as reliable; it can only fail to find bad parameter values.
- A global optimization method, in contrast, will find the absolute worst values of the parameters, and if the associated performance is acceptable, can certify the system as safe. The cost is computation time, which can be very large, even for a relatively small number of parameters. But it may be worth it in cases where the value of certifying the performance is high, or the cost of being wrong about the reliability or safety is high.

### 1.4.3 Role of convex optimization in nonconvex problems

- **Initialization for local optimization**: Starting with a nonconvex problem, we first find an approximate, but convex, formulation of the problem. By solving this approximate problem, which can be done easily and without an initial guess, we obtain the exact solution to the approximate convex problem.
  > This point is then used as the starting point for a local optimization method, applied to the original nonconvex problem.
  {: .prompt-tip }
- **Convex heuristics for nonconvex optimization**: Convex optimization is the basis for several heuristics for solving nonconvex problems.
- **Bounds for global optimization**: Many methods for global optimization require a cheaply computable lower bound on the optimal value of the nonconvex problem. Two standard methods for doing this are based on convex optimization. In _relaxation_, each nonconvex constraint is replaced with a looser, but convex, constraint. In _Lagrangian relaxation_, the Lagrangian dual problem is solved. This problem is convex, and provides a lower bound on the optimal value of the nonconvex problem.
