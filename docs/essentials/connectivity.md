---
title: 'Xamarin.Essentials: Connectivity'
description: Xamarin.Essentials 中的 Connectivity 類別可讓您監視裝置網路狀況的變更、檢查目前的網路存取，以及目前連線方式。
ms.assetid: E1B1F152-B1D5-4227-965E-C0AEBF528F49
author: jamesmontemagno
ms.author: jamont
ms.date: 01/08/2019
ms.custom: video
ms.openlocfilehash: c70510f7b47f93c6119532b6a1c06f6c2e9e56ea
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "67855764"
---
# <a name="xamarinessentials-connectivity"></a>Xamarin.Essentials: Connectivity

**Connectivity** 類別可讓您監視裝置網路狀況的變更、檢查目前的網路存取，以及目前連線方式。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

若要存取 **Connectivity** 功能，需要下列平台特定設定。

# <a name="android"></a>[Android](#tab/android)

需要 `AccessNetworkState` 權限，而且必須在 Android 專案中設定。 能以下列方式新增：

開啟 [Properties]**** 資料夾下的 **AssemblyInfo.cs** 檔案並新增：

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.AccessNetworkState)]
```

或更新 Android 資訊清單：

在 **「屬性」** 資料夾下打開**AndroidManifest.xml**檔,並在**清單**節點內添加以下內容。

```xml
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

禍以滑鼠右鍵按一 Android 專案並開啟專案的屬性。 在 [Android 資訊清單]**** 下，尋找 [必要權限]**** 區域並檢查 [存取網路狀態]**** 權限。 這將會自動更新 **AndroidManifest.xml** 檔案。

# <a name="ios"></a>[iOS](#tab/ios)

不需要進行額外設定。

# <a name="uwp"></a>[UWP](#tab/uwp)

不需要進行額外設定。

-----

## <a name="using-connectivity"></a>使用 Connectivity

在類別中新增對 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

檢查目前的網路存取：

```csharp
var current = Connectivity.NetworkAccess;

if (current == NetworkAccess.Internet)
{
    // Connection to internet is available
}
```

[網路存取](xref:Xamarin.Essentials.NetworkAccess)可分為下列類別：

* **網際網路** – 本機與網際網路存取。
* **ConstrainedInternet** – 受限網際網路存取。 指出 網頁驗證與入口網站連線能力，其中提供對 Web 入口網站的本機存取，但對網際網路的存取要求該特定認證必須透過入口網站提供。
* **本機** –僅限本機網路存取。
* **無** – 無連線能力。
* **未知** – 無法判斷網際網路連線能力。

您可以檢查裝置目前正在使用哪種類型的[連線設定檔](xref:Xamarin.Essentials.ConnectionProfile)：

```csharp
var profiles = Connectivity.ConnectionProfiles;
if (profiles.Contains(ConnectionProfile.WiFi))
{
    // Active Wi-Fi connection.
}
```

每當連線設定檔或網路存取變更時，您都會在觸發時收到事件：

```csharp
public class ConnectivityTest
{
    public ConnectivityTest()
    {
        // Register for connectivity changes, be sure to unsubscribe when finished
        Connectivity.ConnectivityChanged += Connectivity_ConnectivityChanged;
    }

    void Connectivity_ConnectivityChanged(object sender, ConnectivityChangedEventArgs e)
    {
        var access = e.NetworkAccess;
        var profiles = e.ConnectionProfiles;
    }
}
```

## <a name="limitations"></a>限制

請務必注意，`NetworkAccess` 有可能回報 `Internet`，但對 Web 的完整存取不可用。 由於連線能力在每個平台上的運作方式，它只能保證連線可用。 例如，裝置可能連線到 Wi-Fi 網路，但路由器與網際網路之間的連線中斷。 在此情況中，可能回報網際網路，但沒有作用中連線可用。

## <a name="api"></a>API

* [Connectivity 原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Connectivity)
* [Connectivity API 文件](xref:Xamarin.Essentials.Connectivity)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Connectivity-Essential-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
