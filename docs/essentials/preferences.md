---
title: Xamarin.Essentials:偏好設定
description: 本文件描述 Xamarin.Essentials 中的喜好設定類別，可將應用程式喜好設定儲存在鍵/值存放區中。 會討論如何使用類別和可以儲存的資料類型。
ms.assetid: AA81BCBD-79BA-448F-942B-BA4415CA50FF
author: jamesmontemagno
ms.author: jamont
ms.date: 01/15/2019
ms.custom: video
ms.openlocfilehash: 6d57b5ce9cb61363eef24b230f6cf71894f66198
ms.sourcegitcommit: 53f5e83f4e246be703917d7cc719c8cc959517ab
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2019
ms.locfileid: "54317878"
---
# <a name="xamarinessentials-preferences"></a>Xamarin.Essentials:偏好設定

**喜好設定**類別可協助將應用程式喜好設定儲存於鍵/值存放區中。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-preferences"></a>使用喜好設定

在類別中新增對 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

將指定「鍵」的值儲存在喜好設定中：

```csharp
Preferences.Set("my_key", "my_value");
```

從喜好設定或預設值 (若未設定) 擷取值：

```csharp
var myValue = Preferences.Get("my_key", "default_value");
```

從喜好設定中移除「鍵」：

```csharp
Preferences.Remove("my_key");
```

移除所有喜好設定：

```csharp
Preferences.Clear();
```

除了這些方法之外，每個方法都有一個選擇性 `sharedName`，可用於建立其他容器以供選擇。 請閱讀以下平台實作細節。

## <a name="supported-data-types"></a>支援的資料類型

**喜好設定**支援下列資料類型：

- **bool**
- **double**
- **int**
- **float**
- **long**
- **string**
- **DateTime**

## <a name="implementation-details"></a>實作詳細資料

`DateTime` 的值會使用 `DateTime` 類別定義的兩種方法，以 64 位元二進位 (長整數) 格式儲存：[`ToBinary`](xref:System.DateTime.ToBinary) 方法用於編碼 `DateTime` 的值，[`FromBinary`](xref:System.DateTime.FromBinary(System.Int64)) 方法則用於解碼該值。 請參閱這些方法的文件，以便在已儲存的 `DateTime` 並非國際標準時間 (UTC) 值時，對解碼值進行調整。

## <a name="platform-implementation-specifics"></a>平台實作特性

# <a name="androidtabandroid"></a>[Android](#tab/android)

所有資料都會儲存於[共用的喜好設定](https://developer.android.com/training/data-storage/shared-preferences.html)中。 如果未指定 `sharedName`，則會使用預設的共用喜好設定，否則該名稱會用來取得具有指定名稱的**私人**共用喜好設定。

# <a name="iostabios"></a>[iOS](#tab/ios)

[KeyChain](https://docs.microsoft.com/xamarin/ios/app-fundamentals/user-defaults) 用於在 iOS 裝置上儲存值。 如果未指定 `sharedName`，則會使用 `StandardUserDefaults`；否則該名稱會用於建立具有用於 `NSUserDefaultsType.SuiteName` 之指定名稱的新 `NSUserDefaults`。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

[ApplicationDataContainer](https://docs.microsoft.com/uwp/api/windows.storage.applicationdatacontainer) 用於將值儲存在裝置上。 如果未指定 `sharedName`，則會使用 `LocalSettings`否則該名稱會用於建立 `LocalSettings` 內的容器。

--------------

## <a name="persistence"></a>持續性

解除安裝應用程式會移除所有「喜好設定」。 但針對使用 [「自動備份」](https://developer.android.com/guide/topics/data/autobackup) 的 Android 6.0 (API 層級 23) 或更新版，並在其上執行的應用程式除外。 根據預設，此功能為啟用並保留應用程式資料，包括「共用喜好設定」，這是**喜好設定** API 使用的內容。 您可以透過下列 Google [文件](https://developer.android.com/guide/topics/data/autobackup) 來停用此項目。

## <a name="limitations"></a>限制

儲存字串時，此 API 旨在存放少量文字。  若嘗試將其用來儲存大量文字，效能會欠佳。

## <a name="api"></a>API

- [喜好設定原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Preferences)
- [喜好設定 API 文件](xref:Xamarin.Essentials.Preferences)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Preferences-Essential-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
