---
title: Xamarin.Forms可重複使用的 RoundEffect
description: RoundEffect 是可重複使用的效果，可以套用至衍生自 VisualElement 的任何控制項，以將控制項轉譯為圓形。
ms.prod: xamarin
ms.assetid: B5DE7507-B565-4EE5-9897-27E5733FD173
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 10/25/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: fc3776934a4c109b2527132b11c6c6a93b7d9f9e
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84138849"
---
# <a name="xamarinforms-reusable-roundeffect"></a>Xamarin.Forms可重複使用的 RoundEffect

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-roundeffect/)

RoundEffect 會將衍生自 VisualElement 的任何控制項簡化為圓形。 這種效果可以用來建立圓形影像、按鈕和其他控制項：

[![RoundEffect iOS 和 Android 上的螢幕擷取畫面](example-roundeffect-images/round-effect-cropped.png)](example-roundeffect-images/round-effect.png#lightbox)

## <a name="create-a-shared-routingeffect"></a>建立共用 RoutingEffect

必須在共用的專案中建立效果類別，以建立跨平臺效果。 範例應用程式會建立一個 `RoundEffect` 衍生自類別的空 `RoutingEffect` 類別：

```csharp
public class RoundEffect : RoutingEffect
{
    public RoundEffect() : base($"Xamarin.{nameof(RoundEffect)}")
    {
    }
}
```

這個類別可讓共用的專案解析程式代碼或 XAML 中的效果參考，但不提供任何功能。 效果必須具有每個平臺的「執行」。

## <a name="implement-the-android-effect"></a>執行 Android 效果

Android 平臺專案 `RoundEffect` 會定義衍生自的類別 `PlatformEffect` 。 這個類別會以 `assembly` 允許解析效果類別的屬性來標記 Xamarin.Forms ：

```csharp
[assembly: ResolutionGroupName("Xamarin")]
[assembly: ExportEffect(typeof(RoundEffectDemo.Droid.RoundEffect), nameof(RoundEffectDemo.Droid.RoundEffect))]
namespace RoundEffectDemo.Droid
{
    public class RoundEffect : PlatformEffect
    {
        // ...
    }
}
```

Android 平臺會使用的概念 `OutlineProvider` 來定義控制項的邊緣。 範例專案包含 `CornerRadiusProvider` 衍生自類別的類別 `ViewOutlineProvider` ：

```csharp
class CornerRadiusOutlineProvider : ViewOutlineProvider
{
    Element element;

    public CornerRadiusOutlineProvider(Element formsElement)
    {
        element = formsElement;
    }

    public override void GetOutline(Android.Views.View view, Outline outline)
    {
        float scale = view.Resources.DisplayMetrics.Density;
        double width = (double)element.GetValue(VisualElement.WidthProperty) * scale;
        double height = (double)element.GetValue(VisualElement.HeightProperty) * scale;
        float minDimension = (float)Math.Min(height, width);
        float radius = minDimension / 2f;
        Rect rect = new Rect(0, 0, (int)width, (int)height);
        outline.SetRoundRect(rect, radius);
    }
}
```

這個類別會使用 `Width` 實例的和 `Height` 屬性 Xamarin.Forms `Element` 來計算屬於最短維度一半的半徑。

定義大綱提供者之後， `RoundEffect` 類別就可以取用它來執行效果：

```csharp
public class RoundEffect : PlatformEffect
{
    ViewOutlineProvider originalProvider;
    Android.Views.View effectTarget;

    protected override void OnAttached()
    {
        try
        {
            effectTarget = Control ?? Container;
            originalProvider = effectTarget.OutlineProvider;
            effectTarget.OutlineProvider = new CornerRadiusOutlineProvider(Element);
            effectTarget.ClipToOutline = true;
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Failed to set corner radius: {ex.Message}");
        }
    }

    protected override void OnDetached()
    {
        if(effectTarget != null)
        {
            effectTarget.OutlineProvider = originalProvider;
            effectTarget.ClipToOutline = false;
        }
    }
}
```

`OnAttached`當效果附加至元素時，會呼叫方法。 會儲存現有的物件，以便在中斷連結 `OutlineProvider` 效果時可以還原。 的新實例會當做 `CornerRadiusOutlineProvider` 使用 `OutlineProvider` ，而且 `ClipToOutline` 會設定為 true，以將溢位的元素裁剪至外框框線。

`OnDetatched`從專案中移除效果並還原原始值時，會呼叫方法 `OutlineProvider` 。

> [!NOTE]
> 視元素類型而定， `Control` 屬性不一定是 null。 如果 `Control` 屬性不是 null，則圓角可以直接套用至控制項。 不過，如果是 null，則圓角必須套用至 `Container` 物件。 此 `effectTarget` 欄位可讓效果套用至適當的物件。

## <a name="implement-the-ios-effect"></a>執行 iOS 效果

IOS 平臺專案 `RoundEffect` 會定義衍生自的類別 `PlatformEffect` 。 這個類別會以 `assembly` 允許解析效果類別的屬性來標記 Xamarin.Forms ：

```csharp
[assembly: ResolutionGroupName("Xamarin")]
[assembly: ExportEffect(typeof(RoundEffectDemo.iOS.RoundEffect), nameof(RoundEffectDemo.iOS.RoundEffect))]
namespace RoundEffectDemo.iOS
{
    public class RoundEffect : PlatformEffect
    {
        // ...
    }
```

在 iOS 上，控制項具有 `Layer` 屬性，其具有 `CornerRadius` 屬性。 `RoundEffect`IOS 上的類別實會計算適當的圓角半徑，並更新圖層的 `CornerRadius` 屬性：

```csharp
public class RoundEffect : PlatformEffect
{
    nfloat originalRadius;
    UIKit.UIView effectTarget;

    protected override void OnAttached()
    {
        try
        {
            effectTarget = Control ?? Container;
            originalRadius = effectTarget.Layer.CornerRadius;
            effectTarget.ClipsToBounds = true;
            effectTarget.Layer.CornerRadius = CalculateRadius();
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Failed to set corner radius: {ex.Message}");
        }
    }

    protected override void OnDetached()
    {
        if (effectTarget != null)
        {
            effectTarget.ClipsToBounds = false;
            if (effectTarget.Layer != null)
            {
                effectTarget.Layer.CornerRadius = originalRadius;
            }
        }
    }

    float CalculateRadius()
    {
        double width = (double)Element.GetValue(VisualElement.WidthRequestProperty);
        double height = (double)Element.GetValue(VisualElement.HeightRequestProperty);
        float minDimension = (float)Math.Min(height, width);
        float radius = minDimension / 2f;

        return radius;
    }
}
```

方法會根據的 `CalculateRadius` 最小維度來計算 radius Xamarin.Forms `Element` 。 `OnAttached`當效果附加至控制項時，會呼叫方法，並更新圖層的 `CornerRadius` 屬性。 它會將 `ClipToBounds` 屬性設定為，讓溢位專案 `true` 裁剪至控制項的框線。 `OnDetatched`從控制項中移除效果並反轉這些變更時，會呼叫方法，以還原原始的圓角半徑。

> [!NOTE]
> 視元素類型而定， `Control` 屬性不一定是 null。 如果 `Control` 屬性不是 null，則圓角可以直接套用至控制項。 不過，如果是 null，則圓角必須套用至 `Container` 物件。 此 `effectTarget` 欄位可讓效果套用至適當的物件。

## <a name="consume-the-effect"></a>使用效果

一旦效果跨平臺執行，控制項就可以使用它 Xamarin.Forms 。 的常見應用 `RoundEffect` 是讓 `Image` 物件迴圈。 下列 XAML 會顯示套用至實例的效果 `Image` ：

```xaml
<Image Source=outdoors"
       HeightRequest="100"
       WidthRequest="100">
    <Image.Effects>
        <local:RoundEffect />
    </Image.Effects>
</Image>
```

效果也可以套用在程式碼中：

```csharp
var image = new Image
{
    Source = ImageSource.FromFile("outdoors"),
    HeightRequest = 100,
    WidthRequest = 100
};
image.Effects.Add(new RoundEffect());
```

`RoundEffect`類別可以套用至任何衍生自的控制項 `VisualElement` 。

> [!NOTE]
> 為了讓效果計算正確的半徑，其套用的控制項必須有明確的大小。 因此， `HeightRequest` `WidthRequest` 應該會定義和屬性。 如果受影響的控制項出現在中 `StackLayout` ，則其 `HorizontalOptions` 屬性不應該使用其中一個**展開**值，例如或， `LayoutOptions.CenterAndExpand` 它將不會有正確的維度。

## <a name="related-links"></a>相關連結

- [RoundEffect 範例應用程式](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-roundeffect/)
- [效果簡介](~/xamarin-forms/app-fundamentals/effects/introduction.md)
- [建立效果](~/xamarin-forms/app-fundamentals/effects/creating.md)
