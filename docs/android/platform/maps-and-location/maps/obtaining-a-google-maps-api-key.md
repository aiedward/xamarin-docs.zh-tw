---
title: "取得 Google 地圖服務 API 金鑰"
ms.topic: article
ms.prod: xamarin
ms.assetid: D5969C57-3444-465E-D6FF-249AEE62E127
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: b529d0090595cc8a3020f37606d5dc3db5f0db74
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="obtaining-a-google-maps-api-key"></a>取得 Google 地圖服務 API 金鑰

若要在 Android 中使用 Google 地圖功能，您需要註冊 Google 的 Maps API 索引鍵。 之前這樣做，您會看到空白的方格，而不是對應您的應用程式。 您必須取得 Google 地圖 Android API v2 金鑰-從較舊的 Google 地圖 Android API 金鑰 v1 的金鑰將無法運作。

取得地圖 API v2 金鑰包含下列步驟：

1.  擷取用來簽署應用程式的金鑰存放區的 sha-1 指紋。
2.  在 Google Api 主控台中建立專案。
3.  取得 API 金鑰。

<a name="Step_1_-_Obtaining_your_Signing_Key_Fingerprint" />

## <a name="obtaining-your-signing-key-fingerprint"></a>取得簽署金鑰指紋

若要將來自 Google 要求 Maps API 金鑰，您需要知道 sha-1 的指紋用來簽署應用程式的金鑰存放區。
一般而言，這表示您必須判斷 sha-1 的指紋的偵錯金鑰存放區，而且然後 sha-1 指紋辨識為用來簽署您的應用程式版本的金鑰存放區。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

根據預設，用來簽署的應用程式可以是 Xamarin.Android 偵錯版本的金鑰存放區位於下列位置：

**C:\\使用者\\[USERNAME]\\AppData\\本機\\Xamarin\\for Android 單聲道\\debug.keystore**

您可以從 JDK 執行 `keytool` 命令來取得金鑰儲存區的相關資訊。 此工具通常是 Java bin 目錄中找到：

**C:\\Program Files (x86)\\Java\\jdk [VERSION]\\bin\\keytool.exe**

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

根據預設，用來簽署的應用程式可以是 Xamarin.Android 偵錯版本的金鑰存放區位於下列位置：

**Android/debug.keystore 的 /Users/[USERNAME]/.local/share/Xamarin/Mono**

您可以從 JDK 執行 `keytool` 命令來取得金鑰儲存區的相關資訊。 此工具通常是 Java bin 目錄中找到：

**/System/Library/Java/JavaVirtualMachines/[VERSION].jdk/Contents/Home/bin/keytool**

-----


執行 keytool 使用下列命令 （使用如上所示的檔案路徑）：

```shell
keytool -list -v -keystore [STORE FILENAME] -alias [KEY NAME] -storepass [STORE PASSWORD] -keypass [KEY PASSWORD]
```

### <a name="debugkeystore-example"></a>Debug.keystore 範例

預設偵錯金鑰 （這會自動為您建立的偵錯），請使用下列命令：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

```cmd
keytool.exe -list -v -keystore "C:\Users\[USERNAME]\AppData\Local\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
```

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

