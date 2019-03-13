---
title: 疑難排解秘訣
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 56137ACA-4811-B312-6860-E16D0FA123F7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/15/2018
ms.openlocfilehash: b2f11bd09e1b1b3fd7af29a026229494a081ad11
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668552"
---
# <a name="troubleshooting-tips"></a>疑難排解秘訣


## <a name="getting-diagnostic-information"></a>取得診斷資訊

Xamarin.Android 會有幾個地方，來追蹤各種 bug 時的外觀。
它們包括：

1.  診斷 MSBuild 輸出。
2.  裝置部署記錄檔。
3.  Android 偵錯記錄檔輸出。


<a name="Diagnostic_MSBuild_Output" />

## <a name="diagnostic-msbuild-output"></a>診斷 MSBuild 輸出

診斷 MSBuild 可能包含有關封裝建置的其他資訊，而且可能包含某些套件的部署資訊。

若要在 Visual Studio 內啟用診斷 MSBuild 輸出：

1.  按一下 **工具 > 選項...**
2.  在左側的樹狀結構檢視中，選取 **專案和方案 > 建置並執行**
3.  在右窗格中，請設定 MSBuild 建置輸出詳細等級的下拉式清單中為診斷
4.  按一下 [確定] 
5.  清除並重建您的套件。
6.  診斷輸出會顯示在 [輸出] 面板中。


若要啟用診斷 MSBuild 輸出，在 Visual Studio for Mac/OS x:

1.  按一下  **Visual Studio for Mac > 喜好設定...**
2.  在左側的樹狀結構檢視中，選取 **專案 > 建置**
3.  在右窗格中，設定記錄檔的詳細資訊下拉式清單為診斷
4.  按一下 [確定] 
5.  重新啟動 Visual Studio for Mac
6.  清除並重建您的套件。
7.  診斷輸出會顯示錯誤板內 (**檢視 > 板 > 錯誤**)，依序按一下 [建置輸出] 按鈕。




## <a name="device-deployment-logs"></a>裝置部署記錄檔

若要啟用 Visual Studio 內的裝置部署記錄功能：

1.  **工具 > 選項...**>
2.  在左側的樹狀結構檢視中，選取  **Xamarin > Android 設定**
3.  在右窗格中，啟用 [X]**延伸模組的偵錯記錄 （將 monodroid.log 寫入您的桌面）** 核取方塊。
4.  在您的桌面上 monodroid.log 檔案會寫入記錄訊息。


Visual Studio for Mac 一律會寫入裝置的部署記錄檔。 找到這些物件是略為困難;*AndroidUtils*每個日期 + 時間部署，例如建立記錄檔：**AndroidTools-2012-10-24_12-35-45.log**。

-  在 Windows 中，記錄檔會寫入至`%LOCALAPPDATA%\XamarinStudio-{VERSION}\Logs`。
-  在 OS X 上記錄檔會寫入至`$HOME/Library/Logs/XamarinStudio-{VERSION}`。




## <a name="android-debug-log-output"></a>Android 偵錯記錄輸出

