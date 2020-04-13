---
title: 安卓中的應用程式連結
description: 本指南將討論 Android 6.0 如何支援應用連結,該技術允許行動應用回應網站上的 URL。 它將討論什麼是應用連結,如何在 Android 6.0 應用程式中實現應用連結,以及如何配置網站以授予域移動應用的許可權。
ms.prod: xamarin
ms.assetid: 48174E39-19FD-43BC-B54C-9AF11D4B1F91
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: af90c286d2bb960a9f78547dd15c3d98a69529ae
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "75487824"
---
# <a name="app-linking-in-android"></a>安卓中的應用程式連結

_本指南將討論 Android 6.0 如何支援應用連結,該技術允許行動應用回應網站上的 URL。它將討論什麼是應用連結,如何在 Android 6.0 應用程式中實現應用連結,以及如何配置網站以授予域移動應用的許可權。_

## <a name="app-linking-overview"></a>套用連結概述

移動應用程式在許多情況下不再位於孤島&ndash;中,它們及其網站是其業務的重要組成部分。 企業最好將其 Web 狀態和行動應用程式無縫連接,並在啟動行動應用程式的網站上連結,並在行動應用程式中顯示相關內容。 *應用程式連結*(也稱為*深度連結*)是允許行動裝置回應URI並啟動對應於該URI的行動應用程式的一種技術。

當用戶單擊移動瀏覽器中的連結時,Android 會通過*意圖系統*&ndash;處理應用連結,移動瀏覽器將發送 Android 將委派給註冊應用程式的意圖。 例如,單擊烹飪網站上的連結將打開與該網站關聯的移動應用程式,並向用戶顯示特定配方。 如果註冊了多個應用程式來處理該意圖,則 Android 將引發所謂的*消除歧義對話方塊*,該對話方塊將詢問使用者選擇應處理該意圖的應用程式,例如:

![清除歧義對話框的範例螢幕擷取](app-linking-images/01-disambiguation-dialog.png)

Android 6.0 通過使用自動連結處理改進了這方面的功能。 Android 可以自動將應用程式註冊為&ndash;URI的預設處理程式,應用程式將自動啟動並直接導航到相關活動。 Android 6.0 決定如何處理 URI 單擊取決於以下條件:

1. **現有應用已與URI**&ndash;關聯使用者可能已經將現有應用與URI關聯。 在這種情況下,Android 將繼續使用該應用程式。
2. **沒有現有應用與 URI 關聯,但安裝了**&ndash;支援應用在這種情況下,使用者尚未指定現有應用,因此 Android 將使用已安裝的支援應用程式來處理請求。
3. **沒有現有應用與URI關聯,但安裝了許多支援應用**&ndash;因為有多個應用程式支援URI,將顯示消除歧義對話框,並且使用者必須選擇哪個應用將處理URI。

如果用戶沒有安裝支援URI的應用,並且隨後安裝了一個應用,則Android將在驗證與與URI關聯的網站關聯後,將該應用程式設置為URI的默認處理程式。

本指南將討論如何配置 Android 6.0 應用程式以及如何創建和發佈數位資產連結檔以支援 Android 6.0 中的應用程式連結。

## <a name="requirements"></a>需求

本指南需要 Xamarin.Android 6.1 和針對 Android 6.0(API 級別 23)或更高版本的應用程式。

