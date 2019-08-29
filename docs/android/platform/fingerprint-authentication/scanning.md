---
title: 掃描指紋
ms.prod: xamarin
ms.assetid: 1CDDC096-77E0-47B3-BE0B-8953E2DDACD3
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/23/2016
ms.openlocfilehash: 15afd5b1812e0423097e889cd8c2558ca01a8074
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70119747"
---
# <a name="scanning-for-fingerprints"></a>掃描指紋

現在, 我們已瞭解如何準備 Xamarin 應用程式以使用指紋驗證, 讓我們回到`FingerprintManager.Authenticate`方法, 並討論其在 Android 6.0 指紋驗證中的位置。 如需指紋驗證的工作流程快速總覽, 請參閱這份清單:

1. 叫用`FingerprintManager.Authenticate` `CryptoObject` , 並傳遞和`FingerprintManager.AuthenticationCallback`實例。 `CryptoObject`是用來確保指紋驗證結果不會遭到篡改。 
2. 子類別化[FingerprintManager. authenticationcallback 傳給](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)類別。 當指紋驗證開始時, `FingerprintManager`將會提供這個類別的實例。 當指紋掃描器完成時, 它會叫用這個類別上的其中一個回呼方法。
3. 撰寫程式碼來更新 UI, 讓使用者知道裝置已啟動指紋掃描器, 並且正在等候使用者互動。 
4. 當指紋掃描器完成時, Android 會藉由在上一個步驟中所提供的`FingerprintManager.AuthenticationCallback`實例上叫用方法, 將結果傳回給應用程式。
5. 應用程式會通知使用者指紋驗證結果, 並適當地回應結果。 

下列程式碼片段是活動中將開始掃描指紋的方法範例:

```csharp
protected void FingerPrintAuthenticationExample()
{
    const int flags = 0; /* always zero (0) */

    // CryptoObjectHelper is described in the previous section.
    CryptoObjectHelper cryptoHelper = new CryptoObjectHelper();    
    
    // cancellationSignal can be used to manually stop the fingerprint scanner. 
    cancellationSignal = new Android.Support.V4.OS.CancellationSignal();
    
    FingerprintManagerCompat fingerprintManager = FingerprintManagerCompat.From(this);
    
    // AuthenticationCallback is a base class that will be covered later on in this guide.
    FingerprintManagerCompat.AuthenticationCallback authenticationCallback = new MyAuthCallbackSample(this);

    // Start the fingerprint scanner.
    fingerprintManager.Authenticate(cryptoHelper.BuildCryptoObject(), flags, cancellationSignal, authenticationCallback, null);
}
```

讓我們更詳細地討論`Authenticate`方法中的每個參數:

- 第一個參數是一種_加密_物件, 指紋掃描器將用它來協助驗證指紋掃描的結果。 這個物件可能是`null`, 在這種情況下, 應用程式必須盲目地信任未遭指紋結果的任何修改。 建議`CryptoObject`將具現化並提供`FingerprintManager`給, 而不是 null。 [建立 CryptObject](~/android/platform/fingerprint-authentication/creating-a-cryptoobject.md)將詳細說明如何`CryptoObject`根據`Cipher`來具現化。
- 第二個參數一律為零。 Android 檔將其識別為一組旗標, 且最有可能保留供未來使用。 
- 第三個參數`cancellationSignal`是用來關閉指紋掃描器並取消目前要求的物件。 這是[Android CancellationSignal](https://developer.android.com/reference/android/os/CancellationSignal.html), 而不是 .net framework 中的類型。
- 第四個參數是必要的, 而且是子類別`AuthenticationCallback`化抽象類別的類別。 此類別上的`FingerprintManager`方法將會叫用, 以在完成時通知用戶端, 以及結果為何。 因為要瞭解如何執行`AuthenticationCallback`, 它會在[它自己的章節](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md)中討論。
- 第五個參數是選擇性`Handler`的實例。 如果提供`FingerprintManager` `Looper`物件, 則會在處理來自指紋硬體的訊息時, 使用該物件中的。 `Handler` 一般而言, 一個不需要提供`Handler`, FingerprintManager 會`Looper`從應用程式使用。

## <a name="cancelling-a-fingerprint-scan"></a>取消指紋掃描

使用者 (或應用程式) 可能需要在啟動後取消指紋掃描。 在此情況下, 請[`IsCancelled`](https://developer.android.com/reference/android/os/CancellationSignal.html#isCanceled())在所提供[`CancellationSignal`](https://developer.android.com/reference/android/os/CancellationSignal.html) `FingerprintManager.Authenticate`的上叫用方法, 以啟動指紋掃描。

既然我們已經看過`Authenticate`方法, 讓我們來仔細檢查一些更重要的參數。 首先, 我們將探討如何[回應驗證回呼](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md), 這將討論如何將[FingerprintManager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)子類別化, 讓 Android 應用程式回應指紋掃描器所提供的結果。




## <a name="related-links"></a>相關連結

- [CancellationSignal](https://developer.android.com/reference/android/os/CancellationSignal.html)
- [FingerprintManager.AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [FingerprintManager.CryptoObject](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat.CryptoObject](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [FingerprintManager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
