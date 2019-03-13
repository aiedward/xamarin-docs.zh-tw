---
title: Xamarin.iOS 測試及部署 - 疑難排解
description: 本文件提供與程式碼簽署和佈建、TestFlight，以及將 iOS 應用程式套件組合從 Mac 組建主機複製到 Windows 的相關疑難排解祕訣。
ms.prod: xamarin
ms.assetid: 65286D09-F74D-4F22-B6CD-D1BCD7FC7992
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/23/2017
ms.openlocfilehash: 239f20515e734a6c0b517e07bc2d1e4289cabe65
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50104787"
---
# <a name="xamarinios-testing-and-deployment---troubleshooting"></a>Xamarin.iOS 測試及部署 - 疑難排解

## <a name="code-signing--provisioning"></a>程式碼簽署與佈建

利用 iOS 進行程式碼簽署和佈建可能非常棘手，因此務必要確定程式碼簽署憑證與佈建設定檔井然有序。

* 大型的團隊應該避免使用 Xcode 中的 [Fix issue] \(修正問題\) 按鈕，如下圖所示：

    [![](troubleshooting-images/fixissue.png "[Fix issue] \(修正問題\) 對話方塊")](troubleshooting-images/fixissue.png#lightbox)

    這將會建立新的佈建設定檔和憑證。 從好處想，這會在每次小組成員按一下佈建設定檔時建立一個佈建設定檔，造成設定檔混亂。 從壞處想，它將會撤銷公司內其他人的憑證，導致其應用程式停止運作。

* 保持「鑰匙圈存取」井然有序，並刪除過期的憑證和設定檔。 企業憑證可持續使用三年，其他憑證則僅能持續使用一年。 您無法更新憑證，因此需要在舊的憑證到期之前建立新的憑證。 請務必撤銷並刪除舊的憑證，並使用新的憑證重新簽署應用程式。

* 安裝新的佈建設定檔時，移除舊的佈建設定檔。 這表示 Visual Studio for Mac 不需要決定要使用哪個設定檔。 若要達成此目的，請先確定刪除 Apple 開發人員中心中的設定檔，然後瀏覽至 [Preferences] \(喜好設定\) > [Your Account] \(您的帳戶\) > [View Details] \(檢視詳細資料\)。選取佈建設定檔，然後按一下 [顯示於 Finder]。 這將顯示設定檔在 Mac 檔案系統中的位置，之後就可以使用 Finder 刪除該位置。

* 請確認所有必要的憑證和對應的私密金鑰都可以使用。 因為每個小組都需要一個開發人員憑證 (以便在自己的裝置上安裝應用程式)，以及一個散發憑證 (以安裝在其他裝置上)

* 安裝新的佈建設定檔或憑證時，重新啟動 Xcode 和 Visual Studio for Mac / Visual Studio。

## <a name="testflight"></a>TestFlight

有時候測試不如預期順暢。  下列步驟有助於使用 TestFlight 解決問題：

- TestFlight 僅適用於以 iOS 8 和更新版本為目標的應用程式。

- 必須要有具備 Beta 權利的 *App Store 散發設定檔* 。

- [New iOS App submission] \(新的 iOS 應用程式提交\) 視窗必須包含與應用程式的 **Info.plist** 完全相同的資訊，而且必須填入所有區段。 必須針對應用程式指定圖示，才能上傳至 TestFlight。

- 上傳新的組建時，需要 1 到 5 分鐘的時間，直到組建出現在 iTunes Connect 中為止。

- 您必須為每個版本的應用程式開啟 [TestFlight Beta Testing](~/ios/deploy-test/testflight.md#beta-testing) \(TestFlight Beta 測試\) 開關。

- 同時也是內部測試人員的每個開發人員小組成員都必須開啟 [Internal Tester] \(內部測試人員\) 開關。

- 屬於或擁有另一個 iTunes Connect 帳戶的使用者不得為內部測試人員。 他們只能新增為外部測試人員。

- 您可以個別新增、選取和邀請內部和外部使用者。 每個清單都必須受到個別管理。

- Apple 必須核准將散發給外部測試人員的每個組建。 如果組建版本變更，則需要由 Apple 進行新的 Beta 審查。 如果組建編號變更，則審查是選擇性的。

- 中繼資料必須加入至散發給外部測試人員的組建。 這可以透過按一下 [我的應用程式] > [發行前版本] 中的組建編號來存取。

- 每天只能提交兩個組建以供審查。 因為變更版本會強制進行審查，這表示每天只能變更版本號碼兩次。

<a name="Automatically_copy_app_bundles_back_to_Windows" />

## <a name="automatically-copy-app-bundles-back-to-windows"></a>自動將 .app 套件組合複製回 Windows

[!include[](~/ios/includes/copy-app-bundle-to-windows.md)]
