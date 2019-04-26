---
title: 建立 CryptoObject
ms.prod: xamarin
ms.assetid: 4D159B80-FFF4-4136-A7F0-F8A5C6B86838
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 1305a8a1f39d34b5e91e478a769750911afb2b3e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60953184"
---
# <a name="creating-a-cryptoobject"></a>建立 CryptoObject

指紋驗證結果的完整性很重要的應用程式&ndash;是應用程式如何知道使用者的身分識別。 它是理論上，協力廠商惡意程式碼來攔截和竄改指紋掃描器所傳回的結果。 本節將討論的技巧，可以保留指紋辨識結果的有效性。 

`FingerprintManager.CryptoObject`是 Java 密碼編譯 Api 的包裝，而且會由`FingerprintManager`來保護驗證要求的完整性。 一般而言，`Javax.Crypto.Cipher`物件是加密的指紋掃描器結果的機制。 `Cipher`物件本身會使用由使用 Android 金鑰儲存區 Api 的應用程式金鑰。

若要了解這些類別全部一起運作的方式，我們來看看下列程式碼示範如何建立`CryptoObject`，然後再詳細說明：

```csharp
public class CryptoObjectHelper
{
    // This can be key name you want. Should be unique for the app.
    static readonly string KEY_NAME = "com.xamarin.android.sample.fingerprint_authentication_key";

    // We always use this keystore on Android.
    static readonly string KEYSTORE_NAME = "AndroidKeyStore";

    // Should be no need to change these values.
    static readonly string KEY_ALGORITHM = KeyProperties.KeyAlgorithmAes;
    static readonly string BLOCK_MODE = KeyProperties.BlockModeCbc;
    static readonly string ENCRYPTION_PADDING = KeyProperties.EncryptionPaddingPkcs7;
    static readonly string TRANSFORMATION = KEY_ALGORITHM + "/" +
                                            BLOCK_MODE + "/" +
                                            ENCRYPTION_PADDING;
    readonly KeyStore _keystore;

    public CryptoObjectHelper()
    {
        _keystore = KeyStore.GetInstance(KEYSTORE_NAME);
        _keystore.Load(null);
    }

    public FingerprintManagerCompat.CryptoObject BuildCryptoObject()
    {
        Cipher cipher = CreateCipher();
        return new FingerprintManagerCompat.CryptoObject(cipher);
    }

    Cipher CreateCipher(bool retry = true)
    {
        IKey key = GetKey();
        Cipher cipher = Cipher.GetInstance(TRANSFORMATION);
        try
        {
            cipher.Init(CipherMode.EncryptMode | CipherMode.DecryptMode, key);
        } catch(KeyPermanentlyInvalidatedException e)
        {
            _keystore.DeleteEntry(KEY_NAME);
            if(retry)
            {
                CreateCipher(false);
            } else
            {
                throw new Exception("Could not create the cipher for fingerprint authentication.", e);
            }
        }
        return cipher;
    }

    IKey GetKey()
    {
        IKey secretKey;
        if(!_keystore.IsKeyEntry(KEY_NAME))
        {
            CreateKey();
        }

        secretKey = _keystore.GetKey(KEY_NAME, null);
        return secretKey;
    }

    void CreateKey()
    {
        KeyGenerator keyGen = KeyGenerator.GetInstance(KeyProperties.KeyAlgorithmAes, KEYSTORE_NAME);
        KeyGenParameterSpec keyGenSpec =
            new KeyGenParameterSpec.Builder(KEY_NAME, KeyStorePurpose.Encrypt | KeyStorePurpose.Decrypt)
                .SetBlockModes(BLOCK_MODE)
                .SetEncryptionPaddings(ENCRYPTION_PADDING)
                .SetUserAuthenticationRequired(true)
                .Build();
        keyGen.Init(keyGenSpec);
        keyGen.GenerateKey();
    }
}
```

範例程式碼會建立新`Cipher`針對每個`CryptoObject`，使用應用程式所建立的索引鍵。 索引鍵由`KEY_NAME`中的開頭設定的變數`CryptoObjectHelper`類別。 此方法`GetKey`會試著擷取使用 Android 金鑰儲存區 Api 金鑰。 如果索引鍵不存在，則此方法`CreateKey`會建立新的金鑰，應用程式。

密碼藉由呼叫具現化`Cipher.GetInstance`、 正在接受_轉換_（告訴加密如何加密和解密資料的字串值）。 若要呼叫`Cipher.Init`將會完成藉由提供應用程式的金鑰加密的初始化。 

