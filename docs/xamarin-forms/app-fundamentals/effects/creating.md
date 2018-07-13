---
title: 建立的效果
description: 效果簡化自訂控制項。 這篇文章會示範如何建立變更的項目控制項的背景色彩，當控制項取得焦點時的效果。
ms.prod: xamarin
ms.assetid: 9E2C8DB0-36A2-4F13-8E3C-A66D7021DB13
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2016
ms.openlocfilehash: b29d83999724a35293882f7b9efc0158171c4fd2
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998156"
---
# <a name="creating-an-effect"></a>建立的效果

_效果簡化自訂控制項。這篇文章會示範如何建立變更的項目控制項的背景色彩，當控制項取得焦點時的效果。_

建立作用中的每個平台特定專案的程序如下所示：

1. 建立的子類別`PlatformEffect`類別。
1. 覆寫`OnAttached`方法，並寫入邏輯，以自訂控制項。
1. 覆寫`OnDetached`方法，並寫入邏輯，以清除控制項自訂項目，如有必要。
1. 新增[ `ResolutionGroupName` ](xref:Xamarin.Forms.ResolutionGroupNameAttribute)效果類別屬性。 這個屬性設定的效果，導致無法與其他具有相同名稱的效果衝突公司寬命名空間。 請注意，這個屬性只能套用一次每個專案。
1. 新增[ `ExportEffect` ](xref:Xamarin.Forms.ExportEffectAttribute)效果類別屬性。 這個屬性會影響向 Xamarin.Forms，由群組名稱，以及用來找出的效果，再將它套用至控制項的唯一識別碼。 屬性會採用兩個參數 – 效果，並將用來找出的效果，再將它套用至控制項的唯一字串的型別名稱。

效果然後可供將它附加到適當的控制項。

> [!NOTE]
> 它是選擇性的以提供每個平台專案中的效果。 嘗試使用的效果，其中未登錄時，會傳回非 null 值，不執行任何動作。

範例應用程式示範`FocusEffect`變更控制項的背景色彩獲得焦點時。 下圖說明範例應用程式，以及其間的關聯性中的每個專案的責任：

![](creating-images/focus-effect.png "焦點效果專案責任")

[ `Entry` ](xref:Xamarin.Forms.Entry)控制`HomePage`自訂`FocusEffect`每個平台專屬專案中的類別。 每個`FocusEffect`類別衍生自`PlatformEffect`每個平台的類別。 這會導致`Entry`控制呈現平台專屬的背景色彩，以變更當控制項取得焦點，如下列螢幕擷取畫面所示：

![](creating-images/screenshots-1.png "焦點放在每個平台上的效果")
![](creating-images/screenshots-2.png "焦點放在每個平台上的效果")

## <a name="creating-the-effect-on-each-platform"></a>建立每個平台上的效果

下列各節將討論的平台特定實作`FocusEffect`類別。

## <a name="ios-project"></a>iOS 專案

下列程式碼範例示範`FocusEffect`針對 iOS 專案的實作：

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

`OnAttached`方法會設定`BackgroundColor`淺紫與控制項屬性`UIColor.FromRGB`方法，並也會儲存在欄位中的這個色彩。 這項功能會包裝在`try` / `catch`封鎖萬一效果會附加至控制項並沒有`BackgroundColor`屬性。 沒有實作係由`OnDetached`方法因為不會清除的必要。

`OnElementPropertyChanged`覆寫回應 Xamarin.Forms 控制項上的可繫結的屬性變更。 當[ `IsFocused` ](xref:Xamarin.Forms.VisualElement.IsFocused)屬性變更`BackgroundColor`如果控制項具有焦點的控制項屬性變更為白色，否則它會變更為淺紫色。 這項功能會包裝在`try` / `catch`封鎖萬一效果會附加至控制項並沒有`BackgroundColor`屬性。

## <a name="android-project"></a>Android 專案

下列程式碼範例示範`FocusEffect`針對 Android 專案的實作：

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

`OnAttached`方法呼叫`SetBackgroundColor`方法來設定來啟用控制項的背景色彩綠色，並也會儲存在欄位中的這個色彩。 這項功能會包裝在`try` / `catch`封鎖萬一效果會附加至控制項並沒有`SetBackgroundColor`屬性。 沒有實作係由`OnDetached`方法因為不會清除的必要。

`OnElementPropertyChanged`覆寫回應 Xamarin.Forms 控制項上的可繫結的屬性變更。 當[ `IsFocused` ](xref:Xamarin.Forms.VisualElement.IsFocused)屬性變更，如果控制項具有焦點之控制項的背景色彩變更為白色，否則它會變更為淺綠色。 這項功能會包裝在`try` / `catch`封鎖萬一效果會附加至控制項並沒有`BackgroundColor`屬性。

## <a name="universal-windows-platform-projects"></a>通用 Windows 平台專案

