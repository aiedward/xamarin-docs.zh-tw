---
title: 平台專用
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用和建立平臺專屬。
ms.prod: xamarin
ms.assetid: 4729DB9C-8800-4E29-9D66-3BE13C5F8C94
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/01/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b029ffdb0a16d9f54951ae3754ca0f1eb7f69aaa
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556876"
---
# <a name="platform-specifics"></a>平台專用

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

_平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。_

透過 XAML 或流暢程式碼 API 取用平臺特定的程式如下所示：

1. 新增命名空間的宣告或指示詞 `xmlns` `using` [`Xamarin.Forms.PlatformConfiguration`](xref:Xamarin.Forms.PlatformConfiguration) 。
1. `xmlns` `using` 針對包含平臺特定功能的命名空間，加入宣告或指示詞：
    1. 在 iOS 上，這是 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 命名空間。
    1. 在 Android 上，這是 [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) 命名空間。 針對 Android AppCompat，這是 [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat) 命名空間。
    1. 在通用 Windows 平臺上，這是 [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) 命名空間。
1. 從 XAML 或使用流暢 API 的程式碼套用特定平臺 `On<T>` 。 的值 `T` 可以是 [`iOS`](xref:Xamarin.Forms.PlatformConfiguration.iOS) 、 [`Android`](xref:Xamarin.Forms.PlatformConfiguration.Android) 或 [`Windows`](xref:Xamarin.Forms.PlatformConfiguration.Windows) 來自 [`Xamarin.Forms.PlatformConfiguration`](xref:Xamarin.Forms.PlatformConfiguration) 命名空間的類型。

> [!NOTE]
> 請注意，嘗試在無法使用的平臺上使用平臺特定的平臺，將不會產生錯誤。 相反地，程式碼會在沒有套用平臺特定的情況下執行。

