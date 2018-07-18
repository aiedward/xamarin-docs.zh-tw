---
title: iOS 的安全性和隱私權功能
description: 這份文件描述的安全性和隱私權功能 iOS，並討論如何使用它們搭配 Xamarin.iOS。 它探討在 iOS 10 及如何存取私用的使用者資料所做的更新。
ms.prod: xamarin
ms.assetid: 718C8721-C359-4650-878A-D68E159A3F53
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 1a28bf394d29c09bfd264f03e0eea6c8b582f271
ms.sourcegitcommit: ec50c626613f2f9af51a9f4a52781129bcbf3fcb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2018
ms.locfileid: "37854749"
---
# <a name="ios-security-and-privacy-features"></a>iOS 的安全性和隱私權功能

_本文涵蓋使用 iOS 和它們如何影響 Xamarin.iOS 應用程式的安全性和隱私權。_

Apple 進行了數個增強功能的安全性和隱私權，在 iOS 10 （和更新版本） 可協助開發人員改進其應用程式的安全性，並確保使用者的隱私權。 這篇文章將涵蓋在 Xamarin.iOS 應用程式中實作這些功能。
    
<a name="General-Enhancements" />

## <a name="general-enhancements"></a>一般增強功能

下列的一般變更進行了安全性和隱私權在 iOS 10:

- 常見的資料安全性架構 (CDSA) API 已被取代，應以 SecKey API，來產生非對稱金鑰來取代。
- 新`NSAllowsArbitraryLoadsInWebContent`可以將金鑰新增至應用程式**Info.plist**檔案，並可讓應用程式的其餘部分仍啟用 Apple Transport Security (ATS) 保護而正確載入的網頁。 如需詳細資訊，請參閱我們[應用程式的傳輸安全性](~/ios/app-fundamentals/ats.md)文件。
- 因為新的剪貼簿，在 iOS 10 和 macOS Sierra 允許複製並貼上裝置之間的使用者，所以 API 已擴充到允許的剪貼簿，受限於特定的裝置，並會自動清除在指定的時間點的時間戳記。 此外，具名的 pasteboards 不會再保存，並應該取代成共用剪貼板容器。
- 對於所有 SSL/TLS 連線，RC4 對稱編碼器現在預設會停用。 此外，安全傳輸 API 不再支援 SSLv3，建議開發人員停止儘速使用 sha-1 和 3DES 加密。

<a name="Accessing-Private-User-Data" />

## <a name="accessing-private-user-data"></a>存取私人使用者資料

在 iOS 10 （或更新版本） 上執行的應用程式必須以靜態方式宣告輸入中的一或多個隱私金鑰來存取特定功能或使用者資訊的意圖他們**Info.plist**說明為什麼應用程式想要取得使用者的檔案存取權。

> [!IMPORTANT]
> 無法提供必要的金鑰將會以無訊息方式終止由系統在嘗試存取受限制的功能或使用者的詳細資訊，其中之一時的應用程式_無誤_！ 如果應用程式啟動時意外失敗的 ios 10，確保所有必要**Info.plist**尚未指定。

下列隱私權相關的索引鍵是可用：

