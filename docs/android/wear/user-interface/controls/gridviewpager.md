---
title: GridViewPager
ms.prod: xamarin
ms.assetid: A1CDD5F0-049B-4DFA-A268-8A875D26A675
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/02/2018
ms.openlocfilehash: 9c80535dfddd2a279fac66ab159c2a600681bb71
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456999"
---
# <a name="gridviewpager"></a>GridViewPager

[GridViewPager](/samples/xamarin/monodroid-samples/wear-gridviewpager)範例會示範如何針對 Android 磨損執行2d 選擇器導覽模式。

![正方形顯示上 GridViewPager 的範例螢幕擷取畫面](gridviewpager-images/gridviewpager.png)

首先，將 [Xamarin Android 磨損支援](https://www.nuget.org/packages/Xamarin.Android.Wear/) NuGet 套件新增至您的專案。

版面配置 XML 如下所示：

```xml
<android.support.wearable.view.GridViewPager xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/pager"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:keepScreenOn="true" />
```

建立 [`GridPagerAdapter`](https://developer.android.com/reference/android/support/wearable/view/GridPagerAdapter.html)
 (或子類別，例如 [`FragmentGridPagerAdapter`](https://developer.android.com/reference/android/support/wearable/view/FragmentGridPagerAdapter.html)
提供要在使用者導覽時顯示的視圖。

[範例介面卡](https://github.com/xamarin/monodroid-samples/blob/master/wear/GridViewPager/GridViewPager/SimpleGridPagerAdapter.cs)會顯示如何執行必要的方法，包括、、 `RowCount` `GetColumnCount` `GetBackground` 和的覆寫`GetFragment`

連接介面卡，如下所示：

```csharp
pager.Adapter = new SimpleGridPagerAdapter (this, FragmentManager);
```

## <a name="related-links"></a>相關連結

- [Google 的2D 選擇器檔](https://developer.android.com/training/wearables/ui/2d-picker.html)
- [穿戴式檔](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
- [GridViewPager (範例) ](/samples/xamarin/monodroid-samples/wear-gridviewpager)