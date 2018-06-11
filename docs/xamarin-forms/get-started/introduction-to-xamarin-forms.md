---
title: Xamarin.Forms 簡介
description: 本文提供 Xamarin.Forms 的簡介，以及如何使用它開始撰寫應用程式。
ms.prod: xamarin
ms.assetid: f619595f-3ee7-439b-a1bc-d13e5106e6e9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/02/2016
ms.openlocfilehash: 15a26ce633e8321e9101289276c9da302e5bd8cc
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2018
ms.locfileid: "35243690"
---
# <a name="an-introduction-to-xamarinforms"></a>Xamarin.Forms 簡介

_Xamarin.Forms 是一個以原生方式跨平台支援的 UI 工具組抽象概念，可讓開發人員輕鬆地建立可以跨 Android、iOS、Windows 和通用 Windows 平台共用的使用者介面。系統會使用目標平台的原生控制項呈現使用者介面，讓 Xamarin.Forms 應用程式保留每個平台適當的外觀及操作。本文介紹 Xamarin.Forms，以及如何開始使用它來撰寫應用程式。_

<a name="Overview" />

## <a name="overview"></a>總覽

Xamarin.Forms 是一個可讓開發人員快速建立跨平台使用者介面的架構。 它對於使用者介面有自己的抽象概念，會使用 iOS、Android 或通用 Windows 平台 (UWP) 上的原生控制項來呈現。 也就是說，應用程式可以共用大部分的使用者介面程式碼，而且仍然保留目標平台的原生外觀及操作。

Xamarin.Forms 允許快速開發可能會隨著時間的推移而演化為複雜應用程式的應用程式原型。 Xamarin.Forms 應用程式屬於原生應用程式，因此沒有其他工具組的限制，例如，瀏覽器沙箱功能、受限的 API 或效能不佳。 使用 Xamarin.Forms 撰寫的應用程式可以使用任何 API 或基礎平台的功能，例如 (但不限於) iOS 上的 CoreMotion、PassKit 和 StoreKit；Android 上的 NFC 和 Google Play 服務；以及 Windows 上的磚。 此外，您可以建立將使用 Xamarin.Forms 建立其使用者介面部分，同時使用原生 UI 工具組建立其他部分的應用程式。

Xamarin.Forms 應用程式的架構方式與傳統的跨平台應用程式相同。 最常見的方法是使用[可攜式類別庫](~/cross-platform/app-fundamentals/pcl.md)或[共用專案](~/cross-platform/app-fundamentals/shared-projects.md)存放共用程式碼，並建立將會取用共用程式碼的平台專屬應用程式。

在 Xamarin.Forms 中建立使用者介面有兩種技術。 第一種技術是完全使用 C# 原始程式碼建立 UI。 第二種技術是使用*可延伸應用程式標記語言* (XAML)，這是一種宣告式的標記語言，用於描述使用者介面。 如需 XAML 的詳細資訊，請參閱 [XAML 基本知識](~/xamarin-forms/xaml/xaml-basics/index.md)。

本文討論 Xamarin.Forms 架構的基本概念，並涵蓋下列主題：

