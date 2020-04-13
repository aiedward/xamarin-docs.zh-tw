---
title: 開始使用指紋身份驗證
ms.prod: xamarin
ms.assetid: 7BACCB36-8E3E-4E5D-B8EF-56A639839FD2
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/17/2018
ms.openlocfilehash: 746a096f93036e63b29bc917826259f88426cead
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020286"
---
# <a name="getting-started-with-fingerprint-authentication"></a>開始使用指紋身份驗證

要開始使用,我們首先介紹如何配置 Xamarin.Android 專案,以便應用程式能夠使用指紋身份驗證:

1. 更新**AndroidManifest.xml**以聲明指紋 API 所需的許可權。
2. 獲取對的`FingerprintManager`引用。
3. 檢查設備是否能夠進行指紋掃描。

## <a name="requesting-permissions-in-the-application-manifest"></a>在應用程式清單中請求權限

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Android 應用程式必須請求`USE_FINGERPRINT`清單 中的許可權。 以下螢幕擷取如何在 Visual Studio 中向應用程式新增此許可權:

[![在\_Android 清單螢幕中啟用使用指紋](get-started-images/fingerprint-01-vs.png)](get-started-images/fingerprint-01-vs.png#lightbox) 

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

Android 應用程式必須請求`USE_FINGERPRINT`清單 中的許可權。 以下螢幕截圖示範如何向 Mac 視覺化工作室中的應用程式新增此許可權:

[![在 Android 應用程式螢幕中啟用使用指紋](get-started-images/fingerprint-01-xs.png)](get-started-images/fingerprint-01-xs.png#lightbox) 

-----

## <a name="getting-an-instance-of-the-fingerprintmanager"></a>取得指紋管理員的實體

接下來,應用程式必須獲取`FingerprintManager`或類`FingerprintManagerCompat`的實例。 要與舊版本的 Android 相容,Android 應用程式應使用 Android 支援 v4 NuGet 包中的相容性 API。 以下代碼片段如何從作業系統取得適當的物件: 

```csharp
// Using the Android Support Library v4
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);

// Using API level 23:
FingerprintManager fingerprintManager = context.GetSystemService(Context.FingerprintService) as FingerprintManager;
```  

在前面的代碼段中,`context`是任何`Android.Content.Context`Android 。 通常,這是執行身份驗證的活動。

## <a name="checking-for-eligibility"></a>檢查資格

應用程式必須執行多個檢查,以確保可以使用指紋身份驗證。 應用程式總共使用五個條件來檢查資格:  

**API 級別 23**&ndash;指紋 API 需要 API 級別 23 或更高。 該`FingerprintManagerCompat`類將為您包裝 API 級別檢查。 因此,建議使用**Android 支援庫 v4**和`FingerprintManagerCompat`;這將解釋這些檢查之一。

**硬體**&ndash;當應用程式首次啟動時,應檢查是否存在指紋掃描器:

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.IsHardwareDetected)
{
    // Code omitted
}
```

**設備是安全的**&ndash;用戶必須用螢幕鎖保護設備。 如果用戶沒有使用螢幕鎖保護設備,並且安全性對應用程式很重要,則應通知用戶必須配置螢幕鎖。 以下代碼段演示如何檢查此預查詢:

```csharp
KeyguardManager keyguardManager = (KeyguardManager) GetSystemService(KeyguardService);
if (!keyguardManager.IsKeyguardSecure)
{
}
```

**已註冊的指紋**&ndash;用戶必須至少有一個指紋在操作系統中註冊。 每次身份驗證嘗試之前應執行此權限檢查:

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.HasEnrolledFingerprints)
{
    // Can't use fingerprint authentication - notify the user that they need to
    // enroll at least one fingerprint with the device.
}
```

**許可權**&ndash;應用程式在使用應用程式之前必須請求使用者的許可權。 對於 Android 5.0 和更低,使用者授予許可權作為安裝應用的條件。 Android 6.0 引入了一種新的許可權模型,該模型在運行時檢查許可權。 此代碼段是如何檢查 Android 6.0 上的權限的範例:

```csharp
// The context is typically a reference to the current activity.
Android.Content.PM.Permission permissionResult = ContextCompat.CheckSelfPermission(context, Manifest.Permission.UseFingerprint);
if (permissionResult == Android.Content.PM.Permission.Granted)
{
    // Permission granted - go ahead and start the fingerprint scanner.
}
else
{
    // No permission. Go and ask for permissions and don't start the scanner. See
    // https://developer.android.com/training/permissions/requesting.html
}
```

每次應用程式提供身份驗證選項時檢查所有這些條件將確保用戶獲得最佳的用戶體驗。 對其設備或作業系統的更改或升級可能會影響指紋身份驗證的可用性。 如果選擇緩存任何這些檢查的結果,請確保滿足升級方案。

有關如何在 Android 6.0 中請求權限的詳細資訊,請參閱 Android 指南[,在執行時請求許可權](https://developer.android.com/training/permissions/requesting.html)。

## <a name="related-links"></a>相關連結

- [Context](xref:Android.Content.Context)
- [鑰匙保護管理員](xref:Android.App.KeyguardManager)
- [內容壓縮](https://developer.android.com/reference/android/support/v4/content/ContextCompat)
- [指紋管理員](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [指紋管理員康柏](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
- [在執行時要求權限](https://developer.android.com/training/permissions/requesting.html)
