---
title: 在 iOS 上主要執行緒控制更新
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 這篇文章說明如何使用 iOS 平台特定，可讓控制項的配置和轉譯在主執行緒上執行的更新。
ms.prod: xamarin
ms.assetid: 945E711D-9BD2-4BF9-9FB3-CBE0D5B25A49
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: b9f39cd33d660999cfa00f2003edab7af731ca7c
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2019
ms.locfileid: "65925713"
---
# <a name="main-thread-control-updates-on-ios"></a>在 iOS 上主要執行緒控制更新

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)

控制項的配置和轉譯要對主執行緒，而不是在背景執行緒上執行的更新，可讓此 iOS 平台專屬。 它應該很少需要但在某些情況下可能會導致當機。 藉由設定其已在使用的 XAML`Application.HandleControlUpdatesOnMainThread`可繫結的屬性，以`true`:

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

- [PlatformSpecifics （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
