---
title: HttpClient 堆疊和 iOS/macOS SSL/TLS 實作選取器
description: HttpClient 堆疊與 SSL/TLS 實作的選取器，決定將 Xamarin iOS、 tvOS 或 macOS 應用程式所使用的 HttpClient 和 SSL/TLS 實作。
ms.prod: xamarin
ms.assetid: 12101297-BB04-4410-85F0-A0D41B7E6591
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 04/20/2018
ms.openlocfilehash: a1cf4340a2d9e26490f0e605f47ca43a14ae4c72
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2018
---
# <a name="httpclient-stack-and-ssltls-implementation-selector-for-iosmacos"></a>HttpClient 堆疊和 iOS/macOS SSL/TLS 實作選取器

**HttpClient 實作的選取器**Xamarin.iOS，Xamarin.tvOS 和 Xamarin.Mac 控制其`HttpClient`使用實作。 您可以切換到使用原生 iOS、 tvOS 或 macOS 傳輸的實作 (`NSUrlSession`或`CFNetwork`，取決於作業系統)。 其優點是 TLS 1.2 支援的小型二進位檔，並更快下載;缺點是它需要事件迴圈執行的非同步作業執行。

專案必須參考**System.Net.Http**組件。

> [!WARNING]
> **年 4 月，2018年**– 因為更高的安全性需求，包括 PCI 合規性，主要雲端提供者和網頁伺服器應停止支援 TLS 1.2 比舊的版本。  在舊版的 Visual Studio 預設使用 TLS 的舊版本中建立的 Xamarin 專案。
>
> 為了確保您的應用程式繼續使用這些伺服器與服務，**您應該更新您的 Xamarin 專案與`NSUrlSession`設定如下所示，然後重新建置並重新部署您的應用程式**給您的使用者。

<a name="Selecting-a-HttpClient-Stack" />

### <a name="selecting-a-httpclient-stack"></a>選取 HttpClient 堆疊

若要調整`HttpClient`正由您的應用程式：

