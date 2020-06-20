---
title: Xamarin.Forms主版-詳細資料頁面
description: Xamarin.FormsMasterDetailPage 是管理兩個相關資訊頁面的頁面–顯示專案的主版頁面，以及顯示主版頁面上專案相關詳細資料的詳細資料頁面。 本文說明如何使用 MasterDetailPage 及在其資訊頁面之間巡覽。
ms.prod: xamarin
ms.assetid: 119945E3-58B8-4630-A3D2-8B561529D53B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c751a1843479f1e98739964631999dfdb0e3b634
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84569630"
---
# <a name="xamarinforms-master-detail-page"></a>Xamarin.Forms主版-詳細資料頁面

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage)

_Xamarin.FormsMasterDetailPage 是管理兩個相關資訊頁面的頁面–顯示專案的主版頁面，以及顯示主版頁面上專案相關詳細資料的詳細資料頁面。本文說明如何使用 MasterDetailPage，並在其資訊頁面之間流覽。_

## <a name="overview"></a>概觀

主版頁面通常會顯示一份項目清單，如下列螢幕擷取畫面所示：

[![](master-detail-page-images/masterpage-components.png "Master Page Components")](master-detail-page-images/masterpage-components-large.png#lightbox "Master Page Components")

項目清單的位置在每個平台上都相同，且選取其中一個項目會巡覽至對應的詳細資料頁面。 此外，主版頁面也會提供包含一個按鈕的導覽列，該按鈕可用來巡覽至作用中的詳細資料頁面：

- 在 iOS 上，導覽列會出現在頁面頂端，並有一個按鈕，可巡覽至詳細資料頁面。 此外，可以藉由將主版頁面撥動至左側，巡覽至作用中的詳細資料頁面。
- 在 Android 上，導覽列會出現在頁面頂端，並顯示一個標題、圖示，以及可巡覽至詳細資料頁面的按鈕。 該圖示是在 `[Activity]` 屬性中定義，以裝飾 Android 平台特定專案中的 `MainActivity` 類別。 此外，可以藉由將主版頁面撥動至左側、點選畫面最右邊的詳細資料頁面，和點選畫面底部的 [上一步]** 按鈕，巡覽至作用中的詳細資料頁面。
- 在通用 Windows 平台 (UWP) 上，導覽列出現在頁面頂端，並具有可巡覽至詳細資料頁面的按鈕。

詳細資料頁面會顯示對應至主版頁面上所選取項目的資料，且會顯示詳細資料頁面的主要元件，如下列螢幕擷取畫面所示：

![](master-detail-page-images/detailpage-components.png "Detail Page Components")

詳細資料頁面包含導覽列，其內容會因平台而不同：

- 在 iOS 上，導覽列會出現在頁面頂端並顯示標題，而且有一個按鈕會回到主版頁面，前提是詳細資料頁面實例包裝在 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 實例中。 此外，可以藉由將詳細資料頁面撥動至右側，回到主版頁面。
- 在 Android 上，導覽列出現在頁面頂端並顯示標題、圖示，以及可返回主版頁面的按鈕。 該圖示是在 `[Activity]` 屬性中定義，以裝飾 Android 平台特定專案中的 `MainActivity` 類別。
- 在 UWP 上，導覽列出現在頁面頂端並顯示標題，並具有可返回主版頁面的按鈕。

### <a name="navigation-behavior"></a>巡覽行為

主版頁面和詳細資料頁面之間的巡覽體驗行為，取決於平台：

- 在 iOS 上，當主版頁面從左邊滑動時，詳細資料頁面會「滑」** 向右邊，詳細資料頁面的左邊部分仍然可見。
- 在 Android 上，詳細資料頁面和主版頁面會彼此「重疊」**。
- 在 UWP 上，如果屬性設定為，則主版頁面會從詳細資料頁面的左邊滑出 [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) `Popover` 。 如需詳細資訊，請參閱[控制詳細資料頁面顯示行為](#controlling-the-detail-page-display-behavior)。

在橫向模式中會觀察到類似的行為，只除了 iOS 和 Android 上的主版頁面，會具有與直向模式主版頁面類似的寬度，因此會顯示更多詳細資料頁面。

如需控制巡覽行為的資訊，請參閱[控制詳細資料頁面的顯示行為](#controlling-the-detail-page-display-behavior)。

## <a name="creating-a-masterdetailpage"></a>建立 MasterDetailPage

[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)包含 [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) 和屬性，兩者都 [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) 是型別 [`Page`](xref:Xamarin.Forms.Page) ，分別用來取得和設定主版和詳細資料頁面。

> [!IMPORTANT]
> [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)是設計成根頁面，而使用它做為其他頁面類型的子頁面，可能會導致非預期且不一致的行為。 此外，建議的主版頁面 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 應一律為 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 實例，而且詳細資料頁面只能填入 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 、 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 和 `ContentPage` 實例。 這有助於跨所有平台確保一致的使用者體驗。

下列 XAML 程式碼範例顯示 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 設定 [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) 和 [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) 屬性的：

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

下列程式碼範例顯示 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 以 c # 建立的對等：

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

[`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master)屬性會設定為 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 實例。 [`MasterDetailPage.Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail)屬性會設定為 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 包含 `ContentPage` 實例的。

### <a name="creating-the-master-page"></a>建立主版頁面

下列 XAML 程式碼範例會顯示物件的宣告 `MasterPage` ，這個宣告會透過屬性來參考 [`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master) ：

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

此頁面包含 [`ListView`](xref:Xamarin.Forms.ListView) ，其會將其 [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) 屬性設定為實例陣列，以在 XAML 中填入資料 `MasterPageItem` 。 每個 `MasterPageItem` 會定義 `Title`、`IconSource` 和 `TargetType` 屬性。

[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)會指派給 [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) 屬性，以顯示每個 `MasterPageItem` 。 `DataTemplate`包含 [`ViewCell`](xref:Xamarin.Forms.ViewCell) 由和組成的 [`Image`](xref:Xamarin.Forms.Image) [`Label`](xref:Xamarin.Forms.Label) 。 會 [`Image`](xref:Xamarin.Forms.Image) 顯示 `IconSource` 屬性值，而會 [`Label`](xref:Xamarin.Forms.Label) 顯示 `Title` 每個的屬性值 `MasterPageItem` 。

頁面已設定其 [`Title`](xref:Xamarin.Forms.Page.Title) 和 [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) 屬性。 圖示會出現在詳細資料頁面上，前提是詳細資料頁面有標題列。 您必須將詳細資料頁面實例包裝在實例中，才能在 iOS 上啟用此功能 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 。

> [!NOTE]
> [`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master)頁面必須 [`Title`](xref:Xamarin.Forms.Page.Title) 設定其屬性，否則會發生例外狀況。

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

![](master-detail-page-images/masterpage.png "Master Page Example")

### <a name="creating-and-displaying-the-detail-page"></a>建立和顯示詳細資料頁面

`MasterPage`實例包含 `ListView` 會公開其實例的屬性， [`ListView`](xref:Xamarin.Forms.ListView) 讓 `MainPage` [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 實例可以註冊事件處理常式來處理 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 事件。 這可讓 `MainPage` 實例將屬性設定 [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) 為代表所選取專案的頁面 `ListView` 。 下列程式碼範例示範事件處理常式：

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

- 它會 [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) 從實例中抓取， [`ListView`](xref:Xamarin.Forms.ListView) 並假設它不是，會 `null` 將詳細資料頁面設定為儲存在之屬性中的頁面類型的新實例 `TargetType` `MasterPageItem` 。 頁面類型會包裝在實例中 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) ，以確保透過中的屬性所參考的圖示 [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) `MasterPage` 會顯示在 iOS 的詳細資料頁面上。
- 中選取的專案 [`ListView`](xref:Xamarin.Forms.ListView) 會設為， `null` 以確保 `ListView` 下一次出現時，不會選取任何專案 `MasterPage` 。
- 詳細資料頁面會藉由將屬性設定為來呈現給使用者 [`MasterDetailPage.IsPresented`](xref:Xamarin.Forms.MasterDetailPage.IsPresented) `false` 。 此屬性控制要顯示主版頁面還是詳細資料頁面。 如果要顯示主版頁面，它應該設定為 `true`，若要顯示詳細資料頁面，則設定為 `false`。

下列螢幕擷取畫面顯示 `ContactPage` 詳細資料頁面，在主版頁面上選取它之後便會加以顯示：

![](master-detail-page-images/detailpage.png "Detail Page Example")

### <a name="controlling-the-detail-page-display-behavior"></a>控制詳細資料頁面顯示行為

如何 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 管理主版和詳細資料頁面，取決於應用程式是在手機或平板電腦上執行、裝置的方向，以及屬性的值 [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) 。 此屬性會決定詳細資料頁面的顯示方式。 可能值如下：

- **預設** – 會使用平台預設來顯示頁面。
- **Popover** – 詳細資料頁面會蓋住或部分蓋住主版頁面。
- **分割** – 主版頁面顯示在左邊，詳細資料頁面在右邊。
- **SplitOnLandscape** – 裝置處於橫向時，會使用分割畫面。
- **SplitOnPortrait** – 裝置處於直向時，會使用分割畫面。

下列 XAML 程式碼範例示範如何 [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) 在上設定屬性 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) ：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
                  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                  x:Class="MasterDetailPageNavigation.MainPage"
                  MasterBehavior="Popover">
  ...
</MasterDetailPage>
```

下列程式碼範例顯示 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 以 c # 建立的對等：

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

不過，屬性的值 [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) 只會影響在平板電腦或桌面上執行的應用程式。 在手機上執行的應用程式一律為 *Popover* 行為。

## <a name="summary"></a>摘要

本文示範如何使用 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) ，並在其資訊頁面之間流覽。 Xamarin.Forms `MasterDetailPage` 是管理兩個相關資訊頁面的頁面–顯示專案的主版頁面，以及顯示主版頁面上專案相關詳細資料的詳細資料頁面。

## <a name="related-links"></a>相關連結

- [頁面種類](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [MasterDetailPage (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage)
- [MasterDetailPage](xref:Xamarin.Forms.MasterDetailPage)
