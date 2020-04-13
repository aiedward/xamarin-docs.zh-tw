---
title: 第19章摘要。 集合檢視
description: 使用 Xamarin.表單創建行動應用程式:第 19 章摘要。 集合檢視
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 0AEC3A5C-586E-4D0F-9895-67E99A053A79
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: bffbd2dec4a8494723597ba6e0f0af69e57f3718
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73032859"
---
# <a name="summary-of-chapter-19-collection-views"></a>第19章摘要。 集合檢視

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)

> [!NOTE] 
> 本頁的註釋指示 Xamarin.Forms 與本書中介紹的材料有分歧的領域。

Xamarin.Forms 定義了三個檢視,它們維護集合並顯示其元素:

- [`Picker`](xref:Xamarin.Forms.Picker)是一個相對較短的字串項目清單,允許使用者選擇一個
- [`ListView`](xref:Xamarin.Forms.ListView)通常是通常相同類型和格式的項目的長清單,也允許使用者選擇一個
- [`TableView`](xref:Xamarin.Forms.TableView)是*儲存格*的集合(通常有多種類型和外觀),用於顯示資料或管理使用者輸入

MVVM 應用程式通常`ListView`使用 來顯示可選的物件集合。

## <a name="program-options-with-picker"></a>使用選取器的程式選項

當您[`Picker`](xref:Xamarin.Forms.Picker)需要允許使用者從相對較短的專案`string`清單中選擇一個選項時,這是一個不錯的選擇。

### <a name="the-picker-and-event-handling"></a>挑選器與事件處理

[**PickerDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerDemo)範例展示如何使用 XAML 設定`Picker`[`Title`](xref:Xamarin.Forms.Picker.Title)屬性`string`[`Items`](xref:Xamarin.Forms.Picker.Items)並將項添加到集合中。 當用戶選擇`Picker`時 ,它以與平台相關的方式`Items`顯示集合 中的項。

該[`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged)事件指示使用者何時選擇了專案。 然後,從零[`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex)開始的屬性指示所選項。 如果未選擇任何專案,`SelectedIndex`則等&ndash;於 1。

您還可以使用來`SelectedIndex`初始化所選項,但必須在填`Items`充 集合后設置它。 在 XAML 中,這意味著您可能使用屬性元素`SelectedIndex`來設定 。

### <a name="data-binding-the-picker"></a>資料繫結選取器

該`SelectedIndex`屬性由可綁定屬性支援,但`Items`不是 ,因此很難使用 數據`Picker`綁定。 一種解決方案是`Picker`[`ObjectToIndexConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ObjectToIndexConverter.cs)結合[**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)函式庫中的圖集使用 。 [**選取器綁定**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerBinding)演示了其工作原理。

> [!NOTE] 
> Xamarin.表單`Picker`現在`ItemsSource`包含和支援`SelectedItem`資料綁定的屬性。 請參考[拾取器](~/xamarin-forms/user-interface/picker/index.md)。

## <a name="rendering-data-with-listview"></a>使用 ListView 呈現資料

[`ListView`](xref:Xamarin.Forms.ListView)是唯一從中派生[`ItemsView<TVisual>`](xref:Xamarin.Forms.ItemsView`1)[`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource)繼承[`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate)和 屬性的類。

`ItemsSource`類型,`IEnumerable`但默認情況下`null`是 ,必須顯式初始化或通過數據綁定將設置為集合。 此集合中的項可以是任何類型的。

`ListView`定義設置為[`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem)`ItemsSource`集合中一個項或`null`未選擇項的屬性。 `ListView`選擇新項目[`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected)時觸發事件。

### <a name="collections-and-selections"></a>集合和選擇

[**ListViewList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewList)範例填`ListView`充 集合中的`Color``List<Color>`17 個 值。 這些專案是可選的,但默認情況下,它們顯示時帶有其不吸引人`ToString`的表示形式。 本章中的幾個示例演示如何修復該顯示,使其盡可能吸引人。

### <a name="the-row-separator"></a>行分隔符號

在 iOS 和 Android 顯示幕上,一條細線分隔行。 您可以使用和[`SeparatorVisibility`](xref:Xamarin.Forms.ListView.SeparatorVisibility)[`SeparatorColor`](xref:Xamarin.Forms.ListView.SeparatorColor)屬性控制此。 `SeparatorVisibility`屬性的類型[`SeparatorVisibility`](xref:Xamarin.Forms.SeparatorVisibility)為 ,具有兩個成員的枚舉:

- [`Default`](xref:Xamarin.Forms.SeparatorVisibility.Default),預設設置
- [`None`](xref:Xamarin.Forms.SeparatorVisibility.None)

### <a name="data-binding-the-selected-item"></a>資料繫結選取的選項

該`SelectedItem`屬性由可綁定屬性支援,因此它可以是數據綁定的源或目標。 其預設值`BindingMode``OneWayToSource`為 ,但通常它是雙向數據綁定的目標,尤其是在 MVVM 方案中。 [**ListViewArray**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewArray)示例演示了這種類型的綁定。

### <a name="the-observablecollection-difference"></a>可觀測集合差異

[**ListViewLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewLogger)範例`ItemsSource`將的`ListView`屬性設置到集合`List<DateTime>`中,然後每隔一秒使用計時`DateTime`器 逐步將新物件添加到集合中。

但是,`ListView`不會自動更新自身`List<T>`, 因為集合沒有指示何時將項添加到集合或從集合中刪除的通知機制。

在這樣的方案中[`ObservableCollection<T>`](xref:System.Collections.ObjectModel.ObservableCollection`1)`System.Collections.ObjectModel`,定義了一個更好的類。 類別的介面,[`INotifyCollectionChanged`](xref:System.Collections.Specialized.INotifyCollectionChanged)因此當將項新增[`CollectionChanged`](xref:System.Collections.ObjectModel.ObservableCollection`1.CollectionChanged)到集合或從集合中刪除時,或者當在集合中替換或行動專案時,將觸發事件。 當內部`ListView`檢測到類實現`INotifyCollectionChanged`已設置為`ItemsSource`其 屬性時,它將處理程式`CollectionChanged`附加到 事件,並在集合更改時更新其顯示。

[**可觀察記錄器**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ObservableLogger)範例演示了`ObservableCollection`的用途。

### <a name="templates-and-cells"></a>樣本和儲存格

預設情況下,使用`ListView`每個`ToString`專案 的方法在其集合中顯示項。 更好的方法涉及定義範本以顯示專案。

