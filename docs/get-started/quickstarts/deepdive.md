---
title: Xamarin.Forms快速入門深入探討
description: 本文會使用來檢查應用程式開發的基本概念 Xamarin.Forms 。 涵蓋的主題包括 Xamarin.Forms 應用程式的結構、架構和應用程式基本概念，以及使用者介面。
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.custom: video
ms.assetid: 7B2340A1-6883-41D8-860C-0BB6C4E0C316
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/27/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1bfb76f71a2ac9d8bc9ae84152501909000b9623
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84132518"
---
# <a name="xamarinforms-quickstart-deep-dive"></a>Xamarin.Forms快速入門深入探討

在[ Xamarin.Forms 快速入門](~/get-started/index.yml)中，已建立 Notes 應用程式。 本文會回顧已建立的內容，以瞭解應用程式如何工作的基本概念 Xamarin.Forms 。

::: zone pivot="windows"

## <a name="introduction-to-visual-studio"></a>Visual Studio 簡介

Visual Studio 會將程式碼組織成*方案*和*專案*。 方案是可以容納一或多個專案的容器。 專案可以是應用程式、支援程式庫、測試應用程式等等。 Notes 應用程式是由一個包含四項專案的方案所組成，如下列螢幕擷取畫面所示：

![](deepdive-images/vs/solution.png "Visual Studio Solution Explorer")

這些專案包括：

- Notes – 此專案為包含所有共用程式碼與共用 UI 的 .NET Standard 程式庫專案。
- Notes.Android – 此專案包含 Android 特定的程式碼，且為 Android 應用程式的進入點。
- Notes.iOS – 此專案包含 iOS 特定的程式碼，且為 iOS 應用程式的進入點。
- Notes.UWP – 此專案包含通用 Windows 平台 (UWP) 特定程式碼，且為 UWP 應用程式的進入點。

## <a name="anatomy-of-a-xamarinforms-application"></a>Xamarin.Forms應用程式的剖析

下列螢幕擷取畫面顯示 Visual Studio 中 Note .NET Standard 程式庫專案的內容：

![](deepdive-images/vs/net-standard-project.png "Phoneword .NET Standard Project Contents")

專案具有 [相依性 **]** 節點，其中包含**NuGet**和**SDK**節點：

- **NuGet** &ndash;已 Xamarin.Forms 新增至專案的和 sqlite-net Pcl NuGet 套件。
- **SDK** &ndash;`NETStandard.Library` 中繼套件，其參考定義 .NET Standard 的一組完整 NuGet 套件。

::: zone-end
::: zone pivot="macos"

## <a name="introduction-to-visual-studio-for-mac"></a>Visual Studio for Mac 簡介

[Visual Studio for Mac](/visualstudio/mac/)遵循將程式碼組織成*方案*和*專案*的 Visual Studio 實務。 方案是可以容納一或多個專案的容器。 專案可以是應用程式、支援程式庫、測試應用程式等等。 Notes 應用程式是由一個包含三項專案的方案所組成，如下列螢幕擷取畫面所示：

![](deepdive-images/vsmac/solution.png "Visual Studio for Mac Solution Pane")

這些專案包括：

- Notes – 此專案為包含所有共用程式碼與共用 UI 的 .NET Standard 程式庫專案。
- Notes.Android – 此專案包含 Android 特定程式碼，且為 Android 應用程式的進入點。
- Notes.iOS – 此專案包含 iOS 特定程式碼，且為 iOS 應用程式的進入點。

## <a name="anatomy-of-a-xamarinforms-application"></a>Xamarin.Forms應用程式的剖析

下列螢幕擷取畫面顯示 Visual Studio for Mac 中 Note .NET Standard 程式庫專案的內容：

![](deepdive-images/vsmac/net-standard-project.png "Phoneword .NET Standard Library Project Contents")

專案具有 [相依性 **]** 節點，其中包含**NuGet**和**SDK**節點：

- **NuGet** &ndash;已 Xamarin.Forms 新增至專案的和 sqlite-net Pcl NuGet 套件。
- **SDK** &ndash;`NETStandard.Library` 中繼套件，其參考定義 .NET Standard 的一組完整 NuGet 套件。

