---
title: 解決與 System.webserver 相關的問題。追蹤和 TPL 資料流程
description: PCL 案例研究：如何解決 Microsoft TPL Dataflow NuGet 套件有關 System.Diagnostics.Tracing 的問題？
ms.prod: xamarin
ms.assetid: 7986A556-382D-4D00-ACCF-3589B4029DE8
ms.date: 04/17/2018
author: davidortinau
ms.author: daortin
ms.openlocfilehash: e7c0bcc7450ab718659723293f995c83b4dc517a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73013580"
---
# <a name="pcl-case-study-how-can-i-resolve-problems-related-to-systemdiagnosticstracing-for-the-microsoft-tpl-dataflow-nuget-package"></a>PCL 案例研究：如何解決 Microsoft TPL Dataflow NuGet 套件有關 System.Diagnostics.Tracing 的問題？

> [!IMPORTANT]
> 這個特定的範例 `System.Diagnostic.Tracing` 不會再于最新版本的 Xamarin 中產生任何錯誤。 雖然建議的因應措施仍然有效，請注意「錯誤層級」一節中所述的一些 bug 已獲得修正。
> 此外，您還應該注意，.NET Standard 現在是執行跨平臺 .NET Api 的慣用方式。

## <a name="summary"></a>總結

Xamarin. iOS 和 Xamarin 不會將其允許的每個 PCL 設定檔的100% 實作為參考。 為了在 Visual Studio for Mac、Visual Studio 和 NuGet 套件管理員中實用的便利性，Xamarin 專案允許使用只具有_不完整_之執行的數個設定檔。 例如，不論是 Xamarin 或 Xamarin，目前都在「System.webserver」 PCL 命名空間中包含完整的類型。 嘗試使用 Microsoft TPL 資料流程 NuGet 封裝的預設 `portable-net45+win8+wpa81` 版本時，這項限制會導致三層錯誤。

## <a name="workaround-switch-the-app-project-to-reference-the-portable-net45win8wp8wpa81-version-of-the-tpl-dataflow-library"></a>因應措施：切換應用程式專案，以參考 TPL 資料流程程式庫的 `portable-net45+win8+wp8+wpa81` 版本

（這可避免三個錯誤層級，適用于所有最新版本的 Xamarin）。

1. 在文字編輯器中開啟應用程式專案 **.csproj**檔案。

2. 尋找看起來像這樣的程式程式碼：

    ```xml
    <HintPath>..\packages\Microsoft.Tpl.Dataflow.4.5.24\lib\portable-net45+win8+wpa81\System.Threading.Tasks.Dataflow.dll</HintPath>
    ```

3. 將 `portable-net45+win8+wpa81` 變更為 `portable-net45+win8+wp8+wpa81` （`+wp8` 新增）：

    ```xml
    <HintPath>..\packages\System.Threading.Tasks.Dataflow.4.5.25\lib\portable-net45+win8+wp8+wpa81\System.Threading.Tasks.Dataflow.dll</HintPath>
    ```

### <a name="explanation"></a>說明

`portable-net45+win8+wp8+wpa81` 版本的**連結**庫完全不會參考 system.servicemodel，因此它可以徹底避免這三個層_級_的問題。

### <a name="limitations"></a>限制

- `portable-net45+win8+wp8+wpa81` 版本的程式庫可能不包含 `portable-net45+win8+wpa81` 版本之功能的100%。

- 根據預設，NuGet 套件管理員會安裝 `portable-net45+win8+wpa81` 版本的 PCL NuGet 套件，因此您必須以手動方式調整參照。

## <a name="details-about-the-three-layers-of-errors"></a>三個錯誤層級的詳細資料

