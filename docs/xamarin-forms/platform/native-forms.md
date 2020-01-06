---
title: Xamarin 原生專案中的 xamarin
description: 本文說明如何取用直接新增至 Xamarin 原生專案的 ContentPage 衍生頁面，以及如何在兩者之間流覽。
ms.prod: xamarin
ms.assetid: f343fc21-dfb1-4364-a332-9da6705d36bc
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/19/2019
ms.openlocfilehash: 83b445b8379ad5181ab1dce142cca06625be79ad
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75487330"
---
# <a name="xamarinforms-in-xamarin-native-projects"></a>Xamarin 原生專案中的 xamarin

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/native2forms)

一般而言，Xamarin. Forms 應用程式包含一或多個衍生自[`ContentPage`](xref:Xamarin.Forms.ContentPage)的頁面，而這些頁面會由 .NET Standard 程式庫專案或共用專案中的所有平臺共用。 不過，原生表單可讓 `ContentPage`衍生的頁面直接新增至原生的 Xamarin. iOS、Xamarin 和 UWP 應用程式。 相較于讓原生專案取用來自 .NET Standard 程式庫專案或共用專案的 `ContentPage`衍生頁面，將頁面直接加入至原生專案的優點是可以使用原生視圖來擴充頁面。 然後可以在 XAML 中以 `x:Name` 命名原生視圖，並從程式碼後置參考。 如需原生視圖的詳細資訊，請參閱[原生視圖](~/xamarin-forms/platform/native-views/index.md)。

在原生專案中使用 Xamarin. Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage)衍生頁面的程式如下所示：

1. 將 [Xamarin] NuGet 封裝新增至原生專案。
1. 將 [ [`ContentPage`](xref:Xamarin.Forms.ContentPage)衍生的頁面] 和 [任何相依性] 加入至原生專案。
1. 呼叫 `Forms.Init` 方法。
1. 使用下列其中一個擴充方法來建立[`ContentPage`](xref:Xamarin.Forms.ContentPage)衍生頁面的實例，並將它轉換成適當的原生類型： iOS 的 `CreateViewController`、適用于 Android 的 `CreateSupportFragment`，或適用于 UWP 的 `CreateFrameworkElement`。
1. 使用原生導覽 API，流覽至[`ContentPage`](xref:Xamarin.Forms.ContentPage)衍生頁面的原生類型標記法。

在原生專案可以建立[`ContentPage`](xref:Xamarin.Forms.ContentPage)衍生的頁面之前，您必須先呼叫 `Forms.Init` 方法來初始化表單。 選擇何時執行此動作主要取決於應用程式流程中最方便的時機–它可以在應用程式啟動時執行，或是在建立 `ContentPage`衍生的頁面之前進行。 在本文中，和隨附的範例應用程式中，會在應用程式啟動時呼叫 `Forms.Init` 方法。

> [!NOTE]
> **NativeForms**範例應用程式方案不包含任何 Xamarin 專案。 相反地，它是由一個 Xamarin 專案、一個 Xamarin Android 專案和一個 UWP 專案所組成。 每個專案都是原生專案，會使用原生表單取用[`ContentPage`](xref:Xamarin.Forms.ContentPage)衍生的頁面。 不過，原生專案無法從 .NET Standard 程式庫專案或共用專案取用 `ContentPage`衍生頁面的原因。

使用原生表單時，像是[`DependencyService`](xref:Xamarin.Forms.DependencyService)、 [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter)和資料系結引擎等表單功能，都仍然有效。 不過，頁面導覽必須使用原生導覽 API 來執行。

## <a name="ios"></a>iOS

在 iOS 上，`AppDelegate` 類別中的 `FinishedLaunching` 覆寫通常是執行應用程式啟動相關工作的位置。 它會在應用程式啟動後呼叫，而且通常會覆寫以設定主視窗和視圖控制器。 下列程式碼範例顯示範例應用程式中的 `AppDelegate` 類別：

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

