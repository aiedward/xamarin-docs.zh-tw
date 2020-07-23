---
title: MonoGame 遊戲台參考
description: 本檔說明遊戲台，這是在 MonoGame 中存取輸入裝置的跨平臺類別。 它討論如何從遊戲台讀取輸入，並提供範例程式碼。
ms.prod: xamarin
ms.assetid: 1F71F3E8-2397-4C6A-8163-6731ECFB7E03
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 11b1cfda435e97b09f9d1eded22f11f912d1783d
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86934026"
---
# <a name="monogame-gamepad-reference"></a>MonoGame 遊戲台參考

_遊戲台是一種標準、跨平臺的類別，用來存取 MonoGame 中的輸入裝置。_

`GamePad`可以用來從多個 MonoGame 平臺上的輸入裝置讀取輸入。 本指南說明如何使用遊戲台類別。 由於每個輸入裝置的版面配置和其所提供的按鈕數目不同，因此本指南包含顯示各種裝置對應的圖表。

## <a name="gamepad-as-a-replacement-for-xbox360gamepad"></a>用來取代 Xbox360GamePad 的遊戲程式

原始的「類型」 API 提供 `Xbox360GamePad` 類別，可從 Xbox 360 或電腦上的遊戲控制器讀取輸入。 MonoGame 已將此取代為 `GamePad` 類別，因為 Xbox 360 控制器無法用於大部分的 MonoGame 平臺（例如 iOS 或 Xbox one）。 儘管名稱變更，類別的用法與 `GamePad` `Xbox360GamePad` 類別類似。

## <a name="reading-input-from-gamepad"></a>從遊戲台讀取輸入

`GamePad`類別提供一種標準化的方式來讀取任何 MonoGame 平臺上的輸入。 它提供兩種方法的資訊：

- `GetState`–傳回控制器的按鈕、類比杆和3d 板的目前狀態。
- `GetCapabilities`–傳回硬體功能的相關資訊，例如控制器是否有特定按鈕或支援震動。

### <a name="example-moving-a-character"></a>範例：移動字元

下列程式碼示範如何透過設定其和屬性，使用左側的捲軸來移動字元 `XVelocity` `YVelocity` 。 此程式碼假設 `characterInstance` 是具有和屬性之物件的實例 `XVelocity` `YVelocity` ：

```csharp
// In Update, or some code called every frame:
var gamePadState = GamePad.GetState(PlayerIndex.One);
// Use gamePadState to move the character
characterInstance.XVelocity = gamePadState.ThumbSticks.Left.X * characterInstance.MaxSpeed;
characterInstance.YVelocity = gamePadState.ThumbSticks.Left.Y * characterInstance.MaxSpeed;
```

### <a name="example-detecting-pushes"></a>範例：偵測推播

`GamePadState`提供控制器目前狀態的相關資訊，例如是否按下某個按鈕。 某些動作（例如進行字元跳躍）需要檢查按鈕是否已推送（不是在上一個畫面格，而是在此框架下）或已釋放（已關閉最後一個框架，但不在此框架下）。

若要執行這種類型的邏輯，您必須建立儲存前一個畫面格 `GamePadState` 和目前框架的本機變數 `GamePadState` 。 下列範例顯示如何儲存和使用上一個框架 `GamePadState` 來執行跳躍：

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

`GetCapabilities`可以用來檢查控制器是否有特定硬體，例如特定按鈕或類比杆。 下列程式碼示範如何在需要同時出現兩個按鈕的遊戲中，檢查控制器上的 B 和 Y 按鈕：

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

iOS 應用程式支援無線遊戲控制器輸入。

> [!IMPORTANT]
> 適用于 MonoGame 3.5 的 NuGet 套件不包含無線遊戲控制器的支援。 在 iOS 上使用遊戲台類別時，需要從來源建立 MonoGame 3.5，或使用 MonoGame 3.6 NuGet 二進位檔。

### <a name="ios-game-controller"></a>iOS 遊戲控制器

類別會傳回 `GamePad` 從無線控制器讀取的屬性。 中的屬性 `GamePad` 提供標準 iOS 控制器硬體的良好涵蓋範圍，如下圖所示：

![遊戲台中的屬性可為標準 iOS 控制器硬體提供良好的涵蓋範圍，如下圖所示](input-images/image1.png)

## <a name="apple-tv"></a>Apple 電視

Apple 電視遊戲可以使用 Siri 遙控器或無線遊戲控制器來進行輸入。

### <a name="siri-remote"></a>Siri 遠端

*Siri Remote*是 Apple TV 的原生輸入裝置。 雖然可以透過事件讀取來自 Siri 遠端的值（如[Siri 遠端和藍牙控制器指南](~/ios/tvos/platform/remote-bluetooth.md)所示），但 `GamePad` 類別可以從 Siri 遠端傳回值。

請注意，只能 `GamePad` 從 [播放] 按鈕和觸控介面讀取輸入：

![請注意，遊戲台只能從 [播放] 按鈕和觸控介面讀取輸入](input-images/image2.png)

由於觸控式介面的移動是透過屬性來讀取 `DPad` ，因此會使用類別來報告移動值 `ButtonState` 。 換句話說，值只能當做 `ButtonState.Pressed` 或使用 `ButtonState.Released` ，而不是數值或筆勢。

### <a name="apple-tv-game-controller"></a>Apple 電視遊戲控制器

Apple TV 的遊戲控制器的行為與 iOS 應用程式的遊戲控制器相同。 如需詳細資訊，請參閱[IOS 遊戲控制器](#ios-game-controller)一節。 

## <a name="xbox-one"></a>Xbox One

Xbox One 主控台支援從 Xbox One 遊戲控制器讀取輸入。

### <a name="xbox-one-game-controller"></a>Xbox One 遊戲控制器

Xbox one 遊戲控制器是最常見的 Xbox one 輸入裝置。 `GamePad`類別會提供來自遊戲控制器硬體的輸入值。

![遊戲台類別提供來自遊戲控制器硬體的輸入值](input-images/image3.png)

## <a name="summary"></a>總結

本指南提供 MonoGame 的 `GamePad` 類別、如何執行輸入讀取邏輯，以及常見實作為圖表的總覽 `GamePad` 。

## <a name="related-links"></a>相關連結

- [MonoGame 遊戲台](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Input_GamePad)
