---
title: Xamarin. 表單快速入門深入探討
description: 本文會檢查使用 Xamarin.Forms 開發應用程式的基本概念。 涵蓋的主題包含 Xamarin.Forms 應用程式的結構、架構和應用程式基本概念，以及使用者介面。
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.custom: video
ms.assetid: 7B2340A1-6883-41D8-860C-0BB6C4E0C316
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/27/2018
ms.openlocfilehash: 3936fe16ee768505c53ec119c51dcbecef7e6fbe
ms.sourcegitcommit: efbc69acf4ea484d8815311b058114379c9db8a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2019
ms.locfileid: "73842961"
---
# <a name="xamarinforms-quickstart-deep-dive"></a>Xamarin. 表單快速入門深入探討

在 [ [Xamarin] 快速入門](~/get-started/index.yml)中，已建立 Notes 應用程式。 本文會檢閱已經建立的項目，以了解 Xamarin.Forms 應用程式運作方式的基本概念。

::: zone pivot="windows"

## <a name="introduction-to-visual-studio"></a>Visual Studio 簡介

Visual Studio 會將程式碼組織成「方案」和「專案」。 方案是可以容納一或多個專案的容器。 專案可以是應用程式、支援程式庫、測試應用程式等等。 Notes 應用程式是由一個包含四個專案的方案所組成，如下列螢幕擷取畫面所示：

![](deepdive-images/vs/solution.png "Visual Studio Solution Explorer")

這些專案包括：

- 附注–此專案是包含所有共用程式碼和共用 UI 的 .NET Standard 程式庫專案。
- 記事：此專案包含 Android 專用的程式碼，而且是 Android 應用程式的進入點。
- 記事-此專案包含 iOS 專屬的程式碼，而且是 iOS 應用程式的進入點。
- 記事：此專案包含通用 Windows 平臺（UWP）的特定程式碼，而且是 UWP 應用程式的進入點。

## <a name="anatomy-of-a-xamarinforms-application"></a>Xamarin. Forms 應用程式的結構

下列螢幕擷取畫面顯示 Visual Studio 中 Notes .NET Standard 程式庫專案的內容：

![](deepdive-images/vs/net-standard-project.png "Phoneword .NET Standard Project Contents")

此專案具有**相依性**節點，其中包含 **NuGet** 和 **SDK** 節點：

- **Nuget** &ndash; 已新增至專案的 [Xamarin] 和 [sqlite-net-Pcl] nuget 套件。
- **SDK** &ndash; `NETStandard.Library` 中繼套件會參考定義 .NET Standard 的一組完整 NuGet 套件。

::: zone-end
::: zone pivot="macos"

## <a name="introduction-to-visual-studio-for-mac"></a>Visual Studio for Mac 簡介

[Visual Studio for Mac](/visualstudio/mac/) 遵循 Visual Studio 的做法，將程式碼組織成「方案」和「專案」。 方案是可以容納一或多個專案的容器。 專案可以是應用程式、支援程式庫、測試應用程式等等。 Notes 應用程式是由一個包含三個專案的方案所組成，如下列螢幕擷取畫面所示：

![](deepdive-images/vsmac/solution.png "Visual Studio for Mac Solution Pane")

這些專案包括：

- 附注–此專案是包含所有共用程式碼和共用 UI 的 .NET Standard 程式庫專案。
- 記事-此專案包含 Android 專用程式碼，而且是 Android 應用程式的進入點。
- 記事-此專案包含 iOS 特定程式碼，而且是 iOS 應用程式的進入點。

## <a name="anatomy-of-a-xamarinforms-application"></a>Xamarin. Forms 應用程式的結構

下列螢幕擷取畫面顯示 Visual Studio for Mac 中 Notes .NET Standard 程式庫專案的內容：

![](deepdive-images/vsmac/net-standard-project.png "Phoneword .NET Standard Library Project Contents")

此專案具有**相依性**節點，其中包含 **NuGet** 和 **SDK** 節點：

- **Nuget** &ndash; 已新增至專案的 [Xamarin] 和 [sqlite-net-Pcl] nuget 套件。
- **SDK** &ndash; `NETStandard.Library` 中繼套件會參考定義 .NET Standard 的一組完整 NuGet 套件。

