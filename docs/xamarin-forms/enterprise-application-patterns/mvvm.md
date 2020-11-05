---
title: 模型視圖-ViewModel 模式
description: 本章說明 eShopOnContainers 行動應用程式如何使用 MVVM 模式，將應用程式的商務和展示邏輯與使用者介面完全分開。
ms.prod: xamarin
ms.assetid: dd8c1813-df44-4947-bcee-1a1ff2334b87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 78609a6e91a2783d869bafbd88679cfc9be00795
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374156"
---
# <a name="the-model-view-viewmodel-pattern"></a>模型視圖-ViewModel 模式

> [!NOTE]
> 此電子書已在2017的春季發行，且自那時起尚未更新。 本書中有很多工具價值，但有些材質已過期。

Xamarin.Forms開發人員體驗通常牽涉到在 XAML 中建立使用者介面，然後加入在使用者介面上操作的程式碼後端。 隨著應用程式的修改，以及大小與範圍的成長，可能會發生複雜的維護問題。 這些問題包括 UI 控制項與商務邏輯之間的緊密結合，這會增加進行 UI 修改的成本，以及對此程式碼進行單元測試的困難。

ViewModel (MVVM) 模式的模型視圖可協助您將應用程式的商務和展示邏輯與使用者介面 (UI) 完全分開。 在應用程式邏輯和 UI 之間保持乾淨的分隔有助於解決許多開發問題，並可讓應用程式更容易進行測試、維護和演進。 它也可以大幅改善程式碼重複使用的機會，並讓開發人員和 UI 設計工具在開發應用程式的個別元件時，更輕鬆地共同作業。

## <a name="the-mvvm-pattern"></a>MVVM 模式

MVVM 模式中有三個核心元件：模型、視圖及視圖模型。 每個服務都有不同的用途。 圖2-1 顯示三個元件之間的關聯性。

![MVVM 模式](mvvm-images/mvvm.png)

**圖 2-1** ： MVVM 模式

除了瞭解每個元件的職責之外，也請務必瞭解它們彼此互動的方式。 概括而言，「知道」視圖模型和「視圖」模型「知道」模型，但模型不會察覺到視圖模型，而且視圖模型不會察覺到。 因此，視圖模型會從模型中隔離視圖，並允許模型獨立于視圖之外發展。

使用 MVVM 模式的優點如下：

- 如果有封裝現有商務邏輯的現有模型執行，變更它可能會很困難或有風險。 在此案例中，視圖模型會作為模型類別的介面卡，並可讓您避免對模型程式碼進行任何重大變更。
- 開發人員可以建立視圖模型和模型的單元測試，而不需要使用 view。 視圖模型的單元測試可以執行與視圖所使用的功能完全相同的功能。
- 您可以在不觸及程式碼的情況下重新設計應用程式 UI，但前提是該視圖完全是在 XAML 中執行。 因此，新的視圖版本應該使用現有的視圖模型。
- 設計人員和開發人員可以在開發過程中獨立且同時地在其元件上工作。 設計人員可以專注于視圖，而開發人員可以在視圖模型和模型元件上工作。

有效使用 MVVM 的關鍵在於瞭解如何將應用程式程式碼納入正確的類別，以及瞭解類別的互動方式。 下列各節將討論 MVVM 模式中每個類別的責任。

### <a name="view"></a>檢視

此視圖負責定義使用者在螢幕上看到的結構、配置和外觀。 在理想的情況下，每個視圖都是在 XAML 中定義，而程式碼後端則不包含商務邏輯。 不過，在某些情況下，程式碼後端可能會包含 UI 邏輯，以執行難以在 XAML 中表示的視覺行為，例如動畫。

在 Xamarin.Forms 應用程式中，視圖通常是 [`Page`](xref:Xamarin.Forms.Page) 衍生或衍生的 [`ContentView`](xref:Xamarin.Forms.ContentView) 類別。 不過，您也可以使用資料範本來表示視圖，這會指定在顯示物件時，用來以視覺方式呈現物件的 UI 元素。 作為視圖的資料範本不具有任何程式碼後端，而且是設計來系結至特定的視圖模型類型。

> [!TIP]
> 避免啟用和停用程式碼後端中的 UI 元素。 確定視圖模型負責定義邏輯狀態變更，這些變更會影響視圖顯示的某些層面，例如是否有可用的命令，或指出作業已暫止。 因此，系結以查看模型屬性來啟用和停用 UI 專案，而不是在程式碼後端啟用和停用它們。

