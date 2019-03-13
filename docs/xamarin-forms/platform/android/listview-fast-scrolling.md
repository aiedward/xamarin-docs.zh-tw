---
title: 在 Android 上捲動的 ListView 快速
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 這篇文章說明如何使用 Android 平台特定，可讓您快速捲動 ListView 中的資料。
ms.prod: xamarin
ms.assetid: 37D95A2D-74AC-488A-B903-2BDD799EAA5C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: d4b4a72d2bfe77c433c17fa9f427a95121855e01
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209213"
---
# <a name="listview-fast-scrolling-on-android"></a>在 Android 上捲動的 ListView 快速

[![下載範例](~/media/shared/download.png) 下載範例](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

此 Android 平台專屬用來啟用 快速捲動中的資料[ `ListView` ](xref:Xamarin.Forms.ListView)。 它由在 XAML 中設定`ListView.IsFastScrollEnabled`附加屬性`boolean`值：

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

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

var listView = new Xamarin.Forms.ListView { IsGroupingEnabled = true, ... };
listView.SetBinding(ItemsView<Cell>.ItemsSourceProperty, "GroupedEmployees");
listView.GroupDisplayBinding = new Binding("Key");
listView.On<Android>().SetIsFastScrollEnabled(true);
```

`ListView.On<Android>`方法可讓您指定這個平台專屬只會在 Android 上執行。 `ListView.SetIsFastScrollEnabled`方法，請在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空間，用來啟用中的資料進行快速捲動[ `ListView` ](xref:Xamarin.Forms.ListView)。 颾魤 ㄛ`SetIsFastScrollEnabled`方法可用來切換藉由呼叫快速捲動`IsFastScrollEnabled`方法來傳回是否已啟用快速捲動：

```csharp
listView.On<Android>().SetIsFastScrollEnabled(!listView.On<Android>().IsFastScrollEnabled());
```

結果是透過資料在該快速捲動[ `ListView` ](xref:Xamarin.Forms.ListView)可以啟用，如此會變更捲軸捲動方塊的大小：

[![](listview-fast-scrolling-images/fastscroll.png "ListView FastScroll 平台專屬")](listview-fast-scrolling-images/fastscroll-large.png#lightbox "ListView FastScroll 平台專屬")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
