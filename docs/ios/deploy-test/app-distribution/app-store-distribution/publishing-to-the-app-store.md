---
title: 將 Xamarin.iOS 應用程式發佈到 App Store
description: 本文件描述如何設定、建置及發佈 Xamarin.iOS 應用程式，以在 App Store 中發佈。
ms.prod: xamarin
ms.assetid: DFBCC0BA-D233-4DC4-8545-AFBD3768C3B9
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/23/2017
ms.openlocfilehash: b8bea29e71e055621e7d0b85d3736ec6cc9ba3b4
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34785696"
---
# <a name="publishing-xamarinios-apps-to-the-app-store"></a>將 Xamarin.iOS 應用程式發佈到 App Store

為了讓所有 iOS 裝置皆可取得應用程式，Apple 要求應用程式必須透過 *App Store* 發佈，這使應用程式市集成為了 iOS 應用程式的一站式購物地點。 在具有 500,000 個以上應用程式的商店中，各類應用程式的開發人員皆隨著此單一散發點的大成功獲得龐大利潤。 App Store 為應用程式開發人員同時提供散發與支付系統，是一套周全解決方案。

將應用程式提交至 App Store 的程序包含：

1. 建立「應用程式識別碼」和選取「權利」。
2. 建立**散發佈建設定檔**。
3. 使用此設定檔來建置應用程式。
4. 透過 **iTunes Connect** 來提交應用程式。


本文涵蓋在 App Store 散發中佈建、建置及提交應用程式所需的一切步驟。

## <a name="before-you-submit-an-application"></a>在您提交應用程式前

