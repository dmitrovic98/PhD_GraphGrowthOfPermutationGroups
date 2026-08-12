# EigenBallTest

This directory contains **MAGMA** code for searching for eigenvectors whose support is contained in a ball of a prescribed finite radius in (potentially infinite) voltage covers of graphs.

The code implements a lemma from the thesis concerning the extension-by-zero of certain eigenvectors of induced subgraphs. It was used to find a finite-support $0$-eigenvector over $\mathbb{F}_2$ for a particular $\mathbb{Z}^5$-voltage cover of a $120$-vertex, $4$-valent, $2$-arc-transitive graph discussed in **Section 8.1** of my doctoral thesis.

## Files

The directory contains the following files:

* `EigenBallTest.txt` — the main MAGMA code implementing the search algorithm, together with auxiliary functions for working with voltage covers.
* `T120_GraphData.txt` — data and parameters for applying the algorithm to the particular voltage cover considered in **Chapter 8**, together with an example of an eigenvector found by the search algorithm.

## Usage

`EigenBallTest.txt` is a tool of independent interest for searching for and verifying eigenvectors over $\mathbb{Z}_m$ in voltage covers of arbitrary graphs, provided the corresponding graph and voltage-cover parameters are supplied in the same format as in `T120_GraphData.txt`.

---

## Where this code is used in the thesis

The mathematical result implemented by this code is **Lemma 7.2.1** from **Section 7.2** of the thesis:

> **Lemma 7.2.1.** (Extension-by-zeros) Let $H$ be an abelian group and let $\phi \in \operatorname{End}(H)$. Let $\Gamma$ be a locally finite graph, let $S \subseteq V\Gamma$, and let $\Gamma_{\overline{S}}$ be the induced subgraph of $\Gamma$ induced by the closure $\overline{S}$ of $S$ in $\Gamma$. If $f \in E_{\phi,H}(\Gamma_{\overline{S}})$ and $f$ vanishes on $\partial S$, then $f$ extends by zeros to an element of $E_{\phi,H}(\Gamma)$.

The corresponding search procedure is described in **Algorithm 7.2.8**, where we take $S = B_r(v)$ to be the ball of radius $r$ around a vertex $v$ in $\Gamma$ and increase the radius $r$ until an eigenvector satisfying the required boundary condition is found.

### Application in Chapter 8

The particular case addressed by the code is the $\mathbb{Z}^5$-voltage cover defined in **Construction 8.1.5** of the thesis. This is the voltage cover associated with the $120$-vertex, $4$-valent, $2$-arc-transitive graph referred to as **$T120$** in this repository.

The underlying graph $T120$ is isomorphic to the Cayley graph

$$
\operatorname{Cay}\left(S_5,{(1,5),(2,5),(3,5),(4,5)}\right),
$$

as given in **Definition 8.1.1** of the thesis.

---

## Mathematical background

### The extension-by-zero lemma

Let $H$ be an abelian group, let $\phi \in \operatorname{End}(H)$, and let $\Gamma$ be a locally finite graph. The **$\phi$-eigengroup** of $\Gamma$ over $H$, denoted by

$$
E_{\phi,H}(\Gamma),
$$

consists of all functions $f \colon V\Gamma \to H$ satisfying

$$
\phi(f(v)) = \sum_{u \in \Gamma(v)} f(u)
\qquad\text{for every } v \in V\Gamma.
$$

Eigengroups generalise eigenspaces of adjacency matrices of graphs over fields. In particular, when $H$ is the cyclic group of order $p$, eigengroups coincide with the usual eigenspaces of graphs over $\mathbb{F}_p$.

For a subset $S \subseteq V\Gamma$, let $\overline{S}$ denote its **closure** in $\Gamma$ and let $\partial S$ denote its **boundary**, so that

$$
\overline{S}
============

# \bigcup_{s\in S} \bigl({s}\cup\Gamma(s)\bigr)

S\cup\partial S.
$$

The **extension-by-zero lemma** states that if

$$
f \in E_{\phi,H}(\Gamma_{\overline{S}})
$$

and $f$ vanishes on $\partial S$, then extending $f$ by zero outside $\overline{S}$ gives an element of $E_{\phi,H}(\Gamma)$ with the same support.

In other words, if $f$ is an eigenvector of the subgraph induced by $\overline{S}$ and

