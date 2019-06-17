---
title: 建立自訂的 Xamarin.Forms 佈景主題
description: 這篇文章說明如何建立自訂的 Xamarin.Forms 佈景主題中的應用程式用來參考。
ms.prod: xamarin
ms.assetid: 4FE08ADC-093F-47FA-B33C-20CF08B5D7E0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/01/2017
ms.openlocfilehash: 0897afeacffc89e30b28474e4530a83d05d33cd2
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61187279"
---
# <a name="creating-a-custom-xamarinforms-theme"></a>建立自訂的 Xamarin.Forms 佈景主題

![](~/media/shared/preview.png "此 API 目前為預覽狀態")

除了從 Nuget 套件新增佈景主題 (例如[Light](~/xamarin-forms/user-interface/themes/light.md)並[深色](~/xamarin-forms/user-interface/themes/dark.md)佈景主題)，可以在您的應用程式中建立您自己的資源字典，可參考的佈景主題。

## <a name="example"></a>範例

三個`BoxView`上顯示的 s[佈景主題頁面](~/xamarin-forms/user-interface/themes/index.md)根據兩個可下載的主題中所定義的三個類別的樣式設定。

若要了解這些的運作方式，下列標記會建立對等的樣式，您無法直接將新增至您**App.xaml**。

附註`Class`屬性`Style`(相對於 [`x:Key`](~/xamarin-forms/user-interface/styles/inheritance.md)
屬性適用於 Xamarin.Forms 較早版本）。

```xml
<ResourceDictionary>
  <!-- DEFINE ANY CONSTANTS -->
  <Color x:Key="SeparatorLineColor">#CCCCCC</Color>
  <Color x:Key="iOSDefaultTintColor">#007aff</Color>
  <Color x:Key="AndroidDefaultAccentColorColor">#1FAECE</Color>
  <OnPlatform x:TypeArguments="Color" x:Key="AccentColor">
    <On Platform="iOS" Value="{StaticResource iOSDefaultTintColor}" />
    <On Platform="Android" Value="{StaticResource AndroidDefaultAccentColorColor}" />
  </OnPlatform>
  <!--  BOXVIEW CLASSES -->
  <Style TargetType="BoxView" Class="HorizontalRule">
    <Setter Property="BackgroundColor" Value="{ StaticResource SeparatorLineColor }" />
    <Setter Property="HeightRequest" Value="1" />
  </Style>

  <Style TargetType="BoxView" Class="Circle">
    <Setter Property="BackgroundColor" Value="{ StaticResource AccentColor }" />
    <Setter Property="WidthRequest" Value="34"/>
    <Setter Property="HeightRequest" Value="34"/>
    <Setter Property="HorizontalOptions" Value="Start" />

    <Setter Property="local:ThemeEffects.Circle" Value="True" />
  </Style>

  <Style TargetType="BoxView" Class="Rounded">
    <Setter Property="BackgroundColor" Value="{ StaticResource AccentColor }" />
    <Setter Property="HorizontalOptions" Value="Start" />
    <Setter Property="BackgroundColor" Value="{ StaticResource AccentColor }" />

    <Setter Property="local:ThemeEffects.CornerRadius" Value="4" />
  </Style>
</ResourceDictionary>
```

您會發現`Rounded`類別是指自訂效果`CornerRadius`。
這種效果的程式碼如下所示的方式來參考它正確自訂`xmlns`必須加入至**App.xaml**的根項目：

```csharp
xmlns:local="clr-namespace:ThemesDemo;assembly=ThemesDemo"
```

### <a name="c-code-in-the-net-standard-library-project-or-shared-project"></a>.NET Standard 程式庫專案或共用專案中的 C# 程式碼

建立循右上角的程式碼`BoxView`會使用[效果](~/xamarin-forms/app-fundamentals/effects/index.md)。
使用套用的圓角半徑`BindableProperty`並實作藉由套用[效果](~/xamarin-forms/app-fundamentals/effects/index.md)。 效果要求中的平台特定程式碼[iOS](#ios)並[Android](#android)專案 （如下所示。）

```csharp
namespace ThemesDemo
{
  public static class ThemeEffects
  {
  public static readonly BindableProperty CornerRadiusProperty =
    BindableProperty.CreateAttached("CornerRadius", typeof(double), typeof(ThemeEffects), 0.0, propertyChanged: OnChanged<CornerRadiusEffect, double>);
    private static void OnChanged<TEffect, TProp>(BindableObject bindable, object oldValue, object newValue)
              where TEffect : Effect, new()
    {
        if (!(bindable is View view))
        {
            return;
        }

        if (EqualityComparer<TProp>.Equals(newValue, default(TProp)))
        {
            var toRemove = view.Effects.FirstOrDefault(e => e is TEffect);
            if (toRemove != null)
            {
                view.Effects.Remove(toRemove);
            }
        }
        else
        {
            view.Effects.Add(new TEffect());
        }

    }
    public static void SetCornerRadius(BindableObject view, double radius)
    {
        view.SetValue(CornerRadiusProperty, radius);
    }

    public static double GetCornerRadius(BindableObject view)
    {
        return (double)view.GetValue(CornerRadiusProperty);
    }

    private class CornerRadiusEffect : RoutingEffect
    {
        public CornerRadiusEffect()
            : base("Xamarin.CornerRadiusEffect")
        {
        }
    }
  }
}
```

<a name="ios" />

### <a name="c-code-in-the-ios-project"></a>在 iOS 專案的 C# 程式碼

```csharp
using System;
using Xamarin.Forms;
using Xamarin.Forms.Platform.iOS;
using CoreGraphics;
using Foundation;
using XFThemes;

namespace ThemesDemo.iOS
{
    public class CornerRadiusEffect : PlatformEffect
    {
        private nfloat _originalRadius;

        protected override void OnAttached()
        {
            if (Container != null)
            {
                _originalRadius = Container.Layer.CornerRadius;
                Container.ClipsToBounds = true;

                UpdateCorner();
            }
        }

        protected override void OnDetached()
        {
            if (Container != null)
            {
                Container.Layer.CornerRadius = _originalRadius;
                Container.ClipsToBounds = false;
            }
        }

        protected override void OnElementPropertyChanged(System.ComponentModel.PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(args);

            if (args.PropertyName == ThemeEffects.CornerRadiusProperty.PropertyName)
            {
                UpdateCorner();
            }
        }

        private void UpdateCorner()
        {
            Container.Layer.CornerRadius = (nfloat)ThemeEffects.GetCornerRadius(Element);
        }
    }
}
```

<a name="android" />

### <a name="c-code-in-the-android-project"></a>在 Android 專案中的 C# 程式碼

```csharp
using System;
using Xamarin.Forms.Platform;
using Xamarin.Forms.Platform.Android;
using Android.Views;
using Android.Graphics;

namespace ThemesDemo.Droid
{
    public class CornerRadiusEffect : BaseEffect
    {
        private ViewOutlineProvider _originalProvider;

        protected override bool CanBeApplied()
        {
            return Container != null && Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop;
        }

        protected override void OnAttachedInternal()
        {
            _originalProvider = Container.OutlineProvider;
            Container.OutlineProvider = new CornerRadiusOutlineProvider(Element);
            Container.ClipToOutline = true;
        }

        protected override void OnDetachedInternal()
        {
            Container.OutlineProvider = _originalProvider;
            Container.ClipToOutline = false;
        }

        protected override void OnElementPropertyChanged(System.ComponentModel.PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(args);

            if (!Attached)
            {
                return;
            }

            if (args.PropertyName == ThemeEffects.CornerRadiusProperty.PropertyName)
            {
                Container.Invalidate();
            }
        }

        private class CornerRadiusOutlineProvider : ViewOutlineProvider
        {
            private Xamarin.Forms.Element _element;

            public CornerRadiusOutlineProvider(Xamarin.Forms.Element element)
            {
                _element = element;
            }

            public override void GetOutline(Android.Views.View view, Outline outline)
            {
                var pixels =
                    (float)ThemeEffects.GetCornerRadius(_element) *
                    view.Resources.DisplayMetrics.Density;

                outline.SetRoundRect(new Rect(0, 0, view.Width, view.Height), (int)pixels);
            }
        }
    }
}
```


## <a name="summary"></a>總結

定義每個控制項需要自訂外觀的樣式，可以建立自訂佈景主題。 控制項的多個樣式應該可區別不同`Class`屬性在資源字典中，並藉由設定套用`StyleClass`控制項上的屬性。

樣式也可以利用[效果](~/xamarin-forms/app-fundamentals/effects/index.md)來進一步自訂控制項的外觀。

[隱含樣式](~/xamarin-forms/user-interface/styles/implicit.md)(但沒有`x:Key`或是`Style`屬性) 繼續套用到符合的所有控制項`TargetType`。
