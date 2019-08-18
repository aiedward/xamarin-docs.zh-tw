---
title: 建立效果
description: 效果會簡化控制項的自訂。 本文示範如何在控制項取得焦點時，建立變更 Entry 控制項背景色彩的效果。
ms.prod: xamarin
ms.assetid: 9E2C8DB0-36A2-4F13-8E3C-A66D7021DB13
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2016
ms.openlocfilehash: f1c18c30a2da019fb9d1c09fac17c9f095dafedc
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739351"
---
# <a name="creating-an-effect"></a>建立效果

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-focuseffect)

_效果會簡化控制項的自訂。本文示範如何在控制項取得焦點時，建立變更 Entry 控制項背景色彩的效果。_

在每個平台特定專案中建立效果的程序如下：

1. 建立 `PlatformEffect` 類別的子類別。
1. 覆寫 `OnAttached` 方法，並撰寫自訂控制項的邏輯。
1. 覆寫 `OnDetached` 方法，並撰寫清除控制項自訂的邏輯 (如有必要)。
1. 將 [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) 屬性新增至效果類別。 這個屬性會設定效果的全公司命名空間，防止與其他同名效果發生衝突。 請注意，每個專案只能套用一次這個屬性。
1. 將 [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) 屬性新增至效果類別。 這個屬性會註冊 Xamarin.Forms 使用的效果唯一識別碼以及群組名稱，先找到效果，再將它套用至控制項。 屬性會採用兩個參數 – 效果的類型名稱，以及用來找出效果再將它套用至控制項的唯一字串。

然後就可以將效果附加至適當控制項來使用效果。

> [!NOTE]
> 您可以選擇每個平台專案是否提供效果。 嘗試在未註冊的情況下使用效果，會傳回不執行任何動作的非 Null 值。

範例應用程式示範 `FocusEffect`，在控制項獲得焦點時變更控制項的背景色彩。 下圖說明範例應用程式中每個專案的責任，以及它們之間的關聯性：

![](creating-images/focus-effect.png "焦點效果專案責任")

`HomePage` 上的 [`Entry`](xref:Xamarin.Forms.Entry) 控制項是由每個平台特定專案中的 `FocusEffect` 類別自訂。 每個 `FocusEffect` 類別都衍生自每個平台的 `PlatformEffect` 類別。 這會導致以平台特定背景色彩轉譯 `Entry` 控制項，在控制項取得焦點時變更，如下列螢幕擷取畫面所示：

![](creating-images/screenshots-1.png "每個平台上的焦點效果")
![](creating-images/screenshots-2.png "每個平台上的焦點效果")

## <a name="creating-the-effect-on-each-platform"></a>在每個平台上建立效果

下列各節會討論 `FocusEffect` 類別的平台特定實作。

## <a name="ios-project"></a>iOS 專案

下列程式碼範例會示範 iOS 專案的 `FocusEffect` 實作：

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Platform.iOS;

[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(EffectsDemo.iOS.FocusEffect), nameof(EffectsDemo.iOS.FocusEffect))]
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

`OnAttached` 方法會使用 `UIColor.FromRGB` 方法將控制項的 `BackgroundColor` 屬性設為淺紫色，並將這個色彩也儲存在欄位中。 這項功能會包裝在 `try`/`catch` 區塊中，以免效果附加至的控制項沒有 `BackgroundColor` 屬性。 因為沒有必要的清除，所以 `OnDetached` 方法不提供實作。

`OnElementPropertyChanged` 覆寫會回應 Xamarin.Forms 控制項上可繫結屬性的變更。 當 [`IsFocused`](xref:Xamarin.Forms.VisualElement.IsFocused) 屬性變更時，如果控制項有焦點，則控制項的 `BackgroundColor` 屬性會變更為白色，否則會變更為淺紫色。 這項功能會包裝在 `try`/`catch` 區塊中，以免效果附加至的控制項沒有 `BackgroundColor` 屬性。

## <a name="android-project"></a>Android 專案

下列程式碼範例會示範 Android 專案的 `FocusEffect` 實作：

```csharp
using System;
using Xamarin.Forms;
using Xamarin.Forms.Platform.Android;

[assembly: ResolutionGroupName("MyCompany")]
[assembly: ExportEffect(typeof(EffectsDemo.Droid.FocusEffect), nameof(EffectsDemo.Droid.FocusEffect))]
namespace EffectsDemo.Droid
{
    public class FocusEffect : PlatformEffect
    {
        Android.Graphics.Color originalBackgroundColor = new Android.Graphics.Color(0, 0, 0, 0);
        Android.Graphics.Color backgroundColor;

        protected override void OnAttached()
        {
            try
            {
                backgroundColor = Android.Graphics.Color.LightGreen;
                Control.SetBackgroundColor(backgroundColor);
            }
            catch (Exception ex)
            {
                Console.WriteLine("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached()
        {
        }

        protected override void OnElementPropertyChanged(System.ComponentModel.PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(args);
            try
            {
                if (args.PropertyName == "IsFocused")
                {
                    if (((Android.Graphics.Drawables.ColorDrawable)Control.Background).Color == backgroundColor)
                    {
                        Control.SetBackgroundColor(originalBackgroundColor);
                    }
                    else
                    {
                        Control.SetBackgroundColor(backgroundColor);
                    }
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Cannot set property on attached control. Error: ", ex.Message);
            }
        }
    }
}
```

