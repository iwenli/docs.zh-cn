---
title: 如何使用 finally 执行清理代码 - C# 编程指南
ms.date: 07/20/2015
helpviewer_keywords:
- try/finally blocks [C#]
- exceptions [C#], try/finally block
- exception handling [C#], try/finally block
ms.assetid: 1b1e5aef-3f32-4a88-9d39-b5fffb33bdaf
ms.openlocfilehash: d1ba761e64053d656ad4cd004133fc455a57c6f6
ms.sourcegitcommit: 7588136e355e10cbc2582f389c90c127363c02a5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2020
ms.locfileid: "75705270"
---
# <a name="how-to-execute-cleanup-code-using-finally-c-programming-guide"></a>如何使用 finally 执行清理代码（C# 编程指南）
`finally` 语句的用途是确保立即进行对象（通常是容纳外部资源的对象）的必要清理（即使引发异常）。 这类清理的一个示例是在使用之后立即对 <xref:System.IO.FileStream> 调用 <xref:System.IO.Stream.Close%2A>（而不是等待公共语言运行时对对象进行垃圾回收），如下所示：  
  
 [!code-csharp[csProgGuideExceptions#16](~/samples/snippets/csharp/VS_Snippets_VBCSharp/csProgGuideExceptions/CS/Exceptions.cs#16)]  
  
## <a name="example"></a>示例  
 若要将上面的代码转换为 `try-catch-finally` 语句，可将清理代码与工作代码分开，如下所示。  
  
 [!code-csharp[csProgGuideExceptions#17](~/samples/snippets/csharp/VS_Snippets_VBCSharp/csProgGuideExceptions/CS/Exceptions.cs#17)]  
  
 因为可能会在 `try` 块中进行 `OpenWrite()` 调用之前的任何时候发生异常，或 `OpenWrite()` 调用本身可能会失败，所以我们不保证在尝试关闭文件时文件处于打开状态。 `finally` 块添加了一个检查，以便在调用 <xref:System.IO.Stream.Close%2A> 方法之前确保 <xref:System.IO.FileStream> 对象不是 `null`。 如果不进行 `null` 检查，则 `finally` 块可能会引发自己的 <xref:System.NullReferenceException>，但是在可能时应避免在 `finally` 块中引发异常。  
  
 数据库连接是在 `finally` 块中进行关闭的另一个很好的候选项。 因为与数据库服务器之间的允许连接数有时会受到限制，所以应尽快关闭数据库连接。 如果在可以关闭连接之前引发异常，则这是使用 `finally` 块比等待垃圾回收更好的另一种情况。  
  
## <a name="see-also"></a>另请参阅

- [C# 编程指南](../index.md)
- [异常和异常处理](./index.md)
- [异常处理](./exception-handling.md)
- [using 语句](../../language-reference/keywords/using-statement.md)
- [try-catch](../../language-reference/keywords/try-catch.md)
- [try-finally](../../language-reference/keywords/try-finally.md)
- [try-catch-finally](../../language-reference/keywords/try-catch-finally.md)