請務必了解有某些情況下，Android 可能使失效的索引鍵： 

* 與裝置已註冊新的指紋。
* 沒有註冊與裝置的指紋。
* 使用者已停用螢幕鎖定。
* 使用者已變更的螢幕鎖定 （screenlock 或所使用的 PIN/模式的類型）。

當發生這種情況`Cipher.Init`將會擲回[ `KeyPermanentlyInvalidatedException` ](https://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html)。 上述的範例程式碼會攔截該例外狀況、 刪除機碼，和接著建立一個新。

下一節將討論如何建立金鑰，並將它儲存在裝置上。

## <a name="creating-a-secret-key"></a>建立祕密金鑰

`CryptoObjectHelper`類別會使用 Android [ `KeyGenerator` ](https://developer.xamarin.com/api/type/Javax.Crypto.KeyGenerator/)建立金鑰，並將其儲存在裝置上。 `KeyGenerator`類別可以建立金鑰，但需要一些中繼資料建立的索引鍵的類型有關。 此資訊提供的執行個體所[ `KeyGenParameterSpec` ](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html)類別。 

A`KeyGenerator`具現化使用`GetInstance`factory 方法。 範例程式碼會使用[_進階加密標準_](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) (_AES_) 做為加密演算法。 AES 將資料分成區塊的固定大小，並加密每個這些區塊。

下一步`KeyGenParameterSpec`會使用建立`KeyGenParameterSpec.Builder`。 `KeyGenParameterSpec.Builder`包裝所要建立的索引鍵的下列資訊：

* 金鑰的名稱。
* 索引鍵必須是有效的加密和解密。
* 在範例程式碼`BLOCK_MODE`設定為_Cipher Block Chaining_ (`KeyProperties.BlockModeCbc`)，這表示每個區塊會進行 xor 處理，與前一個區塊 （建立每個區塊之間的相依性）。 
* `CryptoObjectHelper`會使用[_公用金鑰密碼編譯標準 #7_ ](https://tools.ietf.org/html/rfc2315) (_PKCS7_) 來產生出的區塊，以確保其為所有相同的大小會填補位元組數.
* `SetUserAuthenticationRequired(true)` 表示使用者驗證的必要索引鍵才能使用。

一次`KeyGenParameterSpec`會建立，它用來初始化`KeyGenerator`，這會產生金鑰，並安全地將它儲存在裝置上。 

## <a name="using-the-cryptoobjecthelper"></a>使用 CryptoObjectHelper

現在，範例程式碼已封裝建立的邏輯的大部分`CryptoWrapper`成`CryptoObjectHelper`類別，讓我們再次瀏覽本指南開頭的程式碼，並且使用`CryptoObjectHelper`建立加密和啟動指紋掃描器： 

```csharp
protected void FingerPrintAuthenticationExample()
{
    const int flags = 0; /* always zero (0) */
    
    CryptoObjectHelper cryptoHelper = new CryptoObjectHelper();
    cancellationSignal = new Android.Support.V4.OS.CancellationSignal();
    
    // Using the Support Library classes for maximum reach
    FingerprintManagerCompat fingerPrintManager = FingerprintManagerCompat.From(this);
    
    // AuthCallbacks is a C# class defined elsewhere in code.
    FingerprintManagerCompat.AuthenticationCallback authenticationCallback = new MyAuthCallbackSample(this);

    // Here is where the CryptoObjectHelper builds the CryptoObject. 
    fingerprintManager.Authenticate(cryptohelper.BuildCryptoObject(), flags, cancellationSignal, authenticationCallback, null);
}
```

既然我們已了解如何建立`CryptoObject`，可讓您繼續看到如何`FingerprintManager.AuthenticationCallbacks`用來傳送至 Android 應用程式的指紋掃描器服務的結果。



## <a name="related-links"></a>相關連結

- [Cipher](https://developer.xamarin.com/api/type/Javax.Crypto.Cipher/)
- [FingerprintManager.CryptoObject](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat.CryptoObject](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [KeyGenerator](https://developer.xamarin.com/api/type/Javax.Crypto.KeyGenerator/)
- [KeyGenParameterSpec](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html)
- [KeyGenParameterSpec.Builder](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.Builder.html)
- [KeyPermanentlyInvalidatedException](https://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html)
- [KeyProperties](https://developer.android.com/reference/android/security/keystore/KeyProperties.html)
- [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)
- [RFC 2315 - PCKS #7](https://tools.ietf.org/html/rfc2315)
