---
title: ColorDialog 组件概述
ms.date: 03/30/2017
f1_keywords:
- ColorDialog
helpviewer_keywords:
- color dialog box [Windows Forms], about color dialog box
- ColorDialog component [Windows Forms], about ColorDialog
ms.assetid: 6dbdd8f0-f697-4728-bb09-7ea156f6d800
ms.openlocfilehash: 48d9d5072335908f85e65933dadafb1b69f28528
ms.sourcegitcommit: de17a7a0a37042f0d4406f5ae5393531caeb25ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76736965"
---
# <a name="colordialog-component-overview-windows-forms"></a>ColorDialog 组件概述（Windows 窗体）
Windows 窗体 <xref:System.Windows.Forms.ColorDialog> 组件是一个预配置的对话框，用户可以使用该对话框从调色板中选择颜色，并向该调色板添加自定义颜色。 此对话框与在其他基于 Windows 的应用程序中看到的用于选择颜色的对话框相同。 在基于 Windows 的应用程序中，使用它作为一种替代配置自己对话框的简单解决方案。  
  
 对话框中选择的颜色将在 "<xref:System.Windows.Forms.ColorDialog.Color%2A>" 属性中返回。 如果将 <xref:System.Windows.Forms.ColorDialog.AllowFullOpen%2A> 属性设置为 `false`，则将禁用 "定义自定义颜色" 按钮，并将用户限制为调色板中的预定义颜色。 如果 <xref:System.Windows.Forms.ColorDialog.SolidColorOnly%2A> 属性设置为 `true`，则用户无法选择抖动颜色。 若要显示该对话框，必须调用其 <xref:System.Windows.Forms.CommonDialog.ShowDialog%2A> 方法。  
  
## <a name="see-also"></a>另请参阅

- <xref:System.Windows.Forms.ColorDialog>
- [ColorDialog 组件](colordialog-component-windows-forms.md)
- [对话框控件和组件](dialog-box-controls-and-components-windows-forms.md)
- [如何：更改 Windows 窗体 ColorDialog 组件的外观](how-to-change-the-appearance-of-the-windows-forms-colordialog-component.md)
