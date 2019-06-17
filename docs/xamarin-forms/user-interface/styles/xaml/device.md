---
title: 在 Xamarin.Forms 中的裝置樣式
description: Xamarin.Forms 包含六個動態的樣式，稱為裝置樣式，Device.Styles 類別中。 這篇文章說明如何使用 Xamarin.Forms 應用程式中的裝置樣式。
ms.prod: xamarin
ms.assetid: 7FF19ED1-0822-4238-9435-AD970317A2F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: 05e8fb9ef26f2a2a0b812b25853e4f61e658fa46
ms.sourcegitcommit: 482aef652bdaa440561252b6a1a1c0a40583cd32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2019
ms.locfileid: "65970558"
---
# <a name="device-styles-in-xamarinforms"></a>在 Xamarin.Forms 中的裝置樣式

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/DynamicStyles/)

_Xamarin.Forms 包含六個動態的樣式，稱為裝置樣式，Device.Styles 類別中。_

*裝置*樣式：

- [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle)
- [`CaptionStyle`](xref:Xamarin.Forms.Device.Styles.CaptionStyle)
- [`ListItemDetailTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyle)
- [`ListItemTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyle)
- [`SubtitleStyle`](xref:Xamarin.Forms.Device.Styles.SubtitleStyle)
- [`TitleStyle`](xref:Xamarin.Forms.Device.Styles.TitleStyle)

所有六個樣式只套用至[ `Label` ](xref:Xamarin.Forms.Label)執行個體。 例如， `Label` ，顯示一個段落的本文可能會設定其[ `Style` ](xref:Xamarin.Forms.NavigableElement.Style)屬性設[ `BodyStyle` ](xref:Xamarin.Forms.Device.Styles.BodyStyle)。

下列程式碼範例示範如何使用*裝置*XAML 頁面中的樣式：

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

裝置樣式會繫結至使用`DynamicResource`標記延伸。 可以藉由變更在 iOS 中看到樣式的動態本質**協助工具**設定文字大小。 外觀*裝置*樣式是每個平台，而不同，如下列螢幕擷取畫面所示：

![](device-images/device-styles.png "每個平台上的裝置樣式")

*裝置*樣式可以也衍生自 splittunneling [ `BaseResourceKey` ](xref:Xamarin.Forms.Style.BaseResourceKey)裝置樣式的索引鍵名稱的屬性。 在上述程式碼範例`myBodyStyle`繼承自[ `BodyStyle` ](xref:Xamarin.Forms.Device.Styles.BodyStyle)並設定重音符號的文字色彩。 如需有關動態樣式繼承的詳細資訊，請參閱[動態樣式繼承](~/xamarin-forms/user-interface/styles/xaml/dynamic.md#dynamic-style-inheritance)。

下列程式碼範例示範如何在 C# 中的對等頁面：

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

[ `Style` ](xref:Xamarin.Forms.NavigableElement.Style)屬性的每個[ `Label` ](xref:Xamarin.Forms.Label)執行個體設定為適當的屬性，從[ `Devices.Styles` ](xref:Xamarin.Forms.Device.Styles)類別。

## <a name="accessibility"></a>協助工具選項

*裝置*樣式會採用協助工具偏好設定，因此當每個平台上變更協助工具偏好設定，將會變更字型大小。 因此，若要支援可存取的文字，請確認*裝置*樣式來做為基礎的應用程式內的任何文字樣式。

下列螢幕擷取畫面示範裝置上的樣式，請在每個平台，具有最小的可存取字型：

[![](device-images/minimum-size.png "每個平台上的可存取的小型裝置樣式")](device-images/minimum-size-large.png#lightbox "每個平台上的可存取的小型裝置樣式")

下列螢幕擷取畫面示範裝置上的樣式，請在每個平台，具有最大可存取的字型大小：

![](device-images/maximum-size.png "每個平台上的可存取的大型裝置樣式")

## <a name="related-links"></a>相關連結

- [文字樣式](~/xamarin-forms/user-interface/text/styles.md)
- [XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [動態樣式 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/DynamicStyles/)
- [使用樣式 （範例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [Device.Styles](xref:Xamarin.Forms.Device.Styles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [樣式](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
