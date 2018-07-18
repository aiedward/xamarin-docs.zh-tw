---
title: 為附加屬性參數傳遞的效果
description: 附加的屬性可用來定義可回應執行階段的屬性變更效果參數。 本文示範如何使用附加屬性，以將參數傳遞的效果，並變更在執行階段參數。
ms.prod: xamarin
ms.assetid: DFCDCB9F-17DD-4117-BD53-B4FB206BB387
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/05/2016
ms.openlocfilehash: 9483e424a74a88ce3f0eb49624bb5315551f2062
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996447"
---
# <a name="passing-effect-parameters-as-attached-properties"></a>為附加屬性參數傳遞的效果

_附加的屬性可用來定義可回應執行階段的屬性變更效果參數。本文示範如何使用附加屬性，以將參數傳遞的效果，並變更在執行階段參數。_

建立回應執行階段的屬性變更的效果參數的程序如下所示：

1. 建立`static`類別，其中包含要傳遞之效果的每個參數的附加的屬性。
1. 將用來控制的新增或移除此類別會附加至之控制項的效果的類別中加入額外的附加的屬性。 請確認此連接屬性暫存器`propertyChanged`屬性的值變更時，會執行的委派。
1. 建立`static`getter 和 setter，每個附加屬性。
1. 實作中的邏輯`propertyChanged`新增和移除效果的委派。
1. 實作內的巢狀的類別`static`類別，名為之後的效果，哪一個子類別`RoutingEffect`類別。 建構函式，呼叫基底類別建構函式，傳入的串連，解析群組名稱，以及每個平台特定效果類別指定的唯一識別碼。

參數加入適當的控制項中的附加的屬性和屬性值，然後傳遞的效果。 此外，參數可以在執行階段變更，藉由指定新的附加的屬性值。

> [!NOTE]
> 附加的屬性是屬性的特殊類型的可繫結，但連結至其他物件，而且可辨識在 XAML 中的一個類別中定義為包含類別，以及以句號分隔屬性名稱的屬性。 如需詳細資訊，請參閱 <<c0> [ 附加屬性](~/xamarin-forms/xaml/attached-properties.md)。

範例應用程式示範`ShadowEffect`，將所顯示的文字加上陰影[ `Label` ](xref:Xamarin.Forms.Label)控制項。 此外，可以在執行階段變更陰影的色彩。 下圖說明範例應用程式，以及其間的關聯性中的每個專案的責任：

![](attached-properties-images/shadow-effect.png "陰影效果專案責任")

A [ `Label` ](xref:Xamarin.Forms.Label)控制`HomePage`自訂`LabelShadowEffect`每個平台專屬專案中。 參數會傳遞至每個`LabelShadowEffect`透過; 附加屬性在`ShadowEffect`類別。 每個`LabelShadowEffect`類別衍生自`PlatformEffect`每個平台的類別。 這會導致要新增至所顯示的文字陰影`Label`控制項，如下列螢幕擷取畫面所示：

![](attached-properties-images/screenshots.png "每個平台上的陰影效果")

## <a name="creating-effect-parameters"></a>建立效果參數

A`static`應該代表效果參數，建立類別，如下列程式碼範例所示：

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

`ShadowEffect`包含五個附加的屬性，具有`static`getter 和 setter，每個附加屬性。 這些屬性的四個代表要傳遞至每個平台專屬的參數`LabelShadowEffect`。 `ShadowEffect`類別也會定義`HasShadow`附加屬性是用來控制的新增或移除的控制項要的效果，`ShadowEffect`附加至類別。 此連接屬性暫存器`OnHasShadowChanged`屬性的值變更時，會執行的方法。 此方法加入或移除的值為基礎的效果`HasShadow`附加屬性。

