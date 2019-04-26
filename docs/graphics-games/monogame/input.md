---
title: MonoGame 遊戲台參考
description: 本文件說明遊戲台，用於存取 MonoGame 中的輸入的裝置的跨平台類別。 它討論如何讀取從遊戲台輸入，並提供範例程式碼。
ms.prod: xamarin
ms.assetid: 1F71F3E8-2397-4C6A-8163-6731ECFB7E03
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 235166b78dfbd4998086a2925a54137f1922f5d1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61385643"
---
# <a name="monogame-gamepad-reference"></a>MonoGame 遊戲台參考

_遊戲台是標準的跨平台的類別，來存取在 MonoGame 的輸入的裝置。_

`GamePad` 可用來讀取輸入從多個 MonoGame 平台上的輸入裝置。 本指南說明如何使用遊樂器類別。 因為輸入的每個裝置不同的版面配置和它所提供的按鈕數目，則本指南包含的圖表，顯示不同的裝置對應。

## <a name="gamepad-as-a-replacement-for-xbox360gamepad"></a>遊戲台 Xbox360GamePad 來取代

提供的原始 XNA API`Xbox360GamePad`讀取輸入從 Xbox 360 或電腦上的遊戲控制器的類別。 MonoGame 已取代此`GamePad`類別因為 Xbox 360 控制器不能用於大部分的 MonoGame 平台 （例如 iOS 或 Xbox One）。 名稱變更，使用儘管`GamePad`類別是類似於`Xbox360GamePad`類別。

## <a name="reading-input-from-gamepad"></a>讀取輸入，從遊戲台

`GamePad`類別會提供標準化的方式，讀取輸入的任何 MonoGame 平台上。 它會提供資訊透過兩種方法：

- `GetState` -傳回控制器的按鈕、 類比的隨身碟和方向鍵的目前狀態。
- `GetCapabilities` -傳回的硬體功能相關資訊，例如控制器是否有特定按鈕，或使用支援震動功能。

### <a name="example-moving-a-character"></a>範例：移動的字元

下列程式碼會示範如何使用 「 左捲動方塊隨身碟 」 移動的字元，藉由設定其`XVelocity`和`YVelocity`屬性。 此程式碼會假設`characterInstance`是具有物件的執行個體`XVelocity`和`YVelocity`屬性：

```csharp
// In Update, or some code called every frame:
var gamePadState = GamePad.GetState(PlayerIndex.One);
// Use gamePadState to move the character
characterInstance.XVelocity = gamePadState.ThumbSticks.Left.X * characterInstance.MaxSpeed;
characterInstance.YVelocity = gamePadState.ThumbSticks.Left.Y * characterInstance.MaxSpeed;
```

### <a name="example-detecting-pushes"></a>範例：偵測推播

`GamePadState` 提供的控制站，例如是否按下特定按鈕的目前狀態的相關資訊。 某些動作，像是讓字元跳，需要檢查如果按鈕已推送 （未關閉最後一個畫面格，但已關閉此框架） 或釋出 （已關閉最後一個畫面格，但不是關閉此框架）。

若要執行這種類型的邏輯，儲存上一個畫面格的區域變數`GamePadState`和目前的框架`GamePadState`必須建立。 下列範例示範如何儲存和使用上一個畫面格`GamePadState`實作包含在跳躍：

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

### <a name="example-checking-for-buttons"></a>範例：檢查按鈕

`GetCapabilities` 可用來檢查控制器是否有特定的硬體，例如類比隨身碟或特定的按鈕。 下列程式碼示範如何檢查在遊戲中需要有兩個按鈕的控制站上的 [B 和 Y] 按鈕：

```csharp
var capabilities = GamePad.GetCapabilities(PlayerIndex.One);
bool hasBButton = capabilities.HasBButton;
bool hasXButton = capabilities.HasXButton;
if(!hasBButton || !hasXButton)
{
    NotifyUserOfMissingButtons();
}
```

## <a name="ios"></a>iOS

iOS 應用程式支援無線遊戲控制器的輸入。

> [!IMPORTANT]
> MonoGame 3.5 的 NuGet 套件不包含無線的遊戲控制器的支援。 在 iOS 上使用遊戲台類別需要建置 MonoGame 3.5 來源，或使用 MonoGame 3.6 NuGet 二進位檔。

### <a name="ios-game-controller"></a>iOS 遊戲控制器

`GamePad`類別會傳回讀取無線控制器的屬性。 中的屬性`GamePad`提供良好的涵蓋範圍適用於標準 iOS 控制站硬體，如下圖所示：

![](input-images/image1.png "遊戲板中的屬性提供良好的涵蓋範圍適用於標準 iOS 控制站硬體，在此圖中所示")

## <a name="apple-tv"></a>Apple TV

Apple TV 遊戲可以供輸入使用 Siri 遠端或無線的遊戲控制器。

### <a name="siri-remote"></a>Siri 遠端

*Siri 遠端*Apple TV 的是原生的輸入的裝置。 雖然可以透過事件讀取 Siri 遠端中的值 (如中所示[Siri 遠端和藍牙控制器指南](~/ios/tvos/platform/remote-bluetooth.md))，則`GamePad`類別可以傳回值，從 Siri 遠端。

請注意，`GamePad`只能讀取輸入從 [開始] 按鈕和觸控介面：

![](input-images/image2.png "請注意，只能讀取輸入從 [開始] 按鈕和觸控介面遊戲台")

因為觸控介面移動讀完`DPad`屬性，會報告值使用的移動`ButtonState`類別。 換句話說，才可使用做為值`ButtonState.Pressed`或`ButtonState.Released`，而不是數值或筆勢。

### <a name="apple-tv-game-controller"></a>Apple 電視遊戲控制器

Apple tv 的遊戲控制器的運作方式的 iOS 應用程式的遊戲控制器。 如需詳細資訊，請參閱 < [iOS 遊戲控制器](#ios-game-controller)一節。 

## <a name="xbox-one"></a>Xbox One

Xbox One 主控台支援 Xbox One 遊戲控制器的讀取的輸入。

### <a name="xbox-one-game-controller"></a>Xbox One 遊戲控制器

Xbox One 遊戲控制器是最常見的輸入的裝置的 Xbox One。 `GamePad`類別提供的遊戲控制器硬體的輸入的值。

![](input-images/image3.png "遊戲台類別提供輸入的值，從遊戲控制器硬體")

## <a name="summary"></a>總結

本指南提供的 MonoGame 的總覽`GamePad`類別，實作方式輸入讀取邏輯和圖表的常見`GamePad`實作。

## <a name="related-links"></a>相關連結

- [MonoGame 遊戲台](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Input_GamePad)
