---
title: 建立效果
description: 效果簡化自訂控制項。 本文示範如何建立控制項取得焦點時，變更項目控制項的背景色彩的效果。
ms.prod: xamarin
ms.assetid: 9E2C8DB0-36A2-4F13-8E3C-A66D7021DB13
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2016
ms.openlocfilehash: 12b8906dd4562e58dede181e773e4046b8434214
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2018
---
# <a name="creating-an-effect"></a>建立效果

_效果簡化自訂控制項。本文示範如何建立控制項取得焦點時，變更項目控制項的背景色彩的效果。_

建立影響每個平台專屬專案中的程序如下所示：

1. 建立的子類別`PlatformEffect`類別。
1. 覆寫`OnAttached`方法，並寫入自訂控制項的邏輯。
1. 覆寫`OnDetached`方法，並寫入視需要清除控制項的自訂邏輯。
1. 新增[ `ResolutionGroupName` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResolutionGroupNameAttribute/)效果類別屬性。 這個屬性會設定公司寬的命名空間效果，防止與其他效果具有相同名稱發生衝突。 請注意，這個屬性只能套用一次每個專案。
1. 新增[ `ExportEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ExportEffectAttribute/)效果類別屬性。 這個屬性使用 Xamarin.Forms，由群組名稱，以及用來尋找套用至控制項之前生效的唯一識別碼，註冊效果。 屬性會採用兩個參數 – 效果，並將用來找出的效果之前套用至控制項的唯一字串的型別名稱。

效果然後可供將它連接至適當的控制項。

> [!NOTE]
> 它是選擇性的以提供每個平台專案中的效果。 嘗試使用的效果，其中一個未登錄時，會傳回非 null 值，不做任何動作。

範例應用程式示範`FocusEffect`獲得焦點時變更控制項的背景色彩。 下圖說明範例應用程式，以及它們之間的關聯性中的每一個專案的責任：

![](creating-images/focus-effect.png "焦點效果專案責任")

[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)上控制`HomePage`以自訂`FocusEffect`每個平台專屬專案中的類別。 每個`FocusEffect`類別衍生自`PlatformEffect`每個平台的類別。 這會導致`Entry`控制所呈現平台專屬的背景色彩變更時控制項取得焦點，如下列螢幕擷取畫面所示：

![](creating-images/screenshots-1.png "焦點放在每個平台上的效果")
![](creating-images/screenshots-2.png "焦點放在每個平台上的效果")

## <a name="creating-the-effect-on-each-platform"></a>建立每個平台上的效果

下列章節會討論的平台特定實作`FocusEffect`類別。

## <a name="ios-project"></a>iOS 的專案

下列程式碼範例示範`FocusEffect`實作針對 iOS 專案：

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Platform.iOS;

[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(FocusEffect), "FocusEffect")]
namespace EffectsDemo.iOS
{
    public class FocusEffect : PlatformEffect
    {
        UIColor backgroundColor;

        protected override void OnAttached ()
        {
            try {
                Control.BackgroundColor = backgroundColor = UIColor.FromRGB (204, 153, 255);
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }

        protected override void OnElementPropertyChanged (PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged (args);

            try {
                if (args.PropertyName == "IsFocused") {
                    if (Control.BackgroundColor == backgroundColor) {
                        Control.BackgroundColor = UIColor.White;
                    } else {
                        Control.BackgroundColor = backgroundColor;
                    }
                }
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }
    }
}
```

`OnAttached`方法會設定`BackgroundColor`淡紫色與到控制項的屬性`UIColor.FromRGB`方法，和此色彩也會儲存在欄位中。 這項功能會包裝在`try` / `catch`封鎖以防效果附加至控制項沒有`BackgroundColor`屬性。 沒有實作由提供`OnDetached`方法因為不會清除的必要。

`OnElementPropertyChanged`覆寫回應 Xamarin.Forms 控制項上的可繫結的屬性變更。 當[ `IsFocused` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsFocused/)屬性變更`BackgroundColor`如果焦點在控制項的控制項屬性變更為白色，否則它會變更為淡紫色。 這項功能會包裝在`try` / `catch`封鎖以防效果附加至控制項沒有`BackgroundColor`屬性。

## <a name="android-project"></a>Android 專案

下列程式碼範例示範`FocusEffect`實作針對 Android 專案：

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Platform.Android;

[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(FocusEffect), "FocusEffect")]
namespace EffectsDemo.Droid
{
    public class FocusEffect : PlatformEffect
    {
        Android.Graphics.Color backgroundColor;

