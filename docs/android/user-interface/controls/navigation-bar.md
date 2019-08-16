---
title: Xamarin Android 導覽列
ms.prod: xamarin
ms.assetid: 6023DB7E-9E72-4B90-A96A-11BC297B8A3D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/01/2017
ms.openlocfilehash: 3bb2b47623c03d335ae1edc4bf87881622823ea1
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69522928"
---
# <a name="xamarinandroid-navigation-bar"></a>Xamarin Android 導覽列

Android 4 也引入了新系統使用者介面功能，稱為 *瀏覽列* ，以提供不包含硬體按鈕的裝置上的導覽控制項 **首頁** ， **回** ，並 **功能表** 。
下列螢幕擷取畫面顯示來自「結點主要裝置的流覽列:

 [![Android 導覽列範例](navigation-bar-images/19-navbar.png)](navigation-bar-images/19-navbar.png#lightbox)

有數個新的旗標可控制導覽列及其控制項的可見度, 以及在 Android 3 中引進的系統列可見度。 旗標會定義在`Android.View.View`類別中, 並列示如下:

- `SystemUiFlagVisible`&ndash;讓巡覽列顯示。 
- `SystemUiFlagLowProfile`&ndash;在導覽列中縮小控制項的亮度。 
- `SystemUiFlagHideNavigation`&ndash;隱藏巡覽列。 


您可以藉由設定`SystemUiVisibility`屬性, 將這些旗標套用至視圖階層中的任何視圖。 如果多個 views 已設定此屬性, 則系統會將它們與或作業結合, 並套用它們, 只要設定旗標的視窗會保留焦點。 當您移除視圖時, 它所設定的任何旗標也會一併移除。

下列範例顯示一個簡單的應用程式, 其中按一下任何按鈕會變更`SystemUiVisibility`:

 [![示範可見、低設定檔和隱藏 SystemUiVisibility 的螢幕擷取畫面](navigation-bar-images/18-systemuivisibility.png)](navigation-bar-images/18-systemuivisibility.png#lightbox)

要變更的`SystemUiVisibility`程式碼會`TextView`從每個按鈕的 click 事件處理常式, 設定上的屬性, 如下所示:

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

此外, `SystemUiVisibility`變更也會`SystemUiVisibilityChange`引發事件。 就像設定`SystemUiVisibility`屬性一樣, 可以針對階層中`SystemUiVisibilityChange`的任何視圖註冊事件的處理常式。 例如, 下列程式碼會使用`TextView`實例來註冊事件:

```csharp
tv.SystemUiVisibilityChange +=
  delegate(object sender, View.SystemUiVisibilityChangeEventArgs e) {
        tv.Text = String.Format ("Visibility = {0}", e.Visibility);
  };
```



## <a name="related-links"></a>相關連結

- [SystemUIVisibilityDemo (範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/systemuivisibilitydemo)
- [霜淇淋三明治簡介](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 平臺](https://developer.android.com/sdk/android-4.0.html)
