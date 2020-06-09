---
title: 使用 UrhoSharp 建立3D 遊戲
description: 本檔提供 UrhoSharp 的總覽，描述場景、元件、圖形、相機、動作、使用者輸入、音效等等。
ms.prod: xamarin
ms.assetid: D9BEAD83-1D9E-41C3-AD4B-3D87E13674A0
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: f9ea9c4f6f2c920d903bd6f136dd0b98ddc7bf57
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84574323"
---
# <a name="using-urhosharp-to-build-a-3d-game"></a>使用 UrhoSharp 建立3D 遊戲

在撰寫您的第一場遊戲之前，您想要先熟悉基本概念：如何設定您的場景、如何載入資源（這包含您的作品），以及如何為您的遊戲建立簡單的互動。

<a name="scenenodescomponentsandcameras"></a>

## <a name="scenes-nodes-components-and-cameras"></a>場景、節點、元件和相機

場景模型可以描述為以元件為基礎的場景圖形。 場景是由場景節點的階層組成，從根節點開始，這也代表整個場景。 每個 `Node` 都有3d 轉換（位置、旋轉和縮放）、名稱、識別碼，加上任意數目的元件。  元件讓節點進入現實，它們可以加入視覺標記法（ `StaticModel` ），它們可以發出音效（ `SoundSource` ），也可以提供衝突界限等等。

