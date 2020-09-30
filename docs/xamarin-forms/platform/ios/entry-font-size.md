---
title: IOS 上的輸入字型大小
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用可調整專案字型大小的 iOS 平臺特定。
ms.prod: xamarin
ms.assetid: E8881D4E-902B-4397-A43E-916B2885EC87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 22e020ad585c39671d3e3cc8ec47f55c8c41c90c
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563753"
---
# <a name="entry-font-size-on-ios"></a>IOS 上的輸入字型大小

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平臺特定用來調整的字型大小，以確保輸入的 [`Entry`](xref:Xamarin.Forms.Entry) 文字元合控制項的大小。 它是在 XAML 中使用，方法是將 [`Entry.AdjustsFontSizeToFitWidth`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.AdjustsFontSizeToFitWidthProperty) 附加屬性設定為 `boolean` 值：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
    <StackLayout Margin="20">
        <Entry x:Name="entry"
               Placeholder="Enter text here to see the font size change"
               FontSize="22"
               ios:Entry.AdjustsFontSizeToFitWidth="true" />
        ...
    </StackLayout>
</ContentPage>
```

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

entry.On<iOS>().EnableAdjustsFontSizeToFitWidth();
```

`Entry.On<iOS>`方法指定此平臺特定的只會在 iOS 上執行。 [ `Entry.EnableAdjustsFontSizeToFitWidth` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. EnableAdjustsFontSizeToFitWidth (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。Entry} ) # A3 方法（在 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 命名空間中）是用來調整所輸入文字的字型大小，以確保它符合中的大小 [`Entry`](xref:Xamarin.Forms.Entry) 。 此外， [`Entry`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry) 命名空間中的類別 `Xamarin.Forms.PlatformConfiguration.iOSSpecific` 也有 [ `DisableAdjustsFontSizeToFitWidth` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. DisableAdjustsFontSizeToFitWidth (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。Entry} ) # A3 方法會停用此平臺特定，而 [ `SetAdjustsFontSizeToFitWidth` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. SetAdjustsFontSizeToFitWidth (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。Entry}、system.string) # A7 方法，可透過呼叫 [ `AdjustsFontSizeToFitWidth` ] (x：，用來切換字型大小調整 Xamarin.Forms 。PlatformConfiguration. iOSSpecific. AdjustsFontSizeToFitWidth (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。Entry} ) # A11 方法：

```csharp
entry.On<iOS>().SetAdjustsFontSizeToFitWidth(!entry.On<iOS>().AdjustsFontSizeToFitWidth());
```

結果是調整的字型大小， [`Entry`](xref:Xamarin.Forms.Entry) 以確保輸入的文字元合控制項的大小：

![調整輸入字型大小平臺特定](entry-font-size-images/entry-font-size.png)

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)