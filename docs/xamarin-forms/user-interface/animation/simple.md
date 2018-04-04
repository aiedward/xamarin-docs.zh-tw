---
title: 簡單的動畫
description: ViewExtensions 類別提供擴充方法可以用來建構簡單動畫。 本文示範如何建立並取消使用 ViewExtensions 類別的動畫。
ms.prod: xamarin
ms.assetid: 4A6FAE5A-848F-4CE0-BFA1-22A6309B5225
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/27/2017
ms.openlocfilehash: 0d2cc30f9bc1ae5602394b8ca2d8e75517a01b54
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="simple-animations"></a>簡單的動畫

_ViewExtensions 類別提供擴充方法可以用來建構簡單動畫。本文示範如何建立並取消使用 ViewExtensions 類別的動畫。_


[ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/)類別提供可用來建立簡單的動畫的下列擴充方法：

- [`TranslateTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.TranslateTo/p/Xamarin.Forms.VisualElement/System.Double/System.Double/System.UInt32/Xamarin.Forms.Easing/) 繪製[ `TranslationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/)和[ `TranslationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/)屬性[ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/)。
- [`ScaleTo`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) 繪製[ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)屬性[ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/)。
- [`RelScaleTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RelScaleTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) 適用於動畫的增量增加或減少以[ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)屬性[ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/)。
- [`RotateTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) 繪製[ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/)屬性[ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/)。
- [`RelRotateTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RelRotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) 適用於動畫的增量增加或減少以[ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/)屬性[ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/)。
- [`RotateXTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateXTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) 繪製[ `RotationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.RotationX/)屬性[ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/)。
- [`RotateYTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateYTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) 繪製[ `RotationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.RotationY/)屬性[ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/)。
- [`FadeTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.FadeTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) 繪製[ `Opacity` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Opacity/)屬性[ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/)。

根據預設，每個動畫會 250 毫秒。 不過，建立動畫時，可以指定每個動畫的持續時間。

[ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/)類別還包括[ `CancelAnimations` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.CancelAnimations/p/Xamarin.Forms.VisualElement/)方法可以用來取消任何動畫。

> [!NOTE]
> [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/)類別提供[ `LayoutTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.LayoutTo/p/Xamarin.Forms.VisualElement/Xamarin.Forms.Rectangle/System.UInt32/Xamarin.Forms.Easing/)擴充方法。 不過，這個方法僅供以動畫方式顯示可以包含大小和位置變更版面配置狀態之間轉換的版面配置。 因此，它只應由[ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/)子類別。

