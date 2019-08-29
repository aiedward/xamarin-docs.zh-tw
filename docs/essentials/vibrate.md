---
title: Xamarin.Essentials:震動
description: 本文件描述 Xamarin.Essentials 中的震動類別，可讓您在所需的時間內啟動和停止震動功能。
ms.assetid: 7E8B24C4-2625-4DAE-A129-383542D34F1E
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 2e4cf713f9ad7478c0d8e288fd3beff4b5015ef5
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70120107"
---
# <a name="xamarinessentials-vibration"></a>Xamarin.Essentials:震動

**震動**類別可讓您在所需的時間內啟動和停止震動功能。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

若要存取**震動**功能，需要下列平台特定設定。

# <a name="androidtabandroid"></a>[Android](#tab/android)

需要震動權限，而且必須在 Android 專案中設定。 能以下列方式新增：

開啟 [Properties]  資料夾下的 **AssemblyInfo.cs** 檔案並新增：

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Vibrate)]
```

或更新 Android 資訊清單：

開啟 [Properties]  資料夾下的 **AndroidManifest.xml** 檔案並在 [manifest]  節點內新增下列內容。

```xml
<uses-permission android:name="android.permission.VIBRATE" />
```

禍以滑鼠右鍵按一 Android 專案並開啟專案的屬性。 在 [Android 資訊清單]  下，尋找 [必要權限]  區域並選取 [震動]  權限。 這將會自動更新 **AndroidManifest.xml** 檔案。

# <a name="iostabios"></a>[iOS](#tab/ios)

不需要進行額外設定。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

無平台差異。

-----

## <a name="using-vibration"></a>使用震動

在類別中新增對 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

可以要求震動功能一段時間或預設的 500 毫秒。

```csharp
try
{
    // Use default vibration length
    Vibration.Vibrate();

    // Or use specified time
    var duration = TimeSpan.FromSeconds(1);
    Vibration.Vibrate(duration);
}
catch (FeatureNotSupportedException ex)
{
    // Feature not supported on device
}
catch (Exception ex)
{
    // Other error has occurred.
}
```

可以使用 `Cancel` 方法要求取消裝置震動：

```csharp
try
{
    Vibration.Cancel();
}
catch (FeatureNotSupportedException ex)
{
    // Feature not supported on device
}
catch (Exception ex)
{
    // Other error has occurred.
}
```

## <a name="platform-differences"></a>平台差異

# <a name="androidtabandroid"></a>[Android](#tab/android)

無平台差異。

# <a name="iostabios"></a>[iOS](#tab/ios)

- 僅在裝置設定為「鈴響時震動」時震動。
- 一律震動 500 毫秒。
- 無法取消震動。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

無平台差異。

-----

## <a name="api"></a>API

- [震動原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Vibration)
- [震動 API 文件](xref:Xamarin.Essentials.Vibration)
