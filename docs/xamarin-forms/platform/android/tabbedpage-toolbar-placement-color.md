---
title: 在 Android 上 TabbedPage 工具列位置和色彩
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用 Android 平臺特定的，在 TabbedPage 上設定工具列的位置和色彩。
ms.prod: xamarin
ms.assetid: A5C68D6A-9A5F-42EE-845D-1E5B0CB1544E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8dc17ea4dba8257ef82c33aa87d6ffc9974658ac
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374182"
---
# <a name="tabbedpage-toolbar-placement-and-color-on-android"></a>在 Android 上 TabbedPage 工具列位置和色彩

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

> [!IMPORTANT]
> 在上設定工具列色彩的平臺細節 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 現在已過時，且已由 [`SelectedTabColor`](xref:Xamarin.Forms.TabbedPage.SelectedTabColor) 和屬性取代 [`UnselectedTabColor`](xref:Xamarin.Forms.TabbedPage.UnselectedTabColor) 。 如需詳細資訊，請參閱 [建立 TabbedPage](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md#create-a-tabbedpage)。

這些平臺細節可用來設定上工具列的位置和色彩 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 。 它們是在 XAML 中使用，方法是將 [`TabbedPage.ToolbarPlacement`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.ToolbarPlacementProperty) 附加屬性設定為列舉的值 [`ToolbarPlacement`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement) ，並將 [`TabbedPage.BarItemColor`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.BarItemColorProperty) 和 [`TabbedPage.BarSelectedItemColor`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.BarSelectedItemColorProperty) 附加屬性 [`Color`](xref:Xamarin.Forms.Color) 設定為：

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.ToolbarPlacement="Bottom"
            android:TabbedPage.BarItemColor="Black"
            android:TabbedPage.BarSelectedItemColor="Red">
    ...
</TabbedPage>
```

或者，您也可以使用流暢的 API 從 c # 取用它們：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetToolbarPlacement(ToolbarPlacement.Bottom)
             .SetBarItemColor(Color.Black)
             .SetBarSelectedItemColor(Color.Red);
```

`TabbedPage.On<Android>`方法會指定這些平臺專屬特性只會在 Android 上執行。 [ `TabbedPage.SetToolbarPlacement` ] (x： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. TabbedPage. SetToolbarPlacement (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Android、 Xamarin.Forms 。TabbedPage}， Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. ToolbarPlacement) # A3 方法（在 [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) 命名空間中）是用來設定上的工具列位置 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) ，並 [`ToolbarPlacement`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement) 提供下列值給列舉：

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Default) –表示工具列會放在頁面上的預設位置。 這是手機上頁面的頂端，以及其他裝置慣用語頁面的底部。
- [`Top`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Top) –表示工具列位於頁面頂端。
- [`Bottom`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Bottom) –表示工具列會放在頁面底部。

此外，[ `TabbedPage.SetBarItemColor` ] (x： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. TabbedPage. SetBarItemColor (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Android、 Xamarin.Forms 。TabbedPage}， Xamarin.Forms 。Color) # A3 和 [ `TabbedPage.SetBarSelectedItemColor` ] (x： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. TabbedPage. SetBarSelectedItemColor (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Android、 Xamarin.Forms 。TabbedPage}， Xamarin.Forms 。色彩) # A7 方法可用來分別設定工具列專案和選取的工具列專案的色彩。

> [!NOTE]
> [ `GetToolbarPlacement` ] (x： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. TabbedPage. GetToolbarPlacement (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Android、 Xamarin.Forms 。TabbedPage} ) # A3，[ `GetBarItemColor` ] (x： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. TabbedPage. GetBarItemColor (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Android、 Xamarin.Forms 。TabbedPage} ) # A7 和 [ `GetBarSelectedItemColor` ] (x： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. TabbedPage. GetBarSelectedItemColor (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Android、 Xamarin.Forms 。TabbedPage} ) # A11 方法可以用來取出工具列的位置和色彩 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 。

結果是工具列位置、工具列專案的色彩，以及所選取工具列專案的色彩可以在上設定 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) ：

![TabbedPage 工具列設定](tabbedpage-toolbar-placement-color-images/tabbedpage-toolbar-placement.png)

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)