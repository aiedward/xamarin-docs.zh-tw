---
title: "第 3 部分。 XAML 標記延伸"
description: "XAML 標記延伸會構成一項重要功能在 XAML 中，可將屬性設定為物件或間接參考來自其他來源的值。 XAML 標記延伸是特別重要的共用物件，並參考整個應用程式，所使用的常數，但他們資料繫結中找到其最大的公用程式。"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F4A37564-B18B-42FF-B841-9A1949895AB6
author: charlespetzold
ms.author: chape
ms.date: 10/25/2017
ms.openlocfilehash: 1c5c4c30a7e506e19fc4dc0728fb55851ec4911f
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2018
---
# <a name="part-3-xaml-markup-extensions"></a>第 3 部分。 XAML 標記延伸

_XAML 標記延伸會構成一項重要功能在 XAML 中，可將屬性設定為物件或間接參考來自其他來源的值。XAML 標記延伸是特別重要的共用物件，並參考整個應用程式，所使用的常數，但他們資料繫結中找到其最大的公用程式。_

## <a name="xaml-markup-extensions"></a>XAML 標記延伸

一般情況下，您可以使用 XAML 設定明確的值，例如字串、 數字、 列舉成員，或是轉換成在幕後值的字串物件的屬性。

有時候，不過，屬性必須改為參考其他，地方定義的值，或可能需要很少的處理程式碼在執行階段。 針對這些用途，XAML*標記延伸*可用。

這些 XAML 標記延伸不是 XML 的擴充功能。 XAML 是完全合法的 XML。 因為它們都由類別中實作的程式碼被稱為"extensions" `IMarkupExtension`。 您可以撰寫您自己的自訂標記延伸。

許多情況下，XAML 標記延伸會立即辨識在 XAML 檔案，因為它們會顯示以大括號分隔的屬性設定為: {和}，但有時標記延伸會出現在標記做為傳統的項目。

## <a name="shared-resources"></a>共用的資源

某些 XAML 頁面會包含數個檢視的屬性設定為相同的值。 例如，有許多的屬性設定為這些`Button`物件是否相同：

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
                FontSize="Large" />

        <Button Text="Do that!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                BorderWidth="3"
                Rotation="-15"
                TextColor="Red"
                FontSize="Large" />

        <Button Text="Do the other thing!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                BorderWidth="3"
                Rotation="-15"
                TextColor="Red"
                FontSize="Large" />

    </StackLayout>
</ContentPage>
```

如果其中一個屬性必須變更，您可能想要進行的變更就可以一次，而不是三次。 如果這是程式碼，您會很可能會使用常數和靜態的唯讀物件維持一致且輕鬆地修改這類值。

在 XAML 中，一個常用來儲存這類值或方案中的物件*資源字典*。 `VisualElement`類別會定義名為的屬性`Resources`型別的`ResourceDictionary`，這是類型的索引鍵的字典`string`和類型的值`object`。 您可以將物件放入此字典，並從標記，在 XAML 中的所有參考。

若要在網頁上使用的資源字典，包含一組`Resources`屬性項目標記。 它是最方便的方式將這些頁面的頂端：

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

它也是必要明確包含`ResourceDictionary`標記：

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

現在物件的各種類型的值可以加入至資源字典。 這些類型必須是可具現化。 它們不能是抽象類別，例如。 這些型別也必須擁有公用的無參數建構函式。 每個項目需要使用指定的字典索引鍵`x:Key`屬性。 例如: 

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

這兩個項目為結構類型的值`LayoutOptions`，和每個有唯一的索引鍵和一個或兩個屬性設定。 在程式碼和標記中，會更常使用的靜態欄位`LayoutOptions`，但以下是更方便地設定屬性。

現在就必須設定`HorizontalOptions`和`VerticalOptions`到這些資源，這些按鈕的屬性和可透過`StaticResource`XAML 標記延伸：

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="3"
        Rotation="-15"
        TextColor="Red"
        FontSize="Large" />
```

`StaticResource`標記延伸一律以大括號，分隔並包含字典的索引鍵。

名稱`StaticResource`區分從`DynamicResource`，Xamarin.Forms 也支援。 `DynamicResource` 適用於在執行階段中，可能會變更的值相關聯的字典索引鍵時`StaticResource`一次的項目頁面上的建構時存取從字典的項目。

如`BorderWidth`屬性，就必須在字典中儲存成雙精度浮點數。 XAML 方便地定義像一般資料類型的標記`x:Double`和`x:Int32`:

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

這些兩個資源可以參考相同的方式`LayoutOptions`值：

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="{StaticResource borderWidth}"
        Rotation="{StaticResource rotationAngle}"
        TextColor="Red"
        FontSize="Large" />
