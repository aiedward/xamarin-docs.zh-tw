---
title: 在 iOS 上的資料格背景色彩
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 這篇文章說明如何使用 iOS 平台特定設定在 iOS 上的儲存格的預設背景色彩。
ms.prod: xamarin
ms.assetid: 2A3FDACF-5AE2-40DE-8488-6FE41733712F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 396e674bb3e5642f7c54455ee9e30ba5bf232f18
ms.sourcegitcommit: 00744f754527e5b55154365f89691caaf1c9d929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2019
ms.locfileid: "57557410"
---
# <a name="cell-background-color-on-ios"></a>在 iOS 上的資料格背景色彩

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

此 iOS 平台特定設定的預設背景色彩[ `Cell` ](xref:Xamarin.Forms.Cell)執行個體。 它由在 XAML 中設定`Cell.DefaultBackgroundColor`可繫結的屬性，以[ `Color` ](xref:Xamarin.Forms.Color):

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

`ListView.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 `Cell.SetDefaultBackgroundColor`方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間，設定為指定的資料格背景色彩[ `Color` ](xref:Xamarin.Forms.Color)。 颾魤 ㄛ`Cell.DefaultBackgroundColor`方法可用來擷取目前的資料格背景色彩。

結果是中的背景色彩[ `Cell` ](xref:Xamarin.Forms.Cell)可以設定為特定[ `Color` ](xref:Xamarin.Forms.Color):

[![青綠色群組首資料格，在 iOS 上的螢幕擷取畫面](cell-background-color-images/group-header-cell-color.png "藍綠色群組首資料格與 ListView")](cell-background-color-images/group-header-cell-color-large.png#lightbox "藍綠色群組首資料格與 ListView")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
