---
title: Android 上的 ViewCell 內容動作
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用可啟用 ViewCell 內容動作舊版模式的 Android 平臺特定。
ms.prod: xamarin
ms.assetid: 8BD71B10-5037-443F-9975-B941CE393E5A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/24/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e829c7f3763d25574b9ca70c1118ee9521633b48
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93368462"
---
# <a name="viewcell-context-actions-on-android"></a>Android 上的 ViewCell 內容動作

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

根據預設 Xamarin.Forms ，在4.3 中，當 [`ViewCell`](xref:Xamarin.Forms.ViewCell) Android 應用程式中的每個專案定義內容動作時 [`ListView`](xref:Xamarin.Forms.ListView) ，會在變更中選取的專案時更新內容動作功能表 `ListView` 。 不過，在舊版的 Xamarin.Forms 內容動作功能表中，並不會更新，而這種行為稱為 `ViewCell` 舊版模式。 如果 `ListView` 使用 [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) 來 `ItemTemplate` 從 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 定義不同內容動作的物件進行設定，則舊版模式可能會導致不正確的行為。

此 Android 平臺特定 [`ViewCell`](xref:Xamarin.Forms.ViewCell) 會啟用內容動作功能表舊版模式，以提供回溯相容性，如此一來，當選取的專案變更時，就不會更新內容動作功能表 [`ListView`](xref:Xamarin.Forms.ListView) 。 它是在 XAML 中使用，方法是將可系結 `ViewCell.IsContextActionsLegacyModeEnabled` 屬性設定為 `true` ：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ItemsSource="{Binding Items}">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell android:ViewCell.IsContextActionsLegacyModeEnabled="true">
                        <ViewCell.ContextActions>
                            <MenuItem Text="{Binding Item1Text}" />
                            <MenuItem Text="{Binding Item2Text}" />
                        </ViewCell.ContextActions>
                        <Label Text="{Binding Text}" />
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

viewCell.On<Android>().SetIsContextActionsLegacyModeEnabled(true);
```

`ViewCell.On<Android>`方法指定此平臺特定的只會在 Android 上執行。 `ViewCell.SetIsContextActionsLegacyModeEnabled`命名空間中的方法 [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) 是用來啟用 [`ViewCell`](xref:Xamarin.Forms.ViewCell) 內容動作功能表舊版模式，如此一來，當選取的專案變更時，就不會更新內容動作功能表 [`ListView`](xref:Xamarin.Forms.ListView) 。 此外， `ViewCell.GetIsContextActionsLegacyModeEnabled` 方法可以用來傳回是否啟用內容動作舊版模式。

下列螢幕擷取畫面顯示 [`ViewCell`](xref:Xamarin.Forms.ViewCell) 已啟用的內容動作舊版模式：

![Android 上已啟用 ViewCell 舊版模式的螢幕擷取畫面](viewcell-context-actions-images/legacy-mode-enabled.png "ViewCell 舊版模式已啟用")

在此模式中，即使針對儲存格2定義了不同的內容功能表項目，儲存格1和資料格2的顯示內容動作功能表項目也相同。

下列螢幕擷取畫面顯示 [`ViewCell`](xref:Xamarin.Forms.ViewCell) 已停用的內容動作舊版模式，這是預設 Xamarin.Forms 行為：

![Android 上已停用 ViewCell 舊版模式的螢幕擷取畫面](viewcell-context-actions-images/legacy-mode-disabled.png "ViewCell 舊版模式已停用")

在此模式中，儲存格1和資料格2會顯示正確的內容動作功能表項目。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)