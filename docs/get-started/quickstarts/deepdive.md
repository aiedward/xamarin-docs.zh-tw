---
title: Xamarin.Forms 快速入門深入探討
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
ms.openlocfilehash: c0e8ec27898cc842d485967e525c2936d7a0f56d
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "77131055"
---
# <a name="xamarinforms-quickstart-deep-dive"></a>Xamarin.Forms 快速入門深入探討

Notes 應用程式已建置於 [Xamarin.Forms 快速入門](~/get-started/index.yml)中。 本文會檢閱已經建立的項目，以了解 Xamarin.Forms 應用程式運作方式的基本概念。

::: zone pivot="windows"

## <a name="introduction-to-visual-studio"></a>Visual Studio 簡介

視覺化工作室將程式碼組織到*解決方案*與*專案中*。 方案是可以容納一或多個專案的容器。 專案可以是應用程式、支援程式庫、測試應用程式等等。 Notes 應用程式是由一個包含四項專案的方案所組成，如下列螢幕擷取畫面所示：

![](deepdive-images/vs/solution.png "Visual Studio Solution Explorer")

這些專案包括：

- Notes – 此專案為包含所有共用程式碼與共用 UI 的 .NET Standard 程式庫專案。
- Notes.Android – 此專案包含 Android 特定的程式碼，且為 Android 應用程式的進入點。
- Notes.iOS – 此專案包含 iOS 特定的程式碼，且為 iOS 應用程式的進入點。
- Notes.UWP – 此專案包含通用 Windows 平台 (UWP) 特定程式碼，且為 UWP 應用程式的進入點。

## <a name="anatomy-of-a-xamarinforms-application"></a>Xamarin.Forms 應用程式的結構

下列螢幕擷取畫面顯示 Visual Studio 中 Note .NET Standard 程式庫專案的內容：

![](deepdive-images/vs/net-standard-project.png "Phoneword .NET Standard Project Contents")

專案具有包含**NuGet**和**SDK**節點**的相依項**節點:

- **NuGet** &ndash; 已新增至專案的 Xamarin.Forms 與 sqlite-net-pcl NuGet 套件。
- **SDK** &ndash;`NETStandard.Library` 中繼套件，其參考定義 .NET Standard 的一組完整 NuGet 套件。

::: zone-end
::: zone pivot="macos"

## <a name="introduction-to-visual-studio-for-mac"></a>Visual Studio for Mac 簡介

[Mac 的可視化工作室](/visualstudio/mac/)遵循將代碼組織到*解決方案*和*專案中*的視覺工作室實踐。 方案是可以容納一或多個專案的容器。 專案可以是應用程式、支援程式庫、測試應用程式等等。 Notes 應用程式是由一個包含三項專案的方案所組成，如下列螢幕擷取畫面所示：

![](deepdive-images/vsmac/solution.png "Visual Studio for Mac Solution Pane")

這些專案包括：

- Notes – 此專案為包含所有共用程式碼與共用 UI 的 .NET Standard 程式庫專案。
- Notes.Android – 此專案包含 Android 特定程式碼，且為 Android 應用程式的進入點。
- Notes.iOS – 此專案包含 iOS 特定程式碼，且為 iOS 應用程式的進入點。

## <a name="anatomy-of-a-xamarinforms-application"></a>Xamarin.Forms 應用程式的結構

下列螢幕擷取畫面顯示 Visual Studio for Mac 中 Note .NET Standard 程式庫專案的內容：

![](deepdive-images/vsmac/net-standard-project.png "Phoneword .NET Standard Library Project Contents")

專案具有包含**NuGet**和**SDK**節點**的相依項**節點:

- **NuGet** &ndash; 已新增至專案的 Xamarin.Forms 與 sqlite-net-pcl NuGet 套件。
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

Xamarin.Forms 應用程式的架構方式與傳統的跨平台應用程式相同。 共用程式碼通常放在 .NET Standard 程式庫中，而平台專用的應用程式則會取用共用程式碼。 下圖表顯示 Notes 應用程式關聯性的概觀：

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