$$
f(v)=0
\qquad\text{for every }v\in\partial S,
$$

then $f$ can be extended by zero to an eigenvector of the full graph $\Gamma$ with the same eigenvalue and support.


The code exploits this lemma by taking $S$ to be a ball

$$
S = B_\Gamma(v,r)
$$

of radius $r$ around a chosen vertex $v$. In this case,

$$
\overline{S}=B_\Gamma(v,r+1),
$$

and $\partial S$ consists precisely of the vertices at distance $r+1$ from $v$.

Consequently, to find an eigenvector supported inside $B_\Gamma(v,r)$, it is enough to search for an eigenvector of the subgraph induced by $B_\Gamma(v,r+1)$ that vanishes on the boundary of $B_\Gamma(v,r)$.

This is precisely the search performed by the main function in `EigenBallTest.txt`: for increasing values of $r$, it constructs the induced subgraph on $B_\Gamma(v,r+1)$ and searches for an appropriate eigenvector satisfying the required boundary conditions.

---

## Voltage-cover representation

The code is designed to work with voltage covers of a base graph $X$.

Vertices in the voltage cover are represented by pairs

$$
(i,\mathbf{v}),
$$

where $i$ is a vertex of the base graph $X$ and $\mathbf{v}$ is an element of the voltage group. If $j$ is a neighbour of $i$ in $X$ and $e$ is the voltage assigned to the arc $(i,j)$, then the corresponding neighbour of $(i,\mathbf{v})$ in the voltage cover is

$$
(j,\mathbf{v}+e).
$$

In the $T120$ example, the voltage group is $\mathbb{Z}^5$.

The voltage data are supplied through the following variables:

* `X` — the base graph $X$, including its vertex and edge sets;
* `CoTreeARCS` — the edges of $X$ not contained in a fixed spanning tree of $X$, together with a chosen orientation for each edge;
* `Voltages` — the voltage vectors corresponding to the oriented edges in `CoTreeARCS`.

The lists `CoTreeARCS` and `Voltages` must have the same ordering. The voltage associated with an oriented edge is given by the corresponding entry of `Voltages`. If the edge is traversed in the opposite direction, the voltage is negated.

This convention is implemented by the function `AssignVoltage`.

The function `NeighboursInAVoltageGraph` uses these voltage assignments to determine the neighbours of a vertex in the voltage cover.

---

## How the algorithm works

The main search function is

```magma
EigenTestForBallsRadiusAtMostRInVoltageGraph
```

It takes a base graph, voltage data, a starting vertex, a maximum radius, a modulus, and a proposed eigenvalue.

For each radius

[
r=0,1,\ldots,R,
]

the algorithm performs the following steps.

### 1. Construct the ball

Starting from the chosen vertex $v$, the algorithm constructs

[
S=B(v,r)
]

in the voltage cover.

The boundary of $S$ is maintained separately.

### 2. Construct the closure

The closure

[
\overline{S}=B(v,r+1)
]

is constructed by adjoining the boundary of $S$.

The code also records the edges in the induced graph on this set.

### 3. Construct the induced graph

The graph

[
Y=\Gamma[\overline{S}]
]

induced by the closure of $S$ is constructed.

### 4. Impose the eigenvector equation

Let $A$ be the adjacency matrix of $Y$. To search for an appropriate eigenvector with eigenvalue $t$, the code considers the kernel of
[
A-tI.
]

where the the entries corresponding to vertices in the boundary of $S$ are then constrained to be zero.

Thus, the kernel being computed consists precisely of the vectors which satisfy both:

1. the eigenvector equation on the induced graph; and
2. the required zero conditions on the boundary.

### 5. Search for a nonzero kernel vector

If the resulting matrix has a nonzero kernel vector, the algorithm returns:

* `true`, indicating that a suitable eigenvector was found;
* the radius (r);
* a nonzero kernel vector;
* its support, where each vertex is paired up with the value of the vector at that vertex.

If no suitable eigenvector is found for any radius up to $R$, the function returns `false`.





## How the algorithm works

The main search function is

```magma
EigenTestForBallsRadiusAtMostRInVoltageGraph
```

It takes a base graph, voltage data, a starting vertex, a maximum radius, a modulus, and a proposed eigenvalue.

