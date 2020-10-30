---
title: ASP.NET Core 中的脚本标记帮助程序
author: rick-anderson
ms.author: riande
description: 了解 ASP.NET Core 脚本标记帮助程序属性以及每个属性在扩展 HTML 脚本标记的行为中所起的作用。
ms.custom: mvc
ms.date: 12/02/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: mvc/views/tag-helpers/builtin-th/script-tag-helper
ms.openlocfilehash: f5856bf19681a42551f82bb15c769f192f338b4a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053496"
---
# <a name="script-tag-helper-in-aspnet-core"></a><span data-ttu-id="140ac-103">ASP.NET Core 中的脚本标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="140ac-103">Script Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="140ac-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="140ac-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="140ac-105">[标记帮助程序](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper)用于生成指向主要或回退脚本文件的链接。</span><span class="sxs-lookup"><span data-stu-id="140ac-105">The [Script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) generates a link to a primary or fall back script file.</span></span> <span data-ttu-id="140ac-106">通常主脚本文件位于[内容分发网络](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN)。</span><span class="sxs-lookup"><span data-stu-id="140ac-106">Typically the primary script file is on a [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span></span>

[!INCLUDE[](~/includes/cdn.md)]

<span data-ttu-id="140ac-107">可以使用脚本标记帮助程序指定脚本文件的 CDN 以及回退文件（CDN 不可用时）。</span><span class="sxs-lookup"><span data-stu-id="140ac-107">The Script Tag Helper allows you to specify a CDN for the script file and a fallback when the CDN is not available.</span></span> <span data-ttu-id="140ac-108">脚本标记帮助程序借助本地宿主的可靠性提供 CDN 性能优势。</span><span class="sxs-lookup"><span data-stu-id="140ac-108">The Script Tag Helper provides the performance advantage of a CDN with the robustness of local hosting.</span></span>

<span data-ttu-id="140ac-109">以下 :::no-loc(Razor)::: 标记显示了 `script` 具有回退的元素：</span><span class="sxs-lookup"><span data-stu-id="140ac-109">The following :::no-loc(Razor)::: markup shows a `script` element with a fallback:</span></span>

```html
<script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-3.3.1.min.js"
        asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
        asp-fallback-test="window.jQuery"
        crossorigin="anonymous"
        integrity="sha384-tsQFqpEReu7ZLhBV2VZlAu7zcOV+rXbYlF2cqB8txI/8aZajjp4Bqd+V6D5IgvKT">
</script>
```

<span data-ttu-id="140ac-110">请勿使用 `<script>` 元素的 [](https://developer.mozilla.org/docs/Web/HTML/Element/script) 属性来延迟加载 CDN 脚本。</span><span class="sxs-lookup"><span data-stu-id="140ac-110">Don't use the `<script>` element's [defer](https://developer.mozilla.org/docs/Web/HTML/Element/script) attribute to defer loading the CDN script.</span></span> <span data-ttu-id="140ac-111">脚本标记帮助程序呈现能够立即执行 [asp-fallback-test](#asp-fallback-test) 表达式的 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="140ac-111">The Script Tag Helper renders JavaScript that immediately executes the [asp-fallback-test](#asp-fallback-test) expression.</span></span> <span data-ttu-id="140ac-112">如果延迟加载 CDN 脚本，则该表达式失败。</span><span class="sxs-lookup"><span data-stu-id="140ac-112">The expression fails if loading the CDN script is deferred.</span></span>

## <a name="commonly-used-script-tag-helper-attributes"></a><span data-ttu-id="140ac-113">常用的脚本标记帮助程序属性</span><span class="sxs-lookup"><span data-stu-id="140ac-113">Commonly used Script Tag Helper attributes</span></span>

<span data-ttu-id="140ac-114">若要了解所有脚本标记帮助程序属性和方法，请参阅[标记帮助程序](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper)。</span><span class="sxs-lookup"><span data-stu-id="140ac-114">See [Script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) for all the Script Tag Helper attributes, properties, and methods.</span></span>

### <a name="asp-fallback-test"></a><span data-ttu-id="140ac-115">asp-fallback-test</span><span class="sxs-lookup"><span data-stu-id="140ac-115">asp-fallback-test</span></span>

<span data-ttu-id="140ac-116">主脚本中定义的用于回退测试的脚本方法。</span><span class="sxs-lookup"><span data-stu-id="140ac-116">The script method defined in the primary script to use for the fallback test.</span></span> <span data-ttu-id="140ac-117">有关详细信息，请参阅 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackTestExpression>。</span><span class="sxs-lookup"><span data-stu-id="140ac-117">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackTestExpression>.</span></span>

### <a name="asp-fallback-src"></a><span data-ttu-id="140ac-118">asp-fallback-src</span><span class="sxs-lookup"><span data-stu-id="140ac-118">asp-fallback-src</span></span>

<span data-ttu-id="140ac-119">主 URL 失效后要回退到的脚本标签的 URL。</span><span class="sxs-lookup"><span data-stu-id="140ac-119">The URL of a Script tag to fallback to in the case the primary one fails.</span></span> <span data-ttu-id="140ac-120">有关详细信息，请参阅 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackSrc>。</span><span class="sxs-lookup"><span data-stu-id="140ac-120">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackSrc>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="140ac-121">其他资源</span><span class="sxs-lookup"><span data-stu-id="140ac-121">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
