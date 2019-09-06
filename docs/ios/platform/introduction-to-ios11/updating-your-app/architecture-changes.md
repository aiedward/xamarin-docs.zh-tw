---
title: IOS 11 中的架構變更
description: 本檔說明如何在 iOS 11 中取代32位應用程式。 它討論如何將應用程式更新成以64位架構為目標。
ms.prod: xamarin
ms.assetid: 55F62F3F-8570-402B-B7D9-2875F76CB946
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/13/2016
ms.openlocfilehash: 15cd6139cc83639146e6044d2b791d94ee30f2d9
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70286355"
---
# <a name="architecture-changes-in-ios-11"></a>IOS 11 中的架構變更

使用 iOS 11 時，您應該注意的其中一項最大的變更是取代32位的應用程式支援，如[Apple 的](https://developer.apple.com/news/?id=06282017b)新聞版本中所述。 現有應用程式的所有新應用程式和更新都必須支援64位。 32位應用程式**將不會**在 iOS 11 中啟動。

若要在 Visual Studio for Mac 中更新您的應用程式，請使用下列步驟：

1. 以滑鼠右鍵按一下專案名稱，以開啟 [**專案選項**]。
2. 流覽至 [ **IOS 組建**] 索引標籤。
3. 針對**Debug | iphonesimulator;** 和**Release | iphonesimulator;** ，將 [**支援的架構**] 下拉式設定為 [ **x86_64** ]：

    ![變更模擬器架構下拉式](architecture-changes-images/image1.png)

4. 若是 iOS 裝置，請將設定變更為 [ **Debug] | [iPhone** ]，並將 [支援的**架構**] 下拉式下拉設定為**ARM64**：

    ![[變更裝置架構] 下拉式](architecture-changes-images/image2.png)

5. 將設定變更為 [**發行] | [iPhone** ]，並將 [支援的**架構**] 下拉式細分為 [ **ARM64**]。

如需32位和64位架構的詳細資訊，請參閱[32/64 位平臺考慮](~/cross-platform/macios/32-and-64/index.md#ios)指南。

## <a name="related-links"></a>相關連結

- [IOS 11 （Apple）的新功能](https://developer.apple.com/ios/)
- [已更新 App Store 產品頁面（Apple）](https://developer.apple.com/app-store/product-page/)
- [更新 iOS 11 應用程式（WWDC）（影片）](https://developer.apple.com/videos/play/wwdc2017/204/)
