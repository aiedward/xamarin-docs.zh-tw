---
title: Xamarin.Forms在 Xamarin 原生專案中
description: 本文說明如何取用直接新增至 Xamarin 原生專案的 ContentPage 衍生頁面，以及如何在兩者之間流覽。
ms.prod: xamarin
ms.assetid: f343fc21-dfb1-4364-a332-9da6705d36bc
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/19/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9fb741a03d1c8dd2a8754120d0b46567d8889a0b
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84132271"
---
# <a name="xamarinforms-in-xamarin-native-projects"></a>Xamarin.Forms在 Xamarin 原生專案中

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/native2forms)

一般而言， Xamarin.Forms 應用程式會包含一個或多個衍生自的頁面 [`ContentPage`](xref:Xamarin.Forms.ContentPage) ，而這些頁面會由 .NET Standard 程式庫專案或共用專案中的所有平臺共用。 不過，原生表單可讓 `ContentPage` 衍生的頁面直接新增至原生的 Xamarin. iOS、Xamarin 和 UWP 應用程式。 相較于讓原生專案取用 `ContentPage` 來自 .NET Standard 程式庫專案或共用專案的衍生頁面，將頁面直接加入至原生專案的優點是可以使用原生視圖來擴充頁面。 然後可以在 XAML 中使用原生視圖命名 `x:Name` ，並從程式碼後置中參考。 如需原生視圖的詳細資訊，請參閱[原生視圖](~/xamarin-forms/platform/native-views/index.md)。

在 Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) 原生專案中使用衍生頁面的程式如下所示：

1. 將 Xamarin.Forms NuGet 套件新增至原生專案。
1. 將 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 衍生的網頁和任何相依性新增至原生專案。
1. 呼叫 `Forms.Init` 方法。
1. [`ContentPage`](xref:Xamarin.Forms.ContentPage)使用下列其中一個擴充方法來建立衍生頁面的實例，並將它轉換成適當的原生類型： `CreateViewController` 適用于 iOS、 `CreateSupportFragment` 適用于 Android 或 `CreateFrameworkElement` 適用于 UWP。
1. 使用原生導覽 API，流覽至衍生頁面的原生類型標記法 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 。

Xamarin.Forms必須先呼叫方法來初始化， `Forms.Init` 原生專案才能建立衍生的 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 頁面。 選擇何時執行這項作業主要取決於應用程式流程中最方便的時機–它可以在應用程式啟動時執行，或在衍生的頁面建立之前進行 `ContentPage` 。 在本文中，和隨附的範例應用程式中， `Forms.Init` 會在應用程式啟動時呼叫方法。

> [!NOTE]
> **NativeForms**範例應用程式方案不包含任何 Xamarin.Forms 專案。 相反地，它是由一個 Xamarin 專案、一個 Xamarin Android 專案和一個 UWP 專案所組成。 每個專案都是使用原生表單來取用衍生頁面的原生專案 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 。 不過，原生專案無法 `ContentPage` 從 .NET Standard 程式庫專案或共用專案取用衍生頁面的原因並不合理。

使用原生表單時， Xamarin.Forms 像是、和資料系結引擎等功能 [`DependencyService`](xref:Xamarin.Forms.DependencyService) [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) 都仍然可以使用。 不過，頁面導覽必須使用原生導覽 API 來執行。

## <a name="ios"></a>iOS

在 iOS 上， `FinishedLaunching` 類別中的覆寫 `AppDelegate` 通常是執行應用程式啟動相關工作的位置。 它會在應用程式啟動後呼叫，而且通常會覆寫以設定主視窗和視圖控制器。 下列程式碼範例顯示 `AppDelegate` 範例應用程式中的類別：

```csharp
[Register("AppDelegate")]
public class AppDelegate : UIApplicationDelegate
{
    public static AppDelegate Instance;
    UIWindow _window;
    AppNavigationController _navigation;

    public static string FolderPath { get; private set; }

    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        Forms.Init();

        Instance = this;
        _window = new UIWindow(UIScreen.MainScreen.Bounds);

        UINavigationBar.Appearance.SetTitleTextAttributes(new UITextAttributes
        {
            TextColor = UIColor.Black
        });

        FolderPath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData));
        UIViewController mainPage = new NotesPage().CreateViewController();
        mainPage.Title = "Notes";

        _navigation = new AppNavigationController(mainPage);
        _window.RootViewController = _navigation;
        _window.MakeKeyAndVisible();

        return true;
    }
    // ...
}
```

