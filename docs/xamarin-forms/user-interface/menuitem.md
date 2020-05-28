---
title: Xamarin.FormsMenuItem
description: ''
ms.prod: ''
ms.assetId: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: de8c6bff2c9dc72821692708f5852cd874c31ede
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139200"
---
# <a name="xamarinforms-menuitem"></a>Xamarin.FormsMenuItem

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-menuitemdemos/)

Xamarin.Forms [`MenuItem`](xref:Xamarin.Forms.MenuItem) 類別會定義功能表的功能表項目，例如 `ListView` 專案內容功能表和 Shell 應用程式飛出視窗功能表。

下列螢幕擷取畫面顯示 `MenuItem` `ListView` IOS 和 Android 上內容功能表中的物件：

[![「IOS 和 Android 上的 MenuItems」](menuitem-images/menuitem-demo-cropped.png "IOS 和 Android 上的 MenuItems")](menuitem-images/menuitem-demo-full.png#lightbox "IOS 和 Android 上的 MenuItems 完整映射")

`MenuItem`類別會定義下列屬性：

* [`Command`](xref:Xamarin.Forms.MenuItem.Command)是， `ICommand` 允許將使用者動作（例如滑鼠點按或按一下）系結至 viewmodel 上定義的命令。
* [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter)是 `object` ，它會指定應傳遞至的參數 `Command` 。
* [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource)是 `ImageSource` 定義顯示圖示的值。
* [`IsDestructive`](xref:Xamarin.Forms.MenuItem.IsDestructive)這是一個 `bool` 值，指出是否會 `MenuItem` 從清單中移除其相關聯的 UI 元素。
* [`IsEnabled`](xref:Xamarin.Forms.MenuItem.IsEnabled)這是一個 `bool` 值，指出此物件是否會回應使用者輸入。
* [`Text`](xref:Xamarin.Forms.MenuItem.Text)這是 `string` 指定顯示文字的值。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，因此 `MenuItem` 實例可以是資料系結的目標。

## <a name="create-a-menuitem"></a>建立 MenuItem

`MenuItem`物件可以在物件專案的內容功能表中使用 `ListView` 。 最常見的模式是在 `MenuItem` 實例內建立物件，以當做的 `ViewCell` `DataTemplate` 物件使用 `ListView` `ItemTemplate` 。 當 `ListView` 物件已填入時，會使用建立每個專案 `DataTemplate` ，並在 `MenuItem` 啟用專案的操作功能表時公開選項。

下列範例顯示物件內容中的具現 `MenuItem` 化 `ListView` ：

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

`MenuItem`也可以在程式碼中建立：

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

## <a name="define-menuitem-behavior-with-events"></a>定義事件的 MenuItem 行為

`MenuItem` 類別會公開 `Clicked` 事件。 事件處理常式可以附加至這個事件，以回應在 XAML 中點按或按一下該 `MenuItem` 實例：

```xaml
<MenuItem ...
          Clicked="OnItemClicked" />
```

事件處理常式也可以附加在程式碼中：

```csharp
MenuItem item = new MenuItem { ... }
item.Clicked += OnItemClicked;
```

先前的範例參考了 `OnItemClicked` 事件處理常式。 下列程式碼顯示範例的執行：

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

`MenuItem`類別透過 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 物件和介面，支援模型視圖 VIEWMODEL （MVVM）模式 `ICommand` 。 下列 XAML 會顯示系結 `MenuItem` 至 viewmodel 上定義之命令的實例：

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

範例應用程式包含一個 `DataService` 類別，用來取得用來填入物件的專案清單 `ListView` 。 Viewmodel 會具現化，並具有類別中的專案 `DataService` ，並 `BindingContext` 在程式碼後置中設定為：

```csharp
public MenuItemXamlMvvmPage()
{
    InitializeComponent();
    BindingContext = new ListPageViewModel(DataService.GetListItems());
}
```

## <a name="menuitem-icons"></a>MenuItem 圖示

> [!WARNING]
> `MenuItem`物件只會在 Android 上顯示圖示。 在其他平臺上，只會顯示內容所指定的文字 `Text` 。

 圖示是使用屬性所指定 `IconImageSource` 。 如果指定了圖示，將不會顯示內容所指定的文字 `Text` 。 下列螢幕擷取畫面顯示在 `MenuItem` Android 上具有圖示的：

![「Android 上 MenuItem 圖示的螢幕擷取畫面」](menuitem-images/menuitem-android-icon.png "Android 上 MenuItem 圖示的螢幕擷取畫面")

如需在中使用影像的詳細資訊 Xamarin.Forms ，請參閱[中的 Xamarin.Forms 影像](~/xamarin-forms/user-interface/images.md)。

## <a name="enable-or-disable-a-menuitem-at-runtime"></a>在執行時間啟用或停用 MenuItem

若要啟用 `MenuItem` 在執行時間停用，請將其屬性系結 `Command` 至 `ICommand` 執行，並確定 `canExecute` 委派會適當地啟用和停用 `ICommand` 。

> [!IMPORTANT]
> `IsEnabled`使用 `Command` 屬性來啟用或停用時，請勿將屬性系結至另一個屬性 `MenuItem` 。

下列範例顯示的屬性系結 `MenuItem` `Command` 至 `ICommand` 名為的 `MyCommand` 。

```xaml
<MenuItem Text="My menu item"
          Command="{Binding MyCommand}" />
```

此 `ICommand` 執行需要一個 `canExecute` 委派來傳回屬性的值 `bool` ，以啟用和停用 `MenuItem` ：

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

    public ToolbarItemViewModel()
    {
        MyCommand = new Command(() =>
        {
            // Execute logic here
        },
        () => IsToolbarItemEnabled);
    }
}
```

在此範例中， `MenuItem` 會停用，直到 `IsMenuItemEnabled` 設定屬性為止。 發生這種情況時，會 `Command.ChangeCanExecute` 呼叫方法，這會 `canExecute` `MyCommand` 重新評估的委派。

## <a name="cross-platform-context-menu-behavior"></a>跨平臺內容功能表行為

內容功能表會在每個平臺上以不同方式存取和顯示。

在 Android 上，內容功能表會以長按的方式在清單專案上啟用。 內容功能表會取代 [標題] 和 [導覽列] 區域，而 `MenuItem` 選項會顯示為水準按鈕。

![「Android 上內容功能表的螢幕擷取畫面」](menuitem-images/menuitem-android-icon.png "Android 上內容功能表的螢幕擷取畫面")

在 iOS 上，內容功能表會藉由在清單專案上進行輕刷來啟用。 內容功能表會顯示在清單專案上，並 `MenuItems` 顯示為水準按鈕。

![「IOS 上內容功能表的螢幕擷取畫面」](menuitem-images/menuitem-ios-contextmenu.png "IOS 上內容功能表的螢幕擷取畫面")

在 UWP 上，以滑鼠右鍵按一下清單專案即可啟動內容功能表。 內容功能表會以垂直清單的形式顯示在游標附近。

![「UWP 上內容功能表的螢幕擷取畫面」](menuitem-images/menuitem-uwp.png "UWP 上內容功能表的螢幕擷取畫面")

## <a name="related-links"></a>相關連結

* [MenuItem 示範](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-menuitemdemos/)
* [中的影像Xamarin.Forms](~/xamarin-forms/user-interface/images.md)
