---
title: "Xamarin.iOS 錯誤"
ms.topic: article
ms.prod: xamarin
ms.assetid: 9F76162B-D622-45DA-996B-2FBF8017E208
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/26/2017
ms.openlocfilehash: 5a11ca19de7ce06088478f1a39dae5a246d701a8
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="xamarinios-errors"></a>Xamarin.iOS 錯誤


## <a name="mt0xxx-mtouch-error-messages"></a>MT0xxx: mtouch 錯誤訊息

例如： 參數、 遺漏工具的環境。

<!--
 MT0xxx mtouch itself, e.g. parameters, environment (e.g. missing tools)
 https://github.com/xamarin/xamarin-macios/blob/master/tools/mtouch/error.cs
    -->

### <a name="a-namemt0000mt0000-unexpected-error---please-fill-a-bug-report-at-httpbugzillaxamarincom"></a><a name="MT0000"/>MT0000： 未預期的錯誤-請填寫 錯誤報告在 http://bugzilla.xamarin.com

發生未預期的錯誤狀況。 請[提出問題報告](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)盡可能最多資訊，包括：

* 完整建置記錄檔，具有最大值的詳細資訊 (例如`-v -v -v -v`中**其他 mtouch 引數**);
* 重現錯誤; 最小測試案例和
* 所有的版本資訊

若要取得正確的版本資訊的最簡單方式是使用**Visual Studio for Mac**功能表上，**有關 Visual Studio for Mac**項目，**顯示詳細資料**按鈕和複製/貼上版本資訊 (您可以使用**複製資訊**按鈕)。

### <a name="a-namemt0001mt0001--devname-was-provided-without-any-device-specific-action"></a><a name="MT0001"/>MT0001: '-devname' 並無任何裝置的特定動作提供

這是如果-devname 傳遞至 mtouch 任何裝置的特定動作時，就會發出警告 (層 logdev /-installdev /-killdev /-launchdev /-listapps) 要求。

### <a name="a-namemt0002mt0002-could-not-parse-the-environment-variable-"></a><a name="MT0002"/>MT0002： 無法剖析的環境變數 *。

如果您嘗試設定無效的環境索引鍵，就會發生此錯誤 = 組變數值。 正確的格式如下： `mtouch --setenv=VARIABLE=VALUE`

### <a name="a-namemt0003mt0003-application-name-exe-conflicts-with-an-sdk-or-product-assembly-dll-name"></a><a name="MT0003"/>MT0003： 應用程式名稱 '*.exe' 與 SDK 或產品的組件 (.dll) 名稱相衝突。

可執行組件名稱和應用程式的名稱不符合應用程式中的任何 dll 的名稱。 請修改可執行檔的名稱。

### <a name="a-namemt0004mt0004-new-refcounting-logic-requires-sgen-to-be-enabled-too"></a><a name="MT0004"/>MT0004： 新 refcounting 邏輯需要 SGen 太啟用。

如果您啟用 refcounting 延伸模組也必須啟用 SGen 記憶體回收行程，在專案的 iOS 建置選項 （進階索引標籤） 中。

消除開頭 Xamarin.iOS 7.2.1 這項需求，可以啟用新的 refcounting 邏輯 Boehm 和 SGen 記憶體回收行程。

### <a name="a-namemt0005mt0005-the-output-directory--does-not-exist"></a><a name="MT0005"/>MT0005： 輸出目錄 * 不存在。

請建立目錄。

不會再產生這個錯誤，mtouch 就會自動建立目錄不存在。

### <a name="a-namemt0006mt0006-there-is-no-devel-platform-at--use---platformplat-to-specify-the-sdk"></a><a name="MT0006"/>MT0006： 沒有任何對內平台 *，使用--平台 = P 指定 SDK。

Xamarin.iOS 找不到錯誤訊息中所述的位置上的 SDK 目錄。 請確認路徑正確。

### <a name="a-namemt0007mt0007-the-root-assembly--does-not-exist"></a><a name="MT0007"/>MT0007： 根組件 * 不存在。

Xamarin.iOS 找不到組件在錯誤訊息中所述的位置。 請確認路徑正確。

### <a name="a-namemt0008mt0008-you-should-provide-one-root-assembly-only-found--assemblies-"></a><a name="MT0008"/>MT0008： 您應該提供一個根組件，找到 # 組件: *。

多個根組件傳遞給 mtouch，雖然可以有只有一個根組件。

### <a name="a-namemt0009mt0009-error-while-loading-assemblies-"></a><a name="MT0009"/>MT0009： 載入組件時發生錯誤: *。

載入的組件的根組件的參考時發生錯誤。 組建輸出中，可能會提供詳細資訊。

### <a name="a-namemt0010mt0010-could-not-parse-the-command-line-arguments-"></a><a name="MT0010"/>MT0010： 無法剖析命令列引數: *。

剖析命令列引數時發生錯誤。 請確認它們全都正確。

### <a name="a-namemt0011mt0011--was-built-against-a-more-recent-runtime--than-monotouch-supports"></a><a name="MT0011"/>MT0011: * 高於 MonoTouch 支援已針對較新的執行階段 （*） 而建置。

因為專案使用 Xamarin.iOS BCL 不建立類別庫的參考，通常會報告這個警告。

使用.NET 4.0 SDK 的應用程式可能無法運作，只支援.NET 2.0 中，在系統的相同方式使用.NET 4.0 建立的程式庫可能不適用於 Xamarin.iOS，它可以使用不存在的 API Xamarin.iOS 上。

一般的解決方法是建置 Xamarin.iOS 類別庫的文件庫。 這可藉由建立新的 Xamarin.iOS 類別庫專案，並加入所有原始程式檔。 如果您沒有程式庫的原始程式碼，您應該連絡廠商，並要求他們提供其程式庫的 Xamarin.iOS 相容版本。

### <a name="a-namemt0012mt0012-incomplete-data-is-provided-to-complete-"></a><a name="MT0012"/>MT0012： 不完整的資料提供給完成 *。

此錯誤不會在目前版本中的 Xamarin.iOS 不再報告。

### <a name="a-namemt0013mt0013-profiling-support-requires-sgen-to-be-enabled-too"></a><a name="MT0013"/>MT0013： 設定檔作業支援需要 sgen 太啟用。

SGen (-sgen)，程式碼剖析，則必須啟用 (-程式碼剖析) 已啟用。

### <a name="a-namemt0014mt0014-the-ios--sdk-does-not-support-building-applications-targeting-"></a><a name="MT0014"/>MT0014: IOS * SDK 不支援為目標的建置應用程式 *。

這會發生在下列情況：

*  ARMv6 已啟用，且已安裝 Xcode 4.5 或更新版本。
*  ARMv7s 已啟用，且已安裝 Xcode 4.4 或更早版本。


請確認已安裝的 Xcode 版本支援選取的架構。

### <a name="a-namemt0015mt0015-invalid-abi--supported-abis-are-i386-x8664--armv7-armv7llvm-armv7llvmthumb2-armv7s-armv7sllvm-armv7sllvmthumb2-arm64-and-arm64llvm"></a><a name="MT0015"/>MT0015： 無效的 ABI: *。 會支援的 ABIs: i386、 x86_64、 armv7，armv7 + llvm、 armv7 + llvm + thumb2、 armv7s、 armv7s + llvm、 armv7s + llvm + thumb2、 arm64 和 arm64 + llvm。

無效的 ABI 傳遞給 mtouch。 請指定有效的 ABI。

### <a name="a-namemt0016mt0016-the-option--has-been-deprecated"></a><a name="MT0016"/>MT0016： 選項 * 已被取代。

提及的 mtouch 選項已被取代，且會被忽略。

### <a name="a-namemt0017mt0017-you-should-provide-a-root-assembly"></a><a name="MT0017"/>MT0017： 您應提供根組件。

需要指定根組件 （通常是主要可執行檔） 建置應用程式時。

### <a name="a-namemt0018mt0018-unknown-command-line-argument-"></a><a name="MT0018"/>MT0018： 未知的命令列引數: *。

Mtouch 無法辨識的錯誤訊息中所述的命令列引數。

### <a name="a-namemt0019mt0019-only-one---loginstallkilllaunchdev-or---launchdebugsim-option-can-be-used"></a><a name="MT0019"/>MT0019: 只有一個-[記錄檔 | 安裝 | kill | 啟動] 開發人員或--[啟動 | 偵錯] sim 選項才能使用。

有幾個選項不能同時使用的 mtouch:

-  --logdev
-  --installdev
-  --killdev
-  -launchdev
-  -launchdebug
-  -launchsim




### <a name="a-namemt0020mt0020-the-valid-options-for--are-"></a><a name="MT0020"/>MT0020 的有效選項 '\*'是'\*'。



### <a name="a-namemt0021mt0021-cannot-compile-using-gccg---use-gcc-when-using-the-static-registrar-this-is-the-default-when-compiling-for-device-either-remove-the---use-gcc-flag-or-use-the-dynamic-registrar---registrardynamic"></a><a name="MT0021"/>使用 gcc MT0021 無法編譯 / g + + (-使用 gcc) 時使用靜態的註冊機構 （的裝置進行編譯時，這是預設值）。 請移除--使用 gcc 旗標，或使用動態註冊機構 (-註冊機構： 動態)。



### <a name="a-namemt0022mt0022-the-options---unsupported--enable-generics-in-registrar-and---registrar-are-not-compatible"></a><a name="MT0022"/>MT0022 選項 '-不支援-啟用-泛型-中的註冊機構' 和 '-註冊機構 ' 不相容。

移除這兩個選項`--unsupported--enable-generics-in-registrar`和`--registrar`。 從預設的註冊機構開始 Xamarin.iOS 7.2.1 支援泛型。

不會再顯示此錯誤 (命令列引數`--unsupported--enable-generics-in-registrar`已經移除了 mtouch)。

### <a name="a-namemt0023mt0023-application-name-exe-conflicts-with-another-user-assembly"></a><a name="MT0023"/>MT0023 應用程式名稱 '*.exe' 與另一個使用者組件發生衝突。

可執行組件名稱和應用程式的名稱不符合應用程式中的任何 dll 的名稱。 請修改可執行檔的名稱。

### <a name="a-namemt0024mt0024-could-not-find-required-file-"></a><a name="MT0024"/>MT0024 找不到必要的檔案 ' *'。



### <a name="a-namemt0025mt0025-no-sdk-version-was-provided-please-add---sdkxy-to-specify-which-ios-sdk-should-be-used-to-build-your-application"></a><a name="MT0025"/>提供 MT0025 否 SDK 版本。 請新增`--sdk=X.Y`來指定哪些 iOS SDK 應該用來建置應用程式。



### <a name="a-namemt0026mt0026-could-not-parse-the-command-line-argument--"></a><a name="MT0026"/>MT0026 無法剖析命令列引數 ' *': *



### <a name="a-namemt0027mt0027-the-options--and--are-not-compatible"></a><a name="MT0027"/>MT0027 選項\*'和'\*' 不相容。



### <a name="a-namemt0028mt0028-cannot-enable-pie--pie-when-targeting-ios-41-or-earlier-please-disable-pie--piefalse-or-set-the-deployment-target-to-at-least-ios-42"></a><a name="MT0028"/>MT0028 無法啟用圓形圖 (-圓形圖) 為目標 iOS 4.1 或更早版本時。 請停用圓形圖 (-圓形圖： false) 或至少將部署目標設定為 iOS 4.2



### <a name="a-namemt0029mt0029-repl---enable-repl-is-only-supported-in-the-simulator---sim"></a><a name="MT0029"/>MT0029: REPL (-啟用 repl) 僅適用於在模擬器 (-sim)。

如果您要建置適用於模擬器僅支援複寫。 這表示，如果您傳遞`--enable-repl`至 mtouch，您還必須傳遞`--sim`。

### <a name="a-namemt0030mt0030-the-executable-name--and-the-app-name--are-different-this-may-prevent-crash-logs-from-getting-symbolicated-properly"></a><a name="MT0030"/>MT0030： 檔名 (\*) 和應用程式名稱 (\*) 都不同，這可能導致損毀記錄檔無法取得正確 symbolicated。

當 Xcode symbolicates （轉換函式名稱和檔案行數字的記憶體位址） 的可執行檔和應用程式需要不同的名稱 （不含副檔名） 時，程序可能失敗。

若要修正此變更 '應用程式名稱' 在專案的組建/iOS 應用程式選項或變更 '組件名稱' 專案的組建/輸出 選項。

### <a name="a-namemt0031mt0031-the-command-line-arguments---enable-background-fetch-and---launch-for-background-fetch-require---launchsim-too"></a><a name="MT0031"/>MT0031: 命令列引數 '-啟用背景擷取 '和 '-啟動的背景擷取' 需要 '-launchsim' 太。

### <a name="a-namemt0032mt0032-the-option---debugtrack-is-ignored-unless---debug-is-also-specified"></a><a name="MT0032"/>MT0032： 選項 '-debugtrack' 會被忽略，除非 '-偵錯 ' 同時指定。

### <a name="a-namemt0033mt0033--a-xamarinios-project-must-reference-either-monotouchdll-or-xamariniosdll"></a><a name="MT0033"/>MT0033 Xamarin.iOS 專案必須參考 monotouch.dll 或 Xamarin.iOS.dll

### <a name="a-namemt0034mt0034--cannot-include-both-monotouchdll-and-xamariniosdll-in-the-same-xamarinios-project----is-referenced-explicitly-while--is-referenced-by-"></a><a name="MT0034"/>MT0034 不能包含 'monotouch.dll' 和 'Xamarin.iOS.dll' 中相同的 Xamarin.iOS 專案-'\*' 會明確參考，而 '\*' 正由 ' *'。

<!-- MT0035 unused -->

### <a name="a-namemt0036mt0036--cannot-launch-a--simulator-for-a--app-please-enable-the-correct-architectures-in-your-projects-ios-build-options-advanced-page"></a><a name="MT0036"/>MT0036 無法啟動 * 模擬器 * 應用程式。 請啟用正確的 architecture(s) 中專案的 iOS 組建選項 （進階頁面）。

### <a name="a-namemt0037mt0037--monotouchdll-is-not-64-bit-compatible-either-reference-xamariniosdll-or-do-not-build-for-a-64-bit-architecture-arm64-andor-x8664"></a><a name="MT0037"/>MT0037 monotouch.dll 與 64 位元不相容。 請參考 Xamarin.iOS.dll，或針對 64 位元的架構 （ARM64 和/或 x86_64） 建立。

### <a name="a-namemt0038mt0038--the-old-registrars---registraroldstaticolddynamic-are-not-supported-when-referencing-xamariniosdll"></a><a name="MT0038"/>MT0038 舊註冊機構 (-註冊機構： oldstatic | olddynamic) 不支援參考 Xamarin.iOS.dll 時。

### <a name="a-namemt0039mt0039--applications-targeting-armv6-cannot-reference-xamariniosdll"></a><a name="MT0039"/>為目標 ARMv6 MT0039 應用程式不能參考 Xamarin.iOS.dll。

### <a name="a-namemt0040mt0040--could-not-find-the-assembly--referenced-by-"></a><a name="MT0040"/>MT0040 找不到組件 '\*'，參考的'\*'。

### <a name="a-namemt0041mt0041--cannot-reference-both-monotouchdll-and-xamariniosdll"></a><a name="MT0041"/>MT0041 無法參考 'monotouch.dll' 和 'Xamarin.iOS.dll'。

### <a name="a-namemt0042mt0042--no-reference-to-either-monotouchdll-or-xamariniosdll-was-found-a-reference-to-monotouchdll-will-be-added"></a><a name="MT0042"/>找不到 monotouch.dll 或 Xamarin.iOS.dll MT0042 否參考。 將加入 monotouch.dll 的參考。

### <a name="a-namemt0043mt0043-the-boehm-garbage-collector-is-currently-not-supported-when-referencing-xamariniosdll-the-sgen-garbage-collector-has-been-selected-instead"></a><a name="MT0043"/>MT0043: Boehm 記憶體回收行程目前不支援參考 'Xamarin.iOS.dll' 時。 SGen 記憶體回收行程已改為選取。

整合專案僅支援只 SGen 記憶體回收行程。 請確定有指定記憶體回收行程 Boehm 沒有其他 mtouch 旗標。

### <a name="a-namemt0044mt0044---listsim-is-only-supported-with-xcode-60-or-later"></a><a name="MT0044"/>MT0044: Xcode 6.0 或更新版本才支援-listsim。

安裝 Xcode 較新版本。

### <a name="a-namemt0045mt0045---extension-is-only-supported-when-using-the-ios-80-or-later-sdk"></a><a name="MT0045"/>MT0045:-擴充功能，才支援使用 iOS 8.0 （含） 以後的 SDK。

<!-- MT0046 is not reported anymore -->

### <a name="a-namemt0047mt0047-the-minimum-deployment-target-for-unified-applications-is-511-the-current-deployment-target-is--please-select-a-newer-deployment-target-in-your-projects-ios-application-options"></a><a name="MT0047"/>MT0047： 整合應用程式的最小的部署目標是 5.1.1，目前的部署目標是 ' *'。 請選擇較新的部署目標中專案的 iOS 應用程式選項。

<!-- MT0048 is not reported anymore -->

### <a name="a-namemt0049mt0049-framework-is-supported-only-if-deployment-target-is-80-or-later--features-might-not-work-correctly"></a><a name="MT0049"/>MT0049: *.framework 僅支援部署目標是 8.0 或更新版本。 * 功能可能無法正確運作。

在部署目標是指的 iOS 版本中不支援指定的架構。 部署目標更新為較新的 iOS 版本，或是從應用程式中移除指定的架構的使用方式。

<!-- MT0050 is not reported anymore -->

### <a name="a-namemt0051mt0051-xamarinios--requires-xcode-50-or-later-the-current-xcode-version-found-in--is-"></a><a name="MT0051"/>MT0051: Xamarin.iOS * 需要 Xcode 5.0 或更新版本。 目前的 Xcode 版本 (在中找到 *) 是 *。

安裝更新版本 Xcode。

### <a name="a-namemt0052mt0052-no-command-specified"></a><a name="MT0052"/>MT0052： 未指定的命令。

Mtouch 未不指定任何動作。

<!-- 0053 is used by mmp -->

### <a name="a-namemt0054mt0054-unable-to-canonicalize-the-path--"></a><a name="MT0054"/>MT0054： 無法規範化路徑 ' *': *

這是內部錯誤。 如果您看到此錯誤，請將 bug 歸檔[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

### <a name="a-namemt0055mt0055-the-xcode-path--does-not-exist"></a><a name="MT0055"/>MT0055: Xcode 路徑 ' *' 不存在。

使用 Xcode 路徑傳遞`--sdkroot`不存在。 請指定有效的路徑。

### <a name="a-namemt0056mt0056-cannot-find-xcode-in-the-default-location-applicationsxcodeapp-please-install-xcode-or-pass-a-custom-path-using---sdkroot-path"></a><a name="MT0056"/>MT0056： 在預設位置找不到 Xcode (/ Applications/Xcode.app)。 請安裝 Xcode，或將自訂路徑使用-sdkroot 傳遞<path>。
### <a name="a-namemt0057mt0057-cannot-determine-the-path-to-xcodeapp-from-the-sdk-root--please-specify-the-full-path-to-the-xcodeapp-bundle"></a><a name="MT0057"/>MT0057： 無法判定要 Xcode.app 路徑從 sdk 根 ' *'。 請指定 Xcode.app 組合的完整路徑。

使用傳遞路徑`--sdkroot`未指定有效的 Xcode 應用程式。 請指定 Xcode 應用程式的路徑。

### <a name="a-namemt0058mt0058-the-xcodeapp--is-invalid-the-file--does-not-exist"></a><a name="MT0058"/>MT0058: Xcode.app '\*' 無效 (檔案 '\*' 不存在)。

使用傳遞路徑`--sdkroot`未指定有效的 Xcode 應用程式。 請指定 Xcode 應用程式的路徑。

### <a name="a-namemt0059mt0059-could-not-find-the-currently-selected-xcode-on-the-system-"></a><a name="MT0059"/>MT0059： 找不到系統上的目前選取的 Xcode: *

### <a name="a-namemt0060mt0060-could-not-find-the-currently-selected-xcode-on-the-system-xcode-select---print-path-returned--but-that-directory-does-not-exist"></a><a name="MT0060"/>MT0060： 在系統上找不到目前所選的 Xcode。 'xcode 選取-列印路徑' 傳回 ' *'，但該目錄不存在。

### <a name="a-namemt0061mt0061-no-xcodeapp-specified-using---sdkroot-using-the-system-xcode-as-reported-by-xcode-select---print-path-"></a><a name="MT0061"/>MT0061： 指定 （使用-sdkroot） 沒有 Xcode.app 所 'xcode-選取-列印路徑' 回報使用系統 Xcode: *

此為參考用的警告，說明 Xcode 會因為指定的任何使用。

### <a name="a-namemt0062mt0062-no-xcodeapp-specified-using---sdkroot-or-xcode-select---print-path-using-the-default-xcode-instead-"></a><a name="MT0062"/>MT0062： 指定 （使用-sdkroot 或 ' xcode-選取-列印路徑 '），改為使用預設 Xcode 沒有 Xcode.app: *

此為參考用的警告，說明 Xcode 會因為指定的任何使用。

### <a name="a-namemt0063mt0063-cannot-find-the-executable-in-the-extension--no-cfbundleexecutable-entry-in-its-infoplist"></a><a name="MT0063"/>MT0063： 延伸模組中找不到可執行檔 * （沒有 CFBundleExecutable 的項目在其 Info.plist 中）

每個 Info.plist 必須已經有可執行檔 （使用 CFBundleExecutable 項目），不過應該自動產生的項目，在建置期間。

這通常表示 Xamarin.iOS; 中的 bug請檔案錯誤報告在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)與測試案例。

