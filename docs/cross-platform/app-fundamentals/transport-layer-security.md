---
title: 傳輸層安全性 (TLS) 1.2
description: 為 Android、 iOS 和 Mac 上的 Xamarin 專案啟用 TLS 1.2
ms.prod: xamarin
ms.assetid: 399F71C6-16A4-4ABC-B30D-AF17D066A5FA
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 04/20/2018
ms.openlocfilehash: 6205e8633ccdd2c1e568e7de8103c38eb9edbc2f
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2018
---
# <a name="transport-layer-security-tls-12"></a>傳輸層安全性 (TLS) 1.2

使用最新版[_傳輸層安全性_(TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security)務必確保安全的應用程式的網路通訊。

> [!WARNING]
> **年 4 月，2018年**– 因為更高的安全性需求，包括 PCI 合規性，主要雲端提供者和網頁伺服器應停止支援 TLS 1.2 比舊的版本。  在舊版的 Visual Studio 預設使用 TLS 的舊版本中建立的 Xamarin 專案。
>
> 為了確保您的應用程式繼續使用這些伺服器與服務，**您應該更新您的 Xamarin 專案，以使用下列設定，然後重新建置並重新部署您的應用程式**給您的使用者。

專案必須參考**System.Net.Http**組件，並進行設定，如下所示。

## <a name="update-android-to-tls-12"></a>TLS 1.2 更新 Android

更新**HttpClient 實作**和**SSL/TLS 實作**啟用 TLS 1.2 安全性選項。

> [!NOTE]
> 需要 Android 5.0 或更新版本。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

這些設定位於**專案屬性 > Android 選項**，然後按一下**進階**按鈕：

[![HttpClient 和 TLS Visual Studio 中設定](transport-layer-security-images/android-win-sml.png)](transport-layer-security-images/android-win.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

這些設定位於**專案選項 > 建置 > Android 建置** 索引標籤：

[![HttpClient 和 TLS Visual Studio 中設定的 Mac](transport-layer-security-images/android-mac-sml.png)](transport-layer-security-images/android-mac.png#lightbox)

-----

## <a name="update-ios-to-tls-12"></a>TLS 1.2 更新 iOS

更新**HttpClient 實作**啟用 TSL 1.2 安全性選項。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

中可以找到此設定**專案屬性 > iOS 建置**:

[![HttpClient 和 TLS Visual Studio 中設定](transport-layer-security-images/ios-win-sml.png)](transport-layer-security-images/ios-win.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

中可以找到此設定**專案選項 > 建置 > iOS 建置** 索引標籤：

[![HttpClient Visual Studio 中設定的 Mac](transport-layer-security-images/ios-mac-sml.png)](transport-layer-security-images/ios-mac.png#lightbox)

-----

## <a name="update-macos-to-tls-12-in-visual-studio-for-mac"></a>適用於 Mac 的 Visual Studio 中的 TLS 1.2 update macOS

更新**HttpClient 實作**選項**專案選項 > 建置 > Mac 建置**啟用 TSL 1.2 安全性 索引標籤：

[![HttpClient Visual Studio 中設定的 Mac](transport-layer-security-images/macos-mac-sml.png)](transport-layer-security-images/macos-mac.png#lightbox)

## <a name="alternative-configuration-options"></a>另外一種組態選項

本節討論如上所示的 TLS 1.2 支援設定的替代方案。
如果他們了解使用不同的層級的 TLS 支援的風險，應用程式開發人員應該只會考慮這些替代方案。

### <a name="httpclient-implementation"></a>HttpClient 實作

Xamarin 開發人員已經能夠在他們的程式碼中使用原生網路類別，不過也會使用可決定哪些網路堆疊的選項`HttpClient`類別。 這提供熟悉的.NET API 的原生平台速度和安全性優點。

可選的按鈕有：

- **Managed 的堆疊**– Mono 提供的網路功能，或
- **原生堆疊**– 各種網路功能的基礎平台 （Android、 iOS 或 macOS） 所提供的 Api。

Managed 的堆疊提供最高層級的相容性與現有的.NET 程式碼，不過它可以會減慢，並產生較大的可執行檔大小。

原生選項可以更快速和有更佳的安全性 （包含 TLS 1.2），但可能不會提供所有功能和選項`HttpClient`類別。

### <a name="ssltls-implementation-android"></a>SSL/TLS 實作 (Android)

Android 專案選項也可讓您選擇支援 SSL/TLS 實作：

- **單聲道/受管理**– 在 Android 上的 TLS 1.1
- **原生**– 在 Android 上的 TLS 1.2。

新的 Xamarin 專案預設值為支援 TLS 1.2 （這建議用於所有專案） 的原生實作，但是如果您可以切換回 managed 程式碼所需的相容性的原因。

> [!IMPORTANT]
> **Mono/Managed**選項已被[移除從 iOS 和 Mac](https://developer.xamarin.com/releases/ios/xamarin.ios_10/xamarin.ios_10.8/)專案選項。
>
> IOS 和 Mac 平台上，會一律使用原生的選項。

## <a name="platform-specific-details"></a>平台特定詳細資料

上述的摘要說明 HttpClient 和 SSL/TLS 實作 Xamarin 專案中的專案層級設定。 HttpClient 實作也可以在程式碼中設定動態。 這些特定平台指南，如需詳細資訊，請參閱：

- [**Android**](~/android/app-fundamentals/http-stack.md)
- [**iOS 和 Mac**](~/cross-platform/macios/http-stack.md)


## <a name="summary"></a>總結

可能的情況下，應用程式應該使用傳輸層安全性 (TLS) 1.2。
您應該更新現有的應用程式根據此篇文章中的指示中的設定，然後重新建置並重新部署至您的客戶。

## <a name="related-links"></a>相關連結

- [應用程式傳輸安全性](~/ios/app-fundamentals/ats.md)
- [Xamarin.Android 環境](~/android/deploy-test/environment.md)
- [Xamarin 循環 9 (第 2017 年 2 月)](https://releases.xamarin.com/stable-release-cycle-9/)
- [TLS (Wikipedia)](https://en.wikipedia.org/wiki/Transport_Layer_Security)
- [單聲道 4.8 版本資訊-TLS 1.2 支援](http://www.mono-project.com/docs/about-mono/releases/4.8.0/#tls-12-support)
- [BoringSSL](https://boringssl.googlesource.com/boringssl/)
- [HttpClient、 HttpClientHandler 和 WebRequestHandler 說明](https://blogs.msdn.microsoft.com/henrikn/2012/08/07/httpclient-httpclienthandler-and-webrequesthandler-explained/)
- [System.Net.HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.118).aspx)
- [System.Net.HttpClientHandler](https://msdn.microsoft.com/library/system.net.http.httpclienthandler(v=vs.118).aspx)
- [System.Net.HttpMessageHandler](https://msdn.microsoft.com/library/system.net.http.httpmessagehandler(v=vs.118).aspx)
- [System.Net.HttpWebRequest](https://msdn.microsoft.com/library/system.net.httpwebrequest(v=vs.110).aspx)
- [System.Net.WebClient](https://msdn.microsoft.com/library/system.net.webclient(v=vs.110).aspx)
- [System.Net.WebRequest](https://msdn.microsoft.com/library/system.net.webrequest(v=vs.110).aspx)
- [java.net.URLConnection](http://developer.android.com/reference/java/net/URLConnection.html)
- [Foundation.CFNetwork](https://developer.xamarin.com/api/type/CoreFoundation.CFNetwork/)
- [Foundation.NSUrlConnection](https://developer.xamarin.com/api/type/Foundation.NSUrlConnection/)
- [System.Net.WebRequest](https://msdn.microsoft.com/library/system.net.webrequest(v=vs.110).aspx)
- [HTTP 用戶端 （範例）](https://developer.xamarin.com/samples/monotouch/HttpClient/)
