---
title: IOS 上的頁面狀態列可見度
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 本文說明如何使用 iOS 平臺特定的來設定頁面上狀態列的可見度。
ms.prod: xamarin
ms.assetid: D8BB7C24-A27F-4758-8557-6A81F909ABD9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: a187efa9310fa150ddc884d8b42da5ccb9ecee11
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655850"
---
# <a name="page-status-bar-visibility-on-ios"></a>IOS 上的頁面狀態列可見度

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

這個 iOS 平臺特定是用來設定上[`Page`](xref:Xamarin.Forms.Page)狀態列的可見度, 並包含控制狀態列進入或`Page`離開之方式的能力。 它由在 XAML 中設定`Page.PrefersStatusBarHidden`附加屬性的值`StatusBarHiddenMode`列舉型別，並選擇性地`Page.PreferredStatusBarUpdateAnimation`附加屬性的值`UIStatusBarAnimation`列舉型別：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.PrefersStatusBarHidden="True"
             ios:Page.PreferredStatusBarUpdateAnimation="Fade">
  ...
</ContentPage>
```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetPrefersStatusBarHidden(StatusBarHiddenMode.True)
         .SetPreferredStatusBarUpdateAnimation(UIStatusBarAnimation.Fade);
```

`Page.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 `Page.SetPrefersStatusBarHidden`方法，請在`Xamarin.Forms.PlatformConfiguration.iOSSpecific`命名空間，用來設定 [狀態] 列的可見性[ `Page` ](xref:Xamarin.Forms.Page)藉由指定的其中一個`StatusBarHiddenMode`列舉值： `Default`， `True`或`False`。 `StatusBarHiddenMode.True`並`StatusBarHiddenMode.False`值設定狀態 列可見性，不論裝置方向和`StatusBarHiddenMode.Default`值會隱藏垂直 compact 的環境中的 狀態 列。

結果是在 [狀態] 列的可見性[ `Page` ](xref:Xamarin.Forms.Page)可以設定：

![](page-status-bar-visibility-images/hide-status-bar.png "狀態列可見性平台專屬")

> [!NOTE]
> 在  [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)，指定`StatusBarHiddenMode`列舉值也會更新所有的子頁面上的 [狀態] 列。 所有其他[ `Page` ](xref:Xamarin.Forms.Page)-衍生型別，指定的`StatusBarHiddenMode`列舉值只會更新目前頁面上的 [狀態] 列。

`Page.SetPreferredStatusBarUpdateAnimation`方法用來設定 [狀態] 列如何進入或離開[ `Page` ](xref:Xamarin.Forms.Page)藉由指定的其中一個`UIStatusBarAnimation`列舉值： `None`， `Fade`，或`Slide`。 如果`Fade`或是`Slide`指定列舉值，0.25 的第二個動畫執行狀態列進入或離開`Page`。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
