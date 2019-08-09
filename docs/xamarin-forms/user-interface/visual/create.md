---
title: 建立 Xamarin. Forms 視覺效果轉譯器
description: 建立可選擇性套用至 VisualElement 物件的 Xamarin 視覺效果, 而不需要將 Xamarin. 表單檢視設為子類別。
ms.prod: xamarin
ms.assetid: 80BF9C72-AC28-4AAF-9DDD-B60CBDD1CD59
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/12/2019
ms.openlocfilehash: 8173ebcc174df6e34a53f226a43083bd28941031
ms.sourcegitcommit: 2e5a6b8bcd1a073b54604f51538fd108e1c2a8e5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/09/2019
ms.locfileid: "68869382"
---
# <a name="create-a-xamarinforms-visual-renderer"></a>建立 Xamarin. Forms 視覺效果轉譯器

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)

[Xamarin] 視覺效果可讓轉譯器建立並選擇性地[`VisualElement`](xref:Xamarin.Forms.VisualElement)套用至物件, 而不需要將 Xamarin. 表單檢視設為子類別。 指定`IVisual`類型做為其`ExportRendererAttribute`一部分的轉譯器將用來轉譯加入宣告的視圖, 而不是預設轉譯器。 在轉譯器選取項目時，`Visual`屬性檢視的檢查，且包含在轉譯器選取項目程序。

> [!IMPORTANT]
> 目前無法在呈現視圖之後變更[屬性,但未來的版本將會變更。`Visual`](xref:Xamarin.Forms.VisualElement.Visual)

建立和使用 Xamarin 的程式如下:

