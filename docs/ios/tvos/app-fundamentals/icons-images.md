---
title: 使用 tvOS 圖示和影像在 Xamarin 中
description: 本文件說明如何使用圖示和使用 Xamarin 建置的 tvOS 應用程式中的映像。 它討論啟動映像、 多層式映像、 應用程式圖示，等等。
ms.prod: xamarin
ms.assetid: A2DA4347-0563-4C72-A8D7-5B9DE9E28712
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 96af7fab366c3fd3493cf5adbf183d80b7c1ee26
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116455"
---
# <a name="working-with-tvos-icons-and-images-in-xamarin"></a>使用 tvOS 圖示和影像在 Xamarin 中

建立目眩圖示和影像是開發您的 Apple TV 應用程式的擬真的使用者體驗的重要部分。 本指南將說明如何建立並將包含所需的圖形資產 Xamarin.tvOS 應用程式所需的步驟：

- [啟動映像](#Launch-Image)-launch 影像會顯示當您的應用程式第一次啟動並完成啟動之後，會將應用程式的第一個畫面所取代。
- [分層式映像](#Layered-Images)-特有的 Apple TV、 Apple 的新分層式映像的工作與視差效果可建立 3D 效果，為選取的項目。 有數種方式可[建立分層式映像](#Creating-Layered-Images)。
- [應用程式圖示](#App-Icons)-圖示所需的不只 Apple TV 主畫面，但應用程式存放區。 他們必須提供做為分層式映像。
- [Top Shelf 影像](#Top-Shelf-Image)-如果您的應用程式會放在 [首頁] 畫面的頂端資料列，它必須以反白顯示您的應用程式功能的最上層影像。 或者，您可以提供[動態 Top Shelf 內容](#Dynamic-Top-Shelf-Content)反白顯示您的應用程式中的內容。
- [遊戲中心映像](#Game-Center-Images)-如果您的應用程式是一種遊戲，並使用 Game Center，會需要數個其他映像。
- [設定 Xamarin.tvOS 專案映像](#Setting-Xamarin.tvOS-Project-Images)-涵蓋 Xamarin.tvOS 應用程式中設定應用程式圖示與 Launch 影像所需的步驟。

> [!IMPORTANT]
> Apple 電視上的所有映像會在 1 倍解析度 (`@1x`)，您應該_只有_使用此大小的映像。 包括較大，更高解析度的圖形不只需要時間來下載並使用更多的記憶體和儲存體，但必須在執行階段會以動態方式重新調整，且會對繪圖效能造成負面的影響。

<a name="Launch-Image" />

## <a name="launch-image"></a>啟動映像

啟動映像會在 Apple TV，一開始啟動您 Xamarin.tvOS 應用程式時，會顯示第一件事，每一個 tvOS 應用程式必須在此情況下，提供啟動映像。 

啟動映像隨即快速出現，並提供您的應用程式是快速且回應迅速的印象。 Apple TV 會啟動映像以取代您的應用程式的第一個畫面短時間內那里之後。

啟動影像不是廣告或藝術運算式的好機會，它們的存在只可以讓您認為您的應用程式快速地啟動，而且準備好使用。

|啟動映像大小|注意|
|---|---|
|1920x1080px<br>|僅適用於非分層.png 檔案|

Apple 會進行下列建議來設計您的應用程式啟動映像：

- **幾乎相同的第一個畫面**-您的啟動畫面應該接近您的應用程式盡可能的第一個畫面。 包括不同的圖形或項目可能會導致惱人的 「 閃爍 」 的第一個畫面出現時。
- **避免使用文字**-啟動影像是靜態的而且此情況下，就不得當地語系化才會顯示。
- **晉級啟動**-因為 Apple TV 使用者經常切換應用程式，您不應該強調應用程式啟動程序。
- **任何廣告或商標**-您的啟動映像不應為 [關於] 畫面，或包含任何商標，除非它是您的應用程式的第一個畫面的靜態部分。 廣告會嚴格禁止。

<a name="Setting-the-Launch-Image" />

### <a name="setting-the-launch-image"></a>設定的啟動影像

若要設定您的 tvOS 專案啟動映像，請執行下列動作：

1. 在 **方案總管**，按兩下`Assets.xcassets`以開啟它進行編輯： 

    [![](icons-images-images/asset01.png "Assets.xcassets 檔案")](icons-images-images/asset01.png#lightbox)
2. 在 **資產編輯器**，按一下`LaunchImages`資產： 

    [![](icons-images-images/asset02.png "LaunchImages 資產")](icons-images-images/asset02.png#lightbox)
3. 按一下  **1x Apple TV**項目並選取 啟動映像，或從檔案系統，選擇性地將新的映像拖曳： 

    [![](icons-images-images/asset03.png "選取啟動影像")](icons-images-images/asset03.png#lightbox)
4. 儲存您的變更。

<a name="Layered-Images" />

## <a name="layered-images"></a>層次映像檔

新增 Apple TV 中，以產生可協助保護使用者心裡房間對面，以連接到螢幕上的內容中的沙發上的 3D 效果視差效果的分層式映像使用。

層次映像檔包含從兩個 （2） 若要五 （5） 區隔結合以形成完整的映像的層。 除了背景圖層，每個圖層會使用其疊置順序以及透明度，來建立深度的幻影。 當使用者互動使用分層式映像時，更高的 Z 排序階層會調整，而且重疊來建立這種效果。

[![](icons-images-images/layered01.png "多層式映像 Z 排序圖表")](icons-images-images/layered01.png#lightbox)

> [!IMPORTANT]
> 層次映像檔所需的應用程式的圖示，而是選擇性的其他[可焦點化項目](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection)（例如頂架影像中）。 不過，Apple 建議使用分層式映像進行任何可以取得焦點，在您的應用程式中的影像。




Apple 會進行下列建議來設計您的分層式映像：

- **進行背景圖層不透明**-您的背景圖層 （第 1 層）**必須**是不透明，或當您嘗試使用 Apple 電視上的分層式映像時，您會收到錯誤。 其他所有圖層可以包含多個層級的透明度，以增強 3D 效果。
- **隔離前景、 中間與背景的項目**-置於前景中的重要項目 （例如遊戲個字元）、 用於次要的項目或陰影的中間。 最後，包括中性的背景，以提供較高的分層中的階段。
- **在前景中保留文字**-除非您要為文字會被較高的層級的遮蔽，通常它應該是最上層的圖層上。
- **使用簡單的分層**-視差效果的設計目標是細微因此保留您的圖層以避免突兀，變得不切實際的影響最小。
- **包含安全區域**-因為上方的圖層可以裁剪視差效果時，您需要每個圖層內建的安全區域的框線。 如果您收到您的內容太接近層 edge 時，它可以成為裁。 多個調整及裁剪與較低層級，會發生較高的分層。 請參閱[調整大小的映像層](#Sizing-Image-Layers)下一節。
- **預覽通常**-分層式映像應該先預覽通常以確保所需的 3D 效果就會發生，且無個別的圖層上的內容會被裁剪。 您應該預覽您分層式映像實際的 Apple TV 硬體來確定它們依照預期方式轉譯。

可能的話，您應該一律使用內建`UIKit`控制項以顯示當他們回到焦點時，它們會自動取得視差效果的分層式映像。

<a name="Sizing-Image-Layers" />

### <a name="sizing-image-layers"></a>調整映像層

請務必記得加_安全區域_到每個圖層，將會撰寫分層式映像的框線。 因為個別的圖層可以調整及裁剪視差效果時，圖層的內容可以裁剪關閉，如果它是太靠近圖層的 edge:

[![](icons-images-images/layered02.png "35 的像素框線")](icons-images-images/layered02.png#lightbox)

<a name="Creating-Layered-Images" />

### <a name="creating-layered-images"></a>建立分層式映像

tvOS 運作方式與分層式映像中，格式如下：

- **汽車檔案**-這是由 Apple 建立的專屬資產目錄格式。 您不會建立汽車檔案直接，同時從任何 LSR 檔案建立在編譯時期和包含在您的應用程式套件組合。
- **LSR 映像**-這是由 Apple 建立的專屬映像格式。 使用[視差匯出工具 Adobe Photoshop 外掛程式](https://itunespartner.apple.com/assets/downloads/ParallaxExporter_Apps.zip)或[視差預覽程式](http://itunespartner.apple.com/assets/downloads/Parallax%20Previewer.dmg)建立並預覽 LSR 格式的分層式映像。
- **Assets.xcassets** -從兩個 （2） 若要五 （5） 標準`.png`格式化映像，就會編譯到汽車或 LSR 的資產目錄包含在編譯時期格式化分層式映像。
- **LCR 檔案**-這是由 Apple 建立的專屬檔案格式。 LCR 檔案的用意是要做為其中一個內容伺服器下載的其他內容。 LCR 檔案應該永遠不會包含在您的應用程式套件組合。 LCR 檔案會產生從 LSR 或 Photoshop 檔案使用`layerutil`Xcode 所隨附的命令列工具。

<a name="The-Parallax-Previewer" />

### <a name="the-parallax-previewer"></a>視差預覽程式

建立 Apple[視差預覽程式](http://itunespartner.apple.com/assets/downloads/Parallax%20Previewer.dmg)preview 並建立分層式映像所需的應用程式圖示和選擇性的可焦點化項目。 預覽程式會顯示每一層形成完整的分層式映像：

[![](icons-images-images/layered03.png "視差預覽程式")](icons-images-images/layered03.png#lightbox)

同時預覽分層式映像，您可以使用滑鼠來旋轉影像和預覽視差效果。 使用**+** （加號） 及**-** （減號） 按鈕來新增和移除的圖層。

在建立新的分層式映像時，它可以 LSR 格式匯出並包含在您的應用程式套件組合。

如需有關建立和預覽分層式映像的詳細資訊，請參閱 Apple[建立視差插圖](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/CreatingParallaxArtwork.html#//apple_ref/doc/uid/TP40015241-CH19-SW1)一節[應用程式的程式設計指南 tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)。

<a name="App-Icons" />

## <a name="app-icons"></a>應用程式圖示

Xamarin.tvOS 應用程式會需要不只是應用程式圖示 Apple 電視主畫面，而也針對 App Store 圖示。 應用程式圖示是您第一次變更，讓您的潛在使用者的絕佳的印象，並應該傳達您的應用程式用途，且一目了然。

[![](icons-images-images/icon01.png "應用程式圖示")](icons-images-images/icon01.png#lightbox)

每個應用程式必須提供一個小型和大型版本的應用程式圖示。 小圖示將使用 Apple TV 主畫面上，在安裝應用程式。 應用程式存放區使用大型的版本。 大型的應用程式圖示應該模擬小型圖示版本的外觀與風格。

|小圖示||大圖示||
|---|---|---|---|
|實際大小|400x240px|大小|1280x768px|
|安全區域的大小|370x222px|||
|未取得焦點的大小|300x180px|||
|具有焦點的大小|370x222px|||

> [!IMPORTANT]
> 必須為提供您的應用程式圖示**分層式映像**。 請參閱[分層式映像](#Layered-Images)一節以取得詳細資料。




Apple 提供下列建議來建立您的應用程式圖示：

- **提供單一婧鎏鶗懘**– 設計使用單一的焦點圖示直接置於中央的圖示。
- **使用簡單的背景**– 簡化您圖示的背景，讓較高的分層脫穎而出。請考慮使用簡單的色彩或難以察覺的漸層。
- **文字數量限制**– 因為應用程式的名稱會出現在圖示下方，選取使用者時，您應該只包含文字時務必要設計的圖示。
- **不要使用螢幕擷取畫面**-螢幕擷取畫面圖示太複雜，但是不允許使用者檢視您的應用程式，一眼的目的。
- **保持圖示的方形**– tvOS 會自動套用遮罩，稍微四捨五入邊角的圖示。 不包含此捨入自己。
- **仔細您的圖層分隔**– 文字應該是右上角，大部分的圖層、 中央和中立的背景，可讓您較高的分層，平台的次要項目。
- **使用漸層和仔細 Shadows** – 漸層和陰影可以衝突與視差效果讓它們應該小心使用。 簡單上到下，淺-深漸層停駐樣式效果最佳。 Shadows 通常最適合做為明確、 hard-edged 濃淡。
- **不同圖層的透明度**– 使用各種層級的上您的應用程式圖示，以增加 3D 效果較高層級的透明度。 背景圖層必須是不透明，或者它就會產生錯誤。

<a name="Setting-the-App-Icons" />

### <a name="setting-the-app-icons"></a>設定應用程式圖示

若要設定您的 tvOS 專案所需的應用程式圖示，請執行下列動作：

1. 在 **方案總管**，按兩下`Assets.xcassets`以開啟它進行編輯： 

    [![](icons-images-images/asset01.png "Assets.xcassets fileg")](icons-images-images/asset01.png#lightbox)
2. 在 **資產編輯器**，展開 `App Icon & Top Shelf Image`資產： 

    [![](icons-images-images/asset04.png "展開最上層影像資產")](icons-images-images/asset04.png#lightbox)
3. 接下來，依序展開`App Icon - Small`資產： 

    [![](icons-images-images/asset05.png "展開 應用程式圖示-小型的資產")](icons-images-images/asset05.png#lightbox)
4. 然後展開`Back`資產，然後按一下 `Contents`項目： 

    [![](icons-images-images/asset06.png "然後展開 上一步資產")](icons-images-images/asset06.png#lightbox)
5. 按一下  **1x Apple TV 項目**和選取的影像檔。
6. 重複上述步驟`Front`和`Middle`資產。
7. 然後重複相同步驟，以定義`App Icon - Large`資產。
4. 儲存您的變更。

<a name="Top-Shelf-Image" />

## <a name="top-shelf-image"></a>Top Shelf 影像

如果使用者具有 Xamarin.tvOS 程式置於頂端資料列，在 Apple TV 主畫面上，使用者會選取您的應用程式時，將會顯示大型的最上層影像。 此映像應該反白顯示您的應用程式的功能，或提供其內容的直接連結。

[![](icons-images-images/topshelf01.png "Top Shelf 影像範例")](icons-images-images/topshelf01.png#lightbox)

最上層影像可以提供為單一的靜態`.png`或`.lsr`檔案 (請參閱[建立分層式映像](#Creating-Layered-Images)) 或它可以動態建立在執行階段為單一資料列的可設定焦點的項目 (請參閱[動態高層貨架內容](#Dynamic-Top-Shelf-Content)下方)。

|Top Shelf 影像大小|注意|
|---|---|
|1920x720px|靜態的.png 或分層的.lsr 檔案|

Apple 提供下列建議來建立您的 Top Shelf 映像：

- **使用豐富的靜態影像**– 如果您的應用程式不會提供動態內容，其最上層影像將會非可焦點化。 您可以使用此映像，反白顯示的應用程式或您的品牌功能。
- **應用程式內容的連結**– 動態的 Top Shelf 版面配置提供快速連結到您的使用者尋找應用程式中最重要的內容。 使用此區域來提供快速的連結來啟動您的應用程式，並立即跳到指定的內容。
- **展示最新內容**– 豐富 Top Shelf 內容可以繪製您的應用程式的使用者，並將其想要使用多個它。 使用此區域的區域來展示最高評等或最新內容。
- **個人化內容**– 他們最常使用的使用者位置或慣用的應用程式主畫面的頂端資料列中。 您可以使用 Top Shelf，顯示其方式是最感興趣的內容。
- **不允許廣告**– 廣告嚴格禁止顯示在 Top Shelf。 您可能會顯示最新購買的內容，但應該顯示任何定價的資訊。

### <a name="setting-the-top-shelf-image"></a>設定 Top Shelf 影像

若要設定您的 tvOS 專案所需的最上層影像，請執行下列動作：

1. 在 **方案總管**，按兩下`Assets.xcassets`以開啟它進行編輯： 

    [![](icons-images-images/asset01.png "Assets.xcassets 檔案")](icons-images-images/asset01.png#lightbox)
2. 在 **資產編輯器**，展開 `App Icon & Top Shelf Image`資產： 

    [![](icons-images-images/asset04.png "展開最上層影像資產")](icons-images-images/asset04.png#lightbox)
3. 按一下`Top Shelf Image`資產： 

    [![](icons-images-images/asset07.png "最上層影像資產")](icons-images-images/asset07.png#lightbox)
5. 按一下  **1x Apple TV 項目**和選取的影像檔。
6. 儲存您的變更。

<a name="Dynamic-Top-Shelf-Content" />

### <a name="dynamic-top-shelf-content"></a>動態高層貨架內容

而不是顯示靜態的最上層影像，Top Shelf 可以包含的動態資料列[可焦點化項目](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection)或一組動態的捲動橫幅。 這兩個這些動態樣式可讓您反白顯示您的應用程式或跳躍到其最常使用的功能所提供的內容。

<a name="Sectioned-Content-Row" />

#### <a name="sectioned-content-row"></a>分段的內容資料列

這個動態的 Top Shelf 內容的類型會顯示單一資料列的向下捲動，選擇性地分成區段可焦點化項目。 它通常用來反白顯示新的、 最愛，或最近檢視過的應用程式內容。

內容會以單一、 水平捲動清單的內容不會出現在目前的一段選取的內容下的標籤 （目前具有焦點）。 如果使用者選取特定的一段內容，將會啟動您的應用程式，以及它們應該採取直接將該內容。

下列內容的大小就會需要：

||海報 (2:3)|正方形 (1:1)|高畫質電視 (16:9)|
|---|---|---|---|
|實際大小|404x608px|608x608px|908x512px|
|安全區域的大小|380x570px|570x570px|852x479px|
|未取得焦點的大小|333x500px|500x500px|782x440px|
|具有焦點的大小|380x570px|570x570px|852x479px|

Apple 提供的分段內容的資料列的下列建議：

- **完成資料列**– 您應該提供足夠的內容，跨越整個螢幕的寬度。
- **混合的影像縮放比例**– 分段內容的資料列設計用來保存各種影像大小 （從在上面提供的清單）。 如果您不要不過混合影像大小，請注意，額外的調整將會套用到標準化顯示內容。

<a name="Scrolling-Inset-Banners" />

#### <a name="scrolling-inset-banners"></a>捲動內凹橫幅

（選擇性） Xamarin.tvOS 應用程式可以為自動捲動和迴圈的幾乎填滿螢幕的橫幅集合 Top Shelf 呈現其內容。 此樣式通常用來示範豐富、 新的內容，例如新的電視節目。

除了自動捲動，使用者可以掌控橫幅，並使用 Siri 遠端任一方向捲動。 進行小型，循環的筆勢 Siri 遠端橫幅會在成為焦點時，將會啟用該橫幅視差效果。

**橫幅影像 （額外的寬型）**

|   |   |
|---|---|
|實際大小|1940x624px|
|安全區域的大小|1740x620px|
|未取得焦點的大小|1740x560px|
|具有焦點的大小|1740x620px|

捲動內凹橫幅是可為靜態`.png`分層或`.lsr`檔案。

Apple 提供捲動內凹橫幅的下列建議：

- **內容量**-您應該提供至少三 （3） 橫幅的向下捲動到感覺自然。 您應該包含不超過 8 （8） 橫幅或它讓瀏覽不易供一般使用者。
- **內容文字**-如果您的橫幅可讓您要求中的文字應該包含在橫幅影像。 如果您使用分層式映像，您的文字應該是最上層的圖層。

請參閱 Apple [TVServices Framework 參考](https://developer.apple.com/library/prerelease/tvos/documentation/TVServices/Reference/TVServices_Ref/index.html#//apple_ref/doc/uid/TP40016412)如需有關將 Top Shelf 延伸模組新增至您的應用程式，以提供動態的 Top Shelf 內容。

<a name="Game-Center-Images" />

## <a name="game-center-images"></a>Game Center 映像

如果 Xamarin.tvOS 應用程式是遊戲，且您已包含 Game Center 的支援，就必須使用數個更多的影像資產：

- **成就圖示**-不透明的映像會需要每個將自動裁剪成的圓形的成就。 成就是非可設定焦點的項目。
- **儀表板圖檔**-選用的映像可提供將會出現在 Game Center 中的應用程式的儀表板的頂端。 這些映像為非可焦點化項目。
- **排行榜插圖**-您必須提供之間一 （1） 到三 （3） 16:9 外觀比例映像的每個應用程式支援的排行榜。 這些可能是靜態`.png`分層或`.lsr`檔案。 排行榜插圖是可焦點化項目。

||成就圖示|儀表板圖檔|排行榜插圖|
|---|---|---|---|
|顯示大小|200x200px|923x150px|N/A|
|實際大小|320x320px|N/A|659x371px|
|安全區域的大小|N/A|N/A|618x348px|
|未取得焦點的大小|N/A|N/A|548x309px|
|具有焦點的大小|N/A|N/A|618x348px|

如需有關使用 Game Center 的詳細資訊，請參閱 Apple [Game Center Programming Guide](https://developer.apple.com/library/prerelease/tvos/documentation/NetworkingInternet/Conceptual/GameKit_Guide/Introduction/Introduction.html)。

<a name="Working-with-Images" />

## <a name="working-with-images"></a>使用影像

TvOS 9 是 iOS 9 的子集，因為相同的技術，用來包含和在 Xamarin.iOS 應用程式中顯示影像也會適用於 Xamarin.tvOS 應用程式中。 請參閱我們[顯示影像](~/ios/app-fundamentals/images-icons/displaying-an-image.md)文件的詳細資訊。

<a name="Setting-Xamarin.tvOS-Project-Images" />

## <a name="setting-xamarintvos-project-images"></a>設定 Xamarin.tvOS 專案映像

如上所述，所有的 tvOS 應用程式需要[啟動映像](#Launch-Image)，並[應用程式圖示](#App-Icons)。 本章節涵蓋後，選取 應用程式圖示與 Launch 影像 Xamarin.tvOS 應用程式專案的資產目錄設定。

請執行下列動作：

1. 在 **方案總管**，按兩下`Info.plist`以開啟它進行編輯： 

    [![](icons-images-images/info01.png "Info.plist 檔案")](icons-images-images/info01.png#lightbox)
2. 在**Info.Plist 編輯器**，選取資產目錄 (設定上述[設定應用程式圖示](#Setting-the-App-Icons)一節) 的**應用程式圖示**: 

    [![](icons-images-images/info02.png "Info.Plist 編輯器")](icons-images-images/info02.png#lightbox)
3. 接下來，選取 Assets Catalog (設定上述[設定啟動映像](#Setting-the-Launch-Image)一節) 的**啟動影像**。
4. 儲存您的變更。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已涵蓋所有的映像類型與 Xamarin.tvOS 應用程式中使用的大小。 首先，它涵蓋啟動映像、 分層式映像、 應用程式圖示、 Top Shelf 影像和 Game Center 映像。 然後，它涵蓋與 Xamarin.tvOS 應用程式中的映像的工作。

## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
