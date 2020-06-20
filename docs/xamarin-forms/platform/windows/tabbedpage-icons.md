---
title: Windows 上的 TabbedPage 圖示
description: 平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 Windows 平臺特定的，讓頁面圖示顯示在 TabbedPage 工具列上。
ms.prod: xamarin
ms.assetid: 7C5031A5-74EE-4469-994E-BEA7BA9D33CB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f6db5014050ad3f037869120d017e51803a7c48f
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136535"
---
# <a name="tabbedpage-icons-on-windows"></a>Windows 上的 TabbedPage 圖示

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此通用 Windows 平臺平臺特定的可讓網頁圖示顯示在工具列上 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) ，並提供選擇性地指定圖示大小的功能。 將 [`TabbedPage.HeaderIconsEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.HeaderIconsEnabledProperty) 附加屬性設為 `true` ，並選擇性地將 [`TabbedPage.HeaderIconsSize`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.HeaderIconsSizeProperty) 附加屬性設定為值，即可在 XAML 中使用它 [`Size`](xref:Xamarin.Forms.Size) ：

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

或者，您也可以使用 Fluent API，從 c # 取用它：

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

`TabbedPage.On<Windows>`方法會指定此平臺特定只會在通用 Windows 平臺上執行。 [ `TabbedPage.SetHeaderIconsEnabled` ] （X： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. TabbedPage. SetHeaderIconsEnabled （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration。 Windows， Xamarin.Forms 。TabbedPage}，system.string）方法，在 [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) 命名空間中，是用來開啟或關閉標頭圖示。 [ `TabbedPage.SetHeaderIconsSize` ] （X： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. TabbedPage. SetHeaderIconsSize （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration。 Windows， Xamarin.Forms 。TabbedPage}、 Xamarin.Forms 。大小））方法（選擇性）使用值指定標頭圖示大小 [`Size`](xref:Xamarin.Forms.Size) 。

此外， `TabbedPage` 命名空間中的類別 `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` 也有一 [`EnableHeaderIcons`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.EnableHeaderIcons*) 種方法，可啟用標頭圖示、 [`DisableHeaderIcons`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.DisableHeaderIcons*) 停用標頭圖示的方法，以及傳回 [`IsHeaderIconsEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.IsHeaderIconsEnabled*) 值的方法， `boolean` 指出是否已啟用標頭圖示。

結果就是頁面圖示可以顯示在 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 工具列上，並選擇性地將圖示大小設定為所需的大小：

![已啟用 TabbedPage 圖示的平臺特定](tabbedpage-icons-images/tabbedpage-icons.png "已啟用 TabbedPage 圖示的平臺特定")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
