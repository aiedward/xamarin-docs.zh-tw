---
title: ListView 資料來源
description: 本文說明如何在 Xamarin.Forms listview 中填入資料，以及如何搭配 listview 使用資料系結。
ms.prod: xamarin
ms.assetid: B5571660-1E82-4379-95C3-0725288CF5D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 38a895c9064fc012aec35b37eac78bb16ff009a9
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84131504"
---
# <a name="listview-data-sources"></a>ListView 資料來源

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)

Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) 用於顯示資料的清單。 本文說明如何在中填入 `ListView` 資料，以及如何將資料系結至選取的專案。

## <a name="itemssource"></a>ItemsSource

[`ListView`](xref:Xamarin.Forms.ListView)會使用屬性填入資料 [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) ，這可以接受任何實作為集合 `IEnumerable` 。 填入的最簡單方式 `ListView` 牽涉到使用字串陣列：

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

對等的 C# 程式碼為：

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

![](data-and-databinding-images/itemssource-simple.png "ListView Displaying List of Strings")

這個方法會將 `ListView` 字串清單填入。 根據預設， `ListView` 會呼叫 `ToString` 並顯示 `TextCell` 每個資料列的結果。 若要自訂資料的顯示方式，請參閱[儲存格外觀](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)。

因為 `ItemsSource` 已傳送到陣列，所以內容將不會隨著基礎清單或陣列變更而更新。 如果您想要在新增、移除和變更基礎清單中的專案時，ListView 會自動更新，您必須使用 `ObservableCollection` 。 [`ObservableCollection`](xref:System.Collections.ObjectModel.ObservableCollection`1)在中定義， `System.Collections.ObjectModel` 而且與類似 `List` ，不同之處在于它可以通知 `ListView` 任何變更：

```csharp
ObservableCollection<Employee> employees = new ObservableCollection<Employee>();
listView.ItemsSource = employees;

//Mr. Mono will be added to the ListView because it uses an ObservableCollection
employees.Add(new Employee(){ DisplayName="Mr. Mono"});
```

## <a name="data-binding"></a>資料繫結

資料系結是將使用者介面物件的屬性系結至某個 CLR 物件屬性的「粘連」，例如 viewmodel 中的類別。 資料系結很有用，因為它會取代許多單調乏味的程式碼，藉此簡化使用者介面的開發。

資料系結的運作方式是讓物件保持同步，因為其系結的值變更。 您不需要在每次控制項的值變更時撰寫事件處理常式，而是在 viewmodel 中建立系結並啟用系結。

如需資料系結的詳細資訊，請參閱資料系結[基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)，這是[ Xamarin.Forms XAML 基本概念文章系列](~/xamarin-forms/xaml/xaml-basics/index.md)的第四部分。

### <a name="binding-cells"></a>系結資料格

資料格（和資料格的子系）的屬性可以系結至中的物件屬性 `ItemsSource` 。 例如， `ListView` 可以用來呈現員工清單。

Employee 類別：

```csharp
public class Employee
{
    public string DisplayName {get; set;}
}
```

`ObservableCollection<Employee>`會建立、設定為 `ListView` `ItemsSource` ，而且清單會填入資料：

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
> 雖然 `ListView` 會更新以回應其基礎的變更，但 `ObservableCollection` 如果將 `ListView` 不同的 `ObservableCollection` 實例指派給原始 `ObservableCollection` 參考（例如），則不會更新 `employees = otherObservableCollection;` 。

下列程式碼片段示範系結 `ListView` 至員工清單的：

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

此 XAML 範例會定義 `ContentPage` 包含的 `ListView` 。 `ListView` 的資料來源是透過 `ItemsSource` 屬性設定的。 `ItemsSource` 中每個資料列的配置都是在 `ListView.ItemTemplate` 元素內定義的。 這會導致下列螢幕擷取畫面：

![](data-and-databinding-images/bound-data.png "ListView using Data Binding")

> [!WARNING]
> `ObservableCollection`不是安全線程。 修改 `ObservableCollection` 會導致 UI 更新在執行修改的相同執行緒上發生。 如果執行緒不是主要的 UI 執行緒，則會造成例外狀況。

### <a name="binding-selecteditem"></a>系結 SelectedItem

您通常會想要系結至選取的專案 `ListView` ，而不是使用事件處理常式來回應變更。 若要在 XAML 中執行這項操作，請系結 `SelectedItem` 屬性：

```xaml
<ListView x:Name="listView"
          SelectedItem="{Binding Source={x:Reference SomeLabel},
          Path=Text}">
 …
</ListView>
```

假設 `listView` `ItemsSource` 是字串清單， `SomeLabel` 則會將其屬性系結 `Text` 至 `SelectedItem` 。

## <a name="related-links"></a>相關連結

- [雙向系結（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)
