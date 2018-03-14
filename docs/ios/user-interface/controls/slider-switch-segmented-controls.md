---
title: "滑桿、 交換器及分割的控制項"
ms.topic: article
ms.prod: xamarin
ms.assetid: 85BF0EC8-E581-49CD-B9E7-98BE4C5A0F6B
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 282a4cb59545703c5172f8747cb5b633e7b648dc
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="sliders-switches-and-segmented-controls"></a>滑桿、 交換器及分割的控制項

<a name="Sliders" />


## <a name="sliders"></a>滑桿

滑桿控制項允許的範圍內的數字值的簡單的選取項目。 控制項的預設值為 0 到 1 之間的值，但您可以自訂這些限制。

 [![](slider-switch-segmented-controls-images/image25a.png "Slider")](slider-switch-segmented-controls-images/image25a.png#lightbox)

下列螢幕擷取畫面會顯示在設計工具中編輯的屬性：

 [![](slider-switch-segmented-controls-images/image26a.png "滑桿屬性")](slider-switch-segmented-controls-images/image25a.png#lightbox)

您可以在程式碼中設定這些值，如下所示，包括以顯示目前選取的值中的處理常式連接`UILabel`控制項：

```csharp
slider1.MinValue = -1;
slider1.MaxValue = 2;
slider1.Value = 0.5f; // the current value
slider1.ValueChanged += (sender,e) => label1.Text = ((UISlider)sender).Value.ToString ();
```

您也可以自訂設定滑桿的視覺外觀

```csharp
slider1.ThumbTintColor = UIColor.Blue;
slider1.MinimumTrackTintColor = UIColor.Gray;
slider1.MaximumTrackTintColor = UIColor.Green;
```

自訂的滑桿看起來像這樣：

 [![](slider-switch-segmented-controls-images/image27a.png "自訂的滑桿")](slider-switch-segmented-controls-images/image28a.png#lightbox)

> [!IMPORTANT]
> 目前沒有[bug](http://stackoverflow.com/a/19496179)造成`ThumbTint`未如預期般呈現在執行階段。 您可以加入下列程式碼行**之前**上方的程式碼，以解決這個問題。 [[來源](http://stackoverflow.com/a/21396794)]:
>
> `slider1.SetThumbImage(UIImage.FromBundle("thumb.png"),UIControlState.Normal);`
> 
> 您可以使用任何影像，它將會覆寫，但請確定它用來放置_中_資源目錄，而且呼叫程式碼中。

<a name="Switch" />

## <a name="switch"></a>參數

使用 iOS`UISwitch`布林值的輸入，可能會由其他平台上使用選項按鈕。 使用者可以藉由移動操作控制項*thumb*之間**開/關**位置。

 [![](slider-switch-segmented-controls-images/image28a.png "Switch")](slider-switch-segmented-controls-images/image28a.png#lightbox)

參數的外觀，可以以自訂**屬性板**設計工具，可讓您控制的預設狀態，**開/關濃淡**色彩和**on/off 映像**. 下圖所示：

 [![](slider-switch-segmented-controls-images/image29a.png "參數屬性")](slider-switch-segmented-controls-images/image29a.png#lightbox)

也可以在程式碼中設定參數的屬性，例如下列程式碼會顯示一個交換器的預設值以及`On`:

```csharp
switch1.On = true;
```

 <a name="Segmented_Controls" />


## <a name="segmented-controls"></a>分割的控制項

分割控制項是組織的方式，可讓使用者互動少數的選項。 在水平配置和每個區段會做為獨立的按鈕。 分割的控制項時使用設計工具，可以找到下**工具箱 > 控制項**，並看起來應該像下圖：

 [![](slider-switch-segmented-controls-images/segmentedcontrol.png "分割的控制項")](slider-switch-segmented-controls-images/segmentedcontrol.png#lightbox)

如下所示的設計介面上，個別選取每個區段可讓設計工具的一項獨特功能：

 [![](slider-switch-segmented-controls-images/segmentedcontrolselection.png "分割的控制項")](slider-switch-segmented-controls-images/segmentedcontrolselection.png#lightbox)

這可讓屬性板，用來更精確地控制每個區段的屬性。 您可以看到下面的螢幕擷取畫面中可編輯的屬性：

 [![](slider-switch-segmented-controls-images/segmentedcontrolproperties.png "分割的控制項")](slider-switch-segmented-controls-images/segmentedcontrolproperties.png#lightbox)

您應該注意中 iOS7，已被取代分割控制項樣式，因此，調整此 iOS7 應用程式中的選項不會影響。

## <a name="related-links"></a>相關連結

- [控制項 （範例）](https://developer.xamarin.com/samples/Controls/)
- [警示的控制站](https://developer.xamarin.com/recipes/ios/standard_controls/alertcontroller/)
