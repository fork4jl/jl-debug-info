# Run full `LinearAlgebra` tests

## System info

```jl
julia> versioninfo(verbose=true)
Julia Version 1.11.0-DEV.4
Commit 3690639a09* (2023-07-05 09:04 UTC)
Platform Info:
  OS: Linux (x86_64-linux-gnu)
      Ubuntu 22.04.2 LTS
  uname: Linux 5.19.0-46-generic #47~22.04.1-Ubuntu SMP PREEMPT_DYNAMIC Wed Jun 21 15:35:31 UTC 2 x86_64 x86_64
  CPU: Intel(R) Core(TM) i7-10700 CPU @ 2.90GHz:
                 speed         user         nice          sys         idle          irq
       #1-16  4700 MHz        219 s         20 s        157 s      37337 s          0 s
  Memory: 15.363094329833984 GB (14078.0234375 MB free)
  Uptime: 236.44 sec
  Load Avg:  0.03  0.08  0.03
  WORD_SIZE: 64
  LIBM: libopenlibm
  LLVM: libLLVM-15.0.7 (ORCJIT, skylake)
  Threads: 1 on 16 virtual cores
```

+ 16 CPU theard
+ 16 GiB mem + 8 GiB swap


## patch

- none!


## test step

1. `reboot` and start test.
2. run test
```sh
$ cd julia/
$ env JULIA_CPU_THREADS=16  make -C test/ LinearAlgebra
```
1. modify `JULIA_CPU_THREADS`
2. run test again
