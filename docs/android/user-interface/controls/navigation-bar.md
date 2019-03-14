---
title: 巡覽列
ms.prod: xamarin
ms.assetid: 6023DB7E-9E72-4B90-A96A-11BC297B8A3D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/01/2017
ms.openlocfilehash: 9455cac81a0f9ea81e08cf63397e45c1698e1c1b
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670008"
---
# <a name="navigation-bar"></a>巡覽列

Android 4 也引入了新系統使用者介面功能，稱為 *瀏覽列* ，以提供不包含硬體按鈕的裝置上的導覽控制項 **首頁** ， **回** ，並 ** 功能表** 。
下列螢幕擷取畫面顯示導覽列，從 Nexus 質數裝置：

 [![Android 導覽列中的範例](navigation-bar-images/19-navbar.png)](navigation-bar-images/19-navbar.png#lightbox)

有幾個新旗標，以控制可見性的巡覽列及其子控制項，以及 Android 3 中引進的 [系統] 列的可見性。 中所定義的旗標`Android.View.View`類別，並如下所示：

-   `SystemUiFlagVisible` &ndash; 可導覽列中顯示。 
-   `SystemUiFlagLowProfile` &ndash; Dims 導覽列中的控制項。 
-   `SystemUiFlagHideNavigation` &ndash; 隱藏巡覽列。 


這些旗標可以藉由設定套用至任何檢視中檢視階層`SystemUiVisibility`屬性。 如果多個檢視都有這個屬性設定，系統就會結合 OR 運算，並套用它們，只要 [旗標設定] 視窗會保留焦點。 當您移除檢視時，也會移除它已設定任何旗標。

下列範例顯示簡單的應用程式，按一下按鈕的任何變更`SystemUiVisibility`:

 [![示範看得見、 低設定檔，以及隱藏 SystemUiVisibility 的螢幕擷取畫面](navigation-bar-images/18-systemuivisibility.png)](navigation-bar-images/18-systemuivisibility.png#lightbox)

若要變更的程式碼`SystemUiVisibility`中設定屬性`TextView`從每個按鈕的 click 事件處理常式，如下所示：

```csharp
var tv = FindViewById<TextView> (Resource.Id.systemUiFlagTextView);
var lowProfileButton = FindViewById<Button>(Resource.Id.lowProfileButton);
var hideNavButton = FindViewById<Button> (Resource.Id.hideNavigation);
var visibleButton = FindViewById<Button> (Resource.Id.visibleButton);
           
lowProfileButton.Click += delegate {
    tv.SystemUiVisibility =
        (StatusBarVisibility)View.SystemUiFlagLowProfile;
};
           
hideNavButton.Click += delegate {
    tv.SystemUiVisibility =
       (StatusBarVisibility)View.SystemUiFlagHideNavigation;        
};
           
visibleButton.Click += delegate {
    tv.SystemUiVisibility = (StatusBarVisibility)View.SystemUiFlagVisible;
}
```

此外，`SystemUiVisibility`變更引發`SystemUiVisibilityChange`事件。 就像設定`SystemUiVisibility`屬性中的處理常式`SystemUiVisibilityChange`可以註冊事件，以便在階層中的任何檢視。 例如，以下的程式碼會使用`TextView`報名活動的執行個體：

```csharp
tv.SystemUiVisibilityChange +=
  delegate(object sender, View.SystemUiVisibilityChangeEventArgs e) {
        tv.Text = String.Format ("Visibility = {0}", e.Visibility);
  };
```



## <a name="related-links"></a>相關連結

- [SystemUIVisibilityDemo （範例）](https://developer.xamarin.com/samples/monodroid/SystemUIVisibilityDemo/)
- [簡介 Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 平台](https://developer.android.com/sdk/android-4.0.html)
