---
title: :::no-loc(Razor):::与 ASP.NET Core 中的应用程序部件共享控制器、视图和页面等
author: rick-anderson
description: :::no-loc(Razor):::与 ASP.NET Core 中的应用程序部件共享控制器、视图、页面及更多内容
ms.author: riande
ms.date: 11/11/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 33deb5ff794982e0c074186bb2abb88344e8a116
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061179"
---
# <a name="share-controllers-views-no-locrazor-pages-and-more-with-application-parts"></a><span data-ttu-id="0840d-103">与应用程序部件共享控制器、视图、 :::no-loc(Razor)::: 页和更多内容</span><span class="sxs-lookup"><span data-stu-id="0840d-103">Share controllers, views, :::no-loc(Razor)::: Pages and more with Application Parts</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0840d-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0840d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="0840d-105">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="0840d-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0840d-106">应用程序部件是对应用资源的抽象化。 </span><span class="sxs-lookup"><span data-stu-id="0840d-106">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="0840d-107">应用程序部件允许 ASP.NET Core 发现控制器、查看组件、标记帮助程序、 :::no-loc(Razor)::: 页面、razor 编译源等。</span><span class="sxs-lookup"><span data-stu-id="0840d-107">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, :::no-loc(Razor)::: Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="0840d-108"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> 是应用程序部件。</span><span class="sxs-lookup"><span data-stu-id="0840d-108"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> is an Application part.</span></span> <span data-ttu-id="0840d-109">`AssemblyPart` 用于封装程序集引用，并公开类型和编译引用。</span><span class="sxs-lookup"><span data-stu-id="0840d-109">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="0840d-110">[功能提供程序](#fp)使用应用程序部件填充 ASP.NET Core 应用的功能。</span><span class="sxs-lookup"><span data-stu-id="0840d-110">[Feature providers](#fp) work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="0840d-111">应用程序部件的主要用例是将应用配置为从程序集中发现（或避免加载）ASP.NET Core 功能。</span><span class="sxs-lookup"><span data-stu-id="0840d-111">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="0840d-112">例如，可能需要在多个应用之间共享通用功能。</span><span class="sxs-lookup"><span data-stu-id="0840d-112">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="0840d-113">使用应用程序部件，你可以使用多个应用共享包含控制器、视图、 :::no-loc(Razor)::: 页面、razor 编译源、标记帮助程序以及更多应用程序 (DLL) 的程序集。</span><span class="sxs-lookup"><span data-stu-id="0840d-113">Using Application Parts, you can share an assembly (DLL) containing controllers, views, :::no-loc(Razor)::: Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="0840d-114">相对于在多个项目中复制代码，首选共享程序集。</span><span class="sxs-lookup"><span data-stu-id="0840d-114">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="0840d-115">ASP.NET Core 应用从 <xref:System.Web.WebPages.ApplicationPart> 加载功能。</span><span class="sxs-lookup"><span data-stu-id="0840d-115">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="0840d-116"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> 类表示受程序集支持的应用程序部件。</span><span class="sxs-lookup"><span data-stu-id="0840d-116">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="0840d-117">加载 ASP.NET Core 功能</span><span class="sxs-lookup"><span data-stu-id="0840d-117">Load ASP.NET Core features</span></span>

<span data-ttu-id="0840d-118">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> 和 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> 类发现并加载 ASP.NET Core 功能（控制器、视图组件等）。</span><span class="sxs-lookup"><span data-stu-id="0840d-118">Use the <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> and <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="0840d-119"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> 跟踪可用的应用程序部件和功能提供程序。</span><span class="sxs-lookup"><span data-stu-id="0840d-119">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="0840d-120">在 `Startup.ConfigureServices` 中配置 `ApplicationPartManager`：</span><span class="sxs-lookup"><span data-stu-id="0840d-120">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="0840d-121">以下代码提供使用 `AssemblyPart` 配置 `ApplicationPartManager` 的可选方法：</span><span class="sxs-lookup"><span data-stu-id="0840d-121">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="0840d-122">前面的两个代码示例从程序集加载 `SharedController`。</span><span class="sxs-lookup"><span data-stu-id="0840d-122">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="0840d-123">`SharedController` 未在该应用的项目中。</span><span class="sxs-lookup"><span data-stu-id="0840d-123">The `SharedController` is not in the app's project.</span></span> <span data-ttu-id="0840d-124">请参阅 [WebAppParts 解决方案](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts)示例下载。</span><span class="sxs-lookup"><span data-stu-id="0840d-124">See the [WebAppParts solution](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="0840d-125">包含视图</span><span class="sxs-lookup"><span data-stu-id="0840d-125">Include views</span></span>

