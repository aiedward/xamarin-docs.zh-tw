---
title: 取得 Google Maps API 金鑰
description: 如何取得 Google Maps API 金鑰，以將地圖功能新增至您的應用程式。
ms.prod: xamarin
ms.assetid: D5969C57-3444-465E-D6FF-249AEE62E127
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/25/2018
ms.openlocfilehash: bf0a099546b2d5610a639cbf9af4c7676d10bef9
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73020054"
---
# <a name="obtaining-a-google-maps-api-key"></a>取得 Google Maps API 金鑰

若要使用 Android 中的 Google Maps 功能，您必須向 Google 註冊 Maps API 金鑰。 在這麼做之前，您只會看到空白方格，而不是應用程式中的對應。 您必須取得 Google Maps Android API v2 金鑰-來自舊版 Google Maps 的 android API 金鑰 v1 將無法使用。

取得地圖服務 API v2 金鑰包含下列步驟：

1. 取出用來簽署應用程式之金鑰儲存區的 SHA-1 指紋。
2. 在 Google Api 主控台中建立專案。
3. 取得 API 金鑰。

## <a name="obtaining-your-signing-key-fingerprint"></a>取得您的簽署金鑰指紋

若要向 Google 要求 Maps API 金鑰，您必須知道用來簽署應用程式之金鑰儲存區的 SHA-1 指紋。
一般來說，這表示您必須判斷 debug 金鑰存放區的 SHA-1 指紋，然後才是用來簽署應用程式以供發行之金鑰儲存區的 SHA-1 指紋。

<!-- markdownlint-disable MD001 -->

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

根據預設，您可以在下列位置找到用來簽署 Xamarin. Android 應用程式之 debug 版本的金鑰儲存區：

**C：\\使用者\\[USERNAME]\\AppData\\本機\\Xamarin\\Mono （適用于 Android）\\debug. 金鑰儲存區**

您可以從 JDK 執行 `keytool` 命令來取得金鑰儲存區的相關資訊。 這項工具通常會在 JAVA bin 目錄中找到：

**C：\\Program Files （x86）\\JAVA\\jdk [VERSION]\\bin\\keytool**

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

根據預設，您可以在下列位置找到用來簽署 Xamarin. Android 應用程式之 debug 版本的金鑰儲存區：

**適用于 Android/debug. 金鑰儲存區的/Users/[USERNAME]/.local/share/Xamarin/Mono**

您可以從 JDK 執行 `keytool` 命令來取得金鑰儲存區的相關資訊。 這項工具通常會在 JAVA bin 目錄中找到：

**/System/Library/JAVA/JAVAVirtualMachines/[VERSION] jdk/內容/首頁/bin/keytool**

-----

使用下列命令執行 keytool （使用如上所示的檔案路徑）：

```shell
keytool -list -v -keystore [STORE FILENAME] -alias [KEY NAME] -storepass [STORE PASSWORD] -keypass [KEY PASSWORD]
```

### <a name="debugkeystore-example"></a>Debug. 金鑰存放區範例

若為預設的偵錯工具金鑰（它會自動為您建立以供您進行偵錯工具），請使用下列命令：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

```cmd
keytool.exe -list -v -keystore "C:\Users\[USERNAME]\AppData\Local\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
```

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