::: zone-end

此專案也包含多個檔案：

- **資料\NoteDatabase.cs** – 此類別包含的程式碼可建立資料庫，並從中讀取資料、將資料寫入其中，以及從中刪除資料。
- **模型\Note.cs** – 此類別可定義 `Note` 模型，該模型的執行個體會儲存應用程式中每個 Note 的相關資料。
- **App.xaml** - `App` 類別的 XAML 標記，可定義應用程式的資源字典。
- **App.xaml.cs** - `App` 類別的程式碼後置，負責將應用程式在每個平台上顯示的第一個頁面初始化，以及處理應用程式生命週期事件。
- **AssemblyInfo.cs** – 此檔案包含與專案相關、適用於組件層級的應用程式屬性。
- **NotesPage.xaml** – `NotesPage` 類別的 XAML 標記，其定義應用程式啟動時，所顯示頁面的 UI。
- **NotesPage.xaml.cs** – `NotesPage` 類別的程式碼後置，其中包含使用者與頁面互動時，所執行的商務邏輯。
- **NoteEntryPage.xaml** – `NoteEntryPage` 類別的 XAML 標記，其定義使用者進入 Note 時，所顯示頁面的 UI。
- **NoteEntryPage.xaml.cs** – `NoteEntryPage` 類別的程式碼後置，其中包含使用者與頁面互動時，所執行的商務邏輯。

