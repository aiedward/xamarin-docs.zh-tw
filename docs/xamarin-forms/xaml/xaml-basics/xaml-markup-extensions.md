---
title: 第 3 部分。 XAML 標記延伸
description: XAML 標記延伸模組組成允許屬性設為物件或間接參考來自其他來源的值的 XAML 中的重要功能。
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F4A37564-B18B-42FF-B841-9A1949895AB6
author: charlespetzold
ms.author: chape
ms.date: 3/27/2018
ms.openlocfilehash: 6fcb051d2c24c7da169106b06ad5ebfc91edafa6
ms.sourcegitcommit: 3e980fbf92c69c3dd737554e8c6d5b94cf69ee3a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2018
ms.locfileid: "37935611"
---
# <a name="part-3-xaml-markup-extensions"></a>第 3 部分。 XAML 標記延伸

_XAML 標記延伸模組組成允許屬性設為物件或間接參考來自其他來源的值的 XAML 中的重要功能。XAML 標記延伸是特別重要，共用物件，以及參考整個應用程式，所使用的常數，但他們在資料繫結中找到其最大的公用程式。_

## <a name="xaml-markup-extensions"></a>XAML 標記延伸

一般情況下，您可以使用 XAML 物件的屬性設定為明確的值，例如字串、 數字、 列舉成員或轉換成在幕後值的字串。

有時候，不過，屬性必須改為參考其他，定義於某處的值，或可能需要處理在執行階段的程式碼。 對於這些用途，XAML*標記延伸*可用。

這些 XAML 標記延伸模組不是 XML 的延伸模組。 XAML 是完全合法的 XML。 它們之所以稱為"extensions"，因為它們由類別中實作的程式碼支援`IMarkupExtension`。 您可以撰寫自己的自訂標記延伸模組。

在許多情況下，XAML 標記延伸是 XAML 檔案中立即辨識出因為它們會顯示以大括號分隔的屬性設定為: {和}，但有時標記延伸模組會出現在標記中做為傳統的項目。

## <a name="shared-resources"></a>共用的資源

某些 XAML 頁面會包含數個檢視屬性設成相同的值。 例如，有很多的屬性設定為這些`Button`物件是否相同：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <StackLayout>
        <Button Text="Do this!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                BorderWidth="3"
                Rotation="-15"
                TextColor="Red"
                FontSize="24" />

        <Button Text="Do that!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                BorderWidth="3"
                Rotation="-15"
                TextColor="Red"
                FontSize="24" />

        <Button Text="Do the other thing!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                BorderWidth="3"
                Rotation="-15"
                TextColor="Red"
                FontSize="24" />

    </StackLayout>
</ContentPage>
```

如果其中一個屬性需要變更，您可能想要進行的變更，就可以一次，而不是三次。 如果這是程式碼時，您會有可能使用常數和靜態的唯讀物件為了保護這類值一致且輕鬆地修改。

在 XAML 中，一個常用的解決方案是用來儲存這類值或物件中*資源字典*。 `VisualElement`類別會定義名為的屬性`Resources`型別的`ResourceDictionary`，這是類型的索引鍵的字典`string`型別的和值`object`。 您可以把物件放到這個字典，然後再從標記中，所有在 XAML 中參考它們。

若要在頁面上使用的資源字典，包含一組`Resources`屬性項目標記。 它是最方便的方式將這些頁面的頂端：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>

    </ContentPage.Resources>
    ...
</ContentPage>
```

它也是需要明確包含`ResourceDictionary`標記：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>
        <ResourceDictionary>

        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

現在物件的各種類型的值可以新增至資源字典。 這些類型必須是可具現化。 它們不能是抽象類別，例如。 這些類型也必須有公用的無參數建構函式。 每個項目需要使用指定的字典索引鍵`x:Key`屬性。 例如: 

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>
        <ResourceDictionary>
            <LayoutOptions x:Key="horzOptions"
                           Alignment="Center" />

            <LayoutOptions x:Key="vertOptions"
                           Alignment="Center"
                           Expands="True" />
        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

