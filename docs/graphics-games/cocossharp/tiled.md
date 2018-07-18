---
title: 使用與 CocosSharp 並排顯示
description: 並排顯示是一個功能強大、 有彈性，並建立垂直及等磚的完整應用程式將對應的遊戲。 CocosSharp 提供內建整合磚塊的原生檔案格式。
ms.prod: xamarin
ms.assetid: 804C042C-F62A-4E6C-B10F-06528637F0E2
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: 8a7782097324829b8150b968c5658a864d1fab4a
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
ms.locfileid: "33921296"
---
# <a name="using-tiled-with-cocossharp"></a>使用與 CocosSharp 並排顯示

_並排顯示是一個功能強大、 有彈性，並建立垂直及等磚的完整應用程式將對應的遊戲。CocosSharp 提供內建整合磚塊的原生檔案格式。_

*磚塊*應用程式是一種標準建立*磚對應*用於開發遊戲。 本指南將逐步引導如何將現有的.tmx 檔案 （磚塊所建立的檔案），並將它用於 CocosSharp 遊戲。 特別是此指南將說明如何：

 - 並排顯示地圖的用途
 - 使用.tmx 檔案
 - 轉譯的像素封面的考量
 - 在執行階段使用圖格屬性

當完成我們將會在下列示範：

![](tiled-images/image1.png "依照本指南中的步驟建立此示範應用程式")


## <a name="the-purpose-of-tile-maps"></a>並排顯示地圖的用途

磚對應已存在於開發遊戲數十年，但仍常用於 2D 遊戲的效率及 esthetics。 圖格對應就能夠達到的效率透過其磚設定 – 磚對應所使用之來源影像的高度。 圖格集是一個影像合併成一個檔案的集合。 雖然磚對應中使用的影像參考磚的集合，包含多個較小的影像檔也會呼叫精靈的工作表，或對應遊戲開發中的精靈。 磚設定如何使用我們將使用我們的示範中的磚集加上格線，我們可以將視覺化：

![](tiled-images/image2.png "視覺化的檢視的磚設定如何使用圖格集合將用於此示範中加上格線")

