---
title: Xamarin.Forms SwipeView
description: Xamarin.FormsSwipeView 是包裝內容專案的容器控制項，並提供滑動手勢所顯示的內容功能表項目。
ms.prod: xamarin
ms.assetId: 602456B5-701B-4948-B454-B1F31283F1CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/11/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d0ebae93405cb115a0f1e87453ab9b438202ef30
ms.sourcegitcommit: 1decf2c65dc4c36513f7dd459a5df01e170a036f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98115245"
---
# <a name="no-locxamarinforms-swipeview"></a>Xamarin.Forms SwipeView

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-swipeviewdemos/)

`SwipeView`是包裝內容專案的容器控制項，並提供滑動手勢所顯示的快顯功能表項：

[![螢幕擷取畫面：在 iOS 和 Android 上，SwipeView 滑動 CollectionView 中的專案](swipeview-images/swipeview-collectionview.png "SwipeView 滑動專案")](swipeview-images/swipeview-collectionview-large.png#lightbox "SwipeView 滑動專案")

`SwipeView` 會定義下列屬性：

- `LeftItems`，代表在 `SwipeItems` 從左側撥動控制項時可叫用的滑動專案。
- `RightItems`， `SwipeItems` 代表可在從右邊撥動控制項時叫用的滑動專案。
- `TopItems`，代表在 `SwipeItems` 從上撥動控制項時可叫用的滑動專案。
- `BottomItems`， `SwipeItems` 代表可在從下撥動控制項時叫用的滑動專案。
- `Threshold`，代表與 `double` 裝置無關的單位數目，這些單位會觸發滑動手勢以完全顯示滑動專案。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標和樣式。

此外，會 `SwipeView` 繼承 [`Content`](xref:Xamarin.Forms.ContentView.Content) 類別中的屬性 [`ContentView`](xref:Xamarin.Forms.ContentView) 。 `Content`屬性是類別的 content 屬性 `SwipeView` ，因此不需要明確設定。

`SwipeView`類別也會定義三個事件：

- `SwipeStarted` 當滑動開始時引發。 `SwipeStartedEventArgs`伴隨這個事件的物件具有 `SwipeDirection` 類型的屬性 `SwipeDirection` 。
- `SwipeChanging` 會在滑動移動時引發。 `SwipeChangingEventArgs`伴隨這個事件的物件具有 `SwipeDirection` 屬性、類型為 `SwipeDirection` ，以及 `Offset` 類型的屬性 `double` 。
- `SwipeEnded` 當滑動結束時就會引發。 `SwipeEndedEventArgs`伴隨這個事件的物件具有 `SwipeDirection` 屬性、類型為 `SwipeDirection` ，以及 `IsOpen` 類型的屬性 `bool` 。

此外， `SwipeView` 包含 `Open` 和 `Close` 方法，分別以程式設計方式開啟和關閉滑動專案。

> [!NOTE]
> `SwipeView` 在 iOS 和 Android 上都有平臺特定，可控制開啟時使用的轉換 `SwipeView` 。 如需詳細資訊，請參閱 [iOS 上的 SwipeView 滑動轉換模式](~/xamarin-forms/platform/ios/swipeview-swipetransitionmode.md) 和 [Android 上的 SwipeView 滑動轉換模式](~/xamarin-forms/platform/android/swipeview-swipetransitionmode.md)。

## <a name="create-a-swipeview"></a>建立 SwipeView

A `SwipeView` 必須定義換行的內容 `SwipeView` ，以及滑動手勢所顯示的滑動專案。 滑動專案是一或多個 `SwipeItem` 放在四個 `SwipeView` 方向集合（ `LeftItems` 、 `RightItems` 、 `TopItems` 或）之一的物件 `BottomItems` 。

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

在此範例中， `SwipeView` 內容是 [`Grid`](xref:Xamarin.Forms.Grid) ，其中包含 [`Label`](xref:Xamarin.Forms.Label) ：

[![IOS 和 Android 上 SwipeView 內容的螢幕擷取畫面](swipeview-images/swipeview-content.png "SwipeView 內容")](swipeview-images/swipeview-content-large.png#lightbox "SwipeView 內容")

滑動專案是用來在內容上執行動作 `SwipeView` ，並且會在從左側撥動控制項時顯示：

[![螢幕擷取畫面： iOS 和 Android 上的 SwipeView 滑動專案](swipeview-images/swipeview-swipeitems.png "SwipeView 滑動專案")](swipeview-images/swipeview-swipeitems-large.png#lightbox "SwipeView 滑動專案")

依預設，當使用者按下時，會執行滑動專案。 不過，可以變更此行為。 如需詳細資訊，請參閱 [滑動模式](#swipe-mode)。

執行滑動專案之後，就會隱藏滑動專案並 `SwipeView` 重新顯示內容。 不過，可以變更此行為。 如需詳細資訊，請參閱 [滑動行為](#swipe-behavior)。

> [!NOTE]
> 滑動內容和滑動專案可以內嵌方式放置，或定義為資源。

## <a name="swipe-items"></a>滑動專案

`LeftItems`、、 `RightItems` `TopItems` 和 `BottomItems` 集合都是型別 `SwipeItems` 。 `SwipeItems`類別會定義下列屬性：

- `Mode`，類型為 `SwipeMode` ，表示滑動互動的效果。 如需滑動模式的詳細資訊，請參閱 [滑動模式](#swipe-mode)。
- `SwipeBehaviorOnInvoked`，類型為 `SwipeBehaviorOnInvoked` ，表示叫用 `SwipeView` 滑動專案之後的行為方式。 如需滑動行為的詳細資訊，請參閱 [滑動行為](#swipe-behavior)。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標和樣式。

每個滑動專案都會定義為一個 `SwipeItem` 物件，並放入四個方向集合的其中一個 `SwipeItems` 。 `SwipeItem`類別衍生自 [`MenuItem`](xref:Xamarin.Forms.MenuItem) 類別，並新增下列成員：

- 型別為 `BackgroundColor` 的屬性， `Color` 定義滑動專案的背景色彩。 這個屬性是由可系結屬性所支援。
- `Invoked`執行滑動專案時所引發的事件。

> [!IMPORTANT]
> [`MenuItem`](xref:Xamarin.Forms.MenuItem)類別會定義數個屬性，包括 `Command` 、 `CommandParameter` 、 `IconImageSource` 和 `Text` 。 您可以在物件上設定這些屬性 `SwipeItem` 來定義其外觀，以及定義叫用 `ICommand` 滑動專案時所執行的。 如需詳細資訊，請參閱[ Xamarin.Forms MenuItem](~/xamarin-forms/user-interface/menuitem.md)。

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

各項的外觀 `SwipeItem` 是由 `Text` 、 `IconImageSource` 和屬性的組合所定義 `BackgroundColor` ：

[![螢幕擷取畫面： iOS 和 Android 上的 SwipeView 滑動專案](swipeview-images/swipeview-swipeitems.png "SwipeView 滑動專案")](swipeview-images/swipeview-swipeitems-large.png#lightbox "SwipeView 滑動專案")

當 `SwipeItem` 按下時，它的 `Invoked` 事件就會引發，並由其註冊的事件處理常式處理。 此外，也會 `MenuItem.Clicked` 引發事件。 或者，您也 `Command` 可以將屬性設定為叫 `ICommand` 用時將執行的實作為 `SwipeItem` 。

> [!NOTE]
> `SwipeItem`使用或屬性定義的外觀時 `Text` `IconImageSource` ，內容一律會置中。

除了將滑動專案定義為 `SwipeItem` 物件之外，也可以定義自訂滑動專案視圖。 如需詳細資訊，請參閱 [自訂滑動專案](#custom-swipe-items)。

## <a name="swipe-direction"></a>滑動方向

`SwipeView` 支援四種不同的滑動方向，並以物件加入的方向集合來定義滑動方向 `SwipeItems` `SwipeItem` 。 每個滑動方向都可以容納自己的滑動專案。 例如，下列範例會顯示 `SwipeView` 其滑動專案取決於滑動方向的。

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

在此範例中， `SwipeView` 內容可以向右或向左撥動。 向右輕輕會顯示 [ **刪除** ] 滑動專案，而向左輕刷會顯示 [我的 **最愛** ] 和 [ **共用** 滑動] 專案。

> [!WARNING]
> 一次只能設定一個方向 `SwipeItems` 集合的實例 `SwipeView` 。 因此，您不能在上有兩個 `LeftItems` 定義 `SwipeView` 。

`SwipeStarted`、 `SwipeChanging` 和事件會透過 `SwipeEnded` `SwipeDirection` 事件引數中的屬性來報告滑動方向。 這個屬性的型別是 `SwipeDirection` 由四個成員組成的列舉型別（enumeration）：

- `Right` 表示已發生正確的滑動。
- `Left` 指出發生左方刷。
- `Up` 指出已發生向上滑動。
- `Down` 表示已發生向下滑動。

## <a name="swipe-threshold"></a>滑動閾值

`SwipeView` 包含 `Threshold` 類型的屬性 `double` ，此屬性工作表示觸發滑動手勢以完全顯示滑動專案的裝置獨立單位數目。

下列範例顯示的 `SwipeView` 會設定 `Threshold` 屬性：

```xaml
<SwipeView Threshold="200">
    <SwipeView.LeftItems>
        <SwipeItems>
            <SwipeItem Text="Favorite"
                       IconImageSource="favorite.png"
                       BackgroundColor="LightGreen" />
        </SwipeItems>
    </SwipeView.LeftItems>
    <!-- Content -->
</SwipeView>
```

在此範例中， `SwipeView` 必須針對200裝置獨立單位撥動，才能完全顯示 `SwipeItem` 。

> [!NOTE]
> 目前， `Threshold` 屬性只會在 iOS 和 Android 上執行。

## <a name="swipe-mode"></a>滑動模式

`SwipeItems`類別具有 `Mode` 屬性，指出滑動互動的效果。 這個屬性應該設定為其中一個 `SwipeMode` 列舉成員：

- `Reveal` 指出滑動會顯示滑動專案。 此為 `SwipeItems.Mode` 屬性的預設值。
- `Execute` 指出滑動會執行滑動專案。

在 [顯示模式] 中，使用者會撥動 `SwipeView` 以開啟包含一或多個滑動專案的功能表，而且必須明確地點一下滑動專案來執行它。 執行滑動專案之後，就會關閉滑動專案並 `SwipeView` 重新顯示內容。 在執行模式中，使用者撥動 `SwipeView` 以開啟一個功能表，其中包含一個滑動專案，接著會自動執行。 執行之後，就會關閉滑動專案並 `SwipeView` 重新顯示內容。

下列範例顯示 `SwipeView` 已設定為使用執行模式的：

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

在此範例中， `SwipeView` 內容可以撥動許可權來顯示立即執行的滑動專案。 執行之後， `SwipeView` 內容會重新顯示。

## <a name="swipe-behavior"></a>滑動行為

`SwipeItems`類別具有 `SwipeBehaviorOnInvoked` 屬性，表示叫 `SwipeView` 用滑動專案之後的行為方式。 這個屬性應該設定為其中一個 `SwipeBehaviorOnInvoked` 列舉成員：

- `Auto` 表示在顯示模式中， `SwipeView` 會在叫用滑動專案之後關閉，而在執行模式中，則會在叫 `SwipeView` 用滑動專案之後保持開啟狀態。 此為 `SwipeItems.SwipeBehaviorOnInvoked` 屬性的預設值。
- `Close` 表示叫 `SwipeView` 用滑動專案之後關閉。
- `RemainOpen` 表示叫 `SwipeView` 用滑動專案之後仍保持開啟。

下列範例顯示已 `SwipeView` 設定為在叫用滑動專案之後保持開啟狀態：

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

您可以使用類型來定義自訂滑動專案 `SwipeItemView` 。 `SwipeItemView`類別衍生自 [`ContentView`](xref:Xamarin.Forms.ContentView) 類別，並新增下列屬性：

- `Command`，類型為 `ICommand` ，在點擊滑動專案時會執行。
- `CommandParameter`，屬於 `object` 類型，這是傳遞至 `Command` 的參數。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標和樣式。

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

在此範例中， `SwipeItemView` [`StackLayout`](xref:Xamarin.Forms.StackLayout) 包含包含 [`Entry`](xref:Xamarin.Forms.Entry) 和的 [`Label`](xref:Xamarin.Forms.Label) 。 當使用者將輸入輸入至之後 `Entry` ，的其餘部分 `SwipeViewItem` 可以執行 `ICommand` 屬性所定義的 `SwipeItemView.Command` 。

## <a name="open-and-close-a-swipeview-programmatically"></a>以程式設計方式開啟和關閉 SwipeView

`SwipeView` 包含 `Open` 和 `Close` 方法，分別以程式設計方式開啟和關閉滑動專案。 根據預設，這些方法會在 `SwipeView` 其開啟或關閉時建立動畫。

`Open`方法需要 `OpenSwipeItem` 引數，以指定將開啟的方向 `SwipeView` 。 `OpenSwipeItem`列舉包含四個成員：

- `LeftItems`，表示 `SwipeView` 將從左側開啟，以顯示集合中的滑動專案 `LeftItems` 。
- `TopItems`，表示 `SwipeView` 將從頂端開啟，以顯示集合中的滑動專案 `TopItems` 。
- `RightItems`，表示 `SwipeView` 將從右邊開啟，以顯示集合中的滑動專案 `RightItems` 。
- `BottomItems`，表示 `SwipeView` 將從底部開啟，以顯示集合中的滑動專案 `BottomItems` 。

此外，此 `Open` 方法也會接受選擇性 `bool` 引數，以定義是否 `SwipeView` 會在開啟時繪製動畫。

如果指定 `SwipeView` 了 `swipeView` ，則下列範例會示範如何開啟 `SwipeView` 來顯示集合中的滑動專案 `LeftItems` ：

```csharp
swipeView.Open(OpenSwipeItem.LeftItems);
```

`swipeView`然後可以使用方法來關閉 `Close` ：

```csharp
swipeView.Close();
```

> [!NOTE]
> `Close`方法也會接受選擇性 `bool` 引數，以定義是否 `SwipeView` 會在關閉時進行動畫。

## <a name="disable-a-swipeview"></a>停用 SwipeView

應用程式可能會進入以輕量內容專案的狀態，而不是有效的作業。 在這種情況下，您 `SwipeView` 可以藉由將其 `IsEnabled` 屬性設定為來停用 `false` 。 這會讓使用者無法滑動內容來顯示滑動專案。

此外，定義或的屬性時， `Command` `SwipeItem` `SwipeItemView` `CanExecute` `ICommand` 可以指定的委派來啟用或停用滑動專案。

## <a name="related-links"></a>相關連結

- [SwipeView (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-swipeviewdemos/)
- [Xamarin.Forms MenuItem](~/xamarin-forms/user-interface/menuitem.md)
