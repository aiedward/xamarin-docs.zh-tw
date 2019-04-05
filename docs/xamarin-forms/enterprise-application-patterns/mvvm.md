---
title: Model View ViewModel 模式
description: 本章說明如何 eShopOnContainers 的行動裝置應用程式會使用 MVVM 模式以明確區分其使用者介面的應用程式的商務和呈現邏輯。
ms.prod: xamarin
ms.assetid: dd8c1813-df44-4947-bcee-1a1ff2334b87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 87448c556c66ea086db70699848227e1f671792b
ms.sourcegitcommit: be51b459a0a148ae3adca31d7599f53f7b2c3a68
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2019
ms.locfileid: "59019382"
---
# <a name="the-model-view-viewmodel-pattern"></a>Model View ViewModel 模式

在 XAML 中，建立使用者介面，然後再新增 會在使用者介面運作的程式碼後，通常牽涉到 Xamarin.Forms 開發人員體驗。 當應用程式修改，並成長的大小和範圍時，可能會發生複雜的維護工作的問題。 這些問題包括 UI 控制項和商務邏輯，這樣會讓 UI 修改，以及單元測試這類程式碼的困難度的成本增加的緊密連結。

Model View ViewModel (MVVM) 模式可協助以明確區分從其使用者介面 (UI) 的應用程式的商務和呈現邏輯。 維護應用程式邏輯和 UI 清楚劃分幫助您解決許多開發問題，並可讓您更輕鬆地測試、 維護及改進的應用程式。 它可以也可大幅提升程式碼重複使用機會，並可讓開發人員和開發的應用程式及其個別組件時，UI 設計工具，能夠更輕易地共同作業。

## <a name="the-mvvm-pattern"></a>MVVM 模式

MVVM 模式有三個核心元件： 模型、 檢視和檢視模型。 每個有不同的用途時。 圖 2-1 會顯示三個元件之間的關聯性。

![](mvvm-images/mvvm.png "MVVM 模式")

**圖 2-1**:MVVM 模式

除了了解每個元件的責任，也很重要，若要了解它們如何彼此互動。 概括而言，檢視 「 知道 」 檢視模型中，檢視模型 「 知道 」 模型，但模型不會知道檢視模型，並檢視模型未察覺的檢視。 因此，檢視模型將隔離模型，從檢視，而且可讓模型發展與檢視分開。

使用 MVVM 模式的優點如下所示：

-   如果沒有現有的模型實作封裝現有的商務邏輯，很難或有風險，若要變更它。 在此案例中，檢視模型做為模型類別的配接器，並可讓您避免模型程式碼進行任何重大的變更。
-   開發人員可以建立檢視模型與模型中，單元的測試，而不使用檢視。 檢視模型的單元測試可以執行完全相同的功能所用的檢視。
-   可以不必變更程式碼中，重新設計應用程式 UI，前提是完全以 XAML 實作的檢視。 因此，新檢視的版本應該使用現有的檢視模型。
-   設計人員和開發人員可以使用獨立和同時其元件在開發程序。 設計工具可以專注於檢視中，而開發人員可以處理的檢視模型和模型元件。

要有效地使用 MVVM 的索引鍵是了解如何建構正確的類別，將應用程式程式碼並了解類別互動的方式。 下列各節討論的每個類別中的 MVVM 模式的責任。

### <a name="view"></a>檢視

負責定義結構、 配置和外觀的使用者會看到畫面上檢視。 在理想情況下，每個檢視定義在 XAML，以限制程式碼後置不包含商務邏輯。 不過，在某些情況下，程式碼後置可能會包含 UI 邏輯實作很難表達 XAML，例如動畫的視覺行為。

