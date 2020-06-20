---
title: 中的裝置樣式Xamarin.Forms
description: Xamarin.Forms在 Device. Styles 類別中包含六個動態樣式（稱為裝置樣式）。 本文說明如何在應用程式中使用裝置樣式 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: 7FF19ED1-0822-4238-9435-AD970317A2F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b835847fea39e1c2f968e7b81fb9d22f68ea461c
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84140097"
---
# <a name="device-styles-in-xamarinforms"></a>中的裝置樣式Xamarin.Forms

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)

_在裝置中，Xamarin 包含六個動態樣式（稱為裝置樣式）。_

*裝置*樣式如下：

- [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle)
- [`CaptionStyle`](xref:Xamarin.Forms.Device.Styles.CaptionStyle)
- [`ListItemDetailTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyle)
- [`ListItemTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyle)
- [`SubtitleStyle`](xref:Xamarin.Forms.Device.Styles.SubtitleStyle)
- [`TitleStyle`](xref:Xamarin.Forms.Device.Styles.TitleStyle)

這六種樣式都只能套用至 [`Label`](xref:Xamarin.Forms.Label) 實例。 例如， `Label` 顯示段落主體的會將其 [`Style`](xref:Xamarin.Forms.NavigableElement.Style) 屬性設定為 [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle) 。

下列程式碼範例示範如何在 XAML 頁面中使用*裝置*樣式：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DeviceStylesPage" Title="Device" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="myBodyStyle" TargetType="Label"
              BaseResourceKey="BodyStyle">
                <Setter Property="TextColor" Value="Accent" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Label Text="Title style"
              Style="{DynamicResource TitleStyle}" />
            <Label Text="Subtitle text style"
              Style="{DynamicResource SubtitleStyle}" />
            <Label Text="Body style"
              Style="{DynamicResource BodyStyle}" />
            <Label Text="Caption style"
              Style="{DynamicResource CaptionStyle}" />
            <Label Text="List item detail text style"
              Style="{DynamicResource ListItemDetailTextStyle}" />
            <Label Text="List item text style"
              Style="{DynamicResource ListItemTextStyle}" />
            <Label Text="No style" />
            <Label Text="My body style"
              Style="{StaticResource myBodyStyle}" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

裝置樣式會使用標記延伸系結至 `DynamicResource` 。 您可以變更文字大小的**協助工具**設定，在 iOS 中看到樣式的動態性質。 在每個平臺上，*裝置*樣式的外觀會不同，如下列螢幕擷取畫面所示：

![](device-images/device-styles.png "Device Styles on Each Platform")

您也可以將屬性設定為裝置樣式的索引鍵名稱，衍生自*裝置*樣式 [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) 。 在上述程式碼範例中， `myBodyStyle` 繼承自 [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle) 並設定重音文字色彩。 如需動態樣式繼承的詳細資訊，請參閱[動態樣式繼承](~/xamarin-forms/user-interface/styles/xaml/dynamic.md#dynamic-style-inheritance)。

下列程式碼範例示範 c # 中的對等頁面：

```csharp
public class DeviceStylesPageCS : ContentPage
{
    public DeviceStylesPageCS ()
    {
        var myBodyStyle = new Style (typeof(Label)) {
            BaseResourceKey = Device.Styles.BodyStyleKey,
            Setters = {
                new Setter {
                    Property = Label.TextColorProperty,
                    Value = Color.Accent
                }
            }
        };

        Title = "Device";
        IconImageSource = "csharp.png";
        Padding = new Thickness (0, 20, 0, 0);

        Content = new StackLayout {
            Children = {
                new Label { Text = "Title style", Style = Device.Styles.TitleStyle },
                new Label { Text = "Subtitle style", Style = Device.Styles.SubtitleStyle },
                new Label { Text = "Body style", Style = Device.Styles.BodyStyle },
                new Label { Text = "Caption style", Style = Device.Styles.CaptionStyle },
                new Label { Text = "List item detail text style",
                  Style = Device.Styles.ListItemDetailTextStyle },
                new Label { Text = "List item text style", Style = Device.Styles.ListItemTextStyle },
                new Label { Text = "No style" },
                new Label { Text = "My body style", Style = myBodyStyle }
            }
        };
    }
}
```

[`Style`](xref:Xamarin.Forms.NavigableElement.Style)每個實例的屬性 [`Label`](xref:Xamarin.Forms.Label) 會從類別設定為適當的屬性 [`Devices.Styles`](xref:Xamarin.Forms.Device.Styles) 。

## <a name="accessibility"></a>Accessibility

*裝置*樣式會遵循協助工具喜好設定，因此當每個平臺上的協助工具喜好設定改變時，字型大小也會隨之變更。 因此，若要支援可存取的文字，請確定*裝置*樣式是用來作為應用程式內任何文字樣式的基礎。

下列螢幕擷取畫面會示範每個平臺上的裝置樣式，以及可存取的最小字型大小：

[![](device-images/minimum-size.png "Accessible Small Device Styles on Each Platform")](device-images/minimum-size-large.png#lightbox "Accessible Small Device Styles on Each Platform")

下列螢幕擷取畫面會示範每個平臺上具有最大可存取字型大小的裝置樣式：

![](device-images/maximum-size.png "Accessible Large Device Styles on Each Platform")

## <a name="related-links"></a>相關連結

- [文字樣式](~/xamarin-forms/user-interface/text/styles.md)
- [XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [動態樣式（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)
- [使用樣式（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [裝置。樣式](xref:Xamarin.Forms.Device.Styles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary) \(英文\)
- [Style](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
