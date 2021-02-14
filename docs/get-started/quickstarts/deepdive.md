---
title: Xamarin.Forms 快速入門深入探討
description: 本文探討使用 Shell 進行應用程式開發的基本概念 Xamarin.Forms 。 涵蓋的主題包括 Xamarin.Forms Shell 應用程式、架構和應用程式基本概念，以及使用者介面的剖析。
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.custom: video
ms.assetid: F65C83B7-BC9F-425F-8662-931B652A2946
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/25/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 723ea0f3c6703824bfbfca51f4ecfc8c5ab0b83a
ms.sourcegitcommit: 0a6b19004932c1ac82e16c95d5d3d5eb35a5b17f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2021
ms.locfileid: "100255303"
---
# <a name="xamarinforms-quickstart-deep-dive"></a>Xamarin.Forms 快速入門深入探討

在[ Xamarin.Forms 快速入門](~/get-started/index.yml)中，已建立 Notes 應用程式。 本文將探討已建立的專案，以瞭解 Xamarin.Forms Shell 應用程式運作方式的基本概念。

::: zone pivot="windows"

## <a name="introduction-to-visual-studio"></a>Visual Studio 簡介

Visual Studio 會將程式碼組織成 *方案* 和 *專案*。 方案是可以容納一或多個專案的容器。 專案可以是應用程式、支援程式庫、測試應用程式等等。 Notes 應用程式是由一個包含三項專案的方案所組成，如下列螢幕擷取畫面所示：

![Visual Studio 方案總管](deepdive-images/vs/solution.png)

這些專案包括：

- Notes – 此專案為包含所有共用程式碼與共用 UI 的 .NET Standard 程式庫專案。
- Notes.Android – 此專案包含 Android 特定的程式碼，且為 Android 應用程式的進入點。
- Notes.iOS – 此專案包含 iOS 特定的程式碼，且為 iOS 應用程式的進入點。

## <a name="anatomy-of-a-xamarinforms-application"></a>Xamarin.Forms應用程式的剖析

下列螢幕擷取畫面顯示 Visual Studio 中 Note .NET Standard 程式庫專案的內容：

![Phoneword .NET Standard 專案內容](deepdive-images/vs/net-standard-project.png)

專案具有相依性 **節點，** 其中包含 **NuGet** 和 **SDK** 節點：

- **NuGet** &ndash; 已 Xamarin.Forms Xamarin.Essentials 新增至專案的、、Newtonsoft.Js，以及 sqlite-net-pcl NuGet 套件。
- **SDK** &ndash;`NETStandard.Library` 中繼套件，其參考定義 .NET Standard 的一組完整 NuGet 套件。

::: zone-end
::: zone pivot="macos"

## <a name="introduction-to-visual-studio-for-mac"></a>Visual Studio for Mac 簡介

[Visual Studio for Mac](/visualstudio/mac/) 遵循將程式碼組織成 *方案* 和 *專案* 的 Visual Studio 做法。 方案是可以容納一或多個專案的容器。 專案可以是應用程式、支援程式庫、測試應用程式等等。 Notes 應用程式是由一個包含三項專案的方案所組成，如下列螢幕擷取畫面所示：

![Visual Studio for Mac 方案窗格](deepdive-images/vsmac/solution.png)

這些專案包括：

- Notes – 此專案為包含所有共用程式碼與共用 UI 的 .NET Standard 程式庫專案。
- Notes.Android – 此專案包含 Android 特定程式碼，且為 Android 應用程式的進入點。
- Notes.iOS – 此專案包含 iOS 特定程式碼，且為 iOS 應用程式的進入點。

## <a name="anatomy-of-a-xamarinforms-application"></a>Xamarin.Forms應用程式的剖析

下列螢幕擷取畫面顯示 Visual Studio for Mac 中 Note .NET Standard 程式庫專案的內容：

![Phoneword .NET Standard 程式庫專案內容](deepdive-images/vsmac/net-standard-project.png)

專案具有相依性 **節點，** 其中包含 **NuGet** 和 **SDK** 節點：

