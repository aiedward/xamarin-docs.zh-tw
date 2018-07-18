---
title: Xamarin.Essentials： 連線
description: Xamarin.Essentials 連線類別可讓您監視裝置的網路狀況中的變更，請檢查目前的網路存取，與目前連接方式。
ms.assetid: E1B1F152-B1D5-4227-965E-C0AEBF528F49
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 54c165e15e725caaecb1573b74cfe295170db141
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2018
ms.locfileid: "38848605"
---
# <a name="xamarinessentials-connectivity"></a>Xamarin.Essentials： 連線

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**連線**類別可讓您監視的變更，在裝置的網路情況下，檢查目前的網路存取，與目前連接方式。

## <a name="getting-started"></a>快速入門

若要存取**連線**須有下列的平台特定設定的功能。

# <a name="androidtabandroid"></a>[Android](#tab/android)

`AccessNetworkState`權限是必要的而且必須設定 Android 專案中。 這可以透過下列方式新增：

開啟**AssemblyInfo.cs**下方的檔案**屬性**資料夾，並新增：

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.AccessNetworkState)]
```

或更新 Android 資訊清單：

開啟**AndroidManifest.xml**下方檔案**屬性**資料夾，並新增下列內**資訊清單**節點。

```xml
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

或 Anroid 專案上按一下滑鼠右鍵，然後開啟專案的內容。 底下**Android 資訊清單**尋找**必要權限：** 區域，並檢查**存取網路狀態**權限。 這樣會自動更新**AndroidManifest.xml**檔案。

# <a name="iostabios"></a>[iOS](#tab/ios)

不需要其他設定。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

不需要其他設定。

-----

## <a name="using-connectivity"></a>使用連線

在您的類別加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

檢查目前的網路存取權：

```csharp
var current = Connectivity.NetworkAccess;

if (current == NetworkAccess.Internet)
{
    // Connection to internet is available
}
```

[網路存取](xref:Xamarin.Essentials.NetworkAccess)分為下列類別：

* **網際網路**-本機和網際網路存取。
* **ConstrainedInternet** – 受限的網際網路存取。 指出網頁驗證入口網站的連線能力，其中提供入口網站的本機存取，但存取網際網路需要透過入口網站提供了特定的憑證。
* **本機**– 本機網路僅限存取。
* **無**– 沒有連線可用。
* **未知**– 無法判定網際網路連線。

您可以檢查何種[連線設定檔](xref:Xamarin.Essentials.ConnectionProfile)正在使用的裝置：

```csharp
var profiles = Connectivity.Profiles;
if (profiles.Contains(ConnectionProfile.WiFi))
{
    // Active Wi-Fi connection.
}
```

每當連線設定檔或網路存取的變更，您可以接收事件時觸發：

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

請務必請注意，您可以可`Internet`回報`NetworkAccess`但無法使用完整 web 存取權。 每個平台連線的運作方式，因為它只能保證的連接可用。 比方說裝置可能會連線到 Wi-fi 網路，但與網際網路中斷連線的路由器。 在這個執行個體可能會報告網際網路，但作用中連線無法使用。

## <a name="api"></a>API

* [連線來源的程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Connectivity)
* [連線 API 文件](xref:Xamarin.Essentials.Connectivity)
