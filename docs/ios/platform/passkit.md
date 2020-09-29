---
title: 在 Xamarin 中 PassKit
description: 錢包應用程式可讓 iOS 使用者在其裝置上儲存數位傳遞。 PassKit 架構可讓開發人員以程式設計方式與通過互動。
ms.prod: xamarin
ms.assetid: 74B9973B-C1E8-B727-3F6D-59C1F98BAB3A
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/13/2018
ms.openlocfilehash: edbdf656774026eab66ada748bedb61cead3a31a
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435328"
---
# <a name="passkit-in-xamarinios"></a>在 Xamarin 中 PassKit

IOS 錢包應用程式可讓使用者在其裝置上儲存數位傳遞。
這些階段會由商家產生，並透過電子郵件、Url 或商家本身的 iOS 應用程式傳送給客戶。 這些階段可代表各種不同的事物，從電影票證到忠誠度卡，再來走走。 PassKit 架構可讓開發人員以程式設計方式與通過互動。

本檔介紹使用 PassKit API 搭配 Xamarin 的錢包。

 [![錢包會儲存並組織手機上的所有通過](passkit-images/image1.png)](passkit-images/image1.png#lightbox)

## <a name="requirements"></a>需求

本檔中討論的 PassKit 功能需要 iOS 6 和 Xcode 4.5，以及 Xamarin. iOS 6.0。

## <a name="introduction"></a>簡介

PassKit 所解決的主要問題是條碼的散發與管理。 目前使用條碼的一些真實世界範例包括：

- **線上購買 movie 券** –客戶通常會透過電子郵件傳送代表其票證的條碼。 此條碼會列印出來，並將其移至要掃描的電影。
- **忠誠度卡** –客戶會在其錢包或錢包中攜帶一些不同的商店特定卡片，以便在購買商品時顯示和掃描。
- **優惠券** –優惠券會透過電子郵件、可列印的網頁、letterboxes，以及報紙和雜誌中的條碼散發。 客戶可將他們帶入掃描用的商店，以接收貨物、服務或退貨的折扣。
- Pass **pass –類似**于購買電影票證。

PassKit 提供上述每個案例的替代方案：

- **Movie 券** –購買之後，客戶會透過電子郵件或網站連結) ，將事件票證傳遞 (。 在電影的過程中，pass 將會自動在鎖定畫面上顯示為提醒，並且在電影抵達時，可輕鬆地取出並顯示在錢包中以進行掃描。
- **忠誠度卡** （而不是 (或除了提供實體卡片) 之外，商店也可以透過電子郵件或在網站登入) 商店卡片通過之後發出 (。 商店可以提供額外的功能，例如透過推播通知更新通過的帳戶餘額，以及使用地理位置服務，當客戶接近商店位置時，傳遞就會自動出現在鎖定畫面上。
- **贈券** –您可以使用獨特的特性輕鬆地產生優惠券 pass，以協助進行追蹤，並透過電子郵件或網站連結散發。 當使用者在特定位置附近，並（或）指定日期 (（例如到期日接近) 時），下載的優惠券會自動出現在鎖定畫面上。 因為優惠券是儲存在使用者的手機上，所以它們一律很方便，因此不會變錯。 優惠券可能會鼓勵客戶下載隨附的應用程式，因為您可以將 App Store 連結納入通過，以提升與客戶的互動。
- Pass pass –在線上簽入程式之後，客戶會透過電子郵件或連結收到他們**的通過 pass** 。 傳輸提供者所提供的附屬應用程式可能包含簽入程式，也可讓客戶執行額外的功能，例如選擇其基座或食物。 傳輸提供者可以使用推播通知，以在延遲或取消傳輸時更新傳遞。 隨著採用時間的限制，pass 會在鎖定畫面上顯示為提醒，並提供快速存取權給 Pass。

在其核心中，PassKit 提供簡單且方便的方式，在您的 iOS 裝置上儲存及顯示條碼。 隨著額外的時間和位置鎖定畫面整合，推播通知和隨附的應用程式整合，可為非常精密的銷售、票證和帳單服務提供基礎。

## <a name="passkit-ecosystem"></a>PassKit 生態系統

PassKit 不只是 CocoaTouch 中的 API，而是應用程式、資料和服務的大型生態系統的一部分，可協助保護條碼和其他資料的安全共用與管理。 此高階圖表會顯示與建立和使用 pass 相關的不同實體：

 [![此高階圖表會顯示與建立和使用 pass 相關的實體](passkit-images/image2.png)](passkit-images/image2.png#lightbox)

生態系統的每個部分都有明確定義的角色：

- **錢包** – Apple 的內建 iOS 應用程式，可儲存和顯示通過。 這是轉譯用在真實世界中的唯一位置 (ie 會顯示條碼，以及通過) 中的所有當地語系化資料。
- **附屬應用程式** –由 pass 提供者所建立的 iOS 6 應用程式，可延伸其問題的傳遞功能，例如將值新增至商店卡片、變更座位通過或其他商務特定功能。 不需要隨附的應用程式就能派上用場。
- **您的伺服器** –可以產生和簽署傳遞的安全伺服器。 您的隨附應用程式可能會連線到您的伺服器，以產生新的通過或要求更新現有的行程。 您可以選擇性地執行錢包會呼叫更新行程的 web 服務 API。
- **APNS 伺服器** ：您的伺服器能夠使用 APNS，在指定的裝置上通知大量更新。 將通知推送至錢包，然後將其連線至您的伺服器，以取得變更的詳細資料。 附屬應用程式不需要為這項功能執行 APNS， (他們可以接聽  `PKPassLibraryDidChangeNotification` ) 。
- **管道應用** 程式–不會直接操作 (傳遞的應用程式（例如隨附的應用程式）會) ，但可以藉由辨識通過並允許將其新增至錢包，來改善其公用程式。 郵件用戶端、社交網路瀏覽器和其他資料匯總應用程式可能會遇到附件或傳遞的連結。

整個生態系統看起來很複雜，因此值得一提的是，某些元件是選擇性的，而且可能會有更簡單的 PassKit 實現。

## <a name="what-is-a-pass"></a>什麼是 Pass？

Pass 是代表票證、優惠券或卡片的資料集合。 它可能適用于個別 (的單一用途，因此會包含詳細資料，例如航班號和基座配置) 或可由任意數量的使用者共用的多個使用權杖 (例如折扣優惠券) 。 如需詳細說明，請閱讀 Apple 的「 [關於傳遞](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Bundle/Chapters/Introduction.html) 檔案」檔。

### <a name="types"></a>類型

目前有五種支援的型別，可在 [錢包] 應用程式中依行程的版面配置和上邊緣進行辨識：

- **事件票證** –小型半圓切除。
- **Boarding Pass**先出，您可以指定 (例如，在側邊的凹槽、傳輸特定的圖示。 匯流排、訓練、飛機) 。
- **商店卡片** –左上角，例如信用卡或轉帳卡。
- **優惠券** –沿著頂端 perforated。
- **一般** –與商店卡片相同，進位在上方。

這個螢幕擷取畫面中會顯示五個傳遞型別， (順序：贈券、generic、商店卡片、pass pass 和事件票證) ：

 [![這五個傳遞類型會顯示在此螢幕擷取畫面中](passkit-images/image3.png)](passkit-images/image3.png#lightbox)

### <a name="file-structure"></a>檔案結構

傳遞檔案實際上是副檔名為 **pkpass** 的 ZIP 封存，其中包含一些特定的 JSON 檔案 (必要的) 、各種不同的影像檔 (選擇性) 以及當地語系化的字串 (也可以選擇) 。

- **pass.js開啟** -必要。 包含傳遞的所有資訊。
- **manifest.js開啟** -必要。 包含傳遞中每個檔案的 SHA1 雜湊，但簽章檔案和這個檔案 ( # B0 的) 。
- 簽**章-必要**。 使用 iOS 布建  `manifest.json` 入口網站中產生的憑證簽署檔案來建立。
- **logo.png** –選擇性。
- **background.png** –選擇性。
- **icon.png** –選擇性。
- 可**當地語系化的字串**檔案–選擇性。

傳遞檔案的目錄結構如下所示 (這是 ZIP 封存) 的內容：

 [![傳遞檔的目錄結構如下所示](passkit-images/image4.png)](passkit-images/image4.png#lightbox)

### <a name="passjson"></a>pass.js開啟

JSON 的格式是因為通常會在伺服器上建立傳遞，這表示產生程式碼在伺服器上是平臺中立的。 每個階段中的三個重要資訊片段如下：

- **teamIdentifier** –這會將您產生的所有傳遞連結至您的 App Store 帳戶。 此值會顯示在 iOS 布建入口網站中。
- **passTypeIdentifier** –如果您產生一種以上的類型) ，請在布建入口網站中註冊群組傳遞 (。 例如，咖啡廳可能會建立商店智慧卡類型，以允許客戶獲得忠誠度信用額度，但也可以使用個別的優惠券 pass 類型來建立和散發折扣優惠券。 同一咖啡廳甚至可能會保存即時音樂事件，並為這些活動發出事件票證傳遞。
- **serialNumber** –此中的唯一字串  `passTypeidentifier` 。 值對錢包而言是不透明的，但在與您的伺服器通訊時，請務必追蹤特定的通過。

每個階段中都有大量的其他 JSON 金鑰，例如如下所示的範例：

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

僅支援2D 格式： PDF417、Aztec、QR。 Apple 會宣告1D 條碼 unsuited，以掃描 backlit 電話畫面。

條碼底下顯示的替代文字是選擇性的，有些商家希望能夠手動讀取/輸入。

ISO-8859-1 編碼是最常見的，請檢查將讀取您的行程的掃描系統所使用的編碼方式。

### <a name="relevancy-lock-screen"></a>鎖定畫面) 的相關性 (

有兩種類型的資料可能會在鎖定畫面上顯示傳遞：

 **位置**

在 Pass 中最多可以指定10個位置，例如，客戶經常造訪的商店，或是電影院或機場的位置。 客戶可以透過隨附的應用程式設定這些位置，或提供者可以從使用方式資料 (（如果是以客戶的許可權) 收集）。

當 [通過] 顯示在鎖定畫面時，就會計算出範圍，如此當使用者離開該區域時，就會在鎖定畫面中隱藏傳遞。 Radius 系結至傳遞樣式，以防止濫用。

 **日期及時間**

只可在傳遞中指定一個日期/時間。 日期和時間適合用來觸發「上線行程」和「事件票證」的鎖定畫面提醒。

可以透過 push 或 PassKit API 來更新，如此一來，就可以在多用途票證 (（例如，季節或體育) 的季節票證）時更新日期/時間。

### <a name="localization"></a>當地語系化

將傳遞轉譯成多個語言類似于將 iOS 應用程式當地語系化–使用延伸模組建立語言特定目錄 `.lproj` ，並將當地語系化元素放在內部。 文字翻譯應輸入至檔案 `pass.strings` ，而當地語系化影像的名稱應與在傳遞根目錄中取代的影像相同。

## <a name="security"></a>安全性

Pass 會使用您在 iOS 布建入口網站中產生的私人憑證進行簽署。 簽署傳遞的步驟如下：

1. 為 pass 目錄中的每個檔案計算 SHA1 雜湊 (不包括  `manifest.json` 或檔案  `signature` ，在此階段中，無論如何都不應該存在) 。
1. 寫入  `manifest.json` 為每個檔案名的 JSON 索引鍵/值清單及其雜湊。
1. 使用憑證來簽署檔案  `manifest.json` ，並將結果寫入至名為的檔案  `signature` 。
1. 將所有專案壓縮並提供所產生檔案的 `.pkpass` 副檔名。

由於您的私密金鑰是簽署傳遞的必要項，因此只能在您所控制的安全伺服器上完成此程式。 請勿散發您的金鑰，以嘗試在應用程式中產生 pass。

## <a name="configuration-and-setup"></a>組態和設定

本節包含的指示可協助您設定布建詳細資料，並建立您的第一個階段。

### <a name="provisioning-passkit"></a>布建 PassKit

為了讓 pass 進入 App Store，必須連結至開發人員帳戶。 這需要兩個步驟：

1. 必須使用稱為「傳遞類型識別碼」的唯一識別碼來註冊 pass。
1. 必須產生有效的憑證，才能使用開發人員的數位簽章來簽署 pass。

若要建立傳遞類型識別碼，請執行下列動作。

#### <a name="create-a-pass-type-id"></a>建立傳遞類型識別碼

第一個步驟是針對要支援的每一種不同 _類型_ 的傳遞，設定傳遞類型識別碼。 傳遞識別碼 (或傳遞類型識別碼) 會建立傳遞的唯一識別碼。 我們將使用此識別碼，透過憑證將 pass 連結至您的開發人員帳戶。

1. 在 iOS 布建  [入口網站的 [憑證]、[識別碼] 和 [設定檔] 區段](https://developer.apple.com/account/overview.action)中，流覽至 [  **識別碼** ] 並選取 [  **傳遞類型** 識別碼]。 然後選取 **+** 按鈕來建立新的傳遞類型： [ ![ 建立新的傳遞類型](passkit-images/passid.png)](passkit-images/passid.png#lightbox)

2. 提供 **描述** (名稱) 和 **識別碼** (傳遞的唯一字串) 。 請注意，在此範例中，所有的傳遞類型識別碼都必須以字串開頭 `pass.` `pass.com.xamarin.coupon.banana` ：請[ ![ 提供描述和識別碼](passkit-images/register.png)](passkit-images/register.png#lightbox)

3. 按下 [ **註冊** ] 按鈕以確認傳遞識別碼。

#### <a name="generate-a-certificate"></a>產生憑證

若要為此傳遞類型識別碼建立新的憑證，請執行下列動作：

1. 從清單中選取新建立的傳遞識別碼，然後按一下 [**編輯**]： [ ![ 從清單中選取新的傳遞識別碼](passkit-images/pass-done.png)](passkit-images/pass-done.png#lightbox)

    然後，選取 [**建立憑證 ...** ] :

    [![選取 [建立憑證]](passkit-images/cert-dist.png)](passkit-images/cert-dist.png#lightbox)

2. 依照下列步驟來建立憑證簽署要求 (CSR) 。
  
3. 按開發人員入口網站上的 [ **繼續** ] 按鈕，然後上傳 CSR 以產生您的憑證。

4. 下載憑證，然後按兩下以將它安裝在您的 keychain 中。

既然我們已為此傳遞類型識別碼建立憑證，下一節將說明如何以手動方式建立傳遞。

如需布建錢包的詳細資訊，請參閱 [使用功能](~/ios/deploy-test/provisioning/capabilities/wallet-capabilities.md) 指南。

### <a name="create-a-pass-manually"></a>手動建立傳遞

現在我們已建立了 Pass 型別，我們可以在模擬器或裝置上手動製作 pass 以進行測試。 建立 pass 的步驟如下：

- 建立包含傳遞檔案的目錄。
- 建立檔案的 pass.js，其中包含所有必要的資料。
- 如有需要，請在資料夾 (中包含映射) 。
- 計算資料夾中每個檔案的 SHA1 雜湊，並寫入 manifest.js。
- 使用下載的憑證 p12 檔案登入 manifest.js。
- 壓縮目錄的內容，並使用 pkpass 副檔名重新命名。

本文的 [範例程式碼](/samples/xamarin/ios-samples/passkit) 中會有一些原始程式檔，可用來產生 pass。 使用 CreateAPassManually 目錄的目錄中的檔案 `CouponBanana.raw` 。 下列檔案存在：

 [![這些檔案存在](passkit-images/image18.png)](passkit-images/image18.png#lightbox)

開啟 pass.js開啟並編輯 JSON。 您必須至少更新和， `passTypeIdentifier` `teamIdentifer` 才能符合您的 Apple 開發人員帳戶。

```json
"passTypeIdentifier" : "pass.com.xamarin.coupon.banana",
"teamIdentifier" : "?????????",
```

然後，您必須計算每個檔案的雜湊，並建立檔案 `manifest.json` 。 當您完成時，它看起來會像這樣：

```json
{
  "icon@2x.png" : "30806547dcc6ee084a90210e2dc042d5d7d92a41",
  "icon.png" : "87e9ffb203beb2cce5de76113f8e9503aeab6ecc",
  "pass.json" : "c83cd1441c17ecc6c5911bae530d54500f57d9eb",
  "logo.png" : "b3cd8a488b0674ef4e7d941d5edbb4b5b0e6823f",
  "logo@2x.png" : "3ccd214765507f9eab7244acc54cc4ac733baf87"
}
```

接著，您必須使用為此傳遞類型識別碼產生的憑證 ( p12 檔案) ，來產生此檔案的簽章。

#### <a name="signing-on-a-mac"></a>在 Mac 上簽署

從[Apple 下載](https://developer.apple.com/downloads/index.action?name=Passbook)網站下載**錢包 Seed Support 教材**。 使用 `signpass` 工具將您的資料夾轉換為通過 (這也會計算 SHA1 雜湊，並將輸出壓縮成 pkpass 檔案) 。

#### <a name="testing"></a>測試

如果您要檢查這些工具的輸出 (藉由將檔案名設定為 .zip，然後將其開啟) ，您會看到下列檔案 (注意新增 `manifest.json` 和檔案 `signature`) ：

 [![檢查這些工具的輸出](passkit-images/image19.png)](passkit-images/image19.png#lightbox)

一旦您簽署、壓縮和重新命名檔案， (例如 若要 `BananaCoupon.pkpass`) 您可以將它拖曳到模擬器進行測試，或以電子郵件傳送給自己，以在真實裝置上取得。 您應該會看到 **新增** pass 的畫面，如下所示：

 [![新增 [通過] 畫面](passkit-images/image20.png)](passkit-images/image20.png#lightbox)

一般情況下，該程式會在伺服器上自動進行，不過，手動傳遞建立可能是小型企業的選項，而小型企業只建立不需要支援後端伺服器的贈券。

## <a name="wallet"></a>電子錢包

錢包是 PassKit 生態系統的核心部分。 這個螢幕擷取畫面顯示空的錢包，以及 pass 清單和個別行程的外觀：

 [![此螢幕擷取畫面顯示空的錢包，以及 pass 清單和個別行程的外觀](passkit-images/image21.png)](passkit-images/image21.png#lightbox)

錢包的功能包括：

- 它是用來進行掃描的唯一位置，它是用來進行掃描的條碼。
- 使用者可以變更更新的設定。 若已啟用，推播通知可能會觸發更新傳遞中的資料。
- 使用者可以啟用或停用鎖定畫面整合。 啟用時，這可讓傳遞根據內嵌于傳遞的相關時間和位置資料，自動顯示在鎖定畫面上。
- 如果傳遞 JSON 中提供了 web 伺服器 URL，則傳遞的反向就支援提取到重新整理。
- 如果在 pass JSON 中提供應用程式的識別碼，則可 (或下載隨附的應用程式) 。
- 您可以)  (，以刻意的切碎動畫來刪除傳遞。

## <a name="adding-passes-into-wallet"></a>將 pass 加入錢包

您可以透過下列方式將 pass 新增至錢包：

- **管道應用程式** -這些不會直接操作傳遞，它們只是載入傳遞檔案，並提供使用者將其新增至錢包的選項。 

- **附屬應用程式** -這些是由提供者所撰寫，以發佈通過，並提供其他功能來流覽或編輯它們。 Xamarin iOS 應用程式具有 PassKit API 的完整存取權，可建立和操作通過。 然後可以使用來將 pass 新增至錢包 `PKAddPassesViewController` 。 本檔的 **隨附應用程式** 一節會更詳細地說明此程式。

### <a name="conduit-applications"></a>管道應用程式

管道應用程式是可代表使用者接收行程的中繼應用程式，應該進行程式設計以辨識其內容類型，並提供可新增至錢包的功能。 管道應用程式的範例包括：

- **Mail** –將附件辨識為 Pass。
- **Safari** –按一下 [傳遞 URL] 連結時，會辨識 Pass 內容類型。
- **其他自訂應用程式** –可接收附件或開啟連結 (社交媒體用戶端、郵件讀者等) 的任何應用程式。

此螢幕擷取畫面顯示 iOS 6 中的 **郵件** 如何辨識傳遞附件，以及在接觸) 提供的 (時， **將其新增** 至錢包。

 [![此螢幕擷取畫面顯示 iOS 6 中的郵件如何辨識傳遞附件](passkit-images/image22.png)](passkit-images/image22.png#lightbox)

 [![此螢幕擷取畫面顯示郵件如何提供將傳遞附件新增至錢包](passkit-images/image23.png)](passkit-images/image23.png#lightbox)

如果您要建立的應用程式可能是通過的管道，可以透過下列方式辨識：

- **副檔名** -. pkpass
- **MIME 類型** -application/vnd.openxmlformats-officedocument.spreadsheetml.sheet. pkpass
- **UTI** – pkpass

管道應用程式的基本作業是取出傳遞檔案並呼叫 PassKit， `PKAddPassesViewController` 讓使用者可以選擇將 pass 新增至自己的錢包。 下一節的 **附屬應用程式**涵蓋此 view controller 的執行。

管道應用程式不需要針對特定的傳遞類型識別碼進行布建，與附屬應用程式的使用方式相同。

## <a name="companion-applications"></a>附屬應用程式

附屬應用程式提供使用 Pass 的額外功能，包括建立傳遞、更新與通過相關聯的資訊，以及管理與應用程式相關聯的傳遞。

附屬應用程式不應該嘗試複製錢包的功能。 它們並非用來顯示掃描的傳遞。

本節的其餘部分將說明如何建立與 PassKit 互動的基本附屬應用程式。

### <a name="provisioning"></a>佈建

因為錢包是商店技術，所以必須個別布建應用程式，且不能使用小組布建設定檔或萬用字元應用程式識別碼。 請參閱使用 [功能](~/ios/deploy-test/provisioning/capabilities/wallet-capabilities.md) 指南，為錢包應用程式建立唯一的應用程式識別碼和布建設定檔。

### <a name="entitlements"></a>權利

**Plist**檔案應該包含在所有最近的 Xamarin 專案中。 若要加入新的 plist 檔案，請依照「使用 [權利](~/ios/deploy-test/provisioning/entitlements.md) 指南」中的步驟執行。

若要設定權利，請執行下列動作：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

按兩下 Solution Pad 中的 **plist** 檔案，以開啟 plist 編輯器：

![Plst 編輯器](passkit-images/image31.png)

在 [錢包] 區段下，選取 [ **啟用錢包** ] 選項

![啟用錢包權利](passkit-images/image32.png)

預設選項是讓您的應用程式允許所有的傳遞類型。 不過，您可以限制您的應用程式，而且只允許一部分的 team pass 類型。 若要啟用這項選擇，請選取 [ **允許 team pass 類型的子集]** ，然後輸入您想要允許之子集的傳遞類型識別碼。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

按兩下 **plist** 檔案以開啟 XML 原始檔。

若要加入錢包權利，請在下拉式清單中將 **屬性** 設為 `Passbook Identifiers` ，以自動設定 **類型** `Array` 。 然後，將字串 **值** 設定為 `$(TeamIdentifierPrefix)*` ：

![啟用錢包權利](passkit-images/image33.png)

這可讓您的應用程式允許所有票卡類型。 若要限制您的應用程式而只允許部分小組票卡類型，請將字串值設定為：

`$(TeamIdentifierPrefix)pass.$(CFBundleIdentifier)`

其中 `pass.$(CFBundleIdentifier)` 是先前建立的傳遞識別碼[above](~/ios/platform/passkit.md)

-----

### <a name="debugging"></a>偵錯

如果您在部署應用程式時遇到問題，請確認您使用的是正確的布建**設定檔**，而且在 iPhone 套件組合 `Entitlements.plist` **簽署**選項中選取為**自訂權利**檔案。

如果您在部署時遇到此錯誤：

```
Installation failed: Your code signing/provisioning profiles are not correctly configured (error: 0xe8008016)
```

然後， `pass-type-identifiers` 權利陣列 (不正確，或不符合布建 **設定檔**) 。 確認 Pass 類型識別碼和您的小組識別碼正確無誤。

## <a name="classes"></a>類別

下列 PassKit 類別可供應用程式存取階段使用：

- **PKPass** – Pass 的實例。
- **PKPassLibrary** –提供 API 以存取裝置上的傳遞。
- **PKAddPassesViewController** –用來顯示使用者在其錢包中儲存的傳遞。
- **PKAddPassesViewControllerDelegate** – Xamarin iOS 開發人員

## <a name="example"></a>範例

請參閱本文的 [範例程式碼](/samples/xamarin/ios-samples/passkit) 中的 PassLibrary 專案。 它會示範下列在錢包附屬應用程式中所需的常見功能：

### <a name="check-that-wallet-is-available"></a>檢查錢包是否可用

IPad 上不提供錢包，因此應用程式應該在嘗試存取 PassKit 功能之前先檢查。

```csharp
if (PKPassLibrary.IsAvailable) {
    // create an instance and do stuff...
}
```

### <a name="creating-a-pass-library-instance"></a>建立傳遞程式庫實例

PassKit 程式庫並非 singleton，應用程式應該建立並儲存和實例，以存取 PassKit API。

```csharp
if (PKPassLibrary.IsAvailable) {
    library = new PKPassLibrary ();
    // do stuff...
}
```

### <a name="get-a-list-of-passes"></a>取得通過的清單

應用程式可以向程式庫要求傳遞清單。 這份清單會由 PassKit 自動篩選，因此您只會看到已使用您的小組識別碼建立的傳遞，以及您權利中列出的傳遞。

```csharp
var passes = library.GetPasses ();  // returns PKPass[]
```

請注意，模擬器不會篩選傳回的傳遞清單，因此應該一律在實際裝置上測試這個方法。 這份清單可以顯示在 Ios uitableview 範例中。 [範例應用程式](/samples/xamarin/ios-samples/passkit)在新增兩個優惠券之後看起來像這樣：

 [![新增兩個優惠券之後，範例應用程式看起來像這樣](passkit-images/image29.png)](passkit-images/image29.png#lightbox)

### <a name="displaying-passes"></a>顯示通過

有一組有限的資訊可用於轉譯附屬應用程式內的 pass。

從這組標準屬性中進行選擇，以顯示通過的清單，如範例程式碼所示。

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

此字串在 [範例](/samples/xamarin/ios-samples/passkit)中會顯示為警示：

 [![範例中的已選取優惠券警示](passkit-images/image30.png)](passkit-images/image30.png#lightbox)

您也可以使用 `LocalizedValueForFieldKey()` 方法，從您所設計的傳遞中的欄位取出資料 (因為您會知道哪些欄位應該存在) 。 範例程式碼不會顯示此範例。

### <a name="loading-a-pass-from-a-file"></a>從檔案載入傳遞

因為只能以使用者的許可權將 pass 新增至錢包，所以必須顯示視圖控制器才能讓他們決定。 此程式碼用於範例中的 [ **新增** ] 按鈕，以載入內嵌在應用程式中的預先建立的傳遞 (您應該將它取代為您已簽署的) ：

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

傳遞會以 [ **新增** ] 和 [ **取消** ] 選項顯示：

 [![使用 [新增] 和 [取消] 選項提出的傳遞](passkit-images/image20.png)](passkit-images/image20.png#lightbox)

### <a name="replace-an-existing-pass"></a>取代現有的傳遞

取代現有的傳遞不需要使用者的許可權，不過如果傳遞不存在，將會失敗。

```csharp
if (library.Contains (newPass)) {
     library.Replace (newPass);
}
```

### <a name="editing-a-pass"></a>編輯通過

PKPass 不是可變動的，因此您無法在程式碼中更新傳遞物件。 若要改變傳遞中的資料，應用程式必須能夠存取可以記錄傳遞的 web 伺服器，並使用應用程式可下載的更新值產生新的傳遞檔案。

必須在伺服器上進行傳遞檔案建立，因為必須使用必須保持私密且安全的憑證來簽署傳遞。

一旦產生更新的傳遞檔案之後，請使用 `Replace` 方法來覆寫裝置上的舊資料。

### <a name="display-a-pass-for-scanning"></a>顯示掃描的傳遞

如先前所述，只有錢包可以顯示掃描的行程。 您可以使用方法來顯示傳遞 `OpenUrl` ，如下所示：

 `UIApplication.SharedApplication.OpenUrl (p.PassUrl);`

### <a name="receiving-notifications-of-changes"></a>接收變更的通知

應用程式可以使用來接聽對傳遞程式庫所做的變更 `PKPassLibraryDidChangeNotification` 。 變更可能是因為在背景中觸發更新的通知所造成，因此在您的應用程式中接聽它們是很好的作法。

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

註冊通知時必須傳遞程式庫實例，因為 PKPassLibrary 並非 singleton。

## <a name="server-processing"></a>伺服器處理

建立伺服器應用程式以支援 PassKit 的詳細討論已超出本簡介文章的範圍。

請參閱 [dotnet-passbook](https://github.com/tomasmcguinness/dotnet-passbook) 開放原始碼 c # 伺服器端程式碼。

## <a name="push-notifications"></a>推播通知

使用推播通知更新階段的詳細討論已超出本簡介文章的範圍。

當需要更新時，您需要執行由 Apple 所定義的 REST API，以回應來自錢包的 web 要求。

如需詳細資訊，請參閱 Apple 的 [更新傳遞](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/PassKit_PG/Updating.html#//apple_ref/doc/uid/TP40012195-CH5-SW1) 指南。

## <a name="summary"></a>摘要

本文介紹了 PassKit，並概述一些原因，並說明必須針對完整 PassKit 解決方案實行的不同部分。 其中說明設定 Apple 開發人員帳戶以建立 pass 所需的步驟、手動進行傳遞的流程，以及如何從 Xamarin 應用程式存取 PassKit Api。

## <a name="related-links"></a>相關連結

- [適用于開發人員的錢包](https://developer.apple.com/wallet/)
- [PassKit 範例](/samples/xamarin/ios-samples/passkit)
- [錢包開發人員指南](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/PassKit_PG/index.html#//apple_ref/doc/uid/TP40012195-CH1-SW1)
- [架構– Apple Pay 和錢包 (WWDC 影片) ](https://developer.apple.com/videos/frameworks/apple-pay-and-wallet)
- [PassKit 架構參考](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Framework/_index.html)
- [Passbook Web 服務參考](https://developer.apple.com/library/prerelease/ios/#documentation/PassKit/Reference/PassKit_WebService/WebService.html)
- [關於傳遞檔案](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Bundle/Chapters/Introduction.html)
- [dotnet-passbook](https://github.com/tomasmcguinness/dotnet-passbook)，這是用來產生 iOS 錢包套件的開放原始碼程式庫
- [iOS 6 簡介](~/ios/platform/introduction-to-ios6/index.md)