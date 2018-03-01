---
title: "回應驗證回呼"
ms.topic: article
ms.prod: xamarin
ms.assetid: 6533AFC9-1A1C-4897-A154-4D4ECFE27761
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/06/2017
ms.openlocfilehash: 371ffae8e14a630cb548f4a9ee2bf0bd06f7284c
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="responding-to-authentication-callbacks"></a>回應驗證回呼

在背景中執行它自己的執行緒上的指紋掃描器，而且完成後會回報掃描的結果叫用的一種方法`FingerprintManager.AuthenticationCallback`UI 執行緒上。 Android 應用程式必須提供自己的處理常式以擴充這個抽象類別，實作所有下列方法：

* **`OnAuthenticationError(int errorCode, ICharSequence errString)`** &ndash; 無法復原的錯誤時呼叫。 沒有任何應用程式或使用者可以執行以更正狀態，但可能再試一次。
* **`OnAuthenticationFailed()`** &ndash; 已偵測到使用指紋，但無法辨識的裝置時，會叫用這個方法。
* **`OnAuthenticationHelp(int helpMsgId, ICharSequence helpString)`** &ndash; 可復原的錯誤，例如快速移轉掃描器撥動至手指時呼叫。
* **`OnAuthenticationSucceeded(FingerprintManagerCompati.AuthenticationResult result)`** &ndash; 這稱為已辨識出指紋時。

如果`CryptoObject`呼叫時，使用`Authenticate`，建議您呼叫`Cipher.DoFinal`中`OnAuthenticationSuccessful`。
`DoFinal` 如果密碼已遭到竄改或不正確初始化將會擲回例外狀況，指出，指紋掃描器的結果可能已遭竄改的應用程式之外。


> [!NOTE]
> **注意：**建議您保留的回呼類別相對較低的加權，並使用免費的應用程式特定的邏輯。 回呼可以做為 「 流量複製"Android 應用程式與結果之間從指紋掃描器。

## <a name="a-sample-authentication-callback-handler"></a>範例驗證回呼處理常式

下列類別是最小的範例`FingerprintManager.AuthenticationCallback`實作： 

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

`OnAuthenticationSucceeded` 檢查`Cipher`提供給`FingerprintManager`時`Authentication`叫用。 如果是這樣，`DoFinal`加密上呼叫方法。 這樣會關閉`Cipher`，還原成原始狀態。 如果發生問題與密碼，然後`DoFinal`將會擲回例外狀況，並且應該視為驗證嘗試失敗。

`OnAuthenticationError`和`OnAuthenticationHelp`回呼每個接收一個整數，表示問題為何。 下節說明每個可能的說明或錯誤碼。 兩個回呼的用途與類似&ndash;以通知應用程式指紋驗證失敗。 它們之間的差異是嚴重性。 `OnAuthenticationHelp` 為使用者復原的錯誤，例如撥動指紋太快。`OnAuthenticationError`會更嚴重的錯誤，例如損毀的指紋掃描器。

請注意，`OnAuthenticationError`指紋掃描透過取消時就會叫用`CancellationSignal.Cancel()`訊息。 `errMsgId`參數會有 5 個值 (`FingerprintState.ErrorCanceled`)。 根據需求，實作`AuthenticationCallbacks`可能比其他錯誤以不同的方式處理這種情況。 

`OnAuthenticationFailed` 已成功地掃描指紋，但是不符合任何指紋與裝置註冊時叫用。 

## <a name="help-codes-and-error-message-ids"></a>說明碼和錯誤訊息識別碼 

清單和說明碼和錯誤碼的描述，請參閱[Android SDK 文件](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html#FINGERPRINT_ACQUIRED_GOOD)FingerprintManager 類別。 Xamarin.Android 表示這些值與`Android.Hardware.Fingerprints.FingerprintState`列舉：


-   **`AcquiredGood`** &ndash; （值 0）取得影像是很好。


-   **`AcquiredImagerDirty`** &ndash; （值 3）指紋映像已太吵雜因為可疑或偵測到的灰塵感應器上。 比方說，是合理的多個後，傳回這個`AcquiredInsufficient`或實際的灰塵感應器 （陷入不斷像素為單位，負責等等） 上的偵測。 使用者必須採取動作時，這傳回清除感應器。


-   **`AcquiredInsufficient`** &ndash; （值 2）指紋映像已太吵雜處理因為偵測到的條件 （也就是乾面板） 或可能已變更的感應器 (請參閱`AcquiredImagerDirty`。



-   **`AcquiredPartial`** &ndash; （值 1）偵測到僅部分指紋映像。 在註冊期間，使用者都應該通知上需要若要解決這個問題，例如，發生的情況&ldquo;穩固地按感應器上。&rdquo;



-   **`AcquiredTooFast`** &ndash; （值為 5）指紋映像已完整，因為快速移動量。 雖然大部分適用於線性陣列感應器，也會發生此問題期間擷取手指移動。 要求使用者移動速度較慢手指 （線性），或在較長的感應器保持手指。




-   **`AcquiredToSlow`** &ndash; （值 4）指紋映像已移動的不足，無法讀取。 這是最適合需要撥動影片的線性陣列感應器。



-   **`ErrorCanceled`** &ndash; （值為 5）已取消作業，因為無法使用指紋感應器。 比方說，這可能會發生時切換使用者、 裝置遭到鎖定，或另一項擱置作業可以防止或停用它。



-   **`ErrorHwUnavailable`** &ndash; （值 1）無法使用的硬體。 請稍後再試。




-   **`ErrorLockout`** &ndash; （值 7）因為 API 因嘗試太多次而遭到鎖定，已取消作業。




-   **`ErrorNoSpace`** &ndash; （值 4）作業，像是註冊; 傳回的錯誤狀態無法完成作業，因為沒有足夠的儲存空間剩餘要完成此作業。



-   **`ErrorTimeout`** &ndash; （值 3）傳回目前的要求執行太長時的錯誤狀態。 這被為了避免無限期等候指紋感應器的程式。 逾時為平台和感應器而異，但通常約 30 秒。



-   **`ErrorUnableToProcess`** &ndash; （值 2）當感應器無法處理目前的映像時，傳回的錯誤狀態。



## <a name="related-links"></a>相關連結

- [Cipher](https://docs.oracle.com/javase/7/docshttps://developer.xamarin.com/api/javax/crypto/Cipher.html)
- [AuthenticationCallback](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [AuthenticationCallback](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.AuthenticationCallback.html)
