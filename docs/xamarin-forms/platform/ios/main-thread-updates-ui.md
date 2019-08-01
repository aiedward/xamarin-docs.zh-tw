---
title: IOS 上的主要執行緒控制項更新
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 本文說明如何使用 iOS 平臺特定的, 讓控制項版面配置和轉譯更新在主執行緒上執行。
ms.prod: xamarin
ms.assetid: 945E711D-9BD2-4BF9-9FB3-CBE0D5B25A49
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 9603cccc1f08be057bc66012cdde75e1b7391f1a
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655271"
---
# <a name="main-thread-control-updates-on-ios"></a>IOS 上的主要執行緒控制項更新

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

這個 iOS 平臺特定可讓控制項版面配置和轉譯更新在主執行緒上執行, 而不是在背景執行緒上執行。 它應該很少需要但在某些情況下可能會導致當機。 藉由設定其已在使用的 XAML`Application.HandleControlUpdatesOnMainThread`可繫結的屬性，以`true`:

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.HandleControlUpdatesOnMainThread="true">
    ...
</Application>
```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetHandleControlUpdatesOnMainThread(true);
```

`Application.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 `Application.SetHandleControlUpdatesOnMainThread`方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間，可用來控制是否控制配置和轉譯的更新，會對主執行緒，而不是在背景執行緒上執行。 颾魤 ㄛ`Application.GetHandleControlUpdatesOnMainThread`方法可以用來傳回是否正在執行控制項的配置和轉譯更新主執行緒上。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