如需有關 Xamarin.iOS 應用程式結構的詳細資訊，請參閱 [Xamarin.iOS 應用程式的結構](~/ios/get-started/hello-ios/hello-ios-deepdive.md#anatomy-of-a-xamarinios-application)。 如需有關 Xamarin.Android 應用程式結構的詳細資訊，請參閱 [Xamarin.Android 應用程式的結構](~/android/get-started/hello-android/hello-android-deepdive.md#anatomy)。

## <a name="architecture-and-application-fundamentals"></a>架構與應用程式基本概念

Xamarin.Forms應用程式的架構方式與傳統的跨平臺應用程式相同。 共用程式碼通常放在 .NET Standard 程式庫中，而平台專用的應用程式則會取用共用程式碼。 下圖表顯示 Notes 應用程式關聯性的概觀：

::: zone pivot="windows"

![](deepdive-images/vs/architecture.png "Notes Architecture")

::: zone-end
::: zone pivot="macos"

![](deepdive-images/vsmac/architecture.png "Notes Architecture")

::: zone-end

為了讓啟動程式碼的重複使用最大化， Xamarin.Forms 應用程式具有名為的單一類別， `App` 負責具現化每個平臺上的應用程式將顯示的第一頁，如下列程式碼範例所示：

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

此 `MainPage` 程式碼會將類別的屬性設定 `App` 為 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 實例，其內容為 `NotesPage` 實例。

此外，**AssemblyInfo.cs** 檔案還包含適用於組件層級的單一應用程式屬性：

```csharp
using Xamarin.Forms.Xaml;

[assembly: XamlCompilation(XamlCompilationOptions.Compile)]
```

[`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute)屬性會開啟 xaml 編譯器，以便將 xaml 直接編譯成中繼語言。 如需詳細資訊，請參閱 [XAML 編譯](~/xamarin-forms/xaml/xamlc.md)。

## <a name="launching-the-application-on-each-platform"></a>在每個平台上啟動應用程式

### <a name="ios"></a>iOS

若要在 iOS 中啟動初始 Xamarin.Forms 網頁，記事. iOS 專案會定義 `AppDelegate` 繼承自類別的類別 `FormsApplicationDelegate` ：

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

覆 `FinishedLaunching` 寫會藉 Xamarin.Forms 由呼叫方法來初始化架構 `Init` 。 這會導致的 iOS 特定執行 Xamarin.Forms 在應用程式中載入，而根視圖控制器是由呼叫方法所設定 `LoadApplication` 。

### <a name="android"></a>Android

若要在 Android 中啟動初始 Xamarin.Forms 頁面，Notes 專案包含使用屬性建立的程式碼 `Activity` ，以及 `MainLauncher` 從類別繼承的活動 `FormsAppCompatActivity` ：

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

覆 `OnCreate` 寫會藉 Xamarin.Forms 由呼叫方法來初始化架構 `Init` 。 這會導致在 Xamarin.Forms 載入應用程式之前，先在應用程式中載入的 Android 特定執行 Xamarin.Forms 。

::: zone pivot="windows"

### <a name="universal-windows-platform"></a>通用 Windows 平台

在通用 Windows 平臺（UWP）應用程式中， `Init` Xamarin.Forms 會從類別叫用初始化架構的方法 `App` ：

```csharp
Xamarin.Forms.Forms.Init (e);

if (e.PreviousExecutionState == ApplicationExecutionState.Terminated)
{
  ...
}
```

這會導致在 Xamarin.Forms 應用程式中載入 UWP 特定的執行。 初始 Xamarin.Forms 網頁是由類別所啟動 `MainPage` ：

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

Xamarin.Forms應用程式會使用方法載入 `LoadApplication` 。

> [!NOTE]
> 通用 Windows 平臺的應用程式可以使用建立 Xamarin.Forms ，但只能在 Windows 上使用 Visual Studio。

::: zone-end

## <a name="user-interface"></a>使用者介面

有四個主要的控制項群組可用來建立應用程式的使用者介面 Xamarin.Forms ：

1. **頁面**– Xamarin.Forms 頁面代表跨平臺行動應用程式畫面。 Notes 應用程式會使用 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 類別來顯示單一畫面。 如需有關頁面的詳細資訊，請參閱[ Xamarin.Forms 頁面](~/xamarin-forms/user-interface/controls/pages.md)。
1. **Views** – Xamarin.Forms views 是顯示在使用者介面上的控制項，例如標籤、按鈕和文字輸入方塊。 完成的 Notes 應用程式會使用 [`ListView`](xref:Xamarin.Forms.ListView) 、 [`Editor`](xref:Xamarin.Forms.Editor) 和 [`Button`](xref:Xamarin.Forms.Button) views。 如需有關 views 的詳細資訊，請參閱[ Xamarin.Forms views](~/xamarin-forms/user-interface/controls/views.md)。
1. **版面**配置– Xamarin.Forms 版面配置是用來將 views 組成邏輯結構的容器。 Notes 應用程式會使用 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 類別，以垂直堆疊排列檢視，而類別則會 [`Grid`](xref:Xamarin.Forms.Grid) 以水準方式排列按鈕。 如需版面配置的詳細資訊，請參閱[ Xamarin.Forms 版面](~/xamarin-forms/user-interface/controls/layouts.md)配置。
1. **單元**格–資料 Xamarin.Forms 格是用於清單中專案的特殊元素，並描述如何繪製清單中的每個專案。 Notes 應用程式會使用 [`TextCell`](xref:Xamarin.Forms.TextCell) 來顯示清單中每個資料列的兩個專案。 如需有關資料格的詳細資訊，請參閱資料[ Xamarin.Forms 格](~/xamarin-forms/user-interface/controls/cells.md)。

在執行階段，每個控制項將會對應到其原生對等項目，也就是將呈現的項目。

### <a name="layout"></a>Layout

Notes 應用程式會使用 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 來簡化跨平臺應用程式開發，方法是在螢幕上自動排文視圖，而不論螢幕大小為何。 每個子項目都是以加入這些子項目的順序，一個接著一個地水平或垂直放置。 將使用多少空間 `StackLayout` 取決於 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 設定和屬性的方式 [`VerticalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) ，但根據預設， `StackLayout` 會嘗試使用整個畫面。

下列 XAML 程式碼顯示使用來配置的範例 [`StackLayout`](xref:Xamarin.Forms.StackLayout) `NoteEntryPage` ：

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

根據預設，會 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 假設為垂直方向。 不過，您可以藉由將 [`StackLayout.Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) 屬性設定為列舉成員，將它變更為水準方向 [`StackOrientation.Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal) 。

> [!NOTE]
> 您可以透過 `HeightRequest` 和 `WidthRequest` 屬性來設定檢視的大小。

如需類別的詳細資訊 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ，請參閱[StackLayout](~/xamarin-forms/user-interface/layouts/stacklayout.md)。

### <a name="responding-to-user-interaction"></a>回應使用者互動

在 XAML 中定義的物件可以引發程式碼後置檔案中處理的事件。 下列程式碼範例顯示 `OnSaveButtonClicked` 類別程式碼後置中的方法 `NoteEntryPage` ，其會執行以回應 [`Clicked`](xref:Xamarin.Forms.Button.Clicked) [*儲存*] 按鈕上引發的事件。

```csharp
async void OnSaveButtonClicked(object sender, EventArgs e)
{
    var note = (Note)BindingContext;
    note.Date = DateTime.UtcNow;
    await App.Database.SaveNoteAsync(note);
    await Navigation.PopAsync();
}
```

`OnSaveButtonClicked` 方法會將 Note 儲存在資料庫中，然後巡覽回上一頁。

> [!NOTE]
> XAML 類別的程式碼後置檔案可以存取在 XAML 中使用獲指派名稱及 `x:Name` 屬性定義的物件。 指派給此屬性的值與 C# 變數具有相同的規則，因為該值的開頭必須是字母或底線，而且不得包含內嵌的空格。

[儲存] 按鈕與 `OnSaveButtonClicked` 方法會以 `NoteEntryPage` 類別的 XAML 標記連接：

```xaml
<Button Text="Save"
        Clicked="OnSaveButtonClicked" />
```

### <a name="lists"></a>清單

[`ListView`](xref:Xamarin.Forms.ListView)負責以垂直方式顯示清單中的專案集合。 `ListView` 中的每個項目都會被包含在單一資料格中。

下列程式碼範例顯示 [`ListView`](xref:Xamarin.Forms.ListView) 來自的 `NotesPage` ：

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

中每個資料列的配置 [`ListView`](xref:Xamarin.Forms.ListView) 都是在專案中定義 [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) ，並使用資料系結來顯示應用程式所取出的任何附注。 [`ListView.ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource)在中，屬性會設定為數據源 `NotesPage.xaml.cs` ：

```csharp
protected override async void OnAppearing()
{
    base.OnAppearing();

    listView.ItemsSource = await App.Database.GetNotesAsync();
}
```    

此程式碼會將 [`ListView`](xref:Xamarin.Forms.ListView) 儲存在資料庫中的任何附注填入其中。

在中選取資料列時 [`ListView`](xref:Xamarin.Forms.ListView) ，就會 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 引發事件。 當事件引發時，就會執行名為 `OnListViewItemSelected` 的事件處理常式：

```csharp
async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
{
    if (e.SelectedItem != null)
    {
        ...
    }
}
```

[`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected)事件可以透過屬性存取與儲存格相關聯的物件 [`e.SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem) 。

如需類別的詳細資訊 [`ListView`](xref:Xamarin.Forms.ListView) ，請參閱[ListView](~/xamarin-forms/user-interface/listview/index.md)。

## <a name="navigation"></a>瀏覽

Xamarin.Forms會根據所使用的類型，提供許多不同的頁面導覽體驗 [`Page`](xref:Xamarin.Forms.Page) 。 若為 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 實例導覽，則可以是階層式或模式。 如需強制回應導覽的詳細資訊，請參閱強制回應[ Xamarin.Forms 頁面](~/xamarin-forms/app-fundamentals/navigation/modal.md)。

> [!NOTE]
> [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)、 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 和 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 類別提供替代的導覽體驗。 如需詳細資訊，請參閱[導覽](~/xamarin-forms/app-fundamentals/navigation/index.md)。

在階層式導覽中， [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 會使用類別 [`ContentPage`](xref:Xamarin.Forms.ContentPage) ，視需要向前和向後流覽物件堆疊。 類別會將導覽實作為物件的後進先出（LIFO）堆疊 [`Page`](xref:Xamarin.Forms.Page) 。 若要將一頁移到另一頁，應用程式會將新的頁面推送到導覽堆疊上，該頁面就會變成使用中的頁面。 若要返回到上一頁，應用程式將會從導覽堆疊中快顯目前的頁面，新的最上層頁面就會變成使用中的頁面。

`NavigationPage` 類別也會將巡覽列新增到顯示標題，以及返回上一頁之平台相應 [上一頁]**** 按鈕的頁面頂端。

新增至導覽堆疊中的第一頁稱為應用程式「根」** 頁面，而下列程式碼範例會示範如何在 Notes 應用程式中完成此作業：

```csharp
public App ()
{
    ...
    MainPage = new NavigationPage (new NotesPage ());
}
```

所有 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 實例都有一個 [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) 屬性，可公開修改頁面堆疊的方法。 只有在應用程式包含時，才應該叫用這些方法 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 。 若要流覽至 `NoteEntryPage` ，必須叫用 [ `PushAsync` ] （x： Xamarin.Forms 。NavigationPage. PushAsync （ Xamarin.Forms 。Page））方法，如以下程式碼範例所示：

```csharp
await Navigation.PushAsync(new NoteEntryPage());
```

這會使新的 `NoteEntryPage` 物件推送至導覽堆疊上，從而變成使用中的頁面。

無論是裝置上的實體按鈕還是螢幕上的按鈕，按下裝置上的 [上一頁]** 按鈕都可以從導覽堆疊快顯使用中的頁面。 若要以程式設計方式回到原始頁面， `NoteEntryPage` 物件必須叫用 [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) 方法，如下列程式碼範例所示：

```csharp
await Navigation.PopAsync();
```

如需有關階層式導覽的詳細資訊，請參閱[階層式導覽](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)。

## <a name="data-binding"></a>資料繫結

資料系結是用來簡化 Xamarin.Forms 應用程式顯示和與其資料互動的方式。 它會在使用者介面與基礎應用程式之間建立連線。 [`BindableObject`](xref:Xamarin.Forms.BindableObject)類別包含許多可支援資料系結的基礎結構。

資料繫結會連接兩個物件，稱為*來源*和*目標*。 *來源*物件會提供資料。 *目標*物件將會取用 (而且通常會顯示) 來源物件中的資料。 例如， [`Editor`](xref:Xamarin.Forms.Editor) （*目標*物件）通常會將其屬性系結 [`Text`](xref:Xamarin.Forms.InputView.Text) 至 `string` *來源*物件中的公用屬性。 下圖說明繫結關聯性：

![](deepdive-images/data-binding.png "Data Binding")

資料繫結的主要優點是您不再需要擔心檢視和資料來源之間的資料同步處理。 *來源*物件中的變更會在幕後，透過繫結架構自動推送至*目標*物件，而目標物件中的變更則可以選擇性地推送回*來源*物件。

建立資料繫結需要進行兩項步驟：

- [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)*目標*物件的屬性必須設定為*來源*。
- *目標*和*來源*之間必須建立繫結。 在 XAML 中，這是藉由使用 [`Binding`](xref:Xamarin.Forms.Xaml.BindingExtension) 標記延伸來達成。

在 Notes 應用程式中，系結目標是 [`Editor`](xref:Xamarin.Forms.Editor) 顯示附注的，而 `Note` 實例設定為 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 的是系結 `NoteEntryPage` 來源。

`NoteEntryPage` 的 `BindingContext` 會於頁面導覽期間設定，如下列程式碼範例所示：

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

在將 `OnNoteAddedClicked` 新的附注加入至應用程式時所執行的方法中， [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 的 `NoteEntryPage` 會設定為新的 `Note` 實例。 在 `OnListViewItemSelected` 方法中，當您在中選取了現有的附注時，會將的 [`ListView`](xref:Xamarin.Forms.ListView) `BindingContext` `NoteEntryPage` 設定為選取的 `Note` 實例（透過屬性存取） [`e.SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem) 。

> [!IMPORTANT]
> 雖然 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 可以個別設定每個*目標*物件的屬性，但這並不是必要的。 `BindingContext` 為其所有子系繼承的特殊屬性。 因此，當 `BindingContext` 上的 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 設定為實例時，的 `Note` 所有子系 `ContentPage` 都具有相同的 `BindingContext` ，而且可以系結至物件的公用屬性 `Note` 。

中的會系結 [`Editor`](xref:Xamarin.Forms.Editor) `NoteEntryPage` 至 `Text` 物件的屬性 `Note` ：

```xaml
<Editor Placeholder="Enter your note"
        Text="{Binding Text}"
        ... />
```

在 [`Editor.Text`](xref:Xamarin.Forms.InputView.Text) 來源物件的屬性和屬性之間建立系結 `Text` 。 *source* 在 `Editor` 中所做的變更將會自動傳播到 `Note` 物件。 同樣地，如果對屬性進行了變更，系結 `Note.Text` Xamarin.Forms 引擎也會更新的內容 `Editor` 。 這稱為*雙向繫結*。

如需資料系結的詳細資訊，請參閱[ Xamarin.Forms 資料](~/xamarin-forms/app-fundamentals/data-binding/index.md)系結。

## <a name="styling"></a>樣式

Xamarin.Forms應用程式通常會包含多個具有相同外觀的視覺元素。 在設定每個視覺元素的外觀時，可能產生重複且容易出錯。 作為替代，您可以建立定義外觀的樣式，然後套用至必要的視覺元素。

[`Style`](xref:Xamarin.Forms.Style)類別會將屬性值的集合分組成一個物件，然後再套用到多個視覺元素實例。 樣式會儲存在中 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) ，不論是在應用層級、頁面層級或視圖層級。 選擇要在何處定義 `Style` 會影響其可使用的位置：

