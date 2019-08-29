---
title: 回應驗證回呼
ms.prod: xamarin
ms.assetid: 6533AFC9-1A1C-4897-A154-4D4ECFE27761
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/06/2017
ms.openlocfilehash: f1fc484931ba7a574ac660b4856f20b1cb1e08a3
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70119590"
---
# <a name="responding-to-authentication-callbacks"></a>回應驗證回呼

指紋掃描器會在自己的執行緒上以背景執行, 而完成後, 它會在 UI 執行緒上叫用的`FingerprintManager.AuthenticationCallback`一個方法, 以報告掃描的結果。 Android 應用程式必須提供自己的處理常式, 以擴充此抽象類別, 並執行下列所有方法:

- **`OnAuthenticationError(int errorCode, ICharSequence errString)`** &ndash;當發生無法復原的錯誤時呼叫。 除了可能會再試一次以外, 應用程式或使用者不能執行任何動作來修正這種情況。
- **`OnAuthenticationFailed()`** &ndash;當偵測到指紋但裝置無法辨識時, 就會叫用此方法。
- **`OnAuthenticationHelp(int helpMsgId, ICharSequence helpString)`** &ndash;當有可復原的錯誤時呼叫, 例如透過掃描器快速撥動的手指。
- **`OnAuthenticationSucceeded(FingerprintManagerCompati.AuthenticationResult result)`** &ndash;當指紋已被辨識時, 就會呼叫此程式。

如果在呼叫`Authenticate`時使用了, 建議您在中`OnAuthenticationSuccessful`呼叫`Cipher.DoFinal`。 `CryptoObject`
`DoFinal`如果加密遭篡改或初始化不正確, 將會擲回例外狀況, 表示指紋掃描器的結果可能已在應用程式外部遭到篡改。


> [!NOTE]
> 建議讓回呼類別保持相對輕量, 並無應用程式特定邏輯。 回呼應作為 Android 應用程式與指紋掃描器的結果之間的「流量 cop」。

## <a name="a-sample-authentication-callback-handler"></a>驗證回呼處理常式範例

下列類別是最小`FingerprintManager.AuthenticationCallback`執行的範例: 

```csharp
class MyAuthCallbackSample : FingerprintManagerCompat.AuthenticationCallback
{
    // Can be any byte array, keep unique to application.
    static readonly byte[] SECRET_BYTES = {1, 2, 3, 4, 5, 6, 7, 8, 9};
    // The TAG can be any string, this one is for demonstration.
    static readonly string TAG = "X:" + typeof (SimpleAuthCallbacks).Name;

    public MyAuthCallbackSample()
    {
    }

    public override void OnAuthenticationSucceeded(FingerprintManagerCompat.AuthenticationResult result)
    {
        if (result.CryptoObject.Cipher != null) 
        {
            try
            {
                // Calling DoFinal on the Cipher ensures that the encryption worked.
                byte[] doFinalResult = result.CryptoObject.Cipher.DoFinal(SECRET_BYTES);
    
                // No errors occurred, trust the results.              
            }
            catch (BadPaddingException bpe)
            {
                // Can't really trust the results.
                Log.Error(TAG, "Failed to encrypt the data with the generated key." + bpe);
            }
            catch (IllegalBlockSizeException ibse)
            {
                // Can't really trust the results.
                Log.Error(TAG, "Failed to encrypt the data with the generated key." + ibse);
            }
        }
        else
        {
            // No cipher used, assume that everything went well and trust the results.
        }
    }

    public override void OnAuthenticationError(int errMsgId, ICharSequence errString)
    {
        // Report the error to the user. Note that if the user canceled the scan,
        // this method will be called and the errMsgId will be FingerprintState.ErrorCanceled.
    }

    public override void OnAuthenticationFailed()
    {
        // Tell the user that the fingerprint was not recognized.
    }

    public override void OnAuthenticationHelp(int helpMsgId, ICharSequence helpString)
    {
        // Notify the user that the scan failed and display the provided hint.
    }
}
```

`OnAuthenticationSucceeded`當叫用`Cipher`時`Authentication` , 檢查是否已`FingerprintManager`提供給。 若是如此, `DoFinal`則會在加密上呼叫方法。 這會關閉`Cipher`, 並將其還原為其原始狀態。 如果加密發生問題, 則`DoFinal`會擲回例外狀況, 而驗證嘗試應該會被視為失敗。

