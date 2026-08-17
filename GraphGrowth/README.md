# Graph Growth of Permutation Groups

## Overview
This directory contains the **MAGMA** code used to computationally investigate the graph growth of finite transitive permutation groups.

It implements a number of results from the literature that determine the graph growth of transitive permutation groups, together with a decision procedure for determining whether a given permutation group has constant, polynomial, or exponential graph growth, or is conjectured to have one of these types of growth. The code also includes a procedure for applying these tests to all transitive permutation groups of a given degree and storing the resulting data.

The repository also contains separate data files for each degree $2$ through $47$, listing the indices of the corresponding transitive permutation groups partitioned according to their graph growth according to the computations performed using these codes.

## Files

### `GraphGrowthTheorems.txt`

Contains statements of the individual results from the literature, their references and MAGMA implementations with additional comments for clarity where needed. These results form the building blocks of the main decision procedure but can also be applied independently.

### `GraphGrowthTest.txt`

Contains the main graph growth testing routine. This code applies the algorithm based on the individual results in `GraphGrowthTheorems.txt` to a given permutation group and determines whether the available computational criteria establish its type of graph growth. If the graph growth cannot be determined, the routine outputs the conjectured type of graph growth according to the conjectures discussed in the *Mathematical Background* subsection.

### `GraphGrowthTestDegree.txt`

Contains the code used to run the graph growth test for all transitive permutation groups of a specified degree and store the resulting data in a structured format.

## Mathematical background

### Graph Growth
Let $\Gamma$ be a finite connected graph and let $G$ be a vertex-transitive group of automorphisms of $\Gamma$. The pair $(\Gamma,G)$ is called locally - $L$ if the permutation group induced by the action of the vertex-stabiliser $G_v$ on the neighbourhood of a vertex $v$ is permutation isomorphic to $L$.

The growth function $\mathcal{G}_L\colon\mathbb{N}\to\mathbb{N}$, defined by

$$
\mathcal{G}_L\colon n\mapsto
\max_{\substack{(\Gamma,G)\text{ locally-$L$}\newline|V\Gamma|=n}}
|G_v|
$$

records the largest possible order of a vertex-stabiliser among all locally-$L$ graph-group pairs $(\Gamma,G)$ with $|V\Gamma|=n$. Intuitively, the graph growth of $L$ describes the asymptotic behaviour of $\mathcal{G}_L$ as $n\rightarrow \infty$.

### Types of Graph Growth

The graph growth of a permutation group $L$ is said to be

* **constant** if there exists an integer $M\in \mathbb{N}$ such that $|G_v|\leq M$ for all locally - $L$ graph-group pairs $(\Gamma,G)$.
* **polynomial** if
  * there exists a real constant $\alpha>0$ such that $|G_v|\leq |V\Gamma|^\alpha$ for all locally - $L$ graph-group pairs $(\Gamma,G)$; **and**
  * if there exists a real constant $\beta>0$ and a family of arbitrarily large locally - $L$ graph-group pairs $\{(\Gamma_n,G_n)\}_{n\in\mathbb{N}}$ such that $|(G_n)_v|\geq |V\Gamma_n|^\beta$ for all $v\in V\Gamma_n$.
* **exponential** if there exists a real constant $c>1$ and a family of arbitrarily large locally - $L$ graph-group pairs $\{(\Gamma_n,G_n)\}_{n\in\mathbb{N}}$ such that $|(G_n)_v|\geq c^{|V\Gamma_n|}$ for all $v\in V\Gamma_n$.

### Graph Growth Conjectures

**PSV Conjecture**: A permutation group has constant graph growth if and only if it is semiprimitive.

**Polynomial Graph Growth Conjecture**: A transitive permutation group $L$ has polynomial graph growth if and only if $L$ is not semiprimitive and for all block systems $\mathcal{B}$ for $L$ and $B\in\mathcal{B}$, we have that

$$
\ker(L\curvearrowright \mathcal{B})_{(B)}= 1.
$$

**Exponential Graph Growth Conjecture**: A transitive permutation group $L$ has exponential graph growth if and only if $L$ admits a (nontrivial) block system $\mathcal{B}$ with $B\in\mathcal{B}$ such that

$$
\ker(L\curvearrowright\mathcal{B})_{(B)} \neq 1.
$$

## The graph growth test

The file `GraphGrowthTest.txt` contains the auxiliary data and functions used by the main graph-growth decision procedure.

