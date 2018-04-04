---
title: StoreKit 的變更
description: iOS 6 導入了兩個變更存放區套件應用程式開發介面： 能夠顯示 iTunes （與應用程式市集/iBookstore） 從的產品，在您的應用程式和新應用程式內購買的 Apple 會裝載您可下載檔案的選項。 本文件說明如何實作與 Xamarin.iOS 這些功能。
ms.prod: xamarin
ms.assetid: 253D37D7-44C7-D012-3641-E15DC41C2699
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: 8a7a70c3f84518141cf44d630fb4137051d0c866
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="changes-to-storekit"></a>StoreKit 的變更

_iOS 6 導入了兩個變更存放區套件應用程式開發介面： 能夠顯示 iTunes （與應用程式市集/iBookstore） 從的產品，在您的應用程式和新應用程式內購買的 Apple 會裝載您可下載檔案的選項。本文件說明如何實作與 Xamarin.iOS 這些功能。_

在 iOS6 市集套件的主要變更是這兩個新功能：

-   **在應用程式內容顯示 & Purchasing** – 使用者可以購買並下載應用程式、 音樂、 書籍和其他 iTunes 內容而不需離開您的應用程式。 您也可以連結至您自己的應用程式升級至購買或只鼓勵評論和評等。 
-   **應用程式內購買的裝載內容**– Apple 會儲存和傳遞其不需要個別的伺服器來裝載您的檔案，自動支援背景下載並可讓您的內容與您在應用程式內購買的產品，相關聯撰寫較少的程式碼。 


建議閱讀這份文件時，會搭配現有的 Xamarin.iOS[中應用程式內購買](~/ios/platform/in-app-purchasing/index.md)文件。

## <a name="requirements"></a>需求

本文所討論之市集套件功能需要 iOS 6 和 Xcode 4.5，以及 Xamarin.iOS 6.0。


## <a name="in-app-content-display--purchasing"></a>顯示在應用程式內容 （& s) 購買

