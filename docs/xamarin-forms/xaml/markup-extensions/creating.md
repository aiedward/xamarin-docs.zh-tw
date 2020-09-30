---
title: 建立 XAML 標記延伸
description: 本文說明如何定義您自己的自訂 Xamarin.Forms XAML 標記延伸。 XAML 標記延伸是實 IMarkupExtension 或 IMarkupExtension 介面的類別 <T> 。
ms.prod: xamarin
ms.assetid: 797C1EF9-1C8E-4208-8610-9B79CCF17D46
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 43c8cd0dd7b50e3a5bfbd15d9858bd4502fedacc
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91558774"
---
# <a name="creating-xaml-markup-extensions"></a>建立 XAML 標記延伸

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)

在程式設計層級上，XAML 標記延伸是實作為 [`IMarkupExtension`](xref:Xamarin.Forms.Xaml.IMarkupExtension) 或介面的類別 [`IMarkupExtension<T>`](xref:Xamarin.Forms.Xaml.IMarkupExtension`1) 。 您可以在 GitHub 存放庫的[ **MarkupExtensions**目錄](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Xaml/MarkupExtensions)中，流覽以下所述標準標記延伸的原始程式碼 Xamarin.Forms 。

您也可以從或衍生自訂您自己的自訂 XAML 標記 `IMarkupExtension` 延伸 `IMarkupExtension<T>` 。 如果標記延伸取得特定類型的值，請使用一般格式。 這是數個標記延伸的情況 Xamarin.Forms ：

- `TypeExtension` 來自 `IMarkupExtension<Type>`
- `ArrayExtension` 來自 `IMarkupExtension<Array>`
- `DynamicResourceExtension` 來自 `IMarkupExtension<DynamicResource>`
- `BindingExtension` 來自 `IMarkupExtension<BindingBase>`
- `ConstraintExpression` 來自 `IMarkupExtension<Constraint>`

這兩個 `IMarkupExtension` 介面只會定義一個名為的方法 `ProvideValue` ：

```csharp
public interface IMarkupExtension
{
    object ProvideValue(IServiceProvider serviceProvider);
}

public interface IMarkupExtension<out T> : IMarkupExtension
{
    new T ProvideValue(IServiceProvider serviceProvider);
}
```

因為 `IMarkupExtension<T>` 衍生自 `IMarkupExtension` 並且 `new` 在上包含關鍵字 `ProvideValue` ，所以它包含這兩種 `ProvideValue` 方法。

XAML 標記延伸通常會定義有助於傳回值的屬性。  (明顯的例外狀況是 `NullExtension` ，只會傳回 `ProvideValue` `null` 。 ) `ProvideValue` 方法具有類型的單一引數 `IServiceProvider` ，將在本文稍後討論。

## <a name="a-markup-extension-for-specifying-color"></a>用來指定色彩的標記延伸

下列 XAML 標記延伸可讓您 `Color` 使用色調、飽和度和亮度元件來建立值。 它會針對色彩的四個元件定義四個屬性，包括初始化為1的 Alpha 元件。 類別衍生自 `IMarkupExtension<Color>` 以表示傳回 `Color` 值：

```csharp
public class HslColorExtension : IMarkupExtension<Color>
{
    public double H { set; get; }

    public double S { set; get; }

    public double L { set; get; }

    public double A { set; get; } = 1.0;

    public Color ProvideValue(IServiceProvider serviceProvider)
    {
        return Color.FromHsla(H, S, L, A);
    }

    object IMarkupExtension.ProvideValue(IServiceProvider serviceProvider)
    {
        return (this as IMarkupExtension<Color>).ProvideValue(serviceProvider);
    }
}
```

因為 `IMarkupExtension<T>` 衍生自 `IMarkupExtension` ，所以類別必須包含兩個 `ProvideValue` 方法，一個會傳回， `Color` 另一個 `object` 則會傳回，但是第二個方法可以直接呼叫第一個方法。

**HSL 色彩示範**頁面會顯示 XAML 檔案中可顯示的各種方式， `HslColorExtension` 以指定的色彩 `BoxView` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:MarkupExtensions"
             x:Class="MarkupExtensions.HslColorDemoPage"
             Title="HSL Color Demo">

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="BoxView">
                <Setter Property="WidthRequest" Value="80" />
                <Setter Property="HeightRequest" Value="80" />
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout>
        <BoxView>
            <BoxView.Color>
                <local:HslColorExtension H="0" S="1" L="0.5" A="1" />
            </BoxView.Color>
        </BoxView>

        <BoxView>
            <BoxView.Color>
                <local:HslColor H="0.33" S="1" L="0.5" />
            </BoxView.Color>
        </BoxView>

        <BoxView Color="{local:HslColorExtension H=0.67, S=1, L=0.5}" />

        <BoxView Color="{local:HslColor H=0, S=0, L=0.5}" />

        <BoxView Color="{local:HslColor A=0.5}" />
    </StackLayout>
</ContentPage>
```

請注意，當 `HslColorExtension` 是 XML 標記時，四個屬性會設定為屬性，但在大括弧之間出現時，四個屬性會以逗號分隔，而不加上引號。 、和的預設值為 `H` `S` `L` 0，而的預設值 `A` 為1，所以如果您想要將這些屬性設定為預設值，就可以省略這些屬性。 最後一個範例顯示亮度為0的範例，這通常會產生黑色，但是 Alpha 色板是0.5，因此它是透明的，而且會針對頁面的白色背景顯示為灰色：

[![HSL 色彩示範](creating-images/hslcolordemo-small.png "HSL 色彩示範")](creating-images/hslcolordemo-large.png#lightbox "HSL 色彩示範")

## <a name="a-markup-extension-for-accessing-bitmaps"></a>用來存取點陣圖的標記延伸

的引數 `ProvideValue` 是一個物件，該物件 [`IServiceProvider`](xref:System.IServiceProvider) 會執行 .net 命名空間中定義的介面 `System` 。 此介面有一個成員，這是一個 `GetService` 以引數命名的方法 `Type` 。

`ImageResourceExtension`下面顯示的類別會顯示一個可能使用的 `IServiceProvider` ，並 `GetService` 取得 `IXmlLineInfoProvider` 可提供行和字元資訊的物件，以指出偵測到特定錯誤的位置。 在此情況下，如果尚未設定屬性，就會引發例外狀況 `Source` ：

```csharp
[ContentProperty("Source")]
class ImageResourceExtension : IMarkupExtension<ImageSource>
{
    public string Source { set; get; }

    public ImageSource ProvideValue(IServiceProvider serviceProvider)
    {
        if (String.IsNullOrEmpty(Source))
        {
            IXmlLineInfoProvider lineInfoProvider = serviceProvider.GetService(typeof(IXmlLineInfoProvider)) as IXmlLineInfoProvider;
            IXmlLineInfo lineInfo = (lineInfoProvider != null) ? lineInfoProvider.XmlLineInfo : new XmlLineInfo();
            throw new XamlParseException("ImageResourceExtension requires Source property to be set", lineInfo);
        }

        string assemblyName = GetType().GetTypeInfo().Assembly.GetName().Name;
        return ImageSource.FromResource(assemblyName + "." + Source, typeof(ImageResourceExtension).GetTypeInfo().Assembly);
    }

    object IMarkupExtension.ProvideValue(IServiceProvider serviceProvider)
    {
        return (this as IMarkupExtension<ImageSource>).ProvideValue(serviceProvider);
    }
}
```

`ImageResourceExtension` 當 XAML 檔案需要存取儲存為 .NET Standard 程式庫專案中內嵌資源的影像檔時，會很有説明。 它會使用 `Source` 屬性來呼叫靜態 `ImageSource.FromResource` 方法。 這個方法需要完整的資源名稱，其中包含元件名稱、資料夾名稱，以及以句號分隔的檔案名。 方法的第二個引數 `ImageSource.FromResource` 會提供元件名稱，而且只有在 UWP 上的發行組建才需要。 無論何時，都 `ImageSource.FromResource` 必須從包含點陣圖的元件呼叫，這表示此 XAML 資源延伸模組不能是外部程式庫的一部分，除非影像也在該程式庫中。 如需存取儲存為內嵌資源之點陣圖的詳細資訊， (參閱 [**內嵌影像**](~/xamarin-forms/user-interface/images.md#embedded-images) 文章。 ) 

雖然 `ImageResourceExtension` 需要 `Source` 設定屬性，但 `Source` 屬性會在屬性中指定為類別的 content 屬性。 這表示 `Source=` 可以省略以大括弧括住的運算式部分。 在 [ **影像資源示範** ] 頁面中，專案會 `Image` 使用資料夾名稱和檔案名（以句號分隔）提取兩個影像：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:MarkupExtensions"
             x:Class="MarkupExtensions.ImageResourceDemoPage"
             Title="Image Resource Demo">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Image Source="{local:ImageResource Images.SeatedMonkey.jpg}"
               Grid.Row="0" />

        <Image Source="{local:ImageResource Images.FacePalm.jpg}"
               Grid.Row="1" />

    </Grid>
</ContentPage>
```

以下是程式執行情況：

[![影像資源示範](creating-images/imageresourcedemo-small.png "影像資源示範")](creating-images/imageresourcedemo-large.png#lightbox "影像資源示範")

## <a name="service-providers"></a>服務提供者

藉由使用的 `IServiceProvider` 引數 `ProvideValue` ，xaml 標記延伸可以存取其所使用之 xaml 檔案的實用資訊。 但是若要 `IServiceProvider` 成功使用引數，您必須知道特定內容中有何種服務可用。 若要瞭解這項功能，最佳方式是在 GitHub 上的儲存機制的[ **MarkupExtensions**資料夾](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Xaml/MarkupExtensions)中，研究現有 XAML 標記延伸的原始程式碼 Xamarin.Forms 。 請注意，有些類型的服務是內部的 Xamarin.Forms 。

在某些 XAML 標記延伸中，這項服務可能很有用：

```csharp
 IProvideValueTarget provideValueTarget = serviceProvider.GetService(typeof(IProvideValueTarget)) as IProvideValueTarget;
```

`IProvideValueTarget`介面會定義兩個屬性： `TargetObject` 和 `TargetProperty` 。 在類別中取得這項資訊時 `ImageResourceExtension` ， `TargetObject` 是， `Image` 而且 `TargetProperty` 是 `BindableProperty` `Source` 屬性的物件 `Image` 。 這是已設定 XAML 標記延伸的屬性。

`GetService`具有引數的呼叫會實際傳回型別為的 `typeof(IProvideValueTarget)` 物件，這個物件 `SimpleValueTargetProvider` 是在 `Xamarin.Forms.Xaml.Internals` 命名空間中定義的。 如果您將的傳回值轉換 `GetService` 為該型別，您也可以存取 `ParentObjects` 屬性（property），這個屬性（property）是包含專案的陣列 `Image` 、 `Grid` 父系，以及的 `ImageResourceDemoPage` 父系 `Grid` 。

## <a name="conclusion"></a>結論

XAML 標記延伸藉由擴充可從各種來源設定屬性的功能，在 XAML 扮演重要角色。 此外，如果現有的 XAML 標記延伸未提供您所需的內容，您也可以自行撰寫。

## <a name="related-links"></a>相關連結

- [標記延伸 (範例) ](/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)
- [書籍中的 XAML 標記延伸章節 Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)