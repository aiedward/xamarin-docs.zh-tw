---
title: 應用程式開發介面的外觀
description: iOS 可讓您套用在靜態類別層級，而非針對個別物件的視覺屬性設定，以便將變更套用至該控制項的應用程式中的所有執行個體。
ms.prod: xamarin
ms.assetid: C1727F0C-82B1-D085-D46F-C6383FF04B16
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 7c7e4909cc12f49411c527af12fc0e4855979804
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="appearance-api"></a>應用程式開發介面的外觀

_iOS 可讓您套用在靜態類別層級，而非針對個別物件的視覺屬性設定，以便將變更套用至該控制項的應用程式中的所有執行個體。_

這項功能，會透過靜態公開在 Xamarin.iOS`Appearance`支援它的所有 UIKit 控制項的屬性。 視覺外觀 （屬性，例如為濃淡的色彩和背景的影像） 可以因此輕鬆地自訂，以讓您的應用程式一致的外觀。 IOS 5 中推出，外觀 API，而它的某些部分已被取代，在 iOS 9 它仍然是達到某些設定樣式和佈景主題效果 Xamarin.iOS 應用程式中的好方法。

## <a name="overview"></a>總覽

iOS 可讓您自訂許多 UIKit 控制項，以便符合您要套用到您的應用程式的商標的標準控制項的外觀。

有兩種不同的方式套用自訂的外觀：

- **控制執行個體上直接**– 您可以設定許多控制項包括工具列、 導覽列、 按鈕和滑桿濃淡的色彩、 背景影像和標題位置 （以及某些其他屬性）。

- **設定外觀的靜態屬性的預設值**– 每個控制項的可自訂屬性會公開透過`Appearance`靜態屬性。 您套用這些屬性的任何自訂項目將作為該類型的屬性設定之後所建立的任何控制項的預設值。

外觀的範例應用程式會示範這三個方法，這些螢幕擷取畫面所示：

 [![](introduction-to-the-appearance-api-images/appearance01.png "外觀的範例應用程式將示範三種方法")](introduction-to-the-appearance-api-images/appearance01.png#lightbox)

從 iOS 8，開始外觀 proxy 已擴充到 TraitCollections。
 `AppearanceForTraitCollection` 可用來設定特定特性集合上的預設外觀。 閱讀更多相關內容位於[簡介分鏡腳本](~/ios/user-interface/storyboards/unified-storyboards.md)指南。


## <a name="setting-appearance-properties"></a>設定外觀屬性

在第一個畫面中，靜態外觀類別用於設定樣式的按鈕和黃色/橙色項目，就像這樣：

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

在綠色項目的樣式設定如下，`ViewDidLoad`方法覆寫預設值和*外觀*靜態類別：

```csharp
slider2.ThumbTintColor = UIColor.FromRGB (0,127,70); // dark green
slider2.MinimumTrackTintColor = UIColor.FromRGB (66,255,63);
slider2.MaximumTrackTintColor = UIColor.FromRGB (197,255,132);
```

```csharp
progress2.ProgressTintColor = UIColor.FromRGB (66,255,63);
progress2.TrackTintColor = UIColor.FromRGB (197,255,132);
```

## <a name="using-uiappearance-in-xamarinforms"></a>使用 Xamarin.Forms 中 UIAppearance

外觀 API 時可能十分有用[樣式的 iOS 應用程式](~/xamarin-forms/platform/ios/theme.md#uiappearance)Xamarin.Forms 方案中。 中的幾行`AppDelegate`類別可協助您實作特定的色彩配置，而不需要建立[自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)。


### <a name="custom-themes-and-uiappearance"></a>自訂的佈景主題和 UIAppearance

iOS 可讓許多的視覺屬性的使用者介面控制項 」 佈景主題的 「 使用*UIAppearance* Api 來強制執行所有的執行個體的特定控制項具有相同的外觀。 這會公開為許多使用者介面控制項類別，而非個別的執行個體控制項的外觀屬性。 設定靜態的顯示屬性`Appearance`屬性會影響所有應用程式中的該類型的控制項。

若要進一步了解概念，請考慮使用範例。

若要變更特定`UISegmentedControl`有洋紅色濃淡，我們會參考特定的控制項，我們就像這樣在螢幕上`ViewDidLoad`:

```csharp
sg1.TintColor = UIColor.Magenta;
```

或者，屬性板設計工具中設定的值： 

[![](introduction-to-the-appearance-api-images/propertiespadtint.png "屬性板濃淡")](introduction-to-the-appearance-api-images/propertiespadtint.png#lightbox)

下圖說明處理，這只在名為 'sg1' 的控制項上設定濃淡。

 [![](introduction-to-the-appearance-api-images/image53.png "設定個別控制項濃淡")](introduction-to-the-appearance-api-images/image53.png#lightbox)

若要設定許多控制項以這種方式就是完全沒有效率，因此我們可以改為設定靜態`Appearance`類別本身的屬性。 下列程式碼所示：

```csharp
UISegmentedControl.Appearance.TintColor = UIColor.Magenta;
```

下圖現在會說明兩個不同客層之的控制項設為洋紅的外觀：

 [![](introduction-to-the-appearance-api-images/image54.png "設定外觀控制濃淡")](introduction-to-the-appearance-api-images/image54.png#lightbox)

`Appearance` 屬性設定在應用程式生命週期的早期例如 AppDelegate`FinishedLaunching`事件，或在 ViewController 才能顯示受影響的控制項。


請參閱[外觀 API 簡介](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md)如需詳細資訊。


## <a name="related-links"></a>相關連結

- [外觀 （範例）](https://developer.xamarin.com/samples/monotouch/IntroToAppearance/)
- [UIAppearance 通訊協定參照](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIAppearance_Protocol/)
- [Xamarin.Forms 中的外觀](~/xamarin-forms/platform/ios/theme.md#uiappearance)
