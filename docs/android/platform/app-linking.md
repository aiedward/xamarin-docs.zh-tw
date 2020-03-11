---
title: Android 中的應用程式連結
description: 本指南將討論 Android 6.0 如何支援應用程式連結，這項技術可讓行動應用程式回應網站上的 Url。 其中將討論什麼是應用程式連結、如何在 Android 6.0 應用程式中執行應用程式連結，以及如何設定網站將許可權授與網域的行動應用程式。
ms.prod: xamarin
ms.assetid: 48174E39-19FD-43BC-B54C-9AF11D4B1F91
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: af90c286d2bb960a9f78547dd15c3d98a69529ae
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "75487824"
---
# <a name="app-linking-in-android"></a>Android 中的應用程式連結

_本指南將討論 Android 6.0 如何支援應用程式連結，這項技術可讓行動應用程式回應網站上的 Url。其中將討論什麼是應用程式連結、如何在 Android 6.0 應用程式中執行應用程式連結，以及如何設定網站將許可權授與網域的行動應用程式。_

## <a name="app-linking-overview"></a>應用程式連結總覽

行動應用程式不再存在於定址接收器 &ndash; 在許多情況下，它們是其企業的重要元件及其網站。 企業可以順暢地連接其 web 目前狀態和行動應用程式，並提供網站上的連結來啟動行動應用程式，並在行動應用程式中顯示相關的內容。 *應用程式連結*（也稱為*深層連結*）是一種技術，可讓行動裝置回應 uri 並啟動與該 uri 對應的行動應用程式。

Android 會透過*意圖系統*&ndash; 處理應用程式連結，當使用者按一下行動瀏覽器中的連結時，行動瀏覽器會分派 Android 將委派給已註冊應用程式的意圖。 例如，按一下烹飪網站上的連結，會開啟與該網站相關聯的行動應用程式，並向使用者顯示特定配方。 如果註冊了多個應用程式來處理該意圖，則 Android 會引發所謂的去除混淆*對話方塊*，詢問使用者哪個應用程式要選取應處理意圖的應用程式，例如：

![消除混淆對話方塊的範例螢幕擷取畫面](app-linking-images/01-disambiguation-dialog.png)

Android 6.0 使用自動連結處理來改善這項操作。 Android 可以自動將應用程式註冊為 URI 的預設處理常式 &ndash; 應用程式會自動啟動，並直接流覽至相關的活動。 Android 6.0 決定如何處理 URI 按一下的方式取決於下列準則：

1. **現有的應用程式已與 uri 相關聯**&ndash; 使用者可能已將現有的應用程式與 uri 相關聯。 在此情況下，Android 會繼續使用該應用程式。
2. **沒有任何現有的應用程式與 URI 相關聯，但支援的應用程式已安裝**&ndash; 在此案例中，使用者尚未指定現有的應用程式，因此 Android 會使用已安裝的支援應用程式來處理要求。
3. **沒有任何現有的應用程式與 uri 相關聯，但有許多**支援的應用程式已安裝 &ndash; 因為有多個應用程式支援 URI，將會顯示去除混淆對話方塊，使用者必須選取哪個應用程式會處理 uri。

如果使用者沒有安裝支援 URI 的應用程式，且後續安裝了該應用程式，則 Android 會在確認與與 URI 相關聯的網站關聯之後，將該應用程式設定為 URI 的預設處理常式。

本指南將討論如何設定 Android 6.0 應用程式，以及如何建立和發佈數位資產連結檔案，以支援 Android 6.0 中的應用程式連結。

## <a name="requirements"></a>需求

本指南需要 Xamarin. Android 6.1 和以 Android 6.0 （API 層級23）或更高版本為目標的應用程式。

