---
title: 第 19 章的摘要。 集合檢視
description: 使用 Xamarin.Forms 建立行動應用程式：第 19 章的摘要。 集合檢視
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 0AEC3A5C-586E-4D0F-9895-67E99A053A79
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 795478805b582b956ee491bdfecd84485c1bc30e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61334487"
---
# <a name="summary-of-chapter-19-collection-views"></a>第 19 章的摘要。 集合檢視

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)

> [!NOTE] 
> 在此頁面上的附註表示其中 Xamarin.Forms 有分歧活頁簿中所呈現的題材的區域。

Xamarin.Forms 可定義三個檢視維護集合，並顯示其項目：

- [`Picker`](xref:Xamarin.Forms.Picker) 是相對較短的字串項目清單，可讓使用者選擇其中一個
- [`ListView`](xref:Xamarin.Forms.ListView) 通常是一長串的項目通常是相同類型和格式，也可讓使用者選擇其中一個
- [`TableView`](xref:Xamarin.Forms.TableView) 是的集合*儲存格*（通常屬於不同的類型和外觀） 顯示資料或管理使用者輸入

很常見的 MVVM 應用程式，以使用`ListView`顯示可選取物件的集合。

## <a name="program-options-with-picker"></a>使用選取器的程式選項

[ `Picker` ](xref:Xamarin.Forms.Picker)當您需要允許使用者選擇從相對較短的清單中的選項是不錯的選擇`string`項目。

### <a name="the-picker-and-event-handling"></a>選擇器和事件處理

[ **PickerDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerDemo)範例示範如何使用 XAML 來設定`Picker` [ `Title` ](xref:Xamarin.Forms.Picker.Title)屬性，並新增`string`項目[ `Items` ](xref:Xamarin.Forms.Picker.Items)集合。 當使用者選取`Picker`，它會顯示中的項目`Items`平台相依的方式的集合。

[ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged)事件表示當使用者選取項目。 以零為起始[ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex)屬性則表示選取的項目。 如果未不選取任何項目，則`SelectedIndex`等於&ndash;1。

您也可以使用`SelectedIndex`將選取的項目，但它必須設定之後`Items`填滿集合。 在 XAML，這表示您可能會使用 property 項目來設定`SelectedIndex`。

### <a name="data-binding-the-picker"></a>資料繫結選擇器

`SelectedIndex`屬性做為後盾的可繫結的屬性，但`Items`不是，使用資料繫結與`Picker`並不容易。 一個解決方案是使用`Picker`結合[ `ObjectToIndexConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ObjectToIndexConverter.cs)例如[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫。 [ **PickerBinding** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerBinding)示範其運作方式。

> [!NOTE] 
> Xamarin.Forms`Picker`現在包含`ItemsSource`和`SelectedItem`支援資料繫結的屬性。 請參閱[選擇器](~/xamarin-forms/user-interface/picker/index.md)。

## <a name="rendering-data-with-listview"></a>使用 ListView 的轉譯資料

[ `ListView` ](xref:Xamarin.Forms.ListView)是唯一的類別衍生自[ `ItemsView<TVisual>` ](xref:Xamarin.Forms.ItemsView`1)它所繼承[ `ItemsSource` ](xref:Xamarin.Forms.ItemsView`1.ItemsSource)和[ `ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate)屬性。

`ItemsSource` 屬於類型`IEnumerable`但`null`預設且必須明確地初始化或 （通常） 會設透過資料繫結的集合。 在此集合中的項目可以是任何類型。

`ListView` 定義[ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem)屬於其中一個屬性設為其中一項中的項目`ItemsSource`集合或`null`如果在不選取任何項目。 `ListView` 會引發[ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected)選取新的項目時的事件。

### <a name="collections-and-selections"></a>集合與選取項目

[ **ListViewList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewList)範例填滿`ListView`具有 17`Color`中的值`List<Color>`集合。 項目可選取，但根據預設它們會顯示具有其不討喜`ToString`表示法。 在這一章中的數個範例示範如何修正顯示，並使它為視需要吸引人。

### <a name="the-row-separator"></a>資料列分隔符號

在 iOS 和 Android 的顯示，細線區隔資料列。 您可以控制這[ `SeparatorVisibility` ](xref:Xamarin.Forms.ListView.SeparatorVisibility)並[ `SeparatorColor` ](xref:Xamarin.Forms.ListView.SeparatorColor)屬性。 `SeparatorVisibility` 屬性的類型是[ `SeparatorVisibility` ](xref:Xamarin.Forms.SeparatorVisibility)，含有兩個成員的列舉類型：

