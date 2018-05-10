---
ms.assetid: 1BB412D1-FC3D-4E69-8B01-B976A3DB6328
title: WPF 和。Xamarin.Forms： 相似性和差異
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: 21ffca65ee72308d1340a1db43471228b2adbe91
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
---
# <a name="wpf-vs-xamarinforms-similarities--differences"></a>WPF 和。Xamarin.Forms： 相似性和差異

## <a name="control-templates"></a>控制項範本

WPF 支援的概念*控制項樣板*提供控制項的視覺效果指示 (`Button`，`ListBox`等。)。 Xamarin.Forms 如上面所述，使用實體_轉譯_互動以視覺化方式檢視控制項的原生平台 （iOS、 Android、 等等） 這個類別。

不過，Xamarin.Forms_沒有_有`ControlTemplate`類型-它正用於設定主題的`Page`物件。 它提供的定義`Page`，提供一致的內容，但可讓網頁的使用者變更色彩、 字型、 等等，甚至是加入讓應用程式唯一的項目。

常見的使用方式，這是項目，例如驗證對話方塊，提示，並提供可自訂的應用程式內有標準化，但可成為佈景主題頁面外觀與風格。 這項支援的一部分，會使用許多熟悉的 WPF 名為控制項：

1. `ContentPage`
2. `ContentView`
3. `ContentPresenter`
4. `TemplateBinding`

但請務必了解，這些都是_不_Xamarin.Forms 中提供相同的目的。 如需有關這項功能的詳細資訊，請參閱[文件頁面](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md)。

## <a name="xaml"></a>XAML

XAML 做的宣告式標記語言中，為 WPF 和 Xamarin.Forms。 大部分的情況下，語法是相同-的主要差異是由 XAML 圖形定義/建立的物件。

- Xamarin.Forms 支援[XAML 2009 規格](/dotnet/framework/xaml-services/xaml-2009-language-features/); 這可讓您更輕鬆地定義資料，例如`string`s， `int`s、 等等，以及定義的泛型型別和引數傳遞給建構函式。

