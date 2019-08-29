---
title: Xamarin.iOS 的應用程式群組功能
description: 為應用程式新增功能通常需要額外的佈建設定。 本指南說明「應用程式群組」功能所需的設定。
ms.prod: xamarin
ms.assetid: 0A61220B-BBAC-492B-9D3B-578986E64064
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/15/2017
ms.openlocfilehash: 20aa1da478916bf4c8949103a5ce7fbf1f5d8f93
ms.sourcegitcommit: 3d21bb1a6d9b78b65aa49917b545c39d44aa3e3c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70064832"
---
# <a name="app-group-capabilities-in-xamarinios"></a>Xamarin.iOS 的應用程式群組功能

_為應用程式新增功能通常需要額外的佈建設定。本指南說明「應用程式群組」功能所需的設定。_

「應用程式群組」可讓不同的應用程式 (或應用程式及其擴充功能) 存取共用檔案儲存體位置。 「應用程式群組」可用於資料下列資料：

* [Apple Watch 設定](~/ios/watchos/app-fundamentals/settings.md)
* [共用 NSUserDefaults](~/ios/app-fundamentals/user-defaults.md)
* [共用檔案](~/ios/watchos/app-fundamentals/parent-app.md#files)

## <a name="configure-a-new-app-group"></a>設定新的應用程式群組

設定共用位置時，會使用 [App Group](https://developer.apple.com/library/content/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19) \(應用程式群組\) 來設定，這是在 [Apple Developer Center](https://developer.apple.com/account/).\(Apple 開發人員中心\) 上的 [Certificates, Identifiers & Profiles] \(憑證、識別碼及設定檔\)  中進行設定。 每個專案的 Entitlements.plist 中也必須參考此值。

應用程式群組會有識別碼，這通常是具有群組的套件組合識別碼。 前置詞的「套件組合識別碼」。 例如，套件組合識別碼 `com.xamarin.WatchSettings` 的應用程式群組會是  `group.com.xamarin.WatchSettings`。

若要建立新的「應用程式群組」，請執行下列動作：

1. 瀏覽 Apple 的  [iOS Developer Center](https://developer.apple.com/account/) \(iOS 開發人員中心\)，開啟您的 [Account] \(帳戶\)  並登入。
2. 選取 [Certificates, IDs & Profiles] \(憑證、識別碼及設定檔\)  。
3. 在 [Identifiers] \(識別碼\)  底下，選取 [App Groups] \(應用程式群組\)  ，然後按一下 [+]  按鈕來建立新的群組。
4. 輸入新群組的 [Name] \(名稱\)  和 [Identifier] \(識別碼\)  ，然後按一下 [Continue] \(繼續\)  按鈕： 
   
    ![新增 App Group (應用程式群組) 詳細資料](app-groups-capabilities-images/image52.png)

5. 按一下 [Register] \(註冊\)  按鈕來建立群組，然後按一下 [Done] \(完成\)  來返回已註冊的 App Group (應用程式群組) 清單。

## <a name="configure-an-app-to-use-app-groups"></a>設定讓應用程式使用應用程式群組

建立 App Group (應用程式群組) 之後，請設定 App ID (應用程式識別碼) 以便讓應用程式使用它。

請執行下列動作：

1. 前往 Apple 的 [iOS Developer Center](https://developer.apple.com/account/) (iOS 開發人員中心)，然後使用 Apple 開發人員帳戶來登入。
2. 從 [Program Resources] \(程式資源\)  功能表中，選取 [Certificates, IDs & Profiles] \(憑證、識別碼及設定檔\)  。
3. 在 [Identifiers] \(識別碼\)  底下，選取 [App IDs] \(應用程式識別碼\)  ，然後按一下 [+]  按鈕來建立新的識別碼。
4. 輸入 App ID (應用程式識別碼) 的 [Name] \(名稱\)，並為它提供 [Explicit App ID] \(明確的應用程式識別碼\)。
5. 在 [App Services] \(應用程式服務\)  底下，啟用 [App Group] \(應用程式群組\)  ，然後按一下 [Continue] \(繼續\) 按鈕：

    ![新增 App Group (應用程式群組) 應用程式服務](app-groups-capabilities-images/image53.png)

6. 確認設定，然後按一下 [Register] \(註冊\)  按鈕來建立 App ID (應用程式識別碼)。
7. 按一下 [Done] \(完成\)  來返回已註冊的 App ID (應用程式識別碼) 清單。
8. 從清單中選取新建立的 App ID (應用程式識別碼)，然後按一下 [Edit] \(編輯\)  按鈕：

    ![從清單中選取 App ID (應用程式識別碼)](app-groups-capabilities-images/image54.png)

9. 在 [Service] \(服務\) 的 [App Group] \(應用程式群組\)  底下，按一下 [Edit] \(編輯\)  按鈕：

    ![從清單中選取 App ID (應用程式識別碼)](app-groups-capabilities-images/image55.png)

10. 選取上述建立的App Group (應用程式群組)，然後按一下 [Continue] \(繼續\)  按鈕：

    ![新增 App Group (應用程式群組)](app-groups-capabilities-images/image56.png)

11. 按一下 [Assign] \(指派\)  ，然後按一下 [Done] \(完成\)  按鈕來返回已註冊的 App ID (應用程式識別碼) 清單。
12. 針對任何將使用「應用程式群組」的應用程式 (或擴充功能) 重複上述步驟。

## <a name="next-steps"></a>後續步驟
 
以下清單說明可能需要進行的額外步驟：

* 在您的應用程式中使用架構命名空間。
* 為您的應用程式新增必要的權利。 如需有關所需權利及如何新增這些權利的詳細資訊，請參閱[使用權利](~/ios/deploy-test/provisioning/entitlements.md)指南。
* 在應用程式的 [iOS 套件組合簽署] 中，確定 [自訂權利] 已設定為 **Entitlements.plist**。   這「不是」 __  「偵錯」和「iOS 模擬器」組建的預設設定。

如果您在應用程式服務方面遇到問題，請參閱主要指南的[疑難排解](~/ios/deploy-test/provisioning/capabilities/index.md)一節。
