---
title: 開始使用指紋驗證
ms.prod: xamarin
ms.assetid: 7BACCB36-8E3E-4E5D-B8EF-56A639839FD2
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/17/2018
ms.openlocfilehash: 05069272bfa25cc1f003d4aeb83e15bd223c2830
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118470"
---
# <a name="getting-started-with-fingerprint-authentication"></a>開始使用指紋驗證

若要開始，接著讓我們先討論如何設定 Xamarin.Android 專案，讓應用程式都可以使用指紋驗證：

1. 更新**AndroidManifest.xml**宣告指紋 Api 需要的權限。
2. 取得參考`FingerprintManager`。
3. 請檢查裝置能夠指紋掃描。

## <a name="requesting-permissions-in-the-application-manifest"></a>要求的權限，在應用程式資訊清單

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Android 應用程式必須要求`USE_FINGERPRINT`資訊清單中的權限。 下列螢幕擷取畫面顯示如何將此權限新增至 Visual Studio 2015 中的應用程式：

[![啟用使用\_Android 資訊清單 畫面中的指紋](get-started-images/fingerprint-01-vs.png)](get-started-images/fingerprint-01-vs.png#lightbox) 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

Android 應用程式必須要求`USE_FINGERPRINT`資訊清單中的權限。 下列螢幕擷取畫面示範如何在 Visual Studio for Mac 應用程式中加入此權限：

[![在 Android 應用程式畫面中啟用 UseFingerprint](get-started-images/fingerprint-01-xs.png)](get-started-images/fingerprint-01-xs.png#lightbox) 

-----

## <a name="getting-an-instance-of-the-fingerprintmanager"></a>取得 FingerprintManager 的執行個體

接下來，應用程式必須取得的執行個體`FingerprintManager`或`FingerprintManagerCompat`類別。 若要使用舊版 Android 相容，Android 應用程式應該使用的相容性 API 找不到的 Android 支援 v4 NuGet 套件中。 下列程式碼片段示範如何從作業系統取得適當的物件： 

```csharp
// Using the Android Support Library v4
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);

// Using API level 23:
FingerprintManager fingerprintManager = context.GetSystemService(Context.FingerprintService) as FingerprintManager;
```  

在上一個片段中，`context`是任何 Android `Android.Content.Context`。 這通常是正在執行驗證的活動。

## <a name="checking-for-eligibility"></a>正在檢查適用性

應用程式必須執行數個檢查，以確保您可使用指紋驗證。 總計有五個應用程式用來檢查資格的條件：  

**API 層級 23** &ndash;指紋 Api 需要 API 層級 23 或更高版本。 `FingerprintManagerCompat`類別會包裝您的 API 層級檢查。 基於這個理由，它是要使用的建議**Android 支援程式庫 v4**和`FingerprintManagerCompat`; 這會負責處理其中一個這些檢查。

**硬體**&ndash;當第一次啟動應用程式時，就應該檢查指紋掃描器是否存在：

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.IsHardwareDetected)
{
    // Code omitted
}
```

**裝置受到保護**&ndash;使用者必須具有與螢幕鎖定所保護的裝置。 如果使用者未受保護的螢幕鎖定的裝置和安全性都很重要的應用程式，然後應該通知使用者，必須設定螢幕鎖定。 下列程式碼片段示範如何檢查此之：

```csharp
KeyguardManager keyguardManager = (KeyguardManager) GetSystemService(KeyguardService);
if (!keyguardManager.IsKeyguardSecure)
{
}
```

**註冊指紋**&ndash;使用者必須具有至少一個向作業系統的指紋。 此權限檢查應該會發生在每個驗證嘗試之前：

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.HasEnrolledFingerprints)
{
    // Can't use fingerprint authentication - notify the user that they need to
    // enroll at least one fingerprint with the device.
}
```

**權限**&ndash;應用程式必須要求使用者的權限才能使用應用程式。 如需 Android 5.0 和較低，使用者授與的權限安裝的應用程式的條件。 Android 6.0 引進了新的權限模型，在執行階段檢查權限。 此程式碼片段是如何在 Android 6.0 上的權限檢查的範例：

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
    // http://developer.android.com/training/permissions/requesting.html
}
```

檢查所有這些條件，每次應用程式提供驗證選項可確保使用者可以獲得最佳使用者體驗。 變更或升級至他們的裝置或作業系統，可能會影響可用性的指紋驗證。 如果您選擇快取的任何這些檢查結果，請務必考量的升級案例。

如需有關如何在 Android 6.0 中的要求權限的詳細資訊，請參閱 Android 指南[在執行階段要求的權限](http://developer.android.com/training/permissions/requesting.html)。

## <a name="related-links"></a>相關連結

- [內容](https://developer.xamarin.com/api/type/Android.Content.Context/)
- [ContextCompat](https://developer.xamarin.com/api/type/Android.Support.V4.Content.ContextCompat/)
- [KeyguardManager](https://developer.xamarin.com/api/type/Android.App.KeyguardManager/)
- [FingerprintManager](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
- [在執行階段的要求權限](http://developer.android.com/training/permissions/requesting.html)
