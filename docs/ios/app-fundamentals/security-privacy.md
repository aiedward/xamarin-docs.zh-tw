---
title: iOS 安全性和隱私權功能
description: 本檔說明 iOS 的安全性和隱私權功能, 並討論如何搭配使用它們與 Xamarin。 它會查看在 iOS 10 中進行的更新, 以及如何存取私用使用者資料。
ms.prod: xamarin
ms.assetid: 718C8721-C359-4650-878A-D68E159A3F53
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 5c818cac3b26e94710a64938a80690b8d4946320
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/31/2019
ms.locfileid: "70200209"
---
# <a name="ios-security-and-privacy-features"></a>iOS 安全性和隱私權功能

_本文涵蓋如何使用 iOS 中的安全性和隱私權, 以及它們如何影響 Xamarin iOS 應用程式。_

Apple 在 iOS 10 (及更新版本) 中提供了許多安全性和隱私權方面的增強功能, 可協助開發人員改善其應用程式的安全性, 並確保使用者的隱私權。 本文將討論如何在 Xamarin iOS 應用程式中執行這些功能。

<a name="General-Enhancements" />

## <a name="general-enhancements"></a>一般增強功能

IOS 10 中的安全性和隱私權已進行下列一般變更:

- Common Data Security 架構 (CDSA) API 已被取代, 應取代為 SecKey API 以產生非對稱金鑰。
- 新`NSAllowsArbitraryLoadsInWebContent`的金鑰可以新增至應用程式的**plist**檔案, 並允許在其他應用程式的 Apple Transport Security (ATS) 保護仍然啟用時, 正確載入網頁。 如需詳細資訊, 請參閱我們的[應用程式傳輸安全性](~/ios/app-fundamentals/ats.md)檔。
- 由於 iOS 10 和 macOS Sierra 中的新剪貼簿可讓使用者在裝置之間進行複製和貼上, 因此 API 已擴充為允許將剪貼簿限制為特定裝置, 並在指定的時間點自動將其加上時間戳記。 此外, 命名為 pasteboards 不再保存, 且應取代為共用的存放夾容器。
- 針對所有 SSL/TLS 連線, 現在預設會停用 RC4 對稱式加密。 此外, 安全傳輸 API 已不再支援 SSLv3, 建議開發人員儘快停止使用 SHA-1 和3DES 密碼編譯。

<a name="Accessing-Private-User-Data" />

## <a name="accessing-private-user-data"></a>存取私用使用者資料

在 iOS 10 (或更新版本) 上執行的應用程式必須在其**plist**檔案中輸入一或多個隱私權金鑰, 以靜態方式宣告其意圖, 以存取特定功能或使用者資訊。此檔案會向使用者說明為何應用程式想要取得存取權。

> [!IMPORTANT]
> 無法提供必要金鑰的應用程式將會在系統嘗試存取其中一項受限制的功能或使用者資訊時, 以無訊息方式終止,_而不會發生錯誤_! 如果應用程式在 iOS 10 上意外啟動失敗, 請確定已指定所有必要的**資訊。 plist** 。

下列隱私權相關金鑰可供使用:

