---
title: 效能和使用 CCRenderTexture 的視覺效果
description: CCRenderTexture 可讓開發人員藉由減少繪製呼叫，改善他們 CocosSharp 的遊戲的效能，而且可用來建立視覺效果。 本指南隨附 CCRenderTexture 範例，以提供如何有效地使用這個類別的實際操作範例。
ms.prod: xamarin
ms.assetid: F02147C2-754B-4FB4-8BE0-8261F1C5F574
author: conceptdev
ms.author: crdun
ms.openlocfilehash: 2e4d9a8fdefd0c543c29061da37098e443d1c092
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50123111"
---
# <a name="performance-and-visual-effects-with-ccrendertexture"></a>效能和使用 CCRenderTexture 的視覺效果

_CCRenderTexture 可讓開發人員藉由減少繪製呼叫，改善他們 CocosSharp 的遊戲的效能，而且可用來建立視覺效果。本指南隨附 CCRenderTexture 範例，以提供如何有效地使用這個類別的實際操作範例。_

`CCRenderTexture`類別會提供用於呈現單一材質的多個 CocosSharp 物件的功能。 建立後，`CCRenderTexture`可以用於執行個體，才能有效呈現圖形，並且實作視覺效果。 `CCRenderTexture` 可讓多個物件即將呈現至單一材質一次。 然後，該紋理可以重複使用的每個畫面，減少繪製呼叫的總數。