For each radius

$$
r=0,1,\ldots,R,
$$

the algorithm performs the following steps.

### 1. Construct the ball

Starting from the chosen vertex $v$, the algorithm constructs the ball

$$
S=B_\Gamma(v,r)
$$

in the voltage cover $\Gamma$.

The boundary $\partial S$ is maintained separately.

### 2. Construct the closure

The closure of $S$ is

$$
\overline{S}=B_\Gamma(v,r+1).
$$

The algorithm constructs this larger ball by adjoining the boundary of $S$ and records the edges between vertices in the resulting set.

### 3. Construct the induced graph

The induced subgraph

$$
Y=\Gamma[\overline{S}]
$$

is constructed from the vertices and edges recorded in the previous step.

### 4. Impose the eigenvector equation and boundary conditions

Let $A$ be the adjacency matrix of $Y$. To search for an eigenvector with eigenvalue $t$, the code considers the system

$$
(A-tI)f=0
$$

over the specified coefficient ring, together with the additional conditions

$$
f(v)=0
\qquad\text{for every }v\in\partial S.
$$

Thus, the resulting kernel consists precisely of the vectors that satisfy both:

1. the eigenvector equation on the induced graph $Y$; and
2. the required zero conditions on the boundary of $S$.

### 5. Search for a nonzero kernel vector

If the resulting system has a nonzero solution, the algorithm returns:

* `true`, indicating that a suitable eigenvector was found;
* the radius `r` at which it was found;
* a nonzero kernel vector; and
* its support, with each vertex paired with the corresponding value of the eigenvector.

If no suitable eigenvector is found for any radius up to $R$, the function returns `false`.


---

## Main functions

### `AssignVoltage`

```magma
AssignVoltage(CoTreeARCS, Voltages, e)
```

Returns the voltage associated with the oriented edge $e$.

If the reverse orientation of an edge in `CoTreeARCS` is supplied, the corresponding voltage is negated.

---

### `NeighboursInAVoltageGraph`

```magma
NeighboursInAVoltageGraph(X, CoTreeARCS, Voltages, v)
```

Returns the neighbours of the vertex $v$ in the voltage cover.

A voltage-cover vertex is represented as a pair consisting of a base-graph vertex and a voltage-group element. The function determines the corresponding neighbours in the base graph and adds the appropriate voltage to the second coordinate.

---

### `BallInVoltageGraph`

```magma
BallInVoltageGraph(X, CoTreeARCS, Voltages, v, r)
```

Constructs a ball of radius $r$ centred at $v$ in the voltage cover.

The function returns information describing:

* the vertices currently in the ball;
* the boundary of the ball;
* the relevant edges.

This is used internally by the main eigenvector-search function.

---

### `EigenTestForBallsRadiusAtMostRInVoltageGraph`

```magma
EigenTestForBallsRadiusAtMostRInVoltageGraph(
    X, CoTreeARCS, Voltages, v, R, m, t
)
```

This is the main function of the file.

It searches balls of radius at most $R$ centred at $v$ for an eigenvector with eigenvalue $t$ satisfying the zero-boundary condition from the extension-by-zero lemma.

The parameter $m$ determines the coefficient ring $\mathbb{Z}_m$ used for the kernel computation.

The output has the form

```magma
b, r, f, S
```

where:

* `b` boolean value indicating whether a suitable eigenvector was found;
* `r` is the first radius at which one was found;
* `f` is a nonzero vector satisfying the required conditions;
* `S` is the support of the vector along with the corresponding values;

If no vector is found, the function returns `false` together with an empty result.

---

### `IsEigenVectorVoltageGraph`

```magma
IsEigenVectorVoltageGraph(X, CoTreeARCS, Voltages, S, m, t)
```

This function is used to verify a proposed eigenvector over $\mathbb{Z}_m$.

Here $S$ specifies the support of a vector alongside the corresponding values. The function constructs the subgraph induced by the closure of $S$ in the voltage cover and checks the eigenvector equation using the adjacency matrix over $\mathbb{Z}_m$.

This provides a convenient way of independently checking an eigenvector obtained from the search (or from other sources).

---

## T120 example

The file `EigenBallTest_T120.txt` contains the data needed to run the search on the particular voltage cover used in Chapter 8.

