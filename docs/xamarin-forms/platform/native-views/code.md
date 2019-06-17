---
title: 在 C# 中的原生檢視
description: 從使用 C# 建立的 Xamarin.Forms 頁面，可直接參考原生 iOS、 Android 和 UWP 的檢視。 這篇文章會示範如何將原生檢視新增至 Xamarin.Forms 版面配置建立使用 C# 中，以及如何覆寫的自訂檢視，以更正其度量 API 使用方式配置。
ms.prod: xamarin
ms.assetid: 230F937C-F914-4B21-8EA1-1A2A9E644769
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: 8587cade1c5b4a6882f21603ee869f94f38fd04a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61085964"
---
# <a name="native-views-in-c"></a>在 C# 中的原生檢視

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/NativeEmbedding/)

_從使用 C# 建立的 Xamarin.Forms 頁面，可直接參考原生 iOS、 Android 和 UWP 的檢視。這篇文章會示範如何將原生檢視新增至 Xamarin.Forms 版面配置建立使用 C# 中，以及如何覆寫的自訂檢視，以更正其度量 API 使用方式配置。_

## <a name="overview"></a>總覽

可讓任何 Xamarin.Forms 控制項`Content`若要設定，或具有`Children`集合中，可以加入平台專用的檢視。 例如，iOS`UILabel`可以直接加入至[ `ContentView.Content` ](xref:Xamarin.Forms.ContentView.Content)屬性，或[ `StackLayout.Children` ](xref:Xamarin.Forms.Layout`1.Children)集合。 不過，請注意，這項功能需要使用`#if`定義 Xamarin.Forms 共用專案方案中，且無法使用從 Xamarin.Forms.NET Standard 程式庫的方案。

下列螢幕擷取畫面示範特定平台可讓您檢視具有已新增至 Xamarin.Forms [ `StackLayout` ](xref:Xamarin.Forms.StackLayout):

[![](code-images/screenshots-sml.png "包含平台專用的檢視 StackLayout")](code-images/screenshots.png#lightbox "StackLayout 包含平台專用的檢視")

將平台專用的檢視新增至 Xamarin.Forms 版面配置功能會啟用每個平台上的兩個擴充方法：

- `Add` -將平台專屬的檢視，來加入[ `Children` ](xref:Xamarin.Forms.Layout`1.Children)版面配置的集合。
- `ToView` – 會採用平台專屬檢視，並將它包裝成 Xamarin.Forms [ `View` ](xref:Xamarin.Forms.View)可以設定為`Content`控制項的屬性。

Xamarin.Forms 的共用專案中使用這些方法需要匯入適當的特定平台 Xamarin.Forms 命名空間：

- **iOS** – Xamarin.Forms.Platform.iOS
- **Android** – Xamarin.Forms.Platform.Android
- **通用 Windows 平台 (UWP)** – Xamarin.Forms.Platform.UWP

## <a name="adding-platform-specific-views-on-each-platform"></a>新增平台專用的檢視，每個平台

下列各節示範如何將平台專用的檢視加入至每個平台 Xamarin.Forms 版面配置。

### <a name="ios"></a>iOS

下列程式碼範例示範如何新增`UILabel`要[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)並[ `ContentView` ](xref:Xamarin.Forms.ContentView):

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

此範例假設`stackLayout`和`contentView`先前在 XAML 或 C# 中建立執行個體。

### <a name="android"></a>Android

下列程式碼範例示範如何新增`TextView`要[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)並[ `ContentView` ](xref:Xamarin.Forms.ContentView):

```csharp
var textView = new TextView (MainActivity.Instance) { Text = originalText, TextSize = 14 };
stackLayout.Children.Add (textView);
contentView.Content = textView.ToView();
```

此範例假設`stackLayout`和`contentView`先前在 XAML 或 C# 中建立執行個體。

### <a name="universal-windows-platform"></a>通用 Windows 平台

下列程式碼範例示範如何新增`TextBlock`要[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)並[ `ContentView` ](xref:Xamarin.Forms.ContentView):

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

此範例假設`stackLayout`和`contentView`先前在 XAML 或 C# 中建立執行個體。

## <a name="overriding-platform-measurements-for-custom-views"></a>覆寫自訂檢視的平台測量

每個平台上的自訂檢視通常只會正確實作它們的設計版面配置情節的度量。 比方說，自訂檢視可能已設計為只佔用裝置的可用寬度的一半。 不過之後要與其他使用者共用, 自訂檢視可能需要佔用裝置的完整可用寬度。 因此，它可以是必要時重複使用 Xamarin.Forms 版面配置中，覆寫的自訂檢視度量的實作。 基於這個理由，`Add`和`ToView`擴充方法可提供可測量的委派，指定可以覆寫的自訂檢視版面配置，當它新增至 Xamarin.Forms 版面配置覆寫。

下列各節示範如何覆寫的自訂檢視，更正其度量 API 使用方式配置。

### <a name="ios"></a>iOS

下列程式碼範例所示`CustomControl`類別，繼承自`UILabel`:

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

此檢視的執行個體加入至[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)，如下列程式碼範例所示：

```csharp
var customControl = new CustomControl {
  MinimumFontSize = 14,
  Lines = 0,
  LineBreakMode = UILineBreakMode.WordWrap,
  Text = "This control has incorrect sizing - there's empty space above and below it."
};
stackLayout.Children.Add (customControl);
```

不過，因為`CustomControl.SizeThatFits`覆寫一律會傳回高度為 150，檢視會顯示空白空間之上和之下，如下列螢幕擷取畫面所示：

![](code-images/ios-bad-measurement.png "iOS CustomControl 與 SizeThatFits 實作不正確")

此問題的解決方案是要提供`GetDesiredSizeDelegate`實作，如下列程式碼範例所示：

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

這個方法會使用所提供的寬度`CustomControl.SizeThatFits`方法，但會以替代的高度為 70 的 150 的高度。 當`CustomControl`執行個體新增至[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)，則`FixSize`方法可以指定為`GetDesiredSizeDelegate`若要修正錯誤所提供的度量`CustomControl`類別：

```csharp
stackLayout.Children.Add (customControl, FixSize);
```

這會導致空白空間之上和之下，不正確，顯示自訂檢視，如下列螢幕擷取畫面所示：

![](code-images/ios-good-measurement.png "iOS CustomControl 使用 GetDesiredSize 覆寫")

### <a name="android"></a>Android

下列程式碼範例所示`CustomControl`類別，繼承自`TextView`:

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

此檢視的執行個體加入至[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)，如下列程式碼範例所示：

```csharp
var customControl = new CustomControl (MainActivity.Instance) {
  Text = "This control has incorrect sizing - it doesn't occupy the available width of the device.",
  TextSize = 14
};
stackLayout.Children.Add (customControl);
```

不過，因為`CustomControl.OnMeasure`覆寫一律會傳回所要求的寬度的一半，檢視就會顯示佔用一半可用寬度的裝置，如下列螢幕擷取畫面所示：

![](code-images/android-bad-measurement.png "Android CustomControl 錯誤 OnMeasure 實作")

此問題的解決方案是要提供`GetDesiredSizeDelegate`實作，如下列程式碼範例所示：

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

這個方法會使用所提供的寬度`CustomControl.OnMeasure`方法，但將它乘以兩個。 當`CustomControl`執行個體新增至[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)，則`FixSize`方法可以指定為`GetDesiredSizeDelegate`若要修正錯誤所提供的度量`CustomControl`類別：

```csharp
stackLayout.Children.Add (customControl, FixSize);
```

這會導致自訂檢視所顯示正確、 佔用寬度的裝置，如下列螢幕擷取畫面所示：

![](code-images/android-good-measurement.png "Android CustomControl 與自訂 GetDesiredSize 委派")

### <a name="universal-windows-platform"></a>通用 Windows 平台

下列程式碼範例所示`CustomControl`類別，繼承自`Panel`:

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

此檢視的執行個體加入至[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)，如下列程式碼範例所示：

```csharp
var brokenControl = new CustomControl {
  Text = "This control has incorrect sizing - it doesn't occupy the available width of the device."
};
stackLayout.Children.Add(brokenControl);
```

不過，因為`CustomControl.ArrangeOverride`覆寫一律會傳回所要求的寬度的一半，檢視會裁剪為可用寬度的一半的裝置，如下列螢幕擷取畫面所示：

![](code-images/winrt-bad-measurement.png "UWP CustomControl 錯誤 ArrangeOverride 實作")

此問題的解決方案是要提供`ArrangeOverrideDelegate`實作中，新增至檢視時[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)，如下列程式碼範例所示：

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

這個方法會使用所提供的寬度`CustomControl.ArrangeOverride`方法，但將它乘以兩個。 這會導致自訂檢視所顯示正確、 佔用寬度的裝置，如下列螢幕擷取畫面所示：

![](code-images/winrt-good-measurement.png "UWP CustomControl ArrangeOverride 委派")

## <a name="summary"></a>總結

這篇文章說明如何將原生檢視新增至 Xamarin.Forms 版面配置建立使用 C# 中，以及如何覆寫的自訂檢視，以更正其度量 API 使用方式配置。


## <a name="related-links"></a>相關連結

- [NativeEmbedding （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/NativeEmbedding/)
- [原生格式](~/xamarin-forms/platform/native-forms.md)
