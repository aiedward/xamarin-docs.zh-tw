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
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "79303915"
---
# <a name="troubleshooting-tips"></a>疑難排解秘訣

## <a name="getting-diagnostic-information"></a>取得診斷資訊

Xamarin.Android 在跟蹤各種錯誤時有幾個地方可看。
其中包括：

1. 診斷MSBuild輸出。
2. 設備部署日誌。
3. 安卓調試日誌輸出。

<a name="Diagnostic_MSBuild_Output" />

## <a name="diagnostic-msbuild-output"></a>診斷 MSBuild 輸出

診斷 MSBuild 可以包含與包構建相關的其他資訊,並可能包含一些包部署資訊。

若要在 Visual Studio 內啟用診斷 MSBuild 輸出：

1. 點選**工具>選項...**
2. 在左方樹檢視中,選擇 **「專案和解決方案>產生與執行**
3. 在右邊面板中,將 MSBuild 產生輸出詳細度下拉至診斷
4. 按一下 [確定]****。
5. 清除並重建您的套件。
6. 診斷輸出在「輸出」面板中可見。

要在 Visual Studio 內為 Mac/OS X 啟用診斷 MSBuild 輸出,請選擇:

1. 按下 **"視覺工作室"查看 Mac >首选项...**
2. 在左邊樹檢視中,選擇 **「產生>專案**
3. 在右側面板中,將日誌詳細度下拉設置為"診斷"
4. 按一下 [確定]****。
5. 重新啟動 Visual Studio for Mac
6. 清除並重建您的套件。
7. 通過按下「生成輸出」按鈕,診斷輸出在「錯誤鍵盤」(**查看>板>錯誤**)中可見。

## <a name="device-deployment-logs"></a>裝置部署記錄

要在可視化工作室中啟用設備部署日誌記錄,我們:

1. **工具>選項...**>
2. 在左側樹檢視中,選擇**Xamarin > Android 設定**
3. 在右側面板中,啟用 [X]**擴展調試日誌記錄(將單體.log 寫入桌面)** 複選框。
4. 記錄訊息將寫入桌面上的單體.log 檔。

Mac 的可視化工作室始終寫入設備部署日誌。 把它們加在一起稍微困難一點;*AndroidUtils*日誌檔是為每天和部署發生的時間創建的,例如 **:AndroidTools-2012-10-24_12-35-45.log**。

- 在 Windows 上,紀錄檔寫`%LOCALAPPDATA%\XamarinStudio-{VERSION}\Logs`入 。
- 在 OS X 上,`$HOME/Library/Logs/XamarinStudio-{VERSION}`紀錄檔寫入 。

## <a name="android-debug-log-output"></a>安卓調試日誌輸出

