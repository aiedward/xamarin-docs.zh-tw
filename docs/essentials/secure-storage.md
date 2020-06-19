---
title: Xamarin.Essentials：安全存放裝置
description: 本檔描述中的 SecureStorage 類別 Xamarin.Essentials ，這有助於安全地儲存簡單的索引鍵/值組。 此文件討論如何使用該類別、平台實作特性與限制。
ms.assetid: 78856C0D-76BB-406E-A880-D5A3987B7D64
author: jamesmontemagno
ms.author: jamont
ms.date: 04/02/2019
ms.custom: video
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 061bc1cfe785ad080092ba21340f7d38bc499ed9
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84801942"
---
# <a name="xamarinessentials-secure-storage"></a>Xamarin.Essentials：安全存放裝置

**SecureStorage** 類別有助於安全地存放簡單的機碼/值組。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

若要存取 **SecureStorage** 功能，需要下列平台特定設定：

# <a name="android"></a>[Android](#tab/android)

> [!TIP]
> [應用程式自動備份 (Auto Backup for Apps)](https://developer.android.com/guide/topics/data/autobackup) 是 Android 6.0 (API 層級 23) 與更新版本中的功能，可備份使用者的應用程式資料 (共用喜好設定、應用程式內部存放區中的檔案，以及其他特定檔案)。 當重新安裝應用程式或在新裝置上安裝應用程式時，會還原資料。 這會對 `SecureStorage` 造成影響，它利用備份的共用喜好設定，而且在進行還原時無法解密。 Xamarin.Essentials會藉由移除金鑰來自動處理這種情況，使其可以重設，但是您可以藉由停用自動備份來採取額外的步驟。

### <a name="enable-or-disable-backup"></a>啟用或停用備份
您可以透過將 `AndroidManifest.xml` 檔案中的 `android:allowBackup` 設定設定為 false，以選擇停用整個應用程式的自動備份。 只有當您計畫以其他方式還原資料時，才建議使用此方式。

```xml
<manifest ... >
    ...
    <application android:allowBackup="false" ... >
        ...
    </application>
</manifest>
```

### <a name="selective-backup"></a>選擇性備份
您可以將自動備份設定為停用特定內容而不予備份。 您可以建立自訂規則集以排除 `SecureStore` 項目而不予備份。

1. 在 **AndroidManifest.xml** 中設定 `android:fullBackupContent` 屬性：

    ```xml
    <application ...
        android:fullBackupContent="@xml/auto_backup_rules">
    </application>
    ```

2. 在 **Resources/xml** 目錄中搭配 **AndroidResource** 的建置動作，建立名為 **auto_backup_rules.xml** 的新 XML 檔案。 接著設定下列內容以包括所有共用喜好設定，但 `SecureStorage` 除外：

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <full-backup-content>
        <include domain="sharedpref" path="."/>
        <exclude domain="sharedpref" path="${applicationId}.xamarinessentials.xml"/>
    </full-backup-content>
    ```

# <a name="ios"></a>[iOS](#tab/ios)

當您在 **iOS 模擬器** 上開發時，請啟用 **Keychain** 權利並為應用程式的套件組合識別碼新增金鑰鏈存取群組。

開啟 iOS 專案中的 **Entitlements.plist**，並尋找 **Keychain** 權利並予以啟用。 這會自動將應用程式的識別碼新增為群組。

在專案屬性中的 [iOS 套件組合簽署]**** 下，將 [自訂權利]**** 設定為 **Entitlements.plist**。

> [!TIP]
> 當部署到 iOS 裝置時，不需要此權利且應該移除。

# <a name="uwp"></a>[UWP](#tab/uwp)

不需要進行額外設定。

-----

## <a name="using-secure-storage"></a>使用 Secure Storage

Xamarin.Essentials在您的類別中新增的參考：

```csharp
using Xamarin.Essentials;
```

若要將給定「機碼」__ 的值儲存在安全的存放區中：

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

從安全的存放區擷取值：

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
> 若沒有值與該要求機碼相關聯，`GetAsync` 將會傳回 `null`。

若要移除指定的機碼，請呼叫：

```csharp
SecureStorage.Remove("oauth_token");
```

若要移除所有機碼，請呼叫：

```csharp
SecureStorage.RemoveAll();
```

## <a name="platform-implementation-specifics"></a>平台實作特性

# <a name="android"></a>[Android](#tab/android)

[Android KeyStore](https://developer.android.com/training/articles/keystore.html) 是用來存放在將值以檔案名稱 **[YOUR-APP-PACKAGE-ID].xamarinessentials** 儲存到[共用喜好設定](https://developer.android.com/training/data-storage/shared-preferences.html)之前用於加密值的密碼編譯金鑰。  在共用喜好設定檔案中使用的金鑰 (不是密碼編譯金鑰，適用於「值」__ 的「金鑰」__) 是傳遞到 `SecureStorage` API 之金鑰的「MD5 雜湊」__。

**API 層級 23 與更高版本**

在較新的 API 層級上，會從 Android KeyStore 取得 **AES** 金鑰並搭配 **AES/GCM/NoPadding** 密碼編譯使用，以在將值存放在共用喜好設定檔案中之前加密值。

**API 層級22 與更低版本**

在較舊的 API 層級上，Android KeyStore 只支援存放 **RSA** 金鑰，此金鑰會搭配 **RSA/ECB/PKCS1Padding** 密碼編譯使用以加密 **AES** 金鑰 (在執行階段隨機產生) 並存放在機碼 _SecureStorageKey_ 下的共用喜好設定檔案中 (若尚未產生)。

**SecureStorage** 使用 [Preferences](preferences.md) API 並遵循 [Preferences](preferences.md#persistence) 文件中概述的相同資料持續性原則。 若裝置從 API 層級 22 或較低的層級升級到 API 層級 23 與更高版本，將會繼續使用此類型的加密，除非將應用程式解除安裝或呼叫 **RemoveAll**。

# <a name="ios"></a>[iOS](#tab/ios)

[KeyChain](xref:Security.SecKeyChain) 是用來安全地在 iOS 裝置上存放值。  `SecRecord` 是用來存放 `Service` 值設定為 **[YOUR-APP-BUNDLE-ID].xamarinessentials** 的值。

在某些案例中，KeyChain 資料會與 iCloud 同步，而且解除安裝應用程式可能不會將安全值從 iCloud 與使用者的其他裝置移除。

# <a name="uwp"></a>[UWP](#tab/uwp)

[DataProtectionProvider](https://docs.microsoft.com/uwp/api/windows.security.cryptography.dataprotection.dataprotectionprovider) 可用來安全地在 UWP 裝置上為值加密。

加密值會存放在 `ApplicationData.Current.LocalSettings` 中名為 **[YOUR-APP-ID].xamarinessentials** 的容器內。

**SecureStorage** 使用 [Preferences](preferences.md) API 並遵循 [Preferences](preferences.md#persistence) 文件中概述的相同資料持續性原則。 它也會使用， `LocalSettings` 其限制是每個設定的名稱最多可以是255個字元的長度。 每個設定的大小最多可達8K 位元組，而且每個複合設定的大小最多可達64K 位元組。

-----

## <a name="limitations"></a>限制

此 API 旨在存放少量文字。  若嘗試使用它來存放大量文字，效能會變差。

## <a name="api"></a>API

- [SecureStorage 原始程式碼](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/SecureStorage)
- [SecureStorage API 文件](xref:Xamarin.Essentials.SecureStorage)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Secure-Storage-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