1. 從 Xamarin 的所有 Mac 版本（非公用錯誤34888），目前不存在**所有的 xamarin** 。 iOS 版本低於9.0 （或低於 Windows 上的 XamarinVS 3.11.1443）（已在中修正）[Bug 32388](https://bugzilla.xamarin.com/show_bug.cgi?id=32388)）。 此問題將會根據部署目標和連結器設定，導致下列其中一個錯誤：

    - FileNotFoundException：錯誤：載入元件時發生例外狀況：。無法載入元件 ' System.webserver. 追蹤，Version = 4.0.0.0，Culture = 中性，PublicKeyToken = b03f5f7f11d50a3a '。 這可能不存在於 Mono for Android 設定檔嗎？

    - 無法載入檔案或元件 ' System.webserver ' 或它的其中一個相依性。 系統找不到指定的檔案。 （FileNotFoundException）

    - MTOUCH： error MT3001：無法對元件 '/Users/macuser/Projects/TPLDataflow/UnifiedSingleViewIphone1/obj/iPhone/Debug/mtouch-cache/64/Build/System.Threading.Tasks.Dataflow.dll ' 進行 AOT

    - MTOUCH： error MT2002：無法解析元件： ' System.webserver，Version = 4.0.0.0，Culture = 中性，PublicKeyToken = b03f5f7f11d50a3a '

2. "System.servicemodel [" 中類型目前的 Mono 實](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs)缺少某些方法多載（[Bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337)）。 建立 Xamarin 應用程式時，此問題會導致下列其中一個連結器錯誤：

    - /Library/Frameworks/Mono.framework/External/xbuild/Xamarin/Android/Xamarin.Android.Common.targets：錯誤：執行工作 LinkAssemblies 時發生錯誤：錯誤 XA2006：中繼資料專案 ' System.object ' 的參考。 EventSource：：WriteEvent （system.string，System.object []） ' （定義于 ' system.web，Version = 4.5.24.0，Culture = 中性，PublicKeyToken = b03f5f7f11d50a3a '），來自 ' system.string，Version = 4.5.24.0，Culture = 中性，，無法解析 PublicKeyToken = b03f5f7f11d50a3a '。

    - MTOUCH： error MT2002：無法解析 "System.web：： WriteEvent （system.string，System.object []）" 從 "" 的參考。版本 = 4.0.0.0，文化特性 = 中性，PublicKeyToken =。b03f5f7f11d50a3a

