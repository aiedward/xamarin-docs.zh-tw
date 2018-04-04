---
title: 疑難排解秘訣
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 56137ACA-4811-B312-6860-E16D0FA123F7
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/15/2018
ms.openlocfilehash: 961f9f38687790343f225d95c74e00e98f594c28
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="troubleshooting-tips"></a>疑難排解秘訣


## <a name="getting-diagnostic-information"></a>取得診斷資訊

Xamarin.Android 有幾個地方看起來會追蹤有許多錯誤。
它們包括：

1.  診斷 MSBuild 輸出。
2.  裝置部署記錄檔。
3.  Android 的偵錯記錄檔輸出。


<a name="Diagnostic_MSBuild_Output" />

## <a name="diagnostic-msbuild-output"></a>診斷 MSBuild 輸出

診斷 MSBuild 可能包含有關封裝建置的其他資訊，而且可能包含某些套件的部署資訊。

若要在 Visual Studio 內啟用診斷 MSBuild 輸出：

1.  按一下**工具 > 選項...**
2.  在左側樹狀檢視中，選取**專案和方案 > 建置並執行**
3.  在右面板中，設定 MSBuild 建置輸出詳細等級的下拉式清單中為診斷
4.  按一下 [確定] 
5.  清除並重建您的套件。
6.  輸出面板中看見診斷輸出。


若要啟用 Visual Studio 中的診斷 MSBuild 輸出 Mac/OS x:

1.  按一下**Visual Studio for Mac > 喜好設定...**
2.  在左側樹狀檢視中，選取**專案 > 建置**
3.  在右面板中，設定記錄檔詳細等級 下拉式清單為診斷
4.  按一下 [確定] 
5.  重新啟動 Visual Studio for Mac
6.  清除並重建您的套件。
7.  診斷的輸出會顯示錯誤板內 (**檢視 > 填補 > 錯誤**)，依序按一下 [建置輸出] 按鈕。




## <a name="device-deployment-logs"></a>裝置部署記錄檔

若要啟用 Visual Studio 中的裝置部署記錄：

1.  **工具 > 選項...**>
2.  在左側樹狀檢視中，選取**Xamarin > Android 設定**
3.  在右面板中，啟用 [X]**延伸模組的偵錯記錄 （寫入至您的桌面的 monodroid.log）**核取方塊。
4.  在桌面上 monodroid.log 檔案會寫入記錄檔訊息。


Visual Studio for Mac 一律會寫入裝置部署記錄檔。 找到這些物件會稍微更困難;*AndroidUtils*每個日期 + 時間部署，例如建立記錄檔： **AndroidTools-2012-10-24_12-35-45.log**。

-  在 Windows 中，記錄檔會寫入至`%LOCALAPPDATA%\XamarinStudio-{VERSION}\Logs`。
-  在 OS X 上，記錄檔會寫入至`$HOME/Library/Logs/XamarinStudio-{VERSION}`。




## <a name="android-debug-log-output"></a>Android 的偵錯記錄檔輸出

