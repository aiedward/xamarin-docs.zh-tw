---
title: 建立 DataTemplate
description: 在 ResourceDictionary，或從自訂類型或適當的 Xamarin.Forms 資料格類型，資料範本可以建立內嵌。 這份文件會探索每個技術。
ms.prod: xamarin
ms.assetid: CFF4AB5E-9069-461C-84D8-F9F6C38510AB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: cd4266fb8e7d68a9f93bd169ca70c6a0f516a357
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="creating-a-datatemplate"></a>建立 DataTemplate

_在 ResourceDictionary，或從自訂類型或適當的 Xamarin.Forms 資料格類型，資料範本可以建立內嵌。這份文件會探索每個技術。_

常見使用案例[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)顯示資料，集合中的物件[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)。 在每一個資料格資料的外觀[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)可以藉由設定管理[ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/)屬性[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)。 有一些技術可以用來完成這項作業：

- [建立內嵌 DataTemplate](#inline)。
- [建立具有類型 DataTemplate](#type)。
- [建立做為資源 DataTemplate](#resource)。

不論所使用的技巧，結果是每個儲存格的外觀[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)由定義[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)，如下列螢幕擷取畫面所示：

![](creating-images/data-template-appearance.png "使用 DataTemplate 的 ListView")

<a name="inline" />

## <a name="creating-an-inline-datatemplate"></a>建立內嵌 DataTemplate

[ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/)屬性可以設定為內嵌[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)。 如果不需要重複使用資料範本的其他位置，則應該使用內嵌範本，這是一個會放置在適當的控制項屬性的直接子系。 在指定的項目`DataTemplate`定義的每個資料格，外觀，如下列 XAML 程式碼範例所示：

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

內嵌的子系[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)必須是的或衍生自，輸入[ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/)。 配置內`ViewCell`受這裡[ `Grid` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/)。 `Grid`包含三種[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)執行個體的繫結其[ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/)屬性，以適當的屬性，每個`Person`集合中的物件。

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

在 C# 中，內嵌[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)會使用指定的建構函式多載建立`Func`引數。

<a name="type" />

## <a name="creating-a-datatemplate-with-a-type"></a>建立具有類型 DataTemplate

[ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/)屬性也設定為[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)所建立的儲存格類型。 這種方法的優點是可以在整個應用程式的多個資料範本的重複使用的儲存格類型所定義的外觀。 下列 XAML 程式碼顯示這種方法的範例：

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

在這裡， [ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/)屬性設定為[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)所建立之自訂類型的定義資料格的外觀。 自訂型別必須衍生自類型[ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/)，如下列程式碼範例所示：

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

內[ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/)，版面配置由這裡管理[ `Grid` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/)。 `Grid`包含三種[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)執行個體的繫結其[ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/)屬性，以適當的屬性，每個`Person`集合中的物件。

對等的 C# 程式碼如下列範例所示：

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

在 C# 中， [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)會建立使用指定的儲存格類型做為引數的建構函式多載。 資料格類型必須衍生自類型[ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/)，如下列程式碼範例所示：

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
> 請注意，Xamarin.Forms 也包含可以用來顯示簡單的資料中的資料格類型[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)資料格。 如需詳細資訊，請參閱[儲存格外觀](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)。

<a name="resource" />

## <a name="creating-a-datatemplate-as-a-resource"></a>為資源建立 DataTemplate

資料範本也可以建立為可重複使用物件[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)。 這藉由讓每個宣告的唯一`x:Key`屬性，提供描述性的索引鍵中`ResourceDictionary`，如下列 XAML 程式碼範例所示：

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

[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)指派給[ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/)屬性使用`StaticResource`標記延伸。 請注意，當`DataTemplate`定義頁面的[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)，也無法定義控制層級或應用程式層級。

下列程式碼範例顯示相同頁面在 C# 中：

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

[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)加入至[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)使用[ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ResourceDictionary.Add/p/System.String/System.Object/)方法，指定`Key`是用來的字串參考`DataTemplate`時擷取它。

## <a name="summary"></a>總結

本文說明如何從自訂類型，或建立內嵌、 資料範本[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)。 如果不需要重複使用資料範本的其他位置，則應該使用內嵌範本。 或者，資料範本可以重複使用定義為自訂類型，或控制層級頁面層級或應用程式層級資源。


## <a name="related-links"></a>相關連結

- [儲存格外觀](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [資料範本 （範例）](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplates/)
- [DataTemplate](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)
