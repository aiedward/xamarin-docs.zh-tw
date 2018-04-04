---
title: PCL 案例研究： 如何解決 System.Diagnostics.Tracing Microsoft TPL 資料流程 NuGet 套件的相關問題？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7986A556-382D-4D00-ACCF-3589B4029DE8
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: 04814b78fd035005aabd8b9229d36bbda17ba140
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="pcl-case-study-how-can-i-resolve-problems-related-to-systemdiagnosticstracing-for-the-microsoft-tpl-dataflow-nuget-package"></a>PCL 案例研究： 如何解決 System.Diagnostics.Tracing Microsoft TPL 資料流程 NuGet 套件的相關問題？

## <a name="summary"></a>總結

Xamarin.iOS 和 Xamarin.Android 不會實作每個可做為參考 PCL 設定檔的 100%。 Xamarin 專案方便實際在 Visual Studio for Mac、 Visual Studio 和 NuGet 套件管理員中，允許使用數個設定檔，只有_不完整_實作。 例如，Xamarin.iOS 和 Xamarin.Android 都不目前包含的完整實作 「 System.Diagnostics.Tracing"PCL 中的型別命名空間。 這項限制會導致 3 層錯誤時嘗試使用預設`portable-net45+win8+wpa81`Microsoft TPL 資料流程 NuGet 封裝版本。


## <a name="workaround-switch-the-app-project-to-reference-the-portable-net45win8wp8wpa81-version-of-the-tpl-dataflow-library"></a>因應措施： 切換應用程式專案參考`portable-net45+win8+wp8+wpa81`TPL 資料流程程式庫版本

（這可避免錯誤和適用於最新版 Xamarin 所有 3 層級。）

1. 開啟應用程式專案`.csproj`文字編輯器中的檔案。

2. 找到行看起來像這樣：

    ```xml
    <HintPath>..\packages\Microsoft.Tpl.Dataflow.4.5.24\lib\portable-net45+win8+wpa81\System.Threading.Tasks.Dataflow.dll</HintPath>
    ```

3. 將 `portable-net45+win8+wpa81` 變更為 `portable-net45+win8+**wp8**+wpa81`：

    ```xml
    <HintPath>..\packages\System.Threading.Tasks.Dataflow.4.5.25\lib\portable-net45+win8+wp8+wpa81\System.Threading.Tasks.Dataflow.dll</HintPath>
    ```

### <a name="explanation"></a>說明

`portable-net45+win8+wp8+wpa81`版本的程式庫不會參考 「 System.Diagnostics.Tracing.dll"_完全_，因此它完全可以避免 3 的所有圖層的問題。

### <a name="limitations"></a>限制

- `portable-net45+win8+wp8+wpa81`程式庫版本可能不會包含 100%的功能`portable-net45+win8+wpa81`版本。

- NuGet 套件管理員安裝`portable-net45+win8+wpa81`根據預設，因此您必須以手動方式調整參考 PCL NuGet 封裝版本。




## <a name="details-about-the-3-layers-of-errors"></a>3 層級中的錯誤詳細資料

