---
title: ListView 的資料來源
description: 這篇文章說明如何填入 Xamarin.Forms ListView 的資料，以及如何使用 ListView 中的資料繫結。
ms.prod: xamarin
ms.assetid: B5571660-1E82-4379-95C3-0725288CF5D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 17c353844a7ddc808e5d9f0632434472913170a4
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995202"
---
# <a name="listview-data-sources"></a>ListView 的資料來源

ListView 是用來顯示資料的清單。 我們將了解填入 ListView 與資料，以及我們如何可以將繫結至選取的項目。

- **[設定 ItemsSource](#ItemsSource)**  &ndash;使用簡易清單或陣列。
- **[資料繫結](#Data_Binding)** &ndash;建立模型與 ListView 之間的關聯性。 繫結適合 MVVM 模式。

## <a name="itemssource"></a>ItemsSource
使用資料填入 ListView`ItemsSource`屬性，它可以接受任何集合，實作`IEnumerable`。 最簡單的方式，來填入`ListView`牽涉到使用字串的陣列：

```csharp
var listView = new ListView();
listView.ItemsSource = new string[]{
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

//monochrome will not appear in the list because it was added
//after the list was populated.
listView.ItemsSource.Add("monochrome");
```

![](data-and-databinding-images/itemssource-simple.png "ListView 顯示的字串清單，")

上述的方法將會填入`ListView`字串的清單。 根據預設，`ListView`稱之為`ToString`，並顯示在結果`TextCell`每個資料列。 若要自訂 顯示資料的方式，請參閱[儲存格的外觀](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)。

因為`ItemsSource`已傳送到陣列中，內容將不會更新為基礎的清單或陣列變更。 如果您想要自動更新，如新增、 移除和變更的基礎清單中的項目 ListView 時，您必須使用`ObservableCollection`。 [`ObservableCollection`](xref:System.Collections.ObjectModel.ObservableCollection`1) 定義於`System.Collections.ObjectModel`一樣，而且`List`，只不過它就會通知`ListView`的任何變更：

```csharp
ObservableCollection<Employees> employeeList = new ObservableCollection<Employess>();
listView.ItemsSource = employeeList;

//Mr. Mono will be added to the ListView because it uses an ObservableCollection
employeeList.Add(new Employee(){ DisplayName="Mr. Mono"});
```

<a name="Data_Binding" />

## <a name="data-binding"></a>資料繫結
資料繫結是 「 黏附 」 使用者介面物件的屬性所繫結的某些 CLR 物件，例如在您的 ViewModel 類別的屬性。 資料繫結是很有用，因為它藉由取代許多無聊的未定案程式碼簡化使用者介面的開發。

資料繫結的運作方式是讓物件保持同步，因為其繫結的值變更。 而不需要撰寫事件處理常式，每當控制項的值變更時，您可以建立繫結，並啟用繫結，在您的 ViewModel。

如需有關資料繫結的詳細資訊，請參閱 <<c0> [ 資料繫結的基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)這是部分的四[Xamarin.Forms XAML 基本知識文章系列](~/xamarin-forms/xaml/xaml-basics/index.md)。

### <a name="binding-cells"></a>儲存格繫結
資料格 （和資料格的子系） 的屬性可以繫結中物件的屬性至`ItemsSource`。 比方說，ListView 可用來呈現的映像的員工清單。

「 員工 」 類別：

```csharp
public class Employee{
    public string DisplayName {get; set;}
}
```

`ObservableCollection<Employee>` 已建立並設定為`ListView`的`ItemsSource`:

```csharp
ObservableCollection<Employee> employees = new ObservableCollection<Employee>();
public EmployeeListPage()
{
  //defined in XAML to follow
  EmployeeView.ItemsSource = employees;
  ...
}
```

清單會填入資料：

```csharp
public EmployeeListPage()
{
  ...
  employees.Add(new Employee{ DisplayName="Rob Finnerty"});
  employees.Add(new Employee{ DisplayName="Bill Wrestler"});
  employees.Add(new Employee{ DisplayName="Dr. Geri-Beth Hooper"});
  employees.Add(new Employee{ DisplayName="Dr. Keith Joyce-Purdy"});
  employees.Add(new Employee{ DisplayName="Sheri Spruce"});
  employees.Add(new Employee{ DisplayName="Burt Indybrick"});
}
```

下列程式碼片段示範`ListView`繫結至的員工清單：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
xmlns:constants="clr-namespace:XamarinFormsSample;assembly=XamarinFormsXamlSample"
x:Class="XamarinFormsXamlSample.Views.EmployeeListPage"
Title="Employee List">
  <ListView x:Name="EmployeeView">
    <ListView.ItemTemplate>
      <DataTemplate>
        <TextCell Text="{Binding DisplayName}" />
      </DataTemplate>
    </ListView.ItemTemplate>
  </ListView>
</ContentPage>
```

雖然它可能已繫結在 XAML 中，請注意繫結已為了簡單起見，程式碼中的設定。

XAML 的上一個位元會定義`ContentPage`，其中包含`ListView`。 資料來源`ListView`透過設定`ItemsSource`屬性。 每個資料列的版面配置`ItemsSource`內定義`ListView.ItemTemplate`項目。

結果如下：

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

假設`listView`的`ItemsSource`是一份字串`SomeLabel`會有繫結至其 text 屬性`SelectedItem`。



## <a name="related-links"></a>相關連結

- [雙向繫結 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/SwitchEntryTwoBinding)
- [1.4 的版本資訊](http://forums.xamarin.com/discussion/35451/xamarin-forms-1-4-0-released/)
- [1.3 的版本資訊](http://forums.xamarin.com/discussion/29934/xamarin-forms-1-3-0-released/)
