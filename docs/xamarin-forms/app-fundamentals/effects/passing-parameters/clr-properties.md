---
title: 傳遞效果參數做為通用語言執行階段屬性
description: Common Language Runtime (CLR) 屬性可以用來定義不回應執行階段屬性變更效果參數。 本文示範如何使用 CLR 屬性參數傳遞給效果。
ms.prod: xamarin
ms.assetid: 4B50466C-5DBD-45DD-B1E6-BE9524C92F27
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/05/2016
ms.openlocfilehash: c85256803da137c850e502cfad917de703b161b5
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/07/2018
ms.locfileid: "34846470"
---
# <a name="passing-effect-parameters-as-common-language-runtime-properties"></a>傳遞效果參數做為通用語言執行階段屬性

_Common Language Runtime (CLR) 屬性可以用來定義不回應執行階段屬性變更效果參數。本文示範如何使用 CLR 屬性參數傳遞給效果。_

建立不回應執行階段屬性變更效果參數的程序如下所示：

1. 建立`public`類別的子類別[ `RoutingEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RoutingEffect/)類別。 `RoutingEffect`類別代表包裝內部效果通常平台特定的平台無關效果。
1. 建立呼叫基底類別建構函式，在解析群組名稱，與每個平台專屬效果類別指定的唯一識別碼的串連中傳遞的建構函式。
1. 將屬性加入至每個參數傳遞之效果的類別。

藉由指定每個屬性值時具現化效果然後效果傳遞參數。

範例應用程式示範`ShadowEffect`，將所顯示的文字加上陰影[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)控制項。 下圖說明範例應用程式，以及它們之間的關聯性中的每一個專案的責任：

![](clr-properties-images/shadow-effect.png "陰影效果專案責任")

A [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)上控制`HomePage`以自訂`LabelShadowEffect`每個平台專屬專案中。 參數會傳遞至每個`LabelShadowEffect`屬性中透過`ShadowEffect`類別。 每個`LabelShadowEffect`類別衍生自`PlatformEffect`每個平台的類別。 這會導致新增至所顯示的文字陰影`Label`控制，如下列螢幕擷取畫面所示：

![](clr-properties-images/screenshots.png "每個平台上的陰影效果")

## <a name="creating-effect-parameters"></a>建立參數的效果

A`public`類別的子類別[ `RoutingEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RoutingEffect/)應該代表效果參數，建立類別，如下列程式碼範例所示：

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

`ShadowEffect`包含四個屬性，代表要傳遞給每個平台專屬的參數`LabelShadowEffect`。 類別建構函式會呼叫基底類別建構函式，傳遞參數，其中 解析群組名稱，與每個平台專屬效果類別指定的唯一識別碼的串連。 因此的新執行個體的`MyCompany.LabelShadowEffect`會加入至控制項的[ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/)集合時`ShadowEffect`具現化。

## <a name="consuming-the-effect"></a>使用效果

下列 XAML 程式碼範例所示[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)控制要`ShadowEffect`連接：

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

對等項目[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)在 C# 會顯示在下列程式碼範例：

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

在這兩個程式碼範例中，執行個體`ShadowEffect`類別具現化之前要加入至控制項的每個屬性所指定的值且[ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/)集合。 請注意，`ShadowEffect.Color`屬性使用平台專屬的色彩值。 如需詳細資訊，請參閱[裝置類別](~/xamarin-forms/platform/device.md)。

## <a name="creating-the-effect-on-each-platform"></a>建立每個平台上的效果

下列章節會討論的平台特定實作`LabelShadowEffect`類別。

### <a name="ios-project"></a>iOS 的專案

下列程式碼範例示範`LabelShadowEffect`實作針對 iOS 專案：

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

`OnAttached`方法擷取`ShadowEffect`執行個體，並設定`Control.Layer`屬性，以指定的屬性值來建立陰影。 這項功能會包裝在`try` / `catch`封鎖以防效果附加至控制項沒有`Control.Layer`屬性。 沒有實作由提供`OnDetached`方法因為不會清除的必要。

### <a name="android-project"></a>Android 專案

下列程式碼範例示範`LabelShadowEffect`實作針對 Android 專案：

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

`OnAttached`方法擷取`ShadowEffect`執行個體，並呼叫[ `TextView.SetShadowLayer` ](https://developer.xamarin.com/api/member/Android.Widget.TextView.SetShadowLayer/p/System.Single/System.Single/System.Single/Android.Graphics.Color/)方法來建立使用指定的屬性值的陰影。 這項功能會包裝在`try` / `catch`封鎖以防效果附加至控制項沒有`Control.Layer`屬性。 沒有實作由提供`OnDetached`方法因為不會清除的必要。

### <a name="universal-windows-platform-project"></a>通用 Windows 平台專案

下列程式碼範例示範`LabelShadowEffect`實作通用 Windows 平台 (UWP) 專案：

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

通用 Windows 平台不提供陰影效果，因此`LabelShadowEffect`實作這兩個平台上的會模擬一個藉由新增第二個位移[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)背後主要`Label`。 `OnAttached`方法擷取`ShadowEffect`執行個體，建立新`Label`，並設定一些版面配置屬性上`Label`。 然後它會建立陰影藉由設定[ `TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.TextColor/)， [ `TranslationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/)，和[ `TranslationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/)屬性以控制的色彩和位置`Label`. `shadowLabel`則插入位移背後主要`Label`。 這項功能會包裝在`try` / `catch`封鎖以防效果附加至控制項沒有`Control.Layer`屬性。 沒有實作由提供`OnDetached`方法因為不會清除的必要。

## <a name="summary"></a>總結

本文示範使用 CLR 屬性，將參數傳遞給效果。 CLR 屬性可以用來定義不回應執行階段屬性變更效果參數。


## <a name="related-links"></a>相關連結

- [自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Effect](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/)
- [PlatformEffect](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformEffect%3CTContainer,TControl%3E/)
- [RoutingEffect](https://developer.xamarin.com/api/type/Xamarin.Forms.RoutingEffect/)
- [陰影效果 （範例）](https://developer.xamarin.com/samples/xamarin-forms/effects/shadoweffect/)
