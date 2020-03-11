---
title: 第19章的摘要。 集合視圖
description: 使用 Xamarin 建立 Mobile Apps：第19章的摘要。 集合視圖
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 0AEC3A5C-586E-4D0F-9895-67E99A053A79
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: bffbd2dec4a8494723597ba6e0f0af69e57f3718
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "73032859"
---
# <a name="summary-of-chapter-19-collection-views"></a>第19章的摘要。 集合視圖

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)

> [!NOTE] 
> 此頁面上的附注指出 Xamarin 從書籍中呈現的材料中分歧的區域。

Xamarin 會定義三個可維護集合並顯示其元素的視圖：

- [`Picker`](xref:Xamarin.Forms.Picker)是一個相對較短的字串專案清單，可讓使用者選擇一個
- [`ListView`](xref:Xamarin.Forms.ListView)通常是具有相同類型和格式的長專案清單，也可以讓使用者選擇一個
- [`TableView`](xref:Xamarin.Forms.TableView)是用來顯示資料或管理使用者輸入的*儲存格*集合（通常是各種類型和外觀）

MVVM 應用程式通常會使用 `ListView` 來顯示可選取的物件集合。

## <a name="program-options-with-picker"></a>使用選擇器的程式選項

當您需要允許使用者從相對較短的 `string` 專案清單中選擇選項時， [`Picker`](xref:Xamarin.Forms.Picker)是不錯的選擇。

### <a name="the-picker-and-event-handling"></a>選擇器和事件處理

[**PickerDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerDemo)範例會示範如何使用 XAML 來設定 `Picker` [`Title`](xref:Xamarin.Forms.Picker.Title)屬性，並將 `string` 專案新增至[`Items`](xref:Xamarin.Forms.Picker.Items)集合。 當使用者選取 `Picker`時，它會以與平臺相關的方式顯示 `Items` 集合中的專案。

[`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged)事件會指出使用者已選取專案的時間。 以零為基底的[`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex)屬性會指出選取的專案。 如果未選取任何專案，`SelectedIndex` 等於 &ndash;1。

您也可以使用 `SelectedIndex` 來初始化選取的專案，但必須在填入 `Items` 集合之後設定。 在 XAML 中，這表示您可能會使用 property 元素來設定 `SelectedIndex`。

### <a name="data-binding-the-picker"></a>將選擇器系結至資料

`SelectedIndex` 屬性是由可系結屬性所支援，但 `Items` 不是，因此使用資料系結與 `Picker` 很棘手。 其中一個解決方案是搭配使用 `Picker` 與[`ObjectToIndexConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ObjectToIndexConverter.cs) ，例如[**FormsBook. 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中的一個。 [**PickerBinding**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerBinding)會示範其運作方式。

> [!NOTE] 
> [Xamarin] `Picker` 現在包含支援資料系結的 `ItemsSource` 和 `SelectedItem` 屬性。 請參閱[選擇器](~/xamarin-forms/user-interface/picker/index.md)。

## <a name="rendering-data-with-listview"></a>使用 ListView 轉譯資料

[`ListView`](xref:Xamarin.Forms.ListView)是唯一衍生自從中繼承[`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource)和[`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate)屬性之[`ItemsView<TVisual>`](xref:Xamarin.Forms.ItemsView`1)的類別。

`ItemsSource` 的類型為 `IEnumerable` 但預設為 `null`，而且必須透過資料系結明確初始化或（更常見）設定為集合。 這個集合中的專案可以是任何類型。

`ListView` 定義[`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem)屬性，其會設定為 `ItemsSource` 集合中的其中一個專案，如果未選取任何專案，則會 `null`。 `ListView` 在選取新專案時引發[`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected)事件。

### <a name="collections-and-selections"></a>集合和選取範圍

[**ListViewList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewList)範例會在 `List<Color>` 集合中填入17個 `Color` 值的 `ListView`。 專案是可選取的，但根據預設，它們會以其不討喜 `ToString` 標記法來顯示。 本章中的幾個範例會示範如何修正該顯示，並讓它成為所需的吸引力。

### <a name="the-row-separator"></a>資料列分隔符號

在 iOS 和 Android 上顯示時，細線會分隔資料列。 您可以使用[`SeparatorVisibility`](xref:Xamarin.Forms.ListView.SeparatorVisibility)和[`SeparatorColor`](xref:Xamarin.Forms.ListView.SeparatorColor)屬性來控制此內容。 `SeparatorVisibility` 屬性是[`SeparatorVisibility`](xref:Xamarin.Forms.SeparatorVisibility)類型，這是具有兩個成員的列舉：

