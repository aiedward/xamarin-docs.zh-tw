---
title: 取得 Google Maps API 金鑰
description: 如何取得 Google Maps API 金鑰，以將地圖功能新增至您的應用程式。
ms.prod: xamarin
ms.assetid: D5969C57-3444-465E-D6FF-249AEE62E127
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/25/2018
ms.openlocfilehash: c19a2c7c8b0a02f946bf4984945a3b24cca26d1c
ms.sourcegitcommit: 1550019cd1e858d4d13a4ae6dfb4a5947702f24b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92897529"
---
# <a name="obtaining-a-google-maps-api-key"></a>取得 Google Maps API 金鑰

若要在 Android 中使用 Google Maps 功能，您需要向 Google 註冊 Maps API 金鑰。 在您這麼做之前，您只會在應用程式中看到空白方格，而不是對應。 您必須從較舊的 Google Maps Android API 金鑰 v1 取得 Google Maps Android API v2 金鑰金鑰，否則無法運作。

取得 Maps API v2 金鑰包含下列步驟：

1. 取出用來簽署應用程式之金鑰儲存區的 SHA-1 指紋。
2. 在 Google Api 主控台中建立專案。
3. 取得 API 金鑰。

## <a name="obtaining-your-signing-key-fingerprint"></a>取得簽署金鑰指紋

若要從 Google 要求 Maps API 金鑰，您需要知道用來簽署應用程式之金鑰儲存區的 SHA-1 指紋。
一般來說，這表示您必須判斷 debug 金鑰儲存區的 SHA-1 指紋，然後是用於簽署應用程式以供發行的金鑰儲存區的 SHA-1 指紋。

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

根據預設，用來簽署 Xamarin. Android 應用程式之偵錯工具的金鑰儲存區可以在下列位置找到：

**C： \\ Users \\ [USERNAME] \\ AppData \\ 本機 \\ Xamarin \\ Mono for Android \\ debug. 金鑰儲存區**

您可以從 JDK 執行 `keytool` 命令來取得金鑰儲存區的相關資訊。 此工具通常可以在 JAVA bin 目錄中找到：

**C： \\ Program Files \\ Android \\ JDK \\ microsoft_dist_openjdk_ [VERSION] \\ bin \\keytool.exe**

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

根據預設，用來簽署 Xamarin. Android 應用程式之偵錯工具的金鑰儲存區可以在下列位置找到：

**適用于 Android/debug 的/Users/[USERNAME]/.local/share/Xamarin/Mono。金鑰儲存區**

您可以從 JDK 執行 `keytool` 命令來取得金鑰儲存區的相關資訊。 此工具通常可以在 JAVA bin 目錄中找到：

**/System/Library/JAVA/JAVAVirtualMachines/[VERSION]. jdk/內容/Home/bin/keytool**

-----

使用下列命令 () 所示的檔案路徑來執行 keytool：

```shell
keytool -list -v -keystore [STORE FILENAME] -alias [KEY NAME] -storepass [STORE PASSWORD] -keypass [KEY PASSWORD]
```

### <a name="debugkeystore-example"></a>Debug. 金鑰儲存區範例

針對預設的偵錯工具金鑰 (自動為您建立以進行) 的偵錯工具，請使用此命令：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

```cmd
keytool.exe -list -v -keystore "C:\Users\[USERNAME]\AppData\Local\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
```

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

```bash
keytool -list -v -keystore /Users/[USERNAME]/.local/share/Xamarin/Mono\ for\ Android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

-----

### <a name="production-keys"></a>生產金鑰

將應用程式部署至 Google Play 時，必須 [使用私密金鑰進行簽署](~/android/deploy-test/signing/index.md)。
`keytool`需要以私密金鑰詳細資料執行，以及產生用來建立生產 Google MAPS API 金鑰的 sha-1 指紋。 請記得在部署之前，使用正確的 Google Maps API 金鑰更新 **AndroidManifest.xml** 檔案。

### <a name="keytool-output"></a>Keytool 輸出

您應該會在主控台視窗中看到類似下列輸出的內容：

```shell
Alias name: androiddebugkey
Creation date: Jan 01, 2016
Entry type: PrivateKeyEntry
Certificate chain length: 1
Certificate[1]:
Owner: CN=Android Debug, O=Android, C=US
Issuer: CN=Android Debug, O=Android, C=US
Serial number: 4aa9b300
Valid from: Mon Jan 01 08:04:04 UTC 2013 until: Mon Jan 01 18:04:04 PST 2033
Certificate fingerprints:
    MD5:  AE:9F:95:D0:A6:86:89:BC:A8:70:BA:34:FF:6A:AC:F9
    SHA1: BB:0D:AC:74:D3:21:E1:43:07:71:9B:62:90:AF:A1:66:6E:44:5D:75
    Signature algorithm name: SHA1withRSA
    Version: 3
