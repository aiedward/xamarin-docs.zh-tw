---
title: 將您的應用程式更新至 iOS 11
description: 本檔連結的各種指南，會說明已發行 iOS 11 的 Xamarin iOS 開發人員可用的新功能。 例如，視覺效果設計更新、App Store 變更和應用程式圖示更新。
ms.prod: xamarin
ms.assetid: EC809504-9CF6-4949-B6EE-36384297E744
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/13/2016
ms.openlocfilehash: 81d863b12aa7748acc6876929fb5d6361a20b507
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032122"
---
# <a name="updating-your-app-to-ios-11"></a>將您的應用程式更新至 iOS 11

在 iOS 11 中，Apple 引進了架構更新、新的視覺效果變更，以及更新的 iTunes Connect 程式。 本指南會探索每個變更，協助您取得已針對 iOS 11 更新的 Xamarin iOS 應用程式。

## <a name="architecture-changesarchitecture-changesmd"></a>[架構變更](architecture-changes.md)

使用 iOS 11 時，您應該注意的其中一項最大的變更是取代32位的應用程式支援，如[Apple 的](https://developer.apple.com/news/?id=06282017b)新聞版本中所述。

本指南會逐步引導您更新64位的應用程式。

## <a name="visual-design-updatesvisual-designmd"></a>[視覺化設計更新](visual-design.md)

在 iOS 11 中，Apple 引進了新的視覺效果變更，包括對導覽列、搜尋列和資料表視圖的更新。 此外，也提供了改進功能，讓邊界和全螢幕內容更具彈性。 本指南涵蓋這些變更。

## <a name="app-store-changesapp-store-changesmd"></a>[App Store 變更](app-store-changes.md)

IOS App Store 已經完成重新設計，不僅允許使用者有效率地導覽存放區，還可讓您以開發人員的身分將您的應用程式推廣給使用者。 這些升級包括應用程式內購買和產品頁面更新的更新。 iOS 11 也會新增有關如何與使用者通訊、如何新增您的應用程式圖示，以及如何將應用程式發行至公用的更新。

## <a name="app-icon-updates"></a>應用程式圖示更新

> [!NOTE]
> 應用程式圖示現在應由_資產目錄_傳遞。 

如需使用資產目錄的詳細資訊，請參閱[App Store 圖示](~/ios/app-fundamentals/images-icons/app-store-icon.md)指南。 如需將 plist 的圖示從資訊遷移至資產目錄的說明，請參閱 [從 Plist 遷移至資產目錄](~/ios/app-fundamentals/images-icons/app-icons.md)指南。

資產目錄中的必要圖示命名為**App Store** ，且大小應為 1024 x 1024。 Apple 表明資產目錄中的應用程式市集圖示不得透明或含有 Alpha 色板。

![資產目錄中的 App store 圖示位置。](images/image1.png)

## <a name="related-links"></a>相關連結

- [IOS 11 （Apple）的新功能](https://developer.apple.com/ios/)
- [已更新 App Store 產品頁面（Apple）](https://developer.apple.com/app-store/product-page/)
- [更新 iOS 11 應用程式（WWDC）（影片）](https://developer.apple.com/videos/play/wwdc2017/204/)
