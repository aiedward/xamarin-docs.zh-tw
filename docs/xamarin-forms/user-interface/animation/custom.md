---
title: 中的自訂動畫Xamarin.Forms
description: 本文示範如何使用 [Xamarin 動畫] 類別來建立和取消動畫、同步處理多個動畫，以及建立自訂動畫，以動畫顯示現有動畫方法不會產生動畫的屬性。
ms.prod: xamarin
ms.assetid: 03B2E3FC-E720-4D45-B9A0-711081FC1907
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/10/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 573f18de0d7593d832505eb6bb2b492caea024a1
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84946100"
---
# <a name="custom-animations-in-xamarinforms"></a>中的自訂動畫Xamarin.Forms

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-custom)

_動畫類別是所有動畫的建立區塊 Xamarin.Forms ，而 ViewExtensions 類別中的擴充方法會建立一或多個動畫物件。本文示範如何使用動畫類別來建立和取消動畫、同步處理多個動畫，以及建立自訂動畫，以動畫顯示現有動畫方法不會產生動畫的屬性。_

建立物件時，必須指定一些參數 `Animation` ，包括正在動畫之屬性的開始和結束值，以及變更屬性值的回呼。 `Animation`物件也可以維護可執行及同步處理的子動畫集合。 如需詳細資訊，請參閱[子動畫](#child-animations)。

藉 [`Animation`](xref:Xamarin.Forms.Animation) 由呼叫 [ `Commit` ] （x： Xamarin.Forms . 動畫. Commit （）來執行以類別建立的動畫（不一定會包含子動畫）。 Xamarin.FormsSystem.windows.media.animation.ianimatable>、System.string、system.object、system.string、 Xamarin.Forms 。緩動，system.string {system.string，system.string}，system.string {system.string}）方法的值。 這個方法會指定動畫的持續時間，以及其他專案之間，控制是否要重複動畫的回呼。

此外， [`Animation`](xref:Xamarin.Forms.Animation) 類別具有 `IsEnabled` 可供檢查的屬性，以判斷作業系統是否已停用動畫，例如啟用省電模式時。

## <a name="create-an-animation"></a>建立動畫

建立物件時 [`Animation`](xref:Xamarin.Forms.Animation) ，通常需要至少三個參數，如下列程式碼範例所示：

```csharp
var animation = new Animation (v => image.Scale = v, 1, 2);
```

此程式碼會將實例之屬性的動畫， [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) [`Image`](xref:Xamarin.Forms.Image) 從1的值定義為2的值。 所衍生的動畫值 Xamarin.Forms 會傳遞給指定為第一個引數的回呼，以用來變更屬性的值 `Scale` 。

動畫的啟動方式是呼叫 [ `Commit` ] （x： Xamarin.Forms . 動畫. Commit （） Xamarin.Forms 。System.windows.media.animation.ianimatable>、System.string、system.object、system.string、 Xamarin.Forms 。如下列程式碼範例所示，緩動、system.string {system.string、system.string}、System.object {system.string}）方法：

```csharp
animation.Commit (this, "SimpleAnimation", 16, 2000, Easing.Linear, (v, c) => image.Scale = 1, () => true);
```

請注意，[ `Commit` ] （x： Xamarin.Forms . 動畫. Commit （ Xamarin.Forms ）。System.windows.media.animation.ianimatable>、System.string、system.object、system.string、 Xamarin.Forms 。緩時，System.object {system.string，system.string}，system.string {system.string}））方法不會傳回 `Task` 物件。 相反地，會透過回呼方法來提供通知。

下列引數是在方法中指定的 `Commit` ：

- 第一個引數（*擁有*者）會識別動畫的擁有者。 這可以是套用動畫的視覺專案，或是另一個視覺元素（例如頁面）。
- 第二個引數（*name*）會以名稱來識別動畫。 名稱會與擁有者結合，以唯一識別動畫。 此唯一識別可接著用來判斷動畫是否正在執行（[ `AnimationIsRunning` ] （x：） Xamarin.Forms 。Petzold.animationextensions. AnimationIsRunning （ Xamarin.Forms 。System.windows.media.animation.ianimatable>、System.string）），或取消它（[ `AbortAnimation` ] （x：） Xamarin.Forms 。Petzold.animationextensions. AbortAnimation （ Xamarin.Forms 。System.windows.media.animation.ianimatable>，System.string）））。
- 第三個引數（*速率*）表示每次呼叫在此函式中定義的回呼方法之間的毫秒數 [`Animation`](xref:Xamarin.Forms.Animation) 。
- 第四個引數（*長度*）表示動畫的持續時間（以毫秒為單位）。
- 第五個引數（*緩動*）定義要在動畫中使用的緩動函式。 或者，您也可以將緩動函式指定為對此函數的引數 [`Animation`](xref:Xamarin.Forms.Animation) 。 如需緩時函數的詳細資訊，請參閱[簡化](~/xamarin-forms/user-interface/animation/easing.md)函式。
- 第六個引數（*完成*）是當動畫完成時，將會執行的回呼。 這個回呼會採用兩個引數，其中第一個引數表示最後一個值，而第二個引數則是 `bool` ， `true` 如果動畫已取消，則會設定為。 或者，您可以將*完成*的回呼指定為函式的引數 [`Animation`](xref:Xamarin.Forms.Animation) 。 不過，只要使用單一動畫，如果*已完成*回呼同時在函式 `Animation` 和方法中指定 `Commit` ，就只會執行方法中指定的回呼 `Commit` 。
- 第七個引數（*重複*）是允許重複動畫的回呼。 它會在動畫結束時呼叫，並傳回 `true` 表示動畫應重複。

整體效果是建立一個動畫， [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) 使用緩動函式，將的屬性 [`Image`](xref:Xamarin.Forms.Image) 從1增加為2秒（2000毫秒） [`Linear`](xref:Xamarin.Forms.Easing.Linear) 。 每次動畫完成時，其 `Scale` 屬性都會重設為1，且動畫會重複。

> [!NOTE]
> 並行的動畫（彼此獨立執行）可以藉由 `Animation` 為每個動畫建立一個物件，然後 `Commit` 在每個動畫上呼叫方法來加以構建。

### <a name="child-animations"></a>子動畫

[`Animation`](xref:Xamarin.Forms.Animation)類別也支援子動畫，這牽涉到建立 `Animation` 其他 `Animation` 物件所要加入的物件。 這可讓一系列的動畫執行並同步處理。 下列程式碼範例示範如何建立和執行子動畫：

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

在這兩個程式碼範例中， [`Animation`](xref:Xamarin.Forms.Animation) 會建立父物件， `Animation` 然後再加入其他物件。 [ `Add` ] （X：）的前兩個引數 Xamarin.Forms 。動畫。 Add （System.object， Xamarin.Forms double，，，動畫））方法指定開始和完成子動畫的時機。 引數值必須介於0和1之間，而且代表父動畫中指定子動畫將使用的相對期間。 因此，在此範例中，將會在動畫的前半部作用中，將會在動畫的後半部作用中， `scaleUpAnimation` `scaleDownAnimation` 而且會在 `rotateAnimation` 整個持續時間內生效。

整體效果是動畫會在4秒（4000毫秒）發生。 會在 `scaleUpAnimation` [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) 2 秒內，從1到2繪製屬性的動畫。 然後會在 `scaleDownAnimation` `Scale` 2 秒內，從2到1繪製屬性的動畫。 同時發生這兩個調整動畫時，會在 `rotateAnimation` [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) 4 秒內從0到360繪製屬性的動畫。 請注意，縮放動畫也會使用緩動函數。 「 [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn) 緩時變」函式會導致 [`Image`](xref:Xamarin.Forms.Image) 最初縮小，然後才會變大，而緩動函式則 [`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut) 會使小於 `Image` 其實際大小接近完成動畫的結尾。

[`Animation`](xref:Xamarin.Forms.Animation)使用子動畫的物件與不會產生下列各項之間有一些差異：

- 使用子動畫時，子動畫上*完成*的回呼會指出子系完成的時間，而傳遞至方法的*完成*回呼則會 `Commit` 指出整個動畫完成的時間。
- 使用子動畫時， `true` 從方法的*重複*回呼傳回 `Commit` 不會導致動畫重複，但是動畫將會繼續執行，而不會有新的值。
- 在方法中包含緩動函式 `Commit` ，而緩動函式傳回的值大於1時，動畫將會終止。 如果緩動函式傳回的值小於0，則此值會壓制為0。 若要使用會傳回小於0或大於1之值的緩動函式，它必須在其中一個子動畫中指定，而不是在 `Commit` 方法中。

[`Animation`](xref:Xamarin.Forms.Animation)類別也包含 [ `WithConcurrent` ] （x： Xamarin.Forms 。WithConcurrent （ Xamarin.Forms 。[動畫]、[Double]、[system.string]）方法，可用來將子動畫新增至父 `Animation` 物件。 不過，其*begin*和*finish*引數值不會限制為0到1，但只有對應到0到1範圍的子動畫部分，才會處於作用中狀態。 例如，如果 `WithConcurrent` 方法呼叫定義以1到6的屬性為目標的子動畫 [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) ，但具有-2 和3的*開始*和*結束*值，則-2 的*開始*值會對應至 `Scale` 值1，而*完成*值3則會對應至 `Scale` 值6。 由於0和1範圍以外的值不會在動畫中播放，因此 `Scale` 屬性只會從3到6的動畫。

## <a name="cancel-an-animation"></a>取消動畫

應用程式可以透過呼叫 [ `AbortAnimation` ] （x：）來取消動畫 Xamarin.Forms 。Petzold.animationextensions. AbortAnimation （ Xamarin.Forms 。System.windows.media.animation.ianimatable>，System.string））擴充方法，如下列程式碼範例所示：

```csharp
this.AbortAnimation ("SimpleAnimation");
```

請注意，動畫會由動畫擁有者和動畫名稱的組合來唯一識別。 因此，必須指定執行動畫時指定的擁有者和名稱，才能取消動畫。 因此，程式碼範例會立即取消名為的動畫，其為 `SimpleAnimation` 頁面所擁有。

## <a name="create-a-custom-animation"></a>建立自訂動畫

這裡所示的範例，示範了可以使用類別中的方法來達成的動畫 [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) 。 不過，類別的優點 [`Animation`](xref:Xamarin.Forms.Animation) 是它可以存取回呼方法，這會在動畫值變更時執行。 這可讓回呼執行任何想要的動畫。 例如，下列程式碼範例 [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) 會藉由將頁面的屬性設定為方法所建立的值來繪製動畫 [`Color`](xref:Xamarin.Forms.Color) [`Color.FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double)) ，而色調值的範圍從0到1：

