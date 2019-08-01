---
title: Windows 上的 ListView SelectionMode
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 本文說明如何使用 Windows 平臺特定的來控制 ListView 中的專案是否可以回應點按手勢。
ms.prod: xamarin
ms.assetid: 57EF3A7F-1407-4B31-AE21-D149293D4228
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: f6a90a8a0397db99a245f706450e7dc83097a45e
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656903"
---
# <a name="listview-selectionmode-on-windows"></a>Windows 上的 ListView SelectionMode

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

通用 Windows 平台上，預設 Xamarin.Forms [ `ListView` ](xref:Xamarin.Forms.ListView)會使用原生`ItemClick`事件，以回應互動，而不是原生`Tapped`事件。 這可提供協助工具功能，讓 Windows 朗讀程式和鍵盤可以互動`ListView`。 不過，它也會呈現在任何點選手勢`ListView`無法運作。

這個通用 Windows 平臺平臺特定[`ListView`](xref:Xamarin.Forms.ListView)控制項中的專案是否可以回應點按手勢, 以及原生`ListView`是否引發`ItemClick`或`Tapped`事件。 它由在 XAML 中設定[ `ListView.SelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.SelectionModeProperty)附加屬性的值[ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode)列舉型別：

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <ListView ... windows:ListView.SelectionMode="Inaccessible">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

listView.On<Windows>().SetSelectionMode(ListViewSelectionMode.Inaccessible);
```

`ListView.On<Windows>`方法可讓您指定這個特定平台-通用 Windows 平台上只會執行。 [ `ListView.SetSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.SetSelectionMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.ListView},Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode))方法，請在[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空間，是用來控制中的項目[ `ListView` ](xref:Xamarin.Forms.ListView)可以回應與點選手勢，[ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode)提供兩個可能值的列舉型別：

- [`Accessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Accessible) – 表示`ListView`就會引發原生`ItemClick`事件處理互動，並因此提供協助工具功能。 因此，Windows 朗讀程式和鍵盤可以互動`ListView`。 不過中的項目`ListView`無法回應點選手勢。 這是預設行為，如`ListView`通用 Windows 平台上的執行個體。
- [`Inaccessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Inaccessible) – 表示`ListView`就會引發原生`Tapped`來處理互動的事件。 因此中的項目`ListView`可以回應點選手勢。 不過，沒有任何協助工具功能，並因此 Windows 朗讀程式和鍵盤不能與互動`ListView`。

> [!NOTE]
> `Accessible`並`Inaccessible`的選取模式是互斥的而且您必須選擇可供存取[ `ListView` ](xref:Xamarin.Forms.ListView)或`ListView`可以回應點選手勢。

颾魤 ㄛ [ `GetSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.GetSelectionMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.ListView}))方法可以用來傳回目前[ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode)。

結果是，指定[ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode)會套用至[ `ListView` ](xref:Xamarin.Forms.ListView)，哪些控制項是否中的項目`ListView`點選手勢，可回應，因此是否原生`ListView`引發`ItemClick`或`Tapped`事件。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
