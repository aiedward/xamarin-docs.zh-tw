---
title: C# 中的原生檢視
description: 'IOS、Android 和 UWP 的原生視圖可以直接從 Xamarin.Forms 使用 c # 建立的頁面參考。 本文示範如何將原生視圖加入 Xamarin.Forms 使用 c # 建立的配置，以及如何覆寫自訂視圖的配置以更正其測量 API 的使用方式。'
ms.prod: xamarin
ms.assetid: 230F937C-F914-4B21-8EA1-1A2A9E644769
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4cad46bdee1b49c316947bc56bdb69a3b9e9a270
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86938204"
---
# <a name="native-views-in-c"></a>C 中的原生視圖\#

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeembedding)

_IOS、Android 和 UWP 的原生視圖可以直接從 Xamarin.Forms 使用 c # 建立的頁面參考。本文示範如何將原生視圖加入 Xamarin.Forms 使用 c # 建立的配置，以及如何覆寫自訂視圖的配置以更正其測量 API 的使用方式。_

## <a name="overview"></a>概觀

Xamarin.Forms允許 `Content` 設定或具有集合的任何控制項， `Children` 都可以加入平臺特定的視圖。 例如，iOS `UILabel` 可以直接加入至 [`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content) 屬性或 [`StackLayout.Children`](xref:Xamarin.Forms.Layout`1.Children) 集合。 不過，請注意，這項功能需要在 `#if` Xamarin.Forms 共用專案解決方案中使用定義，而且無法從 Xamarin.Forms .NET Standard 程式庫方案取得。

下列螢幕擷取畫面示範已新增至的平臺特定視圖 Xamarin.Forms [`StackLayout`](xref:Xamarin.Forms.StackLayout) ：

