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
ms.openlocfilehash: 69594924f26afff133d8f211199cac44e66254d9
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84128020"
---
# <a name="page-status-bar-visibility-on-ios"></a>IOS 上的頁面狀態列可見度

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

這個 iOS 平臺特定是用來設定上狀態列的可見度 [`Page`](xref:Xamarin.Forms.Page) ，並包含控制狀態列進入或離開之方式的能力 `Page` 。 它會在 XAML 中使用，方法是將 `Page.PrefersStatusBarHidden` 附加屬性設定為列舉的值 `StatusBarHiddenMode` ，並選擇性地將 `Page.PreferredStatusBarUpdateAnimation` 附加屬性設為列舉的值 `UIStatusBarAnimation` ：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.PrefersStatusBarHidden="True"
             ios:Page.PreferredStatusBarUpdateAnimation="Fade">
  ...
</ContentPage>
```

或者，您也可以使用 Fluent API，從 c # 取用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetPrefersStatusBarHidden(StatusBarHiddenMode.True)
         .SetPreferredStatusBarUpdateAnimation(UIStatusBarAnimation.Fade);
```

`Page.On<iOS>`方法會指定此平臺特定只會在 iOS 上執行。 `Page.SetPrefersStatusBarHidden`命名空間中的方法 `Xamarin.Forms.PlatformConfiguration.iOSSpecific` 是藉由指定其中一個列舉值，用來設定上狀態列的可見度 [`Page`](xref:Xamarin.Forms.Page) `StatusBarHiddenMode` ： `Default` 、 `True` 或 `False` 。 `StatusBarHiddenMode.True` `StatusBarHiddenMode.False` 不論裝置的方向為何，和值都會設定狀態列可見度，而 `StatusBarHiddenMode.Default` 值則會隱藏垂直精簡環境中的狀態列。

結果是可以設定上狀態列的可見度 [`Page`](xref:Xamarin.Forms.Page) ：

![](page-status-bar-visibility-images/hide-status-bar.png "Status Bar Visibility Platform-Specific")

> [!NOTE]
> 在上 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) ，指定的 `StatusBarHiddenMode` 列舉值也會更新所有子頁面上的狀態列。 在所有其他 [`Page`](xref:Xamarin.Forms.Page) 衍生的類型上，指定的 `StatusBarHiddenMode` 列舉值只會更新目前頁面上的狀態列。

`Page.SetPreferredStatusBarUpdateAnimation`方法是用來設定狀態列如何藉 [`Page`](xref:Xamarin.Forms.Page) 由指定其中一個列舉值來進入或離開 `UIStatusBarAnimation` ： `None` 、 `Fade` 或 `Slide` 。 如果 `Fade` 指定或 `Slide` 列舉值，則會執行0.25 秒動畫，因為狀態列會進入或離開 `Page` 。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
