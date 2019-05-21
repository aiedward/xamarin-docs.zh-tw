---
title: ListView 在 iOS 上的資料列動畫
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 這篇文章說明如何使用 iOS 平台特定的控制列動畫是否已停用時正在更新 ListView 項目集合。
ms.prod: xamarin
ms.assetid: E8F5103F-4D8E-4A5A-A16C-7FA14EE786AC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/21/2019
ms.openlocfilehash: ac71be25492866b1cf2b12d3343c2f4095fc738d
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2019
ms.locfileid: "65925796"
---
# <a name="listview-row-animations-on-ios"></a>ListView 在 iOS 上的資料列動畫

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)

此 iOS 平台特定的控制項是否資料列動畫時停用[ `ListView` ](xref:Xamarin.Forms.ListView)正在更新項目集合。 它由在 XAML 中設定`ListView.RowAnimationsEnabled`可繫結的屬性，以`false`:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ... ios:ListView.RowAnimationsEnabled="false">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

listView.On<iOS>().SetRowAnimationsEnabled(false);
```

`ListView.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 `ListView.SetRowAnimationsEnabled`方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間，可用來控制資料列動畫是否停用時[ `ListView` ](xref:Xamarin.Forms.ListView)正在更新項目集合。 颾魤 ㄛ`ListView.GetRowAnimationsEnabled`方法可以用來傳回資料列動畫是否已停用上`ListView`。

> [!NOTE]
> [`ListView`](xref:Xamarin.Forms.ListView) 預設會啟用資料列的動畫。 因此，動畫發生於新的資料列插入至`ListView`。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
