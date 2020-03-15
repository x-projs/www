---
title: Linux Kernel Bug For HyperV
date: 2020-03-14 21:40:10
tags: linux
---

Today, I played my [new version Linux kernel 5.5.9](create-wsl-distribution.md) on WSL2. Quickly, I hit a very interesting
bug. Please see the following code

```cpp
#include <thread>
#include <chrono>
using namespace std::literals::chrono_literals;

int main() {
    auto start = std::chrono::high_resolution_clock::now();
    while (std::chrono::high_resolution_clock::now() - start < 1s);
    return 0;
}
```

If you run the above code, you would get the following result:

    xyb@ubuntu:~$ time gcc a.c
    xyb@ubuntu:~$ time ./a.out

    real    0m1.011s
    user    0m0.010s <-- This is wrong!
    sys     0m0.000s

The user time is just `0.010s`. It's a wrong value obviously. After a lot of investigation, I found it might be a bug in the latest Linux kernel.
In linux, [the unit of `shced_clock()` should be nano seconds](https://github.com/torvalds/linux/blob/d3dca69085e94e52a1d61a34b8e5f73a9f3d7eed/kernel/sched/clock.c#L60), but in `hyperv_timer.c`, the unit, I believe, is 100 nano seconds.

I made a fix here: https://github.com/xieyubo/WSL2-Linux-Kernel/commit/85fc62846acb12a06b18f6e60bba849315f51d86.
After this fix, re-run the above application:

    xyb@ubuntu:~$ time ./a.out

    real    0m1.003s
    user    0m0.999s
    sys     0m0.004s

The value looks right now.
