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
ms.openlocfilehash: 32d9142fb74cba637982438a2aa984adb759d847
ms.sourcegitcommit: bb18e6b3b5d592374b17932efa1e85c3b5fbad57
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950712"
---
# <a name="automatic-provisioning-for-xamarinios"></a>Xamarin.iOS 的自動佈建

_成功安裝了 Xamarin 之後，iOS 開發的下一步就是布建您的 iOS 裝置。本指南將探索如何使用自動布建來要求開發憑證和設定檔。_

## <a name="requirements"></a>規格需求

自動布建適用于 Visual Studio for Mac、Visual Studio 2019 和 Visual Studio 2017 (15.7 版和更高版本的) 。 

> [!NOTE]
> 您也必須擁有付費的 Apple 開發人員帳戶，才能使用這項功能。 如需 Apple 開發人員帳戶的詳細資訊，請參考 [裝置](~/ios/get-started/installation/device-provisioning/index.md) 布建指南。
> 如果您沒有付費的 Apple 開發人員帳戶，請參閱 [適用于 Xamarin 的免費](~/ios/get-started/installation/device-provisioning/free-provisioning.md) 布建指南。

> [!NOTE]
> 開始之前，請務必先接受 [Apple Developer 入口網站](https://developer.apple.com/account/) 或 [App Store Connect](https://appstoreconnect.apple.com/)中的任何授權合約。


## <a name="enable-automatic-provisioning"></a>啟用自動佈建

開始自動簽署程式之前，請確定您已在 Visual Studio 中新增 Apple ID，如 [Apple 帳戶管理](~/cross-platform/macios/apple-account-management.md) 指南中所述。 

新增「應用程式識別碼」之後，您可以使用任何關聯的「小組」__。 這可讓您針對小組建立憑證、設定檔及其他識別碼。 小組識別碼也用來建立將包含在布建設定檔中之應用程式識別碼的前置詞。 有了這項資訊，便可讓 Apple 驗證您與所宣稱的身分識別相符。

若要自動簽署應用程式以在 iOS 裝置上部署，請執行下列動作：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 Visual Studio for Mac 中開啟 iOS 專案。

2. 開啟 **Info.plist** 檔案。

3. 選取 [ **應用程式** ] 索引標籤。

4. 在 [簽署]**** 區段，選取 [自動佈建]****：

    ![[小組] 選取器下拉式清單](automatic-provisioning-images/image2.png)

5. 從 [小組]**** 下拉式清單中選取您的小組。

6. 幾秒鐘之後，就會建立「簽署憑證」和「佈建設定檔」：

    ![成功建立憑證和設定檔](automatic-provisioning-images/image5.png)

    如果自動簽署失敗，**自動簽署台**將會顯示該錯誤的原因。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

> [!NOTE]
> 如果您使用 Visual Studio 2017 或 Visual Studio 2019 (16.4 版和更舊的) ，則必須先將您 [的 Mac 組建主機配對](~/ios/get-started/installation/windows/connecting-to-mac/index.md) ，才能繼續進行。

1. 在 [ **方案總管**中，以滑鼠右鍵按一下 iOS 專案名稱，然後選取 [ **屬性**]。 然後，流覽至 [ **iOS** 套件組合簽署] 索引標籤：

    ![IOS 屬性中套件組合簽署頁面的螢幕擷取畫面。](automatic-provisioning-images/bundle-signing-win.png)

2. 選取 **自動** 布建配置。

3. 從 [ **小組** ] 下拉式功能表中選取您的小組，以啟動自動簽署程式。 Visual Studio 會指出處理常式是否已順利完成：

    ![[套件組合簽署] 頁面的螢幕擷取畫面，其中反白顯示「自動布建已順利完成」訊息。](automatic-provisioning-images/signing-success-win.png)

-----

## <a name="run-automatic-provisioning"></a>執行自動布建

啟用自動布建時，Visual Studio 將會在發生下列任何情況時，視需要重新執行進程：

- 您的 Mac 上插入了 iOS 裝置
  - 這會自動檢查以了解該裝置是否已在 Apple Developer Portal (Apple 開發人員入口網站) 上註冊。 如果不是，則會新增它，並產生包含該設定檔的新布建設定檔。
- 您應用程式的「套件組合識別碼」已變更
  - 這會更新應用程式識別碼。 系統會建立包含此應用程式識別碼的新佈建設定檔。
- 在 Entitlements.plist 檔案中啟用了支援的功能。
  - 系統會將此功能新增至應用程式識別碼，並產生一個含有此更新過之應用程式識別碼的新佈建設定檔。
  - 目前並非所有功能都受到支援。 如需有關所支援功能的詳細資訊，請參閱[使用功能](~/ios/deploy-test/provisioning/capabilities/index.md)指南。

## <a name="wildcard-app-ids"></a>萬用字元應用程式識別碼

在 Visual Studio for Mac 和 Visual Studio 2019 (16.5 版或更新版本) 中，自動布建預設會嘗試根據**plist**中指定的套件組合**識別碼**，建立及使用萬用字元應用程式識別碼和布建設定檔，而不是明確的應用程式識別碼。 萬用字元應用程式識別碼可減少要在 Apple Developer 入口網站中維護的設定檔和識別碼數目。

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

如果您的應用程式使用其中一種權利，Visual Studio 會嘗試建立明確的 (，而不是) 應用程式識別碼的萬用字元。

## <a name="troubleshoot"></a>疑難排解 

- 可能需要數小時的時間，才能核准新的 Apple 開發人員帳戶。 您必須等到帳戶獲得核准之後，才能啟用自動布建。
- 如果自動布建程式失敗並出現錯誤訊息 `Authentication Service Is Unavailable` ，請登入 [App Store Connect](https://appstoreconnect.apple.com/) 或 [appleid.apple.com](https://appleid.apple.com) ，確認您已接受最新的服務合約。
- 如果您收到錯誤訊息 `Authentication Error: Xcode 7.3 or later is required to continue developing with your Apple ID.` ，請確定選取的小組具有 Apple 開發人員計畫的有效付費成員資格。 若要使用付費的 Apple 開發人員帳戶，請參閱 [適用于 Xamarin iOS 應用程式的免費](~/ios/get-started/installation/device-provisioning/free-provisioning.md) 布建指南。

## <a name="related-links"></a>相關連結

- [免費佈建](~/ios/get-started/installation/device-provisioning/free-provisioning.md)
- [應用程式散發](~/ios/deploy-test/app-distribution/index.md)
- [疑難排解](~/ios/deploy-test/troubleshooting.md)
- [Apple - 應用程式散發指南](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
