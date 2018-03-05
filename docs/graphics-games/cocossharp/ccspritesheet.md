---
title: "改善 CCSpriteSheet 的畫面播放速率"
description: "CCSpriteSheet 提供合併，並使用一種材質中的多個映像檔案的功能。 減少紋理計數可以改善遊戲的載入次數與畫面播放速率。"
ms.topic: article
ms.prod: xamarin
ms.assetid: A1334030-750C-4C60-8B84-1A8A54B0D00E
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/24/2017
ms.openlocfilehash: ec8a641fbd15f826e92ada62f65b17dd46b369e4
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="improving-framerate-with-ccspritesheet"></a>改善 CCSpriteSheet 的畫面播放速率

_CCSpriteSheet 提供合併，並使用一種材質中的多個映像檔案的功能。減少紋理計數可以改善遊戲的載入次數與畫面播放速率。_

許多遊戲都需要順暢執行，並在行動硬體上的快速載入的最佳化工作。 `CCSpriteSheet`類別可協助解決 CocosSharp 遊戲所遇到的許多常見效能問題。 本指南涵蓋了常見效能問題，以及如何解決使用`CCSpriteSheet`類別。


# <a name="what-is-a-sprite-sheet"></a>在精靈的工作表是什麼？

A*精靈表*，這可以也稱為*紋理 atlas*，是會結合成一個檔案的多個映像的映像。 這可以改善執行階段效能，以及內容的載入時間。

例如下, 圖是三個不同的映像所建立的簡單精靈工作表。 個別映像可以是任何規模，並產生的精靈工作表並不需要完全填滿：

![](ccspritesheet-images/image1.png "個別映像可以是任何規模，以及產生的精靈工作表並不需要完全填滿")


## <a name="render-states"></a>呈現狀態

CocosSharp 的視覺物件 (例如`CCSprite`) 簡化轉譯程式碼透過傳統圖形化的應用程式開發介面轉譯程式碼，例如 MonoGame 或 OpenGL，需要建立頂點緩衝區 (如中所述[繪圖與 3D 圖形MonoGame 的頂點](~/graphics-games/monogame/3d/part2.md)指南)。 其簡化，儘管 CocosSharp 不排除設定成本*呈現狀態*，這是轉譯程式碼必須切換材質或其他呈現相關狀態的次數。

CocosSharp 的內部程式碼呈現每個視覺項目，循序往返，與目前的視覺化樹狀結構開頭`CCScene`。 例如，請考慮下列場景：

![](ccspritesheet-images/image2.png "請考慮此場景")

CocosSharp 會導致四顆星轉譯序列中：

![](ccspritesheet-images/image3.png "CocosSharp 順序呈現四顆星")

由於每個`CCSprite`使用相同紋理，CocosSharp 可以群組所有四顆星在一起。 此程式碼需要每個畫面格的只有一個呈現狀態指派 （指派的星型的紋理）。 這個案例是非常有效率。

當然，很少的遊戲會使用只有一個映像。 下列場景導入了地球圖形：

![](ccspritesheet-images/image4.png "此畫面導入了地球圖形")

在理想情況下 CocosSharp 應繪製所有小使用一個映像，第一次 （例如顆星），然後依照使用的映像 （地球） 的其餘部分：

![](ccspritesheet-images/image5.png "在理想情況下 CocosSharp 應繪製所有小例如顆星，然後依照使用的映像世界各地的其餘部分的第一次使用一個映像")

以上所述的順序需要兩個呈現狀態： 一個在第一個世界各地星號，第一個。

如果要將所有`CCSprite`執行個體都是相同的子系`CCNode`，然後 CocosSharp 會最佳化來減少呈現狀態變更的繪製順序。 另一方面，如果`CCSprite`編排，因此無法最佳化呈現 CocosSharp 執行個體 (例如，如果它們是不同的實體的一部分`CCNode`執行個體)，則可能無法達到最佳順序。 下列範例顯示五個呈現狀態所導致的最差可能的繪製順序：

