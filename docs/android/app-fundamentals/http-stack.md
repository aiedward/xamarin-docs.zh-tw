---
title: 適用于 Android 的 HttpClient 堆疊和 SSL/TLS 執行選取器
description: HttpClient 堆疊和 SSL/TLS 實作為選取器會決定您的 Xamarin Android 應用程式將使用的 HttpClient 和 SSL/TLS 執行。
ms.prod: xamarin
ms.assetid: D7ABAFAB-5CA2-443D-B902-2C7F3AD69CE2
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/20/2018
ms.openlocfilehash: f9f9b112a083615f9cf1d74d7cf81f5ca4f7901f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019240"
---
# <a name="httpclient-stack-and-ssltls-implementation-selector-for-android"></a>適用于 Android 的 HttpClient 堆疊和 SSL/TLS 執行選取器

HttpClient 堆疊和 SSL/TLS 實作為選取器會決定您的 Xamarin Android 應用程式將使用的 HttpClient 和 SSL/TLS 執行。

專案必須參考**系統 .net. Http**元件。

> [!WARNING]
> **2018 年4月，** 由於增加的安全性需求（包括 PCI 合規性），主要雲端提供者和網頁伺服器預期會停止支援超過1.2 的 TLS 版本。 在舊版中建立的 Xamarin 專案 Visual Studio 預設為使用舊版的 TLS。
>
> 為了確保您的應用程式能繼續使用這些伺服器和服務，**您應該使用如下所示的 `Android HttpClient` 和 `Native TLS 1.2` 設定來更新 Xamarin 專案，然後重新建立應用程式並重新部署**至使用者。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[HttpClient] 設定位於 [**專案選項] > [Android 選項**]，然後按一下 [ **Advanced Options** ] 按鈕。

以下是 TLS 1.2 支援的建議設定：

