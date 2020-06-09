---
title: Xamarin.iOS 應用程式的臨機操作散發
description: 本文件提供臨機操作散發技術的概觀，此技術主要用於透過大範圍使用者來測試 Xamarin.iOS 應用程式。
ms.prod: xamarin
ms.assetid: 3B621CAD-103C-478A-97C3-829015F48D1A
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: ae3c60699f7e31c66893723ac73248ced9a2d72a
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84567966"
---
# <a name="ad-hoc-distribution-for-xamarinios-apps"></a>Xamarin.iOS 應用程式的臨機操作散發

_本文件提供臨機操作散發技術的概觀，此技術主要用於透過大範圍使用者來測試 Xamarin.iOS 應用程式。_

完成 Xamarin.iOS 應用程式開發之後，軟體開發生命週期的下一步就是將應用程式散發給使用者測試。

iTunes Connect 可作為管理應用程式測試的選項，可在 [TestFlight](~/ios/deploy-test/testflight.md) 指南中取得詳細資訊。 不過，由於 Apple Developer Enterprise Program 成員無法存取 iTunes Connect，所以「臨機操作」 ** 散發是測試這些應用程式最好的方法。

Xamarin.iOS 應用程式可透過「臨機操作」** 散發進行使用者測試，其可在 Apple Developer Program 和 Apple Developer Enterprise Program 中取得，最多可測試 100 部 iOS 裝置。

