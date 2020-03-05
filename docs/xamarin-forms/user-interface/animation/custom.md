---
title: Xamarin 中的自訂動畫
description: 本文示範如何使用 [Xamarin 動畫] 類別來建立和取消動畫、同步處理多個動畫，以及建立自訂動畫，以動畫顯示現有動畫方法不會產生動畫的屬性。
ms.prod: xamarin
ms.assetid: 03B2E3FC-E720-4D45-B9A0-711081FC1907
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/10/2019
ms.openlocfilehash: 405d7990b622b890aa3d66bd632662f086441666
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "78292629"
---
# <a name="custom-animations-in-xamarinforms"></a>Xamarin 中的自訂動畫

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-custom)

_動畫類別是所有 Xamarin. 表單動畫的建立區塊，而 ViewExtensions 類別中的擴充方法會建立一或多個動畫物件。本文示範如何使用動畫類別來建立和取消動畫、同步處理多個動畫，以及建立自訂動畫，以動畫顯示現有動畫方法不會產生動畫的屬性。_

建立 `Animation` 物件時，必須指定一些參數，包括正在動畫之屬性的開始和結束值，以及變更屬性值的回呼。 `Animation` 物件也可以維護可執行及同步處理的子動畫集合。 如需詳細資訊，請參閱[子動畫](#child)。

藉由呼叫[`Commit`](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean}))方法，來執行以[`Animation`](xref:Xamarin.Forms.Animation)類別（不一定包括子動畫）建立的動畫。 這個方法會指定動畫的持續時間，以及其他專案之間，控制是否要重複動畫的回呼。

此外， [`Animation`](xref:Xamarin.Forms.Animation)類別具有 `IsEnabled` 屬性，可加以檢查以判斷作業系統是否已停用動畫，例如啟用省電模式時。

## <a name="create-an-animation"></a>建立動畫

建立[`Animation`](xref:Xamarin.Forms.Animation)物件時，通常需要至少三個參數，如下列程式碼範例所示：

```csharp
var animation = new Animation (v => image.Scale = v, 1, 2);
```

此程式碼會將[`Image`](xref:Xamarin.Forms.Image)實例之[`Scale`](xref:Xamarin.Forms.VisualElement.Scale)屬性的動畫，從1的值定義為2。 由 Xamarin 所衍生的動畫值會傳遞至指定為第一個引數的回呼，用來變更 `Scale` 屬性的值。

動畫會使用[`Commit`](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean}))方法的呼叫來啟動，如下列程式碼範例所示：

```csharp
animation.Commit (this, "SimpleAnimation", 16, 2000, Easing.Linear, (v, c) => image.Scale = 1, () => true);
```

請注意， [`Commit`](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean}))方法不會傳回 `Task` 物件。 相反地，會透過回呼方法來提供通知。

下列引數是在 `Commit` 方法中指定：

- 第一個引數（*擁有*者）會識別動畫的擁有者。 這可以是套用動畫的視覺專案，或是另一個視覺元素（例如頁面）。
- 第二個引數（*name*）會以名稱來識別動畫。 名稱會與擁有者結合，以唯一識別動畫。 此唯一識別可接著用來判斷動畫是否正在執行（[`AnimationIsRunning`](xref:Xamarin.Forms.AnimationExtensions.AnimationIsRunning(Xamarin.Forms.IAnimatable,System.String))），或取消它（[`AbortAnimation`](xref:Xamarin.Forms.AnimationExtensions.AbortAnimation(Xamarin.Forms.IAnimatable,System.String))）。
- 第三個引數（*速率*）表示每次呼叫[`Animation`](xref:Xamarin.Forms.Animation)函式中所定義的回呼方法之間的毫秒數
- 第四個引數（*長度*）表示動畫的持續時間（以毫秒為單位）。
- 第五個引數（*緩動*）定義要在動畫中使用的緩動函式。 或者，可以將緩動函式指定為[`Animation`](xref:Xamarin.Forms.Animation)的參數的引數。 如需緩時函數的詳細資訊，請參閱[簡化](~/xamarin-forms/user-interface/animation/easing.md)函式。
- 第六個引數（*完成*）是當動畫完成時，將會執行的回呼。 這個回呼會採用兩個引數，其中第一個引數表示最後一個值，而第二個引數是設定為 `true` 的 `bool` （如果已取消動畫）。 或者，您也可以將*完成*的回呼指定為[`Animation`](xref:Xamarin.Forms.Animation)的函式的引數。 不過，透過單一動畫，如果*已完成*的回呼是在 `Animation` 的函式和 `Commit` 方法中指定，則只會執行 `Commit` 方法中指定的回呼。
- 第七個引數（*重複*）是允許重複動畫的回呼。 它會在動畫結束時呼叫，並傳回 `true` 表示動畫應重複。

