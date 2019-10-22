---
title: IOS 上的儲存格背景色彩
description: 平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺特定的來設定 iOS 上儲存格的預設背景色彩。
ms.prod: xamarin
ms.assetid: 2A3FDACF-5AE2-40DE-8488-6FE41733712F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 24276dce97e4935ba41d7012cf6a9aa8fa2658a8
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "68651381"
---
# <a name="cell-background-color-on-ios"></a>IOS 上的儲存格背景色彩

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平臺特定會設定[`Cell`](xref:Xamarin.Forms.Cell)實例的預設背景色彩。 它會在 XAML 中使用，方法是將 `Cell.DefaultBackgroundColor` 可系結屬性設定為[`Color`](xref:Xamarin.Forms.Color)：

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

或者，也可以C#使用 Fluent API 來取用：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var viewCell = new ViewCell { View = ... };
viewCell.On<iOS>().SetDefaultBackgroundColor(Color.Teal);
```

@No__t_0 方法指定此平臺特定的只會在 iOS 上執行。 [@No__t_2](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間中的 `Cell.SetDefaultBackgroundColor` 方法會將儲存格背景色彩設定為指定的[`Color`](xref:Xamarin.Forms.Color)。 此外，`Cell.DefaultBackgroundColor` 方法可以用來抓取目前的儲存格背景色彩。

結果是[`Cell`](xref:Xamarin.Forms.Cell)中的背景色彩可以設定為特定[`Color`](xref:Xamarin.Forms.Color)：

[![IOS 上的青色群組首儲存格的螢幕擷取畫面](cell-background-color-images/group-header-cell-color.png "具有藍綠色群組首儲存格的 ListView")](cell-background-color-images/group-header-cell-color-large.png#lightbox "具有藍綠色群組首儲存格的 ListView")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
