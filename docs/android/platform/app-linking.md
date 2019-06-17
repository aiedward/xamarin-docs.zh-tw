---
title: 在 Android 應用程式連結
description: 本指南會討論如何 Android 6.0 支援應用程式連結，以回應 url 在網站上的行動裝置應用程式的技術。 它將討論哪些應用程式連結是、 如何實作應用程式連結在 Android 6.0 應用程式，以及如何設定網站要授與權限的網域中的行動應用程式。
ms.prod: xamarin
ms.assetid: 48174E39-19FD-43BC-B54C-9AF11D4B1F91
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: dd4ba236df8e5993c7f7ed86393eb66ce01db595
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60956145"
---
# <a name="app-linking-in-android"></a>在 Android 應用程式連結

_本指南會討論如何 Android 6.0 支援應用程式連結，以回應 url 在網站上的行動裝置應用程式的技術。它將討論哪些應用程式連結是、 如何實作應用程式連結在 Android 6.0 應用程式，以及如何設定網站要授與權限的網域中的行動應用程式。_

## <a name="app-linking-overview"></a>應用程式連結的概觀

行動應用程式不再處於定址接收器&ndash;在許多情況下，它們是重要的業務，以及其網站的元件。 最好的企業，順暢地連線其網站和行動應用程式，以啟動行動應用程式，並在行動裝置應用程式中顯示相關內容的網站上的連結。 *應用程式連結*(也稱為*深層連結*) 是一項技術，可讓行動裝置，以回應的 URI，並啟動行動應用程式對應至該 URI。

Android 會處理透過應用程式連結*意圖系統*&ndash;當使用者按一下連結，以在行動瀏覽器中，於行動瀏覽器會分派 Android 會委派給已註冊的應用程式意圖。 例如，按一下烹飪的網站上的連結會開啟與該網站相關聯的行動應用程式並向使用者顯示特定配方。 如果有多個應用程式已註冊要處理該意圖，則 Android 將會引發所謂*去除混淆對話方塊*，會要求使用者来選取的應用程式應該處理的目的，應用程式範例：

![去除混淆對話方塊的範例螢幕擷取畫面](app-linking-images/01-disambiguation-dialog.png)

Android 6.0 改善這種使用自動連結處理。 適用於 Android uri 自動註冊為預設處理常式的應用程式可能&ndash;應用程式會自動啟動，並直接瀏覽至相關的活動。 Android 6.0 如何決定來處理 URI 按一下取決於下列條件：

1. **現有的應用程式 je již přiřazen URI** &ndash;使用者可能有已關聯的現有應用程式的 uri。 在此情況下，Android 會繼續使用該應用程式。
2. **沒有任何現有的應用程式相關聯的 URI，但支援的應用程式安裝**&ndash;在此案例中，使用者尚未指定現有的應用程式，因此 Android 會使用已安裝支援的應用程式來處理要求。
3. **沒有任何現有的應用程式相關聯的 URI，但許多支援的應用程式會安裝**&ndash;因為有多個應用程式支援的 URI，去除混淆對話方塊將會顯示，而且使用者必須選取哪一個應用程式將會處理的 URI。

如果使用者有無安裝支援的應用程式的 URI，且後續安裝其中一個，Android 會將該應用程式做為預設處理常式 uri 之後驗證 URI 相關聯的網站與相關聯的。

本指南會討論如何設定 Android 6.0 應用程式，以及如何建立及發佈以支援應用程式連結在 Android 6.0 中的數位資產的連結檔案。

## <a name="requirements"></a>需求

本指南需要 Xamarin.Android 6.1 和目標 Android 6.0 （API 層級 23） 的應用程式或更高版本。