Android將寫許多訊息到[Android 除錯紀錄](~/android/deploy-test/debugging/android-debug-log.md)。
Xamarin.Android 使用 Android 系統屬性來控制向 Android 調試日誌生成其他消息。 Android 系統屬性可以通過[Android 除錯橋 (adb)](https://developer.android.com/guide/developing/tools/adb.html)中的*setprop*命令進行設定:

```shell
adb shell setprop PROPERTY_NAME PROPERTY_VALUE
```

在程序啟動期間讀取系統屬性,因此必須在啟動應用程式之前設置,或者必須在系統屬性更改後重新啟動應用程式。

### <a name="xamarinandroid-system-properties"></a>Xamarin.Android 系統屬性

Xamarin.Android 支援以下系統屬性:

- *debug.mono.debug:* 如果不是空字串,這相當於`*mono-debug*`。

- *debug.mono.env*: 在應用程式*|* 啟動期間 ,在單聲道初始化*之前*,要匯出的管道分離 ('') 環境變數清單。 這允許設置控制單聲道日誌記錄的環境變數。

  > [!NOTE]
  > *|* 由於該值為『分隔,該值必須具有額外的報價級別,因為\`adb *shell*\`命令將刪除一組引號。

  > [!NOTE]
  > Android 系統屬性值的長度不能超過92個字元。

  範例：

  ```
  adb shell setprop debug.mono.env "'MONO_LOG_LEVEL=info|MONO_LOG_MASK=asm'"
  ```

- *debug.mono.log*: 一個逗號分隔 *(',')* 元件清單,這些元件應列印到 Android 除錯日誌的其他消息。 默認情況下,不設置任何內容。 元件包括:

  - *所有*:列印所有信件
  - *gc*: 列印與 GC 相關的消息。
  - *gref*: 列印(弱、全域)引用分配和處理消息。
  - *lref*:列印本地引用分配和分配消息。

  > [!NOTE]
  > 這些都是*非常*冗長的。 除非確實需要,否則不要啟用。

- *debug.mono.trace*: 允許設定[單聲道 -跟蹤](http://docs.go-mono.com/?link=man%3amono(1))`=PROPERTY_VALUE`設置。

## <a name="deleting-bin-and-obj"></a>移除`bin`與`obj`

Xamarin.安卓過去曾遭受過以下情況的折磨:

- 您遇到一個奇怪的生成或運行時錯誤。
- `Clean`、或`Rebuild`手動刪除`bin``obj`和 目錄。
- 問題消失了。

由於這些問題對開發人員工作效率的影響,我們投入鉅資解決此類問題。

如果出現此類問題::

1. 做一個心理筆記。 最後一次將項目帶入此狀態的操作是什麼?
1. 保存當前生成日誌。 請嘗試再次產生,並記錄[診斷產生紀錄](#diagnostic-msbuild-output)。
1. 提交[錯誤報告][bug]。

在刪除和`bin``obj`目錄之前,將它們壓縮起來,並在需要時保存以供以後診斷。 您可能只是`Clean`您的 Xamarin.Android 應用程式專案,以便讓事情重新工作。

[bug]: https://github.com/xamarin/xamarin-android/wiki/Submitting-Bugs,-Feature-Requests,-and-Pull-Requests

## <a name="xamarinandroid-cannot-resolve-systemvaluetuple"></a>Xamarin.安卓無法解析系統。

由於與 Visual Studio 不相容,將發生此錯誤。

- **Visual Studio 2017 更新 1(** 版本 15.1 或更版本)僅與**系統相容。**

- **Visual Studio 2017 更新2(** 版本15.2或更高版本)僅與**系統相容。**

請選擇正確的系統。

## <a name="gc-messages"></a>GC 訊息

可以通過將 debug.mono.log 系統屬性設置為包含 gc 的值來查看 GC 元件訊息。

GC 消息在 GC 執行時生成,並提供有關 GC 執行多少工作的資訊:

```shell
I/monodroid-gc(12331): GC cleanup summary: 81 objects tested - resurrecting 21.
```

通過將`MONO_LOG_LEVEL`環境變數設定為 :可以生成其他 GC 資訊,`debug`如計時 資訊:

```shell
adb shell setprop debug.mono.env MONO_LOG_LEVEL=debug
```

這將導致(大量)其他單聲道消息,包括以下三個後果:

```shell
D/Mono (15723): GC_BRIDGE num-objects 1 num_hash_entries 81226 sccs size 81223 init 0.00ms df1 285.36ms sort 38.56ms dfs2 50.04ms setup-cb 9.95ms free-data 106.54ms user-cb 20.12ms clenanup 0.05ms links 5523436/5523436/5523096/1 dfs passes 1104 6883/11046605
D/Mono (15723): GC_MINOR: (Nursery full) pause 2.01ms, total 287.45ms, bridge 225.60 promoted 0K major 325184K los 1816K
D/Mono ( 2073): GC_MAJOR: (user request) pause 2.17ms, total 2.47ms, bridge 28.77 major 576K/576K los 0K/16K
```

在消息`GC_BRIDGE`中`num-objects`, 是此傳遞所考慮的網路物件數`num_hash_entries`, 是在此調用網橋代碼期間處理的物件數。

在`GC_MINOR`和`GC_MAJOR`消息`total`中,是暫停世界時的時間量(沒有線程正在執行),而`bridge`網橋處理代碼(處理 JAva VM)中所花費的時間量。 當橋處理發生時,世界*不會*暫停。

 *通常*,值`num_hash_entries`越大`bridge`, 集合所花費的時間就`total`越多,收集 所花費的時間也就越大。

## <a name="global-reference-messages"></a>全域參考訊息

要啟用全域引用紀錄 (GREF) 紀錄紀錄 *,debug.mono.log*系統屬性必須包含*gref*,例如:

```shell
adb shell setprop debug.mono.log gref
```

Xamarin.Android 使用 Android 全域引用來提供 JAva 實例和關聯的託管實例之間的映射,就像調用 JAVA 方法時需要向 JAva 提供 JAVA 實例一樣。

遺憾的是,Android 模擬器一次只允許存在 2000 個全域引用。 硬體具有更高的限制為 52000 個全域引用。 在模擬器上運行應用程式時,下限可能存在問題,因此了解實例來自*何處*可能非常有用。

> [!NOTE]
> 全域引用計數是 Xamarin.Android 的內部引用,不包括(並且不包括)載入到流程中的其他本機庫獲取的全域引用。 使用全域引用計數作為估計值。

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

有四個後果消息:

- 全域引用創建:這些行以 *[g]* 開頭,將為創建代碼路徑提供堆疊跟蹤。
- 全域引用銷毀:這些行以 *-g-* 開頭,並且可能為處理全域引用的代碼路徑提供堆疊跟蹤。 如果 GC 正在釋放 gref,則不提供堆疊跟蹤。
- 弱全域引用創建:這些行以 *[w]* 開頭。
- 弱全域引用破壞:這些行以 *-w-* 開頭。

在所有郵件中 *,"grefc"* 值是 Xamarin.Android 創建的全域引用的計數,而*grefwc*值是 Xamarin.Android 創建的弱全域引用的計數。 *句柄*或*obj 句柄*值是 JNI*/* 句柄值,'' 之後的字元是句柄值類型 *:/L*表示本地引用 *,/G*表示全域引用 *,/W*表示弱全域引用。

作為 GC 進程的一部分,全域引用 ([g]) 將轉換為弱全域引用(導致 [w] 和 -g-),踢 Java 端 GC,然後檢查弱全域引用以查看是否收集了它。 如果它仍然處於活動狀態,則圍繞弱 ref (_g_-w-)創建一個新的 gref,否則弱 ref 將被銷毀 (-w)。

## <a name="java-instance-is-created-and-wrapped-by-a-mcw"></a>Java 實體由 MCW 建立及包裝

```shell
I/monodroid-gref(27679): +g+ grefc 2211 gwrefc 0 obj-handle 0x4066df10/L -> new-handle 0x4066df10/L from ...
I/monodroid-gref(27679): handle 0x4066df10; key_handle 0x4066df10: Java Type: `android/graphics/drawable/TransitionDrawable`; MCW type: `Android.Graphics.Drawables.TransitionDrawable`
```

## <a name="a-gc-is-being-performed"></a>執行 GC...

```shell
I/monodroid-gref(27679): +w+ grefc 1953 gwrefc 259 obj-handle 0x4066df10/G -> new-handle 0xde68f95f/W from take_weak_global_ref_jni
I/monodroid-gref(27679): -g- grefc 1952 gwrefc 259 handle 0x4066df10/G from take_weak_global_ref_jni
```

## <a name="object-is-still-alive-as-handle--null"></a>物件仍然處於活動狀態,作為句柄 != null
## <a name="wref-turned-back-into-a-gref"></a>沃夫斯轉回一個gref

```shell
I/monodroid-gref(27679): *try_take_global obj=0x4976f080 -> wref=0xde68f95f handle=0x4066df10
I/monodroid-gref(27679): +g+ grefc 1930 gwrefc 39 obj-handle 0xde68f95f/W -> new-handle 0x4066df10/G from take_global_ref_jni
I/monodroid-gref(27679): -w- grefc 1930 gwrefc 38 handle 0xde68f95f/W from take_global_ref_jni
```

## <a name="object-is-dead-as-handle--null"></a>物件已死,因為句柄 = null
## <a name="wref-is-freed-no-new-gref-created"></a>wref 被釋放,沒有創建新的 gref

```shell
I/monodroid-gref(27679): *try_take_global obj=0x4976f080 -> wref=0xde68f95f handle=0x0
I/monodroid-gref(27679): -w- grefc 1914 gwrefc 296 handle 0xde68f95f/W from take_global_ref_jni
```

這裡有一個"有趣的"皺紋:在 4.0 之前運行 Android 的目標上,gref 值等於 Android 運行時記憶體中的 JAVA 物件的位址。 (也就是說,GC 是一個不可移動的保守收集器,並且它直接引用這些物件。因此,在 [g]、[w]、-g-、[g]、-w- 序列之後,生成的 gref 將具有與原始 gref 值相同的值。 這使得通過日誌進行清理相當簡單。

但是,Android 4.0 具有移動收集器,不再直接引用 Android 運行時 VM 物件。 因此,在 [g]、[w]、-g-、[g]、-w- 序列之後,gref 值*將不同*。 如果物件在多個 G 中存活,它將使用多個 gref 值,從而更難確定實例的實際分配位置。

### <a name="querying-programmatically"></a>以程式設計查詢

您可以通過`JniRuntime`查詢 物件來查詢 GREF 和 WREF 計數。

`Java.Interop.JniRuntime.CurrentRuntime.GlobalReferenceCount`- 全域參考計數

`Java.Interop.JniRuntime.CurrentRuntime.WeakGlobalReferenceCount`- 弱參考計數

## <a name="android-debug-logs"></a>安卓調試日誌

[Android 除錯紀錄](~/android/deploy-test/debugging/android-debug-log.md)可能會提供有關您看到的任何運行時錯誤的其他上下文。

## <a name="floating-point-performance-is-terrible"></a>浮點性能糟糕!

或者,「我的應用在調試生成中比使用發佈版本運行快 10 倍!

Xamarin.Android 支援多個設備 ABI: *armeabi,**武裝v7a,* 和*x86*. 設備 AIS 可以在**專案屬性>應用程式選項卡>支援的體系結構中**指定。

除錯生成使用提供所有 ABI 的 Android 套件,因此將針對目標設備使用最快的 ABI。

發佈版本將僅包括「項目屬性」選項卡中選擇的 AIS。可以選擇多個。

*armeabi*是預設的 ABI,並且具有最廣泛的設備支援。 *然而*,armeabi不支援多CPU設備和硬體浮點,其他的東西。 因此,使用 armeabi 發佈運行時的應用將綁定到單個內核,並使用軟浮動實現。 這兩者都會導致應用性能顯著降低。

如果你的應用需要體面的浮點性能(例如遊戲),你應該啟用*armeabi-v7a* ABI。 您可能只想支援*armeabi-v7a*執行時,但這意味著僅支援*armeabi*的舊設備將無法運行你的應用。

## <a name="could-not-locate-android-sdk"></a>找不到 Android SDK

有 2 下載從谷歌為 Android SDK 為 Windows.
如果選擇 .exe 安裝程式,它將編寫註冊表項,告訴 Xamarin.Android,它安裝的位置。 如果您選擇 .zip 檔案並自行解壓縮,Xamarin.Android 不知道在哪裡查找 SDK。 您可以通過存**取工具>選項> Xamarin > Android 設置**來告訴 Xamarin.Android:

[![Xamarin Android 設定中的 Android SDK 位置](troubleshooting-images/01.png)](troubleshooting-images/01.png#lightbox)

## <a name="ide-does-not-display-target-device"></a>IDE 不顯示目標裝置

有時,您將嘗試將應用程式部署到設備,但要部署到的設備未顯示在「選擇設備」對話框中。 當 Android 調試橋決定去度假時,可能會發生這種情況。

要診斷此問題,找到[adb 程式](~/android/deploy-test/debugging/android-debug-log.md),然後執行:

```shell
adb devices
```

如果您的裝置不存在,則需要重新啟動 Android 除錯橋伺服器,以便找到您的裝置:

```shell
adb kill-server
adb start-server
```

HTC 同步軟體可能會阻止**adb 啟動伺服器**正常工作。 如果**adb 啟動伺服器**命令未列印出它啟動的連接埠,請退出 HTC 同步軟體並嘗試重新啟動 adb 伺服器。

## <a name="the-specified-task-executable-keytool-could-not-be-run"></a>無法執行指定的任務執行「密鑰工具」

這意味著您的 PATH 不包含 Java SDK 的 bin 目錄所在的目錄。 檢查您是否遵循[了安裝](~/android/get-started/installation/index.md)指南中的這些步驟。

## <a name="monodroidexe-or-aresgenexe-exited-with-code-1"></a>單體.exe 或 aresgen.exe 離開與代碼 1

為了説明除錯此問題,請進入 Visual Studio 並更改 MSBuild 詳細等級,為此,請選擇:**工具>選項>專案和****解決方案>生成**和**執行> MSBuild 專案產生輸出詳細性**並將此值設置為 **「正常**」。。

重新生成並檢查 Visual Studio 的「輸出」窗格,該窗格應包含全部錯誤。

## <a name="there-is-not-enough-storage-space-on-the-device-to-deploy-the-package"></a>裝置上沒有足夠的儲存空間來部署套件

當您未在 Visual Studio 內啟動模擬器時,將發生這種情況。 在 Visual Studio 外部啟動模擬器時,您`-partition-size 512`需要傳遞 選項,例如。

```shell
emulator -partition-size 512 -avd MonoDroid
```

使用正確的模擬器名稱,並[設定模擬器時使用的名稱](~/android/get-started/installation/windows.md#device)。

## <a name="install_failed_invalid_apk-when-installing-a-package"></a>安裝\_套件\_\_時 安裝失敗 APK

Android 包名稱*必須*包含句點 *('.*')。 編輯包名稱,使其包含句點。

- 在視覺工作室內:
  - 右鍵按下項目>屬性
  - 按一下左側的「Android 清單」選項卡。
  - 更新包名稱欄位。
    - 如果您看到消息&ldquo;「未找到 AndroidManifest.xml」。。 按一下以添加一個。&rdquo;,單擊鏈接,然後更新"包"名稱欄位。
- 在 Mac 視覺工作室內:
  - 右鍵按一下您的專案>選項。
  - 導航到"生成/Android 應用程式"部分。
  - 將「包」名稱欄位更改為包含".

## <a name="install_failed_missing_shared_library-when-installing-a-package"></a>安裝\_套件\_\_時\_安裝故障分享函式庫

此上下文中的「共用庫」不是本機共用庫 *(libfoo.so*) 檔案,*但*相反,它是必須單獨安裝在目標設備上(如 Google 地圖)的庫。

Android 包`<uses-library/>`指定 元素需要哪些共用庫。 如果目標裝置上不存在*所需的*`//uses-library/@android:required`庫(例如*為 true,* 這是預設值),則包安裝將失敗 *,安裝\_\_\_\_* 失敗。

要確定哪些共用庫是必需的,請查看*生成的*
**AndroidManifest.xml**檔(例如**obj\\調試\\AndroidManifest.xml),\\**並查找`<uses-library/>`元素。 `<uses-library/>`元素可以手動添加到專案**的\\屬性 AndroidManifest.xml**檔中,並通過[UseLibraryAttribute 自定義屬性](xref:Android.App.UsesLibraryAttribute)。

例如,添加對*Mono.Android.GoogleMaps.dll*的程式集引用將`<uses-library/>`隱式 添加 Google 地圖共用庫 。

## <a name="install_failed_update_incompatible-when-installing-a-package"></a>安裝\_套件\_\_時 安裝失敗更新

Android 套件有三個要求:

- 它們必須包含一個」。(請參閱上一個條目)
- 它們必須有一個唯一的字串包名稱(因此,在 Android 應用程式名稱中看到的反向 tld 約定,例如 Chrome 應用的 com.android.chrome)
- 升級包時,包必須具有相同的簽名密鑰。

因此,想像一下這種情況:

1. 產生&將應用部署為除錯應用程式
2. 變更簽署金鑰,例如用作發佈應用程式(或者因為您不喜歡預設提供的除錯簽署金鑰)
3. 安裝應用程式時無需先刪除應用,例如,在 Visual Studio 中除錯>啟動而不除錯

發生這種情況時,包安裝將失敗,出現安裝\_\_失敗\_更新 錯誤,因為在簽名密鑰執行時,包名稱未更改。 [Android 調試日誌](~/android/deploy-test/debugging/android-debug-log.md)還將包含類似於以下消息的消息:

```shell
E/PackageManager(  146): Package [PackageName] signatures do not match the previously installed version; ignoring!
```

要修復此錯誤,請從設備中完全刪除應用程式,然後再重新安裝。

## <a name="install_failed_uid_changed-when-installing-a-package"></a>安裝\_\_套件\_時安裝 失敗 UID 變更

安裝 Android 包時,將為其分配*使用者 ID* (UID)。
*有時*,由於當前未知的原因,在已安裝的應用上安裝時,安裝將失敗`INSTALL_FAILED_UID_CHANGED`,

```shell
ERROR [2015-03-23 11:19:01Z]: ANDROID: Deployment failed
Mono.AndroidTools.InstallFailedException: Failure [INSTALL_FAILED_UID_CHANGED]
   at Mono.AndroidTools.Internal.AdbOutputParsing.CheckInstallSuccess(String output, String packageName)
   at Mono.AndroidTools.AndroidDevice.<>c__DisplayClass2c.<InstallPackage>b__2b(Task`1 t)
   at System.Threading.Tasks.ContinuationTaskFromResultTask`1.InnerInvoke()
   at System.Threading.Tasks.Task.Execute()
```

