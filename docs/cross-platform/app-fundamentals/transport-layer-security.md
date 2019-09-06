---
title: 傳輸層安全性（TLS）1。2
description: 本檔說明如何啟用 Xamarin. iOS、Xamarin 和 Xamarin 專案的 TLS 1.2。 它會示範如何在 Visual Studio 2019 和 Visual Studio for Mac 中執行這項操作。
ms.prod: xamarin
ms.assetid: 399F71C6-16A4-4ABC-B30D-AF17D066A5FA
author: conceptdev
ms.author: crdun
ms.date: 04/20/2018
ms.openlocfilehash: 6175725ae3eea805680b4da81aa0458aa3f8a68c
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70280359"
---
# <a name="transport-layer-security-tls-12"></a>傳輸層安全性（TLS）1。2

使用最新版本的[_傳輸層安全性_（TLS）](https://en.wikipedia.org/wiki/Transport_Layer_Security)對於確保應用程式網路通訊安全而言非常重要。

> [!WARNING]
> **2018 年4月，** 由於增加的安全性需求（包括 PCI 合規性），主要雲端提供者和網頁伺服器預期會停止支援超過1.2 的 TLS 版本。 在舊版中建立的 Xamarin 專案 Visual Studio 預設為使用舊版的 TLS。
>
> 為了確保您的應用程式能繼續使用這些伺服器和服務，**您應該更新 Xamarin 專案以使用下列設定，然後重新建立應用程式並重新部署**至使用者。

專案必須參考**系統 .net. Http**元件，並加以設定，如下所示。

## <a name="update-xamarinandroid-to-tls-12"></a>將 Xamarin 更新為 TLS 1。2

更新**HttpClient 的執行**和**SSL/TLS 的實**作為選項，以啟用 TLS 1.2 安全性。

> [!NOTE]
> 需要 Android 5.0 或更新版本。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

這些設定可以在 [**專案屬性] > [Android 選項**] 中找到，然後按一下 [ **Advanced** ] 按鈕：

[![在 Visual Studio 中設定 HttpClient 和 TLS](transport-layer-security-images/android-win-sml.png)](transport-layer-security-images/android-win.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

這些設定可在 專案選項 中找到 **> 組建 > Android 組建** 索引標籤：

[![在 Visual Studio for Mac 中設定 HttpClient 和 TLS](transport-layer-security-images/android-mac-sml.png)](transport-layer-security-images/android-mac.png#lightbox)

-----

## <a name="update-xamarinios-to-tls-12"></a>將 Xamarin 更新為 TLS 1。2

更新 HttpClient 的 [**執行**] 選項，以啟用 TSL 1.2 安全性。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

這項設定可以在 [**專案屬性] > [IOS 組建**] 中找到：

[![在 Visual Studio 中設定 HttpClient 和 TLS](transport-layer-security-images/ios-win-sml.png)](transport-layer-security-images/ios-win.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

這項設定可以在 **專案選項 中找到 > 組建 > IOS 組建** 索引標籤：

[![設定 Visual Studio for Mac 中的 HttpClient](transport-layer-security-images/ios-mac-sml.png)](transport-layer-security-images/ios-mac.png#lightbox)

-----

## <a name="update-xamarinmac-to-tls-12"></a>將 Xamarin 更新為 TLS 1。2

在 Visual Studio for Mac 中，若要在 Xamarin. Mac 應用程式中啟用 TLS 1.2，請更新 專案選項 中的 HttpClient **執行** 選項， **> 組建 > Mac 組建**：

[![設定 Visual Studio for Mac 中的 HttpClient](transport-layer-security-images/macos-mac-sml.png)](transport-layer-security-images/macos-mac.png#lightbox)

> [!WARNING]
> 即將推出的 Xamarin.Mac 4.8 版只會支援 macOS 10.9 或更高版本。
> 舊版 Xamarin.Mac 支援 macOS 10.7 或更高版本，但這些較舊的 macOS 版本缺乏足夠的 TLS 基礎結構，無法支援 TLS 1.2。 若要以 macOS 10.7 或 10.8 為目標，請使用 Xamarin.Mac 4.6 或更舊版本。

## <a name="alternative-configuration-options"></a>替代設定選項

本節討論如上所示的 TLS 1.2 支援設定的替代方案。
如果應用程式開發人員瞭解使用不同層級 TLS 支援的風險，則應該只考慮這些替代方案。

### <a name="httpclient-implementation"></a>HttpClient 執行

Xamarin 開發人員一直都能夠在其程式碼中使用原生網路類別，不過也有一個選項可判斷類別所`HttpClient`使用的網路堆疊。 這提供了熟悉的 .NET API，具備原生平臺的速度和安全性優勢。

可選的按鈕有：

- **受控堆疊**– Mono 提供的網路功能，或
- **原生堆疊**–基礎平臺（Android、IOS 或 macOS）所提供的各種網路 api。

受控堆疊可提供與現有 .NET 程式碼最高層級的相容性，不過速度可能較慢，因而導致更大的可執行檔案大小。

原生選項的速度會更快，而且具有較佳的`HttpClient`安全性（包括 TLS 1.2），但可能不會提供類別的所有功能和選項。

### <a name="ssltls-implementation-android"></a>SSL/TLS 實行（Android）

Android 專案選項也可讓您選擇要支援的 SSL/TLS 執行：

- **Mono/受控**– Android 上的 TLS 1。1
- Android 上的**原生**– TLS 1.2。

新的 Xamarin 專案預設為支援 TLS 1.2 的原生執行（建議用於所有專案），不過基於相容性的考慮，您可以視需要切換回 managed 程式碼。

> [!IMPORTANT]
> [IOS 和 Mac](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/xamarin.ios_10/xamarin.ios_10.8.md)專案選項已移除**Mono/Managed**選項。
>
> 原生選項一律會在 iOS 和 Mac 平臺上使用。

## <a name="platform-specific-details"></a>平臺特定詳細資料

上述摘要說明 Xamarin 專案中 HttpClient 和 SSL/TLS 實作為專案層級的設定。 您也可以在程式碼中動態設定 HttpClient 的執行。 如需詳細資訊，請參閱下列平臺特定指南：

- [**Android**](~/android/app-fundamentals/http-stack.md)
- [**iOS 和 Mac**](~/cross-platform/macios/http-stack.md)

## <a name="summary"></a>總結

應用程式應該盡可能使用傳輸層安全性（TLS）1.2。
您應該根據本文中的指示來更新現有應用程式中的設定，然後重新建立並重新部署至您的客戶。

## <a name="related-links"></a>相關連結

- [應用程式傳輸安全性](~/ios/app-fundamentals/ats.md)
- [Xamarin. Android 環境](~/android/deploy-test/environment.md)
- [Xamarin 週期9（2017年2月）](https://releases.xamarin.com/stable-release-cycle-9/)
- [TLS （維琪百科）](https://en.wikipedia.org/wiki/Transport_Layer_Security)
- [Mono 4.8 版本資訊-TLS 1.2 支援](https://www.mono-project.com/docs/about-mono/releases/4.8.0/#tls-12-support)
- [BoringSSL](https://boringssl.googlesource.com/boringssl/)
- [HttpClient、HttpClientHandler 和 WebRequestHandler 說明](https://blogs.msdn.microsoft.com/henrikn/2012/08/07/httpclient-httpclienthandler-and-webrequesthandler-explained/)
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
- [HTTP 用戶端（範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/httpclient/)
