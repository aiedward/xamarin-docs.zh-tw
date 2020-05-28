---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f5a2be4bd61056a42593fc45e1abdd3679795bc0
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139980"
---
# <a name="tabbedpage-toolbar-placement-and-color-on-android"></a>Android 上的 TabbedPage 工具列位置和色彩

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

> [!IMPORTANT]
> 在上設定工具列色彩的平臺細節， [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 現在已過時，並已由 [`SelectedTabColor`](xref:Xamarin.Forms.TabbedPage.SelectedTabColor) 和屬性所取代 [`UnselectedTabColor`](xref:Xamarin.Forms.TabbedPage.UnselectedTabColor) 。 如需詳細資訊，請參閱[Create a TabbedPage](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md#create-a-tabbedpage)。

這些平臺細節是用來設定上工具列的位置和色彩 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 。 它們會在 XAML 中使用，方法是將 [`TabbedPage.ToolbarPlacement`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.ToolbarPlacementProperty) 附加屬性設為列舉的值 [`ToolbarPlacement`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement) ，並將 [`TabbedPage.BarItemColor`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.BarItemColorProperty) 和 [`TabbedPage.BarSelectedItemColor`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.BarSelectedItemColorProperty) 附加屬性設定為 [`Color`](xref:Xamarin.Forms.Color) ：

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.ToolbarPlacement="Bottom"
            android:TabbedPage.BarItemColor="Black"
            android:TabbedPage.BarSelectedItemColor="Red">
    ...
</TabbedPage>
```

或者，您也可以使用 Fluent API，從 c # 取用它們：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetToolbarPlacement(ToolbarPlacement.Bottom)
             .SetBarItemColor(Color.Black)
             .SetBarSelectedItemColor(Color.Red);
```

`TabbedPage.On<Android>`方法會指定這些平臺細節只會在 Android 上執行。 [ `TabbedPage.SetToolbarPlacement` ] （X： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. TabbedPage. SetToolbarPlacement （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. Android、 Xamarin.Forms 。TabbedPage}、 Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. ToolbarPlacement））方法（在 [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) 命名空間中）是用來設定上的工具列位置 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) ，而 [`ToolbarPlacement`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement) 列舉提供下列值：

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Default)–表示工具列會放在頁面上的預設位置。 這是電話上的頁面頂端，以及其他裝置慣用語上頁面的底部。
- [`Top`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Top)–表示工具列會放在頁面頂端。
- [`Bottom`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Bottom)–表示工具列會放在頁面底部。

此外，[ `TabbedPage.SetBarItemColor` ] （x： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. TabbedPage. SetBarItemColor （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. Android、 Xamarin.Forms 。TabbedPage}、 Xamarin.Forms 。Color））和 [ `TabbedPage.SetBarSelectedItemColor` ] （x： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. TabbedPage. SetBarSelectedItemColor （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. Android、 Xamarin.Forms 。TabbedPage}、 Xamarin.Forms 。Color））方法，分別用來設定工具列專案和所選工具列專案的色彩。

> [!NOTE]
> [ `GetToolbarPlacement` ] （X： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. TabbedPage. GetToolbarPlacement （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. Android、 Xamarin.Forms 。TabbedPage}）），[ `GetBarItemColor` ] （x： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. TabbedPage. GetBarItemColor （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. Android、 Xamarin.Forms 。TabbedPage}））和 [ `GetBarSelectedItemColor` ] （x： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. TabbedPage. GetBarSelectedItemColor （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. Android、 Xamarin.Forms 。TabbedPage}））方法可以用來抓取工具列的位置和色彩 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 。

結果就是工具列位置、工具列專案的色彩，以及所選工具列專案的色彩可以在上設定 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) ：

![](tabbedpage-toolbar-placement-color-images/tabbedpage-toolbar-placement.png)

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
