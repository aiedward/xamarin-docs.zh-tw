---
title: 中的簡單動畫 Xamarin.Forms
description: ViewExtensions 類別提供可用於建立簡單動畫的擴充方法。 本文將示範如何使用 ViewExtensions 類別來建立和取消動畫。
ms.prod: xamarin
ms.assetid: 4A6FAE5A-848F-4CE0-BFA1-22A6309B5225
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c3281db44d1c710e2fc971c75cf0e91a94ba12f2
ms.sourcegitcommit: 977b4f1b89d73b7773a25b9d8aba410d713bcab7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2021
ms.locfileid: "105298599"
---
# <a name="simple-animations-in-xamarinforms"></a>中的簡單動畫 Xamarin.Forms

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-animation-basic)

_ViewExtensions 類別提供可用於建立簡單動畫的擴充方法。本文將示範如何使用 ViewExtensions 類別來建立和取消動畫。_

[`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions)類別提供下列擴充方法，可用於建立簡單的動畫：

- [ `CancelAnimations` ] (x： Xamarin.Forms 。ViewExtensions. CancelAnimations (Xamarin.Forms 。VisualElement) ) 會取消任何動畫。
- [ `FadeTo` ] (x： Xamarin.Forms 。ViewExtensions. FadeTo (Xamarin.Forms 。VisualElement，system.string， Xamarin.Forms system.object。) ) 中的屬性建立動畫的簡化 [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。
- [ `RelScaleTo` ] (x： Xamarin.Forms 。ViewExtensions. RelScaleTo (Xamarin.Forms 。VisualElement，system.string， Xamarin.Forms system.object。「簡化」) ) 會將動畫增量增加或減少套用到的 [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) 屬性 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。
- [ `RotateTo` ] (x： Xamarin.Forms 。ViewExtensions. RotateTo (Xamarin.Forms 。VisualElement，system.string， Xamarin.Forms system.object。) ) 中的屬性建立動畫的簡化 [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。
- [ `RelRotateTo` ] (x： Xamarin.Forms 。ViewExtensions. RelRotateTo (Xamarin.Forms 。VisualElement，system.string， Xamarin.Forms system.object。「簡化」) ) 會將動畫增量增加或減少套用到的 [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) 屬性 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。
- [ `RotateXTo` ] (x： Xamarin.Forms 。ViewExtensions. RotateXTo (Xamarin.Forms 。VisualElement，system.string， Xamarin.Forms system.object。) ) 中的屬性建立動畫的簡化 [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX) [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。
- [ `RotateYTo` ] (x： Xamarin.Forms 。ViewExtensions. RotateYTo (Xamarin.Forms 。VisualElement，system.string， Xamarin.Forms system.object。) ) 中的屬性建立動畫的簡化 [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY) [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。
- [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) 以動畫呈現的 [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) 屬性 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。
- `ScaleXTo` 以動畫呈現的 [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX) 屬性 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。
- `ScaleYTo` 以動畫呈現的 [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY) 屬性 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。
- [ `TranslateTo` ] (x： Xamarin.Forms 。ViewExtensions. TranslateTo (Xamarin.Forms 。VisualElement、system.string、system.object、system.string、 Xamarin.Forms 。簡化) ) 將的 [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) 和 [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) 屬性動畫 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。

根據預設，每個動畫都會花費250毫秒。 不過，在建立動畫時，可以指定每個動畫的持續時間。

> [!NOTE]
> [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions)類別提供 [ `LayoutTo` ] (x： Xamarin.Forms 。ViewExtensions. LayoutTo (Xamarin.Forms 。VisualElement， Xamarin.Forms 。矩形， Xamarin.Forms system.object。簡化) ) 擴充方法。 不過，此方法的目的是要讓版面配置用來在包含大小和位置變更的版面配置狀態之間建立動畫轉換的動畫。 因此，它只能由子類別使用 [`Layout`](xref:Xamarin.Forms.Layout) 。

類別中的動畫擴充方法 [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) 全都是非同步，而且會傳回 `Task<bool>` 物件。 `false`如果動畫完成，則傳回值為， `true` 如果已取消動畫則為。 因此，動畫方法通常應該與運算子搭配使用 `await` ，讓您可以輕鬆地判斷動畫何時完成。 此外，您也可以使用在前一個方法完成後執行的後續動畫方法來建立順序動畫。 如需詳細資訊，請參閱 [複合動畫](#compound-animations)。

如果需要讓動畫在背景中完成，則 `await` 可以省略運算子。 在此案例中，動畫擴充方法會在起始動畫之後快速傳回，動畫會在背景中發生。 建立複合動畫時，可以利用這項作業。 如需詳細資訊，請參閱 [複合動畫](#composite-animations)。

如需有關運算子的詳細資訊 `await` ，請參閱 [非同步支援總覽](~/cross-platform/platform/async.md)。

## <a name="single-animations"></a>單一動畫

中的每個擴充方法 [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) 會在一段時間內，從某個值逐漸變更屬性到另一個值的單一動畫作業。 本節將探討每個動畫作業。

### <a name="rotation"></a>旋轉

下列程式碼範例將示範如何使用 [ `RotateTo` ] (x： Xamarin.Forms 。ViewExtensions. RotateTo (Xamarin.Forms 。VisualElement，system.string， Xamarin.Forms system.object。簡化) ) 方法以建立 [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) 屬性的動畫 [`Image`](xref:Xamarin.Forms.Image) ：

```csharp
await image.RotateTo (360, 2000);
image.Rotation = 0;
```

這段程式碼會藉 [`Image`](xref:Xamarin.Forms.Image) 由在2秒內最多旋轉360度來建立實例的動畫 (2000 毫秒) 。 [ `RotateTo` ] (x： Xamarin.Forms 。ViewExtensions. RotateTo (Xamarin.Forms 。VisualElement，system.string， Xamarin.Forms system.object。「簡化」) ) 方法會 [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) 取得動畫開頭的目前屬性值，然後從該值旋轉為其第一個引數 (360) 。 動畫完成時，影像的 [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) 屬性會重設為0。 這可確保在 `Rotation` 動畫結束之後，屬性不會維持在360，這會導致額外的旋轉。

下列螢幕擷取畫面顯示每個平臺上進行中的旋轉：

![旋轉動畫](simple-images/rotateto.png)

> [!NOTE]
> 除了 [ `RotateTo` ] (x： Xamarin.Forms 。ViewExtensions. RotateTo (Xamarin.Forms 。VisualElement，system.string， Xamarin.Forms system.object。簡化) ) 方法，也有 [ `RotateXTo` ] (x： Xamarin.Forms 。ViewExtensions. RotateXTo (Xamarin.Forms 。VisualElement，system.string， Xamarin.Forms system.object。簡化) ) 和 [ `RotateYTo` ] (x： Xamarin.Forms 。ViewExtensions. RotateYTo (Xamarin.Forms 。VisualElement，system.string， Xamarin.Forms system.object。簡化會分別為和屬性建立動畫的) ) 方法 [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX) [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY) 。

### <a name="relative-rotation"></a>相對旋轉

下列程式碼範例將示範如何使用 [ `RelRotateTo` ] (x： Xamarin.Forms 。ViewExtensions. RelRotateTo (Xamarin.Forms 。VisualElement，system.string， Xamarin.Forms system.object。簡化) ) 方法以累加方式增加或減少的 [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) 屬性 [`Image`](xref:Xamarin.Forms.Image) ：

```csharp
await image.RelRotateTo (360, 2000);
```

這段程式碼會在 [`Image`](xref:Xamarin.Forms.Image) 2 秒 (2000 毫秒) 的開始位置旋轉360度，以繪製實例的動畫。 [ `RelRotateTo` ] (x： Xamarin.Forms 。ViewExtensions. RelRotateTo (Xamarin.Forms 。VisualElement，system.string， Xamarin.Forms system.object。「簡化」) ) 方法會 [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) 取得動畫開頭的目前屬性值，然後從該值旋轉為值，再加上它的第一個引數 (360) 。 這可確保每個動畫一律會從開始位置旋轉360度。 因此，如果在動畫已在進行時叫用新的動畫，它會從目前的位置開始，而且可能會在非360度遞增的位置結束。

下列螢幕擷取畫面顯示每個平臺上進行中的相對旋轉：

![相對旋轉動畫](simple-images/relrotateto.png)

### <a name="scaling"></a>擴縮

下列程式碼範例將示範 [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) 如何使用方法，以動畫顯示的 [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) 屬性 [`Image`](xref:Xamarin.Forms.Image) ：

```csharp
await image.ScaleTo (2, 2000);
```

這段程式碼會藉 [`Image`](xref:Xamarin.Forms.Image) 由相應增加到2秒的大小兩倍（ (2000 毫秒) ）來動畫處理實例。 [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*)方法會 [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) 取得動畫開頭 (預設值 1) 的目前屬性值，然後從該值調整為第一個引數 (2) 。 這項效果會將影像的大小擴展至其大小的兩倍。

下列螢幕擷取畫面顯示每個平臺上進行的調整：

![調整動畫](simple-images/scaleto.png)

> [!NOTE]
> 除了方法之外，還有一些 [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) `ScaleXTo` 方法會分別為 `ScaleYTo` 和屬性建立動畫 [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX) [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY) 。

### <a name="relative-scaling"></a>相對調整

下列程式碼範例將示範如何使用 [ `RelScaleTo` ] (x： Xamarin.Forms 。ViewExtensions. RelScaleTo (Xamarin.Forms 。VisualElement，system.string， Xamarin.Forms system.object。簡化) ) 方法以建立 [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) 屬性的動畫 [`Image`](xref:Xamarin.Forms.Image) ：

```csharp
await image.RelScaleTo (2, 2000);
```

這段程式碼會藉 [`Image`](xref:Xamarin.Forms.Image) 由相應增加到2秒的大小兩倍（ (2000 毫秒) ）來動畫處理實例。 [ `RelScaleTo` ] (x： Xamarin.Forms 。ViewExtensions. RelScaleTo (Xamarin.Forms 。VisualElement，system.string， Xamarin.Forms system.object。「簡化」) ) 方法會 [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) 取得動畫開頭的目前屬性值，然後將該值從該值調整為值，再加上第一個引數 (2) 。 這可確保每個動畫一律會從開始位置調整為2。

### <a name="scaling-and-rotation-with-anchors"></a>使用錨點調整和旋轉

[`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX)和 [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) 屬性會設定和屬性的縮放或旋轉中心 [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) 。 因此，其值也會影響 [ `RotateTo` ] (x： Xamarin.Forms 。ViewExtensions. RotateTo (Xamarin.Forms 。VisualElement，system.string， Xamarin.Forms system.object。簡化) ) 和 [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) 方法。