![](ccspritesheet-images/image6.png "這會顯示五個呈現狀態所導致的最差可能的繪圖順序")

很難進行最佳化，因為繪圖順序必須遵守的視覺化樹狀結構呈現狀態`CCNode`執行個體。 此樹狀結構通常是結構化以容易使用 （例如實體包含及其視覺子系），或演出者所定義，因為所需的視覺化配置而組織。

當然，理想的情況是具有單一轉譯狀態，雖然具有多個映像。 CocosSharp 遊戲可以完成這項作業結合成單一檔案中，所有映像，然後載入該檔案 (連同伴隨**.plist**檔案) 至`CCSpriteSheet`。 使用`CCSpriteSheet`類別變得更為重要的遊戲，其具有大量的映像，或其具有非常複雜的配置。 

## <a name="load-times"></a>載入時間

將多個映像結合成一個檔案時，也會提升多種原因所造成的遊戲的載入時間：

 - 結合成單一檔案的多個映像可以減少整體的特質，透過使用像素
 - 載入較少的檔案表示每個檔案額外負荷較少，例如剖析.png 標頭
 - 載入較少的檔案需要小於搜尋時間，這一點很重要的磁碟為基礎的媒體，例如 Dvd 和傳統電腦硬碟

# <a name="using-ccspritesheet-in-code"></a>程式碼中使用 CCSpriteSheet

若要建立`CCSpriteSheet`執行個體，該程式碼必須提供的映像和檔案會定義要用於每個畫面格的影像區域。 可以載入影像，做為**.png**或**.xnb**檔案 (如果使用[內容的管線](~/graphics-games/cocossharp/content-pipeline/index.md))。 定義框架檔案是**.plist**可以以手動方式建立的檔案或*TexturePacker* （其中討論如下）。

