---
title: ASP.NET Core 的身份验证示例
author: rick-anderson
description: 提供指向 ASP.NET Core 存储库中的身份验证示例的链接。
ms.author: riande
ms.date: 01/31/2019
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
uid: security/authentication/samples
ms.openlocfilehash: 4153a443748dbff40be19e25fc1c719ee4e39609
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060334"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="823c8-103">ASP.NET Core 的身份验证示例</span><span class="sxs-lookup"><span data-stu-id="823c8-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="823c8-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="823c8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="823c8-105">[ASP.NET Core 存储库](https://github.com/dotnet/AspNetCore)包含 *AspNetCore/src/Security/samples* 文件夹中的以下身份验证示例：</span><span class="sxs-lookup"><span data-stu-id="823c8-105">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="823c8-106">声明转换</span><span class="sxs-lookup"><span data-stu-id="823c8-106">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* <span data-ttu-id="823c8-107">[:::no-loc(Cookie)::: 验证](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/:::no-loc(Cookie):::s)</span><span class="sxs-lookup"><span data-stu-id="823c8-107">[:::no-loc(Cookie)::: authentication](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/:::no-loc(Cookie):::s)</span></span>
* [<span data-ttu-id="823c8-108">自定义策略提供程序-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="823c8-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="823c8-109">动态身份验证方案和选项</span><span class="sxs-lookup"><span data-stu-id="823c8-109">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="823c8-110">[外部声明](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/:::no-loc(Identity):::.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="823c8-110">[External claims](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/:::no-loc(Identity):::.ExternalClaims)</span></span>
* [<span data-ttu-id="823c8-111">:::no-loc(cookie):::基于请求选择和其他身份验证方案</span><span class="sxs-lookup"><span data-stu-id="823c8-111">Selecting between :::no-loc(cookie)::: and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="823c8-112">限制对静态文件的访问</span><span class="sxs-lookup"><span data-stu-id="823c8-112">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="823c8-113">运行示例</span><span class="sxs-lookup"><span data-stu-id="823c8-113">Run the samples</span></span>

* <span data-ttu-id="823c8-114">选择 [分支](https://github.com/dotnet/AspNetCore)。</span><span class="sxs-lookup"><span data-stu-id="823c8-114">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="823c8-115">例如： `release/3.1`</span><span class="sxs-lookup"><span data-stu-id="823c8-115">For example, `release/3.1`</span></span>
* <span data-ttu-id="823c8-116">克隆或下载 [ASP.NET Core 存储库](https://github.com/dotnet/AspNetCore)。</span><span class="sxs-lookup"><span data-stu-id="823c8-116">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="823c8-117">验证是否已安装与 ASP.NET Core 存储库的克隆相匹配的 [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) 版本。</span><span class="sxs-lookup"><span data-stu-id="823c8-117">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="823c8-118">导航到 *AspNetCore/src/Security/samples* 中的示例，并使用运行该示例 `dotnet run` 。</span><span class="sxs-lookup"><span data-stu-id="823c8-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="823c8-119">[ASP.NET Core 存储库](https://github.com/dotnet/AspNetCore)包含 *AspNetCore/src/Security/samples* 文件夹中的以下身份验证示例：</span><span class="sxs-lookup"><span data-stu-id="823c8-119">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="823c8-120">声明转换</span><span class="sxs-lookup"><span data-stu-id="823c8-120">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/ClaimsTransformation)
* <span data-ttu-id="823c8-121">[:::no-loc(Cookie)::: 验证](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/:::no-loc(Cookie):::s)</span><span class="sxs-lookup"><span data-stu-id="823c8-121">[:::no-loc(Cookie)::: authentication](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/:::no-loc(Cookie):::s)</span></span>
* [<span data-ttu-id="823c8-122">自定义策略提供程序-IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="823c8-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="823c8-123">动态身份验证方案和选项</span><span class="sxs-lookup"><span data-stu-id="823c8-123">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="823c8-124">[外部声明](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/:::no-loc(Identity):::.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="823c8-124">[External claims](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/:::no-loc(Identity):::.ExternalClaims)</span></span>
* [<span data-ttu-id="823c8-125">:::no-loc(cookie):::基于请求选择和其他身份验证方案</span><span class="sxs-lookup"><span data-stu-id="823c8-125">Selecting between :::no-loc(cookie)::: and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="823c8-126">限制对静态文件的访问</span><span class="sxs-lookup"><span data-stu-id="823c8-126">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="823c8-127">运行示例</span><span class="sxs-lookup"><span data-stu-id="823c8-127">Run the samples</span></span>

* <span data-ttu-id="823c8-128">选择 [分支](https://github.com/dotnet/AspNetCore)。</span><span class="sxs-lookup"><span data-stu-id="823c8-128">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="823c8-129">例如： `release/2.1`</span><span class="sxs-lookup"><span data-stu-id="823c8-129">For example, `release/2.1`</span></span>
* <span data-ttu-id="823c8-130">克隆或下载 [ASP.NET Core 存储库](https://github.com/dotnet/AspNetCore)。</span><span class="sxs-lookup"><span data-stu-id="823c8-130">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="823c8-131">验证是否已安装与 ASP.NET Core 存储库的克隆相匹配的 [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) 版本。</span><span class="sxs-lookup"><span data-stu-id="823c8-131">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="823c8-132">导航到 *AspNetCore/src/Security/samples* 中的示例，并使用运行该示例 `dotnet run` 。</span><span class="sxs-lookup"><span data-stu-id="823c8-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
