---
title: Windows 上的 RefreshView 提取方向
description: 平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 Windows 平臺特定的，讓 RefreshView 的提取方向得以變更。
ms.prod: xamarin
ms.assetid: 407A862B-281E-4384-9696-C0655830B84D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2019
ms.openlocfilehash: cf2ab38bed7b45a48fcf0b5f86add49c0d4cc21f
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697315"
---
# <a name="refreshview-pull-direction-on-windows"></a>Windows 上的 RefreshView 提取方向

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

這個通用 Windows 平臺平臺特定的可讓 `RefreshView` 的提取方向變更，以符合顯示資料之可滾動控制項的方向。 它會在 XAML 中使用，方法是將 `RefreshView.RefreshPullDirection` 可系結屬性設定為 `RefreshPullDirection` 列舉的值：

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

或者，也可以C#使用 Fluent API 來取用：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...
refreshView.On<Windows>().SetRefreshPullDirection(RefreshPullDirection.LeftToRight);
```

@No__t_0 方法會指定這個平臺特定的只會在通用 Windows 平臺上執行。 [@No__t_2](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空間中的 `RefreshView.SetRefreshPullDirection` 方法是用來設定 `RefreshView` 的提取方向，而 `RefreshPullDirection` 列舉會提供四個可能的值：

- `LeftToRight` 表示從左至右的提取會起始重新整理。
- `TopToBottom` 表示從上到下的提取會起始重新整理，而且是 `RefreshView` 的預設提取方向。
- `RightToLeft` 表示從右至左的提取會起始重新整理。
- `BottomToTop` 表示從底部到頂端的提取會起始重新整理。

此外，`GetRefreshPullDirection` 方法可以用來傳回目前的 `RefreshView` `RefreshPullDirection`。

結果是指定的 `RefreshPullDirection` 會套用至 `RefreshView`，以設定提取方向以符合顯示資料之可滾動控制項的方向。 下列螢幕擷取畫面顯示具有 `LeftToRight` 提取方向的 `RefreshView`：

[![UWP 上由左到右提取方向的 RefreshView 螢幕擷取畫面](refreshview-pulldirection-images/refreshview-pulldirection.png "具有由左到右提取方向的 RefreshView")](refreshview-pulldirection-images/refreshview-pulldirection-large.png#lightbox "具有由左到右提取方向的 RefreshView")

> [!NOTE]
> 當您變更提取方向時，進度圓形的開始位置會自動旋轉，讓箭號在適當的提取方向位置上啟動。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
