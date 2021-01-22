---
title: Xamarin 中的滑杆、交換器和分段的控制項
description: 本檔討論在 Xamarin 中的投影片、開關和分段的控制項，並說明如何以程式設計方式和在 iOS 設計工具中使用它們。
ms.prod: xamarin
ms.assetid: 85BF0EC8-E581-49CD-B9E7-98BE4C5A0F6B
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 65fd825b6858efa02fcada196b4229cc69313b3e
ms.sourcegitcommit: 513feb0e07558766e3de4a898e53d56b27c20559
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98697484"
---
# <a name="sliders-switches-and-segmented-controls-in-xamarinios"></a>Xamarin 中的滑杆、交換器和分段的控制項

<a name="Sliders"></a>

## <a name="sliders"></a>滑桿

滑杆控制項可讓您在範圍內簡單選取數值。 控制項預設為介於0和1之間的值，但可以自訂這些限制。

 [![滑桿](slider-switch-segmented-controls-images/image25a.png)](slider-switch-segmented-controls-images/image25a.png#lightbox)

下列螢幕擷取畫面顯示可在設計工具中編輯的屬性：

 [![滑杆屬性](slider-switch-segmented-controls-images/image26a.png)](slider-switch-segmented-controls-images/image25a.png#lightbox)

您可以在程式碼中設定這些值（如下所示），包括連接處理常式以顯示控制項中目前選取的值 `UILabel` ：

```csharp
slider1.MinValue = -1;
slider1.MaxValue = 2;
slider1.Value = 0.5f; // the current value
slider1.ValueChanged += (sender,e) => label1.Text = ((UISlider)sender).Value.ToString ();
```

您也可以藉由設定來自訂滑杆的視覺外觀

```csharp
slider1.ThumbTintColor = UIColor.Blue;
slider1.MinimumTrackTintColor = UIColor.Gray;
slider1.MaximumTrackTintColor = UIColor.Green;
```

自訂的滑杆看起來像這樣：

 [![自訂滑杆](slider-switch-segmented-controls-images/image27a.png)](slider-switch-segmented-controls-images/image28a.png#lightbox)

> [!IMPORTANT]
> 目前有一個 [錯誤](https://stackoverflow.com/a/19496179) ，導致在 `ThumbTint` 執行時間不會如預期般呈現。 您可以在上述程式碼 **之前** 新增下面這行程式碼，作為因應措施。 [[來源](https://stackoverflow.com/a/21396794)]：
>
> `slider1.SetThumbImage(UIImage.FromBundle("thumb.png"),UIControlState.Normal);`
> 
> 您可以使用任何影像，因為它會被覆寫，但請確定它是放置在資原始目錄 _中_ ，並在您的程式碼中呼叫。

<a name="Switch"></a>

## <a name="switch"></a>參數

iOS 會使用 `UISwitch` 做為布林輸入，可在其他平臺上以選項按鈕表示。 使用者可以藉由 **在開啟/關閉** 位置之間移動 *滾動* 盒來操作控制項。

 [![開關](slider-switch-segmented-controls-images/image28a.png)](slider-switch-segmented-controls-images/image28a.png#lightbox)

您可以在設計工具的 **Properties Pad** 中自訂切換的外觀，讓您控制預設狀態、 **開啟/關閉色調** 色彩以及 **開啟/關閉影像**。 如下圖所示：

 [![切換屬性](slider-switch-segmented-controls-images/image29a.png)](slider-switch-segmented-controls-images/image29a.png#lightbox)

您也可以在程式碼中設定參數的屬性，例如，下列程式碼將會顯示參數，其預設值為 `On` ：

```csharp
switch1.On = true;
```

 <a name="Segmented_Controls"></a>

## <a name="segmented-controls"></a>區段控制項

分割的控制項是一種有條理的方式，可讓使用者與少量的選項互動。 它會水準配置，且每個區段都會作為個別按鈕。 使用設計工具時，可以在 [ **工具箱] > 控制項** 下找到分割的控制項，看起來應該如下圖所示：

 [![分割的控制項圖示。](slider-switch-segmented-controls-images/segmentedcontrol.png)](slider-switch-segmented-controls-images/segmentedcontrol.png#lightbox)

設計工具的一項獨特功能，可讓您在設計介面上個別選取每個區段，如下所示：

 [![個別選取分割的控制項區段。](slider-switch-segmented-controls-images/segmentedcontrolselection.png)](slider-switch-segmented-controls-images/segmentedcontrolselection.png#lightbox)

這可讓 Properties Pad 用來更精確地控制每個區段的屬性。 您可以在下列螢幕擷取畫面中看到可編輯的屬性：

 [![分割的控制項屬性。](slider-switch-segmented-controls-images/segmentedcontrolproperties.png)](slider-switch-segmented-controls-images/segmentedcontrolproperties.png#lightbox)

請注意，分割的控制項樣式在 iOS7 中已被取代，因此在 iOS7 應用程式中調整這項功能的選項將不會有任何作用。

## <a name="related-links"></a>相關連結

- [控制項 (範例) ](/samples/xamarin/ios-samples/controls)
- [警示控制器](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller)