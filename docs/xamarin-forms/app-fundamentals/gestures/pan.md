---
title: 新增 pan 手勢辨識器
description: 本文說明如何使用平移手勢來水平及垂直平移影像，以便在用於顯示的檢視區小於影像大小時，可檢視所有影像內容。
ms.prod: xamarin
ms.assetid: 42CBD2CF-432D-4F19-A05E-D569BB7F8713
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 886191725421a754e696409deb7a3cca0f6ea923
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87917698"
---
# <a name="add-a-pan-gesture-recognizer"></a>新增 pan 手勢辨識器

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-pangesture)

_移動流覽手勢是用來偵測螢幕上的手指移動，並將該動作套用至內容，並使用類別來執行 `PanGestureRecognizer` 。移動流覽手勢的常見案例是以水準和垂直方式平移影像，讓所有影像內容在顯示于小於影像尺寸的視窗處時，都可以加以查看。這可以藉由在視口中移動影像來完成，如本文所示。_

若要使用移動流覽手勢來移動使用者介面專案，請建立 [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) 實例、處理 [`PanUpdated`](xref:Xamarin.Forms.PanGestureRecognizer.PanUpdated) 事件，並將新的手勢辨識器新增至 [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) 使用者介面專案上的集合。 下列程式碼範例顯示 `PanGestureRecognizer` 附加至元素的 [`Image`](xref:Xamarin.Forms.Image) ：

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

您可以在使用者介面項目周圍包裝此類別，讓手勢平移包裝的使用者介面項目。 下列 XAML 程式碼範例示範如何 `PanContainer` 包裝 [`Image`](xref:Xamarin.Forms.Image) 元素：

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

下列程式碼範例顯示如何 `PanContainer` [`Image`](xref:Xamarin.Forms.Image) 在 c # 頁面中包裝專案：

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

在這兩個範例中， [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 和 [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 屬性會設定為所顯示影像的寬度和高度值。

當 [`Image`](xref:Xamarin.Forms.Image) 元素收到平移手勢時，顯示的影像將會移動流覽。 平移是由 `PanContainer.OnPanUpdated` 方法所執行，如下列程式碼範例所示：

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

此方法會根據使用者的平移手勢，更新包裝使用者介面項目的可檢視內容。 使用 [`TotalX`](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalX) 實例的和 [`TotalY`](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalY) 屬性值 [`PanUpdatedEventArgs`](xref:Xamarin.Forms.PanUpdatedEventArgs) 來計算平移的方向和距離，即可達成此目的。 `App.ScreenWidth` 和 `App.ScreenHeight` 屬性提供檢視區的寬度和高度，並依個別平台特定專案設定為裝置的螢幕寬度和螢幕高度值。 然後，將其 [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) 和屬性設定為計算值，以移動流覽已包裝的使用者元素 [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) 。

在不佔用全螢幕的專案中移動內容時，可以從專案的和屬性取得該區的高度和寬度 [`Height`](xref:Xamarin.Forms.VisualElement.Height) [`Width`](xref:Xamarin.Forms.VisualElement.Width) 。

> [!NOTE]
> 顯示高解析度影像可能會大幅增加應用程式的記憶體使用量。 因此，請務必只有在必要時才加以建立，並在應用程式不再需要時立即釋出。 如需詳細資訊，請參閱[最佳化影像資源](~/xamarin-forms/deploy-test/performance.md#optimize-image-resources)。

## <a name="related-links"></a>相關連結

- [PanGesture (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-pangesture)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [PanGestureRecognizer](xref:Xamarin.Forms.PanGestureRecognizer)