整體效果是建立一個動畫，將[`Image`](xref:Xamarin.Forms.Image)的[`Scale`](xref:Xamarin.Forms.VisualElement.Scale)屬性從1到2，使用[`Linear`](xref:Xamarin.Forms.Easing.Linear)的緩時數（2000毫秒）來增加。 每次動畫完成時，其 `Scale` 屬性都會重設為1，且動畫會重複。

> [!NOTE]
> 並行的動畫（彼此獨立執行）可以藉由建立每個動畫的 `Animation` 物件，然後在每個動畫上呼叫 `Commit` 方法來加以建造。

<a name="child" />

### <a name="child-animations"></a>子動畫

[`Animation`](xref:Xamarin.Forms.Animation)類別也支援子動畫，這牽涉到建立加入其他 `Animation` 物件的 `Animation` 物件。 這可讓一系列的動畫執行並同步處理。 下列程式碼範例示範如何建立和執行子動畫：

```csharp
var parentAnimation = new Animation ();
var scaleUpAnimation = new Animation (v => image.Scale = v, 1, 2, Easing.SpringIn);
var rotateAnimation = new Animation (v => image.Rotation = v, 0, 360);
var scaleDownAnimation = new Animation (v => image.Scale = v, 2, 1, Easing.SpringOut);

parentAnimation.Add (0, 0.5, scaleUpAnimation);
parentAnimation.Add (0, 1, rotateAnimation);
parentAnimation.Add (0.5, 1, scaleDownAnimation);

parentAnimation.Commit (this, "ChildAnimations", 16, 4000, null, (v, c) => SetIsEnabledButtonState (true, false));
```

或者，您也可以撰寫更簡潔的程式碼範例，如下列程式碼範例所示：

```csharp
new Animation {
    { 0, 0.5, new Animation (v => image.Scale = v, 1, 2) },
    { 0, 1, new Animation (v => image.Rotation = v, 0, 360) },
    { 0.5, 1, new Animation (v => image.Scale = v, 2, 1) }
    }.Commit (this, "ChildAnimations", 16, 4000, null, (v, c) => SetIsEnabledButtonState (true, false));
```

在這兩個程式碼範例中，會建立一個父系[`Animation`](xref:Xamarin.Forms.Animation)物件，然後再新增額外的 `Animation` 物件。 [`Add`](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation))方法的前兩個引數會指定開始和完成子動畫的時機。 引數值必須介於0和1之間，而且代表父動畫中指定子動畫將使用的相對期間。 因此，在此範例中，`scaleUpAnimation` 將在動畫的前半部作用中，`scaleDownAnimation` 將在動畫的後半部作用中，而 `rotateAnimation` 則會在整個持續時間內作用。

整體效果是動畫會在4秒（4000毫秒）發生。 `scaleUpAnimation` 會在2秒內，從1到2繪製[`Scale`](xref:Xamarin.Forms.VisualElement.Scale)屬性的動畫。 然後，`scaleDownAnimation` 會在2秒內，將 `Scale` 屬性從2動畫繪製到1。 同時發生這兩個調整動畫時，`rotateAnimation` 會在4秒內，從0到360繪製[`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)屬性的動畫。 請注意，縮放動畫也會使用緩動函數。 [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn)的緩時函式會導致[`Image`](xref:Xamarin.Forms.Image)一開始先壓縮，再變大，而[`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut)緩動函式會導致 `Image` 小於完整動畫結尾的實際大小。

