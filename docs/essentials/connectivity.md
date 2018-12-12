---
title: 'Xamarin.Essentials: Connectivity'
description: Xamarin.Essentials 中的 Connectivity 類別可讓您監視裝置網路狀況的變更、檢查目前的網路存取，以及目前連線方式。
ms.assetid: E1B1F152-B1D5-4227-965E-C0AEBF528F49
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 3c29fc85d20e3a4d91a1ae63feca1cb668af141e
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2018
ms.locfileid: "52899015"
---
# <a name="xamarinessentials-connectivity"></a>Xamarin.Essentials: Connectivity

**Connectivity** 類別可讓您監視裝置網路狀況的變更、檢查目前的網路存取，以及目前連線方式。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

若要存取 **Connectivity** 功能，需要下列平台特定設定。

# <a name="androidtabandroid"></a>[Android](#tab/android)

需要 `AccessNetworkState` 權限，而且必須在 Android 專案中設定。 能以下列方式新增：

開啟 [Properties] 資料夾下的 **AssemblyInfo.cs** 檔案並新增：

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.AccessNetworkState)]
```

或更新 Android 資訊清單：

開啟 [Properties] 資料夾下的 **AndroidManifest.xml** 檔案並在 [manifest] 節點內新增下列內容。

```xml
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

禍以滑鼠右鍵按一 Android 專案並開啟專案的屬性。 在 [Android 資訊清單] 下，尋找 [必要權限] 區域並檢查 [存取網路狀態] 權限。 這將會自動更新 **AndroidManifest.xml** 檔案。

# <a name="iostabios"></a>[iOS](#tab/ios)

不需要進行額外設定。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

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

    void Connectivity_ConnectivityChanged(object sender, ConnectivityChangedEventArgs  e)
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
