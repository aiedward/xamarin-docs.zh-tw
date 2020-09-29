---
title: iOS 6 中對 StoreKit 所做的變更
description: iOS 6 引進了商店套件 API 的兩項變更：在您的應用程式中顯示 iTunes (和 App Store/iBookstore) 產品的能力，以及 Apple 將在其中裝載可下載檔案的新應用程式內購買選項。 本檔說明如何使用 Xamarin 來執行這些功能。
ms.prod: xamarin
ms.assetid: 253D37D7-44C7-D012-3641-E15DC41C2699
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 85e6be722b0d2ddbd2c63955bd19b2907e062156
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433010"
---
# <a name="changes-to-storekit-in-ios-6"></a>iOS 6 中對 StoreKit 所做的變更

_iOS 6 引進了商店套件 API 的兩項變更：在您的應用程式中顯示 iTunes (以及 App Store/iBookstore) 產品，以及 Apple 將在其中裝載可下載檔案的新應用程式內購買選項。本檔說明如何使用 Xamarin 來執行這些功能。_

IOS6 中儲存套件的主要變更為下列兩項新功能：

- **應用程式內顯示 & 購買的內容** -使用者無須離開您的應用程式，即可購買及下載應用程式、音樂、書籍和其他 iTunes 內容。 您也可以連結至自己的應用程式，以升階購買或只鼓勵評論和評等。
- **應用程式內購買** 裝載的內容– Apple 將儲存和傳遞與您的應用程式內購買產品相關聯的內容，因此不需要個別的伺服器來裝載您的檔案、自動支援背景下載，並可讓您撰寫較少的程式碼。

如需 StoreKit Api 的詳細涵蓋範圍，請參閱 [應用程式內購買](~/ios/platform/in-app-purchasing/index.md) 指南。

## <a name="requirements"></a>需求

本檔中討論的商店套件功能需要 iOS 6 和 Xcode 4.5，以及 Xamarin. iOS 6.0。

## <a name="in-app-content-display--purchasing"></a>& 購買的應用程式內內容顯示

IOS 中新的應用程式內購買功能，可讓使用者在您的應用程式內查看產品資訊，以及購買或下載產品。
先前的應用程式必須觸發 iTunes、App Store 或 iBookstore，這會導致使用者離開原始應用程式。 這項新功能會在您的應用程式完成時，自動將使用者傳回到您的應用程式。

