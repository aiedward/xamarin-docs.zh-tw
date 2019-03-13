---
title: App Store 散發
description: 本文件描述如何在 App Store 上發佈 Xamarin.iOS 應用程式。 它討論如何建立發佈憑證、如何建立散發佈建設定檔，以及如何設定 iTunes Connect 和提交應用程式。
ms.prod: xamarin
ms.assetid: B07E2C1F-A6DF-43CB-BFB0-0252A5558467
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/23/2017
ms.openlocfilehash: 14cf2910767e9c205e5ddc8f580020505f54ef46
ms.sourcegitcommit: 93c9fe61eb2cdfa530960b4253eb85161894c882
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55832024"
---
# <a name="app-store-distribution"></a>App Store 散發

完成 Xamarin.iOS 應用程式開發之後，軟體開發生命週期的下一步就是使用 iTunes App Store 將應用程式散發給使用者。 這是散發應用程式最常用的方式。 藉由 Apple 的 App Store 來發佈應用程式，可提供給全球使用者使用。

> [!IMPORTANT]
> Apple [已宣佈](https://developer.apple.com/ios/submit/)從 2019 年 3 月開始，提交至 App Store 的所有應用程式與更新，都必須使用 iOS 12.1 SDK 或更新版本 (包含在 Xcode 10.1 或更新版本中) 進行建置。
> 應用程式也應支援 iPhone XS 及 12.9" iPad Pro 的螢幕大小。

如同開發應用程式，散發應用程式需使用適當的「佈建設定檔」來佈建應用程式。 佈建設定檔是一種檔案，其包含程式碼簽署資訊、應用程式身分識別及預期的散發機制。 對於非 App Store 的散發，佈建設定檔也包含可用來部署應用程式之裝置的相關資訊。

> [!IMPORTANT]
> **重要：** 您**必須**是 Apple Developer Program 的個人或組織成員身分，才可使用 iTunes Connect 並將應用程式發佈至 App Store。 如果您是 Apple Developer **Enterprise** Program 的成員，將無法執行此頁面的操作步驟。

<a name="provisioning" />

## <a name="provisioning-an-app-for-app-store-distribution"></a>佈建適用於 App Store 散發的應用程式

不論您計劃如何發行 Xamarin.iOS 應用程式，都將必須建置特定的「散發佈建設定檔」。 此設定檔可讓應用程式進行數位簽署以便發行，使其可安裝在 iOS 裝置上。 類似於開發佈建設定檔，散發設定檔將包含下列內容：

- 應用程式識別碼
- 散發憑證

您可以選擇與用於開發佈建設定檔相同的**應用程式識別碼**和**裝置**；但如果您還沒有，則需要建立一個「散發憑證」，以在將應用程式提交到應用程式市集時，確認您的組織。 以下將介紹建立「散發憑證」的步驟。

> [!NOTE]
> 只有小組代理人和管理員可以建立散發憑證和佈建設定檔。

<a name="creatingcertificate" />

## <a name="creating-a-distribution-certificate"></a>建立散發憑證

1. 瀏覽至 Apple Developer Member Center 的「憑證、識別碼與設定檔」區段。
2. 在 [憑證] 下，選取 [生產環境]。
3. 按一下 [+] 按鈕以建立新的憑證。
4. 在「生產環境」標題下，選取 [App Store and Ad Hoc] (App Store 和臨機操作)：

    [![](images/createcertmanually01.png "選取 [App Store and Ad Hoc] (App Store 和臨機操作)")](images/createcertmanually01.png#lightbox)
5. 按一下 [繼續]，並遵循指示以透過 Keychain 存取來建立憑證簽署要求：

    [![](images/createcertmanually02.png "透過 Keychain 存取來建立憑證簽署要求")](images/createcertmanually02.png#lightbox)
6. 依指示建立 CSR 之後，請按一下 [繼續] 並將 CSR 上傳到 Member Center：

    [![](images/createcertmanually03.png "將 CSR 上傳到 Member Center")](images/createcertmanually03.png#lightbox)

7. 按一下 [產生] 以建立您的憑證。
8. 最後，**下載**完成的憑證，並按兩下檔案以進行安裝。
9. 此時，電腦上應該已安裝憑證，但是您可能需要[重新整理設定檔](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download)，以確保會顯示在 Xcode 中。

或者，也可以透過 Xcode 中的 [喜好設定] 對話方塊來要求憑證。 若要這樣做，請遵循下列步驟：

1.   選取您的小組，並按一下 [管理憑證…]：[![](images/selectteam.png "選取小組並檢視詳細資料")](images/selectteam.png#lightbox)

2.   接著，按一下 [iOS Distribution Certificate] (iOS 散發憑證) 旁邊的 [建立] 按鈕：[![](images/selectcert.png "建立 iOS 散發憑證")](images/selectcert.png#lightbox)

3.   如下所示，將根據您小組的權限產生簽署識別；您也可能需要等待小組專員或管理員批准：[![](images/generated.png "將產生簽署識別和顯示對話方塊")](images/generated.png#lightbox)


<a name="creatingprofile" />

## <a name="creating-a-distribution-profile"></a>建立散發設定檔

<a name="creatingappid" />

### <a name="creating-an-app-id"></a>建立應用程式識別碼

如同您所建立的其他任何佈建設定檔，此處也需要應用程式識別碼才能識別您要散發給使用者裝置的應用程式。 如果您尚未建立應用程式識別碼，請遵循下列步驟來建立：


1. 在 [Apple Developer Center](https://developer.apple.com/account/overview.action) 中，瀏覽到「憑證、識別碼與設定檔」區段。 選取 [識別碼] 下的 [應用程式識別碼]。
2. 按一下 [+] 按鈕，並提供可在入口網站中識別應用程式的**名稱**。
3. 應用程式前置詞應該已設定為您的小組識別碼，且無法變更。 選取「明確」或「萬用字元」應用程式識別碼，並以反向 DNS 格式輸入套件組合識別碼，例如：
    - **明確**com.[網域名稱].[應用程式名稱]
    - **萬用字元**com.[網域名稱].*
4. 選取您的應用程式所需要的任何[應用程式服務](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#appservices)。
5. 按一下 [繼續] 按鈕，然後依照畫面上的指示來建立新的 App ID (應用程式識別碼)。


### <a name="creating-a-provisioning-profile"></a>建立佈建設定檔

一旦您有建立散發設定檔所需的元件之後，請遵循下列步驟來建立散發設定檔：

1. 返回 Apple Provisioning Portal 並選取 [佈建] > [散發]：

    [![](images/distribute01.png "選取 [佈建] > [散發]")](images/distribute01.png#lightbox)

2. 按一下 [+] 按鈕，並選取您想要建立為  **App Store** 的散發設定檔類型：

    [![](images/distribute02.png "建立 App Store 散發設定檔")](images/distribute02.png#lightbox)

3. 按一下 [繼續] 按鈕，並從您要建立散發設定檔的下拉式清單中選取應用程式識別碼：

    [![](images/distribute03.png "從下拉式清單中選取應用程式識別碼")](images/distribute03.png#lightbox)

4. 按一下 [繼續] 按鈕，並選取簽署應用程式所需的憑證：

    [![](images/distribute04.png "選取簽署應用程式所需的憑證")](images/distribute04.png#lightbox)

5. 按一下 [繼續] 按鈕，然後選取允許執行 Xamarin.iOS 應用程式的 iOS 裝置：

    [![](images/distribute05.png "選取允許執行 Xamarin.iOS 應用程式的 iOS 裝置")](images/distribute05.png#lightbox)

6. 按一下 [繼續] 按鈕，並針對新的散發設定檔輸入**名稱**：

    [![](images/distribute06.png "針對新的散發設定檔輸入名稱")](images/distribute06.png#lightbox)

7. 按一下 [產生] 按鈕，以建立新的設定檔，並完成流程。


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

 您可能需要結束 Visual Studio for Mac 並讓 Xcode 重新整理其可用「簽署身分識別」和「佈建設定檔」的清單 (遵循[要求簽署身分識別](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download)一節中的指示)，之後才能在 Visual Studio for Mac 中使用新的散發設定檔。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

 您可能需要結束 Visual Studio 並讓 Xcode (在組建 主機的 Mac 上) 重新整理其可用「簽署身分識別」和「佈建設定檔」的清單 (遵循[要求簽署身分識別](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download)一節中的指示)，之後才能在 Visual Studio 中使用新的散發設定檔。

-----

<a name="selectprofile" />

## <a name="selecting-a-distribution-profile-in-a-xamarinios-project"></a>在 Xamarin.iOS 專案中選取散發設定檔

當您準備好為將於 iTunes App Store 上銷售的 Xamarin.iOS 應用程式進行最終建置，請選取上面所建立的散發設定檔。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

 請在 Visual Studio for Mac 中執行下列動作：

1. 在方案總管 中，按兩下專案名稱，將其開啟進行編輯。
2. 從 [組態] 下拉式清單中選取 [iOS Bundle Signing] (iOS 套件組合簽署) 和 [發行 | iPhone]：

    ![](images/releasexs01.png "從 [組態] 下拉式清單中選取 [發行 | iPhone]")
3. 在大多數情況下，可以保留 [簽署識別] 和 [佈建設定檔] 的預設值為 [自動] ，Visual Studio for Mac 會根據 Info.plist 中的套件組合識別碼選擇正確的設定檔：

    ![](images/releasexs02.png "預設值設為 [自動] 的 [簽署識別] 和 [佈建設定檔]")
4. 視需要，可在下拉式清單中選取 [簽署識別] 和 [散發設定檔] (上面所建立的)：

    ![](images/releasexs03.png "選取 [簽署識別] 和 [散發設定檔]")
5. 按一下 [確定] 按鈕以儲存所做的變更。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

 請在 Visual Studio 中執行下列動作：

1. 請以滑鼠右鍵按一下方案總管 中的專案名稱，並且選取 [屬性]，將其開啟進行編輯。
2. 從 [組態] 下拉式清單中選取 [iOS Bundle Signing] (iOS 套件組合簽署) 和 [發行 | iPhone]：

    ![](images/releasevs01.png "從 [組態] 下拉式清單中選取 [發行 | iPhone]")
3. 在大多數情況下，可以保留 [簽署識別] 和 [佈建設定檔] 的預設值為 [自動] ，Visual Studio 會根據 Info.plist 中的應用程式套件組合識別碼選擇正確的設定檔。

    ![](images/releasevs02.png "預設值設為 [自動] 的 [簽署識別] 和 [佈建設定檔]")
4. 視需要，可在下拉式清單中選取 [簽署識別] 和 [散發設定檔] (上面所建立的)：

    ![](images/releasevs03.png "選取 [簽署識別] 和 [散發設定檔]")
5. 將您的變更儲存至專案屬性。

-----

<a name="itunesconnect" />

## <a name="configuring-your-application-in-itunes-connect"></a>在 iTunes Connect 中設定應用程式

成功佈建應用程式後，下一步是在 [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa) 中設定應用程式，iTunes Connect 是一套可管理 App Store 中 iOS 應用程式和進行其他作業的 Web 工具。

您必須先在 iTunes Connect 中正確安裝並設定 Xamarin.iOS 應用程式，才能將其提交給 Apple 進行審查，最終達到在 App Store 作為付費或免費 App 發行的目標。

如需更多詳細資料，請參閱我們的[在 iTunes Connect 中設定應用程式](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)文件。

<a name="submitting" />

## <a name="submitting-an-app-to-itunes-connect"></a>將 iTunes Connect 提交至應用程式

一旦應用程式已使用「發佈佈建設定檔」進行簽署並已在 iTunes Connect 中建立之後，可將應用程式的二進位檔案上傳到 Apple 以供檢閱。 成功通過 Apple 的審查後，即可將其發佈至 App Store。

如需如何將應用程式發佈至 App Store 的詳細資訊，請參閱[發佈至 App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)。

<a name="windows" />

## <a name="automatically-copy-app-bundles-back-to-windows"></a>自動將 .app 套件組合複製回 Windows

[!include[](~/ios/includes/copy-app-bundle-to-windows.md)]

## <a name="summary"></a>總結

本文涵蓋了準備在 App Store 中發佈 Xamarin.iOS 應用程式的關鍵元件。

## <a name="related-links"></a>相關連結

- [在 iTunes Connect 中設定應用程式](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [發行至 App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)
- [內部作業散發](~/ios/deploy-test/app-distribution/in-house-distribution.md)
- [臨機操作散發](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)
- [iTunesMetadata.plist 檔案](~/ios/deploy-test/app-distribution/itunesmetadata.md)
- [IPA 支援](~/ios/deploy-test/app-distribution/ipa-support.md)