此`MainPage`代碼將`App`類的屬性設置到其內容[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)`NotesPage`為 實例的實例。

此外，**AssemblyInfo.cs** 檔案還包含適用於組件層級的單一應用程式屬性：

```csharp
using Xamarin.Forms.Xaml;

[assembly: XamlCompilation(XamlCompilationOptions.Compile)]
```

該[`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute)屬性將打開 XAML 編譯器,以便 XAML 直接編譯為中間語言。 如需詳細資訊，請參閱 [XAML 編譯](~/xamarin-forms/xaml/xamlc.md)。

## <a name="launching-the-application-on-each-platform"></a>在每個平台上啟動應用程式

### <a name="ios"></a>iOS

若要在 iOS 中啟動初始的 Xamarin.Forms 頁面，則 Notes.iOS 專案會定義繼承自 `FormsApplicationDelegate` 類別的 `AppDelegate` 類別：

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

若要在 Android 中啟動初始的 Xamarin.Forms 頁面，則 Notes.Android 專案會包含使用 `MainLauncher` 屬性來建立 `Activity` 的程式碼，其中活動會從 `FormsAppCompatActivity` 類別繼承：

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

如此會在應用程式中載入 UWP 專用的 Xamarin.Forms 實作。 而初始的 Xamarin.Forms 頁面則由 `MainPage` 類別啟動：

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
> 通用 Windows 平台應用程式可以使用 Xamarin.Forms 來建置，但僅限於在 Windows 上使用 Visual Studio 的情況下。

::: zone-end

## <a name="user-interface"></a>使用者介面

您可以使用四個主要的控制項群組，以建立 Xamarin.Forms 應用程式的使用者介面：

1. **頁面** - Xamarin.Forms 頁面代表跨平台行動應用程式畫面。 Notes 應用程式[`ContentPage`](xref:Xamarin.Forms.ContentPage)使用 類來顯示單個螢幕。 如需有關頁面的詳細資訊，請參閱 [Xamarin.Forms 頁面](~/xamarin-forms/user-interface/controls/pages.md)。
1. **檢視** - Xamarin.Forms 檢視是顯示在使用者介面上的控制項，例如標籤、按鈕和文字輸入方塊。 已完成的 Notes[`ListView`](xref:Xamarin.Forms.ListView)[`Editor`](xref:Xamarin.Forms.Editor)應用程式[`Button`](xref:Xamarin.Forms.Button)使用 、 和 檢視。 如需有關檢視的詳細資訊，請參閱 [Xamarin.Forms 檢視](~/xamarin-forms/user-interface/controls/views.md)。
1. **版面配置** - Xamarin.Forms 版面配置是將檢視構成邏輯結構所使用的容器。 Notes 應用程式[`StackLayout`](xref:Xamarin.Forms.StackLayout)使用 類在垂直堆疊中排列檢視[`Grid`](xref:Xamarin.Forms.Grid), 使用類水準排列按鈕。 如需有關版面配置的詳細資訊，請參閱 [Xamarin.Forms 版面配置](~/xamarin-forms/user-interface/controls/layouts.md)。
1. **資料格** - Xamarin.Forms 資料格是在清單中用於項目的特定元素，並描述如何在清單中繪製每個項目。 Notes 應用程式[`TextCell`](xref:Xamarin.Forms.TextCell)使用 顯示清單中每行的兩個專案。 如需有關資料格的詳細資訊，請參閱 [Xamarin.Forms 資料格](~/xamarin-forms/user-interface/controls/cells.md)。

在執行階段，每個控制項將會對應到其原生對等項目，也就是將呈現的項目。

### <a name="layout"></a>配置

Notes 應用程式[`StackLayout`](xref:Xamarin.Forms.StackLayout)使用 通過自動排列螢幕上的檢視來簡化跨平臺應用程式開發,而不考慮螢幕大小。 每個子項目都是以加入這些子項目的順序，一個接著一個地水平或垂直放置。 `StackLayout`將使用的空間取決於[`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions)設定[`VerticalOptions`](xref:Xamarin.Forms.View.HorizontalOptions)和 屬性的方式,但預設`StackLayout`情況下, 將嘗試使用整個螢幕。