1. 按兩下**專案名稱**中**方案總管] 中**以開啟 [專案選項。
2. 切換至**建置**專案的設定 (例如， **iOS 建置**Xamarin.iOS 應用程式)。
3. 從**HttpClient 實作**下拉式清單中，選取`HttpClient`輸入做為下列其中之一： **NSUrlSession** （建議）， **CFNetwork**，或**管理**。

[![從受管理、 CFNetwork 或 NSUrlSession 選擇 HttpClient 實作](http-stack-images/http-xs-sml.png)](http-stack-images/http-xs.png#lightbox)

> [!TIP]
> 如需 TLS 1.2 支援`NSUrlSession`建議選項。

<a name="NSUrlSession" />

### <a name="nsurlsession"></a>NSUrlSession

`NSURLSession`-根據處理常式根據原生`NSURLSession`用於 iOS 7 及更新版本的架構。 
**這是建議的設定。**

#### <a name="pros"></a>專業人員

- 它會使用原生應用程式開發介面進行更好的效能和較小的可執行檔大小。
- 支援的最新的標準，例如 TLS 1.2。

#### <a name="cons"></a>缺點

- 需要 iOS 7 或更新版本。
- 某些`HttpClient`功能/選項都無法使用。

<a name="CFNetwork" />

### <a name="cfnetwork"></a>CFNetwork

`CFNetwork`-根據處理常式根據原生`CFNetwork`用於 iOS 6 和更新版本的架構。

#### <a name="pros"></a>專業人員

- 它會使用原生應用程式開發介面進行更好的效能和較小的可執行檔大小。
- 較新的標準，例如 TLS 1.2 的支援。

#### <a name="cons"></a>缺點

- 需要 iOS 6 或更新版本。
- WatchOS 上無法使用。
- 無法使用某些 HttpClient/選項功能。

<a name="Managed" />

### <a name="managed"></a>Managed

Managed 處理常式是隨附的 Xamarin 的上一個版本的管理完善 HttpClient 處理常式。

#### <a name="pros"></a>專業人員

- 它有最適合的功能集 Microsoft.NET 和舊版 Xamarin。

#### <a name="cons"></a>缺點

- 它無法與 Apple 作業系統完全整合，且僅限於 TLS 1.0。 它可能無法連線至安全的 web 伺服器，或在未來的雲端服務。
- 它通常比慢很多之類的加密在原生 Api。
- 它需要更 managed 程式碼，以便建立更大的應用程式可散布。

### <a name="programmatically-setting-the-httpmessagehandler"></a>以程式設計方式設定 HttpMessageHandler

除了上述的整個專案的組態，您可以也具現化`HttpClient`，以及將所需`HttpMessageHandler`透過建構函式，如下列程式碼片段所示：

```csharp
// This will use the default message handler for the application; as
// set in the Project Options for the project.
HttpClient client = new HttpClient();

// This will create an HttpClient that explicitly uses the CFNetworkHandler
HttpClient client = new HttpClient(new CFNetworkHandler());

// This will create an HttpClient that explicitly uses NSUrlSessionHandler
HttpClient client = new HttpClient(new NSUrlSessionHandler());
```

這樣做可使用不同`HttpMessageHandler`中的宣告從**專案選項**對話方塊。

<a name="New-SSL-TLS-implementation-build-option" />
<a name="Selecting-a-SSL-TLS-implementation" />
<a name="Apple-TLS" />

## <a name="ssltls-implementation"></a>SSL/TLS 實作

SSL （安全通訊端層） 和 TLS （傳輸層安全性），後續提供支援 HTTP 和其他網路連線，透過`System.Net.Security.SslStream`。 Xamarin.iOS、 Xamarin.tvOS 或 Xamarin.Mac 的`System.Net.Security.SslStream`實作會呼叫 Apple 的原生 SSL/TLS 實作，而不要使用 單聲道所提供的 managed 的實作。 Apple 的原生實作支援 TLS 1.2。

<a name="App-Transport-Security" />

## <a name="app-transport-security"></a>應用程式的傳輸安全性

Apple 的_應用程式的傳輸安全性_(ATS) 會強制執行 （例如應用程式的後端伺服器） 的網際網路資源與您的應用程式之間的安全連線。 AT 可確保所有的網際網路通訊符合要保障連線安全的最佳作法，因此可以防止意外洩漏機密資訊直接透過您的應用程式或它正在使用的程式庫。

因為在 iOS 9、 tvOS 9 和 OS X 10.11 (El Capitan) 建置的應用程式預設會啟用 AT 和更新版本中，所有連線使用`NSUrlConnection`，`CFUrl`或`NSUrlSession`都會受到 AT 安全性需求。 如果您的連線不符合這些需求，它們將會失敗並發生例外狀況。

根據您選取的 HttpClient 堆疊及 SSL/TLS 實作，您可能需要修改您的應用程式以 AT 正常運作。

若要了解有關 AT 的詳細資訊，請參閱我們[應用程式的傳輸安全性指南](~/ios/app-fundamentals/ats.md)。

## <a name="known-issues"></a>已知問題

本節將討論 TLS 支援 Xamarin.iOS 中已知的的問題。

### <a name="project-failed-to-load-with-error-requested-value-appletls-wasnt-found"></a>無法載入，發生錯誤 「 找不到 AppleTLS 要求值 」 的專案

Xamarin.iOS 9.8 導入了一些新的設定包含 **.csproj** Xamarin.iOS 應用程式檔案。 與舊版的 Xamarin.iOS 開啟專案時，這些變更可能會造成問題。 下列螢幕擷取畫面是可能會顯示在此案例中的錯誤訊息的範例：

![嘗試載入專案時發生錯誤的螢幕擷取畫面要求找不到的值舊版](http-stack-images/tlserror-xs.png)

此錯誤的起因是引進的`MtouchTlsProvider`設 Xamarin.iOS 9.8 中的專案檔。 如果不是可更新至 Xamarin.iOS 9.8 （或更新版本），因應方式是以手動方式編輯 **.csproj**檔案應用程式中，移除`MtouchTlsprovider`項目，然後儲存變更的專案檔案。

下列程式碼片段是什麼的範例`MtouchTlsProvider`設定可能看起來像內 **.csproj**檔案：

```xml
<MtouchTlsProvider>Default</MtouchTlsProvider>
```

## <a name="related-links"></a>相關連結

- [傳輸層安全性 (TLS)](~/cross-platform/app-fundamentals/transport-layer-security.md)
- [應用程式傳輸安全性](~/ios/app-fundamentals/ats.md)
