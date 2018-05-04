---
title: 在 Android 應用程式連結
description: 本指南將討論如何 Android 6.0 支援應用程式連結，可讓行動裝置應用程式回應 Url 在網站上的技術。 它將會討論哪些應用程式連結是、 如何實作應用程式連結，在 Android 6.0 應用程式，以及如何設定網站要授與權限的網域中的行動應用程式。
ms.prod: xamarin
ms.assetid: 48174E39-19FD-43BC-B54C-9AF11D4B1F91
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 84185bb616597ee62a35c1acacc5e3664f500c21
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/03/2018
---
# <a name="app-linking-in-android"></a>在 Android 應用程式連結

_本指南將討論如何 Android 6.0 支援應用程式連結，可讓行動裝置應用程式回應 Url 在網站上的技術。它將會討論哪些應用程式連結是、 如何實作應用程式連結，在 Android 6.0 應用程式，以及如何設定網站要授與權限的網域中的行動應用程式。_

## <a name="app-linking-overview"></a>應用程式連結的概觀

行動應用程式不再存在於定址接收器&ndash;在許多情況下，它們是重要的元件，它們的企業，以及其網站。 最好針對企業順暢地連線其網站和行動應用程式，以啟動 行動應用程式和行動裝置應用程式中顯示相關內容的網站上的連結。 *應用程式連結*(也稱為*深層連結*) 是其中一種技術，可讓行動裝置，以回應至 URI，然後啟動與這個 URI 對應的行動應用程式。

Android 處理應用程式連結到*意圖系統*&ndash;行動瀏覽器，當使用者按一下行動瀏覽器中的連結時，會發送 Android 會委派給已註冊的應用程式意圖。 比方說，烹飪的網站上的連結會開啟該網站相關聯的行動裝置應用程式並向使用者顯示特定的配方。 如果有多個應用程式登錄來處理該意圖，則 Android 將會引發所謂*去除混淆對話方塊*，將會詢問使用者可選取應針對處理意圖，應用程式之應用程式範例：

![範例螢幕擷取畫面去除混淆 對話方塊](app-linking-images/01-disambiguation-dialog.png)

使用自動連結處理，這可改善 android 6.0。 Android 對自動註冊應用程式的預設處理常式做為 URI 可能&ndash;應用程式會自動啟動，並直接瀏覽到相關的活動。 決定處理 URI click Android 6.0 的方式取決於下列條件：

1. **現有的應用程式已使用的 URI 相關聯**&ndash;使用者可能有已關聯的現有應用程式的 uri。 在此情況下，Android 將會繼續使用該應用程式。
2. **任何現有的應用程式相關聯的 uri，但安裝支援的應用程式**&ndash;在此案例中，使用者尚未指定現有的應用程式，因此 Android 將會使用已安裝支援的應用程式來處理要求。
3. **任何現有的應用程式相關聯的 uri，但許多支援的應用程式會安裝**&ndash;因為有多個應用程式支援的 URI，去除混淆對話方塊將會顯示，而且使用者必須先選取哪一個應用程式將會處理的 URI。

如果使用者具有不支援 URI 的應用程式安裝，且其中一個後續安裝，然後 Android 會設定該應用程式的預設處理常式 uri 之後驗證與 URI 相關聯的網站之間的關聯。

本指南將討論如何設定 Android 6.0 應用程式，以及如何建立及發行數位資產的連結檔案，以支援 Android 6.0 中的應用程式連結。

## <a name="requirements"></a>需求

本指南需要 Xamarin.Android 6.1 以及 Android 6.0 （API 層級 23） 為目標的應用程式或更高版本。

