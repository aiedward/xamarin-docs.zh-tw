---
title: 建立 Xamarin.Forms 視覺效果轉譯器
description: 建立 Xamarin.Forms 選擇性地套用至 VisualElement 物件，而不需要子類別 Xamarin.Forms 檢視的視覺效果。
ms.prod: xamarin
ms.assetid: 80BF9C72-AC28-4AAF-9DDD-B60CBDD1CD59
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/12/2019
ms.openlocfilehash: a11c2045fa6119d0689834c35794bc8913c80bd6
ms.sourcegitcommit: a7170494e1975f0f1be547a45444752fd8e57819
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2019
ms.locfileid: "58506963"
---
# <a name="create-a-xamarinforms-visual-renderer"></a>建立 Xamarin.Forms 視覺效果轉譯器

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VisualDemos/)

Xamarin.Forms 的視覺效果可讓系統建立，並選擇性地套用到的轉譯器[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)物件，而不需要子類別 Xamarin.Forms 檢視。 指定的轉譯器`IVisual`一部分的型別，其`ExportRendererAttribute`，將會用來呈現檢視，而不是預設的轉譯器中選擇。 在轉譯器選取項目時，`Visual`屬性檢視的檢查，且包含在轉譯器選取項目程序。

> [!IMPORTANT]
> 目前[ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual)呈現的檢視，但這會在未來版本中變更之後，就無法變更屬性。

建立及使用 Xamarin.Forms 視覺效果轉譯器的程序是：

1. 建立所需的檢視的平台轉譯器。 如需詳細資訊，請參閱 <<c0> [ 建立轉譯器](#create-platform-renderers)。
1. 建立衍生自類型`IVisual`。 如需詳細資訊，請參閱 <<c0> [ 建立 IVisual 類型](#create-an-ivisual-type)。
1. 註冊`IVisual`一部分的型別`ExportRendererAttribute`裝飾的轉譯器。 如需詳細資訊，請參閱 <<c0> [ 註冊 IVisual 類型](#register-the-ivisual-type)。
1. 取用 Visual 轉譯器設定[ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual)若要在檢視上的屬性`IVisual`名稱。 如需詳細資訊，請參閱 <<c0> [ 取用 Visual 轉譯器](#consume-the-visual-renderer)。
1. [選用]註冊的名稱`IVisual`型別。 如需詳細資訊，請參閱 <<c0> [ 註冊 IVisual 類型的名稱](#register-a-name-for-the-ivisual-type)。

## <a name="create-platform-renderers"></a>建立平台的轉譯器

如需建立轉譯器類別的詳細資訊，請參閱[自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)。 不過請注意 Xamarin.Forms 視覺效果轉譯器套用至檢視，而不需要子類別的檢視。

此處概述的轉譯器類別會實作自訂[ `Button` ](xref:Xamarin.Forms.Button)會顯示其含陰影的文字。

### <a name="ios"></a>iOS

下列程式碼範例顯示適用於 iOS 的按鈕轉譯器：

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

下列程式碼範例顯示適用於 Android 的按鈕轉譯器：

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

在您跨平台程式庫中，建立衍生自類型`IVisual`:

```csharp
public class CustomVisual : IVisual
{
}
```

`CustomVisual`型別可以再向轉譯器類別，允許[ `Button` ](xref:Xamarin.Forms.Button)選擇使用轉譯器的物件。

## <a name="register-the-ivisual-type"></a>註冊 IVisual 類型

在平台專案中，裝飾的轉譯器類別`ExportRendererAttribute`:

```csharp
[assembly: ExportRenderer(typeof(Xamarin.Forms.Button), typeof(CustomButtonRenderer), new[] { typeof(CustomVisual) })]
public class CustomButtonRenderer : ButtonRenderer
{
    protected override void OnElementChanged(ElementChangedEventArgs<Button> e)
    {
        ...
    }
}
```

在此範例中，`ExportRendererAttribute`指定`CustomButtonRenderer`類別將用來呈現耗用[ `Button` ](xref:Xamarin.Forms.Button)物件，與`IVisual`類型註冊為第三個引數。 指定的轉譯器`IVisual`一部分的型別，其`ExportRendererAttribute`，將會用來呈現檢視，而不是預設的轉譯器中選擇。

## <a name="consume-the-visual-renderer"></a>取用 Visual 轉譯器

A [ `Button` ](xref:Xamarin.Forms.Button)物件可以選擇使用轉譯器類別，藉由設定其[ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual)屬性設`Custom`:

```xaml
<Button Visual="Custom"
        Text="CUSTOM BUTTON"
        BackgroundColor="{StaticResource PrimaryColor}"
        TextColor="{StaticResource SecondaryTextColor}"
        HorizontalOptions="FillAndExpand" />
```

> [!NOTE]
> 在 XAML 中，型別轉換子不需要包含在"Visual"後的置詞[ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual)屬性值。 不過，也可以指定完整型別名稱。

對等的 C# 程式碼是：

```csharp
Button button = new Button { Text = "CUSTOM BUTTON", ... };
button.Visual = new CustomVisual();
```

在轉譯器選取項目時， [ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual)屬性[ `Button` ](xref:Xamarin.Forms.Button)是檢查，而且包含在轉譯器選取程序。 如果轉譯器不位於項目，則將使用 Xamarin.Forms 預設轉譯器。

下列螢幕擷取畫面顯示轉譯[ `Button` ](xref:Xamarin.Forms.Button)，其中顯示其含陰影的文字：

[![使用陰影的文字、 iOS 和 Android 上的自訂按鈕的螢幕擷取畫面](material-visual-images/custom-button.png "陰影的文字與按鈕")](material-visual-images/custom-button-large.png#lightbox)

## <a name="register-a-name-for-the-ivisual-type"></a>註冊 IVisual 類型的名稱

[ `VisualAttribute` ](xref:Xamarin.Forms.VisualAttribute)可用來選擇性地註冊不同的名稱`IVisual`型別。 這種方法可以用來解決命名衝突之間不同的視覺化程式庫，或在您只想要視覺效果，請參閱的情況下，由其型別名稱不同的名稱。

[ `VisualAttribute` ](xref:Xamarin.Forms.VisualAttribute)應該在任一個的跨平台程式庫或平台專案中的組件層級定義：

```csharp
[assembly: Visual("MyVisual", typeof(CustomVisual))]
```

`IVisual`類型可以使用透過已註冊的名稱：

```xaml
<Button Visual="MyVisual"
        ... />
```

> [!NOTE]
> 取用時的視覺效果，透過已註冊的名稱，則必須包含任何"Visual"後置詞。

## <a name="related-links"></a>相關連結

- [材質的視覺效果 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VisualDemos/)
- [Xamarin.Forms 資料視覺效果](material-visual.md)
- [自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
