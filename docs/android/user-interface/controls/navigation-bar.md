---
title: Xamarin Android 導覽列
ms.prod: xamarin
ms.assetid: 6023DB7E-9E72-4B90-A96A-11BC297B8A3D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/01/2017
ms.openlocfilehash: f28b9f19e901d75c432dfecbfec8a63588df3d70
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029233"
---
# <a name="xamarinandroid-navigation-bar"></a>Xamarin Android 導覽列

Android 4 引進了一個新的系統使用者介面功能，稱為*導覽*列，它會在未包含 [**首頁**]、[**上一頁**] 和 [**功能表**] 硬體按鈕的裝置上提供導覽控制項。
下列螢幕擷取畫面顯示來自「結點主要裝置的流覽列：

 [Android 導覽列![範例](navigation-bar-images/19-navbar.png)](navigation-bar-images/19-navbar.png#lightbox)

有數個新的旗標可控制導覽列及其控制項的可見度，以及在 Android 3 中引進的系統列可見度。 旗標定義于 `Android.View.View` 類別中，如下所示：

- `SystemUiFlagVisible` &ndash; 會顯示巡覽列。 
- `SystemUiFlagLowProfile` &ndash; 會在導覽列中縮小控制項的亮度。 
- `SystemUiFlagHideNavigation` &ndash; 隱藏巡覽列。 

您可以藉由設定 `SystemUiVisibility` 屬性，將這些旗標套用至視圖階層中的任何視圖。 如果多個 views 已設定此屬性，則系統會將它們與或作業結合，並套用它們，只要設定旗標的視窗會保留焦點。 當您移除視圖時，它所設定的任何旗標也會一併移除。

下列範例顯示一個簡單的應用程式，其中按一下任何按鈕會變更 `SystemUiVisibility`：

 [示範可見、低設定檔和隱藏 SystemUiVisibility 的![螢幕擷取畫面](navigation-bar-images/18-systemuivisibility.png)](navigation-bar-images/18-systemuivisibility.png#lightbox)

變更 `SystemUiVisibility` 的程式碼會從每個按鈕的 click 事件處理常式設定 `TextView` 的屬性，如下所示：

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

此外，`SystemUiVisibility` 變更會引發 `SystemUiVisibilityChange` 事件。 就像設定 `SystemUiVisibility` 屬性一樣，`SystemUiVisibilityChange` 事件的處理常式可以針對階層中的任何視圖進行註冊。 例如，下列程式碼會使用 `TextView` 實例來註冊事件：

```csharp
tv.SystemUiVisibilityChange +=
  delegate(object sender, View.SystemUiVisibilityChangeEventArgs e) {
        tv.Text = String.Format ("Visibility = {0}", e.Visibility);
  };
```

## <a name="related-links"></a>相關連結

- [SystemUIVisibilityDemo （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/systemuivisibilitydemo)
- [霜淇淋三明治簡介](https://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 平臺](https://developer.android.com/sdk/android-4.0.html)
