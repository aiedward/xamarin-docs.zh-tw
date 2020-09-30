---
title: Xamarin.Forms ImageButton
description: ImageButton 會顯示影像，並回應點擊或按一下以引導應用程式執行特定工作。
ms.prod: xamarin
ms.assetid: B5906AB6-3F79-4FCB-8C78-1F0AF18AB39E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/04/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3b27ef8ecbd5f357eabd728423b5787ea222c593
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562297"
---
# <a name="no-locxamarinforms-imagebutton"></a>Xamarin.Forms ImageButton

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_ImageButton 會顯示影像，並回應點擊或按一下以引導應用程式執行特定工作。_

此 `ImageButton` 視圖結合了 [`Button`](xref:Xamarin.Forms.Button) view 和 [`Image`](xref:Xamarin.Forms.Image) view，以建立其內容為影像的按鈕。 使用者按下 `ImageButton` 手指，或按一下滑鼠右鍵，以指示應用程式執行特定工作。 但是，與視圖不同的 `Button` 是， `ImageButton` view 沒有文字和文字外觀的概念。

> [!NOTE]
> 雖然 [`Button`](xref:Xamarin.Forms.Button) view 定義了一個 [`Image`](xref:Xamarin.Forms.Button.Image) 屬性，可讓您在上顯示影像，但 `Button` 這個屬性的目的是要在文字旁邊顯示小圖示時使用 `Button` 。

本指南中的程式碼範例取自 [FormsGallery 範例](/samples/xamarin/xamarin-forms-samples/formsgallery)。

## <a name="setting-the-image-source"></a>設定影像來源

`ImageButton` 定義 `Source` 屬性，此屬性應設定為要顯示在按鈕中的影像，而影像來源為檔案、URI、資源或串流。 如需從不同來源載入影像的詳細資訊，請參閱[中 Xamarin.Forms 的影像](images.md)。

下列範例顯示如何 `ImageButton` 在 XAML 中具現化：

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

`Source`屬性會指定出現在中的影像 `ImageButton` 。 在此範例中，它會設定為將從每個平臺專案載入的本機檔案，並產生下列螢幕擷取畫面：

