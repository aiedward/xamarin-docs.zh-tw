---
title: "手動上傳 APK"
ms.topic: article
ms.prod: xamarin
ms.assetid: 1309C251-ABF0-4412-B1F5-200DC8321A9D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: c09dcefb97a5edafcd03394e5ae3146b69a40745
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="manually-uploading-the-apk"></a>手動上傳 APK

<a name="Uploading_the_APK" />

第一次將 APK 提交給 Google Play 時 (或如果使用舊版 Xamarin.Android)，必須透過 [Google Play 開發人員控制台](https://play.google.com/apps/publish)手動上傳 APK。 本指南將說明此程序所需的步驟。 

<a name="devconsole" />

## <a name="google-play-developer-console"></a>Google Play 開發人員控制台

編譯完 APK 並備妥宣傳資產之後，必須將應用程式上傳到 Google Play。 做法是登入 [Google Play 開發人員控制台](https://play.google.com/apps/publish)(參見下圖)。 按一下 [在 Google Play 上發佈 Android 應用程式] 按鈕，以起始散發應用程式的程序。

[ ![Google Play 開發人員控制台](manually-uploading-the-apk-images/00-google-play-developer-console-sml.png)](manually-uploading-the-apk-images/00-google-play-developer-console.png)

如果您已經有向 Google Play 註冊的現有應用程式，請按一下 [新增應用程式] 按鈕：

[ ![[新增應用程式] 按鈕](manually-uploading-the-apk-images/01-existing-app-sml.png)](manually-uploading-the-apk-images/01-existing-app.png)

顯示 [新增應用程式] 對話方塊時，輸入應用程式的名稱，然後按一下 [上傳 APK]：

[ ![[上傳 APK] 按鈕](manually-uploading-the-apk-images/02-add-new-application-sml.png)](manually-uploading-the-apk-images/02-add-new-application.png)

下一個畫面會允許發行 Alpha 測試階段、Beta 測試階段或正式發佈階段的應用程式。 在以下範例中，已選取 [Alpha 測試階段] 索引標籤。 由於 **MyApp** 並不使用授權服務，因此針對此範例並不需要按一下 [取得授權金鑰] 按鈕。 在這裡會按一下 [將您的第一個 APK 上傳到 Alpha 測試階段] 按鈕來發行至 Alpha 頻道：

[ ![[將您的第一個 APK 上傳到 Alpha 測試階段] 按鈕](manually-uploading-the-apk-images/03-upload-to-alpha-sml.png)](manually-uploading-the-apk-images/03-upload-to-alpha.png)

[將新 APK 上傳到 ALPHA 測試階段] 對話方塊隨即顯示。 您可以按一下 [瀏覽檔案] 按鈕或拖放 APK 來上傳 APK： 

[ ![[將新 APK 上傳到 ALPHA 測試階段] 對話方塊](manually-uploading-the-apk-images/04-upload-dialog-sml.png)](manually-uploading-the-apk-images/04-upload-dialog.png)

請務必上傳已準備好要發行並進行散發的 APK。
下一個對話方塊會指出 APK 的上傳進度：

[ ![上傳進度指示](manually-uploading-the-apk-images/05-upload-progress-sml.png)](manually-uploading-the-apk-images/05-upload-progress.png)

上傳 APK 之後，即可選取測試方法：

[ ![[選擇測試方法] 對話方塊](manually-uploading-the-apk-images/06-select-testing-method-sml.png)](manually-uploading-the-apk-images/06-select-testing-method.png)

如需有關應用程式測試的詳細資訊，請參閱[設定 Alpha/Beta 版測試環境](https://support.google.com/googleplay/android-developer/answer/3131213?hl=en)指南。

上傳 APK 之後，APK 會儲存為草稿。 必須等到提供更多詳細資料給 Google Play 之後，才能發行 APK，如接下來所述。

<a name="Listing_Details" />

## <a name="store-listing"></a>商店資訊

按一下 **Google Play 開發人員控制台**中的 [商店資訊]，以輸入Google Play 將向可能的應用程式使用者顯示的資訊： 

[ ![[商店資訊] 對話方塊](manually-uploading-the-apk-images/07-store-listing-sml.png)](manually-uploading-the-apk-images/07-store-listing.png)

<a name="Upload_Assets" />

### <a name="graphics-assets"></a>圖片資產

向下捲動至 [商店資訊] 頁面的 [圖片資產] 區段：

[ ![[圖片資產] 區段](manually-uploading-the-apk-images/08-graphic-assets-sml.png)](manually-uploading-the-apk-images/08-graphic-assets.png)

所有先前準備的宣傳資產都會在此區段中上傳。 針對必須提供哪些宣傳資產，以及應該以哪種格式提供，都有提供相關指引。

<a name="categorization" />

### <a name="categorization"></a>分類

在 [圖片資產] 區段之後是 [分類] 區段，請選取應用程式類型和類別：

[ ![[分類] 區段](manually-uploading-the-apk-images/09-categorization-sml.png)](manually-uploading-the-apk-images/09-categorization.png)

下一個區段會涵蓋內容分級。

<a name="contact_details" />

### <a name="contact-details"></a>聯絡詳細資料

此頁面的最後一個區段式 [聯絡詳細資料] 區段。 此區段可用來收集應用程式開發人員的聯絡資訊：

[ ![[聯絡詳細資料] 區段](manually-uploading-the-apk-images/10-contact-details-sml.png)](manually-uploading-the-apk-images/10-contact-details.png)

您可以在 [隱私權政策] 區段提供隱私權政策的 URL，如以上所示。

<a name="content_rating" />

## <a name="content-rating"></a>內容分級

按一下 **Google Play 開發人員控制台**中的 [內容分級]。 在此頁面中，您可以指定應用程式的內容分級。 Google Play 要求所有應用程式都要指定內容分級。 請按一下 [繼續] 按鈕以完成內容分級問卷：

[ ![[內容分級] 區段](manually-uploading-the-apk-images/11-content-rating-sml.png)](manually-uploading-the-apk-images/11-content-rating.png)

Google Play 上的所有應用程式都必須根據 Google Play 分級系統進行分級。 除了內容分級之外，所有應用程式也必須遵守 Google 的[開發人員內容政策](http://www.android.com/us/developer-content-policy.html)。

以下列出 Google Play 分級系統中的四個等級，並提供一些指引作為會要求或強制分級的特徵或內容： 

-   **所有人** &ndash;不可存取、發行或分享位置資料。 不可裝載任何使用者產生的內容。 不可讓使用者之間進行通訊。 

-   **少年級** &ndash; 存取但不分享位置資料的應用程式。 描述輕微或卡通暴力。 

-   **青少年級** &ndash; 涉及藥物或菸酒。 賭博主題或模擬賭博。 煽動性的內容。 褻瀆或低級幽默。 具挑逗性或性暗示。 
    強烈的幻想型暴力。 真實型暴力。 允許使用者找到彼此。 允許使用者彼此進行通訊。 
    分享使用者位置資料。 

-   **成人級** &ndash; 著重於菸酒或藥物的消費或販售。 著重於具挑逗性或性方面的暗示。 圖片暴力。 

「青少年級」清單中的項目相當主觀，因此看似規定「青少年級」分級的指導方針可能強度會足以達到「成人級」分級。 

<a name="pricing_and_distribution" />

## <a name="pricing-amp-distribution"></a>定價與發佈

按一下 **Google Play 開發人員控制台**中的 [定價與發佈]。 在此頁面中，如果應用程式是付費應用程式，請設定價格。
或者，您也可以將應用程式免費散發給所有使用者。 一旦將應用程式指定為免費，它就必須維持免費。
Google Play 不允許將免費應用程式變更為付費應用程式 (不過，可以在免費應用程式使用應用程式內結帳功能來銷售內容)。 Google Play 允許隨時將付費應用程式變更為免費應用程式。

發行付費應用程式之前，必須先有商家帳戶。若要這麼做，請按一下 [設定商家帳戶] 並依照指示進行操作。

[ ![[定價與發佈] 對話方塊](manually-uploading-the-apk-images/12-pricing-sml.png)](manually-uploading-the-apk-images/12-pricing.png)

<a name="manage_countries" />

### <a name="manage-countries"></a>管理國家/地區

下一個區段 [管理國家/地區] 可控制可將應用程式發行至哪些國家/地區：

[ ![[管理國家/地區] 對話方塊](manually-uploading-the-apk-images/13-manage-countries-sml.png)](manually-uploading-the-apk-images/13-manage-countries.png)

<a name="other_information" />

### <a name="other-information"></a>其他資訊

請進一步向下捲動以指定應用程式是否含廣告內容。 此外，[裝置類別] 區段也提供選項，可讓您視需要發行適用於 Android Wear、Android TV 或 Android Auto 的應用程式：

[ ![[含廣告內容] 區段](manually-uploading-the-apk-images/14-contains-ads-sml.png)](manually-uploading-the-apk-images/14-contains-ads.png)

此區段之後是一些可選取的額外選項，例如選擇加入 [闔家適用] 計劃，然後透過 Google Play for Education 散發應用程式。

<a name="consent" />

### <a name="consent"></a>同意

位於 [定價與發佈] 頁面底部的是 [同意] 區段。
這是一個強制性區段，用來宣告應用程式符合 [Android 內容指導方針](http://www.android.com/market/terms/developer-content-policy.html#hl=us)，並認可應用程式受到美國出口法律約束：

[ ![[同意] 區段](manually-uploading-the-apk-images/15-consent-sml.png)](manually-uploading-the-apk-images/15-consent.png)

關於發行 Xamarin.Android 應用程式，還有更多本指南無法涵蓋的內容。
如需有關在 Google Play 中發行應用程式的詳細資訊，請參閱 [歡迎使用 Google Play 開發人員控制台說明中心](https://support.google.com/googleplay/android-developer#topic=3450769)。


<a name="Google_Play_Filters" />

## <a name="google-play-filters"></a>Google Play 篩選器

使用者在瀏覽 Google Play 網站來尋找應用程式時，可以搜尋所有已發行的應用程式。 使用者從 Android 裝置瀏覽 Google Play 時，結果會稍微不同。 結果將會根據與所用裝置的相容性進行篩選。 例如，如果應用程式必須傳送 SMS 訊息，Google Play 就不會對任何無法傳送 SMS 訊息的裝置顯示該應用程式。 對搜尋套用的篩選條件會從下列內容建立：

1.  裝置的硬體組態。
2.  應用程式資訊清單中的宣告。
3.  所使用的電訊廠商 (如果有的話)。
4.  裝置的位置。

您可以在應用程式的資訊清單中新增元素，來協助控制 Google Play 商店中篩選應用程式的方式。 以下列出可用來篩選應用程式的資訊清單元素和屬性：

-   [supports-screen](http://developer.android.com/guide/topics/manifest/supports-screens-element.html) &ndash; Google Play 將會使用這些屬性，根據螢幕大小來判斷應用程式是否可以部署到某個裝置。 
    Google Play 會假設 Android 可以將較小的版面配置放在較大的螢幕中，但無法將較大的版面配置放在較小的螢幕中。 因此，宣稱可以支援一般螢幕的應用程式會出現在大型螢幕的搜尋結果中，但不會出現在小型螢幕的搜尋結果中。 如果 Xamarin.Android 應用程式並未在資訊清單檔中提供 `<supports-screen>` 元素，Google Play 將會假設所有屬性值都為 true，且應用程式支援所有螢幕大小。 此元素必須以手動方式新增至 **AndroidManifest.xml**。 

-   [uses-configuration](http://developer.android.com/guide/topics/manifest/uses-configuration-element.html) &ndash; 此資訊清單元素可用來要求特定硬體功能，例如鍵盤類型、導航裝置、觸控式螢幕等。此元素必須以手動方式新增至 **AndroidManifest.xml**。 

-   [uses-feature](http://developer.android.com/guide/topics/manifest/uses-feature-element.html) &ndash; 此資訊清單元素會宣告應用程式運作所需的裝置必備硬體或軟體功能。 此屬性只用來提供資訊。 Google Play 不會對不符合此篩選條件的裝置顯示此應用程式。 您仍可透過其他方式 (手動或下載) 安裝此應用程式。 此元素必須以手動方式新增至 **AndroidManifest.xml**。 

-   [uses-library](http://developer.android.com/guide/topics/manifest/uses-library-element.html) &ndash; 此元素會指定裝置上必須要有特定的共用程式庫，例如 Google Maps。 指定此元素時，也可以使用 `Android.App.UsesLibaryAttribute` 來指定。 例如:  

    ```csharp
    [assembly: UsesLibrary("com.google.android.maps", true)]
    ```

-   [uses-permission](http://developer.android.com/guide/topics/manifest/uses-permission-element.html) &ndash; 此元素可用來推斷應用程式執行必備但可能尚未以 `<uses-feature>` 元素適當宣告的特定硬體功能。 例如，如果應用程式會要求使用相機的權限，則即使沒有任何宣告相機的 `<uses-feature>` 元素存在，Google Play 也會假設該裝置必須具有相機。 設定此元素時，可以使用 `Android.App.UsesPermissionsAttribute` 來設定。 例如:  

    ```csharp
    [assembly: UsesPermission(Manifest.Permission.Camera)]
    ```

-   [uses-sdk](http://developer.android.com/guide/topics/manifest/uses-sdk-element.html) &ndash; 此元素可用來宣告應用程式所需的最低 Android API 層級。 您可以在 Xamarin.Android 專案的 Xamarin.Android 選項中設定此元素。 

-   [compatible-screens](http://developer.android.com/guide/topics/manifest/compatible-screens-element.html) &ndash; 此元素可用來篩選不符合此元素所指定螢幕大小和密度的應用程式。 大多數應用程式都不應該使用此屬性。 針對需要嚴格控制應用程式散發的特定高效能遊戲或應用程式，才需要使用此屬性。 建議使用上述的 `<support-screen>` 屬性。 

-   [supports-gl-texture](http://developer.android.com/guide/topics/manifest/supports-gl-texture-element.html) &ndash; 此元素可用來宣告應用程式所需的 GL 材質壓縮形成方式。 大多數應用程式都不應該使用此屬性。 針對需要嚴格控制應用程式散發的特定高效能遊戲或應用程式，才需要使用此屬性。 

如需有關設定應用程式資訊清單的詳細資訊，請參閱 Android [應用程式宣示說明](https://developer.android.com/guide/topics/manifest/manifest-intro.html)主題。
