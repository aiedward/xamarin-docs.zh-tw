---
title: Xamarin.Forms TwoPaneView
description: 本指南說明如何使用 Xamarin.Forms TwoPaneView，將雙螢幕裝置 (例如 Surface Duo 和 Surface Neo) 的應用程式體驗最佳化。
ms.prod: xamarin
ms.assetid: 17ee8afa-5e7c-4a4f-a9b6-2aca03f30fe3
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
ms.openlocfilehash: 1992a714774dba79e42c82e71af0bfe6aed7feb7
ms.sourcegitcommit: 07941cf9704ff88cf4087de5ebdea623ff54edb1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77145502"
---
# <a name="xamarinforms-twopaneview"></a>Xamarin.Forms TwoPaneView

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-samples/tree/pre-release/UserInterface/DualScreenDemos)

表示一個容器，其中包含兩個檢視，會根據可用空間來調整內容大小，並將其並排或一上一下擺放。 TwoPaneView 繼承自 Grid，因此最簡單的做法是將這些屬性看待成套用到格線的屬性

## <a name="set-up-twopaneview"></a>設定 TwoPaneView

`TwoPaneView` 的 `Source` 屬性可以接受 URI 或本機檔案路徑。 當開啟媒體時，會立即開始播放。

```xaml
<ContentPage xmlns:dualScreen="clr-namespace:Xamarin.Forms.DualScreen;assembly=Xamarin.Forms.DualScreen">
    <dualScreen:TwoPaneView>
        <dualScreen:TwoPaneView.Pane1>
            <StackLayout>
                <Label Text="Pane1 Content"></Label>
            </StackLayout>
        </dualScreen:TwoPaneView.Pane1>
        <dualScreen:TwoPaneView.Pane2>
            <StackLayout>
                <Label Text="Pane2 Content"></Label>
            </StackLayout>
        </dualScreen:TwoPaneView.Pane2>
    </dualScreen:TwoPaneView>
</ContentPage>
```

## <a name="understand-twopaneview-modes"></a>了解 TwoPaneView 模式

只能啟用下列模式之一

- `SinglePane`：目前只會顯示一個窗格
- `Wide`：兩個窗格會水平配置。 窗格會一左一右擺放。 當使用雙螢幕而且裝置為直向時，會採用此模式
- `Tall`：兩個窗格會垂直配置。 窗格會一上一下擺放。 當使用雙螢幕而且裝置為橫向時，會採用此模式

## <a name="control-twopaneview-when-its-only-on-one-screen"></a>當 TwoPaneView 只在一個螢幕上時進行控制

只有在 TwoPaneView 佔用單一螢幕時，才需使用下列相關屬性。 如果 TwoPaneView 跨越兩個螢幕，這些屬性就沒有作用。

- `MinTallModeHeight`：指出高度下限，控制項必須是此高度才能進入直向模式
- `MinWideModeWidth`：指出寬度下限，控制項必須是此寬度才能進入橫向模式
- `Pane1Length`：在 Wide 模式中設定 Pane1 的寬度，在 Tall 模式中設定 Pane1 的高度，在 SinglePane 模式中則沒有作用
- `Pane2Length`：在 Wide 模式中設定 Pane2 的寬度，在 Tall 模式中設定 Pane2 的高度，在 SinglePane 模式中則沒有作用

## <a name="properties-that-apply-when-on-one-screen-or-two"></a>在一個螢幕或兩個螢幕上所套用的屬性

- `TallModeConfiguration`：在直向模式中，這表示左/右排列，或您是否只想要顯示 TwoPaneViewPriority 所定義的單一窗格
- `WideModeConfiguration`：在 Wide 模式中，這表示上/下排列，或您是否只想要顯示 TwoPaneViewPriority 所定義的單一窗格
- `PanePriority`：在 SinglePane 模式中要顯示 Pane1 或 Pane2

## <a name="related-links"></a>相關連結

- [DualScreen (範例)](https://github.com/xamarin/xamarin-forms-samples/tree/pre-release/UserInterface/DualScreenDemos)
