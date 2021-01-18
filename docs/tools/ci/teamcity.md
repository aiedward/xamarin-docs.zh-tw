---
title: 搭配 Xamarin 使用 TeamCity
description: 本指南將討論使用 TeamCity 來編譯行動應用程式，然後將其提交至 App Center 測試所需的步驟。
ms.prod: xamarin
ms.assetid: AC2626CB-28A7-4808-B2A9-789D67899546
author: davidortinau
ms.author: daortin
ms.date: 04/01/2020
ms.openlocfilehash: 553f40a407fa8003a6545214a545f00b01fb0ed6
ms.sourcegitcommit: b75c369adb8e02a429b6c0fed8ba4a855099bf01
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/18/2021
ms.locfileid: "98555031"
---
# <a name="using-teamcity-with-xamarin"></a>搭配 Xamarin 使用 TeamCity

_本指南將討論使用 TeamCity 來編譯行動應用程式，然後將其提交至 App Center 測試所需的步驟。_

如《 [持續整合簡介》簡介](~/tools/ci/intro-to-ci.md) 中所述，在開發品質的行動應用程式時，持續整合 (CI) 是很實用的作法。 持續整合伺服器軟體有許多可行的選項;本指南將著重于從 JetBrains [TeamCity](https://www.jetbrains.com/teamcity/) 。

TeamCity 安裝有數種不同的排列。 下列清單說明其中一些排列：

- **Windows 服務** –在此案例中，TeamCity 會在 Windows 以 windows 服務的形式啟動時啟動。 它必須與 Mac 組建主機配對，以編譯任何 iOS 應用程式。

- **在 OS X 上啟動** 背景程式-概念上，這類似于以上一個步驟中所述的 Windows 服務執行。 根據預設，組建會在根帳號下執行。

- **OS X 上的使用者帳戶** -可以在每次使用者登入時啟動的使用者帳戶下執行 TeamCity。

在先前的案例中，在 OS X 上的使用者帳戶下執行 TeamCity 是最簡單且最容易設定的。

設定 TeamCity 有幾個相關步驟：

- **安裝 TeamCity** –本指南未討論 TeamCity 的安裝。 本指南假設 TeamCity 是在使用者帳戶下安裝並執行。 如需 [安裝 TeamCity](https://confluence.jetbrains.com/display/TCD8/Installation) 的指示，請參閱 [TeamCity 8 檔](https://confluence.jetbrains.com/display/TCD8/TeamCity+Documentation) 中的 JetBrains。

- **準備組建伺服器** –這個步驟牽涉到安裝建立行動應用程式所需的必要軟體、工具和憑證，並準備發佈。

- 建立 **組建腳本**–此步驟並非絕對必要，但組建腳本是可自動建立應用程式的實用輔助工具。 使用組建腳本有助於針對可能發生的組建問題進行疑難排解，並提供一致且可重複的方式來建立要散發的二進位檔，即使未練習持續整合也是一樣。

- **建立 TeamCity 專案** –完成前三個步驟之後，我們必須建立 TeamCity 專案，以包含取出原始程式碼、編譯專案，並將測試提交至 App Center 測試所需的所有中繼資料。

## <a name="requirements"></a>需求

需要使用 [App Center 測試](/appcenter/test-cloud/) 體驗。

需要熟悉 TeamCity 8.1。 TeamCity 的安裝已超出本檔的範圍。 假設 TeamCity 是安裝在 OS X Mavericks 上，並且是在一般使用者帳戶下執行，而不是在根帳號下執行。

組建伺服器應該是獨立的電腦，執行作業系統 X，專門用於持續整合。 在理想情況下，組建伺服器不會負責任何其他角色，例如資料庫伺服器、網頁伺服器或開發人員工作站。

> [!IMPORTANT]
> 本指南未涵蓋 Xamarin 的「無周邊」安裝。

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="preparing-the-build-server"></a>準備組建伺服器

設定組建伺服器的一個重要步驟是安裝所有必要的工具、軟體和憑證來建立行動應用程式。 組建伺服器可以編譯行動解決方案並執行任何測試，這是很重要的。 為了將設定問題降至最低，軟體和工具應該安裝在裝載 TeamCity 的相同使用者帳戶中。 下列清單詳細說明所需的內容：

1. **Visual Studio for Mac** –這包含了 Xamarin 和 Xamarin. Android。
2. **登入 Xamarin 元件存放區** -此步驟是選擇性的，且只有在您的應用程式使用 Xamarin 元件存放區中的元件時才需要。 此時主動登入元件存放區，會在 TeamCity 組建嘗試編譯應用程式時避免任何問題。
3. **Xcode** –編譯和簽署 iOS 應用程式需要 Xcode。
4. **Xcode Command-Line 工具** -這會在「 [更新 Ruby with rbenv ruby-build](https://github.com/calabash/calabash-ios/wiki) 指南」的「安裝」一節的步驟1中加以說明。
5. **簽署身分識別 & 布建設定檔** –透過 XCode 匯入憑證和布建設定檔。 如需詳細資訊，請參閱匯出簽署身分識別和布建 [設定檔](https://developer.apple.com/library/ios/recipes/xcode_help-accounts_preferences/articles/export_signing_assets.html) 的 Apple 指南。
6. **Android 金鑰庫** –將必要的 Android 金鑰庫複製到 TeamCity 使用者可存取的目錄，例如 `~/Documents/keystores/MyAndroidApp1` 。
7. **Calabash** –如果您的應用程式具有使用 Calabash 撰寫的測試，這是選擇性步驟。 如需詳細資訊，請參閱《 [在 OS X 上安裝 Calabash X Mavericks](https://github.com/calabash/calabash-ios/wiki) 指南》和 [更新 Ruby with rbenv ruby-build](https://github.com/calabash/calabash-ios/wiki) 指南。

下圖說明所有這些元件：

![下圖說明所有這些元件](teamcity-images/image1.png)

所有軟體安裝完成後，請登入使用者帳戶，並確認所有軟體都已正確安裝且正常運作。 這應該包括編譯解決方案，以及將應用程式提交至 App Center 測試。 您可以執行組建腳本來簡化這項工作，如下一節所述。

## <a name="create-a-build-script"></a>建立組建腳本

雖然 TeamCity 可以處理將行動應用程式編譯並提交至 App Center 測試的所有層面;建議您建立組建腳本。 組建腳本提供下列優點：

1. **檔** -組建腳本是如何建立軟體的檔形式。 這會移除一些與部署應用程式相關聯的「魔術」，並可讓開發人員專注在功能上。
1. **重複性** –組建腳本可確保每次編譯和部署應用程式時，都會以相同的方式進行，不論工作是誰或什麼。 這種可重複的一致性會移除因為組建或人為錯誤而可能出現的任何問題或錯誤。
1. **版本控制** -組建腳本可以包含在原始檔控制系統中。 這表示如果發現錯誤或不正確，就可以追蹤、監視和修正組建腳本的變更。
1. **準備環境** –組建腳本可包含邏輯來安裝任何必要的協力廠商相依性。 這可確保應用程式是以適當的元件建立。

組建腳本可以像 Windows) 上的 PowerShell 檔案 (一樣簡單，或是在 OS X) 上 (bash 腳本。 建立組建腳本時，有數種指令碼語言選擇：

- [**Rake**](https://github.com/jimweirich/rake) –這是以 Ruby 為基礎的 Domain-Specific 語言 (DSL) 以建立專案。 Rake 具有熱門的優點，以及更豐富的程式庫生態系統。

- [**psake**](https://github.com/psake/psake) –這是建立軟體的 Windows PowerShell 程式庫

- [**假**](https://fsharp.github.io/FAKE/) –這是以 F # 為基礎的 DSL，讓您可以視需要使用現有的 .net 程式庫。

使用哪一種指令碼語言取決於您的喜好設定和需求。

> [!NOTE]
> 您可以使用以 XML 為基礎的組建系統（例如 MSBuild 或 NAnt），但這些都缺少專門用來建立軟體之 DSL 的表達和可維護性。

### <a name="parameterizing-the-build-script"></a>參數化組建腳本

建立和測試軟體的程式需要資訊，應保持秘密。 建立 APK 可能需要金鑰儲存區的密碼和/或金鑰儲存區中的金鑰別名。 同樣地，App Center 測試需要開發人員唯一的 [API 金鑰](/appcenter/api-docs/) 。 這些類型的值不應該在組建腳本中硬式編碼。 相反地，它們應該會以變數的形式傳遞至組建腳本。

較不敏感的是值，例如 iOS 裝置識別碼或 Android 裝置識別碼，以識別 App Center 應用於測試回合的裝置。 這些都不是需要保護的值，但可能會從組建變更為組建。

將這些類型的變數儲存在組建腳本之外，也可讓您更輕鬆地在組織內共用組建腳本，例如開發人員。 開發人員可以使用與組建伺服器完全相同的腳本，但可以使用自己的金鑰庫和 [API 金鑰](/appcenter/api-docs/)。

有兩個可能的選項可用來儲存這些敏感性值：

- **設定檔** -為了保護 [API 金鑰](/appcenter/api-docs/) ，此值不應簽入版本控制。 您可以為每部電腦建立檔案。 從此檔案讀取值的方式取決於所使用的指令碼語言。

- **環境變數** –這些變數可以輕鬆地以個別電腦為基礎來設定，而且與基礎指令碼語言無關。

每個選擇都有其優點和缺點。 TeamCity 適用于環境變數，因此在建立組建腳本時，本指南會建議這項技術。

### <a name="build-steps"></a>組建步驟

組建腳本必須執行下列步驟：

- **編譯應用程式** –這包括使用正確的布建設定檔簽署應用程式。

- 將 **應用程式提交給 Xamarin Test Cloud** –這包括將 APK 與適當金鑰儲存區對齊的簽章和 zip。

以下將詳細說明這兩個步驟。

#### <a name="compiling-a-xamarinios-application"></a>編譯 Xamarin iOS 應用程式

[!include[](~/tools/ci/includes/commandline-compile-of-xamarin-ios-ipa.md)]

#### <a name="compiling-a-xamarinandroid-application"></a>編譯 Xamarin. Android 應用程式

若要編譯 Android 應用程式，請在 Windows) 上使用 **xbuild** (或 **msbuild** ：

```bash
/Library/Frameworks/Mono.framework/Commands/xbuild /t:SignAndroidPackage /p:Configuration=Release /path/to/android.csproj
```
編譯 Android 應用程式 **xbuild** 會使用專案，而 iOS 應用程式 **xbuild** 會使用該方案。

#### <a name="submitting-xamarinuitests-to-app-center"></a>將 Uitest 提交至 App Center

您可以使用 [App CENTER CLI](https://github.com/microsoft/appcenter-cli)提交 uitest，如下列程式碼片段所示：

```bash
appcenter test run uitest --app <TEAM-NAME/APP-NAME> --devices <DEVICE_SET> --token <API_KEY> --app-path <appname.APK-or-appname.IPA> --merge-nunit-xml report.xml --build-dir pathToUITestBuildDir
```

當測試執行時，將會以稱為 **report.xml** 的 NUNIT 樣式 XML 檔案格式傳回測試結果。 TeamCity 會在組建記錄檔中顯示資訊。

如需如何將 Uitest 提交至 App Center 的詳細資訊，請參閱 [準備 xamarin. Android 應用程式](/appcenter/test-cloud/uitest/preparing-for-upload-android) 或 [準備 Xamarin iOS 應用程式](/appcenter/test-cloud/uitest/preparing-for-upload-ios)。

#### <a name="submitting-calabash-tests-to-app-center"></a>將 Calabash 測試提交至 App Center

您可以使用 [App CENTER CLI](https://github.com/microsoft/appcenter-cli)提交 Calabash 測試，如下列程式碼片段所示：

```bash
appcenter test run calabash --app <TEAM-NAME/APP-NAME> --devices <DEVICE_SET> --token <API_KEY> --app-path <appname.APK-or-appname.IPA> --project-dir pathToProjectDir
```

若要將 Android 應用程式提交至 App Center 測試，必須先使用 calabash-Android 重建 APK 測試伺服器：

```bash
$ calabash-android build </path/to/signed/APK>
$ appcenter test run calabash --app <TEAM-NAME/APP-NAME> --devices <DEVICE_SET> --token <API_KEY> --app-path <appname.APK> --project-dir pathToProjectDir
```

如需提交 Calabash 測試的詳細資訊，請參閱 [將 Calabash 測試提交至測試雲端](https://github.com/calabash/calabash-ios/wiki)的 Xamarin 指南。

## <a name="creating-a-teamcity-project"></a>建立 TeamCity 專案

安裝 TeamCity 並 Visual Studio for Mac 可以建立您的專案之後，就可以在 TeamCity 中建立專案，以建立專案並將其提交至 App Center。

1. 從透過網頁瀏覽器登入 TeamCity 開始。 流覽至根專案：

    ![流覽至根專案](teamcity-images/image2.png "流覽至根專案") 在根專案底下，建立新的子專案：

    ![流覽至根專案底下的根專案，建立新的子專案](teamcity-images/image3.png "流覽至根專案底下的根專案，建立新的子專案")
2. 建立子專案之後，請新增新的組建設定：

    ![建立子專案之後，請新增新的組建設定](teamcity-images/image5.png "建立子專案之後，請新增新的組建設定")
3. 將 VC 專案附加至組建設定。 這是透過 [版本控制] 設定畫面來完成的：

    ![這是透過 [版本控制] 設定畫面來完成](teamcity-images/image6.png "這是透過 [版本控制] 設定畫面來完成")

    如果未建立任何 VC 專案，您可以從新的 VC 根頁面建立一個專案，如下所示：

    ![如果未建立任何 VC 專案，您可以從新的 VC 根目錄頁面建立一個專案。](teamcity-images/image7.png "如果未建立任何 VC 專案，您可以選擇從新的 VC 根目錄頁面建立一個專案。")

    附加了 VC 根目錄之後，TeamCity 將會簽出項目，並嘗試自動偵測組建步驟。 如果您很熟悉 TeamCity，則可以選取其中一個偵測到的組建步驟。 現在，您可以放心忽略偵測到的組建步驟。

4. 接下來，設定組建觸發程式。 這會在符合特定條件時將組建排入佇列，例如，當使用者將程式碼認可至存放庫時。 下列螢幕擷取畫面顯示如何新增組建觸發程式：

    ![此螢幕擷取畫面顯示如何新增組建觸發](teamcity-images/image8.png "此螢幕擷取畫面顯示如何新增組建觸發程式") 程式您可以在下列螢幕擷取畫面中看到設定組建觸發程式的範例：

    ![您可以在此螢幕擷取畫面中看到設定組建觸發程式的範例](teamcity-images/image9.png "您可以在此螢幕擷取畫面中看到設定組建觸發程式的範例")

5. 上一節的參數化組建腳本，建議將某些值儲存為環境變數。 您可以透過 [參數] 畫面，將這些變數新增至組建設定。 新增 App Center [API 金鑰](/appcenter/api-docs/)、IOS 裝置識別碼和 ANDROID 裝置識別碼的變數，如下列螢幕擷取畫面所示：

    ![新增 App Center 測試 API 金鑰、iOS 裝置識別碼和 Android 裝置識別碼的變數](teamcity-images/image11.png "為 Test Cloud API 金鑰、iOS 裝置識別碼和 Android 裝置識別碼新增變數")

6. 最後一個步驟是新增組建步驟，以叫用組建腳本來編譯應用程式，並將應用程式加入至 App Center 測試。 下列螢幕擷取畫面是使用 Rakefile 來建立應用程式的組建步驟範例：

    ![這個螢幕擷取畫面是使用 Rakefile 來建立應用程式的組建步驟範例](teamcity-images/image12.png "這個螢幕擷取畫面是使用 Rakefile 來建立應用程式的組建步驟範例")

7. 此時，組建設定已完成。 建議您觸發組建，以確認已正確設定專案。 若要這樣做，最好的方式就是對儲存機制認可一個小、不重要的變更。 TeamCity 應該會偵測認可並啟動組建。

8. 組建完成後，請檢查組建記錄檔，並查看是否有任何需要注意的組建發生問題或警告。

## <a name="summary"></a>總結

本指南涵蓋了如何使用 TeamCity 來建立 Xamarin 行動應用程式，然後將其提交至 App Center 測試。 我們討論了如何建立組建腳本，以將組建程式自動化。 組建腳本會負責編譯應用程式、提交至 App Center 測試，以及等待結果。

然後我們已討論如何在 TeamCity 中建立專案，以在每次開發人員認可程式碼並呼叫組建腳本時，將組建排入佇列。

## <a name="related-links"></a>相關連結

- [準備 Xamarin. Android 應用程式](/appcenter/test-cloud/uitest/preparing-for-upload-android)
- [準備 Xamarin iOS 應用程式](/appcenter/test-cloud/uitest/preparing-for-upload-ios)
- [安裝和設定 TeamCity](https://confluence.jetbrains.com/display/TCD8/Installing+and+Configuring+the+TeamCity+Server)
