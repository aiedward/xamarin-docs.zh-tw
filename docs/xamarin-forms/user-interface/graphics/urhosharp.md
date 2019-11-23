---
title: 在 Xamarin 中使用 UrhoSharp
description: 本文說明如何使用 UrhoSharp，將3D 圖形新增至 Xamarin 應用程式，以獲得先進的視覺效果。
ms.prod: xamarin
ms.assetid: 0646B98E-CC04-4537-9715-9F82338FD7FF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/11/2016
ms.openlocfilehash: 7045bd4d3343d0c11c6cd52fa02cdc005175b8a7
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772927"
---
# <a name="using-urhosharp-in-xamarinforms"></a>在 Xamarin 中使用 UrhoSharp

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/urho-samples/tree/master/FormsSample)

## <a name="what-is-urhosharp"></a>什麼是 UrhoSharp？

[UrhoSharp](~/graphics-games/urhosharp/index.md)是適用于 Xamarin 和 .net 開發人員的強大3d 引擎。 此[簡介](~/graphics-games/urhosharp/introduction.md)會進一步說明 UrhoSharp 程式庫，而[這些附注](~/graphics-games/urhosharp/using.md)會說明如何程式設計場景和動作。

UrhoSharp 可以用來轉譯 Xamarin 應用程式中的圖形。
這個[範例](https://github.com/xamarin/urho-samples/tree/master/FormsSample)示範如何使用 UrhoSharp 來建立互動式的3d 圖表：

![](urhosharp-images/ios-animation.gif "在 iOS 上 UrhoSharp 3d 互動式圖表")
![] (urhosharp-images/android-animation.gif "UrhoSharp Android 上的3d 互動式圖表")

## <a name="adding-the-urhosharp-nuget-packages"></a>新增 UrhoSharp Nuget 套件

在使用 UrhoSharp 之前，開發人員必須在其解決方案中新增 UrhoSharp Nuget 套件。 本指南假設有一個包含 iOS、Android 和 .NET Standard 程式庫專案的 Xamarin. Forms 專案。 所有程式碼都會寫入 .NET Standard 程式庫專案中;但 UrhoSharp Nuget 也必須新增至 iOS 和 Android 專案。

UrhoSharp Nuget 套件包含建立 UrhoSharp 物件所需的所有物件。 UrhoSharp nuget 套件包含 `UrhoSurface` 類別，用來裝載 Xamarin 中的 UrhoSharp。
若要開始，請以滑鼠右鍵按一下 .NET Standard 程式庫專案中的 [**套件**] 資料夾，然後選取 [**新增套件**]。輸入搜尋詞彙**UrhoSharp**，**針對 [Xamarin**] 選取 [UrhoSharp]，然後按一下 [**新增套件**]。

新增封裝對話方塊(urhosharp-images/add-package.png#lightbox "新增封裝對話方塊") [ ![(urhosharp-images/add-package-sml.png "")] ]

UrhoSharp NuGet 套件會新增至專案：

![](urhosharp-images/packages.png "封裝資料夾")

針對平臺特定專案（例如 iOS 和 Android）重複上述步驟。

## <a name="walkthrough-adding-urhosharp-to-a-xamarinforms-app"></a>逐步解說：將 UrhoSharp 新增至 Xamarin. Forms 應用程式

下列步驟描述 UrhoSharp 範例中的程式碼：

1. [建立 Xamarin 表單頁面](#1)
2. [新增 UrhoSurface](#2)
3. [建立 Urho 應用程式](#3)
4. [將圖表類別新增至 UrhoSurface](#4)
5. [與 UrhoSharp 互動](#5)

請注意，此範例C#會使用6個功能，而且可能不會在舊版的 Visual Studio 上編譯。

<a name="1"/>

### <a name="1-create-a-xamarin-forms-page"></a>1. 建立 Xamarin Forms 頁面

下列程式碼顯示在新增任何 Urho 相關的程式碼之前，`UrhoPage` 的 [Xamarin] 頁面：

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

<a name="2"/>

### <a name="2-add-the-urhosurface"></a>2. 新增 UrhoSurface

UrhoSharp 可以裝載在 `ContentPage` 中，就像其他的 Xamarin 控制項一樣。
下列程式碼片段顯示新增至 [Xamarin] 頁面的 `UrhoSurface`：

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

<a name="3"/>

### <a name="3-build-a-urho-application"></a>3. 建立 Urho 應用程式

請參閱 `Charts` 類別，以執行此範例中使用的 Urho 3D 圖形。 基本程式碼大綱如下所示-請注意，類別會執行 `Urho.Application`，這與在**App.cs**中實作為 `Xamarin.Forms.Application` 類別不同。

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

<a name="4"/>

### <a name="4-add-the-charts-class-to-the-urhosurface"></a>4. 將圖表類別加入至 UrhoSurface

使用 `UrhoSurface.Show<T>` 的泛型方法，將 Urho 應用程式加入至 [Xamarin] 頁面。 下列程式碼片段顯示建立 `Charts` 類別所需的其他程式碼：

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

注意： `Show<T>` 方法是非同步，而且應該使用 `await` 關鍵字來呼叫。

<a name="5"/>

### <a name="5-interacting-with-urhosharp"></a>5. 與 UrhoSharp 互動

此範例可讓您選取和修改圖表列。 `Charts` 類別會公開 `Bars` 和 `SelectedBar`，以啟用此互動。

每個「橫條」都有一個選取的事件處理常式，會在呈現 `Charts` 類別之後，逐一查看已公開的 `Bars` 集合：

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

事件處理常式會使用 [Xamarin] `Slider` 控制項的值，來調整指定之橫條的高度：

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

最後，將兩個 `Slider` 控制項連接，以便在其值變更時，UrhoSharp 畫布會受到影響。 第一個滑杆會旋轉3D 圖表影像，而第二個滑杆則會調整所選橫條的高度。

```csharp
rotationSlider = new Slider(0, 500, 250);
rotationSlider.ValueChanged += (s, e) => urhoApp?.Rotate((float)(e.NewValue - e.OldValue));

selectedBarSlider = new Slider(0, 5, 2.5);
selectedBarSlider.ValueChanged += OnValuesSliderValueChanged;
```

[頁面頂端](#what-is-urhosharp)的動畫會顯示執行中的範例。

## <a name="summary"></a>摘要

此頁面會顯示如何使用 UrhoSharp 將3D 資料視覺效果加入至 Xamarin. 表單。 如需如何使用上述方法來建立可包含在 Xamarin 中的 Urho 場景，請閱讀[UrhoSharp 檔](~/graphics-games/urhosharp/index.md)。

## <a name="related-links"></a>相關連結

- [圖表範例（C# 6）](https://github.com/xamarin/urho-samples/tree/master/FormsSample)
- [UrhoSharp](~/graphics-games/urhosharp/index.md)
