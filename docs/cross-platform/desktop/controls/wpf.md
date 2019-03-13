---
ms.assetid: 1BB412D1-FC3D-4E69-8B01-B976A3DB6328
title: WPF 和。Xamarin.Forms： 相似性與差異
description: 這份文件相比較，並對照 WPF Xamarin.Forms。 它討論控制項範本、 XAML、 繫結基礎結構、 資料範本，ItemsControl，UserControl、 導覽和 URL 導覽。
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: d3e772c270f6003d01e3e7b487f69f682fec2d61
ms.sourcegitcommit: 6be6374664cd96a7d924c2e0c37aeec4adf8be13
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2018
ms.locfileid: "51617588"
---
# <a name="wpf-vs-xamarinforms-similarities--differences"></a>WPF 和。Xamarin.Forms： 相似性與差異

## <a name="control-templates"></a>控制項範本

WPF 支援的概念*控制項樣板*提供控制項的視覺效果指示 (`Button`， `ListBox`，依此類推。)。 如先前所述，Xamarin.Forms 使用具體_轉譯_原生平台 （iOS、 Android 等等），以視覺化方式檢視控制項進行互動的類別。

不過，Xamarin.Forms_並未_有`ControlTemplate`類型-它用於佈景主題`Page`物件。 它提供的定義`Page`，提供一致的內容，但可讓變更色彩、 字型等，甚至新增項目，使其成為唯一的應用程式頁面的使用者。

這個常用的是項目，例如驗證對話方塊，提示，並提供可自訂在應用程式標準化，但可成為佈景主題的頁面外觀與風格。 這項支援的一部分，會使用許多熟悉的 WPF 名為控制項：

1. `ContentPage`
2. `ContentView`
3. `ContentPresenter`
4. `TemplateBinding`

但請務必知道，這些都是_不_在 Xamarin.Forms 中提供相同的目的。 如需有關這項功能的詳細資訊，請參閱[文件頁面](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md)。

## <a name="xaml"></a>XAML

XAML 是做為宣告式標記語言用於 WPF 和 Xamarin.Forms。 大部分的情況下，這個語法完全相同-主要差異是由 XAML 圖形定義/建立的物件。

- 支援 Xamarin.Forms [XAML 2009 規格](/dotnet/framework/xaml-services/xaml-2009-language-features/); 這可讓您更輕鬆地定義資料，例如`string`s， `int`s、 等等，以及定義的泛型型別和引數傳遞給建構函式。

