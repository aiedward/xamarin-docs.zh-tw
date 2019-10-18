---
title: Xamarin. 表單 MenuItem
description: MenuItem 類別用來建立功能表的功能表項目，例如 ListView 專案內容功能表和 Shell 應用程式飛出視窗功能表。
ms.prod: xamarin
ms.assetId: 62655C21-6053-466D-A7F4-DE2BE36538F5
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 08/01/2019
ms.openlocfilehash: 5bc36f03eac4ced7c19a0053dfea93dbe2ca4497
ms.sourcegitcommit: 850dd7a3ed10eb3f66692e765d3e31438cff0288
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2019
ms.locfileid: "72531018"
---
# <a name="xamarinforms-menuitem"></a>Xamarin. 表單 MenuItem

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-menuitemdemos/)

[Xamarin] [`MenuItem`](xref:Xamarin.Forms.MenuItem)類別會定義功能表的功能表項目，例如 `ListView` 專案內容功能表和 Shell 應用程式飛出視窗功能表。

下列螢幕擷取畫面顯示 iOS 和 Android 上 `ListView` 內容功能表中的 `MenuItem` 物件：

[![「IOS 和 Android 上的 MenuItems」](menuitem-images/menuitem-demo-cropped.png "IOS 和 Android 上的 MenuItems")](menuitem-images/menuitem-demo-full.png#lightbox "IOS 和 Android 上的 MenuItems full image")

@No__t_0 類別會定義下列屬性：

* [`Command`](xref:Xamarin.Forms.MenuItem.Command)是一種 `ICommand`，可讓您將使用者動作（例如手指點按或按一下）系結至 viewmodel 上定義的命令。
* [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter)是指定應傳遞至 `Command` 之參數的 `object`。
* [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource)是定義顯示圖示的 `ImageSource` 值。
* [`IsDestructive`](xref:Xamarin.Forms.MenuItem.IsDestructive)是 `bool` 值，指出 `MenuItem` 是否會從清單中移除其相關聯的 UI 元素。
* [`IsEnabled`](xref:Xamarin.Forms.MenuItem.IsEnabled)是 `bool` 值，可判斷此物件是否會回應使用者輸入。
* [`Text`](xref:Xamarin.Forms.MenuItem.Text)是指定顯示文字的 `string` 值。

這些屬性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件所支援，因此 `MenuItem` 實例可以是資料系結的目標。

## <a name="create-a-menuitem"></a>建立 MenuItem

`MenuItem` 物件可以在 `ListView` 物件專案的內容功能表中使用。 最常見的模式是在 `ViewCell` 實例中建立 `MenuItem` 物件，這會用來做為 `ListView`s `ItemTemplate` 的 `DataTemplate` 物件。 填入 `ListView` 物件時，會使用 `DataTemplate` 建立每個專案，並在啟用專案的操作功能表時公開 `MenuItem` 選項。

下列範例顯示 `ListView` 物件內容中的 `MenuItem` 具現化：

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

您也可以在程式碼中建立 `MenuItem`：

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

`MenuItem` 類別會公開 `Clicked` 事件。 事件處理常式可以附加至這個事件，以回應在 XAML 中點擊或按一下 `MenuItem` 實例：

```xaml
<MenuItem ...
          Clicked="OnItemClicked" />
```

事件處理常式也可以附加在程式碼中：

```csharp
MenuItem item = new MenuItem { ... }
item.Clicked += OnItemClicked;
```

先前的範例參考 `OnItemClicked` 事件處理常式。 下列程式碼顯示範例的執行：

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

@No__t_0 類別透過[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件和 `ICommand` 介面，支援模型視圖 VIEWMODEL （MVVM）模式。 下列 XAML 會顯示系結至 viewmodel 上所定義之命令的 `MenuItem` 實例：

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

在上述範例中，會定義兩個 `MenuItem` 物件，其 `Command` 和 `CommandParameter` 屬性系結至 viewmodel 上的命令。 Viewmodel 包含 XAML 中參考的命令：

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

範例應用程式包含一個 `DataService` 類別，用來取得用來填入 `ListView` 物件的專案清單。 Viewmodel 會具現化，其中包含 `DataService` 類別中的專案，並在程式碼後置中設定為 `BindingContext`：

```csharp
public MenuItemXamlMvvmPage()
{
    InitializeComponent();
    BindingContext = new ListPageViewModel(DataService.GetListItems());
}
```

## <a name="menuitem-icons"></a>MenuItem 圖示

> [!WARNING]
> `MenuItem` 物件只會在 Android 上顯示圖示。 在其他平臺上，只會顯示 [`Text`] 屬性所指定的文字。

 圖示是使用 `IconImageSource` 屬性指定的。 如果指定了圖示，將不會顯示 [`Text`] 屬性所指定的文字。 下列螢幕擷取畫面顯示在 Android 上具有圖示的 `MenuItem`：

![「Android 上 MenuItem 圖示的螢幕擷取畫面」](menuitem-images/menuitem-android-icon.png "Android 上 MenuItem 圖示的螢幕擷取畫面")

如需在 [表單] 中使用影像的詳細資訊，請參閱[xamarin 中的影像](~/xamarin-forms/user-interface/images.md)。

## <a name="cross-platform-context-menu-behavior"></a>跨平臺內容功能表行為

內容功能表會在每個平臺上以不同方式存取和顯示。

在 Android 上，內容功能表會以長按的方式在清單專案上啟用。 內容功能表會取代 [標題] 和 [導覽列] 區域，`MenuItem` 選項會顯示為水準按鈕。

![「Android 上內容功能表的螢幕擷取畫面」](menuitem-images/menuitem-android-icon.png "Android 上內容功能表的螢幕擷取畫面")

在 iOS 上，內容功能表會藉由在清單專案上進行輕刷來啟用。 內容功能表會顯示在清單專案上，而 `MenuItems` 會顯示為水準按鈕。

![「IOS 上內容功能表的螢幕擷取畫面」](menuitem-images/menuitem-ios-contextmenu.png "IOS 上內容功能表的螢幕擷取畫面")

在 UWP 上，以滑鼠右鍵按一下清單專案即可啟動內容功能表。 內容功能表會以垂直清單的形式顯示在游標附近。

![「UWP 上內容功能表的螢幕擷取畫面」](menuitem-images/menuitem-uwp.png "UWP 上內容功能表的螢幕擷取畫面")

## <a name="related-links"></a>相關連結

* [MenuItem 示範](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-menuitemdemos/)
* [Xamarin 中的影像](~/xamarin-forms/user-interface/images.md)
