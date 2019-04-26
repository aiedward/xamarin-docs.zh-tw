---
title: 搭配 Xamarin 使用小組縣 （市）
description: 本指南會討論使用 TeamCity 編譯行動應用程式，並再將它們提交到 Xamarin Test Cloud 所需的步驟。
ms.prod: xamarin
ms.assetid: AC2626CB-28A7-4808-B2A9-789D67899546
author: lobrien
ms.author: laobri
ms.date: 03/23/2017
ms.openlocfilehash: a3cb79f33b64d933aa8ab4d3555479cc16238992
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61218886"
---
# <a name="using-team-city-with-xamarin"></a>搭配 Xamarin 使用小組縣 （市）

_本指南會討論使用 TeamCity 編譯行動應用程式，並再將它們提交到 Xamarin Test Cloud 所需的步驟。_

中所述[連續整合簡介](~/tools/ci/intro-to-ci.md)指南中，持續整合 (CI) 是很有用的作法開發高品質的行動應用程式時。 有許多基本的可行的選項，如持續整合伺服器軟體;本指南將著重[TeamCity](http://www.jetbrains.com/teamcity/) jetbrains。

有數個不同的排列方式的 TeamCity 安裝。 以下是其中部分項目的清單：

- **Windows 服務**-在此案例中，TeamCity 啟動 Windows 時開機成 Windows 服務。 它必須與編譯任何 iOS 應用程式的 「 Mac 組建主機配對。

- **啟動精靈，在 OS X 上的**– 就概念而言，這是非常類似於上一個步驟中所述的 Windows 服務身分執行。 根據預設，將根帳戶下執行組建。

- **在 OS X 上的使用者帳戶**– 您可在每次使用者登入時啟動的使用者帳戶下執行 TeamCity。

前一個案例中，在 OS X 上執行的使用者帳戶下的 TeamCity 是最簡單且容易安裝。

有幾個步驟設定 TeamCity:

- **安裝 TeamCity** – 本指南未涵蓋的 TeamCity 安裝。 本指南假設 TeamCity 已安裝且正在執行的使用者帳戶下。 上的指示[安裝 TeamCity](http://confluence.jetbrains.com/display/TCD8/Installation)篇[TeamCity 8 文件](http://confluence.jetbrains.com/display/TCD8/TeamCity+Documentation)jetbrains。

- **準備組建伺服器**-這個步驟包含安裝必要的軟體工具，並需要憑證可建置行動應用程式，並準備發佈。

- **建立建置指令碼**-這個步驟不是絕對必要，但建置指令碼是很有用的輔助工具，建置應用程式自動安裝。 使用組建指令碼可協助進行疑難排解，可能會發生，並提供一致、 高再現性的方法，來建立散發的二進位檔，即使不已練習過持續整合組建問題。

- **建立 TeamCity 專案**– 前三個步驟都完成後，我們必須建立將包含所有中繼資料的 TeamCity 專案需要擷取的原始程式碼、 編譯專案，並提交到 Xamarin Test Cloud 測試。

## <a name="requirements"></a>需求

體驗[Xamarin Test Cloud](https://developer.xamarin.com/guides/testcloud)需要。

需要熟悉 TeamCity 8.1。 TeamCity 的安裝已超出本文的範圍。 它會假設 TeamCity OS X Mavericks 上已安裝，而且一般使用者帳戶而不是根帳戶下執行。

在組建伺服器應該是獨立的電腦，並執行 OS X，專門用於持續整合。 在理想情況下，組建伺服器不會負責任何其他角色，例如資料庫伺服器、 網頁伺服器或開發人員工作站。

> [!IMPORTANT]
> 本指南並未涵蓋 Xamarin 的 「 無周邊 」 安裝。

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="preparing-the-build-server"></a>準備組建伺服器

安裝所有必要的工具、 軟體和建置行動應用程式的憑證，是很重要的步驟中設定組建伺服器。 請務必在組建伺服器，是能夠編譯行動解決方案，並執行任何測試。 若要降低組態問題，軟體和工具應該安裝在裝載 TeamCity 的相同使用者帳戶。 以下是所需項目的清單：

1. **Visual Studio for Mac** – 這包含 Xamarin.iOS 和 Xamarin.Android。
2. **登入 Xamarin 元件存放區**– 這是選擇性步驟，而且只需要您的應用程式會從 Xamarin 元件存放區使用的元件。 TeamCity 組建嘗試將編譯的應用程式時，主動在此時登入元件存放區會造成任何問題。
3. **Xcode** – Xcode，才能編譯並登入的 iOS 應用程式。
4. **Xcode 命令列工具**– 這所述的步驟 1 中的 [安裝] 區段[rbenv 與更新的 Ruby](https://developer.xamarin.com/guides/testcloud/calabash/updating-ruby-using-rbenv/)指南。
5. **簽署身分識別和佈建設定檔**– 匯入憑證和佈建設定檔，透過 XCode。 請參閱 Apple 指南[匯出的簽署識別和佈建設定檔](https://developer.apple.com/library/ios/recipes/xcode_help-accounts_preferences/articles/export_signing_assets.html)如需詳細資訊。
6. **Android Keystores** – 將所需的 Android keystores 複製到 TeamCity 使用者具有存取權，也就是目錄`~/Documents/keystores/MyAndroidApp1`。
7. **Calabash** – 這是選擇性步驟，如果您的應用程式已經使用 Calabash 撰寫的測試。 請參閱[OS X Mavericks 上的安裝 Calabash](https://developer.xamarin.com/guides/testcloud/calabash/osx-installation/)指南並[rbenv 與更新的 Ruby](https://developer.xamarin.com/guides/testcloud/calabash/updating-ruby-using-rbenv/)指南以取得詳細的資訊。

下圖說明所有這些元件：

![](teamcity-images/image1.png "此圖說明所有這些元件")

一旦所有軟體安裝之後，登入的使用者帳戶，並確認所有軟體都已正確安裝，而且運作。 這應該包括編譯方案，並送出至 Test Cloud 應用程式。 這可大幅簡化執行建置指令碼，在下一節中所述。

## <a name="create-a-build-script"></a>建立組建指令碼

雖然完全有可能的 TeamCity 處理編譯並提交至 Test Cloud 的行動應用程式本身的所有層面，但強烈建議建立的組建指令碼。 建置指令碼提供下列優點：

1. **文件**– 建置指令碼做為一種文件上建置軟體的方式。 這會移除一些 「 神奇 」 與部署應用程式相關聯，可讓開發人員專注於功能。
1. **可重複性**– 建置指令碼可確保每次應用程式是編譯及部署，其發生的方式完全相同，無論誰或功能運作。 任何問題或錯誤，因為未正確執行的組建中可能會蔓延或人為錯誤，則會移除這個重複的一致性。
1. **版本控制**– 建置指令碼可以包含在原始檔控制系統。 這表示，建置指令碼的變更可以追蹤、 監視，並更正如果發現錯誤或不正確。
1. **準備環境**– 建置指令碼可以包含邏輯來安裝任何必要的第 3 個合作對象相依性。 這可確保應用程式會以適當的元件。

建置指令碼可以簡單，像是 Powershell 上的檔案 （Windows) 或 bash 指令碼 （在 OS X)。 在建立組建指令碼時，有數個選項的指令碼語言：

- [**Rake** ](https://github.com/jimweirich/rake) – 這是網域特定語言 (DSL) 來建置專案，根據 Ruby。 Rake 有大受歡迎的優點和程式庫的豐富生態系統。

- [**psake** ](https://github.com/psake/psake) – 這是建置軟體的 Windows Powershell 程式庫

- [**假**](http://fsharp.github.io/FAKE/) – 這是在 DSLF#這讓您能夠利用現有的.NET 程式庫，如有必要。

會使用的指令碼語言取決於您的喜好設定和需求。 [TaskyPro Calabash](https://github.com/xamarin/test-cloud-samples/tree/master/TaskyPro/TaskyPro-Calabash)範例包含使用 Rake 為的範例[建置指令碼](https://github.com/xamarin/test-cloud-samples/blob/master/TaskyPro/TaskyPro-Calabash/Rakefile)。

> [!NOTE]
> 可以使用 XML 為基礎建置系統，例如 MSBuild 或 NAnt，但這些缺少的易讀性與 DSL，專門用來建置軟體的可維護性。

### <a name="parameterizing-the-build-script"></a>參數化的組建指令碼

建置和測試軟體的程序需要應該保持機密的資訊。 特別是建立 APK，可能會要求輸入密碼以金鑰存放區和/或在金鑰存放區索引鍵的別名。 同樣地，測試雲端需要至少是唯一的開發人員的 API 金鑰。 這些值的類型不應該硬式編碼在建置指令碼。 而是應該將它們傳遞為變數建置指令碼。

較不容易受到是例如執行 iOS 的裝置識別碼或識別應該用於測試雲端的裝置所測試的 Android 裝置識別碼的值。 這些不會受到保護，需要的值，但它們可能會變更不同組建。

儲存這些類型的變數之外建置指令碼也可讓您更輕鬆地與開發人員，例如共用組織內的組建指令碼。 開發人員可能會與組建伺服器，使用完全相同的指令碼，但是可以使用自己的 keystores 和 API 金鑰。

有兩個可能的選項，來儲存這些敏感的值：

- **組態檔**– 來保護測試雲端 API 金鑰，這個值不應該簽入版本控制。 每部機器，就可以建立檔案。 從這個檔案讀取值的方式，取決於所使用的指令碼語言。

- **環境變數**– 這些可以輕鬆地在每部電腦，然後邊框無關的基礎的指令碼語言設定。

有其優點和缺點每個選擇。 TeamCity 使用環境變數運作得很好，因此本指南建立的組建指令碼時，會建議這項技術。

### <a name="build-steps"></a>建置步驟

建置指令碼必須能夠執行下列步驟：

- **將應用程式編譯**– 這包含簽署應用程式正確佈建設定檔。

- **提交到 Xamarin Test Cloud 應用程式**– 這包含簽署並對齊 APK 使用適當的金鑰儲存區的 zip。

這兩個步驟將會在下面詳細說明。

#### <a name="compiling-a-xamarinios-application"></a>編譯的 Xamarin.iOS 應用程式

[!include[](~/tools/ci/includes/commandline-compile-of-xamarin-ios-ipa.md)]

#### <a name="compiling-a-xamarinandroid-application"></a>編譯 Xamarin.Android 應用程式

若要編譯的 Android 應用程式，請使用**xbuild** (或**msbuild**在 Windows 上):

```bash
/Library/Frameworks/Mono.framework/Commands/xbuild /t:SignAndroidPackage /p:Configuration=Release /path/to/android.csproj
```

請注意，Xamarin Android 應用程式編譯**xbuild**建置 iOS 應用程式會使用專案中，而且**xbuild**需要方案。

#### <a name="submitting-xamarinuitests-to-test-cloud"></a>提交至 Test Cloud Xamarin.UITests

使用提交 Uitest`test-cloud.exe`應用程式，如下列程式碼片段所示：

```bash
test-cloud.exe <path-to-apk-or-ipa-file> <test-cloud-team-api-key> --devices <device-selection-id> --assembly-dir <path-to-tests-containing-test-assemblies> --nunit-xml report.xml --user <email>
```

測試結果時執行測試時，會傳回名的 NUnit 樣式 XML 檔案的形式**report.xml**。 TeamCity 將建置記錄檔中顯示的資訊。

如需如何將提交至 Test Cloud 的 Uitest 的詳細資訊，請參閱本指南上[準備上傳的 Xamarin.UITests](/appcenter/test-cloud/preparing-for-upload/uitest/)。

#### <a name="submitting-calabash-tests-to-test-cloud"></a>提交 Calabash 測試來測試雲端

使用提交 Calabash 測試`test-cloud`gem，如下列程式碼片段所示：

```bash
test-cloud submit /path/to/APK-or-IPA <test-cloud-team-api-key> --devices <device-id> --user <email>
```
若要提交至測試雲端的 Android 應用程式，就必須先重建使用 calabash android APK 測試伺服器：

```bash
$ calabash-android build </path/to/signed/APK>
$ test-cloud submit /path/to/APK <test-cloud-team-api-key> --devices <ANDROID_DEVICE_ID> --profile=android --config=config/cucumber.yml --pretty
```
如需有關提交 Calabash 測試的詳細資訊，請參閱 Xamarin 的指南 」 上[送出至 Test Cloud 的 Calabash 測試](https://developer.xamarin.com/guides/testcloud/calabash/working-with/submitting-tests-to-xamarin-test-cloud/)。

## <a name="creating-a-teamcity-project"></a>建立 TeamCity 專案

一旦安裝 TeamCity 和 Visual Studio for Mac 可以建置專案，就可以建置專案，並將它提交至 Test Cloud 的 TeamCity 在建立專案。

1. 透過網頁瀏覽器的登入 TeamCity 開始著手。 瀏覽至根專案：

    ![瀏覽至根專案](teamcity-images/image2.png "瀏覽至根專案")根專案，底下建立新的子專案：

    ![瀏覽至根專案下面根專案，建立新的子專案](teamcity-images/image3.png "瀏覽至根專案下面根專案，建立新的子專案")
2. 子專案建立之後，加入新的建置組態：

    ![一旦建立子專案，加入新的組建組態](teamcity-images/image5.png "建立子專案之後, 加入新的組建組態")
3. 附加 VC 專案的建置組態。 這是透過 [版本控制設定] 畫面中完成：

    ![這會透過 [版本控制設定] 畫面](teamcity-images/image6.png "這會透過 [版本控制設定] 畫面")

    如果沒有 VC 專案建立，您可以建立一個從新的 VC 根頁面，如下所示：

    ![如果沒有 VC 專案建立，您必須建立一個新的 VC 根頁面的選項](teamcity-images/image7.png "如果沒有 VC 專案建立，您必須建立一個新的 VC 根頁面的選項")

    在 VCS 根附加之後，TeamCity 會簽出的專案，然後嘗試自動偵測該建置步驟。 如果您已熟悉使用 TeamCity，然後您可以選取其中一個偵測到的組建步驟。 它可以安全地忽略目前的偵測到的建置步驟。

4. 接下來，設定組建觸發程序。 這將會排入佇列組建時符合特定條件，例如當使用者認可至存放庫的程式碼。 下列螢幕擷取畫面顯示如何新增組建觸發程序：

    ![此螢幕擷取畫面顯示如何新增組建觸發程序](teamcity-images/image8.png "此螢幕擷取畫面顯示如何新增組建觸發程序")以下的螢幕擷取畫面中，就可以看到設定組建觸發程序的範例：

    ![設定組建觸發程序的範例可以在這個螢幕擷取畫面中看到](teamcity-images/image9.png "設定組建觸發程序的範例可以看到此螢幕擷取畫面")

5. 上節中，參數化建置指令碼，建議將一些值儲存為環境變數。 這些變數可以將組建組態，透過 [參數] 畫面。 將變數新增測試雲端 API 金鑰、 iOS 裝置識別碼和 Android 的裝置識別碼如以下螢幕擷取畫面所示：

    ![將變數新增測試雲端 API 金鑰、 iOS 裝置識別碼和 Android 的裝置識別碼](teamcity-images/image11.png "將變數新增測試雲端 API 金鑰、 iOS 裝置識別碼和 Android 的裝置識別碼")

6. 最後一個步驟是加入將會叫用建置指令碼来編譯的應用程式和加入佇列到測試雲端應用程式的建置步驟。 下列螢幕擷取畫面是用以 Rakefile 建置應用程式的建置步驟的範例：

    ![此螢幕擷取畫面是用以 Rakefile 建置應用程式的建置步驟的範例](teamcity-images/image12.png "這個螢幕擷取畫面是用以 Rakefile 建置應用程式的建置步驟的範例")

7. 此時，已完成組建設定。 您最好要確認已正確設定專案的組建觸發程序。 若要這樣做的好方法是小型的無意義的變更認可至儲存機制。 TeamCity 應該偵測認可，並開始建置。

8. 完成組建後，檢查組建記錄檔，並查看是否有任何問題或需要注意的警告與組建。

## <a name="summary"></a>總結

本指南涵蓋如何使用 TeamCity 建置 Xamarin 行動裝置應用程式，並再將它們提交至 Test Cloud。 我們討論如何建立自動化建置流程的組建指令碼。 組建指令碼會負責編譯應用程式提交至 Test Cloud，等待的結果

然後我們涵蓋了如何將組建排入佇列的每當開發人員認可程式碼，會呼叫建置指令碼的 TeamCity 在建立專案。

## <a name="related-links"></a>相關連結

- [正在準備 Xamarin.UITests fpr 上傳](/appcenter/test-cloud/preparing-for-upload/uitest/)
- [安裝和設定 TeamCity](http://confluence.jetbrains.com/display/TCD8/Installing+and+Configuring+the+TeamCity+Server)
