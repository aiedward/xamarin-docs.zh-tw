---
title: 在 Windows 上的頁面工具列位置
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 這篇文章說明如何使用 Windows 平台特定，以變更頁面上的工具列的位置。
ms.prod: xamarin
ms.assetid: 99F29E95-0C36-4A3B-BDE8-7E9F119E844E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: e0121a9f40135e24663b93bb1db6b6456f2abb3a
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/10/2019
ms.locfileid: "54208893"
---
# <a name="page-toolbar-placement-on-windows"></a>在 Windows 上的頁面工具列位置

[![下載範例](~/media/shared/download.png) 下載範例](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

此通用 Windows 平台特定平台-用來變更工具列的位置上[ `Page` ](xref:Xamarin.Forms.Page)，而且由在 XAML 中設定[ `Page.ToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Page.ToolbarPlacementProperty) ; 附加屬性值[ `ToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement)列舉型別：

```xaml
<TabbedPage ...
            xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
            windows:Page.ToolbarPlacement="Bottom">
  ...
</TabbedPage>
```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

page.On<Windows>().SetToolbarPlacement(ToolbarPlacement.Bottom);
```

`Page.On<Windows>`方法可讓您指定這個平台專屬只會在 Windows 上執行。 [ `Page.SetToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Page.SetToolbarPlacement(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.Page},Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement))方法，請在[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空間，用來設定工具列位置中，使用[ `ToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement)列舉型別提供三個值： [ `Default` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Default)， [ `Top` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Top)，以及[ `Bottom` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Bottom)。

結果就是指定的工具列位置，會套用至[ `Page` ](xref:Xamarin.Forms.Page)執行個體：

[![](page-toolbar-placement-images/toolbar-placement.png "工具列位置平台專屬")](page-toolbar-placement-images/toolbar-placement-large.png#lightbox "工具列放置平台專屬")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
