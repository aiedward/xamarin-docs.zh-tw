---
title: IOS 上的頁面狀態列可見度
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺特定的，以設定頁面上狀態列的可見度。
ms.prod: xamarin
ms.assetid: D8BB7C24-A27F-4758-8557-6A81F909ABD9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f653f2a617022ddd80e910fcea7c43450cb911d0
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93366720"
---
# <a name="page-status-bar-visibility-on-ios"></a>IOS 上的頁面狀態列可見度

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平臺特定是用來設定狀態列上的可見度 [`Page`](xref:Xamarin.Forms.Page) ，而且它包含控制狀態列進入或離開的能力 `Page` 。 它是在 XAML 中使用，方法是將 `Page.PrefersStatusBarHidden` 附加屬性設定為列舉的值 `StatusBarHiddenMode` ，並選擇性地將 `Page.PreferredStatusBarUpdateAnimation` 附加屬性設定為 `UIStatusBarAnimation` 列舉值：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.PrefersStatusBarHidden="True"
             ios:Page.PreferredStatusBarUpdateAnimation="Fade">
  ...
</ContentPage>
```

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetPrefersStatusBarHidden(StatusBarHiddenMode.True)
         .SetPreferredStatusBarUpdateAnimation(UIStatusBarAnimation.Fade);
```

`Page.On<iOS>`方法指定此平臺特定的只會在 iOS 上執行。 `Page.SetPrefersStatusBarHidden`命名空間中的方法 `Xamarin.Forms.PlatformConfiguration.iOSSpecific` 是用來藉 [`Page`](xref:Xamarin.Forms.Page) 由指定其中一個列舉值來設定狀態列的可見度 `StatusBarHiddenMode` ： `Default` 、 `True` 或 `False` 。 `StatusBarHiddenMode.True` `StatusBarHiddenMode.False` 無論裝置方向為何，和值都會設定狀態列可見度，而 `StatusBarHiddenMode.Default` 值會隱藏垂直 compact 環境中的狀態列。

結果是可以設定上狀態列的可見度 [`Page`](xref:Xamarin.Forms.Page) ：

![狀態列可見度 Platform-Specific](page-status-bar-visibility-images/hide-status-bar.png)

> [!NOTE]
> 在上 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) ，指定的 `StatusBarHiddenMode` 列舉值也會更新所有子頁面上的狀態列。 在所有其他 [`Page`](xref:Xamarin.Forms.Page) 衍生的類型上，指定的 `StatusBarHiddenMode` 列舉值只會更新目前頁面上的狀態列。

`Page.SetPreferredStatusBarUpdateAnimation`方法是用來藉 [`Page`](xref:Xamarin.Forms.Page) 由指定其中一個列舉值，來設定狀態列的進入或離開方式 `UIStatusBarAnimation` ： `None` 、 `Fade` 或 `Slide` 。 如果 `Fade` 指定了或 `Slide` 列舉值，則當狀態列進入或離開時，會執行0.25 秒動畫 `Page` 。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)