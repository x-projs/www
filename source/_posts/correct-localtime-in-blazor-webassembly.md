---
title: Correct Local Time In Blazor Webassembly
date: 2020-08-02 05:04:54
tags:
    - wasm
    - webassembly
    - blazor
    - blazor-wasm
---

Blazor webassembly has a bug, it always use UTC time as your local timezone. So no matter what the timezone you are in, `.ToLocalTime()` will always returning UTC time to you.

In order to show the correct local time to the user, we need manually adjust the time based on the timezone offset which we can get from javascript. The code looks like this:

``` csharp
    public class Program
    {
        public static async Task Main(string[] args)
        {
            var builder = WebAssemblyHostBuilder.CreateDefault(args);
            ...
            var host = builder.Build();
            ...
            DateTimeExtension.sTimezoneOffset = await host.Services.GetRequiredService<IJSRuntime>().InvokeAsync<int>("eval", "-new Date().getTimezoneOffset()");
        }
    }

    public static class DateTimeExtension
    {
        public static int sTimezoneOffset;

        public static DateTime ToRealLocalTime(this DateTime datetime)
        {
            return datetime.AddMinutes(sTimezoneOffset);
        }
    }
```

One thing is worth to mention, the javascript code `new Date().getTimezoneOffset()` returns `UTC - localtime` in minutes, so we use `-` before it to get the delta of `localtime - UTC` which can be directly used in `AddMinutes()`.
