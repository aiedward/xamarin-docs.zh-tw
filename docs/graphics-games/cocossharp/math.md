---
title: 與 CocosSharp 2D 數學運算
description: 本指南涵蓋 2D 數學開發遊戲。 它使用 CocosSharp 示範如何執行一般的遊戲開發工作，並說明這些工作背後的數學運算。
ms.prod: xamarin
ms.assetid: 5C241AB4-F97E-4B61-B93C-F5D307BCD517
author: charlespetzold
ms.author: chape
ms.date: 03/27/2017
ms.openlocfilehash: 13279df69b7a22117c10d74f1a15c082aff13264
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
---
# <a name="2d-math-with-cocossharp"></a>與 CocosSharp 2D 數學運算

_本指南涵蓋 2D 數學開發遊戲。它使用 CocosSharp 示範如何執行一般的遊戲開發工作，並說明這些工作背後的數學運算。_

放置和移動程式碼的物件為各種規模的開發遊戲的核心部分。 定位和移動都需要使用數學是否遊戲需要移動沿著直線或將三角函數用於旋轉物件。 本文件將涵蓋下列主題：

 - 速度
 - 加速
 - 旋轉 CocosSharp 物件
 - 使用旋轉與速度

不具有強式數學背景，或使用者已長時間-忘記學校、 從這些主題的開發人員不需要擔心-這份文件會細分概念成接近大小的片段，並將會伴隨理論上與實用範例的說明。 簡單地說，這篇文章會回答歷數學學生:"當我實際上必須使用此內容？ 」


## <a name="requirements"></a>需求

雖然這份文件主要著重於 CocosSharp 的數學端，程式碼範例假設使用物件繼承表單`CCNode`。 此外，因為`CCNode`不包含值針對速度和速度，程式碼會假設其提供值，例如 VelocityX、 VelocityY、 AccelerationX 和 AccelerationY 實體使用。 如需有關實體的詳細資訊，請參閱我們的逐步解說[CocosSharp 中的實體](~/graphics-games/cocossharp/entities.md)。


## <a name="velocity"></a>速度

遊戲開發人員使用的詞彙*速度*來描述物件的方式移動 – 特別速度的項目移動，而且方向是移動。 

使用兩種類型的單位所定義的速度： 位置單位和時間單位。 例如，汽車的速度被定義為每小時的英哩或公里每小時。 遊戲開發人員通常使用像素為單位來定義如何快速物件秒移動。 例如，項目符號可能移動每秒的 300 像素的速度。 也就是說，如果 300 像素為單位，每秒在移動項目符號，然後它會有移 600 單位兩秒，和 900 單位三秒，以此類推。 一般來說，速度值*新增*物件的位置 （如我們會看到下面）。

雖然我們會使用速度解釋之單元的速度，但詞彙速度通常是指值的方向，獨立而詞彙速度是指速度和方向。 因此，指派項目符號的速度 （假設項目符號會包括所需內容的類別） 可能看起來像這樣：


```csharp
// This bullet is not moving horizontally, so set VelocityX to 0:
bulletInstance.VelocityX = 0;
// Positive Y is "up" so move the bullet up 300 units per second:
bulletInstance.VelocityY = 300;
```


### <a name="implementing-velocity"></a>實作速度

CocosSharp 未實作速度，因此需要移動物件必須實作自己的移動邏輯。 新遊戲開發人員實作速度通常犯讓其速度的畫面播放速率而定。 也就是下列*不正確實作*看似提供正確的結果，但會根據遊戲的畫面播放速率：

```csharp
// VelocityX and VelocityY will be added every time this code executes
this.PositionX += this.VelocityX;
this.PositionY += this.VelocityY;
```

如果該遊戲在更高的畫面播放速率 （例如每秒而不是每秒 30 個畫面的畫面格數 60） 執行時，物件會移動速度如果執行的速度較慢的畫面播放速率。 同樣地，如果無法處理框架做為高的畫面播放速率 （這可能因為使用裝置的資源的背景處理程序） 遊戲，遊戲會變慢。

