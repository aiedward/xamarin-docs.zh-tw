---
title: 回應驗證回呼
ms.prod: xamarin
ms.assetid: 6533AFC9-1A1C-4897-A154-4D4ECFE27761
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/06/2017
ms.openlocfilehash: 8dc06740355bd95828e1a1bd8d9d15a2ef37e6b2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027538"
---
# <a name="responding-to-authentication-callbacks"></a>回應驗證回呼

指紋掃描程式在後台運行在其自己的線程上,當它完成時,它將通過在UI線程上調用的`FingerprintManager.AuthenticationCallback`一種方法來報告掃描結果。 Android 應用程式必須提供自己的處理程式,該處理程式擴展此抽象類,實現以下所有方法:

- **`OnAuthenticationError(int errorCode, ICharSequence errString)`**&ndash;當出現不可恢復錯誤時調用。 除了可能重試外,應用程式或用戶無法執行任何操作來糾正這種情況。
- **`OnAuthenticationFailed()`**&ndash;當檢測到指紋但設備未識別指紋時,將調用此方法。
- **`OnAuthenticationHelp(int helpMsgId, ICharSequence helpString)`**&ndash;當存在可恢復錯誤時調用,例如手指被輕掃到快速通過掃描器。
- **`OnAuthenticationSucceeded(FingerprintManagerCompati.AuthenticationResult result)`**&ndash;當指紋被識別時,將調用這一點。

如果在呼叫`CryptoObject``Authenticate`時使用了 ,`Cipher.DoFinal``OnAuthenticationSuccessful`建議呼叫 。
`DoFinal`如果密碼被篡改或初始化不當,則將引發異常,表明指紋掃描器的結果可能在應用程式外部被篡改。

> [!NOTE]
> 建議保持回調類相對輕重且無應用程式特定邏輯。 回調應在 Android 應用程式和指紋掃描器的結果之間充當「交通員警」。

## <a name="a-sample-authentication-callback-handler"></a>範例識別處理程式

以下類別是最小`FingerprintManager.AuthenticationCallback`的範例: 

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

`OnAuthenticationSucceeded`檢查是否向呼叫`Cipher``FingerprintManager``Authentication`時提供 。 如果是這樣,則在`DoFinal`密碼上調用該方法。 這將關閉`Cipher`, 將其還原到其原始狀態。 如果密碼出現問題,則`DoFinal`將引發異常,身份驗證嘗試應視為失敗。

和`OnAuthenticationError``OnAuthenticationHelp`回調每個接收一個整數,指示問題所在。 以下部分介紹每個可能的説明或錯誤代碼。 這兩個回調具有類似的用途&ndash;,用於通知應用程式指紋身份驗證失敗。 它們的不同程度在嚴重程度上。 `OnAuthenticationHelp`是用戶可恢復的錯誤,如刷指紋太快;`OnAuthenticationError`是更嚴重的錯誤,如損壞的指紋掃描器。

請注意,`OnAuthenticationError`當透過訊息取消指紋掃描時`CancellationSignal.Cancel()`, 將呼叫該訊息。 參數`errMsgId`的值將為 5`FingerprintState.ErrorCanceled` ( 。 根據要求,實現的可能`AuthenticationCallbacks`與其他錯誤不同地處理這種情況。 

`OnAuthenticationFailed`在成功掃描指紋時調用,但與設備註冊的任何指紋不匹配。 

## <a name="help-codes-and-error-message-ids"></a>說明代碼與錯誤訊息 ID 

錯誤代碼和説明代碼的清單和說明可在指紋管理器類的 Android [SDK 文檔中](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html#FINGERPRINT_ACQUIRED_GOOD)找到。 Xamarin.Android`Android.Hardware.Fingerprints.FingerprintState`用 枚舉表示這些值:

- **`AcquiredGood`**&ndash; (值 0) 獲取的圖像良好。

- **`AcquiredImagerDirty`**&ndash; (值 3) 由於感測器上懷疑或檢測到污垢,指紋圖像太嘈雜。 例如,在多次`AcquiredInsufficient`或實際檢測到感測器上的污垢(卡住的圖元、帶條等)后,可以返回此值。 使用者應採取措施在返回感測器時清潔感測器。

- **`AcquiredInsufficient`**&ndash; (值 2) 由於檢測到的情況(即乾燥的皮膚)或可能臟的感測器(`AcquiredImagerDirty`參見 ),指紋圖像太吵,無法處理。

- **`AcquiredPartial`**&ndash; (值 1) 僅檢測到部分指紋圖像。 在註冊期間,應告知使用者需要發生什麼才能解決此問題,例如,&ldquo;用力按按感測器。&rdquo;

- **`AcquiredTooFast`**&ndash; (值 5) 由於快速運動,指紋圖像不完整。 雖然主要適用於線性陣列感測器,但如果在採集過程中手指移動,也可能發生此情況。 應要求使用者移動手指較慢(線性)或讓手指在感測器上移動更長時間。

- **`AcquiredToSlow`**&ndash; (值 4) 由於缺乏運動,指紋圖像不可讀。 這最適合需要滑動運動的線性陣列感測器。

- **`ErrorCanceled`**&ndash; (值 5) 操作已取消,因為指紋感測器不可用。 例如,當使用者切換、設備鎖定或另一個掛起的操作阻止或禁用它時,可能會發生這種情況。

- **`ErrorHwUnavailable`**&ndash; (值 1) 硬體不可用。 請稍後再試一次。

- **`ErrorLockout`**&ndash; (值 7) 操作已取消,因為 API 由於嘗試次數過多而鎖定。

- **`ErrorNoSpace`**&ndash; (值 4) 為註冊等操作返回的錯誤狀態;無法完成該操作,因為沒有足夠的存儲空間來完成該操作。

- **`ErrorTimeout`**&ndash; (值 3) 當目前請求運行時間過長時返回錯誤狀態。 這是為了防止程式無限期地等待指紋感測器。 超時是特定於平台和感測器的,但通常約為 30 秒。

- **`ErrorUnableToProcess`**&ndash; (值 2) 當感測器無法處理當前圖像時返回錯誤狀態。

## <a name="related-links"></a>相關連結

- [密碼](https://docs.oracle.com/javase/7/docs/api/javax/crypto/Cipher.html)
- [驗證回撥](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [驗證回撥](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.AuthenticationCallback.html)
