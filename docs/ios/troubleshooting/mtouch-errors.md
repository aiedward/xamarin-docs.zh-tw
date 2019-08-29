---
title: Xamarin iOS 錯誤
description: 本檔說明 mtouch 所產生的各種錯誤, 這是用來組合 Xamarin iOS 應用程式的工具。 錯誤是由程式碼所列出, 並提供完整描述。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9F76162B-D622-45DA-996B-2FBF8017E208
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/06/2018
ms.openlocfilehash: d75f46f8b06ef0e743218d5f42d5b2732b6e4158
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70120968"
---
# <a name="xamarinios-errors"></a>Xamarin iOS 錯誤

## <a name="mt0xxx-mtouch-error-messages"></a>MT0xxx: mtouch 錯誤訊息

例如， 參數、環境、遺漏工具。

<!--
 MT0xxx mtouch itself, e.g. parameters, environment (e.g. missing tools)
 https://github.com/xamarin/xamarin-macios/blob/master/tools/mtouch/error.cs
  -->

<a name="MT0000" />

### <a name="mt0000-unexpected-error---please-fill-a-bug-report-at-httpsgithubcomxamarinxamarin-maciosissuesnew"></a>MT0000:意外的錯誤-請于前填寫錯誤報表 https://github.com/xamarin/xamarin-macios/issues/new

發生未預期的錯誤狀況。 請在[GitHub](https://github.com/xamarin/xamarin-macios/issues/new)上提出新問題, 並盡可能提供最多資訊, 包括:

- 完整組建記錄檔, 最高詳細資訊 ( `-v -v -v -v`例如在**其他 mtouch 引數**中);
- 重現錯誤的最小測試案例;和
- 所有版本的

取得確切版本資訊的最簡單方式是使用  **Visual Studio for Mac**  功能表,**關於 Visual Studio for Mac**專案、**顯示詳細資料** 按鈕, 以及複製/貼上版本資訊 (您可以使用 **複製資訊** 按鈕).

<a name="MT0001" />

### <a name="mt0001--devname-was-provided-without-any-device-specific-action"></a>MT0001: 在沒有任何裝置特定動作的情況下提供 '-devname '

如果在未要求任何裝置特定的動作 (-logdev/-installdev/-killdev/-launchdev/-listapps) 時, 將-devname 傳遞至 mtouch, 就會發出警告。

<a name="MT0002" />

### <a name="mt0002-could-not-parse-the-environment-variable-"></a>MT0002:無法剖析環境變數 *。

如果您嘗試設定不正確環境索引鍵 = 值變數組, 就會發生此錯誤。 正確的格式為:`mtouch --setenv=VARIABLE=VALUE`

<a name="MT0003" />

### <a name="mt0003-application-name-exe-conflicts-with-an-sdk-or-product-assembly-dll-name"></a>MT0003:應用程式名稱 ' * .exe ' 與 SDK 或產品元件 (.dll) 名稱衝突。

可執行元件的名稱和應用程式的名稱, 不能符合應用程式中任何 dll 的名稱。 請修改可執行檔的名稱。

<a name="MT0004" />

### <a name="mt0004-new-refcounting-logic-requires-sgen-to-be-enabled-too"></a>MT0004:新的 refcounting 邏輯也需要啟用 SGen。

如果您啟用 refcounting 擴充功能, 您也必須在專案的 [iOS 組建選項] ([高級] 索引標籤) 中啟用 SGen 垃圾收集行程。

從7.2.1 開始, 已提升這項需求, 新的 refcounting 邏輯可以同時啟用 Boehm 和 SGen 垃圾收集行程。

<a name="MT0005" />

### <a name="mt0005-the-output-directory--does-not-exist"></a>MT0005:輸出目錄 * 不存在。

請建立目錄。

此錯誤不會再產生, mtouch 會自動建立目錄 (如果不存在)。

<a name="MT0006" />

### <a name="mt0006-there-is-no-devel-platform-at--use---platformplat-to-specify-the-sdk"></a>MT0006:* 沒有對內平臺, 請使用--platform = 平臺來指定 SDK。

Xamarin 在錯誤訊息中所述的位置找不到 SDK 目錄。 請確認路徑是否正確。

<a name="MT0007" />

### <a name="mt0007-the-root-assembly--does-not-exist"></a>MT0007:根元件 * 不存在。

Xamarin 在錯誤訊息中所述的位置找不到元件。 請確認路徑是否正確。

<a name="MT0008" />

### <a name="mt0008-you-should-provide-one-root-assembly-only-found--assemblies-"></a>MT0008:您應該只提供一個根元件, 找到 # 個元件: *。

已將一個以上的根元件傳遞至 mtouch, 但只能有一個根元件。

<a name="MT0009" />

### <a name="mt0009-error-while-loading-assemblies-"></a>MT0009:載入元件時發生錯誤: *。

從根元件參考載入元件時發生錯誤。 可能會在組建輸出中提供詳細資訊。

<a name="MT0010" />

### <a name="mt0010-could-not-parse-the-command-line-arguments-"></a>MT0010:無法剖析命令列引數: *。

剖析命令列引數時發生錯誤。 請確認它們都正確。

<a name="MT0011" />

### <a name="mt0011--was-built-against-a-more-recent-runtime--than-monotouch-supports"></a>MT0011: * 是針對比 MonoTouch 支援的較新執行時間 (*) 所建立。

通常會報告此警告, 因為專案參考到不是使用 Xamarin. iOS BCL 所建立的類別庫。

使用 .NET 4.0 SDK 的應用程式可能無法在僅支援 .NET 2.0 的系統上使用, 但使用 .NET 4.0 建立的程式庫可能無法在 Xamarin 上執行, 它可能會使用不存在於 Xamarin iOS 上的 API。

一般的解決方法是將程式庫建立為 Xamarin. iOS 類別庫。 這可以藉由建立新的 Xamarin 類別庫專案來完成, 並將所有原始程式檔加入其中。 如果您沒有程式庫的原始程式碼, 您應洽詢廠商, 並要求他們提供其程式庫的 Xamarin 與 iOS 相容版本。

<a name="MT0012" />

### <a name="mt0012-incomplete-data-is-provided-to-complete-"></a>MT0012:提供了不完整的資料來完成 *。

在目前的 Xamarin 版本中, 不會再回報此錯誤。

<a name="MT0013" />

### <a name="mt0013-profiling-support-requires-sgen-to-be-enabled-too"></a>MT0013:分析支援也需要啟用 sgen。

如果啟用分析 (--程式碼剖析), 則必須啟用 SGen (--SGen)。

<a name="MT0014" />

### <a name="mt0014-the-ios--sdk-does-not-support-building-applications-targeting-"></a>MT0014:IOS * SDK 不支援建立以 * 為目標的應用程式。

這可能會在下列情況下發生:

- 已啟用 ARMv6, 且已安裝 Xcode 4.5 或更新版本。
- 已啟用 ARMv7s, 且已安裝 Xcode 4.4 或更早版本。

請確認已安裝的 Xcode 版本支援選取的架構。

<a name="MT0015" />

### <a name="mt0015-invalid-abi--supported-abis-are-i386-x86_64--armv7-armv7llvm-armv7llvmthumb2-armv7s-armv7sllvm-armv7sllvmthumb2-arm64-and-arm64llvm"></a>MT0015:不正確 ABI: *。 支援的 Abi 包括: i386、x86_64、armv7、armv7 + llvm、armv7 + llvm + thumb2、armv7s、armv7s + llvm、armv7s + llvm + thumb2、arm64 和 arm64 + llvm。

傳遞了不正確 ABI 給 mtouch。 請指定有效的 ABI。

<a name="MT0016" />

### <a name="mt0016-the-option--has-been-deprecated"></a>MT0016:選項 * 已被取代。

所述的 mtouch 選項已被取代, 將會被忽略。

<a name="MT0017" />

### <a name="mt0017-you-should-provide-a-root-assembly"></a>MT0017:您應該提供根元件。

建立應用程式時, 必須指定根元件 (通常是主要可執行檔)。

<a name="MT0018" />

### <a name="mt0018-unknown-command-line-argument-"></a>MT0018:未知的命令列引數: *。

Mtouch 無法辨識錯誤訊息中所述的命令列引數。

<a name="MT0019" />

### <a name="mt0019-only-one---loginstallkilllaunchdev-or---launchdebugsim-option-can-be-used"></a>MT0019:只能使用一個--[log | install | kill | 啟動] dev 或--[啟動 | debug] sim 選項。

有數個 mtouch 選項無法同時使用:

- --logdev
- --installdev
- --killdev
- --launchdev
- --launchdebug
- --launchsim

<a name="MT0020" />

### <a name="mt0020-the-valid-options-for--are-"></a>MT0020:'\*' 的有效選項為 '\*'。

<a name="MT0021" />

### <a name="mt0021-cannot-compile-using-gccg---use-gcc-when-using-the-static-registrar-this-is-the-default-when-compiling-for-device-either-remove-the---use-gcc-flag-or-use-the-dynamic-registrar---registrardynamic"></a>MT0021:使用靜態註冊機構時, 無法使用 gcc/g + + (--使用-gcc) 進行編譯 (這是針對裝置編譯時的預設值)。 請移除--use-gcc 旗標, 或使用動態註冊機構 (--註冊機構: dynamic)。

<a name="MT0022" />

### <a name="mt0022-the-options---unsupported--enable-generics-in-registrar-and---registrar-are-not-compatible"></a>MT0022:選項 '--不支援----------------in------註冊機構 ' 與 '--

同時移除和`--registrar`這`--unsupported--enable-generics-in-registrar`兩個選項。 從7.2.1 開始, 預設註冊機構支援泛型。

已不再顯示此錯誤 (命令列引數`--unsupported--enable-generics-in-registrar`已從 mtouch 移除)。

<a name="MT0023" />

### <a name="mt0023-application-name-exe-conflicts-with-another-user-assembly"></a>MT0023:應用程式名稱 ' * .exe ' 與另一個使用者元件相衝突。

可執行元件的名稱和應用程式的名稱, 不能符合應用程式中任何 dll 的名稱。 請修改可執行檔的名稱。

<a name="MT0024" />

### <a name="mt0024-could-not-find-required-file-"></a>MT0024:找不到必要的檔案 ' * '。

<a name="MT0025" />

### <a name="mt0025-no-sdk-version-was-provided-please-add---sdkxy-to-specify-which-ios-sdk-should-be-used-to-build-your-application"></a>MT0025:未提供任何 SDK 版本。 請新增`--sdk=X.Y`以指定應該使用哪個 iOS SDK 來建立您的應用程式。

<a name="MT0026" />

### <a name="mt0026-could-not-parse-the-command-line-argument--"></a>MT0026:無法剖析命令列引數 ' * ': *

<a name="MT0027" />

### <a name="mt0027-the-options--and--are-not-compatible"></a>MT0027:選項 '\*' 和 '\*' 不相容。

<a name="MT0028" />

### <a name="mt0028-cannot-enable-pie--pie-when-targeting-ios-41-or-earlier-please-disable-pie--piefalse-or-set-the-deployment-target-to-at-least-ios-42"></a>MT0028:以 iOS 4.1 或更早版本為目標時, 無法啟用圓形圖 (-圓形圖)。 請停用圓形圖 (-圓形圖: false), 或將部署目標設定為至少 iOS 4。2

<a name="MT0029" />

### <a name="mt0029-repl---enable-repl-is-only-supported-in-the-simulator---sim"></a>MT0029:只有在模擬器 (--sim) 中才支援複寫 (--啟用-複寫)。

只有當您要建立模擬器時, 才支援複寫。 這表示如果您將傳遞`--enable-repl`至 mtouch, 則也必須通過`--sim`。

<a name="MT0030" />

### <a name="mt0030-the-executable-name--and-the-app-name--are-different-this-may-prevent-crash-logs-from-getting-symbolicated-properly"></a>MT0030:可執行檔名稱\*() 和應用程式名稱\*() 不同, 這可能會導致損毀記錄無法正確 symbolicated。

當可執行檔和應用程式有不同的名稱 (不含副檔名) 時, 如果 Xcode symbolicates (將記憶體位址轉譯為函式名稱和檔案/行號), 進程可能會失敗。

若要修正此問題, 請在專案的組建/iOS 應用程式選項中變更「應用程式名稱」, 或在專案的組建/輸出選項中變更「元件名稱」。

<a name="MT0031" />

### <a name="mt0031-the-command-line-arguments---enable-background-fetch-and---launch-for-background-fetch-require---launchsim-too"></a>MT0031:命令列引數 '--enable-background-fetch ' 和 '--啟動---------------------------

<a name="MT0032" />

### <a name="mt0032-the-option---debugtrack-is-ignored-unless---debug-is-also-specified"></a>MT0032:除非同時指定了 '--debug ', 否則會忽略 '--debugtrack ' 選項。

<a name="MT0033" />

### <a name="mt0033-a-xamarinios-project-must-reference-either-monotouchdll-or-xamariniosdll"></a>MT0033:一個 Xamarin. iOS 專案必須參考 monotouch 或. .dll

<a name="MT0034" />

### <a name="mt0034-cannot-include-both-monotouchdll-and-xamariniosdll-in-the-same-xamarinios-project----is-referenced-explicitly-while--is-referenced-by-"></a>MT0034:不能同時在同一個 Xamarin. ios 專案\*中同時包含 ' monotouch ' 和 ' Xamarin ',\*因為 ' ' 是由 ' * ' 所參考。

<!-- MT0035 unused -->

<a name="MT0036" />

### <a name="mt0036-cannot-launch-a--simulator-for-a--app-please-enable-the-correct-architectures-in-your-projects-ios-build-options-advanced-page"></a>MT0036:無法啟動 * 應用程式的 * 模擬器。 請在您專案的 iOS 組建選項 (Advanced page) 中啟用正確的架構。

<a name="MT0037" />

### <a name="mt0037-monotouchdll-is-not-64-bit-compatible-either-reference-xamariniosdll-or-do-not-build-for-a-64-bit-architecture-arm64-andor-x86_64"></a>MT0037: monotouch 與64位不相容。 參考 Xamarin 或不是針對64位架構 (ARM64 和/或 x86_64) 所建立。

<a name="MT0038" />

### <a name="mt0038-the-old-registrars---registraroldstaticolddynamic-are-not-supported-when-referencing-xamariniosdll"></a>MT0038:參考 Xamarin 時, 不支援舊的註冊機構 (--註冊機構: oldstatic | olddynamic)。

<a name="MT0039" />

### <a name="mt0039-applications-targeting-armv6-cannot-reference-xamariniosdll"></a>MT0039:以 ARMv6 為目標的應用程式無法參考 Xamarin. .dll。

<a name="MT0040" />

### <a name="mt0040-could-not-find-the-assembly--referenced-by-"></a>MT0040:找不到 '\* \*' 所參考的元件 ' '。

<a name="MT0041" />

### <a name="mt0041-cannot-reference-both-monotouchdll-and-xamariniosdll"></a>MT0041:無法同時參考 ' monotouch ' 和 ' Xamarin '。

<a name="MT0042" />

### <a name="mt0042-no-reference-to-either-monotouchdll-or-xamariniosdll-was-found-a-reference-to-monotouchdll-will-be-added"></a>MT0042:找不到任何 monotouch 或 Xamarin 的參考。 將會加入 monotouch 的參考。

<a name="MT0043" />

### <a name="mt0043-the-boehm-garbage-collector-is-currently-not-supported-when-referencing-xamariniosdll-the-sgen-garbage-collector-has-been-selected-instead"></a>MT0043:參考 ' Xamarin ' 時, 目前不支援 Boehm 垃圾收集行程。 已改為選取 SGen 垃圾收集行程。

整合專案僅支援 SGen 垃圾收集行程。 請確定沒有任何其他的 mtouch 旗標指定 Boehm 做為垃圾收集行程。

<a name="MT0044" />

### <a name="mt0044---listsim-is-only-supported-with-xcode-60-or-later"></a>MT0044:--listsim 僅支援 Xcode 6.0 或更新版本。

安裝較新的 Xcode 版本。

<a name="MT0045" />

### <a name="mt0045---extension-is-only-supported-when-using-the-ios-80-or-later-sdk"></a>MT0045:--只有在使用 iOS 8.0 (或更新版本) SDK 時, 才支援延伸模組。

<!-- MT0046 is not reported anymore -->

<a name="MT0047" />

### <a name="mt0047-the-minimum-deployment-target-for-unified-applications-is-511-the-current-deployment-target-is--please-select-a-newer-deployment-target-in-your-projects-ios-application-options"></a>MT0047:統一應用程式的最小部署目標為 5.1.1, 目前的部署目標為 ' * '。 請在專案的 iOS 應用程式選項中選取較新的部署目標。

<!-- MT0048 is not reported anymore -->

<a name="MT0049" />

### <a name="mt0049-framework-is-supported-only-if-deployment-target-is-80-or-later--features-might-not-work-correctly"></a>只有當部署目標是8.0 或更新版本時, 才支援 MT0049: *. framework。 * 功能可能無法正常運作。

部署目標所參考的 iOS 版本不支援指定的架構。 請將部署目標更新為較新的 iOS 版本, 或從應用程式中移除指定架構的使用方式。

<!-- MT0050 is not reported anymore -->

<a name="MT0051" />

### <a name="mt0051-xamarinios--requires-xcode-50-or-later-the-current-xcode-version-found-in--is-"></a>MT0051:Xamarin. iOS * 需要 Xcode 5.0 或更新版本。 目前的 Xcode 版本 (在 * 中找到) 是 *。

安裝較新的 Xcode。

<a name="MT0052" />

### <a name="mt0052-no-command-specified"></a>MT0052:未指定任何命令。

未針對 mtouch 指定任何動作。

<!-- 0053 is used by mmp -->

<a name="MT0054" />

### <a name="mt0054-unable-to-canonicalize-the-path--"></a>MT0054:無法正常化路徑 ' * ': *

這是內部錯誤。 如果您看到此錯誤, 請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出新的問題。

<a name="MT0055" />

### <a name="mt0055-the-xcode-path--does-not-exist"></a>MT0055:Xcode 路徑 ' * ' 不存在。

使用`--sdkroot`傳遞的 Xcode 路徑不存在。 請指定有效的路徑。

<a name="MT0056" />

### <a name="mt0056-cannot-find-xcode-in-the-default-location-applicationsxcodeapp-please-install-xcode-or-pass-a-custom-path-using---sdkroot-path"></a>MT0056:在預設位置 (/Applications/Xcode.app) 找不到 Xcode。 請安裝 Xcode, 或使用--sdkroot \<path > 傳遞自訂路徑。

<a name="MT0057" />

### <a name="mt0057-cannot-determine-the-path-to-xcodeapp-from-the-sdk-root--please-specify-the-full-path-to-the-xcodeapp-bundle"></a>MT0057:無法從 sdk 根 ' * ' 判斷 Xcode 的路徑。 請指定 Xcode 應用程式套件組合的完整路徑。

使用`--sdkroot`傳遞的路徑未指定有效的 Xcode 應用程式。 請指定 Xcode 應用程式的路徑。

<a name="MT0058" />

### <a name="mt0058-the-xcodeapp--is-invalid-the-file--does-not-exist"></a>MT0058:Xcode 的應用程式 '\*' 無效 (檔案 '\*' 不存在)。

使用`--sdkroot`傳遞的路徑未指定有效的 Xcode 應用程式。 請指定 Xcode 應用程式的路徑。

<a name="MT0059" />

### <a name="mt0059-could-not-find-the-currently-selected-xcode-on-the-system-"></a>MT0059:在系統上找不到目前選取的 Xcode: *

<a name="MT0060" />

### <a name="mt0060-could-not-find-the-currently-selected-xcode-on-the-system-xcode-select---print-path-returned--but-that-directory-does-not-exist"></a>MT0060:在系統上找不到目前選取的 Xcode。 ' xcode-select--print-path ' 傳回 ' * ', 但該目錄不存在。

<a name="MT0061" />

### <a name="mt0061-no-xcodeapp-specified-using---sdkroot-using-the-system-xcode-as-reported-by-xcode-select---print-path-"></a>MT0061未指定 Xcode (使用--sdkroot), 請使用 ' Xcode-select--print-path ' 所報告的系統 Xcode: *

這是參考用警告, 說明將使用的 Xcode, 因為未指定任何一個。

<a name="MT0062" />

### <a name="mt0062-no-xcodeapp-specified-using---sdkroot-or-xcode-select---print-path-using-the-default-xcode-instead-"></a>MT0062:未指定 Xcode (使用--sdkroot 或 ' Xcode-select--print-path '), 請改為使用預設 Xcode: *

這是參考用警告, 說明將使用的 Xcode, 因為未指定任何一個。

<a name="MT0063" />

### <a name="mt0063-cannot-find-the-executable-in-the-extension--no-cfbundleexecutable-entry-in-its-infoplist"></a>MT0063:在擴充功能中找不到可執行檔 (其資訊中沒有 CFBundleExecutable 專案)。

每個 plist 都必須有可執行檔 (使用 CFBundleExecutable 專案), 不過, 在組建期間應該會自動產生一個專案。

這通常表示在 Xamarin. iOS 中的錯誤;請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上使用測試案例提出新問題。

<a name="MT0064" />

### <a name="mt0064-xamarinios-only-supports-embedded-frameworks-with-unified-projects"></a>MT0064:Xamarin 僅支援具有整合專案的內嵌架構。

在使用 Unified API 時, Xamarin 僅支援內嵌架構;請更新您的專案, 以使用 Unified API。

<a name="MT0065" />

### <a name="mt0065-xamarinios-only-supports-embedded-frameworks-when-deployment-target-is-at-least-80-current-deployment-target--embedded-frameworks-"></a>MT0065:當部署目標至少為 8.0 (目前的部署目標: * embedded framework: *) 時, Xamarin 僅支援內嵌的架構。

當部署目標至少為 8.0 (因為舊版 iOS 不支援內嵌架構) 時, Xamarin 僅支援內嵌架構。

請將專案 plist 中的部署目標更新為8.0 或更高版本。

<a name="MT0066" />

### <a name="mt0066-invalid-build-registrar-assembly-"></a>MT0066:組建註冊機構元件無效: *

這通常表示在 Xamarin. iOS 中的錯誤;請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上使用測試案例提出新問題。

<a name="MT0067" />

### <a name="mt0067-invalid-registrar-"></a>MT0067:註冊機構無效: *

這通常表示在 Xamarin. iOS 中的錯誤;請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上使用測試案例提出新問題。

<a name="MT0068" />

### <a name="mt0068-invalid-value-for-target-framework-"></a>MT0068:目標 framework 的值無效: *。

使用--target framework 引數傳遞了不正確目標架構。 請指定有效的目標架構。

<a name="MT0069" />

<!--### MT0069: currently unused -->

<a name="MT0070" />

### <a name="mt0070-invalid-target-framework--valid-target-frameworks-are-"></a>MT0070:不正確目標 framework: *。 有效的目標 framework 為: *。

使用--target framework 引數傳遞了不正確目標架構。 請指定有效的目標架構。

<a name="MT0071" />

### <a name="mt0071-unknown-platform--this-usually-indicates-a-bug-in-xamarinios-please-file-a-bug-report-at-httpbugzillaxamarincom-with-a-test-case"></a>MT0071:未知的平臺: *。 這通常表示 Xamarin.iOS; 中的 bug請提出錯誤報告在 http://bugzilla.xamarin.com 與測試案例。

這通常表示在 Xamarin. iOS 中的錯誤;請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上使用測試案例提出新問題。

<a name="MT0072" />

### <a name="mt0072-extensions-are-not-supported-for-the-platform-"></a>MT0072:平臺 ' * ' 不支援延伸模組。

這通常表示在 Xamarin. iOS 中的錯誤;請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上使用測試案例提出新問題。

<a name="MT0073" />

### <a name="mt0073-xamarinios--does-not-support-a-deployment-target-of--the-minimum-is--please-select-a-newer-deployment-target-in-your-projects-infoplist"></a>MT0073:Xamarin. iOS * 不支援部署目標 * (最小值為 *)。 請在您專案的 Info. plist 中選取較新的部署目標。

最小部署目標是錯誤訊息中指定的目標。請在專案的 Info. plist 中選取較新的部署目標。

如果無法更新部署目標, 請使用較舊版本的 Xamarin. iOS。

<a name="MT0074" />

### <a name="mt0074-xamarinios--does-not-support-a-minimum-deployment-target-of--the-maximum-is--please-select-an-older-deployment-target-in-your-projects-infoplist-or-upgrade-to-a-newer-version-of-xamarinios"></a>MT0074:Xamarin. iOS * 不支援最低部署目標 * (最大值為 *)。 請在專案的 plist 中選取較舊的部署目標, 或升級至較新版本的 Xamarin. iOS。

Xamarin 不支援將最低部署目標設定為比這個特定版本的 Xamarin 建立的版本更高的版本。

請在專案的 plist 中選取較舊的最低部署目標, 或升級至較新版本的 Xamarin. iOS。

<a name="MT0075" />

### <a name="mt0075-invalid-architecture--for--projects-valid-architectures-are-"></a>MT0075:* 專案的架構 ' * ' 無效。 有效的架構為: *

指定了不正確架構。 請確認架構是否有效。

<a name="MT0076" />

### <a name="mt0076-no-architecture-specified-using-the---abi-argument-an-architecture-is-required-for--projects"></a>MT0076:未指定任何架構 (使用--abi 引數)。 \* 專案需要架構。

這通常表示在 Xamarin. iOS 中的錯誤;請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上使用測試案例提出新問題。

<a name="MT0077" />

### <a name="mt0077-watchos-projects-must-be-extensions"></a>MT0077:WatchOS 專案必須是延伸模組。

這通常表示在 Xamarin. iOS 中的錯誤;請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上使用測試案例提出新問題。

<a name="MT0078" />

### <a name="mt0078-incremental-builds-are-enabled-with-a-deployment-target--80-currently--this-is-not-supported-the-resulting-application-will-not-launch-on-ios-9-so-the-deployment-target-will-be-set-to-80"></a>MT0078:以部署目標啟用累加組建 < 8.0 (目前為 *)。 這不受支援 (產生的應用程式將不會在 iOS 9 上啟動), 因此部署目標會設定為8.0。

這是一則警告, 通知此組建的部署目標已設定為 8.0, 讓累加式組建正常運作。

只有當部署目標至少為8.0 時, 才支援增量組建 (因為產生的應用程式不會在 iOS 9 上啟動)。

<a name="MT0079" />

### <a name="mt0079-the-recommended-xcode-version-for-xamarinios--is-xcode--or-later-the-current-xcode-version-found-in--is-"></a>MT0079:建議的 Xcode 版本為 Xcode * 或更新版本。 目前的 Xcode 版本 (在 * 中找到) 是 *。

這項警告會通知目前版本的 Xcode 不是此版本之 Xamarin 的建議 Xcode 版本。

請升級 Xcode 以確保最佳行為。

<a name="MT0080" />

### <a name="mt0080-disabling-newrefcount---new-refcountfalse-is-deprecated"></a>MT0080:停用 NewRefCount,--new-refcount: false, 已被取代。

這是警告, 會通知停用新 refcount (--new-refcount: false) 的要求已被忽略。

所有專案的新 refcount 功能現在都是必要的, 因此不可能再停用。

<a name="MT0081" />

### <a name="mt0081-the-command-line-argument---download-crash-report-also-requires---download-crash-report-to"></a>MT0081:命令列引數--下載-損毀報表也需要--下載-當機-回報至。

<a name="MT0082" />

### <a name="mt0082-repl---enable-repl-is-only-supported-when-linking-is-not-used---nolink"></a>MT0082:只有在未使用連結 (--nolink) 時, 才支援複寫 (--啟用-複寫)。

<a name="MT0083" />

### <a name="mt0083-asm-only-bitcode-is-not-supported-on-watchos-use-either---bitcodemarker-or---bitcodefull"></a>MT0083:WatchOS 不支援僅限 Asm 的 bitcode。 請使用--bitcode: 標記或--bitcode: full。

<a name="MT0084" />

### <a name="mt0084-bitcode-is-not-supported-in-the-simulator-do-not-pass---bitcode-when-building-for-the-simulator"></a>MT0084:模擬器不支援 Bitcode。 建立模擬器時不要通過--bitcode。

<a name="MT0085" />

### <a name="mt0085-no-reference-to--was-found-it-will-be-added-automatically"></a>MT0085:找不到 ' * ' 的參考。 它會自動新增。

<a name="MT0086" />

### <a name="mt0086-a-target-framework---target-framework-must-be-specified-when-building-for-tvos-or-watchos"></a>MT0086:建立 TVOS 或 WatchOS 時, 必須指定目標 framework (--目標架構)。

這表示在 Xamarin. iOS 中的錯誤。 請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出新問題。

<a name="MT0087" />

### <a name="mt0087-incremental-builds---fastdev-is-not-supported-with-the-boehm-gc-incremental-builds-will-be-disabled"></a>MT0087:Boehm GC 不支援累加式組建 (--fastdev)。 增量組建將會停用。

<a name="MT0088" />

### <a name="mt0088-the-gc-must-be-in-cooperative-mode-for-watchos-apps-please-remove-the---coopfalse-argument-to-mtouch"></a>MT0088:GC 必須處於 watchOS 應用程式的合作模式。 請移除 mtouch 的----------------

<a name="MT0089" />

### <a name="mt0089-the-option--cannot-take-the-value--when-cooperative-mode-is-enabled-for-the-gc"></a>MT0089:為 GC 啟用\*合作模式時, 選項 '\*' 無法接受值 ' '。

<a name="MT0091" />

### <a name="mt0091-this-version-of-xamarinios-requires-the--sdk-shipped-with-xcode--either-upgrade-xcode-to-get-the-required-header-files-or-set-the-managed-linker-behaviour-to-link-framework-sdks-only-to-try-to-avoid-the-new-apis"></a>MT0091:此版本的 Xamarin 需要 * SDK (隨附于 Xcode *)。 請升級 Xcode 以取得必要的標頭檔, 或將 managed 連結器行為設定為僅連結 Framework Sdk (以嘗試避免新的 Api)。

若要建立您的應用程式, 則必須從錯誤訊息中所指定的 SDK 版本, 將標頭檔案。 若要修正此錯誤, 建議的方法是升級 Xcode 以取得所需的 SDK, 這會包含所有必要的標頭檔。 如果您已安裝多個版本的 Xcode, 或想要在非預設位置中使用 Xcode, 請務必在 IDE 的喜好設定中設定正確的 Xcode 位置。

有一個可能的替代方案, 就是啟用 managed 連結器。 這會移除未使用的 API, 包括在大多數情況下, 缺少標頭檔案的新 API (或不完整)。 不過, 如果您的專案使用的 API 與您的 Xcode 所提供的版本較新的 SDK 相比, 這將無法正常執行。

最後一個 straw 的解決方案是使用舊版的 Xamarin, 其中一個支援您的專案所需的 SDK。

<!-- MT0092 used by mlaunch -->

<a name="MT0093" />

### <a name="mt0093-could-not-find-mlaunch"></a>MT0093:找不到 ' mlaunch '。

<!-- MT0094 is not reported anymore -->

<a name="MT0095" />

### <a name="mt0095-aot-files-could-not-be-copied-to-the-destination-directory-dest-error"></a>MT0095:無法將 Aot 檔案複製到目的地目錄 {dest}: {error}

<a name="MT0096" />

### <a name="mt0096-no-reference-to-xamariniosdll-was-found"></a>MT0096:找不到 Xamarin. .dll 的參考。

<!-- MT0097: used by mmp -->
<!-- MT0098: used by mmp -->

<a name="MT0099" />

### <a name="mt0099-internal-error--please-file-a-bug-report-with-a-test-case-httpbugzillaxamarincom"></a>MT0099:內部錯誤 *。 請提出問題報告與測試案例 (http://bugzilla.xamarin.com) 。

當 Xamarin 中的內部一致性檢查失敗時, 就會回報此錯誤訊息。

這通常表示在 Xamarin. iOS 中的錯誤;請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上使用測試案例提出新問題。

<a name="MT0100" />

### <a name="mt0100-invalid-assembly-build-target--please-file-a-bug-report-with-a-test-case-httpbugzillaxamarincom"></a>MT0100:不正確元件組建目標: ' * '。 請提出問題報告與測試案例 (http://bugzilla.xamarin.com) 。

當 Xamarin 中的內部一致性檢查失敗時, 就會回報此錯誤訊息。

這通常表示在 Xamarin. iOS 中的錯誤;請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上使用測試案例提出新問題。

<a name="MT0101" />

### <a name="mt0101-the-assembly--is-specified-multiple-times-in---assembly-build-target-arguments"></a>MT0101:元件 ' * ' 在--元件-組建-目標引數中指定了多次。

錯誤訊息中所提及的元件會在--assembly-build-target 引數中多次指定。 請確認每個元件只提及一次。

<a name="MT0102" />

### <a name="mt0102-the-assemblies--and--have-the-same-target-name--but-different-targets--and-"></a>MT0102:元件 '\*' 和 '\*' 具有相同的目標名稱 ('\*'), 但目標 ('\*' 和 ' * ') 不同。

錯誤訊息中提及的元件有衝突的組建目標。

例如：

```
  --assembly-build-target:Assembly1.dll=framework=MyBinary --assembly-build-target:Assembly2.dll=dynamiclibrary=MyBinary
```

這個範例會嘗試使用相同的 make (`MyBinary`) 建立動態連結程式庫和架構。

<a name="MT0103" />

### <a name="mt0103-the-static-object--contains-more-than-one-assembly--but-each-static-object-must-correspond-with-exactly-one-assembly"></a>MT0103:靜態物件 '\*' 包含一個以上的元件 ('\*'), 但每個靜態物件都必須只對應一個元件。

錯誤訊息中提及的元件全部都會編譯成單一靜態物件。 這是不允許的, 每個元件都必須編譯成不同的靜態物件。

例如：

```
--assembly-build-target:Assembly1.dll=staticobject=MyBinary --assembly-build-target:Assembly2.dll=staticobject=MyBinary
```

這個範例會嘗試建立由兩個元件`MyBinary`(`Assembly1.dll`和`Assembly2.dll`) 組成的靜態物件 (), 這是不允許的。

<a name="MT0105" />

### <a name="mt0105-no-assembly-build-target-was-specified-for-"></a>MT0105:未指定 ' * ' 的元件組建目標。

使用`--assembly-build-target`指定元件組建目標時, 應用程式中的每個元件都必須已指派組建目標。

當錯誤訊息中提及的元件沒有指派的元件組建目標時, 就會回報此錯誤。

`--assembly-build-target`如需詳細資訊, 請參閱相關檔。

<a name="MT0106" />

### <a name="mt0106-the-assembly-build-target-name--is-invalid-the-character--is-not-allowed"></a>MT0106:元件組建目標名稱 '\*' 無效: 不允許字元 '\*'。

元件組建目標名稱必須是有效的檔案名。

例如, 這些值將會觸發此錯誤:

```
--assembly-build-target:Assembly1.dll=staticobject=my/path.o
```

因為`my/path.o`不是有效的檔案名, 因為目錄分隔符號。

<a name="MT0107" />

### <a name="mt0107-the-assemblies--have-different-custom-llvm-optimizations--which-is-not-allowed-when-they-are-all-compiled-to-a-single-binary"></a>MT0107:元件 '\*' 有不同的自訂 LLVM 優化\*(), 不允許在全部編譯成單一二進位檔時使用。

<a name="MT0108" />

### <a name="mt0108-the-assembly-build-target--did-not-match-any-assemblies"></a>MT0108:元件組建目標 ' * ' 不符合任何元件。

<a name="MT0109" />

### <a name="mt0109-the-assembly-0-was-loaded-from-a-different-path-than-the-provided-path-provided-path-1-actual-path-2"></a>MT0109:元件 '{0}' 已從不同的路徑載入, 而非提供的路徑 (提供的{1}路徑:, 實際{2}路徑:)。

這是一則警告, 表示應用程式所參考的元件已從不同的位置載入, 而不是所要求的。

這可能表示應用程式參考了具有相同名稱的多個元件, 但來自不同位置, 這可能會導致非預期的結果 (只會使用第一個元件)。

<a name="MT0110" />

### <a name="mt0110-incremental-builds-have-been-disabled-because-this-version-of-xamarinios-does-not-support-incremental-builds-in-projects-that-include-third-party-binding-libraries-and-that-compiles-to-bitcode"></a>MT0110:已停用累加組建, 因為此版本的 Xamarin 不支援專案中包含協力廠商系結程式庫, 且會編譯為 bitcode 的累加式組建。

增量組建已停用, 因為這個版本的 Xamarin 不支援專案中包含協力廠商系結程式庫, 且會編譯為 bitcode (tvOS 和 watchOS 專案) 的累加式組建。

您不需要採取任何動作, 這則訊息是純粹的資訊。

如需詳細資訊, 請參閱 bug #[51710](https://bugzilla.xamarin.com/show_bug.cgi?id=51710)。

此警告不會再回報。

<a name="MT0111" />

### <a name="mt0111-bitcode-has-been-enabled-because-this-version-of-xamarinios-does-not-support-building-watchos-projects-using-llvm-without-enabling-bitcode"></a>MT0111:Bitcode 已啟用, 因為此版本的 Xamarin 不支援使用 LLVM 來建立 watchOS 專案, 而不啟用 Bitcode。

已自動啟用 Bitcode, 因為這個版本的 Xamarin 不支援使用 LLVM 來建立 watchOS 專案, 而不啟用 Bitcode。

您不需要採取任何動作, 這則訊息是純粹的資訊。

如需詳細資訊, 請參閱 bug #[51634](https://bugzilla.xamarin.com/show_bug.cgi?id=51634)。

<a name="MT0112" />

### <a name="mt0112-native-code-sharing-has-been-disabled-because-"></a>MT0112:已停用原生程式碼共用, 因為 *

有多個原因可以停用程式碼共用:

- 因為容器應用程式的部署目標早于 iOS 8.0 (它是 *))。

