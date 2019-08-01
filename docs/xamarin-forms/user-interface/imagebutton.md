---
title: Xamarin.Forms ImageButton
description: ImageButton 顯示影像，並回應點選或按一下，以指示應用程式執行特定工作。
ms.prod: xamarin
ms.assetid: B5906AB6-3F79-4FCB-8C78-1F0AF18AB39E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/19/2018
ms.openlocfilehash: 90d860cb6429025672a058e22e76f029872d3b34
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657179"
---
# <a name="xamarinforms-imagebutton"></a>Xamarin.Forms ImageButton

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_ImageButton 顯示影像，並回應點選或按一下，以指示應用程式執行特定工作。_

`ImageButton`檢視結合[ `Button` ](xref:Xamarin.Forms.Button)檢視並[ `Image` ](xref:Xamarin.Forms.Image)檢視，以建立其內容的按鈕是影像。 使用者按下`ImageButton`食指或按一下滑鼠導向的應用程式來執行特定工作。 不過，不同於`Button`檢視中，`ImageButton`檢視並沒有文字和文字外觀的概念。

> [!NOTE]
> 雖然[ `Button` ](xref:Xamarin.Forms.Button)檢視定義[ `Image` ](xref:Xamarin.Forms.Button.Image)屬性，可讓您在上顯示的影像`Button`，這個屬性要顯示的小圖示時使用旁`Button`文字。

本指南中的程式碼範例取自[FormsGallery 範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)。

## <a name="setting-the-image-source"></a>設定影像來源

`ImageButton` 定義`Source`應該設定為映像，以顯示 [] 按鈕，在與映像來源檔案、 URI、 一個資源，或資料流的屬性。 如需有關如何從不同的來源載入映像的詳細資訊，請參閱[Xamarin.Forms 中的映像](images.md)。

下列範例示範如何具現化`ImageButton`在 XAML 中：

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

`Source`屬性會指定出現在映像`ImageButton`。 在此範例中，它會設定將會從每個平台專案，導致下列的螢幕擷取畫面載入本機檔案：

