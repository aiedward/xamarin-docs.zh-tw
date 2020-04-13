---
title: 建立 Xamarin.Forms DataTemplate
description: 您可以在 ResourceDictionary 中，或是從自訂類型或適當的 Xamarin.Forms 資料格類型，以內嵌方式建立資料範本。 本文將探索各種方法。
ms.prod: xamarin
ms.assetid: CFF4AB5E-9069-461C-84D8-F9F6C38510AB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: 399f411acd497b9d55ca81f670556430fe5f5503
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771292"
---
# <a name="creating-a-xamarinforms-datatemplate"></a>建立 Xamarin.Forms DataTemplate

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)

_數據範本可以內聯、資源字典或自定義類型或相應的 Xamarin.Forms 單元格類型創建。本文將探討每種技術。_

的常見使用機制[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)是顯示 來自中物件[`ListView`](xref:Xamarin.Forms.ListView)集合的資料 。 可以透過[`ListView`](xref:Xamarin.Forms.ListView)[`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1)設定屬性設定為管理中每個儲存格中的資料的[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)外觀 。 您可以使用一些方法來完成此作業：

- [建立內嵌 DataTemplate](#inline)。
- [使用類型 建立資料樣本](#type)。
- [將資料樣本建立為資源](#resource)。

無論使用哪種技術,結果都是由 定義[`ListView`](xref:Xamarin.Forms.ListView)[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)中的每個 單元格,如以下螢幕截圖所示:

![](creating-images/data-template-appearance.png "ListView with a DataTemplate")

<a name="inline" />

## <a name="creating-an-inline-datatemplate"></a>建立內嵌 DataTemplate

屬性[`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1)可以設定為內[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)聯 。 如果不需要在他處重複使用資料範本，則應該使用以適當控制項屬性直接子系形式放置的內嵌範本。 在 `DataTemplate` 中指定的項目會定義每個資料格的外觀，如下列 XAML 程式碼範例所示：

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

內聯[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)的子級必須為 或派生自[`Cell`](xref:Xamarin.Forms.Cell)類型 。 此範例使用 派[`ViewCell`](xref:Xamarin.Forms.ViewCell)生`Cell`自的 。 中`ViewCell`佈局由管理在此處[`Grid`](xref:Xamarin.Forms.Grid)。 包含`Grid`三[`Label`](xref:Xamarin.Forms.Label)個實例,它們將其[`Text`](xref:Xamarin.Forms.Label.Text)屬性綁定到集合中`Person`每個 物件的相應屬性。

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

在 C#[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)中, 內`Func`聯使用指定 參數的建構函數重載創建。

<a name="type" />

## <a name="creating-a-datatemplate-with-a-type"></a>使用類型建立 DataTemplate

該[`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1)屬性也可以設置[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)為 從單元格類型創建的。 使用這種方法的優點為，資料格類型所定義的外觀可供多個資料範本重複用於整個應用程式。 下列 XAML 程式碼顯示此方法的範例：

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

在這裡,[`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1)該屬性設置[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)為 從定義單元格外觀的自定義類型創建的。 自定義類型必須派生自[`ViewCell`](xref:Xamarin.Forms.ViewCell)類型 ,如以下代碼範例所示:

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

在[`ViewCell`](xref:Xamarin.Forms.ViewCell)中,佈局由管理[`Grid`](xref:Xamarin.Forms.Grid)在此處。 包含`Grid`三[`Label`](xref:Xamarin.Forms.Label)個實例,它們將其[`Text`](xref:Xamarin.Forms.Label.Text)屬性綁定到集合中`Person`每個 物件的相應屬性。

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

在 C#[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)中,使用建構函數重載創建,該建構函數將單元格類型指定為參數。 儲存格型態必須派生自類型[`ViewCell`](xref:Xamarin.Forms.ViewCell),如以下代碼範例所示:

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
> 請注意,Xamarin.Forms 還包括可用於在儲存格中[`ListView`](xref:Xamarin.Forms.ListView)顯示簡單資料的儲存格類型。 如需詳細資訊，請參閱[資料格外觀](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)。

<a name="resource" />

## <a name="creating-a-datatemplate-as-a-resource"></a>將 DataTemplate 建立為資源

數據範本也可以創建為中的[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)可重用物件。 做法是為每個宣告提供唯一的 `x:Key` 屬性，這會在 `ResourceDictionary` 中為它提供描述性索引鍵，如下列 XAML 程式碼範例所示：

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

[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)通過使用`StaticResource`標記擴展分配給[`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1)屬性。 請注意,雖然在`DataTemplate`頁面[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)中定義 ,也可以在控件級別或應用程式級別定義。

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

[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)將添加到[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)[`Add`](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object))使用方法,該方法指定用於`Key`引用`DataTemplate`檢索 時引用的字串。

## <a name="summary"></a>總結

本文介紹了如何從自定義類型或 在 中創建資料範本、內聯[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)範本、 如果不需要在他處重複使用資料範本，則應該使用內嵌範本。 或者，您可以將資料範本定義為自訂類型，或是定義為控制項層級、頁面層級或應用程式層級資源，來重複使用它。

## <a name="related-links"></a>相關連結

- [儲存格外觀](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [Data Templates (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates) (資料範本 (範例))
- [DataTemplate](xref:Xamarin.Forms.DataTemplate)
