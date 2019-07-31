---
title: IOS 上已命名字型大小的協助工具調整
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 本文說明如何使用 iOS 平臺特定的來停用已命名字型大小的協助工具縮放。
ms.prod: xamarin
ms.assetid: B443BAF6-E6F6-4A0E-80B5-CAACE6B550EF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/28/2019
ms.openlocfilehash: 678ea2210389eb33bc3a184d758f2bfa07c60bd9
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656738"
---
# <a name="accessibility-scaling-for-named-font-sizes-on-ios"></a>IOS 上已命名字型大小的協助工具調整

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平臺特定會停用所命名字型大小的協助工具調整。 它由在 XAML 中設定`Application.EnableAccessibilityScalingForNamedFontSizes`可繫結的屬性，以`false`:

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.EnableAccessibilityScalingForNamedFontSizes="false">
    ...
</Application>
```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetEnableAccessibilityScalingForNamedFontSizes(false);
```

`Application.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 [命名`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)空間中的`Application.SetEnableAccessibilityScalingForNamedFontSizes`方法是用來停用 iOS 協助工具設定所縮放的命名字型大小。 此外, `Application.GetEnableAccessibilityScalingForNamedFontSizes`方法可以用來傳回已命名的字型大小是否由 iOS 協助工具設定縮放。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