Android 會寫入許多訊息[Android 偵錯記錄檔](~/android/deploy-test/debugging/android-debug-log.md)。
Xamarin.Android 會使用 Android 系統屬性來控制 Android 偵錯記錄檔的其他訊息的產生。 可以透過設定 android 系統屬性*setprop*命令內[Android Debug Bridge (adb)](https://developer.android.com/guide/developing/tools/adb.html):

```shell
adb shell setprop PROPERTY_NAME PROPERTY_VALUE
```

系統屬性程序在啟動期間，會讀取，因此必須先設定應用程式就會啟動，或是必須重新啟動應用程式之後會變更的系統屬性。



### <a name="xamarinandroid-system-properties"></a>Xamarin.Android 系統屬性

Xamarin.Android 支援下列系統屬性：

-   *debug.mono.debug*:如果非空白字串，這就相當於`*mono-debug*`。

-   *debug.mono.env*:以管道分隔 ('*|*') 的應用程式在啟動期間，匯出的環境變數清單*之前*mono 已初始化。 這可讓設定環境變數，控制 mono 的記錄功能。

    - *注意*：由於的值為 '*|*'-分隔，值必須具有額外的層級的引號，作為\` *adb 殼層*\`命令會移除引號括住的一組。

    - *注意*：Android 系統屬性值可以是不超過 92 個字元的長度。

    - 範例：

            adb shell setprop debug.mono.env "'MONO_LOG_LEVEL=info|MONO_LOG_MASK=asm'"

-   *debug.mono.log*:以逗號分隔 ('*，*') 的列印到 Android 偵錯記錄檔的其他訊息的元件清單。 根據預設，不會設定。 元件包括：

    -   *所有*:列印所有訊息
    -   *gc*:列印與 GC 相關的訊息。
    -   *gref*:列印弱式 （通用） 的參考配置和解除配置的訊息。
    -   *lref*:列印本機參考的配置和解除配置的訊息。

    *附註*： 這些是*極*詳細資訊。 除非您真的需要否則請不要啟用。

-   *debug.mono.trace*:允許您設定[mono-追蹤](http://docs.go-mono.com/?link=man%3amono(1))`=PROPERTY_VALUE`設定。



## <a name="xamarinandroid-cannot-resolve-systemvaluetuple"></a>Xamarin.Android 無法解析 System.ValueTuple

這個錯誤是因為使用 Visual Studio 不相容。

- **Visual Studio 2017 Update 1** （版本 15.1 或更舊版本） 才相容**System.ValueTuple NuGet 4.3.0** （或更舊版本）。

- **Visual Studio 2017 Update 2** （版本 15.2 或更新版本） 才相容**System.ValueTuple NuGet 4.3.1** （或更新版本）。

請選擇正確的 System.ValueTuple NuGet 與您的 Visual Studio 2017 安裝相對應。


## <a name="gc-messages"></a>GC 訊息

GC 元件訊息可檢視 debug.mono.log 系統屬性設定為包含 gc 的值。

GC 會執行，並提供關於多少工作 GC 並未的資訊時，會產生 GC 的訊息：

```shell
I/monodroid-gc(12331): GC cleanup summary: 81 objects tested - resurrecting 21.
```

可以產生其他的 GC 資訊，例如計時資訊，藉由設定`MONO_LOG_LEVEL`環境變數，以`debug`:

```shell
adb shell setprop debug.mono.env MONO_LOG_LEVEL=debug
```

這會導致 （大量） 額外 Mono 的訊息，包括下列三個後果：

```shell
D/Mono (15723): GC_BRIDGE num-objects 1 num_hash_entries 81226 sccs size 81223 init 0.00ms df1 285.36ms sort 38.56ms dfs2 50.04ms setup-cb 9.95ms free-data 106.54ms user-cb 20.12ms clenanup 0.05ms links 5523436/5523436/5523096/1 dfs passes 1104 6883/11046605
D/Mono (15723): GC_MINOR: (Nursery full) pause 2.01ms, total 287.45ms, bridge 225.60 promoted 0K major 325184K los 1816K
D/Mono ( 2073): GC_MAJOR: (user request) pause 2.17ms, total 2.47ms, bridge 28.77 major 576K/576K los 0K/16K
```

在 `GC_BRIDGE`訊息中，`num-objects`是此階段的考量，橋接器物件的數目和`num_hash_entries`是橋接程式碼的這個引動過程期間處理的物件數目。

中`GC_MINOR`並`GC_MAJOR`訊息，`total`是一段時間，而世界已暫停 （沒有執行緒正在執行） 時`bridge`是橋接器處理程式碼 （這會處理與 Java VM） 中所花費的時間量。 항 상*不*暫停橋接器處理發生時。

 *一般情況下*的值愈大`num_hash_entries`，則更多時間`bridge`需要集合，和較大`total`會收集所花費的時間。



## <a name="global-reference-messages"></a>全域參考訊息

若要啟用全域參考 loggig (GRF) 記錄*debug.mono.log*系統的屬性必須包含*grf*，例如：

```shell
adb shell setprop debug.mono.log gref
```

Xamarin.Android 會使用 Android 的全域參考，來叫用的 Java 執行個體必須要提供給 Java Java 方法時，提供做為 Java 執行個體和相關的 managed 執行個體之間的對應。

不幸的是，Android 模擬器只允許 2000年全域參考存在一次。 硬體有更高限制的 52000 全域的參考。 較低的限制可能會造成問題，因此了解，在模擬器上執行應用程式時*其中*執行個體來自可能非常有用。

 *請注意*： 全域的參考計數是內部 Xamarin.Android，並不會 （而且也無法） 包含由其他處理程序所載入的原生程式庫的全域參考。 使用全域的參考計數為估計值。

```shell
I/monodroid-gref(12405): +g+ grefc 108 gwrefc 0 obj-handle 0x40517468/L -> new-handle 0x40517468/L from    at Java.Lang.Object.RegisterInstance(IJavaObject instance, IntPtr value, JniHandleOwnership transfer)
I/monodroid-gref(12405):    at Java.Lang.Object.SetHandle(IntPtr value, JniHandleOwnership transfer)
I/monodroid-gref(12405):    at Java.Lang.Object..ctor(IntPtr handle, JniHandleOwnership transfer)
I/monodroid-gref(12405):    at Java.Lang.Thread+RunnableImplementor..ctor(System.Action handler, Boolean removable)
I/monodroid-gref(12405):    at Java.Lang.Thread+RunnableImplementor..ctor(System.Action handler)
I/monodroid-gref(12405):    at Android.App.Activity.RunOnUiThread(System.Action action)
I/monodroid-gref(12405):    at Mono.Samples.Hello.HelloActivity.UseLotsOfMemory(Android.Widget.TextView textview)
I/monodroid-gref(12405):    at Mono.Samples.Hello.HelloActivity.<OnCreate>m__3(System.Object o)
I/monodroid-gref(12405): handle 0x40517468; key_handle 0x40517468: Java Type: `mono/java/lang/RunnableImplementor`; MCW type: `Java.Lang.Thread+RunnableImplementor`
I/monodroid-gref(12405): Disposing handle 0x40517468
I/monodroid-gref(12405): -g- grefc 107 gwrefc 0 handle 0x40517468/L from    at Java.Lang.Object.Dispose(System.Object instance, IntPtr handle, IntPtr key_handle, JObjectRefType handle_type)
I/monodroid-gref(12405):    at Java.Lang.Object.Dispose()
I/monodroid-gref(12405):    at Java.Lang.Thread+RunnableImplementor.Run()
I/monodroid-gref(12405):    at Java.Lang.IRunnableInvoker.n_Run(IntPtr jnienv, IntPtr native__this)
I/monodroid-gref(12405):    at System.Object.c200fe6f-ac33-441b-a3a0-47659e3f6750(IntPtr , IntPtr )
I/monodroid-gref(27679): +w+ grefc 1916 gwrefc 296 obj-handle 0x406b2b98/G -> new-handle 0xde68f4bf/W from take_weak_global_ref_jni
I/monodroid-gref(27679): -w- grefc 1915 gwrefc 294 handle 0xde691aaf/W from take_global_ref_jni
```

有四個訊息的結果：

-  全域參考建立： 這些是開頭的行會 *+ g +* ，並會提供堆疊追蹤建立的程式碼路徑。
-  全域參考解構： 這些是開頭的行會 *-g-* ，處置全域參考的程式碼路徑可能會提供堆疊追蹤。 如果 GC 正在處置的 grf，將會不提供任何堆疊追蹤。
-  全域的弱式參考建立： 這些是開頭的行會 *+ w +* 。
-  全域的弱式參考解構： 這些是開頭行會 *-w-* 。


在所有的訊息中， *grefc*值是 Xamarin.Android 所建立，全域參考計數而*grefwc*值是弱式全域參考 Xamarin.Android 所建立的計數。 *處理*或是*obj 控制代碼*的值是 JNI 控制代碼值，而且後面的字元 ' */*' 是控制代碼值的型別： */L*本機參考，如 */G*全域的參考，和 */W*為全域的弱式參考。

GC 程序的一部分，全域的參考 （+ g +） 會轉換成全域的弱式參考 (造成 a + w + 和-g-)、 開始 Java 端 GC，以及再弱式全域參考會檢查以查看所收集。 如果仍在執行，弱式參考的建立新 grf (+ g +，-w-)，否則為弱式參考被終結時 (-w)。

## <a name="java-instance-is-created-and-wrapped-by-a-mcw"></a>建立並由 MCW 包裝 Java 執行個體

```shell
I/monodroid-gref(27679): +g+ grefc 2211 gwrefc 0 obj-handle 0x4066df10/L -> new-handle 0x4066df10/L from ...
I/monodroid-gref(27679): handle 0x4066df10; key_handle 0x4066df10: Java Type: `android/graphics/drawable/TransitionDrawable`; MCW type: `Android.Graphics.Drawables.TransitionDrawable`
```

## <a name="a-gc-is-being-performed"></a>正在執行 GC...

```shell
I/monodroid-gref(27679): +w+ grefc 1953 gwrefc 259 obj-handle 0x4066df10/G -> new-handle 0xde68f95f/W from take_weak_global_ref_jni
I/monodroid-gref(27679): -g- grefc 1952 gwrefc 259 handle 0x4066df10/G from take_weak_global_ref_jni
```

## <a name="object-is-still-alive-as-handle--null"></a>物件是否仍保持運作，為控制代碼 ！ = null
## <a name="wref-turned-back-into-a-gref"></a>轉換回 grf wref

```shell
I/monodroid-gref(27679): *try_take_global obj=0x4976f080 -> wref=0xde68f95f handle=0x4066df10
I/monodroid-gref(27679): +g+ grefc 1930 gwrefc 39 obj-handle 0xde68f95f/W -> new-handle 0x4066df10/G from take_global_ref_jni
I/monodroid-gref(27679): -w- grefc 1930 gwrefc 38 handle 0xde68f95f/W from take_global_ref_jni
```

## <a name="object-is-dead-as-handle--null"></a>物件是死了，為控制代碼 = = null
## <a name="wref-is-freed-no-new-gref-created"></a>wref 已釋放，任何新建立的 grf

```shell
I/monodroid-gref(27679): *try_take_global obj=0x4976f080 -> wref=0xde68f95f handle=0x0
I/monodroid-gref(27679): -w- grefc 1914 gwrefc 296 handle 0xde68f95f/W from take_global_ref_jni
```

還有一個 「 有趣 」 的缺點： 於執行 Android 4.0 之前的目標，grf 值相當於 Java 物件的位址在 Android 執行階段的記憶體中。 （也就是 GC 是非移動，保守，收集器，而且它會處理這些物件的直接參考）。因此之後 + g + + w +、-g-、 + g +，-w 順序，產生 grf 會有原始 grf 值相同的值。 這可讓透過記錄檔的 grepping 相當簡單。

Android 4.0，不過，已移動的收集器，而且不會再將 Android 執行階段的直接參考 VM 物件。 因此之後, + g + + w +、-g-、 + g +、-w 順序、 grf 值*將會不同*。 如果物件不受影響多個 Gc，它會變成由數個 grf 值，以致更難以判斷實際從配置執行個體。

### <a name="querying-programmatically"></a>以程式設計方式查詢

您可以藉由查詢來查詢 GRF 」 和 「 WREF 計數`JniRuntime`物件。

`Java.Interop.JniRuntime.CurrentRuntime.GlobalReferenceCount` 全域的參考計數

`Java.Interop.JniRuntime.CurrentRuntime.WeakGlobalReferenceCount` -弱式參考計數



## <a name="android-debug-logs"></a>Android 偵錯記錄檔

[Android 偵錯記錄檔](~/android/deploy-test/debugging/android-debug-log.md)可能會提供有關您所見的任何執行階段錯誤的其他內容。



## <a name="floating-point-performance-is-terrible"></a>浮點數的效能很可怕的 ！

或者，「 我的應用程式執行速度快 10 倍比偵錯組建與發行組建 ！ 」

Xamarin.Android 支援多個裝置 Abi: *armeabi*， *armeabi-v7a*，並*x86*。 在中指定裝置 Abi**專案屬性 > 應用程式 索引標籤 > 支援的架構**。

偵錯組建會使用 Android 套件提供所有的 Abi，並因此會使用最快速的 ABI 的目標裝置。

發行組建只會包含在專案屬性 索引標籤中選取的 Abi。您可以選取多個。

*armeabi*是預設值的 ABI，且最廣泛的裝置支援。 *不過*，armeabi 不支援多個 CPU 的裝置和硬體浮點、 amont 其他項目。 因此，應用程式使用 armeabi 版本執行階段會繫結至單一核心，而且將會使用軟浮點實作。 這兩種都可以參與您的應用程式明顯變慢的效能。

如果您的應用程式需要適當的浮點數效能 （例如遊戲），您應該啟用*armeabi-v7a* ABI。 您可能想要只支援*armeabi-v7a*執行階段，這表示，雖然較舊的裝置僅支援*armeabi*將無法執行您的應用程式。



## <a name="could-not-locate-android-sdk"></a>找不到 Android SDK

有 2 下載從 Google 的 Android SDK for Windows。
如果您選擇.exe 安裝程式時，它會寫入告訴 Xamarin.Android 安裝所在的登錄機碼。 如果您選擇的.zip 檔案，並將它解壓縮自行，Xamarin.Android 就不知道哪裡可找到 SDK。 您可以告訴 Xamarin.Android 其中 SDK 是 Visual Studio 中前往**工具 > 選項 > Xamarin > Android 設定**:

[![Xamarin Android 設定 中的 android SDK 位置](troubleshooting-images/01.png)](troubleshooting-images/01.png#lightbox)



## <a name="ide-does-not-display-target-device"></a>IDE 不會顯示目標裝置

有時候您會嘗試在應用程式部署至裝置，但您想要部署至未顯示在 [選取裝置] 對話方塊的裝置。 當 Android Debug Bridge 決定要去渡假時，也可會發生。

若要診斷此問題，找出[adb 程式](~/android/deploy-test/debugging/android-debug-log.md)，然後執行：

```shell
adb devices
```

如果您的裝置不存在，您就需要重新啟動 Android Debug Bridge 伺服器，以便可以找到您的裝置：

```shell
adb kill-server
adb start-server
```

HTC 同步處理軟體可能會阻止**adb 開始伺服器**無法正常運作。 如果**adb 開始伺服器**命令不會列印出連接埠啟動，請結束 HTC 同步處理軟體，然後嘗試重新啟動 adb 伺服器。


## <a name="the-specified-task-executable-keytool-could-not-be-run"></a>無法執行指定的工作可執行檔"keytool"

這表示您的路徑不包含 Java SDK 的 bin 目錄所在的目錄。 請檢查您已遵循這些步驟，從[安裝](~/android/get-started/installation/index.md)指南。


## <a name="monodroidexe-or-aresgenexe-exited-with-code-1"></a>monodroid.exe 或 aresgen.exe 已結束，代碼 1

若要協助您偵錯這個問題，請到 Visual Studio 變更 MSBuild 詳細資訊層級，若要這樣做，請選取：**工具 > 選項 > 專案**並**解決方案 > 建置**並**執行 > MSBuild 專案建置輸出詳細等級**並將此值設定為**一般**。

重建和檢查 Visual Studio 輸出 窗格，其中應包含完整的錯誤。

## <a name="there-is-not-enough-storage-space-on-the-device-to-deploy-the-package"></a>若要將封裝部署在裝置上沒有足夠的儲存空間

會發生這種情況時不啟動 Visual Studio 中的從模擬器。 當啟動模擬器，Visual Studio 之外，您需要傳遞`-partition-size 512`選項，例如

```shell
emulator -partition-size 512 -avd MonoDroid
```

請確定您使用正確的模擬器名稱，亦即[時設定模擬器，您使用的名稱](~/android/get-started/installation/windows.md#device)。


## <a name="installfailedinvalidapk-when-installing-a-package"></a>安裝\_失敗\_無效\_安裝套件時的 APK

Android 套件名稱*必須*包含句號 ('*。*')。 使其包含句號，請編輯您的套件名稱。

-   在 Visual Studio:
    -   以滑鼠右鍵按一下專案 > 屬性
    -   按一下左側 [Android 資訊清單] 索引標籤。
    -   更新 [封裝名稱] 欄位。
        -   如果您看到訊息&ldquo;否 AndroidManifest.xml 找到。 按一下以新增一個。&rdquo;，按一下  連結，然後更新 封裝名稱 欄位。
-   在 Visual Studio for Mac:
    -   以滑鼠右鍵按一下專案 > 選項。
    -   瀏覽至組建 / Android 應用程式一節。
    -   變更封裝名稱欄位必須包含 '。 '。




## <a name="installfailedmissingsharedlibrary-when-installing-a-package"></a>安裝\_失敗\_MISSING\_共用\_安裝套件時的程式庫

稱為 「 共用程式庫 」 在此內容中的是*未*原生共用程式庫 (*libfoo.so*) 檔案; 而是必須分別安裝在目標裝置，例如 Google 地圖的程式庫。

Android 套件可讓您指定的共用程式庫是必要項目`<uses-library/>`項目。 如果*必要*程式庫不存在於目標裝置上 (例如`//uses-library/@android:required`是*true*，預設值)，則套件安裝將會失敗並*安裝\_失敗\_遺漏\_SHARED\_程式庫*。

若要判斷哪一個共用的程式庫所需，請檢視*產生*
**AndroidManifest.xml**檔案 (例如**obj\\偵錯\\android\\AndroidManifest.xml**)，並尋找`<uses-library/>`項目。 `<uses-library/>` 可以手動新增項目，在您的專案**屬性\\AndroidManifest.xml**檔案並透過[UsesLibraryAttribute 自訂屬性](https://developer.xamarin.com/api/type/Android.App.UsesLibraryAttribute/)。

例如，將加入的組件參考*Mono.Android.GoogleMaps.dll*會隱含地新增`<uses-library/>`Google Maps 共用程式庫。



## <a name="installfailedupdateincompatible-when-installing-a-package"></a>安裝\_失敗\_更新\_不相容的安裝套件時

Android 套件有三個需求：

-   它們必須包含 '。 '（請參閱先前的項目）
-   它們必須有唯一字串的套件名稱 (因此在 Android 應用程式名稱，例如 Chrome 應用程式的 com.android.chrome 中看到的反向 tld 慣例)
-   升級封裝時，封裝必須有相同的簽署金鑰。

因此，假設：

1.  您建置與部署您的應用程式，做為偵錯應用程式
2.  您將變更簽章的金鑰，例如為做為發行應用程式 （或因為您不喜歡預設提供偵錯簽署金鑰）
3.  您安裝應用程式，而不需先移除它，例如偵錯 > 啟動但不偵錯 Visual Studio 內


當發生這種情況時，套件安裝將會失敗並安裝\_失敗\_更新\_不相容的錯誤，因為封裝名稱未變更時的簽署金鑰未。 [Android 偵錯記錄檔](~/android/deploy-test/debugging/android-debug-log.md)也會包含類似的訊息：

```shell
E/PackageManager(  146): Package [PackageName] signatures do not match the previously installed version; ignoring!
```

若要修正這個錯誤，完全移除應用程式從您的裝置再重新安裝。


## <a name="installfaileduidchanged-when-installing-a-package"></a>安裝\_失敗\_UID\_安裝套件時，變更

安裝 Android 套件時，它會指派*使用者識別碼*(UID)。
*有時候*，目前為未知原因，透過已安裝的應用程式安裝時，安裝將會失敗並`INSTALL_FAILED_UID_CHANGED`:

```shell
ERROR [2015-03-23 11:19:01Z]: ANDROID: Deployment failed
Mono.AndroidTools.InstallFailedException: Failure [INSTALL_FAILED_UID_CHANGED]
   at Mono.AndroidTools.Internal.AdbOutputParsing.CheckInstallSuccess(String output, String packageName)
   at Mono.AndroidTools.AndroidDevice.<>c__DisplayClass2c.<InstallPackage>b__2b(Task`1 t)
   at System.Threading.Tasks.ContinuationTaskFromResultTask`1.InnerInvoke()
   at System.Threading.Tasks.Task.Execute()
```

若要解決這個問題，請*完全解除安裝*Android 套件，藉由從 Android 目標的 GUI 安裝應用程式，或使用`adb`:

```shell
$ adb uninstall @PACKAGE_NAME@
```

**請勿使用** `adb uninstall -k`，這*保留*應用程式資料，並因此保留衝突的 UID，目標裝置上。



## <a name="release-apps-fail-to-launch-on-device"></a>發行應用程式無法在裝置上啟動

Android 偵錯記錄檔輸出會包含類似的訊息：

```shell
D/AndroidRuntime( 1710): Shutting down VM
W/dalvikvm( 1710): threadid=1: thread exiting with uncaught exception (group=0xb412f180)
E/AndroidRuntime( 1710): FATAL EXCEPTION: main
E/AndroidRuntime( 1710): java.lang.UnsatisfiedLinkError: Couldn't load monodroid: findLibrary returned null
E/AndroidRuntime( 1710):        at java.lang.Runtime.loadLibrary(Runtime.java:365)
```

如果是的話，有兩個可能的原因：

1.  .Apk 不提供目標裝置支援的 ABI。
    比方說，.apk 只包含 armeabi-v7a 二進位檔，而且目標裝置僅支援 armeabi。

2.  [Android bug](http://code.google.com/p/android/issues/detail?id=21670)。 如果發生這種情況，解除安裝應用程式、 跨手指，並重新安裝應用程式。

若要修正 (1)，編輯 專案選項/屬性並[必要的 ABI 的支援新增至支援的 Abi 清單](~/android/app-fundamentals/cpu-architectures.md)。 若要判斷您需要新增哪個 ABI，請針對您的目標裝置執行下列 adb 命令：

```shell
adb shell getprop ro.product.cpu.abi
adb shell getprop ro.product.cpu.abi2
```

輸出會包含主要 （和選擇性次要） Abi。

```shell
$ adb shell getprop | grep ro.product.cpu
[ro.product.cpu.abi2]: [armeabi]
[ro.product.cpu.abi]: [armeabi-v7a]
```

## <a name="the-outpath-property-is-not-set-for-project-ldquomyappcsprojrdquo"></a>專案未設定 OutPath 屬性&ldquo;MyApp.csproj&rdquo;

這通常表示您有 HP 電腦和環境變數&ldquo;平台&rdquo;MCD 或 HPD 等已設定為項目。 這通常設定為 MSBuild 平台屬性與衝突&ldquo;任何 CPU&rdquo;或是&ldquo;x86&rdquo;。 您必須從電腦移除這個環境變數，然後 MSBuild 才能運作：

-   控制台 > System > 進階 > 環境變數

重新啟動 Visual Studio 或 Visual Studio for Mac，然後再次嘗試重建。 項目現在應該在如預期般運作。

## <a name="javalangclasscastexception-monoandroidruntimejavaobject-cannot-be-cast-to"></a>java.lang.ClassCastException: mono.android.runtime.JavaObject 無法轉換成...

Xamarin.Android 4.x 不正確地封送處理巢狀泛型類型正確。 例如，請考慮下列 C\#使用程式碼[SimpleExpandableListAdapter](https://developer.xamarin.com/api/type/Android.Widget.SimpleExpandableListAdapter/):


```csharp
// BAD CODE; DO NOT USE
var groupData = new List<IDictionary<string, object>> () {
        new Dictionary<string, object> {
                { "NAME", "Group 1" },
                { "IS_EVEN", "This group is odd" },
        },
};
var childData = new List<IList<IDictionary<string, object>>> () {
        new List<IDictionary<string, object>> {
                new Dictionary<string, object> {
                        { "NAME", "Child 1" },
                        { "IS_EVEN", "This group is odd" },
                },
        },
};
mAdapter = new SimpleExpandableListAdapter (
        this,
        groupData,
        Android.Resource.Layout.SimpleExpandableListItem1,
        new string[] { "NAME", "IS_EVEN" },
        new int[] { Android.Resource.Id.Text1, Android.Resource.Id.Text2 },
        childData,
        Android.Resource.Layout.SimpleExpandableListItem2,
        new string[] { "NAME", "IS_EVEN" },
        new int[] { Android.Resource.Id.Text1, Android.Resource.Id.Text2 }
);
```


問題在於，Xamarin.Android 不正確地封送處理巢狀泛型類型。 `List<IDictionary<string, object>>`要封送處理至[java.lang.ArrrayList](https://developer.xamarin.com/api/type/Java.Util.ArrayList/)，但`ArrayList`這包含`mono.android.runtime.JavaObject`執行個體 (其中參考`Dictionary<string, object>`執行個體) 而不是實作的項目[java.util.Map](https://developer.xamarin.com/api/type/Java.Util.IMap/)，產生的下列例外狀況：

```shell
E/AndroidRuntime( 2991): FATAL EXCEPTION: main
E/AndroidRuntime( 2991): java.lang.ClassCastException: mono.android.runtime.JavaObject cannot be cast to java.util.Map
E/AndroidRuntime( 2991):        at android.widget.SimpleExpandableListAdapter.getGroupView(SimpleExpandableListAdapter.java:278)
E/AndroidRuntime( 2991):        at android.widget.ExpandableListConnector.getView(ExpandableListConnector.java:446)
E/AndroidRuntime( 2991):        at android.widget.AbsListView.obtainView(AbsListView.java:2271)
E/AndroidRuntime( 2991):        at android.widget.ListView.makeAndAddView(ListView.java:1769)
E/AndroidRuntime( 2991):        at android.widget.ListView.fillDown(ListView.java:672)
E/AndroidRuntime( 2991):        at android.widget.ListView.fillFromTop(ListView.java:733)
E/AndroidRuntime( 2991):        at android.widget.ListView.layoutChildren(ListView.java:1622)
```

因應措施為使用所提供[Java 集合型別](~/android/internals/api-design.md)而不是`System.Collections.Generic`型別的&ldquo;內部&rdquo;型別。 封送處理的執行個體時，這會導致適當的 Java 類型。 （下列程式碼會比以降低 grf 存留期需要更複雜。 它可以改變原始的程式碼，透過簡化`s/List/JavaList/g`和`s/Dictionary/JavaDictionary/g`如果 grf 存留期不擔心。)

```csharp
// insert good code here
using (var groupData = new JavaList<IDictionary<string, object>> ()) {
    using (var groupEntry = new JavaDictionary<string, object> ()) {
        groupEntry.Add ("NAME", "Group 1");
        groupEntry.Add ("IS_EVEN", "This group is odd");
        groupData.Add (groupEntry);
    }
    using (var childData = new JavaList<IList<IDictionary<string, object>>> ()) {
        using (var childEntry = new JavaList<IDictionary<string, object>> ())
        using (var childEntryDict = new JavaDictionary<string, object> ()) {
            childEntryDict.Add ("NAME", "Child 1");
            childEntryDict.Add ("IS_EVEN", "This child is odd.");
            childEntry.Add (childEntryDict);
            childData.Add (childEntry);
        }
        mAdapter = new SimpleExpandableListAdapter (
            this,
            groupData,
            Android.Resource.Layout.SimpleExpandableListItem1,
            new string[] { "NAME", "IS_EVEN" },
            new int[] { Android.Resource.Id.Text1, Android.Resource.Id.Text2 },
            childData,
            Android.Resource.Layout.SimpleExpandableListItem2,
            new string[] { "NAME", "IS_EVEN" },
            new int[] { Android.Resource.Id.Text1, Android.Resource.Id.Text2 }
        );
    }
}
```

[這將在未來版本中修正](https://bugzilla.xamarin.com/show_bug.cgi?id=5401)。


## <a name="unexpected-nullreferenceexceptions"></a>非預期的 NullReferenceExceptions

偶爾[Android 偵錯記錄檔](~/android/deploy-test/debugging/android-debug-log.md)會提及 NullReferenceExceptions，&ldquo;不會發生&rdquo;或來自 Mono for Android 執行階段應用程式就會中斷之前的程式碼：

```shell
E/mono(15202): Unhandled Exception: System.NullReferenceException: Object reference not set to an instance of an object
E/mono(15202):   at Java.Lang.Object.GetObject (IntPtr handle, System.Type type, Boolean owned)
E/mono(15202):   at Java.Lang.Object._GetObject[IOnTouchListener] (IntPtr handle, Boolean owned)
E/mono(15202):   at Java.Lang.Object.GetObject[IOnTouchListener] (IntPtr handle, Boolean owned)
E/mono(15202):   at Android.Views.View+IOnTouchListenerAdapter.n_OnTouch_Landroid_view_View_Landroid_view_MotionEvent_(IntPtr jnienv, IntPtr native__this, IntPtr native_v, IntPtr native_e)
E/mono(15202):   at (wrapper dynamic-method) object:b039cbb0-15e9-4f47-87ce-442060701362 (intptr,intptr,intptr,intptr)
```

或

```shell
E/mono    ( 4176): Unhandled Exception:
E/mono    ( 4176): System.NullReferenceException: Object reference not set to an instance of an object
E/mono    ( 4176): at Android.Runtime.JNIEnv.NewString (string)
E/mono    ( 4176): at Android.Util.Log.Info (string,string)
```

當 Android 執行階段決定中止可能發生的原因，包括達到目標的 GRF 限制，或執行任意數目的程序，可以發生此狀況&ldquo;錯誤&rdquo;使用 JNI。

若要查看是否為這種情況下，檢查 Android 的偵錯記錄檔訊息，以從您的程序類似於：

```shell
E/dalvikvm(  123): VM aborting
```


## <a name="abort-due-to-global-reference-exhaustion"></a>因為全域參考耗盡中止

Android 執行階段的 JNI 層僅支援有限的數目的 JNI 物件參考的時間會在任何指定時間點有效。 當超過這個限制時，事情就會中斷。

GRF (*全域參考*) 限制為 2000年參考在模擬器中，而在硬體上的 ~ 52000 參考。

您知道您正在建立太多 GREFs，當您看到這類 Android 的偵錯記錄檔中的訊息：

```shell
D/dalvikvm(  602): GREF has increased to 1801
```

當您到達 GRF 限制時，會列印訊息，如下所示：

```shell
D/dalvikvm(  602): GREF has increased to 2001
W/dalvikvm(  602): Last 10 entries in JNI global reference table:
W/dalvikvm(  602):  1991: 0x4057eff8 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1992: 0x4057f010 cls=Landroid/graphics/Point; (28 bytes)
W/dalvikvm(  602):  1993: 0x40698e70 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1994: 0x40698e88 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1995: 0x40698ea0 cls=Landroid/graphics/Point; (28 bytes)
W/dalvikvm(  602):  1996: 0x406981f0 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1997: 0x40698208 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1998: 0x40698220 cls=Landroid/graphics/Point; (28 bytes)
W/dalvikvm(  602):  1999: 0x406956a8 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  2000: 0x406956c0 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602): JNI global reference table summary (2001 entries):
W/dalvikvm(  602):    51 of Ljava/lang/Class; 164B (41 unique)
W/dalvikvm(  602):    46 of Ljava/lang/Class; 188B (17 unique)
W/dalvikvm(  602):     6 of Ljava/lang/Class; 212B (6 unique)
W/dalvikvm(  602):    11 of Ljava/lang/Class; 236B (7 unique)
W/dalvikvm(  602):     3 of Ljava/lang/Class; 260B (3 unique)
W/dalvikvm(  602):     4 of Ljava/lang/Class; 284B (2 unique)
W/dalvikvm(  602):     8 of Ljava/lang/Class; 308B (6 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 316B
W/dalvikvm(  602):     4 of Ljava/lang/Class; 332B (3 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 356B
W/dalvikvm(  602):     2 of Ljava/lang/Class; 380B (1 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 428B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 452B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 476B
W/dalvikvm(  602):     2 of Ljava/lang/Class; 500B (1 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 548B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 572B
W/dalvikvm(  602):     2 of Ljava/lang/Class; 596B (2 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 692B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 956B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 1004B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 1148B
W/dalvikvm(  602):     2 of Ljava/lang/Class; 1172B (1 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 1316B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 3428B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 3452B
W/dalvikvm(  602):     1 of Ljava/lang/String; 28B
W/dalvikvm(  602):     2 of Ldalvik/system/VMRuntime; 12B (1 unique)
W/dalvikvm(  602):    10 of Ljava/lang/ref/WeakReference; 28B (10 unique)
W/dalvikvm(  602):     1 of Ldalvik/system/PathClassLoader; 44B
W/dalvikvm(  602):  1553 of Landroid/graphics/Point; 20B (1553 unique)
W/dalvikvm(  602):   261 of Landroid/graphics/Point; 28B (261 unique)
W/dalvikvm(  602):     1 of Landroid/view/MotionEvent; 100B
W/dalvikvm(  602):     1 of Landroid/app/ActivityThread$ApplicationThread; 28B
W/dalvikvm(  602):     1 of Landroid/content/ContentProvider$Transport; 28B
W/dalvikvm(  602):     1 of Landroid/view/Surface$CompatibleCanvas; 44B
W/dalvikvm(  602):     1 of Landroid/view/inputmethod/InputMethodManager$ControlledInputConnectionWrapper; 36B
W/dalvikvm(  602):     1 of Landroid/view/ViewRoot$1; 12B
W/dalvikvm(  602):     1 of Landroid/view/ViewRoot$W; 28B
W/dalvikvm(  602):     1 of Landroid/view/inputmethod/InputMethodManager$1; 28B
W/dalvikvm(  602):     1 of Landroid/view/accessibility/AccessibilityManager$1; 28B
W/dalvikvm(  602):     1 of Landroid/widget/LinearLayout$LayoutParams; 44B
W/dalvikvm(  602):     1 of Landroid/widget/LinearLayout; 332B
W/dalvikvm(  602):     2 of Lorg/apache/harmony/xnet/provider/jsse/TrustManagerImpl; 28B (1 unique)
W/dalvikvm(  602):     1 of Landroid/view/SurfaceView$MyWindow; 36B
W/dalvikvm(  602):     1 of Ltouchtest/RenderThread; 92B
W/dalvikvm(  602):     1 of Landroid/view/SurfaceView$3; 12B
W/dalvikvm(  602):     1 of Ltouchtest/DrawingView; 412B
W/dalvikvm(  602):     1 of Ltouchtest/Activity1; 180B
W/dalvikvm(  602): Memory held directly by tracked refs is 75624 bytes
E/dalvikvm(  602): Excessive JNI global references (2001)
E/dalvikvm(  602): VM aborting
```


在上述範例中 (即順帶一提，來自[bug 685215](https://bugzilla.novell.com/show_bug.cgi?id=685215)) 問題是，太多 Android.Graphics.Point 正在建立執行個體，請參閱[註解\#2](https://bugzilla.novell.com/show_bug.cgi?id=685215#c2)的修正程式清單這個特定的錯誤。

通常很實用的解決方案是要尋找的型別有太多的執行個體配置&ndash;上述的傾印中 Android.Graphics.Point&ndash;然後尋找 其中建立您的原始程式碼和處置它們適當地 (使其Java-object 存留期已縮短)。 這不一定都適合 (\#685215 是多執行緒，因此簡單式解決方案可避免 Dispose 呼叫)，但首先来考慮。

您可以讓[GRF 記錄](~/android/troubleshooting/index.md)GREFs 建立時，以及多少存在。


## <a name="abort-due-to-jni-type-mismatch"></a>中止因為 JNI 類型不符

如果您手動-向前復原 JNI 程式碼，很可能類型不符合正確，例如： 如果您嘗試叫用`java.lang.Runnable.run`不會實作的型別上`java.lang.Runnable`。 當發生這種情況時，會有一則訊息像這樣 Android 偵錯記錄檔中：

```shell
W/dalvikvm( 123): JNI WARNING: can't call Ljava/Type;;.method on instance of Lanother/java/Type;
W/dalvikvm( 123):              in Lmono/java/lang/RunnableImplementor;.n_run:()V (CallVoidMethodA)
...
E/dalvikvm( 123): VM aborting
```

## <a name="dynamic-code-support"></a>動態程式碼支援

### <a name="dynamic-code-does-not-compile"></a>動態程式碼無法編譯

若要使用 C\#動態應用程式或程式庫中，您有 system.core.dll 的參考、 Microsoft.CSharp.dll 和 Mono.CSharp.dll 加入您的專案。

### <a name="in-release-build-missingmethodexception-occurs-for-dynamic-code-at-run-time"></a>在發行組建 MissingMethodException 動態程式碼在執行階段發生。

-   很可能您的應用程式專案沒有 system.core.dll 的參考、 Microsoft.CSharp.dll 或 Mono.CSharp.dll 參考。 請確定會參考這些組件。

    -   請記住該動態程式碼一律成本。 如果您需要有效率的程式碼，請考慮不使用動態程式碼。

-   在第一個預覽中，除非應用程式程式碼會明確地使用每個組件中的型別，這些組件都已排除。 請參閱以下的因應措施： [http://lists.ximian.com/pipermail/mo...il/009798.html](http://lists.ximian.com/pipermail/monodroid/2012-April/009798.html)


## <a name="projects-built-with-aotllvm-crash-on-x86-devices"></a>在 x86 上使用 AOT + LLVM 損毀所建置的專案的裝置

部署以建置應用程式時[AOT + LLVM](~/android/deploy-test/release-prep/index.md) x86 型裝置上，您可能會看到類似如下的例外狀況錯誤訊息：

```shell
Assertion: should not be reached at /Users/.../external/mono/mono/mini/tramp-x86.c:124
Fatal signal 6 (SIGABRT), code -6 in tid 4051 (amarin.bug56111)
```

這是所報告的已知的問題[56111](https://bugzilla.xamarin.com/show_bug.cgi?id=56111)。 因應措施是停用 LLVM。
