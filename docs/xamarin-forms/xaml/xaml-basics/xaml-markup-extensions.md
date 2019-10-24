---
title: 第 3 部分： XAML 標記延伸
description: XAML 標記延伸是 XAML 中的一項重要功能，可讓您將屬性設定為間接從其他來源參考的物件或值。
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F4A37564-B18B-42FF-B841-9A1949895AB6
author: davidbritch
ms.author: dabritch
ms.date: 03/27/2018
ms.openlocfilehash: 1321f08cba4bf4cf23759ebb179a603b544ffc2e
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696655"
---
# <a name="part-3-xaml-markup-extensions"></a>第 3 部分： XAML 標記延伸

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_XAML 標記延伸是 XAML 中的一項重要功能，可讓您將屬性設定為間接從其他來源參考的物件或值。XAML 標記延伸對於共用物件和參考整個應用程式所使用的常數而言特別重要，但是它們在資料系結中發現其最大的公用程式。_

## <a name="xaml-markup-extensions"></a>XAML 標記延伸

一般而言，您會使用 XAML 將物件的屬性設定為明確的值，例如字串、數位、列舉成員，或轉換成幕後的值的字串。

不過，有時候屬性必須改為參考在其他位置定義的值，或者，在執行時間，程式碼可能需要少量處理。 基於這些目的，XAML*標記延伸*模組可供使用。

這些 XAML 標記延伸不是 XML 的延伸。 XAML 完全是合法的 XML。 它們稱為「延伸模組」，因為它們是由實 `IMarkupExtension` 的類別中的程式碼所支援。 您可以撰寫自己的自訂標記延伸模組。

在許多情況下，xaml 檔案中的 XAML 標記延伸模組會立即被辨識，因為它們會顯示為以大括弧分隔的屬性設定： {和}，但有時標記延伸會在標記中顯示為傳統元素。

## <a name="shared-resources"></a>共用資源

某些 XAML 頁面包含數個將屬性設為相同值的視圖。 例如，這些 `Button` 物件的許多屬性設定都相同：

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

如果其中一個屬性需要變更，您可能只想要變更一次，而不是三次。 如果這是程式碼，您可能會使用常數和靜態唯讀物件，協助您將這類值保持一致且容易修改。

在 XAML 中，其中一個常用的解決方案是將這類值或物件儲存在*資源字典*中。 @No__t_0 類別會定義名為 `Resources` 類型為 `ResourceDictionary` 的屬性，這是具有類型 `string` 和類型 `object` 值的字典。 您可以將物件放入這個字典中，然後從標記中參考它們，全部都在 XAML 中。

若要在頁面上使用資源字典，請包含一對 `Resources` 的屬性元素標記。 最方便的方式是將這些放在頁面頂端：

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

也必須明確包含 `ResourceDictionary` 標記：

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

現在，您可以將各種類型的物件和值加入至資源字典。 這些類型必須是可具現化。 例如，它們不能是抽象類別。 這些類型也必須有公用無參數的函式。 每個專案都需要以 `x:Key` 屬性指定的字典索引鍵。 例如:

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

這兩個專案是結構類型 `LayoutOptions` 的值，而且每個都有一個唯一索引鍵和一個或兩個屬性集。 在程式碼和標記中，使用 `LayoutOptions` 的靜態欄位更為常見，但在此情況下，設定屬性會比較方便。

現在，您必須將這些按鈕的 [`HorizontalOptions`] 和 [`VerticalOptions` 屬性] 設定為這些資源，並使用 `StaticResource` 的 XAML 標記延伸來完成：

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="3"
        Rotation="-15"
        TextColor="Red"
        FontSize="24" />
```

@No__t_0 標記延伸一律以大括弧分隔，並包含字典索引鍵。

名稱 `StaticResource` 將它與 Xamarin 也支援的 `DynamicResource` 區別。 `DynamicResource` 適用于與執行時間中可能會變更的值相關聯的字典索引鍵，而 `StaticResource` 只會在結構化頁面上的元素時，從字典存取元素一次。

針對 `BorderWidth` 屬性，必須在字典中儲存 double。 XAML 可方便地定義常見資料類型（例如 `x:Double` 和 `x:Int32` 的標記）：

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

您不需要將它放在三行。 這個旋轉角度的這個字典專案只會佔用一行：

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

您可以用與 `LayoutOptions` 值相同的方式來參考這兩個資源：

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="{StaticResource borderWidth}"
        Rotation="{StaticResource rotationAngle}"
        TextColor="Red"
        FontSize="24" />
```