如果已將 [`Image`](xref:Xamarin.Forms.Image) 置於配置的中央，下列程式碼範例將示範如何藉由設定其屬性來旋轉影像中心周圍的影像 [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) ：

```csharp
double radius = Math.Min(absoluteLayout.Width, absoluteLayout.Height) / 2;
image.AnchorY = radius / image.Height;
await image.RotateTo(360, 2000);
```

若要在 [`Image`](xref:Xamarin.Forms.Image) 版面配置的中央周圍旋轉實例， [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) 必須將和 [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) 屬性設定為相對於之寬度和高度的值 `Image` 。 在此範例中，的中心 `Image` 定義為位於配置的中央，因此預設 `AnchorX` 值0.5 不需要變更。 但是， `AnchorY` 屬性會重新定義為從頂端 `Image` 到版面配置中心點的值。 這可確保在 `Image` 版面配置的中心點周圍進行360度的完整旋轉，如下列螢幕擷取畫面所示：

![使用錨點旋轉動畫](simple-images/rotate-anchors.png)

### <a name="translation"></a>翻譯

下列程式碼範例將示範如何使用 [ `TranslateTo` ] (x： Xamarin.Forms 。ViewExtensions. TranslateTo (Xamarin.Forms 。VisualElement、system.string、system.object、system.string、 Xamarin.Forms 。簡化) ) 方法，以建立其 [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) 和 [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) 屬性的動畫 [`Image`](xref:Xamarin.Forms.Image) ：

