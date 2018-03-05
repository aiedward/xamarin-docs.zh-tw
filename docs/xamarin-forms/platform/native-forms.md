---
title: "原生格式"
description: "原生格式讓 Xamarin.Forms ContentPage 衍生頁可供原生 Xamarin.iOS、 Xamarin.Android 和通用 Windows 平台 (UWP) 專案。 原生專案可能會耗用 ContentPage 衍生頁面直接加入至專案，或從可攜式類別程式庫 (PCL)、.NET 標準程式庫或共用的專案。 本文說明如何使用 ContentPage 衍生的頁面，都會直接加入至原生專案，以及如何在它們之間瀏覽。"
ms.topic: article
ms.prod: xamarin
ms.assetid: f343fc21-dfb1-4364-a332-9da6705d36bc
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/11/2018
ms.openlocfilehash: 89636b874f8dbc8f66280dcc1ed99d0f832ff312
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="native-forms"></a>原生格式

_原生格式讓 Xamarin.Forms ContentPage 衍生頁可供原生 Xamarin.iOS、 Xamarin.Android 和通用 Windows 平台 (UWP) 專案。原生專案可能會耗用 ContentPage 衍生頁面直接加入至專案，或從可攜式類別程式庫 (PCL)、.NET 標準程式庫或共用的專案。本文說明如何使用 ContentPage 衍生的頁面，都會直接加入至原生專案，以及如何在它們之間瀏覽。_

Xamarin.Forms 應用程式通常包括衍生自的一個或多個頁面[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)，以及這些頁面中 PCL、.NET 標準程式庫或共用專案共用之所有平台。 不過，原生格式可讓`ContentPage`-衍生直接加入至原生 Xamarin.iOS、 Xamarin.Android 和 UWP 應用程式的頁面。 相較於具有原生專案取用`ContentPage`-衍生的頁面從 PCL、.NET 標準程式庫或共用專案，直接將頁面加入原生專案中的優點是頁面可以使用原生的檢視來延伸。 然後可以在 XAML 中使用命名原生檢視`x:Name`並參考從程式碼後置。 如需原生檢視的詳細資訊，請參閱[原生檢視](~/xamarin-forms/platform/native-views/index.md)。

