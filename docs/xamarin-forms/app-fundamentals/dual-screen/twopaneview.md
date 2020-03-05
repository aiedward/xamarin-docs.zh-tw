---
title: Xamarin. 表單雙畫面版面配置
description: 本指南說明如何使用 Xamarin.Forms TwoPaneView，將雙螢幕裝置 (例如 Surface Duo 和 Surface Neo) 的應用程式體驗最佳化。
ms.prod: xamarin
ms.assetid: 17ee8afa-5e7c-4a4f-a9b6-2aca03f30fe3
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
ms.openlocfilehash: e961dc537d8b793feb3587ec89b69c53ab821088
ms.sourcegitcommit: 5b6d3bddf7148f8bb374de5657bdedc125d72ea7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2020
ms.locfileid: "78160568"
---
# <a name="xamarinforms-dual-screen-layout"></a>Xamarin. 表單雙畫面版面配置

![](~/media/shared/preview.png "This API is currently pre-release")

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/DualScreenDemos)

`TwoPaneView` 類別表示包含兩個檢視的容器，其會根據可用空間來調整內容大小，並將內容並排或由上至下擺放。 `TwoPaneView` 繼承自 `Grid`，因此最簡單的做法是將這些屬性看待成套用到格線的屬性。

## <a name="set-up-twopaneview"></a>設定 TwoPaneView

`TwoPaneView.Source` 屬性可以採用 URI 或本機檔案路徑。 當開啟媒體時，會立即開始播放：

```xaml
<ContentPage xmlns:dualScreen="clr-namespace:Xamarin.Forms.DualScreen;assembly=Xamarin.Forms.DualScreen">
    <dualScreen:TwoPaneView>
        <dualScreen:TwoPaneView.Pane1>
            <StackLayout>
                <Label Text="Pane1 Content" />
            </StackLayout>
        </dualScreen:TwoPaneView.Pane1>
        <dualScreen:TwoPaneView.Pane2>
            <StackLayout>
                <Label Text="Pane2 Content" />
            </StackLayout>
        </dualScreen:TwoPaneView.Pane2>
    </dualScreen:TwoPaneView>
</ContentPage>
```

## <a name="understand-twopaneview-modes"></a>了解 TwoPaneView 模式

只能啟用下列其中一項模式：

- `SinglePane` 表示目前只會顯示一個窗格。
- `Wide`：兩個窗格會水平配置。 窗格會一左一右擺放。 當使用雙螢幕而且裝置為直向時，會採用此模式。
- `Tall`：兩個窗格會垂直配置。 窗格會一上一下擺放。 當使用雙螢幕而且裝置為橫向時，會採用此模式。

## <a name="control-twopaneview-when-its-only-on-one-screen"></a>當 TwoPaneView 只在一個螢幕上時進行控制

下列屬性適用於 `TwoPaneView` 佔用單一螢幕時：

- `MinTallModeHeight` 指出最小高度，控制項最小必須為此高度才能進入直向模式。
- `MinWideModeWidth` 指出最小寬度，控制項最小必須為此寬度才能進入橫向模式。
- `Pane1Length` 用於在 Wide 模式中設定 Pane1 的寬度，以及在 Tall 模式中設定 Pane1 的高度，在 SinglePane 模式中則沒有作用。
- `Pane2Length` 可用於在 Wide 模式中設定 Pane2 的寬度，以及在 Tall 模式中設定 Pane2 的高度，在 SinglePane 模式中則沒有作用。

> [!IMPORTANT]
> 如果 `TwoPaneView` 跨越兩個螢幕，這些屬性就沒有作用。

## <a name="properties-that-apply-when-on-one-screen-or-two"></a>在一個螢幕或兩個螢幕上所套用的屬性

下列屬性適用於 `TwoPaneView` 佔用單一螢幕或兩個螢幕時：

- `TallModeConfiguration` 指出直向模式中的左/右排列，或您是否只要顯示 TwoPaneViewPriority 所定義的單一窗格。
- `WideModeConfiguration` 指出橫向模式中的上/下排列，或您是否只要顯示 TwoPaneViewPriority 所定義的單一窗格。
- `PanePriority` 決定在處於 SinglePane 模式中時，是否要顯示 Pane1 或 Pane2。

## <a name="related-links"></a>相關連結

- [DualScreen (範例)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/DualScreenDemos)
