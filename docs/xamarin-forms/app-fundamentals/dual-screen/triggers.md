---
title: Xamarin.形式雙屏觸發器
description: 本文介紹如何使用 Xamarin.Forms 雙螢幕觸發器來回應使用 XAML 的使用者介面更改。
ms.prod: xamarin
ms.assetid: 2181715D-3995-4E71-9A21-6B892F0B3B59
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2020
ms.openlocfilehash: 0cce23973c90c89ce90e40651a2646d5f1bdd2c0
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "78165528"
---
# <a name="xamarinforms-dual-screen-triggers"></a>Xamarin.形式雙屏觸發器

![](~/media/shared/preview.png "This API is currently pre-release")

命名[`Xamarin.Forms.DualScreen`](xref:Xamarin.Forms.DualScreen)空間包括兩個狀態觸發器:

- [`SpanModeStateTrigger`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger)當附加布局[`VisualState`](xref:Xamarin.Forms.VisualState)的視圖模式發生更改時,將觸發更改。
- `WindowSpanModeStateTrigger`當視窗的[`VisualState`](xref:Xamarin.Forms.VisualState)檢視模式發生更改時,將觸發更改。

有關狀態觸發器的詳細資訊,請參閱[狀態觸發器](~/xamarin-forms/app-fundamentals/triggers.md#state-triggers)。

## <a name="span-mode-state-trigger"></a>跨模式狀態觸發器

當[`SpanModeStateTrigger`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger)附加佈局[`VisualState`](xref:Xamarin.Forms.VisualState)的 跨距模式發生更改時,將觸發更改。 此觸發器具有單個可綁定屬性:

- [`SpanMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode)的類型[`TwoPaneViewMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode),指示應應用[`VisualState`](xref:Xamarin.Forms.VisualState)的 跨距模式。

> [!NOTE]
> [`SpanModeStateTrigger`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger)衍生的自類別,[`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase)因此可以將事件處理程式附加到[`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged)事件 。

下面的 XAML 範例[`Grid`](xref:Xamarin.Forms.Grid)顯示`SpanModeStateTrigger`包含 物件的:

```xaml
<Grid>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup>
            <VisualState x:Name="GridSingle">
                <VisualState.StateTriggers>
                    <dualScreen:SpanModeStateTrigger SpanMode="SinglePane"/>
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Green" />
                </VisualState.Setters>
            </VisualState>
            <VisualState x:Name="GridWide">
                <VisualState.StateTriggers>
                    <dualScreen:SpanModeStateTrigger SpanMode="Wide" />
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Red" />
                </VisualState.Setters>
            </VisualState>
            <VisualState x:Name="GridTall">
                <VisualState.StateTriggers>
                    <dualScreen:SpanModeStateTrigger SpanMode="Tall" />
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Purple" />
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
    ...
</Grid>
```

在此示例中,在[`Grid`](xref:Xamarin.Forms.Grid)物件上設置可視狀態。 當僅顯示一個`Grid`窗格時,背景顏色為綠色;當窗格並排顯示時,背景顏色為紅色;當窗格顯示在頂部底部時,背景顏色為紫色。

## <a name="window-span-mode-state-trigger"></a>視窗跨度模式狀態觸發器

當`WindowSpanModeStateTrigger`視窗的[`VisualState`](xref:Xamarin.Forms.VisualState)跨 距模式發生更改時,將觸發更改。 此觸發器具有單個可綁定屬性:

- [`SpanMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode)的類型[`TwoPaneViewMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode),指示應應用[`VisualState`](xref:Xamarin.Forms.VisualState)的 跨距模式。

> [!NOTE]
> `WindowSpanModeStateTrigger`衍生的自類別,[`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase)因此可以將事件處理程式附加到[`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged)事件 。

下面的 XAML 範例[`Grid`](xref:Xamarin.Forms.Grid)顯示`WindowSpanModeStateTrigger`包含 物件的:

```xaml
<Grid>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup>
            <VisualState x:Name="NotSpanned">
                <VisualState.StateTriggers>
                    <dualScreen:WindowSpanModeStateTrigger SpanMode="SinglePane"/>
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Red" />
                </VisualState.Setters>
            </VisualState>
            <VisualState x:Name="Spanned">
                <VisualState.StateTriggers>
                    <dualScreen:WindowSpanModeStateTrigger SpanMode="Wide" />
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Green" />
                </VisualState.Setters>
            </VisualState>
                <VisualState x:Name="Tall">
                    <VisualState.StateTriggers>
                        <dualScreen:WindowSpanModeStateTrigger SpanMode="Tall" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor" Value="Yellow" />
                    </VisualState.Setters>
                </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
    ...
</Grid>    
```

在此示例中,在[`Grid`](xref:Xamarin.Forms.Grid)物件上設置可視狀態。 當僅顯示一個`Grid`窗格時, 的背景顏色為紅色, 當窗格並排顯示時, 和在頂部底部顯示窗格時為黃色。

## <a name="related-links"></a>相關連結

- [Xamarin.Forms 觸發程序](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.表單可視化狀態管理員](~/xamarin-forms/user-interface/visual-state-manager.md)