以下 XAML 代碼顯示使用[`StackLayout`](xref:Xamarin.Forms.StackLayout)佈局 的範`NoteEntryPage`例:

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

預設情況下,[`StackLayout`](xref:Xamarin.Forms.StackLayout)假定垂直方向。 但是,通過將屬性設置為[`StackLayout.Orientation`](xref:Xamarin.Forms.StackLayout.Orientation)[`StackOrientation.Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal)枚舉成員,可以將其更改為水準方向。

> [!NOTE]
> 您可以透過 `HeightRequest` 和 `WidthRequest` 屬性來設定檢視的大小。

關於類別的詳細資訊,[`StackLayout`](xref:Xamarin.Forms.StackLayout)請參考[堆疊佈局](~/xamarin-forms/user-interface/layouts/stack-layout.md)。

### <a name="responding-to-user-interaction"></a>回應使用者互動

在 XAML 中定義的物件可以引發程式碼後置檔案中處理的事件。 `OnSaveButtonClicked`下面的代碼示例顯示了`NoteEntryPage`類的代碼後面中的方法,該方法是為了[`Clicked`](xref:Xamarin.Forms.Button.Clicked)回應 *"保存"* 按鈕上的事件觸發而執行的。

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

[`ListView`](xref:Xamarin.Forms.ListView)負責在清單中垂直顯示項的集合。 `ListView` 中的每個項目都會被包含在單一資料格中。

以下代碼範例顯示[`ListView`](xref:Xamarin.Forms.ListView)的`NotesPage`。

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

中每行的佈局[`ListView`](xref:Xamarin.Forms.ListView)[`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate)在 元素中定義,並使用數據綁定顯示應用程式檢索的任何註釋。 屬性[`ListView.ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource)設定為資料來源,在`NotesPage.xaml.cs`中 :

```csharp
protected override async void OnAppearing()
{
    base.OnAppearing();

    listView.ItemsSource = await App.Database.GetNotesAsync();
}
```    

此程式碼使用資料庫中[`ListView`](xref:Xamarin.Forms.ListView)儲存的任何註解填滿 。

在 中選擇行[`ListView`](xref:Xamarin.Forms.ListView)時[`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected), 將觸發事件。 當事件引發時，就會執行名為 `OnListViewItemSelected` 的事件處理常式：

```csharp
async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
{
    if (e.SelectedItem != null)
    {
        ...
    }
}
```

事件[`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected)[`e.SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem)可以通過 屬性訪問與單元格關聯的物件。

關於此類別的詳細資訊,[`ListView`](xref:Xamarin.Forms.ListView)請參考[ListView](~/xamarin-forms/user-interface/listview/index.md)。

## <a name="navigation"></a>導覽

Xamarin.Forms 提供了許多不同的頁面導航體驗,具體取決於所[`Page`](xref:Xamarin.Forms.Page)使用的 類型。 例如[`ContentPage`](xref:Xamarin.Forms.ContentPage),導航可以是分層的,也可以是模式的。 如需強制回應導覽的資訊，請參閱 [Xamarin.Forms 強制回應頁面](~/xamarin-forms/app-fundamentals/navigation/modal.md)。

> [!NOTE]
> [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)[`CarouselPage`](xref:Xamarin.Forms.CarouselPage)和[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)類提供替代導航體驗。 如需詳細資訊，請參閱[導覽](~/xamarin-forms/app-fundamentals/navigation/index.md)。

在分層導航中,[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)類用於根據需要向前和向後流覽[`ContentPage`](xref:Xamarin.Forms.ContentPage)一堆 物件。 該類實現導航作為[`Page`](xref:Xamarin.Forms.Page)最後一個入出(LIFO)物件堆疊。 若要將一頁移到另一頁，應用程式會將新的頁面推送到導覽堆疊上，該頁面就會變成使用中的頁面。 若要返回到上一頁，應用程式將會從導覽堆疊中快顯目前的頁面，新的最上層頁面就會變成使用中的頁面。

