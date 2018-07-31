---
title: Xamarin.Essentials： 安全的儲存體
description: 本文件說明在 Xamarin.Essentials，可協助安全地儲存簡單的索引鍵/值組的 SecureStorage 類別。 它討論如何使用類別、 平台實作的特性和限制。
ms.assetid: 78856C0D-76BB-406E-A880-D5A3987B7D64
author: redth
ms.author: jodick
ms.date: 05/04/2018
ms.openlocfilehash: 2dfdb7051b269e73c68290a557849b9ae606c165
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353291"
---
# <a name="xamarinessentials-secure-storage"></a>Xamarin.Essentials： 安全的儲存體

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**SecureStorage**類別可協助安全地儲存簡單的索引鍵/值組。

## <a name="getting-started"></a>快速入門

若要存取**SecureStorage**功能，下列平台特定安裝程式會需要：

# <a name="androidtabandroid"></a>[Android](#tab/android)

不需要其他設定。

# <a name="iostabios"></a>[iOS](#tab/ios)

在開發 iOS 模擬器上時，啟用**鑰匙圈**權利，並新增 keychain 存取群組的應用程式的套件組合識別碼。

開啟**Entitlements.plist** iOS 專案時，發現**Keychain**權利，將它啟用。 這會自動加入做為群組的應用程式的識別碼。

在專案屬性中，在**iOS 套件組合簽署**設定**自訂權利**來**Entitlements.plist**。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

不需要其他設定。

-----

## <a name="using-secure-storage"></a>使用安全的儲存體

在您的類別加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

若要儲存的值指定_金鑰_安全的儲存體中：

```csharp
try
{
  await SecureStorage.SetAsync("oauth_token", "secret-oauth-token-value");
}
catch (Exception ex)
{
  // Possible that device doesn't support secure storage on device.
}
```

若要擷取安全的儲存體中的值：

```csharp
try
{
  var oauthToken = await SecureStorage.GetAsync("oauth_token");
}
catch (Exception ex)
{
  // Possible that device doesn't support secure storage on device.
}
```

> [!NOTE]
> 如果沒有要求的索引鍵相關聯的值`GetAsync`會傳回`null`。

若要移除特定索引鍵，請呼叫：

```csharp
SecureStorage.Remove("oauth_token");
```

若要移除所有機碼，請呼叫：

```csharp
SecureStorage.RemoveAll();
```


## <a name="platform-implementation-specifics"></a>平台實作的特性

# <a name="androidtabandroid"></a>[Android](#tab/android)

[Android KeyStore](https://developer.android.com/training/articles/keystore.html)用來儲存用來加密值儲存到之前的加密金鑰[共用的喜好設定](https://developer.android.com/training/data-storage/shared-preferences.html)檔案名稱的 **.xamarinessentials [您的應用程式-封裝-識別碼]**.  共用的喜好設定檔案中使用的索引鍵是_MD5 雜湊_傳入的索引鍵的`SecureStorage`Api。

## <a name="api-level-23-and-higher"></a>API 層級 23 和更新版本

在較新的 API 層級**AES**機碼是取自 Android 金鑰儲存區，搭配**AES/GCM/NoPadding**加密來加密值，然後再儲存在共用的喜好設定檔案中。

## <a name="api-level-22-and-lower"></a>API 層級 22 和較低

在舊版的 API 層級上 Android 金鑰儲存區僅支援儲存**RSA**搭配使用的金鑰**RSA/ECB/PKCS1Padding**加密來加密**AES** （隨機金鑰產生在執行階段） 並儲存在共用的喜好設定檔案，在機碼底下_SecureStorageKey_，如果其中一個不已經產生。

**SecureStorage**會使用[喜好設定](preferences.md)API，並如下所示相同的資料持續性中所述[喜好設定](preferences.md#persistence)文件。 如果裝置升級 API 層級 22 或更低的 API 層級 23 和更新版本時，這類加密會繼續使用，除非在解除安裝應用程式或**RemoveAll**呼叫。

# <a name="iostabios"></a>[iOS](#tab/ios)

[金鑰鏈](https://developer.xamarin.com/api/type/Security.SecKeyChain/)用來在 iOS 裝置上安全地儲存值。  `SecRecord`用來儲存值已經`Service`值設定為 **[您的應用程式-BUNDLE-ID].xamarinessentials**。

KeyChain 資料同步使用 iCloud，在某些情況下，解除安裝應用程式可能不會移除從 iCloud 與其他使用者的裝置的安全的值。

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

[DataProtectionProvider](https://docs.microsoft.com/uwp/api/windows.security.cryptography.dataprotection.dataprotectionprovider)是用來安全地儲存在 UWP 裝置的加密值。

加密的值會儲存在`ApplicationData.Current.LocalSettings`，在名稱為容器內 **[您的應用程式-識別碼].xamarinessentials**。

**SecureStorage**會使用[喜好設定](preferences.md)API，並如下所示相同的資料持續性中所述[喜好設定](preferences.md#persistence)文件。

-----

## <a name="limitations"></a>限制

此 API 會用來儲存少量文字。  效能可能會變慢，如果您嘗試使用它來儲存大量的文字。

## <a name="api"></a>API

- [SecureStorage 原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/SecureStorage)
- [SecureStorage API 文件](xref:Xamarin.Essentials.SecureStorage)
