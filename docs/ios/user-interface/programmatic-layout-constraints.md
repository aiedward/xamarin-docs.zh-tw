---
title: 在 Xamarin 中的程式設計版面配置條件約束
description: 本指南說明如何在程式碼中C#使用 Ios 自動版面配置條件約束, 而不是在 ios 設計工具中建立它們。
ms.prod: xamarin
ms.assetid: 119C8365-B470-4CD4-85F7-086F0A46DCBB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: c26e064a32762dcb1d088e614830a7a9632f9b1b
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70120730"
---
# <a name="programmatic-layout-constraints-in-xamarinios"></a>在 Xamarin 中的程式設計版面配置條件約束

_本指南說明如何在程式碼中C#使用 Ios 自動版面配置條件約束, 而不是在 ios 設計工具中建立它們。_

自動設定 (也稱為「調適型版面配置」) 是一種回應式設計方法。 不同于過渡配置系統, 其中每個專案的位置都是硬式編碼至螢幕上的某個點, 自動設定是關於*關聯*性-專案相對於設計介面上其他元素的位置。 自動設定的核心是條件約束或規則的概念, 其定義了在螢幕上其他元素內容中的專案或專案集合的位置。 由於元素不會系結至螢幕上的特定位置, 因此條件約束有助於建立可在不同螢幕大小和裝置方向上看起來良好的調適型版面配置。

一般來說, 在 iOS 中使用自動版面配置時, 您將使用 iOS 設計工具, 以圖形方式將配置條件約束放在 UI 專案上。 不過, 有時候您可能需要在程式碼中C#建立和套用條件約束。 例如, 當您使用已新增至的`UIView`動態建立 UI 元素時。

本指南將說明如何使用C#程式碼建立及使用條件約束, 而不是在 iOS 設計工具中以圖形方式建立。

<a name="Creating-Constraints-Programmatically" />

## <a name="creating-constraints-programmatically"></a>以程式設計方式建立條件約束

如上所述, 通常您會在 iOS 設計工具中使用自動版面配置條件約束。 針對您必須以程式設計方式建立條件約束的時間, 您有三個選項可供選擇:

