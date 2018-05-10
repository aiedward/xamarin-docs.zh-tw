---
title: Xamarin.Essentials 安全儲存體
description: SecureStorage 類別可協助安全地儲存簡單的索引鍵/值組。
ms.assetid: 78856C0D-76BB-406E-A880-D5A3987B7D64
author: redth
ms.author: jodick
ms.date: 05/04/2018
ms.openlocfilehash: 24d1e29ba0203aaafc3e21533478f6c505cc09b3
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
---
# <a name="xamarinessentials-secure-storage"></a>Xamarin.Essentials 安全儲存體

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**SecureStorage**類別可協助安全地儲存簡單的索引鍵/值組。

## <a name="using-secure-storage"></a>使用安全的儲存體

在您類別中加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

若要儲存的值指定_金鑰_安全存放裝置中：

```csharp
await SecureStorage.SetAsync("oauth_token", "secret-oauth-token-value");
```

若要擷取的值從安全的儲存體：

```csharp
var oauthToken = await SecureStorage.GetAsync("oauth_token");
```

## <a name="platform-implementation-specifics"></a>平台實作的特性

# <a name="androidtabandroid"></a>[Android](#tab/android)

[Android KeyStore](https://developer.android.com/training/articles/keystore.html)用來儲存用來加密值儲存到之前的加密金鑰[共用的喜好設定](https://developer.android.com/training/data-storage/shared-preferences.html)的檔案名稱 **.xamarinessentials [您的應用程式-封裝-ID]**.  共用的喜好設定檔中使用的索引鍵是_MD5 雜湊_傳入的索引鍵的`SecureStorage`API。

## <a name="api-level-23-and-higher"></a>應用程式開發介面層級 23 及更高版本

在較新的應用程式開發介面層級**AES**機碼是取自 Android 的金鑰存放區，使用**AES/GCM/NoPadding**加密值，才能儲存在共用的喜好設定檔中加密。

## <a name="api-level-22-and-lower"></a>應用程式開發介面層級 22 和較低

在舊版的應用程式開發介面層級上 Android 的金鑰存放區僅支援儲存**RSA**索引鍵，可搭配**RSA/ECB/PKCS1Padding**加密來加密**AES** （隨機金鑰產生在執行階段） 並儲存在共用的喜好設定下的檔案索引鍵_SecureStorageKey_，如果其中一個已不產生。

從裝置解除安裝應用程式時，將會移除所有的加密的值。

# <a name="iostabios"></a>[iOS](#tab/ios)

[KeyChain](https://developer.xamarin.com/api/type/Android.Security.KeyChain/)用來安全地儲存在 iOS 裝置上的值。  `SecRecord`用來儲存值具有`Service`值設定為 **[您的應用程式-配套-ID].xamarinessentials**。

KeyChain 資料同步使用 icloud 的功能，在某些情況下，解除安裝應用程式可能不會移除從 icloud 的功能和其他裝置之使用者的安全的值。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

[DataProtectionProvider](https://docs.microsoft.com/en-us/uwp/api/windows.security.cryptography.dataprotection.dataprotectionprovider)用來安全地在 UWP 裝置上的 encryped 值。

Encryped 值會儲存在`ApplicationData.Current.LocalSettings`，名稱為容器內 **[您的應用程式識別碼的].xamarinessentials**。

解除安裝應用程式會使_LocalSettings_，以及要一併移除所有加密的值。

-----

## <a name="limitations"></a>限制

這個 API 被為了儲存小量的文字。  效能可能會變慢，如果您嘗試使用它來儲存大量的文字。

## <a name="api"></a>API

- [SecureStorage 原始程式碼](https://github.com/xamarin/Essentials/tree/master/Essentials/SecureStorage)
- [SecureStorage API 文件](xref:Xamarin.Essentials.SecureStorage)
