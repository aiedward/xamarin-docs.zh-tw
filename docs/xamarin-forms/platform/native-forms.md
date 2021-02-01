---
title: Xamarin.Forms 在 Xamarin 原生專案中
description: 本文說明如何使用直接新增至 Xamarin 原生專案的 ContentPage 衍生頁面，以及如何在這些頁面之間流覽。
ms.prod: xamarin
ms.assetid: f343fc21-dfb1-4364-a332-9da6705d36bc
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/01/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6d17f20babd11a58540306f146ee9dc2bf1a5da4
ms.sourcegitcommit: 9ab5a1e346e20f54e8b7aa655fd3d117b43978cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2021
ms.locfileid: "99223551"
---
# <a name="no-locxamarinforms-in-xamarin-native-projects"></a>Xamarin.Forms 在 Xamarin 原生專案中

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/native2forms)

一般而言， Xamarin.Forms 應用程式會包含一或多個衍生自的頁面 [`ContentPage`](xref:Xamarin.Forms.ContentPage) ，而且這些頁面會由 .NET Standard 程式庫專案或共用專案中的所有平臺共用。 不過，原生表單可讓 `ContentPage` 衍生的頁面直接新增至原生 Xamarin. iOS、Xamarin 和 UWP 應用程式。 相較于讓原生專案 `ContentPage` 從 .NET Standard 程式庫專案或共用專案取用衍生的頁面，將頁面直接新增至原生專案的好處是可以使用原生視圖擴充頁面。 然後可以在 XAML 中以 XAML 命名原生視圖 `x:Name` ，並從程式碼後端參考。 如需原生視圖的詳細資訊，請參閱 [原生視圖](~/xamarin-forms/platform/native-views/index.md)。

Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) 在原生專案中使用衍生頁面的程式如下所示：

1. 將 Xamarin.Forms NuGet 套件新增至原生專案。
1. 將 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 衍生的網頁和任何相依性加入至原生專案。
1. 呼叫 `Forms.Init` 方法。
1. [`ContentPage`](xref:Xamarin.Forms.ContentPage)使用下列其中一個擴充方法來建立衍生頁面的實例，並將它轉換成適當的原生類型： `CreateViewController` 適用于 IOS、 `CreateSupportFragment` Android 或 `CreateFrameworkElement` UWP。
1. 使用原生流覽 API，流覽至衍生頁面的原生類型標記法 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 。

Xamarin.Forms 在 `Forms.Init` 原生專案可以建立衍生的頁面之前，必須先呼叫方法來初始化 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 。 選擇要執行這項作業的時機，主要取決於應用程式流程中最方便的時間-它可以在應用程式啟動時執行，或是在建立衍生的頁面之前執行 `ContentPage` 。 在本文和隨附的範例應用程式中， `Forms.Init` 會在應用程式啟動時呼叫此方法。

> [!NOTE]
> **NativeForms** 範例應用程式解決方案未包含任何 Xamarin.Forms 專案。 相反地，它是由 Xamarin 專案、Xamarin. Android 專案和 UWP 專案所組成。 每個專案都是使用原生表單來取用衍生頁面的原生專案 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 。 不過，原生專案無法 `ContentPage` 從 .NET Standard 程式庫專案或共用專案取用衍生的頁面，因此沒有原因。

使用原生表單時， Xamarin.Forms [`DependencyService`](xref:Xamarin.Forms.DependencyService) 、和資料系結引擎等功能仍可 [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) 正常運作。 不過，您必須使用原生流覽 API 來執行頁面流覽。

## <a name="ios"></a>iOS

