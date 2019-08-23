---
title: Xamarin. 表單 MenuItem
description: MenuItem 類別用來建立功能表的功能表項目, 例如 ListView 專案內容功能表和 Shell 應用程式飛出視窗功能表。
ms.prod: xamarin
ms.assetId: 62655C21-6053-466D-A7F4-DE2BE36538F5
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 08/01/2019
ms.openlocfilehash: cbc39ee38ce623ce446d50494829119058fc88dc
ms.sourcegitcommit: 1341f2950b775a4daa7d0548a51fdef759afd6e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2019
ms.locfileid: "69976478"
---
# <a name="xamarinforms-menuitem"></a>Xamarin. 表單 MenuItem

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-menuitem/)

[Xamarin [`MenuItem`](xref:Xamarin.Forms.MenuItem) ] 類別會定義功能表的功能表項目, `ListView`例如專案內容功能表和 Shell 應用程式飛出視窗功能表。

下列螢幕擷取畫面顯示`MenuItem` iOS 和 Android `ListView`上內容功能表中的物件:

[ (menuitem-images/menuitem-demo-cropped.png "Ios 和 android 上")的「 ![ios 和 android 上的 MenuItems]」 MenuItems](menuitem-images/menuitem-demo-full.png#lightbox "IOS 和 Android 上的 MenuItemsfull image")

`MenuItem`類別會定義下列屬性:

* [`Command`](xref:Xamarin.Forms.MenuItem.Command)`ICommand`是, 允許將使用者動作 (例如滑鼠點按或按一下) 系結至 viewmodel 上定義的命令。
* [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter)是, 它會指定應傳遞至的`Command`參數。 `object`
* [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource)是定義顯示圖示的值。`ImageSource`
* [`IsDestructive`](xref:Xamarin.Forms.MenuItem.IsDestructive)這是`bool`一個值, 指出是否`MenuItem`會從清單中移除其相關聯的 UI 元素。
* [`IsEnabled`](xref:Xamarin.Forms.MenuItem.IsEnabled)這是`bool`判斷此物件是否會回應使用者輸入的值。
* [`Text`](xref:Xamarin.Forms.MenuItem.Text)這是指定顯示文字的值。`string`

這些屬性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件所支援`MenuItem` , 因此實例可以是資料系結的目標。

## <a name="create-a-menuitem"></a>建立 MenuItem

`MenuItem`物件可以在`ListView`物件專案的內容功能表中使用。 最常見的模式`MenuItem`是在`ViewCell`實例內建立物件, 以當做的`DataTemplate`物件`ListView` `ItemTemplate`使用。 當物件已填入時`DataTemplate`, 會使用建立每個專案, 並在`MenuItem`啟用專案的操作功能表時公開選項。 `ListView`

下列範例顯示`MenuItem` `ListView`物件內容中的具現化:

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

也`MenuItem`可以在程式碼中建立:

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

`MenuItem` 類別會公開 `Clicked` 事件。 事件處理常式可以附加至這個事件, 以回應在 XAML 中點按或`MenuItem`按一下該實例:

```xaml
<MenuItem ...
          Clicked="OnItemClicked" />
```

事件處理常式也可以附加在程式碼中:

```csharp
MenuItem item = new MenuItem { ... }
item.Clicked += OnItemClicked;
```

先前的範例參考`OnItemClicked`了事件處理常式。 下列程式碼顯示範例的執行:

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

類別透過[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件和`ICommand`介面, 支援模型視圖 ViewModel (MVVM) 模式。 `MenuItem` 下列 XAML 會顯示`MenuItem`系結至 viewmodel 上定義之命令的實例:

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

在上述範例中, 會`MenuItem`定義兩個物件, `Command`並`CommandParameter`將其和屬性系結至 viewmodel 上的命令。 Viewmodel 包含 XAML 中參考的命令:

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

範例應用程式包含一個`DataService`類別, 用來取得用來填入`ListView`物件的專案清單。 Viewmodel 會具現化, 並具有`DataService`類別中的專案, 並`BindingContext`在程式碼後置中設定為:

```csharp
public MenuItemXamlMvvmPage()
{
    InitializeComponent();
    BindingContext = new ListPageViewModel(DataService.GetListItems());
}
```

## <a name="menuitem-icons"></a>MenuItem 圖示

> [!WARNING]
> `MenuItem`物件只會在 Android 上顯示圖示。 在其他平臺上, 只會顯示`Text`屬性所指定的文字。

 圖示是使用`IconImageSource`屬性所指定。 如果指定了圖示, 將不會顯示`Text`屬性所指定的文字。 下列螢幕擷取畫面顯示`MenuItem`在 Android 上具有圖示的:

「 ![Android 上 MenuItem 圖示的螢幕擷取畫面]」(menuitem-images/menuitem-android-icon.png "Android 上 MenuItem 圖示的螢幕擷取畫面")

如需在 [表單] 中使用影像的詳細資訊, 請參閱[xamarin 中的影像](~/xamarin-forms/user-interface/images.md)。

## <a name="cross-platform-context-menu-behavior"></a>跨平臺內容功能表行為

內容功能表會在每個平臺上以不同方式存取和顯示。

在 Android 上, 內容功能表會以長按的方式在清單專案上啟用。 內容功能表會取代 [標題] 和 [導覽列`MenuItem` ] 區域, 而選項會顯示為水準按鈕。

「 ![Android 上內容功能表的螢幕擷取畫面]」(menuitem-images/menuitem-android-icon.png "Android 上內容功能表的螢幕擷取畫面")

在 iOS 上, 內容功能表會藉由在清單專案上進行輕刷來啟用。 內容功能表會顯示在清單專案上, 並`MenuItems`顯示為水準按鈕。

「 ![IOS 上內容功能表的螢幕擷取畫面]」(menuitem-images/menuitem-ios-contextmenu.png "IOS 上內容功能表的螢幕擷取畫面")

在 UWP 上, 以滑鼠右鍵按一下清單專案即可啟動內容功能表。 內容功能表會以垂直清單的形式顯示在游標附近。

「 ![UWP 上內容功能表的螢幕擷取畫面]」(menuitem-images/menuitem-uwp.png "UWP 上內容功能表的螢幕擷取畫面")

## <a name="related-links"></a>相關連結

* [MenuItem 示範](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-menuitem/)
* [Xamarin 中的影像](~/xamarin-forms/user-interface/images.md)
