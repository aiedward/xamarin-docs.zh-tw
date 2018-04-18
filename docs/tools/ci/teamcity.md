---
title: 使用小組縣 （市） 與 Xamarin
description: 本指南會討論與使用 TeamCity 編譯行動應用程式，然後將它們提交至 Xamarin Test Cloud 所需的步驟。
ms.prod: xamarin
ms.assetid: AC2626CB-28A7-4808-B2A9-789D67899546
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 03/23/2017
ms.openlocfilehash: 34702fafdd0d767362b0ca32ab56e880ed7cb366
ms.sourcegitcommit: 775a7d1cbf04090eb75d0f822df57b8d8cff0c63
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2018
---
# <a name="using-team-city-with-xamarin"></a>使用小組縣 （市） 與 Xamarin

_本指南會討論與使用 TeamCity 編譯行動應用程式，然後將它們提交至 Xamarin Test Cloud 所需的步驟。_

中所述[連續整合簡介](~/tools/ci/intro-to-ci.md)指南中，持續整合 (CI) 時很有用的作法開發行動應用程式品質。 有許多可行的選項，如持續整合伺服器軟體。本指南將著重在[TeamCity](http://www.jetbrains.com/teamcity/)從 JetBrains。

有數個不同的排列方式 TeamCity 安裝。 以下是一些其中的清單：

- **Windows 服務**-在此案例中，當 Windows 開機為 Windows 服務會啟動 TeamCity。 它必須搭配 Mac 建置主應用程式編譯任何 iOS 應用程式。

- **啟動精靈，在 OS X 上的**– 在概念上，這是非常類似於上一個步驟中所述的 Windows 服務的身分執行。 根據預設，將根帳號底下執行的組建。

- **OS X 上的使用者帳戶**– 很可能在啟動每次使用者登入的使用者帳戶下執行 TeamCity。

先前的案例中，在 OS X 上使用者帳戶下執行 TeamCity 是最簡單且容易安裝程式。

有幾個步驟設定 TeamCity:

- **安裝 TeamCity** – TeamCity 安裝未涵蓋在本指南中。 本指南假設 TeamCity 已安裝且正在執行的使用者帳戶。 指示[安裝 TeamCity](http://confluence.jetbrains.com/display/TCD8/Installation)位於[TeamCity 8 文件](http://confluence.jetbrains.com/display/TCD8/TeamCity+Documentation)由 JetBrains。

- **準備建置伺服器**-這個步驟包括安裝必要的軟體工具，並需要憑證可建置行動應用程式，並準備發佈。

- **建立建置指令碼**-這個步驟不是絕對必要，但組建指令碼是很有用的輔助工具，來建置應用程式自動安裝。 使用組建指令碼可協助疑難排解可能會發生，並提供一致且可重複的方法，來建立散發，二進位碼檔案，即使不已連續整合組建問題。

- **建立 A TeamCity 專案**– 前三個步驟完成後，我們必須建立會包含中繼資料的所有 TeamCity 專案擷取的原始碼、 編譯專案，和送出至 Xamarin Test Cloud 測試所需。

## <a name="requirements"></a>需求

體驗[Xamarin Test Cloud](https://developer.xamarin.com/guides/testcloud)需要。

需要熟悉 TeamCity 8.1。 TeamCity 安裝已超出本文的範圍。 它會假設 TeamCity OS X Mavericks 上已安裝和一般使用者帳戶而不是根帳戶下執行。

組建伺服器應該是獨立的電腦，並執行 OS X，專門用於持續整合。 在理想情況下，組建伺服器不會負責任何其他角色，例如資料庫伺服器、 網頁伺服器或開發人員工作站。

> [!IMPORTANT]
> 本指南未涵蓋的 「 遠端控制 」 安裝 Xamarin。

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="preparing-the-build-server"></a>準備組建伺服器

設定組建伺服器的重要步驟是安裝所有必要工具、 軟體和建置行動應用程式的憑證。 請務必確認組建伺服器能夠編譯行動方案並執行任何測試。 若要降低組態問題，軟體和工具應該安裝在裝載 TeamCity 的相同使用者帳戶。 以下是所需內容的清單：

1. **Visual Studio for Mac** – 這包含 Xamarin.iOS 和 Xamarin.Android。
2. **Xamarin 元件存放區的登入**– 這是選擇性的步驟，而且只需要您的應用程式會從 Xamarin 元件存放區使用元件。 編譯應用程式嘗試 TeamCity 組建時，主動此時登入元件存放區會造成任何問題。
3. **Xcode** – Xcode 時需要編譯並登入的 iOS 應用程式。
4. **Xcode 命令列工具**– 這所述的步驟 1 中的 [安裝] 區段[更新 Ruby 與 rbenv](https://developer.xamarin.com/guides/testcloud/calabash/updating-ruby-using-rbenv/)指南。
5. **簽署識別 （& s） 佈建的設定檔**– 匯入憑證和佈建設定檔，透過 XCode。 請參閱 Apple 指南[匯出的簽署識別和佈建的設定檔](https://developer.apple.com/library/ios/recipes/xcode_help-accounts_preferences/articles/export_signing_assets.html)如需詳細資訊。
6. **Android Keystores** -複製的目錄，TeamCity 使用者具有存取權，也就是必要的 Android keystores `~/Documents/keystores/MyAndroidApp1`。
7. **Calabash** – 這是選擇性步驟，如果您的應用程式已經使用 Calabash 撰寫的測試。 請參閱[OS X Mavericks 上的安裝 Calabash](https://developer.xamarin.com/guides/testcloud/calabash/osx-installation/)指南和[更新 Ruby 與 rbenv](https://developer.xamarin.com/guides/testcloud/calabash/updating-ruby-using-rbenv/)指南以取得詳細資訊。

下圖說明所有這些元件：

![](teamcity-images/image1.png "此圖說明所有這些元件")

一旦所有軟體安裝之後，登入的使用者帳戶，並確認所有軟體都已正確安裝，並可運作。 這應該會編譯方案並送出至測試雲端應用程式。 這可大幅簡化執行組建指令碼中下, 一節中所述。

## <a name="create-a-build-script"></a>建立組建指令碼

雖然它是完全有可能的 TeamCity 處理編譯並提交到測試雲端的行動應用程式本身的所有層面，強烈建議建立組建指令碼。 組建指令碼提供下列優點：

1. **文件**– 組建指令碼做為一種文件上建立軟體的方式。 這會移除部分的"magic"與部署應用程式相關聯，可讓開發人員專注於功能。
1. **重複性**– 組建指令碼可確保應用程式進行編譯和部署，每次它發生的方式完全相同，不論使用者或項目執行這項工作。 任何問題或錯誤，可能會因為不正確地執行的組建中不經意或人為錯誤，則會移除這個重複的一致性。
1. **版本控制**– 組建指令碼可以包含在原始檔控制系統。 這表示，組建指令碼的變更都可以追蹤、 監視，並更正錯誤，如果發現錯誤或不準確。
1. **將環境備妥**– 組建指令碼可包含安裝任何必要第 3 個合作對象相依性的邏輯。 這可確保應用程式所建置的適當的元件。

組建指令碼可以是 Powershell 上的檔案 （Windows) 或 bash 指令碼 （在 OS X) 一樣簡單。 當建立組建指令碼，有數個選項的指令碼語言：

- [**耙**](https://github.com/jimweirich/rake) – 這是用來建置專案，根據 Ruby 網域特定領域語言 (DSL)。 耙具有受歡迎情況看出的優點和豐富的生態系統的程式庫。

- [**psake** ](https://github.com/psake/psake) – 這是建置軟體的 Windows Powershell 程式庫

- [**假造**](http://fsharp.github.io/FAKE/) – 這是在 F # 中，可以利用現有的.NET 程式庫，視基礎 DSL。

使用的指令碼語言，取決於您的喜好設定和需求。 [TaskyPro Calabash](https://github.com/xamarin/test-cloud-samples/tree/master/TaskyPro/TaskyPro-Calabash)範例包含示範如何使用為耙[建置指令碼](https://github.com/xamarin/test-cloud-samples/blob/master/TaskyPro/TaskyPro-Calabash/Rakefile)。

> [!NOTE]
> 您可使用 XML 為基礎建置系統，例如 MSBuild 或 NAnt，但這些缺少表達和 DSL，專門用來建置軟體的可維護性。

### <a name="parameterizing-the-build-script"></a>參數化組建指令碼

建置和測試軟體的程序需要應該保持機密的資訊。 特別是建立 APK 可能需要密碼金鑰存放區和 （或） 在金鑰存放區索引鍵的別名。 同樣地，測試雲端需要至少有唯一的開發人員的 API 金鑰。 這些值的類型不應該硬式編碼的組建指令碼。 而是它們應該傳遞做為變數至組建指令碼。

非機密是例如所執行的 iOS 裝置識別碼或識別應該用於測試雲端的裝置的測試的 Android 裝置識別碼的值。 這些不需要受保護的值，但它們可能會變更從組建。

儲存這些類型的變數之外組建指令碼可輕鬆地在組織內的組建指令碼開發人員的共用，例如。 開發人員可以使用完全相同的指令碼之組建伺服器，但可以使用他們自己的 keystores 和 API 金鑰。

有兩種可能的選項，用於儲存這些機密值：

- **組態檔**– 用以保護此值不應該簽入版本控制的測試雲端 API 金鑰。 每一部電腦，可以建立檔案。 從這個檔案讀取值的方式取決於所使用的指令碼語言。

- **環境變數**– 這些可以輕鬆地設定每部電腦和 ared 無關的基礎的指令碼語言。

有一些優點和缺點，每個選項。 TeamCity 可正確地切割與環境變數中，因此建立建置指令碼時，本指南會建議這項技術。

### <a name="build-steps"></a>建置步驟

組建指令碼必須能夠執行下列步驟：

- **編譯應用程式**– 這包括簽署應用程式正確佈建設定檔。

- **送出應用程式以 Xamarin Test Cloud** – 這包括簽署並對齊 APK 與適當的金鑰存放區壓縮。

這兩個步驟將在下面將更多詳細說明。

#### <a name="compiling-a-xamarinios-application"></a>編譯 Xamarin.iOS 應用程式

[!include[](~/tools/ci/includes/commandline-compile-of-xamarin-ios-ipa.md)]

#### <a name="compiling-a-xamarinandroid-application"></a>編譯 Xamarin.Android 應用程式

若要編譯 Android 應用程式，請使用**xbuild** (或**msbuild** Windows 上):

```bash
/Library/Frameworks/Mono.framework/Commands/xbuild /t:SignAndroidPackage /p:Configuration=Release /path/to/android.csproj
```

請注意，Xamarin Android 應用程式編譯為**xbuild**建置 iOS 應用程式中使用專案和**xbuild**需要解決方案。

#### <a name="submitting-xamarinuitests-to-test-cloud"></a>送出至測試雲端 Xamarin.UITests

使用提交 UITests`test-cloud.exe`應用程式，如下列程式碼片段所示：

```bash
test-cloud.exe <path-to-apk-or-ipa-file> <test-cloud-team-api-key> --devices <device-selection-id> --assembly-dir <path-to-tests-containing-test-assemblies> --nunit-xml report.xml --user <email>
```

測試結果時執行測試時，會呼叫 NUnit 樣式 XML 檔案的形式傳回**report.xml**。 TeamCity 將建置記錄檔中顯示的資訊。

如需如何送出至測試雲端 UITests 的詳細資訊，請參閱 Xamarin 的指南，在[提交 Test Cloud 測試](https://developer.xamarin.com/guides/testcloud/uitest/working-with/submitting-tests-to-xamarin-test-cloud/)。

#### <a name="submitting-calabash-tests-to-test-cloud"></a>正在提交 Calabash 測試來測試雲端

使用提交 Calabash 測試`test-cloud`健身，如下列程式碼片段所示：

```bash
test-cloud submit /path/to/APK-or-IPA <test-cloud-team-api-key> --devices <device-id> --user <email>
```
若要提交到測試雲端 Android 應用程式，就必須先重建使用 calabash android APK 測試伺服器：

```bash
$ calabash-android build </path/to/signed/APK>
$ test-cloud submit /path/to/APK <test-cloud-team-api-key> --devices <ANDROID_DEVICE_ID> --profile=android --config=config/cucumber.yml --pretty
```
如需提交 Calabash 測試的詳細資訊，請參閱 Xamarin 的指南 」 上[提交 Calabash 測試來測試雲端](https://developer.xamarin.com/guides/testcloud/calabash/working-with/submitting-tests-to-xamarin-test-cloud/)。

## <a name="creating-a-teamcity-project"></a>建立 TeamCity 專案

一旦 TeamCity 已安裝與 Visual Studio for Mac 可以建置專案時，就會是 TeamCity 建置專案，並將它提交至測試雲端中建立專案的時候。

1. 啟動登入 TeamCity 透過網頁瀏覽器。 瀏覽至根專案：

    ![瀏覽至根專案](teamcity-images/image2.png "瀏覽至根專案")根專案，底下建立新的子專案：

    ![瀏覽至根專案底下根專案，建立新的子專案](teamcity-images/image3.png "瀏覽至根專案底下根專案，建立新的子專案")
2. 一旦建立子專案之後，加入新的組建組態：

    ![一旦建立子專案之後，加入新的建置組態](teamcity-images/image5.png "一旦建立子專案之後，加入新的組建組態")
3. 附加 VC 專案建置組態。 這是透過 [版本控制設定] 畫面：

    ![這是透過 [版本控制設定] 畫面](teamcity-images/image6.png "透過版本控制設定畫面")

    如果沒有 VC 專案建立，您可以選擇建立一個從新的 VC 根頁面如下所示：

    ![如果沒有 VC 專案建立，您可以選擇建立一個新的 VC 根頁面從](teamcity-images/image7.png "如果沒有 VC 專案建立，您必須建立一個從新的 VC 根頁面的選項")

    在 VC 根附加之後，TeamCity 將簽出的專案，然後再次嘗試自動偵測到建置步驟。 如果您很熟悉 teamcity，然後您可以選取其中一個偵測到的建置步驟。 它可以安全地忽略立即偵測到的建置步驟。

4. 接著，設定建立觸發程序。 當符合特定條件時，例如當使用者認可至儲存機制的程式碼會將佇列的組建。 下列螢幕擷取畫面顯示如何加入組建觸發程序：

    ![這個螢幕擷取畫面顯示如何加入組建觸發程序](teamcity-images/image8.png "這個螢幕擷取畫面顯示如何加入組建觸發程序")下列螢幕擷取畫面中，可以看到設定組建觸發程序的範例：

    ![這個螢幕擷取畫面中，可以看到設定組建觸發程序的範例](teamcity-images/image9.png "這個螢幕擷取畫面中，可以看到設定組建觸發程序的範例")

5. 上一節，將參數化建置指令碼，建議將某些值儲存為環境變數。 這些變數可以加入至組建組態，透過 [參數] 畫面。 將變數加入測試雲端 API 金鑰、 iOS 裝置識別碼和 Android 的裝置識別碼如以下螢幕擷取畫面所示：

    ![將變數加入測試雲端 API 金鑰、 iOS 裝置識別碼和 Android 的裝置識別碼](teamcity-images/image11.png "將變數加入測試雲端 API 金鑰、 iOS 裝置識別碼和 Android 的裝置識別碼")

6. 最後一個步驟是加入建置步驟，將會叫用組建指令碼来編譯的應用程式和測試雲端應用程式加入佇列。 下列螢幕擷取畫面是使用 Rakefile 建置應用程式的建置步驟的範例：

    ![這個螢幕擷取畫面是使用 Rakefile 建置應用程式的建置步驟的範例](teamcity-images/image12.png "這個螢幕擷取畫面是使用 Rakefile 建置應用程式的建置步驟的範例")

7. 此時，已完成組建設定。 最好要確認已正確設定專案的組建觸發程序。 若要這樣做的好方法是小型的無意義的變更認可至儲存機制。 TeamCity 應該偵測認可，並啟動組建。

8. 組建完成後，請檢查組建記錄檔，並查看是否有任何問題或需要注意的建置警告。

## <a name="summary"></a>總結

本指南涵蓋如何使用 TeamCity 建置 Xamarin 的行動應用程式，然後將其發行到測試雲端。 我們將討論建立組建指令碼來自動化建置程序。 組建指令碼會負責編譯應用程式、 送出到測試雲端中，並且等候的結果

然後我們涵蓋如何在 TeamCity 將組建排入佇列的程式碼開發人員會認可每次，並將呼叫組建指令碼中建立專案。

## <a name="related-links"></a>相關連結

- [正在提交測試，Xamarin Test Cloud (UITest)](https://developer.xamarin.com~/testcloud/uitest/working-with/submitting-tests-to-xamarin-test-cloud/)
- [正在提交測試，Xamarin Test Cloud (Calabash)](https://developer.xamarin.com~/testcloud/calabash/working-with/submitting-tests-to-xamarin-test-cloud/)
- [安裝和設定 TeamCity](http://confluence.jetbrains.com/display/TCD8/Installing+and+Configuring+the+TeamCity+Server)
