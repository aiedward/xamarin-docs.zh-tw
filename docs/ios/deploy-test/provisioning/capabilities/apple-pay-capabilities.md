---
title: Xamarin.iOS 的 Apple Pay 功能
description: 為應用程式新增功能通常需要額外的佈建設定。 本指南說明 Apple Pay 功能所需的設定。
ms.prod: xamarin
ms.assetid: 735CC916-16A4-471B-87F7-0535E24288D7
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/15/2017
ms.openlocfilehash: 9d0ffbd0da99134dfae3e42fe8baf4111c80935f
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70762996"
---
# <a name="apple-pay-capabilities-in-xamarinios"></a>Xamarin.iOS 的 Apple Pay 功能

_為應用程式新增功能通常需要額外的佈建設定。本指南說明 Apple Pay 功能所需的設定。_

Apple Pay 可讓使用者透過其 iOS 裝置支付實體產品的費用。 本節說明如何在 Apple Developer Center (Apple 開發人員中心) 中建立 Apple Pay 所需的一切必要元件。

透過開發人員中心佈建新的應用程式時，需要進行三個步驟：

1. 建立 Merchant ID (商家識別碼)。
2. 建立具有 Apply Pay 功能的 App ID (應用程式識別碼)，然後在其中新增商家。
3. 為該 Merchant ID (商家識別碼) 產生憑證。

下列步驟將引導您完成建立上述項目的程序：

<a name="merchantid" />

## <a name="create-merchant-id"></a>建立商家識別碼

Merchant ID (商家識別碼) 可用來讓 Apple Pay 知道您可以接受付款，而且此識別碼會傳遞給 PassKit 的 `PaymentRequest` 方法並在 Apple Pay 權利中使用：