-  [檢查 Xamarin.Forms 應用程式](#Examining_A_Xamarin.Forms_Application)。
-  [如何使用 Xamarin.Forms 頁面和控制項](#Views_and_Layouts)。
-  [如何使用顯示資料清單](#Lists_in_Xamarin.Forms)。
-  [如何設定資料繫結](#Data_Binding)。
-  [如何在頁面之間導覽](#Navigation)。
-  [後續步驟](#Next_Steps)。

<a name="Examining_A_Xamarin_Forms_Application" />

### <a name="examining-a-xamarinforms-application"></a>檢查 Xamarin.Forms 應用程式

在 Visual Studio for Mac 和 Visual Studio 中，預設 Xamarin.Forms 應用程式範本會建立可向使用者顯示文字的最簡單 Xamarin.Forms 方案。 如果您執行應用程式，其外觀應該類似以下的螢幕擷取畫面：

[![](introduction-to-xamarin-forms-images/image05-sml.png "預設 Xamarin.Forms 應用程式")](introduction-to-xamarin-forms-images/image05.png#lightbox "預設 Xamarin.Forms 應用程式")

螢幕擷取畫面中的每個畫面都對應至 Xamarin.Forms 中的*頁面*。 [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) 在 Android 中代表「活動」、在 iOS 中代表「檢視控制器」、在通用 Windows 平台上 (UWP) 則代表「頁面」。 上方螢幕擷取畫面中的範例會將 [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) 物件具現化，並使用該物件顯示 [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)。

若要將啟動程式碼的重複使用發揮到極致，Xamarin.Forms 應用程式有一個名為 `App` 的單一類別，此類別負責具現化將顯示的第一個 [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)。 下列程式碼中可以看到 `App` 類別的範例：

```csharp
public class App : Application
{
  public App ()
  {
    MainPage = new ContentPage {
      Content =  new Label
      {
          Text = "Hello, Forms !",
          VerticalOptions = LayoutOptions.CenterAndExpand,
          HorizontalOptions = LayoutOptions.CenterAndExpand,
      }
      };
  }
}
```

此程式碼會將頁面上以垂直置中和水平置中顯示單一 [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) 的新 [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) 物件具現化。

<a name="Launching_the_Initial_Xamarin_Forms_Page_on_Each_Platform" />

### <a name="launching-the-initial-xamarinforms-page-on-each-platform"></a>在每個平台上啟動 Xamarin.Forms 初始頁面

若要使用應用程式內的這個 [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)，每個平台應用程式都必須將 Xamarin.Forms 架構初始化，並在啟動時提供 [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) 的執行個體。 這個初始化步驟會因為平台不同而互異，以下各節將會討論該步驟。

<a name="Launching_in_iOS" />

#### <a name="ios"></a>iOS

若要在 iOS 中啟動 Xamarin.Forms 初始頁面，平台專案會包含繼承自 `Xamarin.Forms.Platform.iOS.FormsApplicationDelegate` 類別的 `AppDelegate` 類別，如下列程式碼範例所示：

```csharp
[Register("AppDelegate")]
public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate
{
    public override bool FinishedLaunching(UIApplication app, NSDictionary options)
    {
      global::Xamarin.Forms.Forms.Init ();
      LoadApplication (new App ());
      return base.FinishedLaunching (app, options);
    }
}
```

`FinishedLoading` 覆寫會呼叫 `Init` 方法，藉此初始化 Xamarin.Forms 架構。 如此會在 `LoadApplication` 方法的呼叫設定根檢視控制器之前，先在應用程式中載入 iOS 專用的 Xamarin.Forms 實作。

<a name="Launching_in_Android" />

#### <a name="android"></a>Android

若要在 Android 中啟動 Xamarin.Forms 初始頁面，平台專案包含的程式碼可利用 `MainLauncher` 屬性、繼承自 `FormsApplicationActivity` 類別的活動，建立 `Activity`，如下列程式碼範例所示：

```csharp
namespace HelloXamarinFormsWorld.Android
{
    [Activity(Label = "HelloXamarinFormsWorld", MainLauncher = true,
        ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);
            Xamarin.Forms.Forms.Init(this, bundle);
            LoadApplication (new App ());
        }
    }
}
```

`OnCreate` 覆寫會呼叫 `Init` 方法，藉此初始化 Xamarin.Forms 架構。 如此會在載入 Xamarin.Forms 應用程式之前，先在應用程式中載入 Android 專用的 Xamarin.Forms 實作。

#### <a name="universal-windows-platform"></a>通用 Windows 平台

在通用 Windows 平台 (UWP) 應用程式中，初始化 Xamarin.Forms 架構的 `Init` 方法是從 `App` 類別叫用的：

```csharp
Xamarin.Forms.Forms.Init (e);

if (e.PreviousExecutionState == ApplicationExecutionState.Terminated)
{
  ...
}
```

如此會在應用程式中載入 UWP 專用的 Xamarin.Forms 實作。 如下列程式碼範例所示，Xamarin.Forms 的初始頁面是由 `MainPage` 類別所啟動：

```csharp
public partial class MainPage
{
    public MainPage()
    {
      this.InitializeComponent();
      this.LoadApplication(new HelloXamarinFormsWorld.App());
    }
}
```

Xamarin.Forms 應用程式是以 `LoadApplication` 方法來載入。

<a name="Views_and_Layouts" />

### <a name="views-and-layouts"></a>檢視和版面配置

有四個主要的控制項群組可用來建立 Xamarin.Forms 應用程式的使用者介面。

1. **頁面** - Xamarin.Forms 頁面代表跨平台行動應用程式畫面。 如需有關頁面的詳細資訊，請參閱 [Xamarin.Forms 頁面](~/xamarin-forms/user-interface/controls/pages.md)。
1. **版面配置** - Xamarin.Forms 版面配置是將檢視構成邏輯結構所使用的容器。 如需有關版面配置的詳細資訊，請參閱 [Xamarin.Forms 版面配置](~/xamarin-forms/user-interface/controls/layouts.md)。
1. **檢視** - Xamarin.Forms 檢視是顯示在使用者介面上的控制項，例如標籤、按鈕和文字輸入方塊。 如需有關檢視的詳細資訊，請參閱 [Xamarin.Forms 檢視](~/xamarin-forms/user-interface/controls/views.md)。
1. **資料格** - Xamarin.Forms 資料格是在清單中用於項目的特定元素，並描述如何在清單中繪製每個項目。 如需有關資料格的詳細資訊，請參閱 [Xamarin.Forms 資料格](~/xamarin-forms/user-interface/controls/cells.md)。

在執行階段，每個控制項將會對應到其原生對等項目，也就是將呈現的項目。

控制項裝載在版面配置內。 實作常用版面配置的 [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) 類別現在會經過檢查。

<a name="StackLayout" />

#### <a name="stacklayout"></a>StackLayout

[`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) 會自動在不同大小的螢幕上排列控制項，藉以簡化跨平台應用程式的開發。 每個子項目都是以加入這些子項目的順序，一個接著一個地水平或垂直放置。 `StackLayout` 將使用的空間量將取決於設定 [`HorizontalOptions`](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) 和 [`VerticalOptions`](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) 屬性的方式，但預設 `StackLayout` 會嘗試使用整個螢幕。

下列 XAML 程式碼示範使用 [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) 排列三個 [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) 控制項的範例：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml" x:Class="HelloXamarinFormsWorldXaml.StackLayoutExample1" Padding="20">
  <StackLayout Spacing="10">
    <Label Text="Stop" BackgroundColor="Red" Font="20" />
    <Label Text="Slow down" BackgroundColor="Yellow" Font="20" />
    <Label Text="Go" BackgroundColor="Green" Font="20" />
  </StackLayout>
</ContentPage>
```

對等的 C# 程式碼會顯示在以下程式碼範例中：

```csharp
public class StackLayoutExample : ContentPage
{
    public StackLayoutExample()
    {
        Padding = new Thickness(20);
        var red = new Label
        {
            Text = "Stop", BackgroundColor = Color.Red, FontSize = 20
        };
        var yellow = new Label
        {
            Text = "Slow down", BackgroundColor = Color.Yellow, FontSize = 20
        };
        var green = new Label
        {
            Text = "Go", BackgroundColor = Color.Green, FontSize = 20
        };

        Content = new StackLayout
        {
            Spacing = 10,
            Children = { red, yellow, green }
        };
    }
}
```

根據預設，[`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) 假設是垂直方向，如下列螢幕擷取畫面所示：

[![](introduction-to-xamarin-forms-images/image09-sml.png "垂直 StackLayout")](introduction-to-xamarin-forms-images/image09.png#lightbox "垂直 StackLayout")

[`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) 的方向可以變更為水平方向，如下列 XAML 程式碼範例所示：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml" x:Class="HelloXamarinFormsWorldXaml.StackLayoutExample2" Padding="20">
  <StackLayout Spacing="10" VerticalOptions="End" Orientation="Horizontal" HorizontalOptions="Start">
    <Label Text="Stop" BackgroundColor="Red" Font="20" />
    <Label Text="Slow down" BackgroundColor="Yellow" Font="20" />
    <Label Text="Go" BackgroundColor="Green" Font="20" />
  </StackLayout>
</ContentPage>
```

對等的 C# 程式碼會顯示在以下程式碼範例中：

```csharp
public class StackLayoutExample: ContentPage
{
    public StackLayoutExample()
    {
        // Code that creates labels removed for clarity
        Content = new StackLayout
        {
            Spacing = 10,
            VerticalOptions = LayoutOptions.End,
            Orientation = StackOrientation.Horizontal,
            HorizontalOptions = LayoutOptions.Start,
            Children = { red, yellow, green }
        };
    }
}
```

下列螢幕擷取畫面顯示產生的版面配置：

[![](introduction-to-xamarin-forms-images/image10-sml.png "水平 StackLayout")](introduction-to-xamarin-forms-images/image10.png#lightbox "水平 StackLayout")

控制項的大小可以透過 `HeightRequest` 和 `WidthRequest` 屬性設定，如下列 XAML 程式碼範例所示：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml" x:Class="HelloXamarinFormsWorldXaml.StackLayoutExample3" Padding="20">
  <StackLayout Spacing="10" VerticalOptions="End" Orientation="Horizontal" HorizontalOptions="Start">
    <Label Text="Stop" BackgroundColor="Red" Font="20" WidthRequest="100" />
    <Label Text="Slow down" BackgroundColor="Yellow" Font="20" WidthRequest="100" />
    <Label Text="Go" BackgroundColor="Green" Font="20" WidthRequest="200" />
  </StackLayout>
</ContentPage>
```

對等的 C# 程式碼會顯示在以下程式碼範例中：

```csharp
var red = new Label
{
    Text = "Stop", BackgroundColor = Color.Red, FontSize = 20, WidthRequest = 100
};
var yellow = new Label
{
    Text = "Slow down", BackgroundColor = Color.Yellow, FontSize = 20, WidthRequest = 100
};
var green = new Label
{
    Text = "Go", BackgroundColor = Color.Green, FontSize = 20, WidthRequest = 200
};

Content = new StackLayout
{
    Spacing = 10,
    VerticalOptions = LayoutOptions.End,
    Orientation = StackOrientation.Horizontal,
    HorizontalOptions = LayoutOptions.Start,
    Children = { red, yellow, green }
};
```

下列螢幕擷取畫面顯示產生的版面配置：

[![](introduction-to-xamarin-forms-images/image11-sml.png "具有 LayoutOptions 的水平 StackLayout")](introduction-to-xamarin-forms-images/image11.png#lightbox "具有 LayoutOptions 的水平 StackLayout")

如需 [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) 類別的詳細資訊，請參閱 [StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md)。

<a name="Lists_in_Xamarin_Forms" />

## <a name="lists-in-xamarinforms"></a>Xamarin.Forms 中的清單

[`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) 控制項負責在螢幕上顯示項目的集合 – `ListView` 中的每個項目都會包含在單一資料格中。 根據預設，`ListView` 將使用內建 [`TextCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell/) 範本並呈現單行文字。

下列程式碼範例顯示簡單的 [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) 範例：

```csharp
var listView = new ListView
{
    RowHeight = 40
};
listView.ItemsSource = new string []
{
    "Buy pears", "Buy oranges", "Buy mangos", "Buy apples", "Buy bananas"
};
Content = new StackLayout
{
    VerticalOptions = LayoutOptions.FillAndExpand,
    Children = { listView }
};
```

下列螢幕擷取畫面顯示產生的 [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)：

 ![](introduction-to-xamarin-forms-images/image13.png "ListView")

如需 [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) 控制項的詳細資訊，請參閱 [ListView](~/xamarin-forms/user-interface/listview/index.md)。

<a name="Binding_to_a_Custom_Class" />

### <a name="binding-to-a-custom-class"></a>繫結至自訂類別

[`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) 控制項也可以使用預設的 [`TextCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell/) 範本顯示自訂物件。

下列程式碼範例顯示 `TodoItem` 類別：

```csharp
public class TodoItem
{
    public string Name { get; set; }
    public bool Done { get; set; }
}
```

您可以填入 [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) 控制項，如下列程式碼範例所示：

```csharp
listView.ItemsSource = new TodoItem [] {
    new TodoItem { Name = "Buy pears" },
    new TodoItem { Name = "Buy oranges", Done=true} ,
    new TodoItem { Name = "Buy mangos" },
    new TodoItem { Name = "Buy apples", Done=true },
    new TodoItem { Name = "Buy bananas", Done=true }
};
```

您可以建立繫結以設定 [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) 會顯示的 `TodoItem` 屬性，如下列程式碼範例所示：

```csharp
listView.ItemTemplate = new DataTemplate(typeof(TextCell));
listView.ItemTemplate.SetBinding(TextCell.TextProperty, "Name");
```

這會建立可指定 `TodoItem.Name` 屬性路徑的繫結，而且將會產生先前顯示的螢幕擷取畫面。

如需有關繫結至自訂類別的詳細資訊，請參閱 [ListView 資料來源](~/xamarin-forms/user-interface/listview/data-and-databinding.md)。

<a name="Selecting_an_Item_in_a_ListView" />

### <a name="selecting-an-item-in-a-listview"></a>在 ListView 中選取項目

若要回應觸碰 [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) 中資料格的使用者，應該處理 [`ItemSelected`](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemSelected/) 事件，如下列程式碼範例所示：

```csharp
listView.ItemSelected += async (sender, e) => {
    await DisplayAlert("Tapped!", e.SelectedItem + " was tapped.", "OK");
};
```

當包含在 [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) 內時，[`PushAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PushAsync/p/Xamarin.Forms.Page/) 方法可用來開啟具有內建向後導覽功能的新頁面。 [`ItemSelected`](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemSelected/) 事件可以存取透過 [`e.SelectedItem`](https://developer.xamarin.com/api/property/Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem/) 屬性與資料格產生關聯的物件、將該物件繫結至新頁面，並使用 `PushAsync` 顯示新的頁面，如下列程式碼範例所示：

```csharp
listView.ItemSelected += async (sender, e) => {
    var todoItem = (TodoItem)e.SelectedItem;
    var todoPage = new TodoItemPage(todoItem); // so the new page shows correct data
    await Navigation.PushAsync(todoPage);
};
```

每個平台都會以自己的方式實作內建的向後導覽功能。 如需詳細資訊，請參閱[導覽](#Navigation)。

如需有關 [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) 選取項目的詳細資訊，請參閱 [ListView 互動性](~/xamarin-forms/user-interface/listview/interactivity.md)。

<a name="Customizing_the_appearance_of_a_cell" />

### <a name="customizing-the-appearance-of-a-cell"></a>自訂資料格外觀

若要自訂資料格外觀，請將 [`ViewCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) 類別子類別化，然後將此類別的類型設定為 [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) 的 [`ItemTemplate`](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemTemplate/) 屬性。

下列螢幕擷取畫面所示的資料格是由一個 [`Image`](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) 和兩個 [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) 控制項所組成：

 ![](introduction-to-xamarin-forms-images/image14.png "ListView 自訂資料格外觀")

若要建立此自訂版面配置，應該將 [`ViewCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) 類別子類別化，如下列程式碼範例所示：

```csharp
class EmployeeCell : ViewCell
{
    public EmployeeCell()
    {
        var image = new Image
        {
            HorizontalOptions = LayoutOptions.Start
        };
        image.SetBinding(Image.SourceProperty, new Binding("ImageUri"));
        image.WidthRequest = image.HeightRequest = 40;

        var nameLayout = CreateNameLayout();
        var viewLayout = new StackLayout()
        {
           Orientation = StackOrientation.Horizontal,
           Children = { image, nameLayout }
        };
        View = viewLayout;
    }

    static StackLayout CreateNameLayout()
    {
        var nameLabel = new Label
        {
            HorizontalOptions= LayoutOptions.FillAndExpand
        };
        nameLabel.SetBinding(Label.TextProperty, "DisplayName");

        var twitterLabel = new Label
        {
           HorizontalOptions = LayoutOptions.FillAndExpand,
           Font = Fonts.Twitter
        };
        twitterLabel.SetBinding(Label.TextProperty, "Twitter");

        var nameLayout = new StackLayout()
        {
           HorizontalOptions = LayoutOptions.StartAndExpand,
           Orientation = StackOrientation.Vertical,
           Children = { nameLabel, twitterLabel }
        };
        return nameLayout;
    }
}
```

此程式碼會執行下列工作：

-  它會新增 [`Image`](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) 控制項，並將其繫結至 `Employee` 物件的 `ImageUri` 屬性。 如需有關資料繫結的詳細資訊，請參閱[資料繫結](#Data_Binding)。
-  它會建立具有垂直方向的 [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) 以容納兩個 [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) 控制項。 `Label` 控制項會繫結至 `Employee` 物件的 `DisplayName` 和 `Twitter` 屬性。
-  它會建立將要裝載現有 [`Image`](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) 和 `StackLayout` 的 [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)。 它將使用水平方向排列其子系。

一旦建立自訂資料格之後，[`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) 控制項便可以透過將其包裝在 [`DataTemplate`](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) 中來適用該資料格，如下列程式碼範例所示：

```csharp
List<Employee> myListOfEmployeeObjects = GetAListOfAllEmployees();
var listView = new ListView
{
    RowHeight = 40
};
listView.ItemsSource = myListOfEmployeeObjects;
listView.ItemTemplate = new DataTemplate(typeof(EmployeeCell));
```

此程式碼會將 `Employee` 的 `List` 提供給 [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)。 每個資料格都會使用 `EmployeeCell` 類別呈現。 `ListView` 會將 `Employee` 物件傳遞至 `EmployeeCell` 作為其 [`BindingContext`](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/)。

如需有關自訂資料格外觀的詳細資訊，請參閱[資料格外觀](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)。

<a name="Using_XAML_to_Create_and_Customize_A_List" />

### <a name="using-xaml-to-create-and-customize-a-list"></a>使用 XAML 建立與自訂清單

上一節中 [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) 的 XAML 對等項目會顯示在下列程式碼範例中：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:local="clr-namespace:XamarinFormsXamlSample;assembly=XamarinFormsXamlSample"
             xmlns:constants="clr-namespace:XamarinFormsSample;assembly=XamarinFormsXamlSample"
             x:Class="XamarinFormsXamlSample.Views.EmployeeListPage"
             Title="Employee List">
  <ListView x:Name="listView" IsVisible="false" ItemsSource="{x:Static local:App.Employees}" ItemSelected="EmployeeListOnItemSelected">
    <ListView.ItemTemplate>
      <DataTemplate>
        <ViewCell>
          <ViewCell.View>
            <StackLayout Orientation="Horizontal">
              <Image Source="{Binding ImageUri}" WidthRequest="40" HeightRequest="40" />
              <StackLayout Orientation="Vertical" HorizontalOptions="StartAndExpand">
                <Label Text="{Binding DisplayName}" HorizontalOptions="FillAndExpand" />
                <Label Text="{Binding Twitter}" Font="{x:Static constants:Fonts.Twitter}"/>
              </StackLayout>
            </StackLayout>
          </ViewCell.View>
        </ViewCell>
      </DataTemplate>
    </ListView.ItemTemplate>
  </ListView>
</ContentPage>
```

此 XAML 會定義包含 [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) 的 [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)。 `ListView` 的資料來源是透過 [`ItemsSource`](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemsSource/) 屬性設定的。 `ItemsSource` 中每個資料列的配置都是在 [`ListView.ItemTemplate`](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemTemplate/) 元素內定義的。

<a name="Data_Binding" />

## <a name="data-binding"></a>資料繫結

資料繫結會連接兩個物件，稱為*來源*和*目標*。 *來源*物件會提供資料。 *目標*物件將會取用 (而且通常會顯示) 來源物件中的資料。 例如，[`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) (*目標*物件) 通常會將其 [`Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/) 屬性繫結至*來源*物件中的公用 `string` 屬性。 下圖說明繫結關聯性：

![](introduction-to-xamarin-forms-images/data-binding.png "資料繫結")

資料繫結的主要優點是您不再需要擔心檢視和資料來源之間的資料同步處理。 *來源*物件中的變更會在幕後，透過繫結架構自動推送至*目標*物件，而目標物件中的變更則可以選擇性地推送回*來源*物件。

建立資料繫結是兩個步驟的程序：

- *目標*物件的 [`BindingContext`](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) 屬性必須設定為*來源*。
- *目標*和*來源*之間必須建立繫結。 在 XAML 中，可使用 [`Binding`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.BindingExtension/) 標記延伸達到此目的。 在 C# 中，則可以透過 [`SetBinding`](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetBinding/p/Xamarin.Forms.BindableProperty/Xamarin.Forms.BindingBase/) 方法達到此目的。

如需有關資料繫結的詳細資訊，請參閱[資料繫結基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)。

### <a name="xaml"></a>XAML

下列程式碼顯示在 XAML 中執行資料繫結的範例：

```xaml
<Entry Text="{Binding FirstName}" ... />
```

在*來源*物件的 [`Entry.Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.Text/) 屬性和 `FirstName` 屬性之間會建立繫結。 在 `Entry` 控制項中所做的變更將會自動傳播到 `employeeToDisplay` 物件。 同樣地，如果是對 `employeeToDisplay.FirstName` 屬性進行變更，則 Xamarin.Forms 繫結引擎也會更新 `Entry` 控制項的內容。 這稱為*雙向繫結*。 若要讓雙向繫結運作，模型類別必須實作 `INotifyPropertyChanged` 介面。

雖然可以在 XAML 中設定 `EmployeeDetailPage` 類別的 [`BindingContext`](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) 屬性，但在此處是在程式碼後置將其設定為 `Employee` 物件的執行個體：

```csharp
public EmployeeDetailPage(Employee employee)
{
    InitializeComponent();
    this.BindingContext = employee;
}
```

每個*目標*物件的 [`BindingContext`](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) 屬性都可以個別設定，但這並非必要。 `BindingContext` 為其所有子系繼承的特殊屬性。 因此，當 [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) 上的 `BindingContext` 設定為 `Employee` 執行個體時，`ContentPage` 的所有子系都有相同的 `BindingContext`，而且可以繫結至 `Employee` 物件的公用屬性。

### <a name="c35"></a>C&#35;

下列程式碼顯示在 C# 中執行資料繫結的範例：

```csharp
public EmployeeDetailPage(Employee employeeToDisplay)
{
    this.BindingContext = employeeToDisplay;
    var firstName = new Entry()
    {
        HorizontalOptions = LayoutOptions.FillAndExpand
    };
    firstName.SetBinding(Entry.TextProperty, "FirstName");
    ...
}
```

系統會為 [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) 建構函式傳遞 `Employee` 物件的執行個體，並將 [`BindingContext`](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) 設定為要繫結的物件。 系統會將 [`Entry`](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) 控制項具現化，並在*來源*物件的 [`Entry.Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.Text/) 屬性和 `FirstName` 屬性之間建立繫結。 在 `Entry` 控制項中所做的變更將會自動傳播到 `employeeToDisplay` 物件。 同樣地，如果是對 `employeeToDisplay.FirstName` 屬性進行變更，則 Xamarin.Forms 繫結引擎也會更新 `Entry` 控制項的內容。 這稱為*雙向繫結*。 若要讓雙向繫結運作，模型類別必須實作 `INotifyPropertyChanged` 介面。

`SetBinding` 方法會接受兩個參數。 第一個參數會指定繫結類型的相關資訊。 第二個參數用來提供要繫結的項目或繫結方式的相關資訊。 在大部分情況下，第二個參數只是 [`BindingContext`](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) 上容納屬性名稱的字串。 下列語法用來直接繫結至 `BindingContext`：

```csharp
someLabel.SetBinding(Label.TextProperty, new Binding("."));
```

點語法會告知 Xamarin.Forms 使用 [`BindingContext`](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) 作為資料來源，而不是 `BindingContext` 上的屬性。 這在 `BindingContext` 為簡單類型 (例如 `string` 或 `int`) 時非常實用。

<a name="INotifyPropertyChanged" />

### <a name="property-change-notification"></a>屬性變更通知

根據預設，建立繫結時，*目標*物件只會接收*來源*物件的值。 為了讓 UI 與資料來源同步，當*來源*物件變更時，必須有一種方法通知*目標*物件。 這項機制是由 `INotifyPropertyChanged` 介面提供。 當基礎屬性值變更時，實作這個介面將會通知所有資料繫結的控制項。

實作 `INotifyPropertyChanged` 的物件必須在使用新的值更新其中一個屬性時引發 `PropertyChanged` 事件，如下列程式碼範例所示：

```csharp
public class MyObject : INotifyPropertyChanged
{
    public event PropertyChangedEventHandler PropertyChanged;

    string _firstName;
    public string FirstName
    {
        get { return _firstName; }
        set
        {
            if (value.Equals(_firstName, StringComparison.Ordinal))
            {
                // Nothing to do - the value hasn't changed;
                return;
            }
            _firstName = value;
            OnPropertyChanged();

        }
    }

    void OnPropertyChanged([CallerMemberName] string propertyName = null)
    {
        var handler = PropertyChanged;
        if (handler != null)
        {
            handler(this, new PropertyChangedEventArgs(propertyName));
        }
    }
}
```

當 `MyObject.FirstName` 屬性變更時，會叫用將引發 `PropertyChanged` 事件的 `OnPropertyChanged` 方法。 為避免引發不必要的事件，如果屬性值沒有變更，就不會引發 `PropertyChanged` 事件。

請注意，在 `OnPropertyChanged` 方法中，`propertyName` 參數是以 `CallerMemberName` 屬性裝飾。 這可確保使用 `null` 值叫用 `OnPropertyChanged` 方法時，`CallerMemberName` 屬性將提供叫用 `OnPropertyChanged` 之方法的名稱。

<a name="Navigation" />

## <a name="navigation"></a>巡覽

Xamarin.Forms 會根據所使用的 [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) 類型，提供多種不同的網頁導覽體驗。 若是 [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) 執行個體，則有兩種導覽體驗：

- [階層式導覽](#Hierarchical_Navigation)
- [強制回應導覽](#Modal_Navigation)

[`CarouselPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/)、[`MasterDetailPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) 和 [`TabbedPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) 類別會提供替代的導覽體驗。 如需詳細資訊，請參閱[導覽](~/xamarin-forms/app-fundamentals/navigation/index.md)。

<a name="Hierarchical_Navigation" />

### <a name="hierarchical-navigation"></a>階層式導覽

[`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) 類別提供的階層式導覽體驗讓使用者能夠視需要，向前及向後導覽頁面。 此類別會實作一堆後進先出 (LIFO) 的 [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) 物件導覽。

在階層式導覽中，[`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) 類別用來導覽一疊 [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) 物件。 若要將一頁移到另一頁，應用程式會將新的頁面推送到導覽堆疊上，該頁面就會變成使用中的頁面。 若要返回到上一頁，應用程式將會從導覽堆疊中快顯目前的頁面，新的最上層頁面就會變成使用中的頁面。

加入至導覽堆疊中的第一頁指的是應用程式的*根*頁面，下列程式碼範例會示範如何完成此作業：

```csharp
public App ()
{
    MainPage = new NavigationPage(new EmployeeListPage());
}
```

若要導覽至 `LoginPage`，必須在目前頁面的 [`Navigation`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) 屬性上叫用 [`PushAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PushAsync/p/Xamarin.Forms.Page/) 方法，如下列程式碼範例所示：

```csharp
await Navigation.PushAsync(new LoginPage());
```

這會使新的 `LoginPage` 物件推送至導覽堆疊上，而變成使用中的頁面。

無論是裝置上的實體按鈕還是螢幕上的按鈕，按下裝置上的 [上一頁] 按鈕都可以從導覽堆疊快顯使用中的頁面。 若要以程式設計的方式返回上一頁，`LoginPage` 執行個體必須叫用 [`PopAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PopAsync()/) 方法，如下列程式碼範例所示：

```csharp
await Navigation.PopAsync();
```

如需有關階層式導覽的詳細資訊，請參閱[階層式導覽](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)。

<a name="Modal_Navigation" />

### <a name="modal-navigation"></a>強制回應導覽

Xamarin.Forms 可支援強制回應頁面。 強制回應頁面鼓勵使用者完成各自獨立且無法離開的工作，直到完成或取消工作為止。

強制回應頁面可以是 Xamarin.Forms 所支援的任何一種 [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) 類型。 若要顯示強制回應頁面，應用程式會將其推送到導覽堆疊上，該頁面就會變成使用中的頁面。 若要返回上一頁，應用程式將會從導覽堆疊中快顯目前的頁面，新的最上層頁面就會變成使用中的頁面。

[`Navigation`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) 屬性會在任何 [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) 衍生類型上公開強制回應導覽方法。 [`Navigation`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) 屬性也會公開可以從中取得導覽堆疊中強制回應頁面的 [`ModalStack`](https://developer.xamarin.com/api/property/Xamarin.Forms.INavigation.ModalStack/) 屬性。 不過，沒有執行強制回應堆疊操作，或快顯至強制回應導覽中根目錄的概念。 這是因為基礎平台上普遍不支援這些作業。

> [!NOTE]
> 執行強制回應頁面瀏覽不需要 [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) 執行個體。

若要強制導覽至 `LoginPage`，必須在目前頁面的 [`Navigation`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) 屬性上叫用 [`PushModalAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page)/) 方法，如下列程式碼範例所示：

```csharp
await Navigation.PushModalAsync(new LoginPage());
```

這會使 `LoginPage` 執行個體推送至導覽堆疊上，而變成使用中的頁面。

無論是裝置上的實體按鈕還是螢幕上的按鈕，按下裝置上的 [上一頁] 按鈕都可以從導覽堆疊快顯使用中的頁面。 若要以程式設計的方式返回原始頁面，`LoginPage` 執行個體必須叫用 [`PopModalAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopModalAsync()/) 方法，如下列程式碼範例所示：

```csharp
await Navigation.PopModalAsync();
```

這會使 `LoginPage` 執行個體從導覽堆疊中移除，進而使新的最上層頁面變成使用中的頁面。

如需有關強制回應導覽的詳細資訊，請參閱[強制回應頁面](~/xamarin-forms/app-fundamentals/navigation/modal.md)。

<a name="Next_Steps" />

## <a name="next-steps"></a>後續步驟

本簡介文章應該可讓您開始撰寫 Xamarin.Forms 應用程式。 建議的後續步驟包括了解下列功能：

- 控制項範本可以在執行階段輕鬆地設定和重新設定應用程式頁面的佈景主題。 如需詳細資訊，請參閱[控制項範本](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md)。
- 資料範本可以針對支援的控制項，定義資料的呈現方式。 如需詳細資訊，請參閱[資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。
- 共用程式碼可以透過 [`DependencyService`](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/) 類別存取原生功能。 如需有關詳細資訊，請參閱[透過 DependencyService 存取原生功能](~/xamarin-forms/app-fundamentals/dependency-service/index.md)。
- Xamarin.Forms 包含簡單的傳訊服務，可傳送和接收訊息，因此減少類別間的結合程度。 如需詳細資訊，請參閱[使用 MessagingCenter 發行和訂閱](~/xamarin-forms/app-fundamentals/messaging-center.md)。
- 系統會在每個平台上使用 `Renderer` 類別，以不同的方式呈現每個頁面、版面配置和控制項，進而建立原生控制項、將其排列在畫面上，然後加入在共用程式碼中指定的行為。 開發人員可以實作自己的自訂 `Renderer` 類別，以自訂控制項的外觀及/或行為。 如需詳細資訊，請參閱[自訂呈現方式](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)。
- 效果也可以允許在每個平台上自訂原生控制項。 系統會在平台專屬的專案中，透過將 [`PlatformEffect`](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformEffect%3CTContainer,TControl%3E/) 控制項子類別化來建立效果，然後透過將其附加至適當的 Xamarin.Forms 控制項來取用。 如需詳細資訊，請參閱[效果](~/xamarin-forms/app-fundamentals/effects/index.md)。

或者，Charles Petzold 的書籍《使用 Xamarin.Forms 建立行動應用程式》是深入了解 Xamarin.Forms 的絕佳選擇。 如需詳細資訊，請參閱[使用 Xamarin.Forms 建立行動應用程式](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)。

## <a name="summary"></a>總結

本文提供 Xamarin.Forms 的簡介，以及如何使用它開始撰寫應用程式。 Xamarin.Forms 是一個以原生方式跨平台支援的 UI 工具組抽象概念，可讓開發人員輕鬆地建立可以跨 Android、iOS 和通用 Windows 平台共用的使用者介面。 系統會使用目標平台的原生控制項呈現使用者介面，讓 Xamarin.Forms 應用程式保留每個平台適當的外觀及操作。


## <a name="related-links"></a>相關連結

- [XAML 基本知識](~/xamarin-forms/xaml/xaml-basics/index.md)
- [控制項參考](~/xamarin-forms/user-interface/controls/index.md)
- [使用者介面](~/xamarin-forms/user-interface/index.md)
- [Xamarin.Forms 範例](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [使用者入門範例](https://developer.xamarin.com/samples/xamarin-forms/GettingStarted/)
- [Xamarin.Forms](https://developer.xamarin.com/api/namespace/Xamarin.Forms/)
- [免費的自我引導式學習 (影片)](https://university.xamarin.com/self-guided) \(英文\)
- [Hello, Xamarin.Forms iOS 活頁簿](https://developer.xamarin.com/workbooks/xamarin-forms/getting-started/GettingStartedWithXamarinForms-ios.workbook)
