---
title: Xamarin 中的應用程式傳輸安全性
description: 應用程式傳輸安全性（ATS）會在網際網路資源（例如應用程式的後端伺服器）和您的應用程式之間，強制執行安全的連接。
ms.prod: xamarin
ms.assetid: F8C5E444-2D05-4D9B-A2EF-EB052CD6F007
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/13/2017
ms.openlocfilehash: 84d235bb7c6874255ea025ff5897e150bd6f023b
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292400"
---
# <a name="app-transport-security-in-xamarinios"></a>Xamarin 中的應用程式傳輸安全性

_應用程式傳輸安全性（ATS）會在網際網路資源（例如應用程式的後端伺服器）和您的應用程式之間，強制執行安全的連接。_

本文將介紹應用程式傳輸安全性在 iOS 9 應用程式上強制執行的安全性變更，以及[這對您的 Xamarin ios 專案所代表的意義](#xamarinsupport)。它會涵蓋[ATS 設定選項](#config)，並涵蓋如何選擇不使用[ATS](#optout) ATS （如有必要）。 因為預設會啟用 ATS，所以任何不安全的網際網路連線都會在 iOS 9 應用程式中引發例外狀況（除非您已明確允許）。

## <a name="about-app-transport-security"></a>關於應用程式傳輸安全性

如上所述，ATS 可確保 iOS 9 和 OS X El Capitan 中的所有網際網路通訊都符合安全連線最佳作法，藉此防止意外洩漏敏感性資訊，無論是直接透過您的應用程式或程式庫耗用.

針對現有的應用程式，盡可能執行 `HTTPS` 通訊協定。 針對新的 Xamarin iOS 應用程式，您應該在與網際網路資源通訊時，以獨佔方式使用 `HTTPS`。 此外，您必須使用具有轉寄密碼的 TLS 1.2 版來加密高階 API 通訊。

使用[NSUrlConnection](xref:Foundation.NSUrlConnection)、 [CFUrl](xref:CoreFoundation.CFUrl)或[NSUrlSession](xref:Foundation.NSUrlSession)所建立的任何連線，預設會在 iOS 9 和 OS X 10.11 （El Capitan）所建立的應用程式中使用 ATS。

## <a name="default-ats-behavior"></a>預設 ATS 行為

由於預設會在針對 iOS 9 和 OS X 10.11 （El Capitan）建立的應用程式中啟用 ATS，因此使用[NSUrlConnection](xref:Foundation.NSUrlConnection)、 [CFUrl](xref:CoreFoundation.CFUrl)或[NSUrlSession](xref:Foundation.NSUrlSession)的所有連線都將受限於 ATS 安全性需求。 如果您的連線不符合這些需求，則會失敗並產生例外狀況。

### <a name="ats-connection-requirements"></a>ATS 連接需求

ATS 會針對所有網際網路連線強制執行下列需求：

- 所有連線加密都必須使用轉寄密碼。 請參閱下列已接受的密碼清單。
- 傳輸層安全性（TLS）通訊協定必須是1.2 或更高版本。
- 至少有一個 SHA256 指紋具有2048位或更高的 RSA 金鑰，或是256位或更大的橢圓曲線（ECC）金鑰必須用於所有憑證。

同樣地，由於 iOS 9 中預設已啟用 ATS，因此任何嘗試建立不符合這些需求的連線都會導致擲回例外狀況。

<a name="ATS-Compatible-Ciphers" />

### <a name="ats-compatible-ciphers"></a>ATS 相容的密碼

ATS 安全的網際網路通訊可接受下列轉寄密碼加密類型：

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

如需有關使用 iOS 網際網路通訊類別的詳細資訊，請參閱 Apple 的[NSURLConnection 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSURLConnection_Class/index.html#//apple_ref/doc/uid/TP40003755)、 [CFURL 參考](https://developer.apple.com/library/prerelease/ios/documentation/CoreFoundation/Reference/CFURLRef/index.html#//apple_ref/doc/uid/20001206)或[NSURLSession 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/Foundation/Reference/NSURLSession_class/index.html#//apple_ref/doc/uid/TP40013435)。

<a name="xamarinsupport" />

## <a name="supporting-ats-in-xamarinios"></a>支援 Xamarin 中的 ATS

由於預設會在 iOS 9 和 OS X El Capitan 中啟用 ATS，因此，如果您的 Xamarin iOS 應用程式或它使用的任何程式庫或服務連線到網際網路，您必須採取一些動作，否則您的連線會導致擲回例外狀況。

針對現有的應用程式，Apple 建議您儘快支援 `HTTPS` 通訊協定。 如果您無法連接到不支援 `HTTPS` 的協力廠商 web 服務，或者如果支援 `HTTPS` 不可行，您可以退出宣告 ATS。 如需詳細資訊，請參閱下面的選擇不[ATS](#optout)一節。

針對新的 Xamarin iOS 應用程式，您應該在與網際網路資源通訊時，以獨佔方式使用 `HTTPS`。 同樣地，有時可能會發生這種情況（例如使用協力廠商 web 服務），而您必須退出宣告 ATS。

此外，ATS 會強制使用具有轉寄密碼的 TLS 1.2 版來加密高階 API 通訊。 如需詳細資訊，請參閱上面的 < [ATS 連線需求](#ats-connection-requirements)和[ATS 相容](#ats-compatible-ciphers)的加密一節。

雖然您可能不熟悉 TLS （[傳輸層安全性](https://en.wikipedia.org/wiki/Transport_Layer_Security)），但它是 SSL （[安全通訊端層](https://en.wikipedia.org/wiki/Transport_Layer_Security)）的後續版本，並提供一組密碼編譯通訊協定，以透過網路連線來強制執行安全性。

TLS 層級是由您使用的 web 服務所控制，因此在應用程式的控制範圍外。 `HttpClient` 和 `ModernHttpClient` 都應該自動使用伺服器所支援的最高層級 TLS 加密。

根據您所使用的伺服器（尤其是協力廠商服務），您可能需要停用轉寄密碼或選取較低的 TLS 層級。 如需詳細資訊，請參閱下面的設定[ATS 選項](#configuring-ats-options)一節。

> [!IMPORTANT]
> 應用程式傳輸安全性不適用於使用**受控 HTTPClient**的 Xamarin 應用程式。 它僅適用于使用 CFNetwork **HTTPClient**執行的連線，或僅限**NSURLSession HTTPClient**的實作為連接。

### <a name="setting-the-httpclient-implementation"></a>設定 HTTPClient 的執行

若要設定 iOS 應用程式所使用的 HTTPClient 執行，請按兩下 **方案總管**中的**專案**，以開啟 **專案選項**。 流覽至 [ **IOS 組建**]，然後在 [ **HttpClient 執行**] 下拉式清單中選取所需的用戶端類型：

![](ats-images/client01.png "Setting the iOS Build Options")

#### <a name="managed-handler"></a>受控處理常式

Managed 處理常式是由舊版的 Xamarin 隨附的完全受控 HttpClient 處理常式，而且是預設的處理常式。

優點：

- 它與 Microsoft .NET 和舊版 Xamarin 最相容。

缺點：

- 它不完全與 iOS 整合（例如，它僅限於 TLS 1.0）。
- 通常速度會比原生 Api 慢很多。
- 它需要更多受控碼，並建立較大的應用程式。

#### <a name="cfnetwork-handler"></a>CFNetwork 處理常式

以 CFNetwork 為基礎的處理常式是以原生 `CFNetwork` 架構為基礎。

優點：

- 使用原生 API 以獲得更佳的效能和較小的可執行檔案大小。
- 新增 TLS 1.2 等較新標準的支援。

缺點：

- 需要 iOS 6 或更新版本。
- WatchOS 無法使用。
- 有些 HttpClient 功能和選項無法使用。

#### <a name="nsurlsession-handler"></a>NSUrlSession 處理常式

以 NSUrlSession 為基礎的處理常式是以原生 `NSUrlSession` API 為基礎。

優點：

- 使用原生 API 以獲得更佳的效能和較小的可執行檔案大小。
- 新增 TLS 1.2 等較新標準的支援。

缺點：

- 需要 iOS 7 或更新版本。
- 有些 HttpClient 功能和選項無法使用。

## <a name="diagnosing-ats-issues"></a>診斷 ATS 問題

嘗試直接或從 iOS 9 中的 web view 連線到網際網路時，您可能會收到下列格式的錯誤：

> 應用程式傳輸安全性已封鎖純文字 HTTP （`http://www.-the-blocked-domain.com`）資源載入，因為它不安全。 您可以透過應用程式的 plist 檔案來設定暫時的例外狀況。

在 iOS9 中，應用程式傳輸安全性（ATS）會在網際網路資源（例如應用程式的後端伺服器）和您的應用程式之間強制執行安全的連線。 此外，ATS 需要使用 `HTTPS` 通訊協定和高階 API 通訊，透過具有轉寄密碼的 TLS 1.2 版進行加密。

由於預設會在針對 iOS 9 和 OS X 10.11 （El Capitan）建立的應用程式中啟用 ATS，因此使用 `NSURLConnection`、`CFURL` 或 `NSURLSession` 的所有連線都會受到 ATS 的安全性需求。 如果您的連線不符合這些需求，則會失敗並產生例外狀況。

Apple 也提供可編譯（或選擇性地轉碼至 Xamarin 和C#）的[TLSTool 範例應用程式](https://developer.apple.com/library/mac/samplecode/sc1236/Introduction/Intro.html#//apple_ref/doc/uid/DTS40014927-Intro-DontLinkElementID_2)，並可用來診斷 ATS/TLS 問題。 如需如何解決此問題的相關資訊，請參閱下面的選擇不[ATS](#optout)一節。

<a name="config" />

## <a name="configuring-ats-options"></a>設定 ATS 選項

您可以設定應用程式**資訊 plist**檔案中特定索引鍵的值，以設定 ATS 的數個功能。 下列機碼可用於控制 ATS （_縮排以顯示它們的嵌套方式_）：

```
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

每個索引鍵都有下列類型和意義：

- **NSAppTransportSecurity** （`Dictionary`）-包含 ATS 的所有設定機碼和值。
- **NSAllowsArbitraryLoads** （`Boolean`）-如果 `NSExceptionDomains`中**未**列出的任何網域，`YES` ATS 將會停用。 針對列出的網域，將會使用指定的安全性設定。
- **NSAllowsArbitraryLoadsInWebContent** （`Boolean`）-如果 `YES` 讓網頁能夠正確載入，同時仍為應用程式的其餘部分啟用 Apple Transport SECURITY （ATS）保護。
- **NSExceptionDomains** （`Dictionary`）-網域的集合，以及 ATS 應該針對指定的網域使用的安全性設定。
- **\<的功能變數名稱--exception as string >** （`Dictionary`）-指定網域的例外狀況集合（例如 `www.xamarin.com`)。
- **NSExceptionMinimumTLSVersion** （`String`）-最低 TLS 版本為 `TLSv1.0`、`TLSv1.1` 或 `TLSv1.2` （這是預設值）。
- **NSExceptionRequiresForwardSecrecy** （`Boolean`）-如果 `NO` 網域不需要使用具有轉寄安全性的密碼。 預設值是 `YES`。
- **NSExceptionAllowsInsecureHTTPLoads** （`Boolean`）-如果 `NO` （預設值），則與此網域的所有通訊都必須是 `HTTPS` 通訊協定。
- **NSRequiresCertificateTransparency** （`Boolean`）-如果 `YES` 網域的安全通訊端層（SSL）必須包含有效的透明度資料。 預設值是 `NO`。
- **NSIncludesSubdomains** （`Boolean`）-如果 `YES` 這些設定會覆寫此網域的所有子域。 預設值是 `NO`。
- **NSThirdPartyExceptionMinimumTLSVersion** （`String`）-當網域是開發人員控制外部的協力廠商服務時所使用的 TLS 版本。
- **NSThirdPartyExceptionRequiresForwardSecrecy** （`Boolean`）-如果 `YES` 協力廠商網域需要轉寄密碼。
- **NSThirdPartyExceptionAllowsInsecureHTTPLoads** （`Boolean`）-如果 `YES` ATS 將允許與協力廠商網域進行不安全的通訊。

<a name="optout" />

### <a name="opting-out-of-ats"></a>退出 ATS

雖然 Apple 強烈建議使用 `HTTPS` 通訊協定，以及安全地與網際網路型資訊的通訊，但有時可能不一定會發生這種情況。 例如，如果您正在與協力廠商 web 服務通訊，或在您的應用程式中使用網際網路提供的廣告。

如果您的 Xamarin iOS 應用程式必須對不安全的網域提出要求，則下列應用程式的**資訊 plist**檔案變更將會停用 ATS 針對指定網域強制執行的安全性預設值：

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

在 Visual Studio for Mac 內，按兩下**方案總管**中的 `Info.plist` 檔案，切換至 [**來源**] 視圖並新增上述索引鍵：

[![](ats-images/ats01.png "The Source view of the Info.plist file")](ats-images/ats01.png#lightbox)

如果您的應用程式需要從不安全的網站載入及顯示 web 內容，請將下列程式新增至您應用程式的**plist**檔案，讓網頁能夠正確載入，同時仍會為其餘的應用程式啟用 Apple Transport SECURITY （ATS）保護功能：

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key> NSAllowsArbitraryLoadsInWebContent</key>
    <true/>
</dict>
```

（選擇性）您可以對應用程式的**plist**檔案進行下列變更，以完全停用所有網域和網際網路通訊的 ATS：

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
```

在 Visual Studio for Mac 內，按兩下**方案總管**中的 `Info.plist` 檔案，切換至 [**來源**] 視圖並新增上述索引鍵：

[![](ats-images/ats02.png "The Source view of the Info.plist file")](ats-images/ats02.png#lightbox)

> [!IMPORTANT]
> 如果您的應用程式需要連接到不安全的網站，您應該**一律**使用 `NSExceptionDomains` 將該網域輸入為例外狀況，而不是使用 `NSAllowsArbitraryLoads`完全關閉 ATS。 `NSAllowsArbitraryLoads` 應該僅用於極緊急的情況。

同樣地，停用 ATS_只能_做為最後的手段，如果切換到安全連線是無法使用或不切實際的。

<a name="Summary" />

## <a name="summary"></a>摘要

本文引進了應用程式傳輸安全性（ATS），並說明它如何強制執行與網際網路的安全通訊。 首先，我們涵蓋了在 iOS 9 上執行的 Xamarin iOS 應用程式所需的變更 ATS。 接著，我們討論了如何控制 ATS 的功能和選項。 最後，我們在您的 Xamarin iOS 應用程式仲介紹了退出 ATS。

## <a name="related-links"></a>相關連結

- [iOS 9 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [iOS 9 開發人員](https://developer.apple.com/ios/pre-release/)
- [iOS 9。0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
