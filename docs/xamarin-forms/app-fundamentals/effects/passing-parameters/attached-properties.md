---
title：「將效果參數當做附加屬性傳遞」描述：「附加屬性可以用來定義會回應執行時間屬性變更的效果參數。 本文示範如何使用附加屬性將參數傳遞至效果，並在執行時間變更參數。
assetid： DFCDCB9F-17DD-4117-BD53-B4FB206BB387 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. date： 08/05/2016 no-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="passing-effect-parameters-as-attached-properties"></a>將效果參數傳遞為附加屬性

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-shadoweffectruntimechange)

_附加屬性可以用來定義會回應執行時間屬性變更的效果參數。本文示範如何使用附加屬性將參數傳遞至效果，以及在執行時間變更參數。_

建立會回應執行階段屬性變更之效果參數的程序如下：

1. 建立 `static` 類別，其中包含要傳遞至效果之每個參數的附加屬性。
1. 將其他附加屬性新增至類別，該類別將用於控制新增或移除對該類別將會附加至之控制項的效果。 請確保此附加屬性會註冊 `propertyChanged` 委派；當屬性值變更時，就會執行此委派。
1. 建立每個附加屬性的 `static` getter 和 setter。
1. 實作 `propertyChanged` 委派中的邏輯，以新增和移除效果。
1. 在 `static` 類別內實作巢狀類別，該類別是 `RoutingEffect` 類別的子類別，以效果來命名。 針對建構函式，呼叫基底類別建構函式，並傳入解析群組名稱串連，以及每個平台特定效果類別所指定的唯一識別碼。

參數可藉由將附加屬性和屬性值新增至適當的控制項中來傳遞效果。 此外，參數可以藉由指定新的附加屬性值，在執行階段變更。

> [!NOTE]
> 附加屬性是可繫結屬性的特殊類型，定義於一個類別但附加至其他物件，而且在 XAML 中會識別為包含類別和屬性名稱並以句號分隔的屬性。 如需詳細資訊，請參閱[附加屬性](~/xamarin-forms/xaml/attached-properties.md)。

範例應用程式會示範 `ShadowEffect` ，它會將陰影新增至控制項所顯示的文字 [`Label`](xref:Xamarin.Forms.Label) 。 此外，陰影的色彩可以在執行階段變更。 下圖說明範例應用程式中每個專案的責任，以及它們之間的關聯性：

![](attached-properties-images/shadow-effect.png "Shadow Effect Project Responsibilities")

[`Label`](xref:Xamarin.Forms.Label)上的控制項 `HomePage` 是由 `LabelShadowEffect` 每個平臺特定專案中的所自訂。 透過 `ShadowEffect` 類別中的附加屬性，將參數傳遞至每個 `LabelShadowEffect`。 每個 `LabelShadowEffect` 類別都衍生自每個平台的 `PlatformEffect` 類別。 這會導致將陰影新增至 `Label` 控制項所顯示的文字，如下列螢幕擷取畫面所示：

![](attached-properties-images/screenshots.png "Shadow Effect on each Platform")

## <a name="creating-effect-parameters"></a>建立效果參數

應建立代表效果參數的 `static` 類別，如下列程式碼範例所示：

