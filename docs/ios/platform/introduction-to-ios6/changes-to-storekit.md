---
title: 在 iOS 6 StoreKit 的變更
description: iOS 6 會介紹市集套件 API 中的兩項變更： 能夠顯示 iTunes （和 App Store/iBookstore） 產品，從在您的應用程式和新應用程式內購買，Apple 將裝載您可下載檔案的選項。 本文件說明如何實作這些功能，搭配 Xamarin.iOS。
ms.prod: xamarin
ms.assetid: 253D37D7-44C7-D012-3641-E15DC41C2699
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 5d1bb5ab636cd7527a560332a9890e9907fac454
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118314"
---
# <a name="changes-to-storekit-in-ios-6"></a>在 iOS 6 StoreKit 的變更

_iOS 6 簡介市集套件 API 中的兩項變更： 能夠顯示 iTunes （和 App Store/iBookstore） 產品，從在您的應用程式和新應用程式內購買，Apple 將裝載您可下載檔案的選項。本文件說明如何實作這些功能，搭配 Xamarin.iOS。_

在 iOS6 市集套件的主要變更是這兩項新功能：

- **在應用程式內容顯示 & Purchasing** – 使用者可以購買並下載應用程式、 音樂、 書籍和其他 iTunes 內容而不需要離開您的應用程式。 您也可以連結至您自己的應用程式，以提升購買，或只是建議檢閱和評等。
- **應用程式內購買的裝載內容**– Apple 將會儲存並傳遞它不需要個別的伺服器來裝載您的檔案，會自動支援背景下載，並可讓您的內容與您在應用程式內購買的產品，相關聯撰寫較少的程式碼。

請參閱[應用程式內購買](~/ios/platform/in-app-purchasing/index.md)StoreKit Api 的詳細說明的指南。

## <a name="requirements"></a>需求

這份文件所述的套件存放區功能需要 iOS 6 和 Xcode 4.5 中，以及 Xamarin.iOS 6.0。

## <a name="in-app-content-display--purchasing"></a>應用程式內顯示的內容與購買

在 iOS 中新的應用程式內購買功能可讓使用者檢視產品資訊並購買或下載應用程式內產品。
先前的應用程式必須觸發 iTunes、 App Store 或 iBookstore，可能會導致保留原始的應用程式的使用者。 在完成時，這項新功能會自動將使用者傳回至應用程式。

