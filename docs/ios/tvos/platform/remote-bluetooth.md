---
title: "Siri 遠端和藍芽控制站"
description: "本文涵蓋 Xamarin.tvOS 應用程式中支援的新 Siri 遠端和藍芽遊戲控制器。"
ms.topic: article
ms.prod: xamarin
ms.assetid: A2DA4347-0563-4C72-A8D7-5B9DE9E28712
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 5d74479e995c5c6ba6f6fd9fd23fbca78718ee31
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="siri-remote-and-bluetooth-controllers"></a>Siri 遠端和藍芽控制站

_本文涵蓋 Xamarin.tvOS 應用程式中支援的新 Siri 遠端和藍芽遊戲控制器。_


Xamarin.tvOS 應用程式的使用者不會互動的介面直接做為使用 iOS 其中他們點選裝置螢幕上的映像但間接從跨空間使用[Siri 遠端](#The-Siri-Remote)。

如果您的應用程式的遊戲，您可以選擇性地建置中的第 3 個合作對象，做為支援 iOS (MFI)[藍芽遊戲控制器](#Bluetooth-Game-Controllers)您應用程式中。

[ ![](remote-bluetooth-images/intro01.png "藍芽遠端及遊戲控制器")](remote-bluetooth-images/intro01.png)

本文說明[Siri 遠端](#The-Siri-Remote)，[觸控介面筆勢](#Touch-Surface-Gestures)和[Siri 遠端按鈕](#Siri-Remote-Buttons)並示範如何使用它們透過[筆勢和分鏡腳本](#Gestures-and-Storyboards)，[筆勢和程式碼](#Gestures-and-Code)和[低階事件處理](#Low-Level-Event-Handling)。 最後，它討論[使用遊戲控制器](#Working-with-Game-Controllers)Xamarin.tvOS 應用程式中。

<a name="The-Siri-Remote" />

## <a name="the-siri-remote"></a>Siri 遠端

將會與 Apple TV 和應用程式 Xamarin.tvOS，互動使用者的主要方式是透過包含 Siri 遠端。 Apple 設計來橋接坐在沙發上和 Apple 電視遙控器使用者介面在不同房間電視螢幕上顯示的使用者之間的距離遠端。

您身為 tvOS 應用程式開發人員的挑戰是建立快速、 簡單易用，而且美觀使用者介面，會利用 Siri 遠端觸控介面、 加速計、 迴轉儀和按鈕。

[ ![](remote-bluetooth-images/remote01.png "Siri 遠端")](remote-bluetooth-images/remote01.png)

Siri 遠端具有下列功能和 tvOS 應用程式內的預期使用方式：

<table width="100%" border="1px">
<tr>
    <td><b>功能</b></td>
    <td><b>一般應用程式使用量</b></td>
    <td><b>遊戲的應用程式使用量</b></td>
</tr>
<tr>
    <td valign="top"><b>觸控介面</b><br/>若要瀏覽，請按下以選取按住關聯式功能表撥動。</td>
    <td valign="top"><b>點選/撥動：</b><br/>UI 可設定焦點的項目之間的導覽。<br/><br/><b>按一下：</b><br/>啟動選取的 （焦點） 項目。</td>
    <td valign="top"><b>點選/撥動：</b><br/>遊戲的設計而定，而且可以當做 D-pad 依據點選邊緣上。<br/><br/><b>按一下：</b><br/>執行主要按鈕的功能。</td>
</tr>
<tr>
    <td valign="top"><b>Menu</b><br/>按下即可返回上一個畫面或功能表。</td>
    <td valign="top">返回上一個畫面，並會跳到首頁 Apple TV 螢幕，從主應用程式畫面。</td>
    <td valign="top">暫停和繼續遊戲，傳回上一個螢幕，結束首頁 Apple TV 螢幕從主應用程式畫面。</td>
</tr>
<tr>
    <td valign="top"><b>Siri/Search</b><br/>使用 Siri 國家/地區，請按住中所有其他國家/地區，顯示搜尋螢幕的語音控制。</td>
    <td valign="top">N/A</td>
    <td valign="top">N/A</td>
</tr>
<tr>
    <td valign="top"><b>矔菛/縸懫</b><br/>播放和暫停媒體或提供應用程式中的第二個函式。</td>
    <td valign="top">開始播放媒體和暫停/繼續播放。</td>
    <td valign="top">執行次要按鈕函式，或略過簡介影片 (如果存在)。</td>
</tr>
<tr>
    <td valign="top"><b>Home</b><br/>按下返回 [首頁] 螢幕，連按兩下以顯示執行中應用程式，請按住進入睡眠狀態的裝置。</td>
    <td valign="top">N/A</td>
    <td valign="top">N/A</td>
</tr>
<tr>
    <td valign="top"><b>磁碟區</b><br/>控制項附加音訊/視訊設備磁碟區。</td>
    <td valign="top">N/A</td>
    <td valign="top">N/A</td>
</tr>
</table>

<a name="Touch-Surface-Gestures" />

## <a name="touch-surface-gestures"></a>觸控筆勢介面

Siri 遠端觸控介面是能夠偵測到各種不同的單指筆勢，您可以回應 Xamarin.tvOS 應用程式中：

<table width="100%">
<tr>
    <td valign="top" width="30%"><img src="remote-bluetooth-images/Gesture01.png"></td>
    <td valign="top" width="30%"><img src="remote-bluetooth-images/Gesture02.png"></td>
    <td valign="top" width="30%"><img src="remote-bluetooth-images/Gesture03.png"></td>
</tr>
<tr>
    <td valign="top"><b>撥動：</b><br/>在畫面上的 UI 項目之間移動選取項目 （焦點） （上、 下左、 右）。 撥動可以用來捲動的內容使用快速慣性大型清單。</td>
    <td valign="top"><b>按一下：</b><br/>啟動選取的項目 （焦點），或像是在遊戲的主要按鈕。 按一下並按住，可以啟用關聯式功能表或次要函式。</td>
    <td valign="top"><b>點選：</b><br/>輕量點選 觸控介面的邊緣上就像是方向鍵，將焦點移上、 下、 左或右依據點選的區域上的方向按鈕。 根據應用程式時，可以用來顯示隱藏的控制項。</td>
</tr>
</table>

Apple 提供使用觸控介面筆勢的下列建議：

* **區分按一下和點選**-按一下是刻意的動作，使用者並非常適用於選取項目、 啟動和在遊戲的主要按鈕。 點選是更難捉摸，因為使用者通常在他們的手持有 Siri 遠端，而且不小心可以輕鬆地啟用點選事件應該謹慎使用。
* **不重新定義標準的筆勢**位使用者擁有特定的動作將會執行特定動作，預期的情況下您不應該重新函式的這些筆勢或意義定義應用程式中。 唯一例外的是期間作用中的遊戲的遊戲應用程式。
* **定義新筆勢盡量少用**-同樣地，使用者有特定的動作將會執行特定動作的預期。 您應避免定義自訂的筆勢來執行一般動作。 同樣地，遊戲會是最一般的例外狀況可以在其中自訂軌跡加入樂趣，擬真播放遊戲。
* **如果可行，回應 D-pad 點選**-少量觸控介面的邊緣會反應像 D-pad 將焦點移遊戲控制器或方向下、, 左或向右角點選。 如果可行，您應該回應這些應用程式或遊戲中的筆勢。

<a name="Siri-Remote-Buttons" />

## <a name="siri-remote-buttons"></a>Siri 遙控器按鈕

除了觸控介面上的手勢，使用者按一下 [觸控] 介面，或按下矔菛/縸懫按鈕可以回應您的應用程式。 如果您要存取 Siri 遠端使用遊戲控制器架構，也可以偵測到所按下功能表按鈕。 

此外，按下功能表按鈕可以偵測到使用標準的筆勢辨識器`UIKit`項目。 攔截按下功能表按鈕時，如果您將會負責關閉目前的檢視和檢視控制器，並返回前一個。

> [!IMPORTANT]
> **注意：**您應該**一律**函式指派給遙控器上的 [矔菛/縸懫] 按鈕。 具有非功能性的按鈕可以進行中斷，以使用者尋找應用程式。 如果您沒有有效的函式，此按鈕，將指派 （觸控介面按一下） 主要按鈕相同的功能。




<a name="Gestures-and-Storyboards" />

## <a name="gestures-and-storyboards"></a>筆勢和分鏡腳本

若要使用 Siri 遠端 Xamarin.tvOS 應用程式中最簡單的方式是加入介面的設計工具中檢視的筆勢辨識器。

若要加入的筆勢辨識器，執行下列作業：

1. 在**方案總管 中**，連按兩下`Main.storyboard`檔案，並開啟它進行編輯介面設計工具。
2. 拖曳**點選 筆勢辨識器**從**文件庫**並將它放在檢視上： 

    [ ![](remote-bluetooth-images/storyboard01.png "點選 筆勢辨識器")](remote-bluetooth-images/storyboard01.png)
3. 請檢查**選取**中**按鈕**區段**屬性偵測器**: 

    [ ![](remote-bluetooth-images/storyboard02.png "檢查選取")](remote-bluetooth-images/storyboard02.png)
4. **選取**表示軌跡會回應使用者按一下**觸控介面**Siri 遙控器上。 您也可以選擇回應**功能表**，**矔菛/縸懫**，**向上**，**向**，**左**和**右邊**按鈕。
5. 接下來，呼叫**動作**從**點選 筆勢辨識器**並呼叫它`TouchSurfaceClicked`: 

    [ ![](remote-bluetooth-images/storyboard03.png "點選 筆勢辨識器中的動作")](remote-bluetooth-images/storyboard03.png)
6. 儲存變更並返回 Visual Studio for mac。

編輯檢視控制器 (範例`FirstViewController.cs`) 檔案，然後加入下列程式碼來處理觸發的動作：

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

如需使用分鏡腳本的詳細資訊，請參閱我們[Hello，tvOS 快速入門指南](~/ios/tvos/get-started/hello-tvos.md)。 特別是[建立使用者介面](~/ios/tvos/get-started/hello-tvos.md#Creating-the-User-Interface)和[撰寫的程式碼使用插座和動作](~/ios/tvos/get-started/hello-tvos.md#Writing-the-Code)區段。

<a name="Gestures-and-Code" />

## <a name="gestures-and-code"></a>筆勢和程式碼

（選擇性） 您可以直接在 C# 程式碼中建立手勢，並將它們加入至檢視中，您的使用者介面中。 例如，若要加入一系列的撥動筆勢辨識器，編輯檢視控制器並加入下列程式碼：

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

如果您要建立自訂型別根據`UIKit`Xamarin.tvOS 應用程式中 (例如`UIView`)，您也可以提供透過按下按鈕的低層級的處理能力`UIPress`事件。 

A`UIPress`事件是要 tvOS 什麼`UITouch`事件是 iOS，除了`UIPress`傳回按 Siri 遙控器上的按鈕的相關資訊，或其他連結藍芽裝置 （例如遊戲控制器）。 `UIPress` 事件描述按下按鈕和其狀態 （Began、 已取消、 變更或結束）。 

等藍芽遊戲控制器，裝置上的類比按鈕`UIPress`也會傳回強制套用至按鈕的數量。 `Type`屬性`UIPress`事件定義哪一個實體的按鈕有已變更的狀態，請在其餘的屬性時說明發生的變更。

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

除了標準 Siri 遠端隨附 Apple TV，第 3 個合作對象，做為 iOS (MFI) 藍芽遊戲控制器可以搭配 Apple TV 和用來控制 Xamarin.tvOS 應用程式。

[ ![](remote-bluetooth-images/game01.png "藍芽遊戲控制器")](remote-bluetooth-images/game01.png)

遊戲控制器可以用來強化遊戲及提供了解在遊戲繪圖。 它們也可以用來控制標準 Apple TV 介面，讓使用不具有遠端與控制器之間切換。

> [!IMPORTANT]
> **注意：**藍芽遊戲控制器可能會讓使用者選擇性購買，您的應用程式不會強制要求使用者購買。 如果您的應用程式支援遊戲控制器就必須也支援使用 Siri 遠端使遊戲能由 Apple TV 的所有使用者。


遊戲控制器具有下列功能和 tvOS 應用程式內的預期使用方式：
<table width="100%" border="1px">
<tr>
    <td><b>功能</b></td>
    <td><b>一般應用程式使用量</b></td>
    <td><b>遊戲的應用程式使用量</b></td>
</tr>
<tr>
    <td valign="top"><b>D-Pad</b></td>
    <td valign="top">導覽程式 UI 項目 （將焦點變更）。</td>
    <td valign="top">取決於遊戲。</td>
</tr>
<tr>
    <td valign="top"><b>A</b></td>
    <td valign="top">啟動選取的項目 （焦點）。</td>
    <td valign="top">執行主要按鈕的功能，並確認對話方塊動作。</td>
</tr>
<tr>
    <td valign="top"><b>B</b></td>
    <td valign="top">傳回上一個螢幕，或如果應用程式的主畫面上會跳到 [首頁] 螢幕。</td>
    <td valign="top">執行次要按鈕的功能，或傳回上一個螢幕。</td>
</tr>
<tr>
    <td valign="top"><b>X</b></td>
    <td valign="top">啟動媒體的播放或暫停/繼續播放。</td>
    <td valign="top">取決於遊戲。</td>
</tr>
<tr>
    <td valign="top"><b>Y</b></td>
    <td valign="top">N/A</td>
    <td valign="top">取決於遊戲。</td>
</tr>
<tr>
    <td valign="top"><b>Menu</b></td>
    <td valign="top">傳回上一個螢幕，或如果應用程式的主畫面上會跳到 [首頁] 螢幕。</td>
    <td valign="top">暫停/繼續應用程式的主畫面上如果遊戲，便會回到上一個畫面或 [首頁] 畫面的結束。</td>
</tr>
<tr>
    <td valign="top"><b>左的 Shoulder 按鈕</b></td>
    <td valign="top">瀏覽左邊。</td>
    <td valign="top">取決於遊戲。</td>
</tr>
<tr>
    <td valign="top"><b>左邊的觸發程序</b></td>
    <td valign="top">瀏覽左邊。</td>
    <td valign="top">取決於遊戲。</td>
</tr>
<tr>
    <td valign="top"><b>右 Shoulder 按鈕</b></td>
    <td valign="top">瀏覽權限。</td>
    <td valign="top">取決於遊戲。</td>
</tr>
<tr>
    <td valign="top"><b>正確的觸發程序</b></td>
    <td valign="top">瀏覽權限</td>
    <td valign="top">取決於遊戲。</td>
</tr>
<tr>
    <td valign="top"><b>左搖桿</b></td>
    <td valign="top">導覽程式 UI 項目 （將焦點變更）。</td>
    <td valign="top">取決於遊戲。</td>
</tr>
<tr>
    <td valign="top"><b>右搖桿</b></td>
    <td valign="top">N/A</td>
    <td valign="top">取決於遊戲。</td>
</tr>
</table>

Apple 提供遊戲控制器使用下列的建議：

- **確認 遊戲控制器連線**-tvOS 應用程式可以啟動和停止在任何時間由終端使用者。 您一定要檢查的遊戲控制器在啟動應用程式或醒著的時間存在並視需要採取動作。
- **請確定您的應用程式運作上使用 Siri 遠端及遊戲控制器**-不需要使用者使用 Siri 遠端及遊戲控制器使用您的應用程式之間切換。 測試您的應用程式通常搭配這兩種類型的控制站，確保所有項目可以輕鬆地瀏覽，並如預期般運作。
- **提供方式回**-按下功能表按鈕應該會一律傳回上一個螢幕。 如果使用者是在主應用程式畫面上，功能表按鈕應該傳回它們以 Apple TV 主畫面。 遊戲設定期間的功能表按鈕應該會顯示警示，讓使用者暫停/繼續遊戲或返回主功能表的能力。

<a name="Working-with-Game-Controllers" />

## <a name="working-with-game-controllers"></a>使用 遊戲控制器

如同前面所述，除了標準 Siri 遠端隨附使用 Apple TV，使用者可以選擇性地附加 3rd 合作對象，做為 iOS (MFI) 藍芽遊戲控制器，並使用它來控制 Xamarin.tvOS 應用程式。

如果您的應用程式需要低層級的控制站的輸入，您可以使用 Apple 的[遊戲控制器 Framework](https://developer.apple.com/library/prerelease/tvos/documentation/ServicesDiscovery/Conceptual/GameControllerPG/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013276)具有 tvos 下列修改：

- 微遊戲控制器設定檔 (`GCMicroGamepad`) 已新增至目標 Siri 遠端。
- 新`GCEventViewController`類別可以用來路由傳送到您的應用程式的遊戲控制器事件。 請參閱[判斷遊戲控制器輸入](#Determining-Game-Controller-Input)下面章節以取得詳細資料。

<a name="Game-Controller-Support-Requirements" />

### <a name="game-controller-support-requirements"></a>遊戲控制器支援需求

Apple 有數個 Xamarin.tvOS 應用程式支援遊戲控制器必須符合的特定需求：

- **您必須支援 Siri 遠端**-您必須一律支援 Siri 遠端。 您的遊戲無法要求第 3 個合作對象可以播放的遊戲控制器。
- **您必須支援擴充的控制項配置**-所有 tvOS 遊戲控制器都是非 formfitting 擴充控制站。
- **遊戲必須是具有獨立控制器 Playable** -如果您的應用程式支援擴充的遊戲控制器，它必須只與該遊戲控制器可播放。
- **您必須支援矔菛/縸懫按鈕**-期間遊戲，而使用者按下 [矔菛/縸懫] 按鈕，您應該顯示警示，讓使用者可以暫停/繼續遊戲或返回主功能表。

<a name="Enabling-Game-Controller-Support" />

### <a name="enabling-game-controller-support"></a>啟用遊戲控制器支援

若要啟用遊戲控制器支援 Xamarin.tvOS 應用程式中的，按兩下`Info.plist`檔案**方案總管 中**開啟進行編輯：

[ ![](remote-bluetooth-images/game02.png "Info.plist 編輯器")](remote-bluetooth-images/game02.png)

在下**遊戲控制器**區段中，勾選由**啟用遊戲控制器**，然後檢查所有應用程式將支援的遊戲控制器類型。

<a name="Using-the-Siri-Remote-as-a-Game-Controller" />

### <a name="using-the-siri-remote-as-a-game-controller"></a>使用 Siri 遠端為遊戲控制器

隨附的 Apple TV Siri 遠端可用來當做有限的遊戲控制器。 類似其他遊戲控制器，它會出現遊戲控制器架構當做`GCController`物件和支援`GCMotion`和`GCMicroGamepad`設定檔。

做遊戲控制器時使用 Siri 遠端會具有下列特性：

- [觸控] 介面可用來當做 d-pad 提供類比的輸入的資料。
- 遠端可用在縱向或橫向列印方向，而您的應用程式可讓您決定是否設定檔物件應該翻轉輸入的資料自動。
- 按一下 [觸控] 介面的作用就像按下按鈕**A**遊戲控制站上。
- 矔菛/縸懫按鈕就像是按鈕**X**遊戲控制站上。
- 功能表按鈕應該會顯示警示，讓使用者暫停/繼續遊戲或返回主功能表的能力。

<a name="Summary" />

### <a name="determining-game-controller-input"></a>判斷輸入遊戲控制器

不同於 iOS 其中收到遊戲控制器的事件，以平行方式，與觸控事件、 tvOS 處理所有低階事件，提供高階`UIKit`事件。 如此一來，如果您需要的存取權的低層級的遊戲控制器事件時，您必須關閉`UIKit`的預設行為。

在 tvOS，當您想要處理的遊戲控制器輸入，您必須使用直接`GCEventViewController`（或子類別） 顯示遊戲的使用者介面。 每當`GCEventViewController`是*第一個回應*，遊戲控制器輸入將會擷取及傳遞至您的應用程式，透過遊戲控制器架構。

您可以使用`UserInteractionEnabled`屬性`GCEventViewController`類別，以切換處理和處理事件的方式。

如需實作遊戲控制器支援資訊，請參閱 Apple[使用遊戲控制器](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/WorkingwithGameControllers.html)一節中[tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/index.html)和[遊戲控制器程式設計手冊](https://developer.apple.com/library/prerelease/tvos/documentation/ServicesDiscovery/Conceptual/GameControllerPG/Introduction/Introduction.html)。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已涵蓋新 Siri 遠端隨附 Apple TV、 觸控介面筆勢和 Siri 遙控器按鈕。 接下來，它涵蓋使用筆勢和分鏡腳本、 手勢和程式碼和低階事件。 最後，如果將討論使用遊戲控制器。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
