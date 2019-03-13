---
title: 簡介 UrhoSharp
description: 本文件說明 UrhoSharp 應用程式和各種的指南和範例應用程式，示範如何使用 UrhoSharp 連結的基本結構。
ms.prod: xamarin
ms.assetid: 18041443-5093-4AF7-8B20-03E00478EF35
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: a3e14ebca961e828fc578035adaca5ba2a809438
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2018
ms.locfileid: "34783551"
---
# <a name="an-introduction-to-urhosharp"></a>簡介 UrhoSharp

![UrhoSharp 標誌](introduction-images/urhosharp-icon.png)

UrhoSharp 是 Xamarin 和.NET 開發人員的強大 3D 遊戲引擎。  它是在精神與 Apple 的 SceneKit 和 SpriteKit 類似，包括物理學，瀏覽、 網路和許多更時仍然正在進行跨平台。

它是一個.NET 繫結[Urho3D](http://urho3d.github.io/)引擎，可讓開發人員撰寫跨平台程式碼，可以將目標 Android、 iOS、 Windows 和 Mac 使用相同程式碼基底，並可以轉譯為 OpenGL 和 Direct3D 的系統。

UrhoSharp 是功能的具有許多現成的遊戲引擎：

- 功能強大的 3D 圖形呈現
- [物理模擬](https://developer.xamarin.com/api/namespace/Urho.Physics/)（使用的項目符號的程式庫）
- [場景處理](https://developer.xamarin.com/api/type/Urho.Scene/)
- Await/非同步支援
- [好記的動作的 API](https://developer.xamarin.com/api/namespace/Urho.Actions/)
- [2D 到 3D 場景的整合](https://developer.xamarin.com/api/namespace/Urho.Urho2D/)
- [使用 FreeType 字型呈現](https://developer.xamarin.com/api/type/Urho.Gui.FontFaceFreeType/)
- [用戶端和網路功能的伺服器](https://developer.xamarin.com/api/namespace/Urho.Network/)
- [匯入各種不同的資產](https://developer.xamarin.com/api/namespace/Urho.Resources/)（具有開啟資產的程式庫）
- [瀏覽網格和 pathfinding](https://developer.xamarin.com/api/namespace/Urho.Navigation/) （使用繞道重新轉型）
- [碰撞偵測的凸殼產生](https://developer.xamarin.com/api/type/Urho.Physics.CollisionShape/)（使用 StanHull）
- [音訊播放](https://developer.xamarin.com/api/namespace/Urho.Audio/)(使用**libvorbis**)

## <a name="getting-started"></a>快速入門

形式方便地散發 UrhoSharp [NuGet 套件](https://www.nuget.org/)，可以將它新增至您的 C# 或 F # 專案 Windows、 Mac、 Android 或 iOS 為目標。  NuGet 隨附於執行您的程式所需的程式庫以及由引擎所使用的基本資產 (CoreData)。

### <a name="urho-as-a-portable-class-library"></a>Urho 為可攜式類別程式庫

從平台特定專案，或從可攜式類別庫專案中，可讓您跨所有平台，所有程式碼重複使用，就可以取用 Urho 封裝。  這表示您必須在每個平台上執行所有是撰寫平台的特定進入點，並再將控制權移轉給您共用的遊戲程式碼。

### <a name="samples"></a>範例

您可以取得 Urho 功能想要一窺 Visual Studio for Mac 或 Visual Studio 中開啟的解決方案範例：

[https://github.com/xamarin/urho-samples](https://github.com/xamarin/urho-samples)

預設的方案包含專案適用於 Android、 iOS、 Windows 和 mac。  我們有安排該解決方案，可讓我們有小型的平台特定的啟動器，，所有的範例程式碼和遊戲的程式碼位於可攜式類別庫，說明如何將程式碼重複使用最大化跨所有平台。

請參閱[Urho 和您的平台](~/graphics-games/urhosharp/platform/index.md)頁面，如需有關如何建立您自己的解決方案。

因為所有的範例共用一組常用的使用者介面項目，範例擷取了基本的設定，此檔案中：

[https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/Core/Sample.cs](https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/Core/Sample.cs)

這會提供範例的基底類別，會處理一些基本的按鍵輸入和觸控事件，設定網路攝影機，提供基本的使用者介面項目，而這可讓每個範例，以專注於特定展示的功能。

下列範例顯示什麼引擎所能執行：

- [Samply 遊戲](https://github.com/xamarin/urho-samples/tree/master/SamplyGame)ShootySkies 的簡單複製。

而其他樣本會顯示每個範例的個別屬性。

## <a name="basic-structure"></a>基本結構

您的遊戲應該子類別 [`Application`](https://developer.xamarin.com/api/type/Urho.Application/)
類別，這是您將在其中設定您的遊戲 (在[ `Setup` ](https://developer.xamarin.com/api/member/Urho.Application.Setup/)方法) 並啟動您的遊戲 (在[ `Start` ](https://developer.xamarin.com/api/member/Urho.Application.Start)方法)。  然後，您會建構您的主要使用者介面。  我們將逐步解說一個小的範例，顯示設定 3D 場景，部分 UI 項目和簡單行為附加至該 Api。

```csharp
class MySample : Application {
    protected override void Start ()
    {
        CreateScene ();
        Input.KeyDown += (args) => {
            if (args.Key == Key.Esc) Exit ();
        };
    }

    async void CreateScene()
    {
        // UI text
        var helloText = new Text()
        {
            Value = "Hello World from MySample",
            HorizontalAlignment = HorizontalAlignment.Center,
            VerticalAlignment = VerticalAlignment.Center
        };
        helloText.SetColor(new Color(0f, 1f, 1f));
        helloText.SetFont(
            font: ResourceCache.GetFont("Fonts/Font.ttf"),
            size: 30);
        UI.Root.AddChild(helloText);

        // Create a top-level scene, must add the Octree
        // to visualize any 3D content.
        var scene = new Scene();
        scene.CreateComponent<Octree>();
        // Box
        Node boxNode = scene.CreateChild();
        boxNode.Position = new Vector3(0, 0, 5);
        boxNode.Rotation = new Quaternion(60, 0, 30);
        boxNode.SetScale(0f);
        StaticModel modelObject = boxNode.CreateComponent<StaticModel>();
        modelObject.Model = ResourceCache.GetModel("Models/Box.mdl");
        // Light
        Node lightNode = scene.CreateChild(name: "light");
        lightNode.SetDirection(new Vector3(0.6f, -1.0f, 0.8f));
        lightNode.CreateComponent<Light>();
        // Camera
        Node cameraNode = scene.CreateChild(name: "camera");
        Camera camera = cameraNode.CreateComponent<Camera>();
        // Viewport
        Renderer.SetViewport(0, new Viewport(scene, camera, null));
        // Perform some actions
        await boxNode.RunActionsAsync(
            new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1)));
        await boxNode.RunActionsAsync(
            new RepeatForever(new RotateBy(duration: 1,
                deltaAngleX: 90, deltaAngleY: 0, deltaAngleZ: 0)));
     }
}
```

如果您執行此應用程式時，您很快就會發現執行階段所抱怨您的資產不存在。  您要做為開頭的特殊的目錄名稱 「 資料 」，在專案中建立階層，並在此，您會將您在程式中參考的資產。  然後，您必須設定每個資產 」 複製到輸出目錄 」 到 「 複製如果新版 」 的項目屬性，如此可確保您的資料是否有。

讓我們為您解說這裡。

若要啟動您的應用程式，您會呼叫引擎初始化函式，後面接著建立您的應用程式類別，像這樣的新執行個體：

```csharp
new MySample().Run();
```

執行階段會叫用`Setup`和`Start`為您的方法。  如果您覆寫`Setup`您可以設定引擎參數 （不在此範例中顯示）。

您必須覆寫`Start`，這會啟動您的遊戲。  在此方法會載入您的資產、 連接事件處理常式、 設定場景和啟動您想要的任何動作。  在我們的範例中，我們都建立少量顯示給使用者，以及設定 3D 場景的 UI。

下列程式碼片段會使用 UI 架構，來建立文字項目，並將它新增至您的應用程式：

```csharp
// UI text
var helloText = new Text()
{
    Value = "Hello World from UrhoSharp",
    HorizontalAlignment = HorizontalAlignment.Center,
    VerticalAlignment = VerticalAlignment.Center
};
helloText.SetColor(new Color(0f, 1f, 1f));
helloText.SetFont(
    font: ResourceCache.GetFont("Fonts/Font.ttf"),
    size: 30);
UI.Root.AddChild(helloText);
```

UI 架構有提供非常簡單的遊戲中使用者介面，以及其運作方式是加入至新的節點[ `UI.Root` ](https://developer.xamarin.com/api/property/Urho.Gui.UI.Root/)節點。

我們的範例設定的第二個部分，主要的場景。  這牽涉到幾個步驟，建立 3D 場景，請在畫面中，建立 3D 方塊加入光線、 觀景窗和檢視區。  這些探索一節中詳細[場景、 節點、 元件和相機](~/graphics-games/urhosharp/using.md#scenenodescomponentsandcameras)。

我們的範例中的第三個部分會觸發幾個動作。  動作是藉由呼叫隨選的節點可以執行配方，說明特定的效果，並一次建立他們[ `RunActionAsync` ](https://developer.xamarin.com/api/member/Urho.Node.RunActionsAsync)方法`Node`。

第一個動作縮放方塊彈跳效果和第二個旋轉方塊不限次數：

```csharp
await boxNode.RunActionsAsync(
    new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1)));
```

上述說明我們所建立的第一個動作有何[ `ScaleTo` ](https://developer.xamarin.com/api/type/Urho.Actions.ScaleTo/)動作，這是只是配方，指出您想要調整規模，以便針對其中一個值的第二個節點的小數位數屬性。  此動作接著要包裝的加/減速的動作[ `EaseBounceOut` ](https://developer.xamarin.com/api/type/Urho.Actions.EaseBounceInOut/)動作。  加/減速動作會扭曲線性執行動作，然後套用效果，在此情況下提供外彈跳效果。
讓我們配方可以寫成：

```csharp
var recipe = new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1));
```

一旦建立此配方，我們會執行此配方：

```csharp
await boxNode.RunActionsAsync (recipe)
```

Await 表示會想要的動作完成時繼續此行之後執行。  動作完成之後我們會觸發第二個動畫。

[使用 UrhoSharp](~/graphics-games/urhosharp/using.md)深入探索文件 Urho 及如何建構您的程式碼，來建置遊戲的基本概念。

## <a name="copyrights"></a>著作權

這份文件會包含從 Xamarin Inc.的原始內容，但廣泛繪製 Urho3D 專案的開放原始碼文件，並包含 Cocos2D 專案的螢幕擷取畫面。

