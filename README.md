# *Graph Growth of Permutation Groups* Computational Repository

This repository contains the **MAGMA** code and computational data accompanying my doctoral thesis:

> **Đ. Mitrović, *Graph Growth of Permutation Groups*, University of Auckland, 2026.**

The repository contains the computational work underlying several of the results in the thesis.

The code is organised into three main components:

* **`GraphGrowth`** — computational investigation of the graph growth of finite transitive permutation groups;
* **`LiftingCriterion`** — implementation of a criterion for determining whether automorphisms of a graph lift along an abelian voltage cover;
* **`EigenBallTest`** — an algorithm for searching for finite-support eigenvectors in voltage covers.

The three components are largely independent, but the latter two are used in the study of a particular voltage cover of the $4$-valent $2$-arc-transitive graph $T_{120}$ considered in Chapter 8 of the thesis.

---

## Repository Structure

### `GraphGrowth`

This directory contains the code used to computationally investigate the graph growth of finite transitive permutation groups.

The implementation includes:

* MAGMA implementations of individual results from the literature determining graph growth (discussed in Chapter 9 of the thesis).
* additional results established in the thesis;
* a main decision procedure combining these results;
* procedures for testing all transitive permutation groups of a specified degree;
* data files recording the resulting classifications of transitive permutation groups with respect to graph growth.

The main files are:

* `GraphGrowthTheorems.txt` — individual theoretical results, references, and their MAGMA implementations;
* `GraphGrowthTest.txt` — the main decision procedure for determining the graph growth of a given transitive permutation group;
* `GraphGrowthTestDegree.txt` — procedure for applying the test to all transitive permutation groups of a given degree.

The repository also contains separate data files for every degree (2\leq d\leq47). These files partition the transitive permutation groups of degree $d$ according to whether their graph growth is established or conjectured to be constant, polynomial, or exponential.

The computations in Chapter 9 of the thesis were obtained by running the degree-wise procedure for every degree from $2$ through $47$.

See [`GraphGrowth/README.md`](GraphGrowth/README.md) for the full description of the mathematical background, algorithms, usage, and computational data.

---

### `LiftingCriterion`

This directory contains an implementation of the lifting criterion for automorphisms of graphs along abelian voltage covers.

The implementation is based on Proposition 5.1 of:

> A. Malnič, D. Marušič and P. Potočnik, *Elementary abelian covers of graphs*, Journal of Algebraic Combinatorics **20** (2004), 71–97.

The code tests the lifting condition on a generating set of cycles for the first homology group of the base graph. Given a voltage assignment, an automorphism of the base graph, and an automorphism of the voltage group, it determines whether the corresponding compatibility condition is satisfied.

The directory also contains the data required to apply the criterion to the graph $T_{120}$ and the corresponding $\mathbb{Z}^5$-voltage cover studied in Chapter 8.

The main files are:

* `LiftingCriterion.txt` — implementation of the lifting criterion and its auxiliary functions;
* `T120_LiftingData.txt` — graph, voltage, homology, and automorphism data for the (T_{120}) example.

See [`LiftingCriterion/README.md`](LiftingCriterion/README.md) for the full description and instructions for reproducing the computations.

---

### `EigenBallTest`

This directory contains an algorithm for searching for eigenvectors whose support is contained in a ball of prescribed finite radius in a (not necessarily finite) voltage cover of a graph.

The algorithm is based on the **Extension-by-zeros Lemma** (Lemma 7.2.1 of the thesis) and implements the search procedure described in Algorithm 7.2.8.

The main idea is to search for an eigenvector of the subgraph induced by the closure of a ball which vanishes on the boundary. The Extension-by-zeros Lemma then guarantees that the vector extends to an eigenvector of the full graph with the same support.

The code was used to find and verify a finite-support $0$-eigenvector over $\mathbb{F}_2$ for the $\mathbb{Z}^5$-voltage cover of $T_{120}$ considered in Chapter 8.

The main files are:

* `EigenBallTest.txt` — implementation of the eigenvector search algorithm and auxiliary voltage-cover functions;
* `T120_GraphData.txt` — graph and voltage data for the $T_{120}$ example, together with an eigenvector found by the search.

The code is written to be adaptable to other voltage covers, provided the base graph and voltage data are supplied in the required format.

See [`EigenBallTest/README.md`](EigenBallTest/README.md) for the mathematical background, algorithm description, usage, and reproducibility instructions.

---

## The $T_{120}$ Example

A common computational object appearing in the repository is the graph $T_{120}$, a $120$-vertex $4$-valent $2$-arc-transitive graph considered in Chapter 8.

The graph is represented as the Cayley graph

$$
T_{120}\cong
Cay\left(S_5,{(1,5),(2,5),(3,5),(4,5)}\right).
$$

A particular $\mathbb{Z}^5$-voltage cover of $T_{120}$ is studied in the thesis.

Two computational aspects of this cover are addressed in the repository:

1. **Lifting automorphisms:** `LiftingCriterion` is used to verify that $Aut(T_{120})$ lifts to the voltage cover.
2. **Eigenvectors:** `EigenBallTest` is used to search for, and independently verify, a finite-support $0$-eigenvector over $\mathbb{F}_2$.

These computations provide the computational evidence required for the results concerning this voltage cover in Chapter 8.

---

## Reproducibility

All code in this repository is written in **MAGMA**.

The individual directories contain their own README files describing:

* the mathematical results implemented by the code;
* the structure and purpose of each file;
* the inputs and outputs of the main procedures;
* examples of how to run the computations;
* the data required to reproduce the computations appearing in the thesis.

The repository is therefore intended both as a **computational companion to the results in the thesis** and as a collection of reusable MAGMA tools for related problems involving permutation groups, graph covers, and eigengroups.

---

## Relationship to the Thesis

The organisation of the repository follows the computational components of the thesis:

| Repository component | Main thesis material | Purpose                                                                                      |
| -------------------- | -------------------- | -------------------------------------------------------------------------------------------- |
| `GraphGrowth`        | Chapter 9            | Computational classification of graph growth for transitive permutation groups               |
| `LiftingCriterion`   | Chapter 8            | Verification that automorphisms lift along the $T_{120}$ voltage cover                       |
| `EigenBallTest`      | Chapters 7–8         | Search for finite-support eigengroup elements and application to the $T_{120}$ voltage cover |

The repository includes both implementations of previously known theoretical results and computational results obtained as part of the thesis. In particular, the `GraphGrowth` data include the new graph-growth classifications established through the computational work of the thesis.

---

## Requirements

The code requires **MAGMA**. No additional external packages are required.

The code was tested using **MAGMA V2.29-6**.

For detailed requirements and usage instructions, see the README file in each individual directory.
