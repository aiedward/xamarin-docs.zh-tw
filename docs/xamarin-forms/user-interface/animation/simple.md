---
title: Xamarin 中的簡單動畫
description: ViewExtensions 類別提供可用來建立簡單動畫的擴充方法。 本文示範如何使用 ViewExtensions 類別來建立和取消動畫。
ms.prod: xamarin
ms.assetid: 4A6FAE5A-848F-4CE0-BFA1-22A6309B5225
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/05/2019
ms.openlocfilehash: a4f91da6fcaefaffd41177d99ebe906aca3fdba2
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75487304"
---
# <a name="simple-animations-in-xamarinforms"></a>Xamarin 中的簡單動畫

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-basic)

_ViewExtensions 類別提供可用來建立簡單動畫的擴充方法。本文示範如何使用 ViewExtensions 類別來建立和取消動畫。_

[`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions)類別提供下列可用於建立簡單動畫的擴充方法：

- [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing))會以動畫呈現[`VisualElement`](xref:Xamarin.Forms.VisualElement)的[`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)和[`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)屬性。
- [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*)會以動畫呈現[`VisualElement`](xref:Xamarin.Forms.VisualElement)的[`Scale`](xref:Xamarin.Forms.VisualElement.Scale)屬性。
- `ScaleXTo` 會以動畫呈現[`VisualElement`](xref:Xamarin.Forms.VisualElement)的[`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX)屬性。
- `ScaleYTo` 會以動畫呈現[`VisualElement`](xref:Xamarin.Forms.VisualElement)的[`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY)屬性。
- [`RelScaleTo`](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))會將動畫增量增加或減少套用至[`VisualElement`](xref:Xamarin.Forms.VisualElement)的[`Scale`](xref:Xamarin.Forms.VisualElement.Scale)屬性。
- [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))會以動畫呈現[`VisualElement`](xref:Xamarin.Forms.VisualElement)的[`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)屬性。
- [`RelRotateTo`](xref:Xamarin.Forms.ViewExtensions.RelRotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))會將動畫增量增加或減少套用至[`VisualElement`](xref:Xamarin.Forms.VisualElement)的[`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)屬性。
- [`RotateXTo`](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))會以動畫呈現[`VisualElement`](xref:Xamarin.Forms.VisualElement)的[`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX)屬性。
- [`RotateYTo`](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))會以動畫呈現[`VisualElement`](xref:Xamarin.Forms.VisualElement)的[`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)屬性。
- [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))會以動畫呈現[`VisualElement`](xref:Xamarin.Forms.VisualElement)的[`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity)屬性。

根據預設，每個動畫將需要250毫秒。 不過，建立動畫時，可以指定每個動畫的持續時間。

