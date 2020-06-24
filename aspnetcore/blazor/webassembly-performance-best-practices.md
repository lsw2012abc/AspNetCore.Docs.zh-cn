---
title: ASP.NET Core Blazor WebAssembly 性能最佳做法
author: pranavkm
description: 用于提高 ASP.NET Core Blazor WebAssembly 应用性能并避免常见性能问题的提示。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/08/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: c5169231eec67a43830f761bff7585deff774613
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103212"
---
# <a name="aspnet-core-blazor-webassembly-performance-best-practices"></a><span data-ttu-id="003c3-103">ASP.NET Core Blazor WebAssembly 性能最佳做法</span><span class="sxs-lookup"><span data-stu-id="003c3-103">ASP.NET Core Blazor WebAssembly performance best practices</span></span>

<span data-ttu-id="003c3-104">作者：[Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="003c3-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="003c3-105">本文提供了 ASP.NET Core Blazor WebAssembly 性能最佳做法的准则。</span><span class="sxs-lookup"><span data-stu-id="003c3-105">This article provides guidelines for ASP.NET Core Blazor WebAssembly performance best practices.</span></span>

## <a name="avoid-unnecessary-component-renders"></a><span data-ttu-id="003c3-106">避免不必要的组件呈现</span><span class="sxs-lookup"><span data-stu-id="003c3-106">Avoid unnecessary component renders</span></span>

<span data-ttu-id="003c3-107">借助 Blazor 的差分算法，当算法感知到组件未更改时，不用重新呈现组件。</span><span class="sxs-lookup"><span data-stu-id="003c3-107">Blazor's diffing algorithm avoids rerendering a component when the algorithm perceives that the component hasn't changed.</span></span> <span data-ttu-id="003c3-108">可重写 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> 来实现对组件呈现的精细控制。</span><span class="sxs-lookup"><span data-stu-id="003c3-108">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> for fine-grained control over component rendering.</span></span>

<span data-ttu-id="003c3-109">如果创作了一个仅限 UI 的组件，且该组件在最初呈现后从未更改，则请将 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 配置为返回 `false`：</span><span class="sxs-lookup"><span data-stu-id="003c3-109">If authoring a UI-only component that never changes after the initial render, configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to return `false`:</span></span>

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

<span data-ttu-id="003c3-110">大多数应用不需要精细控制，但是 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 也可用于选择性地呈现响应 UI 事件的组件。</span><span class="sxs-lookup"><span data-stu-id="003c3-110">Most apps don't require fine-grained control, but <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> can also be used to selectively render a component responding to a UI event.</span></span>

<span data-ttu-id="003c3-111">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="003c3-111">In the following example:</span></span>

* <span data-ttu-id="003c3-112"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 被重写并设置为 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 字段的值，该字段最初在组件加载时为 `false`。</span><span class="sxs-lookup"><span data-stu-id="003c3-112"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden and set to the value of the <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> field, which is initially `false` when the component loads.</span></span>
* <span data-ttu-id="003c3-113">选中该按钮后，<xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 设置为 `true`，这将强制组件重新呈现并显示更新后的 `currentCount`。</span><span class="sxs-lookup"><span data-stu-id="003c3-113">When the button is selected, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is set to `true`, which forces the component to rerender with the updated `currentCount`.</span></span>
* <span data-ttu-id="003c3-114">重新呈现后，<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 立即将 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 的值设置回 `false`，以防止在下次选中该按钮之前进一步重新呈现。</span><span class="sxs-lookup"><span data-stu-id="003c3-114">Immediately after rerendering, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> sets the value of <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> back to `false` to prevent further rerendering until the next time the button is selected.</span></span>

```razor
<p>Current count: @currentCount</p>

<button @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;
    private bool shouldRender;

    protected override bool ShouldRender() => shouldRender;

    protected override void OnAfterRender(bool first)
    {
        shouldRender = false;
    }

    private void IncrementCount()
    {
        currentCount++;
        shouldRender = true;
    }
}
```

<span data-ttu-id="003c3-115">有关详细信息，请参阅 <xref:blazor/components/lifecycle#after-component-render>。</span><span class="sxs-lookup"><span data-stu-id="003c3-115">For more information, see <xref:blazor/components/lifecycle#after-component-render>.</span></span>

## <a name="virtualize-re-usable-fragments"></a><span data-ttu-id="003c3-116">虚拟化可重用的片段</span><span class="sxs-lookup"><span data-stu-id="003c3-116">Virtualize re-usable fragments</span></span>

<span data-ttu-id="003c3-117">组件提供了一种方便的方法来生成代码和标记的可重用片段。</span><span class="sxs-lookup"><span data-stu-id="003c3-117">Components offer a convenient approach to produce re-usable fragments of code and markup.</span></span> <span data-ttu-id="003c3-118">通常，我们建议创作最符合应用要求的单个组件。</span><span class="sxs-lookup"><span data-stu-id="003c3-118">In general, we recommend authoring individual components that best align with the app's requirements.</span></span> <span data-ttu-id="003c3-119">需要注意的是，每个附加的子组件都会增加呈现父组件所需的总时间。</span><span class="sxs-lookup"><span data-stu-id="003c3-119">One caveat is that each additional child component contributes to the total time it takes to render a parent component.</span></span> <span data-ttu-id="003c3-120">对于大多数应用，额外的开销可以忽略不计。</span><span class="sxs-lookup"><span data-stu-id="003c3-120">For most apps, the additional overhead is negligible.</span></span> <span data-ttu-id="003c3-121">生成大量组件的应用应考虑使用策略来减少处理开销，例如限制所呈现的组件的数量。</span><span class="sxs-lookup"><span data-stu-id="003c3-121">Apps that produce a large number of components should consider using strategies to reduce processing overhead, such as limiting the number of rendered components.</span></span>

<span data-ttu-id="003c3-122">例如，如果某网格或列表要呈现数百个包含组件的行，则该网格或列表呈现时会大量使用处理器。</span><span class="sxs-lookup"><span data-stu-id="003c3-122">For example, a grid or list that renders hundreds of rows containing components is processor intensive to render.</span></span> <span data-ttu-id="003c3-123">请考虑将网格或列表布局虚拟化，以便在任何给定时间都只呈现其中的一部分组件。</span><span class="sxs-lookup"><span data-stu-id="003c3-123">Consider virtualizing a grid or list layout so that only a subset of the components is rendered at any given time.</span></span> <span data-ttu-id="003c3-124">有关组件子集呈现的示例，请参阅[虚拟化示例应用（aspnet/示例 GitHub 存储库）](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization)中的以下组件：</span><span class="sxs-lookup"><span data-stu-id="003c3-124">For an example of component subset rendering, see the following components in the [Virtualization sample app (aspnet/samples GitHub repository)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span></span>

* <span data-ttu-id="003c3-125">`Virtualize` 组件（[共享/Virtualize.razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)）：用 C# 语言编写的一种组件，实现了 <xref:Microsoft.AspNetCore.Components.ComponentBase> 来根据用户滚动呈现一组天气数据行。</span><span class="sxs-lookup"><span data-stu-id="003c3-125">`Virtualize` component ([Shared/Virtualize.razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): A component written in C# that implements <xref:Microsoft.AspNetCore.Components.ComponentBase> to render a set of weather data rows based on user scrolling.</span></span>
* <span data-ttu-id="003c3-126">`FetchData` 组件（[页面/FetchData.razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)）：使用 `Virtualize` 组件一次显示 25 行天气数据。</span><span class="sxs-lookup"><span data-stu-id="003c3-126">`FetchData` component ([Pages/FetchData.razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): Uses the `Virtualize` component to display 25 rows of weather data at a time.</span></span>

## <a name="avoid-javascript-interop-to-marshal-data"></a><span data-ttu-id="003c3-127">不要用 JavaScript 互操作来封送数据</span><span class="sxs-lookup"><span data-stu-id="003c3-127">Avoid JavaScript interop to marshal data</span></span>

<span data-ttu-id="003c3-128">在 Blazor WebAssembly 中，JavaScript (JS) 互操作调用必须遍历 WebAssembly-JS 边界。</span><span class="sxs-lookup"><span data-stu-id="003c3-128">In Blazor WebAssembly, a JavaScript (JS) interop call must traverse the WebAssembly-JS boundary.</span></span> <span data-ttu-id="003c3-129">如果跨两个上下文序列化和反序列化内容，会产生应用处理开销。</span><span class="sxs-lookup"><span data-stu-id="003c3-129">Serializing and deserializing content across the two contexts creates processing overhead for the app.</span></span> <span data-ttu-id="003c3-130">频繁的 JS 互操作调用通常会对性能产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="003c3-130">Frequent JS interop calls often adversely affects performance.</span></span> <span data-ttu-id="003c3-131">为了减少数据的跨边界封送，请确定应用能否将许多小的有效负载合并到一个大的有效负载中，以避免在 WebAssembly 与 JS 之间频繁切换上下文。</span><span class="sxs-lookup"><span data-stu-id="003c3-131">To reduce the marshalling of data across the boundary, determine if the app can consolidate many small payloads into a single large payload to avoid the high volume of context switching between WebAssembly and JS.</span></span>

## <a name="use-systemtextjson"></a><span data-ttu-id="003c3-132">使用 System.Text.Json</span><span class="sxs-lookup"><span data-stu-id="003c3-132">Use System.Text.Json</span></span>

Blazor<span data-ttu-id="003c3-133"> 的 JS 互操作实现依赖于 <xref:System.Text.Json> - 这是一个性能高但内存分配较低的 JSON 序列化库。</span><span class="sxs-lookup"><span data-stu-id="003c3-133">'s JS interop implementation relies on <xref:System.Text.Json>, which is a high-performance JSON serialization library with low memory allocation.</span></span> <span data-ttu-id="003c3-134">与添加一个或多个备用 JSON 库相比，使用 <xref:System.Text.Json> 不会增加应用有效负载的大小。</span><span class="sxs-lookup"><span data-stu-id="003c3-134">Using <xref:System.Text.Json> doesn't result in additional app payload size over adding one or more alternate JSON libraries.</span></span>

<span data-ttu-id="003c3-135">有关迁移指南，请参阅[如何从 Newtonsoft.Json 迁移到 System.Text.Json](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to)。</span><span class="sxs-lookup"><span data-stu-id="003c3-135">For migration guidance, see [How to migrate from Newtonsoft.Json to System.Text.Json](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span></span>

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a><span data-ttu-id="003c3-136">根据需要使用同步的和未封装的 JS 互操作 API</span><span class="sxs-lookup"><span data-stu-id="003c3-136">Use synchronous and unmarshalled JS interop APIs where appropriate</span></span>

Blazor<span data-ttu-id="003c3-137"> WebAssembly 额外提供了两个 <xref:Microsoft.JSInterop.IJSRuntime> 版本，而 Blazor 服务器应用只有一个版本：</span><span class="sxs-lookup"><span data-stu-id="003c3-137"> WebAssembly offers two additional versions of <xref:Microsoft.JSInterop.IJSRuntime> over the single version available to Blazor Server apps:</span></span>

* <span data-ttu-id="003c3-138"><xref:Microsoft.JSInterop.IJSInProcessRuntime> 允许同步调用 JS 互操作调用，其开销低于异步版本：</span><span class="sxs-lookup"><span data-stu-id="003c3-138"><xref:Microsoft.JSInterop.IJSInProcessRuntime> allows invoking JS interop calls synchronously, which has less overhead than the asynchronous versions:</span></span>

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (IJSInProcessRuntime)JS;

          var value = jsInProcess.Invoke<string>("jsInteropCall");
      }
  }
  ```

* <span data-ttu-id="003c3-139"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> 允许使用未封装的 JS 互操作调用：</span><span class="sxs-lookup"><span data-stu-id="003c3-139"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> permits unmarshalled JS interop calls:</span></span>

  ```javascript
  function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
  }
  ```

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (WebAssemblyJSRuntime)JS;

          var value = jsInProcess.InvokeUnmarshalled<string>("jsInteropCall");
      }
  }
  ```

  > [!WARNING]
  > <span data-ttu-id="003c3-140">虽然使用 <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> 这种 JS 互操作方法的开销最小，但与这些 API 交互所需的 JavaScript API 目前没有文档记录，而且可能会在将来的版本中出现中断性变更。</span><span class="sxs-lookup"><span data-stu-id="003c3-140">While using <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> has the least overhead of the JS interop approaches, the JavaScript APIs required to interact with these APIs are currently undocumented and subject to breaking changes in future releases.</span></span>

## <a name="reduce-app-size"></a><span data-ttu-id="003c3-141">减小应用大小</span><span class="sxs-lookup"><span data-stu-id="003c3-141">Reduce app size</span></span>

### <a name="intermediate-language-il-linking"></a><span data-ttu-id="003c3-142">中间语言 (IL) 链接</span><span class="sxs-lookup"><span data-stu-id="003c3-142">Intermediate Language (IL) linking</span></span>

<span data-ttu-id="003c3-143">通过[链接 Blazor WebAssembly 应用](xref:blazor/host-and-deploy/configure-linker)，可剪裁应用二进制文件中未使用的代码来减小应用的大小。</span><span class="sxs-lookup"><span data-stu-id="003c3-143">[Linking a Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-linker) reduces the app's size by trimming unused code in the app's binaries.</span></span> <span data-ttu-id="003c3-144">默认情况下，仅在 `Release` 配置中生成时才启用链接器。</span><span class="sxs-lookup"><span data-stu-id="003c3-144">By default, the linker is only enabled when building in `Release` configuration.</span></span> <span data-ttu-id="003c3-145">要从此中受益，请使用 [dotnet publish](/dotnet/core/tools/dotnet-publish) 命令发布应用用于部署，并将 [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) 选项设置为 `Release`：</span><span class="sxs-lookup"><span data-stu-id="003c3-145">To benefit from this, publish the app for deployment using the [dotnet publish](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

```dotnetcli
dotnet publish -c Release
```

### <a name="compression"></a><span data-ttu-id="003c3-146">压缩</span><span class="sxs-lookup"><span data-stu-id="003c3-146">Compression</span></span>

<span data-ttu-id="003c3-147">发布 Blazor WebAssembly 应用时，将在发布过程中对输出内容进行静态压缩，从而减小应用的大小，并免去运行时压缩的开销。</span><span class="sxs-lookup"><span data-stu-id="003c3-147">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> Blazor<span data-ttu-id="003c3-148"> 依赖服务器来执行内容协商和提供静态压缩的文件。</span><span class="sxs-lookup"><span data-stu-id="003c3-148"> relies on the server to perform content negotation and serve statically-compressed files.</span></span>

<span data-ttu-id="003c3-149">部署应用后，请验证该应用是否提供压缩的文件。</span><span class="sxs-lookup"><span data-stu-id="003c3-149">After an app is deployed, verify that the app serves compressed files.</span></span> <span data-ttu-id="003c3-150">检查浏览器开发人员工具中的“网络”选项卡，并验证文件是否具有 `Content-Encoding: br` 或 `Content-Encoding: gz`。</span><span class="sxs-lookup"><span data-stu-id="003c3-150">Inspect the Network tab in a browser's Developer Tools and verify that the files are served with `Content-Encoding: br` or `Content-Encoding: gz`.</span></span> <span data-ttu-id="003c3-151">如果主机未提供压缩的文件，请按照 <xref:blazor/host-and-deploy/webassembly#compression> 中的说明操作。</span><span class="sxs-lookup"><span data-stu-id="003c3-151">If the host isn't serving compressed files, follow the instructions in <xref:blazor/host-and-deploy/webassembly#compression>.</span></span>

### <a name="disable-unused-features"></a><span data-ttu-id="003c3-152">禁用未使用的功能</span><span class="sxs-lookup"><span data-stu-id="003c3-152">Disable unused features</span></span>

Blazor<span data-ttu-id="003c3-153"> WebAssembly 的运行时包含以下 .NET 功能；如果应用不需要这些功能就能减少有效负载的大小，可将它们禁用：</span><span class="sxs-lookup"><span data-stu-id="003c3-153"> WebAssembly's runtime includes the following .NET features that can be disabled if the app doesn't require them for a smaller payload size:</span></span>

* <span data-ttu-id="003c3-154">包含数据文件来确保时区信息正确。</span><span class="sxs-lookup"><span data-stu-id="003c3-154">A data file is included to make timezone information correct.</span></span> <span data-ttu-id="003c3-155">如果应用不需要此功能，请考虑通过将应用项目文件中的 `BlazorEnableTimeZoneSupport` MSBuild 属性设置为 `false` 来禁用它：</span><span class="sxs-lookup"><span data-stu-id="003c3-155">If the app doesn't require this feature, consider disabling it by setting the `BlazorEnableTimeZoneSupport` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

* <span data-ttu-id="003c3-156">包括排序规则信息来确保 <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> 之类的 API 正常工作。</span><span class="sxs-lookup"><span data-stu-id="003c3-156">Collation information is included to make APIs such as <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> work correctly.</span></span> <span data-ttu-id="003c3-157">如果确定应用不需要排序规则数据，请考虑通过将应用项目文件中的 `BlazorWebAssemblyPreserveCollationData` MSBuild 属性设置为 `false` 来禁用它：</span><span class="sxs-lookup"><span data-stu-id="003c3-157">If you're certain that the app doesn't require the collation data, consider disabling it by setting the `BlazorWebAssemblyPreserveCollationData` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```