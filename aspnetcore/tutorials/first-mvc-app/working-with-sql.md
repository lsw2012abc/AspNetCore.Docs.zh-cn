---
title: 第 5 部分，在 ASP.NET Core MVC 应用中使用数据库
author: rick-anderson
description: 第 5 部分，将模型添加到 ASP.NET Core MVC 应用
ms.author: riande
ms.date: 11/10/2020
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
uid: tutorials/first-mvc-app/working-with-sql
ms.openlocfilehash: c082457258346637499b8ea8000dd8792931112f
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "94422647"
---
# <a name="part-5-work-with-a-database-in-an-aspnet-core-mvc-app"></a>第 5 部分，在 ASP.NET Core MVC 应用中使用数据库

::: moniker range=">= aspnetcore-3.0"

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

`MvcMovieContext` 对象处理连接到数据库并将 `Movie` 对象映射到数据库记录的任务。 在 Startup.cs 文件的 `ConfigureServices` 方法中向[依赖关系注入](xref:fundamentals/dependency-injection)容器注册数据库上下文：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

ASP.NET Core [配置](xref:fundamentals/configuration/index)系统会读取 `ConnectionString`。 进行本地开发时，它从 *appsettings.json* 文件获取连接字符串：

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

ASP.NET Core [配置](xref:fundamentals/configuration/index)系统会读取 `ConnectionString`。 进行本地开发时，它从 *appsettings.json* 文件获取连接字符串：

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

当应用部署到测试服务器或生产服务器时，环境变量可用于将连接字符串设置为生产 SQL Server。 有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB 是轻型版的 SQL Server Express 数据库引擎，以程序开发为目标。 LocalDB 作为按需启动并在用户模式下运行的轻量级数据库没有复杂的配置。 默认情况下，LocalDB 数据库在 C:/Users/{user} 目录中创建 .mdf 文件 。

* 从“视图”菜单中，打开“SQL Server 对象资源管理器”(SSOX) 。

  ![“视图”菜单](working-with-sql/_static/ssox.png)

* 右键单击 `Movie` 表，然后单击“视图设计器”

  ![Movie 表上打开的上下文菜单](working-with-sql/_static/design.png)

  ![设计器中打开的 Movie 表](working-with-sql/_static/dv.png)

请注意 `ID` 旁边的密钥图标。 默认情况下，EF 将名为 `ID` 的属性设置为主键。

* 右键单击 `Movie` 表，然后单击“查看数据”

  ![Movie 表上打开的上下文菜单](working-with-sql/_static/ssox2.png)

  ![显示表数据的打开的 Movie 表](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a>设定数据库种子

在 Models 文件夹中创建一个名为 `SeedData` 的新类。 将生成的代码替换为以下代码：

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/SeedData.cs?name=snippet_1)]

如果 DB 中有任何电影，则会返回种子初始值设定项，并且不会添加任何电影。

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>添加种子初始值设定项

将 Program.cs 的内容替换为以下代码：

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Program.cs)]

测试应用

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 删除 DB 中的所有记录。 可以使用浏览器中的删除链接，也可从 SSOX 执行此操作。
* 强制应用初始化（调用 `Startup` 类中的方法），使种子方法能够正常运行。 若要强制进行初始化，必须先停止 IIS Express，然后再重新启动它。 可以使用以下任一方法来执行此操作：

  * 右键单击通知区域中的 IIS Express 系统任务栏图标，然后点击“退出”或“停止站点” 

    ![IIS Express 系统任务栏图标](working-with-sql/_static/iisExIcon.png)

    ![上下文菜单](working-with-sql/_static/stopIIS.png)

    * 如果是在非调试模式下运行 VS 的，请按 F5 以在调试模式下运行
    * 如果是在调试模式下运行 VS 的，请停止调试程序并按 F5

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

删除 DB 中的所有记录（使种子方法运行）。 停止并启动应用以设定数据库种子。

---

应用将显示设定为种子的数据。

![在 Microsoft Edge 中打开的显示电影数据的 MVC 电影应用程序](working-with-sql/_static/m55.png)

> [!div class="step-by-step"]
> [上一页](adding-model.md)
> [下一页](controller-methods-views.md)
::: moniker-end

::: moniker range="< aspnetcore-3.0"

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

`MvcMovieContext` 对象处理连接到数据库并将 `Movie` 对象映射到数据库记录的任务。 在 Startup.cs 文件的 `ConfigureServices` 方法中向[依赖关系注入](xref:fundamentals/dependency-injection)容器注册数据库上下文：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

ASP.NET Core [配置](xref:fundamentals/configuration/index)系统会读取 `ConnectionString`。 进行本地开发时，它从 *appsettings.json* 文件获取连接字符串：

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

ASP.NET Core [配置](xref:fundamentals/configuration/index)系统会读取 `ConnectionString`。 进行本地开发时，它从 *appsettings.json* 文件获取连接字符串：

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

将应用部署到测试或生产服务器时，可使用环境变量或另一种方法将连接字符串设置为实际的 SQL Server。 有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB 是轻型版的 SQL Server Express 数据库引擎，以程序开发为目标。 LocalDB 作为按需启动并在用户模式下运行的轻量级数据库没有复杂的配置。 默认情况下，LocalDB 数据库在 C:/Users/{user} 目录中创建 .mdf 文件 。

* 从“视图”菜单中，打开“SQL Server 对象资源管理器”(SSOX) 。

  ![“视图”菜单](working-with-sql/_static/ssox.png)

* 右键单击 `Movie` 表，然后单击“视图设计器”

  ![Movie 表上打开的上下文菜单](working-with-sql/_static/design.png)

  ![设计器中打开的 Movie 表](working-with-sql/_static/dv.png)

请注意 `ID` 旁边的密钥图标。 默认情况下，EF 将名为 `ID` 的属性设置为主键。

* 右键单击 `Movie` 表，然后单击“查看数据”

  ![Movie 表上打开的上下文菜单](working-with-sql/_static/ssox2.png)

  ![显示表数据的打开的 Movie 表](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a>设定数据库种子

在 Models 文件夹中创建一个名为 `SeedData` 的新类。 将生成的代码替换为以下代码：

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/SeedData.cs?name=snippet_1)]

如果 DB 中有任何电影，则会返回种子初始值设定项，并且不会添加任何电影。

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>添加种子初始值设定项

将 Program.cs 的内容替换为以下代码：

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Program.cs)]

测试应用

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 删除 DB 中的所有记录。 可以使用浏览器中的删除链接，也可从 SSOX 执行此操作。
* 强制应用初始化（调用 `Startup` 类中的方法），使种子方法能够正常运行。 若要强制进行初始化，必须先停止 IIS Express，然后再重新启动它。 可以使用以下任一方法来执行此操作：

  * 右键单击通知区域中的 IIS Express 系统任务栏图标，然后点击“退出”或“停止站点” 

    ![IIS Express 系统任务栏图标](working-with-sql/_static/iisExIcon.png)

    ![上下文菜单](working-with-sql/_static/stopIIS.png)

    * 如果是在非调试模式下运行 VS 的，请按 F5 以在调试模式下运行
    * 如果是在调试模式下运行 VS 的，请停止调试程序并按 F5

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

删除 DB 中的所有记录（使种子方法运行）。 停止并启动应用以设定数据库种子。

---

应用将显示设定为种子的数据。

![在 Microsoft Edge 中打开的显示电影数据的 MVC 电影应用程序](working-with-sql/_static/m55_mac.png)

> [!div class="step-by-step"]
> [上一页](adding-model.md)
> [下一页](controller-methods-views.md)

::: moniker-end