::: zone-end

此專案也包含多個檔案：

- **Data\NoteDatabase.cs** –此類別包含用來建立資料庫、從中讀取資料、將資料寫入其中，以及刪除資料的程式碼。
- **Models\Note.cs** –這個類別會定義一個 `Note` 模型，其實例會儲存應用程式中每個便箋的相關資料。
- **App.xaml** - `App` 類別的 XAML 標記，可定義應用程式的資源字典。
- **App.xaml.cs** - `App` 類別的程式碼後置，負責將應用程式在每個平台上顯示的第一個頁面初始化，以及處理應用程式生命週期事件。
- **AssemblyInfo.cs** –此檔案包含專案的相關應用程式屬性，該專案會在元件層級套用。
- **NotesPage** -`NotesPage` 類別的 xaml 標記，可定義應用程式啟動時所顯示之頁面的 UI。
- **NotesPage.xaml.cs** – `NotesPage` 類別的程式碼後置，其中包含使用者與頁面互動時所執行的商務邏輯。
- **NoteEntryPage** -`NoteEntryPage` 類別的 xaml 標記，可定義使用者輸入便箋時所顯示之頁面的 UI。
- **NoteEntryPage.xaml.cs** – `NoteEntryPage` 類別的程式碼後置，其中包含使用者與頁面互動時所執行的商務邏輯。

