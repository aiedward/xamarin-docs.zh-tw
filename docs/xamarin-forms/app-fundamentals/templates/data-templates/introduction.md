---
title: Xamarin.Forms 資料範本簡介
description: Xamarin.Forms 資料範本可以在支援的控制項上定義資料呈現方式。 本文提供資料範本的簡介，探討為何資料範本是必要的。
ms.prod: xamarin
ms.assetid: 4ED4ACF4-BE4A-44ED-8EAF-C03947B8663B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: 10bba38de1dc8908ad853d5e4ca2bb845b4ac8c6
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771271"
---
# <a name="introduction-to-xamarinforms-data-templates"></a>Xamarin.Forms 資料範本簡介

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)

_Xamarin.Forms 數據範本提供了定義受支援控制項上數據的表示功能。本文介紹了數據範本,檢查它們為什麼是必需的。_

要顯示[`ListView`](xref:Xamarin.Forms.ListView)物件集合`Person`的 。 下列程式碼範例顯示 `Person` 類別的定義：

```csharp
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }
    public string Location { get; set; }
}
```

`Person` 類別定義 `Name`、`Age` 和 `Location` 屬性，可在建立 `Person` 物件時進行設定。 [`ListView`](xref:Xamarin.Forms.ListView)用於顯示`Person`物件的集合,如以下 XAML 代碼範例所示:

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

透過從`Person`實體中[`ListView`](xref:Xamarin.Forms.ListView)初始化屬性,[`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource)項將添加到 XAML 中。

> [!NOTE]
> 請注意，`x:Array` 項目需要 `Type` 屬性，以指出陣列中的項目類型。

等效的 C# 頁顯示在以下代碼範例中,該範[`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource)例將 屬性初始化到`Person``List`實體:

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

在[`ListView`](xref:Xamarin.Forms.ListView)集合`ToString`中顯示物件時的調用。 由於沒有 `Person.ToString` 覆寫，因此 `ToString` 會傳回每個物件的類型名稱，如下列螢幕擷取畫面所示：

![](introduction-images/no-data-template.png "ListView without a Data Template")

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

這將導致[`ListView`](xref:Xamarin.Forms.ListView)顯示集合中每個`Person.Name`物件 的屬性值,如以下螢幕截圖所示:

![](introduction-images/override-tostring.png "ListView with a Data Template")

`Person.ToString` 覆寫可能會傳回由 `Name`、`Age` 和 `Location` 屬性組成的格式化字串。 不過，此方法只會提供對每個資料項目外觀有限的控制。 為了取得更大的靈活性,可以[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)建立定義資料外觀的 。

## <a name="creating-a-datatemplate"></a>建立 DataTemplate

A[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)用於指定數據的外觀,通常使用數據綁定來顯示數據。 為常見使用機制是在 顯示 物件集合中[`ListView`](xref:Xamarin.Forms.ListView)的資料時 。 例如，當 `ListView` 繫結至 `Person` 物件集合時，會將 `ListView.ItemTemplate` 屬性設定為 `DataTemplate`，來定義 `ListView` 中每個 `Person` 物件的外觀。 `DataTemplate` 會包含繫結至每個 `Person` 物件屬性值的項目。 如需有關資料繫結的詳細資訊，請參閱[資料繫結基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)。

A[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)可用以下屬性的值:

- [`ListView.HeaderTemplate`](xref:Xamarin.Forms.ListView.HeaderTemplate)
- [`ListView.FooterTemplate`](xref:Xamarin.Forms.ListView.FooterTemplate)
- [`ListView.GroupHeaderTemplate`](xref:Xamarin.Forms.ListView.GroupHeaderTemplate)
- [`ItemsView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1)由繼承,由[`ListView`](xref:Xamarin.Forms.ListView)繼承。
- [`MultiPage.ItemTemplate`](xref:Xamarin.Forms.MultiPage`1)由[`CarouselPage`](xref:Xamarin.Forms.CarouselPage)[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)繼承的[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)與 。

> [!NOTE]
> 請注意,雖然[`TableView`](xref:Xamarin.Forms.TableView)[`Cell`](xref:Xamarin.Forms.Cell)使用 物件,但它[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)不使用 。 這是因為資料繫結一律會在 `Cell` 物件上直接設定。

這個[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)選項的屬性的直接子級放置的 A 稱為*內聯範本*。 或者，您可以將 `DataTemplate` 定義為控制項層級、頁面層級或應用程式層級資源。 選擇在哪裡定義可以使用[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)的影響:

- 在[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)控件級別定義的只能應用於控制項。
- 在[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)頁面級別定義的控制項可以應用於頁面上的多個有效控制項。
- 在[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)應用程式級別定義的可以應用於整個應用程式的有效控制項。

當資料範本共用 `x:Key` 屬性時，檢視階層架構中較低的資料範本會優先於定義於較高位置的資料範本。 例如，頁面層級資料範本將會覆寫應用程式層級資料範本，而控制項層級資料範本 (或內嵌資料範本) 將會覆寫頁面層級資料範本。

## <a name="related-links"></a>相關連結

- [儲存格外觀](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [Data Templates (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates) (資料範本 (範例))
- [DataTemplate](xref:Xamarin.Forms.DataTemplate)
