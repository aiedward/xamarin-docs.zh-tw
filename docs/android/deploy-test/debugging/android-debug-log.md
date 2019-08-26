---
title: Android 偵錯記錄檔
description: 如何使用偵錯記錄檔偵錯 Xamarin.Android 應用程式。
ms.prod: xamarin
ms.assetid: 01A715FE-9E9D-9B85-8A59-6568D8A09CA5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/22/2018
ms.openlocfilehash: fd28143a2d4678a240d9d6f9f1cc3b1c5da80222
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69525704"
---
# <a name="android-debug-log"></a>Android 偵錯記錄檔

開發人員所使用的其中一個非常常見的應用程式偵錯技巧就是呼叫 `Console.WriteLine`。 不過，在 Android 這類行動平台上並沒有主控台。 Android 裝置會提供一個記錄檔，您可以在撰寫應用程式時使用此記錄檔。 由於擷取此記錄檔時所輸入命令的緣故，因此這個記錄檔有時會被稱為 _logcat_。 使用 [偵錯記錄檔]  工具來檢視已記錄的資料。

## <a name="android-debug-log-overview"></a>Android 偵錯記錄檔概觀

[偵錯記錄檔]  工具能提供在透過 Visual Studio 對應用程式進行偵錯的期間檢視記錄檔輸出的方式。 偵錯記錄檔支援下列裝置：

- 實體 Android 手機、平板電腦及穿戴式裝置。
- 在 Android Emulator 上執行的 Android 虛擬裝置。 

> [!NOTE]
> [偵錯記錄檔]  工具無法搭配 Xamarin Live Player 運作。

**偵錯記錄檔**不會顯示應用程式於裝置上獨立執行期間 (也就是當它與 Visual Studio 之間的連線中斷時) 所產生的記錄訊息。


## <a name="accessing-the-debug-log-from-visual-studio"></a>從 Visual Studio 存取偵錯記錄檔

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

若要開啟 [偵錯記錄檔]  工具，請按一下工具列上的 [裝置記錄檔 (logcat)]  圖示：

