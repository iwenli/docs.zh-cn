---
ms.openlocfilehash: d21b2e092d460fdfc367d0f490228ed44ad5c6cc
ms.sourcegitcommit: 63bb83322814f5e5e5c5b69939b14a3139a6ca7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2020
ms.locfileid: "85365634"
---
### <a name="built-in-support-for-winrt-is-removed-from-net"></a><span data-ttu-id="aa12b-101">已从 .NET 中删除对 WinRT 的内置支持</span><span class="sxs-lookup"><span data-stu-id="aa12b-101">Built-in support for WinRT is removed from .NET</span></span>

<span data-ttu-id="aa12b-102">已删除对使用 .NET 中的 [Windows 运行时 (WinRT)](/uwp/winrt-cref/winrt-type-system) API 的内置支持。</span><span class="sxs-lookup"><span data-stu-id="aa12b-102">Built-in support for consumption of [Windows runtime (WinRT)](/uwp/winrt-cref/winrt-type-system) APIs in .NET is removed.</span></span>

#### <a name="version-introduced"></a><span data-ttu-id="aa12b-103">引入的版本</span><span class="sxs-lookup"><span data-stu-id="aa12b-103">Version introduced</span></span>

<span data-ttu-id="aa12b-104">5.0 预览版 6</span><span class="sxs-lookup"><span data-stu-id="aa12b-104">5.0 Preview 6</span></span>

#### <a name="change-description"></a><span data-ttu-id="aa12b-105">更改描述</span><span class="sxs-lookup"><span data-stu-id="aa12b-105">Change description</span></span>

<span data-ttu-id="aa12b-106">以前，CoreCLR 可能会使用 [Windows 元数据 (WinMD) 文件](/uwp/winrt-cref/winmd-files)来激活和使用 WinRT 类型。</span><span class="sxs-lookup"><span data-stu-id="aa12b-106">Previously, CoreCLR could consume [Windows metadata (WinMD) files](/uwp/winrt-cref/winmd-files) to active and consume WinRT types.</span></span> <span data-ttu-id="aa12b-107">从 .NET 5.0 开始，CoreCLR 不再能够直接使用 WinMD 文件。</span><span class="sxs-lookup"><span data-stu-id="aa12b-107">Starting in .NET 5.0, CoreCLR can no longer consume WinMD files directly.</span></span>

<span data-ttu-id="aa12b-108">如果尝试引用不受支持的程序集，则会收到 <xref:System.IO.FileNotFoundException>。</span><span class="sxs-lookup"><span data-stu-id="aa12b-108">If you attempt to reference an unsupported assembly, you'll get a <xref:System.IO.FileNotFoundException>.</span></span> <span data-ttu-id="aa12b-109">如果激活 WinRT 类，则会收到 <xref:System.PlatformNotSupportedException>。</span><span class="sxs-lookup"><span data-stu-id="aa12b-109">If you activate a WinRT class, you'll get a <xref:System.PlatformNotSupportedException>.</span></span>

<span data-ttu-id="aa12b-110">做出此中断性变更的原因如下：</span><span class="sxs-lookup"><span data-stu-id="aa12b-110">This breaking change was made for the following reasons:</span></span>

- <span data-ttu-id="aa12b-111">便于独立于 .NET 运行时单独开发和改进 WinRT。</span><span class="sxs-lookup"><span data-stu-id="aa12b-111">So WinRT can be developed and improved separately from the .NET runtime.</span></span>
- <span data-ttu-id="aa12b-112">为了与为其他操作系统（例如 iOS 和 Android）提供的互操作系统对称。</span><span class="sxs-lookup"><span data-stu-id="aa12b-112">For symmetry with interop systems provided for other operating systems, such as iOS and Android.</span></span>
- <span data-ttu-id="aa12b-113">为了利用其他 .NET 功能，例如 C# 功能、中间语言 (IL) 链接和预先 (AOT) 编译。</span><span class="sxs-lookup"><span data-stu-id="aa12b-113">To take advantage of other .NET features, such as C# features, intermediate language (IL) linking, and ahead-of-time (AOT) compilation.</span></span>
- <span data-ttu-id="aa12b-114">为了简化 .NET 运行时基本代码。</span><span class="sxs-lookup"><span data-stu-id="aa12b-114">To simplify the .NET runtime codebase.</span></span>

#### <a name="recommended-action"></a><span data-ttu-id="aa12b-115">建议操作</span><span class="sxs-lookup"><span data-stu-id="aa12b-115">Recommended action</span></span>

- <span data-ttu-id="aa12b-116">删除对 [Microsoft.Windows.SDK.Contracts 包](https://www.nuget.org/packages/Microsoft.Windows.SDK.Contracts)的引用，并将其替换为对 [Microsoft.Windows.SDK.NET 包](https://www.nuget.org/packages/microsoft.windows.sdk.net)的引用。</span><span class="sxs-lookup"><span data-stu-id="aa12b-116">Remove references to the [Microsoft.Windows.SDK.Contracts package](https://www.nuget.org/packages/Microsoft.Windows.SDK.Contracts) and replace them with references to the [Microsoft.Windows.SDK.NET package](https://www.nuget.org/packages/microsoft.windows.sdk.net).</span></span>

- <span data-ttu-id="aa12b-117">使用 [C#/WinRT](/windows/uwp/csharp-winrt/) 工具链在 .NET 5.0 及更高版本中生成或自定义 WinRT API 和类型。</span><span class="sxs-lookup"><span data-stu-id="aa12b-117">Use the [C#/WinRT](/windows/uwp/csharp-winrt/) tool chain to generate or customize WinRT APIs and types in .NET 5.0 and later versions.</span></span>

#### <a name="category"></a><span data-ttu-id="aa12b-118">类别</span><span class="sxs-lookup"><span data-stu-id="aa12b-118">Category</span></span>

<span data-ttu-id="aa12b-119">Interop</span><span class="sxs-lookup"><span data-stu-id="aa12b-119">Interop</span></span>

#### <a name="affected-apis"></a><span data-ttu-id="aa12b-120">受影响的 API</span><span class="sxs-lookup"><span data-stu-id="aa12b-120">Affected APIs</span></span>

- <xref:System.IO.WindowsRuntimeStorageExtensions?displayProperty=fullName>
- <xref:System.IO.WindowsRuntimeStreamExtensions?displayProperty=fullName>
- <xref:System.Runtime.InteropServices.WindowsRuntime?displayProperty=fullName>
- <xref:System.WindowsRuntimeSystemExtensions?displayProperty=fullName>
- <xref:Windows.Foundation.Point?displayProperty=fullName>
- <xref:Windows.Foundation.Size?displayProperty=fullName>
- <xref:Windows.UI.Color?displayProperty=fullName>

<!--

#### Affected APIs

- `T:System.IO.WindowsRuntimeStorageExtensions`
- `T: System.IO.WindowsRuntimeStreamExtensions`
- `N:System.Runtime.InteropServices.WindowsRuntime`
- `T:System.WindowsRuntimeSystemExtensions`
- `T:Windows.Foundation.Point`
- `T:Windows.Foundation.Size`
- `T:Windows.UI.Color`

-->