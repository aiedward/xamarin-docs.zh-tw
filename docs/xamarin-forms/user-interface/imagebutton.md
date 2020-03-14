---
title: Xamarin. Forms ImageButton
description: ImageButton 會顯示影像，並回應點按或按一下，以指示應用程式執行特定工作。
ms.prod: xamarin
ms.assetid: B5906AB6-3F79-4FCB-8C78-1F0AF18AB39E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/04/2019
ms.openlocfilehash: 7c6647a0299b5ece3caaaa1d322ec1a0efac3557
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304132"
---
# <a name="xamarinforms-imagebutton"></a>Xamarin. Forms ImageButton

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_ImageButton 會顯示影像，並回應點按或按一下，以指示應用程式執行特定工作。_

[`ImageButton`] 視圖會結合 [ [`Button`](xref:Xamarin.Forms.Button) view] 和 [ [`Image`](xref:Xamarin.Forms.Image) ] 視圖，以建立其內容為影像的按鈕。 使用者以手指按下 `ImageButton`，或使用滑鼠按一下它，以指示應用程式執行特定工作。 不過，不同于 `Button` 視圖，[`ImageButton`] 視圖沒有文字和文字外觀的概念。

> [!NOTE]
> 雖然 [ [`Button`](xref:Xamarin.Forms.Button) ] 視圖會定義[`Image`](xref:Xamarin.Forms.Button.Image)屬性，讓您可以在 `Button`上顯示影像，但此屬性是在顯示 [`Button`] 文字旁邊的小圖示時使用。

本指南中的程式碼範例取自[FormsGallery 範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)。

## <a name="setting-the-image-source"></a>設定映射來源

`ImageButton` 定義應設定為要在按鈕中顯示之影像的 `Source` 屬性，其中影像來源為檔案、URI、資源或資料流程。 如需從不同來源載入影像的詳細資訊，請參閱[Xamarin 中的影像](images.md)。

下列範例顯示如何在 XAML 中具現化 `ImageButton`：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="FormsGallery.XamlExamples.ImageButtonDemoPage"
             Title="ImageButton Demo">
    <StackLayout>
        <Label Text="ImageButton"
               FontSize="50"
               FontAttributes="Bold"
               HorizontalOptions="Center" />

       <ImageButton Source="XamarinLogo.png"
                    HorizontalOptions="Center"
                    VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

`Source` 屬性會指定出現在 `ImageButton`中的影像。 在此範例中，它會設定為將從每個平臺專案載入的本機檔案，並產生下列螢幕擷取畫面：

