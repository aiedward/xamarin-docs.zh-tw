---
title: 傳輸層安全性 (TLS) 1.2
description: 本檔說明如何啟用適用于 Xamarin 的 TLS 1.2、Xamarin. Android 和 Xamarin 專案。 它會示範如何在 Visual Studio 2019 和 Visual Studio for Mac 中進行這項作業。
ms.prod: xamarin
ms.assetid: 399F71C6-16A4-4ABC-B30D-AF17D066A5FA
author: davidortinau
ms.author: daortin
ms.date: 04/20/2018
ms.openlocfilehash: fda004fe32b8f7d047298608a500cf72c4e9f06c
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453322"
---
# <a name="transport-layer-security-tls-12"></a>傳輸層安全性 (TLS) 1.2

使用最新版本的 [_傳輸層安全性_ (TLS) ](https://en.wikipedia.org/wiki/Transport_Layer_Security) 很重要，可確保應用程式網路的通訊安全。

> [!WARNING]
> **2018 年4月** –由於安全性需求增加（包括 PCI 合規性），主要雲端提供者和 web 伺服器預期會停止支援早于1.2 的 TLS 版本。 在舊版 Visual Studio 中建立的 Xamarin 專案預設為使用舊版 TLS。
>
> 為了確保您的應用程式能夠繼續使用這些伺服器和服務， **您應該更新 Xamarin 專案以使用下列設定，然後重新建立應用程式並重新部署** 給使用者。

專案必須參考 **系統 .net. Http** 元件，並設定如下所示。

## <a name="update-xamarinandroid-to-tls-12"></a>將 Xamarin 更新為 TLS 1。2

更新 **HttpClient 執行** 和 **SSL/TLS 的實** 作為選項，以啟用 TLS 1.2 安全性。

> [!NOTE]
> 需要 Android 5.0 或更新版本。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

您可以在 [ **專案屬性 > Android 選項** ] 中找到這些設定，然後按一下 [ **Advanced** ] 按鈕：

[![在 Visual Studio 中設定 HttpClient 和 TLS](transport-layer-security-images/android-win-sml.png)](transport-layer-security-images/android-win.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

這些設定可以在 [ **建立 > Android 組建** ] 索引標籤的 [專案選項] 中找到 >：

[![在 Visual Studio for Mac 中設定 HttpClient 和 TLS](transport-layer-security-images/android-mac-sml.png)](transport-layer-security-images/android-mac.png#lightbox)

-----

## <a name="update-xamarinios-to-tls-12"></a>將 Xamarin 更新為 TLS 1。2

更新 **HttpClient 實行** 選項，以啟用 TSL 1.2 安全性。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

您可以在 [ **IOS 組建] > 的 [專案屬性**] 中找到這項設定：

[![在 Visual Studio 中設定 HttpClient 和 TLS](transport-layer-security-images/ios-win-sml.png)](transport-layer-security-images/ios-win.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

您可以在 [ **組建 > IOS 組建** ] 索引標籤的 [專案 > 選項] 中找到這項設定：

[![在 Visual Studio for Mac 中設定 HttpClient](transport-layer-security-images/ios-mac-sml.png)](transport-layer-security-images/ios-mac.png#lightbox)

-----

## <a name="update-xamarinmac-to-tls-12"></a>將 Xamarin 更新為 TLS 1。2

在 Visual Studio for Mac 中，若要在 Xamarin 應用程式中啟用 TLS 1.2，請更新 [專案選項] 中的 [ **HttpClient** ] 選項 **> 組建 > Mac 組建**：

[![在 Visual Studio for Mac 中設定 HttpClient](transport-layer-security-images/macos-mac-sml.png)](transport-layer-security-images/macos-mac.png#lightbox)

> [!WARNING]
> 即將推出的 Xamarin.Mac 4.8 版只會支援 macOS 10.9 或更高版本。
> 舊版 Xamarin.Mac 支援 macOS 10.7 或更高版本，但這些較舊的 macOS 版本缺乏足夠的 TLS 基礎結構，無法支援 TLS 1.2。 若要以 macOS 10.7 或 10.8 為目標，請使用 Xamarin.Mac 4.6 或更舊版本。

## <a name="alternative-configuration-options"></a>替代設定選項

本節將討論如上所示的 TLS 1.2 支援設定替代專案。
如果應用程式開發人員瞭解使用不同層級 TLS 支援的風險，則應該只考慮這些替代方案。

### <a name="httpclient-implementation"></a>HttpClient 執行

Xamarin 開發人員一直都可以在程式碼中使用原生網路功能類別，不過還有一個可決定類別所使用之網路堆疊的選項 `HttpClient` 。 這提供了熟悉的 .NET API，其具有原生平臺的速度和安全性優勢。

可用選項包括：

- **受控堆疊** – Mono 提供的網路功能，或
- **原生堆疊** –基礎平臺所提供的各種網路 Api (Android、IOS 或 macOS) 。

受控堆疊會提供與現有 .NET 程式碼的最高相容性層級，但速度會變慢，並產生較大的可執行檔案大小。

原生選項的速度更快，而且具有更佳的安全性 (包括 TLS 1.2) ，但可能無法提供類別的所有功能和選項 `HttpClient` 。

### <a name="ssltls-implementation-android"></a> (Android) 的 SSL/TLS 執行

Android 專案選項也可讓您選擇要支援的 SSL/TLS 執行：

- **Mono/受控** – Android 上的 TLS 1。1
- **原生** – Android 上的 TLS 1.2。

新的 Xamarin 專案預設為支援 TLS 1.2 (的原生實作為) 的所有專案建議使用，不過您可以視需要切換回 managed 程式碼，以取得相容性的原因。

> [!IMPORTANT]
> 已[從 iOS 和 Mac 專案選項中移除](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/xamarin.ios_10/xamarin.ios_10.8.md) **Mono/Managed**選項。
>
> 原生選項一律會在 iOS 和 Mac 平臺上使用。

## <a name="platform-specific-details"></a>平臺特定的詳細資料

上述摘要說明 Xamarin 專案中 HttpClient 和 SSL/TLS 執行的專案層級設定。 您也可以在程式碼中動態設定 HttpClient 執行。 如需詳細資訊，請參閱這些平臺特定指南：

- [**Android**](~/android/app-fundamentals/http-stack.md)
- [**iOS 和 Mac**](~/cross-platform/macios/http-stack.md)

## <a name="summary"></a>摘要

應用程式應該盡可能使用傳輸層安全性 (TLS) 1.2。
您應該根據本文中的指示來更新現有應用程式中的設定，然後重新建立並重新部署至您的客戶。

## <a name="related-links"></a>相關連結

- [應用程式傳輸安全性](~/ios/app-fundamentals/ats.md)
- [Xamarin.Android 環境](~/android/deploy-test/environment.md)
- [Xamarin 週期 9 (2017 年2月) ](https://releases.xamarin.com/stable-release-cycle-9/)
- [ (維琪百科) 的 TLS ](https://en.wikipedia.org/wiki/Transport_Layer_Security)
- [Mono 4.8 版本資訊-TLS 1.2 支援](https://www.mono-project.com/docs/about-mono/releases/4.8.0/#tls-12-support)
- [BoringSSL](https://boringssl.googlesource.com/boringssl/)
- [說明 HttpClient、HttpClientHandler 和 WebRequestHandler](/archive/blogs/henrikn/httpclient-httpclienthandler-and-webrequesthandler-explained)
- [System .Net. HttpClient](/previous-versions/visualstudio/hh193681(v=vs.118))
- [System .Net. HttpClientHandler](/previous-versions/visualstudio/hh138157(v=vs.118))
- [System .Net. HttpMessageHandler](/previous-versions/visualstudio/hh138091(v=vs.118))
- [System .Net. HttpWebRequest](/dotnet/api/system.net.httpwebrequest)
- [System.Net.WebClient](/dotnet/api/system.net.webclient)
- [System .Net. WebRequest](/dotnet/api/system.net.webrequest)
- [URLConnection](https://developer.android.com/reference/java/net/URLConnection.html)
- [CFNetwork](xref:CoreFoundation.CFNetwork)
- [NSUrlConnection](xref:Foundation.NSUrlConnection)
- [System .Net. WebRequest](/dotnet/api/system.net.webrequest)
- [HTTP 用戶端 (範例) ](/samples/xamarin/ios-samples/httpclient/)