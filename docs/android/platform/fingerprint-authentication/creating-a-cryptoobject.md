---
title: 建立加密物件
ms.prod: xamarin
ms.assetid: 4D159B80-FFF4-4136-A7F0-F8A5C6B86838
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 871058d1c128b37a0f2e77b43587139efb433de1
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "75487772"
---
# <a name="creating-a-cryptoobject"></a>建立加密物件

指紋身份驗證結果的完整性對於應用程式&ndash;很重要,重要的是應用程式如何知道使用者的身份。 理論上,第三方惡意軟體可以攔截和篡改指紋掃描程式返回的結果。 本節將討論一種保存指紋結果有效性的技術。 

`FingerprintManager.CryptoObject`是 Java 加密 API 周圍的`FingerprintManager`包裝器,由保護身份驗證請求的完整性。 通常,`Javax.Crypto.Cipher`對像是加密指紋掃描器結果的機制。 物件`Cipher`本身將使用應用程式使用 Android 金鑰存儲 API 創建的金鑰。

為了瞭解這些類如何協同工作,我們首先介紹以下代碼,演示如何創建`CryptoObject`,然後更詳細地解釋:

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
            cipher.Init(CipherMode.EncryptMode, key);
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

範例代碼將使用應用程式創建的金鑰為每個`Cipher``CryptoObject`創建一個新代碼。 鍵由`CryptoObjectHelper`類開頭設置`KEY_NAME`的 變數標識。 該方法`GetKey`將嘗試使用 Android 金鑰存儲 API 檢索密鑰。 如果金鑰不存在,則該方法`CreateKey`將為應用程式創建一個新密鑰。

密碼通過調用實例化`Cipher.GetInstance`,獲取_轉換_(告訴密碼如何加密和解密數據的字串值)。 呼叫`Cipher.Init`將透過提供應用程式中的密鑰來完成密碼的初始化。 

請務必認識到,在某些情況下,Android 可能會使密鑰無效: 

- 設備已註冊了新的指紋。
- 沒有隨設備註冊的指紋。
- 使用者已禁用屏幕鎖定。
- 使用者已更改螢幕鎖(螢幕鎖的類型或使用的 PIN/模式)。

發生這種情況時,`Cipher.Init`會拋[`KeyPermanentlyInvalidatedException`](https://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html)出一個 。 上述示例代碼將捕獲該異常,刪除密鑰,然後創建新的異常。

下一節將討論如何創建密鑰並將其存儲在設備上。

## <a name="creating-a-secret-key"></a>建立金鑰

類`CryptoObjectHelper`使用[`KeyGenerator`](xref:Javax.Crypto.KeyGenerator)Android 創建金鑰並將其存儲在設備上。 類`KeyGenerator`可以創建金鑰,但需要一些關於要創建的密鑰類型的元數據。 此資訊由[`KeyGenParameterSpec`](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html)類的實例提供。 

A`KeyGenerator``GetInstance`使用工廠方法實例化。 範例代碼使用[_進階加密標準_](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)(_AES)_ 作為加密演演算法. AES 將資料分解為固定大小的塊,並加密每個塊。

接下來,使用`KeyGenParameterSpec``KeyGenParameterSpec.Builder`建立 。 關於`KeyGenParameterSpec.Builder`要建立的金鑰的以下資訊:

- 索引鍵名稱。
- 金鑰必須對加密和解密都有效。
- 在範例代碼中,`BLOCK_MODE`設定為_密碼塊連結_`KeyProperties.BlockModeCbc`(), 這意味著每個塊都與前一個塊一起 XORed(在每個塊之間創建依賴項)。 
- 使用`CryptoObjectHelper`[_公開金鑰加密標準#7_](https://tools.ietf.org/html/rfc2315) _(PKCS7)_ 生成將填充模組的位元組,以確保它們的大小相同。
- `SetUserAuthenticationRequired(true)`表示在使用密鑰之前需要使用者身份驗證。

建立後`KeyGenParameterSpec`,它用於初始化`KeyGenerator`,這將產生密鑰並將其安全地存儲在設備上。 

## <a name="using-the-cryptoobjecthelper"></a>使用加密物件說明程式

現在,範例代碼已封裝了創建`CryptoWrapper``CryptoObjectHelper`類的大部分邏輯,讓我們從本指南的開頭重新訪問代碼,並`CryptoObjectHelper`使用創建 Cipher 並啟動指紋掃描程式: 

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

現在,我們已經看到了如何創建一個`CryptoObject`,讓我們繼續看看如何`FingerprintManager.AuthenticationCallbacks`使用 ,以傳輸指紋掃描器服務的結果到Android應用程式。

## <a name="related-links"></a>相關連結

- [密碼](xref:Javax.Crypto.Cipher)
- [指紋管理員.加密物件](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)
- [指紋管理員複合物.加密物件](https://developer.android.com/reference/android/support/v4/hardware/fingerprint/FingerprintManagerCompat.CryptoObject.html)
- [鑰匙產生器](xref:Javax.Crypto.KeyGenerator)
- [金鑰基因參數規格](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.html)
- [金鑰參數Spec.產生器](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.Builder.html)
- [金鑰永久失效異常](https://developer.android.com/reference/android/security/keystore/KeyPermanentlyInvalidatedException.html)
- [關鍵屬性](https://developer.android.com/reference/android/security/keystore/KeyProperties.html)
- [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)
- [RFC 2315 - PCKS#7](https://tools.ietf.org/html/rfc2315)
