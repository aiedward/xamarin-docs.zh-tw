---
title: ListView 的資料來源
description: 這篇文章說明如何填入 Xamarin.Forms ListView 的資料，以及如何使用 ListView 中的資料繫結。
ms.prod: xamarin
ms.assetid: B5571660-1E82-4379-95C3-0725288CF5D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/30/2018
ms.openlocfilehash: aa968562470a1e3405bf68be7eb0294273970386
ms.sourcegitcommit: a5ef4497db04dfa016865bc7454b3de6ff088554
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2019
ms.locfileid: "70998018"
---
# <a name="listview-data-sources"></a>ListView 的資料來源

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)

[Xamarin [`ListView`](xref:Xamarin.Forms.ListView) ] 用於顯示資料的清單。 本文說明如何在中填入`ListView`資料，以及如何將資料系結至選取的專案。

## <a name="itemssource"></a>ItemsSource

A [ `ListView` ](xref:Xamarin.Forms.ListView)填入資料使用[ `ItemsSource` ](xref:Xamarin.Forms.ItemsView`1.ItemsSource)屬性，它可以接受任何集合，實作`IEnumerable`。 最簡單的方式，來填入`ListView`牽涉到使用字串的陣列：

```xaml
<ListView>
      <ListView.ItemsSource>
          <x:Array Type="{x:Type x:String}">
            <x:String>mono</x:String>
            <x:String>monodroid</x:String>
            <x:String>monotouch</x:String>
            <x:String>monorail</x:String>
            <x:String>monodevelop</x:String>
            <x:String>monotone</x:String>
            <x:String>monopoly</x:String>
            <x:String>monomodal</x:String>
            <x:String>mononucleosis</x:String>
          </x:Array>
      </ListView.ItemsSource>
</ListView>
```

對等的 C# 程式碼是：

```csharp
var listView = new ListView();
listView.ItemsSource = new string[]
{
  "mono",
  "monodroid",
  "monotouch",
  "monorail",
  "monodevelop",
  "monotone",
  "monopoly",
  "monomodal",
  "mononucleosis"
};
```

![](data-and-databinding-images/itemssource-simple.png "ListView 顯示的字串清單，")

這個方法會將字串`ListView`清單填入。 根據預設，`ListView`稱之為`ToString`，並顯示在結果`TextCell`每個資料列。 若要自訂 顯示資料的方式，請參閱[儲存格的外觀](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)。

因為`ItemsSource`已傳送到陣列中，內容將不會更新為基礎的清單或陣列變更。 如果您想要自動更新，如新增、 移除和變更的基礎清單中的項目 ListView 時，您必須使用`ObservableCollection`。 [`ObservableCollection`](xref:System.Collections.ObjectModel.ObservableCollection`1) 定義於`System.Collections.ObjectModel`一樣，而且`List`，只不過它就會通知`ListView`的任何變更：

```csharp
ObservableCollection<Employee> employees = new ObservableCollection<Employee>();
listView.ItemsSource = employees;

//Mr. Mono will be added to the ListView because it uses an ObservableCollection
employees.Add(new Employee(){ DisplayName="Mr. Mono"});
```

## <a name="data-binding"></a>資料繫結

資料系結是將使用者介面物件的屬性系結至某個 CLR 物件屬性的「粘連」，例如 viewmodel 中的類別。 資料繫結是很有用，因為它藉由取代許多無聊的未定案程式碼簡化使用者介面的開發。

資料繫結的運作方式是讓物件保持同步，因為其繫結的值變更。 您不需要在每次控制項的值變更時撰寫事件處理常式，而是在 viewmodel 中建立系結並啟用系結。

如需有關資料繫結的詳細資訊，請參閱 <<c0> [ 資料繫結的基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)這是部分的四[Xamarin.Forms XAML 基本知識文章系列](~/xamarin-forms/xaml/xaml-basics/index.md)。

### <a name="binding-cells"></a>儲存格繫結

資料格 （和資料格的子系） 的屬性可以繫結中物件的屬性至`ItemsSource`。 例如， `ListView`可以用來呈現員工清單。

「 員工 」 類別：

```csharp
public class Employee
{
    public string DisplayName {get; set;}
}
```

會建立、設定`ListView` `ItemsSource`為，而且清單會填入資料： `ObservableCollection<Employee>`

```csharp
ObservableCollection<Employee> employees = new ObservableCollection<Employee>();
public ObservableCollection<Employee> Employees { get { return employees; }}

public EmployeeListPage()
{
    EmployeeView.ItemsSource = employees;

    // ObservableCollection allows items to be added after ItemsSource
    // is set and the UI will react to changes
    employees.Add(new Employee{ DisplayName="Rob Finnerty"});
    employees.Add(new Employee{ DisplayName="Bill Wrestler"});
    employees.Add(new Employee{ DisplayName="Dr. Geri-Beth Hooper"});
    employees.Add(new Employee{ DisplayName="Dr. Keith Joyce-Purdy"});
    employees.Add(new Employee{ DisplayName="Sheri Spruce"});
    employees.Add(new Employee{ DisplayName="Burt Indybrick"});
}
```

> [!WARNING]
> `ObservableCollection`不是安全線程。 `ObservableCollection`修改會導致 UI 更新在執行修改的相同執行緒上發生。 如果執行緒不是主要的 UI 執行緒，則會造成例外狀況。

下列程式碼片段示範`ListView`繫結至的員工清單：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:constants="clr-namespace:XamarinFormsSample;assembly=XamarinFormsXamlSample"
             x:Class="XamarinFormsXamlSample.Views.EmployeeListPage"
             Title="Employee List">
  <ListView x:Name="EmployeeView"
            ItemsSource="{Binding Employees}">
    <ListView.ItemTemplate>
      <DataTemplate>
        <TextCell Text="{Binding DisplayName}" />
      </DataTemplate>
    </ListView.ItemTemplate>
  </ListView>
</ContentPage>
```

此 XAML 範例`ContentPage`會定義`ListView`包含的。 資料來源`ListView`透過設定`ItemsSource`屬性。 中`ItemsSource`每個資料列的配置都是在`ListView.ItemTemplate`元素內定義。 這會導致下列螢幕擷取畫面：

![](data-and-databinding-images/bound-data.png "使用資料繫結的 ListView")

### <a name="binding-selecteditem"></a>繫結 SelectedItem

通常您會想要繫結至選取的項目`ListView`，而不是比使用事件處理常式來回應變更。 若要在 XAML 中這樣做，請繫結`SelectedItem`屬性：

```xaml
<ListView x:Name="listView"
          SelectedItem="{Binding Source={x:Reference SomeLabel},
          Path=Text}">
 …
</ListView>
```

`listView` `SomeLabel` `Text`假設是字串清單，則會將其屬性系結至`SelectedItem`。 `ItemsSource`

## <a name="related-links"></a>相關連結

- [雙向繫結 （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)
