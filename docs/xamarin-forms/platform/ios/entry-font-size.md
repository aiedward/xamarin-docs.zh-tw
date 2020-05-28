---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 57498811d8789d8ef9ef775f8f39f141b77659c8
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138524"
---
# <a name="entry-font-size-on-ios"></a>IOS 上的輸入字型大小

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

這個 iOS 平臺特定是用來調整的字型大小， [`Entry`](xref:Xamarin.Forms.Entry) 以確保輸入的文字元合控制項的大小。 它會在 XAML 中使用，方法是將 [`Entry.AdjustsFontSizeToFitWidth`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.AdjustsFontSizeToFitWidthProperty) 附加屬性設定為 `boolean` 值：

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

或者，您也可以使用 Fluent API，從 c # 取用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

entry.On<iOS>().EnableAdjustsFontSizeToFitWidth();
```

`Entry.On<iOS>`方法會指定此平臺特定只會在 iOS 上執行。 [ `Entry.EnableAdjustsFontSizeToFitWidth` ] （X： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. EnableAdjustsFontSizeToFitWidth （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. iOS、 Xamarin.Forms 。Entry}））方法（在 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 命名空間中）是用來調整輸入文字的字型大小，以確保它符合中的大小 [`Entry`](xref:Xamarin.Forms.Entry) 。 此外， [`Entry`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry) 命名空間中的類別 `Xamarin.Forms.PlatformConfiguration.iOSSpecific` 也有一個 [ `DisableAdjustsFontSizeToFitWidth` ] （x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. DisableAdjustsFontSizeToFitWidth （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. iOS、 Xamarin.Forms 。Entry}））方法，它會停用此平臺特定的，以及 [ `SetAdjustsFontSizeToFitWidth` ] （x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. SetAdjustsFontSizeToFitWidth （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. iOS、 Xamarin.Forms 。Entry}、system.string）方法，可以藉由呼叫 [ `AdjustsFontSizeToFitWidth` ] （x：，用來切換字型大小調整 Xamarin.Forms 。PlatformConfiguration. iOSSpecific. AdjustsFontSizeToFitWidth （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. iOS、 Xamarin.Forms 。Entry}））方法：

```csharp
entry.On<iOS>().SetAdjustsFontSizeToFitWidth(!entry.On<iOS>().AdjustsFontSizeToFitWidth());
```

結果是會調整的字型大小， [`Entry`](xref:Xamarin.Forms.Entry) 以確保輸入的文字元合控制項的大小：

![](entry-font-size-images/entry-font-size.png "Adjust Entry Font Size Platform-Specific")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