[`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions)類別也包含可以用來取消任何動畫的[`CancelAnimations`](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement))方法。

> [!NOTE]
> [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions)類別提供[`LayoutTo`](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing))的擴充方法。 不過，此方法的目的是要供版面配置使用，以在包含大小和位置變更的版面配置狀態之間建立動畫轉換。 因此，它應該僅供[`Layout`](xref:Xamarin.Forms.Layout)子類別使用。

[`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions)類別中的動畫擴充方法都是非同步，而且會傳回 `Task<bool>` 物件。 如果動畫完成，則傳回值為 `false`，如果動畫已取消，則會 `true`。 因此，動畫方法通常應該與 `await` 運算子搭配使用，讓您可以輕鬆地判斷動畫何時完成。 此外，它也可以在先前的方法完成之後，使用後續的動畫方法來建立順序動畫。 如需詳細資訊，請參閱[複合動畫](#compound)。

如果需要讓動畫在背景中完成，則可以省略 `await` 運算子。 在此案例中，動畫擴充方法會在起始動畫之後快速傳回，動畫會在背景中發生。 建立複合動畫時，可以利用這項作業。 如需詳細資訊，請參閱[複合動畫](#composite)。

如需 `await` 運算子的詳細資訊，請參閱[非同步支援總覽](~/cross-platform/platform/async.md)。

## <a name="single-animations"></a>單一動畫

[`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions)中的每個擴充方法都會執行單一動畫作業，此作業會在一段時間內，將屬性從某個值逐漸變更為另一個值。 本節將探討每個動畫作業。

### <a name="rotation"></a>旋轉

下列程式碼範例將示範如何使用[`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法，以動畫顯示[`Image`](xref:Xamarin.Forms.Image)的[`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)屬性：

```csharp
await image.RotateTo (360, 2000);
image.Rotation = 0;
```

此程式碼會在2秒（2000毫秒）內旋轉到360度，以繪製[`Image`](xref:Xamarin.Forms.Image)實例的動畫。 [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法會取得動畫開頭的目前[`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)屬性值，然後從該值旋轉到其第一個引數（360）。 動畫完成後，影像的[`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)屬性會重設為0。 這可確保在動畫結束之後，`Rotation` 屬性不會維持在360，這會導致額外的旋轉。

下列螢幕擷取畫面顯示每個平臺上進行的輪替：

![](simple-images/rotateto.png "Rotation Animation")

> [!NOTE]
> 除了[`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法以外，還有[`RotateXTo`](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))和[`RotateYTo`](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法，分別會建立[`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX)和[`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)屬性的動畫。

### <a name="relative-rotation"></a>相對旋轉

下列程式碼範例將示範如何使用[`RelRotateTo`](xref:Xamarin.Forms.ViewExtensions.RelRotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法，以累加方式增加或減少[`Image`](xref:Xamarin.Forms.Image)的[`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)屬性：

```csharp
await image.RelRotateTo (360, 2000);
```

此程式碼會在2秒（2000毫秒）的開始位置旋轉360度，以繪製[`Image`](xref:Xamarin.Forms.Image)實例的動畫。 [`RelRotateTo`](xref:Xamarin.Forms.ViewExtensions.RelRotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法會取得動畫開頭的目前[`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)屬性值，然後從該值旋轉到值加上其第一個引數（360）。 這可確保每個動畫一律會從開始位置旋轉360度。 因此，如果在動畫已經在進行時叫用新的動畫，它就會從目前的位置開始，而且可能會在不是360度增量的位置結束。

下列螢幕擷取畫面顯示每個平臺上進行的相對旋轉：

![](simple-images/relrotateto.png "Relative Rotation Animation")

### <a name="scaling"></a>縮放

下列程式碼範例將示範如何使用[`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*)方法，以動畫顯示[`Image`](xref:Xamarin.Forms.Image)的[`Scale`](xref:Xamarin.Forms.VisualElement.Scale)屬性：

```csharp
await image.ScaleTo (2, 2000);
```

這段程式碼會以動畫呈現[`Image`](xref:Xamarin.Forms.Image)實例，其大小上限為2秒（2000毫秒）。 [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*)方法會取得動畫開頭的目前[`Scale`](xref:Xamarin.Forms.VisualElement.Scale)屬性值（預設值為1），然後從該值調整為其第一個引數（2）。 這的效果是將影像的大小擴充到其大小的兩倍。

下列螢幕擷取畫面顯示每個平臺上的調整進行中：

![](simple-images/scaleto.png "Scaling Animation")

> [!NOTE]
> 除了[`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*)方法以外，還有 `ScaleXTo` 和 `ScaleYTo` 方法，分別會建立[`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX)和[`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY)屬性的動畫。

### <a name="relative-scaling"></a>相對縮放比例

下列程式碼範例將示範如何使用[`RelScaleTo`](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法，以動畫顯示[`Image`](xref:Xamarin.Forms.Image)的[`Scale`](xref:Xamarin.Forms.VisualElement.Scale)屬性：

```csharp
await image.RelScaleTo (2, 2000);
```

這段程式碼會以動畫呈現[`Image`](xref:Xamarin.Forms.Image)實例，其大小上限為2秒（2000毫秒）。 [`RelScaleTo`](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法會取得動畫開頭的目前[`Scale`](xref:Xamarin.Forms.VisualElement.Scale)屬性值，然後從該值調整為值加上其第一個引數（2）。 這可確保每個動畫一律會從開始位置調整為2。

### <a name="scaling-and-rotation-with-anchors"></a>使用錨點縮放和旋轉

[ [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) ] 和 [ [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) ] 屬性會設定[`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)和[`Scale`](xref:Xamarin.Forms.VisualElement.Scale)屬性的縮放或旋轉的中心。 因此，其值也會影響[`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))和[`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*)方法。

假設有一個已放在版面配置中心的[`Image`](xref:Xamarin.Forms.Image) ，下列程式碼範例將示範如何藉由設定其[`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY)屬性來旋轉影像中心的影像：

```csharp
double radius = Math.Min(absoluteLayout.Width, absoluteLayout.Height) / 2;
image.AnchorY = radius / image.Height;
await image.RotateTo(360, 2000);
```

若要在版面配置中央旋轉[`Image`](xref:Xamarin.Forms.Image)實例， [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX)和[`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY)屬性必須設定為相對於 `Image`寬度和高度的值。 在此範例中，`Image` 的中心定義為位於版面配置的中央，因此預設的 `AnchorX` 值0.5 不需要變更。 不過，`AnchorY` 屬性會重新定義為從 `Image` 頂端到版面配置中心點的值。 這可確保 `Image` 會在版面配置的中心點上進行360度的完整旋轉，如下列螢幕擷取畫面所示：

![](simple-images/rotate-anchors.png "Rotation Animation with Anchors")

### <a name="translation"></a>轉譯

下列程式碼範例將示範如何使用[`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing))方法，以動畫顯示[`Image`](xref:Xamarin.Forms.Image)的[`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)和[`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)屬性：

```csharp
await image.TranslateTo (-100, -100, 1000);
```

這段程式碼會以水準和垂直方式轉譯[`Image`](xref:Xamarin.Forms.Image)實例（1000毫秒）來進行動畫。 [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing))方法會同時將影像100圖元轉譯為左邊，並將100圖元向上轉譯。 這是因為第一個和第二個引數都是負數。 提供正數會將影像向右和向下轉譯。

下列螢幕擷取畫面顯示每個平臺上的轉譯進行中：

![](simple-images/translateto.png "Translation Animation")

> [!NOTE]
> 如果某專案一開始是以螢幕配置，然後轉譯到螢幕上，則在轉譯之後，專案的輸入配置會保持關閉畫面，而且使用者無法與它互動。 因此，建議您在最後一個位置配置視圖，然後執行任何必要的翻譯。

### <a name="fading"></a>淡出

下列程式碼範例將示範如何使用[`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法，以動畫顯示[`Image`](xref:Xamarin.Forms.Image)的[`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity)屬性：

```csharp
image.Opacity = 0;
await image.FadeTo (1, 4000);
```

此程式碼會在超過4秒（4000毫秒）內漸淡，以動畫呈現[`Image`](xref:Xamarin.Forms.Image)實例。 [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法會取得動畫開頭的目前[`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity)屬性值，然後從該值淡入到其第一個引數（1）。

下列螢幕擷取畫面顯示每個平臺上的淡入進度：

![](simple-images/fadeto.png "Fading Animation")

<a name="compound" />

## <a name="compound-animations"></a>複合動畫

複合動畫是動畫的連續組合，可以使用 `await` 運算子來建立，如下列程式碼範例所示：

```csharp
await image.TranslateTo (-100, 0, 1000);    // Move image left
await image.TranslateTo (-100, -100, 1000); // Move image up
await image.TranslateTo (100, 100, 2000);   // Move image diagonally down and right
await image.TranslateTo (0, 100, 1000);     // Move image left
await image.TranslateTo (0, 0, 1000);       // Move image up
```

在此範例中， [`Image`](xref:Xamarin.Forms.Image)會轉譯超過6秒（6000毫秒）。 `Image` 的轉譯會使用五個動畫，而 `await` 運算子則表示每個動畫會依序執行。 因此，在先前的方法完成之後，會執行後續的動畫方法。

<a name="composite" />

## <a name="composite-animations"></a>複合動畫

複合動畫是一種動畫的組合，其中會同時執行兩個以上的動畫。 複合動畫可透過混合等候和非等候的動畫來建立，如下列程式碼範例所示：

```csharp
image.RotateTo (360, 4000);
await image.ScaleTo (2, 2000);
await image.ScaleTo (1, 2000);
```

在此範例中， [`Image`](xref:Xamarin.Forms.Image)會縮放並同時旋轉超過4秒（4000毫秒）。 `Image` 的縮放會使用與旋轉同時發生的兩個順序動畫。 [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法會在沒有 `await` 運算子的情況下執行，並立即傳回，第一個[`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*)動畫隨即開始。 第一個 `ScaleTo` 方法呼叫上的 `await` 運算子會延遲第二個 `ScaleTo` 方法呼叫，直到第一個 `ScaleTo` 方法呼叫完成為止。 此時，`RotateTo` 動畫是完成半形，而 `Image` 會旋轉180度。 在最後2秒（2000毫秒）期間，第二個 `ScaleTo` 動畫和 `RotateTo` 動畫都完成。

### <a name="running-multiple-asynchronous-methods-concurrently"></a>同時執行多個非同步方法

`static` 的 `Task.WhenAny` 和 `Task.WhenAll` 方法是用來同時執行多個非同步方法，因此可以用來建立複合動畫。 這兩種方法都會傳回 `Task` 物件，並接受每個傳回 `Task` 物件之方法的集合。 當集合中的任何方法完成執行時，`Task.WhenAny` 方法就會完成，如下列程式碼範例所示：

```csharp
await Task.WhenAny<bool>
(
  image.RotateTo (360, 4000),
  image.ScaleTo (2, 2000)
);
await image.ScaleTo (1, 2000);
```

在此範例中，`Task.WhenAny` 方法呼叫包含兩個工作。 第一個工作會將映射旋轉超過4秒（4000毫秒），而第二個工作會將影像調整為2秒（2000毫秒）。 當第二個工作完成時，`Task.WhenAny` 方法呼叫就會完成。 不過，即使[`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法仍在執行，第二個[`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*)方法也可以開始。

當集合中的所有方法都完成時，`Task.WhenAll` 方法就會完成，如下列程式碼範例所示：

```csharp
// 10 minute animation
uint duration = 10 * 60 * 1000;

await Task.WhenAll (
  image.RotateTo (307 * 360, duration),
  image.RotateXTo (251 * 360, duration),
  image.RotateYTo (199 * 360, duration)
);
```

在此範例中，`Task.WhenAll` 方法呼叫包含三個工作，每個工作都會執行10分鐘以上的作業。 每個 `Task` 會為 251 [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))提供不同數目的360度旋轉–307旋轉以進行[`RotateXTo`](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))，以及[`RotateYTo`](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))的199旋轉。 這些值為質數，因此可確保旋轉不會同步處理，因此不會產生重複的模式。

下列螢幕擷取畫面顯示每個平臺上的多個輪替進行中：

![](simple-images/multiple-rotations.png "Composite Animation")

## <a name="canceling-animations"></a>取消動畫

應用程式可以透過呼叫 `static` [`ViewExtensions.CancelAnimations`](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement))方法來取消一或多個動畫，如下列程式碼範例所示：

```csharp
ViewExtensions.CancelAnimations (image);
```

這會立即取消目前正在[`Image`](xref:Xamarin.Forms.Image)實例上執行的所有動畫。

## <a name="summary"></a>總結

本文示範如何使用[`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions)類別來建立和取消動畫。 這個類別會提供擴充方法，可用來建立可旋轉、縮放、轉譯和淡化[`VisualElement`](xref:Xamarin.Forms.VisualElement)實例的簡單動畫。

## <a name="related-links"></a>相關連結

- [非同步支援概觀](~/cross-platform/platform/async.md)
- [基本動畫（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-basic)
- [ViewExtensions](xref:Xamarin.Forms.ViewExtensions)