使用子動畫的[`Animation`](xref:Xamarin.Forms.Animation)物件與不會有一些差異：

- 使用子動畫時，子動畫上*完成*的回呼會指出子系完成的時間，而傳遞至 `Commit` 方法的*完成*回呼會指出整個動畫完成的時間。
- 使用子動畫時，從 `Commit` 方法的*重複*回呼傳回 `true` 不會導致動畫重複，但動畫將會繼續執行，而不會有新的值。
- 當 `Commit` 方法中包含緩動函式，而緩動函式傳回的值大於1時，動畫將會終止。 如果緩動函式傳回的值小於0，則此值會壓制為0。 若要使用會傳回小於0或大於1之值的緩動函式，它必須指定于其中一個子動畫，而不是在 `Commit` 方法中。

[`Animation`](xref:Xamarin.Forms.Animation)類別也包含[`WithConcurrent`](xref:Xamarin.Forms.Animation.WithConcurrent(Xamarin.Forms.Animation,System.Double,System.Double))方法，可用於將子動畫新增至父 `Animation` 物件。 不過，其*begin*和*finish*引數值不會限制為0到1，但只有對應到0到1範圍的子動畫部分，才會處於作用中狀態。 例如，如果 `WithConcurrent` 方法呼叫定義的子動畫的目標是從1到6的[`Scale`](xref:Xamarin.Forms.VisualElement.Scale)屬性，但具有-2 和3的*開始*和*結束*值，則-2 的*開始*值會對應至 `Scale` 值1，而*完成*值3則對應至6的 `Scale` 值。 由於0和1範圍以外的值不會在動畫中播放，因此 `Scale` 屬性只會從3到6的動畫。

## <a name="cancel-an-animation"></a>取消動畫

應用程式可以透過呼叫[`AbortAnimation`](xref:Xamarin.Forms.AnimationExtensions.AbortAnimation(Xamarin.Forms.IAnimatable,System.String))擴充方法來取消動畫，如下列程式碼範例所示：

```csharp
this.AbortAnimation ("SimpleAnimation");
```

請注意，動畫會由動畫擁有者和動畫名稱的組合來唯一識別。 因此，必須指定執行動畫時指定的擁有者和名稱，才能取消動畫。 因此，程式碼範例會立即取消名為 `SimpleAnimation` 的動畫，此為頁面所擁有。

## <a name="create-a-custom-animation"></a>建立自訂動畫

這裡所示的範例，示範了可以使用[`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions)類別中的方法來達成的動畫。 不過， [`Animation`](xref:Xamarin.Forms.Animation)類別的優點是它可以存取回呼方法，這會在動畫值變更時執行。 這可讓回呼執行任何想要的動畫。 例如，下列程式碼範例會將頁面的[`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor)屬性設定為[`Color`](xref:Xamarin.Forms.Color) [`Color.FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double))方法所建立的值，並以介於0到1的色調值進行動畫處理：

```csharp
new Animation (callback: v => BackgroundColor = Color.FromHsla (v, 1, 0.5),
  start: 0,
  end: 1).Commit (this, "Animation", 16, 4000, Easing.Linear, (v, c) => BackgroundColor = Color.Default);
```

產生的動畫會透過彩虹的色彩來提供頁面背景前進的外觀。

如需建立複雜動畫的更多範例，包括貝茲曲線動畫，請參閱[使用 Xamarin 建立 Mobile Apps](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)的第[22 章](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)。

## <a name="create-a-custom-animation-extension-method"></a>建立自訂動畫擴充方法

[`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions)類別中的擴充方法會從其目前值建立屬性的動畫，並將其設為指定的值。 這樣就很容易建立一個 `ColorTo` 動畫方法，用來在某個值之間建立色彩的動畫，因為：

