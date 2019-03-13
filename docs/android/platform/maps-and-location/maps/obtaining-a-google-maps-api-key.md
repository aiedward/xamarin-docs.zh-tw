---
title: 取得 Google Maps API 金鑰
description: 如何取得 Google Maps API 金鑰新增至您的應用程式對應功能。
ms.prod: xamarin
ms.assetid: D5969C57-3444-465E-D6FF-249AEE62E127
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/25/2018
ms.openlocfilehash: bfeb9d8fa2a0b5a9b18ab8266500586e2e3b6c68
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120017"
---
# <a name="obtaining-a-google-maps-api-key"></a>取得 Google Maps API 金鑰

若要在 Android 中使用 Google Maps 的功能，您需要註冊 google Maps API 金鑰。 之前這樣做，您會看到空白的方格，而不是對應應用程式中。 您必須取得 Google Maps Android API v2 金鑰-從較舊的 Google Maps Android API 金鑰的 v1 的金鑰將無法運作。

取得地圖服務 API v2 金鑰包含下列步驟：

1.  擷取用來簽署應用程式的金鑰存放區的 sha-1 指紋。
2.  在 Google APIs console 建立專案。
3.  取得 API 金鑰。


## <a name="obtaining-your-signing-key-fingerprint"></a>取得簽署金鑰指紋

若要要求時從 Google Maps API 金鑰，您需要知道的金鑰存放區用來簽署應用程式的 sha-1 指紋。
一般而言，這表示您必須判斷 sha-1 指紋的偵錯金鑰儲存區，然後 sha-1 指紋用來簽署您的應用程式版本的金鑰存放區。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

根據預設值用來簽署的 Xamarin.Android 應用程式可以偵錯版本的金鑰存放區找到位於下列位置：

**C:\\使用者\\[USERNAME]\\AppData\\本機\\Xamarin\\Mono for Android\\debug.keystore**

您可以從 JDK 執行 `keytool` 命令來取得金鑰儲存區的相關資訊。 這項工具通常是 Java 的 bin 目錄中找到：

**C:\\Program Files (x86)\\Java\\jdk [VERSION]\\bin\\keytool.exe**

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

根據預設值用來簽署的 Xamarin.Android 應用程式可以偵錯版本的金鑰存放區找到位於下列位置：

**針對 Android/debug.keystore /Users/[USERNAME]/.local/share/Xamarin/Mono**

您可以從 JDK 執行 `keytool` 命令來取得金鑰儲存區的相關資訊。 這項工具通常是 Java 的 bin 目錄中找到：

**/System/Library/Java/JavaVirtualMachines/[VERSION].jdk/Contents/Home/bin/keytool**

-----


執行 keytool 使用下列命令 （使用如上所示的檔案路徑）：

```shell
keytool -list -v -keystore [STORE FILENAME] -alias [KEY NAME] -storepass [STORE PASSWORD] -keypass [KEY PASSWORD]
```

### <a name="debugkeystore-example"></a>Debug.keystore 範例

預設偵錯金鑰 （這會自動為您建立的偵錯），請使用下列命令：

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

在應用程式部署至 Google Play 時，它必須是[使用私密金鑰簽署](~/android/deploy-test/signing/index.md)。
`keytool`必須具有私用金鑰的詳細資訊，以及產生用來建立生產環境的 Google Maps API 金鑰的 sha-1 指紋執行。 請記得更新**AndroidManifest.xml**具有正確的 Google Maps API 金鑰，再進行部署的檔案。

### <a name="keytool-output"></a>Keytool 輸出

您應該會看到類似下列輸出主控台視窗中：

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

您將會使用 sha-1 指紋 (列出後**SHA1**) 本指南稍後的。

## <a name="creating-an-api-project"></a>建立 API 專案

擷取簽章的金鑰儲存區的 sha-1 指紋之後，就必須建立新的專案，在 Google APIs console （或加入現有的專案中的 Google Maps Android API v2 服務）。

