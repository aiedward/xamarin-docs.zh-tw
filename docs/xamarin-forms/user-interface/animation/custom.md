---
title: 自訂動畫
description: 動畫類別是所有 Xamarin.Forms 動畫 ViewExtensions 類別建立一個或多個動畫物件的擴充方法的建置組塊。 本文將示範如何使用動畫類別來建立和取消動畫、 同步處理多個動畫，以及建立自訂的動畫，而不現有動畫方法所繪製的屬性以動畫顯示。
ms.prod: xamarin
ms.assetid: 03B2E3FC-E720-4D45-B9A0-711081FC1907
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
ms.openlocfilehash: 238268a3ad2b82494f1d096d0cbeba97edb90366
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/07/2018
ms.locfileid: "34847780"
---
# <a name="custom-animations"></a>自訂動畫

_動畫類別是所有 Xamarin.Forms 動畫 ViewExtensions 類別建立一個或多個動畫物件的擴充方法的建置組塊。本文將示範如何使用動畫類別來建立和取消動畫、 同步處理多個動畫，以及建立自訂的動畫，而不現有動畫方法所繪製的屬性以動畫顯示。_


建立時，就必須指定的參數數目`Animation`物件，包括正在顯示動畫之屬性的開始和結束值和屬性的值變更的回呼。 `Animation`物件也可讓您可以執行，並同步處理的子組的集合。 如需詳細資訊，請參閱[其子動畫](#child)。

執行以建立動畫[ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/)類別，這可能包括或不包括子動畫，達成方式是呼叫[ `Commit` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Commit/p/Xamarin.Forms.IAnimatable/System.String/System.UInt32/System.UInt32/Xamarin.Forms.Easing/System.Action{System.Double,System.Boolean}/System.Func{System.Boolean}/)方法。 這個方法指定的持續時間的動畫，並在其他項目，控制是否要重複動畫的回呼。

## <a name="creating-an-animation"></a>建立動畫

建立時[ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/)物件時，一般而言，至少三個參數是必要的如下列程式碼範例所示：

```csharp
var animation = new Animation (v => image.Scale = v, 1, 2);
```

此程式碼定義的動畫[ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)屬性[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)執行個體從值 1 的值為 2。 動畫的值，這透過 Xamarin.Forms 衍生，會傳遞至回呼指定為第一個引數，它用於將值變更`Scale`屬性。

呼叫會啟動動畫[ `Commit` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Commit/p/Xamarin.Forms.IAnimatable/System.String/System.UInt32/System.UInt32/Xamarin.Forms.Easing/System.Action{System.Double,System.Boolean}/System.Func{System.Boolean}/)方法，如下列程式碼範例所示：

```csharp
animation.Commit (this, "SimpleAnimation", 16, 2000, Easing.Linear, (v, c) => image.Scale = 1, () => true);
```

請注意， [ `Commit` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Commit/p/Xamarin.Forms.IAnimatable/System.String/System.UInt32/System.UInt32/Xamarin.Forms.Easing/System.Action{System.Double,System.Boolean}/System.Func{System.Boolean}/)方法不會傳回`Task`物件。 相反地，透過回呼方法提供通知。

下列引數中指定`Commit`方法：

- 第一個引數 (*擁有者*) 識別動畫的擁有者。 這可以是在其上動畫套用瞬間的視覺項目或另一個視覺項目，例如頁。
- 第二個引數 (*名稱*) 識別的名稱動畫。 結合來唯一識別動畫的擁有者名稱。 唯一識別可以再用來判斷是否正在執行動畫 ([`AnimationIsRunning`](https://developer.xamarin.com/api/member/Xamarin.Forms.AnimationExtensions.AnimationIsRunning/p/Xamarin.Forms.IAnimatable/System.String/))，或取消 ([`AbortAnimation`](https://developer.xamarin.com/api/member/Xamarin.Forms.AnimationExtensions.AbortAnimation/p/Xamarin.Forms.IAnimatable/System.String/))。
- 第三個引數 (*速率*) 表示的定義中的回呼方法每次呼叫之間的毫秒數[ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/)建構函式
- 第四個引數 (*長度*) 表示動畫，以毫秒為單位的持續時間。
- 第五個引數 (*easing*) 會定義要用於動畫的 easing 函式。 或者，可以指定 easing 函式的引數為[ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/)建構函式。 如需 easing 函式的詳細資訊，請參閱[Easing 函式](~/xamarin-forms/user-interface/animation/easing.md)。
- 第六個引數 (*完成*) 是將在動畫完成時執行的回呼。 此回呼會採用兩個引數，表示最後的值，而且第二個引數的第一個引數`bool`設為`true`取消動畫。 或者，*完成*回呼可以指定的引數為[ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/)建構函式。 不過，在使用單一的動畫，如果*完成*中同時指定回呼`Animation`建構函式和`Commit`方法，只在指定的回呼`Commit`方法將會執行。
- 第七個引數 (*重複*) 是可讓重複動畫的回呼。 它稱為結尾的動畫，並傳回`true`表示應該重複動畫。

整體的作用是建立動畫，從而[ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)屬性[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)從 1 到 2，超過 2 秒 （2000年毫秒為單位），使用[ `Linear`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.Linear/) easing 函式。 在動畫完成時，每次它`Scale`屬性會重設為 1，並在動畫重複。

> [!NOTE]
> 可以藉由建立建構並行的動畫，彼此執行`Animation`物件每一個動畫，然後再呼叫`Commit`上每個動畫方法。

<a name="child" />

### <a name="child-animations"></a>其子動畫

[ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/)類別也支援子動畫牽涉到建立`Animation`物件的其他`Animation`物件就會加入。 這可讓一系列的動畫執行，並同步處理。 下列程式碼範例示範如何建立和執行其子動畫：

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

或者，可以更精確地，為下列程式碼範例示範撰寫程式碼範例：

```csharp
new Animation {
    { 0, 0.5, new Animation (v => image.Scale = v, 1, 2) },
    { 0, 1, new Animation (v => image.Rotation = v, 0, 360) },
    { 0.5, 1, new Animation (v => image.Scale = v, 2, 1) }
    }.Commit (this, "ChildAnimations", 16, 4000, null, (v, c) => SetIsEnabledButtonState (true, false));
```

在這兩個程式碼範例中，父代[ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/)建立物件時，其他的`Animation`物件接著會加入。 前兩個引數[ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Add/p/System.Double/System.Double/Xamarin.Forms.Animation/)方法指定何時開始和完成子動畫。 引數值必須介於 0 和 1，並代表相對的期間內指定的子系動畫為作用中的父動畫。 因此，在此範例`scaleUpAnimation`的前半的動畫，將會啟用`scaleDownAnimation`的後半的動畫，將會啟用而`rotateAnimation`後的整個持續時間。

整體結果會是動畫發生超過 4 秒 （4000 毫秒為單位）。 `scaleUpAnimation`繪製[ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)屬性從 1 到 2，超過 2 秒。 `scaleDownAnimation`然後繪製`Scale`為 1，2 秒超過 2 屬性。 會發生兩個小數位數的動畫，`rotateAnimation`繪製[ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/)屬性從 0 到 360，超過 4 秒。 請注意，縮放動畫也會使用加/減速函數。 [ `SpringIn` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringIn/) Easing 函式會導致[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)才可以取得更大，最初壓縮和[ `SpringOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringOut/) easing 函式會導致`Image`變成小於結束時完成動畫的實際大小。

有許多差異的[ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/)其子動畫所使用物件，另一個則不會：

- 使用子動畫時*完成*子動畫回呼指出已完成子系，而*完成*回呼傳遞至`Commit`方法指出何時已完成完整的動畫。
- 當使用其子動畫時，傳回`true`從*重複*上的回呼`Commit`方法不會造成動畫重複，但動畫會繼續執行不含新值。
- 在包含 easing 函式中的`Commit`方法，並加/減速函數傳回的值大於 1，將會終止動畫。 如果 easing 函式會傳回小於 0 的值，該值會限於 0。 若要使用加/減速函數會傳回值小於 0 或大於 1 時，它必須指定其中一個子動畫，而不是在`Commit`方法。

[ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/)類別還包括[ `WithConcurrent` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.WithConcurrent/p/Xamarin.Forms.Animation/System.Double/System.Double/)方法，用於將其子動畫加入至父代`Animation`物件。 不過，其*開始*和*完成*引數值不是限制為 0 到 1，但是只有一部分會對應到範圍 0 到 1 的子系動畫，將會啟用。 例如，如果`WithConcurrent`方法呼叫會定義為目標的子系動畫[ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)屬性從 1 到 6，但與*開始*和*完成*的值-2 和 3，*開始*-2 的值會對應到`Scale`值為 1，而*完成*3 的值會對應到`Scale`6 的值。 因為 0 和 1 的範圍以外的值將沒有任何部分播放的動畫，`Scale`屬性將只動畫顯示介於 3 到 6。

## <a name="canceling-an-animation"></a>取消動畫

應用程式可以取消呼叫動畫[ `AbortAnimation` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AnimationExtensions.AbortAnimation/p/Xamarin.Forms.IAnimatable/System.String/)擴充方法，如下列程式碼範例所示：

```csharp
this.AbortAnimation ("SimpleAnimation");
```

請注意，動畫會唯一識別由動畫擁有者和動畫名稱的組合。 因此，擁有人和名稱會指定當執行動畫必須指定要取消動畫。 因此，程式碼範例將會立即取消名為動畫`SimpleAnimation`頁面所擁有。

## <a name="creating-a-custom-animation"></a>建立自訂動畫

此處提供的範例到目前為止已示範動畫中的方法與同樣可達到[ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/)類別。 不過，利用[ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/)類別是確認它具有存取權的動畫的值變更時執行回呼方法。 這可讓實作任何所需的動畫回呼。 例如，下列程式碼範例以動畫顯示[ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.BackgroundColor/)屬性設定為頁面的[ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/)值由[ `Color.FromHsla` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromHsla/p/System.Double/System.Double/System.Double/System.Double/)方法，並包含範圍從 0 到 1 的色調值：

```csharp
new Animation (callback: v => BackgroundColor = Color.FromHsla (v, 1, 0.5),
  start: 0,
  end: 1).Commit (this, "Animation", 16, 4000, Easing.Linear, (v, c) => BackgroundColor = Color.Default);
```

所產生的動畫提供提前彩虹圖案的色彩會透過網頁背景的外觀。

如需其他範例，建立複雜的動畫，包括貝茲曲線的動畫，請參閱[章 22](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)的[建立行動應用程式使用 Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)。

## <a name="creating-a-custom-animation-extension-method"></a>建立自訂動畫擴充方法

中的擴充方法[ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/)類別建立從目前的值屬性設為指定值的動畫。 這讓使用者難以建立，例如`ColorTo`動畫方法可以用來以動畫顯示到另一個值從色彩中，因為：

- 唯一[ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/)屬性所定義[ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/)類別是[ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.BackgroundColor/)，永遠不想要的`Color`屬性若要製作動畫。
- 通常的目前值[ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/)屬性是[ `Color.Default` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Default/)，這不是實際的色彩，就無法使用插補計算中。

這個問題的解決方案為沒有`ColorTo`方法的目標特定[ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/)屬性。 相反地，與傳遞插值回呼方法寫入`Color`回呼叫端的值。 此外，方法會啟動，並結束`Color`引數。

`ColorTo`方法可以實作為擴充方法使用[ `Animate` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AnimationExtensions.Animate{T}/p/Xamarin.Forms.IAnimatable/System.String/System.Func{System.Double,T}/System.Action{T}/System.UInt32/System.UInt32/Xamarin.Forms.Easing/System.Action{T,System.Boolean}/System.Func{System.Boolean}/)方法中的[ `AnimationExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AnimationExtensions/)類別，以提供它的功能。 這是因為`Animate`方法可以用於目標屬性的型別不是`double`，如下列程式碼範例所示：

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

[ `Animate` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AnimationExtensions.Animate{T}/p/Xamarin.Forms.IAnimatable/System.String/System.Func{System.Double,T}/System.Action{T}/System.UInt32/System.UInt32/Xamarin.Forms.Easing/System.Action{T,System.Boolean}/System.Func{System.Boolean}/)方法需要*轉換*引數，這是一種回呼方法。 此回呼的輸入是一律`double`範圍從 0 到 1。 因此，`ColorTo`方法來定義自己的轉換`Func`接受`double`範圍從 0 到 1，且傳回[ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/)與該值對應值。 `Color`值由插入計算[ `R` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.R/)， [ `G` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.G/)， [ `B` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.B/)，和[ `A`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.A/)提供兩個值`Color`引數。 `Color`值接著會傳遞至回呼方法，讓應用程式特定的屬性。

這個方法可讓`ColorTo`方法，以動畫顯示任何[ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/)屬性，如下列程式碼範例所示：

```csharp
await Task.WhenAll(
  label.ColorTo(Color.Red, Color.Blue, c => label.TextColor = c, 5000),
  label.ColorTo(Color.Blue, Color.Red, c => label.BackgroundColor = c, 5000));
await this.ColorTo(Color.FromRgb(0, 0, 0), Color.FromRgb(255, 255, 255), c => BackgroundColor = c, 5000);
await boxView.ColorTo(Color.Blue, Color.Red, c => boxView.Color = c, 4000);
```

在此程式碼範例中，`ColorTo`方法以動畫方式顯示[ `TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.TextColor/)和[ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.BackgroundColor/)屬性[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)， `BackgroundColor`屬性] 頁面上，而[ `Color` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BoxView.Color/)屬性[ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/)。

## <a name="summary"></a>總結

本文示範如何使用[ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/)類別來建立和取消動畫、 同步處理多個動畫，並建立自訂的動畫，而不由現有動畫展示動畫的屬性以動畫顯示方法。 `Animation`類別是所有 Xamarin.Forms 動畫的建置組塊。


## <a name="related-links"></a>相關連結

- [自訂動畫 （範例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/animation/custom/)
- [動畫](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/)
- [AnimationExtensions](https://developer.xamarin.com/api/type/Xamarin.Forms.AnimationExtensions/)
