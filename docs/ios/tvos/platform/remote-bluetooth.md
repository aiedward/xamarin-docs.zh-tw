---
title: Siri 遠端和藍牙控制器在 Xamarin 中 tvOS 的
description: 本文說明如何使用 Siri 遠端和藍牙遊戲控制器中使用 Xamarin 所撰寫的 tvOS 應用程式。
ms.prod: xamarin
ms.assetid: BDB9894A-236B-424B-9032-ACD12A6C5720
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 14c62051afd7489389f154c21b3a76b9aad3f32e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115532"
---
# <a name="siri-remote-and-bluetooth-controllers-for-tvos-in-xamarin"></a>Siri 遠端和藍牙控制器在 Xamarin 中 tvOS 的

Xamarin.tvOS 應用程式的使用者不會互動的介面直接做為 ios，他們點選裝置的畫面上的映像但間接從之間的空間使用[Siri 遠端](#The-Siri-Remote)。

如果您的應用程式是遊戲，您可以選擇性地建置的第 3 個合作對象，做為支援 iOS (MFI)[藍芽遊戲控制器](#Bluetooth-Game-Controllers)您應用程式中。

[![](remote-bluetooth-images/intro01.png "藍芽遠端及遊戲控制器")](remote-bluetooth-images/intro01.png#lightbox)

這篇文章說明[Siri 遠端](#The-Siri-Remote)，[觸控介面筆勢](#Touch-Surface-Gestures)並[Siri 遠端按鈕](#Siri-Remote-Buttons)並示範如何使用它們透過[筆勢和分鏡腳本](#Gestures-and-Storyboards)，[筆勢和程式碼](#Gestures-and-Code)並[低層級事件處理](#Low-Level-Event-Handling)。 最後，它會討論[使用 遊戲控制器](#Working-with-Game-Controllers)Xamarin.tvOS 應用程式中。

<a name="The-Siri-Remote" />

## <a name="the-siri-remote"></a>Siri 遠端

使用者會與 Apple TV 中，與 Xamarin.tvOS 應用程式之間互動的主要方式是透過包含 Siri 遠端。 Apple 設計來橋接坐在沙發上並顯示在電視螢幕上房間對面的 Apple TV 使用者介面上的使用者之間的距離遠端。

您身為 tvOS 應用程式開發人員的挑戰就是建立快速、 容易使用且引人注目的使用者介面，會利用 Siri 遠端的觸控介面、 加速計、 陀螺儀和按鈕。

[![](remote-bluetooth-images/remote01.png "Siri 遠端")](remote-bluetooth-images/remote01.png#lightbox)

Siri 遠端提供下列功能和 tvOS 應用程式內的預期使用方式：

|功能|一般應用程式使用量|遊戲應用程式使用量|
|---|---|---|
|**觸控式表面**<br />若要瀏覽，來選取並按住的關聯式功能表按下撥動。|**點選/撥動**<br />UI 可焦點化項目之間的導覽。<br /><br />**按一下**<br />啟動選取的 （焦點） 項目。|**點選/撥動**<br />遊戲設計而定，可以當成 D-pad 點選邊緣。<br /><br />**按一下**<br />執行主要按鈕的功能。|
|**Menu**<br />按下返回上一個畫面或功能表。|返回上一個畫面，並從主要的應用程式 畫面會跳到 Apple 電視主畫面。|暫停和繼續遊戲，傳回到前一個畫面，結束到 Apple 電視主畫面從主應用程式畫面。|
|**Siri/搜尋**<br />使用 Siri 的國家/地區，請按住語音控制項，所有其他國家/地區，顯示搜尋畫面。|N/A|N/A|
|**矔菛/縸**<br />播放和暫停媒體或提供應用程式中的第二個函式。|啟動媒體的播放和暫停/繼續播放。|執行次要按鈕的功能，或略過簡介影片 (如果存在)。|
|**Home**<br />若要返回 [首頁] 畫面按下，連按兩下以顯示執行應用程式，請按住進入睡眠狀態的裝置。|N/A|N/A|
|**磁碟區**<br />控制項附加音訊/視訊設備的磁碟區。|N/A|N/A|

<a name="Touch-Surface-Gestures" />

## <a name="touch-surface-gestures"></a>觸控介面筆勢

Siri 遠端的觸控介面是能夠偵測各種不同的單指筆勢，您可以回應 Xamarin.tvOS 應用程式中：

|揮擊|按一下|點選|
|---|---|---|
|![](remote-bluetooth-images/Gesture01.png)|![](remote-bluetooth-images/Gesture02.png)|![](remote-bluetooth-images/Gesture03.png)|
|在畫面上的 UI 項目之間移動選取項目 （焦點） （上、 下左、 右）。 撥動可捲動的內容使用快速慣性的大型清單。|啟動選取的項目 （焦點），或像是遊戲中的 [主要] 按鈕。 按一下並按住，可以啟用關聯式功能表或次要的函式。|輕度點選 觸控介面的邊緣上就像是方向鍵，將焦點移增加、 下、 左或右依據點選的區域上的方向按鈕。 根據應用程式，可以用來顯示隱藏的控制項。|

Apple 提供下列建議使用觸控介面筆勢：

* **區分按一下和點選**-按一下是刻意的動作，使用者並是適合用來選取項目、 啟用和遊戲的主要按鈕。 點選更為合適，因為使用者通常在他們手持有 Siri 遠端，而不小心可以輕鬆地啟用點選事件應該謹慎使用。
* **不重新定義標準的筆勢**-使用者可以預期特定的筆勢，會執行特定動作，您不應該重新定義意義或函式，這些筆勢的應用程式中。 唯一例外的是在作用中的遊戲的遊戲應用程式。
* **定義新筆勢盡量不要**-使用者同樣地，具有預期特定的筆勢，會執行特定動作。 您應避免定義自訂的筆勢來執行一般動作。 而同樣地，遊戲會是最常見的例外狀況可以在其中自訂軌跡加入有趣、 擬真遊戲的遊戲。
* **視情況回應 D-pad 點選**-輕點一下 觸控介面的邊緣會做出回應，例如方向鍵將焦點移遊戲控制器或方向下、, 左或右角。 如果適當的話，您應該回應您的應用程式或遊戲中的這些筆勢。

<a name="Siri-Remote-Buttons" />

## <a name="siri-remote-buttons"></a>Siri 遠端按鈕

在觸控式介面上的手勢，除了您的應用程式可以回應使用者按一下 觸控 介面，或按下 播放/暫停按鈕。 如果您要存取 Siri 遠端使用遊戲控制器架構，您也可以偵測按下功能表按鈕。 

此外，按下功能表按鈕可以偵測到筆勢辨識器使用標準`UIKit`項目。 如果您攔截按下功能表按鈕時，您就可以負責關閉目前的檢視和檢視控制器，並返回前一個。

> [!IMPORTANT]
> 您應該**一律**函式指派給遙控器上的 [矔菛/縸] 按鈕。 擁有非功能性的按鈕可以讓您尋找中斷使用者的應用程式。 如果您沒有有效的函式，此按鈕，將指派相同的函式，做為主要按鈕 （觸控介面按一下）。

<a name="Gestures-and-Storyboards" />

## <a name="gestures-and-storyboards"></a>筆勢和分鏡腳本

使用 Siri 遠端 Xamarin.tvOS 應用程式中的最簡單方式是將筆勢辨識器新增至您在介面設計工具中的檢視。

若要新增的筆勢辨識器，請執行下列作業：

1. 在 **方案總管**，連按兩下`Main.storyboard`檔案，並開啟它進行編輯介面設計工具。
2. 拖曳**點選 筆勢辨識器**從**程式庫**並將它放在檢視上： 

    [![](remote-bluetooth-images/storyboard01.png "點選 筆勢辨識器")](remote-bluetooth-images/storyboard01.png#lightbox)
3. 請檢查**選取 [** 中 **] 按鈕**一節**屬性偵測器**: 

    [![](remote-bluetooth-images/storyboard02.png "檢查選取")](remote-bluetooth-images/storyboard02.png#lightbox)
4. **選取**表示會回應使用者按一下 筆勢**觸控介面**Siri 遠端。 您也可以選擇回應** 功能表**，**矔菛/縸**，**向上**，**向下**，**左**和**右**按鈕。
5. 接下來，接通**動作**從**點選 筆勢辨識器**，並為它`TouchSurfaceClicked`: 

    [![](remote-bluetooth-images/storyboard03.png "點選 筆勢辨識器動作")](remote-bluetooth-images/storyboard03.png#lightbox)
6. 儲存變更並返回 Visual Studio for mac。

編輯您的檢視控制器 (範例`FirstViewController.cs`) 檔案，並新增下列程式碼來處理所觸發的筆勢：

```csharp
using System;
using UIKit;

namespace tvRemote
{
    public partial class FirstViewController : UIViewController
    {
        ...

        #region Custom Actions
        partial void TouchSurfaceClicked (Foundation.NSObject sender) {
            // Handle click here
            ...
        }
        #endregion
    }
}
```

如需有關使用分鏡腳本的詳細資訊，請參閱我們[Hello，tvOS 快速入門指南](~/ios/tvos/get-started/hello-tvos.md)。 特別[建立使用者介面](~/ios/tvos/get-started/hello-tvos.md#Creating-the-User-Interface)並[撰寫程式碼輸出和動作](~/ios/tvos/get-started/hello-tvos.md#Writing-the-Code)區段。

<a name="Gestures-and-Code" />

## <a name="gestures-and-code"></a>筆勢和程式碼

（選擇性） 您可以建立筆勢直接在C#程式碼，並將它們新增至您的使用者介面中的檢視。 比方說，若要新增一系列的撥動筆勢辨識器，請編輯您的檢視控制器並新增下列程式碼：

```csharp
using System;
using UIKit;

namespace tvRemote
{
    public partial class SecondViewController : UIViewController
    {
        #region Constructors
        public SecondViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();    

            // Wire-up gestures
            var upGesture = new UISwipeGestureRecognizer (() => {
                RemoteView.ArrowPressed = "Up";
                ButtonLabel.Text = "Swiped Up";
            }) {
                Direction = UISwipeGestureRecognizerDirection.Up
            };
            this.View.AddGestureRecognizer (upGesture);

            var downGesture = new UISwipeGestureRecognizer (() => {
                RemoteView.ArrowPressed = "Down";
                ButtonLabel.Text = "Swiped Down";
            }) {
                Direction = UISwipeGestureRecognizerDirection.Down
            };
            this.View.AddGestureRecognizer (downGesture);

            var leftGesture = new UISwipeGestureRecognizer (() => {
                RemoteView.ArrowPressed = "Left";
                ButtonLabel.Text = "Swiped Left";
            }) {
                Direction = UISwipeGestureRecognizerDirection.Left
            };
            this.View.AddGestureRecognizer (leftGesture);

            var rightGesture = new UISwipeGestureRecognizer (() => {
                RemoteView.ArrowPressed = "Right";
                ButtonLabel.Text = "Swiped Right";
            }) {
                Direction = UISwipeGestureRecognizerDirection.Right
            };
            this.View.AddGestureRecognizer (rightGesture);
        }
        #endregion
    }
}
```

<a name="Low-Level-Event-Handling" />

## <a name="low-level-event-handling"></a>低層級的事件處理

如果您要建立自訂的型別，根據`UIKit`Xamarin.tvOS 應用程式中 (例如`UIView`)，您也可以提供透過按下按鈕的低階處理`UIPress`事件。 

A`UIPress`事件是以 tvOS`UITouch`事件是否為 iOS，除了`UIPress`傳回 Siri 遠端上按下的按鈕的相關資訊，或其他連結藍芽裝置 （例如遊戲控制器）。 `UIPress` 事件說明按下按鈕和它的狀態 （開始、 取消、 變更或已結束）。 

在藍芽遊戲與控制器類似，裝置上的類比按鈕`UIPress`也會傳回套用至按鈕的強制數量。 `Type`屬性`UIPress`事件可讓您定義的實體按鈕已變更的狀態，而其餘的屬性描述所發生的變更。

下列程式碼示範處理低階`UIPress`事件`UIView`:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvRemote
{
    public partial class EventView : UIView
    {
        #region Computed Properties
        public override bool CanBecomeFocused {
            get {
                return true;
            }
        }
        #endregion

        #region 
        public EventView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void PressesBegan (NSSet<UIPress> presses, UIPressesEvent evt)
        {
            base.PressesBegan (presses, evt);

            foreach (UIPress press in presses) {
                // Was the Touch Surface clicked?
                if (press.Type == UIPressType.Select) {
                    BackgroundColor = UIColor.Red;
                }
            }
        }

        public override void PressesCancelled (NSSet<UIPress> presses, UIPressesEvent evt)
        {
            base.PressesCancelled (presses, evt);

            foreach (UIPress press in presses) {
                // Was the Touch Surface clicked?
                if (press.Type == UIPressType.Select) {
                    BackgroundColor = UIColor.Clear;
                }
            }
        }

        public override void PressesChanged (NSSet<UIPress> presses, UIPressesEvent evt)
        {
            base.PressesChanged (presses, evt);
        }

        public override void PressesEnded (NSSet<UIPress> presses, UIPressesEvent evt)
        {
            base.PressesEnded (presses, evt);

            foreach (UIPress press in presses) {
                // Was the Touch Surface clicked?
                if (press.Type == UIPressType.Select) {
                    BackgroundColor = UIColor.Clear;
                }
            }
        }
        #endregion
    }
}
```

如同`UITouch`事件，如果您需要實作的任何`UIPress`事件覆寫，則應實作所有的四個。

<a name="Bluetooth-Game-Controllers" />

## <a name="bluetooth-game-controllers"></a>藍芽遊戲控制器

除了標準 Siri 遠端隨附於 Apple TV、 第 3 方、 所做的 iOS (MFI) 藍芽遊戲控制器可以搭配 Apple TV，用於控制 Xamarin.tvOS 應用程式。

[![](remote-bluetooth-images/game01.png "藍芽遊戲控制器")](remote-bluetooth-images/game01.png#lightbox)

遊戲控制器可用來增強遊戲，而且提供了解在遊戲中的訓練活動的使用中。 它們也可以用來控制標準的 Apple TV 介面，因此不需要使用遠端和控制器之間切換。

> [!IMPORTANT]
> 藍芽遊戲控制器可能會讓終端使用者的選選購，您的應用程式不會強制使用者進行購買。 如果您的應用程式支援遊戲控制器就必須也支援 Siri 遠端，以便在遊戲是由 Apple TV 的所有使用者使用。

遊戲控制器具有下列功能和 tvOS 應用程式內的預期使用方式：

|功能|一般應用程式使用量|遊戲應用程式使用量|
|---|---|---|
|**D-Pad**|巡覽 UI 項目 （將焦點變更）。|取決於遊戲。|
|**A**|啟動選取的項目 （焦點）。|會執行主要按鈕的功能，並確認對話方塊中的動作。|
|**B**|傳回前一個畫面，或如果在 應用程式的主畫面上，則會跳到 首頁 畫面。|執行次要按鈕的功能，或在上一個螢幕。|
|**X**|啟動媒體的播放或暫停/繼續播放。|取決於遊戲。|
|**Y**|N/A|取決於遊戲。|
|**Menu**|傳回前一個畫面，或如果在 應用程式的主畫面上，則會跳到 首頁 畫面。|暫停/繼續應用程式的主畫面上如果遊戲，傳回到前一個畫面或主畫面的結束。|
|**左的 Shoulder 按鈕**|瀏覽左側。|取決於遊戲。|
|**左側的觸發程序**|瀏覽左側。|取決於遊戲。|
|**右 Shoulder 按鈕**|瀏覽權限。|取決於遊戲。|
|**正確的觸發程序**|瀏覽權限|取決於遊戲。|
|**左搖桿**|巡覽 UI 項目 （將焦點變更）。|取決於遊戲。|
|**右搖桿**|N/A|取決於遊戲。|

Apple 提供下列建議使用 遊戲控制器：

- **確認遊戲控制器連線**-您的 tvOS 應用程式可以啟動和停止隨時終端使用者。 您一定要檢查的遊戲控制器在啟動應用程式] 或 [醒著的時間存在並視需要採取動作。
- **請確定您的應用程式可以運作上 Siri 遠端和遊戲控制器**-不需要使用者 Siri 遠端和遊戲控制器，若要使用您的應用程式之間切換。 測試您的應用程式通常搭配這兩種類型的控制站，確定所有項目是容易瀏覽，並且如預期般運作。
- **提供方式回**-按下功能表按鈕應該一律傳回上一個螢幕。 如果使用者是在主應用程式畫面上，[功能表] 按鈕應該傳回它們以 Apple TV 主畫面。 在遊戲，[功能表] 按鈕應該會顯示警示，讓使用者可以暫停/繼續遊戲，或返回主功能表。

<a name="Working-with-Game-Controllers" />

## <a name="working-with-game-controllers"></a>使用 遊戲控制器

如上所述，除了標準 Siri 遠端隨附使用 Apple TV 中，使用者可以選擇連接第 3 方，做為 iOS (MFI) 藍芽遊戲控制器，並使用它來控制 Xamarin.tvOS 應用程式。

如果您的應用程式需要低層級的控制站的輸入，您可以使用 Apple[遊戲控制器架構](https://developer.apple.com/library/prerelease/tvos/documentation/ServicesDiscovery/Conceptual/GameControllerPG/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013276)具有 tvOS 的下列修改：

- Micro 遊戲控制器設定檔 (`GCMicroGamepad`) 已新增至目標 Siri 遠端。
- 新`GCEventViewController`類別可用來將遊戲控制器透過您的應用程式的事件路由。 請參閱[判斷遊戲控制器輸入](#Determining-Game-Controller-Input)節以取得詳細資料。

<a name="Game-Controller-Support-Requirements" />

### <a name="game-controller-support-requirements"></a>遊戲控制器支援需求

Apple 會有幾項特定的需求，如果您的 Xamarin.tvOS 應用程式支援遊戲控制器必須符合：

- **您必須支援 Siri 遠端**-您必須一律支援 Siri 遠端。 您的遊戲無法要求第 3 方要播放的遊戲控制器。
- **您必須支援擴充的控制項配置**-所有的 tvOS 遊戲控制器為非 formfitting 擴充控制站。
- **遊戲必須具有獨立控制站的 Playable** -如果您的應用程式支援擴充的遊戲控制器，它必須單獨使用該遊戲控制器播放。
- **您必須支援播放/暫停按鈕**-期間的遊戲，如果使用者按下 [矔菛/縸] 按鈕，您應該顯示警示，讓使用者可以暫停/繼續遊戲或傳回至主功能表。

<a name="Enabling-Game-Controller-Support" />

### <a name="enabling-game-controller-support"></a>啟用遊戲控制器支援

若要啟用遊戲控制器支援 Xamarin.tvOS 應用程式中的，按兩下`Info.plist`檔案中**方案總管 中**以開啟它進行編輯：

[![](remote-bluetooth-images/game02.png "Info.plist 編輯器")](remote-bluetooth-images/game02.png#lightbox)

底下**遊戲控制器**區段中，藉由核取**啟用遊戲控制器**，然後檢查所有應用程式將支援的遊戲控制器類型。

<a name="Using-the-Siri-Remote-as-a-Game-Controller" />

### <a name="using-the-siri-remote-as-a-game-controller"></a>使用 Siri 遠端為遊戲控制器

Siri 遠端 Apple TV，可用來當做有限的遊戲控制器。 其他遊戲與控制器類似，它會出現為遊戲控制器架構`GCController`物件和支援`GCMotion`而`GCMicroGamepad`設定檔。

Siri 遠端做遊戲控制器時，具有下列特性：

- [觸控] 介面可用來當做 d-pad 提供類比的輸入的資料。
- 遠端可用在縱向或橫向方向，而您的應用程式可讓您決定是否設定檔物件應該翻轉輸入的資料自動。
- 按一下 [觸控] 介面的作用就像按下按鈕**A**遊戲控制站上。
- 播放/暫停按鈕的作用就像按鈕**X**遊戲控制站上。
- [功能表] 按鈕應該會顯示警示，讓使用者可以暫停/繼續遊戲，或返回主功能表。

<a name="Summary" />

### <a name="determining-game-controller-input"></a>判斷輸入遊戲控制器

不同於其中遊戲控制器事件可以收到觸控事件與平行的 iOS、 tvOS 處理所有低階的事件，以提供高層級`UIKit`事件。 如此一來，如果您需要的最低層級的遊戲控制器事件的存取，您必須自行關閉`UIKit`的預設行為。

在 tvOS，當您想要處理的遊戲控制器的輸入，您需要使用直接`GCEventViewController`（或子類別） 來顯示遊戲的使用者介面。 每當`GCEventViewController`已*第一個回應*，遊戲控制器輸入會擷取並將其傳遞至您的應用程式，透過 遊戲控制器架構。

您可以使用`UserInteractionEnabled`屬性`GCEventViewController`類別，以切換處理和處理事件的方式。

如需實作遊戲控制器支援資訊，請參閱 Apple 的[使用 遊戲控制器](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/WorkingwithGameControllers.html)一節中[應用程式的程式設計指南 tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/index.html)和[遊戲控制器程式設計手冊](https://developer.apple.com/library/prerelease/tvos/documentation/ServicesDiscovery/Conceptual/GameControllerPG/Introduction/Introduction.html)。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已涵蓋新的 Siri 遠端隨附於 Apple TV、 觸控式介面筆勢和 Siri 遠端按鈕。 接下來，它涵蓋處理筆勢和分鏡腳本、 手勢和程式碼與低層級的事件。 最後，如果討論使用遊戲控制器。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