在 Xamarin.Forms 應用程式中，檢視是通常[ `Page` ](xref:Xamarin.Forms.Page)-衍生或[ `ContentView` ](xref:Xamarin.Forms.ContentView)-衍生的類別。 不過，檢視也可以由一個資料範本，其中指定用來以視覺方式表示物件，它顯示時的 UI 項目表示。 資料範本做為檢視並沒有任何程式碼後置，而且設計來繫結至特定檢視的模型型別。

> [!TIP]
> 避免啟用和停用 UI 項目，在程式碼後置中。 請確定檢視模型負責定義邏輯的狀態變更會影響檢視的顯示，例如命令是否可用，或指出作業已暫止的某些層面。 因此，啟用和停用 UI 項目繫結至檢視模型屬性，而不是啟用和停用它們的程式碼後置。

有數個選項，以回應互動的檢視，檢視模型上執行程式碼，例如按鈕點選或選擇項目。 如果控制項支援的命令，該控制項的`Command`屬性可以是資料繫結至`ICommand`上檢視模型的屬性。 叫用控制項的命令時，就會執行檢視模型中的程式碼。 除了命令之外，行為可以附加至檢視中的物件，而且要叫用的命令或引發的事件，可接聽。 在回應時，便可以叫用此行為`ICommand`上檢視模型或檢視模型上的方法。

### <a name="viewmodel"></a>ViewModel

檢視模型實作屬性和命令的檢視可資料繫結，並通知透過變更通知事件的任何狀態變更的檢視。 內容和檢視模型提供的命令定義的功能，以提供 ui，但檢視可讓您決定要顯示這項功能有何。

> [!TIP]
> 保留 UI 回應使用非同步作業。 行動裝置應用程式應該讓 UI 執行緒解除封鎖以改善使用者的認知效能。 因此，在檢視模型中，使用 I/O 作業的非同步方法，並引發事件，以非同步方式通知屬性變更的檢視。

檢視模型也會負責協調的檢視所需的任何模型類別互動的。 通常是檢視模型與模型類別之間的一對多關聯性。 檢視模型可能會選擇要公開 （expose） 直接至檢視模型類別，以便在檢視中的控制項可以直接的資料繫結。 在此情況下，模型類別必須設計為支援資料繫結，並將變更通知事件。

每個檢視模型提供的表單，可以輕鬆地使用檢視模型中的資料。 若要這麼做，檢視模型有時會執行資料轉換。 這項資料轉換置於檢視模型是個不錯的主意，因為它會提供檢視可以繫結至的屬性。 例如，檢視模型可能會結合以方便顯示由檢視的兩個屬性的值。

> [!TIP]
> 集中在轉換層中的資料轉換。 它也可作為個別的資料轉換層位於檢視模型和檢視之間使用轉換器。 比方說，當資料需要特殊格式，不會提供檢視模型，這可能是必要的。

為了讓參與雙向資料繫結與檢視的檢視模型，其屬性必須引發`PropertyChanged`事件。 檢視模型滿足此需求，藉由實作`INotifyPropertyChanged`介面，並引發`PropertyChanged`屬性變更時的事件。

集合，請檢視友善`ObservableCollection<T>`提供。 此集合會實作變更通知，減輕開發人員不必實作`INotifyCollectionChanged`集合上的介面。

### <a name="model"></a>型號

模型類別是在非 visual 類別來封裝應用程式的資料。 因此，模型可以視為代表應用程式的網域模型，其中通常包含資料模型，以及商務和驗證邏輯。 模型物件的範例包括資料傳輸物件 (Dto)、 純舊 CLR 物件 (Poco)，以及產生的實體和 proxy 物件。

模型類別通常用於搭配服務或封裝資料存取和快取的儲存機制。

## <a name="connecting-view-models-to-views"></a>連接至檢視的檢視模型

可以使用 Xamarin.Forms 的資料繫結功能，將檢視模型連線至檢視。 有許多可用來建構檢視和檢視模型，並在執行階段產生關聯的方法。 這些方法分為兩種類別，稱為檢視第一個組合和檢視模型中的第一個組合。 檢視第一個組合和檢視模型的第一個組合的喜好設定與複雜性問題之間進行選擇。 不過，所有的方法會共用相同的目的是要有指派給其 BindingContext 屬性的檢視模型的檢視。