使用 Xamarin 元件儲存的[Rivets NuGet 套件](https://www.nuget.org/packages/Rivets/),在早期版本的 Android 中可以進行應用程式連結。 鉚釘包與 Android 6.0 中的應用程式連結不相容;它不支援 Android 6.0 應用程式連結。

## <a name="configuring-app-linking-in-android-60"></a>在 Android 6.0 中設定應用程式連結

在 Android 6.0 中設置應用連結涉及兩個主要步驟:

1. 為網站URI&ndash;的意圖**篩選器添加一個或多個意圖篩選器**,可指導 Android 如何處理移動瀏覽器中的 URL 點擊。
2. **在網站上發佈*Digital Asset Links JSON***&ndash;數位資產連結 JSON 檔,這是一個上載到網站的檔,由 Android 用於驗證行動應用與網站域之間的關係。 如果沒有這一點,Android 無法將應用安裝為 URI 的預設句柄;用戶必須手動執行此操作。

<a name="configure-intent-filter" />

### <a name="configuring-the-intent-filter"></a>設定意向篩選器

有必要配置一個意圖篩選器,該篩選器將 URI(或可能一組 URI)從網站映射到 Android 應用程式中的活動。 在 Xamarin.Android 中,這種關係是通過使用[意向篩選器屬性](xref:Android.App.IntentFilterAttribute)修飾活動而建立的。 意圖篩選器必須聲明以下資訊:

- **`Intent.ActionView`**&ndash;這將註冊意向篩選器以回應檢視資訊的請求
- **`Categories`**&ndash;意圖篩選器應同時註冊**[意向.類別可流覽和](xref:Android.Content.Intent.CategoryBrowsable)****[意圖。](xref:Android.Content.Intent.CategoryDefault)**
- **`DataScheme`**&ndash;意圖篩選器必須宣告`http`與/`https`或 。 這是僅有的兩個有效方案。
- **`DataHost`**&ndash;這是 URI 將源自的域。
- **`DataPathPrefix`**&ndash;這是網站上資源的可選路徑。
- **`AutoVerify`**&ndash;該`autoVerify`屬性告訴 Android 驗證應用程式與網站之間的關係。 下文將對此進行更多討論。

下面的範例展示如何使用[此元件屬性](xref:Android.App.IntentFilterAttribute)來處理來自與的連結`https://www.recipe-app.com/recipes``http://www.recipe-app.com/recipes`:

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

Android將在將應用程式註冊為URI的預設處理程式之前,根據網站上的數位資產檔驗證意圖篩選器標識的每個主機。 在 Android 將應用建立為預設處理程式之前,所有意圖篩選器都必須通過驗證。

### <a name="creating-the-digital-assets-link-file"></a>建立數字資產連結檔案

Android 6.0 應用連結要求 Android 在將應用程式設置為 URI 的預設處理程式之前,必須驗證應用程式與網站之間的關聯。 首次安裝應用程式時,將發生此驗證。 *數位資產連結*文件是由相關 Web 網域託管的 JSON 檔。

> [!NOTE]
> 屬性`android:autoVerify`必須由意圖篩選&ndash;器 設置,否則 Android 不會執行驗證。

此檔案由網域的網站站長放置在該**https://domain/.well-known/assetlinks.json**位置 。

數位資產檔包含 Android 驗證關聯所需的中繼資料。 **assetlinks.json**檔具有以下鍵值對:

- `namespace`&ndash; Android 應用程式的名稱空間。
- `package_name`&ndash; Android 應用程式的包名稱(在應用程式清單中聲明)。
- `sha256_cert_fingerprints`&ndash;已簽名應用程式的 SHA256 指紋。 有關如何取得應用程式的 SHA1 指紋的詳細資訊,請參閱[指南「尋找金鑰庫的 MD5」或 SHA1 簽署](~/android/deploy-test/signing/keystore-signature.md)。

以下代碼段是列出單個應用程式**的資產連結.json**的範例:

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

可以註冊多個 SHA256 指紋以支援應用程式的不同版本或版本。 下一個**資產連結.json**檔案是註冊多個應用程式的範例:

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

[谷歌數字資產鏈接網站](https://developers.google.com/digital-asset-links/tools/generator)有一個在線工具,可以幫助創建和測試數字資產檔。

### <a name="testing-app-links"></a>測試套用連結

實現應用連結后,應測試各個部分,以確保它們按預期工作。

可以確認數位資產檔是否透過使用 Google 的數位資產連結 API 進行了正確格式化和託管,如以下範例所示:

```html
https://digitalassetlinks.googleapis.com/v1/statements:list?source.web.site=
  https://<WEB SITE ADDRESS>:&relation=delegate_permission/common.handle_all_urls
```

可以執行兩個測試,以確保已正確配置意圖篩選器,並確保應用設定為 URI 的預設處理程式:

1. 數位資產檔已正確託管,如上所述。 第一個測試將發送一個意圖,Android 應重定向到移動應用程式。 Android 應用程式應啟動並顯示為 URL 註冊的活動。 在指令提示型態下:

    ```shell
    $ adb shell am start -a android.intent.action.VIEW \
        -c android.intent.category.BROWSABLE \
        -d "http://<domain1>/recipe/scalloped-potato"
    ```

2. 顯示給定設備上安裝的應用程式的現有鏈路處理策略。 以下命令將轉儲設備上每個使用者的連結策略清單,並包含以下資訊。 在命令提示字元中，輸入下列命令：

    ```shell
    $ adb shell dumpsys package domain-preferred-apps
    ```

    - **`Package`**&ndash;應用程式的包名稱。
    - **`Domain`**&ndash;其 Web 連結將由應用程式處理的網域(由空格分隔)
    - **`Status`**&ndash;這是應用的當前連結處理狀態。 值**始終**表示應用程式`android:autoVerify=true`已聲明並通過了系統驗證。 它後面跟著一個十六進位數位,表示Android系統的偏好記錄。

    例如：

    ```shell
    $ adb shell dumpsys package domain-preferred-apps

    App linkages for user 0:
    Package: com.android.vending
    Domains: play.google.com market.android.com
    Status: always : 200000002
    ```

## <a name="summary"></a>總結

本指南討論了應用連結在Android 6.0中的工作原理。 然後,它介紹了如何配置 Android 6.0 應用程式以支援和回應應用連結。 它還討論了如何在 Android 應用程式中測試應用連結。

## <a name="related-links"></a>相關連結

- [尋找金鑰儲存區的 MD5 或 SHA1 簽章](~/android/deploy-test/signing/keystore-signature.md)
- [套用連結](https://developers.facebook.com/docs/applinks)
- [谷歌數位資產連結](https://developers.google.com/digital-asset-links/)
- [語句清單產生器與測試器](https://developers.google.com/digital-asset-links/tools/generator)
