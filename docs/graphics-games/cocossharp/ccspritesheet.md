---
title: 改善 CCSpriteSheet 的畫面播放速率
description: CCSpriteSheet 提供功能結合，並使用一種材質中的許多映像檔。 減少紋理計數可以改善遊戲的載入時間和畫面播放速率。
ms.prod: xamarin
ms.assetid: A1334030-750C-4C60-8B84-1A8A54B0D00E
author: conceptdev
ms.author: crdun
ms.date: 03/24/2017
ms.openlocfilehash: eab6153653a8c8df2068aaaf879d84d35473c541
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61245518"
---
# <a name="improving-frame-rate-with-ccspritesheet"></a>改善 CCSpriteSheet 的畫面播放速率

_CCSpriteSheet 提供功能結合，並使用一種材質中的許多映像檔。減少紋理計數可以改善遊戲的載入時間和畫面播放速率。_

多數遊戲需要最佳化工作，以順利執行，並在行動裝置的硬體上快速載入。 `CCSpriteSheet`類別可以協助解決許多 CocosSharp 的遊戲所遇到的常見效能問題。 本指南涵蓋的常見效能問題，以及如何解決使用`CCSpriteSheet`類別。


## <a name="what-is-a-sprite-sheet"></a>什麼是 sprite 表？

A *sprite 表*，這可以也稱為*紋理圖集*，是會結合成一個檔案的多個映像的映像。 這可以改善執行階段效能，以及內容的載入時間。

例如下, 圖是三個不同的映像所建立的簡單 sprite 工作表。 個別的映像可以是任何規模大小，並產生 sprite 工作表則不需要完全填滿：

![](ccspritesheet-images/image1.png "個別的映像可以是任何規模大小，而且產生的 sprite 表不需要完全填滿")


### <a name="render-states"></a>呈現狀態

CocosSharp 的視覺物件 (例如`CCSprite`) 簡化轉譯程式碼透過傳統圖形化的 API 轉譯程式碼，例如 MonoGame 或 OpenGL，需要建立頂點緩衝區 (中所述[繪圖與 3D 圖形頂點 MonoGame](~/graphics-games/monogame/3d/part2.md)指南)。 儘管其單純性，CocosSharp 不能免除設定的成本*呈現狀態*，這是轉譯程式碼必須切換紋理或轉譯相關的其他狀態的次數。

CocosSharp 的內部程式碼呈現每個視覺項目以循序方式，藉由周遊視覺化樹狀結構開頭與目前`CCScene`。 例如，請考慮下列場景：

![](ccspritesheet-images/image2.png "請考慮此場景")

CocosSharp 會呈現四顆星，序列中：

![](ccspritesheet-images/image3.png "CocosSharp 會依序呈現四顆星")

由於每個`CCSprite`會使用相同的材質，CocosSharp 可以群組所有的四顆星在一起。 此程式碼需要每個畫面的只有一個呈現狀態指派 （星狀材質的指派）。 此案例中是非常有效率。

當然，很少的遊戲使用只有一個影像。 下列的場景導入了全球圖形：

![](ccspritesheet-images/image4.png "此場景導入了全球圖形")

在理想情況下 CocosSharp 應該繪製所有 sprite 使用一個映像第一次 （例如顆星），則使用的映像 （全球） 精靈的其餘部分：

![](ccspritesheet-images/image5.png "在理想情況下 CocosSharp 應繪製所有第一次使用一個映像，例如顆星，然後使用映像全球 sprite 的其餘部分的 sprite")

以上所述的順序需要兩個呈現狀態： 一個在第一顆星，地球上的第一個。

如果要將所有`CCSprite`執行個體是相同的子系`CCNode`，然後 CocosSharp 會最佳化來減少呈現狀態變更的繪製順序。 另一方面，如果`CCSprite`執行個體，因此無法最佳化呈現 CocosSharp 組織 (例如，如果它們屬於不同的實體`CCNode`執行個體)，則可能無法達到最佳的順序。 下列範例顯示五種呈現狀態所導致的最差可能的繪製順序：

