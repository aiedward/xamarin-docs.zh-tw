---
title: Xamarin.Essentials： 震動
description: 本文件說明在 Xamarin.Essentials，可讓您啟動和停止震動功能所需的一段時間的震動類別。
ms.assetid: 7E8B24C4-2625-4DAE-A129-383542D34F1E
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: ca21f43631c261cd384f9049f30f0fa29e2ca44e
ms.sourcegitcommit: ec50c626613f2f9af51a9f4a52781129bcbf3fcb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2018
ms.locfileid: "37855165"
---
# <a name="xamarinessentials-vibration"></a>Xamarin.Essentials： 震動

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**震動**類別可讓您啟動和停止震動功能所需的一段時間。

## <a name="getting-started"></a>快速入門

若要存取**震動**須有下列的平台特定設定的功能。

# <a name="androidtabandroid"></a>[Android](#tab/android)

震動權限是必要的而且必須設定 Android 專案中。 這可以透過下列方式新增：

開啟**AssemblyInfo.cs**下方的檔案**屬性**資料夾，並新增：

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Vibrate)]
```

或更新 Android 資訊清單：

開啟**AndroidManifest.xml**下方檔案**屬性**資料夾，並新增下列內**資訊清單**節點。

```xml
<uses-permission android:name="android.permission.VIBRATE" />
```

或 Anroid 專案上按一下滑鼠右鍵，然後開啟專案的內容。 底下**Android 資訊清單**尋找**必要權限：** 區域，並檢查**震動**權限。 這樣會自動更新**AndroidManifest.xml**檔案。

# <a name="iostabios"></a>[iOS](#tab/ios)

不需要其他設定。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

不需要其他設定。

-----

## <a name="using-vibration"></a>使用震動

在您的類別加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

設定量的時間或 500 毫秒的預設值，您可以要求震動功能。

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

可以使用要求的裝置震動的取消`Cancel`方法：

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

任何平台差異。

# <a name="iostabios"></a>[iOS](#tab/ios)

* 只有組裝置設定為 「 在通道上的震動"時。
* 一律組 500 毫秒。
* 無法取消震動。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

任何平台差異。

-----

## <a name="api"></a>API

- [震動原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Vibration)
- [震動 API 文件](xref:Xamarin.Essentials.Vibration)
