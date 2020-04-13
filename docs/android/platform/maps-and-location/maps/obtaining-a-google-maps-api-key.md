---
title: 取得 Google Maps API 金鑰
description: 如何獲取 Google 地圖 API 金鑰,以便向應用添加地圖功能。
ms.prod: xamarin
ms.assetid: D5969C57-3444-465E-D6FF-249AEE62E127
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/25/2018
ms.openlocfilehash: 371876d087c7027d4cfe2d2d9ada8b0dbedb5dd5
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "75488968"
---
# <a name="obtaining-a-google-maps-api-key"></a>取得 Google Maps API 金鑰

要在 Android 中使用 Google 地圖功能,您需要向 Google 註冊地圖 API 金鑰。 執行此操作之前,您將在應用程式中看到空白網格而不是地圖。 你必須獲得谷歌地圖AndroidAPIv2密鑰 - 密鑰從舊的谷歌地圖AndroidAPI密鑰v1將不起作用。

取得地圖 API v2 金鑰涉及以下步驟:

1. 檢索用於對應用程式進行簽名的密鑰存儲的 SHA-1 指紋。
2. 在 Google API 控制台中創建專案。
3. 獲取 API 金鑰。

## <a name="obtaining-your-signing-key-fingerprint"></a>取得簽署金鑰指紋

要向 Google 請求地圖 API 金鑰,您需要瞭解用於對應用程式進行簽名的密鑰儲存的 SHA-1 指紋。
通常,這意味著您必須確定調試密鑰存儲的 SHA-1 指紋,然後確定用於對應用程式進行釋放簽名的密鑰存儲的 SHA-1 指紋。

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

預設情況下,用於對 Xamarin.Android 應用程式的除錯版本進行簽名的金鑰庫可以在以下位置找到:

**C:\\\\\\使用者 [USERNAME]\\\\\\應用資料本地 Xamarin\\單聲道用於 Android 除錯.keystore**

您可以從 JDK 執行 `keytool` 命令來取得金鑰儲存區的相關資訊。 此工具通常位於 Java bin 目錄中:

**C:\\\\程式\\\\檔案 Android\\\\jdk microsoft_dist_openjdk_ [VERSION] 箱鍵工具.exe**

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

預設情況下,用於對 Xamarin.Android 應用程式的除錯版本進行簽名的金鑰庫可以在以下位置找到:

**/使用者/[使用者名]/.本地/共用/Xamarin/Mono 用於 Android/調試.keystore**

您可以從 JDK 執行 `keytool` 命令來取得金鑰儲存區的相關資訊。 此工具通常位於 Java bin 目錄中:

**/系統/圖書館/JaVA/JaVA虛擬機/[VERSION]jdk/內容/首頁/箱/鑰匙工具**

-----

使用以下指令執行金鑰工具(使用上面顯示的檔案路徑):

```shell
keytool -list -v -keystore [STORE FILENAME] -alias [KEY NAME] -storepass [STORE PASSWORD] -keypass [KEY PASSWORD]
```

### <a name="debugkeystore-example"></a>除錯金鑰儲存範例

對於預設除錯金鑰(自動為您建立用於除錯),請使用以下指令:

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

將應用程式部署到 Google Play 時,必須[使用私密金鑰簽署](~/android/deploy-test/signing/index.md)。
`keytool`需要使用私鑰詳細資訊運行,以及用於創建生產 Google 地圖 API 金鑰的生成的 SHA-1 指紋。 請記住在部署之前使用正確的 Google 地圖 API 密鑰更新**AndroidManifest.xml**檔。

### <a name="keytool-output"></a>鑰匙工具輸出

您應該在主控台視窗中看到類似以下輸出的內容:

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

您將在本指南的後面部分使用 SHA-1 指紋(在**SHA1**之後列出)。

## <a name="creating-an-api-project"></a>建立 API 專案

檢索簽名密鑰庫的 SHA-1 指紋後,有必要在 Google API 控制台中創建新專案(或將 Google 地圖 Android API v2 服務添加到現有專案)。

