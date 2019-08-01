---
title: 在 Xamarin 中的滑杆、切換和分段控制項
description: 本檔討論 Xamarin 中的投影片、切換和分段控制項, 並說明如何以程式設計方式和 iOS 設計工具來使用它們。
ms.prod: xamarin
ms.assetid: 85BF0EC8-E581-49CD-B9E7-98BE4C5A0F6B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: be4ae87600e533cdfdf39c204a8ef6af4682ec0c
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655764"
---
# <a name="sliders-switches-and-segmented-controls-in-xamarinios"></a>在 Xamarin 中的滑杆、切換和分段控制項

<a name="Sliders" />

## <a name="sliders"></a>滑杆

滑杆控制項可讓您簡單選擇範圍內的數值。 控制項預設為0到1之間的值, 但可以自訂這些限制。

 [![](slider-switch-segmented-controls-images/image25a.png "一直")](slider-switch-segmented-controls-images/image25a.png#lightbox)

下列螢幕擷取畫面顯示在設計工具中可編輯的屬性:

 [![](slider-switch-segmented-controls-images/image26a.png "滑杆屬性")](slider-switch-segmented-controls-images/image25a.png#lightbox)

您可以在程式碼中設定這些值, 如下所示, 包括連接處理常式以顯示`UILabel`控制項中目前選取的值:

```csharp
slider1.MinValue = -1;
slider1.MaxValue = 2;
slider1.Value = 0.5f; // the current value
slider1.ValueChanged += (sender,e) => label1.Text = ((UISlider)sender).Value.ToString ();
```

您也可以設定來自訂滑杆的視覺外觀。

```csharp
slider1.ThumbTintColor = UIColor.Blue;
slider1.MinimumTrackTintColor = UIColor.Gray;
slider1.MaximumTrackTintColor = UIColor.Green;
```

自訂的滑杆如下所示:

 [![](slider-switch-segmented-controls-images/image27a.png "自訂滑杆")](slider-switch-segmented-controls-images/image28a.png#lightbox)

> [!IMPORTANT]
> 目前有一個[bug](https://stackoverflow.com/a/19496179)導致`ThumbTint`不會如預期般在執行時間轉譯。 您可以在上述程式碼**之前**加入下列程式程式碼, 以作為因應措施。 [[來源](https://stackoverflow.com/a/21396794)]:
>
> `slider1.SetThumbImage(UIImage.FromBundle("thumb.png"),UIControlState.Normal);`
> 
> 您可以使用任何影像, 因為它會遭到覆寫, 但請務必將它放在 Resources 目錄_中_, 並在您的程式碼中呼叫。

<a name="Switch" />

## <a name="switch"></a>參數

iOS 會使用`UISwitch`做為布林值輸入, 在其他平臺上可以用選項按鈕表示。 使用者可以在**開啟/關閉**位置之間移動*滾動*盒, 藉此操作控制項。

 [![](slider-switch-segmented-controls-images/image28a.png "參數")](slider-switch-segmented-controls-images/image28a.png#lightbox)

參數的外觀可以在設計工具的**Properties Pad**中自訂, 這可讓您控制預設狀態、**開啟/關閉色調**色彩和**開啟/關閉影像**。 如下圖所示:

 [![](slider-switch-segmented-controls-images/image29a.png "切換屬性")](slider-switch-segmented-controls-images/image29a.png#lightbox)

參數的屬性也可以在程式碼中設定, 例如, 下列程式碼會顯示參數的預設值`On`:

```csharp
switch1.On = true;
```

 <a name="Segmented_Controls" />


## <a name="segmented-controls"></a>區段控制項

分段的控制項是一種可讓使用者與少數選項互動的組織方式。 它會水準配置, 而每個區段會當做個別的按鈕來運作。 使用設計工具時, 可以在 [**工具箱] > 控制項**底下找到分割的控制項, 看起來應該如下圖所示:

 [![](slider-switch-segmented-controls-images/segmentedcontrol.png "分段的控制項")](slider-switch-segmented-controls-images/segmentedcontrol.png#lightbox)

設計工具的一項獨特功能, 可讓您在設計介面上個別選取每個區段, 如下所示:

 [![](slider-switch-segmented-controls-images/segmentedcontrolselection.png "分段的控制項")](slider-switch-segmented-controls-images/segmentedcontrolselection.png#lightbox)

這可讓 Properties Pad 用來更精確地控制每個區段的屬性。 您可以在下列螢幕擷取畫面中看到可編輯的屬性:

 [![](slider-switch-segmented-controls-images/segmentedcontrolproperties.png "分段的控制項")](slider-switch-segmented-controls-images/segmentedcontrolproperties.png#lightbox)

請注意, 分段控制項樣式在 iOS7 中已被取代, 因此在 iOS7 應用程式中調整這個的選項不會有任何作用。

## <a name="related-links"></a>相關連結

- [控制項 (範例)](https://docs.microsoft.com/samples/xamarin/ios-samples/controls)
- [警示控制器](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller)
