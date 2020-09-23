---
title: 將 Xamarin.iOS 應用程式發佈到 App Store
description: 本文件描述如何設定、建置及發佈 Xamarin.iOS 應用程式，以在 App Store 中發佈。
ms.prod: xamarin
ms.assetid: DFBCC0BA-D233-4DC4-8545-AFBD3768C3B9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/25/2018
ms.openlocfilehash: 1f556e7b63cabcb2a0909927df4970d367338694
ms.sourcegitcommit: 2d41e287ccf937efa24813151e371298b2b4a582
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2020
ms.locfileid: "91102280"
---
# <a name="publishing-xamarinios-apps-to-the-app-store"></a>將 Xamarin.iOS 應用程式發佈到 App Store

若要將應用程式發佈到 [App Store](https://www.apple.com/ios/app-store/)，應用程式開發人員必須先將其 (以及螢幕擷取畫面、描述、圖示和其他資訊) 提交給 Apple 進行審查。 應用程式一經核准，Apple 就會將它放在 App Store，使用者可以在其中購買並直接從其 iOS 裝置中進行安裝。

本指南描述要遵循的步驟，以便為 App Store 準備應用程式，並將它傳送給 Apple 進行審查。 特別是，它會描述：

> [!div class="checklist"]
>
> - 遵循 App Store 審查指導方針
> - 設定應用程式識別碼和權利
> - 提供 App Store 圖示和應用程式圖示
> - 設定 App Store 佈建設定檔
> - 更新 [發行]**** 組建組態
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

每個 iOS 應用程式具有唯一的應用程式識別碼，其擁有一組稱為「權利」** 的相關聯應用程式服務。 權利允許應用程式執行各種動作，例如接收推播通知、存取 HealthKit 之類的 iOS 功能等等。

若要建立應用程式識別碼，並選取任何所需的權利，請瀏覽 [Apple Developer 入口網站](https://developer.apple.com/account/)，並遵循下列步驟：

1. 登入 [Apple Developer 入口網站](https://developer.apple.com/account/)。 如果您還沒有 Apple ID，請先 [建立 APPLE id](https://appleid.apple.com/account?appId=632&returnUrl=https%3A%2F%2Fdeveloper.apple.com%2Faccount%2F#!&page=create) 。
2. 移至 [ **憑證、識別碼 & 設定檔** ] 區段，然後移至 [ **識別碼** ] 區段。
3. 按一下 **+** 頁面頂端的 [識別碼] 標題旁的按鈕。
4. 選取 **應用程式識別碼** ，然後按一下 [ **繼續**]。 
5. 選取 [ **應用程式** ]，然後按一下 [ **繼續**]。
6. 輸入新應用程式的 **描述** 和套件組合 **識別碼** ，然後選取您的 Xamarin iOS 應用程式所需的任何 **功能** 。 應用程式服務進一步詳述於[使用 Xamarin.iOS 中的功能](~/ios/deploy-test/provisioning/capabilities/index.md)指南。 當您完成選擇時，請按一下 [ **繼續**]。
7. 按一下 [ **註冊** ] 按鈕，完成建立新的應用程式識別碼的程式。

定義應用程式識別碼時，除了選取並設定必要的應用程式服務之外，您也必須藉由編輯 **Info.plist** 和 **Entitlements.plist** 檔案，在 Xamarin.iOS 專案中設定應用程式識別碼和權利。 如需詳細資訊，請查看[使用 Xamarin.iOS 中的權利](~/ios/deploy-test/provisioning/entitlements.md)指南，該指南描述如何建立 **Entitlements.plist** 檔案，以及其所包含之各種權利設定的意義。

## <a name="include-an-app-store-icon"></a>包含 App Store 圖示

當您將應用程式提交給 Apple 時，請確定其所含的資產目錄包含了 App Store 圖示。 若要了解如何執行這項操作，請查看 [Xamarin.iOS 中的 App Store 圖示](~/ios/app-fundamentals/images-icons/app-store-icon.md)指南。

## <a name="set-the-apps-icons-and-launch-screens"></a>設定應用程式圖示與啟動畫面

若要讓 Apple 在 App Store 提供 iOS 應用程式，對於可執行該應用程式的所有 iOS 裝置，iOS 應用程式必須具有適當的圖示與啟動畫面。 如需如何設定應用程式圖示與啟動畫面的詳細資訊，請參閱下列指南：

- [Xamarin.iOS 中的應用程式圖示](~/ios/app-fundamentals/images-icons/app-icons.md)
- [Xamarin.iOS 應用程式的啟動畫面](~/ios/app-fundamentals/images-icons/launch-screens.md)

## <a name="create-and-install-an-app-store-provisioning-profile"></a>建立並安裝 App Store 佈建設定檔

iOS 會使用佈建設定檔** 來控制特定應用程式組建的部署方式。 這些檔案包含用於簽署應用程式之憑證、應用程式識別碼，以及應用程式可安裝位置的相關資訊。 若為開發與臨機操作散發，佈建設定檔也會包含您可部署應用程式的允許裝置清單。 然而，因為公開散發的唯一管道是 App Store，所以 App Store 散發只會包含憑證與應用程式識別碼資訊。

若要建立並安裝 App Store 佈建設定檔，請遵循下列步驟：

1. 登入 [Apple Developer 入口網站](https://developer.apple.com/account/)。
2. 移至 [ **憑證、識別碼 & 設定檔** ] 區段，然後移至 [ **設定檔** ] 區段。
3. 按一下該 **+** 按鈕，選取 [ **IOS 應用程式開發** ] 和 [ **App Store**]，然後按一下 [ **繼續**]。
4. 從清單中選取應用程式的 [應用程式識別碼]****，然後按一下 [繼續]****。
5. 選取簽署憑證，然後按一下 [繼續]****。
6. 選取要包含在此設定檔中的裝置，然後按一下 [ **繼續**]。
7. 輸入布建 **設定檔名稱** ，然後按一下 [ **產生** ] 以產生設定檔。
8. 使用 Xamarin 的 [Apple 帳戶管理](~/cross-platform/macios/apple-account-management.md)工具，將新建立的佈建設定檔下載至您的 Mac。 如果您是在 Mac 上，也可以直接從 Apple Developer 入口網站下載佈建設定檔，並按兩下該檔案來進行安裝。

如需詳細指示，請參閱[建立散發設定檔](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioningprofile)與[在 Xamarin.iOS 專案中選取散發設定檔](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#selectprofile)。

## <a name="update-the-release-build-configuration"></a>更新發行組建組態

新的 Xamarin.iOS 專案會自動設定 [偵錯]**** 和 [發行]** 組建組態** __。 若要適當設定 [發行]**** 組建，請遵循下列步驟：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 從 **Solution Pad**中，開啟位於 iOS 專案內的 **plist** 檔案。 
2. 請確定您是在 **應用程式** 視圖中。 您可以按一下下方導覽列中的選項來設定此選項。
3. 選取 [ **手動** 布建] 作為簽署選項。 儲存並關閉檔案。
4. 以滑鼠右鍵按一下 [Solution Pad]**** 中的 [專案名稱]****，選取 [選項]****，然後巡覽至 [iOS 組建]**** 索引標籤。
5. 將 [組態]**** 設定為 [發行]****，並將 [平台]**** 設定為 [iPhone]****。
6. 若要使用特定的 iOS SDK 建置，請從 [SDK 版本]**** 清單中進行選取。 否則，請將此值保留為 [預設]****。
7. 連結可藉由移除未使用的程式碼，減少應用程式的整體大小。
   * 在大部分情況下， **連結器行為** 應該設定為 **僅限連結 Framework sdk**的預設值。
   * 使用 [ **不要連結** ] 選項可能會導致 Apple 拒絕應用程式，因為在 Xamarin 中，會使用 [ **僅連結 Framework sdk** ] 選項連結的非公用 ios api
   * **Link all** 應謹慎使用，因為它會從專案中的所有元件（包括協力廠商程式庫）中去除程式碼。 它可以去除協力廠商程式庫只能透過連結器無法偵測到的反映使用的程式碼，因為它會進行靜態程式碼分析，以判斷正在使用的程式庫程式碼。 請小心使用 **Link** ，因為您可能必須手動保留一些類別和（或）方法，以避免因為遺失程式碼而導致執行時間失敗。
   * 如需詳細資訊，請參閱[連結 Xamarin.iOS 應用程式](~/ios/deploy-test/linker.md)指南。
8. 針對 iOS 11，請選取支援 **ARM64** 的其中一個裝置架構。 如需為 64 位元 iOS 裝置進行建置的詳細資訊，請參閱 [32/64 位元平台考量](~/cross-platform/macios/32-and-64/index.md)文件中的**啟用 Xamarin.iOS 應用程式的 64 位元組建**一節。
9. 您可能想要使用 **LLVM** 編譯器來建置較小且更快速的程式碼。 不過，此選項會增加編譯時間。
10. 核取 [最佳化 PNG 影像]****，進一步減少您的應用程式大小。
11. 不應啟用調試，因為它會使組建 _不_ 必要的大小變大。
12. 根據您的應用程式需求，也可以考慮調整要使用的**記憶體回收**類型與**國際化**設定。
    
    如上所述設定選項之後，您的組建設定看起來應該像這樣：

    ![iOS 組建設定](publishing-to-the-app-store-images/build-m157.png "iOS 組建設定")

    另請參閱《 [iOS 組建機制](~/ios/deploy-test/ios-build-mechanics.md) 指南》，此指南會進一步說明組建設定。

13. 流覽至 [ **iOS**套件組合簽署] 索引標籤。如果無法編輯此處的選項，請確定已在**plist**檔案中選取 [**手動**布建]。
14. 確定 [組態]**** 設定為 [發行]****，且 [平台]**** 設定為 [iPhone]****。
15. 將 [簽署身分識別]**** 設定為 [散發 (自動)]****。
16. 針對 [佈建設定檔]****，選取[上方建立](#create-and-install-an-app-store-provisioning-profile)的 App Store 佈建設定檔。

    您的專案套件組合簽署選項現在看起來像這樣：

    ![iOS 套件組合簽署](publishing-to-the-app-store-images/bundleSigning-m157.png "iOS 套件組合簽署")

17. 按一下 [確定]**** 儲存您對專案屬性進行的變更。

# <a name="visual-studio-2019"></a>[Visual Studio 2019](#tab/windows)

1. 請確定 Visual Studio 2019 已 [與 Mac 組建主機配對](~/ios/get-started/installation/windows/connecting-to-mac/index.md)。
2. 以滑鼠右鍵按一下 [方案總管]**** 中的 [專案名稱]****，並選取 [屬性]****。
3. 瀏覽至 [iOS 組建]**** 索引標籤，並將 [組態]**** 設定為 [發行]****，同時將 [平台]**** 設定為 [iPhone]****。
4. 若要使用特定的 iOS SDK 來建立，請從 [ **SDK 版本** ] 清單中選取它。 否則，請將此值保留為 [預設]****。
5. 連結可藉由移除未使用的程式碼，減少應用程式的整體大小。
   * 在大部分情況下， **連結器行為** 應該設定為 **僅限連結 Framework sdk**的預設值。
   * 使用 [ **不要連結** ] 選項可能會導致 Apple 拒絕應用程式，因為在 Xamarin 中，會使用 [ **僅連結 Framework sdk** ] 選項連結的非公用 ios api
   * **Link all** 應謹慎使用，因為它會從專案中的所有元件（包括協力廠商程式庫）中去除程式碼。 它可以去除協力廠商程式庫只能透過連結器無法偵測到的反映使用的程式碼，因為它會進行靜態程式碼分析，以判斷正在使用的程式庫程式碼。 請小心使用 **Link** ，因為您可能必須手動保留一些類別和（或）方法，以避免因為遺失程式碼而導致執行時間失敗。
   * 如需詳細資訊，請參閱[連結 Xamarin.iOS 應用程式](~/ios/deploy-test/linker.md)指南。
6. 針對 iOS 11，請選取支援 **ARM64** 的其中一個裝置架構。 如需為 64 位元 iOS 裝置進行建置的詳細資訊，請參閱 [32/64 位元平台考量](~/cross-platform/macios/32-and-64/index.md)文件中的**啟用 Xamarin.iOS 應用程式的 64 位元組建**一節。
7. 您可能想要使用 **LLVM** 編譯器來建置較小且更快速的程式碼。 不過，此選項會增加編譯時間。
8. 核取 [最佳化 PNG 影像]****，進一步減少您的應用程式大小。
9. 不應啟用調試，因為它會使組建 _不_ 必要的大小變大。
10. 根據您的應用程式需求，也可以考慮調整要使用的**記憶體回收**類型與**國際化**設定。

    設定上述選項之後，您的組建設定看起來應該像這樣：

    ![iOS 組建設定](publishing-to-the-app-store-images/build-w157.png "iOS 組建設定")

    另請參閱《 [iOS 組建機制](~/ios/deploy-test/ios-build-mechanics.md) 指南》，此指南會進一步說明組建設定。

11. 流覽至 [ **iOS** 套件組合簽署] 索引標籤。請確定設定 **已設定為** [ **發行**]、[ **平臺** ] 設定為 [ **IPhone**]，並已選取 [ **手動** 布建]。
12. 將 [簽署身分識別]**** 設定為 [散發 (自動)]****。
13. 針對 [佈建設定檔]****，選取[上方建立](#create-and-install-an-app-store-provisioning-profile)的 App Store 佈建設定檔。

    您的專案套件組合簽署選項現在看起來像這樣：

    ![iOS 套件組合簽署設定](publishing-to-the-app-store-images/bundleSigning-w157.png "iOS 套件組合簽署設定")

14. 儲存組建組態並關閉它。

# <a name="visual-studio-2017"></a>[Visual Studio 2017](#tab/win-vs2017)

1. 確定 Visual Studio 2017 已[與 Mac 組建主機配對](~/ios/get-started/installation/windows/connecting-to-mac/index.md)。
2. 以滑鼠右鍵按一下 [方案總管]**** 中的 [專案名稱]****，並選取 [屬性]****。
3. 瀏覽至 [iOS 組建]**** 索引標籤，並將 [組態]**** 設定為 [發行]****，同時將 [平台]**** 設定為 [iPhone]****。
4. 若要使用特定的 iOS SDK 來建立，請從 [ **SDK 版本** ] 清單中選取它。 否則，請將此值保留為 [預設]****。
5. 連結可藉由移除未使用的程式碼，減少應用程式的整體大小。
   * 在大部分情況下， **連結器行為** 應該設定為 **僅限連結 Framework sdk**的預設值。
   * 使用 [ **不要連結** ] 選項可能會導致 Apple 拒絕應用程式，因為在 Xamarin 中，會使用 [ **僅連結 Framework sdk** ] 選項連結的非公用 ios api
   * **Link all** 應謹慎使用，因為它會從專案中的所有元件（包括協力廠商程式庫）中去除程式碼。 它可以去除協力廠商程式庫只能透過連結器無法偵測到的反映使用的程式碼，因為它會進行靜態程式碼分析，以判斷正在使用的程式庫程式碼。 請小心使用 **Link** ，因為您可能必須手動保留某些類別和（或）方法等，以避免因為遺失程式碼而導致執行時間失敗。
   * 如需詳細資訊，請參閱[連結 Xamarin.iOS 應用程式](~/ios/deploy-test/linker.md)指南。
6. 針對 iOS 11，請選取支援 **ARM64** 的其中一個裝置架構。 如需為 64 位元 iOS 裝置進行建置的詳細資訊，請參閱 [32/64 位元平台考量](~/cross-platform/macios/32-and-64/index.md)文件中的**啟用 Xamarin.iOS 應用程式的 64 位元組建**一節。
7. 您可能想要使用 **LLVM** 編譯器來建置較小且更快速的程式碼。 不過，此選項會增加編譯時間。
8. 核取 [最佳化 PNG 影像]****，進一步減少您的應用程式大小。
9. 不應啟用調試，因為它會使組建 _不_ 必要的大小變大。
10. 根據您的應用程式需求，也可以考慮調整要使用的 [記憶體回收]**** 類型與 [國際化]**** 設定。

    設定上述選項之後，您的組建設定看起來應該像這樣：

    ![iOS 組建設定](publishing-to-the-app-store-images/build-w157.png "iOS 組建設定")

    另請參閱《 [iOS 組建機制](~/ios/deploy-test/ios-build-mechanics.md) 指南》，此指南會進一步說明組建設定。

11. 流覽至 [ **iOS** 套件組合簽署] 索引標籤。請確定設定 **已設定為** [ **發行**]、[ **平臺** ] 設定為 [ **IPhone**]，並已選取 [ **手動** 布建]。
12. 將 [簽署身分識別]**** 設定為 [散發 (自動)]****。
13. 針對 [佈建設定檔]****，選取[上方建立](#create-and-install-an-app-store-provisioning-profile)的 App Store 佈建設定檔。

    您的專案套件組合簽署選項現在看起來像這樣：

    ![iOS 套件組合簽署設定](publishing-to-the-app-store-images/bundleSigning-w157.png "iOS 套件組合簽署設定")

14. 巡覽至 [iOS IPA 選項]**** 索引標籤。
15. 確定 [組態]**** 設定為 [發行]****，且 [平台]**** 設定為 [iPhone]****。
16. 核取 [建置 iTunes 套件封存檔 (IPA)]**** 核取方塊。 此設定會導致每個 [發行]**** 組建 (因為這是選取的組態) 產生 .ipa 檔案。 這個檔案可以提交給 Apple，以便在 App Store 上發行。

    > [!NOTE]
    > App Store 發行並不需要 **iTunes 中繼資料**和 **iTunesArtwork**。 如需詳細資訊，請查看 [Xamarin.iOS 應用程式中的 iTunesMetadata.plist 檔案](~/ios/deploy-test/app-distribution/itunesmetadata.md)和 [iTunes 插圖](~/ios/app-fundamentals/images-icons/app-icons.md#itunes-artwork)。

17. 若要指定不同於 Xamarin.iOS 專案名稱的 .ipa 檔名，請在 [套件名稱]**** 欄位中輸入它。

    ![iOS 套件組合簽署設定](publishing-to-the-app-store-images/ipaOptions-w157.png "iOS 套件組合簽署設定")

18. 儲存組建組態並關閉它。

-----

## <a name="configure-your-app-in-itunes-connect"></a>在 iTunes Connect 中設定您的應用程式

[iTunes Connect](https://itunesconnect.apple.com) 是一套 Web 架構工具，可讓您在 App Store 上管理 iOS 應用程式。 您必須先在 iTunes Connect 中正確設定 Xamarin.iOS 應用程式，才能將其提交給 Apple 進行審查，並在 App Store 上發行。

若要了解如何執行這項操作，請閱讀[在 iTunes Connect 中設定應用程式](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)指南。

## <a name="build-and-submit-your-app"></a>建置並提交您的應用程式

在正確設定您的組建設定且 iTunes Connect 正在等待您的提交之後，您現在可以建置您的應用程式，並將它提交給 Apple。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 Visual Studio for Mac 中，選取 [發行]**** 組建組態及要為其建置的裝置 (不是模擬器)。

    ![組建設定和平臺選擇](publishing-to-the-app-store-images/chooseConfig-m157.png "組建設定和平臺選擇")

2. 從 [ **組建** ] 功能表中，選取 [封存 **以供發行**]。
3. 封存建立之後，將會顯示 [封存 **] 視圖。** 按一下 [簽署並散發...]****，以開啟 [發佈精靈]。

    ![[封存] 視圖中 [簽署並散發] 按鈕位置的螢幕擷取畫面。](publishing-to-the-app-store-images/archives-mac.png "[封存] 視圖中 [簽署並散發] 按鈕位置的螢幕擷取畫面。")

    > [!NOTE]
    > 根據預設，[封存]**** 檢視只會顯示已開啟方案的封存。 若要查看所有具有封存的方案，請按一下 [顯示所有封存]**** 核取方塊。 最好是保留舊的封存，以便在必要時，可以使用它們包含的偵錯資訊將當機報告符號化。

4. 選取 [App Store]**** 散發通道。 按 [下一步]  。

5. 選取 [ **上傳** ] 作為目的地。 按 [下一步]  。

6. 在 [佈建設定檔]**** 視窗中，選取您的簽署身分識別、應用程式和佈建設定檔。 按 [下一步]  。

    ![[布建設定檔] 頁面的螢幕擷取畫面，其中顯示有效的簽署身分識別、應用程式和布建設定檔選取專案。](publishing-to-the-app-store-images/provProfileSelect-mac.png "[布建設定檔] 頁面的螢幕擷取畫面，其中已選取有效的簽署身分識別、應用程式和布建設定檔。")

7. 在 [ **連接資訊 App Store** ] 視窗中，從功能表中選取 Apple ID 使用者名稱，然後輸入 [應用程式特定的密碼](https://support.apple.com/ht204397)。 按 [下一步]  。

    ![App Store Connect 資訊嚮導] 頁面的螢幕擷取畫面，其中顯示已選取的 Apple ID 使用者名稱。](publishing-to-the-app-store-images/connectInfo-mac.png "App Store Connect 資訊嚮導] 頁面的螢幕擷取畫面，其中顯示已選取的 Apple ID 使用者名稱。")

8. 確認套件的詳細資料，然後按一下 [ **發佈**]。 選取要儲存 ipa 檔案的位置之後，嚮導會將您的應用程式上傳至 App Store 連接。

    > [!NOTE]
    > Apple 會拒絕 .ipa 檔案中包含 **iTunesMetadata.plist** 的應用程式，因而導致發生如下錯誤：
    >
    > `ERROR: ERROR ITMS-90047: "Disallowed paths ( "iTunesMetadata.plist" ) found at: Payload/iPhoneApp1.app"`
    >
    > 如需這項錯誤的因應措施，請查看 [Xamarin 論壇中的這篇文章](https://forums.xamarin.com/discussion/40388/disallowed-paths-itunesmetadata-plist-found-at-when-submitting-to-app-store/p1)。

# <a name="visual-studio-2019"></a>[Visual Studio 2019](#tab/windows)

> [!NOTE]
> Visual Studio 2019 16.3 版和更新版本支援發行至 App Store。

1. 請確定 Visual Studio 2019 已與 [Mac 組建主機配對](~/ios/get-started/installation/windows/connecting-to-mac/index.md)。
2. 從 [**方案**設定] 下拉式清單中選取 [**發行**]，然後從 [**方案平臺**] 下拉式清單中選取**iPhone** 。

    ![顯示 [方案設定] 設為 [發行]、將 [方案平臺] 設定為 [iPhone] 和 [目標設定為裝置] 的 [Visual Studio] 工具列螢幕擷取畫面](publishing-to-the-app-store-images/chooseConfig-w157.png "顯示 [方案設定] 設為 [發行]、將 [方案平臺] 設定為 [iPhone] 和 [目標設定為裝置] 的 [Visual Studio] 工具列螢幕擷取畫面")

3. 從 [ **組建** ] 功能表中，選取 [封存 **...**]。這會開啟「封存 **管理員** 」並開始建立封存。

4. 一旦建立封存，請按一下 [ **散發** ] 以開啟發佈嚮導。

    ![封存管理員視圖中 [發佈] 按鈕位置的螢幕擷取畫面。](publishing-to-the-app-store-images/archives-win.png "封存管理員視圖中 [發佈] 按鈕位置的螢幕擷取畫面。")

5. 選取 [App Store]**** 散發通道。

6. 選取您的簽署身分識別和布建設定檔。 按一下 **[上傳] 以儲存**。

    ![顯示有效簽署身分識別和布建設定檔選取專案的 [發佈嚮導] 螢幕擷取畫面。](publishing-to-the-app-store-images/provProfileSelect-win.png "顯示有效簽署身分識別和布建設定檔選取專案的 [發佈嚮導] 螢幕擷取畫面。")

7. 輸入您的 Apple ID 和 [應用程式特定密碼](https://support.apple.com/ht204397)。 按一下 **[確定]** ，開始將您的應用程式上傳至 App Store 連接。

    ![快顯視窗的螢幕擷取畫面，可輸入您的 Apple ID 和應用程式特定密碼。](publishing-to-the-app-store-images/connectInfo-win.png "快顯視窗的螢幕擷取畫面，可輸入您的 Apple ID 和應用程式特定密碼。")

# <a name="visual-studio-2017"></a>[Visual Studio 2017](#tab/win-vs2017)

> [!NOTE]
> Visual Studio 2017 不支援在 Visual Studio for Mac 和 Visual Studio 2019 中找到的完整發行工作流程。
>
> 下列步驟適用于 Xcode 10。
>
> 您仍然可以遵循下列步驟來建立。IPA 檔案，但若要使用 Xcode 11 (部署到 App Store，則必須 [使用 Visual Studio for Mac](?tabs=macos#build-and-submit-your-app)的 iOS 13 支援) 。

1. 確定 Visual Studio 2017 已[與 Mac 組建主機配對](~/ios/get-started/installation/windows/connecting-to-mac/index.md)。
2. 從 Visual Studio 2017 [方案組態]**** 下拉式清單中選取 [發行]****，並從 [方案平台]**** 下拉式清單中選取 [iPhone]****。

    ![組建設定和平臺選擇](publishing-to-the-app-store-images/chooseConfig-w157.png "組建設定和平臺選擇")

3. 建置專案。 這會建立 .ipa 檔案。

    > [!NOTE]
    > 本文件的[更新發行組建組態](#update-the-release-build-configuration)一節設定了應用程式的組建設定，以針對每個 [發行]**** 組建建立 .ipa 檔案。

4. 若要在 Windows 電腦上尋找 .ipa 檔案，請在 Visual Studio 2019 或 Visual Studio 2017 的 [方案總管]**** 中，以滑鼠右鍵按一下 Xamarin.iOS 專案名稱，然後選擇 [在檔案總管中開啟資料夾]****。 然後，在剛剛開啟的 Windows **檔案總管**中，巡覽至 **bin/iPhone/Release** 子目錄。 除非您已[自訂 .ipa 檔案輸出位置](#customize-the-ipa-location)，否則它應該是位於此目錄中。
5. 若要改為在 Mac 組建主機上檢視 .ipa 檔案，請在 Visual Studio 2019 或 Visual Studio 2017 的 [方案總管]**** 中 (在 Windows 上)，以滑鼠右鍵按一下 Xamarin.iOS 專案名稱，然後選取 [在組建伺服器上顯示 IPA 檔案]****。 這會在 Mac 組建主機上開啟 [搜尋工具]**** 視窗並選取 .ipa 檔案。

    > [!TIP]
    >
    > 只有當您使用 Xcode 10 並建立 iOS 12 及更早版本時，下列步驟才有效。
    >
    > 若要使用適用于 iOS 13) 的 Xcode 11 (部署到 App Store，您應該 [使用 Visual Studio for Mac](?tabs=macos#build-and-submit-your-app) 來建立和上傳您的應用程式。 Xcode 11 將無法使用**應用程式載入**器。

6. 在 Mac 組建主機上開啟 [應用程載入器]****。 在 Xcode 中，選取 [Xcode] > [開啟開發人員工具] > [應用程式載入器]****。

    > [!NOTE]
    > 如需此工具的詳細資訊，請查看[有關應用程式載入器的 Apple 文件](https://help.apple.com/itc/apploader/#/apdS673accdb)。

7. 登入應用程式載入器 (請注意，您必須為您的 Apple ID [建立應用程式專用密碼](https://support.apple.com/ht204397))。
8. 選取 [Deliver Your App] (傳遞您的應用程式)****，並按一下 [選擇]**** 按鈕：

    ![選取 [Deliver Your App] \(傳遞您的應用程式\)](publishing-to-the-app-store-images/publishvs01.png "選取 [Deliver Your App] \(傳遞您的應用程式\)")

9. 選取上方建立的 .ipa 檔案，然後按一下 [確定]****。
10. 應用程式載入器將會驗證檔案：

    ![驗證畫面](publishing-to-the-app-store-images/publishvs02.png "驗證畫面")

11. 按一下 [下一步]**** 按鈕後，就會對 App Store 驗證應用程式：

    ![對 App Store 進行驗證](publishing-to-the-app-store-images/publishvs03.png "對 App Store 進行驗證")

12. 按一下 [傳送]**** 按鈕將應用程式傳送至 Apple 進行審查。
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

![等待審核](publishing-to-the-app-store-images/image21.png "等待審核")

## <a name="tips-and-tricks"></a>秘訣和訣竅

### <a name="customize-the-ipa-location"></a>自訂 .ipa 位置

**MSBuild** 屬性 `IpaPackageDir` 可讓您自訂 .ipa 檔案的輸出位置。 如果將 `IpaPackageDir` 設定為自訂位置，.ipa 檔案將會置於其中，而不是放在預設時間戳記子目錄。 建立須仰賴特定目錄路徑才能正常運作的自動組建 (例如用於持續整合 (CI) 組建) 時，這會很有用。

新屬性有數個可能的使用方式。 例如，若要將 .ipa 檔案輸出至舊的預設目錄 (如 Xamarin.iOS 9.6 和較舊版本)，您可以使用下列其中一種方法來將 `IpaPackageDir` 屬性設定為 `$(OutputPath)`。 兩種方法都與所有 Unified API Xamarin.iOS 組建相容，包括 IDE 組建以及使用 **msbuild** 或 **mdtool** 的命令列組建：

- 第一種選擇是在 **MSBuild** 檔案的 `<PropertyGroup>` 元素內設定 `IpaPackageDir` 屬性。 例如，您可以將下列 `<PropertyGroup>` 新增至 iOS 應用程式專案 .csproj 檔案的底部 (在結尾的 `</Project>` 標籤之前)：

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
       <MtouchArch>ARMv7, ARM64</MtouchArch>
       <MtouchHttpClientHandler>HttpClientHandler</MtouchHttpClientHandler>
       <MtouchTlsProvider>Default</MtouchTlsProvider>
       <BuildIpa>true</BuildIpa>
       <IpaPackageDir>$(OutputPath)</IpaPackageDir>
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

## <a name="summary"></a>摘要

本文描述如何設定組建，並提交 iOS 應用程式以在 App Store 上發行。

## <a name="related-links"></a>相關連結

- [Apple Developer 入口網站 (Apple)](https://developer.apple.com/account/)
- [iTunes Connect (Apple)](https://itunesconnect.apple.com)
- [App Store 審查指導方針 (Apple)](https://developer.apple.com/appstore/resources/approval/guidelines.html)
- [常見的應用程式拒絕案例 (Apple)](https://developer.apple.com/app-store/review/rejections/)
- [使用 Xamarin 中的功能](~/ios/deploy-test/provisioning/capabilities/index.md)
- [在 Xamarin 中使用權利](~/ios/deploy-test/provisioning/entitlements.md)
- [在 iTunes Connect 中設定應用程式](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [Xamarin.iOS 中的應用程式圖示](~/ios/app-fundamentals/images-icons/app-icons.md)
- [Xamarin.iOS 應用程式的啟動畫面](~/ios/app-fundamentals/images-icons/launch-screens.md)
