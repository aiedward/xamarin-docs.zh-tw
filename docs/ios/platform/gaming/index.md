---
title: iOS 遊戲應用程式開發介面
description: 本文涵蓋 iOS 9，可用來改善 Xamarin.iOS 遊戲的圖形和音訊的功能所提供的新遊戲增強功能。
ms.prod: xamarin
ms.assetid: 958D38FD-9240-482E-9A42-D6671ED8F2B0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 34d3d6980819510a3390e2c30069818d6dfd721f
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="ios-gaming-apis"></a>iOS 遊戲應用程式開發介面

_本文涵蓋 iOS 9，可用來改善 Xamarin.iOS 遊戲的圖形和音訊的功能所提供的新遊戲增強功能。_

Apple 所做的數個技術改良在 iOS 9 遊戲應用程式開發介面可讓您更輕鬆地實作 Xamarin.iOS 應用程式中的遊戲圖形與音訊。
這些包括這兩個輕鬆開發透過高層級架構和另外的 iOS 裝置的 GPU，以提升的速度及圖形的能力。

[![](images/flocking01.png "執行 flocking 的應用程式範例")](images/flocking01.png#lightbox)

這包括 GameplayKit、 ReplayKit、 模型 I/O、 MetalKit 和金屬效能著色器連同金屬、 SceneKit 和 SpriteKit 新的增強功能。

本文將介紹的各種方式來改善 Xamarin.iOS 遊戲透過 iOS 9 的新遊戲增強功能：

## <a name="introducing-gameplaykit"></a>介紹 GameplayKit

Apple 的新 GameplayKit framework 提供一組技術，可讓您輕鬆地建立適用於 iOS 裝置的遊戲藉由減少重複性的一般實作所需的程式碼數量。 GameplayKit 提供工具來快速開發遊戲可以輕鬆地結合與圖形 （例如 SceneKit 或 SpriteKit） 引擎的機制來傳送已完成的遊戲。

GameplayKit 包含數個，常見，例如遊戲玩的演算法：

- 行為型，則代理程式的模擬，可讓您定義的移動和 AI 會自動執行的目標。
- 開啟為基礎的遊戲 minmax 人工地智慧。
- 模糊的原因，以提供緊急行為與資料驅動型遊戲邏輯規則系統。

此外，GameplayKit 所需的建置組塊方法遊戲開發使用模組化架構中提供下列功能：

- 處理複雜，程序性程式碼的狀態機器架構的系統在遊戲進行中。
- 提供的工具隨機遊戲婧矔菛不可預測性，而不會造成偵錯問題。
- 可重複使用、 元件化實體基礎架構。

若要深入了解 GameplayKit，請參閱 Apple [Gameplaykit 程式設計指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/GameplayKit_Guide/index.html#//apple_ref/doc/uid/TP40015172)和[GameplayKit Framework 參考](https://developer.apple.com/library/prerelease/ios/documentation/GameplayKit/Reference/GameplayKit_Framework/index.html#//apple_ref/doc/uid/TP40015199)。

## <a name="gameplaykit-examples"></a>GameplayKit 範例

讓我們快速查看在使用遊戲進行套件 Xamarin.iOS 應用程式中實作一些簡單的遊戲機制。

### <a name="pathfinding"></a>Pathfinding

Pathfinding 是 AI 項目在遊戲的遊戲面板其熟悉的能力。
例如，尋找整個迷宮或 3D 字元第一位人員-藍世界地形透過 2D 敵人。

請考慮下列對應：

[![](images/gkpathfindpath.png "Pathfinding 對應的範例")](images/gkpathfindpath.png#lightbox)

使用 pathfinding 這個 C# 程式碼可以找出透過對應的方式：

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

### <a name="classical-expert-system"></a>古典專家系統

下列 C# 程式碼片段示範如何 GameplayKit 可以用來實作典型的專家系統：

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

根據給定的一組規則 (`GKRule`) 和一組已知的專家系統的輸入 (`GKRuleSystem`) 將會建立可預測的輸出 (`fizzbuzz`如上述範例中)。

### <a name="flocking"></a>Flocking

Flocking 可讓一群 AI 控制遊戲的實體成 flock，其中回應的動作和動作的負責人實體，就像在途中鳥的 flock 或 fish swimming 的學校群組行為。

下列程式碼片段的 C# 程式碼會實作圖形顯示使用 GameplayKit 和 SpriteKit flocking 行為：

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

接下來，實作此畫面中檢視控制器：

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

執行時，一點動畫_"Boids"_將 flock 周圍我們手指點選：

[![](images/flocking01.png "稍微動畫的 Boids 將 flock 周圍手指點選")](images/flocking01.png#lightbox)

### <a name="other-apple-examples"></a>其他 Apple 範例

除了上述的範例，提供下列的範例應用程式可能是以 C# 和 Xamarin.iOS 轉碼 Apple:

- [FourInARow： 使用對手 AI GameplayKit Minmax 專長](https://developer.apple.com/library/prerelease/ios/samplecode/FourInARow/Introduction/Intro.html#//apple_ref/doc/uid/TP40016142)
- [AgentsCatalog: GameplayKit 中使用的代理程式系統](https://developer.apple.com/library/prerelease/ios/samplecode/AgentsCatalog/Introduction/Intro.html#//apple_ref/doc/uid/TP40016141)
- [DemoBots： 建置跨平台遊戲 SpriteKit 與 GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179)

## <a name="metal"></a>Metal

在 iOS 9，Apple 對數個變更和新功能以提供低負擔存取 GPU 的金屬。 使用金屬您可以最大化的圖形和運算可能您的 iOS 應用程式。

裸機架構包含下列新功能：

- 新的私用和深度樣板紋理 OS x。
- 改善的陰影品質深度壓制和個別前端和後樣板值。
- 裸機陰影語言和金屬標準程式庫的改進。
- 計算著色器支援廣泛的像素格式。

### <a name="the-metalkit-framework"></a>MetalKit 架構

MetalKit framework 提供一組公用程式類別和功能，減少使用金屬中 iOS 應用程式所需的工作量。 MetalKit 提供三個主要區域中的支援：

1. 從各種不同的來源包括常見的格式，例如 PNG、 JPEG、 KTX 和 PVR 載入非同步紋理。
2. 簡單的模型 I/O 存取基礎金屬特定模型處理的資產。 這些功能已經過高的最佳化，提供模型 I/O 網狀結構和金屬緩衝區之間的有效率的資料傳輸。
3. 預先定義的金屬檢視和檢視管理可大幅降低顯示圖形呈現中 iOS 應用程式所需的程式碼數量。

若要深入了解 MetalKit，請參閱 Apple [MetalKit Framework 參考](https://developer.apple.com/library/prerelease/ios/documentation/MetalKit/Reference/MTKFrameworkReference/index.html#//apple_ref/doc/uid/TP40015356)，[金屬程式設計指南](https://developer.apple.com/library/prerelease/ios/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221)，[金屬 Framework 參考](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalFrameworkReference/index.html#//apple_ref/doc/uid/TP40014161)和[金屬陰影語言指南](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalShadingLanguageGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014364)。

### <a name="metal-performance-shaders-framework"></a>裸機效能著色器架構

裸機效能著色器架構提供高度最佳化圖形集以及計算著色器，以用於您的金屬型 iOS 應用程式。 每個架構已特別調整成金屬提供高效能的金屬效能著色器中的著色器支援 iOS Gpu。

藉由使用金屬效能著色器類別，您可以達到最高的效能可能在每個特定的 iOS GPU 上而不需要為目標，並維護個別的程式碼基底。 裸機效能著色器可以搭配任何金屬的資源，例如紋理和緩衝區。

「 裸機效能著色器架構 」 提供一組通用的著色器，例如：

- **高斯模糊**(`MPSImageGaussianBlur`)
- **Sobel 邊緣偵測**(`MPSImageSobel`)
- **映像長條圖**(`MPSImageHistogram`)

如需詳細資訊，請參閱 Apple[金屬陰影語言指南](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalShadingLanguageGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014364)。

## <a name="introducing-model-io"></a>導入模型 I/O

Apple 的模型 I/O 架構提供深入的了解的 3D 資產 （例如模型和其相關的資源）。 模型 I/O 提供實體為基礎的資料，模型和光源可用 GameplayKit、 金屬與 SceneKit iOS 的遊戲。

透過模型 I/O，則可支援下列類型的工作：

- 匯入光源材料、 網狀結構資料、 相機設定和其他場景為基礎的資訊從許多熱門的軟體和遊戲引擎格式。
- 處理，或產生場景為基礎的資訊如建立可循序材質 sky 國土或試吃到網狀結構光源。
- 適用於 MetalKit、 SceneKit 且 GLKit 有效率地載入轉譯的 GPU 緩衝區中的遊戲資產。
- 場景為基礎的資訊匯出到許多熱門的軟體和遊戲引擎格式。

若要深入了解模型 I/O，請參閱 Apple[模型 I/O Framework 參考](https://developer.apple.com/library/prerelease/ios/documentation/ModelIO/Reference/ModelIO_Framework/index.html#//apple_ref/doc/uid/TP40015421)

## <a name="introducing-replaykit"></a>介紹 ReplayKit

Apple 的新 ReplayKit 架構可讓您輕鬆地加入 iOS 遊戲玩遊戲的記錄，並讓使用者快速且輕鬆地編輯和共用應用程式內從這段影片。

如需詳細資訊，請參閱 Apple[社交 ReplayKit 和遊戲中心的視訊會](https://developer.apple.com/videos/wwdc/2015/?id=605)及其[DemoBots： 建置跨平台遊戲 SpriteKit 與 GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179)範例應用程式。

## <a name="scenekit"></a>SceneKit

場景套件是 3D 場景的圖形，可簡化使用 3D 圖形 API。 它引進 OS X 10.8，和 iOS 8 現在有了。 使用場景套件建立沈浸式 3D 視覺效果和 3D 的休閒遊戲不需要 OpenGL 的專業知識。 常見的場景圖形概念上建置，場景套件抽象化 OpenGL 和 OpenGL ES，因此很容易就能加入 3D 內容至應用程式的複雜性。 不過，如果您是 OpenGL 專家，場景套件會有很棒支援直接與 OpenGL 以及繫結。 也包含許多功能，可補充 3D 圖形，例如物理，並充分整合數個其他 Apple 架構，例如核心動畫、 Core 映像和套件精靈。

如需詳細資訊，請參閱我們[SceneKit](~/ios/platform/gaming/scenekit.md)文件。

### <a name="scenekit-changes"></a>SceneKit 變更

Apple 已加入下列新功能 SceneKit ios 9:

- Xcode 現在提供一個場景編輯器，可讓您快速建置遊戲和互動式 3D 應用程式，藉由編輯在 Xcode 中的直接從場景。
- `SCNView`和`SCNSceneRenderer`類別可以用來啟用 （在支援的 iOS 裝置） 上的金屬轉譯。
- `SCNAudioPlayer`和`SCNNode`類別可以用來新增自動追蹤 iOS 應用程式的 播放程式位置的空間音訊效果。

如需詳細資訊，請參閱我們[SceneKit 文件](~/ios/platform/introduction-to-ios8.md#scenekit)與 Apple [SceneKit Framework 參考](https://developer.apple.com/library/prerelease/ios/documentation/SceneKit/Reference/SceneKit_Framework/index.html#//apple_ref/doc/uid/TP40012283)和[Fox： 建置 SceneKit 遊戲使用 Xcode 場景編輯器](https://developer.apple.com/library/prerelease/ios/samplecode/Fox/Introduction/Intro.html#//apple_ref/doc/uid/TP40016154)範例專案。

## <a name="spritekit"></a>SpriteKit

精靈套件，2D 遊戲，向 Apple 架構有一些有趣的新功能，在 iOS 8 和 OS X Yosemite。 這些包括整合場景套件、 著色器支援、 光源、 陰影、 條件約束、 法線貼圖產生與物理增強功能。 特別是，新物理功能使遊戲中加入實際的效果很容易。

如需詳細資訊，請參閱我們[SpriteKit](~/ios/platform/gaming/spritekit.md)文件。

### <a name="spritekit-changes"></a>SpriteKit 變更

Apple 已加入下列新功能 SpriteKit ios 9:

- 自動追蹤玩家的位置的空間音訊效果`SKAudioNode`類別。
- Xcode 現在功能的場景編輯器 」 和 「 動作編輯器，讓您輕鬆的 2D 遊戲和應用程式建立的。
- 很容易捲動遊戲的支援，以新的相機節點 (`SKCameraNode`) 物件。
- 在 iOS 裝置支援金屬，SpriteKit 會自動使用它進行呈現，即使您已在使用自訂的 OpenGL ES 著色器。

如需詳細資訊，請參閱我們[SpriteKit 文件](~/ios/platform/introduction-to-ios8.md#spritekit)Apple [SpriteKit Framework 參考](https://developer.apple.com/library/prerelease/ios/documentation/SpriteKit/Reference/SpriteKitFramework_Ref/index.html#//apple_ref/doc/uid/TP40013041)及其[DemoBots： 建置跨平台遊戲和 SpriteKit 和GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179)範例應用程式。

## <a name="summary"></a>總結

這篇文章已涵蓋新遊戲功能 Xamarin.iOS 應用程式提供該 iOS 9。
它引進 GameplayKit 和模型的 I/O;裸機; 的主要增強功能以及 SceneKit 和 SpriteKit 的新功能。



## <a name="related-links"></a>相關連結

- [iOS 9 範例](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 的開發人員](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