```csharp
public static class ShadowEffect
{
  public static readonly BindableProperty HasShadowProperty =
    BindableProperty.CreateAttached ("HasShadow", typeof(bool), typeof(ShadowEffect), false, propertyChanged: OnHasShadowChanged);
  public static readonly BindableProperty ColorProperty =
    BindableProperty.CreateAttached ("Color", typeof(Color), typeof(ShadowEffect), Color.Default);
  public static readonly BindableProperty RadiusProperty =
    BindableProperty.CreateAttached ("Radius", typeof(double), typeof(ShadowEffect), 1.0);
  public static readonly BindableProperty DistanceXProperty =
    BindableProperty.CreateAttached ("DistanceX", typeof(double), typeof(ShadowEffect), 0.0);
  public static readonly BindableProperty DistanceYProperty =
    BindableProperty.CreateAttached ("DistanceY", typeof(double), typeof(ShadowEffect), 0.0);

  public static bool GetHasShadow (BindableObject view)
  {
    return (bool)view.GetValue (HasShadowProperty);
  }

  public static void SetHasShadow (BindableObject view, bool value)
  {
    view.SetValue (HasShadowProperty, value);
  }
  ...

  static void OnHasShadowChanged (BindableObject bindable, object oldValue, object newValue)
  {
    var view = bindable as View;
    if (view == null) {
      return;
    }

    bool hasShadow = (bool)newValue;
    if (hasShadow) {
      view.Effects.Add (new LabelShadowEffect ());
    } else {
      var toRemove = view.Effects.FirstOrDefault (e => e is LabelShadowEffect);
      if (toRemove != null) {
        view.Effects.Remove (toRemove);
      }
    }
  }

  class LabelShadowEffect : RoutingEffect
  {
    public LabelShadowEffect () : base ("MyCompany.LabelShadowEffect")
    {
    }
  }
}
```

`ShadowEffect` 包含五個附加屬性，每個附加屬性都具有 `static` getter 和 setter。 其中四個屬性代表要傳遞至每個平台特定 `LabelShadowEffect` 的參數。 `ShadowEffect` 類別也會定義 `HasShadow` 附加屬性，該附加屬性會用於控制附加至控制項之 `ShadowEffect` 類別效果的新增或移除。 此附加屬性會註冊 `OnHasShadowChanged` 方法；當屬性的值變更時，就會執行此方法。 此方法會根據 `HasShadow` 附加屬性的值來新增或移除效果。

類別子類別的嵌套 `LabelShadowEffect` 類別 [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect) 支援新增和移除效果。 `RoutingEffect` 類別代表包裝通常是平台特定之內部效果的平台獨立效果。 這可簡化效果移除程序，因為對於平台特定效果，並不存在對類型資訊的編譯時間資訊存取。 `LabelShadowEffect` 建構函式會呼叫基底類別建構函式，並傳入由解析群組名稱串連組成的參數，以及每個平台特定效果類別所指定的唯一識別碼。 這會在 `OnHasShadowChanged` 方法中提供效果的新增和移除，如下所示：

- **效果新增** – `LabelShadowEffect` 的新執行個體會新增至控制項的 [`Effects`](xref:Xamarin.Forms.Element.Effects) 集合。 這會取代使用 [`Effect.Resolve`](xref:Xamarin.Forms.Effect.Resolve(System.String)) 方法來新增效果。
- **效果移除** – 會擷取和移除 [`Effects`](xref:Xamarin.Forms.Element.Effects) 集合中 `LabelShadowEffect` 控制項的第一個執行個體。

## <a name="consuming-the-effect"></a>使用效果

您 `LabelShadowEffect` 可以藉由將附加屬性新增至控制項來使用每個平臺特定 [`Label`](xref:Xamarin.Forms.Label) ，如下列 XAML 程式碼範例所示：

```xaml
<Label Text="Label Shadow Effect" ...
       local:ShadowEffect.HasShadow="true" local:ShadowEffect.Radius="5"
       local:ShadowEffect.DistanceX="5" local:ShadowEffect.DistanceY="5">
  <local:ShadowEffect.Color>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="Black" />
        <On Platform="Android" Value="White" />
        <On Platform="UWP" Value="Red" />
    </OnPlatform>
  </local:ShadowEffect.Color>
</Label>
```

[`Label`](xref:Xamarin.Forms.Label)下列程式碼範例顯示 c # 中的對等用法：

```csharp
var label = new Label {
  Text = "Label Shadow Effect",
  ...
};

Color color = Color.Default;
switch (Device.RuntimePlatform)
{
    case Device.iOS:
        color = Color.Black;
        break;
    case Device.Android:
        color = Color.White;
        break;
    case Device.UWP:
        color = Color.Red;
        break;
}

ShadowEffect.SetHasShadow (label, true);
ShadowEffect.SetRadius (label, 5);
ShadowEffect.SetDistanceX (label, 5);
ShadowEffect.SetDistanceY (label, 5);
ShadowEffect.SetColor (label, color));
```