- **NuGet** &ndash; 已 Xamarin.Forms Xamarin.Essentials 新增至專案的、、Newtonsoft.Js，以及 sqlite-net-pcl NuGet 套件。
- **SDK** &ndash;`NETStandard.Library` 中繼套件，其參考定義 .NET Standard 的一組完整 NuGet 套件。

::: zone-end

專案也包含多個檔案：

- **資料\NoteDatabase.cs** – 此類別包含的程式碼可建立資料庫，並從中讀取資料、將資料寫入其中，以及從中刪除資料。
- **模型\Note.cs** – 此類別可定義 `Note` 模型，該模型的執行個體會儲存應用程式中每個 Note 的相關資料。
- **Views\AboutPage.xaml** –類別的 xaml 標記 `AboutPage` ，它會定義 ABOUT 頁面的 UI。
- **Views\AboutPage.xaml.cs** –類別的程式碼後端 `AboutPage` ，其中包含使用者與頁面互動時所執行的商務邏輯。
- **Views\NotesPage.xaml** –類別的 xaml 標記 `NotesPage` ，定義應用程式啟動時顯示的頁面 UI。
- **Views\NotesPage.xaml.cs** –類別的程式碼後端 `NotesPage` ，其中包含使用者與頁面互動時所執行的商務邏輯。
- **Views\NoteEntryPage.xaml** –類別的 xaml 標記 `NoteEntryPage` ，它會定義使用者輸入備註時所顯示之頁面的 UI。
- **Views\NoteEntryPage.xaml.cs** –類別的程式碼後端 `NoteEntryPage` ，其中包含使用者與頁面互動時所執行的商務邏輯。
- **App.xaml** - `App` 類別的 XAML 標記，可定義應用程式的資源字典。
- **App.xaml.cs** –類別的程式碼後端 `App` ，負責具現化 Shell 應用程式，以及處理應用程式生命週期事件。
- **AppShell** -類別的 xaml 標記 `AppShell` ，它會定義應用程式的視覺化階層。
- **AppShell.xaml.cs** –類別的程式碼後端 `AppShell` ，它會建立的路由，以便透過程式設計的 `NoteEntryPage` 方式來流覽。
- **AssemblyInfo.cs** – 此檔案包含與專案相關、適用於組件層級的應用程式屬性。