這些兩個項目是結構類型的值`LayoutOptions`，和每個都有唯一的索引鍵和一個或兩個屬性設定。 在程式碼和標記，它是更為普遍使用的靜態欄位`LayoutOptions`，但以下是更方便地設定屬性。

現在就必須設定`HorizontalOptions`並`VerticalOptions`這些資源，這些按鈕的屬性並完成與`StaticResource`XAML 標記延伸模組：

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="3"
        Rotation="-15"
        TextColor="Red"
        FontSize="24" />
```

`StaticResource`標記延伸模組一律會以大括號，並包含字典索引鍵。

名稱`StaticResource`使得它與`DynamicResource`，Xamarin.Forms 也支援。 `DynamicResource` 適用於在執行階段，可能會變更的值相關聯的字典索引鍵時`StaticResource`存取來自字典的項目，一次 頁面上的項目建構的時。

針對`BorderWidth`屬性，就必須儲存在字典中的雙精度浮點數。 XAML 輕鬆定義標記等的一般資料型別`x:Double`和`x:Int32`:

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <LayoutOptions x:Key="horzOptions"
                       Alignment="Center" />

        <LayoutOptions x:Key="vertOptions"
                       Alignment="Center"
                       Expands="True" />

        <x:Double x:Key="borderWidth">
            3
        </x:Double>
    </ResourceDictionary>
</ContentPage.Resources>
```

您不需要將它放在三行。 此旋轉角度這個字典項目只會佔用一行：

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <LayoutOptions x:Key="horzOptions"
                       Alignment="Center" />

        <LayoutOptions x:Key="vertOptions"
                       Alignment="Center"
                       Expands="True" />

         <x:Double x:Key="borderWidth">
            3
         </x:Double>

        <x:Double x:Key="rotationAngle">-15</x:Double>
    </ResourceDictionary>
</ContentPage.Resources>
```

這兩個資源中的相同方式來參考`LayoutOptions`值：

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="{StaticResource borderWidth}"
        Rotation="{StaticResource rotationAngle}"
        TextColor="Red"
        FontSize="24" />
```

資源類型的`Color`，您可以使用直接指派一種類型的屬性時，您使用的相同字串表示。 建立資源時，會叫用型別轉換子。 以下是一種資源類型的`Color`:

```xaml
<Color x:Key="textColor">Red</Color>
```

通常，程式設定`FontSize`屬性的成員`NamedSize`列舉型別，例如`Large`。 `FontSizeConverter`類別將它轉換成平台相依的值，使用在幕後的運作方式`Device.GetNamedSized`方法。 不過，當定義字型大小資源，較為合理使用數字的值，並顯示為`x:Double`類型：

```xaml
<x:Double x:Key="fontSize">24</x:Double>
```

現在所有屬性除了`Text`資源設定所定義：

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="{StaticResource borderWidth}"
        Rotation="{StaticResource rotationAngle}"
        TextColor="{StaticResource textColor}"
        FontSize="{StaticResource fontSize}" />
```

您也可使用`OnPlatform`內定義不同的值，平台的資源字典。 以下是如何`OnPlatform`物件可以是不同的文字色彩的資源字典的一部分：

```xaml
<OnPlatform x:Key="textColor"
            x:TypeArguments="Color">
    <On Platform="iOS" Value="Red" />
    <On Platform="Android" Value="Aqua" />
    <On Platform="UWP" Value="#80FF80" />