臨機操作散發的優點在於不需要 App Store 批准，並可透過網頁伺服器或 iTunes 進行無線安裝。 但是，每位成員每年最多只能開發和散發 **100** 部裝置，且必須手動透過這些裝置的 UDID 新增至 Member Center。 如需如何新增裝置的詳細資訊，請參閱[裝置佈建](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#adddevice)指南。

臨機操作散發需要應用程式使用含程式碼簽署資訊臨機操作「佈建設定檔」**、應用程式的身分識別碼、可安裝應用程式的裝置，以進行佈建。

本指南將介紹如何對 臨機操作散發進行佈建，以及如何散發 Xamarin.iOS 應用程式。

<a name="setup"></a>

## <a name="setting-up-for-distribution"></a>著手設定散發

即使您打算發佈用於內部部署的 Xamarin.iOS 應用程式，為了方便測試，您也將會需要建置一個特定於該應用程式的臨機操作佈建設定檔。 此設定檔可讓應用程式進行數位簽署以便發行，使其可安裝在 iOS 裝置上。

下節將描述如何著手設定散發憑證與散發佈建設定檔。

> [!NOTE]
> 只有小組代理人和管理員可以建立散發憑證和佈建設定檔。

<a name="createcertificate"></a>

## <a name="create-a-distribution-certificate"></a>建立散發憑證

1. 瀏覽至 Apple Developer Member Center 的「憑證、識別碼與設定檔」** 區段。
2. 在 [憑證]** 下，選取 [生產環境]****。
3. 按一下 **+** 按鈕以建立新的憑證。
4. 根據您的計劃成員身分，在 [生產環境]** 標題下，選取 [In-House and Ad Hoc] (內部和臨機操作)**** 或是 [App Store and Ad HocA] (App Store 和臨機操作)****：

   [![選取 [內部] 和 [臨機操作]，或 [App Store] 和 [臨機操作]](ad-hoc-distribution-images/cert-first-small.png)](ad-hoc-distribution-images/cert-first-large.png#lightbox)

5. 按一下 [繼續]，並遵循指示以透過 Keychain 存取來建立憑證簽署要求：

   [![透過 Keychain 存取建立憑證簽署要求](ad-hoc-distribution-images/createcertmanually02.png)](ad-hoc-distribution-images/createcertmanually02.png#lightbox)

6. 依指示建立 CSR 之後，請按一下 [繼續] 並將 CSR 上傳到 Member Center：

   [![將 CSR 上傳到成員中心](ad-hoc-distribution-images/createcertmanually03.png)](ad-hoc-distribution-images/createcertmanually03.png#lightbox)

7. 按一下 [產生] 以建立您的憑證。
8. 最後，下載完成的憑證，並按兩下檔案以進行安裝。
9. 此時，電腦上應該已安裝憑證，但是您可能需要[重新整理設定檔](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download)，以確保其會顯示在 Xcode 中。

或者，也可以透過 Xcode 中的 [喜好設定] 對話方塊來要求憑證。 若要這樣做，請遵循下面的步驟：

1. 選取您的小組，然後按一下 [**管理憑證 ...**]： [ ![ 選取小組](ad-hoc-distribution-images/selectteam.png)](ad-hoc-distribution-images/selectteam.png#lightbox)

2. 接下來，按一下 [**加號（+）** ] 按鈕，然後選取 [ **ios app store**]： [ ![ 選取 [ios app store](ad-hoc-distribution-images/selectcert.png)](ad-hoc-distribution-images/selectcert.png#lightbox) ]

<a name="createprofile"></a>

## <a name="create-a-distribution-provisioning-profile"></a>建立散發佈建設定檔

<a name="createappid"></a>

### <a name="create-an-app-id"></a>建立應用程式識別碼
如同您所建立的任何其他佈建設定檔，此處也需要應用程式識別碼才能識別要散發給使用者裝置的應用程式。 如果您尚未建立應用程式識別碼，請遵循下列步驟來建立：

1. 在 [Apple Developer Center](https://developer.apple.com/account/overview.action) 中，瀏覽到「憑證、識別碼與設定檔」** 區段。 選取 [識別碼]**** 下的 [應用程式識別碼]****。
2. 按一下 [] **+** 按鈕，並提供可在入口網站中識別它的**名稱**。
3. 應用程式前置詞應該已設定為您的小組識別碼，且無法變更。 選取「明確」或「萬用字元」應用程式識別碼，並以反向 DNS 格式輸入套件組合識別碼，例如：
    - **明確**：`com.[DomainName].[AppName]`
    - **萬用字元**：`com.[DomainName].*`
4. 選取您的應用程式所需要的任何[應用程式服務](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioning-for-application-services)。
5. 按一下 [繼續]**** 按鈕，並遵循畫面上的指示來建立新的應用程式識別碼。

一旦您有建立散發設定檔所需的元件之後，請遵循下列步驟來建立散發設定檔：

1. 返回 Apple 布建入口網站，然後選取 [布建 **> 發佈**]：選取 [布建[ ![ > 散發](ad-hoc-distribution-images/distribute01.png)](ad-hoc-distribution-images/distribute01.png#lightbox)]

2. 按一下 [] **+** 按鈕，然後選取您想要建立為臨機操作的**Ad-Hoc**散發配置檔案類型：

    [![建立特定散發類型](ad-hoc-distribution-images/distribute02.png)](ad-hoc-distribution-images/distribute02.png#lightbox)

3. 按一下 [繼續]**** 按鈕，並從您要建立散發設定檔的下拉式清單中選取應用程式識別碼：

    [![從下拉式清單中選取 [應用程式識別碼]](ad-hoc-distribution-images/distribute03.png)](ad-hoc-distribution-images/distribute03.png#lightbox)

4. 按一下 [繼續]**** 按鈕，並選取簽署應用程式所需的散發憑證：

    [![選取簽署應用程式所需的散發憑證](ad-hoc-distribution-images/distribute04.png)](ad-hoc-distribution-images/distribute04.png#lightbox)

5. 按一下 [繼續]**** 按鈕，並針對新的散發設定檔輸入**名稱**：

    [![輸入新散發設定檔的名稱](ad-hoc-distribution-images/distribute06.png)](ad-hoc-distribution-images/distribute06.png#lightbox)

6. 按一下 [產生]**** 按鈕，以建立新的設定檔，並完成流程。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

您可能需要結束 Visual Studio for Mac 並讓 Xcode 重新整理其可用「簽署身分識別」和「佈建設定檔」的清單 (遵循[在 Xcode 中下載設定檔和憑證](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download)一節中的指示)，之後才能在 Visual Studio for Mac 中使用新的散發設定檔。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

您可能需要結束 Visual Studio 並讓 Xcode (在建置主機的 Mac 上) 重新整理其可用「簽署身分識別」和「佈建設定檔」的清單 (遵循[在 Xcode 中下載設定檔和憑證](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download)一節中的指示)，之後才能在 Visual Studio 中使用新的散發設定檔。

-----

<a name="selectprofile"></a>

## <a name="selecting-a-distribution-profile-in-a-xamarinios-project"></a>在 Xamarin.iOS 專案中選取散發設定檔

當您準備好進行 Xamarin.iOS 應用程式的最終建置，請選取上面所建立的散發設定檔。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

 請在 Visual Studio for Mac 中執行下列動作：

1. 在方案總管**** 中，按兩下專案名稱，將其開啟進行編輯。
2. 從 [組態]**** 下拉式清單中選取 [iOS Bundle Signing] (iOS 套件組合簽署)**** 和組建的類型：

    ![從 [組態] 下拉式清單中選取組建的類型](ad-hoc-distribution-images/releasexs01.png)
3. 在大多數情況下，可以保留 [簽署識別]**** 和 [佈建設定檔]**** 的預設值為 [自動] ****，Visual Studio for Mac 會根據 Info.plist 中的應用程式套件組合識別碼選擇正確的設定檔：

    ![預設值設為 [自動] 的 [簽署識別] 和 [佈建設定檔]](ad-hoc-distribution-images/releasexs02.png)
4. 視需要，可在下拉式清單中選取 [簽署識別] 和 [散發設定檔] (上面所建立的)：

    ![選取 [簽署識別] 和 [散發設定檔]](ad-hoc-distribution-images/releasexs03.png)
5. 按一下 [確定]**** 按鈕以儲存所做的變更。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)
 在 Visual Studio 中，執行下列操作：

1. 請以滑鼠右鍵按一下方案總管**** 中的專案名稱，並且選取 [屬性]****，將其開啟進行編輯。
2. 從 [組態]**** 下拉式清單中選取 [iOS Bundle Signing] (iOS 套件組合簽署)**** 和組建的類型：

    ![從 [組態] 下拉式清單中選取組建的類型](ad-hoc-distribution-images/releasevs01.png)
3. 在大多數情況下，可以保留 [簽署識別]**** 和 [佈建設定檔]**** 的預設值為 [自動]****，Visual Studio 會根據 Info.plist 中的應用程式套件組合識別碼選擇正確的設定檔：

    ![預設值設為 [自動] 的 [簽署識別] 和 [佈建設定檔]](ad-hoc-distribution-images/releasevs02.png)
4. 視需要，可在下拉式清單中選取 [簽署識別] 和 [散發設定檔] (上面所建立的)：

    ![選取 [簽署識別] 和 [散發設定檔]](ad-hoc-distribution-images/releasevs03.png)
5. 將您的變更儲存至專案屬性。

-----

<a name="adhoc"></a>

## <a name="ad-hoc-distribution"></a>臨機操作散發

雖然 [TestFlight](~/ios/deploy-test/testflight.md) 是常用的 Beta 測試和散發方法，但它是 iTunes Connect 的一部分，因此無法供 **Apple Developer Enterprise Program** 成員使用。

如果無法選擇使用 iTunes Connect，臨機操作散發可讓開發人員在許多裝置上對應用程式進行 Beta 測試。 臨機操作散發的運作方式與內部散發類似，需要建立 IPA；IPA 可使用無線散發或是透過 iTunes 手動散發。

<a name="IPA_Creation"></a>

### <a name="ipa-support-for-ad-hoc-deployment"></a>IPA 對臨機操作部署的支援

應用程式在佈建之後，可以封裝為稱為 *IPA* 的檔案。 這是包含應用程式與其他中繼資料和圖示的 ZIP 檔案。 IPA 可以用來在本機中將應用程式新增至 iTunes，以便應用程式能直接同步處理到佈建設定檔中所包含的裝置。

如需有關建立 .IPA 的詳細資訊，請參閱[.Ipa 支援](~/ios/deploy-test/app-distribution/ipa-support.md)指南。

## <a name="summary"></a>總結

本文介紹測試 Xamarin.iOS 應用程式所必須的 臨機操作散發機制。

## <a name="related-links"></a>相關連結

- [App Store 散發](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)
- [內部作業散發](~/ios/deploy-test/app-distribution/in-house-distribution.md)
- [Itunesmetadata.plist. plist 檔案](~/ios/deploy-test/app-distribution/itunesmetadata.md)
- [.IPA 支援](~/ios/deploy-test/app-distribution/ipa-support.md)