```csharp
await image.TranslateTo (-100, -100, 1000);
```

這段程式碼 [`Image`](xref:Xamarin.Forms.Image) 會將實例以水準和垂直方式轉譯為1秒， (1000 毫秒) 來動畫。 [ `TranslateTo` ] (x： Xamarin.Forms 。ViewExtensions. TranslateTo (Xamarin.Forms 。VisualElement、system.string、system.object、system.string、 Xamarin.Forms 。「簡化」) ) 方法可同時將影像100圖元轉譯為左方，並將100圖元向上轉譯。 這是因為第一個和第二個引數都是負數。 提供正數會將影像向右和向下轉譯。

下列螢幕擷取畫面顯示每個平臺上正在進行的轉譯：

![轉譯動畫](simple-images/translateto.png)

> [!NOTE]
> 如果一開始就將某個元素配置出來，然後轉譯成螢幕，則在轉譯之後，專案的輸入版面配置會維持在螢幕上，而使用者無法與其互動。 因此，建議您應在其最終位置中配置視圖，然後執行任何必要的翻譯。

### <a name="fading"></a>淡出

下列程式碼範例將示範如何使用 [ `FadeTo` ] (x： Xamarin.Forms 。ViewExtensions. FadeTo (Xamarin.Forms 。VisualElement，system.string， Xamarin.Forms system.object。簡化) ) 方法以建立 [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) 屬性的動畫 [`Image`](xref:Xamarin.Forms.Image) ：

