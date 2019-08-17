---
title: 在 Xamarin 原生專案中的 Xamarin.Forms
description: 這篇文章說明如何使用 ContentPage 衍生頁面直接新增到 Xamarin 原生專案，以及如何之間瀏覽。
ms.prod: xamarin
ms.assetid: f343fc21-dfb1-4364-a332-9da6705d36bc
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/03/2019
ms.openlocfilehash: 0543d35b8bd4160aa84688da21dbc5bda5408444
ms.sourcegitcommit: 9912e57ff6124c583600f9460ebfa3f7f7525960
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69560305"
---
# <a name="xamarinforms-in-xamarin-native-projects"></a>在 Xamarin 原生專案中的 Xamarin.Forms

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/native2forms)

Xamarin.Forms 應用程式通常包含一或多個頁面衍生自[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)，，而這些頁面時，可共用所有平台上，.NET Standard 程式庫專案或共用專案中。 不過，原生格式可讓`ContentPage`-衍生直接加入原生的 Xamarin.iOS、 Xamarin.Android 和 UWP 應用程式的頁面。 相較於具有原生專案取用`ContentPage`-衍生的頁面，從.NET Standard 程式庫專案或共用專案中，直接將頁面加入原生專案中的優點是頁面可以使用原生檢視來延伸。 可以再使用 XAML 中名為原生檢視`x:Name`和參考從程式碼後置。 如需有關原生檢視的詳細資訊，請參閱[原生檢視](~/xamarin-forms/platform/native-views/index.md)。