<span data-ttu-id="0840d-126">使用类库[ :::no-loc(Razor)::: 将视图](xref:razor-pages/ui-class)包含在程序集中。</span><span class="sxs-lookup"><span data-stu-id="0840d-126">Use a [:::no-loc(Razor)::: class library](xref:razor-pages/ui-class) to include views in the assembly.</span></span>

### <a name="prevent-loading-resources"></a><span data-ttu-id="0840d-127">阻止加载资源</span><span class="sxs-lookup"><span data-stu-id="0840d-127">Prevent loading resources</span></span>

<span data-ttu-id="0840d-128">可以使用应用程序部件来避免加载特定程序集或位置中的资源。 </span><span class="sxs-lookup"><span data-stu-id="0840d-128">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="0840d-129">添加或删除 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> 集合的成员，将隐藏或提供资源。</span><span class="sxs-lookup"><span data-stu-id="0840d-129">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="0840d-130">`ApplicationParts` 集合中条目的顺序并不重要。</span><span class="sxs-lookup"><span data-stu-id="0840d-130">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="0840d-131">在使用 `ApplicationPartManager` 配置容器中的服务之前，对该类进行配置。</span><span class="sxs-lookup"><span data-stu-id="0840d-131">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="0840d-132">例如，在调用 `AddControllersAsServices` 之前配置 `ApplicationPartManager`。</span><span class="sxs-lookup"><span data-stu-id="0840d-132">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="0840d-133">在 `ApplicationParts` 集合上调用 `Remove`，将删除资源。</span><span class="sxs-lookup"><span data-stu-id="0840d-133">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="0840d-134">`ApplicationPartManager` 包括以下内容的部件：</span><span class="sxs-lookup"><span data-stu-id="0840d-134">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="0840d-135">应用的程序集和依赖程序集。</span><span class="sxs-lookup"><span data-stu-id="0840d-135">The app's assembly and dependent assemblies.</span></span>
* `Microsoft.AspNetCore.Mvc.ApplicationParts.Compiled:::no-loc(Razor):::AssemblyPart`
* `Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation`
* <span data-ttu-id="0840d-136">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span><span class="sxs-lookup"><span data-stu-id="0840d-136">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="0840d-137">`Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::`.</span><span class="sxs-lookup"><span data-stu-id="0840d-137">`Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::`.</span></span>

<a name="fp"></a>

## <a name="feature-providers"></a><span data-ttu-id="0840d-138">功能提供程序</span><span class="sxs-lookup"><span data-stu-id="0840d-138">Feature providers</span></span>

