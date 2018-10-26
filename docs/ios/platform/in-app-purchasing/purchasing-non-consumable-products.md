---
title: 購買非取用產品在 Xamarin.iOS 中
description: 本文件說明非取用產品 Xamarin.iOS，也就是使用者所購買的功能，隨時都可無限期，無論裝置。
ms.prod: xamarin
ms.assetid: 635D9CA2-6BCA-53E1-7B10-968029AA3493
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 060403baf8ac28b9b160632a01471b9828735069
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118496"
---
# <a name="purchasing-non-consumable-products-in-xamarinios"></a>購買非取用產品在 Xamarin.iOS 中

非取用產品 '' 所擁有的客戶。 在預期情況是，它們一定會存取它們，即使他們的裝置遺失/遭竊或購買新的。 它們是適用於書籍、 雜誌的問題、 遊戲的層級、 相片篩選條件，' pro 功能 ' 等。一旦使用者已購買非取用產品，它們永遠不需要支付一次。 如果您的程式碼不小心讓他們再試，StoreKit 會顯示已購買的訊息。

## <a name="non-consumable-products-sample"></a>非取用產品範例

[InAppPurchaseSample 程式碼](https://developer.xamarin.com/samples/monotouch/StoreKit/)包含名為專案*NonConsumables*。 程式碼範例示範如何實作非取用產品使用相片篩選器做為範例。 一旦您已購買篩選您可以不斷地將它套用到相片。 您永遠不需要重新購買它。   
   
   
   
 在購買程序會顯示在這一系列的螢幕擷取畫面-**購買**按鈕會變成功能啟用按鈕：   
   
   
   
 [![](purchasing-non-consumable-products-images/image34.png "在購買程序會顯示在這一系列的螢幕擷取畫面")](purchasing-non-consumable-products-images/image34.png#lightbox)   
   
   
   
 購買程序是等同於可取用產品中;主要的差別是在購買應用程式程式碼中的追蹤方式。 在此範例中 [購買] 按鈕才可用，如果已購買產品，否則按鈕啟動本身的功能。   
   
   
   

下圖顯示類別和執行非取用產品採購應用程式存放區伺服器之間的互動：   
   
   
   
 [![](purchasing-non-consumable-products-images/image35.png "類別與執行非取用產品的應用程式存放區伺服器之間的互動購買")](purchasing-non-consumable-products-images/image35.png#lightbox)   
   
   
   
 需求的可取用的範例中的主要差異是購買程序完成之後若要避免重新購買更新使用者介面。 在此範例中，成功的交易的通知會更新使用者介面，讓**購買**按鈕會轉換成一個按鈕，就會啟動本身的功能。

## <a name="re-purchasing-non-consumable-products"></a>重新購買非取用產品

您的程式碼通常應該隱藏或重新規劃 [購買] 按鈕，一旦產品已成功購買，以防止使用者嘗試再次購買產品。 範例應用程式的做法是變更**購買**成可運作的範例相片篩選器按鈕的按鈕。   
   
   
   
 有應用程式無法分辨是否已購買非取用產品的情況：

-  如果應用程式會刪除並重新安裝在裝置上，所有的購買記錄就會消失，（除非 / 直到使用者執行的備份還原）。 
-  如果使用者具有兩個 （含） 以上的裝置上安裝應用程式，並對其中一個裝置的購買。 其他裝置會繼續顯示可供購買的產品。 
-  如果客戶嘗試重新購買非取用產品在這些情況下，應用程式存放區會滿足一次免費的產品。 若要執行購買一開始會顯示使用者介面 （比方說，就會出現確認警示和 Apple ID 必須） 不過使用者則會看到訊息，告知他們已購買產品。  
   
   
   
 在此案例中的程式碼路徑正是以一般方式購買相同，唯一的差異：

-  使用者不會不會收費一次的產品。
-  `SKPaymentTransaction`物件傳遞至應用程式會有`OriginalTransaction`指的是交易一開始購買產品時所產生的屬性。 
-  銷售非取用產品的應用程式也必須實作的 StoreKit**還原**功能來幫助使用者擷取現有的購買項目。 
