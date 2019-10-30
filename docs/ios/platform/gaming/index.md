---
title: Xamarin 中的 iOS 遊戲 Api
description: 本文涵蓋 iOS 9 提供的新遊戲增強功能，可用於改善您的 Xamarin。 iOS 遊戲的圖形和音訊功能。
ms.prod: xamarin
ms.assetid: 958D38FD-9240-482E-9A42-D6671ED8F2B0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: fd79153520036ae49fd6fd9abe652e2c7459b30d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032507"
---
# <a name="ios-gaming-apis-in-xamarinios"></a>Xamarin 中的 iOS 遊戲 Api

_本文涵蓋 iOS 9 提供的新遊戲增強功能，可用於改善您的 Xamarin。 iOS 遊戲的圖形和音訊功能。_

Apple 在 iOS 9 中對遊戲 Api 進行了幾項技術改進，讓您更輕鬆地在 Xamarin iOS 應用程式中執行遊戲圖形和音訊。
其中包括透過高階架構的輕鬆開發，以及運用 iOS 裝置的 GPU 功能來改善速度和圖形功能。

[![](images/flocking01.png "An example of an app running flocking")](images/flocking01.png#lightbox)

這包括 GameplayKit、ReplayKit、Model i/o、MetalKit 和金屬效能著色器，以及金屬、SceneKit 和 SpriteKit 的新增強功能。

本文將介紹使用 iOS 9 新遊戲增強功能來改善您的 Xamarin iOS 遊戲的所有方法：

## <a name="introducing-gameplaykit"></a>GameplayKit 簡介

Apple 的新 GameplayKit 架構提供了一組技術，可讓您輕鬆地為 iOS 裝置建立遊戲，方法是減少執行所需的重複、通用程式碼量。 GameplayKit 提供的工具可讓您開發遊戲機制，然後輕鬆地與圖形引擎（例如 SceneKit 或 SpriteKit）結合，以快速提供已完成的遊戲。

GameplayKit 包含數個常見的遊戲播放演算法，例如：

- 以行為為基礎的代理程式模擬，可讓您定義 AI 將自動追求的移動和目標。
- Minmax 的人工智慧，用於輪流遊戲。
- 具有模糊推理的資料驅動遊戲邏輯規則系統，可提供緊急行為。

此外，GameplayKit 會使用可提供下列功能的模組化架構，來進行遊戲開發的建立區塊方法：

- 用來處理遊戲中複雜程式碼型系統的狀態機器。
- 提供隨機遊戲播放和不可預測性，而不會造成偵錯工具問題的工具。
- 可重複使用、以元件化的實體為基礎的架構。

若要深入瞭解 GameplayKit，請參閱 Apple 的[GameplayKit 程式設計指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/GameplayKit_Guide/index.html#//apple_ref/doc/uid/TP40015172)和[GameplayKit 架構參考](https://developer.apple.com/library/prerelease/ios/documentation/GameplayKit/Reference/GameplayKit_Framework/index.html#//apple_ref/doc/uid/TP40015199)。

## <a name="gameplaykit-examples"></a>GameplayKit 範例

讓我們快速瞭解如何使用遊戲播放套件，在 Xamarin iOS 應用程式中執行一些簡單的遊戲播放機制。

### <a name="pathfinding"></a>Pathfinding

Pathfinding 是讓遊戲的 AI 元素能夠在遊戲面板周圍尋找其方式的能力。
例如，2D 敵人透過射擊世界地形，透過迷宮或3D 字元來尋找它。

請考慮下列地圖：

[![](images/gkpathfindpath.png "An example pathfinding map")](images/gkpathfindpath.png#lightbox)

使用 pathfinding 時C# ，此程式碼可以透過對應找到一種方法：

```csharp
var a = GKGraphNode2D.FromPoint (new Vector2 (0, 5));
var b = GKGraphNode2D.FromPoint (new Vector2 (3, 0));
var c = GKGraphNode2D.FromPoint (new Vector2 (2, 6));
var d = GKGraphNode2D.FromPoint (new Vector2 (4, 6));
var e = GKGraphNode2D.FromPoint (new Vector2 (6, 5));
var f = GKGraphNode2D.FromPoint (new Vector2 (6, 0));

a.AddConnections (new [] { b, c }, false);
b.AddConnections (new [] { e, f }, false);
c.AddConnections (new [] { d }, false);
d.AddConnections (new [] { e, f }, false);

var graph = GKGraph.FromNodes(new [] { a, b, c, d, e, f });

var a2e = graph.FindPath (a, e); // [ a, c, d, e ]
var a2f = graph.FindPath (a, f); // [ a, b, f ]

Console.WriteLine(String.Join ("->", (object[]) a2e));
Console.WriteLine(String.Join ("->", (object[]) a2f));
```

### <a name="classical-expert-system"></a>傳統專家系統

下列C#程式碼片段顯示如何使用 GameplayKit 來執行傳統的專家系統：

```csharp
string output = "";
bool reset = false;
int input = 15;

public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    /*
    If reset is true, clear the output and set reset to false
    */
    var clearRule = GKRule.FromPredicate ((rules) => reset, rules => {
        output = "";
        reset = false;
    });
    clearRule.Salience = 1;

    var fizzRule = GKRule.FromPredicate (mod (3), rules => {
        output += "fizz";
    });
    fizzRule.Salience = 2;

    var buzzRule = GKRule.FromPredicate (mod (5), rules => {
        output += "buzz";
    });
    buzzRule.Salience = 2;

    /*
    This *always* evaluates to true, but is higher Salience, so evaluates after lower-salience items
    (which is counter-intuitive). Print the output, and reset (thus triggering "ResetRule" next time)
    */
    var outputRule = GKRule.FromPredicate (rules => true, rules => {
        System.Console.WriteLine(output == "" ? input.ToString() : output);
        reset = true;
    });
    outputRule.Salience = 3;

    var rs = new GKRuleSystem ();
    rs.AddRules (new [] {
        clearRule,
        fizzRule,
        buzzRule,
        outputRule
    });

    for (input = 1; input < 16; input++) {
        rs.Evaluate ();
        rs.Reset ();
    }
}

protected Func<GKRuleSystem, bool> mod(int m)
{
    Func<GKRuleSystem,bool> partiallyApplied = (rs) => input % m == 0;
    return partiallyApplied;
}
```

根據一組指定的規則（`GKRule`）和一組已知的輸入，專家系統（`GKRuleSystem`）會建立可預測的輸出（在上述範例中為`fizzbuzz`）。

### <a name="flocking"></a>Flocking

Flocking 允許一組 AI 控制的遊戲實體以 flock 的方式運作，其中的群組會回應潛在客戶實體的移動和動作，例如航班的 flock 或魚 swimming。

下列C#程式碼片段會使用 GameplayKit 和 SpriteKit 來執行圖形顯示的 flocking 行為：

```csharp
using System;
using SpriteKit;
using CoreGraphics;
using UIKit;
using GameplayKit;
using Foundation;
using System.Collections.Generic;
using System.Linq;
using OpenTK;

namespace FieldBehaviorExplorer
{
    public static class FlockRandom
    {
        private static GKARC4RandomSource rand = new GKARC4RandomSource ();

        static FlockRandom ()
        {
            rand.DropValues (769);
        }

        public static float NextUniform ()
        {
            return rand.GetNextUniform ();
        }
    }

    public class FlockingScene : SKScene
    {
        List<Boid> boids = new List<Boid> ();
        GKComponentSystem componentSystem;
        GKAgent2D trackingAgent; //Tracks finger on screen
        double lastUpdateTime = Double.NaN;
        //Hold on to behavior so it doesn't get GC'ed
        static GKBehavior flockingBehavior;
        static GKGoal seekGoal;

        public FlockingScene (CGSize size) : base (size)
        {
            AddRandomBoids (20);

            var scale = 0.4f;
            //Flocking system
            componentSystem = new GKComponentSystem (typeof(GKAgent2D));
            var behavior = DefineFlockingBehavior (boids.Select (boid => boid.Agent).ToArray<GKAgent2D>(), scale);
            boids.ForEach (boid => {
                boid.Agent.Behavior = behavior;
                componentSystem.AddComponent(boid.Agent);
            });

            trackingAgent = new GKAgent2D ();
            trackingAgent.Position = new Vector2 ((float) size.Width / 2.0f, (float) size.Height / 2.0f);
            seekGoal = GKGoal.GetGoalToSeekAgent (trackingAgent);
        }

        public override void TouchesBegan (NSSet touches, UIEvent evt)
        {
            boids.ForEach(boid => boid.Agent.Behavior.SetWeight(1.0f, seekGoal));
        }

        public override void TouchesEnded (NSSet touches, UIEvent evt)
        {
            boids.ForEach (boid => boid.Agent.Behavior.SetWeight (0.0f, seekGoal));
        }

        public override void TouchesMoved (NSSet touches, UIEvent evt)
        {
            var touch = (UITouch) touches.First();
            var loc = touch.LocationInNode (this);
            trackingAgent.Position = new Vector2((float) loc.X, (float) loc.Y);
        }

        private void AddRandomBoids (int count)
        {
            var scale = 0.4f;
            for (var i = 0; i < count; i++) {
                var b = new Boid (UIColor.Red, this.Size, scale);
                boids.Add (b);
                this.AddChild (b);
            }
        }

        internal static GKBehavior DefineFlockingBehavior(GKAgent2D[] boidBrains, float scale)
        {
            if (flockingBehavior == null) {
                var flockingGoals = new GKGoal[3];
                flockingGoals [0] = GKGoal.GetGoalToSeparate (boidBrains, 100.0f * scale, (float)Math.PI * 8.0f);
                flockingGoals [1] = GKGoal.GetGoalToAlign (boidBrains, 40.0f * scale, (float)Math.PI * 8.0f);
                flockingGoals [2] = GKGoal.GetGoalToCohere (boidBrains, 40.0f * scale, (float)Math.PI * 8.0f);

                flockingBehavior = new GKBehavior ();
                flockingBehavior.SetWeight (25.0f, flockingGoals [0]);
                flockingBehavior.SetWeight (10.0f, flockingGoals [1]);
                flockingBehavior.SetWeight (10.0f, flockingGoals [2]);
            }
            return flockingBehavior;
        }

        public override void Update (double currentTime)
        {
            base.Update (currentTime);
            if (Double.IsNaN(lastUpdateTime)) {
                lastUpdateTime = currentTime;
            }
            var delta = currentTime - lastUpdateTime;
            componentSystem.Update (delta);
        }
    }

    public class Boid : SKNode, IGKAgentDelegate
    {
        public GKAgent2D Agent { get { return brains; } }
        public SKShapeNode Sprite { get { return sprite; } }

        class BoidSprite : SKShapeNode
        {
            public BoidSprite (UIColor color, float scale)
            {
                var rot = CGAffineTransform.MakeRotation((float) (Math.PI / 2.0f));
                var path = new CGPath ();
                path.MoveToPoint (rot, new CGPoint (10.0, 0.0));
                path.AddLineToPoint (rot, new CGPoint (0.0, 30.0));
                path.AddLineToPoint (rot, new CGPoint (10.0, 20.0));
                path.AddLineToPoint (rot, new CGPoint (20.0, 30.0));
                path.AddLineToPoint (rot, new CGPoint (10.0, 0.0));
                path.CloseSubpath ();

                this.SetScale (scale);
                this.Path = path;
                this.FillColor = color;
                this.StrokeColor = UIColor.White;

            }
        }

        private GKAgent2D brains;
        private BoidSprite sprite;
        private static int boidId = 0;

        public Boid (UIColor color, CGSize size, float scale)
        {
            brains = BoidBrains (size, scale);
            sprite = new BoidSprite (color, scale);
            sprite.Position = new CGPoint(brains.Position.X, brains.Position.Y);
            sprite.ZRotation = brains.Rotation;
            sprite.Name = boidId++.ToString ();

            brains.Delegate = this;

            this.AddChild (sprite);
        }

        private GKAgent2D BoidBrains(CGSize size, float scale)
        {
            var brains = new GKAgent2D ();
            var x = (float) (FlockRandom.NextUniform () * size.Width);
            var y = (float) (FlockRandom.NextUniform () * size.Height);
            brains.Position = new Vector2 (x, y);

            brains.Rotation = (float)(FlockRandom.NextUniform () * Math.PI * 2.0);
            brains.Radius = 30.0f * scale;
            brains.MaxSpeed = 0.5f;
            return brains;
        }

        [Export ("agentDidUpdate:")]
        public void AgentDidUpdate (GameplayKit.GKAgent agent)
        {
        }

        [Export ("agentWillUpdate:")]
        public void AgentWillUpdate (GameplayKit.GKAgent agent)
        {
            var brainsIn = (GKAgent2D) agent;
            sprite.Position = new CGPoint(brainsIn.Position.X, brainsIn.Position.Y);
            sprite.ZRotation = brainsIn.Rotation;
            Console.WriteLine ($"{sprite.Name} -> [{sprite.Position}], {sprite.ZRotation}");
        }
    }
}
```

接下來，在 view controller 中執行此場景：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
        // Perform any additional setup after loading the view, typically from a nib.
        this.View = new SKView {
        ShowsFPS = true,
        ShowsNodeCount = true,
        ShowsDrawCount = true
    };
}

public override void ViewWillLayoutSubviews ()
{
    base.ViewWillLayoutSubviews ();

    var v = (SKView)View;
    if (v.Scene == null) {
        var scene = new FlockingScene (View.Bounds.Size);
        scene.ScaleMode = SKSceneScaleMode.AspectFill;
        v.PresentScene (scene);
    }
}
```

執行時，小動畫的「 _Boids_ 」會 flock 我們的手指點：

[![](images/flocking01.png "The little animated Boids will flock around the finger taps")](images/flocking01.png#lightbox)

### <a name="other-apple-examples"></a>其他 Apple 範例

除了上述範例以外，Apple 也提供下列可轉碼至C#和 Xamarin 的範例應用程式：

- [FourInARow：使用適用于對手 AI 的 GameplayKit Minmax 策略家](https://developer.apple.com/library/prerelease/ios/samplecode/FourInARow/Introduction/Intro.html#//apple_ref/doc/uid/TP40016142)
- [AgentsCatalog：在 GameplayKit 中使用代理程式系統](https://developer.apple.com/library/prerelease/ios/samplecode/AgentsCatalog/Introduction/Intro.html#//apple_ref/doc/uid/TP40016141)
- [DemoBots：使用 SpriteKit 和 GameplayKit 建立跨平臺遊戲](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179)

## <a name="metal"></a>Metal

在 iOS 9 中，Apple 對金屬進行了幾項變更和新增功能，以提供 GPU 的低額外負荷存取。 您可以使用金屬，將您的 iOS 應用程式的圖形和運算能力最大化。

金屬架構包含下列新功能：

- OS X 的新私用和深度樣板材質。
- 改良的陰影品質，具有深度固定和不同的 front 和 back 樣板值。
- 金屬陰影語言和金屬標準程式庫的改良功能。
- 計算著色器支援更廣泛的像素格式。

### <a name="the-metalkit-framework"></a>MetalKit 架構

MetalKit 架構提供一組公用程式類別和功能，可減少在 iOS 應用程式中使用裸機所需的工作量。 MetalKit 提供三個主要領域的支援：

1. 從各種來源進行非同步材質載入，包括像是 PNG、JPEG、KTX 和 PVR 的一般格式。
2. 輕鬆存取以模型 i/o 為基礎的資產，以進行金屬特定模型處理。 這些功能已高度優化，可在模型 i/o 網格與金屬緩衝區之間提供有效率的資料傳輸。
3. 預先定義的金屬視圖和視圖管理，可大幅減少在 iOS 應用程式中顯示圖形轉譯所需的程式碼數量。

若要深入瞭解 MetalKit，請參閱 Apple 的[MetalKit 架構參考](https://developer.apple.com/library/prerelease/ios/documentation/MetalKit/Reference/MTKFrameworkReference/index.html#//apple_ref/doc/uid/TP40015356)、[金屬程式設計指南](https://developer.apple.com/library/prerelease/ios/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221)、[金屬架構參考](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalFrameworkReference/index.html#//apple_ref/doc/uid/TP40014161)和[金屬陰影語言指南](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalShadingLanguageGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014364)。

### <a name="metal-performance-shaders-framework"></a>金屬效能著色器架構

金屬效能著色器架構提供高度優化的圖形和計算式著色器集合，以便在您的金屬型 iOS 應用程式中使用。 金屬效能著色器架構中的每個著色器都已特別調整，以提供裸機支援的 iOS Gpu 高效能。

藉由使用金屬效能著色器類別，您可以在每個特定的 iOS GPU 上達到最高的效能，而不需要鎖定和維護個別的程式碼基底。 金屬效能著色器可以與任何材質資源（例如紋理和緩衝區）搭配使用。

金屬效能著色器架構提供一組常見的著色器，例如：

- **高斯模糊**（`MPSImageGaussianBlur`）
- **Sobel 邊緣偵測**（`MPSImageSobel`）
- **影像長條圖**（`MPSImageHistogram`）

如需詳細資訊，請參閱 Apple 的[金屬網底語言指南](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalShadingLanguageGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014364)。

## <a name="introducing-model-io"></a>模型 i/o 簡介

Apple 的模型 i/o 架構可讓您深入瞭解3D 資產（例如模型和其相關資源）。 模型 i/o 為您的 iOS 遊戲提供了以實體為基礎的材質、模型和光源，可以與 GameplayKit、裸機和 SceneKit 搭配使用。

有了模型 i/o，您就可以支援下列類型的工作：

- 從各種熱門軟體和遊戲引擎格式匯入光源、材質、網狀資料、攝影機設定和其他以場景為基礎的資訊。
- 處理或產生以場景為基礎的資訊，例如建立 cti 紋理天空 domes 或製作光源進入網格。
- 與 MetalKit、SceneKit 和 GLKit 合作，有效率地將遊戲資產載入 GPU 緩衝區以進行轉譯。
- 將以場景為基礎的資訊匯出到各種熱門的軟體和遊戲引擎格式。

若要深入瞭解模型 i/o，請參閱 Apple 的[模型 I/o 架構參考](https://developer.apple.com/library/prerelease/ios/documentation/ModelIO/Reference/ModelIO_Framework/index.html#//apple_ref/doc/uid/TP40015421)

## <a name="introducing-replaykit"></a>ReplayKit 簡介

Apple 的新 ReplayKit 架構可讓您輕鬆地將遊戲播放記錄新增至您的 iOS 遊戲，並可讓使用者快速且輕鬆地在應用程式中編輯並分享這部影片。

如需詳細資訊，請參閱 Apple 的[社交 ReplayKit 和 Game Center 影片](https://developer.apple.com/videos/wwdc/2015/?id=605)及其[DemoBots：使用 SpriteKit 和 GameplayKit 範例應用程式建立跨平臺遊戲](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179)。

## <a name="scenekit"></a>SceneKit

場景套件是可簡化使用3D 圖形的3D 場景圖形 API。 它最初是在 OS X 10.8 中引進，現在已進入 iOS 8。 使用場景套件建立沉浸式3D 視覺效果，而休閒3D 遊戲不需要 OpenGL 的專業知識。 以常見的場景圖形概念為基礎，場景套件會抽象化 OpenGL 和 OpenGL ES 的複雜性，讓您很容易就能將3D 內容新增至應用程式。 不過，如果您是 OpenGL 專家，場景套件也具有直接與 OpenGL 結合的絕佳支援。 它也包含許多輔助3D 圖形的功能，例如物理，並與數個其他 Apple 架構（例如核心動畫、核心影像和 Sprite 套件）緊密整合。

如需詳細資訊，請參閱我們的[SceneKit](~/ios/platform/gaming/scenekit.md)檔。

### <a name="scenekit-changes"></a>SceneKit 變更

Apple 已針對 iOS 9 將下列新功能新增至 SceneKit：

- Xcode 現在提供場景編輯器，可讓您直接從 Xcode 內編輯場景，以快速建立遊戲和互動式3D 應用程式。
- `SCNView` 和 `SCNSceneRenderer` 類別可以用來啟用金屬轉譯（在支援的 iOS 裝置上）。
- `SCNAudioPlayer` 和 `SCNNode` 類別可用來新增空間音訊效果，以自動追蹤播放程式位置至 iOS 應用程式。

如需詳細資訊，請參閱我們的[SceneKit 檔](~/ios/platform/introduction-to-ios8.md#scenekit)和 Apple 的[SceneKit 架構參考](https://developer.apple.com/library/prerelease/ios/documentation/SceneKit/Reference/SceneKit_Framework/index.html#//apple_ref/doc/uid/TP40012283)和[Fox：使用 Xcode 場景編輯器範例專案建立 SceneKit 遊戲](https://developer.apple.com/library/prerelease/ios/samplecode/Fox/Introduction/Intro.html#//apple_ref/doc/uid/TP40016154)。

## <a name="spritekit"></a>SpriteKit

來自 Apple 的2D 遊戲架構 Sprite 套件，在 iOS 8 和 OS X Yosemite 中有一些有趣的新功能。 其中包括與場景套件、著色器支援、光源、陰影、條件約束、一般地圖產生和物理增強功能的整合。 特別的是，新的物理功能讓您很容易就能在遊戲中加入實際的效果。

如需詳細資訊，請參閱我們的[SpriteKit](~/ios/platform/gaming/spritekit.md)檔。

### <a name="spritekit-changes"></a>SpriteKit 變更

Apple 已針對 iOS 9 將下列新功能新增至 SpriteKit：

- 空間音效效果，會自動追蹤播放程式在 `SKAudioNode` 類別中的位置。
- Xcode 現在具有場景編輯器和動作編輯器，可讓您輕鬆地建立2D 遊戲和應用程式。
- 使用新的相機節點（`SKCameraNode`）物件，輕鬆地滾動遊戲支援。
- 在支援金屬的 iOS 裝置上，SpriteKit 會自動使用它來呈現，即使您已經使用自訂的 OpenGL ES 著色器也一樣。

如需詳細資訊，請參閱我們的[SpriteKit 檔](~/ios/platform/introduction-to-ios8.md#spritekit)Apple 的[SpriteKit 架構參考](https://developer.apple.com/library/prerelease/ios/documentation/SpriteKit/Reference/SpriteKitFramework_Ref/index.html#//apple_ref/doc/uid/TP40013041)及其[DemoBots：使用 SpriteKit 和 GameplayKit 範例應用程式建立跨平臺遊戲](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179)。

## <a name="summary"></a>總結

本文涵蓋 iOS 9 針對您的 Xamarin iOS 應用程式所提供的新遊戲功能。
它引進了 GameplayKit 和 Model i/o;金屬的主要增強功能;以及 SceneKit 和 SpriteKit 的新功能。

## <a name="related-links"></a>相關連結

- [iOS 9 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [iOS 9 開發人員](https://developer.apple.com/ios/pre-release/)
- [iOS 9。0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
