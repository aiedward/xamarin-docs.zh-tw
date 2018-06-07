---
title: MVVM
ms.prod: xamarin
ms.assetid: dd8c1813-df44-4947-bcee-1a1ff2334b87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 03a3c411c7031ece32a8987b480f96ad39909201
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34793946"
---
# <a name="mvvm"></a>MVVM

在 XAML 中，建立使用者介面，然後將加入程式碼後置操作所在的使用者介面，通常牽涉到 Xamarin.Forms 開發人員的體驗。 在應用程式會修改及規模和範圍時，可能發生的複雜的維護工作的問題。 這些問題包括 UI 控制項和商務邏輯，可增加做出 UI 修改，以及單元測試這類程式碼的困難度的成本之間的緊密結合。

模型-檢視-ViewModel (MVVM) 模式，有助於清楚分隔應用程式從其使用者介面 (UI) 的商務和呈現邏輯。 維護應用程式邏輯和 UI 清楚分隔有助於解決許多開發的問題，並可以讓您更輕鬆地測試、 維護及發展的應用程式。 它可以也可大幅改善程式碼重複使用的機會，並可讓開發人員和開發應用程式中其各自的一部分時，UI 設計工具可以更輕易地共同作業。

## <a name="the-mvvm-pattern"></a>MVVM 模式

MVVM 模式中有三個核心元件： 模型、 檢視和檢視模型。 每個有不同的用途。 圖 2-1 會顯示三個元件之間的關聯性。

![](mvvm-images/mvvm.png "MVVM 模式")

**圖 2-1**: MVVM 模式

除了了解每個元件的責任，也很一定要了解它們如何與彼此互動。 概括而言，檢視 「 知道 」 檢視模型，和檢視模型 「 知道 」 模型，但模型不會察覺的檢視模型，而且檢視模型不會察覺的檢視。 因此，檢視模型隔離從模型的檢視，並允許與檢視分開發展的模型。

使用 MVVM 模式的優點如下：

-   如果沒有現有的模型實作封裝現有商務邏輯，可能很困難或將其變更的風險。 在此案例中，檢視模型做為模型類別的配接器，並可讓您避免模型程式碼進行任何重大變更。
-   開發人員可以建立單元測試，檢視模型和模型，而不使用檢視。 檢視模型的單元測試可以運用完全相同的功能所用的檢視。
-   可以不需觸及程式碼，即可重新設計應用程式 UI，前提是檢視實作可在 XAML 中。 因此，新檢視的版本應該使用現有的檢視模型。
-   設計和開發人員可以彼此獨立，而且同時在工作及其元件在開發程序。 設計人員可以專注在檢視上，而開發人員可以處理的檢視模型和模型元件。

要有效地使用 MVVM 的索引鍵位於和了解如何互動的類別中了解如何建構成正確的類別，應用程式程式碼。 下列章節會討論每個 MVVM 模式中的類別的責任。

### <a name="view"></a>檢視

檢視會負責定義結構、 配置和外觀在螢幕上看到的使用者。 理想情況下，每個檢視是在 XAML 中，以定義限制程式碼後置不包含商務邏輯。 不過，在某些情況下，程式碼後置可能包含 UI 邏輯實作 visual 很難 express 在 XAML 中，例如動畫的行為。

Xamarin.Forms 應用程式中檢視通常是[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)-衍生或[ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/)-衍生的類別。 不過，檢視也可以透過資料範本，指定要用來顯示時以視覺方式表示物件的 UI 元素表示。 資料範本做為檢視並沒有任何程式碼後置，而為了將繫結至特定檢視的模型型別。

> [!TIP]
> 避免啟用和停用 UI 項目，程式碼後置中。 請檢視模型必須負責定義邏輯的狀態變更會影響檢視的顯示中，例如命令是否可用，或指示的作業正在擱置中的某些層面。 因此，啟用和停用 UI 項目繫結至檢視模型屬性，而不是啟用和停用這些程式碼後置中。

有幾個選項，例如按一下按鈕，以回應互動的檢視，檢視模型上執行程式碼或項目選取項目。 如果控制項支援的命令，此控制項的`Command`屬性可以是資料繫結至`ICommand`上檢視模型的屬性。 叫用控制項的命令時，就會執行檢視模型中的程式碼。 除了命令行為可以附加到在檢視中的物件和要叫用的命令或引發的事件，可以接聽。 為了回應，然後可以叫用此行為`ICommand`檢視模型或檢視模型上的方法上。