使用 Xamarin.Forms 的程序[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-衍生原生專案中的頁面如下所示：

1. 加入原生專案中的 Xamarin.Forms NuGet 套件。
1. 新增[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-衍生 頁面上，以及任何相依，原生專案。
1. 呼叫 `Forms.Init` 方法。
1. 建構的執行個體[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-衍生頁面，並將它轉換成適當的原生類型，使用下列的擴充方法的其中一個：`CreateViewController`對於 iOS，`CreateFragment`或`CreateSupportFragment`for Android，或`CreateFrameworkElement` uwp。
1. 瀏覽至的原生類型表示[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-衍生使用原生瀏覽應用程式開發介面的頁面。

必須藉由呼叫初始化 Xamarin.Forms`Forms.Init`方法之前原生專案可以建構[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-衍生頁面。 它是最方便的應用程式流程中選擇何時要執行此動作主要取決於-無法執行應用程式啟動時或之前`ContentPage`-建構衍生的頁面。 在本文中，並隨附的範例應用程式，`Forms.Init`方法在應用程式啟動時呼叫。

> [!NOTE]
> **請注意**: **NativeForms**範例應用程式方案不包含任何 Xamarin.Forms 專案。 相反地，會包含專案 Xamarin.iOS、 Xamarin.Android 專案時和 UWP 專案。 每個專案是使用原生格式來取用的原生專案[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-衍生的頁面。 不過，沒有的理由為何無法使用原生專案`ContentPage`-取自 PCL、.NET 標準程式庫或共用專案中的頁面。

當使用原生格式，Xamarin.Forms 這類功能[ `DependencyService` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/)， [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/)，和資料繫結引擎，而仍的所有工作。

## <a name="ios"></a>iOS

在 iOS、`FinishedLaunching`中覆寫`AppDelegate`類別通常是可執行應用程式啟動相關的工作。 應用程式已啟動，並通常會覆寫設定的主視窗和檢視控制站之後呼叫。 下列程式碼範例示範`AppDelegate`範例應用程式中的類別：

```csharp
[Register("AppDelegate")]
public class AppDelegate : UIApplicationDelegate
{
    public static AppDelegate Instance;

    UIWindow _window;
    UINavigationController _navigation;

    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        Forms.Init();

        Instance = this;
        _window = new UIWindow(UIScreen.MainScreen.Bounds);

        UINavigationBar.Appearance.SetTitleTextAttributes(new UITextAttributes
        {
            TextColor = UIColor.Black
        });

        var mainPage = new PhonewordPage().CreateViewController();
        mainPage.Title = "Phoneword";

        _navigation = new UINavigationController(mainPage);
        _window.RootViewController = _navigation;
        _window.MakeKeyAndVisible();

        return true;
    }
    ...
}
```

`FinishedLaunching`方法會執行下列工作：

- Xamarin.Forms 藉由呼叫初始化`Forms.Init`方法。
- 若要參考`AppDelegate`類別會儲存在`static``Instance`欄位。 這是為了提供一個機制來呼叫方法中定義的其他類別`AppDelegate`類別。
- `UIWindow`，這原生 iOS 應用程式中檢視的主要容器建立。
- `PhonewordPage`類別，方法是透過 Xamarin.Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-衍生在 XAML 中定義的頁面、 建構及轉換成`UIViewController`使用`CreateViewController`擴充方法。
- `Title`屬性`UIViewController`設定，這將會顯示在`UINavigationBar`。
- A`UINavigationController`建立用於管理階層式導覽。 `UINavigationController`類別會管理檢視的控制器、 堆疊和`UIViewController`傳遞至建構函式將會呈現一開始時`UINavigationController`載入。
- `UINavigationController`執行個體設定為最上層`UIViewController`如`UIWindow`，而`UIWindow`被設定為應用程式金鑰的視窗，就會顯示。

一次`FinishedLaunching`方法已執行，在 Xamarin.Forms 中定義 UI`PhonewordPage`類別將會顯示，如下列螢幕擷取畫面所示：

[![](native-forms-images/ios-phonewordpage.png "iOS PhonewordPage")](native-forms-images/ios-phonewordpage-large.png "iOS PhonewordPage")

與 UI，例如互動上點選[ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)，將會導致事件處理常式中`PhonewordPage`執行程式碼後置。 例如，當使用者點選**呼叫記錄**按鈕時，下列事件處理常式會執行：

```csharp
void OnCallHistory(object sender, EventArgs e)
{
    AppDelegate.Instance.NavigateToCallHistoryPage();
}
```

`static` `AppDelegate.Instance`欄位可讓`AppDelegate.NavigateToCallHistoryPage`方法被叫用的下列程式碼範例所示：

```csharp
public void NavigateToCallHistoryPage()
{
    var callHistoryPage = new CallHistoryPage().CreateViewController();
    callHistoryPage.Title = "Call History";
    _navigation.PushViewController(callHistoryPage, true);
}
```

`NavigateToCallHistoryPage`方法會將轉換 Xamarin.Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-衍生頁面，即可`UIViewController`與`CreateViewController`擴充方法和集合`Title`屬性`UIViewController`。 `UIViewController`再推入至`UINavigationController`由`PushViewController`方法。 因此，在 Xamarin.Forms 中定義的 UI`CallHistoryPage`類別將會顯示，如下列螢幕擷取畫面所示：

[![](native-forms-images/ios-callhistorypage.png "iOS CallHistoryPage")](native-forms-images/ios-callhistorypage-large.png "iOS CallHistoryPage")

當`CallHistoryPage`顯示，請點選 [上一步] 箭號就會出現`UIViewController`的`CallHistoryPage`類別從`UINavigationController`，傳回使用者`UIViewController`如`PhonewordPage`類別。

## <a name="android"></a>Android

在 Android 上，`OnCreate`中覆寫`MainActivity`類別通常是可執行應用程式啟動相關的工作。 下列程式碼範例示範`MainActivity`範例應用程式中的類別：

```csharp
public class MainActivity : AppCompatActivity
{
    public static MainActivity Instance;

    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);

        Forms.Init(this, bundle);
        Instance = this;

        SetContentView(Resource.Layout.Main);
        var toolbar = FindViewById<Toolbar>(Resource.Id.toolbar);
        SetSupportActionBar(toolbar);
        SupportActionBar.Title = "Phoneword";

        var mainPage = new PhonewordPage().CreateFragment(this);
        FragmentManager
            .BeginTransaction()
            .Replace(Resource.Id.fragment_frame_layout, mainPage)
            .Commit();
        ...
    }
    ...
}
```

`OnCreate`方法會執行下列工作：

- Xamarin.Forms 藉由呼叫初始化`Forms.Init`方法。
- 若要參考`MainActivity`類別會儲存在`static``Instance`欄位。 這是為了提供一個機制來呼叫方法中定義的其他類別`MainActivity`類別。
- `Activity`配置資源的設定內容。 版面配置包含範例應用程式、`LinearLayout`包含`Toolbar`，和`FrameLayout`做為片段容器。
- `Toolbar`已擷取，並設定為在動作列`Activity`，而且會設定動作列標題。
- `PhonewordPage`類別，方法是透過 Xamarin.Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-衍生在 XAML 中定義的頁面、 建構及轉換成`Fragment`使用`CreateFragment`擴充方法。
- `FragmentManager`類別會建立並認可交易，以取代`FrameLayout`執行個體，其`Fragment`如`PhonewordPage`類別。

如需有關片段的詳細資訊，請參閱[片段](~/android/platform/fragments/index.md)。

> [!NOTE]
> **請注意**： 除了`CreateFragment`擴充方法，也包含 Xamarin.Forms`CreateSupportFragment`方法。 `CreateFragment`方法會建立`Android.App.Fragment`可以使用目標應用程式開發介面 11 應用程式和更新版本。 `CreateSupportFragment`方法會建立`Android.Support.V4.App.Fragment`可用以 11 以前的 API 版本為目標的應用程式中。

一次`OnCreate`方法已執行，在 Xamarin.Forms 中定義 UI`PhonewordPage`類別將會顯示，如下列螢幕擷取畫面所示：

[![](native-forms-images/android-phonewordpage.png "Android PhonewordPage")](native-forms-images/android-phonewordpage-large.png "Android PhonewordPage")

與 UI，例如互動上點選[ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)，將會導致事件處理常式中`PhonewordPage`執行程式碼後置。 例如，當使用者點選**呼叫記錄**按鈕時，下列事件處理常式會執行：

```csharp
void OnCallHistory(object sender, EventArgs e)
{
    MainActivity.Instance.NavigateToCallHistoryPage();
}
```

`static` `MainActivity.Instance`欄位可讓`MainActivity.NavigateToCallHistoryPage`方法被叫用的下列程式碼範例所示：

```csharp
public void NavigateToCallHistoryPage()
{
    var callHistoryPage = new CallHistoryPage().CreateFragment(this);
    FragmentManager
        .BeginTransaction()
        .AddToBackStack(null)
        .Replace(Resource.Id.fragment_frame_layout, callHistoryPage)
        .Commit();
}
```

`NavigateToCallHistoryPage`方法會將轉換 Xamarin.Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-衍生頁面，即可`Fragment`與`CreateFragment`擴充方法，並將`Fragment`片段回堆疊。 因此，在 Xamarin.Forms 中定義的 UI`CallHistoryPage`將會顯示，如下列螢幕擷取畫面所示：

[![](native-forms-images/android-callhistorypage.png "Android CallHistoryPage")](native-forms-images/android-callhistorypage-large.png "Android CallHistoryPage")

當`CallHistoryPage`顯示，請點選 [上一步] 箭號就會出現`Fragment`的`CallHistoryPage`片段上一頁堆疊，從傳回使用者`Fragment`的`PhonewordPage`類別。

### <a name="enabling-back-navigation-support"></a>啟用向後巡覽支援

`FragmentManager`類別具有`BackStackChanged`每當片段上一頁堆疊的內容變更時引發的事件。 `OnCreate`方法中的`MainActivity`類別包含此事件的匿名的事件處理常式：

```csharp
FragmentManager.BackStackChanged += (sender, e) =>
{
    bool hasBack = FragmentManager.BackStackEntryCount > 0;
    SupportActionBar.SetHomeButtonEnabled(hasBack);
    SupportActionBar.SetDisplayHomeAsUpEnabled(hasBack);
    SupportActionBar.Title = hasBack ? "Call History" : "Phoneword";
};
```

這個事件處理常式會顯示在動作列上一步 按鈕提供會有一或多個`Fragment`片段上的執行個體傳回堆疊。 點選 [上一頁] 按鈕的回應由`OnOptionsItemSelected`覆寫：

```csharp
public override bool OnOptionsItemSelected(Android.Views.IMenuItem item)
{
    if (item.ItemId == global::Android.Resource.Id.Home && FragmentManager.BackStackEntryCount > 0)
    {
        FragmentManager.PopBackStack();
        return true;
    }
    return base.OnOptionsItemSelected(item);
}
```

`OnOptionsItemSelected`覆寫會在已選取 [選項] 功能表中的項目時呼叫。 這項實作快顯片段上一頁堆疊，從目前的片段，前提是已選取 [上一頁] 按鈕，並有一或多個`Fragment`片段上的執行個體傳回堆疊。

### <a name="multiple-activities"></a>多個活動

當應用程式由多個活動，組成[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-衍生的頁面可以內嵌入每個活動。 在此案例中，`Forms.Init`只有在需要呼叫方法`OnCreate`覆寫第一個`Activity`，內嵌 Xamarin.Forms `ContentPage`。 不過，這有下列影響：

- 值`Xamarin.Forms.Color.Accent`將取自`Activity`呼叫`Forms.Init`方法。
- 值`Xamarin.Forms.Application.Current`相關聯`Activity`呼叫`Forms.Init`方法。

### <a name="choosing-a-file"></a>選擇檔案

內嵌[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-衍生使用的頁面[ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) ，需要支援 「 選擇檔案 」 的 HTML 按鈕、`Activity`需要覆寫`OnActivityResult`方法：

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    ActivityResultCallbackRegistry.InvokeCallback(requestCode, resultCode, data);
}
```

## <a name="uwp"></a>UWP

在 UWP、 原生`App`類別通常是可執行應用程式啟動相關的工作。 Xamarin.Forms 通常初始化時，在 Xamarin.Forms UWP 應用程式中`OnLaunched`覆寫在原生`App`類別，以傳遞`LaunchActivatedEventArgs`引數`Forms.Init`方法。 基於這個理由，原生的 UWP 應用程式使用 Xamarin.Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-衍生的頁面可以最容易呼叫`Forms.Init`方法從`App.OnLaunched`方法。

根據預設，原生`App`類別會啟動`MainPage`做為第一頁的應用程式的類別。 下列程式碼範例示範`MainPage`範例應用程式中的類別：

```csharp
public sealed partial class MainPage : Page
{
    public static MainPage Instance;

    public MainPage()
    {
        this.InitializeComponent();
        this.NavigationCacheMode = NavigationCacheMode.Enabled;
        Instance = this;
        this.Content = new Phoneword.UWP.Views.PhonewordPage().CreateFrameworkElement();
    }
    ...
}
```

`MainPage`建構函式會執行下列工作：

- 啟用快取 頁面上，使新`MainPage`使用者瀏覽至網頁時，將不建構。
- 若要參考`MainPage`類別會儲存在`static``Instance`欄位。 這是為了提供一個機制來呼叫方法中定義的其他類別`MainPage`類別。
- `PhonewordPage`類別，方法是透過 Xamarin.Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-衍生在 XAML 中定義的頁面、 建構及轉換成`FrameworkElement`使用`CreateFrameworkElement`擴充方法，並將其設定的內容`MainPage`類別。

一次`MainPage`建構函式已經執行，在 Xamarin.Forms 中定義 UI`PhonewordPage`類別將會顯示，如下列螢幕擷取畫面所示：

[![](native-forms-images/uwp-phonewordpage.png "UWP PhonewordPage")](native-forms-images/uwp-phonewordpage-large.png "UWP PhonewordPage")

與 UI，例如互動上點選[ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)，將會導致事件處理常式中`PhonewordPage`執行程式碼後置。 例如，當使用者點選**呼叫記錄**按鈕時，下列事件處理常式會執行：

```csharp
void OnCallHistory(object sender, EventArgs e)
{
    Phoneword.UWP.MainPage.Instance.NavigateToCallHistoryPage();
}
```

`static` `MainPage.Instance`欄位可讓`MainPage.NavigateToCallHistoryPage`方法被叫用的下列程式碼範例所示：

```csharp
public void NavigateToCallHistoryPage()
{
    this.Frame.Navigate(new CallHistoryPage());
}
```

在 UWP 中的瀏覽通常透過執行`Frame.Navigate`方法會採用`Page`引數。 Xamarin.Forms 定義`Frame.Navigate`擴充方法採用[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-衍生頁面執行個體。 因此，當`NavigateToCallHistoryPage`執行的方法時，在 Xamarin.Forms 中定義 UI`CallHistoryPage`將會顯示，如下列螢幕擷取畫面所示：

[![](native-forms-images/uwp-callhistorypage.png "UWP CallHistoryPage")](native-forms-images/uwp-callhistorypage-large.png "UWP CallHistoryPage")

當`CallHistoryPage`顯示，請點選 [上一步] 箭號就會出現`FrameworkElement`的`CallHistoryPage`從應用程式中的上一頁堆疊，傳回使用者`FrameworkElement`的`PhonewordPage`類別。

### <a name="enabling-back-navigation-support"></a>啟用向後巡覽支援

在 UWP，應用程式必須啟用向後巡覽，所有的軟硬體上一頁按鈕，跨不同裝置的表單係數。 這可以透過註冊事件處理常式`BackRequested`事件，可在執行`OnLaunched`方法在原生`App`類別：

```csharp
protected override void OnLaunched(LaunchActivatedEventArgs e)
{
    Frame rootFrame = Window.Current.Content as Frame;

    if (rootFrame == null)
    {
        ...      
        // Place the frame in the current Window
        Window.Current.Content = rootFrame;

        SystemNavigationManager.GetForCurrentView().BackRequested += OnBackRequested;
    }
    ...
}
```

啟動應用程式時，`GetForCurrentView`方法擷取`SystemNavigationManager`物件相關聯的目前檢視中，然後註冊事件處理常式`BackRequested`事件。 應用程式只會收到這個事件，如果它是前景應用程式，並在回應時，呼叫`OnBackRequested`事件處理常式：

```csharp
void OnBackRequested(object sender, BackRequestedEventArgs e)
{
    Frame rootFrame = Window.Current.Content as Frame;
    if (rootFrame.CanGoBack)
    {
        e.Handled = true;
        rootFrame.GoBack();
    }
}
```

`OnBackRequested`事件處理常式呼叫`GoBack`方法上的應用程式和集合的根畫面`BackRequestedEventArgs.Handled`屬性`true`來標示為已處理的事件。 若要將事件標記為已處理的失敗可能會導致系統不用離開 （上的行動裝置系列） 的應用程式，或略過 （桌面裝置系列上） 事件。

應用程式依賴在電話上，提供系統上一頁按鈕，但是可以選擇是否要顯示在桌上型裝置上的標題列上一步 按鈕。 這藉由設定`AppViewBackButtonVisibility`屬性設為其中的`AppViewBackButtonVisibility`列舉值：

```csharp
void OnNavigated(object sender, NavigationEventArgs e)
{
    SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility =
        ((Frame)sender).CanGoBack ? AppViewBackButtonVisibility.Visible : AppViewBackButtonVisibility.Collapsed;
}
```

`OnNavigated`事件處理常式，會在回應中執行`Navigated`頁面巡覽發生時，事件的引發，更新的標題列上一步按鈕的可見性。 這可確保標題列的 [上一頁] 按鈕時，會顯示應用程式中的上一頁堆疊不是空白，或移除的標題列中，如果應用程式中的上一頁堆疊是空的。

在 UWP 上向後巡覽支援的相關資訊，請參閱[瀏覽歷程記錄及向後導覽，用於 UWP 應用程式](/windows/uwp/design/basics/navigation-history-and-backwards-navigation/)。

## <a name="summary"></a>總結

原生格式允許 Xamarin.Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-衍生頁面，以供原生 Xamarin.iOS、 Xamarin.Android 和通用 Windows 平台 (UWP) 專案。 原生專案可能會耗用`ContentPage`-衍生直接加入至專案，或從 PCL、.NET 標準程式庫或共用專案的頁面。 本文說明如何使用`ContentPage`-衍生都會直接加入至原生專案，以及如何在它們之間瀏覽的頁面。


## <a name="related-links"></a>相關連結

- [NativeForms （範例）](https://developer.xamarin.com/samples/xamarin-forms/Native2Forms/)
- [原生檢視](~/xamarin-forms/platform/native-views/index.md)
