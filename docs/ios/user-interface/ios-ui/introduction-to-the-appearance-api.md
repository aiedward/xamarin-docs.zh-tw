---
title: Xamarin 中的外觀 API
description: iOS 可讓您在靜態類別層級（而不是個別物件）套用視覺屬性設定，如此一來，變更就會套用到應用程式中該控制項的所有實例。
ms.prod: xamarin
ms.assetid: C1727F0C-82B1-D085-D46F-C6383FF04B16
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 11/15/2018
ms.openlocfilehash: b0f09a729c6998e7a728bfc3d805058e7a43a54a
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70287072"
---
# <a name="appearance-api-in-xamarinios"></a>Xamarin 中的外觀 API

_iOS 可讓您在靜態類別層級（而不是個別物件）套用視覺屬性設定，如此一來，變更就會套用到應用程式中該控制項的所有實例。_

這項功能會透過所有支援它的 UIKit 控制項`Appearance`上的靜態屬性，在 Xamarin 中公開。 因此，可以輕鬆地自訂視覺外觀（像是色調色彩和背景影像等屬性），讓您的應用程式具有一致的外觀。 外觀 API 是在 iOS 5 中引進，而其中某些部分在 iOS 9 中已被取代，但仍是在 Xamarin iOS 應用程式中達成一些樣式和主題效果的好方法。

## <a name="overview"></a>總覽

iOS 可讓您自訂許多 UIKit 控制項的外觀，使標準控制項符合您想要套用至應用程式的商標。

有兩種不同的方式可以套用自訂外觀：

- **直接在控制項實例上**–您可以在許多控制項上設定色調色彩、背景影像和標題位置（以及其他屬性），包括工具列、導覽列、按鈕和滑杆。

- 在**外觀靜態屬性上設定預設值**–每個控制項的可自訂屬性`Appearance`會透過靜態屬性公開。 您套用至這些屬性的任何自訂，都會當做在設定屬性之後所建立之該類型的任何控制項的預設值使用。

外觀範例應用程式會示範這三個方法，如下列螢幕擷取畫面所示：

[![](introduction-to-the-appearance-api-images/appearance01-sml.png "外觀範例應用程式會示範這三種方法")](introduction-to-the-appearance-api-images/appearance01.png#lightbox)

從 iOS 8，外觀 proxy 已擴充至 TraitCollections。
 `AppearanceForTraitCollection`可以用來設定特定特性集合的預設面板。 您可以在分鏡腳本[簡介](~/ios/user-interface/storyboards/unified-storyboards.md)指南中閱讀更多相關資訊。

## <a name="setting-appearance-properties"></a>設定外觀屬性

在第一個畫面中，靜態外觀類別是用來為按鈕和黃色/橙色元素的樣式，如下所示：

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

在覆寫預設值和`ViewDidLoad` *外觀*靜態類別的方法中，綠色元素樣式的設定如下：

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

在 Xamarin. Forms 解決方案中設定[iOS 應用程式的樣式](~/xamarin-forms/platform/ios/formatting.md#uiappearance)時，外觀 API 會很有用。 `AppDelegate`類別中有幾行可以協助您執行特定的色彩配置，而不需要建立[自訂](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)轉譯器。

### <a name="custom-themes-and-uiappearance"></a>自訂主題和 UIAppearance

iOS 可讓使用者介面控制項的許多視覺化屬性「主題」使用*UIAppearance* api，以強制特定控制項的所有實例都具有相同的外觀。 這會公開為許多使用者介面控制項類別上的外觀屬性，而不是控制項的個別實例。 在靜態`Appearance`屬性上設定顯示內容會影響應用程式中該類型的所有控制項。

若要進一步瞭解此概念，請考慮範例。

若要將特定`UISegmentedControl`的設為具有洋紅色色調，我們會參考螢幕上的特定控制項，如下`ViewDidLoad`所示：

```csharp
sg1.TintColor = UIColor.Magenta;
```

或者，在設計工具的 [屬性] 面板中設定值：

[![](introduction-to-the-appearance-api-images/propertiespadtint.png "Properties Pad 淡色")](introduction-to-the-appearance-api-images/propertiespadtint.png#lightbox)

下圖說明，這只會在名為 ' sg1 ' 的控制項上設定色調。

[![](introduction-to-the-appearance-api-images/image53.png "設定個別的控制項淡色")](introduction-to-the-appearance-api-images/image53.png#lightbox)

若要以這種方式設定許多控制項，會完全沒有效率，因此我們可以改`Appearance`為在類別本身設定靜態屬性。 這會顯示在下列程式碼中：

```csharp
UISegmentedControl.Appearance.TintColor = UIColor.Magenta;
```

下圖說明兩個分割的控制項，並將其外觀設定為洋紅：

[![](introduction-to-the-appearance-api-images/image54.png "設定外觀控制項淡色")](introduction-to-the-appearance-api-images/image54.png#lightbox)

`Appearance`屬性應該在應用程式生命週期的早期設定，例如在 AppDelegate 的`FinishedLaunching`事件中，或在顯示受影響控制項之前的 ViewController 中。

如需詳細資訊，請參閱[外觀 API 簡介](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md)。

## <a name="related-links"></a>相關連結

- [外觀（範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/appearance)
- [UIAppearance 通訊協定參考](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIAppearance_Protocol/)
- [Xamarin 中的外觀](~/xamarin-forms/platform/ios/formatting.md#uiappearance)