### <a name="viewmodel"></a>ViewModel

檢視模型內容及命令的檢視可資料繫結，會實作，並通知變更通知事件的任何狀態變更的檢視。 內容和檢視模型所提供的命令定義的功能，以提供 ui，但檢視判斷該功能的顯示方式。

> [!TIP]
> UI 仍能繼續回應使用非同步作業。 行動裝置應用程式應該保留來改善效能的使用者的感覺解除封鎖 UI 執行緒。 因此，在檢視模型中，使用 I/O 作業的非同步方法，引發事件，以非同步方式通知屬性變更的檢視。

檢視模型也會負責協調的檢視所需的任何模型類別互動。 通常是檢視模型和模型類別之間的一對多關聯性。 檢視模型可能會選擇公開模型類別，直接至檢視，讓控制項檢視中的可以直接的資料繫結。 在此情況下，模型類別必須設計來支援資料繫結和變更通知事件。

每個檢視模型提供的形式來檢視可以輕鬆地取用模型中的資料。 若要完成此動作，檢視模型有時會執行資料轉換。 這項資料轉換置於檢視模型是個不錯的主意，因為它提供的檢視可繫結至屬性。 例如，檢視模型可能會結合以方便顯示由檢視的兩個屬性的值。

> [!TIP]
> 集中在轉換層中的資料轉換。 它也可作為個別的資料轉換層位於檢視模型和檢視之間的轉換器。 這可以是必要，比方說，在資料所需的特殊格式設定不會提供檢視模型。

為了讓參與雙向資料繫結與檢視中檢視模型，其屬性必須引發`PropertyChanged`事件。 檢視模型符合此需求，藉由實作`INotifyPropertyChanged`介面，並引發`PropertyChanged`事件的屬性變更時。

集合，請為檢視易記`ObservableCollection<T>`提供。 這個集合會實作變更通知，因此開發人員不必實作`INotifyCollectionChanged`集合上的介面。

### <a name="model"></a>型號

模型類別是封裝的應用程式資料的非視覺化類別。 因此，此模型可以視為代表應用程式的領域模型通常包含資料模型，以及商務及驗證邏輯。 模型物件的範例包括資料傳輸物件 (Dto)、 純舊 CLR 物件 (POCOs)，並產生的實體和 proxy 物件。

模型類別通常用於搭配服務或封裝資料存取和快取的儲存機制。

## <a name="connecting-view-models-to-views"></a>連接至檢視的檢視模型

檢視模型可以連接至檢視，使用 Xamarin.Forms 中的資料繫結功能。 有多種方法可以用來建構檢視和檢視模型，並在執行階段產生關聯。 這些方法可分成兩個類別，稱為檢視第一個組合，以及檢視模型中的第一個組合。 檢視第一個組合和模型的第一個組合是喜好設定與複雜度的問題的檢視之間做選擇。 不過，這方法都共用相同的目的是要有一個指派給它的其他屬性的檢視模型的檢視。

檢視與第一個組合應用程式在概念上被組成連接到它們依存的檢視模型的檢視。 這種方法的主要優點是可以輕鬆地建構鬆散偶合，單元測試應用程式因為檢視模型都有自己的檢視表上沒有相依關係。 也很容易地遵循其視覺化結構，而不是以追蹤程式碼執行，以了解如何建立和相關聯的類別需要了解應用程式的結構。 此外，檢視第一個建構對齊 Xamarin.Forms 導覽系統負責建構的網頁瀏覽時，這使得檢視模型的第一個組合，複雜且不當使用平台。

檢視模型的第一個組合應用程式是在概念上組成檢視模型、 與服務要負責尋找檢視模型的檢視。 檢視模型中的第一個組合感覺對某些開發人員而言更為自然，因為檢視建立可以抽象，讓它們能夠專注於應用程式的邏輯非 UI 結構。 此外，它可讓其他檢視模型所要建立的檢視模型。 不過，這種方法通常很複雜，而且會變得很難了解如何建立和相關聯的應用程式的各個部分。

