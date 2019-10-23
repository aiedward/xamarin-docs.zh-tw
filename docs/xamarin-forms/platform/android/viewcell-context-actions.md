---
title: 在 Android 上 ViewCell 內容動作
description: 平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用可啟用 ViewCell 內容動作舊版模式的 Android 平臺特定。
ms.prod: xamarin
ms.assetid: 8BD71B10-5037-443F-9975-B941CE393E5A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/24/2019
ms.openlocfilehash: b040c7c5b7f132b0832469efba91dd89d6b2ddbd
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697427"
---
# <a name="viewcell-context-actions-on-android"></a>在 Android 上 ViewCell 內容動作

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

根據預設，從 Xamarin. 表單4.3，當 Android 應用程式中的[`ViewCell`](xref:Xamarin.Forms.ViewCell)定義[`ListView`](xref:Xamarin.Forms.ListView)中每個專案的內容動作時，當 `ListView` 中選取的專案變更時，就會更新內容動作功能表。 不過，在舊版的 Xamarin 中，不會更新內容動作功能表，而這種行為稱為 `ViewCell` 的舊版模式。 如果 `ListView` 使用[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)從定義不同內容動作的[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)物件來設定其 `ItemTemplate`，這種舊版模式可能會導致不正確的行為。

此 Android 平臺特定會啟用[`ViewCell`](xref:Xamarin.Forms.ViewCell)內容動作功能表舊版模式，以提供回溯相容性，以便在[`ListView`](xref:Xamarin.Forms.ListView)中選取的專案變更時，不會更新內容動作功能表。 它會在 XAML 中使用，方法是將 `ViewCell.IsContextActionsLegacyModeEnabled` 可系結屬性設定為 `true`：

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

或者，也可以C#使用 Fluent API 來取用：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

viewCell.On<Android>().SetIsContextActionsLegacyModeEnabled(true);
```

@No__t_0 方法指定此平臺特定的只會在 Android 上執行。 [@No__t_2](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空間中的 `ViewCell.SetIsContextActionsLegacyModeEnabled` 方法是用來啟用[`ViewCell`](xref:Xamarin.Forms.ViewCell)內容動作功能表舊版模式，因此當[`ListView`](xref:Xamarin.Forms.ListView)中選取的專案變更時，不會更新內容動作功能表。 此外，`ViewCell.GetIsContextActionsLegacyModeEnabled` 方法可以用來傳回是否已啟用內容動作舊版模式。

下列螢幕擷取畫面顯示已啟用[`ViewCell`](xref:Xamarin.Forms.ViewCell)內容動作舊版模式：

![在 Android 上啟用 ViewCell 舊版模式的螢幕擷取畫面](viewcell-context-actions-images/legacy-mode-enabled.png "ViewCell 舊版模式已啟用")

在此模式中，儘管針對資料格2定義了不同的內容功能表項目，顯示的內容動作功能表項目與儲存格1和資料格2相同。

下列螢幕擷取畫面顯示已停用的[`ViewCell`](xref:Xamarin.Forms.ViewCell)內容動作舊版模式，這是預設的 Xamarin。表單行為：

![已停用 Android 上 ViewCell 舊版模式的螢幕擷取畫面](viewcell-context-actions-images/legacy-mode-disabled.png "已停用 ViewCell 舊版模式")

在此模式中，會針對儲存格1和資料格2顯示正確的內容動作功能表項目。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
