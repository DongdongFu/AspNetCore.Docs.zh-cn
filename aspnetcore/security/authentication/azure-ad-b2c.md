---
title: ASP.NET Core 中的 Azure Active Directory B2C 的云身份验证
author: camsoper
description: 了解如何设置 ASP.NET Core Azure Active Directory B2C 身份验证。
ms.author: casoper
ms.custom: devx-track-csharp, mvc
ms.date: 01/21/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authentication/azure-ad-b2c
ms.openlocfilehash: f917bec8f2d929e62bf43494159a63458f135c5f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061387"
---
# <a name="cloud-authentication-with-azure-active-directory-b2c-in-aspnet-core"></a><span data-ttu-id="7f383-103">ASP.NET Core 中的 Azure Active Directory B2C 的云身份验证</span><span class="sxs-lookup"><span data-stu-id="7f383-103">Cloud authentication with Azure Active Directory B2C in ASP.NET Core</span></span>

<span data-ttu-id="7f383-104">作者：[Cam Soper](https://twitter.com/camsoper)</span><span class="sxs-lookup"><span data-stu-id="7f383-104">By [Cam Soper](https://twitter.com/camsoper)</span></span>

<span data-ttu-id="7f383-105">[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) 是用于 web 和移动应用的云标识管理解决方案。</span><span class="sxs-lookup"><span data-stu-id="7f383-105">[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) is a cloud identity management solution for web and mobile apps.</span></span> <span data-ttu-id="7f383-106">此服务为云中和本地托管的应用提供身份验证。</span><span class="sxs-lookup"><span data-stu-id="7f383-106">The service provides authentication for apps hosted in the cloud and on-premises.</span></span> <span data-ttu-id="7f383-107">身份验证类型包括个人帐户、社交网络帐户和联合企业帐户。</span><span class="sxs-lookup"><span data-stu-id="7f383-107">Authentication types include individual accounts, social network accounts, and federated enterprise accounts.</span></span> <span data-ttu-id="7f383-108">此外，Azure AD B2C 可以通过最少的配置来提供多重身份验证。</span><span class="sxs-lookup"><span data-stu-id="7f383-108">Additionally, Azure AD B2C can provide multi-factor authentication with minimal configuration.</span></span>

> [!TIP]
> <span data-ttu-id="7f383-109">Azure Active Directory (Azure AD) 和 Azure AD B2C 是单独的产品产品。</span><span class="sxs-lookup"><span data-stu-id="7f383-109">Azure Active Directory (Azure AD) and Azure AD B2C are separate product offerings.</span></span> <span data-ttu-id="7f383-110">Azure AD 租户表示组织，而 Azure AD B2C 租户表示与信赖方应用程序一起使用的标识集合。</span><span class="sxs-lookup"><span data-stu-id="7f383-110">An Azure AD tenant represents an organization, while an Azure AD B2C tenant represents a collection of identities to be used with relying party applications.</span></span> <span data-ttu-id="7f383-111">若要了解详细信息，请参阅 [Azure AD B2C：常见问题解答)  (](/azure/active-directory-b2c/active-directory-b2c-faqs)常见问题。</span><span class="sxs-lookup"><span data-stu-id="7f383-111">To learn more, see [Azure AD B2C: Frequently asked questions (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs).</span></span>

<span data-ttu-id="7f383-112">本教程介绍如何执行下列操作：</span><span class="sxs-lookup"><span data-stu-id="7f383-112">In this tutorial, learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7f383-113">创建 Azure Active Directory B2C 租户</span><span class="sxs-lookup"><span data-stu-id="7f383-113">Create an Azure Active Directory B2C tenant</span></span>
> * <span data-ttu-id="7f383-114">在 Azure AD B2C 中注册应用</span><span class="sxs-lookup"><span data-stu-id="7f383-114">Register an app in Azure AD B2C</span></span>
> * <span data-ttu-id="7f383-115">使用 Visual Studio 创建一个 ASP.NET Core web 应用，该应用配置为使用 Azure AD B2C 租户进行身份验证</span><span class="sxs-lookup"><span data-stu-id="7f383-115">Use Visual Studio to create an ASP.NET Core web app configured to use the Azure AD B2C tenant for authentication</span></span>
> * <span data-ttu-id="7f383-116">配置控制 Azure AD B2C 租户的行为的策略</span><span class="sxs-lookup"><span data-stu-id="7f383-116">Configure policies controlling the behavior of the Azure AD B2C tenant</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7f383-117">先决条件</span><span class="sxs-lookup"><span data-stu-id="7f383-117">Prerequisites</span></span>

<span data-ttu-id="7f383-118">本演练需要以下各项：</span><span class="sxs-lookup"><span data-stu-id="7f383-118">The following are required for this walkthrough:</span></span>

* [<span data-ttu-id="7f383-119">Microsoft Azure 订阅</span><span class="sxs-lookup"><span data-stu-id="7f383-119">Microsoft Azure subscription</span></span>](https://azure.microsoft.com/free/dotnet/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [<span data-ttu-id="7f383-120">Visual Studio 2019</span><span class="sxs-lookup"><span data-stu-id="7f383-120">Visual Studio 2019</span></span>](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)

## <a name="create-the-azure-active-directory-b2c-tenant"></a><span data-ttu-id="7f383-121">创建 Azure Active Directory B2C 租户</span><span class="sxs-lookup"><span data-stu-id="7f383-121">Create the Azure Active Directory B2C tenant</span></span>

<span data-ttu-id="7f383-122">[按照文档中的说明](/azure/active-directory-b2c/active-directory-b2c-get-started)创建 Azure Active Directory B2C 租户。</span><span class="sxs-lookup"><span data-stu-id="7f383-122">Create an Azure Active Directory B2C tenant [as described in the documentation](/azure/active-directory-b2c/active-directory-b2c-get-started).</span></span> <span data-ttu-id="7f383-123">出现提示时，在本教程中，将租户与 Azure 订阅相关联是可选的。</span><span class="sxs-lookup"><span data-stu-id="7f383-123">When prompted, associating the tenant with an Azure subscription is optional for this tutorial.</span></span>

## <a name="register-the-app-in-azure-ad-b2c"></a><span data-ttu-id="7f383-124">在 Azure AD B2C 中注册应用程序</span><span class="sxs-lookup"><span data-stu-id="7f383-124">Register the app in Azure AD B2C</span></span>

<span data-ttu-id="7f383-125">在新创建的 Azure AD B2C 租户中，使用 " **注册 web 应用** " 部分下的 [文档中的步骤](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application)注册应用。</span><span class="sxs-lookup"><span data-stu-id="7f383-125">In the newly created Azure AD B2C tenant, register your app using [the steps in the documentation](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application) under the **Register a web app** section.</span></span> <span data-ttu-id="7f383-126">请在 **创建 web 应用客户端机密** 部分停止。</span><span class="sxs-lookup"><span data-stu-id="7f383-126">Stop at the **Create a web app client secret** section.</span></span> <span data-ttu-id="7f383-127">本教程不需要客户端密码。</span><span class="sxs-lookup"><span data-stu-id="7f383-127">A client secret isn't required for this tutorial.</span></span> 

<span data-ttu-id="7f383-128">使用以下值：</span><span class="sxs-lookup"><span data-stu-id="7f383-128">Use the following values:</span></span>

| <span data-ttu-id="7f383-129">设置</span><span class="sxs-lookup"><span data-stu-id="7f383-129">Setting</span></span>                       | <span data-ttu-id="7f383-130">值</span><span class="sxs-lookup"><span data-stu-id="7f383-130">Value</span></span>                     | <span data-ttu-id="7f383-131">注释</span><span class="sxs-lookup"><span data-stu-id="7f383-131">Notes</span></span>                                                                                                                                                                                              |
|-------------------------------|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="7f383-132">名称</span><span class="sxs-lookup"><span data-stu-id="7f383-132">**Name**</span></span>                      | <span data-ttu-id="7f383-133">*&lt;应用名称&gt;*</span><span class="sxs-lookup"><span data-stu-id="7f383-133">*&lt;app name&gt;*</span></span>        | <span data-ttu-id="7f383-134">输入向使用者描述你的应用程序的应用程序的 **名称** 。</span><span class="sxs-lookup"><span data-stu-id="7f383-134">Enter a **Name** for the app that describes your app to consumers.</span></span>                                                                                                                                 |
| <span data-ttu-id="7f383-135"> 包括 Web 应用/Web API</span><span class="sxs-lookup"><span data-stu-id="7f383-135">**Include web app / web API**</span></span> | <span data-ttu-id="7f383-136">是</span><span class="sxs-lookup"><span data-stu-id="7f383-136">Yes</span></span>                       |                                                                                                                                                                                                    |
| <span data-ttu-id="7f383-137"> 允许隐式流</span><span class="sxs-lookup"><span data-stu-id="7f383-137">**Allow implicit flow**</span></span>       | <span data-ttu-id="7f383-138">是</span><span class="sxs-lookup"><span data-stu-id="7f383-138">Yes</span></span>                       |                                                                                                                                                                                                    |
| <span data-ttu-id="7f383-139">回复 URL</span><span class="sxs-lookup"><span data-stu-id="7f383-139">**Reply URL**</span></span>                 | `https://localhost:44300/signin-oidc` | <span data-ttu-id="7f383-140">回复 URL 属于终结点，允许 Azure AD B2C 在其中返回应用请求的任何令牌。</span><span class="sxs-lookup"><span data-stu-id="7f383-140">Reply URLs are endpoints where Azure AD B2C returns any tokens that your app requests.</span></span> <span data-ttu-id="7f383-141">Visual Studio 提供要使用的回复 URL。</span><span class="sxs-lookup"><span data-stu-id="7f383-141">Visual Studio provides the Reply URL to use.</span></span> <span data-ttu-id="7f383-142">现在，请按 enter `https://localhost:44300/signin-oidc` 完成表单。</span><span class="sxs-lookup"><span data-stu-id="7f383-142">For now, enter `https://localhost:44300/signin-oidc` to complete the form.</span></span> |
| <span data-ttu-id="7f383-143">**应用 ID URI**</span><span class="sxs-lookup"><span data-stu-id="7f383-143">**App ID URI**</span></span>                | <span data-ttu-id="7f383-144">留空</span><span class="sxs-lookup"><span data-stu-id="7f383-144">Leave blank</span></span>               | <span data-ttu-id="7f383-145">本教程不需要。</span><span class="sxs-lookup"><span data-stu-id="7f383-145">Not required for this tutorial.</span></span>                                                                                                                                                                    |
| <span data-ttu-id="7f383-146">**包含本机客户端**</span><span class="sxs-lookup"><span data-stu-id="7f383-146">**Include native client**</span></span>     | <span data-ttu-id="7f383-147">否</span><span class="sxs-lookup"><span data-stu-id="7f383-147">No</span></span>                        |                                                                                                                                                                                                    |

> [!WARNING]
> <span data-ttu-id="7f383-148">如果设置非 localhost 回复 URL，请注意 ["回复 url" 列表中允许的内容的约束](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application)。</span><span class="sxs-lookup"><span data-stu-id="7f383-148">If setting up a non-localhost Reply URL, be aware of the [constraints on what is allowed in the Reply URL list](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application).</span></span> 

<span data-ttu-id="7f383-149">注册应用后，会显示租户中的应用列表。</span><span class="sxs-lookup"><span data-stu-id="7f383-149">After the app is registered, the list of apps in the tenant is displayed.</span></span> <span data-ttu-id="7f383-150">选择刚注册的应用程序。</span><span class="sxs-lookup"><span data-stu-id="7f383-150">Select the app that was just registered.</span></span> <span data-ttu-id="7f383-151">选择 " **应用程序 ID** " 字段右侧的 " **复制** " 图标，将其复制到剪贴板。</span><span class="sxs-lookup"><span data-stu-id="7f383-151">Select the **Copy** icon to the right of the **Application ID** field to copy it to the clipboard.</span></span>

<span data-ttu-id="7f383-152">目前不能在 Azure AD B2C 租户中配置其他内容，但会将浏览器窗口保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="7f383-152">Nothing more can be configured in the Azure AD B2C tenant at this time, but leave the browser window open.</span></span> <span data-ttu-id="7f383-153">创建 ASP.NET Core 应用后，会进行更多配置。</span><span class="sxs-lookup"><span data-stu-id="7f383-153">There is more configuration after the ASP.NET Core app is created.</span></span>

## <a name="create-an-aspnet-core-app-in-visual-studio"></a><span data-ttu-id="7f383-154">在 Visual Studio 中创建 ASP.NET Core 应用</span><span class="sxs-lookup"><span data-stu-id="7f383-154">Create an ASP.NET Core app in Visual Studio</span></span>

<span data-ttu-id="7f383-155">可以将 Visual Studio Web 应用程序模板配置为使用 Azure AD B2C 租户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="7f383-155">The Visual Studio Web Application template can be configured to use the Azure AD B2C tenant for authentication.</span></span>

<span data-ttu-id="7f383-156">在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="7f383-156">In Visual Studio:</span></span>

1. <span data-ttu-id="7f383-157">创建新的 ASP.NET Core Web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="7f383-157">Create a new ASP.NET Core Web Application.</span></span> 
2. <span data-ttu-id="7f383-158">从模板列表中选择 " **Web 应用程序** "。</span><span class="sxs-lookup"><span data-stu-id="7f383-158">Select **Web Application** from the list of templates.</span></span>
3. <span data-ttu-id="7f383-159">选择 " **更改身份验证** " 按钮。</span><span class="sxs-lookup"><span data-stu-id="7f383-159">Select the **Change Authentication** button.</span></span>
    
    !["更改身份验证" 按钮](./azure-ad-b2c/_static/changeauth.png)

4. <span data-ttu-id="7f383-161">在 " **更改身份验证** " 对话框中，选择 " **个人用户帐户** "，然后在下拉列表中选择 " **连接到云中的现有用户存储** "。</span><span class="sxs-lookup"><span data-stu-id="7f383-161">In the **Change Authentication** dialog, select **Individual User Accounts** , and then select **Connect to an existing user store in the cloud** in the dropdown.</span></span> 
    
    !["更改身份验证" 对话框](./azure-ad-b2c/_static/changeauthdialog.png)

5. <span data-ttu-id="7f383-163">完成具有以下值的窗体：</span><span class="sxs-lookup"><span data-stu-id="7f383-163">Complete the form with the following values:</span></span>
    
    | <span data-ttu-id="7f383-164">设置</span><span class="sxs-lookup"><span data-stu-id="7f383-164">Setting</span></span>                       | <span data-ttu-id="7f383-165">值</span><span class="sxs-lookup"><span data-stu-id="7f383-165">Value</span></span>                                                 |
    |-------------------------------|-------------------------------------------------------|
    | <span data-ttu-id="7f383-166">**域名**</span><span class="sxs-lookup"><span data-stu-id="7f383-166">**Domain Name**</span></span>               | <span data-ttu-id="7f383-167">*&lt;B2C 租户的域名&gt;*</span><span class="sxs-lookup"><span data-stu-id="7f383-167">*&lt;the domain name of your B2C tenant&gt;*</span></span>          |
    | <span data-ttu-id="7f383-168">**应用程序 ID**</span><span class="sxs-lookup"><span data-stu-id="7f383-168">**Application ID**</span></span>            | <span data-ttu-id="7f383-169">*&lt;从剪贴板粘贴应用程序 ID&gt;*</span><span class="sxs-lookup"><span data-stu-id="7f383-169">*&lt;paste the Application ID from the clipboard&gt;*</span></span> |
    | <span data-ttu-id="7f383-170">**回调路径**</span><span class="sxs-lookup"><span data-stu-id="7f383-170">**Callback Path**</span></span>             | <span data-ttu-id="7f383-171">*&lt;使用默认值&gt;*</span><span class="sxs-lookup"><span data-stu-id="7f383-171">*&lt;use the default value&gt;*</span></span>                       |
    | <span data-ttu-id="7f383-172">**注册或登录策略**</span><span class="sxs-lookup"><span data-stu-id="7f383-172">**Sign-up or sign-in policy**</span></span> | `B2C_1_SiUpIn`                                        |
    | <span data-ttu-id="7f383-173">**重置密码策略**</span><span class="sxs-lookup"><span data-stu-id="7f383-173">**Reset password policy**</span></span>     | `B2C_1_SSPR`                                          |
    | <span data-ttu-id="7f383-174">**编辑配置文件策略**</span><span class="sxs-lookup"><span data-stu-id="7f383-174">**Edit profile policy**</span></span>       | <span data-ttu-id="7f383-175">*&lt;留空&gt;*</span><span class="sxs-lookup"><span data-stu-id="7f383-175">*&lt;leave blank&gt;*</span></span>                                 |
    
    <span data-ttu-id="7f383-176">选择 " **回复 uri** " 旁边的 " **复制** " 链接，以将答复 uri 复制到剪贴板。</span><span class="sxs-lookup"><span data-stu-id="7f383-176">Select the **Copy** link next to **Reply URI** to copy the Reply URI to the clipboard.</span></span> <span data-ttu-id="7f383-177">选择 **"确定"** 以关闭 " **更改身份验证** " 对话框。</span><span class="sxs-lookup"><span data-stu-id="7f383-177">Select **OK** to close the **Change Authentication** dialog.</span></span> <span data-ttu-id="7f383-178">选择 **"确定"** 以创建 web 应用。</span><span class="sxs-lookup"><span data-stu-id="7f383-178">Select **OK** to create the web app.</span></span>

## <a name="finish-the-b2c-app-registration"></a><span data-ttu-id="7f383-179">完成 B2C 应用注册</span><span class="sxs-lookup"><span data-stu-id="7f383-179">Finish the B2C app registration</span></span>

<span data-ttu-id="7f383-180">返回到包含 B2C 应用程序属性的浏览器窗口。</span><span class="sxs-lookup"><span data-stu-id="7f383-180">Return to the browser window with the B2C app properties still open.</span></span> <span data-ttu-id="7f383-181">将前面指定的临时 **回复 URL** 更改为从 Visual Studio 复制的值。</span><span class="sxs-lookup"><span data-stu-id="7f383-181">Change the temporary **Reply URL** specified earlier to the value copied from Visual Studio.</span></span> <span data-ttu-id="7f383-182">选择窗口顶部的 " **保存** "。</span><span class="sxs-lookup"><span data-stu-id="7f383-182">Select **Save** at the top of the window.</span></span>

> [!TIP]
> <span data-ttu-id="7f383-183">如果未复制回复 URL，请使用 web 项目属性中的 "调试" 选项卡上的 HTTPS 地址，并从中追加 **CallbackPath** 值 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="7f383-183">If you didn't copy the Reply URL, use the HTTPS address from the Debug tab in the web project properties, and append the **CallbackPath** value from *appsettings.json* .</span></span>

## <a name="configure-policies"></a><span data-ttu-id="7f383-184">配置策略</span><span class="sxs-lookup"><span data-stu-id="7f383-184">Configure policies</span></span>

<span data-ttu-id="7f383-185">使用 Azure AD B2C 文档中的步骤 [创建注册或登录策略](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions)，然后 [创建密码重置策略](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions)。</span><span class="sxs-lookup"><span data-stu-id="7f383-185">Use the steps in the Azure AD B2C documentation to [create a sign-up or sign-in policy](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions), and then [create a password reset policy](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions).</span></span> <span data-ttu-id="7f383-186">使用提供 **Identity 者** 文档、 **注册属性** 和 **应用程序声明** 中提供的示例值。</span><span class="sxs-lookup"><span data-stu-id="7f383-186">Use the example values provided in the documentation for **Identity providers** , **Sign-up attributes** , and **Application claims** .</span></span> <span data-ttu-id="7f383-187">使用 " **立即运行** " 按钮测试文档中所述的策略是可选的。</span><span class="sxs-lookup"><span data-stu-id="7f383-187">Using the **Run now** button to test the policies as described in the documentation is optional.</span></span>

> [!WARNING]
> <span data-ttu-id="7f383-188">确保策略名称与文档中所述的完全相同，因为在 Visual Studio 的 " **更改身份验证** " 对话框中使用了这些策略。</span><span class="sxs-lookup"><span data-stu-id="7f383-188">Ensure the policy names are exactly as described in the documentation, as those policies were used in the **Change Authentication** dialog in Visual Studio.</span></span> <span data-ttu-id="7f383-189">可以在中验证策略名称 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="7f383-189">The policy names can be verified in *appsettings.json* .</span></span>

## <a name="configure-the-underlying-openidconnectoptionsjwtbearerno-loccookie-options"></a><span data-ttu-id="7f383-190">配置基础 OpenIdConnectOptions/JwtBearer/ Cookie options</span><span class="sxs-lookup"><span data-stu-id="7f383-190">Configure the underlying OpenIdConnectOptions/JwtBearer/Cookie options</span></span>

<span data-ttu-id="7f383-191">若要直接配置基础选项，请在中使用适当的方案常数 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="7f383-191">To configure the underlying options directly, use the appropriate scheme constant in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(
    AzureAD[B2C]Defaults.OpenIdScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<CookieAuthenticationOptions>(
    AzureAD[B2C]Defaults.CookieScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<JwtBearerOptions>(
    AzureAD[B2C]Defaults.JwtBearerAuthenticationScheme, options => 
    {
        // Omitted for brevity
    });
```

## <a name="run-the-app"></a><span data-ttu-id="7f383-192">运行应用程序</span><span class="sxs-lookup"><span data-stu-id="7f383-192">Run the app</span></span>

<span data-ttu-id="7f383-193">在 Visual Studio 中，按 **F5** 生成并运行应用。</span><span class="sxs-lookup"><span data-stu-id="7f383-193">In Visual Studio, press **F5** to build and run the app.</span></span> <span data-ttu-id="7f383-194">Web 应用启动后，选择 " **接受** " 以接受 cookie (如果系统提示) ，然后选择 " **登录** "。</span><span class="sxs-lookup"><span data-stu-id="7f383-194">After the web app launches, select **Accept** to accept the use of cookies (if prompted), and then select **Sign in** .</span></span>

![登录应用](./azure-ad-b2c/_static/signin.png)

<span data-ttu-id="7f383-196">浏览器重定向到 Azure AD B2C 租户。</span><span class="sxs-lookup"><span data-stu-id="7f383-196">The browser redirects to the Azure AD B2C tenant.</span></span> <span data-ttu-id="7f383-197">使用现有帐户登录 (如果已创建测试策略) 或选择 " **立即注册** " 创建新帐户。</span><span class="sxs-lookup"><span data-stu-id="7f383-197">Sign in with an existing account (if one was created testing the policies) or select **Sign up now** to create a new account.</span></span> <span data-ttu-id="7f383-198">" **忘记了密码？** " 链接用于重置忘记的密码。</span><span class="sxs-lookup"><span data-stu-id="7f383-198">The **Forgot your password?** link is used to reset a forgotten password.</span></span>

![Azure AD B2C 登录](./azure-ad-b2c/_static/b2csts.png)

<span data-ttu-id="7f383-200">成功登录后，浏览器将重定向到 web 应用。</span><span class="sxs-lookup"><span data-stu-id="7f383-200">After successfully signing in, the browser redirects to the web app.</span></span>

![Success](./azure-ad-b2c/_static/success.png)

## <a name="next-steps"></a><span data-ttu-id="7f383-202">后续步骤</span><span class="sxs-lookup"><span data-stu-id="7f383-202">Next steps</span></span>

<span data-ttu-id="7f383-203">在本教程中，你了解了如何执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="7f383-203">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7f383-204">创建 Azure Active Directory B2C 租户</span><span class="sxs-lookup"><span data-stu-id="7f383-204">Create an Azure Active Directory B2C tenant</span></span>
> * <span data-ttu-id="7f383-205">在 Azure AD B2C 中注册应用</span><span class="sxs-lookup"><span data-stu-id="7f383-205">Register an app in Azure AD B2C</span></span>
> * <span data-ttu-id="7f383-206">使用 Visual Studio 创建一个 ASP.NET Core Web 应用程序，该应用程序配置为使用 Azure AD B2C 租户进行身份验证</span><span class="sxs-lookup"><span data-stu-id="7f383-206">Use Visual Studio to create an ASP.NET Core Web Application configured to use the Azure AD B2C tenant for authentication</span></span>
> * <span data-ttu-id="7f383-207">配置控制 Azure AD B2C 租户的行为的策略</span><span class="sxs-lookup"><span data-stu-id="7f383-207">Configure policies controlling the behavior of the Azure AD B2C tenant</span></span>

<span data-ttu-id="7f383-208">现在，ASP.NET Core 应用配置为使用 Azure AD B2C 进行身份验证， [授权属性](xref:security/authorization/simple) 可用于保护你的应用。</span><span class="sxs-lookup"><span data-stu-id="7f383-208">Now that the ASP.NET Core app is configured to use Azure AD B2C for authentication, the [Authorize attribute](xref:security/authorization/simple) can be used to secure your app.</span></span> <span data-ttu-id="7f383-209">通过学习以下内容继续开发应用：</span><span class="sxs-lookup"><span data-stu-id="7f383-209">Continue developing your app by learning to:</span></span>

* <span data-ttu-id="7f383-210">[自定义 Azure AD B2C 用户界面](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization)。</span><span class="sxs-lookup"><span data-stu-id="7f383-210">[Customize the Azure AD B2C user interface](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).</span></span>
* <span data-ttu-id="7f383-211">[配置密码复杂性要求](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity)。</span><span class="sxs-lookup"><span data-stu-id="7f383-211">[Configure password complexity requirements](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).</span></span>
* <span data-ttu-id="7f383-212">[启用多重身份验证](/azure/active-directory-b2c/active-directory-b2c-reference-mfa)。</span><span class="sxs-lookup"><span data-stu-id="7f383-212">[Enable multi-factor authentication](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).</span></span>
* <span data-ttu-id="7f383-213">配置其他标识提供程序，例如 [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app)、 [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app)、 [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app)、 [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app)、 [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app)等。</span><span class="sxs-lookup"><span data-stu-id="7f383-213">Configure additional identity providers, such as [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app), and others.</span></span>
* <span data-ttu-id="7f383-214">[使用 Azure AD 图形 API](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) 从 Azure AD B2C 租户检索其他用户信息，例如组成员身份。</span><span class="sxs-lookup"><span data-stu-id="7f383-214">[Use the Azure AD Graph API](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) to retrieve additional user information, such as group membership, from the Azure AD B2C tenant.</span></span>
* <span data-ttu-id="7f383-215">[如何使用 Azure AD B2C 保护使用 ASP.NET Core 生成的 WEB API](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C)。</span><span class="sxs-lookup"><span data-stu-id="7f383-215">[How to secure a Web API built with ASP.NET Core using the Azure AD B2C](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C).</span></span>
* <span data-ttu-id="7f383-216">[教程：使用 Azure Active Directory B2C 授予对 ASP.NET WEB API 的访问权限](/azure/active-directory-b2c/tutorial-web-api-dotnet)。</span><span class="sxs-lookup"><span data-stu-id="7f383-216">[Tutorial: Grant access to an ASP.NET web API using Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-web-api-dotnet).</span></span>
