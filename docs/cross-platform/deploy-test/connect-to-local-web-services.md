---
title: 從 iOS 模擬器和 Android 模擬器連線到本機 Web 服務
description: 此文章說明在 iOS 模擬器或 Android 模擬器中執行的 Xamarin 行動應用程式，能夠如何使用在本機執行的 ASP.NET Core Web 服務。
ms.prod: xamarin
ms.assetid: FD8FE199-898B-4841-8041-CC9CA1A00917
author: davidbritch
ms.author: dabritch
ms.date: 02/04/2021
ms.openlocfilehash: 6c9e91d8c434a0deea8c419def7dc3f1800b1d06
ms.sourcegitcommit: 3b6eec7841868f50827271105577ecdc6766c162
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2021
ms.locfileid: "99606572"
---
# <a name="connect-to-local-web-services-from-ios-simulators-and-android-emulators"></a>從 iOS 模擬器和 Android 模擬器連線到本機 web 服務

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/webservices-todorest/)

許多行動應用程式會使用 Web 服務。 開發階段中通常都會在本機部署 Web 服務，並透過在 iOS 模擬器或 Android 模擬器中執行的行動應用程式取用。 這樣就不需將 Web 服務部署至裝載的端點，而且能夠提供簡易的偵錯體驗，因為行動應用程式和 Web 服務都是在本機執行。

在 iOS 模擬器或 Android 模擬器中執行的行動應用程式，可以取用透過 HTTP 公開並在本機執行的 ASP.NET Core Web 服務，如下所示：

- 在 iOS 模擬器中執行的應用程式可以透過電腦的 IP 位址，或透過 `localhost` 主機名稱，連線到本機 HTTP Web 服務。 例如，提供一個透過 `/api/todoitems/` 相對 URI 公開 GET 作業的本機 HTTP Web 服務，在 iOS 模擬器中執行的應用程式就可以透過傳送 GET 要求至 `http://localhost:<port>/api/todoitems/` 來取用作業。
- 在 Android 模擬器中執行的應用程式可以透過 `10.0.2.2` 位址連線到本機 HTTP Web 服務，也就是您的主機回送介面 (在開發電腦上為 `127.0.0.1`) 的別名。 例如，提供一個透過 `/api/todoitems/` 相對 URI 公開 GET 作業的本機 HTTP Web 服務，在 Android 模擬器中執行的應用程式就可以透過傳送 GET 要求至 `http://10.0.2.2:<port>/api/todoitems/` 來取用作業。

但是，必須進行一些額外的工作，在 iOS 模擬器或 Android 模擬器中執行的應用程式才能取用透過 HTTPS 公開的本機 Web 服務。 針對此案例，流程如下：

