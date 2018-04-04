---
title: MonoGame 遊戲台參考
description: 遊戲台是標準、 跨平台的類別來存取在 MonoGame 輸入的裝置。
ms.prod: xamarin
ms.assetid: 1F71F3E8-2397-4C6A-8163-6731ECFB7E03
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: 37e6b0a6365b1e93192c0eaad4fd3975c3cbf010
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="monogame-gamepad-reference"></a>MonoGame 遊戲台參考

_遊戲台是標準、 跨平台的類別來存取在 MonoGame 輸入的裝置。_

`GamePad` 可用來讀取輸入從多個 MonoGame 平台上的輸入裝置。 本指南示範如何使用遊戲台類別。 因為每個輸入的裝置的不同版面配置和它所提供的按鈕數目，本指南包含顯示各種裝置對應的圖表。


# <a name="gamepad-as-a-replacement-for-xbox360gamepad"></a>遊戲台 Xbox360GamePad 來取代

提供的原始 XNA API`Xbox360GamePad`類別從 遊戲控制器上的 Xbox 360 或電腦的讀取輸入。 MonoGame 已取代此`GamePad`類別，因為無法使用 Xbox 360 控制器，在大部分的 MonoGame 平台 （例如 iOS 或 Xbox One）。 名稱變更，使用儘管`GamePad`類別是類似於`Xbox360GamePad`類別。


# <a name="reading-input-from-gamepad"></a>從遊戲台讀取輸入

`GameController`類別提供標準化的方式，讀取輸入的任何 MonoGame 平台上。 它會提供資訊透過兩種方法：

 - `GetState` -傳回目前狀態的控制站的按鈕、 類比隨身碟和方向鍵。
 - `GetCapabilities` -傳回的硬體功能的相關資訊，例如控制器是否有特定的按鈕或支援震動。


## <a name="example-moving-a-character"></a>範例： 移動一個字元

下列程式碼將示範如何使用左的隨身碟等移動一個字元，藉由設定其`XVelocity`和`YVelocity`屬性。 此程式碼假設`characterInstance`是含有物件的執行個體`XVelocity`和`YVelocity`屬性：


```csharp
// In Update, or some code called every frame:
var gamePadState = GamePad.GetState(PlayerIndex.One);
// Use gamePadState to move the character
characterInstance.XVelocity = gamePadState.ThumbSticks.Left.X * characterInstance.MaxSpeed;
characterInstance.YVelocity = gamePadState.ThumbSticks.Left.Y * characterInstance.MaxSpeed;
```


## <a name="example-detecting-pushes"></a>範例： 偵測推播通知

`GamePadState` 提供目前的控制器，例如某些按鈕是否按下狀態的相關資訊。 某些動作，例如進行字元跳，如果，則需要檢查 按鈕已按下 （未關閉最後一個畫面格，但已關閉此框架） 或釋出 （已向最後一個畫面格，但不是關閉這個畫面格）。 

若要執行這種類型的邏輯，儲存上一個畫面格的本機變數`GamePadState`和目前的框架`GamePadState`必須建立。 下列範例示範如何儲存及使用上一個畫面格`GamePadState`實作跳躍：


```csharp
// At class scope:
// Store the last frame's and this frame's GamePadStates.
// "new" them so that code doesn't have to perform null checks:
GamePadState lastFrameGamePadState = new GamePadState();
GamePadState currentGamePadState = new GamePadState();
protected override void Update(GameTime gameTime)
{
    // store off the last state before reading the new one:
    lastFrameGamePadState = currentGamePadState;
    currentGamePadState = GamePad.GetState(PlayerIndex.One);
    bool wasAButtonPushed = 
currentGamePadState.Buttons.A == ButtonState.Pressed
        && lastFrameGamePadState.Buttons.A == ButtonState.Released;
    if(wasAButtonPushed)
    {
        MakeCharacterJump();
    }
...
}
```


## <a name="example-checking-for-buttons"></a>範例： 檢查按鈕

`GetCapabilities` 可用來檢查控制器是否有特定的硬體，例如特定的按鈕或類比搖桿。 下列程式碼會示範如何檢查 B 和 Y 上的按鈕需要有兩個按鈕的遊戲中的控制站：


```csharp
var capabilities = GamePad.GetCapabilities(PlayerIndex.One);
bool hasBButton = capabilities.HasBButton;
bool hasXButton = capabilities.HasXButton;
if(!hasBButton || !hasXButton)
{
    NotifyUserOfMissingButtons();
}
```


# <a name="ios"></a>iOS

iOS 應用程式支援無線遊戲控制器輸入。

> [!IMPORTANT]
> MonoGame 3.5 的 NuGet 套件不包含無線的遊戲控制器的支援。 在 iOS 上使用遊戲台類別需要建置 MonoGame 3.5 來源，或使用 MonoGame 3.6 NuGet 二進位檔。 



## <a name="ios-game-controller"></a>iOS 遊戲控制器

`GamePad`類別會傳回從無線控制站讀取屬性。 中的屬性`GamePad`提供良好地涵蓋標準 ios 控制站硬體，如下列圖表所示：

![](input-images/image1.png "遊戲台中的屬性提供良好地涵蓋標準 ios 控制站硬體，在此圖中所示")


# <a name="apple-tv"></a>Apple TV

Apple TV 的遊戲可以輸入使用 Siri 遠端或無線的遊戲控制器。


## <a name="siri-remote"></a>Siri 遠端

*Siri 遠端*Apple tv 是原生的輸入的裝置。 雖然從 Siri 遠端的值可以讀取透過事件 (如中所示[Siri 遠端和藍芽控制器指南](~/ios/tvos/platform/remote-bluetooth.md))、`GamePad`類別可以傳回值，從遠端使用 Siri。

請注意，`GamePad`只能讀取輸入從 [開始] 按鈕和觸控介面： 

![](input-images/image2.png "請注意，遊戲台可以只讀取輸入從 [開始] 按鈕並觸控介面")

因為觸控仔細閱讀介面移動`DPad`屬性的值會回報使用的移動`ButtonState`類別。 換句話說，才可使用做為值`ButtonState.Pressed`或`ButtonState.Released`，而非數值或手勢。


## <a name="apple-tv-game-controller"></a>Apple TV 遊戲控制器

Apple tv 的遊戲控制器運作方式完全相同的 iOS 應用程式的遊戲控制器。 如需詳細資訊，請參閱[iOS 遊戲控制器區段](#iOS_Game_Controller)。 


# <a name="xbox-one"></a>Xbox One

Xbox One 主控台支援從 Xbox One 的遊戲控制器讀取輸入。


## <a name="xbox-one-game-controller"></a>Xbox 一個遊戲控制器

Xbox One 的遊戲控制器是最常見的輸入的裝置的 Xbox One。 `GamePad`類別會提供從遊戲控制器硬體的輸入的值。

![](input-images/image3.png "這個遊戲台類別會提供輸入的值，從 遊戲控制器硬體")


# <a name="summary"></a>總結

本指南提供概觀 MonoGame 的`GamePad`類別，實作輸入讀取邏輯和圖表的常見方式`GamePad`實作。

## <a name="related-links"></a>相關連結

- [MonoGame GamePad](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Input_GamePad)