The base graph is a graph on (120) vertices. The file explicitly defines its edge set and a collection of oriented arcs, and then supplies the corresponding voltage data.

The voltage group is represented by

```magma
Z5 := RSpace(Z,5);
```

so that vertices in the voltage cover are represented using elements of $\mathbb{Z}^5$.

The file then converts the voltage matrix into a list `Voltages`, matching the ordering of the oriented arcs, and runs the main search with

```magma
EigenTestForBallsRadiusAtMostRInVoltageGraph(
    X, ARCS, Vol, <1, Z5!0>, 9, 2, 0
);
```

Thus, this example searches for an eigenvector with eigenvalue $0$ over $\mathbb{F}_2$, among balls of radius at most $9$ centred at the vertex

```magma
<1, Z5!0>
```

of the voltage cover.

## Example eigenvector

The file `EigenBallTest_T120.txt` also contains one eigenvector found using the search above.

The vector is represented by a set of pairs

```magma
< <base_vertex, voltage_vector>, value_at_vertex>
```

corresponding to the vertices on which the eigenvector is nonzero.

For example, an entry of the form

```magma
< <47, [0,1,1,1,0]>, 1>
```

represents the voltage-cover vertex whose base-graph coordinate is `47` and voltage coordinate is ([0,1,1,1,0]). The eigenvector takes values $1$ at this vertex.

---

## Verifying the example eigenvector

The saved eigenvector can be checked using the verification function

```magma
IsEigenVectorVoltageGraphF2
```

from `EigenBallTest.txt`.

After loading both `EigenBallTest.txt`. and `EigenBallTest_T120.txt`, one can evaluate

```magma
IsEigenVectorVoltageGraphF2(X, ARCS, Voltages, S, 2, 0);
```

where $S$ is the support recorded in `EigenBallTest_T120.txt`. The function returns `true` if the corresponding vector satisfies the eigenvector equation over $\mathbb{F}_2$.

---

## Reproducing the computation

To compute more eigenvectors of the $\mathbb{Z}^5$-voltage cover of $T120$ perform the following steps:

1. Load the file `EigenBallTest.txt` containing the relevant methods.
2. Load the file `EigenBallTest_T120.txt` containing the relevant data on T120.
3. Run `EigenTestForBallsRadiusAtMostRInVoltageGraph(X,CoTreeARCS,Voltages,<1,Z5!0>,R,2,0);` for some integer $R \geq 9$.
4. Inspect the returned values `b`, `r`, `v`, and `S`.
---
5. Optionally, verify that the set $S$ saved in `EigenBallTest_T120.txt` defines an eigenvector using `IsEigenVectorVoltageGraphF2(X, CoTreeARCS, Voltages, S, 2, 0);`.

The T120 file already contains a complete example call, so it can also be used as a template for applying the search to other voltage covers.

To adapt the computation to a different voltage cover, the principal data that need to be replaced are:

* the base graph `X`;
* the oriented arc list `ARCS`;
* the corresponding voltage list `Voltages`;
* the starting vertex;
* the maximum radius `R`;
* the modulus `m`;
* the eigenvalue `t`.

---

## Notes

* The main search function stops as soon as it finds a suitable nonzero vector, so the returned radius is the first radius at which the search succeeds.
* The voltage data must be ordered consistently with `CoTreeARCS`.
* Reversing the orientation of an edge changes the sign of its voltage.
* The main search routine allows computations over `Integers(m)`, while `IsEigenVectorVoltageGraphF2` is specifically implemented over (\mathbb F_2).
* The supplied T120 data are intended both as a reproducible example and as a template for adapting the code to other voltage covers.

---

## Source and attribution

The mathematical basis for the computation is the extension-by-zero lemma stated in **[Lemma 7.2.1 of the thesis]**.

The implementation of the voltage-cover search is part of the computational work accompanying the thesis.

[Insert here the appropriate citation for the source on which the implementation or voltage-cover construction is based.]

If the code is reused or modified, please cite:

> **Đ. Mitrović, *[Graph Growth of Permutation Groups]*, University of Auckland, 2026].**

---

## Requirements

The code requires **MAGMA** with support for:

* finite and integer matrix computations;
* graph constructions;
* vector spaces and modules;
* kernel computations.

No additional external packages are required.

The code has been tested using MAGMA V2.29-6.