```csharp
new Animation (callback: v => BackgroundColor = Color.FromHsla (v, 1, 0.5),
  start: 0,
  end: 1).Commit (this, "Animation", 16, 4000, Easing.Linear, (v, c) => BackgroundColor = Color.Default);
```

產生的動畫會透過彩虹的色彩來提供頁面背景前進的外觀。

如需建立複雜動畫的更多範例，包括貝茲曲線動畫，請參閱[使用 Xamarin.Forms 建立 Mobile Apps](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)的第[22 章](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)。

## <a name="create-a-custom-animation-extension-method"></a>建立自訂動畫擴充方法

類別中的擴充方法會 [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) 從其目前值建立屬性的動畫，並將其設為指定的值。 這樣就很難以建立一個動畫方法，例如， `ColorTo` 可以用來以動畫顯示某個值的色彩到另一個值，因為：

- 類別所 [`Color`](xref:Xamarin.Forms.Color) 定義的唯一屬性 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 是 [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) ，這不一定是要建立動畫的所需 `Color` 屬性。
- 屬性的目前值通常 [`Color`](xref:Xamarin.Forms.Color) 是 [`Color.Default`](xref:Xamarin.Forms.Color.Default) ，這不是真正的色彩，而且無法用於插補計算中。

這個問題的解決方法是，沒有以 `ColorTo` 特定屬性為目標的方法 [`Color`](xref:Xamarin.Forms.Color) 。 相反地，它可以使用回呼方法來撰寫，將插補 `Color` 值傳遞回呼叫端。 此外，此方法會採用開始和結束 `Color` 引數。

