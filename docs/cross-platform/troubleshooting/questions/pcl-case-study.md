---
title: 解決 System.Diagnostics.Tracing TPL 資料流程的相關問題
description: PCL 案例研究：如何解決 Microsoft TPL Dataflow NuGet 套件之 System.Diagnostics.Tracing 的有關問題？
ms.prod: xamarin
ms.assetid: 7986A556-382D-4D00-ACCF-3589B4029DE8
ms.date: 04/17/2018
author: asb3993
ms.author: amburns
ms.openlocfilehash: d9aa85b946f20addb7d69c559bff68c6b1f75429
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61342270"
---
# <a name="pcl-case-study-how-can-i-resolve-problems-related-to-systemdiagnosticstracing-for-the-microsoft-tpl-dataflow-nuget-package"></a>PCL 案例研究：如何解決 Microsoft TPL Dataflow NuGet 套件之 System.Diagnostics.Tracing 的有關問題？

> [!IMPORTANT]
> 這個特定範例中的`System.Diagnostic.Tracing`不再產生任何錯誤，預設會在最新版的 Xamarin。 雖然建議的因應措施仍然可以運作，請注意一些 「 錯誤的層級 」 一節所述的問題已獲得修正。
> 此外，您應該注意，.NET Standard 現在是實作跨平台.NET Api 的慣用的方法。

## <a name="summary"></a>總結

Xamarin.iOS 和 Xamarin.Android 不會實作的每個可做為參考的 PCL 設定檔的 100%。 為了方便在 Visual Studio for Mac 中，Visual Studio 和 NuGet 套件管理員的實用起見，Xamarin 專案允許使用數個設定檔僅具有_完整_實作。 例如，Xamarin.iOS 和 Xamarin.Android 都不目前包含"System.Diagnostics.Tracing 」 PCL 中的類型的完整實作命名空間。 這項限制會導致錯誤的三個層級時，嘗試使用預設的`portable-net45+win8+wpa81`Microsoft TPL Dataflow NuGet 套件的版本。

## <a name="workaround-switch-the-app-project-to-reference-the-portable-net45win8wp8wpa81-version-of-the-tpl-dataflow-library"></a>因應措施：切換至參考的應用程式專案`portable-net45+win8+wp8+wpa81`TPL 資料流程程式庫版本

（這可避免錯誤和適用於 Xamarin 的所有最新版本的所有三個層級。）

1. 開啟應用程式專案 **.csproj**在文字編輯器中的檔案。

2. 尋找看起來類似下列一行：

    ```xml
    <HintPath>..\packages\Microsoft.Tpl.Dataflow.4.5.24\lib\portable-net45+win8+wpa81\System.Threading.Tasks.Dataflow.dll</HintPath>
    ```

3. 變更`portable-net45+win8+wpa81`要`portable-net45+win8+wp8+wpa81`(`+wp8`加入):

    ```xml
    <HintPath>..\packages\System.Threading.Tasks.Dataflow.4.5.25\lib\portable-net45+win8+wp8+wpa81\System.Threading.Tasks.Dataflow.dll</HintPath>
    ```

### <a name="explanation"></a>說明

`portable-net45+win8+wp8+wpa81`程式庫版本不會參考**System.Diagnostics.Tracing.dll** _完全_，所以會完全避免所有三個層面的問題。

### <a name="limitations"></a>限制

- `portable-net45+win8+wp8+wpa81`程式庫版本可能不會包含 100%的功能`portable-net45+win8+wpa81`版本。

- NuGet 套件管理員安裝`portable-net45+win8+wpa81`根據預設，因此您必須以手動方式調整參考 PCL 的 NuGet 套件的版本。

## <a name="details-about-the-three-layers-of-errors"></a>關於錯誤的三個層面的詳細資料