- **隱私權-Apple Music 使用方式描述**(`NSAppleMusicUsageDescription`)-可讓開發人員描述應用程式為何想要存取使用者的媒體櫃。
- **隱私權-藍牙週邊設備使用說明**(`NSBluetoothPeripheralUsageDescription`)-可讓開發人員描述應用程式要在使用者裝置上存取 Bluetooth 的原因。
- **隱私權-行事曆使用方式描述**(`NSCalendarsUsageDescription`)-可讓開發人員描述應用程式為何想要存取使用者的行事曆。
- **隱私權-相機使用方式描述**(`NSCameraUsageDescription`)-可讓開發人員描述應用程式想要存取裝置相機的原因。
- **隱私權-連絡人使用方式描述**(`NSContactsUsageDescription`)-可讓開發人員描述應用程式想要存取使用者連絡人的原因。
- **隱私權-健全狀況共用使用方式描述**(`NSHealthShareUsageDescription`)-可讓開發人員描述應用程式為何想要存取使用者的健康情況資料。 如需詳細資訊, 請參閱 Apple 的[HKHealthStore 類別參考](https://developer.apple.com/reference/healthkit/hkhealthstore)。
- **隱私權-健康情況更新使用方式描述**(`NSHealthUpdateUsageDescription`)-可讓開發人員描述應用程式為何想要編輯使用者的健康情況資料。 如需詳細資訊, 請參閱 Apple 的[HKHealthStore 類別參考](https://developer.apple.com/reference/healthkit/hkhealthstore)。
- **隱私權-HomeKit 使用方式描述**(`NSHomeKitUsageDescription`)-可讓開發人員描述應用程式想要存取使用者的 HomeKit 設定資料的原因。
- **隱私權-位置一律使用方式描述**(`NSLocationAlwaysUsageDescription`)-可讓開發人員描述應用程式為何想要一律擁有使用者位置的存取權。
- 不再**隱私權-位置使用方式描述**(`NSLocationUsageDescription`)-可讓開發人員描述應用程式想要存取使用者位置的原因。 *注意：IOS 8 (和更新版本) 中的這個金鑰已被取代。請`NSLocationAlwaysUsageDescription`改用`NSLocationWhenInUseUsageDescription`或。*
- **隱私權-使用中的位置描述**(`NSLocationWhenInUseUsageDescription`)-可讓開發人員描述應用程式在執行時, 要存取使用者位置的原因。
- 不再**隱私權-媒體櫃使用方式描述**-可讓開發人員描述應用程式想要存取使用者媒體櫃的原因。 *注意：IOS 8 (和更新版本) 中的這個金鑰已被取代。請`NSAppleMusicUsageDescription`改用。*
- **隱私權-麥克風使用方式描述**(`NSMicrophoneUsageDescription`)-可讓開發人員描述應用程式想要存取裝置麥克風的原因。
- **隱私權-動作使用方式描述**(`NSMotionUsageDescription`)-可讓開發人員描述應用程式想要存取裝置加速計的原因。
- **隱私權-相片媒體櫃使用方式描述**(`NSPhotoLibraryUsageDescription`)-可讓開發人員描述應用程式為何要存取使用者的相片媒體櫃。
- **隱私權-提醒使用方式描述**(`NSRemindersUsageDescription`)-可讓開發人員描述應用程式想要存取使用者提醒的原因。
- **隱私權-Siri 使用方式描述**(`NSSiriUsageDescription`)-可讓開發人員描述應用程式為何要將使用者資料傳送至 Siri。
- **隱私權-語音辨識使用方式描述**(`NSSpeechRecognitionUsageDescription`)-可讓開發人員描述應用程式為何要將使用者資料傳送至 Apple 的語音辨識伺服器。
- **隱私權-電視提供者使用方式描述**(`NSVideoSubscriberAccountUsageDescription`)-可讓開發人員描述應用程式想要存取使用者的電視提供者帳戶的原因。

如需使用**plist**金鑰的詳細資訊, 請參閱 Apple 的[資訊屬性清單索引鍵參考](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Introduction/Introduction.html#//apple_ref/doc/uid/TP40009248-SW1)。

<a name="Setting-Privacy-Keys" />

## <a name="setting-privacy-keys"></a>設定隱私權金鑰

請使用下列範例來存取 iOS 10 (及更新版本) 上的 HomeKit, 開發人員必須將`NSHomeKitUsageDescription`金鑰新增至應用程式的**plist**檔案, 並提供字串來宣告應用程式想要存取使用者之 HomeKit 資料庫的原因。 第一次執行應用程式時, 會向使用者顯示這個字串:

![範例 NSHomeKitUsageDescription 警示](security-privacy-images/info01.png "範例 NSHomeKitUsageDescription 警示")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Visual Studio 目前不支援從預設的 iOS 資訊清單編輯器中編輯**plist**隱私權金鑰的 ios。 相反地, 您必須使用 [一般 PList 編輯器], 請執行下列動作:

1. 以滑鼠右鍵按一下**方案總管**中的**plist**檔案, 然後選取 [**開啟方式**...]。
2. 從程式清單中選取 [**一般 PList 編輯器**] 以開啟檔案, 然後按一下 **[確定]** 。

    ![選取一般 PList 編輯器](security-privacy-images/InfoEditorSelectionVs.png "選取一般 PList 編輯器")
3. 在編輯器 **+** 中, 按一下最後一個資料列上的按鈕, 將新專案加入清單中。 這稱為「自訂屬性」, 其類型設定為`String` , 而空白值為。
4. 按一下屬性名稱, 隨即會出現下拉式清單。
5. 從下拉式清單中選取隱私權金鑰 (例如 [**隱私權-HomeKit 使用方式描述**]): 

    ![選取隱私權金鑰](security-privacy-images/InfoPListEditorSelectKey.png "選取隱私權金鑰")
6. 在 [值] 資料行中輸入描述, 以瞭解應用程式想要存取給定功能或使用者資訊的原因: 

    ![輸入描述](security-privacy-images/InfoPListSetValue.png "輸入描述")
7. 將變更儲存到檔案。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

若要設定任何隱私權金鑰, 請執行下列動作:

1. 在 [方案總管] 中，按兩下 [Info.plist] 檔案以開啟它進行編輯。
2. 在畫面底部, 切換至 [**來源**] 視圖。
3. 將新**專案**新增至清單。
4. 從下拉式清單中選取隱私權金鑰 (例如 [**隱私權-HomeKit 使用方式描述**]): 

    ![選取隱私權金鑰](security-privacy-images/info02.png "選取隱私權金鑰")
5. 輸入應用程式想要存取給定功能或使用者資訊的原因描述: 

    ![輸入描述](security-privacy-images/info03.png "輸入描述")
6. 將變更儲存到檔案。

-----

> [!IMPORTANT]
> 在上述範例中, 無法在**plist**檔案中`NSHomeKitUsageDescription`設定金鑰, 會導致應用程式以無訊息方式_失敗_(在執行時間由系統關閉), 而在 iOS 10 (或更新版本) 中執行時, 不會發生錯誤。

<a name="Summary" />

## <a name="summary"></a>總結

本文涵蓋 Apple 在 iOS 10 中所做的安全性和隱私權變更, 以及它們如何影響 Xamarin iOS 應用程式。

## <a name="related-links"></a>相關連結

- [iOS 10 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