將 `ShadowEffect.HasShadow` 附加屬性設為 `true` 會執行 `ShadowEffect.OnHasShadowChanged` 方法，以將加入或移除 `LabelShadowEffect` 至 [`Label`](xref:Xamarin.Forms.Label) 控制項。 在這兩個程式碼範例中，`ShadowEffect.Color` 附加屬性會提供平台特定的色彩值。 如需詳細資訊，請參閱[裝置類別](~/xamarin-forms/platform/device.md)。

此外， [`Button`](xref:Xamarin.Forms.Button) 允許在執行時間變更陰影色彩。 按一下 `Button` 時，下列程式碼會藉由設定 `ShadowEffect.Color` 附加屬性來變更陰影色彩：

```csharp
ShadowEffect.SetColor (label, Color.Teal);
```

### <a name="consuming-the-effect-with-a-style"></a>透過樣式使用效果

效果可以透過將附加屬性新增至控制項來使用，也可以由樣式使用。 下列 XAML 程式碼範例顯示陰影效果的「明確」** 樣式，該陰影效果可以套用至 [`Label`](xref:Xamarin.Forms.Label) 控制項：

```xaml
<Style x:Key="ShadowEffectStyle" TargetType="Label">
  <Style.Setters>
    <Setter Property="local:ShadowEffect.HasShadow" Value="True" />
    <Setter Property="local:ShadowEffect.Radius" Value="5" />
    <Setter Property="local:ShadowEffect.DistanceX" Value="5" />
    <Setter Property="local:ShadowEffect.DistanceY" Value="5" />
  </Style.Setters>
</Style>
```

[`Style`](xref:Xamarin.Forms.Style)可以套用至 [`Label`](xref:Xamarin.Forms.Label) ，方法是 [`Style`](xref:Xamarin.Forms.NavigableElement.Style) 使用標記延伸將其屬性設為 `Style` 實例 `StaticResource` ，如下列程式碼範例所示：

```xaml
<Label Text="Label Shadow Effect" ... Style="{StaticResource ShadowEffectStyle}" />
```

如需樣式的詳細資訊，請參閱[樣式](~/xamarin-forms/user-interface/styles/index.md)。

## <a name="creating-the-effect-on-each-platform"></a>在每個平台上建立效果

下列各節會討論 `LabelShadowEffect` 類別的平台特定實作。

### <a name="ios-project"></a>iOS 專案

下列程式碼範例示範 iOS 專案的 `LabelShadowEffect` 實作：

```csharp
[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.iOS
{
    public class LabelShadowEffect : PlatformEffect
    {
        protected override void OnAttached ()
        {
            try {
                UpdateRadius ();
                UpdateColor ();
                UpdateOffset ();
                Control.Layer.ShadowOpacity = 1.0f;
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
        ...

        void UpdateRadius ()
        {
            Control.Layer.ShadowRadius = (nfloat)ShadowEffect.GetRadius (Element);
        }

        void UpdateColor ()
        {
            Control.Layer.ShadowColor = ShadowEffect.GetColor (Element).ToCGColor ();
        }

        void UpdateOffset ()
        {
            Control.Layer.ShadowOffset = new CGSize (
                (double)ShadowEffect.GetDistanceX (Element),
                (double)ShadowEffect.GetDistanceY (Element));
        }
    }
```

`OnAttached` 方法會呼叫使用 `ShadowEffect` getter 來擷取附加屬性值的方法，將 `Control.Layer` 屬性設為屬性值以建立陰影。 這項功能會包裝在 `try`/`catch` 區塊中，以免效果附加至的控制項沒有 `Control.Layer` 屬性。 因為沒有必要的清除，所以 `OnDetached` 方法不提供實作。

#### <a name="responding-to-property-changes"></a>回應屬性變更

