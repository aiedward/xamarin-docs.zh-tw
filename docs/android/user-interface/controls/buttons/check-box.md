---
title: 核取方塊
ms.prod: xamarin
ms.assetid: A884AF10-D5EA-72CA-2301-B80CEC7FFBE7
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 85c505d03e7a763b24fb176b6a94c0fe43009b79
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
ms.locfileid: "30765749"
---
# <a name="checkbox"></a>核取方塊

在本節中，您將建立的核取方塊選取項目，使用[ `CheckBox` ](https://developer.xamarin.com/api/type/Android.Widget.CheckBox) widget。 按下核取方塊時，快顯通知訊息會指出目前狀態的核取方塊。

開啟**Resources/layout/Main.axml**檔案，然後加入[ `CheckBox` ](https://developer.xamarin.com/api/type/Android.Widget.CheckBox/)元素 (內[ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout)):

```xml
<CheckBox android:id="@+id/checkbox"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="check it out" />
```

若要變更狀態時，請執行一些動作，加入下列程式碼的結尾[ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle)方法：

```csharp
CheckBox checkbox = FindViewById<CheckBox>(Resource.Id.checkbox);

checkbox.Click += (o, e) => {
    if (checkbox.Checked)
        Toast.MakeText (this, "Selected", ToastLength.Short).Show ();
    else
        Toast.MakeText (this, "Not selected", ToastLength.Short).Show ();
};
```

這會擷取[ `CheckBox` ](https://developer.xamarin.com/api/type/Android.Widget.CheckBox/)項目在版面配置，然後處理 Click 事件，它會定義要在按下核取方塊時進行的動作。 按一下時， [ `Checked` ](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked/)屬性時呼叫，以檢查核取方塊的新狀態。 如果核取，則[ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/)會顯示訊息 「 選取 」，否則它會顯示 「 未選取"。 [ `CheckBox` ](https://developer.xamarin.com/api/type/Android.Widget.CheckBox/)處理自己的狀態變更，因此您只需要查詢目前的狀態。

執行應用程式。

**提示：** 如果您需要自行變更狀態 (例如當載入已儲存[ `CheckBoxPreference` ](https://developer.xamarin.com/api/type/Android.Preferences.CheckBoxPreference)，使用[ `Checked` ](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked)屬性 setter 或[ `Toggle()`](https://developer.xamarin.com/api/member/Android.Widget.CompoundButton.Toggle)方法。

*此頁面上的部分是修改依據工作建立及 Android 的開放原始碼專案所共用，並依據條款中所述來使用*
[*Creative Commons 2.5 Attribution 授權*](http://creativecommons.org/licenses/by/2.5/).