1. 在瀏覽器中,導航到[Google 開發人員主控台 API &服务仪表板](https://console.developers.google.com/apis/dashboard/),然後單擊「**選擇專案**」。 點選項目名稱或透過按下 **「新增項目」 建立新名稱**:

   [![谷歌開發者主控台建立專案按鈕](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs-sml.png)](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs.png#lightbox)

2. 如果建立新專案,請在顯示的 **「新專案」** 對話方塊中輸入專案名稱。 此對話框將創建基於專案名稱的唯一項目 ID。 接下來,按下「**建立**」按鈕,如以下範例所示:

   [![新專案命名為XamarinMapsDemo](obtaining-a-google-maps-api-key-images/02-new-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/02-new-project-vs.png#lightbox)

3. 大約一分鐘後,將創建專案,並帶您到專案的 **「儀錶板」** 頁面。 從那裡,點擊**啟用 APIIS 與服務**:

   [![在「庫」部分單擊 Google 地圖 Android API](obtaining-a-google-maps-api-key-images/03-api-selection-vs-sml.png)](obtaining-a-google-maps-api-key-images/03-api-selection-vs.png#lightbox)

4. 在**API 函式庫**頁面中,按**下 Android 的地圖 SDK**。 在下一頁上,按下**開啟**開啟此項目的服務:

   [![點選「儀表板」 部分中的「啟用」按鈕](obtaining-a-google-maps-api-key-images/04-enable-api-vs-sml.png)](obtaining-a-google-maps-api-key-images/04-enable-api-vs.png#lightbox)

此時,API 專案已創建,並且 Google 地圖 Android API v2 已添加到其中。 但是,在為項目創建憑據之前,您不能在專案中使用此 API。 下一節將介紹如何創建 API 金鑰,並將 Xamarin.Android 應用程式白名單為,以便授權它使用此密鑰。

## <a name="obtaining-the-api-key"></a>取得 API 金鑰

創建**Google 開發人員主控台**API 專案後,必須創建 Android API 金鑰。 Xamarin.Android 應用程式在被授予對 Android 地圖 API v2 的訪問許可權之前,必須具有 API 密鑰。

1. 在顯示**的 Android 地圖 SDK**頁中(按下一步中的**ENABLE**後),轉到 **「憑據」** 選項卡並按下「**建立認證」** 按鈕:

   [![映射 Android 認證訊息的 SDK](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs-sml.png)](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs.png#lightbox)

2. 點選**API 鍵**:

   [![新增項目對話框](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs.png#lightbox)

3. 按下此按鈕後,將生成 API 金鑰。 接下來,有必要限制此密鑰,以便只有你的應用可以使用此密鑰調用 API。 按下 **「限制鍵**」 :

   [![單擊憑據頁上的"限制鍵"](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs-sml.png)](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs.png#lightbox)

4. 將**名稱**欄位從**API 金鑰 1**更改為一個名稱,以説明您記住金鑰用於什麼(本示例中使用了**XamarinMaps DemoKey)。** 接下來,按一下**Android 應用**單選按鈕:

   [![在認證頁面上選擇 Android 應用](obtaining-a-google-maps-api-key-images/08-key-restriction-vs-sml.png)](obtaining-a-google-maps-api-key-images/08-key-restriction-vs.png#lightbox)

5. 要新增 SHA-1 指紋,請按下 **「 新增套件名稱」 與 「指紋**」 :

   [![單擊"添加包名稱和指紋"](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs.png#lightbox)

6. 輸入應用的包名稱並輸入 SHA-1 認證指紋`keytool`(如本指南前面所述)。 在下面的範例中,輸入`XamarinMapsDemo`的套件名稱,然後是從**除錯**器取得的 SHA-1 憑證指紋:

   [![輸入的套件名稱是 com.xamarin.docs.android.map](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs-sml.png)](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs.png#lightbox)

7. 請注意,為了使您的 APK 能夠造訪 Google 地圖,您必須包含用於對 APK 簽名的每個金鑰儲存(調試和發佈)的 SHA-1 指紋和包名稱。 例如,如果使用一台電腦進行調試,使用另一台計算機生成版本 APK,則應包括第一台電腦的調試鑰存儲中的 SHA-1 證書指紋和第二台電腦的發布密鑰存儲中的 SHA-1 證書指紋。 按下 **+ 新增套件名稱和指紋**以添加其他指紋和套件名稱,如本示例所示:

   [![新增另一個指紋將建立另一個 SHA-1 憑證](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs.png#lightbox)

8. 按一下 [儲存] **** 按鈕以儲存您的變更。 接下來,您將返回到 API 金鑰的清單。 如果您有之前創建的其他 API 金鑰,它們也將在此處列出。 這個範例中,僅列出 API 金鑰(在前面的步驟中建立):

   [![XamarinMapsDemoKey 顯示在 API 金鑰清單中](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs-sml.png)](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs.png#lightbox)

## <a name="connect-the-project-to-a-billable-account"></a>將專案連線到計費帳戶

從 2018 年 6 月 11 日開始,如果專案未連接到計費帳戶(即使該服務仍對行動應用是免費的),則 API 金鑰將不起作用。

1. 按下漢堡選單按鈕並選擇 **「計費**」頁面:

   [![選擇漢堡選單計費部分](obtaining-a-google-maps-api-key-images/13-goto-billing-vs-sml.png)](obtaining-a-google-maps-api-key-images/13-goto-billing-vs.png#lightbox)

2. 點選 **「連結計費帳號」**, 然後在顯示的彈出視窗上建立**BILLING 帳戶**(如果您沒有帳戶,將引導您創建新帳戶),從而將專案連結到計費帳戶:

   [![將專案連結到計費帳戶](obtaining-a-google-maps-api-key-images/14-link-billing-account-vs-sml.png)](obtaining-a-google-maps-api-key-images/14-link-billing-account-vs.png#lightbox)

## <a name="adding-the-key-to-your-project"></a>將金鑰新增到項目

最後,將此 API 金鑰添加到 Xamarin.Android 應用的**Android 清單.XML**檔中。 在下面的範例中,`YOUR_API_KEY`將取代為在前面的步驟中產生的 API 金鑰:

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

- [谷歌API主控台](https://code.google.com/apis/console/)
- [谷歌地圖API金鑰](https://developers.google.com/maps/documentation/android/start#the_google_maps_api_key)
- [keytool](https://docs.oracle.com/javase/6/docs/technotes/tools/windows/keytool.html.)