[![工具列上 [裝置記錄檔] 工具的位置](android-debug-log-images/vswin-01-logcat-sml.png)](android-debug-log-images/vswin-01-logcat.png#lightbox)

或是從下列其中一項功能表選項，啟動 [裝置記錄檔]  工具：

- [檢視] -> [其他視窗] -> [裝置記錄檔] 
- [工具] -> [Android] -> [裝置記錄檔] 

下列螢幕擷取畫面會示範 [偵錯工具]  視窗的各個部分：

[![[偵錯工具] 視窗的各個部分](android-debug-log-images/vswin-03-features-sml.png)](android-debug-log-images/vswin-03-features.png#lightbox)

- **裝置選取器** &ndash; 選取要監視的實體裝置或執行中的模擬器。

- **記錄檔項目** &ndash; 來自 logcat 的記錄訊息資料表。

- **清除記錄檔項目** &ndash; 從資料表中清除所有目前的記錄檔項目。

- **播放/暫停** &ndash; 在更新或暫停新記錄項目的顯示之間切換。

- **停止** &ndash; 中止新記錄檔項目的顯示。

- **搜尋方塊** &ndash; 在此方塊中輸入搜尋字串以篩選出記錄項目的子集。


顯示 [偵錯記錄檔]  工具視窗時，請使用裝置下拉式功能表來選擇要監視的 Android 裝置：

[![[裝置選取器] 的位置](android-debug-log-images/vswin-02-devices-combo-sml.png)](android-debug-log-images/vswin-02-devices-combo.png#lightbox)

選取裝置之後，[裝置記錄]  工具會自動新增來自執行中應用程式的記錄項目 &ndash; 這些記錄項目會顯示於記錄項目的資料表中。 在裝置之間切換會停止及啟動裝置記錄。 請注意，必須先載入 Android 專案，裝置才會出現在裝置選取器中。 若裝置沒有出現在裝置選取器中，請確認該裝置是否有出現在位於 [啟動]  按鈕旁邊的 Visual Studio 裝置下拉式功能表中。


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

若要開啟 [裝置記錄檔]  ，請按一下 [檢視] > [面板] > [裝置記錄檔]  ：

[![[裝置記錄檔] 功能表項目的位置](android-debug-log-images/vsmac-01-logcat-sml.png)](android-debug-log-images/vsmac-01-logcat.png#lightbox)

下列螢幕擷取畫面會示範 [偵錯工具]  視窗的各個部分：

[![[偵錯工具] 視窗的功能](android-debug-log-images/vsmac-03-features-sml.png)](android-debug-log-images/vsmac-03-features.png#lightbox)

- **裝置選取器** &ndash; 選取要監視的實體裝置或執行中的模擬器。

- **記錄檔項目** &ndash; 來自 logcat 的記錄訊息資料表。

- **清除記錄檔項目** &ndash; 從資料表中清除所有目前的記錄檔項目。

- **搜尋方塊** &ndash; 在此方塊中輸入搜尋字串以篩選出記錄項目的子集。

- **顯示訊息** &ndash; 切換告知性訊息的顯示。

- **顯示警告** &ndash; 切換警告訊息的顯示 (警告訊息會以黃色顯示)。

- **顯示錯誤** &ndash; 切換錯誤訊息的顯示 (錯誤訊息會以紅色顯示)。

- **重新連線** &ndash; 重新連線至裝置並重新整理記錄檔項目顯示。

- **新增標記** &ndash; 在最新的記錄項目之後插入標記訊息 (例如 `--- Marker N ---`)，其中 _N_ 為從 1 開始的計數器，並會隨新標記的新增以 1 為單位遞增。

顯示 [偵錯記錄檔] 工具視窗時，請使用裝置下拉式功能表來選擇要監視的 Android 裝置：

[![[裝置選取器] 的位置](android-debug-log-images/vsmac-02-devices-combo-sml.png)](android-debug-log-images/vsmac-02-devices-combo.png#lightbox)

選取裝置之後，[裝置記錄]  工具會自動新增來自執行中應用程式的記錄項目 &ndash; 這些記錄項目會顯示於記錄項目的資料表中。 在裝置之間切換會停止及啟動裝置記錄。 請注意，必須先載入 Android 專案，裝置才會出現在裝置選取器中。 若裝置沒有出現在裝置選取器中，請確認該裝置是否有出現在位於 [啟動]  按鈕旁邊的 Visual Studio 裝置下拉式功能表中。

-----


## <a name="accessing-from-the-command-line"></a>從命令列存取

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

另一個檢視偵錯記錄檔的方式是透過命令列。 開啟命令提示字元視窗，並瀏覽至 Android SDK 平台工具資料夾 (SDK 平台工具資料夾通常位於 **C:\\Program Files (x86)\\Android\\android-sdk\\platform-tools**)。

若只有連接單一裝置 (實體裝置或模擬器)，則可以透過輸入下列命令來檢視記錄檔：

```shell
$ adb logcat
```

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

另一個檢視偵錯記錄檔的方式是透過命令列。 開啟終端機視窗，並瀏覽至 Android SDK 平台工具資料夾 (SDK 平台工具資料夾通常位於 **/Users/username/Library/Developer/Xamarin/android-sdk-macosx/platform-tools**)。

若只有連接單一裝置 (實體裝置或模擬器)，則可以透過輸入下列命令來檢視記錄檔：

```shell
$ ./adb logcat
```

-----


如果連接的裝置有多部，則必須明確識別該裝置。 例如，**adb -d logcat** 會顯示唯一連接之實體裝置的記錄檔，而 **adb -e logcat** 則會顯示唯一執行中之模擬器的記錄檔。

可以透過輸入 **adb** 並閱讀說明訊息來找到更多命令。


## <a name="writing-to-the-debug-log"></a>寫入至偵錯記錄檔

您可以使用 [Android.Util.Log](xref:Android.Util.Log) \(英文\) 類別上的方法將訊息寫入至**偵錯記錄檔**。
例如： 

```csharp
string tag = "myapp";

Log.Info (tag, "this is an info message");
Log.Warn (tag, "this is a warning message");
Log.Error (tag, "this is an error message");
```

這會產生類似下列內容的輸出：

```shell
I/myapp   (11103): this is an info message
W/myapp   (11103): this is a warning message
E/myapp   (11103): this is an error message
```

您也可以使用 `Console.WriteLine` 寫入**偵錯記錄**&ndash;這些訊息會顯示在 Logcat 中，但輸出格式略有不同 (當在 Android 上偵錯 Xamarin.Forms 應用程式時，此技術特別實用)：

```csharp
System.Console.WriteLine ("DEBUG - Button Clicked!");
```

這會在 Logcat 中產生類似下列內容的輸出：

```
Info (19543) / mono-stdout: DEBUG - Button Clicked!
```

## <a name="interesting-messages"></a>有趣的訊息

讀取記錄檔時 (特別是在為其他人提供記錄檔片段時)，直接讀取整個記錄檔通常是一件耗費心力的事情。
若要使瀏覽記錄檔訊息變得更為輕鬆，請尋找類似下列內容的記錄檔項目：

```shell
I/ActivityManager(12944): Starting: Intent { act=android.intent.action.MAIN cat=[android.intent.category.LAUNCHER] flg=0x10200000 cmp=GcTest.GcTest/gctest.Activity1 } from pid 24175
```

特別是尋找符合規則運算式且同時包含應用程式套件名稱的行：

```shell
^I.*ActivityManager.*Starting: Intent
```

這是與活動開頭對應的行，且「大多數」  (並非全部) 下列訊息應該與應用程式相關。

注意到每則訊息都會包含產生訊息之處理序的處理序識別項 (pid)。 在上述 `ActivityManager` 訊息中，產生訊息的是 `12944` 處理序。 若要判斷哪個處理序是所偵錯之應用程式的處理序，請尋找 **mono.MonoRuntimeProvider** 訊息： 

```shell
I/ActivityThread(  602): Pub TouchTest.TouchTest.__mono_init__: mono.MonoRuntimeProvider
```

此訊息式來自已啟動的處理序。 所有包含此 pid 的後續訊息都會是來自相同的處理序。
