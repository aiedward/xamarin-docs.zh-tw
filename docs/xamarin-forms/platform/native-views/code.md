---
title: 在 C# 中的原生檢視
description: 原生 iOS、 Android 和 UWP 從檢視可以從使用 C# 建立 Xamarin.Forms 頁面直接參考。 本文示範如何將原生檢視加入到使用 C# 中，建立 Xamarin.Forms 版面配置，以及如何覆寫更正其測量應用程式開發介面使用的自訂檢視的版面配置。
ms.prod: xamarin
ms.assetid: 230F937C-F914-4B21-8EA1-1A2A9E644769
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: c3a79947b02e0f877fd4ea1b0ddb72486c222719
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2018
ms.locfileid: "34050048"
---
# <a name="native-views-in-c"></a>在 C# 中的原生檢視

_原生 iOS、 Android 和 UWP 從檢視可以從使用 C# 建立 Xamarin.Forms 頁面直接參考。本文示範如何將原生檢視加入到使用 C# 中，建立 Xamarin.Forms 版面配置，以及如何覆寫更正其測量應用程式開發介面使用的自訂檢視的版面配置。_

## <a name="overview"></a>總覽

可讓任何 Xamarin.Forms 控制項`Content`以設定，或具有`Children`集合，可以加入平台專屬檢視。 例如，iOS`UILabel`可以直接加入至[ `ContentView.Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ContentView.Content/)屬性，或[ `StackLayout.Children` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout%3CT%3E.Children/)集合。 不過，請注意這項功能需要使用`#if`Xamarin.Forms 共用專案方案中定義，而且無法使用從 Xamarin.Forms.NET 標準程式庫的方案。

下列螢幕擷取畫面示範平台專屬檢視具有已新增至 Xamarin.Forms [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/):

[![](code-images/screenshots-sml.png "包含平台專屬檢視 StackLayout")](code-images/screenshots.png#lightbox "StackLayout 包含平台專屬檢視")

將平台專屬的檢視加入至 Xamarin.Forms 版面配置功能會啟用在每個平台上的兩種擴充方法：

- `Add` – 加入至特定平台檢視[ `Children` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout%3CT%3E.Children/)配置的集合。
- `ToView` – 採用平台專屬檢視，並將它包裝成 Xamarin.Forms [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)可設定為`Content`控制項的屬性。

Xamarin.Forms 共用專案中使用這些方法需要匯入適當的平台專屬 Xamarin.Forms 命名空間：

- **iOS** – Xamarin.Forms.Platform.iOS
- **Android** – Xamarin.Forms.Platform.Android
- **通用 Windows 平台 (UWP)** – Xamarin.Forms.Platform.UWP

## <a name="adding-platform-specific-views-on-each-platform"></a>加入平台專屬檢視每個平台

下列章節將說明如何將平台專屬的檢視加入至每個平台上的 Xamarin.Forms 版面配置。

### <a name="ios"></a>iOS

下列程式碼範例示範如何將`UILabel`至[ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)和[ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/):

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

此範例假設`stackLayout`和`contentView`先前 XAML 或 C# 中建立執行個體。

### <a name="android"></a>Android

下列程式碼範例示範如何將`TextView`至[ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)和[ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/):

```csharp
var textView = new TextView (MainActivity.Instance) { Text = originalText, TextSize = 14 };
stackLayout.Children.Add (textView);
contentView.Content = textView.ToView();
```

此範例假設`stackLayout`和`contentView`先前 XAML 或 C# 中建立執行個體。

### <a name="universal-windows-platform"></a>通用 Windows 平台

下列程式碼範例示範如何將`TextBlock`至[ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)和[ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/):

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

此範例假設`stackLayout`和`contentView`先前 XAML 或 C# 中建立執行個體。

## <a name="overriding-platform-measurements-for-custom-views"></a>覆寫自訂檢視的平台測量

每個平台上的自訂檢視通常只正確實作它們的設計的版面配置實例的度量。 例如，自訂檢視可能已設計，以只佔用裝置的可用寬度的一半。 不過之後要與其他使用者共用, 的自訂檢視可能需要佔用完整可用寬度的裝置。 因此，可能需要時重複使用 Xamarin.Forms 配置中，覆寫的自訂檢視度量實作。 基於這個原因，`Add`和`ToView`擴充方法可提供讓度量委派指定，會加入至 Xamarin.Forms 配置時可以覆寫自訂檢視配置的覆寫。

下列章節示範如何覆寫的自訂檢視，更正其測量應用程式開發介面使用方式配置。

### <a name="ios"></a>iOS

下列程式碼範例示範`CustomControl`類別，繼承自`UILabel`:

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

此檢視執行個體加入至[ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)，如下列程式碼範例所示：

```csharp
var customControl = new CustomControl {
  MinimumFontSize = 14,
  Lines = 0,
  LineBreakMode = UILineBreakMode.WordWrap,
  Text = "This control has incorrect sizing - there's empty space above and below it."
};
stackLayout.Children.Add (customControl);
```

不過，因為`CustomControl.SizeThatFits`覆寫一律會傳回高度為 150、 檢視會顯示空白空間之上和之下，如下列螢幕擷取畫面所示：

![](code-images/ios-bad-measurement.png "iOS CustomControl 錯誤 SizeThatFits 實作")

此問題的解決方案是要提供`GetDesiredSizeDelegate`實作中，如下列程式碼範例所示：

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

這個方法會使用所提供的寬度`CustomControl.SizeThatFits`方法，但會替代 150 高度為 70 的高度。 當`CustomControl`執行個體新增至[ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)、`FixSize`方法可以指定為`GetDesiredSizeDelegate`修正錯誤所提供的度量`CustomControl`類別：

```csharp
stackLayout.Children.Add (customControl, FixSize);
```

這會導致空的空間之上和之下，不正確，顯示的自訂檢視，如下列螢幕擷取畫面所示：

![](code-images/ios-good-measurement.png "iOS CustomControl 與 GetDesiredSize 覆寫")

### <a name="android"></a>Android

下列程式碼範例示範`CustomControl`類別，繼承自`TextView`:

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

此檢視執行個體加入至[ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)，如下列程式碼範例所示：

```csharp
var customControl = new CustomControl (MainActivity.Instance) {
  Text = "This control has incorrect sizing - it doesn't occupy the available width of the device.",
  TextSize = 14
};
stackLayout.Children.Add (customControl);
```

不過，因為`CustomControl.OnMeasure`覆寫一律會傳回要求的寬度的一半，檢視便會顯示佔用只可用寬度的一半裝置，如下列螢幕擷取畫面所示：

![](code-images/android-bad-measurement.png "Android CustomControl 錯誤 OnMeasure 實作")

此問題的解決方案是要提供`GetDesiredSizeDelegate`實作中，如下列程式碼範例所示：

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

這個方法會使用所提供的寬度`CustomControl.OnMeasure`方法，但將它乘以兩個。 當`CustomControl`執行個體新增至[ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)、`FixSize`方法可以指定為`GetDesiredSizeDelegate`修正錯誤所提供的度量`CustomControl`類別：

```csharp
stackLayout.Children.Add (customControl, FixSize);
```

這會導致自訂檢視所顯示正確，佔用寬度的裝置，如下列螢幕擷取畫面所示：

![](code-images/android-good-measurement.png "Android CustomControl 與自訂 GetDesiredSize 委派")

### <a name="universal-windows-platform"></a>通用 Windows 平台

下列程式碼範例示範`CustomControl`類別，繼承自`Panel`:

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

此檢視執行個體加入至[ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)，如下列程式碼範例所示：

```csharp
var brokenControl = new CustomControl {
  Text = "This control has incorrect sizing - it doesn't occupy the available width of the device."
};
stackLayout.Children.Add(brokenControl);
```

不過，因為`CustomControl.ArrangeOverride`覆寫一律會傳回要求的寬度的一半，檢視會遭到裁剪以可用寬度的一半裝置，如下列螢幕擷取畫面所示：

![](code-images/winrt-bad-measurement.png "UWP CustomControl 錯誤 ArrangeOverride 實作")

此問題的解決方案是要提供`ArrangeOverrideDelegate`實作中，加入至檢視時[ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)，如下列程式碼範例所示：

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

這個方法會使用所提供的寬度`CustomControl.ArrangeOverride`方法，但將它乘以兩個。 這會導致自訂檢視所顯示正確，佔用寬度的裝置，如下列螢幕擷取畫面所示：

![](code-images/winrt-good-measurement.png "UWP CustomControl ArrangeOverride 委派")

## <a name="summary"></a>總結

本文說明如何將原生檢視加入到使用 C# 中，建立 Xamarin.Forms 版面配置，以及如何覆寫更正其測量應用程式開發介面使用的自訂檢視的版面配置。


## <a name="related-links"></a>相關連結

- [NativeEmbedding （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/NativeEmbedding/)
- [原生格式](~/xamarin-forms/platform/native-forms.md)