範例應用程式，其中[可以在這裡下載](https://developer.xamarin.com/samples/mobile/SpriteSheetDemo/)，建立`CCSpriteSheet`從**.png**和**.plist**檔案使用下列程式碼：

```csharp
CCSpriteSheet sheet = new CCSpriteSheet ("sheet.plist", "sheet.png"); 
```

一次載入，`CCSpriteSheet`包含`List`的`CCSpriteFrame`執行個體 – 對應至其中一個用來建立整個工作表的來源映像的每個執行個體。 如果是**SpriteSheetDemo**專案，`CCSpriteSheet`包含三個映像。 **.Plist**可以檢查檔案，在 Visual Studio for Mac 或任何文字編輯器中若要查看可用的映像。 如果我們檢視**.plist**我們可以看到三個畫面格 （省略要強調的索引鍵名稱的區段） 的文字編輯器中的檔案：


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

我們可以使用`Find`方法，如下所示的名稱，尋找框架 (省略來強調程式碼`CCSpriteFrame`使用量):


```csharp
CCSpriteFrame frame;
...
frame = sheet.Frames.Find(item=>item.TextureFilename == "farBackground.png"); 
CCSprite sprite = new CCSprite (frame); 
...
```

因為`CCSprite`建構函式可以採用`CCSpriteFrame`參數時，程式碼永遠不會有要調查的詳細資料`CCSpriteFrame`，例如哪些紋理它使用，或主要精靈工作表中的影像區域。


#  <a name="creating-a-sprite-sheet-plist"></a>建立精靈表.plist

.Plist 檔案是以 xml 為基礎的檔案，可建立和編輯。 同樣地，可以使用編輯程式的映像將多個檔案合併成一個大型的檔案。 建立和維護精靈工作表可能會非常耗時，因為我們將探討 TexturePacker 程式可以匯出 CocosSharp 格式的檔案。 TexturePacker 提供一套免費和 「 Pro"的版本，且適用於 Windows 和 Mac OS。 本指南的其餘部分後面可以接著使用免費版本。 

可以是 TexturePacker[從 TexturePacker 網站下載](https://www.codeandweb.com/texturepacker)。 開啟時，TexturePacker 沒有載入專案。 [開始] 畫面可讓您新增小開啟最近使用的專案 （如果尚未建立其他專案），然後選取要使用精靈的工作表的格式。 CocosSharp 使用 Cocos2D 資料格式：

![](ccspritesheet-images/image7.png "CocosSharp 使用 Cocos2D 資料格式")

映像檔案 (例如**.png**) 可以新增至 TexturePacker 拖他們從 Windows 檔案總管上 Windows 或 mac 上的搜尋工具 TexturePacker 加入檔案時，會自動更新精靈的工作表預覽：

![](ccspritesheet-images/image8.png "TexturePacker 會自動更新精靈工作表的預覽，每當加入檔案")

若要匯出精靈的工作表，請按一下**發行精靈的工作表**按鈕，然後選取精靈工作表的位置。 .Plist 檔案和影像檔時，會將儲存 TexturePacker。

若要使用產生的檔案，加入.png 和.plist CocosSharp 專案。 如需將檔案加入至 CocosSharp 專案資訊，請參閱[實作 BouncingGame 指南](~/graphics-games/cocossharp/first-game/part2.md)。 一旦加入檔案之後，可以載入到`CCSpriteSheet`是稍早在上述程式碼中所示：

```csharp
CCSpriteSheet sheet = new CCSpriteSheet ("sheet.plist", "sheet.png"); 
```

## <a name="considerations-for-maintaining-a-texturepacker-sprite-sheet"></a>維護 TexturePacker 精靈工作表的考量

遊戲會開發，如演出者可能會加入、 移除或修改封面。 任何變更都需要更新的精靈工作表。 下列考量可簡化精靈頁維護工作：

 - 在資料夾在專案中，保留原始的檔案 （用來建立精靈的工作表的檔案），並確定它們會加入至版本控制。 每當變更是重新建立精靈的工作表，將會需要這些檔案。
 - 請勿原始檔案加入 Visual Studio for Mac/Visual Studio，或如果加入，將**建置動作**至**無**。 如果檔案會加入，而且具有特定平台**建置動作**，則將會不必要地增加產生的應用程式檔案的大小。
 - 請考慮使用*智慧資料夾*TexturePacker 中。 智慧型資料夾自動加入任何自主映像精靈工作表。 這項功能可以使用大量的映像儲存許多開發遊戲時的時間。 

    ![](ccspritesheet-images/image9.png "這項功能可以節省很多開發遊戲時的時間使用大量的影像")
 - 特別注意落在精靈的紋理大小。 有些較舊的電話硬體不支援大於 2048 x 2048 的紋理大小。 此外，2048 x 2048 的 32 位元映像會使用 17 幾乎 mb 的 RAM – 大量的記憶體。
 - TexturePacker 不包含資料夾名稱精靈中依預設，因此可能會有名稱衝突。 最好決定是否要包含資料夾名稱，或不在開發的開頭。 較大的遊戲應該考慮使用資料夾名稱以避免衝突。 若要包含資料夾路徑，請按一下**w advanced<**中**資料**區段，並檢查**前面加上的資料夾名稱**。 

    ![](ccspritesheet-images/image10.png "若要包含資料夾路徑，請按一下顯示在資料區段的 進階並檢查前面加上的資料夾名稱")

# <a name="summary"></a>總結

本指南涵蓋如何建立及使用`CCSpriteSheet`類別。 其中也涵蓋如何建構可載入的檔案`CCSpriteSheet`TexturePacker 程式執行個體。

## <a name="related-links"></a>相關連結

- [CCSpriteSheet](https://developer.xamarin.com/api/type/CocosSharp.CCSpriteSheet/)
- [完整示範 （範例）](https://developer.xamarin.com/samples/mobile/SpriteSheetDemo/)
