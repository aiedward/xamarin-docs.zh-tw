---
title: 使用 Xamarin.iOS 中的功能
description: 為應用程式新增功能通常需要額外的佈建設定。 本指南說明所有功能所需的設定。
ms.prod: xamarin
ms.assetid: 98A4676F-992B-4593-8D38-6EEB2EB0801C
ms.technology: xamarin-ios
author: asb3993
ms.author: amburns
ms.date: 05/06/2018
ms.openlocfilehash: c897b1f5fbdf950e6858d7b73ebed60049f60e8e
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34785553"
---
# <a name="working-with-capabilities-in-xamarinios"></a>使用 Xamarin.iOS 中的功能

_為應用程式新增功能通常需要額外的佈建設定。_ 本指南說明所有功能所需的設定。

Apple 為開發人員提供「功能」(通常稱為「應用程式服務」) 來延伸 iOS 應用程式的功能及擴大其用途範圍。 這些功能可讓開發人員在其應用程式中加入更深的平台功能整合，例如：能夠從應用程式、額外的裝置服務 (例如 Siri) 等起始金錢交易。
這些功能可以與 Xamarin.iOS 專案搭配使用。 以下提供完整的服務清單說明：

* 應用程式群組
* 相關聯的網域
* 資料保護
* Game Center
* HealthKit
* HomeKit
* 無線配件組態
* iCloud
* App 內購買
* Inter-App 音訊
* Apple Pay
* 電子錢包
* 推播通知
* 個人 VPN
* Siri
* 地圖
* 背景模式
* Keychain 共用
* 網路延伸
* 作用區組態
* 多重路徑
* NFC 標籤讀取


您可以透過 Visual Studio for Mac 和 Visual Studio 2017 啟用功能，也可以在 Apple Developer Portal (Apple 開發人員入口網站) 中以手動方式啟用功能。 有些功能 (例如「電子錢包」、Apple Pay 及 iCloud) 需要額外的「應用程式識別碼」組態。

本指南說明如何在 Visual Studio 中自動以及透過開發人員中心手動，在您應用程式中啟用這每一項「應用程式服務」，包括可能需要的任何額外設定。 

## <a name="adding-app-services"></a>新增應用程式服務

若要使用功能，應用程式必須具有有效的佈建設定檔，其中包含已啟用正確服務的「應用程式識別碼」。 建立此佈建設定檔時，可以在 Visual Studio for Mac 和 Visual Studio 2017 中自動建立，也可以在 Apple Developer Center (Apple 開發人員中心) 中以手動方式建立。

本節說明如何使用 Visual Studio 的自動化佈建或開發人員中心來啟用大多數功能。 有些功能 (例如「電子錢包」、iCloud、Apple Pay 及「應用程式群組」) 需要額外的設定。 在相鄰的指南中將會詳細說明這些功能。

