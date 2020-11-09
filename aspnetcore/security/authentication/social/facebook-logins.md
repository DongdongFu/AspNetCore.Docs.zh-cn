---
title: ASP.NET Core 中的 Facebook 外部登录设置
author: rick-anderson
description: 包含代码示例的教程演示如何将 Facebook 帐户用户身份验证集成到现有 ASP.NET Core 应用。
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
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
uid: security/authentication/facebook-logins
ms.openlocfilehash: be0b655645fd2bd0eab9f9c30a65485f386cead3
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053353"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a><span data-ttu-id="0cd82-103">ASP.NET Core 中的 Facebook 外部登录设置</span><span class="sxs-lookup"><span data-stu-id="0cd82-103">Facebook external login setup in ASP.NET Core</span></span>

<span data-ttu-id="0cd82-104">作者：[Valeriy Novytskyy](https://github.com/01binary) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0cd82-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

<span data-ttu-id="0cd82-105">本教程中的代码示例演示如何使用户能够使用在 [前一页](xref:security/authentication/social/index)上创建的示例 ASP.NET Core 3.0 项目登录其 Facebook 帐户。</span><span class="sxs-lookup"><span data-stu-id="0cd82-105">This tutorial with code examples shows how to enable your users to sign in with their Facebook account using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span> <span data-ttu-id="0cd82-106">首先，我们要按照 [官方步骤](https://developers.facebook.com)创建 FACEBOOK 应用 ID。</span><span class="sxs-lookup"><span data-stu-id="0cd82-106">We start by creating a Facebook App ID by following the [official steps](https://developers.facebook.com).</span></span>

## <a name="create-the-app-in-facebook"></a><span data-ttu-id="0cd82-107">在 Facebook 中创建应用</span><span class="sxs-lookup"><span data-stu-id="0cd82-107">Create the app in Facebook</span></span>

* <span data-ttu-id="0cd82-108">将 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet 包添加到项目。</span><span class="sxs-lookup"><span data-stu-id="0cd82-108">Add the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet package to the project.</span></span>

* <span data-ttu-id="0cd82-109">导航到 [Facebook 开发人员应用](https://developers.facebook.com/apps/) 页面并登录。</span><span class="sxs-lookup"><span data-stu-id="0cd82-109">Navigate to the [Facebook Developers app](https://developers.facebook.com/apps/) page and sign in.</span></span> <span data-ttu-id="0cd82-110">如果还没有 Facebook 帐户，请使用登录页上的 " **注册 Facebook** " 链接创建一个。</span><span class="sxs-lookup"><span data-stu-id="0cd82-110">If you don't already have a Facebook account, use the **Sign up for Facebook** link on the login page to create one.</span></span>  <span data-ttu-id="0cd82-111">获得 Facebook 帐户后，请按照说明注册为 Facebook 开发人员。</span><span class="sxs-lookup"><span data-stu-id="0cd82-111">Once you have a Facebook account, follow the instructions to register as a Facebook Developer.</span></span>

* <span data-ttu-id="0cd82-112">从 " **我的应用** " 菜单中，选择 " **创建应用** " 以创建新的应用 ID。</span><span class="sxs-lookup"><span data-stu-id="0cd82-112">From the **My Apps** menu select **Create App** to create a new App ID.</span></span>

   ![Facebook for 开发人员门户在 Microsoft Edge 中打开](index/_static/FBMyApps.png)

* <span data-ttu-id="0cd82-114">填写表单，然后点击 " **创建应用 ID** " 按钮。</span><span class="sxs-lookup"><span data-stu-id="0cd82-114">Fill out the form and tap the **Create App ID** button.</span></span>

  ![创建新的应用 ID 窗体](index/_static/FBNewAppId.png)

* <span data-ttu-id="0cd82-116">在 "新建应用" 卡中，选择 " **添加产品** "。</span><span class="sxs-lookup"><span data-stu-id="0cd82-116">On the new App card, select **Add a Product** .</span></span>  <span data-ttu-id="0cd82-117">在 **Facebook 登录** 卡上，单击 " **设置** "</span><span class="sxs-lookup"><span data-stu-id="0cd82-117">On the **Facebook Login** card, click **Set Up**</span></span> 

  ![产品设置页](index/_static/FBProductSetup.png)

* <span data-ttu-id="0cd82-119">**快速入门** 向导会启动，并 **选择一个平台** 作为第一页。</span><span class="sxs-lookup"><span data-stu-id="0cd82-119">The **Quickstart** wizard launches with **Choose a Platform** as the first page.</span></span> <span data-ttu-id="0cd82-120">现在，通过单击左下方菜单中的 " **FaceBook 登录\*\*\*\*设置** " 链接，绕过向导：</span><span class="sxs-lookup"><span data-stu-id="0cd82-120">Bypass the wizard for now by clicking the **FaceBook Login** **Settings** link in the menu on the lower left:</span></span>

  ![跳过快速入门](index/_static/FBSkipQuickStart.png)

* <span data-ttu-id="0cd82-122">将显示 " **客户端 OAuth 设置** " 页：</span><span class="sxs-lookup"><span data-stu-id="0cd82-122">You are presented with the **Client OAuth Settings** page:</span></span>

  !["客户端 OAuth 设置" 页](index/_static/FBOAuthSetup.png)

* <span data-ttu-id="0cd82-124">输入附加到 " **有效的 OAuth 重定向 uri** " 字段中的 */SIGNIN-FACEBOOK* 的开发 URI (例如： `https://localhost:44320/signin-facebook`) 。</span><span class="sxs-lookup"><span data-stu-id="0cd82-124">Enter your development URI with */signin-facebook* appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-facebook`).</span></span> <span data-ttu-id="0cd82-125">稍后在本教程中配置的 Facebook 身份验证将自动处理 */signin-facebook* 路由中的请求以实现 OAuth 流。</span><span class="sxs-lookup"><span data-stu-id="0cd82-125">The Facebook authentication configured later in this tutorial will automatically handle requests at */signin-facebook* route to implement the OAuth flow.</span></span>

> [!NOTE]
> <span data-ttu-id="0cd82-126">URI */signin-facebook* 设置为 facebook 身份验证提供程序的默认回调。</span><span class="sxs-lookup"><span data-stu-id="0cd82-126">The URI */signin-facebook* is set as the default callback of the Facebook authentication provider.</span></span> <span data-ttu-id="0cd82-127">通过[FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions)类的继承的[RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)属性配置 Facebook 身份验证中间件时，可以更改默认的回叫 URI。</span><span class="sxs-lookup"><span data-stu-id="0cd82-127">You can change the default callback URI while configuring the Facebook authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) class.</span></span>

* <span data-ttu-id="0cd82-128">单击 **“保存更改”** 。</span><span class="sxs-lookup"><span data-stu-id="0cd82-128">Click **Save Changes** .</span></span>

* <span data-ttu-id="0cd82-129">单击 **Settings**  >  左侧导航栏中的 "设置" " **基本** " 链接。</span><span class="sxs-lookup"><span data-stu-id="0cd82-129">Click **Settings** > **Basic** link in the left navigation.</span></span>

  <span data-ttu-id="0cd82-130">在此页上，请记下 `App ID` 和 `App Secret` 。</span><span class="sxs-lookup"><span data-stu-id="0cd82-130">On this page, make a note of your `App ID` and your `App Secret`.</span></span> <span data-ttu-id="0cd82-131">在下一部分中，你将同时添加到 ASP.NET Core 应用程序：</span><span class="sxs-lookup"><span data-stu-id="0cd82-131">You will add both into your ASP.NET Core application in the next section:</span></span>

* <span data-ttu-id="0cd82-132">部署站点时，需要重新访问 **Facebook 登录** 设置页面并注册新的公共 URI。</span><span class="sxs-lookup"><span data-stu-id="0cd82-132">When deploying the site you need to revisit the **Facebook Login** setup page and register a new public URI.</span></span>

## <a name="store-the-facebook-app-id-and-secret"></a><span data-ttu-id="0cd82-133">存储 Facebook 应用 ID 和机密</span><span class="sxs-lookup"><span data-stu-id="0cd82-133">Store the Facebook app ID and secret</span></span>

<span data-ttu-id="0cd82-134">用 [机密管理器](xref:security/app-secrets)存储敏感设置，如 FACEBOOK 应用 ID 和机密值。</span><span class="sxs-lookup"><span data-stu-id="0cd82-134">Store sensitive settings such as the Facebook app ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="0cd82-135">对于本示例，请使用以下步骤：</span><span class="sxs-lookup"><span data-stu-id="0cd82-135">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="0cd82-136">按照 [启用密钥存储](xref:security/app-secrets#enable-secret-storage)中的说明初始化密钥存储的项目。</span><span class="sxs-lookup"><span data-stu-id="0cd82-136">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="0cd82-137">将敏感设置存储在本地密钥存储中，并提供机密密钥 `Authentication:Facebook:AppId` 和 `Authentication:Facebook:AppSecret` ：</span><span class="sxs-lookup"><span data-stu-id="0cd82-137">Store the sensitive settings in the local secret store with the secret keys `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a><span data-ttu-id="0cd82-138">配置 Facebook 身份验证</span><span class="sxs-lookup"><span data-stu-id="0cd82-138">Configure Facebook Authentication</span></span>

<span data-ttu-id="0cd82-139">将 Facebook 服务添加到 `ConfigureServices` *Startup.cs* 文件的方法中：</span><span class="sxs-lookup"><span data-stu-id="0cd82-139">Add the Facebook service in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a><span data-ttu-id="0cd82-140">用 Facebook 登录</span><span class="sxs-lookup"><span data-stu-id="0cd82-140">Sign in with Facebook</span></span>

* <span data-ttu-id="0cd82-141">运行应用并选择 **"登录"** 。</span><span class="sxs-lookup"><span data-stu-id="0cd82-141">Run the app and select **Log in** .</span></span> 
* <span data-ttu-id="0cd82-142">在 " **使用其他服务进行登录** " 下，选择 Facebook。</span><span class="sxs-lookup"><span data-stu-id="0cd82-142">Under **Use another service to log in.** , select Facebook.</span></span>
* <span data-ttu-id="0cd82-143">你将重定向到 **Facebook** 进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="0cd82-143">You are redirected to **Facebook** for authentication.</span></span>
* <span data-ttu-id="0cd82-144">输入 Facebook 凭据。</span><span class="sxs-lookup"><span data-stu-id="0cd82-144">Enter your Facebook credentials.</span></span>
* <span data-ttu-id="0cd82-145">你将重定向回到你的网站，你可以在其中设置电子邮件。</span><span class="sxs-lookup"><span data-stu-id="0cd82-145">You are redirected back to your site where you can set your email.</span></span>

<span data-ttu-id="0cd82-146">你现在已使用 Facebook 凭据登录：</span><span class="sxs-lookup"><span data-stu-id="0cd82-146">You are now logged in using your Facebook credentials:</span></span>

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a><span data-ttu-id="0cd82-147">响应取消授权外部登录</span><span class="sxs-lookup"><span data-stu-id="0cd82-147">React to cancel authorize external sign-in</span></span>

<span data-ttu-id="0cd82-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath> 当用户未批准请求的授权请求时，可以提供用户代理的重定向路径。</span><span class="sxs-lookup"><span data-stu-id="0cd82-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath> can provide a redirect path to the user agent when the user doesn't approve the requested authorization demand.</span></span>

<span data-ttu-id="0cd82-149">下面的代码将设置 `AccessDeniedPath` 为 `"/AccessDeniedPathInfo"` ：</span><span class="sxs-lookup"><span data-stu-id="0cd82-149">The following code sets the `AccessDeniedPath` to `"/AccessDeniedPathInfo"`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

<span data-ttu-id="0cd82-150">建议 `AccessDeniedPath` 页面包含以下信息：</span><span class="sxs-lookup"><span data-stu-id="0cd82-150">We recommend the `AccessDeniedPath` page contain the following information:</span></span>

*  <span data-ttu-id="0cd82-151">远程身份验证已取消。</span><span class="sxs-lookup"><span data-stu-id="0cd82-151">Remote authentication was canceled.</span></span>
* <span data-ttu-id="0cd82-152">此应用需要身份验证。</span><span class="sxs-lookup"><span data-stu-id="0cd82-152">This app requires authentication.</span></span>
* <span data-ttu-id="0cd82-153">若要再次尝试登录，请选择 "登录" 链接。</span><span class="sxs-lookup"><span data-stu-id="0cd82-153">To try sign-in again, select the Login link.</span></span>

### <a name="test-accessdeniedpath"></a><span data-ttu-id="0cd82-154">测试 AccessDeniedPath</span><span class="sxs-lookup"><span data-stu-id="0cd82-154">Test AccessDeniedPath</span></span>

* <span data-ttu-id="0cd82-155">导航到 [facebook.com](https://www.facebook.com/)</span><span class="sxs-lookup"><span data-stu-id="0cd82-155">Navigate to [facebook.com](https://www.facebook.com/)</span></span>
* <span data-ttu-id="0cd82-156">如果已登录，则必须注销。</span><span class="sxs-lookup"><span data-stu-id="0cd82-156">If you are signed in, you must sign out.</span></span>
* <span data-ttu-id="0cd82-157">运行应用并选择 "Facebook 登录"。</span><span class="sxs-lookup"><span data-stu-id="0cd82-157">Run the app and select Facebook sign-in.</span></span>
* <span data-ttu-id="0cd82-158">选择 " **暂时 Not** "。</span><span class="sxs-lookup"><span data-stu-id="0cd82-158">Select **Not now** .</span></span> <span data-ttu-id="0cd82-159">你将重定向到指定的 `AccessDeniedPath` 页面。</span><span class="sxs-lookup"><span data-stu-id="0cd82-159">You are redirected to the specified `AccessDeniedPath` page.</span></span>

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="0cd82-160">有关 Facebook 身份验证支持的配置选项的详细信息，请参阅 [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API 参考。</span><span class="sxs-lookup"><span data-stu-id="0cd82-160">See the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API reference for more information on configuration options supported by Facebook authentication.</span></span> <span data-ttu-id="0cd82-161">配置选项可用于：</span><span class="sxs-lookup"><span data-stu-id="0cd82-161">Configuration options can be used to:</span></span>

* <span data-ttu-id="0cd82-162">请求有关用户的其他信息。</span><span class="sxs-lookup"><span data-stu-id="0cd82-162">Request different information about the user.</span></span>
* <span data-ttu-id="0cd82-163">添加查询字符串参数以自定义登录体验。</span><span class="sxs-lookup"><span data-stu-id="0cd82-163">Add query string arguments to customize the login experience.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="0cd82-164">疑难解答</span><span class="sxs-lookup"><span data-stu-id="0cd82-164">Troubleshooting</span></span>

* <span data-ttu-id="0cd82-165">**仅 ASP.NET Core 2.x：** 如果 Identity 未通过调用进行 `services.AddIdentity` 配置 `ConfigureServices` ，尝试进行身份验证会导致 *ArgumentException：必须提供 "SignInScheme" 选项* 。</span><span class="sxs-lookup"><span data-stu-id="0cd82-165">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided* .</span></span> <span data-ttu-id="0cd82-166">本教程中使用的项目模板可确保完成此操作。</span><span class="sxs-lookup"><span data-stu-id="0cd82-166">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="0cd82-167">如果尚未通过应用初始迁移来创建站点数据库，则在处理请求错误时，将会出现 *数据库操作失败* 的情况。</span><span class="sxs-lookup"><span data-stu-id="0cd82-167">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="0cd82-168">点击 " **应用迁移** " 以创建数据库，然后单击 "刷新" 以继续出现错误。</span><span class="sxs-lookup"><span data-stu-id="0cd82-168">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="0cd82-169">后续步骤</span><span class="sxs-lookup"><span data-stu-id="0cd82-169">Next steps</span></span>

* <span data-ttu-id="0cd82-170">本文演示了如何通过 Facebook 进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="0cd82-170">This article showed how you can authenticate with Facebook.</span></span> <span data-ttu-id="0cd82-171">您可以遵循类似的方法向 [前一页](xref:security/authentication/social/index)上列出的其他提供程序进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="0cd82-171">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="0cd82-172">将网站发布到 Azure web 应用后，应 `AppSecret` 在 Facebook 开发人员门户中重置。</span><span class="sxs-lookup"><span data-stu-id="0cd82-172">Once you publish your web site to Azure web app, you should reset the `AppSecret` in the Facebook developer portal.</span></span>

* <span data-ttu-id="0cd82-173">`Authentication:Facebook:AppId` `Authentication:Facebook:AppSecret` 在 Azure 门户中将和设置为应用程序设置。</span><span class="sxs-lookup"><span data-stu-id="0cd82-173">Set the `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="0cd82-174">配置系统设置为从环境变量读取密钥。</span><span class="sxs-lookup"><span data-stu-id="0cd82-174">The configuration system is set up to read keys from environment variables.</span></span>
