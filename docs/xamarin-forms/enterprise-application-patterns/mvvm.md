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
ms.openlocfilehash: c07fc9f42c4175b4e4569ab95a951b24c130c4f3
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84572048"
---
# <a name="the-model-view-viewmodel-pattern"></a>模型視圖-ViewModel 模式

Xamarin.Forms開發人員體驗通常牽涉到在 XAML 中建立使用者介面，然後加入在使用者介面上運作的程式碼後置。 隨著應用程式的修改，以及大小和範圍的成長，可能會發生複雜的維護問題。 這些問題包括 UI 控制項與商務邏輯之間的緊密結合，這會增加進行 UI 修改的成本，以及對這類程式碼進行單元測試的困難。

模型 ViewModel （MVVM）模式有助於將應用程式的商務和展示邏輯與其使用者介面（UI）完全分開。 維護應用程式邏輯與 UI 之間的清楚分隔，有助於解決許多開發問題，並可讓應用程式更容易測試、維護和發展。 它也可以大幅改善程式碼重複使用的機會，並可讓開發人員和 UI 設計工具在開發應用程式的個別部分時，更輕鬆地共同作業。

## <a name="the-mvvm-pattern"></a>MVVM 模式

MVVM 模式中有三個核心元件：模型、視圖和視圖模型。 每個服務都有不同的用途。 圖2-1 顯示這三個元件之間的關聯性。

![](mvvm-images/mvvm.png "The MVVM pattern")

**圖 2-1**： MVVM 模式

除了瞭解每個元件的責任之外，也請務必瞭解它們彼此互動的方式。 概括而言，視圖「知道」視圖模型，而視圖模型「知道」模型，但模型不知道視圖模型，而且視圖模型不知道此視圖。 因此，視圖模型會從模型中隔離此視圖，並允許模型獨立于視圖之外進行進化。

使用 MVVM 模式的優點如下：

- 如果現有的模型執行封裝了現有的商務邏輯，變更它可能會很艱難或有風險。 在此案例中，視圖模型會做為模型類別的介面卡，並可讓您避免對模型程式碼進行任何重大變更。
- 開發人員可以建立視圖模型和模型的單元測試，而不需要使用 view。 視圖模型的單元測試可以執行與視圖所使用的完全相同功能。
- 應用程式 UI 可以重新設計，而不需要觸及程式碼，前提是該視圖完全以 XAML 執行。 因此，新的視圖版本應該與現有的視圖模型搭配使用。
- 設計師和開發人員可以在開發程式期間，獨立且同時在其元件上工作。 設計師可以專注于 view，而開發人員可以處理視圖模型和模型元件。

使用 MVVM 的關鍵在於瞭解如何將應用程式程式碼納入正確的類別，以及瞭解類別的互動方式。 下列各節將討論 MVVM 模式中每個類別的責任。

### <a name="view"></a>檢視

此視圖會負責定義使用者在螢幕上看到的結構、版面配置和外觀。 在理想的情況下，每個視圖都會以 XAML 定義，而程式碼後置則不會包含商務邏輯。 不過，在某些情況下，程式碼後置可能會包含 UI 邏輯，此介面會執行不容易在 XAML 中表達的視覺行為，例如動畫。

在 Xamarin.Forms 應用程式中，view 通常是 [`Page`](xref:Xamarin.Forms.Page) 衍生或衍生的 [`ContentView`](xref:Xamarin.Forms.ContentView) 類別。 不過，您也可以使用資料範本來表示 views，這會指定要在顯示物件時用來以視覺方式表示的 UI 元素。 資料範本做為視圖並不會有任何程式碼後置，而是設計來系結至特定的視圖模型類型。

> [!TIP]
> 避免在程式碼後置中啟用和停用 UI 元素。 請確定視圖模型負責定義會影響視圖顯示某些層面的邏輯狀態變更，例如是否有可用的命令，或指示作業是否已暫止。 因此，藉由系結來啟用和停用 UI 元素，而不是在程式碼後置中啟用和停用它們。

有數個選項可在視圖模型上執行程式碼，以回應視圖上的互動，例如按一下按鈕或專案選取。 如果控制項支援命令，控制項的 `Command` 屬性可以資料系結至 `ICommand` 視圖模型上的屬性。 叫用控制項的命令時，將會執行視圖模型中的程式碼。 除了命令之外，行為也可以附加至視圖中的物件，並可接聽要叫用的命令或引發的事件。 在回應中，行為可以接著在視圖模型 `ICommand` 上叫用，或在 view 模型上叫用方法。