```bash
keytool -list -v -keystore /Users/[USERNAME]/.local/share/Xamarin/Mono\ for\ Android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

-----


### <a name="production-keys"></a>產品金鑰

在 Google play 中部署應用程式，它必須是[使用私密金鑰簽署](~/android/deploy-test/signing/index.md)。
`keytool`必須要與私用金鑰的詳細資料，並產生用來建立生產 Google Maps API 金鑰的 sha-1 指紋一起執行。 請記得更新**AndroidManifest.xml**具有正確 Google Maps API 金鑰之前部署的檔案。

### <a name="keytool-output"></a>Keytool 輸出

您應該會看到類似下列的輸出在主控台視窗中：

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

您將會使用 sha-1 指紋 (之後列出**SHA1**) 本指南稍後的。

<a name="Step_2_-Create_an_API_project" />

## <a name="creating-an-api-project"></a>建立 API 專案

擷取的簽章的金鑰存放區的 sha-1 指紋之後，就必須在 Google Api 主控台中建立新的專案 （或 Google 地圖 Android API v2 服務加入至現有的專案）。

1. 在瀏覽器中，瀏覽至[Google 開發人員主控台](https://console.developers.google.com/)： 按一下**建立專案**:

   [![Google 開發人員主控台建立的專案 按鈕](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs-sml.png)](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs.png)

2. 在**新專案**對話方塊出現，請輸入專案名稱。
   對話方塊會製造根據您的專案名稱，唯一的專案識別碼，在此範例所示：

   [![新的專案名稱為 XamarinMapsDemo](obtaining-a-google-maps-api-key-images/02-new-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/02-new-project-vs.png)

3. 按一下 [建立] 按鈕。 約一分鐘之後, 會建立專案，並將您導向**API Manager**頁面。 在**文件庫**區段中，按一下**Google 地圖 Android API**:

   [![按一下 Google 地圖 Android 應用程式開發介面程式庫一節](obtaining-a-google-maps-api-key-images/03-api-selection-vs-sml.png)](obtaining-a-google-maps-api-key-images/03-api-selection-vs.png)

4. 在頂端**Google 地圖 Android API**頁面上，按一下**啟用**若要開啟這個專案服務：

   [![按一下 [儀表板] 區段中的 [啟用] 按鈕](obtaining-a-google-maps-api-key-images/04-enable-api-vs-sml.png)](obtaining-a-google-maps-api-key-images/04-enable-api-vs.png)


此時會建立 API 專案和 Google 地圖 Android API v2 已新增至它。 不過，您才可以使用此 API 在您的專案中為其建立認證。 接下來我們將探討如何建立 API 金鑰與白名單 Xamarin.Android 應用程式，讓它已獲授權使用此金鑰。

<a name="Obtaining_the_API_Key" />

## <a name="obtaining-the-api-key"></a>取得 API 金鑰

之後**Google 開發人員主控台**API 專案已建立，則需要建立 Android API 金鑰。 它們會授與存取權 Android 對應 API v2 Xamarin.Android 應用程式必須具備 API 金鑰。

1. 在**Google 地圖 Android API**顯示頁面 (按一下後**啟用**上一個步驟中)，按一下**前往認證**按鈕：

   [![這個 API 已啟用訊息](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs-sml.png)](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs.png)

2. 在**認證**頁面上，按一下**我需要哪些認證？**按鈕：

   [![將認證新增至您的專案對話方塊](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs.png)

3. 按一下此按鈕後，即會產生 API 金鑰。 接下來是必須限制此金鑰可讓您應用程式呼叫 Api 與此索引鍵。 按一下**限制金鑰**:

   [![按一下 [認證] 頁面上的限制金鑰](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs-sml.png)](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs.png)

4. 變更**名稱**欄位從**API 金鑰 1**名稱可協助您記住此金鑰的使用 (**XamarinMapsDemoKey**用在此範例中)。 接下來，按一下**Android 應用程式**選項按鈕：

   [![選取 [認證] 頁面上的 Android 應用程式](obtaining-a-google-maps-api-key-images/08-key-restriction-vs-sml.png)](obtaining-a-google-maps-api-key-images/08-key-restriction-vs.png)

5. 若要新增 sha-1 指紋，請按一下**+ 加入封裝的名稱和指紋**:

   [![按一下 新增套件名稱和指紋](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs.png)

6. 輸入您的應用程式封裝名稱，並輸入 sha-1 憑證指紋 (透過取得`keytool`稍早在本指南中所述)。 在下列範例中，封裝名稱`XamarinMapsDemo`是輸入，後面接著取自的憑證 sha-1 指紋**debug.keystore**:

   [![輸入封裝名稱是 com.xamarin.docs.android.map](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs-sml.png)](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs.png)

7. 請注意，為了讓您存取 Google 地圖 APK，您必須包含 sha-1 指紋，並封裝您用來簽署您 APK 每 keystore （偵錯和發行） 的名稱。 例如，如果您使用一部電腦進行偵錯和產生發行 APK 的另一部電腦，您應該包含 sha-1 憑證指紋，從 偵錯金鑰存放區中的第一部電腦和 sha-1 憑證指紋，從發行金鑰存放區中的第二部電腦中。 按一下**+ 加入封裝的名稱和指紋**加入另一個的指紋和封裝名稱，在此範例所示：

   [![加入另一個指紋會建立另一個 sha-1 憑證](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs.png)

8. 按一下 [儲存] 按鈕儲存您的變更。 接下來，您會回到您的 API 金鑰清單。 如果您有其他您稍早建立的 API 金鑰，則會也在此列出。 在此範例中，會列出一個應用程式開發介面索引鍵 （在上一個步驟中建立）：

   [![API 金鑰清單中顯示 XamarinMapsDemoKey](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs-sml.png)](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs.png)


<a name="Adding_the_Key" />

## <a name="adding-the-key-to-your-project"></a>將索引鍵加入至您的專案

最後，會加入到此 API 金鑰**AndroidManifest.XML** Xamarin.Android 應用程式的檔案。 在下列範例中，`YOUR_API_KEY`換成在先前步驟中產生的 API 金鑰是：

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    android:versionName="4.10" package="com.xamarin.docs.android.mapsandlocationdemo"
    android:versionCode="10">
...

  <application android:label="@string/app_name">
    <!-- Put your Google Maps V2 API Key here. -->
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="YOUR_API_KEY" />
    <meta-data android:name="com.google.android.gms.version" android:value="@integer/google_play_services_version" />
  </application>
</manifest>
```


## <a name="related-links"></a>相關連結

- [Google Api 主控台](https://code.google.com/apis/console/)
- [Google 地圖服務 API 金鑰](https://developers.google.com/maps/documentation/android/start#the_google_maps_api_key)
- [keytool](http://docs.oracle.com/javase/6/docs/technotes/tools/windows/keytool.html.)
