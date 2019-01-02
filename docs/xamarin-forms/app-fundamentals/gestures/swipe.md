---
title: 新增撥動手勢辨識器
description: 本文說明如何辨識檢視上發生的撥動手勢。
ms.prod: xamarin
ms.assetid: 164976C2-1429-49FB-9EB6-621E2681C19B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/14/2018
ms.openlocfilehash: 2e28d3e7035eb570b2053d39cec8b8d91dada6f0
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2018
ms.locfileid: "53059414"
---
# <a name="adding-a-swipe-gesture-recognizer"></a>新增撥動手勢辨識器

[![下載範例](~/media/shared/download.png) 下載範例](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/SwipeGesture/)

_撥動手勢會在手指以水平或垂直方向橫跨畫面移動時發生，且通常用於起始內容的導覽。本文的程式碼範例取自[撥動手勢](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/SwipeGesture/)範例。_

若要讓 [`View`](xref:Xamarin.Forms.View) 辨識撥動手勢，請建立 [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) 執行個體，將 [`Direction`](xref:Xamarin.Forms.SwipeGestureRecognizer.Direction) 屬性設為 [`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection) 列舉值 (`Left`、`Right`、`Up` 或 `Down`)，選擇性地設定 [`Threshold`](xref:Xamarin.Forms.SwipeGestureRecognizer.Threshold) 屬性，處理 [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) 事件，並將新的手勢辨識器新增至檢視上的 [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) 集合。 下列程式碼範例顯示附加至 [`BoxView`](xref:Xamarin.Forms.BoxView) 的 `SwipeGestureRecognizer`：

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

[`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) 類別也包含 [`Threshold`](xref:Xamarin.Forms.SwipeGestureRecognizer.Threshold) 屬性，您可以選擇性地將其設為 `uint` 值，以代表為了辨識撥動必須達到的最小撥動距離 (裝置獨立單位)。 這個屬性的預設值為 100，表示撥動距離 (裝置獨立單位) 低於 100 的撥動將會被忽略。

## <a name="recognizing-the-swipe-direction"></a>辨識撥動方向

上述範例會將 [`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction) 屬性設定為從 [`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection) 列舉中選擇單一值。 不過，您也可將此屬性設定為從 `SwipeDirection` 列舉中選擇多個值，以便引發 [ `Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) 事件以回應多個方向的撥動。 不過，條件約束是單一 [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) 只能辨識發生在同一個軸上的撥動。 因此，您可以將 `Direction` 屬性設定為 `Left` 和 `Right` 以辨識發生在水平軸上的撥動：

```xaml
<SwipeGestureRecognizer Direction="Left,Right" Swiped="OnSwiped"/>
```

同樣地， 您可以將 [`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction) 屬性設定為 `Up` 和 `Down` 以辨識發生在垂直軸上的撥動：

```csharp
var swipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Up | SwipeDirection.Down };
```

或者，您可以針對每個撥動方向建立 [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) 以辨識每個方向的撥動：

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
> 在上述範例中，相同事件處理常式會回應 [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) 事件的引發。 不過，每個 [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) 執行個體都可以視需要使用不同的事件處理常式。

## <a name="responding-to-the-swipe"></a>撥動的回應

下列範例顯示 [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) 事件的事件處理常式：

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

可檢查 [`SwipedEventArgs`](xref:Xamarin.Forms.SwipedEventArgs) 以判斷撥動的方向，並使用自訂邏輯，視需要回應撥動。 可從事件引數的 [`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction) 屬性取得撥動方向，其會設定為 [`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection) 列舉的其中一個值。 此外，事件引數也具有 [`Parameter`](xref:Xamarin.Forms.SwipedEventArgs.Parameter) 屬性，其會設定為 [`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter) 屬性 (若已定義) 的值。

## <a name="using-commands"></a>使用命令

[`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) 類別也包含 [`Command`](xref:Xamarin.Forms.SwipeGestureRecognizer.Command) 和 [`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter) 屬性。 這些屬性通常用於使用 Model-View-ViewModel (MVVM) 模式的應用程式。 `Command` 屬性定義要在辨識撥動手勢後叫用的 `ICommand`，`CommandParameter` 屬性則定義要傳遞至 `ICommand.` 的物件。下列程式碼範例示範如何將 `Command` 屬性繫結至檢視模型中定義的 `ICommand`，而該模型的執行個體已設定為頁面 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)：

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

`SwipeCommand` 是檢視模型執行個體中定義的 `ICommand` 類型屬性，該檢視模型執行個體已設定為頁面 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)。 辨識撥動手勢之後，將會執行 `SwipeCommand` 物件的 `Execute` 方法。 `Execute` 方法的引數就是 [`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter) 屬性的值。 如需命令的詳細資訊，請參閱[命令介面](~/xamarin-forms/app-fundamentals/data-binding/commanding.md)。

## <a name="creating-a-swipe-container"></a>建立撥動容器

下列程式碼範例所示的 `SwipeContainer` 類別是一般化的撥動辨識類別，其會由 [`View`](xref:Xamarin.Forms.View) 包裝以執行撥動手勢辨識：

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

`SwipeContainer` 類別會為所有四個撥動方向建立 [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) 物件，並附加 `Swipe` 事件處理常式。 這些事件處理常式會叫用 `SwipeContainer` 所定義的 `Swipe` 事件。

下列 XAML 程式碼範例會顯示包裝 [`BoxView`](xref:Xamarin.Forms.BoxView) 的 `SwipeContainer` 類別：

```xaml
<ContentPage ...>
    <StackLayout>
        <local:SwipeContainer Swipe="OnSwiped" ...>
            <BoxView Color="Teal" ... />
        </local:SwipeContainer>
    </StackLayout>
</ContentPage>
```

下列程式碼範例顯示 `SwipeContainer` 如何在 C# 頁面中包裝 [`BoxView`](xref:Xamarin.Forms.BoxView)：

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

當 [`BoxView`](xref:Xamarin.Forms.BoxView) 收到撥動手勢時，會引發 [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) 中的 [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) 事件。 這由 `SwipeContainer` 類別處理，而該類別會引發它自己的 `Swipe` 事件。 這個 `Swipe` 事件會在頁面上處理。 接著，即可檢查 [`SwipedEventArgs`](xref:Xamarin.Forms.SwipedEventArgs) 以判斷撥動的方向，並使用自訂邏輯，視需要回應撥動。

## <a name="related-links"></a>相關連結

- [SwipeGesture (Samples)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/SwipeGesture/)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [SwipeGestureRecognizer](xref:Xamarin.Forms.SwipeGestureRecognizer)
