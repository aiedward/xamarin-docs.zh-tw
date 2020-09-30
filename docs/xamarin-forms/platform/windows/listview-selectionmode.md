---
title: Windows 上的 ListView SelectionMode
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用 Windows 平臺特定的，來控制 ListView 中的專案是否可以回應以點擊手勢。
ms.prod: xamarin
ms.assetid: 57EF3A7F-1407-4B31-AE21-D149293D4228
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: de412e064fa84e516dcb8e9b604068c84a2689e6
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563610"
---
# <a name="listview-selectionmode-on-windows"></a>Windows 上的 ListView SelectionMode

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

在通用 Windows 平臺上，根據預設，會 Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) 使用原生 `ItemClick` 事件來回應互動，而不是原生 `Tapped` 事件。 這會提供協助工具功能，讓 Windows 朗讀程式和鍵盤可以與進行互動 `ListView` 。 不過，它也會在無法運作的情況下轉譯任何點的手勢 `ListView` 。

這通用 Windows 平臺平臺特定的控制中的專案是否 [`ListView`](xref:Xamarin.Forms.ListView) 可以回應點一下手勢，以及原生是否 `ListView` 引發 `ItemClick` 或 `Tapped` 事件。 它是在 XAML 中使用，方法是將 [`ListView.SelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.SelectionModeProperty) 附加屬性設定為 [`ListViewSelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) 列舉值：

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

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

listView.On<Windows>().SetSelectionMode(ListViewSelectionMode.Inaccessible);
```

`ListView.On<Windows>`方法指定此平臺特定只會在通用 Windows 平臺上執行。 [ `ListView.SetSelectionMode` ] (x： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. SetSelectionMode (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。ListView}、 Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. ListViewSelectionMode) # A3 方法（在 [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) 命名空間中）是用來控制中的專案是否 [`ListView`](xref:Xamarin.Forms.ListView) 可以回應點擊手勢，以及 [`ListViewSelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) 提供兩個可能值的列舉：

- [`Accessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Accessible) –表示 `ListView` 將引發原生 `ItemClick` 事件來處理互動，因此提供協助工具功能。 因此，Windows 朗讀程式和鍵盤可以與進行互動 `ListView` 。 但是，中的專案 `ListView` 無法回應點擊手勢。 這是 `ListView` 通用 Windows 平臺上實例的預設行為。
- [`Inaccessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Inaccessible) –表示 `ListView` 將引發原生 `Tapped` 事件來處理互動。 因此，中的專案 `ListView` 可以回應點擊手勢。 不過，沒有任何協助工具功能，因此 Windows 朗讀程式和鍵盤無法與進行互動 `ListView` 。

> [!NOTE]
> `Accessible`和 `Inaccessible` 選取模式是互斥的，而您必須在可存取的 [`ListView`](xref:Xamarin.Forms.ListView) 或可回應點一下手勢的之間進行選擇 `ListView` 。

此外，[ `GetSelectionMode` ] (x： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. GetSelectionMode (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。ListView} ) # A3 方法可以用來傳回目前的 [`ListViewSelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) 。

結果會將指定的套用 [`ListViewSelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) 至 [`ListView`](xref:Xamarin.Forms.ListView) ，以控制中的專案是否 `ListView` 可以回應點一下手勢，以及原生是否 `ListView` 引發 `ItemClick` 或 `Tapped` 事件。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)