有幾個選項可在 view 模型上執行程式碼，以回應視圖的互動，例如按一下按鈕或選取專案。 如果控制項支援命令，控制項的 `Command` 屬性可以是資料系結至 `ICommand` 視圖模型上的屬性。 叫用控制項的命令時，將會執行視圖模型中的程式碼。 除了命令之外，行為也可以附加至 view 中的物件，而且可以接聽要叫用的命令或引發的事件。 在回應中，行為可以在視圖模型上叫用， `ICommand` 或在視圖模型上叫用方法。

### <a name="viewmodel"></a>ViewModel

視圖模型會執行 view 可將資料系結至其中的屬性和命令，並透過變更通知事件來通知查看任何狀態變更。 視圖模型提供的屬性和命令可定義 UI 提供的功能，但此視圖會決定該功能的顯示方式。

> [!TIP]
> 使用非同步作業讓 UI 保持回應。 行動應用程式應讓 UI 執行緒保持解除封鎖，以改善使用者對效能的認知。 因此，在 view 模型中，使用非同步方法進行 i/o 作業並引發事件，以非同步方式通知屬性變更的觀點。

視圖模型也負責協調視圖與任何所需模型類別的互動。 視圖模型和模型類別之間通常會有一對多關聯性。 視圖模型可能會選擇直接將模型類別公開給視圖，讓視圖中的控制項可以直接系結至資料。 在此情況下，模型類別必須設計為支援資料系結和變更通知事件。

每個視圖模型都會以表單中的形式提供資料，讓您可以輕鬆地使用。 為了達成此目的，視圖模型有時候會執行資料轉換。 將這項資料轉換放在 view 模型是個不錯的主意，因為它提供了 view 可以系結的屬性。 例如，視圖模型可能會結合兩個屬性的值，讓視圖更容易顯示。

> [!TIP]
> 在轉換層集中集中資料轉換。 您也可以使用轉換器作為位於視圖模型與視圖之間的個別資料轉換層。 這可能是必要的，例如，當資料需要視圖模型未提供的特殊格式時。

為了讓視圖模型參與與視圖的雙向資料系結，其屬性必須引發 `PropertyChanged` 事件。 View 模型會藉由執行介面來滿足這項需求 `INotifyPropertyChanged` ，並 `PropertyChanged` 在屬性變更時引發事件。

如果是集合，則會提供易記的視圖 `ObservableCollection<T>` 。 此集合會執行集合變更通知，讓開發人員不必在 `INotifyCollectionChanged` 集合上執行介面。

### <a name="model"></a>型號

模型類別是封裝應用程式資料的非視覺類別。 因此，您可以將模型視為代表應用程式的領域模型，這通常會包含資料模型以及商務和驗證邏輯。 模型物件的範例包括資料傳輸物件 (Dto) 、簡單的 CLR 物件 (Poco) 以及產生的實體和 proxy 物件。

模型類別通常會搭配用來封裝資料存取和快取的服務或存放庫一起使用。

## <a name="connecting-view-models-to-views"></a>將視圖模型連接至視圖

您可以使用的資料系結功能，將視圖模型連接到 views Xamarin.Forms 。 有許多方法可以用來在執行時間建立視圖和查看模型，並將其關聯。 這些方法分為兩種類別，也就是所謂的 view first 組合和 view model first 組合。 在 view first 組合和 view model first 組合之間進行選擇，是一項喜好和複雜性的問題。 不過，所有方法都有相同的目標，也就是讓視圖將視圖模型指派給其 BindingCoNtext 屬性。

使用 view first 組合時，應用程式在概念上是由連接至它們所依賴之視圖模型的視圖所組成。 這種方法的主要優點是，它可讓您輕鬆地建立鬆散結合的單元測試應用程式，因為視圖模型不會依賴視圖本身。 您也可以藉由遵循視覺效果，輕鬆地瞭解應用程式的結構，而不必追蹤程式碼執行，以瞭解如何建立和關聯類別。 此外，view first 建築會與負責在 Xamarin.Forms 導覽時建立頁面的導覽系統一致，這使得視圖模型的第一個撰寫複雜，並與平臺不相符。

