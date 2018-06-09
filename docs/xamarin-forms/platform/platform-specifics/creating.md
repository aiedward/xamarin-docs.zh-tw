---
title: 建立平台特性
description: 本文將示範如何公開透過平台專屬的效果。
ms.prod: xamarin
ms.assetid: 0D0E6274-6EF2-4D40-BB77-3D8E53BCD24B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/23/2016
ms.openlocfilehash: a07ff2e206a08ee40355733ab2c1026f29de2f2f
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35242780"
---
# <a name="creating-platform-specifics"></a>建立平台特性

_廠商可以建立自己的平台特性效果。效果提供的特定功能，然後透過特定平台。結果是可以在 XAML 中，透過和 fluent 應用程式開發的程式碼 API 更輕鬆地取用的效果。本文將示範如何公開透過平台專屬的效果。_

## <a name="overview"></a>總覽

建立特定平台的程序如下所示：

1. 實作特定功能的效應。 如需詳細資訊，請參閱[建立效果](~/xamarin-forms/app-fundamentals/effects/creating.md)。
1. 建立會公開 （expose） 的效果的平台特定類別。 如需詳細資訊，請參閱[建立平台專屬類別](#creating)。
1. 平台特定類別中實作以允許透過 XAML 使用平台專屬附加的屬性。 如需詳細資訊，請參閱[將附加屬性加入](#attached_property)。
1. 平台特定類別中實作以允許透過 fluent 應用程式開發的程式碼應用程式開發介面使用平台專屬的擴充方法。 如需詳細資訊，請參閱[加入擴充方法](#extension_methods)。
1. 修改的效果實作，使效果僅適用於特定平台叫用相同的平台為效果上。 如需詳細資訊，請參閱[建立效果](#creating_the_effect)。

公開特定平台的效果的結果是，效果可更輕鬆地使用透過 XAML 和 fluent 應用程式開發的程式碼應用程式開發介面。

> [!NOTE]
> 它被 envisaged 廠商會使用這項技術，來建立自己平台-細節，以簡化使用者的耗用量。 雖然使用者可能選擇建立自己的平台特性，請注意，它需要更多的程式碼建立和使用效果比。

範例應用程式示範`Shadow`新增所顯示的文字陰影的平台特定[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)控制項：

![](creating-images/screenshots.png "平台專屬的陰影")

範例應用程式會實作`Shadow`特定平台的每個平台上，為了便於了解。 不過，每個平台專屬效果實作中，除了陰影類別的實作是基本相同，每個平台。 因此，本指南著重於陰影類別和相關聯的效果，在單一平台上的實作。

如需效果的詳細資訊，請參閱[效果的自訂控制項](~/xamarin-forms/app-fundamentals/effects/index.md)。

<a name="creating" />

## <a name="creating-a-platform-specific-class"></a>建立平台專屬類別

特定平台會建立為`public static`類別：

```csharp
namespace MyCompany.Forms.PlatformConfiguration.iOS
{
  public static Shadow
  {
    ...
  }
}
```

下列章節會討論的實作`Shadow`特定平台和相關聯的效果。

<a name="attached_property" />

### <a name="adding-an-attached-property"></a>加入附加的屬性

附加的屬性必須新增至`Shadow`特定平台以允許透過 XAML 耗用量：

```csharp
namespace MyCompany.Forms.PlatformConfiguration.iOS
{
    using System.Linq;
    using Xamarin.Forms;
    using Xamarin.Forms.PlatformConfiguration;
    using FormsElement = Xamarin.Forms.Label;

    public static class Shadow
    {
        const string EffectName = "MyCompany.LabelShadowEffect";

        public static readonly BindableProperty IsShadowedProperty =
            BindableProperty.CreateAttached("IsShadowed",
                                            typeof(bool),
                                            typeof(Shadow),
                                            false,
                                            propertyChanged: OnIsShadowedPropertyChanged);

        public static bool GetIsShadowed(BindableObject element)
        {
            return (bool)element.GetValue(IsShadowedProperty);
        }

        public static void SetIsShadowed(BindableObject element, bool value)
        {
            element.SetValue(IsShadowedProperty, value);
        }

        ...

        static void OnIsShadowedPropertyChanged(BindableObject element, object oldValue, object newValue)
        {
            if ((bool)newValue)
            {
                AttachEffect(element as FormsElement);
            }
            else
            {
                DetachEffect(element as FormsElement);
            }
        }

        static void AttachEffect(FormsElement element)
        {
            IElementController controller = element;
            if (controller == null || controller.EffectIsAttached(EffectName))
            {
                return;
            }
            element.Effects.Add(Effect.Resolve(EffectName));
        }

        static void DetachEffect(FormsElement element)
        {
            IElementController controller = element;
            if (controller == null || !controller.EffectIsAttached(EffectName))
            {
                return;
            }

            var toRemove = element.Effects.FirstOrDefault(e => e.ResolveId == Effect.Resolve(EffectName).ResolveId);
            if (toRemove != null)
            {
                element.Effects.Remove(toRemove);
            }
        }
    }
}
```

`IsShadowed`附加的屬性用來新增`MyCompany.LabelShadowEffect`生效，並從控制項移除的`Shadow`類別附加到。 這個檔案附加屬性的暫存器`OnIsShadowedPropertyChanged`屬性的值變更時，會執行的方法。 接著，這個方法會呼叫`AttachEffect`或`DetachEffect`值為基礎的方法，以新增或移除效果`IsShadowed`附加屬性。 若要加入或移除從控制項，藉由修改控制項的效果[ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/)集合。

> [!NOTE]
> 請注意，效果的解決方式是指定為解析群組名稱和唯一識別碼的效果實作上所指定的串連的值。 如需詳細資訊，請參閱[建立效果](~/xamarin-forms/app-fundamentals/effects/creating.md)。

如需附加屬性的詳細資訊，請參閱[附加屬性](~/xamarin-forms/xaml/attached-properties.md)。

<a name="extension_methods" />

### <a name="adding-extension-methods"></a>加入擴充方法

擴充方法必須新增至`Shadow`特定平台以允許透過 fluent 應用程式開發的程式碼應用程式開發介面的耗用量：

```csharp
namespace MyCompany.Forms.PlatformConfiguration.iOS
{
    using System.Linq;
    using Xamarin.Forms;
    using Xamarin.Forms.PlatformConfiguration;
    using FormsElement = Xamarin.Forms.Label;

    public static class Shadow
    {
        ...
        public static bool IsShadowed(this IPlatformElementConfiguration<iOS, FormsElement> config)
        {
            return GetIsShadowed(config.Element);
        }

        public static IPlatformElementConfiguration<iOS, FormsElement> SetIsShadowed(this IPlatformElementConfiguration<iOS, FormsElement> config, bool value)
        {
            SetIsShadowed(config.Element, value);
            return config;
        }
        ...
    }
}
```

`IsShadowed`和`SetIsShadowed`擴充方法叫用 get 和 set 存取子的`IsShadowed`分別附加屬性。 每個擴充方法對`IPlatformElementConfiguration<iOS, FormsElement>`類型，指定可以在上叫用平台專屬[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)從 iOS 的執行個體。

<a name="creating_the_effect" />

### <a name="creating-the-effect"></a>建立效果

`Shadow`特定平台將加入`MyCompany.LabelShadowEffect`至[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)，並將它移除。 下列程式碼範例示範`LabelShadowEffect`實作針對 iOS 專案：

```csharp
[assembly: ResolutionGroupName("MyCompany")]
[assembly: ExportEffect(typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace ShadowPlatformSpecific.iOS
{
    public class LabelShadowEffect : PlatformEffect
    {
        protected override void OnAttached()
        {
            UpdateShadow();
        }

        protected override void OnDetached()
        {
        }

        protected override void OnElementPropertyChanged(PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(args);

            if (args.PropertyName == Shadow.IsShadowedProperty.PropertyName)
            {
                UpdateShadow();
            }
        }

        void UpdateShadow()
        {
            try
            {
                if (((Label)Element).OnThisPlatform().IsShadowed())
                {
                    Control.Layer.CornerRadius = 5;
                    Control.Layer.ShadowColor = UIColor.Black.CGColor;
                    Control.Layer.ShadowOffset = new CGSize(5, 5);
                    Control.Layer.ShadowOpacity = 1.0f;
                }
                else if (!((Label)Element).OnThisPlatform().IsShadowed())
                {
                    Control.Layer.ShadowOpacity = 0;
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

`UpdateShadow`方法會設定`Control.Layer`屬性來建立陰影，但前提是`IsShadowed`附加的屬性設定為`true`，並假設`Shadow`特定平台叫用相同的平台上，效果，則實作。 這項檢查會執行與`OnThisPlatform`方法。

如果`Shadow.IsShadowed`附加屬性值變更在執行階段，來回應的移除陰影效果需求。 因此，覆寫的版本的`OnElementPropertyChanged`方法用來回應可繫結的屬性變更的呼叫`UpdateShadow`方法。

如需建立效果的詳細資訊，請參閱[建立效果](~/xamarin-forms/app-fundamentals/effects/creating.md)和[傳遞做為附加屬性的效果參數](~/xamarin-forms/app-fundamentals/effects/passing-parameters/attached-properties.md)。

## <a name="consuming-a-platform-specific"></a>使用特定平台

`Shadow`特定平台由在 XAML 中設定`Shadow.IsShadowed`附加屬性`boolean`值：

```xaml
<ContentPage xmlns:ios="clr-namespace:MyCompany.Forms.PlatformConfiguration.iOS" ...>
  ...
  <Label Text="Label Shadow Effect" ios:Shadow.IsShadowed="true" ... />
  ...
</ContentPage>
```

或者，可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using MyCompany.Forms.PlatformConfiguration.iOS;

...

shadowLabel.On<iOS>().SetIsShadowed(true);
```

如需使用平台細節的詳細資訊，請參閱[耗用的平台特性](~/xamarin-forms/platform/platform-specifics/consuming/index.md)。

## <a name="summary"></a>總結

本文示範如何公開透過平台專屬的效果。 結果是可以在 XAML 中，透過和 fluent 應用程式開發的程式碼 API 更輕鬆地取用的效果。


## <a name="related-links"></a>相關連結

- [ShadowPlatformSpecific （範例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/shadowplatformspecific/)
- [自訂控制項和效果](~/xamarin-forms/app-fundamentals/effects/index.md)
- [附加屬性](~/xamarin-forms/xaml/attached-properties.md)
