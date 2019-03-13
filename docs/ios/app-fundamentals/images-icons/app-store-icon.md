---
title: 在 Xamarin.iOS 中的應用程式市集圖示
description: 本文件說明如何管理 Xamarin.iOS 應用程式的 App Store 圖示使用資產目錄。 先前，itunes Connect 管理 App Store 圖示。
ms.prod: xamarin
ms.assetid: BFB5665A-F557-46E1-B35E-870CC2026AD9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/26/2017
ms.openlocfilehash: 53e25ae9f4650254f2aaaa03dc8727fae674c9f0
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105879"
---
# <a name="app-store-icons-in-xamarinios"></a>在 Xamarin.iOS 中的應用程式市集圖示

Xcode 9 之前所有的 App Store 圖示已新增透過 iTunes Connect。 不過，這已超出的情況。 App Store 圖示現在必須納入您的專案套件組合的一部分並新增資產目錄中。 Apple 會拒絕沒有 App Store 圖示的應用程式。

App Store 圖示是您的應用程式，因此它必須是令人印象深刻的使用者並顯示在小尺寸下也的面貌。 容易記憶的圖示為乾淨、簡單、可立即辨識。

Apple 建議在設計應用程式圖示時遵循下列指導方針：

- 確保圖示適合應用程式。
- 建立與應用程式設計一致的簡單圖示。
- 圖示中避免使用文字。
- 全域思考：單一應用程式圖式將用於市集內的所有區域。

在 App Store 中顯示的應用程式圖示必須有 1024 x 1024 影像像素。  Apple 表明資產目錄中的應用程式市集圖示不得透明或含有 Alpha 色板。

如需詳細資訊，請參閱 Apple [iOS 人性化介面指導方針](https://developer.apple.com/ios/human-interface-guidelines/icons-and-images/image-size-and-resolution/)。

## <a name="adding-an-app-store-icon"></a>加入 App Store 圖示

App Store 圖示現在應以資產目錄傳遞。 

加入 App Store 圖示執行下列作業：

1. 找出**Assets.xcassets**中設定的映像**appicon**在專案檔案。 
    - 所有新的專案應該具備**appicon**檔案，其中包含 Assets.xcassets 影像集。
    - 若要加入新的資產目錄，以滑鼠右鍵按一下專案，然後選取**新增 > 新的檔案 > 資產目錄**。
    - 若要加入新的應用程式圖示的影像集，以滑鼠右鍵按一下該圖示集區，然後選取**應用程式圖示和啟動影像 > 新的應用程式圖示**:
    
    ![加入新的映像 set 選項](app-store-icon-images/image1.png)

2. 捲動到  **App Store**清單中的圖示：

    ![App Store 圖示](app-store-icon-images/image2.png)

3. 按一下圖示，並瀏覽 1024 x 1024 影像像素。 儲存資產目錄。




## <a name="related-links"></a>相關連結

- [管理圖示使用資產目錄](~/ios/app-fundamentals/images-icons/app-icons.md#managing)
