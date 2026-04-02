[![INFORMS Journal on Computing Logo](https://INFORMSJoC.github.io/logos/INFORMS_Journal_on_Computing_Header.jpg)](https://pubsonline.informs.org/journal/ijoc)

# MultiObjectiveAlgorithms.jl: a Julia package for solving multi-objective optimization problems

This archive is distributed in association with the [INFORMS Journal on Computing](https://pubsonline.informs.org/journal/ijoc) under the [MPL 2.0 License](LICENSE.md).

The software and data in this repository are a snapshot of the software and data
that were used in the research reported on in the paper
[MultiObjectiveAlgorithms.jl: a Julia package for solving multi-objective optimization problems](https://doi.org/10.1287/ijoc.2025.1449) by Oscar Dowson, Xavier Gandibleux, and Gökhan Kof.

The snapshot is based on
[this SHA](https://github.com/jump-dev/MultiObjectiveAlgorithms.jl/commit/4b78a85711f8a9b46712f9c7dcc88cb5116ede89)
in the development repository.

**Important: This code is being developed on an on-going basis at
https://github.com/jump-dev/MultiObjectiveAlgorithms.jl. Please go there if you
would like to get a more recent version or would like support**

## Cite

To cite the contents of this repository, please cite both the paper and this
epo, using their respective DOIs.

https://doi.org/10.1287/ijoc.2025.1449

https://doi.org/10.1287/ijoc.2025.1449.cd

Below is the BibTex for citing this snapshot of the repository.

```
@misc{DowsonMathOptAI,
  author =        {Dowson, Oscar and Gandibleux, Xavier and Kof, {G{\"o}khan},
  publisher =     {INFORMS Journal on Computing},
  title =         {MultiObjectiveAlgorithms.jl: a Julia package for solving multi-objective optimization problems},
  year =          {2026},
  doi =           {10.1287/ijoc.2025.1449.cd},
  url =           {https://github.com/INFORMSJoC/2025.1449},
  note =          {Available for download at https://github.com/INFORMSJoC/2025.1449},
}
```

<img src="https://raw.githubusercontent.com/jump-dev/MultiObjectiveAlgorithms.jl/master/moa.png" alt="An image of the Moa bird. Licensed into the Public Domain by https://freesvg.org/moa" width="100px"/>

# MultiObjectiveAlgorithms.jl

[![Build Status](https://github.com/jump-dev/MultiObjectiveAlgorithms.jl/actions/workflows/ci.yml/badge.svg?branch=master)](https://github.com/jump-dev/MultiObjectiveAlgorithms.jl/actions?query=workflow%3ACI)
[![codecov](https://codecov.io/gh/jump-dev/MultiObjectiveAlgorithms.jl/branch/master/graph/badge.svg)](https://codecov.io/gh/jump-dev/MultiObjectiveAlgorithms.jl)

[MultiObjectiveAlgorithms.jl](https://github.com/jump-dev/MultiObjectiveAlgorithms.jl)
(MOA) is a collection of algorithms for multi-objective optimization.

## License

`MultiObjectiveAlgorithms.jl` is licensed under the [MPL 2.0 License](https://github.com/jump-dev/MultiObjectiveAlgorithms.jl/blob/master/LICENSE.md).

## Getting help

If you need help, please ask a question on the [JuMP community forum](https://jump.dev/forum).

If you have a reproducible example of a bug, please [open a GitHub issue](https://github.com/jump-dev/MultiObjectiveAlgorithms.jl/issues/new).

## Installation

Install MOA using `Pkg.add`:

```julia
import Pkg
Pkg.add("MultiObjectiveAlgorithms")
```

## Use with JuMP

Use `MultiObjectiveAlgorithms` with JuMP as follows:

```julia
using JuMP
import HiGHS
import MultiObjectiveAlgorithms as MOA
model = JuMP.Model(() -> MOA.Optimizer(HiGHS.Optimizer))
set_attribute(model, MOA.Algorithm(), MOA.Dichotomy())
set_attribute(model, MOA.SolutionLimit(), 4)
```

Replace `HiGHS.Optimizer` with an optimizer capable of solving a
single-objective instance of your optimization problem.

You may set additional optimizer attributes, the supported attributes depend on
the choice of solution algorithm.

## Documentation

Documentation is available in the JuMP documentation. There are sections in
the manual on [setting a vector-valued objective](https://jump.dev/JuMP.jl/stable/manual/objective/#Set-a-vector-valued-objective)
and [working with multiple solutions](https://jump.dev/JuMP.jl/stable/manual/solutions/#Multiple-solutions).
See also the [list of tutorials that use MOA](https://jump.dev/JuMP.jl/stable/tutorials/overview/#tutorial_MultiObjectiveAlgorithms).

## Algorithm

Set the algorithm using the `MOA.Algorithm()` attribute. The value must be one
of the algorithms supported by MOA. Consult their docstrings for details.

Some algorithms are restricted to certain problem classes. The solution set
depends on the algorithm and the problem class.

| `MOA.Algorithm`           | Applicable problem class |
| :------------------------ | ------------------------ |
| `MOA.Chalmet()`           | Exactly two objectives   |
| `MOA.Dichotomy()`         | Exactly two objectives   |
| `MOA.DominguezRios()`     | Discrete variables only  |
| `MOA.EpsilonConstraint()` | Exactly two objectives   |
| `MOA.Hierarchical()`      | Any                      |
| `MOA.KirlikSayin()`       | Discrete variables only  |
| `MOA.Lexicographic()` [default] | Any                |
| `MOA.RandomWeighting()`   | Any                      |
| `MOA.Sandwiching()`       | Any                      |
| `MOA.TambyVanderpooten()` | Discrete variables only  |

## Other optimizer attributes

There are a number of optimizer attributes supported by the algorithms in MOA.

Each algorithm supports only a subset of the attributes. Consult the algorithm's
docstring for details on which attributes it supports, and how it uses them in
the solution process.

 * `MOA.AllowInnerInterrupt()`
 * `MOA.EpsilonConstraintStep()`
 * `MOA.LexicographicAllPermutations()`
 * `MOA.ObjectiveAbsoluteTolerance(index::Int)`
 * `MOA.ObjectivePriority(index::Int)`
 * `MOA.ObjectiveRelativeTolerance(index::Int)`
 * `MOA.ObjectiveWeight(index::Int)`
 * `MOA.SilentInner()`
 * `MOA.SolutionLimit()`
 * `MOI.TimeLimitSec()`

Query the number of scalar subproblems that were solved using

 * `MOA.SubproblemCount()`

For example:

```julia
using JuMP
import HiGHS
import MultiObjectiveAlgorithms as MOA
model = Model(() -> MOA.Optimizer(HiGHS.Optimizer))
# build the model
optimize!(model)
get_attribute(model, MOA.SubproblemCount())
```

## Solution ordering

Results are lexicographically ordered by their objective vectors. The order
depends on the objective sense. The first result is best.

## Ideal point

By default, MOA will compute the ideal point, which can be queried using the
`MOI.ObjectiveBound` attribute (or `JuMP.objective_bound`).

Computing the ideal point requires as many solves as the dimension of the
objective function. Thus, if you do not need the ideal point information, you
can improve the performance of MOA by setting the `MOA.ComputeIdealPoint()`
attribute to `false`:

```julia
using JuMP
import HiGHS
import MultiObjectiveAlgorithms as MOA
model = Model(() -> MOA.Optimizer(HiGHS.Optimizer))
set_attribute(model, MOA.ComputeIdealPoint(), false)
```
