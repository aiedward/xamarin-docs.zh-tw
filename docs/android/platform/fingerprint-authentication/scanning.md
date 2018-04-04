---
title: 掃描指紋
ms.prod: xamarin
ms.assetid: 1CDDC096-77E0-47B3-BE0B-8953E2DDACD3
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/23/2016
ms.openlocfilehash: 2b20402fd2cd6782247fb2c62513d7aff43a95a6
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="scanning-for-fingerprints"></a>掃描指紋

既然我們已經看到如何準備使用指紋驗證 Xamarin.Android 應用程式，讓我們回到`FingerprintManager.Authenticate`方法，並討論中 Android 6.0 指紋驗證它的位置。 這份清單中描述的指紋驗證工作流程的快速概觀：

1. 叫用`FingerprintManager.Authenticate`，並傳遞`CryptoObject`和`FingerprintManager.AuthenticationCallback`執行個體。 `CryptoObject`用來確保指紋驗證結果未遭竄改。 
2. 子類別[FingerprintManager.AuthenticationCallback](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)類別。 這個類別的執行個體將會提供給`FingerprintManager`當指紋驗證開始。 指紋掃描器完成時，它會叫用其中一種回呼方法，這個類別。
3. 撰寫程式碼以更新 UI，以讓使用者知道該裝置已啟動指紋掃描器，且正在等候使用者互動。 
4. 當完成指紋掃描器時，Android 會結果傳回至應用程式上叫用方法`FingerprintManager.AuthenticationCallback`上一個步驟中提供的執行個體。
5. 應用程式會通知使用者的指紋驗證結果，並做出適當的結果。 

下列程式碼片段是活動將會開始掃描指紋中方法的範例：

```csharp
protected void FingerPrintAuthenticationExample()
{
    const int flags = 0; /* always zero (0) */

    // CryptoObjectHelper is described in the previous section.
    CryptoObjectHelper cryptoHelper = new CryptoObjectHelper();    
    
    // cancellationSignal can be used to manually stop the fingerprint scanner. 
    cancellationSignal = new Android.Support.V4.OS.CancellationSignal();
    
    FingerprintManagerCompat fingerPrintManager = FingerprintManagerCompat.From(this);
    
    // AuthenticationCallback is a base class that will be covered later on in this guide.
    FingerprintManagerCompat.AuthenticationCallback authenticationCallback = new MyAuthCallbackSample(this);

    // Start the fingerprint scanner.
    fingerprintManager.Authenticate(cryptoHelper.BuildCryptoObject(), flags, cancellationSignal, authenticationCallback, null);
}
```

讓我們來討論每個參數中`Authenticate`方法中更多詳細資料：

* 第一個參數是_crypto_物件指紋掃描器會用來協助驗證指紋掃描的結果。 這個物件可能`null`，應用程式在此情況下必須盲目地信任該動作已遭竄改指紋結果。 建議`CryptoObject`具現化並提供給`FingerprintManager`而不是 null。 [建立 CryptObject](~/android/platform/fingerprint-authentication/creating-a-cryptoobject.md)將說明詳細如何具現化`CryptoObject`根據`Cipher`。
* 第二個參數永遠是零。 Android 的文件識別這組旗標為和很可能是保留供未來使用。 
* 第三個參數，`cancellationSignal`是用來關閉指紋掃描器並取消目前要求的物件。 這是[Android CancellationSignal](http://developer.android.com/reference/android/os/CancellationSignal.html)，且不會從.NET framework 型別。
* 第四個參數是強制性，是一種類別的子類別`AuthenticationCallback`抽象類別。 這個類別的方法將會通知用戶端叫用時`FingerprintManager`已完成，以及結果。 因為沒有很多了解實作`AuthenticationCallback`，都將涵蓋於[是自己區段](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md)。
* 第五個參數是選用`Handler`執行個體。 如果`Handler`提供物件時，`FingerprintManager`將使用`Looper`從處理指紋硬體的訊息時，該物件。 一般而言，一個不需要提供`Handler`，將會使用 FingerprintManager`Looper`從應用程式。

## <a name="cancelling-a-fingerprint-scan"></a>取消指紋掃描

它可能需要取消指紋掃描之後已起始使用者 （或應用程式）。 在此情況下，叫用[ `IsCancelled` ](http://developer.android.com/reference/android/os/CancellationSignal.html#isCanceled())方法[ `CancellationSignal` ](http://developer.android.com/reference/android/os/CancellationSignal.html) ，提供給`FingerprintManager.Authenticate`它已叫用時，開始的指紋掃描。

既然我們已經看到`Authenticate`方法，讓我們來檢查部份詳細更重要的參數。 首先，我們會探討[回應驗證回呼](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md)，其中將討論如何子類別[FingerprintManager.AuthenticationCallback](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)，啟用 Android 應用程式以回應提供由指紋掃描的結果。




## <a name="related-links"></a>相關連結

- [CancellationSignal](http://developer.android.com/reference/android/os/CancellationSignal.html)
- [FingerprintManager.AuthenticationCallback](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [FingerprintManager.CryptoObject](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat.CryptoObject](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [FingerprintManager](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
