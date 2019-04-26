---
title: 回應驗證回呼
ms.prod: xamarin
ms.assetid: 6533AFC9-1A1C-4897-A154-4D4ECFE27761
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/06/2017
ms.openlocfilehash: c720a30a59eea8f1ed74033da8d1c045a1fb9109
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61023479"
---
# <a name="responding-to-authentication-callbacks"></a>回應驗證回呼

在背景中執行自己的執行緒上的指紋掃描器和完成時則會叫用的一種方法回報的掃描結果`FingerprintManager.AuthenticationCallback`UI 執行緒上。 Android 應用程式必須提供自己的處理常式以擴充此抽象類別，實作下列所有方法：

* **`OnAuthenticationError(int errorCode, ICharSequence errString)`** &ndash; 無法復原的錯誤時呼叫。 沒有任何應用程式或使用者可以如何更正此情況，但可能再試一次。
* **`OnAuthenticationFailed()`** &ndash; 已偵測到使用指紋，但無法辨識的裝置時，會叫用這個方法。
* **`OnAuthenticationHelp(int helpMsgId, ICharSequence helpString)`** &ndash; 可復原的錯誤，例如快速移轉掃描器所撥動以手指時呼叫。
* **`OnAuthenticationSucceeded(FingerprintManagerCompati.AuthenticationResult result)`** &ndash; 這稱為時被辨識指紋。

如果`CryptoObject`呼叫時，曾經`Authenticate`，建議您呼叫`Cipher.DoFinal`在`OnAuthenticationSuccessful`。
`DoFinal` 如果密碼已遭他人修改，或未正確初始化將會擲回例外狀況，這表示，指紋掃描器的結果可能已遭竄改並在應用程式之外。


> [!NOTE]
> 建議您保留回呼類別相當輕量，以及免費的應用程式特定邏輯。 回呼應該做為 「 交通警察"Android 應用程式和結果從指紋掃描器。

## <a name="a-sample-authentication-callback-handler"></a>驗證回撥處理常式範例

下列類別是範例的最小`FingerprintManager.AuthenticationCallback`實作： 

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

`OnAuthenticationSucceeded` 檢查是否`Cipher`提供給`FingerprintManager`當`Authentication`叫用。 如果是這樣，`DoFinal`加密上呼叫方法。 這會關閉`Cipher`，還原為其原始狀態。 如果發生問題與加密，然後`DoFinal`將會擲回例外狀況，並視為驗證嘗試失敗。

`OnAuthenticationError`和`OnAuthenticationHelp`回呼，每個接收整數，指出問題為何。 下節說明每個可能的說明或錯誤碼。 兩個回呼的用途類似&ndash;以通知應用程式指紋驗證失敗。 它們之間的差異是嚴重性。 `OnAuthenticationHelp` 是使用者復原的錯誤，例如撥動太快; 指紋`OnAuthenticationError`是更嚴重的錯誤，例如損毀的指紋掃描器。

請注意，`OnAuthenticationError`指紋掃描透過取消時將會叫用`CancellationSignal.Cancel()`訊息。 `errMsgId`參數會有值為 5 (`FingerprintState.ErrorCanceled`)。 根據需求，實作`AuthenticationCallbacks`可能不同於其他錯誤處理這種情況。 

`OnAuthenticationFailed` 指紋成功掃描，但不是符合任何已註冊到裝置的指紋時，會叫用。 

## <a name="help-codes-and-error-message-ids"></a>說明程式碼和錯誤訊息識別碼 

清單和描述的錯誤碼和說明程式碼中可能可以找到[Android SDK 文件](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.html#FINGERPRINT_ACQUIRED_GOOD)FingerprintManager 類別。 Xamarin.Android 代表這些值搭配`Android.Hardware.Fingerprints.FingerprintState`列舉：


-   **`AcquiredGood`** &ndash; （值 0）取得映像的良好。


-   **`AcquiredImagerDirty`** &ndash; （值為 3）指紋映像的太吵雜，因為懷疑或偵測到的灰塵感應器上。 比方說，是合理的多個後，傳回這個`AcquiredInsufficient`偵測是否有實際的感應器 （停滯的像素為單位，通關等等） 上的灰塵。 使用者應該在採取動作來清除時，這傳回的感應器。


-   **`AcquiredInsufficient`** &ndash; （值 2）指紋映像的太吵處理由於偵測到的條件 （也就是 dry 面板） 或可能已變更的感應器 (請參閱`AcquiredImagerDirty`。



-   **`AcquiredPartial`** &ndash; （值 1）偵測到只有部分的指紋的映像。 在註冊期間，使用者應被告知上需要發生的情況來解決這個問題，例如&ldquo;用力按感應器上。&rdquo;



-   **`AcquiredTooFast`** &ndash; （值為 5）指紋映像的不完整，因為快速的影片。 雖然大部分適用於線性陣列感應器，這也有可能在擷取期間移動手指了。 要求使用者移動手指速度較慢 （線性） 或保留較長的感應器上的手指。




-   **`AcquiredToSlow`** &ndash; （值 4）指紋映像已移動的不足，無法讀取。 這是最適合需要撥動動作的線性陣列感應器。



-   **`ErrorCanceled`** &ndash; （值為 5）作業已取消，因為無法使用指紋感應器。 比方說，這可能會發生在使用者切換、 在裝置鎖定時，或另一個暫止的作業無法安裝或停用它時。



-   **`ErrorHwUnavailable`** &ndash; （值 1）無法使用的硬體。 請稍後再試。




-   **`ErrorLockout`** &ndash; （值為 7）因為 API 因嘗試太多次而遭到鎖定，所以取消作業。




-   **`ErrorNoSpace`** &ndash; （值 4）註冊; 等作業傳回的錯誤狀態無法完成作業，因為沒有完成作業所需的剩餘足夠的儲存體。



-   **`ErrorTimeout`** &ndash; （值為 3）當目前的要求執行時間太久時，傳回的錯誤狀態。 這被為了防止程式無限期等候指紋感應器。 逾時是平台和感應器而異，但通常是 30 秒。



-   **`ErrorUnableToProcess`** &ndash; （值 2）當感應器無法處理目前的映像時，傳回的錯誤狀態。



## <a name="related-links"></a>相關連結

- [Cipher](https://docs.oracle.com/javase/7/docs/api/javax/crypto/Cipher.html)
- [AuthenticationCallback](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.AuthenticationCallback.html)
- [AuthenticationCallback](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.AuthenticationCallback.html)
