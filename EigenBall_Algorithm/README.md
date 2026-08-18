# EigenBallTest

This directory contains **Magma** code for searching for eigenvectors whose support is contained in a ball of a prescribed finite radius in (potentially infinite) voltage covers of graphs. The code implements a lemma from my doctoral thesis **Graph growth of permutation groups** concerning the extensions of certain eigenvectors of induced subgraphs. This code has been used to find a finite-support $0$-eigenvector over $\mathbb{F}_2$ for a particular $\mathbb{Z}^5$-voltage cover of a $120$-vertex, $4$-valent, $2$-arc-transitive graph discussed in **Section 8.1** of my doctoral thesis.

## Files

The directory contains the following files:

* `EigenBallTest.txt` — the main Magma code implementing the search algorithm, together with auxiliary functions for working with voltage covers.
* `T120_GraphData.txt` — data and parameters for applying the algorithm to the particular voltage cover considered in **Chapter 8** of my doctoral thesis, together with an example of an eigenvector found by the search algorithm.

`EigenBallTest.txt` is a tool of independent interest for computing and verifying eigenvectors of voltage covers of arbitrary graphs over $\mathbb{Z}_m$, provided the corresponding graph and voltage-cover parameters are supplied in the same format as in `T120_GraphData.txt`.

## Application to constructing graph families with exponentially large eigengroups over $\mathbb{F}_2$

The particular case addressed by the code is the $\mathbb{Z}^5$-voltage cover defined in **Construction 8.1.5** of the thesis. This is the voltage cover associated with the $120$-vertex $4$-valent $2$-arc-transitive connected graph referred to as **$T_{120}$** in this repository.

The underlying graph $T_{120}$ is isomorphic to the Cayley graph

$$
{Cay}\left(S_5, \{(1,5),(2,5),(3,5),(4,5)\} \right),
$$

as given in **Definition 8.1.1** of the thesis.


### Verifying the example eigenvector

In order to prove **Theorem 8.1.10**, i.e., prove that the $\mathbb{Z}^5$-voltage cover of $T_{120}$ admits a $0$-eigenvector over $\mathbb{F}_2$ with finite support, it suffices to check that the set $S$ saved in `T120_GraphData.txt` defines such an eigenvector using the verification function

```magma
IsEigenVectorVoltageGraph
```

from `EigenBallTest.txt`.

After loading both `EigenBallTest.txt` and `T120_GraphData.txt`, run

```magma
IsEigenVectorVoltageGraphF2(X, CoTreeARCS, Voltages, S, 2, 0);
```

The function returns `true` if the corresponding vector satisfies the eigenvector equation over $\mathbb{F}_2$.

## Mathematical background

The mathematical result implemented by this code is **Lemma 7.2.1** from **Section 7.2** of my doctoral thesis:

> **Lemma 7.2.1.** [Extension-by-zeros] Let $H$ be an abelian group and let $\phi \in {End}(H)$. Let $\Gamma$ be a locally finite graph, let $S \subseteq V\Gamma$, and let $\Gamma_{\overline{S}}$ be the subgraph of $\Gamma$ induced by the closure $\overline{S}$ of $S$ in $\Gamma$. If $f \in E_{\phi,H}(\Gamma_{\overline{S}})$ and $f$ vanishes on $\partial S$, then $f$ to an element of $E_{\phi,H}(\Gamma)$ with the same support.

The corresponding search procedure is described in **Algorithm 7.2.8**, where we take $S$ to be $B_r(v)$, that is the ball of radius $r$ around a vertex $v$ in $\Gamma$, and increase the radius $r$ until an eigenvector satisfying the required boundary condition is found.


### Definitions

Let $H$ be an abelian group, let $\phi \in {End}(H)$, and let $\Gamma$ be a locally finite graph. The **$\phi$-eigengroup** of $\Gamma$ over $H$, denoted by $E_{\phi,H}(\Gamma)$ consists of all functions $f \colon V\Gamma \to H$ satisfying

$$
\phi(f(v)) = \sum_{u \in \Gamma(v)} f(u)
\qquad\text{for every } v \in V\Gamma.
$$

Eigengroups generalise eigenspaces of adjacency matrices of graphs over fields. In particular, when $H$ is the cyclic group of order $p$, eigengroups coincide with the usual eigenspaces of graphs over $\mathbb{F}_p$.

For a subset $S \subseteq V\Gamma$, let $\overline{S}$ denote its **closure** in $\Gamma$ and let $\partial S$ denote its **boundary**, so that

