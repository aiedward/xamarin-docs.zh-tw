---
title: Xamarin.Essentials： 喜好設定
description: 本文件說明在 Xamarin.Essentials，這會儲存在索引鍵/值存放區中的應用程式喜好設定的喜好設定類別。 它討論如何使用類別和可以儲存的資料類型。
ms.assetid: AA81BCBD-79BA-448F-942B-BA4415CA50FF
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: ca6d4f1ec60a80b483c79dd75267144e67d80c0b
ms.sourcegitcommit: 081a2d094774c6f75437d28b71d22607e33aae71
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2018
ms.locfileid: "37403486"
---
# <a name="xamarinessentials-preferences"></a>Xamarin.Essentials： 喜好設定

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**喜好設定**類別可協助將應用程式喜好設定索引鍵/值存放區中。

## <a name="using-preferences"></a>使用喜好設定

在您的類別加入 Xamarin.Essentials 的參考：

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

若要移除_金鑰_喜好設定中：

```csharp
Preferences.Remove("my_key");
```

若要移除所有的喜好設定：

```csharp
Preferences.Clear();
```

除了這些方法各自採用在選擇性`sharedName`，可用來建立額外的喜好設定的容器。 請閱讀以下的平台實作細節。

## <a name="supported-data-types"></a>支援的資料類型

中支援下列資料類型**喜好設定**:

- **bool**
- **double**
- **int**
- **float**
- **long**
- **string**
- **DateTime**

## <a name="implementation-details"></a>實作詳細資料

值`DateTime`會以格式儲存在 64 位元二進位檔 （長整數） 所定義的方法有兩種`DateTime`類別： [ `ToBinary` ](xref:System.DateTime.ToBinary)方法來編碼`DateTime`值，而[ `FromBinary` ](xref:System.DateTime.FromBinary(System.Int64))方法解碼值。 這可能會為已解碼的調整方法的文件值時，請參閱`DateTime`是也就是儲存非 Coordinated Universal Time (UTC) 值。

## <a name="platform-implementation-specifics"></a>平台實作的特性

# <a name="androidtabandroid"></a>[Android](#tab/android)

所有資料都會都儲存成[共用的喜好設定](https://developer.android.com/training/data-storage/shared-preferences.html)。 如果沒有`sharedName`共用的預設喜好設定，否則用來取得的名稱指定**私人**共用喜好設定與指定的名稱。

# <a name="iostabios"></a>[iOS](#tab/ios)

[使用 NSUserDefaults](https://docs.microsoft.com/en-us/xamarin/ios/app-fundamentals/user-defaults)用來在 iOS 裝置上儲存的值。 如果沒有`sharedName`指定`StandardUserDefaults`會使用，否則名稱用來建立新`NSUserDefaults`具有指定名稱用於`NSUserDefaultsType.SuiteName`。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

[ApplicationDataContainer](https://docs.microsoft.com/en-us/uwp/api/windows.storage.applicationdatacontainer)用來將值儲存在裝置上。 如果沒有`sharedName`指定`LocalSettings`會使用，否則名稱用來建立新的容器內的`LocalSettings`。

--------------

## <a name="limitations"></a>限制

當儲存的字串，此 API 會用來儲存少量文字。  效能可能會 subpar，如果您嘗試使用它來儲存大量的文字。

## <a name="api"></a>API

- [喜好設定原始程式碼。](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Preferences)
- [喜好設定 API 文件](xref:Xamarin.Essentials.Preferences)