1. **System.Diagnostics.Tracing.dll**外觀組件是目前所有的 Mac 版本的 Xamarin.Android （非公用錯誤 34888） 不存在，並不存在從所有的 Xamarin.iOS 版本小於 9.0 （或低於 XamarinVS 3.11.1443在 Windows) 上 (在固定[Bug 32388](https://bugzilla.xamarin.com/show_bug.cgi?id=32388))。 此問題會導致下列錯誤，根據部署的目標和連結器的其中一個設定：

    - Xamarin.Android.Common.targets:錯誤：載入組件時發生的例外狀況：System.IO.FileNotFoundException:無法載入組件 ' System.Diagnostics.Tracing，version=4.0.0.0，Culture = neutral，publickeytoken=b03f5f7f11d50a3a '。 可能是它不存在於 Mono for Android 的設定檔嗎？

    - 無法載入檔案或組件 'System.Diagnostics.Tracing' 或其中一個相依性。 系統找不到指定的檔案。 (System.IO.FileNotFoundException)

    - MTOUCH： 錯誤 MT3001:無法 AOT 的組件 ' / Users/macuser/Projects/TPLDataflow/UnifiedSingleViewIphone1/obj/iPhone/Debug/mtouch-cache/64/Build/System.Threading.Tasks.Dataflow.dll '

    - MTOUCH： 錯誤 MT2002:無法解析組件：' System.Diagnostics.Tracing，version=4.0.0.0，Culture = neutral，publickeytoken=b03f5f7f11d50a3a '

2. 目前[Mono 實作 「 System.Diagnostics.Tracing"中的型別](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs)遺漏某些方法多載 ([Bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337))。 建置 Xamarin 應用程式時，此問題會以下任一連結器錯誤：

    - / Library/Frameworks/Mono.framework/External/xbuild/Xamarin/Android/Xamarin.Android.Common.targets： 錯誤：執行錯誤工作 LinkAssemblies： 錯誤 XA2006:中繼資料參考項目 'System.Void System.Diagnostics.Tracing.EventSource::WriteEvent(System.Int32,System.Object[])' (定義於' System.Threading.Tasks.Dataflow，版本 = 4.5.24.0，文化特性 = 中性，PublicKeyToken = b03f5f7f11d50a3a')從 'System.Threading.Tasks.Dataflow，版本 = 4.5.24.0，Culture = neutral，publickeytoken=b03f5f7f11d50a3a' 無法解析。

    - MTOUCH： 錯誤 MT2002:無法解析來自 「 System.Void System.Diagnostics.Tracing.EventSource::WriteEvent(System.Int32,System.Object[])"參考"System.Diagnostics.Tracing，version=4.0.0.0，Culture = neutral，PublicKeyToken = b03f5f7f11d50a3a"

3. 目前[Mono 實作 「 System.Diagnostics.Tracing"中的型別](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs)目前還有_空白_"dummy"的實作 ([Bug 34890](https://bugzilla.xamarin.com/show_bug.cgi?id=34890))。 因此，任何嘗試使用這些方法在執行階段可能會產生非預期的結果。 針對_特定_案例似乎 Microsoft TPL 資料流程程式庫，呼叫`WriteEvent(System.Int32,System.Object[])`不是不可或缺的大部分的程式庫的行為，因此 「 layer 2 」 的修正程式 ([Bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337)，加入空白實作） 可能足以用於大部分的 Microsoft TPL 資料流程的使用案例。

## <a name="questions--answers"></a>問題與解答

### <a name="i-was-able-to-leave-linking-enabled-with-the-codeportable-net45win8wpa81code-version-of-the-library-on-older-versions-of-xamarinios-or-on-xamarinandroid-how-did-that-work"></a>我才能夠保留連結以啟用<code>portable-net45+win8+wpa81</code>舊版 Xamarin.iOS 或 Xamarin.Android 程式庫版本。 如何執行該工作？

#### <a name="answer"></a>回應

很_可能_以建置完整 「 成功 」 （連結已啟用） 的 Xamarin.iOS 的較舊版本中，或在 Mac 上的 Xamarin.Android 中如果包含的參考`System.Diagnostics.Tracing.dll`_參考組件_\[1\]而非_外觀的組件_ \[2]，但不幸的是這不是 「 正確 」 的因應措施。 參考組件只為了要在建置時使用_可攜式程式庫_，例如應用程式的平台專屬程式碼。 嘗試_執行_參考組件 （而不是針對它只是組建） 中所包含的程式碼是有可能產生非預期的結果。 正確的修正將會新增遺漏 Mono 的小組`WriteEvent(System.Int32,System.Object[])`若要多載[ `EventSource` ](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs)類型 ([Bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337))。 現在最好的選擇是要切換至`portable-net45+win8+wp8+wpa81`上述的因應措施 」 一節中所述的 Microsoft TPL 資料流程程式庫版本。

(可能會在之後看到相關的較舊且簡潔回應從 StackOverflow 閱讀這篇文章的人 (<https://stackoverflow.com/a/23591322/2561894>)，請注意，參考組件和外觀的組件之間的差別是_不_那里提到。)

**\[1\] 「 參考組件 」 的位置**

Windows：`C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETPortable\v4.5\System.Diagnostics.Tracing.dll`

Mac (Mono): `/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/xbuild-frameworks/.NETPortable/v4.5/System.Diagnostics.Tracing.dll`

**\[2\] 「 外貌組件 」 的位置**

Windows：`C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.5\Facades\System.Diagnostics.Tracing.dll`

Mac (Mono): `/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/4.5/Facades/System.Diagnostics.Tracing.dll`


### <a name="will-it-help-if-i-manually-add-a-reference-to-the-systemdiagnosticstracing-facade-assembly"></a>就這樣有幫助我手動新增"System.Diagnostics.Tracing 」 外觀組件的參考嗎？

_特別是解決問題，請使用下列 2 個步驟嗎？_

1. _複製`System.Diagnostics.Tracing.dll`外觀為應用程式的專案資料夾，從下列位置之一的組件：_

    Windows：`C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.5\Facades\System.Diagnostics.Tracing.dll`

    Mac (Mono): `/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/4.5/Facades/System.Diagnostics.Tracing.dll`

2. _Xamarin.iOS 或 Xamarin.Android 應用程式專案中加入外觀組件的參考。_

#### <a name="answer"></a>回應

否，這會幫助。

- Xamarin.iOS 9.0 或在 Windows 上的 Xamarin.Android 的任何最新版本，這個因應措施已完全重複，並可能會造成編譯錯誤類似"具有相同的身分識別的組件 'System.Diagnostics.Tracing' 已經匯入。 」。

- 這個因應措施如 8.10 可或更低的 Xamarin.iOS 或 Xamarin.Android 在 Mac 上，將幫助，但_只_「 layer 1 」 的遺失組件問題。 它會_不_解決 「 layer 2 」 連結器錯誤，所以不完整的解決方案。

### <a name="can-i-use-the-systemdiagnosticstracing-nuget-packagehttpswwwnugetorgpackagessystemdiagnosticstracing-to-solve-the-problem"></a>我可以使用[System.Diagnostics.Tracing NuGet 套件](https://www.nuget.org/packages/System.Diagnostics.Tracing/)來解決問題嗎？

#### <a name="answer"></a>回應

否，NuGet 3.0"System.Diagnostics.Tracing 」 套件只會包含"DNXCore50"和"netcore50 」 的平台特定實作。 它明確_省略_Xamarin.Android (「 MonoAndroid") 和 Xamarin.iOS （「 MonoTouch"和"xamarinios 」） 的實作。 這表示會有 安裝套件_不會影響_Xamarin.Android 和 Xamarin.iOS 專案。 這兩個平台所提供的 NuGet 套件假設其_自己_類型實作。 這項假設是 「 正確 」 沒有 Mono 的意義實作的命名空間，而下所述的點\#2 和\#3 的 [詳細資料的相關錯誤的三個層面] 上方，實作是目前不完整。 讓適當的修正將會針對 Mono 的小組來解決[Bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337)並[Bug 34890](https://bugzilla.xamarin.com/show_bug.cgi?id=34890)。

## <a name="next-steps"></a>後續步驟

如需進一步協助，請連絡我們，或如果此問題持續，即使利用上述的資訊，請參閱[適用於 Xamarin 有哪些支援選項？](~/cross-platform/troubleshooting/support-options.md)如需連絡選項，建議，以及如何如有需要請提出新錯誤報告。
