---
title: Android 磨損控制項
ms.prod: xamarin
ms.assetid: 5B62A5F8-5E55-4B3C-BFC4-E21CDB27C08B
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 54ef09af1da484305eecc2107d0245426d11a646
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030301"
---
# <a name="android-wear-controls"></a>Android 磨損控制項

Android 磨損應用程式可以使用已用於一般 Android 應用程式的許多相同控制項，包括 `Button`、`TextView`和影像可繪製資源。 您也可以使用版面配置控制項，包括 `ScrollView`、`LinearLayout`和 `RelativateLayout`。

此頁面會透過[穿戴式支援](https://www.nuget.org/packages/Xamarin.Android.Wear/)NuGet 套件，從 Xamarin 專案中提供的[穿戴式 UI 程式庫](https://developer.android.com/training/wearables/apps/layouts.html#UiLibrary)連結至 Android-磨損特定的控制項。 這些控制項包括下列各項：

- **GridViewPager** &ndash; 建立二維導覽介面，使用者可在其中上下滾動以進行選取（如需詳細資訊，請參閱[GridViewPager](~/android/wear/user-interface/controls/gridviewpager.md)）：

    ![GridViewPager 的範例螢幕擷取畫面](images/gridviewpager.png)

磨損應用程式的其他重要控制包括：

- `BoxInsetLayout` （請參閱使用[螢幕大小](~/android/wear/screen-sizes.md)）、

- `WatchViewStub` （請參閱使用[螢幕大小](~/android/wear/screen-sizes.md)）、

- `CardFrame` （請參閱[Android 建立卡片](https://developer.android.com/training/wearables/ui/cards.html)）、

- `CardScrollView` （請參閱[Android 建立卡片](https://developer.android.com/training/wearables/ui/cards.html)）、

- `WearableListView` （請參閱[Android 建立清單](https://developer.android.com/training/wearables/ui/lists.html)）。

## <a name="related-links"></a>相關連結

- [穿戴式檔](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
