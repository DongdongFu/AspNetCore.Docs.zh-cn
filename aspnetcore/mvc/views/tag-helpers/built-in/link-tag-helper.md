---
title: ASP.NET Core 中的链接标记帮助程序
author: rick-anderson
ms.author: riande
description: 了解 ASP.NET Core 链接标记帮助程序属性以及每个属性在扩展 HTML 链接标记的行为中所起的作用。
ms.custom: mvc
ms.date: 09/24/2019
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
uid: mvc/views/tag-helpers/builtin-th/link-tag-helper
ms.openlocfilehash: 7998cec94ebb56cbe6dbc321f7cb499260d6fe74
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059112"
---
# <a name="link-tag-helper-in-aspnet-core"></a><span data-ttu-id="fae95-103">ASP.NET Core 中的链接标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="fae95-103">Link Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="fae95-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="fae95-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="fae95-105">[链接标记帮助程序](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper)用于生成指向主要或回退 CSS 文件的链接。</span><span class="sxs-lookup"><span data-stu-id="fae95-105">The [Link Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) generates a link to a primary or fall back CSS file.</span></span> <span data-ttu-id="fae95-106">通常主 CSS 文件位于[内容分发网络](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN)。</span><span class="sxs-lookup"><span data-stu-id="fae95-106">Typically the primary CSS file is on a [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span></span>

[!INCLUDE[](~/includes/cdn.md)]

<span data-ttu-id="fae95-107">可以使用链接标记帮助程序指定 CSS 文件的 CDN 以及回退文件（CDN 不可用时）。</span><span class="sxs-lookup"><span data-stu-id="fae95-107">The Link Tag Helper allows you to specify a CDN for the CSS file and a fallback when the CDN is not available.</span></span> <span data-ttu-id="fae95-108">链接标记帮助程序借助本地宿主的可靠性提供 CDN 性能优势。</span><span class="sxs-lookup"><span data-stu-id="fae95-108">The Link Tag Helper provides the performance advantage of a CDN with the robustness of local hosting.</span></span>

<span data-ttu-id="fae95-109">以下 :::no-loc(Razor)::: 标记显示 `head` 使用 ASP.NET Core web 应用程序模板创建的布局文件的元素：</span><span class="sxs-lookup"><span data-stu-id="fae95-109">The following :::no-loc(Razor)::: markup shows the `head` element of a layout file created with the ASP.NET Core web app template:</span></span>

[!code-cshtml[](link-tag-helper/sample/_Layout.cshtml?name=snippet)]

<span data-ttu-id="fae95-110">以下是上述代码呈现的 HTML（非开发环境）：</span><span class="sxs-lookup"><span data-stu-id="fae95-110">The following is rendered HTML from the preceding code (in a non-Development environment):</span></span>

[!code-html[](link-tag-helper/sample/HtmlPage1.html)]

<span data-ttu-id="fae95-111">在上述代码中，链接标记帮助程序生成 `<meta name="x-stylesheet-fallback-test" content="" class="sr-only" />` 元素以及以下 JavaScript（用于验证是否可以从 CDN 获取请求的 bootstrap.min.css 文件）  。</span><span class="sxs-lookup"><span data-stu-id="fae95-111">In the preceding code, the Link Tag Helper generated the `<meta name="x-stylesheet-fallback-test" content="" class="sr-only" />` element and the following JavaScript which is used to verify the requested *bootstrap.min.css* file is available on the CDN.</span></span> <span data-ttu-id="fae95-112">在此示例中，可以获取 CSS 文件，因此标记帮助程序使用 CDN CSS 文件生成 `<link />` 元素。</span><span class="sxs-lookup"><span data-stu-id="fae95-112">In this case, the CSS file was available so the Tag Helper generated the `<link />` element with the CDN CSS file.</span></span>

## <a name="commonly-used-link-tag-helper-attributes"></a><span data-ttu-id="fae95-113">常用的链接标记帮助程序属性</span><span class="sxs-lookup"><span data-stu-id="fae95-113">Commonly used Link Tag Helper attributes</span></span>

<span data-ttu-id="fae95-114">若要了解所有链接标记帮助程序属性和方法，请参阅[链接标记帮助程序](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper)。</span><span class="sxs-lookup"><span data-stu-id="fae95-114">See [Link Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper)  for all the Link Tag Helper attributes, properties, and methods.</span></span>

### <a name="href"></a><span data-ttu-id="fae95-115">href</span><span class="sxs-lookup"><span data-stu-id="fae95-115">href</span></span>

<span data-ttu-id="fae95-116">链接的资源的首选地址。</span><span class="sxs-lookup"><span data-stu-id="fae95-116">Preferred address of the linked resource.</span></span> <span data-ttu-id="fae95-117">在任何情况下，均会将此地址传递到生成的 HTML。</span><span class="sxs-lookup"><span data-stu-id="fae95-117">The address is passed thought to the generated HTML in all cases.</span></span>

### <a name="asp-fallback-href"></a><span data-ttu-id="fae95-118">asp-fallback-href</span><span class="sxs-lookup"><span data-stu-id="fae95-118">asp-fallback-href</span></span>

<span data-ttu-id="fae95-119">主 URL 失效后要回退到的 CSS 样式表的 URL。</span><span class="sxs-lookup"><span data-stu-id="fae95-119">The URL of a CSS stylesheet to fallback to in the case the primary URL fails.</span></span>

### <a name="asp-fallback-test-class"></a><span data-ttu-id="fae95-120">asp-fallback-test-class</span><span class="sxs-lookup"><span data-stu-id="fae95-120">asp-fallback-test-class</span></span>

<span data-ttu-id="fae95-121">样式表中定义的用于回退测试的类名称。</span><span class="sxs-lookup"><span data-stu-id="fae95-121">The class name defined in the stylesheet to use for the fallback test.</span></span> <span data-ttu-id="fae95-122">有关详细信息，请参阅 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>。</span><span class="sxs-lookup"><span data-stu-id="fae95-122">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.</span></span>

### <a name="asp-fallback-test-property"></a><span data-ttu-id="fae95-123">asp-fallback-test-property</span><span class="sxs-lookup"><span data-stu-id="fae95-123">asp-fallback-test-property</span></span>

<span data-ttu-id="fae95-124">用于回退测试的 CSS 属性名称。</span><span class="sxs-lookup"><span data-stu-id="fae95-124">The CSS property name to use for the fallback test.</span></span> <span data-ttu-id="fae95-125">有关详细信息，请参阅 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>。</span><span class="sxs-lookup"><span data-stu-id="fae95-125">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.</span></span>

### <a name="asp-fallback-test-value"></a><span data-ttu-id="fae95-126">asp-fallback-test-value</span><span class="sxs-lookup"><span data-stu-id="fae95-126">asp-fallback-test-value</span></span>

<span data-ttu-id="fae95-127">用于回退测试的 CSS 属性值。</span><span class="sxs-lookup"><span data-stu-id="fae95-127">The CSS property value to use for the fallback test.</span></span> <span data-ttu-id="fae95-128">有关详细信息，请参阅 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>。</span><span class="sxs-lookup"><span data-stu-id="fae95-128">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fae95-129">其他资源</span><span class="sxs-lookup"><span data-stu-id="fae95-129">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
