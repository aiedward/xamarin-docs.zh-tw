---
title: 新增滑動手勢辨識器
description: 本文說明如何辨識檢視上發生的撥動手勢。
ms.prod: xamarin
ms.assetid: 164976C2-1429-49FB-9EB6-621E2681C19B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/14/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: cbd1bdd097bc205f82f98574df5c356b03769e29
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562648"
---
# <a name="add-a-swipe-gesture-recognizer"></a>新增滑動手勢辨識器

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-swipegesture)

_當手指以水準或垂直方向移至螢幕上時，就會發生滑動手勢，通常用來起始內容的導覽。本文中的程式碼範例取自「 [滑動手勢](/samples/xamarin/xamarin-forms-samples/workingwithgestures-swipegesture) 」範例。_

若要 [`View`](xref:Xamarin.Forms.View) 辨識滑動手勢，請建立 [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) 實例、將 [`Direction`](xref:Xamarin.Forms.SwipeGestureRecognizer.Direction) 屬性設為 [`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection) 列舉值 (`Left` 、、 `Right` `Up` 或 `Down`) 、選擇性地設定 [`Threshold`](xref:Xamarin.Forms.SwipeGestureRecognizer.Threshold) 屬性、處理 [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) 事件，以及將新的手勢辨識器新增至 [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) 視圖上的集合。 下列程式碼範例顯示 `SwipeGestureRecognizer` 附加至的 [`BoxView`](xref:Xamarin.Forms.BoxView) ：

```xaml
<BoxView Color="Teal" ...>
    <BoxView.GestureRecognizers>
        <SwipeGestureRecognizer Direction="Left" Swiped="OnSwiped"/>
    </BoxView.GestureRecognizers>
</BoxView>
```

以下是對等的 C# 程式碼：

```csharp
var boxView = new BoxView { Color = Color.Teal, ... };
var leftSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Left };
leftSwipeGesture.Swiped += OnSwiped;

boxView.GestureRecognizers.Add(leftSwipeGesture);
```

此 [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) 類別也包含 [`Threshold`](xref:Xamarin.Forms.SwipeGestureRecognizer.Threshold) 屬性，您可以選擇性地將它設定為 `uint` 值，以表示在裝置獨立的單位中，必須達到才能辨識滑動的最小刷距離。 這個屬性的預設值為 100，表示撥動距離 (裝置獨立單位) 低於 100 的撥動將會被忽略。

## <a name="recognizing-the-swipe-direction"></a>辨識撥動方向

在上述範例中， [`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction) 屬性是從列舉值設定為單一值 [`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection) 。 不過，您也可以將此屬性設定為列舉中的多個值 `SwipeDirection` ，以便 [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) 引發事件以回應一個以上的方向的滑動。 不過，條件約束是單一 [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) 只能辨識在相同軸上發生的撥動。 因此，您可以將 `Direction` 屬性設定為 `Left` 和 `Right` 以辨識發生在水平軸上的撥動：

```xaml
<SwipeGestureRecognizer Direction="Left,Right" Swiped="OnSwiped"/>
```

同樣地，您可以藉由將屬性設定為和來辨識垂直軸上發生的撥動 [`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction) `Up` `Down` ：

```csharp
var swipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Up | SwipeDirection.Down };
```

或者，您 [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) 可以建立每個滑動方向的，以在每個方向辨識撥動：

```xaml
<BoxView Color="Teal" ...>
    <BoxView.GestureRecognizers>
        <SwipeGestureRecognizer Direction="Left" Swiped="OnSwiped"/>
        <SwipeGestureRecognizer Direction="Right" Swiped="OnSwiped"/>
        <SwipeGestureRecognizer Direction="Up" Swiped="OnSwiped"/>
        <SwipeGestureRecognizer Direction="Down" Swiped="OnSwiped"/>
    </BoxView.GestureRecognizers>
</BoxView>
```

以下是對等的 C# 程式碼：

```csharp
var boxView = new BoxView { Color = Color.Teal, ... };
var leftSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Left };
leftSwipeGesture.Swiped += OnSwiped;
var rightSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Right };
rightSwipeGesture.Swiped += OnSwiped;
var upSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Up };
upSwipeGesture.Swiped += OnSwiped;
var downSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Down };
downSwipeGesture.Swiped += OnSwiped;

boxView.GestureRecognizers.Add(leftSwipeGesture);
boxView.GestureRecognizers.Add(rightSwipeGesture);
boxView.GestureRecognizers.Add(upSwipeGesture);
boxView.GestureRecognizers.Add(downSwipeGesture);
```

> [!NOTE]
> 在上述範例中，相同的事件處理常式會回應 [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) 事件的引發。 但是，如果需要，每個 [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) 實例可以使用不同的事件處理常式。

## <a name="responding-to-the-swipe"></a>撥動的回應

事件的事件處理常式如 [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) 下列範例所示：

```csharp
void OnSwiped(object sender, SwipedEventArgs e)
{
    switch (e.Direction)
    {
        case SwipeDirection.Left:
            // Handle the swipe
            break;
        case SwipeDirection.Right:
            // Handle the swipe
            break;
        case SwipeDirection.Up:
            // Handle the swipe
            break;
        case SwipeDirection.Down:
            // Handle the swipe
            break;
    }
}
```

