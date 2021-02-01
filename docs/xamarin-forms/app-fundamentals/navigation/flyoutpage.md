---
title: Xamarin.Forms FlyoutPage
description: Xamarin.FormsFlyoutPage 是管理兩個相關資訊頁面的頁面–顯示專案的飛出視窗頁面，以及顯示飛出視窗頁面上專案相關詳細資料的詳細資料頁面。 本文說明如何使用 FlyoutPage，並在其資訊頁面之間流覽。
ms.prod: xamarin
ms.assetid: 119945E3-58B8-4630-A3D2-8B561529D53B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8a2947ef2c326c57bc646cd980109ed9a1b97347
ms.sourcegitcommit: 9ab5a1e346e20f54e8b7aa655fd3d117b43978cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2021
ms.locfileid: "99223707"
---
# <a name="no-locxamarinforms-flyoutpage"></a>Xamarin.Forms FlyoutPage

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/navigation-flyoutpage)

飛出視窗頁面通常會顯示專案清單，如下列螢幕擷取畫面所示：

[![飛出視窗頁面元件](flyoutpage-images/flyoutpage-components.png)](flyoutpage-images/flyoutpage-components-large.png#lightbox "飛出視窗頁面元件")

項目清單的位置在每個平台上都相同，且選取其中一個項目會巡覽至對應的詳細資料頁面。 此外，[飛出視窗] 頁面也有一個導覽列，其中包含可用來流覽至 [作用中詳細資料] 頁面的按鈕：

- 在 iOS 上，導覽列會出現在頁面頂端，並有一個按鈕，可巡覽至詳細資料頁面。 此外，您可以透過將飛出視窗向左輕刷，來流覽至 [作用中詳細資料] 頁面。
- 在 Android 上，導覽列會出現在頁面頂端，並顯示一個標題、圖示，以及可巡覽至詳細資料頁面的按鈕。 該圖示是在 `[Activity]` 屬性中定義，以裝飾 Android 平台特定專案中的 `MainActivity` 類別。 此外，您可以藉由在畫面最右邊的 [詳細資料] 頁面，然後按一下畫面底部的 [ *上一頁* ] 按鈕，將 [現用詳細資料] 頁面移至左邊，藉以將飛出視窗頁面移至左側。
- 在通用 Windows 平台 (UWP) 上，導覽列出現在頁面頂端，並具有可巡覽至詳細資料頁面的按鈕。

詳細資料頁面會顯示對應至 [飛出視窗] 頁面上所選取專案的資料，而且詳細資料頁面的主要元件會顯示在下列螢幕擷取畫面中：

![詳細資料頁面元件](flyoutpage-images/detailpage-components.png)

詳細資料頁面包含導覽列，其內容會因平台而不同：

- 在 iOS 上，導覽列會出現在頁面頂端並顯示標題，並具有可返回飛出視窗頁面的按鈕，前提是詳細資料頁面實例包裝在 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 實例中。 此外，您可以將 [詳細資料] 頁面向右刷，以將飛出視窗頁面傳回至。
- 在 Android 上，導覽列會出現在頁面頂端，並顯示標題、圖示，以及返回飛出視窗頁面的按鈕。 該圖示是在 `[Activity]` 屬性中定義，以裝飾 Android 平台特定專案中的 `MainActivity` 類別。
- 在 UWP 上，導覽列會出現在頁面頂端並顯示標題，並具有可返回飛出視窗頁面的按鈕。

## <a name="navigation-behavior"></a>流覽行為

飛出視窗和詳細資料頁面之間的導覽體驗行為，與平臺相依：

- 在 iOS 上，詳細資料頁面會在左側的飛出視窗頁面上 *滑動* 至右側，而且詳細資料頁面的左邊部分仍然可見。
- 在 Android 上，詳細資料和飛出視窗頁面會彼此重 *迭* 。
- 在 UWP 上，如果屬性設定為，則飛出視窗頁面會在詳細資料頁面的左邊滑出 [`FlyoutLayoutBehavior`](xref:Xamarin.Forms.FlyoutPage.FlyoutLayoutBehavior) `Popover` 。

在橫向模式中會觀察到類似的行為，不同之處在于 iOS 和 Android 上的飛出視窗頁面在直向模式中具有與飛出視窗頁面一樣的寬度，因此詳細資料頁面將會顯示。

如需控制導覽行為的詳細資訊，請參閱 [控制詳細資料頁面版面配置行為](#control-the-detail-page-layout-behavior)。

## <a name="create-a-flyoutpage"></a>建立 FlyoutPage

的 [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) contains [`Flyout`](xref:Xamarin.Forms.FlyoutPage.Flyout) 和 [`Detail`](xref:Xamarin.Forms.FlyoutPage.Detail) 屬性都是類型 [`Page`](xref:Xamarin.Forms.Page) ，分別用來取得和設定飛出視窗和詳細資料頁面。

> [!IMPORTANT]
> [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage)是設計成根頁面，並使用它做為其他頁面類型中的子頁面，可能會導致非預期且不一致的行為。 此外，建議的飛出視窗頁面 `FlyoutPage` 應該一律為 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 實例，而且詳細資料頁面應該只填入 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 、 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 和 `ContentPage` 實例。 這有助於跨所有平台確保一致的使用者體驗。

下列 XAML 程式碼範例顯示 [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) 設定 [`Flyout`](xref:Xamarin.Forms.FlyoutPage.Flyout) 和 [`Detail`](xref:Xamarin.Forms.FlyoutPage.Detail) 屬性的：

```xaml
<FlyoutPage xmlns="http://xamarin.com/schemas/2014/forms"
            xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
            xmlns:local="clr-namespace:FlyoutPageNavigation;assembly=FlyoutPageNavigation"
            x:Class="FlyoutPageNavigation.MainPage">
    <FlyoutPage.Flyout>
        <local:FlyoutMenuPage x:Name="flyoutPage" />
    </FlyoutPage.Flyout>
    <FlyoutPage.Detail>
        <NavigationPage>
            <x:Arguments>
                <local:ContactsPage />
            </x:Arguments>
        </NavigationPage>
    </FlyoutPage.Detail>
</FlyoutPage>
```

下列程式碼範例顯示 [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) 在 c # 中建立的對等專案：

```csharp
public class MainPageCS : FlyoutPage
{
    FlyoutMenuPageCS flyoutPage;

    public MainPageCS()
    {
        flyoutPage = new FlyoutMenuPageCS();
        Flyout = flyoutPage;
        Detail = new NavigationPage(new ContactsPageCS());
        ...
    }
    ...
}    
```

[`Flyout`](xref:Xamarin.Forms.FlyoutPage.Flyout)屬性設定為 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 實例。 [`Detail`](xref:Xamarin.Forms.FlyoutPage.Detail)屬性會設定為 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 包含 `ContentPage` 實例的。

### <a name="create-the-flyout-page"></a>建立飛出視窗頁面

下列 XAML 程式碼範例 `FlyoutMenuPage` 會顯示透過屬性參考之物件的宣告 [`Flyout`](xref:Xamarin.Forms.FlyoutPage.Flyout) ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="using:FlyoutPageNavigation"
             x:Class="FlyoutPageNavigation.FlyoutMenuPage"
             Padding="0,40,0,0"
             IconImageSource="hamburger.png"
             Title="Personal Organiser">
    <StackLayout>
        <ListView x:Name="listView" x:FieldModifier="public">
            <ListView.ItemsSource>
                <x:Array Type="{x:Type local:FlyoutPageItem}">
                    <local:FlyoutPageItem Title="Contacts" IconSource="contacts.png" TargetType="{x:Type local:ContactsPage}" />
                    <local:FlyoutPageItem Title="TodoList" IconSource="todo.png" TargetType="{x:Type local:TodoListPage}" />
                    <local:FlyoutPageItem Title="Reminders" IconSource="reminders.png" TargetType="{x:Type local:ReminderPage}" />
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

頁面是由將 [`ListView`](xref:Xamarin.Forms.ListView) 其 [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) 屬性設定為物件陣列，以 XAML 填入資料的。 `FlyoutPageItem` 每個 `FlyoutPageItem` 會定義 `Title`、`IconSource` 和 `TargetType` 屬性。

[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)會指派給 [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) 屬性，以顯示每個屬性 `FlyoutPageItem` 。 `DataTemplate`包含 [`ViewCell`](xref:Xamarin.Forms.ViewCell) 由和組成的 [`Image`](xref:Xamarin.Forms.Image) [`Label`](xref:Xamarin.Forms.Label) 。 會 [`Image`](xref:Xamarin.Forms.Image) 顯示 `IconSource` 屬性值，並 [`Label`](xref:Xamarin.Forms.Label) 顯示每個屬性值的 `Title` 屬性值 `FlyoutPageItem` 。

頁面已設定其 [`Title`](xref:Xamarin.Forms.Page.Title) 和 [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) 屬性。 圖示會出現在詳細資料頁面上，前提是詳細資料頁面有標題列。 若要在 iOS 上啟用此功能，請將詳細資料頁面實例包裝在 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 實例中。

> [!NOTE]
> [`Flyout`](xref:Xamarin.Forms.FlyoutPage.Flyout)頁面必須 [`Title`](xref:Xamarin.Forms.Page.Title) 設定其屬性，否則將會發生例外狀況。

下列程式碼範例示範以 C# 建立的相等頁面：

```csharp
public class FlyoutMenuPageCS : ContentPage
{
    ListView listView;
    public ListView ListView { get { return listView; } }

    public FlyoutMenuPageCS()
    {
        var flyoutPageItems = new List<FlyoutPageItem>();
        flyoutPageItems.Add(new FlyoutPageItem
        {
            Title = "Contacts",
            IconSource = "contacts.png",
            TargetType = typeof(ContactsPageCS)
        });
        flyoutPageItems.Add(new FlyoutPageItem
        {
            Title = "TodoList",
            IconSource = "todo.png",
            TargetType = typeof(TodoListPageCS)
        });
        flyoutPageItems.Add(new FlyoutPageItem
        {
            Title = "Reminders",
            IconSource = "reminders.png",
            TargetType = typeof(ReminderPageCS)
        });

        listView = new ListView
        {
            ItemsSource = flyoutPageItems,
            ItemTemplate = new DataTemplate(() =>
            {
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
        Padding = new Thickness(0, 40, 0, 0);
        Content = new StackLayout
        {
            Children = { listView }
        };
    }
}
```

下列螢幕擷取畫面顯示每個平臺上的飛出視窗頁面：

![飛出視窗頁面範例](flyoutpage-images/flyoutpage.png)

### <a name="create-and-display-the-detail-page"></a>建立並顯示詳細資料頁面

`FlyoutMenuPage`實例包含的 `ListView` 屬性會公開其 [`ListView`](xref:Xamarin.Forms.ListView) 實例，讓 `MainPage` [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) 實例可以註冊事件處理常式來處理 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 事件。 這可讓 `MainPage` 實例將屬性設 [`Detail`](xref:Xamarin.Forms.FlyoutPage.Detail) 為表示所選項目的頁面 `ListView` 。 下列程式碼範例示範事件處理常式：

```csharp
public partial class MainPage : FlyoutPage
{
    public MainPage()
    {
        ...
        flyoutPage.listView.ItemSelected += OnItemSelected;
    }

    void OnItemSelected(object sender, SelectedItemChangedEventArgs e)
    {
        var item = e.SelectedItem as FlyoutPageItem;
        if (item != null)
        {
            Detail = new NavigationPage((Page)Activator.CreateInstance(item.TargetType));
            flyoutPage.listView.SelectedItem = null;
            IsPresented = false;
        }
    }
}
```

`OnItemSelected` 方法會執行下列動作：

- 它會 [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) 從實例中抓取， [`ListView`](xref:Xamarin.Forms.ListView) 並假設不是，會 `null` 將詳細資料頁面設定為儲存在的屬性中之頁面類型的新實例 `TargetType` `FlyoutPageItem` 。 頁面類型會包裝在實例中， [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 以確保在 [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) `FlyoutMenuPage` iOS 的詳細資料頁面上，會顯示透過中的屬性所參考的圖示。
- 中的選取專案 [`ListView`](xref:Xamarin.Forms.ListView) 會設定為， `null` 以確保下次顯示時不會 `ListView` 選取任何專案 `FlyoutMenuPage` 。
- 詳細資料頁面會藉由將屬性設定為，向使用者顯示 [`FlyoutPage.IsPresented`](xref:Xamarin.Forms.FlyoutPage.IsPresented) `false` 。 這個屬性會控制是否顯示飛出視窗或詳細資料頁面。 它應該設定為 `true` 以顯示 [飛出視窗] 頁面，以及 `false` 顯示 [詳細資料] 頁面。

下列螢幕擷取畫面顯示 [ `ContactPage` 詳細資料] 頁面，此頁面會在 [飛出視窗] 頁面上選取之後顯示：

![詳細資料頁面範例](flyoutpage-images/detailpage.png)

### <a name="control-the-detail-page-layout-behavior"></a>控制詳細資料頁面版面配置行為

[`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage)管理飛出視窗和詳細資料頁面的方式取決於應用程式是在手機或平板電腦上執行、裝置的方向，以及屬性的值 [`FlyoutLayoutBehavior`](xref:Xamarin.Forms.FlyoutPage.FlyoutLayoutBehavior) 。 此屬性會決定詳細資料頁面的顯示方式。 可能值如下：

- `Default` –頁面會使用平臺預設值顯示。
- `Popover` –詳細資料頁面涵蓋或部分涵蓋飛出視窗頁面。
- `Split` – [飛出視窗] 頁面會顯示在左側，而 [詳細資料] 頁面則位於右邊。
- `SplitOnLandscape` –當裝置處於橫向時，會使用分割畫面。
- `SplitOnPortrait` –當裝置處於直向時，會使用分割畫面。

下列 XAML 程式碼範例示範如何 [`FlyoutLayoutBehavior`](xref:Xamarin.Forms.FlyoutPage.FlyoutLayoutBehavior) 在上設定屬性 [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) ：

```xaml
<FlyoutPage xmlns="http://xamarin.com/schemas/2014/forms"
            xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
            x:Class="FlyoutPageNavigation.MainPage"
            FlyoutLayoutBehavior="Popover">
  ...
</FlyoutPage>
```

下列程式碼範例顯示 [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) 在 c # 中建立的對等專案：

```csharp
public class MainPageCS : FlyoutPage
{
    FlyoutMenuPageCS flyoutPage;

    public MainPageCS()
    {
        ...
        FlyoutLayoutBehavior = FlyoutLayoutBehavior.Popover;
    }
}
```

> [!IMPORTANT]
> 屬性的值 [`FlyoutLayoutBehavior`](xref:Xamarin.Forms.FlyoutPage.FlyoutLayoutBehavior) 只會影響在平板電腦或桌上型電腦上執行的應用程式。 在手機上執行的應用程式一律會有 `Popover` 行為。

## <a name="related-links"></a>相關連結

- [頁面種類](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [FlyoutPage (範例) ](/samples/xamarin/xamarin-forms-samples/navigation-flyoutpage)
- [FlyoutPage API](xref:Xamarin.Forms.FlyoutPage)
