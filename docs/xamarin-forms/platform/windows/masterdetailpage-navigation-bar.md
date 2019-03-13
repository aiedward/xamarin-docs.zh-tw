---
title: 在 Windows 上的 MasterDetailPage 導覽列
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 這篇文章說明如何使用 Windows 平台專屬的 MasterDetailPage 上會摺疊導覽列。
ms.prod: xamarin
ms.assetid: 0E7436C9-FA3E-40CD-801C-3F7ED95C412D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: b1a797cfb9384a37c5140995d02b907138ceef6e
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209453"
---
# <a name="masterdetailpage-navigation-bar-on-windows"></a>在 Windows 上的 MasterDetailPage 導覽列

[![下載範例](~/media/shared/download.png) 下載範例](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

此通用 Windows 平台特定平台-用來摺疊導覽列上[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)，而且由在 XAML 中設定[ `MasterDetailPage.CollapseStyle` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapseStyleProperty)並[ `MasterDetailPage.CollapsedPaneWidth`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapsedPaneWidthProperty)附加屬性：

```xaml
<MasterDetailPage ...
                  xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
                  windows:MasterDetailPage.CollapseStyle="Partial"
                  windows:MasterDetailPage.CollapsedPaneWidth="48">
  ...
</MasterDetailPage>

```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

page.On<Windows>().SetCollapseStyle(CollapseStyle.Partial).CollapsedPaneWidth(148);
```

`MasterDetailPage.On<Windows>`方法可讓您指定這個平台專屬只會在 Windows 上執行。 [ `Page.SetCollapseStyle` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.SetCollapseStyle(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.MasterDetailPage},Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle))方法，請在[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空間，用來指定摺疊樣式[ `CollapseStyle` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle)提供兩個列舉值： [ `Full` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle.Full)並[ `Partial` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle.Partial)。 [ `MasterDetailPage.CollapsedPaneWidth` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapsedPaneWidth(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.MasterDetailPage},System.Double))方法用來指定部分摺疊的導覽列的寬度。

結果是，指定[ `CollapseStyle` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle)會套用至[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)執行個體，也指定的寬度：

[![](masterdetailpage-navigation-bar-images/collapsed-navigation-bar.png "摺疊導覽列平台專屬")](masterdetailpage-navigation-bar-images/collapsed-navigation-bar-large.png#lightbox "摺疊導覽列中特定的平台")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
