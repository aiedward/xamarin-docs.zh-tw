---
title: PassKit
description: "錢包是系統 iOS 應用程式儲存和顯示的條碼和其他資訊，以連結客戶與真實世界' 在電話上的交易。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 74B9973B-C1E8-B727-3F6D-59C1F98BAB3A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: beff54d2b2bb72b2adf1e77819c56004b92e13f7
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="passkit"></a>PassKit

_錢包是系統 iOS 應用程式儲存和顯示的條碼和其他資訊，以連結客戶與真實世界' 在電話上的交易。_

錢包的 Iphone 應用程式，而且 iPod 碰觸的 iOS 6。 它會儲存，並顯示條碼和其他資訊，以連結客戶與真實世界' 在電話上的交易。 傳遞所商家所產生並傳送至客戶透過電子郵件，Url，或是從商店自己的 iOS 應用程式。 錢包儲存和組織在電話上的所有階段並根據日期/時間或裝置的位置在鎖定畫面上顯示傳遞備忘提醒。

本文件介紹 Wallet 傳遞套件搭配使用 API Xamarin.iOS，並討論如何在您的伺服器上實作傳遞。

 [ ![](passkit-images/image1.png "錢包儲存與組織在電話上的所有階段")](passkit-images/image1.png)


## <a name="requirements"></a>需求

本文所討論之市集套件功能需要 iOS 6 和 Xcode 4.5，以及 Xamarin.iOS 6.0。


## <a name="introduction"></a>簡介

傳遞的套件可解決的關鍵問題是條碼的發佈和管理。 如何條碼目前使用的一些真實世界範例包括：

-   **購買線上影片票證**– 客戶通常會寄給代表其票證的條碼。 此條碼已列印且來到劇院掃描項目。
-   **忠誠度卡**– 客戶執行皮夾中面臨的不同存放區特有卡數目，顯示與掃描時，它們購買商品。
-   **優待券**– 優待券透過電子郵件散發為可列印網頁，透過 letterboxes 以及報紙與雜誌的條碼。 客戶會將它們的存放區掃描時，若要接收商品、 服務或折扣回傳。
-   **乘客搭乘傳遞**– 類似於購買影片票證。


傳遞的套件提供的替代方法，針對每個案例：

-   **影片票證**– 購買之後, 客戶加入 （透過電子郵件或網站連結） 事件票證傳遞。 影片方法的時間，以在階段會自動出現在鎖定畫面提醒您，並在抵達劇院階段會輕鬆地擷取並顯示在錢包掃描。
-   **忠誠度卡**– 而非 （或其他） 提供實體卡，存放區可以發出 （透過電子郵件或網站的登入之後） 存放區卡傳遞。 存放區可以提供額外的功能，例如更新上透過推播通知傳遞的帳戶的餘額，使用地理位置服務傳遞無法自動出現在鎖定畫面時客戶即將存放區位置。
-   **優待券**– 優待券傳遞可輕鬆使用唯一的特性，可協助使用追蹤時，產生及分散式透過電子郵件或網站的連結。 使用者在附近的特定位置，及/或在特定日期 （例如，當已接近到期日） 時，可以自動下載優待券出現鎖定螢幕上。 優待券儲存在使用者的電話，因為它們一定是很方便，並且不執行取得錯置。 優待券可能會鼓勵客戶下載附屬應用程式，因為應用程式市集的連結可以結合到階段中，增加的客戶業務開發。
-   **乘客搭乘傳遞**– 線上簽入程序之後，客戶將會收到其就緒階段，透過電子郵件或連結。 傳輸提供者所提供的附屬應用程式可能包含簽入程序，並也可讓客戶執行額外的功能類似選擇其一基座 」 或 「 餐點。 傳輸提供者可以使用推播通知，來更新密碼，如果傳輸的延遲或取消。 方法傳遞搭乘時間會顯示鎖定螢幕上提醒您，和提供的快速存取至階段。


