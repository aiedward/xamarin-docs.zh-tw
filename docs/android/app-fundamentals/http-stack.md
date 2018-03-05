---
title: "HttpClient 堆疊與 SSL/TLS 實作適用於 Android 的選取器"
description: "HttpClient 堆疊與 SSL/TLS 實作器決定 Xamarin.Android 應用程式將使用 HttpClient 和 SSL/TLS 實作。"
ms.topic: article
ms.prod: xamarin
ms.assetid: D7ABAFAB-5CA2-443D-B902-2C7F3AD69CE2
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: bcb6f033c7fad76a17a7a5aa82f48a76b1ae501d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="httpclient-stack-and-ssltls-implementation-selector-for-android"></a>HttpClient 堆疊與 SSL/TLS 實作適用於 Android 的選取器

_HttpClient 堆疊與 SSL/TLS 實作器決定 Xamarin.Android 應用程式將使用 HttpClient 和 SSL/TLS 實作。_

## <a name="overview"></a>總覽

Xamarin.Android 提供會控制 TLS 設定的 Android 應用程式的兩個下拉式方塊。 一個下拉式方塊將會找出哪些`HttpMessageHandler`具現化時，將會使用`HttpClient`物件，而其他識別哪些 TLS 實作將會使用 web 要求。

> [!NOTE]
> **注意：**專案必須參考**System.Net.Http**組件。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

HttpClient 堆疊的設定存在於 Xamarin.Android 專案的專案選項。 按一下**Android 選項**索引標籤，然後再按一下**進階選項** 按鈕。 這會顯示**進階 Android 選項**對話方塊具有兩個下拉式方塊，一個用於 HttpClient 實作，一個用於 SSL/TLS 實作：


[ ![Visual Studio Android 選項](http-stack-images/tls07-vs-sml.png)](http-stack-images/tls07-vs.png)


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

HttpClient 堆疊的設定存在於 Xamarin.Android 專案專案選項。 按一下**建置 > Android 建置**設定並按一下**一般** 索引標籤：

[ ![Visual Studio for Mac Android 選項](http-stack-images/tls07-xs-sml.png)](http-stack-images/tls07-xs.png)


-----

## <a name="httpclient-stack-selector"></a>HttpClient 堆疊選取器

此專案選項會控制其`HttpMessageHandler`實作會在每次`HttpClient`物件具現化。 根據預設，這是 managed `HttpClientHandler`。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[ ![在 Visual Studio 中 android HttpClient 實作下拉式方塊](http-stack-images/tls04-vs-sml.png)](http-stack-images/tls04-vs.png) 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![適用於 Mac 的 Visual Studio 中 android HttpClient 實作下拉式方塊](http-stack-images/tls04-xs.png )

-----

### <a name="managed-httpclienthandler"></a>Managed (HttpClientHandler)

受管理的處理常式是隨附舊版 Xamarin.Android 完全受管理的 HttpClient 處理常式。

#### <a name="pros"></a>優點：

- 它是最相容 （功能） 與 MS.NET 和舊版 Xamarin。

#### <a name="cons"></a>缺點：

- 它不完全整合在一起的作業系統 （例如。 限制為 TLS 1.0）。
- 它是通常變得很慢 （例如。 加密） 比原生 API。
- 它需要更 managed 程式碼，建立較大的應用程式。

### <a name="androidclienthandler"></a>AndroidClientHandler

AndroidClientHandler 是新處理常式委派給原生 Java/OS 的程式碼，而非 managed 程式碼中實作的所有項目。

#### <a name="pros"></a>優點：

- 使用原生 API 來取得較佳的效能和較小的可執行檔大小。
- 例如，支援的最新的標準。 TLS 1.2。

#### <a name="cons"></a>缺點：

- 需要 Android 5.0 或更新版本。
- 無法使用某些 HttpClient/選項功能。


### <a name="programatically-using-androidclienthandler"></a>以程式設計方式使用 `AndroidClientHandler`

