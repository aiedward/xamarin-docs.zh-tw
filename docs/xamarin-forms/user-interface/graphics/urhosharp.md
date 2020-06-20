---
title: 使用中的 UrhoSharpXamarin.Forms
description: 本文說明如何使用 UrhoSharp，將3D 圖形新增至 Xamarin.Forms 應用程式，以獲得先進的視覺效果。
ms.prod: xamarin
ms.assetid: 0646B98E-CC04-4537-9715-9F82338FD7FF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/11/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: fbf717092da7f77e265803fae87efb5bf0e9876f
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84574362"
---
# <a name="using-urhosharp-in-xamarinforms"></a>使用中的 UrhoSharpXamarin.Forms

[![下載範例 ](~/media/shared/download.png) 下載範例](https://github.com/xamarin/urho-samples/tree/master/FormsSample)

## <a name="what-is-urhosharp"></a>什麼是 UrhoSharp？

[UrhoSharp](~/graphics-games/urhosharp/index.md)是適用于 Xamarin 和 .net 開發人員的強大3d 引擎。 此[簡介](~/graphics-games/urhosharp/introduction.md)會進一步說明 UrhoSharp 程式庫，而[這些附注](~/graphics-games/urhosharp/using.md)會說明如何程式設計場景和動作。

UrhoSharp 可以用來呈現應用程式中的圖形 Xamarin.Forms 。
這個[範例](https://github.com/xamarin/urho-samples/tree/master/FormsSample)示範如何使用 UrhoSharp 來建立互動式的3d 圖表：

![](urhosharp-images/ios-animation.gif "UrhoSharp 3D Interactive Chart on iOS")
![](urhosharp-images/android-animation.gif "UrhoSharp 3D Interactive Chart on Android")

## <a name="adding-the-urhosharp-nuget-packages"></a>新增 UrhoSharp NuGet 套件

在使用 UrhoSharp 之前，開發人員必須在其解決方案中新增 UrhoSharp NuGet 套件。 本指南假設 Xamarin.Forms 專案具有 iOS、Android 和 .NET Standard 程式庫專案。 所有程式碼都會寫入 .NET Standard 程式庫專案中;但 UrhoSharp NuGet 也必須新增至 iOS 和 Android 專案。

UrhoSharp NuGet 套件包含建立 UrhoSharp 物件所需的所有物件。 UrhoSharp NuGet 套件包含 `UrhoSurface` 用來在中裝載 UrhoSharp 的類別 Xamarin.Forms 。
若要開始，請以滑鼠右鍵按一下 .NET Standard 程式庫專案中的 [**套件**] 資料夾，然後選取 [**新增套件**]。輸入搜尋詞彙**UrhoSharp**， ** Xamarin.Forms 針對**選取 [UrhoSharp]，然後按一下 [**新增套件**]。

[![](urhosharp-images/add-package-sml.png "Add Packages Dialog")](urhosharp-images/add-package.png#lightbox "Add Packages Dialog")

UrhoSharp NuGet 套件會新增至專案：

![](urhosharp-images/packages.png "Packages Folder")

針對平臺特定專案（例如 iOS 和 Android）重複上述步驟。

## <a name="walkthrough-adding-urhosharp-to-a-xamarinforms-app"></a>逐步解說：將 UrhoSharp 新增至 Xamarin.Forms 應用程式

下列步驟描述 UrhoSharp 範例中的程式碼 Xamarin.Forms ：

1. [建立 Xamarin 表單頁面](#1-create-a-xamarin-forms-page)
2. [新增 UrhoSurface](#2-add-the-urhosurface)
3. [建立 Urho 應用程式](#3-build-an-urho-application)
4. [將圖表類別新增至 UrhoSurface](#4-add-the-charts-class-to-the-urhosurface)
5. [與 UrhoSharp 互動](#5-interacting-with-urhosharp)

請注意，此範例使用 c # 6 功能，而且可能不會在舊版的 Visual Studio 上編譯。

### <a name="1-create-a-xamarin-forms-page"></a>1. 建立 Xamarin Forms 頁面

下列程式碼顯示在 Xamarin.Forms `UrhoPage` 新增任何 Urho 相關程式碼之前的頁面：

```csharp
public class UrhoPage : ContentPage
{
  Slider selectedBarSlider, rotationSlider;

  public UrhoPage()
  {
    // we'll add Urho later

    rotationSlider = new Slider(0, 500, 250);

    selectedBarSlider = new Slider(0, 5, 2.5);

    Title = " UrhoSharp + Xamarin.Forms";
    Content = new StackLayout {
      Padding = new Thickness (12, 12, 12, 40),
      VerticalOptions = LayoutOptions.FillAndExpand,
      Children = {
        rotationSlider,
        new Label { Text = "SELECTED VALUE:" },
        selectedBarSlider,
      }
    };
  }
```

### <a name="2-add-the-urhosurface"></a>2. 新增 UrhoSurface

UrhoSharp 可以裝載在類似的 `ContentPage` 其他 Xamarin.Forms 控制項中。
下列程式碼片段會顯示 `UrhoSurface` 已新增至 Xamarin.Forms 頁面的：

```csharp
using Urho;
using Urho.Forms;
...
public class UrhoPage : ContentPage
{
  UrhoSurface urhoSurface;

  public UrhoPage()
  {
    urhoSurface = new UrhoSurface();
    urhoSurface.VerticalOptions = LayoutOptions.FillAndExpand;
...
    Content = new StackLayout {
    Padding = new Thickness (12, 12, 12, 40),
    VerticalOptions = LayoutOptions.FillAndExpand,
    Children = {
      urhoSurface,  // added
      new Label { Text = "ROTATION:" },
      rotationSlider,
      new Label { Text = "SELECTED VALUE:" },
      selectedBarSlider,
    }
  };
```

### <a name="3-build-an-urho-application"></a>3. 建立 Urho 應用程式

`Charts`如需此範例中使用之 Urho 3d 圖形的執行方式，請參閱類別。 基本程式碼大綱如下所示-請注意，類別 `Urho.Application` 會執行，這與 App.cs 中所實的 `Xamarin.Forms.Application` 類別**App.cs**不同。

```csharp
using Urho;
using Urho.Actions;
using Urho.Gui;
using Urho.Shapes;

namespace FormsSample
{
    public class Charts : Urho.Application
    {
    public Charts (ApplicationOptions options = null) : base(options) { }
    protected override void Start ()
    {
      ...
    }
    protected override void OnUpdate(float timeStep)
    {
      ...
    }
```

[UrhoSharp 檔](~/graphics-games/urhosharp/index.md)包含有關如何建立3d 場景和動作的詳細資訊。

### <a name="4-add-the-charts-class-to-the-urhosurface"></a>4. 將圖表類別加入至 UrhoSurface

使用 `UrhoSurface.Show<T>` 泛型方法，將 Urho 應用程式新增至 Xamarin.Forms 頁面。 下列程式碼片段顯示建立類別所需的其他程式碼 `Charts` ：

```csharp
public class UrhoPage : ContentPage
{
  Charts urhoApp;
  ...
  protected override async void OnAppearing ()
  {
    urhoApp = await urhoSurface.Show<Charts> (new ApplicationOptions(assetsFolder: null)
      { Orientation = ApplicationOptions.OrientationType.Portrait });
  }
```

注意： `Show<T>` 方法是非同步，而且應該使用關鍵字來呼叫 `await` 。

### <a name="5-interacting-with-urhosharp"></a>5. 與 UrhoSharp 互動

此範例可讓您選取和修改圖表列。 `Charts`類別會公開 `Bars` 和 `SelectedBar` 以啟用此互動。

每個「橫條」都有一個選取的事件處理常式，會在 `Charts` 呈現類別之後，逐一查看已公開的 `Bars` 集合：

```csharp
protected override async void OnAppearing ()
{
  urhoApp = await urhoSurface.Show<Charts>(new ApplicationOptions(assetsFolder: null) { Orientation = ApplicationOptions.OrientationType.Portrait });
  foreach (var bar in urhoApp.Bars)
  {
    bar.Selected += OnBarSelection;
  }
}
```

事件處理常式會使用控制項的值 Xamarin.Forms `Slider` 來調整給定橫條的高度：

```csharp
private void OnBarSelection(Bar bar)
{
  //reset value
  selectedBarSlider.ValueChanged -= OnValuesSliderValueChanged;
  selectedBarSlider.Value = bar.Value;
  selectedBarSlider.ValueChanged += OnValuesSliderValueChanged;
}

void OnValuesSliderValueChanged(object sender, ValueChangedEventArgs e)
{  // C# 6
  if (urhoApp?.SelectedBar != null)
  {
    urhoApp.SelectedBar.Value = (float)e.NewValue;
  }
}
```

最後，連接兩個 `Slider` 控制項，以便在其值變更時，UrhoSharp 畫布會受到影響。 第一個滑杆會旋轉3D 圖表影像，而第二個滑杆則會調整所選橫條的高度。

```csharp
rotationSlider = new Slider(0, 500, 250);
rotationSlider.ValueChanged += (s, e) => urhoApp?.Rotate((float)(e.NewValue - e.OldValue));

selectedBarSlider = new Slider(0, 5, 2.5);
selectedBarSlider.ValueChanged += OnValuesSliderValueChanged;
```

[頁面頂端](#what-is-urhosharp)的動畫會顯示執行中的範例。

## <a name="summary"></a>摘要

此頁面會顯示如何使用 UrhoSharp 將3D 資料視覺效果加入至 Xamarin.Forms 。 如需如何使用上述方法來建立可包含在應用程式中的 Urho 場景的詳細資訊，請參閱[UrhoSharp 檔](~/graphics-games/urhosharp/index.md) Xamarin.Forms 。

## <a name="related-links"></a>相關連結

- [圖表範例（c # 6）](https://github.com/xamarin/urho-samples/tree/master/FormsSample)
- [UrhoSharp](~/graphics-games/urhosharp/index.md)
