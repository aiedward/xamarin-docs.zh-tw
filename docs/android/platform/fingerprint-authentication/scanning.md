---
title: 掃描指紋
ms.prod: xamarin
ms.assetid: 1CDDC096-77E0-47B3-BE0B-8953E2DDACD3
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/23/2016
ms.openlocfilehash: 61edd0e4b532f18a8fc28502e5bb990703068776
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027499"
---
# <a name="scanning-for-fingerprints"></a>掃描指紋

現在,我們已經看到了如何準備Xamarin.Android應用程式來使用指紋認證,讓我們回到`FingerprintManager.Authenticate`該方法,並討論它在Android6.0指紋認證的位置。 此清單中介紹了指紋身份驗證工作流的快速概述:

1. 呼叫`FingerprintManager.Authenticate`,`CryptoObject``FingerprintManager.AuthenticationCallback`傳遞 與實體。 `CryptoObject`用於確保指紋身份驗證結果未被篡改。 
2. 子類[指紋管理員.身份驗證回調](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)類。 指紋身份驗證開始時,將提供`FingerprintManager`此類的實例。 指紋掃描程式完成後,它將調用此類上的回調方法之一。
3. 編寫代碼以更新 UI,讓使用者知道設備已啟動指紋掃描程式並正在等待使用者互動。 
4. 完成指紋掃描程式后,Android 將通過調用上一步中`FingerprintManager.AuthenticationCallback`提供的 實例上的方法將結果返回給應用程式。
5. 應用程式將通知使用者指紋身份驗證結果,並酌情對結果做出反應。 

以下代碼段是活動中將開始掃描指紋的方法的範例:

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

- 第一個參數是_指紋_掃描程式將用於幫助驗證指紋掃描結果的加密物件。 此物件可能是`null`,在這種情況下,應用程式必須盲目地相信沒有任何內容篡改指紋結果。 建議實體化,`CryptoObject`並提供給`FingerprintManager`而不是 null。 [建立 CryptObject](~/android/platform/fingerprint-authentication/creating-a-cryptoobject.md)會詳細`CryptoObject`解釋如何 的實體`Cipher`化 。
- 第二個參數始終為零。 Android 文檔將其標識為一組標誌,並且很可能保留以供將來使用。 
- 第三個參數`cancellationSignal`是用於關閉指紋掃描器並取消當前請求的物件。 這是一個[Android 取消信號](https://developer.android.com/reference/android/os/CancellationSignal.html),而不是從.NET框架的類型。
- 第四個參數是強制性的,是子類的`AuthenticationCallback`類。 將調用此類上的方法,在完成 和結果`FingerprintManager`時 向用戶端發出信號。 由於對實現有很多瞭解`AuthenticationCallback`,它將在[它自己的部分](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md)中介紹。
- 第五個參數是可選`Handler`實例。 如果提供了`Handler`一個物件,`FingerprintManager`則在處理`Looper`來自指紋硬體的消息時,將使用 來自該物件的消息。 通常,不需要提供`Handler`, 指紋管理器`Looper`將使用 來自的應用程式。

## <a name="cancelling-a-fingerprint-scan"></a>解除指紋掃描

使用者(或應用程式)可能需要在啟動指紋掃描後取消該掃描。 在此情況下,調用 調[`IsCancelled`](https://developer.android.com/reference/android/os/CancellationSignal.html#isCanceled())用 時`FingerprintManager.Authenticate`[`CancellationSignal`](https://developer.android.com/reference/android/os/CancellationSignal.html)提供給 的方法以啟動指紋掃描。

現在我們已經看到了該方法,`Authenticate`讓我們更詳細地研究一些更重要的參數。 首先,我們將介紹[回應身份驗證回調](~/android/platform/fingerprint-authentication/fingerprint-authentication-callbacks.md),這將討論如何對[指紋管理器](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)進行子類化。

## <a name="related-links"></a>相關連結

- [取消信號](https://developer.android.com/reference/android/os/CancellationSignal.html)
- [指紋管理員.身份驗證回調](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [指紋管理員.加密物件](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [指紋管理員複合物.加密物件](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [指紋管理員](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html)
- [指紋管理員康柏](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.html)
