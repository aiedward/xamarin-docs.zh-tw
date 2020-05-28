---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: da4a8bc66a7986efd3683de6dce1f6af618b85cc
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137848"
---
# <a name="adding-a-pinch-gesture-recognizer"></a>新增捏合手勢辨識器

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-pinchgesture)

_縮小手勢是用來執行互動式縮放，並使用 PinchGestureRecognizer 類別實作為。縮小手勢的常見案例是在縮小位置執行影像的互動式縮放。這是藉由調整視口的內容來完成，並在本文中示範。_

若要讓使用者介面專案以縮小手勢可縮放，請建立 [`PinchGestureRecognizer`](xref:Xamarin.Forms.PinchGestureRecognizer) 實例、處理 [`PinchUpdated`](xref:Xamarin.Forms.PinchGestureRecognizer.PinchUpdated) 事件，並將新的手勢辨識器新增至 [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) 使用者介面專案上的集合。 下列程式碼範例顯示 `PinchGestureRecognizer` 附加至元素的 [`Image`](xref:Xamarin.Forms.Image) ：

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

您可以在使用者介面項目周圍包裝此類別，讓捏合手勢縮放包裝的使用者介面項目。 下列 XAML 程式碼範例示範如何 `PinchToZoomContainer` 包裝 [`Image`](xref:Xamarin.Forms.Image) 元素：

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

下列程式碼範例顯示如何 `PinchToZoomContainer` [`Image`](xref:Xamarin.Forms.Image) 在 c # 頁面中包裝專案：

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

當 [`Image`](xref:Xamarin.Forms.Image) 元素收到縮小的手勢時，顯示的影像將會放大或縮小。縮放是由 `PinchZoomContainer.OnPinchUpdated` 方法執行，如下列程式碼範例所示：

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

此方法會根據使用者的捏合手勢，更新包裝使用者介面項目的縮放等級。 這是藉由使用 [`Scale`](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.Scale) 實例的、 [`ScaleOrigin`](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.ScaleOrigin) 和屬性值 [`Status`](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.Status) [`PinchGestureUpdatedEventArgs`](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs) 來計算要在縮小手勢原點套用的縮放因數。 包裝的使用者專案接著會將其 [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) 、 [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) 和 [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) 屬性設定為計算值，藉此在縮小手勢的原點縮放。

## <a name="related-links"></a>相關連結

- [PinchGesture (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-pinchgesture)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [PinchGestureRecognizer](xref:Xamarin.Forms.PinchGestureRecognizer)
