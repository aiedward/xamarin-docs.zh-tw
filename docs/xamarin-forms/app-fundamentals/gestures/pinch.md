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
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656036"
---
# <a name="adding-a-pinch-gesture-recognizer"></a>新增捏合手勢辨識器

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-pinchgesture)

_捏合手勢用於執行互動式縮放，由 PinchGestureRecognizer 類別實作。捏合手勢的常見案例是在捏合位置執行影像的互動式縮放。這可透過在檢視區的內容中調整影像來完成，如本文所示。_

若要讓使用者介面項目可透過捏合手勢縮放，請建立 [`PinchGestureRecognizer`](xref:Xamarin.Forms.PinchGestureRecognizer) 執行個體、處理 [`PinchUpdated`](xref:Xamarin.Forms.PinchGestureRecognizer.PinchUpdated) 事件，然後將新手勢辨識器新增至使用者介面項目的 [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) 集合。 下列程式碼範例顯示附加至 [`Image`](xref:Xamarin.Forms.Image) 項目的 `PinchGestureRecognizer`：

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

您可以在使用者介面項目周圍包裝此類別，讓捏合手勢縮放包裝的使用者介面項目。 下列 XAML 程式碼範例示範 `PinchToZoomContainer` 如何包裝 [`Image`](xref:Xamarin.Forms.Image) 項目：

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

下列程式碼範例示範 `PinchToZoomContainer` 如何在 C# 頁面中包裝 [`Image`](xref:Xamarin.Forms.Image) 項目：

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

當 [`Image`](xref:Xamarin.Forms.Image) 項目接收到捏合手勢時，則會放大或縮小顯示的影像。縮放由 `PinchZoomContainer.OnPinchUpdated` 方法執行，如下列程式碼範例所示：

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

此方法會根據使用者的捏合手勢，更新包裝使用者介面項目的縮放等級。 這會透過使用 [`PinchGestureUpdatedEventArgs`](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs) 執行個體的 [`Scale`](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.Scale)、[`ScaleOrigin`](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.ScaleOrigin) 和 [`Status`](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.Status) 屬性值，計算要套用至捏合手勢原點的比例因素來完成。 包裝的使用者項目會接著透過將其 [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)、[`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) 和 [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) 屬性設定為計算值，在捏合手勢原點進行縮放。

## <a name="related-links"></a>相關連結

- [PinchGesture (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-pinchgesture)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [PinchGestureRecognizer](xref:Xamarin.Forms.PinchGestureRecognizer)
