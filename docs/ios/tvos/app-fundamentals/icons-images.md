---
title: 在 Xamarin 中使用 tvOS 圖示和影像
description: 本檔說明如何在以 Xamarin 建立的 tvOS 應用程式中使用圖示和影像。 它會討論啟動影像、分層影像、應用程式圖示等等。
ms.prod: xamarin
ms.assetid: A2DA4347-0563-4C72-A8D7-5B9DE9E28712
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 26d4d5ef5f21d16e0de79579c4552e48975a4e2b
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91437135"
---
# <a name="working-with-tvos-icons-and-images-in-xamarin"></a>在 Xamarin 中使用 tvOS 圖示和影像

建立迷人圖示和影像是為 Apple TV 應用程式開發沉浸式使用者體驗的重要部分。 本指南將涵蓋為您的 tvOS 應用程式建立和包含必要圖形資產所需的步驟：

- [啟動映射](#Launch-Image) -當您的應用程式第一次啟動時，即會顯示啟動影像，並在啟動完成後，由應用程式的第一個畫面取代。
- 多[層式影像](#Layered-Images)-apple TV 專用，apple 的新分層影像會使用視差效果來為選取的專案建立3d 效果。 有數種方式可 [建立多層式影像](#Creating-Layered-Images)。
- [應用程式圖示](#App-Icons) -不只需要 Apple TV 首頁畫面的圖示，而是 App Store。 它們必須以分層影像的形式提供。
- [最高貨架影像](#Top-Shelf-Image) -如果您的應用程式是放在主畫面的頂端列，則需要有最大的貨位影像來反白顯示您的應用程式功能。 （選擇性）您可以提供 [動態頂端的內容](#Dynamic-Top-Shelf-Content) ，以反白顯示應用程式中的內容。
- [Game Center 映射](#Game-Center-Images) -如果您的應用程式是遊戲並使用 Game Center，則需要數個額外的映射。
- [設定 TvOS 專案映射](#Setting-Xamarin.tvOS-Project-Images) -涵蓋為您的 tvOS 應用程式設定啟動映射和應用程式圖示所需的步驟。

> [!IMPORTANT]
> Apple TV 上的所有映射都是 () 的1x 解析度 `@1x` ，因此您應該 _只_ 使用此大小的影像。 包含較大、更高解析度的圖形不僅需要時間下載並使用更多記憶體和儲存空間，但必須在執行時間動態重新調整，而且會對繪製效能造成負面影響。

<a name="Launch-Image"></a>

## <a name="launch-image"></a>啟動映射

啟動映射是您的 tvOS 應用程式一開始在 Apple 電視上啟動時所顯示的第一件事，因此，每個 tvOS 應用程式都必須提供啟動映射。

啟動影像會快速出現，並讓您的應用程式快速且迅速回應。 Apple TV 將會在之後的第一個畫面中，將啟動映射取代為應用程式的第一個畫面。

啟動影像不是 ads 或藝術運算式的機會，其存在只是為了讓您的應用程式快速啟動並可供使用的印象。

|啟動映射大小|備註|
|---|---|
|1920x1080px<br>|僅限非分層 .png 檔案|

Apple 針對設計您應用程式的啟動映射提出下列建議：

- 與**第一個畫面幾乎相同**-您的啟動畫面應該盡可能接近應用程式的第一個畫面。 當第一個畫面出現時，包括不同的圖形或專案可能會導致討厭的「flash」。
- **避免使用文字** 啟動影像是靜態的，因此不會在顯示之前當地語系化。
- **把啟動** -由於 Apple TV 使用者經常切換應用程式，因此您不應該注意應用程式啟動流程。
- **沒有廣告或商標** -您的啟動影像不應用來作為「關於」畫面或包含任何商標，除非它是應用程式第一個畫面的靜態部分。 嚴格禁止廣告。

<a name="Setting-the-Launch-Image"></a>

### <a name="setting-the-launch-image"></a>設定啟動映射

若要設定 tvOS 專案的啟動映射，請執行下列動作：

1. 在 [ **方案總管**中，按兩下 `Assets.xcassets` 以開啟它進行編輯：

    [![>appicon 檔案](icons-images-images/asset01.png)](icons-images-images/asset01.png#lightbox)
2. 在 [ **資產編輯器**] 中，按一下 `LaunchImages` 資產：

    [![LaunchImages 資產](icons-images-images/asset02.png)](icons-images-images/asset02.png#lightbox)
3. 按一下 [ **1X APPLE TV** ] 專案，然後選取 [啟動] 影像，或從檔案系統選擇性地拖曳新的影像：

    [![選取啟動映射](icons-images-images/asset03.png)](icons-images-images/asset03.png#lightbox)
4. 儲存變更。

<a name="Layered-Images"></a>

## <a name="layered-images"></a>分層影像

Apple TV 的新功能，分層影像會與視差效果一起運作，以產生3D 效果，以協助將沙發心裡的使用者連接到房間內的螢幕內容。

分層影像包含兩個 (2) 至五個 (5) 個別的層級，這些層級會合並以形成完整的影像。 除了背景層之外，每個圖層都會使用其迭置順序以及透明度來建立深度的幻影。 當使用者與多層式影像互動時，會縮放較高的 Z 排序圖層以建立此效果。

[![分層影像 Z 排序圖表](icons-images-images/layered01.png)](icons-images-images/layered01.png#lightbox)

> [!IMPORTANT]
> 您應用程式的圖示需要多層式影像，而其他可 [設定焦點的專案](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection) 則是選擇性的， (例如最上層貨架影像) 。 不過，Apple 建議針對可在您應用程式中取得焦點的任何影像使用分層映射。

Apple 針對設計您的多層式影像做出下列建議：

- 將**背景層設**為不透明- (第1層的背景層) **必須**是不透明的，否則當您嘗試在 Apple 電視上使用階層式影像時，將會收到錯誤。 所有其他圖層都可包含多個透明度層級，以增強3D 效果。
- **隔離前景、中間和背景元素** -將顯著的元素 (像是遊戲字元) 在前景中，使用次要元素或陰影的中間。 最後，請包含中立的背景，以提供您上層的階段。
- **將文字保留在前景中** -除非您想要讓文字以較高的層級遮蔽，否則通常會在最上層。
- **使用簡單的分層** -視差效果是設計成微妙的，因此將您的圖層維持在最小的程度，以防止 jarring、不切實際的效果。
- **包含安全區域** -因為在視差效果期間可以裁剪較上層，所以您需要在每個圖層建立一個安全區域框線。 如果您的內容太接近圖層邊緣，則可能會遭到裁剪。 上層層級會比較低層體驗更多縮放和裁剪。 請參閱下方的 [大小調整映射圖層](#Sizing-Image-Layers) 一節。
- **預覽通常** 會經常預覽分層影像，以確保所需的3d 效果發生，而且個別圖層上的內容都不會被裁剪。 您應該在實際的 Apple TV 硬體上預覽分層影像，以確保其如預期般呈現。

可能的話，您應該一律使用內建 `UIKit` 控制項來顯示分層影像，因為它們會在焦點出現時自動取得視差效果。

<a name="Sizing-Image-Layers"></a>

### <a name="sizing-image-layers"></a>調整影像圖層的大小

請務必記得將 _安全區域_ 框線包含在將組成分層影像的每個圖層中。 由於個別層級可以在視差效果期間進行縮放和裁剪，因此，如果圖層的內容太接近圖層的邊緣，就可以將其裁剪：

[![35圖元框線](icons-images-images/layered02.png)](icons-images-images/layered02.png#lightbox)

<a name="Creating-Layered-Images"></a>

### <a name="creating-layered-images"></a>建立多層式影像

tvOS 適用于下列格式的分層影像：

- **CAR** 檔案-這是 Apple 所建立的專屬資產目錄格式。 您不會直接建立汽車檔案，它們會在編譯時期從任何 LSR 檔案建立，並包含在應用程式套件組合中。
- **LSR 影像** -這是 Apple 所建立的專屬映射格式。 使用 [視差匯出工具 Adobe Photoshop 外掛程式](https://itunespartner.apple.com/assets/downloads/ParallaxExporter_Apps.zip) 或 [視差預覽](https://itunespartner.apple.com/assets/downloads/Parallax%20Previewer.dmg) 程式，以 LSR 格式建立和預覽分層影像。
- **>appicon** -從兩個 (2) 至五個 (5) `.png` 包含在資產目錄中的標準格式化影像，在編譯時期會編譯成汽車或 LSR 格式化的分層影像。
- **LCR** 檔案-這是 Apple 所建立的專屬檔案格式。 LCR 檔案的目的是用來作為從您的其中一個內容伺服器下載的其他內容。 LCR 檔案絕不能包含在應用程式套件組合中。 LCR 檔案是使用 `layerutil` Xcode 隨附的命令列工具，從 LSR 或 Photoshop 檔案產生。

<a name="The-Parallax-Previewer"></a>

### <a name="the-parallax-previewer"></a>視差預覽器

Apple 建立了 [視差](https://itunespartner.apple.com/assets/downloads/Parallax%20Previewer.dmg) 預覽程式來預覽和建立應用程式圖示和選擇性可設定可設定專案所需的分層映射。 預覽程式會顯示形成完成的分層映射的每個圖層：

[![視差預覽器](icons-images-images/layered03.png)](icons-images-images/layered03.png#lightbox)

預覽分層影像時，您可以使用滑鼠來旋轉影像並預覽視差效果。 使用 **+** (加) 和 **-** (減去) 按鈕來新增和移除圖層。

建立新的分層映射時，可以使用 LSR 格式匯出，並包含在應用程式套件組合中。

如需有關建立和預覽多層式影像的詳細資訊，請參閱[tvOS 的應用程式程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)中 Apple 的[建立視差作品](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/CreatingParallaxArtwork.html#//apple_ref/doc/uid/TP40015241-CH19-SW1)一節。

<a name="App-Icons"></a>

## <a name="app-icons"></a>應用程式圖示

您的 tvOS 應用程式不只需要 Apple TV 首頁畫面的應用程式圖示，也會要求 App Store 的圖示。 應用程式圖示是您第一次的變更，可讓您的潛在使用者印象相當印象，並應一目了然地傳達您的應用程式用途。

[![應用程式圖示](icons-images-images/icon01.png)](icons-images-images/icon01.png#lightbox)

每個應用程式都必須提供小型和大型版本的應用程式圖示。 安裝應用程式時，會在 Apple TV 首頁畫面上使用小型圖示。 App Store 使用大型版本。 大型應用程式圖示應模仿小型圖示版本的外觀和風格。

|小圖示|解決方案|大型圖示|解決方案|
|---|---|---|---|
|實際大小|400x240px||1280x768px|
|安全區域大小|370x222px|||
|未取得焦點大小|300x180px|||
|焦點大小|370x222px|||

> [!IMPORTANT]
> 您必須將應用程式圖示提供為 **分層影像**。 如需詳細資料，請參閱上面的「 [分層影像](#Layered-Images) 」一節。

Apple 提供下列建議來建立您的應用程式圖示：

- **提供單一焦點點** -設計您的圖示，並將單一焦點放在圖示的中央。
- **使用簡單的背景** –讓您的圖示背景保持簡單，讓上層更上層。請考慮使用簡單的色彩或微妙漸層。
- **限制文字數量** –因為當使用者選取應用程式的名稱時，該應用程式的名稱將會出現在圖示的下方，您應該只在對圖示的設計有必要時，才包含文字。
- **不要使用螢幕擷取畫面** -螢幕擷取畫面對圖示而言太複雜，因此不允許使用者一目了然。
- **保留圖示方形** – tvOS 會自動套用遮罩，以稍微四捨五入圖示的角落。 請勿自行包含此舍入。
- **小心地區隔您的圖層** –文字應在最上層、中間的次要專案，以及允許您的上層的中性背景。
- **小心使用漸層和陰影** ，漸層和陰影可能會與視差效果衝突，因此應該謹慎使用。 簡單的由上而下、淺到深的漸層樣式效果最好。 陰影通常最適合用來做為銳利、硬邊邊的色調。
- **不同圖層透明度** -在應用程式圖示的上層使用不同的透明度層級，以增加3d 效果。 背景層必須是不透明的，否則會產生錯誤。

<a name="Setting-the-App-Icons"></a>

### <a name="setting-the-app-icons"></a>設定應用程式圖示

若要設定 tvOS 專案所需的應用程式圖示，請執行下列動作：

1. 在 [ **方案總管**中，按兩下 `Assets.xcassets` 以開啟它進行編輯：

    [![>appicon fileg](icons-images-images/asset01.png)](icons-images-images/asset01.png#lightbox)
2. 在 [ **資產編輯器**] 中，展開 `App Icon & Top Shelf Image` 資產：

    [![展開頂端的商店影像資產](icons-images-images/asset04.png)](icons-images-images/asset04.png#lightbox)
3. 接下來，展開 `App Icon - Small` 資產：

    [![展開應用程式圖示-小型資產](icons-images-images/asset05.png)](icons-images-images/asset05.png#lightbox)
4. 然後展開 `Back` 資產，然後按一下 `Contents` 專案：

    [![然後展開備份資產](icons-images-images/asset06.png)](icons-images-images/asset06.png#lightbox)
5. 按一下 [ **1X APPLE TV] 專案** ，然後選取影像檔案。
6. 針對和資產重複上述步驟 `Front` `Middle` 。
7. 然後重複相同的步驟來定義 `App Icon - Large` 資產。
8. 儲存變更。

<a name="Top-Shelf-Image"></a>

## <a name="top-shelf-image"></a>最高貨架影像

如果使用者已將您的 tvOS 應用程式放在 Apple TV 首頁畫面上的頂端列，當使用者選取您的應用程式時，將會顯示大型的最高貨架影像。 此影像應醒目提示您應用程式的功能，或提供其內容的直接連結。

[![最高貨架影像範例](icons-images-images/topshelf01.png)](icons-images-images/topshelf01.png#lightbox)

您可以將最上層的影像提供為單一靜態 `.png` 或檔案 `.lsr` (請參閱 [建立多層式影像](#Creating-Layered-Images)) 或可在執行時間以動態方式建立為可設定焦點的專案的單一資料列 (請參閱下方的 [動態頂端內容](#Dynamic-Top-Shelf-Content)) 。

|最大貨架影像大小|備註|
|---|---|
|1920x720px|靜態 .png 或 lsr 檔案|

Apple 提供下列建議來建立您的前貨架映射：

- **使用豐富的靜態影像** –如果您的應用程式未提供動態內容，其最高的貨位映射將不可設定為可設定焦點。 您可以使用此影像來醒目提示應用程式或品牌的功能。
- **連結至應用程式內容** –動態頂端貨位配置可提供您的使用者在應用程式中最重要之內容的快速連結。 使用此區域以提供快速連結來啟動您的應用程式，並立即跳到指定的內容。
- **展示最新的內容** –豐富的最新內容可以在您的應用程式中繪製使用者，並讓他們想要使用它。 您可以使用此區域來展示最高分或最新的內容。
- **個人化內容** -使用者將最常使用或最愛的應用程式放在主畫面的頂端列。 使用頂尖貨架來顯示他們最感興趣的內容。
- **不允許廣告** –廣告會嚴格禁止顯示在最上方的貨架中。 您可能會顯示最新的可購買內容，但不應該顯示任何價格資訊。

### <a name="setting-the-top-shelf-image"></a>設定最上層貨架影像

若要設定 tvOS 專案所需的最高貨架影像，請執行下列動作：

1. 在 [ **方案總管**中，按兩下 `Assets.xcassets` 以開啟它進行編輯：

    [![>appicon 檔案](icons-images-images/asset01.png)](icons-images-images/asset01.png#lightbox)
2. 在 [ **資產編輯器**] 中，展開 `App Icon & Top Shelf Image` 資產：

    [![展開頂端的商店影像資產](icons-images-images/asset04.png)](icons-images-images/asset04.png#lightbox)
3. 按一下 `Top Shelf Image` 資產：

    [![最高貨架影像資產](icons-images-images/asset07.png)](icons-images-images/asset07.png#lightbox)
4. 按一下 [ **1X APPLE TV] 專案** ，然後選取影像檔案。
5. 儲存變更。

<a name="Dynamic-Top-Shelf-Content"></a>

### <a name="dynamic-top-shelf-content"></a>動態頂端內容

最上層貨架可以包含動態資料列的可 [設定的專案](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection) ，或動態的滾動橫幅集合，而不是顯示靜態的最上層貨架影像。 這兩種動態樣式可讓您反白顯示應用程式所提供的內容，或跳到最常使用的功能。

<a name="Sectioned-Content-Row"></a>

#### <a name="sectioned-content-row"></a>分節內容資料列

此動態頂端貨位內容類型會顯示單一資料列的滾動，可選擇性地細分為各區段的可設定焦點專案。 它通常用來反白顯示新的、我的最愛或最近觀看的應用程式內容。

內容會以單一、水準滾動的內容清單的形式呈現，而此標籤會出現在目前選取的內容 (目前焦點) 。 如果使用者選取特定的內容，您的應用程式就會啟動，而且應該直接移至該內容。

將需要下列內容大小：

|大小|海報 (2:3) |平方 (1:1) |HDTV (16:9) |
|---|---|---|---|
|實際大小|404x608px|608x608px|908x512px|
|安全區域大小|380x570px|570x570px|852x479px|
|未取得焦點大小|333x500px|500x500px|782x440px|
|焦點大小|380x570px|570x570px|852x479px|

Apple 針對分節內容資料列提供下列建議：

- **完成資料列** –您應該提供足夠的內容來跨越畫面的全寬度。
- **調整混合影像** -分段內容資料列的設計目的，是為了保留影像大小的混合， (自上述) 所提供的清單。 但是，如果您混合影像大小，請注意將會套用額外的調整，以將內容顯示正規化。

<a name="Scrolling-Inset-Banners"></a>

#### <a name="scrolling-inset-banners"></a>滾動內凹橫幅

（選擇性）您的 tvOS 應用程式可以將其內容顯示在頂端貨架中，以自動滾動並迴圈顯示幾乎填滿畫面的橫幅。 此樣式通常用來展示豐富的新內容，例如新電視節目。

除了自動滾動之外，使用者也可以控制橫幅，並使用 Siri 遙控器的任一方向進行滾動。 當橫幅成為焦點時，在 Siri 遙控器上進行小型的圓形手勢將會啟動該橫幅的視差效果。

**橫幅影像 (額外的寬) **

| 大小 | 解決方案 |
|---|---|
|實際大小|1940x624px|
|安全區域大小|1740x620px|
|未取得焦點大小|1740x560px|
|焦點大小|1740x620px|

滾動內凹橫幅可作為靜態或多層式檔案提供 `.png` `.lsr` 。

Apple 針對滾動內凹橫幅提供下列建議：

- **內容數量** -您至少應該提供三 (3) 橫幅，讓滾動感覺自然。 您應包含不超過八個 (8) 橫幅，否則它會對使用者造成流覽困難。
- **內容文字** -如果您的橫幅要求中的文字應該包含在橫幅影像中。 如果您使用多層式影像，您的文字應該會在最上層。

如需在應用程式中新增最高貨架延伸模組的詳細資訊，請參閱 Apple 的 [TVServices Framework 參考](https://developer.apple.com/library/prerelease/tvos/documentation/TVServices/Reference/TVServices_Ref/index.html#//apple_ref/doc/uid/TP40016412) 資料，以提供動態的最高貨架內容。

<a name="Game-Center-Images"></a>

## <a name="game-center-images"></a>Game Center 映射

如果您的 Xamarin. tvOS 應用程式是一項遊戲，且您已包含 Game Center 支援，則需要多個映射資產：

- **成就圖示** -將自動裁剪成圓形的每個成就都需要不透明的影像。 成就是不可設定的專案。
- **儀表板圖稿** -可提供選擇性的影像，以顯示在 Game Center 中的應用程式儀表板頂端。 這些映射不可設定為可設定焦點。
- **排行榜圖稿** -您必須為您的應用程式所支援的每個排行榜，提供一個 (1) 至三個 (3) 16:9 的外觀比例影像。 這些可能是靜態或多層式檔案 `.png` `.lsr` 。 排行榜作品是可設定的。

|大小|成就圖示|儀表板藝術|排行榜作品|
|---|---|---|---|
|可見大小|200x200px|923x150px|n/a|
|實際大小|320x320px|n/a|659x371px|
|安全區域大小|n/a|n/a|618x348px|
|未取得焦點大小|n/a|n/a|548x309px|
|焦點大小|n/a|n/a|618x348px|

如需有關使用 Game Center 的詳細資訊，請參閱 Apple 的 [Game Center 程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/NetworkingInternet/Conceptual/GameKit_Guide/Introduction/Introduction.html)。

<a name="Working-with-Images"></a>

## <a name="working-with-images"></a>使用影像

因為 tvOS 9 是 iOS 9 的子集，所以用來在 Xamarin iOS 應用程式中包含及顯示影像的相同技術，也適用于 tvOS 應用程式。 如需詳細資訊，請參閱 [顯示影像](~/ios/app-fundamentals/images-icons/displaying-an-image.md) 檔。

<a name="Setting-Xamarin.tvOS-Project-Images"></a>

## <a name="setting-xamarintvos-project-images"></a>設定 tvOS 專案影像

如上所述，所有 tvOS apps 都需要 [啟動映射](#Launch-Image)和 [應用程式圖示](#App-Icons)。 本節涵蓋在資產目錄中設定 tvOS 應用程式專案之後，為其選取 [啟動映射] 和 [應用程式] 圖示。

執行下列動作：

1. 在 [ **方案總管**中，按兩下 `Info.plist` 以開啟它進行編輯：

    [![Plist 檔案](icons-images-images/info01.png)](icons-images-images/info01.png#lightbox)
2. 在**Plist 編輯器**中，于**應用程式圖示**的 [[設定應用程式圖示](#Setting-the-App-Icons)] 區段) ，選取上方設定的資產目錄 (：

    [![Plist 編輯器](icons-images-images/info02.png)](icons-images-images/info02.png#lightbox)
3. 接下來，在 [啟動映射] 的 [啟動映射] 區段中，選取 [[啟動映射](#Setting-the-Launch-Image)] 區段中設定的 [資產目錄] (**) 。**
4. 儲存變更。

<a name="Summary"></a>

## <a name="summary"></a>摘要

本文涵蓋了 tvOS 應用程式中使用的所有映射類型和大小。 首先，它涵蓋了啟動影像、分層影像、應用程式圖示、最高貨架影像和 Game Center 影像。 然後，它會說明如何使用 tvOS 應用程式中的映射。

## <a name="related-links"></a>相關連結

- [tvOS 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人體介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [適用于 tvOS 的應用程式程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)