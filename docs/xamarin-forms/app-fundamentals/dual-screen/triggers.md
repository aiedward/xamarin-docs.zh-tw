---
title: Xamarin.Forms雙畫面觸發程式
description: 本文說明如何使用 Xamarin.Forms 雙畫面觸發程式，以 XAML 回應使用者介面變更。
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: df10327b2ac12d2b119f1ab558d7f27e8c319507
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84131192"
---
# <a name="xamarinforms-dual-screen-triggers"></a>Xamarin.Forms雙畫面觸發程式

![](~/media/shared/preview.png "This API is currently pre-release")

[`Xamarin.Forms.DualScreen`](xref:Xamarin.Forms.DualScreen)命名空間包含兩個狀態觸發程式：

- [`SpanModeStateTrigger`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger)[`VisualState`](xref:Xamarin.Forms.VisualState)當附加版面配置的視圖模式變更時，就會觸發變更。
- `WindowSpanModeStateTrigger`[`VisualState`](xref:Xamarin.Forms.VisualState)當視窗的視圖模式變更時，就會觸發變更。

如需狀態觸發程式的詳細資訊，請參閱[狀態觸發](~/xamarin-forms/app-fundamentals/triggers.md#state-triggers)程式。

## <a name="span-mode-state-trigger"></a>Span 模式狀態觸發程式

[`SpanModeStateTrigger`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger) [`VisualState`](xref:Xamarin.Forms.VisualState) 當附加版面配置的範圍模式變更時，會觸發變更。 此觸發程式有一個可系結屬性：

- [`SpanMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode)，屬於類型 [`TwoPaneViewMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode) ，表示應套用的範圍模式 [`VisualState`](xref:Xamarin.Forms.VisualState) 。

> [!NOTE]
> [`SpanModeStateTrigger`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger)衍生自 [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) 類別，因此可將事件處理常式附加至 [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) 事件。

下列 XAML 範例顯示 [`Grid`](xref:Xamarin.Forms.Grid) 包含 `SpanModeStateTrigger` 物件的：

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

在此範例中，會在物件上設定視覺狀態 [`Grid`](xref:Xamarin.Forms.Grid) 。 `Grid`只有在顯示一個窗格時，的背景色彩會是綠色，當窗格並排顯示時，會是紅色，而當窗格顯示在上而下時則為紫色。

## <a name="window-span-mode-state-trigger"></a>視窗範圍模式狀態觸發程式

`WindowSpanModeStateTrigger` [`VisualState`](xref:Xamarin.Forms.VisualState) 當視窗的 [範圍] 模式變更時，會觸發變更。 此觸發程式有一個可系結屬性：

- [`SpanMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode)，屬於類型 [`TwoPaneViewMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode) ，表示應套用的範圍模式 [`VisualState`](xref:Xamarin.Forms.VisualState) 。

> [!NOTE]
> `WindowSpanModeStateTrigger`衍生自 [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) 類別，因此可將事件處理常式附加至 [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) 事件。

下列 XAML 範例顯示 [`Grid`](xref:Xamarin.Forms.Grid) 包含 `WindowSpanModeStateTrigger` 物件的：

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

在此範例中，會在物件上設定視覺狀態 [`Grid`](xref:Xamarin.Forms.Grid) 。 `Grid`只有在顯示一個窗格時，的背景色彩會是紅色，當窗格並排顯示時，會是綠色，而當窗格顯示為 [上] 時，則為黃色。

## <a name="related-links"></a>相關連結

- [Xamarin.Forms導致](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.Forms視覺狀態管理員](~/xamarin-forms/user-interface/visual-state-manager.md)
