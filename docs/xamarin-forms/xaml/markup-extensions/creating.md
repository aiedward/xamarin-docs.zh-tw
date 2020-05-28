---
title: ''
description: 本文說明如何定義您自己的自訂 Xamarin.Forms XAML 標記延伸。 XAML 標記延伸是一種可執行 IMarkupExtension 或 IMarkupExtension 介面的類別 <T> 。
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 878ebcaa5249261afac2776a9e7cf47c0c047135
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84130529"
---
# <a name="creating-xaml-markup-extensions"></a>建立 XAML 標記延伸

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)

在程式設計層級上，XAML 標記延伸是一種可實作為 [`IMarkupExtension`](xref:Xamarin.Forms.Xaml.IMarkupExtension) 或介面的類別 [`IMarkupExtension<T>`](xref:Xamarin.Forms.Xaml.IMarkupExtension`1) 。 您可以在 GitHub 存放庫的[ **MarkupExtensions**目錄](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Xaml/MarkupExtensions)中，流覽下面所述標準標記延伸的原始程式碼 Xamarin.Forms 。

您也可以衍生自或，以定義您自己的自訂 XAML 標記延伸 `IMarkupExtension` `IMarkupExtension<T>` 。 如果標記延伸取得特定類型的值，請使用一般表單。 以下是數個標記延伸的情況 Xamarin.Forms ：

- `TypeExtension`衍生自`IMarkupExtension<Type>`
- `ArrayExtension`衍生自`IMarkupExtension<Array>`
- `DynamicResourceExtension`衍生自`IMarkupExtension<DynamicResource>`
- `BindingExtension`衍生自`IMarkupExtension<BindingBase>`
- `ConstraintExpression`衍生自`IMarkupExtension<Constraint>`

這兩個 `IMarkupExtension` 介面只會定義一個方法，名為 `ProvideValue` ：

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

由於 `IMarkupExtension<T>` 衍生自 `IMarkupExtension` 並 `new` 在上包含關鍵字 `ProvideValue` ，因此它包含兩種 `ProvideValue` 方法。

通常，XAML 標記延伸會定義有助於傳回值的屬性。 （明顯的例外狀況是 `NullExtension` ，只會傳回 `ProvideValue` `null` ）。`ProvideValue`方法具有類型的單一引數 `IServiceProvider` ，將在本文稍後討論。

## <a name="a-markup-extension-for-specifying-color"></a>用於指定色彩的標記延伸

下列 XAML 標記延伸可讓您 `Color` 使用色調、飽和度和亮度元件來建立值。 它會針對色彩的四個元件定義四個屬性，包括初始化為1的 Alpha 元件。 類別衍生自 `IMarkupExtension<Color>` ，表示傳回 `Color` 值：

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

因為 `IMarkupExtension<T>` 衍生自 `IMarkupExtension` ，所以類別必須包含兩個 `ProvideValue` 方法，一個會傳回， `Color` 另一個 `object` 則會傳回，但第二個方法可以直接呼叫第一個方法。

[ **HSL 色彩示範**] 頁面會顯示 `HslColorExtension` 可在 XAML 檔案中顯示的各種方式，以指定的色彩 `BoxView` ：

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

請注意，當 `HslColorExtension` 是 XML 標記時，會將四個屬性設定為屬性，但當它出現在大括弧之間時，四個屬性會以逗號分隔，不加引號。 、和的預設值為 `H` `S` `L` 0，而的預設值 `A` 為1，因此如果您想要將它們設定為預設值，則可以省略這些屬性。 最後一個範例顯示亮度為0的範例，通常會產生黑色，但 Alpha 色板為0.5，因此為半透明，並針對頁面的白色背景顯示為灰色：

[![HSL 色彩示範](creating-images/hslcolordemo-small.png "HSL 色彩示範")](creating-images/hslcolordemo-large.png#lightbox "HSL 色彩示範")

## <a name="a-markup-extension-for-accessing-bitmaps"></a>用於存取點陣圖的標記延伸

的引數 `ProvideValue` 是一個物件，它會執行 [`IServiceProvider`](xref:System.IServiceProvider) .net 命名空間中定義的介面 `System` 。 這個介面有一個成員，這是一個名為 `GetService` 且具有 `Type` 引數的方法。

`ImageResourceExtension`下面所示的類別顯示一個可能的使用 `IServiceProvider` 和， `GetService` 以取得 `IXmlLineInfoProvider` 可提供行和字元資訊的物件，指出偵測到特定錯誤的位置。 在此情況下，當尚未設定屬性時，就會引發例外狀況 `Source` ：

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

`ImageResourceExtension`當 XAML 檔案需要存取儲存為 .NET Standard 程式庫專案中之內嵌資源的影像檔時，會很有説明。 它會使用 `Source` 屬性來呼叫靜態 `ImageSource.FromResource` 方法。 這個方法需要完整的資源名稱，其中包含元件名稱、資料夾名稱，以及以句點分隔的檔案名。 方法的第二個引數 `ImageSource.FromResource` 會提供元件名稱，而且只有 UWP 上的發行組建才需要。 不論 `ImageSource.FromResource` 是否必須從包含點陣圖的元件呼叫，這表示此 XAML 資源延伸模組不可以是外部程式庫的一部分，除非映射也在該程式庫中。 （如需存取儲存為內嵌資源之點陣圖的詳細資訊，請參閱[**內嵌影像**](~/xamarin-forms/user-interface/images.md#embedded-images)文章。）

雖然 `ImageResourceExtension` 需要 `Source` 設定屬性，但 `Source` 屬性會在屬性中表示為類別的 content 屬性。 這表示 `Source=` 可以省略運算式中的部分（大括弧）。 在 [**影像資源示範**] 頁面中， `Image` 元素會使用資料夾名稱和檔案名（以句點分隔）提取兩個影像：

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

藉由使用的 `IServiceProvider` 引數 `ProvideValue` ，xaml 標記延伸可存取其所使用之 XAML 檔案的實用資訊。 但若要 `IServiceProvider` 順利使用引數，您必須知道特定內容中有何種服務可用。 瞭解這項功能的最佳方式，就是在 GitHub 上的存放庫中，研究[ **MarkupExtensions**資料夾](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Xaml/MarkupExtensions)內的現有 XAML 標記延伸的原始程式碼 Xamarin.Forms 。 請注意，某些類型的服務是內部的 Xamarin.Forms 。

在某些 XAML 標記延伸中，此服務可能很有用：

```csharp
 IProvideValueTarget provideValueTarget = serviceProvider.GetService(typeof(IProvideValueTarget)) as IProvideValueTarget;
```

`IProvideValueTarget`介面會定義兩個屬性： `TargetObject` 和 `TargetProperty` 。 當在類別中取得這項資訊時 `ImageResourceExtension` ， `TargetObject` 會是， `Image` 而 `TargetProperty` 則是 `BindableProperty` `Source` 之屬性的物件 `Image` 。 這是已設定 XAML 標記延伸的屬性。

`GetService`具有之引數的呼叫 `typeof(IProvideValueTarget)` 實際上會傳回類型為的物件 `SimpleValueTargetProvider` ，其定義于 `Xamarin.Forms.Xaml.Internals` 命名空間中。 如果您將的傳回值轉換 `GetService` 成該類型，您也可以存取 `ParentObjects` 屬性，這是一個陣列，其中包含專案 `Image` 、 `Grid` 父系和的 `ImageResourceDemoPage` 父系 `Grid` 。

## <a name="conclusion"></a>結論

XAML 標記延伸可讓您從各種來源擴充設定屬性的能力，以在 XAML 中扮演重要的角色。 此外，如果現有的 XAML 標記延伸並未提供您所需的確切功能，您也可以自行撰寫。

## <a name="related-links"></a>相關連結

- [標記延伸（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)
- [書籍中的 XAML 標記延伸章節 Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
