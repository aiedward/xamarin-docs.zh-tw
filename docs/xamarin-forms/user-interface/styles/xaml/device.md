---
title: 裝置樣式
description: Xamarin.Forms 包含六個動態樣式，稱為 Device.Styles 類別中的裝置樣式。
ms.prod: xamarin
ms.assetid: 7FF19ED1-0822-4238-9435-AD970317A2F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: cd2b6219a9a8e880bd48b31706a8abc8a977ca11
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/07/2018
ms.locfileid: "34848157"
---
# <a name="device-styles"></a>裝置樣式

_Xamarin.Forms 包含六個動態樣式，稱為 Device.Styles 類別中的裝置樣式。_

*裝置*樣式：

- [`BodyStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.BodyStyle/)
- [`CaptionStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.CaptionStyle/)
- [`ListItemDetailTextStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.ListItemDetailTextStyle/)
- [`ListItemTextStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.ListItemTextStyle/)
- [`SubtitleStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.SubtitleStyle/)
- [`TitleStyle`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.TitleStyle/)

所有六個樣式只套用至[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)執行個體。 例如，`Label`顯示段落的本文可能會設定其[ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/)屬性[ `BodyStyle` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.BodyStyle/)。

下列程式碼範例示範如何使用*裝置*XAML 頁面中的樣式：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DeviceStylesPage" Title="Device" Icon="xaml.png">
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

若要使用繫結裝置樣式`DynamicResource`標記延伸。 可以在 iOS 中看到樣式的動態本質，藉由變更**協助工具**設定文字大小。 外觀*裝置*樣式是不同的每個平台上，如下列螢幕擷取畫面所示：

![](device-images/device-styles.png "每個平台上的裝置樣式")

*裝置*樣式可以也衍生自藉由設定[ `BaseResourceKey` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BaseResourceKey/)裝置樣式的索引鍵名稱的屬性。 在上述程式碼範例`myBodyStyle`繼承自[ `BodyStyle` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device+Styles.BodyStyle/) ，並將有腔調的文字色彩。 如需動態樣式繼承的詳細資訊，請參閱[動態樣式繼承](~/xamarin-forms/user-interface/styles/xaml/dynamic.md#dynamic-style-inheritance)。

下列程式碼範例會示範 C# 中的對等頁：

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
        Icon = "csharp.png";
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

[ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/)每個屬性[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)執行個體設定為適當的屬性，從[ `Devices.Styles` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Device+Styles/)類別。

## <a name="accessibility"></a>協助工具選項

*裝置*樣式採用協助工具的喜好設定，讓字型的大小，會隨著每個平台上的協助工具喜好設定變更而變更。 因此，若要支援可存取的文字，請確認*裝置*樣式可用做為基礎的應用程式中的任何文字樣式。

下列螢幕擷取畫面示範每個平台上，具有最小的可存取字型的裝置樣式：

[![](device-images/minimum-size.png "每個平台上的可存取的小型裝置樣式")](device-images/minimum-size-large.png#lightbox "每個平台上的可存取的小型裝置樣式")

下列螢幕擷取畫面示範每個平台上，最大的可存取的字型大小的裝置樣式：

![](device-images/maximum-size.png "每個平台上的可存取的大型裝置樣式")

## <a name="summary"></a>總結

Xamarin.Forms 包含六個*動態*樣式，稱為*裝置*樣式，在[ `Devices.Styles` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Device+Styles/)類別。 所有六個樣式只套用至[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)執行個體。


## <a name="related-links"></a>相關連結

- [文字樣式](~/xamarin-forms/user-interface/text/styles.md)
- [XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [動態樣式 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/DynamicStyles/)
- [使用樣式 （範例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [Device.Styles](https://developer.xamarin.com/api/type/Xamarin.Forms.Device+Styles/)
- [ResourceDictionary](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)
- [樣式](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)
- [Setter](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)
