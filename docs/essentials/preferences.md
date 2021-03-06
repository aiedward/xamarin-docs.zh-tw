---
title: Xamarin.Essentials：喜好設定
description: 本檔說明中的喜好設定類別 Xamarin.Essentials ，可將應用程式喜好設定儲存在索引鍵/值存放區中。 會討論如何使用類別和可以儲存的資料類型。
ms.assetid: AA81BCBD-79BA-448F-942B-BA4415CA50FF
author: jamesmontemagno
ms.author: jamont
ms.date: 01/15/2019
ms.custom: video
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 07bfcabc7ffef20bee43531bfab3e78155beb9a9
ms.sourcegitcommit: 58247fe066ad271ee43c8967ac3301fdab6ca2d1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92629570"
---
# <a name="no-locxamarinessentials-preferences"></a>Xamarin.Essentials：喜好設定

**喜好設定** 類別可協助將應用程式喜好設定儲存於鍵/值存放區中。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-preferences"></a>使用喜好設定

Xamarin.Essentials在您的類別中新增的參考：

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

若要檢查指定的索引 _鍵_ 是否存在於喜好設定中：

```csharp
bool hasKey = Preferences.ContainsKey("my_key");
```

從喜好設定中移除「鍵」：

```csharp
Preferences.Remove("my_key");
```

移除所有喜好設定：

```csharp
Preferences.Clear();
```

> [!TIP]
> 上述方法會採用稱為的選擇性 `string` 參數 `sharedName` 。 這個參數是用來為喜好設定建立額外的容器，在某些使用案例中很有用。 一個使用案例是當您的應用程式需要在延伸模組或監看式應用程式之間共用喜好設定時。 請參閱下方的平臺執行細節。

## <a name="supported-data-types"></a>支援的資料類型

**喜好設定** 支援下列資料類型：

- **bool**
- **double**
- **int**
- **float**
- **long**
- **string**
- **DateTime**

## <a name="integrate-with-system-settings"></a>與系統設定整合

喜好設定會以原生方式儲存，可讓您將設定整合至原生系統設定。 遵循平臺檔和範例以與平臺整合：

* Apple：[執行 IOS 設定](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/UserDefaults/Preferences/Preferences.html)套件組合
* [iOS 應用程式喜好設定範例](/samples/xamarin/ios-samples/appprefs/)
* [watchOS 設定](https://developer.xamarin.com/guides/ios/watch/working-with/settings/)
* Android： [具有設定畫面的消費者入門](https://developer.android.com/guide/topics/ui/settings.html)

## <a name="implementation-details"></a>實作詳細資料

的值 `DateTime` 會使用類別所定義的兩種方法，儲存在64位二進位 (長整數) 格式 `DateTime` ： [`ToBinary`](xref:System.DateTime.ToBinary) 方法是用來對值進行編碼 `DateTime` ，而方法會將值解碼 [`FromBinary`](xref:System.DateTime.FromBinary(System.Int64)) 。 請參閱這些方法的文件，以便在已儲存的 `DateTime` 並非國際標準時間 (UTC) 值時，對解碼值進行調整。

## <a name="platform-implementation-specifics"></a>平台實作特性

# <a name="android"></a>[Android](#tab/android)

所有資料都會儲存於[共用的喜好設定](https://developer.android.com/training/data-storage/shared-preferences.html)中。 如果未 `sharedName` 指定，則會使用預設的共用喜好設定，否則會使用名稱來取得具有指定名稱的 **私** 用共用喜好設定。

# <a name="ios"></a>[iOS](#tab/ios)

[KeyChain](../ios/app-fundamentals/user-defaults.md) 用於在 iOS 裝置上儲存值。 如果未指定 `sharedName`，則會使用 `StandardUserDefaults`；否則該名稱會用於建立具有用於 `NSUserDefaultsType.SuiteName` 之指定名稱的新 `NSUserDefaults`。

# <a name="uwp"></a>[UWP](#tab/uwp)

[ApplicationDataContainer](/uwp/api/windows.storage.applicationdatacontainer) 用於將值儲存在裝置上。 如果未 `sharedName` 指定，則 `LocalSettings` 會使用，否則會使用名稱在中建立新的容器 `LocalSettings` 。

`LocalSettings` 也有下列限制，每個設定的名稱最多可以是255個字元。 每個設定的大小最多可有8K 個位元組，而每個複合設定的大小最多可達64K 個位元組。

--------------

## <a name="persistence"></a>持續性

卸載應用程式將會移除所有喜好設定，但在 Android 6.0 (API 層級 23) 或更新版本的應用程式會使用 [__自動備份__](https://developer.android.com/guide/topics/data/autobackup)來移除所有的 _喜好_ 設定。 根據預設，此功能為啟用並保留應用程式資料，包括「共用喜好設定」，這是 **喜好設定** API 使用的內容。 您可以透過下列 Google [文件](https://developer.android.com/guide/topics/data/autobackup) 來停用此項目。

## <a name="limitations"></a>限制

儲存字串時，此 API 旨在存放少量文字。 若嘗試將其用來儲存大量文字，效能會欠佳。

## <a name="api"></a>API

- [喜好設定原始程式碼](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Preferences)
- [喜好設定 API 文件](xref:Xamarin.Essentials.Preferences)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Preferences-Essential-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
