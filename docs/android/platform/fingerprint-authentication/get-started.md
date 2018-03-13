---
title: "開始使用指紋驗證"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7BACCB36-8E3E-4E5D-B8EF-56A639839FD2
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/08/2018
ms.openlocfilehash: e3c986b03408dae98a5a79f257029c10909aeabd
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="getting-started-with-fingerprint-authentication"></a>開始使用指紋驗證

若要開始，讓我們先說明如何設定 Xamarin.Android 專案，以便將應用程式能夠使用指紋驗證：

1. 更新**AndroidManifest.xml**宣告指紋 Api 需要的權限。
2. 取得參考`FingerprintManager`。
3. 請檢查裝置能夠指紋掃描。

## <a name="requesting-permissions-in-the-application-manifest"></a>要求的權限，在應用程式資訊清單

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Android 應用程式必須要求`USE_FINGERPRINT`資訊清單中的權限。 下列螢幕擷取畫面顯示如何將此權限加入至 Visual Studio 2015 中應用程式：

[![啟用使用\_Android 資訊清單的螢幕中的指紋](get-started-images/fingerprint-01-vs.png)](get-started-images/fingerprint-01-vs.png#lightbox) 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

Android 應用程式必須要求`USE_FINGERPRINT`資訊清單中的權限。 下列螢幕擷取畫面顯示如何在 Visual Studio for Mac 應用程式中加入此權限：

[![在 [Android 應用程式] 畫面中啟用 UseFingerprint](get-started-images/fingerprint-01-xs.png)](get-started-images/fingerprint-01-xs.png#lightbox) 

-----

## <a name="getting-an-instance-of-the-fingerprintmanager"></a>取得 FingerprintManager 的執行個體

接下來，應用程式必須取得的執行個體`FingerprintManager`或`FingerprintManagerCompat`類別。 若要與 Android 的舊版本相容，Android 應用程式應該使用的相容性應用程式開發介面找不到的 Android 支援 v4 NuGet 封裝中。 下列程式碼片段示範如何從作業系統取得適當的物件： 

```csharp
// Using the Android Support Library v4
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);

// Using API level 23:
FingerprintManager fingerprintManager = context.GetSystemService(Context.FingerprintService) as FingerprintManager;
```  

在上一個片段中，`context`是任何 Android `Android.Content.Context`。 一般而言，這是正在執行驗證的活動。

## <a name="checking-for-eligibility"></a>檢查資格

應用程式必須執行幾項檢查，以確保能夠使用指紋驗證。 總共有五個應用程式用來檢查資格的條件：  
 

**應用程式開發介面層級 23** &ndash;指紋 Api 需要 API 層級 23 或更高版本。 `FingerprintManagerCompat`類別會自動為您的應用程式開發介面層級檢查換行。 基於這個原因建議使用**Android 支援程式庫 v4**和`FingerprintManagerCompat`; 這會針對這些檢查一個帳戶。

**硬體**&ndash;應用程式啟動時第一次，它應該檢查指紋掃描器的目前狀態：

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.IsHardwareDetected)
{
    // Code omitted
}
```
    
**裝置受到**&ndash;使用者必須使用 螢幕鎖定保護裝置。 如果使用者未受保護的裝置螢幕鎖定，而且安全性都很重要的應用程式，然後應通知使用者，必須設定螢幕鎖定。 下列程式碼片段示範如何檢查此預先 requiste:

```csharp
KeyguardManager keyguardManager = (KeyguardManager) GetSystemService(KeyguardService);
if (!keyguardManager.IsKeyguardSecure)
{
}
```

**註冊指紋**&ndash;使用者必須具有至少一個指紋向作業系統註冊。 在每個驗證嘗試之前，應該進行此權限檢查：

```csharp
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
if (!fingerprintManager.HasEnrolledFingerprints)
{
    // Can't use fingerprint authentication - notify the user that they need to
    // enroll at least one fingerprint with the device.
}
```

**權限**&ndash;應用程式必須要求使用者權限才能使用應用程式。 如需 Android 5.0 和較低，使用者授與的權限做為安裝應用程式的條件。 Android 6.0 導入了新的權限模型，在執行階段檢查權限。 此程式碼片段是如何檢查權限 Android 6.0 的範例：

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

每次它想要使用指紋驗證時，應用程式應該檢查條件 3、 4 和 5。 前兩個條件可以檢查裝置並將結果儲存在執行應用程式第一次 （在共用的喜好設定，例如）。

如需有關如何在 Android 6.0 要求權限的詳細資訊，請參閱 Android 指南[要求權限，在執行階段](http://developer.android.com/training/permissions/requesting.html)。



## <a name="related-links"></a>相關連結

- [Context](https://developer.xamarin.com/api/type/Android.Content.Context/)
- [ContextCompat](https://developer.xamarin.com/api/type/Android.Support.V4.Content.ContextCompat/)
- [KeyguardManager](https://developer.xamarin.com/api/type/Android.App.KeyguardManager/)
- [FingerprintManager](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
- [在執行階段要求的權限](http://developer.android.com/training/permissions/requesting.html)
