---
title: RadioButton
ms.prod: xamarin
ms.assetid: 3C32EA3F-D917-C988-72C5-A17354DA791E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: be473580b24dba6b4f08384771e2097d368f8dc8
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61276592"
---
# <a name="radiobutton"></a>RadioButton

在本節中，您將建立 （啟用一個停用其他） 的兩個互斥的選項按鈕，使用 [`RadioGroup`](https://developer.xamarin.com/api/type/Android.Widget.RadioGroup/)
和 [`RadioButton`](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)
小工具。 按下其中一個選項按鈕時，就會顯示快顯通知訊息。


開啟**Resources/layout/Main.axml**檔案，並新增兩個[ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)s，巢狀方式置於[ `RadioGroup` ](https://developer.xamarin.com/api/type/Android.Widget.RadioGroup/) (內[ `LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)):

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

很重要， [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)s 會群組在一起[ `RadioGroup` ](https://developer.xamarin.com/api/type/Android.Widget.RadioGroup/)項目，因此可以一次選取不超過一個。 Android 的系統會自動處理此邏輯。 當一 [`RadioButton`](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)
在選取的群組，所有其他項目的會自動取消選取。

若要執行一些作業時每個[ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)已選取，我們必須撰寫事件處理常式：

```csharp
private void RadioButtonClick (object sender, EventArgs e)
{
    RadioButton rb = (RadioButton)sender;
    Toast.MakeText (this, rb.Text, ToastLength.Short).Show ();
}
```

首先，會傳入之寄件者會轉換成 RadioButton。
然後 [`Toast`](https://developer.xamarin.com/api/type/Android.Widget.Toast/)
訊息會顯示已選取的選項按鈕的文字。

現在，在底部 [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle)
方法中，新增下列：

```csharp
RadioButton radio_red = FindViewById<RadioButton>(Resource.Id.radio_red);
RadioButton radio_blue = FindViewById<RadioButton>(Resource.Id.radio_blue);

radio_red.Click += RadioButtonClick;
radio_blue.Click += RadioButtonClick;
```

這會擷取每個[ `RadioButton`](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)從版面配置，並將新建立的事件 handlerto 每個。

執行應用程式。

**提示：** 如果您需要自行變更狀態 (例如當載入已儲存[ `CheckBoxPreference` ](https://developer.xamarin.com/api/type/Android.Preferences.CheckBoxPreference/))，使用 [`Checked`](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked/)
屬性 setter 或 [`Toggle()`](https://developer.xamarin.com/api/member/Android.Widget.CompoundButton.Toggle/)
方法。

*此頁面上的部分是根據工作建立及 Android 的開放原始碼專案所共用，並依據所述的條款來使用修改*
[*Creative Commons 2.5 Attribution License*](http://creativecommons.org/licenses/by/2.5/). 