使用 view model first 組合時，應用程式在概念上是由視圖模型組成，而服務則負責尋找視圖模型的視圖。 View model first 組合對某些開發人員來說感覺更加自然，因為建立視圖可以抽象化，讓他們能夠專注于應用程式的邏輯非 UI 結構。 此外，它還允許其他視圖模型建立視圖模型。 不過，這種方法通常很複雜，而且可能會變得難以瞭解應用程式的各個部分如何建立和關聯。

> [!TIP]
> 讓 view 模型和 views 保持獨立。 將視圖系結至資料來源中的屬性，應該是視圖對其對應視圖模型的主體相依性。 具體而言，請不要 [`Button`](xref:Xamarin.Forms.Button) 從 view 模型參考檢視類型，例如和 [`ListView`](xref:Xamarin.Forms.ListView) 。 藉由遵循此處所述的原則，您可以在隔離模式中測試檢視模型，藉由限制範圍來降低軟體瑕疵的可能性。

下列各節將討論連接視圖模型至視圖的主要方法。

### <a name="creating-a-view-model-declaratively"></a>以宣告方式建立視圖模型

最簡單的方法是讓 view 以宣告方式在 XAML 中具現化其對應的視圖模型。 當建立視圖時，也會一併建立對應的視圖模型物件。 下列程式碼範例會示範這種方法：

```xaml
<ContentPage ... xmlns:local="clr-namespace:eShop">  
    <ContentPage.BindingContext>  
        <local:LoginViewModel />  
    </ContentPage.BindingContext>  
    ...  
</ContentPage>
```