`Xamarin.Android.Net.AndroidClientHandler`是`HttpMessageHandler`Xamarin.Android 特別針對實作。 這個類別的執行個體將會使用原生`java.net.URLConnection`實作所有的 HTTP 連接。 理論上來說，這會提供 HTTP 效能和較小的 APK 大小增加。

此程式碼片段是如何明確地為單一執行個體的範例`HttpClient`類別：

```csharp
// Android 5.0 or higher, Xamarin.Android 6.1 or higher
HttpClient client = new HttpClient(new Xamarin.Android.Net.AndroidClientHandler ());
```

> [!NOTE]
>  **請注意**： 基礎的 Android 裝置必須支援 TLS 1.2 （即Android 5.0 及更新版本)


## <a name="ssltls-implementation-build-option"></a>SSL/TLS 實作建置選項

此專案選項會控制哪些基礎 TLS 程式庫將由所有 web 要求，同時`HttpClient`和`WebRequest`。 依預設，會選取 TLS 1.2:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[ ![TLS/SSL 實作 Visual Studio 中的下拉式方塊](http-stack-images/tls06-vs.png)](http-stack-images/tls05-vs.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[ ![TLS/SSL 實作適用於 Mac 的 Visual Studio 中的下拉式方塊](http-stack-images/tls06-xs.png)](http-stack-images/tls05-xs.png)

-----

例如: 

```csharp
var client = new HttpClient();
```

如果 HttpClient 實作設為**Managed**和 TLS 實作已設為**原生的 TLS 1.2 +**，然後在`client`物件會自動使用 managed`HttpClientHandler`和TLS 1.2 （BoringSSL 程式庫所提供） 的 HTTP 要求。

不過，如果**HttpClient 實作**設`AndroidHttpClient`，然後所有`HttpClient`物件會使用基礎 Java 類別`java.net.URLConnection`和會受到**TLS/SSL 實作**值。 `WebRequest` 物件會使用 BoringSSL 程式庫。

## <a name="other-ways-to-control-ssltls-configuration"></a>其他的方式來控制 SSL/TLS 設定

有三種方式 Xamarin.Android 應用程式可以控制 TLS 設定：

1. 專案選項中選取 HttpClient 實作和預設 TLS 的文件庫。
2. 以程式設計方式使用`Xamarin.Android.Net.AndroidClientHandler`。
3. 環境變數宣告 （選擇性）。

三個選項中，建議的方法是使用來宣告預設的 Xamarin.Android 專案選項`HttpMessageHandler`和整個應用程式的 TLS。 然後，如有必要，以程式設計方式具現化`Xamarin.Android.Net.AndroidClientHandler`物件。
這些選項是上面所述。

第三個選項&ndash;使用環境變數&ndash;解釋如下。

### <a name="declare-environment-variables"></a>環境變數的宣告

有兩個使用中 Xamarin.Android TLS 相關的環境變數：

-   `XA_HTTP_CLIENT_HANDLER_TYPE` &ndash; 這個環境變數會宣告預設`HttpMessageHandler`應用程式會使用。 例如: 

    ```csharp
    XA_HTTP_CLIENT_HANDLER_TYPE=Xamarin.Android.Net.AndroidClientHandler
    ```

-   `XA_TLS_PROVIDER` &ndash; 這個環境變數會在哪一個 TLS 庫將會使用，請宣告`btls`， `legacy`，或`default`（此為省略此變數一樣）：

    ```csharp
    XA_TLS_PROVIDER=btls
    ```

此設定環境變數是藉由新增_環境檔案_至專案。 環境檔案是 Unix 格式化純文字檔案的建置動作**AndroidEnvironment**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![在 Visual Studio AndroidEnvironment 建置動作的螢幕擷取畫面。](http-stack-images/tls03-vs.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![螢幕擷取畫面的 AndroidEnvironment 建置動作，在 Visual Studio for mac。](http-stack-images/tls03-xs.png)

-----

請參閱[Xamarin.Android 環境](~/android/deploy-test/environment.md)如需詳細資訊的環境變數和 Xamarin.Android 的指南。


## <a name="related-links"></a>相關連結

- [傳輸層安全性 (TLS)](~/cross-platform/app-fundamentals/transport-layer-security.md)