</OnPlatform>
```

請注意，`OnPlatform`取得兩者`x:Key`屬性，因為它是在字典中的物件和`x:TypeArguments`屬性，因為它是泛型類別。 `iOS`， `Android`，並`UWP`屬性會轉換成`Color`值物件初始化時。

以下是最後一個完整的 XAML 檔案具有存取共用的六個值的三個按鈕：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>
        <ResourceDictionary>
            <LayoutOptions x:Key="horzOptions"
                           Alignment="Center" />

            <LayoutOptions x:Key="vertOptions"
                           Alignment="Center"
                           Expands="True" />

            <x:Double x:Key="borderWidth">3</x:Double>

            <x:Double x:Key="rotationAngle">-15</x:Double>

            <OnPlatform x:Key="textColor"
                        x:TypeArguments="Color">
                <On Platform="iOS" Value="Red" />
                <On Platform="Android" Value="Aqua" />
                <On Platform="UWP" Value="#80FF80" />
            </OnPlatform>

            <x:String x:Key="fontSize">Large</x:String>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout>
        <Button Text="Do this!"
                HorizontalOptions="{StaticResource horzOptions}"
                VerticalOptions="{StaticResource vertOptions}"
                BorderWidth="{StaticResource borderWidth}"
                Rotation="{StaticResource rotationAngle}"
                TextColor="{StaticResource textColor}"
                FontSize="{StaticResource fontSize}" />

        <Button Text="Do that!"
                HorizontalOptions="{StaticResource horzOptions}"
                VerticalOptions="{StaticResource vertOptions}"
                BorderWidth="{StaticResource borderWidth}"
                Rotation="{StaticResource rotationAngle}"
                TextColor="{StaticResource textColor}"
                FontSize="{StaticResource fontSize}" />

        <Button Text="Do the other thing!"
                HorizontalOptions="{StaticResource horzOptions}"
                VerticalOptions="{StaticResource vertOptions}"
                BorderWidth="{StaticResource borderWidth}"
                Rotation="{StaticResource rotationAngle}"
                TextColor="{StaticResource textColor}"
                FontSize="{StaticResource fontSize}" />

    </StackLayout>
</ContentPage>
```

一致的樣式，以及平台相依的樣式，請確認螢幕擷取畫面：