[![包含平臺特定視圖的 StackLayout](code-images/screenshots-sml.png)](code-images/screenshots.png#lightbox "包含平臺特定視圖的 StackLayout")

將平臺特定的視圖加入至版面配置的功能 Xamarin.Forms ，是由每個平臺上的兩個擴充方法所啟用：

- `Add`–將平臺特定的視圖加入至配置的 [`Children`](xref:Xamarin.Forms.Layout`1.Children) 集合。
- `ToView`–採用平臺特定的視圖，並將其包裝為 Xamarin.Forms [`View`](xref:Xamarin.Forms.View) 可設定為 `Content` 控制項屬性的。

在共用專案中使用這些方法 Xamarin.Forms 需要匯入適當的平臺特定 Xamarin.Forms 命名空間：

- **iOS** – Xamarin.Forms 。平臺 iOS
- **Android** – Xamarin.Forms 。Platform. Android
- **通用 Windows 平臺（UWP）** – Xamarin.Forms 。平臺. UWP

## <a name="adding-platform-specific-views-on-each-platform"></a>在每個平臺上新增平臺特定的視圖

下列各節示範如何 Xamarin.Forms 在每個平臺上的版面配置中新增平臺特定的視圖。

### <a name="ios"></a>iOS

下列程式碼範例示範如何將新增 `UILabel` 至 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 和 [`ContentView`](xref:Xamarin.Forms.ContentView) ：

```csharp
var uiLabel = new UILabel {
  MinimumFontSize = 14f,
  Lines = 0,
  LineBreakMode = UILineBreakMode.WordWrap,
  Text = originalText,
};
stackLayout.Children.Add (uiLabel);
contentView.Content = uiLabel.ToView();
```

此範例假設 `stackLayout` `contentView` 先前已使用 XAML 或 c # 建立和實例。

### <a name="android"></a>Android

下列程式碼範例示範如何將新增 `TextView` 至 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 和 [`ContentView`](xref:Xamarin.Forms.ContentView) ：

```csharp
var textView = new TextView (MainActivity.Instance) { Text = originalText, TextSize = 14 };
stackLayout.Children.Add (textView);
contentView.Content = textView.ToView();
```

此範例假設 `stackLayout` `contentView` 先前已使用 XAML 或 c # 建立和實例。

### <a name="universal-windows-platform"></a>通用 Windows 平台

下列程式碼範例示範如何將新增 `TextBlock` 至 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 和 [`ContentView`](xref:Xamarin.Forms.ContentView) ：

```csharp
var textBlock = new TextBlock
{
    Text = originalText,
    FontSize = 14,
    FontFamily = new FontFamily("HelveticaNeue"),
    TextWrapping = TextWrapping.Wrap
};
stackLayout.Children.Add(textBlock);
contentView.Content = textBlock.ToView();
```

此範例假設 `stackLayout` `contentView` 先前已使用 XAML 或 c # 建立和實例。

## <a name="overriding-platform-measurements-for-custom-views"></a>覆寫自訂視圖的平臺測量

每個平臺上的自訂視圖通常只會正確地針對其設計的版面配置案例執行測量。 例如，自訂視圖可能已設計成隻佔用一半的裝置可用寬度。 不過，在與其他使用者共用之後，自訂視圖可能需要佔用裝置的完整可用寬度。 因此，在版面配置中重複使用時，可能需要覆寫自訂視圖量測實 Xamarin.Forms 。 基於這個理由， `Add` 和 `ToView` 擴充方法會提供允許指定測量委派的覆寫，這可在將自訂視圖配置新增至版面配置時，予以覆寫 Xamarin.Forms 。

下列各節示範如何覆寫自訂視圖的配置，以更正其測量 API 的使用方式。

### <a name="ios"></a>iOS

下列程式碼範例顯示 `CustomControl` 類別，其繼承自 `UILabel` ：

```csharp
public class CustomControl : UILabel
{
  public override string Text {
    get { return base.Text; }
    set { base.Text = value.ToUpper (); }
  }

  public override CGSize SizeThatFits (CGSize size)
  {
    return new CGSize (size.Width, 150);
  }
}
```

此視圖的實例會新增至 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ，如下列程式碼範例所示：

```csharp
var customControl = new CustomControl {
  MinimumFontSize = 14,
  Lines = 0,
  LineBreakMode = UILineBreakMode.WordWrap,
  Text = "This control has incorrect sizing - there's empty space above and below it."
};
stackLayout.Children.Add (customControl);
```

不過，因為覆 `CustomControl.SizeThatFits` 寫一律會傳回高度150，所以此視圖會顯示在其上方和下方的空格，如下列螢幕擷取畫面所示：

![具有錯誤 SizeThatFits 執行的 iOS CustomControl](code-images/ios-bad-measurement.png)

此問題的解決方法是提供 `GetDesiredSizeDelegate` 執行，如下列程式碼範例所示：

```csharp
SizeRequest? FixSize (NativeViewWrapperRenderer renderer, double width, double height)
{
  var uiView = renderer.Control;

  if (uiView == null) {
    return null;
  }

  var constraint = new CGSize (width, height);

  // Let the CustomControl determine its size (which will be wrong)
  var badRect = uiView.SizeThatFits (constraint);

  // Use the width and substitute the height
  return new SizeRequest (new Size (badRect.Width, 70));
}
```

這個方法會使用方法所提供的寬度 `CustomControl.SizeThatFits` ，但會將高度的150高度替換為70。 將 `CustomControl` 實例加入至時 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ， `FixSize` 可以將方法指定為， `GetDesiredSizeDelegate` 以修正類別所提供的錯誤測量 `CustomControl` ：

```csharp
stackLayout.Children.Add (customControl, FixSize);
```

這會導致正確顯示自訂視圖，而不含其上方和下方的空白空間，如下列螢幕擷取畫面所示：

![使用 GetDesiredSize 覆寫的 iOS CustomControl](code-images/ios-good-measurement.png)

### <a name="android"></a>Android

下列程式碼範例顯示 `CustomControl` 類別，其繼承自 `TextView` ：

```csharp
public class CustomControl : TextView
{
  public CustomControl (Context context) : base (context)
  {
  }

  protected override void OnMeasure (int widthMeasureSpec, int heightMeasureSpec)
  {
    int width = MeasureSpec.GetSize (widthMeasureSpec);

    // Force the width to half of what's been requested.
    // This is deliberately wrong to demonstrate providing an override to fix it with.
    int widthSpec = MeasureSpec.MakeMeasureSpec (width / 2, MeasureSpec.GetMode (widthMeasureSpec));

    base.OnMeasure (widthSpec, heightMeasureSpec);
  }
}
```

此視圖的實例會新增至 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ，如下列程式碼範例所示：

```csharp
var customControl = new CustomControl (MainActivity.Instance) {
  Text = "This control has incorrect sizing - it doesn't occupy the available width of the device.",
  TextSize = 14
};
stackLayout.Children.Add (customControl);
```

不過，因為覆 `CustomControl.OnMeasure` 寫一律會傳回所要求寬度的一半，所以會顯示只佔用一半的裝置寬度，如下列螢幕擷取畫面所示：

![具有錯誤 OnMeasure 執行的 Android CustomControl](code-images/android-bad-measurement.png)

此問題的解決方法是提供 `GetDesiredSizeDelegate` 執行，如下列程式碼範例所示：

```csharp
SizeRequest? FixSize (NativeViewWrapperRenderer renderer, int widthConstraint, int heightConstraint)
{
  var nativeView = renderer.Control;

  if ((widthConstraint == 0 && heightConstraint == 0) || nativeView == null) {
    return null;
  }

  int width = Android.Views.View.MeasureSpec.GetSize (widthConstraint);
  int widthSpec = Android.Views.View.MeasureSpec.MakeMeasureSpec (
    width * 2, Android.Views.View.MeasureSpec.GetMode (widthConstraint));
  nativeView.Measure (widthSpec, heightConstraint);
  return new SizeRequest (new Size (nativeView.MeasuredWidth, nativeView.MeasuredHeight));
}
```

這個方法會使用方法所提供的寬度 `CustomControl.OnMeasure` ，但會將它乘以二。 將 `CustomControl` 實例加入至時 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ， `FixSize` 可以將方法指定為， `GetDesiredSizeDelegate` 以修正類別所提供的錯誤測量 `CustomControl` ：

```csharp
stackLayout.Children.Add (customControl, FixSize);
```

這會導致正確顯示自訂視圖，佔用裝置的寬度，如下列螢幕擷取畫面所示：

![使用自訂 GetDesiredSize 委派的 Android CustomControl](code-images/android-good-measurement.png)

### <a name="universal-windows-platform"></a>通用 Windows 平台

下列程式碼範例顯示 `CustomControl` 類別，其繼承自 `Panel` ：

```csharp
public class CustomControl : Panel
{
  public static readonly DependencyProperty TextProperty =
    DependencyProperty.Register(
      "Text", typeof(string), typeof(CustomControl), new PropertyMetadata(default(string), OnTextPropertyChanged));

  public string Text
  {
    get { return (string)GetValue(TextProperty); }
    set { SetValue(TextProperty, value.ToUpper()); }
  }

  readonly TextBlock textBlock;

  public CustomControl()
  {
    textBlock = new TextBlock
    {
      MinHeight = 0,
      MaxHeight = double.PositiveInfinity,
      MinWidth = 0,
      MaxWidth = double.PositiveInfinity,
      FontSize = 14,
      TextWrapping = TextWrapping.Wrap,
      VerticalAlignment = VerticalAlignment.Center
    };

    Children.Add(textBlock);
  }

  static void OnTextPropertyChanged(DependencyObject dependencyObject, DependencyPropertyChangedEventArgs args)
  {
    ((CustomControl)dependencyObject).textBlock.Text = (string)args.NewValue;
  }

  protected override Size ArrangeOverride(Size finalSize)
  {
      // This is deliberately wrong to demonstrate providing an override to fix it with.
      textBlock.Arrange(new Rect(0, 0, finalSize.Width/2, finalSize.Height));
      return finalSize;
  }

  protected override Size MeasureOverride(Size availableSize)
  {
      textBlock.Measure(availableSize);
      return new Size(textBlock.DesiredSize.Width, textBlock.DesiredSize.Height);
  }
}
```

此視圖的實例會新增至 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ，如下列程式碼範例所示：

```csharp
var brokenControl = new CustomControl {
  Text = "This control has incorrect sizing - it doesn't occupy the available width of the device."
};
stackLayout.Children.Add(brokenControl);
```

不過，因為覆 `CustomControl.ArrangeOverride` 寫一律會傳回所要求寬度的一半，所以此視圖會裁剪成裝置可用寬度的一半，如下列螢幕擷取畫面所示：

![具有錯誤 ArrangeOverride 執行的 UWP CustomControl](code-images/winrt-bad-measurement.png)

這個問題的解決方法是在 `ArrangeOverrideDelegate` 將視圖加入至時提供一個實作為， [`StackLayout`](xref:Xamarin.Forms.StackLayout) 如下列程式碼範例所示：

```csharp
stackLayout.Children.Add(fixedControl, arrangeOverrideDelegate: (renderer, finalSize) =>
{
    if (finalSize.Width <= 0 || double.IsInfinity(finalSize.Width))
    {
        return null;
    }
    var frameworkElement = renderer.Control;
    frameworkElement.Arrange(new Rect(0, 0, finalSize.Width * 2, finalSize.Height));
    return finalSize;
});
```

這個方法會使用方法所提供的寬度 `CustomControl.ArrangeOverride` ，但會將它乘以二。 這會導致正確顯示自訂視圖，佔用裝置的寬度，如下列螢幕擷取畫面所示：

![具有 ArrangeOverride 委派的 UWP CustomControl](code-images/winrt-good-measurement.png)

## <a name="summary"></a>總結

本文說明如何將原生視圖加入 Xamarin.Forms 使用 c # 建立的配置，以及如何覆寫自訂視圖的配置以更正其測量 API 的使用方式。

## <a name="related-links"></a>相關連結

- [NativeEmbedding （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeembedding)
- [原生表單](~/xamarin-forms/platform/native-forms.md)