在 iOS 上， `FinishedLaunching` 類別中的覆寫 `AppDelegate` 通常是執行應用程式啟動相關工作的位置。 它會在應用程式啟動後呼叫，通常會覆寫以設定主視窗和 view controller。 下列程式碼範例顯示 `AppDelegate` 範例應用程式中的類別：

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

        // Create app-level resource dictionary.
        Xamarin.Forms.Application.Current = new Xamarin.Forms.Application();
        Xamarin.Forms.Application.Current.Resources = new MyDictionary();

        Instance = this;
        _window = new UIWindow(UIScreen.MainScreen.Bounds);

        UINavigationBar.Appearance.SetTitleTextAttributes(new UITextAttributes
        {
            TextColor = UIColor.Black
        });

        FolderPath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData));

        NotesPage notesPage = new NotesPage()
        {
            // Set the parent so that the app-level resource dictionary can be located.
            Parent = Xamarin.Forms.Application.Current
        };

        UIViewController notesPageController = notesPage.CreateViewController();
        notesPageController.Title = "Notes";

        _navigation = new AppNavigationController(notesPageController);

        _window.RootViewController = _navigation;
        _window.MakeKeyAndVisible();

        notesPage.Parent = null;
        return true;
    }
    // ...
}
```

`FinishedLaunching` 方法會執行下列工作：

- Xamarin.Forms 藉由呼叫方法來初始化 `Forms.Init` 。
- 建立新的 `Xamarin.Forms.Application` 物件，並將其應用層級資源字典設定為 `ResourceDictionary` XAML 中定義的。
- 類別的參考 `AppDelegate` 會儲存在 `static` `Instance` 欄位中。 這是為了提供一個機制，讓其他類別呼叫類別中定義的方法 `AppDelegate` 。
- `UIWindow`會建立，這是原生 iOS 應用程式中 views 的主要容器。
- `FolderPath`屬性會初始化為裝置上儲存附注資料的路徑。
- 建立 `NotesPage` 物件，這是 Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) 在 XAML 中定義的衍生頁面，而且其父系設定為先前建立的 `Xamarin.Forms.Application` 物件。
- `NotesPage` `UIViewController` 使用擴充方法，將物件轉換為 `CreateViewController` 。
- `Title`已設定的屬性 `UIViewController` ，將顯示在上 `UINavigationBar` 。
- `AppNavigationController`建立以管理階層式流覽。 這是自訂的流覽控制器類別，其衍生自 `UINavigationController` 。 `AppNavigationController`物件會管理檢視控制器的堆疊，而 `UIViewController` 傳入的函式會在載入時，一開始就顯示 `AppNavigationController` 。
- `AppNavigationController`物件會設定為的最上層 `UIViewController` `UIWindow` ，而且 `UIWindow` 會設定為應用程式的索引鍵視窗，而且會成為可見的。
- `Parent`物件的屬性 `NotesPage` 設定為 `null` ，以避免記憶體流失。

一旦 `FinishedLaunching` 執行方法之後，就會顯示類別中定義的 UI Xamarin.Forms `NotesPage` ，如下列螢幕擷取畫面所示：

[![螢幕擷取畫面：顯示行動裝置上的附注畫面。](native-forms-images/ios-notespage.png "使用 XAML UI 的 Xamarin iOS 應用程式")](native-forms-images/ios-notespage-large.png#lightbox "使用 XAML UI 的 Xamarin iOS 應用程式")

> [!IMPORTANT]
> 所有 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 衍生的頁面都可以使用應用層級中定義的資源 `ResourceDictionary` ，但前提 `Parent` 是該頁面的屬性已設定為 `Application` 物件。

與 UI 互動（例如藉由點擊 **+** [`Button`](xref:Xamarin.Forms.Button) ），會導致程式碼後端中的下列事件處理常式 `NotesPage` 執行：

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    AppDelegate.Instance.NavigateToNoteEntryPage(new Note());
}
```

欄位可讓您叫用 `static` `AppDelegate.Instance` `AppDelegate.NavigateToNoteEntryPage` 方法，如下列程式碼範例所示：

```csharp
public void NavigateToNoteEntryPage(Note note)
{
    NoteEntryPage noteEntryPage = new NoteEntryPage
    {
        BindingContext = note,
        // Set the parent so that the app-level resource dictionary can be located.
        Parent = Xamarin.Forms.Application.Current
    };

    var noteEntryViewController = noteEntryPage.CreateViewController();
    noteEntryViewController.Title = "Note Entry";

    _navigation.PushViewController(noteEntryViewController, true);
    noteEntryPage.Parent = null;
}
```

`NavigateToNoteEntryPage`方法會 Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) 使用擴充方法將衍生的頁面轉換成 `UIViewController` `CreateViewController` ，並設定的 `Title` 屬性 `UIViewController` 。 `UIViewController`然後，方法會推送至 `AppNavigationController` `PushViewController` 。 因此，會顯示類別中定義的 UI Xamarin.Forms `NoteEntryPage` ，如下列螢幕擷取畫面所示：