- [`Style`](xref:Xamarin.Forms.Style)在應用層級定義的實例可以在整個應用程式中套用。
- [`Style`](xref:Xamarin.Forms.Style)在頁面層級定義的實例可以套用至頁面和其子系。
- [`Style`](xref:Xamarin.Forms.Style)在 view 層級定義的實例可以套用至視圖和其子系。

> [!IMPORTANT]
> 應用程式中所使用任何樣式都儲存於應用程式的資源字典中，以避免重複。 不過，頁面特有的 XAML 不應包含於應用程式的資源字典中，因為資源接著將在應用程式啟動時 (而非在頁面要求時) 進行剖析。

每個 [`Style`](xref:Xamarin.Forms.Style) 實例都包含一或多個 [`Setter`](xref:Xamarin.Forms.Setter) 物件的集合，每個都具有 `Setter` [`Property`](xref:Xamarin.Forms.Setter.Property) 和 [`Value`](xref:Xamarin.Forms.Setter.Value) 。 `Property` 為樣式所套用元素的可繫結屬性名稱，且 `Value` 為套用至屬性的值。 下列程式碼範例示範 `NoteEntryPage` 的樣式：

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

此樣式適用于 [`Editor`](xref:Xamarin.Forms.Editor) 頁面上的任何實例。

