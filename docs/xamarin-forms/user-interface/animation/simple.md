---
title: 中的簡單動畫Xamarin.Forms
description: ViewExtensions 類別提供可用來建立簡單動畫的擴充方法。 本文示範如何使用 ViewExtensions 類別來建立和取消動畫。
ms.prod: xamarin
ms.assetid: 4A6FAE5A-848F-4CE0-BFA1-22A6309B5225
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/05/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 89f0182bfebc4d018eb083904595f0fbb211e3ba
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84573374"
---
# <a name="simple-animations-in-xamarinforms"></a>中的簡單動畫Xamarin.Forms

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-basic)

_ViewExtensions 類別提供可用來建立簡單動畫的擴充方法。本文示範如何使用 ViewExtensions 類別來建立和取消動畫。_

[`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions)類別提供下列擴充方法，可用於建立簡單的動畫：

- [ `TranslateTo` ] （x： Xamarin.Forms 。ViewExtensions. TranslateTo （ Xamarin.Forms 。VisualElement，Double，system.string， Xamarin.Forms ，，簡化））動畫的 [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) 和 [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) 屬性 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。
- [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*)以動畫呈現的 [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) 屬性 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。
- `ScaleXTo`以動畫呈現的 [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX) 屬性 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。
- `ScaleYTo`以動畫呈現的 [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY) 屬性 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。
- [ `RelScaleTo` ] （x： Xamarin.Forms 。ViewExtensions. RelScaleTo （ Xamarin.Forms 。VisualElement，System.object，， Xamarin.Forms 。緩時）會將動畫增量增加或減少套用至的 [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) 屬性 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。
- [ `RotateTo` ] （x： Xamarin.Forms 。ViewExtensions. RotateTo （ Xamarin.Forms 。VisualElement，System.object，， Xamarin.Forms 。簡化））繪製 [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) 之屬性的動畫 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。
- [ `RelRotateTo` ] （x： Xamarin.Forms 。ViewExtensions. RelRotateTo （ Xamarin.Forms 。VisualElement，System.object，， Xamarin.Forms 。緩時）會將動畫增量增加或減少套用至的 [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) 屬性 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。
- [ `RotateXTo` ] （x： Xamarin.Forms 。ViewExtensions. RotateXTo （ Xamarin.Forms 。VisualElement，System.object，， Xamarin.Forms 。簡化））繪製 [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX) 之屬性的動畫 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。
- [ `RotateYTo` ] （x： Xamarin.Forms 。ViewExtensions. RotateYTo （ Xamarin.Forms 。VisualElement，System.object，， Xamarin.Forms 。簡化））繪製 [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY) 之屬性的動畫 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。
- [ `FadeTo` ] （x： Xamarin.Forms 。ViewExtensions. FadeTo （ Xamarin.Forms 。VisualElement，System.object，， Xamarin.Forms 。簡化））繪製 [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) 之屬性的動畫 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。

根據預設，每個動畫將需要250毫秒。 不過，建立動畫時，可以指定每個動畫的持續時間。

[`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions)類別也包含 [ `CancelAnimations` ] （x： Xamarin.Forms 。ViewExtensions. CancelAnimations （ Xamarin.Forms 。VisualElement））方法，可用於取消任何動畫。

> [!NOTE]
> [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions)類別提供 [ `LayoutTo` ] （x： Xamarin.Forms 。ViewExtensions. LayoutTo （ Xamarin.Forms 。VisualElement， Xamarin.Forms 。矩形，System.object， Xamarin.Forms 。緩動））擴充方法。 不過，此方法的目的是要供版面配置使用，以在包含大小和位置變更的版面配置狀態之間建立動畫轉換。 因此，它應該僅供子 [`Layout`](xref:Xamarin.Forms.Layout) 類別使用。

