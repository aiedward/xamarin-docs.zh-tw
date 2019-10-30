---
title: iOS 11 簡介
description: 本檔連結到描述 iOS 11 功能的各種指南，包括 ARKit、CoreML、MapKit、PDFKit、SiriKit、願景 framework 等等。
ms.prod: xamarin
ms.assetid: 22C38EA6-6DA9-4B92-B41B-814E589033F6
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/19/2017
ms.openlocfilehash: 7262e44fe862e7a5c71f0b1dcfa5d672932fd4ee
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032191"
---
# <a name="introduction-to-ios-11"></a>iOS 11 簡介

![ARKit 範例](images/arkit.png) ![AR 放置物件](images/arkit2.png) ![CoreML 範例](images/coreml.png) ![MapKit 範例](images/mapkit.png) ![視覺矩形範例](images/vision1.png) ![視覺臉部範例](images/vision2.png) ![拖放範例](images/drag-drop.png) ![拖放範例](images/drag-drop2.png) ![SiriKit 範例](images/sirikit.png)

iOS 11 包含各種不同架構的全新功能和增強功能：

## <a name="preparing-your-app-for-ios-11updating-your-appindexmd"></a>[為 iOS 11 應用程式做好準備](updating-your-app/index.md)

Apple 已引進適用于 iOS 11 的架構更新、新的視覺效果變更和更新的 iTunes Connect 程式。 使用本指南來確保您的 Xamarin iOS 應用程式已準備好發行新版本。

## <a name="arkitarkitindexmd"></a>[ARKit](arkit/index.md)

ARKit 將增強的現實帶入 iOS，讓使用者能夠透過裝置的攝影機與世界互動。
使用 Xamarin 時，您也可以搭配使用[ARKit 與 UrhoSharp](arkit/urhosharp.md)。

## <a name="coremlcoremlmd"></a>[CoreML](coreml.md)

您可以使用 CoreML 將機器學習模型整合到 iOS 11 應用程式中。 CoreML 架構提供簡單的 API，可將現有的模型納入應用程式專案中，以便在應用程式中直接分析問題。

## <a name="corenfccorenfcmd"></a>[CoreNFC](corenfc.md)

iPhone 7 和更新版本的裝置可以讀取近距離無線通訊（NFC）標籤，讓應用程式能夠偵測到其周圍的已標記產品、地點或事物。

## <a name="drag-and-dropdrag-and-dropmd"></a>[拖放](drag-and-drop.md)

拖放架構提供全 iOS 的支援，可透過觸控來四處移動資料。 在 iPad 上，您可以在不同的應用程式內和之間拖曳;在 iPhone 上，您只能在相同的應用程式內拖曳。 支援許多類型的自訂，包括豐富的資料類型、動畫，以及處理多點觸控手勢。

## <a name="mapkitmapkitmd"></a>[MapKit](mapkit.md)

MapKit 有一些改良功能，包括支援自動標記群組，以及將羅盤新增至視圖。

## <a name="pdfkit"></a>PDFKit

PDFKit 現已于 iOS 11 提供，可將 PDF 建立和編輯功能帶入您的應用程式。

## <a name="sirikitsirikitmd"></a>[SiriKit](sirikit.md)

Siri 現在支援更多互動，包括清單和附注，以及其他增強功能，例如替代應用程式名稱。

## <a name="visionvisionmd"></a>[Vision](vision.md)

將各種影像處理和分析功能帶入 iOS，包括臉部偵測和辨識、CoreML 模型、新的條碼偵測 Api、文字和範圍偵測，以及更一般的物件偵測和追蹤。

## <a name="samples"></a>範例

我們有一些C# [範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS11)可説明您開始使用：

- [ARKit 範例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-arkitsample)
- [ARKit 放置物件](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-arkitplacingobjects)
- [ARKit 和 UrhoSharp](arkit/urhosharp.md)
- [CoreML 影像辨識範例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-coremlimagerecognition)
- [使用 Azure 自訂模型的 CoreML](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-coremlazuremodel)
- [CoreNFC 標記讀取器範例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-nfctagreader)
- [拖曳 & Drop Table View](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-draganddroptableview)
- [拖曳 & Drop Collection View](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-draganddropcollectionview)
- [拖曳 & 卸載自訂視圖](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-draganddropcustomview)
- [DragBoard 拖曳 & Drop Sample](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-draganddropdragboard)
- [MapKit 範例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-mapkitsample)
- [SiriKit 範例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-sirikitsample)
- [更新的相片架構範例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-samplephotoapp)
- [願景 & CoreML 範例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-coremlvision)
- [視覺矩形偵測範例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-visionrectangles/)
- [視覺臉部偵測範例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-visionfaces)
- [PDKFit 小工具範例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-pdfannotationwidgetsadvanced)
- [PDFKit 浮水印範例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-pdfdocumentwatermark)

## <a name="related-links"></a>相關連結

- [Xamarin iOS 11 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS11)
