---
title: 適用于 iOS/macOS 的 HttpClient 和 SSL/TLS 執行選取器
description: '[HttpClient 堆疊] 和 [SSL/TLS 執行] 選取器會決定您的 Xamarin iOS、tvOS 或 macOS 應用程式將使用的 HttpClient 和 SSL/TLS 執行。'
ms.prod: xamarin
ms.assetid: 12101297-BB04-4410-85F0-A0D41B7E6591
author: conceptdev
ms.author: crdun
ms.date: 04/20/2018
ms.openlocfilehash: 305c0c939d99207e39a469f7e8c5370cc5555d38
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "71249971"
---
# <a name="httpclient-and-ssltls-implementation-selector-for-iosmacos"></a>適用于 iOS/macOS 的 HttpClient 和 SSL/TLS 執行選取器

TvOS 和 xamarin 的**HttpClient 執行選取器**會控制要使用的`HttpClient`執行方式。 您可以切換至使用 iOS、tvOS 或 macOS 原生傳輸（`NSUrlSession`或`CFNetwork`，視作業系統而定）的執行。 其優勢在於 TLS 1.2-支援、較小的二進位檔，以及更快速的下載;其缺點是，它需要執行事件迴圈，才能執行非同步作業。

專案必須參考**系統 .net. Http**元件。

> [!WARNING]
> **2018 年4月，** 由於增加的安全性需求（包括 PCI 合規性），主要雲端提供者和網頁伺服器預期會停止支援超過1.2 的 TLS 版本。 在舊版中建立的 Xamarin 專案 Visual Studio 預設為使用舊版的 TLS。
>
> 為了確保您的應用程式能夠繼續使用這些伺服器和服務，**您應該使用如下`NSUrlSession`所示的設定來更新 Xamarin 專案，然後重新建立應用程式，並將其重新部署**給使用者。

## <a name="selecting-an-httpclient-stack"></a>選取 HttpClient 堆疊

若要調整`HttpClient`您的應用程式所使用的：

1. 按兩下 **方案總管**中的**專案名稱**，以開啟 專案選項。
2. 切換至專案的**組建**設定（例如，適用于 Xamarin ios 應用程式的**ios 組建**）。
3. 從 [ **HttpClient 執行**] 下拉式清單中`HttpClient` ，選取下列其中一種類型：**NSUrlSession**（建議）、 **CFNetwork**或**受控**。

