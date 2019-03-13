---
title: TabbedPage 工具列位置和 Android 上的色彩
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 這篇文章說明如何使用 Android 平台特定設定的位置和色彩的工具列上 TabbedPage。
ms.prod: xamarin
ms.assetid: A5C68D6A-9A5F-42EE-845D-1E5B0CB1544E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: c68b190e71f83504e20731e9c66571711ced22bc
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209163"
---
# <a name="tabbedpage-toolbar-placement-and-color-on-android"></a>TabbedPage 工具列位置和 Android 上的色彩

[![下載範例](~/media/shared/download.png) 下載範例](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

若要設定的位置和色彩的工具列上使用這些平台特性[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)。 它們都是在 XAML 中藉由設定[ `TabbedPage.ToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.ToolbarPlacementProperty)附加屬性的值[ `ToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement)列舉型別，而[ `TabbedPage.BarItemColor` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.BarItemColorProperty)和[ `TabbedPage.BarSelectedItemColor` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.BarSelectedItemColorProperty)附加至屬性[ `Color` ](xref:Xamarin.Forms.Color):

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.ToolbarPlacement="Bottom"
            android:TabbedPage.BarItemColor="Black"
            android:TabbedPage.BarSelectedItemColor="Red">
    ...
</TabbedPage>
```

或者，他們可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetToolbarPlacement(ToolbarPlacement.Bottom)
             .SetBarItemColor(Color.Black)
             .SetBarSelectedItemColor(Color.Red);
```

`TabbedPage.On<Android>`方法可讓您指定只會在 Android 上執行這些平台特性。 [ `TabbedPage.SetToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetToolbarPlacement(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement))方法，請在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空間，用來設定工具列位置[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)，與[`ToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement)列舉提供下列值：

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Default) – 表示工具列會放在頁面上的預設位置。 這是頁面的在電話上，頁面頂端和底部上其他裝置的習慣用語。
- [`Top`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Top) – 表示工具列位於頁面頂端。
- [`Bottom`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Bottom) – 表示工具列位於頁面底部。

颾魤 ㄛ [ `TabbedPage.SetBarItemColor` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetBarItemColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage},Xamarin.Forms.Color))並[ `TabbedPage.SetBarSelectedItemColor` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetBarSelectedItemColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage},Xamarin.Forms.Color))方法來分別設定工具列項目及選取的工具列項目的色彩。

> [!NOTE]
> [ `GetToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.GetToolbarPlacement(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage}))， [ `GetBarItemColor` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.GetBarItemColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage}))，以及[ `GetBarSelectedItemColor` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.GetBarSelectedItemColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage}))方法可以用來擷取的位置和色彩[ `TabbedPage`](xref:Xamarin.Forms.TabbedPage)工具列。

結果是可以設定工具列位置、 工具列項目的色彩和色彩選取的工具列項目的上[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage):

![](tabbedpage-toolbar-placement-color-images/tabbedpage-toolbar-placement.png)

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
