---
title: Xamarin.Forms 雙螢幕觸發程式
description: 本文說明如何使用 Xamarin.Forms 雙螢幕觸發程式，以 XAML 回應使用者介面變更。
ms.prod: xamarin
ms.assetid: 2181715D-3995-4E71-9A21-6B892F0B3B59
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 59ed6628479048beaaa5a379d495a14079181149
ms.sourcegitcommit: 69bd0fdc698c9b0c0d73217776d7084f32ae88ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/21/2020
ms.locfileid: "90832264"
---
# <a name="no-locxamarinforms-dual-screen-triggers"></a>Xamarin.Forms 雙螢幕觸發程式

[`Xamarin.Forms.DualScreen`](xref:Xamarin.Forms.DualScreen)命名空間包含兩個狀態觸發程式：

- [`SpanModeStateTrigger`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger)[`VisualState`](xref:Xamarin.Forms.VisualState)當附加配置的視圖模式變更時，就會觸發變更。
- `WindowSpanModeStateTrigger`[`VisualState`](xref:Xamarin.Forms.VisualState)當視窗的視圖模式變更時，就會觸發變更。

如需狀態觸發程式的詳細資訊，請參閱 [狀態觸發](~/xamarin-forms/app-fundamentals/triggers.md#state-triggers)程式。

## <a name="span-mode-state-trigger"></a>Span 模式狀態觸發程式

[`SpanModeStateTrigger`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger) [`VisualState`](xref:Xamarin.Forms.VisualState) 當附加配置的範圍模式變更時，會觸發變更。 此觸發程式具有單一可系結屬性：

- [`SpanMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode)，類型為 [`TwoPaneViewMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode) ，表示應該套用的範圍模式 [`VisualState`](xref:Xamarin.Forms.VisualState) 。

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

在此範例中，視覺狀態是在物件上設定的 [`Grid`](xref:Xamarin.Forms.Grid) 。 `Grid`只有在顯示一個窗格時，的背景色彩為綠色，當窗格並排顯示時為紅色，而當窗格顯示在上時，則為紫色。

## <a name="window-span-mode-state-trigger"></a>視窗範圍模式狀態觸發程式

`WindowSpanModeStateTrigger` [`VisualState`](xref:Xamarin.Forms.VisualState) 當視窗的範圍模式變更時，會觸發變更。 此觸發程式具有單一可系結屬性：

- [`SpanMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode)，類型為 [`TwoPaneViewMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode) ，表示應該套用的範圍模式 [`VisualState`](xref:Xamarin.Forms.VisualState) 。

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

在此範例中，視覺狀態是在物件上設定的 [`Grid`](xref:Xamarin.Forms.Grid) 。 `Grid`只有在顯示一個窗格時，的背景色彩為紅色，當窗格並排顯示時為綠色，而當窗格顯示在上時，則為黃色。

## <a name="related-links"></a>相關連結

- [Xamarin.Forms 觸發器](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.Forms 視覺狀態管理員](~/xamarin-forms/user-interface/visual-state-manager.md)