`OnAttached` 方法呼叫 `SetBackgroundColor` 方法將控制項的背景色彩設定為淺綠色，並將這個色彩也儲存在欄位中。 這項功能會包裝在 `try`/`catch` 區塊中，以免效果附加至的控制項沒有 `SetBackgroundColor` 屬性。 因為沒有必要的清除，所以 `OnDetached` 方法不提供實作。

`OnElementPropertyChanged` 覆寫會回應 Xamarin.Forms 控制項上可繫結屬性的變更。 當 [`IsFocused`](xref:Xamarin.Forms.VisualElement.IsFocused) 屬性變更時，如果控制項有焦點，則控制項的背景色彩會變更為白色，否則會變更為淺綠色。 這項功能會包裝在 `try`/`catch` 區塊中，以免效果附加至的控制項沒有 `BackgroundColor` 屬性。

## <a name="universal-windows-platform-projects"></a>通用 Windows 平台專案

下列程式碼範例示範通用 Windows 平台 (UWP) 專案的 `FocusEffect` 實作：

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Platform.UWP;

[assembly: ResolutionGroupName("MyCompany")]
[assembly: ExportEffect(typeof(EffectsDemo.UWP.FocusEffect), nameof(EffectsDemo.UWP.FocusEffect))]
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

`OnAttached` 方法會將控制項的 `Background` 屬性設成青色，並將 `BackgroundFocusBrush` 屬性設為白色。 這項功能會包裝在 `try`/`catch` 區塊中，以免效果附加至的控制項缺少這些屬性。 因為沒有必要的清除，所以 `OnDetached` 方法不提供實作。

## <a name="consuming-the-effect"></a>使用效果

使用 Xamarin.Forms .NET Standard 程式庫或共用程式庫專案效果的程序如下：

1. 宣告效果將要使用的控制項。
1. 藉由將效果新增至控制項的 [`Effects`](xref:Xamarin.Forms.Element.Effects) 集合，來將效果附加至控制項。

> [!NOTE]
> 效果執行個體只能附加至單一控制項。 因此，效果必須解析兩次，才能用於兩個控制項。

## <a name="consuming-the-effect-in-xaml"></a>在 XAML 中使用效果

下列 XAML 程式碼範例示範 `FocusEffect` 附加至的 [`Entry`](xref:Xamarin.Forms.Entry) 控制項：

```xaml
<Entry Text="Effect attached to an Entry" ...>
    <Entry.Effects>
        <local:FocusEffect />
    </Entry.Effects>
    ...
</Entry>
```

.NET Standard 程式庫中的 `FocusEffect` 類別支援在 XAML 中使用效果，如下列程式碼範例所示：

```csharp
public class FocusEffect : RoutingEffect
{
    public FocusEffect () : base ($"MyCompany.{nameof(FocusEffect)}")
    {
    }
}
```

`FocusEffect` 類別子類別化 [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect) 類別，其代表包裝內部效果的平台獨立效果通常是平台特定效果。 `FocusEffect` 類別會呼叫基底類別建構函式，傳入解析群組名稱串連構成的參數 (使用效果類別上的 [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) 屬性指定)，和使用效果類別上的 [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) 屬性指定的唯一識別碼。 因此，當 [`Entry`](xref:Xamarin.Forms.Entry) 在執行階段初始化後，`MyCompany.FocusEffect` 的新執行個體就會新增至控制項的 [`Effects`](xref:Xamarin.Forms.Element.Effects) 集合。

使用行為或使用附加屬性，也可以將效果附加至控制項。 如需使用行為將效果附加至控制項的詳細資訊，請參閱[可重複使用的 EffectBehavior](~/xamarin-forms/app-fundamentals/behaviors/reusable/effect-behavior.md)。 如需使用附加屬性將效果附加至控制項的詳細資訊，請參閱[將參數傳遞至效果](~/xamarin-forms/app-fundamentals/effects/passing-parameters/index.md)。

## <a name="consuming-the-effect-in-cnum"></a>在 C&num; 中使用效果

下列程式碼範例顯示 C# 中的對等 [`Entry`](xref:Xamarin.Forms.Entry)：

```csharp
var entry = new Entry {
  Text = "Effect attached to an Entry",
  ...
};
```

藉由將效果新增至控制項的 [`Effects`](xref:Xamarin.Forms.Element.Effects) 集合，來將 `FocusEffect` 附加至 `Entry` 執行個體，如下列程式碼範例所示：

```csharp
public HomePageCS ()
{
  ...
  entry.Effects.Add (Effect.Resolve ($"MyCompany.{nameof(FocusEffect)}"));
  ...
}
```

[`Effect.Resolve`](xref:Xamarin.Forms.Effect.Resolve(System.String)) 傳回指定名稱的 [`Effect`](xref:Xamarin.Forms.Effect)，它是解析群組名稱的串連 (使用效果類別上的 [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) 屬性指定)，和使用效果類別上的 [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) 屬性指定的唯一識別碼。 如果某個平台不提供效果，則 `Effect.Resolve` 方法會傳回非 `null` 值。

## <a name="summary"></a>總結

本文示範如何在控制項取得焦點時，建立變更 [`Entry`](xref:Xamarin.Forms.Entry) 控制項背景色彩的效果。


## <a name="related-links"></a>相關連結

- [自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Effect](xref:Xamarin.Forms.Effect)
- [PlatformEffect](xref:Xamarin.Forms.PlatformEffect`2)
- [Background Color Effect (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-backgroundcoloreffect) (背景色彩效果 (範例))
- [Focus Effect (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-focuseffect) (焦點效果 (範例))
