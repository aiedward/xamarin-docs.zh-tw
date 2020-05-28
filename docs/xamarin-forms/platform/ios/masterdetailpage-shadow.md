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
ms.openlocfilehash: aaf94536d41da47aec10fc655f9d053b753da5a2
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84135963"
---
# <a name="masterdetailpage-shadow-on-ios"></a>IOS 上的 MasterDetailPage 陰影

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此平臺特定的控制是否在顯示主版頁面時，將的詳細資料頁面套用 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 到它。 將可系結屬性設定為，即可在 XAML 中使用它 `MasterDetailPage.ApplyShadow` `true` ：

```xaml
<MasterDetailPage ...
                  xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                  ios:MasterDetailPage.ApplyShadow="true">
    ...
</MasterDetailPage>
```

或者，您也可以使用 Fluent API，從 c # 取用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

public class iOSMasterDetailPageCS : MasterDetailPage
{
    public iOSMasterDetailPageCS(ICommand restore)
    {
        On<iOS>().SetApplyShadow(true);
        // ...
    }
}
```

`MasterDetailPage.On<iOS>`方法會指定此平臺特定只會在 iOS 上執行。 `MasterDetailPage.SetApplyShadow`命名空間中的方法 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 是用來控制 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 當顯示主版頁面時，是否已套用陰影的詳細資料頁面。 此外， `GetApplyShadow` 方法可以用來判斷是否要將陰影套用至的詳細資料頁面 `MasterDetailPage` 。

結果是， [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 當顯示主版頁面時，的詳細資料頁面會套用陰影：

[![具有和不含陰影的 MasterDetailPage 螢幕擷取畫面](masterdetailpage-shadow-images/shadow.png "具有和不含陰影的 MasterDetailPage")](masterdetailpage-shadow-images/shadow-large.png#lightbox "具有和不含陰影的 MasterDetailPage")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
