---
title: Xamarin. 表單雙畫面觸發程式
description: 本文說明如何使用 Xamarin 的雙畫面觸發程式，以 XAML 回應使用者介面變更。
ms.prod: xamarin
ms.assetid: 2181715D-3995-4E71-9A21-6B892F0B3B59
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2020
ms.openlocfilehash: 0cce23973c90c89ce90e40651a2646d5f1bdd2c0
ms.sourcegitcommit: 0e35d3eafad833d3f19768b001bd804ddda8b69b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2020
ms.locfileid: "78165528"
---
# <a name="xamarinforms-dual-screen-triggers"></a>Xamarin. 表單雙畫面觸發程式

![](~/media/shared/preview.png "This API is currently pre-release")

[`Xamarin.Forms.DualScreen`](xref:Xamarin.Forms.DualScreen)命名空間包含兩個狀態觸發程式：

- [`SpanModeStateTrigger`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger)會在附加版面配置的視圖模式變更時觸發[`VisualState`](xref:Xamarin.Forms.VisualState)變更。
- `WindowSpanModeStateTrigger` 會在視窗的視圖模式變更時觸發[`VisualState`](xref:Xamarin.Forms.VisualState)變更。

如需狀態觸發程式的詳細資訊，請參閱[狀態觸發](~/xamarin-forms/app-fundamentals/triggers.md#state-triggers)程式。

## <a name="span-mode-state-trigger"></a>Span 模式狀態觸發程式

當附加版面配置的範圍模式變更時， [`SpanModeStateTrigger`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger)會觸發[`VisualState`](xref:Xamarin.Forms.VisualState)變更。 此觸發程式有一個可系結屬性：

- [`SpanMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode)，屬於[`TwoPaneViewMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode)類型，表示應套用[`VisualState`](xref:Xamarin.Forms.VisualState)的範圍模式。

> [!NOTE]
> [`SpanModeStateTrigger`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger)衍生自[`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase)類別，因此可將事件處理常式附加至[`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged)事件。

下列 XAML 範例顯示包含 `SpanModeStateTrigger` 物件的[`Grid`](xref:Xamarin.Forms.Grid) ：

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

在此範例中，會在[`Grid`](xref:Xamarin.Forms.Grid)物件上設定視覺狀態。 只有在顯示一個窗格時，`Grid` 的背景色彩是綠色的，當窗格並排顯示時，會是紅色，而當窗格顯示在上而下時則為紫色。

## <a name="window-span-mode-state-trigger"></a>視窗範圍模式狀態觸發程式

當視窗的 [範圍] 模式變更時，`WindowSpanModeStateTrigger` 會觸發[`VisualState`](xref:Xamarin.Forms.VisualState)變更。 此觸發程式有一個可系結屬性：

- [`SpanMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode)，屬於[`TwoPaneViewMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode)類型，表示應套用[`VisualState`](xref:Xamarin.Forms.VisualState)的範圍模式。

> [!NOTE]
> `WindowSpanModeStateTrigger` 衍生自[`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase)類別，因此可將事件處理常式附加至[`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged)事件。

下列 XAML 範例顯示包含 `WindowSpanModeStateTrigger` 物件的[`Grid`](xref:Xamarin.Forms.Grid) ：

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

在此範例中，會在[`Grid`](xref:Xamarin.Forms.Grid)物件上設定視覺狀態。 只有一個窗格顯示時，`Grid` 的背景色彩為紅色，當窗格並排顯示時為綠色，而當窗格顯示為 [上下] 時為黃色。

## <a name="related-links"></a>相關連結

- [Xamarin. 表單觸發程式](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin. 表單視覺狀態管理員](~/xamarin-forms/user-interface/visual-state-manager.md)
