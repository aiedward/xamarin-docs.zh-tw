---
title: Xamarin. Forms 通用控制項屬性、方法和事件
description: 本文說明在 VisualElement 類別上定義的通用屬性、方法和事件，通常用於衍生類別中。
ms.prod: xamarin
ms.assetId: 85A0CCF5-C1D8-40BB-927F-A4D944E5534D
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 08/21/2019
ms.openlocfilehash: 6d10e665c6461655440ddfb2c524cb56a14337f6
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304209"
---
# <a name="xamarinforms-common-control-properties-methods-and-events"></a>Xamarin. Forms 通用控制項屬性、方法和事件

Xamarin `VisualElement` 類別是用於 Xamarin. Forms 應用程式中大部分控制項的基類。 `VisualElement` 類別會定義用於衍生類別中的許多[屬性](#properties)、[方法](#methods)和[事件](#events)。

## <a name="properties"></a>屬性

下列屬性適用于 `VisualElement` 實例。 如需完整清單，請參閱[VISUALELEMENT API 屬性](xref:Xamarin.Forms.VisualElement#properties)。

### [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX)

`AnchorX` 屬性是 `double` 值，可定義 X 軸上的中心點以進行轉換，例如縮放和旋轉。 預設值為0.5。

### [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY)

`AnchorY` 屬性是 `double` 值，可定義 X 軸上的中心點以進行轉換，例如縮放和旋轉。 預設值為0.5。

### [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor)

`BackgroundColor` 屬性是決定控制項背景色彩的 `Color`。 如果未設定，則背景會是預設 `Color` 物件，它會呈現為透明。

### [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors)

`Behaviors` 屬性是 `Behavior` 物件的 `List`。 行為可讓您將可重複使用的功能加入至 `Behaviors` 清單，藉此將其附加至元素。 如需 `Behavior` 類別的詳細資訊，請參閱[Xamarin. 表單行為](~/xamarin-forms/app-fundamentals/behaviors/index.md)。

### [`Bounds`](xref:Xamarin.Forms.VisualElement.Bounds)

`Bounds` 屬性是唯讀的 `Rectangle` 物件，代表控制項所佔用的空間。 在版面配置週期期間，會指派 `Bounds` 屬性值。 `Rectangle` `struct` 包含有用的屬性和方法來測試矩形的交集和內含專案。 如需詳細資訊，請參閱[Xamarin 矩形 API](xref:Xamarin.Forms.Rectangle)。

### [`Effects`](xref:Xamarin.Forms.Element.Effects)

`Effects` 屬性是 `Effect` 物件的 `List`，繼承自 `Element`（x： Xamarin. Forms. Element）類別。 效果可讓您自訂原生控制項，而且通常用於小型的樣式變更。 如需有關 `Effect` 類別的詳細資訊，請參閱[Xamarin 效果](~/xamarin-forms/app-fundamentals/effects/index.md)。

### [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)

`FlowDirection` 屬性是 `FlowDirection` 的列舉值。 [流程方向] 可以設定為 [`MatchParent`]、[`LeftToRight`] 或 [`RightToLeft`]，並決定版面配置順序和方向。 `FlowDirection` 屬性通常是用來支援從右至左讀取的語言。

### [`Height`](xref:Xamarin.Forms.VisualElement.Height)

`Height` 屬性是唯讀的 `double` 值，可描述控制項的呈現高度。 `Height` 屬性是在版面配置週期期間計算，而且無法直接設定。 您可以使用[HeightRequest 屬性](#heightrequest)來要求控制項的高度。

### [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest)

`HeightRequest` 屬性是一個 `double` 值，可決定控制項的所需高度。 控制項的絕對高度可能不符合要求的值。 如需詳細資訊，請參閱[要求屬性](#request-properties)。

### [`InputTransparent`](xref:Xamarin.Forms.VisualElement.InputTransparent)

`InputTransparent` 屬性是決定控制項是否接收使用者輸入的 `bool`。 預設值為 `false`，可確保元素會收到輸入。 這個屬性會在設定時傳送至子專案。 在版面配置類別上將 `InputTransparent` 屬性設定為 `true`，將會導致在配置內的所有專案都不會收到輸入。

### [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled)

`IsEnabled` 屬性是一個 `bool` 值，可決定控制項是否會回應使用者輸入。 預設值是 `true`。 將此屬性設定為 false，將會讓控制項無法接受使用者輸入。

### [`IsFocused`](xref:Xamarin.Forms.VisualElement.IsFocused)

`IsFocused` 屬性是描述控制項目前是否為焦點物件的 `bool` 值。 在控制項上呼叫[`Focus`](#focus)方法會導致 `IsFocused` 值設定為 true。 呼叫[`Unfocus`](#unfocus)方法會將此屬性設定為 false。

### [`IsTabStop`](xref:Xamarin.Forms.VisualElement.IsTabStop)

`IsTabStop` 屬性是一個 `bool` 值，它會定義當使用者使用 tab 鍵向前移動控制項時，控制項是否會收到焦點。 如果此屬性為 false，`TabIndex` 屬性將不會有任何作用。

### [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible)

`IsVisible` 屬性是一個 `bool` 值，可決定是否呈現控制項。 如果控制項的 `IsVisible` 屬性設為 false，則不會顯示，在版面配置週期期間，將不會考慮空間的計算，也無法接受使用者輸入。

### [`MinimumHeightRequest`](xref:Xamarin.Forms.VisualElement.MinimumHeightRequest)

`MinimumHeightRequest` 屬性是一個 `double` 值，可決定當兩個元素爭用有限的空間時，如何處理溢位。 設定 `MinimumHeightRequest` 屬性可讓配置程式將元素向下調整至所要求的最小維度。 如果未指定 `MinimumHeightRequest`，則預設值為-1，而配置程式會將 `HeightRequest` 視為最小值。 這表示沒有 `MinimumHeightRequest` 值的元素將不會有可調整的高度。

如需詳細資訊，請參閱[最小要求屬性](#minimum-request-properties)。

### [`MinimumWidthRequest`](xref:Xamarin.Forms.VisualElement.MinimumWidthRequest)

`MinimumWidthRequest` 屬性是一個 `double` 值，可決定當兩個元素爭用有限的空間時，如何處理溢位。 設定 `MinimumWidthRequest` 屬性可讓配置程式將元素向下調整至所要求的最小維度。 如果未指定 `MinimumWidthRequest`，則預設值為-1，而配置程式會將 `WidthRequest` 視為最小值。 這表示沒有 `MinimumWidthRequest` 值的元素將不會有可調整的寬度。

如需詳細資訊，請參閱[最小要求屬性](#minimum-request-properties)。

### [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity)

`Opacity` 屬性是從零到一個的 `double` 值，會在轉譯期間決定控制項的不透明度。 這個屬性的預設值為1.0。 介於0到1的範圍以外的值將會壓制。 只有在 `true`[`IsVisible`](#isvisible)屬性時，才會套用 `Opacity` 屬性。 不透明度會反復套用。 因此，如果父控制項具有0.5 不透明度，且其子系具有0.5 不透明度，則子系會以有效的0.25 不透明度值呈現。 將輸入控制項的 `Opacity` 屬性設定為0，會有未定義的行為。

### [`Parent`](xref:Xamarin.Forms.Element.Parent)

`Parent` 屬性繼承自 `Element` 類別。 這個屬性是控制項的父系 `Element` 物件。 當專案新增為另一個專案的子系時，通常會自動在元素上設定 `Parent` 屬性。

### [`Resources`](xref:Xamarin.Forms.VisualElement.Resources)

`Resources` 屬性是一個 `ResourceDictionary` 實例，其中填入的索引鍵/值組，通常會在執行時間從 XAML 填入。 此字典可讓應用程式開發人員在編譯時間和執行時間重複使用 XAML 中定義的物件。 字典中的索引鍵是由 XAML 標記的 `x:Key` 屬性所填入。 從 XAML 建立的物件會插入至指定之索引鍵的 `ResourceDictionary`。 一旦初始化之後。

如需詳細資訊，請參閱[資源字典](~/xamarin-forms/xaml/resource-dictionaries.md)。

### [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)

`Rotation` 屬性是介於0到360之間的 `double` 值，會定義 Z 軸的旋轉（以度為單位）。 這個屬性的預設值為 0。 相對於[`AnchorX`](#anchorx)和[`AnchorY`](#anchory)值，會套用旋轉。

### [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX)

`RotationX` 屬性是介於0到360之間的 `double` 值，定義 X 軸的旋轉（以度為單位）。 這個屬性的預設值為 0。 相對於[`AnchorX`](#anchorx)和[`AnchorY`](#anchory)值，會套用旋轉。

### [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)

`RotationY` 屬性是介於0到360之間的 `double` 值，可定義 Y 軸的旋轉（以度為單位）。 這個屬性的預設值為 0。 相對於[`AnchorX`](#anchorx)和[`AnchorY`](#anchory)值，會套用旋轉。

### [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)

`Scale` 屬性是定義控制項尺規的 `double` 值。 這個屬性的預設值為1.0。 調整會相對於[`AnchorX`](#anchorx)和[`AnchorY`](#anchory)值來套用。

### [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX)

`ScaleX` 屬性是定義沿著 X 軸之控制項尺規的 `double` 值。 這個屬性的預設值為1.0。 `ScaleX` 屬性會相對於[`AnchorX`](#anchorx)值套用。

### [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY)

`ScaleY` 屬性是定義沿著 Y 軸之控制項尺規的 `double` 值。 這個屬性的預設值為1.0。 `ScaleY` 屬性會相對於[`AnchorY`](#anchory)值套用。

### [`Style`](xref:Xamarin.Forms.NavigableElement.Style)

`Style` 屬性繼承自 `NavigableElement` 類別。 此屬性是 `Style` 類別的實例。 `Style` 類別包含觸發程式、setter 和行為，可定義視覺專案的外觀和行為。 如需詳細資訊，請參閱[Xamarin. 表單 XAML 樣式](~/xamarin-forms/user-interface/styles/xaml/index.md)。

### [`StyleClass`](xref:Xamarin.Forms.NavigableElement.StyleClass)

`StyleClass` 屬性是代表 `Style` 類別名稱的 `string` 物件清單。 這個屬性繼承自 `NavigableElement` 類別。 `StyleClass` 屬性可讓您將多個樣式屬性套用至 `VisualElement` 實例。 如需詳細資訊，請參閱[Xamarin. 表單樣式類別](~/xamarin-forms/user-interface/styles/xaml/style-class.md)。

### [`TabIndex`](xref:Xamarin.Forms.VisualElement.TabIndex)

`TabIndex` 屬性是 `int` 值，會定義使用 tab 鍵在控制項之間前進時的控制順序。 `TabIndex` 屬性是在 `ITabStopElement` 介面上定義之屬性的實作為，`VisualElement` 類別會執行此操作。

### [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)

`TranslationX` 屬性是 `double` 值，可定義要套用在 X 軸上的差異轉譯。 轉譯會在版面配置之後套用，而且通常用於套用動畫。 將專案轉換為其父容器的界限時，我會避免輸入無法運作。

如需詳細資訊，請參閱[在 Xamarin 中的動畫](~/xamarin-forms/user-interface/animation/index.md)。

### [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)

`TranslationY` 屬性是 `double` 值，可定義要套用在 Y 軸上的差異轉譯。 轉譯會在版面配置之後套用，而且通常用於套用動畫。 將專案轉換為其父容器的界限時，我會避免輸入無法運作。

如需詳細資訊，請參閱[在 Xamarin 中的動畫](~/xamarin-forms/user-interface/animation/index.md)。

### [`Triggers`](xref:Xamarin.Forms.VisualElement.Triggers)

`Triggers` 屬性是 `TriggerBase` 物件的唯讀 `List`。 觸發程式可讓應用程式開發人員在 XAML 中表達動作，以變更控制項的視覺外觀，以回應事件或屬性變更。 如需詳細資訊，請參閱[Xamarin. Forms 觸發](~/xamarin-forms/app-fundamentals/triggers.md)程式。

### [`Visual`](xref:Xamarin.Forms.VisualElement.Visual)

`Visual` 屬性是 `IVisual` 實例，可讓轉譯器建立並選擇性地套用至 `VisualElement` 實例。 `Visual` 屬性會設定為符合其父系，因此在元件上定義轉譯器也會套用至該元件的任何子系。 如果控制項或其祖系上未設定任何自訂轉譯器，則會使用預設的 Xamarin。 如需詳細資訊，請參閱[Xamarin. Forms 視覺效果](~/xamarin-forms/user-interface/visual/index.md)。

### [`Width`](xref:Xamarin.Forms.VisualElement.Width)

`Width` 屬性是唯讀的 `double` 值，可描述控制項的呈現寬度。 `Width` 屬性是在版面配置週期期間計算，而且無法直接設定。 您可以使用[WidthRequest 屬性](#widthrequest)來要求控制項的寬度。

### [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)

`WidthRequest` 屬性是一個 `double` 值，可決定控制項所需的寬度。 控制項的絕對寬度可能不符合要求的值。 如需詳細資訊，請參閱[要求屬性](#request-properties)。

### [`X`](xref:Xamarin.Forms.VisualElement.X)

`X` 屬性是唯讀的 `double` 值，可描述控制項的目前 X 位置。

### [`Y`](xref:Xamarin.Forms.VisualElement.Y)

`Y` 屬性是唯讀的 `double` 值，可描述控制項的目前 Y 位置。

## <a name="methods"></a>方法

下列方法適用于 `VisualElement` 類別。 如需完整清單，請參閱[VISUALELEMENT API 方法](xref:Xamarin.Forms.VisualElement#methods)。

### [`FindByName`](xref:Xamarin.Forms.Element.FindByName*)

`FindByName` 方法是繼承自 `Element` 類別，且具有下列簽章：

```csharp
public object FindByName (string name)
```

這個方法會搜尋所提供 `name` 引數的所有子項目，並傳回具有指定名稱的專案。 如果找不到符合的項目，就會傳回 `null`。

### [`Focus`](xref:Xamarin.Forms.VisualElement.Focus)

`Focus` 方法會嘗試將焦點設定在元素上。 此方法具有下列簽章：

```csharp
public bool Focus ()
```

如果已成功設定鍵盤焦點，`Focus` 方法會傳回 `true`，如果方法呼叫未導致焦點變更，則會 `false`。 元素必須能夠接收焦點，此方法才能正常執行。 在位於或未啟用的專案上呼叫 `Focus` 方法，會有未定義的行為。

### [`Unfocus`](xref:Xamarin.Forms.VisualElement.Unfocus)

`Unfocus` 方法會嘗試移除元素的焦點。 此方法具有下列簽章：

```csharp
public void Unfocus ()
```

專案必須已經有焦點，此方法才能正常執行。

## <a name="events"></a>事件

下列事件適用于 `VisualElement` 類別。 如需完整清單，請參閱[VisualElement 事件](xref:Xamarin.Forms.VisualElement#events)。

### [`Focused`](xref:Xamarin.Forms.VisualElement.Focused)

每當 `VisualElement` 實例收到焦點時，就會引發 `Focused` 事件。 這個事件不會透過 Xamarin. Forms 堆疊來進行，而是直接從原生控制項接收。 這個事件是由[`IsFocused`](#isfocused)屬性 setter 發出。

### [`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged)

每當 `VisualElement` 實例 `Height` 或 `Width` 屬性變更時，就會引發 `SizeChanged` 事件。 如果開發人員想要直接回應大小變更，而不是回應變更後事件，則應該改為執行[`OnSizeAllocated`](xref:Xamarin.Forms.VisualElement.OnSizeAllocated*)虛擬方法。

### [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused)

每當 `VisualElement` 實例失去焦點時，就會引發 `Unfocused` 事件。 這個事件不會透過 Xamarin. Forms 堆疊來進行，而是直接從原生控制項接收。 這個事件是由[`IsFocused`](#isfocused)屬性 setter 發出。

## <a name="units-of-measurement"></a>測量單位

Android、iOS 和 UWP 平臺各有不同的度量單位，可能會因裝置而異。 Xamarin 會使用平臺獨立的測量單位，在裝置和平臺上標準化單位。 在 Xamarin 中，每英寸有160個單位，或每釐米64個單位。

## <a name="request-properties"></a>要求屬性

名稱包含 "request" 的屬性會定義想要的值，這可能不符合實際轉譯的值。 例如，`HeightRequest` 可能設定為150，但如果配置只允許100單位的空間，則控制項的轉譯 `Height` 只會是100。 轉譯的大小會受到可用空間和包含元件的影響。

## <a name="minimum-request-properties"></a>最小要求屬性

最小要求屬性包括 `MinimumHeightRequest` 和 `MinimumWidthRequest`，並可讓您更精確地控制元素如何彼此相對的處理溢位。 不過，與這些屬性相關的版面配置行為有一些重要考慮。

### <a name="unspecified-minimum-property-values"></a>未指定的最小屬性值

如果未設定最小值，則最小屬性的預設值為-1。 版面配置程式會忽略此值，並將絕對值視為最小值。 這個行為的實際結果是，未指定最小值的元素**將不會**壓縮。 指定最小值的元素**將會**縮小。

下列 XAML 會顯示水準 `StackLayout`中的兩個 `BoxView` 元素：

```xaml
<StackLayout Orientation="Horizontal">
    <BoxView HeightRequest="100" BackgroundColor="Purple" WidthRequest="500"></BoxView>
    <BoxView HeightRequest="100" BackgroundColor="Green" WidthRequest="500" MinimumWidthRequest="250"></BoxView>
</StackLayout>
```

第一個 `BoxView` 實例要求的寬度為500，且未指定最小寬度。 第二個 `BoxView` 實例要求的寬度為500，而最小寬度為250。 如果父 `StackLayout` 元素的寬度不足，無法以其要求的寬度同時包含這兩個元件，則配置處理常式會將第一個 `BoxView` 實例視為最小寬度500，因為未指定其他有效的最小值。 第二個 `BoxView` 實例可向下延展至250，它會縮小以符合，直到其寬度達到250單位為止。

如果要讓第一個 `BoxView` 實例的行為在沒有最小寬度的情況下相應減少，則 `MinimumWidthRequest` 必須設定為有效的值，例如0。

### <a name="minimum-and-absolute-property-values"></a>最小和絕對屬性值

當最小值大於絕對值時，不會有未定義的行為。 例如，如果 `MinimumWidthRequest` 設定為100，`WidthRequest` 屬性絕對不應超過100。 指定最小的屬性值時，您應該一律指定絕對值，以確保絕對值大於最小值。

### <a name="minimum-properties-within-a-grid"></a>方格中的最小屬性

`Grid` 配置有自己的系統，可用於資料列和資料行的相對大小調整。 在 `Grid` 配置中使用 `MinimumWidthRequest` 或 `MinimumHeightRequest` 將不會有任何效果。 如需詳細資訊，請參閱[Xamarin. 表單方格](~/xamarin-forms/user-interface/layouts/grid.md)。

## <a name="related-links"></a>相關連結

* [VisualElement API 檔](xref:Xamarin.Forms.VisualElement)
