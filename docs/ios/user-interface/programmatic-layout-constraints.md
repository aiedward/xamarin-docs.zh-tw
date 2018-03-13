---
title: "以程式設計方式配置條件約束"
description: "本指南提供使用 iOS 自動配置條件約束而不是 iOS 設計工具中建立它們的 C# 程式碼。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 119C8365-B470-4CD4-85F7-086F0A46DCBB
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 7819201e76e268ea84bf2cc5d49a5a07b20a04e3
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="programmatic-layout-constraints"></a>以程式設計方式配置條件約束

_本指南提供使用 iOS 自動配置條件約束而不是 iOS 設計工具中建立它們的 C# 程式碼。_

自動配置 （也稱為 「 自動調整的版面配置 」） 是一種回應式設計方法。 不同於這個過渡期的版面配置系統，其中每個元素的位置是硬式編碼至螢幕上的點，自動配置是關於*關聯性*-設計介面上的其他項目位置的項目的位置。 自動配置的核心是 條件約束或規則，以定義位置的項目或項目集在螢幕上的其他項目內容中的概念。 在螢幕上的特定位置，不會繫結項目，因為條件約束會協助建立起來不錯上不同的螢幕大小和裝置的方向調整版面配置。

通常當使用自動配置在 iOS 中，您將使用 iOS 設計工具，以圖形方式將您的 UI 項目上的配置限制。 不過，有些時候可能會當您需要建立適用於 C# 程式碼中的條件約束。 比方說，當使用以動態方式建立 UI 項目加入至`UIView`。

本指南說明如何建立和使用 C# 程式碼使用而不 iOS 設計工具中以圖形方式建立的條件約束。

<a name="Creating-Constraints-Programmatically" />

## <a name="creating-constraints-programmatically"></a>以程式設計方式建立條件約束

如前所述，通常您會經常使用自動配置條件約束 iOS 設計工具中。 若是您需要以程式設計方式建立您的條件約束的時間點，您有三個選項可供選擇：

