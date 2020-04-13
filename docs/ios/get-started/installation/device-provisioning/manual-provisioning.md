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
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "79304860"
---
# <a name="manual-provisioning-for-xamarinios"></a>Xamarin.iOS 的手動佈建

_成功安裝 Xamarin.iOS 後,iOS 開發的下一步是預配 iOS 設備。本指南探討了使用手動預配來設置開發證書和配置檔。_

> [!NOTE]
> 此頁面上的指示與已付費存取 Apple 開發人員計劃的開發人員相關。 如果您有免費帳戶，請查看[免費佈建](~/ios/get-started/installation/device-provisioning/free-provisioning.md)指南，了解有關裝置上測試的詳細資訊。

## <a name="create-a-development-certificate"></a>建立開發憑證

設置開發設備的第一步是創建簽名證書。 簽署憑證包含兩件事:

- 開發憑證
- 私密金鑰

開發憑證和關聯的[金鑰](#understanding-certificate-key-pairs)對 iOS 開發人員而言非常重要：它們會向 Apple 確立您的身分識別，並將您與指定的裝置和設定檔建立關聯以進行開發，類似於將您的數位簽章放在應用程式上。 Apple 會檢查憑證，以針對允許您部署的裝置控制存取權。

您可以存取 Apple 成員中心的[憑證、識別碼及設定檔](https://developer.apple.com/account/resources/certificates/list) (需要登入) 區段來管理開發小組、憑證及設定檔。 Apple 會要求您必須要有簽署身分識別，才能為裝置或模擬器組建程式碼。  

> [!IMPORTANT]
> 請務必注意，在任一時間，您都只能有兩個 iOS 開發憑證。 如果您需要再多建立任何憑證，將必須撤銷現有的憑證。 任何使用已撤銷之憑證的電腦都將無法簽署其應用程式。

在開始手動預配過程之前,應確保在 Visual Studio 中添加了 Apple 開發人員帳戶,如[Apple 帳戶管理](~/cross-platform/macios/apple-account-management.md)指南中所述。 新增 Apple 開發人員帳戶後,請執行以下操作以產生簽名證書:

1. 轉到 Visual Studio 中的 Apple 開發人員帳戶視窗。
    1. Mac:**視覺工作室>偏好>蘋果開發者帳戶**
    2. 視窗:**工具>選項>Xamarin>蘋果帳戶**

2. 選擇一個團隊,然後單擊 **"查看詳細資訊..."**
3. 點選 **「建立憑證」** 並選擇**Apple 開發**或**iOS 開發**。 如果您擁有正確的許可權,則幾秒鐘后將顯示新的簽名標識。

### <a name="understanding-certificate-key-pairs"></a>了解憑證金鑰組

「開發人員設定檔」包含憑證、其相關金鑰，以及該帳戶的任何相關佈建設定檔。 「開發人員設定檔」實際上有兩個版本 — 一個是在 Developer Portal (開發人員入口網站) 上，另一個位於本機 Mac 上。 兩者的差異在於其包含的金鑰類型：入口網站上的設定檔裝載與您憑證相關的所有公開金鑰，而本機 Mac 上的複本則包含所有私密金鑰__。 憑證若要有效，金鑰組必須相符。

> [!WARNING]
> 丟失證書和關聯金鑰可能會造成難以置信的破壞性,因為它需要撤銷現有證書並重新預配任何關聯設備,包括為臨時部署而註冊的設備。 成功設定「開發憑證」之後，請匯出一份備份複本，並將其儲存在安全的地方。 如需有關此做法的詳細資訊，請參閱 Apple 文件中[維護憑證](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/MaintainingCertificates/MaintainingCertificates.html)指南 \(英文\) 的＜Importing Certificates and Profiles＞(匯入憑證與設定檔) 一節。

<a name="provisioning" />

## <a name="provision-an-ios-device-for-development"></a>預先使用 iOS 裝置

現在,您已經使用 Apple 建立了身份並具有開發證書,您必須設置預配設定檔和所需的實體,以便可以將應用部署到 Apple 設備。 裝置執行的 iOS 必須是 Xcode 所支援的版本 — 可能會需要更新裝置、Xcode 或兩者都更新。

<a name="adddevice" />

## <a name="add-a-device"></a>新增裝置

建立佈建設定檔以進行開發時，必須指出哪些裝置可以執行該應用程式。 為了能夠這樣做，每個日曆年度最多可以將 100 部裝置新增至我們的 Developer Portal (開發人員入口網站)，然後我們即可從該處選取要新增至特定佈建設定檔的裝置。 請在您的 Mac 上依照下面的步驟將裝置新增至 Developer Portal (開發人員入口網站)

1. 使用裝置隨附的 USB 纜線，將所要佈建的裝置連接至 Mac。
2. 開啟 Xcode,然後轉到 **「視窗>裝置與模擬器**。
3. 在「**設備」** 選項卡下,從左側的功能表中選擇設備。
4. 反白選取 [識別碼]**** 字串，然後將其複製到剪貼簿：

   ![突出顯示 iOS 識別符字串位置的 Xcode 設備和模擬器視窗。](manual-provisioning-images/xcode-devices.png)

5. 在 Web 瀏覽器中,轉到[開發人員門戶中的「設備」部分](https://developer.apple.com/account/resources/devices/list),**+** 然後單擊 該按鈕:

   ![Apple 開發人員網站上的設備頁面螢幕截圖,並突出顯示了添加按鈕。](manual-provisioning-images/developer-portal-devices.png)

6. 設置正確的**平臺**並提供新設備的名稱。 將前面複製的識別碼貼到 **「裝置 ID 欄**位中:

    ![新設備註冊頁的螢幕截圖,欄位已正確填充。](manual-provisioning-images/new-device-info.png)

7. 按一下 **[繼續]**。
8. 查看資訊,然後單擊"**註冊**"。

請針對將用來進行 Xamarin.iOS 應用程式測試或偵錯的所有 iOS 裝置，重複上述步驟。

<a name="provisioningprofile" />

## <a name="create-a-development-provisioning-profile"></a>建立開發預先設定檔

將裝置新增至開發人員入口網站之後，就必須建立佈建設定檔，並將裝置新增至此設定檔。 

建立佈建設定檔之前，必須先建立 *App ID* \(應用程式識別碼\)。 App ID (應用程式識別碼) 是可唯一識別應用程式的反向 DNS 樣式字串。 下面的步驟將示範如何建立 **Wildcard App ID** \(萬用字元應用程式識別碼\)，此識別碼可用來建置及安裝大多數應用程式。 **Explicit App ID** \(明確的應用程式識別碼\) 僅允許安裝一個應用程式 (具有相符的套件組合識別碼)，且通常用於特定 iOS 功能，例如 Apple Pay 和 HealthKit。 如需有關建立 Explicit App ID (明確的應用程式識別碼) 的資訊，請參閱[使用功能](~/ios/deploy-test/provisioning/capabilities/index.md)指南。

### <a name="new-wildcard-app-id"></a>新的通配子應用程式 ID

1. 轉到[開發人員門戶中的"標識符"部分](https://developer.apple.com/account/resources/identifiers/list),然後單**+** 擊該 按鈕。
2. 選擇**應用指示,** 然後按下「**繼續**」。
3. 提供**說明**。 然後,將**捆綁 ID**設定為**通配符**`com.[DomainName].*`,然後輸入 格式中的 ID:

   ![新的應用 ID 註冊頁的螢幕截圖,其中填充了所需的欄位。](manual-provisioning-images/new-app-id.png)

4. 按一下 **[繼續]**。
5. 查看資訊,然後單擊"**註冊**"。

### <a name="new-provisioning-profile"></a>新的預先設定檔

創建應用 ID 後,可以創建預配配置檔。 此預配設定檔包含有關*哪些*應用(或應用,如果是通配符應用 ID)的資訊,此設定檔與*哪些應用*相關,誰可以使用配置檔(取決於添加的開發人員證書),以及*哪些*設備可以安裝應用。

要手動建立用於開發的預配設定檔,請執行以下操作:

1. 轉到[開發人員門戶中的「設定檔」部分](https://developer.apple.com/account/resources/profiles/list),然後按下**+** 該 按鈕。

2. 在 **"開發"** 下,選擇**iOS 應用程式開發**並按兩下"**繼續**" 。

3. 從下拉菜單中選擇要使用的應用 ID,然後單擊"**繼續**"。

4. 選擇要包括在預配配置檔中的證書,然後單擊"**繼續**"

5. 選擇應用將安裝在的所有設備,然後單擊"**繼續**"。

6. 提供**預先設定檔名稱**,然後單擊**產生**。

7. 您可以選擇單擊下一頁上的 **「下載」** 將預配設定檔下載到 Mac 上。

<a name="download" />

## <a name="download-provisioning-profiles-in-visual-studio"></a>在視覺化工作室下載預配設定檔

在 Apple 開發人員門戶中創建新的預配配置檔後,請使用 Visual Studio 下載它,以便可用於在應用中進行捆綁簽名。

1. 轉到 Visual Studio 中的 Apple 開發人員帳戶視窗。
    1. Mac:**視覺工作室>偏好>蘋果開發者帳戶**
    2. 視窗:**工具>選項>Xamarin>蘋果帳戶**

2. 選擇團隊並單擊"**查看詳細資訊..."**
3. 驗證新設定檔是否顯示在**預配設定檔**清單中。 您可能需要重新啟動可視化工作室才能刷新清單。 
4. 按下 **「下載所有設定檔**」。

新的預配配置檔現在將在 Visual Studio 中提供,並可供使用。

## <a name="deploy-to-a-device"></a>部署到裝置

此時佈建應該已完成，已可將應用程式部署至裝置。 若要這樣做，請遵循下列步驟：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 將設備連接到 Mac。
2. 打開**Info.plist**並確保**捆綁識別碼**與之前創建的應用 ID 符合(除非應用 ID 是通配符)。
3. 在 **'簽名**'部分中,選擇 **'手動預配**"作為**專案**:

    ![選擇手動預配的 Mac 視覺化工作室中 Info.plist 的螢幕截圖](manual-provisioning-images/vsm-info-plist.png)

4. 點選**中簽署選項...**
5. 確保生成配置設置為**調試\iPhone。** 同時開啟**簽章識別**和**預先設定檔**下拉選單,以驗證是否列出了正確的憑證和預配設定檔: 

   ![iOS 捆綁簽名屬性頁,其中打開預配配置檔下拉菜單,列出應用的所有可用預配配置檔。](manual-provisioning-images/vsm-bundle-signing.png)

6. 選擇要使用的特定標識和配置檔,或將其保留為 **"自動**" 。 當設定為**自動**,Mac 的可視化工作室將根據**Info.plist**中的**捆綁標識符**選擇標識和配置檔。 
7. 按一下 [確定]  。
8. 按下 **「運行**」將應用部署到設備。


# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 將設備連接到 Mac 構建主機。
2. 打開**Info.plist**並確保**捆綁識別碼**與之前創建的應用 ID 符合(除非應用 ID 是通配符)。
3. 在**解決方案資源管理器**中,右鍵單擊 iOS 專案名稱,選擇 **「屬性」** 並導航到**iOS 捆綁包簽名**選項卡。
4. 確保生成配置設置為**調試\iPhone。** 在**捆綁簽名**下,選擇**手動預配**作為**專案**:

    ![選擇手動預配的 Mac 視覺化工作室中 Info.plist 的螢幕截圖](manual-provisioning-images/vs-bundle-signing.png)

5. 同時打開**簽章標識**和**預配配置檔**下拉菜單,以驗證是否列出了正確的證書和預配配置檔。
6. 選擇要使用的特定標識和配置檔,或將其保留為 **"自動**" 。 當設定為 **「自動」** 時,視覺化工作室將根據**Info.plist**中的**捆綁識別符**選擇識別和設定檔。 
7. 按下 **「運行**」將應用部署到設備。

-----

## <a name="provisioning-for-application-services"></a>應用程式服務的佈建

Apple 提供一組可針對 Xamarin.iOS 應用程式啟用的精選特別「應用程式服務」(也稱為功能)。 不論是在 iOS Provisioning Portal (iOS 佈建入口網站) 中於建立 **App ID** \(應用程式識別碼\) 時，還是在 Xamarin.iOS 應用程式專案所含的 **Entitlements.plist** 檔案中，都必須設定這些「應用程式服務」。 如需有關將「應用程式服務」新增至您應用程式的資訊，請參閱[功能簡介](~/ios/deploy-test/provisioning/capabilities/index.md)指南和[使用權利](~/ios/deploy-test/provisioning/entitlements.md)指南。

- 建立具有所需應用程式服務的 App ID (應用程式識別碼)。
- 建立包含此 App ID (應用程式識別碼) 的新[佈建設定檔](#provisioningprofile)。
- 在 Xamarin.iOS 專案中設定權利

## <a name="related-links"></a>相關連結

- [免費佈建](~/ios/get-started/installation/device-provisioning/free-provisioning.md)
- [套用分轉](~/ios/deploy-test/app-distribution/index.md)
- [疑難排解](~/ios/deploy-test/troubleshooting.md)
- [Apple - 應用程式散發指南](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
