---
title: Xamarin. Forms SwipeView
description: SwipeView 是一個容器控制項，它會環繞內容專案，並提供滑動手勢所顯示的操作功能表項目。
ms.prod: xamarin
ms.assetId: 602456B5-701B-4948-B454-B1F31283F1CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/11/2020
ms.openlocfilehash: 6131287b200846a033e0c476d7039dfd774cab68
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "78291569"
---
# <a name="xamarinforms-swipeview"></a>Xamarin. Forms SwipeView

![](~/media/shared/preview.png "This API is currently pre-release")

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-swipeviewdemos/)

`SwipeView` 是一個容器控制項，它會包裝內容專案，並提供滑動手勢所顯示的操作功能表項目：

[![在 iOS 和 Android 上 CollectionView 中 SwipeView 的滑動專案螢幕擷取畫面](swipeview-images/swipeview-collectionview.png "SwipeView 滑動專案")](swipeview-images/swipeview-collectionview-large.png#lightbox "SwipeView 滑動專案")

`SwipeView` 適用于 Xamarin. 表單4.4。 不過，它目前是實驗性，而且只能藉由將下列程式程式碼新增至 iOS 上的 `AppDelegate` 類別、Android 上的 `MainActivity` 類別，或在 UWP 上的 `App` 類別中，然後再呼叫 `Forms.Init`：

```csharp
Forms.SetFlags("SwipeView_Experimental");
```

`SwipeView` 會定義下列屬性：

- `LeftItems`，屬於 `SwipeItems`類型，代表當控制項從左側撥動時，可以叫用的滑動專案。
- `RightItems`，屬於 `SwipeItems`類型，代表當控制項從右邊撥動時，可以叫用的滑動專案。
- `TopItems`，屬於 `SwipeItems`類型，代表當控制項從上而下撥動時，可以叫用的滑動專案。
- `BottomItems`，屬於 `SwipeItems`類型，代表當控制項從下撥動時，可以叫用的滑動專案。

這些屬性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件所支援，這表示它們可以是資料系結的目標，以及樣式化的。

此外，`SwipeView` 會從[`ContentView`](xref:Xamarin.Forms.ContentView)類別繼承[`Content`](xref:Xamarin.Forms.ContentView.Content)屬性。 `Content` 屬性是 `SwipeView` 類別的 content 屬性，因此不需要明確設定。

`SwipeView` 類別也會定義四個事件：

- 當滑動開始時，就會引發 `SwipeStarted`。 隨附于此事件的 `SwipeStartedEventArgs` 物件具有 `SwipeDirection` 屬性，其類型為 `SwipeDirection`。
- 當滑動移動時，會引發 `SwipeChanging`。 隨附于此事件的 `SwipeChangingEventArgs` 物件具有 `SwipeDirection` 屬性，其類型為 `SwipeDirection`，而 `Offset` 屬性類型為 `double`。
- 當滑動結束時，就會引發 `SwipeEnded`。 隨附于此事件的 `SwipeEndedEventArgs` 物件具有 `SwipeDirection` 屬性，其類型為 `SwipeDirection`。
- 當關閉滑動專案時，就會引發 `CloseRequested`。

此外，`SwipeView` 會定義 `Close` 方法，以關閉滑動專案。

> [!NOTE]
> `SwipeView` 具有 iOS 和 Android 上的平臺特定，可控制開啟 `SwipeView`時所使用的轉換。 如需詳細資訊，請參閱[iOS 上的 SwipeView 滑動轉換模式](~/xamarin-forms/platform/ios/swipeview-swipetransitionmode.md)和[Android 上的 SwipeView 滑動切換模式](~/xamarin-forms/platform/android/swipeview-swipetransitionmode.md)。

## <a name="create-a-swipeview"></a>建立 SwipeView

`SwipeView` 必須定義 `SwipeView` 包裝的內容，以及滑動手勢所顯示的滑動專案。 [滑動專案] 是放在四個 `SwipeView` 方向集合之一的一或多個 `SwipeItem` 物件-`LeftItems`、`RightItems`、`TopItems`或 `BottomItems`。

下列範例顯示如何在 XAML 中具現化 `SwipeView`：

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems>
            <SwipeItem Text="Favorite"
                       IconImageSource="favorite.png"
                       BackgroundColor="LightGreen"
                       Invoked="OnFavoriteSwipeItemInvoked" />
            <SwipeItem Text="Delete"
                       IconImageSource="delete.png"
                       BackgroundColor="LightPink"
                       Invoked="OnDeleteSwipeItemInvoked" />
        </SwipeItems>
    </SwipeView.LeftItems>
    <!-- Content -->
    <Grid HeightRequest="60"
          WidthRequest="300"
          BackgroundColor="LightGray">
        <Label Text="Swipe right"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
    </Grid>
</SwipeView>
```

對等的 C# 程式碼為：

```csharp
// SwipeItems
SwipeItem favoriteSwipeItem = new SwipeItem
{
    Text = "Favorite",
    IconImageSource = "favorite.png",
    BackgroundColor = Color.LightGreen
};
favoriteSwipeItem.Invoked += OnFavoriteSwipeItemInvoked;

SwipeItem deleteSwipeItem = new SwipeItem
{
    Text = "Delete",
    IconImageSource = "delete.png",
    BackgroundColor = Color.LightPink
};
deleteSwipeItem.Invoked += OnDeleteSwipeItemInvoked;

List<SwipeItem> swipeItems = new List<SwipeItem>() { favoriteSwipeItem, deleteSwipeItem };

// SwipeView content
Grid grid = new Grid
{
    HeightRequest = 60,
    WidthRequest = 300,
    BackgroundColor = Color.LightGray
};
grid.Children.Add(new Label
{
    Text = "Swipe right",
    HorizontalOptions = LayoutOptions.Center,
    VerticalOptions = LayoutOptions.Center
});

SwipeView swipeView = new SwipeView
{
    LeftItems = new SwipeItems(swipeItems),
    Content = grid
};
```

在此範例中，`SwipeView` 內容是包含[`Label`](xref:Xamarin.Forms.Label)的[`Grid`](xref:Xamarin.Forms.Grid) ：

[![SwipeView 內容（在 iOS 和 Android 上）的螢幕擷取畫面](swipeview-images/swipeview-content.png "SwipeView 內容")](swipeview-images/swipeview-content-large.png#lightbox "SwipeView 內容")

[滑動] 專案是用來對 `SwipeView` 內容執行動作，並在從左側撥動控制項時顯示：

[![SwipeView 的螢幕擷取畫面： iOS 和 Android 上的滑動專案](swipeview-images/swipeview-swipeitems.png "SwipeView 滑動專案")](swipeview-images/swipeview-swipeitems-large.png#lightbox "SwipeView 滑動專案")

根據預設，當使用者對它進行點擊時，會執行滑動專案。 不過，可以變更此行為。 如需詳細資訊，請參閱[滑動模式](#swipe-mode)。

一旦執行了滑動專案，就會隱藏滑動專案，並重新顯示 `SwipeView` 的內容。 不過，可以變更此行為。 如需詳細資訊，請參閱[滑動行為](#swipe-behavior)。

> [!NOTE]
> 滑動內容和滑動專案可以內嵌或定義為資源。

## <a name="swipe-items"></a>將專案滑動

`LeftItems`、`RightItems`、`TopItems`和 `BottomItems` 集合都是 `SwipeItems`類型。 `SwipeItems` 類別會定義下列屬性：

- `Mode`，屬於 `SwipeMode`類型，表示滑動互動的效果。 如需滑動模式的詳細資訊，請參閱[滑動模式](#swipe-mode)。
- `SwipeBehaviorOnInvoked`，屬於 `SwipeBehaviorOnInvoked`類型，表示叫用滑動專案之後 `SwipeView` 的行為。 如需有關滑動行為的詳細資訊，請參閱[滑動行為](#swipe-behavior)。

這些屬性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件所支援，這表示它們可以是資料系結的目標，以及樣式化的。

每個輕量專案都會定義為一個 `SwipeItem` 物件，而此物件會放入四個 `SwipeItems` 方向集合中的其中一個。 `SwipeItem` 類別衍生自[`MenuItem`](xref:Xamarin.Forms.MenuItem)類別，並新增下列成員：

- `Color`類型的 `BackgroundColor` 屬性，可定義滑動專案的背景色彩。 這個屬性是由可系結的屬性所支援。
- 執行輕刷專案時所引發的 `Invoked` 事件。

> [!IMPORTANT]
> [`MenuItem`](xref:Xamarin.Forms.MenuItem)類別定義數個屬性，包括 `Command`、`CommandParameter`、`IconImageSource`和 `Text`。 這些屬性可以在 `SwipeItem` 物件上設定，以定義其外觀，以及定義叫用 [輕刷] 專案時所執行的 `ICommand`。 如需詳細資訊，請參閱[Xamarin. 表單 MenuItem](~/xamarin-forms/user-interface/menuitem.md)。

下列範例顯示 `SwipeView``LeftItems` 集合中的兩個 `SwipeItem` 物件：

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems>
            <SwipeItem Text="Favorite"
                       IconImageSource="favorite.png"
                       BackgroundColor="LightGreen"
                       Invoked="OnFavoriteSwipeItemInvoked" />
            <SwipeItem Text="Delete"
                       IconImageSource="delete.png"
                       BackgroundColor="LightPink"
                       Invoked="OnDeleteSwipeItemInvoked" />
        </SwipeItems>
    </SwipeView.LeftItems>
    <!-- Content -->
</SwipeView>
```

每個 `SwipeItem` 的外觀都是由 `Text`、`IconImageSource`和 `BackgroundColor` 屬性的組合所定義：

[![SwipeView 的螢幕擷取畫面： iOS 和 Android 上的滑動專案](swipeview-images/swipeview-swipeitems.png "SwipeView 滑動專案")](swipeview-images/swipeview-swipeitems-large.png#lightbox "SwipeView 滑動專案")

當您按下 `SwipeItem` 時，它的 `Invoked` 事件會引發，並由其註冊的事件處理常式來處理。 或者，`Command` 屬性可以設定為在叫用 `SwipeItem` 時將執行的 `ICommand` 實。

> [!NOTE]
> 當 `SwipeItem` 的外觀僅使用 `Text` 或 `IconImageSource` 屬性來定義時，內容一律會置中。

除了將 [滑動專案] 定義為 `SwipeItem` 物件之外，也可以定義自訂的滑動專案視圖。 如需詳細資訊，請參閱[自訂滑動專案](#custom-swipe-items)。

## <a name="swipe-direction"></a>滑動方向

`SwipeView` 支援四種不同的滑動方向，其中的滑動方向是由 `SwipeItem` 物件加入至的方向 `SwipeItems` 集合所定義。 每個輕量方向都可以包含自己的滑動專案。 例如，下列範例顯示的 `SwipeView`，其輕刷專案取決於滑動方向：

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems>
            <SwipeItem Text="Delete"
                       IconImageSource="delete.png"
                       BackgroundColor="LightPink"
                       Command="{Binding DeleteCommand}" />
        </SwipeItems>
    </SwipeView.LeftItems>
    <SwipeView.RightItems>
        <SwipeItems>
            <SwipeItem Text="Favorite"
                       IconImageSource="favorite.png"
                       BackgroundColor="LightGreen"
                       Command="{Binding FavoriteCommand}" />
            <SwipeItem Text="Share"
                       IconImageSource="share.png"
                       BackgroundColor="LightYellow"
                       Command="{Binding ShareCommand}" />
        </SwipeItems>
    </SwipeView.RightItems>
    <!-- Content -->
</SwipeView>
```

在此範例中，`SwipeView` 內容可以是向右或向左撥動。 向右輕刷會顯示 [**刪除**] 滑動專案，而向左輕刷會顯示 [我的**最愛**] 和 [**共用**] 滑動專案。

> [!WARNING]
> 在 `SwipeView`上，一次只能設定一個方向 `SwipeItems` 集合的實例。 因此，您在 `SwipeView`上不能有兩個 `LeftItems` 定義。

`SwipeStarted`、`SwipeChanging`和 `SwipeEnded` 事件會透過事件引數中的 `SwipeDirection` 屬性來報告滑動方向。 這個屬性的類型是 `SwipeDirection`，這是由四個成員組成的列舉型別（enumeration）：

- `Right` 表示已發生向右滑動。
- `Left` 表示發生左側的滑動。
- `Up` 表示已發生向上滑動。
- `Down` 表示已發生向下滑動。

## <a name="swipe-mode"></a>滑動模式

`SwipeItems` 類別具有 `Mode` 屬性，表示滑動互動的效果。 這個屬性應該設定為其中一個 `SwipeMode` 列舉成員：

- `Reveal` 表示輕刷會顯示滑動專案。 此為 `SwipeItems.Mode` 屬性的預設值。
- `Execute` 表示輕刷會執行滑動專案。

在 [顯示] 模式中，使用者會撥動 `SwipeView` 來開啟包含一個或多個滑動專案的功能表，而且必須明確地按下滑動專案來執行它。 執行滑動專案之後，會關閉滑動專案，並重新顯示 `SwipeView` 的內容。 在 [執行] 模式中，使用者會撥動 `SwipeView` 來開啟功能表，其中包含多個滑動專案，然後會自動執行。 執行之後，會關閉滑動專案，並重新顯示 `SwipeView` 的內容。

下列範例顯示設定為使用執行模式的 `SwipeView`：

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems Mode="Execute">
            <SwipeItem Text="Delete"
                       IconImageSource="delete.png"
                       BackgroundColor="LightPink"
                       Command="{Binding DeleteCommand}" />
        </SwipeItems>
    </SwipeView.LeftItems>
    <!-- Content -->
</SwipeView>
```

在此範例中，`SwipeView` 內容可以是撥動許可權，以顯示立即執行的滑動專案。 執行之後，會重新顯示 `SwipeView` 內容。

## <a name="swipe-behavior"></a>滑動行為

`SwipeItems` 類別具有 `SwipeBehaviorOnInvoked` 屬性，表示叫用滑動專案之後，`SwipeView` 的行為。 這個屬性應該設定為其中一個 `SwipeBehaviorOnInvoked` 列舉成員：

- `Auto` 表示在「顯示模式」中，`SwipeView` 在叫用滑動專案之後關閉，而且在執行模式中，在叫用滑動專案之後，`SwipeView` 會保持開啟狀態。 此為 `SwipeItems.SwipeBehaviorOnInvoked` 屬性的預設值。
- `Close` 表示在叫用滑動專案之後，`SwipeView` 關閉。
- `RemainOpen` 表示叫用滑動專案之後，`SwipeView` 保持開啟狀態。

下列範例顯示設定為在叫用滑動專案之後維持開啟狀態的 `SwipeView`：

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems SwipeBehaviorOnInvoked="RemainOpen">
            <SwipeItem Text="Favorite"
                       IconImageSource="favorite.png"
                       BackgroundColor="LightGreen"
                       Invoked="OnFavoriteSwipeItemInvoked" />
            <SwipeItem Text="Delete"
                       IconImageSource="delete.png"
                       BackgroundColor="LightPink"
                       Invoked="OnDeleteSwipeItemInvoked" />
        </SwipeItems>
    </SwipeView.LeftItems>
    <!-- Content -->
</SwipeView>
```

## <a name="custom-swipe-items"></a>自訂滑動專案

您可以使用 `SwipeItemView` 類型來定義自訂的滑動專案。 `SwipeItemView` 類別衍生自[`ContentView`](xref:Xamarin.Forms.ContentView)類別，並加入下列屬性：

- `Command`，屬於 `ICommand`類型，這是在點擊滑動專案時執行。
- `CommandParameter`，屬於 `object` 類型，這是傳遞至 `Command` 的參數。

這些屬性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件所支援，這表示它們可以是資料系結的目標，以及樣式化的。

`SwipeItemView` 類別也會定義在執行 `Command` 之後，`Invoked` 專案時所引發的事件。

下列範例顯示 `SwipeView`的 `LeftItems` 集合中的 `SwipeItemView` 物件：

```xaml
<SwipeView>
    <SwipeView.LeftItems>
        <SwipeItems>
            <SwipeItemView Command="{Binding CheckAnswerCommand}"
                           CommandParameter="{Binding Source={x:Reference resultEntry}, Path=Text}">
                <StackLayout Margin="10"
                             WidthRequest="300">
                    <Entry x:Name="resultEntry"
                           Placeholder="Enter answer"
                           HorizontalOptions="CenterAndExpand" />
                    <Label Text="Check"
                           FontAttributes="Bold"
                           HorizontalOptions="Center" />
                </StackLayout>
            </SwipeItemView>
        </SwipeItems>
    </SwipeView.LeftItems>
    <!-- Content -->
</SwipeView>
```

在此範例中，`SwipeItemView` 由包含[`Entry`](xref:Xamarin.Forms.Entry)和[`Label`](xref:Xamarin.Forms.Label)的[`StackLayout`](xref:Xamarin.Forms.StackLayout)組成。 在使用者輸入 `Entry`的輸入之後，`SwipeViewItem` 的其餘部分可執行 `SwipeItemView.Command` 屬性所定義的 `ICommand`。

## <a name="disable-a-swipeview"></a>停用 SwipeView

應用程式可能會進入一種狀態，其中的內容專案不是有效的作業。 在這種情況下，`SwipeView` 可以藉由將其 `IsEnabled` 屬性設定為 `false`來停用。 這會讓使用者無法將內容滑動以顯示滑動專案。

此外，當定義 `SwipeItem` 或 `SwipeItemView`的 `Command` 屬性時，可以指定 `ICommand` 的 `CanExecute` 委派來啟用或停用滑動專案。

## <a name="related-links"></a>相關連結

- [SwipeView （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-swipeviewdemos/)
- [Xamarin.Forms MenuItem](~/xamarin-forms/user-interface/menuitem.md)