$$
\overline{S} = \bigcup_{s\in S} \bigl({s}\cup\Gamma(s)\bigr)
$$

and

$$
\partial S = \overline{S}\setminus S.
$$

Note that $\overline{S} = S \sqcup \partial S$. 

Finally, $\Gamma_{\overline{S}}$ is the induced subgraph of $\Gamma$ induced by $\overline{S}$, meaning that $V\Gamma_{\overline{S}} = \overline{S}$ with two vertices adjacent if and only if they are adjacent in $\Gamma$. The Extension-by-zeros Lemma states that if

$$
f \in E_{\phi,H}(\Gamma_{\overline{S}})
$$

and $f$ vanishes on $\partial S$, then extending $f$ by zero outside $\overline{S}$ gives an element of $E_{\phi,H}(\Gamma)$ with the same support.

In other words, if $f$ is an eigenvector of $\Gamma_{\overline{S}}$ and

$$
f(v)=0
\qquad\text{for every }v\in\partial S,
$$

then $f$ can be extended by zero to an eigenvector of the full graph $\Gamma$ with the same eigenvalue and support.


The code exploits this lemma by taking $S$ to be a ball

$$
S = B_r(v)
$$

of radius $r$ around a chosen vertex $v$. In this case,

$$
\overline{S}=B_{r+1}(v),
$$

and $\partial S$ consists precisely of the vertices at distance $r+1$ from $v$.

Consequently, to find an eigenvector supported inside $B_r(v)$, it is enough to search for an eigenvector of the subgraph induced by $B_{r+1}(v)$ that vanishes on the boundary of $B_r(v)$.

This is precisely the search performed by the main function in `EigenBallTest.txt`. For increasing values of $r$, the code constructs the induced subgraph on $B_{r+1}(v)$ and searches for an appropriate eigenvector satisfying the required boundary conditions.

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

In the case of $T_{120}$, the voltage group is $\mathbb{Z}^5$.

The voltage data are supplied through the following variables:

* `X` — the base graph $X$, including its vertex and edge sets;
* `CoTreeARCS` — the edges of $X$ not contained in a fixed spanning tree of $X$, together with a chosen orientation for each edge;
* `Voltages` — the voltage vectors corresponding to the oriented edges in `CoTreeARCS`.

The lists `CoTreeARCS` and `Voltages` must have the same ordering. The voltage associated with an oriented edge is given by the corresponding entry of `Voltages`. If the edge is traversed in the opposite direction, the voltage is negated. This rule in implemented in `AssignVoltage`. The function `NeighboursInAVoltageGraph` uses these voltage assignments to determine the neighbours of a vertex in the voltage cover.

## How the algorithm works

The main search function is

```magma
EigenTestForBallsRadiusAtMostRInVoltageGraph
```

It takes a base graph, voltage data, a starting vertex, a maximum radius, a modulus, and a proposed eigenvalue.

For each radius

$$
r\in [0,1,\ldots,R],
$$

the algorithm performs the following steps.

### 1. Construct the ball

Starting from the chosen vertex $v$, the algorithm constructs

$$
S=B_r(v)
$$

in the voltage cover.

The boundary of $S$ is maintained separately.

### 2. Construct the closure

The closure

$$
\overline{S}=B_{r+1}(v)
$$

is constructed by adjoining the boundary of $S$.

The code also records the edges in the induced graph on this set.

### 3. Construct the induced graph

The graph

$$
Y=\Gamma_{\overline{S}}
$$

induced by the closure of $S$ is constructed.

### 4. Impose the eigenvector equation

Let $A$ be the adjacency matrix of $Y$ over $\mathbb{Z}_m$. To search for an appropriate eigenvector with eigenvalue $t$, the code considers the kernel of

$$
A-tI,
$$

where the the entries corresponding to vertices in the boundary of $S$ are then constrained to be zero.

Thus, the kernel being computed consists precisely of the vectors which satisfy both:

1. the eigenvector equation on the induced graph; and
2. the required zero conditions on the boundary.

### 5. Search for a nonzero kernel vector

The has an output of the form

```magma
b, r, f, S
```

where:

* `b` boolean value indicating whether a suitable eigenvector was found;
* `r` is the first radius at which one was found;
* `f` is a nonzero vector satisfying the required conditions;
* `S` is the support of the vector along with the corresponding values;

If no suitable eigenvector is found for any radius up to $R$, the function returns `false` and an empty result.

