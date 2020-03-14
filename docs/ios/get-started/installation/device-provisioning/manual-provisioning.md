---
title: Xamarin.iOS 的手動佈建
description: 成功安裝 Xamarin.iOS 之後，iOS 開發作業的下一步就是佈建您的 iOS 裝置。 本指南會探討使用手動佈建來設定開發憑證和設定檔。
ms.prod: xamarin
ms.assetid: E26ACC94-F4A5-4FF5-B7D4-BE596745A665
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/06/2020
ms.openlocfilehash: 04cb1b9303e571b2a10cdfa621dcd312162e2893
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304860"
---
# <a name="manual-provisioning-for-xamarinios"></a>Xamarin.iOS 的手動佈建

_成功安裝 Xamarin 之後，iOS 開發的下一步就是布建您的 iOS 裝置。本指南會探索如何使用手動布建來設定開發憑證和設定檔。_

> [!NOTE]
> 此頁面上的指示與已付費存取 Apple 開發人員計劃的開發人員相關。 如果您有免費帳戶，請查看[免費佈建](~/ios/get-started/installation/device-provisioning/free-provisioning.md)指南，了解有關裝置上測試的詳細資訊。

## <a name="create-a-development-certificate"></a>建立開發憑證

設定開發裝置的第一個步驟是建立簽署憑證。 簽署憑證是由兩個專案所組成：

- 開發憑證
- 私密金鑰

