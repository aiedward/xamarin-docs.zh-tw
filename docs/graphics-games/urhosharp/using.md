---
title: 使用 UrhoSharp
description: UrhoSharp 引擎的概觀
ms.prod: xamarin
ms.assetid: D9BEAD83-1D9E-41C3-AD4B-3D87E13674A0
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.openlocfilehash: cdb32c0fe9aa1a267bda5768b9026667723d694c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="using-urhosharp"></a>使用 UrhoSharp

_UrhoSharp 引擎的概觀_

撰寫第一個遊戲之前，您想要取得請基本： 如何設定場景、 如何載入的資源 （這包含您的作品） 和如何建立簡單的互動，您的遊戲。

<a name="scenenodescomponentsandcameras"/>

# <a name="scenes-nodes-components-and-cameras"></a>場景、 節點、 元件和攝影機

場景模型可以稱為以元件為基礎的場景圖形。 場景節點階層的場景，從根節點，這也代表整個場景所組成。 每個[ `Node` ](https://developer.xamarin.com/api/type/Urho.Node/)有 3D 轉換 （位置、 旋轉和小數位數）、 名稱、 識別碼、 加上任意數目的元件。  元件將節點，進行新增的視覺表示法 ([`StaticModel`](https://developer.xamarin.com/api/type/Urho.StaticModel))，它們可發出聲音 ([`SoundSource`](https://developer.xamarin.com/api/type/Urho.Audio.SoundSource))，它們可以提供衝突界限，依此類推。

您可以建立場景和使用的安裝程式節點[Urho 編輯器](#UrhoEditor)，或您可以從 C# 程式碼執行作業。  本文件中我們將探討設定項目，使用程式碼，如這些範例說明以取得項目才會出現在螢幕上的必要項目

除了設定場景，您需要設定[ `Camera` ](https://developer.xamarin.com/api/type/Urho.Camera/)，這是決定向使用者取得顯示的內容。

## <a name="setting-up-your-scene"></a>設定場景

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

## <a name="components"></a>元件

呈現 3D 物件、 音效播放、 物理和指令碼編寫的邏輯更新會啟用所有至各節點建立不同的元件，藉由呼叫[ `CreateComponent<T>()` ](https://developer.xamarin.com/api/member/Urho.Node.CreateComponent%3CT%3E/p/Urho.CreateMode/System.UInt32/)。  例如，安裝您的節點和淺色元件如下：

```csharp
// Create a directional light to the world so that we can see something. The
// light scene node's orientation controls the light direction; we will use
// the SetDirection() function which calculates the orientation from a forward
// direction vector.
// The light will use default settings (white light, no shadows)
var lightNode = scene.CreateChild("DirectionalLight");
lightNode.SetDirection (new Vector3(0.6f, -1.0f, 0.8f));
```

我們已經建立具有名稱的節點上方"`DirectionalLight`」 和設定，但是沒有其他的方向。  現在，我們可以將上述節點變成 light 發出的節點，藉由附加[ `Light` ](https://developer.xamarin.com/api/type/Urho.Light/)元件，與`CreateComponent`:

```csharp
var light = lightNode.CreateComponent<Light>();
```

建立元件`Scene`本身有特殊的角色： 實作場景全功能。 它們應該先建立其他元件，並包括下列各項：

* [`Octree`](https://developer.xamarin.com/api/type/Urho.Octree/)： 會實作空間的資料分割，並加速可見性的查詢。 沒有這個 3D 物件無法轉譯。
* [`PhysicsWorld`](https://developer.xamarin.com/api/type/Urho.Physics.PhysicsWorld/)： 實作物理模擬。 例如物理元件[ `RigidBody` ](https://developer.xamarin.com/api/type/Urho.Physics.RigidBody/)或[ `CollisionShape` ](https://developer.xamarin.com/api/type/Urho.Physics.CollisionShape/)不正常未執行此動作。
* [`DebugRenderer`](https://developer.xamarin.com/api/type/Urho.DebugRenderer/)： 實作偵錯的幾何轉換。

等一般元件[ `Light` ](https://developer.xamarin.com/api/type/Urho.Light)， [ `Camera` ](https://developer.xamarin.com/api/type/Urho.Camera)或[ `StaticModel` ](https://developer.xamarin.com/api/type/Urho.StaticModel)不應直接建立[ `Scene` ](https://developer.xamarin.com/api/type/Urho.Scene)，但而成子節點。

程式庫隨附各種不同的元件，您可以附加至您的節點，使其更生動： 使用者可看見的項目 （模型）、 音效、 固定的內文、 衝突圖形、 數位相機、 光線的來源、 物件發出及執行更多。

## <a name="shapes"></a>圖形

為了方便起見，各種圖形是 Urho.Shapes 命名空間中的簡單節點可用。  這些包括方塊、 陳列、 錐形、 圓柱及平面。

## <a name="camera-and-viewport"></a>相機與檢視區

就像光線，數位相機是元件，因此您必須將附加至節點的元件，這是以這種方式：

```csharp
var CameraNode = scene.CreateChild ("camera");
camera = CameraNode.CreateComponent<Camera>();
CameraNode.Position = new Vector3 (0, 5, 0);
```

與這個項目，您已建立相機、 相機置於 3D 世界下, 一個步驟是通知`Application`，這您想要使用的相機，做法是使用下列程式碼：

```csharp
Renderer.SetViewPort (0, new Viewport (Context, scene, camera, null))
```

現在您應該可以看到您所建立的結果。

## <a name="identification-and-scene-hierarchy"></a>識別與場景的階層

不同於節點，元件不會有名稱。只能由其類型，以及在節點的元件清單，會自動填入的建立順序的索引識別相同節點的內部元件，例如，您可以擷取[ `Light` ](https://developer.xamarin.com/api/type/Urho.Light)元件超出`lightNode`物件上方以這種方式：

```csharp
var myLight = lightNode.GetComponent<Light>();
```

您也可以取得一份所有元件，藉由擷取[ `Components` ](https://developer.xamarin.com/api/property/Urho.Node.Components/)屬性會傳回`IList<Component>`，您可以使用。

建立時，節點和元件中取得場景全域整數識別碼。 他們可以使用函數來查詢從場景[ `GetNode(uint id)` ](https://developer.xamarin.com/api/member/Urho.Scene.GetNode/p/System.UInt32/)和[ `GetComponent(uint id)` ](https://developer.xamarin.com/api/member/Urho.Scene.GetComponent/p/System.UInt32/)。 這是比例如進行遞迴名稱為基礎的場景節點查詢快得多。

沒有內建的實體或遊戲的物件; 概念而是程式設計人員決定節點階層，並將任何指令碼編寫的邏輯哪一個節點中。 一般而言，建立自由移動 3D 世界中的物件做為子系的根節點。 可以建立節點，不論有無名稱使用[ `CreateChild()` ](https://developer.xamarin.com/api/member/Urho.Node.CreateChild/p/System.String/Urho.CreateMode/System.UInt32/)。 不強制執行的節點名稱的唯一性。

某些階層式的組合時，它會建議 （和確實有必要，因為元件不會有自己的 3D 轉換） 來建立子節點。

例如字元他手中保有物件，如果物件應該有其本身就字元的手骨父代的節點 (也[ `Node` ](https://developer.xamarin.com/api/type/Urho.Node/))。  例外狀況是物理[ `CollisionShape` ](https://developer.xamarin.com/api/type/Urho.Physics.CollisionShape)，它可以是 offsetted 和旋轉個別與節點相關。

請注意， [ `Scene`](https://developer.xamarin.com/api/type/Urho.Node/)的擁有計算衍生的世界轉換的子節點時，最佳化故意忽略轉換，因此變更它無效且應該保持原狀 （沒有旋轉的來源位置沒有縮放比例。)

[`Scene`](https://developer.xamarin.com/api/type/Urho.Node/) 節點可以是自由重設父代。 相較之下元件永遠屬於到它們附加至，並不在節點之間移動的節點。 節點及其元件提供[ `Remove()` ](https://developer.xamarin.com/api/member/Urho.Node.Remove()/)函式來完成這項作業而不需透過父代。 一旦移除節點時，節點或有問題的元件上的任何作業後呼叫該函式不是安全的。

它也可建立`Node`，不屬於場景。 這非常有用，例如使用相機移動中的場景，可能會載入或儲存，因為然後相機將不會儲存實際的場景，以及將不會終結場景載入時。 不過請注意，建立幾何、 物理或指令碼元件未附加的節點，並再將它移到場景稍後將會導致這些元件，才能正常運作。

## <a name="scene-updates"></a>場景更新

場景，其更新為啟用 （預設值） 會在每個主迴圈反覆項目自動更新。  應用程式的[ `SceneUpdate` ](https://developer.xamarin.com/api/event/Urho.Scene.SceneUpdate/)在其上叫用事件處理常式。

節點和元件，即可排除來自窗定位在場景更新停用它們，請參閱[ `Enabled` ](https://developer.xamarin.com/api/member/Urho.Node.Enabled)。  行為取決於特定的元件，但例如停用 drawable 元件也可以看不見，而停用聲音來源元件靜音。 如果節點已停用，所有元件會視為為停用，不論他們自己的啟用/停用狀態。

# <a name="adding-behavior-to-your-components"></a>將行為加入至您的元件

建構您的遊戲的最佳方式是讓自己封裝動作項目或項目上遊戲的元件。  這可讓功能自封，從用來顯示它，其行為的資產。

將行為加入至元件的最簡單的方式是使用動作，您可以用來排入佇列，並與 C# 非同步程式設計結合的指示。  這可讓您的元件都非常高的層級的行為，並使其更容易了解發生什麼事。

或者，您可以控制完全情形，您的元件藉由更新 （在以框架為基礎的行為 > 一節中討論） 的每個框架上您元件的屬性。

## <a name="actions"></a>動作

您可以將行為加入節點最輕鬆地使用動作。  動作可以改變各種節點的屬性並執行它們經過一段時間，或重複數次，以指定的動畫曲線。

例如，請考慮您場景的 「 雲端 」 節點，就可以讓它像這樣：

```csharp
await cloud.RunActionsAsync (new FadeOut (duration: 3))
```

動作是不可變的物件，可讓您重複使用的動作來推動不同的物件。

常見慣用語是建立執行反向作業的動作：

```csharp
var gotoExit = new MoveTo (duration: 3, position: exitLocation);
var return = gotoExit.Reverse ();
```

下列範例會為您淡物件在一段三秒。  您也可以執行一個動作之後，比方說，您無法先移動雲端，然後將其隱藏：

```csharp
await cloud.RunActionsAsync (
    new MoveBy  (duration: 1.5f, position: new Vector3(0, 0, 15),
    new FadeOut (duration: 3));
```

如果您想要在同一時間發生的這兩個動作，您可以使用平行的動作，並提供所有動作您想要以平行方式完成：

```csharp
  await cloud.RunActionsAsync (
    new Parallel (
      new MoveBy  (duration: 3, position: new Vector3(0, 0, 15),
      new FadeOut (duration: 3)));
```

在上述範例中的雲端將會移動，並同時淡出。

您會注意到，這些使用 C# 等候時，可讓您以線性方式考慮您想要達到的行為。

## <a name="basic-actions"></a>基本的動作

這些是 UrhoSharp 中支援的動作：

* 移動節點： [ `MoveTo` ](https://developer.xamarin.com/api/type/Urho.Actions.MoveTo)， [ `MoveBy` ](https://developer.xamarin.com/api/type/Urho.Actions.MoveBy)， [ `Place` ](https://developer.xamarin.com/api/type/Urho.Actions.Place)， [ `BezierTo` ](https://developer.xamarin.com/api/type/Urho.Actions.BezierTo)， [ `BezierBy` ](https://developer.xamarin.com/api/type/Urho.Actions.BezierBy), [`JumpTo`](https://developer.xamarin.com/api/type/Urho.Actions.JumpTo), [`JumpBy`](https://developer.xamarin.com/api/type/Urho.Actions.JumpBy)
* 旋轉節點： [ `RotateTo` ](https://developer.xamarin.com/api/type/Urho.Actions.RotateTo)， [`RotateBy`](https://developer.xamarin.com/api/type/Urho.Actions.RotateBy)
* 調整節點： [ `ScaleTo` ](https://developer.xamarin.com/api/type/Urho.Actions.ScaleTo)， [`ScaleBy`](https://developer.xamarin.com/api/type/Urho.Actions.ScaleBy)
* 節點的漸淡： [ `FadeIn` ](https://developer.xamarin.com/api/type/Urho.Actions.FadeIn)， [ `FadeTo` ](https://developer.xamarin.com/api/type/Urho.Actions.FadeTo)， [ `FadeOut` ](https://developer.xamarin.com/api/type/Urho.Actions.FadeOut)， [ `Hide` ](https://developer.xamarin.com/api/type/Urho.Actions.Hide)， [`Blink`](https://developer.xamarin.com/api/type/Urho.Actions.Blink)
* 濃淡： [ `TintTo` ](https://developer.xamarin.com/api/type/Urho.Actions.TintTo)， [`TintBy`](https://developer.xamarin.com/api/type/Urho.Actions.TintBy)
* 時瞬間： [ `Hide` ](https://developer.xamarin.com/api/type/Urho.Actions.Hide)， [ `Show` ](https://developer.xamarin.com/api/type/Urho.Actions.Show)， [ `Place` ](https://developer.xamarin.com/api/type/Urho.Actions.Place)， [ `RemoveSelf` ](https://developer.xamarin.com/api/type/Urho.Actions.RemoveSelf)， [`ToggleVisibility`](https://developer.xamarin.com/api/type/Urho.Actions.ToggleVisibility)
* 迴圈： [ `Repeat` ](https://developer.xamarin.com/api/type/Urho.Actions.Repeat)， [ `RepeatForever` ](https://developer.xamarin.com/api/type/Urho.Actions.RepeatForever)， [`ReverseTime`](https://developer.xamarin.com/api/type/Urho.Actions.ReverseTime)

其他進階的功能包括之組合的[ `Spawn` ](https://developer.xamarin.com/api/type/Urho.Actions.Spawn)和[ `Sequence` ](https://developer.xamarin.com/api/type/Urho.Actions.Sequence)動作。

## <a name="easing---controlling-the-speed-of-your-actions"></a>Easing-控制動作的速度

Easing 是方法，以指示，動畫會展開，而且它可以讓動畫更加愉快的方式。  預設您的動作將會有線性的行為，例如[ `MoveTo` ](https://developer.xamarin.com/api/type/Urho.Actions.MoveTo)動作會有非常機械移動。  您可以包裝您的動作加/減速動作來變更行為，例如，另一個會緩時變開始移動，以加速及緩時變到達的結尾 ([`EasyInOut`](https://developer.xamarin.com/api/type/Urho.Actions.EasyInOut))。

您可以包裝現有的動作為加/減速的動作，例如：

```csharp
await cloud.RunActionAsync (
   new EaseInOut (
     new MoveTo (duration: 3, position: new Vector (0,0,15)), rate:1))
```

有許多加/減速模式下, 圖顯示經過一段時間，控制從開始到完成之物件的值上的不同加/減速類型且其行為：

![Easing 模式](using-images/easing.png "此圖表顯示不同加/減速類型且其行為就會控制在一段時間之物件的值")

## <a name="using-actions-and-async-code"></a>使用動作和非同步程式碼

在您[ `Component` ](https://developer.xamarin.com/api/type/Urho.Component/)子類別，您應該引進備妥您元件的行為，其磁碟機功能的非同步方法。
然後您會叫用此方法使用 C#`await`關鍵字的程式，另一個組件可能是您`Application.Start`方法或為了回應應用程式中的使用者或本文點。

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

在`Launch`上面三個動作方法會啟動： 機器人進入場景，此動作會在一段會剩餘 0.6 秒改變節點的位置。  由於這是非同步選項時，會發生這種同時做為下一個指令，也就是呼叫要`MoveRandomly`。  這個方法會改變機器人平行到隨機位置中的位置。  這藉由執行兩個複合的動作，移動到新位置，並回到原始位置，並重複此步驟，只要機器人保持運作。  而且，若要讓概念更有趣，機器人會保留疑難排解同時。  疑難排解，才會開始每隔 0.1 秒。

## <a name="frame-based-behavior-programming"></a>以框架為基礎的行為程式設計

如果您想要控制您的元件，請依框架為基礎而不是使用動作的行為，就需要執行的動作是覆寫[ `OnUpdate` ](https://developer.xamarin.com/api/member/Urho.Component.OnUpdate)方法您[ `Component` ](https://developer.xamarin.com/api/type/Urho.Component)子類別。  此方法會一次叫用每個畫面格，而且 ReceiveSceneUpdates 屬性設為 true 時，才會叫用。

下列範例示範如何建立`Rotator`元件，然後連接至節點，這會導致要旋轉的節點：

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

這是您會將此元件附加到節點的方式：

```csharp
Node boxNode = new Node();
var rotator = new Rotator() { RotationSpeed = rotationSpeed };
boxNode.AddComponent (rotator);
```

## <a name="combining-styles"></a>結合樣式

您可以使用基礎的非同步/動作模型程式設計大部分的行為，幫助您和不理樣式的程式設計，但您可以也微調您的元件也在每個框架上執行某些更新程式碼的行為。

例如，在 SamplyGame 示範這用於`Enemy`類別將編碼的基本行為會使用動作，但它也可確保，元件會指向使用者所設定的節點方向`Node.LookAt`:

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

# <a name="loading-and-saving-scenes"></a>載入及儲存場景

場景可以載入並儲存成 XML 格式。請參閱函數[ `LoadXml` ](https://developer.xamarin.com/api/member/Urho.Scene.LoadXml)和[ `SaveXML()` ](https://developer.xamarin.com/api/member/Urho.Scene.SaveXml)。 場景載入時，會先移除所有現有內容 （子節點和元件）。 節點與元件的標記與暫存`Temporary`將不會儲存屬性。 序列化程式會處理所有的內建的元件和屬性，但不是聰明，可以處理自訂屬性以及在您的元件子類別中定義的欄位。 但是它提供兩種虛擬方法，這個：

* [`OnSerialize`](https://developer.xamarin.com/api/member/Urho.Component.OnSerialize) 您可以註冊您自訂序列化的狀態

* [`OnDeserialized`](https://developer.xamarin.com/api/member/Urho.Component.OnDeserialize) 您可以在哪裡取得儲存的自訂狀態。

一般而言，自訂元件，將會如下所示：

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

## <a name="object-prefabs"></a>物件 Prefabs

剛載入或儲存整個場景不是有足夠的彈性，遊戲需要以動態方式建立新物件的位置。 相反地，建立複雜的物件和程式碼中設定其屬性也會是冗長。 基於這個理由，所以也可以儲存場景節點，其中包含它的子節點、 元件和屬性。 這些屬性稍後方便地以群組載入。  這類儲存的物件通常稱為 prefab。 執行這項作業的方法有三種：

- 藉由呼叫的程式碼中[ `Node.SaveXml` ](https://developer.xamarin.com/api/member/Urho.Node.SaveXml)節點上
- 在編輯器中，階層視窗中選取節點，然後選擇 [儲存的節點] 從 [檔案] 功能表。
- 使用中的 「 節點 」 命令`AssetImporter`、 其中會儲存場景節點階層，以及任何模型中包含輸入資產 （例如 Collada 檔案）

若要產生的儲存的節點至場景，呼叫[ `InstantiateXml()` ](https://developer.xamarin.com/api/member/Urho.Scene.InstantiateXml)。 節點將會建立做為子系的場景，但可以自由重設父代之後。 必須指定位置和旋轉放在節點。 下列程式碼示範如何具現化 prefab`Ninja.xm`到與所要的位置和旋轉的場景：

```csharp
var prefabPath = Path.Combine (FileSystem.ProgramDir,"Data/Objects/Ninja.xml");
using (var file = new File(Context, prefabPath, FileMode.Read))
{
    scene.InstantiateXml(file, desiredPos, desiredRotation,
        CreateMode.Replicated);
}
```

# <a name="events"></a>事件

UrhoObjects 引發的事件數目，這些會當成 C# 事件產生的各種類別。  除了 C# 為根據的事件模型，所以也可以使用`SubscribeToXXX`方法，可讓您訂閱和訂閱語彙基元保留稍後可用來取消訂閱。  差別在於，前者可讓許多訂閱的呼叫端，而第二個只允許一個，但允許沒用 lambda 樣式方法使用，並且棒的是，以輕鬆地將訂用帳戶。  它們是互斥的。

當您訂閱事件時，您必須提供具有適當的事件引數的引數的方法。

比方說，這是您如何訂閱下滑鼠按鈕事件：

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

Lambda 樣式：

```csharp
public void override Start ()
{
    UI.MouseButtonDown += args => {
        Console.WriteLine ("button pressed");
    };
}
```

有時候您會想要停止接收通知的事件，在這些情況下，儲存傳回值，呼叫`SubscribeTo`方法，並叫用它 Unsubscribe 方法：

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

收到的事件處理常式的參數是強類型的事件引數類別是特定的每個事件，其中包含事件裝載。

# <a name="responding-to-user-input"></a>回應使用者輸入

您可以訂閱這類按鍵的各種事件向下訂閱事件，而且回應傳遞的輸入：

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

但在許多情況下，您要檢查索引鍵的目前狀態，它們正在更新，並據此更新您的程式碼時您場景更新處理常式。  例如，下列可用來更新鍵盤輸入所根據的相機位置：

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

# <a name="resources-assets"></a>資源 （資產）

資源包括初始化或執行階段時載入從大量的儲存體中 UrhoSharp 大部分項目：

- [`Animation`](https://developer.xamarin.com/api/type/Urho.Animation/) -用於架構的動畫
- [`Image`](https://developer.xamarin.com/api/type/Urho.Resources.Image) -代表儲存在各種不同的圖形格式的影像
- [`Model`](https://developer.xamarin.com/api/type/Urho.Model/) -3D 模型
- [`Material`](https://developer.xamarin.com/api/type/Urho.Material) -用來呈現模型的資料。
- [`ParticleEffect`](https://developer.xamarin.com/api/type/Urho.ParticleEffect)- [描述](http://urho3d.github.io/documentation/1.4/_particles.html)如何物件發出器的運作方式，請參閱 「[Particles](#particles)"下方。
- [`Shader`](https://developer.xamarin.com/api/type/Urho.Shader) -自訂的著色器
- [`Sound`](https://developer.xamarin.com/api/type/Urho.Audio.Sound) -音效播放，請參閱 「[聲音](#sound)"下。
- [`Technique`](https://developer.xamarin.com/api/type/Urho.Technique/) -材料呈現技術
- [`Texture2D`](https://developer.xamarin.com/api/type/Urho.Urho2D.Texture2D/) -2D 材質
- [`Texture3D`](https://developer.xamarin.com/api/type/Urho.Texture3D/) -3D 材質
- [`TextureCube`](https://developer.xamarin.com/api/type/Urho.TextureCube/) -Cube 紋理
- `XmlFile`

管理和載入[ `ResourceCache` ](https://developer.xamarin.com/api/type/Urho.Resources.ResourceCache/)子系統 (可做為[ `Application.ResourceCache` ](https://developer.xamarin.com/api/property/Urho.Application.ResourceCache/))。

資源本身會識別其檔案路徑，相對於已註冊的資源目錄或封裝檔案。 根據預設，引擎註冊的資源目錄`Data`和`CoreData`，或封裝`Data.pak`和`CoreData.pak`如果有的話。

如果載入資源失敗，將會記錄錯誤，並會傳回 null 參考。

下列範例會顯示資源的快取擷取資源的常見方式。  在此情況下，對 UI 元素的紋理，這會使用`ResourceCache`屬性從`Application`類別。

```csharp
healthBar.SetTexture(ResourceCache.GetTexture2D("Textures/HealthBarBorder.png"));
```

資源也會以手動方式建立和儲存資源的快取，如同它們一直從磁碟載入。

記憶體的預算可以設定每個資源類型： 如果資源耗用超過允許的更多記憶體，最舊的資源將會移除從快取，否則使用中不再。 根據預設記憶體預算會設定為無限制。

## <a name="bringing-3d-models-and-images"></a>將 3D 模型和影像

Urho3D 嘗試使用現有的檔案格式可能的話，並定義自訂的檔案格式，例如模型絕對必要時 (*.mdl) 和動畫 (*.ani)。 針對這些類型的資產，Urho 提供轉換器- [AssetImporter](http://urho3d.github.io/documentation/1.4/_tools.html)消耗許多熱門的 3D 格式，例如 fbx、 dae、 3ds，和 obj 等等。

另外還有好用的增益集 Blender [ https://github.com/reattiva/Urho3D-Blender ](https://github.com/reattiva/Urho3D-Blender) ，可以匯出格式，可適用於 Urho3D Blender 資產。

## <a name="background-loading-of-resources"></a>資源的背景載入

一般來說，當要求資源使用其中一種`ResourceCache`的`Get`方法，它們會在主執行緒，這可能需要數個毫秒所需的步驟中立即載入 （從磁碟載入檔案、 剖析資料，如有必要上, 傳到 GPU)，因此可能會導致畫面播放速率卸除。

如果您知道您需要哪些資源事先，您可以提出要求時要載入背景執行緒中呼叫`BackgroundLoadResource()`。 您可以使用訂閱的背景載入資源事件`SubscribeToResourceBackgroundLoaded`方法。 它會告訴載入實際上是否成功或失敗。 根據資源，只載入程序的一部分可能會移到背景執行緒，例如分頁裝訂的 GPU 上傳步驟一律必須在主執行緒中發生。 請注意，是否您呼叫其中一個資源載入之資源的背景載入排入佇列的方法，在主執行緒會延滯直到完成其載入。

非同步載入功能的場景`LoadAsync()`和`LoadAsyncXML()`有選項可背景載入第一次載入場景內容之前進行的資源。 它也可用來只載入的資源，而不需修改場景，藉由指定`LoadMode.ResourcesOnly`。 這可讓準備快速具現化的場景或物件 prefab 檔案。

最後花費最大時間 （以毫秒為單位） 每個框架上完成設定也可以設定載入的資源的背景`FinishBackgroundResourcesMs`屬性`ResourceCache`。

<a name="sound"/>

# <a name="sound"></a>音效

音效玩遊戲的方式，很重要的一部分且 UrhoSharp framework 提供一種遊戲中播放音效。  您藉由附加播放音效[ `SoundSource` ](https://developer.xamarin.com/api/type/Urho.Audio.SoundSource/)元件至[ `Node` ](https://developer.xamarin.com/api/type/Urho.Node) ，然後播放命名的檔案從您的資源。

這是它的方式：

```csharp
var explosionNode = Scene.CreateChild();
var sound = explosionNode.CreateComponent<SoundSource>();
soundSource.Play(Application.ResourceCache.GetSound("Sounds/ding.wav"));
soundSource.Gain = 0.5f;
soundSource.AutoRemove = true;
```

<a name="particles"/>

# <a name="particles"></a>物件

物件提供簡易的方式加入至應用程式的一些簡單且便宜的影響。  您可以使用 PEX 格式儲存的物件使用的 puttygen 等工具[ http://onebyonedesign.com/flash/particleeditor/ ](http://onebyonedesign.com/flash/particleeditor/)。

物件是可以加入至節點的元件。  您必須呼叫該節點的`CreateComponent<ParticleEmitter2D>`方法來建立物件，並接著設定物件的效果屬性設定為 2D 效果從資源快取載入。

例如，您可以在您的元件，以顯示配接器時，會轉譯為爆炸某些物件上叫用這個方法：

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

上述程式碼會建立附加至目前元件的爆炸節點，此爆炸節點的內部方法，我們可以建立 2D 物件發出器，並將它設定的效果屬性設定。  我們會執行兩個動作的縮放比例較小，節點，一個 0.5 秒的該屬性保留該大小的。  然後我們會移除爆炸，也會從螢幕的移除物件效果。

使用球面紋理時，上述的物件會呈現像這樣：

![使用球面紋理物件](using-images/image-1.png "會呈現上述物件如下，但使用球面紋理")

這是什麼樣子如果您使用塊狀紋理：

![物件與方塊紋理](using-images/image-2.png "，這是什麼樣子如果使用塊狀紋理")

# <a name="multithreading-support"></a>多執行緒支援

UrhoSharp 是單一執行緒程式庫。  這表示您不應嘗試叫用方法中 UrhoSharp 從背景執行緒，或您可能會損毀的應用程式狀態，並可能會損毀您的應用程式。

如果您想要在背景中執行一些程式碼，然後更新 Urho 元件上的主要 UI，您可以使用[ `Application.InvokeOnMain(Action)` ](https://developer.xamarin.com/api/member/Urho.Application.InvokeOnMain)方法。  此外，您可以的使用 await C# 和.NET 工作 Api，以確保適當的執行緒上已執行的程式碼。


# <a name="urhoeditor"></a>UrhoEditor

您可以從您的平台下載 Urho 編輯器[Urho 網站](http://urho3d.github.io/)，移至下載，然後挑選最新版本。

# <a name="copyrights"></a>著作權

這份文件會包含 Xamarin Inc，從原始內容但廣泛繪製從 Urho3D 專案的開放原始碼文件，包含從 Cocos2D 專案的螢幕擷取畫面。



## <a name="related-links"></a>相關連結

- [地球表面活頁簿](https://developer.xamarin.com/workbooks/graphics/urhosharp/planetearth/planetearth.workbook)
- [瀏覽座標活頁簿](https://developer.xamarin.com/workbooks/graphics/urhosharp/coordinates/ExploringUrhoCoordinates.workbook)
