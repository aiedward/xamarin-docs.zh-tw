---
title: 簡介
description: Xamarin.Forms 資料範本會提供支援的控制項上定義資料的呈現方式的能力。 本文章會提供為什麼會需要這些檢查的資料範本的簡介。
ms.prod: xamarin
ms.assetid: 4ED4ACF4-BE4A-44ED-8EAF-C03947B8663B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: beb1633919a1d4d5bc2f5a8b0452b535a5cc8f3f
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/07/2018
ms.locfileid: "34847105"
---
# <a name="introduction"></a>簡介

_Xamarin.Forms 資料範本會提供支援的控制項上定義資料的呈現方式的能力。本文章會提供為什麼會需要這些檢查的資料範本的簡介。_

請考慮[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)所顯示的集合`Person`物件。 下列程式碼範例顯示定義`Person`類別：

```csharp
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }
    public string Location { get; set; }
}
```

`Person`類別會定義`Name`， `Age`，和`Location`時可以設定的屬性`Person`建立物件。 [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)用來顯示的集合`Person`物件，如下列 XAML 程式碼範例所示：

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
                    <local:Person Name="John" Age="37" Location="USA" />
                    <local:Person Name="Tom" Age="42" Location="UK" />
                    <local:Person Name="Lucas" Age="29" Location="Germany" />
                    <local:Person Name="Tariq" Age="39" Location="UK" />
                    <local:Person Name="Jane" Age="30" Location="USA" />
                </x:Array>
            </ListView.ItemsSource>
        </ListView>
    </StackLayout>
</ContentPage>
```

項目加入[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)在 XAML 中初始化[ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemsSource/)屬性的陣列從`Person`執行個體。

> [!NOTE]
> 請注意，`x:Array`元素需要`Type`屬性，指出陣列中項目的類型。

對等的 C# 頁面會顯示在下列程式碼範例中，用來初始化[ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemsSource/)屬性`List`的`Person`執行個體：

```csharp
public WithoutDataTemplatePageCS()
{
    ...
    var people = new List<Person>
    {
        new Person { Name = "Steve", Age = 21, Location = "USA" },
        new Person { Name = "John", Age = 37, Location = "USA" },
        new Person { Name = "Tom", Age = 42, Location = "UK" },
        new Person { Name = "Lucas", Age = 29, Location = "Germany" },
        new Person { Name = "Tariq", Age = 39, Location = "UK" },
        new Person { Name = "Jane", Age = 30, Location = "USA" }
    };

    Content = new StackLayout
    {
        Margin = new Thickness(20),
        Children = {
            ...
            new ListView { ItemsSource = people, Margin = new Thickness(0, 20, 0, 0) }
        }
    };
}
```

[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)呼叫`ToString`時顯示的物件集合中。 因為沒有任何`Person.ToString`覆寫，`ToString`傳回的類型名稱的每個物件，如下列螢幕擷取畫面所示：

![](introduction-images/no-data-template.png "不使用資料範本的清單檢視")

`Person`物件可以覆寫`ToString`方法，以顯示有意義的資料，如下列程式碼範例所示：

```csharp
public class Person
{
  ...
  public override string ToString ()
  {
    return Name;
  }
}
```

這會導致[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)顯示`Person.Name`屬性值的每個物件在集合中，如下列螢幕擷取畫面所示：

![](introduction-images/override-tostring.png "使用資料範本的清單檢視")

`Person.ToString`覆寫可能會傳回已格式化的字串，其中包含`Name`， `Age`，和`Location`屬性。 不過，這種方法提供的有限的控制項的每個項目資料的外觀。 為了更多的彈性， [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)可以建立定義資料的外觀。

## <a name="creating-a-datatemplate"></a>建立 DataTemplate

A [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)用來指定資料的外觀，且通常用於顯示資料的資料繫結。 顯示資料，集合中的物件時，其常見使用案例[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)。 例如，當`ListView`繫結的集合`Person`物件`ListView.ItemTemplate`屬性將設定為`DataTemplate`，定義每個外觀`Person`物件存放至`ListView`。 `DataTemplate`將包含繫結到每個屬性值的項目`Person`物件。 如需有關資料繫結的詳細資訊，請參閱[資料繫結基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)。

A [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)可用來當做值，針對下列屬性：

- [`ListView.HeaderTemplate`](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.HeaderTemplate/)
- [`ListView.FooterTemplate`](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.FooterTemplate/)
- [`ListView.GroupHeaderTemplate`](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.GroupHeaderTemplate/)
- [`ItemsView.ItemTemplate`](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/)其會繼承[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)。
- [`MultiPage.ItemTemplate`](https://developer.xamarin.com/api/type/Xamarin.Forms.MultiPage%3CT%3E/)其會繼承[ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/)， [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)，和[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)。

> [!NOTE]
> 請注意，雖然[ `TableView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/)使用[ `Cell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/)物件，它不會使用[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)。 這是因為資料繫結永遠會直接在設定`Cell`物件。

A [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)如下的直接子系上方所列的屬性就所謂的放置*內嵌樣板*。 或者，`DataTemplate`可以定義為層級的控制項、 頁面層級或應用程式層級資源。 選擇要定義在何處[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)可以用它的影響：

- A [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)定義於控制項層級只能套用至控制項。
- A [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)定義在頁面層級可以套用至多個頁面上的有效控制項。
- A [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)應用程式層級定義可套用至有效控制整個應用程式。

檢視階層中較低的資料範本會優先於它們共用時更高版本定義總`x:Key`屬性。 例如，頁面層級資料範本，將會覆寫應用程式層級資料範本和頁面層級資料範本將會覆寫控制層級資料範本或內嵌資料範本。


## <a name="related-links"></a>相關連結

- [儲存格外觀](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [資料範本 （範例）](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplates/)
- [DataTemplate](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)
