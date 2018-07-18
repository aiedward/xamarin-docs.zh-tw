---
title: 在 Xamarin.Forms 中的自訂動畫
description: 這篇文章會示範如何使用 Xamarin.FOrms 動畫類別來建立及取消動畫，並同步處理多個動畫，並建立自訂動畫顯示屬性不是由現有的動畫方法建立動畫的動畫。
ms.prod: xamarin
ms.assetid: 03B2E3FC-E720-4D45-B9A0-711081FC1907
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
ms.openlocfilehash: 519368031384e72a2d2e0a7c99053be44ea4cffc
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995215"
---
# <a name="custom-animations-in-xamarinforms"></a>在 Xamarin.Forms 中的自訂動畫

_動畫類別是所有的 Xamarin.Forms 動畫，以建立一或多個動畫物件 ViewExtensions 類別的擴充方法的建置組塊。這篇文章會示範如何使用動畫類別來建立及取消動畫，並同步處理多個動畫，並建立自訂動畫顯示屬性不是由現有的動畫方法建立動畫的動畫。_


必須在建立時，指定的參數數目`Animation`物件，包含正在繪製之屬性的開始和結束值和回呼，以變更屬性的值。 `Animation`物件也可以維護其子動畫可以執行並同步處理的集合。 如需詳細資訊，請參閱 <<c0> [ 其子動畫](#child)。

執行以建立動畫[ `Animation` ](xref:Xamarin.Forms.Animation)類別，這可能會或可能不包含其子動畫，之後，即可呼叫[ `Commit` ](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean}))方法。 這個方法指定的持續時間的動畫，並接手其他項目，可控制是否要重複動畫的回呼。

## <a name="creating-an-animation"></a>建立動畫

建立時[ `Animation` ](xref:Xamarin.Forms.Animation)物件時，通常至少三個參數是必要的如下列程式碼範例所示：

```csharp
var animation = new Animation (v => image.Scale = v, 1, 2);
```

此程式碼定義的動畫[ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale)屬性[ `Image` ](xref:Xamarin.Forms.Image)執行個體從值 1 的值為 2。 動畫的值，這 Xamarin.Forms 所衍生的會傳遞至做為第一個引數，指定的回呼，它用來變更值`Scale`屬性。

藉由呼叫啟動動畫[ `Commit` ](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean}))方法，如下列程式碼範例所示：

```csharp
animation.Commit (this, "SimpleAnimation", 16, 2000, Easing.Linear, (v, c) => image.Scale = 1, () => true);
```

請注意， [ `Commit` ](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean}))方法沒有傳回`Task`物件。 相反地，通知會透過回呼方法的方式提供。

下列引數中指定`Commit`方法：

- 第一個引數 (*擁有者*) 識別動畫的擁有者。 這可以是視覺化的項目套用動畫或另一個視覺元素的詳細資訊，例如頁面。
- 第二個引數 (*名稱*) 識別的名稱動畫。 名稱會與擁有者才能唯一識別動畫結合。 此唯一的識別可以再用來判斷動畫是否正在執行 ([`AnimationIsRunning`](xref:Xamarin.Forms.AnimationExtensions.AnimationIsRunning(Xamarin.Forms.IAnimatable,System.String)))，或將其取消即可 ([`AbortAnimation`](xref:Xamarin.Forms.AnimationExtensions.AbortAnimation(Xamarin.Forms.IAnimatable,System.String)))。
- 第三個引數 (*速率*) 表示的每個呼叫中定義的回呼方法之間的毫秒數[ `Animation` ](xref:Xamarin.Forms.Animation)建構函式
- 第四個引數 (*長度*) 指出動畫，以毫秒為單位的持續時間。
- 第五個引數 (*加/減速*) 定義要用於動畫的 easing 函式。 或者，指定的 easing 函式，做為引數[ `Animation` ](xref:Xamarin.Forms.Animation)建構函式。 如需有關 easing 函式的詳細資訊，請參閱[Easing 函式](~/xamarin-forms/user-interface/animation/easing.md)。
- 第六個引數 (*完成*) 是將動畫完成時執行的回呼。 此回呼會採用兩個引數，以指出最終的值，以及第二個引數的第一個引數`bool`設定為`true`如果動畫已取消。 或者，*完成*回呼可以指定為引數[ `Animation` ](xref:Xamarin.Forms.Animation)建構函式。 不過，透過單一動畫時，如果*完成*兩者都指定回呼`Animation`建構函式和`Commit`方法，只在指定的回呼`Commit`方法將會執行。
- 第七個引數 (*重複*) 是可讓重複動畫的回呼。 它會呼叫結尾的動畫，並傳回`true`表示應該重複動畫。

整體影響是若要建立動畫，從而[ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale)屬性[ `Image` ](xref:Xamarin.Forms.Image)從 1 到 2，超過 2 秒 （2000年毫秒），使用[ `Linear`](xref:Xamarin.Forms.Easing.Linear) easing 函式。 動畫完成時，每次其`Scale`屬性重設為 1，以及動畫重複一次。

