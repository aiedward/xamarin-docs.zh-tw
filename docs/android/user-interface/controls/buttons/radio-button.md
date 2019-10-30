---
title: RadioButton
ms.prod: xamarin
ms.assetid: 3C32EA3F-D917-C988-72C5-A17354DA791E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 9f51adcbd1accb4f780318cc0853e612ed8e5bed
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029307"
---
# <a name="radiobutton"></a>RadioButton

在本節中，您將建立兩個互斥的選項按鈕（啟用一個），使用[`RadioGroup`](xref:Android.Widget.RadioGroup)
和[`RadioButton`](xref:Android.Widget.RadioButton)
部件. 按任一選項按鈕時，將會顯示快顯訊息。

開啟**Resources/layout/axml**檔案，然後加入[`RadioGroup`](xref:Android.Widget.RadioGroup) （在[`LinearLayout`](xref:Android.Widget.LinearLayout)內）中的兩個[`RadioButton`](xref:Android.Widget.RadioButton)s：

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

請務必將[`RadioButton`](xref:Android.Widget.RadioButton) [`RadioGroup`](xref:Android.Widget.RadioGroup)元素群組在一起，以便一次只能選取一個專案。 這項邏輯會由 Android 系統自動處理。 當一個[`RadioButton`](xref:Android.Widget.RadioButton)
在選取的群組中，會自動取消選取所有其他專案。

若要在選取每個[`RadioButton`](xref:Android.Widget.RadioButton)時執行某些動作，我們需要撰寫事件處理常式：

```csharp
private void RadioButtonClick (object sender, EventArgs e)
{
    RadioButton rb = (RadioButton)sender;
    Toast.MakeText (this, rb.Text, ToastLength.Short).Show ();
}
```

首先，傳入的寄件者會轉換成選項按鈕。
然後[`Toast`](xref:Android.Widget.Toast)
訊息會顯示選取的選項按鈕的文字。

現在，在[`OnCreate()`](xref:Android.App.Activity.OnCreate*)的底部
方法中，新增下列內容：

```csharp
RadioButton radio_red = FindViewById<RadioButton>(Resource.Id.radio_red);
RadioButton radio_blue = FindViewById<RadioButton>(Resource.Id.radio_blue);

radio_red.Click += RadioButtonClick;
radio_blue.Click += RadioButtonClick;
```

這會從配置中捕捉每個[`RadioButton`](xref:Android.Widget.RadioButton)，並加入新建立的事件 handlerto。

執行應用程式。

> [!TIP]
> 如果您需要自行變更狀態（例如載入已儲存的[`CheckBoxPreference`](xref:Android.Preferences.CheckBoxPreference)時），請使用[`Checked`](xref:Android.Widget.CompoundButton.Checked)
> 屬性 setter 或[`Toggle()`](xref:Android.Widget.CompoundButton.Toggle)
> 方法。

*此頁面的部分是根據 Android 開放原始碼專案所建立和共用的工作進行修改，並根據*
[*創意 Commons 2.5 屬性授權*](https://creativecommons.org/licenses/by/2.5/)中所述的條款來使用。 