### <a name="viewmodel"></a>ViewModel

視圖模型會執行 view 可以將資料系結至其中的屬性和命令，並透過變更通知事件來通知所有狀態變更的觀點。 視圖模型所提供的屬性和命令會定義 UI 提供的功能，但此視圖會決定要如何顯示該功能。

> [!TIP]
> 使用非同步作業讓 UI 保持回應。 行動應用程式應讓 UI 執行緒保持解除封鎖，以改善使用者對效能的認知。 因此，在視圖模型中，使用非同步方法進行 i/o 作業，並引發事件以非同步方式通知屬性變更的觀點。

視圖模型也會負責協調視圖與所需之任何模型類別的互動。 在視圖模型和模型類別之間，通常會有一對多關聯性。 視圖模型可能會選擇將模型類別直接公開至視圖，讓視圖中的控制項可以直接將資料系結至它們。 在此情況下，模型類別必須設計成支援資料系結和變更通知事件。

每個 view 模型都會從一個表單中的模型提供資料，讓您可以輕鬆地取用。 為了達成此目的，視圖模型有時候會執行資料轉換。 將此資料轉換放在視圖模型中是個不錯的主意，因為它會提供可系結的屬性。 例如，視圖模型可能會結合兩個屬性的值，讓視圖更容易顯示。

> [!TIP]
> 集中化轉換層中的資料轉換。 您也可以使用轉換器做為位於視圖模型和視圖之間的個別資料轉換層。 例如，當資料需要視圖模型不提供的特殊格式時，這可能是必要的。

為了讓視圖模型參與與 view 的雙向資料系結，其屬性必須引發 `PropertyChanged` 事件。 視圖模型會藉由執行介面來滿足這項需求 `INotifyPropertyChanged` ，並 `PropertyChanged` 在屬性變更時引發事件。

針對集合，會提供易記的視圖 `ObservableCollection<T>` 。 這個集合會執行集合已變更的通知，讓開發人員不必 `INotifyCollectionChanged` 在集合上實作為介面。

### <a name="model"></a>模型

模型類別是封裝應用程式資料的非視覺類別。 因此，您可以將模型視為代表應用程式的領域模型，這通常會包含資料模型以及商務和驗證邏輯。 模型物件的範例包括資料傳輸物件（Dto）、簡單的 CLR 物件（Poco），以及產生的實體和 proxy 物件。

模型類別通常會與封裝資料存取和快取的服務或存放庫搭配使用。

## <a name="connecting-view-models-to-views"></a>將視圖模型連接到 Views

您可以使用的資料系結功能，將視圖模型連接到 views Xamarin.Forms 。 有許多方法可以用來在執行時間建立視圖及查看模型，並將它們相關聯。 這些方法分為兩種類別，稱為「視圖」第一次撰寫，以及「視圖模型」第一次組合。 選擇 [第一次撰寫視圖] 和 [觀看模型] [第一次撰寫] 是喜好設定和複雜度的問題。 不過，所有方法都共用相同的目標，這是為了讓視圖具有指派給其 BindingCoNtext 屬性的視圖模型。

透過「視圖」第一次撰寫，應用程式在概念上是由連接到其所依賴之視圖模型的視圖所組成。 這種方法的主要優點是，它可讓您輕鬆地建立鬆散結合、可測試單元的應用程式，因為視圖模型不會相依于 views 本身。 您也可以遵循應用程式的視覺化結構來瞭解其結構，而不必追蹤程式碼的執行，以瞭解如何建立和關聯類別。 此外，視圖第一個結構 Xamarin.Forms 會與負責在導覽發生時建立頁面的流覽系統一致，這使得視圖模型在第一次撰寫複雜且與平臺不對齊。

透過「視圖模型」第一次撰寫應用程式，在概念上是由視圖模型組成，而服務負責尋找視圖模型的視圖。 視圖模型的第一個組合對某些開發人員而言會更加自然，因為可以將建立視圖抽象化，讓它們專注于應用程式的邏輯非 UI 結構。 此外，它還允許其他視圖模型建立視圖模型。 不過，這種方法通常很複雜，而且很難瞭解如何建立和關聯應用程式的各個部分。