下列程式碼範例示範`FocusEffect`通用 Windows 平台 (UWP) 專案的實作：

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

`OnAttached`方法會設定`Background`青色，並設定控制項屬性`BackgroundFocusBrush`屬性為白色。 這項功能會包裝在`try` / `catch`封鎖萬一效果會附加至控制項缺少這些屬性。 沒有實作係由`OnDetached`方法因為不會清除的必要。

## <a name="consuming-the-effect"></a>使用效果

使用 Xamarin.Forms.NET Standard 程式庫或共用程式庫專案影響的程序如下所示：

1. 宣告的控制項，將自訂效果。
1. 附加至控制項的效果，方法是加入控制項的[ `Effects` ](xref:Xamarin.Forms.Element.Effects)集合。

> [!NOTE]
> 影響執行個體只能附加至單一控制項。 因此，效果，必須先解決兩次，並在兩個控制項使用。

## <a name="consuming-the-effect-in-xaml"></a>使用 XAML 中的效果

下列 XAML 程式碼範例所示[ `Entry` ](xref:Xamarin.Forms.Entry)控制項`FocusEffect`連接：

```xaml
<Entry Text="Effect attached to an Entry" ...>
    <Entry.Effects>
        <local:FocusEffect />
    </Entry.Effects>
    ...
</Entry>
```

`FocusEffect` .NET Standard 程式庫中的類別支援 XAML，效果耗用量和下列程式碼範例所示：

```csharp
public class FocusEffect : RoutingEffect
{
    public FocusEffect () : base ("MyCompany.FocusEffect")
    {
    }
}
```

`FocusEffect`類別子類別[ `RoutingEffect` ](xref:Xamarin.Forms.RoutingEffect)類別，其代表包裝內部效果通常是平台特定的平台獨立效果。 `FocusEffect`類別會呼叫基底類別建構函式，傳遞參數，其中解析群組名稱的串連 (使用指定[ `ResolutionGroupName` ](xref:Xamarin.Forms.ResolutionGroupNameAttribute)效果類別上)，和的唯一 ID使用指定[ `ExportEffect` ](xref:Xamarin.Forms.ExportEffectAttribute)效果類別上的屬性。 因此，當[ `Entry` ](xref:Xamarin.Forms.Entry)初始化在執行階段的新執行個體`MyCompany.FocusEffect`新增至控制項[ `Effects` ](xref:Xamarin.Forms.Element.Effects)集合。

效果也可以附加至控制項所使用的行為，或使用附加屬性。 如需有關附加至控制項的影響，藉由使用行為的詳細資訊，請參閱[可重複使用 EffectBehavior](~/xamarin-forms/app-fundamentals/behaviors/reusable/effect-behavior.md)。 如需有關附加至控制項的影響，使用附加的屬性的詳細資訊，請參閱[將參數傳遞至效果](~/xamarin-forms/app-fundamentals/effects/passing-parameters/index.md)。

## <a name="consuming-the-effect-in-cnum"></a>使用 C 中的效果&num;

對等[ `Entry` ](xref:Xamarin.Forms.Entry)在 C# 會顯示在下列程式碼範例：

```csharp
var entry = new Entry {
  Text = "Effect attached to an Entry",
  ...
};
```

`FocusEffect`附加至`Entry`效果加入至控制項的執行個體[ `Effects` ](xref:Xamarin.Forms.Element.Effects)集合，如下列程式碼範例所示：

```csharp
public HomePageCS ()
{
  ...
  entry.Effects.Add (Effect.Resolve ("MyCompany.FocusEffect"));
  ...
}
```

[ `Effect.Resolve` ](xref:Xamarin.Forms.Effect.Resolve(System.String))會傳回[ `Effect` ](xref:Xamarin.Forms.Effect)做為指定的名稱，這是解析群組名稱的串連 (使用指定[ `ResolutionGroupName` ](xref:Xamarin.Forms.ResolutionGroupNameAttribute)效果類別上)，並使用指定的唯一識別碼[ `ExportEffect` ](xref:Xamarin.Forms.ExportEffectAttribute)效果類別上的屬性。 如果一個平台不提供效果`Effect.Resolve`方法會傳回非`null`值。

## <a name="summary"></a>總結

本文示範如何建立變更的背景色彩的效果[ `Entry` ](xref:Xamarin.Forms.Entry)控制當控制項取得焦點。


## <a name="related-links"></a>相關連結

- [自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Effect](xref:Xamarin.Forms.Effect)
- [PlatformEffect](xref:Xamarin.Forms.PlatformEffect`2)
- [背景色彩效果 （範例）](https://developer.xamarin.com/samples/xamarin-forms/effects/backgroundcoloreffect/)
- [焦點效果 （範例）](https://developer.xamarin.com/samples/xamarin-forms/effects/focuseffect/)