原生程式碼共用需要 iOS 8.0, 因為原生程式碼共用是使用 iOS 8.0 引進的使用者架構來執行。

- 因為容器應用程式包含「國際化元件」 (*)。

如果容器應用程式包含國際化元件, 則目前不支援原生程式碼共用。

- 因為容器應用程式具有 managed 連結器 (*) 的自訂 xml 定義。

針對使用 managed 連結器的自訂 xml 定義的專案, 不支援原生程式碼共用。

<a name="MT0113" />

### <a name="mt0113-native-code-sharing-has-been-disabled-for-the-extension--because-"></a>MT0113:已停用延伸模組 ' * ' 的原生程式碼共用, 因為 *。

- 因為容器應用程式 (\*) 與延伸模組 (\*) 之間的 bitcode 選項不同。

  原生程式碼共用需要在共用程式碼的專案之間符合 bitcode 選項。

- 因為容器應用程式 (\*) 與延伸模組 (\*) 之間的--元件組建目標選項不同。

  原生程式碼共用需要--元件組建目標選項在共用程式碼的專案之間是相同的。

  如果未在所有專案中啟用或停用累加式組建, 就會發生這個狀況。

- 因為容器應用程式 (\*) 與延伸模組 (\*) 之間的 I18N 元件不同。

  包含「國際化元件」的延伸模組目前不支援原生程式碼共用。

- 因為在容器應用程式 (\*) 與延伸模組 (\*) 之間, AOT 編譯器的引數不同。

  原生程式碼共用要求 AOT 編譯器的引數在共用程式碼的專案之間沒有差異。

- 因為在容器應用程式 (\*) 與延伸模組 (\*) 之間, AOT 編譯器的其他引數不同。

  原生程式碼共用要求 AOT 編譯器的引數在共用程式碼的專案之間沒有差異。

  如果專案之間的 [以64位浮點數執行所有32位浮點運算] 不同, 就會發生這種狀況。

- 因為容器應用程式 (\*) 和延伸模組 (\*) 中未啟用或停用 LLVM。

  原生程式碼共用需要針對共用程式碼的所有專案啟用或停用 LLVM。

- 因為容器應用程式 (\*) 與延伸模組 (\*) 之間的受管理連結器設定不同。

  原生程式碼共用需要所有共用程式碼的專案都具有相同的 managed 連結器設定。