- [`Default`](xref:Xamarin.Forms.SeparatorVisibility.Default)，預設設定
- [`None`](xref:Xamarin.Forms.SeparatorVisibility.None)

### <a name="data-binding-the-selected-item"></a>資料系結選取的專案

`SelectedItem` 屬性是由可系結屬性所支援，因此它可以是資料系結的來源或目標。 其預設 `BindingMode` 是 `OneWayToSource`，但通常是雙向資料系結的目標，特別是在 MVVM 案例中。 [**ListViewArray**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewArray)範例會示範這種類型的系結。

### <a name="the-observablecollection-difference"></a>ObservableCollection 差異

[**ListViewLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewLogger)範例會將 `ListView` 的 `ItemsSource` 屬性設定為 `List<DateTime>` 集合，然後使用計時器，以每秒漸進方式將新的 `DateTime` 物件新增至集合。

不過，`ListView` 不會自動自行更新，因為 `List<T>` 集合沒有通知機制可指出專案何時加入或移除集合。

在這種情況下，有一個更好的類別是在 `System.Collections.ObjectModel` 命名空間中定義[`ObservableCollection<T>`](xref:System.Collections.ObjectModel.ObservableCollection`1) 。 這個類別會實[`INotifyCollectionChanged`](xref:System.Collections.Specialized.INotifyCollectionChanged)介面，因此當專案加入或從集合中移除，或是在集合中被取代或移動時，就會引發[`CollectionChanged`](xref:System.Collections.ObjectModel.ObservableCollection`1.CollectionChanged)事件。 當 `ListView` 在內部偵測到實 `INotifyCollectionChanged` 的類別已設定為其 `ItemsSource` 屬性時，它會將處理常式附加至 `CollectionChanged` 事件，並在集合變更時更新其顯示。

[**ObservableLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ObservableLogger)範例會示範 `ObservableCollection`的用法。

### <a name="templates-and-cells"></a>範本和資料格

根據預設，`ListView` 會使用每個專案的 `ToString` 方法來顯示其集合中的專案。 較好的方法包括定義範本來顯示專案。

