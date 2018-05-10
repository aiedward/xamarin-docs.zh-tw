---
title: UrhoSharp 簡介
description: 這提供 UrhoSharp 背後的概念簡介
ms.prod: xamarin
ms.assetid: 18041443-5093-4AF7-8B20-03E00478EF35
author: charlespetzold
ms.author: chape
ms.date: 03/29/2017
ms.openlocfilehash: 6fb53aa6d4bad8f8fcae8608ab0192af15fe87b1
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
---
# <a name="an-introduction-to-urhosharp"></a>UrhoSharp 簡介

_這提供 UrhoSharp 背後的概念簡介_

![UrhoSharp 標誌](introduction-images/urhosharp-icon.png)

UrhoSharp 是一個功能強大的 3D 遊戲引擎 Xamarin 和.NET 開發人員。  它是在夠用類似 Apple SceneKit 與 SpriteKit，包含物理、 瀏覽、 網路和更多時仍跨平台。

它會是.NET 繫結[Urho3D](http://urho3d.github.io/)引擎，可讓開發人員撰寫跨平台程式碼可設為目標 Android、 iOS、 Windows 和 Mac 的相同程式碼基底和可以轉譯為 Direct3D 和 OpenGL 系統。

UrhoSharp 是功能的遊戲引擎具有許多現成:

- 功能強大的 3D 圖形呈現
- [物理模擬](https://developer.xamarin.com/api/namespace/Urho.Physics/)（使用項目符號的程式庫）
- [場景處理](https://developer.xamarin.com/api/type/Urho.Scene/)
- Await/非同步支援
- [好記的動作的 API](https://developer.xamarin.com/api/namespace/Urho.Actions/)
- [2D 整合的 3D 場景](https://developer.xamarin.com/api/namespace/Urho.Urho2D/)
- [與 FreeType 字型轉譯](https://developer.xamarin.com/api/type/Urho.Gui.FontFaceFreeType/)
- [用戶端和網路功能的伺服器](https://developer.xamarin.com/api/namespace/Urho.Network/)
- [匯入各種不同的資產](https://developer.xamarin.com/api/namespace/Urho.Resources/)（具有開啟資產的程式庫）
- [瀏覽網狀結構和 pathfinding](https://developer.xamarin.com/api/namespace/Urho.Navigation/) （使用繞道重新轉型）
- [衝突偵測的凸殼產生](https://developer.xamarin.com/api/type/Urho.Physics.CollisionShape/)（使用 StanHull）
- [音訊播放](https://developer.xamarin.com/api/namespace/Urho.Audio/)(與**libvorbis**)

## <a name="getting-started"></a>快速入門

以方便地散發 UrhoSharp [NuGet 封裝](https://www.nuget.org/)，可以將它新增至您的 C# 或 F # 專案目標 Windows、 Mac、 Android 或 iOS。  NuGet 隨附的程式庫，才能執行您的程式，以及由引擎所使用的基本資產 (發生 CoreData)。

### <a name="urho-as-a-portable-class-library"></a>Urho 為可攜式類別程式庫

可以使用 Urho 封裝，在特定平台專案中，或從可攜式類別庫專案，讓您跨所有平台，所有的程式碼重複使用。  這表示，您必須在每個平台上執行則要撰寫您的平台特定的進入點，並再將控制權轉移到共用的遊戲程式碼。

### <a name="samples"></a>範例

您可以取得功用 Urho 的功能適用於 Mac 的 Visual Studio 或 Visual Studio 中開啟的方案範例：

[https://github.com/xamarin/urho-samples](https://github.com/xamarin/urho-samples)

預設方案包含專案的 Android、 iOS、 Windows 和 mac。  我們有結構化該方案，我們有很小的平台特定的啟動器，且所有的範例程式碼和遊戲的程式碼位於可攜式類別庫，說明如何跨所有平台最大化程式碼重複使用。

請參閱[Urho 和您的平台](~/graphics-games/urhosharp/platform/index.md)如需有關如何建立您自己的解決方案的頁面。

因為所有範例共用一組常用的使用者介面項目，這些範例有抽象化此檔案中的基本安裝：

[https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/Core/Sample.cs](https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/Core/Sample.cs)

這會提供範例的基底類別會處理一些基本的按鍵輸入和觸控數位相機龐大的事件，提供基本的使用者介面項目，而這可讓每個範例專注於正在其中顯示的特定功能。

下列範例顯示什麼引擎可以這樣做：

- [Samply 遊戲](https://github.com/xamarin/urho-samples/tree/master/SamplyGame)ShootySkies 的簡單複製。

而其他範例會顯示每個範例的個別屬性。

## <a name="basic-structure"></a>基本結構

您的遊戲應該子類別[ `Application` ](https://developer.xamarin.com/api/type/Urho.Application/)類別，這是您將會在其中安裝您的遊戲 (上[ `Setup` ](https://developer.xamarin.com/api/member/Urho.Application.Setup/)方法) 和啟動遊戲 (在[ `Start` ](https://developer.xamarin.com/api/member/Urho.Application.Start)方法)。  然後您可以建構您的主要使用者介面。  我們將逐步解說示範使用 Api 來設定的 3D 場景，部分 UI 項目和簡單行為附加至一小部分範例。

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

如果您執行此應用程式，您很快就會發現執行階段抱怨資產不存在。  您需要使用特殊的目錄名稱 「 資料 」，開始在專案中建立階層，而且內，您會將您在程式中參考的資產。  然後，您必須設定為每個資產"複製到輸出目錄 」 到 「 複製如果較新 」 的項目屬性，如此可確保您的資料是否有。

讓我們為您解說這裡。

若要啟動您的應用程式，您會呼叫引擎初始化函式，後面接著建立您的應用程式類別，像這樣的新執行個體：

```csharp
new MySample().Run();
```

執行階段會叫用`Setup`和`Start`為您的方法。  如果您覆寫`Setup`您可以設定引擎參數 （不在此範例中顯示）。

您必須覆寫`Start`因為這會啟動您的遊戲。  在此方法將載入您的資產、 連接事件處理常式、 設定場景和啟動您想要的任何動作。  在我們的範例中，我們都建立一些 UI，以顯示使用者以及設定 3D 場景。

下列程式碼片段會建立的文字項目，並將它加入至您的應用程式中使用 UI 架構：

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

UI 架構是否有提供非常簡單的遊戲中使用者介面，和其運作方式是加入新的節點至[ `UI.Root` ](https://developer.xamarin.com/api/property/Urho.Gui.UI.Root/)節點。

我們的範例安裝的第二部分主要場景。  這牽涉到步驟，建立的 3D 場景，在畫面中，建立 3D 方塊加入光線、 攝影機和檢視區的數的字。  這些探索一節中詳細說明[場景、 節點、 元件和相機](~/graphics-games/urhosharp/using.md#scenenodescomponentsandcameras)。

我們的範例的第三個部分會觸發幾個動作。  動作是藉由呼叫上指定的節點可以執行，說明特定的效果，並一次建立它們的配方[ `RunActionAsync` ](https://developer.xamarin.com/api/member/Urho.Node.RunActionsAsync)方法`Node`。

第一個動作縮放彈跳效果方塊和第二個旋轉方塊不限次數：

```csharp
await boxNode.RunActionsAsync(
    new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1)));
```

上述顯示我們建立的第一個動作的方式是[ `ScaleTo` ](https://developer.xamarin.com/api/type/Urho.Actions.ScaleTo/)動作，這是只是注定，指出您想要調整向其中一個值的第二個節點的小數位數屬性。  這個動作依次要包裝加/減速的動作， [ `EaseBounceOut` ](https://developer.xamarin.com/api/type/Urho.Actions.EaseBounceInOut/)動作。  加/減速動作會扭曲線性執行動作，然後套用效果，在此情況下提供彈跳外效果。
讓我們配方可以寫成：

```csharp
var recipe = new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1));
```

一旦已建立的配方，我們會執行配方：

```csharp
await boxNode.RunActionsAsync (recipe)
```

Await 表示會想要繼續執行這一行後面，動作完成時。  動作完成之後我們將觸發程序的第二個動畫。

[使用 UrhoSharp](~/graphics-games/urhosharp/using.md)文件會探索更深入地 Urho 如何建構您的程式碼建置遊戲的基本概念。

## <a name="copyrights"></a>著作權

這份文件會包含 Xamarin Inc，從原始內容但廣泛繪製從 Urho3D 專案的開放原始碼文件，包含從 Cocos2D 專案的螢幕擷取畫面。

### <a name="related-links"></a>相關連結

- [地球表面活頁簿](https://developer.xamarin.com/workbooks/graphics/urhosharp/planetearth/planetearth.workbook)
- [瀏覽座標活頁簿](https://developer.xamarin.com/workbooks/graphics/urhosharp/coordinates/ExploringUrhoCoordinates.workbook)
