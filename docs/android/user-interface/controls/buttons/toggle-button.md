---
title: ToggleButton
ms.prod: xamarin
ms.assetid: 9ADA8FCF-63ED-897A-DD56-D7D86535A92C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: fe444d255beb9c08b4b5bcf5de36a8740e503b55
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029288"
---
# <a name="togglebutton"></a>ToggleButton

在本節中，您將建立一個按鈕，專門用來在兩個狀態之間切換（使用[`ToggleButton`](xref:Android.Widget.ToggleButton) widget）。 如果您有兩個互斥的簡單狀態（例如「開啟」和「關閉」），這個小工具就是選項按鈕的絕佳替代方式。 Android 4.0 （API 層級14）引進了切換按鈕的替代方式，稱為[`Switch`](xref:Android.Widget.Switch)。

您可以在左側影像中看到切換**按鈕**的範例，而右邊的影像則呈現**參數**的範例：

![開啟和關閉狀態的交換器和 ToggleButtons 範例](toggle-button-images/togglebutton-switch.png)  

應用程式使用哪一個控制項的樣式是很重要的。 這兩個 widget 的功能都相同。

開啟**Resources/layout/axml**檔案，並新增[`ToggleButton`](xref:Android.Widget.ToggleButton)元素（在[`LinearLayout`](xref:Android.Widget.LinearLayout)內）：

若要在狀態變更時執行某些動作，請將下列程式碼新增至[`OnCreate()`](xref:Android.App.Activity.OnCreate*)的結尾
方法

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

這會從配置中捕捉[`ToggleButton`](xref:Android.Widget.ToggleButton)專案，並處理 Click 事件，以定義按一下按鈕時要執行的動作。 在此範例中，方法會檢查按鈕的新狀態，然後顯示指出目前狀態的[`Toast`](xref:Android.Widget.Toast)訊息。

請注意， [`ToggleButton`](xref:Android.Widget.ToggleButton)會在 checked 和 unchecked 之間處理自己的狀態變更，因此您只需要詢問哪一個。

執行應用程式。

> [!TIP]
> 如果您需要自行變更狀態（例如載入已儲存的[`CheckBoxPreference`](xref:Android.Preferences.CheckBoxPreference)時），請使用[`Checked`](xref:Android.Widget.CompoundButton.Checked)
> 屬性 setter 或[`Toggle()`](xref:Android.Widget.CompoundButton.Toggle)
> 方法。

## <a name="related-links"></a>相關連結

- [按鈕](https://developer.android.com/reference/android/widget/ToggleButton.html)
- [開關](https://developer.android.com/reference/android/widget/Switch.html)