對於 `Color` 類型的資源，您可以使用直接指派這些類型的屬性時所使用的相同字串表示。 建立資源時，會叫用類型轉換器。 以下是 `Color` 類型的資源：

```xaml
<Color x:Key="textColor">Red</Color>
```

程式通常會將 `FontSize` 屬性設定為 `NamedSize` 列舉的成員，例如 `Large`。 @No__t_0 類別會在幕後運作，使用 `Device.GetNamedSized` 方法將它轉換成平臺相依值。 不過，在定義字型大小資源時，使用數值會比較合理，如下所示 `x:Double` 類型：

```xaml
<x:Double x:Key="fontSize">24</x:Double>
```

現在，`Text` 以外的所有屬性都是由資源設定所定義：

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="{StaticResource borderWidth}"
        Rotation="{StaticResource rotationAngle}"
        TextColor="{StaticResource textColor}"
        FontSize="{StaticResource fontSize}" />
```

您也可以在資源字典中使用 `OnPlatform`，為平臺定義不同的值。 以下是 `OnPlatform` 物件可以是不同文字色彩之資源字典的一部分：

```xaml
<OnPlatform x:Key="textColor"
            x:TypeArguments="Color">
    <On Platform="iOS" Value="Red" />
    <On Platform="Android" Value="Aqua" />
    <On Platform="UWP" Value="#80FF80" />
</OnPlatform>
```

請注意，`OnPlatform` 會同時取得 `x:Key` 屬性，因為它是字典中的物件，也是 `x:TypeArguments` 屬性，因為它是泛型類別。 當物件初始化時，`iOS`、`Android` 和 `UWP` 屬性會轉換成 `Color` 值。

以下是最後一個完整的 XAML 檔案，其中有三個按鈕存取六個共用值：

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

            <x:Double x:Key="fontSize">24</x:Double>
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

螢幕擷取畫面會驗證一致的樣式，以及平臺相依的樣式：

[![Styled 控制項](xaml-markup-extensions-images/sharedresources.png)](xaml-markup-extensions-images/sharedresources-large.png#lightbox)

雖然在頁面頂端定義 `Resources` 集合是最常見的，但請記住，`Resources` 屬性是由 `VisualElement` 所定義，而您可以在頁面上的其他元素上擁有 `Resources` 集合。 例如，請嘗試在此範例中加入一個 `StackLayout`：

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

您會發現按鈕的文字色彩現在是藍色的。 基本上，每當 XAML 剖析器遇到 `StaticResource` 標記延伸時，它就會搜尋視覺化樹狀結構，並使用第一個遇到包含該索引鍵的 `ResourceDictionary`。

儲存在資源字典中的其中一個最常見的物件類型是 [Xamarin] `Style`，它會定義屬性設定的集合。 樣式會在文章[樣式](~/xamarin-forms/user-interface/styles/index.md)中討論。

有時候，開發人員會知道 XAML 是否可以將視覺元素（例如 `Label` 或 `Button` 放在 `ResourceDictionary` 中。 雖然這是可行的，但它並沒有太大的意義。 @No__t_0 的目的是要共用物件。 視覺元素無法共用。 相同的實例不能在單一頁面上出現兩次。

## <a name="the-xstatic-markup-extension"></a>X:Static 標記延伸

雖然其名稱的相似處，`x:Static` 和 `StaticResource` 非常不同。 `StaticResource` 會從資源字典傳回物件，而 `x:Static` 存取下列其中一項：

- 公用靜態欄位
- 公用靜態屬性
- 公用常數位段
- 列舉成員。

定義資源字典的 XAML 執行會支援 `StaticResource` 標記延伸，而 `x:Static` 是 XAML 內建的部分，因為 `x` 前置詞會顯示。

以下幾個範例示範 `x:Static` 如何明確參考靜態欄位和列舉成員：

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="{x:Static LayoutOptions.Start}"
       HorizontalTextAlignment="{x:Static TextAlignment.Center}"
       TextColor="{x:Static Color.Aqua}" />
```

到目前為止，這並沒有什麼印象。 但是 `x:Static` 標記延伸也可以從您自己的程式碼參考靜態欄位或屬性。 例如，以下是一個 `AppConstants` 類別，其中包含一些您可能想要在整個應用程式的多個頁面上使用的靜態欄位：

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

若要在 XAML 檔案中參考這個類別的靜態欄位，您需要某種方式來表示此檔案所在的 XAML 檔案中。 您可以使用 XML 命名空間宣告來執行此動作。

回想一下，做為標準 Xamarin. form XAML 範本之一部分的 XAML 檔案包含兩個 XML 命名空間宣告：一個用於存取 Xamarin. Forms 類別，另一個則用於參考 XAML 內建的標記和屬性：

