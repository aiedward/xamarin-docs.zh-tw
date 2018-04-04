---
title: 傳輸層安全性 (TLS)
description: 為 Android、 iOS 和 Mac 上的 Xamarin 專案啟用 TLS 1.2
ms.prod: xamarin
ms.assetid: 399F71C6-16A4-4ABC-B30D-AF17D066A5FA
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 10/10/2017
ms.openlocfilehash: 8b2d0288248f2468e6976ad4f7c46255690116c0
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="transport-layer-security-tls"></a>傳輸層安全性 (TLS)

_為 Android、 iOS 和 Mac 上的 Xamarin 專案啟用 TLS 1.2_

使用最新版[_傳輸層安全性_(TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security)務必確保安全的應用程式的網路通訊。

> [!NOTE]
> Xamarin 釋放自[2017 年 2 月](https://releases.xamarin.com/stable-release-cycle-9/)預設會在新專案中使用 TLS 1.2。

TLS 1.2 支援現已推出：

* 單聲道 4.8 (包括[TLS 1.2 支援](http://www.mono-project.com/docs/about-mono/releases/4.8.0/#tls-12-support))
* Xamarin.iOS
* Xamarin.Mac
* Xamarin.Android （需要 Android 5.0 或更新版本）

專案必須參考**System.Net.Http**組件。 

## <a name="updating-to-tls-12"></a>TLS 1.2 更新

本節說明一些在 Xamarin 專案中，網路功能的設定選項，所以您可以更新您_現有_利用更安全的通訊協定的應用程式。


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

這些設定位於**專案選項 > Android 選項**，然後按一下**進階**按鈕： 

[![HttpClient 和 TLS Visual Studio 中設定](transport-layer-security-images/properties-vs-sml.png)](transport-layer-security-images/properties-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)
這些設定位於**專案屬性 > 建置選項 > 進階** 索引標籤：

[![HttpClient 和 TLS Xamarin Studio 和 Visual Studio 中設定的 Mac](transport-layer-security-images/properties-xs-sml.png)](transport-layer-security-images/properties-xs.png#lightbox)

-----


### <a name="httpclient-implementation"></a>HttpClient 實作

Xamarin 開發人員已經能夠在他們的程式碼中使用原生網路類別，不過也會使用可決定哪些網路堆疊的選項`HttpClient`類別。 這提供熟悉的.NET API 的原生平台速度和安全性優點。

可選的按鈕有：

- **Managed 的堆疊**– Mono 提供的網路功能，或
- **原生堆疊**– 各種網路功能的基礎平台 （Android、 iOS 或 macOS） 所提供的 Api。

Managed 的堆疊提供最高層級的相容性與現有的.NET 程式碼，不過它可以會減慢，並產生較大的可執行檔大小。

原生選項可以更快速和有更佳的安全性 （包含 TLS 1.2），但可能不會提供所有功能和選項`HttpClient`類別。


### <a name="ssltls-implementation"></a>SSL/TLS 實作

專案選項也可讓您選擇支援 SSL/TLS 實作：

- **單聲道/Managed** – 在 Android 上的 TLS 1.1、 TLS 1.0 iOS 及 macOS 上。
- **原生**-Android、 iOS 和 macOS 上的 TLS 1.2。

新的 Xamarin 專案預設值為支援 TLS 1.2 （這建議用於所有專案） 的原生實作，但是如果您可以切換回 managed 程式碼所需的相容性的原因。

> [!IMPORTANT]
> **Mono/Managed**選項將會移除[未來的版本](https://developer.xamarin.com/releases/ios/xamarin.ios_10/xamarin.ios_10.8/)。
>
> 建議使用原生的選項。

## <a name="platform-specific-details"></a>平台特定詳細資料

上述的摘要說明 HttpClient 和 SSL/TLS 實作 Xamarin 專案中的專案層級設定。 HttpClient 實作也可以設定動態程式碼中，並在 iOS 上有兩個原生選項可供選擇。

- [**Android**](~/android/app-fundamentals/http-stack.md)
- [**iOS 和 Mac**](~/cross-platform/macios/http-stack.md)


## <a name="summary"></a>總結

可能的情況下，應用程式應該使用傳輸層安全性 (TLS) 1.2。
新的應用程式現在預設為此設定，不過您可能需要更新現有的應用程式根據本文章中的指示中的設定。

## <a name="related-links"></a>相關連結

- [應用程式傳輸安全性](~/ios/app-fundamentals/ats.md)
- [Xamarin.Android Environment](~/android/deploy-test/environment.md)
- [Xamarin 循環 9 (第 2017 年 2 月)](https://releases.xamarin.com/stable-release-cycle-9/)
- [TLS (Wikipedia)](https://en.wikipedia.org/wiki/Transport_Layer_Security)
- [單聲道 4.8 版本資訊-TLS 1.2 支援](http://www.mono-project.com/docs/about-monohttps://developer.xamarin.com/releases/4.8.0/#tls-12-support)
- [BoringSSL](https://boringssl.googlesource.com/boringssl/)
- [HttpClient、 HttpClientHandler 和 WebRequestHandler 說明](https://blogs.msdn.microsoft.com/henrikn/2012/08/07/httpclient-httpclienthandler-and-webrequesthandler-explained/)
- [System.Net.HttpClient](https://msdn.microsoft.com/en-us/library/system.net.http.httpclient(v=vs.118).aspx)
- [System.Net.HttpClientHandler](https://msdn.microsoft.com/en-us/library/system.net.http.httpclienthandler(v=vs.118).aspx)
- [System.Net.HttpMessageHandler](https://msdn.microsoft.com/en-us/library/system.net.http.httpmessagehandler(v=vs.118).aspx)
- [System.Net.HttpWebRequest](https://msdn.microsoft.com/en-us/library/system.net.httpwebrequest(v=vs.110).aspx)
- [System.Net.WebClient](https://msdn.microsoft.com/en-us/library/system.net.webclient(v=vs.110).aspx)
- [System.Net.WebRequest](https://msdn.microsoft.com/en-us/library/system.net.webrequest(v=vs.110).aspx)
- [java.net.URLConnection](http://developer.android.com/reference/java/net/URLConnection.html)
- [Foundation.CFNetwork](https://developer.xamarin.com/api/type/CoreFoundation.CFNetwork/)
- [Foundation.NSUrlConnection](https://developer.xamarin.com/api/type/Foundation.NSUrlConnection/)
- [System.Net.WebRequest](https://msdn.microsoft.com/en-us/library/system.net.webrequest(v=vs.110).aspx)
- [HTTP 用戶端 （範例）](https://developer.xamarin.com/samples/monotouch/HttpClient/)
