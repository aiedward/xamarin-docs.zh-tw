---
title: "應用程式的傳輸安全性"
description: "應用程式的傳輸安全性 (ATS) 會強制執行 （例如應用程式的後端伺服器） 的網際網路資源與您的應用程式之間的安全連線。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 0E2217F1-FC96-4D0A-ABAB-D40AD8F96502
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/13/2017
ms.openlocfilehash: 60858e05e222725f05eb67bd7aaa4e56d2ff3880
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="app-transport-security"></a>應用程式的傳輸安全性

_應用程式的傳輸安全性 (ATS) 會強制執行 （例如應用程式的後端伺服器） 的網際網路資源與您的應用程式之間的安全連線。_

本文將介紹應用程式傳輸安全性會強制執行 iOS 9 應用程式為基礎的安全性變更以及[這表示 Xamarin.iOS 專案](#xamarinsupport)，會涵蓋[AT 組態選項](#config)和會涵蓋如何[AT 退出](#optout)AT 必要。 因為預設會啟用 AT，任何不安全的網際網路連線將會引發 9 的 iOS 應用程式中的例外狀況 （除非您已明確地允許它）。


## <a name="about-app-transport-security"></a>關於應用程式的傳輸安全性

如前所述，AT 可確保所有的網際網路通訊，在 iOS 9 和 OS X El Capitan 符合要保障連線安全的最佳作法，因此可以防止意外洩漏機密資訊直接透過您的應用程式或其程式庫耗用。

針對現有的應用程式，實作`HTTPS`盡可能通訊協定。 針對新的 Xamarin.iOS 應用程式，您應該使用`HTTPS`與網際網路資源進行通訊時，以獨佔方式。 此外，您必須使用 TLS 1.2 版與正向加密來加密高階應用程式開發介面通訊。

與所建立的任何連接[NSUrlConnection](https://developer.xamarin.com/api/type/Foundation.NSUrlConnection/)， [CFUrl](https://developer.xamarin.com/api/type/CoreFoundation.CFUrl/)或[NSUrlSession](https://developer.xamarin.com/api/type/Foundation.NSUrlSession/)將使用 AT 預設會在為 iOS 9 和 OS X 10.11 (El Capitan) 建置的應用程式。

## <a name="default-ats-behavior"></a>預設 AT 行為

因為在建置適用於 iOS 9 和 OS X 10.11 (El Capitan) 使用的所有連接的應用程式預設會啟用 AT [NSUrlConnection](https://developer.xamarin.com/api/type/Foundation.NSUrlConnection/)， [CFUrl](https://developer.xamarin.com/api/type/CoreFoundation.CFUrl/)或[NSUrlSession](https://developer.xamarin.com/api/type/Foundation.NSUrlSession/)會受限於AT 安全性需求。 如果您的連線不符合這些需求，它們將會失敗並發生例外狀況。

### <a name="ats-connection-requirements"></a>AT 連線需求

AT 將會強制執行所有的網際網路連線的下列需求：

- 所有連接的密碼必須都使用正向加密。 請參閱以下可接受加密的清單。
- 傳輸層安全性 (TLS) 通訊協定必須是版本 1.2 或更新版本。
- 至少具有 2048 位元或更高的 RSA 金鑰，或 256 位元或更大 Elliptic 曲線 (ECC) 的索引鍵的 SHA256 指紋必須用於所有憑證。

同樣地，在 iOS 9 中預設會啟用 AT，因為連線不符合這些需求的任何嘗試將會導致擲回例外狀況。 

<a name="ATS-Compatible-Ciphers" />

### <a name="ats-compatible-ciphers"></a>AT 相容的加密方式

保護的網際網路通訊的 AT 接受下列的正向加密的加密類型：

- `TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384`
- `TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256`
- `TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384`
- `TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA`
- `TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256`
- `TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA`
- `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
- `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
- `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`
- `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
- `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`

如需有關使用 iOS 網際網路通訊類別的詳細資訊，請參閱 Apple [NSURLConnection 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSURLConnection_Class/index.html#//apple_ref/doc/uid/TP40003755)， [CFURL 參考](https://developer.apple.com/library/prerelease/ios/documentation/CoreFoundation/Reference/CFURLRef/index.html#//apple_ref/doc/uid/20001206)或[NSURLSession 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/Foundation/Reference/NSURLSession_class/index.html#//apple_ref/doc/uid/TP40013435).

<a name="xamarinsupport" />

## <a name="supporting-ats-in-xamarinios"></a>支援 Xamarin.iOS AT

如果 Xamarin.iOS 應用程式或程式庫或它正在使用的服務會建立連線到網際網路，則在 iOS 9 和 OS X El Capitan，預設會啟用 AT，因為您必須採取某些動作，或您的連線會導致擲回例外狀況。

現有的應用程式，建議您支援 Apple`HTTPS`儘速通訊協定。 如果您是因為無法連接到第 3 合作對象不支援的 web 服務`HTTPS`或如果支援`HTTPS`就不太實用，您可以退出 AT。 請參閱[Opting 外 AT](#Opting-Out-of-ATS)下面章節以取得詳細資料。

新的 Xamarin.iOS 應用程式中，您應該使用`HTTPS`與網際網路資源進行通訊時，以獨佔方式。 同樣地，可能會有情況 （例如使用第 3 個合作對象 web 服務） 無法這樣做，您將需要 AT 退出。

此外，AT 會強制執行加密與正向加密使用 TLS 1.2 版的高階應用程式開發介面通訊。 請參閱[AT 連線需求](#ATS-Connection-Requirements)和[AT 相容的加密方式](#ATS-Compatible-Ciphers)上方區段，如需詳細資訊。

雖然您可能不熟悉 TLS ([傳輸層安全性](https://en.wikipedia.org/wiki/Transport_Layer_Security)) 是 SSL 的後續版本 ([安全通訊端層](https://en.wikipedia.org/wiki/Transport_Layer_Security))，並提供密碼編譯通訊協定，透過強制執行安全性的集合網路連線。

TLS 層級由您在使用 web 服務，因此應用程式的控制之外。 同時`HttpClient`和`ModernHttpClient`可以自動使用 TLS 加密伺服器所支援的最高層級。

根據伺服器 （特別是如果它是第 3 個合作對象服務），您會提到要您可能需要停用轉寄密碼，或選取較低的 TLS 層級。 請參閱[設定 AT 選項](#Configuring-ATS-Options)下面章節以取得詳細資料。

> [!IMPORTANT]
> **注意：**應用程式的傳輸安全性不適用於 Xamarin 應用程式使用**管理 HTTPClient 實作**。 它會套用至連線使用 CFNetwork **HTTPClient 實作**或**NSURLSession HTTPClient 實作**只。

### <a name="setting-the-httpclient-implementation"></a>設定 HTTPClient 實作

若要設定 iOS 應用程式所用的 HTTPClient 實作，請按兩下**專案**中**方案總管 中**開啟**專案選項**。 瀏覽至**iOS 建置**並選取所需的用戶端類型下**HttpClient 實作**下拉式清單中：

![](ats-images/client01.png "設定 iOS 建置選項")


#### <a name="managed-handler"></a>受管理的處理常式

Managed 處理常式是隨附的 Xamarin.iOS 先前版本的預設處理常式，且管理完善 HttpClient 處理常式。

優點：

- 它是最適合與 Microsoft.NET 及舊版 Xamarin。

缺點：

- 它並未完全整合與 iOS （例如長度限制為 TLS 1.0）。
- 它是通常比慢很多的原生 Api。
- 它需要更多的 managed 程式碼，並建立更大的應用程式。

#### <a name="cfnetwork-handler"></a>CFNetwork 處理常式

基礎 CFNetwork 處理常式根據原生`CFNetwork`架構。

優點：

- 原生 API，用於較佳的效能與較小的可執行檔大小。
- 加入較新的標準，例如 TLS 1.2 的支援。

缺點：

- 需要 iOS 6 或更新版本。
- 不適用的 watchOS。
- 某些 HttpClient 功能和選項無法使用。

#### <a name="nsurlsession-handler"></a>NSUrlSession Handler

基礎 NSUrlSession 處理常式根據原生`NSUrlSession`應用程式開發介面。

優點：

- 原生 API，用於較佳的效能與較小的可執行檔大小。
- 加入較新的標準，例如 TLS 1.2 的支援。

缺點：

- 需要 iOS 7 或更新版本。
- 某些 HttpClient 功能和選項無法使用。 

## <a name="diagnosing-ats-issues"></a>診斷 AT 問題

當嘗試連線到網際網路，直接或從 web 檢視在 iOS 9 中，您可能會發生錯誤，在表單中：

> 應用程式的傳輸安全性已封鎖純文字 (http://www.-the-blocked-domain.com) 的 HTTP 資源負載，因為它是不安全。 透過您的應用程式 Info.plist 檔案，您可以設定暫時例外狀況。

IOS9，應用程式傳輸安全性 (ATS) 會強制執行 （例如應用程式的後端伺服器） 的網際網路資源與您的應用程式之間的安全連線。 此外，AT 需要通訊使用`HTTPS`通訊協定和 TLS 1.2 版使用正向加密與加密的高階應用程式開發介面通訊。

因為在建置適用於 iOS 9 和 OS X 10.11 (El Capitan) 使用的所有連接的應用程式預設會啟用 AT `NSURLConnection`，`CFURL`或`NSURLSession`都會受到 AT 安全性需求。 如果您的連線不符合這些需求，它們將會失敗並發生例外狀況。

也提供 Apple [TLSTool 範例應用程式](https://developer.apple.com/library/mac/samplecode/sc1236/Introduction/Intro.html#//apple_ref/doc/uid/DTS40014927-Intro-DontLinkElementID_2)編譯的 （或選擇性地轉碼 Xamarin 和 C#） 和用來診斷問題 AT/TLS。 請參閱[Opting 外 AT](#Opting-Out_of_ATS)下面章節，如需如何解決此問題的資訊。


<a name="config" />

## <a name="configuring-ats-options"></a>設定 AT 選項

您可以在您的應用程式中設定特定索引鍵的值來設定的數個功能的 AT **Info.plist**檔案。 下列的按鍵，可用來控制 AT (_縮排，以顯示巢狀方式_):

```csharp
NSAppTransportSecurity
    NSAllowsArbitraryLoads
    NSAllowsArbitraryLoadsInWebContent
    NSExceptionDomains
    <domain-name-for-exception-as-string>
        NSExceptionMinimumTLSVersion
        NSExceptionRequiresForwardSecrecy
        NSExceptionAllowsInsecureHTTPLoads
        NSRequiresCertificateTransparency
        NSIncludesSubdomains
        NSThirdPartyExceptionMinimumTLSVersion
        NSThirdPartyExceptionRequiresForwardSecrecy
        NSThirdPartyExceptionAllowsInsecureHTTPLoads
```

每個索引鍵具有下列類型和意義：

- **NSAppTransportSecurity** (`Dictionary`)-包含所有的設定索引鍵和值 AT。
- **NSAllowsArbitraryLoads** (`Boolean`)-如果`YES`AT 將會停用任何定義域**不**中所列`NSExceptionDomains`。 列出網域中，將使用指定的安全性設定。
- **NSAllowsArbitraryLoadsInWebContent** (`Boolean`)-如果`YES`可讓網頁應用程式的其餘部分仍啟用 Apple 傳輸安全性 (ATS) 保護時正確載入。
- **NSExceptionDomains** (`Dictionary`) 為網域的集合，而且 AT 應該使用這個網域的安全性設定。
- **< Domain-name-for-exception-as-string >** (`Dictionary`)-收集指定網域 （例如例外狀況 `www.xamarin.com`)。
- **NSExceptionMinimumTLSVersion** (`String`)-最小 TLS 版本，可能是`TLSv1.0`，`TLSv1.1`或`TLSv1.2`（此為預設值）。
- **NSExceptionRequiresForwardSecrecy** (`Boolean`)-如果`NO`網域不需要使用具有轉送安全性加密。 預設值是 `YES`。
- **NSExceptionAllowsInsecureHTTPLoads** (`Boolean`)-如果`NO`（預設值） 中，必須是與此網域的所有通訊`HTTPS`通訊協定。
- **NSRequiresCertificateTransparency** (`Boolean`)-如果`YES`網域的安全通訊端層 (SSL) 必須包含有效的透明資料。 預設值是 `NO`。
- **NSIncludesSubdomains** (`Boolean`)-如果`YES`這些設定會覆寫此網域的所有子網域。 預設值是 `NO`。
- **NSThirdPartyExceptionMinimumTLSVersion** (`String`)-第 3 個合作對象服務開發人員可掌控之外的網域時所使用的 TLS 版本。
- **NSThirdPartyExceptionRequiresForwardSecrecy** (`Boolean`)-如果`YES`3 的合作對象網域都需要有正向加密。
- **NSThirdPartyExceptionAllowsInsecureHTTPLoads** (`Boolean`)-如果`YES`AT 會允許不安全的通訊，與第 3 個合作對象的網域。

<a name="optout" />

### <a name="opting-out-of-ats"></a>選擇不足的 AT

當 Apple 強烈建議使用`HTTPS`通訊協定和安全通訊，以網際網路為基礎的資訊時，可能會永遠不可能的時間。 例如，如果您要與第 3 個合作對象 web 服務通訊，或使用 「 網際網路應用程式中傳遞廣告。

Xamarin.iOS 應用程式必須提出要求，以不安全的網域，如果您的應用程式變更下列**Info.plist**檔案將會停用這個網域的 AT 會強制執行安全性預設值：

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSExceptionDomains</key>
    <dict>
        <key>www.the-domain-name.com</key>
        <dict>
            <key>NSExceptionMinimumTLSVersion</key>
            <string>TLSv1.0</string>
            <key>NSExceptionRequiresForwardSecrecy</key>
            <false/>
            <key>NSExceptionAllowsInsecureHTTPLoads</key>
            <true/>
            <key>NSIncludesSubdomains</key>
            <true/>
        </dict>
    </dict>
</dict>
```

在 Visual Studio for Mac，按兩下`Info.plist`檔案**方案總管 中**，切換至**來源**檢視和加入上述機碼：

[ ![](ats-images/ats01.png "Info.plist 檔案的原始碼檢視")](ats-images/ats01.png)


如果您的應用程式必須載入並顯示從非安全的站台的網頁內容，您的應用程式中加入下列**Info.plist**檔案，以允許正確載入，而其餘部分仍啟用 Apple 傳輸安全性 (ATS) 保護的網頁應用程式：

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key> NSAllowsArbitraryLoadsInWebContent</key>
    <true/>
</dict>
```

（選擇性） 您可以進行下列變更您的應用程式**Info.plist**要完全停用所有的網域和網際網路通訊的 AT 檔案：

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
```

在 Visual Studio for Mac，按兩下`Info.plist`檔案**方案總管 中**，切換至**來源**檢視和加入上述機碼：

[ ![](ats-images/ats02.png "Info.plist 檔案的原始碼檢視")](ats-images/ats02.png)

> [!IMPORTANT]
> **注意：**如果您的應用程式都必須連接至不安全的網站，您應該將**一律**使用例外狀況的形式輸入網域`NSExceptionDomains`而不是完全使用時，關閉 AT `NSAllowsArbitraryLoads`。 `NSAllowsArbitraryLoads` 應該只用在極端的緊急情況下。




同樣地，停用 AT 應該_只_作為最後的方法，如果使用切換到安全的連線無法使用或不可行。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已導入的應用程式傳輸安全性 (ATS)，並且描述它強制執行與網際網路的通訊安全的方式。 首先，我們會探討 AT 需要針對執行 ios 9 Xamarin.iOS 應用程式的變更。 然後我們涵蓋控制 AT 功能和選項。 最後，我們會探討退出 AT Xamarin.iOS 應用程式中。



## <a name="related-links"></a>相關連結

- [iOS 9 範例](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 的開發人員](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
