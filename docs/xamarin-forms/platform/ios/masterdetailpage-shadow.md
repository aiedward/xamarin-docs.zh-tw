---
title: 在 iOS 上 MasterDetailPage 陰影
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺特定的，以控制 MasterDetailPage 在顯示主版頁面時，是否已套用陰影的應用程式詳細資料頁面。
ms.prod: xamarin
ms.assetid: FB907EA2-C00A-43A5-84B1-A43584C867A5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/05/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0b3f7a28452d8507c4cb4a42d75b5edb5d62d8e4
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563677"
---
# <a name="masterdetailpage-shadow-on-ios"></a>在 iOS 上 MasterDetailPage 陰影

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

這個平臺特定的控制項，是在顯示 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 主版頁面時，是否已套用陰影的詳細資料頁面。 它是在 XAML 中使用，方法是將可系結 `MasterDetailPage.ApplyShadow` 屬性設定為 `true` ：

```xaml
<MasterDetailPage ...
                  xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                  ios:MasterDetailPage.ApplyShadow="true">
    ...
</MasterDetailPage>
```

或者，您也可以使用流暢的 API，從 c # 中使用它：

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

`MasterDetailPage.On<iOS>`方法指定此平臺特定的只會在 iOS 上執行。 `MasterDetailPage.SetApplyShadow`命名空間中的方法 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 是用來控制是否要在顯示 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 主版頁面時，將的詳細資料頁面套用至該頁面。 此外， `GetApplyShadow` 方法可以用來判斷是否要將陰影套用至的詳細資料頁面 `MasterDetailPage` 。

結果是，在 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 公開主版頁面時，的詳細資料頁面可以套用陰影：

[![螢幕擷取畫面：具有和不含陰影的 MasterDetailPage](masterdetailpage-shadow-images/shadow.png "MasterDetailPage with 和不含陰影")](masterdetailpage-shadow-images/shadow-large.png#lightbox "MasterDetailPage with 和不含陰影")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)