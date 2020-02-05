---
title: ASP.NET Core Web SDK
author: Rick-Anderson
description: Microsoft 的概述。
ms.author: riande
ms.date: 01/25/2020
no-loc:
- Blazor
uid: razor-pages/web-sdk
ms.openlocfilehash: 6a9d531efd2188aed525c949bb124914c31119db
ms.sourcegitcommit: fe41cff0b99f3920b727286944e5b652ca301640
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76869761"
---
# <a name="aspnet-core-web-sdk"></a><span data-ttu-id="86243-103">ASP.NET Core Web SDK</span><span class="sxs-lookup"><span data-stu-id="86243-103">ASP.NET Core Web SDK</span></span>

### <a name="overview"></a><span data-ttu-id="86243-104">概述</span><span class="sxs-lookup"><span data-stu-id="86243-104">Overview</span></span>

<span data-ttu-id="86243-105">`Microsoft.NET.Sdk.Web` 是用于生成 ASP.NET Core 应用的[MSBuild 项目 SDK](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) 。</span><span class="sxs-lookup"><span data-stu-id="86243-105">`Microsoft.NET.Sdk.Web` is an [MSBuild project SDK](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) for building ASP.NET Core apps.</span></span> <span data-ttu-id="86243-106">无需此 SDK 即可构建 ASP.NET Core 应用程序，但 Web SDK 如下：</span><span class="sxs-lookup"><span data-stu-id="86243-106">It's possible to build an ASP.NET Core app without this SDK, however, the Web SDK is:</span></span>

* <span data-ttu-id="86243-107">旨在提供一流的体验。</span><span class="sxs-lookup"><span data-stu-id="86243-107">Tailored towards providing a first-class experience.</span></span>
* <span data-ttu-id="86243-108">适用于大多数用户的建议目标。</span><span class="sxs-lookup"><span data-stu-id="86243-108">The recommended target for most users.</span></span>

<span data-ttu-id="86243-109">在项目中使用 Web SDK：</span><span class="sxs-lookup"><span data-stu-id="86243-109">Use the Web.SDK in a project:</span></span>

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

<span data-ttu-id="86243-110">使用 Web SDK 启用的功能：</span><span class="sxs-lookup"><span data-stu-id="86243-110">Features enabled by using the Web SDK:</span></span>

* <span data-ttu-id="86243-111">面向 .NET Core 3.0 或更高版本的项目隐式引用：</span><span class="sxs-lookup"><span data-stu-id="86243-111">Projects targeting .NET Core 3.0 or later implicitly reference:</span></span>

  * <span data-ttu-id="86243-112">[ASP.NET Core 共享框架](xref:fundamentals/metapackage-app)。</span><span class="sxs-lookup"><span data-stu-id="86243-112">The [ASP.NET Core shared framework](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="86243-113">设计用于构建 ASP.NET Core 应用程序的[分析器](/visualstudio/extensibility/getting-started-with-roslyn-analyzers)。</span><span class="sxs-lookup"><span data-stu-id="86243-113">[Analyzers](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) designed for building ASP.NET Core apps.</span></span>
* <span data-ttu-id="86243-114">Web SDK 导入 MSBuild 目标，这些目标允许使用发布配置文件并使用 WebDeploy 进行发布。</span><span class="sxs-lookup"><span data-stu-id="86243-114">The Web SDK imports MSBuild targets that enable the use of publish profiles and publishing using WebDeploy.</span></span>

### <a name="properties"></a><span data-ttu-id="86243-115">属性</span><span class="sxs-lookup"><span data-stu-id="86243-115">Properties</span></span>

| <span data-ttu-id="86243-116">Property</span><span class="sxs-lookup"><span data-stu-id="86243-116">Property</span></span> | <span data-ttu-id="86243-117">描述</span><span class="sxs-lookup"><span data-stu-id="86243-117">Description</span></span> |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | <span data-ttu-id="86243-118">禁用对 `Microsoft.AspNetCore.App` 共享框架的隐式引用。</span><span class="sxs-lookup"><span data-stu-id="86243-118">Disables implicit reference to the `Microsoft.AspNetCore.App` shared framework.</span></span> |
| `DisableImplicitAspNetCoreAnalyzers` | <span data-ttu-id="86243-119">禁用对 ASP.NET Core 分析器的隐式引用。</span><span class="sxs-lookup"><span data-stu-id="86243-119">Disables implicit reference to ASP.NET Core analyzers.</span></span> |
| `DisableImplicitComponentsAnalyzers` | <span data-ttu-id="86243-120">生成 Blazor （服务器）应用程序时禁用对 Razor 组件分析器的隐式引用。</span><span class="sxs-lookup"><span data-stu-id="86243-120">Disables implicit reference to Razor Components analyzers when building Blazor (server) applications.</span></span> |