`FinishedLaunching` 方法會執行下列工作：

- Xamarin.Forms會藉由呼叫方法來初始化 `Forms.Init` 。
- 類別的參考 `AppDelegate` 會儲存在 `static` `Instance` 欄位中。 這是為了讓其他類別能夠呼叫在類別中定義的方法 `AppDelegate` 。
- `UIWindow`會建立，這是原生 iOS 應用程式中 views 的主要容器。
- `FolderPath`屬性會初始化為裝置上儲存附注資料的路徑。
- `NotesPage`類別（也就是 Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) 在 XAML 中定義的衍生頁面）會 `UIViewController` 使用擴充方法來進行結構化並轉換成 `CreateViewController` 。
- `Title`已設定的屬性 `UIViewController` ，它會顯示在上 `UINavigationBar` 。
- `AppNavigationController`會建立以管理階層式導覽。 這是自訂的流覽控制器類別，其衍生自 `UINavigationController` 。 `AppNavigationController`物件會管理一堆視圖控制器，而 `UIViewController` 傳遞至此函式的會在載入時一開始呈現 `AppNavigationController` 。
- `AppNavigationController`物件會設定為的最上層 `UIViewController` `UIWindow` ，而 `UIWindow` 會設定為應用程式的索引鍵視窗，並顯示為可見。

一旦 `FinishedLaunching` 執行方法， Xamarin.Forms `NotesPage` 就會顯示類別中定義的 UI，如下列螢幕擷取畫面所示：

[![使用 XAML 中定義的 UI 之 Xamarin iOS 應用程式的螢幕擷取畫面](native-forms-images/ios-notespage.png "包含 XAML UI 的 Xamarin iOS 應用程式")](native-forms-images/ios-notespage-large.png#lightbox "包含 XAML UI 的 Xamarin iOS 應用程式")

例如，藉由在上使用來與 UI 互動， **+** [`Button`](xref:Xamarin.Forms.Button) 會導致程式碼後置中的下列事件處理常式 `NotesPage` 執行：

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    AppDelegate.Instance.NavigateToNoteEntryPage(new Note());
}
```

`static` `AppDelegate.Instance` 欄位允許叫用 `AppDelegate.NavigateToNoteEntryPage` 方法，如下列程式碼範例所示：

```csharp
public void NavigateToNoteEntryPage(Note note)
{
    var noteEntryPage = new NoteEntryPage
    {
        BindingContext = note
    }.CreateViewController();
    noteEntryPage.Title = "Note Entry";
    _navigation.PushViewController(noteEntryPage, true);
}
```

`NavigateToNoteEntryPage`方法會 Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) 使用擴充方法，將衍生的頁面轉換成 `UIViewController` `CreateViewController` ，並設定的 `Title` 屬性 `UIViewController` 。 `UIViewController`接著，方法會將推送至 `AppNavigationController` `PushViewController` 。 因此，將會顯示類別中定義的 UI Xamarin.Forms `NoteEntryPage` ，如下列螢幕擷取畫面所示：

[![使用 XAML 中定義的 UI 之 Xamarin iOS 應用程式的螢幕擷取畫面](native-forms-images/ios-noteentrypage.png "包含 XAML UI 的 Xamarin iOS 應用程式")](native-forms-images/ios-noteentrypage-large.png#lightbox "包含 XAML UI 的 Xamarin iOS 應用程式")

當 `NoteEntryPage` 顯示時，[上一頁流覽] 會從彈出類別的，並將 `UIViewController` 使用者傳回 `NoteEntryPage` `AppNavigationController` 給類別的 `UIViewController` `NotesPage` 。 不過， `UIViewController` 從 iOS 原生導覽堆疊彈出，並不會自動處置 `UIViewController` 和附加的 `Page` 物件。 因此， `AppNavigationController` 類別會覆寫 `PopViewController` 方法，以便在回溯導覽上處置視圖控制器：

```csharp
public class AppNavigationController : UINavigationController
{
    //...
    public override UIViewController PopViewController(bool animated)
    {
        UIViewController topView = TopViewController;
        if (topView != null)
        {
            // Dispose of ViewController on back navigation.
            topView.Dispose();
        }
        return base.PopViewController(animated);
    }
}
```

覆 `PopViewController` 寫 `Dispose` `UIViewController` 會針對從 iOS 原生導覽堆疊推出的物件呼叫方法。 若未這麼做，將會導致 `UIViewController` 和附加的 `Page` 物件被孤立。

> [!IMPORTANT]
> 孤立物件無法進行垃圾收集，因而導致記憶體流失。

## <a name="android"></a>Android

在 Android 上， `OnCreate` 類別中的覆寫 `MainActivity` 通常是執行應用程式啟動相關工作的位置。 下列程式碼範例顯示 `MainActivity` 範例應用程式中的類別：

```csharp
public class MainActivity : AppCompatActivity
{
    public static string FolderPath { get; private set; }