如果任何 `ShadowEffect` 附加屬性值在執行階段變更，則效果需要透過顯示變更來回應。 在平台特定的效果類別中，`OnElementPropertyChanged` 方法的覆寫版本是回應可繫結屬性變更的位置，如下列程式碼範例所示：

```csharp
public class LabelShadowEffect : PlatformEffect
{
  ...
  protected override void OnElementPropertyChanged (PropertyChangedEventArgs args)
  {
    if (args.PropertyName == ShadowEffect.RadiusProperty.PropertyName) {
      UpdateRadius ();
    } else if (args.PropertyName == ShadowEffect.ColorProperty.PropertyName) {
      UpdateColor ();
    } else if (args.PropertyName == ShadowEffect.DistanceXProperty.PropertyName ||
               args.PropertyName == ShadowEffect.DistanceYProperty.PropertyName) {
      UpdateOffset ();
    }
  }
  ...
}
```

`OnElementPropertyChanged` 方法會更新陰影的半徑、色彩或位移，前提是已變更適當的 `ShadowEffect` 附加屬性值。 因為此覆寫會呼叫多次，所以請一律檢查變更的屬性。

### <a name="android-project"></a>Android 專案

下列程式碼範例示範 Android 專案的 `LabelShadowEffect` 實作：

```csharp
[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.Droid
{
    public class LabelShadowEffect : PlatformEffect
    {
        Android.Widget.TextView control;
        Android.Graphics.Color color;
        float radius, distanceX, distanceY;

        protected override void OnAttached ()
        {
            try {
                control = Control as Android.Widget.TextView;
                UpdateRadius ();
                UpdateColor ();
                UpdateOffset ();
                UpdateControl ();
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
        ...

        void UpdateControl ()
        {
            if (control != null) {
                control.SetShadowLayer (radius, distanceX, distanceY, color);
            }
        }

        void UpdateRadius ()
        {
            radius = (float)ShadowEffect.GetRadius (Element);
        }

        void UpdateColor ()
        {
            color = ShadowEffect.GetColor (Element).ToAndroid ();
        }

        void UpdateOffset ()
        {
            distanceX = (float)ShadowEffect.GetDistanceX (Element);
            distanceY = (float)ShadowEffect.GetDistanceY (Element);
        }
    }
```

`OnAttached`方法會呼叫使用 getter 來抓取附加屬性值的方法 `ShadowEffect` ，並呼叫方法來呼叫 [`TextView.SetShadowLayer`](xref:Android.Widget.TextView.SetShadowLayer*) 方法，以使用屬性值來建立陰影。 這項功能會包裝在 `try`/`catch` 區塊中，以免效果附加至的控制項沒有 `Control.Layer` 屬性。 因為沒有必要的清除，所以 `OnDetached` 方法不提供實作。

#### <a name="responding-to-property-changes"></a>回應屬性變更

如果任何 `ShadowEffect` 附加屬性值在執行階段變更，則效果需要透過顯示變更來回應。 在平台特定的效果類別中，`OnElementPropertyChanged` 方法的覆寫版本是回應可繫結屬性變更的位置，如下列程式碼範例所示：

```csharp
public class LabelShadowEffect : PlatformEffect
{
  ...
  protected override void OnElementPropertyChanged (PropertyChangedEventArgs args)
  {
    if (args.PropertyName == ShadowEffect.RadiusProperty.PropertyName) {
      UpdateRadius ();
      UpdateControl ();
    } else if (args.PropertyName == ShadowEffect.ColorProperty.PropertyName) {
      UpdateColor ();
      UpdateControl ();
    } else if (args.PropertyName == ShadowEffect.DistanceXProperty.PropertyName ||
               args.PropertyName == ShadowEffect.DistanceYProperty.PropertyName) {
      UpdateOffset ();
      UpdateControl ();
    }
  }
  ...
}
```

`OnElementPropertyChanged` 方法會更新陰影的半徑、色彩或位移，前提是已變更適當的 `ShadowEffect` 附加屬性值。 因為此覆寫會呼叫多次，所以請一律檢查變更的屬性。

### <a name="universal-windows-platform-project"></a>通用 Windows 平台專案

