---
title: Xamarin.iOS 的手動佈建
description: 成功安裝 Xamarin.iOS 之後，iOS 開發作業的下一步就是佈建您的 iOS 裝置。 本指南會探討使用手動佈建來設定開發憑證和設定檔。
ms.prod: xamarin
ms.assetid: E26ACC94-F4A5-4FF5-B7D4-BE596745A665
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/15/2017
ms.openlocfilehash: 945a42485486dbfddfd023a72e88d9127651c71f
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2020
ms.locfileid: "78911517"
---
# <a name="manual-provisioning-for-xamarinios"></a>Xamarin.iOS 的手動佈建

_成功安裝 Xamarin 之後，iOS 開發的下一步就是布建您的 iOS 裝置。本指南會探索如何使用手動布建來設定開發憑證和設定檔。_

> [!NOTE]
> 此頁面上的指示與已付費存取 Apple 開發人員計劃的開發人員相關。 如果您有免費帳戶，請查看[免費佈建](~/ios/get-started/installation/device-provisioning/free-provisioning.md)指南，了解有關裝置上測試的詳細資訊。

## <a name="creating-a-signing-identity"></a>建立簽署身分識別

設定開發裝置的第一步就是建立簽署身分識別。 簽署身分識別是由兩個項目所組成：

- 開發憑證
- 私密金鑰

