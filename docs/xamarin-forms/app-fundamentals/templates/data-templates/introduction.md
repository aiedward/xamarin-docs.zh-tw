---
title: Xamarin.Forms資料範本簡介
description: Xamarin.Forms資料範本提供了在支援的控制項上定義資料呈現方式的能力。 本文提供資料範本的簡介，探討為何資料範本是必要的。
ms.prod: xamarin
ms.assetid: 4ED4ACF4-BE4A-44ED-8EAF-C03947B8663B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3558352c9f43b8e301492077806bbb611e9b58cf
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86929307"
---
# <a name="introduction-to-xamarinforms-data-templates"></a>Xamarin.Forms資料範本簡介

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)

_Xamarin.Forms資料範本提供了在支援的控制項上定義資料呈現方式的能力。本文提供資料範本的簡介，檢查為何需要它們。_

請考慮 [`ListView`](xref:Xamarin.Forms.ListView) 會顯示物件集合的 `Person` 。 下列程式碼範例顯示 `Person` 類別的定義：

```csharp
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }
    public string Location { get; set; }
}
```

`Person` 類別定義 `Name`、`Age` 和 `Location` 屬性，可在建立 `Person` 物件時進行設定。 [`ListView`](xref:Xamarin.Forms.ListView)會用來顯示物件的集合 `Person` ，如下列 XAML 程式碼範例所示：

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

藉 [`ListView`](xref:Xamarin.Forms.ListView) 由 [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) 從實例陣列初始化屬性，將專案新增至 XAML 中的 `Person` 。

> [!NOTE]
> 請注意，`x:Array` 項目需要 `Type` 屬性，以指出陣列中的項目類型。

對等的 c # 頁面會顯示在下列程式碼範例中，它會將 [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) 屬性初始化為 `List` 實例的 `Person` ：

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

會 [`ListView`](xref:Xamarin.Forms.ListView) 在 `ToString` 顯示集合中的物件時呼叫。 由於沒有 `Person.ToString` 覆寫，因此 `ToString` 會傳回每個物件的類型名稱，如下列螢幕擷取畫面所示：

![沒有資料範本的 ListView](introduction-images/no-data-template.png)

`Person` 物件可以覆寫 `ToString` 方法來顯示有意義的資料，如下列程式碼範例所示：

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

這會導致在 [`ListView`](xref:Xamarin.Forms.ListView) `Person.Name` 集合中顯示每個物件的屬性值，如下列螢幕擷取畫面所示：

![具有資料範本的 ListView](introduction-images/override-tostring.png)

`Person.ToString` 覆寫可能會傳回由 `Name`、`Age` 和 `Location` 屬性組成的格式化字串。 不過，此方法只會提供對每個資料項目外觀有限的控制。 為了提供更大的彈性， [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 可以建立，以定義資料的外觀。

## <a name="creating-a-datatemplate"></a>建立 DataTemplate

[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)是用來指定資料的外觀，而且通常會使用資料系結來顯示資料。 其常見的使用案例是在中顯示物件集合的資料時 [`ListView`](xref:Xamarin.Forms.ListView) 。 例如，當 `ListView` 繫結至 `Person` 物件集合時，會將 `ListView.ItemTemplate` 屬性設定為 `DataTemplate`，來定義 `ListView` 中每個 `Person` 物件的外觀。 `DataTemplate` 會包含繫結至每個 `Person` 物件屬性值的項目。 如需有關資料繫結的詳細資訊，請參閱[資料繫結基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)。

[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)可以用來當做下列屬性的值：

- [`ListView.HeaderTemplate`](xref:Xamarin.Forms.ListView.HeaderTemplate)
- [`ListView.FooterTemplate`](xref:Xamarin.Forms.ListView.FooterTemplate)
- [`ListView.GroupHeaderTemplate`](xref:Xamarin.Forms.ListView.GroupHeaderTemplate)
- [`ItemsView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1)，它是由繼承 [`ListView`](xref:Xamarin.Forms.ListView) 。
- [`MultiPage.ItemTemplate`](xref:Xamarin.Forms.MultiPage`1)，它是由 [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) 、 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 和繼承 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 。

> [!NOTE]
> 請注意，雖然 [`TableView`](xref:Xamarin.Forms.TableView) 會使用 [`Cell`](xref:Xamarin.Forms.Cell) 物件，但它不會使用 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。 這是因為資料繫結一律會在 `Cell` 物件上直接設定。

，其做 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 為上列屬性的直接子系，稱為*內嵌範本*。 或者，您可以將 `DataTemplate` 定義為控制項層級、頁面層級或應用程式層級資源。 選擇要在何處定義 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 可使用的影響：

- [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)在控制項層級定義的只能套用至控制項。
- 在 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 頁面層級定義的可套用至頁面上的多個有效控制項。
- 在 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 應用層級定義的可套用至整個應用程式的有效控制項。

當資料範本共用 `x:Key` 屬性時，檢視階層架構中較低的資料範本會優先於定義於較高位置的資料範本。 例如，頁面層級資料範本將會覆寫應用程式層級資料範本，而控制項層級資料範本 (或內嵌資料範本) 將會覆寫頁面層級資料範本。

## <a name="related-links"></a>相關連結

- [儲存格外觀](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [Data Templates (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates) (資料範本 (範例))
- [DataTemplate](xref:Xamarin.Forms.DataTemplate)
