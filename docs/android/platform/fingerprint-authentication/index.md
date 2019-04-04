---
title: 指紋驗證
description: 本指南會討論如何將新增至 Xamarin.Android 應用程式在 Android 6.0 中，導入的指紋驗證。
ms.prod: xamarin
ms.assetid: 6742D874-4988-4516-A946-D5C714B20A10
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 70e12abdf61a6a0bfb36d281bcaa6214199e567d
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57667330"
---
# <a name="fingerprint-authentication"></a>指紋驗證

_本指南會討論如何將新增至 Xamarin.Android 應用程式在 Android 6.0 中，導入的指紋驗證。_


## <a name="fingerprint-authentication-overview"></a>指紋驗證概觀

在 Android 裝置上的指紋掃描器抵達提供傳統的使用者名稱/密碼方法的替代方案的使用者驗證的應用程式。 使用指紋來驗證使用者，讓應用程式納入較不干擾較使用者名稱和密碼的安全性。

FingerprintManager Api 與指紋掃描器以裝置為目標，而且正在執行的 API 層級 23 (Android 6.0) 或更高版本。 Api 位於`Android.Hardware.Fingerprints`命名空間。 Android 支援程式庫 v4 提供指紋適用於 Android 的較舊版本的 Api 版本。 相容性中找到 Api`Android.Support.v4.Hardware.Fingerprint`命名空間，透過散發[Xamarin.Android.Support.v4 NuGet 套件](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)。

[FingerprintManager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html) (和其支援程式庫的相對[FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)) 是使用指紋掃描硬體的主要類別。 這個類別是 Android SDK 包裝函式可管理本身的硬體互動的系統層級服務。 它會負責啟動指紋掃描器，以及從掃描器回應意見反應。 此類別具有相當簡單的介面與只有三個成員：

* **`Authenticate`** &ndash; 這個方法會初始化硬體掃描器，並在背景中，等候使用者掃描其指紋即可，啟動服務。
* **`EnrolledFingerprints`** &ndash; 這個屬性會傳回`true`如果使用者已向裝置的一或多個指紋。
* **`HardwareDetected`** &ndash; 這個屬性用來判斷裝置是否支援指紋掃描。

`FingerprintManager.Authenticate` Android 應用程式會使用方法啟動指紋掃描器。 下列程式碼片段是如何叫用使用支援程式庫相容性 Api 的範例：

```csharp
// context is any Android.Content.Context instance, typically the Activity 
FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(context);
fingerprintManager.Authenticate(FingerprintManager.CryptoObject crypto,
                                int flags,
                                CancellationSignal cancel,
                                FingerprintManagerCompat.AuthenticationCallback callback,
                                Handler handler
                               );
```

本指南會討論如何使用`FingerprintManager`以增強與指紋驗證 Android 應用程式的 Api。 它將討論如何具現化，並建立`CryptoObject`來協助保護指紋掃描器的結果。 我們將檢驗如何應用程式應該子類別`FingerprintManager.AuthenticationCallback`和意見回應來自指紋掃描器。 最後，我們會看到如何註冊 Android 裝置或模擬器上的指紋，以及如何使用**adb**模擬指紋掃描。

## <a name="requirements"></a>需求

指紋驗證需要 Android 6.0 （API 層級 23） 或更高版本以及裝置與指紋掃描器。 

指紋必須已經向是驗證每一位使用者的裝置註冊。 這包括使用密碼、 PIN、 撥動模式或臉部辨識的螢幕鎖定的設定。 就可以模擬一些 Android 模擬器中的指紋驗證功能。  如需有關這些兩個主題的詳細資訊，請參閱[註冊指紋](enrolling-fingerprint.md)一節。 






## <a name="related-links"></a>相關連結

- [指紋指南範例應用程式](https://developer.xamarin.com/samples/monodroid/FingerprintGuide/)
- [指紋對話方塊範例](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog/)
- [在執行階段的要求權限](https://developer.android.com/training/permissions/requesting.html)
- [android.hardware.fingerprint](https://developer.android.com/reference/android/hardware/fingerprint/package-summary.html)
- [android.support.v4.hardware.fingerprint](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/package-summary.html)
- [Android.Content.Context](https://developer.xamarin.com/api/type/Android.Content.Context/)
- [指紋與付款 API （影片）](https://youtu.be/VOn7VrTRlA4)