1. 在您的電腦上建立自我簽署開發憑證。 如需詳細資訊，請參閱[建立開發憑證](#create-a-development-certificate)。
1. 設定您的專案，以針對您的偵錯工具組建使用適當的 `HttpClient` 網路堆疊。 如需詳細資訊，請參閱[設定專案](#configure-your-project)。
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

在 iOS 上執行的 Xamarin 應用程式可以使用受控網路堆疊，或使用原生 `CFNetwork` 或 `NSUrlSession` 網路堆疊。 根據預設，新的 iOS 平台專案使用 `NSUrlSession` 網路堆疊，以支援 TLS 1.2，並使用原生 API 以獲得更好的效能與較小的可執行檔大小。 如需詳細資訊，請參閱[適用於 iOS/macOS 的 HttpClient 和 SSL/TLS 實作選取器](~/cross-platform/macios/http-stack.md)。

### <a name="android"></a>Android

在 Android 上執行的 Xamarin 應用程式可以使用受控 `HttpClient` 網路堆疊，或原生 `AndroidClientHandler` 網路堆疊。 根據預設，新的 Android 平台專案使用 `AndroidClientHandler` 網路堆疊，以支援 TLS 1.2，並使用原生 API 以獲得更好的效能與較小的可執行檔大小。 如需 Android 網路堆疊的詳細資訊，請參閱 [適用于 android 的 HttpClient Stack 和 SSL/TLS 實行選取器](~/android/app-fundamentals/http-stack.md)。

## <a name="specify-the-local-machine-address"></a>指定本機電腦位址

iOS 模擬器和 Android 模擬器都能存取在您本機電腦上執行的安全 Web 服務。 但是每部電腦的本機電腦位址都不一樣。

### <a name="ios"></a>iOS

iOS 模擬器會使用主機電腦網路。 因此，在模擬器中執行的應用程式可以透過電腦 IP 位址，或透過 `localhost` 主機名稱，連線到在您本機電腦上執行的 Web 服務。 例如，提供一個透過 `/api/todoitems/` 相對 URI 公開 GET 作業的本機安全 Web 服務，在 iOS 模擬器中執行的應用程式就可以透過傳送 GET 要求至 `https://localhost:<port>/api/todoitems/` 來使用作業。

> [!NOTE]
> 從 Windows 的 iOS 模擬器中執行行動應用程式時，應用程式會顯示在 [Windows 的遠端 iOS 模擬器](~/tools/ios-simulator/index.md)中。 但是應用程式是在配對的 Mac 上執行。 所以在 Mac 上執行的 iOS 應用程式並沒有 localhost 存取權，無法存取在 Windows 上執行的 Web 服務。

### <a name="android"></a>Android

Android 模擬器的每個執行個體都會與您的開發電腦網路介面隔離，並在虛擬路由器背後執行。 因此，模擬的裝置看不到您的開發電腦，或網路上的其他模擬器執行個體。

但是，每個模擬器的虛擬路由器都會管理一個特殊的網路空間，其中包括預先配置的位址，而 `10.0.2.2` 位址為您主機回送介面的別名 (在您的開發電腦上為 127.0.0.1)。 因此，提供一個透過 `/api/todoitems/` 相對 URI 公開 GET 作業的本機安全 Web 服務，在 Android 模擬器中執行的應用程式就可以透過傳送 GET 要求至 `https://10.0.2.2:<port>/api/todoitems/` 來取用作業。

### <a name="detect-the-operating-system"></a>偵測作業系統

[`DeviceInfo`](xref:Xamarin.Essentials.DeviceInfo)類別可以用來偵測應用程式執行所在的平臺。 然後可以如下所示，設定適當的主機名稱 (讓您能夠存取本機安全 Web 服務)：

```csharp
public static string BaseAddress =
    DeviceInfo.Platform == DevicePlatform.Android ? "https://10.0.2.2:5001" : "https://localhost:5001";
public static string TodoItemsUrl = $"{BaseAddress}/api/todoitems/";
```

如需類別的詳細資訊 `DeviceInfo` ，請參閱 [Xamarin：裝置資訊](~/essentials/device-information.md)。

## <a name="bypass-the-certificate-security-check"></a>繞過憑證安全性檢查

嘗試從在 iOS 模擬器或 Android 模擬器中執行的應用程式叫用本機安全 Web 服務時，將會擲回 `HttpRequestException`，即使是在每個平台上使用受控網路堆疊也一樣。 這是因為本機 HTTPS 開發憑證是自我簽署的，而自我簽署憑證不受 iOS 或 Android 信任。 因此，應用程式在使用本機安全 Web 服務時，必須忽略 SSL 錯誤。 在 iOS 和 Android 上使用 managed 和原生網路堆疊時，可以完成這項作業，方法是將 `ServerCertificateCustomValidationCallback` 物件上的屬性設定 `HttpClientHandler` 為回呼，以忽略本機 HTTPS 開發憑證的憑證安全性檢查結果：

```csharp
// This method must be in a class in a platform project, even if
// the HttpClient object is constructed in a shared project.
public HttpClientHandler GetInsecureHandler()
{
    HttpClientHandler handler = new HttpClientHandler();
    handler.ServerCertificateCustomValidationCallback = (message, cert, chain, errors) =>
    {
        if (cert.Issuer.Equals("CN=localhost"))
            return true;
        return errors == System.Net.Security.SslPolicyErrors.None;
    };
    return handler;
}
```

在此程式碼範例中，當進行驗證的憑證不是 `localhost` 時，就會傳回伺服器憑證驗證結果。 對於此憑證來說，系統會忽略驗證結果並傳回 `true`，指示憑證是否有效。 產生的 `HttpClientHandler` 物件應做為引數傳遞給 debug 組建的函式 `HttpClient` ：

```csharp
#if DEBUG
    HttpClientHandler insecureHandler = GetInsecureHandler();
    HttpClient client = new HttpClient(insecureHandler);
#else
    HttpClient client = new HttpClient();
#endif
```

## <a name="enable-http-clear-text-traffic"></a>啟用 HTTP 純文字流量

（選擇性）您可以將 iOS 和 Android 專案設定為允許純文字 HTTP 流量。 如果後端服務設定為允許 HTTP 流量，您可以在基底 Url 中指定 HTTP，然後設定您的專案以允許純文字流量：

```csharp
public static string BaseAddress =
    DeviceInfo.Platform == DevicePlatform.Android ? "http://10.0.2.2:5000" : "http://localhost:5000";
public static string TodoItemsUrl = $"{BaseAddress}/api/todoitems/";
```

### <a name="ios-ats-opt-out"></a>iOS ATS 退出

若要在 iOS 上啟用純文字本機流量，您應該將下列內容新增至您的 **plist** 檔案，以 [退出宣告 ATS](~/ios/app-fundamentals/ats.md#optout) ：

```xml
<key>NSAppTransportSecurity</key>    
<dict>
    <key>NSAllowsLocalNetworking</key>
    <true/>
</dict>
```

### <a name="android-network-security-configuration"></a>Android 網路安全性設定

若要在 Android 上啟用純文字本機流量，您應該在 **Resources/XML** 資料夾中加入名為 **network_security_config.xml** 的新 XML 檔案，以建立網路安全性設定。 XML 檔案應指定下列設定：

```xml
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
  <domain-config cleartextTrafficPermitted="true">
    <domain includeSubdomains="true">10.0.2.2</domain>
  </domain-config>
</network-security-config>
```

然後，在 Android 資訊清單中的 **應用程式** 節點上設定 **networkSecurityConfig** 屬性：

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest>
    <application android:networkSecurityConfig="@xml/network_security_config">
        ...
    </application>
</manifest>
```

## <a name="related-links"></a>相關連結

- [TodoREST (範例)](/samples/xamarin/xamarin-forms-samples/webservices-todorest/) \(英文\)
- [啟用本機 HTTPS](/aspnet/core/getting-started#enable-local-https)
- [適用於 iOS/macOS 的 HttpClient 和 SSL/TLS 實作選取器](~/cross-platform/macios/http-stack.md)
- [適用於 Android 的 HttpClient 堆疊和 SSL/TLS 實作選擇器](~/android/app-fundamentals/http-stack.md)
- [Android 網路安全性設定](https://devblogs.microsoft.com/xamarin/cleartext-http-android-network-security/)
- [iOS 應用程式傳輸安全性](~/ios/app-fundamentals/ats.md)
- [Xamarin.Essentials：裝置資訊](~/essentials/device-information.md)