檢視第一個組合應用程式是在概念上被組成連接到所相依的檢視模型的檢視。 這種方法的主要優點是，它可以輕鬆建構鬆散耦合、 單元測試應用程式因為檢視模型都不有自己的檢視表上的任何相依性。 也很容易了解應用程式的結構，藉由遵循其視覺結構，而不需要追蹤程式碼執行，以了解如何建立及相關聯的類別。 此外，檢視第一個建構對齊 Xamarin.Forms 導覽系統負責建構頁面瀏覽時，複雜且不當與平台，讓檢視模型的第一個組合。

檢視模型的第一個組合應用程式是在概念上組成檢視模型與服務，負責尋找檢視模型的檢視。 檢視模型中的第一個組合感覺一些開發人員，更自然的因為檢視表的建立，可抽象化，進而將焦點放在應用程式邏輯的非 UI 結構。 此外，它可讓其他檢視模型所建立的檢視模型。 不過，這種方法通常很複雜，而且它可能會變得難以了解如何建立及相關聯的應用程式的各個部分。

> [!TIP]
> 請檢視模型和檢視的獨立。 繫結到資料來源中屬性的檢視應在其對應的檢視模型檢視的主體的相依性。 具體而言，不參考檢視的型別，例如[ `Button` ](xref:Xamarin.Forms.Button)和[ `ListView` ](xref:Xamarin.Forms.ListView)，從檢視模型。 依照此處所述的原則，可以在隔離中，因此減少軟體缺失的可能性，藉由限制範圍測試檢視模型。

下列各節會討論連接至檢視的檢視模型的主要方法。

### <a name="creating-a-view-model-declaratively"></a>以宣告方式建立檢視模型

簡單的方法是以宣告方式具現化在 XAML 中其對應的檢視模型的檢視。 當建構檢視時，也會建構對應的檢視模型物件。 這種方法是以下列程式碼範例所示：

```xaml
<ContentPage ... xmlns:local="clr-namespace:eShop">  
    <ContentPage.BindingContext>  
        <local:LoginViewModel />  
    </ContentPage.BindingContext>  
    ...  
</ContentPage>
```

當[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)建立的執行個體`LoginViewModel`會自動建構並設為檢視[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)。

這個宣告式的建構和指派所檢視的檢視模型的優點是它很簡單，但它需要的檢視模型中的預設 （無參數） 建構函式的缺點則是。

### <a name="creating-a-view-model-programmatically"></a>以程式設計方式建立檢視模型

檢視可以在指派給的檢視模型中產生的程式碼後置檔案的程式碼及其[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)屬性。 通常這是在檢視表的建構函式，如下列程式碼範例所示：

```csharp
public LoginView()  
{  
    InitializeComponent();  
    BindingContext = new LoginViewModel(navigationService);  
}
```

以程式設計方式建構和指派的檢視程式碼後置中檢視模型的好處很簡單。 不過，這種方法的主要缺點是需要提供任何必要的相依性的檢視模型檢視。 使用相依性插入容器可協助您維護鬆散耦合的檢視和檢視模型之間。 如需詳細資訊，請參閱 <<c0> [ 相依性插入](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md)。

### <a name="creating-a-view-defined-as-a-data-template"></a>建立檢視表定義為資料範本

檢視可以定義為資料範本，並檢視模型類型相關聯。 資料範本可以定義為資源，或者可以是會顯示檢視模型在控制項內的內嵌定義。 控制項的內容是檢視模型執行個體，並用來以視覺方式表示該資料範本。 這項技術是在其中檢視模型具現化第一次，接著檢視的建立情況的範例。

<a name="automatically_creating_a_view_model_with_a_view_model_locator" />

