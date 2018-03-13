---
title: "Android 偵錯記錄檔"
ms.topic: article
ms.prod: xamarin
ms.assetid: 01A715FE-9E9D-9B85-8A59-6568D8A09CA5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 26ac42e4b7acbe19dee746130fc335fdf18ffc46
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="android-debug-log"></a>Android 偵錯記錄檔

## <a name="android-debug-log-overview"></a>Android 偵錯記錄檔概觀

開發人員所使用的其中一個非常常見的應用程式偵錯技巧就是呼叫 `Console.WriteLine`。 不過，在 Android 這類行動平台上並沒有主控台。 Android 裝置會提供一個記錄檔，您可以在撰寫應用程式時使用此記錄檔。 由於擷取此記錄檔時所輸入命令的緣故，因此這個記錄檔有時稱為 'logcat' 。

## <a name="accessing-from-visual-studio"></a>從 Visual Studio 存取

在 Visual Studio 2015 和更新的版本中，Android 和 iOS 記錄台已整合在一起。

### <a name="visual-studio-2015--2017"></a>Visual Studio 2015 和 2017

Visual Studio 的新 [裝置記錄檔] 工具視窗會顯示 Android 和 iOS 裝置的記錄檔。 透過執行下列任何一個命令，即可顯示此記錄檔： 

-   [檢視] -> [其他視窗] -> [裝置記錄檔]
-   [工具] -> [Android] -> [裝置記錄檔]
-   Android 工具列 -> [裝置記錄檔]

顯示工具視窗之後，即可從裝置下拉式方塊選取實體裝置。 選取裝置之後，它就會自動開始在表格中新增來自執行中應用程式的記錄項目。 如果在裝置之間切換，將會停止後再啟動裝置記錄。為了讓裝置出現在下拉式方塊中，必須載入 Android 專案。 如果裝置未出現在下拉式方塊中，請先檢查 [啟動偵錯] 下拉式清單中是否有該裝置。 

此工具視窗會提供：記錄項目表、可選取裝置的下拉式方塊、清除記錄項目的方式、搜尋方塊，以及播放/停止/暫停按鈕。 



## <a name="accessing-from-the-command-line"></a>從命令列存取

另一個檢視偵錯記錄檔的方式是透過命令列。 請開啟主控台視窗，然後瀏覽至 Android SDK 平台工具資料夾 (例如 **C:\android-sdk-windows\platform-tools**)。 

如果連接的裝置只有一部，則可以使用以下命令來檢視記錄檔：

```shell
$ adb logcat
```

如果連接的裝置有多部，則必須識別裝置。 例如，`adb -d logcat` 會顯示唯一連接之實體裝置的記錄檔，而 `adb -e logcat` 則會顯示唯一執行中之模擬器的記錄檔。 

如需了解更多命令，只要執行 **adb** 即可。



## <a name="writing-to-the-debug-log"></a>寫入至偵錯記錄檔

您可以使用 [Android.Util.Log](https://developer.xamarin.com/api/type/Android.Util.Log/) 類別上的方法將訊息寫入至「偵錯記錄檔」： 

```csharp
string tag = "myapp";

Log.Info (tag, "this is an info message");
Log.Warn (tag, "this is a warning message");
Log.Error (tag, "this is an error message");
```

這會產生：

```shell
I/myapp   (11103): this is an info message
W/myapp   (11103): this is a warning message
E/myapp   (11103): this is an error message
```


## <a name="interesting-messages"></a>有趣的訊息

讀取記錄檔時，尤其是要提供記錄檔程式碼片段給其他使用者時 (因為提供完整記錄檔 (1) 沒有必要且 (2) 無法讀取)，要開始著手的「最重要」一行就是類似下方的行：

```shell
I/ActivityManager(12944): Starting: Intent { act=android.intent.action.MAIN cat=[android.intent.category.LAUNCHER] flg=0x10200000 cmp=GcTest.GcTest/gctest.Activity1 } from pid 24175
```

具體而言，就是符合以下規則運算式：

```shell
^I.*ActivityManager.*Starting: Intent
```

且含有應用程式套件名稱的行。 這是與活動開頭對應的行，且「大多數」 (並非全部) 下列訊息應該與應用程式相關。 

特別是，每則訊息都會包含產生訊息之處理序的處理序識別項。 在上述 `ActivityManager` 訊息中，產生訊息的是 `12944` 處理序。 若要判斷哪個處理序是所偵錯之應用程式的處理序，請尋找 mono.MonoRuntimeProvider 訊息： 

```shell
I/ActivityThread(  602): Pub TouchTest.TouchTest.__mono_init__: mono.MonoRuntimeProvider
```

此訊息來自所啟動的處理序，而下列所有包含相同 pid 的訊息都是來自相同處理序。 
