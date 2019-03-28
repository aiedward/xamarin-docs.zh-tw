---
title: 若要建置 3D 遊戲使用 UrhoSharp
description: 本文件提供概略 UrhoSharp，描述場景、 元件、 圖形、 相機、 動作、 使用者輸入、 音效以及更多。
ms.prod: xamarin
ms.assetid: D9BEAD83-1D9E-41C3-AD4B-3D87E13674A0
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 5e5c4f1545d39befde6574338ec4c1ca4037ad8b
ms.sourcegitcommit: a7170494e1975f0f1be547a45444752fd8e57819
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2019
ms.locfileid: "58507158"
---
# <a name="using-urhosharp-to-build-a-3d-game"></a>若要建置 3D 遊戲使用 UrhoSharp

撰寫您的第一個遊戲之前，您想要熟悉基本概念： 如何設定場景、 如何載入的資源 （這包含您的作品） 以及如何建立簡單的互動，您的遊戲。

<a name="scenenodescomponentsandcameras"/>

## <a name="scenes-nodes-components-and-cameras"></a>場景、 節點、 元件和相機

場景模型可以描述為元件為基礎的場景圖形中。 場景是由從根節點，這也代表整個場景的場景節點階層所組成。 每個[ `Node` ](https://developer.xamarin.com/api/type/Urho.Node/)有 3D 轉換 （位置、 旋轉和縮放比例）、 名稱、 識別碼、 加上任意數目的元件。  元件會將節點注入生命，他們可以製作新增的視覺表示法 ([`StaticModel`](https://developer.xamarin.com/api/type/Urho.StaticModel))，它們可發出聲音 ([`SoundSource`](https://developer.xamarin.com/api/type/Urho.Audio.SoundSource))，它們可以提供衝突界限，依此類推。

您可以建立您的場景和使用的安裝程式節點[Urho 編輯器](#urhoeditor)，或您可以從 C# 程式碼來執行動作。  在本文中我們將探討設定項目，您可以使用程式碼中，為這些逐步解說說明顯示在螢幕上所得到的必要項目

除了設定您的場景，您必須安裝[ `Camera` ](https://developer.xamarin.com/api/type/Urho.Camera/)，這是決定向使用者取得顯示的內容。

### <a name="setting-up-your-scene"></a>設定場景

您通常會建立此表單 Start 方法：

```csharp
var scene = new Scene ();
// Create the Octree component to the scene. This is required before
// adding any drawable components, or else nothing will show up. The
// default octree volume will be from -1000, -1000, -1000) to
//(1000, 1000, 1000) in world coordinates; it is also legal to place
// objects outside the volume but their visibility can then not be
// checked in a hierarchically optimizing manner
scene.CreateComponent<Octree> ();
// Create a child scene node (at world origin) and a StaticModel
// component into it. Set the StaticModel to show a simple plane mesh
// with a "stone" material. Note that naming the scene nodes is
// optional. Scale the scene node larger (100 x 100 world units)
var planeNode = scene.CreateChild("Plane");
planeNode.Scale = new Vector3 (100, 1, 100);
var planeObject = planeNode.CreateComponent<StaticModel> ();
planeObject.Model = ResourceCache.GetModel ("Models/Plane.mdl");
planeObject.SetMaterial(ResourceCache.GetMaterial("Materials/StoneTiled.xml"));
```

### <a name="components"></a>元件

轉譯 3D 物件、 音效播放、 物理學與指令碼的邏輯更新為全部啟用成節點建立不同的元件，藉由呼叫[ `CreateComponent<T>()` ](https://developer.xamarin.com/api/member/Urho.Node.CreateComponent%3CT%3E/p/Urho.CreateMode/System.UInt32/)。  例如，設定您的節點和淺色元件如下：

```csharp
// Create a directional light to the world so that we can see something. The
// light scene node's orientation controls the light direction; we will use
// the SetDirection() function which calculates the orientation from a forward
// direction vector.
// The light will use default settings (white light, no shadows)
var lightNode = scene.CreateChild("DirectionalLight");
lightNode.SetDirection (new Vector3(0.6f, -1.0f, 0.8f));
```

我們已經建立具有名稱的節點上方"`DirectionalLight`」，並設定方向，但沒有其他項目。  現在，我們可以將上述節點變成淺發出 節點中，藉由附加[ `Light` ](https://developer.xamarin.com/api/type/Urho.Light/)元件，與`CreateComponent`:

```csharp
var light = lightNode.CreateComponent<Light>();
```

建立元件`Scene`本身有特殊的角色： 實作整個場景的功能。 它們應該先建立其他元件，而且包含下列功能：

* [`Octree`](https://developer.xamarin.com/api/type/Urho.Octree/)： 實作資料分割的空間及加速可見性查詢。 沒有此 3D 物件無法轉譯。
* [`PhysicsWorld`](https://developer.xamarin.com/api/type/Urho.Physics.PhysicsWorld/)： 實作物理模擬。 例如物理元件[ `RigidBody` ](https://developer.xamarin.com/api/type/Urho.Physics.RigidBody/)或是[ `CollisionShape` ](https://developer.xamarin.com/api/type/Urho.Physics.CollisionShape/)可以無法正常運作如果沒有這麼做。
* [`DebugRenderer`](https://developer.xamarin.com/api/type/Urho.DebugRenderer/)： 實作偵錯幾何轉譯。

一般元件，例如[ `Light` ](https://developer.xamarin.com/api/type/Urho.Light)， [ `Camera` ](https://developer.xamarin.com/api/type/Urho.Camera)或 [`StaticModel`](https://developer.xamarin.com/api/type/Urho.StaticModel)
不應直接建立[ `Scene` ](https://developer.xamarin.com/api/type/Urho.Scene)，但而成子節點。

程式庫提供各種不同的元件，您可以附加至您的節點，讓它們實際運作： 使用者可見的項目 （模型）、 音效、 固定的內文，衝突的圖形、 相機、 光源、 粒子 fixit 發出器及更多。

### <a name="shapes"></a>圖形

為了方便起見，各種圖案都有簡單 Urho.Shapes 命名空間中的節點。  這些包括方塊、 陳列、 圓錐體、 圓柱和平面。

### <a name="camera-and-viewport"></a>相機與檢視區

就像光線，相機是元件，因此您必須將此元件附加至節點，這是如下所示：

```csharp
var CameraNode = scene.CreateChild ("camera");
camera = CameraNode.CreateComponent<Camera>();
CameraNode.Position = new Vector3 (0, 5, 0);
```

以此方式，您已建立相機、 觀景窗置於 3D 世界下, 一步是告知`Application`，這您想要使用之相機，做法是使用下列程式碼：

```csharp
Renderer.SetViewPort (0, new Viewport (Context, scene, camera, null))
```

而且現在您應該能夠看到您建立的結果。

### <a name="identification-and-scene-hierarchy"></a>識別和場景階層架構

不同於節點，元件不會有名稱;在相同的節點內的元件都只識別其類型和在該節點的元件清單中，在建立順序的索引，比方說，您可以擷取[ `Light` ](https://developer.xamarin.com/api/type/Urho.Light)元件的`lightNode`物件以上所述如下所示：

```csharp
var myLight = lightNode.GetComponent<Light>();
```

您也可以藉由擷取來取得一份所有元件[ `Components` ](https://developer.xamarin.com/api/property/Urho.Node.Components/)屬性會傳回`IList<Component>`，您可以使用。

建立時，節點和元件中取得場景全域整數識別碼。 他們可以使用函式查詢從場景[ `GetNode(uint id)` ](https://developer.xamarin.com/api/member/Urho.Scene.GetNode/p/System.UInt32/)並[ `GetComponent(uint id)` ](https://developer.xamarin.com/api/member/Urho.Scene.GetComponent/p/System.UInt32/)。 這是速度比，例如在名稱為基礎的場景節點查詢遞迴。

沒有內建的實體或遊戲的物件; 概念而是程式設計人員決定節點階層架構中，並在哪些節點可放置任何指令碼的邏輯。 一般而言，3D 世界中的自由移動物件，就會建立做為根節點的子系。 可以建立節點，不論有沒有名稱，使用[ `CreateChild()` ](https://developer.xamarin.com/api/member/Urho.Node.CreateChild/p/System.String/Urho.CreateMode/System.UInt32/)。 不會強制執行的節點名稱的唯一性。

某些階層式的組合時，它會建議 （和事實上有必要，因為元件不會有自己的 3D 轉換） 來建立子節點。

比方說如果字元已保留物件，他手中，物件應該有自己的節點，會變成字元的手骨 (也[ `Node` ](https://developer.xamarin.com/api/type/Urho.Node/))。  例外狀況是物理[ `CollisionShape` ](https://developer.xamarin.com/api/type/Urho.Physics.CollisionShape)，它可以是 offsetted 和旋轉個別與節點相關。

請注意， [ `Scene`](https://developer.xamarin.com/api/type/Urho.Node/)的擁有計算衍生的全局轉換的子節點時，做為最佳化特意不忽略轉換，因此，變更它沒有任何作用和它應該保持原狀 （來源、 沒有旋轉位置未調整。)

[`Scene`](https://developer.xamarin.com/api/type/Urho.Node/) 節點可以是自由重設父代。 相較之下元件一律屬於它們附加至，並不在節點之間移動的節點。 節點和元件提供[ `Remove()` ](https://developer.xamarin.com/api/member/Urho.Node.Remove()/)函式來達成此目的而不需要透過父代。 移除節點後，任何有問題的元件之節點上的作業後呼叫該函式不是安全的。

您也可建立`Node`，不屬於場景。 這非常有用，例如使用相機，因為可能會載入或儲存，場景中移動觀景窗將不會儲存在實際場景，以及，然後都不會終結載入場景時。 不過請注意，建立幾何、 物理或指令碼元件未連結的節點，然後將它移到場景稍後會造成這些元件無法正常運作。

### <a name="scene-updates"></a>場景的更新

已啟用其更新場景上每個主迴圈反覆項目會自動更新 （預設值）。  應用程式的[ `SceneUpdate` ](https://developer.xamarin.com/api/event/Urho.Scene.SceneUpdate/)在其上叫用事件處理常式。

節點和元件可以排除從場景的更新，停用它們，請參閱 < [ `Enabled` ](https://developer.xamarin.com/api/member/Urho.Node.Enabled)。  其行為取決於特定的元件，但例如停用的可繪製的元件也可以看不見，而停用聲音的來源元件靜音。 如果節點已停用，所有元件都會被視為停用，不論它們的啟用/停用狀態。

## <a name="adding-behavior-to-your-components"></a>將行為加入至您的元件

建構您的遊戲的最佳方式是對您自己的元件之封裝的動作項目或項目，您的遊戲。  這使這項功能自封式，從用來顯示它，其行為的資產。

將行為新增至元件的最簡單的方式是使用動作，也就是您可以用來排入佇列，並結合，C# 的非同步程式設計的指示。  這可讓您的元件都非常高的層級的行為，並能更容易了解的事情。

或者，您可以控制確實會發生什麼事您的元件藉由更新您的元件屬性上每個畫面格 （框架為基礎的行為一節所述）。

### <a name="actions"></a>動作

您可以將行為新增至節點非常輕鬆地使用動作。  動作可以改變各種不同的節點屬性和執行它們經過一段時間，或重複使用指定的動畫曲線重試次數。

例如，請考慮在場景的 「 雲端 」 節點，就可以讓它像這樣：

```csharp
await cloud.RunActionsAsync (new FadeOut (duration: 3))
```

動作是不可變的物件，可讓您重複使用的動作，用以執行不同的物件。

一般慣用句，就是建立執行反向作業的動作：

```csharp
var gotoExit = new MoveTo (duration: 3, position: exitLocation);
var return = gotoExit.Reverse ();
```

下列範例會為您淡物件經過一段三秒。  您也可以執行一個動作之後，比方說，您可以先移動雲端，並加以隱藏：

```csharp
await cloud.RunActionsAsync (
    new MoveBy  (duration: 1.5f, position: new Vector3(0, 0, 15),
    new FadeOut (duration: 3));
```

如果您想要在同一時間進行這兩個動作，您可以使用平行的動作，並提供所有動作您想要以平行方式完成：

```csharp
  await cloud.RunActionsAsync (
    new Parallel (
      new MoveBy  (duration: 3, position: new Vector3(0, 0, 15),
      new FadeOut (duration: 3)));
```

在上述範例中雲端將會移動，並在同一時間淡出。

您會發現，這些使用 C# 等候時，可讓您以線性方式思考您想要達成的行為。

### <a name="basic-actions"></a>基本動作

這些是 UrhoSharp 中支援的動作：

* 移動的節點： [ `MoveTo` ](https://developer.xamarin.com/api/type/Urho.Actions.MoveTo)， [ `MoveBy` ](https://developer.xamarin.com/api/type/Urho.Actions.MoveBy)， [ `Place` ](https://developer.xamarin.com/api/type/Urho.Actions.Place)， [ `BezierTo` ](https://developer.xamarin.com/api/type/Urho.Actions.BezierTo)， [ `BezierBy` ](https://developer.xamarin.com/api/type/Urho.Actions.BezierBy), [`JumpTo`](https://developer.xamarin.com/api/type/Urho.Actions.JumpTo), [`JumpBy`](https://developer.xamarin.com/api/type/Urho.Actions.JumpBy)
* 旋轉節點： [ `RotateTo` ](https://developer.xamarin.com/api/type/Urho.Actions.RotateTo)， [`RotateBy`](https://developer.xamarin.com/api/type/Urho.Actions.RotateBy)
* 調整節點： [ `ScaleTo` ](https://developer.xamarin.com/api/type/Urho.Actions.ScaleTo)， [`ScaleBy`](https://developer.xamarin.com/api/type/Urho.Actions.ScaleBy)
* 漸淡節點： [ `FadeIn` ](https://developer.xamarin.com/api/type/Urho.Actions.FadeIn)， [ `FadeTo` ](https://developer.xamarin.com/api/type/Urho.Actions.FadeTo)， [ `FadeOut` ](https://developer.xamarin.com/api/type/Urho.Actions.FadeOut)， [ `Hide` ](https://developer.xamarin.com/api/type/Urho.Actions.Hide)， [`Blink`](https://developer.xamarin.com/api/type/Urho.Actions.Blink)
* 濃淡： [ `TintTo` ](https://developer.xamarin.com/api/type/Urho.Actions.TintTo)， [`TintBy`](https://developer.xamarin.com/api/type/Urho.Actions.TintBy)
* 瞬間： [ `Hide` ](https://developer.xamarin.com/api/type/Urho.Actions.Hide)， [ `Show` ](https://developer.xamarin.com/api/type/Urho.Actions.Show)， [ `Place` ](https://developer.xamarin.com/api/type/Urho.Actions.Place)， [ `RemoveSelf` ](https://developer.xamarin.com/api/type/Urho.Actions.RemoveSelf)， [`ToggleVisibility`](https://developer.xamarin.com/api/type/Urho.Actions.ToggleVisibility)
* 迴圈： [ `Repeat` ](https://developer.xamarin.com/api/type/Urho.Actions.Repeat)， [ `RepeatForever` ](https://developer.xamarin.com/api/type/Urho.Actions.RepeatForever)， [`ReverseTime`](https://developer.xamarin.com/api/type/Urho.Actions.ReverseTime)

其他進階的功能包括的組合[ `Spawn` ](https://developer.xamarin.com/api/type/Urho.Actions.Spawn)並[ `Sequence` ](https://developer.xamarin.com/api/type/Urho.Actions.Sequence)動作。

### <a name="easing---controlling-the-speed-of-your-actions"></a>加/減速-控制動作的速度

加/減速是方法，以引導，動畫會展開，而且它可以讓動畫更愉快的方式。  根據預設您的動作會有線性的行為，例如[ `MoveTo` ](https://developer.xamarin.com/api/type/Urho.Actions.MoveTo)動作會有非常機器人的移動。  您可以將包裝的加/減速動作來變更行為，例如，其中會慢慢開始移動、 加速，而且緩時變結束您的動作 ([`EasyInOut`](https://developer.xamarin.com/api/type/Urho.Actions.EasyInOut))。

您這麼做，例如包裝成加/減速的動作，現有的動作：

```csharp
await cloud.RunActionAsync (
   new EaseInOut (
     new MoveTo (duration: 3, position: new Vector (0,0,15)), rate:1))
```

有許多的加/減速模式下, 圖顯示各種不同加/減速類型和其行為在經過一段時間，控制從開始到完成之物件的值：

![加/減速模式](using-images/easing.png "此圖表顯示不同加/減速類型和其行為就會控制一段時間之物件的值")

### <a name="using-actions-and-async-code"></a>使用動作和非同步程式碼

在您[ `Component` ](https://developer.xamarin.com/api/type/Urho.Component/)子類別中，您應該引進備妥您元件的行為，它的磁碟機功能的非同步方法。
則會叫用這個方法，使用 C#`await`關鍵字，從您的程式，另一個部分是您`Application.Start`方法，或是在您的應用程式中的使用者] 或 [劇本點回應。

例如: 

```csharp
class Robot : Component {
    public bool IsAlive;
    async void Launch ()
    {
        // Dress up our robot
        var cache = Application.ResourceCache;
        var model = node.CreateComponent<StaticModel>();
        model.Model = cache.GetModel ("robot.mdl"));
        model.SetMaterial (cache.GetMaterial ("robot.xml"));
        Node.SetScale (1);

        // Bring the robot into our scene
        await Node.RunActionsAsync(
            new MoveBy(duration: 0.6f, position: new Vector3(0, -2, 0)));
        // Make him move around to avoid the user fire
        MoveRandomly(minX: 1, maxX: 2, minY: -3, maxY: 3, duration: 1.5f);
        // And simultaneously have him shoot at the user
        StartShooting();
    }

    protected async void MoveRandomly (float minX, float maxX,
                                       float minY, float maxY,
                       float duration)
    {
        while (IsAlive){
            var moveAction = new MoveBy(duration,
                new Vector3(RandomHelper.NextRandom(minX, maxX),
                            RandomHelper.NextRandom(minY, maxY), 0));
            await Node.RunActionsAsync(moveAction, moveAction.Reverse());
        }
    }
    protected async void StartShooting()
    {
        while (IsAlive && Node.Components.Count > 0){
            foreach (var weapon in Node.Components.OfType<Weapon>()){
                await weapon.FireAsync(false);
                if (!IsAlive)
                    return;
            }
            await Node.RunActionsAsync(new DelayTime(0.1f));
        }
    }
}
```

在 `Launch`上述三個動作方法會啟動： 機器人進入場景，此動作會經過一段 0.6 秒變更節點的位置。  由於這是非同步選項時，這會同時為下一個指令，也就是呼叫到`MoveRandomly`。  這個方法會改變以平行方式到隨機位置機器人的位置。  這之後，即可執行兩個複合的動作，移動到新的位置，並返回到原始位置並重複此步驟，只要機器人保持運作。  若要讓事情更加有趣，機器人會保留著眼於同時。  疑難排解，才會啟動每個 0.1 秒。

### <a name="frame-based-behavior-programming"></a>畫面格為基礎的行為程式設計

如果您想要控制您的元件，請依框架為基礎，而不是使用動作的行為，您會執行可覆寫[ `OnUpdate` ](https://developer.xamarin.com/api/member/Urho.Component.OnUpdate)方法您[ `Component` ](https://developer.xamarin.com/api/type/Urho.Component)子類別。  這個方法會一次叫用每個畫面，而且 ReceiveSceneUpdates 屬性為 true 時，才會叫用。

下圖顯示如何建立`Rotator`元件，然後連接到節點，這會導致要旋轉的節點：

```csharp
class Rotator : Component {
    public Rotator()
    {
        ReceiveSceneUpdates = true;
    }
    public Vector3 RotationSpeed { get; set; }
    protected override void OnUpdate(float timeStep)
    {
        Node.Rotate(new Quaternion(
            RotationSpeed.X * timeStep,
            RotationSpeed.Y * timeStep,
            RotationSpeed.Z * timeStep),
            TransformSpace.Local);
    }
}
```

而這是如何附加至節點的 此元件：

```csharp
Node boxNode = new Node();
var rotator = new Rotator() { RotationSpeed = rotationSpeed };
boxNode.AddComponent (rotator);
```

### <a name="combining-styles"></a>結合樣式

您可以使用 async/動作基礎模型進行程式設計的行為，也就是適合用來引發不理樣式程式設計中，大部分，但您可以也微調您的元件也在每個框架上執行一些更新程式碼的行為。

例如，在 SamplyGame 示範此介面用於`Enemy`類別會將編碼的基本行為會使用動作，但它也可確保，元件會指向使用者所設定的節點的方向`Node.LookAt`:

```csharp
    protected override void OnUpdate(SceneUpdateEventArgs args)
    {
        Node.LookAt(
            new Vector3(0, -3, 0),
            new Vector3(0, 1, -1),
            TransformSpace.World);
        base.OnUpdate(args);
    }
```

## <a name="loading-and-saving-scenes"></a>載入和儲存場景

可載入和儲存在 XML 格式; 場景列出函式[ `LoadXml` ](https://developer.xamarin.com/api/member/Urho.Scene.LoadXml)並[ `SaveXML()` ](https://developer.xamarin.com/api/member/Urho.Scene.SaveXml)。 載入場景時，會先移除所有現有內容 （子節點和元件）。 節點和標記的暫存區元件`Temporary`將不會儲存屬性。 序列化程式處理所有的內建元件和屬性，但它不是聰明，足以處理自訂屬性以及定義您的元件子類別中的欄位。 不過它會為此提供兩種虛擬方法：

* [`OnSerialize`](https://developer.xamarin.com/api/member/Urho.Component.OnSerialize) 您可以註冊您自訂狀態序列化

* [`OnDeserialized`](https://developer.xamarin.com/api/member/Urho.Component.OnDeserialize) 您可以在哪裡取得您儲存的自訂狀態。

一般而言，自訂元件會如下所示：

```csharp
class MyComponent : Component {
    // Constructor needed for deserialization
    public MyComponent(IntPtr handle) : base(handle) { }
    public MyComponent() { }
    // user defined properties (managed state):
    public Quaternion MyRotation { get; set; }
    public string MyName { get; set; }

    public override void OnSerialize(IComponentSerializer ser)
    {
        // register our properties with their names as keys using nameof()
        ser.Serialize(nameof(MyRotation), MyRotation);
        ser.Serialize(nameof(MyName), MyName);
    }

    public override void OnDeserialize(IComponentDeserializer des)
    {
        MyRotation = des.Deserialize<Quaternion>(nameof(MyRotation));
        MyName = des.Deserialize<string>(nameof(MyName));
    }
    // called when the component is attached to some node
    public override void OnAttachedToNode()
    {
        var node = this.Node;
    }
}
```

### <a name="object-prefabs"></a>物件 Prefabs

只要載入或儲存整個場景不具備足夠的彈性，遊戲需要將以動態方式建立新的物件。 相反地，建立複雜的物件和程式碼中設定其屬性也會單調乏味。 基於這個理由，它也可儲存它的子節點、 元件和屬性會包含一個場景節點。 這些方便日後載入做為群組。  這類已儲存的物件通常稱為 prefab。 執行這項作業的方法有三種：

- 藉由呼叫的程式碼中[ `Node.SaveXml` ](https://developer.xamarin.com/api/member/Urho.Node.SaveXml)節點上
- 在編輯器中，選取 [階層] 視窗中的節點，然後選擇 [儲存節點] 從 [檔案] 功能表。
- 使用中的 「 節點 」 命令`AssetImporter`，這會將儲存場景節點階層架構和任何模型中包含輸入資產 （例如。 Collada 檔案）

若要具現化已儲存的節點到場景呼叫[ `InstantiateXml()` ](https://developer.xamarin.com/api/member/Urho.Scene.InstantiateXml)。 節點將會建立做為子系的場景，但可以自由重設父代之後。 必須指定位置和放置節點的循環。 下列程式碼示範如何具現化 prefab`Ninja.xm`加入所需的位置和旋轉場景：

```csharp
var prefabPath = Path.Combine (FileSystem.ProgramDir,"Data/Objects/Ninja.xml");
using (var file = new File(Context, prefabPath, FileMode.Read))
{
    scene.InstantiateXml(file, desiredPos, desiredRotation,
        CreateMode.Replicated);
}
```

## <a name="events"></a>事件

UrhoObjects 引發的事件數目，這些產生的各種類別當成 C# 事件。  除了 C#-基底的事件模型，您也可使用`SubscribeToXXX`方法，可讓您訂閱，而且將保留的訂用帳戶權杖，您稍後可以使用來取消訂閱。  差別在於，前者可讓訂閱的許多呼叫端，而第二個只允許一個，但可提供好看的 lambda 樣式方式，來使用，並且還，允許的訂用帳戶可輕鬆移除。  它們是互斥的。

當您訂閱事件時，您必須提供採用適當的事件引數的引數的方法。

比方說，這是您如何訂閱滑鼠按鈕按下事件：

```csharp
public void override Start ()
{
    UI.MouseButtonDown += HandleMouseButtonDown;
}

void HandleMouseButtonDown(MouseButtonDownEventArgs args)
{
    Console.WriteLine ("button pressed");
}
```

使用 lambda 樣式：

```csharp
public void override Start ()
{
    UI.MouseButtonDown += args => {
        Console.WriteLine ("button pressed");
    };
}
```

有時候您會想要停止接收通知的事件，在這些情況下，儲存傳回的值，呼叫`SubscribeTo`方法，並叫用它的取消訂閱方法：

```csharp
Subscription mouseSub;

public void override Start ()
{
    mouseSub = UI.SubscribeToMouseButtonDown (args => {
    Console.WriteLine ("button pressed");
      mouseSub.Unsubscribe ();
    };
}
```

收到的事件處理常式的參數是專屬於每個事件，其中包含事件裝載的強型別的事件引數類別。

## <a name="responding-to-user-input"></a>回應使用者輸入

您可以訂閱各種事件，例如按鍵輸入下訂閱事件，並回應所傳遞的輸入：

```csharp
Start ()
{
    UI.KeyDown += HandleKeyDown;
}

void HandleKeyDown (KeyDownEventArgs arg)
{
     switch (arg.Key){
     case Key.Esc: Engine.Exit (); return;
}
```

但在許多情況下，您想要檢查索引鍵的目前狀態，正在更新，並據以更新您的程式碼時您場景更新處理常式。  例如，下列可用來更新根據鍵盤輸入的觀景窗位置：

```csharp
protected override void OnUpdate(float timeStep)
{
    Input input = Input;
    // Movement speed as world units per second
    const float moveSpeed = 4.0f;
    // Read WASD keys and move the camera scene node to the
    // corresponding direction if they are pressed
    if (input.GetKeyDown(Key.W))
        CameraNode.Translate(Vector3.UnitY * moveSpeed * timeStep, TransformSpace.Local);
    if (input.GetKeyDown(Key.S))
        CameraNode.Translate(new Vector3(0.0f, -1.0f, 0.0f) * moveSpeed * timeStep, TransformSpace.Local);
    if (input.GetKeyDown(Key.A))
        CameraNode.Translate(new Vector3(-1.0f, 0.0f, 0.0f) * moveSpeed * timeStep, TransformSpace.Local);
    if (input.GetKeyDown(Key.D))
        CameraNode.Translate(Vector3.UnitX * moveSpeed * timeStep, TransformSpace.Local);
}
```

## <a name="resources-assets"></a>資源 （資產）

資源包括 UrhoSharp 中大部分會從大量的儲存體載入期間初始化或執行階段的項目：

- [`Animation`](https://developer.xamarin.com/api/type/Urho.Animation/) -用於骨架的動畫
- [`Image`](https://developer.xamarin.com/api/type/Urho.Resources.Image) -代表儲存在各種不同的圖形格式的影像
- [`Model`](https://developer.xamarin.com/api/type/Urho.Model/) -3D 模型
- [`Material`](https://developer.xamarin.com/api/type/Urho.Material) -用來呈現模型的資料。
- [`ParticleEffect`](https://developer.xamarin.com/api/type/Urho.ParticleEffect)- [描述](http://urho3d.github.io/documentation/1.4/_particles.html)如何粒子 fixit 發出器的運作方式，請參閱 「[粒子](#particles)」 下方。
- [`Shader`](https://developer.xamarin.com/api/type/Urho.Shader) -自訂著色器
- [`Sound`](https://developer.xamarin.com/api/type/Urho.Audio.Sound) -音效播放，請參閱 「[音效](#sound)」 下方。
- [`Technique`](https://developer.xamarin.com/api/type/Urho.Technique/) -material 轉譯技術
- [`Texture2D`](https://developer.xamarin.com/api/type/Urho.Urho2D.Texture2D/) -2D 材質
- [`Texture3D`](https://developer.xamarin.com/api/type/Urho.Texture3D/) -3D 材質
- [`TextureCube`](https://developer.xamarin.com/api/type/Urho.TextureCube/) -立方體紋理
- `XmlFile`

管理和載入[ `ResourceCache` ](https://developer.xamarin.com/api/type/Urho.Resources.ResourceCache/)子系統 (當作[ `Application.ResourceCache` ](https://developer.xamarin.com/api/property/Urho.Application.ResourceCache/))。

資源本身被識別其檔案路徑，相對於已註冊的資源目錄或封裝檔案。 根據預設，引擎會註冊資源目錄`Data`並`CoreData`，或封裝`Data.pak`和`CoreData.pak`如果有的話。

如果載入資源失敗，就會記錄錯誤，並會傳回 null 參考。

下列範例會示範一般資源快取擷取資源的方式。  在此情況下，UI 項目紋理，這會使用`ResourceCache`屬性從`Application`類別。

```csharp
healthBar.SetTexture(ResourceCache.GetTexture2D("Textures/HealthBarBorder.png"));
```

資源也是以手動方式建立和儲存資源的快取，如同它們一直從磁碟載入。

記憶體預算可以設定每個資源類型： 如果資源耗用更多的記憶體超過允許的值，最舊的資源將會移除從快取，如果不在使用了。 根據預設記憶體預算會設定為無限制。

### <a name="bringing-3d-models-and-images"></a>將 3D 模型和映像

使用現有的檔案格式可能的話，並定義自訂檔案格式，例如模型絕對必要時才嘗試 Urho3D (*.mdl) 和動畫 (*.ani)。 這些類型的資產，如 Urho 提供轉換器- [AssetImporter](http://urho3d.github.io/documentation/1.4/_tools.html)這可能會耗用許多熱門的 3D 格式，例如 fbx、 dae、 3ds，和 obj 等等。

另外還有好用的增益集 Blender [ https://github.com/reattiva/Urho3D-Blender ](https://github.com/reattiva/Urho3D-Blender) ，可以匯出適合 Urho3D 格式中的 「 blender 」 資產。

### <a name="background-loading-of-resources"></a>資源的背景載入

一般來說，當要求的資源使用其中一種`ResourceCache`的`Get`方法，它們會立即在主執行緒，這可能需要幾毫秒，讓所需的步驟中載入 （從磁碟載入檔案、 剖析資料，如有必要上, 傳到 GPU)，因此可能會導致畫面播放速率會卸除。

如果您知道您需要哪些資源事先，您可以提出要求時要載入在背景執行緒中呼叫`BackgroundLoadResource()`。 您可以使用訂閱資源背景載入事件`SubscribeToResourceBackgroundLoaded`方法。 它會告訴載入實際上是否成功或失敗。 視資源，只載入程序的一部分可能會移至背景執行緒，例如分頁裝訂的 GPU 上傳步驟一律必須在主執行緒中發生。 請注意，如果您呼叫其中一個載入方法，與排入佇列的背景載入資源的資源，主執行緒將會延後其載入之前完成。

非同步載入功能的場景`LoadAsync()`和`LoadAsyncXML()`第一次之前繼續場景內容載入的資源有背景負載的選項。 它也可用來僅載入資源，而不需要修改場景，請指定`LoadMode.ResourcesOnly`。 這可讓準備快速具現化的場景或物件 prefab 檔案。

最後花費時間上限 （以毫秒為單位） 每個畫面上完成載入的資源可以透過設定來設定的背景`FinishBackgroundResourcesMs`屬性上的`ResourceCache`。

<a name="sound"/>

## <a name="sound"></a>音效

聲音是遊戲，很重要的一部分，並 UrhoSharp framework 提供一種遊戲中播放音效。  您藉由附加播放音效 [`SoundSource`](https://developer.xamarin.com/api/type/Urho.Audio.SoundSource/)
元件[ `Node` ](https://developer.xamarin.com/api/type/Urho.Node) ，然後播放命名的檔案，從您的資源。

這是做法：

```csharp
var explosionNode = Scene.CreateChild();
var sound = explosionNode.CreateComponent<SoundSource>();
soundSource.Play(Application.ResourceCache.GetSound("Sounds/ding.wav"));
soundSource.Gain = 0.5f;
soundSource.AutoRemove = true;
```

<a name="particles"/>

## <a name="particles"></a>物件

物件會提供簡單的方式，您的應用程式中加入一些簡單又實惠的效果。  您可以使用 PEX 格式儲存的物件之類的工具[ http://onebyonedesign.com/flash/particleeditor/ ](http://onebyonedesign.com/flash/particleeditor/)。

物件是可以新增到節點的元件。  您必須呼叫該節點的`CreateComponent<ParticleEmitter2D>`方法來建立物件，並設定 Effect 屬性為 2D 效果，以設定該物件會從資源快取載入。

比方說，您可以在您的元件，以顯示一些到達時，會轉譯為遽增的物件上叫用這個方法：

```csharp
public async void Explode (Component target)
{
    // show a small explosion when the missile reaches an aircraft.
    var cache = Application.ResourceCache;
    var explosionNode = Scene.CreateChild();
    explosionNode.Position = target.Node.WorldPosition;
    explosionNode.SetScale(1f);
    var particle = explosionNode.CreateComponent<ParticleEmitter2D>();
    particle.Effect = cache.GetParticleEffect2D("explosion.pex");
    var scaleAction = new ScaleTo(0.5f, 0f);
    await explosionNode.RunActionsAsync(
        scaleAction, new DelayTime(0.5f));
    explosionNode.Remove();
}
```

上述程式碼會建立附加至目前元件的擴張節點，這個遽增節點內方法，我們可以建立 2D 物件 fixit 發出器，並設定 Effect 屬性加以設定。  我們會執行兩個動作，會調整為較小，節點的其中一個，讓它以該大小維持 0.5 秒的另一個。  然後，我們要移除的擴張，也會從螢幕移除粒子效果。

使用球面紋理時，上述的物件會轉譯如下：

![物件，使用圓球材質](using-images/image-1.png "上述物件時使用球面紋理會轉譯如下")

而這是如果您使用的區塊式紋理的呈現：

![使用方塊材質粒子](using-images/image-2.png "這就是如果使用區塊式紋理的呈現")

## <a name="multithreading-support"></a>多執行緒支援

UrhoSharp 是單一執行緒程式庫。  這表示您不應該嘗試叫用中的方法 UrhoSharp 從背景執行緒，或您可能會損毀的應用程式狀態，並可能會損毀您的應用程式。

如果您想要在背景中執行一些程式碼，然後更新 Urho 元件上的主要 UI，您可以使用 [`Application.InvokeOnMain(Action)`](https://developer.xamarin.com/api/member/Urho.Application.InvokeOnMain)
方法。  此外，您可以的使用 C# await 和.NET 工作 Api，以確保在適當的執行緒上已執行的程式碼。

## <a name="urhoeditor"></a>UrhoEditor

您可以下載您的平台從 Urho 編輯器[Urho 網站](http://urho3d.github.io/)，請前往 下載項目，並挑選最新版本。

## <a name="copyrights"></a>著作權

這份文件會包含從 Xamarin Inc.的原始內容，但廣泛繪製 Urho3D 專案的開放原始碼文件，並包含 Cocos2D 專案的螢幕擷取畫面。
