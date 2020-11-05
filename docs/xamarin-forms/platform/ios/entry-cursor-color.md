---
title: IOS 上的進入游標色彩
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺特定的來設定專案的游標色彩。
ms.prod: xamarin
ms.assetid: 867D70BA-53F9-4434-8094-85D71DCECC2D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0e037fe33dd765625dcdd270610c203ffbfea602
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373194"
---
# <a name="entry-cursor-color-on-ios"></a>IOS 上的進入游標色彩

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平臺特定會將的游標色彩設定 [`Entry`](xref:Xamarin.Forms.Entry) 為指定的色彩。 它是在 XAML 中使用，方法是將可系結 [`Entry.CursorColor`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.CursorColorProperty) 屬性設定為 [`Color`](xref:Xamarin.Forms.Color) ：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <Entry ... ios:Entry.CursorColor="LimeGreen" />
    </StackLayout>
</ContentPage>
```

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var entry = new Xamarin.Forms.Entry();
entry.On<iOS>().SetCursorColor(Color.LimeGreen);
```

`Entry.On<iOS>`方法指定此平臺特定的只會在 iOS 上執行。 [ `Entry.SetCursorColor` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. SetCursorColor (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。Entry}、 Xamarin.Forms 。Color) # A3 方法在 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 命名空間中，會將資料指標色彩設定為指定的 [`Color`](xref:Xamarin.Forms.Color) 。 此外，[ `Entry.GetCursorColor` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. GetCursorColor (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。Entry} ) # A3 方法可以用來取出目前的資料指標色彩。

結果是，中的資料指標色彩 [`Entry`](xref:Xamarin.Forms.Entry) 可以設定為特定的 [`Color`](xref:Xamarin.Forms.Color) ：

![Entry 游標色彩](entry-cursor-color-images/entry-cursorcolor.png)

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)