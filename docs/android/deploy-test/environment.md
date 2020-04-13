---
title: Xamarin.Android 環境
ms.prod: xamarin
ms.assetid: 67BFD4E1-276C-4B9F-9BD8-A5218D2BD529
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/15/2018
ms.openlocfilehash: 54fc52c2f2460726fe1c22149d4e7cc0e8a92609
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73028064"
---
# <a name="xamarinandroid-environment"></a>Xamarin.Android 環境

## <a name="execution-environment"></a>執行環境

*執行環境*是一組環境變數及 Android 系統屬性，會影響程式的執行。 Android 系統屬性可使用 `adb shell setprop` 命令進行設定，而環境變數則可透過設定 `debug.mono.env` 系統屬性來進行設定：

```shell
## Enable GREF logging
adb shell setprop debug.mono.log gref

## Set the MONO_LOG_LEVEL and MONO_LOG_MASK environment variables
## so that additional Mono messages will be written to `adb logcat`.
adb shell setprop debug.mono.env "'MONO_LOG_LEVEL=info|MONO_LOG_MASK=asm'"
```

Android 系統屬性會針對目標裝置上所有的處理序進行設定。

從 Xamarin.Android 4.6 開始，系統屬性和環境變數都可藉由將「環境檔案」** 新增至專案，以每個應用程式為基礎來進行設定或覆寫。 環境檔案是一個帶有 [`AndroidEnvironment`**建置動作**](~/android/deploy-test/building-apps/build-process.md)的 UNIX 格式純文字檔案。
環境檔案包含以此格式撰寫的行：*key=value*。
註解是開頭為 `#` 的行。 空白行會遭到忽略。

若 *key* 的開頭為大寫字元，則 *key* 便會作為環境變數處理，而 **setenv**(3) 則是用於在處理序啟動時將環境變數設為指定的 *value*。

若 *key* 的開頭為小寫字元，則 *key* 便會作為 Android 系統屬性來處理，其 *value* 為*預設值*：控制 Xamarin.Android 執行行為的 Android 系統屬性會先從 Android 系統屬性儲存區尋找，若找不到，則便會使用環境檔案中指定的值。 這可允許 `adb shell setprop` 用於覆寫環境檔案中的值，作為診斷之用。

## <a name="xamarinandroid-environment-variables"></a>Xamarin.Android 環境變數

Xamarin.Android 支援 `XA_HTTP_CLIENT_HANDLER_TYPE` 變數，可透過 `adb shell setprop debug.mono.env` 或 `$(AndroidEnvironment)` 建置動作進行設定。

### `XA_HTTP_CLIENT_HANDLER_TYPE`