> [!NOTE]
> 可建構並行的動畫，獨立執行，藉由建立`Animation`每個動畫的物件，然後呼叫`Commit`上每個動畫的方法。

<a name="child" />

### <a name="child-animations"></a>其子動畫

[ `Animation` ](xref:Xamarin.Forms.Animation)類別也支援其子動畫，其中包含建立`Animation`物件的其他`Animation`物件會加入。 這可讓一系列的動畫會執行，以及同步處理。 下列程式碼範例示範如何建立和執行其子動畫：

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

或者，在程式碼範例可以撰寫更簡潔，如下列程式碼範例所示：

```csharp
new Animation {
    { 0, 0.5, new Animation (v => image.Scale = v, 1, 2) },
    { 0, 1, new Animation (v => image.Rotation = v, 0, 360) },
    { 0.5, 1, new Animation (v => image.Scale = v, 2, 1) }
    }.Commit (this, "ChildAnimations", 16, 4000, null, (v, c) => SetIsEnabledButtonState (true, false));
```

在這兩個程式碼範例中，父代[ `Animation` ](xref:Xamarin.Forms.Animation)建立物件時，其他的`Animation`物件再加入。 前兩個引數[ `Add` ](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation))方法指定何時開始及完成子動畫。 將引數值必須介於 0 和 1，並代表相對的期間內可使用指定的子系動畫的父代動畫。 因此，在此範例中`scaleUpAnimation`是可使用的動畫的前半`scaleDownAnimation`的後半部的動畫，將會啟用而`rotateAnimation`後的整個持續期間。

整體影響是，動畫會超過 4 秒 （4000 毫秒為單位）。 `scaleUpAnimation`繪製[ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale)從 1 到 2，超過 2 秒的屬性。 `scaleDownAnimation`然後以動畫顯示`Scale`屬性從 2 設為 1，超過 2 秒。 這兩個小數位數動畫發生，而`rotateAnimation`繪製[ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation)屬性從 0 到 360，超過 4 秒。 請注意，調整動畫也會使用 easing 函式。 [ `SpringIn` ](xref:Xamarin.Forms.Easing.SpringIn) Easing 函式會導致[ `Image` ](xref:Xamarin.Forms.Image)最初壓縮再取得較大，而[ `SpringOut` ](xref:Xamarin.Forms.Easing.SpringOut) easing 函式會導致`Image`變成小於實際大小完整的動畫的結尾。

有一些之間的差異[ `Animation` ](xref:Xamarin.Forms.Animation)使用其子動畫的物件和不的一個：

- 使用其子動畫時*完成*回呼，在子系的動畫上指出子系已完成，而*完成*回呼傳遞至`Commit`方法表示已完成完整的動畫。
- 當使用其子動畫時，傳回`true`從*重複*回呼`Commit`方法並不會造成動畫重複，但動畫將會繼續執行而不需要新的值。
- 包括中的加/減速函式時`Commit`方法和 easing 函式傳回的值大於 1，動畫將會終止。 如果 easing 函式會傳回小於 0 的值，該值會限於 0。 若要使用 easing 函式傳回值小於 0 或大於 1，就必須指定在其中的子系動畫，而不是在`Commit`方法。

[ `Animation` ](xref:Xamarin.Forms.Animation)類別也包含[ `WithConcurrent` ](xref:Xamarin.Forms.Animation.WithConcurrent(Xamarin.Forms.Animation,System.Double,System.Double))方法，可用來將其子動畫加入至父代`Animation`物件。 不過，其*開始*並*完成*引數值不限制在 0 到 1，但只有該部分對應至範圍 0 到 1 的子系動畫將會啟用。 例如，如果`WithConcurrent`方法呼叫定義為目標的子系動畫[ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale)屬性從 1 到 6，但*開始*並*完成*的值-2 和 3，*開始*-2 值會對應到`Scale`值為 1，而*完成*值為 3 會對應到`Scale`6 的值。 0 和 1 的範圍以外的值沒有任何部分播放動畫，因為`Scale`屬性將只會從以動畫顯示 3 到 6。

## <a name="canceling-an-animation"></a>正在取消動畫

應用程式可以取消呼叫的動畫[ `AbortAnimation` ](xref:Xamarin.Forms.AnimationExtensions.AbortAnimation(Xamarin.Forms.IAnimatable,System.String))擴充方法，如下列程式碼範例所示：

```csharp
this.AbortAnimation ("SimpleAnimation");
```

請注意，動畫擁有者和動畫名稱的組合唯一識別動畫。 因此，擁有者和名稱會指定當執行動畫必須指定要取消的動畫。 因此，在程式碼範例將會立即取消名為動畫`SimpleAnimation`頁面所擁有。

## <a name="creating-a-custom-animation"></a>建立自訂的動畫

