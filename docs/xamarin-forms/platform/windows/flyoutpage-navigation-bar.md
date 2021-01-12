---
title: 在 Windows 上 FlyoutPage 巡覽列
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用在 FlyoutPage 上折迭巡覽列的 Windows 平臺特定。
ms.prod: xamarin
ms.assetid: 0E7436C9-FA3E-40CD-801C-3F7ED95C412D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e2f6a4c3eaf2668eff7e2123378175f7844a3413
ms.sourcegitcommit: 1decf2c65dc4c36513f7dd459a5df01e170a036f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98115141"
---
# <a name="flyoutpage-navigation-bar-on-windows"></a>在 Windows 上 FlyoutPage 巡覽列

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

這個通用 Windows 平臺平臺特定用來折迭上的導覽列 [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) ，並藉由設定 [`FlyoutPage.CollapseStyle`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.FlyoutPage.CollapseStyleProperty) 和附加屬性，在 XAML 中使用 [`FlyoutPage.CollapsedPaneWidth`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.FlyoutPage.CollapsedPaneWidthProperty) ：

```xaml
<FlyoutPage ...
                  xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
                  windows:FlyoutPage.CollapseStyle="Partial"
                  windows:FlyoutPage.CollapsedPaneWidth="48">
  ...
</FlyoutPage>

```

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

page.On<Windows>().SetCollapseStyle(CollapseStyle.Partial).CollapsedPaneWidth(148);
```

`FlyoutPage.On<Windows>`方法指定此平臺特定的只會在 Windows 上執行。 [ `Page.SetCollapseStyle` ] (x： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. FlyoutPage. SetCollapseStyle (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。FlyoutPage}， Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. CollapseStyle) # A3 方法（在 [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) 命名空間中）是用來指定折迭樣式，且 [`CollapseStyle`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle) 列舉提供兩個值： [`Full`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle.Full) 和 [`Partial`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle.Partial) 。 [ `FlyoutPage.CollapsedPaneWidth` ] (x： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. FlyoutPage. CollapsedPaneWidth (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。FlyoutPage}，system.string) # A3 方法用來指定部分折迭巡覽列的寬度。

結果是將指定的套用 [`CollapseStyle`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle) 至 [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) 實例，並同時指定寬度：

[![折迭的導覽列平臺特定](flyoutpage-navigation-bar-images/collapsed-navigation-bar.png)](flyoutpage-navigation-bar-images/collapsed-navigation-bar-large.png#lightbox "折迭巡覽列 Platform-Specific")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)