- 目前沒有任何方法可以 dyanmically 載入 XAML WPF 可以與像是`XamlReader`。 您可以取得相同的基本功能[NuGet 封裝](https://www.nuget.org/packages/Xamarin.Forms.Dynamic/)雖然。

### <a name="markup-extensions"></a>標記延伸

Xamarin.Forms 可支援擴充 XAML 透過標記延伸和 WPF 很類似。 根據預設，它有相同的基本建置組塊：

1. `{x:Array}`
2. `{Binding}`
3. `{DynamicResource}`
4. `{x:Null}`
5. `{x:Static}`
6. `{StaticResource}`
7. `{x:Type}`

此外，它包含`{x:Reference}`XAML 2009 規格，而`{TemplateBinding}`標記延伸的特製化版本是用於`ControlTemplate`Xamarin.Forms 所支援。

> [!WARNING]
> `ControlTemplate`支援不相同的即使有相同的名稱。

Xamarin.Forms 支援也為自訂標記延伸模組，但實作會有些許不同。 在 WPF 中，您必須衍生自`MarkupExtension`-抽象的基底類別。 在透過 Xamarin.Forms，，會取代介面`IMarkupExtension`或`IMarkupExtension<T>`也就是更有彈性。

如同 WPF 中，單一所需的方法是`ProvideValue`從標記延伸傳回值的方法。

## <a name="binding-infrastructure"></a>繫結基礎結構

資料繫結基礎結構連線至.NET 資料屬性的 視覺屬性其中一個轉的核心概念。 這可讓類 MVVM 架構模式。 基本設計等同-您有可繫結的基底類別[BindableObject](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/)，這是的 WPF 中[DependencyObject](https://msdn.microsoft.com/en-us/library/system.windows.dependencyobject(v=vs.110).aspx)類別。 這個基底類別作為根祖系將加入做為目標的資料繫結中的所有物件。 在衍生的類別，那麼公開[BindableProperty](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)物件做為備份儲存體屬性值 (這些定義為[DependencyProperty](https://msdn.microsoft.com/library/system.windows.dependencyproperty(v=vs.110).aspx) WPF 中的物件)。

### <a name="defining-bindable-properties"></a>定義可繫結屬性

Xamarin.Forms 中的可繫結屬性的定義是 WPF 相同：
1. 物件必須衍生自`BindableObject`。
2. 必須是類型的公用靜態欄位`BindableProperty`宣告來定義屬性的備份儲存體金鑰。
3. 應該使用的公用執行個體內容包裝函式`GetValue`和`SetValue`擷取和變更的屬性值。

如需完整範例，請參閱[可繫結的屬性，在 Xamarin.Forms](~/xamarin-forms/xaml/bindable-properties.md)。

### <a name="attached-properties"></a>附加的屬性

附加的屬性的子集可繫結的屬性以及其在 WPF 中的相同方式運作。 主要差異是屬性的包裝函式在此情況下會省略此步驟取代成一組靜態的 get/set 方法上主控的類別。 請參閱[附加的屬性，在 Xamarin.Forms](~/xamarin-forms/xaml/attached-properties.md)如需詳細資訊。

### <a name="using-the-binding-engine"></a>使用繫結引擎

在 WPF 中使用繫結引擎的程序都是相同的。 它可以利用程式碼後置中建立`Binding`物件繫結至來源物件 （任何.NET 類型） 和選用的屬性值 (如果省略此步驟，它會將來源物件視為屬性本身-如同 WPF)。 然後您可以使用`SetBinding`任何`BindableObject`產生關聯的繫結`BindableProperty`。

或者，您可以定義在 XAML 中使用的繫結關係`BindingExtension`。 它在 WPF 中有相同的基本值做為副檔名。

繫結支援，引擎會更類似 Silverlight 實作於 WPF。 有數個遺漏的功能不在 Xamarin.Forms 中實作的：

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
- `Binding.Mode` 不支援`OneTime`，改為只使用`OneWay`。

#### <a name="relativesource"></a>RelativeSource

不支援針對`RelativeSource`繫結。 在 WPF 中，這些可讓您將繫結至 XAML 中定義其他視覺化項目。 透過 Xamarin.Forms，在這項功能可以使用來達成`{x:Reference}`標記延伸。 例如，假設我們有具有名稱"otherControl 」 具有文字屬性的控制項，我們可以繫結，像這樣：

**WPF**

```xaml
Text={Binding RelativeSource={RelativeSource otherControl}, Path=Text}
```

**Xamarin.Forms**

```xaml
Text={Binding Source={x:Reference otherControl}, Path=Text}
```

相同的功能可用於`{RelativeSource Self}`功能。 但是沒有尋找上的階類型所支援 (`{RelativeSource FindAncestor}`)。

#### <a name="binding-context"></a>繫結內容

在 WPF 中，您可以定義`DataContext`屬性值的 reprents 預設的繫結來源。 如果未定義的繫結來源時，會使用這個屬性值。 關閉視覺化樹狀結構，讓它可以在較高層級定義，然後由子系都被繼承的值。

在透過 Xamarin.Forms，此相同功能是無法，但是屬性名稱是`BindingContext`。

#### <a name="value-converters"></a>值轉換器

值轉換器 Xamarin.Forms-如同 WPF 中完全支援。 使用相同的介面圖形時，但 Xamarin.Forms 中定義的介面`Xamarin.Forms`命名空間。

### <a name="model-view-viewmodel"></a>模型-檢視-ViewModel

MVVM 完全支援 WPF 和 Xamarin.Forms。

WPF 中包含的內建`RoutedCommand`有時用;Xamarin.Forms 已超出沒有內建命令支援`ICommand`介面定義。 您可以包含各種新增必要的基底類別實作 MVVM MVVM 架構。

#### <a name="inotifypropertychanged-and-inotifycollectionchanged"></a>INotifyPropertyChanged 和 INotifyCollectionChanged

Xamarin.Forms 繫結中完全支援這兩個介面。 不同於許多 XAML 為基礎的架構，可以在 Xamarin.Forms （如同 WPF) 中的背景執行緒上引發屬性變更通知並繫結引擎就會正確轉換成 UI 執行緒。

此外，這兩種環境支援`SynchronziationContext`和`async` / `await`執行適當的執行緒封送處理。 WPF 包含`Dispatcher`類別所有的視覺項目，在 Xamarin.Forms 具有靜態方法`Device.BeginInvokeOnMainThread`可以也使用 (雖然`SynchronizationContext`最好使用跨平台撰寫程式碼)。

- Xamarin.Forms 包含`ObservableCollection<T>`可支援集合變更通知。
- 您可以使用`BindingBase.EnableCollectionSynchronization`以允許跨執行緒更新集合。 API 會稍有不同的 WPF 變化，[檢查文件的使用方式詳細資料](https://developer.xamarin.com/api/member/Xamarin.Forms.BindingBase.EnableCollectionSynchronization/)。

## <a name="data-templates"></a>資料範本

若要自訂的轉譯 Xamarin.Forms 中支援資料範本`ListView`資料列 （資料格）。 與不同的是可以利用其中 WPF`DataTemplate`任何內容導向的控制項，而目前 Xamarin.Forms s 只會使用它們的`ListView`。 樣板定義可以是內嵌的定義 (指派給`ItemTemplate`屬性)，或為中的資源`ResourceDictionary`。

此外，它們並非相當具有彈性，其 WPF 對應項目。

1. 根項目`DataTemplate`必須_一律_是`ViewCell`物件。
2. 資料觸發程序完全支援在資料範本中，但必須包含`DataType`指出觸發程序相關聯的屬性類型的屬性。
3. `DataTemplateSelector` 也支援，但衍生自`DataTemplate`，因此只會直接指派`ItemTemplate`屬性 (和比較`ItemTemplateSelector`WPF 中)。

## <a name="itemscontrol"></a>ItemsControl

沒有任何內建 equivelent 至`ItemsControl`在 Xamarin.Forms; 但沒有[自訂的另一個用於 Xamarin.Forms 這裡](https://github.com/xamarinhq/xamu-infrastructure/blob/master/src/XamU.Infrastructure/Controls/ItemsControl.cs)。

## <a name="user-controls"></a>使用者控制項

在 WPF 中， `UserControl`s 用來提供 UI 的相關行為的區段。 透過 Xamarin.Forms，使用`ContentView`達成相同目的。 同時支援繫結和包含在 XAML 中。

## <a name="navigation"></a>巡覽

WPF 包含很少使用`NavigationService`它可以用來提供 「 類似瀏覽器的 「 瀏覽功能。 大部分的應用程式未擔心這不過，改為使用不同`Window`項目或不同區段的視窗，以顯示資料。

在不同的 phone 裝置上_螢幕_通常方案，並因此 Xamarin.Forms 包含數種形式的導覽的支援：

| 導覽樣式 | 頁面類型 |
|--- |--- |
|堆疊為基礎 （推入/彈出）|NavigationPage|
|主要/詳細資料|MasterDetailPage|
|定位點|TabbedPage|
|撥動左/右|CarouselView|

`NavigationPage`是最常見的方式，且每一頁`Navigation`屬性可以用來推入，或將頁面上，瀏覽堆疊。 這是到最接近的 equivelent`NavigationService`在 WPF 中找到。

### <a name="url-navigation"></a>URL 導覽

WPF 是桌面導向的技術，並且可以接受命令列參數，指示啟動行為。 可以使用 Xamarin.Forms[深層連結 URL](https://blog.xamarin.com/deep-link-content-with-xamarin-forms-url-navigation/)跳至頁面上啟動。
