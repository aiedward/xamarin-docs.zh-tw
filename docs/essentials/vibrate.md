---
title: Xamarin.Essentials 震動
description: 震動類別可讓您啟動和停止 vibrate 功能所需的一段時間。
ms.assetid: 7E8B24C4-2625-4DAE-A129-383542D34F1E
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 68b064d9824a82ea733c7c8bef0c2d43f0a04283
ms.sourcegitcommit: 3e05b135b6ff0d607bc2378c1b6e66d2eebbcc3e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2018
---
# <a name="xamarinessentials-vibration"></a>Xamarin.Essentials 震動

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**震動**類別可讓您啟動和停止 vibrate 功能所需的一段時間。

## <a name="getting-started"></a>快速入門

若要存取**震動**還需要下列平台的特定安裝程式的功能。

# <a name="androidtabandroid"></a>[Android](#tab/android)

Vibrate 權限是必要的而且必須設定 Android 專案中。 這可以透過下列方式加入：

開啟**AssemblyInfo.cs**底下**屬性**資料夾並加入：

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Vibrate)]
```

或更新 Android 資訊清單：

開啟**AndroidManifest.xml**底下**屬性**資料夾，然後將下列內部**資訊清單**節點。

```xml
<uses-permission android:name="android.permission.VIBRATE" />
```

或 Anroid 專案上按一下滑鼠右鍵，然後開啟專案的屬性。 在下**Android 資訊清單**尋找**必要的權限：** 區域，然後核取**VIBRATE**權限。 這會自動更新**AndroidManifest.xml**檔案。

# <a name="iostabios"></a>[iOS](#tab/ios)

不需要其他設定。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

不需要其他設定。

-----

## <a name="using-vibration"></a>使用震動

在您類別中加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

震動功能可以要求的時間或預設值是 500 毫秒。

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

可以以要求取消的裝置震動`Cancel`方法：

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

| 平台 | 差異 |
| --- | --- |
| iOS | 一律組 500 毫秒。 |
| iOS | 若要取消震動不可能。 |

## <a name="api"></a>API

- [震動原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Vibration)
- [震動 API 文件](xref:Xamarin.Essentials.Vibration)
