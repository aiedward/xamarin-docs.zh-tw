---
title: IOS 上的儲存格背景色彩
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 本文說明如何使用 iOS 平臺特定的來設定 iOS 上儲存格的預設背景色彩。
ms.prod: xamarin
ms.assetid: 2A3FDACF-5AE2-40DE-8488-6FE41733712F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 24276dce97e4935ba41d7012cf6a9aa8fa2658a8
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68651381"
---
# <a name="cell-background-color-on-ios"></a>IOS 上的儲存格背景色彩

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平臺特定會設定[`Cell`](xref:Xamarin.Forms.Cell)實例的預設背景色彩。 `Cell.DefaultBackgroundColor` [將`Color`](xref:Xamarin.Forms.Color)可系結屬性設定為, 即可在 XAML 中使用它:

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

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var viewCell = new ViewCell { View = ... };
viewCell.On<iOS>().SetDefaultBackgroundColor(Color.Teal);
```

`ListView.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 在`Cell.SetDefaultBackgroundColor` [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間中的方法會將儲存格背景色彩設為指定[`Color`](xref:Xamarin.Forms.Color)的。 此外, `Cell.DefaultBackgroundColor`方法可以用來抓取目前的儲存格背景色彩。

結果是中[`Cell`](xref:Xamarin.Forms.Cell)的背景色彩可以設定為特定[`Color`](xref:Xamarin.Forms.Color)的:

[IOS(cell-background-color-images/group-header-cell-color.png "ListView 上具有青色群組標頭")儲存格的![青色群組首儲存格的螢幕擷取畫面]](cell-background-color-images/group-header-cell-color-large.png#lightbox "具有藍綠色群組首儲存格的 ListView")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