基本上，傳遞的套件會提供簡單、 方便的方式，來儲存和顯示 iPhone 或 iPod touch 裝置上的條碼。 額外的時間和位置鎖定畫面整合，推播通知及附屬應用程式整合提供了一 foundation 非常複雜的銷售額，如票證和計費服務。


## <a name="pass-kit-ecosystem"></a>傳遞套件生態系統

傳遞的套件並不是 API CocoaTouch 內，而不是屬於的較大的生態系統的應用程式、 資料和促進安全的共用的服務和管理的條碼及其他資料。 此高層級圖顯示包含的不同實體建立和使用傳遞：

 [ ![](passkit-images/image2.png "此高層級圖表會顯示實體結中建立和使用階段")](passkit-images/image2.png)

生態系統的每個片段都有明確定義的角色：

-   **錢包**– Apple 的內建的 iOS 應用程式 （適用於 iPhone 和 iPod touch) 會儲存並顯示傳遞。 這是唯一的地方使用 （亦即條碼會顯示，並在階段中的所有當地語系化資料） 的真實世界中呈現階段。
-   **附屬應用程式**– iOS 6 建置傳遞提供者，以擴充所發出，例如將值新增到存放區卡片，變更訂票搭乘成功或其他商務特定函式在通過功能的應用程式。 附屬應用程式不需要傳遞才能發揮作用。
-   **您的伺服器**– 安全伺服器可以產生並簽署發佈的傳遞。 附屬應用程式可能會連接到您的伺服器以產生新的傳遞，或要求更新現有的階段。 您可以選擇性地實作錢包會呼叫以更新階段 web 服務 API。
-   **APNS 伺服器**– 您的伺服器可以通知錢包的更新至特定裝置使用 APNS 階段的能力。 將通知推送到錢包的變更的詳細資料然後會連絡您的伺服器。 附屬應用程式不需要這項功能實作 APNS (可以聆聽`PKPassLibraryDidChangeNotification`)。
-   **應用程式管線**– 應用程式，不要直接操作 （例如 附屬應用程式執行作業） 成功，但可以由可辨識的階段，並且允許使用者加入至錢包改善其公用程式。 郵件用戶端、 社交網路瀏覽器和其他資料彙總應用程式所有遇到附件或傳遞的連結。


完整生態系統看起來複雜，因此您很值得注意的某些元件是選擇性的可能會有更簡單的傳遞的組件實作。

## <a name="what-is-a-pass"></a>什麼是傳遞？

在傳遞是表示票證、 優待券或卡之資料的集合。 它可能適合使用一次個人 （而因此包含詳細資料，例如班機號碼而基座配置），或可能也可由任意數目的使用者 （例如折扣券） 共用的多個使用語彙基元。 詳細的說明可用於 Apple[有關通過檔案](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Bundle/Chapters/Introduction.html)文件。


### <a name="types"></a>類型

目前五個支援的類型，可以加以區別，錢包應用程式中傳遞的配置和頂端邊緣：

-  **事件票證**– 小半圓形裁剪。
-   **訓練階段**– 色塊，傳輸特定的圖示中可以指定 （例如。 匯流排、 訓練、 飛航）。
-   **儲存卡**– 捨入前，像是信用卡或金融卡。
-  **優待券**– perforated 上方。
-  **泛型**– 與存放區卡圓相同。


這個螢幕擷取畫面中顯示五個階段類型 (順序： 優待券，泛型，儲存卡、 搭乘傳遞和事件票證):

 [ ![](passkit-images/image3.png "五個階段類型會顯示在此螢幕擷取畫面")](passkit-images/image3.png)

### <a name="file-structure"></a>檔案結構

傳遞檔案是實際的 ZIP 封存與**.pkpass**延伸，包含一些特定 JSON 檔案 （必要），各種不同的映像檔 （選擇性） 和當地語系化的字串 （也是選擇性的）。