[![基本 ImageButton](imagebutton-images/BasicImageButton.png "基本 ImageButton")](imagebutton-images/BasicImageButton-Large.png#lightbox "基本 ImageButton")

根據預設，`ImageButton` 是矩形，但是您可以使用 `CornerRadius` 屬性為其指定圓角。 如需 `ImageButton` 外觀的詳細資訊，請參閱[ImageButton 外觀](#imagebutton-appearance)。

> [!NOTE]
> 雖然 `ImageButton` 可以載入動畫 GIF，但它只會顯示 GIF 的第一個框架。

下列範例示範如何建立功能相當於先前 XAML 範例的頁面，但完全在中C#：

```csharp
public class ImageButtonDemoPage : ContentPage
{
    public ImageButtonDemoPage()
    {
        Label header = new Label
        {
            Text = "ImageButton",
            FontSize = 50,
            FontAttributes = FontAttributes.Bold,
            HorizontalOptions = LayoutOptions.Center
        };

        ImageButton imageButton = new ImageButton
        {
            Source = "XamarinLogo.png",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };

        // Build the page.
        Title = "ImageButton Demo";
        Content = new StackLayout
        {
            Children = { header, imageButton }
        };
    }
}
```

## <a name="handling-imagebutton-clicks"></a>處理 ImageButton 按一下

`ImageButton` 定義當使用者利用手指或滑鼠指標來點按 `ImageButton` 時，所引發的 `Clicked` 事件。 從 `ImageButton`介面放開手指或滑鼠按鍵時，就會引發事件。 `ImageButton` 必須將其 `IsEnabled` 屬性設定為 `true` 以回應點擊。

下列範例顯示如何在 XAML 中具現化 `ImageButton`，並處理其 `Clicked` 事件：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="FormsGallery.XamlExamples.ImageButtonDemoPage"
             Title="ImageButton Demo">
    <StackLayout>
        <Label Text="ImageButton"
               FontSize="50"
               FontAttributes="Bold"
               HorizontalOptions="Center" />

       <ImageButton Source="XamarinLogo.png"
                    HorizontalOptions="Center"
                    VerticalOptions="CenterAndExpand"
                    Clicked="OnImageButtonClicked" />

        <Label x:Name="label"
               Text="0 ImageButton clicks"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

`Clicked` 事件會設定為位於程式碼後置檔案中名為 `OnImageButtonClicked` 的事件處理常式：

```csharp
public partial class ImageButtonDemoPage : ContentPage
{
    int clickTotal;

    public ImageButtonDemoPage()
    {
        InitializeComponent();
    }

    void OnImageButtonClicked(object sender, EventArgs e)
    {
        clickTotal += 1;
        label.Text = $"{clickTotal} ImageButton click{(clickTotal == 1 ? "" : "s")}";
    }
}
```

當您按下 `ImageButton` 時，就會執行 `OnImageButtonClicked` 方法。 `sender` 引數是負責此事件的 `ImageButton`。 您可以使用這個來存取 `ImageButton` 物件，或區別共用相同 `Clicked` 事件的多個 `ImageButton` 物件。

這個特定的 `Clicked` 處理常式會遞增計數器，並在[`Label`](xref:Xamarin.Forms.Label)中顯示計數器值：

[![基本 ImageButton 按一下](imagebutton-images/ImageButton.png "基本 ImageButton 按一下")](imagebutton-images/ImageButton-Large.png#lightbox "基本 ImageButton 按一下")

下列範例示範如何建立功能相當於先前 XAML 範例的頁面，但完全在中C#：

```csharp
public class ImageButtonDemoPage : ContentPage
{
    Label label;
    int clickTotal = 0;

    public ImageButtonDemoPage()
    {
        Label header = new Label
        {
            Text = "ImageButton",
            FontSize = 50,
            FontAttributes = FontAttributes.Bold,
            HorizontalOptions = LayoutOptions.Center
        };

        ImageButton imageButton = new ImageButton
        {
            Source = "XamarinLogo.png",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };
        imageButton.Clicked += OnImageButtonClicked;

        label = new Label
        {
            Text = "0 ImageButton clicks",
            FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)),
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };

        // Build the page.
        Title = "ImageButton Demo";
        Content = new StackLayout
        {
            Children =
            {
                header,
                imageButton,
                label
            }
        };
    }

    void OnImageButtonClicked(object sender, EventArgs e)
    {
        clickTotal += 1;
        label.Text = $"{clickTotal} ImageButton click{(clickTotal == 1 ? "" : "s")}";
    }
}
```

## <a name="disabling-the-imagebutton"></a>停用 ImageButton

有時候應用程式處於特定狀態，特定的 `ImageButton` 按一下則不是有效的作業。 在這些情況下，應該藉由將其 `IsEnabled` 屬性設定為 `false`來停用 `ImageButton`。

## <a name="using-the-command-interface"></a>使用命令介面

應用程式可以在不處理 `Clicked` 事件的情況下，回應 `ImageButton` 的點擊。 `ImageButton` 會執行稱為_命令或命令_介面的替代通知機制。 這包含兩個屬性：

- [`ICommand`](xref:System.Windows.Input.ICommand)類型的 `Command`，也就是[`System.Windows.Input`](xref:System.Windows.Input)命名空間中定義的介面。
- [`Object`](xref:System.Object)類型的 `CommandParameter` 屬性。

這種方法適用于與資料系結的連接，特別是在執行模型 ViewModel （MVVM）架構時。

如需使用命令介面的詳細資訊，請參閱[按鈕](button.md)指南中[的使用命令介面](button.md#using-the-command-interface)。

## <a name="pressing-and-releasing-the-imagebutton"></a>按下並放開 ImageButton

除了 `Clicked` 事件以外，`ImageButton` 也會定義 `Pressed` 和 `Released` 事件。 當手指按下 `ImageButton`時，或是按下滑鼠按鍵，並將游標置於 `ImageButton`上方時，就會發生 `Pressed` 事件。 釋放手指或滑鼠按鍵時，就會發生 `Released` 事件。 一般來說，`Clicked` 事件也會與 `Released` 事件同時引發，但如果手指或滑鼠指標滑出 `ImageButton` 的表面，則在釋放之前，`Clicked` 事件可能不會發生。

如需這些事件的詳細資訊，請參閱[按鈕](button.md)指南中[的按下並放開按鈕](button.md#pressing-and-releasing-the-button)。

## <a name="imagebutton-appearance"></a>ImageButton 外觀

除了 `ImageButton` 繼承自[`View`](xref:Xamarin.Forms.View)類別的屬性之外，`ImageButton` 也會定義數個會影響其外觀的屬性：

- `Aspect` 是將影像縮放以符合顯示區域的方式。
- `BorderColor` 是圍繞著 `ImageButton`的區域色彩。
- `BorderWidth` 是框線的寬度。
- `CornerRadius` 是 `ImageButton`的圓角半徑。

`Aspect` 屬性可以設定為[`Aspect`](xref:Xamarin.Forms.Aspect)列舉的其中一個成員：

- [`Fill`](xref:Xamarin.Forms.Aspect.Fill) -將影像自動填滿，完全填滿 `ImageButton`。 這可能會導致影像失真。
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill)會將影像裁剪，使其填滿 `ImageButton`，同時保留外觀比例。
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit) letterboxes 影像（如有必要），使整個影像符合 `ImageButton`，並根據影像是寬或高，將空白空間加入至上/下或側邊。 這是[`Aspect`](xref:Xamarin.Forms.Aspect)列舉的預設值。

> [!NOTE]
> `ImageButton` 類別也具有[`Margin`](xref:Xamarin.Forms.View.Margin)和 `Padding` 屬性，可控制 `ImageButton`的版面配置行為。 如需詳細資訊，請參閱[邊界和邊框距離](~/xamarin-forms/user-interface/layouts/margin-and-padding.md)。

## <a name="imagebutton-visual-states"></a>ImageButton 視覺狀態

`ImageButton` 具有 `Pressed` [`VisualState`](xref:Xamarin.Forms.VisualState) ，可以在使用者按下時，用來起始對 `ImageButton` 的視覺變更，前提是已啟用。

下列 XAML 範例顯示如何定義 `Pressed` 狀態的視覺狀態：

```xaml
<ImageButton Source="XamarinLogo.png"
             ...>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>
                    <Setter Property="Scale"
                            Value="1" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Pressed">
                <VisualState.Setters>
                    <Setter Property="Scale"
                            Value="0.8" />
                </VisualState.Setters>
            </VisualState>

        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</ImageButton>
```

`Pressed` [`VisualState`](xref:Xamarin.Forms.VisualState)指定按下 `ImageButton` 時，其[`Scale`](xref:Xamarin.Forms.VisualElement.Scale)屬性會從其預設值1變更為0.8。 `Normal` `VisualState` 指定當 `ImageButton` 處於正常狀態時，其 `Scale` 屬性會設定為1。 因此，整體的效果是當按下 `ImageButton` 時，它的重新調整會稍微小一點，而釋放 `ImageButton` 時，就會重新調整為其預設大小。

如需視覺狀態的詳細資訊，請參閱「 [Xamarin 視覺狀態管理員](~/xamarin-forms/user-interface/visual-state-manager.md)」。

## <a name="related-links"></a>相關連結

- [FormsGallery 範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
