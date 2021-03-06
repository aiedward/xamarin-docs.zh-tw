---
title: Xamarin.Forms 通用控制項屬性、方法和事件
description: 本文說明在 VisualElement 類別上定義的通用屬性、方法和事件，通常用於衍生類別中。
ms.prod: xamarin
ms.assetId: 85A0CCF5-C1D8-40BB-927F-A4D944E5534D
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/22/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c709331b743d6d6ac1f72fe87dae342b4478a920
ms.sourcegitcommit: 9a83e49eb4d2afb8b3a99ac2347f4b9f8a19e76b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93330958"
---
# <a name="xamarinforms-common-control-properties-methods-and-events"></a>Xamarin.Forms 通用控制項屬性、方法和事件

Xamarin.Forms `VisualElement` 類別是應用程式中大部分使用的控制項的基類（base class） Xamarin.Forms 。 `VisualElement`類別會定義用於衍生類別的許多[屬性](#properties)、[方法](#methods)和[事件](#events)。

## <a name="properties"></a>屬性

下列屬性適用于 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 物件。

### [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX)

`AnchorX`屬性是一個 `double` 值，可定義 X 軸上的中心點以進行縮放和旋轉等轉換。 預設值為0.5。

### [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY)

`AnchorY`屬性是一個 `double` 值，會定義 Y 軸上的中心點以進行縮放和旋轉等轉換。 預設值為0.5。

### `Background`

`Background`屬性是一個 `Brush` 值，可在任何控制項中將筆刷當做背景使用。 預設值是 `Brush.Default`。

### [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor)

`BackgroundColor`屬性是 `Color` ，可決定控制項的背景色彩。 如果未設定，背景將會是預設 `Color` 物件，它會呈現為透明。

### [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors)

`Behaviors`屬性是 `List` `Behavior` 物件的。 行為可讓您將可重複使用的功能附加至清單，藉此將這些功能附加至元素 `Behaviors` 。 如需類別的詳細資訊 `Behavior` ，請參閱[ Xamarin.Forms 行為](~/xamarin-forms/app-fundamentals/behaviors/index.md)。

### [`Bounds`](xref:Xamarin.Forms.VisualElement.Bounds)

`Bounds`屬性是唯讀 `Rectangle` 物件，代表控制項所佔用的空間。 在配置 `Bounds` 週期期間會指派屬性值。 `Rectangle` `struct` 包含用來測試矩形交集和內含專案的實用屬性和方法。 如需詳細資訊，請參閱[ Xamarin.Forms 矩形 API](xref:Xamarin.Forms.Rectangle)。

### `Clip`

`Clip`屬性（property）是 `Geometry` 定義元素內容大綱的物件。 若要定義剪輯，請使用 `Geometry` 物件（例如） `EllipseGeometry` 來設定元素的 `Clip` 屬性。 只有位於幾何區域內的區域才會顯示。 如需詳細資訊，請參閱 [具有幾何的剪輯](~/xamarin-forms/user-interface/shapes/geometries.md#clip-with-a-geometry)。

### [`Effects`](xref:Xamarin.Forms.Element.Effects)

`Effects`屬性是 `List` `Effect` 繼承自類別之物件的 [`Element`](xref:Xamarin.Forms.Element) 。 效果可讓您自訂原生控制項，通常用於小型樣式變更。 如需類別的詳細資訊 `Effect` ，請參閱[ Xamarin.Forms 效果](~/xamarin-forms/app-fundamentals/effects/index.md)。

### [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)

`FlowDirection`屬性是 `FlowDirection` 列舉值。 流程方向可以設定為 `MatchParent` 、 `LeftToRight` 或， `RightToLeft` 並決定版面配置順序和方向。 `FlowDirection`屬性通常用來支援由右至左讀取的語言。

### [`Height`](xref:Xamarin.Forms.VisualElement.Height)

`Height`屬性是唯讀 `double` 值，描述控制項的呈現高度。 `Height`屬性是在配置週期期間計算的，而且無法直接設定。 您可以使用 [HeightRequest 屬性](#heightrequest)來要求控制項的高度。

### [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest)

`HeightRequest`屬性是一個 `double` 值，可決定控制項的所需高度。 控制項的絕對高度可能不符合要求的值。 如需詳細資訊，請參閱 [要求屬性](#request-properties)。

### [`InputTransparent`](xref:Xamarin.Forms.VisualElement.InputTransparent)

`InputTransparent`屬性是 `bool` ，可決定控制項是否接收使用者輸入。 預設值為 `false` ，確保元素會收到輸入。 設定時，此屬性會傳送至子項目。 `InputTransparent`將屬性（property）上的屬性（property）設定為， `true` 會導致版面配置內的所有專案都未接收到輸入。

### [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled)

`IsEnabled`屬性是一個 `bool` 值，可決定控制項是否對使用者輸入做出回應。 預設值是 `true`。 將這個屬性設定為 false，會讓控制項無法接受使用者輸入。

### [`IsFocused`](xref:Xamarin.Forms.VisualElement.IsFocused)

`IsFocused`屬性是一個 `bool` 值，描述控制項目前是否為焦點物件。 在 [`Focus`](#focus) 控制項上呼叫方法會導致 `IsFocused` 值設定為 true。 呼叫 [`Unfocus`](#unfocus) 方法會將這個屬性設定為 false。

### [`IsTabStop`](xref:Xamarin.Forms.VisualElement.IsTabStop)

`IsTabStop`屬性（property）是一個 `bool` 值，定義當使用者透過 tab 鍵前進控制項時，控制項是否會收到焦點。 如果這個屬性為 false，則 `TabIndex` 屬性不會有任何作用。

### [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible)

`IsVisible`屬性是一個 `bool` 值，可決定是否要呈現控制項。 將 `IsVisible` 屬性設為 false 的控制項將不會顯示，在版面配置週期期間不會考慮空間計算，也不能接受使用者輸入。

### [`MinimumHeightRequest`](xref:Xamarin.Forms.VisualElement.MinimumHeightRequest)

`MinimumHeightRequest`屬性是一個 `double` 值，可決定當兩個元素競爭有限空間時，如何處理溢位。 設定 `MinimumHeightRequest` 屬性可讓版面配置進程將專案向下調整為要求的最小維度。 如果未 `MinimumHeightRequest` 指定，預設值為-1，而且版面配置程式會將視為 `HeightRequest` 最小值。 這表示沒有值的元素 `MinimumHeightRequest` 將不會有可調整的高度。

如需詳細資訊，請參閱 [要求屬性的最小值](#minimum-request-properties)。

### [`MinimumWidthRequest`](xref:Xamarin.Forms.VisualElement.MinimumWidthRequest)

`MinimumWidthRequest`屬性是一個 `double` 值，可決定當兩個元素競爭有限空間時，如何處理溢位。 設定 `MinimumWidthRequest` 屬性可讓版面配置進程將專案向下調整為要求的最小維度。 如果未 `MinimumWidthRequest` 指定，預設值為-1，而且版面配置程式會將視為 `WidthRequest` 最小值。 這表示沒有值的元素 `MinimumWidthRequest` 將不會有可調整的寬度。

如需詳細資訊，請參閱 [要求屬性的最小值](#minimum-request-properties)。

### [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity)

`Opacity`屬性是 `double` 從零到一的值，可在轉譯期間決定控制項的不透明度。 這個屬性的預設值是1.0。 從0到1的範圍以外的值將會是壓制。 `Opacity`只有當屬性為時，才會套用此屬性 [`IsVisible`](#isvisible) `true` 。 會反復套用不透明度。 因此，如果父控制項有0.5 不透明度，且其子系具有0.5 不透明度，則子系會轉譯為有效的0.25 不透明度值。 將 `Opacity` 輸入控制項的屬性設定為0時，會有未定義的行為。

### [`Parent`](xref:Xamarin.Forms.Element.Parent)

`Parent` 屬性繼承自 `Element` 類別。 這個屬性是 `Element` 控制項父系的物件。 `Parent`當專案加入為另一個專案的子系時，通常會自動在元素上設定屬性。

### [`Resources`](xref:Xamarin.Forms.VisualElement.Resources)

`Resources`屬性（property）是以索引 `ResourceDictionary` 鍵/值組填入的實例，這些索引鍵/值組通常會在執行時間從 XAML 填入。 此字典可讓應用程式開發人員在編譯時間和執行時間重複使用 XAML 中定義的物件。 字典中的索引鍵會從 `x:Key` XAML 標記的屬性填入。 從 XAML 建立的物件會插入中的， `ResourceDictionary` 以取得指定的索引鍵。 初始化之後。

如需詳細資訊，請參閱 [資源字典](~/xamarin-forms/xaml/resource-dictionaries.md)。

### [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)

`Rotation`屬性是 `double` 介於0和360之間的值，用來定義 Z 軸的旋轉（以度為單位）。 這個屬性的預設值為 0。 相對於和值的套用 [`AnchorX`](#anchorx) 旋轉 [`AnchorY`](#anchory) 。

### [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX)

`RotationX`屬性是 `double` 介於0和360之間的值，用來定義 X 軸的旋轉（以度為單位）。 這個屬性的預設值為 0。 相對於和值的套用 [`AnchorX`](#anchorx) 旋轉 [`AnchorY`](#anchory) 。

### [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)

`RotationY`屬性是 `double` 介於0和360之間的值，用來定義 Y 軸的旋轉（以度為單位）。 這個屬性的預設值為 0。 相對於和值的套用 [`AnchorX`](#anchorx) 旋轉 [`AnchorY`](#anchory) 。

### [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)

`Scale`屬性是 `double` 定義控制項小數位數的值。 這個屬性的預設值是1.0。 會套用相對於和值的小數位數 [`AnchorX`](#anchorx) [`AnchorY`](#anchory) 。

### [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX)

`ScaleX`屬性（property）是一個 `double` 值，定義沿著 X 軸的控制項尺規。 這個屬性的預設值是1.0。 `ScaleX`屬性會相對於 [`AnchorX`](#anchorx) 該值套用。

### [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY)

`ScaleY`屬性是一個 `double` 值，可定義沿著 Y 軸的控制項尺規。 這個屬性的預設值是1.0。 `ScaleY`屬性會相對於 [`AnchorY`](#anchory) 該值套用。

### [`Style`](xref:Xamarin.Forms.NavigableElement.Style)

`Style` 屬性繼承自 `NavigableElement` 類別。 這個屬性是類別的實例 `Style` 。 `Style`類別包含觸發程式、setter 和行為，以定義視覺元素的外觀和行為。 如需詳細資訊，請參閱[ Xamarin.Forms XAML 樣式](~/xamarin-forms/user-interface/styles/xaml/index.md)。

### [`StyleClass`](xref:Xamarin.Forms.NavigableElement.StyleClass)

`StyleClass`屬性是 `string` 代表類別名稱的物件清單 `Style` 。 這個屬性繼承自 `NavigableElement` 類別。 `StyleClass`屬性允許將多個樣式屬性套用至 `VisualElement` 實例。 如需詳細資訊，請參閱[ Xamarin.Forms 樣式類別](~/xamarin-forms/user-interface/styles/xaml/style-class.md)。

### [`TabIndex`](xref:Xamarin.Forms.VisualElement.TabIndex)

`TabIndex`屬性（property）是一個 `int` 值，它會在使用 tab 鍵的控制項前進時定義控制項順序。 `TabIndex`屬性（property）是在介面上定義的屬性 `ITabStopElement` （class）的實作為 `VisualElement` 。

### [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)

`TranslationX`屬性是一個 `double` 值，可定義要在 X 軸上套用的差異轉譯。 轉譯會在版面配置之後套用，而且通常用於套用動畫。 轉譯其父容器界限外的專案，以避免輸入無法運作。

如需詳細資訊，請參閱[中 Xamarin.Forms 的動畫](~/xamarin-forms/user-interface/animation/index.md)。

### [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)

`TranslationY`屬性是一個 `double` 值，可定義要在 Y 軸上套用的差異轉譯。 轉譯會在版面配置之後套用，而且通常用於套用動畫。 轉譯其父容器界限外的專案，以避免輸入無法運作。

如需詳細資訊，請參閱[中 Xamarin.Forms 的動畫](~/xamarin-forms/user-interface/animation/index.md)。

### [`Triggers`](xref:Xamarin.Forms.VisualElement.Triggers)

`Triggers`屬性是物件的唯讀 `List` `TriggerBase` 。 觸發程式可讓應用程式開發人員以 XAML 表達動作，以變更控制項的視覺外觀，以回應事件或屬性變更。 如需詳細資訊，請參閱[ Xamarin.Forms 觸發](~/xamarin-forms/app-fundamentals/triggers.md)程式。

### [`Visual`](xref:Xamarin.Forms.VisualElement.Visual)

`Visual`屬性是一個 `IVisual` 實例，可讓您建立轉譯器並選擇性地套用至 `VisualElement` 實例。 `Visual`屬性設定為符合其父系，因此在元件上定義轉譯器也會套用至該元件的任何子系。 如果控制項或其祖系上未設定任何自訂轉譯器，則會使用預設轉譯器 Xamarin.Forms 。 如需詳細資訊，請參閱[ Xamarin.Forms 視覺效果](~/xamarin-forms/user-interface/visual/index.md)。

### [`Width`](xref:Xamarin.Forms.VisualElement.Width)

`Width`屬性是唯讀 `double` 值，描述控制項的呈現寬度。 `Width`屬性是在配置週期期間計算的，而且無法直接設定。 您可以使用 [WidthRequest 屬性](#widthrequest)來要求控制項的寬度。

### [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)

`WidthRequest`屬性是一個 `double` 值，可決定控制項所需的寬度。 控制項的絕對寬度可能不符合要求的值。 如需詳細資訊，請參閱 [要求屬性](#request-properties)。

### [`X`](xref:Xamarin.Forms.VisualElement.X)

`X`屬性是唯讀 `double` 值，可描述控制項目前的 X 位置。

### [`Y`](xref:Xamarin.Forms.VisualElement.Y)

`Y`屬性是唯讀 `double` 值，可描述控制項目前的 Y 位置。

## <a name="methods"></a>方法

類別可以使用下列方法 `VisualElement` 。 如需完整清單，請參閱 [VISUALELEMENT API 方法](xref:Xamarin.Forms.VisualElement#methods)。

### [`FindByName`](xref:Xamarin.Forms.Element.FindByName*)

`FindByName`方法繼承自 `Element` 類別，且具有下列簽章：

```csharp
public object FindByName (string name)
```

這個方法會搜尋所提供引數的所有子專案 `name` ，並傳回具有指定之名稱的元素。 如果找不到符合的項目，就會傳回 `null`。

### [`Focus`](xref:Xamarin.Forms.VisualElement.Focus)

`Focus`方法會嘗試設定元素的焦點。 此方法具有下列簽章：

```csharp
public bool Focus ()
```

`Focus` `true` 如果已成功設定鍵盤焦點，且 `false` 方法呼叫不會導致焦點變更，則此方法會傳回。 元素必須能夠接收此方法的焦點，才能運作。 `Focus`在螢幕上或未啟用的元素上呼叫方法具有未定義的行為。

### [`Unfocus`](xref:Xamarin.Forms.VisualElement.Unfocus)

`Unfocus`方法會嘗試移除元素的焦點。 此方法具有下列簽章：

```csharp
public void Unfocus ()
```

專案必須已有這個方法的焦點，才能運作。

## <a name="events"></a>事件

下列事件可在類別上取得 `VisualElement` 。 如需完整清單，請參閱[ Xamarin.Forms VisualElement 事件](xref:Xamarin.Forms.VisualElement#events)。

### [`Focused`](xref:Xamarin.Forms.VisualElement.Focused)

`Focused`每當實例收到焦點時，就會引發事件 `VisualElement` 。 此事件不會透過堆疊進行反升，而是 Xamarin.Forms 從原生控制項直接收到。 此事件是由 [`IsFocused`](#isfocused) 屬性 setter 發出。

### [`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged)

`SizeChanged`每當 `VisualElement` 實例 `Height` 或屬性變更時，就會引發事件 `Width` 。 如果開發人員想要直接回應大小變更，而不是回應變更後事件，則應改為執行 [`OnSizeAllocated`](xref:Xamarin.Forms.VisualElement.OnSizeAllocated*) 虛擬方法。

### [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused)

`Unfocused`每當實例失去焦點時，就會引發事件 `VisualElement` 。 此事件不會透過堆疊進行反升，而是 Xamarin.Forms 從原生控制項直接收到。 此事件是由 [`IsFocused`](#isfocused) 屬性 setter 發出。

## <a name="units-of-measurement"></a>測量單位

Android、iOS 和 UWP 平臺都有不同的度量單位，可能會因裝置而異。 Xamarin.Forms 使用與平臺無關的度量單位，在不同的裝置和平臺上標準化單位。 在中，每個單位有160單位或64單位 Xamarin.Forms 。

## <a name="request-properties"></a>要求屬性

名稱包含 "request" 的屬性會定義所需的值，這可能不符合實際呈現的值。 例如， `HeightRequest` 可能會設定為150，但如果配置只允許100單位的空間，則呈現的 `Height` 控制項只會是100。 轉譯的大小會受到可用空間和包含元件的影響。

## <a name="minimum-request-properties"></a>要求屬性的最小值

要求屬性的最小值包括 `MinimumHeightRequest` 和 `MinimumWidthRequest` ，並可讓您更精確地控制專案如何處理彼此之間的溢位。 不過，與這些屬性相關的版面配置行為有一些重要的考慮。

### <a name="unspecified-minimum-property-values"></a>未指定的最小屬性值

如果未設定最小值，則最小屬性會預設為-1。 版面配置進程會忽略此值，並將絕對值視為最小值。 這種行為的實際結果是，未指定最小值的元素 **將不會** 壓縮。 指定最小值的元素 **將會** 縮小。

下列 XAML 會 `BoxView` 以水準方式顯示兩個元素 `StackLayout` ：

```xaml
<StackLayout Orientation="Horizontal">
    <BoxView HeightRequest="100" BackgroundColor="Purple" WidthRequest="500"></BoxView>
    <BoxView HeightRequest="100" BackgroundColor="Green" WidthRequest="500" MinimumWidthRequest="250"></BoxView>
</StackLayout>
```

第一個 `BoxView` 實例要求的寬度為500，且未指定寬度下限。 第二個 `BoxView` 實例要求寬度為500，最小寬度為250。 如果父元素的寬度 `StackLayout` 不足以同時包含這兩個元件，則配置進程會將第一個 `BoxView` 實例視為最小寬度500，因為未指定任何其他有效的最小值。 第二個 `BoxView` 實例可以縮小為250，而且會縮小以符合其寬度，直到其寬度達到250單位為止。

如果所需的行為是要讓第一個 `BoxView` 實例相應減少，且沒有最小寬度，則 `MinimumWidthRequest` 必須將設定為有效的值，例如0。

### <a name="minimum-and-absolute-property-values"></a>最小和絕對屬性值

當最小值大於絕對值時，此行為是未定義的。 例如，如果 `WidthRequest` 設為100，則 `MinimumWidthRequest` 屬性絕不能超過100。 指定最小的屬性值時，您應該一律指定絕對值，以確保絕對值大於最小值。

### <a name="minimum-properties-within-a-grid"></a>方格內的最小屬性

`Grid` 版面配置具有自己的系統，可用於資料列和資料行的相對大小調整。 在 `MinimumWidthRequest` 版面配置中使用或 `MinimumHeightRequest` `Grid` 不會有效果。 如需詳細資訊，請參閱[ Xamarin.Forms Grid](~/xamarin-forms/user-interface/layouts/grid.md)。

## <a name="related-links"></a>相關連結

- [VisualElement API](xref:Xamarin.Forms.VisualElement)
