---
title: 在 Windows 上 TabbedPage 圖示
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用 Windows 平臺特定的，讓頁面圖示顯示在 TabbedPage 工具列上。
ms.prod: xamarin
ms.assetid: 7C5031A5-74EE-4469-994E-BEA7BA9D33CB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3b63e66018ec96df49dfed2eb0ac6320a9585084
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93368325"
---
# <a name="tabbedpage-icons-on-windows"></a>在 Windows 上 TabbedPage 圖示

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

這通用 Windows 平臺平臺特定的可讓您在工具列上顯示頁面圖示 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) ，並提供選擇性地指定圖示大小的功能。 它是在 XAML 中使用，方法是將 [`TabbedPage.HeaderIconsEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.HeaderIconsEnabledProperty) 附加屬性設定為 `true` ，並選擇性地將 [`TabbedPage.HeaderIconsSize`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.HeaderIconsSizeProperty) 附加屬性設定為 [`Size`](xref:Xamarin.Forms.Size) 值：

```xaml
<TabbedPage ...
            xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
            windows:TabbedPage.HeaderIconsEnabled="true">
    <windows:TabbedPage.HeaderIconsSize>
        <Size>
            <x:Arguments>
                <x:Double>24</x:Double>
                <x:Double>24</x:Double>
            </x:Arguments>
        </Size>
    </windows:TabbedPage.HeaderIconsSize>
    <ContentPage Title="Todo" IconImageSource="todo.png">
        ...
    </ContentPage>
    <ContentPage Title="Reminders" IconImageSource="reminders.png">
        ...
    </ContentPage>
    <ContentPage Title="Contacts" IconImageSource="contacts.png">
        ...
    </ContentPage>
</TabbedPage>
```

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

public class WindowsTabbedPageIconsCS : Xamarin.Forms.TabbedPage
{
  public WindowsTabbedPageIconsCS()
  {
    On<Windows>().SetHeaderIconsEnabled(true);
    On<Windows>().SetHeaderIconsSize(new Size(24, 24));

    Children.Add(new ContentPage { Title = "Todo", IconImageSource = "todo.png" });
    Children.Add(new ContentPage { Title = "Reminders", IconImageSource = "reminders.png" });
    Children.Add(new ContentPage { Title = "Contacts", IconImageSource = "contacts.png" });
  }
}
```

`TabbedPage.On<Windows>`方法指定此平臺特定只會在通用 Windows 平臺上執行。 [ `TabbedPage.SetHeaderIconsEnabled` ] (x： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. TabbedPage. SetHeaderIconsEnabled (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。TabbedPage} 是命名空間中的}、system.string) # A3 方法， [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) 用來開啟或關閉標頭圖示。 [ `TabbedPage.SetHeaderIconsSize` ] (x： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. TabbedPage. SetHeaderIconsSize (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。TabbedPage}， Xamarin.Forms 。大小) # A3 方法（選擇性）指定具有值的標頭圖示大小 [`Size`](xref:Xamarin.Forms.Size) 。

此外， `TabbedPage` 命名空間中的類別 `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` 也有 [`EnableHeaderIcons`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.EnableHeaderIcons*) 啟用標頭圖示的方法、停用 [`DisableHeaderIcons`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.DisableHeaderIcons*) 標頭圖示的方法，以及傳回 [`IsHeaderIconsEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.IsHeaderIconsEnabled*) 值的方法，此方法 `boolean` 會指出是否已啟用標頭圖示。

結果是可以在工具列上顯示頁面圖示 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) ，並選擇性地將圖示大小設定為所需的大小：

![TabbedPage 圖示已啟用平臺特定](tabbedpage-icons-images/tabbedpage-icons.png "TabbedPage 圖示已啟用平臺特定")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)