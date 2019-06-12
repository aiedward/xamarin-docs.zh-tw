---
title: 滑桿、 交換器及在 Xamarin.iOS 中的分段的控制項
description: 本文件討論的投影片、 交換器和分段的控制項在 Xamarin.iOS，描述如何使用它們，以程式設計方式並在 iOS 設計工具中。
ms.prod: xamarin
ms.assetid: 85BF0EC8-E581-49CD-B9E7-98BE4C5A0F6B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 7b66005ff64763d68dc6101985e514fa56cf896d
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827371"
---
# <a name="sliders-switches-and-segmented-controls-in-xamarinios"></a>滑桿、 交換器及在 Xamarin.iOS 中的分段的控制項

<a name="Sliders" />

## <a name="sliders"></a>滑桿

滑桿控制項允許的範圍內的數字值的簡單的選取項目。 控制項的預設值為介於 0 和 1 之間的值，但您可以自訂這些限制。

 [![](slider-switch-segmented-controls-images/image25a.png "滑桿")](slider-switch-segmented-controls-images/image25a.png#lightbox)

下列螢幕擷取畫面顯示在設計工具中編輯的屬性：

 [![](slider-switch-segmented-controls-images/image26a.png "滑桿屬性")](slider-switch-segmented-controls-images/image25a.png#lightbox)

您可以在程式碼中設定這些值，如下所示，包括連接處理常式，以顯示目前選取的值在`UILabel`控制項：

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

自訂滑桿看起來像這樣：

 [![](slider-switch-segmented-controls-images/image27a.png "自訂滑桿")](slider-switch-segmented-controls-images/image28a.png#lightbox)

> [!IMPORTANT]
> 目前沒有[bug](https://stackoverflow.com/a/19496179)造成`ThumbTint`不在執行階段呈現如預期般運作。 您可以新增下列程式碼行**之前**上述程式碼因應措施。 [[Source](https://stackoverflow.com/a/21396794)]:
>
> `slider1.SetThumbImage(UIImage.FromBundle("thumb.png"),UIControlState.Normal);`
> 
> 您可以使用任何影像，它將會覆寫，但請確定它會放置_在_資源目錄，而且呼叫程式碼中。

<a name="Switch" />

## <a name="switch"></a>參數

iOS 使用`UISwitch`布林值的輸入，可能會由其他平台上使用選項按鈕。 使用者可以藉由移動操作控制項*thumb*之間**開/關**位置。

 [![](slider-switch-segmented-controls-images/image28a.png "交換器")](slider-switch-segmented-controls-images/image28a.png#lightbox)

可以以自訂參數的外觀**Properties Pad**設計工具，可讓您控制的預設狀態，**開/關濃淡**色彩和**on/off 映像**. 下圖所示：

 [![](slider-switch-segmented-controls-images/image29a.png "參數屬性")](slider-switch-segmented-controls-images/image29a.png#lightbox)

也可以在程式碼中設定參數的屬性，例如下列程式碼會顯示一個交換器的預設值以及`On`:

```csharp
switch1.On = true;
```

 <a name="Segmented_Controls" />


## <a name="segmented-controls"></a>區段控制項

分段控制項是組織的方式，以允許使用者與少數選項的互動。 它以水平方式配置和每個區段函式做為個別的按鈕。 當使用設計工具，可以找到分段控制項下**工具箱 > 控制項**，且看起來應該如下圖所示：

 [![](slider-switch-segmented-controls-images/segmentedcontrol.png "分段的控制項")](slider-switch-segmented-controls-images/segmentedcontrol.png#lightbox)

如下圖所示的設計介面上，個別選取每個區段可讓設計工具的一項獨特功能：

 [![](slider-switch-segmented-controls-images/segmentedcontrolselection.png "分段的控制項")](slider-switch-segmented-controls-images/segmentedcontrolselection.png#lightbox)

這可讓 [屬性] 面板，來更精確地控制每個區段的屬性。 您可以看到下面的螢幕擷取畫面中可編輯的屬性：

 [![](slider-switch-segmented-controls-images/segmentedcontrolproperties.png "分段的控制項")](slider-switch-segmented-controls-images/segmentedcontrolproperties.png#lightbox)

請注意，區隔市場的控制項樣式中的過時 iOS7，因此，調整此 iOS7 應用程式中的選項不會影響。

## <a name="related-links"></a>相關連結

- [控制項 （範例）](https://developer.xamarin.com/samples/monotouch/Controls/)
- [警示控制器](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller)
