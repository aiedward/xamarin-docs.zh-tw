---
title: 第 19 章的摘要。 集合檢視
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 0AEC3A5C-586E-4D0F-9895-67E99A053A79
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 8aabf469f877fdb3ec703c2e3fdab13fcb25975a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="summary-of-chapter-19-collection-views"></a>第 19 章的摘要。 集合檢視

Xamarin.Forms 會定義三個檢視的維護集合並顯示其項目：

- [`Picker`](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/) 是相對較短的字串項目清單，可讓使用者選擇其中一個
- [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) 通常是一長串的項目通常屬於相同類型和格式，還可讓使用者選擇其中一個
- [`TableView`](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/) 是一組*儲存格*（通常的各種型別和外觀） 來顯示資料或管理使用者輸入

它是很常見的 MVVM 應用程式使用`ListView`以顯示可選取物件的集合。

## <a name="program-options-with-picker"></a>程式選項 選擇器

[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)時您需要允許使用者選擇從相對較短的清單中的選項是不錯的選擇`string`項目。

### <a name="the-picker-and-event-handling"></a>選擇器和事件處理

[ **PickerDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerDemo)範例示範如何使用 XAML 設定`Picker` [ `Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Title/)屬性並新增`string`項目[ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/)集合。 當使用者選取`Picker`，它會顯示中的項目`Items`平台相關的方式的集合。

[ `SelectedIndexChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Picker.SelectedIndexChanged/)事件表示當使用者選取項目。 以零為起始[ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/)屬性則表示選取的項目。 如果未不選取任何項目，則`SelectedIndex`等於&ndash;1。

您也可以使用`SelectedIndex`初始化 選取的項目，但它必須設定為晚`Items`填滿集合。 在 XAML 中，這表示您可能會使用屬性項目來設定`SelectedIndex`。

### <a name="data-binding-the-picker"></a>資料繫結選擇器

`SelectedIndex`屬性可繫結的屬性所支援但`Items`不是，使用資料繫結與`Picker`困難。 一個解決方案是使用`Picker`搭配[ `ObjectToIndexConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ObjectToIndexConverter.cs)例如[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫。 [ **PickerBinding** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerBinding)示範其運作方式。

## <a name="rendering-data-with-listview"></a>轉譯資料的 ListView

[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)是唯一的類別，衍生自[ `ItemsView<TVisual>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/)它所繼承[ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemsSource/)和[ `ItemTemplate`](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemTemplate/)屬性。

`ItemsSource` 型別`IEnumerable`但`null`預設且必須明確地初始化或 （通常） 會將透過資料繫結的集合。 在此集合中的項目可以是任何類型。

`ListView` 定義[ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.SelectedItem/) ，可能是屬性設定為其中一個項目中`ItemsSource`集合或`null`如果不選取任何項目。 `ListView` 引發[ `ItemSelected` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemSelected/)選取新的項目時的事件。

### <a name="collections-and-selections"></a>集合與選取項目

[ **ListViewList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewList)範例填滿`ListView`與 17`Color`值`List<Color>`集合。 可選取的項目，包括但依預設會顯示具有其難看`ToString`表示相互轉換。 在本章中的數個範例示範如何修正顯示，並且讓它為視需要吸引人。

### <a name="the-row-separator"></a>資料列分隔符號

