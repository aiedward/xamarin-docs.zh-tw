---
title: 指紋驗證
description: 本指南討論如何將 Android 6.0 中介紹的指紋身份驗證添加到 Xamarin.Android 應用程式中。
ms.prod: xamarin
ms.assetid: 6742D874-4988-4516-A946-D5C714B20A10
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 4a4b6ee7a123683a9d5a140c46c0b3542767ffa3
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027517"
---
# <a name="fingerprint-authentication"></a>指紋驗證

_本指南討論如何將 Android 6.0 中介紹的指紋身份驗證添加到 Xamarin.Android 應用程式中。_

## <a name="fingerprint-authentication-overview"></a>指紋身份驗證概述

指紋掃描器在 Android 裝置上的到達為應用程式提供了使用者身份驗證的傳統使用者名/密碼方法的替代方案。 使用指紋對使用者進行身份驗證,應用程式可以納入比使用者名和密碼侵入性較低的安全性。

指紋管理器 API 以具有指紋掃描器的設備為目標,並且運行 API 級別 23 (Android 6.0) 或更高版本。 API 在命名空間中`Android.Hardware.Fingerprints`找到 。 Android 支援庫 v4 提供適用於舊版本的 Android 的指紋 API 版本。 相容性 API`Android.Support.v4.Hardware.Fingerprint`在 命名空間中找到,透過[Xamarin.Android.support.v4 NuGet 包](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)分發。

[指紋管理員](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)(及其支援庫對應的[指紋管理器 Compat)](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)是使用指紋掃描硬體的主要類。 此類是圍繞系統級服務管理與硬體本身交互的 Android SDK 包裝器。 它負責啟動指紋掃描器並響應掃描器的反饋。 此類具有相當簡單的介面,只有三個成員:

- **`Authenticate`**&ndash;此方法將初始化硬體掃描器並在後台啟動服務,等待使用者掃描其指紋。
- **`EnrolledFingerprints`**&ndash;如果使用者在設備中`true`註冊了一個或多個指紋,則此屬性將返回。
- **`HardwareDetected`**&ndash;此屬性用於確定設備是否支援指紋掃描。

Android`FingerprintManager.Authenticate`應用程式使用此方法啟動指紋掃描程式。 以下代碼是如何使用支援函式庫相容性 API 呼叫它的範例:

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

本指南將討論如何使用`FingerprintManager`API 來增強具有指紋身份驗證的 Android 應用程式。 它將介紹如何實例化並創建 一`CryptoObject`個 來説明從指紋掃描器中保護結果。 我們將檢查應用程式應該如何對子類進行子類`FingerprintManager.AuthenticationCallback`,並回應來自指紋掃描器的反饋。 最後,我們將瞭解如何在 Android 設備或模擬器上註冊指紋,以及如何使用**adb**模擬指紋掃描。

## <a name="requirements"></a>需求

指紋認證要求 Android 6.0(API 級別 23)或更高版本以及配備指紋掃描器的設備。 

對於要進行身份驗證的每個使用者,必須已使用設備註冊指紋。 這涉及設置使用密碼、PIN、輕掃模式或面部識別的螢幕鎖。 可以在 Android 模擬器中類比某些指紋身份驗證功能。  有關這兩個主題的詳細資訊,請參閱[註冊指紋](enrolling-fingerprint.md)部分。 

## <a name="related-links"></a>相關連結

- [指紋指南範例應用程式](https://docs.microsoft.com/samples/xamarin/monodroid-samples/fingerprintguide)
- [指紋對話框範例](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)
- [在執行時要求權限](https://developer.android.com/training/permissions/requesting.html)
- [安卓.硬體.指紋](https://developer.android.com/reference/android/hardware/fingerprint/package-summary.html)
- [安卓.支援.v4.硬體.指紋](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/package-summary.html)
- [安卓.內容.上下文](xref:Android.Content.Context)
- [指紋和支付 API(視訊)](https://youtu.be/VOn7VrTRlA4)
