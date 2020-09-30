---
title: Android 上的 ListView 快速滾動
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用 Android 平臺特定的，讓您能夠快速地透過 ListView 中的資料進行滾動。
ms.prod: xamarin
ms.assetid: 37D95A2D-74AC-488A-B903-2BDD799EAA5C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 80b8dc663457316eef831c1e60894cc6b95e281e
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91564039"
---
# <a name="listview-fast-scrolling-on-android"></a>Android 上的 ListView 快速滾動

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 Android 平臺特定用於啟用在中的資料快速滾動 [`ListView`](xref:Xamarin.Forms.ListView) 。 它是在 XAML 中使用，方法是將 `ListView.IsFastScrollEnabled` 附加屬性設定為 `boolean` 值：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        ...
        <ListView ItemsSource="{Binding GroupedEmployees}"
                  GroupDisplayBinding="{Binding Key}"
                  IsGroupingEnabled="true"
                  android:ListView.IsFastScrollEnabled="true">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

var listView = new Xamarin.Forms.ListView { IsGroupingEnabled = true, ... };
listView.SetBinding(ItemsView<Cell>.ItemsSourceProperty, "GroupedEmployees");
listView.GroupDisplayBinding = new Binding("Key");
listView.On<Android>().SetIsFastScrollEnabled(true);
```

`ListView.On<Android>`方法指定此平臺特定的只會在 Android 上執行。 在 `ListView.SetIsFastScrollEnabled` 命名空間中的方法 [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) 是用來啟用在中的資料快速滾動 [`ListView`](xref:Xamarin.Forms.ListView) 。 此外，您 `SetIsFastScrollEnabled` 可以藉由呼叫方法來傳回快速滾動是否已啟用，以使用方法切換快速滾動 `IsFastScrollEnabled` ：

```csharp
listView.On<Android>().SetIsFastScrollEnabled(!listView.On<Android>().IsFastScrollEnabled());
```

結果是可以啟用快速滾動中的資料 [`ListView`](xref:Xamarin.Forms.ListView) ，這會變更捲動方塊的大小：

[![ListView FastScroll 平臺特定](listview-fast-scrolling-images/fastscroll.png)](listview-fast-scrolling-images/fastscroll-large.png#lightbox "ListView FastScroll 平臺特定")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)