<span data-ttu-id="0840d-139">应用程序功能提供程序用于检查应用程序部件，并为这些部件提供功能。</span><span class="sxs-lookup"><span data-stu-id="0840d-139">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="0840d-140">以下 ASP.NET Core 功能有内置功能提供程序：</span><span class="sxs-lookup"><span data-stu-id="0840d-140">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Controllers.ControllerFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.TagHelpers.TagHelperFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.Compilation.MetadataReferenceFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.Compilation.ViewsFeatureProvider>
* <span data-ttu-id="0840d-141">`internal class`[ :::no-loc(Razor)::: CompiledItemFeatureProvider](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.:::no-loc(Razor):::/src/ApplicationParts/:::no-loc(Razor):::CompiledItemFeatureProvider.cs#L14)</span><span class="sxs-lookup"><span data-stu-id="0840d-141">`internal class` [:::no-loc(Razor):::CompiledItemFeatureProvider](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.:::no-loc(Razor):::/src/ApplicationParts/:::no-loc(Razor):::CompiledItemFeatureProvider.cs#L14)</span></span>

<span data-ttu-id="0840d-142">功能提供程序从 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1> 继承，其中 `T` 是功能的类型。</span><span class="sxs-lookup"><span data-stu-id="0840d-142">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="0840d-143">可以为上面列出的任意功能类型实现功能提供程序。</span><span class="sxs-lookup"><span data-stu-id="0840d-143">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="0840d-144">`ApplicationPartManager.FeatureProviders` 中的功能提供程序的顺序可能影响运行时行为。</span><span class="sxs-lookup"><span data-stu-id="0840d-144">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="0840d-145">较晚添加的提供程序可能会影响较早添加的提供程序执行的操作。</span><span class="sxs-lookup"><span data-stu-id="0840d-145">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="display-available-features"></a><span data-ttu-id="0840d-146">显示可用功能</span><span class="sxs-lookup"><span data-stu-id="0840d-146">Display available features</span></span>

<span data-ttu-id="0840d-147">通过[依存关系注入](../../fundamentals/dependency-injection.md)请求 `ApplicationPartManager` 即可以枚举应用的可用功能：</span><span class="sxs-lookup"><span data-stu-id="0840d-147">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="0840d-148">[下载示例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2)使用前面的代码显示应用功能：</span><span class="sxs-lookup"><span data-stu-id="0840d-148">The [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a><span data-ttu-id="0840d-149">应用程序部件中的发现</span><span class="sxs-lookup"><span data-stu-id="0840d-149">Discovery in application parts</span></span>

<span data-ttu-id="0840d-150">使用应用程序部件进行开发时，会遇到 HTTP 404 错误且并不鲜见。</span><span class="sxs-lookup"><span data-stu-id="0840d-150">HTTP 404 errors are not uncommon when developing with application parts.</span></span> <span data-ttu-id="0840d-151">发生这些错误的原因通常是由于未满足某项针对应用程序部件发现方式的基本要求。</span><span class="sxs-lookup"><span data-stu-id="0840d-151">These errors are typically caused by missing an essential requirement for how applications parts are discovered.</span></span> <span data-ttu-id="0840d-152">如果应用返回 HTTP 404 错误，请验证是否满足以下要求：</span><span class="sxs-lookup"><span data-stu-id="0840d-152">If your app returns an HTTP 404 error, verify the following requirements have been met:</span></span>

* <span data-ttu-id="0840d-153">需要将 `applicationName` 设置设置为用于发现的根程序集。</span><span class="sxs-lookup"><span data-stu-id="0840d-153">The `applicationName` setting needs to be set to the root assembly used for discovery.</span></span> <span data-ttu-id="0840d-154">用于发现的根程序集通常是入口点程序集。</span><span class="sxs-lookup"><span data-stu-id="0840d-154">The root assembly used for discovery is normally the entry point assembly.</span></span>
* <span data-ttu-id="0840d-155">根程序集需要引用用于发现的部件。</span><span class="sxs-lookup"><span data-stu-id="0840d-155">The root assembly needs to have a reference to the parts used for discovery.</span></span> <span data-ttu-id="0840d-156">引用可以是直接的，也可以是可传递的。</span><span class="sxs-lookup"><span data-stu-id="0840d-156">The reference can be direct or transitive.</span></span>
* <span data-ttu-id="0840d-157">根程序集需要引用 Web SDK。</span><span class="sxs-lookup"><span data-stu-id="0840d-157">The root assembly needs to reference the Web SDK.</span></span> <span data-ttu-id="0840d-158">该框架的逻辑会将属性标记到用于发现的根程序集中。</span><span class="sxs-lookup"><span data-stu-id="0840d-158">The framework has logic that stamps attributes into the root assembly that are used for discovery.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0840d-159">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0840d-159">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="0840d-160">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="0840d-160">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0840d-161">应用程序部件是对应用资源的抽象化。 </span><span class="sxs-lookup"><span data-stu-id="0840d-161">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="0840d-162">应用程序部件允许 ASP.NET Core 发现控制器、查看组件、标记帮助程序、 :::no-loc(Razor)::: 页面、razor 编译源等。</span><span class="sxs-lookup"><span data-stu-id="0840d-162">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, :::no-loc(Razor)::: Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="0840d-163">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) 是一种应用程序部件。</span><span class="sxs-lookup"><span data-stu-id="0840d-163">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) is an Application part.</span></span> <span data-ttu-id="0840d-164">`AssemblyPart` 用于封装程序集引用，并公开类型和编译引用。</span><span class="sxs-lookup"><span data-stu-id="0840d-164">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="0840d-165">*功能提供程序* 使用应用程序部件填充 ASP.NET Core 应用的功能。</span><span class="sxs-lookup"><span data-stu-id="0840d-165">*Feature providers* work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="0840d-166">应用程序部件的主要用例是将应用配置为从程序集中发现（或避免加载）ASP.NET Core 功能。</span><span class="sxs-lookup"><span data-stu-id="0840d-166">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="0840d-167">例如，可能需要在多个应用之间共享通用功能。</span><span class="sxs-lookup"><span data-stu-id="0840d-167">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="0840d-168">使用应用程序部件，你可以使用多个应用共享包含控制器、视图、 :::no-loc(Razor)::: 页面、razor 编译源、标记帮助程序以及更多应用程序 (DLL) 的程序集。</span><span class="sxs-lookup"><span data-stu-id="0840d-168">Using Application Parts, you can share an assembly (DLL) containing controllers, views, :::no-loc(Razor)::: Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="0840d-169">相对于在多个项目中复制代码，首选共享程序集。</span><span class="sxs-lookup"><span data-stu-id="0840d-169">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="0840d-170">ASP.NET Core 应用从 <xref:System.Web.WebPages.ApplicationPart> 加载功能。</span><span class="sxs-lookup"><span data-stu-id="0840d-170">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="0840d-171"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> 类表示受程序集支持的应用程序部件。</span><span class="sxs-lookup"><span data-stu-id="0840d-171">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="0840d-172">加载 ASP.NET Core 功能</span><span class="sxs-lookup"><span data-stu-id="0840d-172">Load ASP.NET Core features</span></span>

<span data-ttu-id="0840d-173">使用 `ApplicationPart` 和 `AssemblyPart` 类发现并加载 ASP.NET Core 功能（控制器、视图组件等）。</span><span class="sxs-lookup"><span data-stu-id="0840d-173">Use the `ApplicationPart` and `AssemblyPart` classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="0840d-174"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> 跟踪可用的应用程序部件和功能提供程序。</span><span class="sxs-lookup"><span data-stu-id="0840d-174">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="0840d-175">在 `Startup.ConfigureServices` 中配置 `ApplicationPartManager`：</span><span class="sxs-lookup"><span data-stu-id="0840d-175">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="0840d-176">以下代码提供使用 `AssemblyPart` 配置 `ApplicationPartManager` 的可选方法：</span><span class="sxs-lookup"><span data-stu-id="0840d-176">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="0840d-177">前面的两个代码示例从程序集加载 `SharedController`。</span><span class="sxs-lookup"><span data-stu-id="0840d-177">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="0840d-178">`SharedController` 未在应用程序的项目中。</span><span class="sxs-lookup"><span data-stu-id="0840d-178">The `SharedController` is not in the application's project.</span></span> <span data-ttu-id="0840d-179">请参阅 [WebAppParts 解决方案](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts)示例下载。</span><span class="sxs-lookup"><span data-stu-id="0840d-179">See the [WebAppParts solution](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="0840d-180">包含视图</span><span class="sxs-lookup"><span data-stu-id="0840d-180">Include views</span></span>

<span data-ttu-id="0840d-181">使用类库[ :::no-loc(Razor)::: 将视图](xref:razor-pages/ui-class)包含在程序集中。</span><span class="sxs-lookup"><span data-stu-id="0840d-181">Use a [:::no-loc(Razor)::: class library](xref:razor-pages/ui-class) to include views in the assembly.</span></span>

### <a name="prevent-loading-resources"></a><span data-ttu-id="0840d-182">阻止加载资源</span><span class="sxs-lookup"><span data-stu-id="0840d-182">Prevent loading resources</span></span>

<span data-ttu-id="0840d-183">可以使用应用程序部件来避免加载特定程序集或位置中的资源。 </span><span class="sxs-lookup"><span data-stu-id="0840d-183">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="0840d-184">添加或删除 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> 集合的成员，将隐藏或提供资源。</span><span class="sxs-lookup"><span data-stu-id="0840d-184">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="0840d-185">`ApplicationParts` 集合中条目的顺序并不重要。</span><span class="sxs-lookup"><span data-stu-id="0840d-185">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="0840d-186">在使用 `ApplicationPartManager` 配置容器中的服务之前，对该类进行配置。</span><span class="sxs-lookup"><span data-stu-id="0840d-186">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="0840d-187">例如，在调用 `AddControllersAsServices` 之前配置 `ApplicationPartManager`。</span><span class="sxs-lookup"><span data-stu-id="0840d-187">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="0840d-188">在 `ApplicationParts` 集合上调用 `Remove`，将删除资源。</span><span class="sxs-lookup"><span data-stu-id="0840d-188">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="0840d-189">以下代码使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> 删除应用中的 `MyDependentLibrary`：[!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="0840d-189">The following code uses <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> to remove `MyDependentLibrary` from the app: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span></span>

<span data-ttu-id="0840d-190">`ApplicationPartManager` 包括以下内容的部件：</span><span class="sxs-lookup"><span data-stu-id="0840d-190">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="0840d-191">应用的程序集和依赖程序集。</span><span class="sxs-lookup"><span data-stu-id="0840d-191">The app's assembly and dependent assemblies.</span></span>
* <span data-ttu-id="0840d-192">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span><span class="sxs-lookup"><span data-stu-id="0840d-192">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="0840d-193">`Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::`.</span><span class="sxs-lookup"><span data-stu-id="0840d-193">`Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::`.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="0840d-194">应用程序功能提供程序</span><span class="sxs-lookup"><span data-stu-id="0840d-194">Application feature providers</span></span>

<span data-ttu-id="0840d-195">应用程序功能提供程序用于检查应用程序部件，并为这些部件提供功能。</span><span class="sxs-lookup"><span data-stu-id="0840d-195">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="0840d-196">以下 ASP.NET Core 功能有内置功能提供程序：</span><span class="sxs-lookup"><span data-stu-id="0840d-196">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* [<span data-ttu-id="0840d-197">Controllers</span><span class="sxs-lookup"><span data-stu-id="0840d-197">Controllers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="0840d-198">标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="0840d-198">Tag Helpers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="0840d-199">查看组件</span><span class="sxs-lookup"><span data-stu-id="0840d-199">View Components</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="0840d-200">功能提供程序从 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1> 继承，其中 `T` 是功能的类型。</span><span class="sxs-lookup"><span data-stu-id="0840d-200">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="0840d-201">可以为上面列出的任意功能类型实现功能提供程序。</span><span class="sxs-lookup"><span data-stu-id="0840d-201">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="0840d-202">`ApplicationPartManager.FeatureProviders` 中的功能提供程序的顺序可能影响运行时行为。</span><span class="sxs-lookup"><span data-stu-id="0840d-202">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="0840d-203">较晚添加的提供程序可能会影响较早添加的提供程序执行的操作。</span><span class="sxs-lookup"><span data-stu-id="0840d-203">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="display-available-features"></a><span data-ttu-id="0840d-204">显示可用功能</span><span class="sxs-lookup"><span data-stu-id="0840d-204">Display available features</span></span>

<span data-ttu-id="0840d-205">通过[依存关系注入](../../fundamentals/dependency-injection.md)请求 `ApplicationPartManager` 即可以枚举应用的可用功能：</span><span class="sxs-lookup"><span data-stu-id="0840d-205">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="0840d-206">[下载示例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2)使用前面的代码显示应用功能：</span><span class="sxs-lookup"><span data-stu-id="0840d-206">The [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a><span data-ttu-id="0840d-207">应用程序部件中的发现</span><span class="sxs-lookup"><span data-stu-id="0840d-207">Discovery in application parts</span></span>

<span data-ttu-id="0840d-208">使用应用程序部件进行开发时，会遇到 HTTP 404 错误且并不鲜见。</span><span class="sxs-lookup"><span data-stu-id="0840d-208">HTTP 404 errors are not uncommon when developing with application parts.</span></span> <span data-ttu-id="0840d-209">发生这些错误的原因通常是由于未满足某项针对应用程序部件发现方式的基本要求。</span><span class="sxs-lookup"><span data-stu-id="0840d-209">These errors are typically caused by missing an essential requirement for how applications parts are discovered.</span></span> <span data-ttu-id="0840d-210">如果应用返回 HTTP 404 错误，请验证是否满足以下要求：</span><span class="sxs-lookup"><span data-stu-id="0840d-210">If your app returns an HTTP 404 error, verify the following requirements have been met:</span></span>

* <span data-ttu-id="0840d-211">需要将 `applicationName` 设置设置为用于发现的根程序集。</span><span class="sxs-lookup"><span data-stu-id="0840d-211">The `applicationName` setting needs to be set to the root assembly used for discovery.</span></span> <span data-ttu-id="0840d-212">用于发现的根程序集通常是入口点程序集。</span><span class="sxs-lookup"><span data-stu-id="0840d-212">The root assembly used for discovery is normally the entry point assembly.</span></span>
* <span data-ttu-id="0840d-213">根程序集需要引用用于发现的部件。</span><span class="sxs-lookup"><span data-stu-id="0840d-213">The root assembly needs to have a reference to the parts used for discovery.</span></span> <span data-ttu-id="0840d-214">引用可以是直接的，也可以是可传递的。</span><span class="sxs-lookup"><span data-stu-id="0840d-214">The reference can be direct or transitive.</span></span>
* <span data-ttu-id="0840d-215">根程序集需要引用 Web SDK。</span><span class="sxs-lookup"><span data-stu-id="0840d-215">The root assembly needs to reference the Web SDK.</span></span>
  * <span data-ttu-id="0840d-216">ASP.NET Core 框架具有自定义生成逻辑，会将属性标记到用于发现的根程序集中。</span><span class="sxs-lookup"><span data-stu-id="0840d-216">The ASP.NET Core framework has custom build logic that stamps attributes into the root assembly that are used for discovery.</span></span>

::: moniker-end