若要將此列入考量，速度通常被實作使用時間值。 例如，如果`seconds`變數代表秒數 （或分數） 因為已套用最後一個時間速度，則下列程式碼會導致畫面播放速率不論一致移動之物件：

```csharp
// VelocityX and VelocityY will be added every time this code executes
this.PositionX += this.VelocityX * seconds;
this.PositionY += this.VelocityY * seconds;
```

請考慮遊戲可以執行較低的畫面播放速率會經常更新物件較少的位置。 因此，每個更新都會導致進一步比原本如果遊戲正在更新更頻繁地移動的物件。 `seconds`值帳戶為此，報告自上次更新後經過多少時間。

如需如何增加時間為基礎的範例，請參閱[涵蓋時間這個配方基礎移動](https://developer.xamarin.com/recipes/cross-platform/game_development/time_based_movement/)。


### <a name="calculating-positions-using-velocity"></a>計算位置使用速度

在物件會比較時間推移的後一些預測，或幫助微調物件的行為，而不需要執行遊戲，可以使用速度。 比方說，其實作的移動引發的項目符號的開發人員必須具現化後，設定項目符號的速度。 螢幕大小可以用來提供的基礎設定速度。 也就是說，如果開發人員知道項目符號應在 2 秒，移動螢幕的高度，則速度應該設定為除以 2 螢幕的高度。 如果 800 像素高的畫面，項目符號的速度會設定為 400 （也就是 800/2）。

同樣地，遊戲中邏輯可能需要計算物件大約需要多久到達目的地，提供其速度。 這可計算數再除以向旅行物件的速度所傳送的距離。 例如，下列程式碼顯示如何將文字指派給標籤會顯示多久直到飛彈達到它的目標：


```csharp
// We'll assume only the X axis for this example
float distanceX = target.PositionX - missile.PositionX;

float secondsToReachTarget = distanceX / missile.VelocityX;

label.Text = secondsToReachTarget + " seconds to reach target"; 
```


## <a name="acceleration"></a>加速

*加速*是在遊戲開發中，一般概念，而且與其速度共用許多相似之處。 物件是加速或變慢 （如何速度值隨著時間變更），以量化加速。 加速*新增*於速度，就像速度將加入至定位。 常見的應用程式的加速包括重力、 加速，汽車和引發其 thrusters 空間出貨。 

類似於速度，加速中定義的位置和時間單位。不過，加速的時間單位指*平方*單位，會反映加速定義的方式以數學方式。 遊戲加速亦即，通常以測量*每秒的像素為單位平方*。

如果物件具有 10 個單位，每秒平方 X 加速，這表示，其速度將會增加 10 每秒。 如果從陷於停頓後一秒就會在每第二個後, 兩個 10 個單位移動,，秒每 20 單位，第二個，依此類推。

在兩個維度中的加速需要的 X 和 Y 元件，因此可能，如下所示指定：


```csharp
// No horizontal acceleration:
icicle.AccelerationX = 0;
// Simulate gravity with Y acceleration. Negative Y is down, so assign a negative value:
icicle.AccelerationY = -50; 
```


### <a name="acceleration-vs-deceleration"></a>加速減速與

雖然加速和減速有時會在每一天語音區分，還有兩個無技術差異。 重力是會導致加速 force。 如果物件就會擲回向上然後重力會變慢 （減速），但一旦物件已停止上升並落在相同的方向為重力然後重力加速它 （加速）。 如下所示，加速應用程式是否正在套用相同方向或相反的方向移動都相同。 


### <a name="implementing-acceleration"></a>實作加速

實作時，加速是類似於速度 – 它不會自動實作 CocosSharp，並以時間為基礎的加速所需的實作 （而不是以框架為基礎的加速）。 因此簡單加速 （以及速度） 實作可能看起來像是：

```csharp
this.VelocityX += this.AccelerationX * seconds;
this.VelocityY += this.AccelerationY * seconds;
this.PositionX += this.VelocityX * seconds;
this.PositionY += this.VelocityY * seconds;
```

上述程式碼是什麼指*線性近似值*加速實作。 實際上，它會實作加速關閉相當程度的精確度，但不完全精確的模型，加速。 若要協助說明如何實作加速概念包含上方。

下列實作是以數學方式精確的應用程式的加速功能和速度：


```csharp
float halfSecondsSquared = (seconds * seconds) / 2.0f;

this.PositionX += 
    this.Velocity.X * seconds + this.AccelerationX * halfSecondsSquared;
this.PositionY += 
    this.Velocity.Y * seconds + this.AccelerationY * halfSecondsSquared;

this.VelocityX += this.AccelerationX * seconds;
this.VelocityY += this.AccelerationY * seconds; 
```

上述程式碼的最明顯差異是`halfSecondsSquared`變數，其使用方式套用加速定位。 數學原因是超出此教學課程的範圍，但開發人員想要這背後的數學運算可以找到更多資訊[討論整合加速。](http://www.cliffsnotes.com/math/calculus/calculus/integration/distance-velocity-and-acceleration)

實際影響`halfSecondSquare`是加速將以數學方式正確並如預期般運作不論畫面播放速率。 加速線性近似值受限於畫面播放速率 – 低的畫面播放速率卸除變得較不精確的近似值。 使用`halfSecondsSquared`保證程式碼的行為相同，不論畫面播放速率。


## <a name="angles-and-rotation"></a>角度和旋轉

視覺物件，例如`CCSprite`支援透過旋轉`Rotation`變數。 這可以被指派的值來設定其旋轉角度，以度為單位。 例如，下列程式碼顯示如何旋轉`CCSprite`執行個體：


```csharp
CCSprite unrotatedSprite = new CCSprite("star.png");
unrotatedSprite.IsAntialiased = false;
unrotatedSprite.PositionX = 100;
unrotatedSprite.PositionY = 100;
this.AddChild (unrotatedSprite);

CCSprite rotatedSprite = new CCSprite("star.png");
rotatedSprite.IsAntialiased = false;
// This sprite is moved to the right so it doesn’t overlap the first
rotatedSprite.PositionX = 130;
rotatedSprite.PositionY = 100;
rotatedSprite.Rotation = 45;
this.AddChild (rotatedSprite); 
```

這會產生下列項目：

![](math-images/image1.png "這會導致這個螢幕擷取畫面")

請注意，旋轉 45 度旋轉 （即由於歷史原因的旋轉套用方式以數學方式相反）：

![](math-images/image2.png "請注意，旋轉順時針旋轉 45 度這由於歷史原因是相反的旋轉套用方式以數學方式")

一般情況下 CocosSharp 和規則的數學旋轉可以視覺化，如下所示：

![](math-images/image3.png "一般情況下視覺化 CocosSharp 和規則的數學旋轉，像這樣")

![](math-images/image4.png "一般情況下視覺化 CocosSharp 和規則的數學旋轉，像這樣")

這個差別是很重要因為`System.Math`類別會使用逆時針旋轉，因此這個類別所熟悉的開發人員需要使用時，反轉角度`CCNode`執行個體。

我們應該注意，上述圖表會顯示循環以度為單位。不過，某些數學函數 (例如中的函式`System.Math`命名空間) 預期和傳回值中的*弧度*而不是度。 我們會探討如何將稍後在本指南中的兩個單元類型之間轉換。


### <a name="rotating-to-face-a-direction"></a>面對方向旋轉

如上所示，`CCSprite`可使用旋轉`Rotation`屬性。 `Rotation`屬性由`CCNode`(的基底類別`CCSprite`)，這表示旋轉可以套用至實體繼承自`CCNode`以及。 

某些遊戲需要讓面對目標旋轉的物件。 範例包括在播放程式目標或使用者碰觸螢幕的點朝飛行空間出貨疑難排解電腦控制敵人。 不過，旋轉值必須先計算基礎實體旋轉的位置和面對的目標位置上。

此程序需要幾個步驟：

 - 識別*位移*的目標。 位移的 X 和 Y 距離繞實體與目標實體參考。
 - 利用反正切三角函式 （在下面將詳細說明），計算從位移的角度。
 - 調整 0 度旋轉的實體點未旋轉的方向，以及指向之間的差異。
 - 調整的數學旋轉 （逆時針算起） 和 CocosSharp 旋轉 （順時針） 之間的差異。

下列函式 （假設實體中撰寫） 旋轉面對目標實體：


```csharp
// This function assumes that it is contained in a CCNode-inheriting object
public void FacePoint(float targetX, float targetY)
{
    // Calculate the offset - the target's position relative to "this"
    float xOffset = targetX - this.PositionX;
    float yOffset = targetY - this.PositionY;

    // Make sure the target isn't the same point as "this". If so,
    // then rotation cannot be calculated.
    if (targetX != this.PositionX || targetY != this.Position.Y)
    {

        // Call Atan2 to get the radians representing the angle from 
        // "this" to the target
        float radiansToTarget = (float)System.Math.Atan2 (yOffset, xOffset);

        // Since CCNode uses degrees for its rotation, we need to convert
        // from radians
        float degreesToTarget = CCMathHelper.ToDegrees (radiansToTarget);

        // The direction that the entity faces when unrotated. In this case
        // the entity is facing "up", which is 90 degrees 
        const float forwardAngle = 90;

        // Adjust the angle we want to rotate by subtracting the
        // forward angle.
        float adjustedForDirecitonFacing = degreesToTarget - forwardAngle;

        // Invert the angle since CocosSharp uses clockwise rotation
        float cocosSharpAngle = adjustedForDirecitonFacing * -1;

        // Finally assign the rotation
        this.Rotation = rotation = cocosSharpAngle;
    }
} 
```

上述程式碼無法用來讓它正面會面對使用者會接觸，如下所示的畫面上，點旋轉實體：


```csharp
private void HandleInput(System.Collections.Generic.List<CCTouch> touches, CCEvent touchEvent)
{
    if(touches.Count > 0)
    {
        CCTouch firstTouch = touches[0];
        FacePoint (firstTouch.Location.X, firstTouch.Location.Y);
    }
} 
```

此程式碼會產生下列行為：

![](math-images/image5.gif "此程式碼會產生此行為")

#### <a name="using-atan2-to-convert-offsets-to-angles"></a>要轉換使用 Atan2 位移到角度

`System.Math.Atan2` 可用來將位移轉換成角度。 函式名稱`Atan2`來自三角函式的反正切值。 "2"的後置詞從標準中區分此函式`Atan`函式，以完全符合反正切值的數學行為。 反正切值是函式會傳回介於-90 和 + 90 度 （或同等權限，以弧度為單位）。 許多應用程式，包括電腦遊戲，通常需要完整 360 度的值，所以`Math`類別包含`Atan2`來滿足此需求。

請注意，上述程式碼傳遞 Y 參數第一次，然後 X 的參數，呼叫時`Atan2`方法。 這會是回溯一般 X，Y 順序的位置座標。 如需詳細資訊[看到 Atan2 文件](https://msdn.microsoft.com/library/system.math.atan2(v=vs.110).aspx)。

它也是值得注意的傳回值從`Atan2`是以弧度為單位，也就是另一個用來測量角度的單位。 本指南未涵蓋的弧度為單位的詳細資料，但是請記住，在所有三角函數`System.Math`命名空間使用弧度為單位，因此任何值必須在 CocosSharp 物件上使用前先轉換為度。 可以找到更多有關弧度[弧度維基百科頁面中](http://en.wikipedia.org/wiki/Radian)。

#### <a name="forward-angle"></a>正向的角度

一次`FacePoint`方法會將角度轉換成弧度，它會定義`forwardAngle`值。 這個值表示它旋轉的值等於 0 時，實體面臨的角度。 在此範例中，我們假設使用數學旋轉 （相對於 CocosSharp 旋轉） 時，這會 90 度，實體往上，對著。 我們使用數學旋轉這裡因為我們尚未您尚未反轉 CocosSharp 旋轉。

下列範例示範具有哪些實體`forwardAngle`90 度的可能如下：

![](math-images/image6.png "這會顯示具有 forwardAngle 90 度的實體可能如下")


### <a name="angled-velocity"></a>角度的速度

到目前為止我們已經討論過如何將位移轉換成角度。 本節會另一種方式 – 角度，並將它轉換為 X 和 Y 值。 常見範例包括移動中遇到，方向或疑難排解遇到的出貨的方向移動項目符號空間出貨的汽車。 

就概念而言，速度計算方式是先定義所需的速度時未旋轉，然後輪替該實體對向的角度的速度。 為了說明這個概念，速度 （與加速） 可視為 2 維度*向量*（這通常繪製為箭號）。 X 的速度值的向量 = 100，而 Y = 0 可以以視覺化方式檢視，如下所示：

![](math-images/image7.png "X 的速度值的向量 = 100，而 Y = 0 可以以視覺化方式檢視，如下所示")

此向量可旋轉以產生新的速度。 例如，向量旋轉 45 度 （使用以逆時針方向旋轉） 會產生下列：

![](math-images/image8.png "在此使用以逆時針方向旋轉結果的 45 度旋轉的向量")

幸運的是，速度，加速，甚至是位置可以所有旋轉與相同的程式碼，不論如何套用的值。 下列的一般用途函式可用來旋轉的向量 CocosSharp 旋轉的值：


```csharp
// Rotates the argument vector by degrees specified by
// cocosSharpDegrees. In other words, the rotation
// value is expected to be clockwise.
// The vector parameter is modified, so it is both an in and out value
void RotateVector(ref CCVector2 vector, float cocosSharpDegrees)
{
    // Invert the rotation to get degrees as is normally
    // used in math (counterclockwise)
    float mathDegrees = -cocosSharpDegrees;

    // Convert the degrees to radians, as the System.Math
    // object expects arguments in radians
    float radians = CCMathHelper.ToRadians (mathDegrees);

    // Calculate the "up" and "right" vectors. This is essentially
    // a 2x2 matrix that we'll use to rotate the vector
    float xAxisXComponent = (float)System.Math.Cos (radians);
    float xAxisYComponent = (float)System.Math.Sin (radians);
    float yAxisXComponent = (float)System.Math.Cos (radians + CCMathHelper.Pi / 2.0f);
    float yAxisYComponent = (float)System.Math.Sin (radians + CCMathHelper.Pi / 2.0f);

    // Store the original vector values which will be used
    // below to perform the final operation of rotation.
    float originalX = vector.X;
    float originalY = vector.Y;

    // Use the axis values calculated above (the matrix values)
    // to rotate and assign the vector.
    vector.X = originalX * xAxisXComponent + originalY * yAxisXComponent;
    vector.Y = originalX * xAxisYComponent + originalY * yAxisYComponent;
} 
```

完全瞭解`RotateVector`方法需要熟悉餘弦和正弦三角函數以及某些線性代數，這已超出本文範圍。 不過，一旦實作`RotateVector`方法可以用來旋轉任何向量，以包括速度向量。 例如，下列程式碼可能會引發中指定的方向的項目符號`rotation`值：


```csharp
// Create a Bullet instance
Bullet newBullet = new Bullet();

// Define the velocity of the bullet when 
// rotation is 0
CCVector2 velocity = new CCVector2 (0, 100);

// Modify the velocity according to rotation
RotateVector (ref velocity, rotation);

// Assign the newBullet's velocity using the
// rotated vector
newBullet.VelocityX = velocity.X;
newBullet.VelocityY = velocity.Y;

// Set the bullet's rotation so it faces
// the direction that it's flying
newBullet.Rotation = rotation; 
```

此程式碼可能會產生類似：

![](math-images/image9.png "此程式碼可能會產生類似這個螢幕擷取畫面")


## <a name="summary"></a>總結

本指南涵蓋在 2D 遊戲開發 common 數學概念。 它會顯示如何指定及實作速度和加速功能，並涵蓋如何旋轉物件和任何方向移動的向量。
