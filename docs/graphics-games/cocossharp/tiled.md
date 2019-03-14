---
title: 搭配 cocossharp 使用 Tiled
description: 並排顯示是一個功能強大、 有彈性，並建立且互不影響等的圖格的完整應用程式對應的遊戲。 CocosSharp 提供內建整合圖案並排的原生檔案格式。
ms.prod: xamarin
ms.assetid: 804C042C-F62A-4E6C-B10F-06528637F0E2
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 8e7ef890af264bb08827d86c635d555184f1ec00
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57672504"
---
# <a name="using-tiled-with-cocossharp"></a>搭配 cocossharp 使用 Tiled

_並排顯示是一個功能強大、 有彈性，並建立且互不影響等的圖格的完整應用程式對應的遊戲。CocosSharp 提供內建整合圖案並排的原生檔案格式。_

*磚塊*應用程式是一種標準來建立*圖格對應*遊戲開發人員使用。 本指南會逐步解說如何將現有的.tmx 檔案 （磚塊所建立的檔案），並使用 CocosSharp 的遊戲中。 特別是本指南將涵蓋：

 - 並排顯示對應的目的
 - 使用.tmx 檔案
 - 轉譯的像素封面的考量
 - 在執行階段使用圖格屬性

當完成我們將會有下列示範：

![](tiled-images/image1.png "依照本指南中的步驟建立的示範應用程式")


## <a name="the-purpose-of-tile-maps"></a>並排顯示對應的目的

圖格對應已存在於數十年，遊戲開發，但仍中常用的 2D 遊戲的效率及 esthetics。 圖格對應就能夠達到非常高的效率，透過使用磚集 – 使用並排顯示對應的來源映像。 牌組是結合成一個檔案的映像的集合。 雖然 圖格集，請參閱圖格對應中使用的映像，包含多個較小的映像的檔案也稱為 sprite 試算表，或對應中的遊戲開發的 sprite。 我們可以視覺化方式檢視圖格設定如何使用我們將使用我們的示範中的磚集合加上格線：

![](tiled-images/image2.png "視覺化的檢視的圖格設定如何使用將用於此示範中的磚集合加上格線")

