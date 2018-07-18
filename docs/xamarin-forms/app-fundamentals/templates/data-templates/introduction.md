---
title: Introduction to Xamarin.Forms 資料範本
description: Xamarin.Forms 資料範本提供支援的控制項上定義資料的呈現方式的能力。 這篇文章介紹資料範本，檢查為什麼需要這些項目。
ms.prod: xamarin
ms.assetid: 4ED4ACF4-BE4A-44ED-8EAF-C03947B8663B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: 129ce7a04b93bfb3cb1b9a1639aee61cd56d09d5
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998909"
---
# <a name="introduction-to-xamarinforms-data-templates"></a>Introduction to Xamarin.Forms 資料範本

_Xamarin.Forms 資料範本提供支援的控制項上定義資料的呈現方式的能力。這篇文章介紹資料範本，檢查為什麼需要這些項目。_

請考慮[ `ListView` ](xref:Xamarin.Forms.ListView)會顯示集合`Person`物件。 下列程式碼範例顯示定義`Person`類別：

```csharp
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }
    public string Location { get; set; }
}
```

`Person`類別會定義`Name`， `Age`，以及`Location`時可以進行設定的屬性`Person`建立物件。 [ `ListView` ](xref:Xamarin.Forms.ListView)用來顯示集合`Person`物件，如下列 XAML 程式碼範例所示：

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

項目會加入[ `ListView` ](xref:Xamarin.Forms.ListView)中所初始化的 XAML [ `ItemsSource` ](xref:Xamarin.Forms.ItemsView`1.ItemsSource)屬性的陣列從`Person`執行個體。

> [!NOTE]
> 請注意，`x:Array`元素需要`Type`屬性，指出陣列中項目的類型。

對等的 C# 頁面會顯示在下列程式碼範例中，用來初始化[ `ItemsSource` ](xref:Xamarin.Forms.ItemsView`1.ItemsSource)屬性設`List`的`Person`執行個體：

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

[ `ListView` ](xref:Xamarin.Forms.ListView)呼叫`ToString`顯示集合中的物件時。 因為沒有任何`Person.ToString`覆寫，`ToString`傳回型別名稱的每個物件，如下列螢幕擷取畫面所示：

![](introduction-images/no-data-template.png "不使用資料範本的 ListView")

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

這會導致[ `ListView` ](xref:Xamarin.Forms.ListView)顯示`Person.Name`屬性值，每個物件在集合中，如下列螢幕擷取畫面所示：

![](introduction-images/override-tostring.png "使用資料範本的 ListView")

`Person.ToString`覆寫可能會傳回格式化的字串，其中包含`Name`， `Age`，和`Location`屬性。 不過，這個方法會提供有限度地的控制資料的每個項目的外觀。 更多的彈性[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)可以建立定義資料的外觀。

## <a name="creating-a-datatemplate"></a>建立 DataTemplate

A [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)用來指定資料的外觀和通常用以顯示資料的資料繫結。 顯示資料，集合中的物件時，其常見使用案例[ `ListView` ](xref:Xamarin.Forms.ListView)。 例如，當`ListView`繫結至的集合`Person`物件，`ListView.ItemTemplate`屬性會設定為`DataTemplate`所定義的每個出現`Person`物件`ListView`。 `DataTemplate`會包含繫結到每個屬性值的項目`Person`物件。 如需有關資料繫結的詳細資訊，請參閱[資料繫結基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)。

A [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)可用來當做值，針對下列屬性：

- [`ListView.HeaderTemplate`](xref:Xamarin.Forms.ListView.HeaderTemplate)
- [`ListView.FooterTemplate`](xref:Xamarin.Forms.ListView.FooterTemplate)
- [`ListView.GroupHeaderTemplate`](xref:Xamarin.Forms.ListView.GroupHeaderTemplate)
- [`ItemsView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1)其會繼承[ `ListView` ](xref:Xamarin.Forms.ListView)。
- [`MultiPage.ItemTemplate`](xref:Xamarin.Forms.MultiPage`1)其會繼承[ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage)， [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)，以及[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)。

> [!NOTE]
> 請注意，雖然[ `TableView` ](xref:Xamarin.Forms.TableView)會使用[ `Cell` ](xref:Xamarin.Forms.Cell)物件，它不會使用[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)。 這是因為資料繫結永遠會在上直接設定`Cell`物件。

A [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)如上面所列的屬性的直接子系就所謂的放*內嵌範本*。 或者，`DataTemplate`可以定義為控制層級、 頁面層級或應用程式層級資源。 選擇要定義在何處[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)可以用它的影響：

- A [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)定義控制項在層級只能套用至控制項。
- A [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)定義在頁面層級可以套用至多個頁面上的有效控制項。
- A [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)定義應用程式層級可以套用至有效的控制項，整個應用程式。

檢視階層中較低的資料範本會優先於設定更高版本定義，當它們共用`x:Key`屬性。 比方說，應用程式層級的資料範本將會覆寫頁面層級資料範本，並將覆寫頁面層級資料範本，藉由控制層級資料範本或內嵌資料範本。


## <a name="related-links"></a>相關連結

- [儲存格外觀](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [資料範本 （範例）](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplates/)
- [DataTemplate](xref:Xamarin.Forms.DataTemplate)