Android 將會寫入至許多訊息[Android 偵錯記錄檔](~/android/deploy-test/debugging/android-debug-log.md)。
Xamarin.Android 使用 Android 的系統屬性，來控制 Android 的偵錯記錄檔的其他訊息產生。 Android 的系統屬性可以透過設定*setprop*命令內[Android Debug Bridge (adb)](http://developer.android.com/guide/developing/tools/adb.html):

```shell
adb shell setprop PROPERTY_NAME PROPERTY_VALUE
```

系統屬性程序在啟動期間，會讀取，因此必須先設定應用程式啟動或系統屬性已變更之後，必須重新啟動應用程式。



### <a name="xamarinandroid-system-properties"></a>Xamarin.Android 系統屬性

Xamarin.Android 支援下列系統屬性：

-   *debug.mono.debug*： 若為非空白字串，這相當於`*mono-debug*`。

-   *debug.mono.env*： 管道分隔 ('*|*') 的應用程式在啟動期間，匯出環境變數清單*之前*單聲道已初始化。 這可讓控制項單聲道記錄設定環境變數。

    - *請注意*： 由於的值為 '*|*'-分隔，值必須用引號括住，額外層級為\` *adb 殼層*\`命令會移除一組雙引號。

    - *請注意*: Android 的系統屬性值可以是未超過 92 個字元的長度。

    - 範例：

            adb shell setprop debug.mono.env "'MONO_LOG_LEVEL=info|MONO_LOG_MASK=asm'"

-   *debug.mono.log*： 以逗號分隔 ('*，*') 的列印 Android 的偵錯記錄檔的其他訊息的元件清單。 根據預設，會設定做任何動作。 元件包括：

    -   *所有*： 列印所有訊息
    -   *gc*： 列印 GC 相關訊息。
    -   *gref*： 列印弱式 （全域） 參考的配置和解除配置的訊息。
    -   *lref*： 列印本機參考配置和解除配置訊息。

    *請注意*： 這些是*極*詳細資訊。 除非您真的需要否則請勿啟用。

-   *debug.mono.trace*： 允許設定[mono-追蹤](http://docs.go-mono.com/?link=man%3amono(1))`=PROPERTY_VALUE`設定。



## <a name="xamarinandroid-cannot-resolve-systemvaluetuple"></a>Xamarin.Android 無法解析 System.ValueTuple

這個錯誤是因為 Visual Studio 中的不相容。

- **Visual Studio 2017 Update 1** （15.1 或較舊版本） 才相容**System.ValueTuple NuGet 4.3.0** （或舊版）。

- **Visual Studio 2017 Update 2** （版本 15.2 或更新版本） 是只與相容**System.ValueTuple NuGet 4.3.1** （或更新版本）。

請選擇您的 Visual Studio 2017 安裝對應的正確 System.ValueTuple NuGet。


## <a name="gc-messages"></a>GC 訊息

GC 元件訊息可以檢視的 debug.mono.log 系統屬性設為包含 gc 的值。

每當執行 GC，並提供關於多少工作 GC 未資訊時，會產生 GC 的訊息：

```shell
I/monodroid-gc(12331): GC cleanup summary: 81 objects tested - resurrecting 21.
```

其他的 GC 資訊，例如計時資訊可以由設定產生`MONO_LOG_LEVEL`環境變數，以`debug`:

```shell
adb shell setprop debug.mono.env MONO_LOG_LEVEL=debug
```

這將導致 （大量） 額外單聲道的訊息，包括下列三個後果：

```shell
D/Mono (15723): GC_BRIDGE num-objects 1 num_hash_entries 81226 sccs size 81223 init 0.00ms df1 285.36ms sort 38.56ms dfs2 50.04ms setup-cb 9.95ms free-data 106.54ms user-cb 20.12ms clenanup 0.05ms links 5523436/5523436/5523096/1 dfs passes 1104 6883/11046605
D/Mono (15723): GC_MINOR: (Nursery full) pause 2.01ms, total 287.45ms, bridge 225.60 promoted 0K major 325184K los 1816K
D/Mono ( 2073): GC_MAJOR: (user request) pause 2.17ms, total 2.47ms, bridge 28.77 major 576K/576K los 0K/16K
```

在`GC_BRIDGE`訊息，`num-objects`是此階段的考量，橋接器物件的數目和`num_hash_entries`是橋接程式碼的這個引動過程期間處理的物件數目。

中`GC_MINOR`和`GC_MAJOR`訊息，`total`世界已暫停時的時間量 （沒有執行緒執行），而`bridge`是橋接器處理的程式碼 （處理與 Java VM） 中所花費的時間量。 世界*不*橋接器處理進行時暫停。

 *一般情況下*的值越大`num_hash_entries`、 更多時間`bridge`需要集合，和較大`total`收集所花費的時間將會是。



## <a name="global-reference-messages"></a>全域參考訊息

若要啟用記錄、 參考全域 loggig (GREF) *debug.mono.log*系統屬性必須包含*gref*，例如：

```shell
adb shell setprop debug.mono.log gref
```

Xamarin.Android 會叫用的 Java 執行個體必須提供 java Java 方法時，提供 Java 執行個體和相關的 managed 執行個體之間的對應做為使用 Android 全域的參考。

不幸的是，Android 模擬器只允許 2000年全域參考存在一次。 硬體，具有較高限制的 52000 全域的參考。 較低的限制可能會造成問題，因此了解，在模擬器上執行應用程式時*其中*執行個體來自就非常有用。

 *請注意*： 全域的參考計數是內部 Xamarin.Android，，並不會 （而且也無法） 包含全域由其他處理程序所載入的原生程式庫的參考。 使用全域參考計數，做為估計值。

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

-  建立全域參考： 這些是以開頭的程式行*+ g +* ，並會提供堆疊追蹤建立的程式碼路徑。
-  全域參考解構： 這些是以開頭的程式行*-g-* ，可能會提供堆疊追蹤處置全域參考的程式碼路徑。 如果 GC 處置 gref，系統會不提供任何堆疊追蹤。
-  全域的弱式參考建立： 這些是以開頭的程式行*+ w +* 。
-  全域的弱式參考解構： 這些是以開頭的行*-w-* 。


在所有訊息， *grefc*值是全域 Xamarin.Android 已建立的參考計數而*grefwc*值是弱式 Xamarin.Android 已建立的全域參考計數。 *處理*或*obj 控制代碼*值是 JNI 控制代碼值，並在之後的字元 ' */*' 是控制代碼值的型別： */L*對於本機的參考， */G*全域的參考和*/W*為全域的弱式參考。

一部分的 GC 程序的詳細資訊，通用參考 （+ g +） 會轉換成全域的弱式參考 (造成 + w + 和-g-)、 Java 端 GC 開始，然後弱式通用的參考會檢查以查看所收集。 如果仍在執行，建立新 gref 周圍的弱式參考 (+ g +、-w-)，否則為弱式參考被終結 (-w)。

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

## <a name="object-is-still-alive-as-handle--null"></a>物件是仍在執行，以控制代碼 ！ = null
## <a name="wref-turned-back-into-a-gref"></a>wref 變成 gref

```shell
I/monodroid-gref(27679): *try_take_global obj=0x4976f080 -> wref=0xde68f95f handle=0x4066df10
I/monodroid-gref(27679): +g+ grefc 1930 gwrefc 39 obj-handle 0xde68f95f/W -> new-handle 0x4066df10/G from take_global_ref_jni
I/monodroid-gref(27679): -w- grefc 1930 gwrefc 38 handle 0xde68f95f/W from take_global_ref_jni
```

## <a name="object-is-dead-as-handle--null"></a>物件已無作用，以控制代碼 = = null
## <a name="wref-is-freed-no-new-gref-created"></a>wref 會釋放，任何新建立的 gref

```shell
I/monodroid-gref(27679): *try_take_global obj=0x4976f080 -> wref=0xde68f95f handle=0x0
I/monodroid-gref(27679): -w- grefc 1914 gwrefc 296 handle 0xde68f95f/W from take_global_ref_jni
```

還有一個 「 有趣 」 的 wrinkle： 執行 Android 4.0 之前的目標，gref 值是否等於 Android 的執行階段記憶體 Java 物件的位址。 （也就是 GC 是非移動、 保守、 收集器，且它送出這些物件的直接參考。）因此之後 + g + + w +、-g-、 + g +、-w 順序，產生 gref 會有原始 gref 值相同的值。 這可讓透過記錄 grepping 相當簡單。

Android 4.0，不過，已移動的收集器，並不會再交給 Android 的執行階段的直接參考 VM 物件。 因此之後, + g + + w +、-g-、 + g +、-w 順序、 gref 值*不同*。 如果物件未多 Gc，就會移幾個 gref 值、 難以判斷實際從配置執行個體。

### <a name="querying-programmatically"></a>以程式設計方式查詢

您可以藉由查詢來查詢 GREF 和 WREF 計數`JniRuntime`物件。

`Java.Interop.JniRuntime.CurrentRuntime.GlobalReferenceCount` 全域的參考計數

`Java.Interop.JniRuntime.CurrentRuntime.WeakGlobalReferenceCount` -弱式參考計數



## <a name="offline-activation"></a>離線啟用

如果您無法啟動 Xamarin.Android 在 Windows 中，或無法安裝完整版的 Xamarin.Android Mac OS X 上，請參閱[離線啟用](~/android/get-started/installation/index.md)頁面。



## <a name="cant-upgrade-to-indiebusiness-from-trial-account"></a>無法從試用帳戶升級為 Indie/Business

如果您最近購買 Xamarin.Android，先前已啟動 Xamarin.Android 試用版，您可能需要完成下列步驟，以取得這項授權變更收取的 Visual Studio for Mac 或 Visual Studio。

-  關閉 Visual Studio for Mac/Visual Studio
-  移除所有檔案在 Mac 上的 ~/Library/MonoAndroid 或 %PROGRAMDATA%\Mono Android\License\ Windows 的
-  重新開啟 Visual Studio for Mac/Visual Studio，並建置 Xamarin.Android 專案


這可讓您啟動並執行。 如果繼續發生問題，您可能想要再試一次[離線啟用](~/android/get-started/installation/index.md)以完成您的工作站啟用。



## <a name="receiving-activation-incomplete-error-message"></a>接收 ' 啟動不完整的錯誤訊息

使用 Xamarin.Android for Visual Studio 時，可能會發生此問題。 若要解決此問題，請從傳送記錄檔的下列位置*contact@xamarin.com*。

-  記錄位置： **%localappdata%\\Xamarin\\記錄檔**




## <a name="receiving-error-retrieving-update-information-error-message"></a>收到 '擷取更新資訊時發生錯誤' 錯誤訊息

有時候，更新將會失敗並檢查更新時，通常會發生此下列錯誤：

大部分的時間，可以解決這個錯誤，只要記錄無法使用 Xamarin 帳戶，然後再記錄變回中。

若要這麼做，請找出您的平台選擇下方的並遵循的步驟：

**在 Mac 上**：
1. 開啟 Visual Studio for Mac
2. 選取 Visual Studio for Mac > 帳戶...
3. 按一下 登出
4. 按一下 登入
5. 輸入您的認證
6. 檢查更新

**在電腦上使用 Visual Studio:**
1. 開啟 Visual Studio
2. 選取 [工具] > Xamarin 帳戶
3. 按一下 登出
4. 按一下 登入
5. 輸入您的認證
6. 檢查更新

如果此錯誤訊息持續出現，請傳送電子郵件**contact@xamarin.com**。




## <a name="android-debug-logs"></a>Android 的偵錯記錄檔

[Android 偵錯記錄檔](~/android/deploy-test/debugging/android-debug-log.md)可能會提供關於您看到任何執行階段錯誤的其他內容。



## <a name="floating-point-performance-is-terrible"></a>浮點效能太可怕了 ！

或者，「 我的應用程式執行 10 倍速度與發行組建的偵錯組建比 ！ 」

Xamarin.Android 支援多個裝置 ABIs: *armeabi*， *armeabi v7a*，和*x86*。 在中指定裝置 ABIs**專案屬性 > 應用程式 索引標籤 > 支援的架構**。

偵錯組建中使用它可提供所有 ABIs，並因此將會使用最快速的 ABI 的目標裝置的 Android 套件。

發行組建只會包含在專案屬性 索引標籤中選取 ABIs。可以選取多個。

*armeabi*預設 ABI 中，而且有廣泛的裝置支援。 *不過*，armeabi 不支援多重 CPU 裝置和硬體浮點、 amont 其他項目。 因此，應用程式使用 armeabi 版本執行階段會繫結至單一核心，而且將會使用軟浮點實作。 這兩種可以參與您的應用程式明顯變慢的效能。

如果您的應用程式需要不錯浮點效能 （例如遊戲），您應該啟用*armeabi v7a* ABI。 您可能想要只支援*armeabi v7a*執行階段中，這表示，雖然較舊的裝置僅支援*armeabi*將無法執行您的應用程式。



## <a name="could-not-locate-android-sdk"></a>找不到 Android SDK

有可用的 Android SDK for windows 的 Google 的 2 的下載項目。
如果您選擇.exe 安裝程式，則會寫入告訴 Xamarin.Android 安裝位置的登錄機碼。 如果您選擇的.zip 檔案，並將它解壓縮自行，Xamarin.Android 不知道哪裡可找到 SDK。 您可以告訴 Xamarin.Android 其中 SDK 是 Visual Studio 中，請前往**工具 > 選項 > Xamarin > Android 設定**:

[![Android SDK Xamarin Android 設定中的位置](troubleshooting-images/01.png)](troubleshooting-images/01.png#lightbox)



## <a name="ide-does-not-display-target-device"></a>IDE 不會顯示目標裝置

有時候您會嘗試在應用程式部署至裝置，但您想要將部署到不會顯示在 [選取的裝置] 對話方塊的裝置。 Android Debug Bridge 決定休假時，也可能會發生。

若要診斷此問題，找出[adb 程式](~/android/deploy-test/debugging/android-debug-log.md)，然後執行：

```shell
adb devices
```

如果您的裝置不存在，您需要重新啟動 Android Debug Bridge 伺服器，如此可以找到您的裝置：

```shell
adb kill-server
adb start-server
```

HTC 同步軟體可能會導致**adb 啟動 server**無法正常運作。 如果**adb 啟動 server**命令不會列印出啟動的通訊埠，請結束 HTC 同步處理軟體，並再試一次重新啟動 adb 伺服器。


## <a name="the-specified-task-executable-keytool-could-not-be-run"></a>無法執行指定的工作可執行檔"keytool"

這表示您的路徑不包含 Java SDK 的 bin 目錄所在的目錄。 檢查您遵循這些步驟從[安裝](~/android/get-started/installation/index.md)指南。


## <a name="monodroidexe-or-aresgenexe-exited-with-code-1"></a>monodroid.exe 或 aresgen.exe 結束，代碼 1

若要協助您偵錯這個問題，請移到 Visual Studio，然後變更 MSBuild 詳細資訊層級，若要這樣做，請選取：**工具 > 選項 > 專案**和**解決方案 > 建置**和**執行 >MSBuild 專案組建輸出詳細等級**並將此值設定為**一般**。

重建，並檢查 Visual Studio 輸出 窗格，其中應包含完整的錯誤。

## <a name="there-is-not-enough-storage-space-on-the-device-to-deploy-the-package"></a>將封裝部署在裝置上沒有足夠的儲存空間

會發生這種情況是當您不要開始從 Visual Studio 中的模擬器。 當啟動 Visual Studio 外部模擬器時，您需要將`-partition-size 512`選項，例如：

```shell
emulator -partition-size 512 -avd MonoDroid
```

請確定您使用正確的模擬器名稱，也就是[設定模擬器時，您使用的名稱](~/android/get-started/installation/windows.md#device)。


## <a name="installfailedinvalidapk-when-installing-a-package"></a>安裝\_失敗\_無效\_APK 安裝套件時

Android 套件名稱*必須*包含句號 ('*。*')。 編輯封裝名稱，使其包含句號。

-   在 Visual Studio:
    -   以滑鼠右鍵按一下您的專案 > 屬性
    -   按一下 [Android 資訊清單] 索引標籤左側。
    -   更新封裝名稱 欄位。
        -   如果您看到 「&ldquo;否 AndroidManifest.xml 找到。 按一下以加入一個參考。&rdquo;、 按一下連結，然後更新 [封裝名稱] 欄位。
-   在 Visual Studio for Mac:
    -   以滑鼠右鍵按一下您的專案 > 選項。
    -   瀏覽至組建 / Android 應用程式 > 一節。
    -   變更封裝名稱欄位必須包含 '。 '。




## <a name="installfailedmissingsharedlibrary-when-installing-a-package"></a>安裝\_失敗\_遺失\_共用\_安裝套件時的程式庫

「 共用媒體櫃 」 在此內容中的是*不*原生的共用程式庫 (*libfoo.so*) 檔案; 而是必須分別安裝在目標裝置，例如 Google 地圖的文件庫。

Android 套件可讓您指定的共用程式庫是必要項目`<uses-library/>`項目。 如果*必要*程式庫並不存在於目標裝置上 (例如`//uses-library/@android:required`是*true*，預設值)，則套件安裝將會失敗並*安裝\_無法\_遺失\_共用\_文件庫*。

若要判斷需要哪些共用文件庫，檢視*產生*
**AndroidManifest.xml**檔案 (例如**obj\\偵錯\\android\\AndroidManifest.xml**)，並尋找`<uses-library/>`項目。 `<uses-library/>` 元素可在您的專案中手動新增**屬性\\AndroidManifest.xml**檔案並透過[UsesLibraryAttribute 自訂屬性](https://developer.xamarin.com/api/type/Android.App.UsesLibraryAttribute/)。

例如，將加入的組件參考*Mono.Android.GoogleMaps.dll*會隱含地將`<uses-library/>`Google 地圖的共用程式庫。



## <a name="installfailedupdateincompatible-when-installing-a-package"></a>安裝\_失敗\_更新\_不相容的安裝套件時

Android 套件有三個需求：

-   必須包含 '。 '（請參閱先前的項目）
-   它們必須有唯一字串的套件名稱 (因此看到 Android 應用程式名稱，例如 com.android.chrome Chrome 應用程式中的反向 tld 慣例)
-   當升級封裝，封裝必須有相同的簽署金鑰。

因此，假設：

1.  您建立及部署應用程式做為偵錯應用程式
2.  您將變更簽章的金鑰，例如為做為發行應用程式 （或因為您不喜歡預設提供偵錯的簽署金鑰）
3.  您安裝應用程式，而不先移除它，例如偵錯 > 啟動但不偵錯在 Visual Studio 中


封裝安裝在這種情況下，將會失敗並安裝\_失敗\_更新\_不相容的錯誤，因為封裝名稱未變更時的簽署金鑰未。 [Android 偵錯記錄檔](~/android/deploy-test/debugging/android-debug-log.md)也會包含類似的訊息：

```shell
E/PackageManager(  146): Package [PackageName] signatures do not match the previously installed version; ignoring!
```

若要修正這個錯誤，完全移除應用程式從您的裝置再重新安裝。


## <a name="installfaileduidchanged-when-installing-a-package"></a>安裝\_失敗\_UID\_安裝套件時變更

安裝的 Android 套件時，它指派*使用者識別碼*(UID)。
*有時*，對目前為未知的考量，已安裝的應用程式進行安裝時，安裝會失敗並`INSTALL_FAILED_UID_CHANGED`:

```shell
ERROR [2015-03-23 11:19:01Z]: ANDROID: Deployment failed
Mono.AndroidTools.InstallFailedException: Failure [INSTALL_FAILED_UID_CHANGED]
   at Mono.AndroidTools.Internal.AdbOutputParsing.CheckInstallSuccess(String output, String packageName)
   at Mono.AndroidTools.AndroidDevice.<>c__DisplayClass2c.<InstallPackage>b__2b(Task`1 t)
   at System.Threading.Tasks.ContinuationTaskFromResultTask`1.InnerInvoke()
   at System.Threading.Tasks.Task.Execute()
```

若要解決此問題，*完全解除安裝*Android 套件，藉由從 Android 目標的 GUI 安裝應用程式，或使用`adb`:

```shell
$ adb uninstall @PACKAGE_NAME@
```

**請勿使用** `adb uninstall -k`，如此便會如*保留*應用程式資料，並因此保留目標裝置上的衝突 UID。



## <a name="release-apps-fail-to-launch-on-device"></a>發行應用程式無法在裝置上啟動

沒有 Android 偵錯記錄檔輸出將會包含類似的訊息：

```shell
D/AndroidRuntime( 1710): Shutting down VM
W/dalvikvm( 1710): threadid=1: thread exiting with uncaught exception (group=0xb412f180)
E/AndroidRuntime( 1710): FATAL EXCEPTION: main
E/AndroidRuntime( 1710): java.lang.UnsatisfiedLinkError: Couldn't load monodroid: findLibrary returned null
E/AndroidRuntime( 1710):        at java.lang.Runtime.loadLibrary(Runtime.java:365)
```

如果是，有兩個可能的原因為：

1.  .apk 未提供目標裝置支援 ABI。
    例如，.apk 只包含 armeabi v7a 二進位檔，而且目標裝置僅支援 armeabi。

2.  [Android bug](http://code.google.com/p/android/issues/detail?id=21670)。 如果這種情況，解除安裝應用程式、 跨手指，然後重新安裝應用程式。

若要修正 (1)，編輯專案選項/屬性和[加入支援 ABIs 清單支援所需的 ABI](~/android/app-fundamentals/cpu-architectures.md)。 若要判斷您需要加入哪些 ABI，執行下列 adb 命令對您的目標裝置：

```shell
adb shell getprop ro.product.cpu.abi
adb shell getprop ro.product.cpu.abi2
```

輸出會包含主要 （和選用的次要） ABIs。

```shell
$ adb shell getprop | grep ro.product.cpu
[ro.product.cpu.abi2]: [armeabi]
[ro.product.cpu.abi]: [armeabi-v7a]
```

## <a name="the-outpath-property-is-not-set-for-project-ldquomyappcsprojrdquo"></a>OutPath 屬性未設定為專案&ldquo;MyApp.csproj&rdquo;

這通常表示您有 HP 電腦和環境變數&ldquo;平台&rdquo;的 MCD 等 HPD 設為項目。 這通常設定為 MSBuild 平台屬性與衝突&ldquo;任何 CPU&rdquo;或&ldquo;x86&rdquo;。 您必須從電腦移除這個環境變數，然後 MSBuild 才能運作：

-   控制項面板 > System > 進階 > 環境變數

重新啟動 Visual Studio 或 Visual Studio for Mac 並重新建立。 項目現在應該在如預期般運作。

## <a name="javalangclasscastexception-monoandroidruntimejavaobject-cannot-be-cast-to"></a>java.lang.ClassCastException: mono.android.runtime.JavaObject 無法轉換成...

Xamarin.Android 4.x 不會正確地封送處理巢狀泛型類型正確。 例如，請考慮下列 C\#使用程式碼[SimpleExpandableListAdapter](https://developer.xamarin.com/api/type/Android.Widget.SimpleExpandableListAdapter/):


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


問題在於，Xamarin.Android 不正確地封送處理巢狀泛型類型。 `List<IDictionary<string, object>>`要封送處理至[java.lang.ArrrayList](https://developer.xamarin.com/api/type/Java.Util.ArrayList/)，但`ArrayList`包含`mono.android.runtime.JavaObject`執行個體 (參考`Dictionary<string, object>`執行個體) 而不是實作的項目[java.util.Map](https://developer.xamarin.com/api/type/Java.Util.IMap/)，並產生下列例外狀況：

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

因應措施是使用提供[Java 集合型別](~/android/internals/api-design.md)而不是`System.Collections.Generic`類型&ldquo;內部&rdquo;型別。 當封送處理的執行個體，這樣會產生在適當的 Java 類型。 （下列程式碼會比以降低 gref 存留期需要更複雜。 它可以改變原始的程式碼，透過簡化`s/List/JavaList/g`和`s/Dictionary/JavaDictionary/g`如果 gref 存留期間不別擔心。)

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


## <a name="unexpected-nullreferenceexceptions"></a>未預期的 NullReferenceExceptions

偶爾[Android 偵錯記錄檔](~/android/deploy-test/debugging/android-debug-log.md)會提到 NullReferenceExceptions，&ldquo;不會發生&rdquo;或來自單聲道不久前的應用程式還 Android 的執行階段程式碼：

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

Android 的執行階段會決定中止程序，這可能會有許多原因，包括達到目標的 GREF 限制或執行時，這可能發生&ldquo;錯誤&rdquo;JNI 與。

如果是這種情況，請檢查 Android 的偵錯記錄檔訊息，以從程序類似於：

```shell
E/dalvikvm(  123): VM aborting
```


## <a name="abort-due-to-global-reference-exhaustion"></a>因為全域參考耗盡而中止

Android 的執行階段 JNI 層只支援有限的數目的時間是在任何給定時間點有效 JNI 物件參考。 當超過這個限制時，項目就會中斷。

GREF (*全域參考*) 限制為 2000年參考在模擬器中，並在硬體上的 ~ 52000 參考。

您知道您開始建立太多 GREFs，當您看到這類 Android 的偵錯記錄檔中的訊息：

```shell
D/dalvikvm(  602): GREF has increased to 1801
```

當您到達 GREF 限制時，會列印訊息，如下所示：

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


在上述範例中 (即順帶一提，來自於[bug 685215](https://bugzilla.novell.com/show_bug.cgi?id=685215)) 問題是，太多 Android.Graphics.Point 建立執行個體，請參閱 <<c4> [ 註解\#2](https://bugzilla.novell.com/show_bug.cgi?id=685215#c2)的修正程式清單這個特定的 bug。

有用的解決方案通常是以尋找哪種類型有太多的執行個體配置&ndash;在上述的傾印 Android.Graphics.Point&ndash;然後尋找 正在建立它們在原始程式碼和處置它們中適當地 (使其Java-object 存留期會縮短)。 這不一定適合 (\#685215 是多執行緒，因此簡單式方案可以避免 Dispose 呼叫)，但這是要考慮的第一個動作。

您可以啟用[GREF 記錄](~/android/troubleshooting/index.md)GREFs 建立時，以及多少存在。


## <a name="abort-due-to-jni-type-mismatch"></a>中止因為 JNI 類型不符

如果您手-向前復原 JNI 程式碼，很可能類型不符合正確，例如： 如果您嘗試叫用`java.lang.Runnable.run`未實作的型別上`java.lang.Runnable`。 當發生這種情況時，會有訊息如下所示 Android 的偵錯記錄檔中：

```shell
W/dalvikvm( 123): JNI WARNING: can't call Ljava/Type;;.method on instance of Lanother/java/Type;
W/dalvikvm( 123):              in Lmono/java/lang/RunnableImplementor;.n_run:()V (CallVoidMethodA)
...
E/dalvikvm( 123): VM aborting
```

## <a name="dynamic-code-support"></a>動態程式碼支援

### <a name="dynamic-code-does-not-compile"></a>動態程式碼無法編譯

若要使用 C\#動態應用程式或程式庫中，您必須將加入 system.core.dll 的參考、 Microsoft.CSharp.dll 和 Mono.CSharp.dll 您的專案。

### <a name="in-release-build-missingmethodexception-occurs-for-dynamic-code-at-run-time"></a>在發行組建 MissingMethodException 發生於動態程式碼在執行階段。

-   很可能您的應用程式專案，並沒有 system.core.dll 的參考、 Microsoft.CSharp.dll 或 Mono.CSharp.dll 參考。 請確定這些組件參考。

    -   請注意該動態程式碼永遠成本。 如果您需要有效率的程式碼，請考慮不使用動態程式碼。

-   在第一次預覽中，除非應用程式程式碼明確用每個組件中的類型排除這些組件。 請參閱下列的因應措施： [http://lists.ximian.com/pipermail/mo...il/009798.html](http://lists.ximian.com/pipermail/monodroid/2012-April/009798.html)


## <a name="projects-built-with-aotllvm-crash-on-x86-devices"></a>在 x86 上與 AOT + LLVM 損毀所建置的專案的裝置

部署與建置的應用程式時[AOT + LLVM](~/android/deploy-test/release-prep/index.md) x86 型裝置上，您可能會看到類似下列的例外狀況錯誤訊息：

```shell
Assertion: should not be reached at /Users/.../external/mono/mono/mini/tramp-x86.c:124
Fatal signal 6 (SIGABRT), code -6 in tid 4051 (amarin.bug56111)
```

這是已知的問題報告中[56111](https://bugzilla.xamarin.com/show_bug.cgi?id=56111)。 因應措施是停用 LLVM。
