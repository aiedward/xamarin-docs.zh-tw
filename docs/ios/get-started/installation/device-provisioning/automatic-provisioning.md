---
title: 自動化佈建
description: 成功安裝 Xamarin.iOS 之後，iOS 開發作業的下一步就是佈建您的 iOS 裝置。 本指南將探索如何在 Visual Studio for Mac 中使用「自動化佈建」來要求部署憑證和設定檔。
ms.prod: xamarin
ms.assetid: 81FCB2ED-687C-40BC-ABF1-FB4303034D01
ms.technology: xamarin-ios
author: asb3993
ms.author: amburns
ms.date: 11/17/2017
ms.openlocfilehash: 01818d2870c7cf59a0f15385dbb3565f07400ff0
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="automatic-provisioning"></a>自動化佈建

_成功安裝 Xamarin.iOS 之後，iOS 開發作業的下一步就是佈建您的 iOS 裝置。本指南將探索如何在 Visual Studio for Mac 中使用「自動化簽署」來要求開發憑證和設定檔。_

## <a name="requirements"></a>需求

- Visual Studio for Mac 7.3 或更新版本
- Xcode 9 或更新版本

> [!IMPORTANT]
> 本指南說明如何使用 Visual Studio for Mac 來設定 Apple 裝置以進行部署，以及如何部署應用程式。 如需有關此做法的手動步驟，或是若要在 Windows 上使用 Visual Studio 來進行此操作，建議您依照[手動佈建](~/ios/get-started/installation/device-provisioning/manual-provisioning.md)指南中的詳細步驟進行操作。

## <a name="enabling-automatic-signing"></a>啟用自動簽署

在您啟動自動簽署程序之前，應該先確定已在 Visual Studio for Mac 中新增「應用程式識別碼」，如 [Apple 帳戶管理](~/cross-platform/macios/apple-account-management.md)指南所述。 新增「應用程式識別碼」之後，您可以使用任何關聯的「小組」。 這可讓您針對小組建立憑證、設定檔及其他識別碼。 小組識別碼也會用來建立將包含在佈建設定檔中之「應用程式識別碼」的前置詞。 有了這項資訊，便可讓 Apple 驗證您與所宣稱的身分識別相符。

若要自動簽署應用程式以在 iOS 裝置上部署，請執行下列動作：

1. 在 Visual Studio for Mac 中開啟 iOS 專案。

2. 開啟 **Info.plist** 檔案。

3. 在 [簽署] 區段中，選擇 [自動化佈建]：

    ![[小組] 選取器下拉式清單](automatic-provisioning-images/image2.png)

4. 從 [小組] 下拉式清單中選取您的小組。

6. 幾秒鐘之後，就會建立「簽署憑證」和「佈建設定檔」：

    ![成功建立憑證和設定檔](automatic-provisioning-images/image5.png)

    如果自動簽署失敗，**自動簽署台**將會顯示該錯誤的原因。

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
