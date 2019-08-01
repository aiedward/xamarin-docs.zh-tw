---
title: 核取方塊
ms.prod: xamarin
ms.assetid: A884AF10-D5EA-72CA-2301-B80CEC7FFBE7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: f6f594d86cab8b1173ee9f67402862e1ec2890b2
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510364"
---
# <a name="checkbox"></a>核取方塊

在本節中, 您將建立一個核取方塊來選取專案, 並使用[`CheckBox`](xref:Android.Widget.CheckBox)
機械. 當按下核取方塊時, 快顯訊息會指出核取方塊的目前狀態。

開啟**Resources/layout/axml**檔案, 並新增[`CheckBox`](xref:Android.Widget.CheckBox)專案 (在內[`LinearLayout`](xref:Android.Widget.LinearLayout)):

```xml
<CheckBox android:id="@+id/checkbox"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="check it out" />
```

若要在狀態變更時執行某些動作, 請將下列程式碼新增至結尾[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
方法

```csharp
CheckBox checkbox = FindViewById<CheckBox>(Resource.Id.checkbox);

checkbox.Click += (o, e) => {
    if (checkbox.Checked)
        Toast.MakeText (this, "Selected", ToastLength.Short).Show ();
    else
        Toast.MakeText (this, "Not selected", ToastLength.Short).Show ();
};
```

這會捕捉[`CheckBox`](xref:Android.Widget.CheckBox)
來自配置的專案, 然後處理 Click 事件, 這會定義按一下核取方塊時要進行的動作。 按一下時,[`Checked`](xref:Android.Widget.CompoundButton.Checked)
呼叫屬性以檢查核取方塊的新狀態。 如果已核取, 則為[`Toast`](xref:Android.Widget.Toast)
顯示「已選取」訊息, 否則會顯示「未選取」。 該[`CheckBox`](xref:Android.Widget.CheckBox)
會處理自己的狀態變更, 因此您只需要查詢目前的狀態。

執行它。

> [!TIP]
> 如果您需要自行變更狀態 (例如載入已儲存[`CheckBoxPreference`](xref:Android.Preferences.CheckBoxPreference)的時), 請使用[`Checked`](xref:Android.Widget.CompoundButton.Checked)
> 屬性 setter 或[`Toggle()`](xref:Android.Widget.CompoundButton.Toggle)
> 方法。

*此頁面的部分是根據 Android 開放原始碼專案所建立和共用的工作進行修改, 並根據*
[*創意 Commons 2.5 屬性授權*](http://creativecommons.org/licenses/by/2.5/)中所述的條款來使用。
