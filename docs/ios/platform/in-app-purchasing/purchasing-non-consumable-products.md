---
title: 在 Xamarin 中購買不可耗用的產品
description: 本檔描述 Xamarin 中的非使用中產品，這是由使用者所購買的功能，不論裝置為何，都能無限期地繼續使用。
ms.prod: xamarin
ms.assetid: 635D9CA2-6BCA-53E1-7B10-968029AA3493
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/18/2017
ms.openlocfilehash: aa478636b4ab94ab000fd98860646bfa300e9fab
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291319"
---
# <a name="purchasing-non-consumable-products-in-xamarinios"></a>在 Xamarin 中購買不可耗用的產品

非耗材產品是由客戶「擁有」。 預期的是，即使他們的裝置遺失/遭竊或購買新的裝置，他們還是可以隨時存取它們。 它們適用于書籍、雜誌問題、遊戲等級、相片篩選器、「pro 功能」等等。一旦使用者購買了不適用的產品之後，就不需要再支付費用。 如果您的程式碼不小心讓他們嘗試，StoreKit 會顯示已購買的訊息。

## <a name="non-consumable-products-sample"></a>非可耗用產品範例

[InAppPurchaseSample 程式碼](https://docs.microsoft.com/samples/xamarin/ios-samples/storekit)包含名為*NonConsumables*的專案。 此程式碼範例會示範如何使用相片篩選器作為範例，來執行不可耗用的產品。 一旦您購買了篩選器，就可以再次將它套用到相片。 您永遠不需要重新購買。   
   
   
   
 購買程式會顯示在這一系列的螢幕擷取畫面中– [**購買**] 按鈕會變成 [功能啟用] 按鈕：   
   
   
   
 [![](purchasing-non-consumable-products-images/image34.png "此購買程式會顯示在這一系列的螢幕擷取畫面中")](purchasing-non-consumable-products-images/image34.png#lightbox)   
   
   
   
 購買程式與可耗用的產品相同;主要差異在於如何在應用程式代碼中追蹤購買。 在此範例中，只有在尚未購買產品時，才可以使用 [購買] 按鈕，否則按鈕會啟用功能本身。   
   
   
   

下圖顯示類別與 App Store 伺服器之間的互動，以執行無法使用的產品購買：   
   
   
   
 [![](purchasing-non-consumable-products-images/image35.png "類別與 App Store 伺服器之間的互動，用以執行無法取用的產品購買")](purchasing-non-consumable-products-images/image35.png#lightbox)   
   
   
   
 與可耗用範例的主要差異在於，一旦購買完成，使用者介面就會更新，以避免重新購買。 在此範例中，成功的交易通知會更新使用者介面，讓 [**購買**] 按鈕轉換成可啟動功能本身的按鈕。

## <a name="re-purchasing-non-consumable-products"></a>重新購買不可耗用的產品

成功購買產品後，您的程式碼通常應該隱藏或重新規劃購買按鈕，以避免使用者再次嘗試購買產品。 範例應用程式的方式是將 [**購買**] 按鈕變更為讓範例相片篩選器運作的按鈕。   
   
   
   
 在某些情況下，應用程式無法判斷是否已購買非可使用的產品：

- 如果在裝置上刪除並重新安裝應用程式，所有的採購記錄都會消失（除非使用者執行備份還原）。 
- 如果使用者將應用程式安裝在兩部（或以上）的裝置上，並在其中一部裝置上進行購買。 其他裝置會繼續顯示可供購買的產品。 
- 如果客戶在這些情況下嘗試重新購買不可取用的產品，App Store 將會再次完成產品而不收費。 使用者介面一開始會顯示執行購買（例如，會顯示確認警示，而且需要 Apple ID），但是使用者會看到一則訊息，告知他們已購買產品。  
   
   
   
 此案例中的程式碼路徑與一般購買完全相同，唯一的差別如下：

- 使用者不會再次針對該產品收費。
- 傳遞至應用程式的`OriginalTransaction` 物件將會有一個屬性，它會參考最初購買產品時所產生的交易。`SKPaymentTransaction` 
- 銷售非取用產品的應用程式也必須執行 StoreKit 的**還原**功能，以協助使用者取得現有的購買專案。 
