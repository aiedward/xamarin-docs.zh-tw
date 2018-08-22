---
title: HttpClient 和適用於 iOS/macOS 的 SSL/TLS 實作選擇器
description: HttpClient 堆疊與 SSL/TLS 實作選擇器，決定將由您的 Xamarin iOS、 tvOS 或 macOS 應用程式的 HttpClient 和 SSL/TLS 實作。
ms.prod: xamarin
ms.assetid: 12101297-BB04-4410-85F0-A0D41B7E6591
author: asb3993
ms.author: amburns
ms.date: 04/20/2018
ms.openlocfilehash: fd48c7148aadd8d156544113e2d719295294bf40
ms.sourcegitcommit: 47709db4d115d221e97f18bc8111c95723f6cb9b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2018
ms.locfileid: "40251035"
---
# <a name="httpclient-and-ssltls-implementation-selector-for-iosmacos"></a>適用於 iOS/macOS 的 HttpClient 和 SSL/TLS 實作選擇器

**HttpClient 實作選擇器**適用於 Xamarin.iOS，Xamarin.tvOS 和 Xamarin.Mac 控制其`HttpClient`若要使用的實作。 您可以切換為使用 iOS、 tvOS 或 macOS 的原生傳輸的實作 (`NSUrlSession`或`CFNetwork`，取決於作業系統)。 優點是 TLS 1.2 支援、 較小的二進位檔，並更快下載;缺點是它需要事件迴圈的執行中執行的非同步作業。

專案必須參考**System.Net.Http**組件。

> [!WARNING]
> **2018 年 4 月，** – 因為更高的安全性需求，包括 PCI 合規性，主要雲端提供者和 web 伺服器會停止支援 TLS 1.2 與較舊的版本。  在舊版的 Visual Studio 預設使用 TLS 的較舊版本中建立的 Xamarin 專案。
>
> 為了確保您的應用程式可以繼續使用這些伺服器或服務，**您應該更新您的 Xamarin 專案，與`NSUrlSession`設定如下所示，然後重新建置並重新部署您的應用程式**給您的使用者。

### <a name="selecting-an-httpclient-stack"></a>選取 HttpClient 堆疊

若要調整`HttpClient`正由您的應用程式：

