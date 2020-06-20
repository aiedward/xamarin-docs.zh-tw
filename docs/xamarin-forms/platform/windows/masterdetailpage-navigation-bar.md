---
title: MasterDetailPage Windows 上的巡覽列
description: 平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 Windows 平臺特定的來折迭 MasterDetailPage 上的巡覽列。
ms.prod: xamarin
ms.assetid: 0E7436C9-FA3E-40CD-801C-3F7ED95C412D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d3ae71685c7aaebdceacb5f8b7cd5f3dd308407c
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84137240"
---
# <a name="masterdetailpage-navigation-bar-on-windows"></a>MasterDetailPage Windows 上的巡覽列

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

這個通用 Windows 平臺平臺特定的是用來折迭上的導覽列 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) ，並藉由設定 [`MasterDetailPage.CollapseStyle`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapseStyleProperty) 和附加屬性在 XAML 中使用 [`MasterDetailPage.CollapsedPaneWidth`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapsedPaneWidthProperty) ：

```xaml
<MasterDetailPage ...
                  xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
                  windows:MasterDetailPage.CollapseStyle="Partial"
                  windows:MasterDetailPage.CollapsedPaneWidth="48">
  ...
</MasterDetailPage>

```

或者，您也可以使用 Fluent API，從 c # 取用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

page.On<Windows>().SetCollapseStyle(CollapseStyle.Partial).CollapsedPaneWidth(148);
```

`MasterDetailPage.On<Windows>`方法會指定這個平臺特定的只會在 Windows 上執行。 [ `Page.SetCollapseStyle` ] （X： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. MasterDetailPage. SetCollapseStyle （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration。 Windows， Xamarin.Forms 。MasterDetailPage}、 Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. CollapseStyle））方法，在命名空間中，用 [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) 來指定折迭樣式， [`CollapseStyle`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle) 列舉提供兩個值： [`Full`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle.Full) 和 [`Partial`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle.Partial) 。 [ `MasterDetailPage.CollapsedPaneWidth` ] （X： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. MasterDetailPage. CollapsedPaneWidth （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration。 Windows， Xamarin.Forms 。MasterDetailPage}，System.object）方法可用來指定部分折迭導覽列的寬度。

結果是，指定的 [`CollapseStyle`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle) 會套用至 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 實例，同時也會指定寬度：

[![](masterdetailpage-navigation-bar-images/collapsed-navigation-bar.png "Collapsed Navigation Bar Platform-Specific")](masterdetailpage-navigation-bar-images/collapsed-navigation-bar-large.png#lightbox "Collapsed Navigation Bar Platform-Specific")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