在 iOS 中新的應用程式內購買功能可讓使用者檢視產品資訊並購買或下載應用程式內產品。
先前應用程式需要 iTunes、 App Store 或 iBookstore 保留原始的應用程式的使用者可能會導致觸發程序。 當他們完成時，這項新功能會自動將使用者傳回至應用程式。

 [![](changes-to-storekit-images/image1.png "自動購買之後傳回至應用程式")](changes-to-storekit-images/image1.png#lightbox)

有許多案例，這可能很有用，包括 （但不是限於）：

-   **鼓勵使用者對您的應用程式的評分**– 您可以開啟 [應用程式市集] 頁面，讓使用者能夠率，並檢閱您的應用程式，而不離開它。 
-   **跨升級應用程式**– 可讓使用者看到您發行時，立即購買/下載能力的其他應用程式。 
-   **協助使用者尋找並下載內容**– 協助使用者購買您的應用程式尋找、 管理或彙總 （例如內容 音樂相關的應用程式，可以提供歌曲的播放清單，允許從應用程式內購買每首歌曲）。 


一次`SKStoreProductViewController`已經顯示使用者可以如同在 iTunes App Store 的 iBookstore 互動的產品資訊。 包括：

-  （適用於應用程式），檢視螢幕擷取畫面
-  取樣歌曲或視訊 （適用於音樂、 電視節目與電影），
-  讀取 （和寫入） 檢閱、
-  購買和下載，也就是完全內的檢視控制器 」 及 「 儲存套件。 不需要您的應用程式不提供任何額外的程式碼，此工作。 


請注意，有些選項內`SKStoreProductViewController`仍會強制使用者離開您的應用程式，並開啟相關的市集應用程式，例如按一下**相關產品**或應用程式的**支援**連結。

### <a name="skstoreproductviewcontroller"></a>SKStoreProductViewController

若要顯示的產品內的任何應用程式的 API 是非常簡單： 只需要您建立並顯示`SKStoreProductViewController`。 請遵循這些步驟，建立並顯示產品：

1.  建立`StoreProductParameters`物件將參數傳遞給檢視控制器，包括`productId`建構函式中。 
1.  具現化`SKProductViewController`。 請將它指派給類別層級的欄位。 
1.  指定處理常式來檢視控制站的`Finished`事件，應關閉檢視控制器。 當使用者按下取消; 時，會呼叫這個事件或否則終結檢視控制器內的交易。 
1.  呼叫`LoadProduct`方法傳入`StoreProductParameters`和完成處理常式。 完成處理常式必須檢查產品要求已成功，而且如果是這樣，表示`SKProductViewController`為強制回應。 如果無法擷取產品，您應該加入適當的錯誤處理。 

### <a name="example"></a>範例

*ProductView*專案中*StoreKit*這篇文章的範例程式碼會實作`Buy`方法可接受任何產品的 Apple ID，並顯示`SKStoreProductViewController`。 下列程式碼會顯示任何給定的 Apple ID 的產品資訊：

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

應用程式時執行 – 下載或購買，就會發生完全內看起來像這樣`SKStoreProductViewController`:

 [![](changes-to-storekit-images/image2.png "應用程式看起來像這樣，執行時")](changes-to-storekit-images/image2.png#lightbox)

### <a name="supporting-older-operating-systems"></a>支援較舊的作業系統

應用程式範例包含示範如何開啟應用程式市集、 iTunes 或 iBookstore 較舊 iOS 版本中的程式碼。 使用`OpenUrl`方法來開啟正確製作**itunes.com** URL。

您可以執行版本檢查，以判斷哪一個執行程式碼，就像這樣：

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

如果您使用的 Apple 識別碼不是有效的這可能會造成混淆以來表示某種類型的網路或驗證問題，將會發生下列錯誤。

 `Error Domain=SKErrorDomain Code=5 "Cannot connect to iTunes Store"`

### <a name="reading-objective-c-documentation"></a>閱讀 OBJECTIVE-C 文件

開發人員了解 Apple 開發人員入口網站上的存放區套件將會看到一種通訊協定 – [SKStoreProductViewControllerDelegate](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/SKITunesProductViewControllerDelegate_ProtocolRef/Reference/Reference.html) – 討論相對於這項新功能。 委派通訊協定已被公開為只有一個方法 – productViewControllerDidFinish –`Finished`事件`SKStoreProductViewController`Xamarin.iOS 中。


## <a name="determining-apple-ids"></a>判斷 Apple Id

所需的 Apple ID`SKStoreProductViewController`是*數目*（不到與混淆套件組合識別碼 」 com.xamarin.mwc2012"像）。 有幾個不同的方式，您可以找出您想要顯示下, 面所列的產品的 Apple ID:

### <a name="itunesconnect"></a>iTunesConnect

對於您發行的應用程式，所以可以輕鬆地尋找**Apple ID**在 iTunes Connect 中：

 [![](changes-to-storekit-images/image3.png "尋找在 iTunes Connect 中的 Apple ID")](changes-to-storekit-images/image3.png#lightbox)

 <a name="Search_API" />


### <a name="search-api"></a>搜尋 API

Apple 提供動態搜尋 API 來查詢 iTunes App Store iBookstore 中的所有產品。 如何存取 search API 的資訊位於[Apple 的分支機構資源](http://www.apple.com/itunes/affiliates/resources/documentation/itunes-store-web-service-search-api.html)，不過 API 公開給任何人 （不只註冊關係企業）。 若要探索，就可以剖析產生的 JSON`trackId`也就是要搭配使用的 Apple 識別碼`SKStoreProductViewController`。

結果也會包含其他中繼資料，包括顯示資訊和作品 Url 可以用來呈現您的應用程式中的產品。

以下是一些範例：

-   **iBooks app*- [http://itunes.apple.com/search?term=ibooks&amp;entity=software&amp;country=us](http://itunes.apple.com/search?term=ibooks&amp;entity=software&amp;country=us) 
-   **點和袋鼠 iBook*- [http://itunes.apple.com/search?term=dot+and+the+kangaroo&amp;實體 = 電子書&amp;國家/地區 = us](http://itunes.apple.com/search?term=dot+and+the+kangaroo&amp;entity=ebook&amp;country=us) 


### <a name="enterprise-partner-feed"></a>企業夥伴摘要

Apple 提供的可下載版本可供資料庫的一般檔案形式其產品的完整資料傾印的核准的合作。 如果您的存取權限定[企業夥伴摘要](http://www.apple.com/itunes/affiliates/resources/documentation/itunes-enterprise-partner-feed.html)然後任何產品的 Apple ID 位於該資料集。

請注意，許多使用者的 「 企業夥伴摘要的成員[分支機構程式](http://www.apple.com/itunes/affiliates)，可讓贏得產品銷售的佣金。 `SKStoreProductViewController` 不支援分支機構的識別碼 （在撰寫; 這可能會加入 apple 未來）。

### <a name="direct-product-links"></a>直接連結

產品的 Apple ID 來推斷從其 iTunes 預覽 URL 連結。
在任何 iTunes （適用於應用程式、 音樂或書籍） 的產品連結尋找 URL 開頭的一部分`id`並使用數字後面。

比方說，是 iBooks 的直接連結

```csharp
http://itunes.apple.com/us/app/ibooks/id364709193?mt=8
```

Apple ID，而且**364709193**。 同樣地 MWC2012 應用程式中，直接連結是

```csharp
http://itunes.apple.com/us/app/mwc-2012-unofficial/id496963922?mt=8
```

Apple ID，而且**496963922**。

## <a name="in-app-purchase-hosted-content"></a>在應用程式內購買裝載的內容

如果您在應用程式內購買組成可下載的內容 （例如書籍或其他媒體、 遊戲的層級封面和組態或其他大型檔案） 則裝載在您的網頁伺服器上使用這些檔案，且應用程式必須納入以安全地下載後的程式碼購買。 在 iOS 6 Apple 已引進它們將在其中裝載您的檔案，在其伺服器上的選項不需要使用不同的伺服器。 此功能僅適用於非可使用產品 （不取用或訂閱）。 使用 Apple 的裝載服務的優點包括：

-  儲存裝載 & 頻寬成本。
-  可能是更具擴充性比您目前使用任何伺服器主機。 
-  若要撰寫，因為您沒有建立任何伺服器端處理較少程式碼。 
-  對於您實作背景下載。


附註： 測試裝載應用程式內購買內容，在 iOS 模擬器不支援，所以您必須測試與實際裝置。

### <a name="hosted-content-basics"></a>裝載內容的基本概念

IOS 6、 之前沒有提供產品的兩種方式 (更詳細地描述[Xamarin 的應用程式內購買](~/ios/platform/in-app-purchasing/index.md)文件):

-   **內建產品**– 的 '解除鎖定' 購買，但這是內建到應用程式 （可作為程式碼或內嵌的資源） 的功能。 內建的產品範例包括解除鎖定的相片篩選或遊戲中的電源總。 
-   **伺服器傳送產品**– 之後購買應用程式必須從您操作的伺服器下載內容。 此內容是購買期間下載、 儲存在裝置，然後轉譯為提供產品的一部分。 範例包括書籍、 雜誌問題或遊戲背景圖案和組態檔所組成的層級。 


在 iOS 6 Apple 提供的伺服器傳送產品變化： 它們會裝載您在其伺服器上的內容檔案。 這可讓您更容易建置伺服器傳送的產品，因為您不需要操作不同的伺服器，而且可市集套件提供您先前必須自行撰寫背景下載功能。 若要充分利用 Apple 的裝載，讓適用於新的應用程式內購買產品的裝載內容並修改您的市集套件程式碼，以利用.net framework。 然後再使用 Xcode 建置並且上傳至 Apple 的伺服器，供您檢閱和發行產品的內容檔案。

 [![](changes-to-storekit-images/image4.png "建置和傳遞程序")](changes-to-storekit-images/image4.png#lightbox)

使用 App Store 提供應用程式內購買*與裝載內容*需要下列安裝及設定：

-   **iTunes Connect** – 您*必須*讓它們可以 remit 收集代表您的款項，已提供您的銀行與稅務資訊給 Apple。 接著，您可以設定銷售，並設定沙箱使用者帳戶，來測試購買的產品。  *您也必須設定裝載內容**適用於您想要使用 Apple 的主機這些非可使用產品* *。* 
-   **iOS 佈建入口網站**– 建立套件組合識別碼，並啟用您的應用程式的應用程式市集存取任何支援應用程式內購買的應用程式一樣。 
-   **儲存套件**– 顯示產品、 購買的產品和還原交易的應用程式中加入程式碼。  *在 iOS 6 市集套件也會管理您產品的內容，在背景中進行更新的下載。* 
-   **自訂程式碼**– 若要追蹤的客戶購物記錄，以及提供的產品或服務已購買。 利用新的 iOS 6 市集套件類別，例如`SKDownload`擷取 Apple 所裝載的內容。 


下列各節說明如何實作裝載的內容，建立和上傳封裝管理購買並下載程序，針對這個發行項使用的範例程式碼。


### <a name="sample-code"></a>程式碼範例

這個範例專案*HostedNonConsumables* （在 StoreKitiOS6.zip) 示範如何建置的應用程式會使用裝載內容。 應用程式提供了兩個的 「 書籍章節 「 銷售，Apple 的伺服器上裝載的內容。 內容所組成的文字檔案和影像，雖然更複雜的內容無法用在實際的應用程式。

之前、 期間和購買之後，應用程式看起來像這樣：

 [![](changes-to-storekit-images/image5.png "之前、 期間和之後購買應用程式看起來像這樣")](changes-to-storekit-images/image5.png#lightbox)

文字檔案與映像下載並複製到應用程式的文件 目錄。 請參閱[使用檔案系統文件](~/ios/app-fundamentals/file-system.md)如需有關適用於應用程式儲存區不同的目錄。

## <a name="itunes-connect"></a>iTunes Connect

當建立新的產品，將會使用 Apple 的內容裝載務必選取**非取用**產品類型。 其他產品類型不支援裝載的內容。 此外，您不應該啟用內容裝載*現有*銷售，; 只能開啟內容託管新產品的產品。

 [![](changes-to-storekit-images/image6.png "選取的非可使用產品類型")](changes-to-storekit-images/image6.png#lightbox)

輸入**產品識別碼**。 將會有此需要稍後當您建立此產品的內容。

 [![](changes-to-storekit-images/image7.png "輸入產品識別碼")](changes-to-storekit-images/image7.png#lightbox)

內容裝載在詳細資料 區段中設定。 之前在應用程式內購買上線，只是"第內容頁，與 Apple 主機 」 核取方塊。 如果取消選取您想要取消 （即使您已上傳一些測試內容）。 但裝載內容之後，無法移除應用程式內購買已啟動。

 [![](changes-to-storekit-images/image8.png "透過 Apple 的裝載內容")](changes-to-storekit-images/image8.png#lightbox)

一旦您已開啟裝載內容，將會進入產品**等候上傳**狀態和顯示此訊息：

 [![](changes-to-storekit-images/image9.png "會進入等候上傳狀態的產品，並將其顯示這個訊息")](changes-to-storekit-images/image9.png#lightbox)

現在必須使用 Xcode 建立內容，並且使用封存工具上傳。 建立內容套件的指示已列在下一節中**建立。BYOK-KEK-PKG 檔案**。

## <a name="creating-pkg-files"></a>建立。BYOK-KEK-PKG 檔案

您上傳至 Apple 的內容檔案必須符合下列限制：

-  大小不得超過 2 GB。
-  不能包含可執行程式碼 （或點的內容之外的符號連結）。 
-  必須正確地格式化 （包括.plist 檔案） 以及副檔名為.pkg 檔案。 執行這個操作將會自動在遵循這些指示使用 Xcode。 


您可以加入許多不同的檔案和類型的檔案，只要它們符合這些限制。 內容壓縮才能遞送到您的應用程式，且您的程式碼存取它之前，由市集套件解壓縮。

上傳內容套件之後, 可以取代它的新內容。 新的內容必須上傳和提交檢閱/核准透過一般的程序。 您必須遞增`ContentVersion`欄位中更新的內容套件。

### <a name="xcode-in-app-purchase-content-projects"></a>Xcode 應用程式內購買內容的專案

目前建立的應用程式內購買產品的內容套件需要 Xcode。 沒有否 OBJECTIVE C 撰寫程式碼所需。Xcode 有新的專案類型的這些封裝，其中只包含您的檔案和 plist。

我們的範例應用程式有銷售的書籍章節 – 每個章節的內容套件會包含：

-  文字檔案，並
-  代表本章映像。


選取 啟動**檔案 > 新的專案**從功能表中，然後選擇**應用程式內購買內容**:

 [![](changes-to-storekit-images/image10.png "選擇在應用程式內購買的內容")](changes-to-storekit-images/image10.png#lightbox)

輸入**產品名稱**和**公司識別碼**使得**配套識別碼**符合**產品識別碼**您在 iTunes 輸入連接此產品。

 [![](changes-to-storekit-images/image11.png "輸入的名稱和識別碼")](changes-to-storekit-images/image11.png#lightbox)

現在您將有空白**應用程式內購買內容**專案。 您可以滑鼠右鍵按一下和**加入檔案...** 或將欄位拖曳到**專案導覽器**。 請確認**ContentVersion**正確 （它應該開始 1.0，但如果您選擇稍後更新您的內容，請務必記得累加）。

這個螢幕擷取畫面顯示 Xcode 專案和 plist 項目顯示在主視窗中包含的內容檔案：

 [![](changes-to-storekit-images/image12.png "這個螢幕擷取畫面顯示 Xcode，其中包含在專案和 plist 中的項目顯示在主視窗的內容檔案")](changes-to-storekit-images/image12.png#lightbox)

一旦您加入的所有內容檔案可以儲存此專案，稍後再進行編輯或開始上傳程序。

## <a name="uploading-pkg-files"></a>正在上傳。BYOK-KEK-PKG 檔案

上傳內容套件的最簡單方式是使用**Xcode 封存工具**。 選擇**產品 > 封存**從開始功能表：

 ![](changes-to-storekit-images/image13.png "選擇 Archiven")

如下所示，內容套件即會出現在封存中。 請注意下的封存類型和圖示會顯示這是**應用程式內購買內容封存**。 按一下**驗證...** 若要檢查錯誤我們內容封裝而不實際 preforming 上傳。

 [![](changes-to-storekit-images/image14.png "驗證封裝")](changes-to-storekit-images/image14.png#lightbox)

使用您 iTunes Connect 認證登入：

 [![](changes-to-storekit-images/image15.png "登入，而且您 iTunes Connect 認證")](changes-to-storekit-images/image15.png#lightbox)

選擇正確的應用程式和應用程式內購買與此內容相關聯：

 [![](changes-to-storekit-images/image16.png "選擇要與此內容產生關聯的正確的應用程式和應用程式內購買")](changes-to-storekit-images/image16.png#lightbox)

您應該會看到類似這樣的訊息：

 ![](changes-to-storekit-images/image17.png "範例沒有問題的訊息")

現在透過類似的程序，但是按一下**散發...** 將實際上傳內容。

 [![](changes-to-storekit-images/image18.png "發佈應用程式")](changes-to-storekit-images/image18.png#lightbox)

選取要上傳內容的第一個選項：

 ![](changes-to-storekit-images/image19.png "上傳內容")

重新登入：

 [![](changes-to-storekit-images/image15.png "登入")](changes-to-storekit-images/image15.png#lightbox)

選擇要上傳內容至正確的應用程式和應用程式內購買記錄：

 [![](changes-to-storekit-images/image20.png "選擇 應用程式和應用程式內購買記錄")](changes-to-storekit-images/image20.png#lightbox)

上傳您的檔案，請稍候：

 [![](changes-to-storekit-images/image21.png "內容上傳對話方塊")](changes-to-storekit-images/image21.png#lightbox)

上傳完成時，會出現訊息，以通知您的內容已送出的應用程式市集。

 [![](changes-to-storekit-images/image22.png "成功上傳訊息範例")](changes-to-storekit-images/image22.png#lightbox)

在已完成的當您返回 [產品] 頁面在 iTunes Connect 則會顯示封裝的詳細資料，並在**準備要提交**狀態。 當產品處於此狀態時，您可以開始在沙箱環境中測試。 您不需要提交產品在沙箱中進行測試。

 [![](changes-to-storekit-images/image23.png "iTunes Connect 則會顯示封裝的詳細資料，並準備好要送出狀態")](changes-to-storekit-images/image23.png#lightbox)

它也可能需要一些時間 （例如。 幾分鐘的時間） 之間上傳封存和 iTunes 正在更新的連接狀態。 您可以個別提交檢閱產品或提交應用程式二進位檔搭配。 只有 Apple 已正式核准內容後將它無法在應用程式中購買的應用程式市集生產環境中。

### <a name="pkg-file-format"></a>BYOK-KEK-PKG 檔案格式

使用 Xcode 和封存工具來建立及上傳託管的內容套件時，表示永遠不會看到封裝本身的內容。 檔案和目錄建立範例應用程式封裝中的看起來像這樣，與`plist`根和中的產品檔案中的檔案`Contents`子目錄：

 [![](changes-to-storekit-images/image24.png "根和產品中的檔案內容子目錄中 plist 檔案")](changes-to-storekit-images/image24.png#lightbox)

請注意封裝的目錄結構 (特別是在檔案的位置`Contents`子目錄) 因為您需要了解這項資訊從裝置上的套件解壓縮檔案。

### <a name="updating-package-content"></a>更新封裝的內容

已核准後，更新內容的程序：

-  編輯在 Xcode 中內容在應用程式購買的專案。
-  兩點版本號碼。
-  重新上傳至 iTunes Connect。 後續項採購將自動取得最新版本，但是已經有舊版本的使用者將不會收到任何通知。 
-  您的應用程式負責通知使用者，並建議他們擷取較新版的內容。 應用程式也必須建立會下載新版本的函式。 必須使用 「 還原 」 功能的存放區組件。 
-  若要判斷是否有較新版本有您可在您的應用程式擷取 （例如 SKProducts 建置功能 用來擷取產品價格的相同程序） 和 ContentVersion 屬性做比較。 

## <a name="purchasing-overview"></a>購買概觀

在閱讀本節之前, 檢閱現有[中應用程式內購買文件](~/ios/platform/in-app-purchasing/index.md)。

購買的產品與裝載的內容時所發生的事件順序，並下載說明在此圖中：

 [![](changes-to-storekit-images/image25.png "事件裝載的內容與產品時所發生的順序購買並下載")](changes-to-storekit-images/image25.png#lightbox)

1.  在 iTunes Connect 與裝載內容啟用中，可以建立新的產品。 實際的內容是分別在 Xcode 中建構 （為只需為拖曳至資料夾的檔案），然後保存及上傳至 iTunes （任何程式碼是必要）。 然後提交每項產品進行核准之後, 即可用於購買。 範例程式碼還會使用這些產品識別碼是硬式編碼，但裝載透過 Apple 的內容是您可用的產品清單儲存在遠端伺服器，這樣可以更新新產品及內容發佈至 iTunes Connect 送出的更多彈性。 
1.  在使用者購買產品，異動會在處理付款佇列中。 
1.  市集套件購買將要求轉寄到 iTunes 伺服器進行處理。 
1.  交易是 iTunes 在伺服器上完成 （例如。 客戶收取） 和回條會傳回至應用程式，包括是否可下載附加的產品資訊 (如果是，檔案大小和其他中繼資料)。 
1.  您的程式碼應該檢查產品是否 downloadble，以的話也放置付款佇列內容的下載要求。 市集套件會將此要求傳送至 iTunes 伺服器。 
1.  伺服器傳回的內容檔案存放區套件，提供回呼傳回下載進度和剩餘的評估，以您的程式碼的時間。 
1.  完成後，您收到通知，並傳入快取資料夾的檔案位置。 
1.  您的程式碼應該複製檔案，並確認它們，您必須記得已購買產品的任何狀態儲存。 利用這個機會在新的檔案上正確設定備份的旗標 (提示： 如果它們來自伺服器，而且永遠不會進行編輯的使用者，您應該可能略過這些備份，因為使用者可以永遠擷取它們從 Apple 的伺服器在未來)。 
1.  呼叫 FinishTransaction。 這是重要，因為它會從付款佇列中移除交易。 它也很重要您沒有呼叫直到 FinishTransaction 之後您已複製的內容快取目錄。 一旦呼叫 FinishTransaction，快取檔案很可能會快速清除。 


## <a name="implementing-hosted-content-purchase"></a>實作裝載內容的採購單

下列資訊應一併閱讀完整搭配[應用程式內購買文件](~/ios/platform/in-app-purchasing/index.md)。 這份文件中的資訊會著重於裝載的內容與之前的實作之間的差異。


### <a name="classes"></a>類別

已加入或更改 iOS 6 支援裝載內容的下列類別：

-   **SKDownload** – 新的類別，代表進行中的下載。 API 可讓一個以上每一產品，但是一開始只有一個已實作。 
-   **SKProduct** – 加入新屬性： `Downloadable` ， `ContentVersion` ，`ContentLengths`陣列。 
-   **SKPaymentTransaction** – 加入新屬性： `Downloads` ，其中包含的集合`SKDownload`如果此產品已裝載內容可供下載的物件。 
-   **SKPaymentQueue** – 加入新的方法： `StartDownloads` 。 呼叫這個方法時`SKDownload`来擷取其裝載的內容物件。 下載可能會在背景中發生。 
-   **SKPaymentTransactionObserver** – 新的方法： `UpdateDownloads` 。 市集套件會呼叫這個方法與目前的下載作業的進度資訊。 


新的詳細資料`SKDownload`類別：

-   **進度**– 介於 0-1，可讓您向使用者顯示的完成百分比 」 指標的值。 請勿使用進度 = = 1，若要偵測是否已完成下載，請檢查狀態 = = 已完成。 
-   **TimeRemaining** – 估計下載剩下的時間，以秒為單位。 -1 表示它仍會計算估計值。 
-   **狀態**– 作用中、 等待中、 已完成、 失敗、 暫停、 已取消。 
-   **ContentURL** – 檔案的位置的內容已放置在磁碟，在`Cache`目錄。 一旦下載完成，才擴展。 
-   **錯誤**– 請檢查此屬性，如果狀態為已失敗。 


（裝載內容的購買的特定程式碼顯示為綠色） 這張圖表顯示的範例程式碼中的類別之間的互動：

 [![](changes-to-storekit-images/image26.png "裝載內容的購買項目會顯示為綠色，此圖表中")](changes-to-storekit-images/image26.png#lightbox)

本章節的其餘部分會顯示這些類別都已使用的範例程式碼：

### <a name="custompaymentobserver-skpaymenttransactionobserver"></a>CustomPaymentObserver (SKPaymentTransactionObserver)

變更現有`UpdatedTransactions`覆寫可檢查可下載的內容，並呼叫`StartDownloads`必要：

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

新的覆寫的方法`UpdatedDownloads`如下所示。 市集套件會呼叫這個方法之後`StartDownloads`觸發中`UpdatedTransactions`。 這個方法會呼叫*多次*不定每隔提供您的下載進度，然後再次下載已完成。 請注意此方法可接受的陣列`SKDownload`物件，讓每個方法呼叫可以提供多個佇列中下載的狀態。 中所示實作下列下載狀態會檢查每個時間並採取適當動作。

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
            Console.WriteLine ("Cancelled"); // TODO: UI?
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

這個類別包含新方法`SaveDownload`，而在每個下載順利完成之後呼叫。

已成功下載並解壓縮至裝載的內容`Cache`目錄。 結構。BYOK-KEK-PKG 檔案需要的所有檔案，以儲存在`Contents`子目錄，所以下列程式碼檔案中擷取`Contents`子目錄。

程式碼會逐一查看內容套件中的所有檔案，並將複製到`Documents`目錄下，名為的子資料夾中`ProductIdentifier`。 最後呼叫`CompleteTransaction`，而它會呼叫`FinishTransaction`付款佇列中移除交易。

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

當`FinishTransaction`稱為下載檔案不再保證處於`Cache`目錄。 應該複製所有檔案，然後再呼叫`FinishTransaction`。


## <a name="other-considerations"></a>其他考量

上述範例程式碼會示範裝載內容購買相當簡單實作。 另外還有一些重點，您必須考慮：

### <a name="detecting-updated-content"></a>偵測更新的內容

雖然可以更新裝載的內容套件，套件存放區不提供任何機制來推出這些更新已下載且購買產品的使用者。 若要實作這項功能程式碼可能會檢查新`SKProduct.ContentVersion`屬性 (如果`SKProduct`是`Downloadable`) 定期，並偵測值就會遞增。 或者，您可以建置推播通知系統。

### <a name="installing-updated-content-versions"></a>安裝更新的內容版本

上述範例程式碼會略過檔案複製如果檔案已經存在。 如果您想要支援較新版本的下載內容，這不是個好主意。

替代可能要將內容複製到版中，名為的資料夾，並且追蹤目前的版本 （例如 在`NSUserDefaults`或儲存已完成的購買記錄的任一處)。

### <a name="restoring-transactions"></a>還原交易

當`SKPaymentQueue.DefaultQueue.RestoreCompletedTransactions`是市集套件呼叫，傳回使用者的所有先前交易。 如果它們已購買大量項目，或每項採購有很大的內容套件，然後還原可能會導致大量網路流量為所有項目取得排下載一次。

請考慮追蹤是否產品已從實際的下載相關聯的內容套件的個別購買。

### <a name="pausing-restarting-and-canceling-downloads"></a>暫停、 重新啟動和取消下載

雖然範例程式碼不會示範這項功能，就可以暫停及重新啟動裝載的內容下載。 `SKPaymentQueue.DefaultQueue`具有方法`PauseDownloads`，`ResumeDownloads`和`CancelDownloads`。

如果程式碼會呼叫`FinishTransaction`下載在之前的付款佇列上`Finished`則會自動取消下載。

### <a name="setting-the-skip-backup-flag-on-the-downloaded-content"></a>下載的內容上設定略過備份旗標

Apple 的 iCloud 備份方針會建議非使用者內容也就輕鬆地還原從伺服器應該*不*備份 （因為它將會不必要地使用 iCloud 的存放裝置）。 請參閱[檔案系統的處理](~/ios/app-fundamentals/file-system.md)文件，如需有關設定備份的屬性。

## <a name="summary"></a>總結

這篇文章已導入兩個新功能的存放區套件 iOS6： 購買 iTunes 與從應用程式內的其他內容，以及使用 Apple 的伺服器來裝載您自己的應用程式內購買。 搭配現有應一併閱讀本簡介[中應用程式內購買文件](~/ios/platform/in-app-purchasing/index.md)實作市集套件功能的完整涵蓋範圍。

## <a name="related-links"></a>相關連結

- [StoreKit （範例）](https://developer.xamarin.com/samples/StoreKit/)
- [App 內購買](~/ios/platform/in-app-purchasing/index.md)
- [StoreKit Framework 參考](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/StoreKit_Collection/_index.html)
- [SKStoreProductViewController 類別參考](https://developer.apple.com/library/ios/documentation/StoreKit/Reference/SKITunesProductViewController_Ref/SKStoreProductViewController.html)
- [iTunes 搜尋 API 參考](http://www.apple.com/itunes/affiliates/resources/documentation/itunes-store-web-service-search-api.html)
- [SKDownload](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/SKDownload_Ref/Introduction/Introduction.html)
- [SKPaymentQueue](https://developer.apple.com/library/prerelease/ios/documentation/StoreKit/Reference/SKPaymentQueue_Class/Reference/Reference.html#/apple_ref/occ/instm/SKPaymentQueue/cancelDownloads:)
- [SKProduct](https://developer.apple.com/library/prerelease/ios/documentation/StoreKit/Reference/SKProduct_Reference/Reference/Reference.html#/apple_ref/occ/instp/SKProduct/downloadable)
- [WWDC 影片： 銷售產品市集套件](https://developer.apple.com/videos/wwdc/2012/?include=302#302)
