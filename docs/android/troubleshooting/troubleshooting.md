---
title: 疑難排解秘訣
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 56137ACA-4811-B312-6860-E16D0FA123F7
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/15/2018
ms.openlocfilehash: 6d83afa47c459633506736b2497a82c444352c90
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75488929"
---
# <a name="troubleshooting-tips"></a>疑難排解秘訣

## <a name="getting-diagnostic-information"></a>取得診斷資訊

在追蹤各種 bug 時，Xamarin 有幾個地方需要尋找。
它們包括：

1. 診斷 MSBuild 輸出。
2. 裝置部署記錄。
3. Android Debug 記錄輸出。

<a name="Diagnostic_MSBuild_Output" />

## <a name="diagnostic-msbuild-output"></a>診斷 MSBuild 輸出

診斷 MSBuild 可以包含與封裝建立相關的其他資訊，而且可能包含一些封裝部署資訊。

若要在 Visual Studio 內啟用診斷 MSBuild 輸出：

1. 按一下 [**工具] > 選項 ...**
2. 在左側樹狀檢視中，選取 [**專案和方案] > [建立並執行**]
3. 在右側面板中，將 [MSBuild 組建輸出詳細資訊] 下拉式清單設定為 [診斷]
4. 按一下 [確定]
5. 清除並重建您的套件。
6. 診斷輸出會顯示在 [輸出] 面板中。

若要在 Visual Studio for Mac/OS X 內啟用診斷 MSBuild 輸出：

1. 按一下 [ **Visual Studio for Mac > 喜好**設定 ...]
2. 在左側樹狀檢視中，選取 [專案] [ **> 組建**]
3. 在右側面板中，將 [記錄詳細資訊] 下拉式按鈕設定為 [診斷]
4. 按一下 [確定]
5. 重新啟動 Visual Studio for Mac
6. 清除並重建您的套件。
7. 按一下 [組建輸出] 按鈕，即可在 [錯誤] 面板中看到診斷輸出（**View > pad > 錯誤**）。

## <a name="device-deployment-logs"></a>裝置部署記錄檔

若要在 Visual Studio 內啟用裝置部署記錄：

1. **工具 > 選項 ...** >
2. 在左側樹狀檢視中，選取 [ **Xamarin > Android 設定**]
3. 在右側面板中，啟用 [X] 延伸模組的 [**偵錯工具記錄（將 monodroid 寫入您的桌面）** ] 核取方塊。
4. 記錄檔訊息會寫入您桌面上的 monodroid 檔案。

Visual Studio for Mac 一律會寫入裝置部署記錄。 尋找它們有點棘手，每隔一天會建立一個*AndroidUtils*記錄檔，一次部署發生的時間，例如： **AndroidTools-2012-10-24 _12-35-45 .log**。

- 在 Windows 上，記錄檔會寫入 `%LOCALAPPDATA%\XamarinStudio-{VERSION}\Logs`。
- 在 OS X 上，記錄檔會寫入 `$HOME/Library/Logs/XamarinStudio-{VERSION}`。

## <a name="android-debug-log-output"></a>Android Debug 記錄輸出