- [`Default`](xref:Xamarin.Forms.SeparatorVisibility.Default)預設值
- [`None`](xref:Xamarin.Forms.SeparatorVisibility.None)

### <a name="data-binding-the-selected-item"></a>資料繫結選取的項目

`SelectedItem`屬性做為後盾的可繫結的屬性，因此它可以是來源或資料繫結的目標。 其預設值`BindingMode`是`OneWayToSource`，但通常會雙向資料繫結，特別是在 MVVM 案例的目標。 [ **ListViewArray** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewArray)範例會示範這種類型的繫結。

### <a name="the-observablecollection-difference"></a>ObservableCollection 差異

[ **ListViewLogger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewLogger)範例集`ItemsSource`屬性`ListView`至`List<DateTime>`集合，然後逐漸加入新`DateTime`物件加入集合每秒使用計時器。

不過，`ListView`不會自動自行更新因為`List<T>`集合沒有通知機制，以指示當加入或移除集合中項目。

更佳類別將在此情況下是[ `ObservableCollection<T>` ](xref:System.Collections.ObjectModel.ObservableCollection`1)中定義`System.Collections.ObjectModel`命名空間。 這個類別會實作[ `INotifyCollectionChanged` ](xref:System.Collections.Specialized.INotifyCollectionChanged)介面，因此引發[ `CollectionChanged` ](xref:System.Collections.ObjectModel.ObservableCollection`1.CollectionChanged)加入或移除集合中，或取代或內移動時項目時的事件集合中。 當`ListView`內部偵測到類別，實作`INotifyCollectionChanged`已設為其`ItemsSource`屬性，它會將附加至處理常式`CollectionChanged`事件並更新其顯示集合變更時。

[ **ObservableLogger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ObservableLogger)範例示範如何使用`ObservableCollection`。

### <a name="templates-and-cells"></a>範本與資料格

根據預設，`ListView`會顯示使用每個項目其集合中的項目`ToString`方法。 更好的方法牽涉到定義的範本顯示的項目。