### <a name="a-namemt0064mt0064-xamarinios-only-supports-embedded-frameworks-with-unified-projects"></a><a name="MT0064"/>MT0064: Xamarin.iOS 只支援內嵌的架構來使用整合專案。

Xamarin.iOS 只支援內嵌的架構時使用統一的 API。請更新專案，以使用統一的 API。

### <a name="a-namemt0065mt0065-xamarinios-only-supports-embedded-frameworks-when-deployment-target-is-at-least-80-current-deployment-target--embedded-frameworks-"></a><a name="MT0065"/>MT0065: Xamarin.iOS 只支援內嵌的架構時部署目標是至少 8.0 (目前的部署目標: * 內嵌架構: *)

Xamarin.iOS 部署目標是至少 8.0 （因為較早版本的 iOS 不支援內嵌的架構） 時，只支援內嵌的架構。

請將更新部署中的目標專案的 Info.plist 為 8.0 或更新版本。

### <a name="a-namemt0066mt0066-invalid-build-registrar-assembly-"></a><a name="MT0066"/>MT0066： 無效的組建註冊機構組件: *

這通常表示 Xamarin.iOS; 中的 bug請檔案錯誤報告在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)與測試案例。

### <a name="a-namemt0067mt0067-invalid-registrar-"></a><a name="MT0067"/>MT0067： 無效的註冊機構: *

這通常表示 Xamarin.iOS; 中的 bug請檔案錯誤報告在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)與測試案例。

### <a name="a-namemt0068mt0068-invalid-value-for-target-framework-"></a><a name="MT0068"/>MT0068： 目標架構的值無效: *。

使用傳遞無效的目標 framework--目標 framework 引數。 請指定有效的目標 framework。

<!--### <a name="MT0069"/>MT0069: currently unused -->

### <a name="a-namemt0070mt0070-invalid-target-framework--valid-target-frameworks-are-"></a><a name="MT0070"/>MT0070： 無效的目標 framework: *。 有效的目標架構是: *。

使用傳遞無效的目標 framework--目標 framework 引數。 請指定有效的目標 framework。

### <a name="a-namemt0071mt0071-unknown-platform--this-usually-indicates-a-bug-in-xamarinios-please-file-a-bug-report-at-httpbugzillaxamarincom-with-a-test-case"></a><a name="MT0071"/>MT0071： 未知的平台: *。 這通常表示 Xamarin.iOS; 中的 bug請在與測試案例 http://bugzilla.xamarin.com 檔案錯誤報告。

這通常表示 Xamarin.iOS; 中的 bug請檔案錯誤報告在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)與測試案例。

### <a name="a-namemt0072mt0072-extensions-are-not-supported-for-the-platform-"></a><a name="MT0072"/>MT0072： 平台不支援擴充功能 ' *'。

這通常表示 Xamarin.iOS; 中的 bug請檔案錯誤報告在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)與測試案例。

### <a name="a-namemt0073mt0073-xamarinios--does-not-support-a-deployment-target-of--the-minimum-is--please-select-a-newer-deployment-target-in-your-projects-infoplist"></a><a name="MT0073"/>MT0073: Xamarin.iOS * 不支援的部署目標 * (最小值是 *)。 請在您的專案 Info.plist 中，選取較新的部署目標。

最小的部署目標是所指定的錯誤訊息。請在專案的 Info.plist 中，選取較新的部署目標。

如果無法更新部署目標，則請使用較舊版本的 Xamarin.iOS。

### <a name="a-namemt0074mt0074-xamarinios--does-not-support-a-minimum-deployment-target-of--the-maximum-is--please-select-an-older-deployment-target-in-your-projects-infoplist-or-upgrade-to-a-newer-version-of-xamarinios"></a><a name="MT0074"/>MT0074: Xamarin.iOS * 不支援的最小的部署目標 * (最大值是 *)。 請在您的專案 Info.plist 中選取舊的部署目標，或升級至較新版的 Xamarin.iOS。

Xamarin.iOS 不支援將最小的部署目標設定為版本高於已針對建置 Xamarin.iOS 此特定版本的版本。

請在專案的 Info.plist 中選取舊的最小的部署目標，或升級至較新版的 Xamarin.iOS。

### <a name="a-namemt0075mt0075-invalid-architecture--for--projects-valid-architectures-are-"></a><a name="MT0075"/>MT0075： 不正確的結構 ' *' 的 * 專案。 有效的架構: *

指定不正確的結構。 請確認架構有效。

### <a name="a-namemt0076mt0075-no-architecture-specified-using-the---abi-argument-an-architecture-is-required-for--projects"></a><a name="MT0076"/>MT0075： 沒有指定 （使用-abi 引數） 的架構。 架構是為了 * 專案。

這通常表示 Xamarin.iOS; 中的 bug請檔案錯誤報告在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)與測試案例。

### <a name="a-namemt0077mt0076-watchos-projects-must-be-extensions"></a><a name="MT0077"/>MT0076: WatchOS 專案必須是擴充功能。

這通常表示 Xamarin.iOS; 中的 bug請檔案錯誤報告在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)與測試案例。

### <a name="a-namemt0078mt0077-incremental-builds-are-enabled-with-a-deployment-target--80-currently--this-is-not-supported-the-resulting-application-will-not-launch-on-ios-9-so-the-deployment-target-will-be-set-to-80"></a><a name="MT0078"/>MT0077： 啟用累加建置與部署目標 < 8.0 (目前 *)。 這不支援 （產生的應用程式將不會啟動在 iOS 9），因此將會在部署目標設定為 8.0。

這是警告，通知的部署目標已設定為這個組建 8.0，累加建置才能工作正確。

部署目標是至少 8.0 （因為產生的應用程式將不會啟動在 iOS 9 否則） 時，才支援累加建置。

### <a name="a-namemt0079mt0078-the-recommended-xcode-version-for-xamarinios--is-xcode--or-later-the-current-xcode-version-found-in--is-"></a><a name="MT0079"/>MT0078: 建議使用的 Xcode 版本 Xamarin.iOS * 為 Xcode * 或更新版本。 目前的 Xcode 版本 (在中找到 *) 是 *。

這是警告，通知 Xcode 的目前版本不此版本的 Xamarin.iOS Xcode 的建議的版本。

請升級 Xcode 以確保最佳的行為。

### <a name="a-namemt0080mt0080-disabling-newrefcount---new-refcountfalse-is-deprecated"></a><a name="MT0080"/>MT0080： 停用 NewRefCount、--新-refcount:false，已被取代。

這是警告，通知的要求停用新參考計數 (-新增-refcount:false) 已被忽略。

新的參考計數 」 功能是必要的所有專案，而且因此不可以再停用。

### <a name="a-namemt0081mt0081-the-command-line-argument---download-crash-report-also-requires---download-crash-report-to"></a><a name="MT0081"/>MT0081： 命令列引數--下載當機報告也需要-下載損毀-報表-對。
### <a name="a-namemt0082mt0082-repl---enable-repl-is-only-supported-when-linking-is-not-used---nolink"></a><a name="MT0082"/>MT0082: REPL (-啟用 repl) 不使用連結時，才支援 (-nolink)。
### <a name="a-namemt0083mt0083-asm-only-bitcode-is-not-supported-on-watchos-use-either---bitcodemarker-or---bitcodefull"></a><a name="MT0083"/>MT0083: WatchOS 不支援僅限 Asm bitcode。 使用任一-bitcode： 標記或-bitcode： 完整。
### <a name="a-namemt0084mt0084-bitcode-is-not-supported-in-the-simulator-do-not-pass---bitcode-when-building-for-the-simulator"></a><a name="MT0084"/>MT0084: Bitcode 不支援在模擬器中。 建置適用於模擬器時，請勿傳送-bitcode。
### <a name="a-namemt0085mt0085-no-reference-to--was-found-it-will-be-added-automatically"></a><a name="MT0085"/>MT0085： 沒有參考 ' *' 找不到。 它會自動加入。
### <a name="a-namemt0086mt0086-a-target-framework---target-framework-must-be-specified-when-building-for-tvos-or-watchos"></a><a name="MT0086"/>MT0086: 的目標 framework (-目標架構) 為 TVOS 或 WatchOS 建置時，必須指定。

這通常表示 Xamarin.iOS; 中的 bug請檔案錯誤報告在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)與測試案例。

### <a name="a-namemt0087mt0087-incremental-builds---fastdev-is-not-supported-with-the-boehm-gc-incremental-builds-will-be-disabled"></a><a name="MT0087"/>MT0087： 累加建置 (-fastdev) 不支援 Boehm GC。 將停用累加建置。

### <a name="a-namemt0088mt0088-the-gc-must-be-in-cooperative-mode-for-watchos-apps-please-remove-the---coopfalse-argument-to-mtouch"></a><a name="MT0088"/>MT0088： 合作式模式 watchOS 應用程式必須是 GC。 請移除 mtouch--coop: false 引數。

### <a name="a-namemt0089mt0089-the-option--cannot-take-the-value--when-cooperative-mode-is-enabled-for-the-gc"></a><a name="MT0089"/>MT0089： 選項 '\*'無法採用的值'\*' GC 啟用合作式模式時。

### <a name="a-namemt0091mt0091-this-version-of-xamarinios-requires-the--sdk-shipped-with-xcode--either-upgrade-xcode-to-get-the-required-header-files-or-set-the-managed-linker-behaviour-to-link-framework-sdks-only-to-try-to-avoid-the-new-apis"></a><a name="MT0091"/>MT0091： 這個版本的 Xamarin.iOS 需要 * SDK (隨附於 Xcode *)。 請升級 Xcode 取得必要的標頭檔，或設為 連結架構 Sdk 只 （若要嘗試避免新的應用程式開發介面） 的受管理的連結器行為。

Xamarin.iOS 需要的標頭檔，從要建置應用程式的錯誤訊息中指定的 SDK 版本。 若要修正此錯誤的建議的方式是升級 Xcode 取得必要的 SDK，這包括所有必要的標頭檔案。 如果您有多個版本 Xcode 安裝，或想要使用 Xcode 在非預設位置，請確定您的 IDE 喜好設定中設定正確的 Xcode 位置。

可能會發生，替代方案是啟用受管理的連結器。 這會移除未使用 API 包括在大部分情況下，新的 API，其中的標頭檔案已遺失 （或不完整）。 不過這將無法運作如果您的專案使用比您 Xcode 的較新的 SDK 中所導入的應用程式開發介面所提供。

上次 straw 方案就是使用 Xamarin.iOS 的較舊的版本，可支援您的專案的 SDK 需要。

<!-- MT0092 used by mlaunch -->

### <a name="a-namemt0093mt0093-could-not-find-mlaunch"></a><a name="MT0093"/>MT0093： 找不到 'mlaunch'。

<!-- MT0094 is not reported anymore -->

### <a name="a-namemt0095mt0095-aot-files-could-not-be-copied-to-the-destination-directory-dest-error"></a><a name="MT0095"/>MT0095: Aot 檔案無法複製到目的地目錄 {d}: {error}

### <a name="a-namemt0096mt0096-no-reference-to-xamariniosdll-was-found"></a><a name="MT0096"/>MT0096: Xamarin.iOS.dll 沒有參考找不到。

<!-- MT0097: used by mmp -->
<!-- MT0098: used by mmp -->

### <a name="a-namemt0099mt0099-internal-error--please-file-a-bug-report-with-a-test-case-httpbugzillaxamarincom"></a><a name="MT0099"/>MT0099： 內部錯誤 *。 請提出問題報告與測試案例 (http://bugzilla.xamarin.com)。

在 Xamarin.iOS 內部一致性檢查失敗時，會報告此錯誤訊息。

這表示 Xamarin.iOS; 中的 bug請檔案錯誤報告在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)與測試案例。

### <a name="a-namemt0100mt0100-invalid-assembly-build-target--please-file-a-bug-report-with-a-test-case-httpbugzillaxamarincom"></a><a name="MT0100"/>MT0100： 無效的組件建置目標: ' *'。 請提出問題報告與測試案例 (http://bugzilla.xamarin.com)。

在 Xamarin.iOS 內部一致性檢查失敗時，會報告此錯誤訊息。

這一定是 Xamarin.iOS; 中的 bug請檔案錯誤報告在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)與測試案例。

### <a name="a-namemt0101mt0101-the-assembly--is-specified-multiple-times-in---assembly-build-target-arguments"></a><a name="MT0101"/>MT0101： 組件 ' *'-組件建置目標引數中指定了多次。

錯誤訊息中所提及的組件指定了多次-組件建置目標引數中。 請確定每個組件僅一次所述。

### <a name="a-namemt0102mt0102-the-assemblies--and--have-the-same-target-name--but-different-targets--and-"></a><a name="MT0102"/>MT0102： 組件\*'和'\*'具有相同的目標名稱 ('\*')，但不同的目標 ('\*' 和 ' *')。

錯誤訊息中所提及的組件具有衝突的建置目標。

例如: 

    --assembly-build-target:Assembly1.dll=framework=MyBinary --assembly-build-target:Assembly2.dll=dynamiclibrary=MyBinary

這個範例嘗試使用相同的廠牌建立動態程式庫和架構 (`MyBinary`)。

### <a name="a-namemt0103mt0103-the-static-object--contains-more-than-one-assembly--but-each-static-object-must-correspond-with-exactly-one-assembly"></a><a name="MT0103"/>MT0103： 靜態物件 '\*' 包含多個組件 ('\*')，但每個靜態物件必須對應於一個組件。

錯誤訊息中所提及的組件所有會編譯成單一的靜態物件。 這不允許，每個組件必須編譯成不同的靜態物件。

例如: 

    --assembly-build-target:Assembly1.dll=staticobject=MyBinary --assembly-build-target:Assembly2.dll=staticobject=MyBinary

這個範例會嘗試建立靜態物件 (`MyBinary`) 兩個組件所組成 (`Assembly1.dll`和`Assembly2.dll`)，這不允許。

### <a name="a-namemt0105mt0105-no-assembly-build-target-was-specified-for-"></a><a name="MT0105"/>MT0105: 沒有組件建置目標已指定 ' *'。

當指定的組件目標使用建置`--assembly-build-target`，應用程式中的每個組件必須有指派的建置目標。

錯誤訊息中所提及的組件沒有建置指派的目標組件時，會報告此錯誤。

請參閱的文件`--assembly-build-target`以取得詳細資訊。

### <a name="a-namemt0106mt0106-the-assembly-build-target-name--is-invalid-the-character--is-not-allowed"></a><a name="MT0106"/>MT0106： 組件建置目標名稱 '\*' 無效： 字元 '\*' 不允許。

組件的建置目標名稱必須是有效的檔名。

例如這些值將會觸發此錯誤：

    --assembly-build-target:Assembly1.dll=staticobject=my/path.o

因為`my/path.o`不是有效的檔名，因為目錄分隔符號字元。

### <a name="a-namemt0107mt0107-the-assemblies--have-different-custom-llvm-optimizations--which-is-not-allowed-when-they-are-all-compiled-to-a-single-binary"></a><a name="MT0107"/>MT0107： 組件\*' 有不同的自訂 LLVM 最佳化 (\*)，這不允許在所有編譯時以單一的二進位檔。

### <a name="a-namemt0108mt0108-the-assembly-build-target--did-not-match-any-assemblies"></a><a name="MT0108"/>MT0108： 組件建置目標 ' *' 不符合任何組件。

### <a name="a-namemt0109mt0109-the-assembly-0-was-loaded-from-a-different-path-than-the-provided-path-provided-path-1-actual-path-2"></a><a name="MT0109"/>MT0109： 這個組件 '{0}' 已從提供的路徑不同的路徑載入 (提供路徑： {1}、 實際路徑： {2})。

這是警告，表示應用程式所參考的組件已載入從不同的位置要求。

這可能表示應用程式正在參考多個組件具有相同的名稱，但來自不同位置，可能會導致未預期的結果 （將會使用第一個組件）。

### <a name="a-namemt0110mt0110-incremental-builds-have-been-disabled-because-this-version-of-xamarinios-does-not-support-incremental-builds-in-projects-that-include-third-party-binding-libraries-and-that-compiles-to-bitcode"></a><a name="MT0110"/>MT0110： 累加建置已停用，因為在專案中，包含第三方繫結的程式庫和可編譯成 bitcode Xamarin.iOS 這個版本不支援累加建置。

累加建置已停用，因為這個版本的 Xamarin.iOS 不支援累加建置在專案中，包含第三方繫結的程式庫和可編譯成 bitcode （tvOS 和 watchOS 專案）。

您不需要任何動作，此訊息僅供參考。

如進一步資訊，請參閱錯誤 #[51710](https://bugzilla.xamarin.com/show_bug.cgi?id=51710)。

不會再報告這個警告。

### <a name="a-namemt0111mt0111-bitcode-has-been-enabled-because-this-version-of-xamarinios-does-not-support-building-watchos-projects-using-llvm-without-enabling-bitcode"></a><a name="MT0111"/>MT0111: Bitcode 已啟用因為 Xamarin.iOS 這個版本不支援建置 watchOS 專案使用 LLVM 不啟用 bitcode。

Bitcode 已啟用自動因為 Xamarin.iOS 這個版本不支援建置 watchOS 專案使用 LLVM 不啟用 bitcode。

您不需要任何動作，此訊息僅供參考。

如進一步資訊，請參閱錯誤 #[51634](https://bugzilla.xamarin.com/show_bug.cgi?id=51634)。

### <a name="a-namemt0112mt0112-native-code-sharing-has-been-disabled-because-"></a><a name="MT0112"/>MT0112： 原生程式碼共用已停用，因為 *

您可以停用程式碼共用的多個原因有：

* 因為容器應用程式的部署目標是早於 iOS 8.0 以上版本 (它有 *))。

這 iOS 8.0 以上版本引進在由於共用原生程式碼使用使用者介面架構，實作，原生程式碼共用需要 iOS 8.0 以上版本。

* 因為容器應用程式包含 I18N 組件 （*）。

目前不支援原生程式碼共用，如果容器應用程式包含 I18N 組件。

* 因為容器應用程式有自訂的 xml 定義的受管理的連結器 （*）。

原生程式碼共用需要不支援受管理的連結器使用自訂 xml 定義的專案。


### <a name="a-namemt0113mt0113-native-code-sharing-has-been-disabled-for-the-extension--because-"></a><a name="MT0113"/>MT0113： 原生程式碼共用已停用擴充功能 ' *' 因為 *。

* 因為 bitcode 選項不同容器應用程式之間 (\*) 和延伸模組 (\*)。

  原生程式碼共用需要 bitcode 選項符合之間共用程式碼的專案。