    public static MainActivity Instance;

    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);

        Forms.Init(this, bundle);
        Instance = this;

        SetContentView(Resource.Layout.Main);
        var toolbar = FindViewById<Toolbar>(Resource.Id.toolbar);
        SetSupportActionBar(toolbar);
        SupportActionBar.Title = "Notes";

        FolderPath = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.LocalApplicationData));
        Android.Support.V4.App.Fragment mainPage = new NotesPage().CreateSupportFragment(this);
        SupportFragmentManager
            .BeginTransaction()
            .Replace(Resource.Id.fragment_frame_layout, mainPage)
            .Commit();
        ...
    }
    ...
}
```

`OnCreate` 方法會執行下列工作：

- Xamarin.Forms會藉由呼叫方法來初始化 `Forms.Init` 。
- 類別的參考 `MainActivity` 會儲存在 `static` `Instance` 欄位中。 這是為了讓其他類別能夠呼叫在類別中定義的方法 `MainActivity` 。
- `Activity`內容是從配置資源進行設定。 在範例應用程式中，版面配置包含 `LinearLayout` 包含的 `Toolbar` ，而則是 `FrameLayout` 做為片段容器。
- `Toolbar`會抓取並設定為的動作列 `Activity` ，並設定動作列標題。
- `FolderPath`屬性會初始化為裝置上儲存附注資料的路徑。
- `NotesPage`類別（也就是 Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) 在 XAML 中定義的衍生頁面）會 `Fragment` 使用擴充方法來進行結構化並轉換成 `CreateSupportFragment` 。
- `SupportFragmentManager`類別會建立並認可交易，以將 `FrameLayout` 實例取代 `Fragment` 為類別的 `NotesPage` 。

如需有關片段的詳細資訊，請參閱[片段](~/android/platform/fragments/index.md)。

一旦 `OnCreate` 執行方法， Xamarin.Forms `NotesPage` 就會顯示類別中定義的 UI，如下列螢幕擷取畫面所示：

[![使用 XAML 中定義之 UI 的 Xamarin Android 應用程式螢幕擷取畫面](native-forms-images/android-notespage.png "包含 XAML UI 的 Xamarin Android 應用程式")](native-forms-images/android-notespage-large.png#lightbox "包含 XAML UI 的 Xamarin Android 應用程式")

例如，藉由在上使用來與 UI 互動， **+** [`Button`](xref:Xamarin.Forms.Button) 會導致程式碼後置中的下列事件處理常式 `NotesPage` 執行：

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    MainActivity.Instance.NavigateToNoteEntryPage(new Note());
}
```

`static` `MainActivity.Instance` 欄位允許叫用 `MainActivity.NavigateToNoteEntryPage` 方法，如下列程式碼範例所示：

