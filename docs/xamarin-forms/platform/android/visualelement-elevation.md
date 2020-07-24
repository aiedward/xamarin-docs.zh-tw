---
title: Android 上的 VisualElement 提高許可權
description: 平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 Android 平臺特定的，控制以 API 21 或更新版本為目標的應用程式上的 V s 提升許可權。
ms.prod: xamarin
ms.assetid: 5BFD6175-2BBD-41CD-B8F9-521B4750B708
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- ':::no-loc(Xamarin.Forms):::'
- ':::no-loc(Xamarin.Essentials):::'
ms.openlocfilehash: a721f51d3f59bc166a48f5cc3a3eec9712ace837
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "86996678"
---
# <a name="visualelement-elevation-on-android"></a>Android 上的 VisualElement 提高許可權

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 Android 平臺特定是用來控制以 API 21 或更新版本為目標之應用程式上的視覺元素的提高許可權或迭置順序。 視覺專案的高度會決定其繪製順序，而具有較高 Z 值的視覺元素則會 occluding 具有較低 Z 值的視覺元素。 它會在 XAML 中使用，方法是將 `VisualElement.Elevation` 附加屬性設定為 `boolean` 值：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific;assembly=:::no-loc(Xamarin.Forms):::.Core"
             Title="Elevation">
    <StackLayout>
        <Grid>
            <Button Text="Button Beneath BoxView" />
            <BoxView Color="Red" Opacity="0.2" HeightRequest="50" />
        </Grid>        
        <Grid Margin="0,20,0,0">
            <Button Text="Button Above BoxView - Click Me" android:VisualElement.Elevation="10"/>
            <BoxView Color="Red" Opacity="0.2" HeightRequest="50" />
        </Grid>
    </StackLayout>
</ContentPage>
```

或者，您也可以使用 Fluent API，從 c # 取用它：

```csharp
using :::no-loc(Xamarin.Forms):::.PlatformConfiguration;
using :::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific;
...

public class AndroidElevationPageCS : ContentPage
{
    public AndroidElevationPageCS()
    {
        ...
        var aboveButton = new Button { Text = "Button Above BoxView - Click Me" };
        aboveButton.On<Android>().SetElevation(10);

        Content = new StackLayout
        {
            Children =
            {
                new Grid
                {
                    Children =
                    {
                        new Button { Text = "Button Beneath BoxView" },
                        new BoxView { Color = Color.Red, Opacity = 0.2, HeightRequest = 50 }
                    }
                },
                new Grid
                {
                    Margin = new Thickness(0,20,0,0),
                    Children =
                    {
                        aboveButton,
                        new BoxView { Color = Color.Red, Opacity = 0.2, HeightRequest = 50 }
                    }
                }
            }
        };
    }
}
```

`Button.On<Android>`方法會指定此平臺特定僅在 Android 上執行。 `VisualElement.SetElevation`命名空間中的方法 [`:::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific`](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific) 會用來將視覺專案的提升許可權設定為可為 null `float` 。 此外， `VisualElement.GetElevation` 方法可以用來抓取視覺專案的提高許可權值。

結果是可以控制視覺專案的高度，讓具有較高 Z 值的視覺專案遮蔽具有較低 Z 值的視覺元素。 因此，在此範例中，第二個 [`Button`](xref::::no-loc(Xamarin.Forms):::.Button) 會呈現在上方， [`BoxView`](xref::::no-loc(Xamarin.Forms):::.BoxView) 因為它具有較高的提高許可權值：

![VisualElement 提高許可權螢幕擷取畫面](visualelement-elevation-images/elevation.png)

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific.AppCompat)