1. 按兩下**專案名稱**中**方案總管] 中**以開啟 [專案選項。
2. 切換至**建置**專案設定 (例如**iOS 組建**Xamarin.iOS 應用程式)。
3. 從**HttpClient 實作**下拉式清單中，選取`HttpClient`輸入為下列其中之一： **NSUrlSession** （建議）， **CFNetwork**，或**管理**。

[![從受管理、 CFNetwork 或 NSUrlSession 選擇 HttpClient 實作](http-stack-images/http-xs-sml.png)](http-stack-images/http-xs.png#lightbox)

> [!TIP]
> 如需 TLS 1.2 支援`NSUrlSession`建議選項。

### <a name="nsurlsession"></a>NSUrlSession

`NSURLSession`-根據處理常式為基礎的原生`NSURLSession`架構，可在 iOS 7 及更新版本。 
**這是建議的設定。**

#### <a name="pros"></a>專業人員

- 它會使用原生 Api 更好的效能和較小的可執行檔大小。
- 最新的標準，例如 TLS 1.2 支援。

#### <a name="cons"></a>缺點

- 需要 iOS 7 或更新版本。
- 某些`HttpClient`功能/選項都無法使用。

### <a name="cfnetwork"></a>CFNetwork

`CFNetwork`-根據處理常式為基礎的原生`CFNetwork`framework，適用於 iOS 6 和更新版本。

#### <a name="pros"></a>專業人員

- 它會使用原生 Api 更好的效能和較小的可執行檔大小。
- 較新的標準，例如 TLS 1.2 支援。

#### <a name="cons"></a>缺點

- 需要 iOS 6 或更新版本。
- WatchOS 上無法使用。
- 無法使用某些 HttpClient 功能] / [選項。

### <a name="managed"></a>Managed

受控處理常式是隨附舊版 Xamarin 的完全受控的 HttpClient 處理常式。

#### <a name="pros"></a>專業人員

- 它有最相容的功能集 MICROSOFT.NET 和 Xamarin 上的舊版。

#### <a name="cons"></a>缺點

- 它與 Apple Os 不完全整合，並僅限於 TLS 1.0。 它可能無法連線到安全的 web 伺服器，或在未來雲端服務。
- 它通常遠低於在像加密之類的原生 Api。
- 它需要更 managed 程式碼，以建立可散發的較大的應用程式。

### <a name="programmatically-setting-the-httpmessagehandler"></a>以程式設計方式設定 HttpMessageHandler

除了上述的整個專案的組態，您可以也具現化`HttpClient`，並將所需`HttpMessageHandler`透過建構函式，這些程式碼片段所示：

```csharp
// This will use the default message handler for the application; as
// set in the Project Options for the project.
HttpClient client = new HttpClient();

// This will create an HttpClient that explicitly uses the CFNetworkHandler
HttpClient client = new HttpClient(new CFNetworkHandler());

// This will create an HttpClient that explicitly uses NSUrlSessionHandler
HttpClient client = new HttpClient(new NSUrlSessionHandler());
```

這讓您能夠使用不同`HttpMessageHandler`從項目中宣告**專案選項**對話方塊。

## <a name="ssltls-implementation"></a>SSL/TLS 實作

SSL （安全通訊端層） 和及其後繼者 TLS （傳輸層安全性），提供支援 HTTP 及其他網路連線，透過`System.Net.Security.SslStream`。 Xamarin.iOS、 Xamarin.tvOS 或 Xamarin.Mac 的`System.Net.Security.SslStream`實作會呼叫 Apple 原生 SSL/TLS 實作，而不要使用 Mono 所提供的 managed 的實作。 Apple 的原生實作支援 TLS 1.2。

> [!WARNING]
> 即將推出的 Xamarin.Mac 4.8 版本只支援 macOS 10.9 或更新版本。
> 舊版的 Xamarin.Mac 支援 macOS 10.7 或更新版本，但這些較舊的 macOS 版本缺乏足夠的 TLS 基礎結構支援 TLS 1.2。 MacOS 10.7 或 macOS 10.8 為目標，請使用 Xamarin.Mac 4.6 或更早版本。

## <a name="app-transport-security"></a>應用程式的傳輸安全性

Apple_應用程式的傳輸安全性_(ATS) 會強制執行 （例如應用程式的後端伺服器） 的網際網路資源與您的應用程式之間的安全連線。 ATS 可確保所有的網際網路通訊符合保護連線安全的最佳作法，藉此防止意外洩露機密的資訊，直接透過您的應用程式或它正在使用的程式庫。

因為在 iOS 9、 tvOS 9 和 OS X 10.11 (El Capitan) 建置的應用程式預設會啟用 ATS 和更新版本中，使用的所有連線`NSUrlConnection`，`CFUrl`或`NSUrlSession`將受限於 ATS 安全性需求。 如果您的連線不符合這些需求，它們將會失敗並發生例外狀況。

根據您的 HttpClient 堆疊與 SSL/TLS 實作選擇，您可能需要修改您的應用程式與 ATS 正確運作。

若要深入了解 ATS，請參閱我們[應用程式的傳輸安全性指南](~/ios/app-fundamentals/ats.md)。

## <a name="known-issues"></a>已知問題

本節將討論 TLS 支援在 Xamarin.iOS 中已知的的問題。

### <a name="project-failed-to-load-with-error-requested-value-appletls-wasnt-found"></a>無法載入錯誤 「 找不到 AppleTLS 要求值 」 專案

Xamarin.iOS 9.8 版導入了一些新的設定，包含 **.csproj** Xamarin.iOS 應用程式檔案。 在開啟使用舊版 Xamarin.iOS 的專案時，這些變更可能會造成問題。 下列螢幕擷取畫面是可能會顯示在此案例中的錯誤訊息的範例：

![嘗試載入專案時發生錯誤的螢幕擷取畫面會要求找不到的值舊版](http-stack-images/tlserror-xs.png)

此錯誤因為引進`MtouchTlsProvider`設為 Xamarin.iOS 9.8 版中的專案檔。 如果不是可更新至 Xamarin.iOS 9.8 版 （或更新版本），因應措施是手動編輯 **.csproj**檔案的應用程式中，移除`MtouchTlsprovider`項目，然後儲存變更的專案檔。

下列程式碼片段是什麼的範例`MtouchTlsProvider`設定可能看起來像是內 **.csproj**檔案：

```xml
<MtouchTlsProvider>Default</MtouchTlsProvider>
```

## <a name="related-links"></a>相關連結

- [傳輸層安全性 (TLS)](~/cross-platform/app-fundamentals/transport-layer-security.md)
- [應用程式傳輸安全性](~/ios/app-fundamentals/ats.md)
