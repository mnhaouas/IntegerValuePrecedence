# IntegerValuePrecedence

This is an implementation of a Global CP Constraint for maintaining Integer Value Precedence over a sequence of Integer Variables.

This Constraint, along with its filtering algorithm, has first appeared in:
> Law Y.C., Lee J.H.M. (2004) Global Constraints for Integer and Set Value Precedence. In: Wallace M. (eds) *Principles and Practice of Constraint Programming – CP 2004*. CP 2004. Lecture Notes in Computer Science, vol 3258. Springer, Berlin, Heidelberg. doi:[10.1007/978-3-540-30201-8_28](https://doi.org/10.1007/978-3-540-30201-8_28)

## Usefulness

This Constraint, which maintains *Generalized Arc Consistency* (GAC), ensures integer value precedence of value *s* over value *t* across integer variable array *X* and proceeds to the appropriate filtering. It is also possible to use it for a sequence of values with precedence rules (see [Usage](#Usage)).

It is particularly useful in the context of **value symmetry breaking** where there exists two or more *indistinguishable values*. As such, this Global Constraint can be posted as a **redundant** Constraint in a CP model to significantly reduce the search space by pruning similar, unsuccessful sub-trees. Such symmetries can occur, for example, in Clustering problems, Packing problems and Scheduling problems.

## Technical details

### Prerequisites

This Constraint has been implemented using *CP Optimizer Extensions* from *IBM ILOG CPLEX Optimization Studio*. It has been tested with *IBM ILOG CPLEX Optimization Studio* versions 12.8, 12.9. At this time, *CP Optimizer* engine extensions API is **only available in C++**.

If you are a faculty member or a student, you can get *IBM ILOG CPLEX Optimization Studio* for free from IBM.

### Complexity

The filtering algorithm implemented by this Global Constraint has a time complexity in *O*(*n*), where *n* is the size of array *X*, and a space complexity in *&#920;*(1).


## Usage

This Global Constraint can be used in *Concert Technology for C++*, a modeling layer included with *IBM ILOG CPLEX Optimization Studio*, which provides interfaces to *CP Optimizer* as well as *CPLEX Optimizer* for use in your own C++ applications.

To start using this Constraint, include `IloIntPrecedeBinary.h` in your *Concert Technology* C++ sources. You can then add it to your `IloModel` using its member function `IloModel::add`. `IloIntPrecedeBinary` has the following prototype:
```
IloConstraint  IloIntPrecedeBinary(IloEnv env, IloIntVarArray X, IloInt s, IloInt t, const char* name = 0);
```

where:
- `env` is the optimizer's `IloEnv` environment;
- `X` is the modeling layer handle `IloIntVarArray` for the *n*-variable array of integers;
- `s` and `t` are integer values of type `IloInt` where `s`, the antecedent, should precede `t`, the subsequent, in array `X` when fully instantiated;
- `name` is an optional custom name given to the posted Constraint in the model.

In case you need to post this Constraint for a sequence of values (for example 0, 1, ..., K &#8722; 1), the following implementation is advisable:
```
for (IloInt i = 1; i < K; i++)
    model.add(IloIntPrecedeBinary(env, X, i-1, i));
```

Though posting on all possible pairs of values would've ensured strictly stronger filtering, it is inefficient in practice and does not produce better results (see cited paper above). Therefore, it is recommended to post on pairs of adjacent values only.

## Another way...

The same value precedence enforcement can be achieved using a *Regular Language Membership* Constraint, which appears in:
> Pesant G. (2004) A Regular Language Membership Constraint for Finite Sequences of Variables. In: Wallace M. (eds) *Principles and Practice of Constraint Programming – CP 2004*. CP 2004. Lecture Notes in Computer Science, vol 3258. Springer, Berlin, Heidelberg. doi:[10.1007/978-3-540-30201-8_36](https://doi.org/10.1007/978-3-540-30201-8_36)

Look out for a future implementation of this work using *IBM ILOG CPLEX Optimization Studio*.

## Other platforms

Both this Constraint as well as Pesant's Constraint are already available in [Gecode](https://github.com/Gecode/gecode) as built-in Constraints.