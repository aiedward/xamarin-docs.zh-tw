---
title: Windows 上的 RefreshView 提取方向
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用 Windows 平臺特定的，讓 Refreshview 拖動的提取方向得以變更。
ms.prod: xamarin
ms.assetid: 407A862B-281E-4384-9696-C0655830B84D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b5c1310e300373fe75cbcd1464220acf989516ba
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91555680"
---
# <a name="refreshview-pull-direction-on-windows"></a>Windows 上的 RefreshView 提取方向

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此通用 Windows 平臺平臺特定可讓的提取方向 `RefreshView` 變更，以符合顯示資料的可滾動控制項方向。 它是在 XAML 中使用，方法是將可系結 `RefreshView.RefreshPullDirection` 屬性設定為 `RefreshPullDirection` 列舉值：

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <RefreshView windows:RefreshView.RefreshPullDirection="LeftToRight"
                 IsRefreshing="{Binding IsRefreshing}"
                 Command="{Binding RefreshCommand}">
        <ScrollView>
            ...
        </ScrollView>
    </RefreshView>
 </ContentPage>
```

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...
refreshView.On<Windows>().SetRefreshPullDirection(RefreshPullDirection.LeftToRight);
```

`RefreshView.On<Windows>`方法指定此平臺特定只會在通用 Windows 平臺上執行。 `RefreshView.SetRefreshPullDirection`命名空間中的方法 [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) 是用來設定的提取方向 `RefreshView` ，而且 `RefreshPullDirection` 列舉提供四個可能的值：

- `LeftToRight` 指出從左至右的提取會起始重新整理。
- `TopToBottom` 指出從上到下的提取會起始重新整理，而且是的預設提取方向 `RefreshView` 。
- `RightToLeft` 表示從右至左的提取會起始重新整理。
- `BottomToTop` 指出從下到上的提取會起始重新整理。

此外， `GetRefreshPullDirection` 方法可以用來傳回目前 `RefreshPullDirection` 的 `RefreshView` 。

結果會將指定的套用 `RefreshPullDirection` 至 `RefreshView` ，以將提取方向設定為符合可滾動控制項顯示資料的方向。 下列螢幕擷取畫面顯示 `RefreshView` 具有 `LeftToRight` 提取方向的：

[![在 UWP 上具有由左到右提取方向之 Refreshview 拖動的螢幕擷取畫面](refreshview-pulldirection-images/refreshview-pulldirection.png "具有由左到右提取方向的 Refreshview 拖動")](refreshview-pulldirection-images/refreshview-pulldirection-large.png#lightbox "具有由左到右提取方向的 Refreshview 拖動")

> [!NOTE]
> 當您變更提取方向時，進度圓形的開始位置會自動旋轉，讓箭號在提取方向的適當位置開始。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)