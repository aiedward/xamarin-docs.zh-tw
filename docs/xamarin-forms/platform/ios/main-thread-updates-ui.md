---
title: IOS 上的主要執行緒控制項更新
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用可讓您在主執行緒上執行控制項版面配置和轉譯更新的 iOS 平臺特定。
ms.prod: xamarin
ms.assetid: 945E711D-9BD2-4BF9-9FB3-CBE0D5B25A49
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 844e04b91910152954973f7e6f10c0d56acadf22
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563678"
---
# <a name="main-thread-control-updates-on-ios"></a>IOS 上的主要執行緒控制項更新

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平臺特定可在主執行緒上執行控制項配置和轉譯更新，而不是在背景執行緒上執行。 這應該很少需要，但在某些情況下可能會導致當機。 它是在 XAML 中使用，方法是將可系結 `Application.HandleControlUpdatesOnMainThread` 屬性設定為 `true` ：

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.HandleControlUpdatesOnMainThread="true">
    ...
</Application>
```

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetHandleControlUpdatesOnMainThread(true);
```

`Application.On<iOS>`方法指定此平臺特定的只會在 iOS 上執行。 `Application.SetHandleControlUpdatesOnMainThread`命名空間中的方法 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 是用來控制是否在主執行緒上執行控制項版面配置和轉譯更新，而不是在背景執行緒上執行。 此外， `Application.GetHandleControlUpdatesOnMainThread` 方法也可以用來傳回是否正在主執行緒上執行控制項配置和轉譯更新。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)