1. 瀏覽至 [Apple Developer Center](https://developer.apple.com/account/) \(Apple 開發人員中心\)，然後移至 [Certificates, Identifier, and Profiles] \(憑證、識別碼及設定檔\) 區段： 

    ![在 Developer Center (開發人員中心) 選取 [Merchant ID] \(商家識別碼\)](apple-pay-capabilities-images/image57.png)

2. 在 [Identifiers] \(識別碼\) 底下，選取 [Merchant IDs] \(商家識別碼\)，然後選取 [+] 來建立新的商家識別碼：  

3. 在表單中填入新的描述和識別碼，如下所示。 描述可方便您辨識識別碼，並可在稍後變更。 識別碼對您而言必須是唯一的，且開頭必須是字串  `merchant`。 Apple 建議識別碼採用下列格式：`merchant.com.[Your-App-Name]`:
   
    ![新的 Merchant ID (商家識別碼) 詳細資料](apple-pay-capabilities-images/image58.png)

4. 確認詳細資料，然後 [ **Register**]  \(註冊\) 您的識別碼： 
    
    ![Merchant ID (商家識別碼) 確認](apple-pay-capabilities-images/image59.png)

<a name="appid" />

## <a name="create-an-app-id-with-the-apple-pay-capability-that-includes-the-merchant-id"></a>建立具有 Apply Pay 功能且含有商家識別碼的應用程式識別碼

1. 在 [Developer Center](https://developer.apple.com/account/) \(開發人員中心\) 中，按一下 [Identifiers] \(識別碼\) 底下的 [App IDs] \(應用程式識別碼\)： 
    
    ![在 Developer Center (開發人員中心) 中選取 [App ID] \(應用程式識別碼\)](apple-pay-capabilities-images/image6.png)

2. 選取 [+] 按鈕來新增 App ID (應用程式識別碼)： 
   
    ![[Add new App ID] \(新增應用程式識別碼\) 按鈕](apple-pay-capabilities-images/image27.png)

3. 輸入 App ID (應用程式識別碼) 的 [Name] \(名稱\)，並為它提供 [Explicit App ID] \(明確的應用程式識別碼\)：    
   
    ![App ID (應用程式識別碼) 詳細資料畫面](apple-pay-capabilities-images/image35.png)

4. 在 [App Services] \(應用程式服務\) 底下，選取 [Apple Pay]：    
  
    ![App Services (應用程式服務) Apple Pay](apple-pay-capabilities-images/image36.png)

5. 選取 [Continue] \(繼續\)，然後選取 [Register] \(註冊\)。 請注意，在確認畫面上，Apple Pay 的 [Configurable] \(可設定\) 會顯示成已選取 (帶有黃色符號)： 
   
    ![Apple Pay 的確認畫面](apple-pay-capabilities-images/image37.png)

6. 返回 App ID (應用程式識別碼) 清單，然後選取您剛才建立的應用程式識別碼：  
   
    ![編輯 App ID (應用程式識別碼)](apple-pay-capabilities-images/image38.png)

7. 向下捲動到這個已展開區段的底部，然後按一下 [Edit] \(編輯\)。
8. 將清單向下捲動到 Apple Pay，然後按一下 [Edit] \(編輯\) 按鈕：  
    
    ![編輯 Apple Pay App ID (應用程式識別碼) 詳細資料](apple-pay-capabilities-images/image39.png)

9. 選取要與此 App ID (應用程式識別碼) 搭配使用的 Merchant ID (商家識別碼)，然後按一下 [Continue] \(繼續\)：  
    
    ![選取要用於 App ID (應用程式識別碼) 的 Merchant ID (商家識別碼)](apple-pay-capabilities-images/image40.png)

10. 確認 Merchant ID (商家識別碼) 指派項目，然後按 [Assign] \(指派\)：  
    
    ![確認畫面](apple-pay-capabilities-images/image41.png)

此 App ID (應用程式識別碼) 現在已可用來產生 (或重新產生) 新的佈建設定檔，如[使用功能](~/ios/deploy-test/provisioning/capabilities/index.md)指南所述。 

<a name="certificate" />

## <a name="create-a-certificate-for-your-merchant-id"></a>為您的商家識別碼產生憑證

Apple 需要使用憑證來加密與交易關聯的敏感性資料。 每個建立的 Merchant ID (商家識別碼) 都必須有自己的憑證。 

若要建立憑證，請依照下列步驟進行操作：

1. 選取上述建立的 Merchant ID (商家識別碼)，然後按 [Edit] \(編輯\)： 
    
    ![編輯 Merchant ID (商家識別碼) 對話方塊](apple-pay-capabilities-images/image42.png)

2. 在 [iOS Merchant ID Settings] \(iOS 商家識別碼設定\) 畫面上，按一下 [Create Certificate] \(建立憑證\)： 
   
    ![建立付款處理憑證](apple-pay-capabilities-images/image43.png)

3. 回答以下問題： 

    ![處理付款是否將只在中國處理的問題](apple-pay-capabilities-images/image44.png)

4. 此時，系統會提示您建立一個 _Certificate Signing Request_ \(憑證簽署要求\)： 

    ![建立 Certificate Signing Request \(憑證簽署要求\)](apple-pay-capabilities-images/image45.png)
    
    > [!IMPORTANT]
    > 如果您使用 Apple Pay 的付款提供者 (例如 JudoPay 或 Stripe)，它們可以提供一個格式正確的 CSR，供您此時使用。 如需有關要求此 CSR 的資訊，請參閱 [JudoPay](https://www.judopay.com/docs/version-52/apple-pay/getting-started/#create-an-apple-pay-certificate) \(英文\) 和 [Stripe](https://stripe.com/docs/apple-pay/apps#csr) \(英文\) 網站。 若要建立您自己的 CSR，請依照下方的步驟 5 到 8。 建立 CSR 之後，請前往步驟 9。

5. 開啟「鑰匙圈存取」應用程式，然後瀏覽至 [鑰匙圈存取] > [憑證輔助程式] > [從憑證授權要求憑證]： 

     ![使用 Mac 上的鑰匙圈來建立 CSR](apple-pay-capabilities-images/image46.png)

6. 輸入您的電子郵件地址、輸入私密金鑰的名稱、將 [CA 電子郵件地址] 保留空白、選取 [儲存到磁碟] 選項，然後選取 [指定密鑰配對資訊]：

     ![[憑證資訊] 對話方塊](apple-pay-capabilities-images/image47.png)

7. 將 CSR 儲存到便利的位置： 

     ![將 CSR 儲存到本機電腦](apple-pay-capabilities-images/image48.png)

8. 在 [密鑰配對] 資訊畫面中，將 [密鑰大小] 設定為 **256 bit**，將 [驗算法] 設定為 **ECC**，然後按一下 [繼續]：

     ![輸入密鑰配對資訊對話方塊](apple-pay-capabilities-images/image49.png)

9. 在 Developer Center (開發人員中心) 上，按一下 [Continue] \(繼續\) 以上傳 CSR： 

     ![準備將 CSR 上傳到開發人員中心](apple-pay-capabilities-images/image50.png)

10. 按一下 [選取檔案...] 以選取 CSR，然後按 [Continue] \(繼續\) 以將其上傳到開發人員入口網站： 

     ![將 CSR 上傳到開發人員中心](apple-pay-capabilities-images/image51.png)

11. 產生憑證之後，下載憑證並按兩下以將其安裝到您的 Keychain。

如需有關使用 Apple Pay 的詳細資訊，請參閱下列指南：

* [Apple Pay 簡介](~/ios/platform/apple-pay.md)

## <a name="next-steps"></a>後續步驟

以下清單說明可能需要進行的額外步驟：

* 在您的應用程式中使用架構命名空間。
* 為您的應用程式新增必要的權利。 如需有關所需權利及如何新增這些權利的詳細資訊，請參閱[使用權利](~/ios/deploy-test/provisioning/entitlements.md)指南。
* 在應用程式的 [ **iOS 套件組合簽署**] 中，確定 [ **自訂權利**] 已設定為 **Entitlements.plist**。 這「不是」 __  「偵錯」和「iOS 模擬器」組建的預設設定。

如果您在應用程式服務方面遇到問題，請參閱主要指南的[疑難排解](~/ios/deploy-test/provisioning/capabilities/index.md)一節。
