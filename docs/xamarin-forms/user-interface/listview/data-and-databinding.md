---
title: ListView 資料來源
description: 本文說明如何在 Xamarin.Forms listview 中填入資料，以及如何搭配使用資料系結與 listview。
ms.prod: xamarin
ms.assetid: B5571660-1E82-4379-95C3-0725288CF5D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c77a219ab1b729aa279708d04610911fbbfe4e81
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91560126"
---
# <a name="listview-data-sources"></a>ListView 資料來源

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)

Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) 用於顯示資料的清單。 本文說明如何在中填入 `ListView` 資料，以及如何將資料系結至選取的專案。

## <a name="itemssource"></a>ItemsSource

[`ListView`](xref:Xamarin.Forms.ListView)會使用 [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) 屬性來填入資料，而此屬性可以接受任何進行中的集合 `IEnumerable` 。 填入的最簡單方式 `ListView` 牽涉到使用字串陣列：

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

![顯示字串清單的 ListView](data-and-databinding-images/itemssource-simple.png)

這種方法會在中填入 `ListView` 字串清單。 依預設， `ListView` 會呼叫 `ToString` 並在每個資料列的中顯示結果 `TextCell` 。 若要自訂顯示資料的方式，請參閱資料 [格外觀](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)。

因為 `ItemsSource` 已傳送至陣列，所以內容將不會隨著基礎清單或陣列變更而更新。 如果您想要讓 ListView 在基礎清單中加入、移除和變更專案時自動更新，您必須使用 `ObservableCollection` 。 [`ObservableCollection`](xref:System.Collections.ObjectModel.ObservableCollection`1) 是在中定義 `System.Collections.ObjectModel` ，而且與相同 `List` ，不同之處在于它可以通知 `ListView` 任何變更：

```csharp
ObservableCollection<Employee> employees = new ObservableCollection<Employee>();
listView.ItemsSource = employees;

//Mr. Mono will be added to the ListView because it uses an ObservableCollection
employees.Add(new Employee(){ DisplayName="Mr. Mono"});
```

## <a name="data-binding"></a>資料繫結

資料系結是將使用者介面物件的屬性系結至某些 CLR 物件（例如 viewmodel 中的類別）之屬性的「粘連」。 資料系結會很有用，因為它會取代許多單調乏味的程式碼，以簡化使用者介面的開發。

資料系結的運作方式是讓物件在系結值變更時保持同步。 您不必在每次控制項的值變更時撰寫事件處理常式，而是在 viewmodel 中建立系結並啟用系結。

如需資料系結的詳細資訊，請參閱資料系結的[基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)，這是[ Xamarin.Forms XAML 基本專案系列](~/xamarin-forms/xaml/xaml-basics/index.md)的第四部分。

### <a name="binding-cells"></a>系結儲存格

資料格 (的屬性和資料格的子系) 可以系結至中的物件屬性 `ItemsSource` 。 例如， `ListView` 可以用來顯示員工清單。

Employee 類別：

```csharp
public class Employee
{
    public string DisplayName {get; set;}
}
```

`ObservableCollection<Employee>`會建立、設定為 `ListView` `ItemsSource` ，且清單會填入資料：

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
> 雖然 `ListView` 會更新以回應其基礎的變更，但 `ObservableCollection` 如果將 `ListView` 不同的實例指派給原始參考，則不會更新 `ObservableCollection` `ObservableCollection` (例如 `employees = otherObservableCollection;`) 。

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

這個 XAML 範例會定義 `ContentPage` 包含的 `ListView` 。 `ListView` 的資料來源是透過 `ItemsSource` 屬性設定的。 `ItemsSource` 中每個資料列的配置都是在 `ListView.ItemTemplate` 元素內定義的。 這會導致下列螢幕擷取畫面：

![使用資料系結的 ListView](data-and-databinding-images/bound-data.png)

> [!WARNING]
> `ObservableCollection` 不是安全線程。 修改 `ObservableCollection` 會導致 UI 更新在執行修改的相同執行緒上發生。 如果執行緒不是主要 UI 執行緒，則會造成例外狀況。

### <a name="binding-selecteditem"></a>系結 SelectedItem

通常您會想要系結至選取的專案 `ListView` ，而不是使用事件處理常式來回應變更。 若要在 XAML 中執行這項操作，請系結 `SelectedItem` 屬性：

```xaml
<ListView x:Name="listView"
          SelectedItem="{Binding Source={x:Reference SomeLabel},
          Path=Text}">
 …
</ListView>
```

假設 `listView` `ItemsSource` 是字串清單， `SomeLabel` 會將其屬性系結 `Text` 至 `SelectedItem` 。

## <a name="related-links"></a>相關連結

- [雙向綁定 (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)