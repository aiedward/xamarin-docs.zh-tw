---
標題：「建立視覺化轉譯器」 Xamarin.Forms 描述：「建立 Xamarin.Forms 可選擇性套用至 VisualElement 物件的視覺效果，而不需要子類別化 Xamarin.Forms 。」
assetid： 80BF9C72-AC28-4AAF-9DDD-B60CBDD1CD59 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：03/12/2019 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="create-a-xamarinforms-visual-renderer"></a>建立 Xamarin.Forms 視覺效果轉譯器

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)

Xamarin.Forms視覺效果可讓轉譯器建立並選擇性地套用至 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 物件，而不需要子類別化的 Xamarin.Forms 視圖。 指定 `IVisual` 類型做為其一部分的轉譯器 `ExportRendererAttribute` 將用來轉譯加入宣告的視圖，而不是預設轉譯器。 在轉譯器選取時間， `Visual` 會檢查視圖的屬性，並將其包含在轉譯器選取進程中。

> [!IMPORTANT]
> 目前 [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) 無法在呈現視圖之後變更屬性，但未來的版本將會變更。

建立和使用視覺效果轉譯器的 Xamarin.Forms 程式如下：

1. 建立必要視圖的平臺轉譯器。 如需詳細資訊，請參閱[建立](#create-platform-renderers)轉譯器。
1. 建立衍生自的類型 `IVisual` 。 如需詳細資訊，請參閱[建立 IVisual 類型](#create-an-ivisual-type)。
1. `IVisual`將類型註冊為裝飾轉譯器之的一部分 `ExportRendererAttribute` 。 如需詳細資訊，請參閱[註冊 IVisual 類型](#register-the-ivisual-type)。
1. 將 [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) 視圖上的屬性設定為名稱，以取用視覺效果轉譯器 `IVisual` 。 如需詳細資訊，請參閱[使用視覺效果](#consume-the-visual-renderer)轉譯器。
1. 選擇性註冊類型的名稱 `IVisual` 。 如需詳細資訊，請參閱[註冊 IVisual 類型的名稱](#register-a-name-for-the-ivisual-type)。

## <a name="create-platform-renderers"></a>建立平台轉譯器

如需建立轉譯器類別的詳細資訊，請參閱[自訂](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)轉譯器。 不過，請注意， Xamarin.Forms 視覺效果轉譯器會套用至視圖，而不需要將此視圖設為子類別。

這裡所述的轉譯器類別會執行自訂 [`Button`](xref:Xamarin.Forms.Button) ，它會以陰影顯示其文字。

### <a name="ios"></a>iOS

下列程式碼範例顯示 iOS 的按鈕轉譯器：

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

下列程式碼範例顯示適用于 Android 的按鈕轉譯器：

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

在跨平臺程式庫中，建立一個衍生自的類型 `IVisual` ：

```csharp
public class CustomVisual : IVisual
{
}
```

`CustomVisual`然後，可以針對轉譯器類別註冊類型，允許 [`Button`](xref:Xamarin.Forms.Button) 物件使用轉譯器加入宣告。

## <a name="register-the-ivisual-type"></a>註冊 IVisual 類型

在平臺專案中，于 `ExportRendererAttribute` 元件層級新增：

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

在此 iOS 平臺專案的範例中， `ExportRendererAttribute` `CustomButtonRenderer` 會指定將使用類別來轉譯使用 [`Button`](xref:Xamarin.Forms.Button) 中的物件，並將 `IVisual` 類型註冊為第三個引數。 指定 `IVisual` 類型做為其一部分的轉譯器 `ExportRendererAttribute` 將用來轉譯加入宣告的視圖，而不是預設轉譯器。

## <a name="consume-the-visual-renderer"></a>使用視覺效果轉譯器

[`Button`](xref:Xamarin.Forms.Button)物件可以藉由將其屬性設為，選擇使用轉譯器類別 [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) `Custom` ：

```xaml
<Button Visual="Custom"
        Text="CUSTOM BUTTON"
        BackgroundColor="{StaticResource PrimaryColor}"
        TextColor="{StaticResource SecondaryTextColor}"
        HorizontalOptions="FillAndExpand" />
```

> [!NOTE]
> 在 XAML 中，類型轉換器不需要在屬性值中包含 "Visual" 尾碼 [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) 。 不過，您也可以指定完整的類型名稱。

對等的 C# 程式碼為：

```csharp
Button button = new Button { Text = "CUSTOM BUTTON", ... };
button.Visual = new CustomVisual();
```

在轉譯器選取時間， [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) 會檢查的屬性，並將其 [`Button`](xref:Xamarin.Forms.Button) 包含在轉譯器選取進程中。 如果找不到轉譯器，則 Xamarin.Forms 會使用預設轉譯器。

下列螢幕擷取畫面顯示呈現的 [`Button`](xref:Xamarin.Forms.Button) ，它會以陰影顯示其文字：

[![在 iOS 和 Android 上具有陰影文字之自訂按鈕的螢幕擷取畫面](material-visual-images/custom-button.png "具有陰影文字的按鈕")](material-visual-images/custom-button-large.png#lightbox)

## <a name="register-a-name-for-the-ivisual-type"></a>註冊 IVisual 類型的名稱

[`VisualAttribute`](xref:Xamarin.Forms.VisualAttribute)可以用來選擇性地為類型註冊不同的名稱 `IVisual` 。 這種方法可用來解決不同視覺程式庫之間的命名衝突，或在您只想要以不同于其類型名稱的名稱來參考視覺效果的情況下使用。

[`VisualAttribute`](xref:Xamarin.Forms.VisualAttribute)應定義于跨平臺程式庫或平臺專案中的元件層級：

```csharp
[assembly: Visual("MyVisual", typeof(CustomVisual))]
```

`IVisual`然後，可以透過其註冊的名稱取用類型：

```xaml
<Button Visual="MyVisual"
        ... />
```

> [!NOTE]
> 透過其註冊的名稱來取用視覺效果時，必須包含任何 "Visual" 尾碼。

## <a name="related-links"></a>相關連結

- [材質視覺效果（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)
- [Xamarin.Forms材質視覺效果](material-visual.md)
- [自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