若要試驗這項功能，您可以使用[**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中的[`NamedColor`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs)類別。 這個類別會定義 `IList<NamedColor>` 類型的靜態 `All` 屬性，其中包含與 `Color` 結構之公用欄位對應的 141 `NamedColor` 物件。

[**NaiveNamedColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/NaiveNamedColorList)範例會將 `ListView` 的 `ItemsSource` 設定為這個 `NamedColor.All` 屬性，但只會顯示 `NamedColor` 物件的完整類別名稱。

`ListView` 需要範本來顯示這些專案。 在程式碼中，您可以使用參考[`Cell`](xref:Xamarin.Forms.Cell)類別衍生的[`DataTemplate`](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Type))函式，將 `ItemsView<TVisual>` 定義的[`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate)屬性設定為[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)物件。 `Cell` 有五個衍生的：

- [`TextCell`](xref:Xamarin.Forms.TextCell) &mdash; 包含兩個 `Label` 的觀點（概念上說）
- [`ImageCell`](xref:Xamarin.Forms.ImageCell) &mdash; 會將 `Image` 視圖加入至 `TextCell`
- [`EntryCell`](xref:Xamarin.Forms.EntryCell) &mdash; 包含具有 `Label` 的 `Entry` 視圖
- [`SwitchCell`](xref:Xamarin.Forms.SwitchCell) &mdash; 包含具有 `Label` 的 `Switch`
- [`ViewCell`](xref:Xamarin.Forms.ViewCell) &mdash; 可以是任何 `View` （可能有子系）

然後，呼叫 `DataTemplate` 物件上的[`SetValue`](xref:Xamarin.Forms.DataTemplate.SetValue(Xamarin.Forms.BindableProperty,System.Object))和[`SetBinding`](xref:Xamarin.Forms.DataTemplate.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) ，將值與 `Cell` 屬性產生關聯，或在參考 `Cell` 集合中專案屬性的 `ItemsSource` 屬性上設定資料系結。 這會在[**TextCellListCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListCode)範例中示範。

當 `ListView`顯示每個專案時，就會從範本中建立一個小型視覺化樹狀結構，並在專案和此視覺化樹狀結構中元素的屬性之間建立資料系結。 您可以藉由為 `ListView`的[`ItemAppearing`](xref:Xamarin.Forms.ListView.ItemAppearing)和[`ItemDisappearing`](xref:Xamarin.Forms.ListView.ItemDisappearing)事件安裝處理常式，或使用替代的[`DataTemplate`](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Func{System.Object}))函式，以使用每次必須建立專案的視覺化樹狀結構時所呼叫的函數，來瞭解此程式。

[**TextCellListXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListXaml)完全以 XAML 顯示功能完全相同的程式。 `DataTemplate` 標記會設定為 `ListView`的 `ItemTemplate` 屬性，然後 `TextCell` 會設定為 `DataTemplate`。 集合中專案的屬性系結會直接設定在[`Text`](xref:Xamarin.Forms.TextCell.Text)上，並[`Detail`](xref:Xamarin.Forms.TextCell.Detail) `TextCell`的屬性。

### <a name="custom-cells"></a>自訂資料格

在 XAML 中，可以將[`ViewCell`](xref:Xamarin.Forms.ViewCell)設定為 `DataTemplate`，然後將自訂視覺效果樹狀結構定義為 `ViewCell`的[`View`](xref:Xamarin.Forms.ViewCell.View)屬性。 （`View` 是 `ViewCell` 的 content 屬性，因此不需要 `ViewCell.View` 標記）。[**CustomNamedColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CustomNamedColorList)範例會示範這項技術：

[![自訂命名色彩清單的三向螢幕擷取畫面](images/ch19fg11-small.png "自訂命名色彩清單")](images/ch19fg11-large.png#lightbox "自訂命名色彩清單")

為所有平臺取得適當的大小，可能會很棘手。 [`RowHeight`](xref:Xamarin.Forms.ListView.RowHeight)屬性很有用，但在某些情況下，您會想要利用[`HasUnevenRows`](xref:Xamarin.Forms.ListView.HasUnevenRows)屬性，這樣比較不有效率，但會強制 `ListView` 調整資料列的大小。 針對 iOS 和 Android，您必須使用這兩個屬性的其中一個來取得適當的資料列大小。

### <a name="grouping-the-listview-items"></a>群組 ListView 專案

`ListView` 支援專案的群組，以及在這些群組之間流覽。 `ItemsSource` 屬性必須設定為集合集合： `ItemsSource` 設定為的物件必須執行 `IEnumerable`，而且集合中的每個專案也必須執行 `IEnumerable`。 每個群組都應該包含兩個屬性：群組的文字描述和三個字母的縮寫。

[**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中的[`NamedColorGroup`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColorGroup.cs)類別，會建立七個 `NamedColor` 物件的群組。 [**ColorGroupList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorGroupList)範例會示範如何使用這些群組搭配 `ListView` 設定為 `true`的[`IsGroupingEnabled`](xref:Xamarin.Forms.ListView.IsGroupingEnabled)屬性，以及系結至每個群組中屬性的[`GroupDisplayBinding`](xref:Xamarin.Forms.ListView.GroupDisplayBinding)和[`GroupShortNameBinding`](xref:Xamarin.Forms.ListView.GroupShortNameBinding)屬性。

### <a name="custom-group-headers"></a>自訂群組標頭

您可以使用定義標頭範本的[`GroupHeaderTemplate`](xref:Xamarin.Forms.ListView.GroupHeaderTemplate)來取代 `GroupDisplayBinding` 屬性，以建立 `ListView` 群組的自訂標頭。

### <a name="listview-and-interactivity"></a>ListView 和互動性

應用程式通常會藉由將處理常式附加至 `ItemSelected` 或[`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped)事件，或在 `SelectedItem` 屬性上設定資料系結，來取得使用者與 `ListView` 的互動。 但是某些資料格類型（`EntryCell` 和 `SwitchCell`）允許使用者互動，而且也可以建立本身與使用者互動的自訂資料格。 [**InteractiveListView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/InteractiveListView)會建立[`ColorViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs)的100實例，並允許使用者使用 `Slider` 元素的三個來變更每個色彩。 此程式也會使用[**FormsBook**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)中的[`ColorToContrastColorConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorToContrastColorConverter.cs) 。

## <a name="listview-and-mvvm"></a>ListView 和 MVVM

`ListView` 在 MVVM 案例中扮演著重要的角色。 每當 ViewModel 中存在 `IEnumerable` 集合時，它通常會系結至 `ListView`。 此外，集合中的專案通常會執行 `INotifyPropertyChanged`，以便與範本中的屬性系結。

### <a name="a-collection-of-viewmodels"></a>Viewmodel 的集合

