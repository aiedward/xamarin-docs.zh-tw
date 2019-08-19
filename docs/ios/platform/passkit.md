---
title: 在 Xamarin 中 PassKit
description: 錢包應用程式可讓 iOS 使用者在其裝置上儲存數位傳遞。 PassKit 架構可讓開發人員以程式設計方式與傳遞進行互動。
ms.prod: xamarin
ms.assetid: 74B9973B-C1E8-B727-3F6D-59C1F98BAB3A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/13/2018
ms.openlocfilehash: 9f8a092b63da413974f387aca02d81efef9c1625
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528314"
---
# <a name="passkit-in-xamarinios"></a>在 Xamarin 中 PassKit

IOS 錢包應用程式可讓使用者在其裝置上儲存數位傳遞。
這些階段會由商家產生, 並透過電子郵件、Url 或商家本身的 iOS 應用程式傳送給客戶。 這些 pass 可以代表各種不同的事物, 從電影票證到忠誠度卡, 再走刀。 PassKit 架構可讓開發人員以程式設計方式與傳遞進行互動。

本檔介紹錢包, 並搭配使用 PassKit API 與 Xamarin。

 [![](passkit-images/image1.png "電子錢包會在手機上儲存和組織所有刀")](passkit-images/image1.png#lightbox)

## <a name="requirements"></a>需求

本檔中討論的 PassKit 功能需要 iOS 6 和 Xcode 4.5, 以及 Xamarin. iOS 6.0。

## <a name="introduction"></a>簡介

PassKit 所解決的主要問題是條碼的散發與管理。 目前使用條碼的一些真實世界範例包括:

- **線上購買電影票證**–客戶通常會以電子郵件傳送代表其票證的條碼。 這個條碼會列印出來, 並帶到要掃描進入的電影。
- **忠誠度卡**–客戶在其錢包或錢包中攜帶一些不同商店專屬的卡片, 以便在購買貨物時顯示和掃描。
- **贈券**–贈券是透過電子郵件、可列印的網頁、letterboxes, 以及報紙和雜誌中的條碼來散發。 客戶會將其帶入存放區以進行掃描, 以接收貨物、服務或退貨的折扣。
- 「**走刀**」–類似于購買電影票證。

PassKit 提供下列各案例的替代方案:

- **電影票證**–購買後, 客戶可透過電子郵件或網站連結新增事件票證。 隨著電影的使用時間, pass 會自動以提醒形式出現在鎖定畫面上, 而且當電影抵達時, 可以輕鬆地抓取並顯示在錢包中進行掃描。
- **忠誠度卡**–不是提供實體卡, 而是透過電子郵件或在網站登入後, 儲存卡通過。 存放區可以提供額外的功能, 例如透過推播通知更新通過帳戶的平衡, 以及使用地理位置服務, 當客戶接近商店位置時, pass 就會自動出現在鎖定畫面上。
- **贈券**–您可以使用獨特的特性輕鬆地產生優惠券, 以協助追蹤並透過電子郵件或網站連結散發。 下載的優惠券可以在使用者接近特定位置及/或指定的日期時, 自動顯示在鎖定畫面上 (例如當到期日接近時)。 因為優惠券是儲存在使用者的電話上, 所以總是很方便, 而且不會出現錯置。 贈券可能鼓勵客戶下載隨附的應用程式, 因為可以將 App Store 連結併入 Pass 中, 並增加與客戶的互動。
- 參與**階段**–在線上簽入程式之後, 客戶會透過電子郵件或連結接收其參與行程。 傳輸提供者所提供的隨附應用程式可能包含簽入程式, 也可讓客戶執行其他功能, 例如選擇其基座或食物。 傳輸提供者可以使用推播通知, 以在傳輸已延遲或取消時更新 pass。 當「建立時間」接近時, pass 會以提醒形式出現在鎖定畫面上, 並提供快速存取 Pass。

PassKit 在其核心提供了簡單且方便的方式, 可在您的 iOS 裝置上儲存和顯示條碼。 隨著額外的時間和位置鎖定畫面的整合, 推播通知和隨附應用程式整合, 為非常複雜的銷售、票證和帳單服務提供了基礎。

## <a name="passkit-ecosystem"></a>PassKit 生態系統

PassKit 不只是 CocoaTouch 內的 API, 而是較大型的應用程式、資料和服務生態系統的一部分, 有助於安全地共用和管理條碼和其他資料。 此高階圖表會顯示可與建立和使用階段相關的不同實體:

 [![](passkit-images/image2.png "此高階圖表會顯示與建立和使用階段相關的實體")](passkit-images/image2.png#lightbox)

生態系統的每個部分都有明確定義的角色:

- **錢包**– Apple 的內建 iOS 應用程式, 可儲存並顯示通過。 這是轉譯以用於真實世界的唯一位置 (即會顯示條碼, 以及通過中的所有當地語系化資料)。
- **隨附應用**程式–由傳遞提供者所建立的 iOS 6 應用程式, 以擴充他們所發出的傳遞功能, 例如將價值新增至商店卡片、變更座位通過或其他商務特定功能的基座。 傳遞應用程式不需要通過, 就能發揮作用。
- **您的伺服器**–一種安全的伺服器, 可在其中產生和簽署要散發的傳遞。 您的隨附應用程式可能會連線到您的伺服器, 以產生新的 pass 或要求更新現有的通過。 您可以選擇性地執行錢包會呼叫更新階段的 web 服務 API。
- **APNS 伺服器**-您的伺服器能夠使用 APNS 通知特定裝置上的更新包。 將通知推送至錢包, 這會接著連線到您的伺服器, 以取得變更的詳細資料。 隨附應用程式不需要為這項功能執行 APNS (可以接聽`PKPassLibraryDidChangeNotification` )。
- **管道應用**程式–不直接操作通過的應用程式 (例如「附屬應用程式」), 但可以藉由辨識通過並允許將其新增至錢包來改善其公用程式。 郵件用戶端、社交網路瀏覽器和其他資料匯總應用程式可能會遇到附件或通過的連結。

整個生態系統看起來很複雜, 因此值得注意的是, 有些元件是選擇性的, 而且可能會有更簡單的 PassKit 實施。

## <a name="what-is-a-pass"></a>什麼是通過？

Pass 是代表票證、贈券或卡片的資料集合。 它可供個人使用 (因此包含航班號和基座配置等詳細資料), 也可以由任意數量的使用者 (例如折扣優惠券) 共用多個使用權杖。 Apple 的[關於「傳遞](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Bundle/Chapters/Introduction.html)檔案」檔中提供詳細的描述。

### <a name="types"></a>型別

目前有五個支援的類型, 可以透過行程的版面配置和上邊緣來區別于錢包應用程式:

- **事件票證**-小型半圓圖樣。
- 架**Pass** –可以指定邊緣的凹槽、傳輸特定的圖示 (例如 匯流排、訓練、飛機)。
- **商店卡片**–進位, 例如信用卡或轉帳卡。
- **優惠券**–沿著頂端 perforated。
- [**一般**] –與 [儲存卡]、[進位]。


這五個傳遞類型會顯示在此螢幕擷取畫面中 (依序為: 贈券、generic、商店卡片、「服務台通過」和「事件票證」):

 [![](passkit-images/image3.png "這五個傳遞類型會顯示在此螢幕擷取畫面中")](passkit-images/image3.png#lightbox)

### <a name="file-structure"></a>檔案結構

「傳遞」檔案實際上是副檔名為**pkpass**的 ZIP 封存, 其中包含一些特定的 JSON 檔案 (必要)、各種影像檔案 (選擇性), 以及當地語系化的字串 (也是選擇性的)。

- **pass. json** –必要。 包含通過的所有資訊。
- **manifest. json** –必要。 包含傳遞中每個檔案的 SHA1 雜湊, 但簽章檔案和此檔案 (manifest. json) 除外。
- **signature** –必要。 藉由使用 iOS `manifest.json`布建入口網站中產生的憑證來簽署檔案來建立。
- **標誌 .png** –選擇性。
- **背景 .png** –選擇性。
- **圖示 .png** –選擇性。
- 可**當地語系化的字串**檔案–選擇性。

傳遞檔案的目錄結構如下所示 (這是 ZIP 封存的內容):

 [![](passkit-images/image4.png "傳遞檔案的目錄結構如下所示")](passkit-images/image4.png#lightbox)

### <a name="passjson"></a>pass.json

JSON 是格式, 因為通常會在伺服器上建立傳遞, 這表示世代程式碼在伺服器上與平臺無關。 每個階段中的三個重要資訊片段如下:

- **teamIdentifier** –這會連結您產生的所有傳遞至 App Store 帳戶。 此值會顯示在 iOS 布建入口網站中。
- **passTypeIdentifier** –在布建入口網站中註冊, 以群組方式一起傳遞 (如果您產生一個以上的類型)。 例如, 咖啡廳可能會建立商店卡片通過類型, 以允許其客戶獲得忠誠度信用額度, 同時也會使用個別的優惠券 pass 類型來建立和散發折扣優惠券。 相同的咖啡廳甚至可能會保存即時的音樂事件, 並為這些活動發出事件票證傳遞。
- **serialNumber** –這個`passTypeidentifier`中的唯一字串。 此值對錢包而言是不透明的, 但在與您的伺服器通訊時, 追蹤特定的傳遞非常重要。

每個階段中有大量的其他 JSON 金鑰, 範例如下所示:

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

僅支援2D 格式:PDF417、Aztec、QR。 在 backlit 的手機畫面上 unsuited 1D 條碼以進行掃描的 Apple 宣告。

條碼下方所顯示的替代文字是選擇性的-某些商家想要能夠手動讀取/輸入。

ISO-8859-1 編碼是最常見的, 檢查將讀取您通過的掃描系統所使用的編碼方式。

### <a name="relevancy-lock-screen"></a>相關性 (鎖定畫面)

有兩種類型的資料可能會導致在鎖定畫面上顯示傳遞:

 **位置**

一次最多可以指定10個位置, 例如客戶經常造訪的商店, 或電影院或機場的位置。 客戶可以透過隨附的應用程式來設定這些位置, 或提供者可以從使用資料 (如果是以客戶的許可權收集) 來判斷它們。

當 [傳遞] 在鎖定畫面上顯示時, 會計算一個範圍, 讓使用者離開該區域時, 鎖定畫面上會隱藏該階段。 Radius 系結至通過樣式以防止濫用。

 **日期和時間**

只能在 Pass 中指定一個日期/時間。 日期和時間適用于觸發「取得通過」和「事件票證」的鎖定畫面提醒。

可以透過推送或透過 PassKit API 來更新, 以便在多重使用票證 (例如, 對劇院或體育運動的季節票證) 時, 可以更新日期/時間。

### <a name="localization"></a>當地語系化

將傳遞轉譯成多種語言類似于當地語系化 iOS 應用程式–使用`.lproj`副檔名建立語言特定目錄, 並將當地語系化的元素放在內。 文字翻譯應該輸入到`pass.strings`檔案中, 而當地語系化影像的名稱應該與它們在「傳遞」根中取代的影像相同。

## <a name="security"></a>安全性

Pass 會使用您在 iOS 布建入口網站中產生的私用憑證進行簽署。 簽署 pass 的步驟如下:

1. 計算 pass 目錄中每個檔案的 SHA1 雜湊 (請勿包含`manifest.json`或`signature`檔案, 無論如何都不應該存在於這個階段)。
1. 以`manifest.json`每個檔案名的 JSON 索引鍵/值清單寫入, 其雜湊。
1. 使用憑證簽署`manifest.json`檔案, 並將結果寫入名`signature`為的檔案。
1. 將所有專案壓縮, 並為產生`.pkpass`的檔案提供副檔名。


由於您的私密金鑰必須簽署 pass, 因此只能在您控制的安全伺服器上執行此程式。 請勿散發您的金鑰, 以嘗試在應用程式中產生傳遞。

 
## <a name="configuration-and-setup"></a>設定和安裝

本節包含的指示可協助您設定布建詳細資料, 並建立您的第一次傳遞。

### <a name="provisioning-passkit"></a>布建 PassKit

若要讓 pass 進入 App Store, 必須將其連結至開發人員帳戶。 這需要兩個步驟:

1. 必須使用稱為「傳遞類型識別碼」的唯一識別碼來註冊 pass。
1. 必須產生有效的憑證, 才能使用開發人員的數位簽章來簽署 pass。

若要建立「傳遞類型識別碼」, 請執行下列動作。

#### <a name="create-a-pass-type-id"></a>建立傳遞類型識別碼

第一個步驟是針對要支援的每個不同_類型_的傳遞, 設定一個傳遞類型識別碼。 「傳遞識別碼」 (或「傳遞類型識別碼」) 會建立傳遞的唯一識別碼。 我們將使用此識別碼, 以使用憑證將傳遞與您的開發人員帳戶連結。

1. 在 iOS 布建[入口網站的 [憑證、識別碼及設定檔] 區段](https://developer.apple.com/account/overview.action)中, 流覽至 [**識別碼**], 然後選取 [**傳遞類型 id** ]。 然後選取 **+** 按鈕，以建立新的傳遞類型：[![](passkit-images/passid.png "建立新的 pass 類型")](passkit-images/passid.png#lightbox)

2. 提供 Pass 的**描述**(名稱) 和**識別碼**(唯一字串)。 請注意, 在此範例中, 所有傳遞類型`pass.`識別碼的開頭都必須`pass.com.xamarin.coupon.banana`是字串, 我們使用:[![](passkit-images/register.png "提供描述和識別碼")](passkit-images/register.png#lightbox)


3. 按下 [**註冊**] 按鈕以確認傳遞識別碼。

#### <a name="generate-a-certificate"></a>產生憑證

若要建立此傳遞類型識別碼的新憑證, 請執行下列動作:

1. 從清單中選取新建立的 [傳遞識別碼], 然後按一下 [**編輯**]:[![](passkit-images/pass-done.png "從清單中選取新的 Pass ID")](passkit-images/pass-done.png#lightbox)

    然後, 選取 [**建立憑證 ...** ] :

    [![](passkit-images/cert-dist.png "選取 [建立憑證]")](passkit-images/cert-dist.png#lightbox)


2. 遵循步驟來建立憑證簽署要求 (CSR)。
  
3. 在開發人員入口網站上按下 [**繼續**] 按鈕, 並上傳 CSR 以產生您的憑證。

4. 下載憑證並在其上按兩下以將它安裝在您的 keychain 中。


既然我們已建立此傳遞類型識別碼的憑證, 下一節會說明如何手動建立傳遞。

如需布建錢包的詳細資訊, 請參閱[使用功能](~/ios/deploy-test/provisioning/capabilities/wallet-capabilities.md)指南。

### <a name="create-a-pass-manually"></a>手動建立階段

既然我們已經建立了 Pass 型別, 我們可以手動製作一個 pass 來測試模擬器或裝置。 建立階段的步驟如下:

- 建立包含傳遞檔案的目錄。
- 建立包含所有必要資料的 pass. json 檔案。
- 將影像包含在資料夾中 (如有需要)。
- 計算資料夾中每個檔案的 SHA1 雜湊, 並寫入至資訊清單. json。
- 以下載的憑證 p12 檔案簽署資訊清單。
- 壓縮目錄的內容, 並使用 pkpass 副檔名重新命名。


本文的[範例程式碼](https://docs.microsoft.com/samples/xamarin/ios-samples/passkit)中有一些來源檔案, 可以用來產生 pass。 使用 CreateAPassManually 目錄`CouponBanana.raw`目錄中的檔案。 有下列檔案:

 [![](passkit-images/image18.png "這些檔案存在")](passkit-images/image18.png#lightbox)

開啟 [傳遞 json] 並編輯 JSON。 您必須至少更新`passTypeIdentifier`和`teamIdentifer` , 以符合您的 Apple 開發人員帳戶。

```csharp
"passTypeIdentifier" : "pass.com.xamarin.coupon.banana",
"teamIdentifier" : "?????????",
```

接著, 您必須計算每個檔案的雜湊, `manifest.json`並建立檔案。 當您完成時, 它看起來會像這樣:

```csharp
{
  "icon@2x.png" : "30806547dcc6ee084a90210e2dc042d5d7d92a41",
  "icon.png" : "87e9ffb203beb2cce5de76113f8e9503aeab6ecc",
  "pass.json" : "c83cd1441c17ecc6c5911bae530d54500f57d9eb",
  "logo.png" : "b3cd8a488b0674ef4e7d941d5edbb4b5b0e6823f",
  "logo@2x.png" : "3ccd214765507f9eab7244acc54cc4ac733baf87"
}
```

接下來, 必須使用為此傳遞類型識別碼產生的憑證 (p12 檔案) 來產生此檔案的簽章。

#### <a name="signing-on-a-mac"></a>在 Mac 上進行簽署

從[Apple 下載](https://developer.apple.com/downloads/index.action?name=Passbook)網站下載**錢包種子支援材料**。 使用此`signpass`工具將您的資料夾轉換為 pass (這也會計算 SHA1 雜湊, 並將輸出壓縮成 pkpass 檔案)。

#### <a name="testing"></a>測試

如果您要檢查這些工具的輸出 (藉由將 filename 設定為 .zip, 然後再開啟), 您會看到下列檔案 (請注意, 新增`manifest.json`和`signature`檔案):

 [![](passkit-images/image19.png "檢查這些工具的輸出")](passkit-images/image19.png#lightbox)

簽署之後, 請壓縮並重新命名檔案 (例如 若`BananaCoupon.pkpass`要), 您可以將它拖曳到模擬器以進行測試, 或以電子郵件傳送給自己以在實際裝置上取得。 您應該會看到一個畫面來**新增**pass, 如下所示:

 [![](passkit-images/image20.png "新增 [通過] 畫面")](passkit-images/image20.png#lightbox)

一般來說, 伺服器上的程式會自動化, 不過, 手動建立可能是小型企業的選項, 只有建立不需要支援後端伺服器的贈券。

## <a name="wallet"></a>電子錢包

錢包是 PassKit 生態系統的中心部分。 這個螢幕擷取畫面顯示空的錢包, 以及通過清單和個別行程的外觀:

 [![](passkit-images/image21.png "此螢幕擷取畫面顯示空的錢包, 以及通過清單和個別行程的外觀")](passkit-images/image21.png#lightbox)

錢包的功能包括:

- 傳遞的唯一位置是以其條碼呈現以進行掃描。
- 使用者可以變更更新的設定。 若已啟用, 推播通知可以觸發對通過中資料的更新。
- 使用者可以啟用或停用鎖定畫面整合。 啟用時, 這可讓傳遞自動顯示在鎖定畫面上, 這是以內嵌于傳遞中的相關時間和位置資料為基礎。
- 如果傳遞 JSON 中提供了 web 伺服器 URL, pass 的反向會支援提取重新整理。
- 如果在 pass JSON 中提供應用程式的識別碼, 則可以開啟 (或下載) 附屬應用程式。
- 可以刪除通過 (有一種很可愛的清除動畫)。

## <a name="adding-passes-into-wallet"></a>將傳遞至錢包

傳遞可以透過下列方式新增至錢包:

* **管道應用程式**–這些不會直接操作傳遞, 而是只會載入傳遞檔案, 並提供使用者將其新增至錢包的選項。 

* **附屬應用程式**–這些是由提供者所撰寫, 以散發階段, 並提供額外的功能來流覽或編輯它們。 Xamarin iOS 應用程式可完整存取 PassKit API, 以建立及操作階段。 然後, 可以使用`PKAddPassesViewController`將傳遞新增至錢包。 本檔的**隨附應用程式**一節會更詳細地說明此流程。

### <a name="conduit-applications"></a>管道應用程式

管道應用程式是指可能會代表使用者接收傳遞的中繼應用程式, 而且應該以程式設計方式辨識其內容類型, 並提供要新增至錢包的功能。 管道應用程式的範例包括:

- **Mail** –將附件識別為 Pass。
- **Safari** –在按下 [傳遞 URL] 連結時, 可辨識 [傳遞內容類型]。
- **其他自訂應用程式**–接收附件或開啟連結的任何應用程式 (社交媒體用戶端、郵件讀取者等)。


此螢幕擷取畫面顯示 iOS 6 中的**郵件**如何辨識傳遞附件, 以及 (在接觸時)**將其新增**至錢包的方式。

 [![](passkit-images/image22.png "此螢幕擷取畫面顯示 iOS 6 中的郵件如何辨識傳遞附件")](passkit-images/image22.png#lightbox)

 [![](passkit-images/image23.png "這個螢幕擷取畫面顯示 Mail 如何提供將 pass 附件新增至錢包的方式")](passkit-images/image23.png#lightbox)

如果您要建立的應用程式可能是通過的管道, 可以藉由下列方式辨識:

- **副檔名**-. pkpass
- **MIME 類型**-application/application. apple. pkpass
- **UTI** – pkpass


管道應用程式的基本作業是抓取傳遞檔案並呼叫 PassKit `PKAddPassesViewController` , 讓使用者可以選擇是否要將傳遞至其錢包。 下一節的**附屬應用程式**涵蓋此視圖控制器的執行。

管道應用程式不需要針對特定的「傳遞類型識別碼」進行布建, 就像應用程式一樣。

## <a name="companion-applications"></a>隨附應用程式

隨附的應用程式提供額外的功能來使用 Pass, 包括建立傳遞、更新與傳遞相關聯的資訊, 以及管理與應用程式相關聯的傳遞。

隨附的應用程式不應該嘗試複製錢包的功能。 它們不適合用來顯示掃描的階段。

本節的其餘部分將說明如何建立與 PassKit 互動的基本附屬應用程式。

### <a name="provisioning"></a>佈建

因為錢包是商店技術, 所以應用程式必須個別布建, 而且無法使用小組布建設定檔或萬用字元應用程式識別碼。 請參閱[使用功能](~/ios/deploy-test/provisioning/capabilities/wallet-capabilities.md)指南來建立錢包應用程式的唯一應用程式識別碼和布建設定檔。

### <a name="entitlements"></a>權利

所有最近的 Xamarin. iOS 專案中都應該包含**plist**檔案。 若要加入新的 plist 檔案, 請依照[使用權利](~/ios/deploy-test/provisioning/entitlements.md)指南中的步驟進行。

若要設定權利, 請執行下列動作:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

按兩下 Solution Pad 中的**plist**檔案, 開啟 plist 編輯器:

![](passkit-images/image31.png "Plst 編輯器")

在 [錢包] 區段下, 選取 [**啟用錢包**] 選項

![](passkit-images/image32.png "啟用錢包的權利")


預設選項是讓您的應用程式允許所有的傳遞類型。 不過, 您可以限制您的應用程式, 而且只允許一部分的小組傳遞類型。 若要啟用此專案, 請選取 [**允許小組傳遞類型的子集]** , 然後輸入您想要允許之子集的傳遞類型識別碼。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

按兩下**plist**檔案以開啟 XML 原始檔。

若要新增錢包權利, 請在下拉式清單`Passbook Identifiers`中將屬性設定為, 這會自動設定**型** `Array`別。 然後, 將字串**值**設定為`$(TeamIdentifierPrefix)*`:

![](passkit-images/image33.png "啟用錢包的權利")

這可讓您的應用程式允許所有票卡類型。 若要限制您的應用程式, 而且只允許一部分的 team pass 類型, 請將字串值設定為:

`$(TeamIdentifierPrefix)pass.$(CFBundleIdentifier)`

其中`pass.$(CFBundleIdentifier)`是[先前](~/ios/platform/passkit.md)建立的 pass ID

-----

### <a name="debugging"></a>偵錯

如果您在部署應用程式時遇到問題, 請檢查您使用的是正確的布建`Entitlements.plist` **設定檔**, 並且已在**iPhone 配套簽署**選項中選取做為**自訂權利**檔案。

如果您在部署時遇到此錯誤:

```csharp
Installation failed: Your code signing/provisioning profiles are not correctly configured (error: 0xe8008016)
```

而權利陣列則不正確 (或不符合布建**設定檔)。** `pass-type-identifiers` 請確認傳遞類型識別碼和您的小組識別碼正確。

## <a name="classes"></a>類別

下列 PassKit 類別可供應用程式存取階段:

- **PKPass** – Pass 的實例。
- **PKPassLibrary** –提供 API 來存取裝置上的傳遞。
- **PKAddPassesViewController** –用來顯示使用者在其錢包中節省的 pass。
- **PKAddPassesViewControllerDelegate** – Xamarin iOS 開發人員

## <a name="example"></a>範例

請參閱本文的[範例程式碼](https://docs.microsoft.com/samples/xamarin/ios-samples/passkit)中的 PassLibrary 專案。 它會示範在錢包附隨應用程式中所需的下列一般功能:

### <a name="check-that-wallet-is-available"></a>檢查錢包是否可供使用

IPad 無法使用錢包, 因此應用程式應該先檢查, 再嘗試存取 PassKit 功能。

```csharp
if (PKPassLibrary.IsAvailable) {
    // create an instance and do stuff...
}
```

### <a name="creating-a-pass-library-instance"></a>建立傳遞程式庫實例

PassKit 程式庫不是 singleton, 應用程式應該建立並儲存和實例, 以存取 PassKit API。

```csharp
if (PKPassLibrary.IsAvailable) {
    library = new PKPassLibrary ();
    // do stuff...
}
```

### <a name="get-a-list-of-passes"></a>取得通過清單

應用程式可以向程式庫要求一份傳遞清單。 這份清單會由 PassKit 自動篩選, 因此您只能看到已經使用您的小組識別碼建立的 pass, 以及您的權利中所列的傳遞。

```csharp
var passes = library.GetPasses ();  // returns PKPass[]
```

請注意, 模擬器不會篩選傳回的傳遞清單, 因此應該一律在實際裝置上測試此方法。 這份清單可以在 UITableView 中顯示。 新增兩個優惠券之後,[範例應用程式](https://docs.microsoft.com/samples/xamarin/ios-samples/passkit)看起來會像這樣:

 [![](passkit-images/image29.png "新增兩個優惠券之後, 範例應用程式看起來會像這樣")](passkit-images/image29.png#lightbox)

### <a name="displaying-passes"></a>顯示階段

有一組有限的資訊可用於在附屬應用程式中轉譯傳遞。

如範例程式碼所示, 從這組標準屬性中選擇以顯示通過清單。

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

此字串在[範例](https://docs.microsoft.com/samples/xamarin/ios-samples/passkit)中會顯示為警示:

 [![](passkit-images/image30.png "範例中選取的優惠券警示")](passkit-images/image30.png#lightbox)

您也可以使用`LocalizedValueForFieldKey()`方法, 從您所設計之「傳遞」中的欄位抓取資料 (因為您會知道應該存在哪些欄位)。 範例程式碼不會顯示這種情況。

### <a name="loading-a-pass-from-a-file"></a>從檔案載入傳遞

因為 pass 只能以使用者的許可權新增到錢包, 所以必須呈現視圖控制器以讓他們決定。 此程式碼用於範例中的 [**新增**] 按鈕, 以載入內嵌在應用程式中的預先建立階段 (您應該將此內容取代為您已簽署的):

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

Pass 會以**Add**和**Cancel**選項呈現:

 [![](passkit-images/image20.png "以 Add 和 Cancel 選項呈現的 pass")](passkit-images/image20.png#lightbox)

### <a name="replace-an-existing-pass"></a>取代現有的 Pass

取代現有的 pass 並不需要使用者的許可權, 但如果傳遞不存在, 則會失敗。

```csharp
if (library.Contains (newPass)) {
     library.Replace (newPass);
}
```

### <a name="editing-a-pass"></a>編輯 Pass

PKPass 不是可變的, 因此您無法在程式碼中更新傳遞物件。 若要更改傳遞中的資料, 應用程式必須能夠存取可保留傳遞記錄的 web 伺服器, 並以應用程式可以下載的更新值產生新的傳遞檔案。

必須在伺服器上進行傳遞檔案建立, 因為必須使用必須保持私用和安全的憑證來簽署傳遞。

產生更新的傳遞檔案之後, 請使用`Replace`方法來覆寫裝置上的舊資料。

### <a name="display-a-pass-for-scanning"></a>顯示掃描的階段

如先前所述, 只有錢包可以顯示掃描的通過。 您可以使用`OpenUrl`方法來顯示 Pass, 如下所示:

 `UIApplication.SharedApplication.OpenUrl (p.PassUrl);`

### <a name="receiving-notifications-of-changes"></a>接收變更的通知

應用程式可以使用`PKPassLibraryDidChangeNotification`來接聽對傳遞程式庫所做的變更。 變更可能是在背景觸發更新的通知所造成, 因此在您的應用程式中接聽它們是很好的作法。

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

註冊通知時, 請務必傳遞程式庫實例, 因為 PKPassLibrary 不是單一的。

## <a name="server-processing"></a>伺服器處理

建立伺服器應用程式以支援 PassKit 的詳細討論已超出本簡介文章的範圍。

請參閱[dotnet-passbook](https://github.com/tomasmcguinness/dotnet-passbook)開放C#原始碼伺服器端程式碼。

## <a name="push-notifications"></a>推播通知

使用推播通知來更新階段的詳細討論已超出本簡介文章的範圍。

當需要更新時, 您必須執行由 Apple 定義的 REST API, 以回應來自于錢包的 web 要求。

如需詳細資訊, 請參閱 Apple 的[更新通過](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/PassKit_PG/Updating.html#//apple_ref/doc/uid/TP40012195-CH5-SW1)指南。

## <a name="summary"></a>總結

這篇文章介紹了 PassKit, 其中有一些原因會使其有用, 並說明必須針對完整的 PassKit 解決方案所執行的不同部分。 其中說明了設定 Apple 開發人員帳戶來建立階段所需的步驟、手動進行傳遞的程式, 以及如何從 Xamarin 應用程式存取 PassKit Api。

## <a name="related-links"></a>相關連結

- [開發人員的錢包](https://developer.apple.com/wallet/)
- [PassKit 範例](https://docs.microsoft.com/samples/xamarin/ios-samples/passkit)
- [錢包開發人員指南](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/PassKit_PG/index.html#//apple_ref/doc/uid/TP40012195-CH1-SW1)
- [架構– Apple Pay 和錢包 (WWDC 影片)](https://developer.apple.com/videos/frameworks/apple-pay-and-wallet)
- [PassKit 架構參考](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Framework/_index.html)
- [Passbook Web 服務參考](https://developer.apple.com/library/prerelease/ios/#documentation/PassKit/Reference/PassKit_WebService/WebService.html)
- [關於傳遞檔案](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Bundle/Chapters/Introduction.html)
- [dotnet-passbook](https://github.com/tomasmcguinness/dotnet-passbook), 這是用來產生 iOS 錢包套件的開放原始碼程式庫
- [iOS 6 簡介](~/ios/platform/introduction-to-ios6/index.md)
