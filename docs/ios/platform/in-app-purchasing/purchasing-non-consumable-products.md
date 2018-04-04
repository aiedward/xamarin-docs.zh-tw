---
title: 購買非可使用產品
ms.prod: xamarin
ms.assetid: 635D9CA2-6BCA-53E1-7B10-968029AA3493
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 0a581dc222e43f8d4742bd52dc56dc691449a8f2
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="purchasing-non-consumable-products"></a>購買非可使用產品

非可使用產品 '' 所擁有的客戶。 預期是，它們一定會存取它們，即使他們的裝置遺失/遭竊或購買新的。 它們是適用於書籍、 雜誌問題、 遊戲的層級、 相片篩選、 'pro 功能' 等。一旦使用者已購買的非可使用產品，就不需要支付它一次。 如果您的程式碼不小心讓他們再試一次，StoreKit 會顯示已購買的訊息。

## <a name="non-consumable-products-sample"></a>非可使用產品範例

[InAppPurchaseSample 程式碼](https://developer.xamarin.com/samples/monotouch/StoreKit/)包含名為專案*NonConsumables*。 程式碼範例示範如何實作做為範例使用相片篩選的非可使用產品。 一旦您購買篩選您可以不斷地將它套用到相片。 您永遠不需要重新購買它。   
   
   
   
 採購程序會顯示這一系列的螢幕擷取畫面 –**購買**按鈕會變成功能啟用按鈕：   
   
   
   
 [![](purchasing-non-consumable-products-images/image34.png "採購程序會顯示這一系列的螢幕擷取畫面")](purchasing-non-consumable-products-images/image34.png#lightbox)   
   
   
   
 採購程序等同於可消耗的產品。主要的差異是在購買應用程式程式碼中的追蹤方式。 在購買按鈕才的本例可用如果已購買產品，否則按鈕會啟動功能本身。   
   
   
   

下圖顯示類別和執行非可使用產品購買的應用程式存放區伺服器之間的互動：   
   
   
   
 [![](purchasing-non-consumable-products-images/image35.png "類別和應用程式存放區伺服器來執行非可使用產品之間的互動購買")](purchasing-non-consumable-products-images/image35.png#lightbox)   
   
   
   
 您可以使用範例的主要差異是完成購買之後若要避免重新購買更新使用者介面。 在此範例中，成功的交易通知更新使用者介面，讓**購買**按鈕會轉換成會啟用此功能本身的按鈕。

## <a name="re-purchasing-non-consumable-products"></a>重新購買的非可使用產品

您的程式碼通常應該隱藏，或移作他用購買按鈕一旦產品已成功購買，若要防止使用者在嘗試一次購買該產品。 範例應用程式會藉由變更**購買**成可運作的範例相片篩選按鈕的按鈕。   
   
   
   
 有應用程式無法分辨是否已經購買的非可使用產品的情況：

-  如果應用程式會刪除並重新安裝在裝置上，所有的訂單記錄會消失 （除非 / 直到使用者執行的備份還原）。 
-  如果使用者有兩個 （或以上） 的裝置上安裝應用程式，並在其中一個裝置上進行採購。 其他裝置會繼續顯示可以購買產品。 
-  如果客戶嘗試重新購買非可使用產品在這些情況下，應用程式存放區都滿足一次免費產品。 若要執行購買一開始會顯示使用者介面 （比方說，就會出現確認警示和 Apple ID，將需要） 不過使用者然後會看到一個訊息，告知已購買產品。  
   
   
   
 在此案例中的程式碼路徑正是一般訂單相同，唯一的差異：

-  使用者不會不收費一次的產品。
-  `SKPaymentTransaction`物件傳遞至應用程式都有`OriginalTransaction`指的是交易一開始購買的產品時所產生的屬性。 
-  非可使用產品的銷售的應用程式也必須實作 StoreKit 的**還原**功能來幫助使用者擷取現有的購買項目。 