[![基本 ImageButton](imagebutton-images/BasicImageButton.png "基本 ImageButton")](imagebutton-images/BasicImageButton-Large.png#lightbox "基本 ImageButton")

依預設， `ImageButton` 是矩形，但您可以使用屬性為其圓角 `CornerRadius` 。 如需外觀的詳細資訊 `ImageButton` ，請參閱 [ImageButton 外觀](#imagebutton-appearance)。

> [!NOTE]
> 雖然 `ImageButton` 可以載入動畫 gif，但它只會顯示 gif 的第一個畫面格。

下列範例示範如何建立功能相當於先前 XAML 範例但完全以 c # 撰寫的頁面：

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

`ImageButton` 定義 `Clicked` 當使用者 `ImageButton` 利用手指或滑鼠指標來點擊時，所引發的事件。 從的介面釋出手指或滑鼠按鍵時，就會引發此事件 `ImageButton` 。 `ImageButton`必須將其 `IsEnabled` 屬性設定為， `true` 以回應點擊。

下列範例顯示如何 `ImageButton` 在 XAML 中具現化，並處理其 `Clicked` 事件：

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

`Clicked`事件會設定為名為的事件處理常式 `OnImageButtonClicked` ，該處理常式位於程式碼後置檔案中：

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

方法會在點選 `ImageButton``OnImageButtonClicked` 時執行。 `sender`引數是 `ImageButton` 此事件的負責任。 您可以使用此專案來存取 `ImageButton` 物件，或區別多個 `ImageButton` 共用相同事件的物件 `Clicked` 。

這個特定 `Clicked` 的處理常式會遞增計數器，並在中顯示計數器值 [`Label`](xref:Xamarin.Forms.Label) ：

[![基本 ImageButton 按一下](imagebutton-images/ImageButton.png "基本 ImageButton 按一下")](imagebutton-images/ImageButton-Large.png#lightbox "基本 ImageButton 按一下")

下列範例示範如何建立功能相當於先前 XAML 範例但完全以 c # 撰寫的頁面：

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

有時候，應用程式會處於特定狀態，其中特定的 click 不是有效的作業 `ImageButton` 。 在這些情況下，您 `ImageButton` 應該將其屬性設定 `IsEnabled` 為來停用 `false` 。

## <a name="using-the-command-interface"></a>使用命令介面

應用程式可能會在 `ImageButton` 不處理事件的情況下回應點擊 `Clicked` 。 會 `ImageButton` 執行另一個稱為命令或_命令_介面的_commanding_通知機制。 這包括兩個屬性：

- `Command` 型別為的 [`ICommand`](xref:System.Windows.Input.ICommand) 介面，此介面定義于 [`System.Windows.Input`](xref:System.Windows.Input) 命名空間中。
- `CommandParameter` 型別的屬性 [`Object`](xref:System.Object) 。

這種方法適用于與資料系結的連接，特別是在實 ViewModel (MVVM) 架構的模型視圖時。

如需使用命令介面的詳細資訊，請參閱使用[按鈕](button.md)指南中[的命令介面](button.md#using-the-command-interface)。

## <a name="pressing-and-releasing-the-imagebutton"></a>按下並放開 ImageButton

除了 `Clicked` 事件，`ImageButton` 也會定義 `Pressed` 和 `Released` 事件。 `Pressed`當手指按下 `ImageButton` ，或按下滑鼠按鍵時，就會發生此事件 `ImageButton` 。 `Released`釋放手指或滑鼠按鍵時，就會發生此事件。 一般而言， `Clicked` 事件也會與事件同時引發 `Released` ，但如果手指或滑鼠指標從的表面滑離 `ImageButton` ，則 `Clicked` 可能不會發生此事件。

如需這些事件的詳細資訊，請參閱[按鈕](button.md)指南中的[按下並放開按鈕](button.md#pressing-and-releasing-the-button)。

## <a name="imagebutton-appearance"></a>ImageButton 外觀

除了 `ImageButton` 繼承自類別的屬性之外 [`View`](xref:Xamarin.Forms.View) ， `ImageButton` 也會定義數個影響其外觀的屬性：

- `Aspect` 是調整影像大小以符合顯示區域的方式。
- `BorderColor` 是周圍區域的色彩 `ImageButton` 。
- `BorderWidth` 這是框線的寬度。
- `CornerRadius` 是的圓角半徑 `ImageButton` 。

`Aspect`屬性可以設定為列舉的其中一個成員 [`Aspect`](xref:Xamarin.Forms.Aspect) ：

- [`Fill`](xref:Xamarin.Forms.Aspect.Fill) -將影像完全填滿，完全填滿 `ImageButton` 。 這可能會導致影像失真。
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill) -裁剪影像，使其填滿， `ImageButton` 同時保留外觀比例。
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit) -如有必要，請 letterboxes 影像 () 如此一來，整個影像就會放入 `ImageButton` ，並根據影像的寬度或高度，將空白空間新增至頂端/底部或側邊。 這是列舉的預設值 [`Aspect`](xref:Xamarin.Forms.Aspect) 。

> [!NOTE]
> `ImageButton`類別也有 [`Margin`](xref:Xamarin.Forms.View.Margin) 和 `Padding` 屬性，可控制的版面配置行為 `ImageButton` 。 如需詳細資訊，請參閱[邊界和邊框距離](~/xamarin-forms/user-interface/layouts/margin-and-padding.md)。

## <a name="imagebutton-visual-states"></a>ImageButton 視覺狀態

`ImageButton` 的具有， `Pressed` [`VisualState`](xref:Xamarin.Forms.VisualState) 可在使用者按下時，用來起始對的視覺變更 `ImageButton` ，但前提是已啟用它。

下列 XAML 範例顯示如何定義狀態的視覺狀態 `Pressed` ：

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

`Pressed` [`VisualState`](xref:Xamarin.Forms.VisualState) 指定當 `ImageButton` 按下時，其 [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) 屬性將會從預設值1變更為0.8。 `Normal` `VisualState` 指定當 `ImageButton` 處於正常狀態時，其 `Scale` 屬性會設定為1。 因此，整體效果是當 `ImageButton` 按下時，重新調整會稍微減少一點，當 `ImageButton` 釋放時，會重新調整為預設大小。

如需視覺狀態的詳細資訊，請參閱 [ Xamarin.Forms 視覺狀態管理員](~/xamarin-forms/user-interface/visual-state-manager.md)。

## <a name="related-links"></a>相關連結

- [FormsGallery 範例](/samples/xamarin/xamarin-forms-samples/formsgallery)