若要試驗這項功能，您可以使用[ `NamedColor` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs)類別[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫。 這個類別會定義靜態`All`型別的屬性`IList<NamedColor>`包含 141`NamedColor`物件的公用欄位對應`Color`結構。

[ **NaiveNamedColorList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/NaiveNamedColorList)範例集`ItemsSource`的`ListView`至此`NamedColor.All`屬性，但只有完整類別名稱的`NamedColor`物件顯示此項目。

`ListView` 需要顯示這些項目範本。 在程式碼中，您可以設定[ `ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1.ItemTemplate)屬性所定義`ItemsView<TVisual>`來[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)物件使用[`DataTemplate`建構函式](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Type))，參考的衍生[ `Cell` ](xref:Xamarin.Forms.Cell)類別。 `Cell` 有五個的衍生項目：

- [`TextCell`](xref:Xamarin.Forms.TextCell) &mdash; 包含兩個`Label`檢視 （在概念上來說）
- [`ImageCell`](xref:Xamarin.Forms.ImageCell) &mdash; 新增`Image`檢視 `TextCell`
- [`EntryCell`](xref:Xamarin.Forms.EntryCell) &mdash; 包含`Entry`檢視 `Label`
- [`SwitchCell`](xref:Xamarin.Forms.SwitchCell) &mdash; 包含`Switch`與 `Label`
- [`ViewCell`](xref:Xamarin.Forms.ViewCell) &mdash; 可以是任何`View`（可能具有子系）

然後呼叫[ `SetValue` ](xref:Xamarin.Forms.DataTemplate.SetValue(Xamarin.Forms.BindableProperty,System.Object))並[ `SetBinding` ](xref:Xamarin.Forms.DataTemplate.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase))上`DataTemplate`物件建立關聯的值`Cell`屬性，或設定資料繫結`Cell`屬性參考中的項目屬性`ItemsSource`集合。 這示範於[ **TextCellListCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListCode)範例。

因為每個項目會顯示`ListView`、 小型的視覺化樹狀結構從範本中，建構和資料繫結項目和屬性的項目之間建立此視覺樹狀結構中。 您可以安裝的處理常式，以便取得了解此程序[ `ItemAppearing` ](xref:Xamarin.Forms.ListView.ItemAppearing)並[ `ItemDisappearing` ](xref:Xamarin.Forms.ListView.ItemDisappearing)事件`ListView`，或使用替代[ `DataTemplate`建構函式](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Func{System.Object}))使用項目的視覺化樹狀結構必須建立每次呼叫的函式。

[ **TextCellListXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListXaml)完全在 XAML 中會顯示在功能上完全相同的程式。 A`DataTemplate`標記設定為`ItemTemplate`屬性`ListView`，然後`TextCell`設定為`DataTemplate`。 上直接設定的集合中的項目屬性的繫結[ `Text` ](xref:Xamarin.Forms.TextCell.Text)並[ `Detail` ](xref:Xamarin.Forms.TextCell.Detail)屬性`TextCell`。

### <a name="custom-cells"></a>自訂資料格

在 XAML 中是可以設定[ `ViewCell` ](xref:Xamarin.Forms.ViewCell)要`DataTemplate`，然後定義自訂的視覺化樹狀結構作為[ `View` ](xref:Xamarin.Forms.ViewCell.View)屬性`ViewCell`。 (`View`內容的屬性`ViewCell`因此`ViewCell.View`標記不需要。)[ **CustomNamedColorList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CustomNamedColorList)範例會示範這項技術：

[![自訂具名色彩清單的三個螢幕擷取畫面](images/ch19fg11-small.png "自訂具名色彩清單")](images/ch19fg11-large.png#lightbox "自訂具名色彩清單")

取得適合所有平台的大小可能很難。 [ `RowHeight` ](xref:Xamarin.Forms.ListView.RowHeight)屬性會很有用，但在某些情況下您需要求助於[ `HasUnevenRows` ](xref:Xamarin.Forms.ListView.HasUnevenRows)屬性，這是比較沒有效率，但強制`ListView`調整資料列的大小。 適用於 iOS 和 Android，您必須使用這兩個屬性的其中一個以取得適當的資料列大小。

### <a name="grouping-the-listview-items"></a>將 ListView 項目分組

`ListView` 支援的項目群組，以及這些群組之間巡覽。 `ItemsSource`屬性必須設為集合的集合：物件，`ItemsSource`設定為必須實作`IEnumerable`，並在集合中的每個項目必須同時實作`IEnumerable`。 每個群組應該包含兩個屬性： 群組和三個字母縮寫的文字描述。

[ `NamedColorGroup` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColorGroup.cs)類別[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫會建立七個群組的`NamedColor`物件。 [ **ColorGroupList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorGroupList)範例示範如何使用這些群組以[ `IsGroupingEnabled` ](xref:Xamarin.Forms.ListView.IsGroupingEnabled)屬性`ListView`設`true`，及[ `GroupDisplayBinding` ](xref:Xamarin.Forms.ListView.GroupDisplayBinding)並[ `GroupShortNameBinding` ](xref:Xamarin.Forms.ListView.GroupShortNameBinding)屬性繫結至每個群組中的屬性。

### <a name="custom-group-headers"></a>自訂群組標頭

您可建立自訂標頭`ListView`藉由取代群組`GroupDisplayBinding`屬性[ `GroupHeaderTemplate` ](xref:Xamarin.Forms.ListView.GroupHeaderTemplate)定義標題的範本。

### <a name="listview-and-interactivity"></a>ListView 和互動功能

通常應用程式會取得與使用者互動`ListView`藉由附加至處理常式`ItemSelected`或是[ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped)事件，或藉由設定資料繫結的`SelectedItem`屬性。 但某些資料格類型 (`EntryCell`和`SwitchCell`) 允許使用者互動，同時也是可建立自訂的資料格本身與使用者互動。 [ **InteractiveListView** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/InteractiveListView)建立的 100 個執行個體[ `ColorViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) ，並可讓使用者變更使用的每個色彩`Slider`項目。 程式也會利用[ `ColorToContrastColorConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorToContrastColorConverter.cs)中[ **Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)。

## <a name="listview-and-mvvm"></a>ListView 與 MVVM

`ListView` MVVM 案例中扮演重要角色。 每當`IEnumerable`集合中的 ViewModel，通常繫結至`ListView`。 此外，通常在集合中的項目實作`INotifyPropertyChanged`要與在範本中的屬性繫結。

### <a name="a-collection-of-viewmodels"></a>Viewmodel 的集合

瀏覽這[ **SchoolOfFineArts** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt)程式庫會建立數個類別根據[XML 資料檔](http://xamarin.github.io/xamarin-forms-book-samples/SchoolOfFineArt/students.xml)和映像的虛構的學生，在這個虛構的學校。

[ `Student` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/Student.cs)類別衍生自[ `ViewModelBase` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/ViewModelBase.cs)。 [ `StudentBody` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/StudentBody.cs)類別是集合`Student`物件，並也是衍生自`ViewModelBase`。 [ `SchoolViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/SchoolViewModel.cs)下載 XML 檔案，並將所有物件的都組合。

[ **StudentList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/StudentList)程式會使用`ImageCell`以顯示學生和其映像中的`ListView`:

[![學生清單的三個螢幕擷取畫面](images/ch19fg18-small.png "學生清單")](images/ch19fg18-large.png#lightbox "學生清單")

[ **ListViewHeader** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewHeader)範例會將[ `Header` ](xref:Xamarin.Forms.ListView.Header)屬性，但它只會顯示在 Android 上。

### <a name="selection-and-the-binding-context"></a>選取項目和繫結內容

[ **SelectedStudentDetail** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/SelectedStudentDetail)程式繫結`BindingContext`的`StackLayout`至`SelectedItem`屬性`ListView`。 這可讓程式，以顯示所選學生的詳細的資訊。

### <a name="context-menus"></a>操作功能表

資料格可以定義內容功能表中的平台特定的方式實作。 若要建立這個功能表中，新增[ `MenuItem` ](xref:Xamarin.Forms.MenuItem)物件來[ `ContextActions` ](xref:Xamarin.Forms.Cell.ContextActions)屬性`Cell`。

`MenuItem` 會定義五個屬性：

- [`Text`](xref:Xamarin.Forms.MenuItem.Text) 型別 `string`
- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) 型別 `FileImageSource`
- [`IsDestructive`](xref:Xamarin.Forms.MenuItem.IsDestructive) 型別 `bool`
- [`Command`](xref:Xamarin.Forms.MenuItem.Command) 型別 `ICommand`
- [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter) 型別 `object`

`Command`和`CommandParameter`屬性代表每個項目 ViewModel 包含執行所需的功能表命令的方法。 在非 MVVM 案例中，`MenuItem`也會定義[ `Clicked` ](xref:Xamarin.Forms.MenuItem.Clicked)事件。

[ **CellContextMenu** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CellContextMenu)示範這項技巧。 `Command`屬性的每個`MenuItem`繫結至型別的屬性`ICommand`在`Student`類別。 設定`IsDestructive`屬性，以`true`如`MenuItem`，移除或刪除選取的物件。

### <a name="varying-the-visuals"></a>不同的視覺效果

有時候您會想在 視覺效果中的項目會有些微差異`ListView`屬性為基礎。 例如，當某學生的成績點平均值低於 2.0， [ **ColorCodedStudents** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorCodedStudents)範例以紅色顯示該學生的名稱。
透過使用繫結值轉換器，即可達成[ `ThresholdToObjectConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ThresholdToObjectConverter.cs)，在[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫。

### <a name="refreshing-the-content"></a>正在重新整理內容

`ListView`支援下拉式軌跡的重新整理其資料。 程式必須設定[ `IsPullToRefresh` ](xref:Xamarin.Forms.ListView.IsPullToRefreshEnabled)屬性設`true`若要這麼做。 `ListView`藉由設定回應下拉式軌跡其[ `IsRefreshing` ](xref:Xamarin.Forms.ListView.IsRefreshing)屬性設`true`，以及透過引發[ `Refreshing` ](xref:Xamarin.Forms.ListView.Refreshing)事件並 （適用於 MVVM 案例） 呼叫`Execute`的方法與其[ `RefreshCommand` ](xref:Xamarin.Forms.ListView.RefreshCommand)屬性。

程式碼處理`Refresh`事件或`RefreshCommand`可能是更新顯示的資料`ListView`，並設定`IsRefreshing`回到`false`。

[ **RssFeed** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/RssFeed)範例示範如何使用[ `RssFeedViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/RssFeed/RssFeed/RssFeed/RssFeedViewModel.cs)實作`RefreshCommand`和`IsRefreshing`資料繫結屬性。

## <a name="the-tableview-and-its-intents"></a>TableView 和其對應方式

雖然`ListView`通常會顯示多個相同類型，執行個體[ `TableView` ](xref:Xamarin.Forms.TableView)通常著重於提供各種類型的多個屬性的使用者介面。 每個項目會與自己相關聯[ `Cell` ](xref:Xamarin.Forms.Cell)衍生顯示屬性，或提供給它的使用者介面。

### <a name="properties-and-hierarchies"></a>屬性和階層

`TableView` 定義只有四個屬性：

- [`Intent`](xref:Xamarin.Forms.TableView.Intent) 型別的[ `TableIntent` ](xref:Xamarin.Forms.TableIntent)，列舉型別
- [`Root`](xref:Xamarin.Forms.TableView.Root) 型別的[ `TableRoot`](xref:Xamarin.Forms.TableRoot)的內容屬性 `TableView`
- [`RowHeight`](xref:Xamarin.Forms.TableView.RowHeight) 型別 `int`
- [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows) 型別 `bool`

`TableIntent`列舉，指出您想要使用的方式`TableView`:

- [`Data`](xref:Xamarin.Forms.TableIntent.Data)
- [`Form`](xref:Xamarin.Forms.TableIntent.Form)
- [`Settings`](xref:Xamarin.Forms.TableIntent.Settings)
- [`Menu`](xref:Xamarin.Forms.TableIntent.Menu)

這些成員也會建議的部分用法`TableView`。

參與數個其他類別中定義的資料表：

- [`TableSectionBase`](xref:Xamarin.Forms.TableSectionBase) 是抽象類別衍生自`BindableObject`，並定義[ `Title` ](xref:Xamarin.Forms.TableSectionBase.Title)屬性

- [`TableSectionBase<T>`](xref:Xamarin.Forms.TableSectionBase`1) 是抽象類別衍生自`TableSectionBase`並實作`IList<T>`和 `INotifyCollectionChanged`

- [`TableSection`](xref:Xamarin.Forms.TableSection) 衍生自 `TableSectionBase<Cell>`

- [`TableRoot`](xref:Xamarin.Forms.TableRoot) 衍生自 `TableSectionBase<TableSection>`

簡單地說，`TableView`已經`Root`屬性，您將設定為`TableRoot`物件，它是集合的`TableSection`物件，其中每個集合`Cell`物件。 資料表有多個區段，且每個區段會有多個資料格。 資料表本身可以有標題，且每個區段可以有標題。 雖然`TableView`利用`Cell`衍生項目，不會使用`DataTemplate`。

### <a name="a-prosaic-form"></a>Prosaic 表單

[ **EntryForm** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/EntryForm)範例會定義[ `PersonalInformation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs)檢視模型，其中的執行個體會變成`BindingContext`的`TableView`。 每個`Cell`衍生在其`TableSection`隨後可以繫結的屬性`PersonalInformation`類別。

### <a name="custom-cells"></a>自訂資料格

[ **ConditionalCells** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalCells)範例為基礎來延伸**EntryForm**。 [ `ProgrammerInformation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs)類別包含布林值屬性，其可控制兩個額外屬性的適用性。 這兩個額外的屬性，程式會使用自訂`PickerCell`根據[PickerCell.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml)並[PickerCell.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml.cs)中[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫。

雖然`IsEnabled`兩個屬性`PickerCell`項目繫結至布林值屬性，在`ProgrammerInformation`，這項技術似乎不會運作，並在提示下一個範例。

### <a name="conditional-sections"></a>條件式區段

[ **ConditionalSection** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalSection)範例會將放在個別的布林值項目在選取範圍上的條件式的兩個項目`TableSection`。 程式碼後置檔案就會移除這個區段從`TableView`或新增回根據布林值屬性。

### <a name="a-tableview-menu"></a>TableView 功能表

另一個使用`TableView`功能表。 [ **Menucommand** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/MenuCommands)範例會示範一個功能表，並可讓您稍微移動`BoxView`在螢幕上。



## <a name="related-links"></a>相關連結

- [第 19 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch19-Apr2016.pdf)
- [第 19 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)
- [選擇器](~/xamarin-forms/user-interface/picker/index.md)
- [ListView](~/xamarin-forms/user-interface/listview/index.md)
- [TableView](~/xamarin-forms/user-interface/tableview.md)
