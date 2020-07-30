---
title: Bind Unsupported Dom Event in Blazor
date: 2020-07-30 07:04:07
tags:
    - blazor
    - blazor webassembly
---

When user uses mouse middle button to click the HTLM dom element, chrome doesn't fire `onclick` event.
Instead, it fires `onauxclick` event. In blazor, we normally bind `onclick` event like this:

``` csharp
    <a href="..." target="_blank" @onclick="Click">
        ...
    </a>

    @code
    {
        public void Click()
        {
            ...
        }
    }
```

But you can't do this for `onauxclick` event. It will throw the following exception in runtime:

```
blazor.webassembly.js:1 crit: Microsoft.AspNetCore.Components.WebAssembly.Rendering.WebAssemblyRenderer[100]
      Unhandled exception rendering component: Failed to execute 'setAttribute' on 'Element': '@onauxclick' is not a valid attribute name.
      Error: Failed to execute 'setAttribute' on 'Element': '@onauxclick' is not a valid attribute name.
          at e.applyAttribute (https://localhost/_framework/blazor.webassembly.js:1:17247)
          at e.insertElement (https://localhost/_framework/blazor.webassembly.js:1:16358)
          at e.insertFrame (https://localhost/_framework/blazor.webassembly.js:1:15235)
          at e.applyEdits (https://localhost/_framework/blazor.webassembly.js:1:13621)
          at e.updateComponent (https://localhost/_framework/blazor.webassembly.js:1:12937)
          at Object.t.renderBatch (https://localhost/_framework/blazor.webassembly.js:1:4622)
          at Object.window.Blazor._internal.renderBatch (https://localhost/_framework/blazor.webassembly.js:1:34907)
          at _mono_wasm_invoke_js_unmarshalled (https://localhost/_framework/dotnet.5.0.0-preview.7.20364.11.js:1:165464)
          at wasm_invoke_iiiiii (https://localhost/_framework/dotnet.wasm:wasm-function[2795]:0x84169)
          at icall_trampoline_dispatch (https://localhost/_framework/dotnet.wasm:wasm-function[4931]:0xd58a2)
```

From the exception, we know that, in this version of blazor, `onauxclick` is not supported. So you need bind it like this:

``` csharp
    <a href="..." target="_blank" onauxclick="@((Action)(() => Click()))">
        ...
    </a>

    @code
    {
        public void Click()
        {
            ...
        }
    }
```

This way can also be used to bind other events which are not supported natively by blazor.