3. 目前[在 "system.servicemodel" 中，類型的 Mono 實](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs)作為「虛擬」的_空_實（[Bug 34890](https://bugzilla.xamarin.com/show_bug.cgi?id=34890)）。 因此，在執行時間使用這些方法的任何嘗試，可能會產生非預期的結果。 針對 Microsoft TPL 資料流程程式庫的特定案例，似乎 `WriteEvent(System.Int32,System.Object[])` 的呼叫對於大部分的程式庫行為_而言_並不重要，因此「第2層」（[Bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337)，新增空的執行）的修正可能就足以滿足大部分的 Microsoft TPL 資料流程使用案例。

## <a name="questions--answers"></a>問題 & 解答

### <a name="i-was-able-to-leave-linking-enabled-with-the-portable-net45win8wpa81-version-of-the-library-on-older-versions-of-xamarinios-or-on-xamarinandroid-how-did-that-work"></a>我可以在舊版的 Xamarin 或 Xamarin 上，讓程式庫的 `portable-net45+win8+wpa81` 版本保持啟用。 這是怎麼做的？

#### <a name="answer"></a>答覆

如果您包含 `System.Diagnostics.Tracing.dll`_參考元件_的參考 \[1\] 而不是，則可以在舊版的 Xamarin. IOS 或 Mac 上的 Xamarin. Android_中，取得_組建以完成「成功」（已啟用連結）。_外觀元件_\[2]，但可惜的是，這不是「正確」的解決方法。 參考元件僅適用于建立_可移植_的程式庫時，而不是平臺特定的程式碼（例如應用程式）。 嘗試_執行_包含在參考元件中的程式碼（而不只是針對它建立）可能會產生非預期的結果。 Mono 小組將遺漏的 `WriteEvent(System.Int32,System.Object[])` 多載加入至[`EventSource`](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs)類型（[Bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337)），是正確的修正程式。 現在，最佳選項是切換到 Microsoft TPL 資料流程程式庫的 `portable-net45+win8+wp8+wpa81` 版本，如上面的因應措施一節中所述。

（對於可能閱讀本文的任何人，請參閱 StackOverflow （<https://stackoverflow.com/a/23591322/2561894>）的相關簡潔答案，請注意，此處_未_提及參考元件和外觀元件之間的區別）。

**\[1\] 「參考元件」位置**

Windows：`C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETPortable\v4.5\System.Diagnostics.Tracing.dll`

Mac （Mono）： `/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/xbuild-frameworks/.NETPortable/v4.5/System.Diagnostics.Tracing.dll`

**\[2\] 「外觀元件」位置**

Windows：`C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.5\Facades\System.Diagnostics.Tracing.dll`

Mac （Mono）： `/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/4.5/Facades/System.Diagnostics.Tracing.dll`

### <a name="will-it-help-if-i-manually-add-a-reference-to-the-systemdiagnosticstracing-facade-assembly"></a>如果我以手動方式將參考新增至「診斷」外觀元件，它會有説明嗎？

_我特別可以使用這2個步驟來解決問題嗎？_

1. _將 [`System.Diagnostics.Tracing.dll` 外觀元件] 從下列其中一個位置複製到應用程式專案資料夾：_

    Windows：`C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.5\Facades\System.Diagnostics.Tracing.dll`

    Mac （Mono）： `/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/4.5/Facades/System.Diagnostics.Tracing.dll`

2. _在 Xamarin 或 Xamarin 應用程式專案中加入外觀元件的參考。_

#### <a name="answer"></a>答覆

否，這不會有説明。

- 若為 Xamarin. iOS 9.0 或 Windows 上任何最新版本的 Xamarin，此因應措施會嚴格地重複，而且可能會導致類似于「元件」系統的編譯錯誤。已匯入具有相同身分識別的「診斷」。」。

- 針對 Xamarin. iOS 8.10 或更低版本，或適用于 Mac 上的 Xamarin，這種因應措施會有所説明，但_只有_「第1層」遺失元件問題。 它_不_會解決「第2層」連結器錯誤，因此它並不是完整的解決方案。

### <a name="can-i-use-the-systemdiagnosticstracing-nuget-packagehttpswwwnugetorgpackagessystemdiagnosticstracing-to-solve-the-problem"></a>我可以使用 [[追蹤] NuGet 套件](https://www.nuget.org/packages/System.Diagnostics.Tracing/)來解決問題嗎？

#### <a name="answer"></a>答覆

否，NuGet 3.0 "system.servicemodel" 套件只包含 "DNXCore50" 和 "netcore50" 的平臺特定的執行。 它明確_省略_了 xamarin （"MonoAndroid"）和 xamarin （"MonoTouch" 和 "xamarinios"）的實作為。 這表示，安裝套件_不會影響_Xamarin. Android 和 Xamarin. iOS 專案。 NuGet 套件假設這兩個平臺都提供_自己_的類型執行。 這項假設是「正確」，因為 Mono_確實具有命名空間的執行_，但如以下所述：「關於錯誤的三個層級的詳細資料」的點 \#2 和 \#3，則表示目前不完整的執行。 因此，Mono 小組可以解決[bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337)和[bug 34890](https://bugzilla.xamarin.com/show_bug.cgi?id=34890)的適當修正。

## <a name="next-steps"></a>後續步驟

如需進一步的協助，請洽詢我們，或即使在使用上述資訊之後仍然會發生此問題，請參閱[什麼是適用于 Xamarin 的支援選項？](~/cross-platform/troubleshooting/support-options.md)以取得連絡人選項、建議，以及如何在需要時提出新 bug 的相關資訊.