![](ccspritesheet-images/image6.png "這會顯示最差的可能的繪製順序，導致五種呈現狀態")

呈現狀態很難進行最佳化的繪圖順序必須遵守的視覺化樹狀結構因為`CCNode`執行個體。 此樹狀結構通常很容易使用 （例如實體包含其視覺子系），結構化或組織因為所需的視覺化版面配置，如演出者所定義。

當然，理想的情況下是具有單一轉譯狀態，而不論具有多個映像。 CocosSharp 的遊戲可以完成這項作業將所有映像結合成單一檔案，然後載入該檔案 (以及伴隨 **.plist**檔案) 至`CCSpriteSheet`。 使用`CCSpriteSheet`類別變得更為重要的遊戲，其中有大量映像，或具有非常複雜的版面配置。 

### <a name="load-times"></a>載入時間

將多個映像結合成一個檔案時，也會改善多種原因所造成的遊戲的載入時間：

 - 將多個映像結合成單一檔案可減少透過特質的像素為單位的總數
 - 正在載入較少的檔案表示小於每個檔案的額外負荷，例如剖析.png 標頭
 - 載入較少的檔案需要較少搜尋時間，也就是很重要的 Dvd 等傳統電腦硬碟的磁碟為基礎的媒體

## <a name="using-ccspritesheet-in-code"></a>程式碼中使用 CCSpriteSheet

若要建立`CCSpriteSheet`執行個體，此程式碼必須提供的映像和檔案會定義要用於每個畫面格的影像區域。 可以做為載入影像 **.png**或是 **.xnb**檔案 (如果使用[Content Pipeline](~/graphics-games/cocossharp/content-pipeline/index.md))。 檔案定義畫面格數 **.plist**可以用手動方式建立的檔案或*TexturePacker* （將於稍後討論如下）。

