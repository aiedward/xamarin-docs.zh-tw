---
title: 手動簽署 APK
ms.prod: xamarin
ms.assetid: 08549E1C-7F04-4D20-9E7A-794B9D09FD12
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 2d84d149b2eb4194de35fabc69cf44af99c04d25
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76724122"
---
# <a name="manually-signing-the-apk"></a>手動簽署 APK

建置要發行的應用程式之後，必須先簽署 APK 後再進行散發，這樣它才能在 Android 裝置上執行。 此程序通常會使用 IDE 來處理，不過，在某些情況下，需要在命令列手動簽署 APK。 以下是簽署 APK 的相關步驟：

1. **建立私密金鑰** &ndash; 此步驟只需執行一次。 需要私密金鑰，才能以數位方式簽署 APK。
    備妥私密金鑰之後，即可針對未來的發行組建略過此步驟。

2. **Zipalign APK** &ndash; *Zipalign* 是在應用程式上執行的最佳化程序。 它讓 Android 能夠在執行階段，更有效率地與 APK 互動。 Xamarin.Android 會在執行階段進行檢查，而且如果尚未針對 APK 進行 zipalign，則不允許應用程式執行。

3. **簽署 APK** &ndash; 這個步驟包含使用 Android SDK 的 **apksigner** 公用程式，並使用上一個步驟中所建立的私密金鑰來簽署 APK。 使用版本早於 v24.0.3 的 Android SDK 建置工具開發的應用程式，將會使用 JDK 的 **jarsigner** 應用程式。 以下將更詳細討論這兩種工具。

步驟的順序很重要，而且取決於用來簽署 APK 的工具。 使用 **apksigner** 時，務必先針對應用程式進行 **zipalign**，然後使用 **apksigner** 來簽署它。  如果需要使用 **jarsigner** 簽署 APK，則務必先簽署 APK，然後執行 **zipalign**。

## <a name="prerequisites"></a>必要條件

本指南將著重於從 Android SDK 建置工具 (v24.0.3 或更高版本) 使用 **apksigner**。 它假設 APK 已經建置。

