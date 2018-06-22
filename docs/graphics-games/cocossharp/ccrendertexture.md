---
title: 效能和使用 CCRenderTexture 的視覺效果
description: CCRenderTexture 可讓開發人員降低繪製呼叫，來改善的 CocosSharp 遊戲效能，而且可以用來建立視覺效果。 本指南隨附 CCRenderTexture 範例提供如何有效地使用這個類別的實際操作範例。
ms.prod: xamarin
ms.assetid: F02147C2-754B-4FB4-8BE0-8261F1C5F574
author: charlespetzold
ms.author: chape
ms.openlocfilehash: 82d39542b24c6b1669798a0b4e1fb14a81f6b44e
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
ms.locfileid: "33922518"
---
# <a name="performance-and-visual-effects-with-ccrendertexture"></a>效能和使用 CCRenderTexture 的視覺效果

_CCRenderTexture 可讓開發人員降低繪製呼叫，來改善的 CocosSharp 遊戲效能，而且可以用來建立視覺效果。本指南隨附 CCRenderTexture 範例提供如何有效地使用這個類別的實際操作範例。_

`CCRenderTexture`類別提供的功能來呈現為單一材質的多個 CocosSharp 物件。 一旦建立，`CCRenderTexture`可以使用執行個體，才能有效呈現圖形，以及實作視覺效果。 `CCRenderTexture` 允許多個物件轉譯為單一材質一次。 然後，該紋理可以重複使用的每個畫面格，減少的繪製呼叫次數總計。