* **Known examples:** Lists of transitive permutation group IDs whose graph growth is already known to be constant or exponential (this includes certain results of my doctoral thesis). More details on these groups and how to locate them in the MAGMA database is provided in ``GraphGrowthTheorems.txt`.
* **IspGraphResALLSemiPrimitive:** A test for (p)-graph-restrictiveness of semiprimitive groups.
* **RegNilSubGrpTest:** Tests for the existence of a regular nilpotent normal subgroup of a semiprimitive group satisfying the relevant criteria.
* **PolyVsExpConj :** A procedure that, for non-semiprimitive groups, determines whether polynomial or exponential graph growth is conjectured and returns `ConjPolynomial` or `ConjExponential`, respectively.

If the graph growth is determined, the test returns one of `Constant`, `Polynomial`, or `Exponential`. Otherwise, it returns one of `ConjConstant`, `ConjPolynomial`, or `ConjExponential`, according to the classifications suggested by the previously discussed conjectures.

The procedure `GraphGrowth` is structured as follows:

1. **Apply inexpensive tests.** Check simple properties (such as prime degree) and whether the group belongs to previously discussed families known to have constant or exponential graph growth. Then split into two cases: **primitive** and **imprimitive** groups.

2. **Primitive groups.** Test 2-transitivity, O'Nan-Scott HA type (affine type = regular abelian socle), the existence of a regular nilpotent normal subgroup, and (p)-graph-restrictiveness. If any of the tests apply, return `Constant`, otherwise apply **PolyVsExpConj** to obtain the corresponding conjectured classification (`ConjPolynomial` or `ConjExponential`).

3. **Imprimitive groups.** Test the hypothesis of the **Block Complement Theorem** by computing the kernels of maximal block systems. If the theorem applies for at least one block system, return `Exponential`. Otherwise, split into two cases according to whether the group is semiprimitive.

4. **Non-semiprimitive groups.** Test whether $L$ is weakly $p$ -subregular. If so, return `Polynomial`; otherwise, apply **PolyVsExpConj** to obtain the corresponding conjectured classification (`ConjPolynomial` or `ConjExponential`).

5. **Semiprimitive groups.** Test for the existence of two plinths, a regular nilpotent normal subgroup, and $p$ -graph-restrictiveness. If none of these tests applies, return `ConjConstant`.

## Usage and reproducibility

In order to use the individual tests, it suffices to load the file:

```magma
load "GraphGrowthTheorems.txt";
```
Once loaded, each test in the file takes a transitive permutation group as input and returns **true** or **false**, according to whether the result of the corresponding theorem applies to the group. For example:

```magma
L := Sym(5);
IsHATypeRes(L);
```
This returns **true**, indicating that $S_5$ is a primitive group of affine type and hence has constant graph growth by Theorem 1.1 of [Pablo Spiga, An application of the local C(G,T) theorem to a conjecture of Weiss, Bull. Lond. Math. Soc. 48 (2016), no. 1, 12–18].

In order to use the algorithm for the determing the graph growth, it sufficies to load the file:

```magma
load "GraphGrowthTest.txt";
```

The above takes two integers as an input:
* $d$: the degree of the transitive permutation group
* $n$: its index the L of Transitive Permutation Groups in MAGMA.

For example, running

```magma
GraphGrowth(5,5);
```

return `Constant` since the transitive permutation group of degree 5 and index 5, namely $S_5$, has constant graph growth. In order to obtain the ID of the transitive permutation group $L$, one can call:

```magma
TransitiveGroupIdentification(L);
```

In order to test all transitive permutation groups of a given degree simultaneous, it sufficies to load:

```magma
load "GraphGrowthTestDegree.txt";
```

This file loads "GraphGrowthTest.txt" as well, so it is not necessary to load it separately. Once loaded, one can input the integer $d$ for the desired degree and call

```magma
GraphGrowthTestDegree(4);
```
The function returns a sequence of integers corresponding to

* degree $d$,
* total number of (isomorphism classes of) permutation groups of degree $d$,
* number of transitive permutation groups established to have constant graph growth,
* number of transitive permutation groups established to have polynomial graph growth,
* number of transitive permutation groups established to have exponential graph growth,
* number of transitive permutation groups whose graph growth is conjectured to be constant,
* number of transitive permutation groups whose graph growth is conjectured to be polynomial,
* number of transitive permutation groups whose graph growth is conjectured to be exponential.

These numbers are then stored in a separate file called `GraphGrowth_TABLE.txt` (one sequence per row). Moreover, a file called "GraphGrowth_Degree%d.txt" is then created containing $6$ lists (Degree%d_Constant, Degree%d_Polynomial, Degree%d_Exponential, Degree%d_ConjConstant, Degree%d_ConjPolynomial, Degree%d_ConjExponential), each with the indices of the groups falling into the above categories. 

In particular, one can call:
```magma
load "GraphGrowth_Degree%d.txt";
```
in order to work with the transitive permutation groups of degree $d$, now partitioned into above lists according to their graph growth.

The data discussed in the Chapter 9 of my doctoral thesis was obtained by running `GraphGrowthTestDegree(d)` for all $2\leq d \leq 47$. The corresponding files are available in the `GraphGrowthDegree` directory.