使用舊版 Android SDK 建置工具所建置的應用程式必須使用 **jarsigner**，如以下的[使用 jarsigner 簽署 APK](#Sign_the_APK_with_jarsigner) 所述。

## <a name="create-a-private-keystore"></a>建立私密金鑰儲存區

「金鑰儲存區」  是一個安全性憑證的資料庫，此資料庫是使用 Java SDK 的 [keytool](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) \(英文\) 程式所建立。 金鑰儲存區對於發行 Xamarin.Android 應用程式而言是不可或缺的，因為 Android 將不會執行未經過數位簽署的應用程式。

在開發期間，Xamarin.Android 會使用偵錯金鑰儲存區來簽署應用程式，讓應用程式能夠直接部署至模擬器，或部署至設定來使用可偵錯應用程式的裝置。
不過，無法將此金鑰儲存區辨識為用於散發應用程式目的的有效金鑰儲存區。

基於此因素，必須建立私密金鑰儲存區，並用來簽署應用程式。 這是個只應執行一次的步驟，因為相同的金鑰將用於發行更新，然後用來簽署其他應用程式。

請務必保護這個金鑰儲存區。 如果遺失，則它就無法使用 Google Play 來將更新發行至應用程式。
遺失金鑰儲存區所造成問題的唯一解決方式是建立新的金鑰儲存區、使用新的金鑰重新簽署 APK，然後提交新的應用程式。 接著，必須從 Google Play 移除舊的應用程式。 同樣地，如果這個新的金鑰儲存區遭到洩漏或公開散發，則可能會散發應用程式的非官方或惡意版本。

### <a name="create-a-new-keystore"></a>建立新的金鑰儲存區

建立新的金鑰儲存區需要 Java SDK 的命令列工具 [keytool](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) \(英文\)。 下列程式碼片段是如何使用 **keytool** (使用金鑰儲存區的檔案名稱來取代 `<my-filename>`，並使用金鑰儲存區內的金鑰名稱來取代 `<key-name>`) 的範例：

```shell
$ keytool -genkeypair -v -keystore <filename>.keystore -alias <key-name> -keyalg RSA \
          -keysize 2048 -validity 10000
```

**keytool** 將要求的第一件事是金鑰儲存區的密碼。 接著，它將要求一些有助於建立金鑰的資訊。 下列程式碼片段是建立名為 `publishingdoc` 之新金鑰的範例，此金鑰將儲存於 `xample.keystore` 檔案中：

```shell
$ keytool -genkeypair -v -keystore xample.keystore -alias publishingdoc -keyalg RSA -keysize 2048 -validity 10000
Enter keystore password:
Re-enter new password:
What is your first and last name?
  [Unknown]:  Ham Chimpanze
What is the name of your organizational unit?
  [Unknown]:  NASA
What is the name of your organization?
  [Unknown]:  NASA
What is the name of your City or Locality?
  [Unknown]:  Cape Canaveral
What is the name of your State or Province?
  [Unknown]:  Florida
What is the two-letter country code for this unit?
  [Unknown]:  US
Is CN=Ham Chimpanze, OU=NASA, O=NASA, L=Cape Canaveral, ST=Florida, C=US correct?
  [no]:  yes

Generating 2,048 bit RSA key pair and self-signed certificate (SHA1withRSA) with a validity of 10,000 days
        for: CN=Ham Chimpanze, OU=NASA, O=NASA, L=Cape Canaveral, ST=Florida, C=US
Enter key password for <publishingdoc>
        (RETURN if same as keystore password):
Re-enter new password:
[Storing xample.keystore]
```

若要列出儲存於金鑰儲存區中的金鑰，請搭配 &ndash; `list` 選項使用 **keytool**：

```shell
$ keytool -list -keystore xample.keystore
```

## <a name="zipalign-the-apk"></a>針對 APK 進行 Zipalign

使用 **apksigner** 簽署 APK 之前，務必先使用 Android SDK 的 **zipalign** 工具來將檔案最佳化。 **zipalign** 將沿著 4 位元組界限，在APK 中重建資源。 這種對齊方式可讓 Android 快速從 APK 載入資源，進而提高應用程效能，並且可能會減少記憶體使用量。 Xamarin.Android 將進行執行階段檢查，以判斷是否已針對 APK 進行 zipalign。 如果尚未針對 APK 進行 zipalign，將不會執行應用程式。

下列命令將使用已簽署的 APK，並產生名為 **helloworld.apk** 之已簽署且已進行 zipalign 的 APK，而此 APK 已準備好可供散發。

```shell
$ zipalign -f -v 4 mono.samples.helloworld-unsigned.apk helloworld.apk
```

## <a name="sign-the-apk"></a>簽署 APK

針對 APK 進行 zipalign 之後，必須使用金鑰儲存區進行簽署。 作法是使用 **apksigner** 工具，可在 SDK 建置工具版本的 **build-tools** 目錄中找到。  例如，如果已安裝 Android SDK 建置工具 v25.0.3，則可以在該目錄中找到 **apksigner**：

```bash
$ ls $ANDROID_HOME/build-tools/25.0.3/apksigner
/Users/tom/android-sdk-macosx/build-tools/25.0.3/apksigner*
```

下列程式碼片段假設可透過 `PATH` 環境變數來存取 **apksigner**。 它將使用金鑰別名 `publishingdoc` 來簽署 APK，此別名包含於 **xample.keystore** 檔案中：

```shell
$ apksigner sign --ks xample.keystore --ks-key-alias publishingdoc mono.samples.helloworld.apk
```

執行此命令時，**apksigner** 將視需要向金鑰儲存區要求密碼。

如需使用 **apksigner** 的詳細資訊，請參閱 [Google 的文件](https://developer.android.com/studio/command-line/apksigner.html) \(英文\)。

> [!NOTE]
> 根據 [(Google 問題 62696222)](https://issuetracker.google.com/issues/62696222)，Android SDK 中「找不到」**apksigner**。 對此問題的因應措施是安裝 Android SDK 建置工具 v25.0.3，並使用該版本的 **apksigner**。  

<a name="Sign_the_APK_with_jarsigner" />

### <a name="sign-the-apk-with-jarsigner"></a>使用 jarsigner 簽署 APK

> [!WARNING]
> 唯有在需要使用 **jarsigner** 公用程式簽署 APK 時，本節才適用。 建議開發人員使用 **apksigner** 簽署 APK。

此技術牽涉到使用 Java SDK 的 **[jarsigner](https://docs.oracle.com/javase/8/docs/technotes/tools/windows/jarsigner.html)** \(英文\) 命令來簽署 APK 檔案。  **Jarsigner** 工具由 Java SDK 所提供。

下列範例示範如何使用 **jarsigner** 和金鑰 `publishingdoc` 來簽署 APK，此金鑰包含在名為 **xample.keystore** 的金鑰儲存區檔案中：

```shell
$ jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore xample.keystore mono.samples.helloworld.apk publishingdoc
```

> [!NOTE]
> 使用 **jarsigner** 時，務必 _「先」_ 簽署 APK，然後再使用 **zipalign**。  

## <a name="related-links"></a>相關連結

- [Application Signing](https://source.android.com/security/apksigning/) (應用程式簽署)
- [jarsigner](https://docs.oracle.com/javase/8/docs/technotes/tools/windows/jarsigner.html)
- [keytool](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html)
- [zipalign](https://developer.android.com/studio/command-line/zipalign.html)
- [Build Tools 26.0.0 - where did apksigner go?](https://issuetracker.google.com/issues/62696222) (建置工具 26.0.0 - apksigner 移到哪裡了？)