您 [`SwipedEventArgs`](xref:Xamarin.Forms.SwipedEventArgs) 可以使用自訂邏輯，視需要回應滑動程式來檢查滑動方向。 您可以從 [`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction) 事件引數的屬性取得滑動方向，這些引數會設為列舉值的其中一個值 [`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection) 。 此外，事件引數也具有屬性， [`Parameter`](xref:Xamarin.Forms.SwipedEventArgs.Parameter) 該屬性會設定為屬性的值 [`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter) （如果已定義）。

## <a name="using-commands"></a>使用命令

[`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer)類別也包含 [`Command`](xref:Xamarin.Forms.SwipeGestureRecognizer.Command) 和 [`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter) 屬性。 這些屬性通常用於使用 Model-View-ViewModel (MVVM) 模式的應用程式。 屬性會定義要在可辨識 `Command` `ICommand` 滑動手勢時叫用的，而 `CommandParameter` 屬性定義要傳遞至下列程式碼範例的物件，則會示範 `ICommand.` 如何將屬性系結 `Command` 至 `ICommand` 視圖模型中的定義，該模型的實例會設定為頁面 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) ：

```csharp
var boxView = new BoxView { Color = Color.Teal, ... };
var leftSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Left, CommandParameter = "Left" };
leftSwipeGesture.SetBinding(SwipeGestureRecognizer.CommandProperty, "SwipeCommand");
boxView.GestureRecognizers.Add(leftSwipeGesture);
```

對等的 XAML 程式碼如下：

```xaml
<BoxView Color="Teal" ...>
    <BoxView.GestureRecognizers>
        <SwipeGestureRecognizer Direction="Left" Command="{Binding SwipeCommand}" CommandParameter="Left" />
    </BoxView.GestureRecognizers>
</BoxView>
```

`SwipeCommand` 這是在 `ICommand` 設定為頁面的視圖模型實例中所定義之類型的屬性 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 。 辨識撥動手勢之後，將會執行 `SwipeCommand` 物件的 `Execute` 方法。 方法的引數 `Execute` 是屬性的值 [`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter) 。 如需命令的詳細資訊，請參閱[命令介面](~/xamarin-forms/app-fundamentals/data-binding/commanding.md)。

## <a name="creating-a-swipe-container"></a>建立撥動容器

`SwipeContainer`下列程式碼範例所示的類別，是包裝在周圍 [`View`](xref:Xamarin.Forms.View) 以執行滑動手勢辨識的一般化滑動辨識類別：

```csharp
public class SwipeContainer : ContentView
{
    public event EventHandler<SwipedEventArgs> Swipe;

    public SwipeContainer()
    {
        GestureRecognizers.Add(GetSwipeGestureRecognizer(SwipeDirection.Left));
        GestureRecognizers.Add(GetSwipeGestureRecognizer(SwipeDirection.Right));
        GestureRecognizers.Add(GetSwipeGestureRecognizer(SwipeDirection.Up));
        GestureRecognizers.Add(GetSwipeGestureRecognizer(SwipeDirection.Down));
    }

    SwipeGestureRecognizer GetSwipeGestureRecognizer(SwipeDirection direction)
    {
        var swipe = new SwipeGestureRecognizer { Direction = direction };
        swipe.Swiped += (sender, e) => Swipe?.Invoke(this, e);
        return swipe;
    }
}
```

`SwipeContainer`類別會建立 [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) 所有四個滑動方向的物件，並附加 `Swipe` 事件處理常式。 這些事件處理常式會叫用 `SwipeContainer` 所定義的 `Swipe` 事件。

下列 XAML 程式碼範例顯示 `SwipeContainer` 包裝類別的類別 [`BoxView`](xref:Xamarin.Forms.BoxView) ：

```xaml
<ContentPage ...>
    <StackLayout>
        <local:SwipeContainer Swipe="OnSwiped" ...>
            <BoxView Color="Teal" ... />
        </local:SwipeContainer>
    </StackLayout>
</ContentPage>
```

下列程式碼範例顯示如何 `SwipeContainer` [`BoxView`](xref:Xamarin.Forms.BoxView) 在 c # 頁面中包裝 a：

```csharp
public class SwipeContainerPageCS : ContentPage
{
    public SwipeContainerPageCS()
    {
        var boxView = new BoxView { Color = Color.Teal, ... };
        var swipeContainer = new SwipeContainer { Content = boxView, ... };
        swipeContainer.Swipe += (sender, e) =>
        {
          // Handle the swipe
        };

        Content = new StackLayout
        {
            Children = { swipeContainer }
        };
    }
}
```

當 [`BoxView`](xref:Xamarin.Forms.BoxView) 收到滑動手勢時，中的 [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) 事件 [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) 就會引發。 這由 `SwipeContainer` 類別處理，而該類別會引發它自己的 `Swipe` 事件。 這個 `Swipe` 事件會在頁面上處理。 [`SwipedEventArgs`](xref:Xamarin.Forms.SwipedEventArgs)然後可以檢查，以根據需要回應滑動的自訂邏輯，來判斷滑動的方向。

## <a name="related-links"></a>相關連結

- [SwipeGesture (Samples)](/samples/xamarin/xamarin-forms-samples/workingwithgestures-swipegesture)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [SwipeGestureRecognizer](xref:Xamarin.Forms.SwipeGestureRecognizer)