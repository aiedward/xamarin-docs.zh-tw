---
title: Coin 時間遊戲詳細資料
description: 本指南討論在錢幣來比喻時間遊戲中，包括使用圖格對應、 建立實體，以動畫顯示精靈，以及實作有效率的衝突的實作詳細資料。
ms.prod: xamarin
ms.assetid: 5D285684-0417-4E16-BD14-2D1F6DEFBB8B
author: conceptdev
ms.author: crdun
ms.date: 03/24/2017
ms.openlocfilehash: af914e10eb93aa0668743a113ffe647a939fea75
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50112620"
---
# <a name="coin-time-game-details"></a>Coin 時間遊戲詳細資料

_本指南討論在錢幣來比喻時間遊戲中，包括使用圖格對應、 建立實體，以動畫顯示精靈，以及實作有效率的衝突的實作詳細資料。_

錢幣來比喻時適用於 iOS 和 Android 遊戲完整平台遊戲。 遊戲的目標是收集所有層級中銅板和則同時避免敵人和障礙達到結束的門。

![](cointime-images/image1.png "遊戲的目標是收集所有層級中銅板和則達到結束門，同時避免敵人和障礙")

本指南會討論錢幣來比喻時，涵蓋下列主題中的實作詳細資料：

- [使用 tmx 檔案](#working-with-tmx-files)
- [正在載入層級](#level-loading)
- [加入新的實體](#adding-new-entities)
- [動畫的實體](#animated-entities)


## <a name="content-in-coin-time"></a>錢幣來比喻時間中的內容

錢幣來比喻時，表示完整的 CocosSharp 專案可能會如何組織專案範例。 Coin Time 的結構，目標是簡化的新增和維護的內容。 它會使用 **.tmx**所建立的檔案[磚塊](http://www.mapeditor.org)層級和 XML 檔案來定義動畫。 修改或加入新的內容可透過最少的工作。 

雖然這種方法錢幣來比喻時間有效的專案，對於學習及實驗，它也會反映如何專業遊戲進行。 本指南將說明一些簡化的加入和修改內容所採行的方法。


## <a name="working-with-tmx-files"></a>使用 tmx 檔案

使用輸出的.tmx 檔案格式定義錢幣來比喻時間層級[磚塊](http://www.mapeditor.org) 圖格對應編輯器。 如需使用磚塊的詳細討論，請參閱[Cocos Sharp 指南搭配 cocossharp 使用](~/graphics-games/cocossharp/tiled.md)。 

包含在它自己.tmx 檔案中定義每個層級**CoinTime/資產/內容/層級**資料夾。 所有的錢幣來比喻時間層級共用一個地塊檔案，其定義於**mastersheet.tsx**檔案。 此檔案會定義每個圖格，例如 [] 圖格是否有穩固的衝突，或是否應該實體執行個體換成圖格的自訂屬性。 Mastersheet.tsx 檔案可讓您只定義一次並用於所有層級的屬性。 


### <a name="editing-a-tile-map"></a>編輯圖格對應

若要編輯圖格對應，.tmx 中開啟檔案磚塊.tmx 檔案上按兩下，或透過磚塊中的 [檔案] 功能表中開啟它。 Coin 的時間層級的圖格貼圖包含三個層級： 

- **實體**– 這一層包含磚將會取代在執行階段的實體的執行個體。 範例包括播放程式、 銅板、 敵人和的層級的終端機門。
- **地形**– 這一層包含通常具有 實心衝突的磚。 Solid 衝突可讓這些圖格上引導，而不需要一直失敗，播放程式。 使用 solid 衝突的圖格也可以做為背景牆和上限。
- **背景**– 背景圖層包含用來作為靜態背景的圖格。 此圖層不會捲動時將觀景窗移動的層級，建立透過視差深度的外觀。

如我們稍後將探討，層級載入程式碼會預期這些所有錢幣來比喻時間層級中的三個層級。

#### <a name="editing-terrain"></a>編輯地形模型

圖格可以放在中按一下**mastersheet**地塊，然後按一下 [] 圖格的對應。 例如，若要繪製新的地形模型層級中：

1. 選取的地形圖層
1. 按一下要繪製的圖格
1. 按一下或推播，然後拖曳至要繪製的圖格的對應

    ![](cointime-images/image2.png "按一下圖格來繪製 1")

左上方的地塊包含所有錢幣來比喻時間地形。 地形模型，也就是純色，包括**SolidCollision**屬性，如圖格屬性，在畫面的左側所示：

![](cointime-images/image3.png "地形模型，也就是純色，包含 SolidCollision 屬性，如圖格屬性，在畫面的左側所示")

#### <a name="editing-entities"></a>編輯實體

可以新增或移除從層級 – 就像地形實體。 **Mastersheet**地塊已放置關於中間水平的所有實體，所以它們可能無法顯示而不需要向右捲動：

![](cointime-images/image4.png "Mastersheet 地塊已放置關於中間水平的所有實體，所以它們可能無法顯示而不需要向右捲動")

新的實體應該置於**實體**層。

![](cointime-images/image5.png "新的實體應該放在實體層級")

CoinTime 程式碼會尋找**EntityType**層級來識別應取代為實體的圖格的載入時： 

![](cointime-images/image6.png "CoinTime 程式碼會尋找實體類型載入層級來識別應取代為實體的圖格時")

一旦檔案已修改且儲存，所做的變更會自動顯示專案是建置並執行：

![](cointime-images/image7.png "一旦檔案已修改且儲存，所做的變更會自動顯示專案是建置並執行")


### <a name="adding-new-levels"></a>加入新的層級

加入錢幣來比喻時間的層級中的程序需要變更任何程式碼，並變更只有一些小型至專案。 若要新增新的層級：

1. 開啟的層級的資料夾位於 <**CoinTime 根 > \CoinTime\Assets\Content\levels**
1. 複製並貼上其中一個層級，例如**level0.tmx**
1. 重新命名新.tmx 檔案，因此它會繼續使用現有的層級，層級的數字序列例如**level8.tmx**
1. 在 Visual Studio 或 Visual Studio for Mac 中，將新的.tmx 檔案加入 Android 層級資料夾。 請確認該檔案會使用**AndroidAsset**建置動作。

    ![](cointime-images/image8.png "請確認檔案使用 AndroidAsset 建置動作")

1. 將新的.tmx 檔案加入 [iOS] 層級資料夾。 請務必將連結從其原始位置的檔案，並確認它會使用**BundleResource**建置動作。

    ![](cointime-images/image9.png "請務必將連結從其原始位置的檔案，並確認它會使用 BundleResource 建置動作")

新的層級時，應該出現在層級的選取畫面上，為層級 9 (檔案名稱的層級 0 開始，但數字 1 開始的層級的按鈕):

![](cointime-images/image10.png "新的層級應該在層級的選取畫面會顯示為 0，層級 9 層級的檔案名稱的開頭，但數字 1 開始的層級的按鈕")


## <a name="level-loading"></a>正在載入層級

如先前所示，新的層級不需要變更程式碼中的 – 遊戲會自動偵測層級，如果它們是名稱正確，並新增至**層級**資料夾中，使用正確的建置動作 (**BundleResource**或是**AndroidAsset**)。

判斷層級數目邏輯都包含在`LevelManager`類別。 執行個體時`LevelManager`（使用單一子句模式），建構`DetermineAvailbleLevels`方法呼叫：


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

CocosSharp 不提供以跨平台的方法來偵測如果檔案不存在，所以我們不必依賴`TitleContainer`嘗試開啟資料流的類別。 如果開啟資料流的程式碼擲回例外狀況，則檔案並不存在，while 迴圈的符號。 當迴圈完成時，`NumberOfLevels`屬性回報多少的有效層級是專案的一部分。

`LevelSelectScene`類別會使用`LevelManager.NumberOfLevels`來判斷多少的按鈕，以建立在`CreateLevelButtons`方法：


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

`NumberOflevels`屬性用來判斷應該建立的按鈕。 此程式碼會視為使用者目前正在檢視，並只會建立最多六個按鈕，每個頁面的頁面。 按一下時，按鈕執行個體呼叫`HandleButtonClicked`方法：


```csharp
private void HandleButtonClicked(object sender, EventArgs args)
{
    // levelNumber is 1-based, so subtract 1:
    var levelIndex = (sender as Button).LevelNumber - 1;
    LevelManager.Self.CurrentLevel = levelIndex;
    CoinTime.GameAppDelegate.GoToGameScene ();
}
```

這個方法會指派`CurrentLevel`屬性會使用`GameScene`載入層級時。 在設定後`CurrentLevel`，則`GoToGameScene`引發方法時，切換從場景`LevelSelectScene`至`GameScene`。

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


### <a name="loadlevel"></a>LoadLevel

`LoadLevel`方法會負責載入.tmx 檔案，並將它新增至`GameScene`。 這個方法不會建立任何互動的物件，例如衝突或實體 – 它只會建立視覺效果層級，也稱為*環境*。


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

`CCTileMap`建構函式接受檔案名稱，這建立使用傳遞至層級編號`LoadLevel`。 如需有關建立和處理`CCTileMap`執行個體，請參閱[搭配 cocossharp 使用 CocosSharp 指南](~/graphics-games/cocossharp/tiled.md)。

目前，CocosSharp 不允許重新排列圖層，而不需要移除並重新將其新增至其父代`CCScene`(也就是`GameScene`在此情況下)，因此方法的最後幾行，才能重新排序各層。


### <a name="createcollision"></a>CreateCollision

`CreateCollision`方法建構`LevelCollision`執行個體用來執行*solid 衝突*播放程式與環境之間。


```csharp
private void CreateCollision()
{
    levelCollision = new LevelCollision();
    levelCollision.PopulateFrom(currentLevel);
}
```

沒有衝突，播放程式會繼續到層級，遊戲將會無法播放。 Solid 衝突可讓播放程式引導到地上，並防止播放程式從逐步解說牆或跳過，以透過上限。

衝突的錢幣來比喻時間可以不加任何額外的程式碼 – 用來只修改並排顯示的檔案。 


### <a name="processtileproperties"></a>ProcessTileProperties

一旦載入層級，並建立衝突時，`ProcessTileProperties`呼叫來執行圖格屬性為基礎的邏輯。 錢幣來比喻時間包含`PropertyLocation`屬性及這些屬性的圖格的座標所定義的結構：


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

「 Foreach 迴圈會評估每個圖格屬性，檢查是否有索引鍵是`EntityType`或`RemoveMe`。 `EntityType` 表示應建立這些實體執行個體。 `RemoveMe` 指出，應該在執行階段完全移除 [] 圖格。

如果具有的屬性`EntityType`找到的索引鍵，然後`TryCreateEntity`呼叫時，都會嘗試建立使用屬性比對實體`EntityType`機碼：


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


## <a name="adding-new-entities"></a>加入新的實體

錢幣來比喻時間用於其遊戲物件中的實體模式 (這涵蓋[CocosSharp 中的實體引導](~/graphics-games/cocossharp/entities.md))。 所有實體都繼承自`CCNode`，這表示它們可以加入做為子系的`gameplayLayer`。

每個實體類型也會直接透過清單或單一執行個體參考。 例如，`Player`參考`player`欄位，以及所有`Coin`中所參考的執行個體`coins`清單。 保留實體的直接參考 (而不是參考它們透過`gameLayer.Children`清單) 可讓程式碼存取這些實體更方便閱讀，並消除可能相當耗費資源的型別轉換。

現有的程式碼提供實體類型的數的字，以範例，示範如何建立新的實體。 下列步驟可用來建立新的實體：


### <a name="1---define-a-new-class-using-the-entity-pattern"></a>1-定義新的類別，使用實體模式

建立實體的唯一需求是建立類別繼承自`CCNode`。 大多數實體具有一些視覺效果，例如`CCSprite`，其應加入作為其建構函式中的實體的子系。

CoinTime 提供`AnimatedSpriteEntity`類別可簡化建立動畫的實體。 將更詳細地討論動畫[動畫實體區段](#animated-entities)。


### <a name="2--add-a-new-entry-to-the-trycreateentity-switch-statement"></a>2-將新的項目新增至 TryCreateEntity switch 陳述式

新實體的執行個體應該在具現化`TryCreateEntity`。 如果實體需要每個框架邏輯，例如衝突、 AI、 或讀取輸入，然後在`GameScene`需要保存物件的參考。 如果需要多個執行個體 (例如`Coin`或是`Enemy`執行個體)，然後新`List`應新增至`GameScene`類別。


### <a name="3--modify-tile-properties-for-the-new-entity"></a>3 – 修改新實體的圖格屬性

後的程式碼支援建立新的實體，新的實體，就必須新增至地塊。 開啟任何層級也可以編輯地塊`.tmx`檔案。 

地塊儲存分開中.tmx **mastersheet.tsx**檔案，因此必須將它匯入後進行編輯：

![](cointime-images/image11.png "地塊儲存分開.tsx 檔中，因此必須將它匯入後才能編輯")

一旦匯入，在所選的圖格上的屬性都可以編輯，並可以加入實體類型：

![](cointime-images/image12.png "一旦匯入，在所選的圖格上的屬性是可編輯的而且可以加入實體類型")

在建立屬性之後，其值可以設定以符合新的`case`在`TryCreateEntity`:

![](cointime-images/image13.png "在建立屬性之後，其值可以設 TryCreateEntity 中新的大小寫須相符")

地塊變更之後，您必須匯出 – 這會讓所做的變更可供所有其他層級：

![](cointime-images/image14.png "地塊變更之後，您必須匯出")

地塊應該覆寫現有**mastersheet.tsx**地塊：

![](cointime-images/image15.png "他地塊應該覆寫現有的 mastersheet.tsx 地塊")


## <a name="entity-tile-removal"></a>實體 圖格移除

加入遊戲載入圖格對應時，個別的圖格會是靜態的物件。 因為實體需要自訂的行為，例如移動，錢幣來比喻時間程式碼建立實體時，就會移除圖格。

`ProcessTileProperties` 包含邏輯，以移除圖格會建立使用實體`RemoveTile`方法：


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

此自動移除圖格是足夠的實體中地塊，例如銅板和敵人佔用只有一個圖格。 較大型的實體會需要額外的邏輯和屬性。

媒體櫃門需要完全繪製兩個磚：

![](cointime-images/image16.png "媒體櫃門需要兩個完全要繪製的圖格")

媒體櫃門 [下方] 圖格包含用於建立實體屬性 (**EntityType**設為**門**):

![](cointime-images/image17.png "媒體櫃門 [下方] 圖格包含用於建立的實體集門的 EntityType 屬性")

門執行個體建立時，只有 [下方] 圖格門已移除，因為需要額外的邏輯，才能移除頂端的圖格，在執行階段。 頂端的圖格已**RemoveMe**屬性設定為 **，則為 true**:

![](cointime-images/image18.png "頂端的圖格都具有 RemoveMe 屬性設定為 true")



這個屬性用來移除圖格`ProcessTileProperties`:


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


## <a name="entity-offsets"></a>實體位移

藉由對齊中央的 [實體] 圖格的中央位於實體建立的圖格。 較大型的實體，例如`Door`，使用額外的屬性和邏輯來正確放置。 

底部媒體櫃門 磚，以定義`Door`實體位置，指定**YOffset**值為 4。 沒有這個屬性，`Door`執行個體位於磚的中心：

![](cointime-images/image19.png "而不需要此屬性，門執行個體位於中央的 [] 圖格")

 

這藉由套用更正**YOffset**中的值`ProcessTileProperties`:


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


## <a name="animated-entities"></a>動畫的實體

錢幣來比喻時間包含數個動畫的實體。 `Player`並`Enemy`實體播放查核行程動畫和`Door`之後已經收集所有銅板開啟動畫所扮演的實體。


### <a name="achx-files"></a>.achx 檔案

錢幣來比喻時間動畫.achx 檔案中定義。 每個動畫定義之間`AnimationChain`標記中定義下列動畫中所示**propanimations.achx**:


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

這個動畫只會包含在單一的範圍內，導致顯示的靜態影像的特殊圖文集實體。 實體可以使用.achx 檔案，是否顯示單一或多重畫面格動畫。 額外的畫面格可以加入至.achx 檔案，而不需要在程式碼中的任何變更。 

畫面格定義中顯示的映像`TextureName`參數，並在顯示的座標`LeftCoordinate`， `RightCoordinate`， `TopCoordinate`，和`BottomCoordinate`標記。 這些代表要用 – 映像中的動畫畫面格的像素座標**mastersheet.png**在此情況下。

![](cointime-images/image20.png "這些代表動畫影像中的畫面格的像素座標")

`FrameLength`屬性會定義應該顯示動畫中的框架秒數。 單一畫面格動畫會忽略此值。

.Achx 檔案中的所有其他 AnimationChain 屬性會忽略錢幣來比喻時間。


### <a name="animatedspriteentity"></a>AnimatedSpriteEntity

動畫邏輯都包含在`AnimatedSpriteEntity`類別，以做為基底類別，針對使用中的大部分實體`GameScene`。 它提供下列功能：

 - 載入`.achx`檔案
 - 動畫快取載入的動畫
 - 顯示動畫的 CCSprite 執行個體
 - 變更目前的框架的邏輯

尖峰建構函式會提供如何載入及使用動畫的簡單範例：


```csharp
public Spikes ()
{
    LoadAnimations ("Content/animations/propanimations.achx");
    CurrentAnimation = animations [0];
}
```

**PropAnimations.achx**只包含一個動畫，讓建構函式依索引存取這個動畫。 如果.achx 檔案包含多個動畫，則動畫可以依名稱，來參考，如中所示`Enemy`建構函式：


```csharp
walkLeftAnimation = animations.Find (item => item.Name == "WalkLeft");
walkRightAnimation = animations.Find (item => item.Name == "WalkRight");
```


## <a name="summary"></a>總結

本指南涵蓋錢幣來比喻時間的實作詳細資料。 錢幣來比喻時間建立是完整的遊戲，但也可以輕鬆地修改和擴充的專案。 讀者在新增新的層級，以及建立新的實體來進一步了解如何實作錢幣來比喻時間是花時間層級，已進行修改調整建議。

## <a name="related-links"></a>相關連結

- [遊戲專案 （範例）](https://developer.xamarin.com/samples/mobile/CoinTime/)