`NavigationPage` 類別也會將巡覽列新增到顯示標題，以及返回上一頁之平台相應 [上一頁]**** 按鈕的頁面頂端。

新增至導覽堆疊中的第一頁稱為應用程式「根」** 頁面，而下列程式碼範例會示範如何在 Notes 應用程式中完成此作業：

```csharp
public App ()
{
    ...
    MainPage = new NavigationPage (new NotesPage ());
}
```

所有[`ContentPage`](xref:Xamarin.Forms.ContentPage)實例都有一[`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation)個 屬性,該屬性公開用於修改頁面堆疊的方法。 只使用應用程式包含時,才應呼叫這些方法[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)。 要導覽`NoteEntryPage`到 ,必須[`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync(Xamarin.Forms.Page))呼叫 如下代碼範例的範示的方法:

```csharp
await Navigation.PushAsync(new NoteEntryPage());
```

這會使新的 `NoteEntryPage` 物件推送至導覽堆疊上，從而變成使用中的頁面。

無論是裝置上的實體按鈕還是螢幕上的按鈕，按下裝置上的 [上一頁]** 按鈕都可以從導覽堆疊快顯使用中的頁面。 要以程式設計方式傳回到原始頁`NoteEntryPage`, 物件必須[`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync)呼叫 方法,如下代碼範例所示:

```csharp
await Navigation.PopAsync();
```

如需有關階層式導覽的詳細資訊，請參閱[階層式導覽](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)。

## <a name="data-binding"></a>資料繫結

資料繫結用來簡化 Xamarin.Forms 應用程式顯示其資料以及與之互動的方式。 它會在使用者介面與基礎應用程式之間建立連線。 該[`BindableObject`](xref:Xamarin.Forms.BindableObject)類包含支援數據綁定的大部分基礎結構。

資料繫結會連接兩個物件，稱為*來源*和*目標*。 *來源*物件會提供資料。 *目標*物件將會取用 (而且通常會顯示) 來源物件中的資料。 例如[`Editor`](xref:Xamarin.Forms.Editor),(*目標*物件)通常[`Text`](xref:Xamarin.Forms.InputView.Text)會將其 屬性`string`綁定到*源*物件中的公共屬性。 下圖說明繫結關聯性：

![](deepdive-images/data-binding.png "Data Binding")

資料繫結的主要優點是您不再需要擔心檢視和資料來源之間的資料同步處理。 *來源*物件中的變更會在幕後，透過繫結架構自動推送至*目標*物件，而目標物件中的變更則可以選擇性地推送回*來源*物件。

建立資料繫結需要進行兩項步驟：

- 目標[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)物件的屬性*target*必須 設定為*來源*。
- *目標*和*來源*之間必須建立繫結。 在 XAML 中,這是[`Binding`](xref:Xamarin.Forms.Xaml.BindingExtension)通過使用 標記擴展實現的。

在 Notes 應用程式中,綁定目標是顯示[`Editor`](xref:Xamarin.Forms.Editor)註釋 的,而`Note`[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)設置為`NoteEntryPage`的實例 是綁定源。

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

在`OnNoteAddedClicked`方法中,當向應用程式添加新註釋時執行 ,[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)中,`NoteEntryPage``Note`設置為新實例。 在`OnListViewItemSelected`[`ListView`](xref:Xamarin.Forms.ListView)方法中,當在中選擇的現有註釋時,將`BindingContext`執行該`NoteEntryPage`方法的,該實`Note`例將設置為所選實例,該實例[`e.SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem)通過 屬性訪問。

> [!IMPORTANT]
> 雖然可以[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)單獨設置每個*目標*物件的屬性,但這不是必要的。 `BindingContext` 為其所有子系繼承的特殊屬性。 因此,當`BindingContext`[`ContentPage`](xref:Xamarin.Forms.ContentPage)on 設置為`Note`實例 時,`ContentPage`的所有子級`BindingContext`具有相同的 ,`Note`並且可以綁定到 物件的公共屬性。

然後`Text``Note`結合物件的屬性: [`Editor`](xref:Xamarin.Forms.Editor) `NoteEntryPage`