- [Xamarin] 是藉由呼叫 `Forms.Init` 方法來初始化。
- `AppDelegate` 類別的參考會儲存在 [`static` `Instance`] 欄位中。 這是為了讓其他類別能夠呼叫 `AppDelegate` 類別中定義的方法。
- `UIWindow`，這是在原生 iOS 應用程式中建立視圖的主要容器。
- `FolderPath` 屬性會初始化為裝置上儲存附注資料的路徑。
- `NotesPage` 類別，這是在 XAML 中定義的 Xamarin [`ContentPage`](xref:Xamarin.Forms.ContentPage)衍生頁面，會使用 `CreateViewController` 擴充方法來進行結構化並轉換成 `UIViewController`。
- 已設定 `UIViewController` 的 `Title` 屬性，這會顯示在 `UINavigationBar`上。
- 建立 `AppNavigationController` 以管理階層式導覽。 這是自訂的流覽控制器類別，其衍生自 `UINavigationController`。 `AppNavigationController` 物件會管理一堆視圖控制器，而傳遞至此函式的 `UIViewController` 會在載入 `AppNavigationController` 時一開始呈現。
- `AppNavigationController` 物件會設定為 `UIWindow`的頂層 `UIViewController`，而 `UIWindow` 會設定為應用程式的索引鍵視窗，而且會顯示為可見。

一旦執行 `FinishedLaunching` 方法，就會顯示在 Xamarin. Forms `NotesPage` 類別中定義的 UI，如下列螢幕擷取畫面所示：

[![使用 XAML 中定義的 UI 之 Xamarin iOS 應用程式的螢幕擷取畫面](native-forms-images/ios-notespage.png "包含 XAML UI 的 Xamarin iOS 應用程式")](native-forms-images/ios-notespage-large.png#lightbox "包含 XAML UI 的 Xamarin iOS 應用程式")

例如，藉由在 **+** [`Button`](xref:Xamarin.Forms.Button)上進行互動，就會在執行的 `NotesPage` 程式碼後置中產生下列事件處理常式：

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    AppDelegate.Instance.NavigateToNoteEntryPage(new Note());
}
```

[`static` `AppDelegate.Instance`] 欄位可讓您叫用 `AppDelegate.NavigateToNoteEntryPage` 方法，如下列程式碼範例所示：

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

`NavigateToNoteEntryPage` 方法會將 Xamarin [`ContentPage`](xref:Xamarin.Forms.ContentPage)衍生的頁面，轉換為具有 `CreateViewController` 擴充方法的 `UIViewController`，並設定 `Title` 的 `UIViewController`屬性。 然後，`UIViewController` 會透過 `PushViewController` 方法推送至 `AppNavigationController`。 因此，將會顯示在 Xamarin. Forms `NoteEntryPage` 類別中定義的 UI，如下列螢幕擷取畫面所示：

[![使用 XAML 中定義的 UI 之 Xamarin iOS 應用程式的螢幕擷取畫面](native-forms-images/ios-noteentrypage.png "包含 XAML UI 的 Xamarin iOS 應用程式")](native-forms-images/ios-noteentrypage-large.png#lightbox "包含 XAML UI 的 Xamarin iOS 應用程式")

顯示 `NoteEntryPage` 時，[上一頁流覽] 將會從 `AppNavigationController`中快顯 `NoteEntryPage` 類別的 `UIViewController`，並將使用者傳回 `UIViewController` 類別的 `NotesPage`。 不過，從 iOS 原生導覽堆疊彈出 `UIViewController` 並不會自動處置 `UIViewController` 並附加 `Page` 物件。 因此，`AppNavigationController` 類別會覆寫 `PopViewController` 方法，以在回溯導覽時處置視圖控制器：

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

`PopViewController` 覆寫會針對從 iOS 原生導覽堆疊彈出的 `UIViewController` 物件，呼叫 `Dispose` 方法。 如果無法這麼做，將會導致 `UIViewController` 和附加的 `Page` 物件被孤立。

> [!IMPORTANT]
> 孤立物件無法進行垃圾收集，因而導致記憶體流失。

## <a name="android"></a>Android

在 Android 上，`MainActivity` 類別中的 `OnCreate` 覆寫通常是執行應用程式啟動相關工作的位置。 下列程式碼範例顯示範例應用程式中的 `MainActivity` 類別：

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

- [Xamarin] 是藉由呼叫 `Forms.Init` 方法來初始化。
- `MainActivity` 類別的參考會儲存在 [`static` `Instance`] 欄位中。 這是為了讓其他類別能夠呼叫 `MainActivity` 類別中定義的方法。
- `Activity` 內容是從配置資源進行設定。 在範例應用程式中，配置是由包含 `Toolbar`的 `LinearLayout`，以及做為片段容器的 `FrameLayout` 所組成。
- 會抓取 `Toolbar`，並將其設定為 `Activity`的動作列，並設定動作列標題。
- `FolderPath` 屬性會初始化為裝置上儲存附注資料的路徑。
- `NotesPage` 類別，這是在 XAML 中定義的 Xamarin [`ContentPage`](xref:Xamarin.Forms.ContentPage)衍生頁面，會使用 `CreateSupportFragment` 擴充方法來進行結構化並轉換成 `Fragment`。
- `SupportFragmentManager` 類別會建立並認可交易，以 `NotesPage` 類別的 `Fragment` 取代 `FrameLayout` 實例。

如需有關片段的詳細資訊，請參閱[片段](~/android/platform/fragments/index.md)。

一旦執行 `OnCreate` 方法，就會顯示在 Xamarin. Forms `NotesPage` 類別中定義的 UI，如下列螢幕擷取畫面所示：

[![使用 XAML 中定義之 UI 的 Xamarin Android 應用程式螢幕擷取畫面](native-forms-images/android-notespage.png "包含 XAML UI 的 Xamarin Android 應用程式")](native-forms-images/android-notespage-large.png#lightbox "包含 XAML UI 的 Xamarin Android 應用程式")

例如，藉由在 **+** [`Button`](xref:Xamarin.Forms.Button)上進行互動，就會在執行的 `NotesPage` 程式碼後置中產生下列事件處理常式：

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    MainActivity.Instance.NavigateToNoteEntryPage(new Note());
}
```