```csharp
public void NavigateToNoteEntryPage(Note note)
{
    Android.Support.V4.App.Fragment noteEntryPage = new NoteEntryPage
    {
        BindingContext = note
    }.CreateSupportFragment(this);
    SupportFragmentManager
        .BeginTransaction()
        .AddToBackStack(null)
        .Replace(Resource.Id.fragment_frame_layout, noteEntryPage)
        .Commit();
}
```

`NavigateToNoteEntryPage`方法會 Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) 使用擴充方法，將衍生的頁面轉換成 `Fragment` `CreateSupportFragment` ，並將加入 `Fragment` 至片段的後端堆疊。 因此，將會顯示在中定義的 UI Xamarin.Forms `NoteEntryPage` ，如下列螢幕擷取畫面所示：

[![使用 XAML 中定義之 UI 的 Xamarin Android 應用程式螢幕擷取畫面](native-forms-images/android-noteentrypage.png "包含 XAML UI 的 Xamarin Android 應用程式")](native-forms-images/android-noteentrypage-large.png#lightbox "包含 XAML UI 的 Xamarin Android 應用程式")

當 `NoteEntryPage` 顯示時，按 [上一步] 箭號就會從片段的後堆疊中快顯的，並將 `Fragment` `NoteEntryPage` 使用者傳回給 `Fragment` 類別的 `NotesPage` 。

### <a name="enable-back-navigation-support"></a>啟用回溯導覽支援

`SupportFragmentManager`類別有一個 `BackStackChanged` 事件，會在每次片段回溯堆疊的內容變更時引發。 `OnCreate`類別中的方法 `MainActivity` 包含此事件的匿名事件處理常式：

```csharp
SupportFragmentManager.BackStackChanged += (sender, e) =>
{
    bool hasBack = SupportFragmentManager.BackStackEntryCount > 0;
    SupportActionBar.SetHomeButtonEnabled(hasBack);
    SupportActionBar.SetDisplayHomeAsUpEnabled(hasBack);
    SupportActionBar.Title = hasBack ? "Note Entry" : "Notes";
};
```

這個事件處理常式會在動作列上顯示 [上一頁] 按鈕，前提是該片段上有一個或多個 `Fragment` 實例。 點擊 [上一頁] 按鈕的回應是由覆 `OnOptionsItemSelected` 寫處理：

```csharp
public override bool OnOptionsItemSelected(Android.Views.IMenuItem item)
{
    if (item.ItemId == global::Android.Resource.Id.Home && SupportFragmentManager.BackStackEntryCount > 0)
    {
        SupportFragmentManager.PopBackStack();
        return true;
    }
    return base.OnOptionsItemSelected(item);
}
```

`OnOptionsItemSelected`每當選取 [選項] 功能表中的專案時，就會呼叫覆寫。 如果已選取 [上一頁] 按鈕，且片段上有一個或多個實例，則這個執行會從片段的後置堆疊中取出目前的片段 `Fragment` 。

### <a name="multiple-activities"></a>多個活動

當應用程式由多個活動組成時， [`ContentPage`](xref:Xamarin.Forms.ContentPage) 可以將衍生的頁面內嵌到每個活動中。 在此案例中， `Forms.Init` 只需要在第一個內嵌的覆寫中呼叫方法 `OnCreate` `Activity` Xamarin.Forms `ContentPage` 。 不過，這會造成下列影響：

- 的值 `Xamarin.Forms.Color.Accent` 將取自 `Activity` 呼叫 `Forms.Init` 方法的。
- 的值 `Xamarin.Forms.Application.Current` 將會與呼叫方法的相關聯 `Activity` `Forms.Init` 。

### <a name="choose-a-file"></a>選擇檔案

當內嵌的 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 衍生頁面使用 [`WebView`](xref:Xamarin.Forms.WebView) 需要支援 HTML [選擇檔案] 按鈕的時， `Activity` 將需要覆寫 `OnActivityResult` 方法：

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    ActivityResultCallbackRegistry.InvokeCallback(requestCode, resultCode, data);
}
```

## <a name="uwp"></a>UWP

在 UWP 上，原生 `App` 類別通常是執行應用程式啟動相關工作的位置。 Xamarin.Forms通常會在 Xamarin.Forms UWP 應用程式中，于原生類別的覆寫中初始化， `OnLaunched` `App` 以將 `LaunchActivatedEventArgs` 引數傳遞給 `Forms.Init` 方法。 基於這個理由，使用衍生頁面的原生 UWP 應用程式 Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) 最容易就能 `Forms.Init` 從方法呼叫方法 `App.OnLaunched` 。

根據預設，原生 `App` 類別會啟動 `MainPage` 類別做為應用程式的第一頁。 下列程式碼範例顯示 `MainPage` 範例應用程式中的類別：

```csharp
public sealed partial class MainPage : Page
{
    NotesPage notesPage;
    NoteEntryPage noteEntryPage;
    
