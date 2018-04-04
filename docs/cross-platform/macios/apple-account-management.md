---
title: Apple 帳戶管理
ms.prod: xamarin
ms.assetid: 71388B83-699B-4E42-8CBF-8557A4A3CABF
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 04/05/2017
ms.openlocfilehash: 21af0ef09644f39f9be42788b3d8f4977a2143d3
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="apple-account-management"></a>Apple 帳戶管理

Apple 帳戶管理介面提供方法來檢視所有與 Apple id。 相關聯的開發團隊 它也可讓您藉由顯示一份檢視每個小組的更多詳細_的簽署識別_和_佈建的設定檔_您的電腦上所安裝。

您的 Apple ID 的驗證會在命令列執行[fastlane](https://fastlane.tools/)。 fastlane 必須安裝在您成功通過驗證的電腦上。 Fastlane 及安裝方式的詳細資訊會詳細說明[fastlane](~/ios/deploy-test/provisioning/fastlane/index.md)輔助線。

適用於 Mac 的 Visual Studio 中的 [Apple 帳戶] 對話方塊可讓您執行下列作業：

* **建立及管理憑證** 
* **建立及管理佈建設定檔** 

本指南說明如何執行這項操作的詳細資訊。

您也可以使用 iOS 套件組合簽署工具執行下列操作：

* **將新的簽署識別加入至現有的設定檔** 
* **佈建的新裝置** 

如需有關使用這些功能的詳細資訊，請參閱[裝置佈建](~/ios/get-started/installation/device-provisioning/index.md)指南。
️
## <a name="requirements"></a>需求

Apple 帳戶管理位於 Visual Studio for mac。 它不是目前可用在 Visual Studio for Windows。

您必須擁有 Apple 開發人員帳戶，才能使用這項功能。 Apple 開發人員帳戶的詳細資訊可用於[裝置佈建](~/ios/get-started/installation/device-provisioning/index.md)指南。

- 請確定您已連線至網際網路。 這是因為 fastlane 會直接與 Apple 開發人員入口網站進行通訊。
- 請確定您有[fastlane 工具安裝](~/ios/deploy-test/provisioning/fastlane/index.md#Installation)。
- 請確定您有最新 fastlane 工具從[ https://download.fastlane.tools ](https://download.fastlane.tools)。
- 在開始之前，請務必接受任何使用者的授權合約[開發人員入口網站](https://developer.apple.com/account/)。

## <a name="adding-an-apple-developer-account"></a>新增 Apple 開發人員帳戶

1. 若要開啟 [帳戶管理] 對話方塊，請移至**Visual Studio > 喜好設定 > Apple 開發人員帳戶**:

    ![Apple 開發人員帳戶選項](apple-account-management-images/image1.png)

2. 按**+**按鈕以顯示在對話方塊中，符號，如底下所述： 

    ![fastlane 對話方塊。](apple-account-management-images/image2.png)

4. 輸入您的 Apple ID 和密碼，然後按一下**登入** 按鈕。 在此電腦上，這樣會將您的認證儲存在安全的金鑰鏈。 [fastlane](~/ios/deploy-test/provisioning/fastlane/index.md)用來安全地處理您的認證，並將其傳遞至 Apple 開發人員入口網站。
 
5. 選取**永遠允許**上 [警示] 對話方塊，讓 Visual Studio 來使用您的認證：

    ![](apple-account-management-images/image4.png)

6. 已成功新增您的帳戶，您會看到您的 Apple ID 和任何小組屬於您的 Apple ID。

    ![](apple-account-management-images/image5.png)

7. 選取任何小組，然後按**檢視詳細資料...** 按鈕。 這會顯示所有的簽署識別和佈建的設定檔安裝在您的電腦上的清單：

    ![](apple-account-management-images/image6.png)


<a name="managing" />


## <a name="managing-signing-identities-and-provisioning-profiles"></a>管理的簽署識別和佈建設定檔

小組的詳細資料 對話方塊顯示的簽署識別，依類型的清單。 **狀態**欄會提示您是否憑證： 

* **有效**– 簽署識別 （憑證和私密金鑰） 安裝在電腦上，且它尚未過期。

* **不在 金鑰鏈**– Apple 的伺服器上沒有有效的簽署識別。 若要安裝這在您的電腦上，它必須匯出從另一部電腦。 您無法從 Apple 開發人員入口網站下載簽署識別，因為它不會包含私密金鑰。

* **私用金鑰已遺失**– 使用沒有私密金鑰的憑證安裝在 keychain。

* **過期**– 憑證已過期。 您應該將它移除從您的金鑰鏈。

  ![](apple-account-management-images/image7.png)

## <a name="create-a-signing-identities"></a>建立的簽署識別

若要建立新的簽署識別，請選取**建立新憑證**下拉式按鈕，然後選取您需要的類型。 如果您有正確的權限新的簽署身分識別會出現幾秒之後。

如果下拉式清單中的選項會呈現灰色，而且未選取，如下所示，這表示您沒有正確的小組的權限才能建立此類型的憑證。

![](apple-account-management-images/image8.png)

## <a name="download-provisioning-profiles"></a>下載佈建設定檔

小組的詳細資料 對話方塊也會顯示一份所有連接到您的開發人員帳戶的佈建設定檔。 您也可以按到本機電腦下載所有的佈建設定檔**下載所有設定檔**按鈕

![](apple-account-management-images/image9.png)

## <a name="ios-bundle-signing"></a>iOS 套件組合簽署

您的應用程式部署至裝置的資訊，請參閱[裝置佈建](~/ios/get-started/installation/device-provisioning/index.md)指南。

## <a name="troubleshooting"></a>疑難排解

### <a name="view-details-dialog-is-empty"></a>是空的檢視詳細資料 對話方塊

這是目前已知的問題，與 bug 相關[#53906](https://bugzilla.xamarin.com/show_bug.cgi?id=53906)。 請確定您使用最新穩定版本的 Visual Studio for Mac

### <a name="if-you-are-experiencing-issues-logging-in-your-account-please-try-the-following"></a>如果您遇到的問題登入您的帳戶，請嘗試下列方法：

* 開啟金鑰鏈應用程式，然後在 類別目錄選取*密碼*。 搜尋`deliver.`，並刪除所有項目。

### <a name="error-adding-account-please-sign-in-with-an-app-specific-password"></a>「 新增帳戶時發生錯誤。 請使用應用程式專用密碼登入 」

這是因為 2 authentication 已啟用您的帳戶。 請確定您使用最新穩定版本的 Visual Studio for Mac

### <a name="failed-to-create-new-certificate"></a>無法建立新的憑證
「 您已達到這種憑證的限制 」

![](apple-account-management-images/image10.png)

已產生的憑證，允許的數目上限。 若要修正此問題，瀏覽至[Apple 開發人員中心](https://developer.apple.com/account/ios/certificate/distribution)及一個實際執行憑證撤銷。

## <a name="known-issues"></a>已知問題

* 有時 [檢視詳細資料] 對話方塊可能需要相當長的時間來擷取簽署識別和設定檔。
* 通常焦點不會傳回至 Visual Studio for Mac 之後輸入詳細資料，您不是要加入的帳戶。 如果這種情況，再試一次程序。
* 在 Visual Studio for Mac 中建立的佈建設定檔將不會考慮您專案中所選取的權利 (Entitlements.plist)。 在未來的 IDE 版本中，將會新增這項功能。
* 散發佈建設定檔預設會以 App Store 為目標。 內部作業或臨機操作設定檔應該手動予以建立。
