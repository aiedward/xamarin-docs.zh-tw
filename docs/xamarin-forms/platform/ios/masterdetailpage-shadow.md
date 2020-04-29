---
title: IOS 上的 MasterDetailPage 陰影
description: 平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何在顯示主版頁面時，使用 iOS 平臺特定的來控制 MasterDetailPage 的詳細資料頁面是否已套用陰影。
ms.prod: xamarin
ms.assetid: FB907EA2-C00A-43A5-84B1-A43584C867A5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/05/2020
ms.openlocfilehash: eaa82b5accae73e4c6e8eb8d3fbcc873fc111be9
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82532706"
---
# <a name="masterdetailpage-shadow-on-ios"></a>IOS 上的 MasterDetailPage 陰影

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此平臺特定的控制是否在顯示主版頁面[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)時，將的詳細資料頁面套用到它。 將可系結屬性設定`MasterDetailPage.ApplyShadow`為`true`，即可在 XAML 中使用它：

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

`MasterDetailPage.On<iOS>`方法會指定此平臺特定只會在 iOS 上執行。 命名空間中的`MasterDetailPage.SetApplyShadow`方法是用來控制當顯示主版頁面時，是否[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)已套用陰影的詳細資料頁面。 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 此外， `GetApplyShadow`方法可以用來判斷是否要將陰影套用至的詳細資料頁面`MasterDetailPage`。

結果是，當顯示主版頁面時[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) ，的詳細資料頁面會套用陰影：

[![具有和不含陰影的 MasterDetailPage 螢幕擷取畫面](masterdetailpage-shadow-images/shadow.png "具有和不含陰影的 MasterDetailPage")](masterdetailpage-shadow-images/shadow-large.png#lightbox "具有和不含陰影的 MasterDetailPage")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
