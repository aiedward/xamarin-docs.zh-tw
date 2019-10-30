---
title: iOS 6 中對 StoreKit 所做的變更
description: iOS 6 導入了兩項商店套件 API 變更：從您的應用程式中顯示 iTunes （和 App Store/iBookstore）產品的功能，以及 Apple 將用來裝載可下載檔案的新應用程式內購買選項。 本檔說明如何使用 Xamarin 來執行這些功能。
ms.prod: xamarin
ms.assetid: 253D37D7-44C7-D012-3641-E15DC41C2699
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 1d49be1f4339b658e8202d4091b9a12b45d7b507
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031913"
---
# <a name="changes-to-storekit-in-ios-6"></a>iOS 6 中對 StoreKit 所做的變更

_iOS 6 對商店套件 API 引進了兩項變更：從您的應用程式中顯示 iTunes （和 App Store/iBookstore）產品的功能，以及 Apple 將用來裝載可下載檔案的新 [應用程式內購買] 選項。本檔說明如何使用 Xamarin 來執行這些功能。_

在 iOS6 中儲存套件的主要變更為這兩項新功能：

- **應用程式內內容顯示 & 購買**-使用者可以購買並下載應用程式、音樂、書籍和其他 iTunes 內容，而不需要離開您的應用程式。 您也可以連結到您自己的應用程式來推廣購買，或只是鼓勵評論和評等。
- **應用程式內購買**裝載的內容– Apple 會儲存並提供與您的應用程式內購買產品相關聯的內容，這不需要個別的伺服器來裝載您的檔案，而會自動支援背景下載並讓您撰寫較少的程式碼。

如需 StoreKit Api 的詳細涵蓋範圍，請參閱[應用程式內購買](~/ios/platform/in-app-purchasing/index.md)指南。

## <a name="requirements"></a>需求

本檔中討論的商店套件功能需要 iOS 6 和 Xcode 4.5，以及 Xamarin. iOS 6.0。

## <a name="in-app-content-display--purchasing"></a>應用程式內內容顯示 & 購買

IOS 中新的應用程式內購買功能可讓使用者從您的應用程式中查看產品資訊，並購買或下載產品。
先前的應用程式必須觸發 iTunes、App Store 或 iBookstore，這會導致使用者離開原始應用程式。 這項新功能會在完成時自動將使用者傳回您的應用程式。

