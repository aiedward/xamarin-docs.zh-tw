---
title: Xamarin.iOS 應用程式的內部作業散發
description: 本文件針對 Apple Enterprise Developer Program 的成員，提供散發內部作業應用程式的概觀。
ms.prod: xamarin
ms.assetid: 9466E51E-303E-466E-85D7-D0525E16BB37
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 854fecd7945c1090b475b3571678388b8e1cf127
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84573231"
---
# <a name="in-house-distribution-for-xamarinios-apps"></a>Xamarin.iOS 應用程式的內部作業散發

_本文件針對 Apple Enterprise Developer Program 的成員，提供散發內部作業應用程式的概觀。_

完成 Xamarin.iOS 應用程式開發之後，軟體開發生命週期的下一步就是將應用程式散發給使用者。 專屬的應用程式可以透過 **Apple Developer Enterprise Program** 在「內部作業」**(之前稱為「企業」) 散發，其提供下列優點：

- 您的應用程式不必提交給 Apple 來檢閱。
- 可以部署應用程式的裝置數量沒有限制
  - 請務必注意，Apple 清楚表示「內部作業」應用程式僅供內部使用。

另外，也請務必注意有關 Enterprise Program 的下列事項：

- 不提供 iTunes Connect 存取權以進行散發或測試 (包含 TestFlight)。
- 成員資格的費用為每年 $299。

所有應用程式仍然必須由 Apple 簽署。

<a name="testing"></a>

## <a name="testing-your-application"></a>測試應用程式

測試應用程式會透過使用臨機操作散發來執行。 如需關於測試的詳細資訊，請遵循[臨機操作散發](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)指南中的步驟。 請注意，您最多只能測試 100 個裝置。

<a name="setup"></a>

## <a name="getting-set-up-for-distribution"></a>著手設定散發

如同其他的 Apple Developer Program，在 Apple Developer Enterprise Program 下，僅有「小組系統管理員」和「小組代理人」可以建立「散發憑證」和「佈建設定檔」。

Apple Developer Enterprise Program 憑證將持續三年，而佈建設定檔將在一年後到期。

