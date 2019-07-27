---
title: RadioButton
ms.prod: xamarin
ms.assetid: 3C32EA3F-D917-C988-72C5-A17354DA791E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 2279282b08c9d97b239de424cf38aa6f1463dc4d
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510359"
---
# <a name="radiobutton"></a>RadioButton

在本節中, 您將建立兩個互斥的選項按鈕 (啟用其中一個), 其方式是使用[`RadioGroup`](xref:Android.Widget.RadioGroup)
和[`RadioButton`](xref:Android.Widget.RadioButton)
部件. 按任一選項按鈕時, 將會顯示快顯訊息。


開啟**Resources/layout/axml**檔案, 並新增兩個[`RadioButton`](xref:Android.Widget.RadioButton) [`RadioGroup`](xref:Android.Widget.RadioGroup) , 並在中 (位於[`LinearLayout`](xref:Android.Widget.LinearLayout)) 內:

```xml
<RadioGroup
  android:layout_width="fill_parent"
  android:layout_height="wrap_content"
  android:orientation="vertical">
  <RadioButton android:id="@+id/radio_red"
      android:layout_width="wrap_content"
      android:layout_height="wrap_content"
      android:text="Red" />
  <RadioButton android:id="@+id/radio_blue"
      android:layout_width="wrap_content"
      android:layout_height="wrap_content"
      android:text="Blue" />
</RadioGroup>
```

請務必[`RadioButton`](xref:Android.Widget.RadioButton) [`RadioGroup`](xref:Android.Widget.RadioGroup)依元素群組在一起, 以便一次只能選取一個專案。 這項邏輯會由 Android 系統自動處理。 當其中一個[`RadioButton`](xref:Android.Widget.RadioButton)
在選取的群組中, 會自動取消選取所有其他專案。

若要在選取每[`RadioButton`](xref:Android.Widget.RadioButton)個時執行某些動作, 我們需要撰寫事件處理常式:

```csharp
private void RadioButtonClick (object sender, EventArgs e)
{
    RadioButton rb = (RadioButton)sender;
    Toast.MakeText (this, rb.Text, ToastLength.Short).Show ();
}
```

首先, 傳入的寄件者會轉換成選項按鈕。
然後是[`Toast`](xref:Android.Widget.Toast)
訊息會顯示選取的選項按鈕的文字。

現在, 在[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
方法中, 新增下列內容:

```csharp
RadioButton radio_red = FindViewById<RadioButton>(Resource.Id.radio_red);
RadioButton radio_blue = FindViewById<RadioButton>(Resource.Id.radio_blue);

radio_red.Click += RadioButtonClick;
radio_blue.Click += RadioButtonClick;
```

這會從配置中[`RadioButton`](xref:Android.Widget.RadioButton)捕捉每個, 並每個新增新建立的事件 handlerto。

執行應用程式。

> [!TIP]
> 如果您需要自行變更狀態 (例如載入儲存[`CheckBoxPreference`](xref:Android.Preferences.CheckBoxPreference)的時), 請使用[`Checked`](xref:Android.Widget.CompoundButton.Checked)
> 屬性 setter 或[`Toggle()`](xref:Android.Widget.CompoundButton.Toggle)
> 方法。

*此頁面的部分是根據 Android 開放原始碼專案所建立和共用的工作進行修改, 並根據*
[*創意 Commons 2.5 屬性授權*](http://creativecommons.org/licenses/by/2.5/)中所述的條款來使用。 