為了探索此， [**SchoolOfFineArts**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt)程式庫會根據[XML 資料](https://xamarin.github.io/xamarin-forms-book-samples/SchoolOfFineArt/students.xml)檔和此虛構學校的虛構學生影像，建立數個類別。

[`Student`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/Student.cs)類別衍生自[`ViewModelBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/ViewModelBase.cs)。 [`StudentBody`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/StudentBody.cs)類別是 `Student` 物件的集合，而且也衍生自 `ViewModelBase`。 [`SchoolViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/SchoolViewModel.cs)會下載 XML 檔案，並組合所有的物件。

[**StudentList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/StudentList)程式會使用 `ImageCell`，在 `ListView`中顯示學生和其影像：

[![學生清單的三向螢幕擷取畫面](images/ch19fg18-small.png "學生清單")](images/ch19fg18-large.png#lightbox "學生清單")

[**ListViewHeader**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewHeader)範例會新增[`Header`](xref:Xamarin.Forms.ListView.Header)屬性，但只會顯示在 Android 上。

### <a name="selection-and-the-binding-context"></a>選取範圍和系結內容

[**SelectedStudentDetail**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/SelectedStudentDetail)程式會將 `StackLayout` 的 `BindingContext` 系結至 `ListView`的 `SelectedItem` 屬性。 這可讓程式顯示所選學生的詳細資訊。

### <a name="context-menus"></a>操作功能表

資料格可以定義以平臺特定方式執行的內容功能表。 若要建立此功能表，請將[`MenuItem`](xref:Xamarin.Forms.MenuItem)物件新增至 `Cell`的[`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions)屬性。

`MenuItem` 定義五個屬性：

- 類型為 [ 的 `Text`](xref:Xamarin.Forms.MenuItem.Text)`string`
- 類型為 [ 的 `Icon`](xref:Xamarin.Forms.MenuItem.Icon)`FileImageSource`
- 類型為 [ 的 `IsDestructive`](xref:Xamarin.Forms.MenuItem.IsDestructive)`bool`
- 類型為 [ 的 `Command`](xref:Xamarin.Forms.MenuItem.Command)`ICommand`
- 類型為 [ 的 `CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter)`object`

[`Command`] 和 [`CommandParameter`] 屬性工作表示每個專案的 ViewModel 都包含可執行所需功能表命令的方法。 在非 MVVM 案例中，`MenuItem` 也會定義[`Clicked`](xref:Xamarin.Forms.MenuItem.Clicked)事件。

[**CellCoNtextMenu**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CellContextMenu)會示範這項技術。 每個 `MenuItem` 的 `Command` 屬性都會系結至 `Student` 類別中 `ICommand` 類型的屬性。 針對移除或刪除所選取物件的 `MenuItem`，將 `IsDestructive` 屬性設定為 [`true`]。

### <a name="varying-the-visuals"></a>改變視覺效果

有時候，您會想要根據屬性，在 `ListView` 中專案的視覺效果中有些許差異。 例如，當學生的等級點平均值低於2.0 時， [**ColorCodedStudents**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorCodedStudents)範例會以紅色顯示該學生的名稱。
這可以透過在[**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中使用系結值轉換器（ [`ThresholdToObjectConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ThresholdToObjectConverter.cs)）來完成。

### <a name="refreshing-the-content"></a>重新整理內容

`ListView` 支援用來重新整理其資料的下拉手勢。 程式必須將[`IsPullToRefresh`](xref:Xamarin.Forms.ListView.IsPullToRefreshEnabled)屬性設定為 `true` 才能啟用此專案。 `ListView` 會藉由將其[`IsRefreshing`](xref:Xamarin.Forms.ListView.IsRefreshing)屬性設為 `true`，以及引發[`Refreshing`](xref:Xamarin.Forms.ListView.Refreshing)事件和（適用于 MVVM 案例）來呼叫其[`Execute`](xref:Xamarin.Forms.ListView.RefreshCommand)屬性的`RefreshCommand`方法，來回應下拉手勢。

處理 `Refresh` 事件或 `RefreshCommand` 的程式碼，可能會更新 `ListView` 所顯示的資料，並將 `IsRefreshing` 設定回 `false`。

[**Rss 摘要**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/RssFeed)範例會示範如何使用[`RssFeedViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/RssFeed/RssFeed/RssFeed/RssFeedViewModel.cs)來執行資料系結的 `RefreshCommand` 和 `IsRefreshing` 屬性。

## <a name="the-tableview-and-its-intents"></a>TableView 及其意圖

雖然 `ListView` 通常會顯示相同類型的多個實例，但[`TableView`](xref:Xamarin.Forms.TableView)通常著重于為各種類型的多個屬性提供使用者介面。 每個專案都會與其本身的[`Cell`](xref:Xamarin.Forms.Cell)衍生相關聯，以顯示內容或提供使用者介面給它。

### <a name="properties-and-hierarchies"></a>屬性和階層

`TableView` 只會定義四個屬性：

- 類型[`TableIntent`](xref:Xamarin.Forms.TableIntent)的[`Intent`](xref:Xamarin.Forms.TableView.Intent) ，列舉
- 類型[`TableRoot`](xref:Xamarin.Forms.TableRoot)的[`Root`](xref:Xamarin.Forms.TableView.Root) ，`TableView` 的 content 屬性
- 類型為 [ 的 `RowHeight`](xref:Xamarin.Forms.TableView.RowHeight)`int`
- 類型為 [ 的 `HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows)`bool`

`TableIntent` 列舉會指出您想要如何使用 `TableView`：

- [`Data`](xref:Xamarin.Forms.TableIntent.Data)
- [`Form`](xref:Xamarin.Forms.TableIntent.Form)
- [`Settings`](xref:Xamarin.Forms.TableIntent.Settings)
- [`Menu`](xref:Xamarin.Forms.TableIntent.Menu)

這些成員也會建議 `TableView`的一些用途。

定義資料表時牽涉到其他幾個類別：

- [`TableSectionBase`](xref:Xamarin.Forms.TableSectionBase)是從 `BindableObject` 衍生的抽象類別，並定義[`Title`](xref:Xamarin.Forms.TableSectionBase.Title)屬性

- [`TableSectionBase<T>`](xref:Xamarin.Forms.TableSectionBase`1)是從 `TableSectionBase` 衍生的抽象類別，並會執行 `IList<T>` 和 `INotifyCollectionChanged`

- [`TableSection`](xref:Xamarin.Forms.TableSection)衍生自 `TableSectionBase<Cell>`

- [`TableRoot`](xref:Xamarin.Forms.TableRoot)衍生自 `TableSectionBase<TableSection>`

簡言之，`TableView` 具有 `Root` 屬性，您可以將它設定為 `TableRoot` 物件，這是 `TableSection` 物件的集合，其中每一個都是 `Cell` 物件的集合。 資料表有多個區段，而且每個區段都有多個資料格。 資料表本身可以有一個標題，而且每個區段都可以有一個標題。 雖然 `TableView` 會使用 `Cell` 的衍生項，但不會使用 `DataTemplate`。

### <a name="a-prosaic-form"></a>平凡表單

[**EntryForm**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/EntryForm)範例會定義[`PersonalInformation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs) view 模型，其實例會成為 `TableView`的 `BindingContext`。 其 `TableSection` 中的每個 `Cell` 衍生都可以有 `PersonalInformation` 類別屬性的系結。

### <a name="custom-cells"></a>自訂資料格

[**ConditionalCells**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalCells)範例會在**EntryForm**上展開。 [`ProgrammerInformation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs)類別包含一個布林值屬性，可控制兩個額外屬性的適用性。 針對這兩個額外的屬性，程式會使用以[PickerCell](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml)為基礎的自訂 `PickerCell`，以及[**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)組程式庫中的[PickerCell.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml.cs) 。

雖然兩個 `PickerCell` 元素的 `IsEnabled` 屬性會系結至 `ProgrammerInformation`中的布林值屬性，但這項技術似乎不會運作，這會提示下一個範例。

### <a name="conditional-sections"></a>條件式區段

[**ConditionalSection**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalSection)範例會將兩個條件式的專案放在個別 `TableSection`中的布林值專案的選取範圍。 程式碼後置檔案會從 `TableView` 中移除此區段，或根據布林值屬性將其重新加入。

### <a name="a-tableview-menu"></a>TableView 功能表

`TableView` 的另一種用法是功能表。 [**Menucommand 對比**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/MenuCommands)範例會示範一個功能表，可讓您在螢幕周圍移動一些 `BoxView`。

## <a name="related-links"></a>相關連結

- [第19章的全文檢索（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch19-Apr2016.pdf)
- [第19章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)
- [選擇器](~/xamarin-forms/user-interface/picker/index.md)
- [ListView](~/xamarin-forms/user-interface/listview/index.md)
- [TableView](~/xamarin-forms/user-interface/tableview.md)
