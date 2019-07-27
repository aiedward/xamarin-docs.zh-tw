---
title: 指紋驗證
description: 本指南討論如何將 Android 6.0 中引進的指紋驗證新增至 Xamarin Android 應用程式。
ms.prod: xamarin
ms.assetid: 6742D874-4988-4516-A946-D5C714B20A10
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 7917f0cc03a4079d6b2adaba37fdc0332f8626c8
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510598"
---
# <a name="fingerprint-authentication"></a>指紋驗證

_本指南討論如何將 Android 6.0 中引進的指紋驗證新增至 Xamarin Android 應用程式。_


## <a name="fingerprint-authentication-overview"></a>指紋驗證總覽

Android 裝置上的指紋掃描器抵達會提供應用程式, 替代傳統使用者名稱/密碼方法的使用者驗證。 使用指紋來驗證使用者, 可以讓應用程式併入比使用者名稱和密碼更少侵入的安全性。

FingerprintManager Api 會以具有指紋掃描器的裝置為目標, 且執行的是 API 層級 23 (Android 6.0) 或更高版本。 Api 會在`Android.Hardware.Fingerprints`命名空間中找到。 Android 支援程式庫 v4 提供適用于較舊版本 Android 的指紋 Api 版本。 在`Android.Support.v4.Hardware.Fingerprint`命名空間中找到相容性 api, 會透過[支援 v4 NuGet 封裝](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)來散發。

[FingerprintManager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html) (及其支援程式庫對應的[FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)) 是使用指紋掃描硬體的主要類別。 這個類別是管理與硬體本身互動之系統層級服務的 Android SDK 包裝函式。 它負責啟動指紋掃描器, 以及回應掃描器的意見反應。 這個類別具有相當直接的介面, 只有三個成員:

* **`Authenticate`** &ndash;這個方法會初始化硬體掃描器, 並在背景啟動服務, 等待使用者掃描其指紋。
* **`EnrolledFingerprints`** 如果使用者`true`已向裝置註冊一或多個指紋, 則此屬性會傳回。 &ndash;
* **`HardwareDetected`** &ndash;此屬性是用來判斷裝置是否支援指紋掃描。

Android 應用程式會使用方法來啟動指紋掃描器。`FingerprintManager.Authenticate` 下列程式碼片段是如何使用支援程式庫相容性 Api 叫用它的範例:

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

本指南將討論如何使用`FingerprintManager` api 來增強具有指紋驗證的 Android 應用程式。 其中將說明如何具現化和建立`CryptoObject` , 以協助保護指紋掃描器的結果。 我們將檢查應用程式應該如何從`FingerprintManager.AuthenticationCallback`指紋掃描器子類別化和回應意見反應。 最後, 我們將瞭解如何在 Android 裝置或模擬器上註冊指紋, 以及如何使用**adb**來模擬指紋掃描。

## <a name="requirements"></a>需求

指紋驗證需要 Android 6.0 (API 層級 23) 或更高版本, 以及具有指紋掃描器的裝置。 

指紋必須已經向要驗證之每個使用者的裝置註冊。 這牽涉到設定使用密碼、PIN、輕刷模式或臉部辨識的螢幕鎖定。 您可以在 Android Emulator 中模擬部分指紋驗證功能。  如需這兩個主題的詳細資訊, 請參閱[註冊指紋](enrolling-fingerprint.md)一節。 






## <a name="related-links"></a>相關連結

- [指紋指南範例應用程式](https://developer.xamarin.com/samples/monodroid/FingerprintGuide/)
- [指紋對話方塊範例](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog/)
- [在執行時間要求許可權](https://developer.android.com/training/permissions/requesting.html)
- [android.hardware.fingerprint](https://developer.android.com/reference/android/hardware/fingerprint/package-summary.html)
- [android.support.v4.hardware.fingerprint](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/package-summary.html)
- [Android.Content.Context](xref:Android.Content.Context)
- [指紋和付款 API (影片)](https://youtu.be/VOn7VrTRlA4)