巢狀`LabelShadowEffect`類別的子類別[ `RoutingEffect` ](xref:Xamarin.Forms.RoutingEffect)類別，支援效果新增和移除。 `RoutingEffect`類別代表包裝內部效果通常是平台特定的平台獨立效果。 這可簡化效果移除程序，因為沒有任何編譯時間資訊的存取權類型平台專屬的效果。 `LabelShadowEffect`建構函式呼叫基底類別建構函式，傳入參數的串連，解析群組名稱，以及每個平台特定效果類別指定的唯一識別碼所組成。 這可讓效果新增和移除在`OnHasShadowChanged`方法，如下所示：

- **影響加法**– 的新執行個體`LabelShadowEffect`加入至控制項的[ `Effects` ](xref:Xamarin.Forms.Element.Effects)集合。 這會取代使用[ `Effect.Resolve` ](xref:Xamarin.Forms.Effect.Resolve(System.String))来新增效果的方法。
- **移除會影響**– 第一個執行個體`LabelShadowEffect`控制項的[ `Effects` ](xref:Xamarin.Forms.Element.Effects)擷取和移除集合。

## <a name="consuming-the-effect"></a>使用效果

每個平台專屬`LabelShadowEffect`可供新增到附加的屬性[ `Label` ](xref:Xamarin.Forms.Label)控制項，如下列 XAML 程式碼範例所示：

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

對等[ `Label` ](xref:Xamarin.Forms.Label)在 C# 會顯示在下列程式碼範例：

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

設定`ShadowEffect.HasShadow`附加屬性`true`執行`ShadowEffect.OnHasShadowChanged`方法，加入或移除`LabelShadowEffect`來[ `Label` ](xref:Xamarin.Forms.Label)控制項。 在這兩個程式碼範例中，`ShadowEffect.Color`附加的屬性提供平台特定的色彩值。 如需詳細資訊，請參閱 <<c0> [ 裝置類別](~/xamarin-forms/platform/device.md)。

颾魤 ㄛ [ `Button` ](xref:Xamarin.Forms.Button)允許在執行階段變更的陰影色彩。 當`Button`按下時，下列程式碼變更陰影色彩設定`ShadowEffect.Color`附加屬性：

```csharp
ShadowEffect.SetColor (label, Color.Teal);
```

### <a name="consuming-the-effect-with-a-style"></a>使用樣式的效果

可供新增至控制項的附加的屬性的效果也可供一種樣式。 下列 XAML 程式碼範例所示*明確*陰影效果，可以套用至樣式[ `Label` ](xref:Xamarin.Forms.Label)控制項：

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

[ `Style` ](xref:Xamarin.Forms.Style)可以套用至[ `Label` ](xref:Xamarin.Forms.Label)藉由設定其[ `Style` ](xref:Xamarin.Forms.VisualElement.Style)屬性`Style`執行個體使用`StaticResource`標記延伸，如下列程式碼範例所示：

```xaml
<Label Text="Label Shadow Effect" ... Style="{StaticResource ShadowEffectStyle}" />
```

如需有關樣式的詳細資訊，請參閱 <<c0> [ 樣式](~/xamarin-forms/user-interface/styles/index.md)。

## <a name="creating-the-effect-on-each-platform"></a>建立每個平台上的效果

下列各節將討論的平台特定實作`LabelShadowEffect`類別。

### <a name="ios-project"></a>iOS 專案

下列程式碼範例示範`LabelShadowEffect`針對 iOS 專案的實作：

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
            Control.Layer.CornerRadius = (nfloat)ShadowEffect.GetRadius (Element);
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

`OnAttached`方法呼叫的方法，以擷取附加的屬性值使用`ShadowEffect`getter，並設定`Control.Layer`建立陰影的屬性值的屬性。 這項功能會包裝在`try` / `catch`封鎖萬一效果會附加至控制項並沒有`Control.Layer`屬性。 沒有實作係由`OnDetached`方法因為不會清除的必要。

#### <a name="responding-to-property-changes"></a>回應屬性變更