`ColorTo`方法可以實作為擴充方法，以使用 [`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*) 類別中的方法 [`AnimationExtensions`](xref:Xamarin.Forms.AnimationExtensions) 來提供其功能。 這是因為 `Animate` 方法可以用來將不屬於類型的屬性設為目標 `double` ，如下列程式碼範例所示：

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

[`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*)方法需要*轉換*引數，這是回呼方法。 此回呼的輸入一律是 `double` 從0到1的範圍。 因此，此 `ColorTo` 方法會定義它自己的轉換 `Func` ，它會接受 `double` 從0到1的範圍，並傳回與該值 [`Color`](xref:Xamarin.Forms.Color) 對應的值。 `Color`值的計算方式是將 [`R`](xref:Xamarin.Forms.Color.R) [`G`](xref:Xamarin.Forms.Color.G) [`B`](xref:Xamarin.Forms.Color.B) [`A`](xref:Xamarin.Forms.Color.A) 這兩個所提供引數的、、和值插上 `Color` 。 然後，此 `Color` 值會傳遞給應用程式的回呼方法，以進行特定屬性。

這種方法可讓 `ColorTo` 方法以動畫顯示任何 [`Color`](xref:Xamarin.Forms.Color) 屬性，如下列程式碼範例所示：

```csharp
await Task.WhenAll(
  label.ColorTo(Color.Red, Color.Blue, c => label.TextColor = c, 5000),
  label.ColorTo(Color.Blue, Color.Red, c => label.BackgroundColor = c, 5000));
await this.ColorTo(Color.FromRgb(0, 0, 0), Color.FromRgb(255, 255, 255), c => BackgroundColor = c, 5000);
await boxView.ColorTo(Color.Blue, Color.Red, c => boxView.Color = c, 4000);
```

在此程式碼範例中，方法會以動畫呈現的 `ColorTo` [`TextColor`](xref:Xamarin.Forms.Label.TextColor) 和 [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) 屬性 [`Label`](xref:Xamarin.Forms.Label) 、 `BackgroundColor` 頁面的屬性，以及的 [`Color`](xref:Xamarin.Forms.BoxView.Color) 屬性 [`BoxView`](xref:Xamarin.Forms.BoxView) 。

## <a name="related-links"></a>相關連結

- [自訂動畫（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-custom)
- [動畫 API](xref:Xamarin.Forms.Animation)
- [Petzold.animationextensions API](xref:Xamarin.Forms.AnimationExtensions)