應用程式連結，就可以在舊版的 Android 使用[Rivets NuGet 封裝](https://www.nuget.org/packages/Rivets/)Xamarin 元件存放區中。 鉚釘封裝不相容應用程式連結，在 Android 6.0;它不支援 Android 6.0 應用程式連結。

## <a name="configuring-app-linking-in-android-60"></a>在 Android 6.0 中設定 應用程式連結

設定 Android 6.0 中的應用程式連結包含兩個主要步驟：

1. **加入一或多個意圖-篩選的網站 URI** &ndash;意圖篩選器如何處理在行動裝置瀏覽器 URL，請按一下引導 Android。
2. **發行*數位資產連結 JSON*網站上的檔案**&ndash;這是檔案上傳至網站並由 Android 用來驗證行動裝置應用程式和網站的網域之間的關聯性。 未這麼做，Android 無法安裝應用程式做為預設的控制代碼的 URI。使用者必須手動執行。

<a name="configure-intent-filter" />

### <a name="configuring-the-intent-filter"></a>設定意圖篩選器

若要設定之網站的 URI （或可能的 Uri 集合） 會對應至 Android 應用程式中的活動意圖篩選條件必須。 Xamarin.Android，在此關聯性藉由在裝飾的活動[IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)。 意圖篩選必須宣告下列資訊：

* **`Intent.ActionView`** &ndash; 這會註冊意圖篩選，以回應要求，以檢視資訊
* **`Categories`** &ndash;  意圖篩選應該註冊兩者**[Intent.CategoryBrowsable](https://developer.xamarin.com/api/field/Android.Content.Intent.CategoryBrowsable/)** 和**[Intent.CategoryDefault](https://developer.xamarin.com/api/field/Android.Content.Intent.CategoryDefault/)** 能夠正確處理網頁 URI。
* **`DataScheme`** &ndash; 必須宣告意圖篩選`http`及/或`https`。 這些是只有兩個有效的配置。
* **`DataHost`** &ndash; 這是 Uri 源自的網域。
* **`DataPathPrefix`** &ndash; 這是選擇性的網站上的資源路徑。
* **`AutoVerify`** &ndash; `autoVerify`屬性會告知 Android 以確認應用程式和網站之間的關聯性。 這將會討論更下方。

下列範例示範如何使用[IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)來處理來自連結`https://www.recipe-app.com/recipes`來回`http://www.recipe-app.com/recipes`:

```csharp
[IntentFilter(new [] { Intent.ActionView },
              Categories = new[] { Intent.CategoryBrowsable, Intent.CategoryDefault },
              DataScheme = "http",
              DataHost = "recipe-app.com",
              DataPathPrefix = "/recipe",
              AutoVerify=true)]
public class RecipeActivity : Activity
{
    // Code for the activity omitted
}
```

Android 將會驗證由之前的預設處理常式註冊應用程式 uri 的意圖篩選來針對數位資產檔案在網站上的每個主控件。 Android 可以建立應用程式的預設處理常式之前，所有意圖的篩選條件必須通過驗證。

### <a name="creating-the-digital-assets-link-file"></a>建立數位資產的連結檔案

Android 6.0 應用程式連結需要 Android 確認應用程式和網站之間的關聯，才能設定為預設處理常式的應用程式 uri。 第一次安裝應用程式時，會發生這項驗證。 *數位資產連結*檔案是由相關 webdomain(s) 的 JSON 檔案。

> [!NOTE]
> `android:autoVerify`意圖篩選必須設定此屬性&ndash;否則 Android 將不會執行驗證。

檔案放置位置的網域管理員**https://domain/.well-known/assetlinks.json**。

數位資產檔案包含所需之中繼資料以確認關聯的 android。 **Assetlinks.json**檔案具有下列索引鍵-值組：

* `namespace` &ndash; Android 應用程式的命名空間。
* `package_name` &ndash; （在應用程式資訊清單中宣告） 的 Android 應用程式封裝名稱。
* `sha256_cert_fingerprints` &ndash; 已簽署的應用程式 SHA256 fingerprints。 請參閱本指南[尋找金鑰存放區的 MD5 或 SHA1 簽章](~/android/deploy-test/signing/keystore-signature.md)如需有關如何取得應用程式的 SHA1 指紋。

下列程式碼片段是範例**assetlinks.json**與單一的應用程式列：

```json
[
   {
      "relation":[
         "delegate_permission/common.handle_all_urls"
      ],
      "target":{
         "namespace":"android_app",
         "package_name":"com.example",
         "sha256_cert_fingerprints":[
            "14:6D:E9:83:C5:73:06:50:D8:EE:B9:95:2F:34:FC:64:16:A0:83:42:E6:1D:BE:A8:8A:04:96:B2:3F:CF:44:E5"
         ]
      }
   }
]
```

它可註冊為支援不同版本的多個 SHA256 指紋，或您的應用程式的組建。 這個下一步 **assetlinks.json**檔案是註冊多個應用程式的範例：

```json
[
   {
      "relation":[
         "delegate_permission/common.handle_all_urls"
      ],
      "target":{
         "namespace":"android_app",
         "package_name":"example.com.puppies.app",
         "sha256_cert_fingerprints":[
            "14:6D:E9:83:C5:73:06:50:D8:EE:B9:95:2F:34:FC:64:16:A0:83:42:E6:1D:BE:A8:8A:04:96:B2:3F:CF:44:E5"
         ]
      }
   },
   {
      "relation":[
         "delegate_permission/common.handle_all_urls"
      ],
      "target":{
         "namespace":"android_app",
         "package_name":"example.com.monkeys.app",
         "sha256_cert_fingerprints":[
            "14:6D:E9:83:C5:73:06:50:D8:EE:B9:95:2F:34:FC:64:16:A0:83:42:E6:1D:BE:A8:8A:04:96:B2:3F:CF:44:E5"
         ]
      }
   }
]
```

[Google 數位資產連結網站](https://developers.google.com/digital-asset-links/tools/generator)具有線上工具有助於建立及測試數位資產檔案。

### <a name="testing-app-links"></a>測試應用程式連結

在實作之後的應用程式連結，您應該測試不同部分，以確定其如預期般運作。

您可確認數位資產檔案是正確格式，而裝載使用 Google 的數位資產連結應用程式開發介面，在此範例所示：

```html
https://digitalassetlinks.googleapis.com/v1/statements:list?source.web.site=
  https://<WEB SITE ADDRESS>:&relation=delegate_permission/common.handle_all_urls
```

有兩項測試，可以執行，以確保已正確設定意圖篩選器和應用程式設定為預設處理常式，uri:

1.  正確裝載數位資產檔案，如上面所述。 第一項測試會發送的 Android 應重新導向至行動應用程式意圖。 Android 應用程式應該啟動，並顯示 「 url 註冊 」 活動。 在命令提示字元中輸入：

    ```shell
    $ adb shell am start -a android.intent.action.VIEW \
        -c android.intent.category.BROWSABLE \
        -d "http://<domain1>/recipe/scalloped-potato"
    ```

2.  顯示處理給定的裝置上安裝應用程式原則的現有連結。 下列命令會傾印連結原則具有下列資訊的裝置上每位使用者的清單。 在命令提示字元中輸入下列命令：

    ```shell
    $ adb shell dumpsys package domain-preferred-apps
    ```

    * **`Package`** &ndash; 封裝應用程式的名稱。
    * **`Domain`** &ndash; 網域 （以空格分隔） 的應用程式會處理其網頁連結
    * **`Status`** &ndash; 這是應用程式的目前連結處理狀態。 值為**一律**表示應用程式有`android:autoVerify=true`宣告，並已通過系統驗證。 它後面的十六進位數字，代表 Android 的系統記錄的喜好設定。

    例如: 

    ```shell
    $ adb shell dumpsys package domain-preferred-apps

    App linkages for user 0:
    Package: com.android.vending
    Domains: play.google.com market.android.com
    Status: always : 200000002
    ```

## <a name="summary"></a>總結

本指南將討論如何將應用程式連結 Android 6.0 的運作方式。 然後，它涵蓋如何設定 Android 6.0 應用程式支援，並回應至應用程式的連結。 它也將討論如何測試 Android 應用程式中的應用程式連結。


## <a name="related-links"></a>相關連結

- [尋找您的金鑰存放區 MD5 或 SHA1 簽章](~/android/deploy-test/signing/keystore-signature.md)
- [活動和對應方式](https://university.xamarin.com/classes#4)
- [AppLinks](http://applinks.org/)
- [Google 數位資產的連結](https://developers.google.com/digital-asset-links/)
- [陳述式清單產生器和測試人員](https://developers.google.com/digital-asset-links/tools/generator)
