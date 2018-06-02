---
title: HttpClient 堆疊與 SSL/TLS 實作適用於 Android 的選取器
description: HttpClient 堆疊與 SSL/TLS 實作器決定 Xamarin.Android 應用程式將使用 HttpClient 和 SSL/TLS 實作。
ms.prod: xamarin
ms.assetid: D7ABAFAB-5CA2-443D-B902-2C7F3AD69CE2
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 04/20/2018
ms.openlocfilehash: 765c51346ac63a00838fec52bde87b38091e2dd9
ms.sourcegitcommit: a4c2a63ba76b839cda99e4474e7ab46fe307cd39
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2018
ms.locfileid: "34689470"
---
# <a name="httpclient-stack-and-ssltls-implementation-selector-for-android"></a>HttpClient 堆疊與 SSL/TLS 實作適用於 Android 的選取器

HttpClient 堆疊與 SSL/TLS 實作器決定 Xamarin.Android 應用程式將使用 HttpClient 和 SSL/TLS 實作。

專案必須參考**System.Net.Http**組件。

> [!WARNING]
> **年 4 月，2018年**– 因為更高的安全性需求，包括 PCI 合規性，主要雲端提供者和網頁伺服器應停止支援 TLS 1.2 比舊的版本。  在舊版的 Visual Studio 預設使用 TLS 的舊版本中建立的 Xamarin 專案。
>
> 為了確保您的應用程式繼續使用這些伺服器與服務，**您應該更新您的 Xamarin 專案與`Android HttpClient`和`Native TLS 1.2`設定底下所示，然後重新建置並重新部署您的應用程式**到您使用者。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Xamarin.Android HttpClient 組態處於**專案選項 > Android 選項**，然後按一下 [**進階選項**] 按鈕。

以下是建議的設定，如需 TLS 1.2 支援：

