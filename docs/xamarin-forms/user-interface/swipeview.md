---
title: Xamarin. Forms SwipeView
description: SwipeView 是一個容器控制項，它會環繞內容專案，並提供滑動手勢所顯示的操作功能表項目。
ms.prod: xamarin
ms.assetId: 602456B5-701B-4948-B454-B1F31283F1CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/26/2020
ms.openlocfilehash: da6dbe63b7151ef0f9a1defca66fbb3abb25ad1d
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517545"
---
# <a name="xamarinforms-swipeview"></a>Xamarin. Forms SwipeView

![](~/media/shared/preview.png "This API is currently pre-release")

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-swipeviewdemos/)

`SwipeView`是包裝內容專案的容器控制項，可提供滑動手勢所顯示的操作功能表項目：

[![在 iOS 和 Android 上 CollectionView 中 SwipeView 的滑動專案螢幕擷取畫面](swipeview-images/swipeview-collectionview.png "SwipeView 滑動專案")](swipeview-images/swipeview-collectionview-large.png#lightbox "SwipeView 滑動專案")

`SwipeView`適用于 Xamarin. 表單4.4。 不過，它目前是實驗性，而且只能透過將下列程式程式碼`AppDelegate`新增至您的 IOS、 `MainActivity` Android 上的類別，或`App`在 UWP 上的類別中，然後再呼叫： `Forms.Init`

```csharp
Forms.SetFlags("SwipeView_Experimental");
```

`SwipeView` 會定義下列屬性：

- `LeftItems`，屬於類型`SwipeItems`，表示當控制項從左側撥動時，可以叫用的滑動專案。
- `RightItems`，屬於類型`SwipeItems`，表示當控制項從右邊撥動時，可以叫用的滑動專案。
- `TopItems`，屬於類型`SwipeItems`，表示當控制項從上而下撥動時，可以叫用的滑動專案。
- `BottomItems`，屬於類型`SwipeItems`，表示當控制項從下撥動時，可以叫用的滑動專案。

這些屬性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件所支援，這表示它們可以是資料系結的目標，以及樣式化的。

此外，會從`SwipeView` [`ContentView`](xref:Xamarin.Forms.ContentView)類別繼承[`Content`](xref:Xamarin.Forms.ContentView.Content)屬性。 `Content`屬性是`SwipeView`類別的 content 屬性，因此不需要明確設定。

`SwipeView`類別也會定義四個事件：

- `SwipeStarted`當刷開始時，就會引發。 伴隨`SwipeStartedEventArgs`此事件的物件具有類型`SwipeDirection` `SwipeDirection`的屬性。
- `SwipeChanging`會隨著滑動移動而引發。 伴隨`SwipeChangingEventArgs`此事件的`SwipeDirection`物件具有類型`SwipeDirection`的屬性，以及類型`Offset` `double`的屬性。
- `SwipeEnded`當滑動結束時，就會引發。 伴隨`SwipeEndedEventArgs`此事件的物件具有類型`SwipeDirection` `SwipeDirection`的屬性。
- `CloseRequested`當關閉滑動專案時，就會引發。

此外， `SwipeView`包含`Open`和`Close`方法，分別以程式設計方式開啟和關閉滑動專案。

> [!NOTE]
> `SwipeView`具有 iOS 和 Android 上的平臺特定，可控制開啟時所使用的轉換`SwipeView`。 如需詳細資訊，請參閱[iOS 上的 SwipeView 滑動轉換模式](~/xamarin-forms/platform/ios/swipeview-swipetransitionmode.md)和[Android 上的 SwipeView 滑動切換模式](~/xamarin-forms/platform/android/swipeview-swipetransitionmode.md)。

## <a name="create-a-swipeview"></a>建立 SwipeView

`SwipeView`必須定義要`SwipeView`環繞的內容，以及滑動手勢所顯示的滑動專案。 [滑動] 專案是放在`SwipeItem`四個`SwipeView`方向集合之一的一個或多個物件： `LeftItems`、 `RightItems`、 `TopItems`或`BottomItems`。

下列範例顯示如何`SwipeView`在 XAML 中具現化：

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

在此範例中， `SwipeView`內容是[`Grid`](xref:Xamarin.Forms.Grid)包含的[`Label`](xref:Xamarin.Forms.Label)：

[![SwipeView 內容（在 iOS 和 Android 上）的螢幕擷取畫面](swipeview-images/swipeview-content.png "SwipeView 內容")](swipeview-images/swipeview-content-large.png#lightbox "SwipeView 內容")

[滑動] 專案是用來對`SwipeView`內容執行動作，並在從左側撥動控制項時顯示：

[![SwipeView 的螢幕擷取畫面： iOS 和 Android 上的滑動專案](swipeview-images/swipeview-swipeitems.png "SwipeView 滑動專案")](swipeview-images/swipeview-swipeitems-large.png#lightbox "SwipeView 滑動專案")

根據預設，當使用者對它進行點擊時，會執行滑動專案。 不過，可以變更此行為。 如需詳細資訊，請參閱[滑動模式](#swipe-mode)。

一旦執行了滑動專案，就會隱藏滑動專案並重新顯示`SwipeView`內容。 不過，可以變更此行為。 如需詳細資訊，請參閱[滑動行為](#swipe-behavior)。

> [!NOTE]
> 滑動內容和滑動專案可以內嵌或定義為資源。

## <a name="swipe-items"></a>將專案滑動

`LeftItems`、 `RightItems`、 `TopItems`和`BottomItems`集合都是型`SwipeItems`別。 `SwipeItems`類別會定義下列屬性：

- `Mode`，屬於類型`SwipeMode`，表示滑動互動的效果。 如需滑動模式的詳細資訊，請參閱[滑動模式](#swipe-mode)。
- `SwipeBehaviorOnInvoked`，屬於類型`SwipeBehaviorOnInvoked`，表示叫用滑動`SwipeView`專案之後的行為。 如需有關滑動行為的詳細資訊，請參閱[滑動行為](#swipe-behavior)。

這些屬性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件所支援，這表示它們可以是資料系結的目標，以及樣式化的。

每個輕量專案都會定義`SwipeItem`為放入四個`SwipeItems`方向集合之一的物件。 `SwipeItem`類別衍生自[`MenuItem`](xref:Xamarin.Forms.MenuItem)類別，並新增下列成員：

- 類型`BackgroundColor` `Color`的屬性，定義滑動專案的背景色彩。 這個屬性是由可系結的屬性所支援。
- 當`Invoked`執行輕刷專案時所引發的事件。

> [!IMPORTANT]
> [`MenuItem`](xref:Xamarin.Forms.MenuItem)類別會定義數個屬性， `Command`包括`CommandParameter`、 `IconImageSource`、和`Text`。 您可以在`SwipeItem`物件上設定這些屬性來定義其外觀，以及定義在叫`ICommand`用滑動專案時執行的。 如需詳細資訊，請參閱[Xamarin. 表單 MenuItem](~/xamarin-forms/user-interface/menuitem.md)。

下列範例會顯示`SwipeItem` `LeftItems`集合中的兩個物件： `SwipeView`

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

每個`SwipeItem`的外觀都是由`Text`、 `IconImageSource`和`BackgroundColor`屬性的組合所定義：

[![SwipeView 的螢幕擷取畫面： iOS 和 Android 上的滑動專案](swipeview-images/swipeview-swipeitems.png "SwipeView 滑動專案")](swipeview-images/swipeview-swipeitems-large.png#lightbox "SwipeView 滑動專案")

`SwipeItem`當按下時，其`Invoked`事件就會引發，並由其註冊的事件處理常式來處理。 或者，可以`Command`將屬性設定為在叫`ICommand`用時`SwipeItem`執行的實作為。

> [!NOTE]
> 當的外觀`SwipeItem`只使用`Text`或`IconImageSource`屬性來定義時，內容一律會置中。

除了將「滑動專案」定義`SwipeItem`為物件之外，也可以定義自訂的滑動專案視圖。 如需詳細資訊，請參閱[自訂滑動專案](#custom-swipe-items)。

## <a name="swipe-direction"></a>滑動方向

`SwipeView`支援四種不同的輕量方向，其中的滑動方向是由`SwipeItems` `SwipeItem`物件新增至的方向集合所定義。 每個輕量方向都可以包含自己的滑動專案。 例如，下列範例顯示的是`SwipeView` ，其輕刷專案取決於滑動方向：

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

在此範例中， `SwipeView`內容可以是向右或向左撥動。 向右輕刷會顯示 [**刪除**] 滑動專案，而向左輕刷會顯示 [我的**最愛**] 和 [**共用**] 滑動專案。

> [!WARNING]
> 在上，一次只能`SwipeItems`設定一個方向性集合的實例`SwipeView`。 因此，上不能有`LeftItems`兩個定義`SwipeView`。

`SwipeStarted`、 `SwipeChanging`和`SwipeEnded`事件會透過事件引數中的`SwipeDirection`屬性來報告滑動方向。 這個屬性的類型是`SwipeDirection`，它是由四個成員組成的列舉型別（enumeration）：

- `Right`表示已發生向右滑動。
- `Left`表示已發生左側的刷。
- `Up`表示已發生向上滑動。
- `Down`表示已發生向下滑動。

## <a name="swipe-mode"></a>滑動模式

`SwipeItems`類別具有`Mode`屬性，表示滑動互動的效果。 這個屬性應該設定為其中一個`SwipeMode`列舉成員：

- `Reveal`表示滑動會顯示滑動專案。 此為 `SwipeItems.Mode` 屬性的預設值。
- `Execute`表示輕刷會執行滑動專案。

在 [顯示] 模式中，使用者`SwipeView`會撥動以開啟包含一個或多個滑動專案的功能表，而且必須明確地按下滑動專案來執行。 執行滑動專案之後，會關閉滑動專案並重新顯示`SwipeView`內容。 在執行模式中，使用者會撥動`SwipeView`以開啟包含一個更多滑動專案的功能表，然後自動執行。 執行後，會關閉滑動專案，並重新`SwipeView`顯示內容。

下列範例顯示設定為`SwipeView`使用執行模式的：

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

在此範例中， `SwipeView`內容可以是撥動許可權，以顯示立即執行的滑動專案。 執行後， `SwipeView`內容會重新顯示。

## <a name="swipe-behavior"></a>滑動行為

`SwipeItems`類別具有`SwipeBehaviorOnInvoked`屬性，表示叫用滑動專案之後`SwipeView`的行為。 這個屬性應該設定為其中一個`SwipeBehaviorOnInvoked`列舉成員：

- `Auto`表示在 [ `SwipeView`顯示] 模式中，會在叫用滑動專案之後關閉，而在`SwipeView` [執行] 模式中，則會在叫用滑動專案之後維持開啟狀態。 此為 `SwipeItems.SwipeBehaviorOnInvoked` 屬性的預設值。
- `Close`表示在`SwipeView`叫用滑動專案之後關閉。
- `RemainOpen`表示叫用`SwipeView`滑動專案之後，會保持開啟狀態。

下列範例顯示已設定`SwipeView`為在叫用滑動專案之後維持開啟狀態的：

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

自訂的`SwipeItemView`滑動專案可以使用類型來定義。 `SwipeItemView`類別衍生自[`ContentView`](xref:Xamarin.Forms.ContentView)類別，並加入下列屬性：

- `Command`，屬於類型`ICommand`，這是在點擊滑動專案時執行的。
- `CommandParameter`，屬於 `object` 類型，這是傳遞至 `Command` 的參數。

這些屬性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件所支援，這表示它們可以是資料系結的目標，以及樣式化的。

`SwipeItemView`類別也會定義在`Invoked`執行之後`Command` ，按下專案時所引發的事件。

下列範例會顯示`SwipeItemView` `LeftItems`集合中的物件： `SwipeView`

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

在此範例中， `SwipeItemView`由組成[`StackLayout`](xref:Xamarin.Forms.StackLayout) ，其中[`Entry`](xref:Xamarin.Forms.Entry)包含和[`Label`](xref:Xamarin.Forms.Label)。 當使用者在中輸入輸入之後`Entry`， `SwipeViewItem`可以按下的其餘部分，以執行`ICommand` `SwipeItemView.Command`屬性所定義的。

## <a name="open-and-close-a-swipeview-programmatically"></a>以程式設計方式開啟和關閉 SwipeView

`SwipeView`包含`Open`和`Close`方法，分別以程式設計方式開啟和關閉滑動專案。

`Open`方法需要`OpenSwipeItem`引數，以指定`SwipeView`將開啟的方向。 `OpenSwipeItem`列舉有四個成員：

- `LeftItems`，表示`SwipeView`將從左邊開啟，以顯示`LeftItems`集合中的滑動專案。
- `TopItems`，表示`SwipeView`將從頂端開啟，以顯示`TopItems`集合中的滑動專案。
- `RightItems`，表示`SwipeView`將從右邊開啟，以顯示`RightItems`集合中的滑動專案。
- `BottomItems`，表示`SwipeView`將從底部開啟，以顯示`BottomItems`集合中的滑動專案。

在指定`SwipeView`名`swipeView`為的之後，下列範例會示範如何`SwipeView`開啟，以顯示`LeftItems`集合中的滑動專案：

```csharp
swipeView.Open(OpenSwipeItem.LeftItems);
```

然後`swipeView` ，可以使用`Close`方法來關閉：

```csharp
swipeView.Close();
```

> [!NOTE]
> 叫用`Close`方法時，就會`CloseRequested`引發事件。

## <a name="disable-a-swipeview"></a>停用 SwipeView

應用程式可能會進入一種狀態，其中的內容專案不是有效的作業。 在這種情況下`SwipeView` ，可以藉由將其`IsEnabled`屬性設`false`為來停用。 這會讓使用者無法將內容滑動以顯示滑動專案。

此外`Command` ，當定義`SwipeItem`或`SwipeItemView`的屬性時， `CanExecute` `ICommand`可以指定的委派來啟用或停用滑動專案。

## <a name="related-links"></a>相關連結

- [SwipeView （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-swipeviewdemos/)
- [Xamarin.Forms MenuItem](~/xamarin-forms/user-interface/menuitem.md)