```

您將使用本指南稍後的 **SHA1** ) 所列的 sha-1 指紋 (。

## <a name="creating-an-api-project"></a>建立 API 專案

在您抓取了簽署金鑰儲存區的 SHA-1 指紋之後，必須在 Google Api 主控台中建立新的專案 (或將 Google Maps Android API v2 服務新增至現有的專案) 。

1. 在瀏覽器中，流覽至 [Google 開發人員主控台 API & Services 儀表板](https://console.developers.google.com/apis/dashboard/) ，然後按一下 [ **選取專案** ]。 按一下專案名稱，或按一下 [ **新增專案** ] 來建立新專案：

   [![Google 開發人員主控台的 [建立專案] 按鈕](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs-sml.png)](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs.png#lightbox)

2. 如果您已建立新的專案，請在顯示的 [ **新增專案** ] 對話方塊中輸入專案名稱。 此對話方塊會製造以您的專案名稱為基礎的唯一專案識別碼。 接著，按一下 [ **建立** ] 按鈕，如下列範例所示：

   [![新專案的名稱為 XamarinMapsDemo](obtaining-a-google-maps-api-key-images/02-new-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/02-new-project-vs.png#lightbox)

3. 一分鐘之後，就會建立專案，並將您帶到專案的 [ **儀表板** ] 頁面。 從該處按一下 [ **啟用 API 和服務** ]：

   [![按一下 [程式庫] 區段中的 [Google Maps Android API]](obtaining-a-google-maps-api-key-images/03-api-selection-vs-sml.png)](obtaining-a-google-maps-api-key-images/03-api-selection-vs.png#lightbox)

4. 從 [ **API 程式庫** ] 頁面中，按一下 [ **適用于 ANDROID 的 Maps SDK** ]。 在下一個頁面上，按一下 [ **啟用** ] 以開啟此專案的服務：

   [![按一下 [儀表板] 區段中的 [啟用] 按鈕](obtaining-a-google-maps-api-key-images/04-enable-api-vs-sml.png)](obtaining-a-google-maps-api-key-images/04-enable-api-vs.png#lightbox)

此時已建立 API 專案，並已在其中新增 Google Maps Android API v2。 不過，您無法在您的專案中使用此 API，直到您為其建立認證為止。 下一節說明如何建立 API 金鑰，以及授權 Xamarin. Android 應用程式使用此金鑰。

## <a name="obtaining-the-api-key"></a>取得 API 金鑰

建立 **Google Developer Console** API 專案之後，必須建立 Android API 金鑰。 Xamarin. Android 應用程式必須擁有 API 金鑰，才能將其授與 Android 地圖 API v2 的存取權。

1. 在 [ **適用于 Android 的 MAPS SDK** ] 頁面中，按一下上一個步驟中的 [ **啟用** ] 之後 (顯示) 中，移至 [ **認證** ] 索引標籤，然後按一下 [ **建立認證** ] 按鈕：

   [![Maps SDK for Android 認證訊息](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs-sml.png)](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs.png#lightbox)

2. 按一下 [ **API 金鑰** ]：

   [![將認證新增至您的專案對話方塊](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs.png#lightbox)

3. 按一下這個按鈕之後，就會產生 API 金鑰。 接下來必須限制此金鑰，如此一來，只有您的應用程式可以使用此金鑰來呼叫 Api。 按一下 [ **限制金鑰** ：

   [![按一下 [認證] 頁面上的 [限制金鑰]](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs-sml.png)](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs.png#lightbox)

4. 將 [ **名稱** ] 欄位從 **API 金鑰 1** 變更為名稱，以協助您記住 ( **XamarinMapsDemoKey** 在此範例) 中使用的金鑰。 接著，按一下 [ **Android 應用程式** ] 選項按鈕：

   [![在 [認證] 頁面上選取 Android 應用程式](obtaining-a-google-maps-api-key-images/08-key-restriction-vs-sml.png)](obtaining-a-google-maps-api-key-images/08-key-restriction-vs.png#lightbox)

5. 若要加入 SHA-1 指紋，請按一下 [ **+ 新增套件名稱和指紋** ：

   [![按一下 [新增套件名稱和指紋]](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs.png#lightbox)

6. 輸入您的應用程式套件名稱，然後輸入 SHA-1 憑證指紋 (取得， `keytool` 如本指南稍早的) 所述。 在下列範例中，會輸入的封裝名稱 `XamarinMapsDemo` ，後面接著從 **debug. 金鑰** 儲存區取得的 sha-1 憑證指紋：

   [![輸入的套件名稱是 com.xamarin.docs. map](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs-sml.png)](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs.png#lightbox)

7. 請注意，為了讓您的 APK 存取 Google Maps，您必須針對每個金鑰儲存區加入 SHA-1 指紋和套件名稱 (debug 和 release) 用來簽署您的 APK。 例如，如果您使用一部電腦進行 debug，而另一部電腦用來產生 release APK，您應該在第一部電腦的 debug 金鑰儲存區中包含 SHA-1 憑證指紋，並在第二部電腦的版本金鑰儲存區中加入 SHA-1 憑證指紋。 按一下 [ **+ 新增套件名稱和指紋** ]，以新增另一個指紋和套件名稱，如下列範例所示：

   [![新增另一個指紋會建立另一個 SHA-1 憑證](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs.png#lightbox)

8. 按一下 [ **儲存** ] 按鈕以儲存您的變更。 接下來，您會返回您的 API 金鑰清單。 如果您有先前建立的其他 API 金鑰，則也會在這裡列出。 在此範例中，只會列出在先前步驟中建立的一個 API 金鑰)  (：

   [![XamarinMapsDemoKey 會顯示在 [API 金鑰] 清單中](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs-sml.png)](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs.png#lightbox)

## <a name="connect-the-project-to-a-billable-account"></a>將專案連接至可計費帳戶

自6月 2018 11 日起，如果專案未連接到可計費帳戶，則 API 金鑰將無法運作 (即使行動應用程式) 的服務仍然免費。

1. 按一下 [漢堡] 功能表按鈕，然後選取 [ **帳單** ] 頁面：

   [![選取漢堡功能表計費區段](obtaining-a-google-maps-api-key-images/13-goto-billing-vs-sml.png)](obtaining-a-google-maps-api-key-images/13-goto-billing-vs.png#lightbox)

2. 將專案連結至帳單帳戶，方法是按一下 [ **連結帳單帳戶** ]，然後在顯示的快顯視窗上按一下 [ **建立帳單帳戶** ] (如果您沒有帳戶，系統會引導您建立新的) ：

   [![將專案連結至帳單帳戶](obtaining-a-google-maps-api-key-images/14-link-billing-account-vs-sml.png)](obtaining-a-google-maps-api-key-images/14-link-billing-account-vs.png#lightbox)

## <a name="adding-the-key-to-your-project"></a>將金鑰新增至您的專案

最後，將此 API 金鑰新增至您的 Xamarin Android 應用程式的 **AndroidManifest.XML** 檔案。 在下列範例中， `YOUR_API_KEY` 會以先前步驟中產生的 API 金鑰取代：

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    android:versionName="4.10" package="com.xamarin.docs.android.mapsandlocationdemo"
    android:versionCode="10">
...
  <application android:label="@string/app_name">
    <!-- Put your Google Maps V2 API Key here. -->
    <meta-data android:name="com.google.android.maps.v2.API_KEY" android:value="YOUR_API_KEY" />
    <meta-data android:name="com.google.android.gms.version" android:value="@integer/google_play_services_version" />
  </application>
</manifest>
```

## <a name="related-links"></a>相關連結

- [Google Api 主控台](https://code.google.com/apis/console/)
- [Google Maps API 金鑰](https://developers.google.com/maps/documentation/android/start#the_google_maps_api_key)
- [keytool](https://docs.oracle.com/javase/6/docs/technotes/tools/windows/keytool.html.)