範例應用程式，其中[可以在這裡下載](https://developer.xamarin.com/samples/mobile/SpriteSheetDemo/)，會建立`CCSpriteSheet`從 **.png**並 **.plist**檔案中，使用下列程式碼：

```csharp
CCSpriteSheet sheet = new CCSpriteSheet ("sheet.plist", "sheet.png"); 
```

一次載入，`CCSpriteSheet`包含`List`的`CCSpriteFrame`執行個體 – 對應至其中一個用來建立整個工作表的來源映像的每個執行個體。 若是**SpriteSheetDemo**專案，`CCSpriteSheet`包含三個映像。 **.Plist**可以檢查檔案，在 Visual Studio for Mac，或以任何文字編輯器，若要查看可用的映像。 如果我們檢視 **.plist**我們可以看到三個畫面格 （區段省略，則強調索引鍵名稱） 的文字編輯器中的檔案：


```csharp
...
<dict>
    <key>frames</key>
    <dict>
        <key>farBackground.png</key>
        ...
        <key>foreground.png</key>
        ...
<key>forestBackground.png</key>
...
```

我們可以使用`Find`方法來依名稱尋找畫面格時，也將，如下所示 (程式碼省略，則強調`CCSpriteFrame`使用量):


```csharp
CCSpriteFrame frame;
...
frame = sheet.Frames.Find(item=>item.TextureFilename == "farBackground.png"); 
CCSprite sprite = new CCSprite (frame); 
...
```

由於`CCSprite`建構函式可以採用`CCSpriteFrame`參數，程式碼永遠不會有要調查的詳細資料`CCSpriteFrame`，例如哪些材質中，它會使用，或在主要的 sprite 工作表中影像的區域。


## <a name="creating-a-sprite-sheet-plist"></a>建立 sprite 表.plist

.Plist 檔案是以 xml 為基礎的檔案，來建立和編輯以手動方式。 同樣地，編輯程式的映像可用來將多個檔案合併成一個較大的檔案。 建立和維護 sprite 表仍相當耗時，因為我們將探討可以匯出 CocosSharp 格式的檔案之 TexturePacker 程式。 TexturePacker 提供免費和 「 專業 」 版本，因此適用於 Windows 和 Mac OS。 使用免費版本，可遵循本指南的其餘部分。 

可以是 TexturePacker[從 TexturePacker 網站下載](https://www.codeandweb.com/texturepacker)。 開啟時，TexturePacker 並沒有載入的專案。 [開始] 畫面可讓您新增 sprite，開啟最近使用的專案 （如果尚未建立其他專案），並選取要使用 sprite 工作表的格式。 CocosSharp 使用 Cocos2D 資料格式：

![](ccspritesheet-images/image7.png "CocosSharp 使用 Cocos2D 資料格式")

影像檔 (例如 **.png**) 可以新增至 TexturePacker 拖它們從 Windows 檔案總管在 Windows 或 mac 上的搜尋工具 TexturePacker 中新增檔案時，會自動更新 sprite 表預覽：

![](ccspritesheet-images/image8.png "TexturePacker 會自動更新 sprite 表預覽版中新增檔案時")

若要匯出的 sprite 工作表，請按一下**發佈 sprite 表**按鈕，然後選取 sprite 工作表的位置。 TexturePacker 節省.plist 檔案和影像檔。

若要使用產生的檔案，加入.png 和.plist CocosSharp 專案。 如需將檔案新增至 CocosSharp 專案的詳細資訊，請參閱[BouncingGame 指南](~/graphics-games/cocossharp/bouncing-game.md)。 將檔案新增之後，他們可以載入`CCSpriteSheet`是稍早在上述程式碼中所示：

```csharp
CCSpriteSheet sheet = new CCSpriteSheet ("sheet.plist", "sheet.png"); 
```

### <a name="considerations-for-maintaining-a-texturepacker-sprite-sheet"></a>維護 TexturePacker sprite 工作表的考量

開發遊戲，如演出者可能會新增、 移除或修改封面。 任何變更都需要更新的 sprite 工作表。 下列考量可簡化 sprite 表維護：

 - 在資料夾在專案中，保留原始的檔案 （用來建立 sprite 試算表檔案），並確定它們加入至版本控制。 重新建立 sprite 工作表，每次變更時，會需要這些檔案。
 - 請勿將原始的檔案新增至 Visual Studio 中，適用於 Mac/Visual Studio 中，或新增，如果設定**建置動作**要**無**。 如果檔案已加入，且具有特定平台**建置動作**，則將會不必要地增加產生的應用程式檔案的大小。
 - 請考慮使用*智慧型資料夾*TexturePacker 中。 智慧型資料夾自動加入原件工作表包含的任何映像。 這項功能可以儲存大量開發的遊戲時的時間，且含有大量映像。 

    ![](ccspritesheet-images/image9.png "這項功能可以儲存大量開發的遊戲時的時間，且含有大量映像")
 - 敬請期待 sprite 紋理大小。 某些較舊的電話硬體不支援大於 2048x2048 的紋理大小。 此外，2048x2048 的 32 位元映像會使用幾乎 17 mb 的 RAM – 大量的記憶體。
 - TexturePacker 不包含資料夾 sprite 名稱在預設情況下，名稱衝突可能會讓。 最好的方式是決定是否要包含資料夾名稱，或不在開發的開頭。 較大的遊戲應該考慮使用資料夾名稱，以防止發生衝突。 若要包含資料夾路徑，請按一下**advanced<** 中**資料**區段，並檢查**前面加上的資料夾名稱**。 

    ![](ccspritesheet-images/image10.png "若要包含資料夾路徑，按一下 [顯示資料] 區段中的進階並檢查前面加上資料夾名稱")

## <a name="summary"></a>總結

本指南涵蓋如何建立和使用`CCSpriteSheet`類別。 其中也涵蓋如何建構檔案，可以將其載入至`CCSpriteSheet`TexturePacker 程式執行個體。

## <a name="related-links"></a>相關連結

- [CCSpriteSheet](https://developer.xamarin.com/api/type/CocosSharp.CCSpriteSheet/)
- [完整的示範 （範例）](https://developer.xamarin.com/samples/mobile/SpriteSheetDemo/)
