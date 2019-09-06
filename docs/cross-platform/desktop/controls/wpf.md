---
ms.assetid: 1BB412D1-FC3D-4E69-8B01-B976A3DB6328
title: WPF 與Xamarin. 表單：相似 & 差異
description: 本檔會比較 WPF 與 Xamarin 的格式，並將其對照。 其中討論控制項範本、XAML、系結基礎結構、資料範本、ItemsControl、UserControl、導覽和 URL 流覽。
author: conceptdev
ms.author: crdun
ms.date: 04/26/2017
ms.openlocfilehash: d23b449382183b0385eac38c0b9205e48dbe0a34
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290400"
---
# <a name="wpf-vs-xamarinforms-similarities--differences"></a>WPF 與Xamarin. 表單：相似 & 差異

## <a name="control-templates"></a>控制項範本

WPF 支援*控制項範本*的概念，可提供控制項（`Button`、 `ListBox`等）的視覺效果指示。 如上所述，Xamarin 會使用適用于這種情況的具體_呈現_類別，這會與原生平臺（IOS、Android 等）互動以視覺化控制項。

不過，Xamarin_的確_有一個`ControlTemplate`型別，用於主題`Page`物件。 它會提供的定義， `Page`以提供一致的內容，但可讓頁面的使用者變更色彩、字型等，甚至新增專案，使其對應用程式而言是唯一的。

這種情況的常見用法包括驗證對話方塊、提示和提供標準化的，但可在應用程式內自訂的 themable 頁面外觀與風格。 在這項支援的過程中，會使用許多熟悉的 WPF 命名控制項：

1. `ContentPage`
2. `ContentView`
3. `ContentPresenter`
4. `TemplateBinding`

但請務必瞭解，這些服務在 Xamarin 中的用途並_不_相同。 如需這項功能的詳細資訊，請參閱[檔頁面](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md)。

## <a name="xaml"></a>XAML

XAML 是用來做為 WPF 和 Xamarin 的宣告式標記語言。 在大部分的情況下，語法是相同的，主要差異在於 XAML 圖形所定義/建立的物件。

- Xamarin 支援[XAML 2009 規格](/dotnet/framework/xaml-services/xaml-2009-language-features/);這可讓您更輕鬆地定義資料`string`（例如`int`s、s 等），以及定義泛型型別並將引數傳遞至函式。

