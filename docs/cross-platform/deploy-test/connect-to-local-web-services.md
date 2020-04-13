---
title: 從 iOS 模擬器和 Android 模擬器連線到本機 Web 服務
description: 此文章說明在 iOS 模擬器或 Android 模擬器中執行的 Xamarin 行動應用程式，能夠如何使用在本機執行的 ASP.NET Core Web 服務。
ms.prod: xamarin
ms.assetid: FD8FE199-898B-4841-8041-CC9CA1A00917
author: davidbritch
ms.author: dabritch
ms.date: 10/16/2019
ms.openlocfilehash: 29261f2ef6366c0dac8ac82e63584366a5cca0b0
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "74135276"
---
# <a name="connect-to-local-web-services-from-ios-simulators-and-android-emulators"></a>從 iOS 模擬器和 Android 模擬器連線到本機 Web 服務

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest/)

許多行動應用程式會使用 Web 服務。 開發階段中通常都會在本機部署 Web 服務，並透過在 iOS 模擬器或 Android 模擬器中執行的行動應用程式取用。 這樣就不需將 Web 服務部署至裝載的端點，而且能夠提供簡易的偵錯體驗，因為行動應用程式和 Web 服務都是在本機執行。

在 iOS 模擬器或 Android 模擬器中執行的行動應用程式，可以取用透過 HTTP 公開並在本機執行的 ASP.NET Core Web 服務，如下所示：

- 在 iOS 模擬器中執行的應用程式可以透過電腦的 IP 位址，或透過 `localhost` 主機名稱，連線到本機 HTTP Web 服務。 例如，提供一個透過 `/api/todoitems/` 相對 URI 公開 GET 作業的本機 HTTP Web 服務，在 iOS 模擬器中執行的應用程式就可以透過傳送 GET 要求至 `http://localhost:<port>/api/todoitems/` 來取用作業。
- 在 Android 模擬器中執行的應用程式可以透過 `10.0.2.2` 位址連線到本機 HTTP Web 服務，也就是您的主機回送介面 (在開發電腦上為 `127.0.0.1`) 的別名。 例如，提供一個透過 `/api/todoitems/` 相對 URI 公開 GET 作業的本機 HTTP Web 服務，在 Android 模擬器中執行的應用程式就可以透過傳送 GET 要求至 `http://10.0.2.2:<port>/api/todoitems/` 來取用作業。

但是，必須進行一些額外的工作，在 iOS 模擬器或 Android 模擬器中執行的應用程式才能取用透過 HTTPS 公開的本機 Web 服務。 針對此案例，流程如下：