1. `System.Diagnostics.Tracing.dll`片面組件是目前的 Xamarin.Android (非公用 Bug 34888) 的所有 Mac 版本都中並不都存在所有 Xamarin.iOS 版本小於 9.0 （或低於 XamarinVS 3.11.1443 Windows 上） (固定[Bug 32388](https://bugzilla.xamarin.com/show_bug.cgi?id=32388)). 這個問題會導致下列錯誤，取決於部署目標和連結器的其中一個設定：

    - > Xamarin.Android.Common.targets： 錯誤： 載入組件時發生例外狀況： System.IO.FileNotFoundException： 無法載入組件 ' System.Diagnostics.Tracing，Version = 4.0.0.0，Culture = neutral，PublicKeyToken = b03f5f7f11d50a3a'。 可能是它不存在於 Mono Android 設定檔嗎？

    - > 無法載入檔案或組件 'System.Diagnostics.Tracing' 或其中一個相依性。 系統找不到指定的檔案。 (System.IO.FileNotFoundException)

    - > MTOUCH： 錯誤 MT3001： 無法 AOT 組件 ' / Users/macuser/Projects/TPLDataflow/UnifiedSingleViewIphone1/obj/iPhone/Debug/mtouch-cache/64/Build/System.Threading.Tasks.Dataflow.dll '

    - > MTOUCH： 錯誤 MT2002： 無法解析組件: ' System.Diagnostics.Tracing，Version = 4.0.0.0，Culture = neutral，PublicKeyToken = b03f5f7f11d50a3a'



2. 目前["System.Diagnostics.Tracing"中類型的 Mono 實作](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs)遺漏某些方法多載 ([Bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337))。 這個問題會導致下列的連結器錯誤的其中一個建置 Xamarin 應用程式時：

    - > / Library/Frameworks/Mono.framework/External/xbuild/Xamarin/Android/Xamarin.Android.Common.targets： 錯誤： 錯誤正在執行工作 LinkAssemblies： 錯誤 XA2006： 中繼資料項目參考 ' System.Void System.Diagnostics.Tracing.EventSource::WriteEvent(System.Int32,System.Object[])' (定義中 'System.Threading.Tasks.Dataflow，Version = 4.5.24.0，Culture = neutral，PublicKeyToken = b03f5f7f11d50a3a') 從' System.Threading.Tasks.Dataflow、 版本 = 4.5.24.0，文化特性 = neutral，PublicKeyToken = b03f5f7f11d50a3a' 無法解析。

    - > MTOUCH： 錯誤 MT2002： 無法解析"System.Void System.Diagnostics.Tracing.EventSource::WriteEvent(System.Int32,System.Object[])"參考"System.Diagnostics.Tracing，Version = 4.0.0.0，Culture = neutral，PublicKeyToken =b03f5f7f11d50a3a"


3. 目前["System.Diagnostics.Tracing"中類型的 Mono 實作](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs)目前也是_空_"dummy"的實作 ([Bug 34890](https://bugzilla.xamarin.com/show_bug.cgi?id=34890))。 因此，任何嘗試在執行階段使用這些方法可能會產生非預期的結果。 如_特定_案例似乎 TPL 資料流程程式庫，呼叫`WriteEvent(System.Int32,System.Object[])`不是不可或缺的大部分的程式庫的行為，所以 「 layer 2 」 的修正 ([Bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337)，加入空白實作） 會 likey 便已足夠大多數 Microsoft TPL 資料流程的使用案例。




## <a name="questions--answers"></a>問題與解答


###<a name="i-was-able-to-leave-linking-enabled-with-the-codeportable-net45win8wpa81code-version-of-the-library-on-older-versions-of-xamarinios-or-on-xamarinandroid-how-did-that-work"></a>「 我無法將保留連結以啟用<code>portable-net45+win8+wpa81</code>Xamarin.Android 或 Xamarin.iOS 的舊版本上的程式庫版本。 未的運作方式為何？ 」

#### <a name="answer"></a>回應

它是_可能_獲得建置完成 「 成功 」 （連結已啟用） 或在 Mac 上的 Xamarin.Android Xamarin.iOS 的舊版本中包含的參考，如果`System.Diagnostics.Tracing.dll`_參考組件_\[1\]而_片面組件_ \[2]，但不幸的是這不是 「 正確 」 的因應措施。 參考組件只是要在建置時使用_可攜式類別庫_，例如應用程式的平台專屬程式碼。 嘗試_執行_參考組件 （而非只是針對它的組建） 中所包含的程式碼是有可能產生非預期的結果。 單聲道的小組成員加入遺漏也就是正確的修正`WriteEvent(System.Int32,System.Object[])`來多載[ `EventSource` ](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs)類型 ([Bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337))。 現在最好的選擇是要切換至`portable-net45+win8+wp8+wpa81`上述因應措施 > 一節中所述的 Microsoft TPL 資料流程程式庫版本。

(可能會在之後看到相關的較舊、 簡潔回應從 StackOverflow 閱讀本文的任何人 (<http://stackoverflow.com/a/23591322/2561894>)，請注意，參考組件和片面組件之間的差別是_不_那里提到。)


**\[1\] "參考組件 」 的位置**

Windows：`C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETPortable\v4.5\System.Diagnostics.Tracing.dll`

Mac （單聲道）： `/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/xbuild-frameworks/.NETPortable/v4.5/System.Diagnostics.Tracing.dll`

**\[2\] 」 外觀組件 」 的位置**

Windows: <code>C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.5\<strong>Facades</strong>\System.Diagnostics.Tracing.dll</code>

Mac （單聲道）： <code>/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/4.5/<strong>Facades</strong>/System.Diagnostics.Tracing.dll</code>


###<a name="will-it-help-if-i-manually-add-a-reference-to-the-systemdiagnosticstracing-facade-assembly"></a>「 將協助如果我手動新增 「 System.Diagnostics.Tracing"片面組件的參考？ 」

特別是可以解決此問題，使用以下 2 個步驟嗎？

1. 複製`System.Diagnostics.Tracing.dll`片面組件至應用程式專案資料夾中，從下列位置之一：

    Windows：`C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.5\Facades\System.Diagnostics.Tracing.dll`

    Mac （單聲道）： `/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/4.5/Facades/System.Diagnostics.Tracing.dll`

2. Xamarin.iOS 或 Xamarin.Android 應用程式專案中加入片面組件的參考。

#### <a name="answer"></a>回應

否，這將會幫助。

- Xamarin.iOS 9.0 或 Xamarin.Android Windows 上的任何新版本，這個因應措施是嚴格多餘的並可能會造成編譯錯誤類似 「 具有相同識別的組件 'System.Diagnostics.Tracing' 匯入了。 」。

- 此因應措施如 Xamarin.iOS 8.10 可或較低或 Xamarin.Android Mac 上，可協助但_只_「 layer 1 」 的遺失組件問題。 它會_不_解決 「 layer 2 」 連結器錯誤，因此並不完整的解決方案。


###「 我可以使用<a href="https://www.nuget.org/packages/System.Diagnostics.Tracing/">System.Diagnostics.Tracing NuGet 封裝</a>來解決問題？ 」

#### <a name="answer"></a>回應

否，NuGet 3.0"System.Diagnostics.Tracing 」 封裝只會包含"DNXCore50"和"netcore50 」 的平台特定實作。 它明確_省略_(「 MonoAndroid 」) Xamarin.Android 和 Xamarin.iOS （「 MonoTouch"和"xamarinios"） 的實作。 這表示會有 安裝封裝_不會影響_Xamarin.Android 和 Xamarin.iOS 專案。 這兩個平台所提供的 NuGet 封裝假設其_自己_類型的實作。 這項假設為 「 正確 」，這表示沒有 Mono 實作的命名空間，但為底下所討論的點\#2 和\#3 」 的詳細 3 層級中的錯誤 」 上方實作為目前不完整。 適當的修正程式仍然可以供單聲道小組成員解決[Bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337)和[Bug 34890](https://bugzilla.xamarin.com/show_bug.cgi?id=34890)。


## <a name="next-steps"></a>後續步驟

如需進一步協助，請連絡我們，或如果此問題仍然發生即使利用上述的資訊，請參閱[xamarin 有哪些支援的選項？](~/cross-platform/troubleshooting/support-options.md)有關連絡人的選項，建議，以及如何如有需要的檔案新的 bug。