您可以使用 [ [Urho 編輯器](#urhoeditor)] 來建立場景和設定節點，也可以從 c # 程式碼執行作業。  在本檔中，我們將探討如何使用程式碼來設定專案，因為它們說明了讓專案顯示在螢幕上的必要元素

除了設定您的場景以外，您還需要設定 `Camera` ，這會決定要向使用者顯示的內容。

### <a name="setting-up-your-scene"></a>設定場景

您通常會建立此表單的開始方法：

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

### <a name="components"></a>組件

藉由呼叫來建立節點中的不同元件，以轉譯3D 物件、音效播放、物理和已編寫腳本的邏輯更新 `CreateComponent<T>()` 。  例如，設定您的 node 和 light 元件，如下所示：

```csharp
// Create a directional light to the world so that we can see something. The
// light scene node's orientation controls the light direction; we will use
// the SetDirection() function which calculates the orientation from a forward
// direction vector.
// The light will use default settings (white light, no shadows)
var lightNode = scene.CreateChild("DirectionalLight");
lightNode.SetDirection (new Vector3(0.6f, -1.0f, 0.8f));
```

我們已在名稱為 "" 的節點上建立， `DirectionalLight` 並為其設定方向，但不提供任何其他。  現在，我們可以使用下列方式，將上述節點轉換成發光節點 `Light` `CreateComponent` ：

```csharp
var light = lightNode.CreateComponent<Light>();
```

在本身建立的元件 `Scene` 具有特殊角色：可執行整個場景的功能。 它們應該在所有其他元件之前建立，並且包含下列各項：

 `Octree`：實行空間分割和加速可見度查詢。 若沒有此3D 物件，就無法呈現。
 `PhysicsWorld`：實行物理模擬。 物理元件（例如 `RigidBody` 或） `CollisionShape` 在沒有此功能的情況下無法正常運作。
 `DebugRenderer`：執行 debug geometry 轉譯。

一般元件（例如 `Light` 、 `Camera` 或）`StaticModel`
不應該直接建立在 `Scene` 中，而是改為子節點。

程式庫隨附各種不同的元件，可讓您連接到您的節點，以將其融入生活：使用者可見專案（模型）、音效、固定主體、碰撞圖形、相機、光源、物件發射器等等。

### <a name="shapes"></a>圖形

為了方便起見，您可以在 Urho 中以簡單的節點形式提供各種圖形。  這些包括方塊、球體、圓錐、圓柱和飛機。

### <a name="camera-and-viewport"></a>相機和視口

就像光線一樣，攝影機是元件，因此您需要將元件附加至節點，如下所示：

```csharp
var CameraNode = scene.CreateChild ("camera");
camera = CameraNode.CreateComponent<Camera>();
CameraNode.Position = new Vector3 (0, 5, 0);
```

這麼一來，您已建立一個攝影機，並將相機放在3D 世界中，下一個步驟是通知 `Application` 這是您想要使用的攝影機，這是以下列程式碼來完成：

```csharp
Renderer.SetViewPort (0, new Viewport (Context, scene, camera, null))
```

現在您應該能夠看到您建立的結果。

### <a name="identification-and-scene-hierarchy"></a>識別和場景階層

不同于節點，元件沒有名稱;相同節點內的元件只會依據其類型來識別，而節點的元件清單中的索引會填入建立順序，例如，您可以 `Light` 從上述物件取出元件，如下所示 `lightNode` ：

```csharp
var myLight = lightNode.GetComponent<Light>();
```

您也可以取得所有元件的清單，方法是抓取 `Components` `IList<Component>` 會傳回可供您使用之的屬性。

建立時，節點和元件都會取得場景全域整數識別碼。 您可以使用和函式，從場景查詢這些 `GetNode(uint id)` 函數 `GetComponent(uint id)` 。 這比執行以遞迴名稱為基礎的場景節點查詢更快。

實體或遊戲物件沒有內建的概念;而是由程式設計師決定節點階層，以及在哪些節點放置任何已編寫腳本的邏輯。 一般而言，3D 世界中的自由移動物件會建立為根節點的子系。 您可以使用或不搭配名稱來建立節點 `CreateChild` 。 不會強制執行節點名稱的唯一性。

每當有階層式組合時，建議使用（事實上，因為元件沒有自己的3D 轉換）來建立子節點。

例如，如果某個字元在手中保留物件，物件應該會有自己的節點，而這會是該字元的右手骨骼（也就是）的父代 `Node` 。  例外狀況是物理 `CollisionShape` ，可以與節點相對地個別 offsetted 和旋轉。

請注意， `Scene` 在計算子節點的世界衍生轉換時，會將自己的轉換特意忽略為優化，因此，變更它不會有任何作用，而且應該保持不變（原點位置、不旋轉、不進行調整）。

`Scene`節點可以自由重做。 相較之下，元件一定會屬於其所連結的節點，而且無法在節點之間移動。 節點和元件都會提供函式 `Remove` 來完成此動作，而不需要經過父系。 移除節點之後，在呼叫該函式之後，沒有問題的節點或元件上的任何作業都是安全的。

您也可以建立 `Node` 不屬於場景的。 這適用于在可能載入或儲存的場景中移動相機的範例，因為相機將不會與實際場景一起儲存，而且不會在載入場景時終結。 不過，請注意，建立幾何、物理或腳本元件至未連結的節點，然後將它移到場景中，會導致這些元件無法正常運作。

### <a name="scene-updates"></a>場景更新

其更新已啟用（預設）的場景會自動在每個主要迴圈反復專案上更新。  應用程式的 `SceneUpdate` 事件處理常式會在其上叫用。

藉由停用節點和元件，可以將其從場景更新中排除，請參閱 `Enabled` 。  行為取決於特定的元件，但停用可繪製的元件也會使其不可見，同時停用音效來源元件 mutes。 如果節點已停用，不論其本身的啟用/停用狀態為何，其所有元件都會視為停用。

## <a name="adding-behavior-to-your-components"></a>將行為新增至您的元件

建立遊戲結構的最佳方式是讓自己的元件在遊戲中封裝一個動作專案或元素。  這會使功能自我自主（從用來顯示的資產）到其行為。

將行為新增至元件最簡單的方式是使用動作，這是您可以將其排入佇列，並將其與 c # 非同步程式設計結合的指示。  這可讓您元件的行為非常高，並可讓您更輕鬆地瞭解所發生的情況。

或者，您可以藉由更新每個畫面格上的元件屬性（在以框架為基礎的行為一節中所討論），來精確控制您的元件會發生什麼事。

### <a name="actions"></a>動作

您可以使用動作輕鬆地將行為新增至節點。  動作可以改變不同的節點屬性，並在一段時間內執行，或使用指定的動畫曲線重複數次。

例如，假設您的場景上有一個「雲端」節點，您可以像這樣淡入：

```csharp
await cloud.RunActionsAsync (new FadeOut (duration: 3))
```

動作是不可變的物件，可讓您重複使用驅動不同物件的動作。

常見的用法是建立執行反向運算的動作：

```csharp
var gotoExit = new MoveTo (duration: 3, position: exitLocation);
var return = gotoExit.Reverse ();
```

下列範例會在三秒的期間內為您淡出物件。  您也可以在另一個動作之後執行，例如，您可以先移動雲端，然後將它隱藏：

```csharp
await cloud.RunActionsAsync (
    new MoveBy  (duration: 1.5f, position: new Vector3(0, 0, 15),
    new FadeOut (duration: 3));
```

如果您想要同時執行這兩個動作，您可以使用 [平行動作]，並提供您想要平行完成的所有動作：

```csharp
  await cloud.RunActionsAsync (
    new Parallel (
      new MoveBy  (duration: 3, position: new Vector3(0, 0, 15),
      new FadeOut (duration: 3)));
```

在上述範例中，雲端會同時移動和淡出。

您會發現這些使用的是 c # `await` ，可讓您以線性方式思考想要達成的行為。

### <a name="basic-actions"></a>基本動作

以下是 UrhoSharp 中支援的動作：

- 移動節點： `MoveTo` 、 `MoveBy` 、 `Place` 、 `BezierTo` 、 `BezierBy` 、 `JumpTo` 、`JumpBy`
- 旋轉節點： `RotateTo` 、`RotateBy`
- 調整節點： `ScaleTo` 、`ScaleBy`
- 漸淡的節點： `FadeIn` 、 `FadeTo` 、 `FadeOut` 、 `Hide` 、`Blink`
- 淡色： `TintTo` 、`TintBy`
- 時刻： `Hide` 、 `Show` 、 `Place` 、 `RemoveSelf` 、`ToggleVisibility`
- 迴圈： `Repeat` 、 `RepeatForever` 、`ReverseTime`

其他的 advanced 功能包括 `Spawn` 和動作的組合 `Sequence` 。

### <a name="easing---controlling-the-speed-of-your-actions"></a>簡化-控制動作的速度

「簡化」是一種方法，可引導動畫的展開方式，讓您的動畫更愉快。  根據預設，您的動作會有線性行為，例如 `MoveTo` 動作會有非常機器人的移動。  您可以將動作包裝成簡化動作來變更行為，例如，會慢慢開始移動、加速和緩慢到達結尾（ `EasyInOut` ）。

若要這麼做，您可以將現有的動作包裝成緩動動作，例如：

```csharp
await cloud.RunActionAsync (
   new EaseInOut (
     new MoveTo (duration: 3, position: new Vector (0,0,15)), rate:1))
```

有許多簡化模式，下圖顯示各種緩動類型及其在一段時間內所控制物件值的行為，從開始到完成：

![簡化模式](using-images/easing.png "此圖表顯示各種緩動類型及其在一段時間內控制之物件值的行為")

### <a name="using-actions-and-async-code"></a>使用動作和非同步程式碼

在您的子 `Component` 類別中，您應該引進非同步方法來準備元件行為，並為其驅動其功能。
接著，您會從程式的另一個部分使用 c # 關鍵字來叫用這個方法，或是在您的 `await` `Application.Start` 應用程式中回應使用者或故事點的方法。

例如：

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

在上述的方法中， `Launch` 會啟動三個動作：機器人進入場景，此動作會在0.6 秒的期間內改變節點的位置。  因為這是非同步選項，所以這會同時做為呼叫的下一個指令 `MoveRandomly` 。  這個方法會將機器人的位置平行變更為隨機位置。  這是藉由執行兩個複雜的動作，移至新位置，然後回到原始位置並重複這項操作，只要機器人維持在運作狀態即可。  為了讓事情更有趣，機器人會同時繼續進行診斷。  每隔0.1 秒就會開始進行此診斷。

### <a name="frame-based-behavior-programming"></a>以框架為基礎的行為程式設計

如果您想要以框架為基礎來控制元件的行為，而不是使用動作，您可以覆寫子 `OnUpdate` 類別的方法 `Component` 。  這個方法會針對每個畫面叫用一次，而且只有在您將 ReceiveSceneUpdates 屬性設定為 true 時，才會叫用。

下圖顯示如何建立 `Rotator` 元件，然後將它附加至節點，這會導致節點旋轉：

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
            RotationSpeed.X  timeStep,
            RotationSpeed.Y  timeStep,
            RotationSpeed.Z  timeStep),
            TransformSpace.Local);
    }
}
```

這就是您將此元件附加至節點的方式：

```csharp
Node boxNode = new Node();
var rotator = new Rotator() { RotationSpeed = rotationSpeed };
boxNode.AddComponent (rotator);
```

### <a name="combining-styles"></a>結合樣式

您可以使用非同步/以動作為基礎的模型來程式設計大部分的行為，這很適合用於程式設計的暫時模式，但是您也可以微調元件的行為，以便在每個框架上執行一些更新程式碼。

例如，在 SamplyGame 示範中，此類別會使用 `Enemy` 動作來編碼基本行為，但它也可確保元件會藉由設定節點的方向來指向使用者 `Node.LookAt` ：

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

場景可以載入並以 XML 格式儲存;請參閱函數 `LoadXml` 和 `SaveXML` 。 載入場景時，會先移除其中的所有現有內容（子節點和元件）。 將不會儲存以屬性標示為暫時性的節點和元件 `Temporary` 。 序列化程式會處理所有內建元件和屬性，但它不夠聰明，無法處理元件子類別中定義的自訂屬性和欄位。 不過，它會為此提供兩個虛擬方法：

 `OnSerialize`您可以在這裡註冊自訂狀態以進行序列化

 `OnDeserialized`您可以在其中取得已儲存的自訂狀態。

一般而言，自訂群組件看起來會像下面這樣：

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

### <a name="object-prefabs"></a>物件 prefabs

只是載入或儲存整個場景，對於需要動態建立新物件的遊戲而言並沒有太大的彈性。 另一方面，在程式碼中建立複雜物件和設定其屬性也會很繁瑣。 基於這個理由，您也可以儲存包含其子節點、元件和屬性的場景節點。 稍後可以輕鬆地將這些載入為群組。  這類儲存的物件通常稱為 prefab。 有三種方式可以執行此動作：

- 在程式碼中呼叫 `Node.SaveXml` 節點上的
- 在編輯器中，選取 [階層] 視窗中的節點，然後從 [檔案] 功能表中選擇 [另存節點為]。
- 使用中的 "node" 命令 `AssetImporter` ，這會儲存場景節點階層，以及包含在輸入資產中的任何模型（例如 Collada 檔案）

若要將儲存的節點具現化至場景，請呼叫 `InstantiateXml` 。 該節點將會建立為場景的子系，但是可以在這之後自由地重設父代。 需要指定放置節點的位置和旋轉。 下列程式碼示範如何將 prefab 具現化 `Ninja.xm` 至具有所需位置和旋轉的場景：

```csharp
var prefabPath = Path.Combine (FileSystem.ProgramDir,"Data/Objects/Ninja.xml");
using (var file = new File(Context, prefabPath, FileMode.Read))
{
    scene.InstantiateXml(file, desiredPos, desiredRotation,
        CreateMode.Replicated);
}
```

## <a name="events"></a>事件

UrhoObjects 會引發一些事件，這些事件會在產生它們的各種類別上呈現為 c # 事件。  除了以 c # 為基礎的事件模型之外，您也可以使用 `SubscribeToXXX` 方法，讓您訂閱並保留可供稍後用來取消訂閱的訂用帳戶權杖。  其差異在於前者會允許許多呼叫端訂閱，而第二個則只允許一個，但允許使用更好的 lambda 樣式方法，並允許輕鬆移除訂用帳戶。  它們彼此互斥。

當您訂閱事件時，您必須提供可接受引數與適當事件引數的方法。

例如，這就是您訂閱滑鼠按鍵關閉事件的方式：

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

有時候您會想要停止接收事件的通知，在這些情況下，請將呼叫中的傳回值儲存至 `SubscribeTo` 方法，並在其上叫用取消訂閱方法：

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

事件處理常式收到的參數是強型別事件引數類別，它會針對每個事件指定，並包含事件裝載。

## <a name="responding-to-user-input"></a>回應使用者輸入

您可以訂閱各種事件（例如，藉由訂閱事件來關閉按鍵），並回應傳遞的輸入：

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

但在許多情況下，您會希望場景更新處理常式在更新時檢查金鑰的目前狀態，並據此更新您的程式碼。  例如，您可以根據鍵盤輸入，使用下列內容來更新相機位置：

```csharp
protected override void OnUpdate(float timeStep)
{
    Input input = Input;
    // Movement speed as world units per second
    const float moveSpeed = 4.0f;
    // Read WASD keys and move the camera scene node to the
    // corresponding direction if they are pressed
    if (input.GetKeyDown(Key.W))
        CameraNode.Translate(Vector3.UnitY  moveSpeed  timeStep, TransformSpace.Local);
    if (input.GetKeyDown(Key.S))
        CameraNode.Translate(new Vector3(0.0f, -1.0f, 0.0f)  moveSpeed  timeStep, TransformSpace.Local);
    if (input.GetKeyDown(Key.A))
        CameraNode.Translate(new Vector3(-1.0f, 0.0f, 0.0f)  moveSpeed  timeStep, TransformSpace.Local);
    if (input.GetKeyDown(Key.D))
        CameraNode.Translate(Vector3.UnitX  moveSpeed  timeStep, TransformSpace.Local);
}
```

## <a name="resources-assets"></a>資源（資產）

資源包括在初始化或執行時間期間，從大量存放裝置載入 UrhoSharp 中的大部分專案：

- `Animation`-用於框架動畫
- `Image`-代表以各種圖形格式儲存的影像
- `Model`-3D 模型
- `Material`-用來呈現模型的材質。
- `ParticleEffect`- [描述](https://urho3d.github.io/documentation/1.4/_particles.html)物件發射器如何運作，請參閱下方的「[粒子](#particles)」。
- `Shader`-自訂著色器
- `Sound`-播放的聲音，請參閱下方的「[音效](#sound)」。
- `Technique`-材質轉譯技術
- `Texture2D`-2D 材質
- `Texture3D`-3D 材質
- `TextureCube`-Cube 材質
- `XmlFile`

這些是由子系統管理和載入 `ResourceCache` （以形式提供 `Application.ResourceCache` ）。

資源本身的識別方式是其檔案路徑，相對於已註冊的資原始目錄或封裝檔案。 根據預設，引擎會註冊資原始目錄 `Data` 和 `CoreData` 、封裝 `Data.pak` 和（ `CoreData.pak` 如果有的話）。

如果載入資源失敗，將會記錄錯誤，並傳回 null 參考。

下列範例顯示從資源快取提取資源的一般方式。  在此情況下，UI 元素的材質會使用 `ResourceCache` 類別中的屬性 `Application` 。

```csharp
healthBar.SetTexture(ResourceCache.GetTexture2D("Textures/HealthBarBorder.png"));
```

您也可以手動建立資源，並將其儲存到資源快取，如同已從磁片載入一樣。

可以根據資源類型設定記憶體預算：如果資源耗用的記憶體超過允許的數量，則會從快取中移除最舊的資源（如果不再使用的話）。 根據預設，記憶體預算設定為 [無限制]。

### <a name="bringing-3d-models-and-images"></a>帶入3D 模型和影像

Urho3D 嘗試盡可能使用現有的檔案格式，並只在必要時（例如模型（. mdl）和動畫（ani））定義自訂檔案格式。 針對這些類型的資產，Urho 提供了[AssetImporter](https://urho3d.github.io/documentation/1.4/_tools.html) ，其可以使用許多常用的3d 格式，例如 fbx、dae、3ds 和 obj 等等。

此外，也有一個方便 Blender 的增益集 [https://github.com/reattiva/Urho3D-Blender](https://github.com/reattiva/Urho3D-Blender) ，可以使用適用于 Urho3D 的格式來匯出您的 Blender 資產。

### <a name="background-loading-of-resources"></a>資源的背景載入

一般來說，使用其中一個方法來要求資源時， `ResourceCache` `Get` 它們會立即載入主執行緒中，而所有必要的步驟（從磁片載入檔案、剖析資料、視需要上傳至 GPU）可能需要數毫秒的時間，因此會導致畫面播放速率下降。

如果您事先知道所需的資源，您可以呼叫以要求在背景執行緒中載入它們 `BackgroundLoadResource` 。 您可以使用方法來訂閱資源背景載入事件 `SubscribeToResourceBackgroundLoaded` 。 它會判斷載入是否真的成功或失敗。 視資源而定，只有一部分的載入程式可以移至背景執行緒，例如完成的 GPU 上傳步驟一律必須在主執行緒中發生。 請注意，如果您針對已排入背景載入的資源呼叫其中一個資源載入方法，主執行緒將會停止，直到其載入完成為止。

非同步場景載入功能 `LoadAsync` ，而且可以 `LoadAsyncXML` 選擇在繼續載入場景內容之前先載入資源。 您也可以藉由指定，在不修改場景的情況下，用來載入資源 `LoadMode.ResourcesOnly` 。 這可讓準備場景或物件 prefab 檔，以快速具現化。

最後，您可以藉由在上設定屬性，來設定每個完成背景載入資源的每個畫面格所花費的最長時間（以毫秒為單位） `FinishBackgroundResourcesMs` `ResourceCache` 。

<a name="sound"></a>

## <a name="sound"></a>音效

音效是遊戲播放的重要部分，而 UrhoSharp 架構提供了一種在遊戲中播放聲音的方式。  您可以藉由附加來播放聲音`SoundSource`
元件到 `Node` ，然後從您的資源播放已命名的檔案。

這就是其完成方式：

```csharp
var explosionNode = Scene.CreateChild();
var sound = explosionNode.CreateComponent<SoundSource>();
soundSource.Play(Application.ResourceCache.GetSound("Sounds/ding.wav"));
soundSource.Gain = 0.5f;
soundSource.AutoRemove = true;
```

<a name="particles"></a>

## <a name="particles"></a>物件

「粒子」提供簡單的方法，將一些簡單且價格實惠的效果新增至您的應用程式。  您可以使用之類的工具，取用以 PEX 格式儲存的微粒 [http://onebyonedesign.com/flash/particleeditor/](http://onebyonedesign.com/flash/particleeditor/) 。

「粒子」是可以新增至節點的元件。  您必須呼叫節點的 `CreateComponent<ParticleEmitter2D>` 方法來建立物件，然後將 [效果] 屬性設定為從資源快取載入的2d 效果，以設定物件。

例如，您可以在您的元件上叫用這個方法，以顯示在到達時呈現為爆炸的部分粒子：

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

上述程式碼會建立一個已附加至您目前元件的分解節點，在此分解節點中，我們會建立2D 物件發射器，並藉由設定效果屬性來加以設定。  我們會執行兩個動作，其中一個會將節點調整為較小，另一個則會將其保留為0.5 秒的大小。  然後我們會移除激增，這也會從螢幕中移除 [物件] 效果。

使用球體材質時，上述物件的呈現方式如下：

![具有球體材質的微粒](using-images/image-1.png "使用球體材質時，上述物件的呈現方式如下所示")

如果您使用 blocky 材質，這就是它的外觀：

![具有盒材質的微粒](using-images/image-2.png "這就是使用 blocky 材質時的外觀")

## <a name="multi-threading-support"></a>多執行緒支援

UrhoSharp 是單一執行緒程式庫。  這表示您不應該嘗試從背景執行緒叫用 UrhoSharp 中的方法，或者您有損毀應用程式狀態的風險，而且可能會讓應用程式當機。

如果您想要在背景執行某些程式碼，然後更新主要 UI 上的 Urho 元件，您可以使用`Application.InvokeOnMain(Action)`
方法。  此外，您可以使用 c # await 和 .NET 工作 Api，以確保程式碼會在適當的執行緒上執行。

## <a name="urhoeditor"></a>UrhoEditor

您可以從[Urho 網站](http://urho3d.github.io/)下載適用于您平臺的 [Urho 編輯器]，移至 [下載] 並挑選最新版本。

## <a name="copyrights"></a>版權所有

本檔包含 Xamarin Inc. 的原始內容，但會廣泛地從 Urho3D 專案的開放原始碼檔中進行繪製，並包含 Cocos2D 專案的螢幕擷取畫面。