類別中的動畫擴充方法 [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) 全都是非同步，而且會傳回 `Task<bool>` 物件。 `false`如果動畫完成，則傳回值為， `true` 如果動畫已取消，則為。 因此，動畫方法通常應該與運算子搭配使用 `await` ，讓您可以輕鬆地判斷動畫何時完成。 此外，它也可以在先前的方法完成之後，使用後續的動畫方法來建立順序動畫。 如需詳細資訊，請參閱[複合動畫](#compound-animations)。

如果需要讓動畫在背景中完成，則 `await` 可以省略運算子。 在此案例中，動畫擴充方法會在起始動畫之後快速傳回，動畫會在背景中發生。 建立複合動畫時，可以利用這項作業。 如需詳細資訊，請參閱[複合動畫](#composite-animations)。

如需運算子的詳細資訊 `await` ，請參閱[非同步支援總覽](~/cross-platform/platform/async.md)。

## <a name="single-animations"></a>單一動畫

中的每個擴充方法 [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) 都會執行單一動畫作業，以便在一段時間內將屬性從某個值逐漸變更為另一個值。 本節將探討每個動畫作業。

### <a name="rotation"></a>旋轉

下列程式碼範例將示範如何使用 [ `RotateTo` ] （x： Xamarin.Forms 。ViewExtensions. RotateTo （ Xamarin.Forms 。VisualElement，System.object，， Xamarin.Forms 。緩動））方法，以建立的 [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) 屬性動畫 [`Image`](xref:Xamarin.Forms.Image) ：

```csharp
await image.RotateTo (360, 2000);
image.Rotation = 0;
```

這段程式碼會在 [`Image`](xref:Xamarin.Forms.Image) 2 秒（2000毫秒）內旋轉到360度，以繪製實例的動畫。 [ `RotateTo` ] （X： Xamarin.Forms 。ViewExtensions. RotateTo （ Xamarin.Forms 。VisualElement，System.object，， Xamarin.Forms 。緩動）：方法 [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) 會取得動畫開頭的目前屬性值，然後從該值旋轉至其第一個引數（360）。 動畫完成後，影像的 [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) 屬性會重設為0。 這可確保在 `Rotation` 動畫結束後，屬性不會維持在360，這會導致額外的旋轉。

下列螢幕擷取畫面顯示每個平臺上進行的輪替：

![](simple-images/rotateto.png "Rotation Animation")

> [!NOTE]
> 除了 [ `RotateTo` ] （x： Xamarin.Forms 。ViewExtensions. RotateTo （ Xamarin.Forms 。VisualElement，System.object，， Xamarin.Forms 。緩動））方法，也有 [ `RotateXTo` ] （x： Xamarin.Forms 。ViewExtensions. RotateXTo （ Xamarin.Forms 。VisualElement，System.object，， Xamarin.Forms 。緩動）和 [ `RotateYTo` ] （x： Xamarin.Forms 。ViewExtensions. RotateYTo （ Xamarin.Forms 。VisualElement，System.object，， Xamarin.Forms 。緩動））方法， [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX) 分別建立和屬性的動畫 [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY) 。

### <a name="relative-rotation"></a>相對旋轉

下列程式碼範例將示範如何使用 [ `RelRotateTo` ] （x： Xamarin.Forms 。ViewExtensions. RelRotateTo （ Xamarin.Forms 。VisualElement，System.object，， Xamarin.Forms 。簡化））方法，以累加方式增加或減少的 [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) 屬性 [`Image`](xref:Xamarin.Forms.Image) ：

```csharp
await image.RelRotateTo (360, 2000);
```

這段程式碼會透過在 [`Image`](xref:Xamarin.Forms.Image) 2 秒（2000毫秒）的開始位置旋轉360度，來繪製實例的動畫。 [ `RelRotateTo` ] （X： Xamarin.Forms 。ViewExtensions. RelRotateTo （ Xamarin.Forms 。VisualElement，System.object，， Xamarin.Forms 。緩動）：方法 [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) 會取得動畫開頭的目前屬性值，然後從該值旋轉到值加上其第一個引數（360）。 這可確保每個動畫一律會從開始位置旋轉360度。 因此，如果在動畫已經在進行時叫用新的動畫，它就會從目前的位置開始，而且可能會在不是360度增量的位置結束。

下列螢幕擷取畫面顯示每個平臺上進行的相對旋轉：

![](simple-images/relrotateto.png "Relative Rotation Animation")

### <a name="scaling"></a>調整大小

下列程式碼範例將示範 [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) 如何使用方法，以動畫顯示的 [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) 屬性 [`Image`](xref:Xamarin.Forms.Image) ：

```csharp
await image.ScaleTo (2, 2000);
```

這段程式碼會以動畫呈現 [`Image`](xref:Xamarin.Forms.Image) 實例，其大小上限為2秒（2000毫秒）。 [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*)方法 [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) 會取得動畫開頭的目前屬性值（預設值為1），然後從該值調整為其第一個引數（2）。 這的效果是將影像的大小擴充到其大小的兩倍。

下列螢幕擷取畫面顯示每個平臺上的調整進行中：

![](simple-images/scaleto.png "Scaling Animation")

> [!NOTE]
> 除了 [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) 方法之外，還有 `ScaleXTo` 和 `ScaleYTo` 方法可分別建立和屬性的動畫 [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX) [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY) 。

### <a name="relative-scaling"></a>相對縮放比例

下列程式碼範例將示範如何使用 [ `RelScaleTo` ] （x： Xamarin.Forms 。ViewExtensions. RelScaleTo （ Xamarin.Forms 。VisualElement，System.object，， Xamarin.Forms 。緩動））方法，以建立的 [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) 屬性動畫 [`Image`](xref:Xamarin.Forms.Image) ：

```csharp
await image.RelScaleTo (2, 2000);
```

這段程式碼會以動畫呈現 [`Image`](xref:Xamarin.Forms.Image) 實例，其大小上限為2秒（2000毫秒）。 [ `RelScaleTo` ] （X： Xamarin.Forms 。ViewExtensions. RelScaleTo （ Xamarin.Forms 。VisualElement，System.object，， Xamarin.Forms 。緩動）：方法 [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) 會取得動畫開頭的目前屬性值，然後從該值調整為值加上其第一個引數（2）。 這可確保每個動畫一律會從開始位置調整為2。

### <a name="scaling-and-rotation-with-anchors"></a>使用錨點縮放和旋轉

[`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX)和 [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) 屬性會設定和屬性的縮放或旋轉中心 [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) 。 因此，其值也會影響 [ `RotateTo` ] （x： Xamarin.Forms 。ViewExtensions. RotateTo （ Xamarin.Forms 。VisualElement，System.object，， Xamarin.Forms 。簡化））和 [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) 方法。

假設已 [`Image`](xref:Xamarin.Forms.Image) 放在版面配置的中央，下列程式碼範例將示範如何藉由設定其屬性來旋轉影像中心周圍的影像 [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) ：

```csharp
double radius = Math.Min(absoluteLayout.Width, absoluteLayout.Height) / 2;
image.AnchorY = radius / image.Height;
await image.RotateTo(360, 2000);
```

若要在 [`Image`](xref:Xamarin.Forms.Image) 版面配置中央旋轉實例， [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) 必須將和 [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) 屬性設定為相對於之寬度和高度的值 `Image` 。 在此範例中，的中心 `Image` 定義為位於版面配置的中央，因此預設 `AnchorX` 值0.5 不需要變更。 不過， `AnchorY` 屬性會重新定義為從頂端 `Image` 到版面配置中心點的值。 這可確保在 `Image` 版面配置的中心點進行完整的360度旋轉，如下列螢幕擷取畫面所示：

![](simple-images/rotate-anchors.png "Rotation Animation with Anchors")

### <a name="translation"></a>翻譯

下列程式碼範例將示範如何使用 [ `TranslateTo` ] （x： Xamarin.Forms 。ViewExtensions. TranslateTo （ Xamarin.Forms 。VisualElement，Double，system.string， Xamarin.Forms ，，緩動））方法，以動畫顯示的 [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) 和 [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) 屬性 [`Image`](xref:Xamarin.Forms.Image) ：

```csharp
await image.TranslateTo (-100, -100, 1000);
```

這段程式碼 [`Image`](xref:Xamarin.Forms.Image) 會以水準方式轉譯實例，並在1秒（1000毫秒）垂直轉換，以動畫呈現實例。 [ `TranslateTo` ] （X： Xamarin.Forms 。ViewExtensions. TranslateTo （ Xamarin.Forms 。VisualElement，Double，system.string， Xamarin.Forms ，，緩時）方法會同時將影像100圖元轉譯為左邊，並向上轉譯100圖元。 這是因為第一個和第二個引數都是負數。 提供正數會將影像向右和向下轉譯。

下列螢幕擷取畫面顯示每個平臺上的轉譯進行中：

![](simple-images/translateto.png "Translation Animation")

> [!NOTE]
> 如果某專案一開始是以螢幕配置，然後轉譯到螢幕上，則在轉譯之後，專案的輸入配置會保持關閉畫面，而且使用者無法與它互動。 因此，建議您在最後一個位置配置視圖，然後執行任何必要的翻譯。

### <a name="fading"></a>淡出

下列程式碼範例將示範如何使用 [ `FadeTo` ] （x： Xamarin.Forms 。ViewExtensions. FadeTo （ Xamarin.Forms 。VisualElement，System.object，， Xamarin.Forms 。緩動））方法，以建立的 [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) 屬性動畫 [`Image`](xref:Xamarin.Forms.Image) ：

```csharp
image.Opacity = 0;
await image.FadeTo (1, 4000);
```

這段程式碼 [`Image`](xref:Xamarin.Forms.Image) 會在超過4秒（4000毫秒）內漸淡，以動畫呈現實例。 [ `FadeTo` ] （X： Xamarin.Forms 。ViewExtensions. FadeTo （ Xamarin.Forms 。VisualElement，System.object，， Xamarin.Forms 。緩動）：方法 [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) 會取得動畫開頭的目前屬性值，然後從該值淡入到其第一個引數（1）。

下列螢幕擷取畫面顯示每個平臺上的淡入進度：

![](simple-images/fadeto.png "Fading Animation")

## <a name="compound-animations"></a>複合動畫

複合動畫是動畫的連續組合，可以使用運算子來建立 `await` ，如下列程式碼範例所示：

```csharp
await image.TranslateTo (-100, 0, 1000);    // Move image left
await image.TranslateTo (-100, -100, 1000); // Move image up
await image.TranslateTo (100, 100, 2000);   // Move image diagonally down and right
await image.TranslateTo (0, 100, 1000);     // Move image left
await image.TranslateTo (0, 0, 1000);       // Move image up
```

在此範例中， [`Image`](xref:Xamarin.Forms.Image) 會轉譯超過6秒（6000毫秒）。 的轉譯 `Image` 會使用五個動畫，並使用 `await` 運算子來指示每個動畫循序執行。 因此，在先前的方法完成之後，會執行後續的動畫方法。

## <a name="composite-animations"></a>複合動畫

複合動畫是一種動畫的組合，其中會同時執行兩個以上的動畫。 複合動畫可透過混合等候和非等候的動畫來建立，如下列程式碼範例所示：

```csharp
image.RotateTo (360, 4000);
await image.ScaleTo (2, 2000);
await image.ScaleTo (1, 2000);
```

在此範例中， [`Image`](xref:Xamarin.Forms.Image) 會縮放並同時旋轉超過4秒（4000毫秒）。 的縮放 `Image` 會使用與旋轉同時發生的兩個順序動畫。 [ `RotateTo` ] （X： Xamarin.Forms 。ViewExtensions. RotateTo （ Xamarin.Forms 。VisualElement，System.object，， Xamarin.Forms 。緩動））方法會在不使用運算子的情況下執行 `await` ，並在第一個 [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) 動畫開始之後立即傳回。 `await`第一個方法呼叫上的運算子會 `ScaleTo` 延遲第二個方法呼叫 `ScaleTo` ，直到第一個 `ScaleTo` 方法呼叫完成為止。 此時， `RotateTo` 動畫已完成一半，而且 `Image` 將會旋轉180度。 在最後2秒（2000毫秒）期間，第二個 `ScaleTo` 動畫和 `RotateTo` 動畫都完成。

### <a name="running-multiple-asynchronous-methods-concurrently"></a>同時執行多個非同步方法

`static` `Task.WhenAny` 和 `Task.WhenAll` 方法是用來同時執行多個非同步方法，因此可以用來建立複合動畫。 這兩種方法都會傳回 `Task` 物件，並接受每個傳回物件的方法集合 `Task` 。 方法會在 `Task.WhenAny` 其集合中的任何方法完成執行時完成，如下列程式碼範例所示：

```csharp
await Task.WhenAny<bool>
(
  image.RotateTo (360, 4000),
  image.ScaleTo (2, 2000)
);
await image.ScaleTo (1, 2000);
```

在此範例中， `Task.WhenAny` 方法呼叫包含兩個工作。 第一個工作會將映射旋轉超過4秒（4000毫秒），而第二個工作會將影像調整為2秒（2000毫秒）。 當第二個工作完成時， `Task.WhenAny` 方法呼叫會完成。 不過，即使 [ `RotateTo` ] （x： Xamarin.Forms 。ViewExtensions. RotateTo （ Xamarin.Forms 。VisualElement，System.object，， Xamarin.Forms 。緩動））方法仍在執行中，第二個 [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) 方法可以開始。

`Task.WhenAll`方法會在其集合中的所有方法完成時完成，如下列程式碼範例所示：

```csharp
// 10 minute animation
uint duration = 10 * 60 * 1000;

await Task.WhenAll (
  image.RotateTo (307 * 360, duration),
  image.RotateXTo (251 * 360, duration),
  image.RotateYTo (199 * 360, duration)
);
```

在此範例中， `Task.WhenAll` 方法呼叫包含三個工作，每個工作都會執行10分鐘以上的作業。 每個 `Task` 都會對 [ `RotateTo` ] （x：）產生不同數目的360度數旋轉–307旋轉 Xamarin.Forms 。ViewExtensions. RotateTo （ Xamarin.Forms 。VisualElement，System.object，， Xamarin.Forms 。緩時），251對 [] 的旋轉 `RotateXTo` （x： Xamarin.Forms 。ViewExtensions. RotateXTo （ Xamarin.Forms 。VisualElement，System.object，， Xamarin.Forms 。緩動）和199的 [ `RotateYTo` ] （x：）旋轉 Xamarin.Forms 。ViewExtensions. RotateYTo （ Xamarin.Forms 。VisualElement，System.object，， Xamarin.Forms 。簡化））。 這些值為質數，因此可確保旋轉不會同步處理，因此不會產生重複的模式。

下列螢幕擷取畫面顯示每個平臺上的多個輪替進行中：

![](simple-images/multiple-rotations.png "Composite Animation")

## <a name="canceling-animations"></a>取消動畫

應用程式可以透過呼叫 `static` [ `ViewExtensions.CancelAnimations` ] （x：）來取消一或多個動畫 Xamarin.Forms 。ViewExtensions. CancelAnimations （ Xamarin.Forms 。VisualElement））方法，如下列程式碼範例所示：

```csharp
ViewExtensions.CancelAnimations (image);
```

這會立即取消目前正在實例上執行的所有動畫 [`Image`](xref:Xamarin.Forms.Image) 。

## <a name="summary"></a>摘要

本文示範如何使用類別來建立和取消動畫 [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) 。 這個類別提供擴充方法，可用於建立旋轉、縮放、轉譯和淡化實例的簡單動畫 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。

## <a name="related-links"></a>相關連結

- [非同步支援總覽](~/cross-platform/platform/async.md)
- [基本動畫（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-basic)
- [ViewExtensions](xref:Xamarin.Forms.ViewExtensions)