    public static MainPage Instance;
    public static string FolderPath { get; private set; }

    public MainPage()
    {
        this.InitializeComponent();
        this.NavigationCacheMode = NavigationCacheMode.Enabled;
        Instance = this;
        FolderPath = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.LocalApplicationData));
        notesPage = new Notes.UWP.Views.NotesPage();
        this.Content = notesPage.CreateFrameworkElement();
        // ...        
    } 
    // ...
}
```

此函式會 `MainPage` 執行下列工作：

- 已啟用頁面的快取，因此 `MainPage` 當使用者流覽回到頁面時，不會建立新的。
- 類別的參考 `MainPage` 會儲存在 `static` `Instance` 欄位中。 這是為了讓其他類別能夠呼叫在類別中定義的方法 `MainPage` 。
- `FolderPath`屬性會初始化為裝置上儲存附注資料的路徑。
- `NotesPage`類別（也就是 Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) 在 XAML 中定義的衍生頁面）會使用擴充方法來進行結構化並轉換成 `FrameworkElement` ，然後 `CreateFrameworkElement` 將設定為類別的內容 `MainPage` 。

執行過此函式之後 `MainPage` ，將會顯示類別中定義的 UI Xamarin.Forms `NotesPage` ，如下列螢幕擷取畫面所示：

[![UWP 應用程式的螢幕擷取畫面，其使用以 XAML 定義的 UI Xamarin.Forms](native-forms-images/uwp-notespage.png "具有 [！的 UWP 應用程式OP.無-LOC （Xamarin）] XAML UI")](native-forms-images/uwp-notespage-large.png#lightbox "具有 [！的 UWP 應用程式OP.無-LOC （Xamarin）] XAML UI")

例如，藉由在上使用來與 UI 互動， **+** [`Button`](xref:Xamarin.Forms.Button) 會導致程式碼後置中的下列事件處理常式 `NotesPage` 執行：

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    MainPage.Instance.NavigateToNoteEntryPage(new Note());
}
```

`static` `MainPage.Instance` 欄位允許叫用 `MainPage.NavigateToNoteEntryPage` 方法，如下列程式碼範例所示：

```csharp
public void NavigateToNoteEntryPage(Note note)
{
    noteEntryPage = new Notes.UWP.Views.NoteEntryPage
    {
        BindingContext = note
    };
    this.Frame.Navigate(noteEntryPage);
}
```

UWP 中的導覽通常是使用 `Frame.Navigate` 方法來執行，而此方法會採用 `Page` 引數。 Xamarin.Forms定義 `Frame.Navigate` 會接受 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 衍生頁面實例的擴充方法。 因此，當 `NavigateToNoteEntryPage` 方法執行時，將會顯示在中定義的 UI Xamarin.Forms `NoteEntryPage` ，如下列螢幕擷取畫面所示：

