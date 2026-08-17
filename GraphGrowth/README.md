# Graph Growth of Permutation Groups

This directory contains the MAGMA code used to computationally investigate the graph growth of finite transitive permutation groups.

The code implements the search algorithms and tests used to determine whether a given transitive permutation group has constant, polynomial, or exponential graph growth.

## Files

### `GraphGrowth_Methods.txt`

Contains the main methods used by the graph-growth search. In particular, it contains the definitions and auxiliary methods required by the computational tests.

### `GraphGrowthTest_Degree.txt`

Contains the code used to run the graph-growth tests for transitive permutation groups of a specified degree.

### `GraphGrowthTest.txt`

Contains the main graph-growth testing routine. This code applies the search algorithm to individual permutation groups and determines whether the available computational criteria establish exponential graph growth.

## Requirements

The code is written in **MAGMA** and requires a MAGMA installation to run.

The files are intended to be used together. `GraphGrowth_Methods.txt` should be loaded before running the testing routines.

## Purpose

The computational approach searches for suitable structures in the associated permutation-group action that certify exponential graph growth. The implementation was developed to test large collections of transitive permutation groups and to support the computational results presented in the thesis.

The code is designed primarily for **testing and verification of the graph-growth results**, rather than as a general-purpose software package.

## Usage

Load the methods file first:

```magma
load "GraphGrowth_Methods.txt";
```

The appropriate test file can then be loaded to perform the desired computation.

The degree-specific test file is intended for systematic searches over transitive permutation groups of a given degree, while `GraphGrowthTest.txt` provides the underlying test for individual groups.

## Reproducibility

The code accompanying this repository was used to generate and verify the computational data concerning graph growth of transitive permutation groups discussed in the thesis.

For the mathematical definitions, theoretical results, and explanation of the algorithms, see the corresponding sections of the thesis.
