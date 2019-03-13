---
title: 在 Xamarin.iOS 中的程式設計版面配置條件約束
description: 本指南提供有關使用 iOS 中的自動版面配置條件約束C#而不是在 iOS 設計工具中建立它們的程式碼。
ms.prod: xamarin
ms.assetid: 119C8365-B470-4CD4-85F7-086F0A46DCBB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 3d8e69af7f790415343abf464ea2bb22e879e025
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106957"
---
# <a name="programmatic-layout-constraints-in-xamarinios"></a>在 Xamarin.iOS 中的程式設計版面配置條件約束

_本指南提供有關使用 iOS 中的自動版面配置條件約束C#而不是在 iOS 設計工具中建立它們的程式碼。_

自動版面配置 （也稱為 「 自動調整的版面配置 」） 是回應式設計的方法。 不同於過渡期的版面配置系統，其中每個元素的位置是硬式編碼為畫面上的點，自動版面配置是關於*關聯性*-項目相對於其他元素的設計介面上的位置。 自動版面配置的核心是條件約束或規則，在螢幕上的其他項目內容中定義位置的項目或項目集的概念。 項目不會繫結到螢幕上的特定位置，因為條件約束來協助建立彈性的配置，看起來沒問題，在不同螢幕大小和裝置方向。

通常在 iOS 中使用自動版面配置，當您將使用 iOS 設計工具來以圖形方式放置在您的 UI 項目上的版面配置條件約束。 不過，有可能是當您需要建立和套用中的條件約束C#程式碼。 比方說，當使用以動態方式建立 UI 項目新增至`UIView`。

本指南將示範如何建立和使用條件約束使用C#而不是在 iOS 設計工具中以圖形方式建立的程式碼。

<a name="Creating-Constraints-Programmatically" />

## <a name="creating-constraints-programmatically"></a>以程式設計方式建立條件約束

如上所述，通常您會使用自動版面配置條件約束使用 iOS 設計工具。 此時，您需要以程式設計方式建立您的條件約束，您會有三個選項可供選擇：

