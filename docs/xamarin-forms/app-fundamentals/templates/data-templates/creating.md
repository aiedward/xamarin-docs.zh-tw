---
title: 建立 Xamarin.Forms DataTemplate
description: 您可以在 ResourceDictionary 中，或是從自訂類型或適當的 Xamarin.Forms 資料格類型，以內嵌方式建立資料範本。 本文將探索各種方法。
ms.prod: xamarin
ms.assetid: CFF4AB5E-9069-461C-84D8-F9F6C38510AB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: 63f9bf82bc8e637aced1afa5d5699ac1e8dc3f8c
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994611"
---
# <a name="creating-a-xamarinforms-datatemplate"></a>建立 Xamarin.Forms DataTemplate

_您可以在 ResourceDictionary 中，或是從自訂類型或適當的 Xamarin.Forms 資料格類型，以內嵌方式建立資料範本。本文將探索各種方法。_

[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 的常見使用方式情節是在 [`ListView`](xref:Xamarin.Forms.ListView) 中顯示物件集合的資料。 將 [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) 屬性設定為 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)，即可管理 [`ListView`](xref:Xamarin.Forms.ListView) 中每個資料格的資料外觀。 您可以使用一些方法來完成此作業：

- [建立內嵌 DataTemplate](#inline)。
- [使用類型建立 DataTemplate](#type)。
- [將 DataTemplate 建立為資源](#resource)。

不論使用哪種方法，結果都是 [`ListView`](xref:Xamarin.Forms.ListView) 中每個資料格的外觀會由 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 定義，如下列螢幕擷取畫面所示：

![](creating-images/data-template-appearance.png "具有 DataTemplate 的 ListView")

<a name="inline" />

## <a name="creating-an-inline-datatemplate"></a>建立內嵌 DataTemplate

[`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) 屬性可以設定為內嵌 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)。 如果不需要在他處重複使用資料範本，則應該使用以適當控制項屬性直接子系形式放置的內嵌範本。 在 `DataTemplate` 中指定的項目會定義每個資料格的外觀，如下列 XAML 程式碼範例所示：

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

內嵌 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 的子系必須是 [`ViewCell`](xref:Xamarin.Forms.ViewCell) 類型或衍生自該類型。 `ViewCell` 內的配置在此處由 [`Grid`](xref:Xamarin.Forms.Grid) 管理。 `Grid` 包含三個 [`Label`](xref:Xamarin.Forms.Label) 執行個體，會將其 [`Text`](xref:Xamarin.Forms.Label.Text) 屬性繫結至集合中每個 `Person` 物件的適當屬性。

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

在 C# 中，內嵌 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 是使用指定 `Func` 引數的建構函式多載所建立。

<a name="type" />

## <a name="creating-a-datatemplate-with-a-type"></a>使用類型建立 DataTemplate

[`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) 屬性也可設定為從資料格類型建立的 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)。 使用這種方法的優點為，資料格類型所定義的外觀可供多個資料範本重複用於整個應用程式。 下列 XAML 程式碼顯示此方法的範例：

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

在此，[`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) 屬性會設定為從自訂類型建立並定義資料格外觀的 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)。 自訂類型必須衍生自 [`ViewCell`](xref:Xamarin.Forms.ViewCell) 類型，如下列程式碼範例所示：

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

[`ViewCell`](xref:Xamarin.Forms.ViewCell) 內的配置在此處由 [`Grid`](xref:Xamarin.Forms.Grid) 管理。 `Grid` 包含三個 [`Label`](xref:Xamarin.Forms.Label) 執行個體，會將其 [`Text`](xref:Xamarin.Forms.Label.Text) 屬性繫結至集合中每個 `Person` 物件的適當屬性。

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

在 C# 中，[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 是使用指定資料格類型作為引數的建構函式多載所建立。 資料格類型必須衍生自 [`ViewCell`](xref:Xamarin.Forms.ViewCell) 類型，如下列程式碼範例所示：

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
> 請注意，Xamarin.Forms 也會包含資料格類型，可用來在 [`ListView`](xref:Xamarin.Forms.ListView) 資料格中顯示簡單資料。 如需詳細資訊，請參閱[資料格外觀](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)。

<a name="resource" />

## <a name="creating-a-datatemplate-as-a-resource"></a>將 DataTemplate 建立為資源

您也可以將資料範本建立為 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 中可重複使用的物件。 做法是為每個宣告提供唯一的 `x:Key` 屬性，這會在 `ResourceDictionary` 中為它提供描述性索引鍵，如下列 XAML 程式碼範例所示：

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

使用 `StaticResource` 標記延伸，將 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 指派給 [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) 屬性。 請注意，雖然 `DataTemplate` 是在頁面的 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 中定義，但也可以在控制項層級或應用程式層級定義。

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

使用 [`Add`](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object)) 方法將 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 新增至 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)，這會指定擷取 `DataTemplate` 時用來參考它的 `Key` 字串。

## <a name="summary"></a>總結

本文說明了如何從自訂類型或在 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 中，以內嵌方式建立資料範本。 如果不需要在他處重複使用資料範本，則應該使用內嵌範本。 或者，您可以將資料範本定義為自訂類型，或是定義為控制項層級、頁面層級或應用程式層級資源，來重複使用它。


## <a name="related-links"></a>相關連結

- [儲存格外觀](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [Data Templates (Samples)](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplates/) (資料範本 (範例))
- [DataTemplate](xref:Xamarin.Forms.DataTemplate)