        protected override void OnAttached ()
        {
            try {
                backgroundColor = Android.Graphics.Color.LightGreen;
                Control.SetBackgroundColor (backgroundColor);

            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }

        protected override void OnElementPropertyChanged (System.ComponentModel.PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged (args);
            try {
                if (args.PropertyName == "IsFocused") {
                    if (((Android.Graphics.Drawables.ColorDrawable)Control.Background).Color == backgroundColor) {
                        Control.SetBackgroundColor (Android.Graphics.Color.Black);
                    } else {
                        Control.SetBackgroundColor (backgroundColor);
                    }
                }
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }
    }
}
```

`OnAttached`方法呼叫`SetBackgroundColor`綠色，和也會在欄位中儲存此色彩的方法設定背景色彩的亮度到控制項。 這項功能會包裝在`try` / `catch`封鎖以防效果附加至控制項沒有`SetBackgroundColor`屬性。 沒有實作由提供`OnDetached`方法因為不會清除的必要。

`OnElementPropertyChanged`覆寫回應 Xamarin.Forms 控制項上的可繫結的屬性變更。 當[ `IsFocused` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsFocused/)屬性變更，如果焦點在控制項，變更控制項的背景色彩為白色，否則它會變更為淺綠色。 這項功能會包裝在`try` / `catch`封鎖以防效果附加至控制項沒有`BackgroundColor`屬性。

## <a name="universal-windows-platform-projects"></a>通用 Windows 平台專案

下列程式碼範例示範`FocusEffect`實作的通用 Windows 平台 (UWP) 專案：

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Platform.UWP;

[assembly: ResolutionGroupName("MyCompany")]
[assembly: ExportEffect(typeof(FocusEffect), "FocusEffect")]
namespace EffectsDemo.UWP
{
    public class FocusEffect : PlatformEffect
    {
        protected override void OnAttached()
        {
            try
            {
                (Control as Windows.UI.Xaml.Controls.Control).Background = new SolidColorBrush(Colors.Cyan);
                (Control as FormsTextBox).BackgroundFocusBrush = new SolidColorBrush(Colors.White);
            }
            catch (Exception ex)
            {
                Debug.WriteLine("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached()
        {
        }
    }
}
```

`OnAttached`方法會設定`Background`青色、 和設定控制項屬性`BackgroundFocusBrush`屬性設為白色。 這項功能會包裝在`try` / `catch`封鎖以防效果附加至控制項缺少這些屬性。 沒有實作由提供`OnDetached`方法因為不會清除的必要。

## <a name="consuming-the-effect"></a>使用效果

使用 Xamarin.Forms.NET 標準程式庫或共用的程式庫專案有效的程序如下所示：

1. 宣告會影響所自訂的控制項。
1. 附加至控制項的影響，將它加入至控制項的[ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/)集合。

> [!NOTE]
> 影響執行個體只能附加至單一的控制項。 因此，效果，必須先解決兩次，在兩個控制項上使用它。

## <a name="consuming-the-effect-in-xaml"></a>使用 XAML 中的效果

下列 XAML 程式碼範例所示[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)控制要`FocusEffect`連接：

```xaml
<Entry Text="Effect attached to an Entry" ...>
    <Entry.Effects>
        <local:FocusEffect />
    </Entry.Effects>
    ...
</Entry>
```

`FocusEffect` .NET 標準文件庫中的類別支援效果耗用量在 XAML 中，與下列程式碼範例所示：

```csharp
public class FocusEffect : RoutingEffect
{
    public FocusEffect () : base ("MyCompany.FocusEffect")
    {
    }
}
```

`FocusEffect`類別子類別[ `RoutingEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RoutingEffect/)類別，表示包裝內部效果通常平台特定的平台獨立結果。 `FocusEffect`類別會呼叫基底類別建構函式，傳遞參數，其中解析群組名稱的串連 (使用指定[ `ResolutionGroupName` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResolutionGroupNameAttribute/)效果類別上)，和的唯一 ID使用指定[ `ExportEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ExportEffectAttribute/)效果類別上的屬性。 因此，當[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)初始化在執行階段的新執行個體`MyCompany.FocusEffect`加入至控制項的[ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/)集合。

效果也可附加至控制項所使用的行為，或使用附加屬性。 如需使用行為來附加至控制項的效果的詳細資訊，請參閱[可重複使用 EffectBehavior](~/xamarin-forms/app-fundamentals/behaviors/reusable/effect-behavior.md)。 如需附加至控制項的效果，藉由使用附加的屬性的詳細資訊，請參閱[將參數傳遞至效果](~/xamarin-forms/app-fundamentals/effects/passing-parameters/index.md)。

## <a name="consuming-the-effect-in-cnum"></a>使用 C 中的效果&num;

對等項目[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)在 C# 會顯示在下列程式碼範例：

```csharp
var entry = new Entry {
  Text = "Effect attached to an Entry",
  ...
};
```

`FocusEffect`附加至`Entry`效果加入至控制項的執行個體[ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/)集合，如下列程式碼範例所示：

```csharp
public HomePageCS ()
{
  ...
  entry.Effects.Add (Effect.Resolve ("MyCompany.FocusEffect"));
  ...
}
```

[ `Effect.Resolve` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Effect.Resolve/p/System.String/)傳回[ `Effect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/)對於指定的名稱，這是解析群組名稱的串連 (使用指定[ `ResolutionGroupName` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResolutionGroupNameAttribute/)效果類別上)，並使用指定的唯一識別碼[ `ExportEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ExportEffectAttribute/)效果類別上的屬性。 如果平台不提供效果`Effect.Resolve`方法會傳回非`null`值。

## <a name="summary"></a>總結

本文示範如何建立變更的背景色彩效果[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)控制當控制項取得焦點。


## <a name="related-links"></a>相關連結

- [自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Effect](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/)
- [PlatformEffect](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformEffect%3CTContainer,TControl%3E/)
- [背景色彩效果 （範例）](https://developer.xamarin.com/samples/xamarin-forms/effects/backgroundcoloreffect/)
- [焦點效果 （範例）](https://developer.xamarin.com/samples/xamarin-forms/effects/focuseffect/)