> [!TIP]
> 讓視圖模型和視圖彼此獨立。 將視圖系結至資料來源中的屬性，應為其對應視圖模型的視圖主體相依性。 具體而言，請不要 [`Button`](xref:Xamarin.Forms.Button) 從視圖模型參考檢視類型，例如和 [`ListView`](xref:Xamarin.Forms.ListView) 。 藉由遵循這裡所述的原則，可隔離地測試檢視模型，因此藉由限制範圍來降低軟體缺失的可能性。

下列各節將討論將視圖模型連接到 views 的主要方法。

### <a name="creating-a-view-model-declaratively"></a>以宣告方式建立視圖模型

最簡單的方法是讓 view 在 XAML 中以宣告方式具現化其對應的視圖模型。 當此視圖經過結構化時，也會一併建立對應的視圖模型物件。 這個方法會在下列程式碼範例中示範：

```xaml
<ContentPage ... xmlns:local="clr-namespace:eShop">  
    <ContentPage.BindingContext>  
        <local:LoginViewModel />  
    </ContentPage.BindingContext>  
    ...  
</ContentPage>
```

當 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 建立時，會自動建立的實例， `LoginViewModel` 並將其設定為視圖的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 。

View 模型的這個宣告式結構和指派具有簡單的優點，但是它的缺點是在視圖模型中需要預設（無參數）的函式。

### <a name="creating-a-view-model-programmatically"></a>以程式設計方式建立視圖模型

一個視圖可以在程式碼後置檔案中擁有程式碼，以將 view 模型指派給其 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 屬性。 這通常會在視圖的函式中完成，如下列程式碼範例所示：

```csharp
public LoginView()  
{  
    InitializeComponent();  
    BindingContext = new LoginViewModel(navigationService);  
}
```

在視圖程式碼後置中，以程式設計方式建立視圖模型並加以指派，有其優點。 不過，此方法的主要缺點是，此視圖需要提供具有任何必要相依性的視圖模型。 使用相依性插入容器有助於維護視圖和視圖模型之間的鬆散結合。 如需詳細資訊，請參閱相依性[插入](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md)。

### <a name="creating-a-view-defined-as-a-data-template"></a>建立定義為資料範本的視圖

View 可以定義為資料範本，並與視圖模型類型建立關聯。 資料範本可以定義為資源，也可以在將顯示視圖模型的控制項內以內嵌方式定義。 控制項的內容是 view model 實例，而資料範本則用來以視覺方式表示。 這項技術是一種情況的範例，其中會先具現化視圖模型，然後再建立視圖。

### <a name="automatically-creating-a-view-model-with-a-view-model-locator"></a>使用視圖模型定位器自動建立視圖模型

視圖模型定位器是一個自訂類別，可管理檢視模型的具現化，以及其與 views 的關聯。 在 eShopOnContainers 行動裝置應用程式中， `ViewModelLocator` 類別具有附加的屬性， `AutoWireViewModel` 用來將視圖模型與視圖產生關聯。 在此視圖的 XAML 中，此附加屬性會設定為 true，表示視圖模型應該自動連接至視圖，如下列程式碼範例所示：

```xaml
viewModelBase:ViewModelLocator.AutoWireViewModel="true"
```

`AutoWireViewModel`屬性是可系結的屬性，它會初始化為 false，而當其值變更時， `OnAutoWireViewModelChanged` 會呼叫事件處理常式。 這個方法會解析視圖的視圖模型。 下列程式碼範例顯示如何達成此目的：

```csharp
private static void OnAutoWireViewModelChanged(BindableObject bindable, object oldValue, object newValue)  
{  
    var view = bindable as Element;  
    if (view == null)  
    {  
        return;  
    }  

    var viewType = view.GetType();  
    var viewName = viewType.FullName.Replace(".Views.", ".ViewModels.");  
    var viewAssemblyName = viewType.GetTypeInfo().Assembly.FullName;  
    var viewModelName = string.Format(  
        CultureInfo.InvariantCulture, "{0}Model, {1}", viewName, viewAssemblyName);  

    var viewModelType = Type.GetType(viewModelName);  
    if (viewModelType == null)  
    {  
        return;  
    }  
    var viewModel = _container.Resolve(viewModelType);  
    view.BindingContext = viewModel;  
}
```

