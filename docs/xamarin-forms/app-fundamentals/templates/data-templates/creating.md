---
title: 建立 Xamarin.Forms DataTemplate
description: 在 ResourceDictionary，或從自訂型別或適當的 Xamarin.Forms 資料格類型，資料範本可以建立內嵌。 本文章探討每一種技巧。
ms.prod: xamarin
ms.assetid: CFF4AB5E-9069-461C-84D8-F9F6C38510AB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: 63f9bf82bc8e637aced1afa5d5699ac1e8dc3f8c
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994611"
---
# <a name="creating-a-xamarinforms-datatemplate"></a>建立 Xamarin.Forms DataTemplate

_在 ResourceDictionary，或從自訂型別或適當的 Xamarin.Forms 資料格類型，資料範本可以建立內嵌。本文章探討每一種技巧。_

常見的使用案例，如[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)顯示資料中的物件集合[ `ListView` ](xref:Xamarin.Forms.ListView)。 在每一個資料格資料的外觀[ `ListView` ](xref:Xamarin.Forms.ListView)可以藉由設定管理[ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1)屬性設[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)。 有幾個可用來完成這項作業的方法：

- [建立內嵌 DataTemplate](#inline)。
- [建立具有類型的 DataTemplate](#type)。
- [建立為資源 DataTemplate](#resource)。

不論所使用的技巧，結果是在每個資料格的外觀[ `ListView` ](xref:Xamarin.Forms.ListView)由此[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)，如下列螢幕擷取畫面所示：

![](creating-images/data-template-appearance.png "使用 DataTemplate 的 ListView")

<a name="inline" />

## <a name="creating-an-inline-datatemplate"></a>建立內嵌 DataTemplate

[ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1)屬性可以設定為內嵌[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)。 如果不需要重複使用資料範本其他位置，則應該使用內嵌範本，也就是會放置在適當的控制項屬性的直接子系的其中一個。 在指定的項目`DataTemplate`定義的每個資料格，外觀，如下列 XAML 程式碼範例所示：

```xaml
<ListView Margin="0,20,0,0">
    <ListView.ItemsSource>
        <x:Array Type="{x:Type local:Person}">
            <local:Person Name="Steve" Age="21" Location="USA" />
            <local:Person Name="John" Age="37" Location="USA" />
            <local:Person Name="Tom" Age="42" Location="UK" />
            <local:Person Name="Lucas" Age="29" Location="Germany" />
            <local:Person Name="Tariq" Age="39" Location="UK" />
            <local:Person Name="Jane" Age="30" Location="USA" />
        </x:Array>
    </ListView.ItemsSource>
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
                <Grid>
                    ...
                    <Label Text="{Binding Name}" FontAttributes="Bold" />
                    <Label Grid.Column="1" Text="{Binding Age}" />
                    <Label Grid.Column="2" Text="{Binding Location}" HorizontalTextAlignment="End" />
                </Grid>
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

內嵌的子系[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)必須是的或衍生自、 輸入[ `ViewCell` ](xref:Xamarin.Forms.ViewCell)。 內的版面配置`ViewCell`由以下[ `Grid` ](xref:Xamarin.Forms.Grid)。 `Grid`包含三種[ `Label` ](xref:Xamarin.Forms.Label)執行個體的繫結其[ `Text` ](xref:Xamarin.Forms.Label.Text)屬性，以適當的屬性，每個`Person`集合中的物件。

對等的 C# 程式碼會顯示在以下程式碼範例中：

```csharp
public class WithDataTemplatePageCS : ContentPage
{
    public WithDataTemplatePageCS()
    {
        ...
        var people = new List<Person>
        {
            new Person { Name = "Steve", Age = 21, Location = "USA" },
            ...
        };

        var personDataTemplate = new DataTemplate(() =>
        {
            var grid = new Grid();
            ...
            var nameLabel = new Label { FontAttributes = FontAttributes.Bold };
            var ageLabel = new Label();
            var locationLabel = new Label { HorizontalTextAlignment = TextAlignment.End };

            nameLabel.SetBinding(Label.TextProperty, "Name");
            ageLabel.SetBinding(Label.TextProperty, "Age");
            locationLabel.SetBinding(Label.TextProperty, "Location");

            grid.Children.Add(nameLabel);
            grid.Children.Add(ageLabel, 1, 0);
            grid.Children.Add(locationLabel, 2, 0);

            return new ViewCell { View = grid };
        });

        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children = {
                ...
                new ListView { ItemsSource = people, ItemTemplate = personDataTemplate, Margin = new Thickness(0, 20, 0, 0) }
            }
        };
    }
}
```

在 C# 中，內嵌[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)會使用指定的建構函式多載建立`Func`引數。

<a name="type" />

## <a name="creating-a-datatemplate-with-a-type"></a>建立具有類型的 DataTemplate

[ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1)屬性也設定為[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)所建立的資料格類型。 這種方法的優點是可以在整個應用程式的多個資料範本重複使用資料格類型所定義的外觀。 下列 XAML 程式碼顯示這種方法的範例：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataTemplates"
             ...>
    <StackLayout Margin="20">
        ...
        <ListView Margin="0,20,0,0">
           <ListView.ItemsSource>
                <x:Array Type="{x:Type local:Person}">
                    <local:Person Name="Steve" Age="21" Location="USA" />
                    ...
                </x:Array>
            </ListView.ItemsSource>
            <ListView.ItemTemplate>
                <DataTemplate>
                    <local:PersonCell />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

在這裡， [ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1)屬性設定為[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)所建立的儲存格的外觀會定義自訂類型。 自訂型別必須衍生自類型[ `ViewCell` ](xref:Xamarin.Forms.ViewCell)，如下列程式碼範例所示：

```xaml
<ViewCell xmlns="http://xamarin.com/schemas/2014/forms"
          xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
          x:Class="DataTemplates.PersonCell">
     <Grid>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="0.5*" />
            <ColumnDefinition Width="0.2*" />
            <ColumnDefinition Width="0.3*" />
        </Grid.ColumnDefinitions>
        <Label Text="{Binding Name}" FontAttributes="Bold" />
        <Label Grid.Column="1" Text="{Binding Age}" />
        <Label Grid.Column="2" Text="{Binding Location}" HorizontalTextAlignment="End" />
    </Grid>
</ViewCell>
```

內[ `ViewCell` ](xref:Xamarin.Forms.ViewCell)，版面配置由以下[ `Grid` ](xref:Xamarin.Forms.Grid)。 `Grid`包含三種[ `Label` ](xref:Xamarin.Forms.Label)執行個體的繫結其[ `Text` ](xref:Xamarin.Forms.Label.Text)屬性，以適當的屬性，每個`Person`集合中的物件。

對等的 C# 程式碼是由下列範例所示：

```csharp
public class WithDataTemplatePageFromTypeCS : ContentPage
{
    public WithDataTemplatePageFromTypeCS()
    {
        ...
        var people = new List<Person>
        {
            new Person { Name = "Steve", Age = 21, Location = "USA" },
            ...
        };

        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children = {
                ...
                new ListView { ItemTemplate = new DataTemplate(typeof(PersonCellCS)), ItemsSource = people, Margin = new Thickness(0, 20, 0, 0) }
            }
        };
    }
}
```

在 C# 中， [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)會使用指定的資料格類型做為引數的建構函式多載來建立。 資料格類型必須衍生自類型[ `ViewCell` ](xref:Xamarin.Forms.ViewCell)，如下列程式碼範例所示：

```csharp
public class PersonCellCS : ViewCell
{
    public PersonCellCS()
    {
        var grid = new Grid();
        ...
        var nameLabel = new Label { FontAttributes = FontAttributes.Bold };
        var ageLabel = new Label();
        var locationLabel = new Label { HorizontalTextAlignment = TextAlignment.End };

        nameLabel.SetBinding(Label.TextProperty, "Name");
        ageLabel.SetBinding(Label.TextProperty, "Age");
        locationLabel.SetBinding(Label.TextProperty, "Location");

        grid.Children.Add(nameLabel);
        grid.Children.Add(ageLabel, 1, 0);
        grid.Children.Add(locationLabel, 2, 0);

        View = grid;
    }
}
```

> [!NOTE]
> 請注意，Xamarin.Forms 也包含可用來顯示簡單的資料中的儲存格型別[ `ListView` ](xref:Xamarin.Forms.ListView)資料格。 如需詳細資訊，請參閱 <<c0> [ 儲存格的外觀](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)。

<a name="resource" />

## <a name="creating-a-datatemplate-as-a-resource"></a>DataTemplate 建立為資源

資料範本也可以為可重複使用物件來建立[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)。 做法是讓每個宣告的唯一`x:Key`屬性，它提供描述性的索引鍵中`ResourceDictionary`，如下列 XAML 程式碼範例所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             ...>
    <ContentPage.Resources>
        <ResourceDictionary>
            <DataTemplate x:Key="personTemplate">
                 <ViewCell>
                    <Grid>
                        ...
                    </Grid>
                </ViewCell>
            </DataTemplate>
        </ResourceDictionary>
    </ContentPage.Resources>
    <StackLayout Margin="20">
        ...
        <ListView ItemTemplate="{StaticResource personTemplate}" Margin="0,20,0,0">
            <ListView.ItemsSource>
                <x:Array Type="{x:Type local:Person}">
                    <local:Person Name="Steve" Age="21" Location="USA" />
                    ...
                </x:Array>
            </ListView.ItemsSource>
        </ListView>
    </StackLayout>
</ContentPage>
```

[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)指派給[ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1)屬性使用`StaticResource`標記延伸。 請注意，雖然`DataTemplate`頁面中定義[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)，它也可定義控制項的層級或應用程式層級。

下列程式碼範例顯示 C# 中的對等的頁面：

```csharp
public class WithDataTemplatePageCS : ContentPage
{
  public WithDataTemplatePageCS ()
  {
    ...
    var personDataTemplate = new DataTemplate (() => {
      var grid = new Grid ();
      ...
      return new ViewCell { View = grid };
    });

    Resources = new ResourceDictionary ();
    Resources.Add ("personTemplate", personDataTemplate);

    Content = new StackLayout {
      Margin = new Thickness(20),
      Children = {
        ...
        new ListView { ItemTemplate = (DataTemplate)Resources ["personTemplate"], ItemsSource = people };
      }
    };
  }
}
```

[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)加入至[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)使用[ `Add` ](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object))方法，以指定`Key`是用來的字串參考`DataTemplate`擷取它時。

## <a name="summary"></a>總結

這篇文章說明如何從自訂類型，或在建立資料範本，內嵌[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)。 如果不需要重複使用資料範本其他位置，則應該使用內嵌範本。 或者，可以藉由定義它，做為自訂類型，或做為控制層級、 頁面層級或應用程式層級資源重複資料範本。


## <a name="related-links"></a>相關連結

- [儲存格外觀](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [資料範本 （範例）](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplates/)
- [DataTemplate](xref:Xamarin.Forms.DataTemplate)
