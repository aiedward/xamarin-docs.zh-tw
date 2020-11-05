---
title: 建立 Xamarin.Forms DataTemplate
description: 資料範本可以內嵌方式建立在 ResourceDictionary 中，或是從自訂類型或適當的資料 Xamarin.Forms 格類型建立。 本文將探索各種方法。
ms.prod: xamarin
ms.assetid: CFF4AB5E-9069-461C-84D8-F9F6C38510AB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1da087a8906eb87b14f015371a584b164a8a9340
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373025"
---
# <a name="creating-a-no-locxamarinforms-datatemplate"></a>建立 Xamarin.Forms DataTemplate

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/templates-datatemplates)

_資料範本可以內嵌方式建立在 ResourceDictionary 中，或是從自訂類型或適當的資料 Xamarin.Forms 格類型建立。本文將探討每項技巧。_

的常見使用案例 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 是在中顯示物件集合的資料 [`ListView`](xref:Xamarin.Forms.ListView) 。 您 [`ListView`](xref:Xamarin.Forms.ListView) 可以藉由將屬性設定為，來管理中每個資料格的資料外觀 [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。 您可以使用一些方法來完成此作業：

- [建立內嵌 DataTemplate](#creating-an-inline-datatemplate)。
- [建立具有類型的 DataTemplate](#creating-a-datatemplate-with-a-type)。
- 將[DataTemplate 建立為資源](#creating-a-datatemplate-as-a-resource)。

無論使用哪一種技術，結果都是由定義的每個資料格的外觀 [`ListView`](xref:Xamarin.Forms.ListView) [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ，如下列螢幕擷取畫面所示：

![具有 DataTemplate 的 ListView](creating-images/data-template-appearance.png)

## <a name="creating-an-inline-datatemplate"></a>建立內嵌 DataTemplate

[`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1)屬性可以設定為內嵌 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。 如果不需要在他處重複使用資料範本，則應該使用以適當控制項屬性直接子系形式放置的內嵌範本。 在 `DataTemplate` 中指定的項目會定義每個資料格的外觀，如下列 XAML 程式碼範例所示：

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

內嵌的子系 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 必須是或衍生自類型 [`Cell`](xref:Xamarin.Forms.Cell) 。 這個範例會使用 [`ViewCell`](xref:Xamarin.Forms.ViewCell) 衍生自的 `Cell` 。 中的版面配置 `ViewCell` 是在這裡由管理 [`Grid`](xref:Xamarin.Forms.Grid) 。 `Grid`包含三個 [`Label`](xref:Xamarin.Forms.Label) 實例，可將其屬性系結 [`Text`](xref:Xamarin.Forms.Label.Text) 至集合中每個物件的適當屬性 `Person` 。

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

在 c # 中， [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 會使用指定引數的函數多載來建立內嵌 `Func` 。

## <a name="creating-a-datatemplate-with-a-type"></a>使用類型建立 DataTemplate

[`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1)屬性也可以設定為 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 從資料格類型建立的。 使用這種方法的優點為，資料格類型所定義的外觀可供多個資料範本重複用於整個應用程式。 下列 XAML 程式碼顯示此方法的範例：

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

在此， [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) 屬性會設定為從定義資料 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 格外觀的自訂類型建立。 自訂類型必須衍生自類型 [`ViewCell`](xref:Xamarin.Forms.ViewCell) ，如下列程式碼範例所示：

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

在中 [`ViewCell`](xref:Xamarin.Forms.ViewCell) ，版面配置是在這裡由進行管理 [`Grid`](xref:Xamarin.Forms.Grid) 。 `Grid`包含三個 [`Label`](xref:Xamarin.Forms.Label) 實例，可將其屬性系結 [`Text`](xref:Xamarin.Forms.Label.Text) 至集合中每個物件的適當屬性 `Person` 。

對等的 C# 程式碼會顯示在下列範例中：

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

在 c # 中， [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 是使用會將資料格類型指定為引數的函式多載所建立。 資料格類型必須衍生自類型 [`ViewCell`](xref:Xamarin.Forms.ViewCell) ，如下列程式碼範例所示：

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
> 請注意， Xamarin.Forms 也包括可以用來在資料格中顯示簡單資料的資料格類型 [`ListView`](xref:Xamarin.Forms.ListView) 。 如需詳細資訊，請參閱[資料格外觀](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)。

## <a name="creating-a-datatemplate-as-a-resource"></a>將 DataTemplate 建立為資源

您也可以在中，將資料範本建立為可重複使用的物件 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 。 做法是為每個宣告提供唯一的 `x:Key` 屬性，這會在 `ResourceDictionary` 中為它提供描述性索引鍵，如下列 XAML 程式碼範例所示：

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

[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) 使用標記延伸將指派給屬性 `StaticResource` 。 請注意，雖然 `DataTemplate` 是在頁面的中定義 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) ，但也可以在控制項層級或應用層級定義。

下列程式碼範例顯示 C# 中的對等頁面：

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

[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)會使用方法加入至 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) [`Add`](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object)) ，這會指定在抓取 `Key` 時用來參考的字串 `DataTemplate` 。

## <a name="summary"></a>總結

本文已說明如何從自訂類型或中的建立資料範本（內嵌） [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 。 如果不需要在他處重複使用資料範本，則應該使用內嵌範本。 或者，您可以將資料範本定義為自訂類型，或是定義為控制項層級、頁面層級或應用程式層級資源，來重複使用它。

## <a name="related-links"></a>相關連結

- [儲存格外觀](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [Data Templates (Samples)](/samples/xamarin/xamarin-forms-samples/templates-datatemplates) (資料範本 (範例))
- [DataTemplate](xref:Xamarin.Forms.DataTemplate)