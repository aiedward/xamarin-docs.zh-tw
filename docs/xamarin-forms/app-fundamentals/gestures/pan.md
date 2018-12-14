---
title: 新增平移手勢辨識器
description: 本文說明如何使用平移手勢來水平及垂直平移影像，以便在用於顯示的檢視區小於影像大小時，可檢視所有影像內容。
ms.prod: xamarin
ms.assetid: 42CBD2CF-432D-4F19-A05E-D569BB7F8713
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: 59e9f4c61bda86faa5a55d70ef91411adb14da6d
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2018
ms.locfileid: "38996802"
---
# <a name="adding-a-pan-gesture-recognizer"></a>新增平移手勢辨識器

_平移手勢用於偵測手指在螢幕上的移動，並會將該動作套用至內容，由 `PanGestureRecognizer` 類別實作。平移手勢的一個常見情節是水平及垂直平移影像，以便在用於顯示的檢視區小於影像大小時，可檢視所有影像內容。這可透過在檢視區中移動影像來完成，如本文所示。_

若要讓使用者介面項目可透過平移手勢移動，請建立 [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) 執行個體，處理 [`PanUpdated`](xref:Xamarin.Forms.PanGestureRecognizer.PanUpdated) 事件，然後將新手勢辨識器新增至使用者介面項目的 [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) 集合。 下列程式碼範例顯示附加至 [`Image`](xref:Xamarin.Forms.Image) 項目的 `PanGestureRecognizer`：

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

> [!NOTE]
> 在 Android 上正確平移至少需要 [Xamarin.Forms 2.1.0-pre1 NuGet 套件](https://www.nuget.org/packages/Xamarin.Forms/2.1.0.6501-pre1)。

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

您可以在使用者介面項目周圍包裝此類別，讓手勢平移包裝的使用者介面項目。 下列 XAML 程式碼範例示範 `PanContainer` 如何包裝 [`Image`](xref:Xamarin.Forms.Image) 項目：

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

下列程式碼範例示範 `PanContainer` 如何在 C# 頁面中包裝 [`Image`](xref:Xamarin.Forms.Image) 項目：

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

在這兩個範例中，會將 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 和 [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 屬性設定為所要顯示影像的寬度和高度值。

當 [`Image`](xref:Xamarin.Forms.Image) 項目收到平移手勢時，則會平移顯示的影像。 平移是由 `PanContainer.OnPanUpdated` 方法所執行，如下列程式碼範例所示：

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

此方法會根據使用者的平移手勢，更新包裝使用者介面項目的可檢視內容。 這會透過使用 [`PanUpdatedEventArgs`](xref:Xamarin.Forms.PanUpdatedEventArgs) 執行個體的 [`TotalX`](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalX) 和 [`TotalY`](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalY) 屬性值，計算平移的方向和距離來完成。 `App.ScreenWidth` 和 `App.ScreenHeight` 屬性提供檢視區的寬度和高度，並依個別平台特定專案設定為裝置的螢幕寬度和螢幕高度值。 包裝的使用者項目會接著透過將其 [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) 和 [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) 設定為計算值來進行平移。

在未佔滿螢幕的項目中平移內容時，檢視區的高度和寬度可從項目的 [`Height`](xref:Xamarin.Forms.VisualElement.Height) 和 [`Width`](xref:Xamarin.Forms.VisualElement.Width) 屬性取得。

> [!NOTE]
> 顯示高解析度影像可能會大幅增加應用程式的記憶體使用量。 因此，請務必只有在必要時才加以建立，並在應用程式不再需要時立即釋出。 如需詳細資訊，請參閱[最佳化影像資源](~/xamarin-forms/deploy-test/performance.md#optimizeimages)。

## <a name="related-links"></a>相關連結

- [PanGesture (Samples)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/PanGesture/)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [PanGestureRecognizer](xref:Xamarin.Forms.PanGestureRecognizer)
