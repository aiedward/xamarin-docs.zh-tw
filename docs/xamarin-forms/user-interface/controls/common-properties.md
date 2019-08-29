---
title: Xamarin. Forms 通用控制項屬性、方法和事件
description: 本文說明在 VisualElement 類別上定義的通用屬性、方法和事件, 通常用於衍生類別中。
ms.prod: xamarin
ms.assetId: 85A0CCF5-C1D8-40BB-927F-A4D944E5534D
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 08/21/2019
ms.openlocfilehash: 6d10e665c6461655440ddfb2c524cb56a14337f6
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70121352"
---
# <a name="xamarinforms-common-control-properties-methods-and-events"></a>Xamarin. Forms 通用控制項屬性、方法和事件

在 xamarin. forms `VisualElement`應用程式中, xamarin 類別是大部分控制項所使用的基類。 類別會定義在衍生類別中使用的許多[屬性](#properties)、[方法](#methods)和[事件。](#events) `VisualElement`

## <a name="properties"></a>屬性

下列屬性可在實例上`VisualElement`使用。 如需完整清單, 請參閱[VISUALELEMENT API 屬性](xref:Xamarin.Forms.VisualElement#properties)。

### [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX)

屬性是一個`double`值, 定義 X 軸上的中心點以進行轉換, 例如縮放和旋轉。 `AnchorX` 預設值為0.5。

### [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY)

屬性是一個`double`值, 定義 X 軸上的中心點以進行轉換, 例如縮放和旋轉。 `AnchorY` 預設值為0.5。

### [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor)

`BackgroundColor` 屬性`Color`是, 可決定控制項的背景色彩。 如果未設定, 則背景會是預設`Color`物件, 它會呈現為透明。

### [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors)

屬性是物件`Behavior`的`List`。 `Behaviors` 行為可讓您將可重複使用的功能加入至清單, `Behaviors`藉此將它們附加至元素。 如需類別的`Behavior`詳細資訊, 請參閱[Xamarin. 表單行為](~/xamarin-forms/app-fundamentals/behaviors/index.md)。

### [`Bounds`](xref:Xamarin.Forms.VisualElement.Bounds)

屬性是唯讀`Rectangle`物件, 代表控制項所佔用的空間。 `Bounds` `Bounds`屬性值會在版面配置週期期間指派。 `Rectangle` 包含有用的屬性和方法,可用於測試矩形的`struct`交集和內含專案。 如需詳細資訊, 請參閱[Xamarin 矩形 API](xref:Xamarin.Forms.Rectangle)。

### [`Effects`](xref:Xamarin.Forms.Element.Effects)

`Effects`屬性是`Effect`物件的, 繼承自`Element`(x: Xamarin. form. Element) 類別。 `List` 效果可讓您自訂原生控制項, 而且通常用於小型的樣式變更。 如需類別的`Effect`詳細資訊, 請參閱[Xamarin 效果](~/xamarin-forms/app-fundamentals/effects/index.md)。

### [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)

屬性是一個`FlowDirection`列舉值。 `FlowDirection` 流程方向可以設定為`MatchParent`、 `LeftToRight`或`RightToLeft` , 並決定版面配置順序和方向。 `FlowDirection`屬性通常是用來支援從右至左讀取的語言。

### [`Height`](xref:Xamarin.Forms.VisualElement.Height)

屬性是唯讀`double`值, 可描述控制項的呈現高度。 `Height` `Height`屬性會在版面配置週期期間計算, 而且無法直接設定。 您可以使用[HeightRequest 屬性](#heightrequest)來要求控制項的高度。

### [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest)

屬性是一個`double`值, 可決定控制項的所需高度。 `HeightRequest` 控制項的絕對高度可能不符合要求的值。 如需詳細資訊, 請參閱[要求屬性](#request-properties)。

### [`InputTransparent`](xref:Xamarin.Forms.VisualElement.InputTransparent)

`InputTransparent` 屬性`bool`是, 可決定控制項是否會接收使用者輸入。 預設值為, `false`確保元素會收到輸入。 這個屬性會在設定時傳送至子專案。 在版面配置類別`true`上將屬性設定為,會導致版面配置中的所有元素都不會收到輸入。`InputTransparent`

### [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled)

屬性是一個`bool`值, 可決定控制項是否會回應使用者輸入。 `IsEnabled` 預設值為 `true`。 將此屬性設定為 false, 將會讓控制項無法接受使用者輸入。

### [`IsFocused`](xref:Xamarin.Forms.VisualElement.IsFocused)

屬性是描述控制項目前是否為焦點物件的值。`bool` `IsFocused` 在控制項上呼叫`IsFocused` [方法會導致將值設定為true。`Focus`](#focus) [`Unfocus`](#unfocus)呼叫方法會將此屬性設定為 false。

### [`IsTabStop`](xref:Xamarin.Forms.VisualElement.IsTabStop)

屬性 (property) `bool`是一個值, 定義當使用者使用 tab 鍵向前流覽控制項時, 控制項是否會收到焦點。 `IsTabStop` 如果此屬性為 false, `TabIndex`屬性將不會有任何作用。

### [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible)

屬性是決定是否呈現控制項的值。`bool` `IsVisible` 屬性設為`IsVisible` false 的控制項將不會顯示, 在版面配置週期期間, 將不會考慮空間的計算, 也無法接受使用者輸入。

### [`MinimumHeightRequest`](xref:Xamarin.Forms.VisualElement.MinimumHeightRequest)

屬性是一個`double`值, 可決定當兩個元素爭用有限的空間時, 如何處理溢位。 `MinimumHeightRequest` `MinimumHeightRequest`設定屬性可讓配置程式將元素向下調整至所要求的最小維度。 如果未指定, 則預設值為-1, 而配置處理常式會將`HeightRequest`視為最小值。 `MinimumHeightRequest` 這表示沒有`MinimumHeightRequest`值的元素不會有可調整的高度。

如需詳細資訊, 請參閱[最小要求屬性](#minimum-request-properties)。

### [`MinimumWidthRequest`](xref:Xamarin.Forms.VisualElement.MinimumWidthRequest)

屬性是一個`double`值, 可決定當兩個元素爭用有限的空間時, 如何處理溢位。 `MinimumWidthRequest` `MinimumWidthRequest`設定屬性可讓配置程式將元素向下調整至所要求的最小維度。 如果未指定, 則預設值為-1, 而配置處理常式會將`WidthRequest`視為最小值。 `MinimumWidthRequest` 這表示沒有`MinimumWidthRequest`值的專案將不會有可調整的寬度。

如需詳細資訊, 請參閱[最小要求屬性](#minimum-request-properties)。

### [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity)

屬性是從零到一個的值,可在轉譯期間決定控制項的不透明度。`double` `Opacity` 這個屬性的預設值為1.0。 介於0到1的範圍以外的值將會壓制。 只有`Opacity`在[`IsVisible`](#isvisible)屬性為`true`時, 才會套用屬性。 不透明度會反復套用。 因此, 如果父控制項具有0.5 不透明度, 且其子系具有0.5 不透明度, 則子系會以有效的0.25 不透明度值呈現。 將輸入控制項的屬性設定為0時,有未定義的行為。`Opacity`

### [`Parent`](xref:Xamarin.Forms.Element.Parent)

`Parent` 屬性繼承自 `Element` 類別。 這個屬性是`Element`控制項父系的物件。 `Parent`屬性通常會在專案新增為另一個專案的子系時自動設定。

### [`Resources`](xref:Xamarin.Forms.VisualElement.Resources)

屬性是一個`ResourceDictionary`實例, 其中填入的索引鍵/值組通常會在執行時間從 XAML 填入。 `Resources` 此字典可讓應用程式開發人員在編譯時間和執行時間重複使用 XAML 中定義的物件。 字典中的索引鍵會從`x:Key` XAML 標記的屬性填入。 從 XAML 建立的物件會插入至指定`ResourceDictionary`之索引鍵的。 一旦初始化之後。

如需詳細資訊, 請參閱[資源字典](~/xamarin-forms/xaml/resource-dictionaries.md)。

### [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)

屬性是介於0到360之間的值,可定義Z軸的旋轉(以度為單位)。`double` `Rotation` 這個屬性的預設值為0。 旋轉會相對於[`AnchorX`](#anchorx)和[`AnchorY`](#anchory)值來套用。

### [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX)

屬性是介於0到360之間的值,可定義X軸的旋轉(以度為單位)。`double` `RotationX` 這個屬性的預設值為0。 旋轉會相對於[`AnchorX`](#anchorx)和[`AnchorY`](#anchory)值來套用。

### [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)

屬性是介於0到360之間的值,可定義Y軸的旋轉(以度為單位)。`double` `RotationY` 這個屬性的預設值為0。 旋轉會相對於[`AnchorX`](#anchorx)和[`AnchorY`](#anchory)值來套用。

### [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)

屬性是定義控制項小數值的值。`double` `Scale` 這個屬性的預設值為1.0。 調整會相對於[`AnchorX`](#anchorx)和[`AnchorY`](#anchory)值來套用。

### [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX)

屬性是定義沿著 X 軸之控制項尺規的值。`double` `ScaleX` 這個屬性的預設值為1.0。 屬性會相對於[`AnchorX`](#anchorx)值來套用。 `ScaleX`

### [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY)

屬性是一個`double`值, 會定義沿著 Y 軸的控制項尺規。 `ScaleY` 這個屬性的預設值為1.0。 屬性會相對於[`AnchorY`](#anchory)值來套用。 `ScaleY`

### [`Style`](xref:Xamarin.Forms.NavigableElement.Style)

`Style` 屬性繼承自 `NavigableElement` 類別。 這個屬性是`Style`類別的實例。 `Style`類別包含觸發程式、setter 和行為, 可定義視覺專案的外觀和行為。 如需詳細資訊, 請參閱[Xamarin. 表單 XAML 樣式](~/xamarin-forms/user-interface/styles/xaml/index.md)。

### [`StyleClass`](xref:Xamarin.Forms.NavigableElement.StyleClass)

屬性是代表`Style`類別名稱的`string`物件清單。 `StyleClass` 這個屬性繼承自 `NavigableElement` 類別。 屬性可讓您將多個樣式屬性套用`VisualElement`至實例。 `StyleClass` 如需詳細資訊, 請參閱[Xamarin. 表單樣式類別](~/xamarin-forms/user-interface/styles/xaml/style-class.md)。

### [`TabIndex`](xref:Xamarin.Forms.VisualElement.TabIndex)

屬性是一個`int`值, 會定義使用 tab 鍵在控制項之間前進時的控制順序。 `TabIndex` 屬性 (property) 是在`ITabStopElement`介面上定義的屬性 (property) 的`VisualElement`實作為 (class)。 `TabIndex`

### [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)

屬性是一個`double`值, 可定義要在 X 軸上套用的差異轉譯。 `TranslationX` 轉譯會在版面配置之後套用, 而且通常用於套用動畫。 將專案轉換為其父容器的界限時, 我會避免輸入無法運作。

如需詳細資訊, 請參閱[在 Xamarin 中的動畫](~/xamarin-forms/user-interface/animation/index.md)。

### [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)

屬性是一個`double`值, 可定義要套用在 Y 軸上的差異轉譯。 `TranslationY` 轉譯會在版面配置之後套用, 而且通常用於套用動畫。 將專案轉換為其父容器的界限時, 我會避免輸入無法運作。

如需詳細資訊, 請參閱[在 Xamarin 中的動畫](~/xamarin-forms/user-interface/animation/index.md)。

### [`Triggers`](xref:Xamarin.Forms.VisualElement.Triggers)

屬性是`TriggerBase`物件的唯讀`List`。 `Triggers` 觸發程式可讓應用程式開發人員在 XAML 中表達動作, 以變更控制項的視覺外觀, 以回應事件或屬性變更。 如需詳細資訊, 請參閱[Xamarin. Forms 觸發](~/xamarin-forms/app-fundamentals/triggers.md)程式。

### [`Visual`](xref:Xamarin.Forms.VisualElement.Visual)

屬性是一個`IVisual`實例, 可讓轉譯器建立並選擇性地套用至`VisualElement`實例。 `Visual` `Visual`屬性會設定為符合其父系, 因此在元件上定義轉譯器也會套用至該元件的任何子系。 如果控制項或其祖系上未設定任何自訂轉譯器, 則會使用預設的 Xamarin。 如需詳細資訊, 請參閱[Xamarin. Forms 視覺效果](~/xamarin-forms/user-interface/visual/index.md)。

### [`Width`](xref:Xamarin.Forms.VisualElement.Width)

屬性是描述控制項呈現寬度的`double`唯讀值。 `Width` `Width`屬性會在版面配置週期期間計算, 而且無法直接設定。 您可以使用[WidthRequest 屬性](#widthrequest)來要求控制項的寬度。

### [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)

屬性是一個`double`值, 可決定控制項的所需寬度。 `WidthRequest` 控制項的絕對寬度可能不符合要求的值。 如需詳細資訊, 請參閱[要求屬性](#request-properties)。

### [`X`](xref:Xamarin.Forms.VisualElement.X)

屬性是唯讀`double`值, 可描述控制項的目前 X 位置。 `X`

### [`Y`](xref:Xamarin.Forms.VisualElement.Y)

屬性是唯讀`double`值, 可描述控制項的目前 Y 位置。 `Y`

## <a name="methods"></a>方法

下列方法可在`VisualElement`類別上取得。 如需完整清單, 請參閱[VISUALELEMENT API 方法](xref:Xamarin.Forms.VisualElement#methods)。

### [`FindByName`](xref:Xamarin.Forms.Element.FindByName*)

方法是繼承`Element`自類別, 而且具有下列簽章: `FindByName`

```csharp
public object FindByName (string name)
```

這個方法會搜尋所提供`name`引數的所有子項目, 並傳回具有指定名稱的專案。 如果找不到相符的`null` , 則會傳回。

### [`Focus`](xref:Xamarin.Forms.VisualElement.Focus)

`Focus`方法會嘗試將焦點設定在元素上。 此方法具有下列簽章：

```csharp
public bool Focus ()
```

如果已成功設定鍵盤焦點, `Focus` `false`方法會傳回, 如果方法呼叫未導致焦點變更, 則傳回。 `true` 元素必須能夠接收焦點, 此方法才能正常執行。 在不是或未實現的專案上呼叫方法,會有未定義的行為。`Focus`

### [`Unfocus`](xref:Xamarin.Forms.VisualElement.Unfocus)

`Unfocus`方法會嘗試移除元素的焦點。 此方法具有下列簽章：

```csharp
public void Unfocus ()
```

專案必須已經有焦點, 此方法才能正常執行。

## <a name="events"></a>事件

下列事件可在`VisualElement`類別上取得。 如需完整清單, 請參閱[VisualElement 事件](xref:Xamarin.Forms.VisualElement#events)。

### [`Focused`](xref:Xamarin.Forms.VisualElement.Focused)

每當實例收到焦點時, `Focused`就會引發事件。 `VisualElement` 這個事件不會透過 Xamarin. Forms 堆疊來進行, 而是直接從原生控制項接收。 這個事件是由[`IsFocused`](#isfocused)屬性 setter 發出。

### [`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged)

每當`SizeChanged`實例或`Width`屬性變更時, 就會引發事件。 `Height` `VisualElement` 如果開發人員想要直接回應大小變更, 而不是回應變更後事件, 則應該改為執行[`OnSizeAllocated`](xref:Xamarin.Forms.VisualElement.OnSizeAllocated*)虛擬方法。

### [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused)

每當實例失去焦點時, `Unfocused`就會引發事件。 `VisualElement` 這個事件不會透過 Xamarin. Forms 堆疊來進行, 而是直接從原生控制項接收。 這個事件是由[`IsFocused`](#isfocused)屬性 setter 發出。

## <a name="units-of-measurement"></a>測量單位

Android、iOS 和 UWP 平臺各有不同的度量單位, 可能會因裝置而異。 Xamarin 會使用平臺獨立的測量單位, 在裝置和平臺上標準化單位。 在 Xamarin 中, 每英寸有160個單位, 或每釐米64個單位。

## <a name="request-properties"></a>要求屬性

名稱包含 "request" 的屬性會定義想要的值, 這可能不符合實際轉譯的值。 例如, `HeightRequest`可能會設定為 150, 但如果配置只允許100單位的空間, 則控制項的`Height`呈現只會是100。 轉譯的大小會受到可用空間和包含元件的影響。

## <a name="minimum-request-properties"></a>最小要求屬性

要求的最小`MinimumHeightRequest`值`MinimumWidthRequest`屬性包括和, 並可讓您更精確地控制元素如何彼此相對的處理溢位。 不過, 與這些屬性相關的版面配置行為有一些重要考慮。

### <a name="unspecified-minimum-property-values"></a>未指定的最小屬性值

如果未設定最小值, 則最小屬性的預設值為-1。 版面配置程式會忽略此值, 並將絕對值視為最小值。 這個行為的實際結果是, 未指定最小值的元素**將不會**壓縮。 指定最小值的元素**將會**縮小。

下列 XAML 會以水準`BoxView` `StackLayout`方式顯示兩個元素:

```xaml
<StackLayout Orientation="Horizontal">
    <BoxView HeightRequest="100" BackgroundColor="Purple" WidthRequest="500"></BoxView>
    <BoxView HeightRequest="100" BackgroundColor="Green" WidthRequest="500" MinimumWidthRequest="250"></BoxView>
</StackLayout>
```

第一個`BoxView`實例要求的寬度為 500, 且未指定最小寬度。 第二`BoxView`個實例要求的寬度為 500, 而最小寬度為250。 如果父`StackLayout`元素的寬度不夠寬, 而無法在其要求的寬度同時包含這兩`BoxView`個元件, 則配置處理常式會將第一個實例視為最小寬度 500, 因為未指定其他有效的最小值。 第二`BoxView`個實例可相應減少到 250, 而且會縮小以符合, 直到其寬度達到250單位為止。

如果所要的行為是讓第`BoxView`一個實例在沒有最小寬度的情況下`MinimumWidthRequest`相應減少, 則必須設定為有效的值, 例如0。

### <a name="minimum-and-absolute-property-values"></a>最小和絕對屬性值

當最小值大於絕對值時, 不會有未定義的行為。 例如, 如果`MinimumWidthRequest`設定為 100 `WidthRequest` , 屬性絕對不應超過100。 指定最小的屬性值時, 您應該一律指定絕對值, 以確保絕對值大於最小值。

### <a name="minimum-properties-within-a-grid"></a>方格中的最小屬性

`Grid`版面配置有自己的系統, 可用於資料列和資料行的相對大小調整。 在`MinimumWidthRequest` 版面`Grid`配置中使用或`MinimumHeightRequest`不會有效果。 如需詳細資訊, 請參閱[Xamarin. 表單方格](~/xamarin-forms/user-interface/layouts/grid.md)。

## <a name="related-links"></a>相關連結

* [VisualElement API 檔](xref:Xamarin.Forms.VisualElement)
