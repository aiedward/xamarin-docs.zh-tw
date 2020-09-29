---
title: Xamarin.Essentials： Haptic 意見反應
description: 本檔說明中的 HapticFeedback 類別 Xamarin.Essentials ，可讓您控制裝置上的 haptic 意見反應。
ms.assetid: 4462936c-4018-443b-906d-d63da6d0ed7d
author: dimonovdd
ms.author: jamont
ms.date: 09/22/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 129501b5b622901d4347daf60d3cee6579bb115a
ms.sourcegitcommit: 744f977b0595f489c592e29c8a3ba548fde02b6f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/28/2020
ms.locfileid: "91414747"
---
# <a name="no-locxamarinessentials-haptic-feedback"></a>Xamarin.Essentials： Haptic 意見反應

**HapticFeedback**類別可讓您控制裝置上的 haptic 意見反應。

![發行前 API](~/media/shared/preview.png)

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

若要存取**震動**功能，需要下列平台特定設定。

# <a name="android"></a>[Android](#tab/android)

需要震動權限，而且必須在 Android 專案中設定。 能以下列方式新增：

開啟 [Properties]**** 資料夾下的 **AssemblyInfo.cs** 檔案並新增：

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Vibrate)]
```

或更新 Android 資訊清單：

開啟 [ **Properties** ] 資料夾底下的**AndroidManifest.xml**檔案，並在**資訊清單**節點內新增下列內容。

```xml
<uses-permission android:name="android.permission.VIBRATE" />
```

禍以滑鼠右鍵按一 Android 專案並開啟專案的屬性。 在 [Android 資訊清單]**** 下，尋找 [必要權限]**** 區域並選取 [震動]**** 權限。 這將會自動更新 **AndroidManifest.xml** 檔案。

# <a name="ios"></a>[iOS](#tab/ios)

不需要進行額外設定。

# <a name="uwp"></a>[UWP](#tab/uwp)

無平台差異。

-----

## <a name="using-haptic-feedback"></a>使用 Haptic 意見反應

Xamarin.Essentials在您的類別中新增的參考：

```csharp
using Xamarin.Essentials;
```

您可以使用或意見反應類型來執行 Haptic 意見反應功能 `Click` `LongPress` 。

```csharp
try
{
    // Perform click feedback
    HapticFeedback.Perform(HapticFeedbackType.Click);

    // Or use long press    
    HapticFeedback.Perform(HapticFeedbackType.LongPress);
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

## <a name="api"></a>API

- [HapticFeedback 來源程式碼](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/HapticFeedback)
- [HapticFeedback API 檔](xref:Xamarin.Essentials.HapticFeedback)