- 因為容器應用程式 (\*) 與延伸模組 (\*) 之間的 managed 連結器略過元件不同。

  原生程式碼共用需要所有共用程式碼的專案都具有相同的 managed 連結器設定。

- 因為擴充功能具有 managed 連結器 (*) 的自訂 xml 定義。

  針對使用 managed 連結器的自訂 xml 定義的專案, 不支援原生程式碼共用。

- 因為容器應用程式不會針對 ABI * (當擴充功能是針對此 ABI 建立) 來建立。

  若要進行機器碼共用, 容器應用程式必須針對任何應用程式延伸模組所建立的所有架構來建立。

  例如: 當 ARM64 + ARMv7 的延伸模組建立時, 但容器應用程式只為 ARM64 建立時, 就會發生這種狀況。

- 因為容器應用程式是針對 ABI \*而建立的, 所以與擴充功能的 abi (\*) 不相容。

  原生程式碼共用需要為完全相同的 API 建立所有專案。

  例如: 當 ARMv7 + llvm + thumb2 的延伸模組組建, 但容器應用程式只建立 ARMv7 + llvm 時, 就會發生這種狀況。

- 因為容器應用程式會從 '\* \*' 參考元件 ' ', 而擴充功能會參考來自 ' * ' 的不同版本。

  機器碼共用要求所有共用程式碼的專案都使用相同版本的所有元件。

<!-- MT0114: used by mmp -->

<a name="MT0115" />

### <a name="mt0115-it-is-recommended-to-reference-dynamic-symbols-using-code---dynamic-symbol-modecode-when-bitcode-is-enabled"></a>MT0115:建議您在啟用 bitcode 時, 使用程式碼 (--動態符號模式 = 程式碼) 來參考動態符號。

Xamarin iOS 專案通常會動態參考原生符號, 這表示原生連結器可能會在原生連結程式期間移除這類原生符號, 因為原生連結器不會看到使用這些符號。

通常 Xamarin 會要求原生連結器保留這類符號 (使用`-u symbol`連結器旗標), 但在針對 bitcode 進行編譯時, 原生連結器不`-u`接受旗標。

Xamarin 已實作為替代方案: 我們會產生額外的原生程式碼來參考這些符號, 因此原生連結器會看到使用這些符號。 這會在編譯為 bitcode 時自動完成。

如果`--dynamic-symbol-mode=linker`傳遞至 mtouch, 則會停用這個替代方案, 而 Xamarin. iOS 會嘗試傳遞`-u`至原生連結器。 這很可能會導致原生連結器錯誤。

解決方法是從專案的`--dynamic-symbol-mode=linker`組建選項中的其他 mtouch 引數中移除引數。

<!-- 0116 - 0124: free to use -->

<a name="MT0116" />

### <a name="mt0116-invalid-architecture-arch-32-bit-architectures-are-not-supported-when-deployment-target-is-11-or-later-make-sure-the-project-does-not-build-for-a-32-bit-architecture"></a>MT0116:不正確架構: {結構}。 當部署目標為11或更新版本時, 不支援32位架構。 請確定專案不是針對32位架構所建立。

iOS 11 不包含32位應用程式的支援, 因此當部署目標為 iOS 11 或更新版本時, 不支援建立32位應用程式。

將專案的 [iOS 組建] 選項中的目標架構變更為 [arm64], 或將專案的 [資訊] plist 中的部署目標變更為先前的 iOS 版本。

<a name="MT0117" />

### <a name="mt0117-cant-launch-a-32-bit-app-on-a-simulator-that-only-supports-64-bit"></a>MT0117:無法在僅支援64位的模擬器上啟動32位應用程式。

<a name="MT0118" />

### <a name="mt0118-aot-files-could-not-be-found-at-the-expected-directory-msymdir"></a>MT0118:在預期的目錄 ' {msymdir} ' 中找不到 Aot 檔案。

<!-- 0119 - 0123: free to use -->

<a name="MT0123" />

### <a name="mt0123-the-executable-assembly--does-not-reference-"></a>MT0123:可執行檔元件 * 不會參考 *。

在可執行元件中找不到平臺元件 (TVOS .dll/Xamarin. WatchOS .dll) 的參考。

這種情況通常會發生在可執行檔專案中, 使用來自平臺元件的任何程式碼;例如, 空的 Main 方法 (沒有其他程式碼) 會顯示此錯誤:

```csharp
class Program {
    void Main (string[] args)
    {
    }
}
```

使用平臺元件中的 API 將會解決此錯誤:

```csharp
class Program {
    void Main (string[] args)
    {
        System.Console.WriteLine (typeof (UIKit.UIWindow));
    }
}
```

<a name="MT0124" />

### <a name="mt0124-could-not-set-the-current-language-to-lang-according-to-langlang-exception"></a>MT0124:無法將目前的語言設定為 ' {lang} ' (根據 LANG = {LANG}): {exception}

這是警告, 表示無法將目前的語言設定為錯誤訊息中的語言。

目前的語言會預設為系統語言。

<a name="MT0125" />

### <a name="mt0125-the---assembly-build-target-command-line-argument-is-ignored-in-the-simulator"></a>MT0125:模擬器中會忽略--assembly-build-target 命令列引數。

不需要採取任何動作, 這則訊息是純粹的資訊。

<a name="MT0126" />

### <a name="mt0126-incremental-builds-have-been-disabled-because-incremental-builds-are-not-supported-in-the-simulator"></a>MT0126:因為模擬器不支援累加式組建, 所以已停用累加組建。

不需要採取任何動作, 這則訊息是純粹的資訊。

<a name="MT0127" />

### <a name="mt0127-incremental-builds-have-been-disabled-because-this-version-of-xamarinios-does-not-support-incremental-builds-in-projects-that-include-more-than-one-third-party-binding-libraries"></a>MT0127:增量組建已停用, 因為這個版本的 Xamarin 不支援包含多個協力廠商系結程式庫之專案中的累加組建。

增量組建已自動停用, 因為這個版本的 Xamarin 不一定會正確地建立具有多個協力廠商系結程式庫的專案。

不需要採取任何動作, 這則訊息是純粹的資訊。

如需詳細資訊, 請參閱 bug #[52727](https://bugzilla.xamarin.com/show_bug.cgi?id=52727)。

<a name="MT0128" />

### <a name="mt0128-could-not-touch-the-file--"></a>MT0128:無法觸控檔案 ' * ': *

觸控檔案時發生失敗 (這是為了確保部分組建正確地完成)。