* 因為--組件建置目標選項都不同，容器應用程式之間 (\*) 和延伸模組 (\*)。

  原生程式碼共用需要的--組件建置目標的選項都是一樣的共用程式碼的專案。

  不啟用或停用所有專案中，如果累加建置可能發生這種狀況。

* 因為不同容器應用程式之間的 I18N 組件 (\*) 和延伸模組 (\*)。

  原生程式碼共用目前不支援包含 I18N 組件的延伸模組。

* 因為容器應用程式之間的 AOT 編譯器的引數截然不同 (\*) 和延伸模組 (\*)。

  原生程式碼共用需要 AOT 編譯器的引數亦無差異之間共用程式碼的專案。

* 因為容器應用程式之間的 AOT 編譯器的其他引數截然不同 (\*) 和延伸模組 (\*)。

  原生程式碼共用需要 AOT 編譯器的引數亦無差異之間共用程式碼的專案。

  如果 '執行所有 32 位元浮點作業與 64 位元浮點數' 不同的專案，就會發生這種狀況。

* 因為未啟用或停用這兩個容器應用程式中 LLVM (\*) 和延伸模組 (\*)。

  原生程式碼共用需要 LLVM 是啟用或停用所有共用程式碼的專案。

* 因為不同容器應用程式之間的受管理的連結器設定 (\*) 和延伸模組 (\*)。

  原生程式碼共用需要受管理的連結器設定是相同的共用程式碼的所有專案。

* 因為不同容器應用程式之間略過的組件的受管理的連結器 (\*) 和延伸模組 (\*)。

  原生程式碼共用需要受管理的連結器設定是相同的共用程式碼的所有專案。

* 因為擴充功能有自訂的 xml 定義的受管理的連結器 （*）。

  原生程式碼共用需要不支援受管理的連結器使用自訂 xml 定義的專案。

* 因為容器應用程式未建置的 ABI * （在此 ABI 建置延伸模組）。

  原生程式碼共用需要容器應用程式建置的任何應用程式擴充功能會針對建立的所有架構。

  例如： 為 ARM64 + ARMv7，建立副檔名為 ARM64 只建立容器應用程式時，就會發生這種狀況。

* 因為容器應用程式所建置的 ABI \*，屬於不相容的擴充功能的 ABI (\*)。

  原生程式碼共用需要的所有專案都建置為相同的 API。

  例如： 副檔名為 ARMv7 + llvm + thumb2，建立容器應用程式只會建置 ARMv7 + llvm 時，就會發生這種狀況。

* 因為容器應用程式參考的組件 '\*'from'\*'，而擴充功能參考不同的版本' *'。

  原生程式碼共用需要的共用程式碼的所有專案都使用相同版本的所有組件。

<!-- MT0114: used by mmp -->

### <a name="a-namemt0115mt0115-it-is-recommended-to-reference-dynamic-symbols-using-code---dynamic-symbol-modecode-when-bitcode-is-enabled"></a><a name="MT0115"/>MT0115： 建議您參考動態使用程式碼的符號 (-動態符號模式 = 程式碼) 時，bitcode 已啟用。

Xamarin.iOS 專案會經常參考原生符號，以動態方式表示原生連結器可能會移除這類的原生符號，在原生連結過程中，因為原生連結器不會看到這些符號可用。

通常 Xamarin.iOS 會詢問要保留這類符號原生連結器 (使用`-u symbol`連結器旗標)，但是當 bitcode 編譯原生連結器不接受`-u`旗標。

Xamarin.iOS 已實作替代方案： 我們產生額外的原生程式碼會參考這些符號，並因此原生連結器將會看到這些符號可用。 當編譯為 bitcode 這是自動完成的。

如果`--dynamic-symbol-mode=linker`傳遞給此替代方案將會停用，及 Xamarin.iOS 嘗試傳遞 mtouch`-u`至原生連結器。 這最有可能會導致原生連結器錯誤。

解決方法是移除`--dynamic-symbol-mode=linker`從專案的組建選項中的其他 mtouch 引數的引數。

<!-- 0116 - 0124: free to use -->

### <a name="a-namemt0125mt0125-the---assembly-build-target-command-line-argument-is-ignored-in-the-simulator"></a><a name="MT0125"/>MT0125:--組件的建置目標在模擬器中的命令列引數會被忽略。

不不需要任何動作，此訊息僅供參考。

### <a name="a-namemt0126mt0126-incremental-builds-have-been-disabled-because-incremental-builds-are-not-supported-in-the-simulator"></a><a name="MT0126"/>MT0126： 累加建置已停用，因為在模擬器中不支援累加建置。

不不需要任何動作，此訊息僅供參考。

### <a name="a-namemt0127mt0127-incremental-builds-have-been-disabled-because-this-version-of-xamarinios-does-not-support-incremental-builds-in-projects-that-include-more-than-one-third-party-binding-libraries"></a><a name="MT0127"/>MT0127： 累加建置已停用，因為這個版本的 Xamarin.iOS 不支援累加建置包含多個的其中一個協力廠商的繫結的程式庫的專案中。

自動擁有停用累加建置，因為這一版的 Xamarin.iOS 不一定具有多個協力廠商的繫結的程式庫的專案正確建置。

不不需要任何動作，此訊息僅供參考。

如進一步資訊，請參閱錯誤 #[52727](https://bugzilla.xamarin.com/show_bug.cgi?id=52727)。

## <a name="mt1xxx-project-related-error-messages"></a>MT1xxx： 專案相關的錯誤訊息

### <a name="mt10xx-installer--mtouch"></a>MT10xx: Installer / mtouch

<!--
 MT1xxx file copy / symlinks (project related)
  MT10xx installer.cs / mtouch.cs
  -->

### <a name="a-namemt1001mt1001-could-not-find-an-application-at-the-specified-directory"></a><a name="MT1001"/>MT1001 找不到應用程式在指定的目錄



### <a name="a-namemt1002mt1002-could-not-create-symlinks-files-were-copied"></a><a name="MT1002"/>MT1002 無法建立符號連結，已複製檔案



### <a name="a-namemt1003mt1003-could-not-kill-the-application--you-may-have-to-kill-the-application-manually"></a><a name="MT1003"/>MT1003 可能不會終止應用程式 ' *'。 您可能必須手動終止應用程式。



### <a name="a-namemt1004mt1004-could-not-get-the-list-of-installed-applications"></a><a name="MT1004"/>MT1004 無法取得已安裝的應用程式的清單。



### <a name="a-namemt1005mt1005-could-not-kill-the-application--on-the-device----you-may-have-to-kill-the-application-manually"></a><a name="MT1005"/>MT1005 可能不會終止應用程式 '\*'上的裝置 」\*': *-您可能必須手動清除應用程式。



### <a name="a-namemt1006mt1006-could-not-install-the-application--on-the-device--"></a><a name="MT1006"/>MT1006 無法安裝應用程式 '\*'上的裝置 」\*': *。



### <a name="a-namemt1007mt1007-failed-to-launch-the-application--on-the-device---you-can-still-launch-the-application-manually-by-tapping-on-it"></a><a name="MT1007"/>無法啟動應用程式 MT1007 '\*'上的裝置 」\*': *。 您仍然可以在其上的點選以手動方式啟動應用程式。



### <a name="a-namemt1008mt1008-failed-to-launch-the-simulator"></a><a name="MT1008"/>MT1008： 無法啟動模擬器



如果 mtouch 無法啟動模擬器時，會報告此錯誤。   這種情形有時因為已經執行的已停用或無作用的模擬器處理序。

在 Unix 命令列上發出下列命令可以用來終止陷入不斷的模擬器處理序：

```bash
$ launchctl list|grep UIKitApplication|awk '{print $3}'|xargs launchctl remove
```

### <a name="a-namemt1009mt1009-could-not-copy-the-assembly--to--"></a><a name="MT1009"/>MT1009 無法複製組件 '\*'to'\*': *

這是 Xamarin.iOS 的特定版本的已知的問題。

如果您發生此問題，請嘗試下列因應措施：

```csharp
sudo chmod 0644 /Library/Frameworks/Xamarin.iOS.framework/Versions/Current/lib/mono/*/*.mdb
```

不過，因為已經 Xamarin.iOS 的最新版本中解決此問題，請將新 bug 歸檔在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)完整版本資訊與組建記錄檔輸出。

### <a name="a-namemt1010mt1010-could-not-load-the-assembly--"></a><a name="MT1010"/>MT1010 無法載入組件 ' *': *



### <a name="a-namemt1011mt1011-could-not-add-missing-resource-file-"></a><a name="MT1011"/>MT1011 無法加入遺漏的資源檔: ' *'



### <a name="a-namemt1012mt1012-failed-to-list-the-apps-on-the-device--"></a><a name="MT1012"/>列出在裝置上的應用程式失敗 MT1012 ' *': *



### <a name="a-namemt1013mt1013-dependency-tracking-error-no-files-to-compare-please-file-a-bug-report-at-httpbugzillaxamarincom-with-a-test-case"></a><a name="MT1013"/>MT1013 相依性追蹤時發生錯誤： 沒有要比較的檔案。 請在與測試案例 http://bugzilla.xamarin.com 檔案錯誤報告。

這表示 Xamarin.iOS 中的 bug。 請將 bug 歸檔在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)與測試 caes。

### <a name="a-namemt1014mt1014-failed-to-re-use-cached-version-of--"></a><a name="MT1014"/>無法重複使用的快取的版本 MT1014 ' *': *。



### <a name="a-namemt1015mt1015--failed-to-create-the-executable--"></a><a name="MT1015"/>無法建立可執行檔 MT1015 ' *': *

### <a name="a-namemt1015mt1015--failed-to-create-the-executable--"></a><a name="MT1015"/>無法建立可執行檔 MT1015 ' *': *

### <a name="a-namemt1016mt1016-failed-to-create-the-notice-file-because-a-directory-already-exists-with-the-same-name"></a><a name="MT1016"/>MT1016： 無法建立通知檔案，因為目錄已存在具有相同名稱。

移除目錄`NOTICE`從專案。

### <a name="a-namemt1017mt1017-failed-to-create-the-notice-file-"></a><a name="MT1017"/>MT1017： 無法建立通知檔案: *。

### <a name="a-namemt1018mt1018-your-application-failed-code-signing-checks-and-could-not-be-installed-on-the-device--check-your-certificates-provisioning-profiles-and-bundle-ids-probably-your-device-is-not-part-of-the-selected-provisioning-profile-error-0xe8008015"></a><a name="MT1018"/>MT1018： 應用程式失敗，程式碼簽章檢查和無法安裝在裝置上 ' *'。 請檢查您的憑證、 佈建設定檔，並組合識別碼。 可能是您的裝置不屬於所選的佈建設定檔 (錯誤： 0xe8008015)。
### <a name="a-namemt1019mt1019-your-application-has-entitlements-not-supported-by-your-current-provisioning-profile-and-could-not-be-installed-on-the-device--please-check-the-ios-device-log-for-more-detailed-information-error-0xe8008016"></a><a name="MT1019"/>MT1019： 應用程式使用具有不支援目前的佈建設定檔的權利和無法安裝在裝置上 ' *'。 如需詳細資訊，請檢查 iOS 裝置記錄 (錯誤： 0xe8008016)。

這種情況：

* 您的應用程式使用具有不支援目前的佈建設定檔的權利。
  可能的解決方案：
  - 指定不同的佈建設定檔支援權利應用程式的需求。
  - 移除目前的佈建設定檔中不支援的權利。
* 您正嘗試將部署到不在您使用的佈建設定檔中的裝置。
  可能的解決方案：
  - 在 Xcode 中的範本建立新的應用程式，選取相同的佈建設定檔，並部署至相同的裝置。 有時 Xcode 可以自動重新整理佈建設定檔 （在其他情況下，Xcode 會詢問您要執行的工作） 的新裝置。
  -移至 iOS 開發人員中心和佈建設定檔更新與新的裝置，然後更新的佈建設定檔下載到電腦。


在 iOS 裝置記錄檔將會顯示有關失敗的詳細資訊的大部分情況下，這可以幫助診斷問題。

### <a name="a-namemt1020mt1020-failed-to-launch-the-application--on-the-device--"></a><a name="MT1020"/>MT1020： 無法啟動應用程式 '\*'上的裝置 」\*': *

### <a name="a-namemt1021mt1021-could-not-copy-the-file--to--2"></a><a name="MT1021"/>MT1021： 無法將檔案複製 '\*'to'\*': {2}

無法複製檔案。 從複製作業的錯誤訊息有錯誤的詳細資訊。

### <a name="a-namemt1022mt1022-could-not-copy-the-directory--to--2"></a><a name="MT1022"/>MT1022： 無法複製目錄 '\*'to'\*': {2}

無法複製目錄。 從複製作業的錯誤訊息有錯誤的詳細資訊。

### <a name="a-namemt1023mt1023-could-not-communicate-with-the-device-to-find-the-application---"></a><a name="MT1023"/>MT1023: 無法與找到的應用程式裝置通訊 ' *': *

嘗試將查閱裝置上的應用程式時，就會發生錯誤。

若要嘗試解決這個問題的項目：

* 從裝置刪除應用程式並再試一次。
* 中斷連線的裝置，然後重新連接它。
* 重新啟動裝置。
* 重新啟動 mac

### <a name="a-namemt1024mt1024-the-application-signature-could-not-be-verified-on-device--please-make-sure-that-the-provisioning-profile-is-installed-and-not-expired-error-0xe8008017"></a><a name="MT1024"/>MT1024： 應用程式簽章無法驗證裝置上 ' *'。 請確定佈建設定檔已安裝且未過期 (錯誤： 0xe8008017)。

裝置會拒絕應用程式進行安裝，因為無法驗證簽章。

請先確定佈建設定檔已安裝且未過期。

### <a name="a-namemt1025mt1025-could-not-list-the-crash-reports-on-the-device-"></a><a name="MT1025"/>MT1025： 無法列出在裝置上的當機報告 *。

嘗試將列出在裝置上的當機報告時，就會發生錯誤。

若要嘗試解決這個問題的項目：

* 從裝置刪除應用程式並再試一次。
* 中斷連線的裝置，然後重新連接它。
* 重新啟動裝置。
* 重新啟動 mac
* 同步處理裝置與 iTunes （這會從裝置移除任何損毀報表）。

### <a name="a-namemt1026mt1026-could-not-download-the-crash-report--from-the-device-"></a><a name="MT1026"/>MT1026： 無法下載的當機報告 * 從裝置 *。

嘗試從裝置下載當機報告時，就會發生錯誤。

若要嘗試解決這個問題的項目：

* 從裝置刪除應用程式並再試一次。
* 中斷連線的裝置，然後重新連接它。
* 重新啟動裝置。
* 重新啟動 mac
* 同步處理裝置與 iTunes （這會從裝置移除任何損毀報表）。

### <a name="a-namemt1027mt1027-cant-use-xcode-7-to-launch-applications-on-devices-with-ios--xcode-7-only-supports-ios-6"></a><a name="MT1027"/>MT1027： 無法啟動應用程式使用 iOS 裝置上的使用 Xcode 7 + * （Xcode 7 僅支援 iOS 6 +）。

您不可能使用 Xcode 7 + 啟動裝置與 iOS 6.0 下方的版本上的應用程式。

請使用較舊版本的 Xcode，或點選應用程式以手動方式啟動它。

### <a name="a-namemt1028mt1028-invalid-device-specification--expected-ios-watchos-or-all"></a><a name="MT1028"/>MT1028： 無效的裝置規格: ' *'。 預期的 'ios'、 'watchos' 或者 'all'。

使用此項目-傳遞裝置規格的裝置不正確。 有效值為: 'ios'、 'watchos' 或 'all'。

### <a name="a-namemt1029mt1029-could-not-find-an-application-at-the-specified-directory-"></a><a name="MT1029"/>MT1029： 找不到應用程式，在指定的目錄: *

傳遞給-launchdev 應用程式路徑不存在。 請指定有效的應用程式套件組合。

### <a name="a-namemt1030mt1030-launching-applications-on-device-using-a-bundle-identifier-is-deprecated-please-pass-the-full-path-to-the-bundle-to-launch"></a><a name="MT1030"/>MT1030： 啟動應用程式使用的套件組合識別碼的裝置上已被取代。 請將完整路徑傳遞至要啟動的組合。

建議您將路徑傳遞至應用程式，而不是套件組合識別碼的裝置上啟動。

### <a name="a-namemt1031mt1031-could-not-launch-the-app--on-the-device--because-the-device-is-locked-please-unlock-the-device-and-try-again"></a><a name="MT1031"/>MT1031： 無法啟動應用程式 '\*'上的裝置 」\*' 因為裝置已被鎖定。 請解除鎖定裝置，然後再試一次。

請解除鎖定裝置，然後再試一次。

### <a name="a-namemt1032mt1032-this-application-executable-might-be-too-large--mb-to-execute-on-device-if-bitcode-was-enabled-you-might-want-to-disable-it-for-development-it-is-only-required-to-submit-applications-to-apple"></a><a name="MT1032"/>MT1032： 這個應用程式可執行檔可能會太大 (* MB) 裝置上執行。 如果已啟用 bitcode 可能會想要停用它進行開發，提交至 Apple 應用程式才需要它。

### <a name="a-namemt1033mt1033-could-not-uninstall-the-application--from-the-device--"></a><a name="MT1033"/>MT1033： 無法解除安裝應用程式 '\*'從裝置'\*': *

<!-- 1034 used by mmp -->

### <a name="a-namemt1035mt1035-cannot-include-different-versions-of-the-framework-name"></a><a name="MT1035"/>MT1035： 不能包含不同的 framework 版本為 '{name}'

您不可能使用不同版本的相同 framework 連結。

這通常會擴充功能參考不同版本的架構比容器應用程式 （可能是透過協力廠商的繫結組件）。