[![](changes-to-storekit-images/image1.png "Automatically returning to an app after purchase")](changes-to-storekit-images/image1.png#lightbox)

如何使用此方法的範例包括：

- **鼓勵使用者對您的應用程式進行評分**–您可以開啟 [app Store] 頁面，讓使用者可以對應用程式進行評分和審核，而不需要離開。
- **交叉推廣應用程式**–允許使用者查看您發佈的其他應用程式，並能夠立即購買/下載。
- **協助使用者尋找及下載內容**–協助使用者購買您的應用程式所尋找、管理或匯總的內容（例如 音樂相關應用程式可以提供歌曲的播放清單，並允許從應用程式內購買每一首歌曲）。

一旦顯示 `SKStoreProductViewController` 之後，使用者就可以像在 iTunes、App Store 或 iBookstore 中一樣，與產品資訊互動。 使用者可以：

- 查看螢幕擷取畫面（適用于應用程式）、
- 範例歌曲或影片（適用于音樂、電視節目和電影）、
- 閱讀（及寫入）評論，
- 購買 & 下載，這完全是在 view controller 和 Store 套件中進行。

`SKStoreProductViewController` 中的某些選項仍然會強制使用者離開您的應用程式，並開啟相關的存放區應用程式，例如按一下 [**相關產品**] 或應用程式的**支援**連結。

### <a name="skstoreproductviewcontroller"></a>SKStoreProductViewController

在任何應用程式中顯示產品的 API 很簡單：只需要您建立和顯示 `SKStoreProductViewController`。 請遵循下列步驟來建立並顯示產品：

1. 建立 `StoreProductParameters` 物件，以將參數傳遞至視圖控制器，包括在此函式中的 `productId`。
1. 具現化 `SKProductViewController`。 將它指派給類別層級欄位。
1. 將處理常式指派給視圖控制器的 `Finished` 事件，這應該會關閉視圖控制器。 當使用者按下 [取消] 時，就會呼叫此事件。或以其他方式在 view controller 內完成交易。
1. 呼叫傳入 `StoreProductParameters` 和完成處理常式的 `LoadProduct` 方法。 完成處理常式應該檢查產品要求是否成功，如果是，則會以強制回應方式呈現 `SKProductViewController`。 應新增適當的錯誤處理，以防無法抓取產品。

### <a name="example"></a>範例

本文的*StoreKit*範例程式碼中的*ProductView*專案會執行可接受任何產品的 Apple ID 並顯示 `SKStoreProductViewController`的 `Buy` 方法。 下列程式碼會顯示任何指定 Apple ID 的產品資訊：

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

應用程式在執行時看起來像下面的螢幕擷取畫面–下載或購買完全在 `SKStoreProductViewController`內進行：

[![](changes-to-storekit-images/image2.png "The app looks like this when running")](changes-to-storekit-images/image2.png#lightbox)

### <a name="supporting-older-operating-systems"></a>支援舊版作業系統

範例應用程式包含的程式碼會示範如何在舊版 iOS 中開啟 App Store、iTunes 或 iBookstore。 使用 `OpenUrl` 方法來開啟適當製作的**Itunes.com** URL。

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

### <a name="errors"></a>錯誤

如果您使用的 Apple ID 無效，將會發生下列錯誤，這可能會造成混淆，因為它暗示了某種類型的網路或驗證問題。

 `Error Domain=SKErrorDomain Code=5 "Cannot connect to iTunes Store"`

### <a name="reading-objective-c-documentation"></a>讀取目標-C 檔

在 Apple 開發人員入口網站上閱讀存放套件的開發人員，會看到與這項新功能相關的通訊協定– [SKStoreProductViewControllerDelegate](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/SKITunesProductViewControllerDelegate_ProtocolRef/Reference/Reference.html) –討論。 委派通訊協定只有一個方法– productViewControllerDidFinish –已公開為 Xamarin 中 `SKStoreProductViewController` 的 `Finished` 事件。

## <a name="determining-apple-ids"></a>判斷 Apple Id

`SKStoreProductViewController` 所需的 Apple ID 是*數位*（不會與 "mwc2012" 之類的配套識別碼混淆）。 有幾種不同的方式可讓您找出您想要顯示之產品的 Apple ID，如下所示：

### <a name="itunesconnect"></a>iTunesConnect

針對您發佈的應用程式，在 iTunes Connect 中可以輕鬆地找到**APPLE ID** ：

[![](changes-to-storekit-images/image3.png "Finding the Apple ID in iTunes Connect")](changes-to-storekit-images/image3.png#lightbox)

 <a name="Search_API" />

### <a name="search-api"></a>搜尋 API

Apple 提供動態搜尋 API 來查詢 App Store、iTunes 和 iBookstore 中的所有產品。 如需如何存取搜尋 API 的資訊，請參閱[Apple 的分支機搆資源](https://www.apple.com/itunes/affiliates/resources/documentation/itunes-store-web-service-search-api.html)，雖然 API 會公開給任何人（而不只是註冊的關係企業）。 您可以剖析產生的 JSON，以探索要搭配 `SKStoreProductViewController`使用的 Apple ID `trackId`。

結果也會包含其他中繼資料，包括可在應用程式中用來呈現產品的顯示資訊和插圖 Url。

以下是一些範例：

- **iBooks 應用程式**– [https://itunes.apple.com/search?term=ibooks&amp ; 實體 = 軟體&amp; 國家/地區 = 美國](https://itunes.apple.com/search?term=ibooks&amp;entity=software&amp;country=us)
- **點和 Kangaroo ibook store** – [https://itunes.apple.com/search?term=dot+and+the+kangaroo&amp ; entity = 電子書&amp; country = us](https://itunes.apple.com/search?term=dot+and+the+kangaroo&amp;entity=ebook&amp;country=us)

### <a name="enterprise-partner-feed"></a>企業合作夥伴摘要

Apple 為核准合作夥伴提供所有產品的完整資料傾印，其形式為可下載資料庫的一般檔案。 如果您有資格存取[企業合作夥伴](https://www.apple.com/itunes/affiliates/resources/documentation/itunes-enterprise-partner-feed.html)摘要，則可以在該資料集中找到任何產品的 Apple ID。

企業合作夥伴摘要的許多使用者都是「[分支機搆計畫](https://www.apple.com/itunes/affiliates)」的成員，可讓您贏得「產品銷售」的傭金。 `SKStoreProductViewController` 不支援分支機搆識別碼（在撰寫本文時）。

### <a name="direct-product-links"></a>直接產品連結

您可以從其 iTunes Preview URL 連結推斷產品的 Apple ID。
在任何 iTunes 產品連結（適用于應用程式、音樂或書籍）中，尋找開頭為 `id` 的 URL 部分，並使用後面的數位。

例如，iBooks 的直接連結是

```csharp
http://itunes.apple.com/us/app/ibooks/id364709193?mt=8
```

而 Apple ID 為**364709193**。 同樣地，對於 MWC2012 應用程式，直接連結是

```csharp
http://itunes.apple.com/us/app/mwc-2012-unofficial/id496963922?mt=8
```

而 Apple ID 為**496963922**。

## <a name="in-app-purchase-hosted-content"></a>應用程式內購買裝載的內容

如果您的應用程式內購買包含可下載的內容（例如書籍或其他媒體、遊戲層級的美工和設定，或其他大型檔案），則這些檔案會裝載在您的 web 伺服器上，而應用程式必須納入程式碼，以便在之後安全地下載購. 從 iOS 6 開始，Apple 會在其伺服器上裝載您的檔案，而不需要個別的伺服器。 此功能僅適用于非使用中的產品（不是可取用或訂用帳戶）。 使用 Apple 主控服務的優點包括：

- 節省裝載 & 頻寬成本。
- 可能比您目前使用的任何伺服器主機更具擴充性。 
- 較少撰寫程式碼，因為您不需要建立任何伺服器端處理。 
- 系統會為您實作為背景下載。

注意：不支援在 iOS 模擬器中測試裝載的應用程式內購買內容，因此您必須使用實際裝置進行測試。

### <a name="hosted-content-basics"></a>託管內容基本概念

在 iOS 6 之前，有兩種方式可以提供產品（在[Xamarin 的應用程式內購買](~/ios/platform/in-app-purchasing/index.md)檔中有更詳細的說明）：

- **內建產品**–藉由購買來「解除鎖定」的功能，但已內建于應用程式中（如程式碼或內嵌資源）。 內建產品的範例包括已解除鎖定的相片篩選器或遊戲中的電源。
- **伺服器提供的產品**-購買後，應用程式必須從您操作的伺服器下載內容。 此內容會在購買期間下載，並儲存在裝置上，然後轉譯為提供產品的一部分。 範例包括書籍、雜誌問題，或包含背景圖樣和設定檔案的遊戲等級。

在 iOS 6 中，Apple 提供了伺服器交付產品的變化：它們會在其伺服器上裝載您的內容檔案。 這可讓您更輕鬆地建立伺服器提供的產品，因為您不需要操作個別的伺服器，而且存放套件提供了您先前必須自行撰寫的背景下載功能。 若要利用 Apple 的裝載，請針對新的應用程式內購買產品啟用內容裝載，並修改您的商店套件程式碼以利用它。 接著會使用 Xcode 建立產品內容檔案，並將其上傳至 Apple 的伺服器，以供審查和發行。

[![](changes-to-storekit-images/image4.png "The build and deliver process")](changes-to-storekit-images/image4.png#lightbox)

使用 App Store 提供*託管內容*的應用程式內購買，需要進行下列安裝和設定：

- **ITunes Connect** –您*必須*已提供您的銀行和稅務資訊給 Apple，才能代表您收取所收集的資金。 接著，您可以將產品設定為銷售，並設定沙箱使用者帳戶來測試購買。  _您也必須針對您想要與 Apple 一起裝載的非使用中產品，設定主控的內容_。
- **iOS 布建入口網站**–建立套件組合識別碼，並啟用應用程式的 app Store 存取權，就像任何支援應用程式內購買的應用程式一樣。
- **商店套件**–將程式碼新增至您的應用程式，以顯示產品、購買產品和還原交易。  _在 iOS 6 商店套件中，也會在背景中使用進度更新來管理您的產品內容下載。_
- **自訂程式碼**–用來追蹤客戶所進行的購買，並提供他們所購買的產品或服務。 利用新的 iOS 6 存放套件類別（例如 `SKDownload`）來抓取 Apple 主控的內容。

下列各節說明如何使用本文的範例程式碼，從建立和上傳封裝到管理購買和下載程式，以執行裝載的內容。

### <a name="sample-code"></a>程式碼範例

範例專案*HostedNonConsumables* （在 StoreKitiOS6 中）會使用裝載的內容。 此應用程式提供兩個「書籍章節」來銷售，其內容裝載于 Apple 的伺服器上。 內容是由文字檔和影像所組成，雖然在實際的應用程式中可以使用更複雜的內容。

在購買之前、期間和之後，應用程式看起來像這樣：

 [![](changes-to-storekit-images/image5.png "The app looks like this before, during and after a purchase")](changes-to-storekit-images/image5.png#lightbox)

系統會下載文字檔和影像，並將其複製到應用程式的 [檔] 目錄。 如需有關應用程式儲存區可用之不同目錄的詳細資訊，請參閱[檔案系統檔](~/ios/app-fundamentals/file-system.md)。

## <a name="itunes-connect"></a>iTunes Connect

建立將使用 Apple 內容裝載的新產品時，請務必選取 [**不可**使用] 產品類型。 其他產品類型則不支援內容裝載。 此外，您不應該為您銷售的*現有*產品啟用內容裝載;僅開啟新產品的內容裝載。

 [![](changes-to-storekit-images/image6.png "Select the Non-Consumable product type")](changes-to-storekit-images/image6.png#lightbox)

輸入**產品識別碼**。 稍後當您建立此產品的內容時，將會需要此識別碼。

 [![](changes-to-storekit-images/image7.png "Enter a Product ID")](changes-to-storekit-images/image7.png#lightbox)

內容裝載會在 [詳細資料] 區段中設定。 在應用程式內購買上線之前，如果您想要取消，請取消核取 [**使用 Apple 主控內容**] 核取方塊（即使您已經上傳一些測試內容）。 不過，在應用程式內購買已上線之後，無法移除內容裝載。

 [![](changes-to-storekit-images/image8.png "Hosting content with Apple")](changes-to-storekit-images/image8.png#lightbox)

一旦您開啟裝載內容，產品將會輸入**等待上傳**狀態並顯示此訊息：

 [![](changes-to-storekit-images/image9.png "The product will enter Waiting for Upload status and show this message")](changes-to-storekit-images/image9.png#lightbox)

內容套件應該使用 Xcode 建立，並使用封存工具來上傳。 建立內容套件的指示會在下一節的**建立中提供.PKG**檔案。

## <a name="creating-pkg-files"></a>製作.PKG 檔案

您上傳至 Apple 的內容檔案必須符合下列限制：

- 大小不得超過 2 GB。
- 不能包含可執行檔程式碼（或指向內容外的符號連結）。
- 格式必須正確（包括**plist**檔案），且副檔名為 **.pkg** 。 如果您使用 Xcode 來遵循這些指示，就會自動完成此動作。

您可以新增許多不同的檔案和檔案類型，只要它們符合這些限制即可。 在您的程式碼存取之前，會先壓縮內容，再傳遞至您的應用程式並由存放套件解壓縮。

上傳內容套件之後，您可以使用較新的內容來取代它。 必須上傳並提交新的內容，才能透過正常程式進行審核/核准。 遞增更新內容套件中的 `ContentVersion` 欄位，表示它是較新的。

### <a name="xcode-in-app-purchase-content-projects"></a>Xcode 應用程式內購買內容專案

建立應用程式內購買產品的內容套件目前需要 Xcode。 不需要任何目標 C 編碼;Xcode 有一個新的專案類型，適用于這些套件，其中只包含您的檔案和 plist。

我們的範例應用程式包含銷售的書籍章節–每個章節內容套件將包含：

- 文字檔，以及
- 用來表示章節的影像。

首先從功能表中選取 [檔案] **> [新增專案**]，然後選擇 [**應用程式內購買內容**]：

 [![](changes-to-storekit-images/image10.png "Choose In-App Purchase Content")](changes-to-storekit-images/image10.png#lightbox)

輸入 [**產品名稱**] 和 [**公司識別碼**]，讓 [套件組合**識別碼**] 符合您在 iTunes Connect 中為此產品輸入的**產品識別碼**。

[![](changes-to-storekit-images/image11.png "Enter the  Name and Identifier")](changes-to-storekit-images/image11.png#lightbox)

現在您會有一個空白的**應用程式內購買內容**專案。 您可以用滑鼠右鍵按一下並**新增檔案 ...** 或將其拖曳至 [**專案導覽器**]。 請確定**ContentVersion**正確（從1.0 開始，但如果您稍後選擇更新內容，請記得將它遞增）。

這個螢幕擷取畫面會顯示 Xcode，其中包含專案中的內容檔案，且 plist 專案會顯示在主視窗中：

[![](changes-to-storekit-images/image12.png "This screenshot shows Xcode with the content files included in the project and the plist entries visible in the main window")](changes-to-storekit-images/image12.png#lightbox)

新增所有內容檔案之後，您可以儲存此專案，稍後再重新編輯，或開始上傳程式。

## <a name="uploading-pkg-files"></a>上傳.PKG 檔案

上傳內容套件最簡單的方式是使用**Xcode Archive Tool**。 從功能表選擇 [**產品 >** 封存] 以開始：

![](changes-to-storekit-images/image13.png "Choose Archiven")

內容套件隨後會出現在封存中，如下所示。 封存類型和圖示顯示這一行是**應用程式內購買內容**封存。 按一下 [**驗證 ...** ] 若要檢查我們的內容套件是否有錯誤，而不實際執行上傳。

[![](changes-to-storekit-images/image14.png "Validate the package")](changes-to-storekit-images/image14.png#lightbox)

使用您的 iTunes Connect 認證登入：

[![](changes-to-storekit-images/image15.png "Login with your iTunes Connect credentials")](changes-to-storekit-images/image15.png#lightbox)

選擇正確的應用程式和應用程式內購買，以關聯此內容：

[![](changes-to-storekit-images/image16.png "Choose the correct application and in-app purchase to associate this content with")](changes-to-storekit-images/image16.png#lightbox)

您應該會看到類似此螢幕擷取畫面的訊息：

![範例沒有問題的訊息](changes-to-storekit-images/image17.png "範例沒有問題的訊息")

現在請執行類似的程式，但按一下 [**散發 ...** ] 實際上會上傳內容。

[![散發應用程式](changes-to-storekit-images/image18.png "散發應用程式")](changes-to-storekit-images/image18.png#lightbox)

選取第一個選項來上傳內容：

![上傳內容](changes-to-storekit-images/image19.png "上傳內容")

再次登入：

[![](changes-to-storekit-images/image15.png "Login in")](changes-to-storekit-images/image15.png#lightbox)

選擇正確的應用程式和應用程式內購買記錄，將內容上傳至：

[![](changes-to-storekit-images/image20.png "Choose the application and in-app purchase record")](changes-to-storekit-images/image20.png#lightbox)

等候您的檔案上傳：

[![](changes-to-storekit-images/image21.png "The content upload dialog")](changes-to-storekit-images/image21.png#lightbox)

上傳完成時，會出現一則訊息，通知您內容已提交至 App Store。

[![](changes-to-storekit-images/image22.png "An example successful upload message")](changes-to-storekit-images/image22.png#lightbox)

完成後，當您返回 iTunes Connect 上的產品頁面時，它會顯示套件詳細資料，並**準備好提交**狀態。 當產品處於此狀態時，您可以在沙箱環境中開始測試。 您不需要在沙箱中「提交」產品來進行測試。

[![](changes-to-storekit-images/image23.png "iTunes Connect it will show the package details and be in Ready to Submit status")](changes-to-storekit-images/image23.png#lightbox)

這可能需要一些時間（例如 幾分鐘），上傳封存和更新的 iTunes Connect 狀態。 您可以單獨提交產品進行審核，或將它與應用程式二進位檔一起提交。 只有在 Apple 正式核准之後，才會在生產 App Store 中提供內容，以便在您的應用程式中進行購買。

### <a name="pkg-file-format"></a>.PKG 檔案格式

使用 Xcode 和封存工具來建立並上傳裝載的內容套件，表示您永遠不會看到套件本身的內容。 針對範例應用程式所建立之套件中的檔案和目錄看起來像下面的螢幕擷取畫面，而根目錄中的**plist**檔案和**內容**子目錄中的產品檔案如下：

[![](changes-to-storekit-images/image24.png "The plist file in the root and the product files in a Contents subdirectory")](changes-to-storekit-images/image24.png#lightbox)

請注意套件的目錄結構（尤其是檔案在 `Contents` 子目錄中的位置），因為您必須瞭解這項資訊，才能從裝置上的套件解壓縮檔案。

### <a name="updating-package-content"></a>更新套件內容

核准後更新內容的程式：

- 在 Xcode 中編輯應用程式內購買內容專案。
- 增加版本號碼。
- 再次上傳至 iTunes Connect。 後續的購買者會自動取得最新版本，但已有舊版本的使用者將不會收到任何通知。
- 您的應用程式會負責通知使用者，並鼓勵他們取得較新版本的內容。 應用程式也必須使用存放區套件的還原功能，建立可下載新版本的函式。
- 若要判斷是否有較新的版本，您可以在應用程式中建立功能以提取 SKProducts （例如， 用來抓取產品價格的相同進程，並比較 ContentVersion 屬性。

## <a name="purchasing-overview"></a>購買總覽

閱讀本節之前，請先參閱現有的[應用程式內購買檔](~/ios/platform/in-app-purchasing/index.md)。

當您購買並下載具有託管內容的產品時，所發生的事件順序如下圖所示：

[![](changes-to-storekit-images/image25.png "The sequence of events that occurs when a product with hosted content is purchased and download")](changes-to-storekit-images/image25.png#lightbox)

1. 新產品可在 iTunes Connect 中建立，並啟用託管內容。 實際的內容會在 Xcode 中分開建立（就像將檔案拖曳到資料夾中一樣），然後封存並上傳至 iTunes （不需要撰寫程式碼）。 然後，每個產品都會提交以供核准，之後就可以購買。 在範例程式碼中，這些產品識別碼已硬式編碼，但如果您將可用的產品清單儲存在遠端伺服器上，以便在將新的產品和內容提交至 iTunes Connect 時進行更新，則會更有彈性地裝載內容給 Apple。
1. 當使用者購買產品時，會將交易放在付款佇列中進行處理。
1. 商店套件會將採購要求轉寄到 iTunes 伺服器進行處理。
1. ITunes 伺服器上的交易已完成（例如 客戶需支付費用），並將回條傳回給應用程式，其中會額外產品資訊，包括是否可下載（如果有的話，檔案大小和其他中繼資料）。
1. 您的程式碼應該會檢查產品是否可供下載，如果是的話，也會提出同時放在付款佇列上的內容下載要求。 商店套件會將此要求傳送至 iTunes 伺服器。
1. 伺服器會將內容檔案傳回給儲存套件，這會提供回呼來將下載進度和剩餘的時間評估傳回給您的程式碼。
1. 完成後，您會收到通知，並在快取資料夾中傳遞檔案位置。
1. 您的程式碼應該複製檔案並加以驗證，並儲存您需要記住產品已購買的任何狀態。 請利用這個機會在新檔案上正確地設定備份旗標（提示：如果它們是來自伺服器，而且不是由使用者進行編輯，您可能會略過備份，因為使用者一律可以在未來從 Apple 的伺服器抓取這些檔案）。
1. 呼叫 FinishTransaction。 此步驟很重要，因為它會從付款佇列中移除交易。 您也必須先將內容複寫到快取目錄之後，才能呼叫 FinishTransaction。 一旦您呼叫 FinishTransaction，就可能很快就會清除快取的檔案。

## <a name="implementing-hosted-content-purchase"></a>執行託管內容購買

下列資訊應與完整的[應用程式內購買檔](~/ios/platform/in-app-purchasing/index.md)一起閱讀。 本檔中的資訊著重于裝載的內容與先前的執行之間的差異。

### <a name="classes"></a>類別

已新增或更改下列類別，以支援 iOS 6 中的託管內容：

- **SKDownload** –代表下載進行中的新類別。 API 允許一個以上的每個產品，但一開始只會執行一個。
- **SKProduct** –新增的屬性： `Downloadable`、`ContentVersion``ContentLengths` 陣列。
- **SKPaymentTransaction** –新增的屬性： `Downloads`，其中包含 `SKDownload` 物件的集合（如果此產品有可供下載的內容）。
- **SKPaymentQueue** –新增的方法： `StartDownloads`。 請使用 `SKDownload` 物件來呼叫這個方法，以提取其裝載的內容。 下載可能會在背景中發生。
- **SKPaymentTransactionObserver** –新的方法： `UpdateDownloads`。 Store 套件會呼叫此方法，其中包含目前下載作業的相關進度資訊。

新 `SKDownload` 類別的詳細資料：

- **進度**–0-1 之間的值，可讓您用來向使用者顯示百分比完整的指標。 請勿使用 [進度 = = 1] 來偵測下載是否已完成，請檢查 [狀態 = = 已完成]。
- **TimeRemaining** –估計剩餘的下載時間（以秒為單位）。 -1 表示它仍在計算估計值。
- **狀態**–作用中、等待中、已完成、失敗、已暫停、已取消。
- **ContentURL** –內容放在磁片上的檔案位置，位於 `Cache` 目錄中。 只有在下載完成後才會填入。
- **錯誤**–如果狀態為 [失敗]，請檢查此屬性。

範例程式碼中的類別之間的互動會顯示在此圖中（裝載的內容購買的程式碼以綠色顯示）：

[![](changes-to-storekit-images/image26.png "Hosted content purchases is shown in green in this diagram")](changes-to-storekit-images/image26.png#lightbox)

本節的其餘部分會顯示已使用這些類別的範例程式碼：

### <a name="custompaymentobserver-skpaymenttransactionobserver"></a>CustomPaymentObserver (SKPaymentTransactionObserver)

變更現有的 `UpdatedTransactions` 覆寫以檢查可下載的內容，並視需要呼叫 `StartDownloads`：

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

新的覆寫方法 `UpdatedDownloads` 如下所示。 儲存套件會在 `UpdatedTransactions`中觸發 `StartDownloads` 之後，呼叫這個方法。 這個方法會以不定的間隔多次呼叫，以提供您下載進度，然後在下載完成時再進行一*次*。 請注意，方法會接受 `SKDownload` 物件的陣列，因此，每個方法呼叫都可提供佇列中多個下載的狀態。 如以下的執行所示，每次都會檢查下載狀態，並採取適當的動作。

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

這個類別包含新的方法 `SaveDownload`，會在每次下載成功完成後呼叫。

已成功下載託管內容，並將其解壓縮到 `Cache` 目錄。 的結構.PKG 檔案需要將所有檔案儲存在 `Contents` 子目錄中，因此下列程式碼會從 `Contents` 子目錄中解壓縮檔案。

程式碼會逐一查看內容套件中的所有檔案，並將這些檔案複製到 `Documents` 目錄中，並在名為的子資料夾中進行 `ProductIdentifier`。 最後，它會呼叫 `CompleteTransaction`，這會呼叫 `FinishTransaction` 以從付款佇列中移除交易。

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

呼叫 `FinishTransaction` 時，下載的檔案將不再保證會在 `Cache` 目錄中。 在呼叫 `FinishTransaction`之前，應該先複製所有檔案。

## <a name="other-considerations"></a>其他考量

上述範例程式碼示範相當簡單的裝載內容購買的執行。 還有一些您必須考慮的其他重點：

### <a name="detecting-updated-content"></a>偵測更新的內容

雖然您可以更新託管的內容套件，但商店套件並不提供任何機制，將這些更新推送給已下載並購買產品的使用者。 若要執行此功能，您的程式碼可能會定期檢查新的 `SKProduct.ContentVersion` 屬性（如果 `SKProduct` `Downloadable`），並偵測值是否遞增。 或者，您也可以建立推播通知系統。

### <a name="installing-updated-content-versions"></a>安裝更新的內容版本

上述範例程式碼會在檔案已經存在時，略過檔案複製。 如果您想要支援下載的較新版本內容，這不是個好主意。

另一個替代方式可能是將內容複寫到名為的資料夾中，並追蹤哪一個是最新版本（例如， 在 `NSUserDefaults` 或您儲存已完成之採購記錄的任何位置。

### <a name="restoring-transactions"></a>還原交易

呼叫 `SKPaymentQueue.DefaultQueue.RestoreCompletedTransactions` 時，商店套件會傳回使用者先前所有的交易。 如果他們購買了大量的專案，或者每個購買都有大型的內容套件，則還原可能會導致大量的網路流量，因為所有內容會一次排入佇列以供下載。

請考慮追蹤產品是否與實際下載的相關內容套件分開購買。

### <a name="pausing-restarting-and-canceling-downloads"></a>暫停、重新開機和取消下載

雖然範例程式碼不會示範這項功能，但您可以暫停和重新開機主控的內容下載。 `SKPaymentQueue.DefaultQueue` 具有 `PauseDownloads`、`ResumeDownloads` 和 `CancelDownloads`的方法。

如果在下載 `Finished` 之前，程式碼會在付款佇列上呼叫 `FinishTransaction`，則會自動取消該下載。

### <a name="setting-the-skip-backup-flag-on-the-downloaded-content"></a>在下載的內容上設定略過備份旗標

Apple 的 iCloud 備份指導方針會建議您不應該備份從伺服器輕鬆還原的非使用者內容（因為它*不*需要使用 iCloud 存放裝置）。 如需設定 backup 屬性的詳細資訊，請參閱[檔案系統檔](~/ios/app-fundamentals/file-system.md)。

## <a name="summary"></a>總結

本文在 iOS6 中引進了兩項新功能：在您的應用程式中購買 iTunes 和其他內容，以及利用 Apple 的伺服器來裝載您自己的應用程式內購買。 本簡介應與現有的[應用程式內購買檔](~/ios/platform/in-app-purchasing/index.md)一起閱讀，以取得完整的商店套件功能涵蓋範圍。

## <a name="related-links"></a>相關連結

- [StoreKit （範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/storekit)
- [App 內購買](~/ios/platform/in-app-purchasing/index.md)
- [StoreKit 架構參考](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/StoreKit_Collection/_index.html)
- [SKStoreProductViewController 類別參考](https://developer.apple.com/library/ios/documentation/StoreKit/Reference/SKITunesProductViewController_Ref/SKStoreProductViewController.html)
- [iTunes 搜尋 API 參考](https://www.apple.com/itunes/affiliates/resources/documentation/itunes-store-web-service-search-api.html)
- [SKDownload](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/SKDownload_Ref/Introduction/Introduction.html)
- [SKPaymentQueue](https://developer.apple.com/library/prerelease/ios/documentation/StoreKit/Reference/SKPaymentQueue_Class/Reference/Reference.html#/apple_ref/occ/instm/SKPaymentQueue/cancelDownloads:)
- [SKProduct](https://developer.apple.com/library/prerelease/ios/documentation/StoreKit/Reference/SKProduct_Reference/Reference/Reference.html#/apple_ref/occ/instp/SKProduct/downloadable)
- [WWDC 影片：使用商店套件銷售產品](https://developer.apple.com/videos/wwdc/2012/?include=302#302)
