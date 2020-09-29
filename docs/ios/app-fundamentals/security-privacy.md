---
title: iOS 安全性和隱私權功能
description: 本檔說明 iOS 的安全性和隱私權功能，並討論如何搭配使用這些功能與 Xamarin. iOS。 它會查看在 iOS 10 中所做的更新，以及如何存取私用使用者資料。
ms.prod: xamarin
ms.assetid: 718C8721-C359-4650-878A-D68E159A3F53
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: f665d2de767721cd2f5eedd45653e36c01395569
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435284"
---
# <a name="ios-security-and-privacy-features"></a>iOS 安全性和隱私權功能

_本文涵蓋如何使用 iOS 中的安全性和隱私權，以及它們如何影響 Xamarin iOS 應用程式。_

Apple 針對 iOS 10 中的安全性和隱私權進行了許多增強 (和更高的) ，可協助開發人員改善其應用程式的安全性，並確保使用者的隱私權。 本文將討論如何在 Xamarin iOS 應用程式中執行這些功能。

<a name="General-Enhancements"></a>

## <a name="general-enhancements"></a>一般增強功能

IOS 10 中的安全性和隱私權已進行下列一般變更：

- Common Data Security 架構 (CDSA) API 已淘汰，應取代為 SecKey API 來產生非對稱金鑰。
- 新的 `NSAllowsArbitraryLoadsInWebContent` 金鑰可以加入至應用程式的 **Info. plist** 檔案，並可讓網頁正確載入，而 Apple TRANSPORT Security (ATS) 保護仍會針對其餘的應用程式啟用。 如需詳細資訊，請參閱我們的 [應用程式傳輸安全性](~/ios/app-fundamentals/ats.md) 檔。
- 因為 iOS 10 和 macOS Sierra 中的新剪貼簿可讓使用者在裝置之間進行複製和貼上，所以已擴充 API 來允許將剪貼簿限制在特定裝置，並在指定的時間點自動清除時間戳記。 此外，名為 pasteboards 的不會再保存，且應取代為共用的夾夾容器。
- 針對所有的 SSL/TLS 連線，現在預設會停用 RC4 對稱式加密。 此外，安全傳輸 API 已不再支援 SSLv3，建議開發人員儘快停止使用 SHA-1 和3DES 密碼編譯。

<a name="Accessing-Private-User-Data"></a>

## <a name="accessing-private-user-data"></a>存取私用使用者資料

在 iOS 10 (或更新版本上執行的應用程式) 必須在其 **資訊 plist** 檔案中輸入一或多個隱私權金鑰，以靜態方式宣告其存取特定功能或使用者資訊的意圖。

> [!IMPORTANT]
> 當應用程式嘗試存取其中一項受限制的功能或使用者資訊時，系統會以無訊息方式終止這些應用程式， _而不會發生錯誤_！ 如果應用程式在 iOS 10 上意外啟動失敗，請確定已指定所有必要的 **資訊。 plist** 。

以下是可用的隱私權相關金鑰：

