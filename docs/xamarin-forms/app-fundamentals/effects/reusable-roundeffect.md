---
title: Xamarin.表單可重複的圓效應
description: RoundEffect 是一種可重用的效果,可應用於從 VisualElement 派生的任何控制項,以將控制件呈現為圓。
ms.prod: xamarin
ms.assetid: B5DE7507-B565-4EE5-9897-27E5733FD173
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 10/25/2019
ms.openlocfilehash: 851ed7a2ad1c416b4d03d583b9d0aeb7f7774eea
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73055957"
---
# <a name="xamarinforms-reusable-roundeffect"></a>Xamarin.表單可重複的圓效應

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-roundeffect/)

RoundEffect 簡化了從 VisualElement 派生為圓的任何控制項的渲染。 這個效果可用於建立圓形影像、按鈕和其他控制項:

[![iOS 和安卓上的圓形效果螢幕截圖](example-roundeffect-images/round-effect-cropped.png)](example-roundeffect-images/round-effect.png#lightbox)

## <a name="create-a-shared-routingeffect"></a>建立分享路由效果

必須在共用項目中創建效果類才能創建跨平臺效果。 範例應用程式建立派生自類`RoundEffect``RoutingEffect`的空類:

```csharp
public class RoundEffect : RoutingEffect
{
    public RoundEffect() : base($"Xamarin.{nameof(RoundEffect)}")
    {
    }
}
```

此類允許共用專案解析對代碼或 XAML 中效果的引用,但不提供任何功能。 效果必須具有每個平台的實現。

## <a name="implement-the-android-effect"></a>實現 Android 效果

Android 平台項目定義派生`RoundEffect``PlatformEffect`自 的類。 此使用`assembly`允許 Xamarin.Forms 解析效果類別屬性的標籤:

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

Android 平臺使用`OutlineProvider`的概 念來定義控制件的邊緣。 範例專案包括派生自`CornerRadiusProvider`類`ViewOutlineProvider`的 類別:

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

類使用 Xamarin.Forms`Width``Height``Element`實例的 和 屬性來計算半徑是最短維度的一半。

定義大綱提供者後,`RoundEffect`類別可以使用它來實現效果:

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

當`OnAttached`效果附加到元素時,將調用該方法。 將保存`OutlineProvider`現有物件,以便在分離效果時還原該物件。 的新實例`CornerRadiusOutlineProvider`用`OutlineProvider`作`ClipToOutline`和 設置為 true,以將溢出元素剪輯到大綱邊框。

當`OnDetatched`元素中刪除效果並還原原始`OutlineProvider`值時,將調用 該方法。

> [!NOTE]
> 根據元素類型,該屬性可能`Control`為空,也可能不為空。 如果`Control`屬性不為空,則可以將圓角直接應用於控制項。 但是,如果圓角為空,則必須將圓角應用於`Container`物件。 該`effectTarget`字段允許將效果應用於相應的物件。

## <a name="implement-the-ios-effect"></a>實作 iOS 效果

iOS 平臺項目定義派生`RoundEffect``PlatformEffect`自的類。 此使用`assembly`允許 Xamarin.Forms 解析效果類別屬性的標籤:

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

在 iOS`Layer`上, 控件具有`CornerRadius`一個屬性 ,該屬性具有屬性。 iOS`RoundEffect`上的 類別的樣數組`CornerRadius`數

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

該方法`CalculateRadius`根據 Xamarin.Forms`Element`的最小尺寸計算半徑。 當`OnAttached`效果附加到控制項時,將呼叫 該方法,並更新圖層`CornerRadius`的屬性 。 它將`ClipToBounds`屬性設定`true`為 ,以便溢出的元素被剪切到控制項的邊界。 當`OnDetatched`從控制項中刪除效果並反轉這些更改,恢復原始角半徑時,將調用該方法。

> [!NOTE]
> 根據元素類型,該屬性可能`Control`為空,也可能不為空。 如果`Control`屬性不為空,則可以將圓角直接應用於控制項。 但是,如果圓角為空,則必須將圓角應用於`Container`物件。 該`effectTarget`字段允許將效果應用於相應的物件。

## <a name="consume-the-effect"></a>消耗效果

一旦在平台中實現效果,Xamarin.Forms 控制件就可以使用它。 的常見應用`RoundEffect`是使對`Image`象成為圓形。 以下 XAML 顯示了`Image`應用於 實體的效果:

```xaml
<Image Source=outdoors"
       HeightRequest="100"
       WidthRequest="100">
    <Image.Effects>
        <local:RoundEffect />
    </Image.Effects>
</Image>
```

該效果也可以應用於代碼:

```csharp
var image = new Image
{
    Source = ImageSource.FromFile("outdoors"),
    HeightRequest = 100,
    WidthRequest = 100
};
image.Effects.Add(new RoundEffect());
```

該`RoundEffect`類可以應用於派生`VisualElement`自 的任何控件。

> [!NOTE]
> 要計算正確的半徑,它所應用到的控制項必須具有顯式大小調整。 因此,應`HeightRequest`定義`WidthRequest`和屬性。 如果受影響的控制器`StackLayout`出現在中,其`HorizontalOptions`屬性不應使用 **「展開」** 值之一`LayoutOptions.CenterAndExpand`,例如或它將沒有準確的維度。

## <a name="related-links"></a>相關連結

- [圓效應範例應用程式](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-roundeffect/)
- [效果簡介](~/xamarin-forms/app-fundamentals/effects/introduction.md)
- [建立效果](~/xamarin-forms/app-fundamentals/effects/creating.md)
