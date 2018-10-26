---
title: GridViewPager
ms.prod: xamarin
ms.assetid: A1CDD5F0-049B-4DFA-A268-8A875D26A675
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/02/2018
ms.openlocfilehash: 1cb71fa2c73b9ab151555559b22def4be1cf5c73
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50112763"
---
# <a name="gridviewpager"></a>GridViewPager

[GridViewPager](https://developer.xamarin.com/samples/GridViewPager/)範例示範如何實作適用於 Android Wear 的 2D 選擇器瀏覽模式。

![範例的螢幕擷取畫面 GridViewPager 正方形顯示](gridviewpager-images/gridviewpager.png)

第一次新增[Xamarin Android Wear 支援](http://www.nuget.org/packages/Xamarin.Android.Wear/)NuGet 封裝加入您的專案。

版面配置 XML 看起來像這樣：

```xml
<android.support.wearable.view.GridViewPager xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/pager"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:keepScreenOn="true" />
```

建立 [`GridPagerAdapter`](http://developer.android.com/reference/android/support/wearable/view/GridPagerAdapter.html)
（或這類的子類別 [`FragmentGridPagerAdapter`](http://developer.android.com/reference/android/support/wearable/view/FragmentGridPagerAdapter.html)
提供檢視以顯示與使用者瀏覽。

[範例配接器](https://github.com/xamarin/monodroid-samples/blob/master/wear/GridViewPager/GridViewPager/SimpleGridPagerAdapter.cs)示範如何實作必要的方法，包括覆寫`RowCount`， `GetColumnCount`， `GetBackground`，及 `GetFragment`

接通配接器，如所示：

```csharp
pager.Adapter = new SimpleGridPagerAdapter (this, FragmentManager);
```



## <a name="related-links"></a>相關連結

- [Google 的 2D 選擇器文件](https://developer.android.com/training/wearables/ui/2d-picker.html)
- [android.support.wearable docs](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
- [GridViewPager （範例）](https://developer.xamarin.com/samples/GridViewPager/)