```bash
keytool -list -v -keystore /Users/[USERNAME]/.local/share/Xamarin/Mono\ for\ Android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

-----

### <a name="production-keys"></a>生產金鑰

將應用程式部署到 Google Play 時，必須[使用私密金鑰進行簽署](~/android/deploy-test/signing/index.md)。
`keytool` 必須以私密金鑰詳細資料執行，而產生的 SHA-1 指紋會用來建立生產的 Google Maps API 金鑰。 請記得在部署之前，使用正確的 Google Maps API 金鑰來更新**androidmanifest.xml。**

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

您將在本指南稍後使用 SHA-1 指紋（在**SHA1**之後列出）。

## <a name="creating-an-api-project"></a>建立 API 專案

在您抓取簽署金鑰儲存區的 SHA-1 指紋之後，必須在 Google Api 主控台中建立新的專案（或將 Google Maps Android API v2 服務新增至現有的專案）。

1. 在瀏覽器中，流覽至[Google 開發人員主控台 API & Services 儀表板](https://console.developers.google.com/apis/dashboard/)]，然後按一下 [**選取專案**。 按一下專案名稱，或按一下 [**新增專案**] 以建立新專案：

   [![Google 開發人員主控台 [建立專案] 按鈕](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs-sml.png)](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs.png#lightbox)

2. 如果您已建立新的專案，請在顯示的 [**新增專案**] 對話方塊中輸入專案名稱。 此對話方塊會根據您的專案名稱製造唯一的專案識別碼。 接下來，按一下 [**建立**] 按鈕，如下列範例所示：

   [![新專案的名稱為 XamarinMapsDemo](obtaining-a-google-maps-api-key-images/02-new-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/02-new-project-vs.png#lightbox)

3. 一分鐘之後，即會建立專案，並將您帶到專案的 [**儀表板**] 頁面。 從該處按一下 [**啟用 API 和服務**]：

   [在 [程式庫] 區段中![按一下 [Google Maps Android API]](obtaining-a-google-maps-api-key-images/03-api-selection-vs-sml.png)](obtaining-a-google-maps-api-key-images/03-api-selection-vs.png#lightbox)

4. 在 [ **API 程式庫**] 頁面中，按一下 [**適用于 ANDROID 的 Maps SDK**]。 在下一個頁面上，按一下 [**啟用**] 以開啟此專案的服務：

   [![按一下 [儀表板] 區段中的 [啟用] 按鈕](obtaining-a-google-maps-api-key-images/04-enable-api-vs-sml.png)](obtaining-a-google-maps-api-key-images/04-enable-api-vs.png#lightbox)

此時已建立 API 專案，並在其中新增了 Google Maps Android API v2。 不過，您無法在專案中使用此 API，除非您為其建立認證。 下一節將說明如何建立 API 金鑰，並以白清單列出 Xamarin Android 應用程式，以獲得授權可使用此金鑰。

## <a name="obtaining-the-api-key"></a>取得 API 金鑰

建立**Google 開發人員主控台**API 專案之後，必須建立 Android api 金鑰。 Xamarin Android 應用程式必須擁有 API 金鑰，才能授與存取 Android Map API v2 的許可權。

1. 在顯示的 [ **MAPS SDK For Android** ] 頁面中（在上一個步驟中按一下 [**啟用**] 之後），移至 [**認證**] 索引標籤，然後按一下 [**建立認證**] 按鈕：

   [適用于 Android 認證的![Maps SDK 訊息](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs-sml.png)](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs.png#lightbox)

2. 按一下 [ **API 金鑰**]：

   [![將認證新增至您的專案對話方塊](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs.png#lightbox)

3. 按一下此按鈕之後，就會產生 API 金鑰。 接下來必須限制此金鑰，讓您的應用程式只能使用此金鑰來呼叫 Api。 按一下 [**限制金鑰**]：

   [![按一下 [認證] 頁面上的 [限制金鑰]](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs-sml.png)](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs.png#lightbox)

4. 將 [**名稱**] 欄位從**API 金鑰 1**變更為可協助您記住金鑰用途的名稱（在此範例中使用**XamarinMapsDemoKey** ）。 接下來，按一下 [ **Android 應用程式**] 選項按鈕：

   [![在 [認證] 頁面上選取 [Android 應用程式]](obtaining-a-google-maps-api-key-images/08-key-restriction-vs-sml.png)](obtaining-a-google-maps-api-key-images/08-key-restriction-vs.png#lightbox)

5. 若要新增 SHA-1 指紋，請按一下 [ **+ 新增套件名稱和指紋**]：

   [![按一下 [新增套件名稱] 和 [指紋]](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs.png#lightbox)

6. 輸入您的應用程式的套件名稱，並輸入 SHA-1 憑證指紋（透過 `keytool` 取得，如本指南稍早所述）。 在下列範例中，會輸入 `XamarinMapsDemo` 的套件名稱，後面接著從**debug. 金鑰**儲存區取得的 sha-1 憑證指紋：

   [輸入的![套件名稱為 .com。](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs-sml.png)](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs.png#lightbox)

7. 請注意，為了讓您的 APK 存取 Google Maps，您必須針對用來簽署 APK 的每個金鑰儲存區（debug 和 release）包含 SHA-1 指紋和套件名稱。 例如，如果您使用一部電腦進行 debug，而另一部電腦用於產生發行 APK，您應該包含第一部電腦的 debug 金鑰儲存區中的 SHA-1 憑證指紋，以及來自的版本金鑰儲存區的 SHA-1 憑證指紋第二部電腦。 按一下 [ **+ 新增套件名稱和指紋**]，以新增另一個指紋和套件名稱，如下列範例所示：

   [新增另一個指紋![建立另一個 SHA-1 憑證](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs.png#lightbox)

8. 按一下 [儲存] 按鈕儲存您的變更。 接下來，您會回到 API 金鑰清單。 如果您稍早建立了其他 API 金鑰，它們也會列在此處。 在此範例中，只會列出一個 API 金鑰（在先前步驟中建立的）：

   [[API 金鑰] 清單中會顯示![XamarinMapsDemoKey](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs-sml.png)](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs.png#lightbox)

## <a name="connect-the-project-to-a-billable-account"></a>將專案連接到可計費帳戶

從2018年6月11日開始，如果專案未連接到可計費帳戶（即使行動應用程式的服務仍然免費，API 金鑰將無法使用）。

1. 按一下 [漢堡] 功能表按鈕，然後選取 [**帳單**] 頁面：

   [![選取漢堡功能表 [計費] 區段](obtaining-a-google-maps-api-key-images/13-goto-billing-vs-sml.png)](obtaining-a-google-maps-api-key-images/13-goto-billing-vs.png#lightbox)

2. 將專案連結至帳單帳戶，方法是按一下 [**連結帳單帳戶**]，然後在顯示的快顯視窗中，按 [**建立帳單帳戶**] （如果您沒有帳戶，系統會引導您建立一個新的）：

   [![將專案連結至帳單帳戶](obtaining-a-google-maps-api-key-images/14-link-billing-account-vs-sml.png)](obtaining-a-google-maps-api-key-images/14-link-billing-account-vs.png#lightbox)

## <a name="adding-the-key-to-your-project"></a>將金鑰新增至您的專案

最後，將此 API 金鑰新增至您的 Xamarin Android 應用程式的**androidmanifest.xml**檔案。 在下列範例中，會將 `YOUR_API_KEY` 取代為在先前步驟中產生的 API 金鑰：

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