```csharp
xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
```

您將需要額外的 XML 命名空間宣告，才能存取其他類別。 每個額外的 XML 命名空間宣告會定義新的前置詞。 若要存取共用應用程式的本機類別 .NET Standard 程式庫（例如 `AppConstants`），XAML 程式設計人員通常會使用前置詞 `local`。 命名空間宣告必須指出 CLR （Common Language Runtime）命名空間名稱（也稱為 .NET 命名空間名稱），這是出現在C# `namespace` 定義或 `using` 指示詞中的名稱：

```csharp
xmlns:local="clr-namespace:XamlSamples"
```

您也可以在 .NET Standard 程式庫所參考的任何元件中，定義 .NET 命名空間的 XML 命名空間宣告。 例如，以下是標準 .NET `System` 命名空間的 `sys` 前置詞，它位於**mscorlib**元件中，這是在勇敢面對考驗「Microsoft 通用物件執行時間程式庫」之後，現在表示「多語系標準通用物件執行時間程式庫」。 因為這是另一個元件，所以您也必須指定元件名稱，在此案例中為**mscorlib**：

```csharp
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

請注意，關鍵字 `clr-namespace` 後面接著冒號和 .NET 命名空間名稱，後面接著分號、關鍵字 `assembly`、等號和元件名稱。

是，`clr-namespace` 後面加上冒號，`assembly`。 此語法是刻意定義的：大部分的 XML 命名空間宣告都會參考開始 URI 配置名稱的 URI，例如 `http`，其後面一律會接著冒號。 這個字串的 `clr-namespace` 部分主要是用來模擬該慣例。

這兩個命名空間宣告都包含在**StaticConstantsPage**範例中。 請注意，`BoxView` 維度會設定為 `Math.PI` 和 `Math.E`，但調整為100的係數：

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

相對於螢幕之結果 `BoxView` 的大小與平臺相關：

[使用 x:Static 標記延伸 ![Controls](xaml-markup-extensions-images/staticconstants.png)](xaml-markup-extensions-images/staticconstants-large.png#lightbox)

## <a name="other-standard-markup-extensions"></a>其他標準標記延伸模組

XAML 內建數個標記延伸模組，而且在 Xamarin. 表單 XAML 檔案中支援。 其中有些不常使用，但在您需要時很重要：

- 如果屬性預設為非 `null` 的值，但您想要將它設定為 `null`，請將它設定為 `{x:Null}` 標記延伸。
- 如果屬性的類型是 `Type`，您可以使用標記延伸 `{x:Type someClass}` 將它指派給 `Type` 物件。
- 您可以使用 `x:Array` 標記延伸，在 XAML 中定義陣列。 此標記延伸具有名為 `Type` 的必要屬性，可指出陣列中元素的類型。
- @No__t_0 標記延伸模組會在[第4部分中討論。資料系結基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)。
- @No__t_0 標記延伸模組會在[相對](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md)系結中討論。

## <a name="the-constraintexpression-markup-extension"></a>ConstraintExpression 標記延伸

標記延伸可以有屬性，但不像 XML 屬性一樣設定。 在標記延伸中，屬性設定會以逗號分隔，且大括弧內不會顯示任何引號。

這可以使用名為 `ConstraintExpression` 的 Xamarin. Forms 標記延伸模組來說明，這會與 `RelativeLayout` 類別搭配使用。 您可以指定子視圖的位置或大小做為常數，或是相對於父系或其他已命名的 view。 @No__t_0 的語法可讓您使用另一個視圖的屬性 `Factor` 時間，再加上 `Constant`，來設定視圖的位置或大小。 比需要程式碼更複雜的東西。

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

您在此範例中最重要的課程可能是標記延伸的語法：標記延伸的大括弧內不能出現引號。 在 XAML 檔案中輸入標記延伸時，自然會想要將屬性的值括在引號中。 防禦誘惑！

以下是正在執行的程式：

[使用條件約束 ![Relative 版面配置](xaml-markup-extensions-images/relativelayout.png)](xaml-markup-extensions-images/relativelayout-large.png#lightbox)

## <a name="summary"></a>總結

此處顯示的 XAML 標記延伸提供 XAML 檔案的重要支援。 但或許最重要的 XAML 標記延伸是 `Binding`，在本系列的下一個部分會涵蓋[第4部分。資料系結基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)。

## <a name="related-links"></a>相關連結

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [第1部分。使用 XAML 消費者入門](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [第2部分。基本 XAML 語法](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [第4部分。資料系結基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [第5部分：從資料系結到 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
