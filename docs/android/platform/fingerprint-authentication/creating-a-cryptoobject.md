---
title: 建立 CryptoObject
ms.prod: xamarin
ms.assetid: 4D159B80-FFF4-4136-A7F0-F8A5C6B86838
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 7328792e0d921beb09389d9a0400ea97766b2246
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70756438"
---
# <a name="creating-a-cryptoobject"></a>建立 CryptoObject

指紋驗證結果的完整性對應用&ndash;程式而言非常重要，這是應用程式知道使用者身分識別的方式。 理論上，協力廠商惡意程式碼可能會攔截並篡改指紋掃描器所傳回的結果。 本節將討論用來保留指紋結果有效性的一項技巧。 

是 JAVA 密碼編譯 api 的包裝函式，並`FingerprintManager`由用來保護驗證要求的完整性。 `FingerprintManager.CryptoObject` 一般而言， `Javax.Crypto.Cipher`物件是加密指紋掃描器結果的機制。 `Cipher`物件本身會使用應用程式使用 Android 金鑰儲存區 api 所建立的金鑰。

若要瞭解這些類別如何共同合作，讓我們先查看下列示範如何建立的`CryptoObject`程式碼，然後再詳細說明：

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

範例程式碼會使用應用程式`Cipher`所建立`CryptoObject`的索引鍵，為每個建立新的。 此索引鍵是由`KEY_NAME` `CryptoObjectHelper`類別開頭所設定的變數所識別。 方法`GetKey`會使用 Android 金鑰儲存區 api 來嘗試並取得金鑰。 如果機碼不存在，則方法`CreateKey`會為應用程式建立新的金鑰。

加密會使用的呼叫來`Cipher.GetInstance`具現化，並採用_轉換_（指示加密如何加密和解密資料的字串值）。 對的呼叫`Cipher.Init`會藉由提供應用程式的索引鍵，完成加密的初始化。 

值得注意的是，在某些情況下，Android 可能會使金鑰失效： 

- 已向裝置註冊新的指紋。
- 未向裝置註冊任何指紋。
- 使用者已停用螢幕鎖定。
- 使用者已變更螢幕鎖定（screenlock 的類型或使用的 PIN/模式）。

發生這種情況`Cipher.Init`時，將[`KeyPermanentlyInvalidatedException`](https://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html)會擲回。 上述範例程式碼將會攔截該例外狀況、刪除金鑰，然後建立一個新的索引鍵。

下一節將討論如何建立金鑰，並將它儲存在裝置上。

## <a name="creating-a-secret-key"></a>建立秘密金鑰

類別會使用 Android [`KeyGenerator`](xref:Javax.Crypto.KeyGenerator)來建立金鑰，並將它儲存在裝置上。 `CryptoObjectHelper` `KeyGenerator`類別可以建立金鑰，但需要一些關於要建立的金鑰類型的中繼資料。 這項[`KeyGenParameterSpec`](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html)資訊是由類別的實例所提供。 

`KeyGenerator` 會`GetInstance`使用 factory 方法具現化。 範例程式碼會使用[_進階加密標準_](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)（_AES_）做為加密演算法。 AES 會將資料分成固定大小的區塊，並加密每個區塊。

接下來， `KeyGenParameterSpec`會`KeyGenParameterSpec.Builder`使用建立。 會`KeyGenParameterSpec.Builder`包裝下列有關要建立之金鑰的資訊：

- 金鑰的名稱。
- 金鑰必須是有效的加密和解密。
- 在範例`BLOCK_MODE`程式碼中，會設定為_加密區塊連結_（`KeyProperties.BlockModeCbc`），這表示每個區塊會與前一個區塊進行 xor （在每個區塊之間建立相依性）。 
- 會使用[_公開金鑰加密標準 #7_](https://tools.ietf.org/html/rfc2315) （PKCS7）來產生可填補區塊的位元組，以確保它們的大小都相同。 `CryptoObjectHelper`
- `SetUserAuthenticationRequired(true)`表示必須先進行使用者驗證，才能使用金鑰。

建立之後，它會用來`KeyGenerator`初始化，它會產生金鑰並將它安全地儲存在裝置上。 `KeyGenParameterSpec` 

## <a name="using-the-cryptoobjecthelper"></a>使用 CryptoObjectHelper

現在，範例程式碼已封裝許多用`CryptoWrapper` `CryptoObjectHelper`來建立的邏輯，接下來讓我們重新流覽`CryptoObjectHelper`本指南開頭的程式碼，並使用來建立加密並啟動指紋掃描器： 

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

既然我們已瞭解如何建立`CryptoObject`，讓我們繼續瞭解`FingerprintManager.AuthenticationCallbacks`如何使用來將指紋掃描器服務的結果傳送至 Android 應用程式。

## <a name="related-links"></a>相關連結

- [分組](xref:Javax.Crypto.Cipher)
- [FingerprintManager.CryptoObject](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [FingerprintManagerCompat.CryptoObject](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [KeyGenerator](xref:Javax.Crypto.KeyGenerator)
- [KeyGenParameterSpec](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html)
- [KeyGenParameterSpec.Builder](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.Builder.html)
- [KeyPermanentlyInvalidatedException](https://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html)
- [KeyProperties](https://developer.android.com/reference/android/security/keystore/KeyProperties.html)
- [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)
- [RFC 2315-PCKS #7](https://tools.ietf.org/html/rfc2315)
