---
title: 新增揮擊筆勢辨識器
description: 這篇文章說明如何辨識檢視上所發生的撥動手勢。
ms.prod: xamarin
ms.assetid: 164976C2-1429-49FB-9EB6-621E2681C19B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/14/2018
ms.openlocfilehash: 95e95d8849824cd2dc31c2019627cc5adbbefeec
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50131144"
---
# <a name="adding-a-swipe-gesture-recognizer"></a>新增揮擊筆勢辨識器

_撥動手勢發生於手指移動以水平或垂直方向，畫面上，且通常用來起始瀏覽內容。這篇文章中的程式碼範例取自[撥動手勢](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/SwipeGesture/)範例。_

若要讓[ `View` ](xref:Xamarin.Forms.View)辨識撥動手勢，建立[ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer)執行個體、 將[ `Direction` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Direction)屬性[ `SwipeDirection`](xref:Xamarin.Forms.SwipeDirection)列舉值 (`Left`， `Right`， `Up`，或`Down`)，選擇性地設定[ `Threshold` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Threshold)屬性，控制代碼[ `Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped)事件，並新增至新的筆勢辨識器[ `GestureRecognizers` ](xref:Xamarin.Forms.View.GestureRecognizers)檢視上的集合。 下列程式碼範例所示`SwipeGestureRecognizer`附加至[ `BoxView` ](xref:Xamarin.Forms.BoxView):

```xaml
<BoxView Color="Teal" ...>
    <BoxView.GestureRecognizers>
        <SwipeGestureRecognizer Direction="Left" Swiped="OnSwiped"/>
    </BoxView.GestureRecognizers>
</BoxView>
```

以下是對等項目C#程式碼：

```csharp
var boxView = new BoxView { Color = Color.Teal, ... };
var leftSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Left };
leftSwipeGesture.Swiped += OnSwiped;

boxView.GestureRecognizers.Add(leftSwipeGesture);
```

[ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer)類別也包含[ `Threshold` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Threshold)屬性，可以選擇性地設定為`uint`值，表示必須可達成的最小的撥動距離撥動以辨識，以與裝置無關單位。 這個屬性的預設值為 100，表示會少於 100 個裝置獨立單位將會忽略任何 swipes。

## <a name="recognizing-the-swipe-direction"></a>辨識的撥動方向

在上述範例[ `Direction` ](xref:Xamarin.Forms.SwipedEventArgs.Direction)屬性設定為單一值，以從[ `SwipeDirection` ](xref:Xamarin.Forms.SwipeDirection)列舉型別。 不過，您也可將此屬性設定為從多個值`SwipeDirection`列舉型別，以便[ `Swiped` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped)事件引發以回應揮擊一個以上的方向。 不過，條件約束是單[ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer)只能辨識 swipes 發生在同一個軸上。 因此，辨識 swipes 發生水平軸上，藉由設定`Direction`屬性，以`Left`和`Right`:

```xaml
<SwipeGestureRecognizer Direction="Left,Right" Swiped="OnSwiped"/>
```

同樣地，辨識 swipes 發生在垂直軸上，藉由設定[ `Direction` ](xref:Xamarin.Forms.SwipedEventArgs.Direction)屬性設`Up`和`Down`:

```csharp
var swipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Up | SwipeDirection.Down };
```

或者， [ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer)針對每個撥動方向可以建立辨識 swipes 中每個方向：

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

以下是對等項目C#程式碼：

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
> 在上述範例中，相同的事件處理常式會回應[ `Swiped` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped)事件的引發。 不過，每個[ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer)執行個體可以視需要使用不同的事件處理常式。

## <a name="responding-to-the-swipe"></a>回應撥動

事件處理常式[ `Swiped` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped)事件在下列範例所示：

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

[ `SwipedEventArgs` ](xref:Xamarin.Forms.SwipedEventArgs)可進行檢查以判斷方向的撥動，以回應所需的撥動的自訂邏輯。 可從取得的撥動方向[ `Direction` ](xref:Xamarin.Forms.SwipedEventArgs.Direction)屬性的事件引數，這將會設定為其中的值[ `SwipeDirection` ](xref:Xamarin.Forms.SwipeDirection)列舉型別。 此外，事件引數也可以[ `Parameter` ](xref:Xamarin.Forms.SwipedEventArgs.Parameter)屬性，將設定的值[ `CommandParameter` ](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter)如果已定義的屬性。

## <a name="using-commands"></a>使用命令

[ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer)類別也包含[ `Command` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Command)並[ `CommandParameter` ](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter)屬性。 這些屬性通常用於使用 Model View ViewModel (MVVM) 模式的應用程式。 `Command`屬性會定義`ICommand`撥動手勢辨識後，要叫用具有`CommandParameter`屬性定義要傳遞至物件`ICommand.`下列程式碼範例示範如何繫結`Command`屬性，以`ICommand`其執行個體已設定為頁面檢視模型中定義[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext):

```csharp
var boxView = new BoxView { Color = Color.Teal, ... };
var leftSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Left, CommandParameter = "Left" };
leftSwipeGesture.SetBinding(SwipeGestureRecognizer.CommandProperty, "SwipeCommand");
boxView.GestureRecognizers.Add(leftSwipeGesture);
```

對等的 XAML 程式碼是：

```xaml
<BoxView Color="Teal" ...>
    <BoxView.GestureRecognizers>
        <SwipeGestureRecognizer Direction="Left" Command="{Binding SwipeCommand}" CommandParameter="Left" />
    </BoxView.GestureRecognizers>
