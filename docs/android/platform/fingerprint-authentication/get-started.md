---
title: 具有指紋驗證的消費者入門
ms.prod: xamarin
ms.assetid: 7BACCB36-8E3E-4E5D-B8EF-56A639839FD2
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/17/2018
ms.openlocfilehash: 746a096f93036e63b29bc917826259f88426cead
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "73020286"
---
# <a name="getting-started-with-fingerprint-authentication"></a>具有指紋驗證的消費者入門

首先，讓我們先討論如何設定 Xamarin Android 專案，讓應用程式能夠使用指紋驗證：

1. 更新**androidmanifest.xml**以宣告指紋 api 所需的許可權。
2. 取得 `FingerprintManager`的參考。
3. 檢查裝置是否能夠進行指紋掃描。

## <a name="requesting-permissions-in-the-application-manifest"></a>在應用程式資訊清單中要求許可權

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Android 應用程式必須要求資訊清單中的 `USE_FINGERPRINT` 許可權。 下列螢幕擷取畫面顯示如何在 Visual Studio 中將此許可權新增至應用程式：

[![啟用 [在 Android 資訊清單中使用\_指紋] 畫面](get-started-images/fingerprint-01-vs.png)](get-started-images/fingerprint-01-vs.png#lightbox) 

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

Android 應用程式必須要求資訊清單中的 `USE_FINGERPRINT` 許可權。 下列螢幕擷取畫面顯示如何在 Visual Studio for Mac 中將此許可權新增至應用程式：

[![在 Android 應用程式畫面中啟用 UseFingerprint](get-started-images/fingerprint-01-xs.png)](get-started-images/fingerprint-01-xs.png#lightbox) 

-----

## <a name="getting-an-instance-of-the-fingerprintmanager"></a>取得 FingerprintManager 的實例

接下來，應用程式必須取得 `FingerprintManager` 或 `FingerprintManagerCompat` 類別的實例。 若要與舊版的 Android 相容，Android 應用程式應該使用在 Android 支援 v4 NuGet 套件中找到的相容性 API。 下列程式碼片段示範如何從作業系統取得適當的物件： 

```csharp
// Using the Android Support Library v4
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);

// Using API level 23:
FingerprintManager fingerprintManager = context.GetSystemService(Context.FingerprintService) as FingerprintManager;
```  

在上一個程式碼片段中，`context` 是任何 Android `Android.Content.Context`。 這通常是執行驗證的活動。

## <a name="checking-for-eligibility"></a>檢查是否符合資格

應用程式必須執行數項檢查，以確保可以使用指紋驗證。 其中，應用程式會使用五個條件來檢查是否符合資格：  

**Api 層級 23** &ndash; 指紋 API 需要 API 層級23或更高版本。 `FingerprintManagerCompat` 類別會為您包裝 API 層級的檢查。 基於這個理由，建議使用**Android 支援程式庫 v4**和 `FingerprintManagerCompat`。這會考慮其中一項檢查。

**硬體**&ndash; 當應用程式第一次啟動時，它應該檢查指紋掃描器是否存在：

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.IsHardwareDetected)
{
    // Code omitted
}
```

**裝置受到保護**，&ndash; 使用者必須使用螢幕鎖定來保護裝置。 如果使用者未使用螢幕鎖定來保護裝置，而且安全性對應用程式而言很重要，則應通知使用者必須設定螢幕鎖定。 下列程式碼片段顯示如何檢查此預先 requiste：

```csharp
KeyguardManager keyguardManager = (KeyguardManager) GetSystemService(KeyguardService);
if (!keyguardManager.IsKeyguardSecure)
{
}
```

已**註冊的指紋**&ndash; 使用者必須至少有一個指紋向作業系統註冊。 這項許可權檢查應該在每次驗證嘗試之前進行：

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.HasEnrolledFingerprints)
{
    // Can't use fingerprint authentication - notify the user that they need to
    // enroll at least one fingerprint with the device.
}
```

應用程式 &ndash; 的**許可權**必須先向使用者要求許可權，才能使用應用程式。 針對 Android 5.0 和更低版本，使用者會授與許可權作為安裝應用程式的條件。 Android 6.0 引進了新的許可權模型，可在執行時間檢查許可權。 此程式碼片段是如何檢查 Android 6.0 上的許可權的範例：

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

每次應用程式提供驗證選項時，檢查所有這些條件，將可確保使用者取得最佳的使用者體驗。 變更或升級至其裝置或作業系統可能會影響指紋驗證的可用性。 如果您選擇快取上述任何一項檢查的結果，請務必滿足升級案例。

如需如何在 Android 6.0 中要求許可權的詳細資訊，請參閱 Android 指南[在執行時間要求許可權](https://developer.android.com/training/permissions/requesting.html)。

## <a name="related-links"></a>相關連結

- [內容](xref:Android.Content.Context)
- [KeyguardManager](xref:Android.App.KeyguardManager)
- [CoNtextCompat](https://developer.android.com/reference/android/support/v4/content/ContextCompat)
- [FingerprintManager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
- [在執行時間要求許可權](https://developer.android.com/training/permissions/requesting.html)
