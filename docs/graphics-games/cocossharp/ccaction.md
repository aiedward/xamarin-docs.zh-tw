---
title: 與 CCAction 建立動畫
description: CCAction 類別簡化了加入 CocosSharp 遊戲動畫。 這些動畫可用來實作功能，或更具吸引力。
ms.prod: xamarin
ms.assetid: 74DBD02A-6F10-4104-A61B-08CB49B733FB
author: charlespetzold
ms.author: chape
ms.date: 03/24/2017
ms.openlocfilehash: b6209816f741423f40945a0fe4391fe921cb35de
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
---
# <a name="animating-with-ccaction"></a>與 CCAction 建立動畫

_CCAction 類別簡化了加入 CocosSharp 遊戲動畫。這些動畫可用來實作功能，或更具吸引力。_

`CCAction` 是可以用來建立動畫 CocosSharp 物件的基底類別。 本指南涵蓋了內建`CCAction`實作一般工作，例如位置、 縮放和旋轉。 它也會查看如何建立自訂實作繼承自`CCAction`。

本指南會使用名為專案**ActionProject**其中[可以在這裡下載](https://developer.xamarin.com/samples/mobile/CCAction)。 本指南使用`CCDrawNode`類別在講述這個[CCDrawNode 與繪圖幾何](~/graphics-games/cocossharp/ccdrawnode.md)指南。


## <a name="running-the-actionproject"></a>執行 ActionProject

**ActionProject**是 CocosSharp 解決方案，可建置適用於 iOS 和 Android。 它可以用來說明如何使用程式碼範例`CCAction`類別，以及即時示範的常見`CCAction`實作。

ActionProject 執行時，顯示三個`CCLabel`左邊的螢幕和兩個所繪製的視覺物件的執行個體`CCDrawNode`檢視的各種動作的執行個體：

![](ccaction-images/image1.png "ActionProject 顯示於左側螢幕和兩個 CCDrawNode 執行個體所繪製的檢視的各種動作的視覺物件的三個 CCLabel 執行個體")

在左邊的標籤指出哪一種類型`CCAction`點選螢幕上時所建立。 根據預設，**位置**選取值，導致`CCMove`動作正在建立並套用至紅色圓圈：

![](ccaction-images/image2.gif "選取的位置值時，導致 CCMove 動作所建立並套用到紅色圓圈")

按一下左邊的標籤變更哪一種`CCAction`會在圓形上執行。 例如，按一下**位置**標籤會循環瀏覽可以變更的不同值：

![](ccaction-images/image3.gif "按一下位置標籤會循環不同的值可以變更顯示")

## <a name="common-variable-changing-ccaction-classes"></a>常用變數變更 CCAction 類別

**ActionProject**會使用下列`CCAction`-繼承類別，屬於 CocosSharp:

 - `CCMoveTo` -變更`CCNode`執行個體的`Position`屬性
 - `CCScaleTo` -變更`CCNode`執行個體的`Scale`屬性
 - `CCRotateTo` -變更`CCNode`執行個體的`Rotation`屬性

本指南是指做為這些動作*變數變更*，也就是說，它們會直接影響的變數`CCNode`新增至。 其他類型的動作稱為*easing*涵蓋在本指南稍後的動作。

**ActionProject**示範這些動作的目的是要修改一段時間的變數。 具體來說，這些`CCActions`建構函式接受兩個引數： 的時間長度才以及要指派的值。 下列程式碼片段顯示如何建立動作的三種類型：


```csharp
switch (VariableOptions [currentVariableIndex])
{
    case "Position":
        coreAction = new CCMoveTo(timeToTake, touch.Location);

        break;
    case "Scale":
        var distance = CCPoint.Distance (touch.Location, drawNodeRoot.Position);
        var desiredScale = distance / DefaultCircleRadius;
        coreAction = new CCScaleTo(timeToTake, desiredScale);

        break;
    case "Rotation":
        float differenceY = touch.Location.Y - drawNodeRoot.PositionY;
        float differenceX = touch.Location.X - drawNodeRoot.PositionX;

        float angleInDegrees = -1 * CCMathHelper.ToDegrees(
            (float)System.Math.Atan2(differenceY, differenceX));

        coreAction = new CCRotateTo (timeToTake, angleInDegrees);

        break; 
...
}
```

一旦建立動作之後，會新增到 CCNode，如下所示：


```csharp
nodeToAddTo.AddAction (coreAction); 
```

`AddAction` 啟動`CCAction`執行個體的行為，而且將會自動執行其邏輯之後畫面到完成為止。

每個類型上列結束以這個字*至*表示`CCAction`會修改`CCNode`，好讓動作完成時，引數的值代表最終狀態。 例如，建立`CCMoveTo`X 位置 = 100，而 Y = 200 會`CCNode`執行個體的`Position`設為 X = 100，Y = 200 的時間指定，而不論結尾`CCNode`開始位置的執行個體。

每個 「 目標 」 類別也有 「 由 」 版本，將引數將值加入至目前的值`CCNode`。 例如，建立`CCMoveBy`X 位置 = 100，而 Y = 200 會導致`CCNode`已從位置是否位於啟動動作時移到右邊 100 單位和總 200 個單位的執行個體。


## <a name="easing-actions"></a>加/減速動作

根據預設，變數變更動作將會執行*線性插補*– 動作將會移往的所需的值以常數速率。 如果插入*位置*線性增加，移動的物件將會立即啟動並停止在開頭和結尾動作、 移動和其速度會維持一致時執行動作。 

非線性插補是較不突兀，並加入波蘭文的項目，因此 CocosSharp 提供各種 easing 可以用來修改變數變更動作的動作。

在**ActionProject**範例中，我們就能切換這些類型的第二個標籤上的 加/減速動作 (預設為**<None>**):

![](ccaction-images/image4.gif "使用者可以在這些類型的加/減速動作，按一下第二個標籤之間切換")

加/減速的動作是特別強大，因為未繫結至任何特定的變數設定動作。 這表示相同的加/減速動作可用來指定位置、 旋轉、 小數位數或自訂動作 （如將會在本指南稍後所示）。

加/減速動作包裝變數設定的動作 (只要變數設定動作繼承自`CCFiniteTimeAction`) 接受變數設定動作做為其建構函式引數。

比方說，如果將標籤設定為**位置**， **CCEaseElastic**，則當觸控偵測到，就會執行下列程式碼 （請注意程式碼，已省略來反白顯示相關的程式行）：


```csharp
CCFiniteTimeAction coreAction = null; 
...
coreAction = new CCMoveTo(timeToTake, touch.Location); 
...
CCAction easing = null; 
...
easing = new CCEaseSineOut (coreAction); 
...
nodeToAddTo.AddAction (easing); 
```

應用程式所示，完全相同 easing 可以套用至其他變數設定的動作例如`CCRotateTo`:

![](ccaction-images/image5.gif "完全相同 easing 可以套用至其他變數設定動作，例如 CCRotateTo")


## <a name="easing-in-out-and-inout"></a>Easing In、 Out 和 InOut

所有加/減速動作都有`In`， `Out`，或`InOut`附加至加/減速型別。 這些授權條款時，請參考 easing 套用：`In`表示 easing 不會套用在開始時，`Out`在結束時，表示和`InOut`表示同時在開頭和結尾。

`In` Easing 動作會影響變數會套用到整個插補 （兩者的開頭和結尾），整個的方式，但通常加/減速動作的最容易辨認的特性會在開始進行。 同樣地，`Out`加/減速動作會依其行為的插補結尾加以區分。 例如，`CCEaseBounceOut`將會導致彈跳結尾動作的物件。


### <a name="out"></a>出

`Out` easing 通常適用於在插補結尾非常顯著的變更。 例如，`CCEaseExponentialOut`它接近目標值會變慢變更變數的變動率：

![](ccaction-images/image6.gif "CCEaseExponentialOut 會變慢變更變數的變更的速率，它接近目標值")


### <a name="in"></a>In

`In` easing 通常適用於最明顯的變更插補的開頭。 例如，`CCEaseExponentialIn`會移動速度更慢動作的開頭：

![](ccaction-images/image7.gif "CCEaseExponentialIn 會移動速度更慢動作的起始處")


### <a name="inout"></a>InOut

`InOut` 通常適用於同時在開頭和結尾非常顯著的變更。 `InOut` 緩和了是通常是對稱的。 例如，`CCEaseExponentialInOut`會緩慢移動開頭和結尾的動作：

![](ccaction-images/image8.gif "CCEaseExponentialInOut 會開頭和結束動作的移動速度很慢")


## <a name="implementing-a-custom-ccaction"></a>實作自訂 CCAction

所有的目前為止，我們所討論的類別都包含在 CocosSharp 提供一般功能。 自訂`CCAction`實作可以提供更大的彈性。 例如，`CCAction`可以使用這會控制經驗列的填滿的比例，以便體驗列非常平滑每當使用者得到的體驗。

`CCAction` 實作通常需要兩個類別：

 - `CCFiniteTimeAction` 實作 – 有限時間動作類別會負責啟動該動作。 它是會具現化的類別，而且是直接加入`CCNode`或加/減速的動作。 它必須具現化，並傳回`CCFiniteTimeActionState`，這將會執行更新。
 - `CCFiniteTimeActionState` 實作 – 在有限的時間執行的狀態類別都會負責更新動作中的變數。 它必須實作的更新函式，以指派的目標，根據時間值的值。 此類別未明確參考的外部`CCFiniteTimeAction`這會建立它。 只適用於 「 幕後"。

**ActionProject**提供自訂`CCFiniteTimeAction`實作呼叫`LineWidthAction,`用來調整頂端的紅色圓形繪製黃色線條的寬度。 請注意，是具現化，並傳回其唯一工作`LineWidthState`執行個體：


```csharp
public class LineWidthAction : CCFiniteTimeAction
{
    float endWidth;

    public LineWidthAction (float duration, float width) : base(duration)
    {
        endWidth = width;
    }

    public override CCFiniteTimeAction Reverse ()
    {
        throw new NotImplementedException ();
    }

    protected override CCActionState StartAction (CCNode target)
    {
        return new LineWidthState (this, target, endWidth);
    }
}
```

如上所述，`LineWidthState`執行這項工作指派該線路的`Width`屬性根據多少`time`已通過：


```csharp
public class LineWidthState : CCFiniteTimeActionState
{
    float deltaWidth;
    float startWidth;

    LineNode castedTarget;

    public LineWidthState(LineWidthAction action, CCNode target, float endWidth) : base(action, target)
    {
        castedTarget = target as LineNode;

        if (castedTarget == null)
        {
            throw new InvalidOperationException ("The argument target must be a LineNode");
        }

        startWidth = castedTarget.Width;
        deltaWidth = endWidth - startWidth;
    }

    public override void Update (float time)
    {
        castedTarget.Width = startWidth + deltaWidth * time;
    }
} 
```

可以與任何加/減速的動作，來變更線條的寬度，以各種方式結合 LineWidthAction，下列動畫所示：

![](ccaction-images/image9.gif "這個動畫所示，可以與任何加/減速的動作，來變更線條的寬度，以各種方式結合 LineWidthAction")


### <a name="interpolation-and-the-update-method"></a>插補，Update 方法

唯一的邏輯，除了上面的類別中儲存值位於`LineWidthState.Update`方法。 `startWidth`變數儲存的目標寬度`LineNode`開頭的動作，而`deltaWidth`變數儲存的值會隨採取的動作而改變。

所得到`time`變數的值是 0 時，我們可以看到目標`LineNode`會在其開始位置：


```csharp
castedTarget.Width = startWidth + deltaWidth * 0; 
```

同樣地，我們可以看到目標`LineNode`會在其目的地所得到的值為 1 的時間變數：


```csharp
castedTarget.Width = startWidth + deltaWidth * 1; 
```

`time`值通常會介於 0 和 1-但不是一定-和`Update`實作不應該假設這些界限。 某些加/減速方法 (例如`CCEaseBackIn`和`CCEaseBackOut`) 會提供在 0 到 1 範圍之外的時間值。


## <a name="conclusion"></a>結論

插補，以及減輕是很重要的一部分建立精美的遊戲，特別是在建立使用者介面。 本指南涵蓋如何使用`CCActions`來進行插補，例如位置和旋轉的標準值，以及自訂值。 `LineWidthState`和`LineWidthAction`類別示範如何實作自訂動作。

## <a name="related-links"></a>相關的連結

- [CCAction](https://developer.xamarin.com/api/type/CocosSharp.CCAction)
- [CCMoveTo](https://developer.xamarin.com/api/type/CocosSharp.CCMoveTo)
- [CCScaleTo](https://developer.xamarin.com/api/type/CocosSharp.CCScaleTo)
- [CCRotateTo](https://developer.xamarin.com/api/type/CocosSharp.CCRotateTo)
- [CCDrawNode](https://developer.xamarin.com/api/type/CocosSharp.CCDrawNode)
- [完整範例](https://developer.xamarin.com/samples/mobile/CCAction/)
