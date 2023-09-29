---
layout: post
title: "Julia: Default parameters in structs"
date: 2023-09-28 22:04:00
categories: regular Julia
tags: regular Julia
image: /assets/article_images/2022-08-15-julia_environment/night-track.JPG
image2: /assets/article_images/2022-08-15-julia_environment/night-track-mobile.JPG
---

In **Julia 1.9** it was finally included in the documentation a way to create `struct`s with default parameters. Although, this capability was available for long time, it was never well documented or explained. This post is a brief overview over `struct`s and how to generate `struct`s with default parameters.

<!--toc:start-->

- [Struct](#struct)
- [Mutable Struct](#mutable-struct)
- [kwdef (Base.@kwdef)](#kwdef-basekwdef)
- [Julia package **Parameters**](#julia-package-parameters)
<!--toc:end-->

## Struct

According to the documentation, `struct` is _the most commonly used kind of type in Julia, specified as a name and a set of fields_. An example would be:

```julia
struct Foo
	bar
	number::Int
	euler::Float64
end
```

As one can see `bar` in theory can be any kind of type, while `number` is an integer and `euler` is a float. One important thing about `struct`s is that they are immutable, _i.e._ once one of the fields is given a value this will not change. For more on `struct`s read for example about [constructors][pap1].

```julia
# Trying to change number
Foo.number = 42
ERROR: setfield!: immutable struct of type Foo cannot be changed
Stacktrace:
 [1] setproperty!(x::Foo, f::Symbol, v::Int64)
   @ Base ./Base.jl:41
 [2] top-level scope
   @ REPL[39]:1
end
```

In case one uses `struct`s with fields of the same type, I tend to use [parametric type structs][pap2].

## Mutable Struct

To change the value of one of the parameters (fields) in a `struct`, first one has to make the `struct` mutable. With this in mind write `mutable struct` before the name of the _`struct`_.

```julia
mutable struct Foo
	bar
	number::Int
	euler::Float64
end
```

## kwdef (Base.@kwdef)

A circle `struct` with some default parameters looks like:

```julia
Base.@kwdef mutable struct Circles
	# Set default values that are loaded
	name_of_c::String   = "gen_1_circle"
	id_my_cir::Int64    = 1
	radius_xx::Float64  = 0.0
	pi_struct::Float64  = 3.141595
end
```

The **Julia** documentation has [more details about kwdef use and limitations][pap3]. Users are encouraged to read the documentation.

## Julia package **Parameters**

If one prefers packages with error control and good descriptions of the parameters in a `struct`, then **Parameters** is the way to go. According to [its github description Parameters][pap3] is _a package to handle numerical-model parameters_.

```julia
# The package
using Parameters

# The struct
@with_kw struct A
	a::Int = 6
	b::Float64 = -1.1
	c::UInt8
end

# Using the struct A
A(c=4)
A
  a: 6
  b: -1.1
  c: 4

# ERROR when c is not defined in the construction of A
A()
ERROR: Field 'c' has no default, supply it with keyword.
```

[pap1]: https://docs.julialang.org/en/v1/manual/constructors/ "Julia documentation on constructors"
[pap2]: https://docs.julialang.org/en/v1/manual/types/#Parametric-Types "Julia parametric types"
[pap3]: https://docs.julialang.org/en/v1/base/base/#Base.@kwdef "Using default parameters in a struc"
[pap4]: https://github.com/mauro3/Parameters.jl "Parameters.jl: A package to handle numerical-model parameters"