開發憑證和關聯的[金鑰](#understanding-certificate-key-pairs)對 iOS 開發人員而言非常重要：它們會向 Apple 確立您的身分識別，並將您與指定的裝置和設定檔建立關聯以進行開發，類似於將您的數位簽章放在應用程式上。 Apple 會檢查憑證，以針對允許您部署的裝置控制存取權。

您可以存取 Apple 成員中心的[憑證、識別碼及設定檔](https://developer.apple.com/account/resources/certificates/list) (需要登入) 區段來管理開發小組、憑證及設定檔。 Apple 會要求您必須要有簽署身分識別，才能為裝置或模擬器組建程式碼。  

> [!IMPORTANT]
> 請務必注意，在任一時間，您都只能有兩個 iOS 開發憑證。 如果您需要再多建立任何憑證，將必須撤銷現有的憑證。 任何使用已撤銷之憑證的電腦都將無法簽署其應用程式。

開始手動布建程式之前，您應該確定已在 Visual Studio 中新增 Apple 開發人員帳戶，如[Apple 帳戶管理](~/cross-platform/macios/apple-account-management.md)指南中所述。 新增 Apple 開發人員帳戶之後，請執行下列動作以產生簽署憑證：

1. 移至 Visual Studio 中的 [Apple 開發人員帳戶] 視窗。
    1. Mac： **Visual Studio > 喜好設定 > Apple 開發人員帳戶**
    2. Windows： **> Xamarin > Apple 帳戶的工具 > 選項**

2. 選取小組，然後按一下 [**查看詳細資料**]。
3. 按一下 [**建立憑證**]，然後選取 [ **Apple 開發**] 或 [ **iOS 開發**]。 如果您有正確的許可權，將會在幾秒後出現新的簽署身分識別。

### <a name="understanding-certificate-key-pairs"></a>了解憑證金鑰組

「開發人員設定檔」包含憑證、其相關金鑰，以及該帳戶的任何相關佈建設定檔。 「開發人員設定檔」實際上有兩個版本 — 一個是在 Developer Portal (開發人員入口網站) 上，另一個位於本機 Mac 上。 兩者的差異在於其包含的金鑰類型：入口網站上的設定檔裝載與您憑證相關的所有公開金鑰，而本機 Mac 上的複本則包含所有私密金鑰。 憑證若要有效，金鑰組必須相符。

> [!WARNING]
> 遺失憑證和相關聯的金鑰可能會造成非常干擾，因為它需要撤銷現有的憑證並重新布建任何相關聯的裝置，包括已註冊進行臨機操作部署的裝置。 成功設定「開發憑證」之後，請匯出一份備份複本，並將其儲存在安全的地方。 如需有關此做法的詳細資訊，請參閱 Apple 文件中[維護憑證](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/MaintainingCertificates/MaintainingCertificates.html)指南 \(英文\) 的＜Importing Certificates and Profiles＞(匯入憑證與設定檔) 一節。

<a name="provisioning" />

## <a name="provision-an-ios-device-for-development"></a>布建 iOS 裝置以進行開發

既然您已建立 Apple 的身分識別並具有開發憑證，您必須設定布建設定檔和必要的實體，才能將應用程式部署到 Apple 裝置。 裝置執行的 iOS 必須是 Xcode 所支援的版本 — 可能會需要更新裝置、Xcode 或兩者都更新。

<a name="adddevice" />

## <a name="add-a-device"></a>新增裝置

建立佈建設定檔以進行開發時，必須指出哪些裝置可以執行該應用程式。 為了能夠這樣做，每個日曆年度最多可以將 100 部裝置新增至我們的 Developer Portal (開發人員入口網站)，然後我們即可從該處選取要新增至特定佈建設定檔的裝置。 請在您的 Mac 上依照下面的步驟將裝置新增至 Developer Portal (開發人員入口網站)

1. 使用裝置隨附的 USB 纜線，將所要佈建的裝置連接至 Mac。
2. 開啟 Xcode，然後移至 **視窗 > 裝置和模擬器**。
3. 在 [**裝置**] 索引標籤下，從左側功能表中選取裝置。
4. 反白選取 [識別碼] 字串，然後將其複製到剪貼簿：

   ![已反白顯示 iOS 識別碼字串位置的 Xcode 裝置和模擬器視窗。](manual-provisioning-images/xcode-devices.png)

5. 在網頁瀏覽器中，移至[開發人員入口網站中](https://developer.apple.com/account/resources/devices/list)的 [裝置] 區段，然後按一下 [ **+** ] 按鈕：

   ![Apple 開發人員網站上 [裝置] 頁面的螢幕擷取畫面，其中反白顯示 [新增] 按鈕。](manual-provisioning-images/developer-portal-devices.png)

6. 設定正確的**平臺**，並提供新裝置的名稱。 將稍早複製的識別碼貼到 [**裝置識別碼**] 欄位中：

    ![[新增裝置註冊] 頁面的螢幕擷取畫面，其中已正確填入欄位。](manual-provisioning-images/new-device-info.png)

7. 按一下 **[繼續]** 。
8. 檢查資訊，然後按一下 [**註冊**]。

請針對將用來進行 Xamarin.iOS 應用程式測試或偵錯的所有 iOS 裝置，重複上述步驟。

<a name="provisioningprofile" />

## <a name="create-a-development-provisioning-profile"></a>建立開發布建設定檔

將裝置新增至開發人員入口網站之後，就必須建立佈建設定檔，並將裝置新增至此設定檔。 

建立佈建設定檔之前，必須先建立 *App ID* \(應用程式識別碼\)。 App ID (應用程式識別碼) 是可唯一識別應用程式的反向 DNS 樣式字串。 下面的步驟將示範如何建立 **Wildcard App ID** \(萬用字元應用程式識別碼\)，此識別碼可用來建置及安裝大多數應用程式。 **Explicit App ID** \(明確的應用程式識別碼\) 僅允許安裝一個應用程式 (具有相符的套件組合識別碼)，且通常用於特定 iOS 功能，例如 Apple Pay 和 HealthKit。 如需有關建立 Explicit App ID (明確的應用程式識別碼) 的資訊，請參閱[使用功能](~/ios/deploy-test/provisioning/capabilities/index.md)指南。

### <a name="new-wildcard-app-id"></a>新的萬用字元應用程式識別碼

1. 移至[開發人員入口網站中](https://developer.apple.com/account/resources/identifiers/list)的 [識別碼] 區段，然後按一下 [ **+** ] 按鈕。
2. 選取 [**應用程式識別碼**]，然後按一下 [**繼續**]。
3. 提供**描述**。 然後將 [套件組合**識別碼**] 設定為 [**萬用字元**]，並以 `com.[DomainName].*`的格式輸入識別碼：

   ![已填入必要欄位之新 [應用程式識別碼] 註冊頁面的螢幕擷取畫面。](manual-provisioning-images/new-app-id.png)

4. 按一下 **[繼續]** 。
5. 檢查資訊，然後按一下 [**註冊**]。

### <a name="new-provisioning-profile"></a>新增布建設定檔

一旦建立應用程式識別碼之後，就可以建立布建設定檔。 此布建設定檔包含與此設定檔相關*的應用程式*（或應用程式，其為萬用字元應用程式識別碼）、可以使用設定檔的*人員*（視新增的開發人員憑證而定），以及*哪些*裝置可以安裝應用程式的資訊。

若要手動建立用於開發的布建設定檔，請執行下列動作：

1. 移至[開發人員入口網站中的 [設定檔] 區段](https://developer.apple.com/account/resources/profiles/list)，然後按一下 [ **+** ] 按鈕。

2. 在 [**開發**] 下選取 [ **IOS 應用程式開發**]，然後按一下 [**繼續**]。

3. 從下拉式功能表中選取要使用的應用程式識別碼，然後按一下 [**繼續**]。

4. 選取要包含在布建設定檔中的憑證，然後按一下 [**繼續**]。

5. 選取將安裝應用程式的所有裝置，然後按一下 [**繼續**]。

6. 提供 [布建**設定檔名稱**]，然後按一下 [**產生**]。

7. 您可以選擇性地在下一頁按一下 [**下載**]，將布建設定檔下載到 Mac。

<a name="download" />

## <a name="download-provisioning-profiles-in-visual-studio"></a>下載 Visual Studio 中的布建設定檔

在 Apple 開發人員入口網站中建立新的布建設定檔之後，請使用 Visual Studio 加以下載，以便在您的應用程式中進行套件組合簽署。

1. 移至 Visual Studio 中的 [Apple 開發人員帳戶] 視窗。
    1. Mac： **Visual Studio > 喜好設定 > Apple 開發人員帳戶**
    2. Windows： **> Xamarin > Apple 帳戶的工具 > 選項**

2. 選取小組，然後按一下 [**查看詳細資料**]。
3. 確認新的設定檔會出現在 [布建**設定檔**] 清單中。 您可能需要重新開機 Visual Studio，才能重新整理清單。 
4. 按一下 [**下載所有設定檔**]。

新的布建設定檔現在會在 Visual Studio 中提供，並可供使用。

## <a name="deploy-to-a-device"></a>部署到裝置

此時佈建應該已完成，已可將應用程式部署至裝置。 若要這樣做，請遵循下列步驟：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 將您的裝置連線到 Mac。
2. 開啟 [ **plist** ]，並確定 [套件組合識別碼] 符合稍早建立的應用程式**識別碼**（除非應用程式識別碼是萬用字元）。
3. 在 [**簽署**] 區段中，選取 [**手動**布建] 作為 [**配置**]：

    ![Visual Studio for Mac 中，已選取手動布建的資訊 plist 螢幕擷取畫面](manual-provisioning-images/vsm-info-plist.png)

4. 按一下 [套件組合**簽署選項 ...** ]
5. 請確定 [組建設定] 已設為 [ **Debug] | [iPhone**]。 開啟 [**簽署身分識別**] 和 [布建**設定檔**] 下拉式功能表，確認已列出正確的憑證和布建設定檔： 

   ![具有 [布建設定檔] 下拉式功能表的 [iOS 套件組合簽署] 屬性頁面會開啟，列出應用程式的所有可用的布建設定檔。](manual-provisioning-images/vsm-bundle-signing.png)

6. 選取要使用的特定身分識別和設定檔，或將它保留為 [**自動**]。 當設定為 [**自動**] 時，Visual Studio for Mac 會根據**plist**中的套件組合**識別碼**，來選取身分識別和設定檔。 
7. 按一下 [確定]。
8. 按一下 [**執行**]，將應用程式部署到您的裝置。


# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 將您的裝置連接到 Mac 組建主機。
2. 開啟 [ **plist** ]，並確定 [套件組合識別碼] 符合稍早建立的應用程式**識別碼**（除非應用程式識別碼是萬用字元）。
3. 在 **方案總管**中，以滑鼠右鍵按一下 ios 專案名稱，選取 **屬性**，然後流覽至 iOS 套件組合**簽署** 索引標籤。
4. 請確定 [組建設定] 已設為 [ **Debug] | [iPhone**]。 在 [套件組合**簽署**] 底下，選取 [**手動**布建] 做為**配置**：

    ![Visual Studio for Mac 中，已選取手動布建的資訊 plist 螢幕擷取畫面](manual-provisioning-images/vs-bundle-signing.png)

5. 同時開啟 [**簽署身分識別**] 和 [布建**設定檔**] 下拉式功能表，確認已列出正確的憑證和布建設定檔。
6. 選取要使用的特定身分識別和設定檔，或將它保留為 [**自動**]。 當設定為 [**自動**] 時，Visual Studio 會根據**plist**中的套件組合**識別碼**，來選取身分識別和設定檔。 
7. 按一下 [**執行**]，將應用程式部署到您的裝置。

-----

## <a name="provisioning-for-application-services"></a>應用程式服務的佈建

Apple 提供一組可針對 Xamarin.iOS 應用程式啟用的精選特別「應用程式服務」(也稱為功能)。 不論是在 iOS Provisioning Portal (iOS 佈建入口網站) 中於建立 **App ID** \(應用程式識別碼\) 時，還是在 Xamarin.iOS 應用程式專案所含的 **Entitlements.plist** 檔案中，都必須設定這些「應用程式服務」。 如需有關將「應用程式服務」新增至您應用程式的資訊，請參閱[功能簡介](~/ios/deploy-test/provisioning/capabilities/index.md)指南和[使用權利](~/ios/deploy-test/provisioning/entitlements.md)指南。

- 建立具有所需應用程式服務的 App ID (應用程式識別碼)。
- 建立包含此 App ID (應用程式識別碼) 的新[佈建設定檔](#provisioningprofile)。
- 在 Xamarin.iOS 專案中設定權利

## <a name="related-links"></a>相關連結

- [免費佈建](~/ios/get-started/installation/device-provisioning/free-provisioning.md)
- [應用程式散發](~/ios/deploy-test/app-distribution/index.md)
- [疑難排解](~/ios/deploy-test/troubleshooting.md)
- [Apple - 應用程式散發指南](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html) \(英文\)
