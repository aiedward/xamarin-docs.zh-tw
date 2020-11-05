---
title: Xamarin.Forms MenuItem
description: MenuItem 類別是用來建立功能表的功能表項目，例如 ListView 專案內容功能表和 Shell 應用程式飛出視窗功能表。
ms.prod: xamarin
ms.assetId: 62655C21-6053-466D-A7F4-DE2BE36538F5
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 08/01/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a7adb890ed103b595aa777d33254ab2fcb776901
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93368137"
---
# <a name="no-locxamarinforms-menuitem"></a>Xamarin.Forms MenuItem

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-menuitemdemos/)

Xamarin.Forms [`MenuItem`](xref:Xamarin.Forms.MenuItem) 類別會定義功能表的功能表項目，例如 `ListView` 專案內容功能表和 Shell 應用程式飛出視窗功能表。

下列螢幕擷取畫面顯示 `MenuItem` `ListView` IOS 和 Android 上內容功能表中的物件：

[![「IOS 和 Android 上的 MenuItems」](menuitem-images/menuitem-demo-cropped.png "IOS 和 Android 上的 MenuItems")](menuitem-images/menuitem-demo-full.png#lightbox "IOS 和 Android 上的 MenuItems 完整映射")

`MenuItem`類別會定義下列屬性：

* [`Command`](xref:Xamarin.Forms.MenuItem.Command) 是允許系結 `ICommand` 使用者動作（例如，點按或按一下）至 viewmodel 上所定義之命令的。
* [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter) 是 `object` ，指定應傳遞至的參數 `Command` 。
* [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource) 是 `ImageSource` 定義顯示圖示的值。
* [`IsDestructive`](xref:Xamarin.Forms.MenuItem.IsDestructive) 這是一個 `bool` 值，指出是否 `MenuItem` 從清單中移除其相關聯的 UI 元素。
* [`IsEnabled`](xref:Xamarin.Forms.MenuItem.IsEnabled) 這是一個 `bool` 值，指出此物件是否會回應使用者輸入。
* [`Text`](xref:Xamarin.Forms.MenuItem.Text) 這是 `string` 指定顯示文字的值。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，因此 `MenuItem` 實例可以是資料系結的目標。

## <a name="create-a-menuitem"></a>建立 MenuItem

`MenuItem` 物件可以在物件專案的內容功能表中使用 `ListView` 。 最常見的模式是在 `MenuItem` 實例內建立物件 `ViewCell` ，以做為的物件使用 `DataTemplate` `ListView` `ItemTemplate` 。 `ListView`填入物件時，它會使用來建立每個專案 `DataTemplate` ，而在 `MenuItem` 專案的內容功能表啟動時公開選項。

下列範例顯示在 `MenuItem` 物件的內容中具現化 `ListView` ：

```xaml
<ListView>
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
                <ViewCell.ContextActions>
                    <MenuItem Text="Context Menu Option" />
                </ViewCell.ContextActions>
                <Label Text="{Binding .}" />
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

您 `MenuItem` 也可以在程式碼中建立：

```csharp
// A function returns a ViewCell instance that
// is used as the template for each list item
DataTemplate dataTemplate = new DataTemplate(() =>
{
    // A Label displays the list item text
    Label label = new Label();
    label.SetBinding(Label.TextProperty, ".");

    // A ViewCell serves as the DataTemplate
    ViewCell viewCell = new ViewCell
    {
        View = label
    };

    // Add a MenuItem instance to the ContextActions
    MenuItem menuItem = new MenuItem
    {
        Text = "Context Menu Option"
    };
    viewCell.ContextActions.Add(menuItem);

    // The function returns the custom ViewCell
    // to the DataTemplate constructor
    return viewCell;
});

// Finally, the dataTemplate is provided to
// the ListView object
ListView listView = new ListView
{
    ...
    ItemTemplate = dataTemplate
};
```

## <a name="define-menuitem-behavior-with-events"></a>使用事件定義 MenuItem 行為

`MenuItem` 類別會公開 `Clicked` 事件。 事件處理常式可以附加至這個事件，以回應在 XAML 中的點按或按一下動作 `MenuItem` ：

```xaml
<MenuItem ...
          Clicked="OnItemClicked" />
```

事件處理常式也可以在程式碼中附加：

```csharp
MenuItem item = new MenuItem { ... }
item.Clicked += OnItemClicked;
```

先前的範例參考了 `OnItemClicked` 事件處理常式。 下列程式碼顯示範例執行：

```csharp
void OnItemClicked(object sender, EventArgs e)
{
    // The sender is the menuItem
    MenuItem menuItem = sender as MenuItem;

    // Access the list item through the BindingContext
    var contextItem = menuItem.BindingContext;

    // Do something with the contextItem here
}
```

## <a name="define-menuitem-behavior-with-mvvm"></a>使用 MVVM 定義 MenuItem 行為

`MenuItem`類別透過 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 物件和介面，支援模型視圖 VIEWMODEL (MVVM) 模式 `ICommand` 。 下列 XAML 顯示系結 `MenuItem` 至 viewmodel 上所定義之命令的實例：

```xaml
<ContentPage.BindingContext>
    <viewmodels:ListPageViewModel />
</ContentPage.BindingContext>

<StackLayout>
    <Label Text="{Binding Message}" ... />
    <ListView ItemsSource="{Binding Items}">
        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <ViewCell.ContextActions>
                        <MenuItem Text="Edit"
                                    IconImageSource="icon.png"
                                    Command="{Binding Source={x:Reference contentPage}, Path=BindingContext.EditCommand}"
                                    CommandParameter="{Binding .}"/>
                        <MenuItem Text="Delete"
                                    Command="{Binding Source={x:Reference contentPage}, Path=BindingContext.DeleteCommand}"
                                    CommandParameter="{Binding .}"/>
                    </ViewCell.ContextActions>
                    <Label Text="{Binding .}" />
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</StackLayout>
```

在上述範例中， `MenuItem` 會定義兩個物件，並將其和屬性系結 `Command` `CommandParameter` 至 viewmodel 上的命令。 Viewmodel 包含 XAML 中參考的命令：

```csharp
public class ListPageViewModel : INotifyPropertyChanged
{
    ...

    public ICommand EditCommand => new Command<string>((string item) =>
    {
        Message = $"Edit command was called on: {item}";
    });

    public ICommand DeleteCommand => new Command<string>((string item) =>
    {
        Message = $"Delete command was called on: {item}";
    });
}
```

範例應用程式包含 `DataService` 類別，用來取得用來填入物件的專案清單 `ListView` 。 Viewmodel 會具現化，其中包含類別中的專案 `DataService` ，並設定為 `BindingContext` 程式碼後端中的：

```csharp
public MenuItemXamlMvvmPage()
{
    InitializeComponent();
    BindingContext = new ListPageViewModel(DataService.GetListItems());
}
```

## <a name="menuitem-icons"></a>MenuItem 圖示

> [!WARNING]
> `MenuItem` 物件只會在 Android 上顯示圖示。 在其他平臺上，只會顯示內容所指定的文字 `Text` 。

 圖示是使用屬性來指定 `IconImageSource` 。 如果指定了圖示，將不會顯示內容所指定的文字 `Text` 。 下列螢幕擷取畫面顯示 `MenuItem` Android 上有圖示的：

![「Android 上 MenuItem 圖示的螢幕擷取畫面」](menuitem-images/menuitem-android-icon.png "Android 上 MenuItem 圖示的螢幕擷取畫面")

如需在中使用影像的詳細資訊 Xamarin.Forms ，請參閱[ Xamarin.Forms 中的影像](~/xamarin-forms/user-interface/images.md)。

## <a name="enable-or-disable-a-menuitem-at-runtime"></a>在執行時間啟用或停用 MenuItem

若要 `MenuItem` 在執行時間啟用停用，請將其屬性系結 `Command` 至 `ICommand` 實作為，並確定 `canExecute` 委派會適當地啟用和停用 `ICommand` 。

> [!IMPORTANT]
> `IsEnabled`使用 `Command` 屬性來啟用或停用時，請勿將屬性系結至另一個屬性 `MenuItem` 。

下列範例會顯示 `MenuItem` 其屬性系結 `Command` 至 `ICommand` 名為的 `MyCommand` 。

```xaml
<MenuItem Text="My menu item"
          Command="{Binding MyCommand}" />
```

此 `ICommand` 執行需要一個傳回 `canExecute` 屬性值的委派， `bool` 以啟用和停用 `MenuItem` ：

```csharp
public class MyViewModel : INotifyPropertyChanged
{
    bool isMenuItemEnabled = false;
    public bool IsMenuItemEnabled
    {
        get { return isMenuItemEnabled; }
        set
        {
            isMenuItemEnabled = value;
            MyCommand.ChangeCanExecute();
        }
    }

    public Command MyCommand { get; private set; }

    public MyViewModel()
    {
        MyCommand = new Command(() =>
        {
            // Execute logic here
        },
        () => IsMenuItemEnabled);
    }
}
```

在此範例中， `MenuItem` 會停用，直到 `IsMenuItemEnabled` 設定屬性為止。 發生這種情況時，會 `Command.ChangeCanExecute` 呼叫方法， `canExecute` `MyCommand` 以重新評估的委派。

## <a name="cross-platform-context-menu-behavior"></a>跨平臺內容功能表行為

操作功能表會在每個平臺上以不同方式存取及顯示。

在 Android 上，內容功能表會在清單專案上長按一段時間啟動。 內容功能表會取代標題和導覽欄區域，而且 `MenuItem` 選項會顯示為水準按鈕。

![「Android 上內容功能表的螢幕擷取畫面」](menuitem-images/menuitem-android-icon.png "Android 上內容功能表的螢幕擷取畫面")

在 iOS 上，內容功能表會透過在清單專案上進行輕刷來啟用。 內容功能表會顯示在清單專案上，並 `MenuItems` 顯示為水準按鈕。

![「IOS 上內容功能表的螢幕擷取畫面」](menuitem-images/menuitem-ios-contextmenu.png "IOS 上內容功能表的螢幕擷取畫面")

在 UWP 上，以滑鼠右鍵按一下清單專案來啟用內容功能表。 內容功能表會以垂直清單的形式顯示在資料指標附近。

![「UWP 上操作功能表的螢幕擷取畫面」](menuitem-images/menuitem-uwp.png "UWP 上內容功能表的螢幕擷取畫面")

## <a name="related-links"></a>相關連結

* [MenuItem 示範](/samples/xamarin/xamarin-forms-samples/userinterface-menuitemdemos/)
* [中的影像 Xamarin.Forms](~/xamarin-forms/user-interface/images.md)