當 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 建立時，的實例會 `LoginViewModel` 自動建立並設定為 view [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 。

視圖模型的這個宣告式結構和指派的優點是很簡單，但有一個缺點，就是在視圖模型中，它需要預設 (無參數) 的函式。

### <a name="creating-a-view-model-programmatically"></a>以程式設計方式建立視圖模型

一個視圖可以在程式碼後置於檔案中，產生將視圖模型指派給其屬性的程式碼 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 。 這通常會在 view 的函式中完成，如下列程式碼範例所示：

```csharp
public LoginView()  
{  
    InitializeComponent();  
    BindingContext = new LoginViewModel(navigationService);  
}
```

在視圖的程式碼後端中，以程式設計方式建立和指派視圖模型的優點是很簡單。 不過，這種方法的主要缺點是，view 需要提供具有任何必要相依性的視圖模型。 使用相依性插入容器有助於維持視圖與視圖模型之間的鬆散結合。 如需詳細資訊，請參閱相依性 [插入](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md)。

### <a name="creating-a-view-defined-as-a-data-template"></a>建立定義為資料範本的視圖

View 可以定義為資料範本，並與視圖模型類型建立關聯。 資料範本可以定義為資源，也可以在將顯示視圖模型的控制項內以內嵌方式定義。 控制項的內容是視圖模型實例，並使用資料範本以視覺化方式表示。 這項技術是一種情況範例，其中會先具現化視圖模型，然後建立視圖。

### <a name="automatically-creating-a-view-model-with-a-view-model-locator"></a>使用 View Model 定位器自動建立視圖模型

視圖模型定位器是自訂類別，可管理檢視模型的具現化，以及其與視圖的關聯。 在 eShopOnContainers 行動裝置應用程式中， `ViewModelLocator` 類別具有附加屬性， `AutoWireViewModel` 可用來將視圖模型與視圖產生關聯。 在視圖的 XAML 中，此附加屬性會設定為 true，表示視圖模型應該自動連接到視圖，如下列程式碼範例所示：

```xaml
viewModelBase:ViewModelLocator.AutoWireViewModel="true"
```

`AutoWireViewModel`屬性是初始化為 false 的可系結屬性，當其值變更時， `OnAutoWireViewModelChanged` 就會呼叫事件處理常式。 這個方法會解析視圖的視圖模型。 下列程式碼範例顯示如何達成此目的：

```csharp
private static void OnAutoWireViewModelChanged(BindableObject bindable, object oldValue, object newValue)  
{  
    var view = bindable as Element;  
    if (view == null)  
    {  
        return;  
    }  

    var viewType = view.GetType();  
    var viewName = viewType.FullName.Replace(".Views.", ".ViewModels.");  
    var viewAssemblyName = viewType.GetTypeInfo().Assembly.FullName;  
    var viewModelName = string.Format(  
        CultureInfo.InvariantCulture, "{0}Model, {1}", viewName, viewAssemblyName);  

    var viewModelType = Type.GetType(viewModelName);  
    if (viewModelType == null)  
    {  
        return;  
    }  
    var viewModel = _container.Resolve(viewModelType);  
    view.BindingContext = viewModel;  
}
```

`OnAutoWireViewModelChanged`方法會嘗試使用以慣例為基礎的方法來解析視圖模型。 此慣例假設：

- 視圖模型與檢視類型位於相同的元件中。
- 視圖位於中。Views 子命名空間。
- 視圖模型在中。Viewmodel 子命名空間。
- 視圖模型名稱會對應到視圖名稱，並以 "ViewModel" 結尾。

最後， `OnAutoWireViewModelChanged` 方法會將 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 檢視類型的設定為已解析的視圖模型類型。 如需有關解決視圖模型類型的詳細資訊，請參閱 [解決方法](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#resolution)。

這種方法的優點是應用程式具有單一類別，負責將視圖模型具現化，以及其對視圖的連接。

> [!TIP]
> 使用 view model 定位器方便替代。 視圖模型定位器也可以當做替代相依性（例如單元測試或設計階段資料）的替代點使用。

## <a name="updating-views-in-response-to-changes-in-the-underlying-view-model-or-model"></a>更新視圖以回應基礎視圖模型或模型中的變更

視圖可存取的所有視圖模型和模型類別都應該執行 `INotifyPropertyChanged` 介面。 在視圖模型或模型類別中執行這個介面，可讓類別在基礎屬性值變更時，提供變更通知給 view 中的任何資料繫結控制項。

應用程式必須符合下列需求，才能針對屬性變更通知的正確用法進行架構：

- `PropertyChanged`如果公用屬性的值變更，一律引發事件。 請勿假設引發 `PropertyChanged` 事件是因為瞭解 XAML 系結的發生方式所造成。
- `PropertyChanged`針對任何計算屬性（其值由視圖模型或模型中的其他屬性使用），一律會引發事件。
- 一律在 `PropertyChanged` 進行屬性變更的方法結尾，或當物件已知處於安全狀態時，引發事件。 引發事件會以同步方式叫用事件的處理常式，中斷作業。 如果發生這種情況，它可能會在處於不安全的部分更新狀態時，將物件公開給回呼函式。 此外，也有可能由事件觸發串聯變更 `PropertyChanged` 。 串聯變更通常需要在串聯變更安全執行之前完成更新。
- 如果屬性不會變更，則永遠不會引發 `PropertyChanged` 事件。 這表示在引發事件之前，您必須先比較舊的和新的值 `PropertyChanged` 。
- 如果您要初始化屬性，則永遠不會 `PropertyChanged` 在視圖模型的函式期間引發事件。 在此情況下，view 中的資料繫結控制項將不會訂閱以接收變更通知。
- 永遠不會 `PropertyChanged` 在類別的公用方法的單一同步調用中，使用相同的屬性名稱引數來引發一個以上的事件。 例如，假設有一個 `NumberOfItems` 屬性的備份存放區是 `_numberOfItems` 欄位，如果方法在 `_numberOfItems` 迴圈執行期間遞增50次，則在所有工作完成之後，它應該只會在 `NumberOfItems` 屬性一次引發屬性變更通知。 若是非同步方法，請 `PropertyChanged` 在非同步接續鏈的每個同步區段中，針對指定的屬性名稱引發事件。

EShopOnContainers 行動應用程式會使用 `ExtendedBindableObject` 類別來提供變更通知，如下列程式碼範例所示：

```csharp
public abstract class ExtendedBindableObject : BindableObject  
{  
    public void RaisePropertyChanged<T>(Expression<Func<T>> property)  
    {  
        var name = GetMemberInfo(property).Name;  
        OnPropertyChanged(name);  
    }  

    private MemberInfo GetMemberInfo(Expression expression)  
    {  
        ...  
    }  
}
```

Xamarin. 表單的類別會實 [`BindableObject`](xref:Xamarin.Forms.BindableObject) 作為 `INotifyPropertyChanged` 介面，並提供 [`OnPropertyChanged`](xref:Xamarin.Forms.BindableObject.OnPropertyChanged(System.String)) 方法。 `ExtendedBindableObject`類別會提供 `RaisePropertyChanged` 方法來叫用屬性變更通知，在此情況下，會使用類別所提供的功能 `BindableObject` 。

EShopOnContainers 行動裝置應用程式中的每個 view 模型類別都是衍生自類別 `ViewModelBase` ，而後者又衍生自 `ExtendedBindableObject` 類別。 因此，每個 view 模型類別都會使用 `RaisePropertyChanged` 類別中的方法 `ExtendedBindableObject` 來提供屬性變更通知。 下列程式碼範例示範 eShopOnContainers mobile 應用程式如何使用 lambda 運算式來叫用屬性變更通知：

```csharp
public bool IsLogin  
{  
    get  
    {  
        return _isLogin;  
    }  
    set  
    {  
        _isLogin = value;  
        RaisePropertyChanged(() => IsLogin);  
    }  
}
```

請注意，以這種方式使用 lambda 運算式需要較小的效能成本，因為每次呼叫都必須評估 lambda 運算式。 雖然效能成本很小，而且通常不會影響應用程式，但成本可能會在有許多變更通知時產生。 不過，這種方法的優點是，它會在重新命名屬性時提供編譯時間型別安全和重構支援。

## <a name="ui-interaction-using-commands-and-behaviors"></a>使用命令和行為的 UI 互動

在行動應用程式中，通常會叫用動作以回應使用者動作，例如按一下按鈕，您可以在程式碼後端檔案中建立事件處理常式來執行這些動作。 不過，在 MVVM 模式中，執行此動作的責任在於視圖模型，而且應該避免將程式碼放在程式碼後置中。

命令提供便利的方式來表示可系結至 UI 中控制項的動作。 它們會封裝可執行動作的程式碼，並協助將它與視圖中的視覺表示保持分離。 Xamarin.Forms 包含可以宣告方式連接至命令的控制項，而這些控制項將會在使用者與控制項互動時叫用命令。

行為也可讓控制項以宣告方式連接至命令。 不過，行為可以用來叫用與控制項所引發之事件範圍相關聯的動作。 因此，行為可解決許多與命令啟用控制項相同的案例，同時提供更大的彈性和控制能力。 此外，行為也可以用來將命令物件或方法與未特別設計來與命令互動的控制項建立關聯。

### <a name="implementing-commands"></a>執行命令

視圖模型通常會公開命令屬性，以便從 view 系結，也就是執行介面的物件實例 `ICommand` 。 有許多 Xamarin.Forms 控制項都提供 `Command` 屬性，該屬性可以是系結至 `ICommand` 視圖模型所提供物件的資料。 `ICommand`介面會定義 `Execute` 方法，此方法會封裝作業本身、 `CanExecute` 方法（指出是否可叫用命令），以及發生 `CanExecuteChanged` 變更時所發生的事件是否應執行此命令。 [`Command`](xref:Xamarin.Forms.Command)提供的和 [`Command<T>`](xref:Xamarin.Forms.Command) 類別 Xamarin.Forms 會執行 `ICommand` 介面，其中 `T` 是和的引數類型 `Execute` `CanExecute` 。

在視圖模型內，型別為 [`Command`](xref:Xamarin.Forms.Command) [`Command<T>`](xref:Xamarin.Forms.Command) 視圖模型中的每個公用屬性都應該有類型的物件 `ICommand` 。 `Command`或函 `Command<T>` 式需要 `Action` 叫用方法時呼叫的回呼物件 `ICommand.Execute` 。 `CanExecute`方法是選擇性的函式參數，而是會傳回的 `Func` `bool` 。

下列程式碼示範如何藉 [`Command`](xref:Xamarin.Forms.Command) 由指定視圖模型方法的委派，來建立代表 register 命令的實例 `Register` ：

```csharp
public ICommand RegisterCommand => new Command(Register);
```

此命令會透過傳回參考的屬性，公開給視圖 `ICommand` 。 在 `Execute` 物件上呼叫方法時 [`Command`](xref:Xamarin.Forms.Command) ，它只會透過在函式中指定的委派，將呼叫轉送到視圖模型中的方法 `Command` 。

`async` `await` 指定命令的委派時，可以使用和關鍵字來叫用非同步方法 `Execute` 。 這表示回呼是 `Task` ，應該等候。 例如，下列程式碼示範如何藉 [`Command`](xref:Xamarin.Forms.Command) 由指定視圖模型方法的委派，來建立代表登入命令的實例 `SignInAsync` ：

```csharp
public ICommand SignInCommand => new Command(async () => await SignInAsync());
```

您可以使用類別將命令具現化，將參數傳遞至 `Execute` 和 `CanExecute` 動作 [`Command<T>`](xref:Xamarin.Forms.Command) 。 例如，下列程式碼會示範如何 `Command<T>` 使用實例來指出 `NavigateAsync` 方法需要型別為的引數 `string` ：

```csharp
public ICommand NavigateCommand => new Command<string>(NavigateAsync);
```

在 [`Command`](xref:Xamarin.Forms.Command) 和類別中 [`Command<T>`](xref:Xamarin.Forms.Command) ， `CanExecute` 每個函式中方法的委派都是選擇性的。 如果未指定委派， `Command` 將會傳回 `true` `CanExecute` 。 不過，視圖模型可以在 `CanExecute` 物件上呼叫方法，以指出命令的狀態變更 `ChangeCanExecute` `Command` 。 這會 `CanExecuteChanged` 引發事件。 UI 中任何系結至命令的控制項將會更新其已啟用的狀態，以反映資料系結命令的可用性。

#### <a name="invoking-commands-from-a-view"></a>從 View 叫用命令

下列程式碼範例示範中的如何 [`Grid`](xref:Xamarin.Forms.Grid) `LoginView` 使用實例系結至 `RegisterCommand` 類別中的 `LoginViewModel` [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) ：

```xaml
<Grid Grid.Column="1" HorizontalOptions="Center">  
    <Label Text="REGISTER" TextColor="Gray"/>  
    <Grid.GestureRecognizers>  
        <TapGestureRecognizer Command="{Binding RegisterCommand}" NumberOfTapsRequired="1" />  
    </Grid.GestureRecognizers>  
</Grid>
```

您也可以使用屬性選擇性地定義命令參數 [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) 。 預期引數的類型是在 `Execute` 和 `CanExecute` 目標方法中指定。 [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)當使用者與附加的控制項互動時，會自動叫用目標命令。 如果有提供命令參數，將會以引數的形式傳遞至命令的 `Execute` 委派。

### <a name="implementing-behaviors"></a>執行行為

行為可讓您將功能新增至 UI 控制項，而不需要將它們設為子類別。 相反地，功能會在行為類別中實作並附加至控制項，如同控制項本身的一部分。 行為可讓您執行通常必須撰寫為程式碼後端的程式碼，因為它會直接與控制項的 API 互動，這樣一來，就可以將它精確地附加至控制項，並封裝成可在多個視圖或應用程式重複使用。 在 MVVM 的內容中，行為是將控制項連接到命令的實用方法。

透過附加屬性附加至控制項的行為稱為 *附加行為* 。 然後，此行為可以使用它所附加之專案的公開 API，在視圖的視覺化樹狀結構中加入該控制項或其他控制項的功能。 EShopOnContainers 行動應用程式包含 `LineColorBehavior` 類別，也就是附加的行為。 如需此行為的詳細資訊，請參閱 [顯示驗證錯誤](~/xamarin-forms/enterprise-application-patterns/validation.md#displaying-validation-errors)。

Xamarin.Forms行為是衍生自 [`Behavior`](xref:Xamarin.Forms.Behavior) 或類別的類別 [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) ，其中 `T` 是應該套用行為的控制項類型。 這些類別提供 `OnAttachedTo` 和 `OnDetachingFrom` 方法，應該覆寫這些方法，以提供當行為附加至控制項並將其從控制項卸離時，所要執行的邏輯。

在 eShopOnContainers 行動裝置應用程式中， `BindableBehavior<T>` 類別衍生自 [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) 類別。 類別的目的 `BindableBehavior<T>` 是要針對 Xamarin.Forms 需要將 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 行為設定為附加控制項的行為提供基類。

`BindableBehavior<T>`類別提供可覆寫的方法， `OnAttachedTo` 可設定行為的，以及可覆寫的可覆 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 寫 `OnDetachingFrom` 方法 `BindingContext` 。 此外，該類別會在 `AssociatedObject` 屬性中儲存對附加控制項的參考。

EShopOnContainers 行動應用程式包含 `EventToCommandBehavior` 類別，可執行命令以回應發生的事件。 這個類別衍生自 `BindableBehavior<T>` 類別，如此一來， `ICommand` 當取用行為時，行為可以系結至並執行屬性所指定的 `Command` 。 下列程式碼範例顯示 `EventToCommandBehavior` 類別：

```csharp
public class EventToCommandBehavior : BindableBehavior<View>  
{  
    ...  
    protected override void OnAttachedTo(View visualElement)  
    {  
        base.OnAttachedTo(visualElement);  

        var events = AssociatedObject.GetType().GetRuntimeEvents().ToArray();  
        if (events.Any())  
        {  
            _eventInfo = events.FirstOrDefault(e => e.Name == EventName);  
            if (_eventInfo == null)  
                throw new ArgumentException(string.Format(  
                        "EventToCommand: Can't find any event named '{0}' on attached type",   
                        EventName));  

            AddEventHandler(_eventInfo, AssociatedObject, OnFired);  
        }  
    }  

    protected override void OnDetachingFrom(View view)  
    {  
        if (_handler != null)  
            _eventInfo.RemoveEventHandler(AssociatedObject, _handler);  

        base.OnDetachingFrom(view);  
    }  

    private void AddEventHandler(  
            EventInfo eventInfo, object item, Action<object, EventArgs> action)  
    {  
        ...  
    }  

    private void OnFired(object sender, EventArgs eventArgs)  
    {  
        ...  
    }  
}
```

`OnAttachedTo`和 `OnDetachingFrom` 方法可用來為屬性中定義的事件註冊和取消註冊事件處理常式 `EventName` 。 然後，當事件引發時， `OnFired` 就會叫用方法，以執行命令。

使用在 `EventToCommandBehavior` 事件引發時執行命令的優點是，命令可以與未設計成與命令互動的控制項相關聯。 此外，這會將事件處理常式代碼移至可進行單元測試的模型。

#### <a name="invoking-behaviors-from-a-view"></a>從視圖調用行為

`EventToCommandBehavior`特別適用于將命令附加至不支援命令的控制項。 例如，在 `ProfileView` `EventToCommandBehavior` `OrderDetailCommand` [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 列出使用者訂單的上引發事件時，會使用來執行， [`ListView`](xref:Xamarin.Forms.ListView) 如下列程式碼所示：

```xaml
<ListView>  
    <ListView.Behaviors>  
        <behaviors:EventToCommandBehavior             
            EventName="ItemTapped"  
            Command="{Binding OrderDetailCommand}"  
            EventArgsConverter="{StaticResource ItemTappedEventArgsConverter}" />  
    </ListView.Behaviors>  
    ...  
</ListView>
```

在執行時間， `EventToCommandBehavior` 會回應與的互動 [`ListView`](xref:Xamarin.Forms.ListView) 。 在中選取專案時， `ListView` [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 就會引發事件，這會 `OrderDetailCommand` 在中執行 `ProfileViewModel` 。 根據預設，事件的事件引數會傳遞至命令。 這項資料會在由屬性中指定的轉換器于來源與目標之間傳遞時進行轉換，而這會從傳回的 `EventArgsConverter` [`Item`](xref:Xamarin.Forms.ItemTappedEventArgs.Item) `ListView` [`ItemTappedEventArgs`](xref:Xamarin.Forms.ItemTappedEventArgs) 。 因此，當 `OrderDetailCommand` 執行時，會將選取的 `Order` 作為參數傳遞給註冊的動作。

如需行為的詳細資訊，請參閱 [行為](~/xamarin-forms/app-fundamentals/behaviors/index.md)。

## <a name="summary"></a>總結

ViewModel (MVVM) 模式的模型視圖可協助您將應用程式的商務和展示邏輯與使用者介面 (UI) 完全分開。 在應用程式邏輯和 UI 之間保持乾淨的分隔有助於解決許多開發問題，並可讓應用程式更容易進行測試、維護和演進。 它也可以大幅改善程式碼重複使用的機會，並讓開發人員和 UI 設計工具在開發應用程式的個別元件時，更輕鬆地共同作業。

使用 MVVM 模式時，應用程式的 UI 和基礎的展示和商務邏輯會分成三個不同的類別：此視圖會封裝 UI 和 UI 邏輯;會封裝展示邏輯和狀態的視圖模型;以及用來封裝應用程式商務邏輯和資料的模型。

## <a name="related-links"></a>相關連結

- [下載電子書 (2Mb PDF) ](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub)  (範例) ](https://github.com/dotnet-architecture/eShopOnContainers)
