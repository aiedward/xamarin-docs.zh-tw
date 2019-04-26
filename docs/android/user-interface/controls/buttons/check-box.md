---
title: 核取方塊
ms.prod: xamarin
ms.assetid: A884AF10-D5EA-72CA-2301-B80CEC7FFBE7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: e03595e8d88a2f12341b9e339d0581c631224848
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61268314"
---
# <a name="checkbox"></a>核取方塊

在本節中，您將建立的核取方塊選取項目，使用 [`CheckBox`](https://developer.xamarin.com/api/type/Android.Widget.CheckBox)
小工具。 按下核取方塊時，便會出現快顯訊息中表示核取方塊的目前狀態。

開啟**Resources/layout/Main.axml**檔案，並新增[ `CheckBox` ](https://developer.xamarin.com/api/type/Android.Widget.CheckBox/)項目 (內[ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout)):

```xml
<CheckBox android:id="@+id/checkbox"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="check it out" />
```

變更狀態時，請執行一些作業，請將下列程式碼新增至結尾 [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle)
方法：

```csharp
CheckBox checkbox = FindViewById<CheckBox>(Resource.Id.checkbox);

checkbox.Click += (o, e) => {
    if (checkbox.Checked)
        Toast.MakeText (this, "Selected", ToastLength.Short).Show ();
    else
        Toast.MakeText (this, "Not selected", ToastLength.Short).Show ();
};
```

這會擷取 [`CheckBox`](https://developer.xamarin.com/api/type/Android.Widget.CheckBox/)
項目在版面配置，然後處理 Click 事件，其定義的動作進行時按一下核取方塊。 按一下時， [`Checked`](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked/)
屬性會呼叫來檢查新的狀態，核取方塊。 如果已核取，則會顯示 [`Toast`](https://developer.xamarin.com/api/type/Android.Widget.Toast/)
顯示訊息"Selected"，否則它會顯示 [未選取]。 的 [`CheckBox`](https://developer.xamarin.com/api/type/Android.Widget.CheckBox/)
會處理它自己的狀態變更，因此您只需要查詢目前的狀態。

執行它。

**提示：** 如果您需要自行變更狀態 (例如當載入已儲存[ `CheckBoxPreference` ](https://developer.xamarin.com/api/type/Android.Preferences.CheckBoxPreference)，使用 [`Checked`](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked)
屬性 setter 或 [`Toggle()`](https://developer.xamarin.com/api/member/Android.Widget.CompoundButton.Toggle)
方法。

*此頁面上的部分是根據工作建立及 Android 的開放原始碼專案所共用，並依據所述的條款來使用修改*
[*Creative Commons 2.5 Attribution License*](http://creativecommons.org/licenses/by/2.5/).
