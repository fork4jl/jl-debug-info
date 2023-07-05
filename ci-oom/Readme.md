
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

```
$ free -h
               total        used        free      shared  buff/cache   available
Mem:            15Gi       975Mi        12Gi       198Mi       1.7Gi        13Gi
Swap:          8.0Gi          0B       8.0Gi
```


## patch

```diff
diff --git a/test/runtests.jl b/test/runtests.jl
index 1264acae98..33cb5fd4c8 100644
--- a/test/runtests.jl
+++ b/test/runtests.jl
@@ -109,7 +109,7 @@ cd(@__DIR__) do
     # multiple worker processes regardless of the value of `net_on`.
     # Otherwise, we use multiple worker processes if and only if `net_on` is true.
     if net_on || JULIA_TEST_USE_MULTIPLE_WORKERS
-        n = min(Sys.CPU_THREADS, length(tests))
+        n = min(Sys.CPU_THREADS, length(tests),2)
         n > 1 && addprocs_with_testenv(n)
         LinearAlgebra.BLAS.set_num_threads(1)
     end
@@ -169,6 +169,12 @@ cd(@__DIR__) do
             rss_str = @sprintf("%5.2f", resp[6] / 2^20)
             printstyled(lpad(rss_str, rss_align, " "), "\n", color=:white)
         finally
+            # XXX: add gc
+            GC.enable_logging(true)
+            GC.gc(false)
+            GC.gc(true)
+            GC.enable_logging(false)
+
             unlock(print_lock)
         end
         nothing
@@ -211,6 +217,8 @@ cd(@__DIR__) do


     all_tests = [tests; node1_tests]
+    @show tests
+    @show node1_tests

     local stdin_monitor
     all_tasks = Task[]
```


## test step

1. `reboot` and start test.
2. run test
```sh
$ cd julia/test
$ ../julia  --check-bounds=yes --startup-file=no --depwarn=error ./runtests.jl  LinearAlgebra -LinearAlgebra/triangular -LinearAlgebra/bidiag -LinearAlgebra/matmul -LinearAlgebra/addmul
```
3. modify `nworkers`: `n = min(Sys.CPU_THREADS, length(tests),2)`
4. run test again
