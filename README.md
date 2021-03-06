[![build-status][build-img]][build-url]
[![codecov][codecov-img]][codecov-url]
[![coveralls][coveralls-img]][coveralls-url]
[![license][license-img]][license-url]

# NonNegLeastSquares.jl
Some nonnegative least squares solvers in Julia.

### Basic Usage:

The command `X = nonneg_lsq(A,B)` solves the optimization problem:

Minimize `||A*X - B||` subject to `Xᵢⱼ >= 0`; in this case,
where `||.||` denotes the Frobenius norm (equivalently, the Euclidean norm if `B` is a column vector).
The arguments `A` and `B` are respectively (m × k) and (m × n) matrices, so `X` is a (k × n) matrix.

### Currently Implemented Algorithms:

The code defaults to the "Pivot Method" algorithm.
To specify a different algorithm, use the keyword argument `alg`. Currently implemented algorithms are:

```julia
nonneg_lsq(A,b;alg=:nnls)  # NNLS
nonneg_lsq(A,b;alg=:fnnls) # Fast NNLS
nonneg_lsq(A,b;alg=:pivot) # Pivot Method
nonneg_lsq(A,b;alg=:pivot,variant=:cache) # Pivot Method (cache pseudoinverse up front)
nonneg_lsq(A,b;alg=:pivot,variant=:comb) # Pivot Method with combinatorial least-squares
```

Default algorithm:

```julia
nonneg_lsq(A,b) # pivot method
```

The keyword `Gram` specifies whether the inputs are Gram matrices (as shown in the examples below).
This defaults to `false`.

```julia
nonneg_lsq(A'*A,A'*b;alg=:nnls,gram=true) # NNLS
nonneg_lsq(A'*A,A'*b;alg=:fnnls,gram=true) # Fast NNLS
```

***References***
* **NNLS**:
     * Lawson, C.L. and R.J. Hanson, Solving Least-Squares Problems, Prentice-Hall, Chapter 23, p. 161, 1974.
* **Fast NNLS**:
     * Bro R, De Jong S. [A fast non-negativitity-constrained least squares algorithm](https://dx.doi.org/10.1002%2F%28SICI%291099-128X%28199709%2F10%2911%3A5%3C393%3A%3AAID-CEM483%3E3.0.CO%3B2-L). Journal of Chemometrics. 11, 393–401 (1997)
* **Pivot Method**:
     * Kim J, Park H. [Fast nonnegative matrix factorization: an active-set-like method and comparisons](http://www.cc.gatech.edu/~hpark/papers/SISC_082117RR_Kim_Park.pdf). SIAM Journal on Scientific Computing 33.6 (2011): 3261-3281.

### Installation:

```julia
Pkg.add("NonNegLeastSquares")
Pkg.test("NonNegLeastSquares")
```

### Simple Example:

```julia
using NonNegLeastSquares

A = [ -0.24  -0.82   1.35   0.36   0.35
      -0.53  -0.20  -0.76   0.98  -0.54
       0.22   1.25  -1.60  -1.37  -1.94
      -0.51  -0.56  -0.08   0.96   0.46
       0.48  -2.25   0.38   0.06  -1.29 ];

b = [-1.6,  0.19,  0.17,  0.31, -1.27];

x = nonneg_lsq(A,b)
```

Produces:

```julia
5-element Array{Float64,1}:
 2.20104
 1.1901
 0.0
 1.55001
 0.0
```

### Speed Comparisons:

Run the `examples/performance_check.jl` script to compare the various algorithms that have been implemented on some synthetic data.
Note that the variants `:cache` and `:comb` of the pivot method improve performance substantially
when the inner dimension, `k`, is small.
For example, when `m = 5000` and `n = 5000` and `k=3`:

```
Comparing pivot:none to pivot:comb with A = randn(5000,3) and B = randn(5000,5000)
-------------------------------------------------------------------------------------
PIVOT:none →   2.337322 seconds (1.09 M allocations: 4.098 GB, 22.74% gc time)
PIVOT:comb →   0.096450 seconds (586.76 k allocations: 23.569 MB, 3.01% gc time)
```

### Algorithims That Need Implementing:

Pull requests are more than welcome, whether it is improving existing algorithms, or implementing new ones.

* Dongmin Kim, Suvrit Sra, and Inderjit S. Dhillon:
[A New Projected Quasi-Newton Approach for the Nonnegative Least Squares Problem](https://www.cs.utexas.edu/ftp/techreports/tr06-54.pdf).
UT Austin TR-06-54, circa 2007.
* Sra Suvrit Kim Dongmin and Inderjit S. Dhillon.
[A non-monotonic method for large-scale non-negative least squares.](https://doi.org/10.1080/10556788.2012.656368)
Optimization Methods and Software, 28(5):1012–1039, 2013.


### See also:

* The box constrained optimization methods in
[Optim.jl](https://github.com/JuliaNLSolvers/Optim.jl)
* The bound constrained methods in
[NLopt.jl](https://github.com/JuliaOpt/NLopt.jl)

<!-- URLs -->
[build-img]: https://travis-ci.org/ahwillia/NonNegLeastSquares.jl.svg
[build-url]: https://travis-ci.org/ahwillia/NonNegLeastSquares.jl?branch=master
[codecov-img]: https://codecov.io/github/ahwillia/NonNegLeastSquares.jl/coverage.svg?branch=master
[codecov-url]: https://codecov.io/github/ahwillia/NonNegLeastSquares.jl?branch=master
[coveralls-img]: https://coveralls.io/repos/ahwillia/NonNegLeastSquares.jl/badge.svg?branch=master
[coveralls-url]: https://coveralls.io/github/ahwillia/NonNegLeastSquares.jl?branch=master
[license-img]: http://img.shields.io/badge/license-MIT-brightgreen.svg?style=flat
[license-url]: LICENSE.md
