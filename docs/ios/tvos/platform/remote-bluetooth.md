---
title: 在 Xamarin 中 Siri 適用于 tvOS 的遠端和藍牙控制器
description: 本文說明如何在以 Xamarin 撰寫的 tvOS apps 中使用 Siri 遠端和藍牙遊戲控制器。
ms.prod: xamarin
ms.assetid: BDB9894A-236B-424B-9032-ACD12A6C5720
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 1c1a51fe5999ac5556f2d27e98603b966fdd7f56
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91437079"
---
# <a name="siri-remote-and-bluetooth-controllers-for-tvos-in-xamarin"></a>在 Xamarin 中 Siri 適用于 tvOS 的遠端和藍牙控制器

您的 tvOS 應用程式的使用者將不會像 iOS 一樣直接與其介面互動，因為它們會在裝置的螢幕上點出影像，但會使用 [Siri 遠端](#The-Siri-Remote)在房間間間接地進行。

如果您的應用程式是一項遊戲，您可以選擇性地為您的應用程式中的 iOS (MFI) [藍牙遊戲控制器](#Bluetooth-Game-Controllers) 建立協力廠商支援。

[![藍牙遠端和遊戲控制器](remote-bluetooth-images/intro01.png)](remote-bluetooth-images/intro01.png#lightbox)

本文說明 [Siri 遠端](#The-Siri-Remote)、 [觸控介面手勢](#Touch-Surface-Gestures) 和 [Siri 遙控器按鈕](#Siri-Remote-Buttons) ，並示範如何透過 [手勢和](#Gestures-and-Storyboards)分鏡腳本、 [手勢和程式碼](#Gestures-and-Code) ，以及 [低層級事件處理](#Low-Level-Event-Handling)來使用它們。 最後，它會討論如何使用 tvOS 應用程式中的 [遊戲控制器](#Working-with-Game-Controllers) 。

<a name="The-Siri-Remote"></a>

## <a name="the-siri-remote"></a>Siri 遠端

使用者將與 Apple TV 和您的 tvOS 應用程式互動的主要方式是透過內含的 Siri 遠端。 Apple 設計了遠端來橋接坐在沙發上的使用者與在電視螢幕上的房間內顯示的 Apple TV 使用者介面之間的距離。

您的 tvOS 應用程式開發人員所面臨的挑戰，就是建立快速、便於使用且視覺效果吸引人的使用者介面，利用 Siri 遠端的觸控介面、加速計、陀螺儀和按鈕。

[![Siri 遠端](remote-bluetooth-images/remote01.png)](remote-bluetooth-images/remote01.png#lightbox)

Siri 遠端在您的 tvOS 應用程式中具有下列功能和預期的用法：

|特徵|一般應用程式使用方式|遊戲應用程式使用方式|
|---|---|---|
|**觸控式介面**<br />切換至 [流覽]，按下以選取並按住內容功能表。|**點一下/輕觸**<br />可設定焦點專案之間的 UI 導覽。<br /><br />**按一下**<br />啟用選取的 (焦點) 專案。|**點一下/輕觸**<br />取決於遊戲設計，並可透過在邊緣上用來作為 D 板。<br /><br />**按一下**<br />執行主要按鈕功能。|
|**功能表**<br />按以返回上一個畫面或功能表。|回到上一個畫面，並從主應用程式畫面結束至 Apple TV 首頁畫面。|暫停和繼續遊戲，回到上一個畫面，並從主應用程式畫面結束至 Apple TV 首頁畫面。|
|**Siri/搜尋**<br />在具有 Siri 的國家/地區中，按下並按住語音控制，在所有其他國家/地區中都會顯示搜尋畫面。|n/a|n/a|
|**播放/暫停**<br />播放和暫停媒體，或在應用程式中提供協助工具。|開始播放媒體播放和暫停/繼續播放。|執行次要按鈕函式或略過簡介影片 (是否存在) 。|
|**首頁**<br />按下以返回主畫面、按兩下以顯示執行中的應用程式，然後按住以睡眠裝置。|n/a|n/a|
|**磁碟區**<br />控制附加的音訊/視頻設備數量。|n/a|n/a|

<a name="Touch-Surface-Gestures"></a>

## <a name="touch-surface-gestures"></a>觸控式介面手勢

Siri 遠端的觸控介面可以偵測您可在 tvOS 應用程式中回應的各種單一手指手勢：

|撥動|按一下|點選|
|---|---|---|
|![移動選取範圍](remote-bluetooth-images/Gesture01.png)|![在職員工選取的專案](remote-bluetooth-images/Gesture02.png)|![方向按鈕](remote-bluetooth-images/Gesture03.png)|
|在螢幕上的 UI 元素之間移動選取範圍 (焦點)  (向上、向下、向左、右) 。 您可以使用 [輕量]，透過慣性快速地滾動大量的內容清單。|啟動所選的 (焦點) 專案，或像遊戲中的主要按鈕一樣運作。 按一下和按住可以啟用內容功能表或次要函數。|在邊緣上輕量觸控介面的方式，就像是在 D 板上的方向按鈕一樣，會根據點擊的區域，將焦點向上、向下、向左或向右移動。 視應用程式而定，可以用來顯示隱藏的控制項。|

Apple 提供下列建議來使用觸控介面手勢：

- **區分點擊和** 點按動作是由使用者刻意採取的動作，而且非常適合用來選取、啟用和遊戲的主要按鈕。 由於使用者經常保有 Siri 的遙控器，而且可能會不小心地輕易地啟用點按事件，因此應該謹慎使用。
- **請勿重新定義標準手勢** -使用者預期特定的手勢會執行特定的動作，您不應在應用程式中重新定義這些筆勢的意義或功能。 唯一的例外是在主動遊戲期間的遊戲應用程式。
- 更**謹慎地定義新的手勢**，使用者預期特定的手勢會執行特定的動作。 您應該避免定義自訂手勢來執行標準動作。 同樣地，遊戲也是最常見的例外狀況，也就是自訂手勢可以為遊戲增添有趣、沉浸式玩遊戲。
- **如果有需要，回應以 d** 鍵的方式按一下觸控介面的邊角邊緣，會像是遊戲控制器上的 d 板，將焦點或方向向上、向下、向左或向右移動。 如果適用，您應該在應用程式或遊戲中回應這些手勢。

<a name="Siri-Remote-Buttons"></a>

## <a name="siri-remote-buttons"></a>Siri 遠端按鈕

除了觸控介面上的手勢之外，您的應用程式也可以回應使用者按一下觸控介面或按下 [播放/暫停] 按鈕。 如果您是使用遊戲控制器架構來存取 Siri 遠端，也可以偵測到按下的功能表按鈕。

此外，您也可以使用具有標準元素的手勢辨識器來偵測功能表按鈕按下 `UIKit` 。 如果您攔截到按下的功能表按鈕，就必須負責關閉目前的 View 和 View 控制器，然後再回到前一個。

> [!IMPORTANT]
> 您應該 **一律** 將函式指派給遠端的播放/暫停按鈕。 擁有 [非功能性] 按鈕可以讓您的應用程式看起來與終端使用者中斷。 如果這個按鈕沒有有效的函式，請將相同的函式指派為主要按鈕 (觸控式介面，請按一下 [) ]。

<a name="Gestures-and-Storyboards"></a>

## <a name="gestures-and-storyboards"></a>手勢和分鏡腳本

在 tvOS 應用程式中使用 Siri Remote 最簡單的方式，就是在介面設計工具中將手勢辨識器新增至您的視圖。

若要加入手勢辨識器，請執行下列動作：

1. 在 [ **方案總管**中，按兩下該檔案 `Main.storyboard` ，然後開啟該檔案以編輯介面設計工具。
2. 從連結**庫**拖曳點一下**手勢辨識器**，並將它放在視圖上：

    [![點一下手勢辨識器](remote-bluetooth-images/storyboard01.png)](remote-bluetooth-images/storyboard01.png#lightbox)
3. 核取 [**屬性偵測器**] 的 [**按鈕**] 區段中的 [**選取**]：

    [![選取](remote-bluetooth-images/storyboard02.png)](remote-bluetooth-images/storyboard02.png#lightbox)
4. **Select** 表示手勢會回應使用者按一下 Siri 遠端上的 **觸控介面** 。 您也可以選擇回應 **功能表**、 **播放/暫停**、 **向上**、 **向下**、向 **左** 和 **向右** 按鈕。
5. 接下來，從 [點一下**手勢辨識器**] 連接**動作**並加以呼叫 `TouchSurfaceClicked` ：

    [![點擊手勢辨識器的動作](remote-bluetooth-images/storyboard03.png)](remote-bluetooth-images/storyboard03.png#lightbox)
6. 儲存您的變更並返回 Visual Studio for Mac。

編輯您的視圖控制器 (範例 `FirstViewController.cs`) 檔案，並新增下列程式碼來處理所觸發的手勢：

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

如需使用分鏡腳本的詳細資訊，請參閱我們的 [Hello，tvOS 快速入門手冊](~/ios/tvos/get-started/hello-tvos.md)。 尤其是 [建立消費者介面](~/ios/tvos/get-started/hello-tvos.md#Creating-the-User-Interface) ，並 [使用 [輸出] 和 [動作](~/ios/tvos/get-started/hello-tvos.md#Writing-the-Code) ] 區段來撰寫程式碼。

<a name="Gestures-and-Code"></a>

## <a name="gestures-and-code"></a>手勢和程式碼

（選擇性）您可以直接在 c # 程式碼中建立手勢，並將它們新增至消費者介面中的 views。 例如，若要新增一系列的滑動手勢辨識器，請編輯您的視圖控制器並加入下列程式碼：

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

<a name="Low-Level-Event-Handling"></a>

## <a name="low-level-event-handling"></a>低層級事件處理

如果您要在 tvOS 應用程式中建立以應用程式為基礎的自訂型別 `UIKit` (例如 `UIView`) ，您也可以透過事件提供按鈕按下低層級的處理功能 `UIPress` 。

`UIPress`事件是 TvOS `UITouch` iOS 的事件，但會傳回 `UIPress` Siri 遠端或其他連接藍牙裝置上按鈕按下的相關資訊 (例如遊戲控制器) 。 `UIPress` 事件會描述所按下的按鈕，以及其狀態 (開始、取消、變更或結束) 。

針對藍牙遊戲控制器等裝置上的類比按鈕，也會傳回套用 `UIPress` 至按鈕的強制數量。 `Type`事件的屬性會 `UIPress` 定義哪一個實體按鈕已變更狀態，而其餘的屬性則描述所發生的變更。

下列程式碼顯示處理的低層級事件範例 `UIPress` `UIView` ：

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

如同 `UITouch` 事件，如果您需要執行任何 `UIPress` 事件覆寫，則應該全部執行四個。

<a name="Bluetooth-Game-Controllers"></a>

## <a name="bluetooth-game-controllers"></a>藍牙遊戲控制器

除了 Apple TV 隨附的標準 Siri 遠端之外，協力廠商是針對 iOS (MFI) 藍牙遊戲控制器可以與 Apple 電視配對，並用來控制您的 tvOS 應用程式。

[![藍牙遊戲控制器](remote-bluetooth-images/game01.png)](remote-bluetooth-images/game01.png#lightbox)

遊戲控制器可以用來增強遊戲，並在遊戲中提供深度的意義。 它們也可以用來控制標準 Apple TV 介面，如此就不需要在遠端和控制器之間切換。

> [!IMPORTANT]
> 藍牙遊戲控制器是終端使用者可能進行的選擇性購買，您的應用程式無法強制使用者購買。 如果您的應用程式支援遊戲控制器，則它也必須支援 Siri 遠端，讓所有 Apple TV 使用者都能使用該遊戲。

遊戲控制器在您的 tvOS 應用程式中具有下列功能和預期的用法：

|特徵|一般應用程式使用方式|遊戲應用程式使用方式|
|---|---|---|
|**D-Pad**|流覽 UI 元素 (變更焦點) 。|取決於遊戲。|
|**A**|啟用選取的 (焦點) 專案。|執行主要按鈕功能，並確認對話方塊動作。|
|**B**|回到上一個畫面，如果在應用程式的主畫面上，則會離開首頁畫面。|執行次要按鈕函式或回到上一個畫面。|
|**X**|開始播放媒體播放或暫停/繼續播放。|取決於遊戲。|
|**Y**|n/a|取決於遊戲。|
|**功能表**|回到上一個畫面，如果在應用程式的主畫面上，則會離開首頁畫面。|暫停/繼續遊戲，回到上一個畫面，如果在應用程式的主畫面上，則會離開首頁畫面。|
|**左邊的肩按鈕**|向左導覽。|取決於遊戲。|
|**左方觸發程式**|向左導覽。|取決於遊戲。|
|**適當的肩按鈕**|向右導覽。|取決於遊戲。|
|**右觸發程式**|向右導覽|取決於遊戲。|
|**左方操縱杆**|流覽 UI 元素 (變更焦點) 。|取決於遊戲。|
|**右側的操縱杆**|n/a|取決於遊戲。|

Apple 針對使用遊戲控制器提供下列建議：

- **確認遊戲控制器** 連線-終端使用者隨時都可以啟動和停止您的 tvOS 應用程式。 您應一律在應用程式啟動或喚醒時間檢查遊戲控制器是否存在，並視需要採取動作。
- **確定您的應用程式同時適用于 Siri 遠端和遊戲控制器** -不需要使用者在 Siri 遠端與遊戲控制器之間切換，就能使用您的應用程式。 使用這兩種控制器來測試您的應用程式，以確保所有專案都能輕鬆地進行流覽並如預期般運作。
- **提供反向** 按下功能表按鈕應該一律返回上一個畫面的方法。 如果使用者是在主要應用程式畫面上，功能表按鈕應該會將其傳回至 Apple TV 首頁畫面。 在遊戲期間，功能表按鈕應該會顯示警示，讓使用者能夠暫停/繼續遊戲或返回主功能表。

<a name="Working-with-Game-Controllers"></a>

## <a name="working-with-game-controllers"></a>使用遊戲控制器

如上所述，除了 Apple TV 隨附的標準 Siri 遠端之外，使用者也可以選擇性地附加協力廠商，針對 iOS (MFI) 藍牙遊戲控制器，並使用它來控制您的 tvOS 應用程式。

如果您的應用程式需要低層級的控制器輸入，您可以使用 Apple 的 [遊戲控制器架構](https://developer.apple.com/library/prerelease/tvos/documentation/ServicesDiscovery/Conceptual/GameControllerPG/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013276) ，它對 tvOS 有下列修改：

- 已 `GCMicroGamepad` 新增以 Siri 遠端為目標的微型遊戲控制器設定檔 () 。
- 新 `GCEventViewController` 類別可以用來透過您的應用程式路由傳送遊戲控制器事件。 如需詳細資訊，請參閱下面的「 [判斷遊戲控制器輸入](#determining-game-controller-input) 」一節。

<a name="Game-Controller-Support-Requirements"></a>

### <a name="game-controller-support-requirements"></a>遊戲控制器支援需求

如果您的 tvOS 應用程式支援遊戲控制器，Apple 有幾個特定需求必須符合：

- **您必須支援 Siri 遠端** -您必須一律支援 Siri 遠端。 您的遊戲無法播放協力廠商遊戲控制器。
- **您必須支援擴充控制項** 配置-所有 TvOS 遊戲控制器都是非 formfitting、延伸控制器。
- **遊戲必須可透過獨立控制器來播放** -如果您的應用程式支援擴充遊戲控制器，則必須與該遊戲控制器單獨播放。
- **您必須支援 [播放/暫停] 按鈕** -在遊戲期間，如果使用者按下 [播放/暫停] 按鈕，您應該會顯示警示，讓使用者能夠暫停/繼續遊戲或返回主功能表。

<a name="Enabling-Game-Controller-Support"></a>

### <a name="enabling-game-controller-support"></a>啟用遊戲控制器支援

若要在您的 tvOS 應用程式中啟用遊戲控制器支援，請按兩下 `Info.plist` **方案總管** 中的檔案以開啟它進行編輯：

[![Plist 編輯器](remote-bluetooth-images/game02.png)](remote-bluetooth-images/game02.png#lightbox)

在 [ **遊戲控制器** ] 區段下，勾選 [ **啟用遊戲控制器**]，然後檢查應用程式將支援的所有遊戲控制器類型。

<a name="Using-the-Siri-Remote-as-a-Game-Controller"></a>

### <a name="using-the-siri-remote-as-a-game-controller"></a>使用 Siri Remote 作為遊戲控制器

Apple TV 隨附的 Siri 遠端可以用來作為受限的遊戲控制器。 就像其他遊戲控制器一樣，它會以物件的形式出現在遊戲控制器架構中， `GCController` 並同時支援 `GCMotion` 和 `GCMicroGamepad` 設定檔。

使用 Siri 遠端做為遊戲控制器時，會有下列特性：

- 觸控介面可用來做為可提供類比輸入資料的 D 板。
- 遠端可用於直向或橫向方向，而您的應用程式會決定設定檔物件是否應該自動翻轉輸入資料。
- 按一下觸控介面，就像在遊戲控制器上按下按鈕 **A** 一樣。
- [播放/暫停] 按鈕的作用就像是遊戲控制器上的按鈕 **X** 。
- [功能表] 按鈕應該會顯示警示，讓使用者能夠暫停/繼續遊戲或返回主功能表。

<a name="Summary"></a>

### <a name="determining-game-controller-input"></a>判斷遊戲控制器輸入

不同于 iOS，遊戲控制器事件可以與觸控事件平行接收，tvOS 會處理所有的低層級事件以傳遞高階 `UIKit` 事件。 如此一來，如果您需要存取低層級的遊戲控制器事件，則必須關閉 `UIKit` 的預設行為。

在 tvOS 上，當您想要直接處理遊戲控制器輸入時，您需要使用 `GCEventViewController` (或子類別) 來顯示遊戲的消費者介面。 只要 `GCEventViewController` 是 *第一個回應*者，遊戲控制器的輸入就會透過遊戲控制器架構來捕捉並傳遞至您的應用程式。

您可以使用 `UserInteractionEnabled` 類別的屬性 `GCEventViewController` 來切換事件的處理和處理方式。

如需執行遊戲控制器支援的相關資訊，請參閱《 tvOS and[遊戲控制器](https://developer.apple.com/library/prerelease/tvos/documentation/ServicesDiscovery/Conceptual/GameControllerPG/Introduction/Introduction.html)[程式](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/index.html)設計指南》中的 Apple[使用遊戲控制器](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/WorkingwithGameControllers.html)一節。

<a name="Summary"></a>

## <a name="summary"></a>摘要

本文涵蓋了 Apple TV、觸控介面手勢和 Siri 遠端按鈕隨附的新 Siri 遠端。 接下來，它涵蓋了筆勢和分鏡腳本、手勢和程式碼，以及低層級的事件。 最後，如果討論過使用遊戲控制器。

## <a name="related-links"></a>相關連結

- [tvOS 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人體介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [適用于 tvOS 的應用程式程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)