遵循此錯誤會有多個[MT1036](#MT1036)列出每個不同的架構路徑的錯誤。

### <a name="a-namemt1036mt1036-framework-name-included-from-path-related-to-previous-error"></a><a name="MT1036"/>MT1036: 包含從 Framework '{name}': {path} （與之前錯誤相關）

會報告此錯誤只搭配[MT1036](#MT1036)。 請參閱[MT1036](#MT1036)如需詳細資訊。

### <a name="mt11xx-debug-service"></a>MT11xx： 偵錯服務

<!--
  MT11xx DebugService.cs
  -->

### <a name="a-namemt1101mt1101-could-not-start-app"></a><a name="MT1101"/>MT1101 無法啟動應用程式



### <a name="a-namemt1102mt1102-could-not-attach-to-the-app-to-kill-it-"></a><a name="MT1102"/>MT1102 無法附加至應用程式 （若要刪除該）: *



### <a name="a-namemt1103mt1103-could-not-detach"></a><a name="MT1103"/>MT1103 無法中斷連接



### <a name="a-namemt1104mt1104-failed-to-send-packet-"></a><a name="MT1104"/>無法傳送封包 MT1104: *



### <a name="a-namemt1105mt1105-unexpected-response-type"></a><a name="MT1105"/>MT1105 未預期的回應類型



### <a name="a-namemt1106mt1106-could-not-get-list-of-applications-on-the-device-request-timed-out"></a><a name="MT1106"/>MT1106 無法取得裝置上的應用程式清單： 要求已逾時。



### <a name="a-namemt1107mt1107-application-failed-to-launch-"></a><a name="MT1107"/>MT1107： 無法啟動應用程式: *

請檢查您的裝置是否鎖定。

如果您要部署企業應用程式，或使用可用的佈建設定檔，您可能會對信任的開發人員 (說明<a href="http://stackoverflow.com/a/30726375/183422">這裡</a>)。

### <a name="a-namemt1108mt1108-could-not-find-developer-tools-for-this-xx-yy-device"></a><a name="MT1108"/>MT1108： 找不到此 XX (YY) 裝置的開發人員工具。

從 mtouch 少數作業需要<tt>DeveloperDiskImage.dmg</tt>檔案必須存在。   這個檔案是 Xcode 的一部分，而且通常位於相對於您用來建置，針對在 SDK <tt>Xcode.app/Contents/Developer/iPhoneOS.platform/DeviceSupport/VERSION/DeveloperDiskImage.dmg</tt>。

這個錯誤可能會發生是因為您不需要符合您已經連接之裝置 DeveloperDiskImage.dmg。


### <a name="a-namemt1109mt1109-application-failed-to-launch-because-the-device-is-locked-please-unlock-the-device-and-try-again"></a><a name="MT1109"/>MT1109： 應用程式無法啟動，因為裝置已鎖定。 請解除鎖定裝置，然後再試一次。

請檢查您的裝置是否鎖定。

### <a name="a-namemt1110mt1110-application-failed-to-launch-because-of-ios-security-restrictions-please-ensure-the-developer-is-trusted"></a><a name="MT1110"/>MT1110： 應用程式無法啟動，因為 iOS 安全性限制。 請確定開發人員是受信任。

如果您要部署企業應用程式，或使用可用的佈建設定檔，您可能會對信任的開發人員 (說明<a href="http://stackoverflow.com/a/30726375/183422">這裡</a>)。

### <a name="mt12xx-simulator"></a>MT12xx： 模擬器

<!--
  MT12xx simcontroller.cs
  -->

### <a name="a-namemt1201mt1201-could-not-load-the-simulator-"></a><a name="MT1201"/>MT1201： 無法載入模擬器: *
### <a name="a-namemt1202mt1202-invalid-simulator-configuration-"></a><a name="MT1202"/>MT1202： 無效的模擬器組態: *
### <a name="a-namemt1203mt1203-invalid-simulator-specification-"></a><a name="MT1203"/>MT1203： 無效的模擬器規格: *
### <a name="a-namemt1204mt1204-invalid-simulator-specification--runtime-not-specified"></a><a name="MT1204"/>MT1204： 無效的模擬器規格 ' *': 未指定的執行階段。
### <a name="a-namemt1205mt1205-invalid-simulator-specification--device-type-not-specified"></a><a name="MT1205"/>MT1205： 無效的模擬器規格 ' *': 未指定的裝置類型。
### <a name="a-namemt1206mt1206-could-not-find-the-simulator-runtime-"></a><a name="MT1206"/>MT1206： 找不到模擬器執行階段 ' *'。
### <a name="a-namemt1207mt1207-could-not-find-the-simulator-device-type-"></a><a name="MT1207"/>MT1207： 找不到模擬器裝置類型，' *'。
### <a name="a-namemt1208mt1208-could-not-find-the-simulator-runtime-"></a><a name="MT1208"/>MT1208： 找不到模擬器執行階段 ' *'。
### <a name="a-namemt1209mt1209-could-not-find-the-simulator-device-type-"></a><a name="MT1209"/>MT1209： 找不到模擬器裝置類型，' *'。
### <a name="a-namemt1210mt1210-invalid-simulator-specification--unknown-key-"></a><a name="MT1210"/>MT1210： 無效的模擬器規格： \*，未知的機碼 '\*'
### <a name="a-namemt1211mt1211-the-simulator-version--does-not-support-the-simulator-type-"></a><a name="MT1211"/>MT1211： 模擬器版本 '\*'不支援模擬器類型'\*'
### <a name="a-namemt1212mt1212-failed-to-create-a-simulator-version-where-type---and-runtime--"></a><a name="MT1212"/>MT1212： 無法建立模擬器版本，類型 = * 和執行階段 = *。
### <a name="a-namemt1213mt1213-invalid-simulator-specification-for-xcode-4-"></a><a name="MT1213"/>MT1213： 適用於 Xcode 4 無效的模擬器規格: *
### <a name="a-namemt1214mt1214-invalid-simulator-specification-for-xcode-5-"></a><a name="MT1214"/>MT1214： Xcode 5 無效的模擬器規格: *
### <a name="a-namemt1215mt1215-invalid-sdk-specified-"></a><a name="MT1215"/>MT1215： 無效的 SDK，指定: *
### <a name="a-namemt1216mt1216-could-not-find-the-simulator-udid-"></a><a name="MT1216"/>MT1216： 找不到模擬器 UDID ' *'。
### <a name="a-namemt1217mt1217-could-not-load-the-app-bundle-at-"></a><a name="MT1217"/>MT1217： 無法載入在應用程式套件組合 ' *'。
### <a name="a-namemt1218mt1218-no-bundle-identifier-found-in-the-app-at-"></a><a name="MT1218"/>MT1218： 在執行的應用程式中沒有套件組合識別碼找到 ' *'。
### <a name="a-namemt1219mt1219-could-not-find-the-simulator-for-"></a><a name="MT1219"/>MT1219： 找不到適用的模擬器 ' *'。
### <a name="a-namemt1220mt1220-could-not-find-the-latest-simulator-runtime-for-device-"></a><a name="MT1220"/>MT1220： 找不到裝置的最新的模擬器執行階段 ' *'。

這通常表示 Xcode 的問題。

項目可以嘗試修正此問題：

* 在 Xcode 中一次使用模擬器。
* 傳遞 SDK 版本，明確使用-sdk <version>。
* 重新安裝 Xcode。

### <a name="a-namemt1221mt1221-could-not-find-the-paired-iphone-simulator-for-the-watchos-simulator-"></a><a name="MT1221"/>MT1221： 找不到 WatchOS 模擬器配對的 iPhone 模擬器 ' *'。

當啟動 WatchOS 模擬器 WatchOS 應用程式，必須是配對的 iOS 模擬器以及。

監看模擬器可搭配 iOS 模擬器使用 Xcode 的裝置 UI (視窗] 功能表-> [裝置)。

### <a name="mt13xx-linkwith"></a>MT13xx: [LinkWith]

<!--
  MT13xx [LinkWith]
  -->


### <a name="a-namemt1301mt1301-native-library---was-ignored-since-it-does-not-match-the-current-build-architectures-"></a><a name="MT1301"/>MT1301 原生程式庫`*`(\*) 已略過，因為它不符合目前組建 architecture(s) (\*)



### <a name="a-namemt1302mt1302-could-not-extract-the-native-library--from--please-ensure-the-native-library-was-properly-embedded-in-the-managed-assembly-if-the-assembly-was-built-using-a-binding-project-the-native-library-must-be-included-in-the-project-and-its-build-action-must-be-objcbindingnativelibrary"></a><a name="MT1302"/>MT1302 無法擷取原生程式庫 ' *' 從 '+'。 請確定原生程式庫已正確地內嵌的 managed 組件中 （如果使用繫結專案建置組件，原生程式庫必須包含在專案中，且其 建置動作必須是 'ObjcBindingNativeLibrary'）。

### <a name="a-namemt1303mt1303-could-not-decompress-the-native-framework--from--please-review-the-build-log-for-more-information-from-the-native-zip-command"></a><a name="MT1303"/>MT1303： 無法解壓縮原生架構 '\*'from'\*'。 請檢閱組建記錄檔，如需詳細資訊，請從原生 'zip' 命令。

無法解壓縮從繫結的程式庫指定的原生架構。

請檢閱 bulid 記錄檔以取得從原生 'zip' 命令失敗的詳細資訊。

### <a name="a-namemt1304mt1304-the-embedded-framework--in--is-invalid-it-does-not-contain-an-infoplist"></a><a name="MT1304"/>MT1304： 內嵌的 framework ' *' 在 * 無效： 它不包含 Info.plist。

指定內嵌的架構不包含 Info.plist，並因此不是有效的架構。

請確定架構無效。

### <a name="a-namemt1305mt1305-the-binding-library--contains-a-user-framework--but-embedded-user-frameworks-require-ios-80-the-current-deployment-target-is--please-set-the-deployment-target-in-the-infoplist-file-to-at-least-80"></a><a name="MT1305"/>MT1305： 繫結程式庫 '\*' 包含的使用者架構 (\*)，但內嵌的使用者介面架構需要 iOS 8.0 以上版本 (目前的部署目標是 *)。 請至少 8.0 Info.plist 檔案中設定部署目標。

指定的繫結的程式庫包含內嵌的架構，但 Xamarin.iOS 僅支援 iOS 8.0 或更新版本上內嵌的架構。

請至少可解決此錯誤的 8.0 Info.plist 檔案中設定部署目標 （或不使用內嵌的架構）。

### <a name="mt14xx-crash-reports"></a>MT14xx： 當機報告

<!--
  MT14xx    CrashReports.cs
  -->

### <a name="a-namemt1400mt1400-could-not-open-crash-report-service-afcconnectionopen-returned-"></a><a name="MT1400"/>MT1400： 無法開啟損毀報表服務： AFCConnectionOpen 傳回 *

嘗試從裝置存取當機報告時，就會發生錯誤。

若要嘗試解決這個問題的項目：

* 從裝置刪除應用程式並再試一次。
* 中斷連線的裝置，然後重新連接它。
* 重新啟動裝置。
* 重新啟動 mac
* 同步處理裝置與 iTunes （這會從裝置移除任何損毀報表）。

### <a name="a-namemt1401mt1401-could-not-close-crash-report-service-afcconnectionclose-returned-"></a><a name="MT1401"/>MT1401： 無法關閉當機報告服務： AFCConnectionClose 傳回 *

嘗試從裝置存取當機報告時，就會發生錯誤。

若要嘗試解決這個問題的項目：

* 從裝置刪除應用程式並再試一次。
* 中斷連線的裝置，然後重新連接它。
* 重新啟動裝置。
* 重新啟動 mac
* 同步處理裝置與 iTunes （這會從裝置移除任何損毀報表）。

### <a name="a-namemt1402mt1402-could-not-read-file-info-for--afcfileinfoopen-returned-"></a><a name="MT1402"/>MT1402： 無法讀取的檔案資訊 *: AFCFileInfoOpen 傳回 *

嘗試從裝置存取當機報告時，就會發生錯誤。

若要嘗試解決這個問題的項目：

* 從裝置刪除應用程式並再試一次。
* 中斷連線的裝置，然後重新連接它。
* 重新啟動裝置。
* 重新啟動 mac
* 同步處理裝置與 iTunes （這會從裝置移除任何損毀報表）。

### <a name="a-namemt1403mt1403-could-not-read-crash-report-afcdirectoryopen--returned-"></a><a name="MT1403"/>MT1403： 無法讀取的當機報告： AFCDirectoryOpen （*） 傳回: *

嘗試從裝置存取當機報告時，就會發生錯誤。

若要嘗試解決這個問題的項目：

* 從裝置刪除應用程式並再試一次。
* 中斷連線的裝置，然後重新連接它。
* 重新啟動裝置。
* 重新啟動 mac
* 同步處理裝置與 iTunes （這會從裝置移除任何損毀報表）。

### <a name="a-namemt1404mt1404-could-not-read-crash-report-afcfilerefopen--returned-"></a><a name="MT1404"/>MT1404： 無法讀取的當機報告： AFCFileRefOpen （*） 傳回: *

嘗試從裝置存取當機報告時，就會發生錯誤。

若要嘗試解決這個問題的項目：

* 從裝置刪除應用程式並再試一次。
* 中斷連線的裝置，然後重新連接它。
* 重新啟動裝置。
* 重新啟動 mac
* 同步處理裝置與 iTunes （這會從裝置移除任何損毀報表）。

### <a name="a-namemt1405mt1405-could-not-read-crash-report-afcfilerefread--returned-"></a><a name="MT1405"/>MT1405： 無法讀取的當機報告： AFCFileRefRead （*） 傳回: *

嘗試從裝置存取當機報告時，就會發生錯誤。

若要嘗試解決這個問題的項目：

* 從裝置刪除應用程式並再試一次。
* 中斷連線的裝置，然後重新連接它。
* 重新啟動裝置。
* 重新啟動 mac
* 同步處理裝置與 iTunes （這會從裝置移除任何損毀報表）。

### <a name="a-namemt1406mt1406-could-not-list-crash-reports-afcdirectoryopen--returned-"></a><a name="MT1406"/>MT1406： 無法列出當機報告： AFCDirectoryOpen （*） 傳回: *

嘗試從裝置存取當機報告時，就會發生錯誤。

若要嘗試解決這個問題的項目：

* 從裝置刪除應用程式並再試一次。
* 中斷連線的裝置，然後重新連接它。
* 重新啟動裝置。
* 重新啟動 mac
* 同步處理裝置與 iTunes （這會從裝置移除任何損毀報表）。

<!--- 1407 used by mmp -->

### <a name="mt16xx-macho"></a>MT16xx: MachO

<!--
  MT16xx    MachO.cs
  -->

### <a name="a-namemt1600mt1600-not-a-mach-o-dynamic-library-unknown-header-0x-"></a><a name="MT1600"/>MT1600： 不符合 O 動態程式庫 （未知標頭 ' 0 x *'）: *。

處理的動態程式庫時發生錯誤。

請確認動態程式庫是有效的符合 O 動態程式庫。

您可以使用驗證程式庫格式`file`從終端機的命令：

    file -arch all -l /path/to/library.dylib

### <a name="a-namemt1601mt1601-not-a-static-library-unknown-header--"></a><a name="MT1601"/>MT1601： 不是靜態程式庫 (未知標頭 ' *'): *。

處理在靜態程式庫時發生錯誤。

請確定靜態程式庫是有效的符合 O 靜態程式庫。

您可以使用驗證程式庫格式`file`從終端機的命令：

    file -arch all -l /path/to/library.a

### <a name="a-namemt1602mt1602-not-a-mach-o-dynamic-library-unknown-header-0x-"></a><a name="MT1602"/>MT1602： 不符合 O 動態程式庫 （未知標頭 ' 0 x *'）: *。

處理的動態程式庫時發生錯誤。

請確認動態程式庫是有效的符合 O 動態程式庫。

您可以使用驗證程式庫格式`file`從終端機的命令：

    file -arch all -l /path/to/library.dylib

### <a name="a-namemt1603mt1603-unknown-format-for-fat-entry-at-position--in-"></a><a name="MT1603"/>MT1603： 未知的格式為 fat 的項目位置 * 中 *。

處理 fat 封存有問題時發生錯誤。

請確定 fat 封存有效。

您可以使用驗證的 fat 封存格式`file`從終端機的命令：

    file -arch all -l /path/to/file

### <a name="a-namemt1604mt1604-file-of-type--is-not-a-macho-file-"></a><a name="MT1604"/>MT1604： 型別的檔案 * 不是 MachO 檔案 （*）。

處理 MachO 有問題的檔案時發生錯誤。

請確定檔案是有效的符合 O 動態程式庫。

您可以使用驗證檔案的格式`file`從終端機的命令：

    file -arch all -l /path/to/file

## <a name="mt2xxx-linker-error-messages"></a>MT2xxx： 連結器錯誤訊息

<!--
 MT2xxx Linker
  MT20xx Linker (general) errors
  -->

### <a name="a-namemt2001mt2001-could-not-link-assemblies"></a><a name="MT2001"/>MT2001 無法連結的組件

此錯誤表示受管理的連結器遇到意外的錯誤，例如例外狀況，並無法完成或儲存正在處理的組件。 確切的錯誤的詳細資訊將為組建記錄檔的一部分例如

``` 
error MT2001: Could not link assemblies.
    Method: `System.Void Todo.TodoListPageCS/<<-ctor>b__1_0>d::SetStateMachine(System.Runtime.CompilerServices.IAsyncStateMachine)`
    Assembly: `QuickTodo, Version=1.0.6297.28241, Culture=neutral, PublicKeyToken=null`
Reason: Value cannot be null.
Parameter name: instruction
```

請務必檔案這類問題的錯誤報告。 在大部分情況下發行適當的修正程式之前可以提供因應措施。 上述資訊是很重要 （以及測試案例和/或組件 binairy） 解決問題。


### <a name="a-namemt2002mt2002-can-not-resolve-reference-"></a><a name="MT2002"/>MT2002 可以解析參考: *



### <a name="a-namemt2003mt2003-option--will-be-ignored-since-linking-is-disabled"></a><a name="MT2003"/>MT2003 選項 ' *' 將被忽略，因為連結已停用



### <a name="a-namemt2004mt2004-extra-linker-definitions-file--could-not-be-located"></a><a name="MT2004"/>MT2004 額外連結器定義檔 ' *' 找不到。



### <a name="a-namemt2005mt2005-definitions-from--could-not-be-parsed"></a><a name="MT2005"/>來自 MT2005 定義 ' *' 無法剖析。



### <a name="a-namemt2006mt2006-can-not-load-mscorlibdll-from--please-reinstall-xamarinios"></a><a name="MT2006"/>MT2006： 無法載入從 mscorlib.dll: *。 請重新安裝 Xamarin.iOS。

這通常表示有 Xamarin.iOS 的安裝問題。 請嘗試重新安裝 Xamarin.iOS。

<!--- 2007 used by mmp -->
<!--- 2009 used by mmp -->

### <a name="a-namemt2010mt2010-unknown-httpmessagehandler--valid-values-are-httpclienthandler-default-cfnetworkhandler-or-nsurlsessionhandler"></a><a name="MT2010"/>MT2010： 未知的 HttpMessageHandler `*`。 有效值為 HttpClientHandler （預設值）、 CFNetworkHandler 或 NSUrlSessionHandler

### <a name="a-namemt2011mt2011-unknown-tlsprovider---valid-values-are-default-or-appletls"></a><a name="MT2011"/>MT2011： 未知的 TlsProvider `*`。  有效值為 default 或 appletls。

若要指定的值`tls-provider=`不是有效的 TLS （傳輸層安全性） 提供者。

`default`和`appletls`是唯一的有效值，兩者均表示相同的選項，也就是提供 SSL/TLS 支援使用原生 Apple TLS API。

<!--- 2012 used by mmp -->
<!--- 2013 used by mmp -->
<!--- 2014 used by mmp -->

### <a name="a-namemt2015mt2015-invalid-httpmessagehandler--for-watchos-the-only-valid-value-is-nsurlsessionhandler"></a><a name="MT2015"/>MT2015： 無效的 HttpMessageHandler `*` watchOS 的。 唯一有效的值是 NSUrlSessionHandler。

這是因為專案檔指定了無效的 HttpMessageHandler，就會發生的警告。

產生預設我們 preview 工具的舊版專案檔中無效的值。

若要修正這個警告，在文字編輯器中，開啟專案檔，並移除所有的 HttpMessageHandler 節點的 XML。

### <a name="a-namemt2016mt2016-invalid-tlsprovider-legacy-option-the-only-valid-value-appletls-will-be-used"></a><a name="MT2016"/>MT2016： 無效的 TlsProvider`legacy`選項。 唯一有效的值`appletls`將使用。

`legacy`提供者，這是完全受管理的 SSLv3 / TLSv1 唯一的提供者，不會隨附 Xamarin.iOS 不再。 使用這個舊的提供者，並建置與較新的專案`appletls`其中一個。

若要修正這個警告，在文字編輯器中，開啟專案檔，並移除所有 'MtouchTlsProvider' 的 xml 節點。

### <a name="a-namemt2017mt2017-could-not-process-xml-description"></a><a name="MT2017"/>MT2017： 無法處理 XML 描述。

這表示在沒有錯誤[自訂 XML 連結器組態檔](https://developer.xamarin.com/guides/cross-platform/advanced/custom_linking/)提供時，請檢閱您的檔案。

### <a name="a-namemt2018mt2018-the-assembly--is-referenced-from-two-different-locations--and-"></a><a name="MT2018"/>MT2018： 組件 '\*' 所參考的兩個不同位置: '\*' 和 ' *'。

錯誤訊息中所提及的組件會載入從多個位置。 請務必一律使用相同版本的組件。

### <a name="a-namemt2019mt2019-can-not-load-the-root-assembly-"></a><a name="MT2019"/>MT2019： 無法載入根組件 ' *'

無法載入根組件。 請確認該路徑中的錯誤訊息參考到現有的檔案，而且它是有效的.NET 組件。

### <a name="a-namemt202xmt202x-binding-optimizer-failed-processing-"></a><a name="MT202x"/>MT202x： 繫結最佳化工具無法處理`...`。

當嘗試最佳化產生繫結程式碼時發生未預期的項目。 錯誤訊息中名為造成問題的項目。 若要修正此問題，名為組件 （或包含的類型或方法命名為） 需要在中提供[bug 報表](http://bugzilla.xamarin.com)以及完成的組建記錄檔，具有已啟用的詳細資訊 (也就是`-v -v -v -v`中**其他 mtouch引數**)。

最後一位數`x`將會：
* `0` 組件名稱。
* `1` 型別名稱。
* `3` 方法名稱。

### <a name="a-namemt2030mt2030-remove-user-resources-failed-processing-"></a><a name="MT2030"/>MT2030： 移除使用者資源無法處理`...`。

當嘗試移除使用者的資源時發生發生無法預期。 錯誤訊息中名為造成問題的組件。 若要修正此問題的組件必須在中提供[bug 報表](http://bugzilla.xamarin.com)以及完成的組建記錄檔，具有已啟用的詳細資訊 (也就是`-v -v -v -v`中**其他 mtouch 引數**)。

使用者資源是在解壓縮後，在建置時，若要建立應用程式套件組合 （做為資源） 組件內所包含的檔案。 包括：

* `__monotouch_content_*` 和`__monotouch_pages_*`資源; 和
* 原生程式庫內嵌於繫結組件中。

### <a name="a-namemt2040mt2040-default-httpmessagehandler-setter-failed-processing-"></a><a name="MT2040"/>MT2040： 預設 HttpMessageHandler setter 無法處理`...`。

嘗試將設為預設值時，發生無法預期發生`HttpMessageHandler`應用程式。 請[bug 報表](http://bugzilla.xamarin.com)以及完成的組建記錄檔，具有已啟用的詳細資訊 (也就是`-v -v -v -v`中**其他 mtouch 引數**)。

### <a name="a-namemt2050mt2050-code-remover-failed-processing-"></a><a name="MT2050"/>MT2050： 程式碼 Remover 無法處理`...`。

當嘗試移除 BCL 傳送與應用程式的程式碼時發生非預期。 請[bug 報表](http://bugzilla.xamarin.com)以及完成的組建記錄檔，具有已啟用的詳細資訊 (也就是`-v -v -v -v`中**其他 mtouch 引數**)。

### <a name="a-namemt2060mt2060-sealer-failed-processing-"></a><a name="MT2060"/>MT2060: Sealer 無法處理`...`。

發生無法預期發生或 devirtualizing 一些方法時嘗試密封類型或方法 （最後一個）。 錯誤訊息中名為造成問題的組件。 若要修正此問題的組件必須在中提供[bug 報表](http://bugzilla.xamarin.com)以及完成的組建記錄檔，具有已啟用的詳細資訊 (也就是`-v -v -v -v`中**其他 mtouch 引數**)。

### <a name="a-namemt2070mt2070-metadata-reducer-failed-processing-"></a><a name="MT2070"/>MT2070： 無法處理之中繼資料減壓器`...`。

當嘗試減少從應用程式的中繼資料時發生發生無法預期。 錯誤訊息中名為造成問題的組件。 若要修正此問題的組件必須在中提供[bug 報表](http://bugzilla.xamarin.com)以及完成的組建記錄檔，具有已啟用的詳細資訊 (也就是`-v -v -v -v`中**其他 mtouch 引數**)。

### <a name="a-namemt2080mt2080-marknsobjects-failed-processing-"></a><a name="MT2080"/>MT2080: MarkNSObjects 無法處理`...`。

嘗試將標記時，發生無法預期發生`NSObject`從應用程式的子類別。 錯誤訊息中名為造成問題的組件。 若要修正此問題的組件必須在中提供[bug 報表](http://bugzilla.xamarin.com)以及完成的組建記錄檔，具有已啟用的詳細資訊 (也就是`-v -v -v -v`中**其他 mtouch 引數**)。

<!-- MT21xx: more linker errors -->

<!--- 2100 used by mmp -->

### <a name="a-namemt2101mt2101-cant-resolve-the-reference--referenced-from-the-method--in-"></a><a name="MT2101"/>MT2101： 無法解析參考 '\*'，參考從方法'\*' 在 ' *'。

處理錯誤訊息中所述的方法時，遇到無效的組件參考。

錯誤訊息中名為造成問題的組件。 若要修正此問題的組件必須在中提供[bug 報表](https://bugzilla.xamarin.com)以及完成的組建記錄檔，具有已啟用的詳細資訊 (也就是`-v -v -v -v`中**其他 mtouch 引數**)。

### <a name="a-namemt2102mt2102-error-processing-the-method--in-the-assembly--"></a><a name="MT2102"/>MT2102： 錯誤處理方法 '\*'中的組件'\*': *

當嘗試將錯誤訊息中所述方法標記時發生發生無法預期。

錯誤訊息中名為造成問題的組件。 若要修正此問題的組件必須在中提供[bug 報表](https://bugzilla.xamarin.com)以及完成的組建記錄檔，具有已啟用的詳細資訊 (也就是`-v -v -v -v`中**其他 mtouch 引數**)。

## <a name="mt3xxx-aot-error-messages"></a>MT3xxx: AOT 錯誤訊息

<!--
 MT3xxx AOT
  MT30xx AOT (general) errors
  -->

### <a name="a-namemt3001mt3001-could-not-aot-the-assembly-"></a><a name="MT3001"/>MT3001 無法 AOT 組件 ' *'

這通常表示 AOT 編譯器中的 bug。 請將 bug 歸檔[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)可用來重現錯誤的專案。

有時候很可能暫時解決這個問題，停用累加建置在專案的 iOS 建置選項中 (但它仍然是 bug，因此請仍回報)。

### <a name="a-namemt3002mt3002-aot-restriction-method--must-be-static-since-it-is-decorated-with-monopinvokecallback-see-httpsdeveloperxamarincomguidesiosadvancedtopicslimitationsreversecallbackshttpsdeveloperxamarincomguidesiosadvancedtopicslimitationsreversecallbacks"></a><a name="MT3002"/>MT3002 AOT 限制： 方法 ' *' 必須是靜態，因為它以 [MonoPInvokeCallback] 裝飾。 請參閱[https://developer.xamarin.com/guides/ios/advanced_topics/limitations/#Reverse_Callbacks](https://developer.xamarin.com/guides/ios/advanced_topics/limitations/#Reverse_Callbacks)

這則錯誤訊息是來自 AOT 編譯器。



### <a name="a-namemt3003mt3003-conflicting---debug-and---llvm-options-soft-debugging-is-disabled"></a><a name="MT3003"/>MT3003 衝突-偵錯和-llvm 選項。 軟偵錯已停用。

偵錯時，不支援 LLVM 已啟用。 如果您需要偵錯應用程式，請先停用 LLVM。

### <a name="a-namemt3004mt3004-could-not-aot-the-assembly--because-it-doesnt-exist"></a><a name="MT3004"/>MT3004 無法 AOT 組件 ' *' 因為它不存在。



### <a name="a-namemt3005mt3005-the-dependency--of-the-assembly--was-not-found-please-review-the-projects-references"></a><a name="MT3005"/>MT3005 相依性 '\*'的組件'\*' 找不到。 請檢閱專案的參考。

這通常是 mscorlib.dll 的組件參考另一個版本的平台組件 （通常是 mscorlib.dll 的.NET 4 版)。

這不支援，可能無法建置或正確執行 （組件可以使用 API Xamarin.iOS 版本沒有 mscorlib.dll.NET 4 版）。

### <a name="a-namemt3006mt3006-could-not-compute-a-complete-dependency-map-for-the-project-this-will-result-in-slower-build-times-because-xamarinios-cant-properly-detect-what-needs-to-be-rebuilt-and-what-does-not-need-to-be-rebuilt-please-review-previous-warnings-for-more-details"></a><a name="MT3006"/>MT3006 無法計算專案的完整相依性對應。 這將導致較慢的建置時間，因為 Xamarin.iOS 無法適當地偵測到必須重建的項目 （和功能不需要重建）。 請檢閱先前的警告，如需詳細資訊。

 建置或正確執行 （組件可以使用 API Xamarin.iOS 版本沒有 mscorlib.dll.NET 4 版）。

### <a name="a-namemt3007mt3007-debug-info-files-mdb-will-not-be-loaded-when-llvm-is-enabled"></a><a name="MT3007"/>MT3007： 偵錯資訊檔案 (.mdb) 將不會載入 llvm 啟用時。

### <a name="a-namemt3008mt3008-bitcode-support-requires-the-use-of-the-llvm-aot-backend---llvm"></a><a name="MT3008"/>MT3008: Bitcode 支援需要 LLVM AOT 後端使用 (-llvm)

Bitcode 支援需要 LLVM AOT 後端使用 (-llvm)。

請停用 Bitcode 支援或啟用 LLVM。

<!--- 3009 used by mmp -->
<!--- 3010 used by mmp -->

## <a name="mt4xxx-code-generation-error-messages"></a>MT4xxx： 程式碼產生錯誤訊息

### <a name="mt40xx-main"></a>MT40xx： 主要

<!--
 MT4xxx code generation
  MT40xx main.m
  -->

### <a name="a-namemt4001mt4001-the-main-template-could-not-be-expanded-to-"></a><a name="MT4001"/>MT4001 主要範本無法擴充至`*`。

產生 main.m 時，就會發生錯誤。 請將 bug 歸檔在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

### <a name="a-namemt4002mt4002-failed-to-compile-the-generated-code-for-pinvoke-methods-please-file-a-bug-report-at-httpbugzillaxamarincom"></a><a name="MT4002"/>MT4002 無法編譯 P/Invoke 方法產生的程式碼。 請在 http://bugzilla.xamarin.com 檔案錯誤報告

無法編譯 P/Invoke 方法產生的程式碼。 請檔案錯誤報告在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

### <a name="mt41xx-registrar"></a>MT41xx： 註冊機構

<!--
  MT41xx registrar.m
  -->

### <a name="a-namemt4101mt4101-the-registrar-cannot-build-a-signature-for-type-"></a><a name="MT4101"/>MT4101 登錄器無法建立類型的簽章`*`。

在執行階段並不知道如何從目標 C.封送處理的匯出 API 中找不到型別

如果您認為 Xamarin.iOS 應該支援的型別有問題，請檔案在的增強功能要求[http://bugzilla.xamarin.com](http://bugzilla.xamarin.com)。

### <a name="a-namemt4102mt4102-the-registrar-found-an-invalid-type--in-signature-for-method--use--instead"></a><a name="MT4102"/>MT4102 登錄器找到無效的型別`*`中方法的簽章`*`。 請改用 `*`。

這目前只會發生一種類型： System.DateTime。 請改用 OBJECTIVE-C 對等項目 (NSDate)。

### <a name="a-namemt4103mt4103-the-registrar-found-an-invalid-type--in-signature-for-method--the-type-implements-inativeobject-but-does-not-have-a-constructor-that-takes-two-intptr-bool-arguments"></a><a name="MT4103"/>MT4103 登錄器找到無效的型別`*`中方法的簽章`*`： 型別實作 INativeObject，但是並沒有會採用兩個建構函式 (IntPtr，bool) 引數

發生這種情況，註冊機構當遇到使用提到的特性的簽章中的型別。 在註冊機構可能需要建立新的執行個體的型別，而且在此情況下需要 (IntPtr，bool) 的建構函式簽章-的第一個引數 (IntPtr) 指定的受管理的控制代碼，第二個如果呼叫端傳遞原生的擁有權（如果此值為 false 將呼叫物件上的 [保留]） 處理。

### <a name="a-namemt4104mt4104-the-registrar-cannot-marshal-the-return-value-for-type--in-signature-for-method-"></a><a name="MT4104"/>MT4104 登錄器無法封送處理類型的傳回值`*`中方法的簽章`*`。

在執行階段並不知道如何從目標 C.封送處理的匯出 API 中找不到型別

如果您認為 Xamarin.iOS 應該支援的型別有問題，請檔案在的增強功能要求[http://bugzilla.xamarin.com](http://bugzilla.xamarin.com)。

### <a name="a-namemt4105mt4105-the-registrar-cannot-marshal-the-parameter-of-type--in-signature-for-method-"></a><a name="MT4105"/>MT4105 登錄器無法封送處理類型的參數`*`中方法的簽章`*`。

如果您認為 Xamarin.iOS 應該支援的型別有問題，請檔案在的增強功能要求[http://bugzilla.xamarin.com](http://bugzilla.xamarin.com)。

### <a name="a-namemt4106mt4106-the-registrar-cannot-marshal-the-return-value-for-structure--in-signature-for-method-"></a><a name="MT4106"/>MT4106 登錄器無法封送處理結構的傳回值`*`中方法的簽章`*`。

在執行階段並不知道如何從目標 C.封送處理的匯出 API 中找不到型別

如果您認為 Xamarin.iOS 應該支援的型別有問題，請檔案在的增強功能要求[http://bugzilla.xamarin.com](http://bugzilla.xamarin.com)。

### <a name="a-namemt4107mt4107-the-registrar-cannot-marshal-the-parameter-of-type--in-signature-for-method-"></a><a name="MT4107"/>MT4107 登錄器無法封送處理類型的參數`*`中方法的簽章`+`。

在執行階段並不知道如何從目標 C.封送處理的匯出 API 中找不到型別

如果您認為 Xamarin.iOS 應該支援的型別有問題，請檔案在的增強功能要求[http://bugzilla.xamarin.com](http://bugzilla.xamarin.com)。

### <a name="a-namemt4108mt4108-the-registrar-cannot-get-the-objectivec-type-for-managed-type-"></a><a name="MT4108"/>MT4108 登錄器無法取得 managed 類型的 ObjectiveC 類型`*`。

在執行階段並不知道如何從目標 C.封送處理的匯出 API 中找不到型別

如果您認為 Xamarin.iOS 應該支援的型別有問題，請檔案在的增強功能要求[http://bugzilla.xamarin.com](http://bugzilla.xamarin.com)。

### <a name="a-namemt4109mt4109-failed-to-compile-the-generated-registrar-code-please-file-a-bug-report-at-httpbugzillaxamarincom"></a><a name="MT4109"/>MT4109 無法編譯產生的註冊機構的程式碼。 請在 http://bugzilla.xamarin.com 檔案錯誤報告

無法編譯產生的程式碼的註冊機構。 組建記錄檔會包含原生編譯器，解釋為何不編譯程式碼的輸出。

這一定是 Xamarin.iOS; 中的 bug請檔案錯誤報告在[http://bugzilla.xamarin.com](http://bugzilla.xamarin.com)與您的專案或測試案例。

### <a name="a-namemt4110mt4110-the-registrar-cannot-marshal-the-out-parameter-of-type--in-signature-for-method-"></a><a name="MT4110"/>MT4110 登錄器無法封送處理類型的 out 參數`*`中方法的簽章`*`。



### <a name="a-namemt4111mt4111-the-registrar-cannot-build-a-signature-for-type--in-method-"></a><a name="MT4111"/>MT4111 登錄器無法建立類型的簽章`*`方法中`*`。



### <a name="a-namemt4112mt4112-the-registrar-found-an-invalid-type--registering-generic-types-with-objective-c-is-not-supported-and-may-lead-to-random-behavior-andor-crashes-for-backwards-compatibility-with-older-versions-of-xamarinios-it-is-possible-to-ignore-this-error-by-passing---unsupported--enable-generics-in-registrar-as-an-additional-mtouch-argument-in-the-projects-ios-build-options-page-see-developerxamarincomguidesiosadvancedtopicsregistrarhttpsdeveloperxamarincomguidesiosadvancedtopicsregistrar-for-more-information"></a><a name="MT4112"/>MT4112 登錄器找到無效的型別`*`。 向 Objective C 中的泛型型別不支援，並可能導致隨機的行為及/或損毀 (針對回溯相容性與舊版本的 Xamarin.iOS 便可忽略此錯誤，藉由傳遞`--unsupported--enable-generics-in-registrar`做為額外的 mtouch專案的 iOS 組建選項 頁面中的引數。 請參閱[developer.xamarin.com/guides/ios/advanced_topics/registrar](https://developer.xamarin.com/guides/ios/advanced_topics/registrar)如需詳細資訊)。



### <a name="a-namemt4113mt4113-the-registrar-found-a-generic-method--exporting-generic-methods-is-not-supported-and-will-lead-to-random-behavior-andor-crashes"></a><a name="MT4113"/>MT4113 登錄器找到泛型的方法: '\*。\*'。 匯出泛型方法不支援，而將會導致隨機的行為及/或當機。



### <a name="a-namemt4114mt4114-unexpected-error-in-the-registrar-for-the-method----please-file-a-bug-report-at-httpbugzillaxamarincom"></a><a name="MT4114"/>方法的註冊機構 MT4114 未預期的錯誤 '\*。\*'-請在 http://bugzilla.xamarin.com 檔案錯誤報告



### <a name="a-namemt4116mt4116-could-not-register-the-assembly--"></a><a name="MT4116"/>MT4116 無法註冊組件 ' *': *



### <a name="a-namemt4117mt4117-the-registrar-found-a-signature-mismatch-in-the-method----the-selector-indicates-the-method-takes--parameters-while-the-managed-method-has--parameters"></a><a name="MT4117"/>MT4117 註冊機構不相符簽章中找到方法 '*。*'-選取器指出此方法採用 * 參數，而受管理的方法有 * 參數。



### <a name="a-namemt4118mt4118-cannot-register-two-managed-types--and--with-the-same-native-name-"></a><a name="MT4118"/>MT4118 無法註冊兩個受管理的類型 ('\*'和'\*') 與原始同名 ('* ')。



### <a name="a-namemt4119mt4119-could-not-register-the-selector--of-the-member--because-the-selector-is-already-registered-on-a-different-member"></a><a name="MT4119"/>MT4119 無法註冊選取器 '\*'的成員'\*。 *' 因為選取器已經註冊在不同的成員。



### <a name="a-namemt4120mt4120-the-registrar-found-an-unknown-field-type--in-field--please-file-a-bug-report-at-httpbugzillaxamarincom"></a><a name="MT4120"/>MT4120 登錄器找到未知的欄位型別 '\*'中欄位'\*。 *'。 請在 http://bugzilla.xamarin.com 檔案錯誤報告

此錯誤表示 Xamarin.iOS 中的 bug。 請檔案錯誤報告在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

### <a name="a-namemt4121mt4121-cannot-use-gccg-to-compile-the-generated-code-from-the-static-registrar-when-using-the-accounts-framework-the-header-files-provided-by-apple-used-during-the-compilation-require-clang-either-use-clang---compilerclang-or-the-dynamic-registrar---registrardynamic"></a><a name="MT4121"/>MT4121 不能使用 GCC / G + + 編譯靜態註冊機構所產生的程式碼時使用帳戶架構 （編譯期間使用的 Apple 提供的標頭檔需要 Clang）。 請使用 Clang (-編譯器： clang) 或動態的註冊機構 (-註冊機構： 動態)。



### <a name="a-namemt4122mt4122-cannot-use-the-clang-compiler-provided-in-the--sdk-to-compile-the-generated-code-from-the-static-registrar-when-non-ascii-type-names--are-present-in-the-application-either-use-gccg---compilergccg-the-dynamic-registrar---registrardynamic-or-a-newer-sdk"></a><a name="MT4122"/>MT4122 不能使用 Clang 編譯器中提供*。* SDK，以編譯靜態註冊機構非 ASCII 時從產生的程式碼型別名稱 ('* ') 會在應用程式。 請使用 GCC / G + + (-gcc 編譯器: | g + +)，動態註冊機構 (-註冊機構： 動態) 或更新版本的 SDK。



### <a name="a-namemt4123mt4123-the-type-of-the-variadic-parameter-in-the-variadic-function--must-be-systemintptr"></a><a name="MT4123"/>MT4123 Variadic 函式的 variadic 參數類型 ' *' 必須是 System.IntPtr。



### <a name="a-namemt4124mt4124-invalid--found-on--please-file-a-bug-report-at-httpbugzillaxamarincom"></a><a name="MT4124"/>MT4124 無效 * 上找到 ' *'。 請在 http://bugzilla.xamarin.com 檔案錯誤報告

此錯誤表示 Xamarin.iOS 中的 bug。 請檔案錯誤報告在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

### <a name="a-namemt4125mt4125-the-registrar-found-an-invalid-type--in-signature-for-method--the-interface-must-have-a-protocol-attribute-specifying-its-wrapper-type"></a><a name="MT4125"/>MT4125 登錄器找到無效的型別 '\*'中方法的簽章'\*': 介面必須要有通訊協定屬性，指定其包裝函式型別。



### <a name="a-namemt4126mt4126-cannot-register-two-managed-protocols--and--with-the-same-native-name-"></a><a name="MT4126"/>MT4126 無法註冊兩個受管理的通訊協定 ('\*'和'\*') 與原始同名 ('* ')。



### <a name="a-namemt4127mt4127-cannot-register-more-than-one-interface-method-for-the-method--which-is-implementing-"></a><a name="MT4127"/>MT4127 無法註冊之方法的多個介面方法 '\*' (這實作 '\*')。



### <a name="a-namemt4128mt4128--the-registrar-found-an-invalid-generic-parameter-type--in-the-method--the-generic-parameter-must-have-an-nsobject-constraint"></a><a name="MT4128"/>MT4128 登錄器找到無效的泛型參數型別 '\*'中方法'\*'。 泛型參數必須要有 'NSObject' 條件約束。

### <a name="a-namemt4129mt4129--the-registrar-found-an-invalid-generic-return-type--in-the-method--the-generic-return-type-must-have-an-nsobject-constraint"></a><a name="MT4129"/>MT4129 登錄器找到無效的泛型傳回類型 '\*'中方法'\*'。 泛型傳回類型必須有 'NSObject' 條件約束。

### <a name="a-namemt4130mt4130--the-registrar-cannot-export-static-methods-in-generic-classes-"></a><a name="MT4130"/>MT4130 登錄器無法匯出泛型類別中的靜態方法 ('* ')。

### <a name="a-namemt4131mt4131--the-registrar-cannot-export-static-properties-in-generic-classes-"></a><a name="MT4131"/>MT4131 登錄器無法匯出泛型類別中的靜態屬性 ('\*。\*')。

### <a name="a-namemt4132mt4132--the-registrar-found-an-invalid-generic-return-type--in-the-property--the-return-type-must-have-an-nsobject-constraint"></a><a name="MT4132"/>MT4132 登錄器找到無效的泛型傳回類型 '\*'中屬性'\*'。 傳回的類型必須有 'NSObject' 條件約束。

### <a name="a-namemt4133mt4133--cannot-construct-an-instance-of-the-type--from-objective-c-because-the-type-is-generic-runtime-exception"></a><a name="MT4133"/>MT4133 無法建構類型的執行個體 ' *' 從 OBJECTIVE-C 因為類型是泛型。 [執行階段例外狀況]

### <a name="a-namemt4134mt4134--your-application-is-using-the--framework-which-isnt-included-in-the-ios-sdk-youre-using-to-build-your-app-this-framework-was-introduced-in-ios--while-youre-building-with-the-ios--sdk-please-select-a-newer-sdk-in-your-apps-ios-build-options"></a><a name="MT4134"/>MT4134 應用程式使用 ' *' 架構，其不包含在 iOS SDK 您用來建置應用程式 (iOS 中引進這個架構 *，而您要建置使用 iOS * SDK。)請在您的應用程式的 iOS 組建選項，選取較新的 SDK。

### <a name="a-namemt4135mt4135--the-member--has-an-export-attribute-that-doesnt-specify-a-selector-a-selector-is-required"></a><a name="MT4135"/>MT4135 成員 '\*。\*' 有未指定選取器的匯出屬性。 需要選取器。

### <a name="a-namemt4136mt4136--the-registrar-cannot-marshal-the-parameter-type--of-the-parameter--in-the-method-"></a><a name="MT4136"/>MT4136 登錄器無法封送處理參數型別 '\*'的參數'\*'中方法'\*。 *'

<!-- MT4137 is unused -->

### <a name="a-namemt4138mt4138--the-registrar-cannot-marshal-the-property-type--of-the-property-"></a><a name="MT4138"/>MT4138 登錄器無法封送處理屬性型別 '\*'的屬性'\*。 *'。

### <a name="a-namemt4139mt4139--the-registrar-cannot-marshal-the-property-type--of-the-property--properties-with-the-connect-attribute-must-have-a-property-type-of-nsobject-or-a-subclass-of-nsobject"></a><a name="MT4139"/>MT4139 登錄器無法封送處理屬性型別 '\*'的屬性'\*。 *'。 使用 [連線] 屬性的內容必須有 NSObject 的屬性類型 （或 NSObject 的子類別）。

### <a name="a-namemt4140mt4140--the-registrar-found-a-signature-mismatch-in-the-method----the-selector-indicates-the-variadic-method-takes--parameters-while-the-managed-method-has--parameters"></a><a name="MT4140"/>MT4140 註冊機構不相符簽章中找到方法 '*。*'-選取器表示 variadic 方法會採用 * 參數，而受管理的方法有 * 參數。

### <a name="a-namemt4141mt4141--cannot-register-the-selector--on-the-member--because-xamarinios-implicitly-registers-this-selector"></a><a name="MT4141"/>MT4141 無法註冊選取器 '\*'上成員'\*' 因為 Xamarin.iOS 隱含註冊這個選取器。

這發生在子類別化架構的型別，而且嘗試實作 '保留'，'版本' 或 'dealloc' 方法：

```csharp
class MyNSObject : NSObject
{
    [Export ("retain")]
    new void Retain () {}

    [Export ("release")]
    new void Release () {}

    [Export ("dealloc")]
    new void Dealloc () {}
}
```

它是架構的不過可能覆寫這些方法，如果類別不是架構的第一個子類別類型：

```csharp
class MyNSObject : NSObject
{
}

class MyCustomNSObject : MyNSObject
{
    [Export ("retain")]
    new void Retain () {}

    [Export ("release")]
    new void Release () {}

    [Export ("dealloc")]
    new void Dealloc () {}
}
```

在此情況下將會覆寫 Xamarin.iOS `retain`，`release`和`dealloc`上`MyNSObject`類別，而且沒有任何衝突。

### <a name="a-namemt4142mt4142-failed-to-register-the-type-"></a><a name="MT4142"/>MT4142： 無法註冊類型 ' *'。
### <a name="a-namemt4143mt4143-the-objectivec-class--could-not-be-registered-it-does-not-seem-to-derive-from-any-known-objectivec-class-including-nsobject"></a><a name="MT4143"/>MT4143: ObjectiveC 類別 ' *' 無法註冊，它似乎不是衍生自 （包括 NSObject） 任何已知 ObjectiveC 類別。
### <a name="a-namemt4144mt4144-cannot-register-the-method--since-it-does-not-have-an-associated-trampoline-please-file-a-bug-report-at-httpbugzillaxamarincom"></a><a name="MT4144"/>MT4144： 無法登錄的方法 ' *' 因為沒有相關聯的 trampoline。 請在 http://bugzilla.xamarin.com 檔案錯誤報告。

這表示 Xamarin.iOS 中的 bug。 請將 bug 歸檔在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

### <a name="a-namemt4145mt4145-invalid-enum--enums-with-the-native-attribute-must-have-a-underlying-enum-type-of-either-long-or-ulong"></a><a name="MT4145"/>MT4145： 無效的列舉 ' *': 具有 [機器碼] 屬性的列舉類型必須是基礎列舉 'long' 或 'ulong'。
### <a name="a-namemt4146mt4146-the-name-parameter-of-the-registrar-attribute-on-the-class---contains-an-invalid-character--"></a><a name="MT4146"/>MT4146: Name 參數的註冊機構上的屬性類別\*'('\*') 包含無效的字元:'\*' (\*)。

Objectice C 類別的名稱不能包含空白字元，這表示`Register`對應的 managed 類別上的屬性不能有`Name`參數不能是包含空白字元。

請確認`Register`錯誤訊息中所述的 managed 類別上的屬性未包含任何空白字元。

### <a name="a-namemt4147mt4147-detected-a-protocol-inheriting-from-the-jsexport-protocol-while-using-the-dynamic-registrar-it-is-not-possible-to-export-protocols-to-javascriptcore-dynamically-the-static-registrar-must-be-used-add---registrarstatic-to-the-additional-mtouch-arguments-in-the-projects-ios-build-options-to-select-the-static-registrar"></a><a name="MT4147"/>MT4147： 偵測到使用動態註冊機構時繼承 JSExport 通訊協定的通訊協定。 不可能將通訊協定動態; 匯出至 JavaScriptCore必須使用靜態的註冊機構 (加入 '-註冊機構： 靜態中專案的 iOS 組建選項，以選取靜態註冊機構的其他 mtouch 引數)。
### <a name="a-namemt4148mt4148-the-registrar-found-a-generic-protocol--exporting-generic-protocols-is-not-supported"></a><a name="MT4148"/>MT4148: 註冊機構找到一般通訊協定: ' *'。 不支援匯出一般通訊協定。
### <a name="a-namemt4149mt4149-cannot-register-the-method--because-the-type-of-the-first-parameter--does-not-match-the-category-type-"></a><a name="MT4149"/>MT4149： 無法登錄的方法 '\*。\*' 因為第一個參數的型別 ('\*') 不符合類別類型 ('\*')。
### <a name="a-namemt4150mt4150-cannot-register-the-type--because-the-type-property--in-its-category-attribute-does-not-inherit-from-nsobject"></a><a name="MT4150"/>MT4150： 無法註冊類型 '\*' 因為型別屬性 ('\*') 在其類別屬性不是繼承自 NSObject。
### <a name="a-namemt4151mt4151-cannot-register-the-type--because-the-type-property-in-its-category-attribute-isnt-set"></a><a name="MT4151"/>MT4151： 無法註冊類型 ' *' 因為沒有設定其類別屬性中的類型屬性。
### <a name="a-namemt4152mt4152-cannot-register-the-type--as-a-category-because-it-implements-inativeobject-or-subclasses-nsobject"></a><a name="MT4152"/>MT4152： 無法註冊類型 ' *' 做為類別目錄因為它實作 INativeObject 或 NSObject 子類別。
### <a name="a-namemt4153mt4153-cannot-register-the-type--as-a-category-because-its-generic"></a><a name="MT4153"/>MT4153： 無法註冊類型 '\*' 做為類別目錄因為它是泛型。
### <a name="a-namemt4154mt4154-cannot-register-the-method--as-a-category-method-because-its-generic"></a><a name="MT4154"/>MT4154： 無法登錄的方法 '\*' 做為類別方法因為它是泛型。
### <a name="a-namemt4155mt4155-cannot-register-the-method--with-the-selector--as-a-category-method-on--because-the-objective-c-already-has-an-implementation-for-this-selector"></a><a name="MT4155"/>MT4155： 無法登錄的方法 '\*'with selector'\*' 上的分類方法為 ' *' Objective C 上已有這個選取器的實作。
### <a name="a-namemt4156mt4156-cannot-register-two-categories--and--with-the-same-native-name-"></a><a name="MT4156"/>MT4156： 無法註冊兩個類別 ('\*'和'\*') 與原始同名 ('* ')。
### <a name="a-namemt4157mt4157-cannot-register-the-category-method--because-at-least-one-parameter-is-required-and-its-type-must-match-the-category-type-"></a><a name="MT4157"/>MT4157： 無法註冊類別方法 '\*' 需要至少一個參數，因此 (和其類型必須符合類別類型 '\*')
### <a name="a-namemt4158mt4158-cannot-register-the-constructor--in-the-category--because-constructors-in-categories-are-not-supported"></a><a name="MT4158"/>MT4158： 無法註冊建構函式 * 類別目錄中 * 因為不支援類別的建構函式。
### <a name="a-namemt4159mt4159-cannot-register-the-method--as-a-category-method-because-category-methods-must-be-static"></a><a name="MT4159"/>MT4159： 無法登錄的方法 ' *' 做為類別方法因為類別方法必須是靜態。

### <a name="a-namemt4160mt4160-invalid-character---found-in-selector--for-"></a><a name="MT4160"/>MT4160： 無效的字元 '\*' (\*) 位於選取器 '\*'for'\*'。

### <a name="a-namemt4161mt4161-the-registrar-found-an-unsupported-structure--all-fields-in-a-structure-must-also-be-structures-field--with-type-2-is-not-a-structure"></a><a name="MT4161"/>MT4161: 註冊機構找到不支援的結構 '\*': 在結構中的所有欄位也必須都是結構 (欄位'\*' 與型別 '{2}' 不是結構)。

在註冊機構找到具有不支援欄位的結構。

公開給 Objective C 的結構中的所有欄位也必須都是結構 （不是類別）。

### <a name="a-namemt4162mt4162-the-type--used-as--2-is-not-available-in---it-was-introduced-in---please-build-with-a-newer--sdk-usually-done-by-using-the-most-recent-version-of-xcode"></a><a name="MT4162"/>MT4162： 型別 '\*' (做為 * {2}) 不適用於 * * (中所導入 * *)\*請使用較新建置 * SDK （通常是由使用 Xcode 的最新版本完成。

在註冊機構找到不在目前的 SDK 中的類型。

請升級 Xcode。

### <a name="a-namemt4163mt4163-internal-error-in-the-registrar--please-file-a-bug-report-at-httpbugzillaxamarincom"></a><a name="MT4163"/>MT4163： 發生內部錯誤的註冊機構 （*）。 請在 http://bugzilla.xamarin.com 檔案錯誤報告

此錯誤表示 Xamarin.iOS 中的 bug。 請檔案錯誤報告在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

### <a name="a-namemt4164mt4164-cannot-export-the-property--because-its-selector--is-an-objective-c-keyword-please-use-a-different-name"></a><a name="MT4164"/>MT4164： 無法匯出的屬性 '\*' 因為其選取器 '\*' Objective C 關鍵字。 請使用不同的名稱。

選取器所討論的屬性不是有效的 Objective C 識別項。

請為選取器，使用有效的 Objective C 識別項。

### <a name="a-namemt4165mt4165-the-registrar-couldnt-find-the-type-systemvoid-in-any-of-the-referenced-assemblies"></a><a name="MT4165"/>MT4165: 註冊機構找不到類型 'System.Void' 在任何參考的組件中。

這項錯誤最可能指出 Xamarin.iOS 中的 bug。 請檔案錯誤報告在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

### <a name="a-namemt4166mt4166-cannot-register-the-method--because-the-signature-contains-a-type--that-isnt-a-reference-type"></a><a name="MT4166"/>MT4166： 無法登錄的方法 '\*' 因為簽章包含的類型 (\*)，這並不是參考型別。

這通常表示 Xamarin.iOS; 中的 bug請將 bug 歸檔在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

### <a name="a-namemt4167mt4167-cannot-register-the-method--because-the-signature-contains-a-generic-type--with-a-generic-argument-type-that-isnt-an-nsobject-subclass-"></a><a name="MT4167"/>MT4167： 無法登錄的方法 '\*' 因為簽章包含泛型型別 (\*) 與泛型引數型別不是 NSObject 子類別 （*）。

這通常表示 Xamarin.iOS; 中的 bug請將 bug 歸檔在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

### <a name="a-namemt4168mt4168-cannot-register-the-type-managedname-because-its-objective-c-name-exportedname-is-an-objective-c-keyword-please-use-a-different-name"></a><a name="MT4168"/>MT4168： 無法註冊類型 '{管理\_name}' 因為其 Objective C 的名稱' {匯出\_名稱}' 是 Objective C 關鍵字。 請使用不同的名稱。

Objective C 的名稱有問題的類型不是有效的 Objective C 識別項。

請使用有效的 Objective C 識別項。

## <a name="mt5xxx-gcc-and-toolchain-error-messages"></a>MT5xxx: GCC 和工具鏈錯誤訊息

### <a name="mt51xx-compilation"></a>MT51xx： 編譯

<!--
 MT5xxx GCC and toolchain
  MT51xx compilation
  -->

### <a name="a-namemt5101mt5101-missing--compiler-please-install-xcode-command-line-tools-component"></a><a name="MT5101"/>MT5101 遺漏 ' *' 編譯器。 請安裝 Xcode '命令列工具' 元件



### <a name="a-namemt5102mt5102-failed-to-assemble-the-file--please-file-a-bug-report-at-httpbugzillaxamarincom"></a><a name="MT5102"/>無法組合檔案 MT5102 ' *'。 請在 http://bugzilla.xamarin.com 檔案錯誤報告



### <a name="a-namemt5103mt5103-failed-to-compile-the-file--please-file-a-bug-report-at-httpbugzillaxamarincom"></a><a name="MT5103"/>無法將檔案編譯 MT5103 ' *'。 請在 http://bugzilla.xamarin.com 檔案錯誤報告



### <a name="a-namemt5104mt5104-could-not-find-neither-the--nor-the--compiler-please-install-xcode-command-line-tools-component"></a><a name="MT5104"/>MT5104 找不到兩者皆非 '\*'或'\*' 編譯器。 請安裝 Xcode '命令列工具' 元件

<!-- 5105 is used by mmp -->

### <a name="a-namemt5106mt5106-could-not-compile-the-files--please-file-a-bug-report-at-httpbugzillaxamarincom"></a><a name="MT5106"/>MT5106： 無法編譯檔案 ' *'。 請在 http://bugzilla.xamarin.com 檔案錯誤報告

這通常表示 Xamarin.iOS; 中的 bug請將 bug 歸檔在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

### <a name="mt52xx-linking"></a>MT52xx： 連結

<!--
  MT52xx linking
  -->

### <a name="a-namemt5201mt5201-native-linking-failed-please-review-the-build-log-and-the-user-flags-provided-to-gcc-"></a><a name="MT5201"/>原生 MT5201 連結失敗。 請參閱組建記錄檔並提供給 gcc 使用者旗標: *

### <a name="a-namemt5202mt5202-native-linking-failed-please-review-the-build-log"></a><a name="MT5202"/>原生 MT5202 連結失敗。 請檢閱組建記錄檔。

### <a name="a-namemt5203mt5203-native-linking-warning-"></a><a name="MT5203"/>MT5203 原生連結警告: *

<!--- 5204-5208 are not used -->

### <a name="a-namemt5209mt5209-native-linking-error-"></a><a name="MT5209"/>MT5209 原生連結時發生錯誤: *

### <a name="a-namemt5210mt5210-native-linking-failed-undefined-symbol--please-verify-that-all-the-necessary-frameworks-have-been-referenced-and-native-libraries-are-properly-linked-in"></a><a name="MT5210"/>MT5210： 原生連結失敗，未定義的符號: *。 請確認所有必要的架構有參考，而且原生程式庫的正確連結。

這會發生原生連結器找不到某個位置所參考的符號。 有數個發生此情形的原因：

* 第三方繫結需要架構，但繫結未指定在其`[LinkWith]`屬性。 解決方案：
  - 如果您是第三方，繫結的作者或存取至其來源，修改繫結的`[LinkWith]`納入架構需要的屬性：

            [LinkWith ("mylib.a", Frameworks = "SystemConfiguration")]

  - 如果您無法修改的協力廠商的繫結，您可以手動連結與必要的 framework 藉由傳遞`-gcc_flags '-framework SystemFramework'`至`mtouch`（這是藉由修改專案的 iOS 組建選項 頁面中的其他 mtouch 引數。 請記住這必須完成每個專案組態）。
* 在某些情況下，受管理的繫結組成幾個原生程式庫，而且所有必須包含在繫結。 很可能有一個以上的原生程式庫中每個繫結專案，因此解決方法是將所有必要原生程式庫加入至繫結專案。</li>
* 受管理的繫結是指不存在於原生程式庫中的原生符號。
    當繫結已存在於段時間，而原生程式碼已在這段時間，讓特定的原生類別已移除或重新命名，而不更新繫結時，就會發生這種情形。
* P/Invoke 參考不存在的原生符號。 開頭為 Xamarin.iOS 7.4 <a href="#MT5214">MT5214</a>此案例將會報告錯誤 （如需詳細資訊，請參閱 MT5214）。
* 第三方繫結 / 使用 c + + 建置程式庫，但繫結未指定在其`[LinkWith]`屬性。 這通常是相當容易識別，因為符號會損害的 c + + 符號 (其中一個常見範例是`__ZNKSt9exception4whatEv`)。
  - 如果您是第三方，繫結的作者或存取至其來源，修改繫結的`[LinkWith]`要設定屬性`IsCxx`旗標：

            [LinkWith ("mylib.a", IsCxx = true)]

  - 如果您不能修改的第三方繫結，或您以手動方式連結的協力廠商程式庫，您可以藉由傳遞設定對等的旗標<code>-cxx</code>至 mtouch （這是藉由修改專案的 iOS 組建選項 頁面中的其他 mtouch 引數. 請記住這必須完成每個專案組態）。



### <a name="a-namemt5211mt5211-native-linking-failed-undefined-objective-c-class--the-symbol--could-not-be-found-in-any-of-the-libraries-or-frameworks-linked-with-your-application"></a><a name="MT5211"/>MT5211： 原生連結失敗，未定義的 Objective C 類別： \*。 符號 '\*' 找不到的任何程式庫或連結到您的應用程式的架構。

這會發生原生連結器找不到某個位置參考 OBJECTIVE-C 類別。 發生此情形的原因如下： 一樣[MT5210](#MT5210)和此外：

* 協力廠商的繫結繫結為 OBJECTIVE-C 通訊協定，但未不加上註解它與<code>[Protocol]</code>在其應用程式開發介面定義中的屬性。 解決方案：
  - 加入遺漏`[Protocol]`屬性：

              [BaseType (typeof (NSObject))]
              [Protocol] // Add this
              public interface MyProtocol
              {
              }



### <a name="a-namemt5212mt5212-native-linking-failed-duplicate-symbol-"></a><a name="MT5212"/>MT5212： 原生連結失敗，重複的符號: *。

這會發生原生連結器在遇到重複的符號之間的所有原生程式庫。 下列這個錯誤可能有一或多個[MT5213](#MT5213)錯誤的每個項目符號的位置。 此錯誤的可能原因：


* 相同的原生程式庫則包含兩次。
* 若要定義相同的符號，就可能發生兩個不同的原生程式庫。
* 原生程式庫未正確建置，並一次以上包含相同的符號。
  您可以使用下列的終端機的命令來確認此 （將 i386 取代 x86_64/armv7/armv7s/arm64 根據哪一個架構中，您要建置的）：

        # Native libraries are usually fat libraries, containing binary code for
        # several architectures in the same file. First we extract the binary
        # code for the architecture we're interested in.
        lipo libNative.a -thin i386 -output libNative.i386.a

        # Now query the native library for the duplicated symbol.
        nm libNative.i386.a | fgrep 'SYMBOL'

        # You can also list the object files inside the native library.
        # In most cases this will reveal duplicated object files.
        ar -t libNative.i386.a

  有幾個可能的方法，若要修正此問題：

  - 要求的原生程式庫提供者修正此問題，並提供更新的版本。
  - 修正問題，請自行移除額外的物件檔案 （這只適用於問題實際上是重複的目的檔）


            # Find out if the library is a fat library, and which
            # architectures it contains.
            lipo -info libNative.a

            # Extract each architecture (i386/x86_64/armv7/armv7s/arm64) to a separate file
            lipo libNative.a -thin ARCH -output libNative.ARCH.a

            # Extract the object files for the offending architecture
            # This will remove the duplicates by overwriting them
            # (since they have the same filename)
            mkdir -p ARCH
            cd ARCH
            ar -x ../libNative.ARCH.a

            # Reassemble the object files in an .a
            ar -r ../libNative.ARCH.a *.o
            cd ..

            # Reassemble the fat library
            lipo *.a -create -output libNative.a


  - 要求連結器，以移除未使用的程式碼。 Xamarin.iOS 會自動執行此動作如果符合所有下列條件：
    - 所有協力廠商繫結`[LinkWith]`屬性已啟用 SmartLink:

            [assembly: LinkWith ("libNative.a", SmartLink = true)]

    - 否`-gcc_flags`傳遞至 mtouch （在專案的 iOS 建置選項的其他 mtouch 引數欄位）。
    - 您也可向連結器直接新增移除未使用的程式碼`-gcc_flags -dead_strip`成其他 mtouch 引數，在專案的 iOS 建置選項。


### <a name="a-namemt5213mt5213-duplicate-symbol-in--location-related-to-previous-error"></a><a name="MT5213"/>MT5213： 重複的符號: * （先前的錯誤相關的位置）

會報告此錯誤只搭配[MT5212](#MT5212)。 請參閱[MT5212](#MT5212)如需詳細資訊。

### <a name="a-namemt5214mt5214--native-linking-failed-undefined-symbol--this-symbol-was-referenced-the-managed-member--please-verify-that-all-the-necessary-frameworks-have-been-referenced-and-native-libraries-linked"></a><a name="MT5214"/>MT5214 原生連結失敗，未定義符號: *。 這個符號參考 managed 的成員 *。 請確認所有必要的架構已參考和原生程式庫連結。

當 managed 程式碼包含 P/Invoke 不存在的原生方法時，會報告此錯誤。 例如: 

```csharp
using System.Runtime.InteropServices;
class MyImports {
   [DllImport ("__Internal")]
   public static extern void MyInexistentFunc ();
}
```

有幾個可能的解決方案：

  -  移除有問題 P/Invokes 的原始程式碼。
  -  可讓受管理的連結器 （這是在專案的 iOS 建置選項中的 「 連結器行為 」 設為"的所有組件 」） 的所有組件。 這會有效移除所有 P/Invokes 不使用應用程式 （自動，而不是以手動方式如先前的點）。 這個選項的缺點是，這可以讓模擬器組建上稍微慢，而且如果它使用反映-可以找到連結器的詳細資訊，它可能會中斷您的應用程式[這裡](~/ios/deploy-test/linker.md))
  -  建立第二個原生程式庫包含遺失的原生符號。 請注意，這只是因應措施 （如果您嘗試將呼叫這些函式，您的應用程式將會損毀）。

### <a name="a-namemt5215mt5215-references-to--might-require-additional--frameworkxxx-or--lxxx-instructions-to-the-native-linker"></a><a name="MT5215"/>MT5215： 參考 ' *' 可能會需要額外的 framework = XXX 或-lXXX 指示原生連結器

這是警告，表示參考的程式庫有問題，偵測到將 P/Invoke，但不是與其連結的應用程式。

### <a name="a-namemt5216mt5216-native-linking-failed-for--please-file-a-bug-report-at-httpbugzillaxamarincom"></a><a name="MT5216"/>MT5216: 原生連結失敗 *。 請在 http://bugzilla.xamarin.com 檔案錯誤報告

連結 AOT 編譯器的輸出時，會報告此錯誤。

這項錯誤最可能指出 Xamarin.iOS 中的 bug。 請檔案錯誤報告在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

### <a name="a-namemt5217mt5217-native-linking-possibly-failed-because-the-linker-command-line-was-too-long--characters"></a><a name="MT5217"/>MT5217： 可能是原生連結失敗，因為連結器命令列太長 (* 字元)。

原生連結失敗，而且可能發生這種因為連結器命令是太長。

Xamarin.iOS 專案會經常參考原生符號，以動態方式表示原生連結器可能會移除這類的原生符號，在原生連結過程中，因為原生連結器不會看到這些符號可用。

通常 Xamarin.iOS 會詢問要保留這類符號使用原生連結器`-u symbol`連結器旗標，但如果有許多這類符號，將整個命令列可能會超出作業系統所指定的命令列長度上限。

有幾個可能的來源，這類動態符號：

* P/Invokes 移到靜態連結程式庫中的方法 (其中是 dll 名稱`__Internal`DllImport 屬性中`[DllImport ("__Internal")]`)。
* 欄位的繫結專案參考靜態連結程式庫中的記憶體位置 (`[Field]`屬性)。
* Objective C 類別中靜態連結程式庫 （使用累加建置時或在不使用靜態的註冊機構） 繫結專案參考。

可能的解決方案：

* 可讓受管理的連結器 （如果所有的組件，而不是唯一的 SDK 組件可能的話）。 動態符號使連結器的命令列不會超過最大值，這可能會移除足夠的來源。
* 減少 P/Invokes、 欄位參考及/或 OBJECTIVE-C 類別的數目。
* 請重寫動態的符號，以較短的名稱。
* 傳遞`-dlsym:false`做為其他 mtouch 引數，在專案的 iOS 建置選項。 使用此選項，Xamarin.iOS 會產生原生參考 AOT 編譯的程式碼，並不需要向連結器，以保留這個符號。 不過，這只適用於裝置建置，而且有 P/Invokes 不存在於靜態程式庫中的函式會導致連結器錯誤。
* 傳遞`--dynamic-symbol-mode=code`做為其他 mtouch 引數，在專案的 iOS 建置選項。 使用此選項，Xamarin.iOS 會產生額外的原生程式碼參考這些符號，而不是要求原生的連結器，以保留這些符號使用命令列引數。 這種方法的缺點是，它將會稍微增加可執行檔的大小。
* 藉由傳遞啟用靜態註冊機構`--registrar:static`做為其他 mtouch 引數，在專案的 iOS 組建選項 （適用於模擬器建置，因為靜態註冊機構已經是預設值為裝置組建）。 靜態註冊機構會產生靜態方式參考 OBJECTIVE-C 類別的程式碼，因此不需要向原生的連結器，以保留這類類別。
* 停用累加建置 （針對裝置組建）。 累加建置已啟用時，靜態的註冊機構所產生的程式碼將不會視為原生連結器，表示 Xamarin.iOS 仍然必須要求連結器，以保留參考 Objective C 的類別。 因此停用累加建置將會導致這項需求。

### <a name="a-namemt5218mt5218-cant-ignore-the-dynamic-symbol-symbol---ignore-dynamic-symbolsymbol-because-it-was-not-detected-as-a-dynamic-symbol"></a><a name="MT5218"/>MT5218： 無法略過動態符號 {symbol} (-忽略動態符號 = {符號}) 因為它未偵測到做為動態的符號。

命令列引數`--ignore-dynamic-symbol=symbol`已通過，但這個符號不是動態的符號必須以手動方式保存為已辨識的符號。

有兩個主要的原因：

* 符號名稱不正確。
    * 不在前面加上底線的符號名稱。
    * Objective C 類別的符號是`OBJC_CLASS_$_<classname>`。
* 符號是正確的但已經保留以正常方式 （某些組建選項原因來更改動態符號的清單） 的符號。

### <a name="mt53xx-other-tools"></a>其他工具 MT53xx:

<!--
  MT53xx other tools
  -->

### <a name="a-namemt5301mt5301-missing-strip-tool-please-install-xcode-command-line-tools-component"></a><a name="MT5301"/>MT5301： 遺漏 '帶狀' 工具。 請安裝 Xcode '命令列工具' 元件



### <a name="a-namemt5302mt5302-missing-dsymutil-tool-please-install-xcode-command-line-tools-component"></a><a name="MT5302"/>MT5302： 遺漏 'dsymutil' 工具。 請安裝 Xcode '命令列工具' 元件



### <a name="a-namemt5303mt5303-failed-to-generate-the-debug-symbols-dsym-directory-please-review-the-build-log"></a><a name="MT5303"/>MT5303： 無法產生偵錯符號 （dSYM 目錄）。 請檢閱組建記錄檔。

若要建立偵錯符號的最終.app 目錄上執行 dsymutil 時，就會發生錯誤。 請檢閱組建記錄檔以查看 dsymutil 的輸出，以及查看如何修正它。

### <a name="a-namemt5304mt5304-failed-to-strip-the-final-binary-please-review-the-build-log"></a><a name="MT5304"/>MT5304： 無法刪除最終的二進位檔。 請檢閱組建記錄檔。

執行 '帶狀' 工具來移除應用程式的偵錯資訊時，就會發生錯誤。

### <a name="a-namemt5305mt5305-missing-lipo-tool-please-install-xcode-command-line-tools-component"></a><a name="MT5305"/>MT5305： 遺漏 'lipo' 工具。 請安裝 Xcode '命令列工具' 元件



### <a name="a-namemt5306mt5306-failed-to-create-the-a-fat-library-please-review-the-build-log"></a><a name="MT5306"/>MT5306： 無法建立 fat 文件庫。 請檢閱組建記錄檔。

執行 'lipo' 工具時，就會發生錯誤。 請檢閱組建記錄檔以查看 'lipo' 回報的錯誤。

### <a name="a-namemt5307mt5307-failed-to-sign-the-executable-please-review-the-build-log"></a><a name="MT5307"/>MT5307： 無法註冊可執行檔。 請檢閱組建記錄檔。

登入應用程式時，就會發生錯誤。 請檢閱組建記錄檔以查看 'codesign' 回報的錯誤。

<!-- 5308 is used by mmp -->
<!-- 5309 is used by mmp -->
<!-- 5310 is used by mmp -->

## <a name="mt6xxx-mtouch-internal-tools-error-messages"></a>MT6xxx: mtouch 內部工具錯誤訊息

### <a name="mt600x-stripper"></a>MT600x： 清除

<!--
 MT6xxx mtouch internal tools
  MT600x Stripper
  -->

### <a name="a-namemt6001mt6001-running-version-of-cecil-doesnt-support-assembly-stripping"></a><a name="MT6001"/>MT6001: Cecil 執行版本不支援移除組件

### <a name="a-namemt6002mt6002-could-not-strip-assembly-"></a><a name="MT6002"/>MT6002： 可能不會刪除組件`*`。

應用程式中移除 managed 程式碼 （移除 IL 程式碼） 的組件時發生錯誤。

### <a name="a-namemt6003mt6003-unauthorizedaccessexception-message"></a><a name="MT6003"/>MT6003: [UnauthorizedAccessException 訊息]

安全性時，發生錯誤條狀配置偵錯符號，從應用程式。

## <a name="mt7xxx-msbuild-error-messages"></a>MT7xxx: MSBuild 錯誤訊息

<!--
 MT7xxx msbuild errors
  -->

### <a name="a-namemt7001mt7001-could-not-resolve-host-ips-for-wifi-debugger-settings"></a><a name="MT7001"/>MT7001： 無法解析主機 Ip WiFi 偵錯工具設定。

*MSBuild 工作： DetectDebugNetworkConfigurationTaskBase*

疑難排解步驟：

- 嘗試執行`csharp -e 'System.Net.Dns.GetHostEntry (System.Net.Dns.GetHostName ()).AddressList'`（這應該可以讓您的 IP 位址而非錯誤很明顯）。
- 嘗試執行 「 ping \`hostname\`"這可讓您的詳細資訊，例如： `cannot resolve MyHost.local: Unknown host`

在某些情況下，這是 「 本機網路 」 問題，而且可以藉由新增未知的主機處理`127.0.0.1   MyHost.local`中`/etc/hosts`。

### <a name="a-namemt7002mt7002-this-machine-does-not-have-any-network-adapters-this-is-required-when-debugging-or-profiling-on-device-over-wifi"></a><a name="MT7002"/>MT7002： 這部電腦沒有任何網路介面卡。 偵錯或分析裝置上透過 WiFi 時，這是必要的。

*MSBuild 工作： DetectDebugNetworkConfigurationTaskBase*

### <a name="a-namemt7003mt7003-the-app-extension--does-not-contain-an-infoplist"></a><a name="MT7003"/>MT7003: 應用程式擴充功能 ' *' 不包含 Info.plist。

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7004mt7004-the-app-extension--does-not-specify-a-cfbundleidentifier"></a><a name="MT7004"/>MT7004: 應用程式擴充功能 ' *' 未指定 CFBundleIdentifier。

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7005mt7005-the-app-extension--does-not-specify-a-cfbundleexecutable"></a><a name="MT7005"/>MT7005: 應用程式擴充功能 ' *' 未指定 CFBundleExecutable。

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7006mt7006-the-app-extension--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a><a name="MT7006"/>MT7006: 應用程式擴充功能 '\*' 有無效的 CFBundleIdentifier (\*)，它不是主要的應用程式套件組合的 CFBundleIdentifier （*）。

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7007mt7007-the-app-extension--has-a-cfbundleidentifier--that-ends-with-the-illegal-suffix-key"></a><a name="MT7007"/>MT7007: 應用程式擴充功能 '\*' 具有 CFBundleIdentifier (\*) 的不合法的後置詞".key 」 以結束。

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7008mt7008-the-app-extension--does-not-specify-a-cfbundleshortversionstring"></a><a name="MT7008"/>MT7008: 應用程式擴充功能 ' *' 未指定 CFBundleShortVersionString。

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7009mt7009-the-app-extension--has-an-invalid-infoplist-it-does-not-contain-an-nsextension-dictionary"></a><a name="MT7009"/>MT7009: 應用程式擴充功能 ' *' 有無效的 Info.plist： 它不包含 NSExtension 字典。

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7010mt7010-the-app-extension--has-an-invalid-infoplist-the-nsextension-dictionary-does-not-contain-an-nsextensionpointidentifier-value"></a><a name="MT7010"/>MT7010: 應用程式擴充功能 ' *' 有無效的 Info.plist: NSExtension 字典未包含 NSExtensionPointIdentifier 值。

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7011mt7011-the-watchkit-extension--has-an-invalid-infoplist-the-nsextension-dictionary-does-not-contain-an-nsextensionattributes-dictionary"></a><a name="MT7011"/>MT7011: WatchKit 副檔名 ' *' 有無效的 Info.plist: NSExtension 字典未包含 NSExtensionAttributes 字典。

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7012mt7012-the-watchkit-extension--does-not-have-exactly-one-watch-app"></a><a name="MT7012"/>MT7012: WatchKit 副檔名 ' *' 沒有剛好一個監看式應用程式。

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7013mt7013-the-watchkit-extension--has-an-invalid-infoplist-uirequireddevicecapabilities-must-contain-the-watch-companion-capability"></a><a name="MT7013"/>MT7013: WatchKit 副檔名 '*' 有無效的 Info.plist: UIRequiredDeviceCapabilities 必須包含' 監看式附屬 ' 功能。

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7014mt7014-the-watch-app--does-not-contain-an-infoplist"></a><a name="MT7014"/>MT7014： 監看式應用程式 ' *' 不包含 Info.plist。

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7015mt7015-the-watch-app--does-not-specify-a-cfbundleidentifier"></a><a name="MT7015"/>MT7015： 監看式應用程式 ' *' 未指定 CFBundleIdentifier。

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7016mt7016-the-watch-app--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a><a name="MT7016"/>MT7016： 監看式應用程式 '\*' 有無效的 CFBundleIdentifier (\*)，它不是主要的應用程式套件組合的 CFBundleIdentifier （*）。

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7017mt7017-the-watch-app--does-not-have-a-valid-uidevicefamily-value-expected-watch-4-but-found--"></a><a name="MT7017"/>MT7017： 監看式應用程式 '\*' 沒有有效的 UIDeviceFamily 值。 必須是 '監看式 (4)'，但找到 '\* （*） '。

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7018mt7018-the-watch-app--does-not-specify-a-cfbundleexecutable"></a><a name="MT7018"/>MT7018： 監看式應用程式 ' *' 未指定 CFBundleExecutable

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7019mt7019-the-watch-app--has-an-invalid-wkcompanionappbundleidentifier-value--it-does-not-match-the-main-app-bundles-cfbundleidentifier-"></a><a name="MT7019"/>MT7019： 監看式應用程式 '\*' 有無效的 WKCompanionAppBundleIdentifier 值 ('\*')，它不符合主要的應用程式套件組合的 CFBundleIdentifier ('* ')。

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7020mt7020-the-watch-app--has-an-invalid-infoplist-the-wkwatchkitapp-key-must-be-present-and-have-a-value-of-true"></a><a name="MT7020"/>MT7020： 監看式應用程式 ' *' 有無效的 Info.plist: WKWatchKitApp 金鑰必須存在並具有值為 'true'。

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7021mt7021-the-watch-app--has-an-invalid-infoplist-the-lsrequiresiphoneos-key-must-not-be-present"></a><a name="MT7021"/>MT7021： 監看式應用程式 ' *' 有無效的 Info.plist: LSRequiresIPhoneOS 金鑰不得存在。

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7022mt7022-the-watch-app--does-not-contain-a-watch-extension"></a><a name="MT7022"/>MT7022： 監看式應用程式 ' *' 不包含 Watch 擴充功能。

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7023mt7023-the-watch-extension--does-not-contain-an-infoplist"></a><a name="MT7023"/>MT7023: Watch 擴充功能 ' *' 不包含 Info.plist。

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7024mt7024-the-watch-extension--does-not-specify-a-cfbundleidentifier"></a><a name="MT7024"/>MT7024: Watch 擴充功能 ' *' 未指定 CFBundleIdentifier。

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7025mt7025-the-watch-extension--does-not-specify-a-cfbundleexecutable"></a><a name="MT7025"/>MT7025: Watch 擴充功能 ' *' 未指定 CFBundleExecutable。

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7026mt7026-the-watch-extension--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a><a name="MT7026"/>MT7026: Watch 擴充功能 '\*' 有無效的 CFBundleIdentifier (\*)，它不是主要的應用程式套件組合的 CFBundleIdentifier （*）。

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7027mt7027-the-watch-extension--has-a-cfbundleidentifier--that-ends-with-the-illegal-suffix-key"></a><a name="MT7027"/>MT7027: Watch 擴充功能 '\*' 具有 CFBundleIdentifier (\*) 的不合法的後置詞".key 」 以結束。

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7028mt7028-the-watch-extension--has-an-invalid-infoplist-it-does-not-contain-an-nsextension-dictionary"></a><a name="MT7028"/>MT7028: Watch 擴充功能 ' *' 有無效的 Info.plist： 它不包含 NSExtension 字典。

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7029mt7029-the-watch-extension--has-an-invalid-infoplist-the-nsextensionpointidentifier-must-be-comapplewatchkit"></a><a name="MT7029"/>MT7029: Watch 擴充功能 ' *' 有無效的 Info.plist: NSExtensionPointIdentifier 必須是"com.apple.watchkit"。

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7030mt7030-the-watch-extension--has-an-invalid-infoplist-the-nsextension-dictionary-must-contain-nsextensionattributes"></a><a name="MT7030"/>MT7030: Watch 擴充功能 ' *' 有無效的 Info.plist: NSExtension 字典必須包含 NSExtensionAttributes。

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7031mt7031-the-watch-extension--has-an-invalid-infoplist-the-nsextensionattributes-dictionary-must-contain-a-wkappbundleidentifier"></a><a name="MT7031"/>MT7031: Watch 擴充功能 ' *' 有無效的 Info.plist: NSExtensionAttributes 字典必須包含 WKAppBundleIdentifier。

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7032mt7032-the-watchkit-extension--has-an-invalid-infoplist-uirequireddevicecapabilities-should-not-contain-the-watch-companion-capability"></a><a name="MT7032"/>MT7032: WatchKit 副檔名 '*' 有無效的 Info.plist: UIRequiredDeviceCapabilities 不應包含' 監看式附屬 ' 功能。

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7033mt7033-the-watch-app--does-not-contain-an-infoplist"></a><a name="MT7033"/>MT7033： 監看式應用程式 ' *' 不包含 Info.plist。

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7034mt7034-the-watch-app--does-not-specify-a-cfbundleidentifier"></a><a name="MT7034"/>MT7034： 監看式應用程式 ' *' 未指定 CFBundleIdentifier。

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7035mt7035-the-watch-app--does-not-have-a-valid-uidevicefamily-value-expected--but-found--"></a><a name="MT7035"/>MT7035： 監看式應用程式 '\*' 沒有有效的 UIDeviceFamily 值。 必須是 '\*'但卻發現'\* (\*)'。

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7036mt7036-the-watch-app--does-not-specify-a-cfbundleexecutable"></a><a name="MT7036"/>MT7036： 監看式應用程式 ' *' 未指定 CFBundleExecutable。

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7037mt7037-the-watchkit-extension-extensionname-has-an-invalid-wkappbundleidentifier-value--it-does-not-match-the-watch-apps-cfbundleidentifier-"></a><a name="MT7037"/>MT7037: WatchKit 擴充功能 '{extensionName}' 有無效的 WKAppBundleIdentifier 值 ('\*')，它不符合監看式應用程式的 CFBundleIdentifier ('\*')。

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7038mt7038-the-watch-app--has-an-invalid-infoplist-the-wkcompanionappbundleidentifier-must-exist-and-must-match-the-main-app-bundles-cfbundleidentifier"></a><a name="MT7038"/>MT7038： 監看式應用程式 ' *' 有無效的 Info.plist: WKCompanionAppBundleIdentifier 必須存在，且必須符合主要的應用程式套件組合的 CFBundleIdentifier。

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7039mt7039-the-watch-app--has-an-invalid-infoplist-the-lsrequiresiphoneos-key-must-not-be-present"></a><a name="MT7039"/>MT7039： 監看式應用程式 ' *' 有無效的 Info.plist: LSRequiresIPhoneOS 金鑰不得存在。

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7040mt7040-the-app-bundle-appbundlepath-does-not-contain-an-infoplist"></a><a name="MT7040"/>MT7040： 應用程式套件組合 {AppBundlePath} 不包含 Info.plist。

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7041mt7041-main-infoplist-path-does-not-specify-a-cfbundleidentifier"></a><a name="MT7041"/>MT7041: Main Info.plist 路徑未指定 CFBundleIdentifier。

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7042mt7042-main-infoplist-path-does-not-specify-a-cfbundleexecutable"></a><a name="MT7042"/>MT7042: Main Info.plist 路徑未指定 CFBundleExecutable。

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7043mt7043-main-infoplist-path-does-not-specify-a-cfbundlesupportedplatforms"></a><a name="MT7043"/>MT7043: Main Info.plist 路徑未指定 CFBundleSupportedPlatforms。

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7044mt7044-main-infoplist-path-does-not-specify-a-uidevicefamily"></a><a name="MT7044"/>MT7044: Main Info.plist 路徑未指定 UIDeviceFamily。

*MSBuild 工作： ValidateAppBundleTaskBase*

### <a name="a-namemt7045mt7045-unrecognized-format-"></a><a name="MT7045"/>MT7045： 無法辨認的格式: *。

*MSBuild 工作： PropertyListEditorTaskBase*

其中 * 可以是：

- 字串
- array
- dict
- bool
- 實數
- 整數
- date
- 資料

### <a name="a-namemt7046mt7046-add-entry--incorrectly-specified"></a><a name="MT7046"/>MT7046: 新增: 項目，*，正確指定。

*MSBuild 工作： PropertyListEditorTaskBase*

### <a name="a-namemt7047mt7047-add-entry--contains-invalid-array-index"></a><a name="MT7047"/>MT7047: 新增: 項目，*，包含無效的陣列索引。

*MSBuild 工作： PropertyListEditorTaskBase*

### <a name="a-namemt7048mt7048-add--entry-already-exists"></a><a name="MT7048"/>MT7048: 新增: * 項目已經存在。

*MSBuild 工作： PropertyListEditorTaskBase*

### <a name="a-namemt7049mt7049-add-cant-add-entry--to-parent"></a><a name="MT7049"/>MT7049: 新增: 無法新增項目，*，以父代。

*MSBuild 工作： PropertyListEditorTaskBase*

### <a name="a-namemt7050mt7050-delete-cant-delete-entry--from-parent"></a><a name="MT7050"/>MT7050: 刪除: 無法刪除項目，*，從父代。

*MSBuild 工作： PropertyListEditorTaskBase*

### <a name="a-namemt7051mt7051-delete-entry--contains-invalid-array-index"></a><a name="MT7051"/>MT7051: 刪除: 項目，*，包含無效的陣列索引。

*MSBuild 工作： PropertyListEditorTaskBase*

### <a name="a-namemt7052mt7052-delete-entry--does-not-exist"></a><a name="MT7052"/>MT7052: 刪除: 項目，*，不存在。

*MSBuild 工作： PropertyListEditorTaskBase*

### <a name="a-namemt7053mt7053-import-entry--incorrectly-specified"></a><a name="MT7053"/>MT7053： 匯入： 項目，*，正確指定。

*MSBuild 工作： PropertyListEditorTaskBase*

### <a name="a-namemt7054mt7054-import-entry--contains-invalid-array-index"></a><a name="MT7054"/>MT7054： 匯入： 項目，*，包含無效的陣列索引。

*MSBuild 工作： PropertyListEditorTaskBase*

### <a name="a-namemt7055mt7055-import-error-reading-file-"></a><a name="MT7055"/>MT7055： 匯入： 讀取檔案時發生錯誤: *。

*MSBuild 工作： PropertyListEditorTaskBase*

### <a name="a-namemt7056mt7056-import-cant-add-entry--to-parent"></a><a name="MT7056"/>MT7056： 匯入： 無法新增項目，*，以父代。

*MSBuild 工作： PropertyListEditorTaskBase*

### <a name="a-namemt7057mt7057-merge-cant-add-array-entries-to-dict"></a><a name="MT7057"/>MT7057: 合併: 無法將陣列項目加入至 dict.

*MSBuild 工作： PropertyListEditorTaskBase*

### <a name="a-namemt7058mt7058-merge-specified-entry-must-be-a-container"></a><a name="MT7058"/>MT7058: 合併: 指定項目必須是容器。

*MSBuild 工作： PropertyListEditorTaskBase*

### <a name="a-namemt7059mt7059-merge-entry--contains-invalid-array-index"></a><a name="MT7059"/>MT7059: 合併: 項目，*，包含無效的陣列索引。

*MSBuild 工作： PropertyListEditorTaskBase*

### <a name="a-namemt7060mt7060-merge-entry--does-not-exist"></a><a name="MT7060"/>MT7060: 合併: 項目，*，不存在。

*MSBuild 工作： PropertyListEditorTaskBase*

### <a name="a-namemt7061mt7061-merge-error-reading-file-"></a><a name="MT7061"/>MT7061: 合併: 讀取檔案時發生錯誤: *。

*MSBuild 工作： PropertyListEditorTaskBase*

### <a name="a-namemt7062mt7062-set-entry--incorrectly-specified"></a><a name="MT7062"/>MT7062: 設定: 項目，*，正確指定。

*MSBuild 工作： PropertyListEditorTaskBase*

### <a name="a-namemt7063mt7063-set-entry--contains-invalid-array-index"></a><a name="MT7063"/>MT7063: 設定: 項目，*，包含無效的陣列索引。

*MSBuild 工作： PropertyListEditorTaskBase*

### <a name="a-namemt7064mt7064-set-entry--does-not-exist"></a><a name="MT7064"/>MT7064: 設定: 項目，*，不存在。

*MSBuild 工作： PropertyListEditorTaskBase*

### <a name="a-namemt7065mt7065-unknown-propertylist-editor-action-"></a><a name="MT7065"/>MT7065： 未知的 PropertyList 編輯器動作: *。

*MSBuild 工作： PropertyListEditorTaskBase*

### <a name="a-namemt7066mt7066-error-loading--"></a><a name="MT7066"/>MT7066： 載入時發生錯誤 ' *': *。

*MSBuild 工作： PropertyListEditorTaskBase*

### <a name="a-namemt7067mt7067-error-saving--"></a><a name="MT7067"/>MT7067： 錯誤節約 ' *': *。

*MSBuild 工作： PropertyListEditorTaskBase*


## <a name="mt8xxx-runtime-error-messages"></a>MT8xxx： 執行階段錯誤訊息

<!--
 MT8xxx runtime
  MT800x misc
  -->

### <a name="a-namemt8001mt8001--version-mismatch-between-the-native-xamarinios-runtime-and-monotouchdll-please-reinstall-xamarinios"></a><a name="MT8001"/>原生 Xamarin.iOS 執行階段之間 monotouch.dll MT8001 版本不相符。 請重新安裝 Xamarin.iOS。

### <a name="a-namemt8002mt8002--could-not-find-the-method--in-the-type-"></a><a name="MT8002"/>MT8002 找不到方法 '\*'in '的 type\*'。

### <a name="a-namemt8003mt8003--failed-to-find-the-closed-generic-method--on-the-type-"></a><a name="MT8003"/>MT8003 無法找到封閉式泛型方法 '\*'on '的 type\*'。

### <a name="a-namemt8004mt8004-cannot-create-an-instance-of--for-the-native-object-0x-of-type--because-another-instance-already-exists-for-this-native-object-of-type-"></a><a name="MT8004"/>MT8004： 無法建立的執行個體 * 的原生物件 0 x * (型別 ' *')，因為另一個執行個體已經存在於此原生的物件 (類型的 *)。

### <a name="a-namemt8005mt8005-wrapper-type--is-missing-its-native-objectivec-class-"></a><a name="MT8005"/>MT8005： 包裝函式類型 '\*'遺漏其原生 ObjectiveC 類別'\*'。

### <a name="a-namemt8006mt8006-failed-to-find-the-selector--on-the-type-"></a><a name="MT8006"/>MT8006: 若要尋找的選取器無法 '\*'on '的 type\*'

### <a name="a-namemt8007mt8007-cannot-get-the-method-descriptor-for-the-selector--on-the-type--because-the-selector-does-not-correspond-to-a-method"></a><a name="MT8007"/>MT8007： 無法取得選取器的方法描述元 '\*'on '的 type\*'，因為選取器沒有對應至方法

### <a name="a-namemt8008mt8008-the-loaded-version-of-xamariniosdll-was-compiled-for--bits-while-the-process-is--bits-please-file-a-bug-at-httpbugzillaxamarincom"></a><a name="MT8008"/>MT8008: Xamarin.iOS.dll 載入的版本編譯的 * 位元，處理程序時 * 位元。 請在 http://bugzilla.xamarin.com 提報 bug。

這表示有不對勁出現在建置程序。 請將 bug 歸檔在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

### <a name="a-namemt8009mt8009-unable-to-locate-the-block-to-delegate-conversion-method-for-the-method-s-parameter--please-file-a-bug-at-httpbugzillaxamarincom"></a><a name="MT8009"/>MT8009： 找不到要委派之方法的轉換方法的區塊*。*'s 參數 # *。 請在 http://bugzilla.xamarin.com 提報 bug。

這表示應用程式開發介面不正確的繫結。 如果這是公開的 Xamarin 應用程式開發介面，請提報 bug 中我們 bugzilla ([http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS))，如果它是協力廠商的繫結，請連絡廠商。

### <a name="a-namemt8010mt8010-native-type-size-mismatch-between-xamariniosmacdll-and-the-executing-architecture-xamariniosmacdll-was-built-for--bit-while-the-current-process-is--bit"></a><a name="MT8010"/>MT8010: Xamarin 之間的原生類型的大小不相符。[iOS |Mac].dll 和執行的架構。 Xamarin。[iOS |已針對建置 Mac].dll *-位元，而目前的處理序是 * 位元。

這表示有不對勁出現在建置程序。 請將 bug 歸檔在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

### <a name="a-namemt8011mt8011-unable-to-locate-the-delegate-to-block-conversion-attribute-delegateproxy-for-the-return-value-for-the-method--please-file-a-bug-at-httpbugzillaxamarincom"></a><a name="MT8011"/>MT8011： 找不到方法的傳回值的區塊轉換屬性 ([DelegateProxy]) 的委派*。*。 請在 http://bugzilla.xamarin.com 提報 bug。

Xamarin.iOS 找不到所需的方法，在執行階段 （一個區塊轉換成委派）。

這通常表示 Xamarin.iOS; 中的 bug請將 bug 歸檔在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

### <a name="a-namemt8012mt8012-invalid-delegateproxyattribute-for-the-return-value-for-the-method--delegatetype-is-null-please-file-a-bug-at-httpbugzillaxamarincom"></a><a name="MT8012"/>MT8012： 方法的傳回值的無效 DelegateProxyAttribute *。*： 其實是 null。 請在 http://bugzilla.xamarin.com 提報 bug。

方法有問題的 DelegateProxy 屬性無效。

這通常表示 Xamarin.iOS; 中的 bug請將 bug 歸檔在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

### <a name="a-namemt8013mt8013-invalid-delegateproxyattribute-for-the-return-value-for-the-method--delegatetype-2-specifies-a-type-without-a-handler-field-please-file-a-bug-at-httpbugzillaxamarincom"></a><a name="MT8013"/>MT8013： 方法的傳回值的無效 DelegateProxyAttribute *。*： 其實 ({2}) 指定沒有 'Handler' 欄位的類型。 請在 http://bugzilla.xamarin.com 提報 bug。

方法有問題的 DelegateProxy 屬性無效。

這通常表示 Xamarin.iOS; 中的 bug請將 bug 歸檔在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

### <a name="a-namemt8014mt8014-invalid-delegateproxyattribute-for-the-return-value-for-the-method--the-delegatetypes-2-handler-field-is-null-please-file-a-bug-at-httpbugzillaxamarincom"></a><a name="MT8014"/>MT8014： 方法的傳回值的無效 DelegateProxyAttribute *。*: 其實的 ({2}) 'Handler' 欄位為 null。 請在 http://bugzilla.xamarin.com 提報 bug。

方法有問題的 DelegateProxy 屬性無效。

這通常表示 Xamarin.iOS; 中的 bug請將 bug 歸檔在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

### <a name="a-namemt8015mt8015-invalid-delegateproxyattribute-for-the-return-value-for-the-method--the-delegatetypes-2-handler-field-is-not-a-delegate-its-a--please-file-a-bug-at-httpbugzillaxamarincom"></a><a name="MT8015"/>MT8015： 方法的傳回值的無效 DelegateProxyAttribute *。*: 其實的 ({2}) 'Handler' 欄位不是委派，它是 *。 請在 http://bugzilla.xamarin.com 提報 bug。

方法有問題的 DelegateProxy 屬性無效。

這通常表示 Xamarin.iOS; 中的 bug請將 bug 歸檔在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

### <a name="a-namemt8016mt8016-unable-to-convert-delegate-to-block-for-the-return-value-for-the-method--because-the-input-isnt-a-delegate-its-a--please-file-a-bug-at-httpbugzillaxamarincom"></a><a name="MT8016"/>MT8016： 無法轉換封鎖方法的傳回值的委派*。*，輸入不是委派，因為它是 *。 請在 http://bugzilla.xamarin.com 提報 bug。

方法有問題的 DelegateProxy 屬性無效。

這通常表示 Xamarin.iOS; 中的 bug請將 bug 歸檔在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<!-- 8017 is used by mmp -->

### <a name="a-namemt8018mt8018-internal-consistency-error-please-file-a-bug-report-at-httpbugzillaxamarincom"></a><a name="MT8018"/>MT8018： 內部一致性錯誤。 請在 http://bugzilla.xamarin.com 檔案錯誤報告。

這表示 Xamarin.iOS 中的 bug。 請將 bug 歸檔在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

### <a name="a-namemt8019mt8019-could-not-find-the-assembly--in-the-loaded-assemblies"></a><a name="MT8019"/>MT8019： 找不到組件 * 中載入的組件。

這表示 Xamarin.iOS 中的 bug。 請將 bug 歸檔在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

### <a name="a-namemt8020mt8020-could-not-find-the-module-with-metadatatoken--in-the-assembly-"></a><a name="MT8020"/>MT8020： 找不到之模組的 MetadataToken * 組件中 *。

這表示 Xamarin.iOS 中的 bug。 請將 bug 歸檔在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

### <a name="a-namemt8021mt8021-unknown-implicit-token-type-"></a><a name="MT8021"/>MT8021： 未知的隱含語彙基元類型: *。

這表示 Xamarin.iOS 中的 bug。 請將 bug 歸檔在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

### <a name="a-namemt8022mt8022-expected-the-token-reference--to-be-a--but-its-a--please-file-a-bug-report-at-httpbugzillaxamarincom"></a><a name="MT8022"/>MT8022： 預期的語彙基元的參考 * 為 *，但它是 *。 請在 http://bugzilla.xamarin.com 檔案錯誤報告。

這表示 Xamarin.iOS 中的 bug。 請將 bug 歸檔在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

### <a name="a-namemt8023mt8023-an-instance-object-is-required-to-construct-a-closed-generic-method-for-the-open-generic-method--token-reference--please-file-a-bug-report-at-httpbugzillaxamarincom"></a><a name="MT8023"/>MT8023： 執行個體物件，才能建構的開放式泛型方法為封閉式泛型方法: * (語彙基元的參考: *)。 請在 http://bugzilla.xamarin.com 檔案錯誤報告。

這表示 Xamarin.iOS 中的 bug。 請將 bug 歸檔在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。