-   **pass.json** -必要。 包含以傳遞的所有資訊。
-   **manifest.json** -必要。 包含每個檔案中的簽章檔案以外的傳遞，且此檔案 (manifest.json) 的 SHA1 雜湊。
-   **簽章**-必要。 建立的簽章`manifest.json`產生在 iOS 佈建入口網站中的憑證檔案。
-  **logo.png** – 選擇性。
-  **background.png** – 選擇性。
-  **icon.png** – 選擇性。
-  **可當地語系化的字串檔案**– 選擇性。


傳遞檔案的目錄結構如下所示 （這是 ZIP 封存的內容）：

 [ ![](passkit-images/image4.png "傳遞檔案的目錄結構如下所示")](passkit-images/image4.png)

### <a name="passjson"></a>pass.json

JSON 是格式，因為傳遞通常會建立在伺服器上-則表示產生程式碼是無從驗證平台伺服器上。 在每個階段中三個索引鍵項目是資訊的：

-   **teamIdentifier** – 此選項會連結到您的應用程式市集帳戶產生的所有階段。 這個值會顯示在 iOS 佈建入口網站中。
-   **passTypeIdentifier** – 群組佈建入口網站中的暫存器一起傳遞 （如果您產生一個以上的型別）。 例如，咖啡廳可能會建立存放區卡傳遞類型，才能允許贏得忠誠度信用額度，讓客戶，但建立與散發折扣券不同優待券傳遞的類型。 該相同咖啡廳甚至可能會保存即時事件並發出的事件票證傳遞。
-   **serialNumber** – 這內的唯一字串`passTypeidentifier`。 值是 Wallet 不透明，但對於追蹤的特定傳遞與您的伺服器通訊時是很重要。


沒有大量其他的 JSON 索引鍵，在每個階段中，如下所示的範例：

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

只有 2D 格式都受到支援： PDF417、 阿茲特克、 QR。 Apple 宣告 1d 條碼是不適 backlit 手機畫面上的掃描。

條碼下方顯示的替代文字是選擇性的 – 某些商家想要能夠手動 read/類型。

ISO 8859-1 編碼是最常見的核取哪一種編碼將閱讀您傳遞掃描系統使用。

### <a name="relevancy-lock-screen"></a>相關性 （鎖定螢幕）

有兩種可能會導致將傳遞給鎖定螢幕上顯示的資料類型：

 **位置**

可以指定最多 10 個位置中傳遞，例如客戶經常造訪的存放區或影片或機場的位置。 客戶可以設定這些位置透過附屬應用程式或提供者無法決定其從使用資料 （如果收集客戶的權限）。

鎖定螢幕上顯示的傳遞時，範圍被計算，因此傳遞時的使用者離職後的區域隱藏的鎖定畫面。 Radius 是繫結至傳遞以防止不當使用的樣式。

 **日期和時間**

行程中，可以指定只有一個日期/時間。 日期和時間適用於觸發鎖定畫面提醒搭乘傳遞和事件的票證。

可以透過推入或更新透過 PassKit API，以便將日期/時間可能會在多個使用票證 （例如季節票證劇場或運動複雜） 的情況下獨立更新。

### <a name="localization"></a>當地語系化

轉譯成多種語言的傳遞為類似 iOS 應用程式當地語系化 – 建立語言特定目錄與`.lproj`延伸模組，並將當地語系化的項目內。 文字轉譯應該輸入`pass.strings`檔案，而當地語系化的映像應該有相同名稱做為這些屬性取代階段根目錄中的影像。

## <a name="security"></a>安全性

將會使用您在 iOS 佈建入口網站中產生的私用憑證簽署。 登入階段的步驟如下：

1.  計算行程目錄中每個檔案的 SHA1 雜湊 (不包括`manifest.json`或`signature`檔案，這兩者應仍存在這個階段)。
1.  寫入`manifest.json`為每個檔案名稱和其雜湊的 JSON 索引鍵/值清單。
1.  使用憑證來簽署`manifest.json`檔案，並將結果寫入至檔案，稱為`signature`。
1.  壓縮的所有項目，並提供所產生的檔案`.pkpass`檔案副檔名。


