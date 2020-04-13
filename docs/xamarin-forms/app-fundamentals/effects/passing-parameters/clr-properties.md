---
title: 將效果參數當作 Common Language Runtime 屬性傳遞
description: Common Language Runtime (CLR) 屬性可用來定義不會回應執行階段屬性變更的效果參數。 本文示範如何使用 CLR 屬性將參數傳遞至效果。
ms.prod: xamarin
ms.assetid: 4B50466C-5DBD-45DD-B1E6-BE9524C92F27
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/05/2016
ms.openlocfilehash: 04d96dad455cbcf8360f12ee97a0540e7e746d62
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771466"
---
# <a name="passing-effect-parameters-as-common-language-runtime-properties"></a>將效果參數當作 Common Language Runtime 屬性傳遞

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-shadoweffect)

_通用語言執行時 (CLR) 屬性可用於定義不回應執行時屬性更改的效果參數。本文演示使用 CLR 屬性傳遞參數以產生效果。_

建立不會回應執行階段屬性變更之效果參數的程序如下：

1. 創建對`public`類進行子類[`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect)的類。 `RoutingEffect` 類別代表包裝通常是平台特定之內部效果的平台獨立效果。
1. 建立呼叫基底類別建構函式的建構函式，並傳入解析群組名稱串連，以及每個平台特定效果類別所指定的唯一識別碼。
1. 針對每個要傳遞至效果的參數，將屬性新增至其類別。

然後在具現化效果時，透過指定每個屬性的值來將參數傳遞至效果。

範例應用程式展示到控制器`ShadowEffect`的文字加入陰影的[`Label`](xref:Xamarin.Forms.Label)。 下圖說明範例應用程式中每個專案的責任，以及它們之間的關聯性：

![](clr-properties-images/shadow-effect.png "Shadow Effect Project Responsibilities")

上的[`Label`](xref:Xamarin.Forms.Label)`HomePage`控制件`LabelShadowEffect`由每個特定於平臺的專案自定義。 透過 `ShadowEffect` 類別中的屬性，將參數傳遞至每個 `LabelShadowEffect`。 每個 `LabelShadowEffect` 類別都衍生自每個平台的 `PlatformEffect` 類別。 這會導致將陰影新增至 `Label` 控制項所顯示的文字，如下列螢幕擷取畫面所示：

![](clr-properties-images/screenshots.png "Shadow Effect on each Platform")

## <a name="creating-effect-parameters"></a>建立效果參數

應`public`建立類子類[`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect)以 表示效果參數的類,如以下代碼範例所示:

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

`ShadowEffect` 包含四個屬性，代表要傳遞至每個平台特定 `LabelShadowEffect` 的參數。 類別建構函式會呼叫基底類別建構函式，並傳入由解析群組名稱串連組成的參數，以及每個平台特定效果類別所指定的唯一識別碼。 因此,當實例化`MyCompany.LabelShadowEffect`[`Effects`](xref:Xamarin.Forms.Element.Effects)`ShadowEffect`時 ,將 處到控件集合的新實例。

## <a name="consuming-the-effect"></a>使用效果

下面的 XAML 代碼範例[`Label`](xref:Xamarin.Forms.Label)顯示了`ShadowEffect`附加到 的 控制項:

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

C#[`Label`](xref:Xamarin.Forms.Label)中的等效項目顯示在以下代碼範例中:

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

在這兩個程式碼範例中`ShadowEffect`, 類別的實體在添加到控制[`Effects`](xref:Xamarin.Forms.Element.Effects)項的集合之前,會實例化為每個屬性指定值。 請注意，`ShadowEffect.Color` 屬性使用平台特定的色彩值。 如需詳細資訊，請參閱[裝置類別](~/xamarin-forms/platform/device.md)。

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

該方法`OnAttached`檢`ShadowEffect`索 實例,並[`TextView.SetShadowLayer`](xref:Android.Widget.TextView.SetShadowLayer*)調用 方法使用指定的屬性值創建陰影。 這項功能會包裝在 `try`/`catch` 區塊中，以免效果附加至的控制項沒有 `Control.Layer` 屬性。 因為沒有必要的清除，所以 `OnDetached` 方法不提供實作。

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

通用 Windows 平臺不提供陰影效果,因此,兩個`LabelShadowEffect`平臺上 的實現[`Label`](xref:Xamarin.Forms.Label)`Label`通過在主 平台後面添加第二個偏移量來類比一個。 `OnAttached` 方法會擷取 `ShadowEffect` 執行個體、建立新的 `Label`，並在 `Label` 上設定一些配置屬性。 然後,它通過設置[`TextColor`](xref:Xamarin.Forms.Label.TextColor)和[`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)[`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)屬性來控制`Label`的顏色和位置來創建陰影。 `shadowLabel` 會接著偏移插入在主要 `Label` 的後方。 這項功能會包裝在 `try`/`catch` 區塊中，以免效果附加至的控制項沒有 `Control.Layer` 屬性。 因為沒有必要的清除，所以 `OnDetached` 方法不提供實作。

## <a name="summary"></a>總結

本文示範了如何使用 CLR 屬性將參數傳遞至效果。 CLR 屬性可用來定義不會回應執行階段屬性變更的效果參數。

## <a name="related-links"></a>相關連結

- [自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [效果](xref:Xamarin.Forms.Effect)
- [PlatformEffect](xref:Xamarin.Forms.PlatformEffect`2)
- [RoutingEffect](xref:Xamarin.Forms.RoutingEffect)
- [Shadow Effect (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-shadoweffect) (陰影效果 (範例))
