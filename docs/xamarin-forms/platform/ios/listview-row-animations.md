---
title: IOS 上的 ListView 資料列動畫
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 本文說明如何使用 iOS 平臺特定的來控制在更新 ListView 專案集合時是否停用資料列動畫。
ms.prod: xamarin
ms.assetid: E8F5103F-4D8E-4A5A-A16C-7FA14EE786AC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/21/2019
ms.openlocfilehash: 9e44c22e670847102cf0bc0f79a860abcac30760
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68652826"
---
# <a name="listview-row-animations-on-ios"></a>IOS 上的 ListView 資料列動畫

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平臺專屬的[`ListView`](xref:Xamarin.Forms.ListView)控制項會在專案集合更新時, 是否要停用資料列動畫。 它由在 XAML 中設定`ListView.RowAnimationsEnabled`可繫結的屬性，以`false`:

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

`ListView.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 命名空間`ListView.SetRowAnimationsEnabled`中的方法可用來控制更新[`ListView`](xref:Xamarin.Forms.ListView)專案集合時, 是否要停用資料列動畫。 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 此外, `ListView.GetRowAnimationsEnabled`方法可以用來傳回上的`ListView`資料列動畫是否停用。

> [!NOTE]
> [`ListView`](xref:Xamarin.Forms.ListView)預設會啟用資料列動畫。 因此, 在中插入`ListView`新的資料列時, 就會發生動畫。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
