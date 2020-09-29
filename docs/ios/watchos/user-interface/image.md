---
title: Xamarin 中的 watchOS 影像控制項
description: 本檔說明如何在以 Xamarin 建立的 watchOS 應用程式中使用影像控制項。 它會討論 WKInterfaceImage 控制項、SetImage 方法、將影像新增至監看式延伸模組、動畫等等。
ms.prod: xamarin
ms.assetid: B741C207-3427-46F3-9C90-A52BF8933FA4
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: f1c21d64c8e1e271043e7d0b918f6033e21daac7
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91432991"
---
# <a name="watchos-image-controls-in-xamarin"></a>Xamarin 中的 watchOS 影像控制項

watchOS 提供 [`WKInterfaceImage`](xref:WatchKit.WKInterfaceImage) 控制項來顯示影像和簡單的動畫。 有些控制項也可以有背景影像 (例如按鈕、群組和介面控制器) 。

![Apple Watch ](image-images/image-walkway.png) ![ 以簡單的動畫顯示圖片 Apple Watch](image-images/image-animation.png)
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

使用資產目錄映射將影像新增至 Watch 套件應用程式。
只有 **@2x** 版本是必要的，因為所有監看式裝置都有 Retina 顯示。

![只有2x 版本是必要的，因為所有監看式裝置都有 Retina 顯示器](image-images/asset-universal-sml.png)

最好的作法是確定影像本身為監看顯示的正確大小。 *避免* 使用不正確調整大小的影像 (特別是大型影像) 和縮放，以顯示在監看式上。

您可以使用資產目錄映射中的 Watch 套件大小 (38mm 和 42mm) ，為每個顯示大小指定不同的影像。

![您可以使用資產目錄映射中的 Watch 套件大小38mm 和42mm，為每個顯示大小指定不同的影像](image-images/asset-watch-sml.png)

## <a name="images-on-the-watch"></a>觀賞上的影像

顯示影像最有效率的方式，就是將 *它們包含在 watch 應用程式專案中* ，然後使用方法來顯示影像 `SetImage(string imageName)` 。

例如， [WatchKitCatalog](/samples/xamarin/ios-samples/watchos-watchkitcatalog/) 範例在 watch 應用程式專案中有一些新增至資產目錄的影像：

![WatchKitCatalog 範例在 watch 應用程式專案中有一些新增至資產目錄的影像](image-images/asset-whale-sml.png)

您可以使用 `SetImage` with string name 參數，有效率地在 watch 上載入和顯示這些專案：

```csharp
myImageControl.SetImage("Whale");
myOtherImageControl.SetImage("Worry");
```

### <a name="background-images"></a>背景影像

相同的邏輯適用于 `SetBackgroundImage (string imageName)` `Button` 、 `Group` 和類別上的 `InterfaceController` 。 將影像儲存在 watch 應用程式本身，即可達到最佳效能。

## <a name="images-in-the-watch-extension"></a>Watch 延伸模組中的影像

除了載入儲存在監看式應用程式本身的影像之外，您還可以從延伸模組組合將影像傳送至 watch 應用程式以供顯示 (或是從遠端位置下載影像，並顯示這些) 。

若要從監看式延伸模組載入影像，請建立 `UIImage` 實例，然後 `SetImage` 使用物件來呼叫 `UIImage` 。

例如， [WatchKitCatalog](/samples/xamarin/ios-samples/watchos-watchkitcatalog) 範例在監看延伸專案中有一個名為 **黃蜂** 的映射：

![WatchKitCatalog 範例在監看延伸專案中有一個名為黃蜂的影像](image-images/asset-bumblebee-sml.png)

下列程式碼會導致：

- 正在載入至記憶體中的映射，以及
- 顯示在監看式上。

```csharp
using (var image = UIImage.FromBundle ("Bumblebee")) {
    myImageControl.SetImage (image);
}
```

## <a name="animations"></a>動畫

若要建立一組影像的動畫，它們的開頭都應該是相同的前置詞，並具有數位尾碼。

[WatchKitCatalog](/samples/xamarin/ios-samples/watchos-watchkitcatalog)範例的 watch 應用程式專案中，有一系列的編號影像具有**匯流排**首碼：

![WatchKitCatalog 範例的 watch 應用程式專案中有一系列的編號影像具有匯流排首碼](image-images/asset-bus-animation-sml.png)

若要以動畫顯示這些影像，請先使用前置詞名稱載入影像， `SetImage` 然後呼叫 `StartAnimating` ：

```csharp
animatedImage.SetImage ("Bus");
animatedImage.StartAnimating ();
```

呼叫 `StopAnimating` 影像控制項以停止動畫迴圈：

```csharp
animatedImage.StopAnimating ();
```

<a name="cache"></a>

## <a name="appendix-caching-images-watchos-1"></a>附錄： (watchOS 1) 快取影像

> [!IMPORTANT]
> watchOS 3 應用程式完全在裝置上執行。 下列資訊僅適用于 watchOS 1 應用程式。

如果應用程式重複使用儲存在延伸模組 (或已) 下載的映射，您可以在監看的儲存體中快取映射，以提升後續顯示器的效能。

使用 `WKInterfaceDevice` s `AddCachedImage` 方法將影像傳送至監看式，然後使用 `SetImage` with image name 參數作為字串來顯示它：

```csharp
var device = WKInterfaceDevice.CurrentDevice;
using (var image = UIImage.FromBundle ("Bumblebee")) {
    if (!device.AddCachedImage (image, "Bumblebee")) {
            Console.WriteLine ("Image cache full.");
        } else {
            cachedImage.SetImage ("Bumblebee");
        }
    }
}
```

您可以使用程式碼，在程式碼中查詢影像快取的內容 `WKInterfaceDevice.CurrentDevice.WeakCachedImages` 。

### <a name="managing-the-cache"></a>管理快取

20 MB 大小的快取。 它會在應用程式重新開機時保持不變，而且當它填滿時，您必須負責在 `RemoveCachedImage` 物件上使用或方法清除檔案 `RemoveAllCachedImages` `WKInterfaceDevice.CurrentDevice` 。

## <a name="related-links"></a>相關連結

- [WatchKitCatalog (範例) ](/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Apple 的影像檔](https://developer.apple.com/documentation/watchkit/wkinterfaceimage)