[![螢幕擷取畫面：顯示行動裝置上的附注專案。](native-forms-images/ios-noteentrypage.png "使用 XAML UI 的 Xamarin iOS 應用程式")](native-forms-images/ios-noteentrypage-large.png#lightbox "使用 XAML UI 的 Xamarin iOS 應用程式")

當 `NoteEntryPage` 顯示時，後端流覽將會 `UIViewController` 從取出類別的，將使用者傳 `NoteEntryPage` `AppNavigationController` 回到類別的 `UIViewController` `NotesPage` 。 不過， `UIViewController` 從 iOS 原生導覽堆疊中取出，並不會自動處置 `UIViewController` 和附加的 `Page` 物件。 因此， `AppNavigationController` 類別會覆寫 `PopViewController` 方法，以處置回溯導覽上的 view 控制器：

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

覆 `PopViewController` 寫 `Dispose` `UIViewController` 會在從 iOS 原生導覽堆疊中快顯的物件上呼叫方法。 若未這樣做，將會導致 `UIViewController` 和附加 `Page` 物件變成孤立。

> [!IMPORTANT]
> 孤立的物件無法進行垃圾收集，因此會導致記憶體流失。

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

        // Create app-level resource dictionary.
        Xamarin.Forms.Application.Current = new Xamarin.Forms.Application();
        Xamarin.Forms.Application.Current.Resources = new MyDictionary();

        Instance = this;

        SetContentView(Resource.Layout.Main);
        var toolbar = FindViewById<Toolbar>(Resource.Id.toolbar);
        SetSupportActionBar(toolbar);
        SupportActionBar.Title = "Notes";

        FolderPath = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.LocalApplicationData));

        NotesPage notesPage = new NotesPage()
        {
            // Set the parent so that the app-level resource dictionary can be located.
            Parent = Xamarin.Forms.Application.Current
        };
        AndroidX.Fragment.App.Fragment notesPageFragment = notesPage.CreateSupportFragment(this);

        SupportFragmentManager
            .BeginTransaction()
            .Replace(Resource.Id.fragment_frame_layout, mainPage)
            .Commit();
        //...

        notesPage.Parent = null;
    }
    ...
}
```

`OnCreate` 方法會執行下列工作：

- Xamarin.Forms 藉由呼叫方法來初始化 `Forms.Init` 。
- 建立新的 `Xamarin.Forms.Application` 物件，並將其應用層級資源字典設定為 `ResourceDictionary` XAML 中定義的。
- 類別的參考 `MainActivity` 會儲存在 `static` `Instance` 欄位中。 這是為了提供一個機制，讓其他類別呼叫類別中定義的方法 `MainActivity` 。
- `Activity`內容是從版面配置資源設定。 在範例應用程式中，版面配置是由 `LinearLayout` 包含的 `Toolbar` ，以及 `FrameLayout` 做為片段容器的。
- `Toolbar`會取出並設定為的動作列 `Activity` ，並設定動作列標題。
- `FolderPath`屬性會初始化為裝置上儲存附注資料的路徑。
- 建立 `NotesPage` 物件，這是 Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) 在 XAML 中定義的衍生頁面，而且其父系設定為先前建立的 `Xamarin.Forms.Application` 物件。
- `NotesPage` `Fragment` 使用擴充方法，將物件轉換為 `CreateSupportFragment` 。
- `SupportFragmentManager`類別會建立並認可將 `FrameLayout` 實例取代為類別之的交易 `Fragment` `NotesPage` 。
- `Parent`物件的屬性 `NotesPage` 設定為 `null` ，以避免記憶體流失。

如需片段的詳細資訊，請參閱 [片段](~/android/platform/fragments/index.md)。

一旦 `OnCreate` 執行方法之後，就會顯示類別中定義的 UI Xamarin.Forms `NotesPage` ，如下列螢幕擷取畫面所示：

[![螢幕擷取畫面：在行動裝置上顯示具有藍色橫幅和彩色備註文字的附注畫面。](native-forms-images/android-notespage.png "具有 XAML UI 的 Xamarin Android 應用程式")](native-forms-images/android-notespage-large.png#lightbox "具有 XAML UI 的 Xamarin Android 應用程式")

> [!IMPORTANT]
> 所有 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 衍生的頁面都可以使用應用層級中定義的資源 `ResourceDictionary` ，但前提 `Parent` 是該頁面的屬性已設定為 `Application` 物件。

與 UI 互動（例如藉由點擊 **+** [`Button`](xref:Xamarin.Forms.Button) ），會導致程式碼後端中的下列事件處理常式 `NotesPage` 執行：

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    MainActivity.Instance.NavigateToNoteEntryPage(new Note());
}
```

