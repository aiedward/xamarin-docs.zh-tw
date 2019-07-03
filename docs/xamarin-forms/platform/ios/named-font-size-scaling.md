---
title: 在 iOS 上名為字型大小的存取範圍調整
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 這篇文章說明如何使用 iOS 平台特定停用協助工具調整已命名的字型大小。
ms.prod: xamarin
ms.assetid: B443BAF6-E6F6-4A0E-80B5-CAACE6B550EF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/28/2019
ms.openlocfilehash: 1fc6fefe1f9fe48fe2abb367b376a5a6f3484462
ms.sourcegitcommit: 0fd04ea3af7d6a6d6086525306523a5296eec0df
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2019
ms.locfileid: "67517947"
---
# <a name="accessibility-scaling-for-named-font-sizes-on-ios"></a>在 iOS 上名為字型大小的存取範圍調整

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)

此 iOS 平台特定停用協助工具調整已命名的字型大小。 它由在 XAML 中設定`Application.EnableAccessibilityScalingForNamedFontSizes`可繫結的屬性，以`false`:

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

`Application.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 `Application.SetEnableAccessibilityScalingForNamedFontSizes`方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間，用來停用 iOS 協助工具設定所要調整規模的具名的字型大小。 颾魤 ㄛ`Application.GetEnableAccessibilityScalingForNamedFontSizes`方法可以用來傳回具名的字型大小是否會調整 iOS 協助工具設定。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
