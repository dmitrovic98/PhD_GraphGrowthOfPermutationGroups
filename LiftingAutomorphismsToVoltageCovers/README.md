
# LiftingCriterion

This directory contains **MAGMA** code for testing whether automorphisms of a base graph lift along a voltage cover.

The implementation is based on the lifting criterion for (elementary) abelian voltage covers described by **Proposition 5.1** of A. Malnič, D. Marušič and P. Potočnik in *Elementary abelian covers of graphs*, J. Algebraic Combinatorics **20** (2004), 71–97

The code tests the lifting condition on a generating set of cycles for the first homology group of the base graph. Given a voltage assignment, an automorphism of the base graph, and an automorphism of the voltage group, the code determines whether the corresponding compatibility condition holds for every generating cycle.

## Files

The directory contains the following files:

* `LiftingCriterion.txt` — the main MAGMA code implementing the lifting criterion, together with auxiliary functions for applying automorphisms to cycles and computing cycle voltages.
* `T120_LiftingData.txt` — data and parameters for applying the criterion to the $120$-vertex $4$-valent $2$-arc-transitive graph $T_{120}$ and its corresponding voltage cover considered in the thesis.

## Mathematical background

### Voltage covers

Let $X$ be a connected graph and let $N$ be an abelian voltage group. A voltage assignment associates an element of $N$ to each oriented edge of $N$, with opposite orientations receiving inverse voltages.

The corresponding voltage cover has vertices represented by pairs

$$
(v,n),
$$

where $v\in VX$ and $n\in N$. If an oriented edge $(v,w)$ has voltage $e$, then

$$
(v,n)\sim(w,n+e).
$$

In the computations in this directory, the voltage group is understood to be an additive subgroup of an integer lattice. For example, in the $T_{120}$ case, the voltage group is $\mathbb{Z}^5$.

A spanning tree of $X$ is fixed, and voltages are assigned to the cotree arcs (voltages of all other arcs are trivial). The voltage of an arbitrary cycle is obtained by summing the voltages of its oriented edges, with the sign changed when an edge is traversed in the opposite direction.

### Lifting automorphisms

Let $g\in{Aut}(X)$ be an automorphism of the base graph. We are interested in determining whether $g$ lifts along the voltage cover, that is, whether there exists an automorphism of the voltage cover which projects onto $g$ on the base graph.

For abelian covers, this question can be expressed in terms of the action of $g$ on the first homology group of $X$ and the voltage assignment.

In the setting implemented here, let $\sigma$ be the relevant automorphism of the voltage group. The lifting condition is tested on a generating set `GenH1` of cycles by checking that

$$
\zeta(C)^\sigma=\zeta(C^g)
$$

for every $C\in\texttt{GenH1}$, where $\zeta(C)$ denotes the voltage of the cycle $C$ and $C^g$ is the image of $C$ under $g$.

Thus, the question of whether $g$ lifts is reduced to a finite collection of computations involving cycle voltages.

## How the algorithm works

The main function is

```magma
LiftingCriterion
```

It takes:

* `Voltages` — the voltage vectors assigned to the cotree arcs;
* `GenH1` — a generating set of cycles representing the generators of the first homology group;
* `CoTreeARCS` — the oriented cotree arcs (order of elements compatible with the order of elements of `Voltages`;
* `R` — the coefficient ring used in the computation;
* `dim` — the dimension of the voltage group;
* `g` — the automorphism of the base graph being tested;
* `sigma` — the corresponding automorphism of the voltage group.

The function proceeds as follows.

### 1. Apply the automorphism to the generating cycles

For each cycle $C$ in `GenH1`, the function applies the graph automorphism $g$ to every vertex of $C$, producing the image cycle

$$
C^g.
$$

This is implemented by the functions `ApplyMap` and `ApplyMapToCycle`.

### 2. Compute cycle voltages

The function `VoltageOfACycle` computes the voltage of a cycle by summing the voltages assigned to its oriented edges.

If an edge is traversed in the reverse direction to its orientation in `CoTreeARCS`, its voltage is negated. It is necessary that the two lists, `Voltages` and `CoTreeARCS`, are compatibly ordered.

### 3. Test the lifting condition

For each generating cycle $C$, the code compares

$$
\zeta(C)^\sigma
$$

with

$$
\zeta(C^g).
$$

If these values differ for any generator, the function immediately returns `false`.

If the equality holds for every generator, the function returns `true`.

Thus,

```magma
LiftingCriterion(...)
```

returns `true` precisely when the supplied automorphism $g$ satisfies the implemented lifting criterion for the given voltage assignment and voltage-group automorphism $\sigma$.

## Application to $T_{120}$

The computations in `T120_LiftingData.txt` apply the lifting criterion to the $120$-vertex $4$-valent $2$-arc-transitive graph $T_{120}$ considered in the thesis.

The graph $T_{120}$ is the Cayley graph

$$
{Cay}\left(S_5,{(1,5),(2,5),(3,5),(4,5)}\right).
$$

We have that

$$
{Aut}(T_{120}) \cong S_5\rtimes S_4,
$$

where $S_5$ is regular and $S_4$ consists of inner automorphisms of $S_5$ induced by conjugation by the elements of $Stab_{S_5}(5)$, which clearly preserve the connection set of $T_{120}$.

The corresponding voltage cover is the $\mathbb{Z}^5$-voltage cover considered in Chapter 8 of the thesis.

The file `T120_LiftingData.txt` contains

* vertex-set and edge-set of the graph $T_{120}$,
* spanning tree $T$ of $T_{120}$, corresponding co-tree arcs and homology generators,
* voltages in $\mathbb{Z^5}$,
* automorphisms $x$, $y$, $a$, $b$ and $c$ generating $Aut(T_{120})$, where $x$ and $y$ generate the regular copy of $S_5$, and $a$, $b$, and $c$ generate the stabiliser of the identity isomorphic to $S_4$,
* matrices $X$, $Y$, $A$, $B$ and $C$ in $GL(5,\mathbb{Z})$,
* calls to `LiftingCriterion` used to prove that each of the above automorphisms lifts along the voltage cover.

## Reference

The lifting criterion implemented by this code is based on **Proposition 5.1** in 

> A. Malnič, D. Marušič and P. Potočnik, *Elementary abelian covers of graphs*, Journal of Algebraic Combinatorics **20** (2004), 71–97.
