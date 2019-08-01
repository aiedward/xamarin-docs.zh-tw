---
title: 平台特性
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 本文說明如何使用和建立平臺細節。
ms.prod: xamarin
ms.assetid: 4729DB9C-8800-4E29-9D66-3BE13C5F8C94
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/01/2018
ms.openlocfilehash: f6190b9c0d29d57d6d509bdff25e2ce3572e3a3c
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68646841"
---
# <a name="platform-specifics"></a>平台專用

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

_平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。_

使用特定平台-透過 XAML，或透過 fluent 的程式碼 API 的程序如下所示：

1. 新增`xmlns`宣告或`using`指示詞[ `Xamarin.Forms.PlatformConfiguration` ](xref:Xamarin.Forms.PlatformConfiguration)命名空間。
1. 新增`xmlns`宣告或`using`包含平台特定功能之命名空間指示詞：
    1. 在 iOS 上，這是[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間。
    1. 在 Android 上，這是[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空間。 這是針對 Android 的 AppCompat [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)命名空間。
    1. 在通用 Windows 平台，這是[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空間。
1. 從 XAML，或使用的程式碼，適用於特定平台`On<T>`fluent API。 值`T`可以是[ `iOS` ](xref:Xamarin.Forms.PlatformConfiguration.iOS)， [ `Android` ](xref:Xamarin.Forms.PlatformConfiguration.Android)，或[ `Windows` ](xref:Xamarin.Forms.PlatformConfiguration.Windows)類型[ `Xamarin.Forms.PlatformConfiguration`](xref:Xamarin.Forms.PlatformConfiguration)命名空間。

> [!NOTE]
> 請注意，嘗試使用就無法使用的平台上的平台專屬不會導致錯誤。 相反地，程式碼會執行而不需要平台特定方式套用。

透過平台特性取用`On<T>`fluent 的程式碼 API 傳回[ `IPlatformElementConfiguration` ](xref:Xamarin.Forms.IPlatformElementConfiguration`2)物件。 這可讓具有階層式方法的相同物件上叫用的多個平台特性。

如需有關 Xamarin 所提供之平臺細節的詳細資訊, 請參閱[IOS 平臺](~/xamarin-forms/platform/ios/index.md)專屬的[Android 平臺](~/xamarin-forms/platform/android/index.md)詳細資料和[Windows 平臺](~/xamarin-forms/platform/windows/index.md)詳細資料。

## <a name="creating-platform-specifics"></a>建立平臺細節

廠商可以使用效果來建立自己的平臺細節。 效果會提供專用的功能，則透過特定平台公開。 結果是可以在透過 XAML，以及透過 fluent 的程式碼 API 更容易取用的效果。

建立特定平台的程序如下所示：

1. 實作特定功能的效應。 如需詳細資訊，請參閱 <<c0> [ 建立效果](~/xamarin-forms/app-fundamentals/effects/creating.md)。
1. 建立平台特定類別會公開 （expose） 的效果。 如需詳細資訊，請參閱 <<c0> [ 建立平台特定類別](#creating-a-platform-specific-class)。
1. 在平台特定類別中實作以允許特定平台-使用透過 XAML 附加的屬性。 如需詳細資訊，請參閱 <<c0> [ 將附加屬性加入](#adding-an-attached-property)。
1. 在平台特定類別中實作以允許透過 fluent 的程式碼 API 使用平台專屬的擴充方法。 如需詳細資訊，請參閱 <<c0> [ 加入擴充方法](#adding-extension-methods)。
1. 修改影響實作，讓效果僅適用於特定平台叫用為效果相同的平台上。 如需詳細資訊，請參閱 <<c0> [ 建立效果](#creating-the-effect)。

公開為平台特定效果的結果是，效果可以更輕鬆地使用透過 XAML，以及透過 fluent 的程式碼 API。

> [!NOTE]
> 它被設想廠商會使用此技巧來建立自己平台特性，以方便取用的使用者。 雖然使用者可以選擇建立自己的平台特性，請注意，它需要更多的程式碼，比建立及使用的效果。

[範例應用程式](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shadowplatformspecific)會示範`Shadow`平臺特定的, 其會將陰影新增至[`Label`](xref:Xamarin.Forms.Label)控制項所顯示的文字:

![](images/screenshots.png "陰影特定平台")

[範例應用程式](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shadowplatformspecific) `Shadow`會在每個平臺上執行平臺特定, 以方便您瞭解。 不過，除了每個平台特定效果實作陰影類別的實作是每個平台基本相同的。 因此，本指南著重於實作陰影類別和相關聯的單一平台上的效果。

如需效果的詳細資訊，請參閱[效果的自訂控制項](~/xamarin-forms/app-fundamentals/effects/index.md)。

### <a name="creating-a-platform-specific-class"></a>建立平臺特定類別

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

下列各節討論實作`Shadow`平台特定和相關聯的效果。

#### <a name="adding-an-attached-property"></a>加入附加屬性

附加的屬性必須新增至`Shadow`平台特定，以允許透過 XAML 的耗用量：

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

`IsShadowed`附加的屬性用來新增`MyCompany.LabelShadowEffect`生效，並從控制項移除的`Shadow`類別會附加至。 此連接屬性暫存器`OnIsShadowedPropertyChanged`屬性的值變更時，會執行的方法。 接著，這個方法會呼叫`AttachEffect`或`DetachEffect`值為基礎的方法來新增或移除效果`IsShadowed`附加屬性。 加入或移除控制項，藉由修改控制項的效果[ `Effects` ](xref:Xamarin.Forms.Element.Effects)集合。

> [!NOTE]
> 請注意，效果的解決方式是指定為解析群組名稱和指定效果實作的唯一識別碼的串連的值。 如需詳細資訊，請參閱 <<c0> [ 建立效果](~/xamarin-forms/app-fundamentals/effects/creating.md)。

如需有關附加屬性的詳細資訊，請參閱[附加屬性](~/xamarin-forms/xaml/attached-properties.md)。

#### <a name="adding-extension-methods"></a>加入擴充方法

擴充方法必須新增至`Shadow`平台特定，以允許透過 fluent 的程式碼 API 的耗用量：

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

`IsShadowed`並`SetIsShadowed`擴充方法叫用 get 和 set 存取子的`IsShadowed`附加屬性，分別。 每個擴充方法作`IPlatformElementConfiguration<iOS, FormsElement>`型別，指定可以在上叫用平台專屬[ `Label` ](xref:Xamarin.Forms.Label) iOS 中的執行個體。

#### <a name="creating-the-effect"></a>建立效果

`Shadow`特定平台將加入`MyCompany.LabelShadowEffect`要[ `Label` ](xref:Xamarin.Forms.Label)，並將它移除。 下列程式碼範例示範`LabelShadowEffect`針對 iOS 專案的實作：

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

`UpdateShadow`方法會設定`Control.Layer`屬性，以建立陰影，前提是`IsShadowed`附加的屬性設定為`true`，並假設`Shadow`特定平台叫用相同的平台上，效果被實作的。 這項檢查會執行與`OnThisPlatform`方法。

如果`Shadow.IsShadowed`附加屬性值變更在執行階段，回應所移除之陰影效果需求。 因此，覆寫的版本的`OnElementPropertyChanged`方法來呼叫回應可繫結的屬性變更`UpdateShadow`方法。

如需建立效果的詳細資訊，請參閱[建立影響](~/xamarin-forms/app-fundamentals/effects/creating.md)並[傳遞為附加屬性的效果參數](~/xamarin-forms/app-fundamentals/effects/passing-parameters/attached-properties.md)。

### <a name="consuming-the-platform-specific"></a>使用平臺特定

`Shadow`特定平台由在 XAML 中設定`Shadow.IsShadowed`; 附加屬性`boolean`值：

```xaml
<ContentPage xmlns:ios="clr-namespace:MyCompany.Forms.PlatformConfiguration.iOS" ...>
  ...
  <Label Text="Label Shadow Effect" ios:Shadow.IsShadowed="true" ... />
  ...
</ContentPage>
```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using MyCompany.Forms.PlatformConfiguration.iOS;

...

shadowLabel.On<iOS>().SetIsShadowed(true);
```

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [ShadowPlatformSpecific （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shadowplatformspecific)
- [iOS 平臺-細節](~/xamarin-forms/platform/ios/index.md)
- [Android 平臺-細節](~/xamarin-forms/platform/android/index.md)
- [Windows 平臺-細節](~/xamarin-forms/platform/windows/index.md)
- [自訂控制項產生的作用](~/xamarin-forms/app-fundamentals/effects/index.md)
- [附加屬性](~/xamarin-forms/xaml/attached-properties.md)
- [PlatformConfiguration API](xref:Xamarin.Forms.PlatformConfiguration)
