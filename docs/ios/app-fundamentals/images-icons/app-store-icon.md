---
title: 應用程式存放區圖示
description: 本文涵蓋包括和管理影像資產 Xamarin.iOS 應用程式中的要做為應用程式存放區圖示。
ms.prod: xamarin
ms.assetid: BFB5665A-F557-46E1-B35E-870CC2026AD9
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/26/2017
ms.openlocfilehash: f8d993ccb23817e237b9cef8074b881f3ea4b3a2
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="app-store-icon"></a>應用程式存放區圖示

_本文涵蓋包括和管理影像資產 Xamarin.iOS 應用程式中的要做為應用程式存放區圖示。_

Xcode 9 之前是透過 iTunes Connect 新增所有的應用程式存放區圖示。 不過，這不會再是大小寫。 應用程式市集圖示現在必須納入您的專案組合的一部分並加入資產目錄中。 不包含應用程式存放區圖示的應用程式將會拒絕的 Apple。

應用程式存放區圖示是表面之應用程式給使用者，因此它必須是文數而顯示在小的大小。 容易記憶的圖示為乾淨、簡單、可立即辨識。

Apple 建議在設計應用程式圖示時遵循下列指導方針：

- 確保圖示適合應用程式。
- 建立與應用程式設計一致的簡單圖示。
- 圖示中避免使用文字。
- 全域思考：單一應用程式圖式將用於市集內的所有區域。

在 App Store 中顯示的應用程式圖示必須有 1024 x 1024 影像像素。  Apple 表明資產目錄中的應用程式市集圖示不得透明或含有 Alpha 色板。

如需詳細資訊，請參閱 Apple [iOS 人性化介面指導方針](https://developer.apple.com/ios/human-interface-guidelines/icons-and-images/image-size-and-resolution/)。

## <a name="adding-an-app-store-icon"></a>新增應用程式存放區圖示

App Store 圖示現在應以資產目錄傳遞。 

若要加入應用程式存放區圖示執行下列作業：

1. 找出**AppIcon**映像中設定**Assets.xcassets**專案檔。 
    - 所有新的專案應該會隨附**Assets.xcassets** AppIcon 映像組的檔案。
    - 若要加入新的資產目錄，以滑鼠右鍵按一下您的專案並選取**新增 > 新的檔案 > 資產目錄**。
    - 若要加入新的應用程式圖示的影像集合，以滑鼠右鍵按一下該圖示集區，然後選取**應用程式圖示和啟動影像 > 新的應用程式圖示**:
    
    ![加入新的映像 set 選項](app-store-icon-images/image1.png)

2. 捲動到**App Store**清單中的圖示：

    ![應用程式存放區圖示](app-store-icon-images/image2.png)

3. 按一下圖示，然後瀏覽 1024 x 1024 影像像素。 將儲存為資產類別。




## <a name="related-links"></a>相關連結

- [管理資產目錄圖示](~/ios/app-fundamentals/images-icons/app-icons.md#managing)
