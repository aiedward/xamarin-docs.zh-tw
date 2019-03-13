---
title: Xamarin.Mac 的錯誤訊息 (mmp)
description: 本文件列出 mmp，用來封裝已編譯的組件載入可執行檔的 Mac 應用程式的工具所產生的錯誤。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5B26339F-A202-4E41-9229-D0BC9E77868E
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/27/2018
ms.openlocfilehash: 640d1adc048bec167508d8c288b62d498f061b0d
ms.sourcegitcommit: 4859da8772dbe920fdd653180450e5ddfb436718
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2018
ms.locfileid: "50234969"
---
# <a name="xamarinmac-error-messages-mmp"></a>Xamarin.Mac 的錯誤訊息 (mmp)

## <a name="mm0xxx-mmp-error-messages"></a>MM0xxx: mmp 錯誤訊息

例如， 參數、 遺漏工具的環境。

<a name="MM0000" />

#### <a name="mm0000-unexpected-error---please-file-a-bug-report-at-httpsgithubcomxamarinxamarin-maciosissuesnew"></a>MM0000： 未預期的錯誤-請檔案錯誤報告在 https://github.com/xamarin/xamarin-macios/issues/new

發生未預期的錯誤狀況。 請[提出 bug 報告](https://github.com/xamarin/xamarin-macios/issues/new)盡可能最多資訊，包括：

* 完整建置記錄檔，並以最詳細 (例如`-v -v -v -v`中**其他 mmp 引數**);
* 最少重現錯誤，測試案例和
* 所有的版本資訊

若要取得正確的版本資訊最簡單的方式是使用**Xamarin Studio**  功能表中，**關於 Xamarin Studio**項目，**顯示詳細資料**按鈕，然後複製/貼上的版本資訊 (您可以使用**複製資訊**按鈕)。

<a name="MM0001" />

#### <a name="mm0001-this-version-of-xamarinmac-requires-mono-0-the-current-mono-version-is-1-please-update-the-monoframework-from-httpmono-projectcomdownloads"></a>MM0001： 此版本的 Xamarin.Mac 需要 Mono {0} (目前的 Mono 版本是{1})。 請將更新從 Mono.framework http://mono-project.com/Downloads

<a name="MM0003" />

#### <a name="mm0003-application-name-0exe-conflicts-with-an-sdk-or-product-assembly-dll-name"></a>MM0003： 應用程式名稱 '{0}.exe' je v konfliktu SDK 或產品組件 (.dll) 名稱。

<a name="MM0007" />

#### <a name="mm0007-the-root-assembly-0-does-not-exist"></a>MM0007： 根組件 '{0}' 不存在

<a name="MM0008" />

#### <a name="mm0008-you-should-provide-one-root-assembly-only-found-0-assemblies-1"></a>MM0008： 您應該提供一個根組件，找到{0}組件: '{1}'

<a name="MM0010" />

#### <a name="mm0010-could-not-parse-the-command-line-arguments-0"></a>MM0010： 無法剖析命令列引數： {0}

<!-- 0013 is unused -->

<a name="MM0016" />

#### <a name="mm0016-the-option-0-has-been-deprecated"></a>MM0016: 選項 '{0}' 已被取代。

<a name="MM0017" />

#### <a name="mm0017-you-should-provide-a-root-assembly"></a>MM0017： 您應提供根組件

<a name="MM0018" />

#### <a name="mm0018-unknown-command-line-argument-0"></a>MM0018： 未知的命令列引數: '{0}'

<a name="MM0020" />

#### <a name="mm0020-the-valid-options-for-0-are-1"></a>MM0020： 有效的選項為 '{0}'是'{1}'。

<a name="MM0023" />

#### <a name="mm0023-application-name-0exe-conflicts-with-another-user-assembly"></a>MM0023： 應用程式名稱 '{0}.exe' 與另一個使用者組件衝突。

<a name="MM0026" />

#### <a name="mm0026-could-not-parse-the-command-line-argument-0-1"></a>MM0026： 無法剖析命令列引數 '{0}': {1}

<a name="MM0043" />

#### <a name="mm0043-the-boehm-garbage-collector-is-not-supported-the-sgen-garbage-collector-has-been-selected-instead"></a>MM0043： 不支援 Boehm 記憶體回收行程。 SGen 記憶體回收行程已改為選取。

<a name="MM0050" />

#### <a name="mm0050-you-cannot-provide-a-root-assembly-if---no-root-assembly-is-passed"></a>MM0050： 如果-傳遞沒有根組件，無法提供根組件。

<a name="MM0051" />

#### <a name="mm0051-an-output-directory---output-is-required-if---no-root-assembly-is-passed"></a>MM0051： 輸出目錄 (-輸出)-如果沒有根組件會傳遞，則需要。

<a name="MM0053" />

#### <a name="mm0053-cannot-disable-new-refcount-with-the-unified-api"></a>MM0053： 無法停用新的 refcount 使用統一的 API。

<a name="MM0056" />

#### <a name="mm0056-cannot-find-xcode-in-any-of-our-default-locations-please-install-xcode-or-pass-a-custom-path-using---sdkrootpath"></a>MM0056： 在我們的預設位置中找不到 Xcode。 請安裝 Xcode，或傳遞自訂路徑使用--sdkroot =<path>

<a name="MM0059" />

#### <a name="mm0059-could-not-find-the-currently-selected-xcode-on-the-system-0"></a>MM0059： 找不到系統上的目前選取的 Xcode: {0};

<a name="MM0060" />

#### <a name="mm0060-could-not-find-the-currently-selected-xcode-on-the-system-xcode-select---print-path-returned-0-but-that-directory-does-not-exist"></a>MM0060： 在系統上找不到目前選取的 Xcode。 'xcode-select--列印路徑' 傳回 '{0}'，但該目錄不存在。

<a name="MM0068" />

#### <a name="mm0068-invalid-value-for-target-framework-0"></a>MM0068： 無效的值，目標 framework: {0}。

<a name="MM0071" />

#### <a name="mm0071-unknown-platform--this-usually-indicates-a-bug-in-xamarinmac-please-file-a-bug-report-at-httpsbugzillaxamarincom-with-a-test-case"></a>MM0071： 未知的平台: *。 這通常表示 Xamarin.Mac; 中的 bug請檔案錯誤報告在 https://bugzilla.xamarin.com 與測試案例。

這通常表示 Xamarin.Mac; 中的 bug請提出錯誤報告在[ https://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=Xamarin.Mac)與測試案例。

<a name="MM0079" />

#### <a name="mm0079-internal-error---no-executable-was-copied-into-the-app-bundle-please-contact-supportxamarincom"></a>MM0079： 內部錯誤-沒有可執行檔會複製到應用程式套件組合。 請連絡 'support@xamarin.com'

<a name="MM0080" />

#### <a name="mm0080-disabling-newrefcount---new-refcountfalse-is-deprecated"></a>MM0080： 停用 NewRefCount、--新增-refcount:false，已被取代。

<!-- 0088 used by mtouch -->
<!-- 0089 used by mtouch -->

<a name="MM0091" />

#### <a name="mm0091-this-version-of-xamarinmac-requires-the--sdk-shipped-with-xcode--either-upgrade-xcode-to-get-the-required-header-files-or-use-the-dynamic-registrar-or-set-the-managed-linker-behaviour-to-link-platform-or-link-framework-sdks-only-to-try-to-avoid-the-new-apis"></a>MM0091： 此版本的 Xamarin.Mac 需要 * SDK (隨附於 Xcode *)。 請升級以取得必要的標頭檔案或使用動態的註冊機構或連結平台或僅連結 Framework Sdk （以盡量避免新的 Api） 來設定受管理的連結器行為的 Xcode。

Xamarin.Mac 需要的標頭檔，從指定錯誤訊息，來建置您的應用程式使用靜態的註冊機構中的 SDK 版本。 若要修正此錯誤的建議的方式是升級 Xcode 來取得必要的 SDK，這將包含所有必要的標頭檔案。 如果您有多個版本的 Xcode 安裝，或想要使用 Xcode，在非預設位置，請確定您的 IDE 喜好設定中設定正確的 Xcode 位置。

一種潛在、 替代的解決方案，是要讓受管理的連結器。 這會移除未使用的 API 包括在大部分情況下，新的 API，其中的標頭檔案已遺失 （或不完整）。 不過如果您的專案使用較新的 SDK 與您的 Xcode 中所導入的 API，將不會運作這所提供。

第二個可能的替代解決方案，會改為使用動態的註冊機構。 這樣會以動態方式註冊型別，以強制啟始成本，但是可以移除標頭檔需求。 

最後一個 straw 解決方案是使用較舊版本的 Xamarin.Mac，支援您的專案的 SDK 需要。

<a name="MM0097" />

#### <a name="mm0097-machineconfig-file-0-can-not-be-found"></a>MM0097: machine.config 檔案 '{0}' 找不到。

<a name="MM0098" />

#### <a name="mm0098-aot-compilation-is-only-available-on-unified"></a>MM0098: AOT 編譯才可以使用整合

<a name="MM0099" />

#### <a name="mm0099-internal-error-0-please-file-a-bug-report-with-a-test-case-httpbugzillaxamarincom"></a>MM0099： 內部錯誤{0}。 請提出問題報告與測試案例 (http://bugzilla.xamarin.com)。

<a name="MM0114" />

#### <a name="mm0114-hybrid-aot-compilation-requires-all-assemblies-to-be-aot-compiled"></a>MM0114： 混合式 AOT 編譯需要要 AOT 編譯的所有組件。

## <a name="mm1xxx-file-copy--symlinks-project-related"></a>MM1xxx： 檔案複製 / 符號連結 （相關的專案）

<a name="MM1034" />

#### <a name="mm1034-could-not-create-symlink-file---target-error-number"></a>MM1034： 無法建立符號連結 '{file}'-> '{target}': 錯誤 {number}

### <a name="mm14xx-product-assemblies"></a>MM14xx： 產品組件

<a name="MM1401" />

#### <a name="mm1401-the-required-0-assembly-is-missing-from-the-references"></a>MM1401: 需要 '{0}' 中參考的組件遺漏

<a name="MM1402" />

#### <a name="mm1402-the-assembly-0-is-not-compatible-with-this-tool"></a>MM1402： 組件 '{0}' 與此工具不相容

<a name="MM1403" />

#### <a name="mm1403-0-1-could-not-be-found-target-framework-0-is-unusable-to-package-the-application"></a>MM1403: {0} '{1}' 找不到。 目標 framework '{0}' 封裝應用程式無法使用。

<a name="MM1404" />

#### <a name="mm1404-target-framework-0-is-invalid"></a>MM1404： 目標 framework '{0}' 無效。

<a name="MM1405" />

#### <a name="mm1405-usefullxammacframework-must-always-target-framework-net-45-not-0-which-is-invalid"></a>MM1405: useFullXamMacFramework 必須一律以目標 framework.NET 4.5 中，不 '{0}' 為無效的

<a name="MM1406" />

#### <a name="mm1406-target-framework-0-is-invalid-when-targetting-xamarinmac-45-net-framwork"></a>MM1406： 目標 framework '{0}' 是無效的時機為目標的 Xamarin.Mac 4.5.NET framwork。

<a name="MM1407" />

#### <a name="mm1407-mismatch-between-xamarinmac-reference-0-and-target-framework-selected-1"></a>MM1407: Xamarin.Mac 參考不相符 '{0}'與選取的目標 framework'{1}'。

### <a name="mm15xx-assembly-gathering-not-requiring-linker-errors"></a>MM15xx： 組件 （不需要連結器） 正在蒐集錯誤

<a name="MM1501" />

#### <a name="mm1501-can-not-resolve-reference-0"></a>MM1501： 無法解析參考： {0}

### <a name="machocs"></a>MachO.cs

<a name="MM1600" />

#### <a name="mm1600-not-a-mach-o-dynamic-library-unknown-header-0x0-1"></a>MM1600： 不是 MACH-O 動態程式庫 (未知標頭 ' 0 x{0}'): {1}。

<a name="MM1601" />

#### <a name="mm1601-not-a-static-library-unknown-header-0-1"></a>MM1601： 不是靜態程式庫 (未知標頭 '{0}'): {1}。

<a name="MM1602" />

#### <a name="mm1602-not-a-mach-o-dynamic-library-unknown-header-0x0-1"></a>MM1602： 不是 MACH-O 動態程式庫 (未知標頭 ' 0 x{0}'): {1}。

<a name="MM1603" />

#### <a name="mm1603-unknown-format-for-fat-entry-at-position-0-in-1"></a>MM1603： 未知的格式，fat 位置處的項目{0}在{1}。

<a name="MM1604" />

#### <a name="mm1604-file-of-type-0-is-not-a-macho-file-1"></a>MM1604： 檔案的類型{0}不是 MachO 檔案 ({1})。

## <a name="mm2xxx-linker"></a>MM2xxx： 連結器

### <a name="mm20xx-linker-general-errors"></a>MM20xx： 連結器 （一般） 錯誤

<a name="MM2001" />

#### <a name="mm2001-could-not-link-assemblies"></a>MM2001： 無法將連結的組件

<a name="MM2002" />

#### <a name="mm2002-can-not-resolve-reference-0"></a>MM2002： 無法解析參考： {0}

<a name="MM2003" />

#### <a name="mm2003-option-0-will-be-ignored-since-linking-is-disabled"></a>MM2003： 選項 '{0}' 將被忽略，因為連結已停用

<a name="MM2004" />

#### <a name="mm2004-extra-linker-definitions-file-0-could-not-be-located"></a>MM2004： 額外連結器定義檔 '{0}' 找不到。

<a name="MM2005" />

#### <a name="mm2005-definitions-from-0-could-not-be-parsed"></a>MM2005： 來自定義 '{0}' 無法剖析。

<a name="MM2006" />

#### <a name="mm2006-native-library-0-was-referenced-but-could-not-be-found"></a>MM2006： 原生程式庫 '{0}' 的參考，但找不到。

<a name="MM2007" />

#### <a name="mm2007-xamarinmac-unified-api-against-a-full-net-profile-does-not-support-linking-pass-the--nolink-flag"></a>MM2007: Xamarin.Mac 統一 API 針對完整的.NET 設定檔不支援連結。 傳遞的-nolink 旗標。

<a name="MM2009" />

#### <a name="mm2009-referenced-by-01------this-message-is-related-to-mm2006-"></a>MM2009: 所參考{0}。{1}    ** 此訊息是否關係到 MM2006 **

<a name="MM2010" />

#### <a name="mm2010-unknown-httpmessagehandler-0-valid-values-are-httpclienthandler-default-cfnetworkhandler-or-nsurlsessionhandler"></a>MM2010： 未知的 HttpMessageHandler `{0}`。 有效值為 HttpClientHandler （預設值）、 CFNetworkHandler 或 NSUrlSessionHandler

<a name="MM2011" />

#### <a name="mm2011-unknown-tlsprovider-0--valid-values-are-default-or-appletls"></a>MM2011： 未知的 TLSProvider '{0}。  有效值為 default 或 appletls

<a name="MM2012" />

#### <a name="mm2012-only-first-0-of-1-referenced-by-warnings-shown--this-message-related-to-2009-"></a>MM2012： 前{0}的{1}「 參考 」 所示的警告。 ** 此訊息相關 2009 \*\*

<a name="MM2013" />

#### <a name="mm2013-failed-to-resolve-the-reference-to-0-referenced-in-1-the-app-will-not-include-the-referenced-assembly-and-may-fail-at-runtime"></a>MM2013： 無法解析的參考 」{0}"，參考在"{1}」。 應用程式將不會包含參考的組件，並在執行階段可能會失敗。

<a name="MM2014" />

#### <a name="mm2014-xamarinmac-extensions-do-not-support-linking-request-for-linking-will-be-ignored--this-message-is-obsolete-in-xm-36-"></a>Xamarin.Mac 延伸模組 MM2014： 不支援連結。 將略過連結要求。 ** 這個訊息是 XML 3.6 + 中的過時 \*\*

<!-- 2015 used by mtouch -->

<a name="MM2016" />

#### <a name="mm2016-invalid-tlsprovider-0-option-the-only-valid-value-1-will-be-used"></a>MM2016： 無效的 TlsProvider`{0}`選項。 唯一有效的值`{1}`將使用。

<a name="MM2017" />

#### <a name="mm2017-could-not-process-xml-description-0"></a>MM2017： 無法處理 XML 描述： {0}

<a name="MM202x" />

#### <a name="mm202x-binding-optimizer-failed-processing-"></a>MM202x： 繫結最佳化工具無法處理`...`。

<a name="MM2100" />

#### <a name="mm2100-xamarinmac-classic-api-does-not-support-platform-linking"></a>MM2100: Xamarin.Mac 傳統 API 不支援的平台連結。

<a name="MM2103" />

#### <a name="mm2103-error-processing-assembly--"></a>MM2103： 錯誤處理組件 '\*': *

處理組件時，就會發生意外的錯誤。

造成這個問題的組件是名為錯誤訊息中。 若要修正此問題的組件必須在中提供[bug 報表](https://bugzilla.xamarin.com)以及完成的組建記錄檔與啟用的詳細資訊 (亦即`-v -v -v -v`中**其他 mtouch 引數**)。

<a name="MM2104" />

#### <a name="mm2104-unable-to-link-assembly-0-as-it-is-mixed-mode"></a>MM2104： 無法將連結的組件 '{0}' 因為它是混合模式。

連結器可以處理混合模式組件。

請參閱 https://msdn.microsoft.com/library/x0w2664k.aspx 如需有關混合模式組件。

## <a name="mm3xxx-aot"></a>MM3xxx: AOT

### <a name="mm30xx-aot-general-errors"></a>MM30xx: AOT （一般） 錯誤

<a name="MM3001" />

#### <a name="mm3001-could-not-aot-the-assembly-0"></a>MM3001： 無法 AOT 的組件 '{0}'

<!-- 3002 used by mtouch -->
<!-- 3003 used by mtouch -->
<!-- 3004 used by mtouch -->
<!-- 3005 used by mtouch -->
<!-- 3006 used by mtouch -->
<!-- 3007 used by mtouch -->
<!-- 3008 used by mtouch -->

<a name="MM3009" />

#### <a name="mm3009-aot-of-0-was-requested-but-was-not-found"></a>MM3009: AOT '{0}' 要求，但找不到

<a name="MM3010" />

#### <a name="mm3010-exclusion-of-aot-of-0-was-requested-but-was-not-found"></a>MM3010： 排除的 AOT '{0}' 要求，但找不到

## <a name="mm4xxx-code-generation"></a>MM4xxx： 程式碼產生

### <a name="mm40xx-driverm"></a>MM40xx: driver.m

<a name="MM4001" />

#### <a name="mm4001-the-main-template-could-not-be-expanded-to-0"></a>MM4001： 主要的範本無法展開至`{0}`。

### <a name="mm41xx-registrar"></a>MM41xx： 註冊機構

<a name="MM4134" />

#### <a name="mm4134-your-application-is-using-the-0-framework-which-isnt-included-in-the-macos-sdk-youre-using-to-build-your-app-this-framework-was-introduced-in-osx-2-while-youre-building-with-the-macos-1-sdk-this-configuration-is-not-supported-with-the-static-registrar-pass---registrardynamic-as-an-additional-mmp-argument-in-your-projects-mac-build-option-to-select-alternatively-select-a-newer-sdk-in-your-apps-mac-build-options"></a>MM4134: 應用程式所使用 '{0}' framework，不會包含於您用來建置您的應用程式的 MacOS SDK (OSX 中引進了此架構{2}，而您要建置使用 MacOS {1} SDK。)此組態不支援靜態的註冊機構 (pass，註冊機構： 動態為專案的 Mac 組建 選項以選取 其他 mmp 引數)。 或者您的應用程式的 Mac 組建選項中選取較新的 SDK。

## <a name="mm5xxx-gcc-and-toolchain"></a>MM5xxx: GCC 和工具鏈

### <a name="mm51xx-compilation"></a>MM51xx： 編譯

<a name="MM5101" />

#### <a name="mm5101-missing-0-compiler-please-install-xcode-command-line-tools-component"></a>MM5101： 遺漏 '{0}' 編譯器。 請安裝 Xcode '命令列工具 」 元件。

<!-- 5102 used by mtouch -->

<a name="MM5103" />

#### <a name="mm5103-failed-to-compile-error-code---0-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MM5103： 無法編譯。 錯誤碼- {0}。 請提出錯誤報告在 http://bugzilla.xamarin.com

<!-- 5104 used by mtouch -->

### <a name="mm52xx-linking"></a>MM52xx： 連結

<a name="MM5202" />

#### <a name="mm5202-monoframework-mdk-is-missing-please-install-the-mdk-for-your-monoframework-version-from-httpmono-projectcomdownloads"></a>MM5202: Mono.framework MDK 遺漏。 請安裝 從您 Mono.framework 版本 MDK http://mono-project.com/Downloads

<a name="MM5203" />

#### <a name="mm5203-cant-find-libxammaca-likely-because-of-a-corrupted-xamarinmac-installation-please-reinstall-xamarinmac"></a>MM5203： 找不到 libxammac.a，可能會由於 Xamarin.Mac 安裝損毀。 請重新安裝 Xamarin.Mac。

<a name="MM5204" />

#### <a name="mm5204-invalid-architecture-x8664-is-only-supported-on-non-classic-profiles"></a>MM5204： 無效的架構。 x86_64 被只有非傳統的設定檔。

<a name="MM5205" />

#### <a name="mm5205-invalid-architecture-0-valid-architectures-are-i386-and-x8664-when---profilemobile"></a>MM5205： 不正確的結構 '{0}'。 有效的架構是 i386 及 x86_64 (當-程式碼剖析 = 行動裝置)。

<a name="MM5218" />

#### <a name="mm5218-cant-ignore-the-dynamic-symbol-symbol---ignore-dynamic-symbolsymbol-because-it-was-not-detected-as-a-dynamic-symbol"></a>MM5218： 不能忽視 {符號} 的動態符號 (-略過動態符號 = {符號}) 因為它沒有偵測到為動態的符號。

請參閱[對等的 mtouch 警告](~/ios/troubleshooting/mtouch-errors.md#MT5218)。

<!-- 5206 used by mtouch -->
<!-- 5207 used by mtouch -->
<!-- 5208 used by mtouch -->
<!-- 5209 used by mtouch -->
<!-- 5210 used by mtouch -->
<!-- 5211 used by mtouch -->
<!-- 5212 used by mtouch -->
<!-- 5213 used by mtouch -->
<!-- 5214 used by mtouch -->
<!-- 5215 used by mtouch -->
<!-- 5216 used by mtouch -->
<!-- 5217 used by mtouch -->

### <a name="mm53xx-other-tools"></a>其他工具 MM53xx:

<a name="MM5301" />

#### <a name="mm5301-pkg-config-could-not-be-found-please-install-the-monoframework-from-httpmono-projectcomdownloads"></a>MM5301： 找不到套件組態。 請安裝從 Mono.framework http://mono-project.com/Downloads

<!-- 5302 used by mtouch -->
<!-- 5303 used by mtouch -->
<!-- 5304 used by mtouch -->

<a name="MM5305" />

#### <a name="mm5305-missing-otool-tool-please-install-xcode-command-line-tools-component"></a>MM5305： 遺漏 'otool' 工具。 請安裝 Xcode '命令列工具 」 元件

<a name="MM5306" />

#### <a name="mm5306-missing-dependencies-please-install-xcode-command-line-tools-component"></a>MM5306： 遺失相依性。 請安裝 Xcode '命令列工具 」 元件

<a name="MM5308" />

#### <a name="mm5308-xcode-license-agreement-may-not-have-been-accepted--please-launch-xcode"></a>MM5308: Xcode 授權合約可能不具有已接受。  請啟動 Xcode。

<a name="MM5309" />

#### <a name="mm5309-native-linking-failed-with-error-code-1--check-build-log-for-details"></a>MM5309： 原生連結失敗，錯誤碼為 1。  檢查組建記錄檔以取得詳細資料。

<a name="MM5310" />

#### <a name="mm5310-installnametool-failed-with-an-error-code-0-check-build-log-for-details"></a>MM5310: install_name_tool 失敗，錯誤碼 '{0}'。 檢查組建記錄檔以取得詳細資料。

<!-- MM6xxx: mmp internal tools -->
<!-- MM7xxx: reserved -->

## <a name="mm8xxx-runtime"></a>MM8xxx： 執行階段

### <a name="mm800x-misc"></a>MM800x： 其他

<!-- 8000 used by mtouch -->
<!-- 8001 used by mtouch -->
<!-- 8002 used by mtouch -->
<!-- 8003 used by mtouch -->
<!-- 8004 used by mtouch -->
<!-- 8005 used by mtouch -->
<!-- 8006 used by mtouch -->
<!-- 8007 used by mtouch -->
<!-- 8008 used by mtouch -->
<!-- 8009 used by mtouch -->
<!-- 8010 used by mtouch -->
<!-- 8011 used by mtouch -->
<!-- 8012 used by mtouch -->
<!-- 8013 used by mtouch -->
<!-- 8014 used by mtouch -->
<!-- 8015 used by mtouch -->
<!-- 8016 used by mtouch -->

<a name="MM8017" />

#### <a name="mm8017-the-boehm-garbage-collector-is-not-supported-please-use-sgen-instead"></a>MM8017： 不支援 Boehm 記憶體回收行程。 請改為使用 SGen。

