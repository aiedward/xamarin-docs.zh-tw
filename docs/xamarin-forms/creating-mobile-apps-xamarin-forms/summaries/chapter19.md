---
title： "第19章的摘要。 集合視圖的「描述：」使用下列方式建立 Mobile Apps Xamarin.Forms ：第19章的摘要。 集合 views "ms. 生產： xamarin ms. 技術： xamarin assetid： 0AEC3A5C-586E-4D0F-9895-67E99A053A79 author： davidbritch ms-chap： dabritch ms. date： 07/18/2018 no-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="summary-of-chapter-19-collection-views"></a>第19章的摘要。 集合視圖

[![下載範例 ](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)

> [!NOTE] 
> 此頁面上的附注指出 Xamarin.Forms 從書籍中所呈現之材質分歧的區域。

Xamarin.Forms定義三個可維護集合並顯示其元素的視圖：

- [`Picker`](xref:Xamarin.Forms.Picker)是字串專案的相對簡短清單，可讓使用者選擇一個
- [`ListView`](xref:Xamarin.Forms.ListView)通常是具有相同類型和格式的長專案清單，也可以讓使用者選擇一個
- [`TableView`](xref:Xamarin.Forms.TableView)是用來顯示資料或管理使用者輸入的*儲存格*集合（通常是各種類型和外觀）

MVVM 應用程式通常會使用 `ListView` 來顯示可選取的物件集合。

## <a name="program-options-with-picker"></a>使用選擇器的程式選項

[`Picker`](xref:Xamarin.Forms.Picker)當您需要允許使用者從相對較短的專案清單中選擇選項時，是不錯的選擇 `string` 。

### <a name="the-picker-and-event-handling"></a>選擇器和事件處理

[**PickerDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerDemo)範例會示範如何使用 XAML 來設定 `Picker` [`Title`](xref:Xamarin.Forms.Picker.Title) 屬性，並將 `string` 專案加入至 [`Items`](xref:Xamarin.Forms.Picker.Items) 集合。 當使用者選取時 `Picker` ，它會 `Items` 以平臺相依的方式顯示集合中的專案。

[`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged)事件會指出使用者已選取專案的時間。 以零為基底的 [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) 屬性會指出選取的專案。 如果未選取任何專案，則 `SelectedIndex` 等於 &ndash; 1。

您也可以使用 `SelectedIndex` 來初始化選取的專案，但必須在填入集合之後設定 `Items` 。 在 XAML 中，這表示您可能會使用屬性元素來設定 `SelectedIndex` 。

### <a name="data-binding-the-picker"></a>將選擇器系結至資料

`SelectedIndex`屬性受到可系結屬性的支援 `Items` ，但不是，因此使用資料系結很 `Picker` 容易。 其中一個解決方案是搭配使用 `Picker` 與， [`ObjectToIndexConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ObjectToIndexConverter.cs) 例如[**FormsBook. 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中的。 [**PickerBinding**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerBinding)會示範其運作方式。

> [!NOTE] 
> Xamarin.Forms `Picker` 現在包含支援資料系結的 `ItemsSource` 和 `SelectedItem` 屬性。 請參閱[選擇器](~/xamarin-forms/user-interface/picker/index.md)。

## <a name="rendering-data-with-listview"></a>使用 ListView 轉譯資料

[`ListView`](xref:Xamarin.Forms.ListView)是唯一衍生自的類別， [`ItemsView<TVisual>`](xref:Xamarin.Forms.ItemsView`1) 它會從它繼承 [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) 和 [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) 屬性。

`ItemsSource`的類型是 `IEnumerable` ，但它 `null` 預設為，而且必須透過資料系結明確初始化，或（更常見）設定為集合。 這個集合中的專案可以是任何類型。

`ListView`定義 [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) 屬性，其會設定為集合中的其中一個專案， `ItemsSource` `null` 如果未選取任何專案，則為。 `ListView`[`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected)選取新的專案時，引發事件。

### <a name="collections-and-selections"></a>集合和選取範圍

[**ListViewList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewList)範例會 `ListView` `Color` 在集合中填入具有17個值的 `List<Color>` 。 專案是可選取的，但根據預設，它們會以其不討喜 `ToString` 標記法顯示。 本章中的幾個範例會示範如何修正該顯示，並讓它成為所需的吸引力。

### <a name="the-row-separator"></a>資料列分隔符號

在 iOS 和 Android 上顯示時，細線會分隔資料列。 您可以使用和屬性來控制此 [`SeparatorVisibility`](xref:Xamarin.Forms.ListView.SeparatorVisibility) [`SeparatorColor`](xref:Xamarin.Forms.ListView.SeparatorColor) 。 `SeparatorVisibility`屬性的類型是 [`SeparatorVisibility`](xref:Xamarin.Forms.SeparatorVisibility) ，具有兩個成員的列舉：

- [`Default`](xref:Xamarin.Forms.SeparatorVisibility.Default)，預設設定
- [`None`](xref:Xamarin.Forms.SeparatorVisibility.None)

### <a name="data-binding-the-selected-item"></a>資料系結選取的專案

`SelectedItem`屬性是由可系結屬性所支援，因此它可以是資料系結的來源或目標。 其預設值 `BindingMode` 為 `OneWayToSource` ，但通常是雙向資料系結的目標，特別是在 MVVM 案例中。 [**ListViewArray**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewArray)範例會示範這種類型的系結。

### <a name="the-observablecollection-difference"></a>ObservableCollection 差異

[**ListViewLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewLogger)範例會將的 `ItemsSource` 屬性設定 `ListView` 為 `List<DateTime>` 集合，然後使用計時器，每秒以漸進方式將新的 `DateTime` 物件加入至集合。

不過， `ListView` 不會自動自行更新，因為 `List<T>` 集合沒有通知機制可指出專案何時加入或移除集合。

在這種情況下，您可以在 [`ObservableCollection<T>`](xref:System.Collections.ObjectModel.ObservableCollection`1) 命名空間中定義更好的類別來使用 `System.Collections.ObjectModel` 。 這個類別會實作為 [`INotifyCollectionChanged`](xref:System.Collections.Specialized.INotifyCollectionChanged) 介面，並 [`CollectionChanged`](xref:System.Collections.ObjectModel.ObservableCollection`1.CollectionChanged) 在專案加入或移除集合，或是在集合中被取代或移動時，引發事件。 當內部偵測到實的 `ListView` 類別已 `INotifyCollectionChanged` 設定為其屬性時 `ItemsSource` ，它會將處理常式附加至 `CollectionChanged` 事件，並在集合變更時更新其顯示。

[**ObservableLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ObservableLogger)範例會示範的用法 `ObservableCollection` 。

### <a name="templates-and-cells"></a>範本和資料格

根據預設，會 `ListView` 使用每個專案的方法來顯示其集合中的專案 `ToString` 。 較好的方法包括定義範本來顯示專案。

若要試驗這項功能，您可以使用 [`NamedColor`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs) [**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中的類別。 這個類別會定義類型的靜態 `All` 屬性 `IList<NamedColor>` ，其中包含 `NamedColor` 對應至結構公用欄位的141物件 `Color` 。

[**NaiveNamedColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/NaiveNamedColorList)範例 `ItemsSource` 會將的設定 `ListView` 為這個 `NamedColor.All` 屬性，但只 `NamedColor` 會顯示物件的完整類別名稱。

`ListView`需要範本來顯示這些專案。 在程式碼中，您可以 [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) `ItemsView<TVisual>` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 使用參考類別衍生的函[ `DataTemplate` 式](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Type))，將定義的屬性設定為物件 [`Cell`](xref:Xamarin.Forms.Cell) 。 `Cell`有五個衍生：

- [`TextCell`](xref:Xamarin.Forms.TextCell)&mdash;包含兩個 `Label` 視圖（概念上說）
- [`ImageCell`](xref:Xamarin.Forms.ImageCell)&mdash;將 `Image` 視圖加入至`TextCell`
- [`EntryCell`](xref:Xamarin.Forms.EntryCell)&mdash;包含 `Entry` 具有的視圖`Label`
- [`SwitchCell`](xref:Xamarin.Forms.SwitchCell)&mdash;包含 `Switch` 具有`Label`
- [`ViewCell`](xref:Xamarin.Forms.ViewCell)&mdash;可以是任何 `View` （可能有子系）

然後呼叫 [ `SetValue` ] （x： Xamarin.Forms 。DataTemplate. SetValue （ Xamarin.Forms 。BindableProperty、System.object）和 [ `SetBinding` ] （x： Xamarin.Forms 。DataTemplate. SetBinding （ Xamarin.Forms 。BindableProperty， Xamarin.Forms 。System.windows.data.bindingbase.delay）） `DataTemplate` ，以將值與屬性產生關聯 `Cell` ，或在 `Cell` 參考集合中專案屬性的屬性上設定資料系結 `ItemsSource` 。 這會在[**TextCellListCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListCode)範例中示範。

當每個專案顯示時 `ListView` ，會從範本中建立一個小型視覺化樹狀結構，並在專案和此視覺化樹狀結構中元素的屬性之間建立資料系結。 您可以藉由安裝的 [`ItemAppearing`](xref:Xamarin.Forms.ListView.ItemAppearing) 和事件處理常式 [`ItemDisappearing`](xref:Xamarin.Forms.ListView.ItemDisappearing) `ListView` ，或使用替代的函式（使用每次必須建立[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Func{System.Object}))專案的視覺化樹狀結構時所呼叫的函式），來瞭解這個進程。

[**TextCellListXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListXaml)完全以 XAML 顯示功能完全相同的程式。 `DataTemplate`標記設定為的 `ItemTemplate` 屬性 `ListView` ，然後 `TextCell` 設定為 `DataTemplate` 。 集合中專案之屬性（property）的系結會直接在的 [`Text`](xref:Xamarin.Forms.TextCell.Text) 和屬性上設定 [`Detail`](xref:Xamarin.Forms.TextCell.Detail) `TextCell` 。

### <a name="custom-cells"></a>自訂資料格

在 XAML 中，可以將設定 [`ViewCell`](xref:Xamarin.Forms.ViewCell) 為， `DataTemplate` 然後將自訂視覺效果樹狀結構定義為的 [`View`](xref:Xamarin.Forms.ViewCell.View) 屬性 `ViewCell` 。 （ `View` 是的 content 屬性 `ViewCell` ，因此 `ViewCell.View` 不需要標記）。[**CustomNamedColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CustomNamedColorList)範例會示範這項技術：

[![自訂命名色彩清單的三向螢幕擷取畫面](images/ch19fg11-small.png "自訂命名色彩清單")](images/ch19fg11-large.png#lightbox "自訂命名色彩清單")

為所有平臺取得適當的大小，可能會很棘手。 [`RowHeight`](xref:Xamarin.Forms.ListView.RowHeight)屬性很有用，但在某些情況下，您會想要利用 [`HasUnevenRows`](xref:Xamarin.Forms.ListView.HasUnevenRows) 屬性，這樣比較不有效率，但會強制 `ListView` 調整資料列的大小。 針對 iOS 和 Android，您必須使用這兩個屬性的其中一個來取得適當的資料列大小。

### <a name="grouping-the-listview-items"></a>群組 ListView 專案

`ListView`支援專案的群組，以及在這些群組之間流覽。 `ItemsSource`屬性必須設定為集合的集合： `ItemsSource` 設定為的物件必須執行 `IEnumerable` ，而且集合中的每個專案也必須執行 `IEnumerable` 。 每個群組都應該包含兩個屬性：群組的文字描述和三個字母的縮寫。

[`NamedColorGroup`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColorGroup.cs) [**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中的類別會建立七個 `NamedColor` 物件群組。 [**ColorGroupList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorGroupList)範例會示範如何使用這些群組，並將的 [`IsGroupingEnabled`](xref:Xamarin.Forms.ListView.IsGroupingEnabled) 屬性 `ListView` 設定為 `true` ，並將 [`GroupDisplayBinding`](xref:Xamarin.Forms.ListView.GroupDisplayBinding) 和屬性系結 [`GroupShortNameBinding`](xref:Xamarin.Forms.ListView.GroupShortNameBinding) 至每個群組中的屬性。

### <a name="custom-group-headers"></a>自訂群組標頭

藉 `ListView` 由以 `GroupDisplayBinding` 定義標頭的範本來取代屬性，可以建立群組的自訂標頭 [`GroupHeaderTemplate`](xref:Xamarin.Forms.ListView.GroupHeaderTemplate) 。

### <a name="listview-and-interactivity"></a>ListView 和互動性

應用程式通常會藉 `ListView` 由將處理常式附加至 `ItemSelected` 或 [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 事件，或在屬性上設定資料系結，來取得與的使用者互動 `SelectedItem` 。 但是，某些資料格類型（ `EntryCell` 和 `SwitchCell` ）允許使用者互動，而且也可以建立本身與使用者互動的自訂資料格。 [**InteractiveListView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/InteractiveListView)會建立的100實例 [`ColorViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) ，並允許使用者使用元素的三個來變更每個色彩 `Slider` 。 此程式也會使用 [`ColorToContrastColorConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorToContrastColorConverter.cs) [**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)組中的。

## <a name="listview-and-mvvm"></a>ListView 和 MVVM

`ListView`在 MVVM 案例中扮演重要的角色。 每當 `IEnumerable` 集合存在於 ViewModel 時，它通常會系結至 `ListView` 。 此外，集合中的專案通常會實作為 `INotifyPropertyChanged` 與範本中的屬性系結。

### <a name="a-collection-of-viewmodels"></a>Viewmodel 的集合

為了探索此， [**SchoolOfFineArts**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt)程式庫會根據[XML 資料](https://xamarin.github.io/xamarin-forms-book-samples/SchoolOfFineArt/students.xml)檔和此虛構學校的虛構學生影像，建立數個類別。

[`Student`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/Student.cs)類別衍生自 [`ViewModelBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/ViewModelBase.cs) 。 [`StudentBody`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/StudentBody.cs)類別是物件的集合 `Student` ，而且也衍生自 `ViewModelBase` 。 會 [`SchoolViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/SchoolViewModel.cs) 下載 XML 檔案，並組合所有物件。

[**StudentList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/StudentList)程式會使用 `ImageCell` ，在中顯示學生和其映射 `ListView` ：

[![學生清單的三向螢幕擷取畫面](images/ch19fg18-small.png "學生清單")](images/ch19fg18-large.png#lightbox "學生清單")

[**ListViewHeader**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewHeader)範例 [`Header`](xref:Xamarin.Forms.ListView.Header) 會新增屬性，但它只會顯示在 Android 上。

### <a name="selection-and-the-binding-context"></a>選取範圍和系結內容

[**SelectedStudentDetail**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/SelectedStudentDetail)程式會將的系結 `BindingContext` 至的 `StackLayout` `SelectedItem` 屬性 `ListView` 。 這可讓程式顯示所選學生的詳細資訊。

### <a name="context-menus"></a>操作功能表

資料格可以定義以平臺特定方式執行的內容功能表。 若要建立這個功能表，請將 [`MenuItem`](xref:Xamarin.Forms.MenuItem) 物件加入至的 [`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions) 屬性 `Cell` 。

`MenuItem`定義五個屬性：

- [`Text`](xref:Xamarin.Forms.MenuItem.Text)類型為`string`
- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon)類型為`FileImageSource`
- [`IsDestructive`](xref:Xamarin.Forms.MenuItem.IsDestructive)類型為`bool`
- [`Command`](xref:Xamarin.Forms.MenuItem.Command)類型為`ICommand`
- [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter)類型為`object`

`Command`和 `CommandParameter` 屬性工作表示每個專案的 ViewModel 都包含方法，以執行所需的功能表命令。 在非 MVVM 案例中， `MenuItem` 也會定義 [`Clicked`](xref:Xamarin.Forms.MenuItem.Clicked) 事件。

[**CellCoNtextMenu**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CellContextMenu)會示範這項技術。 `Command`每個的屬性 `MenuItem` 都會系結至 `ICommand` 類別中類型的屬性 `Student` 。 `IsDestructive` `true` 針對 `MenuItem` 移除或刪除選取之物件的，將屬性設定為。

### <a name="varying-the-visuals"></a>改變視覺效果

有時候，您會想要根據屬性，在中的專案視覺效果中有些許差異 `ListView` 。 例如，當學生的等級點平均值低於2.0 時， [**ColorCodedStudents**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorCodedStudents)範例會以紅色顯示該學生的名稱。
這可以透過使用 [`ThresholdToObjectConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ThresholdToObjectConverter.cs) [**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中的系結值轉換器來完成。

### <a name="refreshing-the-content"></a>重新整理內容

`ListView`支援用來重新整理其資料的下拉手勢。 程式必須將屬性設定 [`IsPullToRefresh`](xref:Xamarin.Forms.ListView.IsPullToRefreshEnabled) 為 `true` 以啟用此專案。 會藉 `ListView` 由將其 [`IsRefreshing`](xref:Xamarin.Forms.ListView.IsRefreshing) 屬性設為 `true` ，以及引發 [`Refreshing`](xref:Xamarin.Forms.ListView.Refreshing) 事件和（適用于 MVVM 案例）來呼叫 `Execute` 其屬性的方法， [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand) 來回應下拉手勢。

處理 `Refresh` 事件或的程式碼， `RefreshCommand` 可能會更新所顯示的資料 `ListView` ，並將設定 `IsRefreshing` 回 `false` 。

[**Rss 摘要**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/RssFeed)範例會示範如何使用來執行資料系結的 [`RssFeedViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/RssFeed/RssFeed/RssFeed/RssFeedViewModel.cs) `RefreshCommand` 和 `IsRefreshing` 屬性。

## <a name="the-tableview-and-its-intents"></a>TableView 及其意圖

雖然 `ListView` 通常會顯示相同類型的多個實例，但 [`TableView`](xref:Xamarin.Forms.TableView) 通常著重于為各種類型的多個屬性提供使用者介面。 每個專案都會與其本身的衍生相關聯， [`Cell`](xref:Xamarin.Forms.Cell) 以顯示內容或提供使用者介面給它。

### <a name="properties-and-hierarchies"></a>屬性和階層

`TableView`只定義四個屬性：

- [`Intent`](xref:Xamarin.Forms.TableView.Intent)類型的 [`TableIntent`](xref:Xamarin.Forms.TableIntent) ，列舉
- [`Root`](xref:Xamarin.Forms.TableView.Root)屬於類型的 [`TableRoot`](xref:Xamarin.Forms.TableRoot) 、的 content 屬性`TableView`
- [`RowHeight`](xref:Xamarin.Forms.TableView.RowHeight)類型為`int`
- [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows)類型為`bool`

`TableIntent`列舉會指出您想要如何使用 `TableView` ：

- [`Data`](xref:Xamarin.Forms.TableIntent.Data)
- [`Form`](xref:Xamarin.Forms.TableIntent.Form)
- [`Settings`](xref:Xamarin.Forms.TableIntent.Settings)
- [`Menu`](xref:Xamarin.Forms.TableIntent.Menu)

這些成員也會建議的一些用途 `TableView` 。

定義資料表時牽涉到其他幾個類別：

- [`TableSectionBase`](xref:Xamarin.Forms.TableSectionBase)是衍生自 `BindableObject` 並定義屬性的抽象類別 [`Title`](xref:Xamarin.Forms.TableSectionBase.Title)

- [`TableSectionBase<T>`](xref:Xamarin.Forms.TableSectionBase`1)是衍生自並會執行 `TableSectionBase` `IList<T>` 和的抽象類別`INotifyCollectionChanged`

- [`TableSection`](xref:Xamarin.Forms.TableSection)衍生自`TableSectionBase<Cell>`

- [`TableRoot`](xref:Xamarin.Forms.TableRoot)衍生自`TableSectionBase<TableSection>`

簡言之， `TableView` 具有 `Root` 您設定為物件的屬性 `TableRoot` ，這是物件的集合 `TableSection` ，每一個都是物件的集合 `Cell` 。 資料表有多個區段，而且每個區段都有多個資料格。 資料表本身可以有一個標題，而且每個區段都可以有一個標題。 雖然 `TableView` 會使用 `Cell` 衍生的，但它不會利用 `DataTemplate` 。

### <a name="a-prosaic-form"></a>平凡表單

[**EntryForm**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/EntryForm)範例 [`PersonalInformation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs) 會定義視圖模型，這是的實例，它會成為的 `BindingContext` `TableView` 。 `Cell`其中的每個衍生都 `TableSection` 可以有類別的屬性系結 `PersonalInformation` 。

### <a name="custom-cells"></a>自訂資料格

[**ConditionalCells**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalCells)範例會在**EntryForm**上展開。 [`ProgrammerInformation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs)類別包含一個布林值屬性，可控制兩個額外屬性的適用性。 針對這兩個額外的屬性，程式會使用以 PickerCell 為基礎的自訂， `PickerCell` 以及[**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)組連結[PickerCell.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml)庫中的[PickerCell.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml.cs) 。

雖然這 `IsEnabled` 兩個元素的屬性會系結 `PickerCell` 至中的布林值屬性 `ProgrammerInformation` ，但這項技術似乎不會運作，這會提示下一個範例。

### <a name="conditional-sections"></a>條件式區段

[**ConditionalSection**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalSection)範例會將條件式的兩個專案放在另一個中的布林值專案的選取範圍 `TableSection` 。 程式碼後置檔案會從移除此區段， `TableView` 或根據布林值屬性將其新增回去。

### <a name="a-tableview-menu"></a>TableView 功能表

的另一種用法 `TableView` 是功能表。 [**Menucommand 對比**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/MenuCommands)範例會示範一個功能表，讓您在 `BoxView` 螢幕周圍移動一點。

## <a name="related-links"></a>相關連結

- [第19章的全文檢索（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch19-Apr2016.pdf)
- [第19章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)
- [Picker](~/xamarin-forms/user-interface/picker/index.md)
- [ListView](~/xamarin-forms/user-interface/listview/index.md)
- [TableView](~/xamarin-forms/user-interface/tableview.md)
