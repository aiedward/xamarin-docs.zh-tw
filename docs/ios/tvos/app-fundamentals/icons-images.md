---
title: "使用圖示和映像"
description: "本文涵蓋設計和使用圖示和 Xamarin.tvOS 應用程式內的映像。"
ms.topic: article
ms.prod: xamarin
ms.assetid: A2DA4347-0563-4C72-A8D7-5B9DE9E28712
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 3e3d1663e07b16721d1aa7253e7d0150a609718e
ms.sourcegitcommit: 5fc1c4d17cd9c755604092cf7ff038a6358f8646
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/17/2018
---
# <a name="working-with-icons-and-images"></a>使用圖示和映像

_本文涵蓋設計和使用圖示和 Xamarin.tvOS 應用程式內的映像。_

建立幻象圖示和圖像是開發應用程式 Apple TV 沈浸式使用者經驗很重要的一部分。 本指南將說明如何建立並包含必要的圖形資產 Xamarin.tvOS 應用程式所需的步驟：

- [啟動映像](#Launch-Image)-當您的應用程式第一次啟動並取代為應用程式的第一個畫面啟動完成後，會顯示啟動映像。
- [分層式映像](#Layered-Images)-特有 Apple TV，Apple 的新分層映像的工作與視差效果可建立 3D 效果，針對選取的項目。 有數種[建立分層映像](#Creating-Layered-Images)。
- [應用程式圖示](#App-Icons)-圖示所需的不只 Apple TV 首頁畫面上，但應用程式存放區。 他們必須提供做為分層的映像。
- [排名最前面的貨架映像](#Top-Shelf-Image)-如果您的應用程式放置在 [首頁] 螢幕的上方資料列，它必須要反白顯示您的應用程式功能的頂端放到書架映像。 或者，您可以提供[動態的上架內容](#Dynamic-Top-Shelf-Content)來反白顯示您的應用程式中的內容。
- [遊戲中心映像](#Game-Center-Images)-如果您的應用程式是遊戲，並且使用 Game Center，將需要數個其他映像。
- [設定 Xamarin.tvOS 專案影像](#Setting-Xamarin.tvOS-Project-Images)-涵蓋 Xamarin.tvOS 應用程式設定應用程式圖示與啟動映像所需的步驟。

> [!IMPORTANT]
> **注意：** Apple TV 上的所有映像會在 1 x 解析度 (`@1x`)，而且您應該使用_只_使用此大小的影像。 包括較大，更高解析度圖形不只需要時間來下載並使用更多的記憶體和儲存空間，但必須在執行階段會以動態方式重新調整，並將對繪圖效能造成負面的影響。

<a name="Launch-Image" />

## <a name="launch-image"></a>啟動映像

啟動映像上的 Apple TV，一開始啟動 Xamarin.tvOS 應用程式時，會顯示第一件事並因此，每個 tvOS 應用程式必須提供啟動映像。 

啟動映像會顯示快速，並提供您的應用程式是快速且回應迅速的印象。 Apple TV 會啟動映像以取代您的應用程式的第一個畫面短時間內那里之後。

啟動映像不廣告或藝術運算式有機會、 它們的目的是為了讓您以為您的應用程式快速地啟動，並準備好使用。

|啟動映像大小|注意|
|---|---|
|1920x1080px|僅適用於非分層.png 檔案|

Apple 會進行下列建議來設計您的應用程式啟動映像：

- **幾乎與第一個畫面的相同**-您的啟動螢幕應該是您的應用程式的第一個畫面是盡可能接近。 包括不同的圖形或項目可能會導致惱人"快閃 」 的第一個畫面出現時。
- **避免使用文字**-啟動映像都是靜態，然後在這種情況，不會當地語系化才會顯示。
- **Downplay 啟動**-因為 Apple TV 使用者經常切換應用程式，您不應該特別注意的應用程式的啟動程序。
- **任何廣告或商標設定**-您啟動映像不應該當做 [關於] 畫面，或包含任何商標，除非它是您的應用程式的第一個畫面的靜態部分。 嚴格禁止廣告。

<a name="Setting-the-Launch-Image" />

### <a name="setting-the-launch-image"></a>設定啟動映像

若要設定 tvOS 專案啟動映像，請執行下列動作：

1. 在**方案總管 中**，連按兩下`Assets.xcassets`開啟進行編輯： 

    [![](icons-images-images/asset01.png "Assets.xcassets 檔案")](icons-images-images/asset01.png#lightbox)
2. 在**資產編輯器**，按一下`LaunchImages`資產： 

    [![](icons-images-images/asset02.png "LaunchImages 資產")](icons-images-images/asset02.png#lightbox)
3. 按一下**1 x Apple TV**項目和選取啟動映像，或從檔案系統 （選擇性） 拖曳中的新映像： 

    [![](icons-images-images/asset03.png "選取啟動映像")](icons-images-images/asset03.png#lightbox)
4. 儲存您的變更。

<a name="Layered-Images" />

## <a name="layered-images"></a>分層的映像

新增 Apple TV，分層映像使用視差效果可產生 3D 效果，可協助讓使用者在沙發上心理遙控器輕鬆連線至螢幕上的內容。

分層的映像包含來自兩個 （2） 若要五 （5） 個別結合以形成完整的映像的圖層。 除了背景圖層，每個圖層會使用其疊置順序以及透明度建立深度的幻影。 當使用者互動的分層式映像時，較高的 Z 排序層會調整，而且建立這種效果重疊。

[![](icons-images-images/layered01.png "分層的映像 Z 排序圖表")](icons-images-images/layered01.png#lightbox)

> [!IMPORTANT]
> **注意：**分層映像所需的應用程式的圖示和其他選擇性[可設定焦點的項目](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection)（例如上架映像）。 不過，Apple 建議使用分層的映像可以取得焦點，在您的應用程式中的任何映像。




Apple 會進行下列建議來設計您分層的映像：

- **進行背景圖層不透明**-您的背景圖層 （第 1 層）**必須**是不透明，或當您嘗試在 Apple TV 上使用分層映像時，會出現的錯誤。 其他所有圖層可以包含多個層級的透明度，增強的 3D 效果。
- **找出前景中, 介和背景的項目**-置於前景顯著的項目 （例如遊戲個字元），用於次要的項目或陰影的中間。 最後，包括中性的背景，以提供較高的分層中的階段。
- **保持在前景文字**-除非您想文字，以隱藏較高的層級，通常它應該是最上層的圖層上。
- **使用簡單分層**-視差效果的設計目標是難以察覺到最少的防止突兀，不實際的效果，所以將保留您的圖層。
- **包含安全區域**-因為上層可以裁剪視差效果時，您需要在每個圖層中建置安全區域框線。 如果您收到您的內容太接近層邊緣，它可以成為裁剪。 上層會發生多個縮放比例及裁剪較低層級。 請參閱[縮放映像層](#Sizing-Image-Layers)下一節。
- **預覽通常**-分層式映像應該先預覽通常以確保所需的 3D 效果就會發生，且無個別的圖層上的內容會被裁剪。 您應該預覽分層圖像實際 Apple TV 硬體以確定它們依照預期方式轉譯。

可能的話，您應該一律使用內建`UIKit`控制項以顯示您分層的映像，因為它們進入焦點時，它們會自動得到視差效果。

<a name="Sizing-Image-Layers" />

### <a name="sizing-image-layers"></a>縮放影像圖層

請務必記得納入_安全區域_到每個圖層構成層疊影像的框線。 因為個別的圖層可以調整及裁剪視差效果時，圖層的內容可以從掉，如果它是圖層的邊緣太靠近：

[![](icons-images-images/layered02.png "35 像素框線")](icons-images-images/layered02.png#lightbox)

<a name="Creating-Layered-Images" />

### <a name="creating-layered-images"></a>建立分層映像

tvOS 運作方式與分層映像中，格式如下：

- **CAR 檔案**-這是由 Apple 建立的一種專屬資產目錄格式。 您不汽車檔案會直接建立，它們是從任何 LSR 檔案建立在編譯時期，包含在您的應用程式套件組合。
- **LSR 映像**-這是由 Apple 建立專屬的影像格式。 使用[視差匯出工具 Adobe Photoshop 外掛程式](https://itunespartner.apple.com/assets/downloads/ParallaxExporter_Apps.zip)或[視差預覽程式](http://itunespartner.apple.com/assets/downloads/Parallax%20Previewer.dmg)來建立和 LSR 格式預覽分層的影像。
- **Assets.xcassets** -從兩個 （2） 若要五 （5） 標準`.png`格式化的影像，就會編譯到汽車或 LSR 資產類別中包含在編譯時期格式分層映像。
- **用於支援 LCR 檔案**-這是由 Apple 建立專屬的檔案格式。 用於支援 LCR 檔案被要做為其中一個內容伺服器下載的其他內容。 用於支援 LCR 檔案應該永遠不會包含在您的應用程式套件組合。 從使用 LSR 或 Photoshop 檔案產生 LCR 檔案`layerutil`隨附 Xcode 命令列工具。

<a name="The-Parallax-Previewer" />

### <a name="the-parallax-previewer"></a>視差預覽程式

建立 Apple[視差預覽程式](http://itunespartner.apple.com/assets/downloads/Parallax%20Previewer.dmg)預覽和建立分層映像所需的應用程式圖示和選擇性可設定焦點的項目。 預覽程式顯示 form 完成分層映像的每個圖層：

[![](icons-images-images/layered03.png "視差預覽程式")](icons-images-images/layered03.png#lightbox)

同時預覽分層映像，您可以使用滑鼠來將影像旋轉，以及預覽視差效果。 使用 **+**  （加號） 和 **-**  （減號） 按鈕來新增和移除圖層。

在建立新的分層式映像時，將它 LSR 格式匯出，並包含在您的應用程式套件組合。

如需有關建立和預覽分層映像的詳細資訊，請參閱 Apple[建立視差作品](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/CreatingParallaxArtwork.html#//apple_ref/doc/uid/TP40015241-CH19-SW1)區段[應用程式設計指南 tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)。

<a name="App-Icons" />

## <a name="app-icons"></a>應用程式圖示

Xamarin.tvOS 應用程式將需要不只一個應用程式圖示，首頁 Apple TV 螢幕，但也在應用程式存放區的圖示。 應用程式圖示，則您的第一個變更，使上潛在使用者的絕佳的印象就和應該要能傳達一眼您的應用程式用途。

[![](icons-images-images/icon01.png "應用程式圖示")](icons-images-images/icon01.png#lightbox)

每個應用程式必須提供一個小型和大型應用程式圖示的版本。 安裝應用程式時，將首頁 Apple TV 螢幕上使用小圖示。 大型版本會使用應用程式市集。 大型應用程式圖示應該模擬小型圖示版本的外觀與風格。

|小圖示||大型圖示||
|---|---|---|---|
|實際大小|400x240px|大小|1280x768px|
|安全區域的大小|370x222px|||
|未取得焦點的大小|300x180px|||
|已取得焦點的大小|370x222px|||

> [!IMPORTANT]
> 必須提供您的應用程式圖示，做為**分層映像**。 請參閱[分層映像](#Layered-Images)節以取得詳細資料。




Apple 提供建立應用程式圖示的下列建議：

- **提供單一婧鎏鶗懘**– 設計與單一焦點程式圖示，直接位於中央的圖示。
- **使用簡單的背景**– 簡化圖示背景，讓較高的分層突顯出來。請考慮使用簡單的色彩或難以察覺的漸層。
- **文字數量限制**– 因為應用程式的名稱會出現圖示下方，選取使用者時，您應該只包含文字時務必要圖示的設計。
- **請勿使用螢幕擷取畫面**– 螢幕擷取畫面圖示太複雜，而且不允許使用者看到您一眼的應用程式的用途。
- **保持圖示的方形**– tvOS 會自動套用稍微捨入圖示的邊角的遮罩。 不會包括此捨入自己。
- **仔細您的圖層分隔**– 文字應該是大寫大部分的圖層、 中中間和中性的背景，可讓您上層平台的次要項目。
- **使用漸層和仔細陰影**– 漸層和陰影可以相衝突的視差效果，它們應該謹慎使用。 簡單上到下，淺-深漸層樣式的效果最佳。 Shadows 通常最適合當做尖、 hard-edged 濃淡。
- **變更圖層透明度**– 使用不同的層級的上您的應用程式圖示，以增加 3D 效果較高層級的透明度。 必須不透明背景圖層，或將會導致錯誤。

<a name="Setting-the-App-Icons" />

### <a name="setting-the-app-icons"></a>設定應用程式圖示

若要設定 tvOS 專案所需的應用程式圖示，請執行下列動作：

1. 在**方案總管 中**，連按兩下`Assets.xcassets`開啟進行編輯： 

    [![](icons-images-images/asset01.png "Assets.xcassets fileg")](icons-images-images/asset01.png#lightbox)
2. 在**資產編輯器**，依序展開`App Icon & Top Shelf Image`資產： 

    [![](icons-images-images/asset04.png "展開 靠上架影像資產")](icons-images-images/asset04.png#lightbox)
3. 接下來，依序展開`App Icon - Small`資產： 

    [![](icons-images-images/asset05.png "展開 應用程式圖示為小資產")](icons-images-images/asset05.png#lightbox)
4. 然後展開`Back`資產和中的，按一下 `Contents`項目： 

    [![](icons-images-images/asset06.png "然後展開 上一步資產")](icons-images-images/asset06.png#lightbox)
5. 按一下**1 x Apple TV 項目**選取映像檔。
6. 重複上述步驟`Front`和`Middle`資產。
7. 然後重複相同步驟，以定義`App Icon - Large`資產。
4. 儲存您的變更。

<a name="Top-Shelf-Image" />

## <a name="top-shelf-image"></a>高層貨架映像

如果使用者具有 Xamarin.tvOS 應用程式置於頂端列，在 Apple TV 主畫面上，當使用者選取您的應用程式時將顯示大型的上架映像。 此映像應該反白顯示您的應用程式的功能，或提供其內容的直接連結。

[![](icons-images-images/topshelf01.png "上架影像範例")](icons-images-images/topshelf01.png#lightbox)

可以為單一靜態提供上架映像`.png`或`.lsr`檔案 (請參閱[建立分層映像](#Creating-Layered-Images)) 或它可以動態建立在執行階段為單一資料列的可設定焦點的項目 (請參閱[動態高層貨架內容](#Dynamic-Top-Shelf-Content)下方)。

|高層貨架映像大小|注意|
|---|---|
|1920x720px|靜態的.png 或分層的.lsr 檔案|

Apple 提供下列建議來建立您的上架映像：

- **使用豐富的靜態影像套疊**– 如果您的應用程式不會提供動態內容，它的上架映像將會非可設定焦點。 使用此映像來反白顯示的應用程式或您的品牌功能。
- **應用程式的內容連結**– 上架的動態配置提供快速連結至您的使用者尋找應用程式中最重要的內容。 使用此區域提供快速連結來啟動應用程式，並立即跳至指定的內容。
- **展示最新內容**– 豐富上架內容可以繪製到應用程式的使用者，使他們想要使用多個。 使用這個做為區域展示的最高評等或最新內容。
- **個人化內容**– 使用者及其最常使用或 [首頁] 螢幕的上方資料列中的最愛應用程式。 使用上架顯示其方式是最感興趣的內容。
- **不允許廣告**– 廣告嚴格禁止顯示在上架。 您可能會顯示最新版本可內容，但應該顯示任何定價的資訊。

### <a name="setting-the-top-shelf-image"></a>設定高層貨架影像

若要設定所需 tvOS 專案頂端放到書架映像，請執行下列動作：

1. 在**方案總管 中**，連按兩下`Assets.xcassets`開啟進行編輯： 

    [![](icons-images-images/asset01.png "Assets.xcassets 檔案")](icons-images-images/asset01.png#lightbox)
2. 在**資產編輯器**，依序展開`App Icon & Top Shelf Image`資產： 

    [![](icons-images-images/asset04.png "展開 靠上架影像資產")](icons-images-images/asset04.png#lightbox)
3. 按一下`Top Shelf Image`資產： 

    [![](icons-images-images/asset07.png "上架影像資產")](icons-images-images/asset07.png#lightbox)
5. 按一下**1 x Apple TV 項目**選取映像檔。
6. 儲存您的變更。

<a name="Dynamic-Top-Shelf-Content" />

### <a name="dynamic-top-shelf-content"></a>動態高層貨架內容

而不是顯示靜態的上架映像上, 架可包含的動態資料列[可設定焦點的項目](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection)或一組動態的捲動橫幅。 這兩個這些動態樣式可讓您反白顯示您的應用程式或跳躍到其最常使用的功能所提供的內容。

<a name="Sectioned-Content-Row" />

#### <a name="sectioned-content-row"></a>分段的內容資料列

這個動態的上架的內容類型會顯示單一資料列的向下捲動，選擇性地分成區段可設定焦點的項目。 它通常用來反白顯示新的最愛或最近檢視過的應用程式內容。

內容會顯示成單一、 水平捲動清單的內容出現在目前的一段所選取的內容下的標籤 （目前具有焦點）。 如果使用者選取特定的內容，就會啟動您的應用程式，它們應該採取直接將該內容。

下列內容的大小就會需要：

||海報 (2:3)|正方形 (1:1)|HDTV (16:9)|
|---|---|---|---|
|實際大小|404x608px|608x608px|908x512px|
|安全區域的大小|380x570px|570x570px|852x479px|
|未取得焦點的大小|333x500px|500x500px|782x440px|
|已取得焦點的大小|380x570px|570x570px|852x479px|

Apple 提供的分段內容的資料列的下列建議：

- **完成資料列**– 您也應該提供跨越整個螢幕的寬度，因為內容不夠。
- **混合的影像縮放比例**– 分段內容的資料列設計用來保存映像大小 （從以上所提供的清單） 的混合。 如果您不要不過混合影像大小，請注意，額外的調整將會套用到標準化顯示內容。

<a name="Scrolling-Inset-Banners" />

#### <a name="scrolling-inset-banners"></a>捲動內凹橫幅

（選擇性） 在頂端貨架為自動捲動和迴圈集合幾乎填滿螢幕的橫幅中 Xamarin.tvOS 應用程式會有其內容。 這個樣式通常用於展現豐富、 新的內容，例如新的電視節目。

除了自動捲動，使用者可以控制橫幅，並使用 Siri 遠端任一方向捲動。 進行小，循環的筆勢上使用 Siri 遠端橫幅焦點時，會啟動該橫幅視差效果。

**橫幅影像 （額外寬型）**

|   |   |
|---|---|
|實際大小|1940x624px|
|安全區域的大小|1740x620px|
|未取得焦點的大小|1740x560px|
|已取得焦點的大小|1740x620px|

捲動內凹橫幅可以是提供做為靜態`.png`或分層`.lsr`檔案。

Apple 提供捲動內凹橫幅下列的建議：

- **內容量**-您應該提供至少三 （3） 橫幅感覺自然捲動。 您應該包含不超過 （8） 橫幅或它會讓瀏覽硬終端使用者。
- **內容的文字**-如果您的橫幅要求中的文字應該包含在將橫幅影像。 如果您使用分層的映像，您的文字應該是最上層的圖層。

請參閱 Apple [TVServices Framework 參考](https://developer.apple.com/library/prerelease/tvos/documentation/TVServices/Reference/TVServices_Ref/index.html#//apple_ref/doc/uid/TP40016412)如需將頂端放到書架擴充功能加入至您的應用程式提供上架的動態內容的詳細資訊。

<a name="Game-Center-Images" />

## <a name="game-center-images"></a>遊戲中心映像

如果 Xamarin.tvOS 應用程式的遊戲則已包含 Game Center 支援，就會需要數個多個影像資產：

- **成就圖示**-不透明影像做是為了每個系統將會自動裁剪成的圓形的成就。 成就是非可設定焦點的項目。
- **儀表板圖檔**-選用的映像可提供將會出現在遊戲中心中的應用程式的儀表板的頂端。 這些映像為非可設定焦點。
- **Leaderboard 作品**-您必須提供介於一 （1） 到 （3） 16:9 外觀比例映像應用程式支援的每個 leaderboard。 這些可能是靜態`.png`或分層`.lsr`檔案。 Leaderboard 圖檔是可設定焦點。

||成就圖示|儀表板圖檔|Leaderboard 圖檔|
|---|---|---|---|
|可見的大小|200x200px|923x150px|N/A|
|實際大小|320x320px|N/A|659x371px|
|安全區域的大小|N/A|N/A|618x348px|
|未取得焦點的大小|N/A|N/A|548x309px|
|已取得焦點的大小|N/A|N/A|618x348px|

如需有關使用 Game Center 的詳細資訊，請參閱 Apple[遊戲中心程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/NetworkingInternet/Conceptual/GameKit_Guide/Introduction/Introduction.html)。

<a name="Working-with-Images" />

## <a name="working-with-images"></a>使用影像

因為 tvOS 9 的 iOS 9，用來包含及 Xamarin.iOS 應用程式中顯示映像的相同技巧子集也可用於 Xamarin.tvOS 應用程式。 請參閱我們[顯示影像](~/ios/app-fundamentals/images-icons/displaying-an-image.md)文件的詳細資訊。

<a name="Setting-Xamarin.tvOS-Project-Images" />

## <a name="setting-xamarintvos-project-images"></a>設定 Xamarin.tvOS 專案映像

如前所述，所有 tvOS 應用程式都需要[啟動映像](#Launch-Image)，和[應用程式圖示](#App-Icons)。 本章節涵蓋後，選取 啟動映像和應用程式圖示 Xamarin.tvOS 應用程式專案已設定資產類別中。

請執行下列動作：

1. 在**方案總管 中**，連按兩下`Info.plist`開啟進行編輯： 

    [![](icons-images-images/info01.png "Info.plist 檔案")](icons-images-images/info01.png#lightbox)
2. 在**Info.Plist 編輯器**，選取 Assets Catalog (中設定上面[設定應用程式圖示](#Setting-the-App-Icons)區段) 的**應用程式圖示**: 

    [![](icons-images-images/info02.png "Info.Plist 編輯器")](icons-images-images/info02.png#lightbox)
3. 接下來，選取 Assets Catalog (中設定上面[設定啟動映像](#Setting-the-Launch-Image)區段) 的**啟動映像**。
4. 儲存您的變更。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已涵蓋的所有影像類型和 Xamarin.tvOS 應用程式中使用的大小。 首先，它涵蓋啟動映像、 分層式映像、 應用程式圖示，Top 放到書架影像和遊戲中心映像。 然後，它涵蓋 Xamarin.tvOS 應用程式中的映像使用。

## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
