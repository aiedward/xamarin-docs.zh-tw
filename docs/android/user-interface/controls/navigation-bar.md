---
title: Xamarin. Android 巡覽列
ms.prod: xamarin
ms.assetid: 6023DB7E-9E72-4B90-A96A-11BC297B8A3D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/01/2017
ms.openlocfilehash: 67c5655c3bbea8cd0a8c21f27719221f599bf481
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457428"
---
# <a name="xamarinandroid-navigation-bar"></a>Xamarin. Android 巡覽列

Android 4 引進了新的系統使用者介面功能，稱為 *巡覽列*，可在不包含 [ **首頁**]、[ **上一頁**] 和 [ **功能表**] 的硬體按鈕的裝置上提供導覽控制項。
下列螢幕擷取畫面顯示來自結點質數裝置的巡覽列：

 [![Android 巡覽列的範例](navigation-bar-images/19-navbar.png)](navigation-bar-images/19-navbar.png#lightbox)

有幾個新的旗標可用來控制導覽列及其控制項的可見度，以及在 Android 3 中引進的系統列可見度。 旗標定義于類別中，如下所示 `Android.View.View` ：

- `SystemUiFlagVisible`&ndash;讓巡覽列成為可見。
- `SystemUiFlagLowProfile`&ndash;在巡覽列中縮小控制項的亮度。
- `SystemUiFlagHideNavigation`&ndash;隱藏巡覽列。

您可以藉由設定屬性，將這些旗標套用至 view 階層中的任何視圖 `SystemUiVisibility` 。 如果有多個 views 已設定此屬性，則系統會將它們與 OR 運算結合，並在設定旗標的視窗保留焦點時套用。 當您移除視圖時，也會一併移除它所設定的任何旗標。

下列範例顯示一個簡單的應用程式，其中按一下任何按鈕會變更 `SystemUiVisibility` ：

 [![示範可見、低設定檔和隱藏 SystemUiVisibility 的螢幕擷取畫面](navigation-bar-images/18-systemuivisibility.png)](navigation-bar-images/18-systemuivisibility.png#lightbox)

變更的程式碼會 `SystemUiVisibility` `TextView` 從每個按鈕的 click 事件處理常式設定的屬性，如下所示：

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

此外，變更也會 `SystemUiVisibility` 引發 `SystemUiVisibilityChange` 事件。 就像設定 `SystemUiVisibility` 屬性一樣， `SystemUiVisibilityChange` 也可以針對階層中的任何視圖註冊事件的處理常式。 例如，下列程式碼會使用 `TextView` 實例來註冊事件：

```csharp
tv.SystemUiVisibilityChange +=
  delegate(object sender, View.SystemUiVisibilityChangeEventArgs e) {
        tv.Text = String.Format ("Visibility = {0}", e.Visibility);
  };
```

## <a name="related-links"></a>相關連結

- [SystemUIVisibilityDemo (範例) ](/samples/xamarin/monodroid-samples/systemuivisibilitydemo)