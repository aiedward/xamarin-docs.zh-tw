---
title: 通用語言執行階段屬性為參數傳遞的效果
description: Common Language Runtime (CLR) 屬性可用來定義不回應執行階段的屬性變更的效果參數。 這篇文章示範如何使用 CLR 屬性將參數傳遞的效果。
ms.prod: xamarin
ms.assetid: 4B50466C-5DBD-45DD-B1E6-BE9524C92F27
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/05/2016
ms.openlocfilehash: 1bb357b256a7cc6d52d1d92613f38cbf48400c4c
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995764"
---
# <a name="passing-effect-parameters-as-common-language-runtime-properties"></a>通用語言執行階段屬性為參數傳遞的效果

_Common Language Runtime (CLR) 屬性可用來定義不回應執行階段的屬性變更的效果參數。這篇文章示範如何使用 CLR 屬性將參數傳遞的效果。_

建立不回應執行階段的屬性變更的效果參數的程序如下所示：

1. 建立`public`類別子類別化[ `RoutingEffect` ](xref:Xamarin.Forms.RoutingEffect)類別。 `RoutingEffect`類別代表包裝內部效果通常是平台特定的平台獨立效果。
1. 建立建構函式呼叫基底類別建構函式，傳入的串連，解析群組名稱，以及每個平台特定效果類別指定的唯一識別碼。
1. 將屬性加入每個參數來傳遞之效果的類別。

藉由指定每個屬性值時具現化效果接著效果傳遞參數。

範例應用程式示範`ShadowEffect`，將所顯示的文字加上陰影[ `Label` ](xref:Xamarin.Forms.Label)控制項。 下圖說明範例應用程式，以及其間的關聯性中的每個專案的責任：

![](clr-properties-images/shadow-effect.png "陰影效果專案責任")

A [ `Label` ](xref:Xamarin.Forms.Label)控制`HomePage`自訂`LabelShadowEffect`每個平台專屬專案中。 參數會傳遞至每個`LabelShadowEffect`屬性中透過`ShadowEffect`類別。 每個`LabelShadowEffect`類別衍生自`PlatformEffect`每個平台的類別。 這會導致要新增至所顯示的文字陰影`Label`控制項，如下列螢幕擷取畫面所示：

![](clr-properties-images/screenshots.png "每個平台上的陰影效果")

## <a name="creating-effect-parameters"></a>建立效果參數

A`public`類別子類別化[ `RoutingEffect` ](xref:Xamarin.Forms.RoutingEffect)應該代表效果參數，建立類別，如下列程式碼範例所示：

```csharp
public class ShadowEffect : RoutingEffect
{
  public float Radius { get; set; }

  public Color Color { get; set; }

  public float DistanceX { get; set; }

  public float DistanceY { get; set; }

  public ShadowEffect () : base ("MyCompany.LabelShadowEffect")
  {            
  }
}
```

`ShadowEffect`包含四個屬性，代表要傳遞至每個平台專屬的參數`LabelShadowEffect`。 類別建構函式會呼叫基底類別建構函式，傳入參數的串連，解析群組名稱，以及每個平台特定效果類別指定的唯一識別碼所組成。 因此的新執行個體`MyCompany.LabelShadowEffect`會加入至控制項的[ `Effects` ](xref:Xamarin.Forms.Element.Effects)集合時`ShadowEffect`具現化。

## <a name="consuming-the-effect"></a>使用效果

下列 XAML 程式碼範例所示[ `Label` ](xref:Xamarin.Forms.Label)控制項`ShadowEffect`連接：

```xaml
<Label Text="Label Shadow Effect" ...>
  <Label.Effects>
    <local:ShadowEffect Radius="5" DistanceX="5" DistanceY="5">
      <local:ShadowEffect.Color>
        <OnPlatform x:TypeArguments="Color">
            <On Platform="iOS" Value="Black" />
            <On Platform="Android" Value="White" />
            <On Platform="UWP" Value="Red" />
        </OnPlatform>
      </local:ShadowEffect.Color>
    </local:ShadowEffect>
  </Label.Effects>
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

label.Effects.Add (new ShadowEffect {
  Radius = 5,
  Color = color,
  DistanceX = 5,
  DistanceY = 5
});
```