1. 在您的電腦上建立自我簽署開發憑證。 如需詳細資訊，請參閱[建立開發憑證](#create-a-development-certificate)。
1. 將專案配置為在調試生成中使用`HttpClient`適當的網路堆疊。 如需詳細資訊，請參閱[設定專案](#configure-your-project)。
1. 指定本機電腦的位址。 如需詳細資訊，請參閱[指定本機電腦位址](#specify-the-local-machine-address)。
1. 繞過本機開發憑證安全性檢查。 如需詳細資訊，請參閱[繞過憑證安全性檢查](#bypass-the-certificate-security-check)。

我們會輪流討論每個項目。

## <a name="create-a-development-certificate"></a>建立開發憑證

安裝 .NET Core SDK 會將 ASP.NET Core HTTPS 開發憑證安裝至本機使用者憑證存放區。 但是，雖然已安裝憑證，但憑證不受信任。 若要信任憑證，請執行下面的一次性步驟，以執行 dotnet `dev-certs` 工具：

```dotnetcli
dotnet dev-certs https --trust
```

下列命令會提供 `dev-certs` 工具的說明：

```dotnetcli
dotnet dev-certs https --help
```

或者，當您使用 HTTPS 執行 ASP.NET Core 2.1 專案 (或更新版本) 時，Visual Studio 會偵測開發憑證是否遺失，且將提供安裝並信任該憑證。

> [!NOTE]
> ASP.NET Core HTTPS 開發憑證是自我簽署的。

如需在電腦上啟用本機 HTTPS 的詳細資訊，請參閱[啟用本機 HTTPS](/aspnet/core/getting-started#enable-local-https)。

## <a name="configure-your-project"></a>設定您的專案

在 iOS 和 Android 上執行的 Xamarin 應用程式可以指定 `HttpClient` 類別使用哪些網路堆疊，並提供使用受控網路堆疊，或使用原生網路堆疊的選項。 受控堆疊提供高層級相容性，具備現有 .NET 程式碼，但僅限於 TLS 1.0 且執行速度可能會比較慢，並產生較大的可執行檔大小。 原生堆疊速度可以更快並提供更高的安全性，但可能不會提供 `HttpClient` 類別的所有功能。

### <a name="ios"></a>iOS

在 iOS 上執行的 Xamarin 應用程式可以使用受控網路堆疊，或使用原生 `CFNetwork` 或 `NSUrlSession` 網路堆疊。 根據預設，新的 iOS 平台專案使用 `NSUrlSession` 網路堆疊，以支援 TLS 1.2，並使用原生 API 以獲得更好的效能與較小的可執行檔大小。

但是，當應用程式需要連線在本機執行的安全 Web 服務時，使用受控網路堆疊可讓開發人員更容易進行測試。 因此，建議您設定偵錯模擬器組建設定檔，以使用受控網路堆疊，以及設定發行組建設定檔，以使用 `NSUrlSession` 網路堆疊。 每個網路堆疊都可以用程式設計方式，或透過專案選項中的選取器進行設定。 如需詳細資訊，請參閱[適用於 iOS/macOS 的 HttpClient 和 SSL/TLS 實作選取器](~/cross-platform/macios/http-stack.md)。

### <a name="android"></a>Android

在 Android 上執行的 Xamarin 應用程式可以使用受控 `HttpClient` 網路堆疊，或原生 `AndroidClientHandler` 網路堆疊。 根據預設，新的 Android 平台專案使用 `AndroidClientHandler` 網路堆疊，以支援 TLS 1.2，並使用原生 API 以獲得更好的效能與較小的可執行檔大小。 有關 Android 網路堆疊的詳細資訊,請參閱[適用於 Android 的 HttpClient 堆疊和 SSL/TLS 實現選擇器](~/android/app-fundamentals/http-stack.md)。

## <a name="specify-the-local-machine-address"></a>指定本機電腦位址

iOS 模擬器和 Android 模擬器都能存取在您本機電腦上執行的安全 Web 服務。 但是每部電腦的本機電腦位址都不一樣。

### <a name="ios"></a>iOS

iOS 模擬器會使用主機電腦網路。 因此，在模擬器中執行的應用程式可以透過電腦 IP 位址，或透過 `localhost` 主機名稱，連線到在您本機電腦上執行的 Web 服務。 例如，提供一個透過 `/api/todoitems/` 相對 URI 公開 GET 作業的本機安全 Web 服務，在 iOS 模擬器中執行的應用程式就可以透過傳送 GET 要求至 `https://localhost:<port>/api/todoitems/` 來使用作業。

> [!NOTE]
> 從 Windows 的 iOS 模擬器中執行行動應用程式時，應用程式會顯示在 [Windows 的遠端 iOS 模擬器](~/tools/ios-simulator/index.md)中。 但是應用程式是在配對的 Mac 上執行。 所以在 Mac 上執行的 iOS 應用程式並沒有 localhost 存取權，無法存取在 Windows 上執行的 Web 服務。

### <a name="android"></a>Android

Android 模擬器的每個執行個體都會與您的開發電腦網路介面隔離，並在虛擬路由器背後執行。 因此，模擬的裝置看不到您的開發電腦，或網路上的其他模擬器執行個體。

但是，每個模擬器的虛擬路由器都會管理一個特殊的網路空間，其中包括預先配置的位址，而 `10.0.2.2` 位址為您主機回送介面的別名 (在您的開發電腦上為 127.0.0.1)。 因此，提供一個透過 `/api/todoitems/` 相對 URI 公開 GET 作業的本機安全 Web 服務，在 Android 模擬器中執行的應用程式就可以透過傳送 GET 要求至 `https://10.0.2.2:<port>/api/todoitems/` 來取用作業。

### <a name="xamarinforms-example"></a>Xamarin.Forms 範例

在 Xamarin.Forms 應用程式[`Device`](xref:Xamarin.Forms.Device)中, 該類可用於檢測應用程式正在運行的平臺。 然後可以如下所示，設定適當的主機名稱 (讓您能夠存取本機安全 Web 服務)：

```csharp
public static string BaseAddress =
    Device.RuntimePlatform == Device.Android ? "https://10.0.2.2:5001" : "https://localhost:5001";
public static string TodoItemsUrl = $"{BaseAddress}/api/todoitems/";
```

## <a name="bypass-the-certificate-security-check"></a>繞過憑證安全性檢查

嘗試從在 iOS 模擬器或 Android 模擬器中執行的應用程式叫用本機安全 Web 服務時，將會擲回 `HttpRequestException`，即使是在每個平台上使用受控網路堆疊也一樣。 這是因為本機 HTTPS 開發憑證是自我簽署的，而自我簽署憑證不受 iOS 或 Android 信任。

因此，應用程式在使用本機安全 Web 服務時，必須忽略 SSL 錯誤。 目前,在 iOS 和 Android 上,實現此目的的機制不同。

### <a name="ios"></a>iOS

在 iOS 上,在使用託管網路堆疊時,可以`ServicePointManager.ServerCertificateValidationCallback`通過將 屬性設定為忽略本地 HTTPS 開發證書的憑證安全檢查結果的回調來忽略 SSL 錯誤:

```csharp
#if DEBUG
    System.Net.ServicePointManager.ServerCertificateValidationCallback += (sender, certificate, chain, sslPolicyErrors) =>
    {
        if (certificate.Issuer.Equals("CN=localhost"))
            return true;
        return sslPolicyErrors == System.Net.Security.SslPolicyErrors.None;
    };
#endif
```

在此程式碼範例中，當進行驗證的憑證不是 `localhost` 時，就會傳回伺服器憑證驗證結果。 對於此憑證來說，系統會忽略驗證結果並傳回 `true`，指示憑證是否有效。 在`AppDelegate.FinishedLaunching``LoadApplication(new App())`方法調用之前,應將此代碼添加到 iOS 上的方法。

> [!NOTE]
> iOS 上的本機網路堆疊不會掛入`ServerCertificateValidationCallback`。

### <a name="android"></a>Android

在 Android 上,在使用託管網路和本`AndroidClientHandler`機 網路堆疊時,`ServerCertificateCustomValidationCallback``HttpClientHandler`可以通過將 物件的屬性設定為忽略本地 HTTPS 開發證書的證書安全檢查結果的回調來忽略本地安全 Web 服務上的 SSL 錯誤:

```csharp
public HttpClientHandler GetInsecureHandler()
{
    var handler = new HttpClientHandler();
    handler.ServerCertificateCustomValidationCallback = (message, cert, chain, errors) =>
    {
        if (cert.Issuer.Equals("CN=localhost"))
            return true;
        return errors == System.Net.Security.SslPolicyErrors.None;
    };
    return handler;
}
```

在此程式碼範例中，當進行驗證的憑證不是 `localhost` 時，就會傳回伺服器憑證驗證結果。 對於此憑證來說，系統會忽略驗證結果並傳回 `true`，指示憑證是否有效。 生成的`HttpClientHandler`物件應作為參數傳遞`HttpClient`給建構函數。

## <a name="related-links"></a>相關連結

- [TodoREST (範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest/) \(英文\)
- [開啟本地 HTTPS](/aspnet/core/getting-started#enable-local-https)
- [適用於 iOS/macOS 的 HttpClient 和 SSL/TLS 實作選取器](~/cross-platform/macios/http-stack.md)
- [適用於 Android 的 HttpClient 堆疊和 SSL/TLS 實作選擇器](~/android/app-fundamentals/http-stack.md)
