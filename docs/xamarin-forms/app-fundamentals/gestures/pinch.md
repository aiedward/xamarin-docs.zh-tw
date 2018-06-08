---
title: 加入捏合筆勢辨識器
description: 捏合手勢用來執行互動式縮放，並且與 PinchGestureRecognizer 類別實作。 捏合手勢的常見案例是執行互動式捏合位置之影像的縮放。 這透過調整檢視區中，內容，並會在本文中示範。
ms.prod: xamarin
ms.assetid: 832F7810-F0CF-441A-B04A-3975F3FB8B29
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: b2348a1f0dfacc4a7a0e37f5c9041a07217ff802
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/07/2018
ms.locfileid: "34846109"
---
# <a name="adding-a-pinch-gesture-recognizer"></a>加入捏合筆勢辨識器

_捏合手勢用來執行互動式縮放，並且與 PinchGestureRecognizer 類別實作。捏合手勢的常見案例是執行互動式捏合位置之影像的縮放。這透過調整檢視區中，內容，並會在本文中示範。_

## <a name="overview"></a>總覽

若要讓使用者介面項目將與捏合手勢，建立[ `PinchGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PinchGestureRecognizer/)執行個體，處理[ `PinchUpdated` ](https://developer.xamarin.com/api/event/Xamarin.Forms.PinchGestureRecognizer.PinchUpdated/)事件，並加入至新的筆勢辨識器[`GestureRecognizers` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.GestureRecognizers/)使用者介面項目的集合。 下列程式碼範例示範`PinchGestureRecognizer`附加至[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)項目：

```csharp
var pinchGesture = new PinchGestureRecognizer();
pinchGesture.PinchUpdated += (s, e) => {
  // Handle the pinch
};
image.GestureRecognizers.Add(pinchGesture);
```

這也可藉在 XAML 中，如下列程式碼範例所示：

```xaml
<Image Source="waterfront.jpg">
  <Image.GestureRecognizers>
    <PinchGestureRecognizer PinchUpdated="OnPinchUpdated" />
  </Image.GestureRecognizers>
</Image>
```

程式碼`OnPinchUpdated`事件處理常式接著會新增至程式碼後置檔案：

```csharp
void OnPinchUpdated (object sender, PinchGestureUpdatedEventArgs e)
{
  // Handle the pinch
}
```

## <a name="creating-a-pinchtozoom-container"></a>建立 PinchToZoom 容器

處理捏合手勢執行縮放作業需要某些轉換使用者介面的數學運算。 本章節包含一般化的 helper 類別，可執行數學運算，可以用來以互動方式縮放任何使用者介面項目。 下列程式碼範例顯示 `PinchToZoomContainer` 類別：

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

這個類別可以包裝周圍的使用者介面項目，如此捏合手勢會縮放已包裝的使用者介面項目。 下列 XAML 程式碼範例所示`PinchToZoomContainer`包裝[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)項目：

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

下列程式碼範例示範如何`PinchToZoomContainer`包裝[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) C# 網頁中的項目：

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

當[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)項目收到捏合手勢，顯示的影像將會放大或。縮放由執行`PinchZoomContainer.OnPinchUpdated`方法，下列程式碼範例所示：

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

這個方法會更新已包裝的使用者介面項根據使用者的捏合手勢的縮放層級。 這所使用的值達成[ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.PinchGestureUpdatedEventArgs.Scale/)， [ `ScaleOrigin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.PinchGestureUpdatedEventArgs.ScaleOrigin/)和[ `Status` ](https://developer.xamarin.com/api/property/Xamarin.Forms.PinchGestureUpdatedEventArgs.Status/)屬性[ `PinchGestureUpdatedEventArgs`](https://developer.xamarin.com/api/type/Xamarin.Forms.PinchGestureUpdatedEventArgs/)計算縮放比例套用原點的捏合手勢的執行個體。 已包裝的使用者項目然後放大捏合手勢原點藉由設定其[ `TranslationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/)， [ `TranslationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/)，和[ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)屬性的導出值。

## <a name="summary"></a>總結

捏合手勢用來執行互動式的縮放和實作與[ `PinchGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PinchGestureRecognizer/)類別。


## <a name="related-links"></a>相關連結

- [PinchGesture （範例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/PinchGesture/)
- [GestureRecognizer](https://developer.xamarin.com/api/type/Xamarin.Forms.GestureRecognizer/)
- [PinchGestureRecognizer](https://developer.xamarin.com/api/type/Xamarin.Forms.PinchGestureRecognizer/)