本指南會檢驗如何使用`CCRenderTexture`物件，以改善轉譯卡片可收集的紙牌遊戲 (CCG) 中的效能。 它也會示範如何`CCRenderTexture`可用來讓整個實體變成透明。 本指南參考`CCRenderTexture`[範例專案](https://developer.xamarin.com/samples/mobile/CCRenderTexture/)。

![](ccrendertexture-images/image1.png "本指南參考 CCRenderTexture 範例專案")


## <a name="card--a-typical-entity"></a>卡片 – 一般實體

再看看如何使用`CCRenderTexture`物件，我們會先熟悉與我們自己`Card`實體，我們將使用在此專案來探索`CCRenderTexture`類別。 `Card`類別是一般實體，下列實體模式中所述[實體指南](~/graphics-games/cocossharp/entities.md)。 Card 類別都具有所有其視覺元件 (執行個體`CCSprite`和`CCLabel`) 列為欄位：


```csharp
class Card : CCNode
{
    bool usesRenderTexture;
    List<CCNode> visualComponents = new List<CCNode>();
    CCSprite background;
    CCSprite colorIcon;
    CCSprite monsterSprite;
    CCLabel monsterNameDisplay;
    CCLabel hpDisplay;
    CCLabel descriptionDisplay;
```

卡執行個體可以使用轉譯`CCRenderTexture`，或藉由個別繪製每個視覺元件。 每個元件是一個獨立的物件，雖然`CCNode`系統中實體所使用的父代可讓`Card`單一物件形式 – 至少大部分的情況下的行為。 例如，如果`Card`實體重新調整位置、 調整大小或旋轉，則所有包含的視覺物件會使卡片的單一物件似乎受到影響。 若要查看單一物件所做的卡片，我們可以修改`GameLayer.AddedToScene`方法來設定`useRenderTextures`變數加入`false`:

    
```csharp
protected override void AddedToScene ()
{
    base.AddedToScene();
    GameView.Stats.Enabled = true;
    const bool useRenderTextures = false;
    ...
}
```

`GameLayer`程式碼不會移動每個視覺項目分開，還內每個視覺項目`Card`實體正確的位置：

![](ccrendertexture-images/image1.png "GameLayer 程式碼不會獨立地移動每個視覺項目，但 [卡片] 實體內的每個視覺項目正確的位置")

此範例會編碼為公開每個視覺元件呈現其本身時，可能會發生的兩個問題：

- 由於多個繪製呼叫的效能可能會受到影響
- 當我們稍後將會瀏覽特定的視覺效果，如透明效果，也無法精確地說，實作


### <a name="card-draw-calls"></a>卡片的繪製呼叫

我們的程式碼是什麼可能會找到完整的簡化*可收集的撲克牌遊戲*(CCG) 例如"Magic:: 收集 」 或 「 Hearthstone"。 這時候遊戲只一次顯示三張卡片，並有一小部分的可能單位 （藍色、 綠色和橙色）。 相較之下，完整的遊戲可能螢幕上有超過 20 種卡片，在指定的時間，而且播放程式可能有數百個卡，以建立其投影片時從中選擇。 即使我們遊戲不目前會發生效能問題，可能會有類似的實作的完整的遊戲。

CocosSharp 提供一些深入了解藉由公開繪製呼叫呈現效能執行每個畫面。 我們`GameLayer.AddedToScene`方法會設定`GameView.Stats.Enabled`到`true`，產生的顯示畫面的左下方的效能資訊：

![](ccrendertexture-images/image2.png "GameLayer.AddedToScene 方法將 GameView.Stats.Enabled 設為 true，導致畫面的左下角所顯示的效能資訊")

請注意，儘管三張牌在畫面上，我們十九的繪製呼叫 （每個卡片導致六個繪製呼叫，其中詳細顯示效能資訊帳戶的文字）。 繪製呼叫遊戲的效能有重大的影響，因此 CocosSharp 提供數種方式，以減少它們。 其中一種技術所述[CCSpriteSheet 指南](~/graphics-games/cocossharp/ccspritesheet.md)。 另一個方法是使用`CCRenderTexture`減少下一個呼叫，每個實體，如本指南中，我們將檢驗。


### <a name="card-transparency"></a>卡片的透明度

我們`Card`實體包含`Opacity`屬性來控制透明度，如下列程式碼片段所示：


```csharp
public override byte Opacity
{
    get
    {
        return base.Opacity;
    }
    set
    {
        base.Opacity = value;
        if (usesRenderTexture)
        {
            this.renderTexture.Sprite.Opacity = value;
        }
        else
        {
            foreach (var component in visualComponents)
            {
                component.Opacity = value;
            }
        }
    }
}
```

請注意使用的 setter 支援轉譯紋理或個別轉譯每個元件。 若要查看其效果，請變更`opacity`值加入`127`（大約一半的不透明度） 中`GameLayer.AddedToScene`使得每個元件皆`Opacity`的值`127`:


```csharp
protected override void AddedToScene ()
{
    base.AddedToScene();
    GameView.Stats.Enabled = true;
    const bool useRenderTextures = false;
    const byte opacity = 127;
    ...
}
```

遊戲現在會呈現一些透明度，導致它們才會出現較深，因為背景為黑色的卡片：

![](ccrendertexture-images/image3.png "遊戲現在會呈現一些透明度，導致它們才會出現較深，因為背景為黑色的卡片")

第一眼看起來如同我們的卡片已正確進行透明。 不過，螢幕擷取畫面顯示一些視覺化的問題。

由於我們背景是黑色，我們會預期我們卡的每個部分會變成深因為透明度。 也就是說，變得更透明卡片，深得。 在不透明度 0，`Card`會完全透明 （完全黑色）。 不過，我們的卡片的某些部分沒有變得較暗時不透明度變更為`127`。 更糟的是，我們的卡片的某些部分實際上變得更亮當它們變得更透明。 讓我們看看我們的卡片的部分是黑色*之前*它們是透明的 – 特別的詳細資料的文字和黑色 monster 圖形周圍的外框。 如果我們將這些並排顯示，我們可以看到套用透明度的影響：

![](ccrendertexture-images/image4.png "如果並排放置，可以看到套用透明度的影響")

如先前所述，卡片的所有組件應該變得較暗時變得更透明，但在幾個區域的這不是：

- 機器人的外框會變淡 （會從黑色到灰階再）
- 描述文字變淡 （會從黑色到灰階再）
- 綠色部分的機器人變得較不飽和，但不會變得較暗

若要協助視覺化為何發生這種情況，我們需要記住每個視覺元件，是單獨繪製，而每個部分透明。 繪製的第一個視覺化元件是卡片的背景。 後續的透明項目會繪製在卡片上，且會受到卡片的背景。 如果我們從我們的卡片移除一些文字，並向下移動機器人圖形，我們可以看到卡背景有何影響的機器人。 請注意可以看到橘色線條，從頂端的方塊上的機器人，和機器人的重疊藍色條紋，卡片的中央區域繪製深：

![](ccrendertexture-images/image5.png "請注意，可以看到橘色線條，從頂端的方塊上的機器人，和機器人的重疊藍色條紋，卡片的中央區域繪製深")

使用`CCRenderTexture`讓我們能夠得到整個卡透明而不會影響卡片內的個別元件的呈現我們在本指南稍後會看到。


## <a name="using-ccrendertexture"></a>使用 CCRenderTexture

既然我們已經識別了個別轉譯每個元件的問題，我們將開啟呈現`CCRenderTexture`和比較行為。

若要啟用轉譯`CCRenderTexture`，變更`userRenderTextures`變數加入`true`在`GameLayer.AddedToScene`:


```csharp
protected override void AddedToScene ()
{
    base.AddedToScene();
    GameView.Stats.Enabled = true;
    const bool useRenderTextures = true;
```


### <a name="card-draw-calls"></a>卡片的繪製呼叫

如果我們現在執行遊戲，我們會看到從 nineteen 至四個繪製呼叫 (reduced 從六到其中的每張卡片):

![](ccrendertexture-images/image6.png "如果現在執行遊戲時，繪製呼叫從減少 nineteen 至四個每張卡片從六，以其中一個")

如先前所述，減少這種可以遊戲畫面上的多個 visual 實體上有重大的影響。


### <a name="card-transparency"></a>卡片的透明度

一次`useRenderTextures`設為`true`，透明的卡片會以不同的方式呈現：

![](ccrendertexture-images/image7.png "一旦 useRenderTextures 設為 true，透明的卡片會以不同的方式呈現")

讓我們來比較使用轉譯紋理 （左） 與 （右） 不透明的機器人卡片：

![](ccrendertexture-images/image8.png "比較使用轉譯紋理 （左） 與 （右） 不透明的機器人卡片")

最顯著的差異是在詳細資料的文字 （黑色，而非淺灰色） 和機器人 sprite （而不是光深色和已去色）。


## <a name="ccrendertexture-details"></a>CCRenderTexture 詳細資料

既然我們已了解使用的優點`CCRenderTexture`，讓我們看看如何在中使用`Card`實體。

`CCRenderTexture`是畫布可轉譯的目標。 它有兩個主要差異，相較於遊戲的畫面：

1. `CCRenderTexture`保存中間的畫面格。 這表示`CCRenderTexture`需要只轉譯發生變更時。 在我們的案例，`Card`實體永遠不會變更，因此它只會轉譯一次。 如果有的話`Card`元件變更，則需要智慧卡重繪本身，以其`CCRenderTexture`。 比方說，如果攻擊時，就會變更 HP 值 （健全狀況點為單位），則智慧卡需要來呈現其本身以反映新的 「 HP 」 值。
1. `CCRenderTexture`像素尺寸不會繫結至螢幕。 A`CCRenderTexture`可以是大於或小於裝置解析度。 `Card`程式碼會建立`CCRenderTexture`使用其背景 sprite 的大小。 卡片包含的參考`CCRenderTexture`稱為`renderTexture`:


```csharp
CCRenderTexture renderTexture;
```

`renderTexture`執行個體維持`null`直到`UseRenderTexture`屬性會被指定為 true，其會呼叫`SwitchToRenderTexture`:


```csharp
private void SwitchToRenderTexture()
{
    // The card needs to be moved to the origin (0,0) so it's rendered on the render target. 
    // After it's rendered to the CCRenderTexture, it will be moved back to its old position
    var oldPosition = this.Position;
    // Make sure visuals are part of the card so they get rendered
    bool areVisualComponentsAlreadyAdded = this.Children != null && this.Children.Contains(visualComponents[0]);
    if (!areVisualComponentsAlreadyAdded)
    {
        // Temporarily add them so we can render the object:
        foreach (var component in visualComponents)
        {
            this.AddChild(component);
        }
    }
    // Create the render texture if it hasn't yet been made:
    if (renderTexture == null)
    {
        // Even though the game is zoomed in to create a pixellated look, we are using
        // high-resolution textures. Therefore, we want to have our canvas be 2x as big as 
        // the background so fonts don't appear pixellated
        var pixelResolution = background.ContentSize * 2;
        var unitResolution = background.ContentSize;
        renderTexture = new CCRenderTexture(unitResolution, pixelResolution);
        //renderTexture.Sprite.Scale = .5f;
    }
    // We don't want the render target to be a child of this when we update it:
    if (this.Children != null && this.Children.Contains(renderTexture.Sprite))
    {
        this.Children.Remove(renderTexture.Sprite);
    }
    // Move this instance back to the origin so it is rendered inside the render texture:
    this.Position = CCPoint.Zero;
    // Clears the CCRenderTexture
    renderTexture.BeginWithClear(CCColor4B.Transparent);
    // Visit renders this object and all of its children
    this.Visit();
    // Ends the rendering, which means the CCRenderTexture's Sprite can be used
    renderTexture.End();
    // We no longer want the individual components to be drawn, so remove them:
    foreach (var component in visualComponents)
    {
        this.RemoveChild(component);
    }
    // Move this back to its original position:
    this.Position = oldPosition;
    // add the render texture sprite to this:
    renderTexture.Sprite.AnchorPoint = CCPoint.Zero;
    this.AddChild(renderTexture.Sprite);
}
```

`SwitchToRenderTexture`紋理需要重新整理時，就可以呼叫方法。 它可以呼叫是否已使用智慧卡及其`CCRenderTexture`，或切換到`CCRenderTexture`第一次。

下列各節會探索`SwitchToRenderTexture`方法。 


### <a name="ccrendertexture-size"></a>CCRenderTexture 大小

CCRenderTexture 建構函式需要兩個集合的維度。 第一個控制項的大小`CCRenderTexture`當它繪製時，和第二個指定的像素寬度和高度其內容。 `Card`具現化實體及其`CCRenderTexture`使用背景[ContentSize](https://developer.xamarin.com/api/property/CocosSharp.CCSprite.ContentSize/)。 我們的遊戲`DesignResolution`為 512 的 384，如中所示`ViewController.LoadGame`在 iOS 上和`MainActivity.LoadGame`在 Android 上：


```csharp
int width = 512;
int height = 384;
// Set world dimensions
gameView.DesignResolution = new CCSizeI(width, height);
```

`CCRenderTexture`建構函式呼叫`background.ContentSize`的第一個參數，指出`CCRenderTexture`應該做為背景一樣大`CCSprite`。 因為卡片背景`CCSprite`是 200 像素高，卡片會佔用大約一半的垂直螢幕的高度。

第二個參數傳遞給`CCRenderTexture`建構函式指定的像素解析度`CCRenderTexture`。 中所述[CocosSharp 解析指南](~/graphics-games/cocossharp/resolutions.md)，遊戲的單位中的可檢視區域的高度與寬度通常不是像素的解析度螢幕的相同。 同樣地，CCRenderTexture 可能會使用較大的解析度超過它的大小，所以視覺效果會出現在高解析度裝置上更清晰。

像素解析度是兩次的大小以避免文字看起來不美觀 CCRenderTexture:


```csharp
var unitResolution = background.ContentSize;
var pixelResolution = background.ContentSize * 2;
renderTexture = new CCRenderTexture(unitResolution, pixelResolution);
```

若要比較，我們可以變更要比對 pixelResolution 值`background.ContentSize`（不含正在一倍），並比較結果： 


```csharp
var unitResolution = background.ContentSize;
var pixelResolution = background.ContentSize;
renderTexture = new CCRenderTexture(unitResolution, pixelResolution);
```

![](ccrendertexture-images/image9.png "若要比較，可以變更 pixelResolution 值以符合背景。ContentSize 沒有正在增加一倍，並比較結果")


### <a name="rendering-to-a-ccrendertexture"></a>轉譯為 CCRenderTexture

一般而言，CocosSharp 中的視覺物件不會明確地轉譯。 相反地，將視覺物件加入至`CCLayer`的一部分`CCScene`。 CocosSharp 自動呈現`CCScene`和其視覺階層中每個畫面，而不需要呼叫任何轉譯程式碼。 

相較之下，`CCRenderTexture`必須明確地繪製到。 這項轉譯可以分成三個步驟：

1. `CCRenderTexture.BeginWithClear` 呼叫時，表示所有後續的轉譯目標呼叫`CCRenderTexture`。
1. 物件繼承自`CCNode`(例如`Card`實體) 會呈現`CCRenderTexture`藉由呼叫`Visit`。
1. `CCRenderTexture.End` 呼叫時，表示該轉譯成`CCRenderTexture`完成。

任何數目的物件可以呈現`CCRenderTexture`之間其`Begin`和`End`呼叫。 在呈現前，所有必要的可見物件會加入做為子系：


```csharp
bool areVisualComponentsAlreadyAdded = this.Children != null && this.Children.Contains(visualComponents[0]);
if (!areVisualComponentsAlreadyAdded)
{
    // Temporarily add them so we can render the object:
    foreach (var component in visualComponents)
    {
        this.AddChild(component);
    }
}
```

`renderTexture`不應該屬於卡片呈現時，因此它會移除：


```csharp
// We don't want the render texture to be a child of the card 
// when we call Visit
if (this.Children != null && this.Children.Contains(renderTexture.Sprite))
{
    this.RemoveChild(renderTexture.Sprite);
}
```

現在`Card`執行個體可轉譯本身`CCRenderTexture`執行個體：


```csharp
// Clears the CCRenderTexture
renderTexture.BeginWithClear(CCColor4B.Transparent);
// Visit renders this object and all of its children
this.Visit();
// Ends the rendering, which means the CCRenderTexture's Sprite can be used
renderTexture.End();
```

轉譯完成之後，會移除個別的元件和`CCRenderTexture`重新加入。


```csharp
// We no longer want the individual components to be drawn, so remove them:
foreach (var component in visualComponents)
{
    this.RemoveChild(component);
}
// add the render target sprite to this:
this.AddChild(renderTexture.Sprite);
```

## <a name="summary"></a>總結

本指南涵蓋`CCRenderTexture`類別使用`Card`可用可回收的紙牌遊戲中的實體。 它示範了如何使用`CCRenderTexture`類別來改善畫面播放速率，並適當地實作整個實體的透明度。

雖然本指南使用，但`CCRenderTexture`包含在實體內，這個類別可以用來呈現多個實體，或甚至是整個`CCLayer`全螢幕的效果和效能增強功能的執行個體。

## <a name="related-links"></a>相關連結

- [CCRenderTexture API 參考](https://developer.xamarin.com/api/type/CocosSharp.CCRenderTexture/)
- [完整的專案 （範例）](https://developer.xamarin.com/samples/mobile/CCRenderTexture/)
