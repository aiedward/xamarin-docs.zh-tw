---
title: Apple 帳戶管理
description: 本檔說明如何使用 Visual Studio for Mac 和 Visual Studio 2019 中的 Apple 帳戶管理功能。
ms.prod: xamarin
ms.assetid: 71388B83-699B-4E42-8CBF-8557A4A3CABF
author: conceptdev
ms.author: crdun
ms.date: 05/06/2018
ms.openlocfilehash: 9629d775b45951279178dffa3600e7cd5073dd38
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290622"
---
# <a name="apple-account-management"></a>Apple 帳戶管理

Apple 帳戶管理介面提供一種方式，可讓您查看與 Apple ID 相關聯的所有開發小組。 它也可讓您藉由顯示一份檢視每個小組的更多詳細_簽署身分識別_並_佈建設定檔_安裝在電腦上。

您的 Apple ID 驗證是在命令列上使用[fastlane](https://fastlane.tools/)來執行。 您的電腦上必須安裝 fastlane，才可成功進行驗證。 fastlane 並安裝方式的更多有關詳述 [fastlane](~/ios/deploy-test/provisioning/fastlane/index.md) 輔助線。

[Apple 帳戶] 對話方塊可讓您執行下列動作：

- **建立和管理憑證**
- **建立和管理布建設定檔**

本指南將說明如何執行這項操作的相關資訊。

> [!NOTE]
> Xamarin 的 Apple 帳戶管理工具只會顯示付費 Apple 開發人員帳戶的相關資訊。 若要瞭解如何在沒有付費 Apple developer 帳戶的裝置上測試應用程式，請參閱免費布建[的 Xamarin iOS 應用程式](~/ios/get-started/installation/device-provisioning/free-provisioning.md)指南。

您也可以使用 iOS 自動布建工具，自動建立和管理您的簽署身分識別、應用程式識別碼和布建設定檔。 如需使用這些功能的詳細資訊，請參閱[裝置](~/ios/get-started/installation/device-provisioning/index.md)布建指南。

## <a name="requirements"></a>需求

Apple 帳戶管理適用于 Visual Studio for Mac、Visual Studio 2019 和 Visual Studio 2017 （15.7 版和更高版本）。

您必須擁有 Apple 開發人員帳戶，才能使用這項功能。 有關 Apple developer 帳戶的詳細資訊可在[裝置](~/ios/get-started/installation/device-provisioning/index.md)布建指南中取得。

- 請確定您已連線到網際網路。 這是因為 fastlane 會直接與 Apple Developer 入口網站進行通訊。
- 請確定您已[安裝 fastlane 工具](~/ios/deploy-test/provisioning/fastlane/index.md#Installation)。
- 請確定您有來自[https://download.fastlane.tools](https://download.fastlane.tools)的最新 fastlane 工具。
- 開始之前，請務必接受[開發人員入口網站](https://developer.apple.com/account/)中的任何使用者授權合約。

## <a name="adding-an-apple-developer-account"></a>新增 Apple 開發人員帳戶

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 若要開啟 [帳戶管理] 對話方塊，請移至**Visual Studio > 喜好設定 > Apple 開發人員帳戶**：

    ![Apple 開發人員帳戶選項](apple-account-management-images/image1.png)

2. 按下 **+** 按鈕以顯示登入 對話方塊，如下所述： 

    ![fastlane 對話方塊。](apple-account-management-images/image2.png)

3. 輸入您的 Apple ID 和密碼，然後按一下 [登**入**] 按鈕。 這會將您的認證儲存在這部電腦的安全 Keychain 中。 [fastlane](~/ios/deploy-test/provisioning/fastlane/index.md)是用來安全地處理您的認證，並將其傳遞給 Apple 的開發人員入口網站。
 
4. 選取 [警示] 對話方塊上的 [**永遠允許**]，以允許 Visual Studio 使用您的認證：

    ![[永遠允許警示] 對話方塊](apple-account-management-images/image4.png)

5. 一旦成功新增您的帳戶，您就會看到您的 Apple ID 和您的 Apple ID 所屬的任何小組。

    ![已新增帳戶的 Apple 開發人員帳戶對話方塊](apple-account-management-images/image5.png)

6. 選取任何小組，然後按 [ **View Details （詳細資料**）]。 按鈕。 這會顯示一份所有「簽署身分識別和安裝在您的電腦的佈建設定檔」：

    ![顯示您電腦上的簽署身分識別和布建設定檔的 [查看詳細資料] 畫面](apple-account-management-images/image6.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 開始將您的 Apple ID 新增至 Visual Studio 2019 之前，請確定您的開發環境已與[Mac 組建主機配對](~/ios/get-started/installation/windows/connecting-to-mac/index.md)。

1. 若要開啟 [帳戶管理] 視窗，請移至 [工具] [ **> 選項] [> Xamarin > Apple 帳戶**]：

    ![Apple 帳戶選項畫面](apple-account-management-images/prov1.png)

1. 選取 [**新增**] 按鈕，然後輸入您的 Apple ID 和密碼：

    ![使用者名稱和密碼對話方塊](apple-account-management-images/prov1a.png)

1. 一旦成功新增您的帳戶，您就會看到您的 Apple ID 和您的 Apple ID 所屬的任何小組。
 
1. 選取任何小組，然後按 [ **View Details （詳細資料**）]。 按鈕。 這會顯示一份所有「簽署身分識別和安裝在您的電腦的佈建設定檔」：

    ![使用者名稱和密碼對話方塊](apple-account-management-images/prov2.png)

-----


## <a name="managing-signing-identities-and-provisioning-profiles"></a>管理「簽署身分識別和佈建設定檔」

[小組詳細資料] 對話方塊會顯示依類型組織的簽署身分識別清單。 [**狀態**] 欄會建議您是否有憑證： 

- **有效**–簽署身分識別（憑證和私密金鑰）已安裝在您的電腦上，而且尚未過期。

- **不在 Keychain 中**-Apple 伺服器上有有效的簽署身分識別。 若要在您的電腦上安裝此檔案，必須從另一部電腦匯出。 您無法從 Apple 開發人員入口網站下載簽署身分識別，因為它將不會包含私密金鑰。

- **缺少私密金鑰**– keychain 中已安裝沒有私密金鑰的憑證。

- 已**過期**–憑證已過期。 您應該從 keychain 中移除此程式。

  ![小組詳細資料對話方塊資訊](apple-account-management-images/image7.png)

## <a name="create-a-signing-identities"></a>建立簽署身分識別

若要建立新的簽署身分識別，請選取 **Create Certificate** 下拉式按鈕，然後選取您所需要的類型。 如果您有正確的許可權，將會在幾秒後出現新的簽署身分識別。

如果下拉式方塊中的選項呈現灰色且未選取，則表示您沒有正確的小組許可權可以建立這種類型的憑證。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![建立憑證選項](apple-account-management-images/image8.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![建立憑證選項](apple-account-management-images/prov3.png)

-----

## <a name="download-provisioning-profiles"></a>下載布建設定檔

[小組詳細資料] 對話方塊也會顯示所有連接至您開發人員帳戶的布建配置檔案清單。 您可以按 [**下載所有設定檔**] 按鈕，將所有布建設定檔下載到本機電腦

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![下載布建設定檔區段](apple-account-management-images/image9.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![下載布建設定檔區段](apple-account-management-images/prov4.png)

-----

## <a name="ios-bundle-signing"></a>iOS 套件組合簽署

如需將應用程式部署至裝置的相關資訊，請參閱[裝置](~/ios/get-started/installation/device-provisioning/index.md)布建指南。

## <a name="troubleshooting"></a>疑難排解

### <a name="view-details-dialog-is-empty"></a>[View Details] 對話方塊是空的

這是目前與 bug [#53906](https://bugzilla.xamarin.com/show_bug.cgi?id=53906)相關的已知問題。 請確定您使用的是最新穩定版本的 Visual Studio for Mac

### <a name="if-you-are-experiencing-issues-logging-in-your-account-please-try-the-following"></a>如果您在登入帳戶時遇到問題，請嘗試下列步驟：

- 開啟 keychain 應用程式，然後在 [類別] 下選取 [*密碼*]。 `deliver.`搜尋並刪除所有專案。

### <a name="error-adding-account-please-sign-in-with-an-app-specific-password"></a>「新增帳戶時發生錯誤。 請使用應用程式特定的密碼登入」

這是因為您的帳戶已啟用2個要素驗證。 請確定您使用的是最新穩定版本的 Visual Studio for Mac

### <a name="failed-to-create-new-certificate"></a>無法建立新的憑證
「您已達到此類型憑證的限制」

![[憑證限制] 對話方塊](apple-account-management-images/image10.png)

已產生允許的憑證數目上限。 若要修正此問題，請流覽至[Apple 開發人員中心](https://developer.apple.com/account/ios/certificate/distribution)，並撤銷其中一個生產環境憑證。

## <a name="known-issues"></a>已知問題

- 散發佈建設定檔預設會以 App Store 為目標。 內部作業或臨機操作設定檔應該手動予以建立。
