---
title: GridViewPager
ms.prod: xamarin
ms.assetid: A1CDD5F0-049B-4DFA-A268-8A875D26A675
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/02/2018
ms.openlocfilehash: 77c5eb65601e5bfdac2cbaad2e196a8ae2813981
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030313"
---
# <a name="gridviewpager"></a>GridViewPager

[GridViewPager](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-gridviewpager)範例會示範如何針對 Android 磨損執行2d 選擇器瀏覽模式。

![正方形顯示上 GridViewPager 的範例螢幕擷取畫面](gridviewpager-images/gridviewpager.png)

首先，將[Xamarin Android 磨損支援](https://www.nuget.org/packages/Xamarin.Android.Wear/)NuGet 封裝新增至您的專案。

版面配置 XML 看起來像這樣：

```xml
<android.support.wearable.view.GridViewPager xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/pager"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:keepScreenOn="true" />
```

建立[`GridPagerAdapter`](https://developer.android.com/reference/android/support/wearable/view/GridPagerAdapter.html)
（或子類別，例如[`FragmentGridPagerAdapter`](https://developer.android.com/reference/android/support/wearable/view/FragmentGridPagerAdapter.html)
提供要在使用者流覽時顯示的視圖。

[範例介面卡](https://github.com/xamarin/monodroid-samples/blob/master/wear/GridViewPager/GridViewPager/SimpleGridPagerAdapter.cs)會顯示如何執行必要的方法，包括覆寫 `RowCount`、`GetColumnCount`、`GetBackground`和 `GetFragment`

連接介面卡，如下所示：

```csharp
pager.Adapter = new SimpleGridPagerAdapter (this, FragmentManager);
```

## <a name="related-links"></a>相關連結

- [Google 的2D 選擇器檔](https://developer.android.com/training/wearables/ui/2d-picker.html)
- [穿戴式檔](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
- [GridViewPager （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-gridviewpager)