[`static` `MainActivity.Instance`] 欄位可讓您叫用 `MainActivity.NavigateToNoteEntryPage` 方法，如下列程式碼範例所示：

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

`NavigateToNoteEntryPage` 方法會將 Xamarin [`ContentPage`](xref:Xamarin.Forms.ContentPage)衍生的頁面，轉換為具有 `CreateSupportFragment` 擴充方法的 `Fragment`，並將 `Fragment` 加入至片段後端堆疊。 因此，會顯示在 [Xamarin. 表單] `NoteEntryPage` 中定義的 UI，如下列螢幕擷取畫面所示：

[![使用 XAML 中定義之 UI 的 Xamarin Android 應用程式螢幕擷取畫面](native-forms-images/android-noteentrypage.png "包含 XAML UI 的 Xamarin Android 應用程式")](native-forms-images/android-noteentrypage-large.png#lightbox "包含 XAML UI 的 Xamarin Android 應用程式")

顯示 `NoteEntryPage` 時，按 [上一步] 箭號將會從片段後堆疊中快顯 `NoteEntryPage` 的 `Fragment`，並將使用者傳回 `NotesPage` 類別的 `Fragment`。

### <a name="enable-back-navigation-support"></a>啟用回溯導覽支援

`SupportFragmentManager` 類別具有 `BackStackChanged` 事件，會在每次片段傳回堆疊的內容變更時引發。 `MainActivity` 類別中的 `OnCreate` 方法包含此事件的匿名事件處理常式：

```csharp
SupportFragmentManager.BackStackChanged += (sender, e) =>
{
    bool hasBack = SupportFragmentManager.BackStackEntryCount > 0;
    SupportActionBar.SetHomeButtonEnabled(hasBack);
    SupportActionBar.SetDisplayHomeAsUpEnabled(hasBack);
    SupportActionBar.Title = hasBack ? "Note Entry" : "Notes";
};
```

這個事件處理常式會在動作列上顯示 [上一頁] 按鈕，前提是該片段上有一個或多個 `Fragment` 實例。 點擊 [上一頁] 按鈕的回應是由 `OnOptionsItemSelected` 覆寫來處理：

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

每當選取 [選項] 功能表中的專案時，就會呼叫 `OnOptionsItemSelected` 覆寫。 這個程式會從片段的傳回堆疊中取出目前的片段，但前提是已選取 [上一頁] 按鈕，而在片段上有一個或多個 `Fragment` 實例。

### <a name="multiple-activities"></a>多個活動

當應用程式由多個活動組成時， [`ContentPage`](xref:Xamarin.Forms.ContentPage)衍生的頁面就可以內嵌到每個活動中。 在此案例中，只有在內嵌 Xamarin. 表單 `ContentPage`的第一個 `Activity` 的 `OnCreate` 覆寫中，才需要呼叫 `Forms.Init` 方法。 不過，這會造成下列影響：

- `Xamarin.Forms.Color.Accent` 的值將取自呼叫 `Forms.Init` 方法的 `Activity`。
- `Xamarin.Forms.Application.Current` 的值將會與呼叫 `Forms.Init` 方法的 `Activity` 相關聯。

### <a name="choose-a-file"></a>選擇檔案

當內嵌的[`ContentPage`](xref:Xamarin.Forms.ContentPage)衍生頁面使用需要支援 HTML [選擇檔案] 按鈕的[`WebView`](xref:Xamarin.Forms.WebView)時，`Activity` 將需要覆寫 `OnActivityResult` 方法：

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    ActivityResultCallbackRegistry.InvokeCallback(requestCode, resultCode, data);
}
```

## <a name="uwp"></a>UWP

在 UWP 上，原生 `App` 類別通常是執行應用程式啟動相關工作的位置。 Xamarin 通常會在 Xamarin. Forms UWP 應用程式中，于原生 `App` 類別的 `OnLaunched` 覆寫中初始化，以便將 `LaunchActivatedEventArgs` 引數傳遞至 `Forms.Init` 方法。 基於這個理由，使用 Xamarin [`ContentPage`](xref:Xamarin.Forms.ContentPage)衍生頁面的原生 UWP 應用程式，最容易就能從 `App.OnLaunched` 方法呼叫 `Forms.Init` 方法。

根據預設，native `App` 類別會啟動 `MainPage` 類別，做為應用程式的第一頁。 下列程式碼範例顯示範例應用程式中的 `MainPage` 類別：

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

`MainPage` 的構造函式會執行下列工作：

- 已啟用頁面的快取，因此當使用者流覽回到頁面時，不會建立新的 `MainPage`。
- `MainPage` 類別的參考會儲存在 [`static` `Instance`] 欄位中。 這是為了讓其他類別能夠呼叫 `MainPage` 類別中定義的方法。
- `FolderPath` 屬性會初始化為裝置上儲存附注資料的路徑。
- `NotesPage` 類別是 XAML 中定義的 Xamarin [`ContentPage`](xref:Xamarin.Forms.ContentPage)衍生頁面，會使用 `CreateFrameworkElement` 擴充方法來進行結構化並轉換成 `FrameworkElement`，然後將設定為 `MainPage` 類別的內容。

執行 `MainPage` 的函式之後，將會顯示在 [Xamarin] `NotesPage` 類別中定義的 UI，如下列螢幕擷取畫面所示：

[![UWP 應用程式的螢幕擷取畫面，其使用以 Xamarin 定義的 UI。表單 XAML](native-forms-images/uwp-notespage.png "具有 Xamarin 的 UWP 應用程式 XAML UI")](native-forms-images/uwp-notespage-large.png#lightbox "具有 Xamarin 的 UWP 應用程式 XAML UI")

例如，藉由在 **+** [`Button`](xref:Xamarin.Forms.Button)上進行互動，就會在執行的 `NotesPage` 程式碼後置中產生下列事件處理常式：

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    MainPage.Instance.NavigateToNoteEntryPage(new Note());
}
```

