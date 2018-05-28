---
title: 自動化佈建
description: 成功安裝 Xamarin.iOS 之後，iOS 開發作業的下一步就是佈建您的 iOS 裝置。 本指南將探索使用自動化簽署來要求開發憑證和設定檔。
ms.prod: xamarin
ms.assetid: 81FCB2ED-687C-40BC-ABF1-FB4303034D01
ms.technology: xamarin-ios
author: asb3993
ms.author: amburns
ms.date: 05/22/2018
ms.openlocfilehash: d324e469ba392b14c635990d607bf04c949ad5db
ms.sourcegitcommit: 9f8e7393019791bbd6af4fefaa24a1602adabb4e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2018
---
# <a name="automatic-provisioning"></a>自動化佈建

_成功安裝 Xamarin.iOS 之後，iOS 開發作業的下一步就是佈建您的 iOS 裝置。本指南將探索使用自動化簽署來要求開發憑證和設定檔。_

## <a name="requirements"></a>需求

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

- Visual Studio for Mac 7.3 或更新版本
- Xcode 9 或更新版本

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

- Visual Studio 2017 15.7 版 (或更新版本)

您也必須和具有下列項目的 Mac 組建主機配對：

- Xcode 9 或更新版本

-----

## <a name="enabling-automatic-signing"></a>啟用自動簽署

在您啟動自動簽署程序之前，應該先確定已在 Visual Studio 中新增應用程式識別碼，如 [Apple 帳戶管理](~/cross-platform/macios/apple-account-management.md)指南所述。 新增「應用程式識別碼」之後，您可以使用任何關聯的「小組」。 這可讓您針對小組建立憑證、設定檔及其他識別碼。 小組識別碼也會用來建立將包含在佈建設定檔中之「應用程式識別碼」的前置詞。 有了這項資訊，便可讓 Apple 驗證您與所宣稱的身分識別相符。

> [!IMPORTANT]
> 開始之前，請務必登入 [iTunes Connect](https://itunesconnect.apple.com/) 或 [appleid.apple.com](https://appleid.apple.com)，以檢查您是否已經接受最新的 Apple 帳戶原則。 如果出現提示，請完成步驟，以接受來自 Apple 的任何新帳戶合約。 如果您不接受自 2018 年 5 月起的隱私權合約，便會在佈建裝置時收到以下警示：
> ```
> Unexpected authentication failure. Reason: {
> "authType" : "sa"
>}
>```

若要自動簽署應用程式以在 iOS 裝置上部署，請執行下列動作：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在 Visual Studio for Mac 中開啟 iOS 專案。

2. 開啟 **Info.plist** 檔案。

3. 在 [簽署] 區段中，選擇 [自動化佈建]：

    ![[小組] 選取器下拉式清單](automatic-provisioning-images/image2.png)

4. 從 [小組] 下拉式清單中選取您的小組。

6. 幾秒鐘之後，就會建立「簽署憑證」和「佈建設定檔」：

    ![成功建立憑證和設定檔](automatic-provisioning-images/image5.png)

    如果自動簽署失敗，**自動簽署台**將會顯示該錯誤的原因。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 依[與 Mac 配對](~/ios/get-started/installation/windows/connecting-to-mac/index.md)指南所述配對 Visual Studio 2017 和 Mac。

2. 選取 [專案] > [佈建內容] 開啟 [佈建] 選項

3. 選取 [自動化佈建] 配置：

    ![選取自動配置](automatic-provisioning-images/prov4.png)

4. 從 [小組] 下拉式方塊選取您的小組，啟動自動簽署程序。

    ![選取小組](automatic-provisioning-images/prov3.png)

4. 這樣會啟動自動簽署程序。 然後，Visual Studio 會嘗試產生應用程式識別碼、佈建設定檔，以及簽署身分識別，使用這些成品以供簽署。 您可以在組建輸出中查看產生程序：

    ![組建輸出顯示成品的產生過程](automatic-provisioning-images/prov5.png)

-----

## <a name="triggering-automatic-provisioning"></a>觸發自動化佈建

啟用自動簽署之後，Visual Studio for Mac 將會在發生下列任何情況時，視需要更新這些成品：

* 您的 Mac 上插入了 iOS 裝置
    - 這會自動檢查以了解該裝置是否已在 Apple Developer Portal (Apple 開發人員入口網站) 上註冊。 如果未註冊，就會新增它並產生一個包含它的新佈建設定檔。
* 您應用程式的「套件組合識別碼」已變更
    - 這會更新應用程式識別碼。 系統會建立包含此應用程式識別碼的新佈建設定檔。
* 在 Entitlements.plist 檔案中啟用了支援的功能。
    - 系統會將此功能新增至應用程式識別碼，並產生一個含有此更新過之應用程式識別碼的新佈建設定檔。
    - 目前並非所有功能都受到支援。 如需有關所支援功能的詳細資訊，請參閱[使用功能](~/ios/deploy-test/provisioning/capabilities/index.md)指南。


## <a name="related-links"></a>相關連結

- [免費佈建](~/ios/get-started/installation/device-provisioning/free-provisioning.md)
- [應用程式散發](~/ios/deploy-test/app-distribution/index.md)
- [疑難排解](~/ios/deploy-test/troubleshooting.md)
- [Apple - 應用程式散發指南](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html) \(英文\)
