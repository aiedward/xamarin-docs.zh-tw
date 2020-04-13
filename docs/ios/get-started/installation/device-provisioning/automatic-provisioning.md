---
title: Xamarin.iOS 的自動佈建
description: 成功安裝 Xamarin.iOS 之後，iOS 開發作業的下一步就是佈建您的 iOS 裝置。 本指南將探索使用自動化簽署來要求開發憑證和設定檔。
ms.prod: xamarin
ms.assetid: 81FCB2ED-687C-40BC-ABF1-FB4303034D01
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.custom: video
ms.date: 03/05/2020
ms.openlocfilehash: 069c40b74876bea1d3a0c8fca23b3d90c4b91635
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "79510675"
---
# <a name="automatic-provisioning-for-xamarinios"></a>Xamarin.iOS 的自動佈建

_成功安裝 Xamarin.iOS 後,iOS 開發的下一步是預配 iOS 設備。本指南探討了使用自動預配來請求開發證書和配置檔。_

## <a name="requirements"></a>需求

Mac 視覺工作室、2019 年 Visual Studio 和 2017 可視化工作室(版本 15.7 及以上)提供自動預配。 

> [!NOTE]
> 您還必須有一個付費的 Apple 開發人員帳戶才能使用此功能。 有關 Apple 開發人員帳戶的詳細資訊,請造[訪設備配置](~/ios/get-started/installation/device-provisioning/index.md)指南。
> 如果您沒有付費 Apple 開發人員帳戶,請參閱 [Xamarin.iOS 的免費預配] (*/ios/入門/安裝/設備配置/免費預配.md)指南。

> [!NOTE]
> 開始之前,請務必首先接受[Apple 開發人員門戶](https://developer.apple.com/account/)或[應用商店連接](https://appstoreconnect.apple.com/)中的任何許可協定。


## <a name="enable-automatic-provisioning"></a>啟用自動佈建

在開始自動簽名過程之前,應確保在 Visual Studio 中添加了 Apple ID,如 Apple[帳戶管理](~/cross-platform/macios/apple-account-management.md)指南中所述。 

新增「應用程式識別碼」之後，您可以使用任何關聯的「小組」__。 這可讓您針對小組建立憑證、設定檔及其他識別碼。 團隊 ID 還用於為將在預配設定檔中包含的應用 ID 建立前置碼。 有了這項資訊，便可讓 Apple 驗證您與所宣稱的身分識別相符。

若要自動簽署應用程式以在 iOS 裝置上部署，請執行下列動作：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 Visual Studio for Mac 中開啟 iOS 專案。

2. 開啟 **Info.plist** 檔案。

3. 在 [簽署]**** 區段，選取 [自動佈建]****：

    ![[小組] 選取器下拉式清單](automatic-provisioning-images/image2.png)

4. 從 [小組]**** 下拉式清單中選取您的小組。

5. 幾秒鐘之後，就會建立「簽署憑證」和「佈建設定檔」：

    ![成功建立憑證和設定檔](automatic-provisioning-images/image5.png)

    如果自動簽署失敗，**自動簽署台**將會顯示該錯誤的原因。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

> [!NOTE]
> 如果您使用的是 Visual Studio 2017 或 Visual Studio 2019(版本 16.4 及更舊版),則需要在繼續操作之前[與 Mac 構建主機配對](~/ios/get-started/installation/windows/connecting-to-mac/index.md)。

1. 在**解決方案資源管理員**中,右鍵按下 iOS 專案名稱並選擇**屬性**。 然後,導航到**iOS 捆綁包簽名**選項卡:

    ![iOS 屬性中的捆綁簽名頁的屏幕截圖。](automatic-provisioning-images/bundle-signing-win.png)

2. 選擇**自動預配**方案。

3. 從 **「團隊**下拉選單」中選擇您的團隊,以啟動自動簽名過程。 Visual Studio 將指示該過程是否成功完成:

    ![突出顯示消息的捆綁簽名頁的螢幕截圖"自動預配成功完成」。](automatic-provisioning-images/signing-success-win.png)

-----

## <a name="run-automatic-provisioning"></a>執行自動預先接

啟用自動預配後,如有必要,Visual Studio 將在必要時在以下任何情況中重新運行該過程:

- 您的 Mac 上插入了 iOS 裝置
  - 這會自動檢查以了解該裝置是否已在 Apple Developer Portal (Apple 開發人員入口網站) 上註冊。 如果不是,它將添加它並生成包含它的新預配配置檔。
- 您應用程式的「套件組合識別碼」已變更
  - 這會更新應用程式識別碼。 系統會建立包含此應用程式識別碼的新佈建設定檔。
- 在 Entitlements.plist 檔案中啟用了支援的功能。
  - 系統會將此功能新增至應用程式識別碼，並產生一個含有此更新過之應用程式識別碼的新佈建設定檔。
  - 目前並非所有功能都受到支援。 如需有關所支援功能的詳細資訊，請參閱[使用功能](~/ios/deploy-test/provisioning/capabilities/index.md)指南。

## <a name="wildcard-app-ids"></a>萬用字元應用程式識別碼

在 Mac 和 Visual Studio 2019 的 Visual Studio 中(版本 16.5 或更高),預設情況下,自動預配將嘗試創建和使用通配符應用 ID 和預配配置檔,而不是基於**Info.plist**中指定的**捆綁標識符**的顯式應用 ID。 萬用字元應用程式識別碼可減少要在 Apple Developer 入口網站中維護的設定檔和識別碼數目。

在某些情況下，應用程式的權利需要明確的應用程式識別碼。 下列權利不支援萬用字元應用程式識別碼：

- 應用程式群組
- 相關聯的網域
- Apple Pay
- Game Center
- HealthKit
- HomeKit
- 作用區
- 在應用程式內購買
- 多重路徑
- NFC
- 個人 VPN
- 推播通知
- 無線配件組態

如果應用使用這些許可權之一,Visual Studio 將嘗試創建顯式(而不是通配符)應用 ID。

## <a name="troubleshoot"></a>疑難排解 

- 新的 Apple 開發人員帳戶可能需要幾個小時才能獲得批准。 在帳戶獲得批准之前,您將無法啟用自動預配。
- 如果自動預配過程在錯誤消息`Authentication Service Is Unavailable`中失敗,請登錄到 App Store [Connect](https://appstoreconnect.apple.com/)或[appleid.apple.com](https://appleid.apple.com)以檢查您是否接受了最新的服務協定。
- 如果您收到錯誤消息`Authentication Error: Xcode 7.3 or later is required to continue developing with your Apple ID.`,請確保所選團隊具有 Apple 開發人員計劃的有效付費會員資格。 要使用付費 Apple 開發人員帳戶,請參閱[Xamarin.iOS 應用的免費預配](~/ios/get-started/installation/device-provisioning/free-provisioning.md)指南。

## <a name="related-links"></a>相關連結

- [免費佈建](~/ios/get-started/installation/device-provisioning/free-provisioning.md)
- [套用分轉](~/ios/deploy-test/app-distribution/index.md)
- [疑難排解](~/ios/deploy-test/troubleshooting.md)
- [Apple - 應用程式散發指南](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
