---
title: Xamarin.Forms Master-Detail Page
description: Xamarin.Forms MasterDetailPage 是管理兩個相關資訊頁面的頁面 – 顯示項目的主版頁面，和顯示主版頁面上項目相關詳細資料的詳細資料頁面。 本文說明如何使用 MasterDetailPage 及在其資訊頁面之間巡覽。
ms.prod: xamarin
ms.assetid: 119945E3-58B8-4630-A3D2-8B561529D53B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: c60eecd5ebd0f518514cd77d8dd963568d1a1d43
ms.sourcegitcommit: 482aef652bdaa440561252b6a1a1c0a40583cd32
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2019
ms.locfileid: "65970921"
---
# <a name="xamarinforms-master-detail-page"></a>Xamarin.Forms Master-Detail Page

[![下載範例](~/media/shared/download.png) 下載範例](https://developer.xamarin.com/samples/xamarin-forms/Navigation/MasterDetailPage/)

_Xamarin.Forms MasterDetailPage 是管理兩個相關資訊頁面的頁面 – 顯示項目的主版頁面，和顯示主版頁面上項目相關詳細資料的詳細資料頁面。本文說明如何使用 MasterDetailPage 及在其資訊頁面之間巡覽。_

## <a name="overview"></a>總覽

主版頁面通常會顯示一份項目清單，如下列螢幕擷取畫面所示：

[![](master-detail-page-images/masterpage-components.png "主版頁面元件")](master-detail-page-images/masterpage-components-large.png#lightbox "主版頁面元件")

項目清單的位置在每個平台上都相同，且選取其中一個項目會巡覽至對應的詳細資料頁面。 此外，主版頁面也會提供包含一個按鈕的導覽列，該按鈕可用來巡覽至作用中的詳細資料頁面：

- 在 iOS 上，導覽列會出現在頁面頂端，並有一個按鈕，可巡覽至詳細資料頁面。 此外，可以藉由將主版頁面撥動至左側，巡覽至作用中的詳細資料頁面。
- 在 Android 上，導覽列會出現在頁面頂端，並顯示一個標題、圖示，以及可巡覽至詳細資料頁面的按鈕。 該圖示是在 `[Activity]` 屬性中定義，以裝飾 Android 平台特定專案中的 `MainActivity` 類別。 此外，可以藉由將主版頁面撥動至左側、點選畫面最右邊的詳細資料頁面，和點選畫面底部的 [上一步] 按鈕，巡覽至作用中的詳細資料頁面。
- 在通用 Windows 平台 (UWP) 上，導覽列出現在頁面頂端，並具有可巡覽至詳細資料頁面的按鈕。

詳細資料頁面會顯示對應至主版頁面上所選取項目的資料，且會顯示詳細資料頁面的主要元件，如下列螢幕擷取畫面所示：

![](master-detail-page-images/detailpage-components.png "詳細資料頁面元件")

詳細資料頁面包含導覽列，其內容會因平台而不同：

- 在 iOS 上，導覽列出現在頁面頂端並顯示標題，且具有返回主版頁面的按鈕，前提是詳細資料頁面執行個體包裝在 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 執行個體中。 此外，可以藉由將詳細資料頁面撥動至右側，回到主版頁面。
- 在 Android 上，導覽列出現在頁面頂端並顯示標題、圖示，以及可返回主版頁面的按鈕。 該圖示是在 `[Activity]` 屬性中定義，以裝飾 Android 平台特定專案中的 `MainActivity` 類別。
- 在 UWP 上，導覽列出現在頁面頂端並顯示標題，並具有可返回主版頁面的按鈕。

### <a name="navigation-behavior"></a>巡覽行為

主版頁面和詳細資料頁面之間的巡覽體驗行為，取決於平台：

- 在 iOS 上，當主版頁面從左邊滑動時，詳細資料頁面會「滑」向右邊，詳細資料頁面的左邊部分仍然可見。
- 在 Android 上，詳細資料頁面和主版頁面會彼此「重疊」。
- 在 UWP 上，主版頁面投影片會從詳細資料頁面左上方華過，惟 [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) 屬性必須設定為 `Popover`。 如需詳細資訊，請參閱[控制詳細資料頁面顯示行為](#Controlling_the_Detail_Page_Display_Behavior)。

在橫向模式中會觀察到類似的行為，只除了 iOS 和 Android 上的主版頁面，會具有與直向模式主版頁面類似的寬度，因此會顯示更多詳細資料頁面。

如需控制巡覽行為的資訊，請參閱[控制詳細資料頁面的顯示行為](#Controlling_the_Detail_Page_Display_Behavior)。

## <a name="creating-a-masterdetailpage"></a>建立 MasterDetailPage

[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 包含 [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) 和 [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) 屬性，它們都是 [`Page`](xref:Xamarin.Forms.Page) 類型，分別用來取得和設定主版頁面和詳細資料頁面。

> [!IMPORTANT]
> [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 設計為根頁面，使用它作為其他頁面類型的子分頁，可能會導致非預期且不一致的行為。 此外，建議 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 的主版頁面應該一律為 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 執行個體，且詳細資料頁面應該只填入 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)、[`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 和 `ContentPage` 執行個體。 這有助於跨所有平台確保一致的使用者體驗。

下列 XAML 程式碼範例顯示 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)，它會設定 [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) 和 [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) 屬性：

```xaml
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
                  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                  xmlns:local="clr-namespace:MasterDetailPageNavigation;assembly=MasterDetailPageNavigation"
                  x:Class="MasterDetailPageNavigation.MainPage">
    <MasterDetailPage.Master>
        <local:MasterPage x:Name="masterPage" />
    </MasterDetailPage.Master>
    <MasterDetailPage.Detail>
        <NavigationPage>
            <x:Arguments>
                <local:ContactsPage />
            </x:Arguments>
        </NavigationPage>
    </MasterDetailPage.Detail>
</MasterDetailPage>
```

下列程式碼範例示範以 C# 建立的相等 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)：

```csharp
public class MainPageCS : MasterDetailPage
{
    MasterPageCS masterPage;

    public MainPageCS ()
    {
        masterPage = new MasterPageCS ();
        Master = masterPage;
        Detail = new NavigationPage (new ContactsPageCS ());
        ...
    }
    ...
}
```

[`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master) 屬性設定為 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 執行個體。 [`MasterDetailPage.Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) 屬性設定為包含 `ContentPage` 的 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 執行個體。

### <a name="creating-the-master-page"></a>建立主版頁面

下列 XAML 程式碼範例顯示 `MasterPage` 物件的宣告，它是透過 [`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master) 屬性參考：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="using:MasterDetailPageNavigation"
             x:Class="MasterDetailPageNavigation.MasterPage"
             Padding="0,40,0,0"
             IconImageSource="hamburger.png"
             Title="Personal Organiser">
    <StackLayout>
        <ListView x:Name="listView" x:FieldModifier="public">
           <ListView.ItemsSource>
                <x:Array Type="{x:Type local:MasterPageItem}">
                    <local:MasterPageItem Title="Contacts" IconSource="contacts.png" TargetType="{x:Type local:ContactsPage}" />
                    <local:MasterPageItem Title="TodoList" IconSource="todo.png" TargetType="{x:Type local:TodoListPage}" />
                    <local:MasterPageItem Title="Reminders" IconSource="reminders.png" TargetType="{x:Type local:ReminderPage}" />
                </x:Array>
            </ListView.ItemsSource>
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <Grid Padding="5,10">
                            <Grid.ColumnDefinitions>
                                <ColumnDefinition Width="30"/>
                                <ColumnDefinition Width="*" />
                            </Grid.ColumnDefinitions>
                            <Image Source="{Binding IconSource}" />
                            <Label Grid.Column="1" Text="{Binding Title}" />
                        </Grid>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

頁面包含 [`ListView`](xref:Xamarin.Forms.ListView)，其中藉由將其 [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) 屬性設定為 `MasterPageItem` 執行個體的陣列，來填入 XAML 資料。 每個 `MasterPageItem` 會定義 `Title`、`IconSource` 和 `TargetType` 屬性。

[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 會指派給 [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) 屬性，以顯示每個 `MasterPageItem`。 `DataTemplate` 包含 [`ViewCell`](xref:Xamarin.Forms.ViewCell)，其包含 [`Image`](xref:Xamarin.Forms.Image) 和 [`Label`](xref:Xamarin.Forms.Label)。 針對每個 `MasterPageItem`，[`Image`](xref:Xamarin.Forms.Image) 會顯示 `IconSource` 屬性值，而 [`Label`](xref:Xamarin.Forms.Label) 顯示 `Title` 屬性值。

頁面設定了 [`Title`](xref:Xamarin.Forms.Page.Title) 和 [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) 屬性。 圖示會出現在詳細資料頁面上，前提是詳細資料頁面有標題列。 這必須藉由將詳細資料頁面執行個體包裝在 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 執行個體中來於 iOS 上啟用。

> [!NOTE]
> [`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master) 頁面必須設定其 [`Title`](xref:Xamarin.Forms.Page.Title) 屬性，否則會發生例外狀況。

下列程式碼範例示範以 C# 建立的相等頁面：

```csharp
public class MasterPageCS : ContentPage
{
  public ListView ListView { get { return listView; } }

  ListView listView;

  public MasterPageCS ()
  {
    var masterPageItems = new List<MasterPageItem> ();
    masterPageItems.Add (new MasterPageItem {
      Title = "Contacts",
      IconSource = "contacts.png",
      TargetType = typeof(ContactsPageCS)
    });
    masterPageItems.Add (new MasterPageItem {
      Title = "TodoList",
      IconSource = "todo.png",
      TargetType = typeof(TodoListPageCS)
    });
    masterPageItems.Add (new MasterPageItem {
      Title = "Reminders",
      IconSource = "reminders.png",
      TargetType = typeof(ReminderPageCS)
    });

    listView = new ListView {
      ItemsSource = masterPageItems,
      ItemTemplate = new DataTemplate (() => {
        var grid = new Grid { Padding = new Thickness(5, 10) };
        grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(30) });
        grid.ColumnDefinitions.Add(new ColumnDefinition { Width = GridLength.Star });

        var image = new Image();
        image.SetBinding(Image.SourceProperty, "IconSource");
        var label = new Label { VerticalOptions = LayoutOptions.FillAndExpand };
        label.SetBinding(Label.TextProperty, "Title");

        grid.Children.Add(image);
        grid.Children.Add(label, 1, 0);

        return new ViewCell { View = grid };
      }),
      SeparatorVisibility = SeparatorVisibility.None
    };

    IconImageSource = "hamburger.png";
    Title = "Personal Organiser";
    Content = new StackLayout
    {
      Children = { listView }
    };
  }
}
```

下列螢幕擷取畫面顯示每個平台上的主版頁面：

![](master-detail-page-images/masterpage.png "主版頁面範例")

### <a name="creating-and-displaying-the-detail-page"></a>建立和顯示詳細資料頁面

`MasterPage` 執行個體包含一個 `ListView` 屬性，它會公開其 [`ListView`](xref:Xamarin.Forms.ListView) 執行個體，以便 `MainPage`[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 執行個體可以註冊事件處理常式，來處理 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 事件。 這讓 `MainPage` 執行個體能將 [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) 屬性設定為代表所選 `ListView` 項目的頁面。 下列程式碼範例示範事件處理常式：

```csharp
public partial class MainPage : MasterDetailPage
{
    public MainPage ()
    {
        ...
        masterPage.listView.ItemSelected += OnItemSelected;
    }

    void OnItemSelected (object sender, SelectedItemChangedEventArgs e)
    {
        var item = e.SelectedItem as MasterPageItem;
        if (item != null) {
            Detail = new NavigationPage ((Page)Activator.CreateInstance (item.TargetType));
            masterPage.listView.SelectedItem = null;
            IsPresented = false;
        }
    }
}
```

`OnItemSelected` 方法會執行下列動作：

- 它會從 [`ListView`](xref:Xamarin.Forms.ListView) 執行個體擷取 [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem)，且在它不是 `null` 的前提下，將詳細資料頁面設定為 `MasterPageItem` 的 `TargetType` 屬性中，所儲存頁面類型的新執行個體。 頁面類型包裝在 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 執行個體中，以確保透過 `MasterPage` 上 [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) 屬性參考的圖示，會顯示在 iOS 中的詳細資料頁面上。
- [`ListView`](xref:Xamarin.Forms.ListView) 中的選取項目，設定為 `null` 以確保下次呈現 `MasterPage` 時不會選取任何 `ListView` 項目。
- 詳細資料頁面會呈現給使用者，方法是將 [`MasterDetailPage.IsPresented`](xref:Xamarin.Forms.MasterDetailPage.IsPresented) 屬性設定為 `false`。 此屬性控制要顯示主版頁面還是詳細資料頁面。 如果要顯示主版頁面，它應該設定為 `true`，若要顯示詳細資料頁面，則設定為 `false`。

下列螢幕擷取畫面顯示 `ContactPage` 詳細資料頁面，在主版頁面上選取它之後便會加以顯示：

![](master-detail-page-images/detailpage.png "詳細資料頁面範例")

<a name="Controlling_the_Detail_Page_Display_Behavior" />

### <a name="controlling-the-detail-page-display-behavior"></a>控制詳細資料頁面顯示行為

[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 如何管理主版頁面和詳細資料頁面，取決於應用程式是在手機還是平板電腦上執行、裝置的方向，以及 [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) 屬性的值。 此屬性會決定詳細資料頁面的顯示方式。 可能值如下：

- **預設** – 會使用平台預設來顯示頁面。
- **Popover** – 詳細資料頁面會蓋住或部分蓋住主版頁面。
- **分割** – 主版頁面顯示在左邊，詳細資料頁面在右邊。
- **SplitOnLandscape** – 裝置處於橫向時，會使用分割畫面。
- **SplitOnPortrait** – 裝置處於直向時，會使用分割畫面。

下列 XAML 程式碼範例示範如何設定 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 的 [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) 屬性：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
                  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                  x:Class="MasterDetailPageNavigation.MainPage"
                  MasterBehavior="Popover">
  ...
</MasterDetailPage>
```

下列程式碼範例示範以 C# 建立的相等 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)：

```csharp
public class MainPageCS : MasterDetailPage
{
    MasterPageCS masterPage;

    public MainPageCS ()
    {
        MasterBehavior = MasterBehavior.Popover;
        ...
    }
}
```

不過，[`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) 屬性的值只會影響在平板電腦或桌上型電腦上執行的應用程式。 在手機上執行的應用程式一律為 *Popover* 行為。

## <a name="summary"></a>總結

本文示範如何使用 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 及在其資訊頁面之間巡覽。 Xamarin.Forms `MasterDetailPage` 是管理兩個相關資訊頁面的頁面 – 顯示項目的主版頁面，和顯示主版頁面上項目相關詳細資料的詳細資料頁面。

## <a name="related-links"></a>相關連結

- [頁面變化](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [MasterDetailPage (Samples)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/MasterDetailPage/)
- [MasterDetailPage](xref:Xamarin.Forms.MasterDetailPage)
