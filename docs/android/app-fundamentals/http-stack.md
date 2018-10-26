---
title: HttpClient 堆疊和適用於 Android 的 SSL/TLS 實作選擇器
description: HttpClient 堆疊和 SSL/TLS 實作選擇器會決定將由您的 Xamarin.Android 應用程式的 HttpClient 和 SSL/TLS 實作。
ms.prod: xamarin
ms.assetid: D7ABAFAB-5CA2-443D-B902-2C7F3AD69CE2
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/20/2018
ms.openlocfilehash: 47c9ddf3f1a61b0ec7e2a8ed993ad665267993fd
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114271"
---
# <a name="httpclient-stack-and-ssltls-implementation-selector-for-android"></a>HttpClient 堆疊和適用於 Android 的 SSL/TLS 實作選擇器

HttpClient 堆疊和 SSL/TLS 實作選擇器會決定將由您的 Xamarin.Android 應用程式的 HttpClient 和 SSL/TLS 實作。

專案必須參考**System.Net.Http**組件。

> [!WARNING]
> **2018 年 4 月，** – 因為更高的安全性需求，包括 PCI 合規性，主要雲端提供者和 web 伺服器會停止支援 TLS 1.2 與較舊的版本。  在舊版的 Visual Studio 預設使用 TLS 的較舊版本中建立的 Xamarin 專案。
>
> 為了確保您的應用程式可以繼續使用這些伺服器或服務，**您應該更新您的 Xamarin 專案，與`Android HttpClient`並`Native TLS 1.2`設定如下所示，然後重新建置，並重新部署您的應用程式**到您使用者。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Xamarin.Android HttpClient 組態處於**專案選項 > Android 選項**，然後按一下**進階選項** 按鈕。

以下是建議的設定，如需 TLS 1.2 支援：

