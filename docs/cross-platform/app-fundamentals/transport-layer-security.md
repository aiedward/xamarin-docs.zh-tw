---
title: 傳輸層安全性 (TLS) 1.2
description: 本文件說明如何啟用 TLS 1.2 Xamarin.iOS、 Xamarin.Android 和 Xamarin.Mac 專案。 它會示範如何在 Visual Studio 2019 和 Visual Studio for mac。
ms.prod: xamarin
ms.assetid: 399F71C6-16A4-4ABC-B30D-AF17D066A5FA
author: asb3993
ms.author: amburns
ms.date: 04/20/2018
ms.openlocfilehash: 26870ae0e84a84a7b78f7766a8e134ecfc7b223e
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "58854999"
---
# <a name="transport-layer-security-tls-12"></a>傳輸層安全性 (TLS) 1.2

使用最新版[_傳輸層安全性_(TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security)務必確保應用程式的網路通訊的安全性。

> [!WARNING]
> **2018 年 4 月，** – 因為更高的安全性需求，包括 PCI 合規性，主要雲端提供者和 web 伺服器會停止支援 TLS 1.2 與較舊的版本。  在舊版的 Visual Studio 預設使用 TLS 的較舊版本中建立的 Xamarin 專案。
>
> 為了確保您的應用程式可以繼續使用這些伺服器或服務，**您應該更新您的 Xamarin 專案，使用下列設定，然後重新建置並重新部署您的應用程式**給您的使用者。

專案必須參考**System.Net.Http**組件和設定，如下所示。

## <a name="update-xamarinandroid-to-tls-12"></a>Tls 1.2 的更新 Xamarin.Android

更新**HttpClient 實作**並**SSL/TLS 實作**啟用 TLS 1.2 安全性選項。

> [!NOTE]
> 需要 Android 5.0 或更新版本。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

這些設定可在**專案屬性 > Android 選項**，然後按一下**進階**按鈕：

[![在 Visual Studio 中設定 HttpClient 和 TLS](transport-layer-security-images/android-win-sml.png)](transport-layer-security-images/android-win.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

這些設定可在**專案選項 > 建置 > Android 建置** 索引標籤：

[![設定 HttpClient 和 TLS 在 Visual Studio for Mac](transport-layer-security-images/android-mac-sml.png)](transport-layer-security-images/android-mac.png#lightbox)

-----

## <a name="update-xamarinios-to-tls-12"></a>Tls 1.2 的更新 Xamarin.iOS

更新**HttpClient 實作**啟用 TSL 1.2 安全性選項。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

此設定可在**專案屬性 > iOS 組建**:

[![在 Visual Studio 中設定 HttpClient 和 TLS](transport-layer-security-images/ios-win-sml.png)](transport-layer-security-images/ios-win.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

此設定可在**專案選項 > 建置 > iOS 組建** 索引標籤：

[![Visual Studio 中設定 HttpClient for Mac](transport-layer-security-images/ios-mac-sml.png)](transport-layer-security-images/ios-mac.png#lightbox)

-----

## <a name="update-xamarinmac-to-tls-12"></a>Tls 1.2 的更新 Xamarin.Mac

在 Visual Studio for Mac，若要啟用 TLS 1.2 的 Xamarin.Mac 應用程式中，更新**HttpClient 實作**選項**專案選項 > 建置 > Mac 組建**:

[![Visual Studio 中設定 HttpClient for Mac](transport-layer-security-images/macos-mac-sml.png)](transport-layer-security-images/macos-mac.png#lightbox)

> [!WARNING]
> 即將推出的 Xamarin.Mac 4.8 版只會支援 macOS 10.9 或更高版本。
> 舊版 Xamarin.Mac 支援 macOS 10.7 或更高版本，但這些較舊的 macOS 版本缺乏足夠的 TLS 基礎結構，無法支援 TLS 1.2。 若要以 macOS 10.7 或 10.8 為目標，請使用 Xamarin.Mac 4.6 或更舊版本。

## <a name="alternative-configuration-options"></a>另外一種組態選項

本章節將討論如上所示的 TLS 1.2 支援組態的替代方案。
如果他們了解使用的 TLS 支援不同層級的風險，應用程式開發人員應該只會考慮這些替代方案。

### <a name="httpclient-implementation"></a>HttpClient 實作

Xamarin 開發人員一直都能夠使用他們的程式碼中的原生的網路類別，不過也可決定哪些網路堆疊的選項使用`HttpClient`類別。 這提供熟悉的.NET API 的原生平台的速度和安全性優點。

可選的按鈕有：

- **Managed 的堆疊**– 所提供在 Mono 上執行的網路功能，或
- **原生堆疊**– 各種網路功能的基礎平台 （Android、 iOS 或 macOS） 所提供的 Api。

Managed 的堆疊會提供最高層級的相容性與現有的.NET 程式碼，不過它會變慢，而且導致較大的可執行檔大小。

原生的選項可以更快速並有更好的安全性 （包含 TLS 1.2），但可能不會提供所有功能和選項的`HttpClient`類別。

### <a name="ssltls-implementation-android"></a>SSL/TLS 實作 (Android)

Android 專案選項也可讓您選擇哪一個是支援 SSL/TLS 實作：

- **Mono/管理**– 在 Android 上的 TLS 1.1
- **原生**– 在 Android 上的 TLS 1.2。

新的 Xamarin 專案預設原生支援 TLS 1.2 （這建議用於所有專案） 的實作，不過如果您可以切換回 managed 程式碼所需的相容性的原因。

> [!IMPORTANT]
> **Mono/受控**選項已被[移除從 iOS 和 Mac](https://developer.xamarin.com/releases/ios/xamarin.ios_10/xamarin.ios_10.8/)專案選項。
>
> 一律在 iOS 和 Mac 平台上使用原生的選項。

## <a name="platform-specific-details"></a>平台專屬的詳細資料

上述的摘要說明在 Xamarin 專案中的 HttpClient 和 SSL/TLS 實作的專案層級設定。 HttpClient 實作也可以在程式碼中設定動態。 這些平台特定指南，如需詳細資訊，請參閱：

- [**Android**](~/android/app-fundamentals/http-stack.md)
- [**iOS 和 Mac**](~/cross-platform/macios/http-stack.md)

## <a name="summary"></a>總結

可能的情況下，應用程式應該使用傳輸層安全性 (TLS) 1.2。
您應該更新中的指示，在本文中，根據現有的應用程式的設定，然後重新建置並重新部署至您的客戶。

## <a name="related-links"></a>相關連結

- [應用程式傳輸安全性](~/ios/app-fundamentals/ats.md)
- [Xamarin.Android 環境](~/android/deploy-test/environment.md)
- [Xamarin Cycle 9 (年 2 月 2017)](https://releases.xamarin.com/stable-release-cycle-9/)
- [TLS (Wikipedia)](https://en.wikipedia.org/wiki/Transport_Layer_Security)
- [Mono 4.8 版本資訊-TLS 1.2 支援](https://www.mono-project.com/docs/about-mono/releases/4.8.0/#tls-12-support)
- [BoringSSL](https://boringssl.googlesource.com/boringssl/)
- [HttpClient、 HttpClientHandler 和 WebRequestHandler 說明](https://blogs.msdn.microsoft.com/henrikn/2012/08/07/httpclient-httpclienthandler-and-webrequesthandler-explained/)
- [System.Net.HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.118).aspx)
- [System.Net.HttpClientHandler](https://msdn.microsoft.com/library/system.net.http.httpclienthandler(v=vs.118).aspx)
- [System.Net.HttpMessageHandler](https://msdn.microsoft.com/library/system.net.http.httpmessagehandler(v=vs.118).aspx)
- [System.Net.HttpWebRequest](https://msdn.microsoft.com/library/system.net.httpwebrequest(v=vs.110).aspx)
- [System.Net.WebClient](https://msdn.microsoft.com/library/system.net.webclient(v=vs.110).aspx)
- [System.Net.WebRequest](https://msdn.microsoft.com/library/system.net.webrequest(v=vs.110).aspx)
- [java.net.URLConnection](https://developer.android.com/reference/java/net/URLConnection.html)
- [Foundation.CFNetwork](xref:CoreFoundation.CFNetwork)
- [Foundation.NSUrlConnection](xref:Foundation.NSUrlConnection)
- [System.Net.WebRequest](https://msdn.microsoft.com/library/system.net.webrequest(v=vs.110).aspx)
- [HTTP 用戶端 （範例）](https://developer.xamarin.com/samples/monotouch/HttpClient/)