[![在購買後自動返回應用程式](changes-to-storekit-images/image1.png)](changes-to-storekit-images/image1.png#lightbox)

使用方式的範例包括：

- **鼓勵使用者對您的應用程式進行評分** –您可以開啟 App Store 頁面，讓使用者無須離開就能對您的應用程式進行評分和審核。
- **跨升階應用程式** –允許使用者查看您發佈的其他應用程式，並立即購買/下載。
- **協助使用者尋找並下載內容** –協助使用者購買應用程式尋找、管理或匯總 (例如的內容。 音樂相關的應用程式可以提供歌曲的播放清單，並可讓您在應用程式) 內購買每首歌。

一旦顯示之後， `SKStoreProductViewController` 使用者就可以與產品資訊互動，就好像它們是在 iTunes、App Store 或 iBookstore。 使用者可以：

- 查看應用程式的螢幕擷取畫面 () 、
- 音樂、電視節目和電影的範例歌曲或影片 () 
- 閱讀 (並撰寫) 評論
- 購買 & 下載，這完全是在 view controller 和 Store 套件內進行。

中的某些選項 `SKStoreProductViewController` 仍會強制使用者離開您的應用程式，並開啟相關的商店應用程式，例如按一下 **相關產品** 或應用程式的 **支援** 連結。

### <a name="skstoreproductviewcontroller"></a>SKStoreProductViewController

在任何應用程式中顯示產品的 API 很簡單：只需要建立並顯示 `SKStoreProductViewController` 。 遵循下列步驟來建立和顯示產品：

1. 建立  `StoreProductParameters` 物件，以將參數傳遞至 view 控制器，包括在函式 `productId` 中。
1. 具現化 `SKProductViewController` 。 將它指派給類別層級欄位。
1. 將處理常式指派給視圖控制器的  `Finished` 事件，這應該會關閉 view 控制器。 當使用者按下 [取消] 時，就會呼叫此事件。或在 view controller 內部完成交易。
1. 呼叫  `LoadProduct` 傳入  `StoreProductParameters` 和完成處理常式的方法。 完成處理常式應該會檢查產品要求是否成功，如果是，則以強制回應方式呈現  `SKProductViewController` 。 如果無法抓取產品，則應該新增適當的錯誤處理。

### <a name="example"></a>範例

本文之*StoreKit*範例程式碼中的*ProductView*專案會實作為 `Buy` 接受任何產品的 Apple ID 並顯示的方法 `SKStoreProductViewController` 。 下列程式碼會顯示任何指定 Apple ID 的產品資訊：

```csharp
void Buy (int productId)
{
    var spp = new StoreProductParameters(productId);
    var productViewController = new SKStoreProductViewController ();
    // must set the Finished handler before displaying the view controller
    productViewController.Finished += (sender, err) => {
        // Apple's docs says to use this method to close the view controller
        this.DismissModalViewControllerAnimated (true);
    };
    productViewController.LoadProduct (spp, (ok, err) => { // ASYNC !!!
        if (ok) {
            PresentModalViewController (productViewController, true);
        } else {
            Console.WriteLine (" failed ");
            if (err != null)
                Console.WriteLine (" with error " + err);
        }
    });
}
```

執行時，應用程式看起來會像下面的螢幕擷取畫面：完全在下列情況下進行下載或購買 `SKStoreProductViewController` ：

[![應用程式在執行時看起來像這樣：](changes-to-storekit-images/image2.png)](changes-to-storekit-images/image2.png#lightbox)

### <a name="supporting-older-operating-systems"></a>支援較舊的作業系統

範例應用程式包含的程式碼會顯示如何在舊版 iOS 中開啟 App Store、iTunes 或 iBookstore。 使用 `OpenUrl` 方法來開啟適當製作的 **itunes.com** URL。

您可以執行版本檢查來判斷要執行的程式碼，如下所示：

```csharp
if (UIDevice.CurrentDevice.CheckSystemVersion (6,0)) {
    // do iOS6+ stuff, using SKStoreProductViewController as shown above
} else {
    // don't do stuff requiring iOS 6.0, use the old syntax
    // (which will take the user out of your app)
    var nsurl = new NSUrl("http://itunes.apple.com/us/app/angry-birds/id343200656?mt=8");
    UIApplication.SharedApplication.OpenUrl (nsurl);
}
```

### <a name="errors"></a>Errors

如果您使用的 Apple ID 無效，將會發生下列錯誤，這可能會造成混淆，因為它暗示了某種網路或驗證的問題。

 `Error Domain=SKErrorDomain Code=5 "Cannot connect to iTunes Store"`

### <a name="reading-objective-c-documentation"></a>閱讀目標-C 檔

在 Apple 開發人員入口網站上閱讀商店套件的開發人員，將會看到與這項新功能相關的通訊協定- [SKStoreProductViewControllerDelegate](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/SKITunesProductViewControllerDelegate_ProtocolRef/Reference/Reference.html) –討論。 委派通訊協定只有一個方法（productViewControllerDidFinish），已公開為 `Finished` `SKStoreProductViewController` 在 Xamarin 中的事件。

## <a name="determining-apple-ids"></a>判斷 Apple Id

所需的 Apple ID `SKStoreProductViewController` 是一個 *數位* (不會與套件組合識別碼混淆，例如 "mwc2012" ) 。 您可以使用幾種不同的方式來找出您想要顯示之產品的 Apple ID，如下所示：

### <a name="itunesconnect"></a>iTunesConnect

針對您發佈的應用程式，您可以在 iTunes Connect 中輕鬆找到 **APPLE ID** ：

[![在 iTunes Connect 中尋找 Apple ID](changes-to-storekit-images/image3.png)](changes-to-storekit-images/image3.png#lightbox)

 <a name="Search_API"></a>

### <a name="search-api"></a>搜尋 API

Apple 提供動態搜尋 API 來查詢 App Store、iTunes 和 iBookstore 中的所有產品。 您可以在 Apple 的分支機搆資源中找到有關如何存取搜尋 API 的資訊，雖然 API 會公開給任何人 (而不只是註冊的關係企業) 。 您可以剖析產生的 JSON，以探索 `trackId` 要搭配使用的 APPLE ID `SKStoreProductViewController` 。

結果也會包含其他中繼資料，包括可用來在您的應用程式中呈現產品的顯示資訊和圖片 Url。

以下是一些範例：

- **iBooks 應用程式**– [ https://itunes.apple.com/search?term=ibooks&amp ; entity = software &amp; country = us](https://itunes.apple.com/search?term=ibooks&amp;entity=software&amp;country=us)
- **點和 Kangaroo ibook store** – [ https://itunes.apple.com/search?term=dot+and+the+kangaroo&amp ; entity = 電子書 &amp; country = us](https://itunes.apple.com/search?term=dot+and+the+kangaroo&amp;entity=ebook&amp;country=us)

### <a name="enterprise-partner-feed"></a>企業合作夥伴摘要

Apple 以可供下載的資料庫一般檔案形式，為核准合作夥伴提供所有產品的完整資料傾印。 如果您符合企業合作夥伴摘要的存取權，則可在該資料集中找到任何產品的 Apple ID。

企業合作夥伴摘要的許多使用者都是 [分支機搆方案](https://www.apple.com/itunes/affiliates) 的成員，可讓您贏得產品銷售的傭金。 `SKStoreProductViewController` 在撰寫) 時，不支援分支機搆識別碼 (。

### <a name="direct-product-links"></a>直接產品連結

您可以從 iTunes Preview URL 連結推斷產品的 Apple ID。
在 [應用程式]、[音樂] 或 [書籍] 的任何 iTunes 產品 (連結中，) 尋找開頭為的 URL 部分 `id` ，並使用接下來的數位。

例如，iBooks 的直接連結是

```csharp
http://itunes.apple.com/us/app/ibooks/id364709193?mt=8
```

且 Apple ID 為 **364709193**。 同樣地，在 MWC2012 應用程式中，直接連結是

```csharp
http://itunes.apple.com/us/app/mwc-2012-unofficial/id496963922?mt=8
```

且 Apple ID 為 **496963922**。

## <a name="in-app-purchase-hosted-content"></a>應用程式內購買託管內容

如果您的應用程式內購買包含可下載的內容 (例如書籍或其他媒體、遊戲層級的藝術和設定，或其他大型檔案) 那麼這些檔案就會裝載在您的 web 伺服器上，而且應用程式必須併入程式碼，才能在購買後安全地下載這些檔案。 從 iOS 6 開始，Apple 會在其伺服器上裝載您的檔案，而不需要個別的伺服器。 這項功能僅適用于非取用產品 (無法取用或訂用帳戶) 。 使用 Apple 主機服務的優點包括：

- 節省裝載 & 頻寬成本。
- 可能比您目前使用的伺服器主機更具擴充性。
- 較少撰寫程式碼，因為您不需要建立任何伺服器端處理。
- 系統會為您執行背景下載。

注意：不支援在 iOS 模擬器中測試裝載的應用程式內購買內容，因此您必須使用真實裝置進行測試。

### <a name="hosted-content-basics"></a>託管內容基本概念

在 iOS 6 之前，有兩種方式可以提供產品 (在 [Xamarin 的應用程式內購買](~/ios/platform/in-app-purchasing/index.md) 檔) 中有更詳細的說明：

- **內建產品** -藉由購買來「解除鎖定」的功能，但內建于應用程式中的功能 (為程式碼或內嵌資源) 。 內建產品的範例包括已解除鎖定的相片篩選或遊戲內電源。
- **伺服器提供的產品** –購買之後，應用程式必須從您操作的伺服器下載內容。 此內容會在購買期間下載、儲存在裝置上，然後在提供產品的過程中轉譯。 範例包括書籍、雜誌問題或包含背景圖樣和設定檔的遊戲等級。

在 iOS 6 中，Apple 提供伺服器交付產品的變化：它們會在其伺服器上裝載您的內容檔案。 這可讓您更輕鬆地建立伺服器提供的產品，因為您不需要操作個別的伺服器，而儲存套件會提供您先前必須自行撰寫的背景下載功能。 若要利用 Apple 的主機，請為新的應用程式內購買產品啟用內容裝載，並修改商店套件程式碼以利用它。 接著會使用 Xcode 建立產品內容檔案，並將其上傳至 Apple 的伺服器，以供審查和發行。

[![組建與交付流程](changes-to-storekit-images/image4.png)](changes-to-storekit-images/image4.png#lightbox)

使用 App Store 提供 *託管內容* 的應用程式內購買，需要下列設定和設定：

- **ITunes Connect** –您 *必須* 已提供您的銀行和稅務資訊給 Apple，才能代表您收取所收集的資金。 然後，您可以設定要銷售的產品，並設定沙箱使用者帳戶以測試購買。  _您也必須為您想要使用 Apple 裝載的非使用中產品，設定託管內容_。
- **iOS 布建入口網站** –建立套件組合識別碼，並為您的應用程式啟用 App Store 存取，如同任何支援應用程式內購買的應用程式。
- **Store 套件** –將程式碼新增至您的應用程式，以顯示產品、購買產品和還原交易。  _在 iOS 6 Store 套件中，也會在背景中管理您的產品內容下載，以及進度更新。_
- **自訂程式碼** –用來追蹤客戶所進行的購買，並提供他們所購買的產品或服務。 利用新的 iOS 6 儲存套件類別  `SKDownload` ，例如取出 Apple 所裝載的內容。

下列各節說明如何使用本文的範例程式碼，從建立和上傳套件到管理購買和下載程式，以執行託管的內容。

### <a name="sample-code"></a>範例程式碼

範例專案 *HostedNonConsumables* StoreKitiOS6.zip) 中的 (使用裝載的內容。 應用程式會提供兩個「書籍章節」來銷售，也就是裝載在 Apple 伺服器上的內容。 內容是由文字檔和影像所組成，不過在實際的應用程式中可以使用更複雜的內容。

在購買之前、期間和之後，應用程式看起來像這樣：

 [![在購買之前、期間和之後，應用程式看起來像這樣](changes-to-storekit-images/image5.png)](changes-to-storekit-images/image5.png#lightbox)

系統會下載文字檔和影像，並將其複製到應用程式的 [檔] 目錄中。 如需可用於應用程式儲存之不同目錄的詳細資訊，請參閱 [檔案系統檔](~/ios/app-fundamentals/file-system.md)集。

## <a name="itunes-connect"></a>iTunes Connect

建立將使用 Apple 內容裝載的新產品時，請務必選取 **非** 取用產品類型。 其他產品類型不支援內容裝載。 此外，您不應該針對您銷售的 *現有* 產品啟用內容裝載;只開啟新產品的內容裝載。

 [![選取非取用產品類型](changes-to-storekit-images/image6.png)](changes-to-storekit-images/image6.png#lightbox)

輸入 **產品識別碼**。 稍後當您建立此產品的內容時，將需要此識別碼。

 [![輸入產品識別碼](changes-to-storekit-images/image7.png)](changes-to-storekit-images/image7.png#lightbox)

內容裝載是在 [詳細資料] 區段中設定。 在應用程式內購買上線之前，如果您想要取消 (（即使您已上傳某些測試內容) ，請取消核取 [ **使用 Apple 的主機內容** ] 核取方塊）。 不過，在應用程式內購買之後，無法移除內容裝載。

 [![使用 Apple 裝載內容](changes-to-storekit-images/image8.png)](changes-to-storekit-images/image8.png#lightbox)

當您開啟裝載內容之後，產品會進入 [ **正在等待上傳** 狀態] 並顯示此訊息：

 [![產品會輸入等候上傳狀態並顯示此訊息](changes-to-storekit-images/image9.png)](changes-to-storekit-images/image9.png#lightbox)

內容套件應使用 Xcode 建立，並使用封存工具上傳。 下一節建立時，會提供建立內容套件的指示 **。PKG**檔案。

## <a name="creating-pkg-files"></a>創建。PKG 檔案

您上傳至 Apple 的內容檔案必須符合下列限制：

- 大小不能超過 2 GB。
- 無法包含指向內容) 之外的可執行程式碼 (或符號連結。
- 必須正確格式化 (包括 **plist** 檔案) 且副檔名為 **pkg** 。 如果您使用 Xcode 來遵循這些指示，這會自動完成。

只要它們符合這些限制，您就可以新增許多不同的檔案和檔案類型。 內容會在傳遞至您的應用程式之前壓縮，並在您的程式碼存取之前解壓縮儲存套件。

上傳內容套件之後，可以將它取代為較新的內容。 您必須透過一般程式，將新內容上傳並提交以供審查/核准。 將 `ContentVersion` 更新內容套件中的欄位遞增，以表示它是較新的。

### <a name="xcode-in-app-purchase-content-projects"></a>Xcode 應用程式內購買內容專案

建立應用程式內購買產品的內容套件目前需要 Xcode。 不需要進行任何目標 C 程式碼;針對這些套件，Xcode 具有新的專案類型，其中只包含您的檔案和 plist。

我們的範例應用程式有銷售的書籍章節，每一章內容套件都會包含：

- 文字檔和
- 表示章節的影像。

首先從功能表選取 [檔案] **> [新增專案** ]，然後選擇 [ **應用程式內購買內容**：

 [![選擇應用程式內購買內容](changes-to-storekit-images/image10.png)](changes-to-storekit-images/image10.png#lightbox)

輸入 **產品名稱** 和 **公司識別碼** ，讓套件組合 **識別碼** 符合您在 iTunes Connect 中為此產品輸入的 **產品** 識別碼。

[![輸入名稱和識別碼](changes-to-storekit-images/image11.png)](changes-to-storekit-images/image11.png#lightbox)

現在您將擁有空白的 **應用程式內購買內容** 專案。 您可以用滑鼠右鍵按一下並 **新增檔案 ...** 或將其拖曳至 [ **專案導覽器**] 中。 請確定 **ContentVersion** 正確 (它應該從1.0 開始，但如果您稍後選擇更新內容，請記得將它) 遞增。

這個螢幕擷取畫面顯示 Xcode，其中包含專案中包含的內容檔案，以及在主視窗中顯示的 plist 專案：

[![此螢幕擷取畫面顯示 Xcode，其中包含專案中包含的內容檔案，以及在主視窗中顯示的 plist 專案](changes-to-storekit-images/image12.png)](changes-to-storekit-images/image12.png#lightbox)

一旦您已新增所有內容檔案，您可以儲存此專案，稍後再進行編輯，或開始上傳程式。

## <a name="uploading-pkg-files"></a>上傳。PKG 檔案

上傳內容套件最簡單的方式是使用 **Xcode Archive Tool**。 從功能表中選擇 [ **產品 >** 封存] 以開始：

![選擇 Archiven](changes-to-storekit-images/image13.png)

內容套件接著會出現在封存中，如下所示。
封存類型和圖示顯示這一行是 **應用程式內購買的內容**封存。 按一下 [ **驗證]。** 若要檢查內容套件是否有錯誤，而不實際執行上傳。

[![驗證套件](changes-to-storekit-images/image14.png)](changes-to-storekit-images/image14.png#lightbox)

使用您的 iTunes Connect 認證登入：

[![使用 iTunes Connect 認證登入](changes-to-storekit-images/image15.png)](changes-to-storekit-images/image15.png#lightbox)

選擇正確的應用程式和應用程式內購買，以將此內容與：

[![選擇正確的應用程式和應用程式內購買，以將此內容與](changes-to-storekit-images/image16.png)](changes-to-storekit-images/image16.png#lightbox)

您應該會看到類似下列螢幕擷取畫面的訊息：

![沒有問題的範例訊息](changes-to-storekit-images/image17.png "沒有問題的範例訊息")

現在，請完成類似的程式，但按一下 [**散發 ...** ] 將會實際上傳內容。

[![散發應用程式](changes-to-storekit-images/image18.png "散發應用程式")](changes-to-storekit-images/image18.png#lightbox)

選取第一個選項來上傳內容：

![上傳內容](changes-to-storekit-images/image19.png "上傳內容")

重新登入：

[![登入](changes-to-storekit-images/image15.png)](changes-to-storekit-images/image15.png#lightbox)

選擇正確的應用程式和應用程式內購買記錄，以將內容上傳至：

[![選擇應用程式和應用程式內購買記錄](changes-to-storekit-images/image20.png)](changes-to-storekit-images/image20.png#lightbox)

請稍候，正在上傳您的檔案：

[![內容上傳對話方塊](changes-to-storekit-images/image21.png)](changes-to-storekit-images/image21.png#lightbox)

上傳完成時，會出現一則訊息，告知您內容已提交至 App Store。

[![成功上傳訊息的範例](changes-to-storekit-images/image22.png)](changes-to-storekit-images/image22.png#lightbox)

完成之後，當您回到 iTunes Connect 上的產品頁面時，將會顯示套件詳細資料，並已 **準備好提交** 狀態。 當產品處於這種狀態時，您就可以開始在沙箱環境中測試。 您不需要在沙箱中「提交」產品進行測試。

[![iTunes Connect 會顯示套件詳細資料，並已準備好提交狀態](changes-to-storekit-images/image23.png)](changes-to-storekit-images/image23.png#lightbox)

這可能需要一些時間 (例如。 上傳封存和更新的 iTunes Connect 狀態之間有幾分鐘的) 。 您可以另外提交產品以供審查，或是與應用程式二進位檔一起提交。 只有 Apple 正式核准之後，才會在生產環境 App Store 中提供內容，以便在您的應用程式中購買。

### <a name="pkg-file-format"></a>PKG 檔案格式

使用 Xcode 和 Archive 工具來建立和上傳裝載的內容套件，表示您永遠不會看到套件本身的內容。 針對範例應用程式所建立之封裝中的檔案和目錄看起來像下面的螢幕擷取畫面，其根目錄中的 **plist** 檔案和 **內容** 子目錄中的產品檔案：

[![根目錄中的 plist 檔案和內容子目錄中的產品檔案](changes-to-storekit-images/image24.png)](changes-to-storekit-images/image24.png#lightbox)

請注意，封裝的目錄結構 (特別是子目錄) 中檔案的位置， `Contents` 因為您需要瞭解這項資訊，才能從裝置上的封裝解壓縮檔案。

### <a name="updating-package-content"></a>更新套件內容

核准內容之後，更新內容的程式：

- 編輯 Xcode 中的應用程式內購買內容專案。
- 增加版本號碼。
- 再次上傳至 iTunes Connect。 後續的購買者會自動取得最新版本，但已經有舊版本的使用者將不會收到任何通知。
- 您的應用程式會負責通知使用者，並鼓勵他們取得較新版本的內容。 應用程式也必須使用儲存套件的還原功能，建立下載新版本的函式。
- 若要判斷是否存在較新的版本，您可以在應用程式中建立功能，以提取 SKProducts (例如 用來取出產品價格的相同程式) 並比較 ContentVersion 屬性。

## <a name="purchasing-overview"></a>購買總覽

閱讀本節之前，請先參閱現有的 [應用程式內購買檔](~/ios/platform/in-app-purchasing/index.md)。

此圖說明當您購買具有託管內容的產品並下載時所發生的事件順序：

[![購買並下載具有託管內容的產品時所發生的事件順序](changes-to-storekit-images/image25.png)](changes-to-storekit-images/image25.png#lightbox)

1. 您可以在 iTunes Connect 中建立新產品，並啟用託管內容。 實際的內容會在 (Xcode 中個別建立，就像將檔案拖曳到資料夾) ，然後封存並上傳至 iTunes 一樣 (不需要撰寫任何程式碼) 。 然後會提交每項產品以供核准，之後便可供購買。 在範例程式碼中，這些產品識別碼是硬式編碼，但如果您將可用的產品清單儲存在遠端伺服器上，則使用 Apple 裝載內容會更有彈性，以便在您將新的產品和內容提交至 iTunes Connect 時進行更新。
1. 當使用者購買產品時，會將交易放在付款佇列中進行處理。
1. Store 套件會將購買要求轉寄給 iTunes 伺服器以進行處理。
1. 在 iTunes 伺服器上完成交易 (例如 客戶會向) 收費，並將回條傳回給應用程式，其中包含附加的產品資訊，包括是否可供 (下載，以及檔案大小和其他中繼資料) 。
1. 您的程式碼應該檢查產品是否可供下載，如果需要，也會將內容下載要求放在付款佇列中。 Store 套件會將此要求傳送給 iTunes 伺服器。
1. 伺服器會將內容檔案傳回給儲存套件，此套件會提供回呼，以將下載進度和剩餘的估計值傳回給您的程式碼。
1. 完成之後，您會收到通知，並在快取資料夾中傳遞檔案位置。
1. 您的程式碼應該複製檔案並加以驗證，並儲存您需要記住產品是否已購買的任何狀態。 請利用這個機會，在新的檔案 (提示中正確地設定備份旗標：如果它們來自伺服器，而且不是由使用者進行編輯，您可能會略過備份它們，因為使用者一律可以在未來的) 中從 Apple 的伺服器取得這些旗標。
1. 呼叫 FinishTransaction。 此步驟很重要，因為它會從付款佇列中移除交易。 在您將內容複寫到快取目錄之後，請務必不要呼叫 FinishTransaction。 呼叫 FinishTransaction 之後，可能會快速清除快取的檔案。

## <a name="implementing-hosted-content-purchase"></a>執行託管的內容購買

下列資訊應該與完整的 [應用程式內購買檔](~/ios/platform/in-app-purchasing/index.md)一起閱讀。 本檔中的資訊著重于裝載的內容與先前的執行之間的差異。

### <a name="classes"></a>類別

下列類別已新增或改變，以支援 iOS 6 中的託管內容：

- **SKDownload** –代表下載進行中的新類別。 API 允許一個以上的每個產品，但一開始只會執行一個。
- **SKProduct** –新增的屬性： `Downloadable` 、 `ContentVersion` 、 `ContentLengths` 陣列。
- **SKPaymentTransaction** –新增的屬性： `Downloads` ，  `SKDownload` 如果此產品有可供下載的託管內容，則包含物件的集合。
- **SKPaymentQueue** –新增的方法： `StartDownloads` 。 使用物件呼叫這個方法 `SKDownload` ，以提取其託管內容。 下載可能會在背景中發生。
- **SKPaymentTransactionObserver** – New 方法： `UpdateDownloads` 。 Store 套件會以目前下載作業的相關進度資訊來呼叫這個方法。

新類別的詳細資料 `SKDownload` ：

- **進度** –介於0-1 之間的值，您可以用來向使用者顯示百分比-complete 指標。 請勿使用進度 = = 1 來偵測下載是否完成，請檢查狀態 = = 已完成。
- **TimeRemaining** –剩餘的下載時間預估（以秒為單位）。 -1 表示仍在計算預估。
- **狀態** -作用中、等待中、已完成、失敗、已暫停、已取消。
- **>contenturl** –內容放在磁片上的目錄中的檔案位置  `Cache` 。 只有在下載完成後才會填入。
- **錯誤** –如果狀態為 [失敗]，請檢查此屬性。

範例程式碼中的類別之間的互動如下圖所示 (裝載的內容購買特定的程式碼顯示在綠色) 中：

[![此圖顯示以綠色顯示的託管內容購買](changes-to-storekit-images/image26.png)](changes-to-storekit-images/image26.png#lightbox)

本節的其餘部分會顯示使用這些類別的範例程式碼：

### <a name="custompaymentobserver-skpaymenttransactionobserver"></a>CustomPaymentObserver (SKPaymentTransactionObserver) 

變更現有的覆 `UpdatedTransactions` 寫以檢查可下載的內容，並 `StartDownloads` 在必要時呼叫：

```csharp
public override void UpdatedTransactions (SKPaymentQueue queue, SKPaymentTransaction[] transactions)
{
    foreach (SKPaymentTransaction transaction in transactions) {
        switch (transaction.TransactionState) {
        case SKPaymentTransactionState.Purchased:
            // UPDATED FOR iOS 6
            if (transaction.Downloads != null && transaction.Downloads.Length > 0) {
                // Purchase complete, and it has downloads... so download them!
                SKPaymentQueue.DefaultQueue.StartDownloads (transaction.Downloads);
                // CompleteTransaction() call has moved after downloads complete
            } else {
                // complete the transaction now
                theManager.CompleteTransaction(transaction);
            }
            break;
        case SKPaymentTransactionState.Failed:
            theManager.FailedTransaction(transaction);
            break;
        case SKPaymentTransactionState.Restored:
            // TODO: you must decide how to handle restored transactions.
            // Triggering all the downloads at once is not advisable.
            theManager.RestoreTransaction(transaction);
            break;
        default:
            break;
        }
    }
}
```

新的覆寫方法如下 `UpdatedDownloads` 所示。 Store 套件 `StartDownloads` 會在觸發之後，呼叫這個方法 `UpdatedTransactions` 。 這個方法會以不定的間隔 *多次呼叫，* 以提供您下載進度，然後在下載完成時再次呼叫。 請注意 `SKDownload` ，方法會接受物件陣列，因此每個方法呼叫都能提供您佇列中多個下載的狀態。 如下所示，每次都會檢查下載狀態，並採取適當的動作。

```csharp
// ENTIRELY NEW METHOD IN iOS6
public override void PaymentQueueUpdatedDownloads (SKPaymentQueue queue, SKDownload[] downloads)
{
    Console.WriteLine (" -- PaymentQueueUpdatedDownloads");
    foreach (SKDownload download in downloads) {
        switch (download.DownloadState) {
        case SKDownloadState.Active:
            // TODO: implement a notification to the UI (progress bar or something?)
            Console.WriteLine ("Download progress:" + download.Progress);
            Console.WriteLine ("Time remaining:   " + download.TimeRemaining); // -1 means 'still calculating'
            break;
        case SKDownloadState.Finished:
            Console.WriteLine ("Finished!!!!");
            Console.WriteLine ("Content URL:" + download.ContentUrl);

            // UNPACK HERE! Calls FinishTransaction when it's done
            theManager.SaveDownload (download);

            break;
        case SKDownloadState.Failed:
            Console.WriteLine ("Failed"); // TODO: UI?
            break;
        case SKDownloadState.Cancelled:
            Console.WriteLine ("Canceled"); // TODO: UI?
            break;
        case SKDownloadState.Paused:
        case SKDownloadState.Waiting:
            break;
        default:
            break;
        }
    }
}
```

### <a name="inapppurchasemanager-skproductsrequestdelegate"></a>InAppPurchaseManager (SKProductsRequestDelegate) 

這個類別包含新的方法 `SaveDownload` ，此方法會在每次下載順利完成之後呼叫。

裝載的內容已成功下載並解壓縮到目錄中 `Cache` 。 的結構。PKG 檔案要求所有檔案都必須儲存在 `Contents` 子目錄中，因此下列程式碼會從子目錄中解壓縮檔案 `Contents` 。

程式碼會逐一查看內容套件中的所有檔案，並將其複製到 `Documents` 名為之子資料夾中的目錄 `ProductIdentifier` 。 最後，它會呼叫 `CompleteTransaction` ， `FinishTransaction` 以從付款佇列中移除交易。

```csharp
// ENTIRELY NEW METHOD IN iOS 6
public void SaveDownload (SKDownload download)
{
    var documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal); // Documents folder
    var targetfolder = System.IO.Path.Combine (documentsPath, download.Transaction.Payment.ProductIdentifier);
    // targetfolder will be "/Documents/com.xamarin.storekitdoc.montouchimages/" or something like that
    if (!System.IO.Directory.Exists (targetfolder))
        System.IO.Directory.CreateDirectory (targetfolder);
    foreach (var file in System.IO.Directory.EnumerateFiles
             (System.IO.Path.Combine(download.ContentUrl.Path, "Contents"))) { // Contents directory is the default in .PKG files
        var fileName = file.Substring (file.LastIndexOf ("/") + 1);
        var newFilePath = System.IO.Path.Combine(targetfolder, fileName);
        if (!System.IO.File.Exists(newFilePath)) // HACK: this won't support new versions...
            System.IO.File.Copy (file, newFilePath);
        else
            Console.WriteLine ("already exists " + newFilePath);
    }
    CompleteTransaction (download.Transaction); // so it gets 'finished'
}
```

當 `FinishTransaction` 呼叫時，下載的檔案將不再保證存在於 `Cache` 目錄中。 在呼叫之前，應該先複製所有檔案 `FinishTransaction` 。

## <a name="other-considerations"></a>其他考量

上述範例程式碼示範了相當簡單的託管內容購買執行。 另外還有一些您必須考慮的重點：

### <a name="detecting-updated-content"></a>偵測更新的內容

雖然您可以更新裝載的內容套件，但是 Store 套件並不會提供任何機制，將這些更新推送至已下載並購買產品的使用者。 若要執行這項功能，您的程式碼可能會檢查新的 `SKProduct.ContentVersion` 屬性 (如果 `SKProduct` `Downloadable` 定期) ，並偵測該值是否為遞增。 或者，您也可以建立推播通知系統。

### <a name="installing-updated-content-versions"></a>安裝更新的內容版本

上述範例程式碼會在檔案已存在時，略過檔案複製。 如果您想要支援較新版本的要下載的內容，這並不是個好主意。

另一個替代方案可能是將內容複寫到名為的資料夾，並追蹤哪個是目前版本 (例如。 在 `NSUserDefaults` 或您儲存已完成之購買記錄的任何位置) 。

### <a name="restoring-transactions"></a>還原交易

當 `SKPaymentQueue.DefaultQueue.RestoreCompletedTransactions` 呼叫時，Store 套件會傳回該使用者所有先前的交易。 如果他們已購買大量的專案，或每個購買都有大型的內容套件，則還原可能會導致大量的網路流量，因為所有專案都已排入佇列以供下載。

請考慮追蹤產品是否與實際的相關內容套件下載分開購買。

### <a name="pausing-restarting-and-canceling-downloads"></a>暫停、重新開機和取消下載

雖然範例程式碼不會示範這項功能，但您可以暫停和重新開機裝載的內容下載。 `SKPaymentQueue.DefaultQueue`具有 `PauseDownloads` 、和的方法 `ResumeDownloads` `CancelDownloads` 。

如果程式碼在 `FinishTransaction` 下載前于付款佇列上呼叫 `Finished` ，則會自動取消下載。

### <a name="setting-the-skip-backup-flag-on-the-downloaded-content"></a>在下載的內容上設定 SKIP 備份旗標

Apple 的 iCloud 備份指導方針，建議您 *不要* 備份從伺服器輕鬆還原的非使用者內容 (因為這會不必要地使用 iCloud 儲存體) 。 如需有關設定備份屬性的詳細資訊，請參閱 [檔案系統檔](~/ios/app-fundamentals/file-system.md) 集。

## <a name="summary"></a>摘要

本文引進了 iOS6 中的商店套件的兩項新功能：從您的應用程式中購買 iTunes 和其他內容，以及利用 Apple 的伺服器來裝載您自己的應用程式內購買。 本簡介應與現有的 [應用程式內購買檔](~/ios/platform/in-app-purchasing/index.md) 一起閱讀，以取得完整涵蓋的商店套件功能。

## <a name="related-links"></a>相關連結

- [StoreKit (範例) ](/samples/xamarin/ios-samples/storekit)
- [App 內購買](~/ios/platform/in-app-purchasing/index.md)
- [StoreKit 架構參考](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/StoreKit_Collection/_index.html)
- [SKStoreProductViewController 類別參考](https://developer.apple.com/library/ios/documentation/StoreKit/Reference/SKITunesProductViewController_Ref/SKStoreProductViewController.html)
- [SKDownload](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/SKDownload_Ref/Introduction/Introduction.html)
- [SKPaymentQueue](https://developer.apple.com/library/prerelease/ios/documentation/StoreKit/Reference/SKPaymentQueue_Class/Reference/Reference.html#/apple_ref/occ/instm/SKPaymentQueue/cancelDownloads:)
- [SKProduct](https://developer.apple.com/library/prerelease/ios/documentation/StoreKit/Reference/SKProduct_Reference/Reference/Reference.html#/apple_ref/occ/instp/SKProduct/downloadable)
- [WWDC 影片：使用商店套件銷售產品](https://developer.apple.com/videos/wwdc/2012/?include=302#302)