本指南會檢視如何使用`CCRenderTexture`以改善效能的呈現牌可收集的紙牌遊戲 (CCG) 中的物件。 它也會示範如何`CCRenderTexture`可用來讓整個實體變成透明。 本指南參考了`CCRenderTexture`[範例專案](https://developer.xamarin.com/samples/mobile/CCRenderTexture/)。

![](ccrendertexture-images/image1.png "本指南參考了 CCRenderTexture 範例專案")


## <a name="card--a-typical-entity"></a>卡 – 一般實體

之前查看如何使用`CCRenderTexture`物件，我們會先瞭解自己與`Card`實體，我們將使用此專案來瀏覽`CCRenderTexture`類別。 `Card`類別是典型的實體，並遵循所述的實體模式[實體指南](~/graphics-games/cocossharp/entities.md)。 卡類別都具有其視覺化元件的所有 (的執行個體`CCSprite`和`CCLabel`) 做為欄位所列：


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

卡執行個體可以使用的呈現`CCRenderTexture`，或藉由個別繪製每個視覺元件。 雖然每個元件是一個獨立的物件，`CCNode`系統使用實體中的父代讓`Card`為單一物件 – 至少針對大部分的行為。 例如，如果`Card`實體重新定位、 調整大小或旋轉，則所有包含的視覺物件會受到影響，使卡片的似乎是單一物件。 若要查看卡片為單一物件的行為，我們可以修改`GameLayer.AddedToScene`方法，以設定`useRenderTextures`變數設為`false`:

    
```csharp
protected override void AddedToScene ()
{
    base.AddedToScene();
    GameView.Stats.Enabled = true;
    const bool useRenderTextures = false;
    ...
}
```

`GameLayer`程式碼不會移動每個視覺項目獨立作業，尚未內每個視覺項目`Card`實體正確的位置：

![](ccrendertexture-images/image1.png "GameLayer 程式碼不會獨立地移動每個視覺項目，但卡實體內每個視覺項目正確的位置")

此範例會編碼為公開每個視覺化元件呈現其本身時所發生的兩個問題：

- 這是因為多個繪製呼叫的效能可能會減損
- 當我們稍後會瀏覽特定視覺效果，如透明效果，也無法精確地說，實作


### <a name="card-draw-calls"></a>卡繪製呼叫

我們的程式碼是什麼可能會找到完整的簡化*可收集的撲克牌遊戲*(CCG) 例如"Magic:: 蒐集 」 或 「 Hearthstone"。 我們遊戲只一次顯示三張牌，並具有少數的可能單位 （藍、 綠和橙色）。 相反地，完整的遊戲可能螢幕上已經超過 20 種卡片，在指定的時間，而且播放程式可能有數百個卡，以建立其 deck 時選擇的。 即使我們遊戲不目前會發生效能問題，可能會有類似的實作的完整遊戲。

CocosSharp 提供一些深入藉由公開繪製呼叫的呈現效能執行個別畫面格。 我們`GameLayer.AddedToScene`方法會設定`GameView.Stats.Enabled`至`true`，並產生顯示在畫面的左下方的效能資訊：

![](ccrendertexture-images/image2.png "GameLayer.AddedToScene 方法設定為 true，導致效能資訊顯示在畫面的左下方 GameView.Stats.Enabled")

請注意，儘管有三張畫面上，我們有 nineteen （每個卡導致六個繪製呼叫，效能資訊帳戶顯示更多個文字） 的繪製呼叫。 繪製呼叫，對遊戲的效能有顯著的影響 CocosSharp 會提供數種方式，以減少它們。 中所述的其中一種技術是[CCSpriteSheet 指南](~/graphics-games/cocossharp/ccspritesheet.md)。 另一種技巧是使用`CCRenderTexture`來減少因為我們將檢驗本指南中的下一個呼叫，每個實體。


### <a name="card-transparency"></a>卡透明度

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

請注意，使用的 setter 支援呈現材質或個別呈現每個元件。 若要查看其效果，請變更`opacity`值設定為`127`（大約一半的不透明） 中`GameLayer.AddedToScene`以產生每個元件皆`Opacity`值`127`:


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

遊戲現在會呈現具有某些透明度，讓他們因為背景是黑色看起來更暗卡片：

![](ccrendertexture-images/image3.png "遊戲現在會呈現具有某些透明度，讓他們因為背景是黑色看起來更暗卡片")

第一眼看起來如同我們的卡片已正確進行透明。 不過，螢幕擷取畫面會顯示一些 visual 的問題。

因為我們背景為黑色，我們希望我們卡的每個部分會變成暗由於透明度。 也就是說，變得越透明卡片、 深得。 在不透明度 0，`Card`會完全透明 （完全黑色）。 不過，我們的卡片的某些部分不變得較暗時不透明度已變更為`127`。 更糟的是，我們的卡片的某些部分實際成為亮當它們變得越透明。 讓我們看看我們的卡片的黑色的組件*之前*它們是透明 – 特別的詳細資料的文字和黑色的外框怪物圖形周圍。 如果我們將這些並存，我們可以看到套用透明度的影響：

![](ccrendertexture-images/image4.png "如果並排放置，可以看到套用透明度的影響")

如上面所述，智慧卡的所有部分應該變都得較暗時變得更透明，但在許多方面這不是這樣：

- 機器人大綱變淡時 （會出現從黑色灰色）
- 描述文字變淡時 （會出現從黑色灰色）
- 機器人綠色部分會變成較不飽和，但不會成為深

為協助視覺化為何發生這種情況，我們需要記住每個視覺化元件是單獨繪製、 每個部分透明。 繪製的第一個視覺化元件是卡片的背景。 後續透明的項目會繪製在卡片之上，且將會受到卡背景。 如果我們從我們的卡片移除一些文字，並向下移動機器人圖形，我們可以看到卡背景如何影響機器人。 請注意機器人，可能會發生的橙色行頂端的方塊，並且會繪製深機器人重疊的卡片中央藍色等量磁碟區的區域：

![](ccrendertexture-images/image5.png "請注意機器人，可能會發生的橙色行頂端的方塊，並且會繪製深機器人重疊的卡片中央藍色等量磁碟區的區域")

使用`CCRenderTexture`讓我們可以讓整個卡透明而不會影響卡內的個別元件的呈現我們在本指南稍後將會看到。


## <a name="using-ccrendertexture"></a>使用 CCRenderTexture

既然我們已經識別了個別呈現每個元件的問題，我們將會開啟呈現`CCRenderTexture`和比較行為。

若要啟用轉譯成`CCRenderTexture`，變更`userRenderTextures`變數設為`true`中`GameLayer.AddedToScene`:


```csharp
protected override void AddedToScene ()
{
    base.AddedToScene();
    GameView.Stats.Enabled = true;
    const bool useRenderTextures = true;
```


### <a name="card-draw-calls"></a>卡繪製呼叫

如果我們現在執行遊戲時，我們會看到從 nineteen 到四個繪製呼叫 （從六，以其中一個減少每一張卡片）：

![](ccrendertexture-images/image6.png "如果遊戲現在執行時，繪製呼叫降低從 nineteen 為 4 張卡片從六，以其中一個")

如先前所述，減少這種可以遊戲更多視覺上的實體螢幕上有顯著的影響。


### <a name="card-transparency"></a>卡透明度

一次`useRenderTextures`設`true`，透明的卡片會以不同的方式轉譯：

![](ccrendertexture-images/image7.png "一旦 useRenderTextures 設為 true，透明的卡片會以不同的方式轉譯")

讓我們來比較使用呈現紋理 （左） 與 （右） 的不透明機器人卡片：

![](ccrendertexture-images/image8.png "比較使用呈現紋理 （左） 與 （右） 不透明機器人卡")

最顯著的差異出現在詳細資料文字 （而不是淺灰色的黑色） 及機器人精靈 （而不是光線暗和 desaturated）。


## <a name="ccrendertexture-details"></a>CCRenderTexture 詳細資料

既然我們已看到使用的優點`CCRenderTexture`，讓我們看看如何在中使用`Card`實體。

`CCRenderTexture`是可做為目標的呈現在畫布。 它有兩個主要差異，相較於遊戲的畫面：

1. `CCRenderTexture`持續發生此框架。 這表示`CCRenderTexture`需要只轉譯發生變更時。 在此案例中`Card`實體永遠不會變更，因此它只會轉譯一次。 如果有任何`Card`元件變更時，則需要智慧卡重繪其本身，以其`CCRenderTexture`。 比方說，如果 HP 值 （健全狀況點） 變更時遭受攻擊，智慧卡會需要呈現其本身，以反映新的 HP 值。
1. `CCRenderTexture`像素維度不會繫結至螢幕。 A`CCRenderTexture`可以是大於或小於裝置解析度。 `Card`程式碼會建立`CCRenderTexture`使用其背景精靈的大小。 卡片中包含的參考`CCRenderTexture`呼叫`renderTexture`:


```csharp
CCRenderTexture renderTexture;
```

`renderTexture`執行個體維持`null`直到`UseRenderTexture`屬性會被指派為 true，而它會呼叫`SwitchToRenderTexture`:


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

`SwitchToRenderTexture`紋理需要重新整理時，可以呼叫方法。 您可以呼叫是否已使用智慧卡及其`CCRenderTexture`，或切換到`CCRenderTexture`第一次。

下列各節瀏覽`SwitchToRenderTexture`方法。 


### <a name="ccrendertexture-size"></a>CCRenderTexture 大小

CCRenderTexture 建構函式需要兩個集合的維度。 第一個控制項的大小`CCRenderTexture`當繪製，而第二個指定的像素寬度和高度的內容。 `Card`實體會具現化其`CCRenderTexture`使用背景[ContentSize](https://developer.xamarin.com/api/property/CocosSharp.CCSprite.ContentSize/)。 我們遊戲的`DesignResolution`512 由 384，如下所示的`ViewController.LoadGame`在 iOS 上和`MainActivity.LoadGame`在 Android 上：


```csharp
int width = 512;
int height = 384;
// Set world dimensions
gameView.DesignResolution = new CCSizeI(width, height);
```

`CCRenderTexture`建構函式呼叫`background.ContentSize`的第一個參數，表示`CCRenderTexture`應該做為背景一樣大`CCSprite`。 因為卡背景`CCSprite`是 200 像素高，智慧卡會佔用大約一半的垂直螢幕的高度。

第二個參數傳遞至`CCRenderTexture`建構函式指定的像素解析度`CCRenderTexture`。 中所述[CocosSharp 解析指南](~/graphics-games/cocossharp/resolutions.md)，遊戲單位可檢視區域的高度與寬度通常不是螢幕的像素解析度相同。 同樣地，CCRenderTexture 可能會使用超過它的大小較大的解析度，因此視覺效果會出現較清晰高解析度裝置上。

像素解析度是兩次的大小以避免文字看起來不太美觀 CCRenderTexture:


```csharp
var unitResolution = background.ContentSize;
var pixelResolution = background.ContentSize * 2;
renderTexture = new CCRenderTexture(unitResolution, pixelResolution);
```

若要比較，我們可以變更要符合的 pixelResolution 值`background.ContentSize`（不含加倍），並比較結果： 


```csharp
var unitResolution = background.ContentSize;
var pixelResolution = background.ContentSize;
renderTexture = new CCRenderTexture(unitResolution, pixelResolution);
```

![](ccrendertexture-images/image9.png "若要比較，可以變更 pixelResolution 值相符。不含正在加倍 contentSize 並比較結果")


### <a name="rendering-to-a-ccrendertexture"></a>轉譯為 CCRenderTexture

一般而言，CocosSharp 中的視覺物件不會明確地轉譯。 相反地，將視覺物件加入至`CCLayer`屬於`CCScene`。 自動轉譯 CocosSharp`CCScene`和其視覺階層中每一個框架不會呼叫任何轉譯程式碼。 

相反地，`CCRenderTexture`必須明確地要繪製成。 此轉譯可以分成三個步驟：

1. `CCRenderTexture.BeginWithClear` 呼叫時，表示所有後續的轉譯目標呼叫`CCRenderTexture`。
1. 物件繼承自`CCNode`(像是`Card`實體) 會轉譯為`CCRenderTexture`藉由呼叫`Visit`。
1. `CCRenderTexture.End` 呼叫時，表示該轉譯成`CCRenderTexture`已完成。

任何數目的物件可以轉譯為`CCRenderTexture`之間其`Begin`和`End`呼叫。 在呈現前，所有可見的必要物件會加入做為子系：


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

`renderTexture`不應該屬於智慧卡呈現時，因此它會移除：


```csharp
// We don't want the render texture to be a child of the card 
// when we call Visit
if (this.Children != null && this.Children.Contains(renderTexture.Sprite))
{
    this.RemoveChild(renderTexture.Sprite);
}
```

現在`Card`可以轉譯執行個體本身`CCRenderTexture`執行個體：


```csharp
// Clears the CCRenderTexture
renderTexture.BeginWithClear(CCColor4B.Transparent);
// Visit renders this object and all of its children
this.Visit();
// Ends the rendering, which means the CCRenderTexture's Sprite can be used
renderTexture.End();
```

一旦完成轉譯時，移除個別的元件和`CCRenderTexture`重新加入。


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

本指南涵蓋`CCRenderTexture`類別使用`Card`可回收紙牌遊戲中可用的實體。 它示範了如何使用`CCRenderTexture`類別來改善畫面播放速率，並適當地實作整個實體的透明度。

雖然本指南使用`CCRenderTexture`包含在實體內，這個類別可以用來呈現多個實體，或甚至整個`CCLayer`全螢幕的效果和效能增強功能的執行個體。

## <a name="related-links"></a>相關連結

- [CCRenderTexture API 參考](https://developer.xamarin.com/api/type/CocosSharp.CCRenderTexture/)
- [完整的專案 （範例）](https://developer.xamarin.com/samples/mobile/CCRenderTexture/)
