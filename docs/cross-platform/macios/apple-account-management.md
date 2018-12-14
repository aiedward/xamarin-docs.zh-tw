---
title: Apple 帳戶管理
description: 本文件說明如何在 Visual Studio 中使用的 Apple 帳戶管理功能，適用於 Mac 和 Visual Studio 2017。
ms.prod: xamarin
ms.assetid: 71388B83-699B-4E42-8CBF-8557A4A3CABF
author: asb3993
ms.author: amburns
ms.date: 05/06/2018
ms.openlocfilehash: 1e353aceaf0e2c0525b82c0ccb7e7bcb73df3075
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106061"
---
# <a name="apple-account-management"></a>Apple 帳戶管理

Apple 帳戶管理介面提供方法來檢視所有的開發小組相關聯的 Apple ID。 它也可讓您藉由顯示一份檢視每個小組的更多詳細_簽署身分識別_並_佈建設定檔_安裝在電腦上。

使用命令列上執行您的 Apple ID 的驗證[fastlane](https://fastlane.tools/)。 您已成功驗證您的電腦上必須安裝 fastlane。 fastlane 並安裝方式的更多有關詳述 [fastlane](~/ios/deploy-test/provisioning/fastlane/index.md) 輔助線。

Apple 帳戶 對話方塊可讓您執行下列作業：

* **建立及管理憑證**
* **建立和管理佈建設定檔**

本指南說明如何執行這項操作的詳細資訊。

> [!NOTE]
> Xamarin 的工具，適用於 Apple 帳戶管理只會顯示付費的 Apple 開發人員帳戶的相關資訊。 若要了解如何測試而不需要付費的 Apple 開發人員帳戶的裝置上的應用程式，請參閱[Xamarin.iOS 應用程式的免費佈建](~/ios/get-started/installation/device-provisioning/free-provisioning.md)指南。

您也可以使用 iOS 自動化佈建工具來自動建立及管理您的簽署身分識別、 應用程式識別碼和佈建設定檔。 如需有關如何使用這些功能的詳細資訊，請參閱[裝置佈建](~/ios/get-started/installation/device-provisioning/index.md)指南。

## <a name="requirements"></a>需求

Apple 帳戶管理位於 Visual Studio for Mac 和 Visual Studio 2017 （15.7 版及更新版本）

您必須有 「 Apple 開發人員帳戶才能使用此功能。 在 Apple 開發人員帳戶的詳細資訊位於[裝置佈建](~/ios/get-started/installation/device-provisioning/index.md)指南。

- 請確定您已連線到網際網路。 這是因為 fastlane 會直接與 Apple Developer 入口網站進行通訊。
- 請確定您已[安裝 fastlane 工具](~/ios/deploy-test/provisioning/fastlane/index.md#Installation)。
- 請確定您有最新的 fastlane 工具，從[ https://download.fastlane.tools ](https://download.fastlane.tools)。
- 在開始之前，請務必接受任何使用者的授權合約[開發人員入口網站](https://developer.apple.com/account/)。

## <a name="adding-an-apple-developer-account"></a>新增 Apple 開發人員帳戶

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 若要開啟 帳戶管理對話方塊，請移至**Visual Studio > 喜好設定 > Apple 開發人員帳戶**:

    ![Apple 開發人員帳戶選項](apple-account-management-images/image1.png)

2. 按下 **+** 按鈕以顯示登入 對話方塊，如下所述： 

    ![fastlane 對話方塊。](apple-account-management-images/image2.png)

4. 輸入您的 Apple ID 和密碼，然後按一下**登入**按鈕。 這會儲存在安全的金鑰鏈的認證，這部電腦上。 [fastlane](~/ios/deploy-test/provisioning/fastlane/index.md) 來安全地處理您的認證，並將其傳遞至 Apple 開發人員入口網站。
 
5. 選取**永遠允許**上警示的對話方塊，讓 Visual Studio 來使用您的認證：

    ![一律允許 [警示] 對話方塊](apple-account-management-images/image4.png)

6. 已成功新增您的帳戶，您會看到您的 Apple ID 和您的 Apple ID 是一部分的任何小組。

    ![使用 [新增帳戶的 Apple 開發人員帳戶] 對話方塊](apple-account-management-images/image5.png)

7. 選取任何小組，然後按**檢視詳細資料...** 按鈕。 這會顯示一份所有 「 簽署身分識別和安裝在您的電腦的佈建設定檔：

    ![簽署身分識別和佈建設定檔，您的電腦上檢視詳細資料畫面顯示](apple-account-management-images/image6.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 您開始將您的 Apple ID 新增至 Visual Studio 2017 之前，請確定您的開發環境[至 Mac 組建主機配對](~/ios/get-started/installation/windows/connecting-to-mac/index.md)。

1. 若要開啟 [帳戶管理] 視窗，請前往**工具 > 選項 > Xamarin > Apple 帳戶**:

    ![Apple 帳戶選項畫面](apple-account-management-images/prov1.png)

1. 選取 **新增**按鈕，然後輸入您的 Apple ID 和密碼：

    ![使用者名稱和密碼對話方塊](apple-account-management-images/prov1a.png)

1. 已成功新增您的帳戶，您會看到您的 Apple ID 和您的 Apple ID 是一部分的任何小組。
 
1. 選取任何小組，然後按**檢視詳細資料...** 按鈕。 這會顯示一份所有「簽署身分識別和安裝在您的電腦的佈建設定檔」：

    ![使用者名稱和密碼對話方塊](apple-account-management-images/prov2.png)

-----


## <a name="managing-signing-identities-and-provisioning-profiles"></a>管理「簽署身分識別和佈建設定檔」

小組的詳細資料 對話方塊會顯示一份簽署身分識別，依型別。 **狀態**資料行建議您憑證是否： 

* **有效**– 簽署身分識別 （憑證和私密金鑰） 在電腦上安裝，且它尚未過期。

* **不在 Keychain 中**– Apple 的伺服器上沒有有效的簽署身分識別。 若要安裝此電腦上，它必須從另一部電腦將其匯出。 您無法從 Apple 開發人員入口網站下載的簽署身分識別，因為它不會包含私密金鑰。

* **缺少私密金鑰**– 使用沒有私密金鑰的憑證安裝在金鑰鏈。

* **過期**– 憑證已過期。 您應該將它移除從您的 keychain。

  ![小組的詳細資料 對話方塊資訊](apple-account-management-images/image7.png)

## <a name="create-a-signing-identities"></a>建立簽署身分識別

若要建立新的簽署身分識別，請選取 **Create Certificate** 下拉式按鈕，然後選取您所需要的類型。 如果您有正確的權限的新簽署身分識別會出現在幾秒之後。

如果下拉式清單中的選項會呈現灰色，而且未選取，這表示您沒有正確的小組權限來建立這種類型的憑證。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![建立憑證選項](apple-account-management-images/image8.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![建立憑證選項](apple-account-management-images/prov3.png)

-----

## <a name="download-provisioning-profiles"></a>下載佈建設定檔

小組的詳細資料 對話方塊也會顯示一份所有連線到您的開發人員帳戶的佈建設定檔。 您也可以按到本機電腦下載所有佈建設定檔**下載所有設定檔**按鈕

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![下載佈建的設定檔區段](apple-account-management-images/image9.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![下載佈建的設定檔區段](apple-account-management-images/prov4.png)

-----

## <a name="ios-bundle-signing"></a>iOS 套件組合簽署

如需有關您的應用程式部署至裝置，請參閱[裝置佈建](~/ios/get-started/installation/device-provisioning/index.md)指南。

## <a name="troubleshooting"></a>疑難排解

### <a name="view-details-dialog-is-empty"></a>是空的檢視詳細資料對話方塊

這是目前已知的問題，相關 bug [#53906](https://bugzilla.xamarin.com/show_bug.cgi?id=53906)。 請確定您使用最新的穩定版本的 Visual Studio for Mac

### <a name="if-you-are-experiencing-issues-logging-in-your-account-please-try-the-following"></a>如果您遇到問題，在您的帳戶登入，請嘗試下列動作：

* 開啟 keychain 應用程式，並在 [類別目錄] 下選取*密碼*。 搜尋`deliver.`，並刪除所有的項目。

### <a name="error-adding-account-please-sign-in-with-an-app-specific-password"></a>「 新增帳戶時發生錯誤。 請使用應用程式特定的密碼登入 」

這是因為您帳戶上啟用 2 個因素驗證。 請確定您使用最新的穩定版本的 Visual Studio for Mac

### <a name="failed-to-create-new-certificate"></a>無法建立新的憑證
「 您已達到這種憑證的限制 」

![憑證 [限制] 對話方塊](apple-account-management-images/image10.png)

已產生的憑證允許的最大數目。 若要修正此問題，瀏覽至[Apple Developer Center](https://developer.apple.com/account/ios/certificate/distribution)及撤銷其中一個生產環境憑證。

## <a name="known-issues"></a>已知問題

* 散發佈建設定檔預設會以 App Store 為目標。 內部作業或臨機操作設定檔應該手動予以建立。
