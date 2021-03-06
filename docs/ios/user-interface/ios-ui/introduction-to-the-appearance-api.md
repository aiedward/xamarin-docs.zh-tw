---
title: Xamarin 中的外觀 API
description: iOS 可讓您在靜態類別層級套用視覺化屬性設定，而不是在個別物件上套用，以便將變更套用至應用程式中該控制項的所有實例。
ms.prod: xamarin
ms.assetid: C1727F0C-82B1-D085-D46F-C6383FF04B16
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 11/15/2018
ms.openlocfilehash: 7ba6eca8f74c10254ae93b95725bc73ae100be70
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433846"
---
# <a name="appearance-api-in-xamarinios"></a>Xamarin 中的外觀 API

_iOS 可讓您在靜態類別層級套用視覺化屬性設定，而不是在個別物件上套用，以便將變更套用至應用程式中該控制項的所有實例。_

這項功能是在 Xamarin 中透過 `Appearance` 所有支援它的 UIKit 控制項上的靜態屬性來公開。 您可以輕鬆地自訂視覺外觀 (屬性，例如色調色彩和背景影像) ，以提供您的應用程式一致的外觀。 外觀 API 是在 iOS 5 引進的，雖然它的某些部分在 iOS 9 中已淘汰，但仍是在 Xamarin iOS 應用程式中達成部分樣式和主題效果的好方法。

## <a name="overview"></a>概觀

iOS 可讓您自訂許多 UIKit 控制項的外觀，讓標準控制項符合您想要套用至應用程式的商標。

有兩種不同的方式可套用自訂外觀：

- **直接在控制項實例上** ，您可以在許多控制項（包括工具列、導覽列、按鈕和滑杆）上，設定色調色彩、背景影像和標題位置 (以及某些其他屬性) 。

- **設定外觀靜態屬性的預設值** –每個控制項的可自訂屬性會透過 `Appearance` 靜態屬性公開。 您套用至這些屬性的任何自訂，都會當做設定屬性之後所建立之任何類型控制項的預設值使用。

外觀範例應用程式會示範這三種方法，如下列螢幕擷取畫面所示：

[![外觀範例應用程式會示範這三種方法](introduction-to-the-appearance-api-images/appearance01-sml.png)](introduction-to-the-appearance-api-images/appearance01.png#lightbox)

從 iOS 8 起，外觀 proxy 已擴充至 TraitCollections。
 `AppearanceForTraitCollection` 可以用來設定特定特性集合的預設面板。 您可以在「腳本 [簡介](~/ios/user-interface/storyboards/unified-storyboards.md) 」指南中閱讀詳細資訊。

## <a name="setting-appearance-properties"></a>設定外觀屬性

在第一個畫面中，靜態外觀類別是用來為按鈕和黃色/橙色元素（如下所示）進行樣式：

```csharp
// Set the default appearance values
UIButton.Appearance.TintColor = UIColor.LightGray;
UIButton.Appearance.SetTitleColor(UIColor.FromRGB(0,127,14), UIControlState.Normal);

UISlider.Appearance.ThumbTintColor = UIColor.Red;
UISlider.Appearance.MinimumTrackTintColor = UIColor.Orange;
UISlider.Appearance.MaximumTrackTintColor = UIColor.Yellow;

UIProgressView.Appearance.ProgressTintColor = UIColor.Yellow;
UIProgressView.Appearance.TrackTintColor = UIColor.Orange;
```

在 `ViewDidLoad` 覆寫預設值和 *外觀* 靜態類別的方法中，綠色元素樣式的設定方式如下：

```csharp
slider2.ThumbTintColor = UIColor.FromRGB (0,127,70); // dark green
slider2.MinimumTrackTintColor = UIColor.FromRGB (66,255,63);
slider2.MaximumTrackTintColor = UIColor.FromRGB (197,255,132);
```

```csharp
progress2.ProgressTintColor = UIColor.FromRGB (66,255,63);
progress2.TrackTintColor = UIColor.FromRGB (197,255,132);
```

## <a name="using-uiappearance-in-xamarinforms"></a>在 Xamarin 中使用 UIAppearance

在 Xamarin 方案中設定 [iOS 應用程式的樣式](~/xamarin-forms/platform/ios/formatting.md#uiappearance-api) 時，外觀 API 可能很有用。 類別中的幾行 `AppDelegate` 可協助您在不需要建立 [自訂](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)轉譯器的情況下，執行特定的色彩配置。

### <a name="custom-themes-and-uiappearance"></a>自訂主題和 UIAppearance

iOS 可讓使用者介面控制項的許多視覺屬性使用 *UIAppearance* api 「主題」，強制特定控制項的所有實例具有相同的外觀。 這會公開為許多使用者介面控制項類別上的外觀屬性，而不是控制項的個別實例。 在靜態屬性上設定顯示內容 `Appearance` ，會影響應用程式中該型別的所有控制項。

若要進一步瞭解概念，請考慮範例。

為了將特定的變更 `UISegmentedControl` 為具有洋紅色色調，我們會參考畫面上的特定控制項，如下所示 `ViewDidLoad` ：

```csharp
sg1.TintColor = UIColor.Magenta;
```

或者，在設計工具的 [屬性] 面板中設定值：

[![Properties Pad 淡色](introduction-to-the-appearance-api-images/propertiespadtint.png)](introduction-to-the-appearance-api-images/propertiespadtint.png#lightbox)

下圖說明這只會在名為 ' sg1 ' 的控制項上設定色彩。

[![設定個別控制項色彩](introduction-to-the-appearance-api-images/image53.png)](introduction-to-the-appearance-api-images/image53.png#lightbox)

若要以這種方式設定許多控制項會完全沒有效率，我們可以改為在 `Appearance` 類別本身設定靜態屬性。 這會顯示在下列程式碼中：

```csharp
UISegmentedControl.Appearance.TintColor = UIColor.Magenta;
```

下圖現在說明兩個分割的控制項，其外觀設定為洋紅：

[![設定外觀控制項色彩](introduction-to-the-appearance-api-images/image54.png)](introduction-to-the-appearance-api-images/image54.png#lightbox)

`Appearance` 在應用程式生命週期（例如，在 AppDelegate 的 `FinishedLaunching` 事件中），或在顯示受影響控制項之前的 ViewController 中，應儘早設定屬性。

如需詳細資訊，請參閱 [外觀 API 簡介](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md) 。

## <a name="related-links"></a>相關連結

- [外觀 (範例) ](/samples/xamarin/ios-samples/appearance)
- [UIAppearance 通訊協定參考](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIAppearance_Protocol/)
- [Xamarin 中的外觀](~/xamarin-forms/platform/ios/formatting.md#uiappearance-api)