透過流暢程式碼 API 所取用的平臺細節會傳回 `On<T>` [`IPlatformElementConfiguration`](xref:Xamarin.Forms.IPlatformElementConfiguration`2) 物件。 這可讓您在相同的物件上，使用串聯方法來叫用多個平臺細節。

如需有關所提供之平臺細節的詳細資訊 Xamarin.Forms ，請參閱 [iOS 平臺](~/xamarin-forms/platform/ios/index.md)專屬、 [Android 平臺專用](~/xamarin-forms/platform/android/index.md)和 [Windows 平臺特定](~/xamarin-forms/platform/windows/index.md)。

## <a name="creating-platform-specifics"></a>建立平臺特定

廠商可以建立自己的平臺特定效果。 效果會提供特定功能，然後透過平臺特定來公開。 結果是可以更輕鬆地透過 XAML 和流暢的程式碼 API 取用的效果。

建立平臺特定的程式如下所示：

1. 將特定功能實作為效果。 如需詳細資訊，請參閱 [建立效果](~/xamarin-forms/app-fundamentals/effects/creating.md)。
1. 建立會公開效果的平臺特定類別。 如需詳細資訊，請參閱 [建立平臺特定類別](#creating-a-platform-specific-class)。
1. 在平臺專屬類別中，執行附加屬性，以允許透過 XAML 取用特定平臺。 如需詳細資訊，請參閱 [加入附加屬性](#adding-an-attached-property)。
1. 在平臺專屬類別中，執行擴充方法，以允許透過流暢的程式碼 API 取用特定平臺。 如需詳細資訊，請參閱 [新增擴充方法](#adding-extension-methods)。
1. 修改效果的執行，如此一來，只有在與效果相同的平臺上叫用平臺特定時，才會套用效果。 如需詳細資訊，請參閱 [建立效果](#creating-the-effect)。

將效果公開為平臺特定的結果，是可以更輕鬆地透過 XAML 和流暢的程式碼 API 取用效果。

> [!NOTE]
> 這是設想，廠商會使用這項技術來建立自己的平臺細節，方便使用者取用。 雖然使用者可以選擇建立自己的平臺專屬，但請注意，它所需的程式碼會比建立和使用效果更多。

[範例應用程式](/samples/xamarin/xamarin-forms-samples/userinterface-shadowplatformspecific)會示範 `Shadow` 平臺特定的，將陰影新增至控制項所顯示的文字 [`Label`](xref:Xamarin.Forms.Label) ：

![影子平臺特定](images/screenshots.png)

[範例應用程式](/samples/xamarin/xamarin-forms-samples/userinterface-shadowplatformspecific)會 `Shadow` 在每個平臺上執行平臺特定，以方便您瞭解。 不過，除了每個平臺特定的效果執行之外，每個平臺的影子類別的執行大致相同。 因此，本指南著重了影子類別的執行，以及在單一平臺上相關聯的效果。

如需效果的詳細資訊，請參閱 [使用效果自訂控制項](~/xamarin-forms/app-fundamentals/effects/index.md)。

### <a name="creating-a-platform-specific-class"></a>建立平臺特定類別

平臺特定會建立為 `public static` 類別：

```csharp
namespace MyCompany.Forms.PlatformConfiguration.iOS
{
  public static Shadow
  {
    ...
  }
}
```

下列各節將討論 `Shadow` 平臺特定和相關效果的執行。

#### <a name="adding-an-attached-property"></a>加入附加屬性

您必須將附加屬性新增至 `Shadow` 平臺特定的，以允許透過 XAML 的耗用量：

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

`IsShadowed`附加屬性是用來將效果加入 `MyCompany.LabelShadowEffect` 至類別所附加的控制項，並將其從移除 `Shadow` 。 此附加屬性會註冊 `OnIsShadowedPropertyChanged` 方法；當屬性的值變更時，就會執行此方法。 接著，這個方法會呼叫 `AttachEffect` 或 `DetachEffect` 方法，根據附加屬性的值來新增或移除效果 `IsShadowed` 。 您可以藉由修改控制項的集合，在控制項中加入或移除效果 [`Effects`](xref:Xamarin.Forms.Element.Effects) 。

> [!NOTE]
> 請注意，這個效果的解決方式是指定一個值，此值會串連在效果執行時所指定的解析組名和唯一識別碼。 如需詳細資訊，請參閱 [建立效果](~/xamarin-forms/app-fundamentals/effects/creating.md)。

如需附加屬性的詳細資訊，請參閱[附加屬性](~/xamarin-forms/xaml/attached-properties.md)。

#### <a name="adding-extension-methods"></a>新增擴充方法

擴充方法必須新增至 `Shadow` 平臺特定的，以允許透過流暢的程式碼 API 取用：

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

`IsShadowed`和 `SetIsShadowed` 延伸方法會分別叫用附加屬性的 get 和 set 存取子 `IsShadowed` 。 每個擴充方法都是在類型上操作 `IPlatformElementConfiguration<iOS, FormsElement>` ，指定可在 iOS 的實例上叫用平臺特定 [`Label`](xref:Xamarin.Forms.Label) 。

#### <a name="creating-the-effect"></a>建立效果

`Shadow`平臺特定會將加入 `MyCompany.LabelShadowEffect` 至 [`Label`](xref:Xamarin.Forms.Label) ，並將其移除。 下列程式碼範例示範 iOS 專案的 `LabelShadowEffect` 實作：

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

`UpdateShadow`方法會設定 `Control.Layer` 屬性來建立陰影，前提是 `IsShadowed` 附加屬性設定為 `true` ，而且前提 `Shadow` 是已在執行效果的相同平臺上叫用平臺特定。 這項檢查是以 `OnThisPlatform` 方法執行。

如果 `Shadow.IsShadowed` 附加屬性值在執行時間變更，則效果必須藉由移除陰影來回應。 因此，方法的覆寫版本 `OnElementPropertyChanged` 是用來藉由呼叫方法來回應可系結屬性變更 `UpdateShadow` 。

如需建立效果的詳細資訊，請參閱 [建立效果](~/xamarin-forms/app-fundamentals/effects/creating.md) 並 [傳遞效果參數作為附加屬性](~/xamarin-forms/app-fundamentals/effects/passing-parameters/attached-properties.md)。

### <a name="consuming-the-platform-specific"></a>使用平臺特定的

將 `Shadow` 附加屬性設定為值，即可在 XAML 中使用平臺特定 `Shadow.IsShadowed` `boolean` ：

```xaml
<ContentPage xmlns:ios="clr-namespace:MyCompany.Forms.PlatformConfiguration.iOS" ...>
  ...
  <Label Text="Label Shadow Effect" ios:Shadow.IsShadowed="true" ... />
  ...
</ContentPage>
```

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using MyCompany.Forms.PlatformConfiguration.iOS;

...

shadowLabel.On<iOS>().SetIsShadowed(true);
```

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [ShadowPlatformSpecific (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-shadowplatformspecific)
- [iOS 平台專用](~/xamarin-forms/platform/ios/index.md)
- [Android 平台專用](~/xamarin-forms/platform/android/index.md)
- [Windows 平臺-特定](~/xamarin-forms/platform/windows/index.md)
- [自訂具有效果的控制項](~/xamarin-forms/app-fundamentals/effects/index.md)
- [附加屬性](~/xamarin-forms/xaml/attached-properties.md)
- [PlatformConfiguration API](xref:Xamarin.Forms.PlatformConfiguration)