- 目前沒有任何方法可以使用 WPF 動態載入 XAML `XamlReader`。 不過，您可以透過 [NuGet 套件](https://www.nuget.org/packages/Xamarin.Forms.Dynamic/)取得類似的基本功能。

### <a name="markup-extensions"></a>標記延伸

Xamarin.Forms 可支援擴充透過標記延伸模組，如同 WPF 的 XAML。 根據預設，它會有相同的基本建置組塊：

1. `{x:Array}`
2. `{Binding}`
3. `{DynamicResource}`
4. `{x:Null}`
5. `{x:Static}`
6. `{StaticResource}`
7. `{x:Type}`

此外，它包含`{x:Reference}`XAML 2009 規格，並`{TemplateBinding}`標記延伸模組用來特製化版本`ControlTemplate`Xamarin.Forms 所支援。

> [!WARNING]
> `ControlTemplate`不支援相同-即使它具有相同的名稱。

Xamarin.Forms 支援以及-自訂標記延伸模組，但其實作稍有不同。 在 WPF 中，您必須衍生自`MarkupExtension`-抽象的基底類別。 會在 Xamarin.Forms 中，取代介面`IMarkupExtension`或`IMarkupExtension<T>`這是更有彈性。

如同 WPF 中，單一所需的方法是`ProvideValue`來自標記延伸模組傳回值的方法。

## <a name="binding-infrastructure"></a>繫結基礎結構

結轉的核心概念是資料繫結基礎結構連線至.NET 資料屬性的 視覺屬性。 這可讓等 MVVM 架構模式。 基本的設計完全相同-您必須在可繫結的基底類別[BindableObject](xref:Xamarin.Forms.BindableObject)，這是 wpf [DependencyObject](xref:System.Windows.DependencyObject)類別。 這個基底類別作為根上階的所有物件，將加入為資料繫結中的目標。 在衍生的類別然後公開[BindableProperty](xref:Xamarin.Forms.BindableProperty)物件做為屬性值的備份儲存體 (這些定義為[DependencyProperty](xref:System.Windows.DependencyProperty)在 WPF 中的物件)。

### <a name="defining-bindable-properties"></a>定義可繫結屬性

在 Xamarin.Forms 中的可繫結屬性的定義是 WPF 相同：
1. 物件必須衍生自`BindableObject`。
2. 必須是類型的公用靜態欄位`BindableProperty`宣告來定義屬性的備份儲存體金鑰。
3. 應該使用的公用執行個體屬性包裝函式`GetValue`和`SetValue`來擷取和變更的屬性值。

如需完整範例，請參閱 < [Xamarin.Forms 中的可繫結屬性](~/xamarin-forms/xaml/bindable-properties.md)。

### <a name="attached-properties"></a>附加的屬性

附加的屬性是可繫結屬性的子集，其運作方式相同在 WPF 中。 主要差別在於屬性包裝函式在此情況下是省略此步驟，並取代為一組靜態的 get/set 方法上主控的類別。 請參閱[附加屬性，在 Xamarin.Forms 中](~/xamarin-forms/xaml/attached-properties.md)如需詳細資訊。

### <a name="using-the-binding-engine"></a>使用繫結引擎

因為它是在 WPF 中使用繫結引擎的程序都是相同的。 您可以藉由建立利用程式碼後置中`Binding`物件繫結至來源物件 （任何.NET 類型） 和選用的屬性值 (如果省略此步驟，它會將來源物件視為屬性本身-就像 WPF)。 然後您可以使用`SetBinding`任何`BindableObject`產生關聯的繫結`BindableProperty`。

或者，您可以定義在 XAML 中使用的繫結關係`BindingExtension`。 它在 WPF 中有相同的基本值做為副檔名。

繫結支援和引擎會更類似於 Silverlight 實作比 WPF。 有數個遺漏的功能已不會實作在 Xamarin.Forms 中：

- 沒有任何支援的繫結中的下列功能：
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

不支援`RelativeSource`繫結。 在 WPF 中，這些可讓您將繫結至 XAML 中定義的其他視覺元素。 在 Xamarin.Forms 中，這項功能，可透過`{x:Reference}`標記延伸。 例如，假設我們有具有名稱"otherControl 」 有文字屬性的控制項，我們可以繫結至它像這樣：

**WPF**

```xaml
Text={Binding RelativeSource={RelativeSource otherControl}, Path=Text}
```

**Xamarin.Forms**

```xaml
Text={Binding Source={x:Reference otherControl}, Path=Text}
```

相同的功能可用於`{RelativeSource Self}`功能。 不過沒有尋找上的階類型的支援 (`{RelativeSource FindAncestor}`)。

#### <a name="binding-context"></a>繫結內容

在 WPF 中，您可以定義`DataContext`屬性值的 reprents 來源繫結的預設值。 如果未定義的繫結來源，則會使用這個屬性值。 減少視覺化樹狀結構，讓它可以在較高層級定義，然後由子系繼承值。

在 Xamarin.Forms 中，這個相同的功能是可使用的但是屬性名稱是`BindingContext`。

#### <a name="value-converters"></a>值轉換器

Xamarin.Forms-就像 WPF 中完全支援值轉換器。 使用相同的 [介面] 圖形，但 Xamarin.Forms 中定義的介面`Xamarin.Forms`命名空間。

### <a name="model-view-viewmodel"></a>Model View ViewModel

MVVM 完全支援 WPF 和 Xamarin.Forms。

WPF 中包含的內建`RoutedCommand`有時用;Xamarin.Forms 的任何命令的內建支援，超過`ICommand`介面定義。 您可以包含各種不同的 MVVM 架構，以加入必要的基底類別實作 MVVM。

#### <a name="inotifypropertychanged-and-inotifycollectionchanged"></a>INotifyPropertyChanged 和 INotifyCollectionChanged

Xamarin.Forms 繫結中完全支援這兩個介面。 不同於許多以 XAML 為基礎的架構，可在 Xamarin.Forms （正如 WPF) 中的背景執行緒上引發屬性變更通知，並繫結引擎就會正確地轉換至 UI 執行緒。

