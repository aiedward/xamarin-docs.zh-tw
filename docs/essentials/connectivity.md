---
title: Xamarin.Essentials： 連線
description: 連線中的類別 Xamarin.Essentials 可讓您監視裝置的網路狀況中的變更，請檢查目前的網路存取權，以及目前連接方式。
ms.assetid: E1B1F152-B1D5-4227-965E-C0AEBF528F49
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 54c165e15e725caaecb1573b74cfe295170db141
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782862"
---
# <a name="xamarinessentials-connectivity"></a>Xamarin.Essentials： 連線

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**連線**類別可讓您監視以變更裝置的網路狀況，檢查目前的網路存取權，以及目前連接方式。

## <a name="getting-started"></a>快速入門

若要存取**連線**還需要下列平台的特定安裝程式的功能。

# <a name="androidtabandroid"></a>[Android](#tab/android)

`AccessNetworkState`需要權限，而且必須設定的 Android 專案中。 這可以透過下列方式加入：

開啟**AssemblyInfo.cs**底下**屬性**資料夾並加入：

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.AccessNetworkState)]
```

或更新 Android 資訊清單：

開啟**AndroidManifest.xml**底下**屬性**資料夾，然後將下列內部**資訊清單**節點。

```xml
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

或 Anroid 專案上按一下滑鼠右鍵，然後開啟專案的屬性。 在下**Android 資訊清單**尋找**必要的權限：** 區域，然後核取**存取網路狀態**權限。 這會自動更新**AndroidManifest.xml**檔案。

# <a name="iostabios"></a>[iOS](#tab/ios)

不需要其他設定。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

不需要其他設定。

-----

## <a name="using-connectivity"></a>使用連線能力

在您類別中加入 Xamarin.Essentials 的參考：

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

[網路存取](xref:Xamarin.Essentials.NetworkAccess)分成下列類別：

* **網際網路**– 本機和網際網路存取。
* **ConstrainedInternet** – 限制存取網際網路。 指出客群入口網站的連線，其中提供本機存取 web 入口網站，但存取網際網路，需要透過入口網站提供的特定認證。
* **本機**– 本機網路只能存取。
* **無**– 沒有連線為止。
* **未知**– 無法判定網際網路連線。

您可以檢查哪種類型的[連線設定檔](xref:Xamarin.Essentials.ConnectionProfile)正在使用裝置：

```csharp
var profiles = Connectivity.Profiles;
if (profiles.Contains(ConnectionProfile.WiFi))
{
    // Active Wi-Fi connection.
}
```

每當連線設定檔或網路存取變更可以接收事件時觸發：

```csharp
public class ConnectivityTest
{
    public ConnectivityTest()
    {
        // Register for connectivity changes, be sure to unsubscribe when finished
        Connectivity.ConnectivityChanged += Connectivity_ConnectivityChanged;
    }

    void Connectivity_ConnectivityChanged(ConnectivityChangedEventArgs  e)
    {
        var access = e.NetworkAccess;
        var profiles = e.Profiles;
    }
}
```

## <a name="limitations"></a>限制

它是很重要的一點是可能的`Internet`回報`NetworkAccess`但無法使用完整 web 存取權。 連線的每個平台的運作方式，因為它只可以保證的連接可用。 裝置可能會執行個體連接到 Wi-fi 網路，但與網際網路中斷連線的路由器。 在這個執行個體可能會報告網際網路，但作用中連線無法使用。

## <a name="api"></a>API

* [連線來源的程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Connectivity)
* [連接 API 文件](xref:Xamarin.Essentials.Connectivity)
