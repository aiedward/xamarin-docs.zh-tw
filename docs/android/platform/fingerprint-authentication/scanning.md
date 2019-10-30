---
title: 掃描指紋
ms.prod: xamarin
ms.assetid: 1CDDC096-77E0-47B3-BE0B-8953E2DDACD3
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/23/2016
ms.openlocfilehash: 61edd0e4b532f18a8fc28502e5bb990703068776
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027499"
---
# <a name="scanning-for-fingerprints"></a>掃描指紋

既然我們已瞭解如何準備 Xamarin 應用程式以使用指紋驗證，讓我們回到 `FingerprintManager.Authenticate` 方法，並討論其在 Android 6.0 指紋驗證中的位置。 如需指紋驗證的工作流程快速總覽，請參閱這份清單：

1. 叫用 `FingerprintManager.Authenticate`，傳遞 `CryptoObject` 和 `FingerprintManager.AuthenticationCallback` 實例。 `CryptoObject` 是用來確保指紋驗證結果不會遭到篡改。 
2. 子類別化[FingerprintManager. authenticationcallback 傳給](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)類別。 當指紋驗證啟動時，將會提供此類別的實例給 `FingerprintManager`。 當指紋掃描器完成時，它會叫用這個類別上的其中一個回呼方法。
3. 撰寫程式碼來更新 UI，讓使用者知道裝置已啟動指紋掃描器，並且正在等候使用者互動。 
4. 當指紋掃描器完成時，Android 會藉由在上一個步驟所提供的 `FingerprintManager.AuthenticationCallback` 實例上叫用方法，將結果傳回給應用程式。
5. 應用程式會通知使用者指紋驗證結果，並適當地回應結果。 

下列程式碼片段是活動中將開始掃描指紋的方法範例：

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

讓我們更詳細地討論 `Authenticate` 方法中的每個參數：

- 第一個參數是一種_加密_物件，指紋掃描器將用它來協助驗證指紋掃描的結果。 此物件可能是 `null`的，在此情況下，應用程式必須盲目地信任該內容是否已遭篡改指紋結果。 建議 `CryptoObject` 具現化，並提供給 `FingerprintManager`，而不是 null。 [建立 CryptObject](~/android/platform/fingerprint-authentication/creating-a-cryptoobject.md)將詳細說明如何根據 `Cipher`來具現化 `CryptoObject`。
- 第二個參數一律為零。 Android 檔將其識別為一組旗標，且最有可能保留供未來使用。 
- 第三個參數 `cancellationSignal` 是用來關閉指紋掃描器並取消目前要求的物件。 這是[Android CancellationSignal](https://developer.android.com/reference/android/os/CancellationSignal.html)，而不是 .net framework 中的類型。
- 第四個參數是必要的，而且是子類別，會將 `AuthenticationCallback` 抽象類別分類。 當 `FingerprintManager` 完成且結果為時，將會叫用此類別上的方法，以對用戶端發出信號。 因為要瞭解如何執行 `AuthenticationCallback`，它會在[它自己的一節](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md)中討論。
- 第五個參數是選擇性的 `Handler` 實例。 如果提供了 `Handler` 物件，則在處理來自指紋硬體的訊息時，`FingerprintManager` 將會使用該物件中的 `Looper`。 一般來說，您不需要提供 `Handler`，FingerprintManager 將會使用應用程式中的 `Looper`。

## <a name="cancelling-a-fingerprint-scan"></a>取消指紋掃描

使用者（或應用程式）可能需要在啟動後取消指紋掃描。 在此情況下，請在所提供的[`CancellationSignal`](https://developer.android.com/reference/android/os/CancellationSignal.html)上叫用[`IsCancelled`](https://developer.android.com/reference/android/os/CancellationSignal.html#isCanceled())方法，以在叫用以啟動指紋掃描時 `FingerprintManager.Authenticate`。

既然我們已經看過 `Authenticate` 方法，讓我們來仔細檢查一些更重要的參數。 首先，我們將探討如何[回應驗證回呼](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md)，這將討論如何將[FingerprintManager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)子類別化，讓 Android 應用程式回應指紋掃描器所提供的結果。

## <a name="related-links"></a>相關連結

- [CancellationSignal](https://developer.android.com/reference/android/os/CancellationSignal.html)
- [FingerprintManager. Authenticationcallback 傳給](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [FingerprintManager. CryptoObject](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat. CryptoObject](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [FingerprintManager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [FingerprintManagerCompat](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
