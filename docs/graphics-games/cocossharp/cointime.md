---
title: 參考階段實作詳細資料
description: 本指南討論實作詳細資料，在參考時間遊戲中，包括使用對應圖格、 建立實體、 動畫小，以及實作有效率的衝突。
ms.topic: article
ms.prod: xamarin
ms.assetid: 5D285684-0417-4E16-BD14-2D1F6DEFBB8B
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/24/2017
ms.openlocfilehash: 80250ca9fae98fae653c9b2837b2b1a96fb02203
ms.sourcegitcommit: 7b76c3d761b3ffb49541e2e2bcf292de6587c4e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
---
# <a name="coin-time-implementation-details"></a>參考階段實作詳細資料

_本指南討論實作詳細資料，在參考時間遊戲中，包括使用對應圖格、 建立實體、 動畫小，以及實作有效率的衝突。_

參考時間是完整的 platformer 適用於 iOS 和 Android 遊戲。 遊戲的目標是收集所有層級中錢幣，然後避免敵人和障礙到達結束媒體櫃門。

![](cointime-images/image1.png "遊戲的目標是要收集所有層級中錢幣，然後避免敵人和障礙到達結束媒體櫃門")

本指南將告訴您參考的時間，涵蓋下列主題中的實作詳細資料：

- [使用 TMX 檔案](#Working_with_TMX_Files)
- [載入層級](#Level_Loading)
- [加入新的實體](#Adding_New_Entities)
- [動畫的實體](#Animated_Entities)


# <a name="content-in-coin-time"></a>內容的參考時間

參考時，表示可能會組織完整 CocosSharp 專案的方式範例專案。 錢幣式階段的結構其目的是簡化的新增和維護的內容。 它會使用**.tmx**所建立的檔案[磚塊](http://www.mapeditor.org)層級及定義動畫的 XML 檔案。 修改或加入新的內容可以輕鬆達成。 

雖然這個方法會參考時間學習和試驗的有效專案，它也會反映如何專業遊戲進行。 本指南說明一些簡化加入和修改內容的方法。


# <a name="working-with-tmx-files"></a>使用 TMX 檔案

參考的時間層級會定義使用.tmx 檔案格式，輸出的[磚塊](http://www.mapeditor.org)磚對應編輯器。 如需使用磚塊的詳細討論，請參閱[使用並排顯示與 Cocos 尖指南](~/graphics-games/cocossharp/tiled.md)。 

每個層級中包含它自己.tmx 檔案中定義**CoinTime/資產/內容/層級**資料夾。 所有的參考時間層級共用一個地塊檔，其定義在**mastersheet.tsx**檔案。 此檔案會定義每個圖格，例如是否這些磚的實心衝突，或是否應該取代磚的實體執行個體的自訂屬性。 Mastersheet.tsx 檔案可讓您能夠一次定義和使用的所有層級的屬性。 


## <a name="editing-a-tile-map"></a>編輯磚對應

若要編輯的磚對應，.tmx 中開啟檔案磚塊.tmx 檔案上按兩下，或透過磚塊中的 [檔案] 功能表開啟它。 層級的磚貼圖包含三個層級的參考時間： 

- **實體**– 此圖層包含將會取代實體在執行階段的執行個體的磚。 範例包括播放程式、 錢幣、 敵人和層級結束媒體櫃門。
- **地形**– 此圖層包含通常具有 實心衝突的磚。 實心衝突可讓這些圖格上逐步不含失敗，播放程式。 磚與實心衝突也可以當做牆與上限。
- **背景**– 背景圖層包含會做為靜態背景的圖格。 此圖層不會相機移動整個層級，建立錯誤外觀透過視差深度捲動。

我們稍後將探索，層級載入程式碼必須要有下列三個層級中所有的參考時間層級。

### <a name="editing-terrain"></a>編輯地形
磚可以放在按一下**mastersheet**地塊，，然後按一下 磚上的對應。 例如，若要繪製新地形層級中：

1. 選取地形圖層
1. 若要繪製磚上按一下
1. 按一下或發送和拖曳來繪製磚對應

    ![](cointime-images/image2.png "按一下磚來繪製 1")

左上方的地塊包含所有的參考時間地形。 地形，也就是純色，包括**SolidCollision**屬性在螢幕左邊的圖格屬性中所示：

![](cointime-images/image3.png "地形，也就是純色，包含 SolidCollision 屬性，在螢幕左邊的圖格屬性中所示")

### <a name="editing-entities"></a>編輯實體
可以加入或移除從層級 – 就像地形實體。 **Mastersheet**地塊已放置關於地球的另一端的水平的所有實體，讓它們可能不會顯示不含向右捲動：

![](cointime-images/image4.png "Mastersheet 地塊已放置關於地球的另一端的水平的所有實體，因此它們不一定會顯示不含向右捲動，")

新的實體應該置於**實體**圖層。

![](cointime-images/image5.png "新的實體應該放在實體層")

CoinTime 程式碼會尋找**EntityType**來識別應該由實體取代的磚載入層級的時機： 

![](cointime-images/image6.png "載入層級來識別應該由實體取代的磚時 CoinTime 程式碼看起來 entitytype")

一旦已修改且儲存檔案，所做的變更會自動顯示在專案建置和執行：

![](cointime-images/image7.png "一旦已修改且儲存檔案，所做的變更會自動顯示在專案建置和執行")


## <a name="adding-new-levels"></a>加入新的層級

加入參考時間層級中的程序需要變更任何程式碼，只有少數小型專案的變更。 若要新增新的層級：

1. 開啟的層級的資料夾位於 <**CoinTime 根目錄 > \CoinTime\Assets\Content\levels**
1. 複製並貼上其中一個層級，例如**level0.tmx**
1. 重新命名新.tmx 檔案，因此它會繼續在層級的數字序列以現有的層級，例如**level8.tmx**
1. 在 Visual Studio 或 Visual Studio for Mac，加入新.tmx 檔案至 Android 的層級資料夾。 請確認檔案使用**AndroidAsset**建置動作。

    ![](cointime-images/image8.png "驗證檔案所使用的 AndroidAsset 建置動作")

1. 將新的.tmx 檔案加入 iOS 層級資料夾。 確定該檔案從其原始位置連結，並確認它會使用**BundleResource**建置動作。

    ![](cointime-images/image9.png "確定該檔案從其原始位置連結，並確認它會使用 BundleResource 建置動作")

新的層級應該在層級的選取畫面會顯示為層級 9 (檔案名稱的層級 0 開始，但數字 1 開始的層級的按鈕):

![](cointime-images/image10.png "新的層級應該在層級的選取畫面會顯示為 0，層級 9 層級的檔案名稱開始，但數字 1 開始的層級的按鈕")


# <a name="level-loading"></a>載入層級

如先前所示，新的層級需要任何程式碼中的變更 – 遊戲會自動偵測層級，如果它們會正確命名，並新增到**層級**資料夾具有正確的建置動作 (**BundleResource**或**AndroidAsset**)。

判斷層級數目的邏輯包含於`LevelManager`類別。 執行個體時`LevelManager`（使用單一子句模式），建構`DetermineAvailbleLevels`方法呼叫：


```csharp
private void DetermineAvailableLevels()
{
    // This game relies on levels being named "levelx.tmx" where x is an integer beginning with
    // 1. We have to rely on MonoGame's TitleContainer which doesn't give us a GetFiles method - we simply
    // have to check if a file exists, and if we get an exception on the call then we know the file doesn't
    // exist. 
    NumberOfLevels = 0;
    while (true)
    {
        bool fileExists = false;
        try
        {
            using(var stream = TitleContainer.OpenStream("Content/levels/level" + NumberOfLevels + ".tmx"))
            {
            }
            // if we got here then the file exists!
            fileExists = true;
        }
        catch
        {
            // do nothing, fileExists will remain false
        }
        if (!fileExists)
        {
            break;
        }
        else
        {
            NumberOfLevels++;
        }
    }
}
```

CocosSharp 不提供跨平台方式偵測檔案是否存在，所以我們必須依賴`TitleContainer`嘗試開啟資料流的類別。 如果開啟資料流的程式碼擲回例外狀況，則檔案並不存在，所以 while 迴圈的符號。 當迴圈完成時，`NumberOfLevels`屬性報告多少的有效層級專案的一部分。

`LevelSelectScene`類別會使用`LevelManager.NumberOfLevels`來判斷多少的按鈕，以在建立`CreateLevelButtons`方法：


```csharp
private void CreateLevelButtons()
{
    const int buttonsPerPage = 6;
    int levelIndex0Based = buttonsPerPage * pageNumber;
    int maxLevelExclusive = System.Math.Min (levelIndex0Based + 6, LevelManager.Self.NumberOfLevels);
    int buttonIndex = 0;
    float centerX = this.ContentSize.Center.X;
    const float topRowOffsetFromCenter = 16;
    float topRowY = this.ContentSize.Center.Y + topRowOffsetFromCenter;
    for (int i = levelIndex0Based; i < maxLevelExclusive; i++)
    {
        ...
    }
}
```

`NumberOflevels`屬性用來判斷應建立哪些按鈕。 此程式碼會視為使用者目前正在檢視，並只會建立最多六個每一頁按鈕的分頁。 按一下時，按鈕執行個體呼叫`HandleButtonClicked`方法：


```csharp
private void HandleButtonClicked(object sender, EventArgs args)
{
    // levelNumber is 1-based, so subtract 1:
    var levelIndex = (sender as Button).LevelNumber - 1;
    LevelManager.Self.CurrentLevel = levelIndex;
    CoinTime.GameAppDelegate.GoToGameScene ();
}
```

這個方法會將指派`CurrentLevel`屬性所用`GameScene`載入層級時。 設定之後`CurrentLevel`、`GoToGameScene`引發方法時，切換從場景`LevelSelectScene`至`GameScene`。

`GameScene`建構函式呼叫`GoToLevel`，它會執行層級載入邏輯：


```csharp
private void GoToLevel(int levelNumber)
{
    LoadLevel (levelNumber);
    CreateCollision();
    ProcessTileProperties ();
    touchScreen = new TouchScreenInput(gameplayLayer);
    secondsLeft = secondsPerLevel;
}
```

接下來我們將探討中呼叫方法`GoToLevel`。


## <a name="loadlevel"></a>LoadLevel

`LoadLevel`方法負責載入.tmx 檔案，並將它加入至`GameScene`。 這個方法不會建立任何互動的物件，例如衝突或實體 – 它只會建立視覺效果的層級，也稱為*環境*。


```csharp
private void LoadLevel(int levelNumber)
{
    currentLevel = new CCTileMap ("level" + levelNumber + ".tmx");
    currentLevel.Antialiased = false;
    backgroundLayer = currentLevel.LayerNamed ("Background");
    // CCTileMap is a CCLayer, so we'll just add it under all entities
    this.AddChild (currentLevel);
    // put the game layer after
    this.RemoveChild(gameplayLayer);
    this.AddChild(gameplayLayer);
    this.RemoveChild (hudLayer);
    this.AddChild (hudLayer);
}
```

`CCTileMap`建構函式接受檔案名稱，會使用傳入的層級編號建立`LoadLevel`。 如需有關建立和使用`CCTileMap`執行個體，請參閱[使用並排顯示與 CocosSharp 指南](~/graphics-games/cocossharp/tiled.md)。

目前，CocosSharp 不允許的圖層移除並重新將它們加入至其父代重新調整順序`CCScene`(也就是`GameScene`在此情況下)，因此方法的最後幾行，才能重新排列圖層。


## <a name="createcollision"></a>CreateCollision

`CreateCollision`方法建構`LevelCollision`用來執行的執行個體*實心衝突*player 與環境之間。


```csharp
private void CreateCollision()
{
    levelCollision = new LevelCollision();
    levelCollision.PopulateFrom(currentLevel);
}
```

如果沒有此衝突，播放程式會落入層級，遊戲會無法播放。 實心衝突可讓播放程式引導地面，並防止從逐步解說牆壁或跳躍向上上限透過播放程式。

沒有額外的程式碼 – 只有修改並排顯示的檔案，可以加入衝突的參考時間。 


## <a name="processtileproperties"></a>ProcessTileProperties

一旦載入層級，並建立衝突發生時，`ProcessTileProperties`呼叫來執行圖格屬性為基礎的邏輯。 參考時間包括`PropertyLocation`對內容和磚，含有這些屬性的座標定義的結構：


```csharp
public struct PropertyLocation
{
    public CCTileMapLayer Layer;
    public CCTileMapCoordinates TileCoordinates;
    public float WorldX;
    public float WorldY;
    public Dictionary<string, string> Properties;
}
```

此結構用來建構建立實體執行個體，並移除不必要的圖格`ProcessTileProperties`方法：


```csharp
private void ProcessTileProperties()
{
    TileMapPropertyFinder finder = new TileMapPropertyFinder (currentLevel);
    foreach (var propertyLocation in finder.GetPropertyLocations())
    {
        var properties = propertyLocation.Properties;
        if (properties.ContainsKey ("EntityType"))
        {
            float worldX = propertyLocation.WorldX;
            float worldY = propertyLocation.WorldY;
            if (properties.ContainsKey ("YOffset"))
            {
                string yOffsetAsString = properties ["YOffset"];
                float yOffset = 0;
                float.TryParse (yOffsetAsString, out yOffset);
                worldY += yOffset;
            }
            bool created = TryCreateEntity (properties ["EntityType"], worldX, worldY);
            if (created)
            {
                propertyLocation.Layer.RemoveTile (propertyLocation.TileCoordinates);
            }
        }
        else if (properties.ContainsKey ("RemoveMe"))
        {
            propertyLocation.Layer.RemoveTile (propertyLocation.TileCoordinates);
        }
    }
}
```

「 Foreach 迴圈 」 會評估每個圖格屬性，檢查索引鍵是否是`EntityType`或`RemoveMe`。 `EntityType` 表示應建立實體執行個體。 `RemoveMe` 表示，應該在執行階段完全移除磚。

如果屬性與`EntityType`找到的機碼，然後`TryCreateEntity`呼叫時，都會嘗試建立使用屬性比對實體`EntityType`機碼：


```csharp
private bool TryCreateEntity(string entityType, float worldX, float worldY)
{
    CCNode entityAsNode = null;
    switch (entityType)
    {
    case "Player":
        player = new Player ();
        entityAsNode = player;
        break;
    case "Coin":
        Coin coin = new Coin ();
        entityAsNode = coin;
        coins.Add (coin);
        break;
    case "Door":
        door = new Door ();
        entityAsNode = door;
        break;
    case "Spikes":
        var spikes = new Spikes ();
        this.damageDealers.Add (spikes);
        entityAsNode = spikes;
        break;
    case "Enemy":
        var enemy = new Enemy ();
        this.damageDealers.Add (enemy);
        this.enemies.Add (enemy);
        entityAsNode = enemy;
        break;
    }
    if(entityAsNode != null)
    {
        entityAsNode.PositionX = worldX;
        entityAsNode.PositionY = worldY;
        gameplayLayer.AddChild (entityAsNode);
    }
    return entityAsNode != null;
}
```


# <a name="adding-new-entities"></a>加入新的實體

其中遊戲物件的參考時間會使用的實體模式 (這在講述[CocosSharp 中的實體引導](~/graphics-games/cocossharp/entities.md))。 所有實體都繼承自`CCNode`，這表示可以將它們加入做為子系的`gameplayLayer`。

每個實體類型也會直接透過清單或單一執行個體參考。 例如，`Player`正由`player`欄位，以及所有`Coin`中所參考的執行個體`coins`清單。 保留實體的直接參考 (相對於參考這些透過`gameLayer.Children`清單) 可讓程式碼存取這些項目更方便閱讀，並減少可能相當耗費資源的類型轉換。

現有的程式碼提供範例如何建立新的實體的實體類型的數字。 下列步驟可用來建立新的實體：


## <a name="1---define-a-new-class-using-the-entity-pattern"></a>1-定義使用實體模式的新類別

建立實體的唯一需求是建立繼承自一個類別`CCNode`。 大多數實體具有一些視覺效果，例如`CCSprite`，應該加入其建構函式中的實體的子系。

提供 CoinTime`AnimatedSpriteEntity`可簡化建立動畫的實體類別。 將更詳細地討論動畫[動畫實體 區段](#Animated_Entities)。


## <a name="2--add-a-new-entry-to-the-trycreateentity-switch-statement"></a>2 – TryCreateEntity switch 陳述式來加入新項目

新實體的執行個體應該在具現化`TryCreateEntity`。 如果實體需要每隔框架邏輯，像是衝突、 AI 或讀取輸入，然後在`GameScene`需要保存物件的參考。 如果需要多個執行個體 (例如`Coin`或`Enemy`執行個體)，然後新`List`應加入至`GameScene`類別。


## <a name="3--modify-tile-properties-for-the-new-entity"></a>3 – 修改新實體的圖格屬性

一旦程式碼支援建立新的實體，新實體，就需要加入地塊。 開啟任何層級也可以編輯地塊`.tmx`檔案。 

地塊儲存分開中.tmx **mastersheet.tsx**檔案，因此必須將它匯入後才能編輯：

![](cointime-images/image11.png "地塊儲存個別從.tsx 檔，因此必須將它匯入後才能編輯")

當匯入，在選取的磚上的屬性是可編輯的而且可以加入 EntityType:

![](cointime-images/image12.png "一旦匯入，在選取的磚上的屬性是可編輯的而且可以加入 EntityType")

建立屬性後，其值可以設定以符合新`case`中`TryCreateEntity`:

![](cointime-images/image13.png "其值建立屬性後，可以設定為 TryCreateEntity 中新的大小寫須相符")

地塊已變更之後，必須匯出 – 這會進行變更適用於所有其他層級：

![](cointime-images/image14.png "地塊已變更之後，必須匯出")

地塊應該覆寫現有**mastersheet.tsx**地塊：

![](cointime-images/image15.png "他地塊應該覆寫現有的 mastersheet.tsx 地塊")


# <a name="entity-tile-removal"></a>實體磚移除

遊戲載入圖格對應時，個別的圖格就會是靜態物件。 因為實體需要自訂的行為，例如移動，參考時間程式碼建立實體時，就會移除磚。

`ProcessTileProperties` 包含邏輯，可移除使用實體中建立的磚`RemoveTile`方法：


```csharp
private void ProcessTileProperties()
{
    TileMapPropertyFinder finder = new TileMapPropertyFinder (currentLevel);
    foreach (var propertyLocation in finder.GetPropertyLocations())
    {
        var properties = propertyLocation.Properties;
        if (properties.ContainsKey ("EntityType"))
        {
            ...
            bool created = TryCreateEntity (properties ["EntityType"], worldX, worldY);
            if (created)
            {
                propertyLocation.Layer.RemoveTile (propertyLocation.TileCoordinates);
            }
        }
        ...
    }
}
```

此自動移除的磚已足以應付佔用地塊，例如錢幣和敵人中只有一個磚的實體。 較大的實體會需要額外的邏輯和屬性。

媒體櫃門需要完全繪製兩個磚：

![](cointime-images/image16.png "媒體櫃門需要完全繪製兩個磚")

媒體櫃門的底部磚包含建立實體的屬性 (**EntityType**設**門**):

![](cointime-images/image17.png "媒體櫃門的底部磚包含用於建立媒體櫃門設定 EntityType 的實體屬性")

門執行個體建立時，會移除僅媒體櫃門的底部磚，因為需要額外邏輯，才能移除最上層的磚，在執行階段。 最上層的磚具有**RemoveMe**屬性設定為**true**:

![](cointime-images/image18.png "頂端的圖格都具有 RemoveMe 屬性設為 true")



這個屬性用來移除磚`ProcessTileProperties`:


```csharp
private void ProcessTileProperties()
{
    TileMapPropertyFinder finder = new TileMapPropertyFinder (currentLevel);
    foreach (var propertyLocation in finder.GetPropertyLocations())
    {
        var properties = propertyLocation.Properties;
        ...
        else if (properties.ContainsKey ("RemoveMe"))
        {
            propertyLocation.Layer.RemoveTile (propertyLocation.TileCoordinates);
        }
    }
}
```


# <a name="entity-offsets"></a>實體位移

對齊中央實體的圖格的中央位於實體所建立的磚。 較大的實體，例如`Door`，使用其他的屬性和邏輯來正確地放置。 

下方的媒體櫃門磚，定義`Door`實體位置指定**YOffset** 4 的值。 這個屬性，沒有`Door`並排的中心位於執行個體：

![](cointime-images/image19.png "沒有這個屬性，門執行個體位於圖格的中央")

 

這藉由套用更正**YOffset**值`ProcessTileProperties`:


```csharp
private void ProcessTileProperties()
{
    TileMapPropertyFinder finder = new TileMapPropertyFinder (currentLevel);
    foreach (var propertyLocation in finder.GetPropertyLocations())
    {
        var properties = propertyLocation.Properties;
        if (properties.ContainsKey ("EntityType"))
        {
            float worldX = propertyLocation.WorldX;
            float worldY = propertyLocation.WorldY;
            if (properties.ContainsKey ("YOffset"))
            {
                string yOffsetAsString = properties ["YOffset"];
                float yOffset = 0;
                float.TryParse (yOffsetAsString, out yOffset);
                worldY += yOffset;
            }
            bool created = TryCreateEntity (properties ["EntityType"], worldX, worldY);
            ...
        }
...
    }
}
```


# <a name="animated-entities"></a>動畫的實體

參考時間包括數個動畫項目。 `Player`和`Enemy`實體播放查核行程動畫和`Door`一旦所有錢幣已經收集都到的實體會播放開啟動畫。


## <a name="achx-files"></a>.achx 檔案

參考時間動畫.achx 檔案中定義。 每個動畫之間定義`AnimationChain`標記中定義下列動畫所示**propanimations.achx**:


```xml
<AnimationChain>
  <Name>Spikes</Name>
  <ColorKey>0</ColorKey>
  <Frame>
    <FlipHorizontal>false</FlipHorizontal>
    <FlipVertical>false</FlipVertical>
    <TextureName>..\images\mastersheet.png</TextureName>
    <FrameLength>0.1</FrameLength>
    <LeftCoordinate>1152</LeftCoordinate>
    <RightCoordinate>1168</RightCoordinate>
    <TopCoordinate>128</TopCoordinate>
    <BottomCoordinate>144</BottomCoordinate>
    <RelativeX>0</RelativeX>
    <RelativeY>0</RelativeY>
  </Frame>
</AnimationChain> 
```

這個動畫只會包含在單一的範圍內，導致突然增加實體顯示的靜態影像。 實體可以使用.achx 檔案，是否會顯示單一或多重畫面格的動畫。 不需要在程式碼中的任何變更時，可以加入其他框架.achx 檔案。 

框架會定義要顯示的映像`TextureName`參數，以及顯示在座標`LeftCoordinate`， `RightCoordinate`， `TopCoordinate`，和`BottomCoordinate`標記。 這些代表動畫是正在使用 – 映像中的畫面格的像素座標**mastersheet.png**在此情況下。

![](cointime-images/image20.png "這些代表映像中的動畫畫面格的像素座標")

`FrameLength`屬性會定義應該顯示的畫面格在動畫中的秒數。 單一畫面格動畫會忽略此值。

.Achx 檔案中的所有其他 AnimationChain 屬性會忽略參考時間。


## <a name="animatedspriteentity"></a>AnimatedSpriteEntity

動畫邏輯包含於`AnimatedSpriteEntity`類別會做為基底類別，如在中使用的大部分實體`GameScene`。 它提供下列功能：

 - 載入`.achx`檔案
 - 載入的動畫的動畫快取
 - 顯示動畫 CCSprite 執行個體
 - 變更目前的框架邏輯

如果看到爆增情形建構函式會提供如何載入及使用動畫的簡單範例：


```csharp
public Spikes ()
{
    LoadAnimations ("Content/animations/propanimations.achx");
    CurrentAnimation = animations [0];
}
```

**PropAnimations.achx**只包含一個動畫，讓建構函式來索引存取這個動畫。 如果.achx 檔案包含多個動畫，則中所示，可以依名稱參考動畫`Enemy`建構函式：


```csharp
walkLeftAnimation = animations.Find (item => item.Name == "WalkLeft");
walkRightAnimation = animations.Find (item => item.Name == "WalkRight");
```


# <a name="summary"></a>總結

本指南涵蓋錢幣時間的實作詳細資料。 參考時間建立要在完整的遊戲，但也可以輕鬆地修改並擴充的專案。 加入新的層級，及建立新的實體來進一步了解如何實作參考的時間，是鼓勵花時間層級，讓修改讀取器。

## <a name="related-links"></a>相關連結

- [遊戲專案 （範例）](https://developer.xamarin.com/samples/mobile/CoinTime/)