下列程式碼範例示範通用 Windows 平台 (UWP) 專案的 `LabelShadowEffect` 實作：

```csharp
[assembly: ResolutionGroupName ("MyCompany")]
[assembly: ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.UWP
{
    public class LabelShadowEffect : PlatformEffect
    {
        Label shadowLabel;
        bool shadowAdded = false;

        protected override void OnAttached ()
        {
            try {
                if (!shadowAdded) {
                    var textBlock = Control as Windows.UI.Xaml.Controls.TextBlock;

                    shadowLabel = new Label ();
                    shadowLabel.Text = textBlock.Text;
                    shadowLabel.FontAttributes = FontAttributes.Bold;
                    shadowLabel.HorizontalOptions = LayoutOptions.Center;
                    shadowLabel.VerticalOptions = LayoutOptions.CenterAndExpand;

                    UpdateColor ();
                    UpdateOffset ();

                    ((Grid)Element.Parent).Children.Insert (0, shadowLabel);
                    shadowAdded = true;
                }
            } catch (Exception ex) {
                Debug.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
        ...

        void UpdateColor ()
        {
            shadowLabel.TextColor = ShadowEffect.GetColor (Element);
        }

        void UpdateOffset ()
        {
            shadowLabel.TranslationX = ShadowEffect.GetDistanceX (Element);
            shadowLabel.TranslationY = ShadowEffect.GetDistanceY (Element);
        }
    }
}
```

通用 Windows 平臺不會提供陰影效果，因此 `LabelShadowEffect` 這兩個平臺上的執行會藉由在主要複本後面新增第二個位移來模擬一個 [`Label`](xref:Xamarin.Forms.Label) `Label` 。 `OnAttached` 方法會建立新的 `Label`，並在 `Label` 上設定一些配置屬性。 接著，它會使用 getter 呼叫抓取附加屬性值的方法 `ShadowEffect` ，並藉由設定 [`TextColor`](xref:Xamarin.Forms.Label.TextColor) 、 [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) 和 [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) 屬性來控制的色彩和位置，藉以建立陰影 `Label` 。 `shadowLabel` 會接著偏移插入在主要 `Label` 的後方。 這項功能會包裝在 `try`/`catch` 區塊中，以免效果附加至的控制項沒有 `Control.Layer` 屬性。 因為沒有必要的清除，所以 `OnDetached` 方法不提供實作。

#### <a name="responding-to-property-changes"></a>回應屬性變更

如果任何 `ShadowEffect` 附加屬性值在執行階段變更，則效果需要透過顯示變更來回應。 在平台特定的效果類別中，`OnElementPropertyChanged` 方法的覆寫版本是回應可繫結屬性變更的位置，如下列程式碼範例所示：

```csharp
public class LabelShadowEffect : PlatformEffect
{
  ...
  protected override void OnElementPropertyChanged (PropertyChangedEventArgs args)
  {
    if (args.PropertyName == ShadowEffect.ColorProperty.PropertyName) {
      UpdateColor ();
    } else if (args.PropertyName == ShadowEffect.DistanceXProperty.PropertyName ||
                      args.PropertyName == ShadowEffect.DistanceYProperty.PropertyName) {
      UpdateOffset ();
    }
  }
  ...
}
```

`OnElementPropertyChanged` 方法會更新陰影的色彩或位移，前提是已變更適當的 `ShadowEffect` 附加屬性值。 因為此覆寫會呼叫多次，所以請一律檢查變更的屬性。

## <a name="summary"></a>摘要

本文示範如何使用附加屬性將參數傳遞給效果，並在執行階段變更參數。 附加屬性可用來定義會回應執行階段屬性變更的效果參數。

## <a name="related-links"></a>相關連結

- [自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [作用](xref:Xamarin.Forms.Effect)
- [PlatformEffect](xref:Xamarin.Forms.PlatformEffect`2)
- [RoutingEffect](xref:Xamarin.Forms.RoutingEffect)
- [Shadow Effect (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-shadoweffectruntimechange) (陰影效果 (範例))
