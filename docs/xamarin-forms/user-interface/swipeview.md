---
title： " Xamarin.Forms SwipeView" 描述： " Xamarin.Forms SwipeView 是包裝內容專案的容器控制項，並提供滑動手勢所顯示的操作功能表項目。
assetId： 602456B5-701B-4948-B454-B1F31283F1CF ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. date： 03/26/2020 no-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="xamarinforms-swipeview"></a>Xamarin.FormsSwipeView

![](~/media/shared/preview.png "This API is currently pre-release")

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-swipeviewdemos/)

`SwipeView`是包裝內容專案的容器控制項，可提供滑動手勢所顯示的操作功能表項目：

[![在 iOS 和 Android 上 CollectionView 中 SwipeView 的滑動專案螢幕擷取畫面](swipeview-images/swipeview-collectionview.png "SwipeView 滑動專案")](swipeview-images/swipeview-collectionview-large.png#lightbox "SwipeView 滑動專案")

`SwipeView`可在4.4 中取得 Xamarin.Forms 。 不過，它目前是實驗性，而且只能透過將下列程式程式碼新增至您的 `AppDelegate` iOS、Android 上的類別 `MainActivity` ，或在 UWP 上的類別 `App` 中，然後再呼叫 `Forms.Init` ：

```csharp
Forms.SetFlags("SwipeView_Experimental");
```

`SwipeView` 會定義下列屬性：

- `LeftItems`，屬於類型 `SwipeItems` ，表示當控制項從左側撥動時，可以叫用的滑動專案。
- `RightItems`，屬於類型 `SwipeItems` ，表示當控制項從右邊撥動時，可以叫用的滑動專案。
- `TopItems`，屬於類型 `SwipeItems` ，表示當控制項從上而下撥動時，可以叫用的滑動專案。
- `BottomItems`，屬於類型 `SwipeItems` ，表示當控制項從下撥動時，可以叫用的滑動專案。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標，以及樣式化的。

此外，會 `SwipeView` [`Content`](xref:Xamarin.Forms.ContentView.Content) 從類別繼承屬性 [`ContentView`](xref:Xamarin.Forms.ContentView) 。 `Content`屬性是類別的 content 屬性 `SwipeView` ，因此不需要明確設定。

`SwipeView`類別也會定義四個事件：

- `SwipeStarted`當刷開始時，就會引發。 `SwipeStartedEventArgs`伴隨此事件的物件具有 `SwipeDirection` 類型的屬性 `SwipeDirection` 。
- `SwipeChanging`會隨著滑動移動而引發。 `SwipeChangingEventArgs`伴隨此事件的物件具有 `SwipeDirection` 類型的屬性， `SwipeDirection` 以及 `Offset` 類型的屬性 `double` 。
- `SwipeEnded`當滑動結束時，就會引發。 `SwipeEndedEventArgs`伴隨此事件的物件具有 `SwipeDirection` 類型的屬性 `SwipeDirection` 。
- `CloseRequested`當關閉滑動專案時，就會引發。

此外， `SwipeView` 包含 `Open` 和 `Close` 方法，分別以程式設計方式開啟和關閉滑動專案。

> [!NOTE]
> `SwipeView`具有 iOS 和 Android 上的平臺特定，可控制開啟時所使用的轉換 `SwipeView` 。 如需詳細資訊，請參閱[iOS 上的 SwipeView 滑動轉換模式](~/xamarin-forms/platform/ios/swipeview-swipetransitionmode.md)和[Android 上的 SwipeView 滑動切換模式](~/xamarin-forms/platform/android/swipeview-swipetransitionmode.md)。

## <a name="create-a-swipeview"></a>建立 SwipeView

`SwipeView`必須定義 `SwipeView` 要環繞的內容，以及滑動手勢所顯示的滑動專案。 [滑動] 專案是 `SwipeItem` 放在四個方向集合之一的一個或多個物件 `SwipeView` ： `LeftItems` 、 `RightItems` 、 `TopItems` 或 `BottomItems` 。

下列範例顯示如何 `SwipeView` 在 XAML 中具現化：

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

在此範例中， `SwipeView` 內容是 [`Grid`](xref:Xamarin.Forms.Grid) 包含的 [`Label`](xref:Xamarin.Forms.Label) ：

[![SwipeView 內容（在 iOS 和 Android 上）的螢幕擷取畫面](swipeview-images/swipeview-content.png "SwipeView 內容")](swipeview-images/swipeview-content-large.png#lightbox "SwipeView 內容")

[滑動] 專案是用來對內容執行動作 `SwipeView` ，並在從左側撥動控制項時顯示：

[![SwipeView 的螢幕擷取畫面： iOS 和 Android 上的滑動專案](swipeview-images/swipeview-swipeitems.png "SwipeView 滑動專案")](swipeview-images/swipeview-swipeitems-large.png#lightbox "SwipeView 滑動專案")

根據預設，當使用者對它進行點擊時，會執行滑動專案。 不過，可以變更此行為。 如需詳細資訊，請參閱[滑動模式](#swipe-mode)。

一旦執行了滑動專案，就會隱藏滑動專案並 `SwipeView` 重新顯示內容。 不過，可以變更此行為。 如需詳細資訊，請參閱[滑動行為](#swipe-behavior)。

> [!NOTE]
> 滑動內容和滑動專案可以內嵌或定義為資源。

## <a name="swipe-items"></a>將專案滑動

`LeftItems`、 `RightItems` 、 `TopItems` 和 `BottomItems` 集合都是型別 `SwipeItems` 。 `SwipeItems`類別會定義下列屬性：

- `Mode`，屬於類型 `SwipeMode` ，表示滑動互動的效果。 如需滑動模式的詳細資訊，請參閱[滑動模式](#swipe-mode)。
- `SwipeBehaviorOnInvoked`，屬於類型 `SwipeBehaviorOnInvoked` ，表示叫用 `SwipeView` 滑動專案之後的行為。 如需有關滑動行為的詳細資訊，請參閱[滑動行為](#swipe-behavior)。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標，以及樣式化的。

每個輕量專案都會定義為 `SwipeItem` 放入四個方向集合之一的物件 `SwipeItems` 。 `SwipeItem`類別衍生自 [`MenuItem`](xref:Xamarin.Forms.MenuItem) 類別，並新增下列成員：

- `BackgroundColor`類型的屬性， `Color` 定義滑動專案的背景色彩。 這個屬性是由可系結的屬性所支援。
- `Invoked`當執行輕刷專案時所引發的事件。

> [!IMPORTANT]
> [`MenuItem`](xref:Xamarin.Forms.MenuItem)類別會定義數個屬性，包括 `Command` 、 `CommandParameter` 、 `IconImageSource` 和 `Text` 。 您可以在物件上設定這些屬性 `SwipeItem` 來定義其外觀，以及定義在叫用 `ICommand` 滑動專案時執行的。 如需詳細資訊，請參閱[ Xamarin.Forms MenuItem](~/xamarin-forms/user-interface/menuitem.md)。

下列範例會顯示集合中的兩個 `SwipeItem` 物件 `LeftItems` `SwipeView` ：

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

每個的外觀 `SwipeItem` 都是由 `Text` 、和屬性的組合所定義 `IconImageSource` `BackgroundColor` ：

[![SwipeView 的螢幕擷取畫面： iOS 和 Android 上的滑動專案](swipeview-images/swipeview-swipeitems.png "SwipeView 滑動專案")](swipeview-images/swipeview-swipeitems-large.png#lightbox "SwipeView 滑動專案")

當按 `SwipeItem` 下時，其 `Invoked` 事件就會引發，並由其註冊的事件處理常式來處理。 或者， `Command` 可以將屬性設定為在叫 `ICommand` 用時執行的實作為 `SwipeItem` 。

> [!NOTE]
> 當的外觀 `SwipeItem` 只使用或屬性來定義時 `Text` `IconImageSource` ，內容一律會置中。

除了將「滑動專案」定義為 `SwipeItem` 物件之外，也可以定義自訂的滑動專案視圖。 如需詳細資訊，請參閱[自訂滑動專案](#custom-swipe-items)。

## <a name="swipe-direction"></a>滑動方向

`SwipeView`支援四種不同的輕量方向，其中的滑動方向是由 `SwipeItems` 物件新增至的方向集合所定義 `SwipeItem` 。 每個輕量方向都可以包含自己的滑動專案。 例如，下列範例顯示的是， `SwipeView` 其輕刷專案取決於滑動方向：

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

在此範例中， `SwipeView` 內容可以是向右或向左撥動。 向右輕刷會顯示 [**刪除**] 滑動專案，而向左輕刷會顯示 [我的**最愛**] 和 [**共用**] 滑動專案。

> [!WARNING]
> 在上，一次只能設定一個方向性集合的實例 `SwipeItems` `SwipeView` 。 因此，上不能有兩個 `LeftItems` 定義 `SwipeView` 。

`SwipeStarted`、 `SwipeChanging` 和事件會透過 `SwipeEnded` `SwipeDirection` 事件引數中的屬性來報告滑動方向。 這個屬性的類型是 `SwipeDirection` ，它是由四個成員組成的列舉型別（enumeration）：

- `Right`表示已發生向右滑動。
- `Left`表示已發生左側的刷。
- `Up`表示已發生向上滑動。
- `Down`表示已發生向下滑動。

## <a name="swipe-mode"></a>滑動模式

`SwipeItems`類別具有 `Mode` 屬性，表示滑動互動的效果。 這個屬性應該設定為其中一個 `SwipeMode` 列舉成員：

- `Reveal`表示滑動會顯示滑動專案。 此為 `SwipeItems.Mode` 屬性的預設值。
- `Execute`表示輕刷會執行滑動專案。

在 [顯示] 模式中，使用者會撥動 `SwipeView` 以開啟包含一個或多個滑動專案的功能表，而且必須明確地按下滑動專案來執行。 執行滑動專案之後，會關閉滑動專案並 `SwipeView` 重新顯示內容。 在執行模式中，使用者會撥動 `SwipeView` 以開啟包含一個更多滑動專案的功能表，然後自動執行。 執行後，會關閉滑動專案，並 `SwipeView` 重新顯示內容。

下列範例顯示 `SwipeView` 設定為使用執行模式的：

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

在此範例中， `SwipeView` 內容可以是撥動許可權，以顯示立即執行的滑動專案。 執行後， `SwipeView` 內容會重新顯示。

## <a name="swipe-behavior"></a>滑動行為

`SwipeItems`類別具有 `SwipeBehaviorOnInvoked` 屬性，表示叫用 `SwipeView` 滑動專案之後的行為。 這個屬性應該設定為其中一個 `SwipeBehaviorOnInvoked` 列舉成員：

- `Auto`表示在 [顯示] 模式中，會在叫 `SwipeView` 用滑動專案之後關閉，而在 [執行] 模式中，則會在叫 `SwipeView` 用滑動專案之後維持開啟狀態。 此為 `SwipeItems.SwipeBehaviorOnInvoked` 屬性的預設值。
- `Close`表示在叫 `SwipeView` 用滑動專案之後關閉。
- `RemainOpen`表示叫 `SwipeView` 用滑動專案之後，會保持開啟狀態。

下列範例顯示 `SwipeView` 已設定為在叫用滑動專案之後維持開啟狀態的：

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

自訂的滑動專案可以使用類型來定義 `SwipeItemView` 。 `SwipeItemView`類別衍生自 [`ContentView`](xref:Xamarin.Forms.ContentView) 類別，並加入下列屬性：

- `Command`，屬於類型 `ICommand` ，這是在點擊滑動專案時執行的。
- `CommandParameter`，屬於 `object` 類型，這是傳遞至 `Command` 的參數。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標，以及樣式化的。

`SwipeItemView`類別也會定義在 `Invoked` 執行之後，按下專案時所引發的事件 `Command` 。

下列範例會顯示 `SwipeItemView` 集合中的物件 `LeftItems` `SwipeView` ：

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

在此範例中， `SwipeItemView` 由組成， [`StackLayout`](xref:Xamarin.Forms.StackLayout) 其中包含 [`Entry`](xref:Xamarin.Forms.Entry) 和 [`Label`](xref:Xamarin.Forms.Label) 。 當使用者在中輸入輸入之後 `Entry` ，可以按下的其餘部分， `SwipeViewItem` 以執行 `ICommand` 屬性所定義的 `SwipeItemView.Command` 。

## <a name="open-and-close-a-swipeview-programmatically"></a>以程式設計方式開啟和關閉 SwipeView

`SwipeView`包含 `Open` 和 `Close` 方法，分別以程式設計方式開啟和關閉滑動專案。

`Open`方法需要 `OpenSwipeItem` 引數，以指定將開啟的方向 `SwipeView` 。 `OpenSwipeItem`列舉有四個成員：

- `LeftItems`，表示 `SwipeView` 將從左邊開啟，以顯示集合中的滑動專案 `LeftItems` 。
- `TopItems`，表示 `SwipeView` 將從頂端開啟，以顯示集合中的滑動專案 `TopItems` 。
- `RightItems`，表示 `SwipeView` 將從右邊開啟，以顯示集合中的滑動專案 `RightItems` 。
- `BottomItems`，表示 `SwipeView` 將從底部開啟，以顯示集合中的滑動專案 `BottomItems` 。

在指定 `SwipeView` 名為的 `swipeView` 之後，下列範例會示範如何開啟， `SwipeView` 以顯示集合中的滑動專案 `LeftItems` ：

```csharp
swipeView.Open(OpenSwipeItem.LeftItems);
```

`swipeView`然後，可以使用方法來關閉 `Close` ：

```csharp
swipeView.Close();
```

> [!NOTE]
> 叫用 `Close` 方法時， `CloseRequested` 就會引發事件。

## <a name="disable-a-swipeview"></a>停用 SwipeView

應用程式可能會進入一種狀態，其中的內容專案不是有效的作業。 在這種情況下， `SwipeView` 可以藉由將其 `IsEnabled` 屬性設為來停用 `false` 。 這會讓使用者無法將內容滑動以顯示滑動專案。

此外，當定義或的 `Command` 屬性時 `SwipeItem` `SwipeItemView` ， `CanExecute` `ICommand` 可以指定的委派來啟用或停用滑動專案。

## <a name="related-links"></a>相關連結

- [SwipeView （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-swipeviewdemos/)
- [Xamarin.FormsMenuItem](~/xamarin-forms/user-interface/menuitem.md)
