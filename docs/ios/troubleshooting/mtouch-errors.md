---
title: Xamarin.iOS 錯誤
description: 本文件說明各種 mtouch，用來連結相同的 Xamarin.iOS 應用程式的工具所產生的錯誤。 錯誤的程式碼列出並提供完整的說明。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9F76162B-D622-45DA-996B-2FBF8017E208
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/06/2018
ms.openlocfilehash: e6e3a989db922dc2941cca4c888c862ffe159241
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57672855"
---
# <a name="xamarinios-errors"></a>Xamarin.iOS 錯誤

## <a name="mt0xxx-mtouch-error-messages"></a>MT0xxx: mtouch 錯誤訊息

例如， 參數、 遺漏工具的環境。

<!--
 MT0xxx mtouch itself, e.g. parameters, environment (e.g. missing tools)
 https://github.com/xamarin/xamarin-macios/blob/master/tools/mtouch/error.cs
    -->

<a name="MT0000" />

### <a name="mt0000-unexpected-error---please-fill-a-bug-report-at-httpsgithubcomxamarinxamarin-maciosissuesnew"></a>MT0000:未預期的錯誤-請填寫在錯誤報告 https://github.com/xamarin/xamarin-macios/issues/new

發生未預期的錯誤狀況。 請[提出 bug 報告](https://github.com/xamarin/xamarin-macios/issues/new)盡可能最多資訊，包括：

* 完整建置記錄檔，並以最詳細 (例如`-v -v -v -v`中**其他 mtouch 引數**);
* 最少重現錯誤，測試案例和
* 所有的版本資訊

若要取得正確的版本資訊最簡單的方式是使用**Visual Studio for Mac**  功能表**關於 Visual Studio for Mac**項目，**顯示詳細資料**按鈕和複製/貼上版本資訊 (您可以使用**複製資訊**按鈕)。

<a name="MT0001" />

### <a name="mt0001--devname-was-provided-without-any-device-specific-action"></a>MT0001: '-devname' 並無任何裝置特定動作所提供

這是如果-devname 傳遞給 mtouch 任何裝置特定動作時，就會發出警告 (層 logdev /-installdev /-killdev /-launchdev /-listapps) 要求。

<a name="MT0002" />

### <a name="mt0002-could-not-parse-the-environment-variable-"></a>MT0002:無法剖析環境變數 *。

如果您嘗試設定無效的環境索引鍵，就會發生此錯誤 = 值組的變數。 正確的格式為： `mtouch --setenv=VARIABLE=VALUE`

<a name="MT0003" />

### <a name="mt0003-application-name-exe-conflicts-with-an-sdk-or-product-assembly-dll-name"></a>MT0003:應用程式名稱 '*.exe' je v konfliktu SDK 或產品組件 (.dll) 名稱。

可執行組件的名稱和應用程式的名稱不符合應用程式中的任何 dll 的名稱。 請修改可執行檔的名稱。

<a name="MT0004" />

### <a name="mt0004-new-refcounting-logic-requires-sgen-to-be-enabled-too"></a>MT0004:新的 refcounting 邏輯需要太啟用 SGen。

如果您啟用 refcounting 擴充功能，您也必須啟用 SGen 記憶體回收行程，在專案的 iOS 組建 選項 （進階索引標籤）。

開始使用 Xamarin.iOS 7.2.1 此需求已提高，可以啟用新的 refcounting 邏輯 Boehm 和 SGen 記憶體回收行程。

<a name="MT0005" />

### <a name="mt0005-the-output-directory--does-not-exist"></a>MT0005:輸出目錄 * 不存在。

請建立目錄。

不會再產生這個錯誤，mtouch 會自動建立目錄，如果不存在。

<a name="MT0006" />

### <a name="mt0006-there-is-no-devel-platform-at--use---platformplat-to-specify-the-sdk"></a>MT0006:沒有任何提供平台 *，使用-平台 = 指定 SDK 的平台。

Xamarin.iOS 找不到 SDK 目錄中的錯誤訊息中所述的位置。 請確認路徑正確。

<a name="MT0007" />

### <a name="mt0007-the-root-assembly--does-not-exist"></a>MT0007:根組件 * 不存在。

Xamarin.iOS 找不到錯誤訊息中所述的位置中的組件。 請確認路徑正確。

<a name="MT0008" />

### <a name="mt0008-you-should-provide-one-root-assembly-only-found--assemblies-"></a>MT0008:您應提供根組件，找到 # 組件: *。

一個以上的根組件傳遞給 mtouch，雖然可能只能有一個根組件。

<a name="MT0009" />

### <a name="mt0009-error-while-loading-assemblies-"></a>MT0009:載入組件時發生錯誤: *。

載入組件的根組件參考時發生錯誤。 建置輸出中，可能會提供詳細資訊。

<a name="MT0010" />

### <a name="mt0010-could-not-parse-the-command-line-arguments-"></a>MT0010:無法剖析命令列引數: *。

剖析命令列引數時發生錯誤。 請確認它們都正確。

<a name="MT0011" />

### <a name="mt0011--was-built-against-a-more-recent-runtime--than-monotouch-supports"></a>MT0011: * 高於 MonoTouch 支援較新的執行階段 （*） 建置。

因為專案不是使用 Xamarin.iOS BCL 類別程式庫的參考，通常會報告這個警告。

使用.NET 4.0 SDK 的應用程式可能無法只支援.NET 2.0 中，在系統的相同方式建置使用.NET 4.0 的程式庫，可能無法在 Xamarin.iOS 上運作，它可能會使用 API 不會出現在 Xamarin.iOS 上。

一般的解決方法是建置為 Xamarin.iOS 類別庫的程式庫。 這可藉由建立新的 Xamarin.iOS 類別庫專案，並將它的所有原始程式檔。 如果您沒有程式庫的原始程式碼，您應該連絡廠商，並要求他們提供其程式庫的 Xamarin.iOS 相容版本。

<a name="MT0012" />

### <a name="mt0012-incomplete-data-is-provided-to-complete-"></a>MT0012:若要完成不完整的資料提供 *。

此錯誤不會再報告在目前版本中的 Xamarin.iOS。

<a name="MT0013" />

### <a name="mt0013-profiling-support-requires-sgen-to-be-enabled-too"></a>MT0013:程式碼剖析支援需要太啟用 sgen。

SGen (-sgen)，程式碼剖析，則必須啟用 (-程式碼剖析) 已啟用。

<a name="MT0014" />

### <a name="mt0014-the-ios--sdk-does-not-support-building-applications-targeting-"></a>MT0014:IOS * SDK 不支援建置應用程式目標設為 *。

這會發生在下列情況：

*  ARMv6 已啟用，並已安裝 Xcode 4.5 或更新版本。
*  ARMv7s 已啟用，並已安裝 Xcode 4.4 或更早版本。

請確認已安裝的 Xcode 版本支援選取的架構。

<a name="MT0015" />

### <a name="mt0015-invalid-abi--supported-abis-are-i386-x8664--armv7-armv7llvm-armv7llvmthumb2-armv7s-armv7sllvm-armv7sllvmthumb2-arm64-and-arm64llvm"></a>MT0015:無效的 ABI: *。 支援的 Abi 會： i386，x86_64，armv7，armv7 + llvm、 armv7 + llvm + thumb2、 armv7s、 armv7s + llvm、 armv7s + llvm + thumb2、 arm64 和 arm64 + llvm。

無效的 ABI 傳遞給 mtouch。 請指定有效的 ABI。

<a name="MT0016" />

### <a name="mt0016-the-option--has-been-deprecated"></a>MT0016:此選項 * 已被取代。

提及的 mtouch 選項已被取代，且會被忽略。

<a name="MT0017" />

### <a name="mt0017-you-should-provide-a-root-assembly"></a>MT0017:您應提供根組件。

需要指定根組件 （通常是主要可執行檔） 建置應用程式時。

<a name="MT0018" />

### <a name="mt0018-unknown-command-line-argument-"></a>MT0018:未知的命令列引數: *。

Mtouch 無法辨識的錯誤訊息中所述的命令列引數。

<a name="MT0019" />

### <a name="mt0019-only-one---loginstallkilllaunchdev-or---launchdebugsim-option-can-be-used"></a>MT0019:只有一個-[記錄檔 | 安裝 | 刪除 | 啟動] 開發或--[啟動 | 偵錯] sim 選項可用。

有幾個選項不能同時使用的 mtouch:

-  --logdev
-  --installdev
-  --killdev
-  --launchdev
-  --launchdebug
-  --launchsim

<a name="MT0020" />

### <a name="mt0020-the-valid-options-for--are-"></a>MT0020:有效的選項為 '\*'是'\*'。

<a name="MT0021" />

### <a name="mt0021-cannot-compile-using-gccg---use-gcc-when-using-the-static-registrar-this-is-the-default-when-compiling-for-device-either-remove-the---use-gcc-flag-or-use-the-dynamic-registrar---registrardynamic"></a>MT0021:無法編譯使用 gcc / g + + (-使用 gcc) 時使用靜態的註冊機構 （裝置進行編譯時，這是預設值）。 請移除--使用 gcc 旗標，或使用動態的註冊機構 (-註冊機構： 動態)。

<a name="MT0022" />

### <a name="mt0022-the-options---unsupported--enable-generics-in-registrar-and---registrar-are-not-compatible"></a>MT0022:選項 '-不支援-啟用-泛型-中-登錄器' 和 '-登錄器 ' 不相容。

移除兩個選項`--unsupported--enable-generics-in-registrar`和`--registrar`。 從 Xamarin.iOS 7.2.1 預設註冊機構支援泛型。

不會再看到這個錯誤 (命令列引數`--unsupported--enable-generics-in-registrar`已經移除了 mtouch)。

<a name="MT0023" />

### <a name="mt0023-application-name-exe-conflicts-with-another-user-assembly"></a>MT0023:應用程式名稱 '*.exe' 與另一個使用者組件衝突。

可執行組件的名稱和應用程式的名稱不符合應用程式中的任何 dll 的名稱。 請修改可執行檔的名稱。

<a name="MT0024" />

### <a name="mt0024-could-not-find-required-file-"></a>MT0024:找不到必要的檔案 ' *'。

<a name="MT0025" />

### <a name="mt0025-no-sdk-version-was-provided-please-add---sdkxy-to-specify-which-ios-sdk-should-be-used-to-build-your-application"></a>MT0025:未不提供任何 SDK 版本。 請新增`--sdk=X.Y`來指定哪些 iOS SDK 應該用來建置您的應用程式。

<a name="MT0026" />

### <a name="mt0026-could-not-parse-the-command-line-argument--"></a>MT0026:無法剖析命令列引數 ' *': *

<a name="MT0027" />

### <a name="mt0027-the-options--and--are-not-compatible"></a>MT0027:選項\*'和'\*' 不相容。

<a name="MT0028" />

### <a name="mt0028-cannot-enable-pie--pie-when-targeting-ios-41-or-earlier-please-disable-pie--piefalse-or-set-the-deployment-target-to-at-least-ios-42"></a>MT0028:無法啟用圓形圖 (-圓形圖) 為目標 iOS 4.1 或更早版本時。 請停用圓形圖 (-圓形圖： false) 或至少將部署目標設定為 iOS 4.2

<a name="MT0029" />

### <a name="mt0029-repl---enable-repl-is-only-supported-in-the-simulator---sim"></a>MT0029:REPL (-啟用 repl) 才支援在模擬器中 (-sim)。

如果您正在建置模擬器僅支援複寫。 這表示，如果您傳遞`--enable-repl`mtouch，您必須也傳遞`--sim`。

<a name="MT0030" />

### <a name="mt0030-the-executable-name--and-the-app-name--are-different-this-may-prevent-crash-logs-from-getting-symbolicated-properly"></a>MT0030:可執行檔的名稱 (\*) 和應用程式名稱 (\*) 不同，這可能會導致當機記錄檔無法取得符號式正確。

當 Xcode symbolicates （轉換函式名稱和檔案/行數字的記憶體位址） 如果可執行檔和應用程式有不同的名稱 （不含副檔名），此程序可能會失敗。

若要修正這個變更 '應用程式名稱' 在專案的建置/iOS 應用程式選項或在專案的建置/輸出選項 中的變更 ' Assembly Name'。

<a name="MT0031" />

### <a name="mt0031-the-command-line-arguments---enable-background-fetch-and---launch-for-background-fetch-require---launchsim-too"></a>MT0031:命令列引數 '-啟用背景擷取 '和 '-啟動的背景擷取' 需要 '-launchsim' 太。

<a name="MT0032" />

### <a name="mt0032-the-option---debugtrack-is-ignored-unless---debug-is-also-specified"></a>MT0032:選項 '-debugtrack' 會被忽略，除非 '-偵錯 ' 同時指定。

<a name="MT0033" />

### <a name="mt0033-a-xamarinios-project-must-reference-either-monotouchdll-or-xamariniosdll"></a>MT0033:在 Xamarin.iOS 專案必須參考 monotouch.dll 或 Xamarin.iOS.dll

<a name="MT0034" />

### <a name="mt0034-cannot-include-both-monotouchdll-and-xamariniosdll-in-the-same-xamarinios-project----is-referenced-explicitly-while--is-referenced-by-"></a>MT0034:不能包含 'monotouch.dll' 和 'Xamarin.iOS.dll' 在相同的 Xamarin.iOS 專案中為 '\*' 是明確參考，而 '\*' 參考的 ' *'。

<!-- MT0035 unused -->

<a name="MT0036" />

### <a name="mt0036-cannot-launch-a--simulator-for-a--app-please-enable-the-correct-architectures-in-your-projects-ios-build-options-advanced-page"></a>MT0036:無法啟動 * 模擬器 * 應用程式。 請啟用正確的 architecture(s) 中專案的 iOS 組建 選項 （進階頁面）。

<a name="MT0037" />

### <a name="mt0037-monotouchdll-is-not-64-bit-compatible-either-reference-xamariniosdll-or-do-not-build-for-a-64-bit-architecture-arm64-andor-x8664"></a>MT0037: monotouch.dll 與 64 位元不相容。 請參考 Xamarin.iOS.dll，或針對 64 位元的架構 （ARM64 和/或 x86_64） 不會建置。

<a name="MT0038" />

### <a name="mt0038-the-old-registrars---registraroldstaticolddynamic-are-not-supported-when-referencing-xamariniosdll"></a>MT0038:舊的註冊機構 (-註冊機構： oldstatic | olddynamic) 不支援參考 Xamarin.iOS.dll 時。

<a name="MT0039" />

### <a name="mt0039-applications-targeting-armv6-cannot-reference-xamariniosdll"></a>MT0039:目標 ARMv6 的應用程式無法參考 Xamarin.iOS.dll。

<a name="MT0040" />

### <a name="mt0040-could-not-find-the-assembly--referenced-by-"></a>MT0040:找不到組件 '\*'，參考的'\*'。

<a name="MT0041" />

### <a name="mt0041-cannot-reference-both-monotouchdll-and-xamariniosdll"></a>MT0041:無法參考 'monotouch.dll' 和 'Xamarin.iOS.dll'。

<a name="MT0042" />

### <a name="mt0042-no-reference-to-either-monotouchdll-or-xamariniosdll-was-found-a-reference-to-monotouchdll-will-be-added"></a>MT0042:找不到 monotouch.dll 或 Xamarin.iOS.dll 的參考。 將加入 monotouch.dll 的參考。

<a name="MT0043" />

### <a name="mt0043-the-boehm-garbage-collector-is-currently-not-supported-when-referencing-xamariniosdll-the-sgen-garbage-collector-has-been-selected-instead"></a>MT0043:Boehm 記憶體回收行程目前不支援參考 'Xamarin.iOS.dll' 時。 SGen 記憶體回收行程已改為選取。

整合專案支援只 SGen 記憶體回收行程。 請確定有指定 Boehm 記憶體回收行程為任何其他 mtouch 旗標。

<a name="MT0044" />

### <a name="mt0044---listsim-is-only-supported-with-xcode-60-or-later"></a>MT0044:-listsim 僅適用於 Xcode 6.0 或更新版本。

安裝較新的 Xcode 版本。

<a name="MT0045" />

### <a name="mt0045---extension-is-only-supported-when-using-the-ios-80-or-later-sdk"></a>MT0045:-擴充功能僅適用於當使用 iOS 8.0 （含） 以後的 SDK。

<!-- MT0046 is not reported anymore -->

<a name="MT0047" />

### <a name="mt0047-the-minimum-deployment-target-for-unified-applications-is-511-the-current-deployment-target-is--please-select-a-newer-deployment-target-in-your-projects-ios-application-options"></a>MT0047:整合應用程式的最小的部署目標為 5.1.1，目前的部署目標為 ' *'。 請在您專案的 iOS 應用程式的選項，選取較新的部署目標。

<!-- MT0048 is not reported anymore -->

<a name="MT0049" />

### <a name="mt0049-framework-is-supported-only-if-deployment-target-is-80-or-later--features-might-not-work-correctly"></a>MT0049: *.framework 才支援部署目標是 8.0 或更新版本。 * 功能可能無法正確運作。

在部署目標是指的 iOS 版本不支援指定的架構。 更新部署目標為較新的 iOS 版本，或是從應用程式中移除指定之 framework 的使用方式。

<!-- MT0050 is not reported anymore -->

<a name="MT0051" />

### <a name="mt0051-xamarinios--requires-xcode-50-or-later-the-current-xcode-version-found-in--is-"></a>MT0051:Xamarin.iOS * 需要 Xcode 5.0 或更新版本。 目前的 Xcode 版本 (位於 *) 是 *。

安裝較新的 Xcode。

<a name="MT0052" />

### <a name="mt0052-no-command-specified"></a>MT0052:指定任何命令。

對 mtouch 不指定任何動作。

<!-- 0053 is used by mmp -->

<a name="MT0054" />

### <a name="mt0054-unable-to-canonicalize-the-path--"></a>MT0054:無法規範化路徑 ' *': *

這是內部錯誤。 如果您看到此錯誤，請將 bug 歸檔[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT0055" />

### <a name="mt0055-the-xcode-path--does-not-exist"></a>MT0055:Xcode 路徑 ' *' 不存在。

使用 Xcode 路徑傳遞`--sdkroot`不存在。 請指定有效的路徑。

<a name="MT0056" />

### <a name="mt0056-cannot-find-xcode-in-the-default-location-applicationsxcodeapp-please-install-xcode-or-pass-a-custom-path-using---sdkroot-path"></a>MT0056:在預設位置找不到 Xcode (/ /applications/xcode.app)。 請安裝 Xcode，或傳遞自訂路徑使用--sdkroot <path>。

<a name="MT0057" />

### <a name="mt0057-cannot-determine-the-path-to-xcodeapp-from-the-sdk-root--please-specify-the-full-path-to-the-xcodeapp-bundle"></a>MT0057:無法判定要 Xcode.app 的路徑從 sdk 根目錄 ' *'。 請指定 Xcode.app 套件組合的完整路徑。

路徑可讓您傳遞使用`--sdkroot`未指定有效的 Xcode 應用程式。 請指定要將 Xcode 應用程式的路徑。

<a name="MT0058" />

### <a name="mt0058-the-xcodeapp--is-invalid-the-file--does-not-exist"></a>MT0058:Xcode.app '\*' 無效 (檔案 '\*' 不存在)。

路徑可讓您傳遞使用`--sdkroot`未指定有效的 Xcode 應用程式。 請指定要將 Xcode 應用程式的路徑。

<a name="MT0059" />

### <a name="mt0059-could-not-find-the-currently-selected-xcode-on-the-system-"></a>MT0059:在系統上找不到目前選取的 Xcode: *

<a name="MT0060" />

### <a name="mt0060-could-not-find-the-currently-selected-xcode-on-the-system-xcode-select---print-path-returned--but-that-directory-does-not-exist"></a>MT0060:在系統上找不到目前選取的 Xcode。 'xcode-select--列印路徑' 傳回 ' *'，但該目錄不存在。

<a name="MT0061" />

### <a name="mt0061-no-xcodeapp-specified-using---sdkroot-using-the-system-xcode-as-reported-by-xcode-select---print-path-"></a>MT0061:沒有 Xcode.app specified （使用--sdkroot），使用系統 Xcode 所報告 ' xcode-select--列印路徑 ': *

此為參考用的警告，說明 Xcode 會使用，因為未指定。

<a name="MT0062" />

### <a name="mt0062-no-xcodeapp-specified-using---sdkroot-or-xcode-select---print-path-using-the-default-xcode-instead-"></a>MT0062:沒有 Xcode.app specified （使用--sdkroot 或 ' xcode-select--列印路徑 '），改為使用預設 Xcode: *

此為參考用的警告，說明 Xcode 會使用，因為未指定。

<a name="MT0063" />

### <a name="mt0063-cannot-find-the-executable-in-the-extension--no-cfbundleexecutable-entry-in-its-infoplist"></a>MT0063:找不到延伸模組中可執行檔 * （在其 Info.plist 中的沒有 CFBundleExecutable 項目）

每個 Info.plist 必須有可執行檔 （使用 CFBundleExecutable 項目），不過應該會自動產生的項目，在建置期間。

這通常表示 Xamarin.iOS; 中的 bug請提出錯誤報告在[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)與測試案例。

<a name="MT0064" />

### <a name="mt0064-xamarinios-only-supports-embedded-frameworks-with-unified-projects"></a>MT0064:Xamarin.iOS 只支援內嵌的架構，與整合專案。

Xamarin.iOS 時，才支援內嵌的架構使用統一的 API;請更新您的專案使用統一的 API。

<a name="MT0065" />

### <a name="mt0065-xamarinios-only-supports-embedded-frameworks-when-deployment-target-is-at-least-80-current-deployment-target--embedded-frameworks-"></a>MT0065:Xamarin.iOS 時，才支援內嵌的架構部署目標是至少 8.0 (目前的部署目標: * 內嵌架構: *)

Xamarin.iOS 會在部署目標是至少 8.0 （因為舊版 iOS 不支援內嵌的架構） 時，才支援內嵌的架構。

請更新至 8.0 或更新版本的專案的 Info.plist 中的部署目標。

<a name="MT0066" />

### <a name="mt0066-invalid-build-registrar-assembly-"></a>MT0066:無效的組建註冊機構的組件: *

這通常表示 Xamarin.iOS; 中的 bug請提出錯誤報告在[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)與測試案例。

<a name="MT0067" />

### <a name="mt0067-invalid-registrar-"></a>MT0067:無效的註冊機構: *

這通常表示 Xamarin.iOS; 中的 bug請提出錯誤報告在[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)與測試案例。

<a name="MT0068" />

### <a name="mt0068-invalid-value-for-target-framework-"></a>MT0068:目標 framework 的值無效: *。

使用已傳遞無效的目標 framework-目標 framework 引數。 請指定有效的目標 framework。

<a name="MT0069" />

<!--### MT0069: currently unused -->

<a name="MT0070" />

### <a name="mt0070-invalid-target-framework--valid-target-frameworks-are-"></a>MT0070:無效的目標 framework: *。 有效的目標架構: *。

使用已傳遞無效的目標 framework-目標 framework 引數。 請指定有效的目標 framework。

<a name="MT0071" />

### <a name="mt0071-unknown-platform--this-usually-indicates-a-bug-in-xamarinios-please-file-a-bug-report-at-httpbugzillaxamarincom-with-a-test-case"></a>MT0071:未知的平台: *。 這通常表示 Xamarin.iOS; 中的 bug請提出錯誤報告在 http://bugzilla.xamarin.com與測試案例。

這通常表示 Xamarin.iOS; 中的 bug請提出錯誤報告在[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)與測試案例。

<a name="MT0072" />

### <a name="mt0072-extensions-are-not-supported-for-the-platform-"></a>MT0072:平台不支援的副檔名 ' *'。

這通常表示 Xamarin.iOS; 中的 bug請提出錯誤報告在[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)與測試案例。

<a name="MT0073" />

### <a name="mt0073-xamarinios--does-not-support-a-deployment-target-of--the-minimum-is--please-select-a-newer-deployment-target-in-your-projects-infoplist"></a>MT0073:Xamarin.iOS * 不支援的部署目標 * (最小值是 *)。 請在您專案的 Info.plist 中，選取較新的部署目標。

最小的部署目標是所指定的錯誤訊息;請選取專案的 Info.plist 中的較新的部署目標。

如果更新部署的目標不可行，請使用較舊版本的 Xamarin.iOS。

<a name="MT0074" />

### <a name="mt0074-xamarinios--does-not-support-a-minimum-deployment-target-of--the-maximum-is--please-select-an-older-deployment-target-in-your-projects-infoplist-or-upgrade-to-a-newer-version-of-xamarinios"></a>MT0074:Xamarin.iOS * 不支援的最小的部署目標 * (最大值是 *)。 請選取您專案的 Info.plist 中的較舊的部署目標，或升級至較新的 Xamarin.iOS 版本。

Xamarin.iOS 不支援將最小的部署目標設定為版本高於此特定版本的 Xamarin.iOS 建置的版本。

請在專案的 Info.plist 中，選取舊的最低部署目標或升級至較新的 Xamarin.iOS 版本。

<a name="MT0075" />

### <a name="mt0075-invalid-architecture--for--projects-valid-architectures-are-"></a>MT0075:無效的架構 ' *' 的 * 專案。 有效的架構: *

指定無效的架構。 請確認該架構有效。

<a name="MT0076" />

### <a name="mt0075-no-architecture-specified-using-the---abi-argument-an-architecture-is-required-for--projects"></a>MT0075:（使用-abi 引數） 指定任何架構。 架構是為了 * 專案。

這通常表示 Xamarin.iOS; 中的 bug請提出錯誤報告在[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)與測試案例。

<a name="MT0077" />

### <a name="mt0076-watchos-projects-must-be-extensions"></a>MT0076:WatchOS 專案必須是延伸模組。

這通常表示 Xamarin.iOS; 中的 bug請提出錯誤報告在[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)與測試案例。

<a name="MT0078" />

### <a name="mt0077-incremental-builds-are-enabled-with-a-deployment-target--80-currently--this-is-not-supported-the-resulting-application-will-not-launch-on-ios-9-so-the-deployment-target-will-be-set-to-80"></a>MT0077:啟用累加建置與部署目標 < 8.0 (目前 *)。 這不支援 （產生的應用程式將不會啟動在 iOS 9），因此部署目標將設為 8.0。

這是警告，通知，部署目標已設定為此組建的 8.0，累加建置才能工作正確。

當部署目標是至少 8.0 （因為產生的應用程式將不會啟動在 iOS 9 否則） 時，才支援累加建置。

<a name="MT0079" />

### <a name="mt0078-the-recommended-xcode-version-for-xamarinios--is-xcode--or-later-the-current-xcode-version-found-in--is-"></a>MT0078:建議的 Xcode 版本，適用於 Xamarin.iOS * 是 Xcode * 或更新版本。 目前的 Xcode 版本 (位於 *) 是 *。

這是警告，通知目前的 Xcode 版本不此版本的 Xamarin.iOS 的建議的版本的 Xcode。

請先升級 Xcode，以確保最佳的行為。

<a name="MT0080" />

### <a name="mt0080-disabling-newrefcount---new-refcountfalse-is-deprecated"></a>MT0080:停用 NewRefCount、--新增-refcount:false，已被取代。

這是警告，通知，若要停用新要求 refcount (-新增-refcount:false) 已被忽略。

新的 refcount 功能現在是必要的所有專案，而且不因此可以再停用。

<a name="MT0081" />

### <a name="mt0081-the-command-line-argument---download-crash-report-also-requires---download-crash-report-to"></a>MT0081:命令列引數--下載損毀報表也需要-下載-損毀-報表。

<a name="MT0082" />

### <a name="mt0082-repl---enable-repl-is-only-supported-when-linking-is-not-used---nolink"></a>MT0082:REPL (-啟用 repl) 不使用連結時，才支援 (-nolink)。

<a name="MT0083" />

### <a name="mt0083-asm-only-bitcode-is-not-supported-on-watchos-use-either---bitcodemarker-or---bitcodefull"></a>MT0083:WatchOS 不支援僅限 Asm 的 bitcode。 使用任一-bitcode： 標記或-bitcode： 完整。

<a name="MT0084" />

### <a name="mt0084-bitcode-is-not-supported-in-the-simulator-do-not-pass---bitcode-when-building-for-the-simulator"></a>MT0084:在模擬器中不支援 Bitcode。 當模擬器的建置，請不要傳遞-bitcode。

<a name="MT0085" />

### <a name="mt0085-no-reference-to--was-found-it-will-be-added-automatically"></a>MT0085:不到參考 ' *' 找不到。 它會自動加入。

<a name="MT0086" />

### <a name="mt0086-a-target-framework---target-framework-must-be-specified-when-building-for-tvos-or-watchos"></a>MT0086:目標 framework (-目標架構) 建置 TVOS 或 WatchOS 時必須指定。

這通常表示 Xamarin.iOS; 中的 bug請提出錯誤報告在[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)與測試案例。

<a name="MT0087" />

### <a name="mt0087-incremental-builds---fastdev-is-not-supported-with-the-boehm-gc-incremental-builds-will-be-disabled"></a>MT0087:累加建置 (-fastdev) 不支援 Boehm GC。 將停用累加建置。

<a name="MT0088" />

### <a name="mt0088-the-gc-must-be-in-cooperative-mode-for-watchos-apps-please-remove-the---coopfalse-argument-to-mtouch"></a>MT0088:GC 必須是合作式 watchOS 應用程式的模式。 請移除 mtouch--coop: false 引數。

<a name="MT0089" />

### <a name="mt0089-the-option--cannot-take-the-value--when-cooperative-mode-is-enabled-for-the-gc"></a>MT0089:選項 '\*'無法接受的值'\*' GC 啟用合作式的模式時。

<a name="MT0091" />

### <a name="mt0091-this-version-of-xamarinios-requires-the--sdk-shipped-with-xcode--either-upgrade-xcode-to-get-the-required-header-files-or-set-the-managed-linker-behaviour-to-link-framework-sdks-only-to-try-to-avoid-the-new-apis"></a>MT0091:此版本的 Xamarin.iOS 需要 * SDK (隨附於 Xcode *)。 請升級以取得必要的標頭檔，或設為 僅連結 Framework Sdk （若要盡量避免新的 Api） 的受管理的連結器行為的 Xcode。

Xamarin.iOS 需要的標頭檔，從指定的錯誤訊息，來建置您的應用程式中的 SDK 版本。 若要修正此錯誤的建議的方式是升級 Xcode 來取得必要的 SDK，這將包含所有必要的標頭檔案。 如果您有多個版本的 Xcode 安裝，或想要使用 Xcode，在非預設位置，請確定您的 IDE 喜好設定中設定正確的 Xcode 位置。

潛在的優點，替代方案是讓受管理的連結器。 這會移除未使用的 API 包括在大部分情況下，新的 API，其中的標頭檔案已遺失 （或不完整）。 不過如果您的專案使用較新的 SDK 與您的 Xcode 中所導入的 API，將不會運作這所提供。

最後一個 straw 解決方案是使用較舊版本的 Xamarin.iOS，支援您的專案的 SDK 需要。

<!-- MT0092 used by mlaunch -->

<a name="MT0093" />

### <a name="mt0093-could-not-find-mlaunch"></a>MT0093:找不到 'mlaunch'。

<!-- MT0094 is not reported anymore -->

<a name="MT0095" />

### <a name="mt0095-aot-files-could-not-be-copied-to-the-destination-directory-dest-error"></a>MT0095:Aot 檔案無法複製的目的地目錄 {dest}: {error}

<a name="MT0096" />

### <a name="mt0096-no-reference-to-xamariniosdll-was-found"></a>MT0096:找不到 Xamarin.iOS.dll 的參考。

<!-- MT0097: used by mmp -->
<!-- MT0098: used by mmp -->

<a name="MT0099" />

### <a name="mt0099-internal-error--please-file-a-bug-report-with-a-test-case-httpbugzillaxamarincom"></a>MT0099:內部錯誤 *。 請提出問題報告與測試案例 (http://bugzilla.xamarin.com)。

在 Xamarin.iOS 中的內部一致性檢查失敗時，會報告此錯誤訊息。

這表示在 Xamarin.iOS; 中的 bug請提出錯誤報告在[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)與測試案例。

<a name="MT0100" />

### <a name="mt0100-invalid-assembly-build-target--please-file-a-bug-report-with-a-test-case-httpbugzillaxamarincom"></a>MT0100:無效的組件建置目標: ' *'。 請提出問題報告與測試案例 (http://bugzilla.xamarin.com)。

在 Xamarin.iOS 中的內部一致性檢查失敗時，會報告此錯誤訊息。

這一律是 Xamarin.iOS; 中的 bug請提出錯誤報告在[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)與測試案例。

<a name="MT0101" />

### <a name="mt0101-the-assembly--is-specified-multiple-times-in---assembly-build-target-arguments"></a>MT0101:組件 ' *'-組件建置目標引數中指定多次。

錯誤訊息中所述的組件指定了多次-組件建置目標引數中。 請確定每個組件只提及一次。

<a name="MT0102" />

### <a name="mt0102-the-assemblies--and--have-the-same-target-name--but-different-targets--and-"></a>MT0102:組件\*'和'\*'具有相同的目標名稱 ('\*')，但不同的目標 ('\*' 和 ' *')。

錯誤訊息中所述的組件具有衝突的建置目標。

例如: 

    --assembly-build-target:Assembly1.dll=framework=MyBinary --assembly-build-target:Assembly2.dll=dynamiclibrary=MyBinary

此範例中嘗試使用相同的廠牌建立動態程式庫和架構 (`MyBinary`)。

<a name="MT0103" />

### <a name="mt0103-the-static-object--contains-more-than-one-assembly--but-each-static-object-must-correspond-with-exactly-one-assembly"></a>MT0103:靜態物件 '\*' 包含多個組件 ('\*')，但每個靜態物件必須對應至一個組件。

錯誤訊息中所述的組件全部會編譯成單一的靜態物件。 這不允許，每個組件必須先編譯成不同的靜態物件。

例如: 

    --assembly-build-target:Assembly1.dll=staticobject=MyBinary --assembly-build-target:Assembly2.dll=staticobject=MyBinary

此範例會嘗試建立靜態物件 (`MyBinary`) 包含兩個組件 (`Assembly1.dll`和`Assembly2.dll`)，這不允許。

<a name="MT0105" />

### <a name="mt0105-no-assembly-build-target-was-specified-for-"></a>MT0105:沒有組件建置目標已指定 ' *'。

指定的組件時建置目標使用`--assembly-build-target`，應用程式中的每個組件必須具有指派的建置目標。

錯誤訊息中所述的組件沒有建立指派的目標組件時，會報告此錯誤。

請參閱的文件`--assembly-build-target`如需詳細資訊。

<a name="MT0106" />

### <a name="mt0106-the-assembly-build-target-name--is-invalid-the-character--is-not-allowed"></a>MT0106:組件建置目標名稱 '\*' 無效： 字元 '\*' 不允許。

組件的 build 目標名稱必須是有效的檔名。

比方說這些值將會觸發此錯誤：

    --assembly-build-target:Assembly1.dll=staticobject=my/path.o

因為`my/path.o`不是有效的檔名，因為目錄分隔符號字元。

<a name="MT0107" />

### <a name="mt0107-the-assemblies--have-different-custom-llvm-optimizations--which-is-not-allowed-when-they-are-all-compiled-to-a-single-binary"></a>MT0107:組件\*' 有不同的自訂 LLVM 最佳化 (\*)，這不允許在所有編譯時與單一的二進位檔。

<a name="MT0108" />

### <a name="mt0108-the-assembly-build-target--did-not-match-any-assemblies"></a>MT0108:組件建置目標 ' *' 不符合任何組件。

<a name="MT0109" />

### <a name="mt0109-the-assembly-0-was-loaded-from-a-different-path-than-the-provided-path-provided-path-1-actual-path-2"></a>MT0109:組件 '{0}' 已從提供的路徑不同的路徑載入 (提供路徑： {1}，實際路徑： {2})。

這是一個警告，指出應用程式所參考的組件已載入不同的位置從比要求。

這可能表示應用程式會參考多個組件名稱相同，但從不同的位置，可能會導致非預期的結果 （將使用第一個組件）。

<a name="MT0110" />

### <a name="mt0110-incremental-builds-have-been-disabled-because-this-version-of-xamarinios-does-not-support-incremental-builds-in-projects-that-include-third-party-binding-libraries-and-that-compiles-to-bitcode"></a>MT0110:因為此版本的 Xamarin.iOS 不支援累加建置專案，包含第三方繫結程式庫，且會編譯成 bitcode 中已停用累加建置。

因為此版本的 Xamarin.iOS 不支援累加建置專案，包含第三方繫結程式庫，且會編譯成 bitcode （tvOS 和 watchOS 專案） 中已停用累加建置。

您需要採取任何動作，此訊息僅供參考。

如需進一步資訊 bug #[51710](https://bugzilla.xamarin.com/show_bug.cgi?id=51710)。

不會再報告這個警告。

<a name="MT0111" />

### <a name="mt0111-bitcode-has-been-enabled-because-this-version-of-xamarinios-does-not-support-building-watchos-projects-using-llvm-without-enabling-bitcode"></a>MT0111:Bitcode 尚未啟用，因為此版本的 Xamarin.iOS 不支援建置 watchOS 專案而不啟用 bitcode 使用 LLVM。

Bitcode 已啟用自動因為此版本的 Xamarin.iOS 不支援建置 watchOS 專案而不啟用 bitcode 使用 LLVM。

您需要採取任何動作，此訊息僅供參考。

如需進一步資訊 bug #[51634](https://bugzilla.xamarin.com/show_bug.cgi?id=51634)。

<a name="MT0112" />

### <a name="mt0112-native-code-sharing-has-been-disabled-because-"></a>MT0112:原生程式碼共用已停用，因為 *

有多個原因，可以停用共用程式碼：

* 因為容器應用程式的部署目標是早於 iOS 8.0 以上版本 (它有 *))。

與 iOS 8.0 以上版本導入在因為共用原生程式碼使用使用者架構實作，原生程式碼共用需要 iOS 8.0 以上版本。

* 因為容器應用程式包含 I18N 組件 （*）。

目前不支援原生程式碼共用，如果容器應用程式包含 I18N 組件。

* 因為容器應用程式有自訂的 xml 定義的受管理的連結器 （*）。

原生程式碼共用需要不支援受管理的連結器使用自訂的 xml 定義的專案。

<a name="MT0113" />

### <a name="mt0113-native-code-sharing-has-been-disabled-for-the-extension--because-"></a>MT0113:原生程式碼共用已停用擴充功能 ' *' 因為 *。

* 因為容器應用程式之間的差異的 bitcode 選項 (\*) 和延伸模組 (\*)。

  原生程式碼共用需要的 bitcode 選項符合之間共用程式碼的專案。

* 因為--組件建置目標有不同的容器應用程式之間的選項 (\*) 和延伸模組 (\*)。

  原生程式碼共用需要--組件建置目標的選項都是一樣的共用程式碼的專案。

  為不啟用或停用所有專案中，如果累加建置會發生這種情況。

* 因為 I18N 組件是不同的容器應用程式 (\*) 和延伸模組 (\*)。

  原生程式碼共用目前不支援包含 I18N 組件的延伸模組。

* 由於 AOT 編譯器的引數是不同的容器應用程式 (\*) 和延伸模組 (\*)。

  原生程式碼共用需要 AOT 編譯器的引數亦共用程式碼的專案之間無差異。

* 由於 AOT 編譯器的其他引數是不同的容器應用程式 (\*) 和延伸模組 (\*)。

  原生程式碼共用需要 AOT 編譯器的引數亦共用程式碼的專案之間無差異。

  如果 '執行所有 32 位元浮點作業與 64 位元浮點數' 不同的專案，就會發生這種情況。

* 因為 LLVM 未啟用或停用這兩個容器應用程式中 (\*) 和延伸模組 (\*)。

  原生程式碼共用需要 LLVM 會是啟用或停用所有共用程式碼的專案。

* 因為受管理的連結器設定不同的容器應用程式之間 (\*) 和延伸模組 (\*)。

  原生程式碼共用需要受管理的連結器設定完全相同的共用程式碼的所有專案。

* 因為受管理的連結器略過的組件是不同的容器應用程式 (\*) 和延伸模組 (\*)。

  原生程式碼共用需要受管理的連結器設定完全相同的共用程式碼的所有專案。

* 因為擴充功能有自訂的 xml 定義的受管理的連結器 （*）。

  原生程式碼共用需要不支援受管理的連結器使用自訂的 xml 定義的專案。

* 因為容器應用程式不會建置 abi * （雖然此 ABI 會建置擴充功能）。

  原生程式碼共用需要容器應用程式建置適用於任何應用程式擴充功能會針對建立的所有架構。

  例如： ARM64 + ARMv7，建置延伸模組，但只建置適用於 ARM64 的容器應用程式時，會發生此狀況。

* 因為容器應用程式專為 ABI \*，這不是與擴充功能的 ABI 相容 (\*)。

  原生程式碼共用需要的所有專案，都建置完全相同的 API。

  例如： 為 ARMv7 + llvm thumb2，建置延伸模組，但為 ARMv7 + llvm 只建置容器應用程式時，會發生此狀況。

* 因為容器應用程式所參考的組件 '\*'from'\*'，而延伸模組參考中的不同版本' *'。

  原生程式碼共用需要共用程式碼的所有專案，都使用的所有組件的版本相同。

<!-- MT0114: used by mmp -->

<a name="MT0115" />

### <a name="mt0115-it-is-recommended-to-reference-dynamic-symbols-using-code---dynamic-symbol-modecode-when-bitcode-is-enabled"></a>MT0115:建議您參考動態使用程式碼的符號 (-動態符號模式 = 程式碼) 啟用 bitcode 時。

Xamarin.iOS 專案會經常參考原生的符號，以動態方式表示原生連結器可能會移除這類原生的符號，在原生的連結過程中，因為原生連結器不會看到，會使用這些符號。

通常是 Xamarin.iOS 的原生連結器来保留這類符號會被要求 (使用`-u symbol`連結器旗標)，但是當編譯 bitcode 原生連結器不接受`-u`旗標。

Xamarin.iOS 已實作替代方案： 我們會產生額外的原生程式碼會參考這些符號，並因此原生連結器將會看到，會使用這些符號。 這會自動完成編譯為 bitcode。

如果`--dynamic-symbol-mode=linker`傳遞給 mtouch，此替代方案將會停用，和 Xamarin.iOS 會嘗試將傳遞`-u`原生連結器。 這很可能會導致原生連結器錯誤。

解決方法是移除`--dynamic-symbol-mode=linker`從專案的組建選項中的其他 mtouch 引數的引數。

<!-- 0116 - 0124: free to use -->

<a name="MT0116" />

### <a name="mt0116-invalid-architecture-arch-32-bit-architectures-are-not-supported-when-deployment-target-is-11-or-later-make-sure-the-project-does-not-build-for-a-32-bit-architecture"></a>MT0116:無效的架構: {arch}。 部署目標為 11 或更新版本時，不支援 32 位元架構。 請確定不會建置專案，這是適用於 32 位元架構。

iOS 11 不包含 32 位元應用程式，支援，因此它不支援建置 32 位元應用程式的部署目標是 iOS 11 或更新版本。

Arm64，變更專案的 iOS 組建選項中的目標架構，或變更部署目標專案的 Info.plist 中的較舊 iOS 版本。

<a name="MT0117" />

### <a name="mt0117-cant-launch-a-32-bit-app-on-a-simulator-that-only-supports-64-bit"></a>MT0117:無法啟動模擬器僅支援 64 位元上的 32 位元應用程式。

<a name="MT0118" />

### <a name="mt0118-aot-files-could-not-be-found-at-the-expected-directory-msymdir"></a>MT0118:在預期的目錄 '{msymdir}' 中找不到 Aot 檔案。

<!-- 0119 - 0123: free to use -->

<a name="MT0123" />

### <a name="mt0123-the-executable-assembly--does-not-reference-"></a>MT0123:可執行組件 * 未參考 *。

平台組件找不到任何參考 (Xamarin.iOS.dll / Xamarin.TVOS.dll / Xamarin.WatchOS.dll) 可執行檔的組件中。

這通常發生在使用任何平台組件中，從可執行檔專案中沒有任何程式碼執行個體的空白的 Main 方法 （和任何其他程式碼） 會顯示此錯誤︰

```csharp
class Program {
    void Main (string[] args)
    {
    }
}
```

使用平台組件的 API，將會解決錯誤：

```csharp
class Program {
    void Main (string[] args)
    {
        System.Console.WriteLine (typeof (UIKit.UIWindow));
    }
}
```

<a name="MT0124" />

### <a name="mt0124-could-not-set-the-current-language-to-lang-according-to-langlang-exception"></a>MT0124:無法設定目前的語言為 '{lang}' （根據 l a n G = {LANG}）: {exception}

這是警告，指出目前的語言，無法設定為錯誤訊息中的語言。

目前的語言會預設為系統語言。

<a name="MT0125" />

### <a name="mt0125-the---assembly-build-target-command-line-argument-is-ignored-in-the-simulator"></a>MT0125:--組件建置目標命令列引數會忽略在模擬器中。

不不需要任何動作，此訊息僅供參考。

<a name="MT0126" />

### <a name="mt0126-incremental-builds-have-been-disabled-because-incremental-builds-are-not-supported-in-the-simulator"></a>MT0126:累加建置已停用，因為在模擬器中不支援累加建置。

不不需要任何動作，此訊息僅供參考。

<a name="MT0127" />

### <a name="mt0127-incremental-builds-have-been-disabled-because-this-version-of-xamarinios-does-not-support-incremental-builds-in-projects-that-include-more-than-one-third-party-binding-libraries"></a>MT0127:因為此版本的 Xamarin.iOS 不支援累加建置包含多個協力廠商的繫結程式庫的專案中已停用累加建置。

自動擁有停用累加建置，因為此版本的 Xamarin.iOS 不會永遠建置具有多個第三方繫結程式庫專案正確。

不不需要任何動作，此訊息僅供參考。

如需進一步資訊 bug #[52727](https://bugzilla.xamarin.com/show_bug.cgi?id=52727)。

<a name="MT0128" />

### <a name="mt0128-could-not-touch-the-file--"></a>MT0128:可能不碰觸到檔案 ' *': *

碰觸的檔案 （這為了要確保正確地完成部分組建） 時，就會發生失敗。

最有可能可以忽略這個警告;發生任何問題時提報 bug (https://bugzilla.xamarin.com] (https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)) ，將會加以調查。

## <a name="mt1xxx-project-related-error-messages"></a>MT1xxx:專案相關的錯誤訊息

### <a name="mt10xx-installer--mtouch"></a>MT10xx:安裝程式 / mtouch

<!--
 MT1xxx file copy / symlinks (project related)
  MT10xx installer.cs / mtouch.cs
  -->

<a name="MT1001" />

### <a name="mt1001-could-not-find-an-application-at-the-specified-directory"></a>MT1001:找不到指定目錄的應用程式

<a name="MT1002" />

### <a name="mt1002-could-not-create-symlinks-files-were-copied"></a>MT1002:無法建立符號連結、 已複製檔案

<a name="MT1003" />

### <a name="mt1003-could-not-kill-the-application--you-may-have-to-kill-the-application-manually"></a>MT1003:無法終止應用程式 ' *'。 您可能必須手動終止的應用程式。

<a name="MT1004" />

### <a name="mt1004-could-not-get-the-list-of-installed-applications"></a>MT1004:無法取得已安裝的應用程式清單。

<a name="MT1005" />

### <a name="mt1005-could-not-kill-the-application--on-the-device----you-may-have-to-kill-the-application-manually"></a>MT1005:無法終止應用程式 '\*'上的裝置\*': *-您可能必須手動終止的應用程式。

<a name="MT1006" />

### <a name="mt1006-could-not-install-the-application--on-the-device--"></a>MT1006:無法安裝應用程式 '\*'上的裝置\*': *。

<a name="MT1007" />

### <a name="mt1007-failed-to-launch-the-application--on-the-device---you-can-still-launch-the-application-manually-by-tapping-on-it"></a>MT1007:無法啟動應用程式 '\*'上的裝置\*': *。 您仍然可以在其上的點選以手動方式啟動應用程式。

<a name="MT1008" />

### <a name="mt1008-failed-to-launch-the-simulator"></a>MT1008:無法啟動模擬器

如果 mtouch 無法啟動模擬器時，會報告此錯誤。   這種情形有時因為已執行的過時和無效的模擬器處理序。

在 Unix 命令列上發出下列命令可用來刪除停滯的模擬器處理序：

```bash
$ launchctl list|grep UIKitApplication|awk '{print $3}'|xargs launchctl remove
```

<a name="MT1009" />

### <a name="mt1009-could-not-copy-the-assembly--to--"></a>MT1009:無法複製組件 '\*'到'\*': *

這是特定版本的 Xamarin.iOS 中已知的問題。

如果您發生此問題，請嘗試下列因應措施：

```bash
sudo chmod 0644 /Library/Frameworks/Xamarin.iOS.framework/Versions/Current/lib/mono/*/*.mdb
```

不過，因為在最新的 Xamarin.iOS 版本中已解決此問題，請將新 bug 歸檔在[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)完整版本資訊與組建記錄檔輸出。

<a name="MT1010" />

### <a name="mt1010-could-not-load-the-assembly--"></a>MT1010:無法載入組件 ' *': *

<a name="MT1011" />

### <a name="mt1011-could-not-add-missing-resource-file-"></a>MT1011:無法加入遺漏的資源檔: ' *'

<a name="MT1012" />

### <a name="mt1012-failed-to-list-the-apps-on-the-device--"></a>MT1012:無法列出在裝置上的應用程式 ' *': *

<a name="MT1013" />

### <a name="mt1013-dependency-tracking-error-no-files-to-compare-please-file-a-bug-report-at-httpbugzillaxamarincom-with-a-test-case"></a>MT1013:相依性追蹤錯誤： 沒有要比較的檔案。 請提出錯誤報告在 http://bugzilla.xamarin.com與測試案例。

這表示在 Xamarin.iOS 中的 bug。 請將 bug 歸檔在[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)與測試 caes。

<a name="MT1014" />

### <a name="mt1014-failed-to-re-use-cached-version-of--"></a>MT1014:無法重新使用的快取的版本 ' *': *。

<a name="MT1015" />

### <a name="mt1015-failed-to-create-the-executable--"></a>MT1015:無法建立可執行檔 ' *': *

<a name="MT1015" />

### <a name="mt1015-failed-to-create-the-executable--"></a>MT1015:無法建立可執行檔 ' *': *

<a name="MT1016" />

### <a name="mt1016-failed-to-create-the-notice-file-because-a-directory-already-exists-with-the-same-name"></a>MT1016:無法建立通知檔案，因為目錄已存在具有相同名稱。

移除目錄`NOTICE`從專案。

<a name="MT1017" />

### <a name="mt1017-failed-to-create-the-notice-file-"></a>MT1017:無法建立通知檔案: *。

<a name="MT1018" />

### <a name="mt1018-your-application-failed-code-signing-checks-and-could-not-be-installed-on-the-device--check-your-certificates-provisioning-profiles-and-bundle-ids-probably-your-device-is-not-part-of-the-selected-provisioning-profile-error-0xe8008015"></a>MT1018:您的應用程式程式碼簽章檢查失敗，並無法安裝在裝置上 ' *'。 請檢查您的憑證，佈建設定檔，以及套件組合識別碼。 可能您的裝置不是所選的佈建設定檔的一部分 (錯誤：0xe8008015)。

<a name="MT1019" />

### <a name="mt1019-your-application-has-entitlements-not-supported-by-your-current-provisioning-profile-and-could-not-be-installed-on-the-device--please-check-the-ios-device-log-for-more-detailed-information-error-0xe8008016"></a>MT1019:您的應用程式具有不支援您目前的佈建設定檔的權利和無法安裝在裝置上 ' *'。 如需詳細資訊，請參閱 iOS 裝置記錄檔 (錯誤：0xe8008016)。

如果此情形：

* 您的應用程式有目前的佈建設定檔不支援的權利。
  可能的解決方案：
  - 指定不同的佈建設定檔支援的權利，您的應用程式的需求。
  - 移除目前的佈建設定檔中不支援的權利。
* 您嘗試部署至不包含在您使用的佈建設定檔裝置。
  可能的解決方案：
  - 從範本，以在 Xcode 中建立新的應用程式，選取相同的佈建設定檔，並部署至相同裝置。 有時候 Xcode 重新整理佈建設定檔與新的裝置 （Xcode 會詢問您要執行的其他情況下）。
  -移至 iOS 開發人員中心並更新佈建設定檔與新的裝置，然後下載到您電腦的更新佈建設定檔。

在 iOS 裝置記錄檔將會顯示有關失敗的詳細資訊的大部分情況下，這可以幫助診斷問題。

<a name="MT1020" />

### <a name="mt1020-failed-to-launch-the-application--on-the-device--"></a>MT1020:無法啟動應用程式 '\*'上的裝置\*': *

<a name="MT1021" />

### <a name="mt1021-could-not-copy-the-file--to--2"></a>MT1021:無法複製檔案 '\*'到'\*': {2}

無法複製檔案。 複製作業的錯誤訊息有錯誤的詳細資訊。

<a name="MT1022" />

### <a name="mt1022-could-not-copy-the-directory--to--2"></a>MT1022:無法複製目錄 '\*'到'\*': {2}

無法複製目錄。 複製作業的錯誤訊息有錯誤的詳細資訊。

<a name="MT1023" />

### <a name="mt1023-could-not-communicate-with-the-device-to-find-the-application---"></a>MT1023:無法與裝置，以尋找應用程式通訊以 ' *': *

嘗試將查閱裝置上的應用程式時，就會發生錯誤。

若要嘗試解決這個問題的重要事項︰

* 從裝置刪除應用程式，並再試一次。
* 中斷連接裝置並重新建立。
* 重新啟動裝置。
* 重新啟動 mac。

<a name="MT1024" />

### <a name="mt1024-the-application-signature-could-not-be-verified-on-device--please-make-sure-that-the-provisioning-profile-is-installed-and-not-expired-error-0xe8008017"></a>MT1024:無法在裝置上驗證應用程式簽章 ' *'。 請確定佈建設定檔已安裝且過期 (錯誤：0xe8008017)。

裝置會拒絕應用程式進行安裝，因為無法驗證簽章。

請確定已安裝佈建設定檔，並不過期。

<a name="MT1025" />

### <a name="mt1025-could-not-list-the-crash-reports-on-the-device-"></a>MT1025:無法列出在裝置上的損毀報表 *。

嘗試將列出在裝置上的損毀報告時，就會發生錯誤。

若要嘗試解決這個問題的重要事項︰

* 從裝置刪除應用程式，並再試一次。
* 中斷連接裝置並重新建立。
* 重新啟動裝置。
* 重新啟動 mac。
* 同步處理裝置與 iTunes （這會從裝置移除任何損毀報告）。

<a name="MT1026" />

### <a name="mt1026-could-not-download-the-crash-report--from-the-device-"></a>MT1026:無法下載的當機報告 * 從裝置 *。

嘗試從裝置下載損毀報告時，就會發生錯誤。

若要嘗試解決這個問題的重要事項︰

* 從裝置刪除應用程式，並再試一次。
* 中斷連接裝置並重新建立。
* 重新啟動裝置。
* 重新啟動 mac。
* 同步處理裝置與 iTunes （這會從裝置移除任何損毀報告）。

<a name="MT1027" />

### <a name="mt1027-cant-use-xcode-7-to-launch-applications-on-devices-with-ios--xcode-7-only-supports-ios-6"></a>MT1027:無法啟動 iOS 裝置上的應用程式使用 Xcode 7 + * （Xcode 7 只支援 iOS 6 +）。

您不可以使用 Xcode 7 + 啟動 iOS 版本，低於 6.0 裝置上的應用程式。

請使用較舊版本的 Xcode，或點選應用程式以手動方式啟動它。

<a name="MT1028" />

### <a name="mt1028-invalid-device-specification--expected-ios-watchos-or-all"></a>MT1028:不正確的裝置規格: ' *'。 必須是 'ios'、 'watchos' 或者 'all'。

裝置規格可讓您傳遞使用--裝置無效。 有效值為: 'ios'、 'watchos' 或 'all'。

<a name="MT1029" />

### <a name="mt1029-could-not-find-an-application-at-the-specified-directory-"></a>MT1029:找不到指定目錄的應用程式: *

傳遞給-launchdev 的應用程式路徑不存在。 請指定有效的應用程式套件組合。

<a name="MT1030" />

### <a name="mt1030-launching-applications-on-device-using-a-bundle-identifier-is-deprecated-please-pass-the-full-path-to-the-bundle-to-launch"></a>MT1030:啟動應用程式在裝置上使用套件組合識別碼已被取代。 請將完整路徑傳遞至要啟動的組合中。

建議將路徑傳遞至應用程式，而非只是套件組合識別碼的裝置上啟動。

<a name="MT1031" />

### <a name="mt1031-could-not-launch-the-app--on-the-device--because-the-device-is-locked-please-unlock-the-device-and-try-again"></a>MT1031:無法啟動應用程式 '\*'上的裝置\*' 因為裝置已被鎖定。 請將裝置解除鎖定，然後再試一次。

請將裝置解除鎖定，然後再試一次。

<a name="MT1032" />

### <a name="mt1032-this-application-executable-might-be-too-large--mb-to-execute-on-device-if-bitcode-was-enabled-you-might-want-to-disable-it-for-development-it-is-only-required-to-submit-applications-to-apple"></a>MT1032:此應用程式可執行檔可能會太大 (* MB) 裝置上執行。 如果已啟用 bitcode 要停用它進行開發，只需要提交給 Apple 的應用程式。

<a name="MT1033" />

### <a name="mt1033-could-not-uninstall-the-application--from-the-device--"></a>MT1033:無法解除安裝應用程式 '\*「 from 裝置'\*': *

<!-- 1034 used by mmp -->

<a name="MT1035" />

### <a name="mt1035-cannot-include-different-versions-of-the-framework-name"></a>MT1035:不能包含不同的 framework 版本為 '{name}'

您不可能時要連結的不同版本的相同的架構。

此外，這通常會發生於延伸模組參考不同版本的架構比容器應用程式 （可能是透過第三方繫結組件）。

遵循此錯誤會有多個[MT1036](#MT1036)列出之路徑的每個不同的架構的錯誤。

<a name="MT1036" />

### <a name="mt1036-framework-name-included-from-path-related-to-previous-error"></a>MT1036:來自所包含的 framework '{name}': {path} （與之前錯誤相關）

會報告此錯誤只搭配[MT1036](#MT1036)。 請參閱[MT1036](#MT1036)如需詳細資訊。

### <a name="mt11xx-debug-service"></a>MT11xx:偵錯服務

<!--
  MT11xx DebugService.cs
  -->

<a name="MT1101" />

### <a name="mt1101-could-not-start-app"></a>MT1101:無法啟動應用程式

<a name="MT1102" />

### <a name="mt1102-could-not-attach-to-the-app-to-kill-it-"></a>MT1102:無法附加至應用程式 （以終止為止）: *

<a name="MT1103" />

### <a name="mt1103-could-not-detach"></a>MT1103:無法中斷連結

<a name="MT1104" />

### <a name="mt1104-failed-to-send-packet-"></a>MT1104:無法傳送封包: *

<a name="MT1105" />

### <a name="mt1105-unexpected-response-type"></a>MT1105:未預期的回應類型

<a name="MT1106" />

### <a name="mt1106-could-not-get-list-of-applications-on-the-device-request-timed-out"></a>MT1106:無法在裝置上取得應用程式的清單：要求已逾時。

<a name="MT1107" />

### <a name="mt1107-application-failed-to-launch-"></a>MT1107:應用程式無法啟動: *

請檢查您的裝置是否鎖定。

如果您要部署企業應用程式，或使用免費的佈建設定檔，您可能需要信任的開發人員 (這會說明<a href="https://stackoverflow.com/a/30726375/183422">此處</a>)。

<a name="MT1108" />

### <a name="mt1108-could-not-find-developer-tools-for-this-xx-yy-device"></a>MT1108:找不到此的 XX (YY) 裝置的開發人員工具。

從 mtouch 的幾個作業都需要<tt>DeveloperDiskImage.dmg</tt>檔案會出現。   此檔案屬於 Xcode 的而且通常位於相對於您用來在建置，SDK <tt>Xcode.app/Contents/Developer/iPhoneOS.platform/DeviceSupport/VERSION/DeveloperDiskImage.dmg</tt>。

這就會發生錯誤是因為您不需要符合您已連接之裝置 DeveloperDiskImage.dmg。

<a name="MT1109" />

### <a name="mt1109-application-failed-to-launch-because-the-device-is-locked-please-unlock-the-device-and-try-again"></a>MT1109:應用程式無法啟動，因為在裝置鎖定。 請將裝置解除鎖定，然後再試一次。

請檢查您的裝置是否鎖定。

<a name="MT1110" />

### <a name="mt1110-application-failed-to-launch-because-of-ios-security-restrictions-please-ensure-the-developer-is-trusted"></a>MT1110:應用程式無法啟動，因為 iOS 安全性限制。 請確定開發人員的受信任。

如果您要部署企業應用程式，或使用免費的佈建設定檔，您可能需要信任的開發人員 (這會說明<a href="https://stackoverflow.com/a/30726375/183422">此處</a>)。

<a name="MT1111" />

### <a name="mt1111-application-launched-successfully-but-its-not-possible-to-wait-for-the-app-to-exit-as-requested-because-its-not-possible-to-detect-app-termination-when-launching-using-gdbserver"></a>MT1111:應用程式啟動成功，但它的不可以等候結束要求，因為它不能啟動使用 gdbserver 偵測應用程式終止的應用程式。

### <a name="mt12xx-simulator"></a>MT12xx:模擬器

<!--
  MT12xx simcontroller.cs
  -->

<a name="MT1201" />

### <a name="mt1201-could-not-load-the-simulator-"></a>MT1201:無法載入模擬器: *

<a name="MT1202" />

### <a name="mt1202-invalid-simulator-configuration-"></a>MT1202:無效的模擬器組態: *

<a name="MT1203" />

### <a name="mt1203-invalid-simulator-specification-"></a>MT1203:無效的模擬器規格: *

<a name="MT1204" />

### <a name="mt1204-invalid-simulator-specification--runtime-not-specified"></a>MT1204:無效的模擬器規格 ' *': 未指定的執行階段。

<a name="MT1205" />

### <a name="mt1205-invalid-simulator-specification--device-type-not-specified"></a>MT1205:無效的模擬器規格 ' *': 未指定的裝置類型。

<a name="MT1206" />

### <a name="mt1206-could-not-find-the-simulator-runtime-"></a>MT1206:找不到模擬器執行階段 ' *'。

<a name="MT1207" />

### <a name="mt1207-could-not-find-the-simulator-device-type-"></a>MT1207:找不到模擬器裝置類型 ' *'。

<a name="MT1208" />

### <a name="mt1208-could-not-find-the-simulator-runtime-"></a>MT1208:找不到模擬器執行階段 ' *'。

<a name="MT1209" />

### <a name="mt1209-could-not-find-the-simulator-device-type-"></a>MT1209:找不到模擬器裝置類型 ' *'。

<a name="MT1210" />

### <a name="mt1210-invalid-simulator-specification--unknown-key-"></a>MT1210:無效的模擬器規格： \*，未知的索引鍵 '\*'

<a name="MT1211" />

### <a name="mt1211-the-simulator-version--does-not-support-the-simulator-type-"></a>MT1211:模擬器版本 '\*'不支援模擬器類型'\*'

<a name="MT1212" />

### <a name="mt1212-failed-to-create-a-simulator-version-where-type---and-runtime--"></a>MT1212:無法建立模擬器版本，類型 = * 和執行階段 = *。

<a name="MT1213" />

### <a name="mt1213-invalid-simulator-specification-for-xcode-4-"></a>MT1213:無效的模擬器規格 Xcode 4: *

<a name="MT1214" />

### <a name="mt1214-invalid-simulator-specification-for-xcode-5-"></a>MT1214:無效的模擬器規格 Xcode 5: *

<a name="MT1215" />

### <a name="mt1215-invalid-sdk-specified-"></a>MT1215:指定了無效的 SDK: *

<a name="MT1216" />

### <a name="mt1216-could-not-find-the-simulator-udid-"></a>MT1216:找不到模擬器 UDID ' *'。

<a name="MT1217" />

### <a name="mt1217-could-not-load-the-app-bundle-at-"></a>MT1217:無法載入應用程式套件組合，在 ' *'。

<a name="MT1218" />

### <a name="mt1218-no-bundle-identifier-found-in-the-app-at-"></a>MT1218:在應用程式中找到的任何套件組合識別碼 ' *'。

<a name="MT1219" />

### <a name="mt1219-could-not-find-the-simulator-for-"></a>MT1219:找不到適用的模擬器 ' *'。

<a name="MT1220" />

### <a name="mt1220-could-not-find-the-latest-simulator-runtime-for-device-"></a>MT1220:找不到裝置的最新的模擬器執行階段 ' *'。

這通常表示 Xcode 的問題。

若要嘗試修正此問題的重要事項︰

* 在 Xcode 中，一次使用模擬器。
* 傳遞明確的 SDK 版本，使用-sdk <version>。
* 重新安裝 Xcode。

<a name="MT1221" />

### <a name="mt1221-could-not-find-the-paired-iphone-simulator-for-the-watchos-simulator-"></a>MT1221:找不到 WatchOS 模擬器配對的 iPhone 模擬器 ' *'。

當啟動 WatchOS 應用程式在 WatchOS 模擬器中的，必須是配對的 iOS 模擬器以及。

觀看模擬器可搭配 iOS 模擬器使用 Xcode 的裝置 UI (視窗 功能表-> 裝置)。

### <a name="mt13xx-linkwith"></a>MT13xx: [LinkWith]

<!--
  MT13xx [LinkWith]
  -->

<a name="MT1301" />

### <a name="mt1301-native-library---was-ignored-since-it-does-not-match-the-current-build-architectures-"></a>MT1301:原生程式庫`*`(\*) 已被忽略，因為它不符合目前的組建 architecture(s) (\*)

<a name="MT1302" />

### <a name="mt1302-could-not-extract-the-native-library--from--please-ensure-the-native-library-was-properly-embedded-in-the-managed-assembly-if-the-assembly-was-built-using-a-binding-project-the-native-library-must-be-included-in-the-project-and-its-build-action-must-be-objcbindingnativelibrary"></a>MT1302:無法擷取原生程式庫 ' *' 從 '+'。 請確定原生程式庫已正確內嵌的 managed 組件中 （如果使用繫結專案建置的組件時，原生程式庫必須包含在專案中，且其 建置動作必須是 'ObjcBindingNativeLibrary'）。

<a name="MT1303" />

### <a name="mt1303-could-not-decompress-the-native-framework--from--please-review-the-build-log-for-more-information-from-the-native-zip-command"></a>MT1303:無法解壓縮原生架構 '\*'from'\*'。 請檢閱建置記錄檔，如需詳細資訊，請從原生 'zip' 命令。

無法解壓縮從繫結程式庫指定的原生架構。

請檢閱 bulid 記錄檔，如需有關此原生 'zip' 命令失敗。

<a name="MT1304" />

### <a name="mt1304-the-embedded-framework--in--is-invalid-it-does-not-contain-an-infoplist"></a>MT1304:內嵌的 framework ' *' 在 * 無效： 它不包含 Info.plist。

指定內嵌的架構不包含 Info.plist 中，並因此不是有效的架構。

請確定 「 架構 」 是有效的。

<a name="MT1305" />

### <a name="mt1305-the-binding-library--contains-a-user-framework--but-embedded-user-frameworks-require-ios-80-the-current-deployment-target-is--please-set-the-deployment-target-in-the-infoplist-file-to-at-least-80"></a>MT1305:繫結程式庫 '\*' 包含的使用者架構 (\*)，但內嵌的使用者架構需要 iOS 8.0 以上版本 (目前的部署目標是 *)。 請至少 8.0 Info.plist 檔案中將部署目標。

指定的繫結程式庫包含內嵌的架構，但 Xamarin.iOS ios 8.0 或更新版本只支援內嵌的架構。

請至少可解決此錯誤的 8.0 Info.plist 檔案中將部署目標 （或不使用內嵌的架構）。

### <a name="mt14xx-crash-reports"></a>MT14xx:當機報告

<!--
  MT14xx    CrashReports.cs
  -->

<a name="MT1400" />

### <a name="mt1400-could-not-open-crash-report-service-afcconnectionopen-returned-"></a>MT1400:無法開啟損毀報表服務：AFCConnectionOpen 傳回 *

嘗試從裝置存取的當機報告時，就會發生錯誤。

若要嘗試解決這個問題的重要事項︰

* 從裝置刪除應用程式，並再試一次。
* 中斷連接裝置並重新建立。
* 重新啟動裝置。
* 重新啟動 mac。
* 同步處理裝置與 iTunes （這會從裝置移除任何損毀報告）。

<a name="MT1401" />

### <a name="mt1401-could-not-close-crash-report-service-afcconnectionclose-returned-"></a>MT1401:無法關閉損毀報表服務：AFCConnectionClose 傳回 *

嘗試從裝置存取的當機報告時，就會發生錯誤。

若要嘗試解決這個問題的重要事項︰

* 從裝置刪除應用程式，並再試一次。
* 中斷連接裝置並重新建立。
* 重新啟動裝置。
* 重新啟動 mac。
* 同步處理裝置與 iTunes （這會從裝置移除任何損毀報告）。

<a name="MT1402" />

### <a name="mt1402-could-not-read-file-info-for--afcfileinfoopen-returned-"></a>MT1402:無法讀取的檔案資訊 *:AFCFileInfoOpen 傳回 *

嘗試從裝置存取的當機報告時，就會發生錯誤。

若要嘗試解決這個問題的重要事項︰

* 從裝置刪除應用程式，並再試一次。
* 中斷連接裝置並重新建立。
* 重新啟動裝置。
* 重新啟動 mac。
* 同步處理裝置與 iTunes （這會從裝置移除任何損毀報告）。

<a name="MT1403" />

### <a name="mt1403-could-not-read-crash-report-afcdirectoryopen--returned-"></a>MT1403:無法讀取的當機報告：AFCDirectoryOpen （*） 傳回: *

嘗試從裝置存取的當機報告時，就會發生錯誤。

若要嘗試解決這個問題的重要事項︰

* 從裝置刪除應用程式，並再試一次。
* 中斷連接裝置並重新建立。
* 重新啟動裝置。
* 重新啟動 mac。
* 同步處理裝置與 iTunes （這會從裝置移除任何損毀報告）。

<a name="MT1404" />

### <a name="mt1404-could-not-read-crash-report-afcfilerefopen--returned-"></a>MT1404:無法讀取的當機報告：AFCFileRefOpen （*） 傳回: *

嘗試從裝置存取的當機報告時，就會發生錯誤。

若要嘗試解決這個問題的重要事項︰

* 從裝置刪除應用程式，並再試一次。
* 中斷連接裝置並重新建立。
* 重新啟動裝置。
* 重新啟動 mac。
* 同步處理裝置與 iTunes （這會從裝置移除任何損毀報告）。

<a name="MT1405" />

### <a name="mt1405-could-not-read-crash-report-afcfilerefread--returned-"></a>MT1405:無法讀取的當機報告：AFCFileRefRead （*） 傳回: *

嘗試從裝置存取的當機報告時，就會發生錯誤。

若要嘗試解決這個問題的重要事項︰

* 從裝置刪除應用程式，並再試一次。
* 中斷連接裝置並重新建立。
* 重新啟動裝置。
* 重新啟動 mac。
* 同步處理裝置與 iTunes （這會從裝置移除任何損毀報告）。

<a name="MT1406" />

### <a name="mt1406-could-not-list-crash-reports-afcdirectoryopen--returned-"></a>MT1406:無法列出當機報告：AFCDirectoryOpen （*） 傳回: *

嘗試從裝置存取的當機報告時，就會發生錯誤。

若要嘗試解決這個問題的重要事項︰

* 從裝置刪除應用程式，並再試一次。
* 中斷連接裝置並重新建立。
* 重新啟動裝置。
* 重新啟動 mac。
* 同步處理裝置與 iTunes （這會從裝置移除任何損毀報告）。

<!--- 1407 used by mmp -->

### <a name="mt16xx-macho"></a>MT16xx:MachO

<!--
  MT16xx    MachO.cs
  -->

<a name="MT1600" />

### <a name="mt1600-not-a-mach-o-dynamic-library-unknown-header-0x-"></a>MT1600:不是 MACH-O 動態程式庫 （未知標頭 ' 0 x *'）: *。

處理的動態程式庫時發生錯誤。

請確定動態程式庫是有效的 MACH-O 動態程式庫。

可以使用驗證程式庫格式`file`從終端機命令：

    file -arch all -l /path/to/library.dylib

<a name="MT1601" />

### <a name="mt1601-not-a-static-library-unknown-header--"></a>MT1601:不是靜態程式庫 (未知標頭 ' *'): *。

處理的靜態程式庫時發生錯誤。

請確定靜態程式庫是有效的 MACH-O 靜態程式庫。

可以使用驗證程式庫格式`file`從終端機命令：

    file -arch all -l /path/to/library.a

<a name="MT1602" />

### <a name="mt1602-not-a-mach-o-dynamic-library-unknown-header-0x-"></a>MT1602:不是 MACH-O 動態程式庫 （未知標頭 ' 0 x *'）: *。

處理的動態程式庫時發生錯誤。

請確定動態程式庫是有效的 MACH-O 動態程式庫。

可以使用驗證程式庫格式`file`從終端機命令：

    file -arch all -l /path/to/library.dylib

<a name="MT1603" />

### <a name="mt1603-unknown-format-for-fat-entry-at-position--in-"></a>MT1603:未知的格式，fat 項目的位置 * 的 *。

處理有問題的 fat 封存時發生錯誤。

請確定 fat 封存有效。

可以使用驗證的 fat 封存格式`file`從終端機命令：

    file -arch all -l /path/to/file

<a name="MT1604" />

### <a name="mt1604-file-of-type--is-not-a-macho-file-"></a>MT1604:類型的檔案 * 不是 MachO 檔案 （*）。

處理 MachO 檔案有問題時發生錯誤。

請確定檔案是有效的 MACH-O 動態程式庫。

可以使用驗證檔案的格式`file`從終端機命令：

    file -arch all -l /path/to/file

## <a name="mt2xxx-linker-error-messages"></a>MT2xxx:連結器錯誤訊息

<!--
 MT2xxx Linker
  MT20xx Linker (general) errors
  -->

<a name="MT2001" />

### <a name="mt2001-could-not-link-assemblies"></a>MT2001:無法將組件的連結

此錯誤表示受管理的連結器遇到意外的錯誤，例如例外狀況，並無法完成或儲存處理組件。 確切的錯誤的詳細資訊會屬於組建記錄檔例如

```
error MT2001: Could not link assemblies.
    Method: `System.Void Todo.TodoListPageCS/<<-ctor>b__1_0>d::SetStateMachine(System.Runtime.CompilerServices.IAsyncStateMachine)`
    Assembly: `QuickTodo, Version=1.0.6297.28241, Culture=neutral, PublicKeyToken=null`
Reason: Value cannot be null.
Parameter name: instruction
```

請務必提出這類問題的 bug 報告。 在大部分情況下發行適當的修正程式之前可以提供因應措施。 若要解決此問題，上述資訊是關鍵 （以及測試案例和/或組件 binairy）。

<a name="MT2002" />

### <a name="mt2002-can-not-resolve-reference-"></a>MT2002:無法解析參考: *

<a name="MT2003" />

### <a name="mt2003-option--will-be-ignored-since-linking-is-disabled"></a>MT2003:選項 ' *' 將被忽略，因為連結已停用

<a name="MT2004" />

### <a name="mt2004-extra-linker-definitions-file--could-not-be-located"></a>MT2004:額外的連結器定義檔 ' *' 找不到。

<a name="MT2005" />

### <a name="mt2005-definitions-from--could-not-be-parsed"></a>MT2005:定義從 ' *' 無法剖析。

<a name="MT2006" />

### <a name="mt2006-can-not-load-mscorlibdll-from--please-reinstall-xamarinios"></a>MT2006:無法載入 mscorlib.dll 與: *。 請重新安裝 Xamarin.iOS。

這通常表示會有與您的 Xamarin.iOS 安裝問題。 請嘗試重新安裝 Xamarin.iOS。

<!--- 2007 used by mmp -->
<!--- 2009 used by mmp -->

<a name="MT2010" />

### <a name="mt2010-unknown-httpmessagehandler--valid-values-are-httpclienthandler-default-cfnetworkhandler-or-nsurlsessionhandler"></a>MT2010:未知的 HttpMessageHandler `*`。 有效值為 HttpClientHandler （預設值）、 CFNetworkHandler 或 NSUrlSessionHandler

<a name="MT2011" />

### <a name="mt2011-unknown-tlsprovider---valid-values-are-default-or-appletls"></a>MT2011:未知的 TlsProvider `*`。  有效值為 default 或 appletls。

若要指定的值`tls-provider=`不是有效的 TLS （傳輸層安全性） 提供者。

`default`和`appletls`是唯一有效的值，同時兩者皆表示相同的選項，也就是提供 SSL/TLS 支援使用原生 Apple TLS API。

<!--- 2012 used by mmp -->
<!--- 2013 used by mmp -->
<!--- 2014 used by mmp -->

<a name="MT2015" />

### <a name="mt2015-invalid-httpmessagehandler--for-watchos-the-only-valid-value-is-nsurlsessionhandler"></a>MT2015:無效的 HttpMessageHandler`*`進行 watchOS。 唯一有效的值是 NSUrlSessionHandler。

這是因為專案檔指定了無效的 HttpMessageHandler，就會發生警告。

產生預設情況下我們的預覽工具的舊版專案檔中無效的值。

若要修正這個警告，在文字編輯器中，開啟專案檔案，並從 XML 移除所有的 HttpMessageHandler 節點。

<a name="MT2016" />

### <a name="mt2016-invalid-tlsprovider-legacy-option-the-only-valid-value-appletls-will-be-used"></a>MT2016:無效的 TlsProvider`legacy`選項。 唯一有效的值`appletls`將使用。

`legacy`提供者，也就是完全受控的 SSLv3 / TLSv1 唯一的提供者，再也不會隨附 Xamarin.iOS。 使用這個舊的提供者，現在使用較新建置的專案`appletls`其中一個。

若要修正這個警告，請在文字編輯器中，開啟專案檔案，並移除所有 'MtouchTlsProvider' 的 xml 節點。

<a name="MT2017" />

### <a name="mt2017-could-not-process-xml-description"></a>MT2017:無法處理 XML 描述。

這表示在沒有錯誤[自訂 XML 連結器組態檔](https://developer.xamarin.com/guides/cross-platform/advanced/custom_linking/)提供時，請檢閱您的檔案。

<a name="MT2018" />

### <a name="mt2018-the-assembly--is-referenced-from-two-different-locations--and-"></a>MT2018:組件 '\*' 所參考的兩個不同位置: '\*' 和 ' *'。

從多個位置中的錯誤訊息提到組件會載入的。 請務必一律使用相同版本的組件。

<a name="MT2019" />

### <a name="mt2019-can-not-load-the-root-assembly-"></a>MT2019:無法載入根組件 ' *'

無法載入根組件。 請確認該路徑中的錯誤訊息參考到現有的檔案，而且它是有效的.NET 組件。

<a name="MT202x" />

### <a name="mt202x-binding-optimizer-failed-processing-"></a>MT202x:繫結最佳化工具無法處理`...`。

發生未預期發生時嘗試最佳化產生的繫結程式碼。 造成這個問題的項目出現在錯誤訊息。 若要修正此問題，名為組件 （或包含的型別或方法） 需要提供[bug 報表](http://bugzilla.xamarin.com)以及完成的組建記錄檔與啟用的詳細資訊 (亦即`-v -v -v -v`在**其他 mtouch引數**)。

最後一位數`x`會：
* `0` 組件名稱;
* `1` 型別名稱;
* `3` 方法名稱;

<a name="MT2030" />

### <a name="mt2030-remove-user-resources-failed-processing-"></a>MT2030:移除使用者的資源無法處理`...`。

發生未預期發生時嘗試移除使用者的資源。 造成這個問題的組件是名為錯誤訊息中。 若要修正此問題的組件必須在中提供[bug 報表](http://bugzilla.xamarin.com)以及完成的組建記錄檔與啟用的詳細資訊 (亦即`-v -v -v -v`中**其他 mtouch 引數**)。

使用者資源是 （如資源） 必須解壓縮，在建置時，若要建立應用程式套件組合的組件內所包含的檔案。 包括：

* `__monotouch_content_*` 和`__monotouch_pages_*`資源; 以及
* 原生程式庫內嵌在繫結的組件;

<a name="MT2040" />

### <a name="mt2040-default-httpmessagehandler-setter-failed-processing-"></a>MT2040:預設 HttpMessageHandler setter 無法處理`...`。

嘗試將設為預設值時，發生未預期發生`HttpMessageHandler`應用程式。 請提出[bug 報表](http://bugzilla.xamarin.com)以及完成的組建記錄檔與啟用的詳細資訊 (亦即`-v -v -v -v`中**其他 mtouch 引數**)。

<a name="MT2050" />

### <a name="mt2050-code-remover-failed-processing-"></a>MT2050:程式碼 Remover 無法處理`...`。

發生未預期發生時嘗試移除與應用程式傳送的 BCL 中的程式碼。 請提出[bug 報表](http://bugzilla.xamarin.com)以及完成的組建記錄檔與啟用的詳細資訊 (亦即`-v -v -v -v`中**其他 mtouch 引數**)。

<a name="MT2060" />

### <a name="mt2060-sealer-failed-processing-"></a>MT2060:密封程式無法處理`...`。

發生未預期發生或 devirtualizing 某些方法時嘗試密封類型或方法 （最後一個）。 造成這個問題的組件是名為錯誤訊息中。 若要修正此問題的組件必須在中提供[bug 報表](http://bugzilla.xamarin.com)以及完成的組建記錄檔與啟用的詳細資訊 (亦即`-v -v -v -v`中**其他 mtouch 引數**)。

<a name="MT2070" />

### <a name="mt2070-metadata-reducer-failed-processing-"></a>MT2070:中繼資料的歸納器無法處理`...`。

發生未預期發生時嘗試減少從應用程式的中繼資料。 造成這個問題的組件是名為錯誤訊息中。 若要修正此問題的組件必須在中提供[bug 報表](http://bugzilla.xamarin.com)以及完成的組建記錄檔與啟用的詳細資訊 (亦即`-v -v -v -v`中**其他 mtouch 引數**)。

<a name="MT2080" />

### <a name="mt2080-marknsobjects-failed-processing-"></a>MT2080:處理失敗 MarkNSObjects `...`。

嘗試將標記時，發生未預期發生`NSObject`從應用程式的子類別。 造成這個問題的組件是名為錯誤訊息中。 若要修正此問題的組件必須在中提供[bug 報表](http://bugzilla.xamarin.com)以及完成的組建記錄檔與啟用的詳細資訊 (亦即`-v -v -v -v`中**其他 mtouch 引數**)。

<a name="MT2090" />

### <a name="mt2090-inliner-failed-processing-"></a>MT2090:內嵌者無法處理`...`。

發生未預期發生時嘗試從應用程式的內嵌程式碼。 造成這個問題的組件是名為錯誤訊息中。 若要修正此問題的組件必須在中提供[bug 報表](https://bugzilla.xamarin.com)以及完成的組建記錄檔與啟用的詳細資訊 (亦即`-v -v -v -v`中**其他 mtouch 引數**)。

<!-- MT21xx: more linker errors -->

<!--- 2100 used by mmp -->

<a name="MT2100" />

### <a name="mt2100-smart-enum-conversion-preserver-failed-processing-"></a>MT2100:智慧型列舉轉換守護者無法處理`...`。

發生未預期發生時嘗試轉換方法，從應用程式的智慧列舉標記。 造成這個問題的組件是名為錯誤訊息中。 若要修正此問題的組件必須在中提供[bug 報表](https://bugzilla.xamarin.com)以及完成的組建記錄檔與啟用的詳細資訊 (亦即`-v -v -v -v`中**其他 mtouch 引數**)。

<a name="MT2101" />

### <a name="mt2101-cant-resolve-the-reference--referenced-from-the-method--in-"></a>MT2101:無法解析參考 '\*'，參考從方法'\*' 中 ' *'。

處理錯誤訊息中所述的方法時遇到無效的組件參考。

造成這個問題的組件是名為錯誤訊息中。 若要修正此問題的組件必須在中提供[bug 報表](https://bugzilla.xamarin.com)以及完成的組建記錄檔與啟用的詳細資訊 (亦即`-v -v -v -v`中**其他 mtouch 引數**)。

<a name="MT2102" />

### <a name="mt2102-error-processing-the-method--in-the-assembly--"></a>MT2102:錯誤處理方法 '\*'中的組件'\*': *

發生未預期發生時，嘗試將錯誤訊息中所述的方法。

造成這個問題的組件是名為錯誤訊息中。 若要修正此問題的組件必須在中提供[bug 報表](https://bugzilla.xamarin.com)以及完成的組建記錄檔與啟用的詳細資訊 (亦即`-v -v -v -v`中**其他 mtouch 引數**)。

<a name="MT2103" />

### <a name="mt2103-error-processing-assembly--"></a>MT2103:處理組件時發生錯誤 '\*': *

處理組件時，就會發生意外的錯誤。

造成這個問題的組件是名為錯誤訊息中。 若要修正此問題的組件必須在中提供[bug 報表](https://bugzilla.xamarin.com)以及完成的組建記錄檔與啟用的詳細資訊 (亦即`-v -v -v -v`中**其他 mtouch 引數**)。

<a name="MT2104" />

### <a name="mm2104-unable-to-link-assembly-0-as-it-is-mixed-mode"></a>MM2104:無法將連結的組件 '{0}' 因為它是混合模式。

連結器可以處理混合模式組件。

請參閱 https://msdn.microsoft.com/library/x0w2664k.aspx 如需有關混合模式組件。

## <a name="mt3xxx-aot-error-messages"></a>MT3xxx:AOT 錯誤訊息

<!--
 MT3xxx AOT
  MT30xx AOT (general) errors
  -->

<a name="MT3001" />

### <a name="mt3001-could-not-aot-the-assembly-"></a>MT3001:無法 AOT 的組件 ' *'

這通常表示 AOT 編譯器中的 bug。 請將 bug 歸檔[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)可以用來重現錯誤的專案。

有時候很可能可解決此停用累加建置專案的 iOS 組建選項中 (但它仍是一個 bug，因此請回報還是)。

<a name="MT3002" />

### <a name="mt3002-aot-restriction-method--must-be-static-since-it-is-decorated-with-monopinvokecallback-see-httpsdeveloperxamarincomguidesiosadvancedtopicslimitationsreversecallbackshttpsdeveloperxamarincomguidesiosadvancedtopicslimitationsreversecallbacks"></a>MT3002:AOT 限制：方法 ' *' 必須是靜態，因為它附有 [MonoPInvokeCallback]。 請參閱 [https://developer.xamarin.com/guides/ios/advanced_topics/limitations/#Reverse_Callbacks](https://developer.xamarin.com/guides/ios/advanced_topics/limitations/#Reverse_Callbacks)

此錯誤訊息是來自 AOT 編譯器。

<a name="MT3003" />

### <a name="mt3003-conflicting---debug-and---llvm-options-soft-debugging-is-disabled"></a>MT3003:衝突-偵錯和-llvm 選項。 軟偵錯已停用。

偵錯時，不支援在啟用 LLVM。 如果您需要偵錯應用程式，請先停用 LLVM。

<a name="MT3004" />

### <a name="mt3004-could-not-aot-the-assembly--because-it-doesnt-exist"></a>MT3004:無法 AOT 的組件 ' *' 因為不存在。

<a name="MT3005" />

### <a name="mt3005-the-dependency--of-the-assembly--was-not-found-please-review-the-projects-references"></a>MT3005:相依性 '\*'的組件'\*' 找不到。 請檢閱專案的參考。

此外，這通常會發生時的組件參考另一個版本的平台組件 （通常是.NET 4 版本的 mscorlib.dll）。

這不支援，可能無法建置，或要正確執行 （組件可能會使用 API，從.NET 4 版本的 Xamarin.iOS 版本沒有的 mscorlib.dll）。

<a name="MT3006" />

### <a name="mt3006-could-not-compute-a-complete-dependency-map-for-the-project-this-will-result-in-slower-build-times-because-xamarinios-cant-properly-detect-what-needs-to-be-rebuilt-and-what-does-not-need-to-be-rebuilt-please-review-previous-warnings-for-more-details"></a>MT3006:無法計算專案的完整相依性對應。 這將會導致較慢的建置時間，因為 Xamarin.iOS 無法正確偵測到什麼需要重建 （和什麼不需要重建）。 請之前的警告，如需詳細資訊，檢閱。

 建置或正確執行 （組件可能會使用 API，從.NET 4 版本的 Xamarin.iOS 版本沒有的 mscorlib.dll）。

<a name="MT3007" />

### <a name="mt3007-debug-info-files-mdb-will-not-be-loaded-when-llvm-is-enabled"></a>MT3007:啟用 llvm 時，將不會載入偵錯資訊檔案 (*.mdb)。

<a name="MT3008" />

### <a name="mt3008-bitcode-support-requires-the-use-of-the-llvm-aot-backend---llvm"></a>MT3008:Bitcode 支援需要使用 LLVM AOT 後端 (-llvm)

Bitcode 支援需要使用 LLVM AOT 後端 (-llvm)。

停用 Bitcode 支援或啟用 LLVM。

<!--- 3009 used by mmp -->
<!--- 3010 used by mmp -->

## <a name="mt4xxx-code-generation-error-messages"></a>MT4xxx:程式碼產生錯誤訊息

### <a name="mt40xx-main"></a>MT40xx:主要

<!--
 MT4xxx code generation
  MT40xx main.m
  -->

<a name="MT4001" />

### <a name="mt4001-the-main-template-could-not-be-expanded-to-"></a>MT4001:主要的範本無法展開至`*`。

產生 main.m 時，就會發生錯誤。 請將 bug 歸檔在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT4002" />

### <a name="mt4002-failed-to-compile-the-generated-code-for-pinvoke-methods-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4002:無法編譯產生的程式碼，P/Invoke 方法。 請提出錯誤報告在 http://bugzilla.xamarin.com

無法編譯產生的程式碼，P/Invoke 方法。 請提出錯誤報告在[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

### <a name="mt41xx-registrar"></a>MT41xx:登錄器

<!--
  MT41xx registrar.m
  -->

<a name="MT4101" />

### <a name="mt4101-the-registrar-cannot-build-a-signature-for-type-"></a>MT4101:在註冊機構的版本無法建立類型的簽章`*`。

在執行階段不知道如何封送處理從 OBJECTIVE-C 的匯出 API 中找不到型別

如果您認為 Xamarin.iOS 應該支援的類型有問題，請提出的增強功能要求[ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com)。

<a name="MT4102" />

### <a name="mt4102-the-registrar-found-an-invalid-type--in-signature-for-method--use--instead"></a>MT4102:在註冊機構找到無效的型別`*`方法的簽章中`*`。 請改用 `*`。

這目前僅發生一種類型：System.DateTime。 請改用 Objective C 對等項目 (NSDate)。

<a name="MT4103" />

### <a name="mt4103-the-registrar-found-an-invalid-type--in-signature-for-method--the-type-implements-inativeobject-but-does-not-have-a-constructor-that-takes-two-intptr-bool-arguments"></a>MT4103:在註冊機構找到無效的型別`*`方法的簽章中`*`:型別會實作 INativeObject，但並沒有會採用兩個建構函式 (IntPtr，bool) 引數

發生這種情況時註冊機構會遇到中所述的特性的簽章的類型。 在註冊機構可能需要建立新的執行個體的型別，而且在此情況下需要 (IntPtr，bool) 的建構函式簽章-的第一個引數 (IntPtr) 指定的受管理的控制代碼，如果呼叫端傳遞原生的擁有權，第二個（如果此值為 false 將會在物件上呼叫 '保留'） 處理。

<a name="MT4104" />

### <a name="mt4104-the-registrar-cannot-marshal-the-return-value-for-type--in-signature-for-method-"></a>MT4104:在註冊機構無法封送處理類型的傳回值`*`方法的簽章中`*`。

在執行階段不知道如何封送處理從 OBJECTIVE-C 的匯出 API 中找不到型別

如果您認為 Xamarin.iOS 應該支援的類型有問題，請提出的增強功能要求[ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com)。

<a name="MT4105" />

### <a name="mt4105-the-registrar-cannot-marshal-the-parameter-of-type--in-signature-for-method-"></a>MT4105:在註冊機構無法封送處理的型別參數`*`方法的簽章中`*`。

如果您認為 Xamarin.iOS 應該支援的類型有問題，請提出的增強功能要求[ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com)。

<a name="MT4106" />

### <a name="mt4106-the-registrar-cannot-marshal-the-return-value-for-structure--in-signature-for-method-"></a>MT4106:在註冊機構無法封送處理結構的傳回值`*`方法的簽章中`*`。

在執行階段不知道如何封送處理從 OBJECTIVE-C 的匯出 API 中找不到型別

如果您認為 Xamarin.iOS 應該支援的類型有問題，請提出的增強功能要求[ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com)。

<a name="MT4107" />

### <a name="mt4107-the-registrar-cannot-marshal-the-parameter-of-type--in-signature-for-method-"></a>MT4107:在註冊機構無法封送處理的型別參數`*`方法的簽章中`+`。

在執行階段不知道如何封送處理從 OBJECTIVE-C 的匯出 API 中找不到型別

如果您認為 Xamarin.iOS 應該支援的類型有問題，請提出的增強功能要求[ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com)。

<a name="MT4108" />

### <a name="mt4108-the-registrar-cannot-get-the-objectivec-type-for-managed-type-"></a>MT4108:在註冊機構無法取得 managed 類型的 ObjectiveC 類型`*`。

在執行階段不知道如何封送處理從 OBJECTIVE-C 的匯出 API 中找不到型別

如果您認為 Xamarin.iOS 應該支援的類型有問題，請提出的增強功能要求[ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com)。

<a name="MT4109" />

### <a name="mt4109-failed-to-compile-the-generated-registrar-code-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4109:無法編譯產生的註冊機構的程式碼。 請提出錯誤報告在 http://bugzilla.xamarin.com

無法編譯產生的程式碼的註冊機構。 組建記錄檔會包含原生編譯器，解釋為何不進行編譯的程式碼的輸出。

這一律是 Xamarin.iOS; 中的 bug請提出錯誤報告在[ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com)與您的專案或測試案例。

<a name="MT4110" />

### <a name="mt4110-the-registrar-cannot-marshal-the-out-parameter-of-type--in-signature-for-method-"></a>MT4110:在註冊機構無法封送處理類型的 out 參數`*`方法的簽章中`*`。

<a name="MT4111" />

### <a name="mt4111-the-registrar-cannot-build-a-signature-for-type--in-method-"></a>MT4111:在註冊機構的版本無法建立類型的簽章`*`方法中`*`。

<a name="MT4112" />

### <a name="mt4112-the-registrar-found-an-invalid-type--registering-generic-types-with-objective-c-is-not-supported-and-may-lead-to-random-behavior-andor-crashes-for-backwards-compatibility-with-older-versions-of-xamarinios-it-is-possible-to-ignore-this-error-by-passing---unsupported--enable-generics-in-registrar-as-an-additional-mtouch-argument-in-the-projects-ios-build-options-page-see-developerxamarincomguidesiosadvancedtopicsregistrarhttpsdeveloperxamarincomguidesiosadvancedtopicsregistrar-for-more-information"></a>MT4112:在註冊機構找到無效的型別`*`。 向 OBJECTIVE-C 中的泛型型別不支援，且可能會導致隨機的行為和/或損毀 (用於回溯相容性與舊版 Xamarin.iOS 就可以忽略此錯誤，藉由傳遞`--unsupported--enable-generics-in-registrar`為其他 mtouch專案的 iOS 組建選項 頁面中的引數。 請參閱[developer.xamarin.com/guides/ios/advanced_topics/registrar](https://developer.xamarin.com/guides/ios/advanced_topics/registrar)如需詳細資訊)。

<a name="MT4113" />

### <a name="mt4113-the-registrar-found-a-generic-method--exporting-generic-methods-is-not-supported-and-will-lead-to-random-behavior-andor-crashes"></a>MT4113:在註冊機構找到泛型的方法: '\*。\*'。 匯出泛型方法不支援，並會導致隨機的行為和/或損毀。

<a name="MT4114" />

### <a name="mt4114-unexpected-error-in-the-registrar-for-the-method----please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4114:未預期的錯誤中的註冊機構方法 '\*。\*'-請提出錯誤報告在 http://bugzilla.xamarin.com

<a name="MT4116" />

### <a name="mt4116-could-not-register-the-assembly--"></a>MT4116:無法註冊組件 ' *': *

<a name="MT4117" />

### <a name="mt4117-the-registrar-found-a-signature-mismatch-in-the-method----the-selector-indicates-the-method-takes--parameters-while-the-managed-method-has--parameters"></a>MT4117:註冊機構位於方法簽章不符合 '*。*'-選取器指出此方法會採用 * 參數，而受管理的方法有 * 參數。

<a name="MT4118" />

### <a name="mt4118-cannot-register-two-managed-types--and--with-the-same-native-name-"></a>MT4118:無法註冊兩個的 managed 的類型 ('\*'和'\*') 具有相同的原生名稱 ('* ')。

<a name="MT4119" />

### <a name="mt4119-could-not-register-the-selector--of-the-member--because-the-selector-is-already-registered-on-a-different-member"></a>MT4119:無法註冊選取器 '\*'的成員'\*。 *' 因為選取器已經登錄在不同的成員。

<a name="MT4120" />

### <a name="mt4120-the-registrar-found-an-unknown-field-type--in-field--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4120:在註冊機構找了未知的欄位類型 '\*'中欄位'\*。 *'。 請提出錯誤報告在 http://bugzilla.xamarin.com

此錯誤表示在 Xamarin.iOS 中的 bug。 請提出錯誤報告在[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT4121" />

### <a name="mt4121-cannot-use-gccg-to-compile-the-generated-code-from-the-static-registrar-when-using-the-accounts-framework-the-header-files-provided-by-apple-used-during-the-compilation-require-clang-either-use-clang---compilerclang-or-the-dynamic-registrar---registrardynamic"></a>MT4121:無法使用 GCC / G + + 編譯的靜態的註冊機構從產生的程式碼時使用帳戶架構 （在編譯期間使用的 Apple 提供的標頭檔需要 Clang）。 請使用 Clang (-編譯器： clang) 或動態的註冊機構 (-註冊機構： 動態)。

<a name="MT4122" />

### <a name="mt4122-cannot-use-the-clang-compiler-provided-in-the--sdk-to-compile-the-generated-code-from-the-static-registrar-when-non-ascii-type-names--are-present-in-the-application-either-use-gccg---compilergccg-the-dynamic-registrar---registrardynamic-or-a-newer-sdk"></a>MT4122:無法使用提供的 Clang 編譯器 *。* SDK 編譯產生的程式碼，從靜態的註冊機構時非 ASCII 類型名稱 ('* ') 會出現在應用程式。 請使用 GCC / G + + (-gcc 編譯器: | g + +)、 動態註冊機構 (-註冊機構： 動態) 或更新版本的 SDK。

<a name="MT4123" />

### <a name="mt4123-the-type-of-the-variadic-parameter-in-the-variadic-function--must-be-systemintptr"></a>MT4123:Variadic 函式中的 variadic 參數的型別 ' *' 必須是 System.IntPtr。

<a name="MT4124" />

### <a name="mt4124-invalid--found-on--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4124:無效 * 上找到 ' *'。 請提出錯誤報告在 http://bugzilla.xamarin.com

此錯誤表示在 Xamarin.iOS 中的 bug。 請提出錯誤報告在[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT4125" />

### <a name="mt4125-the-registrar-found-an-invalid-type--in-signature-for-method--the-interface-must-have-a-protocol-attribute-specifying-its-wrapper-type"></a>MT4125:在註冊機構找到無效的類型 '\*'中方法簽章'\*':介面必須要有通訊協定屬性，指定其包裝函式型別。

<a name="MT4126" />

### <a name="mt4126-cannot-register-two-managed-protocols--and--with-the-same-native-name-"></a>MT4126:無法註冊兩個受管理的通訊協定 ('\*'和'\*') 具有相同的原生名稱 ('* ')。

<a name="MT4127" />

### <a name="mt4127-cannot-register-more-than-one-interface-method-for-the-method--which-is-implementing-"></a>MT4127:無法註冊方法的多個介面方法 '\*' (這實作 '\*')。

<a name="MT4128" />

### <a name="mt4128-the-registrar-found-an-invalid-generic-parameter-type--in-the-method--the-generic-parameter-must-have-an-nsobject-constraint"></a>MT4128:在註冊機構找到無效的泛型參數類型 '\*'中方法'\*'。 泛型參數都必須有 'NSObject' 條件約束。

<a name="MT4129" />

### <a name="mt4129-the-registrar-found-an-invalid-generic-return-type--in-the-method--the-generic-return-type-must-have-an-nsobject-constraint"></a>MT4129:在註冊機構找到無效的泛型傳回類型 '\*'中方法'\*'。 泛型傳回類型必須有 'NSObject' 條件約束。

<a name="MT4130" />

### <a name="mt4130-the-registrar-cannot-export-static-methods-in-generic-classes-"></a>MT4130:在註冊機構無法匯出中的泛用類別的靜態方法 ('* ')。

<a name="MT4131" />

### <a name="mt4131-the-registrar-cannot-export-static-properties-in-generic-classes-"></a>MT4131:在註冊機構無法匯出泛型類別中的靜態屬性 ('\*。\*')。

<a name="MT4132" />

### <a name="mt4132-the-registrar-found-an-invalid-generic-return-type--in-the-property--the-return-type-must-have-an-nsobject-constraint"></a>MT4132:在註冊機構找到無效的泛型傳回類型 '\*'中屬性'\*'。 傳回的類型必須有 'NSObject' 條件約束。

<a name="MT4133" />

### <a name="mt4133-cannot-construct-an-instance-of-the-type--from-objective-c-because-the-type-is-generic-runtime-exception"></a>MT4133:無法建構類型的執行個體 ' *' 從 OBJECTIVE-C 因為型別是泛型。 [執行階段例外狀況]

<a name="MT4134" />

### <a name="mt4134-your-application-is-using-the--framework-which-isnt-included-in-the-ios-sdk-youre-using-to-build-your-app-this-framework-was-introduced-in-ios--while-youre-building-with-the-ios--sdk-please-select-a-newer-sdk-in-your-apps-ios-build-options"></a>MT4134:您的應用程式使用 ' *' framework，不會包含於 iOS SDK 您用來建置您的應用程式 (iOS 中引進了此架構 *，而您要建置使用 iOS * SDK。)請在您的應用程式 iOS 組建 選項，選取較新的 SDK。

<a name="MT4135" />

### <a name="mt4135-the-member--has-an-export-attribute-that-doesnt-specify-a-selector-a-selector-is-required"></a>MT4135:成員 '\*。\*' 有未指定選取器的匯出屬性。 需要選取器。

<a name="MT4136" />

### <a name="mt4136-the-registrar-cannot-marshal-the-parameter-type--of-the-parameter--in-the-method-"></a>MT4136:在註冊機構無法封送處理參數類型 '\*'的參數'\*'中方法'\*。 *'

<!-- MT4137 is unused -->

<a name="MT4138" />

### <a name="mt4138-the-registrar-cannot-marshal-the-property-type--of-the-property-"></a>MT4138:在註冊機構無法封送處理屬性類型 '\*'的屬性'\*。 *'。

<a name="MT4139" />

### <a name="mt4139-the-registrar-cannot-marshal-the-property-type--of-the-property--properties-with-the-connect-attribute-must-have-a-property-type-of-nsobject-or-a-subclass-of-nsobject"></a>MT4139:在註冊機構無法封送處理屬性類型 '\*'的屬性'\*。 *'。 NSObject 的屬性類型 （或 NSObject 的子類別），必須使用 [連線] 屬性的屬性。

<a name="MT4140" />

### <a name="mt4140-the-registrar-found-a-signature-mismatch-in-the-method----the-selector-indicates-the-variadic-method-takes--parameters-while-the-managed-method-has--parameters"></a>MT4140:註冊機構位於方法簽章不符合 '*。*'-選取器可讓您表示 variadic 方法會採用 * 參數，而受管理的方法有 * 參數。

<a name="MT4141" />

### <a name="mt4141-cannot-register-the-selector--on-the-member--because-xamarinios-implicitly-registers-this-selector"></a>MT4141:無法註冊選取器 '\*'上成員'\*' 因為 Xamarin.iOS 隱含註冊這個選取器。

這發生在子類別化 framework 型別，並嘗試實作 '保留'，'版本' 或 'dealloc' 方法：

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

它是架構的不過可覆寫這些方法，如果類別不是架構的第一個子類別類型：

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

<a name="MT4142" />

### <a name="mt4142-failed-to-register-the-type-"></a>MT4142:無法註冊類型 ' *'。

<a name="MT4143" />

### <a name="mt4143-the-objectivec-class--could-not-be-registered-it-does-not-seem-to-derive-from-any-known-objectivec-class-including-nsobject"></a>MT4143:ObjectiveC 類別 ' *' 無法註冊，它似乎並沒有衍生自任何已知的 ObjectiveC 類別 （包括 NSObject）。

<a name="MT4144" />

### <a name="mt4144-cannot-register-the-method--since-it-does-not-have-an-associated-trampoline-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4144:無法將方法註冊 ' *' 因為沒有相關聯的 trampoline。 請提出錯誤報告在 http://bugzilla.xamarin.com。

這表示在 Xamarin.iOS 中的 bug。 請將 bug 歸檔在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT4145" />

### <a name="mt4145-invalid-enum--enums-with-the-native-attribute-must-have-a-underlying-enum-type-of-either-long-or-ulong"></a>MT4145:無效的列舉 ' *': 具有 [原生] 屬性的列舉必須有基礎的列舉型別 'long' 或 'ulong'。

<a name="MT4146" />

### <a name="mt4146-the-name-parameter-of-the-registrar-attribute-on-the-class---contains-an-invalid-character--"></a>MT4146:註冊機構上的屬性類別的 Name 參數\*'('\*') 包含無效的字元:'\*' (\*)。

Objectice C 類別的名稱不能包含空白字元，這表示`Register`相對應的 managed 類別上的屬性不能有`Name`參數不能是包含空白字元。

請確認`Register`上錯誤訊息中所述之 managed 類別的屬性不包含任何空白字元。

<a name="MT4147" />

### <a name="mt4147-detected-a-protocol-inheriting-from-the-jsexport-protocol-while-using-the-dynamic-registrar-it-is-not-possible-to-export-protocols-to-javascriptcore-dynamically-the-static-registrar-must-be-used-add---registrarstatic-to-the-additional-mtouch-arguments-in-the-projects-ios-build-options-to-select-the-static-registrar"></a>MT4147:偵測到繼承 JSExport 通訊協定，同時使用動態的註冊機構的通訊協定。 不可能將通訊協定動態; 匯出至 JavaScriptCore必須使用靜態的註冊機構 (新增 '-其他 mtouch 引數，在專案的 iOS 建置選項以選取靜態註冊機構的註冊機構： 靜態)。

<a name="MT4148" />

### <a name="mt4148-the-registrar-found-a-generic-protocol--exporting-generic-protocols-is-not-supported"></a>MT4148:在註冊機構找泛用的通訊協定: ' *'。 不支援匯出一般通訊協定。

<a name="MT4149" />

### <a name="mt4149-cannot-register-the-method--because-the-type-of-the-first-parameter--does-not-match-the-category-type-"></a>MT4149:無法註冊方法 '\*。\*' 因為第一個參數的型別 ('\*') 不符合類別類型 ('\*')。

<a name="MT4150" />

### <a name="mt4150-cannot-register-the-type--because-the-type-property--in-its-category-attribute-does-not-inherit-from-nsobject"></a>MT4150:無法註冊類型 '\*' 因為型別屬性 ('\*') 在其類別屬性不是繼承自 NSObject。

<a name="MT4151" />

### <a name="mt4151-cannot-register-the-type--because-the-type-property-in-its-category-attribute-isnt-set"></a>MT4151:無法註冊類型 ' *' 因為其類別屬性中的 [類型] 屬性未設定。

<a name="MT4152" />

### <a name="mt4152-cannot-register-the-type--as-a-category-because-it-implements-inativeobject-or-subclasses-nsobject"></a>MT4152:無法註冊類型 ' *' 做為類別目錄因為它會實作 INativeObject 或 NSObject 的子類別。

<a name="MT4153" />

### <a name="mt4153-cannot-register-the-type--as-a-category-because-its-generic"></a>MT4153:無法註冊類型 '\*' 做為類別目錄因為它是泛型。

<a name="MT4154" />

### <a name="mt4154-cannot-register-the-method--as-a-category-method-because-its-generic"></a>MT4154:無法將方法註冊 '\*' 做為類別方法因為它是泛型。

<a name="MT4155" />

### <a name="mt4155-cannot-register-the-method--with-the-selector--as-a-category-method-on--because-the-objective-c-already-has-an-implementation-for-this-selector"></a>MT4155:無法註冊方法 '\*'與選取器'\*' 上的分類方法為 ' *' 因為 Objective C 已經有這個選取器的實作。

<a name="MT4156" />

### <a name="mt4156-cannot-register-two-categories--and--with-the-same-native-name-"></a>MT4156:無法註冊兩個類別 ('\*'和'\*') 具有相同的原生名稱 ('* ')。

<a name="MT4157" />

### <a name="mt4157-cannot-register-the-category-method--because-at-least-one-parameter-is-required-and-its-type-must-match-the-category-type-"></a>MT4157:無法註冊類別方法 '\*' 因為至少一個參數是必要的 (且其類型必須符合類別類型 '\*')

<a name="MT4158" />

### <a name="mt4158-cannot-register-the-constructor--in-the-category--because-constructors-in-categories-are-not-supported"></a>MT4158:無法註冊建構函式 * 類別 * 因為不支援類別的建構函式。

<a name="MT4159" />

### <a name="mt4159-cannot-register-the-method--as-a-category-method-because-category-methods-must-be-static"></a>MT4159:無法將方法註冊 ' *' 做為類別方法因為類別方法必須是靜態。

<a name="MT4160" />

### <a name="mt4160-invalid-character---found-in-selector--for-"></a>MT4160:無效的字元 '\*' (\*) 中選取器找到 '\*'for'\*'。

<a name="MT4161" />

### <a name="mt4161-the-registrar-found-an-unsupported-structure--all-fields-in-a-structure-must-also-be-structures-field--with-type-2-is-not-a-structure"></a>MT4161:在註冊機構找到不支援的結構 '\*':在結構中的所有欄位也必須都是結構 (欄位 '\*'與 type'{2}' 不是結構)。

在註冊機構會找到不支援欄位的結構。

結構，其會公開以 OBJECTIVE-C 中的所有欄位也必須都是結構 （不是類別）。

<a name="MT4162" />

### <a name="mt4162-the-type--used-as--2-is-not-available-in---it-was-introduced-in---please-build-with-a-newer--sdk-usually-done-by-using-the-most-recent-version-of-xcode"></a>MT4162:類型 '\*' (做為 * {2}) 不適用於 * * (首見於 * *)\*請使用較新建置 * SDK （通常是使用最新版的 Xcode 來完成。

在註冊機構找到的類型不包含在目前的 SDK。

請先升級 Xcode。

<a name="MT4163" />

### <a name="mt4163-internal-error-in-the-registrar--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4163:註冊機構 （*） 發生內部錯誤。 請提出錯誤報告在 http://bugzilla.xamarin.com

此錯誤表示在 Xamarin.iOS 中的 bug。 請提出錯誤報告在[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT4164" />

### <a name="mt4164-cannot-export-the-property--because-its-selector--is-an-objective-c-keyword-please-use-a-different-name"></a>MT4164:無法匯出的屬性 '\*' 因為其選取器 '\*' Objective C 關鍵字。 請使用不同的名稱。

選取器所討論的屬性不是有效的 Objective C 識別項。

請為選取器，使用有效的 Objective C 識別項。

<a name="MT4165" />

### <a name="mt4165-the-registrar-couldnt-find-the-type-systemvoid-in-any-of-the-referenced-assemblies"></a>MT4165:在註冊機構任何參考的組件中找不到類型 'System.Void'。

此錯誤最可能表示在 Xamarin.iOS 中的 bug。 請提出錯誤報告在[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT4166" />

### <a name="mt4166-cannot-register-the-method--because-the-signature-contains-a-type--that-isnt-a-reference-type"></a>MT4166:無法註冊方法 '\*' 因為簽章包含的類型 (\*) 不是參考型別。

這通常表示 Xamarin.iOS; 中的 bug請將 bug 歸檔在[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT4167" />

### <a name="mt4167-cannot-register-the-method--because-the-signature-contains-a-generic-type--with-a-generic-argument-type-that-isnt-an-nsobject-subclass-"></a>MT4167:無法註冊方法 '\*' 因為簽章包含泛型型別 (\*) 不是 NSObject 子類別 （*） 的泛型引數類型。

這通常表示 Xamarin.iOS; 中的 bug請將 bug 歸檔在[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT4168" />

### <a name="mt4168-cannot-register-the-type-managedname-because-its-objective-c-name-exportedname-is-an-objective-c-keyword-please-use-a-different-name"></a>MT4168:無法註冊類型 '{受控\_名稱}' 因為其 Objective C 的名稱' {匯出\_名稱}' 的 Objective C 關鍵字。 請使用不同的名稱。

討論中類型的 Objective C 名稱不是有效的 Objective C 識別項。

請使用有效的 Objective C 識別項。

<a name="MT4169" />

### <a name="mt4169-failed-to-generate-a-pinvoke-wrapper-for-method-message"></a>MT4169:無法產生的 P/Invoke 包裝函式 {方法}: {message}

Xamarin.iOS 無法產生 P/Invoke 包裝函式的函式所述。
請檢查根本原因的報告的錯誤訊息。

<a name="MT4170" />

### <a name="mt4170-the-registrar-cant-convert-from-managed-type-to-native-type-for-the-return-value-in-the-method-method"></a>MT4170:在 {方法} 方法的傳回值的註冊機構無法從 '{managed type}' 轉換為 '{原生類型}'。

請參閱錯誤的描述<a href="#MT4172">MT4172</a>。

<a name="MT4171" />

### <a name="mt4171-the-bindas-attribute-on-the-member-member-is-invalid-the-bindas-type-type-is-different-from-the-property-type-type"></a>MT4171:在成員 {成員} BindAs 屬性無效： BindAs 型別 {type} 是 {type} 屬性類型不同。

請確定 BindAs 屬性中的型別符合它附加到成員的型別。

<a name="MT4172" />

### <a name="mt4172-the-registrar-cant-convert-from-native-type-to-managed-type-for-the-parameter-parameter-name-in-the-method-method"></a>MT4172:方法 {方法} 中的參數 '{parameter name}' 的註冊機構無法從 '{原生 type}' 轉換為 '{managed type}'。

在註冊機構不支援上述類型之間轉換。

如果有問題的 API 由 Xamarin.iOS; 提供，這會是 Xamarin.iOS 中的 bug請將 bug 歸檔在[ http://bugzilla.xamarin.com ] [ 1]。

如果您遇到這個開發原生程式庫的繫結專案時，我們開啟新增支援新類型的組合。 如果發生這種情況，請提出要求的增強功能 ([http://bugzilla.xamarin.com][2]) 與測試案例，我們將會評估它。

[1]: https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS
[2]: https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS&component=General&bug_severity=enhancement

## <a name="mt5xxx-gcc-and-toolchain-error-messages"></a>MT5xxx:GCC 和工具鏈的錯誤訊息

### <a name="mt51xx-compilation"></a>MT51xx:編譯

<!--
 MT5xxx GCC and toolchain
  MT51xx compilation
  -->

<a name="MT5101" />

### <a name="mt5101-missing--compiler-please-install-xcode-command-line-tools-component"></a>MT5101:遺漏 ' *' 編譯器。 請安裝 Xcode '命令列工具 」 元件

<a name="MT5102" />

### <a name="mt5102-failed-to-assemble-the-file--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5102:無法組合檔案 ' *'。 請提出錯誤報告在 http://bugzilla.xamarin.com

<a name="MT5103" />

### <a name="mt5103-failed-to-compile-the-file--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5103:無法編譯檔案 ' *'。 請提出錯誤報告在 http://bugzilla.xamarin.com

<a name="MT5104" />

### <a name="mt5104-could-not-find-neither-the--nor-the--compiler-please-install-xcode-command-line-tools-component"></a>MT5104:找不到 '\*'和'\*' 編譯器。 請安裝 Xcode '命令列工具 」 元件

<!-- 5105 is used by mmp -->

<a name="MT5106" />

### <a name="mt5106-could-not-compile-the-files--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5106:可能不會編譯檔案 ' *'。 請提出錯誤報告在 http://bugzilla.xamarin.com

這通常表示 Xamarin.iOS; 中的 bug請將 bug 歸檔在[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

### <a name="mt52xx-linking"></a>MT52xx:連結

<!--
  MT52xx linking
  -->

<a name="MT5201" />

### <a name="mt5201-native-linking-failed-please-review-the-build-log-and-the-user-flags-provided-to-gcc-"></a>MT5201:原生連結失敗。 請檢閱建置記錄檔和提供給 gcc 使用者旗標: *

<a name="MT5202" />

### <a name="mt5202-native-linking-failed-please-review-the-build-log"></a>MT5202:原生連結失敗。 請檢閱建置記錄檔。

<a name="MT5203" />

### <a name="mt5203-native-linking-warning-"></a>MT5203:原生連結警告: *

<!--- 5204-5208 are not used -->

<a name="MT5209" />

### <a name="mt5209-native-linking-error-"></a>MT5209:原生連結錯誤: *

<a name="MT5210" />

### <a name="mt5210-native-linking-failed-undefined-symbol--please-verify-that-all-the-necessary-frameworks-have-been-referenced-and-native-libraries-are-properly-linked-in"></a>MT5210:原生連結失敗，未定義的符號: *。 請確認已參考的所有必要的架構，並在會正確地連結到原生程式庫。

會發生這種情況是當原生連結器找不到參考位置的符號。 有幾個原因，這可能是：

* 第三方繫結需要一個架構，但繫結未指定在其`[LinkWith]`屬性。 解決方案：
  - 如果您的第三方繫結、 作者或能夠存取其來源，修改繫結的`[LinkWith]`屬性來包含它所需要的架構：

            [LinkWith ("mylib.a", Frameworks = "SystemConfiguration")]

  - 如果您無法修改第三方繫結，您可以手動連結與必要的 framework 藉由傳遞`-gcc_flags '-framework SystemFramework'`至`mtouch`（這是藉由修改專案的 iOS 組建選項 頁面中的其他 mtouch 引數。 請記住，這必須針對每個專案組態）。
* 在某些情況下，受管理的繫結組成多個原生程式庫，以及所有必須包含在繫結。 可以有一個以上的原生程式庫，每個繫結專案，因此解決方案是只要將所有的必要原生程式庫新增至繫結專案。</li>
* 受管理的繫結是指不存在於原生程式庫中的原生符號。
    當繫結已存在一些時間，而且原生程式碼已修改在這段時間，讓特定的原生類別已移除或重新命名，而尚未更新繫結時，就會發生這種情形。
* P/Invoke 指的是原生的符號不存在。 從 Xamarin.iOS 7.4 <a href="#MT5214">MT5214</a>將報告錯誤，在此情況下 （如需詳細資訊，請參閱 MT5214）。
* 第三方繫結 / 使用 c + +，建置程式庫，但繫結不會指定在其`[LinkWith]`屬性。 這通常是很容易就能辨識，因為符號是損壞的 c + + 符號 (其中一個常見範例是`__ZNKSt9exception4whatEv`)。
  - 如果您的第三方繫結、 作者或能夠存取其來源，修改的繫結`[LinkWith]`屬性，設定`IsCxx`旗標：

            [LinkWith ("mylib.a", IsCxx = true)]

  - 如果您無法修改的第三方繫結，或您手動連結與協力廠商程式庫，您可以藉由傳遞設定的對等的旗標<code>-cxx</code>至 mtouch （這是藉由修改 [其他 mtouch 引數，在專案的 iOS 組建] 選項頁面. 請記住，這必須針對每個專案組態）。

<a name="MT5211" />

### <a name="mt5211-native-linking-failed-undefined-objective-c-class--the-symbol--could-not-be-found-in-any-of-the-libraries-or-frameworks-linked-with-your-application"></a>MT5211:原生連結失敗，未定義的 Objective C 類別： \*。 符號 '\*' 找不到的任何程式庫或架構與您的應用程式連結。

會發生這種情況是當原生連結器找不到某處參考 OBJECTIVE-C 類別。 有幾個原因發生此情形： 相同[MT5210](#MT5210) ，此外：

* 第三方繫結繫結 OBJECTIVE-C 通訊協定，但沒有不加上註解與<code>[Protocol]</code>其 api 定義中的屬性。 解決方案：
  - 新增遺漏`[Protocol]`屬性：

              [BaseType (typeof (NSObject))]
              [Protocol] // Add this
              public interface MyProtocol
              {
              }

<a name="MT5212" />

### <a name="mt5212-native-linking-failed-duplicate-symbol-"></a>MT5212:原生連結失敗，重複的符號: *。

會發生這種情況是當原生連結器遭遇重複所有原生程式庫之間的符號。 遵循此錯誤可能有一或多個[MT5213](#MT5213)錯誤的每個項目符號的位置。 此錯誤的可能原因：

* 相同的原生程式庫是包含兩次。
* 若要定義相同的符號，就會發生兩個不同的原生程式庫。
* 原生程式庫未正確建置，並多次包含相同的符號。
  您可以使用下列命令，從終端機的確認 （將 i386 取代 x86_64/armv7/armv7s/arm64 根據哪一個架構中，您要建置的）：

        # Native libraries are usually fat libraries, containing binary code for
        # several architectures in the same file. First we extract the binary
        # code for the architecture we're interested in.
        lipo libNative.a -thin i386 -output libNative.i386.a

        # Now query the native library for the duplicated symbol.
        nm libNative.i386.a | fgrep 'SYMBOL'

        # You can also list the object files inside the native library.
        # In most cases this will reveal duplicated object files.
        ar -t libNative.i386.a

  有幾個可能的方式，修正此問題：

  - 要求的提供者的原生程式庫修正此問題，並提供更新的版本。
  - 它自行修正藉由移除額外的物件檔案 （這只適用於問題實際上是重複的目的檔）

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

  - 要求的連結器，以移除未使用的程式碼。 Xamarin.iOS 會自動執行此動作如果符合所有下列條件：
    - 所有第三方繫結`[LinkWith]`屬性已啟用 SmartLink:

            [assembly: LinkWith ("libNative.a", SmartLink = true)]

    - 否`-gcc_flags`傳遞給 mtouch （在專案的 iOS 組建 選項的 其他 mtouch 引數 欄位）。
    - 您也可向連結器直接移除未使用的程式碼，藉由新增`-gcc_flags -dead_strip`其他 mtouch 引數，在 iOS 中專案的建置選項。

<a name="MT5213" />

### <a name="mt5213-duplicate-symbol-in--location-related-to-previous-error"></a>MT5213:中有重複的符號: * （與之前錯誤相關的位置）

會報告此錯誤只搭配[MT5212](#MT5212)。 請參閱[MT5212](#MT5212)如需詳細資訊。

<a name="MT5214" />

### <a name="mt5214-native-linking-failed-undefined-symbol--this-symbol-was-referenced-the-managed-member--please-verify-that-all-the-necessary-frameworks-have-been-referenced-and-native-libraries-linked"></a>MT5214:原生連結失敗，未定義的符號: *。 參考這個符號的受管理的成員 *。 請確認所有必要的架構已連結的參考和原生程式庫。

當 managed 程式碼包含不存在的原生方法 P/Invoke 時，會報告此錯誤。 例如: 

```csharp
using System.Runtime.InteropServices;
class MyImports {
   [DllImport ("__Internal")]
   public static extern void MyInexistentFunc ();
}
```

有幾個可能的解決方案：

  -  從原始程式檔中移除有問題 P/Invoke。
  -  啟用受管理的連結器 （這是在專案的 iOS 組建 選項中，藉由將 「 連結器行為 」 設定為 「 所有組件 」） 的所有組件。 這會有效地移除所有 P/Invokes 不使用應用程式 （自動，而不是以手動方式像前一個點）。 缺點是，這會讓您的模擬器組建略慢，而且如果它使用反映-可以找到連結器的詳細資訊，它可能會中斷您的應用程式[此處](~/ios/deploy-test/linker.md))
  -  建立第二個的原生程式庫包含遺失的原生符號。 請注意，這只是因應措施 （如果您嘗試呼叫這些函式，您的應用程式會當機）。

<a name="MT5215" />

### <a name="mt5215-references-to--might-require-additional--frameworkxxx-or--lxxx-instructions-to-the-native-linker"></a>MT5215:參考 ' *' 可能會需要額外-framework = XXX 或-lXXX 指示原生連結器

這是警告，表示參考的程式庫，偵測到 P/Invoke，但不是與其連結的應用程式。

<a name="MT5216" />

### <a name="mt5216-native-linking-failed-for--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5216:設定連結的原生失敗 *。 請提出錯誤報告在 http://bugzilla.xamarin.com

連結 AOT 編譯器的輸出時，會報告此錯誤。

此錯誤最可能表示在 Xamarin.iOS 中的 bug。 請提出錯誤報告在[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT5217" />

### <a name="mt5217-native-linking-possibly-failed-because-the-linker-command-line-was-too-long--characters"></a>MT5217:原生連結可能會失敗，因為連結器命令列太長 (* 字元)。

原生連結失敗，您也可以連結器命令太長，所以發生此。

Xamarin.iOS 專案會經常參考原生的符號，以動態方式表示原生連結器可能會移除這類原生的符號，在原生的連結過程中，因為原生連結器不會看到，會使用這些符號。

通常是 Xamarin.iOS 的原生連結器將使用這類符號會被要求`-u symbol`連結器旗標，但如果有許多這類符號，整個命令列可能會超過最大的命令列長度所指定的作業系統。

有幾個可能的來源，這類動態符號：

* P/Invokes 移到靜態連結程式庫中的方法 (dll 名稱的所在`__Internal`DllImport 屬性中`[DllImport ("__Internal")]`)。
* 欄位參考靜態連結程式庫中的記憶體位置，從繫結專案 (`[Field]`屬性)。
* 從繫結專案 （使用累加建置時或在不使用靜態的註冊機構） 的靜態連結程式庫中所參考的 objective C 類別。

可能的解決方案：

* （如果可進行的所有組件，而不是唯一的 SDK 組件），請讓受管理的連結器。 動態符號，以便連結器的命令列不超過最大值，這可能會移除足夠的來源。
* 減少 P/Invokes、 欄位參考和/或 OBJECTIVE-C 類別的數目。
* 請重寫要較短的名稱的動態符號。
* 傳遞`-dlsym:false`做為其他 mtouch 引數，在 iOS 中專案的建置選項。 使用此選項時，Xamarin.iOS 將產生的原生參考 AOT 編譯的程式碼，和不需要向連結器將保留這個符號。 不過，這只適用於裝置組建，而且有 P/Invokes 不存在於靜態程式庫中的函式，它會導致連結器錯誤。
* 傳遞`--dynamic-symbol-mode=code`做為其他 mtouch 引數，在專案的 iOS 組建選項。 使用此選項時，Xamarin.iOS 將產生額外的原生程式碼會參考這些符號，而不是要求原生連結器將這些符號使用命令列引數。 這種方法的缺點是，它將會稍微增加可執行檔的大小。
* 藉由傳遞啟用靜態註冊機構`--registrar:static`做為其他 mtouch 引數，在 iOS 中的專案組建選項 （對於模擬器組建，因為靜態註冊機構已經預設針對裝置組建）。 靜態註冊機構會產生靜態方式參考 Objective C 類別的程式碼，因此不需要向原生的連結器，以保留這類類別。
* 停用累加建置 （針對裝置組建）。 增量組建已啟用，靜態的註冊機構所產生的程式碼不會視為原生連結器，也就是說，Xamarin.iOS 仍然必須要求連結器，以保留參考 OBJECTIVE-C 類別中。 因此停用累加建置，將導致這項需求。

<a name="MT5218" />

### <a name="mt5218-cant-ignore-the-dynamic-symbol-symbol---ignore-dynamic-symbolsymbol-because-it-was-not-detected-as-a-dynamic-symbol"></a>MT5218:不能忽視 {符號} 的動態符號 (-略過動態符號 = {符號}) 因為它沒有偵測到為動態的符號。

命令列引數`--ignore-dynamic-symbol=symbol`已通過，但這個符號不是動態的符號必須以手動方式保留與已辨識符號。

有兩個主要的原因：

* 符號名稱不正確。
    * 不在前面加上底線的符號名稱。
    * Objective C 類別的符號是`OBJC_CLASS_$_<classname>`。
* 符號是正確的但已經保留以正常方式 （一些建置選項的原因來變更的動態符號的確切清單） 的符號。

### <a name="mt53xx-other-tools"></a>MT53xx:其他工具

<!--
  MT53xx other tools
  -->

<a name="MT5301" />

### <a name="mt5301-missing-strip-tool-please-install-xcode-command-line-tools-component"></a>MT5301:遺漏 '移除' 工具。 請安裝 Xcode '命令列工具 」 元件

<a name="MT5302" />

### <a name="mt5302-missing-dsymutil-tool-please-install-xcode-command-line-tools-component"></a>MT5302:遺漏 'dsymutil' 工具。 請安裝 Xcode '命令列工具 」 元件

<a name="MT5303" />

### <a name="mt5303-failed-to-generate-the-debug-symbols-dsym-directory-please-review-the-build-log"></a>MT5303:無法產生偵錯符號 （dSYM 目錄）。 請檢閱建置記錄檔。

若要建立偵錯符號的最終的.app 目錄上執行 dsymutil 時，就會發生錯誤。 請檢閱建置記錄檔，請參閱 dsymutil 的輸出，並查看如何修正它。

<a name="MT5304" />

### <a name="mt5304-failed-to-strip-the-final-binary-please-review-the-build-log"></a>MT5304:無法刪除最終二進位檔。 請檢閱建置記錄檔。

執行 '帶狀' 工具來移除應用程式的偵錯資訊時，就會發生錯誤。

<a name="MT5305" />

### <a name="mt5305-missing-lipo-tool-please-install-xcode-command-line-tools-component"></a>MT5305:遺漏 'lipo' 工具。 請安裝 Xcode '命令列工具 」 元件

<a name="MT5306" />

### <a name="mt5306-failed-to-create-the-a-fat-library-please-review-the-build-log"></a>MT5306:無法建立 fat 程式庫。 請檢閱建置記錄檔。

執行 'lipo' 工具時，就會發生錯誤。 請檢閱組建記錄檔，以 'lipo' 回報的錯誤。

<a name="MT5307" />

### <a name="mt5307-failed-to-sign-the-executable-please-review-the-build-log"></a>MT5307:無法簽署可執行檔。 請檢閱建置記錄檔。

登入應用程式時，就會發生錯誤。 請檢閱組建記錄檔，以 'codesign' 回報的錯誤。

<!-- 5308 is used by mmp -->
<!-- 5309 is used by mmp -->
<!-- 5310 is used by mmp -->

## <a name="mt6xxx-mtouch-internal-tools-error-messages"></a>MT6xxx: mtouch 內部工具錯誤訊息

### <a name="mt600x-stripper"></a>MT600x:清除

<!--
 MT6xxx mtouch internal tools
  MT600x Stripper
  -->

<a name="MT6001" />

### <a name="mt6001-running-version-of-cecil-doesnt-support-assembly-stripping"></a>MT6001:Cecil 執行版本不支援移除的組件

<a name="MT6002" />

### <a name="mt6002-could-not-strip-assembly-"></a>MT6002:無法移除組件`*`。

移除應用程式中的 managed 程式碼 （移除的 IL 程式碼） 的組件時，就會發生錯誤。

<a name="MT6003" />

### <a name="mt6003-unauthorizedaccessexception-message"></a>MT6003: [UnauthorizedAccessException 訊息]

安全性時發生錯誤移除偵錯符號，從應用程式。

## <a name="mt7xxx-msbuild-error-messages"></a>MT7xxx:MSBuild 錯誤訊息

<!--
 MT7xxx msbuild errors
  -->

<a name="MT7001" />

### <a name="mt7001-could-not-resolve-host-ips-for-wifi-debugger-settings"></a>MT7001:無法解析主機 Ip WiFi 偵錯工具設定。

*MSBuild 工作：DetectDebugNetworkConfigurationTaskBase*

疑難排解步驟：

- 嘗試執行`csharp -e 'System.Net.Dns.GetHostEntry (System.Net.Dns.GetHostName ()).AddressList'`（這將使您的 IP 位址而非錯誤很明顯地）。
- 嘗試執行 「 ping \`hostname\`」 這可能會讓您的詳細資訊，例如： `cannot resolve MyHost.local: Unknown host`

在某些情況下，它是 「 區域網路 」 問題，並藉由新增未知的主機可解決`127.0.0.1   MyHost.local`在`/etc/hosts`。

<a name="MT7002" />

### <a name="mt7002-this-machine-does-not-have-any-network-adapters-this-is-required-when-debugging-or-profiling-on-device-over-wifi"></a>MT7002:這部電腦沒有任何網路介面卡。 偵錯或分析裝置上透過 WiFi 時，這是必要的。

*MSBuild 工作：DetectDebugNetworkConfigurationTaskBase*

<a name="MT7003" />

### <a name="mt7003-the-app-extension--does-not-contain-an-infoplist"></a>MT7003:應用程式擴充功能 ' *' 不包含 Info.plist。

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7004" />

### <a name="mt7004-the-app-extension--does-not-specify-a-cfbundleidentifier"></a>MT7004:應用程式擴充功能 ' *' 未指定 CFBundleIdentifier。

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7005" />

### <a name="mt7005-the-app-extension--does-not-specify-a-cfbundleexecutable"></a>MT7005:應用程式擴充功能 ' *' 未指定 CFBundleExecutable。

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7006" />

### <a name="mt7006-the-app-extension--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7006:應用程式擴充功能 '\*' 具有無效的 CFBundleIdentifier (\*)，它不是主要的應用程式套件組合的 CFBundleIdentifier （*）。

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7007" />

### <a name="mt7007-the-app-extension--has-a-cfbundleidentifier--that-ends-with-the-illegal-suffix-key"></a>MT7007:應用程式擴充功能 '\*' 具有 CFBundleIdentifier (\*)，結尾是不合法的後置詞".key 」。

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7008" />

### <a name="mt7008-the-app-extension--does-not-specify-a-cfbundleshortversionstring"></a>MT7008:應用程式擴充功能 ' *' 未指定 CFBundleShortVersionString。

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7009" />

### <a name="mt7009-the-app-extension--has-an-invalid-infoplist-it-does-not-contain-an-nsextension-dictionary"></a>MT7009:應用程式擴充功能 ' *' 有無效的 Info.plist： 它不包含 NSExtension 字典。

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7010" />

### <a name="mt7010-the-app-extension--has-an-invalid-infoplist-the-nsextension-dictionary-does-not-contain-an-nsextensionpointidentifier-value"></a>MT7010:應用程式擴充功能 ' *' 有無效的 Info.plist: NSExtension 字典不包含 NSExtensionPointIdentifier 值。

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7011" />

### <a name="mt7011-the-watchkit-extension--has-an-invalid-infoplist-the-nsextension-dictionary-does-not-contain-an-nsextensionattributes-dictionary"></a>MT7011:WatchKit 擴充功能 ' *' 有無效的 Info.plist: NSExtension 字典不包含 NSExtensionAttributes 字典。

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7012" />

### <a name="mt7012-the-watchkit-extension--does-not-have-exactly-one-watch-app"></a>MT7012:WatchKit 擴充功能 ' *' 沒有剛好一個 watch 應用程式。

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7013" />

### <a name="mt7013-the-watchkit-extension--has-an-invalid-infoplist-uirequireddevicecapabilities-must-contain-the-watch-companion-capability"></a>MT7013:WatchKit 擴充功能 ' *' 有無效的 Info.plist:UIRequiredDeviceCapabilities 必須包含 '監看式隨附' 功能。

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7014" />

### <a name="mt7014-the-watch-app--does-not-contain-an-infoplist"></a>MT7014:Watch 應用程式 ' *' 不包含 Info.plist。

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7015" />

### <a name="mt7015-the-watch-app--does-not-specify-a-cfbundleidentifier"></a>MT7015:Watch 應用程式 ' *' 未指定 CFBundleIdentifier。

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7016" />

### <a name="mt7016-the-watch-app--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7016:Watch 應用程式 '\*' 具有無效的 CFBundleIdentifier (\*)，它不是主要的應用程式套件組合的 CFBundleIdentifier （*）。

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7017" />

### <a name="mt7017-the-watch-app--does-not-have-a-valid-uidevicefamily-value-expected-watch-4-but-found--"></a>MT7017:Watch 應用程式 '\*' 沒有有效的 UIDeviceFamily 值。 預期的 '(4) 觀看' 卻找到 '\* （*） '。

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7018" />

### <a name="mt7018-the-watch-app--does-not-specify-a-cfbundleexecutable"></a>MT7018:Watch 應用程式 ' *' 未指定 CFBundleExecutable

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7019" />

### <a name="mt7019-the-watch-app--has-an-invalid-wkcompanionappbundleidentifier-value--it-does-not-match-the-main-app-bundles-cfbundleidentifier-"></a>MT7019:Watch 應用程式 '\*' 有無效的 WKCompanionAppBundleIdentifier 值 ('\*')，它不符合主要的應用程式套件組合的 CFBundleIdentifier ('* ')。

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7020" />

### <a name="mt7020-the-watch-app--has-an-invalid-infoplist-the-wkwatchkitapp-key-must-be-present-and-have-a-value-of-true"></a>MT7020:Watch 應用程式 ' *' 有無效的 Info.plist： 必須要有 WKWatchKitApp 金鑰，並將其值為 'true'。

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7021" />

### <a name="mt7021-the-watch-app--has-an-invalid-infoplist-the-lsrequiresiphoneos-key-must-not-be-present"></a>MT7021:Watch 應用程式 ' *' 有無效的 Info.plist: LSRequiresIPhoneOS 金鑰不能存在。

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7022" />

### <a name="mt7022-the-watch-app--does-not-contain-a-watch-extension"></a>MT7022:Watch 應用程式 ' *' 不包含監看式延伸模組。

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7023" />

### <a name="mt7023-the-watch-extension--does-not-contain-an-infoplist"></a>MT7023:監看式延伸模組 ' *' 不包含 Info.plist。

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7024" />

### <a name="mt7024-the-watch-extension--does-not-specify-a-cfbundleidentifier"></a>MT7024:監看式延伸模組 ' *' 未指定 CFBundleIdentifier。

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7025" />

### <a name="mt7025-the-watch-extension--does-not-specify-a-cfbundleexecutable"></a>MT7025:監看式延伸模組 ' *' 未指定 CFBundleExecutable。

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7026" />

### <a name="mt7026-the-watch-extension--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7026:監看式延伸模組 '\*' 具有無效的 CFBundleIdentifier (\*)，它不是主要的應用程式套件組合的 CFBundleIdentifier （*）。

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7027" />

### <a name="mt7027-the-watch-extension--has-a-cfbundleidentifier--that-ends-with-the-illegal-suffix-key"></a>MT7027:監看式延伸模組 '\*' 具有 CFBundleIdentifier (\*)，結尾是不合法的後置詞".key 」。

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7028" />

### <a name="mt7028-the-watch-extension--has-an-invalid-infoplist-it-does-not-contain-an-nsextension-dictionary"></a>MT7028:監看式延伸模組 ' *' 有無效的 Info.plist： 它不包含 NSExtension 字典。

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7029" />

### <a name="mt7029-the-watch-extension--has-an-invalid-infoplist-the-nsextensionpointidentifier-must-be-comapplewatchkit"></a>MT7029:監看式延伸模組 ' *' 有無效的 Info.plist: NSExtensionPointIdentifier 必須是"com.apple.watchkit 」。

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7030" />

### <a name="mt7030-the-watch-extension--has-an-invalid-infoplist-the-nsextension-dictionary-must-contain-nsextensionattributes"></a>MT7030:監看式延伸模組 ' *' 有無效的 Info.plist: NSExtension 字典必須包含 NSExtensionAttributes。

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7031" />

### <a name="mt7031-the-watch-extension--has-an-invalid-infoplist-the-nsextensionattributes-dictionary-must-contain-a-wkappbundleidentifier"></a>MT7031:監看式延伸模組 ' *' 有無效的 Info.plist: NSExtensionAttributes 字典必須包含 WKAppBundleIdentifier。

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7032" />

### <a name="mt7032-the-watchkit-extension--has-an-invalid-infoplist-uirequireddevicecapabilities-should-not-contain-the-watch-companion-capability"></a>MT7032:WatchKit 擴充功能 ' *' 有無效的 Info.plist:UIRequiredDeviceCapabilities 不應包含 '監看式隨附' 功能。

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7033" />

### <a name="mt7033-the-watch-app--does-not-contain-an-infoplist"></a>MT7033:Watch 應用程式 ' *' 不包含 Info.plist。

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7034" />

### <a name="mt7034-the-watch-app--does-not-specify-a-cfbundleidentifier"></a>MT7034:Watch 應用程式 ' *' 未指定 CFBundleIdentifier。

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7035" />

### <a name="mt7035-the-watch-app--does-not-have-a-valid-uidevicefamily-value-expected--but-found--"></a>MT7035:Watch 應用程式 '\*' 沒有有效的 UIDeviceFamily 值。 必須是 '\*'但卻發現'\* (\*)'。

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7036" />

### <a name="mt7036-the-watch-app--does-not-specify-a-cfbundleexecutable"></a>MT7036:Watch 應用程式 ' *' 未指定 CFBundleExecutable。

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7037" />

### <a name="mt7037-the-watchkit-extension-extensionname-has-an-invalid-wkappbundleidentifier-value--it-does-not-match-the-watch-apps-cfbundleidentifier-"></a>MT7037:WatchKit 延伸模組 '{extensionName}' 有無效的 WKAppBundleIdentifier 值 ('\*')，它不符合監看式應用程式的 CFBundleIdentifier ('\*')。

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7038" />

### <a name="mt7038-the-watch-app--has-an-invalid-infoplist-the-wkcompanionappbundleidentifier-must-exist-and-must-match-the-main-app-bundles-cfbundleidentifier"></a>MT7038:Watch 應用程式 ' *' 有無效的 Info.plist: WKCompanionAppBundleIdentifier 必須存在，且必須符合主要的應用程式套件組合的 CFBundleIdentifier。

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7039" />

### <a name="mt7039-the-watch-app--has-an-invalid-infoplist-the-lsrequiresiphoneos-key-must-not-be-present"></a>MT7039:Watch 應用程式 ' *' 有無效的 Info.plist: LSRequiresIPhoneOS 金鑰不能存在。

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7040" />

### <a name="mt7040-the-app-bundle-appbundlepath-does-not-contain-an-infoplist"></a>MT7040:應用程式套件組合 {AppBundlePath} 不包含 Info.plist。

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7041" />

### <a name="mt7041-main-infoplist-path-does-not-specify-a-cfbundleidentifier"></a>MT7041:Main Info.plist 路徑未指定 CFBundleIdentifier。

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7042" />

### <a name="mt7042-main-infoplist-path-does-not-specify-a-cfbundleexecutable"></a>MT7042:Main Info.plist 路徑未指定 CFBundleExecutable。

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7043" />

### <a name="mt7043-main-infoplist-path-does-not-specify-a-cfbundlesupportedplatforms"></a>MT7043:Main Info.plist 路徑未指定 CFBundleSupportedPlatforms。

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7044" />

### <a name="mt7044-main-infoplist-path-does-not-specify-a-uidevicefamily"></a>MT7044:Main Info.plist 路徑未指定 UIDeviceFamily。

*MSBuild 工作：ValidateAppBundleTaskBase*

<a name="MT7045" />

### <a name="mt7045-unrecognized-format-"></a>MT7045:無法辨認的格式: *。

*MSBuild 工作：PropertyListEditorTaskBase*

其中 * 可以是：

- 字串
- array
- dict
- bool
- 實數
- 整數
- date
- 資料

<a name="MT7046" />

### <a name="mt7046-add-entry--incorrectly-specified"></a>MT7046:加入:項目，*，正確指定。

*MSBuild 工作：PropertyListEditorTaskBase*

<a name="MT7047" />

### <a name="mt7047-add-entry--contains-invalid-array-index"></a>MT7047:加入:項目，*，包含無效的陣列索引。

*MSBuild 工作：PropertyListEditorTaskBase*

<a name="MT7048" />

### <a name="mt7048-add--entry-already-exists"></a>MT7048:新增: * 項目已經存在。

*MSBuild 工作：PropertyListEditorTaskBase*

<a name="MT7049" />

### <a name="mt7049-add-cant-add-entry--to-parent"></a>MT7049:加入:無法新增項目，*，以父代。

*MSBuild 工作：PropertyListEditorTaskBase*

<a name="MT7050" />

### <a name="mt7050-delete-cant-delete-entry--from-parent"></a>MT7050:刪除：無法刪除項目，*，從父代。

*MSBuild 工作：PropertyListEditorTaskBase*

<a name="MT7051" />

### <a name="mt7051-delete-entry--contains-invalid-array-index"></a>MT7051:刪除：項目，*，包含無效的陣列索引。

*MSBuild 工作：PropertyListEditorTaskBase*

<a name="MT7052" />

### <a name="mt7052-delete-entry--does-not-exist"></a>MT7052:刪除：項目，*，不存在。

*MSBuild 工作：PropertyListEditorTaskBase*

<a name="MT7053" />

### <a name="mt7053-import-entry--incorrectly-specified"></a>MT7053:匯入：項目，*，正確指定。

*MSBuild 工作：PropertyListEditorTaskBase*

<a name="MT7054" />

### <a name="mt7054-import-entry--contains-invalid-array-index"></a>MT7054:匯入：項目，*，包含無效的陣列索引。

*MSBuild 工作：PropertyListEditorTaskBase*

<a name="MT7055" />

### <a name="mt7055-import-error-reading-file-"></a>MT7055:匯入：讀取檔案時發生錯誤: *。

*MSBuild 工作：PropertyListEditorTaskBase*

<a name="MT7056" />

### <a name="mt7056-import-cant-add-entry--to-parent"></a>MT7056:匯入：無法新增項目，*，以父代。

*MSBuild 工作：PropertyListEditorTaskBase*

<a name="MT7057" />

### <a name="mt7057-merge-cant-add-array-entries-to-dict"></a>MT7057:合併：無法將陣列項目加入至 dict.

*MSBuild 工作：PropertyListEditorTaskBase*

<a name="MT7058" />

### <a name="mt7058-merge-specified-entry-must-be-a-container"></a>MT7058:合併：指定的項目必須是一個容器。

*MSBuild 工作：PropertyListEditorTaskBase*

<a name="MT7059" />

### <a name="mt7059-merge-entry--contains-invalid-array-index"></a>MT7059:合併：項目，*，包含無效的陣列索引。

*MSBuild 工作：PropertyListEditorTaskBase*

<a name="MT7060" />

### <a name="mt7060-merge-entry--does-not-exist"></a>MT7060:合併：項目，*，不存在。

*MSBuild 工作：PropertyListEditorTaskBase*

<a name="MT7061" />

### <a name="mt7061-merge-error-reading-file-"></a>MT7061:合併：讀取檔案時發生錯誤: *。

*MSBuild 工作：PropertyListEditorTaskBase*

<a name="MT7062" />

### <a name="mt7062-set-entry--incorrectly-specified"></a>MT7062:設定：項目，*，正確指定。

*MSBuild 工作：PropertyListEditorTaskBase*

<a name="MT7063" />

### <a name="mt7063-set-entry--contains-invalid-array-index"></a>MT7063:設定：項目，*，包含無效的陣列索引。

*MSBuild 工作：PropertyListEditorTaskBase*

<a name="MT7064" />

### <a name="mt7064-set-entry--does-not-exist"></a>MT7064:設定：項目，*，不存在。

*MSBuild 工作：PropertyListEditorTaskBase*

<a name="MT7065" />

### <a name="mt7065-unknown-propertylist-editor-action-"></a>MT7065:未知的 PropertyList 編輯器動作: *。

*MSBuild 工作：PropertyListEditorTaskBase*

<a name="MT7066" />

### <a name="mt7066-error-loading--"></a>MT7066:載入時發生錯誤 ' *': *。

*MSBuild 工作：PropertyListEditorTaskBase*

<a name="MT7067" />

### <a name="mt7067-error-saving--"></a>MT7067:錯誤節約 ' *': *。

*MSBuild 工作：PropertyListEditorTaskBase*

## <a name="mt8xxx-runtime-error-messages"></a>MT8xxx:執行階段錯誤訊息

<!--
 MT8xxx runtime
  MT800x misc
  -->

<a name="MT8001" />

### <a name="mt8001-version-mismatch-between-the-native-xamarinios-runtime-and-monotouchdll-please-reinstall-xamarinios"></a>MT8001:原生的 Xamarin.iOS 執行階段與 monotouch.dll 的版本不符。 請重新安裝 Xamarin.iOS。

<a name="MT8002" />

### <a name="mt8002-could-not-find-the-method--in-the-type-"></a>MT8002:找不到方法 '\*'中類型'\*'。

<a name="MT8003" />

### <a name="mt8003-failed-to-find-the-closed-generic-method--on-the-type-"></a>MT8003:封閉式泛型方法中找不到 '\*'在類型'\*'。

<a name="MT8004" />

### <a name="mt8004-cannot-create-an-instance-of--for-the-native-object-0x-of-type--because-another-instance-already-exists-for-this-native-object-of-type-"></a>MT8004:無法建立的執行個體 * 的原生物件 0 x * (類型的 ' *')，因為另一個執行個體已經存在於此原生的物件 (類型的 *)。

<a name="MT8005" />

### <a name="mt8005-wrapper-type--is-missing-its-native-objectivec-class-"></a>MT8005:包裝函式類型 '\*'遺漏其原生 ObjectiveC 類別'\*'。

<a name="MT8006" />

### <a name="mt8006-failed-to-find-the-selector--on-the-type-"></a>MT8006:選取器找不到 '\*'在類型'\*'

<a name="MT8007" />

### <a name="mt8007-cannot-get-the-method-descriptor-for-the-selector--on-the-type--because-the-selector-does-not-correspond-to-a-method"></a>MT8007:無法選取器取得方法描述元 '\*'在類型'\*'，因為選取器未對應至方法

<a name="MT8008" />

### <a name="mt8008-the-loaded-version-of-xamariniosdll-was-compiled-for--bits-while-the-process-is--bits-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8008:Xamarin.iOS.dll 載入的版本所編譯的 * 位元，而處理程序是 * 位元。 請將 bug 歸檔在 http://bugzilla.xamarin.com。

這表示有問題發生在建置程序。 請將 bug 歸檔在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT8009" />

### <a name="mt8009-unable-to-locate-the-block-to-delegate-conversion-method-for-the-method-s-parameter--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8009:找不到要委派之方法的轉換方法的區塊 *。*'s 參數 # *。 請將 bug 歸檔在 http://bugzilla.xamarin.com。

這表示未正確繫結 API。 如果這是 Xamarin 所公開的 API，請提報 bug 中我們 bugzilla ([http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS))，則為第三方繫結，請連絡廠商。

<a name="MT8010" />

### <a name="mt8010-native-type-size-mismatch-between-xamariniosmacdll-and-the-executing-architecture-xamariniosmacdll-was-built-for--bit-while-the-current-process-is--bit"></a>MT8010:Xamarin 之間的原生類型大小不符。[iOS |Mac].dll 和執行的架構。 Xamarin。[iOS |專為 Mac].dll *-位元，而目前的處理程序 * 位元。

這表示有問題發生在建置程序。 請將 bug 歸檔在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT8011" />

### <a name="mt8011-unable-to-locate-the-delegate-to-block-conversion-attribute-delegateproxy-for-the-return-value-for-the-method--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8011:找不到方法的傳回值的區塊轉換屬性 ([DelegateProxy]) 的委派 *.*。 請將 bug 歸檔在 http://bugzilla.xamarin.com。

Xamarin.iOS 找不到所需的方法在執行階段 （若要將委派轉換至區塊） 項目。

這通常表示 Xamarin.iOS; 中的 bug請將 bug 歸檔在[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT8012" />

### <a name="mt8012-invalid-delegateproxyattribute-for-the-return-value-for-the-method--delegatetype-is-null-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8012:方法的傳回值的無效 DelegateProxyAttribute *。*:委派是 null。 請將 bug 歸檔在 http://bugzilla.xamarin.com。

討論中方法的 DelegateProxy 屬性無效。

這通常表示 Xamarin.iOS; 中的 bug請將 bug 歸檔在[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT8013" />

### <a name="mt8013-invalid-delegateproxyattribute-for-the-return-value-for-the-method--delegatetype-2-specifies-a-type-without-a-handler-field-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8013:方法的傳回值的無效 DelegateProxyAttribute *。*:委派 ({2}) 指定沒有 'Handler' 欄位的類型。 請將 bug 歸檔在 http://bugzilla.xamarin.com。

討論中方法的 DelegateProxy 屬性無效。

這通常表示 Xamarin.iOS; 中的 bug請將 bug 歸檔在[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT8014" />

### <a name="mt8014-invalid-delegateproxyattribute-for-the-return-value-for-the-method--the-delegatetypes-2-handler-field-is-null-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8014:方法的傳回值的無效 DelegateProxyAttribute *。*:委派 ({2}) 'Handler' 欄位為 null。 請將 bug 歸檔在 http://bugzilla.xamarin.com。

討論中方法的 DelegateProxy 屬性無效。

這通常表示 Xamarin.iOS; 中的 bug請將 bug 歸檔在[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT8015" />

### <a name="mt8015-invalid-delegateproxyattribute-for-the-return-value-for-the-method--the-delegatetypes-2-handler-field-is-not-a-delegate-its-a--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8015:方法的傳回值的無效 DelegateProxyAttribute *。*:委派 ({2}) 'Handler' 欄位不是委派，它是 *。 請將 bug 歸檔在 http://bugzilla.xamarin.com。

討論中方法的 DelegateProxy 屬性無效。

這通常表示 Xamarin.iOS; 中的 bug請將 bug 歸檔在[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT8016" />

### <a name="mt8016-unable-to-convert-delegate-to-block-for-the-return-value-for-the-method--because-the-input-isnt-a-delegate-its-a--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8016:無法轉換為封鎖方法的傳回值的委派 *。*，輸入不是委派，因為它是 *。 請將 bug 歸檔在 http://bugzilla.xamarin.com。

討論中方法的 DelegateProxy 屬性無效。

這通常表示 Xamarin.iOS; 中的 bug請將 bug 歸檔在[ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<!-- 8017 is used by mmp -->

<a name="MT8018" />

### <a name="mt8018-internal-consistency-error-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8018:內部一致性錯誤。 請提出錯誤報告在 http://bugzilla.xamarin.com。

這表示在 Xamarin.iOS 中的 bug。 請將 bug 歸檔在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT8019" />

### <a name="mt8019-could-not-find-the-assembly--in-the-loaded-assemblies"></a>MT8019:找不到組件 * 中載入的組件。

這表示在 Xamarin.iOS 中的 bug。 請將 bug 歸檔在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT8020" />

### <a name="mt8020-could-not-find-the-module-with-metadatatoken--in-the-assembly-"></a>MT8020:找不到與 MetadataToken 模組 * 組件中 *。

這表示在 Xamarin.iOS 中的 bug。 請將 bug 歸檔在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT8021" />

### <a name="mt8021-unknown-implicit-token-type-"></a>MT8021:未知的隱含語彙基元型別: *。

這表示在 Xamarin.iOS 中的 bug。 請將 bug 歸檔在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT8022" />

### <a name="mt8022-expected-the-token-reference--to-be-a--but-its-a--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8022:預期的語彙基元參考 * 為 *，但它是 *。 請提出錯誤報告在 http://bugzilla.xamarin.com。

這表示在 Xamarin.iOS 中的 bug。 請將 bug 歸檔在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT8023" />

### <a name="mt8023-an-instance-object-is-required-to-construct-a-closed-generic-method-for-the-open-generic-method--token-reference--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8023:執行個體物件，才能建構封閉式的泛型方法，在開啟的泛型方法: * (權杖參考: *)。 請提出錯誤報告在 http://bugzilla.xamarin.com。

這表示在 Xamarin.iOS 中的 bug。 請將 bug 歸檔在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。

<a name="MT8024" />

### <a name="mt8024-could-not-find-a-valid-extension-type-for-the-smart-enum-smarttype-please-file-a-bug-at-httpsbugzillaxamarincom"></a>MT8024:找不到智慧型的列舉 '{smart_type}' 的有效的延伸模組類型。 請將 bug 歸檔在 https://bugzilla.xamarin.com。

這表示在 Xamarin.iOS 中的 bug。 請將 bug 歸檔在[http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)。