[![基本的 ImageButton](imagebutton-images/BasicImageButton.png "基本 ImageButton")](imagebutton-images/BasicImageButton-Large.png#lightbox "基本 ImageButton")

根據預設，`ImageButton`是矩形，但您可以使用來提供 it 圓角邊角`CornerRadius`屬性。 如需詳細資訊`ImageButton`外觀，請參閱 < [ImageButton 外觀](#imagebutton-appearance)。

下列範例示範如何建立功能上相當於前一個 XAML 範例中，但完全的頁面C#:

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

`ImageButton` 定義`Clicked`在使用者點選時引發的事件`ImageButton`用手指或滑鼠指標。 從介面發行手指或滑鼠按鈕時引發事件`ImageButton`。 `ImageButton`必須有其`IsEnabled`屬性設定為`true`點選回應。

下列範例示範如何具現化`ImageButton`XAML 和控制代碼中其`Clicked`事件：

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

`Clicked`事件設定為事件處理常式命名為`OnImageButtonClicked`，其位於程式碼後置檔案：

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

當`ImageButton`點選時，`OnImageButtonClicked`方法執行。 `sender`引數是`ImageButton`負責此事件。 您可以使用來存取`ImageButton`物件，或若要區別多個`ImageButton`共用相同的物件`Clicked`事件。

這個特定`Clicked`處理常式會遞增計數器，並顯示中的計數器值[ `Label` ](xref:Xamarin.Forms.Label):

[![基本的 ImageButton 按一下](imagebutton-images/ImageButton.png "基本 ImageButton 按一下")](imagebutton-images/ImageButton-Large.png#lightbox "基本 ImageButton 按一下")

下列範例示範如何建立功能上相當於前一個 XAML 範例中，但完全的頁面C#:

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

有時候應用程式處於特定狀態在特定`ImageButton`按一下不是有效的作業。 在這些情況下，`ImageButton`應該設定停用其`IsEnabled`屬性設`false`。

## <a name="using-the-command-interface"></a>使用命令介面

可以讓應用程式以回應`ImageButton`不需要處理的點選`Clicked`事件。 `ImageButton`實作替代的通知機制，稱為_命令_或是_命令_介面。 這包含兩個屬性：

- `Command` 型別的[ `ICommand` ](xref:System.Windows.Input.ICommand)，在定義的介面[ `System.Windows.Input` ](xref:System.Windows.Input)命名空間。
- `CommandParameter` 屬性型別的[ `Object` ](xref:System.Object)。

這種方法適合與資料繫結，以及特別是當實作 Model View ViewModel (MVVM) 架構。

如需使用命令介面的詳細資訊，請參閱[使用命令介面](button.md#using-the-command-interface)中[ 按鈕](button.md)指南。

## <a name="pressing-and-releasing-the-imagebutton"></a>按下再放開 ImageButton

除了`Clicked`事件，`ImageButton`也會定義`Pressed`和`Released`事件。 `Pressed`按手指時，就會發生事件`ImageButton`，或按下滑鼠指標位在`ImageButton`。 `Released`發行手指或滑鼠按鈕時，就會發生事件。 一般而言，`Clicked`事件也會引發與同時`Released`事件，但如果手指或滑鼠指標的投影片的表面遠離`ImageButton`之前所發行，`Clicked`事件可能不會發生。

如需有關這些事件的詳細資訊，請參閱 <<c0> [ 按下並放開按鈕](button.md#pressing-and-releasing-the-button)中[ 按鈕](button.md)指南。

## <a name="imagebutton-appearance"></a>ImageButton 外觀

除了屬性之外，`ImageButton`繼承自[ `View` ](xref:Xamarin.Forms.View)類別，`ImageButton`也會定義數個會影響其外觀的屬性：

- `Aspect` 是如何影像會縮放以符合顯示區域。
- `BorderColor` 色彩的周圍區域`ImageButton`。
- `BorderWidth` 是框線的寬度。
- `CornerRadius` 是的圓角半徑`ImageButton`。

`Aspect`屬性可以設為其中一個成員[ `Aspect` ](xref:Xamarin.Forms.Aspect)列舉型別：

- [`Fill`](xref:Xamarin.Forms.Aspect.Fill) -會自動縮放以完全並完全填滿影像`ImageButton`。 這可能導致影像失真。
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill) -裁剪影像，使它填滿`ImageButton`同時維持外觀比例。
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit) -上下黑邊 （如有必要） 的映像，讓整個影像融入`ImageButton`，加入至頂端/底端或側邊，根據映像是否寬或高的空白空間。 這是預設值[ `Aspect` ](xref:Xamarin.Forms.Aspect)列舉型別。

> [!NOTE]
> `ImageButton`類別也有[ `Margin` ](xref:Xamarin.Forms.View.Margin)並`Padding`控制版面配置行為的屬性`ImageButton`。 如需詳細資訊，請參閱 <<c0> [ 邊界和邊框距離](~/xamarin-forms/user-interface/layouts/margin-and-padding.md)。

## <a name="imagebutton-visual-states"></a>ImageButton 視覺狀態

`ImageButton` 已`Pressed` [ `VisualState` ](xref:Xamarin.Forms.VisualState)可用來起始 visual 變更`ImageButton`假設它已啟用，使用者按下時。

下列 XAML 範例示範如何定義視覺狀態的`Pressed`狀態：

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

`Pressed` [ `VisualState` ](xref:Xamarin.Forms.VisualState)指定`ImageButton`按下時，其[ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale)屬性將會變更預設值為 1 到 0.8。 `Normal` `VisualState`指定`ImageButton`處於正常狀態，其`Scale`屬性會設定為 1。 因此，整體的影響是，當`ImageButton`是按下，它會重新調整為稍微較小，且當`ImageButton`是發行時，它會重新調整為其預設大小。

如需有關視覺狀態的詳細資訊，請參閱[Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md)。

## <a name="related-links"></a>相關連結

- [FormsGallery 範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
