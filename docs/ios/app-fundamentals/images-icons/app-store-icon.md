---
title: Xamarin 中的 App Store 圖示
description: 本檔說明如何使用資產目錄來管理 Xamarin iOS 應用程式的 App Store 圖示。 先前，App Store 圖示是使用 iTunes Connect 來管理。
ms.prod: xamarin
ms.assetid: BFB5665A-F557-46E1-B35E-870CC2026AD9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/26/2017
ms.openlocfilehash: 62253adafcd028f459e8ca0cc11a8eb3e54d52c3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73004178"
---
# <a name="app-store-icons-in-xamarinios"></a>Xamarin 中的 App Store 圖示

在 Xcode 9 之前，已透過 iTunes Connect 新增所有 App Store 圖示。 不過，這已不再是這種情況。 App Store 圖示現在必須納入為專案套件組合的一部分，並新增到資產目錄中。 Apple 會拒絕不包含 App Store 圖示的應用程式。

App Store 圖示是您的應用程式對使用者的臉部，因此必須以較小的大小記住並顯示良好。 容易記憶的圖示為乾淨、簡單、可立即辨識。

Apple 建議在設計應用程式圖示時遵循下列指導方針：

- 確保圖示適合應用程式。
- 建立與應用程式設計一致的簡單圖示。
- 圖示中避免使用文字。
- 全域思考：單一應用程式圖式將用於市集內的所有區域。

在 App Store 中顯示的應用程式圖示必須有 1024 x 1024 影像像素。  Apple 表明資產目錄中的應用程式市集圖示不得透明或含有 Alpha 色板。

如需詳細資訊，請參閱 Apple 的[IOS 人力介面指導方針](https://developer.apple.com/ios/human-interface-guidelines/icons-and-images/image-size-and-resolution/)。

## <a name="adding-an-app-store-icon"></a>新增 App Store 圖示

App Store 圖示現在應以資產目錄傳遞。 

若要新增 App Store 圖示，請執行下列動作：

1. 在專案的**assets.xcassets**檔案中，找出 [ **AppIcon** ] 影像集。 
    - 所有的新專案都應該隨附**assets.xcassets**檔案，其中包含 AppIcon 影像集。
    - 若要新增資產目錄，請以滑鼠右鍵按一下您的專案，然後選取 **新增 > 新檔案 > 資產目錄**。
    - 若要新增應用程式圖示影像集，請以滑鼠右鍵按一下 [圖示集] 區域，然後選取 [**應用程式圖示] & 啟動映射 > 新增應用程式圖示**：

    ![[加入新的映射集] 選項](app-store-icon-images/image1.png)

2. 流覽至清單中的**App Store**圖示：

    ![App Store 圖示](app-store-icon-images/image2.png)

3. 按一下圖示，然後流覽您的 1024 x 1024 圖元影像。 儲存資產目錄。

## <a name="related-links"></a>相關連結

- [使用資產目錄管理圖示](~/ios/app-fundamentals/images-icons/app-icons.md#managing)
