---
title: watchOS 影像控制項在 Xamarin
description: 本文件說明如何在使用 Xamarin 所建置的 watchOS 應用程式中使用影像控制項。 其中也會討論 WKInterfaceImage 控制項 SetImage 方法，將影像加入至監看式延伸模組、 動畫和更多。
ms.prod: xamarin
ms.assetid: B741C207-3427-46F3-9C90-A52BF8933FA4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: eb58c587f737a5991a21f0efe9964353a8ab0399
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34791247"
---
# <a name="watchos-image-controls-in-xamarin"></a>watchOS 影像控制項在 Xamarin

提供 watchOS [ `WKInterfaceImage` ](https://developer.xamarin.com/api/type/WatchKit.WKInterfaceImage/)控制項來顯示影像和簡單的動畫。 有些控制項也可以讓背景影像 （例如按鈕、 群組和介面控制站）。

![](image-images/image-walkway.png "Apple Watch 顯示圖片") ![](image-images/image-animation.png "Apple Watch 的簡單動畫")
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

若要將影像加入至監看式套件的應用程式中使用資產目錄映像。
只有**@2x**版本是必要的因為所有監看 Retina 顯示器的裝置。

![](image-images/asset-universal-sml.png "只有 2 x 版本是必要的因為所有監看 Retina 顯示器的裝置")

它是個不錯的做法，確保其本身的映像為監看式顯示正確的大小。 *避免*使用正確調整大小的影像 （特別是大數值） 和縮放上監看式予以顯示。

您可以使用在資產目錄映像 （38 公釐與 42 公釐） 的監看式套件大小來指定不同的映像的每個顯示的大小。

![](image-images/asset-watch-sml.png "您可以使用監看式套件大小 38 公釐，42 公釐資產目錄映像中指定不同的映像的每個顯示的大小")


## <a name="images-on-the-watch"></a>在 監看式上的映像

是最有效率的方式來顯示影像*它們包含在監看式應用程式專案*並將它們顯示使用`SetImage(string imageName)`方法。

例如， [WatchKitCatalog](https://developer.xamarin.com/samples/WatchKitCatalog/)範例即會加入至監看式應用程式專案中的資產目錄的影像數目：

![](image-images/asset-whale-sml.png "WatchKitCatalog 範例即會新增到監看式應用程式專案中的資產目錄映像數目")

這些可以有效率地載入並顯示在 監看式使用`SetImage`與字串名稱參數：

```csharp
myImageControl.SetImage("Whale");
myOtherImageControl.SetImage("Worry");
```

### <a name="background-images"></a>背景影像

適用於相同的邏輯`SetBackgroundImage (string imageName)`上`Button`， `Group`，和`InterfaceController`類別。 達成最佳效能是儲存在監看式應用程式中的映像。


## <a name="images-in-the-watch-extension"></a>Watch 擴充功能中的映像

除了載入儲存在監看式應用程式本身的映像，您可以從延伸模組配套傳送映像顯示監看式應用程式 （或您無法從遠端位置，下載映像，並顯示那些）。

若要從 watch 擴充功能載入映像，建立`UIImage`執行個體，然後呼叫`SetImage`與`UIImage`物件。

例如， [WatchKitCatalog](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)範例具有名為的映像**Bumblebee**監看式擴充功能專案中：

![](image-images/asset-bumblebee-sml.png "WatchKitCatalog 範例具有名為 Bumblebee，監看式擴充功能專案中的映像")

下列程式碼會產生：

- 映像載入到記憶體，並
- 顯示在 監看式。

```csharp
using (var image = UIImage.FromBundle ("Bumblebee")) {
    myImageControl.SetImage (image);
}
```


## <a name="animations"></a>Animations

若要以動畫顯示一組映像，它們應該都是以相同的前置詞，數值後置詞。

[WatchKitCatalog](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)範例有監看式應用程式專案中使用的一系列的已編號的映像**匯流排**前置詞：

![](image-images/asset-bus-animation-sml.png "WatchKitCatalog 範例匯流排前置詞在監看式應用程式專案中有一系列的已編號的映像")

若要以動畫顯示這些映像，請先載入映像使用`SetImage`與首碼名稱，然後再呼叫`StartAnimating`:

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
> 完全在裝置上，執行 watchOS 3 應用程式。 下列資訊適用於僅限 watchOS 1 應用程式。

如果應用程式重複使用儲存在擴充功能 （或已下載） 的映像，它可能會快取中監看式的儲存體，以提升效能，後續的顯示畫面影像。

使用`WKInterfaceDevice`s`AddCachedImage`方法映像轉移到監看式，然後使用`SetImage`與映像名稱做為字串參數來顯示它：

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

您可以在程式碼中使用映像快取的內容來查詢`WKInterfaceDevice.CurrentDevice.WeakCachedImages`。


### <a name="managing-the-cache"></a>管理快取

快取的大小約 20 MB。 保留跨應用程式重新啟動時，並填滿時，清除 使用檔案，您必須負責`RemoveCachedImage`或`RemoveAllCachedImages`方法`WKInterfaceDevice.CurrentDevice`物件。



## <a name="related-links"></a>相關連結

- [WatchKitCatalog （範例）](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Apple 的映像文件](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Images.html)