- **隱私權-Apple Music 使用方式描述**(`NSAppleMusicUsageDescription`)-可讓開發人員說明為什麼應用程式想要存取使用者的媒體櫃。
- **隱私權-藍牙周邊的使用方式描述**(`NSBluetoothPeripheralUsageDescription`)-可讓開發人員描述應用程式想要存取使用者的裝置上的藍芽的原因。
- **隱私權-行事曆使用方式描述**(`NSCalendarsUsageDescription`)-可讓開發人員說明為什麼應用程式想要存取使用者的行事曆。
- **隱私權-相機使用方式描述**(`NSCameraUsageDescription`)-可讓開發人員說明為什麼應用程式想要存取裝置的相機。
- **隱私權-連絡人使用方式描述**(`NSContactsUsageDescription`)-可讓開發人員說明為什麼應用程式想要存取使用者的連絡人。
- **隱私權-健康共用使用方式描述**(`NSHealthShareUsageDescription`)-可讓開發人員說明為什麼應用程式想要存取使用者的健康情況資料。 如需詳細資訊，請參閱 Apple [HKHealthStore 類別參考](https://developer.apple.com/reference/healthkit/hkhealthstore)。
- **隱私權-健康更新使用方式描述**(`NSHealthUpdateUsageDescription`)-可讓開發人員說明為什麼應用程式想要編輯使用者的健康情況資料。 如需詳細資訊，請參閱 Apple [HKHealthStore 類別參考](https://developer.apple.com/reference/healthkit/hkhealthstore)。
- **隱私權-HomeKit 使用方式描述**(`NSHomeKitUsageDescription`)-可讓開發人員說明為什麼應用程式想要存取使用者的 HomeKit 組態資料。
- **隱私權-位置一律使用方式描述**(`NSLocationAlwaysUsageDescription`)-可讓開發人員說明為什麼應用程式想要一律可以存取使用者的位置。
- [取代]**隱私權-位置使用方式描述**(`NSLocationUsageDescription`)-可讓開發人員說明為什麼應用程式想要存取使用者的位置。 *注意： 在 iOS 8 （和更新版本） 中已被取代此機碼。使用`NSLocationAlwaysUsageDescription`或`NSLocationWhenInUseUsageDescription`改。*
- **隱私權-位置時在 Use Usage Description** (`NSLocationWhenInUseUsageDescription`)-可讓開發人員說明為什麼應用程式想要執行時存取使用者的位置。
- [取代]**隱私權-媒體程式庫使用方式描述**-可讓開發人員描述應用程式想要存取使用者的媒體櫃的原因。 *注意： 在 iOS 8 （和更新版本） 中已被取代此機碼。使用`NSAppleMusicUsageDescription`改。*
- **隱私權-麥克風使用方式描述**(`NSMicrophoneUsageDescription`)-可讓開發人員說明為什麼應用程式想要存取裝置的麥克風。
- **隱私權-運動使用方式描述**(`NSMotionUsageDescription`)-可讓開發人員說明為什麼應用程式想要存取裝置的加速計。
- **隱私權-照片圖庫使用方式描述**(`NSPhotoLibraryUsageDescription`)-可讓開發人員說明為什麼應用程式想要存取使用者的相片媒體櫃。
- **隱私權-提醒使用方式描述**(`NSRemindersUsageDescription`)-可讓開發人員說明為什麼應用程式想要存取使用者的提醒。
- **隱私權-Siri 使用方式描述**(`NSSiriUsageDescription`)-可讓開發人員說明為什麼應用程式想要將使用者資料傳送至 Siri。
- **隱私權-語音辨識使用方式描述**(`NSSpeechRecognitionUsageDescription`)-可讓開發人員說明為什麼應用程式想要將使用者資料傳送至 Apple 的語音辨識伺服器。
- **隱私權-電視業者使用方式描述**(`NSVideoSubscriberAccountUsageDescription`)-可讓開發人員說明為什麼應用程式想要存取使用者的電視提供者帳戶。

如需有關使用**Info.plist**機碼，請參閱 Apple[資訊屬性清單索引鍵參考](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Introduction/Introduction.html#//apple_ref/doc/uid/TP40009248-SW1)。

<a name="Setting-Privacy-Keys" />

## <a name="setting-privacy-keys"></a>設定隱私金鑰

需要存取 HomeKit iOS 10 （和更新版本） 上的下列範例中，開發人員必須將`NSHomeKitUsageDescription`應用程式的關鍵**Info.plist**檔案，並提供應用程式為何想要存取使用者的 HomeKit 字串宣告資料庫。 若要在使用者第一次執行應用程式，將會看到此字串：

![範例 NSHomeKitUsageDescription 警示](security-privacy-images/info01.png "範例 NSHomeKitUsageDescription 警示")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

目前 Xamarin.iOS for Visual Studio 不支援編輯**Info.plist**預設 iOS 內的隱私金鑰從資訊清單編輯器。 您必須改為使用一般的 PList 編輯器中，因此執行下列動作：

1. 以滑鼠右鍵按一下**Info.plist**中的檔案**方案總管**，然後選取**開啟...**.
2. 選取 **泛型 PList 編輯器**從清單中的程式來開啟檔案，然後按一下**確定**。

    ![選取 泛型 PList 編輯器](security-privacy-images/InfoEditorSelectionVs.png "選取泛型 PList 編輯器")
3. 按一下  **+** 最後一個資料列，在編輯器中，將新的項目新增到清單上的按鈕。 這會呼叫 「 自訂屬性 」，將類型設定為使用`String`和空值。
4. 按一下 屬性名稱，並會出現在下拉式清單。
5. 從下拉式清單中，選取 隱私金鑰 (例如**隱私權-HomeKit 使用方式描述**): 

    ![選取 隱私金鑰](security-privacy-images/InfoPListEditorSelectKey.png "選取隱私金鑰")
6. 輸入 [值] 資料行的描述應用程式想要存取指定的功能或使用者資訊的原因： 

    ![輸入的描述](security-privacy-images/InfoPListSetValue.png "輸入的描述")
7. 將變更儲存到檔案。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

若要設定任何隱私機碼，執行下列作業：

1. 在 [方案總管] 中，按兩下 [Info.plist] 檔案以開啟它進行編輯。
2. 在畫面底部，切換至**來源**檢視。
3. 加入新**項目**至清單。
4. 從下拉式清單中，選取 隱私金鑰 (例如**隱私權-HomeKit 使用方式描述**): 

    ![選取 隱私金鑰](security-privacy-images/info02.png "選取隱私金鑰")
5. 輸入應用程式為何想要存取指定的功能或使用者資訊的描述： 

    ![輸入的描述](security-privacy-images/info03.png "輸入的描述")
6. 將變更儲存到檔案。

-----

> [!IMPORTANT]
> 在範例中指定上述設定的失敗`NSHomeKitUsageDescription`中的索引鍵**Info.plist**檔案會導致應用程式_以無訊息方式失敗_（正在關閉系統在執行階段） 而不需要在 iOS 10 （中執行時的錯誤或更新版本）。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已涵蓋的安全性和隱私權 Apple 已在 iOS 10 和它們如何影響 Xamarin.iOS 應用程式中的變更。

## <a name="related-links"></a>相關連結

- [iOS 10 範例](https://developer.xamarin.com/samples/ios/iOS10/)
