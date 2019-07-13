---
title: ToggleButton
ms.prod: xamarin
ms.assetid: 9ADA8FCF-63ED-897A-DD56-D7D86535A92C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: a22d274feb5539164663ac0c48e5a84bdf5d2c66
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830234"
---
# <a name="togglebutton"></a>ToggleButton

在本節中，您將建立專門用於使用的兩個狀態之間切換 按鈕[ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/)小工具。 此 widget 是選項按鈕的絕佳替代方式，如果您有兩個互斥的簡單狀態 (「 開啟 」 和 「 關閉 」，例如)。 Android 4.0 （API 層級為 14） 導入的替代項目稱為 [切換] 按鈕[ `Switch` ](https://developer.xamarin.com/api/type/Android.Widget.Switch/)。

舉例**ToggleButton**中可以看到左側配對的映像，而映像的右手邊組提供的範例**交換器**:

![參數和 ToggleButtons 內部和外部狀態中的範例](toggle-button-images/togglebutton-switch.png)  

應用程式使用哪一個控制項是樣式問題。 這兩個小工具的功能相同。

開啟**Resources/layout/Main.axml**檔案，並新增[ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/)項目 (內[ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)):

變更狀態時，請執行一些作業，請將下列程式碼新增至結尾 [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle)
方法：

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

這會擷取[ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/)項目在版面配置，並處理 Click 事件，定義要在按一下按鈕時執行的動作。 在此範例中，此方法會檢查新狀態的按鈕，則會顯示[ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/)訊息，指出目前的狀態。

請注意， [ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/)自己的狀態變更之間 checked 與 unchecked，讓您只要求它的控制代碼。

執行應用程式。


> [!TIP]
> 如果您需要自行變更狀態 (例如當載入已儲存[ `CheckBoxPreference` ](https://developer.xamarin.com/api/type/Android.Preferences.CheckBoxPreference/))，使用 [`Checked`](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked/)
> 屬性 setter 或 [`Toggle()`](https://developer.xamarin.com/api/member/Android.Widget.CompoundButton.Toggle/)
> 方法。


## <a name="related-links"></a>相關連結

- [ToggleButton](https://developer.android.com/reference/android/widget/ToggleButton.html)
- [開關](https://developer.android.com/reference/android/widget/Switch.html)
