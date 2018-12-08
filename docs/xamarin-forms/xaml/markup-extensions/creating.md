---
title: 建立 XAML 標記延伸
description: 這篇文章說明如何定義您自己自訂的 Xamarin.Forms XAML 標記延伸模組。 XAML 標記延伸是實作 IMarkupExtension IMarkupExtension 介面的類別。
ms.prod: xamarin
ms.assetid: 797C1EF9-1C8E-4208-8610-9B79CCF17D46
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 531fb9500bdbf9d07ac3f781113768395465bd50
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2018
ms.locfileid: "53050552"
---
# <a name="creating-xaml-markup-extensions"></a>建立 XAML 標記延伸

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/XAML/MarkupExtensions/)

以程式設計方式層級中，XAML 標記延伸是實作的類別[ `IMarkupExtension` ](xref:Xamarin.Forms.Xaml.IMarkupExtension)或是[ `IMarkupExtension<T>` ](xref:Xamarin.Forms.Xaml.IMarkupExtension`1)介面。 您可以瀏覽原始程式碼，如下所述的標準標記延伸[ **Markupextension** directory](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Xaml/MarkupExtensions) Xamarin.Forms GitHub 存放庫。

您也可定義您自己自訂的 XAML 標記延伸模組，藉由衍生自`IMarkupExtension`或`IMarkupExtension<T>`。 如果標記延伸取得特定類型的值，請使用一般格式。 這是使用 Xamarin.Forms 標記延伸的數個案例：

- `TypeExtension` 衍生自 `IMarkupExtension<Type>`
- `ArrayExtension` 衍生自 `IMarkupExtension<Array>`
- `DynamicResourceExtension` 衍生自 `IMarkupExtension<DynamicResource>`
- `BindingExtension` 衍生自 `IMarkupExtension<BindingBase>`
- `ConstraintExpression` 衍生自 `IMarkupExtension<Constraint>`

這兩個`IMarkupExtension`介面定義只有一個方法，名為`ProvideValue`:

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

由於`IMarkupExtension<T>`衍生自`IMarkupExtension`，並包含`new`關鍵字`ProvideValue`，它同時包含`ProvideValue`方法。

非常頻繁且 XAML 標記延伸模組會定義參與的屬性至傳回值。 (明顯的例外狀況`NullExtension`，在其中`ProvideValue`只會傳回`null`。)`ProvideValue`方法具有單一引數的型別`IServiceProvider`，將在本文稍後討論。

## <a name="a-markup-extension-for-specifying-color"></a>指定色彩之標記延伸模組

下列 XAML 標記延伸可讓您建構`Color`值使用色調、 飽和度和亮度的元件。 它會定義四個屬性的色彩，包括會初始化為 1 的 alpha 元件的四個元件。 類別衍生自`IMarkupExtension<Color>`表示`Color`傳回值：

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

因為`IMarkupExtension<T>`衍生自`IMarkupExtension`，此類別必須包含兩個`ProvideValue`方法，傳回對應到一個`Color`另一個則會傳回`object`，但第二個方法可以直接呼叫第一個方法。

**HSL 色彩 Demo**頁面會顯示各種`HslColorExtension`可以出現在 XAML 檔案，以指定的色彩`BoxView`:

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

請注意，當`HslColorExtension`XML 標記，四個屬性會設為屬性，但當它大括號之間出現時，會以不含引號的逗號分隔的四個屬性。 預設值`H`， `S`，並`L`0，且預設值`A`為 1，因此可以省略這些屬性，如果要將它們設為預設值。 最後一個範例顯示的範例，其中的明暗度為 0，這通常會導致黑色，但 alpha 色板為 0.5，因此是半透明的且會出現灰色以白色為背景的頁面：

[![HSL 色彩示範](creating-images/hslcolordemo-small.png "HSL 色彩示範")](creating-images/hslcolordemo-large.png#lightbox "HSL 色彩示範")

## <a name="a-markup-extension-for-accessing-bitmaps"></a>標記延伸來存取的點陣圖

引數`ProvideValue`是實作的物件[ `IServiceProvider` ](xref:System.IServiceProvider)介面，其定義於.NET`System`命名空間。 這個介面具有一個成員，名為的方法`GetService`與`Type`引數。

`ImageResourceExtension`如下所示的類別顯示的可能用法之一`IServiceProvider`並`GetService`若要取得`IXmlLineInfoProvider`可以提供行與字元表示已在偵測到的特定錯誤的資訊的物件。 在此情況下，會引發例外狀況時`Source`屬性尚未設定：

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

`ImageResourceExtension` 當需要存取影像檔儲存為.NET Standard 程式庫專案中的內嵌資源 XAML 檔案時，則會有幫助。 它會使用`Source`屬性，以呼叫靜態`ImageSource.FromResource`方法。 此方法需要完整的資源名稱，其中包含組件名稱、 資料夾名稱，並以句號分隔的檔案名稱。 第二個引數`ImageSource.FromResource`方法提供組件名稱，並只需要此屬性的 UWP 上的發行組建。 不論如何，`ImageSource.FromResource`必須從包含點陣圖，這表示此 XAML 資源延伸模組無法為外部程式庫的一部分，除非影像是該程式庫中的組件呼叫。 (請參閱[**內嵌影像**](~/xamarin-forms/user-interface/images.md#embedded-images)存取點陣圖儲存為內嵌資源的更多有關的文章。)

雖然`ImageResourceExtension`需要`Source`屬性設定，`Source`屬性做為類別的內容屬性所示的屬性。 這表示`Source=`括孤括住之運算式的一部分，則可以省略。 在 **映像資源示範**頁面上，`Image`項目擷取使用資料夾名稱和檔案名稱以句號分隔的兩個映像：

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

以下是執行的程式：

[![映像資源示範](creating-images/imageresourcedemo-small.png "映像資源示範")](creating-images/imageresourcedemo-large.png#lightbox "映像資源示範")

## <a name="service-providers"></a>服務提供者

藉由使用`IServiceProvider`引數`ProvideValue`，XAML 標記延伸可讓 XAML 檔案中使用的實用資訊的存取。 不過，利用`IServiceProvider`引數成功，您需要知道何種服務可在特定內容中。 若要了解這項功能的最佳方式是藉由研究中的現有 XAML 標記延伸模組的原始程式碼[ **Markupextension**資料夾](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Xaml/MarkupExtensions)在 GitHub 上的 Xamarin.Forms 存放庫中。 請注意，服務的某些類型的 Xamarin.Forms 內部。

在某些 XAML 標記延伸模組，這項服務可能很有用：

```csharp
 IProvideValueTarget provideValueTarget = serviceProvider.GetService(typeof(IProvideValueTarget)) as IProvideValueTarget;
```

`IProvideValueTarget`介面會定義兩個屬性：`TargetObject`和`TargetProperty`。 中取得這項資訊的時機`ImageResourceExtension`類別，`TargetObject`是`Image`並`TargetProperty`是`BindableProperty`物件`Source`屬性`Image`。 這是在其已設定 XAML 標記延伸的屬性。

`GetService`呼叫的引數`typeof(IProvideValueTarget)`實際上會傳回型別的物件`SimpleValueTargetProvider`，其定義於`Xamarin.Forms.Xaml.Internals`命名空間。 如果您轉型的傳回值`GetService`成該類型，您也可以存取`ParentObjects`屬性，這是陣列，其中包含`Image`項目，`Grid`父代，而`ImageResourceDemoPage`父代`Grid`。

## <a name="conclusion"></a>結論

XAML 標記延伸在 XAML 中扮演重要的角色，藉由擴充，能夠從各種來源中設定屬性。 此外，如果現有的 XAML 標記延伸模組未提供真正需要您也可以撰寫您自己。

## <a name="related-links"></a>相關連結

- [標記延伸模組 （範例）](https://developer.xamarin.com/samples/xamarin-forms/XAML/MarkupExtensions/)
- [Xamarin.Forms 通訊錄中的 XAML 標記延伸模組的章節](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
