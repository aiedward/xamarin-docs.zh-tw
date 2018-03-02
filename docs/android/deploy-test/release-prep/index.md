---
title: "準備可供發行的應用程式"
ms.topic: article
ms.prod: xamarin
ms.assetid: 9C8145B3-FCF1-4649-8C6A-49672DDA4159
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 7f36a29b00e0393ac0a2d65e7ebe7d290bbdb89a
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="preparing-an-application-for-release"></a>準備可供發行的應用程式

<a name="Compile_the_Application_for_Release" />

應用程式完成編碼和測試之後，必須準備可供散發的套件。 準備此套件的第一項工作是建置可供發行的應用程式，主要是設定一些應用程式屬性。

使用下列步驟建置可供發行的應用程式：

-   **[指定應用程式圖示](#Specify_the_Application_Icon)** &ndash; 每個 Xamarin.Android 應用程式都應要指定應用程式圖示。 雖非技術上必要，但一些市場有此要求，例如 Google Play。

-   **[控制應用程式版本](#Versioning)** &ndash;此步驟牽涉到初始化或更新版本設定資訊。 這對未來應用程式更新很重要，並可確保使用者了解他們已安裝哪個應用程式版本。

-   **[壓縮 APK](#shrink_apk)** &ndash; 在受控碼上使用 Xamarin.Android 連結器並在 Java 位元組程式碼上使用 ProGuard，可大幅降低最終 APK 的大小。

-   **[保護應用程式](#protect_app)** &ndash; 停用偵錯、混淆受控碼、新增反偵錯與反竄改以及使用原生編譯，來防止使用者或攻擊者對應用程式進行偵錯、竄改或還原工程。

-   **[設定封裝屬性](#Set_Packaging_Properties)** &ndash;封裝屬性會控制 Android 應用程式套件 (APK) 的建立。 此步驟可將 APK 最佳化、保護其資產並視需要將套件模組化。

-   **[編譯](#Compile)** &ndash; 此步驟可編譯程式碼與資產，確認會以發行模式建置。

-   **[封存以供發佈](#archive)** &ndash; 此步驟會建置應用程式並置入封存，以供簽署和發佈。

每個步驟在下方均會詳細說明。

<a name="Specify_the_Application_Icon" />

## <a name="specify-the-application-icon"></a>指定應用程式圖示

強烈建議每個 Xamarin.Android 應用程式都指定應用程式圖示。 某些應用程式市集要求一定要有圖示，才能發行 Android 應用程式。

`Application` 屬性的 `Icon` 屬性可用來指定 Xamarin.Android 專案的應用程式圖示。 此屬性可以在 **Properties\AssemblyInfo.cs** 檔案中宣告，如以下範例程式碼片段所示：

```csharp
[assembly: Application(Icon = "@drawable/icon")]
```

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

在 Visual Studio 2015 和更新版本中，透過專案 [屬性] 的 [Android 資訊清單] 區段來指定應用程式圖示，如下列螢幕擷取畫面所示：

[ ![設定應用程式圖示](images/vs/01-application-icon-sml.png)](images/vs/01-application-icon.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

在 Visual Studio for Mac 中，也可以透過 [專案選項] 的 [Android 應用程式] 區段來指定應用程式圖示，如下列螢幕擷取畫面所示：

[ ![設定應用程式圖示](images/xs/01-application-icon-sml.png)](images/xs/01-application-icon.png)

-----

<a name="Versioning" />

## <a name="version-the-application"></a>控制應用程式版本

版本控制對於 Android 應用程式維護和發佈至關重要。 如果沒有採取某種版本控制，會很難判斷應用程式是否應該更新和如何更新。 為了協助進行版本控制，Android 可辨識兩種不同類型的資訊： 

-   **版本號碼** &ndash; 整數值 (由 Android 應用程式內部使用)，表示應用程式的版本。 大部分應用程式一開始都會將此值設定為 1，然後隨每個組建遞增。 此值和版本名稱屬性沒有任何關係或同質性 (請參閱下文)。 應用程式和發佈服務不應對使用者顯示此值。 此值會在 **AndroidManifest.xml** 檔案中儲存為 `android:versionCode`。 

-   **版本名稱** &ndash; 此字串只用來和使用者通訊有關應用程式版本的資訊 (安裝在特定裝置時)。 版本名稱主要是向使用者顯示或在 Google Play 中顯示。 Android 不會在內部使用此字串。 可幫助使用者識別其裝置上所安裝組建的任何字串值，都可以是版本名稱。 此值會在 **AndroidManifest.xml** 檔案中儲存為 `android:versionName`。 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

在 Visual Studio 中，可在專案 [屬性] 的 [Android 資訊清單] 區段中設定這些值，如下列螢幕擷取畫面所示：

[ ![設定版本號碼](images/vs/02-versioning-sml.png)](images/vs/02-versioning.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

這些值可以透過 [專案選項] 的 [建置] > [Android 應用程式] 區段來設定，如下列螢幕擷取畫面所示：

[ ![設定版本號碼](images/xs/02-versioning-sml.png)](images/xs/02-versioning.png)

-----

<a name="shrink_apk" />

## <a name="shrink-the-apk"></a>壓縮 APK

透過結合會移除不必要受控碼的 Xamarin.Android 連結器和會移除未使用 Java 位元組程式碼 Android SDK 的 *ProGuard* 工具，可讓 Xamarin.Android APK 變小。 建置處理序會先使用 Xamarin.Android 連結器以將受控碼 (C#) 層級的應用程式最佳化，然後使用 ProGuard (已啟用的話) 在 Java 位元組程式碼層級將 APK 最佳化。

<a name="Configure_the_Linker" />

### <a name="configure-the-linker"></a>設定連結器

[發行] 模式會關閉共用的執行階段並開啟連結，以讓應用程式只隨附 Xamarin.Android 在執行階段所需的項目。 Xamarin.Android 中的「連結器」使用靜態分析以判斷 Xamarin.Android 應用程式所使用或參考的組件、類型及類型成員。 連結器接著會捨棄所有未使用 (或參考) 的組件、類型及成員。 這樣就能大幅縮小套件的大小。 例如，請考慮 [HelloWorld](~/android/deploy-test/linker.md) 範例，其 APK 最終大小的縮減達到 83%： 

-   設定：無 &ndash; Xamarin.Android 4.2.5 大小 = 17.4 MB。

-   設定：僅限 SDK 組件 &ndash; Xamarin.Android 4.2.5 大小 = 3.0 MB。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

透過專案 [屬性] 的 [Android 選項] 區段來設定連結器選項：

[ ![連結器選項](images/vs/03-linking-sml.png)](images/vs/03-linking.png)

[連結] 下拉式功能表提供下列控制連結器選項：

-   **無** &ndash; 這會關閉連結器，不會執行任何連結。

-   **僅限 SDK 組件** &ndash; 這只會連結 [Xamarin.Android 所需](~/cross-platform/internals/available-assemblies.md)的組件。 
    不會連結其他組件。

-   **SDK 與使用者組件** &ndash; 這會連結應用程式所需的所有組件，而不只是 Xamarin.Android 所需的組件。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

在 [專案選項] 的 [Android 組建] 區段中，透過 [連結器] 索引標籤來設定連結器選項 ，如下列螢幕擷取畫面所示：

[ ![連結器選項](images/xs/03-linking-sml.png)](images/xs/03-linking.png)

控制連結器選項如下所示：

-   **不要連結** &ndash; 這會關閉連結器，不會執行任何連結。

-   **僅連結 SDK 組件** &ndash; 這只會連結 [Xamarin.Android 所需](~/cross-platform/internals/available-assemblies.md)的組件。 不會連結其他組件。

-   **連結所有組件** &ndash; 這會連結應用程式所需的所有組件，而不只是 Xamarin.Android 所需的組件。

-----

連結會產生非預期的副作用，因此務必要在實體裝置上以發行模式重新測試應用程式。

<a name="proguard" />

### <a name="proguard"></a>ProGuard

*ProGuard* 是 Android SDK 工具，可連結和混淆 Java 程式碼。 ProGuard 通常會藉由減少 APK 中的大型內含程式庫 (例如 Google Play 服務) 磁碟使用量，來建立較小的應用程式。 ProGuard 可移除未使用的 Java 位元組程式碼，以讓產生的應用程式縮小。 例如，在小型 Xamarin.Android 應用程式上使用 ProGuard，通常可減少 24%的大小 &ndash; 在有多個程式庫相依性的較大應用程式使用 ProGuard，縮減的程度通常更佳。 

ProGuard 並非要替代 Xamarin.Android 連結器。 Xamarin.Android 受控碼可連結受控碼，而 ProGuard 可連結 Java 位元組程式碼。 建置處理序會先使用 Xamarin.Android 連結器以將應用程式中的受控碼 (C#) 最佳化，然後使用 ProGuard (若已啟用) 於 Java 位元組程式碼層級將 APK 最佳化。 

已勾選 [啟用 ProGuard] 時，Xamarin.Android 會在產生的 APK 上執行 ProGuard 工具。 ProGuard 會在建置階段產生並使用 ProGuard 組態檔。 Xamarin.Android 也支援自訂 *ProguardConfiguration* 建置動作。 您可以將自訂 ProGuard 組態檔加入至專案，以滑鼠右鍵按一下，並選取做為建置動作，如此範例所示： 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![ProGuard 建置動作](images/vs/05-proguard-build-action-sml.png)](images/vs/05-proguard-build-action.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![ProGuard 建置動作](images/xs/05-proguard-build-action-sml.png)](images/xs/05-proguard-build-action.png)

-----

ProGuard 預設為停用。 只有將專案設定為 [發行] 模式時，才能使用 [啟用 ProGuard] 選項。 除非已勾選 [啟用 ProGuard]，否則會忽略所有 ProGuard 建置動作。 Xamarin.Android ProGuard 組態不會混淆 APK，且無法啟用混淆功能，即使是使用自訂的組態檔。 如果您想要使用混淆功能，請參閱[使用 Dotfuscator 保護應用程式](~/android/deploy-test/release-prep/index.md#dotfuscator)。 

如需詳細使用 ProGuard 工具的相關資訊，請參閱 [ProGuard](~/android/deploy-test/release-prep/proguard.md)。

<a name="protect_app" />

## <a name="protect-the-application"></a>保護應用程式

<a name="Disable_Debugging" />

### <a name="disable-debugging"></a>停用偵錯

在 Android 應用程式開發期間，使用 Java Debug Wire Protocol (JDWP) 來執行偵錯。 基於偵錯用途，這項技術允許如 **adb** 的工具來與 JVM 通訊。 Xamarin.Android 應用程式的偵錯組建預設會開啟 JDWP。 雖然 JDWP 在開發期間很重要，但其可能對發行的應用程式造成安全性問題。 

> [!IMPORTANT]
> 永遠停用發行應用程式中的偵錯狀態，因為如果未停用此偵錯狀態.，即能 (透過 JDWP) 取得 Java 處理序的完整存取權，並在應用程式的內容中執行任意程式碼。

Android 資訊清單包含 `android:debuggable` 屬性，可控制是否能對應用程式偵錯。 較好的做法是將 `android:debuggable` 屬性設定為 `false`。 最簡單的做法就是在 **AssemblyInfo.cs** 中新增條件式編譯陳述式： 

```csharp
#if DEBUG
[assembly: Application(Debuggable=true)]
#else
[assembly: Application(Debuggable=false)]
#endif
```

請注意，偵錯組件會自動設定一些權限以方便偵錯 (例如 **Internet** 與 **ReadExternalStorage**)。 然而，發行組建只會使用您明確設定的權限。 如果您發現切換至發行組建造成應用程式失去偵錯組建中可用的權限，請確認您已在 [需要的權限] 中明確啟用此權限，如[權限](~/android/app-fundamentals/permissions.md)所述。 
 

<a name="dotfuscator" id="dotfuscator" />

### <a name="application-protection-with-dotfuscator"></a>使用 Dotfuscator 保護應用程式

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

即使[已停用偵錯](#Disable_Debugging)，攻擊者還是可以重新封裝應用程式、新增或移除組態選項或權限。 這可讓他們進行還原工程、偵錯，或竄改應用程式。
[Dotfuscator Community Edition (CE)](https://www.preemptive.com/products/dotfuscator/overview) 可用來混淆受控碼，並在建置階段將執行階段安全性狀態偵測程式碼插入 Xamarin.Android 應用程式。

Dotfuscator CE 隨附於 Visual Studio，然而只有 Visual Studio 2015 Update 3 (及更新版本) 才有能使用 Xamarin.Android 的正確版本。 若要使用 Dotfuscator，請按一下 [工具] > [PreEmptive Protection - Dotfuscator]。

若要設定 Dotfuscator CE，請參閱[搭配 Xamarin 使用 Dotfuscator Community Edition](https://www.preemptive.com/obfuscating-xamarin-with-dotfuscator)。
一旦設定之後，Dotfuscator CE 會自動保護建立的每個組建。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

即使[已停用偵錯](#Disable_Debugging)，攻擊者還是可以重新封裝應用程式、新增或移除組態選項或權限。 這可讓他們進行還原工程、偵錯，或竄改應用程式。
雖然不支援 Visual Studio for Mac，但您可以使用 Visual Studio 所附的 [Dotfuscator Community Edition (CE)](https://www.preemptive.com/products/dotfuscator/overview) 來混淆受控碼，並在建置階段將執行階段安全性狀態偵測程式碼插入 Xamarin.Android 應用程式。

若要設定 Dotfuscator CE，請參閱[搭配 Xamarin 使用 Dotfuscator Community Edition](https://www.preemptive.com/obfuscating-xamarin-with-dotfuscator)。
一旦設定之後，Dotfuscator CE 會自動保護建立的每個組建。

-----

<a name="bundle" />

### <a name="bundle-assemblies-into-native-code"></a>將組譯碼組合成機器碼

啟用此選項時，系統會將組件組合成原生共用程式庫。 此選項會保護程式碼的安全。它會將受控組件內嵌於原生二進位檔來予以保護。

此選項需要企業授權，並只有在停用 [使用 Fast Deployment] 時才能使用。 [將組件組合成機器碼] 預設為停用。

請注意，[組合成機器碼] 選項「不」代表會將組件編譯成機器碼。 您無法使用 [**AOT 編譯**](#aot)來將組件編譯成機器碼 (目前僅為實驗性用途，並不提供生產使用)。

<a name="aot" />

### <a name="aot-compilation"></a>AOT 編譯

[AOT 編譯] 選項 ([封裝屬性](#Set_Packaging_Properties) 頁面上) 可啟用組件的預先 (AOT) 編譯。 啟用此選項時，在執行階段之前就會預先編譯組件，進而將 Just In Time (JIT) 啟動負荷降至最低。 產生的機器碼會連同未編譯的組件一起包含在 APK 中。 這樣可讓應用程式啟動時間較短，但代價是 APK 大小稍大。

[AOT 編譯] 選項需要企業授權或更高。 只有將專案設定為 [發行] 模式時才能使用 [AOT 編譯]，且該選項預設為停用。 如需 AOT 編譯的詳細資訊，請參閱 [AOT](http://www.mono-project.com/docs/advanced/aot/) \(英文\)。

<a name="llvm" />

#### <a name="llvm-optimizing-compiler"></a>LLVM 最佳化編譯器

「LLVM 最佳化編譯器」會建立更小且速度更快的編譯程式碼，並將 AOT 編譯的組件轉換成機器碼，但代價是建置時間較慢。 LLVM 編譯器預設為停用。 若要使用 LLVM 編譯器，必須先啟用 [AOT 編譯] 選項 (在[封裝屬性](#Set_Packaging_Properties)頁面上)。


> [!NOTE]
> [LLVM 最佳化編譯器] 選項需要商務用授權。  

<a name="Set_Packaging_Properties" />

## <a name="set-packaging-properties"></a>設定封裝屬性

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

封裝屬性可以在專案 [屬性] 的 [Android 選項] 區段中設定，如下列螢幕擷取畫面所示：

[ ![封裝屬性](images/vs/04-packaging-sml.png)](images/vs/04-packaging.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

封裝屬性可以在 [專案選項] 中設定，如下列螢幕擷取畫面所示：

[ ![封裝屬性](images/xs/04-packaging-sml.png)](images/xs/04-packaging.png)

-----

其中許多屬性都是提供偵錯模式使用，例如 [使用共用執行階段] 和 [使用 Fast Deployment]。 不過，設定應用程式以用於發行模式時，還有其他設定可決定如何將應用程式的[大小與執行速度最佳化](#shrink_apk)、[如何保護以免遭到竄改](#protect_app)，以及如何封裝以支援不同架構與大小限制。

<a name="Specify_Supported_Architectures" />

### <a name="specify-supported-architectures"></a>指定支援的架構

準備 Xamarin.Android 應用程式以供發行時，必須指定支援的 CPU 架構。 單一 APK 可以包含機器碼，以支援多個不同的架構。 如需支援多個 CPU 架構的詳細資訊，請參閱 [CPU 架構](~/android/app-fundamentals/cpu-architectures.md)。

<a name="multiabi" />

### <a name="generate-one-package-apk-per-selected-abi"></a>為選取的每一個 ABI 產生一個套件 (.APK)

啟用此選項時，會為每個支援的 ABI 建立一個 APK (於 [進階] 索引標籤上選取，如 [CPU 架構](~/android/app-fundamentals/cpu-architectures.md)中所述) 而不是為所有支援的 ABI 建立單一的大型 APK。 只有將專案設定為 [發行] 模式時才能使用此選項，且該選項預設為停用。

<a name="multidex" />

### <a name="multi-dex"></a>Multi-Dex

當 [啟用 Multi-Dex] 選項已啟用時，Android SDK 工具會用來略過 **.dex** 檔案格式 6.5 萬個方法的限制。 6.5 萬個方法的限制是以應用程式「參考」的 Java 方法數為基礎 (包括任何應用程式相依程式庫中的方法) &ndash; 並非以「原始程式碼中撰寫的」方法數為基礎。 如果應用程式只定義一些方法，但使用許多方法 (或大型程式庫)，則可能會超過 6.5 萬個的限制。

應用程式可能不會使用每個參考程式庫中的每個方法，因此像是 ProGuard (請參閱前述) 等工具能夠從程式碼中移除未使用的方法。 最佳做法是只有在絕對必要時，才將 [啟用 Multi-Dex] 啟用，也就是說即使在使用 ProGuard 之後，應用程式仍參考超過 6.5 萬個 Java 方法。

如需 Multi-Dex 的詳細資訊，請參閱[設定應用程式使用超過 6.4 萬個方法](http://developer.android.com/tools/building/multidex.html) \(英文\)。

<a name="Compile" />

## <a name="compile"></a>編譯

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

上述所有步驟都完成後，應用程式已準備好進行編譯。 選取[建置] > [重建方案]，來確認可在發行模式中成功建置。 請注意，此步驟還不會產生 APK。

[簽署應用程式套件](~/android/deploy-test/signing/index.md)會詳細討論封裝和簽署。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

完成所有上述步驟之後，請編譯應用程式 (選取 [建置] > [全部建置])，來確認可在發行模式中成功建置。 請注意，此步驟還不會產生 APK。

-----


<a name="archive" />

## <a name="archive-for-publishing"></a>封存以供發行

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要開始發行程序，請以滑鼠右鍵按一下 [方案總管] 中的專案，然後選取 [封存] 操作功能表項目：

[ ![封存應用程式](images/vs/07-archive-for-publishing-sml.png)](images/vs/07-archive-for-publishing.png)

[封存] 會啟動 [封存管理員]，並開始封存應用程式套件組合的程序，如以下螢幕擷取畫面所示：

[ ![封存管理員](images/vs/08-archive-manager-sml.png)](images/vs/08-archive-manager.png)

建立封存的另一種方法是，以滑鼠右鍵按一下 [方案總管] 中的解決方案，然後選取 [全部封存]，這樣會建置解決方案，並封存可產生封存的所有 Xamarin 專案：

[ ![全部封存](images/vs/09-archive-all-sml.png)](images/vs/09-archive-all.png)


[封存] 與 [全部封存] 都會自動啟動 [封存管理員]。 若要直接啟動 [封存管理員]，請按一下 [工具] > [封存管理員] 功能表項目：

[ ![啟動封存管理員](images/vs/10-launch-archive-manager-sml.png)](images/vs/10-launch-archive-manager.png)

您可隨時以滑鼠右鍵按一下 [解決方案] 節點並選取 [檢視封存] 來查看解決方案的封存：

[ ![檢視封存](images/vs/11-view-archives-sml.png)](images/vs/11-view-archives.png)

### <a name="the-archive-manager"></a>封存管理員

**封存管理員** 是由**解決方案清單**窗格、**封存清單**及**詳細資料面板**組成：

[ ![封存管理員窗格](images/vs/12-archive-manager-detail-sml.png)](images/vs/12-archive-manager-detail.png)

**解決方案清單**會顯示至少有一個封存專案的解決方案。 **解決方案清單**包括下列區段：

* **目前的解決方案** &ndash; 顯示目前的解決方案。 請注意，如果目前的解決方案無現有的封存，此區域可能會是空的。
* **所有封存** &ndash;顯示所有具有封存的解決方案。
* [搜尋] 文字方塊 (位於頂端) &ndash; 根據文字方塊中輸入的搜尋字串，來篩選 [所有封存] 清單中所列的解決方案。

[封存清單] 顯示所選取解決方案之所有封存的清單。 [封存清單] 包括下列區段：

* **選取的解決方案名稱** &ndash; 顯示在**解決方案清單**中所選取解決方案的名稱。 [封存清單] 中顯示的所有資訊是指這個選取的解決方案。
* **平台篩選** &ndash; 這個欄位可讓封存管理員根據平台類型 (例如 iOS 或 Android) 來篩選封存。
* **封存項目** &ndash;所選取之解決方案的封存清單。 此清單中的每個項目都包括專案名稱、建立日期和平台。 在封存或發佈項目時，也可以顯示其他資訊，例如進度。

**詳細資料面板**可顯示每個封存的其他相關資訊。 它也可讓使用者開始散發工作流程或開啟散發建立所在的資料夾。 [組建註解] 區段可以在封存中包括組建註解。

### <a name="distribution"></a>散發

準備好要發行之應用程式的封存版本時，請選取 [封存管理員] 中的封存，然後按一下 [散發] 按鈕：

[ ![[散發] 按鈕](images/vs/13-distribute-sml.png)](images/vs/13-distribute.png)

[散發通道] 對話方塊顯示應用程式、指出散發工作流程進度，以及所選擇之散發通道的相關資訊。 第一次執行時，會顯示兩個選擇：

[ ![選擇散發通道](images/vs/14-distribution-channel-sml.png)](images/vs/14-distribution-channel.png)

您可選擇下列其中一個散發通道：

* **臨機操作** &ndash; 將簽署的 APK 儲存至可側載至 Android 裝置的磁碟。 繼續前往[簽署應用程式套件](~/android/deploy-test/signing/index.md)，了解如何建立 Android 簽署識別、建立適用於 Android 應用程式的新簽署憑證，並將應用程式的「臨機操作」版本發佈至磁碟。 這是建立 APK 進行測試的好方法。

* **Google Play** &ndash; 可將簽署的 APK 發佈至 Google Play。 繼續前往[發佈至 Google Play](~/android/deploy-test/publishing/publishing-to-google-play/index.md)，了解如何在 Google Play 商店中簽署和發佈 APK。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

若要開始發佈流程，請選取 [建置] > [封存以供發行]：

[ ![封存以供發行](images/xs/07-archive-for-publishing-sml.png)](images/xs/07-archive-for-publishing.png)

[封存以供發行] 可建置專案並組合成封存檔。 [全部封存] 功能表選項可封存解決方案中所有可封存的專案。 建置和統合作業完成時，兩個選項都會自動開啟 [封存管理員]：

[ ![封存檢視](images/xs/08-archives-view-sml.png)](images/xs/08-archives-view.png)

在此範例中，[封存管理員] 只會列出一個封存應用程式 **MyApp**。 請注意，註解欄位允許簡短註解和封存一併儲存。 若要發佈封存的 Xamarin.Android 應用程式版本，請選取 [封存管理員] 中的應用程式，然後按一下 [簽署並散發]，如上所示。 所產生的 [簽署並散發] 對話方塊會出現兩個選項：

[ ![簽署並散發](images/xs/09-sign-and-distribute-sml.png)](images/xs/09-sign-and-distribute.png)


您可以從這裡選擇散發通道：

-   **臨機操作** &ndash; 將簽署的 APK 儲存至磁碟，以便側載至 Android 裝置。 繼續前往[簽署應用程式套件](~/android/deploy-test/signing/index.md)，了解如何建立 Android 簽署識別、建立適用於 Android 應用程式的新簽署憑證，並將應用程式的「臨機操作」&ldquo;&rdquo;版本發佈至磁碟。 這是建立 APK 進行測試的好方法。


-   **Google Play** &ndash; 可將簽署的 APK 發佈至 Google Play。
    繼續前往[發佈至 Google Play](~/android/deploy-test/publishing/publishing-to-google-play/index.md)，了解如何在 Google Play 商店中簽署和發佈 APK。

-----


## <a name="related-links"></a>相關連結

- [多核心裝置和 Xamarin.Android](~/android/deploy-test/multicore-devices.md)
- [CPU 架構](~/android/app-fundamentals/cpu-architectures.md)
- [AOT](http://www.mono-project.com/docs/advanced/aot/)
- [壓縮程式碼和資源](http://developer.android.com/tools/help/proguard.html)
- [設定應用程式使用超過 6.4 萬個方法](http://developer.android.com/tools/building/multidex.html)