```csharp
image.Opacity = 0;
await image.FadeTo (1, 4000);
```

這段程式碼 [`Image`](xref:Xamarin.Forms.Image) 會在超過4秒的時間內將實例動畫 (4000 毫秒) 。 [ `FadeTo` ] (x： Xamarin.Forms 。ViewExtensions. FadeTo (Xamarin.Forms 。VisualElement，system.string， Xamarin.Forms system.object。「簡化」) ) 方法會 [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) 取得動畫開頭的目前屬性值，然後將該值從該值淡化為第一個引數 (1) 。

下列螢幕擷取畫面顯示每個平臺上的淡化進度：

![淡化動畫](simple-images/fadeto.png)

## <a name="compound-animations"></a>複合動畫

複合動畫是動畫的一連串組合，可以使用運算子來建立， `await` 如下列程式碼範例所示：

```csharp
await image.TranslateTo (-100, 0, 1000);    // Move image left
await image.TranslateTo (-100, -100, 1000); // Move image diagonally up and left
await image.TranslateTo (100, 100, 2000);   // Move image diagonally down and right
await image.TranslateTo (0, 100, 1000);     // Move image left
await image.TranslateTo (0, 0, 1000);       // Move image up
```

在此範例中， [`Image`](xref:Xamarin.Forms.Image) 會轉譯超過6秒 (6000 毫秒) 。 的轉譯會 `Image` 使用五個動畫，而 `await` 運算子會指出每個動畫會依序執行。 因此，後續的動畫方法會在上一個方法完成之後執行。

## <a name="composite-animations"></a>複合動畫

複合動畫是動畫的組合，其中兩個以上的動畫會同時執行。 複合動畫可透過混合等候和未等候的動畫來建立，如下列程式碼範例所示：

```csharp
image.RotateTo (360, 4000);
await image.ScaleTo (2, 2000);
await image.ScaleTo (1, 2000);
```

在此範例中， [`Image`](xref:Xamarin.Forms.Image) 會縮放並同時旋轉超過4秒 (4000 毫秒) 。 的縮放 `Image` 會使用兩個在相同時間與旋轉同時發生的連續動畫。 [ `RotateTo` ] (x： Xamarin.Forms 。ViewExtensions. RotateTo (Xamarin.Forms 。VisualElement，system.string， Xamarin.Forms system.object。在不使用運算子的情況下執行簡化) ) 方法， `await` 並在第一次動畫開始之前立即傳回 [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) 。 `await`第一個方法呼叫上的運算子會 `ScaleTo` 延遲第二個 `ScaleTo` 方法呼叫，直到第一個 `ScaleTo` 方法呼叫完成為止。 此時 `RotateTo` 動畫的一半已完成，並 `Image` 將旋轉180度。 在最後2秒 (2000 毫秒) ，第二個 `ScaleTo` 動畫和 `RotateTo` 動畫都會完成。

