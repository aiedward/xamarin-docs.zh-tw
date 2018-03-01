---
title: "建立 CryptoObject"
ms.topic: article
ms.prod: xamarin
ms.assetid: 4D159B80-FFF4-4136-A7F0-F8A5C6B86838
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 6d383bc6077dc0286fa5ee4ae7e88df2d62e4664
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="creating-a-cryptoobject"></a>建立 CryptoObject

指紋驗證結果的完整性很重要的應用程式&ndash;是應用程式如何知道使用者的身分識別。 它是理論上，協力廠商惡意程式碼來攔截和竄改指紋掃描器所傳回的結果。 本節將討論其中一種技術來保留指紋結果的有效性。 

`FingerprintManager.CryptoObject`是 Java 密碼編譯 Api 的包裝函式，並且由`FingerprintManager`來保護驗證要求的完整性。 一般而言，`Javax.Crypto.Cipher`物件是用於加密的指紋掃描器結果的機制。 `Cipher`物件本身會使用可使用 Android 的金鑰存放區 Api 的應用程式所建立的索引鍵。

若要瞭解這些類別全都如何一起運作，我們先來看看下列的程式碼示範如何建立`CryptoObject`，，更詳細地說明：

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

範例程式碼會建立新`Cipher`每個`CryptoObject`，使用應用程式所建立的索引鍵。 索引鍵由`KEY_NAME`變數中的開頭設定`CryptoObjectHelper`類別。 此方法`GetKey`將再試一次，並使用 Android 的金鑰存放區 Api 擷取金鑰。 如果索引鍵不存在，然後方法`CreateKey`將會建立新的金鑰，應用程式。

加密透過呼叫具現化`Cipher.GetInstance`，並且_轉換_（告知如何加密和解密資料加密的字串值）。 若要呼叫`Cipher.Init`將會完成藉由提供從應用程式的金鑰加密的初始化。 

請務必了解有某些情況下，Android 可能會使索引鍵： 

* 裝置已註冊新的指紋。
* 沒有任何使用裝置註冊的指紋。
* 使用者已停用螢幕鎖定。
* 使用者已變更螢幕鎖定 （screenlock 或釘選/模式使用的類型）。

當發生這種情況時，`Cipher.Init`將會擲回[ `KeyPermanentlyInvalidatedException` ](http://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html)。 上述範例程式碼會攔截該例外狀況、 刪除機碼，然後建立一個新。

下一節將討論如何建立機碼，並將它儲存在裝置上。

## <a name="creating-a-secret-key"></a>建立秘密金鑰

`CryptoObjectHelper`類別會使用 Android [ `KeyGenerator` ](https://developer.xamarin.com/api/type/Javax.Crypto.KeyGenerator/)建立金鑰，並將其儲存在裝置上。 `KeyGenerator`類別可以建立金鑰，但需要建立的索引鍵的類型的某些中繼資料。 這項資訊由執行個體[ `KeyGenParameterSpec` ](http://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html)類別。 

A`KeyGenerator`具現化使用`GetInstance`factory 方法。 範例程式碼使用[_進階加密標準_](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) (_AES_) 做為加密演算法。 AES 將資料分成區塊的固定的大小和加密每個這些區塊。

下一步`KeyGenParameterSpec`使用建立`KeyGenParameterSpec.Builder`。 `KeyGenParameterSpec.Builder`包裝要建立的索引鍵的下列資訊：

* 金鑰的名稱。
* 索引鍵必須是有效的加密和解密。
* 範例程式碼`BLOCK_MODE`設_Cipher 區塊鏈結_(`KeyProperties.BlockModeCbc`)，這表示每個區塊都會進行 xor 處理，與先前的區塊 （建立每個區塊之間的相依性）。 
* `CryptoObjectHelper`使用[_公用金鑰密碼編譯標準 #7_ ](https://tools.ietf.org/html/rfc2315) (_PKCS7_) 來產生將空出的區塊，以確保其為相同大小的位元組.
* `SetUserAuthenticationRequired(true)` 表示可使用金鑰之前，不需要使用者驗證。

一次`KeyGenParameterSpec`已建立，它用來初始化`KeyGenerator`，以產生金鑰並安全地將它儲存在裝置上。 

## <a name="using-the-cryptoobjecthelper"></a>使用 CryptoObjectHelper

現在，範例程式碼具有封裝建立的邏輯的大部分`CryptoWrapper`到`CryptoObjectHelper`類別，讓我們重新瀏覽本指南的開頭的程式碼，並且使用`CryptoObjectHelper`以建立加密並啟動指紋掃描器： 

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

既然我們已經看到如何建立`CryptoObject`，可讓移動，以查看如何`FingerprintManager.AuthenticationCallbacks`用於指紋掃描器服務的結果傳送至 Android 應用程式。



## <a name="related-links"></a>相關連結

- [Cipher](https://developer.xamarin.com/api/type/Javax.Crypto.Cipher/)
- [FingerprintManager.CryptoObject](http://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat.CryptoObject](http://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [KeyGenerator](https://developer.xamarin.com/api/type/Javax.Crypto.KeyGenerator/)
- [KeyGenParameterSpec](http://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html)
- [KeyGenParameterSpec.Builder](http://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.Builder.html)
- [KeyPermanentlyInvalidatedException](http://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html)
- [KeyProperties](http://developer.android.com/reference/android/security/keystore/KeyProperties.html)
- [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)
- [RFC 2315-PCKS #7](https://tools.ietf.org/html/rfc2315)