```xaml
<Editor Placeholder="Enter your note"
        Text="{Binding Text}"
        ... />
```

在[`Editor.Text`](xref:Xamarin.Forms.InputView.Text)屬性`Text`和*源*物件的屬性之間建立綁定。 在 `Editor` 中所做的變更將會自動傳播到 `Note` 物件。 同樣地，如果對 `Note.Text` 屬性進行變更，則 Xamarin.Forms 繫結引擎也會更新 `Editor` 的內容。 這稱為*雙向繫結*。

如需資料繫結的詳細資訊，請參閱 [Xamarin.Forms 資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)。

## <a name="styling"></a>樣式

Xamarin.Forms 應用程式通常包含多個具有相同外觀的視覺元素。 在設定每個視覺元素的外觀時，可能產生重複且容易出錯。 作為替代，您可以建立定義外觀的樣式，然後套用至必要的視覺元素。

類[`Style`](xref:Xamarin.Forms.Style)將屬性值的集合分組到一個物件中,然後可以應用於多個可視元素實例。 樣式儲存在、[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)中,無論是在應用程式級別、頁面級別還是檢視級別。 選擇要在何處定義 `Style` 會影響其可使用的位置：

- [`Style`](xref:Xamarin.Forms.Style)在應用程式級別定義的實例可以應用於整個應用程式。
- [`Style`](xref:Xamarin.Forms.Style)在頁面級別定義的實例可以應用於頁面及其子級。
- [`Style`](xref:Xamarin.Forms.Style)在視圖級別定義的實例可以應用於視圖及其子級。

> [!IMPORTANT]
> 應用程式中所使用任何樣式都儲存於應用程式的資源字典中，以避免重複。 不過，頁面特有的 XAML 不應包含於應用程式的資源字典中，因為資源接著將在應用程式啟動時 (而非在頁面要求時) 進行剖析。

每個[`Style`](xref:Xamarin.Forms.Style)實體包含一[`Setter`](xref:Xamarin.Forms.Setter)個或多個物件的集合,每個`Setter`物件都有[`Property`](xref:Xamarin.Forms.Setter.Property)與[`Value`](xref:Xamarin.Forms.Setter.Value)。 `Property` 為樣式所套用元素的可繫結屬性名稱，且 `Value` 為套用至屬性的值。 下列程式碼範例示範 `NoteEntryPage` 的樣式：

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

此樣式應用於頁面上的任何[`Editor`](xref:Xamarin.Forms.Editor)實例。

創建[`Style`](xref:Xamarin.Forms.Style)時[`TargetType`](xref:Xamarin.Forms.Style.TargetType),始終需要該屬性。

> [!NOTE]
> 傳統上，設定 Xamarin.Forms 應用程式的樣式時，都是使用 XAML 樣式來完成。 不過，Xamarin.Forms 也支援使用階層式樣式表 (CSS) 來設定視覺元素的樣式。 如需詳細資訊，請參閱[使用階層式樣式表 (CSS) 設定 Xamarin.Forms 應用程式的樣式](~/xamarin-forms/user-interface/styles/css/index.md)。

如需 XAML 樣式的詳細資訊，請參閱[使用 XAML 樣式設定 Xamarin.Forms 應用程式的樣式](~/xamarin-forms/user-interface/styles/xaml/index.md)。

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

這將[`Style`](xref:Xamarin.Forms.Style)根據所使用的[`Color`](xref:Xamarin.Forms.Color)平臺[`BarBackgroundColor`](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor)為[`BarTextColor`](xref:Xamarin.Forms.NavigationPage.BarTextColor)[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)和設置不同的值和 屬性。

如需有關 XAML 標記延伸的詳細資訊，請參閱 [XAML 標記延伸](~/xamarin-forms/xaml/markup-extensions/index.md)。 如需 `OnPlatform` 標記延伸的資訊，請參閱 [OnPlatform 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension)。

## <a name="testing-and-deployment"></a>測試和部署

