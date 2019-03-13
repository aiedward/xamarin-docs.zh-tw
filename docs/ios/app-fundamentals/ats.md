---
title: 在 Xamarin.iOS 中的應用程式的傳輸安全性
description: 應用程式的傳輸安全性 (ATS) 會強制執行 （例如應用程式的後端伺服器） 的網際網路資源與您的應用程式之間的安全連線。
ms.prod: xamarin
ms.assetid: F8C5E444-2D05-4D9B-A2EF-EB052CD6F007
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/13/2017
ms.openlocfilehash: f9308d3a746a5a0a43cf47cc5ea809c0f82bbe7b
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233818"
---
# <a name="app-transport-security-in-xamarinios"></a>在 Xamarin.iOS 中的應用程式的傳輸安全性

_應用程式的傳輸安全性 (ATS) 會強制執行 （例如應用程式的後端伺服器） 的網際網路資源與您的應用程式之間的安全連線。_

本文將介紹應用程式的傳輸安全性會強制執行 iOS 9 應用程式的安全性變更並[這表示您的 Xamarin.iOS 專案](#xamarinsupport)，它將會涵蓋[ATS 組態選項](#config)和它將討論如何[ATS 退出](#optout)ATS 如有必要。 因為預設會啟用 ATS，任何不安全的網際網路連線將會引發 iOS 9 應用程式中的例外狀況 （除非您已明確允許它）。


## <a name="about-app-transport-security"></a>關於應用程式的傳輸安全性

如上所述，ATS 可確保所有的網際網路通訊，在 iOS 9 和 OS X El Capitan 符合保護連線安全的最佳作法，藉此防止意外洩露機密的資訊，直接透過您的應用程式或其程式庫耗用。

針對現有的應用程式，實作`HTTPS`盡可能通訊協定。 針對新的 Xamarin.iOS 應用程式，您應該使用`HTTPS`與網際網路資源進行通訊時，以獨佔方式。 此外，您必須使用 TLS 1.2 版與正向加密來加密高階 API 通訊。

與所做的任何連線[NSUrlConnection](xref:Foundation.NSUrlConnection)， [CFUrl](xref:CoreFoundation.CFUrl)或是[NSUrlSession](xref:Foundation.NSUrlSession)適用於 iOS 9 和 OS X 10.11 (El Capitan) 建置的應用程式中的預設會使用 ATS。

## <a name="default-ats-behavior"></a>預設 ATS 行為

因為在建置適用於 iOS 9 和 OS X 10.11 (El Capitan) 使用的所有連線的應用程式預設會啟用 ATS [NSUrlConnection](xref:Foundation.NSUrlConnection)， [CFUrl](xref:CoreFoundation.CFUrl)或是[NSUrlSession](xref:Foundation.NSUrlSession)會受限於ATS 安全性需求。 如果您的連線不符合這些需求，它們將會失敗並發生例外狀況。

### <a name="ats-connection-requirements"></a>ATS 連線需求

ATS 會強制所有網際網路連線的下列需求：

- 正向加密，必須使用所有的連接加密。 請參閱下方的可接受加密的清單。
- 傳輸層安全性 (TLS) 通訊協定必須是 1.2 或更新版本。
- 至少具有 2048 位元或更高的 RSA 金鑰，或 256 位元或更高的 Elliptic Curve (ECC) 金鑰的 SHA256 指紋必須用於所有的憑證。

同樣地，因為在 iOS 9 的預設會啟用 ATS，嘗試使不符合這些需求的連接將會導致擲回例外狀況。 

<a name="ATS-Compatible-Ciphers" />

### <a name="ats-compatible-ciphers"></a>ATS 相容的加密

保護網際網路通訊的 ATS 接受下列的正向加密的加密類型：

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

## <a name="supporting-ats-in-xamarinios"></a>在 Xamarin.iOS 中支援 ATS

由於預設會在 iOS 9 和 OS X El Capitan，啟用 ATS，如果您的 Xamarin.iOS 應用程式的任何程式庫或它所使用的服務會建立連線到網際網路，您必須採取某些動作，或您的連線會導致擲回例外狀況。

為現有的應用程式，Apple 會建議您在支援`HTTPS`儘速通訊協定。 如果您是因為您要連接到第 3 方不支援的 web 服務`HTTPS`則支援`HTTPS`不切實際，您可以退出 ATS。 請參閱[Opting 向外延展 ATS](#optout)節以取得詳細資料。

新的 Xamarin.iOS 應用程式中，您應該使用`HTTPS`與網際網路資源進行通訊時，以獨佔方式。 同樣地，可能會有一些情況 （例如使用第 3 個廠商的 web 服務） 不可行，您將需要 ATS 退出。

此外，強制執行加密與正向加密使用 TLS 1.2 版的高階 API 通訊。 請參閱[ATS 連線需求](#ATS-Connection-Requirements)並[ATS 相容編碼器](#ATS-Compatible-Ciphers)上方區段，如需詳細資訊。

雖然您可能不熟悉 TLS ([傳輸層安全性](https://en.wikipedia.org/wiki/Transport_Layer_Security)) 是 SSL 的後繼者 ([安全通訊端層](https://en.wikipedia.org/wiki/Transport_Layer_Security))，並提供密碼編譯通訊協定，透過強制執行安全性的集合網路連線。

TLS 層級由您使用 web 服務所控制，因此應用程式的控制之外。 同時`HttpClient`而`ModernHttpClient`應該會自動使用最高層級的伺服器所支援的 TLS 加密。

根據伺服器，您在跟通訊 （尤其是如果它是第 3 方服務），您可能需要停用轉寄密碼，或選取較低的 TLS 層級。 請參閱[設定 ATS 選項](#Configuring-ATS-Options)節以取得詳細資料。

> [!IMPORTANT]
> 應用程式的傳輸安全性不會套用至使用 Xamarin 應用程式**受管理的 HTTPClient 實作**。 它會套用至連線使用 CFNetwork **HTTPClient 實作**或是**NSURLSession HTTPClient 實作**只。

### <a name="setting-the-httpclient-implementation"></a>設定 HTTPClient 實作

若要設定使用 iOS 應用程式的 HTTPClient 實作，請按兩下**專案**中**方案總管**以開啟**專案選項**。 瀏覽至**iOS 組建**，然後選取在所需的用戶端類型**HttpClient 實作**下拉式清單中：

![](ats-images/client01.png "設定 iOS 建置選項")


#### <a name="managed-handler"></a>受管理的處理常式

受控處理常式是完全受控的 HttpClient 處理常式，與舊版 Xamarin.iOS 已寄出的預設處理常式。

專業人員：

- 它是與 Microsoft.NET 和舊版本的 Xamarin 最相容。

缺點：

- 它完全不使用 iOS （例如限於 TLS 1.0） 整合。
- 它是通常比慢很多原生 Api。
- 它需要更多的 managed 程式碼，並且會建立較大的應用程式。

#### <a name="cfnetwork-handler"></a>CFNetwork 處理常式

CFNetwork 基礎處理常式為基礎的原生`CFNetwork`framework。

專業人員：

- 使用原生 API 的更佳的效能和較小的可執行檔大小。
- 加入較新的標準，例如 TLS 1.2 支援。

缺點：

- 需要 iOS 6 或更新版本。
- WatchOS 的無法使用。
- 某些 HttpClient 功能和選項無法使用。

#### <a name="nsurlsession-handler"></a>NSUrlSession 處理常式

NSUrlSession 基礎處理常式為基礎的原生`NSUrlSession`API。

專業人員：

- 使用原生 API 的更佳的效能和較小的可執行檔大小。
- 加入較新的標準，例如 TLS 1.2 支援。

缺點：

- 需要 iOS 7 或更新版本。
- 某些 HttpClient 功能和選項無法使用。 

## <a name="diagnosing-ats-issues"></a>診斷 ATS 問題

當嘗試連線到網際網路，直接或從 web 檢視，在 iOS 9 中，您可能會發生錯誤，在表單中：

> 應用程式的傳輸安全性已封鎖的純文字 HTTP (http://www.-the-blocked-domain.com)資源負載，因為它是不安全。 暫時性例外狀況可以透過您的應用程式 Info.plist 檔案進行設定。

IOS9，App Transport Security (ATS) 會強制執行 （例如應用程式的後端伺服器） 的網際網路資源與您的應用程式之間的安全連線。 此外，ATS 需要通訊使用`HTTPS`通訊協定和加密正向加密搭配使用 TLS 1.2 版的高階 API 通訊。

因為在建置適用於 iOS 9 和 OS X 10.11 (El Capitan) 使用的所有連線的應用程式預設會啟用 ATS `NSURLConnection`，`CFURL`或`NSURLSession`將受限於 ATS 安全性需求。 如果您的連線不符合這些需求，它們將會失敗並發生例外狀況。

Apple 也提供[TLSTool 範例應用程式](https://developer.apple.com/library/mac/samplecode/sc1236/Introduction/Intro.html#//apple_ref/doc/uid/DTS40014927-Intro-DontLinkElementID_2)編譯的 (或選擇性地與 Xamarin 的轉碼和C#)，並用來診斷 ATS/TLS 的問題。 請參閱[Opting 向外延展 ATS](#optout)區段下方，如需如何解決此問題的資訊。


<a name="config" />

## <a name="configuring-ats-options"></a>設定 ATS 選項

您可以在您的應用程式中設定特定的索引鍵的值來設定數個 ATS 的功能**Info.plist**檔案。 下列機碼可供控制 ATS (_縮排，以顯示巢狀方式_):

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

- **NSAppTransportSecurity** (`Dictionary`)-包含所有的設定索引鍵和值 ATS。
- **NSAllowsArbitraryLoads** (`Boolean`)-如果`YES`任何網域將會停用 ATS**不**中所列`NSExceptionDomains`。 對於列出的網域，您將使用指定的安全性設定。
- **NSAllowsArbitraryLoadsInWebContent** (`Boolean`)-如果`YES`可正確載入，而應用程式的其餘部分仍啟用 Apple Transport Security (ATS) 保護的網頁。
- **NSExceptionDomains** (`Dictionary`)-網域的集合，以及 ATS 應該用於指定網域的安全性設定。
- **< Domain-name-for-exception-as-string >** (`Dictionary`)-針對指定的網域 （例如例外狀況的集合。 `www.xamarin.com`)。
- **NSExceptionMinimumTLSVersion** (`String`)-為的最低 TLS 版本`TLSv1.0`，`TLSv1.1`或`TLSv1.2`（此為預設值）。
- **NSExceptionRequiresForwardSecrecy** (`Boolean`)-如果`NO`網域不需要使用轉送安全性加密。 預設值為 `YES`。
- **NSExceptionAllowsInsecureHTTPLoads** (`Boolean`)-如果`NO`（預設值） 中，必須是與此網域的所有通訊`HTTPS`通訊協定。
- **NSRequiresCertificateTransparency** (`Boolean`)-如果`YES`網域的安全通訊端層 (SSL) 必須包含有效的透明資料。 預設值為 `NO`。
- **NSIncludesSubdomains** (`Boolean`)-如果`YES`這些設定會覆寫此網域的所有子網域。 預設值為 `NO`。
- **NSThirdPartyExceptionMinimumTLSVersion** (`String`)-第 3 個合作對象服務開發人員的控制之外的網域時所使用的 TLS 版本。
- **NSThirdPartyExceptionRequiresForwardSecrecy** (`Boolean`)-如果`YES`第 3 個合作對象網域需要正向加密。
- **NSThirdPartyExceptionAllowsInsecureHTTPLoads** (`Boolean`)-如果`YES`ATS 會允許不安全的通訊，與第 3 個合作對象網域。

<a name="optout" />

### <a name="opting-out-of-ats"></a>選擇向外延展 ATS

雖然 Apple 高度建議使用`HTTPS`通訊協定和安全的通訊，以網際網路為基礎的資訊，可能會有這不見得永遠可行的時間。 例如，如果您要與第 3 方 web 服務通訊，或使用您的應用程式中提供廣告的網際網路。

如果您的 Xamarin.iOS 應用程式必須提出要求，到不安全的網域，下列就會變更為您的應用程式**Info.plist**檔案將會停用強制執行指定的網域安全性預設值：

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

在 Visual Studio for Mac 中，按兩下`Info.plist`檔案中**方案總管**，切換至**來源**檢視，並加入上述機碼：

[![](ats-images/ats01.png "Info.plist 檔案的原始碼 檢視")](ats-images/ats01.png#lightbox)


如果您的應用程式需要載入，並顯示從非安全的站台的網頁內容，您的應用程式中新增下列**Info.plist**檔案，讓正確載入，而其餘部分仍啟用 Apple Transport Security (ATS) 保護的 web 網頁應用程式：

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key> NSAllowsArbitraryLoadsInWebContent</key>
    <true/>
</dict>
```

（選擇性） 您可以進行下列變更，您的應用程式**Info.plist**完全停用 ATS 所有網域及網際網路通訊的檔案：

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
```

在 Visual Studio for Mac 中，按兩下`Info.plist`檔案中**方案總管**，切換至**來源**檢視，並加入上述機碼：

[![](ats-images/ats02.png "Info.plist 檔案的原始碼 檢視")](ats-images/ats02.png#lightbox)

> [!IMPORTANT]
> 如果您的應用程式必須連線至不安全的網站，您應該**一律**例外狀況使用的形式輸入網域`NSExceptionDomains`而不是完全使用時，關閉 ATS `NSAllowsArbitraryLoads`。 `NSAllowsArbitraryLoads` 應該只用在極端的緊急情況下。




同樣地，停用 ATS 應該_只_會當作最後的手段時切換到安全的連線無法使用或不實際。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已導入 App Transport Security (ATS)，並說明它會強制執行與網際網路的安全通訊的方式。 首先，我們會涵蓋 ATS 需要在 iOS 9 上執行 Xamarin.iOS 應用程式的變更。 然後我們會涵蓋控制 ATS 功能和選項。 最後，我們會涵蓋退出 ATS Xamarin.iOS 應用程式中。



## <a name="related-links"></a>相關連結

- [iOS 9 範例](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 的開發人員](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