要解決此問題,請*完全卸載*Android 包,要麼從 Android 目標的 GUI 安裝應用,要麼使用`adb`:

```shell
$ adb uninstall @PACKAGE_NAME@
```

**不要使用**`adb uninstall -k`,因為這將*保留*應用程式數據,從而在目標設備上保留衝突的 UID。

## <a name="release-apps-fail-to-launch-on-device"></a>在裝置上啟動

Android 除錯紀錄輸出是否包含類似於以下消息的消息:

```shell
D/AndroidRuntime( 1710): Shutting down VM
W/dalvikvm( 1710): threadid=1: thread exiting with uncaught exception (group=0xb412f180)
E/AndroidRuntime( 1710): FATAL EXCEPTION: main
E/AndroidRuntime( 1710): java.lang.UnsatisfiedLinkError: Couldn't load monodroid: findLibrary returned null
E/AndroidRuntime( 1710):        at java.lang.Runtime.loadLibrary(Runtime.java:365)
```

如果是這樣,有兩個可能的原因:

1. .apk 不提供目標設備支援的 ABI。
    例如,.apk 僅包含武裝-v7a 二進位檔,目標設備僅支援 armeabi。

2. [一個安卓錯誤](https://code.google.com/p/android/issues/detail?id=21670)。 如果是這種情況,請卸載應用、交叉手指並重新安裝應用。

要修復 (1),請編輯項目選項/屬性,並將[對所需 ABI 的支援到支援的 ABI 清單中](~/android/app-fundamentals/cpu-architectures.md)。要確定需要新增的 ABI,請針對目標裝置執行以下 adb 命令:

```shell
adb shell getprop ro.product.cpu.abi
adb shell getprop ro.product.cpu.abi2
```

輸出將包含主(和可選輔助)AIS。

```shell
$ adb shell getprop | grep ro.product.cpu
[ro.product.cpu.abi2]: [armeabi]
[ro.product.cpu.abi]: [armeabi-v7a]
```

## <a name="the-outpath-property-is-not-set-for-project-ldquomyappcsprojrdquo"></a>"OutPath"屬性未為專案&ldquo;MyApp.csproj 設置&rdquo;

這通常意味著您擁有 HP 計算機,環境&ldquo;變數&rdquo;平臺 已設置為類似 MCD 或 HPD。 這與通常&ldquo;設置為任何&rdquo;CPU&ldquo;或&rdquo;x86 的 MSBuild 平台屬性衝突。 在 MSBuild 能夠正常工作之前,您需要從電腦中移除此環境變數:

- 控制面板>系統>高級>環境變數

重新啟動 Mac 的可視化工作室或可視化工作室,並嘗試重建。 事情現在應該按預期工作。

## <a name="javalangclasscastexception-monoandroidruntimejavaobject-cannot-be-cast-to"></a>java.lang.ClassCastException:單聲道.android.運行時.JAvaObject不能強制轉換為...

Xamarin.Android 4.x 不能正確封送嵌套泛型類型。 例如,使用\#[簡單可擴充清單配接器](xref:Android.Widget.SimpleExpandableListAdapter)請考慮以下 C 程式碼:

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

問題是 Xamarin.Android 錯誤地封送嵌套泛型類型。 `List<IDictionary<string, object>>`正在封送到[java.lang.ArrrayList](xref:Java.Util.ArrayList)`ArrayList`,但 包含`mono.android.runtime.JavaObject`的實`Dictionary<string, object>`例 (引用 實例),而不是實現[java.util.Map](xref:Java.Util.IMap)的東西,從而導致以下異常:

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

解決方法是使用提供的[JAVA 集合類型](~/android/internals/api-design.md),而不是`System.Collections.Generic`&ldquo;內部&rdquo;類型的類型。 這將在封送實例時產生適當的 Java 類型。 (為了減少 gref 存留期,以下代碼比必要代碼複雜得多。 它可以簡化為透過`s/List/JavaList/g`更改原始代碼,`s/Dictionary/JavaDictionary/g`如果 gref 存留期不擔心。

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

[這會在將來的版本中修復](https://bugzilla.xamarin.com/show_bug.cgi?id=5401)。

## <a name="unexpected-nullreferenceexceptions"></a>意外的空參考例外

有時[,Android 調試日誌](~/android/deploy-test/debugging/android-debug-log.md)會提到無法發生的 NullReference&ldquo;異常&rdquo;,或者來自 Android 運行時代碼的 Mono,在應用死前不久:

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

當 Android 運行時決定中止行程時,可能會發生這種情況,這可能發生在任意數量的原因,包括達到目標的 GREF 限制或使用&ldquo;&rdquo;JNI 做錯 某些 事情時。

要查看是否屬於這種情況,請查看 Android 除錯日誌,瞭解來自行程的類似消息:

```shell
E/dalvikvm(  123): VM aborting
```

## <a name="abort-due-to-global-reference-exhaustion"></a>由於全域引用耗盡而中止

Android 運行時的 JNI 層僅支援有限數量的 JNI 物件引用,以在任何給定的時間點有效。 當超過此限制時,事情會中斷。

GREF (*全域參考*) 限制是模擬器中的 2000 個引用,硬體上的引用為 +52000。

當您在 Android 除錯紀錄中看到此類訊息時,您就知道您開始建立太多 GREF:

```shell
D/dalvikvm(  602): GREF has increased to 1801
```

達到 GREF 限制時,將列印如下消息:

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

在上面的例子(順便說一下,它來自[bug685215),](https://bugzilla.novell.com/show_bug.cgi?id=685215)問題是太多的Android.Graphics.Point實例正在創建;有關此特定 Bug 的修復清單,請參閱[\#註釋 2。](https://bugzilla.novell.com/show_bug.cgi?id=685215#c2)

通常,有用的解決方案是查找哪個類型分配了太多的&ndash;實例 Android.Graphics.Point 在&ndash;上述轉儲 中查找它們在原始程式碼中創建的位置並適當地處置它們(以便縮短其 Java 物件存留期)。 這並不總是合適的(685215\#是多線程的,因此簡單的解決方案避免了 Dispose 調用),但這是首先要考慮的。

您可以啟用[GREF 紀錄記錄](~/android/troubleshooting/index.md)以查看何時創建 GREF 以及存在多少。

## <a name="abort-due-to-jni-type-mismatch"></a>由於 JNI 類型不匹配而中止

如果手動滾動 JNI 代碼,則類型可能無法正確匹配,例如,`java.lang.Runnable.run`如果您嘗試呼叫`java.lang.Runnable`未實現的類型。 發生這種情況時,Android 調試日誌中將顯示類似於此的消息:

```shell
W/dalvikvm( 123): JNI WARNING: can't call Ljava/Type;;.method on instance of Lanother/java/Type;
W/dalvikvm( 123):              in Lmono/java/lang/RunnableImplementor;.n_run:()V (CallVoidMethodA)
...
E/dalvikvm( 123): VM aborting
```

## <a name="dynamic-code-support"></a>動態代碼支援

### <a name="dynamic-code-does-not-compile"></a>動態代碼不編譯

要在應用程式\#或庫中使用 C 動態,您必須向專案添加 System.Core.dll、Microsoft.CSharp.dll 和 Mono.CSharp.dll。

### <a name="in-release-build-missingmethodexception-occurs-for-dynamic-code-at-run-time"></a>在發佈版本中,在運行時動態代碼會出現缺失方法異常。

- 您的應用程式專案很可能沒有對 System.Core.dll、Microsoft.CSharp.dll 或 Mono.CSharp.dll 的引用。 確保引用這些程式集。

  - 請記住,動態代碼總是成本。 如果需要高效的代碼,請考慮不使用動態代碼。

- 在第一個預覽中,除非應用程式代碼顯式使用每個程式集中的類型,否則將排除這些程式集。 有關解決方法,請參閱以下內容:[http://lists.ximian.com/pipermail/mo...il/009798.html](http://lists.ximian.com/pipermail/monodroid/2012-April/009798.html)

## <a name="projects-built-with-aotllvm-crash-on-x86-devices"></a>在 x86 裝置使用 AOT_LLVM 當機建構的項目

在基於 x86 的裝置上部署使用[AOT_LLVM](~/android/deploy-test/release-prep/index.md)建構的應用程式時,您可能會看到類似於以下內容的異常錯誤訊息:

```shell
Assertion: should not be reached at /Users/.../external/mono/mono/mini/tramp-x86.c:124
Fatal signal 6 (SIGABRT), code -6 in tid 4051 (Xamarin.bug56111)
```

這是一個已知問題 - 解決方法是禁用 LLVM。
