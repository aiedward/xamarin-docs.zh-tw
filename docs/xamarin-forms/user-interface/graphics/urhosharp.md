---
title: 在 Xamarin.Forms 中使用 UrhoSharp
description: 這篇文章說明如何使用 UrhoSharp 將 3D 圖形新增至 Xamarin.Forms 應用程式的進階視覺效果。
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
# <a name="using-urhosharp-in-xamarinforms"></a>在 Xamarin.Forms 中使用 UrhoSharp

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/urho-samples/tree/master/FormsSample)

## <a name="what-is-urhosharp"></a>什麼是 UrhoSharp？

[UrhoSharp](~/graphics-games/urhosharp/index.md) Xamarin 和.NET 開發人員是功能強大的 3D 引擎。 [簡介](~/graphics-games/urhosharp/introduction.md)UrhoSharp 文件庫中進一步說明和[這些附註](~/graphics-games/urhosharp/using.md)說明程式場景和動作。

UrhoSharp 可用來轉譯在 Xamarin.Forms 應用程式中的圖形。
這[範例](https://github.com/xamarin/urho-samples/tree/master/FormsSample)示範如何 UrhoSharp 無法用來建構互動式 3D 圖表：

![](urhosharp-images/ios-animation.gif "UrhoSharp 3D 互動的圖表在 iOS 上")
![](urhosharp-images/android-animation.gif "UrhoSharp 3D 互動的圖表在 Android 上")

## <a name="adding-the-urhosharp-nuget-packages"></a>新增 UrhoSharp Nuget 套件

之前使用 UrhoSharp，開發人員必須將 UrhoSharp Nuget 套件新增至其解決方案。 本指南假設 iOS、 Android 和.NET Standard 的 Xamarin.Forms 專案程式庫專案。 所有的程式碼會以.NET Standard 程式庫專案中;但 UrhoSharp Nuget 必須新增至 iOS 和 Android 專案太。

UrhoSharp.Forms Nuget 套件包含所有建立 UrhoSharp 物件所需的物件。 UrhoSharp.Forms nuget 套件包含`UrhoSurface`類別，用來主控 UrhoSharp 在 Xamarin.Forms 中。
若要開始，以滑鼠右鍵按一下**封裝**資料夾中，.NET Standard 程式庫專案，然後選取**新增套件...** .輸入搜尋字詞**UrhoSharp.Forms**，選取**適用於 Xamarin.Forms UrhoSharp**，然後按一下**加入封裝**。

[![](urhosharp-images/add-package-sml.png "新增套件 對話方塊")](urhosharp-images/add-package.png#lightbox "新增套件 對話方塊")

UrhoSharp.Forms NuGet 封裝會新增至專案：

![](urhosharp-images/packages.png "Packages 資料夾")

（例如 iOS 和 Android） 平台專屬專案重複上述步驟。

## <a name="walkthrough-adding-urhosharp-to-a-xamarinforms-app"></a>逐步解說：將 UrhoSharp 新增至 Xamarin. Forms 應用程式

下列步驟說明 Xamarin.Forms UrhoSharp 範例中的程式碼：

1. [建立 Xamarin 表單頁面](#1)
2. [新增 UrhoSurface](#2)
3. [建置 Urho 應用程式](#3)
4. [將圖表類別新增至 UrhoSurface](#4)
5. [互動使用 UrhoSharp](#5)

請注意，此範例會使用C#6 功能，並可能不會編譯在舊版的 Visual Studio。

<a name="1"/>

### <a name="1-create-a-xamarin-forms-page"></a>1.建立 Xamarin 表單頁面

下列程式碼示範 Xamarin.Forms 頁面`UrhoPage`在加入任何 Urho 相關的程式碼之前：

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

### <a name="2-add-the-urhosurface"></a>2.新增 UrhoSurface

UrhoSharp 可以裝載於`ContentPage`像其他 Xamarin.Forms 控制項。
底下顯示的程式碼片段`UrhoSurface`新增至 Xamarin.Forms 頁面：

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

### <a name="3-build-a-urho-application"></a>3.建置 Urho 應用程式

請參閱`Charts`Urho 3D 圖形，使用此範例中使用的實作的類別。 基本程式碼大綱會顯示以下-請注意此類別會實作`Urho.Application`到不同`Xamarin.Forms.Application`類別中實作**App.cs**。

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

[UrhoSharp 文件](~/graphics-games/urhosharp/index.md)包含有關如何建置 3D 場景和動作的詳細資訊。

<a name="4"/>

### <a name="4-add-the-charts-class-to-the-urhosurface"></a>4.將圖表類別新增至 UrhoSurface

使用`UrhoSurface.Show<T>`泛型方法新增至 Xamarin.Forms 頁面 Urho 應用程式。 下列程式碼片段示範建立所需的額外程式碼`Charts`類別：

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

注意︰`Show<T>`方法是非同步的而且應使用呼叫`await`關鍵字。

<a name="5"/>

### <a name="5-interacting-with-urhosharp"></a>5.互動使用 UrhoSharp

此範例會讓選取和修改圖表長條。 `Charts`類別會公開`Bars`和`SelectedBar`若要啟用這種互動。

每個 「 列 」 已選取項目事件的處理常式，後面加上`Charts`類別呈現，藉由反覆公開`Bars`集合：

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

事件處理常式會使用 Xamarin.Forms 的值`Slider`調整指定的列高度的控制項：

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

最後，連接兩個`Slider`控制項，好讓其值變更時 UrhoSharp 畫布會受到影響。 第一個滑桿會旋轉 3D 圖表影像，第二個滑桿調整所選取列的高度。

```csharp
rotationSlider = new Slider(0, 500, 250);
rotationSlider.ValueChanged += (s, e) => urhoApp?.Rotate((float)(e.NewValue - e.OldValue));

selectedBarSlider = new Slider(0, 5, 2.5);
selectedBarSlider.ValueChanged += OnValuesSliderValueChanged;
```

在動畫[頁面頂端](#what-is-urhosharp)顯示執行的範例。

## <a name="summary"></a>總結

此頁面會顯示如何將 3D 資料視覺效果新增至 Xamarin.Forms 使用 UrhoSharp。 讀取[UrhoSharp 文件](~/graphics-games/urhosharp/index.md)如需有關如何建置 Urho 場景，可以包含在 Xamarin.Forms 應用程式使用如上所示的方法。

## <a name="related-links"></a>相關連結

- [圖表上的範例 (C# 6)](https://github.com/xamarin/urho-samples/tree/master/FormsSample)
- [UrhoSharp](~/graphics-games/urhosharp/index.md)
