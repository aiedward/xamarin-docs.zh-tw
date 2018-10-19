---
title: 新增取景位置調整的筆勢辨識器
description: 這篇文章會說明如何使用移動瀏覽手勢，以水平及垂直移動瀏覽映像，以便顯示小於影像尺寸檢視區中時可以檢視所有的映像內容。
ms.prod: xamarin
ms.assetid: 42CBD2CF-432D-4F19-A05E-D569BB7F8713
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: 59e9f4c61bda86faa5a55d70ef91411adb14da6d
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2018
ms.locfileid: "38996802"
---
# <a name="adding-a-pan-gesture-recognizer"></a>新增取景位置調整的筆勢辨識器

_移動瀏覽軌跡用於偵測的手指在螢幕上移動，並將該動作套用至內容，並實作使用`PanGestureRecognizer`類別。移動瀏覽軌跡的常見案例是水平及垂直移動瀏覽映像，以便顯示小於影像尺寸檢視區中時可以檢視所有的映像內容。這透過在檢視區中，映像移，並會在本文中示範。_

若要讓使用者介面元素可移動與取景位置調整筆勢，建立[ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer)執行個體，處理[ `PanUpdated` ](xref:Xamarin.Forms.PanGestureRecognizer.PanUpdated)事件，並新增至新的筆勢辨識器[`GestureRecognizers` ](xref:Xamarin.Forms.View.GestureRecognizers)使用者介面項目的集合。 下列程式碼範例所示`PanGestureRecognizer`附加至[ `Image` ](xref:Xamarin.Forms.Image)項目：

```csharp
var panGesture = new PanGestureRecognizer();
panGesture.PanUpdated += (s, e) => {
  // Handle the pan
};
image.GestureRecognizers.Add(panGesture);
```

這也可以達成的 XAML，如下列程式碼範例所示：

```xaml
<Image Source="MonoMonkey.jpg">
  <Image.GestureRecognizers>
    <PanGestureRecognizer PanUpdated="OnPanUpdated" />
  </Image.GestureRecognizers>
</Image>
```

程式碼`OnPanUpdated`事件處理常式接著會新增至程式碼後置檔案：

```csharp
void OnPanUpdated (object sender, PanUpdatedEventArgs e)
{
  // Handle the pan
}
```

> [!NOTE]
> 在 Android 上的正確移動需要[Xamarin.Forms 2.1.0-pre1 NuGet 套件](https://www.nuget.org/packages/Xamarin.Forms/2.1.0.6501-pre1)最小值。

## <a name="creating-a-pan-container"></a>建立移動瀏覽容器

本章節包含的一般化的協助程式類別執行自由格式的移動，通常適用於在映像或對應內瀏覽。 處理移動瀏覽鍵筆勢，才能執行此作業需要一些數學運算來轉換的使用者介面。 這個數學用來只在已包裝的使用者介面項目的界限內移動瀏覽。 下列程式碼範例顯示 `PanContainer` 類別：

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

這個類別可以使筆勢會移動瀏覽已包裝的使用者介面項目包裝的使用者介面項目。 下列 XAML 程式碼範例所示`PanContainer`包裝[ `Image` ](xref:Xamarin.Forms.Image)項目：

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

下列程式碼範例示範如何`PanContainer`包裝[ `Image` ](xref:Xamarin.Forms.Image) C# 頁面中的項目：

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

在這兩個範例中， [ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest)並[ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest)屬性會設為所顯示之影像的寬度和高度值。

當[ `Image` ](xref:Xamarin.Forms.Image)項目收到移動瀏覽動作時，顯示的影像會間移動瀏覽。 移動瀏覽由`PanContainer.OnPanUpdated`方法，以下列程式碼範例所示：

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

這個方法會更新可檢視內容的已包裝的使用者介面項目，根據使用者的移動瀏覽軌跡。 這所使用的值來達成[ `TotalX` ](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalX)和[ `TotalY` ](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalY)屬性[ `PanUpdatedEventArgs` ](xref:Xamarin.Forms.PanUpdatedEventArgs)計算方向的執行個體和取景位置調整的距離。 `App.ScreenWidth`和`App.ScreenHeight`屬性提供的檢視區中，寬度與高度，並且由個別的平台特定專案設定的螢幕寬度和裝置的螢幕高度值。 已包裝的使用者項目然後上下移動，藉由設定其[ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX)並[ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY)屬性的導出值。

當移動瀏覽項目中不會佔用整個螢幕的內容，取得的檢視區寬度與高度的項目從[ `Height` ](xref:Xamarin.Forms.VisualElement.Height)並[ `Width` ](xref:Xamarin.Forms.VisualElement.Width)屬性。

> [!NOTE]
> 顯示高解析度的影像會大幅增加應用程式的記憶體耗用量。 因此，它們應該只建立時需要，而且應該釋出因為應用程式不再需要它們。 如需詳細資訊，請參閱[最佳化影像資源](~/xamarin-forms/deploy-test/performance.md#optimizeimages)。

## <a name="related-links"></a>相關連結

- [PanGesture （範例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/PanGesture/)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [PanGestureRecognizer](xref:Xamarin.Forms.PanGestureRecognizer)
