---
title: Xamarin. 表單可重複使用的 RoundEffect
description: RoundEffect 是可重複使用的效果，可以套用至衍生自 VisualElement 的任何控制項，以將控制項轉譯為圓形。
ms.prod: xamarin
ms.assetid: B5DE7507-B565-4EE5-9897-27E5733FD173
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 10/25/2019
ms.openlocfilehash: 851ed7a2ad1c416b4d03d583b9d0aeb7f7774eea
ms.sourcegitcommit: aa7e9d2c370ba9cbc830f10b94b4cd4221fc5467
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73055957"
---
# <a name="xamarinforms-reusable-roundeffect"></a>Xamarin. 表單可重複使用的 RoundEffect

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-roundeffect/)

RoundEffect 會將衍生自 VisualElement 的任何控制項簡化為圓形。 這種效果可以用來建立圓形影像、按鈕和其他控制項：

[![RoundEffect iOS 和 Android 上的螢幕擷取畫面](example-roundeffect-images/round-effect-cropped.png)](example-roundeffect-images/round-effect.png#lightbox)

## <a name="create-a-shared-routingeffect"></a>建立共用 RoutingEffect

必須在共用的專案中建立效果類別，以建立跨平臺效果。 範例應用程式會建立一個衍生自 `RoutingEffect` 類別的空 `RoundEffect` 類別：

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

Android 平臺專案會定義衍生自 `PlatformEffect`的 `RoundEffect` 類別。 這個類別會標記 `assembly` 屬性，以允許 Xamarin 解析效果類別：

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

Android 平臺會使用 `OutlineProvider` 的概念來定義控制項的邊緣。 範例專案包含衍生自 `ViewOutlineProvider` 類別的 `CornerRadiusProvider` 類別：

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

這個類別會使用 Xamarin `Element` 實例的 `Width` 和 `Height` 屬性，來計算半個最短維度的半徑。

定義大綱提供者之後，`RoundEffect` 類別就可以取用它來執行效果：

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

當效果附加至元素時，會呼叫 `OnAttached` 方法。 會儲存現有的 `OutlineProvider` 物件，以便在中斷連結效果時可以還原。 `CornerRadiusOutlineProvider` 的新實例做為 `OutlineProvider`，`ClipToOutline` 設定為 true，以將溢位的元素裁剪至大綱框線。

從專案中移除效果並還原原始的 `OutlineProvider` 值時，會呼叫 `OnDetatched` 方法。

> [!NOTE]
> 視元素類型而定，`Control` 屬性不一定是 null。 如果 `Control` 屬性不是 null，則圓角可以直接套用至控制項。 不過，如果是 null，則圓角必須套用至 `Container` 物件。 [`effectTarget`] 欄位可讓效果套用至適當的物件。

## <a name="implement-the-ios-effect"></a>執行 iOS 效果

IOS 平臺專案會定義衍生自 `PlatformEffect`的 `RoundEffect` 類別。 這個類別會標記 `assembly` 屬性，以允許 Xamarin 解析效果類別：

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

在 iOS 上，控制項具有 `Layer` 屬性，其具有 `CornerRadius` 屬性。 IOS 上的 `RoundEffect` 類別執行會計算適當的圓角半徑，並更新圖層的 `CornerRadius` 屬性：

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

`CalculateRadius` 方法會根據 Xamarin. 表單 `Element`的最小維度來計算 radius。 當效果附加至控制項時，會呼叫 `OnAttached` 方法，並更新圖層的 `CornerRadius` 屬性。 它會將 `ClipToBounds` 屬性設定為 `true` 因此，溢位的元素會裁剪至控制項的框線。 從控制項中移除效果並反轉這些變更時，會呼叫 `OnDetatched` 方法，以還原原始的圓角半徑。

> [!NOTE]
> 視元素類型而定，`Control` 屬性不一定是 null。 如果 `Control` 屬性不是 null，則圓角可以直接套用至控制項。 不過，如果是 null，則圓角必須套用至 `Container` 物件。 [`effectTarget`] 欄位可讓效果套用至適當的物件。

## <a name="consume-the-effect"></a>使用效果

在整個平臺上執行效果後，Xamarin 就可以使用它。 `RoundEffect` 的常見應用就是讓 `Image` 物件迴圈。 下列 XAML 會顯示套用至 `Image` 實例的效果：

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

`RoundEffect` 類別可以套用至衍生自 `VisualElement`的任何控制項。

> [!NOTE]
> 為了讓效果計算正確的半徑，其套用的控制項必須有明確的大小。 因此，應該定義 `HeightRequest` 和 `WidthRequest` 屬性。 如果受影響的控制項出現在 `StackLayout`中，其 `HorizontalOptions` 屬性不應使用其中一個**展開**值，例如 `LayoutOptions.CenterAndExpand`，否則將不會有正確的維度。

## <a name="related-links"></a>相關連結

- [RoundEffect 範例應用程式](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-roundeffect/)
- [效果簡介](~/xamarin-forms/app-fundamentals/effects/introduction.md)
- [建立效果](~/xamarin-forms/app-fundamentals/effects/creating.md)
