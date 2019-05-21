---
title: 在 Windows 上的 ListView SelectionMode
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 這篇文章說明如何使用 Windows 平台特定，控制在 ListView 中的項目是否可回應點選手勢。
ms.prod: xamarin
ms.assetid: 57EF3A7F-1407-4B31-AE21-D149293D4228
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 59b9025b58af4d7080d0506d4994cb72bf4f096d
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2019
ms.locfileid: "65924748"
---
# <a name="listview-selectionmode-on-windows"></a>在 Windows 上的 ListView SelectionMode

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)

通用 Windows 平台上，預設 Xamarin.Forms [ `ListView` ](xref:Xamarin.Forms.ListView)會使用原生`ItemClick`事件，以回應互動，而不是原生`Tapped`事件。 這可提供協助工具功能，讓 Windows 朗讀程式和鍵盤可以互動`ListView`。 不過，它也會呈現在任何點選手勢`ListView`無法運作。

此通用 Windows 平台特定平台可控制是否中的項目[ `ListView` ](xref:Xamarin.Forms.ListView)點選手勢，可回應，因此是否原生`ListView`引發`ItemClick`或`Tapped`事件。 它由在 XAML 中設定[ `ListView.SelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.SelectionModeProperty)附加屬性的值[ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode)列舉型別：

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

- [PlatformSpecifics （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
