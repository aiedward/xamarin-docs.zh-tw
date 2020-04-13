---
title: 新增平移手勢辨識器
description: 本文說明如何使用平移手勢來水平及垂直平移影像，以便在用於顯示的檢視區小於影像大小時，可檢視所有影像內容。
ms.prod: xamarin
ms.assetid: 42CBD2CF-432D-4F19-A05E-D569BB7F8713
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: 73e312a1af56091a7e579d3fcbcea810ee0efb1e
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "68820976"
---
# <a name="adding-a-pan-gesture-recognizer"></a>新增平移手勢辨識器

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-pangesture)

_平移手勢用於檢測手指在螢幕上的移動並將該移動應用於內容,並與`PanGestureRecognizer`類一起實現。平移手勢的常見方案是水平和垂直平移圖像,以便在圖像顯示在小於圖像尺寸的視口中時,可以查看所有圖像內容。這是通過在視口中移動圖像來實現的,本文演示了這一點。_

要使用戶介面元素與平移手勢一起移動,請建立實例[`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer)、處理[`PanUpdated`](xref:Xamarin.Forms.PanGestureRecognizer.PanUpdated)事件,並將新的手勢識別器添加到[`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers)使用者介面元素的集合中。 以下代碼範例顯示`PanGestureRecognizer`附加到元素的[`Image`](xref:Xamarin.Forms.Image):

```csharp
var panGesture = new PanGestureRecognizer();
panGesture.PanUpdated += (s, e) => {
  // Handle the pan
};
image.GestureRecognizers.Add(panGesture);
```

這也可在 XAML 中完成，如下列程式碼範例所示：

```xaml
<Image Source="MonoMonkey.jpg">
  <Image.GestureRecognizers>
    <PanGestureRecognizer PanUpdated="OnPanUpdated" />
  </Image.GestureRecognizers>
</Image>
```

`OnPanUpdated` 事件處理常式的程式碼會接著新增至程式碼後置檔案：

```csharp
void OnPanUpdated (object sender, PanUpdatedEventArgs e)
{
  // Handle the pan
}
```

## <a name="creating-a-pan-container"></a>建立平移容器

本節包含執行任意平移的一般協助程式類別，通常適合在影像或地圖內巡覽。 處理平移手勢來執行此作業，需要特定數學公式才能轉換使用者介面。 此數學公式僅適用於在包裝的使用者介面項目界限內平移。 下列程式碼範例顯示 `PanContainer` 類別：

```csharp
public class PanContainer : ContentView
{
  double x, y;

  public PanContainer ()
  {
    // Set PanGestureRecognizer.TouchPoints to control the
    // number of touch points needed to pan
    var panGesture = new PanGestureRecognizer ();
    panGesture.PanUpdated += OnPanUpdated;
    GestureRecognizers.Add (panGesture);
  }

  void OnPanUpdated (object sender, PanUpdatedEventArgs e)
  {
    ...
  }
}
```

您可以在使用者介面項目周圍包裝此類別，讓手勢平移包裝的使用者介面項目。 下面的 XAML 代碼範例`PanContainer`顯示了[`Image`](xref:Xamarin.Forms.Image)換行 元素:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:PanGesture"
             x:Class="PanGesture.HomePage">
    <ContentPage.Content>
        <AbsoluteLayout>
            <local:PanContainer>
                <Image Source="MonoMonkey.jpg" WidthRequest="1024" HeightRequest="768" />
            </local:PanContainer>
        </AbsoluteLayout>
    </ContentPage.Content>
</ContentPage>
```

以下代碼範例顯示如何在 C#`PanContainer`[`Image`](xref:Xamarin.Forms.Image)頁 中換行元素:

```csharp
public class HomePageCS : ContentPage
{
  public HomePageCS ()
  {
    Content = new AbsoluteLayout {
      Padding = new Thickness (20),
      Children = {
        new PanContainer {
          Content = new Image {
            Source = ImageSource.FromFile ("MonoMonkey.jpg"),
            WidthRequest = 1024,
            HeightRequest = 768
          }
        }
      }
    };
  }
}
```

在這兩個範例中,[`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)[`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest)和 屬性都設定為顯示的圖像的寬度和高度值。

當[`Image`](xref:Xamarin.Forms.Image)元素收到平移手勢時,將顯示的圖像將被平移。 平移是由 `PanContainer.OnPanUpdated` 方法所執行，如下列程式碼範例所示：

```csharp
void OnPanUpdated (object sender, PanUpdatedEventArgs e)
{
  switch (e.StatusType) {
  case GestureStatus.Running:
    // Translate and ensure we don't pan beyond the wrapped user interface element bounds.
    Content.TranslationX =
      Math.Max (Math.Min (0, x + e.TotalX), -Math.Abs (Content.Width - App.ScreenWidth));
    Content.TranslationY =
      Math.Max (Math.Min (0, y + e.TotalY), -Math.Abs (Content.Height - App.ScreenHeight));
    break;

  case GestureStatus.Completed:
    // Store the translation applied during the pan
    x = Content.TranslationX;
    y = Content.TranslationY;
    break;
  }
}
```

此方法會根據使用者的平移手勢，更新包裝使用者介面項目的可檢視內容。 這是通過使用[`TotalX`](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalX)[`TotalY`](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalY)[`PanUpdatedEventArgs`](xref:Xamarin.Forms.PanUpdatedEventArgs)實例的值和屬性來計算平移的方向和距離來實現的。 `App.ScreenWidth` 和 `App.ScreenHeight` 屬性提供檢視區的寬度和高度，並依個別平台特定專案設定為裝置的螢幕寬度和螢幕高度值。 然後,通過將使用者[`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)元素[`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)和 屬性設置為計算值來平移包裝的使用者元素。

在未佔用全屏的元素中平移內容時,可以從元素[`Height`](xref:Xamarin.Forms.VisualElement.Height)和屬性獲取視口的高度和[`Width`](xref:Xamarin.Forms.VisualElement.Width)寬度。

> [!NOTE]
> 顯示高解析度影像可能會大幅增加應用程式的記憶體使用量。 因此，請務必只有在必要時才加以建立，並在應用程式不再需要時立即釋出。 如需詳細資訊，請參閱[最佳化影像資源](~/xamarin-forms/deploy-test/performance.md#optimize-image-resources)。

## <a name="related-links"></a>相關連結

- [PanGesture (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-pangesture)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [PanGestureRecognizer](xref:Xamarin.Forms.PanGestureRecognizer)