應用程式連結是在舊版的 Android 中使用[Rivets NuGet 套件](https://www.nuget.org/packages/Rivets/)從 Xamarin 元件存放區。 鉚釘套件不相容應用程式連結在 Android 6.0;它不支援 Android 6.0 應用程式連結。

## <a name="configuring-app-linking-in-android-60"></a>設定 Android 6.0 中的 應用程式連結

設定 Android 6.0 中的應用程式連結，牽涉到兩個主要步驟：

1. **新增一或多個意圖篩選網站 URI** &ndash;意圖篩選器如何處理在行動瀏覽器 URL，請按一下引導 Android。
2. **發佈*數位資產連結 JSON*網站上的檔案**&ndash;這是檔案上傳至網站並由 Android 用來確認行動裝置應用程式和網站的網域之間的關聯性。 如果沒有這麼做，Android 無法安裝應用程式作為預設控點的 URI;使用者必須手動進行。

<a name="configure-intent-filter" />

### <a name="configuring-the-intent-filter"></a>設定意圖篩選

必須設定對應至 Android 應用程式中活動的 從網站的 URI （或可能的 Uri 集合） 的意圖篩選器。 在 Xamarin.Android 中，所裝飾的活動建立這種關係[IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)。 意圖篩選必須宣告下列資訊：

* **`Intent.ActionView`** &ndash; 這會註冊意圖篩選，以回應要求，若要檢視資訊
* **`Categories`** &ndash;  意圖篩選應該註冊兩個 **[Intent.CategoryBrowsable](https://developer.xamarin.com/api/field/Android.Content.Intent.CategoryBrowsable/)** 並 **[Intent.CategoryDefault](https://developer.xamarin.com/api/field/Android.Content.Intent.CategoryDefault/)** 能夠正確處理網頁的 URI。
* **`DataScheme`** &ndash; 意圖篩選必須宣告`http`及/或`https`。 這些是只有兩個有效的配置。
* **`DataHost`** &ndash; 這是 Uri 會從產生的網域。
* **`DataPathPrefix`** &ndash; 這是要在網站上的資源的選擇性路徑。
* **`AutoVerify`** &ndash; `autoVerify`屬性會告知 Android，以確認應用程式和網站之間的關聯性。 這將會討論更下方。

下列範例示範如何使用[IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)處理來自連結`https://www.recipe-app.com/recipes`進出`http://www.recipe-app.com/recipes`:

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

Android 會確認每一個可由 URI 做為預設處理常式註冊應用程式之前的意圖篩選來針對數位資產檔案在網站上的主機。 Android 可以做為預設處理常式中建立應用程式之前，所有意圖篩選條件必須通過驗證。

### <a name="creating-the-digital-assets-link-file"></a>建立數位資產的連結檔案

Android 6.0 應用程式連結需要 Android 確認應用程式和網站之間的關聯，才能設定為預設處理常式的應用程式 uri。 第一次安裝應用程式時，會發生這項驗證。 *數位資產連結*檔案是由相關 webdomain(s) 裝載的 JSON 檔案。

> [!NOTE]
> `android:autoVerify`意圖篩選必須設定此屬性&ndash;否則 Android 將不會執行驗證。

檔案放置位置之網域的 webmaster **https://domain/.well-known/assetlinks.json**。

數位資產檔案包含適用於 Android 以確認關聯所需之中繼資料。 **Assetlinks.json**檔案具有下列索引鍵 / 值組：

* `namespace` &ndash; Android 應用程式的命名空間。
* `package_name` &ndash; （在 應用程式資訊清單中宣告） 的 Android 應用程式封裝名稱。
* `sha256_cert_fingerprints` &ndash; 已簽署的應用程式 SHA256 fingerprints。 請參閱本指南[尋找金鑰儲存區的 MD5 或 SHA1 簽章](~/android/deploy-test/signing/keystore-signature.md)如需有關如何取得應用程式的 SHA1 指紋。

下列程式碼片段是舉例**assetlinks.json**與單一應用程式列：

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

它可以註冊一個以上的 SHA256 指紋，來支援不同版本，或您的應用程式的組建。 這個下一步**assetlinks.json**檔案是註冊多個應用程式的範例：

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

[Google 數位資產連結網站](https://developers.google.com/digital-asset-links/tools/generator)具有線上工具可協助建立和測試的數位資產檔案。

### <a name="testing-app-links"></a>測試應用程式連結

在實作之後的應用程式連結，您應該測試各項技術，以確保其如預期般運作。

您可確認數位資產檔案是正確格式化並裝載使用 Google 的數位資產連結 API，在此範例中所示：

```html
https://digitalassetlinks.googleapis.com/v1/statements:list?source.web.site=
  https://<WEB SITE ADDRESS>:&relation=delegate_permission/common.handle_all_urls
```

有兩項測試，可以執行，以確保已正確設定意圖篩選器和應用程式設定為預設處理常式，uri:

1.  如上面所述正確裝載數位資產檔案。 第一項測試會分派 Android 應重新導向至行動應用程式的意圖。 Android 應用程式應該啟動，並顯示 註冊 url 的活動。 在命令提示字元中鍵入：

    ```shell
    $ adb shell am start -a android.intent.action.VIEW \
        -c android.intent.category.BROWSABLE \
        -d "http://<domain1>/recipe/scalloped-potato"
    ```

2.  顯示處理指定的裝置上安裝的應用程式原則的現有連結。 下列命令會傾印每個使用者在裝置上使用下列資訊的連結原則的清單中。 在命令提示字元中輸入下列命令：

    ```shell
    $ adb shell dumpsys package domain-preferred-apps
    ```

    * **`Package`** &ndash; 應用程式封裝名稱。
    * **`Domain`** &ndash; 其網頁連結交由應用程式定義域 （以空格分隔）
    * **`Status`** &ndash; 這是應用程式的目前連結處理狀態。 值為**一律**意謂著應用程式有`android:autoVerify=true`宣告，並已通過系統驗證。 它被後面接著十六進位數字表示的喜好設定的 Android 系統的記錄。

    例如: 

    ```shell
    $ adb shell dumpsys package domain-preferred-apps

    App linkages for user 0:
    Package: com.android.vending
    Domains: play.google.com market.android.com
    Status: always : 200000002
    ```

## <a name="summary"></a>總結

本指南會討論如何將應用程式連結在 Android 6.0 中的運作方式。 然後，它會涵蓋如何設定 Android 6.0 應用程式支援和回應的應用程式連結。 它也會討論如何測試應用程式連結的 Android 應用程式中。


## <a name="related-links"></a>相關連結

- [尋找金鑰儲存區的 MD5 或 SHA1 簽章](~/android/deploy-test/signing/keystore-signature.md)
- [活動和意圖](https://university.xamarin.com/classes#4)
- [AppLinks](http://applinks.org/)
- [Google 數位資產的連結](https://developers.google.com/digital-asset-links/)
- [陳述式清單產生器和測試人員](https://developers.google.com/digital-asset-links/tools/generator)
