---
title: iOS 遊戲 Api 在 Xamarin.iOS 中
description: 本文章涵蓋 iOS 9，可用來改善您的 Xamarin.iOS 遊戲圖形和音訊功能所提供的新遊戲增強功能。
ms.prod: xamarin
ms.assetid: 958D38FD-9240-482E-9A42-D6671ED8F2B0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: d8a531e495a19be7437d4a600e758028594248ab
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116000"
---
# <a name="ios-gaming-apis-in-xamarinios"></a>iOS 遊戲 Api 在 Xamarin.iOS 中

_本文章涵蓋 iOS 9，可用來改善您的 Xamarin.iOS 遊戲圖形和音訊功能所提供的新遊戲增強功能。_

Apple 已改進數個技術在 iOS 9 遊戲 Api 可讓您更輕鬆地在 Xamarin.iOS 應用程式中實作的遊戲圖形和音訊。
這些包括這兩種方便開發的高階架構和 iOS 裝置的 GPU 經改善的速度和圖形功能的力量。

[![](images/flocking01.png "執行群群聚的應用程式範例")](images/flocking01.png#lightbox)

這包括 GameplayKit、 ReplayKit、 模型 I/O、 MetalKit 和金屬效能著色器以及金屬、 SceneKit 和 SpriteKit 的新的增強功能。

本文將介紹的各種方式來改善您的 Xamarin.iOS 遊戲，透過 iOS 9 的新遊戲增強功能：

## <a name="introducing-gameplaykit"></a>簡介 GameplayKit

Apple 的新 GameplayKit 架構提供一組技術，可讓您更輕鬆地建立適用於 iOS 裝置的遊戲藉由減少重複性的通用實作所需的程式碼數量。 GameplayKit 提供工具，快速開發遊戲的機制，可以輕鬆地結合與圖形引擎 （例如 SceneKit 或 SpriteKit），以提供已完成的遊戲。

GameplayKit 包含數個，常見，例如遊戲播放的演算法：

- 行為為基礎，可讓您定義的移動和 AI 會自動追求的目標的代理程式模擬。
- Minmax 人工智慧的回合制遊戲。
- 模糊的原因，以提供緊急的行為與資料驅動型遊戲邏輯規則系統。

此外，GameplayKit 會使用一種模組化架構，提供下列功能到遊戲開發採用 建置組塊：

- 處理複雜的程序性程式碼的狀態機器為基礎的遊戲中的系統。
- 提供隨機遊戲和不可預測性的工具，不會引起調試問題。
- 可重複使用、 元件化的實體基礎架構。

若要深入了解 GameplayKit，請參閱 Apple [Gameplaykit 程式設計指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/GameplayKit_Guide/index.html#//apple_ref/doc/uid/TP40015172)並[GameplayKit Framework 參考](https://developer.apple.com/library/prerelease/ios/documentation/GameplayKit/Reference/GameplayKit_Framework/index.html#//apple_ref/doc/uid/TP40015199)。

## <a name="gameplaykit-examples"></a>GameplayKit 範例

讓我們看一下使用遊戲套件的 Xamarin.iOS 應用程式中實作一些簡單的遊戲機制。

### <a name="pathfinding"></a>Pathfinding

Pathfinding 是遊戲 AI 項目的能力，找出其周圍遊戲面板的方式。
例如，尋找其透過層層的方式或透過第一位人員-射擊世界地形模型的 3D 字元 2D 敵人。

請考慮下列對應：

[![](images/gkpathfindpath.png "範例 pathfinding 對應")](images/gkpathfindpath.png#lightbox)

使用 pathfinding 此C#程式碼可以找到透過對應的方法：

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

### <a name="classical-expert-system"></a>傳統的專家系統

下列程式碼片段的C#程式碼會示範如何使用 GameplayKit，實作傳統的專家系統：

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

根據一組指定的規則 (`GKRule`) 和一組已知的輸入，專家系統 (`GKRuleSystem`) 會建立可預測的輸出 (`fizzbuzz`如上述範例)。

### <a name="flocking"></a>群群聚

群群聚，可讓一群 AI 控制遊戲的實體，以做的 flock，群組會移動和潛在客戶實體，例如的航班鳥 flock 或學校的魚 swimming 的動作的回應。

下列程式碼片段的C#程式碼會實作的圖形顯示使用 GameplayKit 和 SpriteKit 群群聚行為：

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

接下來，在檢視控制器中實作此場景：

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

執行時，一些動畫 _"Boids"_ 將 flock 周圍我們手指點選：

[![](images/flocking01.png "稍微動畫的 Boids 將 flock 周圍手指點選")](images/flocking01.png#lightbox)

### <a name="other-apple-examples"></a>其他 Apple 範例

除了上述的範例，Apple 提供下列的範例應用程式可以以轉碼C#和 Xamarin.iOS:

- [FourInARow： 使用對手 AI GameplayKit Minmax 策略家](https://developer.apple.com/library/prerelease/ios/samplecode/FourInARow/Introduction/Intro.html#//apple_ref/doc/uid/TP40016142)
- [AgentsCatalog： 中 GameplayKit 使用代理程式系統](https://developer.apple.com/library/prerelease/ios/samplecode/AgentsCatalog/Introduction/Intro.html#//apple_ref/doc/uid/TP40016141)
- [DemoBots： 建立使用 SpriteKit 和 GameplayKit 跨平台遊戲](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179)

## <a name="metal"></a>Metal

在 iOS 9 中，Apple 已對有些改變與新增裸機 gpu 提供低額外負荷的存取。 使用金屬您可以最大化的圖形和您的 iOS 應用程式的運算的潛能。

金屬架構包含下列新功能：

- 新的私用與深度樣板紋理 OS x。
- 改善的陰影品質深度限制，並分開前端和後樣板的值。
- 金屬陰影的語言和金屬標準程式庫的改進。
- 計算著色器支援更廣泛的像素格式。

### <a name="the-metalkit-framework"></a>MetalKit Framework

MetalKit 架構提供一組公用程式類別和減少的工作，才能在 iOS 應用程式中使用 Metal 的功能。 MetalKit 提供三個主要區域中的支援：

1. 從各種來源，包括常見的格式，例如 PNG、 JPEG、 KTX 和 PVR 載入非同步的紋理。
2. 輕鬆存取的模型 I/O 基礎裸機特定模型處理的資產。 這些功能有高度最佳化，可提供模型 I/O 網格和裸機緩衝區之間的有效率的資料傳輸。
3. 預先定義的裸機檢視和檢視管理可大幅降低顯示 iOS 應用程式中的圖形轉譯所需的程式碼數量。

若要深入了解 MetalKit，請參閱 Apple [MetalKit Framework 參考](https://developer.apple.com/library/prerelease/ios/documentation/MetalKit/Reference/MTKFrameworkReference/index.html#//apple_ref/doc/uid/TP40015356)， [Metal 程式設計指南](https://developer.apple.com/library/prerelease/ios/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221)， [Metal Framework 參考](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalFrameworkReference/index.html#//apple_ref/doc/uid/TP40014161)和[裸機陰影語言指南](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalShadingLanguageGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014364)。

### <a name="metal-performance-shaders-framework"></a>金屬能著色器架構

Metal 效能著色器架構提供高度最佳化組圖形和設定基礎的計算著色器以用於您裸機基礎的 iOS 應用程式。 每個架構特別微調 Metal 提供高效能的 Metal 效能著色器中的著色器支援 iOS Gpu。

藉由使用金屬效能著色器類別，您可以達到最高的效能可能在每個特定的 iOS GPU 上不需要為目標，並維護個別的程式碼基底。 金屬效能著色器可以搭配任何 Metal 的資源，例如紋理及緩衝區使用。

Metal 效能著色器架構提供一組常見的著色器，例如：

- **高斯柔邊**(`MPSImageGaussianBlur`)
- **Sobel 邊緣偵測**(`MPSImageSobel`)
- **映像長條圖**(`MPSImageHistogram`)

如需詳細資訊，請參閱 Apple [Metal 陰影語言指南](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalShadingLanguageGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014364)。

## <a name="introducing-model-io"></a>簡介模型 I/O

Apple 的模型 I/O 架構提供深入了解 3D 資產 （例如模型和其相關的資源）。 模型的 I/O 會提供您使用實體為基礎的資料，模型和光線，可以搭配 GameplayKit、 裸機和 SceneKit 的 iOS 遊戲。

使用模型的 I/O，您可以支援下列類型的工作：

- 匯入光源、 教材，網格資料、 相機設定和其他場景為基礎的資訊，從各種熱門的軟體和遊戲引擎格式。
- 處理或產生場景為基礎的資訊，例如可循序建立材質的天空國土或製作成網格光源。
- MetalKit、 與 SceneKit GLKit 有效率地載入轉譯的 GPU 緩衝區中的遊戲資產的運作方式。
- 場景為基礎的資訊匯出到各種熱門的軟體和遊戲引擎格式。

若要深入了解模型 I/O，請參閱 Apple 的[模型 I/O Framework 參考](https://developer.apple.com/library/prerelease/ios/documentation/ModelIO/Reference/ModelIO_Framework/index.html#//apple_ref/doc/uid/TP40015421)

## <a name="introducing-replaykit"></a>簡介 ReplayKit

Apple 的新 ReplayKit 架構可讓您輕鬆地將玩遊戲的方式記錄新增至您的 iOS 遊戲，並讓使用者快速且輕鬆地編輯和共用應用程式內的這段影片中。

如需詳細資訊，請參閱 Apple[會使用加入社交網路 ReplayKit 和 Game Center 的視訊](https://developer.apple.com/videos/wwdc/2015/?id=605)及其[DemoBots： 建置跨平台遊戲 SpriteKit 與 GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179)範例應用程式。

## <a name="scenekit"></a>SceneKit

Scenekit 是 3D 場景圖形 API，可簡化的 3D 圖形。 它最初是在 OS X 10.8，現在已有 iOS 8。 使用 Scenekit 建立擬真 3D 視覺效果和 3D 的休閒遊戲不需要 OpenGL 的專業知識。 在一般的場景圖形概念建置，Scenekit 複雜性抽象化的 OpenGL 和 OpenGL ES，因此很容易就能新增 3D 內容的應用程式。 不過，如果您是 OpenGL 專家，Scenekit 有繫結，直接與 OpenGL 也有絕佳的支援。 也包含許多功能可補充 3D 圖形，例如物理條件，並非常適合整合數個其他 Apple 的架構，例如 Core Animation、 Core 映像和 Sprite Kit。

如需詳細資訊，請參閱我們[SceneKit](~/ios/platform/gaming/scenekit.md)文件。

### <a name="scenekit-changes"></a>SceneKit 變更

Apple 已加入下列新功能 SceneKit 的 iOS 9:

- Xcode 現在提供場景編輯器，可讓您快速建置遊戲和互動式 3D 應用程式，藉由編輯從直接在 Xcode 中的場景。
- `SCNView`和`SCNSceneRenderer`類別可以用來啟用 （在支援的 iOS 裝置） 的裸機呈現。
- `SCNAudioPlayer`和`SCNNode`類別可用來新增自動追蹤在 iOS 應用程式的 播放程式位置的空間音訊效果。

如需詳細資訊，請參閱我們[SceneKit 文件](~/ios/platform/introduction-to-ios8.md#scenekit)與 Apple [SceneKit Framework 參考](https://developer.apple.com/library/prerelease/ios/documentation/SceneKit/Reference/SceneKit_Framework/index.html#//apple_ref/doc/uid/TP40012283)和[Fox： 建置 SceneKit 遊戲使用 Xcode 場景編輯器](https://developer.apple.com/library/prerelease/ios/samplecode/Fox/Introduction/Intro.html#//apple_ref/doc/uid/TP40016154)範例專案。

## <a name="spritekit"></a>SpriteKit

Spritekit，Apple 的 2D 遊戲架構有一些有趣的新功能，在 iOS 8 和 OS X Yosemite。 這些包括整合 Scenekit、 著色器的支援、 光源、 shadows、 條件約束、 法線貼圖產生與物理增強功能。 特別是，新的物理功能讓很容易在遊戲中加入實際的效果。

如需詳細資訊，請參閱我們[SpriteKit](~/ios/platform/gaming/spritekit.md)文件。

### <a name="spritekit-changes"></a>SpriteKit 的變更

Apple 已加入下列新功能 SpriteKit 的 iOS 9:

- 自動追蹤玩家的位置使用的空間音訊效果`SKAudioNode`類別。
- Xcode 現在功能的場景編輯器 」 和 「 動作編輯器，簡單的 2D 遊戲和應用程式建立。
- 很容易捲動遊戲支援，以新的相機節點 (`SKCameraNode`) 物件。
- 在支援 Metal 的 iOS 裝置，SpriteKit 會自動使用它進行轉譯，即使您已在使用自訂的 OpenGL ES 著色器。

如需詳細資訊，請參閱我們[SpriteKit 文件](~/ios/platform/introduction-to-ios8.md#spritekit)Apple [SpriteKit Framework 參考](https://developer.apple.com/library/prerelease/ios/documentation/SpriteKit/Reference/SpriteKitFramework_Ref/index.html#//apple_ref/doc/uid/TP40013041)及其[DemoBots： 建立使用 SpriteKit 跨平台遊戲和GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179)範例應用程式。

## <a name="summary"></a>總結

這篇文章已涵蓋新的 Xamarin.iOS 應用程式提供的遊戲功能該 iOS 9。
它引進 GameplayKit 和模型的 I/O;Metal; 主要增強功能以及 SceneKit 和 SpriteKit 的新功能。



## <a name="related-links"></a>相關連結

- [iOS 9 範例](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 的開發人員](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
