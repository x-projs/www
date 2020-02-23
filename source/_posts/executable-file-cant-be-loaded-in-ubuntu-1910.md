---
title: Executable File Can't Be Loaded in Ubuntu 19.10
date: 2020-02-22 20:42:28
tags: linux
---

Today, I found an interesting thing, the following code works well under Ubuntu 18.04,
but failed under Ubuntu 19.10:

```cpp
#include <stdio.h>
#include <dlfcn.h>

int main(int argc, char* argv[]) {
    if (dlopen(argv[0], RTLD_NOW)) {
        printf("dlopen() success\n");
    } else {
        printf("dlopen() failed: %s\n", dlerror());
    }
    return 0;
}
```

Under Ubuntu 19.10 (gcc 9.2.1), it will output:

    dlopen() failed:./a.out: cannot dynamically load position-independent executable

After some investigation, I found it was caused by the behavior change in new glibc. In the new version
of glibc(>=2.30), it adds a check: if the file is position independent executable (PIE flag is set in
FLAGS_1 of Dynamic Secction in ELF structure), `dlopen()` will return failure. So the application can't
load itself anymore. I don't know what's the story at the back of this change. Security issue? Might be....

The workaround is quite straightforward, we only need clean up the `PIE` flag. Here is the sample code:

<script src="https://gist.github.com/xieyubo/6820491646f9d01980a7eadb16564ddf.js"></script>