---
title: 將 Xamarin.iOS 應用程式發佈到 App Store
description: 本文件描述如何設定、建置及發佈 Xamarin.iOS 應用程式，以在 App Store 中發佈。
ms.prod: xamarin
ms.assetid: DFBCC0BA-D233-4DC4-8545-AFBD3768C3B9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/25/2018
ms.openlocfilehash: babfc85de645b285812421d453d51d5be6dceb1c
ms.sourcegitcommit: 93c9fe61eb2cdfa530960b4253eb85161894c882
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55831933"
---
# <a name="publishing-xamarinios-apps-to-the-app-store"></a>將 Xamarin.iOS 應用程式發佈到 App Store

若要將應用程式發佈到 [App Store](https://www.apple.com/ios/app-store/)，應用程式開發人員必須先將其 (以及螢幕擷取畫面、描述、圖示和其他資訊) 提交給 Apple 進行審查。 應用程式一經核准，Apple 就會將它放在 App Store，使用者可以在其中購買並直接從其 iOS 裝置中進行安裝。

本指南描述要遵循的步驟，以便為 App Store 準備應用程式，並將它傳送給 Apple 進行審查。 特別是，它會描述：

> [!div class="checklist"]
> - 遵循 App Store 審查指導方針
> - 設定應用程式識別碼和權利
> - 提供 App Store 圖示和應用程式圖示
> - 設定 App Store 佈建設定檔
> - 更新 [發行] 組建組態
> - 在 iTunes Connect 中設定應用程式
> - 建置您的應用程式，並將它提交給 Apple

> [!IMPORTANT]
> Apple [已宣佈](https://developer.apple.com/ios/submit/)從 2019 年 3 月開始，提交至 App Store 的所有應用程式與更新，都必須使用 iOS 12.1 SDK 或更新版本 (包含在 Xcode 10.1 或更新版本中) 進行建置。
> 應用程式也應支援 iPhone XS 及 12.9" iPad Pro 的螢幕大小。

## <a name="app-store-guidelines"></a>App Store 指導方針

在 App Store 中提交要發佈的應用程式之前，請確定它符合 Apple 之 [App Store Review Guidelines](https://developer.apple.com/appstore/resources/approval/guidelines.html) (App Store 審查指導方針) 所定義的標準。
當您將應用程式提交給 App Store 時，Apple 會進行審查，以確定它符合這些需求。 如果不符合，Apple 將拒絕它，您必須解決上述問題，然後重新提交。
因此，在開發程序中盡早熟悉指導方針是個不錯的主意。

提交應用程式時有幾個特別需要注意的事項：

1. 確定應用程式的描述符合其功能。
2. 測試應用程式不會在正常使用情況下損毀。 這包含其所支援之每部 iOS 裝置的使用方式。

另請查看 Apple 提供的 [App Store 相關資源](https://developer.apple.com/app-store/resources/)。

## <a name="set-up-an-app-id-and-entitlements"></a>設定應用程式識別碼和權利

每個 iOS 應用程式具有唯一的應用程式識別碼，其擁有一組稱為「權利」的相關聯應用程式服務。 權利允許應用程式執行各種動作，例如接收推播通知、存取 HealthKit 之類的 iOS 功能等等。

若要建立應用程式識別碼，並選取任何所需的權利，請瀏覽 [Apple Developer 入口網站](https://developer.apple.com/account/)，並遵循下列步驟：

1. 在 [憑證、識別碼與設定檔] 區段中選取 [識別碼] > [應用程式識別碼]。
2. 按一下 [+] 按鈕，並為新應用程式提供**名稱**與**套件組合識別碼**。
3. 捲動至畫面底部並選取您 Xamarin.iOS 應用程式所需的所有**應用程式服務**。 應用程式服務進一步詳述於[使用 Xamarin.iOS 中的功能](~/ios/deploy-test/provisioning/capabilities/index.md)指南。
4. 按一下 [繼續] 按鈕，並遵循畫面上的指示來建立新的應用程式識別碼。

定義應用程式識別碼時，除了選取並設定必要的應用程式服務之外，您也必須藉由編輯 **Info.plist** 和 **Entitlements.plist** 檔案，在 Xamarin.iOS 專案中設定應用程式識別碼和權利。 如需詳細資訊，請查看[使用 Xamarin.iOS 中的權利](~/ios/deploy-test/provisioning/entitlements.md)指南，該指南描述如何建立 **Entitlements.plist** 檔案，以及其所包含之各種權利設定的意義。

## <a name="include-an-app-store-icon"></a>包含 App Store 圖示

當您將應用程式提交給 Apple 時，請確定其所含的資產目錄包含了 App Store 圖示。 若要了解如何執行這項操作，請查看 [Xamarin.iOS 中的 App Store 圖示](~/ios/app-fundamentals/images-icons/app-store-icon.md)指南。

## <a name="set-the-apps-icons-and-launch-screens"></a>設定應用程式圖示與啟動畫面

若要讓 Apple 在 App Store 提供 iOS 應用程式，對於可執行該應用程式的所有 iOS 裝置，iOS 應用程式必須具有適當的圖示與啟動畫面。 如需如何設定應用程式圖示與啟動畫面的詳細資訊，請參閱下列指南：

- [Xamarin.iOS 中的應用程式圖示](~/ios/app-fundamentals/images-icons/app-icons.md)
- [Xamarin.iOS 應用程式的啟動畫面](~/ios/app-fundamentals/images-icons/launch-screens.md)

## <a name="create-and-install-an-app-store-provisioning-profile"></a>建立並安裝 App Store 佈建設定檔

iOS 會使用佈建設定檔來控制特定應用程式組建的部署方式。 這些檔案包含用於簽署應用程式之憑證、應用程式識別碼，以及應用程式可安裝位置的相關資訊。 若為開發與臨機操作散發，佈建設定檔也會包含您可部署應用程式的允許裝置清單。 然而，因為公開散發的唯一管道是 App Store，所以 App Store 散發只會包含憑證與應用程式識別碼資訊。

若要建立並安裝 App Store 佈建設定檔，請遵循下列步驟：

1. 登入 [Apple Developer 入口網站](https://developer.apple.com/account/)。
2. 在 [憑證、識別碼與設定檔] 中，選取 [佈建設定檔] > [散發]。
3. 按一下 **+** 按鈕，選取 [App Store]，然後按一下 [繼續]。
4. 從清單中選取應用程式的 [應用程式識別碼]，然後按一下 [繼續]。
5. 選取簽署憑證，然後按一下 [繼續]。
6. 輸入 [設定檔名稱]，然後按一下 [繼續] 來產生設定檔。
7. 使用 Xamarin 的 [Apple 帳戶管理](~/cross-platform/macios/apple-account-management.md)工具，將新建立的佈建設定檔下載至您的 Mac。 如果您是在 Mac 上，也可以直接從 Apple Developer 入口網站下載佈建設定檔，並按兩下該檔案來進行安裝。

如需詳細指示，請參閱[建立散發設定檔](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioningprofile)與[在 Xamarin.iOS 專案中選取散發設定檔](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#selectprofile)。

## <a name="update-the-release-build-configuration"></a>更新發行組建組態

新的 Xamarin.iOS 專案會自動設定 [偵錯] 和 [發行] 組建組態。 若要適當設定 [發行] 組建，請遵循下列步驟：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 從 [Solution Pad] 中開啟 **Info.plist**。 選取 [手動佈建]。 儲存並關閉檔案。
2. 以滑鼠右鍵按一下 [Solution Pad] 中的 [專案名稱]，選取 [選項]，然後巡覽至 [iOS 組建] 索引標籤。
3. 將 [組態] 設定為 [發行]，並將 [平台] 設定為 [iPhone]。
4. 若要使用特定的 iOS SDK 建置，請從 [SDK 版本] 清單中進行選取。 否則，請將此值保留為 [預設]。
5. 連結可藉由移除未使用的程式碼，減少應用程式的整體大小。 在大部分情況下，[連結器行為] 應該設定為預設值 [僅連結 Framework SDK]。 在某些情況下，例如使用某些協力廠商程式庫時，可能需要將此值設定為 [不要連結]，以確保不會移除所需的程式碼。 如需詳細資訊，請參閱[連結 Xamarin.iOS 應用程式](~/ios/deploy-test/linker.md)指南。
6. 核取 [最佳化 PNG 影像]，進一步減少您的應用程式大小。
7. 因為偵錯會使組建產生不必要的大小，所以建議「不要」啟用。
8. 針對 iOS 11，請選取支援 **ARM64** 的其中一個裝置架構。 如需為 64 位元 iOS 裝置進行建置的詳細資訊，請參閱 [32/64 位元平台考量](~/cross-platform/macios/32-and-64/index.md)文件中的**啟用 Xamarin.iOS 應用程式的 64 位元組建**一節。
9. 您可能想要使用 **LLVM** 編譯器來建置較小且更快速的程式碼。 不過，此選項會增加編譯時間。
10. 根據您的應用程式需求，也可以考慮調整要使用的 [記憶體回收] 類型與 [國際化] 設定。

    設定上述選項之後，您的組建設定看起來應該像這樣：

    ![iOS 組建設定](publishing-to-the-app-store-images/build-m157.png "iOS 組建設定")

    另請查看 [iOS 組建機制](~/ios/deploy-test/ios-build-mechanics.md)指南，該指南會進一步描述組建設定。

11. 巡覽至 [iOS 套件組合簽署] 索引標籤。如果此處的選項不可編輯，請確定已在 **Info.plist** 檔案中選取 [手動佈建]。
12. 確定 [組態] 設定為 [發行]，且 [平台] 設定為 [iPhone]。
13. 將 [簽署身分識別] 設定為 [散發 (自動)]。
14. 針對 [佈建設定檔]，選取[上方建立](#create-and-install-an-app-store-provisioning-profile)的 App Store 佈建設定檔。

    您的專案套件組合簽署選項現在看起來像這樣：

    ![iOS 套件組合簽署](publishing-to-the-app-store-images/bundleSigning-m157.png "iOS 套件組合簽署")

15. 按一下 [確定] 儲存您對專案屬性進行的變更。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 確定 Visual Studio 2017 已[與 Mac 組建主機配對](~/ios/get-started/installation/windows/connecting-to-mac/index.md)。
2. 以滑鼠右鍵按一下 [方案總管] 中的 [專案名稱]，並選取 [屬性]。
3. 瀏覽至 [iOS 組建] 索引標籤，並將 [組態] 設定為 [發行]，同時將 [平台] 設定為 [iPhone]。
4. 若要使用特定的 iOS SDK 建置，請從 [SDK 版本] 清單中進行選取。 否則，請將此值保留為 [預設]。
5. 連結可藉由移除未使用的程式碼，減少應用程式的整體大小。 在大部分情況下，[連結器行為] 應該設定為預設值 [僅連結 Framework SDK]。 在某些情況下，例如使用某些協力廠商程式庫時，可能需要將此值設定為 [不要連結]，以確保不會移除所需的程式碼。 如需詳細資訊，請參閱[連結 Xamarin.iOS 應用程式](~/ios/deploy-test/linker.md)指南。
6. 核取 [最佳化 PNG 影像]，進一步減少您的應用程式大小。
7. 因為偵錯會使組建產生不必要的大小，所以建議不要啟用。
8. 針對 iOS 11，請選取支援 **ARM64** 的其中一個裝置架構。 如需為 64 位元 iOS 裝置進行建置的詳細資訊，請參閱 [32/64 位元平台考量](~/cross-platform/macios/32-and-64/index.md)文件中的**啟用 Xamarin.iOS 應用程式的 64 位元組建**一節。
9. 您可能想要使用 **LLVM** 編譯器來建置較小且更快速的程式碼。 不過，此選項會增加編譯時間。
10. 根據您的應用程式需求，也可以考慮調整要使用的 [記憶體回收] 類型與 [國際化] 設定。

    設定上述選項之後，您的組建設定看起來應該像這樣：

    ![iOS 組建設定](publishing-to-the-app-store-images/build-w157.png "iOS 組建設定")

    另請查看 [iOS 組建機制](~/ios/deploy-test/ios-build-mechanics.md)指南，該指南會進一步描述組建設定。

11. 巡覽至 [iOS 套件組合簽署] 索引標籤。確定 [組態] 已設定為 [發行]，且 [平台] 已設定為 [iPhone]，且已選取 [手動佈建]。
12. 將 [簽署身分識別] 設定為 [散發 (自動)]。
13. 針對 [佈建設定檔]，選取[上方建立](#create-and-install-an-app-store-provisioning-profile)的 App Store 佈建設定檔。

    您的專案套件組合簽署選項現在看起來像這樣：

    ![iOS 套件組合簽署設定](publishing-to-the-app-store-images/bundleSigning-w157.png "iOS 套件組合簽署設定")

14. 巡覽至 [iOS IPA 選項] 索引標籤。
15. 確定 [組態] 設定為 [發行]，且 [平台] 設定為 [iPhone]。
16. 核取 [建置 iTunes 套件封存檔 (IPA)] 核取方塊。 此設定會導致每個 [發行] 組建 (因為這是選取的組態) 產生 .ipa 檔案。 這個檔案可以提交給 Apple，以便在 App Store 上發行。

    > [!NOTE]
    > App Store 發行並不需要 **iTunes 中繼資料**和 **iTunesArtwork**。 如需詳細資訊，請查看 [Xamarin.iOS 應用程式中的 iTunesMetadata.plist 檔案](~/ios/deploy-test/app-distribution/itunesmetadata.md)和 [iTunes 插圖](~/ios/app-fundamentals/images-icons/app-icons.md#itunes-artwork)。

17. 若要指定不同於 Xamarin.iOS 專案名稱的 .ipa 檔名，請在 [套件名稱] 欄位中輸入它。

    ![iOS 套件組合簽署設定](publishing-to-the-app-store-images/ipaOptions-w157.png "iOS 套件組合簽署設定")

18. 儲存組建組態並關閉它。

-----

## <a name="configure-your-app-in-itunes-connect"></a>在 iTunes Connect 中設定您的應用程式

[iTunes Connect](https://itunesconnect.apple.com) 是一套 Web 架構工具，可讓您在 App Store 上管理 iOS 應用程式。 您必須先在 iTunes Connect 中正確設定 Xamarin.iOS 應用程式，才能將其提交給 Apple 進行審查，並在 App Store 上發行。

若要了解如何執行這項操作，請閱讀[在 iTunes Connect 中設定應用程式](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)指南。

## <a name="build-and-submit-your-app"></a>建置並提交您的應用程式

在正確設定您的組建設定且 iTunes Connect 正在等待您的提交之後，您現在可以建置您的應用程式，並將它提交給 Apple。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 在 Visual Studio for Mac 中，選取 [發行] 組建組態及要為其建置的裝置 (不是模擬器)。

    ![組建組態與平台選擇](publishing-to-the-app-store-images/chooseConfig-m157.png "組建組態與平台選擇")

2. 從 [建置] 功能表中選取 [封存以供發佈]。
3. 建立封存後，[封存] 檢視會隨即顯示：

    ![封存檢視](publishing-to-the-app-store-images/archives-m157.png "封存檢視")

    > [!NOTE]
    > 根據預設，[封存] 檢視只會顯示已開啟方案的封存。 若要查看所有具有封存的方案，請按一下 [顯示所有封存] 核取方塊。 最好是保留舊的封存，以便在必要時，可以使用它們包含的偵錯資訊將當機報告符號化。

4. 按一下 [簽署並散發...]，以開啟 [發佈精靈]。
5. 選取 [App Store] 散發通道。 按 [ **下一步**]。

    ![散發通道選擇](publishing-to-the-app-store-images/distChannel-m157.png "散發通道選擇")

6. 在 [佈建設定檔] 視窗中，選取您的簽署身分識別、應用程式和佈建設定檔。 按 [ **下一步**]。

    ![佈建設定檔選擇](publishing-to-the-app-store-images/provProfileSelect-m157.png "佈建設定檔選擇")

7. 驗證套件的詳細資料，然後按一下 [發佈] 以儲存應用程式的 .ipa 檔案：

    ![應用程式詳細資料驗證](publishing-to-the-app-store-images/publish-m157.png "應用程式詳細資料驗證")

8. 儲存 .ipa 後，即可準備將應用程式上傳至 iTunes Connect。

    ![準備開始提交](publishing-to-the-app-store-images/readyToGo-m157.png "準備開始提交")

9. 按一下 [開啟應用程式載入器] 並登入 (請注意，您必須為您的 Apple ID [建立應用程式專用密碼](https://support.apple.com/ht204397))。

    > [!NOTE]
    > 如需此工具的詳細資訊，請查看[有關應用程式載入器的 Apple 文件](https://help.apple.com/itc/apploader/#/apdS673accdb)。

10. 選取 [Deliver Your App] (傳遞您的應用程式)，並按一下 [選擇] 按鈕：

    ![選取 [傳遞您的應用程式]](publishing-to-the-app-store-images/publishvs01.png "選取 [傳遞您的應用程式]")

11. 選取您在上方建立的 .ipa 檔案，然後按一下 [確定] 按鈕。
12. 應用程式載入器將會驗證檔案：

    ![驗證畫面](publishing-to-the-app-store-images/publishvs02.png "驗證畫面")

13. 按一下 [下一步] 按鈕後，就會對 App Store 驗證應用程式：

    ![對 App Store 進行驗證](publishing-to-the-app-store-images/publishvs03.png "對 App Store 進行驗證")

14. 按一下 [傳送] 按鈕將應用程式傳送至 Apple 進行審查。
15. 當檔案已成功上傳時，應用程式載入器會通知您。

    > [!NOTE]
    > Apple 會拒絕 .ipa 檔案中包含 **iTunesMetadata.plist** 的應用程式，因而導致發生如下錯誤：
    >
    > `ERROR: ERROR ITMS-90047: "Disallowed paths ( "iTunesMetadata.plist" ) found at: Payload/iPhoneApp1.app"`
    >
    > 如需這項錯誤的因應措施，請查看 [Xamarin 論壇中的這篇文章](https://forums.xamarin.com/discussion/40388/disallowed-paths-itunesmetadata-plist-found-at-when-submitting-to-app-store/p1)。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

> [!NOTE]
> Visual Studio 2017 目前不支援位於 Visual Studio for Mac 中的 [封存以供發佈] 工作流程。

1. 確定 Visual Studio 2017 已[與 Mac 組建主機配對](~/ios/get-started/installation/windows/connecting-to-mac/index.md)。
2. 從 Visual Studio 2017 [方案組態] 下拉式清單中選取 [發行]，並從 [方案平台] 下拉式清單中選取 [iPhone]。

    ![組建組態與平台選擇](publishing-to-the-app-store-images/chooseConfig-w157.png "組建組態與平台選擇")

3. 建置專案。 這會建立 .ipa 檔案。

    > [!NOTE]
    > 本文件的[更新發行組建組態](#update-the-release-build-configuration)一節設定了應用程式的組建設定，以針對每個 [發行] 組建建立 .ipa 檔案。

4. 若要在 Windows 電腦上尋找 .ipa 檔案，請在 Visual Studio 2017 的 [方案總管] 中，以滑鼠右鍵按一下 Xamarin.iOS 專案名稱，然後選擇 [在檔案總管中開啟資料夾]。 然後，在剛剛開啟的 Windows **檔案總管**中，巡覽至 **bin/iPhone/Release** 子目錄。 除非您已[自訂 .ipa 檔案輸出位置](#customize-the-ipa-location)，否則它應該是位於此目錄中。
5. 若要改為在 Mac 組建主機上檢視 .ipa 檔案，請在 Visual Studio 2017 的 [方案總管] 中 (在 Windows 上)，以滑鼠右鍵按一下 Xamarin.iOS 專案名稱，然後選取 [顯示組建伺服器上的 IPA 檔案]。 這會在 Mac 組建主機上開啟 [搜尋工具] 視窗並選取 .ipa 檔案。
6. 在 Mac 組建主機上開啟 [應用程載入器]。 在 Xcode 中，選取 [Xcode] > [開啟開發人員工具] > [應用程式載入器]。

    > [!NOTE]
    > 如需此工具的詳細資訊，請查看[有關應用程式載入器的 Apple 文件](https://help.apple.com/itc/apploader/#/apdS673accdb)。

7. 登入應用程式載入器 (請注意，您必須為您的 Apple ID [建立應用程式專用密碼](https://support.apple.com/ht204397))。
8. 選取 [Deliver Your App] (傳遞您的應用程式)，並按一下 [選擇] 按鈕：

    ![選取 [傳遞您的應用程式]](publishing-to-the-app-store-images/publishvs01.png "選取 [傳遞您的應用程式]")

9. 選取上方建立的 .ipa 檔案，然後按一下 [確定]。
10. 應用程式載入器將會驗證檔案：

    ![驗證畫面](publishing-to-the-app-store-images/publishvs02.png "驗證畫面")

11. 按一下 [下一步] 按鈕後，就會對 App Store 驗證應用程式：

    ![對 App Store 進行驗證](publishing-to-the-app-store-images/publishvs03.png "對 App Store 進行驗證")

12. 按一下 [傳送] 按鈕將應用程式傳送至 Apple 進行審查。
13. 當檔案已成功上傳時，應用程式載入器會通知您。

    > [!NOTE]
    > Apple 會拒絕 .ipa 檔案中包含 **iTunesMetadata.plist** 的應用程式，因而導致發生如下錯誤：
    >
    > `ERROR: ERROR ITMS-90047: "Disallowed paths ( "iTunesMetadata.plist" ) found at: Payload/iPhoneApp1.app"`
    >
    > 如需這項錯誤的因應措施，請查看 [Xamarin 論壇中的這篇文章](https://forums.xamarin.com/discussion/40388/disallowed-paths-itunesmetadata-plist-found-at-when-submitting-to-app-store/p1)。

-----

## <a name="itunes-connect-status"></a>iTunes Connect 狀態

若要查看您的應用程式提交狀態，請登入 iTunes Connect，並選取您的應用程式。 初始狀態應該是**等候審查**，但在處理時可能會暫時顯示**已收到上傳**。

![等候審查](publishing-to-the-app-store-images/image21.png "等候審查")

## <a name="tips-and-tricks"></a>秘訣和訣竅

### <a name="customize-the-ipa-location"></a>自訂 .ipa 位置

**MSBuild** 屬性 `IpaPackageDir` 可讓您自訂 .ipa 檔案的輸出位置。 如果將 `IpaPackageDir` 設定為自訂位置，.ipa 檔案將會置於其中，而不是放在預設時間戳記子目錄。 建立須仰賴特定目錄路徑才能正常運作的自動組建 (例如用於持續整合 (CI) 組建) 時，這會很有用。

新屬性有數個可能的使用方式。 例如，若要將 .ipa 檔案輸出至舊的預設目錄 (如 Xamarin.iOS 9.6 和較舊版本)，您可以使用下列其中一種方法來將 `IpaPackageDir` 屬性設定為 `$(OutputPath)`。 兩種方法都與所有 Unified API Xamarin.iOS 組建相容，包括 IDE 組建以及使用 **msbuild** 或 **mdtool** 的命令列組建：

- 第一種選擇是在 **MSBuild** 檔案的 `<PropertyGroup>` 項目內設定 `IpaPackageDir` 屬性。 例如，您可以將下列 `<PropertyGroup>` 新增至 iOS 應用程式專案 .csproj 檔案的底部 (在結尾的 `</Project>` 標籤之前)：

    ```xml
    <PropertyGroup>
      <IpaPackageDir>$(OutputPath)</IpaPackageDir>
    </PropertyGroup>
    ```

- 較好的方法是將 `<IpaPackageDir>` 元素新增至現有 `<PropertyGroup>` 的底部，其對應用於建置 .ipa 檔案的組態。 這種做法較好，因為會在 iOS IPA 選項專案屬性頁面上以規劃的設定，準備具有未來相容性的專案。 如果您目前使用 `Release|iPhone` 組態來建置 .ipa 檔案，整個更新後的屬性群組看起來可能會類似下列內容：

    ```xml
    <PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Release|iPhone'">
       <Optimize>true</Optimize>
       <OutputPath>bin\iPhone\Release</OutputPath>
       <ErrorReport>prompt</ErrorReport>
       <WarningLevel>4</WarningLevel>
       <ConsolePause>false</ConsolePause>
       <CodesignKey>iPhone Developer</CodesignKey>
       <MtouchUseSGen>true</MtouchUseSGen>
       <MtouchUseRefCounting>true</MtouchUseRefCounting>
       <MtouchFloat32>true</MtouchFloat32>
       <CodesignEntitlements>Entitlements.plist</CodesignEntitlements>
       <MtouchLink>SdkOnly</MtouchLink>
       <MtouchArch>;ARMv7, ARM64</MtouchArch>
       <MtouchHttpClientHandler>HttpClientHandler</MtouchHttpClientHandler>
       <MtouchTlsProvider>Default</MtouchTlsProvider>
       <PlatformTarget>x86&</PlatformTarget>
       <BuildIpa>true</BuildIpa>
       <IpaPackageDir>$(OutputPath</IpaPackageDir>
    </PropertyGroup>
    ```

**msbuild** 命令列組建這項替代技術是新增 `/p:` 命令列引數來設定 `IpaPackageDir` 屬性。 在此情況下，請注意 **msbuild** 不會展開命令列上傳入的 `$()` 運算式，因此無法使用 `$(OutputPath)` 語法。 您必須改為提供完整路徑名稱。

```bash
msbuild /p:Configuration="Release" /p:Platform="iPhone" /p:ServerAddress="192.168.1.3" /p:ServerUser="macuser" /p:IpaPackageDir="%USERPROFILE%\Builds" /t:Build SingleViewIphone1.sln
```

或在 Mac 上可能如下：

```bash
msbuild /p:Configuration="Release" /p:Platform="iPhone" /p:IpaPackageDir="$HOME/Builds" /t:Build SingleViewIphone1.sln
```

建立與封存散發組建後，即可準備將您的應用程式提交至 iTunes Connect。

## <a name="summary"></a>總結

本文描述如何設定組建，並提交 iOS 應用程式以在 App Store 上發行。

## <a name="related-links"></a>相關連結

- [Apple Developer 入口網站 (Apple)](https://developer.apple.com/account/)
- [iTunes Connect (Apple)](https://itunesconnect.apple.com)
- [App Store 審查指導方針 (Apple)](https://developer.apple.com/appstore/resources/approval/guidelines.html)
- [常見的應用程式拒絕案例 (Apple)](https://developer.apple.com/app-store/review/rejections/)
- [使用 Xamarin.iOS 中的功能](~/ios/deploy-test/provisioning/capabilities/index.md)
- [使用 Xamarin.iOS 中的權利](~/ios/deploy-test/provisioning/entitlements.md)
- [在 iTunes Connect 中設定應用程式](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [Xamarin.iOS 中的應用程式圖示](~/ios/app-fundamentals/images-icons/app-icons.md)
- [Xamarin.iOS 應用程式的啟動畫面](~/ios/app-fundamentals/images-icons/launch-screens.md)
- [應用程式載入器文件 (Apple)](https://help.apple.com/itc/apploader/#/apdS673accdb)
