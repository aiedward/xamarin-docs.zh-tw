---
title: GridViewPager
ms.prod: xamarin
ms.assetid: A1CDD5F0-049B-4DFA-A268-8A875D26A675
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/02/2018
ms.openlocfilehash: 3a0b1ec9359b1c6067c253b4d04126dbdd726cc5
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="gridviewpager"></a>GridViewPager

[GridViewPager](https://developer.xamarin.com/samples/GridViewPager/)範例會示範如何為 Android 戴上實作的 2D 選擇器瀏覽模式。

![範例的螢幕擷取畫面 GridViewPager 正方形顯示](gridviewpager-images/gridviewpager.png)

第一次加入[Xamarin Android 戴上支援](http://www.nuget.org/packages/Xamarin.Android.Wear/)NuGet 封裝加入您的專案。

版面配置 XML 看起來像這樣：

```xml
<android.support.wearable.view.GridViewPager xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/pager"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:keepScreenOn="true" />
```

建立[ `GridPagerAdapter` ](http://developer.android.com/reference/android/support/wearable/view/GridPagerAdapter.html) (或子類別，例如[ `FragmentGridPagerAdapter` ](http://developer.android.com/reference/android/support/wearable/view/FragmentGridPagerAdapter.html)提供檢視以顯示使用者進行瀏覽。

[範例配接器](https://github.com/xamarin/monodroid-samples/blob/master/wear/GridViewPager/GridViewPager/SimpleGridPagerAdapter.cs)示範如何實作需要的方法，包括覆寫`RowCount`， `GetColumnCount`， `GetBackground`，和 `GetFragment`

連接配接器所示：

```csharp
pager.Adapter = new SimpleGridPagerAdapter (this, FragmentManager);
```



## <a name="related-links"></a>相關連結

- [Google 2D 選擇器文件](https://developer.android.com/training/wearables/ui/2d-picker.html)
- [android.support.wearable 文件](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
- [GridViewPager （範例）](https://developer.xamarin.com/samples/GridViewPager/)