建立時 [`Style`](xref:Xamarin.Forms.Style) ， [`TargetType`](xref:Xamarin.Forms.Style.TargetType) 一律需要屬性。

> [!NOTE]
> Xamarin.Forms一般會使用 XAML 樣式來設定應用程式的樣式。 不過， Xamarin.Forms 也支援使用階層式樣式表（CSS）設定視覺元素的樣式。 如需詳細資訊，請參閱[ Xamarin.Forms 使用階層式樣式表（CSS）設定應用程式的樣式](~/xamarin-forms/user-interface/styles/css/index.md)。

如需 XAML 樣式的詳細資訊，請參閱[ Xamarin.Forms 使用 xaml 樣式設定應用程式的樣式](~/xamarin-forms/user-interface/styles/xaml/index.md)。

### <a name="providing-platform-specific-styles"></a>提供平台特定樣式

`OnPlatform` 標記延伸模組可讓您自訂每個平台的 UI 外觀：

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

這會 [`Style`](xref:Xamarin.Forms.Style) [`Color`](xref:Xamarin.Forms.Color) 根據所 [`BarBackgroundColor`](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor) [`BarTextColor`](xref:Xamarin.Forms.NavigationPage.BarTextColor) [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 使用的平臺，為和屬性設定不同的值。

如需有關 XAML 標記延伸的詳細資訊，請參閱 [XAML 標記延伸](~/xamarin-forms/xaml/markup-extensions/index.md)。 如需 `OnPlatform` 標記延伸的資訊，請參閱 [OnPlatform 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension)。

## <a name="testing-and-deployment"></a>測試和部署

Visual Studio for Mac 和 Visual Studio 都會提供許多選項來測試和部署應用程式。 為應用程式偵錯是應用程式開發週期的共同部分，有助於診斷程式碼問題。 如需詳細資訊，請參閱[設定中斷點](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint)、[逐步執行程式碼](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/step_through_code)，以及[將資訊輸出至記錄視窗](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/output_information_to_log_window)。

模擬器是開始部署與測試應用程式的絕佳位置，而且具備用於測試應用程式的實用功能。 不過，使用者將不會在模擬器中取用最終的應用程式，因此應該及早且經常在實際裝置上測試應用程式。 如需有關 iOS 裝置佈建的詳細資訊，請參閱[裝置佈建](~/ios/get-started/installation/device-provisioning/index.md)。 如需有關 Android 裝置佈建的詳細資訊，請參閱[設定您的裝置以進行開發](~/android/get-started/installation/set-up-device-for-development.md)。

## <a name="next-steps"></a>後續步驟

這一深入探討已使用來檢查應用程式開發的基本概念 Xamarin.Forms 。 建議的後續步驟包括了解下列功能：

- 有四個主要的控制項群組可用來建立應用程式的使用者介面 Xamarin.Forms 。 如需詳細資訊，請參閱[控制項參考](~/xamarin-forms/user-interface/controls/index.md)。
- 資料繫結是連結兩個物件屬性的技術，以便在其中一個屬性變更時，自動反映在另一個屬性上。 如需詳細資訊，請參閱[資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)。
- Xamarin.Forms會根據使用的頁面類型，提供多種不同的頁面導覽體驗。 如需詳細資訊，請參閱[導覽](~/xamarin-forms/app-fundamentals/navigation/index.md)。
- 樣式功能有助於減少重複的標記，並可更輕鬆地變更應用程式外觀。 如需詳細資訊，請參閱設定[ Xamarin.Forms 應用程式的樣式](~/xamarin-forms/user-interface/styles/index.md)。
- XAML 標記延伸可讓您從常值文字字串以外的來源設定項目屬性，以增強 XAML 的功能和彈性。 如需詳細資訊，請參閱 [XAML 標記延伸](~/xamarin-forms/xaml/markup-extensions/index.md)。
- 資料範本可以針對支援的檢視，定義資料的呈現方式。 如需詳細資訊，請參閱[資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。
- 系統會在每個平台上使用 `Renderer` 類別，以不同的方式呈現每個頁面、版面配置和檢視，進而建立原生控制項、將其排列在畫面上，然後加入在共用程式碼中指定的行為。 開發人員可以實作自己的自訂 `Renderer` 類別，以自訂控制項的外觀及/或行為。 如需詳細資訊，請參閱[自訂呈現方式](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)。
- 效果也可以允許在每個平台上自訂原生控制項。 在平臺特定專案中，會藉由將類別子類別化來建立效果 [`PlatformEffect`](xref:Xamarin.Forms.PlatformEffect`2) ，並將其附加至適當的控制項來使用 Xamarin.Forms 。 如需詳細資訊，請參閱[效果](~/xamarin-forms/app-fundamentals/effects/index.md)。
- 共用程式碼可以透過類別存取原生功能 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 。 如需有關詳細資訊，請參閱[透過 DependencyService 存取原生功能](~/xamarin-forms/app-fundamentals/dependency-service/index.md)。

或者，使用 Charles Petzold 的書籍[_建立 Mobile Apps_](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)，是深入瞭解的絕佳位置 Xamarin.Forms 。 此書籍以 PDF 形式或多種電子書格式提供。

## <a name="related-links"></a>相關連結

- [eXtensible Application Markup Language (XAML)](~/xamarin-forms/xaml/index.yml)
- [資料系結](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [控制項參考](~/xamarin-forms/user-interface/controls/index.md)
- [XAML 標記延伸](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Xamarin.Forms範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [消費者入門範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms%20get%20started)
- [Xamarin.FormsAPI 參考](xref:Xamarin.Forms)
- [免費的自我引導式學習 (影片)](https://university.xamarin.com/self-guided/) \(英文\)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Series/Xamarin-101/Xamarin-Solution-Architecture-4-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
