---
title: 在 Xamarin 中使用 tvOS 圖示和影像
description: 本檔說明如何在以 Xamarin 建立的 tvOS 應用程式中使用圖示和影像。 它討論啟動影像、分層影像、應用程式圖示等等。
ms.prod: xamarin
ms.assetid: A2DA4347-0563-4C72-A8D7-5B9DE9E28712
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: b1b6d07b221f702b54833bd87161d6abbadbd4e8
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2020
ms.locfileid: "78915774"
---
# <a name="working-with-tvos-icons-and-images-in-xamarin"></a>在 Xamarin 中使用 tvOS 圖示和影像

建立迷人圖示和圖像是為 Apple 電視應用程式開發沉浸式使用者體驗的重要部分。 本指南將涵蓋建立和包含 tvOS 應用程式所需之圖形資產的必要步驟：

- [啟動映射](#Launch-Image)-啟動映射會在您的應用程式第一次啟動時顯示，並在啟動完成後由應用程式的第一個畫面取代。
- 多[層式影像](#Layered-Images)-特定于 apple 電視，apple 的新分層影像會使用視差效果來建立所選項目的3d 效果。 有數種方式可[建立分層影像](#Creating-Layered-Images)。
- [應用程式圖示](#App-Icons)-不只是 Apple TV 首頁畫面，而是針對 app Store 所需的圖示。 它們必須以分層影像的形式提供。
- [最佳貨位映射](#Top-Shelf-Image)-如果您的應用程式放在主畫面的頂端列，則需要最上層的影像，以反白顯示您的應用程式功能。 （選擇性）您可以提供[動態的主要貨位內容](#Dynamic-Top-Shelf-Content)，以反白顯示應用程式中的內容。
- [Game Center 影像](#Game-Center-Images)-如果您的應用程式是遊戲並使用 Game Center，則需要數個額外的映射。
- [設定 TvOS 專案映射](#Setting-Xamarin.tvOS-Project-Images)-涵蓋為 tvOS 應用程式設定啟動映射和應用程式圖示所需的步驟。

> [!IMPORTANT]
> Apple TV 上的所有影像都是1x 解析度（`@1x`），您應該_只_使用此大小的影像。 包括較大型、較高解析度的圖形，不僅需要時間下載和使用更多的記憶體和儲存空間，還必須在執行時間動態重新調整，並且會對繪圖效能造成負面影響。

<a name="Launch-Image" />

## <a name="launch-image"></a>啟動映射

啟動映射是一開始在 Apple 電視上啟動 tvOS 應用程式時所顯示的第一件事，因此，每個 tvOS 應用程式都必須提供一個啟動映射。 

啟動影像很快就會出現，讓您的應用程式快速且迅速回應。 Apple TV 很快就會以應用程式的第一個畫面取代啟動映射。

啟動映射不是 ads 或藝術運算式的機會，它們只是為了讓您的應用程式快速啟動並可供使用的印象。

|啟動影像大小|注意事項|
|---|---|
|1920x1080px<br>|僅限非多層的 .png 檔案|

Apple 會針對設計應用程式的啟動映射提供下列建議：

- **幾乎與第一個畫面相同**-您的啟動畫面應該盡可能靠近應用程式的第一個畫面。 當第一個畫面出現時，包括不同的圖形或專案可能會導致令人討厭的「flash」。
- **避免使用文字**啟動影像是靜態的，因此在顯示之前將不會當地語系化。
- **把發行**-因為 Apple 電視使用者經常切換應用程式，所以您不應該將注意力放在應用程式啟動流程。
- **無廣告或商標**-您的啟動映射不應用來作為 [關於] 畫面或包含任何商標，除非它是應用程式第一個畫面的靜態部分。 廣告是嚴格禁止的。

<a name="Setting-the-Launch-Image" />

### <a name="setting-the-launch-image"></a>設定啟動映射

若要為您的 tvOS 專案設定啟動映射，請執行下列動作：

1. 在 **方案總管**中，按兩下 `Assets.xcassets` 將其開啟以進行編輯： 

    [![](icons-images-images/asset01.png "The Assets.xcassets file")](icons-images-images/asset01.png#lightbox)
2. 在 **資產編輯器** 中，按一下 `LaunchImages` 資產： 

    [![](icons-images-images/asset02.png "The LaunchImages asset")](icons-images-images/asset02.png#lightbox)
3. 按一下 [ **1X APPLE TV** ] 專案，然後選取 [啟動] 影像，或選擇性地從檔案系統中拖曳新的影像： 

    [![](icons-images-images/asset03.png "Select a Launch Image")](icons-images-images/asset03.png#lightbox)
4. 儲存您的變更。

<a name="Layered-Images" />

## <a name="layered-images"></a>分層影像

Apple TV 的新功能，分層影像會與視差效果搭配使用，以產生3D 效果，協助您將使用者放在沙發心裡上，以連接到房間內的螢幕內容。

多層式映射包含兩個（2）到五（5）個不同的階層，並結合以形成完整的影像。 除了背景層之外，每個圖層都會使用其迭置順序和透明度來建立深度的幻影。 當使用者與分層影像互動時，較高的 Z 排序圖層會縮放並重迭，以建立此效果。

[![](icons-images-images/layered01.png "Layered Images Z-ordered diagram")](icons-images-images/layered01.png#lightbox)

> [!IMPORTANT]
> 您的應用程式圖示需要分層影像，而且對於其他可[設定焦點的專案](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection)（例如最上層的影像）而言是選擇性的。 不過，Apple 建議針對可在應用程式中取得焦點的任何影像使用分層影像。

Apple 會針對設計您的分層影像提供下列建議：

- **讓背景圖層不透明**-您的背景層（圖層1）**必須**是不透明的，否則當您嘗試在 Apple 電視上使用多層式映射時，您會收到錯誤。 所有其他圖層都可以包含多個透明度層級，以增強3D 效果。
- **隔離前景、中間和背景**專案-將重要的元素（例如遊戲字元）放在前景中，使用中間的次要元素或陰影。 最後，包含中性背景以提供您上層的階段。
- **將文字保留在前景中**-除非您想要以較高的層級遮蔽文字，否則通常會在最上層。
- **使用簡單的分層**-視差效果的設計是微妙的，讓您的圖層保持最小，以避免 jarring、不切實際的效果。
- **包含安全區域**-因為在視差效果期間可以裁剪較高層級，所以您必須在每個圖層中建立一個安全區域框線。 如果您的內容太接近圖層邊緣，可能會遭到修剪。 較低層級的縮放和裁剪層級將會更高。 請參閱下方的重設[大小影像圖層](#Sizing-Image-Layers)一節。
- 通常應該**預覽**分層影像，以確保所需的3d 效果，並不會裁剪個別圖層上的任何內容。 您應該在實際的 Apple TV 硬體上預覽多層式影像，以確保它們會如預期般呈現。

可能的話，您應該一律使用內建的 `UIKit` 控制項來顯示階層式影像，因為它們會在焦點出現時自動取得視差效果。

<a name="Sizing-Image-Layers" />

### <a name="sizing-image-layers"></a>調整大小影像圖層

請務必記得將_安全區域_框線包含在將組成分層影像的每一層中。 因為可以在視差效果期間調整和裁剪個別圖層，所以如果圖層的內容太接近圖層的邊緣，則可以將它們裁剪掉：

[![](icons-images-images/layered02.png "35 pixel border")](icons-images-images/layered02.png#lightbox)

<a name="Creating-Layered-Images" />

### <a name="creating-layered-images"></a>建立多層式映射

tvOS 適用于下列格式的多層式映射：

- **車輛**檔案-這是 Apple 所建立的專屬資產目錄格式。 您不會直接建立汽車檔案，而是在編譯時期從任何 LSR 檔案建立，並包含在應用程式套件組合中。
- **LSR 影像**-這是 Apple 所建立的專屬影像格式。 使用[視差匯出工具 Adobe Photoshop 外掛程式](https://itunespartner.apple.com/assets/downloads/ParallaxExporter_Apps.zip)或[視差預覽](https://itunespartner.apple.com/assets/downloads/Parallax%20Previewer.dmg)程式，以 LSR 格式來建立及預覽分層影像。
- **Assets.xcassets** -從兩個（2）到五（5）標準 `.png` 包含在資產目錄中的已格式化影像，這些映射會在編譯時期編譯成汽車或 LSR 格式化的分層影像。
- **LCR**檔案-這是 Apple 所建立的專屬檔案格式。 LCR 檔案主要是用來作為從您的其中一個內容伺服器下載的其他內容。 LCR 檔案永遠不會包含在您的應用程式套件組合中。 LCR 檔案是使用 Xcode 隨附的 `layerutil` 命令列工具，從 LSR 或 Photoshop 檔案產生。

<a name="The-Parallax-Previewer" />

### <a name="the-parallax-previewer"></a>視差預覽器

Apple 已建立[視差](https://itunespartner.apple.com/assets/downloads/Parallax%20Previewer.dmg)預覽程式來預覽並建立應用程式圖示和選擇性可設定焦點專案所需的多層式映射。 預覽程式會顯示形成已完成分層影像的每個圖層：

[![](icons-images-images/layered03.png "The Parallax Previewer")](icons-images-images/layered03.png#lightbox)

預覽分層影像時，您可以使用滑鼠來旋轉影像並預覽視差效果。 使用 [ **+** （加號）] 和 [ **-** （減號）] 按鈕來新增和移除圖層。

建立新的多層式映射時，可以使用 LSR 格式匯出，並包含在您的應用程式組合中。

如需有關建立及預覽分層影像的詳細資訊，請參閱[TvOS 應用程式](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)開發人員指南的 Apple[建立視差藝術品](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/CreatingParallaxArtwork.html#//apple_ref/doc/uid/TP40015241-CH19-SW1)一節。

<a name="App-Icons" />

## <a name="app-icons"></a>應用程式圖示

您的 tvOS 應用程式不僅需要 Apple 電視首頁的應用程式圖示，也會要求 App Store 的圖示。 應用程式圖示是您第一次的變更，讓您對潛在的使用者有絕佳印象，而且應該一眼就能傳達您的應用程式用途。

[![](icons-images-images/icon01.png "The App Icon")](icons-images-images/icon01.png#lightbox)

每個應用程式都必須同時提供其應用程式圖示的小型和大型版本。 在安裝應用程式時，Apple TV 首頁畫面上會使用小型圖示。 應用程式存放區使用的是大型版本。 大型應用程式圖示應該模擬小圖示版本的外觀與風格。

|小圖示||大圖示||
|---|---|---|---|
|實際大小|400x240px|大小|1280x768px|
|安全區域大小|370x222px|||
|未取得焦點大小|300x180px|||
|焦點大小|370x222px|||

> [!IMPORTANT]
> 您的應用程式圖示必須以**分層影像**的形式提供。 如需詳細資訊，請參閱上面的「[分層影像](#Layered-Images)」一節。

Apple 提供下列建議來建立您的應用程式圖示：

- **提供單一焦點點**–以直接放在圖示中央的單一焦點點來設計您的圖示。
- **使用簡單的背景**-讓您的圖示背景更為簡單，讓上層的邊緣更為醒目。請考慮使用簡單的色彩或細微漸層。
- **限制文字數量**–因為應用程式的名稱會在使用者選取時出現在圖示下方，所以您應該只在設計圖示時包含文字。
- **不使用螢幕擷取畫面**–螢幕擷取畫面對圖示而言太複雜，且不允許使用者一目了然地查看您應用程式的用途。
- **將圖示保持為方形**– tvOS 會自動套用遮罩，以稍微四捨五入圖示的角落。 請勿自行加入此四捨五入。
- **小心地區分您的圖層**–文字應位於最上層、中間的次要專案，以及可讓您的上圖層能夠照射的中性背景。
- **小心使用漸層和陰影**–漸層和陰影可能會與視差效果產生衝突，因此應該謹慎使用。 簡單的由上而下、淺到暗漸層樣式的效果最佳。 陰影通常最適合做為銳利的硬式邊緣色調。
- **差異圖層透明度**–在應用程式圖示的上層使用不同的透明度層級，以增加3d 效果。 背景層必須是不透明的，否則會產生錯誤。

<a name="Setting-the-App-Icons" />

### <a name="setting-the-app-icons"></a>設定應用程式圖示

若要設定 tvOS 專案所需的應用程式圖示，請執行下列動作：

1. 在 **方案總管**中，按兩下 `Assets.xcassets` 將其開啟以進行編輯： 

    [![](icons-images-images/asset01.png "The Assets.xcassets fileg")](icons-images-images/asset01.png#lightbox)
2. 在 **資產編輯器** 中，展開 `App Icon & Top Shelf Image` 資產： 

    [![](icons-images-images/asset04.png "Expand the Top Shelf Image asset")](icons-images-images/asset04.png#lightbox)
3. 接下來，展開 `App Icon - Small` 資產： 

    [![](icons-images-images/asset05.png "Expand the App Icon - Small asset")](icons-images-images/asset05.png#lightbox)
4. 然後展開 [`Back` 資產]，並按一下 [`Contents`] 專案： 

    [![](icons-images-images/asset06.png "Then expand the Back asset")](icons-images-images/asset06.png#lightbox)
5. 按一下 [ **1X APPLE TV] 專案**，然後選取影像檔案。
6. 針對 `Front` 和 `Middle` 資產，重複上述步驟。
7. 然後重複相同的步驟來定義 `App Icon - Large` 資產。
8. 儲存您的變更。

<a name="Top-Shelf-Image" />

## <a name="top-shelf-image"></a>主要貨位影像

如果使用者已將您的 tvOS 應用程式放在 Apple 電視首頁螢幕上的頂端列，當使用者選取您的應用程式時，將會顯示較大的頂尖影像。 此影像應會反白顯示您應用程式的功能，或提供其內容的直接連結。

[![](icons-images-images/topshelf01.png "Top Shelf Image example")](icons-images-images/topshelf01.png#lightbox)

您可以將最上層映射當做單一靜態 `.png` 或 `.lsr` 檔案提供（請參閱[建立分層映射](#Creating-Layered-Images)），或在執行時間動態建立為可設定焦點的專案的單一資料列（請參閱下方的[動態主要貨位內容](#Dynamic-Top-Shelf-Content)）。

|主要貨位影像大小|注意事項|
|---|---|
|1920x720px|靜態 .png 或 lsr 檔案|

Apple 提供下列建議來建立您的最上層映射：

- **使用豐富的靜態影像**–如果您的應用程式未提供動態內容，其最上層影像將無法獲得可設定焦點。 使用此影像來反白顯示應用程式或品牌的功能。
- **連結至應用程式內容**–動態的主要貨位版面配置提供您的使用者在應用程式中發現最重要內容的快速連結。 使用此區域提供快速連結來啟動您的應用程式，並立即跳至指定的內容。
- **展示最新的內容**–豐富的熱門貨位內容可以將使用者繪製到您的應用程式，並使其更容易使用。 使用此區域來展示最高分或最新的內容。
- **個人化內容**–使用者將最常使用或最愛的應用程式放在主畫面的頂端列中。 使用頂端的貨位來顯示他們最感興趣的內容。
- **不允許廣告**-廣告不會顯示在最上層。 您可能會顯示最新的可購買內容，但不會顯示任何定價資訊。

### <a name="setting-the-top-shelf-image"></a>設定頂端的貨位影像

若要設定 tvOS 專案所需的前幾個貨位映射，請執行下列動作：

1. 在 **方案總管**中，按兩下 `Assets.xcassets` 將其開啟以進行編輯： 

    [![](icons-images-images/asset01.png "The Assets.xcassets file")](icons-images-images/asset01.png#lightbox)
2. 在 **資產編輯器** 中，展開 `App Icon & Top Shelf Image` 資產： 

    [![](icons-images-images/asset04.png "Expand the Top Shelf Image asset")](icons-images-images/asset04.png#lightbox)
3. 按一下 `Top Shelf Image` 資產： 

    [![](icons-images-images/asset07.png "The Top Shelf Image asset")](icons-images-images/asset07.png#lightbox)
4. 按一下 [ **1X APPLE TV] 專案**，然後選取影像檔案。
5. 儲存您的變更。

<a name="Dynamic-Top-Shelf-Content" />

### <a name="dynamic-top-shelf-content"></a>動態的主要貨位內容

最上層的貨位可以包含可[設定焦點專案](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection)的動態資料列，或動態的一組滾動橫幅，而不是顯示靜態的主要貨位影像。 這兩種動態樣式都可讓您醒目提示應用程式所提供的內容，或跳到其最常使用的功能。

<a name="Sectioned-Content-Row" />

#### <a name="sectioned-content-row"></a>分節內容資料列

這個動態的最大貨位內容類型會顯示單一資料列，可選擇將專案細分成幾個區段。 它通常用來反白顯示新的、我的最愛或最近觀看的應用程式內容。

內容會以單一水準滾動的內容清單形式呈現，且標籤會出現在所選取內容的目前部分（目前有焦點）底下。 如果使用者選取特定的內容，您的應用程式就會啟動，而且應該直接帶到該內容中。

將需要下列內容大小：

||海報（2:3）|正方形（1:1）|HDTV （16:9）|
|---|---|---|---|
|實際大小|404x608px|608x608px|908x512px|
|安全區域大小|380x570px|570x570px|852x479px|
|未取得焦點大小|333x500px|500x500px|782x440px|
|焦點大小|380x570px|570x570px|852x479px|

Apple 針對分節內容資料列提供下列建議：

- **完成資料列**–您應該提供足夠的內容，以跨越螢幕的完整寬度。
- **調整混合影像**–分節的內容資料列是設計來混合影像大小（從上面提供的清單中）。 不過，如果您混合影像大小，請注意，將會套用額外的調整，以將內容顯示標準化。

<a name="Scrolling-Inset-Banners" />

#### <a name="scrolling-inset-banners"></a>滾動內凹橫幅

（選擇性）您的 tvOS 應用程式可將其內容顯示在最上層，做為自動滾動和迴圈的橫幅集合，幾乎會填滿畫面。 此樣式通常用來展示豐富的新內容，例如新的電視節目。

除了自動滾動以外，使用者還可以控制橫幅，並使用 Siri 遠端以任一方向進行滾動。 當橫幅為焦點時，在 Siri 遙控器上進行小型的圓形手勢，將會啟用該橫幅的視差效果。

**橫幅影像（超大寬度）**

|   |   |
|---|---|
|實際大小|1940x624px|
|安全區域大小|1740x620px|
|未取得焦點大小|1740x560px|
|焦點大小|1740x620px|

滾動內凹橫幅可以當做靜態 `.png` 或多層式 `.lsr` 檔案來提供。

Apple 針對捲軸的橫幅提供下列建議：

- **內容數量**-您至少應提供三（3）個橫幅，讓您的滾動感覺自然。 您不應該包含八個以上的橫幅，也不能讓使用者流覽。
- **內容文字**-如果您的橫幅要求中的文字應該包含在橫幅影像中。 如果您使用的是多層式影像，您的文字應該會在最上層。

請參閱 Apple 的[TVServices 架構參考](https://developer.apple.com/library/prerelease/tvos/documentation/TVServices/Reference/TVServices_Ref/index.html#//apple_ref/doc/uid/TP40016412)，以深入瞭解如何將最上層延伸模組新增至您的應用程式，以提供動態的主要貨位內容。

<a name="Game-Center-Images" />

## <a name="game-center-images"></a>Game Center 影像

如果您的 tvOS 應用程式是一項遊戲，而且您已包含 Game Center 支援，則需要更多的映射資產：

- **成就圖示**-將自動裁剪成圓形的每個成就都需要不透明的影像。 成就是不可設定焦點的專案。
- **儀表板插圖**-可以提供選擇性的影像，以顯示在 Game Center 內應用程式儀表板的頂端。 這些映射是不可設定焦點的。
- **排行榜圖案**-您必須為應用程式支援的每個排行榜，提供一（1）到三（3）16:9 的外觀比例影像。 這些可能是靜態 `.png` 或多層式 `.lsr` 檔案。 排行榜的作品可獲得焦點。

||成就圖示|儀表板插圖|排行榜插圖|
|---|---|---|---|
|可見大小|200x200px|923x150px|n/a|
|實際大小|320x320px|n/a|659x371px|
|安全區域大小|n/a|n/a|618x348px|
|未取得焦點大小|n/a|n/a|548x309px|
|焦點大小|n/a|n/a|618x348px|

如需有關使用 Game Center 的詳細資訊，請參閱 Apple 的[Game Center 程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/NetworkingInternet/Conceptual/GameKit_Guide/Introduction/Introduction.html)。

<a name="Working-with-Images" />

## <a name="working-with-images"></a>使用映像

由於 tvOS 9 是 iOS 9 的子集，用來在 Xamarin iOS 應用程式中包含和顯示影像的相同技術也適用于 tvOS 應用程式。 如需詳細資訊，請參閱我們的[顯示影像](~/ios/app-fundamentals/images-icons/displaying-an-image.md)檔。

<a name="Setting-Xamarin.tvOS-Project-Images" />

## <a name="setting-xamarintvos-project-images"></a>設定 tvOS 專案映射

如上所述，所有 tvOS 應用程式都需要[啟動映射](#Launch-Image)和[應用程式圖示](#App-Icons)。 本節涵蓋在資產目錄中設定 tvOS 應用程式專案之後，如何為其選取 [啟動映射] 和 [應用程式] 圖示。

請執行下列動作：

1. 在 **方案總管**中，按兩下 `Info.plist` 將其開啟以進行編輯： 

    [![](icons-images-images/info01.png "The Info.plist file")](icons-images-images/info01.png#lightbox)
2. 在 [ **Plist 編輯器**] 中，選取**應用程式圖示**的 [資產] 類別目錄（在 [[設定應用程式圖示](#Setting-the-App-Icons)] 區段中為上方所設定）： 

    [![](icons-images-images/info02.png "The Info.Plist Editor")](icons-images-images/info02.png#lightbox)
3. 接下來，在**啟動**映射的 [[設定啟動映射](#Setting-the-Launch-Image)] 區段中，選取 [資產] 目錄（如上方所設定）。
4. 儲存您的變更。

<a name="Summary" />

## <a name="summary"></a>摘要

本文涵蓋了 tvOS 應用程式中使用的所有映射類型和大小。 首先，it 涵蓋啟動影像、分層影像、應用程式圖示、最上層影像和 Game Center 影像。 接著，它涵蓋了使用 tvOS 應用程式中的影像。

## <a name="related-links"></a>相關連結

- [tvOS 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人力介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