欄位可讓您叫用 `static` `MainActivity.Instance` `MainActivity.NavigateToNoteEntryPage` 方法，如下列程式碼範例所示：

```csharp
public void NavigateToNoteEntryPage(Note note)
{
    NoteEntryPage noteEntryPage = new NoteEntryPage
    {
        BindingContext = note,
        // Set the parent so that the app-level resource dictionary can be located.
        Parent = Xamarin.Forms.Application.Current
    };

    AndroidX.Fragment.App.Fragment noteEntryFragment = noteEntryPage.CreateSupportFragment(this);
    SupportFragmentManager
        .BeginTransaction()
        .AddToBackStack(null)
        .Replace(Resource.Id.fragment_frame_layout, noteEntryFragment)
        .Commit();

    noteEntryPage.Parent = null;
}
```

`NavigateToNoteEntryPage`方法會 Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) 使用擴充方法將衍生的頁面轉換成 `Fragment` `CreateSupportFragment` ，並將加入 `Fragment` 至片段的堆疊。 因此，將會顯示中定義的 UI Xamarin.Forms `NoteEntryPage` ，如下列螢幕擷取畫面所示：

[![螢幕擷取畫面顯示行動裝置上具有藍色橫幅的附注專案。](native-forms-images/android-noteentrypage.png "具有 XAML UI 的 Xamarin Android 應用程式")](native-forms-images/android-noteentrypage-large.png#lightbox "具有 XAML UI 的 Xamarin Android 應用程式")

當 `NoteEntryPage` 顯示時，按 [上一步] 箭號將會 `Fragment` 從片段的堆疊中取出，並將使用者傳回到 `NoteEntryPage` `Fragment` 類別的 `NotesPage` 。

### <a name="enable-back-navigation-support"></a>啟用回溯流覽支援

`SupportFragmentManager`類別有一個 `BackStackChanged` 事件，會在片段的內容變更時引發。 `OnCreate`類別中的方法 `MainActivity` 包含此事件的匿名事件處理常式：

```csharp
SupportFragmentManager.BackStackChanged += (sender, e) =>
{
    bool hasBack = SupportFragmentManager.BackStackEntryCount > 0;
    SupportActionBar.SetHomeButtonEnabled(hasBack);
    SupportActionBar.SetDisplayHomeAsUpEnabled(hasBack);
    SupportActionBar.Title = hasBack ? "Note Entry" : "Notes";
};
```

此事件處理常式會在動作列上顯示 [上一頁] 按鈕，指出片段上有一個或多個 `Fragment` 實例在堆疊上。 覆寫會處理點擊 [上一頁] 按鈕的回應 `OnOptionsItemSelected` ：

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

`OnOptionsItemSelected`只要選取 [選項] 功能表中的專案，就會呼叫覆寫。 如果已選取 [上一頁] 按鈕，而且片段上有一個或多個實例，則此實作為會從 [傳回] 堆疊中取出目前的片段 `Fragment` 。

### <a name="multiple-activities"></a>多個活動

當應用程式由多個活動組成時， [`ContentPage`](xref:Xamarin.Forms.ContentPage) 可以將衍生的頁面內嵌至每個活動。 在此案例中， `Forms.Init` 只需要在第一個內嵌的覆寫中呼叫方法 `OnCreate` `Activity` Xamarin.Forms `ContentPage` 。 不過，這會造成下列影響：

- 的值 `Xamarin.Forms.Color.Accent` 會從呼叫方法的取得 `Activity` `Forms.Init` 。
- 的值 `Xamarin.Forms.Application.Current` 會與呼叫方法的相關聯 `Activity` `Forms.Init` 。

### <a name="choose-a-file"></a>選擇檔案

內嵌 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 使用 [`WebView`](xref:Xamarin.Forms.WebView) 需要支援 HTML [選擇檔案] 按鈕的衍生頁面時， `Activity` 將需要覆寫 `OnActivityResult` 方法：

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    ActivityResultCallbackRegistry.InvokeCallback(requestCode, resultCode, data);
}
```

## <a name="uwp"></a>UWP

在 UWP 上，原生 `App` 類別通常是執行應用程式啟動相關工作的地方。 Xamarin.Forms 在 UWP 應用程式中，通常會在 Xamarin.Forms 原生類別的覆寫中初始化， `OnLaunched` 以將 `App` `LaunchActivatedEventArgs` 引數傳遞給 `Forms.Init` 方法。 基於這個理由，使用衍生頁面的原生 UWP 應用程式很 Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) 容易就能 `Forms.Init` 從方法呼叫方法 `App.OnLaunched` ：

```csharp
protected override void OnLaunched(LaunchActivatedEventArgs e)
{
    // ...
    Xamarin.Forms.Forms.Init(e);

    // Create app-level resource dictionary.
    Xamarin.Forms.Application.Current = new Xamarin.Forms.Application();
    Xamarin.Forms.Application.Current.Resources = new MyDictionary();

    // ...
}
```

此外，此 `OnLaunched` 方法也可以建立應用程式所需的任何應用層級資源字典。

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
        this.NavigationCacheMode = NavigationCacheMode.Enabled;
        Instance = this;
        FolderPath = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.LocalApplicationData));

        notesPage = new Notes.UWP.Views.NotesPage
        {
            // Set the parent so that the app-level resource dictionary can be located.
            Parent = Xamarin.Forms.Application.Current
        };
        this.Content = notesPage.CreateFrameworkElement();
        // ...
        notesPage.Parent = null;    
    }
    // ...
}
```