請務必注意，過期的憑證無法更新，而是必須使用新憑證來取代過期的憑證，如[下列](#certificate)詳述。

<a name="certificate"></a>

## <a name="creating-a-distribution-certificate"></a>建立散發憑證

1. 瀏覽至 Apple Developer Member Center 的「憑證、識別碼與設定檔」** 區段。
2. 在 [憑證]** 下，選取 [生產環境]****。
3. 按一下 **+** 按鈕以建立新的憑證。
4. 在 [生產環境] ** 標題下，選取 [內部作業和臨機操作]****：

   [![](in-house-distribution-images/createcertmanually01.png "Select In-House and Ad Hoc")](in-house-distribution-images/createcertmanually01.png#lightbox)

5. 按一下 [繼續]，並遵循指示以透過 Keychain 存取來建立憑證簽署要求：

   [![](in-house-distribution-images/createcertmanually02.png "Create a Certificate Signing Request via Keychain Access")](in-house-distribution-images/createcertmanually02.png#lightbox)

6. 依指示建立 CSR 之後，請按一下 [繼續] 並將您的 CSR 上傳到 Member Center：

   [![](in-house-distribution-images/createcertmanually03.png "Upload the CSR to the Member Center")](in-house-distribution-images/createcertmanually03.png#lightbox)

7. 按一下 [產生] 以建立您的憑證。
8. 下載完成的憑證，並按兩下檔案以進行安裝。
9. 此時，電腦上應該已安裝您的憑證，但是您可能需要重新整理設定檔，以確保會顯示在 Xcode 中。

或者，也可以透過 Xcode 中的 [喜好設定] 對話方塊來要求憑證。 若要這樣做，請遵循下面的步驟：

1. 選取您的小組，然後按一下 [檢視詳細資料]**：

   [![](in-house-distribution-images/selectteam.png "Select your team")](in-house-distribution-images/selectteam.png#lightbox)

2. 接著，按一下 [iOS Distribution Certificate] (iOS 散發憑證)**** 旁邊的 [建立]**** 按鈕：

   [![](in-house-distribution-images/selectcert.png "Create the iOS Distribution Certificate")](in-house-distribution-images/selectcert.png#lightbox)

3. 接著，按一下 [加號 (+)]**** 按鈕，然後選取 [iOS App Store]****：

   [![](in-house-distribution-images/selectcert.png "Select iOS App Store")](in-house-distribution-images/selectcert.png#lightbox)

<a name="profile"></a>

## <a name="creating-a-distribution-provisioning-profile"></a>建立散發佈建設定檔

<a name="appid"></a>

### <a name="creating-an-app-id"></a>建立應用程式識別碼

如同您所建立的其他任何佈建設定檔，此處也需要應用程式識別碼才能識別您要散發給使用者裝置的應用程式。 如果您尚未建立應用程式識別碼，請遵循下列步驟來建立：

1. 在 [Apple Developer Center](https://developer.apple.com/account/overview.action) 中，瀏覽到「憑證、識別碼與設定檔」** 區段。 選取 [識別碼]**** 下的 [應用程式識別碼]****。
2. 按一下 [] **+** 按鈕，並提供可在入口網站中識別它的**名稱**。
3. 應用程式前置詞應該已設定為您的小組識別碼，且無法變更。 選取 [明確應用程式識別碼] 或 [萬用字元應用程式識別碼]，並以反向 DNS 格式輸入套件組合識別碼，例如：**明確**：com.[DomainName].[AppName] **萬用字元**：com.[DomainName].*
4. 選取您的應用程式所需要的任何[應用程式服務](~/ios/get-started/installation/device-provisioning/index.md#provisioning-for-application-services)。
5. 按一下 [繼續]**** 按鈕，並遵循畫面指示來建立新的應用程式識別碼。

一旦您有建立散發設定檔所需的元件之後，請遵循下列步驟來建立散發設定檔：

1. 返回 Apple 布建入口網站，然後**選取 [** 布建] [  >  **散發**]：

   [![](in-house-distribution-images/distribute01.png "Select Provisioning > Distribution")](in-house-distribution-images/distribute01.png#lightbox)

2. 按一下 [] **+** 按鈕，然後選取您想要建立為**內部**的散發配置檔案類型：

   [![](in-house-distribution-images/distribute02.png "Create an In-House Distribution Profile")](in-house-distribution-images/distribute02.png#lightbox)

3. 按一下 [繼續]**** 按鈕，並從您要建立散發設定檔的下拉式清單中選取應用程式識別碼：

   [![](in-house-distribution-images/distribute03.png "Select App ID from the dropdown list")](in-house-distribution-images/distribute03.png#lightbox)

4. 按一下 [繼續]**** 按鈕，並選取簽署應用程式所需的散發憑證：

   [![](in-house-distribution-images/distribute04.png "Select distribution certificate required to sign the application")](in-house-distribution-images/distribute04.png#lightbox)

5. 按一下 [繼續]**** 按鈕，並針對新的散發設定檔輸入**名稱**：

   [![](in-house-distribution-images/distribute06.png "Enter a Name for the new Distribution Profile")](in-house-distribution-images/distribute06.png#lightbox)

6. 按一下 [產生]**** 按鈕，以建立新的設定檔，並完成流程。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

 ，您可能需要結束 Visual Studio for Mac 並讓 Xcode 重新整理其可用「簽署身分識別」和「佈建設定檔」的清單 (依照[要求簽署身分識別](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download)區段中的指示)，之後才能在 Visual Studio for Mac 中使用新的散發設定檔。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

您可能必須結束 Visual Studio，並讓 Xcode （在組建主機的 Mac 上）重新整理其可用的簽署身分識別和布建配置檔案清單（遵循[要求籤署](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download)身分識別一節中的指示），之後才能在 Visual Studio 中使用新的散發設定檔。

-----

<a name="inhouse"></a>

## <a name="distributing-your-app-in-house"></a>散發內部作業應用程式

在 Apple Developer Enterprise Program 中，被授權人 是負責散發應用程式的人員，且必須遵循 Apple 所設定的[指導方針](https://developer.apple.com/programs/enterprise/) \(英文\)。

您的應用程式可以使用各種不同的方法來安全地散發，例如：

- 透過 iTunes 於本機中散發
- MDM 伺服器
- 安全的內部 Web 伺服器
- 電子郵件

若要以其中任何一種方式來散發您的應用程式，您必須先建立一個 IPA 檔案，如下一節中所述。

### <a name="creating-an-ipa-for-in-house-deployment"></a>建立內部作業部署的 IPA

應用程式在佈建之後，可以封裝為稱為 *IPA* 的檔案。 這是包含應用程式與其他中繼資料和圖示的 ZIP 檔案。 IPA 可以用來在本機中將應用程式新增至 iTunes，以便應用程式能直接同步處理到佈建設定檔中所包含的裝置。

如需建立 IPA 的詳細資訊，請參閱 [IPA 支援](~/ios/deploy-test/app-distribution/ipa-support.md)指南。

## <a name="summary"></a>總結

本文概要說明如何散發內部作業 Xamarin.iOS 應用程式。

## <a name="related-links"></a>相關連結

- [App Store 散發](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)
- [臨機操作散發](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)
- [Itunesmetadata.plist. plist 檔案](~/ios/deploy-test/app-distribution/itunesmetadata.md)
- [.IPA 支援](~/ios/deploy-test/app-distribution/ipa-support.md)