在這兩個程式碼範例中，執行個體`ShadowEffect`才能新增到控制項的每個屬性，其指定的值具現化類別[ `Effects` ](xref:Xamarin.Forms.Element.Effects)集合。 請注意，`ShadowEffect.Color`屬性會使用平台特定的色彩值。 如需詳細資訊，請參閱 <<c0> [ 裝置類別](~/xamarin-forms/platform/device.md)。

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
                var effect = (ShadowEffect)Element.Effects.FirstOrDefault (e => e is ShadowEffect);
                if (effect != null) {
                    Control.Layer.CornerRadius = effect.Radius;
                    Control.Layer.ShadowColor = effect.Color.ToCGColor ();
                    Control.Layer.ShadowOffset = new CGSize (effect.DistanceX, effect.DistanceY);
                    Control.Layer.ShadowOpacity = 1.0f;
                }
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
    }
}
```

`OnAttached`方法擷取`ShadowEffect`執行個體，以及設定`Control.Layer`屬性，以指定的屬性值，以建立陰影。 這項功能會包裝在`try` / `catch`封鎖萬一效果會附加至控制項並沒有`Control.Layer`屬性。 沒有實作係由`OnDetached`方法因為不會清除的必要。

### <a name="android-project"></a>Android 專案

下列程式碼範例示範`LabelShadowEffect`針對 Android 專案的實作：

```csharp
[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.Droid
{
    public class LabelShadowEffect : PlatformEffect
    {
        protected override void OnAttached ()
        {
            try {
                var control = Control as Android.Widget.TextView;
                var effect = (ShadowEffect)Element.Effects.FirstOrDefault (e => e is ShadowEffect);
                if (effect != null) {
                    float radius = effect.Radius;
                    float distanceX = effect.DistanceX;
                    float distanceY = effect.DistanceY;
                    Android.Graphics.Color color = effect.Color.ToAndroid ();
                    control.SetShadowLayer (radius, distanceX, distanceY, color);
                }
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
    }
}
```

`OnAttached`方法擷取`ShadowEffect`執行個體，並呼叫[ `TextView.SetShadowLayer` ](https://developer.xamarin.com/api/member/Android.Widget.TextView.SetShadowLayer/p/System.Single/System.Single/System.Single/Android.Graphics.Color/)方法用來建立一個使用指定的屬性值的陰影。 這項功能會包裝在`try` / `catch`封鎖萬一效果會附加至控制項並沒有`Control.Layer`屬性。 沒有實作係由`OnDetached`方法因為不會清除的必要。

### <a name="universal-windows-platform-project"></a>通用 Windows 平台專案

下列程式碼範例示範`LabelShadowEffect`通用 Windows 平台 (UWP) 專案的實作：

```csharp
[assembly: ResolutionGroupName ("Xamarin")]
[assembly: ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.UWP
{
    public class LabelShadowEffect : PlatformEffect
    {
        bool shadowAdded = false;

        protected override void OnAttached ()
        {
            try {
                if (!shadowAdded) {
                    var effect = (ShadowEffect)Element.Effects.FirstOrDefault (e => e is ShadowEffect);
                    if (effect != null) {
                        var textBlock = Control as Windows.UI.Xaml.Controls.TextBlock;
                        var shadowLabel = new Label ();
                        shadowLabel.Text = textBlock.Text;
                        shadowLabel.FontAttributes = FontAttributes.Bold;
                        shadowLabel.HorizontalOptions = LayoutOptions.Center;
                        shadowLabel.VerticalOptions = LayoutOptions.CenterAndExpand;
                        shadowLabel.TextColor = effect.Color;
                        shadowLabel.TranslationX = effect.DistanceX;
                        shadowLabel.TranslationY = effect.DistanceY;

                        ((Grid)Element.Parent).Children.Insert (0, shadowLabel);
                        shadowAdded = true;
                    }
                }
            } catch (Exception ex) {
                Debug.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
    }
}
```

通用 Windows 平台不提供陰影效果，因此`LabelShadowEffect`兩種平台的實作會模擬一個新增的第二個位移[ `Label` ](xref:Xamarin.Forms.Label)落後於主要`Label`。 `OnAttached`方法擷取`ShadowEffect`執行個體，請建立新`Label`，並設定一些版面配置屬性上`Label`。 然後藉由設定建立陰影[ `TextColor` ](xref:Xamarin.Forms.Label.TextColor)， [ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX)，以及[ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY)屬性來控制的色彩和位置`Label`. `shadowLabel`再插入位移落後於主要`Label`。 這項功能會包裝在`try` / `catch`封鎖萬一效果會附加至控制項並沒有`Control.Layer`屬性。 沒有實作係由`OnDetached`方法因為不會清除的必要。

## <a name="summary"></a>總結

這篇文章已示範使用 CLR 屬性，將參數傳遞的效果。 CLR 屬性可用來定義不回應執行階段的屬性變更的效果參數。


## <a name="related-links"></a>相關連結

- [自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Effect](xref:Xamarin.Forms.Effect)
- [PlatformEffect](xref:Xamarin.Forms.PlatformEffect`2)
- [RoutingEffect](xref:Xamarin.Forms.RoutingEffect)
- [延伸陰影效果 （範例）](https://developer.xamarin.com/samples/xamarin-forms/effects/shadoweffect/)