```

類型資源`Color`，您可以使用直接指派這些型別的屬性時，您使用的相同字串表示。 建立資源時，會叫用型別轉換子。 以下是類型的資源`Color`:

```xaml
<Color x:Key="textColor">Red</Color>
```

`FontSize`屬性顯示小問題。 屬性會定義為型別的`double`。 當您設定之屬性的成員為`NamedSize`列舉型別，例如`Large`、`FontSizeConverter`類別將它轉換成平台相依的值，使用在幕後運作`Device.GetNamedSized`方法。

不過，您不能定義的資源，字型大小為`double`並將值設為 「 大型 」。 在 XAML 剖析器處理資源的時間，它並不知道此值將用作字型的大小。 

解決方法是定義為資源`string`使用`x:String`類型：

```xaml
<x:String x:Key="fontSize">Large</x:String>
```

現在的所有屬性除外`Text`資源設定所定義：

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="{StaticResource borderWidth}"
        Rotation="{StaticResource rotationAngle}"
        TextColor="{StaticResource textColor}"
        FontSize="{StaticResource fontSize}" />
```

它也可使用`OnPlatform`內定義的平台的不同值的資源字典。 以下是如何`OnPlatform`物件可以是不同的文字色彩的資源字典的一部分：

```xaml
<OnPlatform x:Key="textColor"
            x:TypeArguments="Color">
    <On Platform="iOS" Value="Red" />
    <On Platform="Android" Value="Aqua" />
    <On Platform="UWP" Value="#80FF80" />
</OnPlatform>
```

請注意，`OnPlatform`取得兩者`x:Key`屬性，因為它是在字典中的物件和`x:TypeArguments`屬性，因為它是泛型類別。 `iOS`， `Android`，和`UWP`屬性都會轉換成`Color`時初始化物件的值。

以下是最終完成 XAML 檔案，以存取共用的六個值的三個按鈕：

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
                FontSize"{StaticResource fontSize}" />

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

螢幕擷取畫面會確認一致的樣式，平台相依樣式：

