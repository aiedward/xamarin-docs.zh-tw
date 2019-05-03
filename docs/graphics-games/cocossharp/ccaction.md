---
title: 使用 ccaction 建立動畫
description: Ccaction 建立動畫類別可簡化新增動畫 CocosSharp 的遊戲。 這些動畫可用來實作功能，或更具吸引力。
ms.prod: xamarin
ms.assetid: 74DBD02A-6F10-4104-A61B-08CB49B733FB
author: conceptdev
ms.author: crdun
ms.date: 03/24/2017
ms.openlocfilehash: c486bb2e78579360e0f935219cd82958fedee34b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61357635"
---
# <a name="animating-with-ccaction"></a>使用 ccaction 建立動畫

_Ccaction 建立動畫類別可簡化新增動畫 CocosSharp 的遊戲。這些動畫可用來實作功能，或更具吸引力。_

`CCAction` 是可用來以動畫顯示 CocosSharp 物件的基底類別。 本指南涵蓋內建`CCAction`常見工作，例如位置、 縮放和旋轉的實作。 也會介紹如何建立自訂的實作，藉由繼承自`CCAction`。

本指南使用的專案，稱為**ActionProject**這[可以在這裡下載](https://developer.xamarin.com/samples/mobile/CCAction)。 本指南會使用`CCDrawNode`類別，在講述這個[使用 ccdrawnode 建立繪圖幾何圖形](~/graphics-games/cocossharp/ccdrawnode.md)指南。


## <a name="running-the-actionproject"></a>執行 ActionProject

**ActionProject**是 CocosSharp 解決方案，可以建置適用於 iOS 和 Android。 它提供兩種形式的使用方式的程式碼範例`CCAction`類別，以及常見的即時示範`CCAction`實作。

執行時，ActionProject 會顯示三`CCLabel`左邊的畫面和繪製兩個視覺物件的執行個體`CCDrawNode`檢視的各種動作的執行個體：

![](ccaction-images/image1.png "ActionProject 顯示螢幕的左邊繪製兩個 ccdrawnode 建立執行個體檢視的各種動作的視覺物件上的三個 CCLabel 執行個體")

在左邊的標籤指出哪一種類型`CCAction`點選螢幕上時所建立。 根據預設，**位置**選取值，導致`CCMove`動作建立並套用到紅色圓圈：

![](ccaction-images/image2.gif "選取的位置值時，導致 CCMove 動作正在建立並套用至的紅色圓形")

按一下左邊的標籤變更哪種類型的`CCAction`會在圓形上執行。 例如，按一下**位置**標籤將循環瀏覽不同的值可變更：

![](ccaction-images/image3.gif "按一下 [位置] 標籤將循環瀏覽不同的值可變更")

## <a name="common-variable-changing-ccaction-classes"></a>通用變數變更 ccaction 建立動畫類別

**ActionProject**會使用下列`CCAction`-繼承類別，而這兩者屬於 CocosSharp:

 - `CCMoveTo` -變更`CCNode`執行個體的`Position`屬性
 - `CCScaleTo` -變更`CCNode`執行個體的`Scale`屬性
 - `CCRotateTo` -變更`CCNode`執行個體的`Rotation`屬性

本指南為這些動作是指*變數變更*，這表示，它們會直接影響的變數`CCNode`新增到。 其他類型的動作指*加/減速*本指南稍後所述的動作。

**ActionProject**示範這些動作的目的是要修改一段時間的變數。 具體來說，這些`CCActions`建構函式採用兩個引數： 的時間長度才以及要指派的值。 下列程式碼片段會示範如何建立動作的三種類型：


```csharp
switch (VariableOptions [currentVariableIndex])
{
    case "Position":
        coreAction = new CCMoveTo(timeToTake, touch.Location);

        break;
    case "Scale":
        var distance = CCPoint.Distance (touch.Location, drawNodeRoot.Position);
        var desiredScale = distance / DefaultCircleRadius;
        coreAction = new CCScaleTo(timeToTake, desiredScale);

        break;
    case "Rotation":
        float differenceY = touch.Location.Y - drawNodeRoot.PositionY;
        float differenceX = touch.Location.X - drawNodeRoot.PositionX;

        float angleInDegrees = -1 * CCMathHelper.ToDegrees(
            (float)System.Math.Atan2(differenceY, differenceX));

        coreAction = new CCRotateTo (timeToTake, angleInDegrees);

        break; 
...
}
```

一旦建立動作時，它加入 CCNode，如下所示：


```csharp
nodeToAddTo.AddAction (coreAction); 
```

`AddAction` 啟動`CCAction`執行個體的行為，而且會自動執行其邏輯框架後-畫面格到完成為止。

每個類型上述一字結尾*來*表示`CCAction`將修改`CCNode`，好讓動作完成時，引數的值代表最終狀態。 例如，建立`CCMoveTo`與位置的 X = 100，而 Y = 200 會導致`CCNode`執行個體的`Position`設為 X = 100，Y = 200，結尾的時間指定，而不論`CCNode`開始位置的執行個體。

每個 「 To 」 類別也有"的"版本，這會將引數值的目前值加入上`CCNode`。 例如，建立`CCMoveBy`與位置的 X = 100，而 Y = 200 會導致`CCNode`從它已在動作啟動時的位置移至右邊 100 單位並設定 200 個單位的執行個體。


## <a name="easing-actions"></a>加/減速的動作

根據預設，變數變更動作會執行*線性插補*– 動作會移入所需的值以常數速率。 如果內插*位置*呈線性，移動的物件將會立即啟動並停止移動的開頭和結尾的動作，和它的速度會維持一致，因為會執行動作。 

非線性插補會是比較不突兀，並加入波蘭文的項目，因此 CocosSharp 提供各式各樣的加/減速可用來修改變數變更動作的動作。

在 **ActionProject** 範例中，我們可以這些類型的加/減速的動作，按一下第二個標籤之間切換 (預設為**<None>**):

![](ccaction-images/image4.gif "使用者可以在這些類型的加/減速的動作，按一下第二個標籤之間切換")

加/減速的動作是特別強大，因為它們未繫結至任何特定的變數設定動作。 這表示相同的加/減速動作可用來指派位置、 旋轉、 縮放比例或自訂動作 （如將會在本指南稍後所示）。

加/減速動作包裝變數設定的動作 (只要變數設定動作繼承自`CCFiniteTimeAction`) 接受為其建構函式的引數的變數設定動作。

例如，如果標籤已設定為**位置**， **CCEaseElastic**，則當偵測到觸控時，就會執行下列程式碼 （請注意，已反白顯示的相關行省略的程式碼）：


```csharp
CCFiniteTimeAction coreAction = null; 
...
coreAction = new CCMoveTo(timeToTake, touch.Location); 
...
CCAction easing = null; 
...
easing = new CCEaseSineOut (coreAction); 
...
nodeToAddTo.AddAction (easing); 
```

如所示的應用程式，完全相同加/減速可以套用至其他變數設定的動作例如`CCRotateTo`:

![](ccaction-images/image5.gif "可以套用至其他的變數設定動作，例如 CCRotateTo 的完全相同加/減速")


## <a name="easing-in-out-and-inout"></a>在、 及 InOut 加/減速

所有的加/減速動作都有`In`， `Out`，或`InOut`附加至的加/減速類型。 這些詞彙的意思所套用的加/減速時：`In`表示將會在開始時，套用加/減速`Out`在結束時，表示和`InOut`表示同時在開頭和結尾。

`In`加/減速動作會影響變數套用到整個插補 （包括開頭和結尾），整個的方式，但通常最知名的加/減速動作特性會在開始進行。 同樣地，`Out`加/減速動作的特性在於它們的行為，插補的結尾。 比方說，`CCEaseBounceOut`會導致動作的結尾彈跳的物件。


### <a name="out"></a>出

`Out` 加/減速通常適用於非常顯著的變更插補的結尾。 比方說， `CCEaseExponentialOut` ，等到接近目標值會變慢的變更變數的變動率：

![](ccaction-images/image6.gif "CCEaseExponentialOut 會變慢變更變數的變動的率，當它接近目標值")


### <a name="in"></a>In

`In` 加/減速通常適用於最明顯的變更插補的開頭。 比方說，`CCEaseExponentialIn`會較慢移動動作的開頭：

![](ccaction-images/image7.gif "CCEaseExponentialIn 會較慢移動動作的起始處")


### <a name="inout"></a>InOut

`InOut` 通常會套用最值得注意的變更，同時在開頭和結尾。 `InOut` 加/減速是通常是對稱的。 比方說，`CCEaseExponentialInOut`會緩慢移動的開頭和結尾的動作：

![](ccaction-images/image8.gif "CCEaseExponentialInOut 會緩慢移動的開頭和結束動作")


## <a name="implementing-a-custom-ccaction"></a>實作自訂 ccaction 建立動畫

到目前為止所討論的類別的所有被納入 CocosSharp 提供通用的功能。 自訂`CCAction`實作可以提供額外的彈性。 比方說，`CCAction`可以使用哪些控制項的經驗列填滿的比例，這樣的體驗列平順地調每當使用者獲得的經驗。

`CCAction` 實作通常需要兩個類別：

 - `CCFiniteTimeAction` 實作在有限的時間執行的類別會負責啟動動作。 它會具現化的類別，因此是加入直接`CCNode`或加/減速的動作。 它必須具現化，並傳回`CCFiniteTimeActionState`，這將會執行更新。
 - `CCFiniteTimeActionState` 實作在有限的時間執行的狀態類別會負責更新動作中的變數。 它必須實作更新函式，指派時間值根據在目標上的值。 這個類別未明確參考外部`CCFiniteTimeAction`這會建立它。 它只適用於 「 幕後 」。

**ActionProject**提供自訂`CCFiniteTimeAction`稱為實作`LineWidthAction,`用以調整的紅色圓圈上方繪製的黃色線條寬度。 請注意，具現化，並傳回其唯一的工作是`LineWidthState`執行個體：


```csharp
public class LineWidthAction : CCFiniteTimeAction
{
    float endWidth;

    public LineWidthAction (float duration, float width) : base(duration)
    {
        endWidth = width;
    }

    public override CCFiniteTimeAction Reverse ()
    {
        throw new NotImplementedException ();
    }

    protected override CCActionState StartAction (CCNode target)
    {
        return new LineWidthState (this, target, endWidth);
    }
}
```

如前文所述，`LineWidthState`會指派的一行`Width`屬性根據多少`time`已通過：


```csharp
public class LineWidthState : CCFiniteTimeActionState
{
    float deltaWidth;
    float startWidth;

    LineNode castedTarget;

    public LineWidthState(LineWidthAction action, CCNode target, float endWidth) : base(action, target)
    {
        castedTarget = target as LineNode;

        if (castedTarget == null)
        {
            throw new InvalidOperationException ("The argument target must be a LineNode");
        }

        startWidth = castedTarget.Width;
        deltaWidth = endWidth - startWidth;
    }

    public override void Update (float time)
    {
        castedTarget.Width = startWidth + deltaWidth * time;
    }
} 
```

LineWidthAction 可以結合任何加/減速的動作，來變更線條的寬度，以各種方式，在下列動畫中所示：

![](ccaction-images/image9.gif "這個動畫所示，可以與任何加/減速的動作，來變更線條的寬度，以各種方式結合 LineWidthAction")


### <a name="interpolation-and-the-update-method"></a>內插補點，然後使用 Update 方法

唯一的邏輯，除了上述的類別中儲存值存在於`LineWidthState.Update`方法。 `startWidth`變數會儲存的目標寬度`LineNode`開頭的動作，而`deltaWidth`變數會儲存多少值將會變更透過採取的動作。

以替代`time`變數的值是 0，我們可以看到目標`LineNode`其起始位置，將位於：


```csharp
castedTarget.Width = startWidth + deltaWidth * 0; 
```

同樣地，我們可以看到目標`LineNode`會位於其目的地所得到的值為 1 時間變數：


```csharp
castedTarget.Width = startWidth + deltaWidth * 1; 
```

`time`的值通常介於 0 和 1-但不是一定-和`Update`實作不應該假設這些界限。 某些加/減速方法 (例如`CCEaseBackIn`和`CCEaseBackOut`) 會提供一個時間值在 0 到 1 的範圍之外。


## <a name="conclusion"></a>結論

內插補點和簡化是不可或缺的一部分建立外觀精美的遊戲，特別是在建立使用者介面。 本指南涵蓋如何使用`CCActions`進行插補，例如位置和旋轉的標準值，以及自訂的值。 `LineWidthState`和`LineWidthAction`類別示範如何實作自訂動作。

## <a name="related-links"></a>相關連結

- [CCAction](https://developer.xamarin.com/api/type/CocosSharp.CCAction)
- [CCMoveTo](https://developer.xamarin.com/api/type/CocosSharp.CCMoveTo)
- [CCScaleTo](https://developer.xamarin.com/api/type/CocosSharp.CCScaleTo)
- [CCRotateTo](https://developer.xamarin.com/api/type/CocosSharp.CCRotateTo)
- [CCDrawNode](https://developer.xamarin.com/api/type/CocosSharp.CCDrawNode)
- [完整的範例](https://developer.xamarin.com/samples/mobile/CCAction/)