使用 Xamarin.Forms 的程序[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-衍生原生專案中的頁面如下所示：

1. 加入原生專案中的 Xamarin.Forms NuGet 套件。
1. 新增[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-衍生頁面上，以及任何相依，原生專案。
1. 呼叫 `Forms.Init` 方法。
1. 建構的執行個體[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-衍生頁面，並將它轉換成適當的原生類型，使用下列的擴充方法的其中一個：`CreateViewController`適用於 iOS、`CreateSupportFragment`適用於 Android、 或`CreateFrameworkElement`的UWP。
1. 瀏覽至的原生類型表示法[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-衍生使用原生的瀏覽 API 的頁面。

必須由呼叫初始化 Xamarin.Forms`Forms.Init`方法之前原生專案可以建構[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-衍生頁面。 當它是最方便的方式在您的應用程式流程中，選擇時若要這樣做主要是取決於 – 它可以在應用程式啟動時，或執行之前`ContentPage`-建構衍生的頁面。 在本文中，並隨附的範例應用程式，`Forms.Init`應用程式啟動時呼叫方法。

> [!NOTE]
> **NativeForms**範例應用程式方案不包含所有的 Xamarin.Forms 專案。 相反地，它包含在 Xamarin.iOS 專案、 的 Xamarin.Android 專案和 UWP 專案。 每個專案是原生專案使用原生格式來取用[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-衍生的頁面。 不過，沒有的理由為何要使用原生專案無法`ContentPage`-來自.NET Standard 程式庫專案或共用專案中的頁面。

當使用原生格式，Xamarin.Forms 這類功能[ `DependencyService` ](xref:Xamarin.Forms.DependencyService)， [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter)，和資料繫結引擎，所有工作。 不過，您必須使用原生的瀏覽 API 會執行頁面導覽。

## <a name="ios"></a>iOS

在 iOS 上，`FinishedLaunching`覆寫中`AppDelegate`類別通常是執行應用程式的位置啟動相關的工作。 它會呼叫後的應用程式已啟動，通常會覆寫來設定主視窗和檢視控制器。 下列程式碼範例示範`AppDelegate`範例應用程式中的類別：

```csharp
[Register("AppDelegate")]
public class AppDelegate : UIApplicationDelegate
{
    public static string FolderPath { get; private set; }

    public static AppDelegate Instance;

    UIWindow _window;
    UINavigationController _navigation;
    UIViewController _noteEntryPage;

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

        _navigation = new UINavigationController(mainPage);
        _window.RootViewController = _navigation;
        _window.MakeKeyAndVisible();

        return true;
    }
    ...
}
```

`FinishedLaunching`方法會執行下列工作：

- Xamarin.Forms 會藉由呼叫初始化`Forms.Init`方法。
- 參考`AppDelegate`類別會儲存在`static``Instance`欄位。 這是為了提供機制來呼叫方法中定義其他類別`AppDelegate`類別。
- `UIWindow`，這原生的 iOS 應用程式中檢視的主要容器建立。
- `FolderPath`屬性會初始化為裝置上儲存附注資料的路徑。
- `NotesPage`類別，這是 Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-衍生頁面中 XAML 所定義、 建構及轉換成`UIViewController`使用`CreateViewController`擴充方法。
- `Title`的屬性`UIViewController`設定，這將會顯示在`UINavigationBar`。
- A`UINavigationController`建立來管理階層式導覽。 `UINavigationController`類別會管理堆疊的檢視控制器，而`UIViewController`傳遞至建構函式將會看到一開始時`UINavigationController`載入。
- `UINavigationController`執行個體已設定為最上層`UIViewController`for `UIWindow`，和`UIWindow`被設定為應用程式的 [金鑰] 視窗，就會顯示。

一次`FinishedLaunching`已經執行方法，在 Xamarin.Forms 中的 UI 定義`NotesPage`，也將會顯示類別，如下列螢幕擷取畫面所示：

[ ![Xamarin ios 應用程式的螢幕擷取畫面, 其使用 Xaml](native-forms-images/ios-notespage.png "Xamarin ios 應用程式")中定義的 ui 與 xaml ui](native-forms-images/ios-notespage-large.png#lightbox "包含 XAML UI 的 Xamarin iOS 應用程式")

例如, 藉由在上 **+** [`Button`](xref:Xamarin.Forms.Button)使用來與 UI 互動, 會導致程式`NotesPage`代碼後置中的下列事件處理常式執行:

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    AppDelegate.Instance.NavigateToNoteEntryPage(new Note());
}
```

`static` `AppDelegate.Instance`欄位可讓`AppDelegate.NavigateToNoteEntryPage`要叫用方法，下列程式碼範例所示：

```csharp
public void NavigateToNoteEntryPage(Note note)
{
    _noteEntryPage = new NoteEntryPage
    {
        BindingContext = note
    }.CreateViewController();
    _noteEntryPage.Title = "Note Entry";
    _navigation.PushViewController(_noteEntryPage, true);
}
```

`NavigateToNoteEntryPage`方法會將轉換的 Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-衍生頁面，即可`UIViewController`具有`CreateViewController`擴充方法，並將`Title`屬性`UIViewController`。 `UIViewController`再推入至`UINavigationController`由`PushViewController`方法。 因此，UI 定義在 Xamarin.Forms 中`NoteEntryPage`，也將會顯示類別，如下列螢幕擷取畫面所示：

[ ![Xamarin ios 應用程式的螢幕擷取畫面, 其使用 Xaml](native-forms-images/ios-noteentrypage.png "Xamarin ios 應用程式")中定義的 ui 與 xaml ui](native-forms-images/ios-noteentrypage-large.png#lightbox "包含 XAML UI 的 Xamarin iOS 應用程式")

時`NoteEntryPage`出現時，點選 [上一步] 箭號將會快顯`UIViewController`如`NoteEntryPage`類別`UINavigationController`，傳回使用者`UIViewController`的`NotesPage`類別。

> [!IMPORTANT]
> 從 iOS `UIViewController`原生導覽堆疊中取出, 並不會自動`UIViewController`處置 s。 開發人員必須負責確保`UIViewController`任何不再需要的方法都呼叫它`Dispose` , 否則和附加`Page`的`UIViewController`將會孤立, 且不會被垃圾收集行程收集。導致記憶體流失。

## <a name="android"></a>Android

在 Android 上，`OnCreate`覆寫中`MainActivity`類別通常是執行應用程式的位置啟動相關的工作。 下列程式碼範例示範`MainActivity`範例應用程式中的類別：

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

`OnCreate`方法會執行下列工作：

- Xamarin.Forms 會藉由呼叫初始化`Forms.Init`方法。
- 參考`MainActivity`類別會儲存在`static``Instance`欄位。 這是為了提供機制來呼叫方法中定義其他類別`MainActivity`類別。
- `Activity`內容由版面配置資源設定。 在範例應用程式版面配置包含`LinearLayout`，其中包含`Toolbar`，和`FrameLayout`做為片段的容器。
- `Toolbar`擷取和設定的動作列為`Activity`，而且會設定動作列標題。
- `FolderPath`屬性會初始化為裝置上儲存附注資料的路徑。
- `NotesPage`類別，這是 Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-衍生頁面中 XAML 所定義、 建構及轉換成`Fragment`使用`CreateSupportFragment`擴充方法。
- `SupportFragmentManager`類別會建立並認可交易，它會取代`FrameLayout`執行個體`Fragment`如`NotesPage`類別。

如需有關片段的詳細資訊，請參閱[片段](~/android/platform/fragments/index.md)。

一次`OnCreate`已經執行方法，在 Xamarin.Forms 中的 UI 定義`NotesPage`，也將會顯示類別，如下列螢幕擷取畫面所示：

[ ![Xamarin. android 應用程式的螢幕擷取畫面, 其使用 xaml](native-forms-images/android-notespage.png "Xamarin. android 應用程式")中定義的 ui 與 xaml UI](native-forms-images/android-notespage-large.png#lightbox "包含 XAML UI 的 Xamarin Android 應用程式")

例如, 藉由在上 **+** [`Button`](xref:Xamarin.Forms.Button)使用來與 UI 互動, 會導致程式`NotesPage`代碼後置中的下列事件處理常式執行:

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    MainActivity.Instance.NavigateToNoteEntryPage(new Note());
}
```

`static` `MainActivity.Instance`欄位可讓`MainActivity.NavigateToNoteEntryPage`要叫用方法，下列程式碼範例所示：

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

`NavigateToNoteEntryPage`方法會將轉換的 Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-衍生頁面，即可`Fragment`具有`CreateSupportFragment`擴充方法，並將`Fragment`片段回堆疊。 因此，UI 定義在 Xamarin.Forms 中`NoteEntryPage`將會顯示，如下列螢幕擷取畫面所示：

[ ![Xamarin. android 應用程式的螢幕擷取畫面, 其使用 xaml](native-forms-images/android-noteentrypage.png "Xamarin. android 應用程式")中定義的 ui 與 xaml UI](native-forms-images/android-noteentrypage-large.png#lightbox "包含 XAML UI 的 Xamarin Android 應用程式")

時`NoteEntryPage`出現時，點選 [上一步] 箭號會出現`Fragment`for`NoteEntryPage`片段上一頁堆疊中，從傳回使用者`Fragment`的`NotesPage`類別。

### <a name="enable-back-navigation-support"></a>啟用回溯導覽支援

`SupportFragmentManager`類別具有`BackStackChanged`片段上一頁堆疊的內容變更時，就會引發的事件。 `OnCreate`方法中的`MainActivity`類別包含此事件的匿名事件處理常式：

```csharp
SupportFragmentManager.BackStackChanged += (sender, e) =>
{
    bool hasBack = SupportFragmentManager.BackStackEntryCount > 0;
    SupportActionBar.SetHomeButtonEnabled(hasBack);
    SupportActionBar.SetDisplayHomeAsUpEnabled(hasBack);
    SupportActionBar.Title = hasBack ? "Note Entry" : "Notes";
};
```

這個事件處理常式會顯示動作列上一步按鈕假設有一或多個`Fragment`片段上的執行個體傳回堆疊。 點選 [上一頁] 按鈕的回應由`OnOptionsItemSelected`覆寫：

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

`OnOptionsItemSelected`選取 [選項] 功能表中的項目時，會呼叫覆寫。 此實作中取出片段上一頁堆疊中，從目前的片段，前提是已選取 [上一頁] 按鈕，而且有一或多個`Fragment`片段上的執行個體傳回堆疊。

### <a name="multiple-activities"></a>多個活動

當應用程式由多個活動，組成[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-衍生的頁面可以內嵌在每個活動。 在此案例中，`Forms.Init`需要呼叫方法，只有`OnCreate`的第一個覆寫`Activity`，內嵌 Xamarin.Forms `ContentPage`。 不過，這有下列影響：

- 值`Xamarin.Forms.Color.Accent`將會取自`Activity`呼叫`Forms.Init`方法。
- 值`Xamarin.Forms.Application.Current`相關聯`Activity`呼叫`Forms.Init`方法。

### <a name="choose-a-file"></a>選擇檔案

內嵌時[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-衍生使用的頁面[ `WebView` ](xref:Xamarin.Forms.WebView) ，需要支援 HTML [選擇檔案] 按鈕，則`Activity`需要覆寫`OnActivityResult`方法：

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    ActivityResultCallbackRegistry.InvokeCallback(requestCode, resultCode, data);
}
```

## <a name="uwp"></a>UWP

在 UWP、 原生`App`類別通常是執行應用程式的位置啟動相關的工作。 Xamarin.Forms 通常，在 Xamarin.Forms UWP 應用程式中初始化`OnLaunched`覆寫在原生`App`類別，以傳遞`LaunchActivatedEventArgs`引數`Forms.Init`方法。 基於這個理由，原生 UWP 應用程式使用 Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-衍生的頁面可以最容易呼叫`Forms.Init`方法，從`App.OnLaunched`方法。

根據預設，原生`App`類別會啟動`MainPage`應用程式的第一頁為類別。 下列程式碼範例示範`MainPage`範例應用程式中的類別：

```csharp
public sealed partial class MainPage : Page
{
    public static MainPage Instance;

    public static string FolderPath { get; private set; }

    public MainPage()
    {
        this.InitializeComponent();
        this.NavigationCacheMode = NavigationCacheMode.Enabled;
        Instance = this;
        FolderPath = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.LocalApplicationData));
        this.Content = new Notes.UWP.Views.NotesPage().CreateFrameworkElement();
    }
    ...
}
```

`MainPage`建構函式會執行下列工作：

- 啟用快取 頁面上，以便新`MainPage`當使用者瀏覽回頁面時不會建構。
- 參考`MainPage`類別會儲存在`static``Instance`欄位。 這是為了提供機制來呼叫方法中定義其他類別`MainPage`類別。
- `FolderPath`屬性會初始化為裝置上儲存附注資料的路徑。
- `NotesPage`類別，這是 Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-衍生頁面中 XAML 所定義、 建構及轉換成`FrameworkElement`使用`CreateFrameworkElement`擴充方法，並再將設定的內容`MainPage`類別。

一次`MainPage`建構函式已執行，在 Xamarin.Forms 中的 UI 定義`NotesPage`，也將會顯示類別，如下列螢幕擷取畫面所示：

[ ![UWP 應用程式的螢幕擷取畫面, 其使用以 xamarin. 表單 xaml](native-forms-images/uwp-notespage.png "UWP 應用程式定義的 UI 與 xamarin xaml UI") ](native-forms-images/uwp-notespage-large.png#lightbox "具有 Xamarin 的 UWP 應用程式 XAML UI")

例如, 藉由在上 **+** [`Button`](xref:Xamarin.Forms.Button)使用來與 UI 互動, 會導致程式`NotesPage`代碼後置中的下列事件處理常式執行:

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    MainPage.Instance.NavigateToNoteEntryPage(new Note());
}
```

`static` `MainPage.Instance`欄位可讓`MainPage.NavigateToNoteEntryPage`要叫用方法，下列程式碼範例所示：

```csharp
public void NavigateToNoteEntryPage(Note note)
{
    this.Frame.Navigate(new NoteEntryPage
    {
        BindingContext = note
    });
}
```

瀏覽 UWP 中的通常會使用`Frame.Navigate`方法，後者會採用`Page`引數。 Xamarin.Forms 可定義`Frame.Navigate`擴充方法採用[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-衍生 page 執行個體。 因此，當`NavigateToNoteEntryPage`執行的方法時，在 Xamarin.Forms 中定義的 UI`NoteEntryPage`將會顯示，如下列螢幕擷取畫面所示：

[ ![UWP 應用程式的螢幕擷取畫面, 其使用以 xamarin. 表單 xaml](native-forms-images/uwp-noteentrypage.png "UWP 應用程式定義的 UI 與 xamarin xaml UI") ](native-forms-images/uwp-noteentrypage-large.png#lightbox "具有 Xamarin 的 UWP 應用程式 XAML UI")

時`NoteEntryPage`出現時，點選 [上一步] 箭號將會快顯`FrameworkElement`for`NoteEntryPage`從應用程式內上一頁堆疊，傳回使用者`FrameworkElement`的`NotesPage`類別。

### <a name="enable-back-navigation-support"></a>啟用回溯導覽支援

在 UWP 應用程式必須啟用所有硬體和軟體上一頁按鈕，向後的巡覽，跨不同裝置外型規格。 這可藉由註冊的事件處理常式`BackRequested`事件，就可以執行`OnLaunched`方法，在原生`App`類別：

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

啟動應用程式時，`GetForCurrentView`方法擷取`SystemNavigationManager`物件與目前檢視中，相關聯，則註冊事件處理常式`BackRequested`事件。 應用程式只會收到這個事件，如果它是前景應用程式，並在回應中，呼叫`OnBackRequested`事件處理常式：

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

`OnBackRequested`事件處理常式會呼叫`GoBack`方法上的應用程式和集合的根框架`BackRequestedEventArgs.Handled`屬性設`true`來標示為已處理的事件。 如果無法將事件標示為已處理, 可能會導致事件被忽略。

應用程式會選擇是否要在標題列上顯示 [上一頁] 按鈕。 這藉由設定來達成`AppViewBackButtonVisibility`屬性設為其中的`AppViewBackButtonVisibility`列舉值：

```csharp
void OnNavigated(object sender, NavigationEventArgs e)
{
    SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility =
        ((Frame)sender).CanGoBack ? AppViewBackButtonVisibility.Visible : AppViewBackButtonVisibility.Collapsed;
}
```

`OnNavigated`事件處理常式，會在回應中執行`Navigated`事件的引發，更新上一頁按鈕列標題的可見性，當發生頁面巡覽。 這可確保標題列的 [上一頁] 按鈕時，會顯示應用程式內上一頁堆疊不是空的或移除的標題列中，如果應用程式內上一頁堆疊是空的。

如需 UWP 向後巡覽支援的相關詳細資訊，請參閱[瀏覽歷程記錄及向後的 UWP 應用程式瀏覽](/windows/uwp/design/basics/navigation-history-and-backwards-navigation/)。

## <a name="related-links"></a>相關連結

- [NativeForms （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/native2forms)
- [原生檢視](~/xamarin-forms/platform/native-views/index.md)