[![Visual Studio Android 選項](http-stack-images/android-win-sml.png)](http-stack-images/android-win.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

HttpClient 設定 位於 專案選項 中， **> 組建 > Android 組建**設定，然後按一下 **一般** 索引標籤。

以下是 TLS 1.2 支援的建議設定：

[![Visual Studio for Mac Android 選項](http-stack-images/android-mac-sml.png)](http-stack-images/android-mac.png#lightbox)

-----

## <a name="alternative-configuration-options"></a>替代設定選項

### <a name="androidclienthandler"></a>AndroidClientHandler

AndroidClientHandler 是新的處理常式，會委派至原生 JAVA/OS 程式碼，而不是在 managed 程式碼中執行所有專案。
**這是建議的選項。**

#### <a name="pros"></a>展開

- 使用原生 API 以獲得更佳的效能和較小的可執行檔案大小。
- 支援最新的標準，例如 TLS 1.2。

#### <a name="cons"></a>各有利弊

- 需要 Android 4.1 或更新版本。
- 有些 HttpClient 功能/選項無法使用。

### <a name="managed-httpclienthandler"></a>受控（HttpClientHandler）

Managed 處理常式是完全受控的 HttpClient 處理常式，已隨附于先前的 Xamarin. Android 版本。

#### <a name="pros"></a>展開

- 它是與 MS .NET 和舊版 Xamarin 最相容的（功能）。

#### <a name="cons"></a>各有利弊

- 它不會與 OS 完全整合（例如， 僅限於 TLS 1.0）。
- 速度通常會很慢（例如 加密），而不是原生 API。
- 這需要更多受控碼，以建立更大的應用程式。

### <a name="choosing-a-handler"></a>選擇處理常式

`AndroidClientHandler` 和 `HttpClientHandler` 之間的選擇取決於您的應用程式需求。 建議使用 `AndroidClientHandler`，以取得最新的安全性支援，例如

- 您需要 TLS 1.2 + 支援。
- 您的應用程式以 Android 4.1 （API 16）或更新版本為目標。
- 您需要 `HttpClient`的 TLS 1.2 + 支援。
- 您不需要 `WebClient`的 TLS 1.2 + 支援。

如果您需要 TLS 1.2 + 支援，但必須支援早于 Android 4.1 的 Android 版本，`HttpClientHandler` 是不錯的選擇。 如果您需要 `WebClient`的 TLS 1.2 + 支援，這也是不錯的選擇。

從 Xamarin 8.3 開始，`HttpClientHandler` 預設為具有乏味的 SSL （`btls`）做為基礎 TLS 提供者。 乏味的 SSL TLS 提供者提供下列優點：

- 它支援 TLS 1.2 +。
- 它支援所有 Android 版本。
- 它提供 `HttpClient` 和 `WebClient`的 TLS 1.2 + 支援。

使用乏味 SSL 作為基礎 TLS 提供者的缺點是，它可以增加所產生之 APK 的大小（每個支援的 ABI 增加大約1MB 的額外 APK 大小）。

從 Xamarin. Android 8.3 開始，預設 TLS 提供者為「鏜孔 SSL」（`btls`）。 如果您不想要使用乏味的 SSL，可以將 `$(AndroidTlsProvider)` 屬性設為 `legacy` （如需設定組建屬性的詳細資訊，請參閱[建立](~/android/deploy-test/building-apps/build-process.md)程式），以還原為歷程記錄受控 ssl 的執行。

### <a name="programatically-using-androidclienthandler"></a>以程式設計方式使用 `AndroidClientHandler`

`Xamarin.Android.Net.AndroidClientHandler` 是專為 Xamarin 提供的 `HttpMessageHandler` 實作為。
這個類別的實例將會使用原生 `java.net.URLConnection` 的實作為所有 HTTP 連接。 理論上，這會增加 HTTP 效能和較小的 APK 大小。

此程式碼片段是如何明確地為 `HttpClient` 類別的單一實例進行的範例：

```csharp
// Android 4.1 or higher, Xamarin.Android 6.1 or higher
HttpClient client = new HttpClient(new Xamarin.Android.Net.AndroidClientHandler ());
```

> [!NOTE]
> 基礎 Android 裝置必須支援 TLS 1.2 （ie）。Android 4.1 和更新版本）。 請注意，TLS 1.2 的正式支援是在 Android 5.0 + 中。 不過，有些裝置支援 Android 4.1 + 中的 TLS 1.2。

## <a name="ssltls-implementation-build-option"></a>SSL/TLS 執行組建選項

此專案選項可控制所有 web 要求將會使用哪些基礎 TLS 程式庫，`HttpClient` 和 `WebRequest`。 根據預設，會選取 TLS 1.2：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[Visual Studio 中的![TLS/SSL 執行] 下拉式方塊](http-stack-images/tls06-vs.png)](http-stack-images/tls05-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[Visual Studio for Mac 中的![TLS/SSL 執行] 下拉式方塊](http-stack-images/tls06-xs.png)](http-stack-images/tls05-xs.png#lightbox)

-----

例如:

```csharp
var client = new HttpClient();
```

如果 HttpClient 的執行設定為 [ **Managed** ]，而 TLS 實作為設定為 [**原生 TLS 1.2 +** ]，則 `client` 物件會自動使用受控 `HttpClientHandler` 和 Tls 1.2 （由 BoringSSL 程式庫提供）作為其 HTTP發送.

不過，如果**HttpClient 的執行**設定為 `AndroidHttpClient`，則所有 `HttpClient` 物件都會使用基礎 JAVA 類別 `java.net.URLConnection`，而且不會受到**TLS/SSL 實**值的影響。 `WebRequest` 物件會使用 BoringSSL 程式庫。

## <a name="other-ways-to-control-ssltls-configuration"></a>控制 SSL/TLS 設定的其他方式

有三種方式可讓 Xamarin Android 應用程式控制 TLS 設定：

1. 在 [專案選項] 中，選取 [HttpClient] 和 [預設 TLS 程式庫]。
2. 以程式設計方式使用 `Xamarin.Android.Net.AndroidClientHandler`。
3. 宣告環境變數（選擇性）。

在這三種選擇中，建議的方法是使用 [Xamarin] 專案選項來宣告整個應用程式的預設 `HttpMessageHandler` 和 TLS。 然後，如有必要，以程式設計方式具現化 `Xamarin.Android.Net.AndroidClientHandler` 物件。 上述選項會加以說明。

&ndash; 使用環境變數的第三個選項 &ndash; 如下所述。

### <a name="declare-environment-variables"></a>宣告環境變數

有兩個與在 Xamarin 中使用 TLS 相關的環境變數：

- `XA_HTTP_CLIENT_HANDLER_TYPE` &ndash; 此環境變數會宣告應用程式將使用的預設 `HttpMessageHandler`。 例如:

    ```csharp
    XA_HTTP_CLIENT_HANDLER_TYPE=Xamarin.Android.Net.AndroidClientHandler
    ```

- `XA_TLS_PROVIDER` &ndash; 此環境變數會宣告將使用哪個 TLS 程式庫，不論是 `btls`、`legacy`或 `default` （這與省略此變數相同）：

    ```csharp
    XA_TLS_PROVIDER=btls
    ```

此環境變數是藉由將_環境_檔案加入至專案來設定。 環境檔案是 Unix 格式的純文字檔案，其組建動作為**AndroidEnvironment**：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Visual Studio 中 AndroidEnvironment 組建動作的螢幕擷取畫面。](http-stack-images/tls03-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![Visual Studio for Mac 中 AndroidEnvironment 組建動作的螢幕擷取畫面。](http-stack-images/tls03-xs.png)

-----

如需有關環境變數和 Xamarin 的詳細資訊，請參閱 [Xamarin 環境](~/android/deploy-test/environment.md)指南。

## <a name="related-links"></a>相關連結

- [傳輸層安全性 (TLS)](~/cross-platform/app-fundamentals/transport-layer-security.md)
