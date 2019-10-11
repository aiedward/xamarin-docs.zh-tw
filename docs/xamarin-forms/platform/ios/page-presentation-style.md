---
title: IOS 上的強制回應頁面呈現樣式
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 本文說明如何使用 iOS 平臺特定設定模式頁面的呈現樣式。
ms.prod: xamarin
ms.assetid: C791F7CF-330A-44BA-987A-4CFCCBB9278B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 3b1a88968334bed42be53119c26de43ef9cd1419
ms.sourcegitcommit: eb23b7d745d1090376f9def07e0f11cb089494d0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72171050"
---
# <a name="modal-page-presentation-style-on-ios"></a>IOS 上的強制回應頁面呈現樣式

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

這個 iOS 平臺特定是用來設定強制回應頁面的呈現樣式。 它由在 XAML 中設定`Page.ModalPresentationStyle`可繫結的屬性，以`UIModalPresentationStyle`列舉值：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.ModalPresentationStyle="FormSheet">
    ...
</ContentPage>
```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

public class iOSModalFormSheetPageCS : ContentPage
{
    public iOSModalFormSheetPageCS()
    {
        On<iOS>().SetModalPresentationStyle(UIModalPresentationStyle.FormSheet);
        ...
    }
}
```

`Page.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 `Page.SetModalPresentationStyle`方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間，用來設定強制回應的展示樣式[ `Page` ](xref:Xamarin.Forms.Page)藉由指定下列其中一種`UIModalPresentationStyle`列舉值：

- `FullScreen`可設定以涵蓋整個畫面的強制回應的呈現樣式。 根據預設，強制回應頁面會顯示使用此簡報樣式。
- `FormSheet`其中設定上置中並小於螢幕的強制回應的呈現樣式。

颾魤 ㄛ`GetModalPresentationStyle`方法可用來擷取目前的值`UIModalPresentationStyle`套用至列舉型別[ `Page` ](xref:Xamarin.Forms.Page)。

結果是強制回應的展示樣式[ `Page` ](xref:Xamarin.Forms.Page)可以設定：

[![](page-presentation-style-images/modal-presentation-style-small.png "IPad 上的強制回應呈現樣式")](page-presentation-style-images/modal-presentation-style-large.png#lightbox "IPad 上的強制回應呈現樣式")

> [!NOTE]
> 使用此平台特定設定的強制回應的展示樣式的網頁都必須使用強制回應導覽。 如需詳細資訊，請參閱 < [Xamarin.Forms 強制回應頁面](~/xamarin-forms/app-fundamentals/navigation/modal.md)。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
