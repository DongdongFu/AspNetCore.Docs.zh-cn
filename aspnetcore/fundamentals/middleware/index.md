---
title: ASP.NET Core 中间件
author: rick-anderson
description: 了解 ASP.NET Core 中间件和请求管道。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
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
uid: fundamentals/middleware/index
ms.openlocfilehash: 06f55647ba2bb41152e16bb054e098abbe157cb8
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059359"
---
# <a name="aspnet-core-middleware"></a><span data-ttu-id="8466a-103">ASP.NET Core 中间件</span><span class="sxs-lookup"><span data-stu-id="8466a-103">ASP.NET Core Middleware</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8466a-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="8466a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="8466a-105">中间件是一种装配到应用管道以处理请求和响应的软件。</span><span class="sxs-lookup"><span data-stu-id="8466a-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="8466a-106">每个组件：</span><span class="sxs-lookup"><span data-stu-id="8466a-106">Each component:</span></span>

* <span data-ttu-id="8466a-107">选择是否将请求传递到管道中的下一个组件。</span><span class="sxs-lookup"><span data-stu-id="8466a-107">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="8466a-108">可在管道中的下一个组件前后执行工作。</span><span class="sxs-lookup"><span data-stu-id="8466a-108">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="8466a-109">请求委托用于生成请求管道。</span><span class="sxs-lookup"><span data-stu-id="8466a-109">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="8466a-110">请求委托处理每个 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="8466a-110">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="8466a-111">使用 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 和 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> 扩展方法来配置请求委托。</span><span class="sxs-lookup"><span data-stu-id="8466a-111">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> extension methods.</span></span> <span data-ttu-id="8466a-112">可将一个单独的请求委托并行指定为匿名方法（称为并行中间件），或在可重用的类中对其进行定义。</span><span class="sxs-lookup"><span data-stu-id="8466a-112">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="8466a-113">这些可重用的类和并行匿名方法即为中间件，也叫中间件组件。</span><span class="sxs-lookup"><span data-stu-id="8466a-113">These reusable classes and in-line anonymous methods are *middleware* , also called *middleware components* .</span></span> <span data-ttu-id="8466a-114">请求管道中的每个中间件组件负责调用管道中的下一个组件，或使管道短路。</span><span class="sxs-lookup"><span data-stu-id="8466a-114">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="8466a-115">当中间件短路时，它被称为“终端中间件”，因为它阻止中间件进一步处理请求。</span><span class="sxs-lookup"><span data-stu-id="8466a-115">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="8466a-116"><xref:migration/http-modules>介绍了 ASP.NET Core 和 ASP.NET 4.x 中请求管道之间的差异，并提供了更多的中间件示例。</span><span class="sxs-lookup"><span data-stu-id="8466a-116"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="8466a-117">使用 IApplicationBuilder 创建中间件管道</span><span class="sxs-lookup"><span data-stu-id="8466a-117">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="8466a-118">ASP.NET Core 请求管道包含一系列请求委托，依次调用。</span><span class="sxs-lookup"><span data-stu-id="8466a-118">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="8466a-119">下图演示了这一概念。</span><span class="sxs-lookup"><span data-stu-id="8466a-119">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="8466a-120">沿黑色箭头执行。</span><span class="sxs-lookup"><span data-stu-id="8466a-120">The thread of execution follows the black arrows.</span></span>

![请求处理模式显示请求到达、通过三个中间件进行处理以及响应离开应用。](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="8466a-124">每个委托均可在下一个委托前后执行操作。</span><span class="sxs-lookup"><span data-stu-id="8466a-124">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="8466a-125">应尽早在管道中调用异常处理委托，这样它们就能捕获在管道的后期阶段发生的异常。</span><span class="sxs-lookup"><span data-stu-id="8466a-125">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="8466a-126">尽可能简单的 ASP.NET Core 应用设置了处理所有请求的单个请求委托。</span><span class="sxs-lookup"><span data-stu-id="8466a-126">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="8466a-127">这种情况不包括实际请求管道。</span><span class="sxs-lookup"><span data-stu-id="8466a-127">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="8466a-128">调用单个匿名函数以响应每个 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="8466a-128">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="8466a-129">用 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> 将多个请求委托链接在一起。</span><span class="sxs-lookup"><span data-stu-id="8466a-129">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>.</span></span> <span data-ttu-id="8466a-130">`next` 参数表示管道中的下一个委托。</span><span class="sxs-lookup"><span data-stu-id="8466a-130">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="8466a-131">可通过不调用 next 参数使管道短路。</span><span class="sxs-lookup"><span data-stu-id="8466a-131">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="8466a-132">通常可在下一个委托前后执行操作，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="8466a-132">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=5-10)]

