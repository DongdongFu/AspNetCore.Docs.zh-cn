<span data-ttu-id="9bb62-101">Blazor 服务器应用位于服务器内存中。</span><span class="sxs-lookup"><span data-stu-id="9bb62-101">Blazor server apps live in server memory.</span></span> <span data-ttu-id="9bb62-102">这意味着同一进程中托管了多个应用。</span><span class="sxs-lookup"><span data-stu-id="9bb62-102">That means that there are multiple apps hosted within the same process.</span></span> <span data-ttu-id="9bb62-103">对于每个应用会话，Blazor 会启动具有其自己的 DI 容器作用域的线路。</span><span class="sxs-lookup"><span data-stu-id="9bb62-103">For each app session, Blazor starts a circuit with its own DI container scope.</span></span> <span data-ttu-id="9bb62-104">这意味着，每个 Blazor 会话的作用域内服务都是唯一的。</span><span class="sxs-lookup"><span data-stu-id="9bb62-104">That means that scoped services are unique per Blazor session.</span></span>

> [!WARNING]
> <span data-ttu-id="9bb62-105">我们不建议同一服务器上的应用共享使用单一实例服务的状态，除非采取了极其谨慎的措施，因为这可能会带来安全漏洞，如跨线路泄露用户状态。</span><span class="sxs-lookup"><span data-stu-id="9bb62-105">We don't recommend apps on the same server share state using singleton services unless extreme care is taken, as this can introduce security vulnerabilities, such as leaking user state across circuits.</span></span>

<span data-ttu-id="9bb62-106">如果有状态的单一实例服务是专门为 Blazor 应用设计的，则可以在该应用中使用这些服务。</span><span class="sxs-lookup"><span data-stu-id="9bb62-106">You can use stateful singleton services in Blazor apps if they are specifically designed for it.</span></span> <span data-ttu-id="9bb62-107">例如，假设用户无法控制使用哪些缓存密钥，则可以将内存缓存用作单一实例，因为它需要一个密钥来访问给定的条目。</span><span class="sxs-lookup"><span data-stu-id="9bb62-107">For example, it's ok to use a memory cache as a singleton because it requires a key to access a given entry, assuming users don't have control of what cache keys are used.</span></span>

<span data-ttu-id="9bb62-108">**另外，出于安全原因，不得在 Blazor 应用中使用 <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor>。**</span><span class="sxs-lookup"><span data-stu-id="9bb62-108">**Additionally, again for security reasons, you must not use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> within Blazor apps.**</span></span> <span data-ttu-id="9bb62-109">Blazor 应用在 ASP.NET Core 管道的上下文之外运行。</span><span class="sxs-lookup"><span data-stu-id="9bb62-109">Blazor apps run outside of the context of the ASP.NET Core pipeline.</span></span> <span data-ttu-id="9bb62-110"><xref:Microsoft.AspNetCore.Http.HttpContext> 既不保证在 <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> 中可用，也不保证它会保留启动了 Blazor 应用的上下文。</span><span class="sxs-lookup"><span data-stu-id="9bb62-110">The <xref:Microsoft.AspNetCore.Http.HttpContext> isn't guaranteed to be available within the <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor>, nor is it guaranteed to be holding the context that started the Blazor app.</span></span>

<span data-ttu-id="9bb62-111">若要向 Blazor 应用传递请求状态，建议在初次呈现应用时通过传递到根组件的参数进行传递：</span><span class="sxs-lookup"><span data-stu-id="9bb62-111">The recommended way to pass request state to the Blazor app is through parameters to the root component in the initial rendering of the app:</span></span>

* <span data-ttu-id="9bb62-112">使用要传递到 Blazor 应用的所有数据定义类。</span><span class="sxs-lookup"><span data-stu-id="9bb62-112">Define a class with all the data you want to pass to the Blazor app.</span></span>
* <span data-ttu-id="9bb62-113">使用目前可用的 <xref:Microsoft.AspNetCore.Http.HttpContext> 在 Razor 页中填充该数据。</span><span class="sxs-lookup"><span data-stu-id="9bb62-113">Populate that data from the Razor page using the <xref:Microsoft.AspNetCore.Http.HttpContext> available at that time.</span></span>
* <span data-ttu-id="9bb62-114">将数据作为传递给根组件（应用）的参数传递给 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="9bb62-114">Pass the data to the Blazor app as a parameter to the root component (App).</span></span>
* <span data-ttu-id="9bb62-115">在根组件中定义一个参数，用于保存即将传递给应用的数据。</span><span class="sxs-lookup"><span data-stu-id="9bb62-115">Define a parameter in the root component to hold the data being passed to the app.</span></span>
* <span data-ttu-id="9bb62-116">在应用中使用用户特定的数据；或者，将该数据复制到 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 中的作用域内的服务，以便可以跨应用使用该数据。</span><span class="sxs-lookup"><span data-stu-id="9bb62-116">Use the user-specific data within the app; or alternatively, copy that data into a scoped service within <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> so that it can be used across the app.</span></span>

<span data-ttu-id="9bb62-117">有关更多信息及代码示例，请参见 <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>。</span><span class="sxs-lookup"><span data-stu-id="9bb62-117">For more information and example code, see <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>