[![Visual Studio Android 選項](http-stack-images/android-win-sml.png)](http-stack-images/android-win.png#lightbox)


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

Xamarin.Android HttpClient 組態處於**專案選項 > 建置 > Android 組建**設定，然後按一下 [**一般**] 索引標籤。

以下是建議的設定，如需 TLS 1.2 支援：

[![Visual Studio for Mac 的 Android 選項](http-stack-images/android-mac-sml.png)](http-stack-images/android-mac.png#lightbox)

-----

## <a name="alternative-configuration-options"></a>另外一種組態選項

### <a name="androidclienthandler"></a>AndroidClientHandler

AndroidClientHandler 是新的處理常式委派給原生 Java/OS 的程式碼，而非 managed 程式碼中實作的所有項目。
**這是建議的選項。**

#### <a name="pros"></a>專業人員

- 使用原生 API 的更佳的效能和較小的可執行檔大小。
- 例如，支援的最新的標準。 TLS 1.2。

#### <a name="cons"></a>缺點

- 需要 Android 5.0 或更新版本。
- 無法使用某些 HttpClient 功能] / [選項。

### <a name="managed-httpclienthandler"></a>受控 (HttpClientHandler)

受管理的處理常式是隨附舊版 Xamarin.Android 的完全受控的 HttpClient 處理常式。

#### <a name="pros"></a>專業人員

- 它是最相容 （功能） 與 MS.NET 和 Xamarin 上的舊版。

#### <a name="cons"></a>缺點

- 它不完全整合與作業系統 （例如。 僅限 TLS 1.0）。
- 它是通常更慢 （例如。 加密） 比原生 API。
- 它需要更 managed 程式碼，建立較大的應用程式。



### <a name="choosing-a-handler"></a>選擇處理常式

之間的抉擇`AndroidClientHandler`和`HttpClientHandler`取決於您的應用程式的需求。 `AndroidClientHandler` 建議的最新的安全性支援，例如。

-   您需要的 TLS 1.2 + 支援。
-   您的應用程式的目標 Android 5.0 (API 21) 或更新版本。
-   您需要 TLS 1.2 + 支援`HttpClient`。
-   您不需要支援 TLS 1.2 + `WebClient`。

`HttpClientHandler` 是不錯的選擇，如果您需要 TLS 1.2 + 支援，但必須支援 Android 5.0 之前的 Android 版本。 它是也不錯的選擇，如果您需要 TLS 1.2 + 支援`WebClient`。

從 Xamarin.Android 8.3 `HttpClientHandler` Boring SSL 的預設值 (`btls`) 為基礎的 TLS 提供者。 無聊 SSL TLS 提供者會提供下列優點：

-   它支援 TLS 1.2 +。
-   它支援所有的 Android 版本。
-   它提供的 TLS 1.2 + 支援兩個`HttpClient`和`WebClient`。

做為基礎的 TLS 提供者使用 Boring SSL 的缺點是它可以增加 （新增的其他每個支援的 ABI 的 APK 大小約 1 MB） 產生的 APK 的大小。

從 Xamarin.Android 8.3 的預設 TLS 提供者已 Boring SSL (`btls`)。 如果您不要使用 Boring SSL，您可以藉由設定還原為歷程記錄的受控 SSL 實作`$(AndroidTlsProvider)`屬性，以`legacy`(如需有關設定建置屬性的詳細資訊，請參閱[建置程序](~/android/deploy-test/building-apps/build-process.md))。


### <a name="programatically-using-androidclienthandler"></a>以程式設計方式使用 `AndroidClientHandler`

`Xamarin.Android.Net.AndroidClientHandler`是`HttpMessageHandler`特別針對 Xamarin.Android 的實作。
此類別的執行個體將會使用原生`java.net.URLConnection`實作適用於所有的 HTTP 連線。 理論上來說，這會提供 HTTP 效能和較小的 APK 大小增加。

此程式碼片段是如何明確的單一執行個體的範例`HttpClient`類別：

```csharp
// Android 5.0 or higher, Xamarin.Android 6.1 or higher
HttpClient client = new HttpClient(new Xamarin.Android.Net.AndroidClientHandler ());
```

> [!NOTE]
> 基礎 Android 裝置必須支援 TLS 1.2 （即Android 5.0 及更新版本)


## <a name="ssltls-implementation-build-option"></a>SSL/TLS 實作建置選項

此專案選項可讓您控制哪些基礎 TLS 程式庫將供所有的 web 要求，同時`HttpClient`和`WebRequest`。 根據預設，已選取 TLS 1.2:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![在 Visual Studio 中的 TLS/SSL 實作下拉式方塊](http-stack-images/tls06-vs.png)](http-stack-images/tls05-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![在 Visual Studio for Mac 的 TLS/SSL 實作下拉式方塊](http-stack-images/tls06-xs.png)](http-stack-images/tls05-xs.png#lightbox)

-----

例如: 

```csharp
var client = new HttpClient();
```

如果 HttpClient 實作設為**受控**且 TLS 實作已設定為**原生 TLS 1.2 +**，然後在`client`物件會自動使用 managed`HttpClientHandler`和TLS 1.2 （BoringSSL 程式庫所提供） 針對其 HTTP 要求。

不過，如果**HttpClient 實作**設為`AndroidHttpClient`，然後所有`HttpClient`物件會使用基礎 Java 類別`java.net.URLConnection`並不會受到影響的**TLS/SSL 實作**值。 `WebRequest` 物件會使用 BoringSSL 程式庫。

## <a name="other-ways-to-control-ssltls-configuration"></a>若要控制 SSL/TLS 設定的其他方式

有三種方式，Xamarin.Android 應用程式可以控制 TLS 設定：

1. 在 專案選項中選取 HttpClient 實作和預設 TLS 的文件庫。
2. 以程式設計方式使用`Xamarin.Android.Net.AndroidClientHandler`。
3. 將環境變數宣告 （選擇性）。

建議的方法是使用 Xamarin.Android 專案選項來宣告預設的三個選項中，`HttpMessageHandler`和整個應用程式的 TLS。 然後，如果有必要，請以程式設計方式具現化`Xamarin.Android.Net.AndroidClientHandler`物件。 這些選項是上面所述。

第三個選項&ndash;使用環境變數&ndash;下面會說明。

### <a name="declare-environment-variables"></a>宣告的環境變數

有兩個使用 TLS 在 Xamarin.Android 中相關的環境變數：

- `XA_HTTP_CLIENT_HANDLER_TYPE` &ndash; 這個環境變數宣告的預設`HttpMessageHandler`應用程式會使用。 例如: 

    ```csharp
    XA_HTTP_CLIENT_HANDLER_TYPE=Xamarin.Android.Net.AndroidClientHandler
    ```

- `XA_TLS_PROVIDER` &ndash; 這個環境變數會宣告的 TLS 程式庫會使用，或是`btls`， `legacy`，或`default`（這是省略此變數相同）：

    ```csharp
    XA_TLS_PROVIDER=btls
    ```

此環境變數設定，加上_環境檔案_至專案。 環境檔案是 Unix 格式純文字檔案的建置動作**AndroidEnvironment**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![在 Visual Studio 中的 AndroidEnvironment 建置動作的螢幕擷取畫面。](http-stack-images/tls03-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![螢幕擷取畫面的 AndroidEnvironment 建置動作，在 Visual Studio for mac。](http-stack-images/tls03-xs.png)

-----

請參閱[Xamarin.Android 環境](~/android/deploy-test/environment.md)如需詳細資訊的環境變數和 Xamarin.Android 的指南。


## <a name="related-links"></a>相關連結

- [傳輸層安全性 (TLS)](~/cross-platform/app-fundamentals/transport-layer-security.md)