### <a name="automatically-creating-a-view-model-with-a-view-model-locator"></a>自動建立檢視模型的檢視模型定位器

檢視模型定位器是管理的具現化的檢視模型和檢視其關聯的自訂類別。 在 eShopOnContainers 的行動應用程式中`ViewModelLocator`類別具有附加的屬性， `AutoWireViewModel`，用來將檢視模型與檢視結合。 在檢視的 XAML，此附加的屬性是設定為 true，指出檢視模型應該會自動連接至檢視，如下列程式碼範例所示：

```xaml
viewModelBase:ViewModelLocator.AutoWireViewModel="true"
```

`AutoWireViewModel`屬性是可繫結的屬性，會初始化為 false，和其值變更時`OnAutoWireViewModelChanged`會呼叫事件處理常式。 這個方法會解析檢視的檢視模型。 下列程式碼範例顯示如何即可達成此目的：

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

`OnAutoWireViewModelChanged`方法會嘗試解決使用以慣例為基礎的方法，在檢視模型。 這個慣例假設：

-   檢視模型是在相同的組件的檢視類型。
-   檢視表位於。檢視子命名空間。
-   檢視模型位於。Viewmodel 子命名空間。
-   檢視模型名稱與檢視名稱的對應，並以 「 ViewModel"結尾。

