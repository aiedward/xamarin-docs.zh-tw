---
title: Xamarin.Forms ScrollView
description: Xamarin.FormsScrollView 是能夠滾動其內容的版面配置。
ms.prod: xamarin
ms.assetid: 7B542872-B3D1-49B3-B15E-0E98F53C1F6E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/27/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: af8513d86b7cf02ec7adcee75241d8ce99e2dfab
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556824"
---
# <a name="no-locxamarinforms-scrollview"></a>Xamarin.Forms ScrollView

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-scrollviewdemos)

[![：：：非 loc (Xamarin. Forms) ：：： ScrollView](scrollview-images/layouts.png "：：：非 loc (Xamarin. Forms) ：：： ScrollView")](scrollview-images/layouts-large.png#lightbox "：：：非 loc (Xamarin. Forms) ：：： ScrollView")

[`ScrollView`](xref:Xamarin.Forms.ScrollView) 是能夠滾動其內容的版面配置。 `ScrollView`類別衍生自 [`Layout`](xref:Xamarin.Forms.Layout) 類別，而且依預設會垂直捲動其內容。 `ScrollView`雖然可以是其他版面配置，但只能有單一子系。

> [!WARNING]
> [`ScrollView`](xref:Xamarin.Forms.ScrollView) 物件不應該是嵌套的。 此外， `ScrollView` 物件不應該與提供滾動的其他控制項（例如、和）進行 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 嵌套 [`ListView`](xref:Xamarin.Forms.ListView) [`WebView`](xref:Xamarin.Forms.WebView) 。

[`ScrollView`](xref:Xamarin.Forms.ScrollView) 定義下列屬性：

- [`Content`](xref:Xamarin.Forms.ScrollView.Content)型別為的， [`View`](xref:Xamarin.Forms.View) 代表要顯示在中的內容 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 。
- [`ContentSize`](xref:Xamarin.Forms.ScrollView)型別為的， [`Size`](xref:Xamarin.Forms.Size) 表示內容的大小。 這是一個唯讀屬性。
- [`HorizontalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView)型別為的， [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.HorizontalScrollBarVisibility) 表示水準捲軸是可見的。
- [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation)型別為的， [`ScrollOrientation`](xref:Xamarin.Forms.ScrollOrientation) 表示的滾動方向 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 。 此屬性的預設值為 `Vertical`。
- [`ScrollX`](xref:Xamarin.Forms.ScrollView.ScrollX)型別為的 `double` ，表示目前的 X 捲軸位置。 此唯讀屬性的預設值為0。
- [`ScrollY`](xref:Xamarin.Forms.ScrollView.ScrollY)，類型為 `double` ，表示目前的 Y 滾動位置。 此唯讀屬性的預設值為0。
- [`VerticalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView)型別為的， [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.HorizontalScrollBarVisibility) 表示垂直捲動條可見的時機。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，但 [`Content`](xref:Xamarin.Forms.ScrollView.Content) 屬性除外，這表示它們可以是資料系結和樣式的目標。

[`Content`](xref:Xamarin.Forms.ScrollView.Content)屬性是類別的 [`ContentProperty`](xref:Xamarin.Forms.ContentPropertyAttribute) [`ScrollView`](xref:Xamarin.Forms.ScrollView) ，因此不需要從 XAML 明確設定。

> [!TIP]
> 若要取得最佳的版面配置效能，請遵循將 [版面配置效能優化](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance)的指導方針。

## <a name="scrollview-as-a-root-layout"></a>ScrollView 做為根配置

只能 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 有一個子系，可以是其他版面配置。 因此，在頁面上的根配置是很常見的 `ScrollView` 。 若要滾動其子內容，請 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 計算其內容的高度與其本身高度之間的差異。 這項差異是 `ScrollView` 可以滾動其內容的數量。

[`StackLayout`](xref:Xamarin.Forms.StackLayout)通常會是的子系 `ScrollView` 。 在此案例中， `ScrollView` 會使和其子系 `StackLayout` 高度的總和一樣高。 然後， `ScrollView` 可以判斷其內容可滾動的金額。 如需的詳細資訊 `StackLayout` ，請參閱[ Xamarin.Forms StackLayout](stacklayout.md)。

> [!CAUTION]
> 在垂直的情況 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 下，請避免將 `VerticalOptions` 屬性設為 `Start` 、 `Center` 或 `End` 。 如此一來，就會告訴， `ScrollView` 只是它需要的高度，可能是零。 雖然可以防止 Xamarin.Forms 此可能性，但最好避免程式碼建議您不想要進行的事情。

下列 XAML 範例在 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 頁面上具有作為根配置：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ScrollViewDemos"
             x:Class="ScrollViewDemos.Views.ColorListPage"
             Title="ScrollView demo">
    <ScrollView>
        <StackLayout BindableLayout.ItemsSource="{x:Static local:NamedColor.All}">
            <BindableLayout.ItemTemplate>
                <DataTemplate>
                    <StackLayout Orientation="Horizontal">
                        <BoxView Color="{Binding Color}"
                                 HeightRequest="32"
                                 WidthRequest="32"
                                 VerticalOptions="Center" />
                        <Label Text="{Binding FriendlyName}"
                               FontSize="24"
                               VerticalOptions="Center" />
                    </StackLayout>
                </DataTemplate>
            </BindableLayout.ItemTemplate>
        </StackLayout>
    </ScrollView>
</ContentPage>
```

在此範例中， [`ScrollView`](xref:Xamarin.Forms.ScrollView) 會將其內容設定為 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ，以使用可系結配置來顯示所 [`Color`](xref:Xamarin.Forms.Color) 定義的欄位 Xamarin.Forms 。 依預設，會 `ScrollView` 垂直捲動，以顯示更多內容：

[![根 ScrollView 版面配置的螢幕擷取畫面](scrollview-images/root-layout.png "根 ScrollView 版面配置")](scrollview-images/root-layout-large.png#lightbox "根 ScrollView 版面配置")

對等的 C# 程式碼為：

```csharp
public class ColorListPageCode : ContentPage
{
    public ColorListPageCode()
    {
        DataTemplate dataTemplate = new DataTemplate(() =>
        {
            BoxView boxView = new BoxView
            {
                HeightRequest = 32,
                WidthRequest = 32,
                VerticalOptions = LayoutOptions.Center
            };
            boxView.SetBinding(BoxView.ColorProperty, "Color");

            Label label = new Label
            {
                FontSize = 24,
                VerticalOptions = LayoutOptions.Center
            };
            label.SetBinding(Label.TextProperty, "FriendlyName");

            StackLayout horizontalStackLayout = new StackLayout
            {
                Orientation = StackOrientation.Horizontal,
                Children = { boxView, label }
            };
            return horizontalStackLayout;
        });

        StackLayout stackLayout = new StackLayout();
        BindableLayout.SetItemsSource(stackLayout, NamedColor.All);
        BindableLayout.SetItemTemplate(stackLayout, dataTemplate);

        ScrollView scrollView = new ScrollView { Content = stackLayout };

        Title = "ScrollView demo";
        Content = scrollView;
    }
}
```

如需可系結配置的詳細資訊，請參閱[中 Xamarin.Forms ](bindable-layouts.md)的可系結版面配置。

## <a name="scrollview-as-a-child-layout"></a>ScrollView 做為子版面配置

[`ScrollView`](xref:Xamarin.Forms.ScrollView)可以是不同父配置的子版面配置。

[`ScrollView`](xref:Xamarin.Forms.ScrollView)通常會是的子系 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。 `ScrollView`需要特定的高度來計算其內容高度與其本身高度之間的差異，差別在於 `ScrollView` 可滾動其內容的數量差異。 當是的子系時 `ScrollView` `StackLayout` ，它不會收到特定的高度。 `StackLayout`希望越 `ScrollView` 短越好，也就是內容的高度 `ScrollView` 或零。 若要處理此案例，的 `VerticalOptions` 屬性 `ScrollView` 應該設定為 `FillAndExpand` 。 這會導致 `StackLayout` 提供 `ScrollView` 其他子系不需要的所有額外空間， `ScrollView` 然後會有特定的高度。

下列 XAML 範例將做為 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 子配置給 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ScrollViewDemos.Views.BlackCatPage"
             Title="ScrollView as a child layout demo">
    <StackLayout Margin="20">
        <Label Text="THE BLACK CAT by Edgar Allan Poe"
               FontSize="Medium"
               FontAttributes="Bold"
               HorizontalOptions="Center" />
        <ScrollView VerticalOptions="FillAndExpand">
            <StackLayout>
                <Label Text="FOR the most wild, yet most homely narrative which I am about to pen, I neither expect nor solicit belief. Mad indeed would I be to expect it, in a case where my very senses reject their own evidence. Yet, mad am I not -- and very surely do I not dream. But to-morrow I die, and to-day I would unburthen my soul. My immediate purpose is to place before the world, plainly, succinctly, and without comment, a series of mere household events. In their consequences, these events have terrified -- have tortured -- have destroyed me. Yet I will not attempt to expound them. To me, they have presented little but Horror -- to many they will seem less terrible than barroques. Hereafter, perhaps, some intellect may be found which will reduce my phantasm to the common-place -- some intellect more calm, more logical, and far less excitable than my own, which will perceive, in the circumstances I detail with awe, nothing more than an ordinary succession of very natural causes and effects." />
                <!-- More Label objects go here -->
            </StackLayout>
        </ScrollView>
    </StackLayout>
</ContentPage>
```

在此範例中，有兩個 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 物件。 第一個 `StackLayout` 是根設定物件，其具有 [`Label`](xref:Xamarin.Forms.Label) 物件和 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 做為其子系。 `ScrollView`具有 `StackLayout` 做為其內容的， `StackLayout` 包含多個 `Label` 物件。 這種相片順序可確保第一個 `Label` 一律是螢幕上，而其他物件所顯示的文字 `Label` 可以滾動：

[![子 ScrollView 版面配置的螢幕擷取畫面](scrollview-images/child-layout.png "子 ScrollView 版面配置")](scrollview-images/child-layout-large.png#lightbox "子 ScrollView 版面配置")

對等的 C# 程式碼為：

```csharp
public class BlackCatPageCS : ContentPage
{
    public BlackCatPageCS()
    {
        Label titleLabel = new Label
        {
            Text = "THE BLACK CAT by Edgar Allan Poe",
            // More properties set here to define the Label appearance
        };

        ScrollView scrollView = new ScrollView
        {
            VerticalOptions = LayoutOptions.FillAndExpand,
            Content = new StackLayout
            {
                Children =
                {
                    new Label
                    {
                        Text = "FOR the most wild, yet most homely narrative which I am about to pen, I neither expect nor solicit belief. Mad indeed would I be to expect it, in a case where my very senses reject their own evidence. Yet, mad am I not -- and very surely do I not dream. But to-morrow I die, and to-day I would unburthen my soul. My immediate purpose is to place before the world, plainly, succinctly, and without comment, a series of mere household events. In their consequences, these events have terrified -- have tortured -- have destroyed me. Yet I will not attempt to expound them. To me, they have presented little but Horror -- to many they will seem less terrible than barroques. Hereafter, perhaps, some intellect may be found which will reduce my phantasm to the common-place -- some intellect more calm, more logical, and far less excitable than my own, which will perceive, in the circumstances I detail with awe, nothing more than an ordinary succession of very natural causes and effects.",
                    },
                    // More Label objects go here
                }
            }
        };

        Title = "ScrollView as a child layout demo";
        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children = { titleLabel, scrollView }
        };
    }
}
```

## <a name="orientation"></a>方向

[`ScrollView`](xref:Xamarin.Forms.ScrollView) 具有 [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation) 屬性，代表的滾動方向 `ScrollView` 。 這個屬性的類型是 [`ScrollOrientation`](xref:Xamarin.Forms.ScrollOrientation) ，它會定義下列成員：

- `Vertical` 指出 `ScrollView` 將會垂直捲動。 這個成員是屬性的預設值 [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation) 。
- `Horizontal` 指出 `ScrollView` 將會水準滾動。
- `Both` 指出 `ScrollView` 將會水準且垂直地滾動。
- `Neither` 表示 `ScrollView` 不會滾動。

> [!TIP]
> 您可以藉由將屬性設定 [`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) 為來停用滾動 `Neither` 。

## <a name="detect-scrolling"></a>偵測滾動

[`ScrollView`](xref:Xamarin.Forms.ScrollView) 定義 [`Scrolled`](xref:Xamarin.Forms.ScrollView.Scrolled) 引發的事件以表示發生滾動。 [`ScrolledEventArgs`](xref:Xamarin.Forms.ScrolledEventArgs)事件隨附的物件 `Scrolled` 具有 `ScrollX` 和 `ScrollY` 屬性，兩者都是型別 `double` 。

> [!IMPORTANT]
> `ScrolledEventArgs.ScrollX`和 `ScrolledEventArgs.ScrollY` 屬性（property）可能會有負值，這是因為滾動回開頭時發生的彈跳效果 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 。

下列 XAML 範例顯示 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 設定事件之事件處理常式的 [`Scrolled`](xref:Xamarin.Forms.ScrollView.Scrolled) ：

```xaml
<ScrollView Scrolled="OnScrollViewScrolled">
        ...
</ScrollView>
```

對等的 C# 程式碼為：

```csharp
ScrollView scrollView = new ScrollView();
scrollView.Scrolled += OnScrollViewScrolled;
```

在此範例中， `OnScrollViewScrolled` 事件處理常式會在 [`Scrolled`](xref:Xamarin.Forms.ScrollView.Scrolled) 事件引發時執行：

```csharp
void OnScrollViewScrolled(object sender, ScrolledEventArgs e)
{
    Console.WriteLine($"ScrollX: {e.ScrollX}, ScrollY: {e.ScrollY}");
}
```

在此範例中， `OnScrollViewScrolled` 事件處理常式 [`ScrolledEventArgs`](xref:Xamarin.Forms.ScrolledEventArgs) 會輸出伴隨于事件的物件值。

> [!NOTE]
> [`Scrolled`](xref:Xamarin.Forms.ScrollView.Scrolled)引發使用者起始滾動的事件，以及以程式設計方式滾動的事件。

## <a name="scroll-programmatically"></a>以程式設計方式滾動

[`ScrollView`](xref:Xamarin.Forms.ScrollView) 定義兩個 [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) 方法，以非同步方式滾動 `ScrollView` 。 其中一個多載會在中滾動至指定的位置，而另一個多載則會將 `ScrollView` 指定的專案滾動到視野中。 這兩個多載都有一個額外的引數，可用來指出是否要以動畫顯示捲軸。

> [!IMPORTANT]
> [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*)當屬性設定為時，方法不會產生滾動 [`ScrollView.Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) `Neither` 。

### <a name="scroll-a-position-into-view"></a>將位置滾動至視野

中的位置 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 可以使用 [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) 接受 `double` `x` 和引數的方法來進行滾動 `y` 。 假設有一個 `ScrollView` 名為的垂直物件 `scrollView` ，則下列範例會示範如何從最上層滾動至150與裝置無關的單位 `ScrollView` ：

```csharp
await scrollView.ScrollToAsync(0, 150, true);
```

的第三個引數 [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) 是 `animated` 引數，可決定在以程式設計方式滾動時是否顯示滾動動畫 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 。

### <a name="scroll-an-element-into-view"></a>將元素滾動至 view

您 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 可以使用 [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) 接受 [`Element`](xref:Xamarin.Forms.Element) 和引數的方法，將中的專案滾動到視圖中 [`ScrollToPosition`](xref:Xamarin.Forms.ScrollToPosition) 。 如果指定了一個名為的垂直 `ScrollView` `scrollView` ，並指定名為的 [`Label`](xref:Xamarin.Forms.Label) `label` ，則下列範例會示範如何將元素滾動至 view：

```csharp
await scrollView.ScrollToAsync(label, ScrollToPosition.End, true);
```

的第三個引數 [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) 是 `animated` 引數，可決定在以程式設計方式滾動時是否顯示滾動動畫 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 。

當您將專案轉換成 view 時，可以使用方法的第二個引數來設定專案在捲軸之後的確切位置 `position` [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) 。 此引數接受 [`ScrollToPosition`](xref:Xamarin.Forms.ScrollToPosition) 列舉成員：

- `MakeVisible` 指出專案應該在顯示于中時才會滾動 `ScrollView` 。
- `Start` 指出專案應該滾動至的開頭 `ScrollView` 。
- `Center` 指出元素應滾動至的中心 `ScrollView` 。
- `End` 指出專案應該滾動至的結尾 `ScrollView` 。

## <a name="scroll-bar-visibility"></a>捲軸可見度

[`ScrollView`](xref:Xamarin.Forms.ScrollView) 定義 [`HorizontalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView) 和 [`VerticalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView) 屬性，這些屬性是由可系結屬性所支援。 這些屬性會取得或設定 [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.HorizontalScrollBarVisibility) 代表水準或垂直捲動條是否可見的列舉值。 `ScrollBarVisibility` 列舉會定義下列成員：

- `Default` 表示平臺的預設捲軸行為，而且是和屬性的預設值 `HorizontalScrollBarVisibility` `VerticalScrollBarVisibility` 。
- `Always` 表示捲軸會顯示出來，即使內容符合視野也是如此。
- `Never` 指出不會顯示捲軸，即使內容無法容納在視圖中也一樣。

## <a name="related-links"></a>相關連結

- [ScrollView 示範 (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-scrollviewdemos)
- [Xamarin.Forms StackLayout](stacklayout.md)
- [中的可系結版面配置 Xamarin.Forms](bindable-layouts.md)