[![從受管理、 CFNetwork 或 NSUrlSession 選擇 HttpClient 實作](http-stack-images/http-xs-sml.png)](http-stack-images/http-xs.png#lightbox)

> [!TIP]
> 針對 TLS 1.2 支援， `NSUrlSession`建議使用選項。

### <a name="nsurlsession"></a>NSUrlSession

以為基礎的處理常式是以 iOS `NSURLSession` 7 和更新版本中提供的原生架構為基礎。 `NSURLSession` 
**這是建議的設定。**

#### <a name="pros"></a>展開

- 它會使用原生 Api 來提升效能和較小的可執行檔案大小。
- 支援最新的標準，例如 TLS 1.2。

#### <a name="cons"></a>各有利弊

- 需要 iOS 7 或更新版本。
- 有些`HttpClient`功能/選項無法使用。

### <a name="cfnetwork"></a>CFNetwork

以為基礎的處理常式是以 iOS `CFNetwork` 6 和更新版本中提供的原生架構為基礎。 `CFNetwork`

#### <a name="pros"></a>展開

- 它會使用原生 Api 來提升效能和較小的可執行檔案大小。
- 支援較新的標準，例如 TLS 1.2。

#### <a name="cons"></a>各有利弊

- 需要 iOS 6 或更新版本。
- 無法在 watchOS 上使用。
- 有些 HttpClient 功能/選項無法使用。

### <a name="managed"></a>Managed

受控處理常式是由舊版 Xamarin 隨附的完全受控 HttpClient 處理常式。

#### <a name="pros"></a>展開

- 它與 Microsoft .NET 和舊版 Xamarin 具有最相容的功能集。

#### <a name="cons"></a>各有利弊

- 它不完全與 Apple Os 整合，而且僅限於 TLS 1.0。 未來可能無法連接到安全的 web 伺服器或雲端服務。
- 在加密方面，通常會比原生 Api 慢很多。
- 它需要更多受控碼，因此會建立更大的應用程式可散發。

### <a name="programmatically-setting-the-httpmessagehandler"></a>以程式設計方式設定 HttpMessageHandler

除了上面所示的專案範圍設定之外，您也可以具現化`HttpClient` ，並透過此`HttpMessageHandler`函式插入所需的（如下列程式碼片段所示）：

```csharp
// This will use the default message handler for the application; as
// set in the Project Options for the project.
HttpClient client = new HttpClient();

// This will create an HttpClient that explicitly uses the CFNetworkHandler
HttpClient client = new HttpClient(new CFNetworkHandler());

// This will create an HttpClient that explicitly uses NSUrlSessionHandler
HttpClient client = new HttpClient(new NSUrlSessionHandler());
```

這可讓您使用不同`HttpMessageHandler`于 [**專案選項**] 對話方塊中所宣告的。

## <a name="ssltls-implementation"></a>SSL/TLS 實行

SSL （安全通訊端層）及其後續的 TLS （傳輸層安全性），透過提供對 HTTP 和其他網路`System.Net.Security.SslStream`連線的支援。 TvOS 或 xamarin 的`System.Net.Security.SslStream`執行會呼叫 Apple 的原生 SSL/TLS 實作為，而不是使用 Mono 所提供的 managed 執行。 Apple 的原生實作為支援 TLS 1.2。

> [!WARNING]
> 即將推出的 Xamarin.Mac 4.8 版只會支援 macOS 10.9 或更高版本。
> 舊版 Xamarin.Mac 支援 macOS 10.7 或更高版本，但這些較舊的 macOS 版本缺乏足夠的 TLS 基礎結構，無法支援 TLS 1.2。 若要以 macOS 10.7 或 10.8 為目標，請使用 Xamarin.Mac 4.6 或更舊版本。

## <a name="app-transport-security"></a>應用程式傳輸安全性

Apple 的_應用程式傳輸安全性_（ATS）會在網際網路資源（例如應用程式的後端伺服器）和您的應用程式之間，強制執行安全的連接。 ATS 可確保所有網際網路通訊都符合安全連線最佳作法，藉此防止意外洩漏敏感性資訊，無論是直接透過您的應用程式或它所耗用的程式庫。

由於預設會在針對 iOS 9、tvOS 9 和 OS X 10.11 （El Capitan）和更新版本建立的應用程式中啟用 ATS， `NSUrlConnection`因此`CFUrl`使用`NSUrlSession`或的所有連線都將受限於 ATS 的安全性需求。 如果您的連線不符合這些需求，則會失敗並產生例外狀況。

根據您的 HttpClient 堆疊和 SSL/TLS 的執行選項，您可能需要對應用程式進行修改，才能正確地與 ATS 搭配運作。

若要深入瞭解 ATS，請參閱我們的[應用程式傳輸安全性指南](~/ios/app-fundamentals/ats.md)。

## <a name="known-issues"></a>已知問題

本節將涵蓋 Xamarin 中 TLS 支援的已知問題。

### <a name="project-failed-to-load-with-error-requested-value-appletls-wasnt-found"></a>專案載入失敗，發生錯誤「找不到要求的值 AppleTLS」

Xamarin. iOS 9.8 引進了一些新設定，其中包含適用于 Xamarin iOS 應用程式的 **.csproj**檔案。 當使用舊版的 Xamarin 開啟專案時，這些變更可能會造成問題。 下列螢幕擷取畫面是在此案例中可能顯示的錯誤訊息範例：

![嘗試載入專案時發生錯誤的螢幕擷取畫面，找不到要求的舊值](http-stack-images/tlserror-xs.png)

此錯誤是因為在 Xamarin. iOS 9.8 `MtouchTlsProvider`中將設定引入專案檔所造成。 如果無法更新為 Xamarin. iOS 9.8 （或更高版本），則解決方法是手動編輯 **.csproj**檔案應用程式，移除`MtouchTlsprovider`元素，然後儲存變更的專案檔。

下列程式碼片段是在`MtouchTlsProvider` **.csproj**檔案中設定可能外觀的範例：

```xml
<MtouchTlsProvider>Default</MtouchTlsProvider>
```

## <a name="related-links"></a>相關連結

- [傳輸層安全性 (TLS)](~/cross-platform/app-fundamentals/transport-layer-security.md)
- [應用程式傳輸安全性](~/ios/app-fundamentals/ats.md)
