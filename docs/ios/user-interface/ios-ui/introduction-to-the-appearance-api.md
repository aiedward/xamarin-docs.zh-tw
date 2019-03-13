---
title: 在 Xamarin.iOS 中的外觀 API
description: iOS 可讓您套用在靜態類別層級，而不是個別物件的視覺屬性設定，讓變更套用至該控制項的應用程式中的所有執行個體。
ms.prod: xamarin
ms.assetid: C1727F0C-82B1-D085-D46F-C6383FF04B16
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 11/15/2018
ms.openlocfilehash: bfbc902b0912527fea6aaa58c6706ef5a0ccbf8e
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/10/2019
ms.locfileid: "54207917"
---
# <a name="appearance-api-in-xamarinios"></a>在 Xamarin.iOS 中的外觀 API

_iOS 可讓您套用在靜態類別層級，而不是個別物件的視覺屬性設定，讓變更套用至該控制項的應用程式中的所有執行個體。_

這項功能會公開在 Xamarin.iOS 中透過靜態`Appearance`支援它的所有 UIKit 控制項的屬性。 若要讓您的應用程式一致的外觀皆因此可輕鬆自訂視覺外觀 （為濃淡的色彩和背景影像的屬性等）。 外觀 API 首見於 iOS 5 和它的某些部分已被取代，在 iOS 9 它仍無法達到某些樣式和佈景主題效果在 Xamarin.iOS 應用程式的好方法。

## <a name="overview"></a>總覽

iOS 可讓您自訂多個 UIKit 控制項，來使符合您想要套用到您的應用程式的商標標準控制項的外觀。

有兩種不同的方式，將自訂外觀：

- **控制執行個體上直接**– 您可以設定許多控制項，包括工具列、 導覽列、 按鈕和滑桿濃淡色彩、 背景影像和標題位置 （以及某些其他屬性）。

- **設定外觀的靜態屬性上的預設值**– 每個控制項可自訂的屬性會公開透過`Appearance`靜態屬性。 您將套用至這些屬性的任何自訂將會作為之後的屬性設定會建立該任何的類型控制項的預設值。

外觀的範例應用程式會示範所有三種方法，如下列螢幕擷取畫面所示：

[![](introduction-to-the-appearance-api-images/appearance01-sml.png "外觀的範例應用程式將示範這三種方法")](introduction-to-the-appearance-api-images/appearance01.png#lightbox)

從 iOS 8，開始外觀 proxy 已擴充到 TraitCollections。
 `AppearanceForTraitCollection` 可用來在特定的特性集合上設定的預設外觀。 您可以閱讀更多中了解[分鏡腳本簡介](~/ios/user-interface/storyboards/unified-storyboards.md)指南。

## <a name="setting-appearance-properties"></a>設定外觀屬性

在第一個畫面中，靜態的外觀類別用來設定樣式的按鈕和黃色/橘色項目，像這樣：

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

在綠色的項目樣式設定如下，`ViewDidLoad`它會覆寫預設值的方法和*外觀*靜態類別：

```csharp
slider2.ThumbTintColor = UIColor.FromRGB (0,127,70); // dark green
slider2.MinimumTrackTintColor = UIColor.FromRGB (66,255,63);
slider2.MaximumTrackTintColor = UIColor.FromRGB (197,255,132);
```

```csharp
progress2.ProgressTintColor = UIColor.FromRGB (66,255,63);
progress2.TrackTintColor = UIColor.FromRGB (197,255,132);
```

## <a name="using-uiappearance-in-xamarinforms"></a>在 Xamarin.Forms 中使用 UIAppearance

外觀 API 可能十分有用[設定的 iOS 應用程式的樣式](~/xamarin-forms/platform/ios/formatting.md#uiappearance)Xamarin.Forms 方案中。 中的幾行`AppDelegate`類別可協助實作特定的色彩配置，而不需要建立[自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)。

### <a name="custom-themes-and-uiappearance"></a>自訂佈景主題和 UIAppearance

iOS 可讓許多的視覺屬性的使用者介面控制項，若要設定使用的 「 佈景主題 」 *UIAppearance* Api 來強制執行特定的控制項具有相同的外觀的所有執行個體。 這會公開為許多使用者介面控制項類別，而非個別的執行個體控制項的外觀屬性。 設定靜態的顯示屬性`Appearance`屬性會影響您的應用程式中該類型的所有控制項。

若要進一步了解概念，請考慮範例。

若要變更特定`UISegmentedControl`若要讓洋紅色濃淡，我們會參考特定的控制項，我們在如下的畫面上`ViewDidLoad`:

```csharp
sg1.TintColor = UIColor.Magenta;
```

或者，在設計工具的 [屬性] 面板中設定的值：

[![](introduction-to-the-appearance-api-images/propertiespadtint.png "屬性面板濃淡")](introduction-to-the-appearance-api-images/propertiespadtint.png#lightbox)

下圖說明此設定只控制項名為 'sg1' 上的濃淡。

[![](introduction-to-the-appearance-api-images/image53.png "設定個別控制項濃淡")](introduction-to-the-appearance-api-images/image53.png#lightbox)

若要設定許多控制項以這種方式就是完全沒有效率，因此我們可以改為設定靜態`Appearance`類別本身的屬性。 這是由下列程式碼所示：

```csharp
UISegmentedControl.Appearance.TintColor = UIColor.Magenta;
```

現在下, 圖會說明這兩個分段的控制項設定為洋紅的外觀：

[![](introduction-to-the-appearance-api-images/image54.png "設定外觀控制濃淡")](introduction-to-the-appearance-api-images/image54.png#lightbox)

`Appearance` 應該先設定屬性及早在應用程式生命週期中，例如在 AppDelegate 中`FinishedLaunching`事件，或在 ViewController 才會顯示受影響的控制項。

請參閱[外觀 API 的簡介](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md)如需詳細資訊。

## <a name="related-links"></a>相關連結

- [外觀 （範例）](https://developer.xamarin.com/samples/monotouch/Appearance/)
- [UIAppearance 通訊協定參考](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIAppearance_Protocol/)
- [在 Xamarin.Forms 中的外觀](~/xamarin-forms/platform/ios/formatting.md#uiappearance)