圖格對應排列磚集從個別的圖格。 我們應該注意每個圖格對應不需要儲存它自己的複本，此圖格的設定-相反地，多個圖格對應可以參考相同的牌組。 這表示除了牌組中，圖格圖需要極少的記憶體。 這可讓您建立大量的圖格的對應，即使它們用來建立大型的遊戲 區域中，例如[捲動平台遊戲](https://en.wikipedia.org/wiki/Platform_game)環境。 下列範例顯示可能的排列方式 圖格使用相同：

![](tiled-images/image3.png "下圖顯示使用相同的牌組可能的排列方式")

![](tiled-images/image4.png "下圖顯示使用相同的牌組可能的排列方式")


## <a name="working-with-tmx-files"></a>使用.tmx 檔案

.Tmx 檔案格式是磚塊應用程式，可以建立一個 XML 檔案[磚塊網站上免費下載](http://www.mapeditor.org/)。 .Tmx 檔案格式儲存圖格對應的資訊。 通常遊戲會有一個.tmx 檔案，為每個層級或個別的區域。

本指南著重於如何使用 CocosSharp; 中的現有.tmx 檔案不過，其他教學課程可以在線上找到，包括[磚塊圖編輯器簡介](http://gamedevelopment.tutsplus.com/tutorials/introduction-to-tiled-map-editor--gamedev-2838)。

您必須解壓縮[內容的 zip 檔案](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Tiled.zip?raw=true)在我們的遊戲中使用它。 請注意第一件事是，圖格對應使用這兩個.tmx 檔案 (dungeon.tmx) 以及一個或多個映像檔案會定義 [] 圖格設定資料 (dungeon_1.png)。 遊戲必須包含兩個載入.tmx 在執行階段，因此新增至專案的這兩個**內容**資料夾 (其中包含在**資產**Android 專案中的資料夾)。 具體而言，將檔案新增到資料夾，稱為**tilemaps**內**內容**資料夾：

![](tiled-images/image5.png "將檔案新增至名為 tilemaps [Content] 資料夾內的資料夾")

**Dungeon.tmx**檔案現在可以在執行階段到載入`CCTileMap`物件。 接下來，修改`GameLayer`（或對等的根容器物件） 包含`CCTileMap`執行個體並將它新增為子系：


```csharp
public class GameLayer : CCLayer
{
    CCTileMap tileMap;

    public GameLayer ()
    {
    }

    protected override void AddedToScene ()
    {
        base.AddedToScene ();

        tileMap = new CCTileMap ("tilemaps/dungeon.tmx");
        this.AddChild (tileMap);
    }
} 
```

如果我們執行的遊戲，我們會看到圖格對應會出現在畫面的左下角中：

![](tiled-images/image6.png "如果執行遊戲時，圖格對應會出現在畫面的左下角")


## <a name="considerations-for-rendering-pixel-art"></a>轉譯的像素封面的考量

像素的封面、 電視遊戲機開發內容中是指 2D 視覺藝術，通常會建立由游標，而且通常是低解析度。 像素圖案可以是 restrictively 若要建立，因此通常包含像素封面 圖格設定的低解析度的圖格，例如 16 或 32 像素寬度和高度需要大量的時間。 如果不在執行階段調整，像素封面通常是最新的手機和平板電腦而言太小。

我們可以調整顯示的維度，在我們的遊戲 GameAppDelegate.cs 檔案中，我們將在其中新增呼叫`CCScene.SetDefaultDesignResolution`:


```csharp
 public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    application.PreferMultiSampling = false;
    application.ContentRootDirectory = "Content";
    application.ContentSearchPaths.Add ("animations");
    application.ContentSearchPaths.Add ("fonts");
    application.ContentSearchPaths.Add ("sounds");

    CCSize windowSize = mainWindow.WindowSizeInPixels;

    float desiredWidth = 1024.0f;
    float desiredHeight = 768.0f;
    
    // This will set the world bounds to be (0,0, w, h)
    // CCSceneResolutionPolicy.ShowAll will ensure that the aspect ratio is preserved
    CCScene.SetDefaultDesignResolution (desiredWidth, desiredHeight, CCSceneResolutionPolicy.ShowAll);
    
    // Determine whether to use the high or low def versions of our images
    // Make sure the default texel to content size ratio is set correctly
    // Of course you're free to have a finer set of image resolutions e.g (ld, hd, super-hd)
    if (desiredWidth < windowSize.Width)
    {
        application.ContentSearchPaths.Add ("images/hd");
        CCSprite.DefaultTexelToContentSizeRatio = 2.0f;
    }
    else
    {
        application.ContentSearchPaths.Add ("images/ld");
        CCSprite.DefaultTexelToContentSizeRatio = 1.0f;
    }

    // New code:
    CCScene.SetDefaultDesignResolution (380, 240, CCSceneResolutionPolicy.ShowAll);

    CCScene scene = new CCScene (mainWindow);
    GameLayer gameLayer = new GameLayer ();

    scene.AddChild (gameLayer);

    mainWindow.RunWithScene (scene);
} 
```

如需詳細資訊`CCSceneResolutionPolicy`，請參閱我們的指南[處理 CocosSharp 中的解決方式](~/graphics-games/cocossharp/resolutions.md)。

如果我們現在執行遊戲，我們會看到全螢幕我們的裝置的遊戲需要：

![](tiled-images/image7.png "遊戲的佔滿整個螢幕的裝置")

最後，我們要停用在我們的並排顯示地圖的消除鋸齒。 `Antialiased`轉譯會放大物件時，屬性會套用模糊效果。 消除鋸齒功能有助於減少不美觀的外觀圖形物件，但也可能會導入它自己的轉譯成品。 具體來說，消除鋸齒模糊了每個圖格的內容。 不過，每個圖格邊緣都不會模糊，這可讓個別突顯出來，而不是混合使用相鄰的並排顯示的圖格。 我們也應該注意，像素封面遊戲通常會保留其維護的不美觀外觀*復古*感覺。

設定`Antialiased`要`false`之後建構`tileMap`:


```csharp
protected override void AddedToScene ()
{
    base.AddedToScene ();

    tileMap = new CCTileMap ("tilemaps/dungeon.tmx");

    // new code:
    tileMap.Antialiased = false;

    this.AddChild (tileMap);
} 
```

現在我們 圖格的對應不會出現模糊：

![](tiled-images/image8.png "現在的圖格對應不會模糊不清")


## <a name="using-tile-properties-at-runtime"></a>在執行階段使用圖格屬性

到目前為止我們`CCTileMap`.tmx 檔案載入和顯示，但我們有沒有辦法與它互動。 具體來說，某些圖格 （例如我們寶藏箱子中） 需要有自訂邏輯。 我們將逐步執行如何偵測自訂的圖格屬性，以及各種因應一次在執行階段識別這些屬性。

我們撰寫任何程式碼之前，我們必須將屬性加入至我們的圖格對應中透過磚塊。 若要這樣做，請在 圖案並排方案中開啟 dungeon.tmx 檔案。 請務必開啟的檔案正在使用該遊戲的專案中。

一旦開啟，請選取寶藏箱子中的圖格以檢視其屬性設定：

![](tiled-images/image9.png "一旦開啟，請選取寶藏箱子中的圖格以檢視其屬性設定")

如果看不到寶藏箱子屬性，以滑鼠右鍵按一下寶藏箱子，然後選取**圖格屬性**:

![](tiled-images/image10.png "如果看不到寶藏箱子屬性，寶藏箱子上按一下滑鼠右鍵，然後選取圖格屬性")

並排顯示的屬性會實作具有名稱和值。 若要加入的屬性，請按一下 **+** 按鈕，輸入名稱 **IsTreasure** ，按一下  **確定** ，然後輸入值 **true** : 

![](tiled-images/image11.png "若要新增的屬性，按一下按鈕、 輸入名稱 IsTreasure，按一下 [確定]，然後輸入值為 true")

別忘了將.tmx 檔案儲存之後修改屬性。

最後，我們將新增程式碼來尋找我們新加入的屬性。 我們將會迴圈處理每個`CCTileMapLayer`（我們的對應具有 2 個層級），然後透過每個資料列和資料行，以尋找具有任何磚`IsTreasure`屬性：


```csharp
public class GameLayer : CCLayer
{
    CCTileMap tileMap;

    public GameLayer ()
    {
    }

    protected override void AddedToScene ()
    {
        base.AddedToScene ();

        tileMap = new CCTileMap ("tilemaps/dungeon.tmx");

        // new code:
        tileMap.Antialiased = false;

        this.AddChild (tileMap);

        HandleCustomTileProperties (tileMap);
    }

    void HandleCustomTileProperties(CCTileMap tileMap)
    {
        // Width and Height are equal so we can use either
        int tileDimension = (int)tileMap.TileTexelSize.Width;

        // Find out how many rows and columns are in our tile map
        int numberOfColumns = (int)tileMap.MapDimensions.Size.Width;
        int numberOfRows = (int)tileMap.MapDimensions.Size.Height;

        // Tile maps can have multiple layers, so let's loop through all of them:
        foreach (CCTileMapLayer layer in tileMap.TileLayersContainer.Children)
        {
            // Loop through the columns and rows to find all tiles
            for (int column = 0; column < numberOfColumns; column++)
            {
                // We're going to add tileDimension / 2 to get the position
                // of the center of the tile - this will help us in 
                // positioning entities, and will eliminate the possibility
                // of floating point error when calculating the nearest tile:
                int worldX = tileDimension * column + tileDimension / 2;
                for (int row = 0; row < numberOfRows; row++)
                {
                    // See above on why we add tileDimension / 2
                    int worldY = tileDimension * row + tileDimension / 2;

                    HandleCustomTilePropertyAt (worldX, worldY, layer);
                }
            }
        }
    }

    void HandleCustomTilePropertyAt(int worldX, int worldY, CCTileMapLayer layer)
    {
        CCTileMapCoordinates tileAtXy = layer.ClosestTileCoordAtNodePosition (new CCPoint (worldX, worldY));

        CCTileGidAndFlags info = layer.TileGIDAndFlags (tileAtXy.Column, tileAtXy.Row);

        if (info != null)
        {
            Dictionary<string, string> properties = null;

            try
            {
                properties = tileMap.TilePropertiesForGID (info.Gid);
            }
            catch
            {
                // CocosSharp 
            }

            if (properties != null && properties.ContainsKey ("IsTreasure") && properties["IsTreasure"] == "true" )
            {
                layer.RemoveTile (tileAtXy);

                // todo: Create a treasure chest entity
            }
        }
    }
} 
```

大多數的程式碼都簡單易懂，但我們應該討論寶藏圖格的處理。 在此情況下，我們會移除任何已識別為寶藏箱子的磚。 這是因為寶藏箱子很有可能需要自訂程式碼在執行階段影響衝突，並獎勵播放程式寶藏開啟時的內容。 此外，寶藏可能必須回應所開啟 （變更其視覺外觀），且可能有邏輯，如僅所有螢幕上出現時的敵人已經失效。

換句話說，寶藏箱子將受益於正在實體，而不是在中的簡單磚`CCTileMap`。 如需有關遊戲的實體的詳細資訊，請參閱[CocosSharp 中的實體引導](~/graphics-games/cocossharp/entities.md)。


## <a name="summary"></a>總結

此逐步解說將說明如何載入到 CocosSharp 應用程式建立的圖案並排的.tmx 檔案。 它會顯示如何修改應用程式解析，以說明較低解析度像素圖案，以及如何尋找其屬性，以執行自訂邏輯，例如建立實體執行個體的圖格。

## <a name="related-links"></a>相關連結

- [並排顯示的網站](http://www.mapeditor.org/)
- [內容的 zip](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Tiled.zip?raw=true)