[![](xaml-markup-extensions-images/sharedresources.png "樣式的控制項")](xaml-markup-extensions-images/sharedresources-large.png#lightbox "樣式的控制項")

雖然它是最常用來定義`Resources`集合，頂端的頁面上，請記住，`Resources`屬性由定義`VisualElement`，而且可以有`Resources`在頁面上的其他元素的集合。 例如，再次嘗試新增一個`StackLayout`在此範例中：

```xaml
<StackLayout>
    <StackLayout.Resources>
        <ResourceDictionary>
            <Color x:Key="textColor">Blue</Color>
        </ResourceDictionary>
    </StackLayout.Resources>
    ...
</StackLayout>
```

您會發現按鈕的文字色彩現在是藍色。 基本上，每當 XAML 剖析器碰到`StaticResource`標記延伸，它會搜尋在視覺樹狀結構並使用第一個`ResourceDictionary`遇到包含該索引鍵。

最常見的資源字典中儲存的物件類型的其中一個方法是透過 Xamarin.Forms `Style`，其定義的屬性設定集合。 樣式會在本文中討論[樣式](~/xamarin-forms/user-interface/styles/index.md)。

有時候 XAML 新手開發人員想知道是否它們可以這類放視覺化項目`Label`或是`Button`在`ResourceDictionary`。 雖然您當然可以，它不太合理。 目的`ResourceDictionary`是共用的物件。 無法共用的視覺元素。 相同的執行個體不能出現在單一頁面上的兩倍。

## <a name="the-xstatic-markup-extension"></a>X:static 標記延伸

儘管其名稱的相似之處`x:Static`和`StaticResource`非常不同。 `StaticResource` 從資源字典，同時傳回物件`x:Static`存取下列其中之一：

- 公用靜態欄位
- 公用的靜態屬性
- 公用常數欄位
- 列舉的成員。

`StaticResource`標記延伸支援 XAML 實作，定義資源字典，雖然`x:Static`屬於內建的 XAML，做為`x`前置詞會顯示。

以下是幾個範例示範如何`x:Static`可以明確地參考靜態欄位和列舉成員：

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="{x:Static LayoutOptions.Start}"
       HorizontalTextAlignment="{x:Static TextAlignment.Center}"
       TextColor="{x:Static Color.Aqua}" />
```

到目前為止，這不是很令人印象深刻。 但`x:Static`標記延伸也可以參考靜態欄位或是屬性從您自己的程式碼。 例如，以下是`AppConstants`類別，其中包含一些您可能想要使用整個應用程式的多個頁面上的靜態欄位：

```csharp
using System;
using Xamarin.Forms;

namespace XamlSamples
{
    static class AppConstants
    {
        public static readonly Thickness PagePadding;

        public static readonly Font TitleFont;

        public static readonly Color BackgroundColor = Color.Aqua;

        public static readonly Color ForegroundColor = Color.Brown;

        static AppConstants()
        {
            switch (Device.RuntimePlatform)
            {
                case Device.iOS:
                    PagePadding = new Thickness(5, 20, 5, 0);
                    TitleFont = Font.SystemFontOfSize(35, FontAttributes.Bold);
                    break;

                case Device.Android:
                    PagePadding = new Thickness(5, 0, 5, 0);
                    TitleFont = Font.SystemFontOfSize(40, FontAttributes.Bold);
                    break;

                case Device.UWP:
                    PagePadding = new Thickness(5, 0, 5, 0);
                    TitleFont = Font.SystemFontOfSize(50, FontAttributes.Bold);
                    break;
            }
        }
    }
}
```

若要參考的 XAML 檔案中的這個類別的靜態欄位，您將需要某種方式來表示此檔案所在的 XAML 檔案中。 要這麼做，使用 XML 命名空間宣告。

重新叫用標準的 Xamarin.Forms XAML 範本的過程中建立的 XAML 檔案包含兩個 XML 命名空間宣告： 一個用於存取 Xamarin.Forms 類別，而另一個參考標記和屬性的 XAML 內建函式：

```csharp
xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
```

您將需要額外的 XML 命名空間宣告，來存取其他類別。 每個額外的 XML 命名空間宣告會定義新的前置詞。 若要存取類別本機共用應用程式的.NET Standard 程式庫，例如`AppConstants`，XAML 程式設計人員通常會使用前置詞`local`。 命名空間宣告必須指出 CLR (Common Language Runtime) 命名空間名稱，也就是.NET 命名空間名稱，也就是名稱出現在 C#`namespace`定義或`using`指示詞：

```csharp
xmlns:local="clr-namespace:XamlSamples"
```

您也可以定義.NET Standard 程式庫參考任何組件中的.NET 命名空間的 XML 命名空間宣告。 例如，以下是`sys`標準的.NET 的前置詞`System`命名空間，也就是**mscorlib**組件中，一次建立 「 Microsoft 通用物件執行階段程式庫，」 這項，但現在表示 「 多語系標準通用物件執行階段程式庫。 」 因為這是另一個組件時，您也必須指定組件名稱，在此情況下**mscorlib**:

```csharp
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

請注意，關鍵字`clr-namespace`後面接著冒號及.NET 命名空間名稱，後面接著一個分號，關鍵字`assembly`，等號，以及組件名稱。

是的之後要加冒號`clr-namespace`等號後面，但`assembly`。 語法中定義此方式刻意： 最 XML 命名空間宣告參考的 URI，例如開始一個 URI 配置名稱`http`，這一律後接冒號。 `clr-namespace`這個字串的一部分要模擬這個慣例。

中包含下列命名空間宣告**StaticConstantsPage**範例。 請注意，`BoxView`維度會設為`Math.PI`和`Math.E`，但縮放為 100 倍：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             xmlns:sys="clr-namespace:System;assembly=mscorlib"
             x:Class="XamlSamples.StaticConstantsPage"
             Title="Static Constants Page"
             Padding="{x:Static local:AppConstants.PagePadding}">

    <StackLayout>
       <Label Text="Hello, XAML!"
              TextColor="{x:Static local:AppConstants.BackgroundColor}"
              BackgroundColor="{x:Static local:AppConstants.ForegroundColor}"
              Font="{x:Static local:AppConstants.TitleFont}"
              HorizontalOptions="Center" />

      <BoxView WidthRequest="{x:Static sys:Math.PI}"
               HeightRequest="{x:Static sys:Math.E}"
               Color="{x:Static local:AppConstants.ForegroundColor}"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand"
               Scale="100" />
    </StackLayout>
</ContentPage>
```

結果大小`BoxView`相對於畫面是 平台相關：

 [![](xaml-markup-extensions-images/staticconstants.png "控制項使用 X:static 標記延伸")](xaml-markup-extensions-images/staticconstants-large.png#lightbox "控制項使用 X:static 標記延伸")

## <a name="other-standard-markup-extensions"></a>其他標準標記延伸模組

數個標記延伸是 XAML 內建函式，並支援 Xamarin.Forms XAML 檔案中。 其中某些不常使用，但在需要時是不可或缺：

-  如果屬性的非`null`數值，依預設，但您想要將它設定為`null`，將它設定為`{x:Null}`標記延伸。
-  如果屬性型別的`Type`，您可以將它指派給`Type`物件使用標記延伸`{x:Type someClass}`。
-  您可以定義在 XAML 中使用的陣列`x:Array`標記延伸。 這個標記延伸具有必要的屬性，名為`Type`表示陣列中的項目型別。
- `Binding`標記延伸述[第 4 部分。資料繫結基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)。

## <a name="the-constraintexpression-markup-extension"></a>ConstraintExpression 標記延伸

標記延伸模組可以有屬性，但它們不設定類似 XML 屬性。 在標記延伸模組屬性設定為以逗號分隔，而且沒有引號會出現大括弧括住。

這可以利用名為 Xamarin.Forms 標記延伸說明`ConstraintExpression`，能在`RelativeLayout`類別。 做為常數，或相對於父代或其他具名的檢視，您可以指定的位置或子檢視的大小。 語法`ConstraintExpression`可讓您設定的位置或大小的檢視，使用`Factor`屬性的另一個檢視，再加上一次`Constant`。 比這個更複雜的任何項目需要程式碼。

以下為範例：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.RelativeLayoutPage"
             Title="RelativeLayout Page">

    <RelativeLayout>

        <!-- Upper left -->
        <BoxView Color="Red"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}" />
        <!-- Upper right -->
        <BoxView Color="Green"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=1,
                                            Constant=-40}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}" />
        <!-- Lower left -->
        <BoxView Color="Blue"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=1,
                                            Constant=-40}" />
        <!-- Lower right -->
        <BoxView Color="Yellow"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=1,
                                            Constant=-40}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=1,
                                            Constant=-40}" />

        <!-- Centered and 1/3 width and height of parent -->
        <BoxView x:Name="oneThird"
                 Color="Red"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=0.33}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=0.33}"
                 RelativeLayout.WidthConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=0.33}"
                 RelativeLayout.HeightConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=0.33}"  />

        <!-- 1/3 width and height of previous -->
        <BoxView Color="Blue"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=X}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=Y}"
                 RelativeLayout.WidthConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=Width,
                                            Factor=0.33}"
                 RelativeLayout.HeightConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=Height,
                                            Factor=0.33}"  />
    </RelativeLayout>
</ContentPage>
```

此範例中，您應該採取最重要的一課可能是標記延伸語法： 沒有引號必須出現在標記延伸的大括號。 鍵入時標記延伸在 XAML 檔案中，很自然地想要以引號括住屬性的值。 抵禦常見的錯誤 ！

以下是執行的程式：

[![](xaml-markup-extensions-images/relativelayout.png "使用條件約束的相對版面配置")](xaml-markup-extensions-images/relativelayout-large.png#lightbox "相對的版面配置使用條件約束")

## <a name="summary"></a>總結

如下所示的 XAML 標記延伸提供 XAML 檔案的重要的支援。 但或許是最有價值的 XAML 標記延伸`Binding`，其中涵蓋在本系列的下一個部分[第 4 部分。資料繫結基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)。



## <a name="related-links"></a>相關連結

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [第 1 部分：開始使用 XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [第 2 部分：基本 XAML 語法](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [第 4 部分：資料繫結的基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [第 5 部分：從資料繫結至 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
