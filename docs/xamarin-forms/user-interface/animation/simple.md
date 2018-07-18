---
title: 在 Xamarin.Forms 中的簡單動畫
description: ViewExtensions 類別提供可用來建構簡單的動畫的擴充方法。 本文示範如何建立和取消使用 ViewExtensions 類別的動畫。
ms.prod: xamarin
ms.assetid: 4A6FAE5A-848F-4CE0-BFA1-22A6309B5225
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/27/2017
ms.openlocfilehash: 124fc311d5e2c8c89353ba813df60f0bf1d0b34a
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38997067"
---
# <a name="simple-animations-in-xamarinforms"></a>在 Xamarin.Forms 中的簡單動畫

_ViewExtensions 類別提供可用來建構簡單的動畫的擴充方法。本文示範如何建立和取消使用 ViewExtensions 類別的動畫。_


[ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions)類別提供可用來建立簡單的動畫的下列擴充方法：

- [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) 建立動畫[ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX)並[ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY)屬性[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。
- [`ScaleTo`](xref:Xamarin.Forms.VisualElement.Scale) 建立動畫[ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale)屬性[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。
- [`RelScaleTo`](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) 適用於動畫的累加增加或減少以[ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale)屬性[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。
- [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) 建立動畫[ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation)屬性[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。
- [`RelRotateTo`](xref:Xamarin.Forms.ViewExtensions.RelRotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) 適用於動畫的累加增加或減少以[ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation)屬性[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。
- [`RotateXTo`](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) 建立動畫[ `RotationX` ](xref:Xamarin.Forms.VisualElement.RotationX)屬性[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。
- [`RotateYTo`](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) 建立動畫[ `RotationY` ](xref:Xamarin.Forms.VisualElement.RotationY)屬性[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。
- [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) 建立動畫[ `Opacity` ](xref:Xamarin.Forms.VisualElement.Opacity)屬性[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。

根據預設，每個動畫需要 250 毫秒。 不過，建立動畫時，可以指定每個動畫的持續時間。

[ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions)類別也包含[ `CancelAnimations` ](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement))可用來取消任何動畫的方法。

> [!NOTE]
> [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions)類別會提供[ `LayoutTo` ](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing))擴充方法。 不過，這個方法被要供配置用來以動畫顯示可以包含大小和位置變更版面配置狀態之間轉換。 因此，它應該只由[ `Layout` ](xref:Xamarin.Forms.Layout)子類別。

中的動畫擴充方法[ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions)類別都非同步且傳回`Task<bool>`物件。 傳回值是`false`動畫完成時，如果和`true`如果動畫會取消。 因此，動畫方法應通常搭配`await`運算子，就會讓您能夠輕鬆地判斷當動畫完成時。 此外，然後就可以使用後續的動畫方法執行完成前一個方法之後建立循序的動畫。 如需詳細資訊，請參閱 <<c0> [ 複合動畫](#compound)。

如果需要讓動畫完成在背景中，則`await`運算子，則可以省略。 在此案例中，快速將起始動畫，以在背景中發生的動畫後傳回動畫擴充方法。 建立複合的動畫時，這項作業可以採取的優點。 如需詳細資訊，請參閱 <<c0> [ 複合動畫](#composite)。

如需詳細資訊`await`運算子，請參閱[非同步支援概觀](~/cross-platform/platform/async.md)。

## <a name="single-animations"></a>單一的動畫

在每個擴充方法[ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions)實作漸進式屬性從某個值變更為另一個值經過一段時間的單一動畫作業。 本節探討每個動畫的作業。

### <a name="rotation"></a>旋轉

下列程式碼範例示範如何使用[ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法來建立動畫[ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation)屬性[ `Image` ](xref:Xamarin.Forms.Image):

```csharp
await image.RotateTo (360, 2000);
image.Rotation = 0;
```

此程式碼建立動畫[ `Image` ](xref:Xamarin.Forms.Image)它最多 360 度旋轉超過 2 秒 （2000年毫秒為單位） 的執行個體。 [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法會取得目前[ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation)屬性將動畫的起始值，然後再從該值旋轉，其第一個引數 (360)。 當動畫完成，映像[ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation)屬性重設為 0。 這可確保`Rotation`動畫結束，可能會妨礙其他輪替之後，將不會維持在 360 的屬性。

以下的螢幕擷取畫面顯示在每個平台上進行的旋轉：

![](simple-images/rotateto.png "旋轉的動畫")

### <a name="relative-rotation"></a>相對的旋轉

下列程式碼範例示範如何使用[ `RelRotateTo` ](xref:Xamarin.Forms.ViewExtensions.RelRotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法，以累加方式增加或減少[ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation)屬性[ `Image` ](xref:Xamarin.Forms.Image):

```csharp
await image.RelRotateTo (360, 2000);
```

此程式碼建立動畫[ `Image` ](xref:Xamarin.Forms.Image)它從其起始位置的 360 度旋轉超過 2 秒 （2000年毫秒為單位） 的執行個體。 [ `RelRotateTo` ](xref:Xamarin.Forms.ViewExtensions.RelRotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法會取得目前[ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation)屬性動畫的起始值，然後再旋轉從該值的值加上第一個引數 (360)。 這可確保每個動畫一律會從起始位置的 360 度旋轉。 因此，如果當動畫已在進行中時，會叫用新的動畫，它會從目前位置開始，最後可能不是 360 度的遞增值的位置。

下列螢幕擷取畫面會顯示在每個平台上進行相對的旋轉：

![](simple-images/relrotateto.png "相對的旋轉動畫")

### <a name="scaling"></a>縮放

下列程式碼範例示範如何使用[ `ScaleTo` ](xref:Xamarin.Forms.VisualElement.Scale)方法來建立動畫[ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale)屬性[ `Image` ](xref:Xamarin.Forms.Image):

```csharp
await image.ScaleTo (2, 2000);
```

此程式碼建立動畫[ `Image` ](xref:Xamarin.Forms.Image)向上調整其大小的兩倍至超過 2 秒 （2000年毫秒為單位） 的執行個體。 [ `ScaleTo` ](xref:Xamarin.Forms.VisualElement.Scale)方法會取得目前[ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale)開始的動畫，並從該值然後會調整為其第一個引數 (2) 的屬性值 （預設值為 1）。 這有其大小的兩倍來延伸影像的大小影響。

下列螢幕擷取畫面顯示在每個平台上進行調整：

![](simple-images/scaleto.png "調整的動畫")

### <a name="relative-scaling"></a>相對縮放比例

下列程式碼範例示範如何使用[ `RelScaleTo` ](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法來建立動畫[ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale)屬性[ `Image` ](xref:Xamarin.Forms.Image):

```csharp
await image.RelScaleTo (2, 2000);
```

此程式碼建立動畫[ `Image` ](xref:Xamarin.Forms.Image)向上調整其大小的兩倍至超過 2 秒 （2000年毫秒為單位） 的執行個體。 [ `RelScaleTo` ](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法會取得目前[ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale)開始的動畫，並從該值然後調整大小以值加上第一個引數 (2) 的屬性值。 這可確保每個動畫將一律會從起始位置 2 的調整。

### <a name="scaling-and-rotation-with-anchors"></a>縮放和旋轉起點

[ `AnchorX` ](xref:Xamarin.Forms.VisualElement.AnchorX)並[ `AnchorY` ](xref:Xamarin.Forms.VisualElement.AnchorY)屬性設定的縮放或旋轉的中心[ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation)和[ `Scale`](xref:Xamarin.Forms.VisualElement.Scale)屬性。 因此，其值也會影響[ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))並[ `ScaleTo` ](xref:Xamarin.Forms.VisualElement.Scale)方法。

給定[ `Image` ](xref:Xamarin.Forms.Image) ，已放在版面配置的中心，下列程式碼範例將示範輪替影像的版面配置的中心，藉由設定其[ `AnchorY` ](xref:Xamarin.Forms.VisualElement.AnchorY)屬性：

```csharp
image.AnchorY = (Math.Min (absoluteLayout.Width, absoluteLayout.Height) / 2) / image.Height;
await image.RotateTo (360, 2000);
```

若要旋轉[ `Image` ](xref:Xamarin.Forms.Image)中心周圍的版面配置的執行個體[ `AnchorX` ](xref:Xamarin.Forms.VisualElement.AnchorX)並[ `AnchorY` ](xref:Xamarin.Forms.VisualElement.AnchorY)屬性必須設為值相對於的寬度和高度`Image`。 在此範例中，中央`Image`定義為中心的版面配置，因此預設`AnchorX`值為 0.5 不需要變更。 不過，`AnchorY`屬性已重新定義的值，從頂端`Image`到版面配置的中心點。 這可確保`Image`讓完整的版面配置的中心點周圍的 360 度旋轉，如下列螢幕擷取畫面所示：

![](simple-images/rotate-anchors.png "使用錨點的旋轉動畫")

### <a name="translation"></a>轉譯

下列程式碼範例示範如何使用[ `TranslateTo` ](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing))方法來建立動畫[ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX)並[ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY) 的內容[`Image`](xref:Xamarin.Forms.Image):

```csharp
await image.TranslateTo (-100, -100, 1000);
```

此程式碼建立動畫[ `Image` ](xref:Xamarin.Forms.Image)轉譯它水平和垂直大小超過 1 秒 （1000年毫秒為單位） 的執行個體。 [ `TranslateTo` ](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing))影像 100 像素到最左邊，並向上 100 像素，同時將轉譯方法。 這是因為第一個和第二個引數是這兩個負數的數字。 提供正數，會將轉譯的影像，右邊，然後向下。

下列螢幕擷取畫面會顯示在每個平台上進行翻譯：

![](simple-images/translateto.png "轉譯動畫")

> [!NOTE]
> 如果項目是一開始配置螢幕，而且會轉譯在螢幕上，在轉譯之後的項目輸入的配置會保持關閉螢幕，使用者無法與它互動。 因此，建議應該配置檢視中其最後一個位置，，和任何則需要執行的轉譯。

### <a name="fading"></a>淡出

下列程式碼範例示範如何使用[ `FadeTo` ](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法來建立動畫[ `Opacity` ](xref:Xamarin.Forms.VisualElement.Opacity)屬性[ `Image` ](xref:Xamarin.Forms.Image):

```csharp
image.Opacity = 0;
await image.FadeTo (1, 4000);
```

此程式碼建立動畫[ `Image` ](xref:Xamarin.Forms.Image)淡中超過 4 秒 （4000 毫秒為單位） 執行個體。 [ `FadeTo` ](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法會取得目前[ `Opacity` ](xref:Xamarin.Forms.VisualElement.Opacity)開始的動畫，並再淡出中的從該值以第一個引數 (1) 的屬性值。

下列螢幕擷取畫面顯示淡出每個平台上進行中：

![](simple-images/fadeto.png "淡出動畫")

<a name="compound" />

## <a name="compound-animations"></a>複合的動畫

複合動畫的動畫，循序結合，您可以使用建立`await`運算子，如下列程式碼範例所示：

```csharp
await image.TranslateTo (-100, 0, 1000);    // Move image left
await image.TranslateTo (-100, -100, 1000); // Move image up
await image.TranslateTo (100, 100, 2000);   // Move image diagonally down and right
await image.TranslateTo (0, 100, 1000);     // Move image left
await image.TranslateTo (0, 0, 1000);       // Move image up
```

在此範例中， [ `Image` ](xref:Xamarin.Forms.Image)轉譯超過 6 秒 （6000 毫秒為單位）。 翻譯`Image`五個動畫，會使用`await`指出每個動畫會循序執行的運算子。 因此，後續的動畫方法執行完成前一個方法之後。

<a name="composite" />

## <a name="composite-animations"></a>複合的動畫

複合動畫是動畫組成兩個或多個動畫同時執行的位置。 可以透過混合受期待與非等候動畫，建立複合的動畫，如下列程式碼範例所示：

```csharp
image.RotateTo (360, 4000);
await image.ScaleTo (2, 2000);
await image.ScaleTo (1, 2000);
```

在此範例中， [ `Image` ](xref:Xamarin.Forms.Image)調整，而且同時旋轉超過 4 秒 （4000 毫秒為單位）。 縮放比例`Image`會使用兩個循序的動畫旋轉與同時發生。 [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法執行不含`await`運算子，並立即傳回的第一個[ `ScaleTo` ](xref:Xamarin.Forms.VisualElement.Scale)然後開始的動畫。 `await`運算子，在第一`ScaleTo`方法呼叫會延遲，第二個`ScaleTo`方法呼叫，直到第一個`ScaleTo`方法呼叫已完成。 此時`RotateTo`動畫是一半方式完成，`Image`會旋轉 180 度。 最後 2 秒 （2000年毫秒），在第二個`ScaleTo`動畫和`RotateTo`同時完成動畫。

### <a name="running-multiple-asynchronous-methods-concurrently"></a>同時執行多個非同步方法

`static` `Task.WhenAny`和`Task.WhenAll`方法用來同時執行多個非同步方法，因此可以用來建立複合的動畫。 這兩個方法會傳回`Task`物件，並接受方法的集合，每個傳回`Task`物件。 `Task.WhenAny`方法完成時在其集合中的任何方法完成執行時，如下列程式碼範例所示：

```csharp
await Task.WhenAny<bool>
(
  image.RotateTo (360, 4000),
  image.ScaleTo (2, 2000)
);
await image.ScaleTo (1, 2000);
```

在此範例中，`Task.WhenAny`方法呼叫包含兩個工作。 第一項工作將影像旋轉超過 4 秒 （4000 毫秒為單位），和第二項工作會縮放影像超過 2 秒 （2000年毫秒為單位）。 第二個工作完成時，`Task.WhenAny`方法呼叫完成。 不過，即使[ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))方法仍在執行中，第二個[ `ScaleTo` ](xref:Xamarin.Forms.VisualElement.Scale)方法可以開始。

`Task.WhenAll`方法完成時完成其集合中的所有方法，如下列程式碼範例所示：

```csharp
// 10 minute animation
uint duration = 10 * 60 * 1000;

await Task.WhenAll (
  image.RotateTo (307 * 360, duration),
  image.RotateXTo (251 * 360, duration),
  image.RotateYTo (199 * 360, duration)
);
```

在此範例中，`Task.WhenAll`方法呼叫包含三個工作，其中每一個執行超過 10 分鐘的時間。 每個`Task`可讓不同數目的 360 度旋轉 – 307 旋轉，如[ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))，為 251 旋轉[ `RotateXTo` ](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))，和 199 旋轉的[ `RotateYTo`](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)). 這些值會是質數，如此可確保旋轉未同步，並因此將不會導致重複的模式。

下列螢幕擷取畫面會顯示在每個平台上進行多個旋轉：

![](simple-images/multiple-rotations.png "複合的動畫")

## <a name="canceling-animations"></a>正在取消動畫

應用程式可以取消呼叫的一或多個動畫`static` [ `ViewExtensions.CancelAnimations` ](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement))方法，如下列程式碼範例所示：

```csharp
ViewExtensions.CancelAnimations (image);
```

這將會立即取消目前正在執行的所有動畫[ `Image` ](xref:Xamarin.Forms.Image)執行個體。

## <a name="summary"></a>總結

這篇文章示範建立，並取消使用的動畫[ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions)類別。 這個類別提供擴充方法，可用來建構簡單的動畫的旋轉和調整其規模，翻譯，淡[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)執行個體。


## <a name="related-links"></a>相關連結

- [非同步支援概觀](~/cross-platform/platform/async.md)
- [基本動畫 （範例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/animation/basic/)
- [ViewExtensions](xref:Xamarin.Forms.ViewExtensions)
