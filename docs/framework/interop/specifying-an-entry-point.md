---
title: 指定入口点
ms.date: 03/30/2017
helpviewer_keywords:
- EntryPoint field
- platform invoke, attribute fields
- attribute fields in platform invoke, EntryPoint
ms.assetid: d1247f08-0965-416a-b978-e0b50652dfe3
ms.openlocfilehash: c5f8f735dd3e8c359f88044a532c29303237acc8
ms.sourcegitcommit: 7588136e355e10cbc2582f389c90c127363c02a5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2020
ms.locfileid: "79181314"
---
# <a name="specifying-an-entry-point"></a>指定入口点

入口点标识 DLL 中的函数位置。 在托管项目中，目标函数的原始名称或序号入口点跨越互操作边界标识该函数。 此外，可将入口点映射到其他名称，有效地重命名该函数。  
  
 以下列表列出了重命名 DLL 函数的可能原因：  
  
- 避免使用区分大小写的 API 函数名  
  
- 符合现有的命名标准  
  
- 提供采用不同数据类型的函数（通过声明同一 DLL 函数的多个版本）  
  
- 简化对包含 ANSI 和 Unicode 版本的 API 的使用  
  
 本主题说明如何在托管代码中重命名 DLL 函数。  
  
## <a name="renaming-a-function-in-visual-basic"></a>重命名 Visual Basic 中的函数  

Visual Basic 在 Declare  语句中使用 Function  关键字设置 <xref:System.Runtime.InteropServices.DllImportAttribute.EntryPoint?displayProperty=nameWithType> 字段。 下面的示例演示了一个基本声明。  
  
```vb
Friend Class NativeMethods
    Friend Declare Auto Function MessageBox Lib "user32.dll" (
        ByVal hWnd As IntPtr,
        ByVal lpText As String,
        ByVal lpCaption As String,
        ByVal uType As UInteger) As Integer
End Class
```
  
如下例所示，通过在定义中包括 Alias 关键字，可以用 MsgBox 替换 MessageBox 入口点    。 在这两个示例中，Auto  关键字使你无需指定入口点的字符集版本。 有关选择字符集的详细信息，请参阅[指定字符集](specifying-a-character-set.md)。  
  
```vb
Friend Class NativeMethods
    Friend Declare Auto Function MsgBox _
        Lib "user32.dll" Alias "MessageBox" (
        ByVal hWnd As IntPtr,
        ByVal lpText As String,
        ByVal lpCaption As String,
        ByVal uType As UInteger) As Integer
End Class
```
  
## <a name="renaming-a-function-in-c-and-c"></a>重命名 C# 和 C++ 中的函数  
 可使用 <xref:System.Runtime.InteropServices.DllImportAttribute.EntryPoint?displayProperty=nameWithType> 字段通过名称或序号指定 DLL 函数。 如果方法定义中函数的名称与 DLL 中入口点的名称相同，则不必使用 EntryPoint  字段显式地标识函数。 否则，使用以下属性形式之一指示名称或序号：  
  
```csharp
[DllImport("DllName", EntryPoint = "Functionname")]
[DllImport("DllName", EntryPoint = "#123")]
```
  
 请注意，序号前必须带有井号 (#)。  
  
 下面的示例演示如何使用 EntryPoint 字段将代码中的 MessageBoxA 替换为 MsgBox    。  
  
```csharp
using System;
using System.Runtime.InteropServices;

internal static class NativeMethods
{
    [DllImport("user32.dll", EntryPoint = "MessageBoxA")]
    internal static extern int MessageBox(
        IntPtr hWnd, string lpText, string lpCaption, uint uType);
}
```
  
```cpp
using namespace System;
using namespace System::Runtime::InteropServices;

typedef void* HWND;
[DllImport("user32", EntryPoint = "MessageBoxA")]
extern "C" int MsgBox(
    HWND hWnd, String* lpText, String* lpCaption, unsigned int uType);
```
  
## <a name="see-also"></a>请参阅

- <xref:System.Runtime.InteropServices.DllImportAttribute>
- [在托管代码中创建原型](creating-prototypes-in-managed-code.md)
- [平台调用示例](platform-invoke-examples.md)
- [用平台调用封送数据](marshaling-data-with-platform-invoke.md)