<span data-ttu-id="8466a-133">当委托不将请求传递给下一个委托时，它被称为“让请求管道短路”。</span><span class="sxs-lookup"><span data-stu-id="8466a-133">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline* .</span></span> <span data-ttu-id="8466a-134">通常需要短路，因为这样可以避免不必要的工作。</span><span class="sxs-lookup"><span data-stu-id="8466a-134">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="8466a-135">例如，[静态文件中间件](xref:fundamentals/static-files)可以处理对静态文件的请求，并让管道的其余部分短路，从而起到终端中间件的作用。</span><span class="sxs-lookup"><span data-stu-id="8466a-135">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="8466a-136">如果中间件添加到管道中，且位于终止进一步处理的中间件前，它们仍处理 `next.Invoke` 语句后面的代码。</span><span class="sxs-lookup"><span data-stu-id="8466a-136">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="8466a-137">不过，请参阅下面有关尝试对已发送的响应执行写入操作的警告。</span><span class="sxs-lookup"><span data-stu-id="8466a-137">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="8466a-138">在向客户端发送响应后，请勿调用 `next.Invoke`。</span><span class="sxs-lookup"><span data-stu-id="8466a-138">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="8466a-139">响应启动后，针对 <xref:Microsoft.AspNetCore.Http.HttpResponse> 的更改将引发异常。</span><span class="sxs-lookup"><span data-stu-id="8466a-139">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="8466a-140">例如，[设置标头和状态代码更改将引发异常](xref:performance/performance-best-practices#do-not-modify-the-status-code-or-headers-after-the-response-body-has-started)。</span><span class="sxs-lookup"><span data-stu-id="8466a-140">For example, [setting headers and a status code throw an exception](xref:performance/performance-best-practices#do-not-modify-the-status-code-or-headers-after-the-response-body-has-started).</span></span> <span data-ttu-id="8466a-141">调用 `next` 后写入响应正文：</span><span class="sxs-lookup"><span data-stu-id="8466a-141">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="8466a-142">可能导致违反协议。</span><span class="sxs-lookup"><span data-stu-id="8466a-142">May cause a protocol violation.</span></span> <span data-ttu-id="8466a-143">例如，写入的长度超过规定的 `Content-Length`。</span><span class="sxs-lookup"><span data-stu-id="8466a-143">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="8466a-144">可能损坏正文格式。</span><span class="sxs-lookup"><span data-stu-id="8466a-144">May corrupt the body format.</span></span> <span data-ttu-id="8466a-145">例如，向 CSS 文件中写入 HTML 页脚。</span><span class="sxs-lookup"><span data-stu-id="8466a-145">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="8466a-146"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> 是一个有用的提示，指示是否已发送标头或已写入正文。</span><span class="sxs-lookup"><span data-stu-id="8466a-146"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

<span data-ttu-id="8466a-147"><xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> 委托不会收到 `next` 参数。</span><span class="sxs-lookup"><span data-stu-id="8466a-147"><xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> delegates don't receive a `next` parameter.</span></span> <span data-ttu-id="8466a-148">第一个 `Run` 委托始终为终端，用于终止管道。</span><span class="sxs-lookup"><span data-stu-id="8466a-148">The first `Run` delegate is always terminal and terminates the pipeline.</span></span> <span data-ttu-id="8466a-149">`Run` 是一种约定。</span><span class="sxs-lookup"><span data-stu-id="8466a-149">`Run` is a convention.</span></span> <span data-ttu-id="8466a-150">某些中间件组件可能会公开在管道末尾运行的 `Run[Middleware]` 方法：</span><span class="sxs-lookup"><span data-stu-id="8466a-150">Some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=12-15)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="8466a-151">在前面的示例中，`Run` 委托将 `"Hello from 2nd delegate."` 写入响应，然后终止管道。</span><span class="sxs-lookup"><span data-stu-id="8466a-151">In the preceding example, the `Run` delegate writes `"Hello from 2nd delegate."` to the response and then terminates the pipeline.</span></span> <span data-ttu-id="8466a-152">如果在 `Run` 委托之后添加了另一个 `Use` 或 `Run` 委托，则不会调用该委托。</span><span class="sxs-lookup"><span data-stu-id="8466a-152">If another `Use` or `Run` delegate is added after the `Run` delegate, it's not called.</span></span>

<a name="order"></a>

## <a name="middleware-order"></a><span data-ttu-id="8466a-153">中间件顺序</span><span class="sxs-lookup"><span data-stu-id="8466a-153">Middleware order</span></span>

<span data-ttu-id="8466a-154">下图显示了 ASP.NET Core MVC 和 :::no-loc(Razor)::: Pages 应用的完整请求处理管道。</span><span class="sxs-lookup"><span data-stu-id="8466a-154">The following diagram shows the complete request processing pipeline for ASP.NET Core MVC and :::no-loc(Razor)::: Pages apps.</span></span> <span data-ttu-id="8466a-155">你可以在典型应用中了解现有中间件的顺序，以及在哪里添加自定义中间件。</span><span class="sxs-lookup"><span data-stu-id="8466a-155">You can see how, in a typical app, existing middlewares are ordered and where custom middlewares are added.</span></span> <span data-ttu-id="8466a-156">你可以完全控制如何重新排列现有中间件，或根据场景需要注入新的自定义中间件。</span><span class="sxs-lookup"><span data-stu-id="8466a-156">You have full control over how to reorder existing middlewares or inject new custom middlewares as necessary for your scenarios.</span></span>

![ASP.NET Core 中间件管道](index/_static/middleware-pipeline.svg)

<span data-ttu-id="8466a-158">上图中的“终结点”中间件为相应的应用类型（MVC 或 :::no-loc(Razor)::: Pages）执行筛选器管道。</span><span class="sxs-lookup"><span data-stu-id="8466a-158">The **Endpoint** middleware in the preceding diagram executes the filter pipeline for the corresponding app type&mdash;MVC or :::no-loc(Razor)::: Pages.</span></span>

![ASP.NET Core 筛选器管道](index/_static/mvc-endpoint.svg)

<span data-ttu-id="8466a-160">向 `Startup.Configure` 方法添加中间件组件的顺序定义了针对请求调用这些组件的顺序，以及响应的相反顺序。</span><span class="sxs-lookup"><span data-stu-id="8466a-160">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="8466a-161">此顺序对于安全性、性能和功能至关重要。</span><span class="sxs-lookup"><span data-stu-id="8466a-161">The order is **critical** for security, performance, and functionality.</span></span>

<span data-ttu-id="8466a-162">下面的 `Startup.Configure` 方法按照建议的顺序增加与安全相关的中间件组件：</span><span class="sxs-lookup"><span data-stu-id="8466a-162">The following `Startup.Configure` method adds security-related middleware components in the recommended order:</span></span>

[!code-csharp[](index/snapshot/StartupAll3.cs?name=snippet)]

<span data-ttu-id="8466a-163">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="8466a-163">In the preceding code:</span></span>

* <span data-ttu-id="8466a-164">在使用[单个用户帐户](xref:security/authentication/identity)创建新的 Web 应用时未添加的中间件已被注释掉。</span><span class="sxs-lookup"><span data-stu-id="8466a-164">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="8466a-165">并非所有中间件都需要准确按照此顺序运行，但许多中间件必须遵循这个顺序。</span><span class="sxs-lookup"><span data-stu-id="8466a-165">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="8466a-166">例如：</span><span class="sxs-lookup"><span data-stu-id="8466a-166">For example:</span></span>
  * <span data-ttu-id="8466a-167">`UseCors`、`UseAuthentication` 和 `UseAuthorization` 必须按照上述顺序运行。</span><span class="sxs-lookup"><span data-stu-id="8466a-167">`UseCors`, `UseAuthentication`, and `UseAuthorization` must go in the order shown.</span></span>
  * <span data-ttu-id="8466a-168">由于[此错误](https://github.com/dotnet/aspnetcore/issues/23218)，`UseCors` 当前必须在 `UseResponseCaching` 之前运行。</span><span class="sxs-lookup"><span data-stu-id="8466a-168">`UseCors` currently must go before `UseResponseCaching` due to [this bug](https://github.com/dotnet/aspnetcore/issues/23218).</span></span>

<span data-ttu-id="8466a-169">以下 `Startup.Configure` 方法将为常见应用方案添加中间件组件：</span><span class="sxs-lookup"><span data-stu-id="8466a-169">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="8466a-170">异常/错误处理</span><span class="sxs-lookup"><span data-stu-id="8466a-170">Exception/error handling</span></span>
   * <span data-ttu-id="8466a-171">当应用在开发环境中运行时：</span><span class="sxs-lookup"><span data-stu-id="8466a-171">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="8466a-172">开发人员异常页中间件 (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>) 报告应用运行时错误。</span><span class="sxs-lookup"><span data-stu-id="8466a-172">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>) reports app runtime errors.</span></span>
     * <span data-ttu-id="8466a-173">数据库错误页中间件报告数据库运行时错误。</span><span class="sxs-lookup"><span data-stu-id="8466a-173">Database Error Page Middleware reports database runtime errors.</span></span>
   * <span data-ttu-id="8466a-174">当应用在生产环境中运行时：</span><span class="sxs-lookup"><span data-stu-id="8466a-174">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="8466a-175">异常处理程序中间件 (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>) 捕获以下中间件中引发的异常。</span><span class="sxs-lookup"><span data-stu-id="8466a-175">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="8466a-176">HTTP 严格传输安全协议 (HSTS) 中间件 (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>) 添加 `Strict-Transport-Security` 标头。</span><span class="sxs-lookup"><span data-stu-id="8466a-176">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="8466a-177">HTTPS 重定向中间件 (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>) 将 HTTP 请求重定向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="8466a-177">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="8466a-178">静态文件中间件 (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>) 返回静态文件，并简化进一步请求处理。</span><span class="sxs-lookup"><span data-stu-id="8466a-178">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="8466a-179">:::no-loc(Cookie)::: 策略中间件 (<xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyAppBuilderExtensions.Use:::no-loc(Cookie):::Policy%2A>) 使应用符合欧盟一般数据保护条例 (GDPR) 规定。</span><span class="sxs-lookup"><span data-stu-id="8466a-179">:::no-loc(Cookie)::: Policy Middleware (<xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyAppBuilderExtensions.Use:::no-loc(Cookie):::Policy%2A>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="8466a-180">用于路由请求的路由中间件 (<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A>)。</span><span class="sxs-lookup"><span data-stu-id="8466a-180">Routing Middleware (<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A>) to route requests.</span></span>
1. <span data-ttu-id="8466a-181">身份验证中间件 (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) 尝试对用户进行身份验证，然后才会允许用户访问安全资源。</span><span class="sxs-lookup"><span data-stu-id="8466a-181">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="8466a-182">用于授权用户访问安全资源的授权中间件 (<xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>)。</span><span class="sxs-lookup"><span data-stu-id="8466a-182">Authorization Middleware (<xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>) authorizes a user to access secure resources.</span></span>
1. <span data-ttu-id="8466a-183">会话中间件 (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>) 建立和维护会话状态。</span><span class="sxs-lookup"><span data-stu-id="8466a-183">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>) establishes and maintains session state.</span></span> <span data-ttu-id="8466a-184">如果应用使用会话状态，请在 :::no-loc(Cookie)::: 策略中间件之后和 MVC 中间件之前调用会话中间件。</span><span class="sxs-lookup"><span data-stu-id="8466a-184">If the app uses session state, call Session Middleware after :::no-loc(Cookie)::: Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="8466a-185">用于将 :::no-loc(Razor)::: Pages 终结点添加到请求管道的终结点路由中间件（带有 <xref:Microsoft.AspNetCore.Builder.:::no-loc(Razor):::PagesEndpointRouteBuilderExtensions.Map:::no-loc(Razor):::Pages%2A> 的 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A>）。</span><span class="sxs-lookup"><span data-stu-id="8466a-185">Endpoint Routing Middleware (<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> with <xref:Microsoft.AspNetCore.Builder.:::no-loc(Razor):::PagesEndpointRouteBuilderExtensions.Map:::no-loc(Razor):::Pages%2A>) to add :::no-loc(Razor)::: Pages endpoints to the request pipeline.</span></span>

<!--

FUTURE UPDATE

On the next topic overhaul/release update, add API crosslink to "Database Error Page Middleware" in Item 1 of the list ...

Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage*

... when available via the API docs.

-->

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.Use:::no-loc(Cookie):::Policy();
    app.UseRouting();
    app.UseAuthentication();
    app.UseAuthorization();
    app.UseSession();

    app.UseEndpoints(endpoints =>
    {
        endpoints.Map:::no-loc(Razor):::Pages();
    });
}
```

<span data-ttu-id="8466a-186">在前面的示例代码中，每个中间件扩展方法都通过 <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 命名空间在 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> 上公开。</span><span class="sxs-lookup"><span data-stu-id="8466a-186">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="8466a-187"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> 是添加到管道的第一个中间件组件。</span><span class="sxs-lookup"><span data-stu-id="8466a-187"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="8466a-188">因此，异常处理程序中间件可捕获稍后调用中发生的任何异常。</span><span class="sxs-lookup"><span data-stu-id="8466a-188">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="8466a-189">尽早在管道中调用静态文件中间件，以便它可以处理请求并使其短路，而无需通过剩余组件。</span><span class="sxs-lookup"><span data-stu-id="8466a-189">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="8466a-190">静态文件中间件不提供授权检查。</span><span class="sxs-lookup"><span data-stu-id="8466a-190">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="8466a-191">可公开访问由静态文件中间件服务的任何文件，包括 wwwroot 下的文件。</span><span class="sxs-lookup"><span data-stu-id="8466a-191">Any files served by Static File Middleware, including those under *wwwroot* , are publicly available.</span></span> <span data-ttu-id="8466a-192">若要了解如何保护静态文件，请参阅 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="8466a-192">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="8466a-193">如果静态文件中间件未处理请求，则请求将被传递给执行身份验证的身份验证中间件 (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>)。</span><span class="sxs-lookup"><span data-stu-id="8466a-193">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>), which performs authentication.</span></span> <span data-ttu-id="8466a-194">身份验证不使未经身份验证的请求短路。</span><span class="sxs-lookup"><span data-stu-id="8466a-194">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="8466a-195">虽然身份验证中间件对请求进行身份验证，但仅在 MVC 选择特定 :::no-loc(Razor)::: Page 或 MVC 控制器和操作后，才发生授权（和拒绝）。</span><span class="sxs-lookup"><span data-stu-id="8466a-195">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific :::no-loc(Razor)::: Page or MVC controller and action.</span></span>

<span data-ttu-id="8466a-196">以下示例演示中间件排序，其中静态文件的请求在响应压缩中间件前由静态文件中间件进行处理。</span><span class="sxs-lookup"><span data-stu-id="8466a-196">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="8466a-197">使用此中间件顺序不压缩静态文件。</span><span class="sxs-lookup"><span data-stu-id="8466a-197">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="8466a-198">可以压缩 :::no-loc(Razor)::: Pages 响应。</span><span class="sxs-lookup"><span data-stu-id="8466a-198">The :::no-loc(Razor)::: Pages responses can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseEndpoints(endpoints =>
    {
        endpoints.Map:::no-loc(Razor):::Pages();
    });
}
```

