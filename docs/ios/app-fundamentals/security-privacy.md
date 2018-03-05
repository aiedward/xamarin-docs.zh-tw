---
title: "iOS 的安全性和隱私權功能"
description: "本文涵蓋的 iOS 和它們如何影響 Xamarin.iOS 應用程式的安全性和隱私權的工作。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 718C8721-C359-4650-878A-D68E159A3F53
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: acdcdc2b76a995ca324532c6a034b2fdf8e21db5
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="ios-security-and-privacy-features"></a>iOS 的安全性和隱私權功能

_本文涵蓋的 iOS 和它們如何影響 Xamarin.iOS 應用程式的安全性和隱私權的工作。_

Apple 已進行數個增強功能的安全性和隱私權中 iOS 10 （和大於） 可協助開發人員改善其應用程式的安全性，並確保使用者的隱私權。 本文將討論 Xamarin.iOS 應用程式中實作這些功能。

將會詳細介紹下列主題：

- [一般增強功能](#General-Enhancements)
- [存取私用使用者資料](#Accessing-Private-User-Data)
    - [設定隱私權索引鍵](#Setting-Privacy-Keys)
    
<a name="General-Enhancements" />

## <a name="general-enhancements"></a>一般增強功能

下列的一般變更 iOS 10 中已經出現安全性和隱私權：

- 常見資料安全性架構 (CDSA) 應用程式開發介面已被取代，應該使用 SecKey API 來產生非對稱金鑰來取代。
- 新`NSAllowsArbitraryLoadsInWebContent`索引鍵可以將加入至應用程式的`Info.plist`檔案，並可讓網頁應用程式的其餘部分仍啟用 Apple 傳輸安全性 (ATS) 保護時正確載入。 如需詳細資訊，請參閱我們[應用程式的傳輸安全性](~/ios/app-fundamentals/ats.md)文件。
- 因為新的剪貼簿中 iOS 10 及 macOS 利也可讓使用者複製和貼上裝置之間，已擴充 API，以允許剪貼簿限制為特定的裝置，並加上時間戳記會自動清除特定時點。 此外，具名的 pasteboards 將不再保存，而且應該取代共用剪貼版容器。
- 對於所有 SSL/TLS 連線，RC4 對稱式加密現在預設會停用。 此外，安全傳輸 API 不再支援 SSLv3 與建議開發人員停止儘速使用 sha-1 和 3DES 加密。

<a name="Accessing-Private-User-Data" />

## <a name="accessing-private-user-data"></a>存取私用使用者資料

10 （或更新版本） 在 iOS 上執行的應用程式必須以靜態方式宣告其輸入中的一個或多個隱私權索引鍵來存取特定功能或使用者資訊的意圖其`Info.plist`解釋為什麼應用程式想要存取的使用者的檔案。

> [!IMPORTANT]
> **請注意**提供必要的金鑰將會以無訊息方式終止系統當他們嘗試存取其中一個受限制的功能或使用者資訊時失敗的應用程式_無誤_！ 如果應用程式啟動時意外失敗 10 在 iOS 上，確保所有必要`Info.plist`尚未指定。

下列隱私權相關的索引鍵是可用：

- **隱私權-Apple 音樂使用量描述**(`NSAppleMusicUsageDescription`)-可讓開發人員描述應用程式想要存取使用者的媒體櫃的原因。
- **隱私權-藍芽週邊設備的使用方式描述**(`NSBluetoothPeripheralUsageDescription`)-可讓開發人員描述應用程式想要存取使用者的裝置上的藍芽的原因。
- **隱私權-行事曆使用方式描述**(`NSCalendarsUsageDescription`)-可讓開發人員描述應用程式想要存取使用者的行事曆的原因。
- **隱私權-相機使用方式描述**(`NSCameraUsageDescription`)-可讓開發人員，說明為什麼應用程式想要存取裝置的相機。
- **隱私權-連絡人使用方式描述**(`NSContactsUsageDescription`)-可讓開發人員描述應用程式想要存取 使用者連絡人的原因。
- **隱私權-健全狀況共用使用方式描述**(`NSHealthShareUsageDescription`)-可讓開發人員，說明為什麼應用程式想要存取使用者的健全狀況資料。 如需詳細資訊，請參閱 Apple [HKHealthStore 類別參考](https://developer.apple.com/reference/healthkit/hkhealthstore)。
- **隱私權-健全狀況更新使用方式描述**(`NSHealthUpdateUsageDescription`)-可讓開發人員，說明為什麼應用程式想要編輯使用者的健全狀況資料。 如需詳細資訊，請參閱 Apple [HKHealthStore 類別參考](https://developer.apple.com/reference/healthkit/hkhealthstore)。
- **隱私權-HomeKit 使用方式描述**(`NSHomeKitUsageDescription`)-可讓開發人員描述應用程式想要存取使用者的 HomeKit 組態資料的原因。
- **隱私權-位置一律使用方式描述**(`NSLocationAlwaysUsageDescription`)-可讓開發人員說明為什麼應用程式想要一律可以存取使用者的位置。
- [Deprecated]**隱私權-位置使用方式描述**(`NSLocationUsageDescription`)-可讓開發人員，說明為什麼應用程式想要存取使用者位置。 *注意： 此機碼已被取代在 iOS 8 （和更高）。使用`NSLocationAlwaysUsageDescription`或`NSLocationWhenInUseUsageDescription`改為。*
- **隱私權-位置時在使用使用方式描述**(`NSLocationWhenInUseUsageDescription`)-可讓開發人員，說明為什麼應用程式想要在執行時存取使用者的位置。
- [Deprecated]**隱私權-媒體櫃的使用方式描述**-可讓開發人員描述應用程式想要存取使用者的媒體櫃的原因。 *注意： 此機碼已被取代在 iOS 8 （和更高）。使用`NSAppleMusicUsageDescription`改為。*
- **隱私權-麥克風的使用方式描述**(`NSMicrophoneUsageDescription`)-可讓開發人員描述應用程式想要存取裝置麥克風的原因。
- **隱私權-影片使用方式描述**(`NSMotionUsageDescription`)-可讓開發人員，說明為什麼應用程式想要存取裝置的加速計。
- **隱私權-相片程式庫的使用方式描述**(`NSPhotoLibraryUsageDescription`)-可讓開發人員描述應用程式想要存取使用者的相片程式庫的原因。
- **隱私權-提醒使用方式描述**(`NSRemindersUsageDescription`)-可讓開發人員說明為什麼應用程式想要存取使用者的備忘提醒。
- **隱私權-Siri 使用方式描述**(`NSSiriUsageDescription`)-可讓開發人員說明為什麼應用程式想要將使用者資料傳送至使用 Siri。
- **隱私權-語音辨識使用方式描述**(`NSSpeechRecognitionUsageDescription`)-可讓開發人員，說明為什麼應用程式想要將使用者資料傳送至 Apple 的語音辨識伺服器。
- **隱私權-電視提供者的使用方式描述**(`NSVideoSubscriberAccountUsageDescription`)-可讓開發人員描述應用程式想要存取使用者的電視提供者帳戶的原因。

如需有關使用`Info.plist`金鑰，請參閱 Apple[資訊屬性清單索引鍵參考](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Introduction/Introduction.html#//apple_ref/doc/uid/TP40009248-SW1)。

<a name="Setting-Privacy-Keys" />

## <a name="setting-privacy-keys"></a>設定隱私權索引鍵

下列範例會在 10 （和大於） iOS 上存取 HomeKit，開發人員必須將`NSHomeKitUsageDescription`應用程式的金鑰`Info.plist`檔案，並提供字串，宣告應用程式想要存取使用者的 HomeKit 資料庫的原因。 這個字串會顯示在使用者第一次執行應用程式：

[ ![](security-privacy-images/info01.png "範例 NSHomeKitUsageDescription 警示")](security-privacy-images/info01.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Xamarin.iOS for Visual Studio 目前不支援編輯的安全性增強功能`Info.plist`設定從 ide，因此將需要下列因應措施：

1. 開啟`Info.plist`外部文字編輯器中的檔案。
2. 最後一個以外`</dict>` 節點，加入下列節點： `<key>NSHomeKitUsageDescription</key>`
3. 加入下列節點以提供必要的描述： `<string>Allows the app to control HomeKit enabled devices.</string>`
4. `Info.plist`檔案應該看起來如下所示： 

    [ ![](security-privacy-images/info02vs.png "Info.plist 檔案應該如下所示")](security-privacy-images/info02vs.png)
4. 將變更儲存到檔案。
5. 返回 Visual Studio 並重新編譯的應用程式。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

若要設定的任何隱私金鑰，執行下列作業：

1. 按兩下`Info.plist`檔案**方案總管 中**開啟進行編輯。
2. 在畫面底部，切換至**來源**檢視。
3. 加入新**項目**至清單。
4. 從下拉式清單中，選取 隱私金鑰 (例如**隱私權-HomeKit 使用方式描述**): 

    [ ![](security-privacy-images/info02.png "選取隱私金鑰")](security-privacy-images/info02.png)
5. 輸入應用程式想要存取指定的功能或使用者資訊的原因的描述： 

    [ ![](security-privacy-images/info03.png "輸入的描述")](security-privacy-images/info03.png)
6. 將變更儲存到檔案。

-----

> [!IMPORTANT]
> **注意：**在範例中指定以上版本，無法設定`NSHomeKitUsageDescription`中的索引鍵`Info.plist`檔案會導致應用程式_以無訊息模式失敗_（正在關閉由系統在執行階段） 不會發生錯誤時 iOS 10 中執行 （或更高）。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已涵蓋的安全性和隱私權變更 Apple 所做的 iOS 10 和它們如何影響 Xamarin.iOS 應用程式。



## <a name="related-links"></a>相關連結

- [iOS 10 範例](https://developer.xamarin.com/samples/ios/iOS10/)
