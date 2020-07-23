---
title: 將效果參數當作 Common Language Runtime 屬性傳遞
description: Common Language Runtime (CLR) 屬性可用來定義不會回應執行階段屬性變更的效果參數。 本文示範如何使用 CLR 屬性將參數傳遞至效果。
ms.prod: xamarin
ms.assetid: 4B50466C-5DBD-45DD-B1E6-BE9524C92F27
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/05/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 24468e88d39283d2b8282fe0cb9b59f33fcef2f0
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86936691"
---
# <a name="passing-effect-parameters-as-common-language-runtime-properties"></a>將效果參數當作 Common Language Runtime 屬性傳遞

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-shadoweffect)

_Common Language Runtime （CLR）屬性可用來定義不會回應執行時間屬性變更的效果參數。本文示範如何使用 CLR 屬性將參數傳遞至效果。_

建立不會回應執行階段屬性變更之效果參數的程序如下：

1. 建立類別，將類別子類別化 `public` [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect) 。 `RoutingEffect` 類別代表包裝通常是平台特定之內部效果的平台獨立效果。
1. 建立呼叫基底類別建構函式的建構函式，並傳入解析群組名稱串連，以及每個平台特定效果類別所指定的唯一識別碼。
1. 針對每個要傳遞至效果的參數，將屬性新增至其類別。

然後在具現化效果時，透過指定每個屬性的值來將參數傳遞至效果。

範例應用程式會示範 `ShadowEffect` ，它會將陰影新增至控制項所顯示的文字 [`Label`](xref:Xamarin.Forms.Label) 。 下圖說明範例應用程式中每個專案的責任，以及它們之間的關聯性：

![陰影效果專案責任](clr-properties-images/shadow-effect.png)

[`Label`](xref:Xamarin.Forms.Label)上的控制項 `HomePage` 是由 `LabelShadowEffect` 每個平臺特定專案中的所自訂。 透過 `ShadowEffect` 類別中的屬性，將參數傳遞至每個 `LabelShadowEffect`。 每個 `LabelShadowEffect` 類別都衍生自每個平台的 `PlatformEffect` 類別。 這會導致將陰影新增至 `Label` 控制項所顯示的文字，如下列螢幕擷取畫面所示：

![每個平台上的陰影效果](clr-properties-images/screenshots.png)

## <a name="creating-effect-parameters"></a>建立效果參數

應建立類別的子類別， `public` [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect) 以代表效果參數，如下列程式碼範例所示：

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

`ShadowEffect` 包含四個屬性，代表要傳遞至每個平台特定 `LabelShadowEffect` 的參數。 類別建構函式會呼叫基底類別建構函式，並傳入由解析群組名稱串連組成的參數，以及每個平台特定效果類別所指定的唯一識別碼。 因此，當具現化時，的新實例 `MyCompany.LabelShadowEffect` 將會加入控制項的 [`Effects`](xref:Xamarin.Forms.Element.Effects) 集合中 `ShadowEffect` 。

## <a name="consuming-the-effect"></a>使用效果

下列 XAML 程式碼範例顯示 [`Label`](xref:Xamarin.Forms.Label) 所附加的控制項 `ShadowEffect` ：

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

label.Effects.Add (new ShadowEffect {
  Radius = 5,
  Color = color,
  DistanceX = 5,
  DistanceY = 5
});
```

在這兩個程式碼範例中， `ShadowEffect` 類別的實例會使用為每個屬性指定的值具現化，然後再加入控制項的 [`Effects`](xref:Xamarin.Forms.Element.Effects) 集合中。 請注意，`ShadowEffect.Color` 屬性使用平台特定的色彩值。 如需詳細資訊，請參閱[裝置類別](~/xamarin-forms/platform/device.md)。

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
                var effect = (ShadowEffect)Element.Effects.FirstOrDefault (e => e is ShadowEffect);
                if (effect != null) {
                    Control.Layer.ShadowRadius = effect.Radius;
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

`OnAttached` 方法會擷取 `ShadowEffect` 執行個體，並將 `Control.Layer` 屬性設定為指定的屬性值以建立陰影。 這項功能會包裝在 `try`/`catch` 區塊中，以免效果附加至的控制項沒有 `Control.Layer` 屬性。 因為沒有必要的清除，所以 `OnDetached` 方法不提供實作。

### <a name="android-project"></a>Android 專案

下列程式碼範例示範 Android 專案的 `LabelShadowEffect` 實作：

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

方法會抓取 `OnAttached` `ShadowEffect` 實例，並呼叫方法， [`TextView.SetShadowLayer`](xref:Android.Widget.TextView.SetShadowLayer*) 以使用指定的屬性值來建立陰影。 這項功能會包裝在 `try`/`catch` 區塊中，以免效果附加至的控制項沒有 `Control.Layer` 屬性。 因為沒有必要的清除，所以 `OnDetached` 方法不提供實作。

### <a name="universal-windows-platform-project"></a>通用 Windows 平台專案

下列程式碼範例示範通用 Windows 平台 (UWP) 專案的 `LabelShadowEffect` 實作：

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

通用 Windows 平臺不會提供陰影效果，因此 `LabelShadowEffect` 這兩個平臺上的執行會藉由在主要複本後面新增第二個位移來模擬一個 [`Label`](xref:Xamarin.Forms.Label) `Label` 。 `OnAttached` 方法會擷取 `ShadowEffect` 執行個體、建立新的 `Label`，並在 `Label` 上設定一些配置屬性。 然後，它會藉由設定 [`TextColor`](xref:Xamarin.Forms.Label.TextColor) 、 [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) 和 [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) 屬性來控制的色彩和位置，藉此建立陰影 `Label` 。 `shadowLabel` 會接著偏移插入在主要 `Label` 的後方。 這項功能會包裝在 `try`/`catch` 區塊中，以免效果附加至的控制項沒有 `Control.Layer` 屬性。 因為沒有必要的清除，所以 `OnDetached` 方法不提供實作。

## <a name="summary"></a>總結

本文示範了如何使用 CLR 屬性將參數傳遞至效果。 CLR 屬性可用來定義不會回應執行階段屬性變更的效果參數。

## <a name="related-links"></a>相關連結

- [自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [效果](xref:Xamarin.Forms.Effect)
- [PlatformEffect](xref:Xamarin.Forms.PlatformEffect`2)
- [RoutingEffect](xref:Xamarin.Forms.RoutingEffect)
- [Shadow Effect (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-shadoweffect) (陰影效果 (範例))