如果有任何`ShadowEffect`附加屬性值變更，在執行階段，透過顯示所做的變更來回應的效果需求。 覆寫的新版`OnElementPropertyChanged`方法，在平台特定效果類別中，是可繫結的屬性變更回應的位置，如下列程式碼範例所示：

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

`OnElementPropertyChanged`方法會更新 radius、 色彩或位移陰影，前提是適當`ShadowEffect`附加的屬性值已變更。 一律應進行變更之屬性的核取，因為此覆寫可呼叫多次。

### <a name="android-project"></a>Android 專案

下列程式碼範例示範`LabelShadowEffect`針對 Android 專案的實作：

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

`OnAttached`方法呼叫的方法，以擷取附加的屬性值使用`ShadowEffect`getter，和呼叫的方法，呼叫[ `TextView.SetShadowLayer` ](https://developer.xamarin.com/api/member/Android.Widget.TextView.SetShadowLayer/p/System.Single/System.Single/System.Single/Android.Graphics.Color/)方法用來建立一個使用的屬性值的陰影。 這項功能會包裝在`try` / `catch`封鎖萬一效果會附加至控制項並沒有`Control.Layer`屬性。 沒有實作係由`OnDetached`方法因為不會清除的必要。

#### <a name="responding-to-property-changes"></a>回應屬性變更

如果有任何`ShadowEffect`附加屬性值變更，在執行階段，透過顯示所做的變更來回應的效果需求。 覆寫的新版`OnElementPropertyChanged`方法，在平台特定效果類別中，是可繫結的屬性變更回應的位置，如下列程式碼範例所示：

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

`OnElementPropertyChanged`方法會更新 radius、 色彩或位移陰影，前提是適當`ShadowEffect`附加的屬性值已變更。 一律應進行變更之屬性的核取，因為此覆寫可呼叫多次。

### <a name="universal-windows-platform-project"></a>通用 Windows 平台專案

下列程式碼範例示範`LabelShadowEffect`通用 Windows 平台 (UWP) 專案的實作：

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

通用 Windows 平台不提供陰影效果，因此`LabelShadowEffect`兩種平台的實作會模擬一個新增的第二個位移[ `Label` ](xref:Xamarin.Forms.Label)落後於主要`Label`。 `OnAttached`方法會建立新`Label`，並設定一些版面配置屬性上`Label`。 然後它會呼叫擷取使用附加的屬性值的方法`ShadowEffect`getter，並藉由設定建立陰影[ `TextColor` ](xref:Xamarin.Forms.Label.TextColor)， [ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX)，並[ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY)屬性，以控制色彩和位置`Label`。 `shadowLabel`再插入位移落後於主要`Label`。 這項功能會包裝在`try` / `catch`封鎖萬一效果會附加至控制項並沒有`Control.Layer`屬性。 沒有實作係由`OnDetached`方法因為不會清除的必要。

#### <a name="responding-to-property-changes"></a>回應屬性變更

如果有任何`ShadowEffect`附加屬性值變更，在執行階段，透過顯示所做的變更來回應的效果需求。 覆寫的新版`OnElementPropertyChanged`方法，在平台特定效果類別中，是可繫結的屬性變更回應的位置，如下列程式碼範例所示：

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

`OnElementPropertyChanged`方法會更新色彩或位移陰影，前提是適當`ShadowEffect`附加的屬性值已變更。 一律應進行變更之屬性的核取，因為此覆寫可呼叫多次。

## <a name="summary"></a>總結

這篇文章已示範使用連接屬性，以將參數傳遞的效果，並變更在執行階段參數。 附加的屬性可用來定義可回應執行階段的屬性變更效果參數。


## <a name="related-links"></a>相關連結

- [自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Effect](xref:Xamarin.Forms.Effect)
- [PlatformEffect](xref:Xamarin.Forms.PlatformEffect`2)
- [RoutingEffect](xref:Xamarin.Forms.RoutingEffect)
- [延伸陰影效果 （範例）](https://developer.xamarin.com/samples/xamarin-forms/effects/shadoweffectruntimechange/)