中的動畫擴充方法[ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/)類別都非同步且傳回`Task<bool>`物件。 傳回值是`false`動畫完成時，如果和`true`如果動畫已取消。 因此，動畫方法應通常搭配`await`運算子，就會讓您能夠輕鬆地判斷何時完成動畫。 此外，它就會變成可以完成上一個方法之後執行的後續動畫方法以建立循序的動畫。 如需詳細資訊，請參閱[複合動畫](#compound)。

如果需要讓動畫完成在背景中，然後在`await`運算子，則可以省略。 在此案例中，快速會啟動動畫，以在背景中發生的動畫後傳回動畫擴充方法。 建立複合的動畫時，這項作業可以採取的優點。 如需詳細資訊，請參閱[複合動畫](#composite)。

如需有關`await`運算子，請參閱[非同步支援概觀](~/cross-platform/platform/async.md)。

## <a name="single-animations"></a>單一動畫

在每個擴充方法[ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/)實作逐漸屬性從一個值變更為另一個值經過一段時間的單一動畫作業。 本節會探索每個動畫作業。

### <a name="rotation"></a>旋轉

下列程式碼範例示範如何使用[ `RotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)方法，以動畫方式顯示[ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/)屬性[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/):

```csharp
await image.RotateTo (360, 2000);
image.Rotation = 0;
```

此程式碼繪製[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)旋轉 360 度最多超過 2 秒 （2000年毫秒為單位） 的執行個體。 [ `RotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)方法會取得目前[ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/)屬性的動畫的起始值，並再旋轉該值從其第一個引數 (360)。 動畫之後完成，映像的[ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/)屬性重設為 0。 如此可確保`Rotation`動畫完成，而這可避免其他旋轉後，將不會維持在 360 的屬性。

下列螢幕擷取畫面會顯示在每個平台上進行輪替：

![](simple-images/rotateto.png "旋轉動畫")

### <a name="relative-rotation"></a>相對的旋轉

下列程式碼範例示範如何使用[ `RelRotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RelRotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)方法來以累加方式增加或減少[ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/)屬性[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/):

```csharp
await image.RelRotateTo (360, 2000);
```

此程式碼繪製[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)旋轉 360 度從其開始位置超過 2 秒 （2000年毫秒為單位） 的執行個體。 [ `RelRotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RelRotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)方法會取得目前[ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/)屬性的動畫的起始值，並再旋轉從該值的值加上其第一個引數 (360)。 這可確保每個動畫一定會從開始位置為 360 度旋轉，旋轉。 因此，如果動畫已經在進行中時，會叫用新的動畫，它會從目前位置開始，並可能不是為 360 度遞增的位置結束。

下列螢幕擷取畫面會顯示在每個平台上進行相對旋轉：

![](simple-images/relrotateto.png "相對的旋轉動畫")

### <a name="scaling"></a>縮放

下列程式碼範例示範如何使用[ `ScaleTo` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)方法，以動畫方式顯示[ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)屬性[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/):

```csharp
await image.ScaleTo (2, 2000);
```

此程式碼繪製[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)透過相應增加其大小的兩倍至超過 2 秒 （2000年毫秒為單位） 的執行個體。 [ `ScaleTo` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)方法會取得目前[ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)屬性值 （預設值為 1） 開始的動畫和其第一個引數 (2) 從該值然後標尺。 這有擴充到其大小的兩倍的映像的大小影響。

下列螢幕擷取畫面會顯示在每個平台上進行的調整：

![](simple-images/scaleto.png "調整動畫")

### <a name="relative-scaling"></a>相對縮放比例

下列程式碼範例示範如何使用[ `RelScaleTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RelScaleTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)方法，以動畫方式顯示[ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)屬性[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/):

```csharp
await image.RelScaleTo (2, 2000);
```

此程式碼繪製[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)透過相應增加其大小的兩倍至超過 2 秒 （2000年毫秒為單位） 的執行個體。 [ `RelScaleTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RelScaleTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)方法會取得目前[ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)開始的動畫，然後的規模從該值的值加上其第一個引數 (2) 的屬性值。 這可確保每個動畫一律會從開始位置 2 的縮放比例。

### <a name="scaling-and-rotation-with-anchors"></a>縮放和旋轉錨點

[ `AnchorX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorX/)和[ `AnchorY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorY/)屬性設定的縮放或旋轉的中心[ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/)和[ `Scale`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)屬性。 因此，其值也會影響[ `RotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)和[ `ScaleTo` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)方法。

指定[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) ，已放在配置的中心，下列程式碼範例示範如何藉由設定旋轉中心的版面配置周圍的影像其[ `AnchorY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorY/)屬性：

```csharp
image.AnchorY = (Math.Min (absoluteLayout.Width, absoluteLayout.Height) / 2) / image.Height;
await image.RotateTo (360, 2000);
```

若要旋轉[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)中心周圍的版面配置的執行個體[ `AnchorX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorX/)和[ `AnchorY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorY/)屬性必須設定為的值相對於寬度和高度`Image`。 在此範例中，中央`Image`定義為中心的版面配置，因此預設`AnchorX`值為 0.5 不需要變更。 不過，`AnchorY`屬性已重新定義的值從頂端`Image`版面配置的中心點。 如此可確保`Image`會將完整的中心點的配置，360 度旋轉，如下列螢幕擷取畫面所示：

![](simple-images/rotate-anchors.png "錨點的旋轉動畫")

### <a name="translation"></a>轉譯

下列程式碼範例示範如何使用[ `TranslateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.TranslateTo/p/Xamarin.Forms.VisualElement/System.Double/System.Double/System.UInt32/Xamarin.Forms.Easing/)方法，以動畫方式顯示[ `TranslationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/)和[ `TranslationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/) 屬性[`Image`](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/):

```csharp
await image.TranslateTo (-100, -100, 1000);
```

此程式碼繪製[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)凙它水平及垂直超過 1 秒 （1000年毫秒為單位） 的執行個體。 [ `TranslateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.TranslateTo/p/Xamarin.Forms.VisualElement/System.Double/System.Double/System.UInt32/Xamarin.Forms.Easing/)方法同時會轉譯影像 100 像素為單位向左和向上 100 像素。 這是因為第一個和第二個引數是這兩個字。 提供正數，會將轉譯的向右和向下的映像。

下列螢幕擷取畫面會顯示在每個平台上進行翻譯：

![](simple-images/translateto.png "轉譯動畫")

> [!NOTE]
> 如果項目一開始配置關閉螢幕，然後轉譯在螢幕上轉譯後的項目輸入的配置會保持關閉螢幕，而且使用者無法互動。 因此，建議，應該配置檢視中的最後一個位置，而且任何需要執行的轉譯。

### <a name="fading"></a>淡出

下列程式碼範例示範如何使用[ `FadeTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.FadeTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)方法，以動畫方式顯示[ `Opacity` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Opacity/)屬性[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/):

```csharp
image.Opacity = 0;
await image.FadeTo (1, 4000);
```

此程式碼繪製[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)淡超過 4 秒 （4000 毫秒為單位） 執行個體。 [ `FadeTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.FadeTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)方法會取得目前[ `Opacity` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Opacity/)開始的動畫，然後淡化中的該值從其第一個引數 (1) 的屬性值。

下列螢幕擷取畫面會顯示在每個平台上進行淡出：

![](simple-images/fadeto.png "淡出動畫")

<a name="compound" />

## <a name="compound-animations"></a>複合動畫

複合動畫的動畫，循序結合，可以使用建立`await`運算子，如下列程式碼範例所示：

```csharp
await image.TranslateTo (-100, 0, 1000);    // Move image left
await image.TranslateTo (-100, -100, 1000); // Move image up
await image.TranslateTo (100, 100, 2000);   // Move image diagonally down and right
await image.TranslateTo (0, 100, 1000);     // Move image left
await image.TranslateTo (0, 0, 1000);       // Move image up
```

在此範例中， [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)轉譯超過 6 秒 （6000 毫秒為單位）。 翻譯的`Image`使用五個動畫`await`指出每個動畫循序執行的運算子。 因此，後續的動畫方法執行之後已完成上一個方法。

<a name="composite" />

## <a name="composite-animations"></a>複合動畫

複合動畫是動畫的組合兩個或多個動畫同時執行的位置。 可以混合受期待與非等候動畫建立複合的動畫，如下列程式碼範例所示：

```csharp
image.RotateTo (360, 4000);
await image.ScaleTo (2, 2000);
await image.ScaleTo (1, 2000);
```

在此範例中， [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)調整，而且同時旋轉超過 4 秒 （4000 毫秒為單位）。 縮放比例`Image`會使用兩個循序的動畫旋轉的同時間發生。 [ `RotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)方法執行時不需`await`運算子，並立即傳回的第一個[ `ScaleTo` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)然後開始的動畫。 `await`對第一個運算子`ScaleTo`方法呼叫會延遲，第二個`ScaleTo`方法呼叫，直到第一個`ScaleTo`方法呼叫已完成。 此時`RotateTo`動畫是一半方式完成和`Image`會旋轉 180 度。 在最後 2 秒 （2000年毫秒為單位），第二個`ScaleTo`動畫和`RotateTo`動畫同時完成。

### <a name="running-multiple-asynchronous-methods-concurrently"></a>同時執行多個非同步方法

`static` `Task.WhenAny`和`Task.WhenAll`方法可用來同時執行多個非同步方法，因此可以用來建立複合的動畫。 這兩個方法會傳回`Task`物件，並接受方法的集合，每個傳回`Task`物件。 `Task.WhenAny`方法完成時完成其集合中的任何方法執行時，如下列程式碼範例所示：

```csharp
await Task.WhenAny<bool>
(
  image.RotateTo (360, 4000),
  image.ScaleTo (2, 2000)
);
await image.ScaleTo (1, 2000);
```

在此範例中，`Task.WhenAny`方法呼叫包含兩個工作。 第一項工作將影像旋轉超過 4 秒 （4000 毫秒為單位），和第二項工作會縮放影像超過 2 秒 （2000年毫秒為單位）。 第二個工作完成時，`Task.WhenAny`方法呼叫完成。 不過，即使[ `RotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)方法仍在執行中，第二個[ `ScaleTo` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)方法可以開始。

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

在此範例中，`Task.WhenAll`方法呼叫包含三個工作，其中每個執行超過 10 分鐘。 每個`Task`讓不同數目的 360 度旋轉 – 307 旋轉的[ `RotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)，如 251 旋轉[ `RotateXTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateXTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)，和 199 旋轉的[ `RotateYTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateYTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/). 這些值是質數，因此確保旋轉未同步，並因此將不會導致重複的模式。

下列螢幕擷取畫面會顯示在每個平台上進行多個旋轉：

![](simple-images/multiple-rotations.png "複合動畫")

## <a name="canceling-animations"></a>取消動畫

應用程式可以取消一或多個呼叫的動畫`static` [ `ViewExtensions.CancelAnimations` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.CancelAnimations/p/Xamarin.Forms.VisualElement/)方法，如下列程式碼範例所示：

```csharp
ViewExtensions.CancelAnimations (image);
```

這將會立即取消目前正在執行的所有動畫[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)執行個體。

## <a name="summary"></a>總結

本文示範建立和取消使用動畫[ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/)類別。 這個類別提供擴充方法，可以用來建構簡單旋轉、 縮放、 轉譯，和淡出動畫[ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/)執行個體。


## <a name="related-links"></a>相關連結

- [非同步支援概觀](~/cross-platform/platform/async.md)
- [基本動畫 （範例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/animation/basic/)
- [ViewExtensions](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/)