- 目前沒有任何方法可以使用 WPF 動態載入 XAML `XamlReader`。 不過，您可以透過 [NuGet 套件](https://www.nuget.org/packages/Xamarin.Forms.Dynamic/)取得類似的基本功能。

### <a name="markup-extensions"></a>標記延伸

Xamarin 支援透過標記延伸來擴充 XAML，與 WPF 很相似。 它具有相同的基本建立區塊：

1. `{x:Array}`
2. `{Binding}`
3. `{DynamicResource}`
4. `{x:Null}`
5. `{x:Static}`
6. `{StaticResource}`
7. `{x:Type}`

此外，它包含`{x:Reference}` XAML 2009 規格中的， `{TemplateBinding}`以及用於 Xamarin 所支援之特殊化版本的`ControlTemplate`標記延伸。

> [!WARNING]
> 此`ControlTemplate`支援不相同，即使它具有相同的名稱。

Xamarin 也支援自訂標記延伸模組，但執行方式稍有不同。 在 WPF 中，您必須衍生`MarkupExtension`自-抽象基類。 在 Xamarin 中，會由介面`IMarkupExtension`取代或`IMarkupExtension<T>`較具彈性。

就像 WPF，單一所需的方法是`ProvideValue`從標記延伸傳回值的方法。

## <a name="binding-infrastructure"></a>系結基礎結構

其中一個執行的核心概念是資料系結基礎結構，可將視覺屬性連接至 .NET 資料屬性。 這會啟用像是 MVVM 的架構模式。 基本設計完全相同-您有一個可系結的基類[BindableObject](xref:Xamarin.Forms.BindableObject)，在 WPF 中，這是[system.windows.dependencyobject>](xref:System.Windows.DependencyObject)類別。 這個基類是做為將參與資料系結中目標之所有物件的根上階。 然後，衍生的類別會公開[BindableProperty](xref:Xamarin.Forms.BindableProperty)物件，做為屬性值的支援儲存體（在 WPF 中定義為[DependencyProperty](xref:System.Windows.DependencyProperty)物件）。

### <a name="defining-bindable-properties"></a>定義可繫結屬性

在 Xamarin 中，可系結屬性的定義與 WPF 相同：
1. 物件必須衍生自`BindableObject`。
2. 必須宣告類型`BindableProperty`的公用靜態欄位，才能定義屬性的支援儲存體金鑰。
3. 應該有一個公用實例屬性包裝函式使用`GetValue`和`SetValue`來抓取和變更屬性值。

如需完整範例，請參閱[在 Xamarin 中](~/xamarin-forms/xaml/bindable-properties.md)的可系結屬性。

### <a name="attached-properties"></a>附加的屬性

附加屬性是可系結屬性的子集，而且其作用方式與在 WPF 中相同。 主要的差異在於，在此情況下會省略此步驟屬性包裝函式，並將其取代為擁有類別上的一組靜態 get/set 方法。 如需詳細資訊，請參閱[Xamarin. Forms 中的附加屬性](~/xamarin-forms/xaml/attached-properties.md)。

### <a name="using-the-binding-engine"></a>使用系結引擎

使用系結引擎的程式與在 WPF 中的處理方式相同。 它可以在程式碼後置中使用，方法`Binding`是建立系結至來源物件（任何 .net 型別）和選擇性屬性值（如果省略此步驟，它會將來源物件視為屬性本身-就像 WPF）。 接著，您可以`SetBinding`在任何`BindableObject`上使用來`BindableProperty`建立系結的關聯。

或者，您可以使用`BindingExtension`，在 XAML 中定義系結關聯性。 它與 WPF 中的延伸模組具有相同的基本值。

系結支援和引擎比 WPF 更類似 Silverlight 的執行。 有數個遺漏的功能並未在 Xamarin 中執行。表單：

- 不支援系結中的下列功能：
  - BindingGroupName
  - BindsDirectlyToSource
  - IsAsync
  - MultiBinding
  - NotifyOnSourceUpdated
  - NotifyOnTargetUpdated
  - NotifyOnValidationError
  - UpdateSourceTrigger
  - UpdateSourceExceptionFilter
  - ValidatesOnDataErrors
  - ValidatesOnExceptions
  - ValidationRules 集合
  - XPath
  - XmlNamespaceManager

#### <a name="relativesource"></a>RelativeSource

不支援`RelativeSource`系結。 在 WPF 中，這些可讓您系結至 XAML 中定義的其他視覺元素。 在 Xamarin 中，可以使用`{x:Reference}`標記延伸來達成這項相同的功能。 例如，假設我們有一個名稱為 "otherControl" 的控制項具有 Text 屬性，我們可以將它系結到它，如下所示：

**WPF**

```xaml
Text={Binding RelativeSource={RelativeSource otherControl}, Path=Text}
```

**Xamarin.Forms**

```xaml
Text={Binding Source={x:Reference otherControl}, Path=Text}
```

相同的功能也可以用於`{RelativeSource Self}`功能。 不過，不支援依類型（`{RelativeSource FindAncestor}`）尋找祖系。

#### <a name="binding-context"></a>系結內容

在 WPF 中，您可以定義`DataContext` reprents 預設系結來源的屬性值。 如果未定義系結的來源，則會使用這個屬性值。 此值會在視覺化樹狀結構中向下繼承，讓它在較高的層級上定義，然後由子系使用。

在 Xamarin 中，這項功能是多寡的，但屬性名稱是`BindingContext`。

#### <a name="value-converters"></a>值轉換器

在 Xamarin 中，完全支援值轉換器。表單就像 WPF 一樣。 使用相同的介面圖形，但是 Xamarin. 表單具有在`Xamarin.Forms`命名空間中定義的介面。

### <a name="model-view-viewmodel"></a>Model-View-ViewModel

以 WPF 和 Xamarin 完全支援 MVVM。

WPF 包含內`RoutedCommand`建的，其有時會使用;Xamarin 不提供介面定義以外的`ICommand`內建命令支援。 您可以包含各種 MVVM 架構，以加入必要的基類來執行 MVVM。

#### <a name="inotifypropertychanged-and-inotifycollectionchanged"></a>INotifyPropertyChanged 和 INotifyCollectionChanged

Xamarin. Forms 系結完全支援這兩個介面。 與許多以 XAML 為基礎的架構不同的是，屬性變更通知可以在 Xamarin 的背景執行緒上引發（就像 WPF 一樣），而系結引擎會適當地轉換至 UI 執行緒。

此外，這兩個環境`SynchronziationContext`都`async`支援和/ `await` ，以進行適當的執行緒封送處理。 WPF 在所有`Dispatcher`視覺元素上都包含類別，而 Xamarin 則有一個也`Device.BeginInvokeOnMainThread`可以使用的靜態方法（雖然`SynchronizationContext`對於跨平臺程式碼偏好是慣用的）。

- Xamarin 包含`ObservableCollection<T>`支援集合變更通知的。
- 您可以使用`BindingBase.EnableCollectionSynchronization`來啟用集合的跨執行緒更新。 此 API 與 WPF 變化稍有不同，請[參閱檔以取得使用方式詳細資料](xref:Xamarin.Forms.BindingBase.EnableCollectionSynchronization*)。

## <a name="data-templates"></a>資料範本

資料範本在 Xamarin 中受到支援，可自訂資料`ListView`列（儲存格）的呈現。 不同于可針對任何`DataTemplate`內容導向控制項使用的 WPF，Xamarin. 表單目前只會將`ListView`它們用於。 範本定義可以用內嵌方式定義（指派給`ItemTemplate`屬性），或是做為`ResourceDictionary`中的資源。

此外，它們與其 WPF 對應項的彈性並不一樣。

1. 的根項目`DataTemplate`必須`ViewCell` _一律_為物件。
2. 資料範本中完全支援資料觸發程式，但必須包含`DataType`屬性，以指出與觸發程式相關聯的屬性類型。
3. `DataTemplateSelector`也支援，但衍生自`DataTemplate` ，因此只會直接`ItemTemplate`指派給屬性（ `ItemTemplateSelector`在 WPF 中為 vs）。

## <a name="itemscontrol"></a>ItemsControl

`ItemsControl`在 xamarin 中，沒有任何內建的對等用法，但有一個[適用于 xamarin 的自訂。您可以在這裡取得表單](https://github.com/xamarinhq/xamu-infrastructure/blob/master/src/XamU.Infrastructure/Controls/ItemsControl.cs)。

## <a name="user-controls"></a>使用者控制項

在 WPF 中`UserControl`，會使用來提供具有相關聯線為的 UI 區段。 在 Xamarin 中，我們使用`ContentView`來進行相同的用途。 支援在 XAML 中系結和包含。

## <a name="navigation"></a>巡覽

WPF 包含可用來提供「類瀏覽器」瀏覽功能的罕用 `NavigationService`。 大部分的應用程式不會為此費心，而會改為使用 `Window` 或不同的視窗區段來顯示資料。

在電話裝置上，不同的_畫面_通常是解決方案，因此 Xamarin 會支援數種形式的導覽：

| 導覽樣式 | 頁面類型 |
|--- |--- |
|堆疊型（推播/pop）|NavigationPage|
|主版/詳細|MasterDetailPage|
|定位點|TabbedPage|
|向左/向右滑動|CarouselView|

是最常見的方法，而且每個頁面都有`Navigation`一個屬性，可用來在導覽堆疊上推送或彈出頁面。 `NavigationPage` 這是在 WPF 中`NavigationService`找到的最接近。

### <a name="url-navigation"></a>URL 流覽

WPF 是一種桌面導向的技術，可以接受命令列參數來引導啟動行為。 Xamarin 可以使用[深層 URL 連結](https://blog.xamarin.com/deep-link-content-with-xamarin-forms-url-navigation/)來跳到啟動時的頁面。
