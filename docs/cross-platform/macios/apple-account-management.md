---
title: Apple 帳戶管理
description: 本檔說明如何使用 Visual Studio for Mac 中的 Apple 帳戶管理功能和 Visual Studio 2019。
ms.prod: xamarin
ms.assetid: 71388B83-699B-4E42-8CBF-8557A4A3CABF
author: davidortinau
ms.author: daortin
ms.date: 03/05/2020
ms.openlocfilehash: f7a94f4aa7ec9be02f2365afbb995e9a7d5f3700
ms.sourcegitcommit: d1980b2251999224e71c1289e4b4097595b7e261
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "92928590"
---
# <a name="apple-account-management"></a>Apple 帳戶管理

Visual Studio 中的 Apple 帳戶管理介面提供一種方式，可讓您查看與 Apple ID 相關聯之開發小組的資訊。 它可讓您執行下列作業：

- **新增 Apple 開發人員帳戶**
- **查看簽署憑證和布建設定檔**
- **建立新的簽署憑證**
- **下載現有的布建設定檔**

> [!IMPORTANT]
> Xamarin 的 Apple 帳戶管理工具只會顯示付費 Apple 開發人員帳戶的相關資訊。 若要瞭解如何在沒有付費 Apple 開發人員帳戶的裝置上測試應用程式，請參閱 [適用于 Xamarin iOS 應用程式的免費](~/ios/get-started/installation/device-provisioning/free-provisioning.md) 布建指南。

## <a name="requirements"></a>規格需求

Apple 帳戶管理可在 Visual Studio for Mac、Visual Studio 2019 和 Visual Studio 2017 (15.7 版和更高版本的) 上取得。 您也必須擁有付費的 Apple 開發人員帳戶，才能使用這項功能。 如需 Apple 開發人員帳戶的詳細資訊，請參考 [裝置](~/ios/get-started/installation/device-provisioning/index.md) 布建指南。

> [!NOTE]
> 開始之前，請務必先接受 [Apple Developer 入口網站](https://developer.apple.com/account/)中的任何使用者授權合約。

## <a name="add-an-apple-developer-account"></a>新增 Apple 開發人員帳戶

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 移至 **Visual Studio > 喜好設定 > Apple 開發人員帳戶** ，然後按一下 **+** 按鈕以開啟 [登入] 對話方塊：

    ![Visual Studio for Mac 喜好設定中的 [Apple 開發人員帳戶] 頁面 AScreenshot。](apple-account-management-images/add-account-vsm.png)

2. 輸入您的 Apple ID 和密碼，然後按一下 [登 **入** ]。 這會將您的認證儲存在這部電腦的安全 Keychain 中。

3. 在 [警示] 對話方塊上選取 [ **永遠允許** ]，以允許 Visual Studio 使用您的認證：

    ![一律允許警示對話方塊](apple-account-management-images/image4.png)

4. 成功新增您的帳戶之後，您將會看到您的 Apple ID 和您的 Apple ID 所屬的任何小組：

    ![已新增帳戶的 Apple developer 帳戶對話方塊](apple-account-management-images/image5.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

> [!NOTE]
> 如果您使用 Visual Studio 2017 或 Visual Studio 2019 (16.4 版和更舊的) ，則必須先將您 [的 Mac 組建主機配對](~/ios/get-started/installation/windows/connecting-to-mac/index.md) ，才能繼續進行。

1. 移至 [ **工具 > 選項 > Xamarin > Apple 帳戶** ]，然後按一下 [ **新增** ]：

    ![Visual Studio 選項中 [Apple 帳戶] 頁面的螢幕擷取畫面。](apple-account-management-images/add-account-vsw.png)

2. 輸入您的 Apple ID 和密碼，然後按一下 **[登** 入]。

3. 成功新增您的帳戶之後，您將會看到您的 Apple ID 和您的 Apple ID 所屬的任何小組：

    ![已新增帳戶之開發人員帳戶頁面的螢幕擷取畫面。](apple-account-management-images/accounts-vsw.png)

-----

## <a name="view-signing-certificates-and-provisioning-profiles"></a>查看簽署憑證和布建設定檔

選取小組，然後按一下 [ **查看詳細資料** ]。 開啟對話方塊，以顯示電腦上已安裝的簽署身分識別和布建配置檔案清單。

[小組詳細資料] 對話方塊會顯示簽署身分識別清單，依類型進行組織。 [ **狀態** ] 欄會建議您憑證是否為：

- **有效** –簽署身分識別 (憑證和私密金鑰) 安裝在您的電腦上，且尚未過期。

- **不在 Keychain 中** -Apple 伺服器上有有效的簽署身分識別。 若要在您的電腦上安裝此檔案，必須從另一部電腦匯出。 您無法從 Apple 開發人員入口網站下載簽署身分識別，因為它不會包含私密金鑰。

- **遺漏私密金鑰** – keychain 中已安裝沒有私密金鑰的憑證。

- **過期** -憑證已過期。 您應該從 keychain 中移除此。

  ![小組詳細資料對話方塊資訊](apple-account-management-images/image7.png)

## <a name="create-a-signing-certificate"></a>建立簽署憑證

若要建立新的簽署身分識別，請按一下 [ **建立憑證** ] 開啟下拉式功能表，然後選取您要建立的 [憑證類型](https://help.apple.com/xcode/mac/current/#/dev80c6204ec) 。 如果您有正確的許可權，將會在幾秒鐘後出現新的簽署身分識別。

如果下拉式清單中的選項呈現灰色且未選取，則表示您沒有正確的小組許可權可以建立這種類型的憑證。

## <a name="download-provisioning-profiles"></a>下載布建設定檔

[小組詳細資料] 對話方塊也會顯示所有連接到您的開發人員帳戶的布建配置檔案清單。 您可以按一下 [ **下載所有設定檔** ]，將所有布建設定檔下載到本機電腦。


## <a name="troubleshoot"></a>疑難排解

- 可能需要數小時的時間，才能核准新的 Apple 開發人員帳戶。 您必須等到帳戶獲得核准之後，才能啟用自動布建。

- 如果新增 Apple developer 帳戶失敗並顯示訊息 `Authentication Error: Xcode 7.3 or later is required to continue developing with your Apple ID.` ，請確定您使用的 APPLE ID 具有 Apple 開發人員計畫的有效付費成員資格。 若要使用付費的 Apple 開發人員帳戶，請參閱 [適用于 Xamarin iOS 應用程式的免費](~/ios/get-started/installation/device-provisioning/free-provisioning.md) 布建指南。

- 如果嘗試建立新的簽署憑證失敗，並出現錯誤 `You have reached the limit for certificates of this type` ，則會產生允許的最大憑證數目。 若要修正此問題，請流覽至 [Apple 開發人員中心](https://developer.apple.com/account/ios/certificate/distribution) ，並撤銷其中一個生產憑證。

- 如果您在 Visual Studio for Mac 上登入帳戶時遇到問題，可能的修正方法是開啟 Keychain 應用程式，然後在 [ **類別目錄** ] 下選取 [ **密碼** ]。 搜尋 `deliver.` 並刪除所有找到的專案。

- 如果您的簽署憑證已撤銷，則可以從 Windows 上的下列路徑中移除它： `C:\Users\<user>\AppData\Local\Xamarin\iOS\Provisioning\Certificates` 。

## <a name="known-issues"></a>已知問題

- 散發佈建設定檔預設會以 App Store 為目標。 內部作業或臨機操作設定檔應該手動予以建立。