開發憑證和關聯的[金鑰](#understanding-certificate-key-pairs)對 iOS 開發人員而言非常重要：它們會向 Apple 確立您的身分識別，並將您與指定的裝置和設定檔建立關聯以進行開發，類似於將您的數位簽章放在應用程式上。 Apple 會檢查憑證，以針對允許您部署的裝置控制存取權。

您可以存取 Apple 成員中心的[憑證、識別碼及設定檔](https://developer.apple.com/account/overview.action) (需要登入) 區段來管理開發小組、憑證及設定檔。 Apple 會要求您必須要有簽署身分識別，才能為裝置或模擬器組建程式碼。  

> [!IMPORTANT]
> 請務必注意，在任一時間，您都只能有兩個 iOS 開發憑證。 如果您需要再多建立任何憑證，將必須撤銷現有的憑證。 任何使用已撤銷之憑證的電腦都將無法簽署其應用程式。

若要產生簽署身分識別，請執行下列動作：

1. 登入 Developer Portal (開發人員入口網站) 的 [Certificates, Identifiers, and Profiles](https://developer.apple.com/account/overview.action) \(憑證、識別碼及設定檔\) 區段，然後從 [iOS Apps] \(iOS 應用程式\) 資料行中選取 [Certificates] 區段。 接著，按一下 [ **]+** 以建立新的憑證：

    [![](manual-provisioning-images/cert-plus.png "Click the + to create a new certificate")](manual-provisioning-images/cert-plus.png#lightbox)

2. 針對憑證類型選取 [iOS App Development] \(iOS 應用程式開發\) 選項，然後按一下 [Continue] \(繼續\)。 此畫面可能會依據您的帳戶權限而有所不同：

    [![](manual-provisioning-images/cert-first.png "Select the iOS App Development option for the certificate type")](manual-provisioning-images/cert-first.png#lightbox)

3. 要求一個「憑證簽署要求」，將會上傳此簽署要求以手動產生憑證。 若要這樣做，請在 Mac 上啟動 [鑰匙圈存取]。 瀏覽至主功能表，然後選取 [憑證輔助程式] 和 [從憑證授權要求憑證]如下所示：

      [![](manual-provisioning-images/key-first.png "Request a Certificate Signing Request")](manual-provisioning-images/key-first.png#lightbox)

4. 填入您的資訊，然後選取 [儲存到磁碟] 的選項：

    [![](manual-provisioning-images/key-second.png "Fill in your information")](manual-provisioning-images/key-second.png#lightbox)

5. 將 CSR 儲存在容易找到的位置：

    [![](manual-provisioning-images/cert-third.png "Save the CSR")](manual-provisioning-images/cert-third.png#lightbox)

6. 返回 Provisioning Portal (佈建入口網站)，將憑證上傳到入口網站，然後提交：

    [![](manual-provisioning-images/cert-second.png "Upload the Certificate to the portal")](manual-provisioning-images/cert-second.png#lightbox)

    如果您沒有系統管理員權限，則必須由系統管理員或小組代理人核准憑證。

7. 在憑證獲得核准之後，從 Provisioning Portal (佈建入口網站) 下載它：

    [![](manual-provisioning-images/status-dev.png "Download the Certificate from the Provisioning Portal")](manual-provisioning-images/status-dev.png#lightbox)

8. 按兩下已下載的憑證以啟動 [鑰匙圈存取]，然後開啟 [我的憑證] 面板，其中會顯示新的憑證和關聯的私密金鑰：

    [![](manual-provisioning-images/keychain.png "The Certificate in Keychain Access")](manual-provisioning-images/keychain.png#lightbox)

### <a name="understanding-certificate-key-pairs"></a>了解憑證金鑰組

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

「開發人員設定檔」包含憑證、其相關金鑰，以及該帳戶的任何相關佈建設定檔。 「開發人員設定檔」實際上有兩個版本 — 一個是在 Developer Portal (開發人員入口網站) 上，另一個位於本機 Mac 上。 兩者的差異在於其包含的金鑰類型：入口網站上的設定檔裝載與您憑證相關的所有公開金鑰，而本機 Mac 上的複本則包含所有私密金鑰。 憑證若要有效，金鑰組必須相符。 請在本機 Mac 上保留一份「開發人員設定檔」備份，因為如果遺失私密金鑰，就必須重新產生所有憑證和佈建設定檔。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

「開發人員設定檔」包含憑證、其相關金鑰，以及該帳戶的任何相關佈建設定檔。 「開發人員設定檔」實際上有兩個版本 — 一個是在 Developer Portal (開發人員入口網站) 上，另一個位於 Mac 上。 兩者的差異在於其包含的金鑰類型：入口網站上的設定檔裝載與您憑證相關的所有公開金鑰，而 Mac 上的複本則包含所有私密金鑰。 憑證若要有效，金鑰組必須相符。 請在 Xamarin 組建主機的 Mac 上保留一份「開發人員設定檔」備份，因為如果遺失私密金鑰，就必須重新產生所有憑證和佈建設定檔。

-----

> [!WARNING]
> 遺失憑證和相關金鑰的後果極度嚴重，因為這將需要撤銷現有的憑證並重新佈建所有相關的裝置，包括已註冊要進行臨機操作部署的項目。 成功設定「開發憑證」之後，請匯出一份備份複本，並將其儲存在安全的地方。 如需有關此做法的詳細資訊，請參閱 Apple 文件中[維護憑證](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/MaintainingCertificates/MaintainingCertificates.html)指南 \(英文\) 的＜Importing Certificates and Profiles＞(匯入憑證與設定檔) 一節。

<a name="provisioning" />

## <a name="provisioning-an-ios-device-for-development"></a>佈建 iOS 裝置以進行開發

既然您已向 Apple 確立身分識別並已擁有開發憑證，接著就必須設定佈建設定檔和必要的實體，才能將應用程式部署至 Apple 裝置。 裝置執行的 iOS 必須是 Xcode 所支援的版本 — 可能會需要更新裝置、Xcode 或兩者都更新。

<a name="adddevice" />

## <a name="add-a-device"></a>新增裝置

建立佈建設定檔以進行開發時，必須指出哪些裝置可以執行該應用程式。 為了能夠這樣做，每個日曆年度最多可以將 100 部裝置新增至我們的 Developer Portal (開發人員入口網站)，然後我們即可從該處選取要新增至特定佈建設定檔的裝置。 請在您的 Mac 上依照下面的步驟將裝置新增至 Developer Portal (開發人員入口網站)

1. 啟動 Xcode。
2. 使用裝置隨附的 USB 纜線，將所要佈建的裝置連接至 Mac。
3. 從 [視窗] 功能表中，選取 [裝置]：

   [![](manual-provisioning-images/add01.png "From the Windows menu select Devices")](manual-provisioning-images/add01.png#lightbox)

4. 從左側 [裝置] 視窗上的 [裝置] 清單中選取所需的 iOS 裝置。
5. 反白選取 [識別碼] 字串，然後將其複製到剪貼簿：

   [![](manual-provisioning-images/add02.png "Highlight the Identifier string")](manual-provisioning-images/add02.png#lightbox)

6. 在 Safari 中，瀏覽至 [Apple Developer Center](https://developer.apple.com/membercenter/index.action) \(Apple 開發人員中心\) 並登入。
7. 按一下 [Certificates, Identifiers & Profiles] \(憑證、識別碼及設定檔\) 連結：

   [![](manual-provisioning-images/add03.png "Click the Certificates, Identifiers  Profiles link")](manual-provisioning-images/add03.png#lightbox)

8. 按一下 [Devices] \(裝置\) 連結：

   [![](manual-provisioning-images/add04.png "Click on the Devices link")](manual-provisioning-images/add04.png#lightbox)

9. 按一下 [ **]+** 按鈕：

   [![](manual-provisioning-images/add05.png "Click the + button")](manual-provisioning-images/add05.png#lightbox)

10. 為新裝置提供名稱，然後將上述複製的**識別碼**貼到 [UUID] 欄位中：

    [![](manual-provisioning-images/add06.png "Provide a name for the new device and the device Identifier")](manual-provisioning-images/add06.png#lightbox)

11. 按一下 [繼續] 按鈕。
12. 最後，檢閱資訊，然後按一下 [Register] \(註冊\) 按鈕：

    [![](manual-provisioning-images/add07.png "Review the information")](manual-provisioning-images/add07.png#lightbox)

請針對將用來進行 Xamarin.iOS 應用程式測試或偵錯的所有 iOS 裝置，重複上述步驟。

將裝置新增至開發人員入口網站之後，就必須建立佈建設定檔，並將裝置新增至此設定檔。

<a name="provisioningprofile" />

## <a name="creating-a-development-provisioning-profile"></a>建立開發佈建設定檔

與「開發憑證」一樣，您可以透過 Apple Member Center (Apple 成員中心) 的 [Certificates, Identifiers & Profiles](https://developer.apple.com/account/overview.action) \(憑證、識別碼及設定檔\) 區段來手動建立「佈建設定檔」。

建立佈建設定檔之前，必須先建立 *App ID* \(應用程式識別碼\)。 App ID (應用程式識別碼) 是可唯一識別應用程式的反向 DNS 樣式字串。 下面的步驟將示範如何建立 **Wildcard App ID** \(萬用字元應用程式識別碼\)，此識別碼可用來建置及安裝大多數應用程式。 **Explicit App ID** \(明確的應用程式識別碼\) 僅允許安裝一個應用程式 (具有相符的套件組合識別碼)，且通常用於特定 iOS 功能，例如 Apple Pay 和 HealthKit。 如需有關建立 Explicit App ID (明確的應用程式識別碼) 的資訊，請參閱[使用功能](~/ios/deploy-test/provisioning/capabilities/index.md)指南。

### <a name="app-id"></a>應用程式識別碼

1. 在[開發人員入口網站](https://developer.apple.com/account/overview.action)中，瀏覽至 Apple Developer Center (Apple 開發人員中心) 中的 [Certificate, Identifiers and Profiles] \(憑證、識別碼及設定檔\) 區段。 選取 [Identifiers] \(識別碼\) 底下的 [App IDs] \(應用程式識別碼\)。
2. 按一下 [ **]+** 按鈕，然後提供 [Name] \(名稱\)：

    [![](manual-provisioning-images/appid05a.png "Provide a Name")](manual-provisioning-images/appid05a.png#lightbox)
3. 應用程式前置詞應該已預先設定。 針對應用程式後置詞，選取 [Wildcard App ID] \(萬用字元應用程式識別碼\)。 以 `com.[DomainName].*` 格式輸入 Bundle ID (套件組合識別碼)：

   [![](manual-provisioning-images/appid05b.png "Enter a Bundle ID")](manual-provisioning-images/appid05b.png#lightbox)

4. 按一下 [繼續] 按鈕，然後依照畫面上的指示來建立新的 App ID (應用程式識別碼)。

### <a name="provisioning-profile"></a>佈建設定檔

建立 App ID (應用程式識別碼) 之後，即可產生 Provisioning Profile (佈建設定檔)。 此 Provisioning Profile (佈建設定檔) 包含一些資訊，包括此設定檔與「哪個」應用程式 (或如果是萬用字元應用程式識別碼，則為哪些應用程式) 相關、「誰」可以使用此設定檔 (取決於所新增的開發人員憑證)，以及「哪些」裝置可以安裝應用程式。

若要手動建立佈建設定檔以進行開發，請執行下列操作：

1. 使用 Safari 來瀏覽至 [Apple Developers Member Center](https://developer.apple.com/membercenter/index.action) \(Apple 開發人員成員中心\)，然後在 [Certificates, Identifiers & Profiles] \(憑證、識別碼及設定檔\) 底下，選取 [Provisioning Profiles] \(佈建設定檔\)。
2. 按一下右上角的 [ **]+** 按鈕來建立新的設定檔。
3. 從 [Development] \(開發\) 區段中，選取 [iOS App Development] \(iOS 應用程式開發\) 旁邊的選項按鈕，然後按 [Continue] \(繼續\)：

    [![](manual-provisioning-images/provisioning-profile01.png "Select the type of profile to create")](manual-provisioning-images/provisioning-profile01.png#lightbox)
4. 從下拉式功能表中，選取要使用的 App ID (應用程式識別碼)：

    [![](manual-provisioning-images/provisioning-profile02.png "Select the App ID that to use")](manual-provisioning-images/provisioning-profile02.png#lightbox)
5. 選取要包含在佈建設定檔中的憑證，然後按 [Continue] \(繼續\)：

    [![](manual-provisioning-images/provisioning-profile03.png "Select the Certificates to include in the provisioning profile")](manual-provisioning-images/provisioning-profile03.png#lightbox)
6. 選取將安裝應用程式的所有裝置。

    [![](manual-provisioning-images/provisioning-profile04.png "Select all the devices that the app will be installed on")](manual-provisioning-images/provisioning-profile04.png#lightbox)
7. 為 Provisioning Profile (佈建設定檔) 提供一個可識別的名稱，然後按 [Continue] \(繼續\) 來建立設定檔：

    [![](manual-provisioning-images/provisioning-profile05.png "Provide the Provisioning Profile with an identifiable a name")](manual-provisioning-images/provisioning-profile05.png#lightbox)
8. 按 [Download] \(下載\) 以將佈建設定檔下載到 Mac：

    [![](manual-provisioning-images/provisioning-profile06.png "Download the provisioning profile")](manual-provisioning-images/provisioning-profile06.png#lightbox)

9. 按兩下檔案以在 Xcode 中安裝佈建設定檔。 請注意，除了開啟之外，Xcode 可能不會顯示任何有關它已安裝設定檔的視覺線索。 這可以透過流覽至**Xcode > 視窗 > 裝置和**模擬器來加以驗證。 以滑鼠右鍵按一下您的裝置，然後選取 [顯示布建**設定檔**...]

      [![](manual-provisioning-images/provisioning-profile07-sml.png "Viewing the profile in Xcode")](manual-provisioning-images/provisioning-profile07.png#lightbox)

成功建立佈建設定檔之後，可能必須重新整理 Xcode，Visual Studio for Mac 和 Visual Studio 才能使用所有開發憑證。

<a name="download" />

## <a name="downloading-profiles-and-certificates-in-xcode"></a>在 Xcode 中下載設定檔和憑證

在 Apple Developer Portal (Apple 開發人員入口網站) 中建立的憑證和佈建設定檔可能不會自動顯示在 Xcode 中。 因此，可能必須下載它們，如此 Visual Studio for Mac 和 Visual Studio 才能存取它們。 若要更新和下載在 Apple Developer Portal (Apple 開發人員入口網站) 中建立的任何憑證，請執行下列動作：

1. 結束 Visual Studio for Mac 或 Visual Studio。
2. 啟動 Xcode。
3. 選擇 [Xcode] 功能表 > [Preferences...] \(偏好設定\)
4. 按一下 [Accounts] \(帳戶\) 索引標籤。
5. 選取一個小組，然後按一下 [Download Manual Profiles] \(下載手動設定檔\) 按鈕：

    [![下載手動設定檔](manual-provisioning-images/selectteam1.png)](manual-provisioning-images/selectteam1.png#lightbox)

6. 結束 Xcode。
7. 啟動 Visual Studio for Mac 或 Visual Studio。

新憑證或佈建設定檔將會出現在 Visual Studio for Mac 或 Visual Studio 中，並已可供使用。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

> [!IMPORTANT]
> 可能必須將 Visual Studio for Mac 停止後再重新啟動，才能看見由 Xcode 所更新的新的或已修改的憑證或設定檔。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

> [!IMPORTANT]
> 可能必須將 Visual Studio 停止後再重新啟動，才能看見由 Xcode 所更新的新的或已修改的憑證或設定檔。

-----

## <a name="provisioning-for-application-services"></a>應用程式服務的佈建

Apple 提供一組可針對 Xamarin.iOS 應用程式啟用的精選特別「應用程式服務」(也稱為功能)。 不論是在 iOS Provisioning Portal (iOS 佈建入口網站) 中於建立 **App ID** \(應用程式識別碼\) 時，還是在 Xamarin.iOS 應用程式專案所含的 **Entitlements.plist** 檔案中，都必須設定這些「應用程式服務」。 如需有關將「應用程式服務」新增至您應用程式的資訊，請參閱[功能簡介](~/ios/deploy-test/provisioning/capabilities/index.md)指南和[使用權利](~/ios/deploy-test/provisioning/entitlements.md)指南。

- 建立具有所需應用程式服務的 App ID (應用程式識別碼)。
- 建立包含此 App ID (應用程式識別碼) 的新[佈建設定檔](#provisioningprofile)。
- 在 Xamarin.iOS 專案中設定權利

## <a name="deploying-to-a-device"></a>部署至裝置

此時佈建應該已完成，已可將應用程式部署至裝置。 若要這樣做，請遵循下面的步驟：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

> [!IMPORTANT]
> 在您開始之前，請確定選取 **Info.plist** 中的 [手動佈建]。

1. 將裝置插入 Mac。
2. 在專案的 **Info.plist** 中，確定「套件組合識別碼」與「應用程式識別碼」相符 (除非「應用程式識別碼」是萬用字元)：

   ![輸入識別碼](manual-provisioning-images/deploydevice01xs.png)

3. 在專案上按一下滑鼠右鍵以檢視 [專案選項] 對話方塊，然後瀏覽至 [建置] > [iOS 套件組合簽署]。 從 [簽署身分識別] 和 [佈建設定檔] 旁邊的下拉式清單中，確認 Visual Studio for Mac 可以看見正確的設定檔，然後選取特定的身分識別和設定檔：

   ![選取特定的身分識別 & 設定檔](manual-provisioning-images/deploydevice02xs.png)

   如果這已設定為 [自動]，Visual Studio for Mac 就會根據步驟 2 中設定的「套件組合識別碼」來選取身分識別和設定檔。

4. 確定將組建組態設定為 [iPhone**iPad]**  / ，而不是模擬器。
5. 在 Visual Studio for Mac 中按一下 [執行]，即可檢視在裝置上執行的應用程式。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

> [!IMPORTANT]
> 在您開始之前，請確定選取 [專案] > [佈建內容] 中的 [手動佈建]。

1. 將裝置插入 Mac 組建主機。
2. 在專案的 **Info.plist** 中，確定「套件組合識別碼」與「應用程式識別碼」相符：

   ![輸入識別碼](manual-provisioning-images/servicevs01.png)

3. 在專案上按一下滑鼠右鍵以檢視 [專案選項] 對話方塊，然後瀏覽至 [建置] > [iOS 套件組合簽署]。 從 [簽署身分識別] 和 [佈建設定檔] 旁邊的下拉式清單中，確認 Visual Studio 可以看見正確的設定檔，然後選取特定的身分識別和設定檔。

    如果這已設定為 [自動]，Visual Studio 就會根據步驟 2 中設定的「套件組合識別碼」來選取身分識別和設定檔。

4. 確定將組建組態設定為 [iPhone] 或 [iPad]，而不是模擬器。
5. 在 Visual Studio 中按一下 [執行]，即可檢視在裝置上執行的應用程式。

-----

## <a name="summary"></a>摘要

本指南涵蓋了為 Xamarin.iOS 設定開發環境所需的步驟。 其中探索如何使用開發人員、開發人員的小組、可執行應用程式的裝置及個別應用程式識別碼的相關資訊，對應用程式進行程式碼簽署。

## <a name="related-links"></a>相關連結

- [免費佈建](~/ios/get-started/installation/device-provisioning/free-provisioning.md)
- [應用程式散發](~/ios/deploy-test/app-distribution/index.md)
- [疑難排解](~/ios/deploy-test/troubleshooting.md)
- [Apple - 應用程式散發指南](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html) \(英文\)
