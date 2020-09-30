---
title: IOS 上的儲存格背景色彩
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺特定的，以設定 iOS 上儲存格的預設背景色彩。
ms.prod: xamarin
ms.assetid: 2A3FDACF-5AE2-40DE-8488-6FE41733712F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a9ab12b5aabee03d84c58580ec200de4b63d5106
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562427"
---
# <a name="cell-background-color-on-ios"></a>IOS 上的儲存格背景色彩

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平臺特定會設定實例的預設背景色彩 [`Cell`](xref:Xamarin.Forms.Cell) 。 它是在 XAML 中使用，方法是將可系結 `Cell.DefaultBackgroundColor` 屬性設定為 [`Color`](xref:Xamarin.Forms.Color) ：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ItemsSource="{Binding GroupedEmployees}"
                  IsGroupingEnabled="true">
            <ListView.GroupHeaderTemplate>
                <DataTemplate>
                    <ViewCell ios:Cell.DefaultBackgroundColor="Teal">
                        <Label Margin="10,10"
                               Text="{Binding Key}"
                               FontAttributes="Bold" />
                    </ViewCell>
                </DataTemplate>
            </ListView.GroupHeaderTemplate>
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var viewCell = new ViewCell { View = ... };
viewCell.On<iOS>().SetDefaultBackgroundColor(Color.Teal);
```

`ListView.On<iOS>`方法指定此平臺特定的只會在 iOS 上執行。 在 `Cell.SetDefaultBackgroundColor` 命名空間中，方法會 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 將資料格背景色彩設定為指定的 [`Color`](xref:Xamarin.Forms.Color) 。 此外， `Cell.DefaultBackgroundColor` 方法可以用來抓取目前的儲存格背景色彩。

結果是，中的背景色彩 [`Cell`](xref:Xamarin.Forms.Cell) 可以設定為特定的 [`Color`](xref:Xamarin.Forms.Color) ：

[![IOS 上的青色群組標頭儲存格的螢幕擷取畫面](cell-background-color-images/group-header-cell-color.png "具有青色群組標頭儲存格的 ListView")](cell-background-color-images/group-header-cell-color-large.png#lightbox "具有青色群組標頭儲存格的 ListView")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)