> [!TIP]
> 保留檢視模型和檢視的獨立。 繫結到資料來源中的屬性的檢視應檢視的主體相依於其對應的檢視模式。 具體而言，不參考檢視的型別，例如[ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)和[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)，從檢視模型。 依照此處所述的原則，檢視模型可以地接受獨立測試，藉由限制範圍減少軟體有錯誤的可能性。

下列各節將討論主要的方法，來連接至檢視的檢視模型。

### <a name="creating-a-view-model-declaratively"></a>以宣告方式建立檢視模型

最簡單的方式是以宣告方式具現化在 XAML 中其對應的檢視模型的檢視。 當建構檢視時，會在建構對應的檢視模型物件。 下列程式碼範例將示範這個方法：

```xaml
<ContentPage ... xmlns:local="clr-namespace:eShop">  
    <ContentPage.BindingContext>  
        <local:LoginViewModel />  
    </ContentPage.BindingContext>  
    ...  
</ContentPage>
```

當[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)已建立的執行個體`LoginViewModel`自動建構和設定為檢視的[ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/)。

此宣告式建構和指派由檢視的檢視模型的優點是它很簡單，但它要求預設 （無參數） 建構函式中檢視模型的缺點。

### <a name="creating-a-view-model-programmatically"></a>以程式設計方式建立檢視模型

檢視可檢視模型指派至會產生程式碼後置檔案中有程式碼及其[ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/)屬性。 通常這是在檢視表的建構函式，如下列程式碼範例所示：

```csharp
public LoginView()  
{  
    InitializeComponent();  
    BindingContext = new LoginViewModel(navigationService);  
}
```

以程式設計方式建構和指派的檢視程式碼後置中檢視模型的優點是簡單。 不過，這種方法的主要缺點是需要提供任何必要的相依性的檢視模型檢視。 使用相依性插入容器可協助您維護鬆散耦合的檢視和檢視模型之間。 如需詳細資訊，請參閱[相依性插入](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md)。

### <a name="creating-a-view-defined-as-a-data-template"></a>建立檢視表定義為資料範本

檢視可以定義為資料範本，並檢視模型類型相關聯。 資料範本可定義為資源，或者可以是控制項將會顯示檢視模型內的內嵌定義。 控制項的內容與檢視模型執行個體，並用來以視覺方式表示該資料範本。 這項技術是在其中檢視模型會具現化第一次，後面接著建立檢視的情況下的範例。

<a name="automatically_creating_a_view_model_with_a_view_model_locator" />

### <a name="automatically-creating-a-view-model-with-a-view-model-locator"></a>自動建立的檢視模型定位器檢視模型

檢視模型定位器是管理的具現化的檢視模型和檢視其關聯的自訂類別。 EShopOnContainers 行動應用程式，`ViewModelLocator`類別具有附加的屬性， `AutoWireViewModel`，用來將檢視模型與檢視結合。 在 XAML 檢視中，此連接的屬性設定為 true，以指出檢視模型應該自動連接到該檢視，如下列程式碼範例所示：

```xaml
viewModelBase:ViewModelLocator.AutoWireViewModel="true"
```

`AutoWireViewModel`屬性是可繫結的屬性，初始化為 false，且其值變更時`OnAutoWireViewModelChanged`會呼叫事件處理常式。 這個方法會解析為檢視的檢視模型。 下列程式碼範例顯示如何達成這：

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

`OnAutoWireViewModelChanged`方法會嘗試解析檢視模型使用以慣例為基礎的方法。 這個慣例會假設：

-   檢視模型位於相同的組件的檢視類型。
-   檢視表位於。檢視子命名空間。
-   位於 檢視模型。ViewModels 子命名空間。
-   檢視的模型名稱與檢視名稱的對應，並以 「 ViewModel"結尾。

