---
title: Xamarin.Forms 主版詳細資料頁面
description: Xamarin.Forms MasterDetailPage 是資訊的管理兩個相關的網頁-主版頁面所呈現項目和主版頁面顯示項目的相關詳細資料的詳細資料頁面的頁面。 本文說明如何使用 MasterDetailPage 和其資訊的頁面之間巡覽。
ms.prod: xamarin
ms.assetid: 119945E3-58B8-4630-A3D2-8B561529D53B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 46fa32fc8203b32378f4a4fbe07cb8c9f8dbb854
ms.sourcegitcommit: 7a89735aed9ddf89c855fd33928915d72da40c2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2018
ms.locfileid: "36209202"
---
# <a name="xamarinforms-master-detail-page"></a>Xamarin.Forms 主版詳細資料頁面

_Xamarin.Forms MasterDetailPage 是資訊的管理兩個相關的網頁-主版頁面所呈現項目和主版頁面顯示項目的相關詳細資料的詳細資料頁面的頁面。本文說明如何使用 MasterDetailPage 和其資訊的頁面之間巡覽。_

## <a name="overview"></a>總覽

主版頁面通常會顯示一份項目，如下列螢幕擷取畫面所示：

[![](master-detail-page-images/masterpage-components.png "主版頁面元件")](master-detail-page-images/masterpage-components-large.png#lightbox "主版頁面的元件")

項目清單的位置是每個平台上，完全相同，並選取其中一個項目會巡覽至對應的詳細資料頁面。 此外，主版頁面也提供功能包含一個按鈕，可用來瀏覽至作用中的詳細資料頁面巡覽列：

- 在 iOS 上，瀏覽列出現在頁面頂端，並有一個按鈕，巡覽至詳細資料頁面。 此外，使用中的詳細資料頁面可以瀏覽至由撥動至左側的主版頁面。
- 在 Android 上，導覽列會出現在頁面頂端，並顯示詳細資料頁面的標題、 圖示和瀏覽按鈕。 中所定義的圖示`[Activity]`裝飾的屬性`MainActivity`Android 平台專屬專案中的類別。 此外，使用中的詳細資料頁面可以瀏覽至撥動至左側的主版頁面、 點選 [詳細資料] 頁面最右邊的畫面上，以及依序點選*回*螢幕底部的按鈕。
- 在通用 Windows 平台 (UWP)，瀏覽列是出現在頁面頂端，而且按鈕瀏覽至詳細資料頁面。

對應至項目詳細資料頁面上顯示資料頁面上，選取在主機上，下列螢幕擷取畫面中顯示詳細資料頁面的主要元件：

![](master-detail-page-images/detailpage-components.png "詳細資料頁面的元件")

詳細資料頁面包含導覽列中，內容是平台而定：

- 在 iOS 上，瀏覽列出現在頁面頂端顯示標題、 和前提是包裝在詳細資料頁面執行個體有一個按鈕，返回主版頁面中， [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)執行個體。 此外，若要可以傳回主版頁面撥動右邊的 [詳細資料] 頁面。
- 在 Android 上，導覽列會出現在頁面頂端，並顯示標題、 圖示，並傳回主版頁面的按鈕。 中所定義的圖示`[Activity]`裝飾的屬性`MainActivity`Android 平台專屬專案中的類別。
- UWP、 導覽列會出現在頁面頂端顯示標題、，以及上有一個按鈕，返回主版頁面。

### <a name="navigation-behavior"></a>導覽行為

Master 和詳細資料頁面之間瀏覽體驗的行為取決於平台：

- 在 iOS 上，詳細資料頁面*投影片*為主版頁面投影片右邊，從左側和左側的組件的詳細資料頁面是仍然可見。
- 在 Android 上，詳細資料] 和 [主版頁面是*重疊*另一方。
- 在 UWP，詳細資料] 和 [主版頁面是*交換*。

類似的行為將會觀察到以橫向模式，不同之處在於 iOS 和 Android 上的主版頁面都有為直向模式中的主版頁面類似寬度讓多個詳細資料頁面會顯示。

控制巡覽行為的相關資訊，請參閱[控制的詳細資料頁面顯示行為](#Controlling_the_Detail_Page_Display_Behavior)。

## <a name="creating-a-masterdetailpage"></a>建立 MasterDetailPage

A [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)包含[ `Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/)和[ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/)這兩個類型的屬性[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)，用來取得並分別設定 master] 和 [詳細資料頁面。

> [!IMPORTANT]
> A [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)的設計是 [根] 頁面上，並使用它，因為在其他的頁面類型的子分頁可能會導致非預期且不一致的行為。 此外，建議的主版頁面[ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)應一律[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)執行個體和詳細資料頁面只會填入與[ `TabbedPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)， [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)，和`ContentPage`執行個體。 這有助於確保所有平台的一致的使用者經驗。

下列 XAML 程式碼範例所示[ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)設定[ `Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/)和[ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/)屬性：

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

下列程式碼範例顯示的同等[ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)以 C# 建立：

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

[ `MasterDetailPage.Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/)屬性設定為[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)執行個體。 [ `MasterDetailPage.Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/)屬性設定為[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)包含`ContentPage`執行個體。

### <a name="creating-the-master-page"></a>建立主版頁面

下列 XAML 程式碼範例顯示的宣告`MasterPage`物件，參考透過[ `MasterDetailPage.Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/)屬性：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="using:MasterDetailPageNavigation"
             x:Class="MasterDetailPageNavigation.MasterPage"
             Padding="0,40,0,0"
             Icon="hamburger.png"
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

頁面包含[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) ，會在 XAML 中的資料填入藉由設定其[ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemsSource/)屬性的陣列`MasterPageItem`執行個體。 每個`MasterPageItem`定義`Title`， `IconSource`，和`TargetType`屬性。

A [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)指派給[ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemTemplate/)屬性，顯示每個`MasterPageItem`。 `DataTemplate`包含[ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) ，組成[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)和[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)。 [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)顯示`IconSource`屬性值和[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)顯示`Title`屬性值，每個`MasterPageItem`。

頁面具有其[ `Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Title/)和[ `Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Icon/)屬性集。 圖示會出現在 [詳細資料] 頁面中，前提是詳細資料頁面的標題列。 這必須由包裝的詳細資料頁面的執行個體中啟用 iOS 上[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)執行個體。

> [!NOTE]
> [ `MasterDetailPage.Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/)頁面必須有其[ `Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Title/)設定屬性，或將會發生例外狀況。

下列程式碼範例顯示 C# 中建立的對等頁面：

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

    Icon = "hamburger.png";
    Title = "Personal Organiser";
    Content = new StackLayout
    {
      Children = { listView }
    };
  }
}
```

下列螢幕擷取畫面會顯示每個平台上的主版頁面：

![](master-detail-page-images/masterpage.png "主版頁面範例")

### <a name="creating-and-displaying-the-detail-page"></a>建立和顯示詳細資料頁面

`MasterPage`執行個體會包含`ListView`屬性會公開其[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)執行個體，讓`MainPage` [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)可以註冊執行個體事件處理常式來處理[ `ItemSelected` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemSelected/)事件。 這可讓`MainPage`設定的執行個體[ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/)屬性來表示所選頁面`ListView`項目。 下列程式碼範例示範事件處理常式：

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

`OnItemSelected`方法會執行下列動作：

- 它會擷取[ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.SelectedItem/)從[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)執行個體，並提供不是`null`，中儲存的頁面類型的新執行個體設定詳細資料頁面`TargetType`屬性`MasterPageItem`。 頁面類型包裝在[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)執行個體，以確保透過參考圖示[ `Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Icon/)屬性`MasterPage`會顯示在詳細資料頁面上，在 iOS 中。
- 在選取的項目[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)設`null`以確保沒有任何`ListView`下一次將選取項目`MasterPage`呈現。
- 詳細資料頁面，會藉由設定呈現給使用者[ `MasterDetailPage.IsPresented` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.IsPresented/)屬性`false`。 這個屬性控制是否顯示 master] 或 [詳細資料頁面。 應該設定為`true`顯示主版頁面和`false`以顯示詳細資料頁面。

下列螢幕擷取畫面顯示`ContactPage`詳細資料 頁面上之後已被選取的主版頁面上, 顯示為：

![](master-detail-page-images/detailpage.png "詳細資料頁面範例")

<a name="Controlling_the_Detail_Page_Display_Behavior" />

### <a name="controlling-the-detail-page-display-behavior"></a>控制的詳細資料頁面顯示行為

如何[ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)管理 master] 和 [詳細資料頁面取決於應用程式上的電話或平板電腦、 裝置的方向和值的執行是否[ `MasterBehavior` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.MasterBehavior/)屬性。 此屬性會決定如何顯示詳細資料頁面。 它的可能值為：

- **預設**– 頁會使用平台預設值。
- **Popover** -詳細資料頁面涵蓋，或部分涵蓋的主版頁面。
- **分割**– 主版頁面會顯示在左邊和右邊是詳細資料頁面。
- **SplitOnLandscape** – 當設備處於橫向方向時使用分屏。
- **SplitOnPortrait** – 分割畫面可在裝置處於直式方向。

下列 XAML 程式碼範例示範如何設定[ `MasterBehavior` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.MasterBehavior/)屬性[ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/):

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
                  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                  x:Class="MasterDetailPageNavigation.MainPage"
                  MasterBehavior="Popover">
  ...
</MasterDetailPage>
```

下列程式碼範例顯示的同等[ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)以 C# 建立：

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

不過，值[ `MasterBehavior` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.MasterBehavior/)屬性只會影響在平板電腦或桌上型電腦上執行的應用程式。 一律在手機上執行的應用程式有*Popover*行為。

## <a name="summary"></a>總結

本文示範如何使用[ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)和其資訊的頁面之間巡覽。 Xamarin.Forms`MasterDetailPage`是管理兩個頁面的相關資訊 – 主版頁面所呈現項目和主版頁面顯示項目的相關詳細資料的詳細資料頁面的頁面。


## <a name="related-links"></a>相關連結

- [頁面須](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [MasterDetailPage （範例）](https://developer.xamarin.com/samples/xamarin-forms/Navigation/MasterDetailPage/)
- [MasterDetailPage](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)