## Auxiliary functions

### `AssignVoltage`

```magma
AssignVoltage(CoTreeARCS, Voltages, e)
```

Returns the voltage associated with the oriented edge $e$.

If the reverse orientation of an edge in `CoTreeARCS` is supplied, the corresponding voltage is negated.

### `NeighboursInAVoltageGraph`

```magma
NeighboursInAVoltageGraph(X, CoTreeARCS, Voltages, v)
```

Returns the neighbours of the vertex $v$ in the voltage cover.

A voltage-cover vertex is represented as a pair consisting of a base-graph vertex and a voltage-group element. The function determines the corresponding neighbours in the base graph and adds the appropriate voltage to the second coordinate.

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

### `IsEigenVectorVoltageGraph`

```magma
IsEigenVectorVoltageGraph(X, CoTreeARCS, Voltages, S, m, t)
```

This function is used to verify a proposed eigenvector over $\mathbb{Z}_m$.

Here $S$ specifies the support of a vector alongside the corresponding values. The function constructs the subgraph induced by the closure of $S$ in the voltage cover and checks the eigenvector equation using the adjacency matrix over $\mathbb{Z}_m$.

This provides a convenient way of independently checking an eigenvector obtained from the search (or from other sources).



## $T_{120}$ 

The file `T120_GraphData.txt` contains the data needed to run the search on the particular voltage cover used in Chapter 8.

The base graph is a graph on $120$ vertices. The file explicitly defines its edge set and a collection of oriented arcs, and then supplies the corresponding voltage data.

The voltage group is represented by

```magma
Z5 := RSpace(Z,5);
```

so that vertices in the voltage cover are represented using elements of $\mathbb{Z}^5$.

The file also contains the list `Voltages`, matching the ordering of the oriented arcs.

One can run the main search by calling

```magma
EigenTestForBallsRadiusAtMostRInVoltageGraph(
    X, CoTreeARCS, Voltages, <1, Z5!0>, 9, 2, 0
);
```

Thus, this example searches for an eigenvector with eigenvalue $0$ over $\mathbb{F}_2$, among balls of radius at most $9$ centred at the vertex

```magma
<1, Z5!0>
```

of the voltage cover of $X$.

## Example eigenvector

The file `T120_GraphData.txt` also contains one eigenvector found using the search above.

The eigenvector is given in terms of its support set, whose elements are of the form

```magma
< <base_vertex, voltage_vector>, value_at_vertex>
```

corresponding to the vertices on which the eigenvector is nonzero.

For example, an entry of the form

```magma
< <47, [0,1,1,1,0]>, 1>
```

represents the voltage-cover vertex whose base-graph coordinate is `47` and voltage coordinate is ([0,1,1,1,0]). The eigenvector takes values $1$ at this vertex.

## Reproducing the computation

To compute more eigenvectors of the $\mathbb{Z}^5$-voltage cover of $T_{120}$ perform the following steps:

1. Load the file `EigenBallTest.txt` containing the relevant methods.
2. Load the file `T120_GraphData.txt` containing the relevant data on $T_{120}$.
3. Run `EigenTestForBallsRadiusAtMostRInVoltageGraph(X,CoTreeARCS,Voltages,<1,Z5!0>,R,2,0);` for some integer $R \geq 9$.
4. Inspect the returned values `b`, `r`, `v`, and `S`.


For the purposes of the results in Chapter 8 of the thesis, it suffices to verify that the set $S$ saved in `T120_GraphData.txt` defines a $0$-eigenvector of the $\mathbb{Z}^5$-voltage cover of $X$ over $\mathbb{F}_2$ with support of size $296$ by running

```magma
IsEigenVectorVoltageGraphF2(X, CoTreeARCS, Voltages, S, 2, 0);
```



To adapt the computation to a different voltage cover, the principal data that need to be replaced are:

* the base graph `X`;
* the oriented arc list `CoTreeARCS`;
* the corresponding voltage list `Voltages`;

Then one needs to run the main search algorithm with some appropriate choice of

* the starting vertex;
* the maximum radius `R`;
* the modulus `m`;
* the eigenvalue `t`.



## Notes

* The main search function stops as soon as it finds a suitable nonzero vector, so the returned radius is the first radius at which the search succeeds.
* The voltage data must be ordered consistently with `CoTreeARCS`.
* The supplied $T_{120}$ data are intended both as a reproducible example and as a template for adapting the code to other voltage covers.
