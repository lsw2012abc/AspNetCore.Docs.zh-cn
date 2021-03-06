---
title: 使用身份验证库保护 ASP.NET Core Blazor WebAssembly 独立应用
author: guardrex
description: 了解如何使用身份验证库保护 ASP.NET Core Blazor WebAssembly 独立应用。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2021
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/standalone-with-authentication-library
ms.openlocfilehash: a198606caf55232c221f1d1f1224918d3f87f04c
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280892"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a>使用身份验证库保护 ASP.NET Core Blazor WebAssembly 独立应用

对于 Azure Active Directory (AAD) 和 Azure Active Directory B2C (AAD B2C)，请勿按照本主题中的指南进行操作。请参阅此目录节点中的 AAD 和 AAD B2C 主题。

若要创建使用 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 库的[独立 Blazor WebAssembly 应用](xref:blazor/hosting-models#blazor-webassembly)，请按照适用于所选工具的指南操作。 如果添加对身份验证的支持，请参阅本文中的以下部分，了解有关设置和配置应用的指南。

> [!NOTE]
> Identity 提供程序 (IP) 必须使用 [OpenID Connect (OIDC)](https://openid.net/connect/)。 例如，Facebook 的 IP 不是符合 OIDC 的提供程序，因此本主题中的指南不适用于 Facebook IP。 有关详细信息，请参阅 <xref:blazor/security/webassembly/index#authentication-library>。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

新建具有身份验证机制的 Blazor WebAssembly 项目：

1. 在“新建 ASP.NET Core Web 应用”对话框中选择“Blazor WebAssembly应用”模板后，选择“身份验证”下的“更改”。

1. 通过“存储应用内的用户帐户”选项选择“单个用户帐户”，以使用 ASP.NET Core 的 [Identity](xref:security/authentication/identity) 系统。  此选择将添加身份验证支持，并且最终不会将用户存储在数据库中。 本文的以下部分提供了更多详细信息。

# <a name="visual-studio-code--net-core-cli"></a>[Visual Studio Code/.NET Core CLI](#tab/visual-studio-code+netcore-cli)

在空文件夹中新建具有身份验证机制的 Blazor WebAssembly项目。 通过 `-au|--auth` 选项指定 `Individual` 身份验证机制，以使用 ASP.NET Core 的 [Identity](xref:security/authentication/identity) 系统。 此选择将添加身份验证支持，并且最终不会将用户存储在数据库中。 本文的以下部分提供了更多详细信息。

```dotnetcli
dotnet new blazorwasm -au Individual -o {APP NAME}
```

| 占位符  | 示例        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

使用 `-o|--output` 选项指定的输出位置将创建一个项目文件夹（如果该文件夹不存在）并成为应用程序名称的一部分。

有关详细信息，请参阅 .NET Core 指南中的 [`dotnet new`](/dotnet/core/tools/dotnet-new) 命令。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

新建具有身份验证机制的 Blazor WebAssembly 项目：

1. 在“配置新的 Blazor WebAssembly应用”步骤中，从“身份验证”下拉列表中选择“个人身份验证(应用内)”。

1. 创建应用以使用 ASP.NET Core [Identity](xref:security/authentication/identity)，这样便不会将用户存储在数据库中。 本文的以下部分提供了更多详细信息。

---

## <a name="authentication-package"></a>身份验证包

创建应用以使用个人用户帐户时，该应用会在其项目文件中自动接收 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 包的包引用。 此包提供了一组基元，可帮助应用验证用户身份并获取令牌以调用受保护的 API。

如果向应用添加身份验证，请手动将包添加到应用的项目文件中：

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

对于占位符 `{VERSION}`，可在包的版本历史记录（位于 [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication)）中找到与应用的共享框架版本匹配的最新稳定版本的包。

## <a name="authentication-service-support"></a>身份验证服务支持

使用由 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 包提供的 <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> 扩展方法在服务容器中注册用户身份验证支持。 此方法设置应用与 Identity 提供者 (IP) 交互所需的服务。

对于新应用，请为以下配置中的 `{AUTHORITY}` 和 `{CLIENT ID}` 占位符提供值。 提供与应用的 IP 一起使用所需的其他配置值。 例如用于 Google 的相关值，它需要 `PostLogoutRedirectUri`、`RedirectUri` 和 `ResponseType`。 如果向应用添加身份验证，请使用占位符值和其他配置值将以下代码和配置手动添加到应用中。

`Program.cs`:

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

配置由 `wwwroot/appsettings.json` 文件提供：

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

Google OAuth 2.0 OIDC 示例：

```json
{
  "Local": {
    "Authority": "https://accounts.google.com/",
    "ClientId": "2.......7-e.....................q.apps.googleusercontent.com",
    "PostLogoutRedirectUri": "https://localhost:5001/authentication/logout-callback",
    "RedirectUri": "https://localhost:5001/authentication/login-callback",
    "ResponseType": "id_token"
  }
}
```

重定向 URI (`https://localhost:5001/authentication/login-callback`) 在 [Google API 控制台](https://console.developers.google.com/apis/dashboard)的“凭据” > `{NAME}` > “授权重定向 URI” 中注册，其中 `{NAME}` 是 Google API 控制台的“OAuth 2.0 客户端 ID”应用列表中的应用客户端名称。

使用 OpenID Connect (OIDC) 提供对独立应用的身份验证支持。 <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> 方法接受回叫，以配置使用 OIDC 验证应用所需的参数。 可以从 OIDC 兼容的 IP 中获取配置应用所需的值。 注册应用时获取值，通常在其在线门户中执行此操作。

## <a name="access-token-scopes"></a>访问令牌作用域

Blazor WebAssembly 模板自动为 `openid` 和 `profile` 配置默认作用域。

Blazor WebAssembly 模板不会自动将应用配置为请求安全 API 的访问令牌。 若要将访问令牌预配为登录流的一部分，请将范围添加到 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions> 的默认令牌范围中。 如果向应用添加身份验证，请手动添加以下代码并配置范围 URI。

`Program.cs`:

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

有关详细信息，请参阅“其他方案”一文的以下部分：

* [请求其他访问令牌](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [将令牌附加到传出请求](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a>导入文件

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a>索引页

[!INCLUDE[](~/blazor/includes/security/index-page-authentication.md)]

## <a name="app-component"></a>应用组件

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a>RedirectToLogin 组件

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>LoginDisplay 组件

`LoginDisplay` 组件 (`Shared/LoginDisplay.razor`) 在 `MainLayout` 组件 (`Shared/MainLayout.razor`) 中呈现并管理以下行为：

* 对于经过身份验证的用户：
  * 显示当前用户名。
  * 提供用于注销应用的按钮。
* 对于匿名用户，提供登录选项。

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

## <a name="authentication-component"></a>身份验证组件

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a>其他资源

* <xref:blazor/security/webassembly/additional-scenarios>
* [使用安全默认客户端的应用中未经身份验证或未经授权的 Web API 请求](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:host-and-deploy/proxy-load-balancer>：包含有关以下内容的指导：
  * 使用转接头中间件跨代理服务器和内部网络保留 HTTPS 方案信息。
  * 其他方案和用例，包括手动方案配置、请求路径更改以进行正确请求路由，以及转发适用于 Linux 和非 IIS 反向代理的请求方案。