<span data-ttu-id="8466a-199">对于单页应用程序 (SPA)，SPA 中间件 <xref:Microsoft.Extensions.DependencyInjection.SpaStaticFilesExtensions.UseSpaStaticFiles%2A> 通常是中间件管道中的最后一个。</span><span class="sxs-lookup"><span data-stu-id="8466a-199">For Single Page Applications (SPAs), the SPA middleware <xref:Microsoft.Extensions.DependencyInjection.SpaStaticFilesExtensions.UseSpaStaticFiles%2A> usually comes last in the middleware pipeline.</span></span> <span data-ttu-id="8466a-200">SPA 中间件处于最后的作用是：</span><span class="sxs-lookup"><span data-stu-id="8466a-200">The SPA middleware comes last:</span></span>

* <span data-ttu-id="8466a-201">允许所有其他中间件首先响应匹配的请求。</span><span class="sxs-lookup"><span data-stu-id="8466a-201">To allow all other middlewares to respond to matching requests first.</span></span>
* <span data-ttu-id="8466a-202">允许具有客户端侧路由的 SPA 针对服务器应用无法识别的所有路由运行。</span><span class="sxs-lookup"><span data-stu-id="8466a-202">To allow SPAs with client-side routing to run for all routes that are unrecognized by the server app.</span></span>

<span data-ttu-id="8466a-203">若要详细了解 SPA，请参阅 [React](xref:spa/react) 和 [Angular](xref:spa/angular) 项目模板的指南。</span><span class="sxs-lookup"><span data-stu-id="8466a-203">For more details on SPAs, see the guides for the [React](xref:spa/react) and [Angular](xref:spa/angular) project templates.</span></span>

### <a name="forwarded-headers-middleware-order"></a><span data-ttu-id="8466a-204">转接头中间件顺序</span><span class="sxs-lookup"><span data-stu-id="8466a-204">Forwarded Headers Middleware order</span></span>

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

## <a name="branch-the-middleware-pipeline"></a><span data-ttu-id="8466a-205">对中间件管道进行分支</span><span class="sxs-lookup"><span data-stu-id="8466a-205">Branch the middleware pipeline</span></span>

<span data-ttu-id="8466a-206"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 扩展用作约定来创建管道分支。</span><span class="sxs-lookup"><span data-stu-id="8466a-206"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="8466a-207">`Map` 基于给定请求路径的匹配项来创建请求管道分支。</span><span class="sxs-lookup"><span data-stu-id="8466a-207">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="8466a-208">如果请求路径以给定路径开头，则执行分支。</span><span class="sxs-lookup"><span data-stu-id="8466a-208">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="8466a-209">下表使用前面的代码显示来自 `http://localhost:1234` 的请求和响应。</span><span class="sxs-lookup"><span data-stu-id="8466a-209">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="8466a-210">请求</span><span class="sxs-lookup"><span data-stu-id="8466a-210">Request</span></span>             | <span data-ttu-id="8466a-211">响应</span><span class="sxs-lookup"><span data-stu-id="8466a-211">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="8466a-212">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="8466a-212">localhost:1234</span></span>      | <span data-ttu-id="8466a-213">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="8466a-213">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="8466a-214">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="8466a-214">localhost:1234/map1</span></span> | <span data-ttu-id="8466a-215">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="8466a-215">Map Test 1</span></span>                   |
| <span data-ttu-id="8466a-216">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="8466a-216">localhost:1234/map2</span></span> | <span data-ttu-id="8466a-217">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="8466a-217">Map Test 2</span></span>                   |
| <span data-ttu-id="8466a-218">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="8466a-218">localhost:1234/map3</span></span> | <span data-ttu-id="8466a-219">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="8466a-219">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="8466a-220">使用 `Map` 时，将从 `HttpRequest.Path` 中删除匹配的路径段，并针对每个请求将该路径段追加到 `HttpRequest.PathBase`。</span><span class="sxs-lookup"><span data-stu-id="8466a-220">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="8466a-221">`Map` 支持嵌套，例如：</span><span class="sxs-lookup"><span data-stu-id="8466a-221">`Map` supports nesting, for example:</span></span>

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
```

<span data-ttu-id="8466a-222">此外，`Map` 还可同时匹配多个段：</span><span class="sxs-lookup"><span data-stu-id="8466a-222">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

<span data-ttu-id="8466a-223"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> 基于给定谓词的结果创建请求管道分支。</span><span class="sxs-lookup"><span data-stu-id="8466a-223"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="8466a-224">`Func<HttpContext, bool>` 类型的任何谓词均可用于将请求映射到管道的新分支。</span><span class="sxs-lookup"><span data-stu-id="8466a-224">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="8466a-225">在以下示例中，谓词用于检测查询字符串变量 `branch` 是否存在：</span><span class="sxs-lookup"><span data-stu-id="8466a-225">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?highlight=14-15)]

<span data-ttu-id="8466a-226">下表使用前面的代码显示来自 `http://localhost:1234` 的请求和响应：</span><span class="sxs-lookup"><span data-stu-id="8466a-226">The following table shows the requests and responses from `http://localhost:1234` using the previous code:</span></span>

| <span data-ttu-id="8466a-227">请求</span><span class="sxs-lookup"><span data-stu-id="8466a-227">Request</span></span>                       | <span data-ttu-id="8466a-228">响应</span><span class="sxs-lookup"><span data-stu-id="8466a-228">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="8466a-229">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="8466a-229">localhost:1234</span></span>                | <span data-ttu-id="8466a-230">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="8466a-230">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="8466a-231">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="8466a-231">localhost:1234/?branch=master</span></span> | <span data-ttu-id="8466a-232">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="8466a-232">Branch used = master</span></span>         |

<span data-ttu-id="8466a-233"><xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen%2A> 也基于给定谓词的结果创建请求管道分支。</span><span class="sxs-lookup"><span data-stu-id="8466a-233"><xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen%2A> also branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="8466a-234">与 `MapWhen` 不同的是，如果这个分支不发生短路或包含终端中间件，则会重新加入主管道：</span><span class="sxs-lookup"><span data-stu-id="8466a-234">Unlike with `MapWhen`, this branch is rejoined to the main pipeline if it doesn't short-circuit or contain a terminal middleware:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupUseWhen.cs?highlight=25-26)]

