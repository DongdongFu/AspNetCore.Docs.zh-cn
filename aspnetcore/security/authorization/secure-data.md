---
title: 使用授权保护的用户数据创建 ASP.NET Core 应用
author: rick-anderson
description: '了解如何使用授权保护的用户数据创建 ASP.NET Core web 应用。 包括 HTTPS、身份验证、安全性 ASP.NET Core Identity 。'
ms.author: riande
ms.date: 7/18/2020
ms.custom: mvc, seodec18
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
uid: security/authorization/secure-data
ms.openlocfilehash: accfd46fa72c33976f8af2a39267c993447e036e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051936"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="89e14-104">使用受授权保护的用户数据创建 ASP.NET Core web 应用</span><span class="sxs-lookup"><span data-stu-id="89e14-104">Create an ASP.NET Core web app with user data protected by authorization</span></span>

<span data-ttu-id="89e14-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="89e14-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="89e14-106">查看 [此 pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="89e14-106">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="89e14-107">本教程演示如何创建 ASP.NET Core 的 web 应用，其中包含由授权保护的用户数据。</span><span class="sxs-lookup"><span data-stu-id="89e14-107">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="89e14-108">它将显示已进行身份验证 (已创建的已注册) 用户的联系人列表。</span><span class="sxs-lookup"><span data-stu-id="89e14-108">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="89e14-109">有三个安全组：</span><span class="sxs-lookup"><span data-stu-id="89e14-109">There are three security groups:</span></span>

* <span data-ttu-id="89e14-110">**已注册的用户** 可以查看所有已批准的数据，并可以编辑/删除他们自己的数据。</span><span class="sxs-lookup"><span data-stu-id="89e14-110">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="89e14-111">**经理** 可以批准或拒绝联系人数据。</span><span class="sxs-lookup"><span data-stu-id="89e14-111">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="89e14-112">只有已批准的联系人对用户可见。</span><span class="sxs-lookup"><span data-stu-id="89e14-112">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="89e14-113">**管理员** 可以批准/拒绝和编辑/删除任何数据。</span><span class="sxs-lookup"><span data-stu-id="89e14-113">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="89e14-114">此文档中的图像与最新模板并不完全匹配。</span><span class="sxs-lookup"><span data-stu-id="89e14-114">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="89e14-115">在下图中，用户 Rick (`rick@example.com`) 已登录。</span><span class="sxs-lookup"><span data-stu-id="89e14-115">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="89e14-116">Rick 只能查看已批准的联系人， **编辑** / **删除** / 为其联系人 **创建新** 链接。</span><span class="sxs-lookup"><span data-stu-id="89e14-116">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="89e14-117">只有 Rick 创建的最后一条记录才会显示 " **编辑** " 和 " **删除** " 链接。</span><span class="sxs-lookup"><span data-stu-id="89e14-117">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="89e14-118">在经理或管理员将状态更改为 "已批准" 之前，其他用户将看不到最后一条记录。</span><span class="sxs-lookup"><span data-stu-id="89e14-118">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![显示已登录的 Rick 的屏幕截图](secure-data/_static/rick.png)

<span data-ttu-id="89e14-120">在下图中， `manager@contoso.com` 已登录，并在管理器的角色中：</span><span class="sxs-lookup"><span data-stu-id="89e14-120">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![显示 manager@contoso.com 已登录的屏幕截图](secure-data/_static/manager1.png)

<span data-ttu-id="89e14-122">下图显示了联系人的经理详细信息视图：</span><span class="sxs-lookup"><span data-stu-id="89e14-122">The following image shows the managers details view of a contact:</span></span>

![联系人的经理视图](secure-data/_static/manager.png)

<span data-ttu-id="89e14-124">" **批准** " 和 " **拒绝** " 按钮仅为经理和管理员显示。</span><span class="sxs-lookup"><span data-stu-id="89e14-124">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="89e14-125">在下图中，以 `admin@contoso.com` 管理员的角色登录和：</span><span class="sxs-lookup"><span data-stu-id="89e14-125">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![显示 admin@contoso.com 已登录的屏幕截图](secure-data/_static/admin.png)

<span data-ttu-id="89e14-127">管理员具有所有权限。</span><span class="sxs-lookup"><span data-stu-id="89e14-127">The administrator has all privileges.</span></span> <span data-ttu-id="89e14-128">她可以读取/编辑/删除任何联系人并更改联系人的状态。</span><span class="sxs-lookup"><span data-stu-id="89e14-128">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="89e14-129">此应用是通过 [基架](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) 创建的 `Contact` ：以下模型：</span><span class="sxs-lookup"><span data-stu-id="89e14-129">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="89e14-130">该示例包含以下授权处理程序：</span><span class="sxs-lookup"><span data-stu-id="89e14-130">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="89e14-131">`ContactIsOwnerAuthorizationHandler`：确保用户只能编辑其数据。</span><span class="sxs-lookup"><span data-stu-id="89e14-131">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="89e14-132">`ContactManagerAuthorizationHandler`：允许经理批准或拒绝联系人。</span><span class="sxs-lookup"><span data-stu-id="89e14-132">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="89e14-133">`ContactAdministratorsAuthorizationHandler`：允许管理员批准或拒绝联系人以及编辑/删除联系人。</span><span class="sxs-lookup"><span data-stu-id="89e14-133">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="89e14-134">先决条件</span><span class="sxs-lookup"><span data-stu-id="89e14-134">Prerequisites</span></span>

<span data-ttu-id="89e14-135">本教程是高级教程。</span><span class="sxs-lookup"><span data-stu-id="89e14-135">This tutorial is advanced.</span></span> <span data-ttu-id="89e14-136">你应该熟悉：</span><span class="sxs-lookup"><span data-stu-id="89e14-136">You should be familiar with:</span></span>

* [<span data-ttu-id="89e14-137">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="89e14-137">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="89e14-138">身份验证</span><span class="sxs-lookup"><span data-stu-id="89e14-138">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="89e14-139">帐户确认和密码恢复</span><span class="sxs-lookup"><span data-stu-id="89e14-139">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="89e14-140">授权</span><span class="sxs-lookup"><span data-stu-id="89e14-140">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="89e14-141">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="89e14-141">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="89e14-142">入门和已完成的应用程序</span><span class="sxs-lookup"><span data-stu-id="89e14-142">The starter and completed app</span></span>

<span data-ttu-id="89e14-143">[下载](xref:index#how-to-download-a-sample)[已完成](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples)的应用。</span><span class="sxs-lookup"><span data-stu-id="89e14-143">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="89e14-144">[测试](#test-the-completed-app) 已完成的应用程序，使其安全功能熟悉。</span><span class="sxs-lookup"><span data-stu-id="89e14-144">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="89e14-145">入门应用</span><span class="sxs-lookup"><span data-stu-id="89e14-145">The starter app</span></span>

<span data-ttu-id="89e14-146">[下载](xref:index#how-to-download-a-sample)[初学者](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/)应用。</span><span class="sxs-lookup"><span data-stu-id="89e14-146">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="89e14-147">运行应用程序，点击 " **ContactManager** " 链接，并验证是否可以创建、编辑和删除联系人。</span><span class="sxs-lookup"><span data-stu-id="89e14-147">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="89e14-148">保护用户数据</span><span class="sxs-lookup"><span data-stu-id="89e14-148">Secure user data</span></span>

<span data-ttu-id="89e14-149">以下部分包含创建安全用户数据应用的所有主要步骤。</span><span class="sxs-lookup"><span data-stu-id="89e14-149">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="89e14-150">你可能会发现，引用已完成的项目非常有用。</span><span class="sxs-lookup"><span data-stu-id="89e14-150">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="89e14-151">将联系人数据与用户关联</span><span class="sxs-lookup"><span data-stu-id="89e14-151">Tie the contact data to the user</span></span>

<span data-ttu-id="89e14-152">使用 ASP.NET [Identity](xref:security/authentication/identity) 用户 ID 可确保用户能够编辑其数据，而不是其他用户数据。</span><span class="sxs-lookup"><span data-stu-id="89e14-152">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="89e14-153">将 `OwnerID` 和添加 `ContactStatus` 到 `Contact` 模型：</span><span class="sxs-lookup"><span data-stu-id="89e14-153">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="89e14-154">`OwnerID` 数据库中的表的用户 ID `AspNetUser` [Identity](xref:security/authentication/identity) 。</span><span class="sxs-lookup"><span data-stu-id="89e14-154">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="89e14-155">此 `Status` 字段确定常规用户是否可查看联系人。</span><span class="sxs-lookup"><span data-stu-id="89e14-155">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="89e14-156">创建新的迁移并更新数据库：</span><span class="sxs-lookup"><span data-stu-id="89e14-156">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="89e14-157">将角色服务添加到 Identity</span><span class="sxs-lookup"><span data-stu-id="89e14-157">Add Role services to Identity</span></span>

<span data-ttu-id="89e14-158">追加 [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) 以添加角色服务：</span><span class="sxs-lookup"><span data-stu-id="89e14-158">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a><span data-ttu-id="89e14-159">需要经过身份验证的用户</span><span class="sxs-lookup"><span data-stu-id="89e14-159">Require authenticated users</span></span>

<span data-ttu-id="89e14-160">设置后备身份验证策略，要求对用户进行身份验证：</span><span class="sxs-lookup"><span data-stu-id="89e14-160">Set the fallback authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

<span data-ttu-id="89e14-161">前面突出显示的代码设置了 [后备身份验证策略](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy)。</span><span class="sxs-lookup"><span data-stu-id="89e14-161">The preceding highlighted code sets the [fallback authentication policy](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span></span> <span data-ttu-id="89e14-162">回退身份验证策略要求 \* *_所有_* _ 用户进行身份验证，但 Razor 页面、控制器或操作方法除外。</span><span class="sxs-lookup"><span data-stu-id="89e14-162">The fallback authentication policy requires \* *_all_* _ users to be authenticated, except for Razor Pages, controllers, or action methods with an authentication attribute.</span></span> <span data-ttu-id="89e14-163">例如， Razor 使用或的页、控制器或操作方法 `[AllowAnonymous]` `[Authorize(PolicyName="MyPolicy")]` 使用应用的身份验证属性而不是后备身份验证策略。</span><span class="sxs-lookup"><span data-stu-id="89e14-163">For example, Razor Pages, controllers, or action methods with `[AllowAnonymous]` or `[Authorize(PolicyName="MyPolicy")]` use the applied authentication attribute rather than the fallback authentication policy.</span></span>

<span data-ttu-id="89e14-164">回退身份验证策略：</span><span class="sxs-lookup"><span data-stu-id="89e14-164">The fallback authentication policy:</span></span>

<span data-ttu-id="89e14-165">_ 适用于所有未显式指定身份验证策略的请求。</span><span class="sxs-lookup"><span data-stu-id="89e14-165">_ Is applied to all requests that do not explicitly specify an authentication policy.</span></span> <span data-ttu-id="89e14-166">对于终结点路由服务的请求，这将包括未指定授权属性的任何终结点。</span><span class="sxs-lookup"><span data-stu-id="89e14-166">For requests served by endpoint routing, this would include any endpoint that does not specify an authorization attribute.</span></span> <span data-ttu-id="89e14-167">对于在授权中间件之后由其他中间件提供服务的请求，例如 [静态文件](xref:fundamentals/static-files)，这会将该策略应用到所有请求。</span><span class="sxs-lookup"><span data-stu-id="89e14-167">For requests served by other middleware after the authorization middleware, such as [static files](xref:fundamentals/static-files), this would apply the policy to all requests.</span></span>

<span data-ttu-id="89e14-168">将后备身份验证策略设置为 "要求用户进行身份验证" 可保护新添加的 Razor 页面和控制器。</span><span class="sxs-lookup"><span data-stu-id="89e14-168">Setting the fallback authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="89e14-169">默认情况下，需要进行身份验证比依赖新控制器和 Razor 页面以包括属性更安全 `[Authorize]` 。</span><span class="sxs-lookup"><span data-stu-id="89e14-169">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="89e14-170"><xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions>类还包含 <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="89e14-170">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> class also contains <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType>.</span></span> <span data-ttu-id="89e14-171">`DefaultPolicy` `[Authorize]` 当未指定策略时，是与特性一起使用的策略。</span><span class="sxs-lookup"><span data-stu-id="89e14-171">The `DefaultPolicy` is the policy used with the `[Authorize]` attribute when no policy is specified.</span></span> <span data-ttu-id="89e14-172">`[Authorize]` 不包含命名策略，与不同 `[Authorize(PolicyName="MyPolicy")]` 。</span><span class="sxs-lookup"><span data-stu-id="89e14-172">`[Authorize]` doesn't contain a named policy, unlike `[Authorize(PolicyName="MyPolicy")]`.</span></span>

<span data-ttu-id="89e14-173">有关策略的详细信息，请参阅 <xref:security/authorization/policies> 。</span><span class="sxs-lookup"><span data-stu-id="89e14-173">For more information on policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="89e14-174">MVC 控制器和 Razor 页面要求对所有用户进行身份验证的另一种方法是添加授权筛选器：</span><span class="sxs-lookup"><span data-stu-id="89e14-174">An alternative way for MVC controllers and Razor Pages to require all users be authenticated is adding an authorization filter:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

<span data-ttu-id="89e14-175">前面的代码使用授权筛选器，设置回退策略使用终结点路由。</span><span class="sxs-lookup"><span data-stu-id="89e14-175">The preceding code uses an authorization filter, setting the fallback policy uses endpoint routing.</span></span> <span data-ttu-id="89e14-176">设置回退策略是要求对所有用户进行身份验证的首选方式。</span><span class="sxs-lookup"><span data-stu-id="89e14-176">Setting the fallback policy is the preferred way to require all users be authenticated.</span></span>

<span data-ttu-id="89e14-177">将 [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) 添加到 `Index` 和 `Privacy` 页面，以便匿名用户在注册之前可以获取有关站点的信息：</span><span class="sxs-lookup"><span data-stu-id="89e14-177">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the `Index` and `Privacy` pages so anonymous users can get information about the site before they register:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="89e14-178">配置测试帐户</span><span class="sxs-lookup"><span data-stu-id="89e14-178">Configure the test account</span></span>

<span data-ttu-id="89e14-179">`SeedData`类创建两个帐户：管理员和管理器。</span><span class="sxs-lookup"><span data-stu-id="89e14-179">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="89e14-180">使用 [机密管理器工具](xref:security/app-secrets) 来设置这些帐户的密码。</span><span class="sxs-lookup"><span data-stu-id="89e14-180">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="89e14-181">将项目目录中的密码设置 (包含 *Program.cs* ) 的目录：</span><span class="sxs-lookup"><span data-stu-id="89e14-181">Set the password from the project directory (the directory containing *Program.cs* ):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="89e14-182">如果未指定强密码，则在调用时会引发异常 `SeedData.Initialize` 。</span><span class="sxs-lookup"><span data-stu-id="89e14-182">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="89e14-183">更新 `Main` 以使用测试密码：</span><span class="sxs-lookup"><span data-stu-id="89e14-183">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="89e14-184">创建测试帐户并更新联系人</span><span class="sxs-lookup"><span data-stu-id="89e14-184">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="89e14-185">更新 `Initialize` 类中的方法 `SeedData` ，以创建测试帐户：</span><span class="sxs-lookup"><span data-stu-id="89e14-185">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="89e14-186">向联系人添加管理员用户 ID 和 `ContactStatus` 。</span><span class="sxs-lookup"><span data-stu-id="89e14-186">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="89e14-187">使其中一个联系人 "已提交" 和一个 "已拒绝"。</span><span class="sxs-lookup"><span data-stu-id="89e14-187">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="89e14-188">将用户 ID 和状态添加到所有联系人。</span><span class="sxs-lookup"><span data-stu-id="89e14-188">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="89e14-189">只显示一个联系人：</span><span class="sxs-lookup"><span data-stu-id="89e14-189">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="89e14-190">创建所有者、经理和管理员授权处理程序</span><span class="sxs-lookup"><span data-stu-id="89e14-190">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="89e14-191">`ContactIsOwnerAuthorizationHandler`在 *Authorization* 文件夹中创建一个类。</span><span class="sxs-lookup"><span data-stu-id="89e14-191">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="89e14-192">`ContactIsOwnerAuthorizationHandler`验证对资源的用户是否拥有该资源。</span><span class="sxs-lookup"><span data-stu-id="89e14-192">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="89e14-193">`ContactIsOwnerAuthorizationHandler`调用[上下文。](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)如果当前经过身份验证的用户是联系人所有者，则会成功。</span><span class="sxs-lookup"><span data-stu-id="89e14-193">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="89e14-194">授权处理程序通常：</span><span class="sxs-lookup"><span data-stu-id="89e14-194">Authorization handlers generally:</span></span>

* <span data-ttu-id="89e14-195">`context.Succeed`满足要求时返回。</span><span class="sxs-lookup"><span data-stu-id="89e14-195">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="89e14-196">`Task.CompletedTask`当不满足要求时返回。</span><span class="sxs-lookup"><span data-stu-id="89e14-196">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="89e14-197">`Task.CompletedTask` 不是成功或失败， &mdash; 它允许其他授权处理程序运行。</span><span class="sxs-lookup"><span data-stu-id="89e14-197">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="89e14-198">如果需要显式失败，请返回 [context。失败](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail)。</span><span class="sxs-lookup"><span data-stu-id="89e14-198">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="89e14-199">该应用程序允许联系人所有者编辑/删除/创建他们自己的数据。</span><span class="sxs-lookup"><span data-stu-id="89e14-199">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="89e14-200">`ContactIsOwnerAuthorizationHandler` 不需要检查在要求参数中传递的操作。</span><span class="sxs-lookup"><span data-stu-id="89e14-200">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="89e14-201">创建管理器授权处理程序</span><span class="sxs-lookup"><span data-stu-id="89e14-201">Create a manager authorization handler</span></span>

<span data-ttu-id="89e14-202">`ContactManagerAuthorizationHandler`在 *Authorization* 文件夹中创建一个类。</span><span class="sxs-lookup"><span data-stu-id="89e14-202">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="89e14-203">`ContactManagerAuthorizationHandler`验证对资源的用户是否为管理员。</span><span class="sxs-lookup"><span data-stu-id="89e14-203">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="89e14-204">只有管理人员才能 (新的或更改的) 批准或拒绝内容更改。</span><span class="sxs-lookup"><span data-stu-id="89e14-204">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="89e14-205">创建管理员授权处理程序</span><span class="sxs-lookup"><span data-stu-id="89e14-205">Create an administrator authorization handler</span></span>

<span data-ttu-id="89e14-206">`ContactAdministratorsAuthorizationHandler`在 *Authorization* 文件夹中创建一个类。</span><span class="sxs-lookup"><span data-stu-id="89e14-206">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="89e14-207">`ContactAdministratorsAuthorizationHandler`验证对资源的用户是否为管理员。</span><span class="sxs-lookup"><span data-stu-id="89e14-207">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="89e14-208">管理员可以执行所有操作。</span><span class="sxs-lookup"><span data-stu-id="89e14-208">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="89e14-209">注册授权处理程序</span><span class="sxs-lookup"><span data-stu-id="89e14-209">Register the authorization handlers</span></span>

<span data-ttu-id="89e14-210">Entity Framework Core 使用 AddScoped 的服务必须使用[AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)注册以进行[依赖关系注入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="89e14-210">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="89e14-211">`ContactIsOwnerAuthorizationHandler`使用 [Identity](xref:security/authentication/identity) 在 Entity Framework Core 上构建 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="89e14-211">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="89e14-212">向服务集合注册处理程序，使其可 `ContactsController` 通过 [依赖关系注入](xref:fundamentals/dependency-injection)获得。</span><span class="sxs-lookup"><span data-stu-id="89e14-212">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="89e14-213">将以下代码添加到的末尾 `ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="89e14-213">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="89e14-214">`ContactAdministratorsAuthorizationHandler` 和 `ContactManagerAuthorizationHandler` 将添加为单一实例。</span><span class="sxs-lookup"><span data-stu-id="89e14-214">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="89e14-215">它们是单一实例的，因为它们不使用 EF，并且所需的所有信息都在 `Context` 方法的参数中 `HandleRequirementAsync` 。</span><span class="sxs-lookup"><span data-stu-id="89e14-215">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="89e14-216">支持授权</span><span class="sxs-lookup"><span data-stu-id="89e14-216">Support authorization</span></span>

<span data-ttu-id="89e14-217">在本部分中，将更新 Razor 页面并添加操作要求类。</span><span class="sxs-lookup"><span data-stu-id="89e14-217">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="89e14-218">查看联系操作要求类</span><span class="sxs-lookup"><span data-stu-id="89e14-218">Review the contact operations requirements class</span></span>

<span data-ttu-id="89e14-219">查看 `ContactOperations` 类。</span><span class="sxs-lookup"><span data-stu-id="89e14-219">Review the `ContactOperations` class.</span></span> <span data-ttu-id="89e14-220">此类包含应用支持的要求：</span><span class="sxs-lookup"><span data-stu-id="89e14-220">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="89e14-221">为联系人页创建基类 Razor</span><span class="sxs-lookup"><span data-stu-id="89e14-221">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="89e14-222">创建一个包含联系人页中使用的服务的基类 Razor 。</span><span class="sxs-lookup"><span data-stu-id="89e14-222">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="89e14-223">基类将初始化代码放在一个位置：</span><span class="sxs-lookup"><span data-stu-id="89e14-223">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="89e14-224">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="89e14-224">The preceding code:</span></span>

* <span data-ttu-id="89e14-225">添加 `IAuthorizationService` 服务以访问授权处理程序。</span><span class="sxs-lookup"><span data-stu-id="89e14-225">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="89e14-226">添加 Identity `UserManager` 服务。</span><span class="sxs-lookup"><span data-stu-id="89e14-226">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="89e14-227">添加 `ApplicationDbContext`。</span><span class="sxs-lookup"><span data-stu-id="89e14-227">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="89e14-228">更新 CreateModel</span><span class="sxs-lookup"><span data-stu-id="89e14-228">Update the CreateModel</span></span>

<span data-ttu-id="89e14-229">更新 "创建页模型" 构造函数以使用 `DI_BasePageModel` 基类：</span><span class="sxs-lookup"><span data-stu-id="89e14-229">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="89e14-230">将 `CreateModel.OnPostAsync` 方法更新为：</span><span class="sxs-lookup"><span data-stu-id="89e14-230">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="89e14-231">将用户 ID 添加到 `Contact` 模型。</span><span class="sxs-lookup"><span data-stu-id="89e14-231">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="89e14-232">调用授权处理程序以验证用户是否有权创建联系人。</span><span class="sxs-lookup"><span data-stu-id="89e14-232">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="89e14-233">更新 IndexModel</span><span class="sxs-lookup"><span data-stu-id="89e14-233">Update the IndexModel</span></span>

<span data-ttu-id="89e14-234">更新 `OnGetAsync` 方法以便仅向一般用户显示已批准的联系人：</span><span class="sxs-lookup"><span data-stu-id="89e14-234">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="89e14-235">更新 EditModel</span><span class="sxs-lookup"><span data-stu-id="89e14-235">Update the EditModel</span></span>

<span data-ttu-id="89e14-236">添加一个授权处理程序来验证用户是否拥有该联系人。</span><span class="sxs-lookup"><span data-stu-id="89e14-236">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="89e14-237">由于正在验证资源授权，因此 `[Authorize]` 属性不够。</span><span class="sxs-lookup"><span data-stu-id="89e14-237">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="89e14-238">计算属性时，应用无法访问资源。</span><span class="sxs-lookup"><span data-stu-id="89e14-238">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="89e14-239">基于资源的授权必须是必需的。</span><span class="sxs-lookup"><span data-stu-id="89e14-239">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="89e14-240">如果应用有权访问该资源，则必须执行检查，方法是将其加载到页面模型中，或在处理程序本身中加载它。</span><span class="sxs-lookup"><span data-stu-id="89e14-240">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="89e14-241">通过传入资源键，可以频繁地访问资源。</span><span class="sxs-lookup"><span data-stu-id="89e14-241">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="89e14-242">更新 DeleteModel</span><span class="sxs-lookup"><span data-stu-id="89e14-242">Update the DeleteModel</span></span>

<span data-ttu-id="89e14-243">更新 "删除" 页模型，以使用授权处理程序来验证用户是否具有对联系人的 "删除" 权限。</span><span class="sxs-lookup"><span data-stu-id="89e14-243">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="89e14-244">将授权服务注入视图</span><span class="sxs-lookup"><span data-stu-id="89e14-244">Inject the authorization service into the views</span></span>

<span data-ttu-id="89e14-245">目前，UI 会显示用户不能修改的联系人的编辑和删除链接。</span><span class="sxs-lookup"><span data-stu-id="89e14-245">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="89e14-246">将授权服务注入 *Pages/_ViewImports cshtml* 文件，使其可供所有视图使用：</span><span class="sxs-lookup"><span data-stu-id="89e14-246">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="89e14-247">前面的标记添加了多个 `using` 语句。</span><span class="sxs-lookup"><span data-stu-id="89e14-247">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="89e14-248">更新 *页面/联系人/索引* 中的 " **编辑** " 和 " **删除** " 链接，以便仅为具有适当权限的用户呈现它们：</span><span class="sxs-lookup"><span data-stu-id="89e14-248">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="89e14-249">隐藏不具有更改数据权限的用户的链接不会保护应用的安全。</span><span class="sxs-lookup"><span data-stu-id="89e14-249">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="89e14-250">隐藏链接使应用程序更易于用户理解，只显示有效的链接。</span><span class="sxs-lookup"><span data-stu-id="89e14-250">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="89e14-251">用户可以通过攻击生成的 Url 来对其不拥有的数据调用编辑和删除操作。</span><span class="sxs-lookup"><span data-stu-id="89e14-251">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="89e14-252">Razor页或控制器必须强制进行访问检查以确保数据的安全。</span><span class="sxs-lookup"><span data-stu-id="89e14-252">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="89e14-253">更新详细信息</span><span class="sxs-lookup"><span data-stu-id="89e14-253">Update Details</span></span>

<span data-ttu-id="89e14-254">更新详细信息视图，以便经理可以批准或拒绝联系人：</span><span class="sxs-lookup"><span data-stu-id="89e14-254">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="89e14-255">更新详细信息页模型：</span><span class="sxs-lookup"><span data-stu-id="89e14-255">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="89e14-256">在角色中添加或删除用户</span><span class="sxs-lookup"><span data-stu-id="89e14-256">Add or remove a user to a role</span></span>

<span data-ttu-id="89e14-257">有关信息，请参阅 [此问题](https://github.com/dotnet/AspNetCore.Docs/issues/8502) ：</span><span class="sxs-lookup"><span data-stu-id="89e14-257">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="89e14-258">正在删除用户的权限。</span><span class="sxs-lookup"><span data-stu-id="89e14-258">Removing privileges from a user.</span></span> <span data-ttu-id="89e14-259">例如，在聊天应用中对用户进行静音。</span><span class="sxs-lookup"><span data-stu-id="89e14-259">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="89e14-260">向用户添加特权。</span><span class="sxs-lookup"><span data-stu-id="89e14-260">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="89e14-261">质询和禁止之间的差异</span><span class="sxs-lookup"><span data-stu-id="89e14-261">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="89e14-262">此应用将默认策略设置为 " [需要经过身份验证的用户](#rau)"。</span><span class="sxs-lookup"><span data-stu-id="89e14-262">This app sets the default policy to [require authenticated users](#rau).</span></span> <span data-ttu-id="89e14-263">以下代码允许匿名用户。</span><span class="sxs-lookup"><span data-stu-id="89e14-263">The following code allows anonymous users.</span></span> <span data-ttu-id="89e14-264">允许匿名用户显示质询与禁止之间的差异。</span><span class="sxs-lookup"><span data-stu-id="89e14-264">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="89e14-265">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="89e14-265">In the preceding code:</span></span>

* <span data-ttu-id="89e14-266">如果用户 **未通过身份** 验证， `ChallengeResult` 则返回。</span><span class="sxs-lookup"><span data-stu-id="89e14-266">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="89e14-267">返回后 `ChallengeResult` ，会将用户重定向到登录页。</span><span class="sxs-lookup"><span data-stu-id="89e14-267">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="89e14-268">如果用户已通过身份验证，但未获得授权， `ForbidResult` 则返回。</span><span class="sxs-lookup"><span data-stu-id="89e14-268">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="89e14-269">返回后 `ForbidResult` ，会将用户重定向到 "拒绝访问" 页。</span><span class="sxs-lookup"><span data-stu-id="89e14-269">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="89e14-270">测试已完成的应用程序</span><span class="sxs-lookup"><span data-stu-id="89e14-270">Test the completed app</span></span>

<span data-ttu-id="89e14-271">如果尚未为种子设定用户帐户设置密码，请使用 [机密管理器工具](xref:security/app-secrets#secret-manager) 设置密码：</span><span class="sxs-lookup"><span data-stu-id="89e14-271">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="89e14-272">选择强密码：使用八个或更多字符，并且至少使用一个大写字符、数字和符号。</span><span class="sxs-lookup"><span data-stu-id="89e14-272">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="89e14-273">例如， `Passw0rd!` 满足强密码要求。</span><span class="sxs-lookup"><span data-stu-id="89e14-273">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="89e14-274">从项目的文件夹中执行以下命令，其中 `<PW>` 是密码：</span><span class="sxs-lookup"><span data-stu-id="89e14-274">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="89e14-275">如果应用有联系人：</span><span class="sxs-lookup"><span data-stu-id="89e14-275">If the app has contacts:</span></span>

* <span data-ttu-id="89e14-276">删除表中的所有记录 `Contact` 。</span><span class="sxs-lookup"><span data-stu-id="89e14-276">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="89e14-277">重新启动应用以对数据库进行种子设定。</span><span class="sxs-lookup"><span data-stu-id="89e14-277">Restart the app to seed the database.</span></span>

<span data-ttu-id="89e14-278">测试已完成应用程序的一种简单方法是启动三个不同的浏览器 (或 incognito/InPrivate 会话) 。</span><span class="sxs-lookup"><span data-stu-id="89e14-278">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="89e14-279">在一个浏览器中，注册新用户 (例如 `test@example.com`) 。</span><span class="sxs-lookup"><span data-stu-id="89e14-279">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="89e14-280">使用其他用户登录到每个浏览器。</span><span class="sxs-lookup"><span data-stu-id="89e14-280">Sign in to each browser with a different user.</span></span> <span data-ttu-id="89e14-281">验证下列操作：</span><span class="sxs-lookup"><span data-stu-id="89e14-281">Verify the following operations:</span></span>

* <span data-ttu-id="89e14-282">已注册的用户可以查看所有已批准的联系人数据。</span><span class="sxs-lookup"><span data-stu-id="89e14-282">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="89e14-283">已注册的用户可以编辑/删除他们自己的数据。</span><span class="sxs-lookup"><span data-stu-id="89e14-283">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="89e14-284">经理可以批准/拒绝联系人数据。</span><span class="sxs-lookup"><span data-stu-id="89e14-284">Managers can approve/reject contact data.</span></span> <span data-ttu-id="89e14-285">此 `Details` 视图显示 " **批准** " 和 " **拒绝** " 按钮。</span><span class="sxs-lookup"><span data-stu-id="89e14-285">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="89e14-286">管理员可以批准/拒绝和编辑/删除所有数据。</span><span class="sxs-lookup"><span data-stu-id="89e14-286">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="89e14-287">用户</span><span class="sxs-lookup"><span data-stu-id="89e14-287">User</span></span>                | <span data-ttu-id="89e14-288">应用程序的种子</span><span class="sxs-lookup"><span data-stu-id="89e14-288">Seeded by the app</span></span> | <span data-ttu-id="89e14-289">选项</span><span class="sxs-lookup"><span data-stu-id="89e14-289">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="89e14-290">否</span><span class="sxs-lookup"><span data-stu-id="89e14-290">No</span></span>                | <span data-ttu-id="89e14-291">编辑/删除自己的数据。</span><span class="sxs-lookup"><span data-stu-id="89e14-291">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="89e14-292">是</span><span class="sxs-lookup"><span data-stu-id="89e14-292">Yes</span></span>               | <span data-ttu-id="89e14-293">批准/拒绝和编辑/删除自己的数据。</span><span class="sxs-lookup"><span data-stu-id="89e14-293">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="89e14-294">是</span><span class="sxs-lookup"><span data-stu-id="89e14-294">Yes</span></span>               | <span data-ttu-id="89e14-295">批准/拒绝和编辑/删除所有数据。</span><span class="sxs-lookup"><span data-stu-id="89e14-295">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="89e14-296">在管理员的浏览器中创建联系人。</span><span class="sxs-lookup"><span data-stu-id="89e14-296">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="89e14-297">复制管理员联系人的 "删除" 和 "编辑" 的 URL。</span><span class="sxs-lookup"><span data-stu-id="89e14-297">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="89e14-298">将这些链接粘贴到测试用户的浏览器中，以验证测试用户是否无法执行这些操作。</span><span class="sxs-lookup"><span data-stu-id="89e14-298">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="89e14-299">创建初学者应用</span><span class="sxs-lookup"><span data-stu-id="89e14-299">Create the starter app</span></span>

* <span data-ttu-id="89e14-300">创建 Razor 名为 "ContactManager" 的页面应用</span><span class="sxs-lookup"><span data-stu-id="89e14-300">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="89e14-301">创建具有 **单个用户帐户** 的应用。</span><span class="sxs-lookup"><span data-stu-id="89e14-301">Create the app with **Individual User Accounts** .</span></span>
  * <span data-ttu-id="89e14-302">将其命名为 "ContactManager"，使命名空间与该示例中使用的命名空间匹配。</span><span class="sxs-lookup"><span data-stu-id="89e14-302">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="89e14-303">`-uld` 指定 LocalDB 而不是 SQLite</span><span class="sxs-lookup"><span data-stu-id="89e14-303">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="89e14-304">添加 *模型/联系方式* ：</span><span class="sxs-lookup"><span data-stu-id="89e14-304">Add *Models/Contact.cs* :</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="89e14-305">基架 `Contact` 模型。</span><span class="sxs-lookup"><span data-stu-id="89e14-305">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="89e14-306">创建初始迁移并更新数据库：</span><span class="sxs-lookup"><span data-stu-id="89e14-306">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="89e14-307">如果使用命令遇到 bug `dotnet aspnet-codegenerator razorpage` ，请参阅 [此 GitHub 问题](https://github.com/aspnet/Scaffolding/issues/984)。</span><span class="sxs-lookup"><span data-stu-id="89e14-307">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="89e14-308">更新 *Pages/Shared/_Layout cshtml* 文件中的 **ContactManager** 定位点：</span><span class="sxs-lookup"><span data-stu-id="89e14-308">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="89e14-309">通过创建、编辑和删除联系人来测试应用</span><span class="sxs-lookup"><span data-stu-id="89e14-309">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="89e14-310">设定数据库种子</span><span class="sxs-lookup"><span data-stu-id="89e14-310">Seed the database</span></span>

<span data-ttu-id="89e14-311">将 [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) 类添加到 *Data* 文件夹：</span><span class="sxs-lookup"><span data-stu-id="89e14-311">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="89e14-312">调用 `SeedData.Initialize` 自 `Main` ：</span><span class="sxs-lookup"><span data-stu-id="89e14-312">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="89e14-313">测试该应用是否为该数据库的种子。</span><span class="sxs-lookup"><span data-stu-id="89e14-313">Test that the app seeded the database.</span></span> <span data-ttu-id="89e14-314">如果 contact DB 中存在任何行，则 seed 方法不会运行。</span><span class="sxs-lookup"><span data-stu-id="89e14-314">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="89e14-315">本教程演示如何创建 ASP.NET Core 的 web 应用，其中包含由授权保护的用户数据。</span><span class="sxs-lookup"><span data-stu-id="89e14-315">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="89e14-316">它将显示已进行身份验证 (已创建的已注册) 用户的联系人列表。</span><span class="sxs-lookup"><span data-stu-id="89e14-316">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="89e14-317">有三个安全组：</span><span class="sxs-lookup"><span data-stu-id="89e14-317">There are three security groups:</span></span>

* <span data-ttu-id="89e14-318">**已注册的用户** 可以查看所有已批准的数据，并可以编辑/删除他们自己的数据。</span><span class="sxs-lookup"><span data-stu-id="89e14-318">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="89e14-319">**经理** 可以批准或拒绝联系人数据。</span><span class="sxs-lookup"><span data-stu-id="89e14-319">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="89e14-320">只有已批准的联系人对用户可见。</span><span class="sxs-lookup"><span data-stu-id="89e14-320">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="89e14-321">**管理员** 可以批准/拒绝和编辑/删除任何数据。</span><span class="sxs-lookup"><span data-stu-id="89e14-321">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="89e14-322">在下图中，用户 Rick (`rick@example.com`) 已登录。</span><span class="sxs-lookup"><span data-stu-id="89e14-322">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="89e14-323">Rick 只能查看已批准的联系人， **编辑** / **删除** / 为其联系人 **创建新** 链接。</span><span class="sxs-lookup"><span data-stu-id="89e14-323">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="89e14-324">只有 Rick 创建的最后一条记录才会显示 " **编辑** " 和 " **删除** " 链接。</span><span class="sxs-lookup"><span data-stu-id="89e14-324">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="89e14-325">在经理或管理员将状态更改为 "已批准" 之前，其他用户将看不到最后一条记录。</span><span class="sxs-lookup"><span data-stu-id="89e14-325">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![显示已登录的 Rick 的屏幕截图](secure-data/_static/rick.png)

<span data-ttu-id="89e14-327">在下图中， `manager@contoso.com` 已登录，并在管理器的角色中：</span><span class="sxs-lookup"><span data-stu-id="89e14-327">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![显示 manager@contoso.com 已登录的屏幕截图](secure-data/_static/manager1.png)

<span data-ttu-id="89e14-329">下图显示了联系人的经理详细信息视图：</span><span class="sxs-lookup"><span data-stu-id="89e14-329">The following image shows the managers details view of a contact:</span></span>

![联系人的经理视图](secure-data/_static/manager.png)

<span data-ttu-id="89e14-331">" **批准** " 和 " **拒绝** " 按钮仅为经理和管理员显示。</span><span class="sxs-lookup"><span data-stu-id="89e14-331">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="89e14-332">在下图中，以 `admin@contoso.com` 管理员的角色登录和：</span><span class="sxs-lookup"><span data-stu-id="89e14-332">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![显示 admin@contoso.com 已登录的屏幕截图](secure-data/_static/admin.png)

<span data-ttu-id="89e14-334">管理员具有所有权限。</span><span class="sxs-lookup"><span data-stu-id="89e14-334">The administrator has all privileges.</span></span> <span data-ttu-id="89e14-335">她可以读取/编辑/删除任何联系人并更改联系人的状态。</span><span class="sxs-lookup"><span data-stu-id="89e14-335">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="89e14-336">此应用是通过 [基架](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) 创建的 `Contact` ：以下模型：</span><span class="sxs-lookup"><span data-stu-id="89e14-336">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="89e14-337">该示例包含以下授权处理程序：</span><span class="sxs-lookup"><span data-stu-id="89e14-337">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="89e14-338">`ContactIsOwnerAuthorizationHandler`：确保用户只能编辑其数据。</span><span class="sxs-lookup"><span data-stu-id="89e14-338">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="89e14-339">`ContactManagerAuthorizationHandler`：允许经理批准或拒绝联系人。</span><span class="sxs-lookup"><span data-stu-id="89e14-339">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="89e14-340">`ContactAdministratorsAuthorizationHandler`：允许管理员批准或拒绝联系人以及编辑/删除联系人。</span><span class="sxs-lookup"><span data-stu-id="89e14-340">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="89e14-341">先决条件</span><span class="sxs-lookup"><span data-stu-id="89e14-341">Prerequisites</span></span>

<span data-ttu-id="89e14-342">本教程是高级教程。</span><span class="sxs-lookup"><span data-stu-id="89e14-342">This tutorial is advanced.</span></span> <span data-ttu-id="89e14-343">你应该熟悉：</span><span class="sxs-lookup"><span data-stu-id="89e14-343">You should be familiar with:</span></span>

* [<span data-ttu-id="89e14-344">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="89e14-344">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="89e14-345">身份验证</span><span class="sxs-lookup"><span data-stu-id="89e14-345">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="89e14-346">帐户确认和密码恢复</span><span class="sxs-lookup"><span data-stu-id="89e14-346">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="89e14-347">授权</span><span class="sxs-lookup"><span data-stu-id="89e14-347">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="89e14-348">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="89e14-348">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="89e14-349">入门和已完成的应用程序</span><span class="sxs-lookup"><span data-stu-id="89e14-349">The starter and completed app</span></span>

<span data-ttu-id="89e14-350">[下载](xref:index#how-to-download-a-sample)[已完成](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples)的应用。</span><span class="sxs-lookup"><span data-stu-id="89e14-350">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="89e14-351">[测试](#test-the-completed-app) 已完成的应用程序，使其安全功能熟悉。</span><span class="sxs-lookup"><span data-stu-id="89e14-351">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="89e14-352">入门应用</span><span class="sxs-lookup"><span data-stu-id="89e14-352">The starter app</span></span>

<span data-ttu-id="89e14-353">[下载](xref:index#how-to-download-a-sample)[初学者](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/)应用。</span><span class="sxs-lookup"><span data-stu-id="89e14-353">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="89e14-354">运行应用程序，点击 " **ContactManager** " 链接，并验证是否可以创建、编辑和删除联系人。</span><span class="sxs-lookup"><span data-stu-id="89e14-354">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="89e14-355">保护用户数据</span><span class="sxs-lookup"><span data-stu-id="89e14-355">Secure user data</span></span>

<span data-ttu-id="89e14-356">以下部分包含创建安全用户数据应用的所有主要步骤。</span><span class="sxs-lookup"><span data-stu-id="89e14-356">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="89e14-357">你可能会发现，引用已完成的项目非常有用。</span><span class="sxs-lookup"><span data-stu-id="89e14-357">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="89e14-358">将联系人数据与用户关联</span><span class="sxs-lookup"><span data-stu-id="89e14-358">Tie the contact data to the user</span></span>

<span data-ttu-id="89e14-359">使用 ASP.NET [Identity](xref:security/authentication/identity) 用户 ID 可确保用户能够编辑其数据，而不是其他用户数据。</span><span class="sxs-lookup"><span data-stu-id="89e14-359">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="89e14-360">将 `OwnerID` 和添加 `ContactStatus` 到 `Contact` 模型：</span><span class="sxs-lookup"><span data-stu-id="89e14-360">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="89e14-361">`OwnerID` 数据库中的表的用户 ID `AspNetUser` [Identity](xref:security/authentication/identity) 。</span><span class="sxs-lookup"><span data-stu-id="89e14-361">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="89e14-362">此 `Status` 字段确定常规用户是否可查看联系人。</span><span class="sxs-lookup"><span data-stu-id="89e14-362">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="89e14-363">创建新的迁移并更新数据库：</span><span class="sxs-lookup"><span data-stu-id="89e14-363">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="89e14-364">将角色服务添加到 Identity</span><span class="sxs-lookup"><span data-stu-id="89e14-364">Add Role services to Identity</span></span>

<span data-ttu-id="89e14-365">追加 [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) 以添加角色服务：</span><span class="sxs-lookup"><span data-stu-id="89e14-365">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a><span data-ttu-id="89e14-366">需要经过身份验证的用户</span><span class="sxs-lookup"><span data-stu-id="89e14-366">Require authenticated users</span></span>

<span data-ttu-id="89e14-367">将默认的 "身份验证策略" 设置为 "要求用户进行身份验证"：</span><span class="sxs-lookup"><span data-stu-id="89e14-367">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="89e14-368">您可以 Razor 通过属性在页、控制器或操作方法级别选择不进行身份验证 `[AllowAnonymous]` 。</span><span class="sxs-lookup"><span data-stu-id="89e14-368">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="89e14-369">将默认身份验证策略设置为 "要求用户进行身份验证" 可保护新添加的 Razor 页面和控制器。</span><span class="sxs-lookup"><span data-stu-id="89e14-369">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="89e14-370">默认情况下，需要进行身份验证比依赖新控制器和 Razor 页面以包括属性更安全 `[Authorize]` 。</span><span class="sxs-lookup"><span data-stu-id="89e14-370">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="89e14-371">将 [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) 添加到 "索引"、"关于" 和 "联系人" 页，以便匿名用户在注册之前可以获取有关站点的信息。</span><span class="sxs-lookup"><span data-stu-id="89e14-371">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="89e14-372">配置测试帐户</span><span class="sxs-lookup"><span data-stu-id="89e14-372">Configure the test account</span></span>

<span data-ttu-id="89e14-373">`SeedData`类创建两个帐户：管理员和管理器。</span><span class="sxs-lookup"><span data-stu-id="89e14-373">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="89e14-374">使用 [机密管理器工具](xref:security/app-secrets) 来设置这些帐户的密码。</span><span class="sxs-lookup"><span data-stu-id="89e14-374">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="89e14-375">将项目目录中的密码设置 (包含 *Program.cs* ) 的目录：</span><span class="sxs-lookup"><span data-stu-id="89e14-375">Set the password from the project directory (the directory containing *Program.cs* ):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="89e14-376">如果未指定强密码，则在调用时会引发异常 `SeedData.Initialize` 。</span><span class="sxs-lookup"><span data-stu-id="89e14-376">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="89e14-377">更新 `Main` 以使用测试密码：</span><span class="sxs-lookup"><span data-stu-id="89e14-377">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="89e14-378">创建测试帐户并更新联系人</span><span class="sxs-lookup"><span data-stu-id="89e14-378">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="89e14-379">更新 `Initialize` 类中的方法 `SeedData` ，以创建测试帐户：</span><span class="sxs-lookup"><span data-stu-id="89e14-379">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="89e14-380">向联系人添加管理员用户 ID 和 `ContactStatus` 。</span><span class="sxs-lookup"><span data-stu-id="89e14-380">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="89e14-381">使其中一个联系人 "已提交" 和一个 "已拒绝"。</span><span class="sxs-lookup"><span data-stu-id="89e14-381">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="89e14-382">将用户 ID 和状态添加到所有联系人。</span><span class="sxs-lookup"><span data-stu-id="89e14-382">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="89e14-383">只显示一个联系人：</span><span class="sxs-lookup"><span data-stu-id="89e14-383">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="89e14-384">创建所有者、经理和管理员授权处理程序</span><span class="sxs-lookup"><span data-stu-id="89e14-384">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="89e14-385">创建一个 *授权* 文件夹并 `ContactIsOwnerAuthorizationHandler` 在其中创建一个类。</span><span class="sxs-lookup"><span data-stu-id="89e14-385">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="89e14-386">`ContactIsOwnerAuthorizationHandler`验证对资源的用户是否拥有该资源。</span><span class="sxs-lookup"><span data-stu-id="89e14-386">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="89e14-387">`ContactIsOwnerAuthorizationHandler`调用[上下文。](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)如果当前经过身份验证的用户是联系人所有者，则会成功。</span><span class="sxs-lookup"><span data-stu-id="89e14-387">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="89e14-388">授权处理程序通常：</span><span class="sxs-lookup"><span data-stu-id="89e14-388">Authorization handlers generally:</span></span>

* <span data-ttu-id="89e14-389">`context.Succeed`满足要求时返回。</span><span class="sxs-lookup"><span data-stu-id="89e14-389">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="89e14-390">`Task.CompletedTask`当不满足要求时返回。</span><span class="sxs-lookup"><span data-stu-id="89e14-390">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="89e14-391">`Task.CompletedTask` 不是成功或失败， &mdash; 它允许其他授权处理程序运行。</span><span class="sxs-lookup"><span data-stu-id="89e14-391">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="89e14-392">如果需要显式失败，请返回 [context。失败](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail)。</span><span class="sxs-lookup"><span data-stu-id="89e14-392">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="89e14-393">该应用程序允许联系人所有者编辑/删除/创建他们自己的数据。</span><span class="sxs-lookup"><span data-stu-id="89e14-393">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="89e14-394">`ContactIsOwnerAuthorizationHandler` 不需要检查在要求参数中传递的操作。</span><span class="sxs-lookup"><span data-stu-id="89e14-394">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="89e14-395">创建管理器授权处理程序</span><span class="sxs-lookup"><span data-stu-id="89e14-395">Create a manager authorization handler</span></span>

<span data-ttu-id="89e14-396">`ContactManagerAuthorizationHandler`在 *Authorization* 文件夹中创建一个类。</span><span class="sxs-lookup"><span data-stu-id="89e14-396">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="89e14-397">`ContactManagerAuthorizationHandler`验证对资源的用户是否为管理员。</span><span class="sxs-lookup"><span data-stu-id="89e14-397">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="89e14-398">只有管理人员才能 (新的或更改的) 批准或拒绝内容更改。</span><span class="sxs-lookup"><span data-stu-id="89e14-398">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="89e14-399">创建管理员授权处理程序</span><span class="sxs-lookup"><span data-stu-id="89e14-399">Create an administrator authorization handler</span></span>

<span data-ttu-id="89e14-400">`ContactAdministratorsAuthorizationHandler`在 *Authorization* 文件夹中创建一个类。</span><span class="sxs-lookup"><span data-stu-id="89e14-400">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="89e14-401">`ContactAdministratorsAuthorizationHandler`验证对资源的用户是否为管理员。</span><span class="sxs-lookup"><span data-stu-id="89e14-401">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="89e14-402">管理员可以执行所有操作。</span><span class="sxs-lookup"><span data-stu-id="89e14-402">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="89e14-403">注册授权处理程序</span><span class="sxs-lookup"><span data-stu-id="89e14-403">Register the authorization handlers</span></span>

<span data-ttu-id="89e14-404">Entity Framework Core 使用 AddScoped 的服务必须使用[AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)注册以进行[依赖关系注入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="89e14-404">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="89e14-405">`ContactIsOwnerAuthorizationHandler`使用 [Identity](xref:security/authentication/identity) 在 Entity Framework Core 上构建 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="89e14-405">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="89e14-406">向服务集合注册处理程序，使其可 `ContactsController` 通过 [依赖关系注入](xref:fundamentals/dependency-injection)获得。</span><span class="sxs-lookup"><span data-stu-id="89e14-406">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="89e14-407">将以下代码添加到的末尾 `ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="89e14-407">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="89e14-408">`ContactAdministratorsAuthorizationHandler` 和 `ContactManagerAuthorizationHandler` 将添加为单一实例。</span><span class="sxs-lookup"><span data-stu-id="89e14-408">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="89e14-409">它们是单一实例的，因为它们不使用 EF，并且所需的所有信息都在 `Context` 方法的参数中 `HandleRequirementAsync` 。</span><span class="sxs-lookup"><span data-stu-id="89e14-409">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="89e14-410">支持授权</span><span class="sxs-lookup"><span data-stu-id="89e14-410">Support authorization</span></span>

<span data-ttu-id="89e14-411">在本部分中，将更新 Razor 页面并添加操作要求类。</span><span class="sxs-lookup"><span data-stu-id="89e14-411">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="89e14-412">查看联系操作要求类</span><span class="sxs-lookup"><span data-stu-id="89e14-412">Review the contact operations requirements class</span></span>

<span data-ttu-id="89e14-413">查看 `ContactOperations` 类。</span><span class="sxs-lookup"><span data-stu-id="89e14-413">Review the `ContactOperations` class.</span></span> <span data-ttu-id="89e14-414">此类包含应用支持的要求：</span><span class="sxs-lookup"><span data-stu-id="89e14-414">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="89e14-415">为联系人页创建基类 Razor</span><span class="sxs-lookup"><span data-stu-id="89e14-415">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="89e14-416">创建一个包含联系人页中使用的服务的基类 Razor 。</span><span class="sxs-lookup"><span data-stu-id="89e14-416">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="89e14-417">基类将初始化代码放在一个位置：</span><span class="sxs-lookup"><span data-stu-id="89e14-417">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="89e14-418">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="89e14-418">The preceding code:</span></span>

* <span data-ttu-id="89e14-419">添加 `IAuthorizationService` 服务以访问授权处理程序。</span><span class="sxs-lookup"><span data-stu-id="89e14-419">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="89e14-420">添加 Identity `UserManager` 服务。</span><span class="sxs-lookup"><span data-stu-id="89e14-420">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="89e14-421">添加 `ApplicationDbContext`。</span><span class="sxs-lookup"><span data-stu-id="89e14-421">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="89e14-422">更新 CreateModel</span><span class="sxs-lookup"><span data-stu-id="89e14-422">Update the CreateModel</span></span>

<span data-ttu-id="89e14-423">更新 "创建页模型" 构造函数以使用 `DI_BasePageModel` 基类：</span><span class="sxs-lookup"><span data-stu-id="89e14-423">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="89e14-424">将 `CreateModel.OnPostAsync` 方法更新为：</span><span class="sxs-lookup"><span data-stu-id="89e14-424">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="89e14-425">将用户 ID 添加到 `Contact` 模型。</span><span class="sxs-lookup"><span data-stu-id="89e14-425">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="89e14-426">调用授权处理程序以验证用户是否有权创建联系人。</span><span class="sxs-lookup"><span data-stu-id="89e14-426">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="89e14-427">更新 IndexModel</span><span class="sxs-lookup"><span data-stu-id="89e14-427">Update the IndexModel</span></span>

<span data-ttu-id="89e14-428">更新 `OnGetAsync` 方法以便仅向一般用户显示已批准的联系人：</span><span class="sxs-lookup"><span data-stu-id="89e14-428">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="89e14-429">更新 EditModel</span><span class="sxs-lookup"><span data-stu-id="89e14-429">Update the EditModel</span></span>

<span data-ttu-id="89e14-430">添加一个授权处理程序来验证用户是否拥有该联系人。</span><span class="sxs-lookup"><span data-stu-id="89e14-430">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="89e14-431">由于正在验证资源授权，因此 `[Authorize]` 属性不够。</span><span class="sxs-lookup"><span data-stu-id="89e14-431">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="89e14-432">计算属性时，应用无法访问资源。</span><span class="sxs-lookup"><span data-stu-id="89e14-432">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="89e14-433">基于资源的授权必须是必需的。</span><span class="sxs-lookup"><span data-stu-id="89e14-433">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="89e14-434">如果应用有权访问该资源，则必须执行检查，方法是将其加载到页面模型中，或在处理程序本身中加载它。</span><span class="sxs-lookup"><span data-stu-id="89e14-434">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="89e14-435">通过传入资源键，可以频繁地访问资源。</span><span class="sxs-lookup"><span data-stu-id="89e14-435">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="89e14-436">更新 DeleteModel</span><span class="sxs-lookup"><span data-stu-id="89e14-436">Update the DeleteModel</span></span>

<span data-ttu-id="89e14-437">更新 "删除" 页模型，以使用授权处理程序来验证用户是否具有对联系人的 "删除" 权限。</span><span class="sxs-lookup"><span data-stu-id="89e14-437">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="89e14-438">将授权服务注入视图</span><span class="sxs-lookup"><span data-stu-id="89e14-438">Inject the authorization service into the views</span></span>

<span data-ttu-id="89e14-439">目前，UI 会显示用户不能修改的联系人的编辑和删除链接。</span><span class="sxs-lookup"><span data-stu-id="89e14-439">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="89e14-440">将授权服务注入到 *views/_ViewImports cshtml* 文件中，使其可供所有视图使用：</span><span class="sxs-lookup"><span data-stu-id="89e14-440">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="89e14-441">前面的标记添加了多个 `using` 语句。</span><span class="sxs-lookup"><span data-stu-id="89e14-441">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="89e14-442">更新 *页面/联系人/索引* 中的 " **编辑** " 和 " **删除** " 链接，以便仅为具有适当权限的用户呈现它们：</span><span class="sxs-lookup"><span data-stu-id="89e14-442">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="89e14-443">隐藏不具有更改数据权限的用户的链接不会保护应用的安全。</span><span class="sxs-lookup"><span data-stu-id="89e14-443">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="89e14-444">隐藏链接使应用程序更易于用户理解，只显示有效的链接。</span><span class="sxs-lookup"><span data-stu-id="89e14-444">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="89e14-445">用户可以通过攻击生成的 Url 来对其不拥有的数据调用编辑和删除操作。</span><span class="sxs-lookup"><span data-stu-id="89e14-445">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="89e14-446">Razor页或控制器必须强制进行访问检查以确保数据的安全。</span><span class="sxs-lookup"><span data-stu-id="89e14-446">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="89e14-447">更新详细信息</span><span class="sxs-lookup"><span data-stu-id="89e14-447">Update Details</span></span>

<span data-ttu-id="89e14-448">更新详细信息视图，以便经理可以批准或拒绝联系人：</span><span class="sxs-lookup"><span data-stu-id="89e14-448">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="89e14-449">更新详细信息页模型：</span><span class="sxs-lookup"><span data-stu-id="89e14-449">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="89e14-450">在角色中添加或删除用户</span><span class="sxs-lookup"><span data-stu-id="89e14-450">Add or remove a user to a role</span></span>

<span data-ttu-id="89e14-451">有关信息，请参阅 [此问题](https://github.com/dotnet/AspNetCore.Docs/issues/8502) ：</span><span class="sxs-lookup"><span data-stu-id="89e14-451">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="89e14-452">正在删除用户的权限。</span><span class="sxs-lookup"><span data-stu-id="89e14-452">Removing privileges from a user.</span></span> <span data-ttu-id="89e14-453">例如，在聊天应用中对用户进行静音。</span><span class="sxs-lookup"><span data-stu-id="89e14-453">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="89e14-454">向用户添加特权。</span><span class="sxs-lookup"><span data-stu-id="89e14-454">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="89e14-455">测试已完成的应用程序</span><span class="sxs-lookup"><span data-stu-id="89e14-455">Test the completed app</span></span>

<span data-ttu-id="89e14-456">如果尚未为种子设定用户帐户设置密码，请使用 [机密管理器工具](xref:security/app-secrets#secret-manager) 设置密码：</span><span class="sxs-lookup"><span data-stu-id="89e14-456">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="89e14-457">选择强密码：使用八个或更多字符，并且至少使用一个大写字符、数字和符号。</span><span class="sxs-lookup"><span data-stu-id="89e14-457">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="89e14-458">例如， `Passw0rd!` 满足强密码要求。</span><span class="sxs-lookup"><span data-stu-id="89e14-458">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="89e14-459">从项目的文件夹中执行以下命令，其中 `<PW>` 是密码：</span><span class="sxs-lookup"><span data-stu-id="89e14-459">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="89e14-460">删除和更新数据库</span><span class="sxs-lookup"><span data-stu-id="89e14-460">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="89e14-461">重新启动应用以对数据库进行种子设定。</span><span class="sxs-lookup"><span data-stu-id="89e14-461">Restart the app to seed the database.</span></span>

<span data-ttu-id="89e14-462">测试已完成应用程序的一种简单方法是启动三个不同的浏览器 (或 incognito/InPrivate 会话) 。</span><span class="sxs-lookup"><span data-stu-id="89e14-462">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="89e14-463">在一个浏览器中，注册新用户 (例如 `test@example.com`) 。</span><span class="sxs-lookup"><span data-stu-id="89e14-463">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="89e14-464">使用其他用户登录到每个浏览器。</span><span class="sxs-lookup"><span data-stu-id="89e14-464">Sign in to each browser with a different user.</span></span> <span data-ttu-id="89e14-465">验证下列操作：</span><span class="sxs-lookup"><span data-stu-id="89e14-465">Verify the following operations:</span></span>

* <span data-ttu-id="89e14-466">已注册的用户可以查看所有已批准的联系人数据。</span><span class="sxs-lookup"><span data-stu-id="89e14-466">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="89e14-467">已注册的用户可以编辑/删除他们自己的数据。</span><span class="sxs-lookup"><span data-stu-id="89e14-467">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="89e14-468">经理可以批准/拒绝联系人数据。</span><span class="sxs-lookup"><span data-stu-id="89e14-468">Managers can approve/reject contact data.</span></span> <span data-ttu-id="89e14-469">此 `Details` 视图显示 " **批准** " 和 " **拒绝** " 按钮。</span><span class="sxs-lookup"><span data-stu-id="89e14-469">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="89e14-470">管理员可以批准/拒绝和编辑/删除所有数据。</span><span class="sxs-lookup"><span data-stu-id="89e14-470">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="89e14-471">用户</span><span class="sxs-lookup"><span data-stu-id="89e14-471">User</span></span>                | <span data-ttu-id="89e14-472">应用程序的种子</span><span class="sxs-lookup"><span data-stu-id="89e14-472">Seeded by the app</span></span> | <span data-ttu-id="89e14-473">选项</span><span class="sxs-lookup"><span data-stu-id="89e14-473">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="89e14-474">否</span><span class="sxs-lookup"><span data-stu-id="89e14-474">No</span></span>                | <span data-ttu-id="89e14-475">编辑/删除自己的数据。</span><span class="sxs-lookup"><span data-stu-id="89e14-475">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="89e14-476">是</span><span class="sxs-lookup"><span data-stu-id="89e14-476">Yes</span></span>               | <span data-ttu-id="89e14-477">批准/拒绝和编辑/删除自己的数据。</span><span class="sxs-lookup"><span data-stu-id="89e14-477">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="89e14-478">是</span><span class="sxs-lookup"><span data-stu-id="89e14-478">Yes</span></span>               | <span data-ttu-id="89e14-479">批准/拒绝和编辑/删除所有数据。</span><span class="sxs-lookup"><span data-stu-id="89e14-479">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="89e14-480">在管理员的浏览器中创建联系人。</span><span class="sxs-lookup"><span data-stu-id="89e14-480">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="89e14-481">复制管理员联系人的 "删除" 和 "编辑" 的 URL。</span><span class="sxs-lookup"><span data-stu-id="89e14-481">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="89e14-482">将这些链接粘贴到测试用户的浏览器中，以验证测试用户是否无法执行这些操作。</span><span class="sxs-lookup"><span data-stu-id="89e14-482">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="89e14-483">创建初学者应用</span><span class="sxs-lookup"><span data-stu-id="89e14-483">Create the starter app</span></span>

* <span data-ttu-id="89e14-484">创建 Razor 名为 "ContactManager" 的页面应用</span><span class="sxs-lookup"><span data-stu-id="89e14-484">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="89e14-485">创建具有 **单个用户帐户** 的应用。</span><span class="sxs-lookup"><span data-stu-id="89e14-485">Create the app with **Individual User Accounts** .</span></span>
  * <span data-ttu-id="89e14-486">将其命名为 "ContactManager"，使命名空间与该示例中使用的命名空间匹配。</span><span class="sxs-lookup"><span data-stu-id="89e14-486">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="89e14-487">`-uld` 指定 LocalDB 而不是 SQLite</span><span class="sxs-lookup"><span data-stu-id="89e14-487">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="89e14-488">添加 *模型/联系方式* ：</span><span class="sxs-lookup"><span data-stu-id="89e14-488">Add *Models/Contact.cs* :</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="89e14-489">基架 `Contact` 模型。</span><span class="sxs-lookup"><span data-stu-id="89e14-489">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="89e14-490">创建初始迁移并更新数据库：</span><span class="sxs-lookup"><span data-stu-id="89e14-490">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="89e14-491">更新 *Pages/_Layout cshtml* 文件中的 **ContactManager** 定位点：</span><span class="sxs-lookup"><span data-stu-id="89e14-491">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="89e14-492">通过创建、编辑和删除联系人来测试应用</span><span class="sxs-lookup"><span data-stu-id="89e14-492">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="89e14-493">设定数据库种子</span><span class="sxs-lookup"><span data-stu-id="89e14-493">Seed the database</span></span>

<span data-ttu-id="89e14-494">将 [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) 类添加到 *Data* 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="89e14-494">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="89e14-495">调用 `SeedData.Initialize` 自 `Main` ：</span><span class="sxs-lookup"><span data-stu-id="89e14-495">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="89e14-496">测试该应用是否为该数据库的种子。</span><span class="sxs-lookup"><span data-stu-id="89e14-496">Test that the app seeded the database.</span></span> <span data-ttu-id="89e14-497">如果 contact DB 中存在任何行，则 seed 方法不会运行。</span><span class="sxs-lookup"><span data-stu-id="89e14-497">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="89e14-498">其他资源</span><span class="sxs-lookup"><span data-stu-id="89e14-498">Additional resources</span></span>

* [<span data-ttu-id="89e14-499">在 Azure 应用服务中生成 .NET Core 和 SQL 数据库 Web 应用</span><span class="sxs-lookup"><span data-stu-id="89e14-499">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="89e14-500">[ASP.NET Core 授权实验室](https://github.com/blowdart/AspNetAuthorizationWorkshop)。</span><span class="sxs-lookup"><span data-stu-id="89e14-500">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="89e14-501">此实验室更详细地介绍了本教程中所介绍的安全功能。</span><span class="sxs-lookup"><span data-stu-id="89e14-501">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="89e14-502">自定义基于策略的授权</span><span class="sxs-lookup"><span data-stu-id="89e14-502">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
