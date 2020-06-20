---
title: XAML 中的泛型 Xamarin.Forms
description: Xamarin.FormsXAML 藉由將泛型條件約束指定為類型引數，來提供取用泛型 CLR 類型的支援。
ms.prod: xamarin
ms.assetid: 97B73048-4F90-41AD-AB48-8EB804C4998B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5a033e5feeefc41b97be29491a70632e767aa1b4
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84565196"
---
# <a name="generics-in-xamarinforms-xaml"></a>XAML 中的泛型 Xamarin.Forms

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-generics/)

Xamarin.FormsXAML 藉由將泛型條件約束指定為類型引數，來提供取用泛型 CLR 類型的支援。 這項支援是由指示詞所提供，該指示詞會將 `x:TypeArguments` 泛型的條件約束類型引數傳遞至泛型型別的函式。

> [!IMPORTANT]
> 不支援使用指示詞在 XAML 中定義泛型類別 Xamarin.Forms `x:TypeArguments` 。

型別引數會指定為字串，而且通常會加上前置詞，例如 `sys:String` 和 `sys:Int32` 。 必須加上前置項，因為一般的 CLR 泛型條件約束類型來自未對應至預設命名空間的程式庫 Xamarin.Forms 。 不過，XAML 2009 內建類型（例如 `x:String` 和 `x:Int32` ）也可以指定為類型引數，其中 `x` 是 XAML 2009 的 xaml 語言命名空間。 如需 XAML 2009 內建類型的詳細資訊，請參閱[xaml 2009 語言基本](/dotnet/desktop-wpf/xaml-services/types-for-primitives#xaml-2009-language-primitives)專案。

您可以使用逗號分隔符號來指定多個類型引數。 此外，如果泛型條件約束使用泛型型別，則嵌套的條件約束類型引數應該包含在括弧中。

> [!NOTE]
> `x:Type`標記延伸模組會提供泛型型別的 CLR 類型參考，並 `typeof` 在 c # 中具有與運算子類似的功能。 如需詳細資訊，請參閱[x:Type 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#xtype-markup-extension)。

## <a name="single-primitive-type-argument"></a>單一基本類型引數

您可以使用指示詞，將單一基本類型引數指定為前置字串引數 `x:TypeArguments` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:scg="clr-namespace:System.Collections.Generic;assembly=netstandard"
             ...>
    <CollectionView>
        <CollectionView.ItemsSource>
            <scg:List x:TypeArguments="x:String">
                <x:String>Baboon</x:String>
                <x:String>Capuchin Monkey</x:String>
                <x:String>Blue Monkey</x:String>
                <x:String>Squirrel Monkey</x:String>
                <x:String>Golden Lion Tamarin</x:String>
                <x:String>Howler Monkey</x:String>
                <x:String>Japanese Macaque</x:String>
            </scg:List>
        </CollectionView.ItemsSource>
    </CollectionView>
</ContentPage>
```

在此範例中， `System.Collections.Generic` 會定義為 `scg` XAML 命名空間。 `CollectionView.ItemsSource`屬性會 `List<T>` `string` 使用 XAML 2009 內建類型，設定為以類型引數具現化的 `x:String` 。 `List<string>`集合會使用多個專案進行初始化 `string` 。

或者，您也 `List<T>` 可以使用 CLR 類型來具現化集合 `String` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:scg="clr-namespace:System.Collections.Generic;assembly=netstandard"
             xmlns:sys="clr-namespace:System;assembly=netstandard"
             ...>
    <CollectionView>
        <CollectionView.ItemsSource>
            <scg:List x:TypeArguments="sys:String">
                <sys:String>Baboon</sys:String>
                <sys:String>Capuchin Monkey</sys:String>
                <sys:String>Blue Monkey</sys:String>
                <sys:String>Squirrel Monkey</sys:String>
                <sys:String>Golden Lion Tamarin</sys:String>
                <sys:String>Howler Monkey</sys:String>
                <sys:String>Japanese Macaque</sys:String>
            </scg:List>
        </CollectionView.ItemsSource>
    </CollectionView>
</ContentPage>
```

## <a name="single-object-type-argument"></a>單一物件類型引數

您可以使用指示詞，將單一物件類型引數指定為前置字串引數 `x:TypeArguments` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:models="clr-namespace:GenericsDemo.Models"
             xmlns:scg="clr-namespace:System.Collections.Generic;assembly=netstandard"
             ...>
    <CollectionView>
        <CollectionView.ItemsSource>
            <scg:List x:TypeArguments="models:Monkey">
                <models:Monkey Name="Baboon"
                               Location="Africa and Asia"
                               ImageUrl="https://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg" />
                <models:Monkey Name="Capuchin Monkey"
                               Location="Central and South America"
                               ImageUrl="https://upload.wikimedia.org/wikipedia/commons/thumb/4/40/Capuchin_Costa_Rica.jpg/200px-Capuchin_Costa_Rica.jpg" />
                <models:Monkey Name="Blue Monkey"
                               Location="Central and East Africa"
                               ImageUrl="https://upload.wikimedia.org/wikipedia/commons/thumb/8/83/BlueMonkey.jpg/220px-BlueMonkey.jpg" />
            </scg:List>
        </CollectionView.ItemsSource>
        <CollectionView.ItemTemplate>
            <DataTemplate>
                <Grid Padding="10">
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto" />
                        <RowDefinition Height="Auto" />
                    </Grid.RowDefinitions>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition Width="Auto" />
                        <ColumnDefinition Width="Auto" />
                    </Grid.ColumnDefinitions>
                    <Image Grid.RowSpan="2"
                           Source="{Binding ImageUrl}"
                           Aspect="AspectFill"
                           HeightRequest="60"
                           WidthRequest="60" />
                    <Label Grid.Column="1"
                           Text="{Binding Name}"
                           FontAttributes="Bold" />
                    <Label Grid.Row="1"
                           Grid.Column="1"
                           Text="{Binding Location}"
                           FontAttributes="Italic"
                           VerticalOptions="End" />
                </Grid>
            </DataTemplate>
        </CollectionView.ItemTemplate>
    </CollectionView>
</ContentPage>
```

在此範例中， `GenericsDemo.Models` 會定義為 `models` xaml 命名空間，並 `System.Collections.Generic` 定義為 `scg` xaml 命名空間。 `CollectionView.ItemsSource`屬性會設定為以 `List<T>` 類型引數具現化的 `Monkey` 。 `List<Monkey>`集合會使用多個專案進行初始化 `Monkey` ，而 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 定義每個物件外觀的會 `Monkey` 設定為 `ItemTemplate` 的 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 。

## <a name="multiple-type-arguments"></a>多個類型引數

您可以使用指示詞，將多個類型引數指定為加上前置詞的字串引數（以逗號分隔） `x:TypeArguments` 。 當泛型條件約束使用泛型型別時，嵌套的條件約束類型引數會包含在括弧內：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:models="clr-namespace:GenericsDemo.Models"
             xmlns:scg="clr-namespace:System.Collections.Generic;assembly=netstandard"
             ...>
    <CollectionView>
        <CollectionView.ItemsSource>
            <scg:List x:TypeArguments="scg:KeyValuePair(x:String,models:Monkey)">
                <scg:KeyValuePair x:TypeArguments="x:String,models:Monkey">
                    <x:Arguments>
                        <x:String>Baboon</x:String>
                        <models:Monkey Location="Africa and Asia"
                                       ImageUrl="https://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg" />
                    </x:Arguments>
                </scg:KeyValuePair>
                <scg:KeyValuePair x:TypeArguments="x:String,models:Monkey">
                    <x:Arguments>
                        <x:String>Capuchin Monkey</x:String>
                        <models:Monkey Location="Central and South America"
                                       ImageUrl="https://upload.wikimedia.org/wikipedia/commons/thumb/4/40/Capuchin_Costa_Rica.jpg/200px-Capuchin_Costa_Rica.jpg" />   
                    </x:Arguments>
                </scg:KeyValuePair>
                <scg:KeyValuePair x:TypeArguments="x:String,models:Monkey">
                    <x:Arguments>
                        <x:String>Blue Monkey</x:String>
                        <models:Monkey Location="Central and East Africa"
                                       ImageUrl="https://upload.wikimedia.org/wikipedia/commons/thumb/8/83/BlueMonkey.jpg/220px-BlueMonkey.jpg" />
                    </x:Arguments>
                </scg:KeyValuePair>
            </scg:List>
        </CollectionView.ItemsSource>
        <CollectionView.ItemTemplate>
            <DataTemplate>
                <Grid Padding="10">
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto" />
                        <RowDefinition Height="Auto" />
                    </Grid.RowDefinitions>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition Width="Auto" />
                        <ColumnDefinition Width="Auto" />
                    </Grid.ColumnDefinitions>
                    <Image Grid.RowSpan="2"
                           Source="{Binding Value.ImageUrl}"
                           Aspect="AspectFill"
                           HeightRequest="60"
                           WidthRequest="60" />
                    <Label Grid.Column="1"
                           Text="{Binding Key}"
                           FontAttributes="Bold" />
                    <Label Grid.Row="1"
                           Grid.Column="1"
                           Text="{Binding Value.Location}"
                           FontAttributes="Italic"
                           VerticalOptions="End" />
                </Grid>
            </DataTemplate>
        </CollectionView.ItemTemplate>
    </CollectionView>
</ContentPage    
```

在此範例中， `GenericsDemo.Models` 會定義為 `models` xaml 命名空間，並 `System.Collections.Generic` 定義為 `scg` xaml 命名空間。 `CollectionView.ItemsSource`屬性會設定為 `List<T>` 使用條件約束具現化的 `KeyValuePair<TKey, TValue>` ，並具有內部條件約束類型引數 `string` 和 `Monkey` 。 `List<KeyValuePair<string,Monkey>>`集合會使用非預設的處理常式，以多個專案進行初始化 `KeyValuePair` ， `KeyValuePair` 而 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 定義每個物件外觀的會 `Monkey` 設定為 `ItemTemplate` 的 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 。 如需將引數傳遞至非預設的函式的詳細資訊，請參閱[傳遞函數引數](~/xamarin-forms/xaml/passing-arguments.md#passing-constructor-arguments)。

## <a name="related-links"></a>相關連結

- [XAML 中的泛型（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-generics/)
- [XAML 2009 語言基本類型](/dotnet/desktop-wpf/xaml-services/types-for-primitives#xaml-2009-language-primitives)
- [x:Type 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#xtype-markup-extension)
- [傳遞函數引數](~/xamarin-forms/xaml/passing-arguments.md#passing-constructor-arguments)
