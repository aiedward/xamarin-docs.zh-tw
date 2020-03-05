---
title: Xamarin. Mac 錯誤訊息（mmp）
description: 本檔列出由 mmp 產生的錯誤，這是用來將編譯的元件封裝到可執行檔 Mac 應用程式的工具。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5B26339F-A202-4E41-9229-D0BC9E77868E
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/27/2018
ms.openlocfilehash: b1125ab554bd9c73917c106c7486b742f16e628a
ms.sourcegitcommit: 52fb214c0e0243587d4e9ad9306b75e92a8cc8b7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "78292633"
---
# <a name="xamarinmac-error-messages-mmp"></a>Xamarin. Mac 錯誤訊息（mmp）

## <a name="mm0xxx-mmp-error-messages"></a>MM0xxx： mmp 錯誤訊息

例如 參數、環境、遺漏工具。

<a name="MM0000" />

#### <a name="mm0000-unexpected-error---please-file-a-bug-report-at-httpsgithubcomxamarinxamarin-maciosissuesnew"></a>MM0000：意外的錯誤-請在 https://github.com/xamarin/xamarin-macios/issues/new 提出錯誤報表

發生未預期的錯誤狀況。 請以盡可能多的資訊來提出[bug 報告](https://github.com/xamarin/xamarin-macios/issues/new)，包括：

* 完整組建記錄檔，最高詳細資訊（例如，**其他 mmp 引數**中 `-v -v -v -v`）;
* 重現錯誤的最小測試案例;和
* 所有版本的

取得確切版本資訊的最簡單方式是使用  **Xamarin Studio**  功能表，**關於 Xamarin Studio**專案、**顯示詳細資料** 按鈕，以及複製/貼上版本資訊（您可以使用 **複製資訊** 按鈕）。

<a name="MM0001" />

#### <a name="mm0001-this-version-of-xamarinmac-requires-mono-0-the-current-mono-version-is-1-please-update-the-monoframework-from-httpmono-projectcomdownloads"></a>MM0001：這個 Xamarin 版本需要 Mono {0} （目前的 Mono 版本是 {1}）。 請從 http://mono-project.com/Downloads 更新 Mono. framework

<a name="MM0003" />

#### <a name="mm0003-application-name-0exe-conflicts-with-an-sdk-or-product-assembly-dll-name"></a>MM0003：應用程式名稱 '{0}.exe ' 與 SDK 或產品元件（.dll）名稱衝突。

<a name="MM0007" />

#### <a name="mm0007-the-root-assembly-0-does-not-exist"></a>MM0007：根元件 '{0}' 不存在

<a name="MM0008" />

#### <a name="mm0008-you-should-provide-one-root-assembly-only-found-0-assemblies-1"></a>MM0008：您應該只提供一個根元件，找到 {0} 元件： '{1}'

<a name="MM0009" />

#### <a name="mm0009-error-while-loading-assemblies-"></a>MM0009：載入元件時發生錯誤： *。

從根元件參考載入元件時發生錯誤。 可能會在組建輸出中提供詳細資訊。

<a name="MM0010" />

#### <a name="mm0010-could-not-parse-the-command-line-arguments-0"></a>MM0010：無法剖析命令列引數： {0}

<!-- 0013 is unused -->

<a name="MM0016" />

#### <a name="mm0016-the-option-0-has-been-deprecated"></a>MM0016：選項 '{0}' 已被取代。

<a name="MM0017" />

#### <a name="mm0017-you-should-provide-a-root-assembly"></a>MM0017：您應該提供根元件

<a name="MM0018" />

#### <a name="mm0018-unknown-command-line-argument-0"></a>MM0018：未知的命令列引數： '{0}'

<a name="MM0020" />

#### <a name="mm0020-the-valid-options-for-0-are-1"></a>MM0020： '{0}' 的有效選項為 '{1}'。

<a name="MM0023" />

#### <a name="mm0023-application-name-0exe-conflicts-with-another-user-assembly"></a>MM0023：應用程式名稱 '{0}.exe ' 與另一個使用者元件相衝突。

<a name="MM0026" />

#### <a name="mm0026-could-not-parse-the-command-line-argument-0-1"></a>MM0026：無法剖析命令列引數 '{0}'： {1}

<a name="MM0043" />

#### <a name="mm0043-the-boehm-garbage-collector-is-not-supported-the-sgen-garbage-collector-has-been-selected-instead"></a>MM0043：不支援 Boehm 垃圾收集行程。 已改為選取 SGen 垃圾收集行程。

<a name="MM0050" />

#### <a name="mm0050-you-cannot-provide-a-root-assembly-if---no-root-assembly-is-passed"></a>MM0050：如果未傳遞--no 根元件，您就無法提供根元件。

<a name="MM0051" />

#### <a name="mm0051-an-output-directory---output-is-required-if---no-root-assembly-is-passed"></a>MM0051：如果未傳遞--no 根元件，則需要輸出目錄（--output）。

<a name="MM0053" />

#### <a name="mm0053-cannot-disable-new-refcount-with-the-unified-api"></a>MM0053：無法使用 Unified API 停用新的 refcount。

<a name="MM0056" />

#### <a name="mm0056-cannot-find-xcode-in-any-of-our-default-locations-please-install-xcode-or-pass-a-custom-path-using---sdkrootpath"></a>MM0056：在任何預設位置中都找不到 Xcode。 請安裝 Xcode，或使用--sdkroot =\<路徑來傳遞自訂路徑 >

<a name="MM0059" />

#### <a name="mm0059-could-not-find-the-currently-selected-xcode-on-the-system-0"></a>MM0059：在系統上找不到目前選取的 Xcode： {0};

<a name="MM0060" />

#### <a name="mm0060-could-not-find-the-currently-selected-xcode-on-the-system-xcode-select---print-path-returned-0-but-that-directory-does-not-exist"></a>MM0060：在系統上找不到目前選取的 Xcode。 ' xcode-select--print-path ' 已傳回 '{0}'，但該目錄不存在。

<a name="MM0068" />

#### <a name="mm0068-invalid-value-for-target-framework-0"></a>MM0068：不正確目標 framework 值： {0}。

<a name="MM0071" />

#### <a name="mm0071-unknown-platform--this-usually-indicates-a-bug-in-xamarinmac-please-file-a-bug-report-at-httpsbugzillaxamarincom-with-a-test-case"></a>MM0071：未知的平臺： *。 這通常表示 Xamarin. Mac 中的錯誤;請在測試案例 https://bugzilla.xamarin.com 提出錯誤報表。

這通常表示 Xamarin. Mac 中的錯誤;請在測試案例[https://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=Xamarin.Mac)提出錯誤報表。

<a name="MM0073" />

#### <a name="mm0073-xamarinmac--does-not-support-a-deployment-target-of--the-minimum-is--please-select-a-newer-deployment-target-in-your-projects-infoplist"></a>MM0073： Xamarin \* 不支援 \* 的部署目標（最小值為 \*）。 請在您專案的 Info. plist 中選取較新的部署目標。

最小部署目標是錯誤訊息中指定的目標。請在專案的 Info. plist 中選取較新的部署目標。

如果無法更新部署目標，請使用較舊版本的 Xamarin. Mac。

<a name="MM0074" />

#### <a name="mm0074-xamarinmac--does-not-support-a-deployment-target-of--the-maximum-is--please-select-an-older-deployment-target-in-your-projects-infoplist-or-upgrade-to-a-newer-version-of-xamarinmac"></a>MM0074： Xamarin \* 不支援 \* 的部署目標（最大值為 \*）。 請在專案的 plist 中選取較舊的部署目標，或升級至較新版本的 Xamarin. Mac。

Xamarin 不支援將最低部署目標設定為比這個特定版本的 Xamarin. Mac 建立版本更高的版本。

請在專案的 plist 中選取較舊的最低部署目標，或升級至較新版本的 Xamarin. Mac。

<a name="MM0079" />

#### <a name="mm0079-internal-error---no-executable-was-copied-into-the-app-bundle-please-contact-supportxamarincom"></a>MM0079：內部錯誤-沒有任何可執行檔已複製到應用程式套件組合中。 請洽詢 'support@xamarin.com'

<a name="MM0080" />

#### <a name="mm0080-disabling-newrefcount---new-refcountfalse-is-deprecated"></a>MM0080：停用 NewRefCount，--new-refcount： false，已被取代。

<!-- 0088 used by mtouch -->
<!-- 0089 used by mtouch -->

<a name="MM0091" />

#### <a name="mm0091-this-version-of-xamarinmac-requires-the--sdk-shipped-with-xcode--either-upgrade-xcode-to-get-the-required-header-files-or-use-the-dynamic-registrar-or-set-the-managed-linker-behaviour-to-link-platform-or-link-framework-sdks-only-to-try-to-avoid-the-new-apis"></a>MM0091：此版本的 Xamarin 需要 \* SDK （隨附于 Xcode \*）。 請升級 Xcode 以取得必要的標頭檔，或使用動態註冊機構，或將 managed 連結器行為設定為僅連結平臺或連結架構 Sdk （以嘗試避免新的 Api）。

Xamarin 需要標頭檔（來自錯誤訊息中指定的 SDK 版本），才能使用靜態註冊機構來建立您的應用程式。 若要修正此錯誤，建議的方法是升級 Xcode 以取得所需的 SDK，這會包含所有必要的標頭檔。 如果您已安裝多個版本的 Xcode，或想要在非預設位置中使用 Xcode，請務必在 IDE 的喜好設定中設定正確的 Xcode 位置。

有一個可能的替代方案，就是啟用 managed 連結器。 這會移除未使用的 API，包括在大多數情況下，缺少標頭檔案的新 API （或不完整）。 不過，如果您的專案使用的 API 與您的 Xcode 所提供的版本較新的 SDK 相比，這將無法正常執行。

第二個可能的替代方案是改用動態註冊機構。 這將會以動態方式註冊類型，但移除標頭檔需求，以強制啟動成本。

最後 straw 的解決方案是使用較舊版本的 Xamarin，其中一個支援您的專案所需的 SDK。

<a name="MM0097" />

#### <a name="mm0097-machineconfig-file-0-can-not-be-found"></a>MM0097：找不到 machine.config 檔案 '{0}'。

<a name="MM0098" />

#### <a name="mm0098-aot-compilation-is-only-available-on-unified"></a>MM0098： AOT 編譯僅適用于整合

<a name="MM0099" />

#### <a name="mm0099-internal-error-0-please-file-a-bug-report-with-a-test-case-httpsbugzillaxamarincom"></a>MM0099：內部錯誤 {0}。 請以測試案例（ https://bugzilla.xamarin.com)提出錯誤報表。

<a name="MM0114" />

#### <a name="mm0114-hybrid-aot-compilation-requires-all-assemblies-to-be-aot-compiled"></a>MM0114：混合式 AOT 編譯需要所有元件都是已編譯的 AOT。

<a name="MM0129" />

#### <a name="mm0129-debugging-symbol-file-for--does-not-match-the-assembly-and-is-ignored"></a>MM0129：對 ' * ' 的符號檔進行偵錯工具不符合元件，因此會予以忽略。

無法載入所指定元件的「pdb」（僅限便攜 pdb）或 .mdb 檔案–的偵錯工具。

這通常表示元件比符號更新或更舊。 因為它們不符合它們，所以不能使用它們，而且會忽略符號。

這個警告不會影響所建立的應用程式，不過您可能無法完全進行完整的調試（尤其是來自指定元件的程式碼）。 此外，例外狀況、堆疊追蹤和損毀報告可能會遺漏某些資訊。

請將此問題回報給元件套件的發行者（例如 NuGet 作者），以便在未來的版本中修正這種情況。

<a name="MM0130" />

#### <a name="mm0130-no-root-assemblies-found-you-should-provide-at-least-one-root-assembly"></a>MM0130：找不到根元件。 您至少應提供一個根元件。

執行 `--runregistrar`時，至少應該提供一個根元件。

<a name="MM0131" />

#### <a name="mm0131-product-assembly-0-not-found-in-assembly-list-1"></a>MM0131：在元件清單中找不到產品元件 '{0}'： '{1}'

執行 `--runregistrar`時，元件清單應包含產品元件 XamMac。

<a name="MM0132" />

#### <a name="mm0132-unknown-optimization--valid-values-are-"></a>MM0132：未知的優化： \*。 有效的值為： \*

無法辨識指定的優化。

接受的格式為 `[+|-]optimization-name`，其中 `optimization-name` 是錯誤訊息中所列的其中一個值。

如需每個優化的完整描述，請參閱[組建優化](~/cross-platform/macios/optimizations.md)。

<a name="MM0133" />

#### <a name="mm0133-found-more-than-1-assembly-matching-0-choosing-first-1"></a>MM0133：找到一個以上的元件符合 '{0}' 第一次選擇： '{1}'

<a name="MM0134" />

#### <a name="mm0134-32-bit-applications-should-be-migrated-to-64-bit"></a>MM0134：32位應用程式應該遷移至64位。

Apple 已宣佈不允許 macOS App Store 提交32位應用程式（自1月2018日起）。

此外，32位應用程式將不會在高階「沒有危害」的 macOS 版本上執行。

如需詳細資訊： https://developer.apple.com/news/?id=06282017a

請考慮將您的應用程式和任何相依性更新為64位。

<a name="MM0135" />

#### <a name="mm0135-did-not-link-system-framework-0-referenced-by-assembly-1-because-it-was-introduced-in-2-3-and-were-using-the-2-4-sdk"></a>MM0135：未連結系統架構 '{0}' （由元件 '{1}' 所參考），因為它是在 {2} {3}中引進，而且我們正在使用 {2} {4} SDK。

為了建立您的應用程式，Xamarin 必須連結到系統程式庫，其中有些是根據錯誤訊息中指定的 SDK 版本而定。 由於您使用的是較舊版本的 SDK，因此對這些 Api 的叫用可能會在執行時間失敗。

若要修正此錯誤，建議的方法是升級 Xcode 以取得所需的 SDK。 如果您已安裝多個版本的 Xcode，或想要在非預設位置中使用 Xcode，請務必在 IDE 的喜好設定中設定正確的 Xcode 位置。

或者，啟用 managed[連結器](https://docs.microsoft.com/xamarin/mac/deploy-test/linker)以移除未使用的 api，包括（在大多數情況下）需要指定程式庫的新資源。 不過，如果您的專案所需的 Api 與您的 Xcode 所提供的版本不同，則這將無法正常執行。

作為最後一個 straw 的解決方案，請使用不需要這些新 Sdk 在組建程式期間存在的舊版 Xamarin. Mac。

## <a name="mm1xxx-file-copy--symlinks-project-related"></a>MM1xxx： file copy/符號連結（專案相關）

<a name="MM1034" />

#### <a name="mm1034-could-not-create-symlink-file---target-error-number"></a>MM1034：無法建立符號 ' {file} '-> ' {target} '：錯誤 {number}

### <a name="mm14xx-product-assemblies"></a>MM14xx：產品元件

<a name="MM1401" />

#### <a name="mm1401-the-required-0-assembly-is-missing-from-the-references"></a>MM1401：參考中遺漏必要的 '{0}' 元件

<a name="MM1402" />

#### <a name="mm1402-the-assembly-0-is-not-compatible-with-this-tool"></a>MM1402：元件 '{0}' 與此工具不相容

<a name="MM1403" />

#### <a name="mm1403-0-1-could-not-be-found-target-framework-0-is-unusable-to-package-the-application"></a>MM1403：找不到 {0} '{1}'。 目標 framework '{0}' 無法用來封裝應用程式。

<a name="MM1404" />

#### <a name="mm1404-target-framework-0-is-invalid"></a>MM1404：目標 framework '{0}' 無效。

<a name="MM1405" />

#### <a name="mm1405-usefullxammacframework-must-always-target-framework-net-45-not-0-which-is-invalid"></a>MM1405： useFullXamMacFramework 必須一律以 framework .NET 4.5 為目標，而不是 '{0}'，這是不正確

<a name="MM1406" />

#### <a name="mm1406-target-framework-0-is-invalid-when-targeting-xamarinmac-45-net-framwork"></a>MM1406：目標 framework '{0}' 在以 Xamarin. Mac 4.5 .NET framwork 為目標時無效。

<a name="MM1407" />

#### <a name="mm1407-mismatch-between-xamarinmac-reference-0-and-target-framework-selected-1"></a>MM1407： Xamarin. Mac 參考 '{0}' 與目標 framework 選取的 '{1}' 不相符。

### <a name="mm15xx-assembly-gathering-not-requiring-linker-errors"></a>MM15xx：元件收集（不需要連結器）錯誤

<a name="MM1501" />

#### <a name="mm1501-can-not-resolve-reference-0"></a>MM1501：無法解析參考： {0}

### <a name="machocs"></a>MachO.cs

<a name="MM1600" />

#### <a name="mm1600-not-a-mach-o-dynamic-library-unknown-header-0x0-1"></a>MM1600：不是符合-O 動態連結程式庫（未知的標頭 ' 0x{0}'）： {1}。

<a name="MM1601" />

#### <a name="mm1601-not-a-static-library-unknown-header-0-1"></a>MM1601：不是靜態程式庫（未知的標頭 '{0}'）： {1}。

<a name="MM1602" />

#### <a name="mm1602-not-a-mach-o-dynamic-library-unknown-header-0x0-1"></a>MM1602：不是符合-O 動態連結程式庫（未知的標頭 ' 0x{0}'）： {1}。

<a name="MM1603" />

#### <a name="mm1603-unknown-format-for-fat-entry-at-position-0-in-1"></a>MM1603： {1}中位置 {0} 的 fat 專案格式未知。

<a name="MM1604" />

#### <a name="mm1604-file-of-type-0-is-not-a-macho-file-1"></a>MM1604： {0} 類型的檔案不是 MachO 檔案（{1}）。

## <a name="mm2xxx-linker"></a>MM2xxx：連結器

### <a name="mm20xx-linker-general-errors"></a>MM20xx：連結器（一般）錯誤

<a name="MM2001" />

#### <a name="mm2001-could-not-link-assemblies"></a>MM2001：無法連結元件

<a name="MM2002" />

#### <a name="mm2002-can-not-resolve-reference-0"></a>MM2002：無法解析參考： {0}

<a name="MM2003" />

#### <a name="mm2003-option-0-will-be-ignored-since-linking-is-disabled"></a>MM2003：將忽略選項 '{0}'，因為已停用連結

<a name="MM2004" />

#### <a name="mm2004-extra-linker-definitions-file-0-could-not-be-located"></a>MM2004：找不到額外的連結器定義檔 '{0}'。

<a name="MM2005" />

#### <a name="mm2005-definitions-from-0-could-not-be-parsed"></a>MM2005：無法剖析 '{0}' 中的定義。

<a name="MM2006" />

#### <a name="mm2006-native-library-0-was-referenced-but-could-not-be-found"></a>MM2006：已參考原生程式庫 '{0}'，但找不到。

<a name="MM2007" />

#### <a name="mm2007-xamarinmac-unified-api-against-a-full-net-profile-does-not-support-linking-pass-the--nolink-flag"></a>MM2007：針對完整 .NET 設定檔 Unified API 的 Xamarin. Mac 不支援連結。 傳遞-nolink 旗標。

<a name="MM2009" />

#### <a name="mm2009-referenced-by-01------this-message-is-related-to-mm2006-"></a>MM2009：由 {0}所參考。{1}\*\* 此訊息與 MM2006 \*相關聯 \*

<a name="MM2010" />

#### <a name="mm2010-unknown-httpmessagehandler-0-valid-values-are-httpclienthandler-default-cfnetworkhandler-or-nsurlsessionhandler"></a>MM2010：未知的 HttpMessageHandler `{0}`。 有效值為 HttpClientHandler （預設值）、CFNetworkHandler 或 NSUrlSessionHandler

<a name="MM2011" />

#### <a name="mm2011-unknown-tlsprovider-0--valid-values-are-default-or-appletls"></a>MM2011：未知的 TLSProvider '{0}。  有效值為 default 或 appletls

<a name="MM2012" />

#### <a name="mm2012-only-first-0-of-1-referenced-by-warnings-shown--this-message-related-to-2009-"></a>MM2012：只會顯示 {1} 「參考者」警告的第一個 {0}。 \*\* 與 2009 \*相關的此訊息 \*

<a name="MM2013" />

#### <a name="mm2013-failed-to-resolve-the-reference-to-0-referenced-in-1-the-app-will-not-include-the-referenced-assembly-and-may-fail-at-runtime"></a>MM2013：無法解析 "{1}" 中參考的 "{0}" 參考。 應用程式不會包含參考的元件，而且可能會在執行時間失敗。

<a name="MM2014" />

#### <a name="mm2014-xamarinmac-extensions-do-not-support-linking-request-for-linking-will-be-ignored--this-message-is-obsolete-in-xm-36-"></a>MM2014： Xamarin 副檔名不支援連結。 將略過連結的要求。 \*\* 此訊息在 XM 3.6 + \*中已過時 \*

<!-- 2015 used by mtouch -->

<a name="MM2016" />

#### <a name="mm2016-invalid-tlsprovider-0-option-the-only-valid-value-1-will-be-used"></a>MM2016：不正確 TlsProvider `{0}` 選項。 只會使用 `{1}` 的有效值。

<a name="MM2017" />

#### <a name="mm2017-could-not-process-xml-description-0"></a>MM2017：無法處理 XML 描述： {0}

<a name="MM202x" />

#### <a name="mm202x-binding-optimizer-failed-processing-"></a>MM202x： Binding 優化工具無法處理 `...`。

<a name="MM2100" />

#### <a name="mm2100-xamarinmac-classic-api-does-not-support-platform-linking"></a>MM2100： Xamarin. Mac Classic API 不支援平臺連結。

<a name="MM2103" />

#### <a name="mm2103-error-processing-assembly--"></a>MM2103：處理元件 '\*' 時發生錯誤： *

處理元件時發生未預期的錯誤。

造成此問題的元件是在錯誤訊息中所命名。 若要修正這個問題，必須在[錯誤報表](https://bugzilla.xamarin.com)中提供元件，以及啟用詳細資訊的完整組建記錄檔（也就是在**其他 mtouch 引數**中 `-v -v -v -v`）。

<a name="MM2104" />

#### <a name="mm2104-unable-to-link-assembly-0-as-it-is-mixed-mode"></a>MM2104：無法連結元件 '{0}'，因為它是混合模式。

連結器無法處理混合模式元件。

如需混合模式元件的詳細資訊，請參閱 https://docs.microsoft.com/cpp/dotnet/mixed-native-and-managed-assemblies。

<a name="MM2106" />

#### <a name="mm2106-could-not-optimize-the-call-to-blockliteralsetupblockunsafe-in--at-offset--because-"></a>MM2106：無法在 \* 的位移 \* 中優化 BlockLiteral. SetupBlock [Unsafe] 的呼叫，因為 \*。

當連結器無法優化 `BlockLiteral.SetupBlock` 或 `Block.SetupBlockUnsafe`的呼叫時，會報告此警告。

訊息會指向呼叫 `BlockLiteral.SetupBlock[Unsafe]`的方法，而且也可能提供呼叫無法優化之原因的線索。

請提出[問題](https://github.com/xamarin/xamarin-macios/issues/new)以及完整的組建記錄檔，讓我們可以調查發生了什麼錯誤，並可能在未來啟用更多案例。

<a name="MM2107" />

#### <a name="mm2107-its-not-safe-to-remove-the-dynamic-registrar-because-reasons"></a>MM2107：移除動態註冊機構並不安全，因為 {原因}

當開發人員要求移除動態註冊機構（藉由將 `--optimize:remove-dynamic-registrar` 傳遞至 mmp）時，連結器會報告此警告，但連結器會判斷不安全。

若要移除警告，請移除 mmp 的優化引數，或傳遞 `--nowarn:2107` 以忽略它。

根據預設，每當可能且安全地執行此動作時，就會自動啟用此選項。

<a name="MM2108" />

#### <a name="mm2108-0-was-stripped-of-architectures-except-1-to-comply-with-app-store-restrictions-this-could-break-exisiting-codesigning-signatures-consider-stripping-the-library-with-lipo-or-disabling-with---optimize-trim-architectures"></a>MM2108： '{0}' 已移除架構，但 '{1}' 除外，以符合 App Store 的限制。 這可能會中斷現有的 codesigning 簽章。 請考慮使用 lipo 將程式庫移除，或使用--optimize =-trim-架構來停用）;

App Store 現在會拒絕包含程式庫和包含32位變數之架構的應用程式。 複製到最終的應用程式套件組合時，程式庫已移除未使用的架構。

這是一般安全的功能，而且會減少應用程式套件組合大小，以做為額外的好處。 不過，任何已簽署程式碼的配套架構都會使其簽章失效（而且稍後如果應用程式已簽署，則會重新簽署）。

請考慮使用 `lipo` 從來源程式庫永久移除不必要的架構。 如果應用程式未發佈至 App Store，則可以藉由將 `--optimize=-trim-architectures` 傳遞為額外的 MMP 引數來停用此移除。

<a name="MM2109"/>

#### <a name="mm2109-xamarinmac-classic-api-does-not-support-platform-linking"></a>MM2109： Xamarin. Mac Classic API 不支援平臺連結。

## <a name="mm3xxx-aot"></a>MM3xxx： AOT

### <a name="mm30xx-aot-general-errors"></a>MM30xx： AOT （一般）錯誤

<a name="MM3001" />

#### <a name="mm3001-could-not-aot-the-assembly-0"></a>MM3001：無法將元件 '{0}' 進行 AOT

<!-- 3002 used by mtouch -->
<!-- 3003 used by mtouch -->
<!-- 3004 used by mtouch -->
<!-- 3005 used by mtouch -->
<!-- 3006 used by mtouch -->
<!-- 3007 used by mtouch -->
<!-- 3008 used by mtouch -->

<a name="MM3009" />

#### <a name="mm3009-aot-of-0-was-requested-but-was-not-found"></a>MM3009：已要求 '{0}' 的 AOT，但找不到

<a name="MM3010" />

#### <a name="mm3010-exclusion-of-aot-of-0-was-requested-but-was-not-found"></a>MM3010：已要求排除 '{0}' 的 AOT，但找不到

## <a name="mm4xxx-code-generation"></a>MM4xxx：程式碼產生

### <a name="mm40xx-driverm"></a>MM40xx： driver. m

<a name="MM4001" />

#### <a name="mm4001-the-main-template-could-not-be-expanded-to-0"></a>MM4001：無法將主要範本展開為 `{0}`。

### <a name="mm41xx-registrar"></a>MM41xx：註冊機構

<a name="MM4134" />

#### <a name="mm4134-your-application-is-using-the-0-framework-which-isnt-included-in-the-macos-sdk-youre-using-to-build-your-app-this-framework-was-introduced-in-osx-2-while-youre-building-with-the-macos-1-sdk-this-configuration-is-not-supported-with-the-static-registrar-pass---registrardynamic-as-an-additional-mmp-argument-in-your-projects-mac-build-option-to-select-alternatively-select-a-newer-sdk-in-your-apps-mac-build-options"></a>MM4134：您的應用程式使用「{0}」架構，其未包含在您用來建立應用程式的 MacOS SDK 中（此架構是在 OSX {2}中引進，而您是使用 MacOS {1} SDK 來建立）。靜態註冊機構（傳遞--註冊程式： dynamic 作為您專案的 Mac 組建選項中的其他 mmp 引數）不支援此設定。 或者，在您的應用程式的 Mac 組建選項中選取較新的 SDK。

<a name="MM4173" />

#### <a name="mm4173-the-registrar-cant-compute-the-block-signature-for-the-delegate-of-type-delegate-type-in-the-method-method-because-"></a>MM4173：註冊機構無法計算方法 {method} 中類型 {delegate-類型} 委派的區塊簽章，因為 *。

這是一則警告，表示註冊機構無法將指定方法的區塊簽章插入產生的註冊機構程式碼，因為註冊機構無法計算它。

這表示區塊簽章必須在執行時間進行計算，但速度較慢。

此警告目前有兩個可能的原因：

1. Managed 委派的類型為 `System.Delegate` 或 `System.MulticastDelegate`。 這些類型不代表特定的簽章，這表示註冊機構無法計算對應的原生簽章。 在此情況下，修正程式是使用區塊的特定委派類型（也可以將 `--nowarn:4173` 新增為專案 Mac 組建選項中的其他 mmp 引數）來忽略警告。
2. 註冊機構找不到委派的 `Invoke` 方法。 這不應該發生，因此請提出測試專案的[問題](https://github.com/xamarin/xamarin-macios/issues/new)，讓我們可以修正它。

<a name="MT4174" />

#### <a name="mt4174-unable-to-locate-the-block-to-delegate-conversion-method-for-the-method-methods-parameter-parameter"></a>MT4174：找不到要委派方法 {method} 的參數 # {parameter} 的轉換方法的區塊。

這是一則警告，表示靜態註冊機構找不到建立目標 C 區塊之委派的方法。 嘗試在執行時間進行，以尋找方法，但也可能會失敗（MT8009 例外狀況）。

此警告的其中一個可能原因是手動撰寫使用區塊之 API 的系結。 建議使用系結專案來系結目標-C 程式碼（特別是當它牽涉到區塊時），因為在手動執行時，將它設為正確。

如果不是這種情況，請提出測試案例的[問題](https://github.com/xamarin/xamarin-macios/issues/new)。

## <a name="mm5xxx-gcc-and-toolchain"></a>MM5xxx： GCC 和工具鏈

### <a name="mm51xx-compilation"></a>MM51xx：編譯

<a name="MM5101" />

#### <a name="mm5101-missing-0-compiler-please-install-xcode-command-line-tools-component"></a>MM5101：遺漏 '{0}' 編譯器。 請安裝 Xcode 的命令列工具元件。

<!-- 5102 used by mtouch -->

<a name="MM5103" />

#### <a name="mm5103-failed-to-compile-error-code---0-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MM5103：無法編譯。 錯誤碼-{0}。 請在 http://bugzilla.xamarin.com 提出錯誤報表

<!-- 5104 used by mtouch -->

### <a name="mm52xx-linking"></a>MM52xx：連結

<a name="MM5202" />

#### <a name="mm5202-monoframework-mdk-is-missing-please-install-the-mdk-for-your-monoframework-version-from-httpmono-projectcomdownloads"></a>MM5202： Mono. framework MDK 遺失。 請從 http://mono-project.com/Downloads 安裝您的 Mono. framework 版本的 MDK

<a name="MM5203" />

#### <a name="mm5203-cant-find-libxammaca-likely-because-of-a-corrupted-xamarinmac-installation-please-reinstall-xamarinmac"></a>MM5203：找不到 libxammac，可能是因為已損毀的 Xamarin. Mac 安裝。 請重新安裝 Xamarin. Mac。

<a name="MM5204" />

#### <a name="mm5204-invalid-architecture-x86_64-is-only-supported-on-non-classic-profiles"></a>MM5204：不正確架構。 只有非傳統設定檔才支援 x86_64。

<a name="MM5205" />

#### <a name="mm5205-invalid-architecture-0-valid-architectures-are-i386-and-x86_64-when---profilemobile"></a>MM5205：不正確架構 '{0}'。 有效的架構為 i386 和 x86_64 （當--profile = mobile 時）。

<a name="MM5218" />

#### <a name="mm5218-cant-ignore-the-dynamic-symbol-symbol---ignore-dynamic-symbolsymbol-because-it-was-not-detected-as-a-dynamic-symbol"></a>MM5218：無法忽略動態符號 {symbol} （--忽略-dynamic-符號 = {symbol}），因為未偵測到它是動態符號。

請參閱對[等的 mtouch 警告](~/ios/troubleshooting/mtouch-errors.md#MT5218)。

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

### <a name="mm53xx-other-tools"></a>MM53xx：其他工具

<a name="MM5301" />

#### <a name="mm5301-pkg-config-could-not-be-found-please-install-the-monoframework-from-httpmono-projectcomdownloads"></a>MM5301：找不到 .pkg。 請從 http://mono-project.com/Downloads 安裝 Mono. framework

<!-- 5302 used by mtouch -->
<!-- 5303 used by mtouch -->
<!-- 5304 used by mtouch -->

<a name="MM5305" />

#### <a name="mm5305-missing-otool-tool-please-install-xcode-command-line-tools-component"></a>MM5305：遺漏 ' otool ' 工具。 請安裝 Xcode ' 命令列工具 ' 元件

<a name="MM5306" />

#### <a name="mm5306-missing-dependencies-please-install-xcode-command-line-tools-component"></a>MM5306：缺少相依性。 請安裝 Xcode ' 命令列工具 ' 元件

<a name="MM5308" />

#### <a name="mm5308-xcode-license-agreement-may-not-have-been-accepted--please-launch-xcode"></a>MM5308： Xcode 授權合約可能尚未被接受。  請啟動 Xcode。

<a name="MM5309" />

#### <a name="mm5309-native-linking-failed-with-error-code-1-check-build-log-for-details"></a>MM5309：原生連結失敗，錯誤碼為1。 檢查組建記錄檔以取得詳細資料。

<a name="MM5310" />

#### <a name="mm5310-install_name_tool-failed-with-an-error-code-0-check-build-log-for-details"></a>MM5310： install_name_tool 失敗，錯誤碼為 '{0}'。 檢查組建記錄檔以取得詳細資料。

<a name="MM5311" />

#### <a name="mm5311-lipo-failed-with-an-error-code-0-check-build-log-for-details"></a>MM5311： lipo 失敗，錯誤碼為 '{0}'。 檢查組建記錄檔以取得詳細資料。

<!-- MM6xxx: mmp internal tools -->
<!-- MM7xxx: reserved -->

## <a name="mm8xxx-runtime"></a>MM8xxx：執行時間

### <a name="mm800x-misc"></a>MM800x：其他

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

#### <a name="mm8017-the-boehm-garbage-collector-is-not-supported-please-use-sgen-instead"></a>MM8017：不支援 Boehm 垃圾收集行程。 請改用 SGen。

<a name="MM8025" />

#### <a name="mm8025-failed-to-compute-the-token-reference-for-the-type-typeassemblyqualifiedname-because-reasons"></a>MM8025：無法計算類型 ' {type ' 的權杖參考。AssemblyQualifiedName} '，因為 {原因}

這表示 Xamarin. Mac 中的錯誤。 請在[https://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=Xamarin.Mac)提出 bug。

可能的因應措施是在專案的 Mac 組建選項中，將 `--optimize:-register-protocols` 當做額外的 mmp 引數傳遞，藉以停用 `register-protocols` 優化。

<a name="MM8026" />

#### <a name="mm8026--is-not-supported-when-the-dynamic-registrar-has-been-linked-away"></a>當動態註冊機構已連結出去時，不支援 MM8026： *。

這通常表示 Xamarin 中的錯誤（bug），因為如果需要，動態註冊機構不應連結。 請在[https://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)提出 bug。

藉由將 `--optimize=-remove-dynamic-registrar` 新增至專案 Mac 組建選項中的其他 mmp 引數，可以強制連結器保留動態註冊機構。
