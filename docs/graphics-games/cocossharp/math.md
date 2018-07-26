---
title: 使用 CocosSharp 的 2D 數學運算
description: 本指南涵蓋開發遊戲的 2D 數學運算。 它使用 CocosSharp 示範如何執行一般的遊戲開發工作，並說明這些工作背後的數學運算。
ms.prod: xamarin
ms.assetid: 5C241AB4-F97E-4B61-B93C-F5D307BCD517
author: charlespetzold
ms.author: chape
ms.date: 03/27/2017
ms.openlocfilehash: 63c722b74c7dc7e034475e539f38204aca87763e
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242049"
---
# <a name="2d-math-with-cocossharp"></a>使用 CocosSharp 的 2D 數學運算

_本指南涵蓋開發遊戲的 2D 數學運算。它使用 CocosSharp 示範如何執行一般的遊戲開發工作，並說明這些工作背後的數學運算。_

若要定位和移動程式碼的物件。 規模的開發遊戲的核心組件 定位和移動需要數學運算，使用，是否遊戲需要移動物件沿著一條直線或將旋轉三角函數。 本文件將涵蓋下列主題：

 - 速度
 - 加速
 - 旋轉 CocosSharp 物件
 - 循環使用速度

沒有強式數學背景，或使用者長時間-忘了從學校，這些主題的開發人員不需要擔心-這份文件會細分概念位元組大小的項目，並會伴隨理論說明實用的範例。 簡單地說，這篇文章將回答讀者的數學學生方面的問題: 「 時將我實際上需要使用此項目？ 」


## <a name="requirements"></a>需求

雖然本文主要著重在 CocosSharp 數學的一端，程式碼範例假設使用物件繼承表單`CCNode`。 此外，自`CCNode`不包含值針對速度和加速，程式碼會假設其提供值，例如 VelocityX、 VelocityY、 AccelerationX 和 AccelerationY 實體使用。 如需有關實體的詳細資訊，請參閱我們的逐步解說[CocosSharp 中的實體](~/graphics-games/cocossharp/entities.md)。


## <a name="velocity"></a>速度

遊戲開發人員使用的字詞*速度*來描述物件的方式移動 – 特別速度的項目移動，而且方向，就會移動。 

使用兩種類型的單位來定義速度： 位置單位和時間單位。 比方說，汽車的速度被定義為每小時或每小時公里。 遊戲的開發人員通常使用像素為單位來定義如何快速物件每秒移動。 比方說，可能會每秒的 300 像素的速度移動項目符號。 也就是如果 300 像素，每秒移動項目符號，然後它會有移 600 單位兩秒，和 900 單位在 3 秒，依此類推。 一般來說，速度值*加入*物件的位置 （如我們所見下方）。

雖然我們用來說明速度的單位的速度，詞彙速度通常是指值的方向，獨立而詞彙速度指的速度和方向。 因此，指派 （假設項目符號類別，其中包含必要的屬性） 的項目符號的速度可能會看起來像這樣：


```csharp
// This bullet is not moving horizontally, so set VelocityX to 0:
bulletInstance.VelocityX = 0;
// Positive Y is "up" so move the bullet up 300 units per second:
bulletInstance.VelocityY = 300;
```


### <a name="implementing-velocity"></a>實作的速度

CocosSharp 未實作速度，因此需要移動的物件必須實作它們自己的移動邏輯。 新的遊戲程式開發人員實作速度通常犯讓其速度取決於畫面播放速率。 也就是下列*實作不正確*就似乎會提供正確的結果，但會根據該遊戲的畫面播放速率：

```csharp
// VelocityX and VelocityY will be added every time this code executes
this.PositionX += this.VelocityX;
this.PositionY += this.VelocityY;
```

如果遊戲在更高的畫面播放速率 （例如每秒，而不是每秒 30 畫面格數的 60 個畫面） 執行時，物件會將比如果執行速度較慢的畫面播放速率更快。 同樣地，如果無法處理的畫面播放速率 （這可能會因使用裝置的資源的背景處理程序） 的高階層的畫面格在遊戲，遊戲會變慢。

為了說明這一點，速度通常會實作使用時間值。 例如，如果`seconds`變數代表秒數 （或片段） 由於已套用的最後一個時間的速度，則下列程式碼會產生具有一致的移動，不論畫面播放速率物件：

```csharp
// VelocityX and VelocityY will be added every time this code executes
this.PositionX += this.VelocityX * seconds;
this.PositionY += this.VelocityY * seconds;
```

請考慮在較低的畫面播放速率執行的遊戲會經常更新物件較少的位置。 因此，每個更新都會導致進一步不像如果遊戲在更新更頻繁地移動的物件。 `seconds`值帳戶為此，報告自上次更新經過多少時間。

如需如何將以時間為基礎的移動的範例，請參閱[涵蓋時間此配方會根據移動](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/game_development/time_based_movement)。


### <a name="calculating-positions-using-velocity"></a>計算使用速度的位置

