---
title: Xamarin 中的程式設計配置條件約束
description: '本指南說明如何在 c # 程式碼中使用 iOS 自動設定條件約束，而不是在 iOS 設計工具中建立它們。'
ms.prod: xamarin
ms.assetid: 119C8365-B470-4CD4-85F7-086F0A46DCBB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 3dd413d6d747c9bbec43e5a88f7e24b8e7868327
ms.sourcegitcommit: 4bbf54d2bc1df96af69814e2e5dae47be12e0474
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102602901"
---
# <a name="programmatic-layout-constraints-in-xamarinios"></a>Xamarin 中的程式設計配置條件約束

_本指南說明如何在 c # 程式碼中使用 iOS 自動設定條件約束，而不是在 iOS 設計工具中建立它們。_

自動版面配置 (也稱為「彈性配置」 ) 是回應式設計方法。 不同于轉換版面配置系統，其中每個專案的位置都已硬式編碼為螢幕上的某個點，而 [自動設定] 則是 *關聯* 性，也就是專案相對於設計介面上其他元素的位置。 「自動設定」的核心是一種條件約束或規則的概念，這些條件約束或規則會在螢幕上的其他元素內容中定義專案或專案集的位置。 因為元素未系結至畫面上的特定位置，條件約束有助於建立可在不同螢幕大小和裝置方向上美觀的彈性版面配置。

一般來說，當您在 iOS 中使用自動版面配置時，您將使用 Xcode 的介面產生器，以圖形方式將配置條件約束放置在 UI 專案上。 不過，有時候您可能需要在 c # 程式碼中建立並套用條件約束。 例如，當您使用已加入至的動態建立 UI 元素時 `UIView` 。

本指南將說明如何使用 c # 程式碼建立和使用條件約束，而不是在 Xcode 的 Interface Builder 中以圖形方式建立它們。

<a name="Creating-Constraints-Programmatically"></a>

## <a name="creating-constraints-programmatically"></a>以程式設計方式建立條件約束

如上所述，您通常會在 iOS 設計工具中使用自動設定條件約束。 針對您需要以程式設計方式建立條件約束的時間，您有三個選項可供選擇：