* [版面配置的錨點](#Layout-Anchors)-此 API 提供錨點屬性的存取權 (例如`TopAnchor`，`BottomAnchor`或`HeightAnchor`) 為限制的 UI 項目。
* [版面配置條件約束](#Layout-Constraints)-您可以建立直接使用的條件約束`NSLayoutConstraint`類別。
* [視覺化的格式化語言](#Visual-Format-Language)-提供方法來定義您的條件約束類似 ASCII 作品。

下列各節將介紹每個選項，在 詳細資料。

<a name="Layout-Anchors" />

### <a name="layout-anchors"></a>版面配置的錨點

使用`NSLayoutAnchor`類別，您擁有 fluent 介面來建立錨點屬性，為限制的 UI 項目為基礎的條件約束。 例如，檢視控制器的頂端和底端版面配置輔助線會公開`TopAnchor`，`BottomAnchor`和`HeightAnchor`錨點屬性，而檢視會公開邊緣、 中央、 大小和基準的屬性。

> [!IMPORTANT]
> 除了標準的錨點屬性集，iOS 檢視也包含`LayoutMarginsGuides`和`ReadableContentGuide`屬性。 這些屬性會公開`UILayoutGuide`分別檢視的邊界、 可讀性更高的工作物件內容的指南。

版面配置的錨點提供數種方法可以建立條件約束以容易閱讀、 精簡的格式：

- **ConstraintEqualTo** -定義關聯性其中`first attribute = second attribute + [constant]`以選擇性地提供`constant`位移值。
- **ConstraintGreaterThanOrEqualTo** -定義關聯性其中`first attribute >= second attribute + [constant]`以選擇性地提供`constant`位移值。
- **ConstraintLessThanOrEqualTo** -定義關聯性其中`first attribute <= second attribute + [constant]`以選擇性地提供`constant`位移值。

例如: 

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

一般的版面配置條件約束只被以線性的運算式。 請使用以下範例：

[![](programmatic-layout-constraints-images/graph01.png "版面配置條件約束表示為線性的運算式")](programmatic-layout-constraints-images/graph01.png#lightbox)

這會轉換成下列程式行C#程式碼使用版面配置的錨點：

```csharp
PurpleView.LeadingAnchor.ConstraintEqualTo (OrangeView.TrailingAnchor, 10).Active = true; 
```

其中的部分C#程式碼對應至指定的組件的方程式，如下所示：

|方程式|程式碼|
|---|---|
|項目 1|PurpleView|
|屬性 1|LeadingAnchor|
|Relationship|ConstraintEqualTo|
|乘數|預設值為 1.0，因此未指定|
|項目 2|OrangeView|
|屬性 2|TrailingAnchor|
|常數|10.0|

除了提供解決特定的版面配置條件約束方程式所需的參數，每個配置錨點方法會強制執行參數傳遞給它們的型別的安全。 因此水平的條件約束例如錨定`LeadingAnchor`或`TrailingAnchor`僅適用於具有其他水平錨點類型和乘數僅提供給大小條件約束。

<a name="Layout-Constraints" />

### <a name="layout-constraints"></a>版面配置條件約束

您可以藉由直接建構，手動新增自動版面配置條件約束`NSLayoutConstraint`在C#程式碼。 不同於使用版面配置的錨點中,，您必須指定每個參數值，即使它不會影響上所定義的條件約束。 如此一來，您最後會產生相當多的難以閱讀，未定案程式碼。 例如: 

```csharp
//// Pin the leading edge of the view to the margin
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Leading, NSLayoutRelation.Equal, View, NSLayoutAttribute.LeadingMargin, 1.0f, 0.0f).Active = true;

//// Pin the trailing edge of the view to the margin
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Trailing, NSLayoutRelation.Equal, View, NSLayoutAttribute.TrailingMargin, 1.0f, 0.0f).Active = true;

//// Give the view a 1:2 aspect ratio
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Height, NSLayoutRelation.Equal, OrangeView, NSLayoutAttribute.Width, 2.0f, 0.0f).Active = true;
```

其中`NSLayoutAttribute`列舉會定義檢視的邊界值，並對應至`LayoutMarginsGuide`屬性，例如`Left`， `Right`，`Top`並`Bottom`和`NSLayoutRelation`列舉會定義關聯性，將建立以指定的屬性之間`Equal`，`LessThanOrEqual`或`GreaterThanOrEqual`。

不同於配置的錨點 api，`NSLayoutConstraint`建立方法執行不反白顯示特定的條件約束相當重要的層面並沒有任何編譯時間檢查條件約束上執行。 如此一來，很容易就能建構無效的條件約束，將會在執行階段擲回例外狀況。

<a name="Visual-Format-Language" />

### <a name="visual-format-language"></a>視覺化格式語言

視覺化的格式語言，可讓您定義使用類似字串，提供條件約束所建立的視覺表示法的 ASCII 作品的條件約束。 這有下列優點和缺點：

- 視覺化的格式語言會強制執行建立只有有效的條件約束。
 - 自動版面配置會輸出到主控台使用視覺化格式語言，因此偵錯的訊息看起來將類似的程式碼，用來建立條件約束的條件約束。
 - 視覺化的格式語言，可讓您建立多個條件約束在相同的時間，以非常精簡的運算式。
 - 因為沒有任何編譯端驗證的視覺化格式語言字串，在執行階段只可以發現問題。
 - 由於視覺化格式語言強調視覺效果，透過某些條件約束類型不能與它建立 （例如比例） 的完整性。

使用視覺化格式語言，建立條件約束時，您可以採取下列步驟：

1. 建立`NSDictionary`包含檢視的物件和版面配置輔助線和定義格式時，會使用字串索引鍵。
2. （選擇性） 建立`NSDictionary`，定義一組索引鍵和值 (`NSNumber`) 的條件約束使用的常數值。
3. 單一資料行或資料列的項目，請建立要配置的格式字串。
4. 呼叫`FromVisualFormat`方法的`NSLayoutConstraint`類別來產生條件約束。
5. 呼叫`ActivateConstraints`方法的`NSLayoutConstraint`啟用並套用條件約束的類別。

例如，若要建立視覺化的格式語言前置和尾端的條件約束，您可以使用下列：

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

視覺化的格式語言一律會建立時使用的預設間距，附加至父檢視邊界零點條件約束，因為此程式碼會產生相同的結果，以顯示上述的範例。

針對更複雜的 UI 設計，例如，在單一行上的多個子檢視視覺化格式語言會指定水平間距和垂直對齊方式。 如同上述範例中，它會指定`AlignAllTop``NSLayoutFormatOptions`對齊的所有資料列或資料行，以其短片中檢視。

請參閱 Apple[視覺化格式語言附錄](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1)如指定一般的條件約束和視覺化的格式字串文法的所需的一些範例。

<a name="Summary" />

## <a name="summary"></a>總結

本指南提供建立和使用自動版面配置條件約束，在C#而不是在 iOS 設計工具中以圖形方式建立。 首先，它會探討使用版面配置的錨點 (`NSLayoutAnchor`) 來處理自動版面配置。 接下來，它會顯示如何使用版面配置條件約束 (`NSLayoutConstraint`)。 最後，它會顯示使用視覺化格式語言，針對自動版面配置。

## <a name="related-links"></a>相關連結

- [Storyboard 簡介](~/ios/user-interface/storyboards/index.md)
- [iOS 設計的控制項逐步解說](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [使用適用於 iOS 的 Xamarin 設計工具的自動版面配置](~/ios/user-interface/designer/designer-auto-layout.md#modifying-in-code)
- [Apple-以程式設計方式建立條件約束](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html#//apple_ref/doc/uid/TP40010853-CH16-SW1)
- [Apple-視覺化格式語言附錄](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1)
