---
title: 使用權利
description: 權利是特殊的應用程式功能和安全性權限，其授與對象是已正確設定來使用這些功能和權限的應用程式。
ms.topic: article
ms.prod: xamarin
ms.assetid: 8A3961A2-02AB-4228-A41D-06CB4108D9D0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/15/2017
ms.openlocfilehash: 8b5c88f4af3087f980b586113c274016935585c0
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/22/2018
---
# <a name="working-with-entitlements"></a>使用權利

_權利是特殊的應用程式功能和安全性權限，其授與對象是已正確設定來使用這些功能和權限的應用程式。_

在 iOS 中，應用程式會在「沙箱」中執行，沙箱會提供一組規則，可限制應用程式與特定系統資源或使用者資料之間的存取權。 「權利」可用來要求系統擴充沙箱，以便為您的應用程式提供額外的功能。

若要延伸您應用程式的功能，必須在應用程式的 Entitlements.plist 檔案中提供權利。 您只能延伸特定功能，這些功能都列在[使用功能](~/ios/deploy-test/provisioning/capabilities/index.md)指南中，並且會在[下方](#keyreference)提供說明。 權利會以成對的「機碼/值」形式傳遞給系統，且通常每個功能只需要一對。 特定的「機碼」和「值」會在本指南稍後的[權利機碼參考](#keyreference)一節中說明。
Visual Studio for Mac 和 Visual Studio 提供一個清楚的介面，可透過 Entitlements.plist 編輯器在 Xamarin.iOS 應用程式中新增權利。
本指南將介紹 Entitlements.plist 編輯器及其用法。 此外，本指南也提供可針對每項功能新增至 iOS 專案中的所有權利參考。

## <a name="entitlements-and-provisioning"></a>權利和佈建


Entitlements.plist 檔案可用來指定權利，以及用來簽署應用程式套件組合。

不過，將會需要某些額外的佈建，以確保以正確方式對應用程式進行程式碼簽署。 所使用的佈建設定檔必須包含已啟用所需功能的「應用程式識別碼」。 如需了解如何進行這項操作，請參閱[使用功能](~/ios/deploy-test/provisioning/capabilities/index.md)指南。

> [!IMPORTANT]
> Entitlements.plist 檔案可協助為使用功能的應用程式填入正確的屬性，但無法產生佈建設定檔，因為它並未連結至 Apple 開發人員帳戶。 您將仍然需要使用開發人員入口網站來產生佈建設定檔，以部署和散發應用程式。

## <a name="set-entitlements-in-a-xamarinios-project"></a>在 Xamarin.iOS 專案中設定權利

定義「應用程式識別碼」時，除了選取並設定必要的應用程式服務之外，也必須藉由編輯 **Info.plist** 和 **Entitlements.plist** 檔案，在 Xamarin.iOS 專案中設定權利。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

若要在 Visual Studio for Mac 中設定權利，請執行下列動作：

1. 在 [方案總管]中，按兩下 [Info.plist] 檔案以開啟它進行編輯。
2. 在 [iOS 應用程式目標] 區段中，填入應用程式的名稱，然後輸入定義「應用程式識別碼」時所建立的**套件組合識別碼**：

  ![](entitlements-images/servicexs01.png "輸入套件組合識別碼")

3. 儲存對 **Info.plist** 檔案所做的變更。
4. 在 [方案總管] 中，按兩下 [Entitlements.plist] 檔案以開啟它進行編輯：

    ![](entitlements-images/servicexs02.png "編輯權利")

5. 選取並設定 Xamarin.iOS 應用程式所需的一切權利，使其與建立「應用程式識別碼」時所定義的設定相符。
6. 儲存對 **Entitlements.plist** 檔案所做的變更。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要在 Visual Studio 中設定權利，請執行下列動作：

1. 在 [方案總管] 中，於 [Info.plist] 上按一下滑鼠右鍵，選取 [開啟方式] 和 [屬性清單編輯器] 檔案以開啟它進行編輯。
2. 在 [iOS 應用程式目標] 區段中，填入應用程式的名稱，然後輸入定義「應用程式識別碼」時所建立的**套件組合識別碼**：

  ![](entitlements-images/servicevs01.png "設定套件組合識別碼")

3. 儲存對 **Info.plist** 檔案所做的變更。
4. 在 [方案總管] 中，於 [Entitlements.plist] 上按一下滑鼠右鍵，選取 [開啟方式] 和 [屬性清單編輯器] 以開啟它進行編輯：

    ![](entitlements-images/servicevs02.png "編輯權利")

    或者，按兩下 [Entitlements.plist] 檔案將會開啟「XML 來源」編輯器，可讓您設定權利屬性和機碼值，如下面的[權利參考](#keyreference)一節所述。

5. 選取並設定 Xamarin.iOS 應用程式所需的一切權利，使其與建立「應用程式識別碼」時所定義的設定相符。
6. 儲存對 **Entitlements.plist** 檔案所做的變更。


-----

<a name="add-new" />

## <a name="adding-a-new-entitlementsplist-file"></a>新增 Entitlements.plist 檔案

權利會透過 Entitlements.plist 檔案新增至應用程式。 Xamarin.iOS 專案預設會包含此檔案，但較舊的專案可能不會有此檔案。

若要將 Entitlements.plist 檔案新增至您的 Xamarin.iOS，請執行下列動作：

1.  在專案檔上按一下滑鼠右鍵，然後瀏覽至 [新增] > [新增檔案]：

    ![[新增檔案] 操作功能表](entitlements-images/image1.png)
2.  在 [新增檔案] 對話方塊中，選取 [iOS] > [屬性清單]，然後將它命名為 Entitlements：

    ![[新增檔案] 對話方塊](entitlements-images/image2.png)

<a name="keyreference" />

## <a name="entitlement-key-reference"></a>權利機碼參考

您可以透過 Entitlements.plist 編輯器的 [來源] 面板來新增權利機碼。 使用 Entitlements.plist 編輯器時，通常會新增必要的機碼，但以下會列出這些機碼以供參考。

### <a name="wallet"></a>電子錢包

*   **描述**：正式名稱為 Passbook，電子錢包是儲存和管理票卡的應用程式。 這些票卡可能是信用卡、商店卡、登機證或票證。

    - **票卡類型識別碼**
        * **機碼**：com.apple.developer.pass-type-identifiers
        * **字串**：`$(TeamIdentifierPrefix)*`

* **附註**：
    - 這可讓您的應用程式允許所有票卡類型。 若要限制您的應用程式而只允許部分小組票卡類型，請將字串值設定為：`$(TeamIdentifierPrefix)pass.$(CFBundleIdentifier)`

    其中 pass.$(CFBundleIdentifier) 是[上述](~/ios/platform/passkit.md)建立的「票卡識別碼」

<a name="icloud" />

### <a name="icloud"></a>iCloud

*   **描述**：iCloud 提供一個簡便的方式，可供 iOS 使用者儲存其內容及在裝置之間共用該內容。 有四種方法可供開發人員使用 iCloud 來為其使用者提供儲存方式：「機碼/值」儲存體、UIDocument 儲存體、CoreData，以及直接使用 CloudKit 來為個人檔案和目錄提供儲存體。 如需有關這些方法的詳細資訊，請參閱《iCloud 簡介》指南。

    - **iCloud 文件和 CloudKit**
        - **機碼**：com.apple.developer.ubiquity-container-identifiers
        - **字串**：`$(TeamIdentifierPrefix)$(CFBundleIdentifier)`
    - **iCloud KeyValue 儲存體**
        - **機碼**：com.apple.developer.ubiquity-kvstore-identifier
        - **字串**：`$(TeamIdentifierPrefix)$(CFBundleIdentifier)`

* **附註**：
    - 若要尋找 `$(TeamIdentifierPrefix)` 字串，請登入 developer.apple.com，然後瀏覽 [Member Center] \(會員中心\) > [Your Account] \(您的帳戶\) > [Developer Account Summary] \(開發人員帳戶摘要\) 以取得您的 Team ID (小組識別碼) (如果是單一開發人員，則為 Individual ID (個人識別碼))。 它將是一個 10 個字元的字串 (例如 A93A5CM278)。
    - `$(CFBundleIdentifier)` 字串的開頭為 `iCloud`，在建立 iCloud 容器 (依據[使用功能](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md)指南中的步驟) 時，會設定此字串。
    - 可以使用 $`(TeamIdentifierPrefix)` 和 `$(CFBundleIdentifier)` 預留位置，在建置階段將會以正確的值取代這些預留位置。

### <a name="app-groups"></a>應用程式群組

- **描述**：「應用程式群組」可讓不同的應用程式 (或應用程式及其擴充功能) 存取共用檔案儲存體位置。

    - **機碼**：com.apple.security.application-groups
    - **字串**：group.$(CFBundleIdentifier)

<a name="apple-pay" />

### <a name="apple-pay"></a>Apple Pay

- **描述**：Apple Pay 可讓使用者透過其 iOS 裝置支付實體產品的費用。
    - **機碼**：com.apple.developer.in-app-payments
    - **字串**：merchant.your.mechantid

### <a name="push-notifications"></a>推播通知

- **機碼**：aps-environment
- **字串**：`production` 或 `development`

### <a name="siri"></a>Siri

- **描述**：SiriKit 可讓 iOS 應用程式使用「應用程式擴充功能」(App Extensions) 及新的「意圖和意圖」(Intents and Intents) UI 架構，提供 iOS 裝置上 Siri 和「地圖」應用程式可存取的服務。 如需詳細資訊，請參閱《SiriKit 簡介》指南。
    - **機碼**：com.apple.developer.siri

### <a name="personal-vpn"></a>個人 VPN

- **機碼**：com.apple.developer.networking.vpn.api
- **字串**：allow-vpn

### <a name="keychain-sharing"></a>Keychain 共用

- **描述**：Keychain 共用可讓應用程式開發人員將儲存在裝置 Keychain 中的密碼，與相同小組的其他應用程式開發人員共用。 藉由在字串中傳遞 Keychain 存取群組識別碼，即可限制存取權。
    - **機碼**：keychain-access-groups
    - **字串**：$(AppIdentifierPrefix) $(CFBundleIdentifier)

### <a name="inter-app-audio"></a>Inter-App 音訊

- **描述**：「Inter-App 音訊」可讓開發人員在應用程式之間串流音訊。
    - **機碼**：inter-app-audio
    - **布林值**：YES

### <a name="associated-domains"></a>相關聯的網域

- **描述**：應視為通用連結的相關聯網域應該以此權利傳遞。 實作通用連結可讓應用程式與網站之間進行深層連結。 您應該提供應用程式所支援之每個網域的入口，且每個入口的開頭都應該是 `applinks:`
    - **機碼**：com.apple.developer.associated-domains
    - **字串**：webcredentials:example.com

### <a name="data-protection"></a>資料保護

- **描述**：啟用資料保護可使用內建的加密硬體，以加密格式儲存您應用程式中使用的敏感性資料。 保護層級預設會設定為完整保護 (只有在裝置已解除鎖定的情況下，才能存取檔案)。
    - **機碼**：com.apple.developer.default-data-protection
    - **字串**：NSFileProtectionComplete

### <a name="homekit"></a>HomeKit

- **描述**：HomeKit 架構提供一個可從 iOS 裝置設定、配置及管理所支援家用自動化裝置的平台。 如需有關使用 HomeKit 的詳細資訊，請參閱《HomeKit 簡介》指南。
    - **機碼**com.apple.developer.homekit
    - **布林值**：YES

### <a name="healthkit"></a>HealthKit

- **描述**：HealthKit 是在 iOS 8 中導入的架構，可為健康相關的資訊提供一個集中、協調及安全的資料存放區。 如需有關使用 HealthKit 的詳細資訊，請參閱《HealthKit 簡介》指南。
    - **機碼**：com.apple.developer.healthkit
    - **布林值**：YES

### <a name="wireless-accessory-configuration"></a>無線配件組態

- **描述**：使用「無線配件組態」可讓您的應用程式設定 Wi-Fi 配件
    - **機碼**：com.apple.external-accessory.wireless-configuration
    - **布林值**：YES

## <a name="summary"></a>總結

本指南介紹權利及如何在 Visual Studio for Mac 和 Visual Studio 使用它們。 此外，也提供每項功能的成對「機碼/值」參考。