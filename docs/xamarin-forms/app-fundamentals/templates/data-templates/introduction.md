---
title: Xamarin.Forms 資料範本簡介
description: Xamarin.Forms 資料範本可以在支援的控制項上定義資料呈現方式。 本文提供資料範本的簡介，探討為何資料範本是必要的。
ms.prod: xamarin
ms.assetid: 4ED4ACF4-BE4A-44ED-8EAF-C03947B8663B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: c547a851d9e042f0289ac5c3a2d20ff97f681551
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2019
ms.locfileid: "65926947"
---
# <a name="introduction-to-xamarinforms-data-templates"></a>Xamarin.Forms 資料範本簡介

[![下載範例](~/media/shared/download.png) 下載範例](https://developer.xamarin.com/samples/xamarin-forms/Templates/DataTemplates/)

_Xamarin.Forms 資料範本可以在支援的控制項上定義資料呈現方式。本文提供資料範本的簡介，探討為何資料範本是必要的。_

假設有一個顯示 `Person` 物件集合的 [`ListView`](xref:Xamarin.Forms.ListView)。 下列程式碼範例顯示 `Person` 類別的定義：

```csharp
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }
    public string Location { get; set; }
}
```

`Person` 類別定義 `Name`、`Age` 和 `Location` 屬性，可在建立 `Person` 物件時進行設定。 [`ListView`](xref:Xamarin.Forms.ListView) 用於顯示 `Person` 物件集合，如下列 XAML 程式碼範例所示：

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

您可以透過從 `Person` 執行個體陣列初始化 [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) 屬性，來將項目新增至 XAML 中的 [`ListView`](xref:Xamarin.Forms.ListView)。

> [!NOTE]
> 請注意，`x:Array` 項目需要 `Type` 屬性，以指出陣列中的項目類型。

下列程式碼範例顯示對等的 C# 頁面，該頁面會將 [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) 屬性初始化為 `Person` 執行個體的 `List`：

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

當顯示集合中的物件時，[`ListView`](xref:Xamarin.Forms.ListView) 會呼叫 `ToString`。 由於沒有 `Person.ToString` 覆寫，因此 `ToString` 會傳回每個物件的類型名稱，如下列螢幕擷取畫面所示：

![](introduction-images/no-data-template.png "沒有資料範本的 ListView")

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

這會導致 [`ListView`](xref:Xamarin.Forms.ListView) 顯示集合中每個物件的 `Person.Name` 屬性值，如下列螢幕擷取畫面所示：

![](introduction-images/override-tostring.png "具有資料範本的 ListView")

`Person.ToString` 覆寫可能會傳回由 `Name`、`Age` 和 `Location` 屬性組成的格式化字串。 不過，此方法只會提供對每個資料項目外觀有限的控制。 若要提高彈性，可建立定義資料外觀的 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)。

## <a name="creating-a-datatemplate"></a>建立 DataTemplate

[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 用於指定資料外觀，通常會使用資料繫結來顯示資料。 其常見使用方式情節是在 [`ListView`](xref:Xamarin.Forms.ListView) 中顯示物件集合的資料時。 例如，當 `ListView` 繫結至 `Person` 物件集合時，會將 `ListView.ItemTemplate` 屬性設定為 `DataTemplate`，來定義 `ListView` 中每個 `Person` 物件的外觀。 `DataTemplate` 會包含繫結至每個 `Person` 物件屬性值的項目。 如需有關資料繫結的詳細資訊，請參閱[資料繫結基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)。

[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 可作為下列屬性值使用：

- [`ListView.HeaderTemplate`](xref:Xamarin.Forms.ListView.HeaderTemplate)
- [`ListView.FooterTemplate`](xref:Xamarin.Forms.ListView.FooterTemplate)
- [`ListView.GroupHeaderTemplate`](xref:Xamarin.Forms.ListView.GroupHeaderTemplate)
- [`ItemsView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1)，由 [`ListView`](xref:Xamarin.Forms.ListView) 繼承。
- [`MultiPage.ItemTemplate`](xref:Xamarin.Forms.MultiPage`1)，由 [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)、[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 和 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 繼承。

> [!NOTE]
> 請注意，雖然 [`TableView`](xref:Xamarin.Forms.TableView) 會使用 [`Cell`](xref:Xamarin.Forms.Cell) 物件，但不會使用 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)。 這是因為資料繫結一律會在 `Cell` 物件上直接設定。

作為上述屬性直接子系的 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 稱為「內嵌範本」。 或者，您可以將 `DataTemplate` 定義為控制項層級、頁面層級或應用程式層級資源。 選擇要在何處定義 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 會影響其可使用的位置：

- 在控制項層級定義的 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 只能套用至控制項。
- 在頁面層級定義的 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 可套用至頁面上的多個有效控制項。
- 在應用程式層級定義的 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 則可套用至整個應用程式的所有有效控制項。

當資料範本共用 `x:Key` 屬性時，檢視階層架構中較低的資料範本會優先於定義於較高位置的資料範本。 例如，頁面層級資料範本將會覆寫應用程式層級資料範本，而控制項層級資料範本 (或內嵌資料範本) 將會覆寫頁面層級資料範本。


## <a name="related-links"></a>相關連結

- [儲存格外觀](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [Data Templates (Samples)](https://developer.xamarin.com/samples/xamarin-forms/Templates/DataTemplates/) (資料範本 (範例))
- [DataTemplate](xref:Xamarin.Forms.DataTemplate)