`OnAuthenticationError` 和`OnAuthenticationHelp`回呼各自都會收到一個整數, 指出問題所在。 下一節將說明每個可能的協助或錯誤碼。 這兩個回呼會提供&ndash;類似的目的, 以通知應用程式指紋驗證已失敗。 它們的差異在於嚴重性。 `OnAuthenticationHelp`是使用者可復原的錯誤, 例如, 將指紋變得太快;`OnAuthenticationError`是更嚴重的錯誤, 例如指紋掃描器損毀。

請注意`OnAuthenticationError` , 在透過`CancellationSignal.Cancel()`訊息取消指紋掃描時, 將會叫用。 參數的值會是 5 (`FingerprintState.ErrorCanceled`)。 `errMsgId` 視需求而定, 的執行`AuthenticationCallbacks`可能會以不同于其他錯誤的方式來處理這種情況。 

`OnAuthenticationFailed`當指紋已成功掃描, 但不符合向裝置註冊的任何指紋時, 會叫用。 

## <a name="help-codes-and-error-message-ids"></a>說明代碼和錯誤訊息識別碼 

您可以在 FingerprintManager 類別的[Android SDK 檔](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html#FINGERPRINT_ACQUIRED_GOOD)中找到錯誤碼和說明代碼的清單和描述。 Xamarin 會使用`Android.Hardware.Fingerprints.FingerprintState`列舉來代表這些值:


- **`AcquiredGood`** &ndash; (值 0) 取得的映射良好。


- **`AcquiredImagerDirty`** &ndash; (值 3) 指紋映射由於懷疑或偵測到感應器上的灰塵而太雜訊。 例如, 在感應器上多次`AcquiredInsufficient`或實際的灰塵偵測 (卡住圖元、大量等) 之後, 就可以合理地傳回此值。 當此情況傳回時, 使用者應該採取動作來清除感應器。


- **`AcquiredInsufficient`** (值 2) 由於偵測到的情況 (也就是「乾麵板」) 或可能已變更的感應器 (請參閱`AcquiredImagerDirty`), 指紋映射太過雜訊而無法處理。 &ndash;



- **`AcquiredPartial`** &ndash; (值 1) 僅偵測到部分指紋映射。 在註冊期間, 使用者應該會收到解決此問題所需的資訊, 例如, &ldquo;在感應器上用力按下。&rdquo;



- **`AcquiredTooFast`** &ndash; (值 5) 指紋映射因快速動作而未完成。 雖然大部分適用于線性陣列感應器, 但如果在取得時移動手指, 也可能會發生這種情況。 系統應要求使用者移動手指較慢的 (線性), 或將手指保持在感應器上較長的時間。




- **`AcquiredToSlow`** &ndash; (值 4) 由於沒有動作而無法讀取指紋映射。 這最適合需要滑動動作的線性陣列感應器。



- **`ErrorCanceled`** &ndash; (值 5) 因為指紋感應器無法使用, 所以已取消操作。 例如, 當使用者切換、裝置已鎖定, 或另一個擱置中的作業阻止或停用時, 就可能發生這種情況。



- **`ErrorHwUnavailable`** &ndash; (值 1) 硬體無法使用。 請稍後再試一次。




- **`ErrorLockout`** &ndash; (值 7) 作業已取消, 因為 API 因為嘗試次數過多而遭到鎖定。




- **`ErrorNoSpace`** &ndash; (值 4) 針對註冊之類的作業傳回的錯誤狀態; 因為沒有足夠的儲存空間可完成作業, 所以無法完成操作。



- **`ErrorTimeout`** &ndash; (值 3) 目前要求的執行時間太長時傳回的錯誤狀態。 這是為了防止程式無限期地等候指紋感應器。 時間為平臺和感應器特定, 但通常約30秒。



- **`ErrorUnableToProcess`** &ndash; (值 2) 當感應器無法處理目前的映射時, 所傳回的錯誤狀態。



## <a name="related-links"></a>相關連結

- [分組](https://docs.oracle.com/javase/7/docs/api/javax/crypto/Cipher.html)
- [AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [AuthenticationCallback](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.AuthenticationCallback.html)