1. 建立必要視圖的平臺轉譯器。 如需詳細資訊, 請參閱[建立](#create-platform-renderers)轉譯器。
1. 建立衍生自`IVisual`的類型。 如需詳細資訊, 請參閱[建立 IVisual 類型](#create-an-ivisual-type)。
1. `IVisual` 將`ExportRendererAttribute`類型註冊為裝飾轉譯器之的一部分。 如需詳細資訊, 請參閱[註冊 IVisual 類型](#register-the-ivisual-type)。
1. 將視圖上的[`Visual`](xref:Xamarin.Forms.VisualElement.Visual)屬性設定`IVisual`為名稱, 以取用視覺效果轉譯器。 如需詳細資訊, 請參閱[使用視覺效果](#consume-the-visual-renderer)轉譯器。
1. 選擇性註冊`IVisual`類型的名稱。 如需詳細資訊, 請參閱[註冊 IVisual 類型的名稱](#register-a-name-for-the-ivisual-type)。

## <a name="create-platform-renderers"></a>建立平臺轉譯器

如需建立轉譯器類別的詳細資訊, 請參閱[自訂](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)轉譯器。 不過, 請注意, [Xamarin] 視覺效果轉譯器會套用至視圖, 而不需要將此視圖設為子類別。

這裡所述的轉譯器類別會[`Button`](xref:Xamarin.Forms.Button)執行自訂, 它會以陰影顯示其文字。

### <a name="ios"></a>iOS

下列程式碼範例顯示 iOS 的按鈕轉譯器:

```csharp
public class CustomButtonRenderer : ButtonRenderer
{
    protected override void OnElementChanged(ElementChangedEventArgs<Button> e)
    {
        base.OnElementChanged(e);

        if (e.OldElement != null)
        {
            // Cleanup
        }

        if (e.NewElement != null)
        {
            Control.TitleShadowOffset = new CoreGraphics.CGSize(1, 1);
            Control.SetTitleShadowColor(Color.Black.ToUIColor(), UIKit.UIControlState.Normal);
        }
    }
}
```

### <a name="android"></a>Android

下列程式碼範例顯示適用于 Android 的按鈕轉譯器:

```csharp
public class CustomButtonRenderer : Xamarin.Forms.Platform.Android.AppCompat.ButtonRenderer
{
    public CustomButtonRenderer(Context context) : base(context)
    {
    }

    protected override void OnElementChanged(ElementChangedEventArgs<Button> e)
    {
        base.OnElementChanged(e);

        if (e.OldElement != null)
        {
            // Cleanup
        }

        if (e.NewElement != null)
        {
            Control.SetShadowLayer(5, 3, 3, Color.Black.ToAndroid());
        }
    }
}
```

## <a name="create-an-ivisual-type"></a>建立 IVisual 類型

在跨平臺程式庫中, 建立一個衍生自`IVisual`的類型:

```csharp
public class CustomVisual : IVisual
{
}
```

然後, 可以針對轉譯器類別註冊[`Button`](xref:Xamarin.Forms.Button) 類型,允許物件使用轉譯器加入宣告。`CustomVisual`

## <a name="register-the-ivisual-type"></a>註冊 IVisual 類型

在平臺專案中, 使用`ExportRendererAttribute`來裝飾轉譯器命名空間:

```csharp
[assembly: ExportRenderer(typeof(Xamarin.Forms.Button), typeof(CustomButtonRenderer), new[] { typeof(CustomVisual) })]
namespace VisualDemos.iOS
{
    public class CustomButtonRenderer : ButtonRenderer
    {
        protected override void OnElementChanged(ElementChangedEventArgs<Button> e)
        {
            // ...
        }
    }
}
```

在此 iOS 平臺專案的`ExportRendererAttribute`範例中, 會指定`CustomButtonRenderer`將使用類別來[`Button`](xref:Xamarin.Forms.Button)轉譯使用中的物件, 並將`IVisual`類型註冊為第三個引數。 指定`IVisual`類型做為其`ExportRendererAttribute`一部分的轉譯器將用來轉譯加入宣告的視圖, 而不是預設轉譯器。

## <a name="consume-the-visual-renderer"></a>使用視覺效果轉譯器

物件可以藉由將其[`Visual`](xref:Xamarin.Forms.VisualElement.Visual)屬性設為, `Custom`選擇使用轉譯器類別: [`Button`](xref:Xamarin.Forms.Button)

```xaml
<Button Visual="Custom"
        Text="CUSTOM BUTTON"
        BackgroundColor="{StaticResource PrimaryColor}"
        TextColor="{StaticResource SecondaryTextColor}"
        HorizontalOptions="FillAndExpand" />
```

> [!NOTE]
> 在 XAML 中, 類型轉換器不需要在[`Visual`](xref:Xamarin.Forms.VisualElement.Visual)屬性值中包含 "Visual" 尾碼。 不過, 您也可以指定完整的類型名稱。

對等的 C# 程式碼是：

```csharp
Button button = new Button { Text = "CUSTOM BUTTON", ... };
button.Visual = new CustomVisual();
```

在轉譯器選取時間, [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) [`Button`](xref:Xamarin.Forms.Button)會檢查的屬性, 並將其包含在轉譯器選取進程中。 如果找不到轉譯器, 則會使用 [Xamarin] 預設轉譯器。

下列螢幕擷取畫面顯示呈現[`Button`](xref:Xamarin.Forms.Button)的, 它會以陰影顯示其文字:

[![在 iOS 和 Android 上具有陰影文字之自訂按鈕的螢幕擷取畫面](material-visual-images/custom-button.png "具有陰影文字的按鈕")](material-visual-images/custom-button-large.png#lightbox)

## <a name="register-a-name-for-the-ivisual-type"></a>註冊 IVisual 類型的名稱

可以用來選擇性地為`IVisual`類型註冊不同的名稱。 [`VisualAttribute`](xref:Xamarin.Forms.VisualAttribute) 這種方法可用來解決不同視覺程式庫之間的命名衝突, 或在您只想要以不同于其類型名稱的名稱來參考視覺效果的情況下使用。

[`VisualAttribute`](xref:Xamarin.Forms.VisualAttribute)應定義于跨平臺程式庫或平臺專案中的元件層級:

```csharp
[assembly: Visual("MyVisual", typeof(CustomVisual))]
```

然後`IVisual` , 可以透過其註冊的名稱取用類型:

```xaml
<Button Visual="MyVisual"
        ... />
```

> [!NOTE]
> 透過其註冊的名稱來取用視覺效果時, 必須包含任何 "Visual" 尾碼。

## <a name="related-links"></a>相關連結

- [材質視覺效果 (範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)
- [Xamarin. 表單材質視覺效果](material-visual.md)
- [自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