在 iOS 和 Android 的顯示，單一細線條分隔之資料列。 您可以控制這點與[ `SeparatorVisibiliy` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.SeparatorVisibility/)和[ `SeparatorColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.SeparatorColor/)屬性。 `SeparatorVisibility` 屬性為類型[ `SeparatorVisbility` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SeparatorVisibility/)，具有兩個成員的列舉：

- [`Default`](https://developer.xamarin.com/api/field/Xamarin.Forms.SeparatorVisibility.Default/)預設值
- [`None`](https://developer.xamarin.com/api/field/Xamarin.Forms.SeparatorVisibility.None/)

### <a name="data-binding-the-selected-item"></a>資料繫結選取的項目

`SelectedItem`屬性受到可繫結的屬性，因此它可以是來源或目標的資料繫結。 其預設`BindingMode`是`OneWayToSource`，但通常會雙向資料繫結，特別是在 MVVM 案例的目標。 [ **ListViewArray** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewArray)範例會示範這種類型的繫結。

### <a name="the-observablecollection-difference"></a>ObservableCollection 差異

[ **ListViewLogger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewLogger)範例集`ItemsSource`屬性`ListView`至`List<DateTime>`集合，然後逐漸新增新`DateTime`物件加入至集合每秒使用計時器。

不過，`ListView`不會自動自行更新因為`List<T>`集合沒有以表示當項目加入或移除集合中的通知機制。

更好類別来使用這類案例中是[ `ObservableCollection<T>` ](https://developer.xamarin.com/api/type/System.Collections.ObjectModel.ObservableCollection%3CT%3E/)中定義`System.Collections.ObjectModel`命名空間。 這個類別會實作[ `INotifyCollectionChanged` ](https://developer.xamarin.com/api/type/System.Collections.Specialized.INotifyCollectionChanged/)介面，因而導致引發[ `CollectionChanged` ](https://developer.xamarin.com/api/event/System.Collections.ObjectModel.ObservableCollection%3CT%3E.CollectionChanged/)事件加入或移除集合中，從或時，會取代中移動項目時集合中。 當`ListView`內部偵測到類別實作`INotifyCollectionChanged`已設定為其`ItemsSource`屬性，它會將附加至處理常式`CollectionChanged`事件，並更新其顯示集合變更時。

[ **ObservableLogger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ObservableLogger)範例將示範如何使用`ObservableCollection`。

### <a name="templates-and-cells"></a>樣板和資料格

根據預設，`ListView`使用每個項目其集合中會顯示項目`ToString`方法。 更好的方法需要定義顯示的項目範本。

若要試驗這項功能，您可以使用[ `NamedColor` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs)類別[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫。 這個類別會定義靜態`All`型別的屬性`IList<NamedColor>`包含 141`NamedColor`物件對應於的公用欄位`Color`結構。

[ **NaiveNamedColorList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/NaiveNamedColorList)範例集`ItemsSource`的`ListView`至此`NamedColor.All`屬性，但是只有完整類別名稱的`NamedColor`物件顯示。

`ListView` 需要範本以顯示這些項目。 在程式碼中，您可以設定[ `ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemTemplate/)屬性所定義`ItemsView<TVisual>`至[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)物件使用[`DataTemplate`建構函式](https://developer.xamarin.com/api/constructor/Xamarin.Forms.DataTemplate.DataTemplate/p/System.Type/)，參考的衍生[ `Cell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/)類別。 `Cell` 有五個衍生項目：

- [`TextCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell/) &mdash; 包含兩個`Label`（概念上來說） 的檢視
- [`ImageCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell/) &mdash; 新增`Image`檢視 `TextCell`
- [`EntryCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.EntryCell/) &mdash; 包含`Entry`與檢視 `Label`
- [`SwitchCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.SwitchCell/) &mdash; 包含`Switch`與 `Label`
- [`ViewCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) &mdash; 可以是任何`View`（可能含有子系）

然後呼叫[ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.DataTemplate.SetValue/p/Xamarin.Forms.BindableProperty/System.Object/)和[ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.DataTemplate.SetBinding/p/Xamarin.Forms.BindableProperty/Xamarin.Forms.BindingBase/)上`DataTemplate`要產生關聯的值物件`Cell`屬性，或是為資料繫結設定的`Cell`屬性參考中的項目屬性`ItemsSource`集合。 這示範於[ **TextCellListCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListCode)範例。

由於每個項目會顯示由`ListView`、 小型的視覺化樹狀結構從這個範本時，所建構，以及此視覺化樹狀結構中建立資料繫結項目和屬性之間的項目。 您可以安裝的處理常式，以取得此程序的了解[ `ItemAppearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemAppearing/)和[ `ItemDisappearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemDisappearing/)事件`ListView`，或使用替代[ `DataTemplate`建構函式](https://developer.xamarin.com/api/constructor/Xamarin.Forms.DataTemplate.DataTemplate/p/System.Func%7BSystem.Object%7D/)使用項目的視覺化樹狀結構必須建立每次呼叫的函式。

[ **TextCellListXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListXaml)可在 XAML 中會顯示在功能上完全相同的程式。 A`DataTemplate`標記設定為`ItemTemplate`屬性`ListView`，然後`TextCell`設`DataTemplate`。 集合中的項目屬性的繫結上直接設定[ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TextCell.Text/)和[ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TextCell.Detail/)屬性`TextCell`。

### <a name="custom-cells"></a>自訂儲存格

在 XAML 中是可以設定[ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/)至`DataTemplate`然後定義自訂的視覺化樹狀結構當做[ `View` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ViewCell.View/)屬性`ViewCell`。 (`View`內容屬性的`ViewCell`所以`ViewCell.View`標記不需要。)[ **CustomNamedColorList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CustomNamedColorList)範例會示範這項技術：

[![三個螢幕擷取畫面的自訂具名色彩清單](images/ch19fg11-small.png "自訂具名色彩清單")](images/ch19fg11-large.png#lightbox "自訂具名色彩清單")

取得權限的所有平台的大小可能很困難。 [ `RowHeight` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.RowHeight/)屬性非常實用，但是在某些情況下，您會想求助於[ `HasUnevenRows` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.HasUnevenRows/)比較沒有效率的內容，但強制`ListView`調整資料列的大小。 適用於 iOS 和 Android，您必須使用這兩個屬性的其中一個以取得適當的資料列大小。

### <a name="grouping-the-listview-items"></a>將 ListView 項目分組

`ListView` 支援的項目群組，以及這些群組之間巡覽。 `ItemsSource`屬性必須設定為集合的集合： 物件的`ItemsSource`設定為必須實作`IEnumerable`，並在集合中的每個項目也必須實作`IEnumerable`。 每個群組應該包含兩個屬性： 群組和三個字母縮寫的文字描述。

[ `NamedColorGroup` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColorGroup.cs)類別[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫會建立七個群組的`NamedColor`物件。 [ **ColorGroupList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorGroupList)範例示範如何使用具有這些群組[ `IsGroupingEnabled` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.IsGroupingEnabled/)屬性`ListView`設`true`，和[ `GroupDisplayBinding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.GroupDisplayBinding/)和[ `GroupShortNameBinding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.GroupShortNameBinding/)屬性繫結至每個群組中的屬性。

### <a name="custom-group-headers"></a>自訂群組標頭

您可建立自訂標頭`ListView`群組取代`GroupDisplayBinding`屬性與[ `GroupHeaderTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.GroupHeaderTemplate/)定義範本的標頭。

### <a name="listview-and-interactivity"></a>ListView 和互動功能

應用程式通常會取得與使用者互動`ListView`藉由附加至處理常式`ItemSelected`或[ `ItemTapped` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemTapped/)事件，或藉由設定資料繫結上的`SelectedItem`屬性。 但某些資料格資料型別 (`EntryCell`和`SwitchCell`) 讓使用者互動，而且也可以建立自訂的資料格本身與使用者互動。 [ **InteractiveListView** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/InteractiveListView)建立 100 個執行個體的[ `ColorViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs)並允許使用者變更使用三個每個色彩`Slider`項目。 程式也會使用[ `ColorToContrastColorConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorToContrastColorConverter.cs)中[ **Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)。

## <a name="listview-and-mvvm"></a>ListView 與 MVVM

`ListView` MVVM 案例中扮演重要角色。 每當`IEnumerable`ViewModel 存在於集合中，通常繫結至`ListView`。 此外，通常在集合中的項目實作`INotifyPropertyChanged`要與在範本中的屬性繫結。

### <a name="a-collection-of-viewmodels"></a>ViewModels 的集合

若要瀏覽， [ **SchoolOfFineArts** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt)程式庫會建立數個類別，根據[XML 資料檔](http://xamarin.github.io/xamarin-forms-book-samples/SchoolOfFineArt/students.xml)的虛構這個虛構的學校學生和影像。

[ `Student` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/Student.cs)類別衍生自[ `ViewModelBase` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/ViewModelBase.cs)。 [ `StudentBody` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/StudentBody.cs)類別是集合`Student`物件，並也衍生自`ViewModelBase`。 [ `SchoolViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/SchoolViewModel.cs)下載 XML 檔案，並組合的所有物件。

[ **StudentList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/StudentList)程式使用`ImageCell`顯示學生版和其映像中的`ListView`:

[![三個螢幕擷取畫面的學生清單](images/ch19fg18-small.png "學生清單")](images/ch19fg18-large.png#lightbox "學生清單")

[ **ListViewHeader** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewHeader)範例會將[ `Header` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.Header/)的內容，但只在 Android 上會出現。

### <a name="selection-and-the-binding-context"></a>選取項目和繫結內容

[ **SelectedStudentDetail** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/SelectedStudentDetail)程式繫結`BindingContext`的`StackLayout`至`SelectedItem`屬性`ListView`。 這可讓程式能夠顯示所選的學生的詳細的資訊。

### <a name="context-menus"></a>操作功能表

資料格可以定義特定平台的方式實作的內容功能表。 若要建立這個功能表，新增[ `MenuItem` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MenuItem/)物件加入至[ `ContextActions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Cell.ContextActions/)屬性`Cell`。

`MenuItem` 定義五個屬性：

- [`Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.MenuItem.Text/) 型別 `string`
- [`Icon`](https://developer.xamarin.com/api/property/Xamarin.Forms.MenuItem.Icon/) 型別 `FileImageSource`
- [`IsDestructive`](https://developer.xamarin.com/api/property/Xamarin.Forms.MenuItem.IsDestructive/) 型別 `bool`
- [`Command`](https://developer.xamarin.com/api/property/Xamarin.Forms.MenuItem.Command/) 型別 `ICommand`
- [`CommandParameter`](https://developer.xamarin.com/api/property/Xamarin.Forms.MenuItem.CommandParameter/) 型別 `object`

`Command`和`CommandParameter`屬性代表每個項目的 ViewModel 包含方法，以執行所需的功能表命令。 在非 MVVM 案例中，`MenuItem`也會定義[ `Clicked` ](https://developer.xamarin.com/api/event/Xamarin.Forms.MenuItem.Clicked/)事件。

[ **CellContextMenu** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CellContextMenu)示範這項技巧。 `Command`每個屬性`MenuItem`繫結至型別的屬性`ICommand`中`Student`類別。 設定`IsDestructive`屬性`true`如`MenuItem`，移除或刪除選取的物件。

### <a name="varying-the-visuals"></a>不同的視覺效果

有時候您會想在視覺效果中的項目會有些微差異`ListView`屬性為基礎。 例如，當一位學生成績點平均值低於 2.0， [ **ColorCodedStudents** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorCodedStudents)範例會以紅色顯示該學生的名稱。
這藉由使用繫結值轉換器，來完成[ `ThresholdToObjectConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ThresholdToObjectConverter.cs)，請在[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫。

### <a name="refreshing-the-content"></a>正在重新整理內容

`ListView`支援補償手勢重新整理其資料。 必須設定程式[ `IsPullToRefresh` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.IsPullToRefreshEnabled/)屬性`true`啟用此設定。 `ListView`補償筆勢回應藉由設定其[ `IsRefreshing` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.IsRefreshing/)屬性`true`，並藉由引發[ `Refreshing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.Refreshing/)事件並 （適用於 MVVM 案例） 呼叫`Execute`方法及其[ `RefreshCommand` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.RefreshCommand/)屬性。

程式碼處理`Refresh`事件或`RefreshCommand`可能更新顯示的資料`ListView`並設定`IsRefreshing`回到`false`。

[ **RssFeed** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/RssFeed)範例將示範如何使用[ `RssFeedViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/RssFeed/RssFeed/RssFeed/RssFeedViewModel.cs)實作`RefreshCommand`和`IsRefreshing`資料繫結屬性。

## <a name="the-tableview-and-its-intents"></a>TableView 和其對應方式

雖然`ListView`通常會顯示多個執行個體屬於相同的類型， [ `TableView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/)通常專注於提供的使用者介面為各種類型的多個屬性。 每個項目是與它自己相關聯[ `Cell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/)衍生顯示屬性，或提供給它的使用者介面。

### <a name="properties-and-hierarchies"></a>屬性和階層

`TableView` 定義只能有四個屬性：

- [`Intent`](https://developer.xamarin.com/api/property/Xamarin.Forms.TableView.Intent/) 型別的[ `TableIntent` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TableIntent/)，列舉型別
- [`Root`](https://developer.xamarin.com/api/property/Xamarin.Forms.TableView.Root/) 型別的[ `TableRoot`](https://developer.xamarin.com/api/type/Xamarin.Forms.TableRoot/)的內容屬性 `TableView`
- [`RowHeight`](https://developer.xamarin.com/api/property/Xamarin.Forms.TableView.RowHeight/) 型別 `int`
- [`HasUnevenRows`](https://developer.xamarin.com/api/property/Xamarin.Forms.TableView.HasUnevenRows/) 型別 `bool`

`TableIntent`列舉，指出您想要使用的方式`TableView`:

- [`Data`](https://developer.xamarin.com/api/field/Xamarin.Forms.TableIntent.Data/)
- [`Form`](https://developer.xamarin.com/api/field/Xamarin.Forms.TableIntent.Form/)
- [`Settings`](https://developer.xamarin.com/api/field/Xamarin.Forms.TableIntent.Settings/)
- [`Menu`](https://developer.xamarin.com/api/field/Xamarin.Forms.TableIntent.Menu/)

這些成員也會建議的一些用法`TableView`。

參與數個其他類別中定義的資料表：

- [`TableSectionBase`](https://developer.xamarin.com/api/type/Xamarin.Forms.TableSectionBase/) 是抽象類別衍生自`BindableObject`並定義[ `Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TableSectionBase.Title/)屬性

- [`TableSectionBase<T>`](https://developer.xamarin.com/api/type/Xamarin.Forms.TableSectionBase%3CT%3E/) 是抽象類別衍生自`TableSectionBase`並實作`IList<T>`和 `INotifyCollectionChanged`

- [`TableSection`](https://developer.xamarin.com/api/type/Xamarin.Forms.TableSection/) 衍生自 `TableSectionBase<Cell>`

- [`TableRoot`](https://developer.xamarin.com/api/type/Xamarin.Forms.TableRoot/) 衍生自 `TableSectionBase<TableSection>`

簡單地說，`TableView`具有`Root`屬性設為`TableRoot`物件，它是集合的`TableSection`物件，其中每一個都是一組`Cell`物件。 資料表有多個區段，以及每個區段具有多個資料格。 資料表本身可以有標題，而每個區段可以有標題。 雖然`TableView`利用`Cell`衍生項目，不會使用`DataTemplate`。

### <a name="a-prosaic-form"></a>Prosaic 表單

[ **EntryForm** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/EntryForm)範例會定義[ `PersonalInformation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs)檢視模型，其中的執行個體會變成`BindingContext`的`TableView`。 每個`Cell`中衍生其`TableSection`就可以讓屬性的繫結`PersonalInformation`類別。

### <a name="custom-cells"></a>自訂儲存格

[ **ConditionalCells** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalCells)範例詳述**EntryForm**。 [ `ProgrammerInformation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs)類別包含布林值屬性，其可控制兩個其他屬性的適用性。 對於這兩個額外的屬性，程式會使用自訂`PickerCell`根據[PickerCell.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml)和[PickerCell.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml.cs)中[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫。

雖然`IsEnabled`的兩個屬性`PickerCell`繫結項目中的布林屬性`ProgrammerInformation`，這項技術似乎無法運作，這時系統會提示下一個範例。

### <a name="conditional-sections"></a>條件式區段

[ **ConditionalSection** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalSection)範例放在個別的布林值項目在選取範圍條件的兩個項目`TableSection`。 程式碼後置檔案就會移除從本節`TableView`或新增回根據布林值屬性。

### <a name="a-tableview-menu"></a>TableView 功能表

另一個用途`TableView`為功能表。 [ **MenuCommands** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/MenuCommands)範例將示範如何讓您在移動的方式稍有功能表`BoxView`在螢幕上。



## <a name="related-links"></a>相關連結

- [第 19 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch19-Apr2016.pdf)
- [第 19 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)
- [ListView](~/xamarin-forms/user-interface/listview/index.md)
- [TableView](~/xamarin-forms/user-interface/tableview.md)
