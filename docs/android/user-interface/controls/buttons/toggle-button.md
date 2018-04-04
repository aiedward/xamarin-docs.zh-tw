---
title: ToggleButton
ms.prod: xamarin
ms.assetid: 9ADA8FCF-63ED-897A-DD56-D7D86535A92C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 8323c456a97a033e19374a4bd3ea7468ecafa608
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="togglebutton"></a>ToggleButton

在本節中，您將建立專門用於使用兩個狀態之間切換按鈕[ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/) widget。 這個小工具是絕佳的選項按鈕的替代方式，如果您有兩個互斥的簡單狀態 (「 開啟 」 和 「 關閉 」，例如)。 Android 4.0 （API 層級 14） 引進所謂的切換按鈕的替代方式[ `Switch` ](https://developer.xamarin.com/api/type/Android.Widget.Switch/)。

舉例來說， **ToggleButton**映像的右手邊組提供的範例時可以看到組中，左邊的影像，**交換器**:

![參數和 ToggleButtons 內部和外部狀態中的範例](toggle-button-images/togglebutton-switch.png)  

控制項是樣式的哪一個應用程式使用。 這兩個小工具的功能相同。

開啟**Resources/layout/Main.axml**檔案，然後加入[ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/)元素 (內[ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)):

若要變更狀態時，請執行一些動作，加入下列程式碼的結尾[ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle)方法：

```csharp
ToggleButton togglebutton = FindViewById<ToggleButton>(Resource.Id.togglebutton);

togglebutton.Click += (o, e) => {
    // Perform action on clicks
    if (togglebutton.Checked)
        Toast.MakeText(this, "Checked", ToastLength.Short).Show ();
    else
        Toast.MakeText(this, "Not checked", ToastLength.Short).Show ();
};
```

這會擷取[ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/)項目在版面配置，並處理 Click 事件，它會定義要在按下按鈕時執行的動作。 在此範例中，方法會檢查新的按鈕，然後顯示[ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/)訊息，指出目前的狀態。

請注意， [ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/)自己的狀態變更之間已核對及未核取，因此只要求，這是控制代碼。

執行應用程式。


**提示：**如果您需要自行變更狀態 (例如當載入已儲存[ `CheckBoxPreference` ](https://developer.xamarin.com/api/type/Android.Preferences.CheckBoxPreference/))，使用[ `Checked` ](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked/)屬性 setter 或[ `Toggle()`](https://developer.xamarin.com/api/member/Android.Widget.CompoundButton.Toggle/)方法。


## <a name="related-links"></a>相關連結

- [ToggleButton](http://developer.android.com/reference/android/widget/ToggleButton.html)
- [開關](http://developer.android.com/reference/android/widget/Switch.html)
