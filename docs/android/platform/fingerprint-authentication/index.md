---
title: 指紋驗證
description: 本指南將討論如何將 Android 6.0 中引進的指紋驗證新增至 Xamarin. Android 應用程式。
ms.prod: xamarin
ms.assetid: 6742D874-4988-4516-A946-D5C714B20A10
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: a3fd03ece3618172e68539e2a92e8924e85e2ce5
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453879"
---
# <a name="fingerprint-authentication"></a>指紋驗證

_本指南將討論如何將 Android 6.0 中引進的指紋驗證新增至 Xamarin. Android 應用程式。_

## <a name="fingerprint-authentication-overview"></a>指紋驗證總覽

Android 裝置上的指紋掃描器抵達會提供應用程式替代使用者驗證的傳統使用者名稱/密碼方法。 使用指紋來驗證使用者，可讓應用程式併入比使用者名稱和密碼更不侵入的安全性。

FingerprintManager Api 會將具有指紋掃描器的裝置設為目標，且執行的 API 層級為 23 (Android 6.0) 或更高版本。 Api 可在 `Android.Hardware.Fingerprints` 命名空間中找到。 Android 支援程式庫 v4 提供適用于較舊版本 Android 的指紋 Api 版本。 相容性 Api 可在 `Android.Support.v4.Hardware.Fingerprint` 命名空間中找到，並透過 [Xamarin NuGet 套件](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)散發。

[FingerprintManager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html) (及其支援程式庫對應， [FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)) 是使用指紋掃描硬體的主要類別。 此類別是系統層級服務周圍的 Android SDK 包裝函式，可管理與硬體本身的互動。 它負責啟動指紋掃描器，以及回應掃描器的意見反應。 此類別具有相當直接的介面，僅有三個成員：

- **`Authenticate`**&ndash;此方法將會初始化硬體掃描器，並在背景啟動服務，以等待使用者掃描其指紋。
- **`EnrolledFingerprints`**&ndash; `true` 如果使用者已在裝置上註冊一或多個指紋，則此屬性會傳回。
- **`HardwareDetected`**&ndash;這個屬性是用來判斷裝置是否支援指紋掃描。

`FingerprintManager.Authenticate`Android 應用程式會使用方法來啟動指紋掃描器。 下列程式碼片段是如何使用支援程式庫相容性 Api 來叫用它的範例：

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

本指南將討論如何使用 `FingerprintManager` api 來增強具有指紋驗證的 Android 應用程式。 它將說明如何具現化和建立， `CryptoObject` 以協助保護指紋掃描器的結果。 我們會檢查應用程式如何 `FingerprintManager.AuthenticationCallback` 從指紋掃描器子類別化和回應意見反應。 最後，我們將瞭解如何在 Android 裝置或模擬器上註冊指紋，以及如何使用 **adb** 來模擬指紋掃描。

## <a name="requirements"></a>需求

指紋驗證需要 Android 6.0 (API 層級 23) 或更高版本，以及具有指紋掃描器的裝置。 

指紋必須已向裝置註冊，以供要驗證的每個使用者使用。 這牽涉到設定使用密碼、PIN、滑動線模式或臉部辨識的螢幕鎖定。 您可以在 Android Emulator 中模擬部分指紋驗證功能。  如需這兩個主題的詳細資訊，請參閱 [註冊指紋](enrolling-fingerprint.md) 一節。 

## <a name="related-links"></a>相關連結

- [指紋指南範例應用程式](/samples/xamarin/monodroid-samples/fingerprintguide)
- [指紋對話方塊範例](/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)
- [在執行時間要求許可權](https://developer.android.com/training/permissions/requesting.html)
- [android. 硬體指紋](https://developer.android.com/reference/android/hardware/fingerprint/package-summary.html)
- [android.. a.。](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/package-summary.html)
- [Android. 內容](xref:Android.Content.Context)
- [指紋和付款 API (影片) ](https://youtu.be/VOn7VrTRlA4)