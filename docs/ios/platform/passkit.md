---
title: 在 Xamarin.iOS 中 PassKit
description: 「 電子錢包 」 應用程式可讓 iOS 使用者儲存在其裝置上的數位傳遞。 「 PassKit 架構可讓開發人員以程式設計的方式傳遞與互動。
ms.prod: xamarin
ms.assetid: 74B9973B-C1E8-B727-3F6D-59C1F98BAB3A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/13/2018
ms.openlocfilehash: d1c640bef41e875b3bb427d657c9c239e4c3e16d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121395"
---
# <a name="passkit-in-xamarinios"></a>在 Xamarin.iOS 中 PassKit

IOS 「 電子錢包 」 應用程式可讓使用者在其裝置上儲存數位傳遞。
這些階段會商家所產生，並傳送給客戶，透過電子郵件、 Url，或透過商家自己的 iOS 應用程式。 這些階段可以代表各種項目，從影片到登機忠誠卡的票證。 「 PassKit 架構可讓開發人員以程式設計的方式傳遞與互動。

本文件介紹 「 電子錢包 」 和 「 PassKit API 使用 Xamarin.iOS。

 [![](passkit-images/image1.png "「 電子錢包 」 儲存和組織在手機上的所有階段")](passkit-images/image1.png#lightbox)

## <a name="requirements"></a>需求

在本文件討論 PassKit 功能都需要 iOS 6 和 Xcode 4.5 中，以及 Xamarin.iOS 6.0。

## <a name="introduction"></a>簡介

發佈和管理的條碼 PassKit 解決的重要問題。 條碼目前的使用方式的一些真實世界範例包括：

-   **購買的線上影片票證**– 客戶通常電子條碼，表示他們的票證。 此條碼是列印和掃描項目所花費的杜比劇院效果。
-   **忠誠卡**– 客戶執行 「 電子錢包 」 中面臨的不同存放區特有卡數目，顯示與掃描時，他們購買商品。
-   **優待券**– 優待券會透過電子郵件散發可列印網頁，透過上下黑邊和報紙與雜誌中的條碼。 客戶將他們引導至存放區掃描時，若要接收商品、 服務或折扣的回傳。
-   **乘客搭乘傳遞**– 類似於購買的電影票證。

PassKit 提供針對每個案例的替代方法：

-   **電影票證**– 購買後，客戶會將事件票證傳遞，（透過電子郵件或網站連結）。 電影方法的時間，以提醒您，在鎖定畫面上，會自動出現在階段並在抵達杜比劇院效果 pass 會輕鬆地擷取並顯示在 「 電子錢包 」 進行掃描。
-   **忠誠卡**– 而非 （或除了） 提供對實體卡，存放區可以發出 （透過電子郵件或網站登入之後） 屬於 「 通過 」 存放區卡。 存放區可以提供額外的功能，例如更新上透過推播通知傳遞帳戶的餘額，並使用地理位置服務傳遞無法自動時，會顯示在鎖定畫面上客戶附近的存放位置。
-   **優待券**– 優待券傳遞可以輕鬆地產生唯一的特性，以協助進行追蹤，並透過電子郵件或網站的連結。 已下載的優待券可以自動時，會顯示在鎖定畫面上的使用者附近的特定位置，及/或在指定的日期 （例如，當已接近到期日）。 優待券會儲存在使用者的電話上，因為他們總是好用，並執行未取得錯置。 優待券，可能會鼓勵客戶下載附屬應用程式，因為應用程式市集的連結可以合併到階段中，增加與客戶的參與。
-   **乘客搭乘傳遞**– online 簽入程序之後，客戶會收到其登機透過電子郵件或連結。 傳輸提供者所提供的隨附應用程式可能包括簽入程序，，和也可讓客戶執行額外的功能，例如選擇其一基座或餐點。 傳輸提供者可以使用推播通知，來更新傳遞，如果傳輸的延遲，或已取消。 搭乘時間方法為在階段會出現在鎖定畫面上，提醒您，並提供快速存取至階段。

基本上，PassKit 會提供簡單且方便的方式，來儲存和顯示您的 iOS 裝置上的條碼。 使用額外的時間和位置鎖定畫面整合，推播通知和附屬應用程式將它整合 foundation 非常複雜的銷售，還有提供票證和計費服務。

## <a name="passkit-ecosystem"></a>PassKit 生態系統

PassKit 不是只在產品 CocoaTouch API，而是應用程式、 資料和促進安全共用的服務和管理的條碼及其他資料的大型生態系統的一部分。 此高階圖表會顯示涉及的不同實體中建立和使用傳遞：

 [![](passkit-images/image2.png "此高層級的圖表顯示實體時所包含建立和使用傳遞")](passkit-images/image2.png#lightbox)

生態系統的每個片段都清楚定義的角色：

-   **「 電子錢包 」** – Apple 的內建的 iOS 應用程式可儲存和顯示階段。 這是唯一的地方階段呈現中 （亦即條碼會顯示，以及在階段中的所有當地語系化資料） 的真實世界的使用。
-   **應用程式 」 隨附**– iOS 6 所建置的應用程式傳遞提供者擴充它們發出，例如將值新增至存放區卡片，變更登機證或其他商務特有函式上的座位傳遞的功能。 附屬應用程式不需要屬於 「 通過 」 才能發揮作用。
-   **您的伺服器**– 可以產生和發佈已傳遞的安全伺服器。 附屬應用程式可能會連接到您的伺服器，來產生新的傳遞，或要求更新現有的階段。 您可以選擇性地實作 「 電子錢包 」 會呼叫更新的服務 API 會傳遞 web。
-   **APNS 伺服器**– 您的伺服器能夠通知 「 電子錢包 」 的更新至特定裝置使用 APNS 階段。 將通知推送至 「 電子錢包 」 這再將變更的詳細資訊連絡您的伺服器中。 附屬應用程式不需要實作這項功能的 APNS (它們可以聆聽`PKPassLibraryDidChangeNotification`)。
-   **應用程式的管道**– 不直接操作的應用程式通過 （例如附屬應用程式執行時），但如此可改善其公用程式所辨識的階段，並且允許使用者加入至 「 電子錢包 」。 郵件用戶端、 社交網路瀏覽器和其他應用程式的資料彙總所有遇到附件或傳遞的連結。

整個生態系統看起來複雜，因此值得注意的是，有些元件是選擇性的可能會有更簡單的 PassKit 實作。

## <a name="what-is-a-pass"></a>什麼是屬於 「 通過 」？

屬於 「 通過 」 是表示票證、 優待券或卡之資料的集合。 它可能供個人使用一次 （且因此包含詳細資料，例如航班數目和授權配置），或可能可由任意數目的使用者 （例如折扣券） 共用的多個使用語彙基元。 詳細的描述是用於 Apple[關於傳遞檔案](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Bundle/Chapters/Introduction.html)文件。

### <a name="types"></a>類型

目前五個支援的類型，可區別 「 電子錢包 」 應用程式中傳遞的版面配置和頂端邊緣：

-  **事件票證**– 小型的半圓形剪裁。
-   **就緒階段**– 格那一邊，傳輸特定的圖示中可以指定 （例如。 匯流排、 訓練、 飛航）。
-   **儲存卡**– 捨入前，例如信用卡或轉帳卡。
-  **優待券**– perforated 上方。
-  **泛型**– 相同存放區卡圓的頂端。


在此螢幕擷取畫面顯示五個階段類型 (依順序： 優待券，泛型，儲存卡、 登機和事件票證):

 [![](passkit-images/image3.png "在此螢幕擷取畫面顯示五個階段類型")](passkit-images/image3.png#lightbox)

### <a name="file-structure"></a>檔案結構

傳遞檔案是實際使用 ZIP 封存檔 **.pkpass**延伸模組，其中包含一些特定 JSON 檔案 （必要）、 各種影像檔 （選擇性） 和當地語系化的字串 （也是選擇性的）。

-   **pass.json** – 必要項目。 包含傳遞的所有資訊。
-   **manifest.json** – 必要項目。 包含針對每個檔案在通過簽章檔案以外 (manifest.json) 此檔案的 SHA1 雜湊。
-   **簽章**– 必要項目。 建立簽署`manifest.json`iOS 佈建入口網站中產生的憑證檔案。
-  **logo.png** – 選擇性。
-  **background.png** – 選擇性。
-  **icon.png** – 選擇性。
-  **可當地語系化的字串檔案**– 選擇性。

傳遞檔案的目錄結構如下所示 （這是 ZIP 封存的內容）：

 [![](passkit-images/image4.png "傳遞檔案的目錄結構如下所示")](passkit-images/image4.png#lightbox)

### <a name="passjson"></a>pass.json

JSON 是的格式，因為傳遞通常在伺服器上建立 – 這表示產生程式碼是無從驗證平台的伺服器上。 在每個階段中的三個關鍵部分是資訊的：

-   **teamIdentifier** – 此選項會連結至您的應用程式存放區帳戶產生的所有階段。 這個值會顯示 iOS 佈建入口網站。
-   **passTypeIdentifier** – 註冊群組佈建入口網站中的一起傳遞 （如果您產生一個以上的型別）。 比方說，咖啡廳可能會建立存放區卡片傳遞類型，以允許客戶獲得忠誠度信用額度，但個別優待券也將建立並散發折扣券的型別。 該相同的咖啡廳甚至可能會保存即時事件並發出事件票證傳遞這些。
-   **serialNumber** – 唯一的字串，在這個`passTypeidentifier`。 值對 「 電子錢包 」，是不透明，但對於與您的伺服器通訊時，追蹤的特定傳遞很重要。

還有其他的 JSON 索引鍵，在每個階段中，其中的一個範例如下所示一大堆：

``` 
{
   "passTypeIdentifier":"com.xamarin.passkitdoc.banana",  //Type Identifier (iOS Provisioning Portal)
   "formatVersion":1,                                     //Always 1 (for now)
   "organizationName":"Xamarin",                          //The name which appears on push notifications
   "serialNumber":"12345436XYZ",                          //A number for you to identify this pass
   "teamIdentifier":"XXXAAA1234",                         //Your Team ID
   "description":"Xamarin Demo",                          //
   "foregroundColor":"rgb(54,80,255)",                    //color of the data text (note the syntax)
   "backgroundColor":"rgb(209,255,247)",                  //color of the background
   "labelColor":"rgb(255,15,15)",                         //color of label text and icons
   "logoText":"Banana ",                                  //Text that appears next to logo on top
   "barcode":{                                            //Specification of the barcode (optional)
      "format":"PKBarcodeFormatQR",                       //Format can be QR, Text, Aztec, PDF417
      "message":"FREE-BANANA",                            //What to encode in barcode
      "messageEncoding":"iso-8859-1"                      //Encoding of the message
   },
   "relevantDate":"2012-09-15T15:15Z",                    //When to show pass on screen. ISO8601 formatted.
  /* The following fields are specific to which type of pass. The name of this object specifies the type, e.g., boardingPass below implies this is a boarding pass. Other options include storeCard, generic, coupon, and eventTicket */
   "boardingPass":{
/*headerFields, primaryFields, secondaryFields, and auxiliaryFields are arrays of field object. Each field has a key, label, and value*/
      "headerFields":[          //Header fields appear next to logoText
         {
            "key":"h1-label",   //Must be unique. Used by iOS apps to get the data.
            "label":"H1-label", //Label of the field
            "value":"H1"        //The actual data in the field
         },
         {
            "key":"h2-label",
            "label":"H2-label",
            "value":"H2"
         }
      ],
      "primaryFields":[       //Appearance differs based on pass type
         {
            "key":"p1-label",
            "label":"P1-label",
            "value":"P1"
         }
      ],
      "secondaryFields":[     //Typically appear below primaryFields
         {
            "key":"s1-label",
            "label":"S1-label",
            "value":"S1"
         }
      ],
      "auxiliaryFields":[    //Appear below secondary fields
         {
            "key":"a1-label",
            "label":"A1-label",
            "value":"A1"
         }
      ],
      "transitType":"PKTransitTypeAir"  //Only present in boradingPass type. Value can
                                        //Air, Bus, Boat, or Train. Impacts the picture
                                        //that shows in the middle of the pass.
   }
}
```

### <a name="barcodes"></a>條碼

支援只 2D 格式： PDF417、 阿茲特克、 QR。 Apple 宣告 1d 條碼是不適背光手機畫面上的掃描。

條碼下方顯示的替代文字是選擇性的 – 某些商家想要能夠以手動方式讀取/類型。

ISO-8859-1 編碼是最常見的是，會使用的編碼方式會讀取您的 pass 掃描系統的核取。

### <a name="relevancy-lock-screen"></a>相關性 （鎖定螢幕）

有兩種可能會導致屬於 「 通過 」 可以在鎖定畫面上顯示的資料類型：

 **位置**

最多 10 個位置，請指定屬於 「 通過 」，例如 「 客戶經常造訪的存放區 」 或 「 杜比劇院效果或機場的位置。 客戶可以設定這些位置透過小幫手應用程式，或提供者可以判斷它們的使用情況資料 （如果收集客戶的權限）。

在鎖定畫面上顯示的傳遞時，「 範圍 」 被計算，因此當使用者離開區 pass 隱藏的鎖定畫面。 Radius 是繫結至傳遞以避免不當使用的樣式。

 **日期和時間**

在階段中，可以指定只有一個日期/時間。 日期和時間適合用來觸發鎖定畫面提醒登機和事件的票證。

您可以更新透過推入或透過 PassKit API，以便在 （例如劇場或運動複雜季節票證） 的多用途票證的情況下，無法更新日期/時間。

### <a name="localization"></a>當地語系化

轉譯成多種語言的階段是類似於將 iOS 應用程式當地語系化 – 建立語言特定目錄與`.lproj`延伸模組，並將放在當地語系化的項目。 應該輸入文字翻譯`pass.strings`檔案，而當地語系化的映像應該有它們取代階段根目錄中的映像相同的名稱。

## <a name="security"></a>安全性

將會使用您在 iOS 佈建入口網站中產生的私用憑證簽署。 登入傳遞的步驟如下：

1.  計算行程目錄中的每個檔案的 SHA1 雜湊值 (不包括`manifest.json`或`signature`檔案，兩者都不應該仍存在於這個階段)。
1.  撰寫`manifest.json`作為其雜湊與每個檔案名稱的 JSON 索引鍵/值清單。
1.  使用憑證來簽署`manifest.json`檔案，並將結果寫入檔案，稱為`signature`。
1.  壓縮所有項目，並提供產生的檔案`.pkpass`副檔名。


因為您的私密金鑰才能簽署傳遞，此程序只應該在您控制的安全伺服器上。 不要散發索引鍵，以嘗試並產生應用程式中的階段。

 
## <a name="configuration-and-setup"></a>組態和設定

本節中的指示，協助設定您佈建的詳細資料，並建立您第一次的傳遞。

### <a name="provisioning-passkit"></a>佈建 PassKit

為了讓屬於 「 通過 」 輸入應用程式市集，它必須連結至開發人員帳戶。 這需要兩個步驟：

1.  必須註冊在階段，使用唯一的識別碼，稱為 傳遞類型識別碼。
1.  必須產生有效的憑證，登入開發人員的數位簽章傳遞。

建立下列的票卡類型識別碼重複執行相同動作。

#### <a name="create-a-pass-type-id"></a>建立傳遞類型識別碼

第一個步驟是設定票卡類型識別碼，針對每一個不同_型別_階段都必須支援。 傳遞的識別碼 （或傳遞的型別識別項） 建立階段的唯一識別碼。 我們將使用此識別碼來連結階段開發人員帳戶使用的憑證。

1. 在[的 iOS 佈建入口網站的憑證、 識別碼和設定檔區段](https://developer.apple.com/account/overview.action)，瀏覽至**識別項**，然後選取**傳遞的類型識別碼**。 然後選取**+** 按鈕以建立新的傳遞類型： [ ![](passkit-images/passid.png "建立新的階段類型")](passkit-images/passid.png#lightbox)

2.   提供**描述**（名稱） 及**識別項**（唯一的字串） 階段。 請注意，所有的傳遞類型識別碼必須以字串開頭`pass.`在此範例中，我們使用`pass.com.xamarin.coupon.banana`: [ ![](passkit-images/register.png "提供描述和識別碼")](passkit-images/register.png#lightbox)


3.   確認傳遞的識別碼，藉由按下**註冊** 按鈕。

#### <a name="generate-a-certificate"></a>產生憑證

若要建立此票卡類型識別碼的新憑證，請執行下列各項：

1.  從清單中，選取新建立的傳遞識別碼，然後按一下**編輯**: [ ![](passkit-images/pass-done.png "從清單中選取新的傳遞識別碼")](passkit-images/pass-done.png#lightbox)

    然後，選取  **Create Certificate...** :

    [![](passkit-images/cert-dist.png "選取 建立憑證")](passkit-images/cert-dist.png#lightbox)


2.  請依照下列步驟來建立憑證簽署要求 (CSR)。
  
3. 按下**繼續**開發人員入口網站上的按鈕，然後上傳 CSR 以產生您的憑證。

4. 下載憑證，並在上面按兩下來將它安裝在您的 keychain。


既然我們已經為這個票卡類型識別碼來建立憑證，則下一節會說明如何手動建立屬於 「 通過 」。

如需有關佈建的 「 電子錢包 」 的詳細資訊，請參閱[使用功能](~/ios/deploy-test/provisioning/capabilities/wallet-capabilities.md)指南。

### <a name="create-a-pass-manually"></a>以手動方式建立屬於 「 通過

現在，我們建立我們可以手動將傳遞的類型，打造出將傳遞給在模擬器或裝置上進行測試。 若要存取票的步驟如下：

-  建立包含傳遞檔案的目錄。
-  建立 pass.json 檔案，其中包含所有必要的資料。
-  （如有必要），請在資料夾中包含映像。
-  計算在資料夾中，每個檔案的 SHA1 雜湊，並寫入至 manifest.json。
-  下載的憑證.p12 檔簽署 manifest.json。
-  壓縮目錄的內容，並以.pkpass 副檔名重新命名。


有一些原始程式檔[範例程式碼](https://developer.xamarin.com/samples/monotouch/PassKit/)本文中，可用來產生屬於 「 通過 」。 使用中的檔案`CouponBanana.raw`CreateAPassManually 目錄的目錄。 下列檔案存在：

 [![](passkit-images/image18.png "這些檔案會出現")](passkit-images/image18.png#lightbox)

開啟 pass.json 並編輯 JSON。 您至少必須更新`passTypeIdentifier`和`teamIdentifer`以符合您的 Apple 開發人員帳戶。

```csharp
"passTypeIdentifier" : "pass.com.xamarin.coupon.banana",
"teamIdentifier" : "?????????",
```

您接著必須計算每個檔案的雜湊，並建立`manifest.json`檔案。 當您完成時，它會看起來像這樣：

```csharp
{
  "icon@2x.png" : "30806547dcc6ee084a90210e2dc042d5d7d92a41",
  "icon.png" : "87e9ffb203beb2cce5de76113f8e9503aeab6ecc",
  "pass.json" : "c83cd1441c17ecc6c5911bae530d54500f57d9eb",
  "logo.png" : "b3cd8a488b0674ef4e7d941d5edbb4b5b0e6823f",
  "logo@2x.png" : "3ccd214765507f9eab7244acc54cc4ac733baf87"
}
```

接下來必須產生簽章使用的憑證 （.p12 檔案），為其產生的這個階段類型識別碼。 此檔案

#### <a name="signing-on-a-mac"></a>在 Mac 上的簽章

下載 **「 電子錢包 」 種子支援材料**從[Apple 下載](https://developer.apple.com/downloads/index.action?name=Passbook)站台。 使用`signpass`工具，以將您的資料夾轉換成屬於 「 通過 」 （這也會計算的 SHA1 雜湊，並壓縮輸出到.pkpass 檔案）。

#### <a name="testing"></a>測試

如果您要檢查這些工具的輸出 （藉由設定為.zip 的檔案名稱，然後開啟它），您會看到下列檔案 (請注意新增`manifest.json`和`signature`檔案):

 [![](passkit-images/image19.png "檢查這些工具的輸出")](passkit-images/image19.png#lightbox)

一旦您登入，ZIPped 並重新命名檔案 （例如。 若要`BananaCoupon.pkpass`) 您可以將它拖曳到模擬器來測試，或給自己，以擷取在實際裝置上的電子郵件。 您應該會看到畫面**新增**的階段，像這樣：

 [![](passkit-images/image20.png "新增階段 畫面")](passkit-images/image20.png#lightbox)

通常該程序會自動在伺服器上，但是手動傳遞建立可能只會建立不需要後端伺服器的支援的優待券的小型企業的選項。

## <a name="wallet"></a>電子錢包

「 電子錢包 」 是 PassKit 生態系統的核心。 此螢幕擷取畫面顯示空 「 電子錢包 」，看起來的階段清單和個別的傳遞：

 [![](passkit-images/image21.png "此螢幕擷取畫面顯示空 「 電子錢包 」，看起來的階段清單和個別的傳遞")](passkit-images/image21.png#lightbox)

「 電子錢包 」 的功能包括：

-  這是其掃描的條碼，以呈現階段的唯一位置。
-  使用者可以變更更新的設定。 如果啟用，推播通知可以觸發程序階段中資料的更新。
-  使用者可以啟用或停用鎖定螢幕的整合。 如果啟用，這樣會自動出現在其鎖定畫面上，根據內嵌在階段中的相關時間和位置資料的傳遞。
-  如果 web 伺服器 URL 中傳遞 JSON 提供反向的側邊的行程就會支援以更新提取。
-  附屬應用程式可以開啟 （或下載） 如果在通過 JSON 提供應用程式的識別碼。
-  傳遞 （與可愛的切割動畫） 一起刪除。

## <a name="adding-passes-into-wallet"></a>新增至 「 電子錢包 」 的階段

傳遞可以利用下列方式加入至 「 電子錢包 」:

* **應用程式的管道**– 這些不要直接操作階段中，而只會傳遞檔案載入及呈現給使用者，並將它們新增至 「 電子錢包 」。 

* **應用程式 」 隨附**– 這些撰寫的提供者將散發，並提供其他功能，可瀏覽或編輯它們。 Xamarin.iOS 應用程式可以完整存取 PassKit API 來建立及管理階段。 然後可以加入傳遞至 「 電子錢包 」 使用`PKAddPassesViewController`。 更詳細地說明此程序**附屬應用程式**這份文件的區段。

### <a name="conduit-applications"></a>管道的應用程式

管道的中間的應用程式有可能會收到傳遞代表使用者，並應該去辨識其內容類型，並提供將新增至 「 電子錢包 」 功能。 管道的應用程式的範例包括：

-   **郵件**– 會辨識為屬於 「 通過 」 的附件。
-   **Safari** – 按一下傳遞 URL 連結時，可辨識的傳遞內容類型。
-   **其他自訂應用程式**– 任何收到的附件，或開啟連結 （社交媒體用戶端、 郵件讀取器等） 的應用程式。


此螢幕擷取畫面顯示如何**Mail** iOS 6 會辨識傳遞附件和 （當觸及） 提供給**新增**它 「 電子錢包 」。

 [![](passkit-images/image22.png "此螢幕擷取畫面顯示如何在 iOS 6 中的郵件會辨識傳遞附件")](passkit-images/image22.png#lightbox)

 [![](passkit-images/image23.png "此螢幕擷取畫面顯示郵件提供給 pass 將附件新增至 「 電子錢包 」 的方式")](passkit-images/image23.png#lightbox)

如果您要建置的應用程式，可能是 pass 的管道，它們可以被辨識：

-  **副檔名**-.pkpass
-  **MIME 類型**-application/vnd.apple.pkpass
-  **UTI** – com.apple.pkpass


導管應用程式的基本作業會擷取傳遞檔案，並呼叫 PassKit 的`PKAddPassesViewController`來讓使用者將其 「 電子錢包 」 傳遞選項。 此檢視控制器的實作，會在涵蓋下一節**附屬應用程式**。

管道的應用程式不需要以特定的傳遞類型識別碼中隨附的應用程式進行的相同方式來佈建。

## <a name="companion-applications"></a>附屬應用程式

附屬應用程式提供額外的功能，用於傳遞，包括建立屬於 「 通過 」 更新屬於 「 通過 」 相關聯的資訊，否則管理應用程式相關聯的階段。

附屬應用程式應該不會嘗試複製的 「 電子錢包 」 功能。 它們不適合顯示掃描的階段。

本節的其餘部分會說明如何建置基本附屬應用程式互動的 PassKit。

### <a name="provisioning"></a>佈建

「 電子錢包 」 是一種存放區技術，因為應用程式需要分開佈建，但不能使用小組佈建設定檔或萬用字元應用程式識別碼。 請參閱[使用功能](~/ios/deploy-test/provisioning/capabilities/wallet-capabilities.md)「 電子錢包 」 應用程式建立唯一的 「 應用程式識別碼 」 和 「 佈建設定檔的指南。

### <a name="entitlements"></a>權利

**Entitlements.plist**檔案應該包含所有最近的 Xamarin.iOS 專案中。 若要新增新的 Entitlements.plist 檔案，請遵循[使用權利](~/ios/deploy-test/provisioning/entitlements.md)指南。

若要設定權利執行下列動作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

按兩下**Entitlements.plist**以開啟 Entitlements.plist 編輯器的 Solution Pad 中的檔案：

![](passkit-images/image31.png "Entitlements.plst 編輯器")

在 「 電子錢包 」 區段中，選取**啟用 「 電子錢包 」** 選項

![](passkit-images/image32.png "啟用 「 電子錢包 」 權利")


預設選項是您的應用程式，以允許所有票卡類型。 不過，就可以限制您的應用程式，並只允許部分小組票卡類型。 若要啟用這個，請選取**允許部分小組票卡類型**並輸入您想要允許子集合的傳遞類型識別碼。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

按兩下**Entitlements.plist**開啟 XML 來源檔案的檔案。

若要將 「 電子錢包 」 權利新增，設定**屬性**要`Passbook Identifiers`在下拉式清單中，它會自動將設定**類型** `Array`。 然後，設定字串**值**到`$(TeamIdentifierPrefix)*`:

![](passkit-images/image33.png "啟用 「 電子錢包 」 權利")

這可讓您的應用程式允許所有票卡類型。 若要限制您的應用程式，並只允許部分小組票卡類型，字串將值設定為：

`$(TeamIdentifierPrefix)pass.$(CFBundleIdentifier)`

何處`pass.$(CFBundleIdentifier)`是已建立的傳遞識別碼[上方](~/ios/platform/passkit.md)

-----

### <a name="debugging"></a>偵錯

如果您有部署您的應用程式的問題，請檢查您使用正確**佈建設定檔**而且`Entitlements.plist`當做**自訂權利**檔案**iPhone 套件組合簽署**選項。

如果部署時，您就會發生此錯誤：

```csharp
Installation failed: Your code signing/provisioning profiles are not correctly configured (error: 0xe8008016)
```

然後`pass-type-identifiers`權利陣列不正確 (或不符合**佈建設定檔**)。 請確認傳遞的類型識別碼和您的小組識別碼正確無誤。

## <a name="classes"></a>類別

下列 PassKit 類別可供存取傳遞的應用程式：

-  **PKPass** – 屬於 「 通過 」 的執行個體。
-  **PKPassLibrary** – 提供的 API 來存取裝置上的傳遞。
-  **PKAddPassesViewController** – 用來顯示使用者儲存其 「 電子錢包 」 中的行程。
-  **PKAddPassesViewControllerDelegate** – Xamarin.iOS 開發人員

## <a name="example"></a>範例

中的 PassLibrary 專案，請參閱[範例程式碼](https://developer.xamarin.com/samples/monotouch/PassKit/)這篇文章。 它會示範下列一般函式，將需要在錢包附屬應用程式：

### <a name="check-that-wallet-is-available"></a>「 電子錢包 」 是可用的核取

「 電子錢包 」 不適用於 iPad，好讓應用程式應該檢查之前嘗試存取 PassKit 功能。

```csharp
if (PKPassLibrary.IsAvailable) {
    // create an instance and do stuff...
}
```

### <a name="creating-a-pass-library-instance"></a>建立階段程式庫執行個體

PassKit library 並非單一，應用程式應該建立並儲存，並存取 PassKit API 執行個體。

```csharp
if (PKPassLibrary.IsAvailable) {
    library = new PKPassLibrary ();
    // do stuff...
}
```

### <a name="get-a-list-of-passes"></a>取得一份傳遞

應用程式可以要求傳遞的清單，從程式庫。 這份清單會自動篩選由 PassKit，讓您只可以看到已建立與您的小組識別碼，並列出這些階段權利中。

```csharp
var passes = library.GetPasses ();  // returns PKPass[]
```

請注意，模擬器不會篩選傳回，將清單讓這個方法一律應在實際裝置上測試。 這份清單可以顯示在 UITableView。 [範例應用程式](https://developer.xamarin.com/samples/monotouch/PassKit/)已新增兩個優待券之後看起來像這樣：

 [![](passkit-images/image29.png "範例應用程式看起來這如何之後已新增兩個優待券")](passkit-images/image29.png#lightbox)

### <a name="displaying-passes"></a>顯示傳遞

轉譯階段附屬應用程式內使用一組有限的資訊。

從這一組標準屬性中，選擇與範例程式碼以顯示的階段，清單中。

```csharp
string passInfo =
                "Desc:" + pass.LocalizedDescription
                + "\nOrg:" + pass.OrganizationName
                + "\nID:" + pass.PassTypeIdentifier
                + "\nDate:" + pass.RelevantDate
                + "\nWSUrl:" + pass.WebServiceUrl
                + "\n#" + pass.SerialNumber
                + "\nPassUrl:" + pass.PassUrl;
```

這個字串會顯示為中的警示[範例](https://developer.xamarin.com/samples/monotouch/PassKit/):

 [![](passkit-images/image30.png "在此範例中的優待券選取警示")](passkit-images/image30.png#lightbox)

您也可以使用`LocalizedValueForFieldKey()`方法來擷取在設計階段中的欄位中的資料 （因為您將了解哪些欄位應該會出現）。 範例程式碼不會顯示這。

### <a name="loading-a-pass-from-a-file"></a>從檔案載入屬於 「 通過

屬於 「 通過 」 與該使用者的權限只新增至 「 電子錢包 」，因為檢視控制器必須呈現，讓使用者決定。 此程式碼會在**新增**在範例中，將會內嵌在應用程式 （您應該使用取代您已簽署的其中一個） 的預先建置的階段 按鈕：

```csharp
NSData nsdata;
using ( FileStream oStream = File.Open (newFilePath, FileMode.Open ) ) {
        nsdata = NSData.FromStream ( oStream );
}
var err = new NSError(new NSString("42"), -42);
var newPass = new PKPass(nsdata,out err);
var pkapvc = new PKAddPassesViewController(newPass);
NavigationController.PresentModalViewController (pkapvc, true);
```

Pass 向**新增**並**取消**選項：

 [![](passkit-images/image20.png "在階段有加入和 [取消] 選項")](passkit-images/image20.png#lightbox)

### <a name="replace-an-existing-pass"></a>取代現有的階段

取代現有的階段不需要使用者的權限，不過如果階段不存在，它將會失敗。

```csharp
if (library.Contains (newPass)) {
     library.Replace (newPass);
}
```

### <a name="editing-a-pass"></a>編輯屬於 「 通過

PKPass 不是可變動的因此您無法在程式碼中進行更新傳遞物件。 若要改變屬於 「 通過 」 中的資料應用程式必須存取 web 伺服器，可以追蹤記錄的傳遞，並產生新的傳遞檔案和應用程式可以下載的更新值。

必須在伺服器上完成傳遞檔案建立，因為階段必須使用私用和安全必須保持的憑證來簽署。

產生的更新的傳遞檔案之後, 使用`Replace`覆寫舊的資料，在裝置上的方法。

### <a name="display-a-pass-for-scanning"></a>顯示屬於 「 通過 」 掃描

如先前所述，只有 「 電子錢包 」 可以顯示屬於 「 通過 」 進行掃描。 可以使用顯示屬於 「 通過 」`OpenUrl`方法所示：

 `UIApplication.SharedApplication.OpenUrl (p.PassUrl);`

### <a name="receiving-notifications-of-changes"></a>接收通知的變更

應用程式可以接聽所傳遞的程式庫所做的變更使用`PKPassLibraryDidChangeNotification`。 無法通知觸發在背景中的更新，因此最好在您的應用程式接聽它們所造成的變更。

```csharp
noteCenter = NSNotificationCenter.DefaultCenter.AddObserver (PKPassLibrary.DidChangeNotification, (not) => {
    BeginInvokeOnMainThread (() => {
        new UIAlertView("Pass Library Changed", "Notification Received", null, "OK", null).Show();
        // refresh the list
        var passlist = library.GetPasses ();
        table.Source = new TableSource (passlist, library);
        table.ReloadData ();
    });
}, library);  // IMPORTANT: must pass the library in
```

請務必傳遞的程式庫執行個體，當您註冊通知，因為 PKPassLibrary 並非單一。

## <a name="server-processing"></a>伺服器處理

建置伺服器應用程式以支援 PassKit 的詳細的討論超出本簡介文章的範圍。

請參閱[dotnet passbook](https://github.com/tomasmcguinness/dotnet-passbook)開放原始碼C#伺服器端程式碼。

## <a name="push-notifications"></a>推播通知

若要更新傳遞使用推播通知的詳細的討論超出本簡介文章的範圍。

您需要實作 REST 式 API 回應 web 要求來自 「 電子錢包 」 時需要更新的 Apple 所定義。

請參閱 Apple[更新屬於 「 通過 」](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/PassKit_PG/Updating.html#//apple_ref/doc/uid/TP40012195-CH5-SW1)指南以取得詳細的資訊。

## <a name="summary"></a>總結

這篇文章導入 PassKit、 概述為何很實用的原因，以及描述完整的 PassKit 解決方案必須實作的不同部分。 它所述來設定您的 Apple 開發人員帳戶，以建立階段、 程序，讓屬於 「 通過 」 以手動方式以及如何從 Xamarin.iOS 應用程式存取 PassKit Api 所需的步驟。

## <a name="related-links"></a>相關連結

- [「 電子錢包 」 適用於開發人員](https://developer.apple.com/wallet/)
- [PassKit 範例](https://developer.xamarin.com/samples/monotouch/PassKit/)
- [「 電子錢包 」 開發人員指南](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/PassKit_PG/index.html#//apple_ref/doc/uid/TP40012195-CH1-SW1)
- [架構 – Apple Pay 及 「 電子錢包 」 （WWDC 影片）](https://developer.apple.com/videos/frameworks/apple-pay-and-wallet)
- [PassKit 架構參考](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Framework/_index.html)
- [Passbook Web 服務參考](https://developer.apple.com/library/prerelease/ios/#documentation/PassKit/Reference/PassKit_WebService/WebService.html)
- [關於傳遞檔案](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Bundle/Chapters/Introduction.html)
- [dotnet passbook](https://github.com/tomasmcguinness/dotnet-passbook)，產生 iOS 「 電子錢包 」 套件的開放原始碼程式庫
- [iOS 6 簡介](~/ios/platform/introduction-to-ios6/index.md)