圖格對應排列磚設定的個別磚。 我們應該注意每個圖格對應不需要儲存其自己的圖格副本設定 – 相反地，多個圖格對應可以參考相同的圖格集合。 這表示除了磚的集合，圖格圖需要極少的記憶體。 這可讓大量的並排顯示對應的建立，即使它們用來建立大型的遊戲區域中，例如[捲動 platformer](http://en.wikipedia.org/wiki/Platform_game)環境。 下圖顯示使用相同的圖格集的排列方式其中一個：

![](tiled-images/image3.png "下圖顯示使用相同的牌組可能的排列方式")

![](tiled-images/image4.png "下圖顯示使用相同的牌組可能的排列方式")


## <a name="working-with-tmx-files"></a>使用.tmx 檔案

.Tmx 檔案格式是由磚塊應用程式，可建立的 XML 檔案[磚塊網站上免費下載](http://www.mapeditor.org/)。 .Tmx 檔案格式儲存為並排顯示對應的資訊。 通常遊戲會有一個.tmx 檔案的每個層級或不同區域。

此指南著重於如何使用現有.tmx CocosSharp; 中的檔案不過，其他教學課程可以在線上找到，包括[本簡介磚塊對應編輯器](http://gamedevelopment.tutsplus.com/tutorials/introduction-to-tiled-map-editor--gamedev-2838)。

您要解壓縮[內容的 zip 檔案](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Tiled.zip?raw=true)在我們的遊戲中使用它。 請注意第一件事是，磚對應使用這兩個.tmx 檔案 (dungeon.tmx) 以及一或多個映像檔案會定義磚設定資料 (dungeon_1.png)。 遊戲的必須包含兩個檔案載入.tmx 在執行階段，因此新增這兩個專案的**內容**資料夾 (其包含在**資產**Android 專案中的資料夾)。 具體而言，將檔案加入資料夾，稱為**tilemaps**內**內容**資料夾：

![](tiled-images/image5.png "將檔案新增至內容的資料夾內名為 tilemaps 的資料夾")

**Dungeon.tmx**檔案現在可以載入到執行階段`CCTileMap`物件。 接下來，修改`GameLayer`（或對等的根容器物件） 包含`CCTileMap`執行個體，並將它加入做為子系：


```csharp
public class GameLayer : CCLayer
{
    CCTileMap tileMap;

    public GameLayer ()
    {
    }

    protected override void AddedToScene ()
    {
        base.AddedToScene ();

        tileMap = new CCTileMap ("tilemaps/dungeon.tmx");
        this.AddChild (tileMap);
    }
} 
```

當我們執行的遊戲，我們會看到磚地圖會出現在畫面的左下角：

![](tiled-images/image6.png "如果執行遊戲時，磚地圖會出現在畫面的左下角")


## <a name="considerations-for-rendering-pixel-art"></a>轉譯的像素封面的考量

像素封面、 視訊遊戲開發的內容中是指這通常是由游標，而且通常是低解析度的 2D 視覺美工圖案。 像素圖案可以是 restrictively 來建立，因此像素封面磚集通常包括低解析度的圖格，例如 16 或 32 像素寬度和高度需要大量時間。 如果不在執行階段進行縮放，像素封面通常會太小，大多數最新型的手機和平板電腦。

我們可以調整顯示的維度，在我們的遊戲 GameAppDelegate.cs 檔案中，我們將在其中加入呼叫`CCScene.SetDefaultDesignResolution`:


```csharp
 public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    application.PreferMultiSampling = false;
    application.ContentRootDirectory = "Content";
    application.ContentSearchPaths.Add ("animations");
    application.ContentSearchPaths.Add ("fonts");
    application.ContentSearchPaths.Add ("sounds");

    CCSize windowSize = mainWindow.WindowSizeInPixels;

    float desiredWidth = 1024.0f;
    float desiredHeight = 768.0f;
    
    // This will set the world bounds to be (0,0, w, h)
    // CCSceneResolutionPolicy.ShowAll will ensure that the aspect ratio is preserved
    CCScene.SetDefaultDesignResolution (desiredWidth, desiredHeight, CCSceneResolutionPolicy.ShowAll);
    
    // Determine whether to use the high or low def versions of our images
    // Make sure the default texel to content size ratio is set correctly
    // Of course you're free to have a finer set of image resolutions e.g (ld, hd, super-hd)
    if (desiredWidth < windowSize.Width)
    {
        application.ContentSearchPaths.Add ("images/hd");
        CCSprite.DefaultTexelToContentSizeRatio = 2.0f;
    }
    else
    {
        application.ContentSearchPaths.Add ("images/ld");
        CCSprite.DefaultTexelToContentSizeRatio = 1.0f;
    }

    // New code:
    CCScene.SetDefaultDesignResolution (380, 240, CCSceneResolutionPolicy.ShowAll);

    CCScene scene = new CCScene (mainWindow);
    GameLayer gameLayer = new GameLayer ();

    scene.AddChild (gameLayer);

    mainWindow.RunWithScene (scene);
} 
```

如需有關`CCSceneResolutionPolicy`，請參閱我們的指南[處理解決方式 CocosSharp](~/graphics-games/cocossharp/resolutions.md)。

如果我們現在執行遊戲時，我們會看到我們裝置全螢幕遊戲需要：

![](tiled-images/image7.png "遊戲的佔滿全螢幕的裝置")

最後我們會想要停用在我們的並排顯示地圖的消除鋸齒。 `Antialiased`轉譯會放大物件時，屬性會套用 blurring 效果。 反鋸齒功能可以減少的圖形物件，不太美觀外觀，但可能也會導入它自己的轉譯成品。 具體來說，消除鋸齒模糊了每個磚的內容。 不過，每個磚的邊緣的不模糊，這可讓個別的圖格，突顯出來，而不是相鄰的並排入混用。 我們也應注意像素封面遊戲通常保留不太美觀外觀維護*回溯*感覺。

設定`Antialiased`至`false`之後建構`tileMap`:


```csharp
protected override void AddedToScene ()
{
    base.AddedToScene ();

    tileMap = new CCTileMap ("tilemaps/dungeon.tmx");

    // new code:
    tileMap.Antialiased = false;

    this.AddChild (tileMap);
} 
```

現在我們磚對應不會模糊不清的出現：

![](tiled-images/image8.png "現在磚對應不會模糊不清")


## <a name="using-tile-properties-at-runtime"></a>在執行階段使用圖格屬性

到目前為止我們有`CCTileMap`.tmx 檔案載入及顯示，但沒有方法可以與其互動。 具體來說，某些磚 （例如我們珍貴箱子中） 需要有自訂邏輯。 我們將逐步執行如何偵測自訂圖格屬性，與以回應一次在執行階段中識別這些屬性的各種方式。

我們會撰寫任何程式碼之前，我們需要將內容新增到透過磚塊我們並排顯示地圖。 若要這樣做，請開啟 dungeon.tmx 檔案磚塊程式中。 請務必開啟 [遊戲] 專案中是正在使用的檔案。

一旦開啟，請選取珍貴箱子中的磚，檢視其內容設定：

![](tiled-images/image9.png "一旦開啟，請選取珍貴箱子磚，檢視其內容設定中")

如果看不到珍貴箱子屬性，珍貴箱子上按一下滑鼠右鍵，然後選取**圖格屬性**:

![](tiled-images/image10.png "如果看不到珍貴箱子屬性，珍貴箱子上按一下滑鼠右鍵，然後選取 圖格屬性")

並排顯示的屬性會實作具有名稱和值。 若要加入的屬性，請按一下**+** 按鈕，輸入名稱**IsTreasure**，按一下**確定**，然後輸入值**true**: 

![](tiled-images/image11.png "若要加入屬性，按一下按鈕、 輸入名稱 IsTreasure，按一下 [確定]，然後輸入值為 true")

別忘了儲存.tmx 檔案之後修改內容。

最後，我們會將新增程式碼，以尋找我們新加入的屬性。 我們將會迴圈處理每個`CCTileMapLayer`（我們對應具有 2 層），然後透過每個資料列和資料行來尋找具有任何磚`IsTreasure`屬性：


```csharp
public class GameLayer : CCLayer
{
    CCTileMap tileMap;

    public GameLayer ()
    {
    }

    protected override void AddedToScene ()
    {
        base.AddedToScene ();

        tileMap = new CCTileMap ("tilemaps/dungeon.tmx");

        // new code:
        tileMap.Antialiased = false;

        this.AddChild (tileMap);

        HandleCustomTileProperties (tileMap);
    }

    void HandleCustomTileProperties(CCTileMap tileMap)
    {
        // Width and Height are equal so we can use either
        int tileDimension = (int)tileMap.TileTexelSize.Width;

        // Find out how many rows and columns are in our tile map
        int numberOfColumns = (int)tileMap.MapDimensions.Size.Width;
        int numberOfRows = (int)tileMap.MapDimensions.Size.Height;

        // Tile maps can have multiple layers, so let's loop through all of them:
        foreach (CCTileMapLayer layer in tileMap.TileLayersContainer.Children)
        {
            // Loop through the columns and rows to find all tiles
            for (int column = 0; column < numberOfColumns; column++)
            {
                // We're going to add tileDimension / 2 to get the position
                // of the center of the tile - this will help us in 
                // positioning entities, and will eliminate the possibility
                // of floating point error when calculating the nearest tile:
                int worldX = tileDimension * column + tileDimension / 2;
                for (int row = 0; row < numberOfRows; row++)
                {
                    // See above on why we add tileDimension / 2
                    int worldY = tileDimension * row + tileDimension / 2;

                    HandleCustomTilePropertyAt (worldX, worldY, layer);
                }
            }
        }
    }

    void HandleCustomTilePropertyAt(int worldX, int worldY, CCTileMapLayer layer)
    {
        CCTileMapCoordinates tileAtXy = layer.ClosestTileCoordAtNodePosition (new CCPoint (worldX, worldY));

        CCTileGidAndFlags info = layer.TileGIDAndFlags (tileAtXy.Column, tileAtXy.Row);

        if (info != null)
        {
            Dictionary<string, string> properties = null;

            try
            {
                properties = tileMap.TilePropertiesForGID (info.Gid);
            }
            catch
            {
                // CocosSharp 
            }

            if (properties != null && properties.ContainsKey ("IsTreasure") && properties["IsTreasure"] == "true" )
            {
                layer.RemoveTile (tileAtXy);

                // todo: Create a treasure chest entity
            }
        }
    }
} 
```

大部分的程式碼是一目了然，但我們應該共同討論珍貴磚的處理。 在此情況下，我們會移除任何識別為珍貴箱子的磚。 這是因為珍貴箱子可能需要自訂程式碼在執行階段影響衝突，並把播放程式開啟時珍貴的內容。 此外，珍貴可能需要回應正在開啟 （變更其視覺外觀），而且可能會針對所有螢幕上出現時僅敵人已有邏輯。

換句話說，珍貴箱子將受益於正在實體，而不是正在以簡單的並排顯示`CCTileMap`。 如需有關遊戲的實體的詳細資訊，請參閱[CocosSharp 中的實體引導](~/graphics-games/cocossharp/entities.md)。


## <a name="summary"></a>總結

這個逐步解說將說明如何載入到 CocosSharp 應用程式建立磚塊.tmx 檔案。 它會顯示如何修改應用程式解析，以說明較低解析度像素封面、 以及如何尋找其內容以執行自訂邏輯，例如建立實體執行個體磚。

## <a name="related-links"></a>相關的連結

- [並排顯示的網站](http://www.mapeditor.org/)
- [內容壓縮](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Tiled.zip?raw=true)