[![](xaml-markup-extensions-images/sharedresources.png "樣式的控制項")](xaml-markup-extensions-images/sharedresources-large.png#lightbox "樣式的控制項")

雖然它是最常用來定義`Resources`集合頂端的頁面上，請記住，`Resources`屬性由定義`VisualElement`，而且可以有`Resources`在頁面上的其他項目上的集合。 例如，再次嘗試新增一個`StackLayout`在此範例中：

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

您會發現按鈕的文字色彩現在是藍色。 基本上，每當 XAML 剖析器遇到`StaticResource`標記延伸，它會搜尋視覺化樹狀結構，並使用第一個`ResourceDictionary`遇到包含該索引鍵。

最常見的資源字典中儲存的物件類型的其中一個方法是透過 Xamarin.Forms `Style`，其定義的屬性設定的集合。 文章中討論樣式[樣式](~/xamarin-forms/user-interface/styles/index.md)。

有時 xaml 新的開發人員不知道他們可以將放視覺項目例如`Label`或`Button`中`ResourceDictionary`。 雖然它一定可以，它不太合理。 目的`ResourceDictionary`是共用物件。 無法共用視覺項目。 相同的執行個體不能出現在單一頁面上的兩倍。

## <a name="the-xstatic-markup-extension"></a>X:static 標記延伸

儘管其名稱，則相似性`x:Static`和`StaticResource`非常不同。 `StaticResource` 傳回的物件從資源字典時`x:Static`存取下列其中之一：

- 公用靜態欄位
- 公用靜態屬性
- 公用常數欄位 
- 列舉的成員。 

`StaticResource`標記延伸支援 XAML 實作所定義的資源字典，雖然`x:Static`屬於內建的 XAML，做為`x`前置詞會顯示。

以下是幾個範例會示範如何`x:Static`可以明確地參考靜態欄位和列舉成員：

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="{x:Static LayoutOptions.Start}"
       HorizontalTextAlignment="{x:Static TextAlignment.Center}"
       TextColor="{x:Static Color.Aqua}" />
```

目前為止，這不是很令人驚嘆。 但`x:Static`標記延伸也可以參考靜態欄位或屬性中自己的程式碼。 例如，以下是`AppConstants`類別，其中包含一些您可能想要使用整個應用程式的多個頁面上的靜態欄位：

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

若要參考這個類別的 XAML 檔案中的靜態欄位，您必須使用某種方式來表示此檔案所在的 XAML 檔案中。 您使用 XML 命名空間宣告。

前文提過標準 Xamarin.Forms XAML 範本的過程中建立的 XAML 檔案包含兩個 XML 命名空間宣告： 一個用於存取 Xamarin.Forms 類別和另一個用於參考標記和屬性對 XAML 內建函式：

```csharp
xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
```

您將需要額外的 XML 命名空間宣告來存取其他類別。 每個額外的 XML 命名空間宣告會定義新的前置詞。 若要存取 PCL 中，共用應用程式的本機的類別，例如`AppConstants`，XAML 程式設計人員通常會使用前置詞`local`。 命名空間宣告必須指出 CLR (Common Language Runtime) 命名空間名稱，也就是.NET 命名空間名稱，這就是出現在 C# 中的名稱`namespace`定義或`using`指示詞：

```csharp
xmlns:local="clr-namespace:XamlSamples"
```

您也可以定義 PCL 參考任何組件中的.NET 命名空間的 XML 命名空間宣告。 例如，以下是`sys`前置詞為標準.net`System`命名空間中的**mscorlib**組件，這一次名人的 「 Microsoft 通用物件執行階段程式庫，"，但現在表示 「 多語系標準一般物件執行階段程式庫。 」 因為這是另一個組件，您也必須指定組件名稱，在此情況下**mscorlib**:

```csharp
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

請注意，關鍵字`clr-namespace`後面接著冒號及.NET 命名空間名稱，後面接著一個分號，關鍵字`assembly`，等號，以及組件名稱。

冒號後面的是，`clr-namespace`但是等號遵循`assembly`。 刻意方式定義在這個語法： 最 XML 命名空間宣告會參考 URI，例如開始 URI 配置名稱`http`，這一律後面接上冒號。 `clr-namespace`這個字串的一部分要模擬的慣例。

這些命名空間宣告中包含**StaticConstantsPage**範例。 請注意，`BoxView`維度會設為`Math.PI`和`Math.E`，但已縮放的 100 倍：

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

結果的大小`BoxView`與相對螢幕會因平台而異：

 [![](xaml-markup-extensions-images/staticconstants.png "控制項使用 X:static 標記延伸")](xaml-markup-extensions-images/staticconstants-large.png#lightbox "控制項使用 X:static 標記延伸")

## <a name="other-standard-markup-extensions"></a>其他標準的標記延伸

數個標記延伸都內建到 XAML，和支援 Xamarin.Forms XAML 檔中。 其中有些不會經常使用，但是需要它們時是不可或缺：

-  如果屬性有非`null`值由預設值，而您想要將它設定為`null`，將它設定為`{x:Null}`標記延伸。
-  如果屬性的型別`Type`，您可以將它指派給`Type`物件使用標記延伸`{x:Type someClass}`。
-  您可以定義在 XAML 中使用的陣列`x:Array`標記延伸。 這個標記延伸具有必要的屬性，名為`Type`表示陣列中項目的類型。
- `Binding`標記延伸述[第 4 部分。資料繫結的基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)。

## <a name="the-constraintexpression-markup-extension"></a>ConstraintExpression 標記延伸

標記延伸可以有屬性，但它們不設定類似 XML 屬性。 在標記延伸，屬性設定為以逗號分隔，而且沒有引號會出現在大括號。

這可使用名為 Xamarin.Forms 標記延伸說明`ConstraintExpression`，能在`RelativeLayout`類別。 您可以指定的位置或子檢視的大小為常數，或相對於父代或其他具名的檢視。 語法`ConstraintExpression`可讓您設定的位置或大小檢視使用`Factor`屬性的另一個檢視，再加上一次`Constant`。 比這個更複雜的任何項目需要程式碼。

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

此範例中，您應該採取最重要的課程或許是標記延伸語法： 沒有引號必須出現在標記延伸的大括號。 輸入標記延伸在 XAML 檔案中，時，自然想要以引號括住屬性值。 抵抗誘惑 ！

以下是執行的程式：

[![](xaml-markup-extensions-images/relativelayout.png "使用條件約束的相對配置")](xaml-markup-extensions-images/relativelayout-large.png#lightbox "相對的版面配置使用條件約束")

## <a name="summary"></a>總結

XAML 標記延伸，如下所示的 XAML 檔案，提供重要的支援。 可能是最有價值的 XAML 標記延伸，但是`Binding`，其說明本系列的下一個部分[第 4 部分。資料繫結的基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)。



## <a name="related-links"></a>相關連結

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [第 1 部分：開始使用 XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [第 2 部分：基本 XAML 語法](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [第 4 部分：資料繫結的基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [第 5 部分：從資料繫結至 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
