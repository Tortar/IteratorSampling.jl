# IteratorSampling.jl

[![CI](https://github.com/JuliaDynamics/Agents.jl/workflows/CI/badge.svg)](https://github.com/Tortar/IteratorSampling.jl/actions?query=workflow%3ACI)
[![](https://img.shields.io/badge/docs-stable-blue.svg)](https://tortar.github.io/IteratorSampling.jl/dev)
[![codecov](https://codecov.io/github/Tortar/IteratorSampling.jl/graph/badge.svg?token=F8W0MC53Z0)](https://codecov.io/github/Tortar/IteratorSampling.jl)

This package allows to sample from any iterable in a single pass through the data, 
even if the number of items in the collection is unknown. 

If the iterable is lazy, the memory required is only that of the sample, instead of the
all population, which can be useful for sampling from big data streams.

Moreover, it turns out that sampling with the techniques implemented in this library is also much faster 
in some common cases, as shown below:


```julia
julia> using IteratorSampling

julia> using BenchmarkTools, Random, StatsBase

julia> rng = Xoshiro(42);

julia> iter = Iterators.filter(x -> x != 10, 1:10^7);

julia> @benchmark sample($rng, collect($iter), 10^4; replace=false)
BenchmarkTools.Trial: 35 samples with 1 evaluation.
 Range (min … max):  140.193 ms … 210.101 ms  ┊ GC (min … max): 3.70% … 34.84%
 Time  (median):     141.008 ms               ┊ GC (median):    3.69%
 Time  (mean ± σ):   144.830 ms ±  15.045 ms  ┊ GC (mean ± σ):  6.19% ±  7.00%

  █▂                                                             
  ██▅▁▁▅▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▅▁▁▁▁▁▁▁▁▅ ▁
  140 ms        Histogram: log(frequency) by time        210 ms <

 Memory estimate: 147.05 MiB, allocs estimate: 27.

julia> @benchmark itsample($rng, $iter, 10^4; replace=false)
BenchmarkTools.Trial: 648 samples with 1 evaluation.
 Range (min … max):  6.823 ms …   9.196 ms  ┊ GC (min … max): 0.00% … 0.00%
 Time  (median):     7.899 ms               ┊ GC (median):    0.00%
 Time  (mean ± σ):   7.724 ms ± 417.797 μs  ┊ GC (mean ± σ):  0.01% ± 0.33%

   ▁▅▁▁                                  ▃▆█▆▄▄▃▁              
  ██████▄▆▅▅▆▅▄▆▆▄▅▁▄▄▁▁▅▄▄▄▁▅▄▄▁▁▁▄▁▁▁▁▅█████████▅█▅▆▄▄▁▅▁▄▄ ▇
  6.82 ms      Histogram: log(frequency) by time      8.35 ms <

 Memory estimate: 78.22 KiB, allocs estimate: 4.
```