可能會忽略此警告;若發生任何問題, 請在[GitHub](https://github.com/xamarin/xamarin-macios/issues/new)上提出新問題, 並進行調查。

<a name="MT0135" />

### <a name="mt0135-did-not-link-system-framework-0-referenced-by-assembly-1-because-it-was-introduced-in-2-3-and-were-using-the-2-4-sdk"></a>MT0135:並未{0}連結系統架構 ' ' (由元件 '{1}' 所參考), 因為它是在{2}中導入{3}, 而我們{2}使用的{4}是 SDK。

若要建立您的應用程式, Xamarin 必須針對系統程式庫進行連結, 其中有些是根據錯誤訊息中指定的 SDK 版本而定。 由於您使用的是較舊版本的 SDK, 因此對這些 Api 的叫用可能會在執行時間失敗。

若要修正此錯誤, 建議的方法是升級 Xcode 以取得所需的 SDK。 如果您已安裝多個版本的 Xcode, 或想要在非預設位置中使用 Xcode, 請務必在 IDE 的喜好設定中設定正確的 Xcode 位置。

或者, 啟用 managed[連結器](https://docs.microsoft.com/en-us/xamarin/ios/deploy-test/linker)以移除未使用的 api, 包括 (在大多數情況下) 需要指定程式庫的新資源。 不過, 如果您的專案所需的 Api 與您的 Xcode 所提供的版本不同, 則這將無法正常執行。

作為最後 straw 的解決方案, 請使用不需要這些新 Sdk 的舊版 Xamarin 就能在建立程式期間出現。

## <a name="mt1xxx-project-related-error-messages"></a>MT1xxx:專案相關的錯誤訊息

### <a name="mt10xx-installer--mtouch"></a>MT10xx:安裝程式/mtouch

<!--
 MT1xxx file copy / symlinks (project related)
  MT10xx installer.cs / mtouch.cs
  -->

<a name="MT1001" />

### <a name="mt1001-could-not-find-an-application-at-the-specified-directory"></a>MT1001:在指定的目錄中找不到應用程式

<a name="MT1002" />

### <a name="mt1002-could-not-create-symlinks-files-were-copied"></a>MT1002:無法建立符號連結, 檔案已複製

<a name="MT1003" />

### <a name="mt1003-could-not-kill-the-application--you-may-have-to-kill-the-application-manually"></a>MT1003:無法終止應用程式 ' * '。 您可能必須手動終止應用程式。

<a name="MT1004" />

### <a name="mt1004-could-not-get-the-list-of-installed-applications"></a>MT1004:無法取得已安裝應用程式的清單。

## <a name="mt1xxx-project-related-error-messages"></a>MT1xxx:專案相關的錯誤訊息

<a name="MT1005" />

### <a name="mt1005-could-not-kill-the-application--on-the-device----you-may-have-to-kill-the-application-manually"></a>MT1005:無法終止裝置 '\* \*' 上的應用程式 ' ': *-您可能必須手動終止應用程式。

<a name="MT1006" />

### <a name="mt1006-could-not-install-the-application--on-the-device--"></a>MT1006:無法在裝置 '\* \*' 上安裝應用程式 ' ': *。

<a name="MT1007" />

### <a name="mt1007-failed-to-launch-the-application--on-the-device---you-can-still-launch-the-application-manually-by-tapping-on-it"></a>MT1007:無法啟動裝置 '\* \*' 上的應用程式 ' ': *。 您仍然可以藉由在應用程式上點擊來手動啟動。

<a name="MT1008" />

### <a name="mt1008-failed-to-launch-the-simulator"></a>MT1008:無法啟動模擬器

如果 mtouch 無法啟動模擬器, 就會回報此錯誤。   有時候可能會發生這種情況, 因為已有過時或不正確模擬器進程正在執行。

您可以使用 Unix 命令列上發出的下列命令來終止停滯的模擬器進程:

```bash
$ launchctl list|grep UIKitApplication|awk '{print $3}'|xargs launchctl remove
```

<a name="MT1009" />

### <a name="mt1009-could-not-copy-the-assembly--to--"></a>MT1009無法將元件 '\*' 複製到 '\*': *

這是特定版本的 Xamarin 的已知問題。

如果發生這種情況, 請嘗試下列因應措施:

```bash
sudo chmod 0644 /Library/Frameworks/Xamarin.iOS.framework/Versions/Current/lib/mono/*/*.mdb
```

不過, 由於已在最新版的 Xamarin 中解決此問題, 請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出新的問題, 並提供完整的版本資訊和組建記錄檔輸出。

<a name="MT1010" />

### <a name="mt1010-could-not-load-the-assembly--"></a>MT1010:無法載入元件 ' * ': *

<a name="MT1011" />

### <a name="mt1011-could-not-add-missing-resource-file-"></a>MT1011:無法新增遺漏的資源檔: ' * '

<a name="MT1012" />

### <a name="mt1012-failed-to-list-the-apps-on-the-device--"></a>MT1012:無法列出裝置 ' * ' 上的應用程式: *

<a name="MT1013" />

### <a name="mt1013-dependency-tracking-error-no-files-to-compare-please-file-a-bug-report-at-httpbugzillaxamarincom-with-a-test-case"></a>MT1013:相依性追蹤錯誤: 沒有要比較的檔案。 請提出錯誤報告在 http://bugzilla.xamarin.com 與測試案例。

這表示在 Xamarin. iOS 中的錯誤。 請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上使用測試案例提出新問題。

<a name="MT1014" />

### <a name="mt1014-failed-to-re-use-cached-version-of--"></a>MT1014:無法重複使用 ' * ' 的快取版本: *。

<a name="MT1015" />

### <a name="mt1015-failed-to-create-the-executable--"></a>MT1015:無法建立可執行檔 ' * ': *

<a name="MT1015" />

### <a name="mt1015-failed-to-create-the-executable--"></a>MT1015:無法建立可執行檔 ' * ': *

<a name="MT1016" />

### <a name="mt1016-failed-to-create-the-notice-file-because-a-directory-already-exists-with-the-same-name"></a>MT1016:無法建立通知檔案, 因為已經有相同名稱的目錄。

從專案中`NOTICE`移除目錄。

<a name="MT1017" />

### <a name="mt1017-failed-to-create-the-notice-file-"></a>MT1017:無法建立通知檔案: *。

<a name="MT1018" />

### <a name="mt1018-your-application-failed-code-signing-checks-and-could-not-be-installed-on-the-device--check-your-certificates-provisioning-profiles-and-bundle-ids-probably-your-device-is-not-part-of-the-selected-provisioning-profile-error-0xe8008015"></a>MT1018:您的應用程式無法進行程式碼簽署檢查, 而且無法安裝在裝置 ' * ' 上。 檢查您的憑證、布建設定檔和套件組合識別碼。 可能是您的裝置不是所選布建設定檔的一部分 (錯誤:0xe8008015).

<a name="MT1019" />

### <a name="mt1019-your-application-has-entitlements-not-supported-by-your-current-provisioning-profile-and-could-not-be-installed-on-the-device--please-check-the-ios-device-log-for-more-detailed-information-error-0xe8008016"></a>MT1019:您的應用程式有目前的布建設定檔不支援的權利, 而且無法安裝在裝置 ' * ' 上。 如需詳細資訊, 請參閱 iOS 裝置記錄檔 (錯誤:0xe8008016).

這可能發生在下列情況:

- 您的應用程式具有目前布建設定檔不支援的權利。
  可能的解決方案：
  - 指定不同的布建設定檔, 以支援您的應用程式所需的權利。
  - 移除目前的布建設定檔中不支援的權利。
- 您嘗試部署的裝置不包含在您所使用的布建設定檔中。
  可能的解決方案：
  - 從 Xcode 中的範本建立新的應用程式、選取相同的布建設定檔, 然後部署至相同的裝置。 有時候 Xcode 可以自動使用新的裝置重新整理布建設定檔 (在其他情況下, Xcode 會詢問您要執行的動作)。
  -移至 iOS 開發人員中心, 並使用新的裝置更新布建設定檔, 然後將更新的布建設定檔下載至您的電腦。

在大部分情況下, 失敗的詳細資訊將會列印到 iOS 裝置記錄檔, 這有助於診斷問題。

<a name="MT1020" />

### <a name="mt1020-failed-to-launch-the-application--on-the-device--"></a>MT1020:無法啟動裝置 '\* \*' 上的應用程式 ' ': *

<a name="MT1021" />

### <a name="mt1021-could-not-copy-the-file--to--2"></a>MT1021:無法將檔案 '\*' 複製到 '\*':{2}

無法複製檔案。 複製作業的錯誤訊息具有錯誤的詳細資訊。

<a name="MT1022" />

### <a name="mt1022-could-not-copy-the-directory--to--2"></a>MT1022:無法將目錄 '\*' 複製到 '\*':{2}

無法複製目錄。 複製作業的錯誤訊息具有錯誤的詳細資訊。

<a name="MT1023" />

### <a name="mt1023-could-not-communicate-with-the-device-to-find-the-application---"></a>MT1023:無法與裝置通訊以尋找應用程式 ' * ': *

嘗試查閱裝置上的應用程式時發生錯誤。

嘗試解決此問題的事項:

- 請從裝置中刪除應用程式, 然後再試一次。
- 中斷裝置連線, 然後重新連線。
- 重新開機裝置。
- 重新開機 Mac。

<a name="MT1024" />

### <a name="mt1024-the-application-signature-could-not-be-verified-on-device--please-make-sure-that-the-provisioning-profile-is-installed-and-not-expired-error-0xe8008017"></a>MT1024:無法在裝置 ' * ' 上驗證應用程式簽章。 請確定布建設定檔已安裝且未過期 (錯誤:0xe8008017).

裝置已拒絕應用程式安裝, 因為無法驗證簽章。

請確定布建設定檔已安裝且未過期。

<a name="MT1025" />

### <a name="mt1025-could-not-list-the-crash-reports-on-the-device-"></a>MT1025:無法列出裝置上的損毀報告 *。

嘗試列出裝置上的損毀報告時發生錯誤。

嘗試解決此問題的事項:

- 請從裝置中刪除應用程式, 然後再試一次。
- 中斷裝置連線, 然後重新連線。
- 重新開機裝置。
- 重新開機 Mac。
- 將裝置與 iTunes 同步處理 (這會從裝置移除任何損毀報告)。

<a name="MT1026" />

### <a name="mt1026-could-not-download-the-crash-report--from-the-device-"></a>MT1026:無法從裝置 * 下載損毀報告 *。

嘗試從裝置下載損毀報告時發生錯誤。

嘗試解決此問題的事項:

- 請從裝置中刪除應用程式, 然後再試一次。
- 中斷裝置連線, 然後重新連線。
- 重新開機裝置。
- 重新開機 Mac。
- 將裝置與 iTunes 同步處理 (這會從裝置移除任何損毀報告)。

<a name="MT1027" />

### <a name="mt1027-cant-use-xcode-7-to-launch-applications-on-devices-with-ios--xcode-7-only-supports-ios-6"></a>MT1027:無法使用 Xcode 7 + 來啟動具有 iOS 的裝置上的應用程式 * (Xcode 7 僅支援 iOS 6 +)。

您無法使用 Xcode 7 +, 在6.0 以下的 iOS 版本的裝置上啟動應用程式。

請使用較舊版本的 Xcode, 或手動按一下應用程式以啟動它。

<a name="MT1028" />

### <a name="mt1028-invalid-device-specification--expected-ios-watchos-or-all"></a>MT1028:不正確裝置規格: ' * '。 必須是 ' ios '、' watchos ' 或 ' all '。

使用--device 傳遞的裝置規格無效。 有效的值為: ' ios '、' watchos ' 或 ' all '。

<a name="MT1029" />

### <a name="mt1029-could-not-find-an-application-at-the-specified-directory-"></a>MT1029:在指定的目錄中找不到應用程式: *

傳遞給--launchdev 的應用程式路徑不存在。 請指定有效的應用程式套件組合。

<a name="MT1030" />

### <a name="mt1030-launching-applications-on-device-using-a-bundle-identifier-is-deprecated-please-pass-the-full-path-to-the-bundle-to-launch"></a>MT1030:使用配套識別碼在裝置上啟動應用程式已被取代。 請傳遞組合的完整路徑以啟動。

建議您傳遞應用程式的路徑, 以便在裝置上啟動, 而不只是套件組合識別碼。

<a name="MT1031" />

### <a name="mt1031-could-not-launch-the-app--on-the-device--because-the-device-is-locked-please-unlock-the-device-and-try-again"></a>MT1031:因為裝置已鎖定, 所以\*無法啟動裝置 '\*' 上的應用程式 ' '。 請解除鎖定裝置, 然後再試一次。

請解除鎖定裝置, 然後再試一次。

<a name="MT1032" />

### <a name="mt1032-this-application-executable-might-be-too-large--mb-to-execute-on-device-if-bitcode-was-enabled-you-might-want-to-disable-it-for-development-it-is-only-required-to-submit-applications-to-apple"></a>MT1032:此應用程式可執行檔可能太大 (* MB), 無法在裝置上執行。 如果已啟用 bitcode, 您可能會想要將它停用以供開發之用, 只需要將應用程式提交給 Apple。

<a name="MT1033" />

### <a name="mt1033-could-not-uninstall-the-application--from-the-device--"></a>MT1033:無法從裝置 '\* \*' 卸載應用程式 ' ': *

<!-- 1034 used by mmp -->

<a name="MT1035" />

### <a name="mt1035-cannot-include-different-versions-of-the-framework-name"></a>MT1035:不能包含架構 ' {name} ' 的不同版本

不可能與相同架構的不同版本連結。

當延伸模組參考的架構版本與容器應用程式不同 (可能是透過協力廠商系結元件) 時, 通常就會發生這種情況。

在此錯誤之後, 將會出現多個[MT1036](#MT1036)錯誤, 列出每個不同架構的路徑。

<a name="MT1036" />

### <a name="mt1036-framework-name-included-from-path-related-to-previous-error"></a>MT1036:架構 ' {name} ' 包含自: {path} (與先前的錯誤相關)

此錯誤只會與[MT1036](#MT1036)一起回報。 如需詳細資訊, 請參閱[MT1036](#MT1036) 。

### <a name="mt11xx-debug-service"></a>MT11xx:Debug 服務

<!--
  MT11xx DebugService.cs
  -->

<a name="MT1101" />

### <a name="mt1101-could-not-start-app"></a>MT1101:無法啟動應用程式

<a name="MT1102" />

### <a name="mt1102-could-not-attach-to-the-app-to-kill-it-"></a>MT1102:無法附加至應用程式 (以將其終止): *

<a name="MT1103" />

### <a name="mt1103-could-not-detach"></a>MT1103:無法卸離

<a name="MT1104" />

### <a name="mt1104-failed-to-send-packet-"></a>MT1104:無法傳送封包: *

<a name="MT1105" />

### <a name="mt1105-unexpected-response-type"></a>MT1105:非預期的回應類型

<a name="MT1106" />

### <a name="mt1106-could-not-get-list-of-applications-on-the-device-request-timed-out"></a>MT1106:無法取得裝置上的應用程式清單:要求已超時。

<a name="MT1107" />

### <a name="mt1107-application-failed-to-launch-"></a>MT1107:應用程式無法啟動: *

請檢查您的裝置是否已鎖定。

如果您要部署企業應用程式或使用免費的布建設定檔, 您可能會信任開發人員 (這會在<a href="https://stackoverflow.com/a/30726375/183422">這裡</a>說明)。

<a name="MT1108" />

### <a name="mt1108-could-not-find-developer-tools-for-this-xx-yy-device"></a>MT1108:找不到此 XX (YY) 裝置的開發人員工具。

Mtouch 的少數作業需要`DeveloperDiskImage.dmg`檔案存在。   這個檔案是 Xcode 的一部分, 而且通常是相對於您用來建立的 SDK (在中`Xcode.app/Contents/Developer/iPhoneOS.platform/DeviceSupport/VERSION/DeveloperDiskImage.dmg`)。

發生此錯誤的原因可能是您沒有符合已連線裝置的 DeveloperDiskImage dmg。

<a name="MT1109" />

### <a name="mt1109-application-failed-to-launch-because-the-device-is-locked-please-unlock-the-device-and-try-again"></a>MT1109:應用程式無法啟動, 因為裝置已鎖定。 請解除鎖定裝置, 然後再試一次。

請檢查您的裝置是否已鎖定。

<a name="MT1110" />

### <a name="mt1110-application-failed-to-launch-because-of-ios-security-restrictions-please-ensure-the-developer-is-trusted"></a>MT1110:應用程式因 iOS 安全性限制而無法啟動。 請確定開發人員是受信任的。

如果您要部署企業應用程式或使用免費的布建設定檔, 您可能會信任開發人員 (這會在<a href="https://stackoverflow.com/a/30726375/183422">這裡</a>說明)。

<a name="MT1111" />

### <a name="mt1111-application-launched-successfully-but-its-not-possible-to-wait-for-the-app-to-exit-as-requested-because-its-not-possible-to-detect-app-termination-when-launching-using-gdbserver"></a>MT1111:應用程式已成功啟動, 但無法等待應用程式結束要求, 因為在使用 gdbserver 啟動時, 無法偵測應用程式終止。

### <a name="mt12xx-simulator"></a>MT12xx:模擬器

<!--
  MT12xx simcontroller.cs
  -->

<a name="MT1201" />

### <a name="mt1201-could-not-load-the-simulator-"></a>MT1201:無法載入模擬器: *

<a name="MT1202" />

### <a name="mt1202-invalid-simulator-configuration-"></a>MT1202:不正確模擬器設定: *

<a name="MT1203" />

### <a name="mt1203-invalid-simulator-specification-"></a>MT1203:不正確模擬器規格: *

<a name="MT1204" />

### <a name="mt1204-invalid-simulator-specification--runtime-not-specified"></a>MT1204:不正確模擬器規格 ' * ': 未指定執行時間。

<a name="MT1205" />

### <a name="mt1205-invalid-simulator-specification--device-type-not-specified"></a>MT1205:不正確模擬器規格 ' * ': 未指定裝置類型。

<a name="MT1206" />

### <a name="mt1206-could-not-find-the-simulator-runtime-"></a>MT1206:找不到模擬器執行時間 ' * '。

<a name="MT1207" />

### <a name="mt1207-could-not-find-the-simulator-device-type-"></a>MT1207:找不到模擬器裝置類型 ' * '。

<a name="MT1208" />

### <a name="mt1208-could-not-find-the-simulator-runtime-"></a>MT1208:找不到模擬器執行時間 ' * '。

<a name="MT1209" />

### <a name="mt1209-could-not-find-the-simulator-device-type-"></a>MT1209:找不到模擬器裝置類型 ' * '。

<a name="MT1210" />

### <a name="mt1210-invalid-simulator-specification--unknown-key-"></a>MT1210:不正確模擬器規格\*:, 未知的\*索引鍵 ' '

<a name="MT1211" />

### <a name="mt1211-the-simulator-version--does-not-support-the-simulator-type-"></a>MT1211:模擬器版本 '\*' 不支援模擬器類型 '\*'

<a name="MT1212" />

### <a name="mt1212-failed-to-create-a-simulator-version-where-type---and-runtime--"></a>MT1212:無法建立類型 = * 和執行時間 = * 的模擬器版本。

<a name="MT1213" />

### <a name="mt1213-invalid-simulator-specification-for-xcode-4-"></a>MT1213:Xcode 4: * 的模擬器規格無效

<a name="MT1214" />

### <a name="mt1214-invalid-simulator-specification-for-xcode-5-"></a>MT1214:Xcode 5 的模擬器規格無效: *

<a name="MT1215" />

### <a name="mt1215-invalid-sdk-specified-"></a>MT1215:指定的 SDK 無效: *

<a name="MT1216" />

### <a name="mt1216-could-not-find-the-simulator-udid-"></a>MT1216:找不到模擬器 UDID ' * '。

<a name="MT1217" />

### <a name="mt1217-could-not-load-the-app-bundle-at-"></a>MT1217:無法載入位於 ' * ' 的應用程式套件組合。

<a name="MT1218" />

### <a name="mt1218-no-bundle-identifier-found-in-the-app-at-"></a>MT1218:在 ' * ' 的應用程式中找不到套件組合識別碼。

<a name="MT1219" />

### <a name="mt1219-could-not-find-the-simulator-for-"></a>MT1219:找不到 ' * ' 的模擬器。

<a name="MT1220" />

### <a name="mt1220-could-not-find-the-latest-simulator-runtime-for-device-"></a>MT1220:找不到裝置 ' * ' 的最新模擬器執行時間。

這通常表示 Xcode 發生問題。

嘗試修正此問題的事項:

- 在 Xcode 中使用模擬器一次。
- 使用--SDK \<版本 > 傳遞明確的 SDK 版本。
- 重新安裝 Xcode。

<a name="MT1221" />

### <a name="mt1221-could-not-find-the-paired-iphone-simulator-for-the-watchos-simulator-"></a>MT1221:找不到 WatchOS 模擬器 ' * ' 配對的 iPhone 模擬器。

在 WatchOS 模擬器中啟動 WatchOS 應用程式時, 也必須有配對的 iOS 模擬器。

您可以使用 Xcode 的裝置 UI (功能表視窗-> 裝置), 將監看模擬器與 iOS 模擬器配對。

### <a name="mt13xx-linkwith"></a>MT13xx: [LinkWith]

<!--
  MT13xx [LinkWith]
  -->

<a name="MT1301" />

### <a name="mt1301-native-library---was-ignored-since-it-does-not-match-the-current-build-architectures-"></a>MT1301:已忽略`*`原\*生程式庫 (), 因為它不符合目前的組建架構 (\*)

<a name="MT1302" />

### <a name="mt1302-could-not-extract-the-native-library--from--please-ensure-the-native-library-was-properly-embedded-in-the-managed-assembly-if-the-assembly-was-built-using-a-binding-project-the-native-library-must-be-included-in-the-project-and-its-build-action-must-be-objcbindingnativelibrary"></a>MT1302:無法從 ' + ' 解壓縮原生程式庫 ' * '。 請確認原生程式庫已正確內嵌在 managed 元件中 (如果元件是使用系結專案所建立的, 則原生程式庫必須包含在專案中, 且其組建動作必須是 ' ObjcBindingNativeLibrary ')。

<a name="MT1303" />

### <a name="mt1303-could-not-decompress-the-native-framework--from--please-review-the-build-log-for-more-information-from-the-native-zip-command"></a>MT1303:無法將原生 framework '\*' 從 '\*' 解壓縮。 如需原生 ' zip ' 命令的詳細資訊, 請參閱組建記錄檔。

無法從系結程式庫解壓縮指定的原生架構。

請參閱 bulid 記錄檔, 以取得來自原生 ' zip ' 命令之失敗的詳細資訊。

<a name="MT1304" />

### <a name="mt1304-the-embedded-framework--in--is-invalid-it-does-not-contain-an-infoplist"></a>MT1304:* 中的內嵌架構 ' * ' 無效: 它不包含 plist。

指定的內嵌架構不包含 plist, 因此不是有效的架構。

請確認架構是否有效。

<a name="MT1305" />

### <a name="mt1305-the-binding-library--contains-a-user-framework--but-embedded-user-frameworks-require-ios-80-the-current-deployment-target-is--please-set-the-deployment-target-in-the-infoplist-file-to-at-least-80"></a>MT1305:系結程式庫\*' ' 包含使用者 framework (\*), 但內嵌的使用者架構需要 iOS 8.0 (目前的部署目標是 *)。 請將 plist 檔案中的部署目標設定為至少8.0。

指定的系結程式庫包含內嵌的架構, 但 Xamarin. iOS 僅支援 iOS 8.0 或更新版本上的 embedded framework。

請將 plist 檔案中的部署目標設定為至少 8.0, 以解決此錯誤 (或不使用內嵌的架構)。

### <a name="mt14xx-crash-reports"></a>MT14xx:損毀報告

<!--
  MT14xx    CrashReports.cs
  -->

<a name="MT1400" />

### <a name="mt1400-could-not-open-crash-report-service-afcconnectionopen-returned-"></a>MT1400:無法開啟損毀報表服務:傳回的 AFCConnectionOpen *

嘗試從裝置存取損毀報告時發生錯誤。

嘗試解決此問題的事項:

- 請從裝置中刪除應用程式, 然後再試一次。
- 中斷裝置連線, 然後重新連線。
- 重新開機裝置。
- 重新開機 Mac。
- 將裝置與 iTunes 同步處理 (這會從裝置移除任何損毀報告)。

<a name="MT1401" />

### <a name="mt1401-could-not-close-crash-report-service-afcconnectionclose-returned-"></a>MT1401:無法關閉損毀報表服務:傳回的 AFCConnectionClose *

嘗試從裝置存取損毀報告時發生錯誤。

嘗試解決此問題的事項:

- 請從裝置中刪除應用程式, 然後再試一次。
- 中斷裝置連線, 然後重新連線。
- 重新開機裝置。
- 重新開機 Mac。
- 將裝置與 iTunes 同步處理 (這會從裝置移除任何損毀報告)。

<a name="MT1402" />

### <a name="mt1402-could-not-read-file-info-for--afcfileinfoopen-returned-"></a>MT1402:無法讀取 *: 的檔案資訊傳回的 AFCFileInfoOpen *

嘗試從裝置存取損毀報告時發生錯誤。

嘗試解決此問題的事項:

- 請從裝置中刪除應用程式, 然後再試一次。
- 中斷裝置連線, 然後重新連線。
- 重新開機裝置。
- 重新開機 Mac。
- 將裝置與 iTunes 同步處理 (這會從裝置移除任何損毀報告)。

<a name="MT1403" />

### <a name="mt1403-could-not-read-crash-report-afcdirectoryopen--returned-"></a>MT1403:無法讀取損毀報告:AFCDirectoryOpen (*) 傳回: *

嘗試從裝置存取損毀報告時發生錯誤。

嘗試解決此問題的事項:

- 請從裝置中刪除應用程式, 然後再試一次。
- 中斷裝置連線, 然後重新連線。
- 重新開機裝置。
- 重新開機 Mac。
- 將裝置與 iTunes 同步處理 (這會從裝置移除任何損毀報告)。

<a name="MT1404" />

### <a name="mt1404-could-not-read-crash-report-afcfilerefopen--returned-"></a>MT1404:無法讀取損毀報告:AFCFileRefOpen (*) 傳回: *

嘗試從裝置存取損毀報告時發生錯誤。

嘗試解決此問題的事項:

- 請從裝置中刪除應用程式, 然後再試一次。
- 中斷裝置連線, 然後重新連線。
- 重新開機裝置。
- 重新開機 Mac。
- 將裝置與 iTunes 同步處理 (這會從裝置移除任何損毀報告)。

<a name="MT1405" />

### <a name="mt1405-could-not-read-crash-report-afcfilerefread--returned-"></a>MT1405:無法讀取損毀報告:AFCFileRefRead (*) 傳回: *

嘗試從裝置存取損毀報告時發生錯誤。

嘗試解決此問題的事項:

- 請從裝置中刪除應用程式, 然後再試一次。
- 中斷裝置連線, 然後重新連線。
- 重新開機裝置。
- 重新開機 Mac。
- 將裝置與 iTunes 同步處理 (這會從裝置移除任何損毀報告)。

<a name="MT1406" />

### <a name="mt1406-could-not-list-crash-reports-afcdirectoryopen--returned-"></a>MT1406:無法列出損毀報告:AFCDirectoryOpen (*) 傳回: *

嘗試從裝置存取損毀報告時發生錯誤。

嘗試解決此問題的事項:

- 請從裝置中刪除應用程式, 然後再試一次。
- 中斷裝置連線, 然後重新連線。
- 重新開機裝置。
- 重新開機 Mac。
- 將裝置與 iTunes 同步處理 (這會從裝置移除任何損毀報告)。

<!--- 1407 used by mmp -->

### <a name="mt16xx-macho"></a>MT16xx:MachO

<!--
  MT16xx    MachO.cs
  -->

<a name="MT1600" />

### <a name="mt1600-not-a-mach-o-dynamic-library-unknown-header-0x-"></a>MT1600:不是符合-O 動態連結程式庫 (未知的標頭 ' 0x * '): *。

處理有問題的動態連結程式庫時發生錯誤。

請確定動態連結程式庫是有效的符合-O 動態連結程式庫。

您可以使用來自終端機的`file`命令來驗證程式庫的格式:

```
file -arch all -l /path/to/library.dylib
```

<a name="MT1601" />

### <a name="mt1601-not-a-static-library-unknown-header--"></a>MT1601:不是靜態程式庫 (未知的標頭 ' * '): *。

處理有問題的靜態程式庫時發生錯誤。

請確定靜態程式庫是有效的符合-O 靜態程式庫。

您可以使用來自終端機的`file`命令來驗證程式庫的格式:

```
file -arch all -l /path/to/library.a
```

<a name="MT1602" />

### <a name="mt1602-not-a-mach-o-dynamic-library-unknown-header-0x-"></a>MT1602:不是符合-O 動態連結程式庫 (未知的標頭 ' 0x * '): *。

處理有問題的動態連結程式庫時發生錯誤。

請確定動態連結程式庫是有效的符合-O 動態連結程式庫。

您可以使用來自終端機的`file`命令來驗證程式庫的格式:

```
file -arch all -l /path/to/library.dylib
```

<a name="MT1603" />

### <a name="mt1603-unknown-format-for-fat-entry-at-position--in-"></a>MT1603:在 * 的位置 * 中, fat 專案的格式未知。

處理有問題的 fat 封存時發生錯誤。

請確認 fat 封存有效。

您可以使用來自終端機的`file`命令來驗證 fat 封存的格式:

```
file -arch all -l /path/to/file
```

<a name="MT1604" />

### <a name="mt1604-file-of-type--is-not-a-macho-file-"></a>MT1604:* 類型的檔案不是 MachO 檔案 (*)。

處理有問題的 MachO 檔時發生錯誤。

請確認檔案是有效的符合-O 動態連結程式庫。

您可以使用來自終端機的`file`命令來驗證檔案的格式:

```
file -arch all -l /path/to/file
```

## <a name="mt2xxx-linker-error-messages"></a>MT2xxx:連結器錯誤訊息

<!--
 MT2xxx Linker
  MT20xx Linker (general) errors
  -->

<a name="MT2001" />

### <a name="mt2001-could-not-link-assemblies"></a>MT2001:無法連結元件

此錯誤表示 managed 連結器發生未預期的錯誤 (例如例外狀況), 而且無法完成或儲存正在處理的元件。 確切錯誤的詳細資訊將會是組建記錄檔的一部分, 例如

```
error MT2001: Could not link assemblies.
    Method: `System.Void Todo.TodoListPageCS/<<-ctor>b__1_0>d::SetStateMachine(System.Runtime.CompilerServices.IAsyncStateMachine)`
    Assembly: `QuickTodo, Version=1.0.6297.28241, Culture=neutral, PublicKeyToken=null`
Reason: Value cannot be null.
Parameter name: instruction
```

請務必針對這類問題提出錯誤報表。 在大部分情況下, 可以提供解決方法, 直到發行適當的修正程式為止。 上述資訊是很重要的 (連同測試案例和/或元件 binairy) 來解決問題。

<a name="MT2002" />

### <a name="mt2002-can-not-resolve-reference-"></a>MT2002:無法解析參考: *

<a name="MT2003" />

### <a name="mt2003-option--will-be-ignored-since-linking-is-disabled"></a>MT2003:將忽略選項 ' * ', 因為已停用連結

<a name="MT2004" />

### <a name="mt2004-extra-linker-definitions-file--could-not-be-located"></a>MT2004:找不到額外的連結器定義檔 ' * '。

<a name="MT2005" />

### <a name="mt2005-definitions-from--could-not-be-parsed"></a>MT2005:無法剖析 ' * ' 中的定義。

<a name="MT2006" />

### <a name="mt2006-can-not-load-mscorlibdll-from--please-reinstall-xamarinios"></a>MT2006:無法從: * 載入 mscorlib.dll。 請重新安裝 Xamarin. iOS。

這通常表示您的 Xamarin iOS 安裝發生問題。 請嘗試重新安裝 Xamarin。

<!--- 2007 used by mmp -->
<!--- 2009 used by mmp -->

<a name="MT2010" />

### <a name="mt2010-unknown-httpmessagehandler--valid-values-are-httpclienthandler-default-cfnetworkhandler-or-nsurlsessionhandler"></a>MT2010:未知的`*`HttpMessageHandler。 有效值為 HttpClientHandler (預設值)、CFNetworkHandler 或 NSUrlSessionHandler

<a name="MT2011" />

### <a name="mt2011-unknown-tlsprovider---valid-values-are-default-or-appletls"></a>MT2011:未知的`*`TlsProvider。  有效值為 default 或 appletls。

指定給`tls-provider=`的值不是有效的 TLS (傳輸層安全性) 提供者。

`default` 和`appletls`是唯一有效的值, 兩者都代表相同的選項, 也就是使用原生 Apple TLS API 提供 SSL/TLS 支援。

<!--- 2012 used by mmp -->
<!--- 2013 used by mmp -->
<!--- 2014 used by mmp -->

<a name="MT2015" />

### <a name="mt2015-invalid-httpmessagehandler--for-watchos-the-only-valid-value-is-nsurlsessionhandler"></a>MT2015:WatchOS 的`*` HttpMessageHandler 無效。 唯一有效的值為 NSUrlSessionHandler。

這是因為專案檔指定了不正確 HttpMessageHandler 而發生的警告。

舊版的預覽工具預設會在專案檔中產生不正確值。

若要修正這個警告, 請在文字編輯器中開啟專案檔, 並從 XML 移除所有 HttpMessageHandler 節點。

<a name="MT2016" />

### <a name="mt2016-invalid-tlsprovider-legacy-option-the-only-valid-value-appletls-will-be-used"></a>MT2016:不正確`legacy` TlsProvider 選項。 將會使用唯一`appletls`有效的值。

`legacy`提供者 (這是完全受控的 SSLv3/僅 TLSv1 提供者) 不再隨附于 Xamarin。 使用此舊提供者, 且現在以較新`appletls`版本建立的專案。

若要修正這個警告, 請在文字編輯器中開啟專案檔, 並從 XML 移除所有 ' MtouchTlsProvider ' ' 節點。

<a name="MT2017" />

### <a name="mt2017-could-not-process-xml-description"></a>MT2017:無法處理 XML 描述。

這表示您提供的[自訂 XML 連結器設定檔](~/cross-platform/deploy-test/linker.md)發生錯誤, 請檢查您的檔案。

<a name="MT2018" />

### <a name="mt2018-the-assembly--is-referenced-from-two-different-locations--and-"></a>MT2018:元件 '\*' 是從兩個不同的位置參考:\*' ' 和 ' * '。

錯誤訊息中所提及的元件會從多個位置載入。 請務必一律使用相同版本的元件。

<a name="MT2019" />

### <a name="mt2019-can-not-load-the-root-assembly-"></a>MT2019:無法載入根元件 ' * '

無法載入根元件。 請確認錯誤訊息中的路徑參考了現有的檔案, 而且它是有效的 .NET 元件。

<a name="MT202x" />

### <a name="mt202x-binding-optimizer-failed-processing-"></a>MT202x:系結優化工具`...`處理失敗。

嘗試優化產生的系結程式碼時, 發生非預期的狀況。 造成此問題的元素會在錯誤訊息中命名。 若要修正此問題, 必須在[github](https://github.com/xamarin/xamarin-macios/issues/new)的新問題中提供名為的元件 (或包含名為的類型或方法), 以及已啟用詳細資訊的完整組建記錄檔`-v -v -v -v` (也就是在**其他 mtouch 引數**中)。

最後一個數位`x`會是:
- `0`若為元件名稱, 則為
- `1`針對類型名稱;
- `3`若為方法名稱, 則為

<a name="MT2030" />

### <a name="mt2030-remove-user-resources-failed-processing-"></a>MT2030:移除使用者資源處理`...`失敗。

嘗試移除使用者資源時發生未預期的問題。 造成此問題的元件是在錯誤訊息中所命名。 若要修正此問題, 必須在[github](https://github.com/xamarin/xamarin-macios/issues/new)上的新問題中提供元件, 以及啟用詳細資訊的完整組建記錄檔 ( `-v -v -v -v`也就是在**其他 mtouch 引數**中)。

使用者資源是元件內所包含的檔案 (如資源), 必須在組建時加以解壓縮, 才能建立應用程式套件組合。 包括：

- `__monotouch_content_*`和`__monotouch_pages_*`資源; 以及
- 內嵌于系結元件內的原生程式庫;

<a name="MT2040" />

### <a name="mt2040-default-httpmessagehandler-setter-failed-processing-"></a>MT2040:預設 HttpMessageHandler setter 無法處理`...`。

嘗試設定應用程式的預設值`HttpMessageHandler`時, 發生未預期的問題。 請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出新的問題, 以及已啟用詳細資訊的完整組建記錄檔`-v -v -v -v` (亦即在**其他 mtouch 引數**中)。

<a name="MT2050" />

### <a name="mt2050-code-remover-failed-processing-"></a>MT2050:程式碼 Remover 處理`...`失敗。

嘗試從使用應用程式的 BCL 出貨移除程式碼時, 發生非預期的問題。 請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出新的問題, 以及已啟用詳細資訊的完整組建記錄檔`-v -v -v -v` (亦即在**其他 mtouch 引數**中)。

<a name="MT2060" />

### <a name="mt2060-sealer-failed-processing-"></a>MT2060:Sealer 處理`...`失敗。

嘗試密封類型或方法 (最後) 或 devirtualizing 某些方法時, 發生非預期的狀況。 造成此問題的元件是在錯誤訊息中所命名。 若要修正此問題, 必須在[github](https://github.com/xamarin/xamarin-macios/issues/new)上的新問題中提供元件, 以及啟用詳細資訊的完整組建記錄檔 ( `-v -v -v -v`也就是在**其他 mtouch 引數**中)。

<a name="MT2070" />

### <a name="mt2070-metadata-reducer-failed-processing-"></a>MT2070:中繼資料歸納器處理`...`失敗。

嘗試減少應用程式的中繼資料時, 發生非預期的問題。 造成此問題的元件是在錯誤訊息中所命名。 若要修正此問題, 必須在[github](https://github.com/xamarin/xamarin-macios/issues/new)上的新問題中提供元件, 以及啟用詳細資訊的完整組建記錄檔 ( `-v -v -v -v`也就是在**其他 mtouch 引數**中)。

<a name="MT2080" />

### <a name="mt2080-marknsobjects-failed-processing-"></a>MT2080:MarkNSObjects 處理`...`失敗。

嘗試從應用程式標記`NSObject`子類別時發生非預期的狀況。 造成此問題的元件是在錯誤訊息中所命名。 若要修正此問題, 必須在[github](https://github.com/xamarin/xamarin-macios/issues/new)上的新問題中提供元件, 以及啟用詳細資訊的完整組建記錄檔 ( `-v -v -v -v`也就是在**其他 mtouch 引數**中)。

<a name="MT2090" />

### <a name="mt2090-inliner-failed-processing-"></a>MT2090:章處理`...`失敗。

嘗試從應用程式內嵌程式碼時發生非預期的問題。 造成此問題的元件是在錯誤訊息中所命名。 若要修正此問題, 必須在[github](https://github.com/xamarin/xamarin-macios/issues/new)上的新問題中提供元件, 以及啟用詳細資訊的完整組建記錄檔 ( `-v -v -v -v`也就是在**其他 mtouch 引數**中)。

<!-- MT21xx: more linker errors -->

<!--- 2100 used by mmp -->

<a name="MT2100" />

### <a name="mt2100-smart-enum-conversion-preserver-failed-processing-"></a>MT2100:智慧列舉轉換保留無法處理`...`。

嘗試從應用程式標記智慧型列舉的轉換方法時, 發生非預期的狀況。 造成此問題的元件是在錯誤訊息中所命名。 若要修正此問題, 必須在[github](https://github.com/xamarin/xamarin-macios/issues/new)上的新問題中提供元件, 以及啟用詳細資訊的完整組建記錄檔 ( `-v -v -v -v`也就是在**其他 mtouch 引數**中)。

<a name="MT2101" />

### <a name="mt2101-cant-resolve-the-reference--referenced-from-the-method--in-"></a>MT2101:無法解析參考 '\*', 從 ' * ' 中的方法 '\*' 參考。

處理錯誤訊息中所述的方法時, 發現不正確元件參考。

造成此問題的元件是在錯誤訊息中所命名。 若要修正此問題, 必須在[github](https://github.com/xamarin/xamarin-macios/issues/new)上的新問題中提供元件, 以及啟用詳細資訊的完整組建記錄檔 ( `-v -v -v -v`也就是在**其他 mtouch 引數**中)。

<a name="MT2102" />

### <a name="mt2102-error-processing-the-method--in-the-assembly--"></a>MT2102:處理元件 '\*' 中\*的方法 ' ' 時發生錯誤: *

嘗試標記錯誤訊息中所述的方法時, 發生未預期的問題。

造成此問題的元件是在錯誤訊息中所命名。 若要修正此問題, 必須在[github](https://github.com/xamarin/xamarin-macios/issues/new)上的新問題中提供元件, 以及啟用詳細資訊的完整組建記錄檔 ( `-v -v -v -v`也就是在**其他 mtouch 引數**中)。

<a name="MT2103" />

### <a name="mt2103-error-processing-assembly--"></a>MT2103:處理元件 '\*' 時發生錯誤: *

處理元件時發生未預期的錯誤。

造成此問題的元件是在錯誤訊息中所命名。 若要修正此問題, 必須在[github](https://github.com/xamarin/xamarin-macios/issues/new)上的新問題中提供元件, 以及啟用詳細資訊的完整組建記錄檔 ( `-v -v -v -v`也就是在**其他 mtouch 引數**中)。

<a name="MT2104" />

### <a name="mm2104-unable-to-link-assembly-0-as-it-is-mixed-mode"></a>MM2104:無法連結元件 '{0}', 因為它是混合模式。

連結器無法處理混合模式元件。

請參閱 https://docs.microsoft.com/cpp/dotnet/mixed-native-and-managed-assemblies 如需有關混合模式組件。

## <a name="mt3xxx-aot-error-messages"></a>MT3xxx:AOT 錯誤訊息

<!--
 MT3xxx AOT
  MT30xx AOT (general) errors
  -->

<a name="MT3001" />

### <a name="mt3001-could-not-aot-the-assembly-"></a>MT3001:無法對元件 ' * ' 進行 AOT

這通常表示 AOT 編譯器中的 bug。 請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出新的問題, 並提供可用於重現錯誤的專案。

有時可以藉由停用專案的 iOS 組建選項中的累加組建來解決此問題 (但仍是 bug, 因此請務必回報)。

<a name="MT3002" />

### <a name="mt3002-aot-restriction-method--must-be-static-since-it-is-decorated-with-monopinvokecallback-see-developerxamarincomguidesiosadvanced_topicslimitationsreverse_callbacksiosinternalslimitationsmdreverse-callbacks"></a>MT3002:AOT 限制:方法 ' * ' 必須是靜態的, 因為它是以 [MonoPInvokeCallback] 裝飾。 請參閱[developer.xamarin.com/guides/ios/advanced_topics/limitations/#Reverse_Callbacks](~/ios/internals/limitations.md#reverse-callbacks)

此錯誤訊息來自 AOT 編譯器。

<a name="MT3003" />

### <a name="mt3003-conflicting---debug-and---llvm-options-soft-debugging-is-disabled"></a>MT3003:衝突--debug 和--llvm 選項。 已停用軟性調試。

啟用 LLVM 時, 不支援調試。 如果您需要對應用程式進行 debug 錯, 請先停用 LLVM。

<a name="MT3004" />

### <a name="mt3004-could-not-aot-the-assembly--because-it-doesnt-exist"></a>MT3004:無法對元件 ' * ' 進行 AOT, 因為它不存在。

<a name="MT3005" />

### <a name="mt3005-the-dependency--of-the-assembly--was-not-found-please-review-the-projects-references"></a>MT3005:找不到\*元件 '\*' 的相依性 ' '。 請參閱專案的參考。

當元件參考平臺元件的另一個版本 (通常是 .NET 4 版本的 mscorlib.dll) 時, 通常就會發生這種情況。

這不受支援, 而且可能無法正確地建立或執行 (元件可能會使用來自 Xamarin iOS 版本所沒有的 .NET 4 版 mscorlib.dll 的 API)。

<a name="MT3006" />

### <a name="mt3006-could-not-compute-a-complete-dependency-map-for-the-project-this-will-result-in-slower-build-times-because-xamarinios-cant-properly-detect-what-needs-to-be-rebuilt-and-what-does-not-need-to-be-rebuilt-please-review-previous-warnings-for-more-details"></a>MT3006:無法計算專案的完整相依性對應。 這會導致較慢的組建時間, 因為 Xamarin 無法正確地偵測需要重建的內容 (以及不需要重建的內容)。 如需詳細資訊, 請參閱先前的警告。

 適當地建立或執行 (元件可能會使用來自 Xamarin iOS 版本所沒有的 .NET 4 版 mscorlib.dll 的 API)。

<a name="MT3007" />

### <a name="mt3007-debug-info-files-mdb-will-not-be-loaded-when-llvm-is-enabled"></a>MT3007:啟用 llvm 時, 將不會載入 Debug info 檔案 (* .mdb)。

<a name="MT3008" />

### <a name="mt3008-bitcode-support-requires-the-use-of-the-llvm-aot-backend---llvm"></a>MT3008:Bitcode 支援需要使用 LLVM AOT 後端 (--LLVM)

Bitcode 支援需要使用 LLVM AOT 後端 (--LLVM)。

請停用 Bitcode 支援或啟用 LLVM。

<!--- 3009 used by mmp -->
<!--- 3010 used by mmp -->

## <a name="mt4xxx-code-generation-error-messages"></a>MT4xxx:程式碼產生錯誤訊息

### <a name="mt40xx-main"></a>MT40xx:主要

<!--
 MT4xxx code generation
  MT40xx main.m
  -->

<a name="MT4001" />

### <a name="mt4001-the-main-template-could-not-be-expanded-to-"></a>MT4001:無法將主要範本展開為`*`。

產生`main.m`時發生錯誤。 請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出新問題。

<a name="MT4002" />

### <a name="mt4002-failed-to-compile-the-generated-code-for-pinvoke-methods-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4002:無法編譯 P/Invoke 方法所產生的程式碼。 請提出錯誤報告在 http://bugzilla.xamarin.com

無法編譯 P/Invoke 方法所產生的程式碼。 請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出新問題。

### <a name="mt41xx-registrar"></a>MT41xx:登錄器

<!--
  MT41xx registrar.m
  -->

<a name="MT4101" />

### <a name="mt4101-the-registrar-cannot-build-a-signature-for-type-"></a>MT4101:註冊機構無法建立類型`*`的簽章。

在匯出的 API 中找到類型, 執行時間不知道如何在目標-C 中進行封送處理。

如果您認為 Xamarin 應支援問題類型, 請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出增強要求。

<a name="MT4102" />

### <a name="mt4102-the-registrar-found-an-invalid-type--in-signature-for-method--use--instead"></a>MT4102:註冊機構在方法`*` `*`的簽章中發現不正確類型。 請改用 `*`。

目前只有一種類型:System. DateTime。 請改用目標-C 對等 (NSDate)。

<a name="MT4103" />

### <a name="mt4103-the-registrar-found-an-invalid-type--in-signature-for-method--the-type-implements-inativeobject-but-does-not-have-a-constructor-that-takes-two-intptr-bool-arguments"></a>MT4103:註冊機構在方法`*` `*`的簽章中發現不正確類型:型別會執行 INativeObject, 但不會有接受兩個 (IntPtr, bool) 引數的函式

當註冊機構在簽章中遇到具有所述特性的類型時, 就會發生這種情況。 註冊機構可能需要建立類型的新實例, 在此情況下, 它需要具有 (IntPtr, bool) 簽章的函式-第一個引數 (IntPtr) 指定 managed 控制碼, 而當呼叫者手上有原生的擁有權時, 則是第二個handle (如果此值為 false, 將會在物件上呼叫 ' retain ')。

<a name="MT4104" />

### <a name="mt4104-the-registrar-cannot-marshal-the-return-value-for-type--in-signature-for-method-"></a>MT4104:註冊機構無法封送處理方法`*` `*`的簽章中類型的傳回值。

在匯出的 API 中找到類型, 執行時間不知道如何在目標-C 中進行封送處理。

如果您認為 Xamarin 應支援問題類型, 請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出增強要求。

<a name="MT4105" />

### <a name="mt4105-the-registrar-cannot-marshal-the-parameter-of-type--in-signature-for-method-"></a>MT4105:註冊機構無法封送處理方法`*` `*`的簽章中類型的參數。

如果您認為 Xamarin 應支援問題類型, 請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出增強要求。

<a name="MT4106" />

### <a name="mt4106-the-registrar-cannot-marshal-the-return-value-for-structure--in-signature-for-method-"></a>MT4106:註冊機構無法封送處理方法`*` `*`的簽章中結構的傳回值。

在匯出的 API 中找到類型, 執行時間不知道如何在目標-C 中進行封送處理。

如果您認為 Xamarin 應支援問題類型, 請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出增強要求。

<a name="MT4107" />

### <a name="mt4107-the-registrar-cannot-marshal-the-parameter-of-type--in-signature-for-method-"></a>MT4107:註冊機構無法封送處理方法`*` `+`的簽章中類型的參數。

在匯出的 API 中找到類型, 執行時間不知道如何在目標-C 中進行封送處理。

如果您認為 Xamarin 應支援問題類型, 請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出增強要求。

<a name="MT4108" />

### <a name="mt4108-the-registrar-cannot-get-the-objectivec-type-for-managed-type-"></a>MT4108:註冊機構無法取得 managed 類型`*`的 ObjectiveC 類型。

在匯出的 API 中找到類型, 執行時間不知道如何在目標-C 中進行封送處理。

如果您認為 Xamarin 應支援問題類型, 請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出增強要求。

<a name="MT4109" />

### <a name="mt4109-failed-to-compile-the-generated-registrar-code-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4109:無法編譯產生的註冊機構代碼。 請提出錯誤報告在 http://bugzilla.xamarin.com

無法為註冊機構編譯產生的程式碼。 組建記錄檔會包含原生編譯器的輸出, 說明程式碼不會編譯的原因。

這在 Xamarin 中一律是 bug;請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出您的專案或測試案例的新問題。

<a name="MT4110" />

### <a name="mt4110-the-registrar-cannot-marshal-the-out-parameter-of-type--in-signature-for-method-"></a>MT4110:註冊機構無法封送處理方法`*` `*`的簽章中類型的 out 參數。

<a name="MT4111" />

### <a name="mt4111-the-registrar-cannot-build-a-signature-for-type--in-method-"></a>MT4111:註冊機構無法在方法`*` `*`中建立類型的簽章。

<a name="MT4112" />

### <a name="mt4112-the-registrar-found-an-invalid-type--registering-generic-types-with-objective-c-is-not-supported-and-may-lead-to-random-behavior-andor-crashes-for-backwards-compatibility-with-older-versions-of-xamarinios-it-is-possible-to-ignore-this-error-by-passing---unsupported--enable-generics-in-registrar-as-an-additional-mtouch-argument-in-the-projects-ios-build-options-page-see-developerxamarincomguidesiosadvanced_topicsregistrariosinternalsregistrarmd-for-more-information"></a>MT4112:註冊機構發現不正確類型`*`。 不支援向目標-C 註冊泛型型別, 而且可能會導致隨機行為和/或損毀 (以提供與舊版 Xamarin 的回溯相容性。 iOS 可以藉由傳遞`--unsupported--enable-generics-in-registrar`為其他 mtouch 來忽略此錯誤專案的 [iOS 組建選項] 頁面中的引數。 如需詳細資訊, 請參閱[developer.xamarin.com/guides/ios/advanced_topics/registrar](~/ios/internals/registrar.md) )。

<a name="MT4113" />

### <a name="mt4113-the-registrar-found-a-generic-method--exporting-generic-methods-is-not-supported-and-will-lead-to-random-behavior-andor-crashes"></a>MT4113:註冊機構找到泛型方法: '\*.\*'。 不支援匯出泛型方法, 而且會導致隨機行為和/或當機。

<a name="MT4114" />

### <a name="mt4114-unexpected-error-in-the-registrar-for-the-method----please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4114:方法 '\*.\*' 的註冊機構發生未預期的錯誤-請在上提出錯誤報表 http://bugzilla.xamarin.com

<a name="MT4116" />

### <a name="mt4116-could-not-register-the-assembly--"></a>MT4116:無法註冊元件 ' * ': *

<a name="MT4117" />

### <a name="mt4117-the-registrar-found-a-signature-mismatch-in-the-method----the-selector-indicates-the-method-takes--parameters-while-the-managed-method-has--parameters"></a>MT4117:註冊機構在方法 ' *.* ' 中發現簽章不相符-此選取器表示方法接受 * 參數, 而 managed 方法具有 * 參數。

<a name="MT4118" />

### <a name="mt4118-cannot-register-two-managed-types--and--with-the-same-native-name-"></a>MT4118:無法使用相同的原生名稱\*(' *\*') 來註冊兩個 managed 類型 (' ' 和 ' ')。

<a name="MT4119" />

### <a name="mt4119-could-not-register-the-selector--of-the-member--because-the-selector-is-already-registered-on-a-different-member"></a>MT4119:因為選取器已經在不同\*的成員上註冊,\*所以無法註冊成員 '. * ' 的選取器 ' '。

<a name="MT4120" />

### <a name="mt4120-the-registrar-found-an-unknown-field-type--in-field--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4120:註冊機構在欄位 '\* \*. * ' 中找到未知的欄位類型 ' '。 請提出錯誤報告在 http://bugzilla.xamarin.com

此錯誤表示在 Xamarin. iOS 中發生錯誤。 請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出新問題。

<a name="MT4121" />

### <a name="mt4121-cannot-use-gccg-to-compile-the-generated-code-from-the-static-registrar-when-using-the-accounts-framework-the-header-files-provided-by-apple-used-during-the-compilation-require-clang-either-use-clang---compilerclang-or-the-dynamic-registrar---registrardynamic"></a>MT4121:當使用帳戶架構 (在編譯期間使用的 Apple 所提供的標頭檔需要 Clang) 時, 無法使用 GCC/G + + 來編譯靜態註冊機構所產生的程式碼。 請使用 Clang (--編譯器: Clang) 或動態註冊機構 (--註冊機構: dynamic)。

<a name="MT4122" />

### <a name="mt4122-cannot-use-the-clang-compiler-provided-in-the--sdk-to-compile-the-generated-code-from-the-static-registrar-when-non-ascii-type-names--are-present-in-the-application-either-use-gccg---compilergccg-the-dynamic-registrar---registrardynamic-or-a-newer-sdk"></a>MT4122:無法使用中提供的 Clang 編譯器 *。* 當應用程式中出現非 ASCII 型別名稱 (' * ') 時, 用來編譯靜態註冊機構所產生之程式碼的 SDK。 請使用 GCC/G + + (--編譯器: gcc | G + +)、動態註冊機構 (--註冊機構: dynamic) 或較新的 SDK。

<a name="MT4123" />

### <a name="mt4123-the-type-of-the-variadic-parameter-in-the-variadic-function--must-be-systemintptr"></a>MT4123:Variadic 函數 ' * ' 中的 variadic 參數類型必須是 system.string。

<a name="MT4124" />

### <a name="mt4124-invalid--found-on--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4124:' * ' 上找到不正確 *。 請提出錯誤報告在 http://bugzilla.xamarin.com

此錯誤表示在 Xamarin. iOS 中發生錯誤。 請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出新問題。

<a name="MT4125" />

### <a name="mt4125-the-registrar-found-an-invalid-type--in-signature-for-method--the-interface-must-have-a-protocol-attribute-specifying-its-wrapper-type"></a>MT4125:註冊機構在方法 '\* \*' 的簽章中發現不正確類型 ' ':介面必須有指定其包裝函式類型的通訊協定屬性。

<a name="MT4126" />

### <a name="mt4126-cannot-register-two-managed-protocols--and--with-the-same-native-name-"></a>MT4126:無法以相同的原生名稱\*(' *\*') 註冊兩個受控通訊協定 (' ' 和 ' ')。

<a name="MT4127" />

### <a name="mt4127-cannot-register-more-than-one-interface-method-for-the-method--which-is-implementing-"></a>MT4127:無法為方法 '\*' 註冊一個以上的介面方法 (這是實作為 '\*')。

<a name="MT4128" />

### <a name="mt4128-the-registrar-found-an-invalid-generic-parameter-type--in-the-method--the-generic-parameter-must-have-an-nsobject-constraint"></a>MT4128:註冊機構在方法 '\* \*' 中找到不正確泛型參數類型 ' '。 泛型參數必須有 ' NSObject ' 條件約束。

<a name="MT4129" />

### <a name="mt4129-the-registrar-found-an-invalid-generic-return-type--in-the-method--the-generic-return-type-must-have-an-nsobject-constraint"></a>MT4129:註冊機構在方法 '\* \*' 中找到不正確泛型傳回型別 ' '。 泛型傳回型別必須有 ' NSObject ' 條件約束。

<a name="MT4130" />

### <a name="mt4130-the-registrar-cannot-export-static-methods-in-generic-classes-"></a>MT4130:註冊機構無法匯出泛型類別 (' * ') 中的靜態方法。

<a name="MT4131" />

### <a name="mt4131-the-registrar-cannot-export-static-properties-in-generic-classes-"></a>MT4131:註冊機構無法匯出泛型類別 ('\*.\*') 中的靜態屬性。

<a name="MT4132" />

### <a name="mt4132-the-registrar-found-an-invalid-generic-return-type--in-the-property--the-return-type-must-have-an-nsobject-constraint"></a>MT4132:註冊機構在屬性 '\* \*' 中找到不正確泛型傳回型別 ' '。 傳回型別必須有 ' NSObject ' 條件約束。

<a name="MT4133" />

### <a name="mt4133-cannot-construct-an-instance-of-the-type--from-objective-c-because-the-type-is-generic-runtime-exception"></a>MT4133:無法從目標-C 建立類型 ' * ' 的實例, 因為該類型是泛型。 [執行時間例外狀況]

<a name="MT4134" />

### <a name="mt4134-your-application-is-using-the--framework-which-isnt-included-in-the-ios-sdk-youre-using-to-build-your-app-this-framework-was-introduced-in-ios--while-youre-building-with-the-ios--sdk-please-select-a-newer-sdk-in-your-apps-ios-build-options"></a>MT4134:您的應用程式使用的是 ' * ' 架構, 其未包含在您用來建立應用程式的 iOS SDK 中 (此架構是在使用 iOS * SDK 建立時, 在 iOS * 中引進)。請在您應用程式的 iOS 組建選項中選取較新的 SDK。

<a name="MT4135" />

### <a name="mt4135-the-member--has-an-export-attribute-that-doesnt-specify-a-selector-a-selector-is-required"></a>MT4135:成員 '\*.\*' 有未指定選取器的 Export 屬性。 需要選取器。

<a name="MT4136" />

### <a name="mt4136-the-registrar-cannot-marshal-the-parameter-type--of-the-parameter--in-the-method-"></a>MT4136:註冊機構無法封送處理方法 '\* \*. * ' 中參數\*' ' 的參數類型 ' '

<!-- MT4137 is unused -->

<a name="MT4138" />

### <a name="mt4138-the-registrar-cannot-marshal-the-property-type--of-the-property-"></a>MT4138:註冊機構無法封送處理屬性 '\* \*. * ' 的屬性類型 ' '。

<a name="MT4139" />

### <a name="mt4139-the-registrar-cannot-marshal-the-property-type--of-the-property--properties-with-the-connect-attribute-must-have-a-property-type-of-nsobject-or-a-subclass-of-nsobject"></a>MT4139:註冊機構無法封送處理屬性 '\* \*. * ' 的屬性類型 ' '。 具有 [連接] 屬性的屬性, 其屬性類型必須為 NSObject (或 NSObject 的子類別)。

<a name="MT4140" />

### <a name="mt4140-the-registrar-found-a-signature-mismatch-in-the-method----the-selector-indicates-the-variadic-method-takes--parameters-while-the-managed-method-has--parameters"></a>MT4140:註冊機構在方法 ' *.* ' 中發現簽章不相符-選取器指出 variadic 方法接受 * 參數, 而 managed 方法具有 * 參數。

<a name="MT4141" />

### <a name="mt4141-cannot-register-the-selector--on-the-member--because-xamarinios-implicitly-registers-this-selector"></a>MT4141:無法在成員 '\* \*' 上註冊選取器 ' ', 因為 Xamarin 會隱含地註冊此選取器。

當將架構類型子類別化, 並嘗試執行 ' retain '、' release ' 或 ' dealloc ' 方法時, 就會發生這種情況:

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

不過, 如果類別不是架構類型的第一個子類別, 就可以覆寫這些方法:

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

在此情況下, Xamarin 會覆`retain`寫`release`類別`dealloc`上的`MyNSObject`和, 而且不會發生衝突。

<a name="MT4142" />

### <a name="mt4142-failed-to-register-the-type-"></a>MT4142:無法註冊類型 ' * '。

<a name="MT4143" />

### <a name="mt4143-the-objectivec-class--could-not-be-registered-it-does-not-seem-to-derive-from-any-known-objectivec-class-including-nsobject"></a>MT4143:無法註冊 ObjectiveC 類別 ' * ', 它似乎不是從任何已知的 ObjectiveC 類別 (包括 NSObject) 衍生而來。

<a name="MT4144" />

### <a name="mt4144-cannot-register-the-method--since-it-does-not-have-an-associated-trampoline-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4144:無法註冊方法 ' * ', 因為它沒有相關聯的 trampoline。 請提出錯誤報告在 http://bugzilla.xamarin.com 。

這表示在 Xamarin. iOS 中的錯誤。 請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出新問題。

<a name="MT4145" />

### <a name="mt4145-invalid-enum--enums-with-the-native-attribute-must-have-a-underlying-enum-type-of-either-long-or-ulong"></a>MT4145:不正確列舉 ' * ': 具有 [Native] 屬性的列舉必須有 ' long ' 或 ' ulong ' 的基礎列舉類型。

<a name="MT4146" />

### <a name="mt4146-the-name-parameter-of-the-registrar-attribute-on-the-class---contains-an-invalid-character--"></a>MT4146:類別 '\*' ('\*') 上註冊機構屬性的名稱參數包含不正確字元: '\*' (\*)。

Objectice-C 類別的名稱不能包含空白字元, 這表示對應的 managed `Register`類別`Name`上的屬性不能包含空白字元。

請確認錯誤訊息`Register`中所述 managed 類別上的屬性未包含任何空白字元。

<a name="MT4147" />

### <a name="mt4147-detected-a-protocol-inheriting-from-the-jsexport-protocol-while-using-the-dynamic-registrar-it-is-not-possible-to-export-protocols-to-javascriptcore-dynamically-the-static-registrar-must-be-used-add---registrarstatic-to-the-additional-mtouch-arguments-in-the-projects-ios-build-options-to-select-the-static-registrar"></a>MT4147:在使用動態註冊機構時, 偵測到從 JSExport 通訊協定繼承的通訊協定。 您無法以動態方式將通訊協定匯出至 JavaScriptCore;必須使用靜態註冊機構 (新增 '--註冊程式: 靜態至專案的 iOS 組建選項中的其他 mtouch 引數), 以選取靜態註冊機構。

<a name="MT4148" />

### <a name="mt4148-the-registrar-found-a-generic-protocol--exporting-generic-protocols-is-not-supported"></a>MT4148:註冊機構找到一般通訊協定: ' * '。 不支援匯出一般通訊協定。

<a name="MT4149" />

### <a name="mt4149-cannot-register-the-method--because-the-type-of-the-first-parameter--does-not-match-the-category-type-"></a>MT4149:無法註冊方法\*'.\*', 因為第一個參數 ('\*') 的類型與類別目錄類型 (\*' ') 不相符。

<a name="MT4150" />

### <a name="mt4150-cannot-register-the-type--because-the-type-property--in-its-category-attribute-does-not-inherit-from-nsobject"></a>MT4150:無法註冊類型 '\*', 因為其 Category 屬性中的類型屬性 ('\*') 不是繼承自 NSObject。

<a name="MT4151" />

### <a name="mt4151-cannot-register-the-type--because-the-type-property-in-its-category-attribute-isnt-set"></a>MT4151:無法註冊類型 ' * ', 因為未設定其 Category 屬性中的 Type 屬性。

<a name="MT4152" />

### <a name="mt4152-cannot-register-the-type--as-a-category-because-it-implements-inativeobject-or-subclasses-nsobject"></a>MT4152:無法將類型 ' * ' 註冊為分類, 因為它會執行 INativeObject 或子類別 NSObject。

<a name="MT4153" />

### <a name="mt4153-cannot-register-the-type--as-a-category-because-its-generic"></a>MT4153:無法將類型 '\*' 註冊為分類, 因為它是泛型。

<a name="MT4154" />

### <a name="mt4154-cannot-register-the-method--as-a-category-method-because-its-generic"></a>MT4154:無法將方法 '\*' 註冊為分類方法, 因為它是泛型。

<a name="MT4155" />

### <a name="mt4155-cannot-register-the-method--with-the-selector--as-a-category-method-on--because-the-objective-c-already-has-an-implementation-for-this-selector"></a>MT4155:無法以選取器 '\* \*' 註冊方法 ' ' 做為 ' * ' 上的分類方法, 因為目標-C 已有此選取器的執行。

<a name="MT4156" />

### <a name="mt4156-cannot-register-two-categories--and--with-the-same-native-name-"></a>MT4156:無法以相同的原生\*名稱 ('\** ') 註冊兩個分類 (' ' 和 ' ')。

<a name="MT4157" />

### <a name="mt4157-cannot-register-the-category-method--because-at-least-one-parameter-is-required-and-its-type-must-match-the-category-type-"></a>MT4157:無法註冊類別方法 '\*', 因為至少需要一個參數 (且其類型必須符合類別目錄類型 '\*')

<a name="MT4158" />

### <a name="mt4158-cannot-register-the-constructor--in-the-category--because-constructors-in-categories-are-not-supported"></a>MT4158:無法在類別目錄中註冊此函式 *, 因為不支援類別目錄中的函數。

<a name="MT4159" />

### <a name="mt4159-cannot-register-the-method--as-a-category-method-because-category-methods-must-be-static"></a>MT4159:無法將方法 ' * ' 註冊為分類方法, 因為類別方法必須是靜態的。

<a name="MT4160" />

### <a name="mt4160-invalid-character---found-in-selector--for-"></a>MT4160:\*在 '\* \*' 的選取器 ' ' 中找到不正確字元 ' ' ()。\*

<a name="MT4161" />

### <a name="mt4161-the-registrar-found-an-unsupported-structure--all-fields-in-a-structure-must-also-be-structures-field--with-type-2-is-not-a-structure"></a>MT4161:註冊機構發現不支援的結構\*' ':結構中的所有欄位也必須是結構 (類型為\*'{2}' 的欄位 ' ' 不是結構)。

註冊機構找到具有不支援欄位的結構。

結構中對目標-C 公開的所有欄位也必須是結構 (而非類別)。

<a name="MT4162" />

### <a name="mt4162-the-type--used-as--2-is-not-available-in---it-was-introduced-in---please-build-with-a-newer--sdk-usually-done-by-using-the-most-recent-version-of-xcode"></a>MT4162:類型 '\*' (用為 * {2}) 不適用於 * * (這是在 * * 中引進)。請\*使用較新的 * SDK (通常是使用最新版本的 Xcode 來完成)。

註冊機構發現不包含在目前 SDK 中的類型。

請升級 Xcode。

<a name="MT4163" />

### <a name="mt4163-internal-error-in-the-registrar--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4163:註冊機構 (*) 發生內部錯誤。 請提出錯誤報告在 http://bugzilla.xamarin.com

此錯誤表示在 Xamarin. iOS 中發生錯誤。 請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出新問題。

<a name="MT4164" />

### <a name="mt4164-cannot-export-the-property--because-its-selector--is-an-objective-c-keyword-please-use-a-different-name"></a>MT4164:無法匯出屬性 '\*', 因為其選取器 '\*' 是目標-C 關鍵字。 請使用不同的名稱。

有問題的屬性之選取器不是有效的目標 C 識別碼。

請使用有效的目標-C 識別碼作為選取器。

<a name="MT4165" />

### <a name="mt4165-the-registrar-couldnt-find-the-type-systemvoid-in-any-of-the-referenced-assemblies"></a>MT4165:註冊機構在任何參考的元件中找不到類型 ' System.object '。

此錯誤最可能表示在 Xamarin 中發生錯誤。 請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出新問題。

<a name="MT4166" />

### <a name="mt4166-cannot-register-the-method--because-the-signature-contains-a-type--that-isnt-a-reference-type"></a>MT4166:無法註冊方法 '\*', 因為簽章包含不是參考型別的類型 (\*)。

這通常表示在 Xamarin. iOS 中有錯誤。 請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出新問題。

<a name="MT4167" />

### <a name="mt4167-cannot-register-the-method--because-the-signature-contains-a-generic-type--with-a-generic-argument-type-that-isnt-an-nsobject-subclass-"></a>MT4167:無法註冊方法 '\*', 因為簽章包含的泛型型別 (\*) 具有不是 NSObject 子類別 (*) 的泛型引數類型。

這通常表示在 Xamarin. iOS 中有錯誤。 請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出新問題。

<a name="MT4168" />

### <a name="mt4168-cannot-register-the-type-managed_name-because-its-objective-c-name-exported_name-is-an-objective-c-keyword-please-use-a-different-name"></a>MT4168:無法註冊類型 ' {受控\_名稱} ', 因為它的目標-c 名稱 ' {已匯出\_名稱} ' 是目標-c 關鍵字。 請使用不同的名稱。

有問題之類型的目標-C 名稱不是有效的目標-C 識別碼。

請使用有效的目標-C 識別碼。

<a name="MT4169" />

### <a name="mt4169-failed-to-generate-a-pinvoke-wrapper-for-method-message"></a>MT4169:無法產生 {method} 的 P/Invoke 包裝函式: {message}

Xamarin 無法為所述的產生 P/Invoke 包裝函式。
請檢查回報的錯誤訊息, 以瞭解根本原因。

<a name="MT4170" />

### <a name="mt4170-the-registrar-cant-convert-from-managed-type-to-native-type-for-the-return-value-in-the-method-method"></a>MT4170:註冊機構無法將方法 {method} 中的傳回值, 從 ' {managed type} ' 轉換為 ' {native type} '。

請參閱錯誤<a href="#MT4172">MT4172</a>的描述。

<a name="MT4171" />

### <a name="mt4171-the-bindas-attribute-on-the-member-member-is-invalid-the-bindas-type-type-is-different-from-the-property-type-type"></a>MT4171:成員 {member} 上的 BindAs 屬性無效: BindAs 類型 {type} 與屬性類型 {type} 不同。

請確定 BindAs 屬性中的類型符合它所附加之成員的類型。

<a name="MT4172" />

### <a name="mt4172-the-registrar-cant-convert-from-native-type-to-managed-type-for-the-parameter-parameter-name-in-the-method-method"></a>MT4172:註冊機構無法在方法 {method} 中, 將參數 ' {parameter name} ' 的 ' {native type} ' 轉換成 ' {managed type} '。

註冊機構不支援在提及的類型之間進行轉換。

這是 Xamarin 中的錯誤。如果有問題的 API 是由 Xamarin 所提供, 則為 iOS。 請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出新問題。

如果您在開發原生程式庫的系結專案時遇到此情況, 我們會開放加入新類型組合的支援。 如果是這種情況, 請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上使用測試案例提出增強要求, 我們將會進行評估。

## <a name="mt5xxx-gcc-and-toolchain-error-messages"></a>MT5xxx:GCC 和工具鏈錯誤訊息

### <a name="mt51xx-compilation"></a>MT51xx:編譯

<!--
 MT5xxx GCC and toolchain
  MT51xx compilation
  -->

<a name="MT5101" />

### <a name="mt5101-missing--compiler-please-install-xcode-command-line-tools-component"></a>MT5101:遺漏 ' * ' 編譯器。 請安裝 Xcode ' 命令列工具 ' 元件

<a name="MT5102" />

### <a name="mt5102-failed-to-assemble-the-file--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5102:無法組合檔案 ' * '。 請提出錯誤報告在 http://bugzilla.xamarin.com

<a name="MT5103" />

### <a name="mt5103-failed-to-compile-the-file--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5103:無法編譯檔案 ' * '。 請提出錯誤報告在 http://bugzilla.xamarin.com

<a name="MT5104" />

### <a name="mt5104-could-not-find-neither-the--nor-the--compiler-please-install-xcode-command-line-tools-component"></a>MT5104:找不到 '\*' 或 '\*' 編譯器。 請安裝 Xcode ' 命令列工具 ' 元件

<!-- 5105 is used by mmp -->

<a name="MT5106" />

### <a name="mt5106-could-not-compile-the-files--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5106:無法編譯檔案 ' * '。 請提出錯誤報告在 http://bugzilla.xamarin.com

這通常表示在 Xamarin. iOS 中的錯誤;請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出新問題。

### <a name="mt52xx-linking"></a>MT52xx:連結

<!--
  MT52xx linking
  -->

<a name="MT5201" />

### <a name="mt5201-native-linking-failed-please-review-the-build-log-and-the-user-flags-provided-to-gcc-"></a>MT5201:原生連結失敗。 請檢查提供給 gcc: * 的組建記錄檔和使用者旗標

<a name="MT5202" />

### <a name="mt5202-native-linking-failed-please-review-the-build-log"></a>MT5202:原生連結失敗。 請檢查組建記錄檔。

<a name="MT5203" />

### <a name="mt5203-native-linking-warning-"></a>MT5203:原生連結警告: *

<!--- 5204-5208 are not used -->

<a name="MT5209" />

### <a name="mt5209-native-linking-error-"></a>MT5209:原生連結錯誤: *

<a name="MT5210" />

### <a name="mt5210-native-linking-failed-undefined-symbol--please-verify-that-all-the-necessary-frameworks-have-been-referenced-and-native-libraries-are-properly-linked-in"></a>MT5210:原生連結失敗, 未定義的符號: *。 請確認已參考所有必要的架構, 且已在中正確連結原生程式庫。

當原生連結器找不到某處參考的符號時, 就會發生這種情況。 有幾個原因可能會發生這種情況:

- 協力廠商系結需要架構, 但系結不會在其`[LinkWith]`屬性中指定此項。 適用
  - 如果您是協力廠商系結的作者, 或具有其來源的存取權, 請修改系結`[LinkWith]`的屬性, 以包含所需的架構:

    ```csharp
    [LinkWith ("mylib.a", Frameworks = "SystemConfiguration")]
    ```

  - 如果您無法修改協力廠商系結, 您可以藉由傳遞`-gcc_flags '-framework SystemFramework'`至來`mtouch`手動連結所需的架構 (這是透過在專案的 [iOS 組建選項] 頁面中修改其他 mtouch 引數來完成的。 請記住, 這必須針對每個專案設定進行。
- 在某些情況下, managed 系結是由數個原生程式庫所組成, 而全部都必須包含在系結中。 每個系結專案中都可以有一個以上的原生程式庫, 因此解決方案只會將所有必要的原生程式庫加入至系結專案。</li>
- Managed 系結是指不存在於原生程式庫中的原生符號。
    這通常發生于系結已存在一段時間, 而且在該時間內已修改機器碼, 以便在未更新系結的情況下, 將特定的原生類別移除或重新命名。
- P/Invoke 是指不存在的原生符號。 從 Xamarin. iOS 7.4 開始, 將會針對此案例回報<a href="#MT5214">MT5214</a>錯誤 (如需詳細資訊, 請參閱 MT5214)。
- 協力廠商系結/程式庫是使用C++建立的, 但是系結不會在其`[LinkWith]`屬性中指定這個。 這通常相當容易辨識, 因為符號的C++符號不變 (一個常見的範例是`__ZNKSt9exception4whatEv`)。
  - 如果您是協力廠商系結的作者, 或具有其來源的存取權, 請修改系結`[LinkWith]`的屬性來`IsCxx`設定旗標:

    ```csharp
    [LinkWith ("mylib.a", IsCxx = true)]
    ```

  - 如果您無法修改協力廠商系結, 或使用協力廠商程式庫手動連結, 您可以藉由傳遞`-cxx`至 mtouch 來設定對等旗標 (這是藉由在專案的 [iOS 組建選項] 頁面中修改其他 mtouch 引數來完成). 請記住, 這必須針對每個專案設定進行。

<a name="MT5211" />

### <a name="mt5211-native-linking-failed-undefined-objective-c-class--the-symbol--could-not-be-found-in-any-of-the-libraries-or-frameworks-linked-with-your-application"></a>MT5211:原生連結失敗, 未定義的目標- \*C 類別:。 在與您\*的應用程式連結的任何程式庫或架構中, 找不到符號 ' '。

當原生連結器找不到在某處參考的目標-C 類別時, 就會發生這種情況。 有幾個原因可能會發生這種情況: 與[MT5210](#MT5210)和此外:

- 協力廠商系結系結了目標 C 通訊協定, 但未使用其 api 定義`[Protocol]`中的屬性為其加上批註。 適用
  - 新增遺漏`[Protocol]`的屬性:

    ```csharp
    [BaseType (typeof (NSObject))]
    [Protocol] // Add this
    public interface MyProtocol
    {
    }
    ```

<a name="MT5212" />

### <a name="mt5212-native-linking-failed-duplicate-symbol-"></a>MT5212:原生連結失敗, 重複的符號: *。

這會在原生連結器在所有原生程式庫之間遇到重複符號時發生。 發生此錯誤時, 可能會有一或多個[MT5213](#MT5213)錯誤, 其中包含每個出現符號的位置。 此錯誤的可能原因:

- 相同的原生程式庫會包含兩次。
- 有兩個不同的原生程式庫會定義相同的符號。
- 未正確建立原生程式庫, 且包含相同的符號多次。
  您可以從終端機使用下列命令集來確認這一點 (根據您所建立的架構, 以 x86_64/armv7/armv7s/arm64 取代 i386):

  ```
  # Native libraries are usually fat libraries, containing binary code for
  # several architectures in the same file. First we extract the binary
  # code for the architecture we're interested in.
  lipo libNative.a -thin i386 -output libNative.i386.a

  # Now query the native library for the duplicated symbol.
  nm libNative.i386.a | fgrep 'SYMBOL'

  # You can also list the object files inside the native library.
  # In most cases this will reveal duplicated object files.
  ar -t libNative.i386.a
  ```

  有幾個可能的方法可以修正此問題:

  - 要求原生程式庫的提供者修正此問題, 並提供更新的版本。
  - 藉由移除額外的物件檔案 (這僅適用于問題是否為重複的物件檔案) 來修正

  ```
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
  ```

  - 要求連結器移除未使用的程式碼。 如果符合下列所有條件, 則 Xamarin 會自動執行此動作:
    - 所有協力廠商系結`[LinkWith]`的屬性都已啟用 SmartLink:

      ```csharp
      [assembly: LinkWith ("libNative.a", SmartLink = true)]
      ```

    - [ `-gcc_flags`否] 會傳遞給 mtouch (在專案的 iOS 組建選項的 [其他 mtouch 引數] 欄位中)。
    - 您也可以在專案的 iOS 組建選項中新增`-gcc_flags -dead_strip`至其他 mtouch 引數, 以直接要求連結器移除未使用的程式碼。

<a name="MT5213" />

### <a name="mt5213-duplicate-symbol-in--location-related-to-previous-error"></a>MT5213:中的符號重複: * (與先前錯誤相關的位置)

此錯誤只會與[MT5212](#MT5212)一起回報。 如需詳細資訊, 請參閱[MT5212](#MT5212) 。

<a name="MT5214" />

### <a name="mt5214-native-linking-failed-undefined-symbol--this-symbol-was-referenced-the-managed-member--please-verify-that-all-the-necessary-frameworks-have-been-referenced-and-native-libraries-linked"></a>MT5214:原生連結失敗, 未定義的符號: *。 這個符號已參考 managed 成員 *。 請確認已參考所有必要的架構, 且已連結原生程式庫。

當 managed 程式碼包含不存在之原生方法的 P/Invoke 時, 就會回報此錯誤。 例如：

```csharp
using System.Runtime.InteropServices;
class MyImports {
   [DllImport ("__Internal")]
   public static extern void MyInexistentFunc ();
}
```

有幾個可能的解決方案:

- 從原始程式碼中移除有問題的 P/Invoke。
- 針對所有元件啟用 managed 連結器 (這是在專案的 iOS 組建選項中完成, 方法是將 [連結器行為] 設定為 [所有元件])。 這可有效地移除您不是從應用程式使用的所有 P/Invoke (自動, 而不是像先前的點手動執行)。 缺點是, 這會使您的模擬器組建變得更慢, 而且如果應用程式正在使用反映, 它可能會中斷您的應用程式-連結器的詳細資訊可在[這裡](~/ios/deploy-test/linker.md)找到)
- 建立第二個原生程式庫, 其中包含遺漏的原生符號。 請注意, 這只是一個因應措施 (如果您嘗試呼叫這些函式, 您的應用程式將會損毀)。

<a name="MT5215" />

### <a name="mt5215-references-to--might-require-additional--frameworkxxx-or--lxxx-instructions-to-the-native-linker"></a>MT5215:對 ' * ' 的參考可能需要對原生連結器的其他架構 = XXX 或-lXXX 指令

這是警告, 表示偵測到 P/Invoke 以參考有問題的程式庫, 但應用程式不會與其連結。

<a name="MT5216" />

### <a name="mt5216-native-linking-failed-for--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5216:* 的原生連結失敗。 請提出錯誤報告在 http://bugzilla.xamarin.com

連結 AOT 編譯器的輸出時, 會回報此錯誤。

此錯誤最可能表示在 Xamarin 中發生錯誤。 請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出新問題。

<a name="MT5217" />

### <a name="mt5217-native-linking-possibly-failed-because-the-linker-command-line-was-too-long--characters"></a>MT5217:原生連結可能失敗, 因為連結器命令列太長 (* 個字元)。

原生連結失敗, 而且可能發生這種情況, 因為連結器命令太長。

Xamarin iOS 專案通常會動態參考原生符號, 這表示原生連結器可能會在原生連結程式期間移除這類原生符號, 因為原生連結器不會看到使用這些符號。

通常 Xamarin 會要求原生連結器使用`-u symbol`連結器旗標來保留這類符號, 但如果有許多這類符號, 則整個命令列可能會超過作業系統所指定的命令列長度上限。

這類動態符號有幾個可能的來源:

- P/會叫用靜態程式庫中的方法 (其中 dll 名稱`__Internal`是在 DllImport 屬性`[DllImport ("__Internal")]`中)。
- 從系結專案 (`[Field]`屬性) 之靜態程式庫中的記憶體位置欄位參考。
- 從系結專案的靜態程式庫中參考的目標 C 類別 (使用累加式組建時, 或未使用靜態註冊機構時)。

可能的解決方案：

- 啟用 managed 連結器 (如果可能適用于所有元件, 而不只是 SDK 元件)。 這可能會移除動態符號的足夠來源, 使連結器的命令列不會超過最大值。
- 減少 P/Invoke、欄位參考和 (或) 目標 C 類別的數目。
- 請重寫動態符號, 使其具有較短的名稱。
- 在`-dlsym:false`專案的 iOS 組建選項中, 傳遞做為額外的 mtouch 引數。 使用此選項時, Xamarin 會在 AOT 編譯的程式碼中產生原生參考, 而不需要要求連結器保留此符號。 不過, 這只適用于裝置組建, 如果靜態程式庫中沒有函式的 P/Invoke, 則會導致連結器錯誤。
- 在`--dynamic-symbol-mode=code`專案的 iOS 組建選項中, 傳遞做為額外的 mtouch 引數。 使用此選項時, Xamarin 將會產生參考這些符號的其他機器碼, 而不會要求原生連結器使用命令列引數來保留這些符號。 這種方法的缺點是它會稍微增加可執行檔的大小。
- 藉由在專案的 iOS `--registrar:static`組建選項中傳遞作為其他 mtouch 引數 (適用于模擬器組建, 因為靜態註冊機構已是裝置組建的預設值), 而啟用靜態註冊機構。 靜態註冊機構會產生以靜態方式參考目標 C 類別的程式碼, 因此不需要要求原生連結器來保留這類類別。
- 停用增量組建 (針對裝置組建)。 啟用累加式組建時, 原生連結器不會考慮靜態註冊機構所產生的程式碼, 這表示 Xamarin 仍然必須要求連結器保留參考的目標 C 類別。 因此停用累加式組建將會造成這種需求。

<a name="MT5218" />

### <a name="mt5218-cant-ignore-the-dynamic-symbol-symbol---ignore-dynamic-symbolsymbol-because-it-was-not-detected-as-a-dynamic-symbol"></a>MT5218:無法忽略動態符號 {symbol} (--忽略-dynamic-符號 = {symbol}), 因為未偵測到它是動態符號。

已傳遞命令列自`--ignore-dynamic-symbol=symbol`變數, 但此符號不是被辨識為必須手動保留之動態符號的符號。

這有兩個主要原因:

- 符號名稱不正確。
  - 不要在符號名稱前面加上底線。
  - 目標-C 類別的符號為`OBJC_CLASS_$_<classname>`。
- 符號是正確的, 但它是以一般方式保留的符號 (某些組建選項會導致動態符號的確切清單有所不同)。

### <a name="mt53xx-other-tools"></a>MT53xx:其他工具

<!--
  MT53xx other tools
  -->

<a name="MT5301" />

### <a name="mt5301-missing-strip-tool-please-install-xcode-command-line-tools-component"></a>MT5301:缺少 ' 帶狀 ' 工具。 請安裝 Xcode ' 命令列工具 ' 元件

<a name="MT5302" />

### <a name="mt5302-missing-dsymutil-tool-please-install-xcode-command-line-tools-component"></a>MT5302:缺少 ' dsymutil ' 工具。 請安裝 Xcode ' 命令列工具 ' 元件

<a name="MT5303" />

### <a name="mt5303-failed-to-generate-the-debug-symbols-dsym-directory-please-review-the-build-log"></a>MT5303:無法產生 debug 符號 (dSYM 目錄)。 請檢查組建記錄檔。

在最終. app 目錄上執行 dsymutil 時發生錯誤, 以建立 debug 符號。 請檢查組建記錄檔, 以查看 dsymutil 的輸出, 並瞭解如何修正此問題。

<a name="MT5304" />

### <a name="mt5304-failed-to-strip-the-final-binary-please-review-the-build-log"></a>MT5304:無法去除最後的二進位檔。 請檢查組建記錄檔。

執行 ' 帶狀 ' 工具以從應用程式移除偵錯工具資訊時發生錯誤。

<a name="MT5305" />

### <a name="mt5305-missing-lipo-tool-please-install-xcode-command-line-tools-component"></a>MT5305:缺少 ' lipo ' 工具。 請安裝 Xcode ' 命令列工具 ' 元件

<a name="MT5306" />

### <a name="mt5306-failed-to-create-the-a-fat-library-please-review-the-build-log"></a>MT5306:無法建立 fat 媒體櫃。 請檢查組建記錄檔。

執行 ' lipo ' 工具時發生錯誤。 請檢查組建記錄檔, 以查看 ' lipo ' 所回報的錯誤。

<a name="MT5307" />

### <a name="mt5307-failed-to-sign-the-executable-please-review-the-build-log"></a>MT5307:無法簽署可執行檔。 請檢查組建記錄檔。

簽署應用程式時發生錯誤。 請檢查組建記錄檔, 以查看 ' codesign ' 所回報的錯誤。

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

### <a name="mt6001-running-version-of-cecil-doesnt-support-assembly-stripping"></a>MT6001:執行版本的 Cecil 不支援元件去除

<a name="MT6002" />

### <a name="mt6002-could-not-strip-assembly-"></a>MT6002:無法帶狀的元件`*`。

從應用程式的元件中去除 managed 程式碼 (移除 IL 程式碼) 時發生錯誤。

<a name="MT6003" />

### <a name="mt6003-unauthorizedaccessexception-message"></a>MT6003: [System.unauthorizedaccessexception message]

從應用程式中去除偵測符號時發生安全性錯誤。

## <a name="mt7xxx-msbuild-error-messages"></a>MT7xxx:MSBuild 錯誤訊息

<!--
 MT7xxx msbuild errors
  -->

<a name="MT7001" />

### <a name="mt7001-could-not-resolve-host-ips-for-wifi-debugger-settings"></a>MT7001:無法解析 WiFi 偵錯工具設定的主機 Ip。

*MSBuild 工作:DetectDebugNetworkConfigurationTaskBase*

疑難排解步驟:

- 嘗試執行`csharp -e 'System.Net.Dns.GetHostEntry (System.Net.Dns.GetHostName ()).AddressList'` (這應該會提供 IP 位址, 而非明顯的錯誤)。
- 嘗試執行「ping \`主機名稱\`」, 這可能會提供您更多的資訊, 例如:`cannot resolve MyHost.local: Unknown host`

在某些情況下, 它是「區域網路」的問題, 而且可以藉由在中`127.0.0.1   MyHost.local` `/etc/hosts`新增未知的主機來解決。

<a name="MT7002" />

### <a name="mt7002-this-machine-does-not-have-any-network-adapters-this-is-required-when-debugging-or-profiling-on-device-over-wifi"></a>MT7002:這部電腦沒有任何網路介面卡。 在透過 WiFi 的裝置上進行程式碼剖析或分析時, 這是必要的。

*MSBuild 工作:DetectDebugNetworkConfigurationTaskBase*

<a name="MT7003" />

### <a name="mt7003-the-app-extension--does-not-contain-an-infoplist"></a>MT7003:應用程式延伸模組 ' * ' 不包含 plist。

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7004" />

### <a name="mt7004-the-app-extension--does-not-specify-a-cfbundleidentifier"></a>MT7004:應用程式延伸模組 ' * ' 未指定鍵 cfbundleidentifier。

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7005" />

### <a name="mt7005-the-app-extension--does-not-specify-a-cfbundleexecutable"></a>MT7005:應用程式延伸模組 ' * ' 未指定 CFBundleExecutable。

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7006" />

### <a name="mt7006-the-app-extension--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7006:應用程式延伸模組\*' ' 有不正確鍵 cfbundleidentifier\*(), 其開頭不是主要應用程式套件組合的鍵 cfbundleidentifier (*)。

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7007" />

### <a name="mt7007-the-app-extension--has-a-cfbundleidentifier--that-ends-with-the-illegal-suffix-key"></a>MT7007:應用程式延伸模組\*' ' 具有以不\*合法尾碼 ". key" 結尾的鍵 cfbundleidentifier ()。

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7008" />

### <a name="mt7008-the-app-extension--does-not-specify-a-cfbundleshortversionstring"></a>MT7008:應用程式延伸模組 ' * ' 未指定 CFBundleShortVersionString。

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7009" />

### <a name="mt7009-the-app-extension--has-an-invalid-infoplist-it-does-not-contain-an-nsextension-dictionary"></a>MT7009:應用程式延伸模組 ' * ' 的資訊無效。 plist: 它不包含 NSExtension 字典。

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7010" />

### <a name="mt7010-the-app-extension--has-an-invalid-infoplist-the-nsextension-dictionary-does-not-contain-an-nsextensionpointidentifier-value"></a>MT7010:應用程式延伸模組 ' * ' 的資訊無效。 plist: NSExtension 字典未包含 NSExtensionPointIdentifier 值。

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7011" />

### <a name="mt7011-the-watchkit-extension--has-an-invalid-infoplist-the-nsextension-dictionary-does-not-contain-an-nsextensionattributes-dictionary"></a>MT7011:WatchKit 延伸模組 ' * ' 包含不正確資訊。 plist: NSExtension 字典未包含 NSExtensionAttributes 字典。

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7012" />

### <a name="mt7012-the-watchkit-extension--does-not-have-exactly-one-watch-app"></a>MT7012:WatchKit 擴充功能 ' * ' 不只有一個監看式應用程式。

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7013" />

### <a name="mt7013-the-watchkit-extension--has-an-invalid-infoplist-uirequireddevicecapabilities-must-contain-the-watch-companion-capability"></a>MT7013:WatchKit 擴充功能 ' * ' 有不正確資訊。 plist:UIRequiredDeviceCapabilities 必須包含「監看式」功能。

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7014" />

### <a name="mt7014-the-watch-app--does-not-contain-an-infoplist"></a>MT7014:監看式應用程式 ' * ' 未包含 plist。

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7015" />

### <a name="mt7015-the-watch-app--does-not-specify-a-cfbundleidentifier"></a>MT7015:監看式應用程式 ' * ' 未指定鍵 cfbundleidentifier。

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7016" />

### <a name="mt7016-the-watch-app--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7016:Watch 應用程式 '\*' 有不正確鍵 cfbundleidentifier (\*), 其開頭不是主要應用程式套件組合的鍵 cfbundleidentifier (*)。

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7017" />

### <a name="mt7017-the-watch-app--does-not-have-a-valid-uidevicefamily-value-expected-watch-4-but-found--"></a>MT7017:監看式應用\*程式 ' ' 沒有有效的 UIDeviceFamily 值。 必須是 ' Watch (4) ', 但\*找到 ' (*) '。

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7018" />

### <a name="mt7018-the-watch-app--does-not-specify-a-cfbundleexecutable"></a>MT7018:監看式應用程式 ' * ' 未指定 CFBundleExecutable

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7019" />

### <a name="mt7019-the-watch-app--has-an-invalid-wkcompanionappbundleidentifier-value--it-does-not-match-the-main-app-bundles-cfbundleidentifier-"></a>MT7019:監看式應用\*程式 ' ' 有不正確 WKCompanionAppBundleIdentifier 值\*(' '), 它與主要應用程式套件組合的鍵 cfbundleidentifier (' * ') 不相符。

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7020" />

### <a name="mt7020-the-watch-app--has-an-invalid-infoplist-the-wkwatchkitapp-key-must-be-present-and-have-a-value-of-true"></a>MT7020:監看式應用程式 ' * ' 的資訊無效。 plist: WKWatchKitApp 索引鍵必須存在, 且其值為 ' true '。

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7021" />

### <a name="mt7021-the-watch-app--has-an-invalid-infoplist-the-lsrequiresiphoneos-key-must-not-be-present"></a>MT7021:監看式應用程式 ' * ' 有不正確資訊。 plist: LSRequiresIPhoneOS 金鑰不得存在。

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7022" />

### <a name="mt7022-the-watch-app--does-not-contain-a-watch-extension"></a>MT7022:監看式應用程式 ' * ' 未包含監看式延伸模組。

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7023" />

### <a name="mt7023-the-watch-extension--does-not-contain-an-infoplist"></a>MT7023:Watch 延伸模組 ' * ' 不包含 plist。

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7024" />

### <a name="mt7024-the-watch-extension--does-not-specify-a-cfbundleidentifier"></a>MT7024:Watch 延伸模組 ' * ' 未指定鍵 cfbundleidentifier。

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7025" />

### <a name="mt7025-the-watch-extension--does-not-specify-a-cfbundleexecutable"></a>MT7025:Watch 延伸模組 ' * ' 未指定 CFBundleExecutable。

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7026" />

### <a name="mt7026-the-watch-extension--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7026:Watch 延伸模組 '\*' 有不正確鍵 cfbundleidentifier (\*), 其開頭不是主要應用程式套件組合的鍵 cfbundleidentifier (*)。

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7027" />

### <a name="mt7027-the-watch-extension--has-a-cfbundleidentifier--that-ends-with-the-illegal-suffix-key"></a>MT7027:Watch 延伸模組 '\*' 具有以不合法\*尾碼 ". key" 結尾的鍵 cfbundleidentifier ()。

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7028" />

### <a name="mt7028-the-watch-extension--has-an-invalid-infoplist-it-does-not-contain-an-nsextension-dictionary"></a>MT7028:監看式延伸模組 ' * ' 的資訊無效。 plist: 它不包含 NSExtension 字典。

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7029" />

### <a name="mt7029-the-watch-extension--has-an-invalid-infoplist-the-nsextensionpointidentifier-must-be-comapplewatchkit"></a>MT7029:監看式延伸模組 ' * ' 有不正確資訊。 plist: NSExtensionPointIdentifier 必須是 "watchkit"。

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7030" />

### <a name="mt7030-the-watch-extension--has-an-invalid-infoplist-the-nsextension-dictionary-must-contain-nsextensionattributes"></a>MT7030:監看式延伸模組 ' * ' 有不正確資訊。 plist: NSExtension 字典必須包含 NSExtensionAttributes。

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7031" />

### <a name="mt7031-the-watch-extension--has-an-invalid-infoplist-the-nsextensionattributes-dictionary-must-contain-a-wkappbundleidentifier"></a>MT7031:監看式延伸模組 ' * ' 有不正確資訊。 plist: NSExtensionAttributes 字典必須包含 WKAppBundleIdentifier。

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7032" />

### <a name="mt7032-the-watchkit-extension--has-an-invalid-infoplist-uirequireddevicecapabilities-should-not-contain-the-watch-companion-capability"></a>MT7032:WatchKit 擴充功能 ' * ' 有不正確資訊。 plist:UIRequiredDeviceCapabilities 不應包含「監看式」功能。

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7033" />

### <a name="mt7033-the-watch-app--does-not-contain-an-infoplist"></a>MT7033:監看式應用程式 ' * ' 未包含 plist。

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7034" />

### <a name="mt7034-the-watch-app--does-not-specify-a-cfbundleidentifier"></a>MT7034:監看式應用程式 ' * ' 未指定鍵 cfbundleidentifier。

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7035" />

### <a name="mt7035-the-watch-app--does-not-have-a-valid-uidevicefamily-value-expected--but-found--"></a>MT7035:監看式應用\*程式 ' ' 沒有有效的 UIDeviceFamily 值。 必須是\*' ', 但\*卻\*找到 ' () '。

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7036" />

### <a name="mt7036-the-watch-app--does-not-specify-a-cfbundleexecutable"></a>MT7036:監看式應用程式 ' * ' 未指定 CFBundleExecutable。

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7037" />

### <a name="mt7037-the-watchkit-extension-extensionname-has-an-invalid-wkappbundleidentifier-value--it-does-not-match-the-watch-apps-cfbundleidentifier-"></a>MT7037:WatchKit 延伸模組 ' {extensionName} ' 具有不正確 WKAppBundleIdentifier 值 ('\*'), 它與 Watch 應用程式的鍵 cfbundleidentifier ('\*') 不相符。

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7038" />

### <a name="mt7038-the-watch-app--has-an-invalid-infoplist-the-wkcompanionappbundleidentifier-must-exist-and-must-match-the-main-app-bundles-cfbundleidentifier"></a>MT7038:監看式應用程式 ' * ' 的資訊無效。 plist: WKCompanionAppBundleIdentifier 必須存在, 且必須符合主要應用程式套件組合的鍵 cfbundleidentifier。

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7039" />

### <a name="mt7039-the-watch-app--has-an-invalid-infoplist-the-lsrequiresiphoneos-key-must-not-be-present"></a>MT7039:監看式應用程式 ' * ' 有不正確資訊。 plist: LSRequiresIPhoneOS 金鑰不得存在。

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7040" />

### <a name="mt7040-the-app-bundle-appbundlepath-does-not-contain-an-infoplist"></a>MT7040:應用程式套件組合 {AppBundlePath} 未包含 plist。

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7041" />

### <a name="mt7041-main-infoplist-path-does-not-specify-a-cfbundleidentifier"></a>MT7041:主要資訊。 plist 路徑未指定鍵 cfbundleidentifier。

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7042" />

### <a name="mt7042-main-infoplist-path-does-not-specify-a-cfbundleexecutable"></a>MT7042:主要資訊。 plist 路徑未指定 CFBundleExecutable。

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7043" />

### <a name="mt7043-main-infoplist-path-does-not-specify-a-cfbundlesupportedplatforms"></a>MT7043:主要資訊。 plist 路徑未指定 CFBundleSupportedPlatforms。

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7044" />

### <a name="mt7044-main-infoplist-path-does-not-specify-a-uidevicefamily"></a>MT7044:主要資訊。 plist 路徑未指定 UIDeviceFamily。

*MSBuild 工作:ValidateAppBundleTaskBase*

<a name="MT7045" />

### <a name="mt7045-unrecognized-format-"></a>MT7045:無法辨識的格式: *。

*MSBuild 工作:PropertyListEditorTaskBase*

其中 * 可以是:

- 字串
- array
- dict
- bool
- 實數
- integer
- 日期
- 資料

<a name="MT7046" />

### <a name="mt7046-add-entry--incorrectly-specified"></a>MT7046:加入:專案 (*) 指定不正確。

*MSBuild 工作:PropertyListEditorTaskBase*

<a name="MT7047" />

### <a name="mt7047-add-entry--contains-invalid-array-index"></a>MT7047:加入:專案 (*) 包含不正確陣列索引。

*MSBuild 工作:PropertyListEditorTaskBase*

<a name="MT7048" />

### <a name="mt7048-add--entry-already-exists"></a>MT7048:Add: * 專案已經存在。

*MSBuild 工作:PropertyListEditorTaskBase*

<a name="MT7049" />

### <a name="mt7049-add-cant-add-entry--to-parent"></a>MT7049:加入:無法將專案 (*) 新增至父系。

*MSBuild 工作:PropertyListEditorTaskBase*

<a name="MT7050" />

### <a name="mt7050-delete-cant-delete-entry--from-parent"></a>MT7050:刪除：無法從父系刪除專案 (*)。

*MSBuild 工作:PropertyListEditorTaskBase*

<a name="MT7051" />

### <a name="mt7051-delete-entry--contains-invalid-array-index"></a>MT7051:刪除：專案 (*) 包含不正確陣列索引。

*MSBuild 工作:PropertyListEditorTaskBase*

<a name="MT7052" />

### <a name="mt7052-delete-entry--does-not-exist"></a>MT7052:刪除：專案 (*) 不存在。

*MSBuild 工作:PropertyListEditorTaskBase*

<a name="MT7053" />

### <a name="mt7053-import-entry--incorrectly-specified"></a>MT7053:導專案 (*) 指定不正確。

*MSBuild 工作:PropertyListEditorTaskBase*

<a name="MT7054" />

### <a name="mt7054-import-entry--contains-invalid-array-index"></a>MT7054:導專案 (*) 包含不正確陣列索引。

*MSBuild 工作:PropertyListEditorTaskBase*

<a name="MT7055" />

### <a name="mt7055-import-error-reading-file-"></a>MT7055:導讀取檔案時發生錯誤: *。

*MSBuild 工作:PropertyListEditorTaskBase*

<a name="MT7056" />

### <a name="mt7056-import-cant-add-entry--to-parent"></a>MT7056:導無法將專案 (*) 新增至父系。

*MSBuild 工作:PropertyListEditorTaskBase*

<a name="MT7057" />

### <a name="mt7057-merge-cant-add-array-entries-to-dict"></a>MT7057:Merge無法將陣列專案加入至 dict。

*MSBuild 工作:PropertyListEditorTaskBase*

<a name="MT7058" />

### <a name="mt7058-merge-specified-entry-must-be-a-container"></a>MT7058:Merge指定的專案必須是容器。

*MSBuild 工作:PropertyListEditorTaskBase*

<a name="MT7059" />

### <a name="mt7059-merge-entry--contains-invalid-array-index"></a>MT7059:Merge專案 (*) 包含不正確陣列索引。

*MSBuild 工作:PropertyListEditorTaskBase*

<a name="MT7060" />

### <a name="mt7060-merge-entry--does-not-exist"></a>MT7060:Merge專案 (*) 不存在。

*MSBuild 工作:PropertyListEditorTaskBase*

<a name="MT7061" />

### <a name="mt7061-merge-error-reading-file-"></a>MT7061:Merge讀取檔案時發生錯誤: *。

*MSBuild 工作:PropertyListEditorTaskBase*

<a name="MT7062" />

### <a name="mt7062-set-entry--incorrectly-specified"></a>MT7062:設定：專案 (*) 指定不正確。

*MSBuild 工作:PropertyListEditorTaskBase*

<a name="MT7063" />

### <a name="mt7063-set-entry--contains-invalid-array-index"></a>MT7063:設定：專案 (*) 包含不正確陣列索引。

*MSBuild 工作:PropertyListEditorTaskBase*

<a name="MT7064" />

### <a name="mt7064-set-entry--does-not-exist"></a>MT7064:設定：專案 (*) 不存在。

*MSBuild 工作:PropertyListEditorTaskBase*

<a name="MT7065" />

### <a name="mt7065-unknown-propertylist-editor-action-"></a>MT7065:未知的 PropertyList 編輯器動作: *。

*MSBuild 工作:PropertyListEditorTaskBase*

<a name="MT7066" />

### <a name="mt7066-error-loading--"></a>MT7066:載入 ' * ' 時發生錯誤: *。

*MSBuild 工作:PropertyListEditorTaskBase*

<a name="MT7067" />

### <a name="mt7067-error-saving--"></a>MT7067:儲存 ' * ' 時發生錯誤: *。

*MSBuild 工作:PropertyListEditorTaskBase*

## <a name="mt8xxx-runtime-error-messages"></a>MT8xxx:執行階段錯誤訊息

<!--
 MT8xxx runtime
  MT800x misc
  -->

<a name="MT8001" />

### <a name="mt8001-version-mismatch-between-the-native-xamarinios-runtime-and-monotouchdll-please-reinstall-xamarinios"></a>MT8001:原生的 Xamarin iOS 執行時間與 monotouch 版本不相符。 請重新安裝 Xamarin. iOS。

<a name="MT8002" />

### <a name="mt8002-could-not-find-the-method--in-the-type-"></a>MT8002:在類型 '\*' 中找\*不到方法 ' '。

<a name="MT8003" />

### <a name="mt8003-failed-to-find-the-closed-generic-method--on-the-type-"></a>MT8003:在型別 '\* \*' 上找不到封閉式泛型方法 ' '。

<a name="MT8004" />

### <a name="mt8004-cannot-create-an-instance-of--for-the-native-object-0x-of-type--because-another-instance-already-exists-for-this-native-object-of-type-"></a>MT8004:無法為原生物件 0x * (屬於類型 ' * ') 建立 * 的實例, 因為這個原生物件的另一個實例已經存在 (屬於 * 類型)。

<a name="MT8005" />

### <a name="mt8005-wrapper-type--is-missing-its-native-objectivec-class-"></a>MT8005:包裝函式\*類型 ' ' 缺少其原生 ObjectiveC\*類別 ' '。

<a name="MT8006" />

### <a name="mt8006-failed-to-find-the-selector--on-the-type-"></a>MT8006:在型別 '\* \*' 上找不到選取器 ' '

<a name="MT8007" />

### <a name="mt8007-cannot-get-the-method-descriptor-for-the-selector--on-the-type--because-the-selector-does-not-correspond-to-a-method"></a>MT8007:無法取得類型 '\* \*' 上選取器 ' ' 的方法描述項, 因為選取器未對應至方法

<a name="MT8008" />

### <a name="mt8008-the-loaded-version-of-xamariniosdll-was-compiled-for--bits-while-the-process-is--bits-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8008:已載入的 Xamarin. .dll 版本已針對 * 位編譯, 而此程式是 * 位。 請將 bug 歸檔在 http://bugzilla.xamarin.com 。

這表示在組建程式中發生錯誤。 請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出新問題。

<a name="MT8009" />

### <a name="mt8009-unable-to-locate-the-block-to-delegate-conversion-method-for-the-method-s-parameter--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8009:找不到要委派方法之轉換方法的區塊 *。* 's 參數 # *。 請將 bug 歸檔在 http://bugzilla.xamarin.com 。

這表示 API 未正確系結。 如果這是 Xamarin 所公開的 API, 請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出新的問題。 如果是協力廠商系結, 請洽詢廠商。

<a name="MT8010" />

### <a name="mt8010-native-type-size-mismatch-between-xamariniosmacdll-and-the-executing-architecture-xamariniosmacdll-was-built-for--bit-while-the-current-process-is--bit"></a>MT8010:Xamarin 之間的原生類型大小不相符。[iOS |Mac] .dll 和執行中的架構。 Xamarin.[iOS |Mac] .dll 是針對 * 位所建立, 而目前的進程是 * 位。

這表示在組建程式中發生錯誤。 請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出新問題。

<a name="MT8011" />

### <a name="mt8011-unable-to-locate-the-delegate-to-block-conversion-attribute-delegateproxy-for-the-return-value-for-the-method--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8011:無法為方法的傳回值找出委派來封鎖轉換屬性 ([DelegateProxy])。 請將 bug 歸檔在 http://bugzilla.xamarin.com 。

Xamarin 在執行時間找不到必要的方法 (將委派轉換成區塊)。

這通常表示在 Xamarin. iOS 中有錯誤。 請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出新問題。

<a name="MT8012" />

### <a name="mt8012-invalid-delegateproxyattribute-for-the-return-value-for-the-method--delegatetype-is-null-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8012:方法的傳回值的 DelegateProxyAttribute 無效 *。* :委派為 null。 請將 bug 歸檔在 http://bugzilla.xamarin.com 。

有問題之方法的 DelegateProxy 屬性無效。

這通常表示在 Xamarin. iOS 中有錯誤。 請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出新問題。

<a name="MT8013" />

### <a name="mt8013-invalid-delegateproxyattribute-for-the-return-value-for-the-method--delegatetype-2-specifies-a-type-without-a-handler-field-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8013:方法的傳回值的 DelegateProxyAttribute 無效 *。* :委派 ({2}) 會指定沒有 ' Handler ' 欄位的類型。 請將 bug 歸檔在 http://bugzilla.xamarin.com 。

有`[DelegateProxy]`問題之方法的屬性無效。

這通常表示在 Xamarin. iOS 中有錯誤。 請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出新問題。

<a name="MT8014" />

### <a name="mt8014-invalid-delegateproxyattribute-for-the-return-value-for-the-method--the-delegatetypes-2-handler-field-is-null-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8014:方法的傳回值的 DelegateProxyAttribute 無效 *。* :委派的 ({2}) ' Handler ' 欄位是 null。 請將 bug 歸檔在 http://bugzilla.xamarin.com 。

有`[DelegateProxy]`問題之方法的屬性無效。

這通常表示在 Xamarin. iOS 中有錯誤。 請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出新問題。

<a name="MT8015" />

### <a name="mt8015-invalid-delegateproxyattribute-for-the-return-value-for-the-method--the-delegatetypes-2-handler-field-is-not-a-delegate-its-a--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8015:方法的傳回值的 DelegateProxyAttribute 無效 *。* :委派的 ({2}) 「處理常式」欄位不是委派, 而是 *。 請將 bug 歸檔在 http://bugzilla.xamarin.com 。

有問題之方法的 DelegateProxy 屬性無效。

這通常表示在 Xamarin. iOS 中有錯誤。 請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出新問題。

<a name="MT8016" />

### <a name="mt8016-unable-to-convert-delegate-to-block-for-the-return-value-for-the-method--because-the-input-isnt-a-delegate-its-a--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8016:無法將方法的傳回值的委派轉換為 block *。* 因為輸入不是委派, 所以它是 *。 請將 bug 歸檔在 http://bugzilla.xamarin.com 。

有`[DelegateProxy]`問題之方法的屬性無效。

這通常表示在 Xamarin. iOS 中有錯誤。 請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出新問題。

<!-- 8017 is used by mmp -->

<a name="MT8018" />

### <a name="mt8018-internal-consistency-error-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8018:內部一致性錯誤。 請提出錯誤報告在 http://bugzilla.xamarin.com 。

這表示在 Xamarin. iOS 中的錯誤。 請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出新問題。

<a name="MT8019" />

### <a name="mt8019-could-not-find-the-assembly--in-the-loaded-assemblies"></a>MT8019:在載入的元件中找不到元件 *。

這表示在 Xamarin. iOS 中的錯誤。 請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出新問題。

<a name="MT8020" />

### <a name="mt8020-could-not-find-the-module-with-metadatatoken--in-the-assembly-"></a>MT8020:在元件 * 中找不到具有 MetadataToken * 的模組。

這表示在 Xamarin. iOS 中的錯誤。 請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出新問題。

<a name="MT8021" />

### <a name="mt8021-unknown-implicit-token-type-"></a>MT8021:未知的隱含 token 類型: *。

這表示在 Xamarin. iOS 中的錯誤。 請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出新問題。

<a name="MT8022" />

### <a name="mt8022-expected-the-token-reference--to-be-a--but-its-a--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8022:權杖參考 * 必須是 *, 但它是 *。 請提出錯誤報告在 http://bugzilla.xamarin.com 。

這表示在 Xamarin. iOS 中的錯誤。 請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出新問題。

<a name="MT8023" />

### <a name="mt8023-an-instance-object-is-required-to-construct-a-closed-generic-method-for-the-open-generic-method--token-reference--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8023:需要實例物件, 才能為開放式泛型方法: * (token 參考: *) 建立封閉式泛型方法。 請提出錯誤報告在 http://bugzilla.xamarin.com 。

這表示在 Xamarin. iOS 中的錯誤。 請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出新問題。

<a name="MT8024" />

### <a name="mt8024-could-not-find-a-valid-extension-type-for-the-smart-enum-smart_type-please-file-a-bug-at-httpsbugzillaxamarincom"></a>MT8024:找不到智慧列舉 ' {smart_type} ' 的有效延伸模組類型。 請將 bug 歸檔在 https://bugzilla.xamarin.com 。

這表示在 Xamarin. iOS 中的錯誤。 請在[github](https://github.com/xamarin/xamarin-macios/issues/new)上提出新問題。
