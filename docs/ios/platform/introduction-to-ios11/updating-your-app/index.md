---
title: "您的應用程式更新至 iOS 11"
description: "瀏覽 iOS 11 的新功能"
ms.topic: article
ms.prod: xamarin
ms.assetid: EC809504-9CF6-4949-B6EE-36384297E744
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/13/2016
ms.openlocfilehash: cced7cc3d1b0579c36d598ef0d05da872478c8dc
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="updating-your-app-to-ios-11"></a>您的應用程式更新至 iOS 11

_瀏覽 iOS 11 的新功能_

在 iOS 11、 Apple 已引進架構更新、 新的視覺效果變更，以及更新的 iTunes Connect 程序。 本指南會探索每個這些變更，可幫助您更新適用於 iOS 11 Xamarin.iOS 應用程式。

## <a name="architecture-changesarchitecture-changesmd"></a>[架構變更](architecture-changes.md)

其中一個最大的變更，您應該留意 iOS 11 是已被取代的應用程式中所述的 32 位元支援[Apple](https://developer.apple.com/news/?id=06282017b)按 釋出。

本指南將引導您完成更新您的應用程式，針對 64 位元。

## <a name="visual-design-updatesvisual-designmd"></a>[視覺化設計更新](visual-design.md)

在 iOS 11 中，Apple 引進了新的視覺效果變更，包括瀏覽列、 搜尋列和資料表檢視更新。 此外已改良上邊界和全螢幕的內容，以便更大的彈性。 本指南涵蓋這些變更。

## <a name="app-store-changesapp-store-changesmd"></a>[App Store 變更](app-store-changes.md)

IOS App Store 已完全重新設計，這不只可讓使用者有效率地巡覽存放區，但也可讓您，身為開發人員，以升級您的應用程式的使用者。 這些升級包含在應用程式內購買的更新和更新的產品頁面。 iOS 11 也會加入有關如何與使用者通訊、 如何新增您的應用程式圖示和如何發行應用程式公開的更新。

## <a name="app-icon-updates"></a>更新應用程式圖示

> [!NOTE]
> 現在應該由傳送應用程式圖示_資產目錄_。 

如需使用資產目錄資訊是指[應用程式存放區圖示](~/ios/app-fundamentals/images-icons/app-store-icon.md)指南。 如需移轉的說明圖示，將從 Info.plist 資產目錄，請參閱[從 Info.plist 移轉至資產目錄](~/ios/app-fundamentals/images-icons/app-icons.md)指南。

必要的圖示資產類別中名為**App Store**而且應該是 1024 x 1024 的大小。 Apple 表明資產目錄中的應用程式市集圖示不得透明或含有 Alpha 色板。

![應用程式會將圖示位置儲存在資產目錄。](images/image1.png)

## <a name="related-links"></a>相關連結

- [什麼是 iOS (Apple) 11 中的新功能](https://developer.apple.com/ios/)
- [更新應用程式市集產品網頁 (Apple)](https://developer.apple.com/app-store/product-page/)
- [正在更新您的應用程式適用於 iOS 11 (WWDC) （影片）](https://developer.apple.com/videos/play/wwdc2017/204/)