1. 在瀏覽器中，瀏覽至[Google 開發人員主控台 API 與服務儀表板](https://console.developers.google.com/apis/dashboard/)然後按一下**中，選取專案**。 按一下專案名稱，或建立一個新即可**新的專案**:

   [![Google 開發人員主控台建立的專案 按鈕](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs-sml.png)](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs.png#lightbox)

2. 如果您建立新的專案，請輸入中的專案名稱**新的專案**所顯示對話方塊。 這個對話方塊會製造根據您的專案名稱的唯一的專案識別碼。 接下來，按一下**建立**按鈕在此範例中所示：

   [![新專案命名為 XamarinMapsDemo](obtaining-a-google-maps-api-key-images/02-new-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/02-new-project-vs.png#lightbox)

3. 之後一分鐘左右，會建立專案，並將您導向**儀表板**專案的頁面。 從該處按一下**啟用的 API 和服務**:

   [![按一下 [程式庫] 區段中 Google Maps Android API](obtaining-a-google-maps-api-key-images/03-api-selection-vs-sml.png)](obtaining-a-google-maps-api-key-images/03-api-selection-vs.png#lightbox)

4. 從**API 程式庫**頁面上，按一下**適用於 Android 的地圖服務 SDK**。 在下一步 頁面上，按一下**啟用**若要開啟此專案的服務：

   [![按一下 [儀表板] 區段中的 [啟用] 按鈕](obtaining-a-google-maps-api-key-images/04-enable-api-vs-sml.png)](obtaining-a-google-maps-api-key-images/04-enable-api-vs.png#lightbox)

此時會建立 API 專案，並已新增至它的 Google Maps Android API v2。 不過，您無法使用此 API 在您的專案中，直到您為其建立的認證。 下節將說明如何建立 API 金鑰與白名單的 Xamarin.Android 應用程式，使它已被使用此金鑰的授權。

## <a name="obtaining-the-api-key"></a>取得 API 金鑰

在後**Google Developer Console** API 專案已建立，就必須建立 Android API 金鑰。 Xamarin.Android 應用程式必須擁有 API 金鑰，才能他們被授與存取 Android 的 Map API v2。

1. 在**適用於 Android 的地圖服務 SDK**會顯示的頁面 (按一下後**啟用**上一個步驟中)，請移至**認證**索引標籤，然後按一下 **建立認證**按鈕：

   [![適用於 Android 的認證訊息 maps SDK](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs-sml.png)](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs.png#lightbox)

2. 按一下  **API 金鑰**:

   [![將認證新增至您的專案 對話方塊](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs.png#lightbox)

3. 按一下此按鈕之後，會產生 API 金鑰。 接下來就必須限制此金鑰，讓只有您的應用程式可以呼叫 Api，使用此金鑰。 按一下 **限制索引鍵**:

   [![按一下 [認證] 頁面上的限制的索引鍵](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs-sml.png)](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs.png#lightbox)

4. 變更**名稱**欄位從**API 金鑰 1**會幫助您記住金鑰用於的名稱 (**XamarinMapsDemoKey**用在此範例中)。 接下來，按一下**Android 應用程式**選項按鈕：

   [![選取 [認證] 頁面上的 Android 應用程式](obtaining-a-google-maps-api-key-images/08-key-restriction-vs-sml.png)](obtaining-a-google-maps-api-key-images/08-key-restriction-vs.png#lightbox)

5. 若要新增 sha-1 指紋，請按一下 **+ 新增套件名稱和指紋**:

   [![按一下 新增套件名稱和指紋](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs.png#lightbox)

6. 輸入您的應用程式封裝名稱，然後輸入 sha-1 憑證指紋 (透過取得`keytool`稍早在本指南中所述)。 在下列範例中，封裝名稱`XamarinMapsDemo`是輸入，後面接著取自 sha-1 憑證指紋**debug.keystore**:

   [![輸入的封裝名稱是 com.xamarin.docs.android.map](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs-sml.png)](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs.png#lightbox)

7. 請注意，為了讓您存取 Google 地圖的 APK，您必須包含 sha-1 指紋和封裝名稱的每個金鑰儲存區 （偵錯和發行），您用來簽署您的 APK。 比方說，如果您使用一部電腦進行偵錯和產生發行 APK 的另一部電腦時，您應該包含 sha-1 憑證指紋，從第一部電腦的偵錯金鑰儲存區和從發行金鑰儲存區的 sha-1 憑證指紋第二部電腦中。 按一下  **+ 新增套件名稱和指紋**新增另一個的指紋和封裝名稱，在此範例中所示：

   [![新增另一個的指紋會建立另一個 sha-1 憑證](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs.png#lightbox)

8. 按一下 [儲存] 按鈕儲存您的變更。 接下來，您會回到您的 API 金鑰的清單。 如果您有其他您稍早建立的 API 金鑰，他們會也會列在此處。 在此範例中，會列出只有一個 （在先前步驟中建立） 的 API 金鑰：

   [![XamarinMapsDemoKey 會顯示在 API 金鑰清單](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs-sml.png)](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs.png#lightbox)

## <a name="connect-the-project-to-a-billable-account"></a>將專案連線至可計費的帳戶

從 6 月 11 2018 年的 API 金鑰將無法運作如果專案未連接到的可計費的帳戶 （即使服務仍然是免費的行動裝置應用程式）。

1. 按一下 [漢堡] 功能表按鈕，然後選取**計費**頁面：

   [![選取漢堡功能表的 [帳務] 區段](obtaining-a-google-maps-api-key-images/13-goto-billing-vs-sml.png)](obtaining-a-google-maps-api-key-images/13-goto-billing-vs.png#lightbox)

2. 連結至帳單帳戶的專案，依序按一下**計費帳戶連結**後面**建立帳單帳戶**上顯示快顯視窗 （如果您還沒有帳戶，將會引導您建立新的帳戶）：

   [![計費帳戶的連結專案](obtaining-a-google-maps-api-key-images/14-link-billing-account-vs-sml.png)](obtaining-a-google-maps-api-key-images/14-link-billing-account-vs.png#lightbox)

## <a name="adding-the-key-to-your-project"></a>將金鑰新增至您的專案

最後，新增到此 API 金鑰**AndroidManifest.XML**的 Xamarin.Android 應用程式的檔案。 在下列範例中，`YOUR_API_KEY`是要被取代的先前步驟中產生的 API 金鑰：

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

- [Google APIs Console](https://code.google.com/apis/console/)
- [Google Maps API 金鑰](https://developers.google.com/maps/documentation/android/start#the_google_maps_api_key)
- [keytool](http://docs.oracle.com/javase/6/docs/technotes/tools/windows/keytool.html.)