Android 會將許多訊息寫入[Android Debug 記錄](~/android/deploy-test/debugging/android-debug-log.md)檔。
Xamarin 會使用 Android 系統屬性來控制對 Android Debug 記錄檔的額外訊息產生。 Android 系統屬性可以透過[Android Debug Bridge （adb）](https://developer.android.com/guide/developing/tools/adb.html)內的*setprop*命令來設定：

```shell
adb shell setprop PROPERTY_NAME PROPERTY_VALUE
```

系統屬性會在進程啟動期間讀取，因此必須在啟動應用程式之前設定，或者必須在變更系統屬性之後重新開機應用程式。

### <a name="xamarinandroid-system-properties"></a>Xamarin.Android 系統屬性

Xamarin 支援下列系統屬性：

- *debug*：如果是非空白字串，這就相當於 `*mono-debug*`。

- 在初始化 mono*之前*，會在應用程式啟動期間，以管線分隔（' *|* '）要匯出的環境變數*清單。* 這可讓您設定控制 mono 記錄的環境變數。

  > [!NOTE]
  > 因為值是以 ' *|* ' 分隔，所以此值必須有額外的引號，因為 \`*adb shell*\` 命令會移除一組引號。

  > [!NOTE]
  > Android 系統屬性值的長度不能超過92個字元。

  範例：

  ```
  adb shell setprop debug.mono.env "'MONO_LOG_LEVEL=info|MONO_LOG_MASK=asm'"
  ```

- *debug .log*：應該將其他訊息列印至 Android 偵錯工具記錄檔的元件清單（以逗號分隔（' *，* '））。 根據預設，不會設定任何內容。 元件包括：

  - *全部*：列印所有訊息
  - *gc*：列印與 gc 相關的訊息。
  - *grf*： Print （弱式、全域）參考配置和解除配置訊息。
  - *lref*：列印本機參考配置和解除配置訊息。

  > [!NOTE]
  > 這些是*非常*詳細的資訊。 除非您真的需要，否則請勿啟用。

- *debug. trace*：允許設定[mono--追蹤](http://docs.go-mono.com/?link=man%3amono(1))`=PROPERTY_VALUE` 設定。

## <a name="deleting-bin-and-obj"></a>刪除 `bin` 和 `obj`

在過去的情況下，會發生下列情況：

- 您遇到奇怪的組建或執行階段錯誤。
- 您 `Clean`、`Rebuild`，或手動刪除您的 `bin` 和 `obj` 目錄。
- 問題消失了。

我們致力於修正這些問題，因為它們會影響開發人員的生產力。

如果發生如下的問題：

1. 請注意。 您的專案進入此狀態的最後一個動作是什麼？
1. 儲存您目前的組建記錄檔。 再次嘗試建立，並記錄[診斷組建記錄](#diagnostic-msbuild-output)檔。
1. 提交[錯誤報表][bug]。

在刪除您的 `bin` 和 `obj` 目錄之前，請將其壓縮並加以儲存，以供稍後進行診斷（如有需要）。 您可能只需要 `Clean` 您的 Xamarin. Android 應用程式專案，就能再次運作。

[bug]: https://github.com/xamarin/xamarin-android/wiki/Submitting-Bugs,-Feature-Requests,-and-Pull-Requests

## <a name="xamarinandroid-cannot-resolve-systemvaluetuple"></a>Xamarin 無法解析 System.valuetuple

此錯誤是因為與 Visual Studio 不相容所造成。

- **Visual Studio 2017 Update 1** （15.1 版或更舊版本）只與**system.valuetuple NuGet 4.3.0** （或更舊版本）相容。

- **Visual Studio 2017 Update 2** （15.2 版或更新版本）僅與**system.valuetuple NuGet 4.3.1** （或更新版本）相容。

請選擇與您的 Visual Studio 2017 安裝對應的正確 System.valuetuple NuGet。

## <a name="gc-messages"></a>GC 訊息

將 [debug .log 系統] 屬性設定為包含 gc 的值，即可查看 GC 元件訊息。

每當 GC 執行時，系統就會產生 GC 訊息，並提供 GC 所執行工作的相關資訊：

```shell
I/monodroid-gc(12331): GC cleanup summary: 81 objects tested - resurrecting 21.
```

您可以將 `MONO_LOG_LEVEL` 環境變數設定為 `debug`，以產生其他 GC 資訊（例如計時資訊）：

```shell
adb shell setprop debug.mono.env MONO_LOG_LEVEL=debug
```

這會導致（許多）額外的 Mono 訊息，包括下列三種結果：

```shell
D/Mono (15723): GC_BRIDGE num-objects 1 num_hash_entries 81226 sccs size 81223 init 0.00ms df1 285.36ms sort 38.56ms dfs2 50.04ms setup-cb 9.95ms free-data 106.54ms user-cb 20.12ms clenanup 0.05ms links 5523436/5523436/5523096/1 dfs passes 1104 6883/11046605
D/Mono (15723): GC_MINOR: (Nursery full) pause 2.01ms, total 287.45ms, bridge 225.60 promoted 0K major 325184K los 1816K
D/Mono ( 2073): GC_MAJOR: (user request) pause 2.17ms, total 2.47ms, bridge 28.77 major 576K/576K los 0K/16K
```

在 `GC_BRIDGE` 訊息中，`num-objects` 是此傳遞所考慮的橋接器物件數目，而 `num_hash_entries` 是在此橋接器程式碼調用期間處理的物件數目。

在 `GC_MINOR` 和 `GC_MAJOR` 訊息中，`total` 是在世界暫停的時間量（不會執行任何執行緒），而 `bridge` 是橋接器處理常式代碼（處理 JAVA VM）所花費的時間量。 在進行橋接器處理時，世界*不*會暫停。

 *一般來說*，`num_hash_entries`的值愈大，`bridge` 的集合將會花費更多時間，而花費在收集的 `total` 時間就愈大。

## <a name="global-reference-messages"></a>全域參考訊息

若要啟用全域參考 loggig （GRF）記錄， *debug .log*系統屬性必須包含*grf*，例如：

```shell
adb shell setprop debug.mono.log gref
```

Xamarin 會使用 Android 全域參考來提供 JAVA 實例和相關聯的受控實例之間的對應，如同叫用 JAVA 方法時，JAVA 實例必須提供給 JAVA。

可惜的是，Android 模擬器一次只允許2000個全域參考存在。 硬體具有更高的52000全域參照限制。 在模擬器上執行應用程式時，較低的限制可能會造成問題，*因此知道實例的來源*可能非常有用。

> [!NOTE]
> 全域參考計數是 Xamarin 的內部，而且不會（也不能）包含載入至進程的其他原生程式庫所採取的全域參考。 使用全域參考計數做為估計值。

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

有四個結果訊息：

- 全域參考建立：這些是開頭為 *+ g +* 的行，並會提供建立程式碼路徑的堆疊追蹤。
- 全域參考終結：這些是開頭為 *-g-* 的行，而且可能會針對全域參考的程式碼路徑處置提供堆疊追蹤。 如果 GC 正在處置 grf，則不會提供任何堆疊追蹤。
- 弱式全域參考建立：這些是以 *+ w +* 開頭的行。
- 弱式全域參考銷毀：這些是以 *-w-* 開頭的行。

在所有訊息中， *grefc*值是 xamarin 所建立的全域參考計數，而*Grefwc*值是 xamarin 所建立的弱式全域參考計數。 *控制碼*或*obj 控制碼*值是 JNI handle 值，而 ' */* ' 後面的字元是控制碼值的類型： */l*代表本機參考， */g*代表全域參考，而弱式全域參考則為 */w* 。

做為 GC 程式的一部分，全域參考（+ g +）會轉換成弱式全域參考（導致 + w + 和-g-），並會開始 JAVA 端 GC，然後檢查弱式全域參考以查看是否已收集。 如果它仍在運作中，就會以弱式 ref （+ g +，-w-）來建立新的 grf，否則弱式 ref 會被終結（-w）。

## <a name="java-instance-is-created-and-wrapped-by-a-mcw"></a>JAVA 實例是由 MCW 所建立和包裝的

```shell
I/monodroid-gref(27679): +g+ grefc 2211 gwrefc 0 obj-handle 0x4066df10/L -> new-handle 0x4066df10/L from ...
I/monodroid-gref(27679): handle 0x4066df10; key_handle 0x4066df10: Java Type: `android/graphics/drawable/TransitionDrawable`; MCW type: `Android.Graphics.Drawables.TransitionDrawable`
```

## <a name="a-gc-is-being-performed"></a>正在執行 GC 。

```shell
I/monodroid-gref(27679): +w+ grefc 1953 gwrefc 259 obj-handle 0x4066df10/G -> new-handle 0xde68f95f/W from take_weak_global_ref_jni
I/monodroid-gref(27679): -g- grefc 1952 gwrefc 259 handle 0x4066df10/G from take_weak_global_ref_jni
```

## <a name="object-is-still-alive-as-handle--null"></a>物件仍在作用中，如 handle！ = null
## <a name="wref-turned-back-into-a-gref"></a>wref 已轉換回 grf

```shell
I/monodroid-gref(27679): *try_take_global obj=0x4976f080 -> wref=0xde68f95f handle=0x4066df10
I/monodroid-gref(27679): +g+ grefc 1930 gwrefc 39 obj-handle 0xde68f95f/W -> new-handle 0x4066df10/G from take_global_ref_jni
I/monodroid-gref(27679): -w- grefc 1930 gwrefc 38 handle 0xde68f95f/W from take_global_ref_jni
```

## <a name="object-is-dead-as-handle--null"></a>物件無效，控制碼 = = null
## <a name="wref-is-freed-no-new-gref-created"></a>已釋放 wref，未建立任何新 grf

```shell
I/monodroid-gref(27679): *try_take_global obj=0x4976f080 -> wref=0xde68f95f handle=0x0
I/monodroid-gref(27679): -w- grefc 1914 gwrefc 296 handle 0xde68f95f/W from take_global_ref_jni
```

這裡有一個「有趣」的功能：在4.0 之前執行 Android 的目標上，grf 值等於 Android 執行時間記憶體中 JAVA 物件的位址。 （也就是說，GC 是一個非移動、保守、收集器，它會直接參考這些物件）。因此，在 + g +、+ w +、-g-、+ g +、-w 序列之後，產生的 grf 將會具有與原始 grf 值相同的值。 這讓 grepping 的記錄非常簡單。

不過，android 4.0 有移動的收集器，且不再直接參考 Android 執行時間 VM 物件。 因此，在 + g +、+ w +、-g-、+ g +、-w 序列之後，grf 值將會*不同*。 如果物件不受多個 Gc，則會有數個 grf 值，因此難以判斷實例實際配置來源的位置。

### <a name="querying-programmatically"></a>以程式設計方式查詢

您可以藉由查詢 `JniRuntime` 物件來查詢 GRF 和 WREF 計數。

`Java.Interop.JniRuntime.CurrentRuntime.GlobalReferenceCount`-全域參考計數

`Java.Interop.JniRuntime.CurrentRuntime.WeakGlobalReferenceCount`-弱式參考計數

## <a name="android-debug-logs"></a>Android Debug 記錄檔

[Android Debug 記錄](~/android/deploy-test/debugging/android-debug-log.md)檔可能會提供有關您所看到的任何執行階段錯誤的其他內容。

## <a name="floating-point-performance-is-terrible"></a>浮點效能變得很可怕！

或者，「我的應用程式使用 Debug 組建比發行組建更快執行10倍！」

Xamarin 支援多個裝置 Abi： *armeabi*、 *armeabi armeabi-v7a*和*x86*。 可以在專案屬性中指定裝置 Abi， **> 應用程式**索引標籤 > 支援的架構。

「偵錯工具組建」使用提供所有 Abi 的 Android 套件，因此會針對目標裝置使用最快速的 ABI。

發行組建只會包含在 [專案屬性] 索引標籤中選取的 Abi。可以選取一個以上的。

*armeabi*是預設 ABI，具有最廣泛的裝置支援。 *不過*，armeabi 不支援多 CPU 裝置和硬體浮點，amont 其他專案。 因此，使用 armeabi 版本執行時間的應用程式將會系結至單一核心，並會使用軟浮點執行。 這兩種情況都會導致應用程式的效能明顯降低。

如果您的應用程式需要相當可觀的浮點效能（例如遊戲），您應該啟用*armeabi-Armeabi-v7a* ABI。 您可能只想要支援*armeabi armeabi-v7a*執行時間，這表示只支援*armeabi*的舊版裝置將無法執行您的應用程式。

## <a name="could-not-locate-android-sdk"></a>找不到 Android SDK

適用于 Windows 的 Android SDK 可從 Google 取得2個下載。
如果您選擇 .exe 安裝程式，它將會寫入登錄機碼，告訴 Xamarin Android 安裝所在的位置。 如果您自行選擇 .zip 檔案並將它解壓縮，則 Xamarin 不知道要在哪裡尋找 SDK。 您可以前往 [工具] [ **> 選項] > [xamarin > Android 設定**]，告訴您 SDK 所在的 Visual Studio：

[在 Xamarin Android 設定中 ![Android SDK 位置](troubleshooting-images/01.png)](troubleshooting-images/01.png#lightbox)

## <a name="ide-does-not-display-target-device"></a>IDE 不會顯示目標裝置

有時候您會嘗試將應用程式部署至裝置，但您想要部署的裝置不會顯示在 [選取裝置] 對話方塊中。 當 Android Debug Bridge 決定休假時，就可能發生這種情況。

若要診斷此問題，請尋找[adb 程式](~/android/deploy-test/debugging/android-debug-log.md)，然後執行：

```shell
adb devices
```

如果您的裝置不存在，則需要重新開機 Android Debug Bridge 伺服器，才能找到您的裝置：

```shell
adb kill-server
adb start-server
```

HTC 同步處理軟體可能會阻止**adb 的啟動伺服器**正常運作。 如果**adb start-server**命令未列印出其啟動的埠，請結束 HTC 同步處理軟體，然後嘗試重新開機 adb 伺服器。

## <a name="the-specified-task-executable-keytool-could-not-be-run"></a>無法執行指定的工作可執行檔 "keytool"

這表示您的路徑不包含 JAVA SDK 的 bin 目錄所在的目錄。 請確認您已遵循[安裝](~/android/get-started/installation/index.md)指南中的步驟進行。

## <a name="monodroidexe-or-aresgenexe-exited-with-code-1"></a>monodroid 或 aresgen 已結束，代碼為1

為協助您處理這個問題，請移至 Visual Studio 並變更 MSBuild 詳細資訊層級，若要這樣做，請選取 [**工具] > 選項 > [專案**和**方案] > [建立**並**執行 > MSBuild 專案組建輸出詳細**資訊]，並將此值設定為 [**正常**]。

重建，並檢查 Visual Studio 的 [輸出] 窗格，其中應包含完整的錯誤。

## <a name="there-is-not-enough-storage-space-on-the-device-to-deploy-the-package"></a>裝置上的儲存空間不足，無法部署套件

當您未從 Visual Studio 中啟動模擬器時，就會發生這種情況。 在 Visual Studio 外啟動模擬器時，您必須傳遞 `-partition-size 512` 選項，例如

```shell
emulator -partition-size 512 -avd MonoDroid
```

請確定您使用的是正確的模擬器名稱，亦即您在設定模擬器[時使用的名稱](~/android/get-started/installation/windows.md#device)。

## <a name="install_failed_invalid_apk-when-installing-a-package"></a>安裝套件時，安裝\_失敗\_不正確\_APK

Android 套件名稱*必須*包含句點（' *.* '）。 編輯您的套件名稱，使其包含句點。

- 在 Visual Studio 內：
  - 以滑鼠右鍵按一下您的專案 > 屬性
  - 按一下左側的 [Android 資訊清單] 索引標籤。
  - 更新 [封裝名稱] 欄位。
    - 如果您看到訊息 &ldquo;找不到 Androidmanifest.xml。 按一下以加入一個。&rdquo;中，按一下連結，然後更新 [套件名稱] 欄位。
- 在 Visual Studio for Mac 內：
  - 以滑鼠右鍵按一下您的專案，> 選項。
  - 流覽至 [Build/Android 應用程式] 區段。
  - 將 [封裝名稱] 欄位變更為包含 '. '。

## <a name="install_failed_missing_shared_library-when-installing-a-package"></a>安裝套件時，\_失敗\_遺失\_共用\_程式庫

此內容中的「共用程式庫」*不*是原生共用程式庫（*libfoo.so*）檔案;而是必須分別安裝在目標裝置上的程式庫，例如 Google Maps。

Android 套件會指定 `<uses-library/>` 元素所需的共用程式庫。 如果目標裝置上沒有*必要*的程式庫（例如 `//uses-library/@android:required` 為*true*（預設值），則套件安裝將會失敗，且*安裝\_失敗\_遺失\_共用\_程式庫*。

若要判斷需要哪些共用程式庫，請查看*產生*的
**androidmanifest.xml .xml**檔案（例如**obj\\Debug\\android\\androidmanifest.xml**），然後尋找 `<uses-library/>` 元素。 您可以透過[UsesLibraryAttribute 自訂屬性](xref:Android.App.UsesLibraryAttribute)，在專案的屬性中手動新增 `<uses-library/>` 元素 **\\androidmanifest.xml .xml**檔案和。

例如，將元件參考加入至*GoogleMaps* ，會以隱含方式新增 Google Maps 共用程式庫的 `<uses-library/>`。

## <a name="install_failed_update_incompatible-when-installing-a-package"></a>安裝\_失敗\_更新\_在安裝套件時不相容

Android 套件有三個需求：

- 它們必須包含 '. '（請參閱上一個專案）
- 它們必須有唯一的字串套件名稱（因此，在 Android 應用程式名稱中看到的反向 tld 慣例，例如適用于 Chrome 應用程式的 .com. chrome）
- 升級封裝時，封裝必須具有相同的簽署金鑰。

因此，請想像一下這種情況：

1. 您建立 & 將應用程式部署為 Debug 應用程式
2. 您可以變更簽署金鑰，例如做為發行應用程式使用（或因為您不喜歡預設提供的 Debug 簽署金鑰）
3. 您會在不先移除應用程式的情況下進行安裝，例如 Debug > 啟動而不在 Visual Studio 內進行調試

發生這種情況時，套件安裝將會失敗，且安裝\_失敗\_更新\_不相容錯誤，因為在簽署金鑰執行時，套件名稱並未變更。 [Android Debug 記錄](~/android/deploy-test/debugging/android-debug-log.md)檔也會包含類似以下的訊息：

```shell
E/PackageManager(  146): Package [PackageName] signatures do not match the previously installed version; ignoring!
```

若要修正此錯誤，請在重新安裝之前，從您的裝置完全移除應用程式。

## <a name="install_failed_uid_changed-when-installing-a-package"></a>安裝套件時\_失敗\_UID\_變更

安裝 Android 套件時，系統會將*使用者識別碼*（UID）指派給它。
*有時候*，目前不明的原因是，當您在已安裝的應用程式上安裝時，安裝將會失敗並出現 `INSTALL_FAILED_UID_CHANGED`：

```shell
ERROR [2015-03-23 11:19:01Z]: ANDROID: Deployment failed
Mono.AndroidTools.InstallFailedException: Failure [INSTALL_FAILED_UID_CHANGED]
   at Mono.AndroidTools.Internal.AdbOutputParsing.CheckInstallSuccess(String output, String packageName)
   at Mono.AndroidTools.AndroidDevice.<>c__DisplayClass2c.<InstallPackage>b__2b(Task`1 t)
   at System.Threading.Tasks.ContinuationTaskFromResultTask`1.InnerInvoke()
   at System.Threading.Tasks.Task.Execute()
```