<span data-ttu-id="8466a-235">在前面的示例中，响应 "Hello from main pipeline."</span><span class="sxs-lookup"><span data-stu-id="8466a-235">In the preceding example, a response of "Hello from main pipeline."</span></span> <span data-ttu-id="8466a-236">是为所有请求编写的。</span><span class="sxs-lookup"><span data-stu-id="8466a-236">is written for all requests.</span></span> <span data-ttu-id="8466a-237">如果请求中包含查询字符串变量 `branch`，则在重新加入主管道之前会记录其值。</span><span class="sxs-lookup"><span data-stu-id="8466a-237">If the request includes a query string variable `branch`, its value is logged before the main pipeline is rejoined.</span></span>

## <a name="built-in-middleware"></a><span data-ttu-id="8466a-238">内置中间件</span><span class="sxs-lookup"><span data-stu-id="8466a-238">Built-in middleware</span></span>

<span data-ttu-id="8466a-239">ASP.NET Core 附带以下中间件组件。</span><span class="sxs-lookup"><span data-stu-id="8466a-239">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="8466a-240">“顺序”列提供备注，以说明中间件在请求处理管道中的放置，以及中间件可能会终止请求处理的条件。</span><span class="sxs-lookup"><span data-stu-id="8466a-240">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="8466a-241">如果中间件让请求处理管道短路，并阻止下游中间件进一步处理请求，它被称为“终端中间件”。</span><span class="sxs-lookup"><span data-stu-id="8466a-241">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware* .</span></span> <span data-ttu-id="8466a-242">若要详细了解短路，请参阅[使用 IApplicationBuilder 创建中间件管道](#create-a-middleware-pipeline-with-iapplicationbuilder)部分。</span><span class="sxs-lookup"><span data-stu-id="8466a-242">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="8466a-243">中间件</span><span class="sxs-lookup"><span data-stu-id="8466a-243">Middleware</span></span> | <span data-ttu-id="8466a-244">描述</span><span class="sxs-lookup"><span data-stu-id="8466a-244">Description</span></span> | <span data-ttu-id="8466a-245">顺序</span><span class="sxs-lookup"><span data-stu-id="8466a-245">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="8466a-246">身份验证</span><span class="sxs-lookup"><span data-stu-id="8466a-246">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="8466a-247">提供身份验证支持。</span><span class="sxs-lookup"><span data-stu-id="8466a-247">Provides authentication support.</span></span> | <span data-ttu-id="8466a-248">在需要 `HttpContext.User` 之前。</span><span class="sxs-lookup"><span data-stu-id="8466a-248">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="8466a-249">OAuth 回叫的终端。</span><span class="sxs-lookup"><span data-stu-id="8466a-249">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="8466a-250">授权</span><span class="sxs-lookup"><span data-stu-id="8466a-250">Authorization</span></span>](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A) | <span data-ttu-id="8466a-251">提供身份验证支持。</span><span class="sxs-lookup"><span data-stu-id="8466a-251">Provides authorization support.</span></span> | <span data-ttu-id="8466a-252">紧接在身份验证中间件之后。</span><span class="sxs-lookup"><span data-stu-id="8466a-252">Immediately after the Authentication Middleware.</span></span> |
| [<span data-ttu-id="8466a-253">:::no-loc(Cookie)::: 策略</span><span class="sxs-lookup"><span data-stu-id="8466a-253">:::no-loc(Cookie)::: Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="8466a-254">跟踪用户是否同意存储个人信息，并强制实施 :::no-loc(cookie)::: 字段（如 `secure` 和 `SameSite`）的最低标准。</span><span class="sxs-lookup"><span data-stu-id="8466a-254">Tracks consent from users for storing personal information and enforces minimum standards for :::no-loc(cookie)::: fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="8466a-255">在发出 :::no-loc(cookie)::: 的中间件之前。</span><span class="sxs-lookup"><span data-stu-id="8466a-255">Before middleware that issues :::no-loc(cookie):::s.</span></span> <span data-ttu-id="8466a-256">示例：身份验证、会话、MVC (TempData)。</span><span class="sxs-lookup"><span data-stu-id="8466a-256">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="8466a-257">CORS</span><span class="sxs-lookup"><span data-stu-id="8466a-257">CORS</span></span>](xref:security/cors) | <span data-ttu-id="8466a-258">配置跨域资源共享。</span><span class="sxs-lookup"><span data-stu-id="8466a-258">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="8466a-259">在使用 CORS 的组件之前。</span><span class="sxs-lookup"><span data-stu-id="8466a-259">Before components that use CORS.</span></span> <span data-ttu-id="8466a-260">由于[此错误](https://github.com/dotnet/aspnetcore/issues/23218)，`UseCors` 当前必须在 `UseResponseCaching` 之前运行。</span><span class="sxs-lookup"><span data-stu-id="8466a-260">`UseCors` currently must go before `UseResponseCaching` due to [this bug](https://github.com/dotnet/aspnetcore/issues/23218).</span></span>|
| [<span data-ttu-id="8466a-261">诊断</span><span class="sxs-lookup"><span data-stu-id="8466a-261">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="8466a-262">提供新应用的开发人员异常页、异常处理、状态代码页和默认网页的几个单独的中间件。</span><span class="sxs-lookup"><span data-stu-id="8466a-262">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="8466a-263">在生成错误的组件之前。</span><span class="sxs-lookup"><span data-stu-id="8466a-263">Before components that generate errors.</span></span> <span data-ttu-id="8466a-264">异常终端或为新应用提供默认网页的终端。</span><span class="sxs-lookup"><span data-stu-id="8466a-264">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="8466a-265">转接头</span><span class="sxs-lookup"><span data-stu-id="8466a-265">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="8466a-266">将代理标头转发到当前请求。</span><span class="sxs-lookup"><span data-stu-id="8466a-266">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="8466a-267">在使用已更新字段的组件之前。</span><span class="sxs-lookup"><span data-stu-id="8466a-267">Before components that consume the updated fields.</span></span> <span data-ttu-id="8466a-268">示例：方案、主机、客户端 IP、方法。</span><span class="sxs-lookup"><span data-stu-id="8466a-268">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="8466a-269">运行状况检查</span><span class="sxs-lookup"><span data-stu-id="8466a-269">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="8466a-270">检查 ASP.NET Core 应用及其依赖项的运行状况，如检查数据库可用性。</span><span class="sxs-lookup"><span data-stu-id="8466a-270">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="8466a-271">如果请求与运行状况检查终结点匹配，则为终端。</span><span class="sxs-lookup"><span data-stu-id="8466a-271">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="8466a-272">标头传播</span><span class="sxs-lookup"><span data-stu-id="8466a-272">Header Propagation</span></span>](xref:fundamentals/http-requests#header-propagation-middleware) | <span data-ttu-id="8466a-273">将 HTTP 标头从传入的请求传播到传出的 HTTP 客户端请求中。</span><span class="sxs-lookup"><span data-stu-id="8466a-273">Propagates HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> |
| [<span data-ttu-id="8466a-274">HTTP 方法重写</span><span class="sxs-lookup"><span data-stu-id="8466a-274">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="8466a-275">允许传入 POST 请求重写方法。</span><span class="sxs-lookup"><span data-stu-id="8466a-275">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="8466a-276">在使用已更新方法的组件之前。</span><span class="sxs-lookup"><span data-stu-id="8466a-276">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="8466a-277">HTTPS 重定向</span><span class="sxs-lookup"><span data-stu-id="8466a-277">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="8466a-278">将所有 HTTP 请求重定向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="8466a-278">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="8466a-279">在使用 URL 的组件之前。</span><span class="sxs-lookup"><span data-stu-id="8466a-279">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="8466a-280">HTTP 严格传输安全性 (HSTS)</span><span class="sxs-lookup"><span data-stu-id="8466a-280">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="8466a-281">添加特殊响应标头的安全增强中间件。</span><span class="sxs-lookup"><span data-stu-id="8466a-281">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="8466a-282">在发送响应之前，修改请求的组件之后。</span><span class="sxs-lookup"><span data-stu-id="8466a-282">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="8466a-283">示例：转接头、URL 重写。</span><span class="sxs-lookup"><span data-stu-id="8466a-283">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="8466a-284">MVC</span><span class="sxs-lookup"><span data-stu-id="8466a-284">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="8466a-285">用 MVC/:::no-loc(Razor)::: Pages 处理请求。</span><span class="sxs-lookup"><span data-stu-id="8466a-285">Processes requests with MVC/:::no-loc(Razor)::: Pages.</span></span> | <span data-ttu-id="8466a-286">如果请求与路由匹配，则为终端。</span><span class="sxs-lookup"><span data-stu-id="8466a-286">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="8466a-287">OWIN</span><span class="sxs-lookup"><span data-stu-id="8466a-287">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="8466a-288">与基于 OWIN 的应用、服务器和中间件进行互操作。</span><span class="sxs-lookup"><span data-stu-id="8466a-288">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="8466a-289">如果 OWIN 中间件处理完请求，则为终端。</span><span class="sxs-lookup"><span data-stu-id="8466a-289">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="8466a-290">响应缓存</span><span class="sxs-lookup"><span data-stu-id="8466a-290">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="8466a-291">提供对缓存响应的支持。</span><span class="sxs-lookup"><span data-stu-id="8466a-291">Provides support for caching responses.</span></span> | <span data-ttu-id="8466a-292">在需要缓存的组件之前。</span><span class="sxs-lookup"><span data-stu-id="8466a-292">Before components that require caching.</span></span> <span data-ttu-id="8466a-293">`UseCORS` 必须在 `UseResponseCaching` 之前。</span><span class="sxs-lookup"><span data-stu-id="8466a-293">`UseCORS` must come before `UseResponseCaching`.</span></span>|
| [<span data-ttu-id="8466a-294">响应压缩</span><span class="sxs-lookup"><span data-stu-id="8466a-294">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="8466a-295">提供对压缩响应的支持。</span><span class="sxs-lookup"><span data-stu-id="8466a-295">Provides support for compressing responses.</span></span> | <span data-ttu-id="8466a-296">在需要压缩的组件之前。</span><span class="sxs-lookup"><span data-stu-id="8466a-296">Before components that require compression.</span></span> |
| [<span data-ttu-id="8466a-297">请求本地化</span><span class="sxs-lookup"><span data-stu-id="8466a-297">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="8466a-298">提供本地化支持。</span><span class="sxs-lookup"><span data-stu-id="8466a-298">Provides localization support.</span></span> | <span data-ttu-id="8466a-299">在对本地化敏感的组件之前。</span><span class="sxs-lookup"><span data-stu-id="8466a-299">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="8466a-300">终结点路由</span><span class="sxs-lookup"><span data-stu-id="8466a-300">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="8466a-301">定义和约束请求路由。</span><span class="sxs-lookup"><span data-stu-id="8466a-301">Defines and constrains request routes.</span></span> | <span data-ttu-id="8466a-302">用于匹配路由的终端。</span><span class="sxs-lookup"><span data-stu-id="8466a-302">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="8466a-303">SPA</span><span class="sxs-lookup"><span data-stu-id="8466a-303">SPA</span></span>](xref:Microsoft.AspNetCore.Builder.SpaApplicationBuilderExtensions.UseSpa%2A) | <span data-ttu-id="8466a-304">通过返回单页应用程序 (SPA) 的默认页面，在中间件链中处理来自这个点的所有请求</span><span class="sxs-lookup"><span data-stu-id="8466a-304">Handles all requests from this point in the middleware chain by returning the default page for the Single Page Application (SPA)</span></span> | <span data-ttu-id="8466a-305">在链中处于靠后位置，因此其他服务于静态文件、MVC 操作等内容的中间件占据优先位置。</span><span class="sxs-lookup"><span data-stu-id="8466a-305">Late in the chain, so that other middleware for serving static files, MVC actions, etc., takes precedence.</span></span>|
| [<span data-ttu-id="8466a-306">会话</span><span class="sxs-lookup"><span data-stu-id="8466a-306">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="8466a-307">提供对管理用户会话的支持。</span><span class="sxs-lookup"><span data-stu-id="8466a-307">Provides support for managing user sessions.</span></span> | <span data-ttu-id="8466a-308">在需要会话的组件之前。</span><span class="sxs-lookup"><span data-stu-id="8466a-308">Before components that require Session.</span></span> | 
| [<span data-ttu-id="8466a-309">静态文件</span><span class="sxs-lookup"><span data-stu-id="8466a-309">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="8466a-310">为提供静态文件和目录浏览提供支持。</span><span class="sxs-lookup"><span data-stu-id="8466a-310">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="8466a-311">如果请求与文件匹配，则为终端。</span><span class="sxs-lookup"><span data-stu-id="8466a-311">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="8466a-312">URL 重写</span><span class="sxs-lookup"><span data-stu-id="8466a-312">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="8466a-313">提供对重写 URL 和重定向请求的支持。</span><span class="sxs-lookup"><span data-stu-id="8466a-313">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="8466a-314">在使用 URL 的组件之前。</span><span class="sxs-lookup"><span data-stu-id="8466a-314">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="8466a-315">WebSockets</span><span class="sxs-lookup"><span data-stu-id="8466a-315">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="8466a-316">启用 WebSockets 协议。</span><span class="sxs-lookup"><span data-stu-id="8466a-316">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="8466a-317">在接受 WebSocket 请求所需的组件之前。</span><span class="sxs-lookup"><span data-stu-id="8466a-317">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="8466a-318">其他资源</span><span class="sxs-lookup"><span data-stu-id="8466a-318">Additional resources</span></span>

* <span data-ttu-id="8466a-319">[生存期和注册选项](xref:fundamentals/dependency-injection#lifetime-and-registration-options)包含已设置范围、临时性和单一实例生存期服务的中间件的完整示例  。</span><span class="sxs-lookup"><span data-stu-id="8466a-319">[Lifetime and registration options](xref:fundamentals/dependency-injection#lifetime-and-registration-options) contains a complete sample of middleware with *scoped* , *transient* , and *singleton* lifetime services.</span></span>
* <xref:fundamentals/middleware/write>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8466a-320">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="8466a-320">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="8466a-321">中间件是一种装配到应用管道以处理请求和响应的软件。</span><span class="sxs-lookup"><span data-stu-id="8466a-321">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="8466a-322">每个组件：</span><span class="sxs-lookup"><span data-stu-id="8466a-322">Each component:</span></span>

* <span data-ttu-id="8466a-323">选择是否将请求传递到管道中的下一个组件。</span><span class="sxs-lookup"><span data-stu-id="8466a-323">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="8466a-324">可在管道中的下一个组件前后执行工作。</span><span class="sxs-lookup"><span data-stu-id="8466a-324">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="8466a-325">请求委托用于生成请求管道。</span><span class="sxs-lookup"><span data-stu-id="8466a-325">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="8466a-326">请求委托处理每个 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="8466a-326">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="8466a-327">使用 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 和 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> 扩展方法来配置请求委托。</span><span class="sxs-lookup"><span data-stu-id="8466a-327">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> extension methods.</span></span> <span data-ttu-id="8466a-328">可将一个单独的请求委托并行指定为匿名方法（称为并行中间件），或在可重用的类中对其进行定义。</span><span class="sxs-lookup"><span data-stu-id="8466a-328">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="8466a-329">这些可重用的类和并行匿名方法即为中间件，也叫中间件组件。</span><span class="sxs-lookup"><span data-stu-id="8466a-329">These reusable classes and in-line anonymous methods are *middleware* , also called *middleware components* .</span></span> <span data-ttu-id="8466a-330">请求管道中的每个中间件组件负责调用管道中的下一个组件，或使管道短路。</span><span class="sxs-lookup"><span data-stu-id="8466a-330">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="8466a-331">当中间件短路时，它被称为“终端中间件”，因为它阻止中间件进一步处理请求。</span><span class="sxs-lookup"><span data-stu-id="8466a-331">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="8466a-332"><xref:migration/http-modules>介绍了 ASP.NET Core 和 ASP.NET 4.x 中请求管道之间的差异，并提供了更多的中间件示例。</span><span class="sxs-lookup"><span data-stu-id="8466a-332"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="8466a-333">使用 IApplicationBuilder 创建中间件管道</span><span class="sxs-lookup"><span data-stu-id="8466a-333">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="8466a-334">ASP.NET Core 请求管道包含一系列请求委托，依次调用。</span><span class="sxs-lookup"><span data-stu-id="8466a-334">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="8466a-335">下图演示了这一概念。</span><span class="sxs-lookup"><span data-stu-id="8466a-335">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="8466a-336">沿黑色箭头执行。</span><span class="sxs-lookup"><span data-stu-id="8466a-336">The thread of execution follows the black arrows.</span></span>

![请求处理模式显示请求到达、通过三个中间件进行处理以及响应离开应用。](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="8466a-340">每个委托均可在下一个委托前后执行操作。</span><span class="sxs-lookup"><span data-stu-id="8466a-340">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="8466a-341">应尽早在管道中调用异常处理委托，这样它们就能捕获在管道的后期阶段发生的异常。</span><span class="sxs-lookup"><span data-stu-id="8466a-341">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="8466a-342">尽可能简单的 ASP.NET Core 应用设置了处理所有请求的单个请求委托。</span><span class="sxs-lookup"><span data-stu-id="8466a-342">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="8466a-343">这种情况不包括实际请求管道。</span><span class="sxs-lookup"><span data-stu-id="8466a-343">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="8466a-344">调用单个匿名函数以响应每个 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="8466a-344">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="8466a-345">第一个 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> 委托终止了管道。</span><span class="sxs-lookup"><span data-stu-id="8466a-345">The first <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> delegate terminates the pipeline.</span></span>

<span data-ttu-id="8466a-346">用 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> 将多个请求委托链接在一起。</span><span class="sxs-lookup"><span data-stu-id="8466a-346">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>.</span></span> <span data-ttu-id="8466a-347">`next` 参数表示管道中的下一个委托。</span><span class="sxs-lookup"><span data-stu-id="8466a-347">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="8466a-348">可通过不调用 next 参数使管道短路。</span><span class="sxs-lookup"><span data-stu-id="8466a-348">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="8466a-349">通常可在下一个委托前后执行操作，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="8466a-349">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs)]

<span data-ttu-id="8466a-350">当委托不将请求传递给下一个委托时，它被称为“让请求管道短路”。</span><span class="sxs-lookup"><span data-stu-id="8466a-350">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline* .</span></span> <span data-ttu-id="8466a-351">通常需要短路，因为这样可以避免不必要的工作。</span><span class="sxs-lookup"><span data-stu-id="8466a-351">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="8466a-352">例如，[静态文件中间件](xref:fundamentals/static-files)可以处理对静态文件的请求，并让管道的其余部分短路，从而起到终端中间件的作用。</span><span class="sxs-lookup"><span data-stu-id="8466a-352">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="8466a-353">如果中间件添加到管道中，且位于终止进一步处理的中间件前，它们仍处理 `next.Invoke` 语句后面的代码。</span><span class="sxs-lookup"><span data-stu-id="8466a-353">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="8466a-354">不过，请参阅下面有关尝试对已发送的响应执行写入操作的警告。</span><span class="sxs-lookup"><span data-stu-id="8466a-354">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="8466a-355">在向客户端发送响应后，请勿调用 `next.Invoke`。</span><span class="sxs-lookup"><span data-stu-id="8466a-355">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="8466a-356">响应启动后，针对 <xref:Microsoft.AspNetCore.Http.HttpResponse> 的更改将引发异常。</span><span class="sxs-lookup"><span data-stu-id="8466a-356">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="8466a-357">例如，设置标头和状态代码更改将引发异常。</span><span class="sxs-lookup"><span data-stu-id="8466a-357">For example, changes such as setting headers and a status code throw an exception.</span></span> <span data-ttu-id="8466a-358">调用 `next` 后写入响应正文：</span><span class="sxs-lookup"><span data-stu-id="8466a-358">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="8466a-359">可能导致违反协议。</span><span class="sxs-lookup"><span data-stu-id="8466a-359">May cause a protocol violation.</span></span> <span data-ttu-id="8466a-360">例如，写入的长度超过规定的 `Content-Length`。</span><span class="sxs-lookup"><span data-stu-id="8466a-360">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="8466a-361">可能损坏正文格式。</span><span class="sxs-lookup"><span data-stu-id="8466a-361">May corrupt the body format.</span></span> <span data-ttu-id="8466a-362">例如，向 CSS 文件中写入 HTML 页脚。</span><span class="sxs-lookup"><span data-stu-id="8466a-362">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="8466a-363"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> 是一个有用的提示，指示是否已发送标头或已写入正文。</span><span class="sxs-lookup"><span data-stu-id="8466a-363"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

<a name="order"></a>

## <a name="middleware-order"></a><span data-ttu-id="8466a-364">中间件顺序</span><span class="sxs-lookup"><span data-stu-id="8466a-364">Middleware order</span></span>

<span data-ttu-id="8466a-365">向 `Startup.Configure` 方法添加中间件组件的顺序定义了针对请求调用这些组件的顺序，以及响应的相反顺序。</span><span class="sxs-lookup"><span data-stu-id="8466a-365">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="8466a-366">此顺序对于安全性、性能和功能至关重要。</span><span class="sxs-lookup"><span data-stu-id="8466a-366">The order is **critical** for security, performance, and functionality.</span></span>

<span data-ttu-id="8466a-367">下面的 `Startup.Configure` 方法按照建议的顺序增加与安全相关的中间件组件：</span><span class="sxs-lookup"><span data-stu-id="8466a-367">The following `Startup.Configure` method adds security related middleware components in the recommended order:</span></span>

[!code-csharp[](index/snapshot/Startup22.cs?name=snippet)]

<span data-ttu-id="8466a-368">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="8466a-368">In the preceding code:</span></span>

* <span data-ttu-id="8466a-369">在使用[单个用户帐户](xref:security/authentication/identity)创建新的 Web 应用时未添加的中间件已被注释掉。</span><span class="sxs-lookup"><span data-stu-id="8466a-369">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="8466a-370">并非所有中间件都需要准确按照此顺序运行，但许多中间件必须遵循这个顺序。</span><span class="sxs-lookup"><span data-stu-id="8466a-370">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="8466a-371">例如，`UseCors` 和 `UseAuthentication` 必须按照上述顺序运行。</span><span class="sxs-lookup"><span data-stu-id="8466a-371">For example, `UseCors` and `UseAuthentication` must go in the order shown.</span></span>

<span data-ttu-id="8466a-372">以下 `Startup.Configure` 方法将为常见应用方案添加中间件组件：</span><span class="sxs-lookup"><span data-stu-id="8466a-372">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="8466a-373">异常/错误处理</span><span class="sxs-lookup"><span data-stu-id="8466a-373">Exception/error handling</span></span>
   * <span data-ttu-id="8466a-374">当应用在开发环境中运行时：</span><span class="sxs-lookup"><span data-stu-id="8466a-374">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="8466a-375">开发人员异常页中间件 (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>) 报告应用运行时错误。</span><span class="sxs-lookup"><span data-stu-id="8466a-375">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>) reports app runtime errors.</span></span>
     * <span data-ttu-id="8466a-376">数据库错误页中间件 (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) 报告数据库运行时错误。</span><span class="sxs-lookup"><span data-stu-id="8466a-376">Database Error Page Middleware (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) reports database runtime errors.</span></span>
   * <span data-ttu-id="8466a-377">当应用在生产环境中运行时：</span><span class="sxs-lookup"><span data-stu-id="8466a-377">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="8466a-378">异常处理程序中间件 (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>) 捕获以下中间件中引发的异常。</span><span class="sxs-lookup"><span data-stu-id="8466a-378">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="8466a-379">HTTP 严格传输安全协议 (HSTS) 中间件 (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>) 添加 `Strict-Transport-Security` 标头。</span><span class="sxs-lookup"><span data-stu-id="8466a-379">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="8466a-380">HTTPS 重定向中间件 (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>) 将 HTTP 请求重定向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="8466a-380">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="8466a-381">静态文件中间件 (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>) 返回静态文件，并简化进一步请求处理。</span><span class="sxs-lookup"><span data-stu-id="8466a-381">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="8466a-382">:::no-loc(Cookie)::: 策略中间件 (<xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyAppBuilderExtensions.Use:::no-loc(Cookie):::Policy%2A>) 使应用符合欧盟一般数据保护条例 (GDPR) 规定。</span><span class="sxs-lookup"><span data-stu-id="8466a-382">:::no-loc(Cookie)::: Policy Middleware (<xref:Microsoft.AspNetCore.Builder.:::no-loc(Cookie):::PolicyAppBuilderExtensions.Use:::no-loc(Cookie):::Policy%2A>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="8466a-383">身份验证中间件 (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) 尝试对用户进行身份验证，然后才会允许用户访问安全资源。</span><span class="sxs-lookup"><span data-stu-id="8466a-383">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="8466a-384">会话中间件 (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>) 建立和维护会话状态。</span><span class="sxs-lookup"><span data-stu-id="8466a-384">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>) establishes and maintains session state.</span></span> <span data-ttu-id="8466a-385">如果应用使用会话状态，请在 :::no-loc(Cookie)::: 策略中间件之后和 MVC 中间件之前调用会话中间件。</span><span class="sxs-lookup"><span data-stu-id="8466a-385">If the app uses session state, call Session Middleware after :::no-loc(Cookie)::: Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="8466a-386">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>) 将 MVC 添加到请求管道。</span><span class="sxs-lookup"><span data-stu-id="8466a-386">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>) to add MVC to the request pipeline.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.Use:::no-loc(Cookie):::Policy();
    app.UseAuthentication();
    app.UseSession();
    app.UseMvc();
}
```

<span data-ttu-id="8466a-387">在前面的示例代码中，每个中间件扩展方法都通过 <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 命名空间在 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> 上公开。</span><span class="sxs-lookup"><span data-stu-id="8466a-387">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="8466a-388"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> 是添加到管道的第一个中间件组件。</span><span class="sxs-lookup"><span data-stu-id="8466a-388"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="8466a-389">因此，异常处理程序中间件可捕获稍后调用中发生的任何异常。</span><span class="sxs-lookup"><span data-stu-id="8466a-389">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="8466a-390">尽早在管道中调用静态文件中间件，以便它可以处理请求并使其短路，而无需通过剩余组件。</span><span class="sxs-lookup"><span data-stu-id="8466a-390">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="8466a-391">静态文件中间件不提供授权检查。</span><span class="sxs-lookup"><span data-stu-id="8466a-391">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="8466a-392">可公开访问由静态文件中间件服务的任何文件，包括 wwwroot 下的文件。</span><span class="sxs-lookup"><span data-stu-id="8466a-392">Any files served by Static File Middleware, including those under *wwwroot* , are publicly available.</span></span> <span data-ttu-id="8466a-393">若要了解如何保护静态文件，请参阅 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="8466a-393">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="8466a-394">如果静态文件中间件未处理请求，则请求将被传递给执行身份验证的身份验证中间件 (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>)。</span><span class="sxs-lookup"><span data-stu-id="8466a-394">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>), which performs authentication.</span></span> <span data-ttu-id="8466a-395">身份验证不使未经身份验证的请求短路。</span><span class="sxs-lookup"><span data-stu-id="8466a-395">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="8466a-396">虽然身份验证中间件对请求进行身份验证，但仅在 MVC 选择特定 :::no-loc(Razor)::: Page 或 MVC 控制器和操作后，才发生授权（和拒绝）。</span><span class="sxs-lookup"><span data-stu-id="8466a-396">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific :::no-loc(Razor)::: Page or MVC controller and action.</span></span>

<span data-ttu-id="8466a-397">以下示例演示中间件排序，其中静态文件的请求在响应压缩中间件前由静态文件中间件进行处理。</span><span class="sxs-lookup"><span data-stu-id="8466a-397">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="8466a-398">使用此中间件顺序不压缩静态文件。</span><span class="sxs-lookup"><span data-stu-id="8466a-398">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="8466a-399">可以压缩来自 <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> 的 MVC 响应。</span><span class="sxs-lookup"><span data-stu-id="8466a-399">The MVC responses from <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseMvcWithDefaultRoute();
}
```

## <a name="use-run-and-map"></a><span data-ttu-id="8466a-400">Use、Run 和 Map</span><span class="sxs-lookup"><span data-stu-id="8466a-400">Use, Run, and Map</span></span>

<span data-ttu-id="8466a-401">使用 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>、<xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> 和 <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 配置 HTTP 管道。</span><span class="sxs-lookup"><span data-stu-id="8466a-401">Configure the HTTP pipeline using <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, and <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>.</span></span> <span data-ttu-id="8466a-402">`Use` 方法可使管道短路（即不调用 `next` 请求委托）。</span><span class="sxs-lookup"><span data-stu-id="8466a-402">The `Use` method can short-circuit the pipeline (that is, if it doesn't call a `next` request delegate).</span></span> <span data-ttu-id="8466a-403">`Run` 是一种约定，并且某些中间件组件可公开在管道末尾运行的 `Run[Middleware]` 方法。</span><span class="sxs-lookup"><span data-stu-id="8466a-403">`Run` is a convention, and some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline.</span></span>

<span data-ttu-id="8466a-404"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 扩展用作约定来创建管道分支。</span><span class="sxs-lookup"><span data-stu-id="8466a-404"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="8466a-405">`Map` 基于给定请求路径的匹配项来创建请求管道分支。</span><span class="sxs-lookup"><span data-stu-id="8466a-405">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="8466a-406">如果请求路径以给定路径开头，则执行分支。</span><span class="sxs-lookup"><span data-stu-id="8466a-406">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="8466a-407">下表使用前面的代码显示来自 `http://localhost:1234` 的请求和响应。</span><span class="sxs-lookup"><span data-stu-id="8466a-407">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="8466a-408">请求</span><span class="sxs-lookup"><span data-stu-id="8466a-408">Request</span></span>             | <span data-ttu-id="8466a-409">响应</span><span class="sxs-lookup"><span data-stu-id="8466a-409">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="8466a-410">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="8466a-410">localhost:1234</span></span>      | <span data-ttu-id="8466a-411">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="8466a-411">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="8466a-412">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="8466a-412">localhost:1234/map1</span></span> | <span data-ttu-id="8466a-413">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="8466a-413">Map Test 1</span></span>                   |
| <span data-ttu-id="8466a-414">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="8466a-414">localhost:1234/map2</span></span> | <span data-ttu-id="8466a-415">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="8466a-415">Map Test 2</span></span>                   |
| <span data-ttu-id="8466a-416">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="8466a-416">localhost:1234/map3</span></span> | <span data-ttu-id="8466a-417">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="8466a-417">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="8466a-418">使用 `Map` 时，将从 `HttpRequest.Path` 中删除匹配的路径段，并针对每个请求将该路径段追加到 `HttpRequest.PathBase`。</span><span class="sxs-lookup"><span data-stu-id="8466a-418">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="8466a-419"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> 基于给定谓词的结果创建请求管道分支。</span><span class="sxs-lookup"><span data-stu-id="8466a-419"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="8466a-420">`Func<HttpContext, bool>` 类型的任何谓词均可用于将请求映射到管道的新分支。</span><span class="sxs-lookup"><span data-stu-id="8466a-420">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="8466a-421">在以下示例中，谓词用于检测查询字符串变量 `branch` 是否存在：</span><span class="sxs-lookup"><span data-stu-id="8466a-421">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs)]

<span data-ttu-id="8466a-422">下表使用前面的代码显示来自 `http://localhost:1234` 的请求和响应。</span><span class="sxs-lookup"><span data-stu-id="8466a-422">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="8466a-423">请求</span><span class="sxs-lookup"><span data-stu-id="8466a-423">Request</span></span>                       | <span data-ttu-id="8466a-424">响应</span><span class="sxs-lookup"><span data-stu-id="8466a-424">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="8466a-425">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="8466a-425">localhost:1234</span></span>                | <span data-ttu-id="8466a-426">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="8466a-426">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="8466a-427">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="8466a-427">localhost:1234/?branch=master</span></span> | <span data-ttu-id="8466a-428">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="8466a-428">Branch used = master</span></span>         |

<span data-ttu-id="8466a-429">`Map` 支持嵌套，例如：</span><span class="sxs-lookup"><span data-stu-id="8466a-429">`Map` supports nesting, for example:</span></span>

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
```

<span data-ttu-id="8466a-430">此外，`Map` 还可同时匹配多个段：</span><span class="sxs-lookup"><span data-stu-id="8466a-430">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

## <a name="built-in-middleware"></a><span data-ttu-id="8466a-431">内置中间件</span><span class="sxs-lookup"><span data-stu-id="8466a-431">Built-in middleware</span></span>

<span data-ttu-id="8466a-432">ASP.NET Core 附带以下中间件组件。</span><span class="sxs-lookup"><span data-stu-id="8466a-432">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="8466a-433">“顺序”列提供备注，以说明中间件在请求处理管道中的放置，以及中间件可能会终止请求处理的条件。</span><span class="sxs-lookup"><span data-stu-id="8466a-433">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="8466a-434">如果中间件让请求处理管道短路，并阻止下游中间件进一步处理请求，它被称为“终端中间件”。</span><span class="sxs-lookup"><span data-stu-id="8466a-434">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware* .</span></span> <span data-ttu-id="8466a-435">若要详细了解短路，请参阅[使用 IApplicationBuilder 创建中间件管道](#create-a-middleware-pipeline-with-iapplicationbuilder)部分。</span><span class="sxs-lookup"><span data-stu-id="8466a-435">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="8466a-436">中间件</span><span class="sxs-lookup"><span data-stu-id="8466a-436">Middleware</span></span> | <span data-ttu-id="8466a-437">描述</span><span class="sxs-lookup"><span data-stu-id="8466a-437">Description</span></span> | <span data-ttu-id="8466a-438">顺序</span><span class="sxs-lookup"><span data-stu-id="8466a-438">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="8466a-439">身份验证</span><span class="sxs-lookup"><span data-stu-id="8466a-439">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="8466a-440">提供身份验证支持。</span><span class="sxs-lookup"><span data-stu-id="8466a-440">Provides authentication support.</span></span> | <span data-ttu-id="8466a-441">在需要 `HttpContext.User` 之前。</span><span class="sxs-lookup"><span data-stu-id="8466a-441">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="8466a-442">OAuth 回叫的终端。</span><span class="sxs-lookup"><span data-stu-id="8466a-442">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="8466a-443">:::no-loc(Cookie)::: 策略</span><span class="sxs-lookup"><span data-stu-id="8466a-443">:::no-loc(Cookie)::: Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="8466a-444">跟踪用户是否同意存储个人信息，并强制实施 :::no-loc(cookie)::: 字段（如 `secure` 和 `SameSite`）的最低标准。</span><span class="sxs-lookup"><span data-stu-id="8466a-444">Tracks consent from users for storing personal information and enforces minimum standards for :::no-loc(cookie)::: fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="8466a-445">在发出 :::no-loc(cookie)::: 的中间件之前。</span><span class="sxs-lookup"><span data-stu-id="8466a-445">Before middleware that issues :::no-loc(cookie):::s.</span></span> <span data-ttu-id="8466a-446">示例：身份验证、会话、MVC (TempData)。</span><span class="sxs-lookup"><span data-stu-id="8466a-446">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="8466a-447">CORS</span><span class="sxs-lookup"><span data-stu-id="8466a-447">CORS</span></span>](xref:security/cors) | <span data-ttu-id="8466a-448">配置跨域资源共享。</span><span class="sxs-lookup"><span data-stu-id="8466a-448">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="8466a-449">在使用 CORS 的组件之前。</span><span class="sxs-lookup"><span data-stu-id="8466a-449">Before components that use CORS.</span></span> |
| [<span data-ttu-id="8466a-450">诊断</span><span class="sxs-lookup"><span data-stu-id="8466a-450">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="8466a-451">提供新应用的开发人员异常页、异常处理、状态代码页和默认网页的几个单独的中间件。</span><span class="sxs-lookup"><span data-stu-id="8466a-451">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="8466a-452">在生成错误的组件之前。</span><span class="sxs-lookup"><span data-stu-id="8466a-452">Before components that generate errors.</span></span> <span data-ttu-id="8466a-453">异常终端或为新应用提供默认网页的终端。</span><span class="sxs-lookup"><span data-stu-id="8466a-453">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="8466a-454">转接头</span><span class="sxs-lookup"><span data-stu-id="8466a-454">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="8466a-455">将代理标头转发到当前请求。</span><span class="sxs-lookup"><span data-stu-id="8466a-455">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="8466a-456">在使用已更新字段的组件之前。</span><span class="sxs-lookup"><span data-stu-id="8466a-456">Before components that consume the updated fields.</span></span> <span data-ttu-id="8466a-457">示例：方案、主机、客户端 IP、方法。</span><span class="sxs-lookup"><span data-stu-id="8466a-457">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="8466a-458">运行状况检查</span><span class="sxs-lookup"><span data-stu-id="8466a-458">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="8466a-459">检查 ASP.NET Core 应用及其依赖项的运行状况，如检查数据库可用性。</span><span class="sxs-lookup"><span data-stu-id="8466a-459">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="8466a-460">如果请求与运行状况检查终结点匹配，则为终端。</span><span class="sxs-lookup"><span data-stu-id="8466a-460">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="8466a-461">HTTP 方法重写</span><span class="sxs-lookup"><span data-stu-id="8466a-461">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="8466a-462">允许传入 POST 请求重写方法。</span><span class="sxs-lookup"><span data-stu-id="8466a-462">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="8466a-463">在使用已更新方法的组件之前。</span><span class="sxs-lookup"><span data-stu-id="8466a-463">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="8466a-464">HTTPS 重定向</span><span class="sxs-lookup"><span data-stu-id="8466a-464">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="8466a-465">将所有 HTTP 请求重定向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="8466a-465">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="8466a-466">在使用 URL 的组件之前。</span><span class="sxs-lookup"><span data-stu-id="8466a-466">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="8466a-467">HTTP 严格传输安全性 (HSTS)</span><span class="sxs-lookup"><span data-stu-id="8466a-467">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="8466a-468">添加特殊响应标头的安全增强中间件。</span><span class="sxs-lookup"><span data-stu-id="8466a-468">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="8466a-469">在发送响应之前，修改请求的组件之后。</span><span class="sxs-lookup"><span data-stu-id="8466a-469">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="8466a-470">示例：转接头、URL 重写。</span><span class="sxs-lookup"><span data-stu-id="8466a-470">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="8466a-471">MVC</span><span class="sxs-lookup"><span data-stu-id="8466a-471">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="8466a-472">用 MVC/:::no-loc(Razor)::: Pages 处理请求。</span><span class="sxs-lookup"><span data-stu-id="8466a-472">Processes requests with MVC/:::no-loc(Razor)::: Pages.</span></span> | <span data-ttu-id="8466a-473">如果请求与路由匹配，则为终端。</span><span class="sxs-lookup"><span data-stu-id="8466a-473">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="8466a-474">OWIN</span><span class="sxs-lookup"><span data-stu-id="8466a-474">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="8466a-475">与基于 OWIN 的应用、服务器和中间件进行互操作。</span><span class="sxs-lookup"><span data-stu-id="8466a-475">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="8466a-476">如果 OWIN 中间件处理完请求，则为终端。</span><span class="sxs-lookup"><span data-stu-id="8466a-476">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="8466a-477">响应缓存</span><span class="sxs-lookup"><span data-stu-id="8466a-477">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="8466a-478">提供对缓存响应的支持。</span><span class="sxs-lookup"><span data-stu-id="8466a-478">Provides support for caching responses.</span></span> | <span data-ttu-id="8466a-479">在需要缓存的组件之前。</span><span class="sxs-lookup"><span data-stu-id="8466a-479">Before components that require caching.</span></span> |
| [<span data-ttu-id="8466a-480">响应压缩</span><span class="sxs-lookup"><span data-stu-id="8466a-480">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="8466a-481">提供对压缩响应的支持。</span><span class="sxs-lookup"><span data-stu-id="8466a-481">Provides support for compressing responses.</span></span> | <span data-ttu-id="8466a-482">在需要压缩的组件之前。</span><span class="sxs-lookup"><span data-stu-id="8466a-482">Before components that require compression.</span></span> |
| [<span data-ttu-id="8466a-483">请求本地化</span><span class="sxs-lookup"><span data-stu-id="8466a-483">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="8466a-484">提供本地化支持。</span><span class="sxs-lookup"><span data-stu-id="8466a-484">Provides localization support.</span></span> | <span data-ttu-id="8466a-485">在对本地化敏感的组件之前。</span><span class="sxs-lookup"><span data-stu-id="8466a-485">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="8466a-486">终结点路由</span><span class="sxs-lookup"><span data-stu-id="8466a-486">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="8466a-487">定义和约束请求路由。</span><span class="sxs-lookup"><span data-stu-id="8466a-487">Defines and constrains request routes.</span></span> | <span data-ttu-id="8466a-488">用于匹配路由的终端。</span><span class="sxs-lookup"><span data-stu-id="8466a-488">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="8466a-489">会话</span><span class="sxs-lookup"><span data-stu-id="8466a-489">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="8466a-490">提供对管理用户会话的支持。</span><span class="sxs-lookup"><span data-stu-id="8466a-490">Provides support for managing user sessions.</span></span> | <span data-ttu-id="8466a-491">在需要会话的组件之前。</span><span class="sxs-lookup"><span data-stu-id="8466a-491">Before components that require Session.</span></span> |
| [<span data-ttu-id="8466a-492">静态文件</span><span class="sxs-lookup"><span data-stu-id="8466a-492">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="8466a-493">为提供静态文件和目录浏览提供支持。</span><span class="sxs-lookup"><span data-stu-id="8466a-493">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="8466a-494">如果请求与文件匹配，则为终端。</span><span class="sxs-lookup"><span data-stu-id="8466a-494">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="8466a-495">URL 重写</span><span class="sxs-lookup"><span data-stu-id="8466a-495">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="8466a-496">提供对重写 URL 和重定向请求的支持。</span><span class="sxs-lookup"><span data-stu-id="8466a-496">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="8466a-497">在使用 URL 的组件之前。</span><span class="sxs-lookup"><span data-stu-id="8466a-497">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="8466a-498">WebSockets</span><span class="sxs-lookup"><span data-stu-id="8466a-498">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="8466a-499">启用 WebSockets 协议。</span><span class="sxs-lookup"><span data-stu-id="8466a-499">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="8466a-500">在接受 WebSocket 请求所需的组件之前。</span><span class="sxs-lookup"><span data-stu-id="8466a-500">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="8466a-501">其他资源</span><span class="sxs-lookup"><span data-stu-id="8466a-501">Additional resources</span></span>

* <xref:fundamentals/middleware/write>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end