如需有關 Xamarin.iOS 應用程式結構的詳細資訊，請參閱 [Xamarin.iOS 應用程式的結構](~/ios/get-started/hello-ios/hello-ios-deepdive.md#anatomy-of-a-xamarinios-application)。 如需有關 Xamarin.Android 應用程式結構的詳細資訊，請參閱 [Xamarin.Android 應用程式的結構](~/android/get-started/hello-android/hello-android-deepdive.md#anatomy)。

## <a name="architecture-and-application-fundamentals"></a>架構與應用程式基本概念

Xamarin.Forms應用程式的架構方式與傳統的跨平臺應用程式相同。 共用程式碼通常放在 .NET Standard 程式庫中，而平台專用的應用程式則會取用共用程式碼。 下圖表顯示 Notes 應用程式關聯性的概觀：

![Notes 架構](deepdive-images/architecture.png)

為了將啟動程式碼的重複使用最大化， Xamarin.Forms 應用程式有一個名為的類別， `App` 負責在每個平臺上將應用程式具現化，如下列程式碼範例所示：

```csharp
using Xamarin.Forms;

namespace Notes
{
    public partial class App : Application
    {
        public App()
        {
            InitializeComponent();
            MainPage = new AppShell();
        }
        // ...
    }
}
```

此程式碼會將 `MainPage` 類別的屬性設定 `App` 為 `AppShell` 物件。 `AppShell`類別會定義應用程式的視覺化階層。 Shell 會採用此視覺化階層，並為其產生使用者介面。 如需定義應用程式視覺階層的詳細資訊，請參閱 [應用程式視覺](#application-visual-hierarchy)階層。

此外，**AssemblyInfo.cs** 檔案還包含適用於組件層級的單一應用程式屬性：

```csharp
using Xamarin.Forms.Xaml;

[assembly: XamlCompilation(XamlCompilationOptions.Compile)]
```

[`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute)屬性會開啟 xaml 編譯器，以便將 xaml 直接編譯成中繼語言。 如需詳細資訊，請參閱 [XAML 編譯](~/xamarin-forms/xaml/xamlc.md)。

## <a name="launch-the-application-on-each-platform"></a>在每個平臺上啟動應用程式

在每個平臺上啟動應用程式的方式是平臺專用的。

### <a name="ios"></a>iOS

若要 Xamarin.Forms 在 iOS 中啟動初始頁面，請注意 ios 專案會定義 `AppDelegate` 繼承自類別的類別 `FormsApplicationDelegate` ：

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

覆 `FinishedLaunching` 寫會藉 Xamarin.Forms 由呼叫方法來初始化架構 `Init` 。 這會導致在 Xamarin.Forms 呼叫方法來設定根視圖控制器之前，在應用程式中載入的 iOS 特定執行 `LoadApplication` 。

### <a name="android"></a>Android

若要在 Android 中啟動初始 Xamarin.Forms 頁面，請注意 android 專案所包含的程式碼會使用 `Activity` 屬性建立，而活動會 `MainLauncher` 繼承自 `FormsAppCompatActivity` 類別：

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

覆 `OnCreate` 寫會藉 Xamarin.Forms 由呼叫方法來初始化架構 `Init` 。 這會導致在載入應用程式之前，先將 Android 特定的執行 Xamarin.Forms 載入應用程式 Xamarin.Forms 。

## <a name="application-visual-hierarchy"></a>應用程式視覺階層

Xamarin.Forms Shell 應用程式會在子類別化類別的類別中，定義應用程式的視覺化階層 `Shell` 。 在 Notes 應用程式中，這是 `Appshell` 類別：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Notes.Views"
       x:Class="Notes.AppShell">
    <TabBar>
        <ShellContent Title="Notes"
                      Icon="icon_feed.png"
                      ContentTemplate="{DataTemplate views:NotesPage}" />
        <ShellContent Title="About"
                      Icon="icon_about.png"
                      ContentTemplate="{DataTemplate views:AboutPage}" />
    </TabBar>
</Shell>
```

這個 XAML 是由兩個主要物件所組成：

- `TabBar`. 代表底部索引標籤列 `TabBar` ，當應用程式的導覽模式使用底部索引標籤時，則應使用此選項。 `TabBar` 物件是 `Shell` 物件的子系。
- `ShellContent`，代表中每個索引標籤的 `ContentPage` 物件 `TabBar` 。 每個 `ShellContent` 物件都是物件的子系 `TabBar` 。

這些物件不代表任何使用者介面，而是應用程式視覺階層的組織。 Shell 將會採用這些物件，並產生用於內容的導覽使用者介面。 因此， `AppShell` 類別定義會定義可從底部索引標籤導覽的兩個頁面。 頁面會視需要建立，以回應使用者導覽。

如需 Shell 應用程式的詳細資訊，請參閱[ Xamarin.Forms shell](~/xamarin-forms/app-fundamentals/shell/index.md)。

## <a name="user-interface"></a>使用者介面

有幾個控制項群組可用來建立應用程式的使用者介面 Xamarin.Forms ：

1. **頁面** – Xamarin.Forms 頁面代表跨平臺行動應用程式畫面。 Notes 應用程式會使用 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 類別來顯示單一畫面。 如需有關頁面的詳細資訊，請參閱[ Xamarin.Forms 頁面](~/xamarin-forms/user-interface/controls/pages.md)。
1. **Views** - Xamarin.Forms views 是顯示在使用者介面上的控制項，例如標籤、按鈕和文字輸入方塊。 完成的 Notes 應用程式會使用 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 、 [`Editor`](xref:Xamarin.Forms.Editor) 和 [`Button`](xref:Xamarin.Forms.Button) views。 如需有關 views 的詳細資訊，請參閱[ Xamarin.Forms views](~/xamarin-forms/user-interface/controls/views.md)。
1. **版面** 配置–配置 Xamarin.Forms 是用來將視圖組成邏輯結構的容器。 Notes 應用程式會使用 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 類別來排列垂直堆疊中的視圖，以及 [`Grid`](xref:Xamarin.Forms.Grid) 水準排列按鈕的類別。 如需版面配置的詳細資訊，請參閱[ Xamarin.Forms 版面](~/xamarin-forms/user-interface/controls/layouts.md)配置。

在執行階段，每個控制項將會對應到其原生對等項目，也就是將呈現的項目。

### <a name="layout"></a>Layout

Notes 應用程式會使用 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 來簡化跨平臺應用程式開發，方法是在畫面上自動排文視圖，而不管螢幕大小。 每個子項目都是以加入這些子項目的順序，一個接著一個地水平或垂直放置。 要使用多少空間 `StackLayout` 取決於 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 和屬性的設定方式 [`VerticalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) ，但根據預設， `StackLayout` 將會嘗試使用整個畫面。

下列 XAML 程式碼顯示使用來配置的範例 [`StackLayout`](xref:Xamarin.Forms.StackLayout) `NoteEntryPage` ：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Notes.Views.NoteEntryPage"
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

根據預設，會 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 假設是垂直方向。 不過，您可以藉由將 [`StackLayout.Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) 屬性設定為列舉成員，將其變更為水準方向 [`StackOrientation.Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal) 。

> [!NOTE]
> 您可以透過 `HeightRequest` 和 `WidthRequest` 屬性來設定檢視的大小。

如需類別的詳細資訊 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ，請參閱[ Xamarin.Forms StackLayout](~/xamarin-forms/user-interface/layouts/stacklayout.md)。

### <a name="responding-to-user-interaction"></a>回應使用者互動

在 XAML 中定義的物件可以引發程式碼後置檔案中處理的事件。 下列程式碼範例顯示 `OnSaveButtonClicked` 類別的程式碼後端中的方法 `NoteEntryPage` ，此方法會在回應 [`Clicked`](xref:Xamarin.Forms.Button.Clicked) [ *儲存* ] 按鈕上的事件引發時執行。

```csharp
async void OnSaveButtonClicked(object sender, EventArgs e)
{
    var note = (Note)BindingContext;
    note.Date = DateTime.UtcNow;
    if (!string.IsNullOrWhiteSpace(note.Text))
    {
        await App.Database.SaveNoteAsync(note);
    }
    await Shell.Current.GoToAsync("..");
}
```

`OnSaveButtonClicked` 方法會將 Note 儲存在資料庫中，然後巡覽回上一頁。 如需導覽的詳細資訊，請參閱 [導覽](#navigation)。

> [!NOTE]
> XAML 類別的程式碼後置檔案可以存取在 XAML 中使用獲指派名稱及 `x:Name` 屬性定義的物件。 指派給此屬性的值與 C# 變數具有相同的規則，因為該值的開頭必須是字母或底線，而且不得包含內嵌的空格。

[儲存] 按鈕與 `OnSaveButtonClicked` 方法會以 `NoteEntryPage` 類別的 XAML 標記連接：

```xaml
<Button Text="Save"
        Clicked="OnSaveButtonClicked" />
```

### <a name="lists"></a>清單

[`CollectionView`](xref:Xamarin.Forms.CollectionView)負責顯示清單中的專案集合。 依預設，會垂直顯示清單專案，而且每個專案都會顯示在單一資料列中。

下列程式碼範例會顯示 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 來自的 `NotesPage` ：

```xaml
<CollectionView x:Name="collectionView"
                Margin="{StaticResource PageMargin}"
                SelectionMode="Single"
                SelectionChanged="OnSelectionChanged">
    <CollectionView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical"
                           ItemSpacing="10" />
    </CollectionView.ItemsLayout>
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <StackLayout>
                <Label Text="{Binding Text}"
                       FontSize="Medium" />
                <Label Text="{Binding Date}"
                       TextColor="{StaticResource TertiaryColor}"
                       FontSize="Small" />
            </StackLayout>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

中的每個資料列的配置 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 都是在專案中定義 [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) ，並且使用資料系結來顯示應用程式所抓取的任何附注。 在 [`CollectionView.ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) **NotesPage.xaml.cs** 中，屬性會設定為數據源：

```csharp
protected override async void OnAppearing()
{
    base.OnAppearing();

    collectionView.ItemsSource = await App.Database.GetNotesAsync();
}
```    

這段程式碼會將 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 儲存在資料庫中的任何備註填入，並在頁面出現時執行。

在中選取專案時，就會 [`CollectionView`](xref:Xamarin.Forms.CollectionView) `SelectionChanged` 引發事件。 當事件引發時，就會執行名為 `OnSelectionChanged` 的事件處理常式：

```csharp
async void OnSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    if (e.CurrentSelection != null)
    {
        // ...
    }
}
```

`SelectionChanged`事件可以透過屬性存取與專案相關聯的物件 `e.CurrentSelection` 。

如需類別的詳細資訊 [`CollectionView`](xref:Xamarin.Forms.CollectionView) ，請參閱[ Xamarin.Forms CollectionView](~/xamarin-forms/user-interface/collectionview/index.md)。

## <a name="navigation"></a>導覽

導覽是在 Shell 應用程式中，透過指定要導覽的 URI 執行的。 導覽 Uri 有三個元件：

- *路由*：定義當作 Shell 視覺階層一部分存在之內容的路徑。
- *頁面*。 Shell 視覺階層中不存在的頁面可以從 Shell 應用程式中的任何位置推送到導覽堆疊上。 例如， `NoteEntryPage` 未在 Shell 視覺階層中定義，但可以視需要推送至導覽堆疊。
- 一或多個 *查詢參數*。 查詢參數是導覽時可以傳遞至目的地頁面的參數。

流覽 URI 不一定要包含這三個元件，但是當它的結構是：//route/page？ queryParameters

> [!NOTE]
> 您可以透過屬性，在 Shell 視覺階層中的元素上定義路由 `Route` 。 但是，如果 `Route` 未設定此屬性（例如在 Notes 應用程式中），則會在執行時間產生路由。

如需有關 Shell 導覽的詳細資訊，請參閱[ Xamarin.Forms shell 導覽](~/xamarin-forms/app-fundamentals/shell/navigation.md)。

### <a name="register-routes"></a>註冊路由

若要流覽至不存在於 Shell 視覺階層中的頁面，您必須先向 Shell 路由系統註冊。 使用 `Routing.RegisterRoute` 方法。 在 Notes 應用程式中，這會發生在函式中 `AppShell` ：

```csharp
public partial class AppShell : Shell
{
    public AppShell()
    {
        // ...
        Routing.RegisterRoute(nameof(NoteEntryPage), typeof(NoteEntryPage));
    }
}
```

在此範例中， `NoteEntryPage` 會針對型別註冊名為的路由 `NoteEntryPage` 。 然後，您可以從應用程式中的任何位置，使用以 URI 為基礎的導覽來流覽此頁面。

### <a name="perform-navigation"></a>執行導覽

導覽是由方法所執行 `GoToAsync` ，該方法接受代表要流覽至之路由的引數：

```csharp
await Shell.Current.GoToAsync("NoteEntryPage");
```

在此範例中， `NoteEntryPage` 會流覽至。

> [!IMPORTANT]
> 當瀏覽器堆疊不在 Shell 視覺階層中的頁面時，就會建立導覽堆疊。

流覽至頁面時，可以將資料以查詢參數的形式傳遞至頁面：

```csharp
async void OnSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    if (e.CurrentSelection != null)
    {
        // Navigate to the NoteEntryPage, passing the ID as a query parameter.
        Note note = (Note)e.CurrentSelection.FirstOrDefault();
        await Shell.Current.GoToAsync($"{nameof(NoteEntryPage)}?{nameof(NoteEntryPage.ItemId)}={note.ID.ToString()}");
    }
}
```

這個範例會抓取中目前選取的專案 [`CollectionView`](xref:Xamarin.Forms.CollectionView) ，並流覽至 `NoteEntryPage` ，並將物件的 `ID` 屬性值做為 `Note` 查詢參數傳遞至 `NoteEntryPage.ItemId` 屬性。

若要接收傳遞的資料， `NoteEntryPage` 類別會以 `QueryPropertyAttribute`

```csharp
[QueryProperty(nameof(ItemId), nameof(ItemId))]
public partial class NoteEntryPage : ContentPage
{
    public string ItemId
    {
        set
        {
            LoadNote(value);
        }
    }
    // ...
}
```

的第一個引數 `QueryPropertyAttribute` `ItemId` 會指定屬性將接收傳遞的資料，第二個引數指定查詢參數識別碼。因此， `QueryPropertyAttribute` 上述範例中的 `ItemId` 會指定屬性將 `ItemId` 從方法呼叫中的 URI 接收查詢參數中傳遞的資料 `GoToAsync` 。 `ItemId`然後，屬性會呼叫 `LoadNote` 方法來從裝置取得便箋。

藉由指定 ".." 作為方法的引數來執行回溯導覽 `GoToAsync` ：

```csharp
await Shell.Current.GoToAsync("..");
```

如需回溯導覽的詳細資訊，請參閱回溯 [導覽](~/xamarin-forms/app-fundamentals/shell/navigation.md#backwards-navigation)。

## <a name="data-binding"></a>資料繫結

資料系結是用來簡化 Xamarin.Forms 應用程式顯示和與其資料互動的方式。 它會在使用者介面與基礎應用程式之間建立連線。 [`BindableObject`](xref:Xamarin.Forms.BindableObject)類別包含許多支援資料系結的基礎結構。

資料繫結會連接兩個物件，稱為 *來源* 和 *目標*。 *來源* 物件會提供資料。 *目標* 物件將會取用 (而且通常會顯示) 來源物件中的資料。 例如，) 的 [`Editor`](xref:Xamarin.Forms.Editor) (*目標* 物件通常會將其屬性系結 [`Text`](xref:Xamarin.Forms.InputView.Text) 至 `string` *來源* 物件中的公用屬性。 下圖說明繫結關聯性：

![資料繫結](deepdive-images/data-binding.png)

資料繫結的主要優點是您不再需要擔心檢視和資料來源之間的資料同步處理。 *來源* 物件中的變更會在幕後，透過繫結架構自動推送至 *目標* 物件，而目標物件中的變更則可以選擇性地推送回 *來源* 物件。

建立資料繫結需要進行兩項步驟：

- [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)*目標* 物件的屬性必須設定為 *來源*。
- *目標* 和 *來源* 之間必須建立繫結。 在 XAML 中，您可以使用標記延伸來達成這個目的 [`Binding`](xref:Xamarin.Forms.Xaml.BindingExtension) 。

在 Notes 應用程式中，系結目標是 [`Editor`](xref:Xamarin.Forms.Editor) 顯示附注的，而將 `Note` 實例設為 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 的是系結 `NoteEntryPage` 來源。 一開始，會在頁面的函式 `BindingContext` `NoteEntryPage` 執行時設定的。

```csharp
public NoteEntryPage()
{
    // ...
    BindingContext = new Note();
}
```

在此範例中， `BindingContext` 當建立時，頁面會設定為新的 `Note` `NoteEntryPage` 。 這會處理將新的附注加入至應用程式的案例。

此外， `BindingContext` 如果在上選取了現有的附注，也可以在流覽至 `NoteEntryPage` 時發生的情況下設定頁面 `NotesPage` 。

```csharp
[QueryProperty(nameof(ItemId), nameof(ItemId))]
public partial class NoteEntryPage : ContentPage
{
    public string ItemId
    {
        set
        {
            LoadNote(value);
        }

        async void LoadNote(string itemId)
        {
            try
            {
                int id = Convert.ToInt32(itemId);
                // Retrieve the note and set it as the BindingContext of the page.
                Note note = await App.Database.GetNoteAsync(id);
                BindingContext = note;
            }
            catch (Exception)
            {
                Console.WriteLine("Failed to load note.");
            }
        }    
        // ...    
    }
}
```

在此範例中，當頁面流覽時，頁面會在 `BindingContext` 從資料庫取出之後，設定為選取的 `Note` 物件。

> [!IMPORTANT]
> 雖然 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 可以個別設定每個 *目標* 物件的屬性，但這並不是必要的。 `BindingContext` 為其所有子系繼承的特殊屬性。 因此，當 `BindingContext` 上的 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 設為實例時，的 `Note` 所有子系 `ContentPage` 都相同 `BindingContext` ，而且可以系結至物件的公用屬性 `Note` 。

接著會系結 [`Editor`](xref:Xamarin.Forms.Editor) `NoteEntryPage` 至 `Text` 物件的屬性 `Note` ：

```xaml
<Editor Placeholder="Enter your note"
        Text="{Binding Text}" />
```

[`Editor.Text`](xref:Xamarin.Forms.InputView.Text)建立屬性與 `Text` *來源* 物件之屬性之間的系結。 在 `Editor` 中所做的變更將會自動傳播到 `Note` 物件。 同樣地，如果對屬性進行了變更，系結 `Note.Text` Xamarin.Forms 引擎也會更新的內容 `Editor` 。 這稱為 *雙向繫結*。

如需資料系結的詳細資訊，請參閱[ Xamarin.Forms 資料](~/xamarin-forms/app-fundamentals/data-binding/index.md)系結。

## <a name="styling"></a>樣式

Xamarin.Forms 應用程式通常會包含多個具有相同外觀的視覺元素。 在設定每個視覺元素的外觀時，可能產生重複且容易出錯。 作為替代，您可以建立定義外觀的樣式，然後套用至必要的視覺元素。

[`Style`](xref:Xamarin.Forms.Style)類別會將屬性值的集合分組到一個物件中，然後再套用至多個視覺元素實例。 樣式會儲存在中 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) ，不論是在應用層級、頁面層級或視圖層級。 選擇要在何處定義 `Style` 會影響其可使用的位置：

- [`Style`](xref:Xamarin.Forms.Style) 在應用層級定義的實例可以套用到整個應用程式。
- [`Style`](xref:Xamarin.Forms.Style) 在頁面層級定義的實例可以套用至頁面和其子系。
- [`Style`](xref:Xamarin.Forms.Style) 在視圖層級定義的實例可以套用到視圖和其子系。

> [!IMPORTANT]
> 應用程式中所使用任何樣式都儲存於應用程式的資源字典中，以避免重複。 不過，頁面特有的 XAML 不應包含於應用程式的資源字典中，因為資源接著將在應用程式啟動時 (而非在頁面要求時) 進行剖析。 如需詳細資訊，請參閱 [減少應用程式資源字典的大小](~/xamarin-forms/deploy-test/performance.md#reduce-the-application-resource-dictionary-size)。

每個 [`Style`](xref:Xamarin.Forms.Style) 實例都包含一個或多個 [`Setter`](xref:Xamarin.Forms.Setter) 物件的集合，每個物件都 `Setter` 有 [`Property`](xref:Xamarin.Forms.Setter.Property) 和 [`Value`](xref:Xamarin.Forms.Setter.Value) 。 `Property` 為樣式所套用元素的可繫結屬性名稱，且 `Value` 為套用至屬性的值。 下列程式碼範例示範 `NoteEntryPage` 的樣式：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Notes.Views.NoteEntryPage"
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

此樣式會套用至 [`Editor`](xref:Xamarin.Forms.Editor) 頁面上的任何實例。

建立時 [`Style`](xref:Xamarin.Forms.Style) ， [`TargetType`](xref:Xamarin.Forms.Style.TargetType) 一律需要屬性。

> [!NOTE]
> Xamarin.Forms傳統上使用 XAML 樣式來設定應用程式的樣式。 不過， Xamarin.Forms 也支援使用階層式樣式表 (CSS) 來設定視覺元素樣式。 如需詳細資訊，請參閱 [ Xamarin.Forms 使用階層式樣式表 (CSS) 設定應用程式的樣式 ](~/xamarin-forms/user-interface/styles/css/index.md)。

如需 XAML 樣式的詳細資訊，請參閱 [ Xamarin.Forms 使用 xaml 樣式設定應用程式](~/xamarin-forms/user-interface/styles/xaml/index.md)的樣式。

## <a name="test-and-deployment"></a>測試和部署

Visual Studio for Mac 和 Visual Studio 都會提供許多選項來測試和部署應用程式。 為應用程式偵錯是應用程式開發週期的共同部分，有助於診斷程式碼問題。 如需詳細資訊，請參閱[設定中斷點](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint)、[逐步執行程式碼](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/step_through_code)，以及[將資訊輸出至記錄視窗](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/output_information_to_log_window)。

模擬器是開始部署與測試應用程式的絕佳位置，而且具備用於測試應用程式的實用功能。 不過，使用者將不會在模擬器中取用最終的應用程式，因此應該及早且經常在實際裝置上測試應用程式。 如需有關 iOS 裝置佈建的詳細資訊，請參閱[裝置佈建](~/ios/get-started/installation/device-provisioning/index.md)。 如需有關 Android 裝置佈建的詳細資訊，請參閱[設定您的裝置以進行開發](~/android/get-started/installation/set-up-device-for-development.md)。

## <a name="next-steps"></a>下一步

本深入探討已使用 Shell 來檢查應用程式開發的基本概念 Xamarin.Forms 。 建議的後續步驟包括了解下列功能：

- Xamarin.Forms Shell 會提供大部分行動應用程式所需的基本功能，藉此降低行動應用程式開發的複雜度。 如需詳細資訊，請參閱[ Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md)。
- 有幾個控制項群組可用來建立應用程式的使用者介面 Xamarin.Forms 。 如需詳細資訊，請參閱[控制項參考](~/xamarin-forms/user-interface/controls/index.md)。
- 資料繫結是連結兩個物件屬性的技術，以便在其中一個屬性變更時，自動反映在另一個屬性上。 如需詳細資訊，請參閱[資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)。
- Xamarin.Forms 根據所使用的頁面類型，提供多個頁面導覽體驗。 如需詳細資訊，請參閱[導覽](~/xamarin-forms/app-fundamentals/navigation/index.md)。
- 樣式功能有助於減少重複的標記，並可更輕鬆地變更應用程式外觀。 如需詳細資訊，請參閱設定 [ Xamarin.Forms 應用程式的樣式](~/xamarin-forms/user-interface/styles/index.md)。
- 資料範本可以針對支援的檢視，定義資料的呈現方式。 如需詳細資訊，請參閱[資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。
- 效果也可以允許在每個平台上自訂原生控制項。 系統會將類別子類別化，以在平臺特定專案中建立效果 [`PlatformEffect`](xref:Xamarin.Forms.PlatformEffect`2) ，並將其附加至適當的控制項來取用 Xamarin.Forms 。 如需詳細資訊，請參閱[效果](~/xamarin-forms/app-fundamentals/effects/index.md)。
- 系統會在每個平台上使用 `Renderer` 類別，以不同的方式呈現每個頁面、版面配置和檢視，進而建立原生控制項、將其排列在畫面上，然後加入在共用程式碼中指定的行為。 開發人員可以實作自己的自訂 `Renderer` 類別，以自訂控制項的外觀及/或行為。 如需詳細資訊，請參閱[自訂呈現方式](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)。
- 共用程式碼可以透過 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 類別存取原生功能。 如需有關詳細資訊，請參閱[透過 DependencyService 存取原生功能](~/xamarin-forms/app-fundamentals/dependency-service/index.md)。

## <a name="related-links"></a>相關連結

- [Xamarin.Forms 殼](~/xamarin-forms/app-fundamentals/shell/index.md)
- [eXtensible Application Markup Language (XAML)](~/xamarin-forms/xaml/index.yml)
- [資料系結](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [控制項參考](~/xamarin-forms/user-interface/controls/index.md)
- [開始範例](/samples/browse/?products=xamarin&term=Xamarin.Forms%2bget%2bstarted)
- [Xamarin.Forms 樣品](/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.Forms API 參考](xref:Xamarin.Forms)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Series/Xamarin-101/Xamarin-Solution-Architecture-4-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
