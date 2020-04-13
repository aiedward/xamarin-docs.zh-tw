---
title: 新增撥動手勢辨識器
description: 本文說明如何辨識檢視上發生的撥動手勢。
ms.prod: xamarin
ms.assetid: 164976C2-1429-49FB-9EB6-621E2681C19B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/14/2018
ms.openlocfilehash: ae9b5eb5b768b50ddcbc199040074de855f220de
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "68649449"
---
# <a name="adding-a-swipe-gesture-recognizer"></a>新增撥動手勢辨識器

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-swipegesture)

_當手指以水準或垂直方向在螢幕上移動,並且通常用於啟動內容導航時,將發生輕掃手勢。本文中的代碼示例取自[「滑動手勢」](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-swipegesture)範例。_

要進行[`View`](xref:Xamarin.Forms.View)識別輕掃手勢,請創建實例[`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer),[`Direction`](xref:Xamarin.Forms.SwipeGestureRecognizer.Direction)將 屬性[`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection)設置為 枚舉`Left``Right``Up`值`Down`(、、、 或[`Threshold`](xref:Xamarin.Forms.SwipeGestureRecognizer.Threshold)),[`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped)可以選擇設置 屬性、處理 事件,並將新的手[`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers)勢識別器添加到視圖上的 集合中。 以下代碼範例顯示`SwipeGestureRecognizer`額外的[`BoxView`](xref:Xamarin.Forms.BoxView)。

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

該[`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer)類還包括一[`Threshold`](xref:Xamarin.Forms.SwipeGestureRecognizer.Threshold)個 屬性,該屬性可以選擇設置`uint`為表示 在設備無關的單元中識別輕掃所需的最小滑動距離的值。 這個屬性的預設值為 100，表示撥動距離 (裝置獨立單位) 低於 100 的撥動將會被忽略。

## <a name="recognizing-the-swipe-direction"></a>辨識撥動方向

在上面的示例中,屬性[`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction)設置為從枚舉中[`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection)單一一個值。 但是,還可以將此屬性設置為枚舉中的`SwipeDirection`多個值,以便觸[`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped)發 事件以回應多個方向的輕掃。 但是,約束是,單個[`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer)只能識別在同一軸上發生的輕掃。 因此，您可以將 `Direction` 屬性設定為 `Left` 和 `Right` 以辨識發生在水平軸上的撥動：

```xaml
<SwipeGestureRecognizer Direction="Left,Right" Swiped="OnSwiped"/>
```

同樣,通過將[`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction)屬性設置`Up`為`Down`和 可以識別垂直軸上的輕掃。

```csharp
var swipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Up | SwipeDirection.Down };
```

或者,可以為[`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer)每個滑動方向創建一個,以識別每個方向的輕掃:

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
> 在上面的示例中,相同的事件處理程序回應[`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped)事件觸發。 但是,如果需要[`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer),每個實例可以使用不同的事件處理程式。

## <a name="responding-to-the-swipe"></a>撥動的回應

[`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped)事件的事件處理程式顯示在以下範例中:

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

可以[`SwipedEventArgs`](xref:Xamarin.Forms.SwipedEventArgs)檢查 以確定輕掃的方向,自定義邏輯根據需要響應輕掃。 可以從事件參數[`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction)的屬性中獲取輕掃的方向,該屬性將設置[`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection)為 枚舉的值之一。 此外,事件參數還具有一個[`Parameter`](xref:Xamarin.Forms.SwipedEventArgs.Parameter)屬性,如果已定義,該屬性將設置[`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter)為 屬性的值。

## <a name="using-commands"></a>使用命令

類[`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer)還包括[`Command`](xref:Xamarin.Forms.SwipeGestureRecognizer.Command)和[`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter)屬性。 這些屬性通常用於使用 Model-View-ViewModel (MVVM) 模式的應用程式。 屬性`Command``ICommand``CommandParameter`定義 在識別滑動手勢時要呼叫的,屬性定義要傳遞給`ICommand.`的物件 。下面的代碼範例`Command`示範如何將 屬性繫結到`ICommand`檢視模型中 定義的物件,其實[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)例設定為頁面 :

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

`SwipeCommand`是檢視模型實體定義`ICommand`的類型屬性,設定為頁[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)。 辨識撥動手勢之後，將會執行 `SwipeCommand` 物件的 `Execute` 方法。 方法的`Execute`參數[`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter)是 屬性的值。 如需命令的詳細資訊，請參閱[命令介面](~/xamarin-forms/app-fundamentals/data-binding/commanding.md)。

## <a name="creating-a-swipe-container"></a>建立撥動容器

該`SwipeContainer`類(如以下代碼範例所示)是一個通用的輕掃辨識類,環[`View`](xref:Xamarin.Forms.View)繞 在 以執行滑動手勢識別:

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

該`SwipeContainer`類為[`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer)所有 四個滑動方向創建物件,`Swipe`並附加事件處理程式。 這些事件處理常式會叫用 `SwipeContainer` 所定義的 `Swipe` 事件。

下面的 XAML 代碼範例`SwipeContainer`顯示包裝[`BoxView`](xref:Xamarin.Forms.BoxView)的 類別:

```xaml
<ContentPage ...>
    <StackLayout>
        <local:SwipeContainer Swipe="OnSwiped" ...>
            <BoxView Color="Teal" ... />
        </local:SwipeContainer>
    </StackLayout>
</ContentPage>
```

以下代碼範例顯示如何在`SwipeContainer`C#[`BoxView`](xref:Xamarin.Forms.BoxView)頁中換行:

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

當[`BoxView`](xref:Xamarin.Forms.BoxView)收到輕掃手勢時[`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped)[`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer), 將觸發 中的事件。 這由 `SwipeContainer` 類別處理，而該類別會引發它自己的 `Swipe` 事件。 這個 `Swipe` 事件會在頁面上處理。 然後[`SwipedEventArgs`](xref:Xamarin.Forms.SwipedEventArgs)可以檢查 以確定輕掃的方向,自定義邏輯根據需要響應輕掃。

## <a name="related-links"></a>相關連結

- [SwipeGesture (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-swipegesture)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [SwipeGestureRecognizer](xref:Xamarin.Forms.SwipeGestureRecognizer)
