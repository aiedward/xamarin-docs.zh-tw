---
title: 將您的應用程式更新至 iOS 11
description: 本文件所連結到各種描述可用的 iOS 11 版本的 Xamarin.iOS 開發人員的新功能的指南。 比方說，視覺化設計，應用程式存放區的變更而更新，並更新應用程式圖示。
ms.prod: xamarin
ms.assetid: EC809504-9CF6-4949-B6EE-36384297E744
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/13/2016
ms.openlocfilehash: 3193f27c30080a4335abfe969acb3c8b33516469
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61386337"
---
# <a name="updating-your-app-to-ios-11"></a>將您的應用程式更新至 iOS 11

在 iOS 11 中，Apple 已引進架構更新、 新的視覺變更和更新的 iTunes Connect 程序。 本指南探索這些變更，協助您取得您的 Xamarin.iOS 應用程式更新適用於 iOS 11。

## <a name="architecture-changesarchitecture-changesmd"></a>[架構變更](architecture-changes.md)

其中一個最大的變更，您應該要注意的 ios 11 會取代的 32 位元應用程式的支援，如下所述[Apple](https://developer.apple.com/news/?id=06282017b)新聞稿。

本指南將引導您完成更新您的應用程式，適用於 64 位元。

## <a name="visual-design-updatesvisual-designmd"></a>[視覺化設計更新](visual-design.md)

在 iOS 11 中，Apple 已引進新的視覺變更，包括更新的導覽列、 搜尋列和資料表檢視。 除了進行了改進以提供更大的彈性，邊界和全螢幕的內容。 本指南會說明這些變更。

## <a name="app-store-changesapp-store-changesmd"></a>[App Store 變更](app-store-changes.md)

IOS App Store 已完全重新設計，不僅可讓使用者快速地瀏覽市集，但也可讓您的身為開發人員，以升級您的應用程式給使用者。 這些促銷活動包含在應用程式內購買的更新和更新的產品頁面。 iOS 11 也會加入有關如何與使用者通訊、 如何加入您的應用程式圖示及如何應用程式發行至公用的更新。

## <a name="app-icon-updates"></a>應用程式圖示更新

> [!NOTE]
> 應用程式圖示現在應藉由傳遞_資產目錄_。 

如需使用資產目錄的詳細資訊請參閱[App Store 圖示](~/ios/app-fundamentals/images-icons/app-store-icon.md)指南。 如需移轉的說明圖示，將從 Info.plist 的資產目錄，請參閱[Info.plist 從移轉至資產目錄](~/ios/app-fundamentals/images-icons/app-icons.md)指南。

所需的圖示在資產目錄命名為**App Store**而且應該是 1024 x 1024 的大小。 Apple 表明資產目錄中的應用程式市集圖示不得透明或含有 Alpha 色板。

![應用程式儲存在資產目錄中的圖示位置。](images/image1.png)

## <a name="related-links"></a>相關連結

- [新功能 iOS 11 (Apple)](https://developer.apple.com/ios/)
- [更新應用程式市集產品頁面 (Apple)](https://developer.apple.com/app-store/product-page/)
- [更新您的應用程式，適用於 iOS 11 (WWDC) （影片）](https://developer.apple.com/videos/play/wwdc2017/204/)