因為您的私密金鑰來簽署傳遞需要，應該只有在您控制的安全伺服器上執行此程序。 不要散發您的金鑰，再試一次，並產生應用程式中的階段。

 
## <a name="configuration-and-setup"></a>組態與設定

本節中的指示來協助安裝您佈建的詳細資料，並建立您第一次的傳遞。

### <a name="provisioning-passkit"></a>佈建 PassKit

為了讓將傳遞給輸入應用程式市集，它必須連結至開發人員帳戶。 這需要兩個步驟：

1.  在階段必須使用稱為傳遞類型識別碼的唯一識別碼，註冊
1.  必須產生有效的憑證來簽署開發人員的數位簽章的傳遞。

若要建立下列傳遞類型識別碼執行。


<a name="create-passid"/>

#### <a name="create-a-pass-type-id"></a>建立傳遞類型識別碼

第一個步驟是設定為傳遞類型識別碼每一個不同_類型_階段都必須支援。 傳遞的識別碼 （或傳遞的型別識別項） 建立傳遞的唯一識別碼。 我們將使用此識別碼與您使用憑證的開發人員帳戶連結傳遞。

1. 在[的 ios 佈建入口網站的憑證、 識別碼和設定檔區段](https://developer.apple.com/account/overview.action)，瀏覽至**識別碼**選取**傳遞的類型識別碼**。 然後選取 **+** 按鈕以建立新的傳遞類型： [ ![ ](passkit-images/passid.png "建立新的傳遞類型")](passkit-images/passid.png)

2.   提供**描述**（名稱） 和**識別碼**（唯一的字串） 階段。 請注意，所有傳遞的型別 Id 必須以字串開頭`pass.`在此範例中我們使用`pass.com.xamarin.coupon.banana`: [ ![ ](passkit-images/register.png "提供說明和識別項")](passkit-images/register.png)


3.   確認傳遞識別碼按**註冊** 按鈕。


<a name="generate" />

#### <a name="generate-a-certificate"></a>產生憑證

若要建立新的憑證，這個傳遞類型 id，執行下列作業：

1.  從清單中，選取新建立的傳遞 ID，然後按一下**編輯**: [ ![ ](passkit-images/pass-done.png "從清單中選取新的傳遞識別碼")](passkit-images/pass-done.png)

    然後，選取**建立憑證...** :

    [ ![](passkit-images/cert-dist.png "選取 建立憑證")](passkit-images/cert-dist.png)


2.  請依照下列步驟來建立憑證簽署要求 (CSR)。
  
3. 按**繼續**開發人員入口網站上的按鈕，然後上傳 CSR 來產生您的憑證。

4. 下載憑證，並按兩下它以安裝在您的金鑰鏈。


既然我們已經為這個傳遞類型識別碼來建立憑證下, 一節會說明如何手動建立成功。

如需有關針對錢包的佈建的詳細資訊，請參閱[功能使用](~/ios/deploy-test/provisioning/capabilities/wallet-capabilities.md)指南。

 <a name="Create_a_Pass_Manually" />

### <a name="create-a-pass-manually"></a>手動建立成功

現在，我們已建立傳遞的型別我們可以手動建立模擬器或裝置上測試的傳遞。 若要建立傳遞的步驟如下：

-  建立包含傳遞檔案的目錄。
-  建立 pass.json 檔案，其中包含所有必要的資料。
-  將映像的資料夾 （如有必要）。
-  計算在資料夾中，每個檔案的 SHA1 雜湊，並寫入 manifest.json。
-  簽署 manifest.json 與下載的憑證.p12 檔案。
-  壓縮目錄的內容，並以.pkpass 副檔名重新命名。


可以用來產生傳遞這篇文章的範例程式碼中有部分來源檔案。 使用中的檔案`CouponBanana.raw`CreateAPassManually 目錄的目錄。 下列檔案會有：

 [ ![](passkit-images/image18.png "這些檔案會出現")](passkit-images/image18.png)

開啟 pass.json 並編輯 JSON。 您至少必須更新`passTypeIdentifier`和`teamIdentifer`以符合您的 Apple 開發人員帳戶。

```csharp
"passTypeIdentifier" : "pass.com.xamarin.coupon.banana",
"teamIdentifier" : "?????????",
```

您必須再計算之每個檔案的雜湊，並建立`manifest.json`檔案。 當您完成時，它看起來像下面這樣：

```csharp
{
  "icon@2x.png" : "30806547dcc6ee084a90210e2dc042d5d7d92a41",
  "icon.png" : "87e9ffb203beb2cce5de76113f8e9503aeab6ecc",
  "pass.json" : "c83cd1441c17ecc6c5911bae530d54500f57d9eb",
  "logo.png" : "b3cd8a488b0674ef4e7d941d5edbb4b5b0e6823f",
  "logo@2x.png" : "3ccd214765507f9eab7244acc54cc4ac733baf87"
}
```

接下來必須產生的簽章使用的憑證 （.p12 檔案），為其產生的此階段類型識別碼。 此檔案

 <a name="Signing_On_a_Mac" />


#### <a name="signing-on-a-mac"></a>在 Mac 上的簽章

下載**錢包種子支援材料**從[Apple 下載](https://developer.apple.com/downloads/index.action?name=Passbook)站台。 使用`signpass`開啟您的資料夾 （這也會計算 SHA1 雜湊，並壓縮成.pkpass 檔案輸出） 的傳遞至工具。

 <a name="Signing_On_a_PC" />


#### <a name="signing-on-a-pc"></a>在電腦上的簽章

範例中有這個發行項的程式碼會將名為專案`signpassnet`上執行的.NET 中 Windows。 它會嘗試模擬 Apple 的工具，不過它包含更少的驗證程式碼。

 <a name="Testing" />


#### <a name="testing"></a>測試

如果您是要檢查這些工具的輸出 （藉由設定為.zip 的檔案名稱並將它開啟），您會看到下列檔案 (請注意新增`manifest.json`和`signature`檔案):

 [ ![](passkit-images/image19.png "檢查這些工具的輸出")](passkit-images/image19.png)

一旦您簽署，ZIPped 並重新命名檔案 （例如。 若要`BananaCoupon.pkpass`) 您可以將它拖曳到模擬器來測試，或傳送電子郵件給自己，以擷取在實際裝置上。 您應該會看到螢幕以**新增**的傳遞，像這樣：

 [ ![](passkit-images/image20.png "加入通過螢幕")](passkit-images/image20.png)

通常該程序會自動在伺服器上，但是手動傳遞建立可能只會建立不需要後端伺服器的支援的優待券的小型企業的選項。

 <a name="Wallet" />

## <a name="wallet"></a>電子錢包

錢包是傳遞的套件生態系統的中央部分。 這個螢幕擷取畫面顯示空白 Wallet 和階段清單及個別階段外觀：

 [ ![](passkit-images/image21.png "這個螢幕擷取畫面顯示空白 Wallet 和階段清單及個別階段外觀")](passkit-images/image21.png)

錢包的功能包括：

-  它是以其為掃描條碼呈現階段的唯一位置。
-  使用者可以變更更新設定。 如果啟用，則推播通知可以觸發階段中資料的更新。
-  使用者可以啟用或停用鎖定螢幕的整合。 如果啟用，這可讓將傳遞給會自動出現在其鎖定畫面上，根據相關內嵌在階段中的時間和位置資料。
-  傳遞的反向端支援更新提取，如果傳遞 JSON 中提供 web 伺服器 URL。
-  附屬應用程式可以開啟 （或下載） 如果傳遞 JSON 中提供應用程式的識別碼。
-  （含可愛 shredding 的動畫），您可以刪除傳遞。


 <a name="Getting_Passes_into_Wallet" />

## <a name="adding-passes-into-wallet"></a>加入傳遞至電子錢包

以下列方式時，可以加入會傳遞錢包：

* **應用程式管線**– 這些不要直接操作階段中，而只會載入傳遞檔案，及對使用者顯示加入皮夾中的選項。 

* **附屬應用程式**– 這些由散發傳遞，並提供額外的功能來瀏覽或編輯這些提供者所撰寫。 Xamarin.iOS 應用程式具有完整存取傳遞套件 API 建立和管理階段。 然後可以加入傳遞至錢包使用`PKAddPassesViewController`。 會更詳細地描述此流程**附屬應用程式**本文件一節。

### <a name="conduit-applications"></a>管道應用程式

管道應用程式是中繼應用程式可能會收到傳遞代表使用者，並應該設計成能辨識其內容類型，並提供電子錢包所加入的功能。 管道應用程式的範例包括：

-   **Mail** – 辨識附件傳遞。
-   **Safari** – 按一下傳遞 URL 連結時，會辨識傳遞的內容類型。
-   **其他自訂應用程式**– 任何收到的附件，或開啟連結 （社交媒體用戶端、 郵件的讀取器等） 的應用程式。


這個螢幕擷取畫面顯示如何**郵件**iOS 6 會辨識傳遞附件 （當觸及） 並詢問您是否要**新增**它錢包。

 [ ![](passkit-images/image22.png "這個螢幕擷取畫面顯示 iOS 6 中的郵件會傳遞附件的辨識")](passkit-images/image22.png)

 [ ![](passkit-images/image23.png "這個螢幕擷取畫面顯示郵件如何提供來傳遞將附件加入至錢包")](passkit-images/image23.png)

如果您要建置的應用程式，可能是為傳遞的導管，它們可以辨識的：

-  **檔案副檔名**-.pkpass
-  **MIME 類型**-application/vnd.apple.pkpass
-  **UTI** – com.apple.pkpass


管道應用程式的基本作業會擷取傳遞檔案，並呼叫傳遞套件`PKAddPassesViewController`來讓使用者將傳遞至其錢包選項。 此檢視控制站的實作會在下一節涵蓋的**附屬應用程式**。

管道應用程式不需要針對特定的傳遞類型識別碼一樣附屬應用程式進行佈建。

## <a name="companion-applications"></a>附屬應用程式

附屬應用程式提供與應用程式相關聯的處理階段，包括建立傳遞、 更新階段相關聯的資訊以及相關管理傳遞額外功能。

附屬應用程式應該不會嘗試重複錢包的功能。 它們並不適合顯示行程進行掃描。

這個本節其餘部分描述如何建立基本附屬應用程式互動使用傳遞的套件。

### <a name="provisioning"></a>佈建

因為錢包存放區技術，應用程式必須分別佈建，且無法使用小組佈建設定檔或萬用字元應用程式識別碼。 請參閱[功能使用](~/ios/deploy-test/provisioning/capabilities/wallet-capabilities.md)錢包應用程式建立唯一的應用程式識別碼和佈建設定檔的指南。

### <a name="entitlements"></a>權利

**Entitlements.plist**檔案應該包含在所有最近使用的 Xamarin.iOS 專案。 若要加入新的 Entitlements.plist 檔，請遵循[使用權利](~/ios/deploy-test/provisioning/entitlements.md)指南。

若要設定權利執行下列動作：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

按兩下**Entitlements.plist** Entitlements.plist 編輯器開啟方案板中的檔案：

![](passkit-images/image31.png "Entitlements.plst 編輯器")

錢包 區段下，選取**啟用錢包**選項

![](passkit-images/image32.png "啟用錢包權利")


預設選項是您的應用程式，以允許通過所有類型。 不過，它可能會限制您的應用程式，並只允許小組傳遞類型的子集。 若要啟用這個，請選取**允許子集小組傳遞類型**並輸入您想要允許子集的傳遞類型識別碼。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

按兩下**Entitlements.plist**檔案以開啟 XML 來源檔案。

若要加入的錢包權限，設定**屬性**至`Passbook Identifiers`在下拉式清單中，它會自動將設定**類型** `Array`。 然後，設定字串**值**至`$(TeamIdentifierPrefix)*`:

![](passkit-images/image33.png "啟用錢包權利")

這可讓您的應用程式允許所有票卡類型。 若要限制您的應用程式，並只允許小組傳遞類型的子集，設定字串值為：

`$(TeamIdentifierPrefix)pass.$(CFBundleIdentifier)`

其中`pass.$(CFBundleIdentifier)`是已建立的傳遞識別碼[上方](~/ios/platform/passkit.md)

-----

### <a name="debugging"></a>偵錯

如果您有部署的應用程式的問題，請檢查您使用正確**佈建設定檔**而且`Entitlements.plist`當做**自訂權利**中檔案**iPhone 套件組合簽署**選項。

如果部署時，您就會發生此錯誤：

```csharp
Installation failed: Your code signing/provisioning profiles are not correctly configured (error: 0xe8008016)
```

然後在`pass-type-identifiers`權利陣列不正確 (或不符合**佈建設定檔**)。 請確認傳遞的類型識別碼和您的小組 ID 是否正確。

 <a name="Classes" />

## <a name="classes"></a>類別

下列傳遞的套件類別可供存取傳遞的應用程式：

-  **PKPass** – 在傳遞的執行個體。
-  **PKPassLibrary** – 提供 API 來存取裝置上的傳遞。
-  **PKAddPassesViewController** – 用來顯示使用者儲存其皮夾中的行程。
-  **PKAddPassesViewControllerDelegate** – Xamarin.iOS 開發人員


## <a name="example"></a>範例

請參閱 PassLibrary 專案，在這份文件的範例程式碼中。 它會示範下列一般函式，將需要在錢包附屬應用程式：

### <a name="check-that-wallet-is-available"></a>請檢查錢包可用

錢包並非在 iPad 上可使用，因此應用程式應該檢查然後再嘗試存取傳遞套件功能。

```csharp
if (PKPassLibrary.IsAvailable) {
    // create an instance and do stuff...
}
```

### <a name="creating-a-pass-library-instance"></a>建立階段程式庫執行個體

傳遞的組件庫並非單一，應用程式應建立及儲存和存取傳遞套件 API 執行個體。

```csharp
if (PKPassLibrary.IsAvailable) {
    library = new PKPassLibrary ();
    // do stuff...
}
```

### <a name="get-a-list-of-passes"></a>取得一份傳遞

應用程式可以要求傳遞一份從程式庫。 這份清單會自動篩選所傳遞的套件，讓您只可以查看已建立您的小組 id，並列出這些階段中權利。

```csharp
var passes = library.GetPasses ();  // returns PKPass[]
```

請注意，模擬器不會篩選的行程傳回，清單，這個方法應該一律在實際裝置上測試。 這份清單可顯示在 UITableView，範例應用程式看起來像這樣，在新增兩個優待券之後：

 [ ![](passkit-images/image29.png "範例應用程式外觀如下所示之後已加入兩個優待券")](passkit-images/image29.png)


### <a name="displaying-passes"></a>顯示傳遞

使用轉譯階段附屬應用程式內的一組有限的資訊。

選擇從這一組標準的屬性顯示清單的階段，範例程式碼一樣。

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

這個字串會顯示為此範例中的警示：

 [ ![](passkit-images/image30.png "優待券選取警示，在此範例")](passkit-images/image30.png)

您也可以使用`LocalizedValueForFieldKey()`方法來擷取設計階段中的欄位中的資料 （因為您會知道哪些欄位應會出現）。 範例程式碼不會再顯示這個。

### <a name="loading-a-pass-from-a-file"></a>從檔案載入階段

傳遞具有使用者的權限只新增至錢包，因為檢視控制器必須呈現，讓他們決定。 此程式碼會用於**新增**按鈕在範例中，載入內嵌在應用程式 （您應該更換這為您已登入） 的預先建立的傳遞：

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

在階段有**新增**和**取消**選項：

 [ ![](passkit-images/image20.png "使用 [新增] 和 [取消] 5d; 選項呈現階段")](passkit-images/image20.png)

### <a name="replace-an-existing-pass"></a>取代現有的階段

取代現有的階段不需要使用者的權限，不過如果階段不存在，它將會失敗。

```csharp
if (library.Contains (newPass)) {
     library.Replace (newPass);
}
```

### <a name="editing-a-pass"></a>編輯階段

PKPass 不是可變動的因此您無法更新您的程式碼中的傳遞物件。 若要改變的傳遞應用程式中的資料必須能夠存取 web 伺服器，可以保留的行程記錄，並產生新的行程檔案和應用程式可以下載的更新值。

必須在伺服器上完成傳遞檔案建立，因為階段必須簽署的憑證，必須保持在安全的私人通訊。

一旦更新的傳遞檔案產生之後，使用`Replace`覆寫舊的資料，在裝置上的方法。

### <a name="display-a-pass-for-scanning"></a>顯示一個行程進行掃描

如先前所述，只有錢包可以顯示一個行程進行掃描。 可以使用顯示在行程`OpenUrl`方法所示：

 `UIApplication.SharedApplication.OpenUrl (p.PassUrl);`

### <a name="receiving-notifications-of-changes"></a>接收通知的變更

應用程式可以接聽所傳遞的程式庫所做的變更使用`PKPassLibraryDidChangeNotification`。 變更可能很好的作法，接聽應用程式中觸發在背景中更新的通知所引起。

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

請務必傳遞程式庫執行個體，當您註冊通知，因為 PKPassLibrary 並非單一。

## <a name="server-processing"></a>伺服器處理

建立伺服器應用程式以支援將傳遞的套件的詳細的討論超出本簡介文章的範圍。

提供的.NET 程式碼*signpassnet*範例可用於做為基礎的伺服器端方法，可以產生會傳遞。

檢視[WWDC 視訊： 簡介 Passbook、 第 2 部分](https://developer.apple.com/videos/wwdc/2012/?include=309#309)27:00 分鐘，如需詳細資訊。

### <a name="other-resources"></a>其他資源

請參閱[dotnet passbook](https://github.com/tomasmcguinness/dotnet-passbook)開啟原始碼 C# 伺服器端程式碼。

## <a name="push-notifications"></a>推播通知

更新傳遞使用推播通知的詳細的討論超出本簡介文章的範圍。

您需要實作需要的更新時，回應 web 要求來自錢包 Apple 所定義的 REST 式 API。 提供的.NET 程式碼*signpassnet*範例無法作為用來產生新的傳遞，這些要求的結果。

檢視[WWDC 視訊： 簡介 Passbook、 第 2 部分](https://developer.apple.com/videos/wwdc/2012/?include=309#309)27:00 分鐘，如需詳細資訊。

## <a name="summary"></a>總結

這篇文章會導入傳遞的套件、 概述一些用處的原因，並且描述必須完整傳遞套件解決方案實作的不同部分。 它所述來設定您的 Apple 開發人員帳戶，以建立階段，讓的程序傳遞以手動方式以及如何存取傳遞套件 Api Xamarin.iOS 應用程式所需的步驟。

## <a name="related-links"></a>相關連結

- [CreateAPassManually （範例）](https://developer.xamarin.com/samples/PassKit/)
- [PassKit 範例](https://developer.xamarin.com/samples/monotouch/PassKit/)
- [iOS 6 簡介](~/ios/platform/introduction-to-ios6/index.md)
- [Passbook 程式設計指南](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Conceptual/PassKit_PG/Chapters/Introduction.html)
- [適用於開發人員 passbook](https://developer.apple.com/passbook/)
- [有關通過檔案](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Bundle/Chapters/Introduction.html)
- [傳遞套件 Framework 參考](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Framework/_index.html)
- [傳遞套件 Framework 參考](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Framework/_index.html)
- [Passbook Web 服務參考](https://developer.apple.com/library/prerelease/ios/#documentation/PassKit/Reference/PassKit_WebService/WebService.html)