> [!IMPORTANT]
> 並非所有功能都可以使用自動化佈建來新增和管理。 以下清單包含支援的功能：
>
>* HealthKit 
>* HomeKit 
>* 個人 VPN 
>* 無線配件組態 
>* Inter-App 音訊 
>* SiriKit 
>* 作用區 
>* 網路延伸 
>* NFC 標籤讀取
>* 多重路徑 
>
>目前不支援「推播通知」、Game Center、「App 內購買」、「地圖」、「Keychain 共用」、「相關聯的網域」及「資料保護」功能。 若要新增這些功能，請使用手動佈建並依照[開發人員中心](#devcenter)一節中的步驟進行操作。

## <a name="using-the-ide"></a>使用 IDE

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

在 Visual Studio for Mac 中，功能會新增至 **Entitlements.plist**。 若要新增功能，請使用下列步驟：

1. 開啟您 iOS 應用程式的 **Info.plist** 檔案，並從下拉式方塊選取 [自動化佈建] 配置和您的**小組**。 如果需要協助，請依照[自動佈建](~/ios/get-started/installation/device-provisioning/automatic-provisioning.md)指南中的步驟進行操作：

    ![[自動管理簽署] 選項](images/manage-signing.png)

2. 開啟 **Entitlements.plist** 檔案，然後選取您想要新增的功能：

    ![將功能新增至 entitlements.plist 檔案](images/image17.png)

    選取功能會執行兩項工作：
    * 將該功能新增至您的「應用程式識別碼」
    * 將權利的成對「機碼/值」新增至您的 Entitlements.plist 檔案。

    當執行完這些工作時，Visual Studio for Mac 會藉由顯示下列成功訊息來告知您：

    ![將功能新增至 entitlements.plist 檔案](images/image18.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

功能會新增至 **Entitlements.plist**。 若要在 Visual Studio 2017 中新增功能，請使用下列步驟：

1. 依[與 Mac 配對](~/ios/get-started/installation/windows/connecting-to-mac/index.md)指南所述配對 Visual Studio 2017 和 Mac。

2. 選取 [專案] > [佈建內容] 開啟 [佈建] 選項

3. 從下拉式方塊選取 [自動化佈建] 配置和您的**小組**。 如果需要協助，請依照[自動佈建](~/ios/get-started/installation/device-provisioning/automatic-provisioning.md)指南中的步驟進行操作：

    ![[自動管理簽署] 選項](images/manage-signing-vs.png)

4. 開啟 **Entitlements.plist** 檔案，然後選取您想要新增的功能。 儲存檔案。

    儲存 **Entitlement.plist** 會執行兩項工作：

    * 將該功能新增至您的「應用程式識別碼」
    * 將權利的成對「機碼/值」新增至您的 Entitlements.plist 檔案。

-----


<a name="devcenter" />

## <a name="using-the-developer-center"></a>使用開發人員中心

使用開發人員中心是一個兩步驟的流程，需要建立「應用程式識別碼」，然後使用該「應用程式識別碼」來建立佈建設定檔。 這些步驟如下所述。

### <a name="creating-an-app-id-with-an-app-service"></a>建立具有應用程式服務的應用程式識別碼

1.  在 Mac (如果使用 Windows 電腦，則為組建主機 Mac) 上瀏覽至 [Apple Developer Center](https://developer.apple.com/account) \(Apple 開發人員中心\) 並登入。
2.  選取 [Certificates, Identifiers, and Profiles] \(憑證、識別碼及設定檔\)：

    ![Apple Developer Center (Apple 開發人員中心)](images/image5.png)

3.  在 [Identifiers] \(識別碼\) 底下，選取 [App IDs] \(應用程式識別碼\)：

    ![在 Developer Center (開發人員中心) 中選取 [App ID] \(應用程式識別碼\)](images/image6.png)

4.  按右上角的 [+] 按鈕來建立新的 App ID (應用程式識別碼)。
5.  輸入 App ID (應用程式識別碼) 描述、選取 [Explicit App ID] \(明確的應用程式識別碼\)，然後以 `com.domain.appname` 格式輸入 [Bundle ID] \(套件組合識別碼\)。 這個 Bundle ID (套件組合識別碼) 應該與您專案中的套件組合識別碼相符：

    ![新增 App ID (應用程式識別碼) 詳細資料](images/image7.png)

6.  在 [App Services] \(應用程式服務\) 底下，選取您應用程式中所需的服務：

    ![App Services (應用程式服務) 選取頁面](images/image8.png)

7.  按 [Continue] \(繼續\)。
8.  確認您的 App ID (應用程式識別碼)。 每個服務將會處於下列其中一種狀態：[Enabled] \(已啟用\)、[Disabled] \(已停用\) 或 [Configurable] \(可設定\)，如下所示。 如果是 [Enabled] \(已啟用\)，表示已可在佈建設定檔中使用該服務。 如果是 [Configurable] \(可設定\)，表示此功能需要進行額外的設定。 稍後的小節中將會提供這些額外步驟的更詳細說明。

    ![App ID (應用程式識別碼) 確認](images/image9.png)

9.  按一下 [Register] \(註冊\)，然後按一下 [Done] \(完成\)。 新建立的 App ID (應用程式識別碼) 應該會顯示在 [iOS App IDs] \(iOS 應用程式識別碼\) 清單中。


<a name="provisioningprofile" />

### <a name="creating-a-provisioning-profile"></a>建立佈建設定檔

現在，請建立包含此 App ID (應用程式識別碼) 的佈建設定檔。 請依照下列步驟：

1.  在 Apple Developer Center (Apple 開發人員中心) 中，瀏覽至 [Provisioning Profiles] \(佈建設定檔\) > [All] \(全部\)：

    ![[Provisioning Profile] \(佈建設定檔\) 區段](images/image10.png)

2.  按右上角的 [+] 按鈕來建立新的佈建設定檔。
3.  選取所需的佈建設定檔類型，然後按一下 [Continue] \(繼續\)：

    ![選取 Provisioning Profile (佈建設定檔)](images/image11.png)

4.  從下拉式清單中，選取在上述步驟中建立的 App ID (應用程式識別碼)，然後按 [Continue] \(繼續\)：

    ![選取 App ID (應用程式識別碼)](images/image12.png)

5.  選取用來簽署應用程式的憑證，然後按 [Continue] \(繼續\)：

    ![選取 Certificate (憑證)](images/image13.png)

6.  選取要包含在此設定檔中的裝置，然後按 [Continue] \(繼續\)：

    ![為 Provisioning Profile (佈建設定檔) 選取裝置](images/image14.png)

7.  為設定檔命名以便識別設定檔，然後按 [Continue] \(繼續\) 來產生設定檔：

    ![為 Provisioning Profile (佈建設定檔) 命名](images/image15.png)

8.  按 [Download] \(下載\) 按鈕來下載它，然後在 Finder 中按兩下該檔案來安裝佈建設定檔。

9. 如果您要使用 Visual Studio，請確認選取 [手動佈建] 選項。

10. 在 Visual Studio for Mac / Visual Studio 中，瀏覽至 [專案選項] > [套件組合簽署]，然後將佈建設定檔設定為剛才建立的佈建設定檔：

    ![Visual Studio for Mac 專案選項](images/image16.png)

> [!IMPORTANT]
> 您可能也需要在 Entitlement.plist 檔案中設定權利金鑰，以及在 Info.plist 檔案中設定隱私金鑰。 如需有關這些權利的詳細資訊，請參閱[使用權利](~/ios/deploy-test/provisioning/entitlements.md)指南。

<a name="nextsteps" />

## <a name="next-steps"></a>後續步驟

在伺服器端啟用某項「功能」之後，還需要進行一些工作，才能讓您的應用程式使用功能。 以下清單說明可能需要進行的額外步驟：

*   在您的應用程式中使用架構命名空間。
*   為您的應用程式新增必要的權利。 如需有關所需權利及如何新增這些權利的詳細資訊，請參閱[權利簡介](~/ios/deploy-test/provisioning/entitlements.md)指南。

<a name="troubleshooting" />

## <a name="troubleshooting-capabilities"></a>針對功能進行疑難排解

以下清單詳細說明在開發已啟用某項應用程式服務的應用程式時，會造成障礙的最常見問題。

-   確定已在 Apple Developer Portal (Apple 開發人員入口網站) 的 [Certificates, IDs & Profiles] \(憑證、識別碼及設定檔\) 區段中，適當建立並註冊正確的識別碼。
-   確定已將服務新增至應用程式 (或擴充功能) 的識別碼，並且已將服務設定為使用上述在 Apple Developer Portal (Apple 開發人員入口網站) 的 [Certificates, IDs & Profiles] \(憑證、識別碼及設定檔\) 中建立的 App Group (應用程式群組)/Merchant ID (商家識別碼)/Container (容器)。
-   確定已安裝「佈建設定檔」和「應用程式識別碼」，並且應用程式的 **Info.plist** (位於「Xamarin 專案」中) 使用上述設定的其中一個「應用程式識別碼」。
-   確定應用程式的 **Entitlements.plist** 檔案 (位於「Xamarin 專案」中) 已啟用正確的服務。
-   確定 info.plist 中已設定正確的隱私機碼
-   在應用程式的 [iOS 套件組合簽署] 中，確定 [自訂權利] 已設定為 **Entitlements.plist**。 這「不是」「偵錯」和「iOS 模擬器」組建的預設設定。

<a name="summary" />

## <a name="summary"></a>總結

本指南說明了「功能」(或稱「應用程式服務」)，也描述了如何在 Visual Studio 和 Apple Developer Center (Apple 開發人員中心) 中啟用這些功能。 此外，也詳細說明了如何設定更複雜的服務，例如「電子錢包」、iCloud、Apple Pay 及「應用程式群組」。 最後，還涵蓋了取得設定和簡單疑難排解選項的後續步驟。