在您提交應用程式以將其發佈至 App Store 後，Apple 會對該應用程式進行一系列審查以確保其符合 Apple 的品質與內容方針。 若您的應用程式未符合這些方針，Apple 將會予以退件，而您則必須解決 Apple 列出的不合規問題，然後重新提交。
因此，熟悉這些方針並嘗試以其作為基準改寫應用程式，將使您擁有通過 Apple 檢閱的最佳機會。 您可於此處取得 Apple 的方針：[App Store Review Guidelines](https://developer.apple.com/appstore/resources/approval/guidelines.html) (App Store 審查方針)。

提交應用程式時有幾個特別需要注意的事項：

1. 確認應用程式的描述與應用程式內容相符。
2. 測試應用程式不會在正常使用情況下損毀。 這包含您所支援之每部 iOS 裝置的使用方式。


Apple 也維護了一份應用程式市集提交提示的清單。 您可於此處閱讀這些提示：[Distributing on the App Store](https://developer.apple.com/appstore/resources/submission/tips.html) (在 App Store 上散發)。

## <a name="configuring-your-application-in-itunes-connect"></a>在 iTunes Connect 中設定應用程式

[iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa) 是一套 Web 架構工具，可讓您在 App Store 上管理 iOS 應用程式或進行其他作業。 您必須先在 iTunes Connect 中正確安裝並設定 Xamarin.iOS 應用程式，才能將其提交給 Apple 進行審查，最終達到在 App Store 作為付費或免費 App 發行的目標。

請執行下列動作：

1. 驗證 iTunes Connect 的**合約、稅金與銀行**區段有正確且最新的合約，以發行免費或付費的 iOS 應用程式。
2. 為應用程式建立新的 **iTunes Connect 記錄**，並指定其**顯示名稱** (即為 App Store 中所見名稱)。
3. 選取 [售價] 或指明該應用程式將免費發行。
5. 提供簡明的應用程式**描述**，內容涵蓋其功能與終端使用者的權益。
6. 提供**類別**、**子類別**及**關鍵字**來協助使用者在 App Store 中尋找您的應用程式。
7. 應 Apple 要求提供您網站的**連絡**與**支援** URL。
8. 設定應用程式的**分級**，App Store 中的家長監護將使用此分級。
9. 設定選用 App Store 技術，例如 **Game Center** 與**在應用程式內購買**。

如需更多詳細資料，請參閱我們的[在 iTunes Connect 中設定應用程式](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)文件。

## <a name="preparing-for-app-store-distribution"></a>準備 App Store 散發

若要在 App Store 中發佈應用程式，您必須先為散發建置應用程式，其中包含許多步驟。 下列各節涵蓋準備 Xamarin.iOS 應用程式發佈的一切事項，可讓您建置應用程式並提交至 App Store 以進行審查與發行。

### <a name="provisioning-for-application-services"></a>佈建應用程式服務

Apple 提供一組精選的特別應用程式服務 (又稱權利)，當您為 iOS 應用程式建立唯一識別碼時可予以啟用。 不論您是否使用自訂權利，都必須先為 Xamarin.iOS 應用程式建立唯一識別碼，才能將其發佈至 App Store。

建立應用程式識別碼並選擇性選取權利包含下列步驟，內容涵蓋使用 Apple 的 Web 架構 iOS 佈建入口網站：

1. 在**憑證、識別項與設定檔**區段中選取 [識別碼] > [應用程式識別碼]。
2. 按一下 [+] 按鈕，並為新應用程式提供**名稱**與**套件組合識別碼**。
3. 捲動至畫面底部並選取您 Xamarin.iOS 應用程式所需的所有**應用程式服務**。
4. 按一下 [繼續] 按鈕，並遵循畫面指示來建立新的 應用程式識別碼。

在定義應用程式識別碼時，除了選取及設定所需的應用程式服務之外，您也必須設定 Xamarin.iOS 專案中的應用程式識別碼與權利，方法是設定 `Info.plist` 與 `Entitlements.plist` 檔案。

請執行下列動作：

1. 在方案總管中按兩下 `Info.plist` 檔案將其開啟以進行編輯。
2. 在 **iOS 應用程式目標** 區段中，填入應用程式的名稱，並輸入您在定義應用程式識別碼時建立的**套件組合識別碼**。
3. 將變更儲存至 `Info.plist` 檔案。
4. 在方案總管中按兩下 `Entitlements.plist` 檔案將其開啟以進行編輯。
5. 選取並設定 Xamarin.iOS 應用程式需要的權利，使其符合您在定義應用程式識別碼時所執行的上述設定。
6. 將變更儲存至 `Entitlements.plist` 檔案。

如需詳細指示，請參閱我們的[佈建應用程式](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#appservices)文件。

### <a name="setting-the-store-icons"></a>設定市集圖示

App Store 圖示現在應以資產目錄傳遞。 若要新增 App Store 圖示，請先找到專案中 **Assets.xcassets** 檔案內的 **AppIcon** 影像集。

所需圖示在資產目錄中的名稱為 **App Store**，且大小應為 **1024 x 1024**。 Apple 表明資產目錄中的應用程式市集圖示不得透明或含有 Alpha 色板。

如需設定市集圖示的資訊，請參閱 [App Store 圖示](~/ios/app-fundamentals/images-icons/app-store-icon.md)指南。

### <a name="setting-the-apps-icons-and-launch-screens"></a>設定應用程式圖示與啟動畫面

若要使 Apple 接受 iOS 應用程式使其得以在 App Store 上架，您必須為可執行該應用程式的所有 iOS 裝置提供適當的圖示與啟動畫面。 應用程式圖示會透過 **Assets.xcassets** 檔案中的 **AppIcon** 影像集，新增至您專案的資產目錄。 啟動畫面則會透過分鏡腳本新增。

如需建立應用程式與啟動畫面的詳細指示，請參閱[應用程式圖示](~/ios/app-fundamentals/images-icons/app-icons.md)與[啟動畫面](~/ios/app-fundamentals/images-icons/launch-screens.md)指南。

### <a name="creating-and-installing-a-distribution-profile"></a>建立與安裝散發設定檔

iOS 會使用佈建設定檔來控制特定應用程式組建的部署方式。 這些檔案包含用於簽署應用程式之憑證、應用程式識別碼，以及應用程式可安裝位置的相關資訊。 若為開發與臨機操作散發，佈建設定檔也會包含您可部署應用程式的允許裝置清單。 然而，因為公開散發的唯一管道是 App Store，所以 App Store 散發只會包含憑證與應用程式識別碼資訊。

佈建包含下列步驟，過程會使用 Apple 的 Web 架構 iOS 佈建入口網站：

1.  選取 [佈建] > [散發] 。
2.  按一下 [+] 按鈕，並選取您想要建立為 **App Store** 的散發設定檔類型。
3.  從您要建立散發設定檔的下拉式清單中選取 [應用程式識別碼]。
4.  選取有效的生產 (散發) 憑證來簽署應用程式。
5.  輸入新**散發設定檔**的**名稱**並產生設定檔。
6.  在 Mac 上，開啟至 [Xcode] 並瀏覽至 [Preferences] \(喜好設定\) > [select your Apple ID] \(選取您的 Apple ID\)> [View Details...] \(檢視詳細資料...\)。在 Mac 上，於 Xcode 中下載所有可用的設定檔。
7.  回到整合式開發環境 (IDE)，並在 [iOS 套件組合簽署]  選項下為正確的 [組建組態] (可為 **App Store** 或**發行**) 選取散發佈建設定檔。

如需詳細指示，請參閱[建立散發設定檔](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioningprofile)與[在 Xamarin.iOS 專案中選取散發設定檔](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#selectprofile)。

## <a name="setting-the-build-configuration-for-your-application"></a>為應用程式設定組建組態

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

請執行下列動作：

1. 以滑鼠右鍵按一下 Solution Pad 中的 [專案名稱]，並選取 [選項] 將其開啟進行編輯。
2. 從 [組態] 下拉式清單中選取 [iOS 組建] 和 [發行 | iPhone]：

    ![](publishing-to-the-app-store-images/configurevs01.png "從 [組態] 下拉式清單中選取 [AppStore]")

3. 若您有設為目標的特定 iOS 版本，可從 [SDK 版本] 下拉式清單中加以選取，或是將此值保留在預設設定。
4. 建立連結會將未使用的方法、屬性、類別等項目移除，進而減少應用程式可散發項目的整體大小。在大部分情況下應將其保持在**僅連結 SDK 組件**預設值。 而在使用部分特定協力廠商程式庫的情況下，您可能必須將此值設為**不要連結**，以防必要項目受到移除。 如需詳細資訊，請參閱 [iOS 建置機制](~/ios/deploy-test/ios-build-mechanics.md)指南。
5. 建議您選取 [Optimize PNG image files for iOS] (為 iOS 將 PNG 影像檔最佳化) 核取方塊，這將能進一步減少您應用程式可傳遞項目的大小。
6. 因為偵錯會使組建產生不必要的大小，所以建議「不要」啟用。
8. 針對 iOS 11，您將需要選取支援 **ARM64** 的其中一個裝置架構。 如需為 64 位元 iOS 裝置進行建置的詳細資訊，請參閱 [32/64 位元平台考量](~/cross-platform/macios/32-and-64/index.md)文件中的**啟用 Xamarin.iOS 應用程式的 64 位元組建**一節。
9. 您可以考慮選擇使用 **LLVM** 編譯器，其所建立的程式碼更加輕巧高效，但需要較長的編譯時間。
10. 根據您的應用程式需求，也可以考慮調整要使用的**記憶體回收**類型與**國際化**設定。
11. 儲存您對組建組態進行的變更。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

根據預設，當您在 Visual Studio 建立新的 Xamarin.iOS 應用程式時，會自動為**臨機操作** 與 **App Store** 部署建立組件組態。 在對將要提交至 Apple 的應用程式進行最終建置前，您必須對基底組態進行幾項修改。

請執行下列動作：

1. 以滑鼠右鍵按一下方案總管中的 [專案名稱]，並選取 [屬性] 將其開啟進行編輯。
2. 從 [組態] 下拉式清單選取 [iOS 組建] 與 [AppStore] \(或如果 AppStore 無法使用，請選取 [發行]\)：

    ![](publishing-to-the-app-store-images/configurevs01.png "從 [組態] 下拉式清單中選取 [AppStore]")

3. 若您有設為目標的特定 iOS 版本，可從 [SDK 版本] 下拉式清單中加以選取，或是將此值保留在預設設定。
4. 建立連結會將未使用的方法、屬性、類別等項目移除，進而減少應用程式可散發項目的整體大小。在大部分情況下應將其保持在**僅連結 SDK 組件**預設值。 而在使用部分特定協力廠商程式庫的情況下，您可能必須將此值設為**不要連結**，以防必要項目受到移除。 如需詳細資訊，請參閱 [iOS 建置機制](~/ios/deploy-test/ios-build-mechanics.md)指南。
5. 建議您選取 [Optimize PNG image files for iOS] (為 iOS 將 PNG 影像檔最佳化) 核取方塊，這將能進一步減少您應用程式可傳遞項目的大小。
6. 因為偵錯會使組建產生不必要的大小，所以建議「不要」啟用。
7. 按一下 [進階] 索引標籤：

    ![](publishing-to-the-app-store-images/configurevs02.png "[進階] 索引標籤")

8. 若您的 Xamarin.iOS 應用程式目標為 iOS 8 及 64 位元 iOS 裝置，就必須選取其中一個支援 **ARM64** 的裝置架構。 如需為 64 位元 iOS 裝置進行建置的詳細資訊，請參閱 [32/64 位元平台考量](~/cross-platform/macios/32-and-64/index.md)文件中的**啟用 Xamarin.iOS 應用程式的 64 位元組建**一節。
9. 您可以考慮選擇使用 **LLVM** 編譯器，其所建立的程式碼更加輕巧高效，但需要較長的編譯時間。
10. 根據您的應用程式需求，也可以考慮調整要使用的**記憶體回收**類型與**國際化**設定。
11. 儲存您對組建組態進行的變更。

-----

## <a name="building-and-submitting-the-distributable"></a>建置與提交可散發項目

在 Xamarin.iOS 應用程式經過正確設定後，您現已準備好進行最終散發建置，將應用程式提交給 Apple 進行審查與發行。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

### <a name="build-your-archive"></a>建置封存

1. 在 Visual Studio for Mac 中選取 [發行] | [裝置] 組態：

    ![](publishing-to-the-app-store-images/buildxs01new.png "組態")
1. 從 [建置] 功能表選取 [Archive for Publishing] (封存以供發行)：

    ![](publishing-to-the-app-store-images/buildxs02new.png "選取 [Archive for Publishing] (封存以供發行)")

1. 建立封存後，[封存] 檢視會隨即顯示：

    ![](publishing-to-the-app-store-images/buildxs03new.png "[封存] 檢視會隨即顯示")


> [!NOTE]
> 雖然舊的 _App Store_ 與_臨機操作_設定現已從所有 Visual Studio for Mac 範本專案中移除，但是在較舊的專案中仍然存在這些設定。 發生這種情形時，您可繼續使用上述清單步驟 1 內的 **App Store | 裝置**組態。

### <a name="sign-and-distribute-your-app"></a>簽署並散發應用程式

 每次建置應用程式進行封存，都會自動開啟 [封存] 檢視，顯示依解決方案分組的所有已封存專案。 根據預設，此檢視只會顯示目前開啟的解決方案。 若要查看所有具有封存的解決方案，請按一下 [Show all archives] (顯示所有封存) 選項。

 建議您保留部署至客戶的封存 (App Store 或 Enterprise 部署)，以便日後代表所產生的任何偵錯資訊。

 若要簽署應用程式並準備散發：


1. 選取 [Sign and Distribute...] (簽署並散發...)，如下所示：

    ![](publishing-to-the-app-store-images/buildxs04new.png "選取 [Sign and Distribute...] (簽署並散發...)")

1. 這會開啟發佈精靈。 選取 **App Store** 散發管道以建立套件，然後開啟應用程式載入器：

    ![](publishing-to-the-app-store-images/distribute01.png "開啟應用程式載入器")

1. 在 [佈建設定檔] 畫面上，選取您的簽署識別與對應的佈建設定檔，或重新簽署另一個身分識別：

    ![](publishing-to-the-app-store-images/distribute02.png "選取簽署識別與對應的佈建設定檔")

1. 驗證套件的詳細資料，然後按一下 [發佈]  以儲存您的 `.ipa` 套件：

    ![](publishing-to-the-app-store-images/distribute03.png "驗證套件詳細資料")

1. 儲存 `.ipa` 後，即可準備透過應用程式載入器將應用程式上傳至 iTunes Connect：

    ![](publishing-to-the-app-store-images/distribute04.png "[Publication Succeeded] (發佈成功) 畫面")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

適用於 Visual Studio 的 Xamarin 外掛程式目前不支援封存將 iOS 應用程式發佈至 App Store 的工作流程。 因此，您必須上傳透過**建置臨機操作 IPA** 命令建立的 IPA，如下所述。


## <a name="build-an-ipa"></a>建置 IPA

 本節描述如何建置 IPA，此工作流程類似於使用臨機操作或 Enterprise 散發。 不過，IPA 將以上面建立的 App Store 佈建設定檔來簽署。

 請執行下列動作：

1. 在方案總管中，於 Xamarin.iOS 專案名稱上按一下滑鼠右鍵，然後選取 [屬性] 將其開啟進行編輯：

    ![](publishing-to-the-app-store-images/imagevs01.png "選取 [屬性] ")
1. 選取 [iOS 套件組合簽署] ，並將佈建設定檔變更為 App Store 佈建設定檔：

    ![](publishing-to-the-app-store-images/ipa01.png "選取 [iOS 套件組合簽署]，並將佈建設定檔變更為 App Store 佈建設定檔")
1. 選取 [iOS IPA Options] (iOS IPA 選項) 並從 [組態] 下拉式清單選取 [臨機操作]，若 [臨機操作] 不存在則請改選 [發行]：

    ![](publishing-to-the-app-store-images/imagevs02.png "從 [組態] 下拉式清單選取 [臨機操作]")

1. 您可以選擇指定 IPA 的**套件名稱**如未指定，則會使用與 Xamarin.iOS 專案相同的名稱。
1. 儲存您對專案屬性進行的變更。
1. 從 Visual Studio for Mac 的 [組建組態] 下拉式清單選取 [臨機操作]：

    ![](publishing-to-the-app-store-images/imagevs05.png "從 [組建組態] 下拉式清單選取 [臨機操作]")
1. 建置專案來建立 IPA 套件。
1. IPA 將會建置到 `Bin` > [iOS 裝置] > `Ad Hoc` 資料夾：

    ![](publishing-to-the-app-store-images/imagevs06.png "檔案總管中顯示的 IPA")

-----


## <a name="customizing-the-ipa-location"></a>自訂 IPA 位置

已新增的 **MSBuild** 屬性 `IpaPackageDir` 可讓您輕鬆自訂 `.ipa` 檔案輸出位置。 如果將 `IpaPackageDir` 設定為自訂位置，`.ipa` 檔案將會置於其中，而不是放在預設時間戳記子目錄中。 建立須仰賴特定目錄路徑才能正常運作的自動組建 (例如用於持續整合 (CI) 組建) 時，這會很有用。

新屬性有數個可能的使用方式：

例如，若要將 `.ipa` 檔案輸出至舊的預設目錄 (如 Xamarin.iOS 9.6 和較舊版本)，您可以使用下列其中一種方法來將 `IpaPackageDir` 屬性設定為 `$(OutputPath)`。 兩種方法都與所有 Unified API Xamarin.iOS 組建相容，包括 IDE 組建以及使用 `xbuild`、`msbuild` 或 `mdtool` 的命令列組建：

  - 第一種選擇是在 **MSBuild** 檔案的 `<PropertyGroup>` 項目內設定 `IpaPackageDir` 屬性。 例如，您可以將下列 `<PropertyGroup>` 新增至 iOS 應用程式專案 `.csproj` 檔案的底部 (在結尾的 `</Project>` 標籤之前)：

      ```xml
        <PropertyGroup>
            <IpaPackageDir>$(OutputPath)</IpaPackageDir>
        </PropertyGroup>
      ```
  - 較好的方法是將 `<IpaPackageDir>` 項目新增至現有 `<PropertyGroup>` 的底部，其對應用於建置 `.ipa` 檔案的組態。 這種做法較好，因為會在 iOS IPA 選項專案屬性頁面上以規劃的設定，準備具有未來相容性的專案。 如果您目前使用 `Release|iPhone` 組態來建置 `.ipa` 檔案，更新後的完整屬性群組看起來可能會類似下列內容：

      ```xml
      <PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Release|iPhone' )
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
您可以利用 `msbuild` 或 `xbuild` 命令列組建這項替代技術來新增 `/p:` 命令列引數，以設定 `IpaPackageDir` 屬性。 若要這麼做，請注意 `msbuild` 不會展開傳入命令列上的 `$()` 運算式，因此無法使用 `$(OutputPath)` 語法。 您必須改為提供完整路徑名稱。 Mono 的 `xbuild` 命令不會展開 `$()` 運算式，但仍建議使用完整路徑名稱，因為未來版本最後會改用 [`msbuild` 的跨平台版本](http://www.mono-project.com/docs/about-mono/releases/4.4.0/#msbuild-preview-for-os-x)來取代 `xbuild`。 使用這種方法的完整範例在 Windows 上看起來可能會類似下列內容：

```bash
msbuild /p:Configuration="Release" /p:Platform="iPhone" /p:ServerAddress="192.168.1.3" /p:ServerUser="macuser" /p:IpaPackageDir="%USERPROFILE%\Builds" /t:Build SingleViewIphone1.sln
```

或在 Mac 上可能如下：

```bash
xbuild /p:Configuration="Release" /p:Platform="iPhone" /p:IpaPackageDir="$HOME/Builds" /t:Build SingleViewIphone1.sln
```

建立與封存散發組建後，即可準備將您的應用程式提交至 iTunes Connect。

### <a name="automatically-copy-app-bundles-back-to-windows"></a>自動將 .app 套件組合複製回 Windows

[!include[](~/ios/includes/copy-app-bundle-to-windows.md)]

## <a name="submitting-your-app-to-apple"></a>向 Apple 提交應用程式

> [!NOTE]
> Apple 已於近日變更其 iOS 應用程式的驗證程序，因此 IPA 內包含 `iTunesMetadata.plist` 的應用程式可能會遭到退回。 如果您遇到錯誤 `ERROR: ERROR ITMS-90047: "Disallowed paths ( "iTunesMetadata.plist" ) found at: Payload/iPhoneApp1.app"`，[此處](https://forums.xamarin.com/discussion/40388/disallowed-paths-itunesmetadata-plist-found-at-when-submitting-to-app-store/p1)描述的因應措施應可解決問題。

散發組建完成後，您即可準備將 iOS 應用程式提交至 Apple 以供審查，並在 App Store 上發行。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

 請執行下列動作：

1. 啟動 **Xcode**。
2. 從 [視窗] 功能表選取 [組合管理]。
3. 按一下 [封存] 索引標籤並選取上方建置的封存：

    ![](publishing-to-the-app-store-images/publishxs01.png "選取要提交的封存")
4. 按一下 [驗證...] 按鈕。
5. 選取要驗證的帳戶，並按一下 [選擇] 按鈕：

    ![](publishing-to-the-app-store-images/publishxs02.png "選取要驗證的帳戶")
6. 按一下 [驗證] 按鈕：

    ![](publishing-to-the-app-store-images/publishxs03.png "檔案摘要對話方塊")
7. 若套件組合發生任何問題，都會為您顯示。
8. 在 Visual Studio for Mac 中修正任何問題，並重建封存。
9. 按一下 [提交...] 按鈕。
10. 選取要驗證的帳戶，並按一下 [選擇] 按鈕：

    ![](publishing-to-the-app-store-images/publishxs04.png "選取要驗證的帳戶")
11. 按一下 [提交] 按鈕：

    ![](publishing-to-the-app-store-images/publishxs05.png "檔案摘要對話方塊")
12. 當 Xcode 完成將檔案上傳至 iTunes Connect 後，將會通知您。


當您儲存 _.ipa_ 後，Visual Studio for Mac 中的封存工作流程將會自動開啟應用程式載入器：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

您必須透過使用應用程式載入器應用程式將您的應用程式提交給 Apple 檢閱。 而這些步驟都必須在 Mac 組建主機上完成。 您可於[此處](https://itunesconnect.apple.com/apploader/ApplicationLoader_3.0.dmg)下載應用程式載入器的複本。

-----

1. 選取 [Deliver Your App] (傳遞您的應用程式)，並按一下 [選擇] 按鈕：

    [![](publishing-to-the-app-store-images/publishvs01.png "選取 [Deliver Your App] \(傳遞您的應用程式\)")](publishing-to-the-app-store-images/publishvs01.png#lightbox)

2. 在上方選取您建立的 ZIP 或 IPA 檔案，並按一下 [確定] 按鈕。

3. 應用程式載入器將會驗證檔案：

    [![](publishing-to-the-app-store-images/publishvs02.png "驗證畫面")](publishing-to-the-app-store-images/publishvs02.png#lightbox)
4. 按一下 [下一步] 按鈕後，就會對 App Store 驗證應用程式：

    [![](publishing-to-the-app-store-images/publishvs03.png "對 App Store 進行驗證")](publishing-to-the-app-store-images/publishvs03.png#lightbox)
5. 按一下 [傳送] 按鈕將應用程式傳送至 Apple 進行審查。
6. 當檔案已成功上傳時，應用程式載入器會通知您。

## <a name="itunes-connect-status"></a>iTunes Connect 狀態

如果您登再次登入 iTunes Connect，並從可用的應用程式清單選取您的應用程式，iTunes Connect 中的狀態現在應會顯示其**正在等候檢閱** (其可能在處理時，暫時顯示**已收到上傳**)：

[![](publishing-to-the-app-store-images/image21.png "iTunes Connect 中的狀態現在應會顯示其")正在等候檢閱](publishing-to-the-app-store-images/image21.png#lightbox)

## <a name="summary"></a>總結

本文說明為 App Store 發行設定、建置和提交應用程式的逐步引導。 首先說明建立並安裝散發佈建設定檔所需的步驟。 接下來則逐步解說如何使用 Visual Studio 和 Visual Studio for Mac 建立散發組建。 最後示範如何使用 iTunes Connect 和工具將應用程式提交至 App Store。


## <a name="related-links"></a>相關連結

- [使用影像](~/ios/app-fundamentals/images-icons/index.md)
- [iOS 應用程式開發工作流程指南：散發應用程式](http://developer.apple.com/library/ios/#documentation/Xcode/Conceptual/ios_development_workflow/35-Distributing_Applications/distributing_applications.html)
- [App Store 提交提示](https://developer.apple.com/appstore/resources/submission/tips.html)
- [常見的應用程式拒絕案例](https://developer.apple.com/app-store/review/rejections/)
- [App Store 審查指導方針](https://developer.apple.com/appstore/resources/approval/guidelines.html)