最後，`OnAutoWireViewModelChanged`方法會設定[ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/)解析的檢視模型類型的檢視類型。 如需檢視的模型型別進行解析的詳細資訊，請參閱[解析](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#resolution)。

這種方法的優點的應用程式具有單一類別負責具現化的檢視模型和檢視表的連線。

> [!TIP]
> 使用方便的替代檢視模型定位器。 檢視模型定位器也可用的替代的替代執行方式的相依性，例如單元測試或設計階段資料。

## <a name="updating-views-in-response-to-changes-in-the-underlying-view-model-or-model"></a>更新的檢視，以回應變更在基礎檢視模型

所有的檢視模型和存取檢視的模型類別必須實作`INotifyPropertyChanged`介面。 檢視模型或模型類別中實作這個介面可讓類別提供基礎的屬性值變更時的任何資料繫結的控制項檢視中的變更通知。

應用程式應該架構為屬性變更告知的正確用法，符合下列需求：

-   一律引發`PropertyChanged`如果公用屬性的值變更時發生的事件。 不會假設該引發`PropertyChanged`可以忽略事件，因為知識的 XAML 繫結進行方式。
-   一律引發`PropertyChanged`任何事件計算其值由檢視中的其他屬性的內容模型。
-   一律引發`PropertyChanged`事件的方法，能讓屬性變更，或當已知處於安全狀態的物件的結尾。 引發事件會以同步方式叫用此事件處理常式中斷作業。 如果發生這種情況進行的作業，它可能會公開至回呼函式物件時它是在不安全、 部分更新的狀態。 此外，可能會由觸發的串聯式變更`PropertyChanged`事件。 串聯的變更通常需要更新的階層式的變更是安全執行之前，先完成。
-   永遠不會引發`PropertyChanged`如果屬性不會變更的事件。 這表示您必須比較舊的和新值引發之前`PropertyChanged`事件。
-   永遠不會引發`PropertyChanged`期間如果您要初始化屬性的檢視模型的建構函式的事件。 此時收到變更通知未都會訂閱檢視中的資料繫結控制項。
-   永遠不會引發多個`PropertyChanged`事件使用相同的屬性名稱引數中的類別的公用方法的單一同步引動過程。 例如，假設`NumberOfItems`屬性支援的存放區是`_numberOfItems`欄位中，如果方法遞增`_numberOfItems`五十個時間在執行迴圈時，它應該只引發屬性變更告知上`NumberOfItems`屬性一次，所有工作完成之後。 非同步方法，引發`PropertyChanged`非同步接續鏈結的每個同步區段中指定的屬性名稱的事件。

EShopOnContainers 行動裝置應用程式會使用`ExtendedBindableObject`類別，以提供變更通知，這下列程式碼範例所示：

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

Xamarin.Form 的[ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/)類別會實作`INotifyPropertyChanged`介面，並提供[ `OnPropertyChanged` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.OnPropertyChanged/p/System.String/)方法。 `ExtendedBindableObject`類別提供`RaisePropertyChanged`方法來叫用屬性的變更通知，並在此情況下會使用所提供的功能`BindableObject`類別。

EShopOnContainers 行動應用程式中的每個檢視模型類別衍生自`ViewModelBase`類別，又衍生自`ExtendedBindableObject`類別。 因此，每個檢視模型類別會使用`RaisePropertyChanged`方法中的`ExtendedBindableObject`類別提供屬性變更告知。 下列程式碼範例顯示如何 eShopOnContainers 行動裝置應用程式叫用屬性變更通知使用 lambda 運算式：

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

請注意在這種方式中使用 lambda 運算式牽涉到小的效能成本，因為 lambda 運算式必須評估為每個呼叫。 雖然很小的效能成本，而且通常不會影響應用程式，有許多變更通知時，可以產生成本。 不過，此方法的優點是，它會提供編譯時間型別安全和重構支援重新命名屬性時。

## <a name="ui-interaction-using-commands-and-behaviors"></a>使用命令和行為的 UI 互動

行動裝置的應用程式的動作通常是以回應使用者動作，例如按一下按鈕，在實作的程式碼後置檔案中建立事件處理常式的叫用。 不過，在 MVVM 模式中，實作動作的責任是出檢視模型，並放在程式碼後置中的程式碼應該避免。

命令提供便利的方式來代表可繫結至 UI 中控制項的動作。 這些封裝實作動作的程式碼，並且有助於提升它從檢視中的視覺表示法。 Xamarin.Forms 包含可以命令，以宣告方式連接的控制項，這些控制項將會叫用命令，當使用者與控制項互動。

行為也會讓控制項以宣告方式連線到命令。 不過，行為可以用來叫用的控制項所引發的事件範圍相關聯的動作。 因此，行為時提供更大的彈性和控制可以提供許多相同的案例，以啟用命令的控制項。 此外，行為也可用來關聯已不特別設計來與命令互動的控制項中的命令物件或方法。

### <a name="implementing-commands"></a>實作命令

檢視模型通常會將公開命令屬性，請從檢視中的繫結所實作的物件執行個體`ICommand`介面。 提供許多 Xamarin.Forms 控制項`Command`屬性，可以是資料繫結至`ICommand`檢視模型所提供的物件。 `ICommand`介面會定義`Execute`方法，會封裝作業本身，`CanExecute`方法，指出是否可以叫用命令，並`CanExecuteChanged`變更是否發生影響時，就會發生的事件此命令應該執行。 [ `Command` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/)和[ `Command<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/)透過 Xamarin.Forms，所提供的類別，實作`ICommand`介面，其中`T`是的引數的型別`Execute`和`CanExecute`。

在檢視模型中，應該是類型的物件[ `Command` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/)或[ `Command<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/)中檢視模型類型的每一個公用屬性`ICommand`。 `Command`或`Command<T>`建構函式需要`Action`時已呼叫的回呼物件`ICommand.Execute`叫用方法。 `CanExecute`方法是選擇性的建構函式參數，且`Func`傳回`bool`。

下列程式碼示範如何[ `Command` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/)執行個體，代表註冊命令，而藉由指定的委派建構`Register`檢視模型的方法：

```csharp
public ICommand RegisterCommand => new Command(Register);
```

命令會公開透過此屬性傳回的參考，對該檢視`ICommand`。 當`Execute`上呼叫方法[ `Command` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/)物件，它只會轉送至中檢視模型中所指定的委派透過方法呼叫`Command`建構函式。

非同步方法可以叫用命令使用`async`和`await`關鍵字指定的命令時`Execute`委派。 這表示回呼是`Task`且應等候。 例如，下列程式碼示範如何[ `Command` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/)執行個體，代表一個登入的命令，這藉由指定的委派建構`SignInAsync`檢視模型的方法：

```csharp
public ICommand SignInCommand => new Command(async () => await SignInAsync());
```

參數可以傳遞至`Execute`和`CanExecute`動作使用[ `Command<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/)類別具現化命令。 例如，下列程式碼示範如何`Command<T>`執行個體用來指出`NavigateAsync`方法需要類型的引數`string`:

```csharp
public ICommand NavigateCommand => new Command<string>(NavigateAsync);
```

在這兩[ `Command` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/)和[ `Command<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/)類別，以委派`CanExecute`每個建構函式中的方法是選擇性的。 如果未指定委派，`Command`會傳回`true`如`CanExecute`。 不過，檢視模型可以指出在命令中的變更`CanExecute`藉由呼叫狀態`ChangeCanExecute`方法`Command`物件。 這會導致`CanExecuteChanged`會引發事件。 任何在 UI 中的控制項繫結至命令會更新其啟用的狀態，以反映資料繫結命令的可用性。

#### <a name="invoking-commands-from-a-view"></a>叫用命令從檢視表

下列程式碼範例示範如何[ `Grid` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/)中`LoginView`繫結至`RegisterCommand`中`LoginViewModel`類別使用[ `TapGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/)執行個體：

```xaml
<Grid Grid.Column="1" HorizontalOptions="Center">  
    <Label Text="REGISTER" TextColor="Gray"/>  
    <Grid.GestureRecognizers>  
        <TapGestureRecognizer Command="{Binding RegisterCommand}" NumberOfTapsRequired="1" />  
    </Grid.GestureRecognizers>  
</Grid>
```

命令參數也可以選擇性地定義使用[ `CommandParameter` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TapGestureRecognizer.CommandParameter/)屬性。 已在指定的預期的引數類型`Execute`和`CanExecute`目標方法。 [ `TapGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/)會自動叫用目標命令使用者互動附加的控制項時。 命令參數，如果提供，將會當做引數傳遞給命令的`Execute`委派。

<a name="implementing_behaviors" />

### <a name="implementing-behaviors"></a>實作行為

行為允許加入 UI 控制項，而不具有子類別化的功能。 相反地，功能是行為類別中實作，而且附加到控制項，如同它是控制項本身的一部分。 行為可讓您實作您通常具有寫入做為程式碼後置，因為它與控制項的方式，它可以精確地附加至控制項，並跨多個檢視或應用程式封裝以供重複使用的 API 的直接互動的程式碼。 在 MVVM 內容中，這些行為屬於命令連接控制項很實用的方法。

附加至透過附加的內容控制項的行為稱為*附加行為*。 行為接著可以使用公開應用程式開發介面，它會附加至將功能加入至該控制項或檢視的視覺化樹狀結構中的其他控制項的項目。 EShopOnContainers 行動應用程式包含`LineColorBehavior`類別，這是附加的行為。 如需這個行為的詳細資訊，請參閱[顯示驗證錯誤](~/xamarin-forms/enterprise-application-patterns/validation.md#displaying_validation_errors)。

Xamarin.Forms 行為是衍生自類別[ `Behavior` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior/)或[ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/)類別，其中`T `行為應該套用至控制項的類型。 這些類別會提供`OnAttachedTo`和`OnDetachingFrom`提供行為會附加和卸離控制項時，會執行的邏輯應該覆寫的方法。

EShopOnContainers 行動應用程式，`BindableBehavior<T>`類別衍生自[ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/)類別。 目的`BindableBehavior<T>`類別是基底類別提供需要的 Xamarin.Forms 行為[ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/)設為附加控制項的行為。

`BindableBehavior<T>`類別提供可覆寫`OnAttachedTo`方法，以設定[ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/)的行為，和可覆寫`OnDetachingFrom`方法清除`BindingContext`。 此外，此類別會儲存在附加的控制項的參考`AssociatedObject`屬性。

EShopOnContainers 行動裝置應用程式包含`EventToCommandBehavior`類別，執行命令，以回應事件的發生。 此類別衍生自`BindableBehavior<T>`類別，因此可以繫結至的行為，並且執行`ICommand`所指定`Command`取用行為時的屬性。 下列程式碼範例顯示 `EventToCommandBehavior` 類別：

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

`OnAttachedTo`和`OnDetachingFrom`方法可用來註冊及取消註冊事件處理常式中定義的事件`EventName`屬性。 然後，當事件引發時，`OnFired`叫用方法時，它會執行命令。

使用的優點`EventToCommandBehavior`當事件引發時，執行命令時，命令可能會與未設計成與命令互動控制項相關聯。 此外，這將事件處理程式碼檢視模型，它可在其中進行單元測試。

#### <a name="invoking-behaviors-from-a-view"></a>叫用從檢視表的行為

`EventToCommandBehavior`特別適用於附加至控制項不支援命令的命令。 例如，`ProfileView`使用`EventToCommandBehavior`執行`OrderDetailCommand`時[ `ItemTapped` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemTapped/)事件引發[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)所示，其中會列出使用者的訂單下列程式碼：

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

在執行階段，`EventToCommandBehavior`互動會回應[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)。 當選取的項目`ListView`、 [ `ItemTapped` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemTapped/)會引發事件，會同時執行`OrderDetailCommand`中`ProfileViewModel`。 根據預設，此事件的事件引數會傳遞至命令。 這項資料會轉換來源和目標之間傳遞中指定轉換器`EventArgsConverter`屬性，它會傳回[ `Item` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemTappedEventArgs.Item/)的`ListView`從[ `ItemTappedEventArgs`](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemTappedEventArgs/). 因此，當`OrderDetailCommand`執行時，所選`Order`做為參數傳遞至註冊的動作。

如需行為的詳細資訊，請參閱[行為](~/xamarin-forms/app-fundamentals/behaviors/index.md)。

## <a name="summary"></a>總結

模型-檢視-ViewModel (MVVM) 模式，有助於清楚分隔應用程式從其使用者介面 (UI) 的商務和呈現邏輯。 維護應用程式邏輯和 UI 清楚分隔有助於解決許多開發的問題，並可以讓您更輕鬆地測試、 維護及發展的應用程式。 它可以也可大幅改善程式碼重複使用的機會，並可讓開發人員和開發應用程式中其各自的一部分時，UI 設計工具可以更輕易地共同作業。

使用 MVVM 模式的應用程式 UI 和基本呈現與商務邏輯分成三個不同的類別： 檢視中，封裝的 UI 和 UI 邏輯。檢視模型封裝呈現邏輯和檢視狀態。與封裝的應用程式商務邏輯和資料模型。


## <a name="related-links"></a>相關連結

- [下載電子書 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （範例）](https://github.com/dotnet-architecture/eShopOnContainers)
