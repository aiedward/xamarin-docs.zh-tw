---
title: Xamarin.FormsScrollView
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
ms.openlocfilehash: f527acabe3b051cbfd6450ba6f5328449b1a728c
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84199138"
---
# <a name="xamarinforms-scrollview"></a>Xamarin.FormsScrollView

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-scrollviewdemos)

[![Xamarin.FormsScrollView](scrollview-images/layouts.png "[!OP.無-LOC （Xamarin）] ScrollView")](scrollview-images/layouts-large.png#lightbox "[!OP.無-LOC （Xamarin）] ScrollView")

[`ScrollView`](xref:Xamarin.Forms.ScrollView)是能夠滾動其內容的版面配置。 `ScrollView`類別衍生自 [`Layout`](xref:Xamarin.Forms.Layout) 類別，而且依預設會垂直捲動其內容。 只能 `ScrollView` 有一個子系，雖然這可以是其他配置。

> [!WARNING]
> [`ScrollView`](xref:Xamarin.Forms.ScrollView)不應將物件嵌套。 此外， `ScrollView` 物件不應該與提供滾動的其他控制項（例如、和）加以 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 嵌套 [`ListView`](xref:Xamarin.Forms.ListView) [`WebView`](xref:Xamarin.Forms.WebView) 。

[`ScrollView`](xref:Xamarin.Forms.ScrollView)定義下列屬性：

- [`Content`](xref:Xamarin.Forms.ScrollView.Content)，屬於類型 [`View`](xref:Xamarin.Forms.View) ，表示要在中顯示的內容 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 。
- [`ContentSize`](xref:Xamarin.Forms.ScrollView)，屬於類型 [`Size`](xref:Xamarin.Forms.Size) ，表示內容的大小。 這是一個唯讀屬性。
- [`HorizontalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView)，屬於類型 [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.HorizontalScrollBarVisibility) ，代表水準捲軸是可見的。
- [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation)，屬於類型 [`ScrollOrientation`](xref:Xamarin.Forms.ScrollOrientation) ，表示的滾動方向 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 。 此屬性的預設值為 `Vertical`。
- [`ScrollX`](xref:Xamarin.Forms.ScrollView.ScrollX)，屬於類型 `double` ，表示目前的 X 捲軸位置。 這個唯讀屬性的預設值為0。
- [`ScrollY`](xref:Xamarin.Forms.ScrollView.ScrollY)，屬於類型 `double` ，表示目前的 Y scroll 位置。 這個唯讀屬性的預設值為0。
- [`VerticalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView)，屬於類型 [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.HorizontalScrollBarVisibility) ，代表垂直捲動條可見的時間。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，但 [`Content`](xref:Xamarin.Forms.ScrollView.Content) 屬性除外，這表示它們可以是資料系結和樣式的目標。

[`Content`](xref:Xamarin.Forms.ScrollView.Content)屬性是類別的 [`ContentProperty`](xref:Xamarin.Forms.ContentPropertyAttribute) [`ScrollView`](xref:Xamarin.Forms.ScrollView) ，因此不需要從 XAML 明確設定。

> [!TIP]
> 若要取得最佳的版面配置效能，請遵循[優化版面配置效能](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance)中的指導方針。

## <a name="scrollview-as-a-root-layout"></a>ScrollView 做為根版面配置

只能 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 有一個子系，可以是其他配置。 因此 `ScrollView` ，在頁面上的根版面配置是常見的。 為了滾動其子內容，會 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 計算其內容及其本身高度的高度差異。 這種差異就是 `ScrollView` 可以滾動其內容的數量。

[`StackLayout`](xref:Xamarin.Forms.StackLayout)通常會是的子系 `ScrollView` 。 在此案例中，會使達到與其子系 `ScrollView` 高度的總和一樣高的 `StackLayout` 高度。 然後， `ScrollView` 可以決定其內容可以滾動的數量。 如需的詳細資訊 `StackLayout` ，請參閱[ Xamarin.Forms StackLayout](stacklayout.md)。

> [!CAUTION]
> 在垂直的中 [`ScrollView`](xref:Xamarin.Forms.ScrollView) ，請避免將 `VerticalOptions` 屬性設定為 `Start` 、 `Center` 或 `End` 。 這麼做會告訴， `ScrollView` 只是需要的高度，可能是零。 雖然 Xamarin.Forms 可防範此可能性，但最好避免程式碼建議您不想發生的問題。

下列 XAML 範例在 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 頁面上有做為根版面配置：

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

在此範例中， [`ScrollView`](xref:Xamarin.Forms.ScrollView) 會將其內容設定為，其會使用可系結的配置 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 來顯示所 [`Color`](xref:Xamarin.Forms.Color) 定義的欄位 Xamarin.Forms 。 根據預設， `ScrollView` 垂直捲動會顯示更多內容：

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

如需可系結版面配置的詳細資訊，請參閱[中 Xamarin.Forms ](bindable-layouts.md)的可系結配置。

## <a name="scrollview-as-a-child-layout"></a>ScrollView 做為子版面配置

[`ScrollView`](xref:Xamarin.Forms.ScrollView)可以是不同父系版面配置的子配置。

[`ScrollView`](xref:Xamarin.Forms.ScrollView)通常會是的子系 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。 `ScrollView`需要特定的高度來計算其內容的高度和其本身高度之間的差異，而差異則是 `ScrollView` 可滾動其內容的數量。 當是的子系時 `ScrollView` `StackLayout` ，它不會收到特定的高度。 會 `StackLayout` 希望越 `ScrollView` 短越好，這可能是內容的高度 `ScrollView` 或零。 若要處理這種情況，的 `VerticalOptions` 屬性 `ScrollView` 應該設定為 `FillAndExpand` 。 這會導致 `StackLayout` 提供 `ScrollView` 其他子系不需要的所有額外空間， `ScrollView` 然後會有特定的高度。

下列 XAML 範例具有做為 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 子配置的 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ：

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

在此範例中，有兩個 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 物件。 第一個 `StackLayout` 是根版面設定物件，其具有 [`Label`](xref:Xamarin.Forms.Label) 物件和 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 作為其子系。 `ScrollView`具有 `StackLayout` 做為其內容，其中 `StackLayout` 包含多個 `Label` 物件。 這種安排可確保第一個 `Label` 一律是螢幕上，而其他物件所顯示的文字則 `Label` 可以滾動：

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

[`ScrollView`](xref:Xamarin.Forms.ScrollView)具有 [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation) 屬性，其代表的滾動方向 `ScrollView` 。 這個屬性的類型是 [`ScrollOrientation`](xref:Xamarin.Forms.ScrollOrientation) ，它會定義下列成員：

- `Vertical`表示 `ScrollView` 將會垂直捲動。 這個成員是屬性的預設值 [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation) 。
- `Horizontal`表示 `ScrollView` 將水準滾動。
- `Both`表示 `ScrollView` 將水準和垂直捲動。
- `Neither`表示 `ScrollView` 不會滾動。

> [!TIP]
> 將屬性設定為，即可停用滾動 [`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) `Neither` 。

## <a name="detect-scrolling"></a>偵測滾動

[`ScrollView`](xref:Xamarin.Forms.ScrollView)定義 [`Scrolled`](xref:Xamarin.Forms.ScrollView.Scrolled) 引發的事件，表示已發生滾動。 [`ScrolledEventArgs`](xref:Xamarin.Forms.ScrolledEventArgs)事件隨附的物件 `Scrolled` 具有 `ScrollX` 和 `ScrollY` 屬性，這兩種類型都是 `double` 。

> [!IMPORTANT]
> `ScrolledEventArgs.ScrollX`和 `ScrolledEventArgs.ScrollY` 屬性可以有負值，這是因為在滾動回到開頭時所發生的跳動效果 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 。

下列 XAML 範例顯示 [`ScrollView`](xref:Xamarin.Forms.ScrollView) ，它會設定事件的事件處理常式 [`Scrolled`](xref:Xamarin.Forms.ScrollView.Scrolled) ：

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

在此範例中，事件 `OnScrollViewScrolled` 處理常式會在 [`Scrolled`](xref:Xamarin.Forms.ScrollView.Scrolled) 事件引發時執行：

```csharp
void OnScrollViewScrolled(object sender, ScrolledEventArgs e)
{
    Console.WriteLine($"ScrollX: {e.ScrollX}, ScrollY: {e.ScrollY}");
}
```

在此範例中， `OnScrollViewScrolled` 事件處理常式 [`ScrolledEventArgs`](xref:Xamarin.Forms.ScrolledEventArgs) 會輸出事件隨附之物件的值。

> [!NOTE]
> [`Scrolled`](xref:Xamarin.Forms.ScrollView.Scrolled)引發使用者起始滾動的事件，並以程式設計方式進行滾動。

## <a name="scroll-programmatically"></a>以程式設計方式滾動

[`ScrollView`](xref:Xamarin.Forms.ScrollView)定義兩個 [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) 方法，以非同步方式滾動 `ScrollView` 。 其中一個多載會滾動到中的指定位置，而另一個則會將指定的專案 `ScrollView` 滾動到 view。 這兩個多載都有額外的引數，可用來指出是否要以動畫方式顯示捲軸。

> [!IMPORTANT]
> [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*)當屬性設定為時，方法不會產生捲軸 [`ScrollView.Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) `Neither` 。

### <a name="scroll-a-position-into-view"></a>將位置滾動到視野中

您 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 可以使用 [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) 接受 `double` `x` 和 `y` 引數的方法，將中的位置滾動至。 假設有一個 `ScrollView` 名為的垂直物件 `scrollView` ，下列範例會示範如何從頂端滾動到150裝置獨立單位 `ScrollView` ：

```csharp
await scrollView.ScrollToAsync(0, 150, true);
```

的第三個引數 [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) 是 `animated` 引數，可決定以程式設計方式滾動時是否顯示滾動動畫 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 。

### <a name="scroll-an-element-into-view"></a>將元素滾動到視圖中

中的專案 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 可以使用 [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) 接受 [`Element`](xref:Xamarin.Forms.Element) 和引數的方法來滾動到 view [`ScrollToPosition`](xref:Xamarin.Forms.ScrollToPosition) 。 如果指定了 `ScrollView` 名為的垂直 `scrollView` 和 [`Label`](xref:Xamarin.Forms.Label) 名為的， `label` 則下列範例會示範如何將元素滾動到 view：

```csharp
await scrollView.ScrollToAsync(label, ScrollToPosition.End, true);
```

的第三個引數 [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) 是 `animated` 引數，可決定以程式設計方式滾動時是否顯示滾動動畫 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 。

將專案滾動到 view 時，可以使用方法的第二個引數來設定元素在完成滾動之後的確切位置 `position` [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) 。 這個引數會接受 [`ScrollToPosition`](xref:Xamarin.Forms.ScrollToPosition) 列舉成員：

- `MakeVisible`表示專案應該滾動，直到在中可見為止 `ScrollView` 。
- `Start`指出元素應該滾動到的開頭 `ScrollView` 。
- `Center`表示元素應該滾動到的中心 `ScrollView` 。
- `End`表示元素應該滾動到的結尾 `ScrollView` 。

## <a name="scroll-bar-visibility"></a>捲軸可見度

[`ScrollView`](xref:Xamarin.Forms.ScrollView)定義 [`HorizontalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView) 和 [`VerticalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView) 屬性，並由可系結屬性支援。 這些屬性會取得或設定 [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.HorizontalScrollBarVisibility) 列舉值，表示水準或垂直捲動條是否為可見。 `ScrollBarVisibility` 列舉會定義下列成員：

- `Default`表示平臺的預設捲軸行為，而是和屬性的預設值 `HorizontalScrollBarVisibility` `VerticalScrollBarVisibility` 。
- `Always`表示捲軸會顯示出來，即使內容適合在視圖中也一樣。
- `Never`表示即使內容無法放在視圖中，也不會顯示捲軸。

## <a name="related-links"></a>相關連結

- [ScrollView 示範（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-scrollviewdemos)
- [Xamarin.FormsStackLayout](stacklayout.md)
- [中可系結的版面配置Xamarin.Forms](bindable-layouts.md)
