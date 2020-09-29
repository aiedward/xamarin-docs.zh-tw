---
title: 在 Xamarin 中購買非取用產品
description: 本檔說明 Xamarin 中的非取用產品，這是由使用者購買的功能，無論裝置為何，都可無限期地使用。
ms.prod: xamarin
ms.assetid: 635D9CA2-6BCA-53E1-7B10-968029AA3493
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 78a4f3ae90b1b20e9fe3cd4164726fee34f38eb7
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435505"
---
# <a name="purchasing-non-consumable-products-in-xamarinios"></a>在 Xamarin 中購買非取用產品

非取用產品是由客戶「擁有」。 預期的情況是，即使裝置遺失/遭竊或購買新的裝置，他們仍會一律擁有其存取權。 這些功能適用于書籍、雜誌問題、遊戲等級、相片篩選器、「pro 功能」等等。一旦使用者購買了非使用中的產品，他們就不需要再次支付費用。 如果您的程式碼不小心讓它們嘗試，StoreKit 會顯示已購買的訊息。

## <a name="non-consumable-products-sample"></a>非取用產品範例

[InAppPurchaseSample 程式碼](/samples/xamarin/ios-samples/storekit)包含一個名為*NonConsumables*的專案。 程式碼範例會示範如何使用相片篩選器作為範例，來執行非取用產品。 一旦您購買了篩選器，就可以將它套用至一或多個相片。 您永遠不需要重新購買它。   

購買程式會顯示在這一系列的螢幕擷取畫面中，[ **購買** ] 按鈕會變成 [功能啟用] 按鈕：   

 [![此一系列的螢幕擷取畫面顯示此購買程式](purchasing-non-consumable-products-images/image34.png)](purchasing-non-consumable-products-images/image34.png#lightbox)   

購買程式與取用產品相同;主要差異在於如何在應用程式程式碼中追蹤購買。 在此範例中，只有在尚未購買產品時，才可使用 [購買] 按鈕，否則按鈕會啟動功能本身。   

下圖顯示類別和 App Store 伺服器之間的互動，以執行非取用產品購買：   

 [![類別和 App Store 伺服器之間的互動，以執行非取用產品購買](purchasing-non-consumable-products-images/image35.png)](purchasing-non-consumable-products-images/image35.png#lightbox)   

從取用範例中的主要差異在於購買完成後，使用者介面會更新以防止重新購買。 在此範例中，成功的交易通知會更新使用者介面，以便將 [ **購買** ] 按鈕轉換成啟用功能本身的按鈕。

## <a name="re-purchasing-non-consumable-products"></a>重新購買非取用產品

成功購買產品之後，您的程式碼通常應該會隱藏或重新調整購買按鈕，以防止使用者再次嘗試購買產品。 範例應用程式會將 [ **購買** ] 按鈕變更為讓範例相片篩選運作的按鈕，以執行此作業。   

在某些情況下，應用程式無法分辨是否已購買非取用產品：

- 如果在裝置上刪除並重新安裝應用程式，則所有的購買記錄都會 (，除非使用者執行備份還原) 。 
- 如果使用者將應用程式安裝在兩個 (或多個) 裝置上，並在其中一個裝置上進行購買。 其他裝置將會繼續顯示可供購買的產品。 
- 如果客戶在這些情況下嘗試重新購買非使用中的產品，App Store 將會再次履行產品而不收費。 使用者介面一開始會顯示執行購買 (例如，會顯示確認警示並需要 Apple ID) 不過，使用者接著會看到一則訊息，告知他們已購買產品。  

此案例中的程式碼路徑與一般購買完全相同，唯一的差別在於：

- 此產品不會再次向使用者收費。
- `SKPaymentTransaction`傳遞至應用程式的物件將具有 `OriginalTransaction` 屬性，該屬性會參考最初購買產品時所產生的交易。 
- 銷售非取用產品的應用程式也必須執行 StoreKit 的  **還原** 功能，以協助使用者取得現有的購買專案。