您可以使用 Xamarin 元件存放區中的[鉚釘 NuGet 套件](https://www.nuget.org/packages/Rivets/)，在舊版 Android 中進行應用程式連結。 鉚釘套件與 Android 6.0 中的應用程式連結不相容;它不支援 Android 6.0 應用程式連結。

## <a name="configuring-app-linking-in-android-60"></a>在 Android 6.0 中設定應用程式連結

在 Android 6.0 中設定應用程式連結會包含兩個主要步驟：

1. 為**網站 URI 新增一或多個意圖篩選器**&ndash; 在行動瀏覽器中，于 [如何處理 URL] 中按一下 [意圖篩選指南 Android]。
2. 在**網站上發佈*數位資產連結 JSON*** 檔案 &ndash; 這是上傳至網站的檔案，且由 Android 用來驗證行動應用程式與網站網域之間的關聯性。 若沒有這種情況，Android 就無法將應用程式安裝為 URI 的預設控制碼;使用者必須手動執行此動作。

<a name="configure-intent-filter" />

### <a name="configuring-the-intent-filter"></a>設定意圖篩選

您必須設定意圖篩選，以將 URI （或可能的一組 Uri）從網站對應至 Android 應用程式中的活動。 在 Xamarin 中，此關聯性是藉由使用[IntentFilterAttribute](xref:Android.App.IntentFilterAttribute)來裝飾活動來建立。 意圖篩選準則必須宣告下列資訊：

- **`Intent.ActionView`** &ndash; 這會註冊意圖篩選來回應 view 資訊的要求
- **`Categories`** &ndash; 意圖篩選器應該註冊 **[CategoryBrowsable](xref:Android.Content.Intent.CategoryBrowsable)** 和 **[CategoryDefault](xref:Android.Content.Intent.CategoryDefault)** ，才能正確處理 web URI。
- **`DataScheme`** &ndash; 意圖篩選準則必須宣告 `http` 和/或 `https`。 這些是唯一的兩個有效配置。
- **`DataHost`** &ndash; 這是 uri 將源自的網域。
- **`DataPathPrefix`** &ndash; 這是網站上資源的選擇性路徑。
- **`AutoVerify`** &ndash; `autoVerify` 屬性會告知 Android 驗證應用程式與網站之間的關聯性。 下面將詳細討論這一點。

下列範例顯示如何使用[IntentFilterAttribute](xref:Android.App.IntentFilterAttribute)來處理來自 `https://www.recipe-app.com/recipes` 和 `http://www.recipe-app.com/recipes`的連結：

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

Android 會在將應用程式註冊為 URI 的預設處理常式之前，驗證意圖篩選器針對網站上的數位資產檔案所識別的每一部主機。 所有意圖篩選準則都必須通過驗證，Android 才能將應用程式建立為預設處理常式。

### <a name="creating-the-digital-assets-link-file"></a>建立數位資產連結檔案

Android 6.0 應用程式連結需要 Android 先確認應用程式與網站之間的關聯，再將應用程式設定為 URI 的預設處理常式。 第一次安裝應用程式時，將會進行此驗證。 *數位資產連結*檔案是由相關 webdomain 所裝載的 JSON 檔案。

> [!NOTE]
> `android:autoVerify` 屬性必須由意圖篩選設定 &ndash; 否則 Android 將不會執行驗證。

此檔案是由網域的網站管理員（位於 **https://domain/.well-known/assetlinks.json** 位置）所放置。

數位資產檔案包含 Android 用來驗證關聯所需的中繼資料。 **Assetlinks json**檔案具有下列索引鍵/值組：

- `namespace` &ndash; Android 應用程式的命名空間。
- `package_name` &ndash; Android 應用程式的套件名稱（在應用程式資訊清單中宣告）。
- `sha256_cert_fingerprints` &ndash; 已簽署應用程式的 SHA256 指紋。 如需如何取得應用程式 SHA1 指紋的詳細資訊，請參閱[尋找金鑰儲存區的 MD5 或 SHA1](~/android/deploy-test/signing/keystore-signature.md)簽章指南。

下列程式碼片段是**assetlinks**的範例，其中列出單一應用程式：

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

您可以註冊一個以上的 SHA256 指紋，以支援應用程式的不同版本或組建。 下一個**assetlinks json**檔案是註冊多個應用程式的範例：

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

[Google 數位資產連結網站](https://developers.google.com/digital-asset-links/tools/generator)有一種線上工具，可協助建立和測試數位資產檔案。

### <a name="testing-app-links"></a>測試應用程式連結

在執行應用程式連結之後，應該測試各種不同的部分，以確保它們能如預期般運作。

您可以使用 Google 的數位資產連結 API 來確認數位資產檔案已正確格式化並裝載，如下列範例所示：

```html
https://digitalassetlinks.googleapis.com/v1/statements:list?source.web.site=
  https://<WEB SITE ADDRESS>:&relation=delegate_permission/common.handle_all_urls
```

有兩個可以執行的測試，以確保意圖篩選已正確設定，且應用程式已設定為 URI 的預設處理常式：

1. 數位資產檔案已正確裝載，如上所述。 第一次測試會分派 Android 應重新導向至行動應用程式的意圖。 Android 應用程式應該會啟動，並顯示為 URL 註冊的活動。 在命令提示字元中，輸入：

    ```shell
    $ adb shell am start -a android.intent.action.VIEW \
        -c android.intent.category.BROWSABLE \
        -d "http://<domain1>/recipe/scalloped-potato"
    ```

2. 針對安裝在指定裝置上的應用程式顯示現有的連結處理原則。 下列命令會使用下列資訊，傾印裝置上每個使用者的連結原則清單。 在命令提示字元中，輸入下列命令：

    ```shell
    $ adb shell dumpsys package domain-preferred-apps
    ```

    - **`Package`** &ndash; 應用程式的封裝名稱。
    - **`Domain`** &ndash; 應用程式將會處理其 Web 連結的網域（以空格分隔）
    - **`Status`** &ndash; 這是應用程式目前的連結處理狀態。 的值**一律**表示應用程式已宣告 `android:autoVerify=true`，且已通過系統驗證。 後面接著一個十六進位數位，代表 Android 系統的喜好設定記錄。

    例如：

    ```shell
    $ adb shell dumpsys package domain-preferred-apps

    App linkages for user 0:
    Package: com.android.vending
    Domains: play.google.com market.android.com
    Status: always : 200000002
    ```

## <a name="summary"></a>摘要

本指南討論應用程式連結在 Android 6.0 中的運作方式。 接著會涵蓋如何設定 Android 6.0 應用程式來支援和回應應用程式連結。 同時也會討論如何在 Android 應用程式中測試應用程式連結。

## <a name="related-links"></a>相關連結

- [尋找金鑰儲存區的 MD5 或 SHA1 簽章](~/android/deploy-test/signing/keystore-signature.md)
- [AppLinks](https://developers.facebook.com/docs/applinks)
- [Google 數位資產連結](https://developers.google.com/digital-asset-links/)
- [語句清單產生器和測試器](https://developers.google.com/digital-asset-links/tools/generator)
