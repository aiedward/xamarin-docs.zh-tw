---
title: 搭配 Xamarin 使用小組城市
description: 本指南將討論使用 TeamCity 來編譯行動應用程式，然後將其提交至 Xamarin Test Cloud 的相關步驟。
ms.prod: xamarin
ms.assetid: AC2626CB-28A7-4808-B2A9-789D67899546
author: conceptdev
ms.author: crdun
ms.date: 03/23/2017
ms.openlocfilehash: ee1ef1ecda18ee9817fcf10b7dda0c7b4489bf9f
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "72273123"
---
# <a name="using-team-city-with-xamarin"></a>搭配 Xamarin 使用小組城市

_本指南將討論使用 TeamCity 來編譯行動應用程式，然後將其提交至 Xamarin Test Cloud 的相關步驟。_

如[持續整合簡介](~/tools/ci/intro-to-ci.md)指南中所述，持續整合（CI）是開發高品質行動應用程式時的實用做法。 持續整合伺服器軟體有許多可行的選項;本指南將著重于從 JetBrains [TeamCity](http://www.jetbrains.com/teamcity/) 。

TeamCity 安裝有數個不同的排列。 以下是其中一部分的清單：

- **Windows 服務**–在此案例中，TeamCity 會在 windows 開機為 windows 服務時啟動。 它必須與 Mac 組建主機配對，才能編譯任何 iOS 應用程式。

- **在 OS X 上啟動 Daemon** –在概念上，這非常類似于以 Windows 服務的形式執行，如上一個步驟所述。 根據預設，組建會在根帳號下執行。

- **OS X 上的使用者帳戶**–您可以在每次使用者登入時啟動的使用者帳戶下執行 TeamCity。

在先前的案例中，在 OS X 上的使用者帳戶下執行 TeamCity 是最簡單且最容易設定的。

設定 TeamCity 有幾個步驟：

- **安裝 TeamCity** –本指南未涵蓋 TeamCity 的安裝。 本指南假設 TeamCity 已安裝並在使用者帳戶下執行。 如需[安裝 TeamCity](http://confluence.jetbrains.com/display/TCD8/Installation)的指示，請參閱 JetBrains 的[TeamCity 8 檔](http://confluence.jetbrains.com/display/TCD8/TeamCity+Documentation)。

- **準備組建伺服器**–此步驟牽涉到安裝建立行動應用程式所需的必要軟體、工具和憑證，並準備好散發。

- 建立**組建腳本**–此步驟並非絕對必要，但組建腳本是用來自動建立應用程式的實用輔助工具。 使用組建腳本有助於疑難排解可能發生的組建問題，並提供一致、可重複的方式來建立要散發的二進位檔，即使不是練習持續整合也一樣。

- **建立 TeamCity 專案**：在前三個步驟完成後，我們必須建立 TeamCity 專案，其中包含取出原始程式碼、編譯專案及提交測試以 Xamarin Test Cloud 所需的所有中繼資料。

## <a name="requirements"></a>需求

需要具備[App Center 測試](https://docs.microsoft.com/appcenter/test-cloud/)的經驗。

必須熟悉 TeamCity 8.1。 TeamCity 的安裝已超出本檔的範圍。 假設 TeamCity 是安裝在 OS X Mavericks 上，而且是在一般使用者帳戶下執行，而不是使用根帳號。

組建伺服器應該是獨立電腦，執行 OS X，專門用於持續整合。 在理想的情況下，組建伺服器不會負責任何其他角色，例如資料庫伺服器、網頁伺服器或開發人員工作站。

> [!IMPORTANT]
> 本指南並未涵蓋 Xamarin 的「無周邊」安裝。

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="preparing-the-build-server"></a>準備組建伺服器

設定組建伺服器的重要步驟是安裝所有必要的工具、軟體和憑證，以建立行動應用程式。 請務必讓組建伺服器能夠編譯行動解決方案並執行任何測試。 若要將設定問題降至最低，應將軟體和工具安裝在裝載 TeamCity 的相同使用者帳戶中。 以下是必要功能的清單：

1. **Visual Studio for Mac** –這包括 [xamarin] 和 [Xamarin. Android]。
2. **登入 Xamarin 元件存放區**–這是選擇性步驟，只有在您的應用程式使用 Xamarin 元件存放區中的元件時才需要。 此時主動登入元件存放區，將會在 TeamCity 組建嘗試編譯應用程式時，避免發生任何問題。
3. **Xcode** –編譯和簽署 iOS 應用程式時需要 Xcode。
4. **Xcode 命令列工具**–在[更新 Ruby 與 Rbenv ruby-build](https://github.com/calabash/calabash-ios/wiki)指南的安裝一節的步驟1中會說明這一點。
5. **簽署身分識別 & 布建設定檔**–透過 XCode 匯入憑證和布建設定檔。 如需詳細資訊，請參閱 Apple 的匯出簽署身分識別和布建[設定檔](https://developer.apple.com/library/ios/recipes/xcode_help-accounts_preferences/articles/export_signing_assets.html)指南。
6. **Android 金鑰庫**–將所需的 Android 金鑰庫複製到 TeamCity 使用者可存取的目錄，亦即 `~/Documents/keystores/MyAndroidApp1`。
7. **Calabash** –如果您的應用程式具有使用 Calabash 撰寫的測試，這是選擇性步驟。 如需詳細資訊，請參閱在[OS X Mavericks 上安裝 Calabash](https://github.com/calabash/calabash-ios/wiki)指南和[使用 rbenv ruby-build 更新 Ruby](https://github.com/calabash/calabash-ios/wiki)指南。

下圖說明所有這些元件：

![](teamcity-images/image1.png "This diagram illustrates all of these components")

安裝完所有軟體之後，請登入使用者帳戶，並確認所有軟體都已正確安裝且正常運作。 這應該包括編譯解決方案，並將應用程式提交至測試雲端。 執行組建腳本可大幅簡化這項工作，如下一節所述。

## <a name="create-a-build-script"></a>建立組建腳本

雖然 TeamCity 可以處理編譯和提交行動應用程式的所有層面，以自行測試雲端，但強烈建議您建立組建腳本。 組建腳本提供下列優點：

1. **檔**–組建腳本可做為檔的形式，以瞭解如何建立軟體。 這會移除一些與部署應用程式相關聯的「魔術」，並可讓開發人員專注于功能。
1. **可重複性**–組建腳本可確保每次編譯和部署應用程式時，都會以完全相同的方式執行，不論工作的物件或作業為何。 這種可重複的一致性會移除可能因為組建或人為錯誤而在中蔓延的任何問題或錯誤。
1. **版本**控制–組建腳本可以包含在原始檔控制系統中。 這表示如果發現錯誤或不正確，可以追蹤、監視和更正組建腳本的變更。
1. **準備環境**–組建腳本可以包含安裝任何必要的協力廠商相依性的邏輯。 這可確保應用程式是以適當的元件建立。

組建腳本可以簡單到 Powershell 檔案（在 Windows 上）或 bash 腳本（在 OS X 上）。 建立組建腳本時，指令碼語言有幾個選擇：

- [**Rake**](https://github.com/jimweirich/rake) –這是用來根據 Ruby 建立專案的特定領域語言（DSL）。 Rake 具有熱門和豐富的程式庫生態系統優勢。

- [**psake**](https://github.com/psake/psake) –這是用來建立軟體的 Windows Powershell 程式庫

- [**假**](http://fsharp.github.io/FAKE/)–這是以 DSL 為基礎F#的，可讓您在必要時利用現有的 .net 程式庫。

所使用的指令碼語言取決於您的喜好設定和需求。 [TaskyPro-Calabash](https://github.com/xamarin/test-cloud-samples/tree/master/TaskyPro/TaskyPro-Calabash)範例包含使用 Rake 做為[組建腳本](https://github.com/xamarin/test-cloud-samples/blob/master/TaskyPro/TaskyPro-Calabash/Rakefile)的範例。

> [!NOTE]
> 您可以使用以 XML 為基礎的組建系統（例如 MSBuild 或 NAnt），但這些都缺乏專門用來建立軟體的 DSL 表達和可維護性。

### <a name="parameterizing-the-build-script"></a>參數化組建腳本

建立和測試軟體的程式需要保密的資訊。 特別是，建立 APK 可能需要金鑰儲存區的密碼和/或金鑰存放區中的金鑰別名。 同樣地，測試雲端需要開發人員特有的 API 金鑰。 這些類型的值不應該在組建腳本中硬式編碼。 相反地，它們應該以變數的形式傳遞至組建腳本。

較不敏感的是 iOS 裝置識別碼或 Android 裝置識別碼之類的值，其可識別測試雲端應用於測試回合的裝置。 這些不是需要保護的值，但它們可能會從組建變更為組建。

將這些類型的變數儲存在組建腳本之外，也可以讓開發人員更輕鬆地在組織內共用組建腳本，例如。 開發人員可以使用與組建伺服器完全相同的腳本，但可以使用自己的金鑰庫和 API 金鑰。

有兩個可能的選項可儲存這些敏感值：

- **設定檔**-若要保護測試雲端 API 金鑰，此值不應簽入版本控制中。 您可以為每部電腦建立檔案。 從這個檔案讀取值的方式，取決於所使用的指令碼語言。

- **環境變數**–這些都可以輕鬆地針對個別電腦設定，並共用與基礎指令碼語言無關。

這些選擇各有其優點和缺點。 TeamCity 適用于環境變數，因此在建立組建腳本時，本指南會建議使用這項技術。

### <a name="build-steps"></a>組建步驟

組建腳本必須能夠執行下列步驟：

- **編譯應用程式**–這包括使用正確的布建設定檔簽署應用程式。

- 將**應用程式提交至 Xamarin Test Cloud** –這包括使用適當的金鑰存放區將 APK 的簽署和 zip 對齊。

下面將更詳細說明這兩個步驟。

#### <a name="compiling-a-xamarinios-application"></a>編譯 Xamarin iOS 應用程式

[!include[](~/tools/ci/includes/commandline-compile-of-xamarin-ios-ipa.md)]

#### <a name="compiling-a-xamarinandroid-application"></a>編譯 Xamarin Android 應用程式

若要編譯 Android 應用程式，請使用**xbuild** （或 Windows 上的**msbuild** ）：

```bash
/Library/Frameworks/Mono.framework/Commands/xbuild /t:SignAndroidPackage /p:Configuration=Release /path/to/android.csproj
```

請注意，若要編譯 Xamarin Android 應用程式**xbuild** ，會使用專案，而若要建立 iOS 應用程式**xbuild** ，則需要解決方案。

#### <a name="submitting-xamarinuitests-to-test-cloud"></a>將 Uitest 提交至測試雲端

Uitest 是使用 `test-cloud.exe` 應用程式提交的，如下列程式碼片段所示：

```bash
test-cloud.exe <path-to-apk-or-ipa-file> <test-cloud-team-api-key> --devices <device-selection-id> --assembly-dir <path-to-tests-containing-test-assemblies> --nunit-xml report.xml --user <email>
```

執行測試時，會**以名為**NUNIT 的 xml 檔案格式傳回測試結果。 TeamCity 將會在組建記錄檔中顯示資訊。

如需如何將 Uitest 提交至測試雲端的詳細資訊，請參閱[準備 Xamarin Android 應用程式](/appcenter/test-cloud/preparing-for-upload/xamarin-android-uitest)或[準備 xamarin 應用程式](/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest)。

#### <a name="submitting-calabash-tests-to-test-cloud"></a>將 Calabash 測試提交至測試雲端

Calabash 測試會使用 `test-cloud` gem 來提交，如下列程式碼片段所示：

```bash
test-cloud submit /path/to/APK-or-IPA <test-cloud-team-api-key> --devices <device-id> --user <email>
```

若要將 Android 應用程式提交至測試雲端，必須先使用 calabash-Android 重建 APK 測試伺服器：

```bash
$ calabash-android build </path/to/signed/APK>
$ test-cloud submit /path/to/APK <test-cloud-team-api-key> --devices <ANDROID_DEVICE_ID> --profile=android --config=config/cucumber.yml --pretty
```

如需提交 Calabash 測試的詳細資訊，請參閱[將 Calabash 測試提交至測試雲端](https://github.com/calabash/calabash-ios/wiki)的 Xamarin 指南。

## <a name="creating-a-teamcity-project"></a>建立 TeamCity 專案

安裝 TeamCity 並 Visual Studio for Mac 可以建立您的專案之後，即可在 TeamCity 中建立專案，以建立專案並將其提交至測試雲端。

1. 透過網頁瀏覽器登入 TeamCity 來啟動。 流覽至根專案：

    ![流覽至根專案](teamcity-images/image2.png "流覽至根專案")在根專案底下，建立新的子專案：

    ![流覽至根專案底下的根專案，建立新的子專案](teamcity-images/image3.png "流覽至根專案底下的根專案，建立新的子專案")
2. 建立子專案之後，請加入新的組建設定：

    ![建立子專案之後，新增組建設定](teamcity-images/image5.png "建立子專案之後，新增組建設定")
3. 將 VCS 專案附加至組建設定。 這是透過 [版本控制設定] 畫面來完成：

    ![這是透過 [版本控制設定] 畫面來完成](teamcity-images/image6.png "這是透過 [版本控制設定] 畫面來完成")

    如果沒有建立任何 VCS 專案，您可以選擇從新的 VCS 根頁面建立一個，如下所示：

    ![如果沒有建立任何 VCS 專案，您可以選擇從新的 [VCS 根] 頁面建立一個](teamcity-images/image7.png "如果沒有建立任何 VCS 專案，您可以選擇從新的 [VCS 根] 頁面建立一個")

    附加了 VCS 根之後，TeamCity 將會簽出項目，並嘗試自動偵測組建步驟。 如果您熟悉 TeamCity，則可以選取其中一個偵測到的組建步驟。 現在您可以放心地忽略偵測到的組建步驟。

4. 接下來，設定組建觸發程式。 這會在符合特定條件時將組建排入佇列，例如當使用者將程式碼認可至存放庫時。 下列螢幕擷取畫面顯示如何新增組建觸發程式：

    ![此螢幕擷取畫面顯示如何新增組建觸發](teamcity-images/image8.png "此螢幕擷取畫面顯示如何新增組建觸發程式")程式您可以在下列螢幕擷取畫面中，看到設定組建觸發程式的範例：

    ![在此螢幕擷取畫面中，可以看到設定組建觸發程式的範例](teamcity-images/image9.png "在此螢幕擷取畫面中，可以看到設定組建觸發程式的範例")

5. 上一節會將組建腳本參數化，建議您將一些值儲存為環境變數。 這些變數可以透過 [參數] 畫面新增至組建設定。 新增測試雲端 API 金鑰、iOS 裝置識別碼和 Android 裝置識別碼的變數，如下列螢幕擷取畫面所示：

    ![新增測試雲端 API 金鑰、iOS 裝置識別碼和 Android 裝置識別碼的變數](teamcity-images/image11.png "新增測試雲端 API 金鑰、iOS 裝置識別碼和 Android 裝置識別碼的變數")

6. 最後一個步驟是新增一個會叫用組建腳本的組建步驟，以編譯應用程式，並將應用程式加入至測試雲端。 下列螢幕擷取畫面是使用 Rakefile 建立應用程式的組建步驟範例：

    ![這個螢幕擷取畫面是使用 Rakefile 建立應用程式的組建步驟範例](teamcity-images/image12.png "這個螢幕擷取畫面是使用 Rakefile 建立應用程式的組建步驟範例")

7. 此時，組建設定已完成。 建議您觸發組建，以確認專案已正確設定。 執行此動作的好方法是，對存放庫認可少量且不重要的變更。 TeamCity 應該會偵測到認可並啟動組建。

8. 組建完成後，請檢查組建記錄檔，並查看是否有任何需要注意的組建發生問題或警告。

## <a name="summary"></a>總結

本指南涵蓋如何使用 TeamCity 來建立 Xamarin 行動應用程式，然後將其提交至測試雲端。 我們已討論如何建立組建腳本，以將組建程式自動化。 組建腳本會負責編譯應用程式、提交至測試雲端，以及等待結果

接著，我們討論了如何在 TeamCity 中建立專案，每次開發人員認可程式碼並呼叫組建腳本時，都會將組建排入佇列。

## <a name="related-links"></a>相關連結

- [準備 Xamarin Android 應用程式](/appcenter/test-cloud/preparing-for-upload/xamarin-android-uitest)
- [準備 Xamarin iOS 應用程式](/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest)
- [安裝和設定 TeamCity](http://confluence.jetbrains.com/display/TCD8/Installing+and+Configuring+the+TeamCity+Server)
