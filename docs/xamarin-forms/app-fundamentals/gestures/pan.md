---
title: "新增取景位置調整筆勢辨識器"
description: "取景位置調整筆勢用於偵測拖曳，而且以 PanGestureRecognizer 類別實作。 取景位置調整動作的常見案例是水平及垂直拖曳影像，以便顯示位於小於影像尺寸檢視區時可以檢視所有的映像內容。 這透過移動的檢視區內，映像，並會在本文中示範。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 42CBD2CF-432D-4F19-A05E-D569BB7F8713
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: 4da42a92c83dcc1ec0b0ba2528de672e3fcf3be3
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="adding-a-pan-gesture-recognizer"></a>新增取景位置調整筆勢辨識器

_取景位置調整筆勢用於偵測拖曳，而且以 PanGestureRecognizer 類別實作。取景位置調整動作的常見案例是水平及垂直拖曳影像，以便顯示位於小於影像尺寸檢視區時可以檢視所有的映像內容。這透過移動的檢視區內，映像，並會在本文中示範。_

## <a name="overview"></a>總覽

若要讓使用者介面項目與 pan 筆勢 draggable，建立[ `PanGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PanGestureRecognizer/)執行個體，處理[ `PanUpdated` ](https://developer.xamarin.com/api/event/Xamarin.Forms.PanGestureRecognizer.PanUpdated/)事件，並加入至新的筆勢辨識器[`GestureRecognizers` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.GestureRecognizers/)使用者介面項目的集合。 下列程式碼範例示範`PanGestureRecognizer`附加至[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)項目：

```csharp
var panGesture = new PanGestureRecognizer();
panGesture.PanUpdated += (s, e) => {
  // Handle the pan
};
image.GestureRecognizers.Add(panGesture);
```

這也可藉在 XAML 中，如下列程式碼範例所示：

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
> 在 Android 上正確移動瀏覽需要[Xamarin.Forms 2.1.0-pre1 NuGet 封裝](https://www.nuget.org/packages/Xamarin.Forms/2.1.0.6501-pre1)最少。

## <a name="creating-a-pan-container"></a>建立全景容器

本章節包含的一般化協助程式類別，用於執行不限格式之移動瀏覽通常適用於在映像或地圖中巡覽。 處理取景位置調整手勢，以執行拖曳作業需要某些轉換使用者介面的數學運算。 只有在已包裝的使用者介面元素的界限內拖曳用於這個數學運算。 下列程式碼範例顯示 `PanContainer` 類別：

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

這個類別可以包裝周圍的使用者介面項目，如此取景位置調整軌跡將拖曳的已包裝的使用者介面項目。 下列 XAML 程式碼範例所示`PanContainer`包裝[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)項目：

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

下列程式碼範例示範如何`PanContainer`包裝[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) C# 網頁中的項目：

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

在這兩個範例中， [ `WidthRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.WidthRequest/)和[ `HeightRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.HeightRequest/)屬性會設定為要顯示之影像的寬度和高度值。

當[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)項目收到移動瀏覽動作時，將拖曳顯示的影像。 所執行的拖曳`PanContainer.OnPanUpdated`方法，下列程式碼範例所示：

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

這個方法會更新可檢視之內容的已包裝的使用者介面項目，根據使用者的移動瀏覽筆勢。 這所使用的值達成[ `TotalX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.PanUpdatedEventArgs.TotalX/)和[ `TotalY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.PanUpdatedEventArgs.TotalY/)屬性[ `PanUpdatedEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PanUpdatedEventArgs/)計算方向的執行個體和移動瀏覽的距離。 `App.ScreenWidth`和`App.ScreenHeight`屬性提供的高度和寬度的檢視區，且由個別的平台專屬專案設定的螢幕寬度和裝置的螢幕高度值的。 已包裝的使用者項目然後拖曳藉由設定其[ `TranslationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/)和[ `TranslationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/)屬性的導出值。

移動項目不會佔用全螢幕中的內容時取得的高度和寬度的檢視區的項目從[ `Height` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Height/)和[ `Width` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Width/)屬性。

> [!NOTE]
> 顯示高解析度的影像，可以大幅增加應用程式的記憶體耗用量。 因此，它們應該只能建立時所需的應用程式已不再需要它們時立即釋放。 如需詳細資訊，請參閱[最佳化影像資源](~/xamarin-forms/deploy-test/performance.md#optimizeimages)。

## <a name="summary"></a>總結

取景位置調整筆勢用於偵測拖曳，而且透過實作[ `PanGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PanGestureRecognizer/)類別。



## <a name="related-links"></a>相關連結

- [PanGesture （範例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/PanGesture/)
- [GestureRecognizer](https://developer.xamarin.com/api/type/Xamarin.Forms.GestureRecognizer/)
- [PanGestureRecognizer](https://developer.xamarin.com/api/type/Xamarin.Forms.PanGestureRecognizer/)