* [版面配置起點](#Layout-Anchors)-此應用程式開發介面提供錨點屬性的存取權 (例如`TopAnchor`，`BottomAnchor`或`HeightAnchor`) 為限制的 UI 項目。
* [配置條件約束](#Layout-Constraints)-您可以建立條件約束直接使用`NSLayoutConstraint`類別。
* [視覺化格式的語言](#Visual-Format-Language)-提供方法，以定義您的條件約束類似 ASCII 美工圖案。

下列各節將介紹詳細資料中每一個選項。

<a name="Layout-Anchors" />

### <a name="layout-anchors"></a>版面配置錨點

使用`NSLayoutAnchor`類別，您有 fluent 應用程式開發介面來建立錨點屬性，為限制的 UI 項目所根據的條件約束。 例如，檢視控制站的上方和下方版面配置輔助線公開`TopAnchor`，`BottomAnchor`和`HeightAnchor`錨定屬性，而檢視會公開邊緣、 中央、 大小和基準內容。

> [!IMPORTANT]
> **注意：**錨點屬性的標準集，除了 iOS 檢視也包括`LayoutMarginsGuides`和`ReadableContentGuide`屬性。 這些屬性會公開`UILayoutGuide`工作與檢視的邊界、 可讀性更高的物件分別內容輔助線。

版面配置起點提供容易閱讀、 精簡的格式建立條件約束的幾種方法：

- **ConstraintEqualTo** -定義的關聯性其中`first attribute = second attribute + [constant]`與選擇性提供`constant`位移值。
- **ConstraintGreaterThanOrEqualTo** -定義的關聯性其中`first attribute >= second attribute + [constant]`與選擇性提供`constant`位移值。
- **ConstraintLessThanOrEqualTo** -定義的關聯性其中`first attribute <= second attribute + [constant]`與選擇性提供`constant`位移值。

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

一般的版面配置條件約束可以表示為線性的運算式。 請使用以下範例：

[![](programmatic-layout-constraints-images/graph01.png "表示為線性運算式配置條件約束")](programmatic-layout-constraints-images/graph01.png#lightbox)

這會轉換成 C# 程式碼使用配置起點的下列一行：

```csharp
PurpleView.LeadingAnchor.ConstraintEqualTo (OrangeView.TrailingAnchor, 10).Active = true; 
```

其中的 C# 程式碼組件對應至給定的方程式的部分，如下所示：

<table width="100%" border="1">
<tr>
<td width="50%"><b>方程式</b></td><td><b>程式碼</b></td>
</tr>
<tr>
<td width="50%">項目 1</td><td>PurpleView</td>
</tr>
<tr>
<td width="50%">1 屬性</td><td>LeadingAnchor</td>
</tr>
<tr>
<td width="50%">Relationship</td><td>ConstraintEqualTo</td>
</tr>
<tr>
<td width="50%">乘數</td><td>預設值為 1.0，因此未指定</td>
</tr>
<tr>
<td width="50%">項目 2</td><td>OrangeView</td>
</tr>
<tr>
<td width="50%">屬性 2</td><td>TrailingAnchor</td>
</tr>
<tr>
<td width="50%">常數</td><td>10.0</td>
</tr>
</table>

除了提供解決指定的配置的條件約束方程式所需的參數，每個配置錨點方法，強制執行的參數傳遞給它們的類型安全。 因此水平的條件約束將這類錨定`LeadingAnchor`或`TrailingAnchor`僅適用於其他水平的錨點與型別和乘數僅提供大小限制。

<a name="Layout-Constraints" />

### <a name="layout-constraints"></a>配置條件約束

您可以手動加入自動配置條件約束，以直接建構`NSLayoutConstraint`C# 程式碼。 不像使用配置錨點，您必須指定每個參數值，即使它不會影響上所定義的條件約束。 如此一來，您最後會產生大量的難閱讀，未定案程式碼。 例如: 

```csharp
//// Pin the leading edge of the view to the margin
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Leading, NSLayoutRelation.Equal, View, NSLayoutAttribute.LeadingMargin, 1.0f, 0.0f).Active = true;

//// Pin the trailing edge of the view to the margin
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Trailing, NSLayoutRelation.Equal, View, NSLayoutAttribute.TrailingMargin, 1.0f, 0.0f).Active = true;

//// Give the view a 1:2 aspect ratio
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Height, NSLayoutRelation.Equal, OrangeView, NSLayoutAttribute.Width, 2.0f, 0.0f).Active = true;
```

其中`NSLayoutAttribute`列舉定義檢視的邊界值，而且對應至`LayoutMarginsGuide`屬性，例如`Left`， `Right`，`Top`和`Bottom`和`NSLayoutRelation`列舉定義的關聯性，將做為指定的屬性之間建立`Equal`，`LessThanOrEqual`或`GreaterThanOrEqual`。

不同於配置的錨點 api，`NSLayoutConstraint`建立方法執行不反白顯示特定的條件約束的重要層面並沒有任何編譯時間執行的條件約束檢查。 如此一來，所以可以輕鬆地建構無效的條件約束，將會在執行階段擲回例外狀況。

<a name="Visual-Format-Language" />

### <a name="visual-format-language"></a>視覺化格式的語言

視覺化格式語言，可讓您定義的條件約束使用 ASCII 美工圖案，例如提供條件約束所建立的視覺表示法的字串。 這有下列優點和缺點：

- 視覺化格式語言會強制執行建立只有有效的條件約束。
 - 自動配置輸出至主控台使用視覺化格式語言，因此偵錯的訊息將會類似程式碼用來建立條件約束的條件約束。
 - 視覺化格式語言，可讓您建立多個條件約束在相同的時間非常精簡的運算式。
 - 因為沒有任何編譯端驗證的視覺化格式的語言字串，在執行階段只可以探索問題。
 - 因為視覺化格式語言完整性透過強調視覺效果無法建立某些條件約束類型與它 （例如比例）。

使用視覺化格式語言，建立條件約束時，您可以採取下列步驟：

1. 建立`NSDictionary`包含檢視表物件和版面配置輔助線和字串索引鍵定義格式時使用。
2. 選擇性地建立`NSDictionary`，定義一組索引鍵和值 (`NSNumber`) 的條件約束使用的常數值。
3. 單一資料行或資料列的項目，請建立版面配置的格式字串。
4. 呼叫`FromVisualFormat`方法`NSLayoutConstraint`類別來產生條件約束。
5. 呼叫`ActivateConstraints`方法`NSLayoutConstraint`啟動，並將條件約束套用的類別。

例如，若要建立視覺化格式語言前置和尾端的條件約束，您可以使用下列：

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

視覺化格式語言一律會建立時使用的預設間距，附加至父檢視邊界零點條件約束，則此程式碼會產生以上所顯示的範例相同的結果。

更複雜的 UI 設計，例如多個在單一行中的子檢視的視覺化格式語言所指定的水平間距和垂直對齊方式。 如同上述範例其中指定`AlignAllTop``NSLayoutFormatOptions`對齊所有檢視中的資料列或資料行到其上緣。

請參閱 Apple[視覺化格式語言附錄](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1)如需指定一般條件約束和視覺化格式字串文法的一些範例。

<a name="Summary" />

## <a name="summary"></a>總結

本指南提供建立與使用 C# 中的自動配置條件約束，而非在 iOS 設計工具中以圖形方式建立。 首先，它看使用配置起點 (`NSLayoutAnchor`) 來處理自動配置。 接下來，示範了如何使用配置條件約束 (`NSLayoutConstraint`)。 最後，它會顯示視覺化格式語言使用自動配置。

## <a name="related-links"></a>相關連結

- [Storyboard 簡介](~/ios/user-interface/storyboards/index.md)
- [iOS 可以設計控制項的逐步解說](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [使用 Xamarin 設計工具，適用於 iOS 的自動配置](~/ios/user-interface/designer/designer-auto-layout.md#modifying-in-code)
- [Apple-以程式設計方式建立條件約束](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html#//apple_ref/doc/uid/TP40010853-CH16-SW1)
- [Apple-視覺格式語言附錄](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1)
