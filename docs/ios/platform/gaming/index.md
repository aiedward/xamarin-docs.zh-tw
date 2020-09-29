---
title: Xamarin 中的 iOS 遊戲 Api
description: 本文涵蓋 iOS 9 提供的全新遊戲增強功能，可用來改善您的 Xamarin iOS 遊戲的圖形和音訊功能。
ms.prod: xamarin
ms.assetid: 958D38FD-9240-482E-9A42-D6671ED8F2B0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: 20efcf1af10b7c1d3d36e570bc838e396241ffee
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436238"
---
# <a name="ios-gaming-apis-in-xamarinios"></a>Xamarin 中的 iOS 遊戲 Api

_本文涵蓋 iOS 9 提供的全新遊戲增強功能，可用來改善您的 Xamarin iOS 遊戲的圖形和音訊功能。_

Apple 針對 iOS 9 中的遊戲 Api 進行了幾項技術改進，讓您更輕鬆地在 Xamarin iOS 應用程式中執行遊戲圖形和音訊。
這些功能包括透過高階架構輕鬆進行開發，並利用 iOS 裝置 GPU 的強大功能來改善速度和圖形能力。

[![執行群群聚的應用程式範例](images/flocking01.png)](images/flocking01.png#lightbox)

這包括 GameplayKit、ReplayKit、模型 i/o、MetalKit 和金屬效能著色器，以及裸機、SceneKit 和 SpriteKit 的新增強功能。

本文將介紹使用 iOS 9 的新遊戲增強功能來改善您的 Xamarin iOS 遊戲的所有方法：

## <a name="introducing-gameplaykit"></a>GameplayKit 簡介

Apple new GameplayKit framework 提供一組技術，可讓您藉由減少執行所需的重複、通用程式碼數量，輕鬆地建立 iOS 裝置的遊戲。 GameplayKit 提供的工具可讓您開發遊戲機制，然後輕鬆地與圖形引擎結合 (例如 SceneKit 或 SpriteKit) ，以快速提供已完成的遊戲。

GameplayKit 包含數個常見的遊戲播放演算法，例如：

- 以行為為基礎的代理程式模擬，可讓您定義 AI 將自動採用的移動和目標。
- Minmax 的人工智慧，適用于回合遊戲的播放。
- 具有模糊推理的資料驅動遊戲邏輯規則系統，可提供緊急行為。

此外，GameplayKit 會使用提供下列功能的模組化架構，來進行遊戲開發的建立區塊方法：

- 用來處理遊戲中複雜的程式性程式碼系統的狀態機器。
- 提供隨機遊戲播放和不可預測性的工具，而不會造成偵錯工具的問題。
- 可重複使用的元件化實體架構。

若要深入瞭解 GameplayKit，請參閱 Apple 的 [GameplayKit 程式設計指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/GameplayKit_Guide/index.html#//apple_ref/doc/uid/TP40015172) 和 [GameplayKit 架構參考](https://developer.apple.com/library/prerelease/ios/documentation/GameplayKit/Reference/GameplayKit_Framework/index.html#//apple_ref/doc/uid/TP40015199)。

## <a name="gameplaykit-examples"></a>GameplayKit 範例

讓我們快速瞭解如何使用遊戲播放套件在 Xamarin iOS 應用程式中執行一些簡單的遊戲播放機制。

### <a name="pathfinding"></a>Pathfinding

Pathfinding 可讓遊戲的 AI 元素找出遊戲台周圍的方式。
例如，2D 敵人會透過射擊世界地形，在迷宮或3D 字元中尋找其方式。

請考慮下列對應：

[![範例 pathfinding 對應](images/gkpathfindpath.png)](images/gkpathfindpath.png#lightbox)

使用 pathfinding 時，此 c # 程式碼可透過地圖找出一種方法：

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

下列 c # 程式碼程式碼片段會示範如何使用 GameplayKit 來執行傳統專家系統：

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

根據一組指定的規則 (`GKRule`) 和一組已知的輸入，專家系統 (`GKRuleSystem`) 將會針對上述範例建立可預測的輸出 (`fizzbuzz`) 。

### <a name="flocking"></a>植 絨

群群聚可讓一組 AI 控管的遊戲實體以 flock 的方式運作，其中群組會回應潛在客戶實體的移動和動作，例如航班的 flock 或魚游泳。

下列 c # 程式碼程式碼片段會使用 GameplayKit 和 SpriteKit 來針對圖形顯示來實行群群聚行為：

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

接下來，在 view 控制器中執行此場景：

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

執行時，稍微動畫的「 _Boids_ 」就會 flock 點一下：

[![稍微動畫的 Boids 會 flock 在手指點點點的周圍](images/flocking01.png)](images/flocking01.png#lightbox)

### <a name="other-apple-examples"></a>其他 Apple 範例

除了上述的範例，Apple 提供下列可轉碼至 c # 和 Xamarin 的範例應用程式：

- [FourInARow：使用 GameplayKit Minmax 策略家來取得對手的 AI](https://developer.apple.com/library/prerelease/ios/samplecode/FourInARow/Introduction/Intro.html#//apple_ref/doc/uid/TP40016142)
- [AgentsCatalog：在 GameplayKit 中使用代理程式系統](https://developer.apple.com/library/prerelease/ios/samplecode/AgentsCatalog/Introduction/Intro.html#//apple_ref/doc/uid/TP40016141)
- [DemoBots：使用 SpriteKit 和 GameplayKit 建立跨平臺遊戲](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179)

## <a name="metal"></a>金屬

在 iOS 9 中，Apple 已對裸機進行數個變更和新增，以提供 GPU 的低額外負荷存取權。 您可以使用裸機，將 iOS 應用程式的圖形和計算潛力最大化。

金屬架構包含下列新功能：

- OS X 的新私用和深度樣板紋理。
- 利用深度固定和個別的 front 和上一頁樣板值來改善陰影品質。
- 金屬網底語言和裸機標準程式庫的增強功能。
- 計算著色器支援更廣泛的像素格式。

### <a name="the-metalkit-framework"></a>MetalKit 架構

MetalKit 架構提供一組公用程式類別和功能，可減少在 iOS 應用程式中使用裸機所需的工作量。 MetalKit 在三個主要領域中提供支援：

1. 從各種來源（包括 PNG、JPEG、KTX 和 PVR 等常見格式）載入非同步紋理。
2. 輕鬆存取以模型 i/o 為基礎的資產，以進行裸機特定模型處理。 這些功能經過高度優化，可在模型 i/o 網格與裸機緩衝區之間提供有效率的資料傳輸。
3. 預先定義的裸機觀賞和視圖管理，可大幅減少在 iOS 應用程式內顯示圖形轉譯所需的程式碼數量。

若要深入瞭解 MetalKit，請參閱 Apple 的 [MetalKit 架構參考](https://developer.apple.com/library/prerelease/ios/documentation/MetalKit/Reference/MTKFrameworkReference/index.html#//apple_ref/doc/uid/TP40015356)、 [裸機程式設計指南](https://developer.apple.com/library/prerelease/ios/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221)、金屬 [架構參考](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalFrameworkReference/index.html#//apple_ref/doc/uid/TP40014161) 和 [金屬網底語言指南](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalShadingLanguageGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014364)。

### <a name="metal-performance-shaders-framework"></a>金屬效能著色器架構

金屬效能著色器架構會提供一組高度優化的圖形和以計算為基礎的著色器，供您在以金屬為基礎的 iOS 應用程式中使用。 金屬效能著色器架構中的每個著色器都經過明確調整，以提供裸機支援 iOS Gpu 的高效能。

藉由使用金屬效能著色器類別，您可以在每個特定的 iOS GPU 上達到最高效能，而不需要鎖定和維護個別的程式碼基底。 金屬效能著色器可以搭配任何裸機資源使用，例如紋理和緩衝區。

金屬效能著色器架構會提供一組常見的著色器，例如：

- **高斯模糊** (`MPSImageGaussianBlur`) 
- **Sobel Edge 偵測** (`MPSImageSobel`) 
- **影像長條圖** (`MPSImageHistogram`) 

如需詳細資訊，請參閱 Apple 的 [裸機網底語言指南](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalShadingLanguageGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014364)。

## <a name="introducing-model-io"></a>模型 i/o 簡介

Apple 的模型 i/o 架構可讓您深入瞭解3D 資產 (例如模型及其相關資源) 。 模型 i/o 為您的 iOS 遊戲提供可搭配 GameplayKit、裸機和 SceneKit 使用的實體資料、模型和光源。

您可以使用模型 i/o 來支援下列類型的工作：

- 從各種熱門的軟體和遊戲引擎格式匯入光源、材質、網格資料、相機設定和其他場景資訊。
- 處理或產生場景型資訊，例如建立 cti 紋理天空 domes 或製作光源到網格中。
- 適用于 MetalKit、SceneKit 和 GLKit，可有效率地將遊戲資產載入 GPU 緩衝區以進行轉譯。
- 將以場景為基礎的資訊匯出至各種熱門的軟體和遊戲引擎格式。

若要深入瞭解模型 i/o，請參閱 Apple 的 [模型 I/o 架構參考](https://developer.apple.com/library/prerelease/ios/documentation/ModelIO/Reference/ModelIO_Framework/index.html#//apple_ref/doc/uid/TP40015421)

## <a name="introducing-replaykit"></a>ReplayKit 簡介

Apple 的新 ReplayKit 架構可讓您輕鬆地將遊戲播放記錄新增至 iOS 遊戲，並可讓使用者在應用程式中快速且輕鬆地編輯及分享這段影片。

如需詳細資訊，請參閱 Apple 的 [ReplayKit 和 Game Center 影片](https://developer.apple.com/videos/wwdc/2015/?id=605) 和其 [DemoBots：使用 SpriteKit 和 GameplayKit 範例應用程式建立跨平臺遊戲](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179) 。

## <a name="scenekit"></a>SceneKit

場景套件是3D 場景圖形 API，可簡化使用3D 圖形的程式。 它是在 OS X 10.8 中首次引進，現在已進入 iOS 8。 使用場景套件建立沉浸式3D 視覺效果和休閒3D 遊戲並不需要使用 OpenGL 的專業知識。 場景套件是以常見的場景圖形概念為基礎，可將 OpenGL 和 OpenGL ES 的複雜度抽象化，讓您很容易就能將3D 內容新增至應用程式。 但是，如果您是 OpenGL 專家，場景套件也有絕佳的支援，可直接與 OpenGL 系結。 它也包含許多可補充3D 圖形的功能，例如物理，並與其他數個 Apple 架構（例如核心動畫、核心影像和 Sprite 套件）非常妥善地整合。

如需詳細資訊，請參閱我們的 [SceneKit](~/ios/platform/gaming/scenekit.md) 檔。

### <a name="scenekit-changes"></a>SceneKit 變更

Apple 已將下列新功能新增至 SceneKit for iOS 9：

- Xcode 現在提供場景編輯器，可讓您直接從 Xcode 內編輯場景，以快速建立遊戲和互動式3D 應用程式。
- `SCNView`和 `SCNSceneRenderer` 類別可以用來在支援的 iOS 裝置) 上啟用金屬轉譯 (。
- `SCNAudioPlayer`和 `SCNNode` 類別可以用來新增空間音效效果，以自動追蹤播放程式位置至 iOS 應用程式。

如需詳細資訊，請參閱我們的 [SceneKit 檔](~/ios/platform/introduction-to-ios8.md#scenekit) 和 Apple 的 [SceneKit 架構參考](https://developer.apple.com/library/prerelease/ios/documentation/SceneKit/Reference/SceneKit_Framework/index.html#//apple_ref/doc/uid/TP40012283) 和 [Fox：使用 Xcode 場景編輯器範例專案建立 SceneKit 遊戲](https://developer.apple.com/library/prerelease/ios/samplecode/Fox/Introduction/Intro.html#//apple_ref/doc/uid/TP40016154) 。

## <a name="spritekit"></a>SpriteKit

來自 Apple 的2D 遊戲架構的 Sprite 套件，在 iOS 8 和 OS X Yosemite 中有一些有趣的新功能。 這些包括與場景套件、著色器支援、光源、陰影、條件約束、標準地圖產生和物理增強功能的整合。 尤其是，新的物理功能讓您很容易就能將實際效果新增到遊戲中。

如需詳細資訊，請參閱我們的 [SpriteKit](~/ios/platform/gaming/spritekit.md) 檔。

### <a name="spritekit-changes"></a>SpriteKit 變更

Apple 已將下列新功能新增至 SpriteKit for iOS 9：

- 使用類別自動追蹤玩家位置的空間音效效果 `SKAudioNode` 。
- Xcode 現在提供場景編輯器和動作編輯器，可讓您輕鬆地進行2D 遊戲和應用程式建立。
- 使用 () 物件的新攝影機節點，輕鬆地滾動遊戲支援 `SKCameraNode` 。
- 在支援裸機的 iOS 裝置上，即使您已經使用自訂的 OpenGL ES 著色器，SpriteKit 也會自動使用它來呈現。

如需詳細資訊，請參閱我們的 [SpriteKit 檔](~/ios/platform/introduction-to-ios8.md#spritekit) ： Apple 的 [SpriteKit 架構參考](https://developer.apple.com/library/prerelease/ios/documentation/SpriteKit/Reference/SpriteKitFramework_Ref/index.html#//apple_ref/doc/uid/TP40013041) 及其 [DemoBots：使用 SpriteKit 和 GameplayKit 範例應用程式建立跨平臺遊戲](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179) 。

## <a name="summary"></a>摘要

本文涵蓋了 iOS 9 針對您的 Xamarin iOS 應用程式所提供的新遊戲功能。
它引進了 GameplayKit 和模型 i/o;金屬的主要增強功能;以及 SceneKit 和 SpriteKit 的新功能。

## <a name="related-links"></a>相關連結

- [iOS 9 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS9)
- [適用于開發人員的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [iOS 9。0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)