[`static` `MainPage.Instance`] 欄位可讓您叫用 `MainPage.NavigateToNoteEntryPage` 方法，如下列程式碼範例所示：

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

UWP 中的導覽通常會使用 `Frame.Navigate` 方法來執行，而此方法會採用 `Page` 引數。 Xamarin 會定義採用[`ContentPage`](xref:Xamarin.Forms.ContentPage)衍生頁面實例的 `Frame.Navigate` 擴充方法。 因此，當 `NavigateToNoteEntryPage` 方法執行時，會顯示在 [Xamarin] 表單 `NoteEntryPage` 中定義的 UI，如下列螢幕擷取畫面所示：

[![UWP 應用程式的螢幕擷取畫面，其使用以 Xamarin 定義的 UI。表單 XAML](native-forms-images/uwp-noteentrypage.png "具有 Xamarin 的 UWP 應用程式 XAML UI")](native-forms-images/uwp-noteentrypage-large.png#lightbox "具有 Xamarin 的 UWP 應用程式 XAML UI")

顯示 `NoteEntryPage` 時，按 [上一步] 箭號將會從應用程式內的後置堆疊中快顯 `NoteEntryPage` 的 `FrameworkElement`，並將使用者傳回 `NotesPage` 類別的 `FrameworkElement`。

### <a name="enable-page-resizing-support"></a>啟用頁面調整大小支援

調整 UWP 應用程式視窗的大小時，也應該調整 [Xamarin] 內容的大小。 這是藉由在 `MainPage` 的函式中，為 `Loaded` 事件註冊事件處理常式來完成：

```csharp
public MainPage()
{
    // ...
    this.Loaded += OnMainPageLoaded;
    // ...
}
```

當頁面已配置、呈現並準備好進行互動時，`Loaded` 事件就會引發，並執行 `OnMainPageLoaded` 方法以回應：

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

`OnMainPageLoaded` 方法會註冊 `Frame.SizeChanged` 事件的匿名事件處理常式，當 `ActualHeight` 或 `ActualWidth` 屬性在 `Frame`上變更時，就會引發此事件。 在回應中，會藉由呼叫 `Layout` 方法來調整使用中頁面的 Xamarin 內容大小。

### <a name="enable-back-navigation-support"></a>啟用回溯導覽支援

在 UWP 上，應用程式必須針對不同的裝置外型規格，啟用所有硬體和軟體上一頁按鈕的回溯導覽。 註冊 `BackRequested` 事件的事件處理常式即可完成這項作業，您可以在 `MainPage` 的函式中執行此作業：

```csharp
public MainPage()
{
    // ...
    SystemNavigationManager.GetForCurrentView().BackRequested += OnBackRequested;
}
```

當應用程式啟動時，`GetForCurrentView` 方法會抓取與目前 view 關聯的 `SystemNavigationManager` 物件，然後註冊 `BackRequested` 事件的事件處理常式。 應用程式只會收到此事件（如果它是前景應用程式），而在回應中，會呼叫 `OnBackRequested` 事件處理常式：

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

`OnBackRequested` 事件處理常式會在應用程式的根框架上呼叫 `GoBack` 方法，並將 `BackRequestedEventArgs.Handled` 屬性設定為 `true`，以將事件標示為已處理。 如果無法將事件標示為已處理，可能會導致事件被忽略。

應用程式會選擇是否要在標題列上顯示 [上一頁] 按鈕。 將 `AppViewBackButtonVisibility` 屬性設定為 `App` 類別中的其中一個 `AppViewBackButtonVisibility` 列舉值，即可達成此目的：

```csharp
void OnNavigated(object sender, NavigationEventArgs e)
{
    SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility =
        ((Frame)sender).CanGoBack ? AppViewBackButtonVisibility.Visible : AppViewBackButtonVisibility.Collapsed;
}
```

`OnNavigated` 事件處理常式（為了回應 `Navigated` 事件引發而執行），會在進行頁面導覽時，更新標題列 [上一頁] 按鈕的可見度。 這可確保在應用程式內的後端堆疊不是空的時，可以看到 [標題列] [上一頁] 按鈕，如果應用程式內的後端堆疊是空的，則會從標題列中移除。

如需有關 UWP 的回溯導覽支援的詳細資訊，請參閱[uwp 應用程式的導覽歷程記錄和向後導覽](/windows/uwp/design/basics/navigation-history-and-backwards-navigation/)。

## <a name="related-links"></a>相關連結

- [NativeForms （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/native2forms)
- [原生檢視](~/xamarin-forms/platform/native-views/index.md)