如需有關 Xamarin.iOS 應用程式結構的詳細資訊，請參閱 [Xamarin.iOS 應用程式的結構](~/ios/get-started/hello-ios/hello-ios-deepdive.md#anatomy-of-a-xamarinios-application)。 如需有關 Xamarin.Android 應用程式結構的詳細資訊，請參閱 [Xamarin.Android 應用程式的結構](~/android/get-started/hello-android/hello-android-deepdive.md#anatomy)。

## <a name="architecture-and-application-fundamentals"></a>架構和應用程式基本概念

Xamarin.Forms 應用程式的架構方式與傳統的跨平台應用程式相同。 共用程式碼通常放在 .NET Standard 程式庫中，而平台專用的應用程式則會取用共用程式碼。 下圖顯示 Notes 應用程式的這項關聯性總覽：

::: zone pivot="windows"

![](deepdive-images/vs/architecture.png "Notes Architecture")

::: zone-end
::: zone pivot="macos"

![](deepdive-images/vsmac/architecture.png "Notes Architecture")

::: zone-end

若要將啟動程式碼的重複使用發揮到極致，Xamarin.Forms 應用程式有一個名為 `App` 的單一類別，此類別負責在每個平台上具現化應用程式將顯示的第一頁，如下列程式碼範例所示：

```csharp
using Xamarin.Forms;

namespace Notes
{
    public partial class App : Application
    {
        public App()
        {
            InitializeComponent();
            MainPage = new NavigationPage(new NotesPage());
        }
        ...
    }
}
```

此程式碼會將 `App` 類別的 `MainPage` 屬性設定為內容為 `NotesPage` 實例的[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)實例。

此外， **AssemblyInfo.cs**檔案包含單一應用程式屬性，它會在元件層級套用：

```csharp
using Xamarin.Forms.Xaml;

[assembly: XamlCompilation(XamlCompilationOptions.Compile)]
```

[`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute)屬性會開啟 xaml 編譯器，以便將 xaml 直接編譯成中繼語言。 如需詳細資訊，請參閱 [XAML 編譯](~/xamarin-forms/xaml/xamlc.md)。

## <a name="launching-the-application-on-each-platform"></a>在每個平臺上啟動應用程式

### <a name="ios"></a>iOS

若要在 iOS 中啟動初始的 [Xamarin] 頁面，[記事] 專案會定義繼承自 `FormsApplicationDelegate` 類別的 `AppDelegate` 類別：

```csharp
namespace Notes.iOS
{
    [Register("AppDelegate")]
    public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate
    {
        public override bool FinishedLaunching(UIApplication app, NSDictionary options)
        {
            global::Xamarin.Forms.Forms.Init();
            LoadApplication(new App());
            return base.FinishedLaunching(app, options);
        }
    }
}
```

`FinishedLaunching` 覆寫會呼叫 `Init` 方法，藉此初始化 Xamarin.Forms 架構。 如此會在 `LoadApplication` 方法的呼叫設定根檢視控制器之前，先在應用程式中載入 iOS 專用的 Xamarin.Forms 實作。

### <a name="android"></a>Android

若要在 Android 中啟動初始的 [Xamarin] 頁面，請注意 Android 專案所包含的程式碼會建立具有 `MainLauncher` 屬性的 `Activity`，以及繼承自 `FormsAppCompatActivity` 類別的活動：

```csharp
namespace Notes.Droid
{
    [Activity(Label = "Notes",
              Icon = "@mipmap/icon",
              Theme = "@style/MainTheme",
              MainLauncher = true,
              ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
    {
        protected override void OnCreate(Bundle savedInstanceState)
        {
            TabLayoutResource = Resource.Layout.Tabbar;
            ToolbarResource = Resource.Layout.Toolbar;

            base.OnCreate(savedInstanceState);
            global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
            LoadApplication(new App());
        }
    }
}
```

`OnCreate` 覆寫會呼叫 `Init` 方法，藉此初始化 Xamarin.Forms 架構。 如此會在載入 Xamarin.Forms 應用程式之前，先在應用程式中載入 Android 專用的 Xamarin.Forms 實作。

::: zone pivot="windows"

### <a name="universal-windows-platform"></a>通用 Windows 平台

在通用 Windows 平台 (UWP) 應用程式中，初始化 Xamarin.Forms 架構的 `Init` 方法是從 `App` 類別叫用的：

```csharp
Xamarin.Forms.Forms.Init (e);

if (e.PreviousExecutionState == ApplicationExecutionState.Terminated)
{
  ...
}
```

如此會在應用程式中載入 UWP 專用的 Xamarin.Forms 實作。 初始的 [Xamarin] 頁面是由 `MainPage` 類別啟動：

```csharp
namespace Notes.UWP
{
    public sealed partial class MainPage
    {
        public MainPage()
        {
            this.InitializeComponent();
            this.LoadApplication(new Notes.App());
        }
    }
}
```

Xamarin.Forms 應用程式是以 `LoadApplication` 方法來載入。

> [!NOTE]
> 通用 Windows 平臺的應用程式可以使用 Xamarin 建立，但只能在 Windows 上使用 Visual Studio。

::: zone-end

## <a name="user-interface"></a>使用者介面

有四個主要的控制項群組可用來建立 Xamarin 應用程式的使用者介面：

1. **頁面** - Xamarin.Forms 頁面代表跨平台行動應用程式畫面。 Notes 應用程式會使用[`ContentPage`](xref:Xamarin.Forms.ContentPage)類別來顯示單一畫面。 如需有關頁面的詳細資訊，請參閱 [Xamarin.Forms 頁面](~/xamarin-forms/user-interface/controls/pages.md)。
1. **檢視** - Xamarin.Forms 檢視是顯示在使用者介面上的控制項，例如標籤、按鈕和文字輸入方塊。 完成的 Notes 應用程式會使用 [ [`ListView`](xref:Xamarin.Forms.ListView)]、[ [`Editor`](xref:Xamarin.Forms.Editor)] 和 [ [`Button`](xref:Xamarin.Forms.Button) ] views。 如需有關檢視的詳細資訊，請參閱 [Xamarin.Forms 檢視](~/xamarin-forms/user-interface/controls/views.md)。
1. **版面配置** - Xamarin.Forms 版面配置是將檢視構成邏輯結構所使用的容器。 Notes 應用程式會使用[`StackLayout`](xref:Xamarin.Forms.StackLayout)類別，在垂直堆疊中排列檢視，而[`Grid`](xref:Xamarin.Forms.Grid)類別則會以水準方式排列按鈕。 如需有關版面配置的詳細資訊，請參閱 [Xamarin.Forms 版面配置](~/xamarin-forms/user-interface/controls/layouts.md)。
1. **資料格** - Xamarin.Forms 資料格是在清單中用於項目的特定元素，並描述如何在清單中繪製每個項目。 Notes 應用程式會使用[`TextCell`](xref:Xamarin.Forms.TextCell) ，針對清單中的每個資料列顯示兩個專案。 如需有關資料格的詳細資訊，請參閱 [Xamarin.Forms 資料格](~/xamarin-forms/user-interface/controls/cells.md)。

在執行階段，每個控制項將會對應到其原生對等項目，也就是將呈現的項目。

### <a name="layout"></a>配置

Notes 應用程式會使用[`StackLayout`](xref:Xamarin.Forms.StackLayout)來簡化跨平臺應用程式開發，方法是在螢幕上自動排文視圖，而不論螢幕大小為何。 每個子項目都是以加入這些子項目的順序，一個接著一個地水平或垂直放置。 `StackLayout` 將使用的空間量將取決於設定 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 和 [`VerticalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 屬性的方式，但預設 `StackLayout` 會嘗試使用整個螢幕。

下列 XAML 程式碼顯示使用[`StackLayout`](xref:Xamarin.Forms.StackLayout)配置 `NoteEntryPage`的範例：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Notes.NoteEntryPage"
             Title="Note Entry">
    ...    
    <StackLayout Margin="{StaticResource PageMargin}">
        <Editor Placeholder="Enter your note"
                Text="{Binding Text}"
                HeightRequest="100" />
        <Grid>
            ...
        </Grid>
    </StackLayout>    
</ContentPage>
```

根據預設， [`StackLayout`](xref:Xamarin.Forms.StackLayout)會假設垂直方向。 不過，您可以將[`StackLayout.Orientation`](xref:Xamarin.Forms.StackLayout.Orientation)屬性設定為[`StackOrientation.Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal)列舉成員，將它變更為水準方向。

> [!NOTE]
> 您可以透過 [`HeightRequest`] 和 [`WidthRequest` 屬性] 來設定視圖的大小。

如需 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 類別的詳細資訊，請參閱 [StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md)。

### <a name="responding-to-user-interaction"></a>回應使用者互動

在 XAML 中定義的物件可以引發程式碼後置檔案中處理的事件。 下列程式碼範例會在 `NoteEntryPage` 類別的程式碼後置中顯示 `OnSaveButtonClicked` 方法，這是為了回應 [*儲存*] 按鈕上引發的[`Clicked`](xref:Xamarin.Forms.Button.Clicked)事件而執行。

```csharp
async void OnSaveButtonClicked(object sender, EventArgs e)
{
    var note = (Note)BindingContext;
    note.Date = DateTime.UtcNow;
    await App.Database.SaveNoteAsync(note);
    await Navigation.PopAsync();
}
```

`OnSaveButtonClicked` 方法會將便箋儲存在資料庫中，然後流覽回上一頁。

> [!NOTE]
> XAML 類別的程式碼後置檔案可以存取在 XAML 中使用獲指派名稱及 `x:Name` 屬性定義的物件。 指派給此屬性的值與 C# 變數具有相同的規則，因為該值的開頭必須是字母或底線，而且不得包含內嵌的空格。

[儲存] 按鈕到 `OnSaveButtonClicked` 方法的連接會出現在 `NoteEntryPage` 類別的 XAML 標記中：

```xaml
<Button Text="Save"
        Clicked="OnSaveButtonClicked" />
```

### <a name="lists"></a>清單

[`ListView`](xref:Xamarin.Forms.ListView)會負責以垂直方式顯示清單中的專案集合。 `ListView` 中的每個專案都會包含在單一資料格中。

下列程式碼範例顯示 `NotesPage`的[`ListView`](xref:Xamarin.Forms.ListView) ：

```xaml
<ListView x:Name="listView"
          Margin="{StaticResource PageMargin}"
          ItemSelected="OnListViewItemSelected">
    <ListView.ItemTemplate>
        <DataTemplate>
            <TextCell Text="{Binding Text}"
                      Detail="{Binding Date}" />
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

[`ListView`](xref:Xamarin.Forms.ListView)中每個資料列的配置都是在[`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate)元素內定義，並使用資料系結來顯示應用程式所取出的任何附注。 [`ListView.ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource)屬性會設定為數據源，`NotesPage.xaml.cs`：

```csharp
protected override async void OnAppearing()
{
    base.OnAppearing();

    listView.ItemsSource = await App.Database.GetNotesAsync();
}
```    

此程式碼會在[`ListView`](xref:Xamarin.Forms.ListView)中填入儲存在資料庫中的任何附注。

在[`ListView`](xref:Xamarin.Forms.ListView)中選取資料列時， [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected)事件就會引發。 當事件引發時，會執行名為 `OnListViewItemSelected`的事件處理常式：

```csharp
async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
{
    if (e.SelectedItem != null)
    {
        ...
    }
}
```

[`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected)事件可以透過[`e.SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem)屬性，存取與儲存格相關聯的物件。

如需[`ListView`](xref:Xamarin.Forms.ListView)類別的詳細資訊，請參閱[ListView](~/xamarin-forms/user-interface/listview/index.md)。

## <a name="navigation"></a>巡覽

Xamarin.Forms 會根據所使用的 [`Page`](xref:Xamarin.Forms.Page) 類型，提供多種不同的網頁導覽體驗。 針對[`ContentPage`](xref:Xamarin.Forms.ContentPage)實例導覽可以是階層式或模式。 如需強制回應導覽的詳細資訊，請參閱[Xamarin。表單](~/xamarin-forms/app-fundamentals/navigation/modal.md)強制回應頁面。

> [!NOTE]
> [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)、[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 和 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 類別會提供替代的導覽體驗。 如需詳細資訊，請參閱[導覽](~/xamarin-forms/app-fundamentals/navigation/index.md)。

在階層式導覽中，會使用[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)類別，視需要向前和向後流覽[`ContentPage`](xref:Xamarin.Forms.ContentPage)物件的堆疊。 此類別會實作一堆後進先出 (LIFO) 的 [`Page`](xref:Xamarin.Forms.Page) 物件導覽。 若要將一頁移到另一頁，應用程式會將新的頁面推送到導覽堆疊上，該頁面就會變成使用中的頁面。 若要返回到上一頁，應用程式將會從導覽堆疊中快顯目前的頁面，新的最上層頁面就會變成使用中的頁面。

`NavigationPage` 類別也會將巡覽列新增到顯示標題，以及返回上一頁之平台相應 [上一頁] 按鈕的頁面頂端。

新增至導覽堆疊的第一頁稱為應用程式的*根*頁面，而下列程式碼範例示範如何在 Notes 應用程式中完成這項作業：

```csharp
public App ()
{
    ...
    MainPage = new NavigationPage (new NotesPage ());
}
```

所有 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 執行個體都有一個可公開方法的 [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) 屬性，以修改頁面堆疊。 只有在應用程式包含 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 時，才會叫用這些方法。 若要導覽至 `NoteEntryPage`，必須叫用 [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync(Xamarin.Forms.Page)) 方法，如下列程式碼範例所示：

```csharp
await Navigation.PushAsync(new NoteEntryPage());
```

這會導致新的 `NoteEntryPage` 物件推送到導覽堆疊上，而它會變成使用中的頁面。

無論是裝置上的實體按鈕還是螢幕上的按鈕，按下裝置上的 [上一頁] 按鈕都可以從導覽堆疊快顯使用中的頁面。 若要以程式設計的方式返回到原始頁面，`NoteEntryPage` 物件必須叫用 [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) 方法，如下列程式碼範例所示：

```csharp
await Navigation.PopAsync();
```

如需有關階層式導覽的詳細資訊，請參閱[階層式導覽](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)。

## <a name="data-binding"></a>資料繫結

資料繫結用來簡化 Xamarin.Forms 應用程式顯示其資料以及與之互動的方式。 它會在使用者介面與基礎應用程式之間建立連線。 [`BindableObject`](xref:Xamarin.Forms.BindableObject) 類別包含許多支援資料繫結的基礎結構。

資料繫結會連接兩個物件，稱為*來源*和*目標*。 *來源*物件會提供資料。 *目標*物件將會取用 (而且通常會顯示) 來源物件中的資料。 例如， [`Editor`](xref:Xamarin.Forms.Editor) （*目標*物件）通常會將其[`Text`](xref:Xamarin.Forms.Editor.Text)屬性系結至*來源*物件中的公用 `string` 屬性。 下圖說明繫結關聯性：

![](deepdive-images/data-binding.png "Data Binding")

資料繫結的主要優點是您不再需要擔心檢視和資料來源之間的資料同步處理。 *來源*物件中的變更會在幕後，透過繫結架構自動推送至*目標*物件，而目標物件中的變更則可以選擇性地推送回*來源*物件。

建立資料系結是兩個步驟的程式：

- *目標*物件的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 屬性必須設定為*來源*。
- *目標*和*來源*之間必須建立繫結。 在 XAML 中，可使用 [`Binding`](xref:Xamarin.Forms.Xaml.BindingExtension) 標記延伸達到此目的。

在 Notes 應用程式中，系結目標是顯示附注的[`Editor`](xref:Xamarin.Forms.Editor) ，而 `Note` 實例設定為 `NoteEntryPage` 的[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)是系結來源。

`NoteEntryPage` 的 `BindingContext` 會在頁面導覽期間設定，如下列程式碼範例所示：

```csharp
async void OnNoteAddedClicked(object sender, EventArgs e)
{
    await Navigation.PushAsync(new NoteEntryPage
    {
        BindingContext = new Note()
    });
}

async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
{
    if (e.SelectedItem != null)
    {
        await Navigation.PushAsync(new NoteEntryPage
        {
            BindingContext = e.SelectedItem as Note
        });
    }
}
```

在將新的附注加入至應用程式時所執行的 `OnNoteAddedClicked` 方法中，`NoteEntryPage` 的[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)會設定為新的 `Note` 實例。 在[`ListView`](xref:Xamarin.Forms.ListView)中選取現有附注時執行的 `OnListViewItemSelected` 方法中，會將 `NoteEntryPage` 的 `BindingContext` 設定為選取的 `Note` 實例，這是透過[`e.SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem)屬性來存取。

> [!IMPORTANT]
> 每個*目標*物件的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 屬性都可以個別設定，但這並非必要。 `BindingContext` 為其所有子系繼承的特殊屬性。 因此，當[`ContentPage`](xref:Xamarin.Forms.ContentPage)上的 `BindingContext` 設定為 `Note` 實例時，`ContentPage` 的所有子系都具有相同的 `BindingContext`，而且可以系結至 `Note` 物件的公用屬性。

`NoteEntryPage` 中的[`Editor`](xref:Xamarin.Forms.Editor)接著會系結至 `Note` 物件的 `Text` 屬性：

```xaml
<Editor Placeholder="Enter your note"
        Text="{Binding Text}"
        ... />
```

在*來源*物件的 [`Editor.Text`](xref:Xamarin.Forms.Editor.Text) 屬性和 `Text` 屬性之間會建立繫結。 `Editor` 中所做的變更會自動傳播到 `Note` 物件。 同樣地，如果對 `Note.Text` 屬性進行變更，則 Xamarin. Forms 系結引擎也會更新 `Editor`的內容。 這稱為*雙向繫結*。

如需資料繫結的詳細資訊，請參閱 [Xamarin.Forms 資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)。

## <a name="styling"></a>樣式

Xamarin：表單應用程式通常包含多個具有相同外觀的視覺元素。 設定每個視覺專案的外觀可能重複，而且容易出錯。 相反地，您可以建立定義外觀的樣式，然後套用至必要的視覺元素。

[`Style`](xref:Xamarin.Forms.Style)類別會將屬性值的集合分組到一個物件中，然後再套用到多個視覺元素實例。 樣式會儲存在[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)中，不論是在應用層級、頁面層級或視圖層級。 選擇要在何處定義 `Style` 會影響可使用的位置：

- 在應用層級定義的[`Style`](xref:Xamarin.Forms.Style)實例，可以在整個應用程式中套用。
- 在頁面層級定義的[`Style`](xref:Xamarin.Forms.Style)實例可以套用至頁面和其子系。
- 在 view 層級定義的[`Style`](xref:Xamarin.Forms.Style)實例可以套用至視圖和其子系。

> [!IMPORTANT]
> 整個應用程式中使用的任何樣式都會儲存在應用程式的資源字典中，以避免重複。 不過，頁面特有的 XAML 不應包含於應用程式的資源字典中，因為資源接著將在應用程式啟動時 (而非在頁面要求時) 進行剖析。

每個[`Style`](xref:Xamarin.Forms.Style)實例都包含一或多個[`Setter`](xref:Xamarin.Forms.Setter)物件的集合，每個 `Setter` 都有一個[`Property`](xref:Xamarin.Forms.Setter.Property)和一個[`Value`](xref:Xamarin.Forms.Setter.Value)。 `Property` 是套用樣式之元素的可系結屬性名稱，而 `Value` 則是套用至屬性的值。 下列程式碼範例顯示來自 `NoteEntryPage`的樣式：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Notes.NoteEntryPage"
             Title="Note Entry">
    <ContentPage.Resources>
        <!-- Implicit styles -->
        <Style TargetType="{x:Type Editor}">
            <Setter Property="BackgroundColor"
                    Value="{StaticResource AppBackgroundColor}" />
        </Style>
        ...
    </ContentPage.Resources>
    ...
</ContentPage>
```

此樣式適用于頁面上的任何[`Editor`](xref:Xamarin.Forms.Editor)實例。

建立[`Style`](xref:Xamarin.Forms.Style)時，一律需要[`TargetType`](xref:Xamarin.Forms.Style.TargetType)屬性。

> [!NOTE]
> 以傳統方式使用 XAML 樣式來設定 Xamarin 應用程式的樣式。 不過，Xamarin 也支援使用階層式樣式表（CSS）來設定視覺元素的樣式。 如需詳細資訊，請參閱[使用階層式樣式表（CSS）設定 Xamarin 應用程式的樣式](~/xamarin-forms/user-interface/styles/css/index.md)。

如需 XAML 樣式的詳細資訊，請參閱[使用 XAML 樣式設定 Xamarin.Forms 應用程式的樣式](~/xamarin-forms/user-interface/styles/xaml/index.md)。

### <a name="providing-platform-specific-styles"></a>提供平臺特定樣式

`OnPlatform` 標記延伸可讓您自訂每個平臺的 UI 外觀：

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Notes.App">
    <Application.Resources>
        ...
        <Color x:Key="iOSNavigationBarColor">WhiteSmoke</Color>
        <Color x:Key="AndroidNavigationBarColor">#2196F3</Color>
        <Color x:Key="iOSNavigationBarTextColor">Black</Color>
        <Color x:Key="AndroidNavigationBarTextColor">White</Color>

        <Style TargetType="{x:Type NavigationPage}">
            <Setter Property="BarBackgroundColor"
                    Value="{OnPlatform iOS={StaticResource iOSNavigationBarColor},
                                       Android={StaticResource AndroidNavigationBarColor}}" />
             <Setter Property="BarTextColor"
                    Value="{OnPlatform iOS={StaticResource iOSNavigationBarTextColor},
                                       Android={StaticResource AndroidNavigationBarTextColor}}" />           
        </Style>
        ...
    </Application.Resources>
</Application>
```

這個[`Style`](xref:Xamarin.Forms.Style)會根據所使用的平臺，為[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)的[`BarBackgroundColor`](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor)和[`BarTextColor`](xref:Xamarin.Forms.NavigationPage.BarTextColor)屬性設定不同的[`Color`](xref:Xamarin.Forms.Color)值。

如需有關 XAML 標記延伸的詳細資訊，請參閱 [XAML 標記延伸](~/xamarin-forms/xaml/markup-extensions/index.md)。 如需 `OnPlatform` 標記延伸的詳細資訊，請參閱[OnPlatform 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension)。

## <a name="testing-and-deployment"></a>測試與部署

Visual Studio for Mac 和 Visual Studio 都會提供許多選項來測試和部署應用程式。 為應用程式偵錯是應用程式開發週期的共同部分，有助於診斷程式碼問題。 如需詳細資訊，請參閱[設定中斷點](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint)、[逐步執行程式碼](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/step_through_code)，以及[將資訊輸出至記錄視窗](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/output_information_to_log_window)。

模擬器是開始部署與測試應用程式的絕佳位置，而且具備用於測試應用程式的實用功能。 不過，使用者將不會在模擬器中取用最終的應用程式，因此應該及早且經常在實際裝置上測試應用程式。 如需有關 iOS 裝置佈建的詳細資訊，請參閱[裝置佈建](~/ios/get-started/installation/device-provisioning/index.md)。 如需有關 Android 裝置佈建的詳細資訊，請參閱[設定您的裝置以進行開發](~/android/get-started/installation/set-up-device-for-development.md)。

## <a name="next-steps"></a>後續步驟

這一深入探討已檢查使用 Xamarin 進行應用程式開發的基本概念。 建議的後續步驟包括了解下列功能：

- 有四個主要的控制項群組可用來建立 Xamarin.Forms 應用程式的使用者介面。 如需詳細資訊，請參閱[控制項參考](~/xamarin-forms/user-interface/controls/index.md)。
- 資料繫結是連結兩個物件屬性的技術，以便在其中一個屬性變更時，自動反映在另一個屬性上。 如需詳細資訊，請參閱[資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)。
- Xamarin.Forms 會根據使用的頁面類型，提供多種不同的網頁導覽體驗。 如需詳細資訊，請參閱[導覽](~/xamarin-forms/app-fundamentals/navigation/index.md)。
- 樣式功能有助於減少重複的標記，並可更輕鬆地變更應用程式外觀。 如需詳細資訊，請參閱[設定 Xamarin.Forms 應用程式的樣式](~/xamarin-forms/user-interface/styles/index.md)。
- XAML 標記延伸可讓您從常值文字字串以外的來源設定項目屬性，以增強 XAML 的功能和彈性。 如需詳細資訊，請參閱 [XAML 標記延伸](~/xamarin-forms/xaml/markup-extensions/index.md)。
- 資料範本可以針對支援的檢視，定義資料的呈現方式。 如需詳細資訊，請參閱[資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。
- 系統會在每個平台上使用 `Renderer` 類別，以不同的方式呈現每個頁面、版面配置和檢視，進而建立原生控制項、將其排列在畫面上，然後加入在共用程式碼中指定的行為。 開發人員可以實作自己的自訂 `Renderer` 類別，以自訂控制項的外觀及/或行為。 如需詳細資訊，請參閱[自訂呈現方式](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)。
- 效果也可以允許在每個平台上自訂原生控制項。 系統會在平台專屬的專案中，透過將 [`PlatformEffect`](xref:Xamarin.Forms.PlatformEffect`2) 類別子類別化來建立效果，然後透過將其附加至適當的 Xamarin.Forms 控制項來取用。 如需詳細資訊，請參閱[效果](~/xamarin-forms/app-fundamentals/effects/index.md)。
- 共用程式碼可以透過 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 類別存取原生功能。 如需有關詳細資訊，請參閱[透過 DependencyService 存取原生功能](~/xamarin-forms/app-fundamentals/dependency-service/index.md)。

或者，Charles Petzold 的書籍[_使用 Xamarin.Forms 建立行動應用程式_](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)是深入了解 Xamarin.Forms 的絕佳選擇。 此書籍以 PDF 形式或多種電子書格式提供。

## <a name="related-links"></a>相關連結

- [可擴充應用程式標記語言（XAML）](~/xamarin-forms/xaml/index.yml)
- [資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [控制項參考](~/xamarin-forms/user-interface/controls/index.md)
- [XAML 標記延伸](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Xamarin.Forms 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [使用者入門範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms%20get%20started)
- [Xamarin.Forms API 參考](xref:Xamarin.Forms)
- [免費的自我引導式學習 (影片)](https://university.xamarin.com/self-guided/) \(英文\)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Series/Xamarin-101/Xamarin-Solution-Architecture-4-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
