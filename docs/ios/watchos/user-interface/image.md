---
title: watchOS 在 Xamarin 中的影像控制項
description: 本文件說明如何使用 Xamarin 建置 watchOS 應用程式中使用影像控制項。 它討論 WKInterfaceImage 控制項，SetImage 方法，將影像加入至監看式延伸模組、 動畫和更多功能。
ms.prod: xamarin
ms.assetid: B741C207-3427-46F3-9C90-A52BF8933FA4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: a9e23f1bfa6099c64a0a60d78ecc3c6283a86a96
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108082"
---
# <a name="watchos-image-controls-in-xamarin"></a>watchOS 在 Xamarin 中的影像控制項

提供 watchOS [ `WKInterfaceImage` ](https://developer.xamarin.com/api/type/WatchKit.WKInterfaceImage/)控制項來顯示影像和簡單的動畫。 有些控制項也可以有背景影像 （例如按鈕、 群組和介面控制器）。

![](image-images/image-walkway.png "Apple Watch 顯示圖片") ![ ] (image-images/image-animation.png "Apple Watch 使用簡單的動畫")
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

若要將影像加入至監看式套件應用程式中使用資產目錄映像。
只有**@2x**版本是必要的因為所有監看 Retina 顯示器的裝置。

![](image-images/asset-universal-sml.png "只有 2 個 x 版本是必要的因為所有監看 Retina 顯示器的裝置")

它是個不錯的做法，確保自己的映像會監看式顯示正確的大小。 *避免*使用正確調整大小的映像 （特別是大型的） 和調整大小以監看式上予以顯示。

您可以使用資產目錄映像中的監看式套件大小 （38 公釐和 42 公釐），來指定不同的映像的每個顯示的大小。

![](image-images/asset-watch-sml.png "您也可以在資產目錄映像使用 38 公釐，42 mm 的監看式套件大小，指定不同的映像的每個顯示大小")


## <a name="images-on-the-watch"></a>在 監看式上的映像

是最有效率的方式來顯示影像*將它們包含在監看式應用程式專案*並將它們顯示使用`SetImage(string imageName)`方法。

例如， [WatchKitCatalog](https://developer.xamarin.com/samples/WatchKitCatalog/)範例有一些映像加入至監看式應用程式專案中的資產目錄：

![](image-images/asset-whale-sml.png "WatchKitCatalog 範例有一些映像加入至監看式應用程式專案中的資產目錄")

這些可以有效率地載入並顯示在 監看式使用`SetImage`與字串名稱參數：

```csharp
myImageControl.SetImage("Whale");
myOtherImageControl.SetImage("Worry");
```

### <a name="background-images"></a>背景影像

適用於相同的邏輯`SetBackgroundImage (string imageName)`上`Button`， `Group`，和`InterfaceController`類別。 藉由監看式應用程式本身中儲存映像獲得最佳的效能。


## <a name="images-in-the-watch-extension"></a>在 監看式延伸模組的映像

除了載入儲存在監看式應用程式本身的映像，您也可以將映像從延伸模組套件組合中，傳送 watch 應用程式顯示 （或您可以從遠端位置，下載映像，並顯示那些）。

若要從監看式延伸模組載入映像，建立`UIImage`執行個體，然後呼叫`SetImage`使用`UIImage`物件。

例如， [WatchKitCatalog](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)範例具有名為影像**Bumblebee**監看式延伸模組專案中：

![](image-images/asset-bumblebee-sml.png "WatchKitCatalog 範例具有名 Bumblebee 為監看式延伸模組專案中的映像")

下列程式碼會產生：

- 正在載入到記憶體中，將映像和
- 顯示在 監看式。

```csharp
using (var image = UIImage.FromBundle ("Bumblebee")) {
    myImageControl.SetImage (image);
}
```


## <a name="animations"></a>Animations

若要動畫顯示一組映像，他們應該所有開頭為相同的前置詞和數值的後置字元。

[WatchKitCatalog](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)範例有監看式應用程式專案內的一系列的已編號的映像**匯流排**前置詞：

![](image-images/asset-bus-animation-sml.png "WatchKitCatalog 範例有一系列的已編號的映像，在監看式應用程式專案中使用的匯流排前置詞")

若要顯示這些影像為動畫，第一次載入映像使用`SetImage`具有前置詞的名稱，然後呼叫`StartAnimating`:

```csharp
animatedImage.SetImage ("Bus");
animatedImage.StartAnimating ();
```

呼叫`StopAnimating`影像控制項停止動畫迴圈：

```csharp
animatedImage.StopAnimating ();
```


<a name="cache" />

## <a name="appendix-caching-images-watchos-1"></a>附錄： 快取映像 (watchOS 1)

> [!IMPORTANT]
> watchOS 3 應用程式上執行整個裝置。 下列資訊是僅限 watchOS 1 應用程式。

如果應用程式重複使用的映像會儲存在副檔名為 （或已下載），就可以快取中監看式的儲存體，以提升效能，後續的顯示畫面的影像。

使用 [ `WKInterfaceDevice`s`AddCachedImage`方法，以將映像傳輸到監看式]，然後使用`SetImage`與映像名稱做為字串參數來顯示它：

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

您可以查詢在程式碼中使用的映像快取內容`WKInterfaceDevice.CurrentDevice.WeakCachedImages`。


### <a name="managing-the-cache"></a>管理快取

快取可處理 20 MB 的大小。 它會保留到應用程式重新啟動之後，以及它填滿時，您必須負責清除 使用檔案`RemoveCachedImage`或是`RemoveAllCachedImages`上的方法`WKInterfaceDevice.CurrentDevice`物件。



## <a name="related-links"></a>相關連結

- [WatchKitCatalog （範例）](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Apple 的映像文件](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Images.html)