可以用速度，預測一定程度的時間經過之後中會到物件，或協助調整物件的行為，而不需要執行遊戲。 比方說，開發人員實作一個引發的項目符號的移動必須具現化之後，設定項目符號的速度。 螢幕大小可用來提供基礎設定速度。 也就是開發人員知道，如果項目符號應在 2 秒，移動螢幕的高度，則速度應該設定為除以 2 螢幕的高度。 如果畫面 800 像素高，則項目符號的速度會設為 400 （也就是 800/2）。

同樣地，在遊戲邏輯可能需要計算物件需要多久到達目的地，指定其速度。 這可以計算是將出差在外之物件的速度所傳送的距離。 例如，下列程式碼顯示如何將文字指派給標籤會顯示多久直到飛彈達到其目標：


```csharp
// We'll assume only the X axis for this example
float distanceX = target.PositionX - missile.PositionX;

float secondsToReachTarget = distanceX / missile.VelocityX;

label.Text = secondsToReachTarget + " seconds to reach target"; 
```


## <a name="acceleration"></a>加速

*加速*是遊戲開發，常用的概念和其速度與共用許多相似之處。 物件是加速或變慢 （如何速度值隨著時間變更），以量化加速。 加速*加入*於速度，就像速度將加入至的位置。 常見的應用程式的加速包括重力、 加速、 汽車和引發其 thrusters 空間出貨。 

類似於速度，加速中定義的位置和時間單位;不過，加速的時間單位指*平方*單元，會反映如何加速以數學方式定義。 也就是遊戲加速通常長達*每秒的像素平方*。

如果物件有每秒平方的 10 個單位 X 加速，這表示，其速度將會增加每秒 10。 如果從停滯後一秒就會在 10 個單位每秒後, 兩個移動,，秒每 20 個單位，第二個，依此類推。

在兩個維度中的加速需要 X 和 Y 元件，因此它可能會如下所示進行指派：


```csharp
// No horizontal acceleration:
icicle.AccelerationX = 0;
// Simulate gravity with Y acceleration. Negative Y is down, so assign a negative value:
icicle.AccelerationY = -50; 
```


### <a name="acceleration-vs-deceleration"></a>加速與減速

雖然加速與減速有時會區別在日常語音中，沒有任何技術的差異，兩者之間。 重力是強制會導致加速。 如果物件就會擲回向上然後重力會變慢 （減速），但一旦物件已停止持續增加，因此會落在相同的方向為重力然後重力加速它 （加速）。 如下所示，加速應用程式是否正在套用相同的方向或是相反方向的移動都相同。 


### <a name="implementing-acceleration"></a>實作加速

實作時，加速是類似於速度 – 它不會自動實作 CocosSharp，並以時間為基礎的加速 （相對於畫面格為基礎的加速） 所需的實作。 因此 （以及速度） 的簡單加速實作可能看起來像：

```csharp
this.VelocityX += this.AccelerationX * seconds;
this.VelocityY += this.AccelerationY * seconds;
this.PositionX += this.VelocityX * seconds;
this.PositionY += this.VelocityY * seconds;
```

上述程式碼是什麼指*線性近似值*加速實作。 實際上，它會加速實作關閉相當高的精確度，但不完全精確的模型的加速。 它包含上述來說明如何實作加速的概念。

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