要試著此功能[`NamedColor`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs), 可以在[**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中使用該類。 `All`此類定義一個類型的`IList<NamedColor>`靜態屬性,其中包含`NamedColor``Color`與 結構的公共欄位對應的141個物件。

[**Naive命名ColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/NaiveNamedColorList)範例將`ItemsSource`此`ListView`屬性`NamedColor.All`的`NamedColor`集 。

`ListView`需要一個範本來顯示這些專案。 在代碼中[`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate),可以使用`ItemsView<TVisual>`[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)[`Cell`](xref:Xamarin.Forms.Cell)引用 類的派生的[`DataTemplate`構造函數](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Type))將定義的屬性設置為物件。 `Cell`有五種衍生物:

- [`TextCell`](xref:Xamarin.Forms.TextCell)&mdash;包含兩種`Label`觀點(概念上)
- [`ImageCell`](xref:Xamarin.Forms.ImageCell)&mdash;將`Image`檢視加入`TextCell`
- [`EntryCell`](xref:Xamarin.Forms.EntryCell)&mdash;包含具有`Entry``Label`
- [`SwitchCell`](xref:Xamarin.Forms.SwitchCell)&mdash;包含`Switch``Label`
- [`ViewCell`](xref:Xamarin.Forms.ViewCell)&mdash;可以是任何`View`(可能與兒童)

然後調用[`SetValue`](xref:Xamarin.Forms.DataTemplate.SetValue(Xamarin.Forms.BindableProperty,System.Object))[`SetBinding`](xref:Xamarin.Forms.DataTemplate.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase))`DataTemplate`物件 以將`Cell`值與 屬性相關聯,`Cell``ItemsSource`或設置引用 集合中項屬性的屬性的數據綁定。 這在[**文本清單代碼**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListCode)範例中演示。

當每個項由 顯示`ListView`時 ,從範本構造了一個小可視化樹,並且數據綁定在此可視化樹中的元素的屬性之間建立。 您可以[`ItemAppearing`](xref:Xamarin.Forms.ListView.ItemAppearing)通過 為[`ItemDisappearing`](xref:Xamarin.Forms.ListView.ItemDisappearing)的`ListView`和事件安裝處理程式,或者使用使用每次必須創建項的可視化樹時調用的函數的替代[`DataTemplate`建構函數](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Func{System.Object}))來瞭解此過程。

[**TextCellListXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListXaml)在 XAML 中顯示功能上完全相同的程式。 標記`DataTemplate`設定`ItemTemplate`為的`ListView`屬性`TextCell`,然後設定`DataTemplate`為 。 對集合中項屬性的綁定直接設置在的[`Text`](xref:Xamarin.Forms.TextCell.Text)和屬性[`Detail`](xref:Xamarin.Forms.TextCell.Detail)上。 `TextCell`

### <a name="custom-cells"></a>自訂儲存格

在 XAML 中,[`ViewCell`](xref:Xamarin.Forms.ViewCell)可以將`DataTemplate`設定為, 然後將自訂可視化[`View`](xref:Xamarin.Forms.ViewCell.View)`ViewCell`樹定義為 的屬性。 (`View`是`ViewCell`的內容`ViewCell.View`屬性, 因此不需要標記。[**自訂命名 ColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CustomNamedColorList)範例展示以下技術:

[![自訂命名顏色清單的三重螢幕擷取](images/ch19fg11-small.png "自訂命名顏色清單")](images/ch19fg11-large.png#lightbox "自訂命名顏色清單")

為所有平台正確調整大小可能比較棘手。 該[`RowHeight`](xref:Xamarin.Forms.ListView.RowHeight)屬性很有用,但在某些情況下,您需要求助[`HasUnevenRows`](xref:Xamarin.Forms.ListView.HasUnevenRows)於 該屬性,該屬性效率較低,但`ListView`強制對行進行大小調整。 對於 iOS 和 Android,您必須使用這兩個屬性之一才能獲得正確的行大小調整。

### <a name="grouping-the-listview-items"></a>以清單檢視項目進行群組

`ListView`支援專案分組並在這些組之間導航。 屬性`ItemsSource`必須設定為集合的集合`ItemsSource`: 設定為`IEnumerable`必須實現的物件,集合中的每個項也`IEnumerable`必須實現 。 每個組應包括兩個屬性:組的文本說明和三個字母的縮寫。

[**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫`NamedColor`[`NamedColorGroup`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColorGroup.cs)中的 類創建七組物件。 [**ColorGroupList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorGroupList)[`IsGroupingEnabled`](xref:Xamarin.Forms.ListView.IsGroupingEnabled)範例展示如何將這些組`ListView`與`true`設定為的屬性一起使用,以及綁[`GroupDisplayBinding`](xref:Xamarin.Forms.ListView.GroupDisplayBinding)定[`GroupShortNameBinding`](xref:Xamarin.Forms.ListView.GroupShortNameBinding)到 每個組中的屬性和 屬性。

### <a name="custom-group-headers"></a>自訂群組標頭

可以通過`ListView``GroupDisplayBinding`將 屬性替換為標[`GroupHeaderTemplate`](xref:Xamarin.Forms.ListView.GroupHeaderTemplate)頭的 範本來為組創建自訂標頭。

### <a name="listview-and-interactivity"></a>清單檢視與互動性

通常`ListView`,應用程式通過將處理程式附加`ItemSelected`到[`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped)或 事件`SelectedItem`或通過在 屬性上設置數據綁定來獲取與的使用者交互。 但是某些儲存格型態`EntryCell`(`SwitchCell`和) 允許使用者互動,也可以創建自訂單元格,這些儲存格本身與使用者互動。 [**InteractiveListView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/InteractiveListView)建立 100 個[`ColorViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs)實體,允許使用者使用三組元素更改每`Slider`種顏色 。 該程式還利用了[`ColorToContrastColorConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorToContrastColorConverter.cs) [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)中的 。

## <a name="listview-and-mvvm"></a>清單檢視與 MVVM

`ListView`在 MVVM 方案中扮演重要角色。 每當`IEnumerable`集合存在於 ViewModel 中時,它通常綁`ListView`定到 。 此外,集合中的項通常實現`INotifyPropertyChanged`與範本中的屬性綁定。

### <a name="a-collection-of-viewmodels"></a>檢視模型的集合

為了探索這一點[**,SchoolOfArts**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt)圖書館基於[XML 數據檔和](https://xamarin.github.io/xamarin-forms-book-samples/SchoolOfFineArt/students.xml)虛構學校虛構學生的圖像創建多個課程。

類別[`Student`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/Student.cs)的類別[`ViewModelBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/ViewModelBase.cs)。 類別[`StudentBody`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/StudentBody.cs)是`Student`物件的集合,也派生`ViewModelBase`自 。 下載[`SchoolViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/SchoolViewModel.cs)XML 檔並組裝所有物件。

[**學生清單**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/StudentList)程式使用`ImageCell`在 中顯示學生`ListView`及其影像:

[![學生清單的三重螢幕截圖](images/ch19fg18-small.png "學生名單")](images/ch19fg18-large.png#lightbox "學生名單")

[**ListViewHeader**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewHeader)示例添加了[`Header`](xref:Xamarin.Forms.ListView.Header)一 個屬性,但它只顯示在 Android 上。

### <a name="selection-and-the-binding-context"></a>選擇與繫結中文

[**選擇學生詳細資訊程式**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/SelectedStudentDetail)會`BindingContext`將`StackLayout``SelectedItem`的 結合的屬性`ListView`。 這允許程序顯示有關所選學生的詳細資訊。

### <a name="context-menus"></a>操作功能表

單元格可以定義以特定於平臺的方式實現的上下文菜單。 要創建此功能表,請[`MenuItem`](xref:Xamarin.Forms.MenuItem)向[`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions)的屬性`Cell`添加物件。

`MenuItem`定義五個屬性:

- [`Text`](xref:Xamarin.Forms.MenuItem.Text)類型`string`
- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon)類型`FileImageSource`
- [`IsDestructive`](xref:Xamarin.Forms.MenuItem.IsDestructive)類型`bool`
- [`Command`](xref:Xamarin.Forms.MenuItem.Command)類型`ICommand`
- [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter)類型`object`

和`Command``CommandParameter`屬性表示每個專案的ViewModel包含執行所需功能表命令的方法。 在非 MVVM 專案`MenuItem`中,[`Clicked`](xref:Xamarin.Forms.MenuItem.Clicked)還會定義事件 。

[**單元格上下文功能表**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CellContextMenu)演示此技術。 每個`Command``MenuItem`屬性綁定`ICommand``Student`到 類中的類型屬性。 將`IsDestructive`刪除或`true`移除選取的物件`MenuItem`的屬性設定為

### <a name="varying-the-visuals"></a>改變視覺效果

有時,您需要`ListView`根據屬性對中的項的視覺效果稍有變化。 例如,當學生的年級平均值低於 2.0 時,「[**顏色編碼學生」**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorCodedStudents)範例以紅色顯示該學生的姓名。
這是透過使用綁定值轉換器來實現的,[`ThresholdToObjectConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ThresholdToObjectConverter.cs)在[**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中。

### <a name="refreshing-the-content"></a>更新內容

支援`ListView`用於刷新其數據的下拉手勢。 程式必須將[`IsPullToRefresh`](xref:Xamarin.Forms.ListView.IsPullToRefreshEnabled)`true`屬性設置為啟用此功能。 通過將`ListView`[`IsRefreshing`](xref:Xamarin.Forms.ListView.IsRefreshing)屬性設置`true`為 ()以及[`Refreshing`](xref:Xamarin.Forms.ListView.Refreshing)引發事件和(對於 MVVM 方案`Execute`[`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand))調用其 屬性的方法來回應下拉手勢。

`Refresh`行程或`RefreshCommand`可能更新與顯示的資料,`ListView`並設定`IsRefreshing`回 。 `false`

[**RsSFeed**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/RssFeed)範例展示使用[`RssFeedViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/RssFeed/RssFeed/RssFeed/RssFeedViewModel.cs),`RefreshCommand``IsRefreshing`實現與屬性進行資料綁定。

## <a name="the-tableview-and-its-intents"></a>表視圖及其意圖

雖然`ListView`通常顯示同一類型的多個實例,但[`TableView`](xref:Xamarin.Forms.TableView)通常側重於為各種類型的多個屬性提供用戶介面。 每個專案都與自己的[`Cell`](xref:Xamarin.Forms.Cell)派生項相關聯,用於顯示屬性或向其提供用戶介面。

### <a name="properties-and-hierarchies"></a>屬性和層次結構

`TableView`只定義四個屬性:

- [`Intent`](xref:Xamarin.Forms.TableView.Intent)型[`TableIntent`](xref:Xamarin.Forms.TableIntent)態 , 列舉
- [`Root`](xref:Xamarin.Forms.TableView.Root)的類型[`TableRoot`](xref:Xamarin.Forms.TableRoot),內容屬性`TableView`
- [`RowHeight`](xref:Xamarin.Forms.TableView.RowHeight)類型`int`
- [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows)類型`bool`

Entttttttttttt`TableIntent` `TableView`

- [`Data`](xref:Xamarin.Forms.TableIntent.Data)
- [`Form`](xref:Xamarin.Forms.TableIntent.Form)
- [`Settings`](xref:Xamarin.Forms.TableIntent.Settings)
- [`Menu`](xref:Xamarin.Forms.TableIntent.Menu)

這些成員還建議對`TableView`的一些用途。

定義表時還涉及其他幾個類:

- [`TableSectionBase`](xref:Xamarin.Forms.TableSectionBase)是派生自`BindableObject`並定義屬性[`Title`](xref:Xamarin.Forms.TableSectionBase.Title)的 抽象類

- [`TableSectionBase<T>`](xref:Xamarin.Forms.TableSectionBase`1)是一個抽象類,派生自`TableSectionBase`和`IList<T>`實現 和`INotifyCollectionChanged`

- [`TableSection`](xref:Xamarin.Forms.TableSection)來自`TableSectionBase<Cell>`

- [`TableRoot`](xref:Xamarin.Forms.TableRoot)來自`TableSectionBase<TableSection>`

`TableView`簡而言之,具有一個`Root`屬性,您設置為一`TableRoot`個 物件,該`TableSection`對像是 對象的集合`Cell`,每個物件都是 物件的集合。 表有多個節,每個部分有多個單元格。 表本身可以有一個標題,並且每個部分可以有一個標題。 雖然`TableView`利用了`Cell`衍生工具,但它不`DataTemplate`使用 。

### <a name="a-prosaic-form"></a>平淡的形式

[**EntryForm**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/EntryForm)範例定義一[`PersonalInformation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs)個 檢視模型,其實例`BindingContext`將成為的實例`TableView`。 然後`Cell`,`TableSection`其 中的每個派生項`PersonalInformation`都可以對 類的屬性具有綁定。

### <a name="custom-cells"></a>自訂儲存格

[**條件單元格**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalCells)示例在**條目窗體**上展開。 類[`ProgrammerInformation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs)包括一個布爾屬性,該屬性控制兩個附加屬性的適用性。 對於這兩個附加屬性,程式使用基於`PickerCell`[PickerCell.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml)的自定義,並在[**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中[PickerCell.xaml.cs。](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml.cs)

儘管兩`IsEnabled``PickerCell`個元素的屬性綁定到`ProgrammerInformation`中的 布爾屬性,但此技術似乎不起作用,這提示下一個示例。

### <a name="conditional-sections"></a>條件部份

[**"條件節"**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalSection)範例將以選擇布林項為條件的兩個項放在`TableSection`單獨的 中。 代碼背後的檔從 中刪除此`TableView`部分,或基於布林屬性將其添加回來。

### <a name="a-tableview-menu"></a>表檢視選單

另一`TableView`個用途是功能表。 [**Menu命令**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/MenuCommands)範例展示一個選單,該選單允許您在螢幕上`BoxView`移動一點。

## <a name="related-links"></a>相關連結

- [第十九章 全文(PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch19-Apr2016.pdf)
- [第19章 樣本](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)
- [選擇器](~/xamarin-forms/user-interface/picker/index.md)
- [ListView](~/xamarin-forms/user-interface/listview/index.md)
- [TableView](~/xamarin-forms/user-interface/tableview.md)