[![UWP 應用程式的螢幕擷取畫面，其使用以 XAML 定義的 UI Xamarin.Forms](native-forms-images/uwp-noteentrypage.png "具有 [！的 UWP 應用程式OP.無-LOC （Xamarin）] XAML UI")](native-forms-images/uwp-noteentrypage-large.png#lightbox "具有 [！的 UWP 應用程式OP.無-LOC （Xamarin）] XAML UI")

當 `NoteEntryPage` 顯示時，按 [上一步] 箭號將會 `FrameworkElement` `NoteEntryPage` 從應用程式內的後置堆疊中快顯的，並將使用者傳回給 `FrameworkElement` 類別的 `NotesPage` 。

### <a name="enable-page-resizing-support"></a>啟用頁面調整大小支援

調整 UWP 應用程式視窗的大小時， Xamarin.Forms 也應該調整內容的大小。 若要完成此作業，請在函式中註冊事件的事件處理常式 `Loaded` `MainPage` ：

```csharp
public MainPage()
{
    // ...
    this.Loaded += OnMainPageLoaded;
    // ...
}
```

`Loaded`當頁面已配置、呈現並準備好進行互動時，就會引發事件，並 `OnMainPageLoaded` 在回應中執行方法：

```csharp
void OnMainPageLoaded(object sender, RoutedEventArgs e)
{
    this.Frame.SizeChanged += (o, args) =>
    {
        if (noteEntryPage != null)
            noteEntryPage.Layout(new Xamarin.Forms.Rectangle(0, 0, args.NewSize.Width, args.NewSize.Height));
        else
            notesPage.Layout(new Xamarin.Forms.Rectangle(0, 0, args.NewSize.Width, args.NewSize.Height));
    };
}
```

`OnMainPageLoaded`方法會註冊事件的匿名事件處理常式 `Frame.SizeChanged` ，當 `ActualHeight` 或的 `ActualWidth` 屬性在上變更時，就會引發 `Frame` 此事件。 在回應中， Xamarin.Forms 會藉由呼叫方法來調整使用中頁面的內容 `Layout` 。

### <a name="enable-back-navigation-support"></a>啟用回溯導覽支援

在 UWP 上，應用程式必須針對不同的裝置外型規格，啟用所有硬體和軟體上一頁按鈕的回溯導覽。 註冊事件的事件處理常式即可完成這項作業 `BackRequested` ，可在此函式中執行 `MainPage` ：

```csharp
public MainPage()
{
    // ...
    SystemNavigationManager.GetForCurrentView().BackRequested += OnBackRequested;
}
```

當應用程式啟動時，方法會抓取 `GetForCurrentView` `SystemNavigationManager` 與目前視圖相關聯的物件，然後註冊事件的事件處理常式 `BackRequested` 。 應用程式只會收到此事件（如果它是前景應用程式），而在回應中，會呼叫 `OnBackRequested` 事件處理常式：

```csharp
void OnBackRequested(object sender, BackRequestedEventArgs e)
{
    Frame rootFrame = Window.Current.Content as Frame;
    if (rootFrame.CanGoBack)
    {
        e.Handled = true;
        rootFrame.GoBack();
        noteEntryPage = null;
    }
}
```

`OnBackRequested`事件處理常式會 `GoBack` 在應用程式的根框架上呼叫方法，並將 `BackRequestedEventArgs.Handled` 屬性設定為，以將事件標示為已 `true` 處理。 如果無法將事件標示為已處理，可能會導致事件被忽略。

應用程式會選擇是否要在標題列上顯示 [上一頁] 按鈕。 將 `AppViewBackButtonVisibility` 屬性設定為類別中的其中一個 `AppViewBackButtonVisibility` 列舉值，即可達成此目的 `App` ：

```csharp
void OnNavigated(object sender, NavigationEventArgs e)
{
    SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility =
        ((Frame)sender).CanGoBack ? AppViewBackButtonVisibility.Visible : AppViewBackButtonVisibility.Collapsed;
}
```

`OnNavigated`事件處理常式是為了回應引發的 `Navigated` 事件而執行，會在進行頁面導覽時，更新標題列 [上一頁] 按鈕的可見度。 這可確保在應用程式內的後端堆疊不是空的時，可以看到 [標題列] [上一頁] 按鈕，如果應用程式內的後端堆疊是空的，則會從標題列中移除。

如需有關 UWP 的回溯導覽支援的詳細資訊，請參閱[uwp 應用程式的導覽歷程記錄和向後導覽](/windows/uwp/design/basics/navigation-history-and-backwards-navigation/)。

## <a name="related-links"></a>相關連結

- [NativeForms （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/native2forms)
- [原生檢視](~/xamarin-forms/platform/native-views/index.md)