此處提供的範例到目前為止已證明同樣使用中的方法中達成的動畫[ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions)類別。 不過，善用[ `Animation` ](xref:Xamarin.Forms.Animation)類別是它有回呼方法中，當動畫的值變更時執行的存取。 這可讓回呼來實作任何所需的動畫。 例如，下列程式碼範例會展示動畫[ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor)屬性設定為頁面[ `Color` ](xref:Xamarin.Forms.Color)所建立的值[ `Color.FromHsla` ](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double))方法中，範圍從 0 到 1 的色調值：

```csharp
new Animation (callback: v => BackgroundColor = Color.FromHsla (v, 1, 0.5),
  start: 0,
  end: 1).Commit (this, "Animation", 16, 4000, Easing.Linear, (v, c) => BackgroundColor = Color.Default);
```

產生動畫有提供逐步引導到頁面背景色彩的 rainbow 透過的外觀。

如需建立複雜的動畫，包括貝茲曲線動畫的範例，請參閱[第 22 章](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)的[使用 Xamarin.Forms 建立行動應用程式](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)。

## <a name="creating-a-custom-animation-extension-method"></a>建立自訂動畫擴充方法

中的擴充方法[ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions)類別建立動畫從其目前值的屬性，指定的值。 這讓使用者難以建立，比方說，`ColorTo`因為可以用來以動畫顯示到另一個值色彩的動畫方法：

- 唯一[ `Color` ](xref:Xamarin.Forms.Color)屬性所定義[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)類別是[ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor)，但這並非絕對需要`Color`屬性若要以動畫顯示。
- 通常的目前值[ `Color` ](xref:Xamarin.Forms.Color)屬性是[ `Color.Default` ](xref:Xamarin.Forms.Color.Default)，哪些不是真實的色彩，就無法使用的內插補點計算。

此問題的解決方案是沒有`ColorTo`方法為目標的特定[ `Color` ](xref:Xamarin.Forms.Color)屬性。 相反地，通過插補有回呼方法寫入`Color`回呼叫端的值。 此外，方法會啟動，並結束`Color`引數。

`ColorTo`方法可以實作為擴充方法使用[ `Animate` ](xref:Xamarin.Forms.AnimationExtensions.Animate*)中的方法[ `AnimationExtensions` ](xref:Xamarin.Forms.AnimationExtensions)類別，以提供其功能。 這是因為`Animate`方法可用於目標屬性的型別不是`double`，如下列程式碼範例所示：

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

[ `Animate` ](xref:Xamarin.Forms.AnimationExtensions.Animate*)方法需要*轉換*引數，這是一個回呼方法。 此回呼中的輸入一律是`double`範圍從 0 到 1。 因此，`ColorTo`方法來定義自己的轉換`Func`它會接受`double`範圍從 0 到 1，且會傳回[ `Color` ](xref:Xamarin.Forms.Color)對應至該值的值。 `Color`透過在計算值[ `R` ](xref:Xamarin.Forms.Color.R)， [ `G` ](xref:Xamarin.Forms.Color.G)， [ `B` ](xref:Xamarin.Forms.Color.B)，以及[ `A`](xref:Xamarin.Forms.Color.A)提供兩個`Color`引數。 `Color`值接著會傳遞至回呼方法，讓應用程式特定的屬性。

此方法可讓`ColorTo`方法，以動畫顯示任何[ `Color` ](xref:Xamarin.Forms.Color)屬性，如下列程式碼範例所示：

```csharp
await Task.WhenAll(
  label.ColorTo(Color.Red, Color.Blue, c => label.TextColor = c, 5000),
  label.ColorTo(Color.Blue, Color.Red, c => label.BackgroundColor = c, 5000));
await this.ColorTo(Color.FromRgb(0, 0, 0), Color.FromRgb(255, 255, 255), c => BackgroundColor = c, 5000);
await boxView.ColorTo(Color.Blue, Color.Red, c => boxView.Color = c, 4000);
```

在此範例中，`ColorTo`方法以動畫顯示[ `TextColor` ](xref:Xamarin.Forms.Label.TextColor)並[ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor)屬性[ `Label` ](xref:Xamarin.Forms.Label)， `BackgroundColor`屬性頁面上，而[ `Color` ](xref:Xamarin.Forms.BoxView.Color)屬性[ `BoxView` ](xref:Xamarin.Forms.BoxView)。

## <a name="summary"></a>總結

這篇文章示範如何使用[ `Animation` ](xref:Xamarin.Forms.Animation)類別來建立和取消動畫、 同步處理多個動畫，並建立自訂動畫顯示屬性不是由現有動畫建立動畫的動畫方法。 `Animation`類別是所有的 Xamarin.Forms 動畫的建置組塊。


## <a name="related-links"></a>相關連結

- [自訂動畫 （範例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/animation/custom/)
- [動畫](xref:Xamarin.Forms.Animation)
- [AnimationExtensions](xref:Xamarin.Forms.AnimationExtensions)