[![](changes-to-storekit-images/image1.png "自動會回到購買後的應用程式")](changes-to-storekit-images/image1.png#lightbox)

如何能使用此功能的範例包括：

- **評估您的應用程式的使用者在鼓勵什麼**– 您可以開啟應用程式存放區 頁面，以便使用者可以進行評分或不用離開即可檢閱您的應用程式。
- **跨升級應用程式**– 可讓使用者看到您發行時，能夠立即購買/下載其他應用程式。
- **協助使用者尋找並下載內容**– 幫助使用者購買您的應用程式尋找、 管理，或彙總 （例如內容。 音樂相關的應用程式，可以提供歌曲的播放清單，允許從應用程式內購買的每一首歌）。

一次`SKStoreProductViewController`尚未顯示好像它們是在 iTunes、 App Store 或 iBookstore，使用者可以互動的產品資訊。 使用者可以：

- （適用於應用程式），檢視螢幕擷取畫面
- 範例歌曲或視訊 （適用於音樂、 電視節目與電影），
- 讀取 （和寫入） 檢閱
- 購買並下載，完全在檢視控制器，以及存放區套件內發生這種情況。

內的某些選項`SKStoreProductViewController`仍會強制使用者離開您的應用程式，並開啟相關的市集應用程式，例如按一下**相關產品**或 應用程式的**支援**連結。

### <a name="skstoreproductviewcontroller"></a>SKStoreProductViewController

要顯示在任何應用程式內產品的 API 很簡單： 只需要建立，並顯示`SKStoreProductViewController`。 請遵循下列步驟來建立並顯示產品：

1. 建立`StoreProductParameters`物件，以將參數傳遞至檢視控制器，包括`productId`建構函式。
1. 具現化`SKProductViewController`。 請將它指派給類別層級的欄位。
1. 將處理常式指派給檢視控制器的`Finished`事件，應關閉檢視控制器。 當使用者按下取消; 時，會呼叫此事件否則完成檢視控制器內的交易或。
1. 呼叫`LoadProduct`方法並傳入`StoreProductParameters`和完成處理常式。 完成處理常式必須檢查產品要求已成功，而且如果是這樣，表示`SKProductViewController`以強制回應方式。 如果無法擷取產品就應該將適當的錯誤處理。

### <a name="example"></a>範例

*ProductView*專案中*StoreKit*本文的範例程式碼會實作`Buy`方法可接受任何產品的 Apple ID，並顯示`SKStoreProductViewController`。 下列程式碼會顯示任何給定的 Apple ID 的產品資訊：

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

應用程式時執行 – 下載或購買，就會發生完全內看起來類似以下螢幕擷取畫面`SKStoreProductViewController`:

[![](changes-to-storekit-images/image2.png "應用程式執行時看起來像這樣")](changes-to-storekit-images/image2.png#lightbox)

### <a name="supporting-older-operating-systems"></a>支援較舊的作業系統

範例應用程式包含示範如何在舊版 iOS 的開啟 App Store、 iTunes 或 iBookstore 的程式碼。 使用`OpenUrl`方法來開啟正確製作**itunes.com** URL。

您可以實作版本檢查，以判斷哪一個執行程式碼，如下所示：

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

### <a name="errors"></a>錯誤

如果您使用的 Apple ID 不是有效的這可能會造成混淆因為它表示某種類型的網路或驗證問題，會發生下列錯誤。

 `Error Domain=SKErrorDomain Code=5 "Cannot connect to iTunes Store"`

### <a name="reading-objective-c-documentation"></a>閱讀 OBJECTIVE-C 文件

閱讀有關 Apple 開發人員入口網站上的存放區套件的開發人員將會看到一種通訊協定 – [SKStoreProductViewControllerDelegate](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/SKITunesProductViewControllerDelegate_ProtocolRef/Reference/Reference.html) – 相對於這項新功能的討論。 委派通訊協定已被公開為只有一個方法 – productViewControllerDidFinish –`Finished`上的事件`SKStoreProductViewController`在 Xamarin.iOS 中。

## <a name="determining-apple-ids"></a>決定的 Apple Id

所需的 Apple ID`SKStoreProductViewController`已*數字*（不到與混淆套件組合識別碼 」 com.xamarin.mwc2012 」 等）。 有幾個不同的方式，您可以找出您想要顯示下, 面所列的產品的 Apple ID:

### <a name="itunesconnect"></a>iTunesConnect

對於您發行的應用程式，就可以輕鬆地尋找**Apple ID**在 iTunes Connect 中：

[![](changes-to-storekit-images/image3.png "尋找在 iTunes Connect 中的 Apple ID")](changes-to-storekit-images/image3.png#lightbox)

 <a name="Search_API" />

### <a name="search-api"></a>搜尋 API

Apple 提供動態的搜尋服務 API 來查詢 App Store、 iTunes 以及 iBookstore 中的所有產品。 如何存取搜尋 API 的資訊可在[Apple 的分支機構資源](http://www.apple.com/itunes/affiliates/resources/documentation/itunes-store-web-service-search-api.html)，不過 API 公開給任何人 （不只是已註冊的關係企業）。 若要探索，就可以剖析產生的 JSON`trackId`也就是要搭配使用的 Apple 識別碼`SKStoreProductViewController`。

結果也會包含其他中繼資料，包括顯示資訊和可用來呈現您的應用程式中的產品的圖檔 Url。

以下是一些範例：

- **iBooks 應用程式**– [ http://itunes.apple.com/search?term=ibooks&amp; 實體 = software&amp;國家/地區 = us](http://itunes.apple.com/search?term=ibooks&amp;entity=software&amp;country=us)
- **點和袋鼠 iBook** – [ http://itunes.apple.com/search?term=dot+and+the+kangaroo&amp; 實體 = 電子書&amp;國家/地區 = us](http://itunes.apple.com/search?term=dot+and+the+kangaroo&amp;entity=ebook&amp;country=us)

### <a name="enterprise-partner-feed"></a>企業夥伴摘要

Apple 提供其產品的完整資料傾印使用可下載的資料庫可供一般檔案的形式的已核准的合作夥伴。 如果您符合資格的存取權[企業夥伴摘要](http://www.apple.com/itunes/affiliates/resources/documentation/itunes-enterprise-partner-feed.html)，然後在該資料集，可以找到任何產品的 Apple ID。

許多使用者的企業夥伴摘要是成員[加盟計劃](http://www.apple.com/itunes/affiliates)，可讓產品銷售要贏得獎金。 `SKStoreProductViewController` 不支援分支機構的識別碼 （在撰寫本文時）。

### <a name="direct-product-links"></a>直接的產品連結

產品的 Apple ID 來推斷其 itunes 預覽 URL 連結。
在任何 iTunes 中 （適用於應用程式、 音樂或書籍） 的產品連結尋找起始 URL 的一部分`id`，並使用後面的數字。

比方說，是 iBooks 的直接連結

```csharp
http://itunes.apple.com/us/app/ibooks/id364709193?mt=8
```

Apple ID，且**364709193**。 同樣地 MWC2012 應用程式，直接連結是

```csharp
http://itunes.apple.com/us/app/mwc-2012-unofficial/id496963922?mt=8
```

Apple ID，且**496963922**。

## <a name="in-app-purchase-hosted-content"></a>在應用程式內購買的裝載內容

如果您的應用程式內購買包含可下載的內容 （例如活頁簿或其他媒體、 遊戲關卡設計和組態或其他大型檔案） 來裝載於 web 伺服器，則使用這些檔案和應用程式必須納入安全地下載它們之後的程式碼您可以購買。 從 iOS 6 開始，Apple 將會裝載您的檔案，其在伺服器上，而不需要在不同的伺服器。 此功能只適用於非取用產品 （不取用或訂用帳戶）。 使用 Apple 的裝載服務的優點包括：

- 省下裝載與頻寬成本。
- 可能更具擴充性比您目前使用任何伺服器主機。 
- 減少編寫程式碼，因為您不需要建立任何伺服器端處理。 
- 為您實作背景下載。

注意： 測試裝載在 iOS 模擬器不支援，在應用程式內購買內容，因此您必須使用實際的裝置進行測試。

### <a name="hosted-content-basics"></a>裝載內容的基本概念

IOS 6、 之前沒有提供產品的兩種方式 (更多詳細資料中所述[Xamarin 的應用程式內購買](~/ios/platform/in-app-purchasing/index.md)文件):

- **內建的產品**– 功能，'解除鎖定' 購買，但內建到應用程式 （無論是做為程式碼或內嵌的資源）。 內建的產品範例包括解除鎖定的相片篩選或遊戲中增強物品。
- **伺服器傳遞產品**– 購買後，應用程式必須從您操作的伺服器下載內容。 此內容是在購買期間下載、 儲存在裝置上並轉譯提供產品的一部分。 範例包括書籍、 雜誌的問題或背景圖案和組態檔所組成的遊戲級別。

在 iOS 6 Apple 提供 「 伺服器提供產品的一種變化： 它們會裝載您內容的檔案伺服器上。 這可讓您更容易建置伺服器提供的產品，因為您不需要操作不同的伺服器，而且存放區套件會提供您先前必須撰寫您自己的背景下載功能。 若要充分利用 Apple 的裝載，啟用 適用於新的應用程式內購買產品的內容裝載並修改您的存放區套件程式碼，以利用它。 產品內容檔案然後使用 Xcode 建置並上傳至 Apple 進行審查與發行的伺服器。

[![](changes-to-storekit-images/image4.png "建置和傳遞程序")](changes-to-storekit-images/image4.png#lightbox)

使用 App Store 提供應用程式內購買*裝載內容*需要下列設定，並設定：

- **iTunes Connect** – 您*必須*已經是提供您的銀行和稅務資訊至 Apple，所以它們可以匯款代替您收集的資金。 接著，您可以設定銷售，並設定沙箱使用者帳戶，來測試購買的產品。  _您也必須設定裝載的內容適用於您想要與 Apple 裝載這些非取用產品_。
- **iOS 佈建入口網站**– 建立套件組合識別碼，並啟用您的應用程式的應用程式存放區存取，如同任何應用程式的支援應用程式內購買。
- **儲存套件**– 程式碼加入至您的應用程式，以便顯示產品、 購買產品，以及還原的交易。  _IOS 6 中存放區套件也會管理您的產品內容，在背景，以進行更新的下載。_
- **自訂程式碼**– 若要追蹤客戶的購物記錄，以及提供的產品或他們所購買的服務。 利用新的 iOS 6 市集套件類別，如`SKDownload`擷取 Apple 所裝載之內容。

下列各節說明如何實作裝載的內容，建立和上傳到管理購買的封裝，並下載程序，針對這個發行項使用的範例程式碼。

### <a name="sample-code"></a>程式碼範例

範例專案*HostedNonConsumables* （在 StoreKitiOS6.zip) 會使用裝載的內容。 應用程式提供兩個的 「 書籍章節 「 銷售，其中的內容裝載在 Apple 的伺服器上。 內容所組成的文字檔案和影像，雖然更複雜的內容可在實際的應用程式。

之前、 期間和之後購買，應用程式看起來像這樣：

 [![](changes-to-storekit-images/image5.png "之前、 期間和之後購買應用程式看起來像這樣")](changes-to-storekit-images/image5.png#lightbox)

文字檔案和映像下載並複製到應用程式的文件 目錄。 如需有關適用於應用程式儲存體的不同目錄的詳細資訊，請參閱[檔案系統文件](~/ios/app-fundamentals/file-system.md)。

## <a name="itunes-connect"></a>iTunes Connect

當建立會使用 Apple 的新產品的內容裝載，請務必選取**非取用**產品類型。 其他產品類型不支援內容裝載。 此外，您不應該啟用的內容裝載*現有*您銷售; 只開啟內容裝載適用於新產品的產品。

 [![](changes-to-storekit-images/image6.png "選取非取用產品類型")](changes-to-storekit-images/image6.png#lightbox)

請輸入**產品識別碼**。 此識別碼會是需要稍後當您建立這項產品的內容。

 [![](changes-to-storekit-images/image7.png "輸入產品識別碼")](changes-to-storekit-images/image7.png#lightbox)

內容裝載在詳細資料 區段中設定。 之前上線應用程式內購買，取消核取**裝載的內容，與 Apple**核取方塊，如果您想要取消 （即使您已上傳一些測試內容）。 不過內容裝載之後，無法移除應用程式內購買已正式上線。

 [![](changes-to-storekit-images/image8.png "裝載與 Apple 的內容")](changes-to-storekit-images/image8.png#lightbox)

一旦您已開啟裝載內容，將輸入的產品**等候上傳**狀態，並顯示此訊息：

 [![](changes-to-storekit-images/image9.png "產品會等候輸入上傳狀態，並顯示此訊息")](changes-to-storekit-images/image9.png#lightbox)

內容套件應該使用 Xcode 來建立和上傳使用封存工具。 建立內容套件的指示會提供下一節**建立。PKG 檔案**。

## <a name="creating-pkg-files"></a>正在建立。PKG 檔案

您上傳至 Apple 的內容檔案必須符合下列限制：

- 大小不得超過 2 GB。
- 不能包含可執行程式碼 （或點的內容之外的符號連結）。
- 必須正確地格式化 (包括 **.plist**檔案)，而且有 **.pkg**副檔名。 這會自動完成如果您遵循這些指示使用 Xcode。

您可以新增許多不同的檔案和類型的檔案，只要它們符合這些限制。 內容壓縮才能遞送到您的應用程式，且您的程式碼存取它之前，由市集套件解壓縮。

上傳內容套件之後, 就可以使用較新的內容將其取代。 新的內容必須上傳並提交檢閱/核准透過正常程序。 遞增`ContentVersion`欄位中更新的內容套件，以指出它是較新。

### <a name="xcode-in-app-purchase-content-projects"></a>Xcode 應用程式內購買內容專案

目前建立的應用程式內購買產品的內容套件需要 Xcode。 沒有否 OBJECTIVE C 需要撰寫程式碼;Xcode 會有新的專案類型的這些套件只包含您的檔案和 plist。

每個章節的內容套件會包含-我們的範例應用程式有銷售的書籍章節：

-  文字檔案，並
-  影像來代表一章。


選取開始**檔案 > 新增專案**從功能表中，然後選擇**應用程式內購買內容**:

 [![](changes-to-storekit-images/image10.png "選擇應用程式內購買的內容")](changes-to-storekit-images/image10.png#lightbox)

輸入**Product Name**並**公司識別碼**使得**套件組合識別碼**符合**產品識別碼**您輸入在 iTunes 中連接此產品。

[![](changes-to-storekit-images/image11.png "輸入的名稱和識別碼")](changes-to-storekit-images/image11.png#lightbox)

現在您必須為空白**應用程式內購買內容**專案。 您可以滑鼠右鍵按一下並**新增檔案...** 或將欄位拖曳到**專案導覽器**。 請確認**ContentVersion**正確 （應該開始 1.0，但如果您稍後選擇更新您的內容，請記得它增量）。

此螢幕擷取畫面顯示 Xcode 專案和 plist 項目顯示在主視窗中包含的內容檔案：

[![](changes-to-storekit-images/image12.png "此螢幕擷取畫面顯示所含的內容檔案中的專案和 plist 項目顯示在主視窗的 Xcode")](changes-to-storekit-images/image12.png#lightbox)

新增所有內容的檔案之後，可以儲存此專案並稍後再進行編輯或開始上傳程序。

## <a name="uploading-pkg-files"></a>正在上傳。PKG 檔案

若要上傳內容套件的最簡單方式是使用**Xcode 封存工具**。 選擇**產品 > 封存**從 [開始] 功能表：

![](changes-to-storekit-images/image13.png "選擇 Archiven")

內容套件即會出現在封存中，如下所示。 封存類型和圖示會顯示這一行**封存內容的應用程式內購買**。 按一下 **驗證...** 若要檢查我們的內容套件的錯誤，而不實際執行上傳。

[![](changes-to-storekit-images/image14.png "驗證封裝")](changes-to-storekit-images/image14.png#lightbox)

使用 iTunes Connect 認證的登入：

[![](changes-to-storekit-images/image15.png "使用 iTunes Connect 認證登入")](changes-to-storekit-images/image15.png#lightbox)

選擇正確的應用程式和應用程式內購買，將與此內容：

[![](changes-to-storekit-images/image16.png "選擇正確的應用程式和應用程式內購買，將與此內容")](changes-to-storekit-images/image16.png#lightbox)

您應該會看到類似這個螢幕擷取畫面的訊息：

![範例沒有問題訊息](changes-to-storekit-images/image17.png "範例沒有問題 」 訊息")

現在進行類似的程序，但是按一下**Distribute...** 將實際上傳內容。

[![應用程式散發](changes-to-storekit-images/image18.png "散發應用程式")](changes-to-storekit-images/image18.png#lightbox)

選取要上傳內容的第一個選項：

![上傳內容](changes-to-storekit-images/image19.png "上傳內容")

再次登入：

[![](changes-to-storekit-images/image15.png "登入")](changes-to-storekit-images/image15.png#lightbox)

選擇正確的應用程式和應用程式內購買記錄上傳的內容：

[![](changes-to-storekit-images/image20.png "選擇 應用程式和應用程式內購買記錄")](changes-to-storekit-images/image20.png#lightbox)

稍候，正在上傳您的檔案：

[![](changes-to-storekit-images/image21.png "內容上傳對話方塊")](changes-to-storekit-images/image21.png#lightbox)

上傳完成時，會出現訊息，通知您，內容已提交至 App Store。

[![](changes-to-storekit-images/image22.png "成功上傳訊息的範例")](changes-to-storekit-images/image22.png#lightbox)

之後，是否已完成，當您返回 [產品] 頁面上 iTunes Connect，它會顯示套件詳細資料，並處於**準備好提交**狀態。 當產品處於此狀態時，您可以開始在沙箱環境中測試。 您不需要 [提交] 在沙箱中測試產品。

[![](changes-to-storekit-images/image23.png "iTunes Connect，它會顯示套件詳細資料，並在 準備要送出狀態")](changes-to-storekit-images/image23.png#lightbox)

它也可能需要一些時間 （例如。 幾分鐘的時間） 之間上傳封存和 iTunes Connect 狀態更新。 您可以分別提交檢閱產品，或提交應用程式二進位檔與搭配使用。 只有 Apple 已正式核准內容之後會是可用於生產環境應用程式市集，您的應用程式內購買。

### <a name="pkg-file-format"></a>PKG 檔案格式

使用 Xcode 和封存工具來建立及上傳託管的內容套件時，表示您永遠不會看到封裝本身的內容。 檔案和目錄中建立的範例應用程式外觀類似下面的螢幕擷取畫面的封裝與**plist**根頁面和中的產品檔案的檔案**內容**子目錄：

[![](changes-to-storekit-images/image24.png "Plist 檔案根頁面和內容子目錄中的產品檔案")](changes-to-storekit-images/image24.png#lightbox)

請注意封裝的目錄結構 (特別是在檔案的位置`Contents`子目錄) 因為您必須了解這項資訊來從裝置上的封裝解壓縮檔案。

### <a name="updating-package-content"></a>更新封裝內容

已核准後更新內容的程序：

- 編輯在 Xcode 中的應用程式內購買內容專案。
- 兩點版本號碼。
- 再次上傳至 iTunes Connect。 後續項採購會自動取得最新版本，但已經有舊版本的使用者將不會收到任何通知。
- 您的應用程式負責通知使用者，以及鼓勵他們擷取較新版本的內容。 應用程式也必須建置下載新的版本中，使用 「 還原 」 功能的存放區套件的函式。
- 若要判斷有較新版本，您可以在您的應用程式擷取 SKProducts （例如建置一項功能。 用來擷取產品價格的相同程序） 和 ContentVersion 屬性做比較。

## <a name="purchasing-overview"></a>購買概觀

閱讀本節之前, 檢閱現有[應用程式內購買文件](~/ios/platform/in-app-purchasing/index.md)。

在購買產品，以使用裝載的內容時所發生的事件順序，並下載此圖說明：

[![](changes-to-storekit-images/image25.png "事件裝載的內容使用產品時所發生的順序購買並下載")](changes-to-storekit-images/image25.png#lightbox)

1. 在 iTunes Connect 使用裝載的內容啟用中，可以建立新的產品。 實際的內容是分別在 Xcode 中建構 （為只需為拖曳到資料夾的檔案），然後進行封存，並上傳至 iTunes （不必編寫程式碼是必要）。 然後提交每項產品進行准核之後, 會變得可供購買。 範例程式碼還會使用這些產品識別碼是硬式編碼，但更有彈性，讓它可以更新新的產品和內容至 iTunes Connect 提交時，在遠端伺服器上儲存的可用產品清單，如果裝載與 Apple 的內容。
1. 當使用者購買產品時，交易將會置於付款佇列進行處理。
1. 市集套件購買將要求轉送到 iTunes 伺服器以進行處理。
1. （例如交易完成 iTunes 伺服器上。 客戶已支付的費用） 和回條傳回應用程式，包括是否可下載附加的產品資訊 (如果是，檔案大小和其他中繼資料)。
1. 您的程式碼應該如果產品已下載，請核取，如果是這樣做也會放置在付款佇列內容的下載要求。 市集套件會將此要求傳送至 iTunes 伺服器。
1. 伺服器傳回的內容檔案存放區套件，提供回呼，以傳回下載進度和剩餘的評估，以您的程式碼的時間。
1. 完成後，您取得通知，並傳入快取資料夾的檔案位置。
1. 您的程式碼應該複製檔案，並確認它們，您必須記住已購買產品的任何狀態儲存。 把握這個機會在新的檔案上正確設定備份的旗標 (提示： 如果它們來自伺服器，而且永遠不會進行編輯的使用者，您應該可能略過這些備份，因為使用者可以永遠擷取它們從 Apple 伺服器在未來)。
1. 呼叫 FinishTransaction。 這個步驟很重要，因為它會從付款佇列中移除交易。 也很重要，您不能呼叫直到 FinishTransaction 之後您複製從快取目錄的內容。 一旦您呼叫 FinishTransaction，快取檔案是可能會快速地清除。

## <a name="implementing-hosted-content-purchase"></a>實作裝載內容的採購單

下列資訊應該讀取完整搭配[應用程式內購買項目文件](~/ios/platform/in-app-purchasing/index.md)。 這份文件中的資訊，著重於裝載的內容與先前的實作之間的差異。

### <a name="classes"></a>類別

已新增或改變以支援裝載內容，在 iOS 6 中的下列類別：

- **SKDownload** – 新的類別，代表進行中的下載。 API 可讓一個以上每一產品，不過一開始只有一個已實作。
- **SKProduct** -加入新屬性： `Downloadable`， `ContentVersion`，`ContentLengths`陣列。
- **SKPaymentTransaction** -加入新屬性： `Downloads`，其中包含的集合`SKDownload`物件如果裝載此產品可供下載的內容。
- **SKPaymentQueue** – 新增的新方法： `StartDownloads`。 呼叫這個方法時`SKDownload`来擷取其裝載的內容物件。 下載，可能會在背景中發生。
- **SKPaymentTransactionObserver** – 新的方法： `UpdateDownloads`。 儲存套件呼叫這個方法目前的下載作業的進度資訊。

新的詳細資料`SKDownload`類別：

- **進度**– 介於 0-1，可供您向使用者顯示的完成百分比 」 指標的值。 請勿使用進度 = = 1，若要偵測是否已完成下載，請核取狀態 = = 已完成。
- **TimeRemaining** – 估計下載剩下的時間，以秒為單位。 -1 表示它仍然計算估計值。
- **狀態**– 作用中、 等候、 完成、 失敗、 暫停、 取消。
- **ContentURL** – 檔案的位置的內容已放置在磁碟，在`Cache`目錄。 下載完成後，才填入。
- **錯誤**– 檢查此屬性，如果狀態為已失敗。

範例程式碼中的類別之間的互動會顯示在此圖中 （裝載內容的購買項目特定的程式碼以綠色顯示）：

[![](changes-to-storekit-images/image26.png "裝載內容的購買項目會顯示在此圖中的綠色")](changes-to-storekit-images/image26.png#lightbox)

這些類別已被使用的範例程式碼是以本節的其餘部分所示：

### <a name="custompaymentobserver-skpaymenttransactionobserver"></a>CustomPaymentObserver (SKPaymentTransactionObserver)

變更現有`UpdatedTransactions`覆寫，以檢查是否有可下載的內容，並呼叫`StartDownloads`視：

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

新的覆寫的方法`UpdatedDownloads`如下所示。 市集套件會呼叫這個方法之後`StartDownloads`就會觸發在`UpdatedTransactions`。 這個方法會呼叫*多次*在提供的下載進度不定時間間隔，然後再次下載已完成。 請注意此方法可接受陣列`SKDownload`物件，讓每個方法呼叫可以提供您在佇列中的多個下載的狀態。 中所示實作下列幾種下載狀態會檢查每個時間並採取適當動作。

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

這個類別包含的新方法`SaveDownload`，在每個下載成功完成之後呼叫。

已成功下載並解壓縮至裝載的內容`Cache`目錄。 結構。套件檔案需要的所有檔案，以儲存在`Contents`子目錄，因此下列程式碼將檔案解壓縮從`Contents`子目錄。

程式碼會逐一查看內容套件中的所有檔案，並複製到`Documents`目錄下，針對名為的子資料夾中`ProductIdentifier`。 它會呼叫最後`CompleteTransaction`，其會呼叫`FinishTransaction`移除付款佇列中的交易。

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

當`FinishTransaction`呼叫時，已下載檔案不再保證位於`Cache`目錄。 應該複製所有檔案，然後再呼叫`FinishTransaction`。


## <a name="other-considerations"></a>其他考量

上面的範例程式碼會示範裝載內容購買相當簡單實作。 另外還有，您必須考慮一些其他重點：

### <a name="detecting-updated-content"></a>偵測更新的內容

雖然您可以更新您裝載的內容套件，套件存放區不提供任何機制來推送這些更新已下載並購買產品的使用者。 若要實作這項功能程式碼可能會檢查新`SKProduct.ContentVersion`屬性 (如果`SKProduct`是`Downloadable`) 定期和偵測到的值會在遞增。 或者，您可以建置的推播通知系統。

### <a name="installing-updated-content-versions"></a>安裝更新的內容版本

上述範例程式碼如果會略過檔案複製檔案已經存在。 如果您想要支援較新版本的下載內容，這不是個好主意。

替代方法可能要將內容複製到名為版本，以及追蹤目前的版本 （例如。 在 `NSUserDefaults`或儲存已完成的購買記錄的地方)。

### <a name="restoring-transactions"></a>還原交易

當`SKPaymentQueue.DefaultQueue.RestoreCompletedTransactions`是市集套件呼叫，傳回使用者的所有先前交易。 購買大量項目，則每次購買具有大型的內容套件，然後還原可能會導致大量網路流量，所有項目加入佇列並下載一次。

請考慮追蹤是否產品已從實際下載相關聯的內容套件分開購買。

### <a name="pausing-restarting-and-canceling-downloads"></a>暫停、 重新啟動和取消下載

雖然範例程式碼不會示範這項功能，就可以暫停及重新啟動裝載內容的下載項目。 `SKPaymentQueue.DefaultQueue`有方法來`PauseDownloads`，`ResumeDownloads`和`CancelDownloads`。

如果程式碼會呼叫`FinishTransaction`付款佇列之前下載正在`Finished`則會自動取消下載。

### <a name="setting-the-skip-backup-flag-on-the-downloaded-content"></a>下載的內容上設定略過備份旗標

Apple 的 iCloud 備份指導方針建議非使用者輕鬆地還原從伺服器的內容應該*不*備份 （因為它會不必要地使用 iCloud 儲存體）。 如需有關如何設定備份的屬性的詳細資訊，請參閱[檔案系統](~/ios/app-fundamentals/file-system.md)文件。

## <a name="summary"></a>總結

這篇文章介紹了兩個新功能的存放區套件 iOS6： 購買 iTunes 與從您的應用程式內的其他內容，並使用 Apple 的部署伺服器來託管您自己的應用程式內購買。 應該與現有一倂閱讀本簡介[應用程式內購買文件](~/ios/platform/in-app-purchasing/index.md)實作存放區套件功能的完整涵蓋範圍。

## <a name="related-links"></a>相關連結

- [StoreKit （範例）](https://developer.xamarin.com/samples/StoreKit/)
- [App 內購買](~/ios/platform/in-app-purchasing/index.md)
- [StoreKit Framework 參考](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/StoreKit_Collection/_index.html)
- [SKStoreProductViewController 類別參考](https://developer.apple.com/library/ios/documentation/StoreKit/Reference/SKITunesProductViewController_Ref/SKStoreProductViewController.html)
- [iTunes 搜尋 API 參考](http://www.apple.com/itunes/affiliates/resources/documentation/itunes-store-web-service-search-api.html)
- [SKDownload](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/SKDownload_Ref/Introduction/Introduction.html)
- [SKPaymentQueue](https://developer.apple.com/library/prerelease/ios/documentation/StoreKit/Reference/SKPaymentQueue_Class/Reference/Reference.html#/apple_ref/occ/instm/SKPaymentQueue/cancelDownloads:)
- [SKProduct](https://developer.apple.com/library/prerelease/ios/documentation/StoreKit/Reference/SKProduct_Reference/Reference/Reference.html#/apple_ref/occ/instp/SKProduct/downloadable)
- [WWDC 影片： 銷售市集套件的產品](https://developer.apple.com/videos/wwdc/2012/?include=302#302)
