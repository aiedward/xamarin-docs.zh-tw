---
title: Xamarin.Essentials 喜好設定
description: 喜好設定類別會儲存應用程式喜好設定索引鍵/值存放區中。
ms.assetid: AA81BCBD-79BA-448F-942B-BA4415CA50FF
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 6cca9413cee40fde5b8bb8967db52db7a3a3382f
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
---
# <a name="xamarinessentials-preferences"></a>Xamarin.Essentials 喜好設定

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**喜好設定**類別有助於將應用程式喜好設定索引鍵/值存放區中。

## <a name="using-secure-storage"></a>使用安全的儲存體

在您類別中加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

若要儲存的值指定_金鑰_喜好設定中：

```csharp
Preferences.Set("my_key", "my_value");
```

若要從喜好設定或預設值擷取值，如果未設定：

```csharp
var myValue = Preferences.Get("my_key", "default_value");
```

若要移除_金鑰_從喜好設定：

```csharp
Preferences.Remove("my_key");
```

若要移除所有喜好設定：

```csharp
Preferences.Clear();
```

除了這些方法以外，每個接受中選擇性`sharedName`，可以用來建立其他的喜好設定的容器。 閱讀下列平台實作細節。

## <a name="supported-data-types"></a>支援的資料類型

支援下列資料型別**喜好設定**:

- **bool**
- **double**
- **int**
- **float**
- **long**
- **string**

## <a name="platform-implementation-specifics"></a>平台實作的特性

# <a name="androidtabandroid"></a>[Android](#tab/android)

所有資料都會都儲存成[共用的喜好設定](https://developer.android.com/training/data-storage/shared-preferences.html)。 如果沒有`sharedName`使用共用的預設喜好設定，否則名稱用來取得指定**私人**共用具有指定名稱的喜好設定。

# <a name="iostabios"></a>[iOS](#tab/ios)

[NSUserDefaults](https://docs.microsoft.com/en-us/xamarin/ios/app-fundamentals/user-defaults)用來在 iOS 裝置上儲存的值。 如果沒有`sharedName`指定`StandardUserDefaults`會用於，否則名稱用來建立新的`NSUserDefaults`具有指定之名稱用於`NSUserDefaultsType.SuiteName`。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

[ApplicationDataContainer](https://docs.microsoft.com/en-us/uwp/api/windows.storage.applicationdatacontainer)用來將值儲存在裝置上。 如果沒有`sharedName`指定`LocalSettings`所使用，否則名稱用來建立新的容器內的`LocalSettings`。

--------------

## <a name="limitations"></a>限制

儲存時的字串，此 API 被為了儲存小量的文字。  效能可能會 subpar，如果您嘗試使用它來儲存大量的文字。

## <a name="api"></a>API

- [喜好設定的原始碼](https://github.com/xamarin/Essentials/tree/master/Essentials/Preferences)
- [喜好設定應用程式開發介面文件](xref:Xamarin.Essentials.Preferences)