- **隱私權-Apple Music 使用方式描述** (`NSAppleMusicUsageDescription`) -可讓開發人員描述應用程式想要存取使用者之媒體櫃的原因。
- **隱私權-藍牙周邊使用方式描述** (`NSBluetoothPeripheralUsageDescription`) -可讓開發人員描述應用程式要在使用者裝置上存取藍牙的原因。
- **隱私權-行事曆使用方式描述** (`NSCalendarsUsageDescription`) -可讓開發人員描述應用程式想要存取使用者行事曆的原因。
-  () 的 [**隱私權-相機使用方式描述**] `NSCameraUsageDescription` -可讓開發人員描述應用程式想要存取裝置相機的原因。
- **隱私權-連絡人使用方式描述** (`NSContactsUsageDescription`) -可讓開發人員描述應用程式想要存取使用者連絡人的原因。
- **隱私權-健全狀況共用使用方式描述** (`NSHealthShareUsageDescription`) -可讓開發人員描述應用程式要如何存取使用者的健康情況資料。 如需詳細資訊，請參閱 Apple 的 [HKHealthStore 類別參考](https://developer.apple.com/reference/healthkit/hkhealthstore)。
- **隱私權-健全狀況更新使用方式描述** (`NSHealthUpdateUsageDescription`) -可讓開發人員描述應用程式要編輯使用者的健康情況資料的原因。 如需詳細資訊，請參閱 Apple 的 [HKHealthStore 類別參考](https://developer.apple.com/reference/healthkit/hkhealthstore)。
- **隱私權-HomeKit 使用方式描述** (`NSHomeKitUsageDescription`) -可讓開發人員描述應用程式想要存取使用者 HomeKit 設定資料的原因。
- **隱私權-位置一律使用描述** (`NSLocationAlwaysUsageDescription`) -可讓開發人員描述應用程式希望一律存取使用者位置的原因。
- 否決 **隱私權-位置使用方式描述** (`NSLocationUsageDescription`) -可讓開發人員描述應用程式想要存取使用者位置的原因。 *注意：此金鑰在 iOS 8 中已被取代 (和更高的) 。請改用 `NSLocationAlwaysUsageDescription` 或 `NSLocationWhenInUseUsageDescription` 。*
- **隱私權-使用中的使用描述** (`NSLocationWhenInUseUsageDescription`) -可讓開發人員描述應用程式在執行時要存取使用者位置的原因。
- 否決 **隱私權-媒體程式庫使用方式描述** -可讓開發人員描述應用程式想要存取使用者媒體媒體櫃的原因。 *注意：此金鑰在 iOS 8 中已被取代 (和更高的) 。請改用 `NSAppleMusicUsageDescription` 。*
- **隱私權-麥克風使用方式描述** (`NSMicrophoneUsageDescription`) -可讓開發人員描述應用程式想要存取裝置麥克風的原因。
- **隱私權-動作使用方式描述** (`NSMotionUsageDescription`) -可讓開發人員描述應用程式想要存取裝置的加速計的原因。
- **隱私權-相片庫使用方式描述** (`NSPhotoLibraryUsageDescription`) -可讓開發人員描述應用程式想要存取使用者相片圖庫的原因。
- **隱私權-提醒使用說明** (`NSRemindersUsageDescription`) -可讓開發人員描述應用程式想要存取使用者提醒的原因。
- **隱私權-Siri 使用方式描述** (`NSSiriUsageDescription`) -可讓開發人員描述應用程式要將使用者資料傳送至 Siri 的原因。
- **隱私權-語音辨識使用方式描述** (`NSSpeechRecognitionUsageDescription`) -可讓開發人員描述應用程式想要將使用者資料傳送給 Apple 的語音辨識伺服器的原因。
- **隱私權-電視提供者使用方式描述** (`NSVideoSubscriberAccountUsageDescription`) -可讓開發人員描述應用程式要如何存取使用者的電視提供者帳戶。

如需使用 **資訊. plist** 金鑰的詳細資訊，請參閱 Apple 的 [資訊屬性清單索引鍵參考](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Introduction/Introduction.html#//apple_ref/doc/uid/TP40009248-SW1)。

<a name="Setting-Privacy-Keys"></a>

## <a name="setting-privacy-keys"></a>設定隱私權金鑰

下列範例會在 iOS 10 (和更高的) 上存取 HomeKit，開發人員必須將金鑰新增 `NSHomeKitUsageDescription` 至應用程式的 **Info plist** 檔案，並提供字串來宣告應用程式要存取使用者的 HomeKit 資料庫的原因。 第一次執行應用程式時，將會向使用者顯示這個字串：

![NSHomeKitUsageDescription 警示範例](security-privacy-images/info01.png "NSHomeKitUsageDescription 警示範例")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Visual Studio 目前不支援在預設的 iOS 資訊清單編輯器中編輯 **資訊 plist** 隱私權金鑰。 相反地，您將需要使用一般 PList 編輯器，請執行下列動作：

1. 以滑鼠右鍵按一下**方案總管**中的**plist**檔案，然後選取 [**開啟檔案**...]。
2. 從程式清單中選取 [ **一般 PList 編輯器** ] 以開啟檔案，然後按一下 **[確定]**。

    ![選取 [一般 PList 編輯器]](security-privacy-images/InfoEditorSelectionVs.png "選取 [一般 PList 編輯器]")
3. 在 **+** 編輯器中，按一下最後一個資料列上的按鈕，將新的專案加入至清單中。 這將稱為「自訂屬性」，其類型設為 `String` 和空白值。
4. 按一下屬性名稱，將會出現下拉式清單。
5. 從下拉式清單中，選取隱私權金鑰 (例如 **HomeKit 使用描述**) ： 

    ![選取隱私權金鑰](security-privacy-images/InfoPListEditorSelectKey.png "選取隱私權金鑰")
6. 在 [值] 欄中輸入描述，以瞭解應用程式想要存取特定功能或使用者資訊的原因： 

    ![輸入描述](security-privacy-images/InfoPListSetValue.png "輸入描述")
7. 將變更儲存至檔案。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

若要設定任何隱私權金鑰，請執行下列動作：

1. 在 [方案總管]**** 中，按兩下 [Info.plist]**** 檔案以開啟它進行編輯。
2. 在畫面底部，切換至 [ **來源** ] view。
3. 將新 **專案** 新增至清單。
4. 從下拉式清單中，選取隱私權金鑰 (例如 **HomeKit 使用描述**) ： 

    ![選取隱私權金鑰](security-privacy-images/info02.png "選取隱私權金鑰")
5. 輸入應用程式想要存取指定功能或使用者資訊之原因的描述： 

    ![輸入描述](security-privacy-images/info03.png "輸入描述")
6. 將變更儲存至檔案。

-----

> [!IMPORTANT]
> 在上述範例中，無法 `NSHomeKitUsageDescription` 在 **plist** 檔案中設定索引鍵，會導致應用程式以無訊息方式 _失敗_ (由系統在執行時間關閉) 在 iOS 10 (或更高的) 中執行時，不會發生錯誤。

<a name="Summary"></a>

## <a name="summary"></a>摘要

本文涵蓋了 Apple 在 iOS 10 中所做的安全性和隱私權變更，以及它們如何影響 Xamarin iOS 應用程式。

## <a name="related-links"></a>相關連結

- [iOS 10 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS10)