此外，這兩種環境支援`SynchronziationContext`並`async` / `await`執行適當的執行緒封送處理。 WPF 包含`Dispatcher`類別上所有的視覺元素，Xamarin.Forms 具有靜態方法`Device.BeginInvokeOnMainThread`也可 (雖然`SynchronizationContext`十分適用於跨平台撰寫程式碼)。

- Xamarin.Forms 包含`ObservableCollection<T>`支援變更通知。
- 您可以使用`BindingBase.EnableCollectionSynchronization`以啟用跨執行緒更新集合。 API 會稍有不同的 WPF 種變化中，從[核取 使用方式詳細資料的文件](xref:Xamarin.Forms.BindingBase.EnableCollectionSynchronization*)。

## <a name="data-templates"></a>資料範本

支援的資料範本中自訂轉譯的 Xamarin.Forms`ListView`資料列 （資料格）。 不同於其可以使用 WPF`DataTemplate`的任何內容導向的控制項，而 Xamarin.Forms 目前只會使用它們的`ListView`。 範本定義可以內嵌方式定義 (指派給`ItemTemplate`屬性)，或為中的資源`ResourceDictionary`。

此外，它們並非相當具有彈性，其 WPF 對應項目。

1. 根項目`DataTemplate`必須_一律_是`ViewCell`物件。
2. 完全支援在資料範本中，資料觸發程序，但必須包含`DataType`指出觸發程序相關聯的屬性類型的屬性。
3. `DataTemplateSelector` 也支援，但衍生自`DataTemplate`，並因此只會直接指派`ItemTemplate`屬性 (與`ItemTemplateSelector`在 WPF 中)。

## <a name="itemscontrol"></a>ItemsControl

沒有以任何內建 equivelent `ItemsControl` xamarin.forms; 但沒有[自訂的另一個用於 Xamarin.Forms 這裡提供](https://github.com/xamarinhq/xamu-infrastructure/blob/master/src/XamU.Infrastructure/Controls/ItemsControl.cs)。

## <a name="user-controls"></a>使用者控制項

在 WPF 中， `UserControl`s 可用來提供一段 UI 有相關聯的行為。 在 Xamarin.Forms 中，我們使用`ContentView`達成相同目的。 兩者都支援繫結，也可包含在 XAML 中。

## <a name="navigation"></a>巡覽

WPF 包含可用來提供「類瀏覽器」瀏覽功能的罕用 `NavigationService`。 大部分的應用程式不會為此費心，而會改為使用 `Window` 或不同的視窗區段來顯示資料。

在不同的電話裝置上_螢幕_通常解決方案，並因此 Xamarin.Forms 包含數種形式的導覽的支援：

| 導覽樣式 | 頁面類型 |
|--- |--- |
|堆疊為基礎 （推入/彈出）|NavigationPage|
|主要/詳細資料|MasterDetailPage|
|定位點|TabbedPage|
|揮擊左/右|CarouselView|

`NavigationPage`是最常見的方法，而且每一頁`Navigation`屬性可用來推送或快顯開啟或關閉導覽堆疊上的頁面。 這是以最接近的 equivelent`NavigationService`在 WPF 中找到。

### <a name="url-navigation"></a>URL 巡覽

WPF 是桌面導向技術，並且可以接受命令列直接啟動行為的參數。 可以使用 Xamarin.Forms[深層連結 URL](https://blog.xamarin.com/deep-link-content-with-xamarin-forms-url-navigation/)跳至頁面上啟動。
