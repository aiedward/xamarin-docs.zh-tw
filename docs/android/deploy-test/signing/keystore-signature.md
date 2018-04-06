---
title: 尋找您的金鑰存放區簽章
ms.prod: xamarin
ms.assetid: 1b511fec-e6f6-453e-89c8-810aafb02b77
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 46b43e6689f751c4fac1e8668234fce7f953521e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="finding-your-keystores-signature"></a>尋找您的金鑰存放區簽章

Xamarin.Android 應用程式的 MD5 或 SHA1 簽章取決於用來簽署 APK 的 **.keystore** 檔案。 一般來說，偵錯組建將使用與發行組建不同的 **.keystore** 檔案。

## <a name="for-debug--non-custom-signed-builds"></a>對於偵錯 / 非自訂的簽署組建

Xamarin.Android 會使用同一個 **debug.keystore** 檔案來簽署所有偵錯組建。 這個檔案會在第一次安裝 Xamarin.Android 時產生。下列步驟將詳細說明尋找預設 Xamarin.Android **debug.keystore** 檔案之 MD5 或 SHA1 簽章的程序。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

尋找用來簽署應用程式的 Xamarin **debug.keystore** 檔案。 根據預設，可在下列位置找到用來簽署 Xamarin.Android 應用程式偵錯版本的金鑰儲存區：

**C:\\Users\\*USERNAME*\\AppData\\Local\\Xamarin\\Mono for Android\\debug.keystore**

您可以從 JDK 執行 `keytool.exe` 命令來取得金鑰儲存區的相關資訊。 通常可在下列位置找到此工具：

**C:\\Program Files (x86)\\Java\\jdk*VERSION*\\bin\\keytool.exe**

將包含 **keytool.exe** 的目錄新增至 `PATH` 環境變數。
開啟 [命令提示字元]，然後使用下列命令來執行 `keytool.exe`：

```cmd
keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
```

執行時，**keytool.exe** 應該會輸出下列文字。 [MD5:] 和 [SHA1:] 標籤會識別個別的簽章：

```cmd
Alias name: androiddebugkey
Creation date: Aug 19, 2014
Entry type: PrivateKeyEntry
Certificate chain length: 1
Certificate[1]:
Owner: CN=Android Debug, O=Android, C=US
Issuer: CN=Android Debug, O=Android, C=US
Serial number: 53f3b126
Valid from: Tue Aug 19 13:18:46 PDT 2014 until: Sun Nov 15 12:18:46 PST 2043
Certificate fingerprints:
         MD5:  27:78:7C:31:64:C2:79:C6:ED:E5:80:51:33:9C:03:57
         SHA1: 00:E5:8B:DA:29:49:9D:FC:1D:DA:E7:EE:EE:1A:8A:C7:85:E7:31:23
         SHA256: 21:0D:73:90:1D:D6:3D:AB:4C:80:4E:C4:A9:CB:97:FF:34:DD:B4:42:FC:
08:13:E0:49:51:65:A6:7C:7C:90:45
         Signature algorithm name: SHA1withRSA
         Version: 3
```


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

尋找用來簽署應用程式的 Xamarin **debug.keystore** 檔案。 根據預設，可在下列位置找到用來簽署 Xamarin.Android 應用程式偵錯版本的金鑰儲存區：

**~/.local/share/Xamarin/Mono for Android/debug.keystore**


您可以從 JDK 執行 **keytool** 命令來取得金鑰儲存區的相關資訊。 通常可在下列位置找到此工具：

**/System/Library/Java/JavaVirtualMachines/*VERSION*.jdk/Contents/Home/bin/keytool**

將包含 **keytool** 的目錄新增至 **PATH** 環境變數。
開啟**終端機**，然後使用下列命令來執行 **keytool**：

```bash
$ keytool -list -v -keystore ~/.local/share/Xamarin/Mono\ for\ Android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

執行時，**keytool** 應該會輸出下列文字。 [MD5:] 和 [SHA1:] 標籤會識別個別的簽章：

```bash
Alias name: androiddebugkey
Creation date: Apr 16, 2015
Entry type: PrivateKeyEntry
Certificate chain length: 1
Certificate[1]:
Owner: CN=Android Debug, O=Android, C=US
Issuer: CN=Android Debug, O=Android, C=US
Serial number: 76afa120
Valid from: Thu Apr 16 10:52:27 PDT 2015 until: Sat Apr 08 10:52:27 PDT 2045
Certificate fingerprints:
     MD5:  0A:D3:7E:80:3D:40:2A:23:89:B9:AB:9C:4B:B6:63:36
     SHA1: 89:33:8F:F2:C5:0C:91:08:4A:CF:04:A5:EC:4A:31:80:84:18:0D:D4
     SHA256: 91:AC:3E:2F:CB:EF:50:07:2B:E0:D9:8D:8B:C2:42:87:6A:85:02:86:EB:44:84:10:34:02:ED:35:CE:C6:38:47
     Signature algorithm name: SHA256withRSA
     Version: 3

Extensions:

#1: ObjectId: 2.5.29.14 Criticality=false
SubjectKeyIdentifier [
KeyIdentifier [
0000: 65 6C FE 67 8E CD CB 9A   01 CD 9F E8 25 9C A4 A3  el.g........%...
0010: 4F 4E CF 17                                        ON..
]
]
```

-----

## <a name="for-release--custom-signed-builds"></a>對於發行 / 自訂的簽署組建

使用自訂 **.keystore** 檔案簽署之發行組建的程序與上述相同，但使用發行 **.keystore** 檔案來取代 Xamarin.Android 所使用的 **debug.keystore** 檔案。 使用您自己的值來取代金鑰儲存區密碼，以及建立發行金鑰儲存區檔案時的別名名稱。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

使用 Visual Studio [散發] 精靈來簽署 Xamarin.Android 應用程式時，產生的金鑰儲存區會位於下列位置：

**C:\\Users\\*USERNAME*\\AppData\\Local\\Xamarin\\Mono for Android\\alias\\alias.keystore**

例如，如果您遵循[建立新的憑證](~/android/deploy-test/signing/index.md#newcertvs)中的步驟來建立新的簽署金鑰，產生的範例金鑰儲存區就會位於下列位置：

**C:\\Users\\*USERNAME*\\AppData\\Local\\Xamarin\\Mono for Android\\chimp\\chimp.keystore**

如需簽署 Xamarin.Android 應用程式的詳細資訊，請參閱[簽署 Android 應用程式套件](~/android/deploy-test/signing/index.md)。


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

當 Visual Studio for Mac [簽署並散發] 精靈簽署您的應用程式時，產生的金鑰儲存區會位於下列位置：

**~/Library/Developer/Xamarin/Keystore/*alias*/*alias*.keystore**

例如，如果您遵循[建立新的憑證](~/android/deploy-test/signing/index.md#newcertxs)中的步驟來建立新的簽署金鑰，產生的範例金鑰儲存區就會位於下列位置：

**~/Library/Developer/Xamarin/Keystore/chimp/chimp.keystore**

如需簽署 Xamarin.Android 應用程式的詳細資訊，請參閱[簽署 Android 應用程式套件](~/android/deploy-test/signing/index.md)。


-----
