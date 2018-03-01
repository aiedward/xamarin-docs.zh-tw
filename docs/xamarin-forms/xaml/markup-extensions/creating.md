---
title: "建立 XAML 標記延伸"
description: "定義您自己自訂的 XAML 標記延伸"
ms.topic: article
ms.prod: xamarin
ms.assetid: 797C1EF9-1C8E-4208-8610-9B79CCF17D46
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 01/05/2018
ms.openlocfilehash: eb7189226e4f5d7eb2b55bf61728e65db44ba57b
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="creating-xaml-markup-extensions"></a>建立 XAML 標記延伸

以程式設計方式的層級的 XAML 標記延伸是實作的類別[ `IMarkupExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.IMarkupExtension/)或[ `IMarkupExtension<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.IMarkupExtension%3CT%3E/)介面。 您可以瀏覽下所述的標準標記延伸模組的原始程式碼[ **MarkupExtensions**目錄](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Xaml/MarkupExtensions)Xamarin.Forms GitHub 儲存機制。 

它也可由衍生自定義您自己自訂的 XAML 標記延伸`IMarkupExtension`或`IMarkupExtension<T>`。 如果標記延伸取得特定類型的值，請使用一般的表單。 這是使用數個 Xamarin.Forms 標記延伸的案例：

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

因為`IMarkupExtension<T>`衍生自`IMarkupExtension`並包含`new`關鍵字`ProvideValue`，它同時包含`ProvideValue`方法。

通常，XAML 標記延伸會定義屬性構成的傳回值。 (明顯的例外狀況是`NullExtension`，在其中`ProvideValue`只會傳回`null`。)`ProvideValue`方法具有單一引數的型別`IServiceProvider`，將在本文稍後討論。

## <a name="a-markup-extension-for-specifying-color"></a>標記延伸，為指定的色彩

下列 XAML 標記延伸可讓您建構`Color`值使用色調、 飽和度和亮度元件。 它會定義四個元件的色彩，包括初始化為 1 的 alpha 元件的四個屬性。 類別衍生自`IMarkupExtension<Color>`表示`Color`傳回值：

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

因為`IMarkupExtension<T>`衍生自`IMarkupExtension`，類別必須包含兩個`ProvideValue`方法，傳回的一個`Color`另一個則會傳回`object`，但是第二個方法只可以呼叫第一種方法。

**HSL 色彩示範**頁面會顯示各種不同的方式，`HslColorExtension`可以出現在 XAML 檔案指定的色彩`BoxView`:

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

請注意，當`HslColorExtension`XML 標記，四個屬性的設定屬性，但是當似乎大括號之間，會以不加引號的逗號分隔的四個屬性。 預設值`H`， `S`，和`L`0，且預設值的`A`為 1，因此可以省略這些屬性，如果要將它們設為預設值。 最後一個範例顯示的範例其中亮度為 0，這通常會以黑色，但是 alpha 色板 0.5，因此它是半透明的並顯示灰色白色背景的頁面：

[![HSL Color Demo](creating-images/hslcolordemo-small.png "HSL Color Demo")](creating-images/hslcolordemo-large.png "HSL Color Demo")

## <a name="a-markup-extension-for-accessing-bitmaps"></a>標記延伸來存取的點陣圖

引數`ProvideValue`是實作的物件[ `IServiceProvider` ](https://developer.xamarin.com/api/type/System.IServiceProvider/)介面定義在.NET 中，它`System`命名空間。 這個介面具有一個成員的方法，名為`GetService`與`Type`引數。 

`ImageResourceExtension`如下所示的類別會顯示可能的用途之一`IServiceProvider`和`GetService`取得`IXmlLineInfoProvider`物件可提供行與字元指出偵測特定錯誤的資訊。 在此情況下，會引發例外狀況時`Source`屬性尚未設定：

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

        return ImageSource.FromResource(assemblyName + "." + Source);
    }

    object IMarkupExtension.ProvideValue(IServiceProvider serviceProvider)
    {
        return (this as IMarkupExtension<ImageSource>).ProvideValue(serviceProvider);
    }
}
```

`ImageResourceExtension` XAML 檔案需要存取儲存為可攜式類別庫專案中的內嵌資源影像檔時，就會很有幫助。 它會使用`Source`屬性，以呼叫靜態`ImageSource.FromResource`方法。 此方法需要完整限定的資源名稱，其中包含組件名稱、 資料夾名稱，並以句號分隔的檔案名稱。 `ImageResourceExtension`不需要的組件名稱部分，因此它會取得使用反映的組件名稱前面加上以`Source`屬性。 不論如何，`ImageSource.FromResource`必須從包含點陣圖，這表示此 XAML 資源擴充功能不能是外部的程式庫的一部分，除非的映像也為該文件庫中的組件呼叫。 (請參閱[**內嵌影像**](~/xamarin-forms/user-interface/images.md#embedded_images)點陣圖儲存為內嵌資源的存取詳細資訊的發行項。) 

雖然`ImageResourceExtension`需要`Source`屬性來設定，`Source`屬性會指出在屬性中，做為類別的內容屬性。 這表示`Source=`括孤括住之運算式的一部分，則可以省略。 在**映像資源示範** 頁面上，`Image`項目擷取使用的資料夾名稱和檔案名稱以句號分隔的兩個映像：

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

以下是所有三個平台上執行的程式：

[![映像資源示範](creating-images/imageresourcedemo-small.png "映像資源示範")](creating-images/imageresourcedemo-large.png "映像資源示範")

## <a name="service-providers"></a>服務提供者

使用`IServiceProvider`引數`ProvideValue`，XAML 標記延伸取得有關 XAML 檔案中所用的有用資訊的存取。 不過，利用`IServiceProvider`引數成功，您必須知道哪一種服務是供特定內容中。 若要了解這項功能，最好是藉由研究中的現有 XAML 標記延伸模組的原始程式碼[ **MarkupExtensions**資料夾](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Xaml/MarkupExtensions)GitHub 上的 Xamarin.Forms 儲存機制中。 請注意，服務的某些類型的內部 Xamarin.Forms。

在某些 XAML 標記延伸模組，此服務可能很有用：

```csharp
 IProvideValueTarget provideValueTarget = serviceProvider.GetService(typeof(IProvideValueTarget)) as IProvideValueTarget;
```

`IProvideValueTarget`介面會定義兩個屬性：`TargetObject`和`TargetProperty`。 當中取得這項資訊是`ImageResourceExtension`類別`TargetObject`是`Image`和`TargetProperty`是`BindableProperty`物件`Source`屬性`Image`。 這是在其設定 XAML 標記延伸的屬性。

`GetService`使用引數呼叫`typeof(IProvideValueTarget)`實際傳回型別的物件`SimpleValueTargetProvider`，定義在`Xamarin.Forms.Xaml.Internals`命名空間。 如果您轉型的傳回值`GetService`成該類型，您也可以存取`ParentObjects`屬性，這是陣列，其中包含`Image`項目，`Grid`父代，而`ImageResourceDemoPage`的父代`Grid`。

## <a name="conclusion"></a>結論

XAML 標記延伸在 XAML 中扮演重要角色，藉由延伸設定屬性，從各種來源的能力。 此外，如果沒有提供現有的 XAML 標記延伸，完全必要條件，您也可以撰寫您自己。 


## <a name="related-links"></a>相關連結

- [標記延伸 （範例）](https://developer.xamarin.com/samples/xamarin-forms/XAML/MarkupExtensions/)
- [Xamarin.Forms 通訊錄中的 XAML 標記延伸的章節](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