上述程式碼的最明顯差異是`halfSecondsSquared`變數，要套用來定位的加速其使用方式。 數學的原因是超出本教學課程的範圍，但這背後的數學運算有興趣的開發人員可以找到詳細資訊[此整合加速進行的討論。](http://www.cliffsnotes.com/math/calculus/calculus/integration/distance-velocity-and-acceleration)

實際影響`halfSecondSquare`是加速，將行為以數學方式精確且可預測的方式，不論畫面播放速率。 加速線性近似值受限於畫面播放速率 – 越低畫面播放速率卸除變得較不精確的近似值。 使用`halfSecondsSquared`保證程式碼的行為相同，不論畫面播放速率。


## <a name="angles-and-rotation"></a>角度和旋轉

視覺物件這類`CCSprite`支援透過旋轉`Rotation`變數。 這可以指派給值以設定其旋轉的度數。 例如，下列程式碼顯示如何旋轉`CCSprite`執行個體：


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

![](math-images/image1.png "這會導致此螢幕擷取畫面")

請注意，旋轉 45 度順時針旋轉 （這歷史的原因是相反的方式旋轉會以數學方式套用）：

![](math-images/image2.png "請注意旋轉是順時針旋轉 45 度的歷史的原因是相反的方式旋轉會以數學方式套用")

一般旋轉 CocosSharp 和一般的數學可以視覺化，如下所示：

![](math-images/image3.png "一般情況下視覺化旋轉 CocosSharp 和一般的數學，像這樣")

![](math-images/image4.png "一般情況下視覺化旋轉 CocosSharp 和一般的數學，像這樣")

這個差別是很重要因為`System.Math`類別會使用逆時針旋轉，因此熟悉這個類別的開發人員需要使用時，反轉角度`CCNode`執行個體。

我們應該注意到，上述圖表會顯示旋轉以度為單位;不過，某些數學函數 (例如中的函式`System.Math`命名空間) 預期並傳回值*弧度*而不是度。 我們將探討如何將稍後在本指南中的兩個單位類型之間轉換。


### <a name="rotating-to-face-a-direction"></a>面對方向旋轉

如上所示，`CCSprite`可以使用旋轉`Rotation`屬性。 `Rotation`所提供屬性`CCNode`(的基底類別`CCSprite`)，這表示旋轉可以套用到實體繼承自`CCNode`以及。 

某些遊戲需要面對目標，因此要旋轉的物件。 範例包括電腦控制的敵人，著眼於播放程式的目標，或針對使用者會觸碰螢幕的點要飛往空間出貨。 不過，旋轉值必須先計算根據旋轉的實體位置和要面對的目標位置。

此程序需要幾個步驟：

 - 用來識別*位移*的目標。 位移是指 X 和 Y 之間的距離旋轉的實體與目標實體。
 - 使用 （以下將詳細說明） 的反正切值三角函式，計算從位移的角度。
 - 調整指向右邊和方向指的旋轉的實體時未旋轉 0 度之間的差異。
 - 調整的數學旋轉 （逆時針算起） 和 CocosSharp 旋轉 （順時針） 之間的差異。

下列函式 （假設寫入實體中） 會旋轉面對目標實體：


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

上述程式碼可用來讓它面向其中使用者會觸碰 [] 畫面，如下所示的點旋轉實體：


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

`System.Math.Atan2` 可用來將位移轉換成角度。 函式名稱`Atan2`來自三角函式的反正切值。 "2"的後置詞從標準中區分此函式`Atan`函式，這完全符合數學行為的反正切值。 反正切值是函式會傳回介於-90 和 + 90 度 （或同等權限，以弧度為單位）。 許多應用程式，包括電腦遊戲，通常需要完整 360 度的值，因此`Math`類別包含`Atan2`來滿足這項需求。

請注意，上述程式碼傳遞 Y 參數第一次，然後 X 參數，呼叫時`Atan2`方法。 回溯是從一般的 X，Y 排序的位置座標。 如需詳細資訊[，請參閱 Atan2](https://msdn.microsoft.com/library/system.math.atan2(v=vs.110).aspx)。

也很值得一提的傳回值從`Atan2`是以弧度為單位，也就是另一個用來測量角度的單位。 本指南未涵蓋的弧度為單位，詳細資料，但請注意，在所有三角函數`System.Math`命名空間會使用弧度為單位，因此任何值之前必須轉換為度 CocosSharp 物件上使用。 可以找到更多有關弧度[弧度維基百科頁面在](http://en.wikipedia.org/wiki/Radian)。

#### <a name="forward-angle"></a>正向的角度

一次`FacePoint`方法將轉換為弧度的角度，它會定義`forwardAngle`值。 這個值表示它旋轉的值等於 0 時，實體面向的角度。 在此範例中，我們假設的實體面向向上周游使用數學的旋轉 （相對於 CocosSharp 旋轉） 時，這會 90 度。 我們會使用數學旋轉這裡因為我們還未反轉 CocosSharp 旋轉。

下圖顯示具有哪些實體`forwardAngle`90 度的可能看起來像：

![](math-images/image6.png "這會顯示具有 forwardAngle 90 度的實體可能會如下所示")


### <a name="angled-velocity"></a>角度的速度

到目前為止我們已探討如何將位移轉換成角度。 此區段會是 – 角度，並將它轉換為 X 和 Y 值。 常見範例包括移動的方向，它會對向，或疑難排解項目符號會在面向太空船的方向將移空間出貨一輛車。 

就概念而言，可以先定義所需的速度時未旋轉，然後輪替該速度在面對實體的角度來計算速度。 為了協助說明這個概念，速度 （和加速） 可視覺化為 2d*向量*（這通常是繪製為箭號）。 向量中供 X 的速度值 = 100，而 Y = 0 可視覺化，如下所示：

![](math-images/image7.png "向量中供 X 的速度值 = 100，而 Y = 0 可視覺化，如下所示")

這個向量可以旋轉以產生新的速度。 例如，（使用以逆時針方向旋轉） 的 45 度旋轉的向量會產生下列：

![](math-images/image8.png "在此使用以逆時針方向旋轉結果的 45 度旋轉的向量")

幸運的是，速度、 加速，以及甚至是位置所有可旋轉以相同的程式碼，不論值的套用方式。 下列的一般用途函式可以用來旋轉的向量，依據 CocosSharp 旋轉值：


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

完全掌握`RotateVector`方法需要熟悉一些線性代數 」，已超出本文的範圍以及餘弦和正弦三角函式。 不過，一次實作`RotateVector`方法可用來旋轉包括速度向量的任何向量。 例如，下列程式碼可能會引發指定的方向中的項目符號`rotation`值：


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

本指南涵蓋一般的數學概念 2D 的遊戲開發人員。 它會顯示如何指派和實作速度和加速功能，並涵蓋如何旋轉物件和向量中任何方向移動。
