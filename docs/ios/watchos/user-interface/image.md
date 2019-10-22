---
title: Xamarin 中的 watchOS 影像控制項
description: 本檔說明如何在以 Xamarin 建立的 watchOS 應用程式中使用影像控制項。 其中討論 WKInterfaceImage 控制項、SetImage 方法、將影像新增至監看式擴充功能、動畫等等。
ms.prod: xamarin
ms.assetid: B741C207-3427-46F3-9C90-A52BF8933FA4
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: f9367eda7651ca61a8a3cb0928ad11cb320faab6
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "70769965"
---
# <a name="watchos-image-controls-in-xamarin"></a>Xamarin 中的 watchOS 影像控制項

watchOS 提供[`WKInterfaceImage`](xref:WatchKit.WKInterfaceImage)控制項，以顯示影像和簡單的動畫。 有些控制項也可以具有背景影像（例如按鈕、群組和介面控制器）。

![](image-images/image-walkway.png "顯示圖片的 Apple Watch") ![](image-images/image-animation.png "具有簡單動畫的 Apple Watch")
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

使用資產類別目錄映射來新增影像以監看套件應用程式。
只需要 **@2x** 版本，因為所有 watch 裝置都有 Retina 顯示。

![](image-images/asset-universal-sml.png "Only 2x versions are required, since all watch devices have Retina displays")

最好的作法是確保影像本身是監看顯示的正確大小。 *避免*使用大小不正確的影像（特別是大型映射）和縮放比例，以顯示在監看式上。

您可以使用資產目錄影像中的監看套件大小（38mm 和42mm），為每個顯示大小指定不同的影像。

![](image-images/asset-watch-sml.png "You can use the Watch Kit sizes 38mm and 42mm in an asset catalog image to specify different images for each display size")

## <a name="images-on-the-watch"></a>監看式上的影像

顯示影像最有效率的方式是將*它們包含在 watch 應用程式專案中*，並使用 `SetImage(string imageName)` 方法來顯示它們。

例如， [WatchKitCatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog/)範例在監看式應用程式專案中，已將許多影像新增至資產目錄：

![](image-images/asset-whale-sml.png "The WatchKitCatalog sample has a number of images added to an asset catalog in the watch app project")

您可以使用 `SetImage` 搭配字串名稱參數，有效率地載入並顯示在 watch 上：

```csharp
myImageControl.SetImage("Whale");
myOtherImageControl.SetImage("Worry");
```

### <a name="background-images"></a>背景影像

相同的邏輯適用于 `Button`、`Group` 和 `InterfaceController` 類別上的 `SetBackgroundImage (string imageName)`。 藉由將影像儲存在監看式應用程式本身來達到最佳效能。

## <a name="images-in-the-watch-extension"></a>監看式擴充功能中的影像

除了載入儲存在 watch 應用程式本身的影像之外，您還可以將延伸模組配套中的影像傳送至監看式應用程式以供顯示（或者，您可以從遠端位置下載影像，並加以顯示）。

若要從 watch 擴充功能載入影像，請建立 `UIImage` 實例，然後使用 `UIImage` 物件呼叫 `SetImage`。

例如， [WatchKitCatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)範例在 watch 擴充功能專案中具有名為**黃蜂**的映射：

![](image-images/asset-bumblebee-sml.png "The WatchKitCatalog sample has an image named Bumblebee in the watch extension project")

下列程式碼將會產生：

- 正在載入記憶體中的影像，以及
- 顯示在監看式上。

```csharp
using (var image = UIImage.FromBundle ("Bumblebee")) {
    myImageControl.SetImage (image);
}
```

## <a name="animations"></a>Animations

若要建立一組影像的動畫，它們的開頭都應該是相同的前置詞，並具有數值尾碼。

[WatchKitCatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)範例在 watch 應用程式專案中有一系列具有**匯流排**前置詞的已編號影像：

![](image-images/asset-bus-animation-sml.png "The WatchKitCatalog sample has a series of numbered images in the watch app project with the Bus prefix")

若要將這些影像顯示為動畫，請先使用 `SetImage` 加上前置詞名稱載入影像，然後再呼叫 `StartAnimating`：

```csharp
animatedImage.SetImage ("Bus");
animatedImage.StartAnimating ();
```

在影像控制項上呼叫 `StopAnimating` 以停止動畫迴圈：

```csharp
animatedImage.StopAnimating ();
```

<a name="cache" />

## <a name="appendix-caching-images-watchos-1"></a>附錄：快取影像（watchOS 1）

> [!IMPORTANT]
> watchOS 3 應用程式會完全在裝置上執行。 下列資訊僅適用于 watchOS 1 應用程式。

如果應用程式重複使用儲存在擴充功能中的映射（或已下載），您可以在監看式的儲存體中快取影像，以提高後續顯示器的效能。

使用 `WKInterfaceDevice`s `AddCachedImage` 方法將影像傳送至 watch，然後使用 [影像名稱] 參數的 `SetImage`，做為顯示它的字串：

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

您可以使用 `WKInterfaceDevice.CurrentDevice.WeakCachedImages`，在程式碼中查詢影像快取的內容。

### <a name="managing-the-cache"></a>管理快取

快取的大小約為 20 MB。 它會在應用程式重新開機時保持不變，而當它填滿時，您必須負責使用 `WKInterfaceDevice.CurrentDevice` 物件上的 `RemoveCachedImage` 或 `RemoveAllCachedImages` 方法來清除檔案。

## <a name="related-links"></a>相關連結

- [WatchKitCatalog （範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Apple 的影像檔](https://developer.apple.com/documentation/watchkit/wkinterfaceimage)