`OnAutoWireViewModelChanged`方法會嘗試使用以慣例為基礎的方法來解析視圖模型。 此慣例假設：

- 視圖模型與檢視類型位於相同的元件中。
- Views 位於。Views 子命名空間。
- 視圖模型位於中。Viewmodel 子命名空間。
- 視圖模型名稱會對應到視圖名稱，並以 "ViewModel" 結尾。

最後， `OnAutoWireViewModelChanged` 方法會將 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 檢視類型的設定為已解析的視圖模型類型。 如需有關解析視圖模型類型的詳細資訊，請參閱[解決](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#resolution)方式。

此方法的優點是應用程式具有單一類別，負責具現化視圖模型和其與視圖的連接。

> [!TIP]
> 使用視圖模型定位器以方便替換。 「視圖模型定位器」也可用來做為替代相依性（例如單元測試或設計階段資料）的替換點。

## <a name="updating-views-in-response-to-changes-in-the-underlying-view-model-or-model"></a>更新視圖以回應基礎視圖模型或模型中的變更

所有可供視圖存取的視圖模型和模型類別都應該執行 `INotifyPropertyChanged` 介面。 在視圖模型或模型類別中執行此介面，可讓類別在基礎屬性值變更時，將變更通知提供給視圖中的任何資料繫結控制項。

應用程式應透過符合下列需求，架構以正確使用屬性變更通知：

- `PropertyChanged`當公用屬性的值變更時，一律引發事件。 請不要假設引發 `PropertyChanged` 事件可能因為瞭解 XAML 系結的發生方式而被忽略。
- 一律 `PropertyChanged` 針對任何計算的屬性引發事件，而這些屬性的值是由視圖模型或模型中的其他屬性所使用。
- 一律 `PropertyChanged` 在進行屬性變更的方法結尾，或當物件已知為安全狀態時，引發事件。 引發事件會以同步方式叫用事件的處理常式來中斷作業。 如果發生這種情況，當作業處於不安全、部分更新的狀態時，它可能會將物件公開給回呼函式。 此外，也有可能會由事件觸發串聯變更 `PropertyChanged` 。 串聯式變更通常需要先完成更新，才能執行串聯式變更。
- 若屬性未變更，絕對不會引發 `PropertyChanged` 事件。 這表示在引發事件之前，您必須先比較新舊值 `PropertyChanged` 。
- `PropertyChanged`如果您要初始化屬性，請勿在視圖模型的函式期間引發事件。 在此情況下，視圖中的資料繫結控制項將不會訂閱以接收變更通知。
- 絕對不要在類別的 `PropertyChanged` 公用方法的單一同步調用中，以相同的屬性名稱引數來引發一個以上的事件。 例如，假設有一個 `NumberOfItems` 屬性的備份存放區是 `_numberOfItems` 欄位，如果方法在 `_numberOfItems` 迴圈執行期間增加50倍，則在 `NumberOfItems` 完成所有工作之後，應該只會在屬性上引發屬性變更通知。 針對非同步方法，針對 `PropertyChanged` 非同步接續鏈的每個同步區段中的指定屬性名稱，引發事件。

EShopOnContainers mobile 應用程式會使用 `ExtendedBindableObject` 類別來提供變更通知，如下列程式碼範例所示：

```csharp
public abstract class ExtendedBindableObject : BindableObject  
{  
    public void RaisePropertyChanged<T>(Expression<Func<T>> property)  
    {  
        var name = GetMemberInfo(property).Name;  
        OnPropertyChanged(name);  
    }  

    private MemberInfo GetMemberInfo(Expression expression)  
    {  
        ...  
    }  
}
```

Xamarin 的類別會實 [`BindableObject`](xref:Xamarin.Forms.BindableObject) 作為 `INotifyPropertyChanged` 介面，並提供 [`OnPropertyChanged`](xref:Xamarin.Forms.BindableObject.OnPropertyChanged(System.String)) 方法。 `ExtendedBindableObject`類別提供 `RaisePropertyChanged` 方法來叫用屬性變更通知，而在這麼做時，會使用類別所提供的功能 `BindableObject` 。

EShopOnContainers 行動應用程式中的每個 view 模型類別衍生自 `ViewModelBase` 類別，而後者又衍生自 `ExtendedBindableObject` 類別。 因此，每個 view 模型類別都會使用 `RaisePropertyChanged` 類別中的方法 `ExtendedBindableObject` 來提供屬性變更通知。 下列程式碼範例示範 eShopOnContainers mobile 應用程式如何使用 lambda 運算式來叫用屬性變更通知：

```csharp
public bool IsLogin  
{  
    get  
    {  
        return _isLogin;  
    }  
    set  
    {  
        _isLogin = value;  
        RaisePropertyChanged(() => IsLogin);  
    }  
}
```

請注意，以這種方式使用 lambda 運算式牽涉到較小的效能成本，因為必須針對每個呼叫評估 lambda 運算式。 雖然效能成本很小，而且通常不會影響應用程式，但在有許多變更通知時，成本可能會累積。 不過，這種方法的優點在於，它會在重新命名屬性時提供編譯時間型別安全和重整支援。

## <a name="ui-interaction-using-commands-and-behaviors"></a>使用命令和行為的 UI 互動

在行動裝置應用程式中，通常會叫用動作以回應使用者動作（例如按一下按鈕），這可以藉由在程式碼後置檔案中建立事件處理常式來執行。 不過，在 MVVM 模式中，實作用此動作的責任是使用 view 模型，而且應該避免在程式碼後置中放置程式碼。

命令提供便利的方式來表示可以系結至 UI 中控制項的動作。 它們會封裝用來執行動作的程式碼，並協助將它與視圖中的視覺表示分離。 Xamarin.Forms包含可以宣告方式連接至命令的控制項，而這些控制項將會在使用者與控制項互動時叫用此命令。

行為也允許以宣告方式將控制項連接到命令。 不過，行為可以用來叫用與控制項所引發的事件範圍相關聯的動作。 因此，行為會解決許多與命令啟用控制項相同的案例，同時提供更大的彈性和控制程度。 此外，行為也可以用來將命令物件或方法與不是專門設計來與命令互動的控制項建立關聯。

### <a name="implementing-commands"></a>執行命令

視圖模型通常會公開命令屬性，以便從視圖進行系結，也就是實作為介面的物件實例 `ICommand` 。 有數個 Xamarin.Forms 控制項提供 `Command` 屬性，可以是系結至 `ICommand` 視圖模型所提供之物件的資料。 `ICommand`介面 `Execute` 會定義方法，其會封裝作業本身，此方法會 `CanExecute` 指出是否可以叫用命令，以及 `CanExecuteChanged` 發生影響是否應執行命令的變更時所發生的事件。 [`Command`](xref:Xamarin.Forms.Command)提供的和 [`Command<T>`](xref:Xamarin.Forms.Command) 類別 Xamarin.Forms 會執行 `ICommand` 介面，其中 `T` 是和之引數的類型 `Execute` `CanExecute` 。

在視圖模型中， [`Command`](xref:Xamarin.Forms.Command) [`Command<T>`](xref:Xamarin.Forms.Command) 類型的視圖模型中的每個公用屬性都應該要有類型為或的物件 `ICommand` 。 `Command`或函 `Command<T>` 式需要 `Action` 叫用方法時所呼叫的回呼物件 `ICommand.Execute` 。 `CanExecute`方法是選擇性的函式參數，而是會傳回的 `Func` `bool` 。

下列程式碼示範如何透過 [`Command`](xref:Xamarin.Forms.Command) 指定 view model 方法的委派，來建立代表 register 命令的實例 `Register` ：

```csharp
public ICommand RegisterCommand => new Command(Register);
```

命令會透過傳回之參考的屬性，向視圖公開 `ICommand` 。 在 `Execute` 物件上呼叫方法時 [`Command`](xref:Xamarin.Forms.Command) ，它只會透過在此函式中指定的委派，將呼叫轉送到視圖模型中的方法 `Command` 。

`async` `await` 指定命令的委派時，命令可以使用和關鍵字來叫用非同步方法 `Execute` 。 這表示回呼是 `Task` ，而且應該等候。 例如，下列程式碼會示範如何透過 [`Command`](xref:Xamarin.Forms.Command) 指定 view model 方法的委派，來建立代表登入命令的實例 `SignInAsync` ：

```csharp
public ICommand SignInCommand => new Command(async () => await SignInAsync());
```

參數可以傳遞至 `Execute` 和動作， `CanExecute` 方法是使用 [`Command<T>`](xref:Xamarin.Forms.Command) 類別來具現化命令。 例如，下列程式碼會示範如何 `Command<T>` 使用實例來表示此 `NavigateAsync` 方法需要類型為的引數 `string` ：

```csharp
public ICommand NavigateCommand => new Command<string>(NavigateAsync);
```

在 [`Command`](xref:Xamarin.Forms.Command) 和類別中 [`Command<T>`](xref:Xamarin.Forms.Command) ， `CanExecute` 每個函式中方法的委派都是選擇性的。 如果未指定委派， `Command` 會 `true` 針對傳回 `CanExecute` 。 不過，視圖模型可以藉 `CanExecute` 由呼叫物件上的方法，指出命令狀態的變更 `ChangeCanExecute` `Command` 。 這會導致 `CanExecuteChanged` 引發事件。 任何系結至命令的 UI 控制項都會更新其啟用狀態，以反映資料系結命令的可用性。

#### <a name="invoking-commands-from-a-view"></a>從視圖叫用命令

下列程式碼範例示範如何 [`Grid`](xref:Xamarin.Forms.Grid) 使用實例，將中的系結 `LoginView` 至 `RegisterCommand` 類別中的 `LoginViewModel` [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) ：

```xaml
<Grid Grid.Column="1" HorizontalOptions="Center">  
    <Label Text="REGISTER" TextColor="Gray"/>  
    <Grid.GestureRecognizers>  
        <TapGestureRecognizer Command="{Binding RegisterCommand}" NumberOfTapsRequired="1" />  
    </Grid.GestureRecognizers>  
</Grid>
```

您也可以使用屬性，選擇性地定義命令參數 [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) 。 預期的引數類型是在 `Execute` 和 `CanExecute` 目標方法中指定。 [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)當使用者與附加的控制項互動時，將會自動叫用目標命令。 命令參數（如果有提供的話）會當做引數傳遞給命令的 `Execute` 委派。

### <a name="implementing-behaviors"></a>執行行為

行為可讓您將功能新增至 UI 控制項，而不需要將其設為子類別。 相反地，功能會在行為類別中實作並附加至控制項，如同控制項本身的一部分。 行為可讓您實作為程式碼後置來執行程式碼，因為它會直接與控制項的 API 互動，使其可以精確附加至控制項，並封裝在多個視圖或應用程式之間重複使用。 在 MVVM 的內容中，行為是將控制項連接至命令的實用方法。

透過附加屬性附加至控制項的行為，稱為*附加的行為*。 然後，行為可以使用其附加之專案的公開 API，在視圖的視覺化樹狀結構中加入該控制項的功能，或其他控制項。 EShopOnContainers 行動應用程式包含 `LineColorBehavior` 類別，這是附加的行為。 如需此行為的詳細資訊，請參閱[顯示驗證錯誤](~/xamarin-forms/enterprise-application-patterns/validation.md#displaying-validation-errors)。

「 Xamarin.Forms 行為」（behavior）是衍生自 [`Behavior`](xref:Xamarin.Forms.Behavior) 或 [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) 類別的類別，其中 `T` 是應該套用行為的控制項類型。 這些類別 `OnAttachedTo` 會提供和 `OnDetachingFrom` 方法，這些都應該加以覆寫，以提供當行為附加至控制項並中斷連結時，將會執行的邏輯。

在 eShopOnContainers 行動裝置應用程式中， `BindableBehavior<T>` 類別衍生自 [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) 類別。 類別的目的 `BindableBehavior<T>` 是要提供行為的基類，要求的 Xamarin.Forms [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 行為必須設定為附加的控制項。

類別提供可覆寫的方法，其 `BindableBehavior<T>` `OnAttachedTo` 會設定 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 行為的，以及 `OnDetachingFrom` 會清除的可覆寫方法 `BindingContext` 。 此外，該類別會在 `AssociatedObject` 屬性中儲存對附加控制項的參考。

EShopOnContainers 行動應用程式包含 `EventToCommandBehavior` 類別，它會執行命令以回應發生的事件。 這個類別衍生自 `BindableBehavior<T>` 類別，讓行為可以在使用行為時，系結 `ICommand` 至屬性所指定並執行 `Command` 。 下列程式碼範例顯示 `EventToCommandBehavior` 類別：

```csharp
public class EventToCommandBehavior : BindableBehavior<View>  
{  
    ...  
    protected override void OnAttachedTo(View visualElement)  
    {  
        base.OnAttachedTo(visualElement);  

        var events = AssociatedObject.GetType().GetRuntimeEvents().ToArray();  
        if (events.Any())  
        {  
            _eventInfo = events.FirstOrDefault(e => e.Name == EventName);  
            if (_eventInfo == null)  
                throw new ArgumentException(string.Format(  
                        "EventToCommand: Can't find any event named '{0}' on attached type",   
                        EventName));  

            AddEventHandler(_eventInfo, AssociatedObject, OnFired);  
        }  
    }  

    protected override void OnDetachingFrom(View view)  
    {  
        if (_handler != null)  
            _eventInfo.RemoveEventHandler(AssociatedObject, _handler);  

        base.OnDetachingFrom(view);  
    }  

    private void AddEventHandler(  
            EventInfo eventInfo, object item, Action<object, EventArgs> action)  
    {  
        ...  
    }  

    private void OnFired(object sender, EventArgs eventArgs)  
    {  
        ...  
    }  
}
```

`OnAttachedTo`和 `OnDetachingFrom` 方法是用來註冊和取消註冊屬性中所定義事件的事件處理常式 `EventName` 。 然後，當事件引發時， `OnFired` 會叫用方法，以執行命令。

`EventToCommandBehavior`當事件引發時，使用來執行命令的優點是，命令可以與未設計成與命令互動的控制項建立關聯。 此外，這會移動事件處理常式代碼來查看模型，其中可以進行單元測試。

#### <a name="invoking-behaviors-from-a-view"></a>從視圖叫用行為

`EventToCommandBehavior`特別適合用來將命令附加至不支援命令的控制項。 例如， `ProfileView` `EventToCommandBehavior` `OrderDetailCommand` 當 [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 事件在列出使用者訂單的上引發時，會使用來執行， [`ListView`](xref:Xamarin.Forms.ListView) 如下列程式碼所示：

```xaml
<ListView>  
    <ListView.Behaviors>  
        <behaviors:EventToCommandBehavior             
            EventName="ItemTapped"  
            Command="{Binding OrderDetailCommand}"  
            EventArgsConverter="{StaticResource ItemTappedEventArgsConverter}" />  
    </ListView.Behaviors>  
    ...  
</ListView>
```

在執行時間， `EventToCommandBehavior` 會回應與的互動 [`ListView`](xref:Xamarin.Forms.ListView) 。 在中選取專案時 `ListView` ， [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 將會引發事件，這會 `OrderDetailCommand` 在中執行 `ProfileViewModel` 。 根據預設，事件的事件引數會傳遞至命令。 這項資料會轉換，因為它是在來源與目標之間傳遞，並由屬性中指定的轉換器 `EventArgsConverter` （從傳回） [`Item`](xref:Xamarin.Forms.ItemTappedEventArgs.Item) `ListView` [`ItemTappedEventArgs`](xref:Xamarin.Forms.ItemTappedEventArgs) 。 因此，當 `OrderDetailCommand` 執行時，選取的 `Order` 會當做參數傳遞給已註冊的動作。

如需行為的詳細資訊，請參閱[行為](~/xamarin-forms/app-fundamentals/behaviors/index.md)。

## <a name="summary"></a>摘要

模型 ViewModel （MVVM）模式有助於將應用程式的商務和展示邏輯與其使用者介面（UI）完全分開。 維護應用程式邏輯與 UI 之間的清楚分隔，有助於解決許多開發問題，並可讓應用程式更容易測試、維護和發展。 它也可以大幅改善程式碼重複使用的機會，並可讓開發人員和 UI 設計工具在開發應用程式的個別部分時，更輕鬆地共同作業。

使用 MVVM 模式，應用程式的 UI 和基礎簡報和商務邏輯會分成三個不同的類別：此視圖會封裝 UI 和 UI 邏輯;會封裝呈現邏輯和狀態的視圖模型;和模型，其會封裝應用程式的商務邏輯和資料。

## <a name="related-links"></a>相關連結

- [下載電子書（2 Mb 的 PDF）](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers （GitHub）（範例）](https://github.com/dotnet-architecture/eShopOnContainers)