- [版面配置錨點](#Layout-Anchors) -此 API 可讓您存取錨點屬性 (例如 `TopAnchor` ， `BottomAnchor` 或 `HeightAnchor` 受限制的 UI 專案) 。
- [版面配置條件約束](#Layout-Constraints) -您可以直接使用類別來建立條件約束 `NSLayoutConstraint` 。
- [視覺化格式語言](#Visual-Format-Language) -提供類似 ASCII 的方法來定義條件約束。

下列各節將詳細說明每個選項。

<a name="Layout-Anchors"></a>

### <a name="layout-anchors"></a>版面配置錨點

藉由使用 `NSLayoutAnchor` 類別，您會有一個流暢的介面，可根據所限制的 UI 專案錨點屬性來建立條件約束。 例如，視圖控制器的頂端和底端版面配置輔助線會在 `TopAnchor` `BottomAnchor` `HeightAnchor` 視圖公開 edge、center、size 和基準屬性時公開、和錨點屬性。

> [!IMPORTANT]
> 除了一組標準錨點屬性之外，iOS Views 也包含 `LayoutMarginsGuides` 和 `ReadableContentGuide` 屬性。 這些屬性會公開 `UILayoutGuide` 物件，以分別使用 View 的邊界和可讀取的內容指南。

版面配置錨點提供數種方法，可讓您以易於閱讀的精簡格式建立條件約束：

- **ConstraintEqualTo** -定義 `first attribute = second attribute + [constant]` 具有選擇性提供位移值的關聯性 `constant` 。
- **ConstraintGreaterThanOrEqualTo** -定義 `first attribute >= second attribute + [constant]` 具有選擇性提供位移值的關聯性 `constant` 。
- **ConstraintLessThanOrEqualTo** -定義 `first attribute <= second attribute + [constant]` 具有選擇性提供位移值的關聯性 `constant` 。

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

一般的版面配置條件約束可以簡單表示為線性運算式。 以下列範例為例：

[![以線性運算式表示的版面配置條件約束](programmatic-layout-constraints-images/graph01.png)](programmatic-layout-constraints-images/graph01.png#lightbox)

這會使用版面配置錨點轉換成下列的 c # 程式程式碼：

```csharp
PurpleView.LeadingAnchor.ConstraintEqualTo (OrangeView.TrailingAnchor, 10).Active = true; 
```

C # 程式碼的各個部分會對應到方程式的指定部分，如下所示：

|方程|程式碼|
|---|---|
|項目 1|PurpleView|
|屬性1|LeadingAnchor|
|關聯性|ConstraintEqualTo|
|乘數|預設為1.0，因此未指定|
|項目 2|OrangeView|
|屬性2|TrailingAnchor|
|常數|10.0|

除了提供解決指定的版面配置條件約束方程式所需的參數之外，每個版面配置錨點方法都會強制執行傳遞給它們的參數型別安全。 因此，水準條件約束錨點（例如或）只能搭配 `LeadingAnchor` `TrailingAnchor` 其他水準錨點類型使用，而乘數只會提供給大小條件約束。

<a name="Layout-Constraints"></a>

### <a name="layout-constraints"></a>版面配置條件約束

您可以直接 `NSLayoutConstraint` 在 c # 程式碼中建立，以手動方式新增自動設定條件約束。 不同于使用版面配置錨點，您必須指定每個參數的值，即使它對所定義的條件約束不會有任何影響。 因此，您最後會產生相當大量的難以閱讀的程式碼。 例如：

```csharp
//// Pin the leading edge of the view to the margin
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Leading, NSLayoutRelation.Equal, View, NSLayoutAttribute.LeadingMargin, 1.0f, 0.0f).Active = true;

//// Pin the trailing edge of the view to the margin
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Trailing, NSLayoutRelation.Equal, View, NSLayoutAttribute.TrailingMargin, 1.0f, 0.0f).Active = true;

//// Give the view a 1:2 aspect ratio
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Height, NSLayoutRelation.Equal, OrangeView, NSLayoutAttribute.Width, 2.0f, 0.0f).Active = true;
```

`NSLayoutAttribute`列舉會定義視圖邊界的值，並且對應至 `LayoutMarginsGuide` 屬性（例如、和）， `Left` `Right` `Top` `Bottom` 而 `NSLayoutRelation` 列舉會定義在指定的屬性為、或時建立的關聯性 `Equal` `LessThanOrEqual` `GreaterThanOrEqual` 。

不同于版面配置錨點 API， `NSLayoutConstraint` 建立方法不會反白顯示特定條件約束的重要層面，也不會對條件約束執行編譯時間檢查。 如此一來，就能輕鬆地建立在執行時間擲回例外狀況的無效條件約束。

<a name="Visual-Format-Language"></a>

### <a name="visual-format-language"></a>視覺化格式語言

視覺化格式語言可讓您使用 ASCII 藝術（例如字串）來定義條件約束，以提供所要建立之條件約束的視覺標記法。 這有下列優點和缺點：

- 視覺化格式語言只會強制建立有效的條件約束。
- 使用視覺化格式語言將自動設定輸出至主控台的條件約束，讓偵錯工具訊息類似于用來建立條件約束的程式碼。
- 視覺化格式語言可讓您以非常精簡的運算式同時建立多個條件約束。
- 由於沒有任何編譯端驗證的視覺格式語言字串，因此只能在執行時間探索問題。
- 因為視覺效果格式語言強調視覺效果，所以無法使用它來建立某些條件約束類型 (例如比例) 。

使用視覺化格式語言建立條件約束時，您需要執行下列步驟：

1. 建立 `NSDictionary` ，其中包含 View 物件和版面配置輔助線，以及定義格式時將使用的字串索引鍵。
2. 您可以選擇性地建立 `NSDictionary` ，以定義一組索引鍵和值 (`NSNumber`) 當做條件約束的常數值使用。
3. 建立用來配置單一資料行或專案列的格式字串。
4. 呼叫 `FromVisualFormat` 類別的方法 `NSLayoutConstraint` ，以產生條件約束。
5. 呼叫 `ActivateConstraints` 類別的方法， `NSLayoutConstraint` 以啟動並套用條件約束。

例如，若要以視覺化格式語言建立前置和尾端條件約束，您可以使用下列程式：

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

由於視覺格式語言在使用預設間距時，一律會建立附加至父視圖邊界的零點條件約束，因此此程式碼會產生與上述範例相同的結果。

針對較複雜的 UI 設計，例如單一線條上的多個子視圖，視覺效果格式語言會指定水準間距和垂直對齊。 如同上述範例所示，它會指定將資料 `AlignAllTop` `NSLayoutFormatOptions` 列或資料行中的所有視圖對齊其頂端。

請參閱 Apple 的 [視覺格式語言附錄](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1) ，以取得一些指定通用條件約束和視覺化格式字串文法的範例。

<a name="Summary"></a>

## <a name="summary"></a>摘要

本指南顯示如何在 c # 中建立及使用自動設定條件約束，而不是在 iOS 設計工具中以圖形方式建立。 首先，它會探討如何使用版面配置錨點 (`NSLayoutAnchor`) 來處理自動版面配置。 接下來，它會示範如何使用 () 的版面配置條件約束 `NSLayoutConstraint` 。 最後，它會使用自動版面配置的視覺化格式語言來呈現。

## <a name="related-links"></a>相關連結

- [Storyboard 簡介](~/ios/user-interface/storyboards/index.md)
- [iOS 設計的控制項逐步解說](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [使用適用于 iOS 的 Xamarin 設計工具進行自動版面配置](~/ios/user-interface/designer/designer-auto-layout.md#modifying-in-code)
- [Apple-以程式設計方式建立條件約束](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html#//apple_ref/doc/uid/TP40010853-CH16-SW1)
- [Apple-視覺化格式語言附錄](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1)
