---
title: 使用團隊城與薩馬林
description: 本指南將討論使用 TeamCity 編譯移動應用程式,然後將其提交到應用中心測試所涉及的步驟。
ms.prod: xamarin
ms.assetid: AC2626CB-28A7-4808-B2A9-789D67899546
author: davidortinau
ms.author: daortin
ms.date: 04/01/2020
ms.openlocfilehash: 6ecd453180e8c392ba7d7527778617eb40950a9e
ms.sourcegitcommit: 6f3281a32017cfcebadde8a2d6e10651a277828f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80587457"
---
# <a name="using-team-city-with-xamarin"></a>使用團隊城與薩馬林

_本指南將討論使用 TeamCity 編譯移動應用程式,然後將其提交到應用中心測試所涉及的步驟。_

如[《持續集成入門指南》](~/tools/ci/intro-to-ci.md)所述,在開發高質量的移動應用程式時,持續集成 (CI) 是一種有用的實踐。 連續集成伺服器軟體有許多可行的選項;本指南將重點介紹捷腦團隊[城](https://www.jetbrains.com/teamcity/)。

TeamCity 安裝有幾個不同的排列。 下面的清單描述了其中一些排列:

- **Windows 服務**– 在此方案中,團隊城在 Windows 作為 Windows 服務啟動時啟動。 它必須與 Mac 構建主機配對才能編譯任何 iOS 應用程式。

- **在 OS X 上啟動守護進程**– 從概念上講,這類似於在上一步中描述的 Windows 服務中所述的運行。 默認情況下,生成將在根帳戶下運行。

- **OS X 上的使用者帳戶**– 可以在每次使用者登錄時啟動的使用者帳戶下運行 TeamCity。

在前面的方案中,在 OS X 上的使用者帳戶下運行 TeamCity 是最簡單、最簡單的設置。

設定團隊城涉及幾個步驟:

- **安裝團隊城**– 本指南中未涵蓋 TeamCity 的安裝。 本指南假定 TeamCity 是在使用者帳戶下安裝和運行的。 有關安裝[TeamCity](https://confluence.jetbrains.com/display/TCD8/Installation)的說明,請參閱 JetBrains 的[TeamCity 8 文檔](https://confluence.jetbrains.com/display/TCD8/TeamCity+Documentation)。

- **準備生成伺服器**– 此步驟涉及安裝構建移動應用程式並準備分發所需的軟體、工具和證書。

- **創建生成腳本**– 此步驟並非絕對必要,但生成腳本是構建無人值守的應用程式的有用説明。 使用生成腳本將有助於解決可能出現的生成問題,並提供一種一致、可重複的方式創建分發二進位檔案,即使未實踐連續整合也是如此。

- **創建團隊城專案**– 完成前三個步驟後,我們必須創建一個團隊城專案,該專案將包含檢索原始程式碼、編譯專案並將測試提交到應用中心測試所需的所有元資料。

## <a name="requirements"></a>需求

需要使用[應用中心測試](https://docs.microsoft.com/appcenter/test-cloud/)的經驗。

需要熟悉團隊城 8.1。 TeamCity 的安裝超出了本文檔的範圍。 假定 TeamCity 安裝在 OS X 小牛上,並且在常規使用者帳戶下運行,而不是根帳戶。

生成伺服器應該是一台獨立計算機,運行 OS X,專用於持續集成。 理想情況下,生成伺服器不負責任何其他角色,如資料庫伺服器、Web 伺服器或開發人員工作站。

> [!IMPORTANT]
> 本指南不包括 Xamarin 的「無頭」安裝。

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="preparing-the-build-server"></a>準備產生伺服器

配置生成伺服器的一個關鍵步驟是安裝構建移動應用程式所需的所有工具、軟體和證書。 生成伺服器可以編譯行動解決方案並運行任何測試,這一點很重要。 為了盡量減少配置問題,軟體和工具應安裝在託管 TeamCity 的同一使用者帳戶中。 以下清單詳細說明了需要的內容:

1. **適用於 Mac 的視覺工作室**– 這包括 Xamarin.iOS 和 Xamarin.安卓。
2. **登錄到 Xamarin 元件存儲**– 此步驟是可選的,僅當應用程式使用 Xamarin 元件存儲中的元件時才是必需的。 此時主動登錄到元件存儲將防止 TeamCity 生成嘗試編譯應用程式時出現任何問題。
3. **Xcode** = Xcode 需要編譯和簽名 iOS 應用程式。
4. **Xcode 命令列工具**– 這在[使用 rbenv 指南的「更新 Ruby」](https://github.com/calabash/calabash-ios/wiki)安裝部分的步驟 1 中對此進行了介紹。
5. **簽署識別&預配設定檔**- 透過 XCode 匯入憑證和預配設定檔。 有關詳細資訊,請參閱 Apple 有關[匯出簽名標識和預配配置檔的](https://developer.apple.com/library/ios/recipes/xcode_help-accounts_preferences/articles/export_signing_assets.html)指南。
6. **Android 金鑰儲存**- 將所需的 Android 金鑰儲存複製到 TeamCity 使用者`~/Documents/keystores/MyAndroidApp1`有權存取的目錄,即 。
7. **卡拉巴什**– 如果您的應用程式使用 Calabash 編寫測試,則這是可選步驟。 有關詳細資訊,請參閱[在 OS X 小牛指南上安裝卡拉巴什](https://github.com/calabash/calabash-ios/wiki)指南和使用[rbenv 更新 Ruby](https://github.com/calabash/calabash-ios/wiki)指南。

下圖說明了所有這些元件:

![](teamcity-images/image1.png "This diagram illustrates all of these components")

安裝所有軟體後,登錄到使用者帳戶並確認所有軟體都已正確安裝並正常工作。 這應該涉及編譯解決方案並將應用程式提交到應用中心測試。 這可以通過運行生成腳本來簡化,如下一節所述。

## <a name="create-a-build-script"></a>建立建立文稿

儘管 TeamCity 可以自行處理編譯移動應用程式並將其提交到應用中心測試的所有方面;建議創建生成腳本。 產生文稿具有以下優點:

1. **文件**– 生成腳本用作有關如何構建軟體的文檔形式。 這將刪除與部署應用程式相關的一些"魔力",並允許開發人員專注於功能。
1. **可重複性**– 生成腳本可確保每次編譯和部署應用程式時,無論工作由誰或做什麼,它都會以相同的方式發生。 這種可重複的一致性可消除由於執行不正確的生成或人為錯誤而可能出現的任何問題或錯誤。
1. **版本控制**– 生成文稿可以包含在原始程式碼管理系統中。 這意味著,如果發現錯誤或不準確,可以跟蹤、監視和更正對生成腳本的更改。
1. **準備環境**– 生成腳本可以包含安裝任何必需的第三方依賴項的邏輯。 這將確保應用程式使用適當的元件構建。

生成文稿可以像 PowerShell 檔(在 Windows 上)或 bash 腳本(在 OS X 上)一樣簡單。 建立產生文稿時,文稿語言有多種選擇:

- [**Rake**](https://github.com/jimweirich/rake) – 這是一種基於 Ruby 構建專案的域特定語言 (DSL)。 Rake 具有普及和豐富的圖書館生態系統的優勢。

- [**psake**](https://github.com/psake/psake) – 這是用於建構軟體的 Windows PowerShell 函式庫

- [**FAKE**](https://fsharp.github.io/FAKE/) – 這是基於 F# 中的 DSL,因此在必要時可以使用現有的 .NET 庫。

使用哪種腳本語言取決於您的首選項和要求。

> [!NOTE]
> 可以使用基於 XML 的建構系統(如 MSBuild 或 NAnt),但這些系統缺乏專用於建構軟體的 DSL 的表達性和可維護性。

### <a name="parameterizing-the-build-script"></a>參數化產生文稿

構建和測試軟體的過程需要應保密的資訊。 創建 APK 可能需要金鑰儲存和 /或金鑰存儲中的密鑰別名的密碼。 同樣,應用程式中心測試需要開發人員獨有的[API 金鑰](/appcenter/api-docs/)。 這些類型的值不應在生成腳本中硬編碼。 相反,它們應該作為變數傳遞給生成腳本。

不太敏感的是識別應用中心應用於測試運行的設備的值,如 iOS 設備 ID 或 Android 設備 ID。 這些值不是需要保護的值,但它們可能會從生成更改為生成。

在生成腳本之外存儲這些類型的變數還便於在組織內與開發人員共用生成腳本,例如。 開發人員可以使用與產生伺服器完全相同的文稿,但可以使用自己的金鑰庫和[API 金鑰](/appcenter/api-docs/)。

儲存這些敏感值有兩種可能的選項:

- **設定檔**– 為了保護[API 金鑰](/appcenter/api-docs/),不應將此值簽入版本控制項。 可以為每台電腦創建該檔。 如何從該檔讀取值取決於所使用的腳本語言。

- **環境變數**– 這些變數可以輕鬆地基於每台計算機設置,並且獨立於基礎腳本語言。

每個選項都有優缺點。 TeamCity 很好地處理環境變數,因此本指南將在創建生成腳本時推薦此技術。

### <a name="build-steps"></a>產生步驟

產生文稿必須執行以下步驟:

- **編譯應用程式**– 這包括使用正確的預配配置檔對應用程式進行簽名。

- **將應用程式提交到 Xamarin 測試雲**– 這包括簽名和壓縮 APK 與相應的密鑰庫對齊。

下面將更詳細地解釋這兩個步驟。

#### <a name="compiling-a-xamarinios-application"></a>編譯 Xamarin.iOS 應用程式

[!include[](~/tools/ci/includes/commandline-compile-of-xamarin-ios-ipa.md)]

#### <a name="compiling-a-xamarinandroid-application"></a>編譯 Xamarin.安卓應用程式

要編譯 Android 應用程式,請使用**xbuild(** 或 Windows 上的**msbuild):**

```bash
/Library/Frameworks/Mono.framework/Commands/xbuild /t:SignAndroidPackage /p:Configuration=Release /path/to/android.csproj
```
編譯 Android 應用程式**xbuild**使用專案,而 iOS 應用程式**xbuild**使用解決方案。

#### <a name="submitting-xamarinuitests-to-app-center"></a>向應用程式中心提交 Xamarin.UI 測試

UI測試使用[應用中心 CLI](https://github.com/microsoft/appcenter-cli)提交,如以下代碼段所示:

```bash
appcenter test run uitest --app <TEAM-NAME/APP-NAME> --devices <DEVICE_SET> --token <API_KEY> --app-path <appname.APK-or-appname.IPA> --merge-nunit-xml report.xml --build-dir pathToUITestBuildDir
```

運行測試時,測試結果將以 NUnit 樣式 XML 檔(稱為**report.xml)** 的形式返回。 TeamCity 將在生成日誌中顯示資訊。

有關如何向應用中心提交 UI 測試的詳細資訊,請參閱準備[Xamarin.Android 應用](/appcenter/test-cloud/uitest/preparing-for-upload-android)或[準備 Xamarin.iOS 應用](/appcenter/test-cloud/uitest/preparing-for-upload-ios)。

#### <a name="submitting-calabash-tests-to-app-center"></a>向應用中心提交卡拉巴什測試

卡拉巴什測試使用[應用中心 CLI](https://github.com/microsoft/appcenter-cli)提交,如以下代碼段所示:

```bash
appcenter test run calabash --app <TEAM-NAME/APP-NAME> --devices <DEVICE_SET> --token <API_KEY> --app-path <appname.APK-or-appname.IPA> --project-dir pathToProjectDir
```

要將 Android 應用程式提交到應用中心測試,必須首先使用 calabash-android 重建 APK 測試伺服器:

```bash
$ calabash-android build </path/to/signed/APK>
$ appcenter test run calabash --app <TEAM-NAME/APP-NAME> --devices <DEVICE_SET> --token <API_KEY> --app-path <appname.APK> --project-dir pathToProjectDir
```

有關提交卡拉巴什測試的更多資訊,請參閱 Xamarin 的關於[將卡拉巴什測試提交測試雲](https://github.com/calabash/calabash-ios/wiki)的指南。

## <a name="creating-a-teamcity-project"></a>建立團隊城市項目

安裝 TeamCity 和 Mac 的可視化工作室可以構建您的專案後,是時候在 TeamCity 中創建專案來生成專案並將其提交到應用中心。

1. 首先通過 Web 瀏覽器登錄到 TeamCity。 導覽到根專案:

    ![瀏覽到根項目](teamcity-images/image2.png "瀏覽到根項目")在根專案下方,建立新的子專案:

    ![瀏覽到根項目下面的根項目建立新的子專案](teamcity-images/image3.png "瀏覽到根項目下面的根項目建立新的子專案")
2. 建立子項目後,新增新的產生配置:

    ![建立子項目後,新增新的產生配置](teamcity-images/image5.png "建立子項目後,新增新的產生配置")
3. 將 VCS 專案附加到生成配置。 這通過版本控制設定螢幕完成:

    ![這是透過版本控制設定螢幕完成的](teamcity-images/image6.png "這是透過版本控制設定螢幕完成的")

    如果沒有創建 VCS 專案,則可以從下面的「新 VCS 根」頁創建一個專案:

    ![如果沒有創建 VCS 專案,則可以從「新 VCS 根」頁建立一個專案](teamcity-images/image7.png "如果沒有建立 VCS 專案,則可以選擇從「新 VCS 根」頁建立一個專案")

    附加 VCS 根後,TeamCity 將簽出專案並嘗試自動檢測生成步驟。 如果您熟悉 TeamCity,則可以選擇檢測到的生成步驟之一。 現在可以安全地忽略檢測到的生成步驟。

4. 接下來,配置生成觸發器。 這將在滿足某些條件(例如使用者將代碼提交到存儲庫時)對生成進行排隊。 以下螢幕擷取如何新增產生觸發器:

    ![此螢幕擷取如何新增產生觸發器](teamcity-images/image8.png "此螢幕擷取如何新增產生觸發器")設定產生觸發器的範例可在以下螢幕截圖中看到:

    ![在這裡螢幕擷取設定產生觸發器的範例](teamcity-images/image9.png "在這裡螢幕擷取設定產生觸發器的範例")

5. 上一節"參數化生成腳本"建議將某些值存儲為環境變數。 這些變數可以通過"參數"螢幕添加到生成配置中。 新增應用中心[API 金鑰](/appcenter/api-docs/)、iOS 設備 ID 和 Android 設備 ID 的變數,如下圖所示:

    ![新增應用中心測試 API 金鑰、iOS 裝置 ID 和 Android 裝置 ID 的變數](teamcity-images/image11.png "新增測試雲 API 金鑰、iOS 裝置 ID 和 Android 裝置 ID 的變數")

6. 最後一步是添加一個生成步驟,該步驟將調用生成腳本來編譯應用程式並將應用程式排隊到應用中心測試。 以下螢幕擷取 Rakefile 產生應用程式的產生步驟的範例:

    ![此螢幕擷取 Rakefile 建構應用程式的產生步驟的範例](teamcity-images/image12.png "此螢幕擷取 Rakefile 建構應用程式的產生步驟的範例")

7. 此時,生成配置已完成。 最好觸發生成以確認專案配置正確。 執行此操作的一個好方法是對存儲庫提交一個小的、微不足道的更改。 TeamCity 應檢測提交並啟動生成。

8. 生成完成後,檢查生成日誌,並查看生成是否有任何問題或警告需要注意。

## <a name="summary"></a>總結

本指南介紹了如何使用 TeamCity 構建 Xamarin 行動應用程式,然後將其提交到應用中心測試。 我們討論了創建生成腳本以自動執行生成過程。 生成腳本負責編譯應用程式、提交到應用中心測試以及等待結果。

然後,我們介紹了如何在 TeamCity 中創建一個專案,該專案將在開發人員提交代碼並調用生成腳本時對生成進行排隊。

## <a name="related-links"></a>相關連結

- [準備 Xamarin.安卓應用程式](/appcenter/test-cloud/uitest/preparing-for-upload-android)
- [準備 Xamarin.iOS 應用程式](/appcenter/test-cloud/uitest/preparing-for-upload-ios)
- [安裝與設定團隊城](https://confluence.jetbrains.com/display/TCD8/Installing+and+Configuring+the+TeamCity+Server)
