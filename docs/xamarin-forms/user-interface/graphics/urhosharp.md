---
title: 使用 Xamarin.Forms 中 UrhoSharp
description: UrhoSharp 可用於進階視覺效果應用程式中加入 3D 圖形
ms.prod: xamarin
ms.assetid: 0646B98E-CC04-4537-9715-9F82338FD7FF
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/11/2016
ms.openlocfilehash: 8421355e0630a637589cb4f08c2fec4ea9cdab24
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="using-urhosharp-in-xamarinforms"></a>使用 Xamarin.Forms 中 UrhoSharp

## <a name="what-is-urhosharp"></a>什麼是 UrhoSharp？

[UrhoSharp](~/graphics-games/urhosharp/index.md) Xamarin 和.NET 開發人員是功能強大的 3D 引擎。 [簡介](~/graphics-games/urhosharp/introduction.md)UrhoSharp 程式庫中，進一步說明和[這些附註](~/graphics-games/urhosharp/using.md)描述如何進行程式設計場景和動作。

UrhoSharp 可以用於呈現圖形 Xamarin.Forms 應用程式中。
這[範例](https://github.com/xamarin/urho-samples/tree/master/FormsSample)示範如何 UrhoSharp 無法用來建構互動式 3D 圖表：

![](urhosharp-images/ios-animation.gif "UrhoSharp 3D 互動式的圖表在 iOS 上")
![](urhosharp-images/android-animation.gif "UrhoSharp 3D 互動式的圖表在 Android 上")

## <a name="adding-the-urhosharp-nuget-packages"></a>新增 UrhoSharp Nuget 封裝

在使用之前 UrhoSharp，開發人員需要將 UrhoSharp Nuget 封裝加入至方案。 本指南假設在 Xamarin.Forms 專案中的使用 iOS、 Android 和 PCL 專案。 所有的程式碼會寫入 PCL 專案中但如果 UrhoSharp Nuget 必須新增到 iOS 和 Android 專案太。

UrhoSharp.Forms Nuget 封裝包含所有建立 UrhoSharp 物件所需的物件。 UrhoSharp.Forms nuget 套件會包含`UrhoSurface`用來裝載 UrhoSharp Xamarin.Forms 中的類別。
若要開始，以滑鼠右鍵按一下 PCL**封裝**資料夾，然後選取**新增套件...**.輸入搜尋字詞**UrhoSharp.Forms**，選取**Xamarin.Forms 的 UrhoSharp**，然後按一下 **加入封裝**。

[![](urhosharp-images/add-package-sml.png "新增套件 對話方塊")](urhosharp-images/add-package.png#lightbox "新增套件 對話方塊")

UrhoSharp.Forms NuGet 封裝將會加入至專案：

![](urhosharp-images/packages.png "封裝資料夾")

平台專屬專案中 （例如 iOS 和 Android） 重複上述步驟。

## <a name="walkthrough-adding-urhosharp-to-a-xamarinforms-app"></a>逐步解說： 加入 UrhoSharp Xamarin.Forms 應用程式

下列步驟說明 Xamarin.Forms UrhoSharp 範例中的程式碼：

1. [Creat Xamarin Forms 網頁](#1)
2. [新增 UrhoSurface](#2)
3. [建置 Urho 應用程式](#3)
4. [將圖表類別加入 UrhoSurface](#4)
5. [與 UrhoSharp 互動](#5)

請注意，此範例使用 C# 6 功能可能無法在舊版的 Visual Studio 上進行編譯。

<a name="1"/>

### <a name="1-create-a-xamarin-forms-page"></a>1.建立 Xamarin Forms 頁面

下列程式碼顯示 Xamarin.Forms 頁面`UrhoPage`在加入任何 Urho 相關的程式碼之前：

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
程式碼片段所示`UrhoSurface`Xamarin.Forms 頁面加入：

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

請參閱`Charts`類別的實作，此範例中使用的 Urho 3D 圖形。 顯示基本的程式碼大綱以下-請注意，類別會實作`Urho.Application`至不同`Xamarin.Forms.Application`類別中實作**App.cs**。

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

[UrhoSharp 文件](~/graphics-games/urhosharp/index.md)包含有關如何建立 3D 場景和動作的詳細資訊。

<a name="4"/>

### <a name="4-add-the-charts-class-to-the-urhosurface"></a>4.將圖表類別加入 UrhoSurface

使用`UrhoSurface.Show<T>`泛型的方法加入至 Xamarin.Forms 頁面 Urho 應用程式。 下列程式碼片段會顯示額外的程式碼，才能建立`Charts`類別：

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

注意：`Show<T>`方法為非同步且應該以呼叫`await`關鍵字。

<a name="5"/>

### <a name="5-interacting-with-urhosharp"></a>5.與 UrhoSharp 互動

此範例會讓圖表長條來選取和修改。 `Charts`類別會公開`Bars`和`SelectedBar`若要啟用這種互動。

每個 「 列 」 具有選取項目事件處理常式之後加入`Charts`已呈現類別，藉由重複處理公開`Bars`集合：

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

此事件處理常式會使用 Xamarin.Forms 值`Slider`控制項來調整指定軸的高度：

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

最後，連接兩個`Slider`控制項，好讓它們的值變更時 UrhoSharp 畫布會受到影響。 第一個滑桿旋轉的 3D 圖表影像，並且第二個滑桿調整選取列的高度。

```csharp
rotationSlider = new Slider(0, 500, 250);
rotationSlider.ValueChanged += (s, e) => urhoApp?.Rotate((float)(e.NewValue - e.OldValue));

selectedBarSlider = new Slider(0, 5, 2.5);
selectedBarSlider.ValueChanged += OnValuesSliderValueChanged;
```

在動畫[頁面頂端](#)顯示執行的範例。

## <a name="summary"></a>總結

此頁面會顯示如何 UrhoSharp 可用來將 3D 資料視覺效果加入至 Xamarin.Forms。 讀取[UrhoSharp 文件](~/graphics-games/urhosharp/index.md)如需有關如何建置 Xamarin.Forms 應用程式使用如上所示的方法中可包含的 Urho 場景。


## <a name="related-links"></a>相關連結

- [圖表範例 (C# 6)](https://github.com/xamarin/urho-samples/tree/master/FormsSample)
- [UrhoSharp](~/graphics-games/urhosharp/index.md)
