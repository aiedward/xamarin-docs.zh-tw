---
title: 新增捏合手勢辨識器
description: 本文說明如何使用捏合手勢，在捏合位置執行影像的互動式縮放。
ms.prod: xamarin
ms.assetid: 832F7810-F0CF-441A-B04A-3975F3FB8B29
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: be7a145e93aa4720b38921efc895ca3f3f33edb3
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "68656036"
---
# <a name="adding-a-pinch-gesture-recognizer"></a>新增捏合手勢辨識器

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-pinchgesture)

_捏合手勢用於執行互動式縮放,並與 PinchGesture 識別器類一起實現。捏合手勢的常見方案是在捏合位置執行圖像的互動式縮放。這是通過縮放視口的內容來實現的,本文演示了這一點。_

要使用捏合手勢使用戶介面元素可縮放,請建立實例[`PinchGestureRecognizer`](xref:Xamarin.Forms.PinchGestureRecognizer)、處理[`PinchUpdated`](xref:Xamarin.Forms.PinchGestureRecognizer.PinchUpdated)事件,並將新的手勢識別器添加到[`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers)使用者介面元素的集合中。 以下代碼範例顯示`PinchGestureRecognizer`附加到元素的[`Image`](xref:Xamarin.Forms.Image):

```csharp
var pinchGesture = new PinchGestureRecognizer();
pinchGesture.PinchUpdated += (s, e) => {
  // Handle the pinch
};
image.GestureRecognizers.Add(pinchGesture);
```

這也可在 XAML 中完成，如下列程式碼範例所示：

```xaml
<Image Source="waterfront.jpg">
  <Image.GestureRecognizers>
    <PinchGestureRecognizer PinchUpdated="OnPinchUpdated" />
  </Image.GestureRecognizers>
</Image>
```

`OnPinchUpdated` 事件處理常式的程式碼會接著新增至程式碼後置檔案：

```csharp
void OnPinchUpdated (object sender, PinchGestureUpdatedEventArgs e)
{
  // Handle the pinch
}
```

## <a name="creating-a-pinchtozoom-container"></a>建立 PinchToZoom 容器

處理捏合手勢來執行縮放作業，需要特定數學公式才能轉換使用者介面。 本節包含一般化的協助程式類別來執行數學運算，可用於以互動方式縮放任何使用者介面項目。 下列程式碼範例顯示 `PinchToZoomContainer` 類別：

```csharp
public class PinchToZoomContainer : ContentView
{
  ...

  public PinchToZoomContainer ()
  {
    var pinchGesture = new PinchGestureRecognizer ();
    pinchGesture.PinchUpdated += OnPinchUpdated;
    GestureRecognizers.Add (pinchGesture);
  }

  void OnPinchUpdated (object sender, PinchGestureUpdatedEventArgs e)
  {
    ...
  }
}
```

您可以在使用者介面項目周圍包裝此類別，讓捏合手勢縮放包裝的使用者介面項目。 下面的 XAML 代碼範例`PinchToZoomContainer`顯示了[`Image`](xref:Xamarin.Forms.Image)換行 元素:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:PinchGesture;assembly=PinchGesture"
             x:Class="PinchGesture.HomePage">
    <ContentPage.Content>
        <Grid Padding="20">
            <local:PinchToZoomContainer>
                <local:PinchToZoomContainer.Content>
                    <Image Source="waterfront.jpg" />
                </local:PinchToZoomContainer.Content>
            </local:PinchToZoomContainer>
        </Grid>
    </ContentPage.Content>
</ContentPage>
```

以下代碼範例顯示如何在 C#`PinchToZoomContainer`[`Image`](xref:Xamarin.Forms.Image)頁 中換行元素:

```csharp
public class HomePageCS : ContentPage
{
  public HomePageCS ()
  {
    Content = new Grid {
      Padding = new Thickness (20),
      Children = {
        new PinchToZoomContainer {
          Content = new Image { Source = ImageSource.FromFile ("waterfront.jpg") }
        }
      }
    };
  }
}
```

當[`Image`](xref:Xamarin.Forms.Image)元素收到捏合手勢時,顯示的圖像將放大或縮小。縮放由`PinchZoomContainer.OnPinchUpdated`方法執行,如下代碼範例所示:

```csharp
void OnPinchUpdated (object sender, PinchGestureUpdatedEventArgs e)
{
  if (e.Status == GestureStatus.Started) {
    // Store the current scale factor applied to the wrapped user interface element,
    // and zero the components for the center point of the translate transform.
    startScale = Content.Scale;
    Content.AnchorX = 0;
    Content.AnchorY = 0;
  }
  if (e.Status == GestureStatus.Running) {
    // Calculate the scale factor to be applied.
    currentScale += (e.Scale - 1) * startScale;
    currentScale = Math.Max (1, currentScale);

    // The ScaleOrigin is in relative coordinates to the wrapped user interface element,
    // so get the X pixel coordinate.
    double renderedX = Content.X + xOffset;
    double deltaX = renderedX / Width;
    double deltaWidth = Width / (Content.Width * startScale);
    double originX = (e.ScaleOrigin.X - deltaX) * deltaWidth;

    // The ScaleOrigin is in relative coordinates to the wrapped user interface element,
    // so get the Y pixel coordinate.
    double renderedY = Content.Y + yOffset;
    double deltaY = renderedY / Height;
    double deltaHeight = Height / (Content.Height * startScale);
    double originY = (e.ScaleOrigin.Y - deltaY) * deltaHeight;

    // Calculate the transformed element pixel coordinates.
    double targetX = xOffset - (originX * Content.Width) * (currentScale - startScale);
    double targetY = yOffset - (originY * Content.Height) * (currentScale - startScale);

    // Apply translation based on the change in origin.
    Content.TranslationX = targetX.Clamp (-Content.Width * (currentScale - 1), 0);
    Content.TranslationY = targetY.Clamp (-Content.Height * (currentScale - 1), 0);

    // Apply scale factor.
    Content.Scale = currentScale;
  }
  if (e.Status == GestureStatus.Completed) {
    // Store the translation delta's of the wrapped user interface element.
    xOffset = Content.TranslationX;
    yOffset = Content.TranslationY;
  }
}
```

此方法會根據使用者的捏合手勢，更新包裝使用者介面項目的縮放等級。 這是[`Scale`](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.Scale)通過使用[`ScaleOrigin`](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.ScaleOrigin)實例[`Status`](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.Status)[`PinchGestureUpdatedEventArgs`](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs)的值和 實例的屬性來計算要在捏合手勢的原點應用的比例因子來實現的。 然後,通過將其[`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)的和[`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)[`Scale`](xref:Xamarin.Forms.VisualElement.Scale)屬性設置為計算值,在捏合手勢的原點縮放已包裝的使用者元素。

## <a name="related-links"></a>相關連結

- [PinchGesture (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-pinchgesture)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [PinchGestureRecognizer](xref:Xamarin.Forms.PinchGestureRecognizer)
