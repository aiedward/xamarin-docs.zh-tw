---
title: Android 上的 TabbedPage 頁面轉換動畫
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 本文說明如何使用 Android 平臺特定的, 在流覽 TabbedPage 中的頁面時停用轉換動畫。
ms.prod: xamarin
ms.assetid: 2DB4EA6D-9CED-4137-BAB2-B20A457B1CA3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 4f8d6ec2b06855364970bc9b672c3d3f7b9bfdfc
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68649847"
---
# <a name="tabbedpage-page-transition-animations-on-android"></a>Android 上的 TabbedPage 頁面轉換動畫

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

當您以程式設計方式或在中[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)使用索引標籤列流覽頁面時, 會使用此 Android 平臺特定來停用轉換動畫。 它由在 XAML 中設定`TabbedPage.IsSmoothScrollEnabled`可繫結的屬性，以`false`:

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.IsSmoothScrollEnabled="false">
    ...
</TabbedPage>
```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetIsSmoothScrollEnabled(false);
```

`TabbedPage.On<Android>`方法可讓您指定這個平台專屬只會在 Android 上執行。 `TabbedPage.SetIsSmoothScrollEnabled`方法，請在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空間，可用來控制是否在之間瀏覽頁面時，就會顯示轉換動畫[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)。 颾魤 ㄛ`TabbedPage`類別中`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`命名空間也會有下列方法：

- `IsSmoothScrollEnabled`用來擷取是否會在之間瀏覽頁面時，顯示轉換動畫`TabbedPage`。
- `EnableSmoothScroll`用來在頁面之間巡覽時，啟用轉換動畫`TabbedPage`。
- `DisableSmoothScroll`用來在頁面之間巡覽時，停用轉換動畫`TabbedPage`。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