### <a name="running-multiple-asynchronous-methods-concurrently"></a>同時執行多個非同步方法

`static` `Task.WhenAny` 和 `Task.WhenAll` 方法是用來同時執行多個非同步方法，因此可以用來建立複合動畫。 這兩種方法都會傳回 `Task` 物件，並接受每個方法都會傳回物件的方法集合 `Task` 。 方法會在 `Task.WhenAny` 其集合中的任何方法完成執行時完成，如下列程式碼範例所示：

```csharp
await Task.WhenAny<bool>
(
  image.RotateTo (360, 4000),
  image.ScaleTo (2, 2000)
);
await image.ScaleTo (1, 2000);
```

在此範例中， `Task.WhenAny` 方法呼叫包含兩個工作。 第一個工作會將影像旋轉超過4秒 (4000 毫秒) ，而第二個工作則會將影像調整為2秒 (2000 毫秒) 。 當第二個工作完成時， `Task.WhenAny` 方法呼叫會完成。 不過，雖然 [ `RotateTo` ] (x： Xamarin.Forms 。ViewExtensions. RotateTo (Xamarin.Forms 。VisualElement，system.string， Xamarin.Forms system.object。可簡化) ) 方法仍在執行中，第二種 [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) 方法可以開始。

`Task.WhenAll`方法會在其集合中的所有方法都已完成時完成，如下列程式碼範例所示：

```csharp
// 10 minute animation
uint duration = 10 * 60 * 1000;

await Task.WhenAll (
  image.RotateTo (307 * 360, duration),
  image.RotateXTo (251 * 360, duration),
  image.RotateYTo (199 * 360, duration)
);
```

在此範例中， `Task.WhenAll` 方法呼叫包含三項工作，每個工作都會執行超過10分鐘。 每個都 `Task` 有不同數目的360度旋轉–307的 [ `RotateTo` ] 旋轉 (x： Xamarin.Forms 。ViewExtensions. RotateTo (Xamarin.Forms 。VisualElement，system.string， Xamarin.Forms system.object。簡化) ) ，251的旋轉 [ `RotateXTo` ] (x： Xamarin.Forms 。ViewExtensions. RotateXTo (Xamarin.Forms 。VisualElement，system.string， Xamarin.Forms system.object。將 [ `RotateYTo` ] (x：的) ) 和199旋轉簡化 Xamarin.Forms 。ViewExtensions. RotateYTo (Xamarin.Forms 。VisualElement，system.string， Xamarin.Forms system.object。簡化) ) 。 這些值是質數，因此可確保旋轉不會進行同步處理，因此不會產生重複的模式。

下列螢幕擷取畫面顯示每個平臺上的多個進行中的旋轉：

![複合動畫](simple-images/multiple-rotations.png)

## <a name="canceling-animations"></a>取消動畫

應用程式可以呼叫 [ `CancelAnimations` ] (x：來取消一個或多個動畫 Xamarin.Forms 。ViewExtensions. CancelAnimations (Xamarin.Forms 。VisualElement) ) 擴充方法，如下列程式碼範例所示：

```csharp
image.CancelAnimations();
```

這會立即取消目前實例上正在執行的所有動畫 [`Image`](xref:Xamarin.Forms.Image) 。

## <a name="summary"></a>總結

本文示範如何使用類別來建立和取消動畫 [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) 。 這個類別提供的擴充方法可用來建立可旋轉、縮放、轉譯和淡化實例的簡單動畫 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。

## <a name="related-links"></a>相關連結

- [非同步支援總覽](~/cross-platform/platform/async.md)
- [基本動畫 (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-animation-basic)
- [ViewExtensions](xref:Xamarin.Forms.ViewExtensions)