若要解決此問題，請從 Android 目標的 GUI 安裝應用程式，或使用 `adb`，以*完整卸載*android 套件：

```shell
$ adb uninstall @PACKAGE_NAME@
```

**請勿使用**`adb uninstall -k`，因為這會*保留*應用程式資料，因此會在目標裝置上保留衝突的 UID。

## <a name="release-apps-fail-to-launch-on-device"></a>發行應用程式無法在裝置上啟動

Android Debug 記錄輸出會包含類似以下的訊息：

```shell
D/AndroidRuntime( 1710): Shutting down VM
W/dalvikvm( 1710): threadid=1: thread exiting with uncaught exception (group=0xb412f180)
E/AndroidRuntime( 1710): FATAL EXCEPTION: main
E/AndroidRuntime( 1710): java.lang.UnsatisfiedLinkError: Couldn't load monodroid: findLibrary returned null
E/AndroidRuntime( 1710):        at java.lang.Runtime.loadLibrary(Runtime.java:365)
```

若是如此，可能會有兩個原因：

1. Apk 不會提供目標裝置支援的 ABI。
    例如，apk 只包含 armeabi armeabi-v7a 二進位檔，而目標裝置僅支援 armeabi。

2. [Android bug](https://code.google.com/p/android/issues/detail?id=21670)。 如果是這種情況，請將應用程式卸載，並在您的手指上進行，然後重新安裝應用程式。

若要修正（1），請編輯專案選項/屬性，並[將所需 ABI 的支援新增至支援的 abi 清單](~/android/app-fundamentals/cpu-architectures.md)。 若要判斷您需要新增的 ABI，請針對您的目標裝置執行下列 adb 命令：

```shell
adb shell getprop ro.product.cpu.abi
adb shell getprop ro.product.cpu.abi2
```

輸出將包含主要（和選擇性的次要） Abi。

```shell
$ adb shell getprop | grep ro.product.cpu
[ro.product.cpu.abi2]: [armeabi]
[ro.product.cpu.abi]: [armeabi-v7a]
```

## <a name="the-outpath-property-is-not-set-for-project-ldquomyappcsprojrdquo"></a>未針對 project &ldquo;MyApp .csproj&rdquo; 設定 OutPath 屬性

這通常表示您有一部 HP 電腦，而環境變數 &ldquo;Platform&rdquo; 已設定為 MCD 或 HPD 之類的專案。 這與通常設定為 &ldquo;任何 CPU&rdquo; 或 &ldquo;x86&rdquo;的 MSBuild 平臺屬性衝突。 您必須先從電腦移除此環境變數，MSBuild 才能運作：

- 控制台 > 系統 > Advanced > 環境變數

重新開機 Visual Studio 或 Visual Studio for Mac 並嘗試重建。 專案現在應該會如預期般運作。

## <a name="javalangclasscastexception-monoandroidruntimejavaobject-cannot-be-cast-to"></a>發生 classcastexception： mono. JAVAObject 無法轉換為 .....。

Xamarin 5.x 未正確地正確封送處理已嵌套的泛型型別。 例如，請考慮下列 C\# 使用[SimpleExpandableListAdapter](xref:Android.Widget.SimpleExpandableListAdapter)的程式碼：

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

問題在於，Xamarin 不正確地封送處理嵌套的泛型型別。 `List<IDictionary<string, object>>` 會封送處理至 [java.lang.ArrrayList](xref:Java.Util.ArrayList), 但`ArrayList`包含`mono.android.runtime.JavaObject`實例 (參考`Dictionary<string, object>`實例), 而不是執行 [java.util.Map](xref:Java.Util.IMap)的物件。 產生下列例外狀況:

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

因應措施是使用所提供的[JAVA集合類型](~/android/internals/api-design.md),而不是`System.Collections.Generic`內部&ldquo;類型的類型。 這會在封送處理實例時，產生適當的 JAVA 類型。 （下列程式碼比所需更複雜，以便減少 grf 的存留期。 您可以透過 `s/List/JavaList/g` 來簡化原始程式碼的變更，並在不擔心 grf 存留期時 `s/Dictionary/JavaDictionary/g`。）

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

[未來的版本將會修正此](https://bugzilla.xamarin.com/show_bug.cgi?id=5401)問題。

## <a name="unexpected-nullreferenceexceptions"></a>非預期的 NullReferenceExceptions

[Android Debug 記錄](~/android/deploy-test/debugging/android-debug-log.md)檔偶爾會提及 &ldquo;無法發生的 NullReferenceExceptions，&rdquo; 或在應用程式無法執行之前，從 Mono for Android 執行時間程式碼開始：

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

當 Android 執行時間決定中止進程時，可能會發生這種情況，包括達到目標的 GRF 限制，或使用 JNI &ldquo;錯誤的&rdquo; 執行某些動作。

若要查看是否為這種情況，請檢查 Android Debug 記錄檔，以取得您進程中的訊息，如下所示：

```shell
E/dalvikvm(  123): VM aborting
```

## <a name="abort-due-to-global-reference-exhaustion"></a>因為全域參考耗盡而中止

Android 執行時間的 JNI 層僅支援有限數目的 JNI 物件參考，在任何指定的時間點都是有效的。 超過此限制時，就會發生中斷的情況。

GRF （*全域參考*）限制為模擬器中的2000參考，而硬體上的 ~ 52000 參考。

當您在 Android Debug 記錄檔中看到如下訊息時，您就會開始建立太多 GREFs：

```shell
D/dalvikvm(  602): GREF has increased to 1801
```

當您達到 GRF 限制時，會列印如下的訊息：

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

在上述範例中（也就是來自[bug 685215](https://bugzilla.novell.com/show_bug.cgi?id=685215)），問題是有太多的 Android. 圖形。正在建立點實例;如需此特定 bug 的修正清單，請參閱[批註 \#2](https://bugzilla.novell.com/show_bug.cgi?id=685215#c2) 。

一般來說，有用的解決方案是找出哪些類型有太多配置 &ndash; Android 的實例。在上述傾印 &ndash; 中指向，然後找出它們在原始程式碼中的建立位置，並適當地處置它們（以縮短其 JAVA 物件存留期）。 這不一定適合（\#685215 是多執行緒，因此，簡單的解決方案會避免處置呼叫），但這是要考慮的第一件事。

您可以啟用[Grf 記錄](~/android/troubleshooting/index.md)，以查看 GREFs 的建立時間和存在的數目。

## <a name="abort-due-to-jni-type-mismatch"></a>因 JNI 類型不符而中止

如果您手動變換 JNI 程式碼，類型可能會無法正確地比對，例如，如果您嘗試在未執行 `java.lang.Runnable`的類型上叫用 `java.lang.Runnable.run`。 發生這種情況時，Android Debug 記錄檔中將會出現類似下面的訊息：

```shell
W/dalvikvm( 123): JNI WARNING: can't call Ljava/Type;;.method on instance of Lanother/java/Type;
W/dalvikvm( 123):              in Lmono/java/lang/RunnableImplementor;.n_run:()V (CallVoidMethodA)
...
E/dalvikvm( 123): VM aborting
```

## <a name="dynamic-code-support"></a>動態程式碼支援

### <a name="dynamic-code-does-not-compile"></a>動態程式碼不會編譯

若要在您的應用程式或程式庫中使用 C\# 動態，您必須將 System.object 新增至您的專案。

### <a name="in-release-build-missingmethodexception-occurs-for-dynamic-code-at-run-time"></a>在發行組建中，會在執行時間針對動態程式碼進行 MissingMethodException。

- 您的應用程式專案很可能不會參考到 System.object、Microsoft CSharp .dll 或 Mono. CSharp .dll。 請確定已參考這些元件。

  - 請記住，動態程式碼一律會花費成本。 如果您需要有效率的程式碼，請考慮不要使用動態程式碼。

- 在第一個預覽版本中，除非應用程式代碼明確使用每個元件中的類型，否則會排除這些元件。 如需因應措施，請參閱下列各項： [http://lists.ximian.com/pipermail/mo...il/009798.html](http://lists.ximian.com/pipermail/monodroid/2012-April/009798.html)

## <a name="projects-built-with-aotllvm-crash-on-x86-devices"></a>在 x86 裝置上使用 AOT 和 LLVM 損毀建立的專案

在 x86 裝置上部署以[AOT + LLVM](~/android/deploy-test/release-prep/index.md)建立的應用程式時，您可能會看到類似下面的例外狀況錯誤訊息：

```shell
Assertion: should not be reached at /Users/.../external/mono/mono/mini/tramp-x86.c:124
Fatal signal 6 (SIGABRT), code -6 in tid 4051 (Xamarin.bug56111)
```

這是已知的問題–因應措施是停用 LLVM。