- [版面配置錨點](#Layout-Anchors): 此 API 可讓您存取受條件約束`TopAnchor`之`BottomAnchor` UI `HeightAnchor`專案的錨點屬性 (例如或)。
- [版面配置條件約束](#Layout-Constraints): 您可以直接使用`NSLayoutConstraint`類別建立條件約束。
- [視覺格式設定語言](#Visual-Format-Language)-提供類似 ASCII 的藝術方法, 以定義您的條件約束。

下列各節將詳細說明每個選項。

<a name="Layout-Anchors" />

### <a name="layout-anchors"></a>版面配置錨點

藉由使用`NSLayoutAnchor`類別, 您就有一個流暢的介面, 可根據所限制的 UI 專案錨點屬性來建立條件約束。 例如, 視圖控制器的頂端和底端版面配置輔助線會公開`TopAnchor`、 `BottomAnchor`和`HeightAnchor`錨點屬性, 而 view 會公開 edge、center、size 和基準屬性。

> [!IMPORTANT]
> 除了一組標準的錨點屬性, iOS Views 也包括`LayoutMarginsGuides`和`ReadableContentGuide`屬性。 這些屬性會`UILayoutGuide`公開物件, 以便分別使用此視圖的邊界和可讀取的內容指南。

版面配置錨點提供數種方法, 可讓您以易於閱讀、精簡的格式建立條件約束:

- **ConstraintEqualTo** -定義一個關聯性`first attribute = second attribute + [constant]` , 其中具有選擇性`constant`提供的位移值。
- **ConstraintGreaterThanOrEqualTo** -定義一個關聯性`first attribute >= second attribute + [constant]` , 其中具有選擇性`constant`提供的位移值。
- **ConstraintLessThanOrEqualTo** -定義一個關聯性`first attribute <= second attribute + [constant]` , 其中具有選擇性`constant`提供的位移值。

例如：

```csharp
// Get the parent view's layout
var margins = View.LayoutMarginsGuide;

// Pin the leading edge of the view to the margin
OrangeView.LeadingAnchor.ConstraintEqualTo (margins.LeadingAnchor).Active = true;

// Pin the trailing edge of the view to the margin
OrangeView.TrailingAnchor.ConstraintEqualTo (margins.TrailingAnchor).Active = true;

// Give the view a 1:2 aspect ratio
OrangeView.HeightAnchor.ConstraintEqualTo (OrangeView.WidthAnchor, 2.0f);
```

一般的版面配置條件約束可以表示為線性運算式。 請使用以下範例：

[![](programmatic-layout-constraints-images/graph01.png "以線性運算式表示的版面配置條件約束")](programmatic-layout-constraints-images/graph01.png#lightbox)

這會使用版面配置錨點轉換成C#下列程式程式碼:

```csharp
PurpleView.LeadingAnchor.ConstraintEqualTo (OrangeView.TrailingAnchor, 10).Active = true; 
```

其中的程式C#代碼部分會對應至方程式的給定部分, 如下所示:

|方程式|程式碼|
|---|---|
|專案1|PurpleView|
|屬性1|LeadingAnchor|
|Relationship|ConstraintEqualTo|
|乘數|預設為 1.0, 因此未指定|
|專案2|OrangeView|
|屬性2|TrailingAnchor|
|常數|10.0|

除了只提供解決指定的版面配置條件約束方程式所需的參數之外, 每個版面配置錨點方法都會強制執行傳遞給它們的參數型別安全。 因此水準條件約束錨點`LeadingAnchor` ( `TrailingAnchor`例如或) 只能搭配其他水準錨點類型使用, 而乘數則只會提供給大小條件約束。

<a name="Layout-Constraints" />

### <a name="layout-constraints"></a>版面配置條件約束

您可以直接`NSLayoutConstraint`在程式碼中建立自動版面配置條件C#約束, 藉以手動新增。 不同于使用版面配置錨點, 您必須指定每個參數的值, 即使它不會影響所定義的條件約束。 因此, 您最終會產生相當大的難以閱讀的程式碼。 例如：

```csharp
//// Pin the leading edge of the view to the margin
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Leading, NSLayoutRelation.Equal, View, NSLayoutAttribute.LeadingMargin, 1.0f, 0.0f).Active = true;

//// Pin the trailing edge of the view to the margin
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Trailing, NSLayoutRelation.Equal, View, NSLayoutAttribute.TrailingMargin, 1.0f, 0.0f).Active = true;

//// Give the view a 1:2 aspect ratio
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Height, NSLayoutRelation.Equal, OrangeView, NSLayoutAttribute.Width, 2.0f, 0.0f).Active = true;
```

`Right` `NSLayoutRelation` `LayoutMarginsGuide` `Top` `Bottom`其中, 列舉會定義視圖邊界的值, 並對應至屬性 (例如、和), 而列舉會定義關聯性。 `NSLayoutAttribute` `Left`將在指定的屬性之間建立為`Equal`、 `LessThanOrEqual`或`GreaterThanOrEqual`。

不同于版面配置錨點 API, `NSLayoutConstraint`建立方法不會反白顯示特定條件約束的重要層面, 也不會在條件約束上執行編譯時間檢查。 因此, 很容易就能在執行時間中, 建立會擲回例外狀況的無效條件約束。

<a name="Visual-Format-Language" />

### <a name="visual-format-language"></a>視覺格式語言

視覺化格式語言可讓您使用 ASCII 美工圖案 (例如字串) 來定義條件約束, 以提供所建立之條件約束的視覺標記法。 這有下列優點和缺點:

- 視覺化格式語言只會強制建立有效的條件約束。
- 自動設定: 使用視覺化格式語言將條件約束輸出到主控台, 讓偵錯工具訊息與用來建立條件約束的程式碼類似。
- 視覺化格式語言可讓您使用非常精簡的運算式同時建立多個條件約束。
- 由於視覺格式語言字串沒有任何編譯端驗證, 因此只能在執行時間探索到問題。
- 由於視覺格式語言會強調視覺化的完整性, 因此無法使用它來建立條件約束類型 (例如比例)。

使用視覺化格式語言建立條件約束時, 您會採取下列步驟:

1. `NSDictionary`建立, 其中包含 View 物件和版面配置輔助線, 以及將在定義格式時使用的字串索引鍵。
2. 選擇性地建立`NSDictionary` , 其會定義一組用來當做`NSNumber`條件約束常數值的索引鍵和值 ()。
3. 建立格式字串, 以配置單一資料行或專案的資料列。
4. 呼叫類別`NSLayoutConstraint`的方法,以`FromVisualFormat`產生條件約束。
5. 呼叫類別`NSLayoutConstraint`的方法,以啟動並`ActivateConstraints`套用條件約束。

例如, 若要以視覺化格式語言建立前置和尾端條件約束, 您可以使用下列方式:

```csharp
// Get views being constrained
var views = new NSMutableDictionary (); 
views.Add (new NSString ("orangeView"), OrangeView);

// Define format and assemble constraints
var format = "|-[orangeView]-|";
var constraints = NSLayoutConstraint.FromVisualFormat (format, NSLayoutFormatOptions.AlignAllTop, null, views);

// Apply constraints
NSLayoutConstraint.ActivateConstraints (constraints);
```

由於在使用預設間距時, 視覺化格式語言一律會建立附加至父視圖邊界的零點條件約束, 因此, 此程式碼會產生與上述範例相同的結果。

針對更複雜的 UI 設計 (例如單一行上的多個子視圖), 視覺格式語言會同時指定水準間距和垂直對齊。 如同上述範例所指定, 會將`AlignAllTop` `NSLayoutFormatOptions`資料列或資料行中的所有視圖對齊其頂端。

如需指定通用條件約束和視覺化格式字串文法的一些範例, 請參閱 Apple 的[視覺化格式語言附錄](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1)。

<a name="Summary" />

## <a name="summary"></a>總結

本指南顯示如何在中C#建立和使用自動設定條件約束, 而不是在 iOS 設計工具中以圖形方式建立。 首先, 它探討使用版面配置錨點`NSLayoutAnchor`() 來處理自動版面配置。 接下來, 它會示範如何使用版面配置條件`NSLayoutConstraint`約束 ()。 最後, 它會使用自動版面配置的視覺化格式語言來呈現。

## <a name="related-links"></a>相關連結

- [Storyboard 簡介](~/ios/user-interface/storyboards/index.md)
- [iOS 設計的控制項逐步解說](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [使用 Xamarin Designer for iOS 的自動版面配置](~/ios/user-interface/designer/designer-auto-layout.md#modifying-in-code)
- [Apple-以程式設計方式建立條件約束](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html#//apple_ref/doc/uid/TP40010853-CH16-SW1)
- [Apple-視覺效果格式語言附錄](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1)