最後，`OnAutoWireViewModelChanged`方法會設定[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)解析的檢視的模型型別檢視型別。 如需有關如何解決檢視模型類型的詳細資訊，請參閱[解析度](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#resolution)。

這種方法的優點是，應用程式必須負責具現化的檢視模型和其連線至檢視的單一類別。

> [!TIP]
> 使用檢視模型定位器，以方便的替代。 檢視模型定位器也可用的替代實作相依性，替代成這類的單元測試或設計階段資料。

## <a name="updating-views-in-response-to-changes-in-the-underlying-view-model-or-model"></a>更新的檢視，以回應變更，在基礎檢視模型

所有的檢視模型和存取檢視的模型類別應實作`INotifyPropertyChanged`介面。 檢視模型或模型類別中實作這個介面可讓基礎的屬性值變更時，提供任何資料繫結的控制項檢視中的變更通知的類別。

應該符合下列需求，將應用程式建構的屬性變更通知時的正確用法：

-   一律引發`PropertyChanged`公用屬性的值變更時的事件。 請勿假設該引發`PropertyChanged`可以忽略事件，因為知識的 XAML 繫結的進行方式。
-   一律引發`PropertyChanged`任何事件計算的屬性的值用在檢視中的其他屬性的模型。
-   一律引發`PropertyChanged`事件的方法，可變更的屬性，或當已知為安全的狀態中的物件的結尾。 引發事件時，中斷作業以同步方式叫用事件處理常式。 如果發生這種情況進行的作業，它可能會公開回呼函式物件，當它是在不安全，部分更新的狀態。 此外，就可能所觸發的串聯式變更`PropertyChanged`事件。 串聯的變更通常需要安全地執行串聯的變更才會完成的更新。
-   永遠不會引發`PropertyChanged`如果屬性不會變更的事件。 這表示您必須再引發比較新舊值`PropertyChanged`事件。
-   永遠不會引發`PropertyChanged`期間如果您要初始化屬性的檢視模型的建構函式的事件。 此時收到變更通知未都會訂閱檢視中的資料繫結控制項。
-   永遠不會引發多個`PropertyChanged`事件與單一同步叫用類別的公用方法內的相同屬性名稱引數。 例如，假設`NumberOfItems`屬性是支援的存放區`_numberOfItems`欄位中，如果方法會遞增`_numberOfItems`五十個時間在執行迴圈時，它應該只引發屬性變更通知上`NumberOfItems`屬性一次，完成所有工作之後。 如需非同步方法，引發`PropertyChanged`非同步接續鏈結的每個同步區段中指定的屬性名稱的事件。

EShopOnContainers 的行動裝置應用程式會使用`ExtendedBindableObject`類別，以提供變更通知，這下列程式碼範例所示：

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

Xamarin.Form [ `BindableObject` ](xref:Xamarin.Forms.BindableObject)類別會實作`INotifyPropertyChanged`介面，並提供[ `OnPropertyChanged` ](xref:Xamarin.Forms.BindableObject.OnPropertyChanged(System.String))方法。 `ExtendedBindableObject`類別會提供`RaisePropertyChanged`方法來叫用屬性的變更通知，並在此情況下會使用所提供的功能`BindableObject`類別。

在 eShopOnContainers 的行動裝置應用程式中每個檢視模型類別衍生自`ViewModelBase`類別，又衍生自`ExtendedBindableObject`類別。 因此，每個檢視模型類別會使用`RaisePropertyChanged`方法中的`ExtendedBindableObject`類別，以提供屬性變更通知。 下列程式碼範例示範如何在 eShopOnContainers 的行動應用程式叫用屬性變更通知使用 lambda 運算式：

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

請注意，使用 lambda 運算式以這種方式都會涉及很少的效能成本，因為 lambda 運算式必須評估每個呼叫。 雖然效能成本很小，而且通常不會影響應用程式，有許多變更通知時，可以會產生成本。 不過，這種方法的優點是，它會提供編譯時期型別安全和重新命名屬性時的重構支援。

## <a name="ui-interaction-using-commands-and-behaviors"></a>使用命令和行為的 UI 互動

行動裝置應用程式，是通常叫用動作以回應使用者動作，例如按一下按鈕，在可透過程式碼後置檔案中建立事件處理常式實作。 不過，在 MVVM 模式中，實作動作的責任就是檢視模型，而且應該避免在程式碼後置中的放置程式碼。

命令提供便利的方式來代表可繫結至控制項的 UI 中的動作。 這些封裝實作動作的程式碼，並協助保持分開它們在檢視中的視覺表示。 Xamarin.Forms 包含可以命令，以宣告方式連接的控制項，這些控制項將會叫用命令，當使用者與控制項互動。

行為也可讓控制項以宣告方式連線到命令。 不過，行為可用來叫用一組控制項所引發的事件相關聯的動作。 因此，行為時提供更高的彈性和控制有解決許多相同的案例，以啟用命令的控制項。 此外，行為也可用來關聯已不專為與命令互動的控制項中的命令物件或方法。

### <a name="implementing-commands"></a>實作命令

檢視模型通常會公開命令屬性，從檢視中，繫結所實作的物件執行個體`ICommand`介面。 數字，Xamarin.Forms 控制項提供`Command`屬性，它可以是資料繫結至`ICommand`檢視模型所提供的物件。 `ICommand`介面會定義`Execute`方法中，封裝本身的作業，其中`CanExecute`方法，表示是否可以叫用命令，與`CanExecuteChanged`變更是否發生影響時，就會發生的事件此命令應該執行。 [ `Command` ](xref:Xamarin.Forms.Command)並[ `Command<T>` ](xref:Xamarin.Forms.Command) Xamarin.Forms，所提供的類別實作`ICommand`介面，其中`T`是的引數的型別`Execute`和`CanExecute`。

在檢視模型中，應該是類型的物件[ `Command` ](xref:Xamarin.Forms.Command)或是[ `Command<T>` ](xref:Xamarin.Forms.Command)類型的檢視模型中的每個公用屬性`ICommand`。 `Command`或是`Command<T>`建構函式需要`Action`具有時呼叫的回呼物件`ICommand.Execute`叫用方法。 `CanExecute`方法是選擇性的建構函式參數，且`Func`傳回`bool`。

下列程式碼示範如何[ `Command` ](xref:Xamarin.Forms.Command)執行個體，代表註冊命令，這藉由指定的委派建構`Register`檢視模型的方法：

```csharp
public ICommand RegisterCommand => new Command(Register);
```

此命令會公開至檢視的屬性來傳回的參考`ICommand`。 當`Execute`上呼叫方法[ `Command` ](xref:Xamarin.Forms.Command)物件，它只會轉送至透過委派中所指定的檢視模型中的方法呼叫`Command`建構函式。

非同步方法可以使用叫用命令`async`並`await`關鍵字指定的命令時`Execute`委派。 這表示回呼是`Task`且應該等候。 例如，下列程式碼示範如何[ `Command` ](xref:Xamarin.Forms.Command)執行個體，代表登入命令，這藉由指定的委派建構`SignInAsync`檢視模型的方法：

```csharp
public ICommand SignInCommand => new Command(async () => await SignInAsync());
```

可以將參數傳遞給`Execute`並`CanExecute`使用的動作[ `Command<T>` ](xref:Xamarin.Forms.Command)來具現化命令的類別。 例如，下列程式碼示範如何`Command<T>`執行個體用來指出`NavigateAsync`方法將會需要類型的引數`string`:

```csharp
public ICommand NavigateCommand => new Command<string>(NavigateAsync);
```

在這兩[ `Command` ](xref:Xamarin.Forms.Command)並[ `Command<T>` ](xref:Xamarin.Forms.Command)類別，來委派`CanExecute`每個建構函式中的方法是選擇性的。 如果未指定委派`Command`會傳回`true`如`CanExecute`。 不過，檢視模型可以指出在命令中的變更`CanExecute`狀態，藉由呼叫`ChangeCanExecute`方法`Command`物件。 這會導致`CanExecuteChanged`會引發事件。 任何在 UI 中的控制項繫結至命令會更新以反映可用性的資料繫結命令及其已啟用的狀態。

#### <a name="invoking-commands-from-a-view"></a>叫用命令，從檢視表

下列程式碼範例示範如何[ `Grid` ](xref:Xamarin.Forms.Grid)中`LoginView`繫結至`RegisterCommand`中`LoginViewModel`藉由類別[ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer)執行個體：

```xaml
<Grid Grid.Column="1" HorizontalOptions="Center">  
    <Label Text="REGISTER" TextColor="Gray"/>  
    <Grid.GestureRecognizers>  
        <TapGestureRecognizer Command="{Binding RegisterCommand}" NumberOfTapsRequired="1" />  
    </Grid.GestureRecognizers>  
</Grid>
```

命令參數也可以選擇性地定義使用[ `CommandParameter` ](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter)屬性。 中指定必要的引數的型別`Execute`和`CanExecute`目標方法。 [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer)就會在使用者與附加的控制項互動時自動呼叫目標命令。 命令參數，如果提供，將做為引數傳遞至命令的`Execute`委派。

<a name="implementing_behaviors" />

### <a name="implementing-behaviors"></a>實作行為

行為允許功能新增至 UI 控制項，而不具有子類別。 相反地，功能會在行為類別中實作並附加至控制項，如同控制項本身的一部分。 行為可讓您實作程式碼，您通常必須撰寫程式碼後置，因為它直接互動的方式，您可以精確附加至控制項，並跨多個檢視或應用程式封裝以供重複使用控制項的 API。 在內容中的 MVVM，行為會是個實用的方法，如控制項連接到命令。

附加至控制項，以透過附加屬性的行為就所謂*附加行為*。 行為接著可以使用公開的 API，它所附加到將功能新增至該控制項或其他控制項，檢視的視覺化樹狀結構中的項目。 EShopOnContainers 的行動應用程式包含`LineColorBehavior`類別，這是附加的行為。 如需有關此行為的詳細資訊，請參閱 <<c0> [ 顯示驗證錯誤](~/xamarin-forms/enterprise-application-patterns/validation.md#displaying_validation_errors)。

Xamarin.Forms 行為是類別，衍生自[ `Behavior` ](xref:Xamarin.Forms.Behavior)或是[ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1)類別，其中`T `行為應該套用至控制項的類型。 這些類別提供`OnAttachedTo`和`OnDetachingFrom`提供時的行為是附加至和中斷連結控制項將會執行的邏輯應該覆寫的方法。

在 eShopOnContainers 的行動應用程式中`BindableBehavior<T>`類別衍生自[ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1)類別。 目的`BindableBehavior<T>`類別是為需要的 Xamarin.Forms 行為提供基底類別[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)設為附加的控制項的行為。

`BindableBehavior<T>`類別會提供可覆寫`OnAttachedTo`方法，以設定[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)的行為，以及可覆寫`OnDetachingFrom`方法會清除`BindingContext`。 此外，該類別會在 `AssociatedObject` 屬性中儲存對附加控制項的參考。

EShopOnContainers 的行動裝置應用程式包含`EventToCommandBehavior`類別，以回應事件在發生執行命令。 這個類別衍生自`BindableBehavior<T>`類別，使行為可以繫結至並執行`ICommand`所指定`Command`屬性時的行為會取用。 下列程式碼範例顯示 `EventToCommandBehavior` 類別：

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

`OnAttachedTo`並`OnDetachingFrom`方法來註冊及取消註冊事件處理常式中定義事件`EventName`屬性。 然後，當事件引發時，`OnFired`叫用方法時，它就會執行命令。

使用的優點`EventToCommandBehavior`執行命令，當事件引發時，是 命令可以是未設計成與命令互動的控制項相關聯。 此外，這會將移事件處理程式碼來檢視模型，它可以在其中進行單元測試。

#### <a name="invoking-behaviors-from-a-view"></a>叫用從檢視表的行為

`EventToCommandBehavior`特別適用於附加至的控制項，不支援命令的命令。 例如，`ProfileView`會使用`EventToCommandBehavior`來執行`OrderDetailCommand`時[ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped)上就會引發事件[ `ListView` ](xref:Xamarin.Forms.ListView)所示，其中會列出使用者的訂單下列程式碼：

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

在執行階段`EventToCommandBehavior`會回應互動[ `ListView` ](xref:Xamarin.Forms.ListView)。 當選取的項目`ListView`，則[ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped)就會引發事件，會同時執行`OrderDetailCommand`在`ProfileViewModel`。 根據預設，事件的事件引數會傳遞至命令。 這項資料會轉換來源和目標之間傳遞中指定轉換器所`EventArgsConverter`屬性，會傳回[ `Item` ](xref:Xamarin.Forms.ItemTappedEventArgs.Item)的`ListView`從[ `ItemTappedEventArgs`](xref:Xamarin.Forms.ItemTappedEventArgs). 因此，當`OrderDetailCommand`執行時，所選`Order`做為參數傳遞至註冊的動作。

如需行為的詳細資訊，請參閱[行為](~/xamarin-forms/app-fundamentals/behaviors/index.md)。

## <a name="summary"></a>總結

Model View ViewModel (MVVM) 模式可協助以明確區分從其使用者介面 (UI) 的應用程式的商務和呈現邏輯。 維護應用程式邏輯和 UI 清楚劃分幫助您解決許多開發問題，並可讓您更輕鬆地測試、 維護及改進的應用程式。 它可以也可大幅提升程式碼重複使用機會，並可讓開發人員和開發的應用程式及其個別組件時，UI 設計工具，能夠更輕易地共同作業。

使用 MVVM 模式的應用程式 UI 和基礎的簡報及商務邏輯區分為三個不同的類別： 檢視中，封裝的 UI 和 UI 邏輯;檢視模型中，封裝呈現邏輯和狀態;與模型中，封裝應用程式的商務邏輯和資料。


## <a name="related-links"></a>相關連結

- [下載電子書 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （範例）](https://github.com/dotnet-architecture/eShopOnContainers)