組件限定類型，其必須從 [HttpMessageHandler](https://docs.microsoft.com/dotnet/api/system.net.http.httpmessagehandler?view=xamarinandroid-7.1) 繼承並從 [`HttpClient()` 預設建構函式](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient.-ctor?view=xamarinandroid-7.1#System_Net_Http_HttpClient__ctor)建構。

在 Xamarin.Android 6.1 中，預設不會設定環境變數，並且會使用 [HttpClientHandler](https://docs.microsoft.com/dotnet/api/system.net.http.httpclienthandler?view=xamarinandroid-7.1)。

或者,可以指定`Xamarin.Android.Net.AndroidClientHandler`該值以使用[`java.net.URLConnection`](xref:Java.Net.URLConnection)
「可」** 允許使用 TLS 1.2 (若 Android 支援它的話)。

已在 Xamarin.Android 6.1 中新增。

## <a name="xamarinandroid-system-properties"></a>Xamarin.Android 系統屬性

Xamarin.Android 支援下列系統屬性，可透過 `adb shell setprop` 或 `$(AndroidEnvironment)` 建置動作進行設定。

- `debug.mono.debug`
- `debug.mono.env`
- `debug.mono.gc`
- `debug.mono.log`
- `debug.mono.max_grefc`
- `debug.mono.profile`
- `debug.mono.runtime_args`
- `debug.mono.trace`
- `debug.mono.wref`
- `XA_HTTP_CLIENT_HANDLER_TYPE`

### `debug.mono.debug`

`debug.mono.debug` 系統屬性的值為一個整數。 若為 `1`，則行為便會如同處理序使用 `mono --debug` 啟動一樣。
這通常會在堆疊追蹤中顯示檔案和行資訊，而無須從偵錯工具啟動應用程式。

### `debug.mono.env`

包含一個環境變數清單 (以 `|` 號分隔)。

### `debug.mono.gc`

`debug.mono.debug` 系統屬性的值為一個整數。
若為 `1`，則會記錄 GC 資訊。

這相當於讓 `debug.mono.log` 系統屬性包含 `gc`。

### `debug.mono.log`

控制 Xamarin.Android 記錄到 `adb logcat` 的額外資訊。
它是一個逗號分隔的字串 (`,`)，包含下列值中的其中一個：

- `all`：印出「所有」** 訊息。 這通常不是一個好主意，因為其包含了 `lref` 訊息。
- `assembly`：印出 `.apk` 和組件剖析訊息。
- `gc`：印出與 GC 相關的訊息。
- `gref`：印出 JNI 全域參考訊息。
- `lref`：印出 JNI 區域參考訊息。
  > [!NOTE]
  > 這「真的」** 會讓 `adb logcat` 充斥許多垃圾訊息。
  > 在 Xamarin.Android 5.1 中，這也會建立一個 `.__override__/lrefs.txt` 檔案，該檔案可能會變得「非常大」**。
  > 請避免。
- `timing`：印出某些方法計時資訊。 這也會建立 `.__override__/methods.txt` 及 `.__override__/counters.txt` 檔案。

### `debug.mono.max_grefc`

`debug.mono.max_grefc` 系統屬性的值為一個整數。
它的值會「覆寫」** 針對目標裝置預設偵測到的最大 GRF 計數。

*請注意:* 這僅可用於,`adb shell setprop
debug.mono.max_grefc`因為該值在**環境.txt**檔中不能及時使用。

### `debug.mono.profile`

`debug.mono.profile` 系統屬性會啟用分析工具。
它相當於 `mono --profile` 選項，並且使用相同的值。 (請參閱 [**mono**(1)](http://docs.go-mono.com/?link=man%3amono(1)) 手冊頁面以取得詳細資訊。)

### `debug.mono.runtime_args`

`debug.mono.runtime_args` 系統屬性包含應由 **mono** 剖析的額外選項。

### `debug.mono.trace`

`debug.mono.trace` 系統屬性會啟用追蹤。
它相當於 `mono --trace` 選項，並且使用相同的值。 (請參閱 [**mono**(1)](http://docs.go-mono.com/?link=man%3amono(1)) 手冊頁面以取得詳細資訊。)

一般情況下，「請勿使用」**。 使用追蹤會讓 `adb logcat` 輸出充斥垃圾訊息，嚴重降低程式行為的速度，並會改變程式的行為 (最高並會包含導致其他錯誤狀況)。

然而，「有時候」**，它可讓您執行一些額外的調查...

### `debug.mono.wref`

`debug.mono.wref` 系統屬性會允許覆寫預設偵測到的 JNI 弱式參考機制。 有兩個支援的值：

- `jni`：使用 JNI 弱式參考，由 `JNIEnv::NewWeakGlobalRef()` 建立並由 `JNIEnv::DeleteWeakGlobalREf()` 終結。
- `java`:使用引用`java.lang.WeakReference`實例的 JNI 全域引用。

直到 API-7 及啟用 ART 的 API-19 (Kit Kat) 都會預設使用 `java`。 (API-8 新增 `jni` 參考，ART 會*破壞* `jni` 參考。)

此系統屬性在測試及特定形式的調查時很有用。
「一般情況下」**，您不應變更它。

### <a name="xa_http_client_handler_type"></a>XA\_HTTP\_CLIENT\_HANDLER\_TYPE

在 Xamarin.Android 6.1 中首次引入，此環境變數會宣告 `HttpClient` 使用的預設 `HttpMessageHandler` 實作。 根據預設不會設定此變數，而 Xamarin.Android 會使用 `HttpClientHandler`。

```shell
XA_HTTP_CLIENT_HANDLER_TYPE=Xamarin.Android.Net.AndroidClientHandler
```

> [!NOTE]
> 基礎 Android 裝置必須支援 TLS 1.2。
Android 5.0 及更新版本支援 TLS 1.2

## <a name="example"></a>範例

```shell
## Comments are lines which start with '#'
## Blank lines are ignored.

## Enable GREF messages to `adb logcat`
debug.mono.log=gref

## Clear out a Mono environment variable to decrease logging
MONO_LOG_LEVEL=
```

## <a name="related-links"></a>相關連結

- [傳輸層安全性](~/cross-platform/app-fundamentals/transport-layer-security.md)
