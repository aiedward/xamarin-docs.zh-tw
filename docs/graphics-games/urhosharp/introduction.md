---
title: UrhoSharp 簡介
description: 本檔說明 UrhoSharp 應用程式的基本結構，以及示範如何使用 UrhoSharp 的各種指南和範例應用程式的連結。
ms.prod: xamarin
ms.assetid: 18041443-5093-4AF7-8B20-03E00478EF35
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 37540053cee03a83582fe19ffb1dcf9e1cf4564c
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73011735"
---
# <a name="introduction-to-urhosharp"></a>UrhoSharp 簡介

![UrhoSharp 標誌](introduction-images/urhosharp-icon.png)

UrhoSharp 是適用于 Xamarin 和 .NET 開發人員的強大3D 遊戲引擎。  這與 Apple 的 SceneKit 和 SpriteKit 有相似之處，並包含物理、流覽、網路功能等等，同時仍然跨平臺。

它是 .NET 與[Urho3D](https://urho3d.github.io/)引擎的系結，可讓開發人員使用相同的程式碼基底，撰寫以 Android、IOS、Windows 和 Mac 為目標的跨平臺程式碼，而且可以同時轉譯為 OpenGL 和 Direct3D 系統。

UrhoSharp 是有許多現成功能的遊戲引擎：

- 強大的3D 圖形呈現
- 物理模擬（使用專案符號連結庫）
- 場景處理
- Await/Async 支援
- 易記動作 API
- 2D 整合至3D 場景
- 使用 FreeType 呈現字型
- 用戶端和伺服器網路功能
- 匯入各式各樣的資產（使用開啟的資產庫）
- 導覽網格和 pathfinding （使用重新轉換/繞道）
- 用於碰撞偵測的凸殼產生（使用 StanHull）
- 音訊播放（使用**libvorbis**）

## <a name="get-started"></a>開始使用

UrhoSharp 可輕鬆地散佈為[NuGet 套件](https://www.nuget.org/)，而且可以新增至您C#的或F#以 Windows、Mac、Android 或 iOS 為目標的專案。  NuGet 隨附于執行您的程式所需的程式庫，以及引擎所使用的基本資產（CoreData）。

### <a name="urho-as-a-portable-class-library"></a>Urho 做為可移植的類別庫

您可以從平臺特定的專案或可移植的類別庫專案取用 Urho 套件，讓您在所有平臺上重複使用所有程式碼。  這表示您必須在每個平臺上執行的工作，就是撰寫平臺特定的進入點，然後將控制權轉移給您的共用遊戲程式碼。

### <a name="samples"></a>範例

您可以藉由在 Visual Studio for Mac 或 Visual Studio 的範例解決方案中開啟，以取得 Urho 功能的感受：

[https://github.com/xamarin/urho-samples](https://github.com/xamarin/urho-samples)

預設解決方案包含適用于 Android、iOS、Windows 和 Mac 的專案。  我們已將該解決方案結構化，讓我們有一個小型平臺特定的啟動器，而且所有範例程式碼和遊戲程式碼都位於可移植的類別庫中，說明如何在所有平臺上將程式碼重複使用發揮到最大。

如需如何建立您自己的解決方案的詳細資訊，請參閱[Urho 和您的平臺](~/graphics-games/urhosharp/platform/index.md)頁面。

因為所有的範例都共用一組通用的使用者介面元素，所以這些範例已抽象化此檔案中的基本設定：

[https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/Core/Sample.cs](https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/Core/Sample.cs)

這會提供一個範例基類，此類別可處理一些基本按鍵和觸控事件、設置相機、提供基本的使用者介面專案，並讓每個範例專注于所展示的特定功能。

下列範例顯示引擎能夠執行的作業：

- [Samply 遊戲](https://github.com/xamarin/urho-samples/tree/master/SamplyGame)一個簡單的 ShootySkies 複製。

另一個範例會顯示每個範例的個別屬性。

## <a name="basic-structure"></a>基本結構

您的遊戲應將 `Application` 類別子類別化，這是您在其中設定遊戲（在 `Setup` 方法上）並啟動遊戲（在 `Start` 方法中）的地方。  然後您會建立主要的使用者介面。  我們將逐步解說一個小範例，其中顯示設定3D 場景的 Api、一些 UI 專案，以及附加簡單的行為。

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

如果您執行此應用程式，您很快就會發現執行時間有關于資產的抱怨。  您需要做的是在專案中建立一個以特殊目錄名稱 "Data" 為開頭的階層，然後在這裡面，將您在程式中參考的資產放在其中。  接著，您必須在每個資產的專案屬性中設定 [複製到輸出目錄]，以在 [更新時才複製]，以確保您的資料位於該處。

讓我們解釋這裡的情況。

若要啟動您的應用程式，您可以呼叫引擎初始化函式，然後建立應用程式類別的新實例，如下所示：

```csharp
new MySample().Run();
```

執行時間將會為您叫用 `Setup` 和 `Start` 方法。  如果您覆寫 `Setup` 則可以設定引擎參數（在此範例中不會顯示）。

您必須覆寫 `Start`，因為這將會啟動您的遊戲。  在此方法中，您將會載入您的資產、連接事件處理常式、設定您的場景，並啟動任何您想要的動作。  在我們的範例中，我們會建立一些 UI 來向使用者顯示，以及設定3D 場景。

下列程式碼片段會使用 UI 架構來建立文字專案，並將其新增至您的應用程式：

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

UI 架構是提供非常簡單的遊戲內使用者介面，其運作方式是將新的節點加入至 `UI.Root` 節點。

範例的第二個部分會將主要場景進行設置。  這牽涉到幾個步驟：建立3D 場景、在螢幕中建立3D 方塊、新增光源、攝影機和一個視口。  在[場景、節點、元件和相機](~/graphics-games/urhosharp/using.md#scenenodescomponentsandcameras)一節中，將會更詳細地探索這些功能。

範例的第三個部分會觸發幾個動作。  動作是描述特定效果的配方，一旦建立之後，就可以在 `Node` 上呼叫 `RunActionAsync` 方法，依需求節點來執行。

第一個動作會調整具有跳動效果的方塊，而第二個動作則會永久旋轉方塊：

```csharp
await boxNode.RunActionsAsync(
    new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1)));
```

上圖顯示我們所建立的第一個動作是 `ScaleTo` 動作，這只是一個配方，表示您想要將第二個值調整為一個節點的小數值屬性。  此動作接著會圍繞緩動動作（`EaseBounceOut` 動作）加以包裝。  「緩動」動作會扭曲動作的線性執行並套用效果，在此情況下，它會提供跳動的效果。
因此，我們的配方可以撰寫為：

```csharp
var recipe = new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1));
```

一旦建立好配方，我們就會執行此配方：

```csharp
await boxNode.RunActionsAsync (recipe)
```

Await 表示在動作完成時，會想要繼續執行這一行之後。  動作完成後，我們會觸發第二個動畫。

[使用 UrhoSharp](~/graphics-games/urhosharp/using.md)檔深入探索 Urho 背後的概念，以及如何建立程式碼的結構來打造遊戲。

## <a name="copyrights"></a>版權所有

本檔包含 Xamarin Inc. 的原始內容，但會廣泛地從 Urho3D 專案的開放原始碼檔中進行繪製，並包含 Cocos2D 專案的螢幕擷取畫面。
