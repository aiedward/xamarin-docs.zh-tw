---
title: 掃描指紋
ms.prod: xamarin
ms.assetid: 1CDDC096-77E0-47B3-BE0B-8953E2DDACD3
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/23/2016
ms.openlocfilehash: ed7f31b011c32b25f431801e47c570900589e131
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106321"
---
# <a name="scanning-for-fingerprints"></a>掃描指紋

既然我們已了解如何準備 Xamarin.Android 應用程式使用指紋驗證，讓我們回到`FingerprintManager.Authenticate`方法，並討論在 Android 6.0 指紋驗證。 指紋驗證的工作流程的快速概觀會說明這份清單：

1. 叫用`FingerprintManager.Authenticate`，並傳遞`CryptoObject`和`FingerprintManager.AuthenticationCallback`執行個體。 `CryptoObject`用來確保指紋驗證結果未遭他人修改。 
2. 子類別[FingerprintManager.AuthenticationCallback](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)類別。 此類別的執行個體將會提供給`FingerprintManager`當指紋驗證開始。 指紋掃描器完成時，它就會將其中一種回呼方法，這個類別上叫用。
3. 撰寫程式碼來更新 UI，讓使用者知道該裝置已啟動指紋掃描器和正在等候使用者互動。 
4. 指紋掃描器完成時，Android 會傳回結果至應用程式上叫用方法`FingerprintManager.AuthenticationCallback`上一個步驟中所提供的執行個體。
5. 應用程式會通知使用者已將指紋驗證結果，並做出回應以適當的結果。 

下列程式碼片段是會開始掃描指紋活動中方法的範例：

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

讓我們來討論每個這些參數在`Authenticate`更多詳細資料中的方法：

* 第一個參數是_crypto_物件指紋掃描器，將會用來協助進行驗證的指紋掃描結果。 這個物件可能`null`，在此情況下，應用程式必須盲目地信任沒有已竄改指紋辨識結果。 建議`CryptoObject`具現化並提供給`FingerprintManager`而不是 null。 [建立 CryptObject](~/android/platform/fingerprint-authentication/creating-a-cryptoobject.md)會詳細說明如何具現化`CryptoObject`根據`Cipher`。
* 第二個參數一律為零。 Android 文件識別這組旗標，並很可能是保留供未來使用。 
* 第三個參數，`cancellationSignal`是用來關閉指紋掃描器，並取消目前的要求物件。 這是[Android CancellationSignal](http://developer.android.com/reference/android/os/CancellationSignal.html)，而非從.NET framework 型別。
* 第四個參數是必要的是一種類別子類別化`AuthenticationCallback`抽象類別。 此類別的方法將會叫用來通知用戶端時`FingerprintManager`已完成，以及它們的結果。 因為有許多需要了解實作`AuthenticationCallback`，它將涵蓋[是自己的一節](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md)。
* 第五個參數是選擇性`Handler`執行個體。 如果`Handler`提供物件，則`FingerprintManager`將使用`Looper`從該物件處理從指紋硬體訊息時。 一般而言，一個不需要提供`Handler`，將會使用 FingerprintManager`Looper`從應用程式。

## <a name="cancelling-a-fingerprint-scan"></a>正在取消指紋掃描

它可能需要針對使用者 （或應用程式） 後已起始取消指紋掃描。 在此情況下，叫用[ `IsCancelled` ](http://developer.android.com/reference/android/os/CancellationSignal.html#isCanceled())方法[ `CancellationSignal` ](http://developer.android.com/reference/android/os/CancellationSignal.html) ，提供給`FingerprintManager.Authenticate`它已叫用時，開始的指紋掃描。

既然我們已經看到`Authenticate`方法，讓我們來檢查一些更重要的參數，在更多詳細資料。 首先，我們將探討[回應驗證回呼](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md)，其中將討論如何子類別[FingerprintManager.AuthenticationCallback](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)，讓 Android 應用程式做出回應指紋掃描器所提供的結果。




## <a name="related-links"></a>相關連結

- [CancellationSignal](http://developer.android.com/reference/android/os/CancellationSignal.html)
- [FingerprintManager.AuthenticationCallback](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [FingerprintManager.CryptoObject](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat.CryptoObject](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [FingerprintManager](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
