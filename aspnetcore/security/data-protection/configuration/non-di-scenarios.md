---
title: ASP.NET Core 中数据保护的非 DI 感知情境
author: rick-anderson
description: 了解如何支持不能或不想使用依赖关系注入提供的服务的数据保护方案。
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/configuration/non-di-scenarios
ms.openlocfilehash: 03257596cafd9ec99f90b44d8fcb878b6747ba39
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052820"
---
# <a name="non-di-aware-scenarios-for-data-protection-in-aspnet-core"></a><span data-ttu-id="223e2-103">ASP.NET Core 中数据保护的非 DI 感知情境</span><span class="sxs-lookup"><span data-stu-id="223e2-103">Non-DI aware scenarios for Data Protection in ASP.NET Core</span></span>

<span data-ttu-id="223e2-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="223e2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="223e2-105">ASP.NET Core 数据保护系统通常会 [添加到服务容器中](xref:security/data-protection/consumer-apis/overview) ，并由从属组件通过依赖关系注入 (DI) 使用。</span><span class="sxs-lookup"><span data-stu-id="223e2-105">The ASP.NET Core Data Protection system is normally [added to a service container](xref:security/data-protection/consumer-apis/overview) and consumed by dependent components via dependency injection (DI).</span></span> <span data-ttu-id="223e2-106">但是，在某些情况下，这种情况并不可行，尤其是将系统导入现有应用时。</span><span class="sxs-lookup"><span data-stu-id="223e2-106">However, there are cases where this isn't feasible or desired, especially when importing the system into an existing app.</span></span>

<span data-ttu-id="223e2-107">为了支持这些方案， [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) 包提供了一个具体类型 [DataProtectionProvider](/dotnet/api/Microsoft.AspNetCore.DataProtection.DataProtectionProvider)，这提供了一种简单的方法来使用数据保护，而无需依赖于 DI。</span><span class="sxs-lookup"><span data-stu-id="223e2-107">To support these scenarios, the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) package provides a concrete type, [DataProtectionProvider](/dotnet/api/Microsoft.AspNetCore.DataProtection.DataProtectionProvider), which offers a simple way to use Data Protection without relying on DI.</span></span> <span data-ttu-id="223e2-108">`DataProtectionProvider`类型实现[IDataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionprovider)。</span><span class="sxs-lookup"><span data-stu-id="223e2-108">The `DataProtectionProvider` type implements [IDataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionprovider).</span></span> <span data-ttu-id="223e2-109">构造 `DataProtectionProvider` 仅要求提供 [DirectoryInfo](/dotnet/api/system.io.directoryinfo) 实例以指示应在何处存储提供程序的加密密钥，如以下代码示例所示：</span><span class="sxs-lookup"><span data-stu-id="223e2-109">Constructing `DataProtectionProvider` only requires providing a [DirectoryInfo](/dotnet/api/system.io.directoryinfo) instance to indicate where the provider's cryptographic keys should be stored, as seen in the following code sample:</span></span>

[!code-csharp[](non-di-scenarios/_static/nodisample1.cs)]

<span data-ttu-id="223e2-110">默认情况下， `DataProtectionProvider` 具体类型不会在将原始密钥材料保存到文件系统之前对其进行加密。</span><span class="sxs-lookup"><span data-stu-id="223e2-110">By default, the `DataProtectionProvider` concrete type doesn't encrypt raw key material before persisting it to the file system.</span></span> <span data-ttu-id="223e2-111">这是为了支持开发人员指向网络共享的情况，并且数据保护系统无法自动推导适当的静止密钥加密机制。</span><span class="sxs-lookup"><span data-stu-id="223e2-111">This is to support scenarios where the developer points to a network share and the Data Protection system can't automatically deduce an appropriate at-rest key encryption mechanism.</span></span>

<span data-ttu-id="223e2-112">此外， `DataProtectionProvider` 默认情况下，具体类型不 [隔离应用](xref:security/data-protection/configuration/overview#per-application-isolation) 。</span><span class="sxs-lookup"><span data-stu-id="223e2-112">Additionally, the `DataProtectionProvider` concrete type doesn't [isolate apps](xref:security/data-protection/configuration/overview#per-application-isolation) by default.</span></span> <span data-ttu-id="223e2-113">只要其 [用途参数](xref:security/data-protection/consumer-apis/purpose-strings) 匹配，使用同一密钥目录的所有应用都可以共享有效负载。</span><span class="sxs-lookup"><span data-stu-id="223e2-113">All apps using the same key directory can share payloads as long as their [purpose parameters](xref:security/data-protection/consumer-apis/purpose-strings) match.</span></span>

<span data-ttu-id="223e2-114">[DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider)构造函数接受可用于调整系统行为的可选配置回调。</span><span class="sxs-lookup"><span data-stu-id="223e2-114">The [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider) constructor accepts an optional configuration callback that can be used to adjust the behaviors of the system.</span></span> <span data-ttu-id="223e2-115">下面的示例演示如何使用对 [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname)的显式调用来还原隔离。</span><span class="sxs-lookup"><span data-stu-id="223e2-115">The sample below demonstrates restoring isolation with an explicit call to [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname).</span></span> <span data-ttu-id="223e2-116">该示例还演示了如何将系统配置为使用 Windows DPAPI 自动加密保留密钥。</span><span class="sxs-lookup"><span data-stu-id="223e2-116">The sample also demonstrates configuring the system to automatically encrypt persisted keys using Windows DPAPI.</span></span> <span data-ttu-id="223e2-117">如果目录指向 UNC 共享，你可能希望在所有相关的计算机上分发共享证书，并将系统配置为使用基于证书的加密，并调用 [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate)。</span><span class="sxs-lookup"><span data-stu-id="223e2-117">If the directory points to a UNC share, you may wish to distribute a shared certificate across all relevant machines and to configure the system to use certificate-based encryption with a call to [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate).</span></span>

[!code-csharp[](non-di-scenarios/_static/nodisample2.cs)]

> [!TIP]
> <span data-ttu-id="223e2-118">`DataProtectionProvider`需要创建具体类型的实例。</span><span class="sxs-lookup"><span data-stu-id="223e2-118">Instances of the `DataProtectionProvider` concrete type are expensive to create.</span></span> <span data-ttu-id="223e2-119">如果应用维护该类型的多个实例，并且它们都使用相同的密钥存储目录，应用性能可能会降低。</span><span class="sxs-lookup"><span data-stu-id="223e2-119">If an app maintains multiple instances of this type and if they're all using the same key storage directory, app performance might degrade.</span></span> <span data-ttu-id="223e2-120">如果使用 `DataProtectionProvider` 类型，建议你创建此类型一次，并尽可能多地重用它。</span><span class="sxs-lookup"><span data-stu-id="223e2-120">If you use the `DataProtectionProvider` type, we recommend that you create this type once and reuse it as much as possible.</span></span> <span data-ttu-id="223e2-121">`DataProtectionProvider`从它创建的类型和所有[IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector)实例对于多个调用方是线程安全的。</span><span class="sxs-lookup"><span data-stu-id="223e2-121">The `DataProtectionProvider` type and all [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector) instances created from it are thread-safe for multiple callers.</span></span>