</BoxView>
```

`SwipeCommand` 是的型別屬性`ICommand`設定為頁面檢視的模型執行個體中定義[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)。 撥動手勢辨識後，`Execute`方法的`SwipeCommand`物件將會執行。 引數`Execute`方法就是值[ `CommandParameter` ](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter)屬性。 如需有關命令的詳細資訊，請參閱 <<c0> [ 的命令介面](~/xamarin-forms/app-fundamentals/data-binding/commanding.md)。

## <a name="creating-a-swipe-container"></a>建立撥動容器

`SwipeContainer`類別，會顯示在下列程式碼範例中，是一般化的撥動辨識類別會包裝著[ `View` ](xref:Xamarin.Forms.View)來執行揮擊筆勢辨識：

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

`SwipeContainer`類別會建立[ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer)物件的所有四個的撥動方向，並附加`Swipe`事件處理常式。 這些事件處理常式叫用`Swipe`所定義的事件`SwipeContainer`。

下列 XAML 程式碼範例所示`SwipeContainer`類別包裝[ `BoxView` ](xref:Xamarin.Forms.BoxView):

```xaml
<ContentPage ...>
    <StackLayout>
        <local:SwipeContainer Swipe="OnSwiped" ...>
            <BoxView Color="Teal" ... />
        </local:SwipeContainer>
    </StackLayout>
</ContentPage>
```

下列程式碼範例示範如何`SwipeContainer`包裝[ `BoxView` ](xref:Xamarin.Forms.BoxView)在C#頁面上：

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

當[ `BoxView` ](xref:Xamarin.Forms.BoxView)收到撥動手勢[ `Swiped` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped)中的事件[ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer)引發。 這由處理`SwipeContainer`類別，就會引發它自己`Swipe`事件。 這`Swipe`頁面上處理事件。 [ `SwipedEventArgs` ](xref:Xamarin.Forms.SwipedEventArgs)可隨後進行檢查以判斷方向的撥動，以回應所需的撥動的自訂邏輯。

## <a name="related-links"></a>相關連結

- [撥動手勢 （範例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/SwipeGesture/)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [SwipeGestureRecognizer](xref:Xamarin.Forms.SwipeGestureRecognizer)
