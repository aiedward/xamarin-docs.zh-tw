---
title: 在 Xamarin 中 Siri 遠端和藍牙控制器以進行 tvOS
description: 本文說明如何在以 Xamarin 撰寫的 tvOS 應用程式中使用 Siri 遠端和藍牙遊戲控制器。
ms.prod: xamarin
ms.assetid: BDB9894A-236B-424B-9032-ACD12A6C5720
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: 71a74d8f9046cd978a40b03da1921cd1fac9405f
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769185"
---
# <a name="siri-remote-and-bluetooth-controllers-for-tvos-in-xamarin"></a>在 Xamarin 中 Siri 遠端和藍牙控制器以進行 tvOS

TvOS 應用程式的使用者不會與它直接與 iOS 互動，因為它會在裝置的螢幕上使用影像，而是透過[Siri 遠端](#The-Siri-Remote)在房間間間接進行。

如果您的應用程式是遊戲，您可以選擇性地建立協力廠商的支援，也就是您應用程式中的 iOS （MFI）[藍牙遊戲控制器](#Bluetooth-Game-Controllers)。

[![](remote-bluetooth-images/intro01.png "Bluetooth 遠端和遊戲控制器")](remote-bluetooth-images/intro01.png#lightbox)

本文說明[Siri 遠端](#The-Siri-Remote)、[觸控表面手勢](#Touch-Surface-Gestures)和[Siri 遠端按鈕](#Siri-Remote-Buttons)，並示範如何透過[手勢和](#Gestures-and-Storyboards)分鏡腳本、[手勢和程式碼](#Gestures-and-Code)，以及[低層級的事件處理](#Low-Level-Event-Handling)來使用它們。 最後，它會討論如何在 tvOS 應用程式中使用[遊戲控制器](#Working-with-Game-Controllers)。

<a name="The-Siri-Remote" />

## <a name="the-siri-remote"></a>Siri 遠端

使用者將與 Apple 電視和您的 tvOS 應用程式互動的主要方式是透過內含的 Siri 遠端。 Apple 設計了遠端來橋接使用者坐在沙發上的距離，以及在電視螢幕上的房間內顯示的 Apple 電視使用者介面。

您的 tvOS 應用程式開發人員所面臨的挑戰，就是建立快速、容易使用且視覺上吸引人的使用者介面，利用 Siri 遠端的觸控表面、加速計、陀螺儀和按鈕。

[![](remote-bluetooth-images/remote01.png "Siri 遠端")](remote-bluetooth-images/remote01.png#lightbox)

在您的 tvOS 應用程式中，Siri 遠端具有下列功能和預期的使用方式：

|功能|一般應用程式使用方式|遊戲應用程式使用方式|
|---|---|---|
|**觸控介面**<br />滑動以導覽，並按下以選取並保存內容功能表。|**點擊/滑動**<br />可設定焦點的專案之間的 UI 導覽。<br /><br />**然後**<br />啟用選取的（聚焦）專案。|**點擊/滑動**<br />取決於遊戲設計，並可在邊緣上點擊以作為 D 板。<br /><br />**然後**<br />執行主要按鈕功能。|
|**Menu**<br />按以返回上一個畫面或功能表。|回到上一個畫面，並從主要應用程式畫面結束至 Apple TV 首頁畫面。|暫停並繼續遊戲，返回上一個畫面，並從主要應用程式畫面結束至 Apple TV 首頁畫面。|
|**Siri/搜尋**<br />在具有 Siri 的國家/地區中，在所有其他國家/地區中按住語音控制以顯示搜尋畫面。|N/A|N/A|
|**播放/暫停**<br />播放並暫停媒體，或在應用程式中提供次要功能。|啟動媒體播放，並暫停/繼續播放。|執行次要按鈕功能或略過簡介影片（如果有的話）。|
|**Home**<br />按以返回主畫面，按兩下以顯示執行中的應用程式，按住並按住進入睡眠裝置。|N/A|N/A|
|**磁碟區**<br />控制附加的音訊/視頻設備磁片區。|N/A|N/A|

<a name="Touch-Surface-Gestures" />

## <a name="touch-surface-gestures"></a>觸控介面手勢

Siri 遠端的觸控介面能夠偵測您可以在 tvOS 應用程式中回應的各種單一手指手勢：

|撥動|按一下|點選|
|---|---|---|
|![](remote-bluetooth-images/Gesture01.png)|![](remote-bluetooth-images/Gesture02.png)|![](remote-bluetooth-images/Gesture03.png)|
|在螢幕上的 UI 元素之間移動選取範圍（焦點）（向上、向左、右）。 輕量可以用來使用慣性快速地流覽大型的內容清單。|啟動選取的（聚焦）專案，或作用類似遊戲中的主要按鈕。 按一下和按住可以啟用內容功能表或協助工具。|輕輕地點擊邊緣上的觸控介面，其作用就像是在 D 板上使用方向按鈕，視所選的區域而定，向上、向下、向左或向右移動焦點。 視應用程式而定，可以用來顯示隱藏的控制項。|

Apple 提供下列使用觸控介面手勢的建議：

- **區分**點按和點按是使用者的刻意動作，而且很適合用於遊戲的選擇、啟用和主要按鈕。 點擊會比較微妙，而且應該謹慎使用，因為使用者通常會將 Siri 遙控器保持在最右邊，而且可能會意外地啟用攻點事件。
- **不要重新定義標準手勢**-使用者預期特定的手勢會執行特定動作，您不應該在應用程式中重新定義這些手勢的意義或功能。 其中一個例外狀況是在作用中游戲期間的遊戲應用程式。
- **謹慎定義新手勢**-同樣地，使用者會預期特定的手勢會執行特定的動作。 您應該避免定義自訂手勢來執行標準動作。 同樣地，遊戲也是最常見的例外狀況，其中的自訂手勢可以增加遊戲的樂趣、沉浸式播放。
- **如果適當的話，回應**觸控表面角落邊緣的 d-pad 點輕輕點擊，會像是遊戲控制器上的 d 板，而移動焦點或方向向上、向下、向左或向右。 如果適用的話，您應該在應用程式或遊戲中回應這些手勢。

<a name="Siri-Remote-Buttons" />

## <a name="siri-remote-buttons"></a>Siri 遠端按鈕

除了觸控介面上的手勢外，您的應用程式也可以回應使用者按一下觸控介面或按下 [播放/暫停] 按鈕。 如果您使用遊戲控制器架構來存取 Siri 遠端，您也可以偵測到按下的功能表按鈕。

此外，您可以使用筆勢辨識器搭配標準`UIKit`元素來偵測按下的功能表按鈕。 如果您攔截到按下的功能表按鈕，將會負責關閉目前的 View 和 View Controller，並返回上一個控制器。

> [!IMPORTANT]
> 您應該**一律**將函數指派給遠端的 [播放/暫停] 按鈕。 具有 [非功能性] 按鈕可讓您的應用程式看起來與終端使用者中斷。 如果您沒有此按鈕的有效函式，請指派與主要按鈕相同的功能（觸控介面按一下）。

<a name="Gestures-and-Storyboards" />

## <a name="gestures-and-storyboards"></a>手勢和分鏡腳本

在您的 tvOS 應用程式中使用 Siri 遠端的最簡單方式，就是在介面設計工具中將手勢辨識器新增至您的 views。

若要新增手勢辨識器，請執行下列動作：

1. 在 **方案總管**中，按兩下`Main.storyboard`檔案，然後開啟檔案以編輯介面設計工具。
2. 從連結**庫**拖曳點一下**手勢辨識器**，並將它放置在 View 上：

    [![](remote-bluetooth-images/storyboard01.png "攻點手勢辨識器")](remote-bluetooth-images/storyboard01.png#lightbox)
3. 勾選 [**屬性偵測器**] 的 [**按鈕**] 區段中的 [**選取**]：

    [![](remote-bluetooth-images/storyboard02.png "核取 [選取]")](remote-bluetooth-images/storyboard02.png#lightbox)
4. **選取**表示手勢會回應使用者按一下 Siri 遙控器上的**觸控介面**。 您也可以選擇回應 **功能表**， **矔菛/縸** ， **向上** ， **向下** ， **左** 和 **右** 按鈕。
5. 接下來，從點按手勢辨識**器**連線到**動作**，並`TouchSurfaceClicked`加以呼叫：

    [![](remote-bluetooth-images/storyboard03.png "來自點手勢辨識器的動作")](remote-bluetooth-images/storyboard03.png#lightbox)
6. 儲存您的變更，並返回 Visual Studio for Mac。

編輯您的 View Controller （ `FirstViewController.cs`範例）檔案，並新增下列程式碼來處理所觸發的手勢：

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

如需使用分鏡腳本的詳細資訊，請參閱我們的[Hello，tvOS 快速入門手冊](~/ios/tvos/get-started/hello-tvos.md)。 具體而言，是[建立使用者介面](~/ios/tvos/get-started/hello-tvos.md#Creating-the-User-Interface)，並[使用「輸出」和「動作](~/ios/tvos/get-started/hello-tvos.md#Writing-the-Code)」區段來撰寫程式碼。

<a name="Gestures-and-Code" />

## <a name="gestures-and-code"></a>手勢和程式碼

（選擇性）您可以直接在程式C#代碼中建立手勢，並將其新增至使用者介面中的 views。 例如，若要新增一系列的滑動手勢辨識器，請編輯您的 View Controller 並新增下列程式碼：

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

## <a name="low-level-event-handling"></a>低層級事件處理

如果您要在 tvOS 應用程式`UIKit`中建立以為基礎的自訂類型（例如`UIView`），您也可以透過`UIPress`事件提供低層級的按鈕處理功能。

事件是 tvOS iOS 的`UITouch`事件，但`UIPress`會傳回 Siri 遠端或其他連接的藍牙裝置（例如遊戲控制器）上按下按鈕的相關資訊。 `UIPress` `UIPress`事件會描述所按下的按鈕及其狀態（開始、取消、變更或結束）。

針對藍牙遊戲控制器等裝置上的類比按鈕`UIPress` ，也會傳回套用至按鈕的強制數量。 `UIPress`事件的屬性會定義哪些實體按鈕已變更狀態，而其餘屬性則描述所發生的變更。 `Type`

下列程式碼顯示處理的低層級`UIPress`事件`UIView`的範例：

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

就像`UIPress`事件一樣，如果您需要執行任何事件覆寫，就應該全部執行四個。 `UITouch`

<a name="Bluetooth-Game-Controllers" />

## <a name="bluetooth-game-controllers"></a>藍牙遊戲控制器

除了隨附于 Apple TV、協力廠商、iOS （MFI）藍牙遊戲控制器的標準 Siri 遠端之外，也可以與 Apple TV 配對，並用來控制您的 tvOS 應用程式。

[![](remote-bluetooth-images/game01.png "藍牙遊戲控制器")](remote-bluetooth-images/game01.png#lightbox)

遊戲控制器可以用來增強遊戲，並在遊戲中提供深度的意義。 它們也可以用來控制標準 Apple TV 介面，因此使用時不需要在遠端和控制器之間切換。

> [!IMPORTANT]
> Bluetooth 遊戲控制器是使用者可能會進行的選擇性購買，您的應用程式無法強制使用者購買一個。 如果您的應用程式支援遊戲控制器，則也必須支援 Siri 遠端，讓所有 Apple 電視使用者都可以使用該遊戲。

遊戲控制器在您的 tvOS 應用程式中具有下列功能和預期的使用方式：

|功能|一般應用程式使用方式|遊戲應用程式使用方式|
|---|---|---|
|**D-Pad**|流覽 UI 元素（變更焦點）。|視遊戲而定。|
|**A**|啟用選取的（聚焦）專案。|執行主要按鈕功能並確認對話方塊動作。|
|**B**|回到上一個畫面，如果在應用程式的主畫面上，則會離開主畫面。|執行次要按鈕函數或回到上一個畫面。|
|**X**|啟動媒體播放或暫停/繼續播放。|視遊戲而定。|
|**Y**|N/A|視遊戲而定。|
|**Menu**|回到上一個畫面，如果在應用程式的主畫面上，則會離開主畫面。|暫停/繼續遊戲，返回上一個畫面，如果在應用程式的主畫面上，則會離開主畫面。|
|**左肩按鈕**|向左導覽。|視遊戲而定。|
|**左方觸發程式**|向左導覽。|視遊戲而定。|
|**右肩按鈕**|向右導覽。|視遊戲而定。|
|**Right 觸發程式**|向右流覽|視遊戲而定。|
|**左方控制杆**|流覽 UI 元素（變更焦點）。|視遊戲而定。|
|**右側的操縱杆**|N/A|視遊戲而定。|

Apple 提供下列使用遊戲控制器的建議：

- **確認遊戲控制器**連線-使用者可以隨時啟動和停止您的 tvOS 應用程式。 您應該一律在應用程式啟動或喚醒時間檢查遊戲控制器是否存在，並視需要採取動作。
- **確保您的應用程式可同時在 Siri 遠端和遊戲控制器上運作**-不需要使用者在 Siri 遠端和遊戲控制器之間切換，就能使用您的應用程式。 使用這兩種類型的控制器來測試您的應用程式，以確保所有專案都容易導覽並如預期般運作。
- **提供一種方式**：按下功能表按鈕時，應該一律回到上一個畫面。 如果使用者位於主要應用程式畫面，則功能表按鈕應該會將其傳回至 Apple 電視首頁畫面。 在遊戲期間，功能表按鈕應該會顯示警示，讓使用者能夠暫停/繼續遊戲或回到主功能表。

<a name="Working-with-Game-Controllers" />

## <a name="working-with-game-controllers"></a>使用遊戲控制器

如上所述，除了 Apple TV 隨附的標準 Siri 遠端以外，使用者也可以選擇性地附加協力廠商（針對 iOS （MFI）藍牙遊戲控制器所建立），並使用它來控制您的 tvOS 應用程式。

如果您的應用程式需要低層級的控制器輸入，您可以使用 Apple 的[遊戲控制器架構](https://developer.apple.com/library/prerelease/tvos/documentation/ServicesDiscovery/Conceptual/GameControllerPG/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013276)，其具有 tvOS 的下列修改：

- 已新增微遊戲控制器配置`GCMicroGamepad`檔（）以 Siri 遠端為目標。
- 新`GCEventViewController`的類別可以用來透過應用程式路由傳送遊戲控制器事件。 如需詳細資訊，請參閱下面的[判斷遊戲控制器輸入](#determining-game-controller-input)一節。

<a name="Game-Controller-Support-Requirements" />

### <a name="game-controller-support-requirements"></a>遊戲控制器支援需求

如果您的 tvOS 應用程式支援遊戲控制器，Apple 有數個必須符合的特定需求：

- **您必須支援 Siri 遠端**-您必須一律支援 Siri 遠端。 您的遊戲不需要可播放的協力廠商遊戲控制器。
- **您必須支援擴充控制項**配置-所有 TvOS 遊戲控制器都是非 formfitting 的延伸控制器。
- **遊戲必須使用獨立控制器來播放**-如果您的應用程式支援擴充的遊戲控制器，則必須單獨與該遊戲控制器進行播放。
- **您必須支援 [播放]/[暫停] 按鈕**-在遊戲期間，如果使用者按下 [播放/暫停] 按鈕，您應該會顯示警示，讓使用者能夠暫停/繼續遊戲或回到主功能表。

<a name="Enabling-Game-Controller-Support" />

### <a name="enabling-game-controller-support"></a>啟用遊戲控制器支援

若要在您的 tvOS 應用程式中啟用遊戲控制器支援，請按兩下`Info.plist` **方案總管**中的檔案以開啟它進行編輯：

[![](remote-bluetooth-images/game02.png "Plist 編輯器")](remote-bluetooth-images/game02.png#lightbox)

在 [**遊戲控制器**] 區段底下，勾選 [**啟用遊戲控制器**]，然後檢查應用程式將支援的所有遊戲控制器類型。

<a name="Using-the-Siri-Remote-as-a-Game-Controller" />

### <a name="using-the-siri-remote-as-a-game-controller"></a>使用 Siri 遠端做為遊戲控制器

Apple TV 隨附的 Siri 遠端可用來做為有限的遊戲控制器。 就像其他遊戲控制器一樣，它會以`GCController`物件的形式顯示在遊戲控制器架構中，並`GCMotion`同時`GCMicroGamepad`支援和設定檔。

使用 Siri 遠端做為遊戲控制器時，會具有下列特性：

- 觸控介面可用來做為提供類比輸入資料的 D 板。
- 遠端可用於直向或橫向方向，而您的應用程式會決定設定檔物件是否應該自動翻轉輸入資料。
- 按一下觸控介面的作用就像是在遊戲控制器上按下按鈕**A** 。
- [播放]/[暫停] 按鈕的作用就像是遊戲控制器上的按鈕**X** 。
- 功能表按鈕應該會顯示警示，讓使用者能夠暫停/繼續遊戲或回到主功能表。

<a name="Summary" />

### <a name="determining-game-controller-input"></a>判斷遊戲控制器輸入

與使用觸控事件同時接收遊戲控制器事件的 iOS 不同，tvOS 會處理所有的低層級事件，以提供高階`UIKit`事件。 因此，如果您需要存取低層級的遊戲控制器事件，則`UIKit`必須關閉的預設行為。

在 tvOS 上，當您想要直接處理遊戲控制器輸入時，您必須`GCEventViewController`使用（或子類別）來顯示遊戲的使用者介面。 當*第一次回應*時，遊戲控制器輸入會透過遊戲控制器架構來捕捉並傳遞至您的應用程式。`GCEventViewController`

您可以使用`UserInteractionEnabled` `GCEventViewController`類別的屬性來切換事件的處理和處理方式。

如需有關實施遊戲控制器支援的詳細資訊，請參閱 tvOS 和[遊戲控制器](https://developer.apple.com/library/prerelease/tvos/documentation/ServicesDiscovery/Conceptual/GameControllerPG/Introduction/Introduction.html)程式設計[指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/index.html)中的 Apple[與遊戲控制器合作](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/WorkingwithGameControllers.html)一節。

<a name="Summary" />

## <a name="summary"></a>總結

本文涵蓋了 Apple TV、觸控表面手勢和 Siri 遠端按鈕隨附的新 Siri 遠端。 接下來，它涵蓋了使用筆勢和分鏡腳本、手勢和程式碼，以及低層級事件。 最後，如果討論的是使用遊戲控制器。

## <a name="related-links"></a>相關連結

- [tvOS 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人力介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
