---
title: IOS 11 中的架構變更
description: 本文件說明 iOS 11 中的 32 位元應用程式已被的取代。 它討論如何更新目標的 64 位元架構的應用程式。
ms.prod: xamarin
ms.assetid: 55F62F3F-8570-402B-B7D9-2875F76CB946
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/13/2016
ms.openlocfilehash: b7d1df6ed2a8bd480025681ebcbe48acd7592564
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61169337"
---
# <a name="architecture-changes-in-ios-11"></a>IOS 11 中的架構變更

其中一個最大的變更，您應該要注意的 ios 11 會取代的 32 位元應用程式的支援，如下所述[Apple](https://developer.apple.com/news/?id=06282017b)新聞稿。 所有新的應用程式和更新現有的應用程式必須支援 64 位元。 32 位元應用程式**將不會啟動**iOS 11 中。

若要更新您的應用程式，在 Visual Studio for Mac，請使用下列步驟：

1. 以滑鼠右鍵按一下專案名稱，以開啟**專案選項**。
2. 瀏覽至**iOS 組建** 索引標籤。
3. 設定**支援的架構**下拉式清單，以**x86_64** for**偵錯 | iPhoneSimulator**並**版本 | iPhoneSimulator**:

    ![變更模擬器架構下拉式清單](architecture-changes-images/image1.png)

4. 針對 iOS 裝置，將設定變更為**偵錯 | iPhone**並設定**支援的架構**下拉式清單可**ARM64**:

    ![變更裝置架構下拉式清單](architecture-changes-images/image2.png)

5. 將組態變更為**發行 | iPhone**並設定**支援的架構**下拉式清單可**ARM64**。

如需有關 32 位元和 64 位元架構的詳細資訊，請參閱[32/64 位元平台考量](~/cross-platform/macios/32-and-64/index.md#ios)指南。

## <a name="related-links"></a>相關連結

- [新功能 iOS 11 (Apple)](https://developer.apple.com/ios/)
- [更新應用程式市集產品頁面 (Apple)](https://developer.apple.com/app-store/product-page/)
- [更新您的應用程式，適用於 iOS 11 (WWDC) （影片）](https://developer.apple.com/videos/play/wwdc2017/204/)