此函式會 `MainPage` 執行下列工作：

- 頁面會啟用快取，因此 `MainPage` 當使用者流覽回頁面時，不會建立新的。
- 類別的參考 `MainPage` 會儲存在 `static` `Instance` 欄位中。 這是為了提供一個機制，讓其他類別呼叫類別中定義的方法 `MainPage` 。
- `FolderPath`屬性會初始化為裝置上儲存附注資料的路徑。
- 建立 `NotesPage` 物件，這是 Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) 在 XAML 中定義的衍生頁面，而且其父系設定為先前建立的 `Xamarin.Forms.Application` 物件。
- `NotesPage` `FrameworkElement` 使用擴充方法，將物件轉換成 `CreateFrameworkElement` ，然後將它設定為類別的內容 `MainPage` 。
- `Parent`物件的屬性 `NotesPage` 設定為 `null` ，以避免記憶體流失。

一旦執行了函式 `MainPage` ，就會顯示類別中定義的 UI Xamarin.Forms `NotesPage` ，如下列螢幕擷取畫面所示：

[![螢幕擷取畫面顯示包含附注和日期/時間的附注頁面。](native-forms-images/uwp-notespage.png "具有：：： no loc (Xamarin 的 UWP 應用程式) ：：： XAML UI")](native-forms-images/uwp-notespage-large.png#lightbox "具有：：： no loc (Xamarin 的 UWP 應用程式) ：：： XAML UI")

> [!IMPORTANT]
> 所有 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 衍生的頁面都可以使用應用層級中定義的資源 `ResourceDictionary` ，但前提 `Parent` 是該頁面的屬性已設定為 `Application` 物件。

與 UI 互動（例如藉由點擊 **+** [`Button`](xref:Xamarin.Forms.Button) ），會導致程式碼後端中的下列事件處理常式 `NotesPage` 執行：

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    MainPage.Instance.NavigateToNoteEntryPage(new Note());
}
```

欄位可讓您叫用 `static` `MainPage.Instance` `MainPage.NavigateToNoteEntryPage` 方法，如下列程式碼範例所示：

```csharp
public void NavigateToNoteEntryPage(Note note)
{
    noteEntryPage = new Notes.UWP.Views.NoteEntryPage
    {
        BindingContext = note,
        // Set the parent so that the app-level resource dictionary can be located.
        Parent = Xamarin.Forms.Application.Current
    };
    this.Frame.Navigate(noteEntryPage);
    noteEntryPage.Parent = null;
}
```

UWP 中的導覽通常是使用 `Frame.Navigate` 方法（採用引數）來執行 `Page` 。 Xamarin.Forms 定義 `Frame.Navigate` 採用 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 衍生頁面實例的擴充方法。 因此，當 `NavigateToNoteEntryPage` 方法執行時，中所定義的 UI Xamarin.Forms `NoteEntryPage` 將會顯示，如下列螢幕擷取畫面所示：

[![螢幕擷取畫面顯示附注頁面，其中包含已輸入附注的文字方塊。](native-forms-images/uwp-noteentrypage.png "具有：：： no loc (Xamarin 的 UWP 應用程式) ：：： XAML UI")](native-forms-images/uwp-noteentrypage-large.png#lightbox "具有：：： no loc (Xamarin 的 UWP 應用程式) ：：： XAML UI")

當 `NoteEntryPage` 顯示時，按 [上一步] 箭號將會 `FrameworkElement` `NoteEntryPage` 從應用程式後端堆疊中取出，並將使用者傳回到 `FrameworkElement` 類別的 `NotesPage` 。

### <a name="enable-page-resizing-support"></a>啟用頁面大小調整支援

調整 UWP 應用程式視窗的大小時， Xamarin.Forms 也應調整內容的大小。 在函式中註冊事件的事件處理常式，即可完成此作業 `Loaded` `MainPage` ：

```csharp
public MainPage()
{
    // ...
    this.Loaded += OnMainPageLoaded;
    // ...
}
```

當頁面配置、轉譯 `Loaded` 和準備進行互動時，就會引發此事件，並 `OnMainPageLoaded` 在回應中執行方法：

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

`OnMainPageLoaded`方法會註冊事件的匿名事件處理常式 `Frame.SizeChanged` ，當 `ActualHeight` 上的或屬性變更時，就會引發此事件 `ActualWidth` `Frame` 。 在回應中，使用中 Xamarin.Forms 頁面的內容會藉由呼叫方法來調整大小 `Layout` 。

### <a name="enable-back-navigation-support"></a>啟用回溯流覽支援

在 UWP 上，應用程式必須在不同的裝置外型規格中啟用所有硬體和軟體上一頁按鈕的回溯導覽。 您可以註冊事件的事件處理常式來完成這 `BackRequested` 項作業，這可以在函式中執行 `MainPage` ：

```csharp
public MainPage()
{
    // ...
    SystemNavigationManager.GetForCurrentView().BackRequested += OnBackRequested;
}
```

當應用程式啟動時，方法會抓取 `GetForCurrentView` `SystemNavigationManager` 與目前視圖相關聯的物件，然後註冊事件的事件處理常式 `BackRequested` 。 應用程式只會在它是前景應用程式時接收此事件，而在回應中則會呼叫 `OnBackRequested` 事件處理常式：

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

`OnBackRequested`事件處理常式會 `GoBack` 在應用程式的根框架上呼叫方法，並將 `BackRequestedEventArgs.Handled` 屬性設定為， `true` 以將事件標示為已處理。 若無法將事件標示為已處理，可能會導致事件被忽略。

應用程式會選擇是否要在標題列上顯示上一頁按鈕。 這是藉由將 `AppViewBackButtonVisibility` 屬性設定為類別中的其中一個 `AppViewBackButtonVisibility` 列舉值來達成 `App` ：

```csharp
void OnNavigated(object sender, NavigationEventArgs e)
{
    SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility =
        ((Frame)sender).CanGoBack ? AppViewBackButtonVisibility.Visible : AppViewBackButtonVisibility.Collapsed;
}
```

`OnNavigated`事件處理常式（為了回應 `Navigated` 事件引發而執行）會在進行頁面流覽時，更新標題列 [上一頁] 按鈕的可見度。 這可確保當應用程式內送堆疊不是空的，或從標題列中移除的標題列 [上一頁] 按鈕是空的。

如需 UWP 的上一頁導覽支援詳細資訊，請參閱 [uwp 應用程式的流覽歷程記錄和回溯導覽](/windows/uwp/design/basics/navigation-history-and-backwards-navigation/)。

## <a name="related-links"></a>相關連結

- [NativeForms (範例) ](/samples/xamarin/xamarin-forms-samples/native2forms)
- [原生檢視](~/xamarin-forms/platform/native-views/index.md)
