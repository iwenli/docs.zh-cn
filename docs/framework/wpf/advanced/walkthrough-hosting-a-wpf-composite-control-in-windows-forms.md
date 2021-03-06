---
title: 在 Windows 窗体中托管 WPF 复合控件
titleSuffix: ''
ms.date: 03/30/2017
helpviewer_keywords:
- hosting WPF content in Windows Forms [WPF]
ms.assetid: 0ac41286-4c1b-4b17-9196-d985cb844ce1
ms.openlocfilehash: 88efab8adf36989938ba5aa887a28b41eb8820f3
ms.sourcegitcommit: e48a54ebe62e874500a7043f6ee0b77a744d55b4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2020
ms.locfileid: "80291626"
---
# <a name="walkthrough-hosting-a-wpf-composite-control-in-windows-forms"></a>演练：在 Windows 窗体中承载 WPF 复合控件
[!INCLUDE[TLA#tla_winclient](../../../../includes/tlasharptla-winclient-md.md)] 提供了用于创建应用程序的丰富环境。 但是，当您对 Windows 窗体代码进行大量投资时，使用扩展现有 Windows 窗体应用程序比从头开始[!INCLUDE[TLA2#tla_winclient](../../../../includes/tla2sharptla-winclient-md.md)]重写应用程序更有效。 常见方案是，您希望在 Windows 窗体应用程序中嵌入一个或多个实现[!INCLUDE[TLA2#tla_winclient](../../../../includes/tla2sharptla-winclient-md.md)]的控件。 有关自定义 WPF 控件的详细信息，请参阅[控件自定义](../controls/control-customization.md)。  
  
 本演练将引导您完成承载[!INCLUDE[TLA2#tla_winclient](../../../../includes/tla2sharptla-winclient-md.md)]复合控件以在 Windows 窗体应用程序中执行数据输入的应用程序。 复合控件打包在一个 DLL 中。 此常规步骤可扩展到更复杂的应用程序和控件。 本演练设计为外观和功能几乎相同，演练[：在 WPF 中托管 Windows 窗体复合控件](walkthrough-hosting-a-windows-forms-composite-control-in-wpf.md)。 主要区别在于承载方案是相反的。  
  
 本演练分为两个部分。 第一部分简要介绍了复合控件的[!INCLUDE[TLA2#tla_winclient](../../../../includes/tla2sharptla-winclient-md.md)]实现。 第二部分将详细讨论如何在 Windows 窗体应用程序中托管复合控件、从控件接收事件以及访问控件的某些属性。  
  
 本演练涉及以下任务：  
  
- 实现 WPF 复合控件。  
  
- 实现 Windows 窗体主机应用程序。  
  
 有关本演练中说明的任务的完整代码列表，请参阅在 Windows[窗体示例中托管 WPF 复合控件](https://github.com/microsoft/WPF-Samples/tree/master/Migration%20and%20Interoperability/WindowsFormsHostingWpfControl)。  
  
## <a name="prerequisites"></a>先决条件  

若要完成本演练，必须具有 Visual Studio。  
  
## <a name="implementing-the-wpf-composite-control"></a>实现 WPF 复合控件  
 此示例[!INCLUDE[TLA2#tla_winclient](../../../../includes/tla2sharptla-winclient-md.md)]中使用的复合控件是一个简单的数据输入窗体，它采用用户的名称和地址。 当用户单击两个按钮的其中一个以指示任务已完成时，该控件会引发将该信息返回给主机的自定义事件。 下图显示呈现的控件。

 下图显示了 WPF 复合控件：

 ![显示简单 WPF 控件的屏幕截图。](./media/walkthrough-hosting-a-wpf-composite-control-in-windows-forms/windows-presentation-foundation-composite-control.png)  
  
### <a name="creating-the-project"></a>创建项目  
 启动项目：  
  
1. 启动可视化工作室，然后打开 **"新项目**"对话框。  
  
2. 在 Visual C# 和 Windows 类别中，选择**WPF 用户控制库**模板。  
  
3. 将新项目命名为 `MyControls`。  
  
4. 对于位置，指定一个方便命名的顶级文件夹，如`WindowsFormsHostingWpfControl`。 随后，将主机应用程序放在此文件夹中。  
  
5. 单击“确定”**** 以创建项目。 默认项目包含名为 的`UserControl1`单个控件。  
  
6. 在解决方案资源管理器中，`UserControl1`重命名为`MyControl1`。  
  
 项目应具有对以下系统 DLL 的引用。 如果默认未包含其中任何 DLL，请将它们添加到项目中。  
  
- PresentationCore  
  
- PresentationFramework  
  
- 系统  
  
- WindowsBase  
  
### <a name="creating-the-user-interface"></a>创建用户界面  
 复合[!INCLUDE[TLA#tla_ui](../../../../includes/tlasharptla-ui-md.md)]控件的 。 [!INCLUDE[TLA#tla_xaml](../../../../includes/tlasharptla-xaml-md.md)] 复合控件[!INCLUDE[TLA2#tla_ui](../../../../includes/tla2sharptla-ui-md.md)]由五<xref:System.Windows.Controls.TextBox>个元素组成。 每个<xref:System.Windows.Controls.TextBox>元素都有一个<xref:System.Windows.Controls.TextBlock>用作标签的关联元素。 底部有两<xref:System.Windows.Controls.Button>个元素，**确定**和**取消**。 当用户单击任一按钮时，该控件会引发将信息返回给主机的自定义事件。  
  
#### <a name="basic-layout"></a>基本布局  
 元素中[!INCLUDE[TLA2#tla_ui](../../../../includes/tla2sharptla-ui-md.md)]包含各种<xref:System.Windows.Controls.Grid>元素。 可以使用 以<xref:System.Windows.Controls.Grid>在 HTML 中使用`Table`元素的方式排列复合控件的内容。 [!INCLUDE[TLA2#tla_winclient](../../../../includes/tla2sharptla-winclient-md.md)]也有一个<xref:System.Windows.Documents.Table>元素，但<xref:System.Windows.Controls.Grid>更轻量，更适合简单的布局任务。  
  
 以下 XAML 演示基本布局。 此 XAML 通过指定<xref:System.Windows.Controls.Grid>元素中的列和行数来定义控件的整体结构。  
  
 在 MyControl1.xaml 中，将现有 XAML 替换为以下 XAML。  
  
 [!code-xaml[WindowsFormsHostingWpfControl#101](~/samples/snippets/csharp/VS_Snippets_Wpf/WindowsFormsHostingWpfControl/CSharp/MyControls/Page1.xaml#101)]  
[!code-xaml[WindowsFormsHostingWpfControl#102](~/samples/snippets/csharp/VS_Snippets_Wpf/WindowsFormsHostingWpfControl/CSharp/MyControls/Page1.xaml#102)]  
  
#### <a name="adding-textblock-and-textbox-elements-to-the-grid"></a>向 Grid 添加 TextBlock 和 TextBox 元素  
 通过将[!INCLUDE[TLA2#tla_ui](../../../../includes/tla2sharptla-ui-md.md)]元素和<xref:System.Windows.Controls.Grid.RowProperty>属性设置为相应的行号和<xref:System.Windows.Controls.Grid.ColumnProperty>列号，在网格中放置元素。 请记住行号和列号是从零开始的。 可以通过设置元素<xref:System.Windows.Controls.Grid.ColumnSpanProperty>的属性来使元素跨越多个列。 有关<xref:System.Windows.Controls.Grid>元素的详细信息，请参阅[创建网格元素](../controls/how-to-create-a-grid-element.md)。  
  
 以下 XAML 显示了<xref:System.Windows.Controls.TextBox>复合控件及其<xref:System.Windows.Controls.TextBlock><xref:System.Windows.Controls.Grid.RowProperty>和<xref:System.Windows.Controls.Grid.ColumnProperty>属性的元素，这些属性设置为将元素正确放置在网格中。  
  
 在 MyControl1.xaml 中，在<xref:System.Windows.Controls.Grid>元素中添加以下 XAML。  
  
 [!code-xaml[WindowsFormsHostingWpfControl#103](~/samples/snippets/csharp/VS_Snippets_Wpf/WindowsFormsHostingWpfControl/CSharp/MyControls/Page1.xaml#103)]  
  
#### <a name="styling-the-ui-elements"></a>设置 UI 元素的样式  
 数据输入窗体上的许多元素外观相似，这意味着它们对于其若干属性具有相同的设置。 前面的 XAML 使用<xref:System.Windows.Style>元素为元素类定义标准属性设置，而不是单独设置每个元素的属性。 这种方法可以降低控件的复杂性，并使你能够通过单个样式特性更改多个元素的外观。  
  
 元素<xref:System.Windows.Style>包含在元素的属性<xref:System.Windows.Controls.Grid><xref:System.Windows.FrameworkElement.Resources%2A>中，因此它们可以由控件中的所有元素使用。 如果命名了样式，则可以通过将<xref:System.Windows.Style>元素集添加到样式的名称来将其应用于元素。 未命名的样式将成为该元素的默认样式。 有关[!INCLUDE[TLA2#tla_winclient](../../../../includes/tla2sharptla-winclient-md.md)]样式的详细信息，请参阅[样式和模板](../../../desktop-wpf/fundamentals/styles-templates-overview.md)化。  
  
 以下 XAML 显示了<xref:System.Windows.Style>复合控件的元素。 若要查看样式如何应用于元素，请参阅前一个 XAML。 例如，最后一<xref:System.Windows.Controls.TextBlock>`inlineText`个元素具有样式，最后一<xref:System.Windows.Controls.TextBox>个元素使用默认样式。  
  
 在 MyControl1.xaml 中，在<xref:System.Windows.Controls.Grid>开始元素之后添加以下 XAML。  
  
 [!code-xaml[WindowsFormsHostingWpfControl#104](~/samples/snippets/csharp/VS_Snippets_Wpf/WindowsFormsHostingWpfControl/CSharp/MyControls/Page1.xaml#104)]  
  
#### <a name="adding-the-ok-and-cancel-buttons"></a>添加“确定”和“取消”按钮  
 复合控件上的最后一个元素是**OK**和**Cancel**<xref:System.Windows.Controls.Button>元素，它们占据 的最后一行的前两列<xref:System.Windows.Controls.Grid>。 这些元素使用公共事件处理程序`ButtonClicked`和在以前的 XAML 中定义的默认<xref:System.Windows.Controls.Button>样式。  
  
 在 MyControl1.xaml 中，在最后一个<xref:System.Windows.Controls.TextBox>元素之后添加以下 XAML。 [!INCLUDE[TLA2#tla_xaml](../../../../includes/tla2sharptla-xaml-md.md)]复合控件的部分现已完成。  
  
 [!code-xaml[WindowsFormsHostingWpfControl#105](~/samples/snippets/csharp/VS_Snippets_Wpf/WindowsFormsHostingWpfControl/CSharp/MyControls/Page1.xaml#105)]  
  
### <a name="implementing-the-code-behind-file"></a>实现代码隐藏文件  
 代码落后文件MyControl1.xaml.cs实现三个基本任务：
  
1. 处理用户单击其中一个按钮时发生的事件。  
  
2. 从<xref:System.Windows.Controls.TextBox>元素检索数据，并将其打包到自定义事件参数对象中。  
  
3. 引发自定义`OnButtonClick`事件，该事件通知主机用户已完成并将数据传回主机。  
  
 该控件还公开多个可用来更改外观的颜色和字体属性。 与用于<xref:System.Windows.Forms.Integration.WindowsFormsHost>承载 Windows 窗体控件的类不同，<xref:System.Windows.Forms.Integration.ElementHost>类仅公开控件的属性。 <xref:System.Windows.Controls.Panel.Background%2A> 为了保持此代码示例与演练中讨论的示例（[在 WPF 中托管 Windows 窗体复合控件）](walkthrough-hosting-a-windows-forms-composite-control-in-wpf.md)之间的相似性，该控件将直接公开其余属性。  
  
#### <a name="the-basic-structure-of-the-code-behind-file"></a>代码隐藏文件的基本结构  
 代码背后的文件由单个命名空间组成，`MyControls`它将包含两个类`MyControl1`和`MyControlEventArgs`。  
  
```csharp  
namespace MyControls  
{  
  public partial class MyControl1 : Grid  
  {  
    //...  
  }  
  public class MyControlEventArgs : EventArgs  
  {  
    //...  
  }  
}  
```  
  
 第一个类`MyControl1`是包含实现 MyControl1.xaml 中[!INCLUDE[TLA2#tla_ui](../../../../includes/tla2sharptla-ui-md.md)]定义的功能的代码的部分类。 分析 MyControl1.xaml 时，[!INCLUDE[TLA2#tla_xaml](../../../../includes/tla2sharptla-xaml-md.md)]将 转换为同一部分类，并将两个部分类合并为编译控件。 出于此原因，代码隐藏文件中的类名必须与分配给 MyControl1.xaml 的类名相匹配，并且它必须继承自控件的根元素。 第二个类`MyControlEventArgs`是一个事件参数类，用于将数据发送回主机。  
  
 打开 MyControl1.xaml.cs。 更改现有类声明，使其具有以下名称并从 继承<xref:System.Windows.Controls.Grid>。  
  
 [!code-csharp[WindowsFormsHostingWpfControl#21](~/samples/snippets/csharp/VS_Snippets_Wpf/WindowsFormsHostingWpfControl/CSharp/MyControls/Page1.xaml.cs#21)]  
  
#### <a name="initializing-the-control"></a>初始化控件  
 下面的代码实现几个基本任务：  
  
- 声明私有事件`OnButtonClick`及其关联的委托`MyControlEventHandler`。  
  
- 创建几个存储用户数据的私有全局变量。 此数据通过相应的属性公开。  
  
- 实现控件<xref:System.Windows.FrameworkElement.Loaded>事件的处理程序`Init`， 此处理程序通过向全局变量分配 MyControl1.xaml 中定义的值来对它们进行初始化。 为此，它使用<xref:System.Windows.FrameworkElement.Name%2A>分配给的典型<xref:System.Windows.Controls.TextBlock>元素`nameLabel`， 访问该元素的属性设置。  
  
 删除现有构造函数并将以下代码添加到类`MyControl1`。  
  
 [!code-csharp[WindowsFormsHostingWpfControl#11](~/samples/snippets/csharp/VS_Snippets_Wpf/WindowsFormsHostingWpfControl/CSharp/MyControls/Page1.xaml.cs#11)]  
  
#### <a name="handling-the-buttons-click-events"></a>处理按钮的单击事件  
 用户指示通过单击 **"确定"** 按钮或 **"取消"** 按钮完成数据输入任务。 这两个按钮使用相同的<xref:System.Windows.Controls.Primitives.ButtonBase.Click>事件处理程序 。 `ButtonClicked` 这两个按钮都有一个`btnOK`名称`btnCancel`或 ，使处理程序能够通过检查`sender`参数的值来确定单击了哪个按钮。 该处理程序执行以下任务：  
  
- 创建`MyControlEventArgs`包含<xref:System.Windows.Controls.TextBox>元素数据的对象。  
  
- 如果用户单击"**取消"** 按钮，则将`MyControlEventArgs`对象的`IsOK`属性设置到`false`。  
  
- 引发`OnButtonClick`事件以向主机指示用户已完成，并传递收集的数据。  
  
 在`Init`方法之后将以下代码`MyControl1`添加到类中。  
  
 [!code-csharp[WindowsFormsHostingWpfControl#12](~/samples/snippets/csharp/VS_Snippets_Wpf/WindowsFormsHostingWpfControl/CSharp/MyControls/Page1.xaml.cs#12)]  
  
#### <a name="creating-properties"></a>创建属性  
 类的其余部分只是公开对应于前面所述的全局变量的属性。 当属性更改时，set 访问器会通过更改对应的元素属性并更新基础全局变量来修改控件的外观。  
  
 将以下代码添加到类`MyControl1`。  
  
 [!code-csharp[WindowsFormsHostingWpfControl#13](~/samples/snippets/csharp/VS_Snippets_Wpf/WindowsFormsHostingWpfControl/CSharp/MyControls/Page1.xaml.cs#13)]  
  
#### <a name="sending-the-data-back-to-the-host"></a>将数据发送回主机  
 文件中的最后一个组件是`MyControlEventArgs`类，用于将收集的数据发送回主机。  
  
 将以下代码添加到命名`MyControls`空间。 该实现非常简单明了，因而不再进一步讨论。  
  
 [!code-csharp[WindowsFormsHostingWpfControl#14](~/samples/snippets/csharp/VS_Snippets_Wpf/WindowsFormsHostingWpfControl/CSharp/MyControls/Page1.xaml.cs#14)]  
  
 生成解决方案。 生成将产生一个名为 MyControls.dll 的 DLL。  
  
<a name="winforms_host_section"></a>
## <a name="implementing-the-windows-forms-host-application"></a>实现 Windows 窗体主机应用程序  
 Windows 窗体主机应用程序使用对象<xref:System.Windows.Forms.Integration.ElementHost>承载[!INCLUDE[TLA2#tla_winclient](../../../../includes/tla2sharptla-winclient-md.md)]复合控件。 应用程序处理事件`OnButtonClick`以从复合控件接收数据。 该应用程序还有一组选项按钮，可用于修改控件的外观。 下图显示应用程序。  

下图显示了托管在 Windows 窗体应用程序中的 WPF 复合控件"  

 ![显示 Windows 窗体托管 Avalon 控件的屏幕截图。](./media/walkthrough-hosting-a-wpf-composite-control-in-windows-forms/windows-form-hosting-avalon-control.png)  
  
### <a name="creating-the-project"></a>创建项目  
 启动项目：  
  
1. 启动可视化工作室，然后打开 **"新项目**"对话框。  
  
2. 在 Visual C# 和 Windows 类别中，选择**Windows 窗体应用程序**模板。  
  
3. 将新项目命名为 `WFHost`。  
  
4. 对于位置，指定包含 MyControls 项目的同一顶层文件夹。  
  
5. 单击“确定”**** 以创建项目。  
  
 您还需要添加对包含和其他程序集的 DLL 的`MyControl1`引用。  
  
1. 右键单击解决方案资源管理器中的项目名称，然后选择 **"添加参考**"。  
  
2. 单击"**浏览"** 选项卡，然后浏览到包含 MyControls.dll 的文件夹。 在本演练中，此文件夹位于 MyControls\bin\Debug。  
  
3. 选择"我的控件.dll"，然后单击"**确定**"。  
  
4. 添加对下列程序集的引用。  
  
    - PresentationCore  
  
    - PresentationFramework  
  
    - System.Xaml  
  
    - WindowsBase  
  
    - WindowsFormsIntegration  
  
### <a name="implementing-the-user-interface-for-the-application"></a>实现应用程序的用户界面  
 Windows 窗体应用程序的 UI 包含若干个与 WPF 复合控件进行交互的控件。  
  
1. 在 Windows 窗体设计器中打开 Form1。  
  
2. 放大窗体以适应控件。  
  
3. 在窗体的右上角，添加控件<xref:System.Windows.Forms.Panel?displayProperty=nameWithType>以保存[!INCLUDE[TLA2#tla_winclient](../../../../includes/tla2sharptla-winclient-md.md)]复合控件。  
  
4. 将以下<xref:System.Windows.Forms.GroupBox?displayProperty=nameWithType>控件添加到窗体中。  
  
    |“属性”|文本|  
    |----------|----------|  
    |groupBox1|背景色|  
    |groupBox2|前景色|  
    |groupBox3|字号|  
    |groupBox4|字体系列|  
    |groupBox5|字形|  
    |groupBox6|字体粗细|  
    |groupBox7|来自控件的数据|  
  
5. 将以下<xref:System.Windows.Forms.RadioButton?displayProperty=nameWithType>控件添加到<xref:System.Windows.Forms.GroupBox?displayProperty=nameWithType>控件。  
  
    |GroupBox|“属性”|文本|  
    |--------------|----------|----------|  
    |groupBox1|radioBackgroundOriginal|原始|  
    |groupBox1|radioBackgroundLightGreen|LightGreen|  
    |groupBox1|radioBackgroundLightSalmon|LightSalmon|  
    |groupBox2|radioForegroundOriginal|原始|  
    |groupBox2|radioForegroundRed|Red|  
    |groupBox2|radioForegroundYellow|Yellow|  
    |groupBox3|radioSizeOriginal|原始|  
    |groupBox3|radioSizeTen|10|  
    |groupBox3|radioSizeTwelve|12|  
    |groupBox4|radioFamilyOriginal|原始|  
    |groupBox4|radioFamilyTimes|宋体, Times New Roman|  
    |groupBox4|radioFamilyWingDings|WingDings|  
    |groupBox5|radioStyleOriginal|一般|  
    |groupBox5|radioStyleItalic|斜体|  
    |groupBox6|radioWeightOriginal|原始|  
    |groupBox6|radioWeightBold|加粗|  
  
6. 将以下<xref:System.Windows.Forms.Label?displayProperty=nameWithType>控件添加到最后一个<xref:System.Windows.Forms.GroupBox?displayProperty=nameWithType>。 这些控件显示[!INCLUDE[TLA2#tla_winclient](../../../../includes/tla2sharptla-winclient-md.md)]复合控件返回的数据。  
  
    |GroupBox|“属性”|文本|  
    |--------------|----------|----------|  
    |groupBox7|lblName|姓名：|  
    |groupBox7|lblAddress|街道地址:|  
    |groupBox7|lblCity|市/县:|  
    |groupBox7|lblState|状态：|  
    |groupBox7|lblZip|邮政编码:|  
  
### <a name="initializing-the-form"></a>初始化窗体  
 通常，在窗体<xref:System.Windows.Forms.Form.Load>的事件处理程序中实现托管代码。 以下代码显示<xref:System.Windows.Forms.Form.Load>事件处理程序、[!INCLUDE[TLA2#tla_winclient](../../../../includes/tla2sharptla-winclient-md.md)]复合控件<xref:System.Windows.FrameworkElement.Loaded>事件的处理程序以及以后使用的多个全局变量的声明。  
  
 在 Windows 窗体设计器中，双击窗体以创建<xref:System.Windows.Forms.Form.Load>事件处理程序。 在Form1.cs的顶部，添加以下`using`语句。  
  
 [!code-csharp[WindowsFormsHostingWpfControl#10](~/samples/snippets/csharp/VS_Snippets_Wpf/WindowsFormsHostingWpfControl/CSharp/WFHost/Form1.cs#10)]  
  
 将现有`Form1`类的内容替换为以下代码。  
  
 [!code-csharp[WindowsFormsHostingWpfControl#2](~/samples/snippets/csharp/VS_Snippets_Wpf/WindowsFormsHostingWpfControl/CSharp/WFHost/Form1.cs#2)]  
  
 前面的`Form1_Load`代码中的方法显示了托管控件的一[!INCLUDE[TLA2#tla_winclient](../../../../includes/tla2sharptla-winclient-md.md)]般过程：  
  
1. 创建新 <xref:System.Windows.Forms.Integration.ElementHost> 对象。  
  
2. 将控件的属性<xref:System.Windows.Forms.Control.Dock%2A>设置为<xref:System.Windows.Forms.DockStyle.Fill?displayProperty=nameWithType>。  
  
3. 将<xref:System.Windows.Forms.Integration.ElementHost>控件添加到<xref:System.Windows.Forms.Panel>控件的集合。 <xref:System.Windows.Forms.Control.Controls%2A>  
  
4. 创建控件的[!INCLUDE[TLA2#tla_winclient](../../../../includes/tla2sharptla-winclient-md.md)]实例。  
  
5. 通过将控件分配给<xref:System.Windows.Forms.Integration.ElementHost>控件的属性<xref:System.Windows.Forms.Integration.ElementHost.Child%2A>，在窗体上承载复合控件。  
  
 `Form1_Load`方法中的其余两行将处理程序附加到两个控制事件：  
  
- `OnButtonClick`是当用户单击 **"确定"** 或 **"取消"** 按钮时由复合控件触发的自定义事件。 处理该事件可获取用户的响应并收集用户指定的任何数据。  
  
- <xref:System.Windows.FrameworkElement.Loaded>是[!INCLUDE[TLA2#tla_winclient](../../../../includes/tla2sharptla-winclient-md.md)]控件在完全加载时引发的标准事件。 此处使用该事件是因为本示例需要使用控件中的属性初始化几个全局变量。 在窗体<xref:System.Windows.Forms.Form.Load>事件时，控件未完全加载，并且这些值仍设置为`null`。 您需要等待控件的事件<xref:System.Windows.FrameworkElement.Loaded>发生，然后才能访问这些属性。  
  
 事件<xref:System.Windows.FrameworkElement.Loaded>处理程序显示在前面的代码中。 下`OnButtonClick`一节将讨论处理程序。  
  
### <a name="handling-onbuttonclick"></a>处理 OnButtonClick  
 当用户`OnButtonClick`单击 **"确定"** 或 **"取消"** 按钮时，将发生该事件。  
  
 事件处理程序检查事件参数的`IsOK`字段以确定单击了哪个按钮。 `lbl`*数据*变量与前面讨论的<xref:System.Windows.Forms.Label>控件相对应。 如果用户单击 **"确定"** 按钮，则控件<xref:System.Windows.Controls.TextBox>控件中的数据将分配给相应的<xref:System.Windows.Forms.Label>控件。 如果用户单击 **"取消"，** 则<xref:System.Windows.Forms.Label.Text%2A>值将设置为默认字符串。  
  
 将以下按钮单击事件处理程序代码添加到`Form1`类。  
  
 [!code-csharp[WindowsFormsHostingWpfControl#3](~/samples/snippets/csharp/VS_Snippets_Wpf/WindowsFormsHostingWpfControl/CSharp/WFHost/Form1.cs#3)]  
  
 生成并运行应用程序。 在 WPF 复合控件中添加一些文本，然后单击 **"确定**"。 文本将显示在标签中。 此时，尚未添加代码来处理单选按钮。  
  
### <a name="modifying-the-appearance-of-the-control"></a>修改控件的外观  
 窗体<xref:System.Windows.Forms.RadioButton>上的控件将使用户能够更改[!INCLUDE[TLA2#tla_winclient](../../../../includes/tla2sharptla-winclient-md.md)]复合控件的前景和背景颜色以及多个字体属性。 背景颜色由<xref:System.Windows.Forms.Integration.ElementHost>对象公开。 其余属性作为控件的自定义属性公开。  
  
 双击窗体上<xref:System.Windows.Forms.RadioButton>的每个控件以创建<xref:System.Windows.Forms.RadioButton.CheckedChanged>事件处理程序。 将<xref:System.Windows.Forms.RadioButton.CheckedChanged>事件处理程序替换为以下代码。  
  
 [!code-csharp[WindowsFormsHostingWpfControl#4](~/samples/snippets/csharp/VS_Snippets_Wpf/WindowsFormsHostingWpfControl/CSharp/WFHost/Form1.cs#4)]  
  
 生成并运行应用程序。 单击不同的单选按钮来查看在 WPF 复合控件上的效果。  
  
## <a name="see-also"></a>请参阅

- <xref:System.Windows.Forms.Integration.ElementHost>
- <xref:System.Windows.Forms.Integration.WindowsFormsHost>
- [在 Visual Studio 中设计 XAML](/visualstudio/xaml-tools/designing-xaml-in-visual-studio)
- [演练：在 WPF 中承载 Windows 窗体复合控件](walkthrough-hosting-a-windows-forms-composite-control-in-wpf.md)
- [演练：在 Windows 窗体中托管 3D WPF 复合控件](walkthrough-hosting-a-3-d-wpf-composite-control-in-windows-forms.md)