- [`VisualElement`](xref:Xamarin.Forms.VisualElement)類別所定義的唯一[`Color`](xref:Xamarin.Forms.Color)屬性是[`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor)，這不一定是要建立動畫所需的 `Color` 屬性。
- 通常[`Color`](xref:Xamarin.Forms.Color)屬性的目前值是[`Color.Default`](xref:Xamarin.Forms.Color.Default)，這不是真實的色彩，而且無法用於插補計算中。

這個問題的解決方法是不讓 `ColorTo` 方法以特定[`Color`](xref:Xamarin.Forms.Color)屬性為目標。 相反地，它可以使用回呼方法來撰寫，將插補 `Color` 值傳遞回呼叫端。 此外，方法將會使用開始和結束 `Color` 引數。

`ColorTo` 方法可以實作為擴充方法，以在[`AnimationExtensions`](xref:Xamarin.Forms.AnimationExtensions)類別中使用[`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*)方法來提供其功能。 這是因為 `Animate` 方法可以用來將不屬於類型 `double`的屬性設為目標，如下列程式碼範例所示：

```csharp
public static class ViewExtensions
{
  public static Task<bool> ColorTo(this VisualElement self, Color fromColor, Color toColor, Action<Color> callback, uint length = 250, Easing easing = null)
  {
    Func<double, Color> transform = (t) =>
      Color.FromRgba(fromColor.R + t * (toColor.R - fromColor.R),
                     fromColor.G + t * (toColor.G - fromColor.G),
                     fromColor.B + t * (toColor.B - fromColor.B),
                     fromColor.A + t * (toColor.A - fromColor.A));
    return ColorAnimation(self, "ColorTo", transform, callback, length, easing);
  }

  public static void CancelAnimation(this VisualElement self)
  {
    self.AbortAnimation("ColorTo");
  }

  static Task<bool> ColorAnimation(VisualElement element, string name, Func<double, Color> transform, Action<Color> callback, uint length, Easing easing)
  {
    easing = easing ?? Easing.Linear;
    var taskCompletionSource = new TaskCompletionSource<bool>();

    element.Animate<Color>(name, transform, callback, 16, length, easing, (v, c) => taskCompletionSource.SetResult(c));
    return taskCompletionSource.Task;
  }
}
```

[`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*)方法需要*轉換*引數，這是回呼方法。 此回呼的輸入一律為範圍從0到1的 `double`。 因此，`ColorTo` 方法會定義它自己的轉換 `Func`，它會接受從0到1的 `double` 範圍，並傳回與該值對應的[`Color`](xref:Xamarin.Forms.Color)值。 `Color` 值的計算方式，是將這兩個所提供`A`引數的[`R`](xref:Xamarin.Forms.Color.R)、 [`G`](xref:Xamarin.Forms.Color.G)、 [`B`](xref:Xamarin.Forms.Color.B)和[`Color`](xref:Xamarin.Forms.Color.A)值插上。 然後，`Color` 值會傳遞給應用程式的回呼方法，以進行特定屬性。

這種方法可讓 `ColorTo` 方法以動畫顯示任何[`Color`](xref:Xamarin.Forms.Color)屬性，如下列程式碼範例所示：

```csharp
await Task.WhenAll(
  label.ColorTo(Color.Red, Color.Blue, c => label.TextColor = c, 5000),
  label.ColorTo(Color.Blue, Color.Red, c => label.BackgroundColor = c, 5000));
await this.ColorTo(Color.FromRgb(0, 0, 0), Color.FromRgb(255, 255, 255), c => BackgroundColor = c, 5000);
await boxView.ColorTo(Color.Blue, Color.Red, c => boxView.Color = c, 4000);
```

在此程式碼範例中，`ColorTo` 方法會以動畫呈現[`Label`](xref:Xamarin.Forms.Label)的[`TextColor`](xref:Xamarin.Forms.Label.TextColor)和[`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor)屬性、頁面的 `BackgroundColor` 屬性，以及[`Color`](xref:Xamarin.Forms.BoxView)的[`BoxView`](xref:Xamarin.Forms.BoxView.Color)屬性。

## <a name="related-links"></a>相關連結

- [自訂動畫（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-custom)
- [動畫 API](xref:Xamarin.Forms.Animation)
- [Petzold.animationextensions API](xref:Xamarin.Forms.AnimationExtensions)