Visual Studio for Mac 和 Visual Studio 都會提供許多選項來測試和部署應用程式。 為應用程式偵錯是應用程式開發週期的共同部分，有助於診斷程式碼問題。 如需詳細資訊，請參閱[設定中斷點](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint)、[逐步執行程式碼](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/step_through_code)，以及[將資訊輸出至記錄視窗](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/output_information_to_log_window)。

模擬器是開始部署與測試應用程式的絕佳位置，而且具備用於測試應用程式的實用功能。 不過，使用者將不會在模擬器中取用最終的應用程式，因此應該及早且經常在實際裝置上測試應用程式。 如需有關 iOS 裝置佈建的詳細資訊，請參閱[裝置佈建](~/ios/get-started/installation/device-provisioning/index.md)。 如需有關 Android 裝置佈建的詳細資訊，請參閱[設定您的裝置以進行開發](~/android/get-started/installation/set-up-device-for-development.md)。

## <a name="next-steps"></a>後續步驟

本深入探討檢視使用 Xamarin.Forms 開發應用程式的基本概念。 建議的後續步驟包括了解下列功能：

- 有四個主要的控制項群組可用來建立 Xamarin.Forms 應用程式的使用者介面。 如需詳細資訊，請參閱[控制項參考](~/xamarin-forms/user-interface/controls/index.md)。
- 資料繫結是連結兩個物件屬性的技術，以便在其中一個屬性變更時，自動反映在另一個屬性上。 如需詳細資訊，請參閱[資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)。
- Xamarin.Forms 會根據使用的頁面類型，提供多種不同的網頁導覽體驗。 如需詳細資訊，請參閱[導覽](~/xamarin-forms/app-fundamentals/navigation/index.md)。
- 樣式功能有助於減少重複的標記，並可更輕鬆地變更應用程式外觀。 如需詳細資訊，請參閱[設定 Xamarin.Forms 應用程式的樣式](~/xamarin-forms/user-interface/styles/index.md)。
- XAML 標記延伸可讓您從常值文字字串以外的來源設定項目屬性，以增強 XAML 的功能和彈性。 如需詳細資訊，請參閱 [XAML 標記延伸](~/xamarin-forms/xaml/markup-extensions/index.md)。
- 資料範本可以針對支援的檢視，定義資料的呈現方式。 如需詳細資訊，請參閱[資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。
- 系統會在每個平台上使用 `Renderer` 類別，以不同的方式呈現每個頁面、版面配置和檢視，進而建立原生控制項、將其排列在畫面上，然後加入在共用程式碼中指定的行為。 開發人員可以實作自己的自訂 `Renderer` 類別，以自訂控制項的外觀及/或行為。 如需詳細資訊，請參閱[自訂呈現方式](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)。
- 效果也可以允許在每個平台上自訂原生控制項。 效果是通過對[`PlatformEffect`](xref:Xamarin.Forms.PlatformEffect`2)類的子類在特定於平臺的項目中創建的,並通過將它們附加到相應的 Xamarin.Forms 控制件來使用。 如需詳細資訊，請參閱[效果](~/xamarin-forms/app-fundamentals/effects/index.md)。
- 共用代碼可以[`DependencyService`](xref:Xamarin.Forms.DependencyService)通過 類訪問本機功能。 如需有關詳細資訊，請參閱[透過 DependencyService 存取原生功能](~/xamarin-forms/app-fundamentals/dependency-service/index.md)。

或者，Charles Petzold 的書籍[_使用 Xamarin.Forms 建立行動應用程式_](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)是深入了解 Xamarin.Forms 的絕佳選擇。 此書籍以 PDF 形式或多種電子書格式提供。

## <a name="related-links"></a>相關連結

- [eXtensible Application Markup Language (XAML)](~/xamarin-forms/xaml/index.yml)
- [資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [控制項參考](~/xamarin-forms/user-interface/controls/index.md)
- [XAML 標記延伸](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Xamarin.Forms 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [抓取入門示例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms%20get%20started)
- [Xamarin.Forms API 參考](xref:Xamarin.Forms)
- [免費的自我引導式學習 (影片)](https://university.xamarin.com/self-guided/) \(英文\)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Series/Xamarin-101/Xamarin-Solution-Architecture-4-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