[![Visual Studio Android 選項](http-stack-images/android-win-sml.png)](http-stack-images/android-win.png#lightbox)


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

Xamarin.Android HttpClient 組態處於**專案選項 > 建置 > Android 建置**設定並按一下**一般** 索引標籤。

以下是建議的設定，如需 TLS 1.2 支援：

[![Visual Studio for Mac Android 選項](http-stack-images/android-mac-sml.png)](http-stack-images/android-mac.png#lightbox)

-----

## <a name="alternative-configuration-options"></a>另外一種組態選項

### <a name="androidclienthandler"></a>AndroidClientHandler

AndroidClientHandler 是新處理常式委派給原生 Java/OS 的程式碼，而非 managed 程式碼中實作的所有項目。
**這是建議的選項。**

#### <a name="pros"></a>專業人員

- 使用原生 API 來取得較佳的效能和較小的可執行檔大小。
- 例如，支援的最新的標準。 TLS 1.2。

#### <a name="cons"></a>缺點

- 需要 Android 5.0 或更新版本。
- 無法使用某些 HttpClient/選項功能。

### <a name="managed-httpclienthandler"></a>Managed (HttpClientHandler)

受管理的處理常式是隨附舊版 Xamarin.Android 完全受管理的 HttpClient 處理常式。

#### <a name="pros"></a>專業人員

- 它是最相容 （功能） 與 MS.NET 和舊版 Xamarin。

#### <a name="cons"></a>缺點

- 它不完全整合在一起的作業系統 （例如。 限制為 TLS 1.0）。
- 它是通常變得很慢 （例如。 加密） 比原生 API。
- 它需要更 managed 程式碼，建立較大的應用程式。



### <a name="choosing-a-handler"></a>選擇處理常式

選擇`AndroidClientHandler`和`HttpClientHandler`應用程式的需求而定。 `AndroidClientHandler` 建議的最新的安全性支援，例如。

-   您需要支援 TLS 1.2 +。
-   您的應用程式的目標 Android 5.0 (API 21) 或更新版本。
-   您需要 TLS 1.2 + 支援`HttpClient`。
-   您不需要支援 TLS 1.2 + `WebClient`。

`HttpClientHandler` 如果您需要 TLS 1.2 + 是不錯的選擇支援，但必須支援早於 Android 5.0 的 Android 版本。 它是也不錯的選擇，如果您需要 TLS 1.2 + 支援`WebClient`。

開頭為 Xamarin.Android 8.3`HttpClientHandler`無聊 ssl 的預設值 (`btls`) 為基礎的 TLS 提供者。 無聊 SSL TLS 提供者會提供下列優點：

-   它支援 TLS 1.2 +。
-   它支援所有的 Android 版本。
-   它提供兩個支援 TLS 1.2 +`HttpClient`和`WebClient`。

做為從基礎 TLS 提供者使用無聊 SSL 的缺點是它可以增加產生的 APK （新增的每個支援的 ABI 額外 APK 大小約 1 MB） 的大小。

從開始 Xamarin.Android 8.3，預設 TLS 提供者是無聊 SSL (`btls`)。 如果您不想要使用無聊 SSL，您可以藉由設定還原至受管理的歷程記錄 SSL 實作`$(AndroidTlsProvider)`屬性`legacy`(如需設定建置屬性的詳細資訊，請參閱[建置流程](~/android/deploy-test/building-apps/build-process.md))。


### <a name="programatically-using-androidclienthandler"></a>以程式設計方式使用 `AndroidClientHandler`

`Xamarin.Android.Net.AndroidClientHandler`是`HttpMessageHandler`Xamarin.Android 特別針對實作。
這個類別的執行個體將會使用原生`java.net.URLConnection`實作所有的 HTTP 連接。 理論上來說，這會提供 HTTP 效能和較小的 APK 大小增加。

此程式碼片段是如何明確地為單一執行個體的範例`HttpClient`類別：

```csharp
// Android 5.0 or higher, Xamarin.Android 6.1 or higher
HttpClient client = new HttpClient(new Xamarin.Android.Net.AndroidClientHandler ());
```

> [!NOTE]
> 基礎的 Android 裝置必須支援 TLS 1.2 （即Android 5.0 及更新版本)


## <a name="ssltls-implementation-build-option"></a>SSL/TLS 實作建置選項

此專案選項會控制哪些基礎 TLS 程式庫將由所有 web 要求，同時`HttpClient`和`WebRequest`。 依預設，會選取 TLS 1.2:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![TLS/SSL 實作 Visual Studio 中的下拉式方塊](http-stack-images/tls06-vs.png)](http-stack-images/tls05-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![TLS/SSL 實作適用於 Mac 的 Visual Studio 中的下拉式方塊](http-stack-images/tls06-xs.png)](http-stack-images/tls05-xs.png#lightbox)

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

三個選項中，建議的方法是使用來宣告預設的 Xamarin.Android 專案選項`HttpMessageHandler`和整個應用程式的 TLS。 然後，如有必要，以程式設計方式具現化`Xamarin.Android.Net.AndroidClientHandler`物件。 這些選項是上面所述。

第三個選項&ndash;使用環境變數&ndash;解釋如下。

### <a name="declare-environment-variables"></a>環境變數的宣告

有兩個使用中 Xamarin.Android TLS 相關的環境變數：

- `XA_HTTP_CLIENT_HANDLER_TYPE` &ndash; 這個環境變數會宣告預設`HttpMessageHandler`應用程式會使用。 例如: 

    ```csharp
    XA_HTTP_CLIENT_HANDLER_TYPE=Xamarin.Android.Net.AndroidClientHandler
    ```

- `XA_TLS_PROVIDER` &ndash; 這個環境變數會在哪一個 TLS 庫將會使用，請宣告`btls`， `legacy`，或`default`（此為省略此變數一樣）：

    ```csharp
    XA_TLS_PROVIDER=btls
    ```

此設定環境變數是藉由新增_環境檔案_至專案。 環境檔案是 Unix 格式化純文字檔案的建置動作**AndroidEnvironment**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![在 Visual Studio AndroidEnvironment 建置動作的螢幕擷取畫面。](http-stack-images/tls03-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![螢幕擷取畫面的 AndroidEnvironment 建置動作，在 Visual Studio for mac。](http-stack-images/tls03-xs.png)

-----

請參閱[Xamarin.Android 環境](~/android/deploy-test/environment.md)如需詳細資訊的環境變數和 Xamarin.Android 的指南。


## <a name="related-links"></a>相關連結

- [傳輸層安全性 (TLS)](~/cross-platform/app-fundamentals/transport-layer-security.md)
