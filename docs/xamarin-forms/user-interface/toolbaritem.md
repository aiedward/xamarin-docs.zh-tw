---
title： " Xamarin.Forms ToolbarItem" 描述： "ToolbarItem 類別是在應用程式的導覽列中使用的特殊按鈕類型。"
assetId： CC737D54-0280-46BD-A2BC-A0FB67DDD6A1 ms. 技術： xamarin-表單作者： profexorgeek ms. author： jusjohns ms. 日期：07/29/2019 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="xamarinforms-toolbaritem"></a>Xamarin.FormsToolbarItem

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-toolbaritem/)

Xamarin.Forms [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) 類別是一種特殊類型的按鈕，可以加入 `Page` 物件的 `ToolbarItems` 集合中。 每個 `ToolbarItem` 物件會在應用程式的導覽列中顯示為按鈕。 `ToolbarItem`實例可以有圖示，並顯示為主要或次要功能表項目。 `ToolbarItem`類別繼承自 [`MenuItem`](xref:Xamarin.Forms.MenuItem) 。

下列螢幕擷取畫面顯示 `ToolbarItem` iOS 和 Android 上巡覽列中的物件：

![「Android 和 iOS 上的 ToolbarItem 示範螢幕擷取畫面」](toolbaritem-images/toolbaritem-device-screenshot.png "Android 和 iOS 上的 ToolbarItem 示範螢幕擷取畫面")

`ToolbarItem`類別會定義下列屬性：

* [`Order`](xref:Xamarin.Forms.ToolbarItem.Order)這是一個 `ToolbarItemOrder` 列舉值，可判斷 `ToolbarItem` 實例是否會顯示在主要或次要功能表中。
* [`Priority`](xref:Xamarin.Forms.ToolbarItem.Priority)這是一個 `integer` 值，可決定物件集合中專案的顯示順序 `Page` `ToolbarItems` 。

`ToolbarItem`類別會從類別繼承下列常用的屬性 `MenuItem` ：

* [`Command`](xref:Xamarin.Forms.MenuItem.Command)是， `ICommand` 允許將使用者動作（例如滑鼠點按或按一下）系結至 viewmodel 上定義的命令。
* [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter)是 `object` ，它會指定應傳遞至的參數 `Command` 。
* [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource)這是一個 `ImageSource` 值，可決定物件上的顯示圖示 `ToolbarItem` 。
* [`Text`](xref:Xamarin.Forms.MenuItem.Text)是 `string` ，可決定物件上的顯示文字 `ToolbarItem` 。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，因此 `ToolbarItem` 實例可以是資料系結的目標。

> [!NOTE]
> 從物件建立工具列的替代方法 [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) 是將 [`NavigationPage.TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) 附加屬性設定為包含多個視圖的版面配置類別。 如需詳細資訊，請參閱在[導覽列中顯示 Views](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md#displaying-views-in-the-navigation-bar)。

## <a name="create-a-toolbaritem"></a>建立 ToolbarItem

`ToolbarItem`物件可以在 XAML 中具現化。 您 `Text` `IconImageSource` 可以設定和屬性，以決定如何在巡覽列中顯示按鈕。 下列範例示範如何 `ToolbarItem` 使用一些通用屬性集來具現化，並將其新增至 `ContentPage` 的 `ToolbarItems` 集合：

```xaml
<ContentPage.ToolbarItems>
    <ToolbarItem Text="Example Item"
                 IconImageSource="example_icon.png"
                 Order="Primary"
                 Priority="0" />
</ContentPage.ToolbarItems>
```

這個範例會產生一個 `ToolbarItem` 物件，其中包含文字、圖示，並且會先出現在主要導覽欄區域中。 `ToolbarItem`也可以在程式碼中建立並加入至 `ToolbarItems` 集合：

```csharp
ToolbarItem item = new ToolbarItem
{
    Text = "Example Item",
    IconImageSource = ImageSource.FromFile("example_icon.png"),
    Order = ToolbarItemOrder.Primary,
    Priority = 0
};

// "this" refers to a Page object
this.ToolbarItems.Add(item);
```

所代表的檔案 `string` （以屬性形式提供） `IconImageSource` 必須存在於每個平臺專案中。

> [!NOTE]
> 映射資產在每個平臺上的處理方式不同。 `ImageSource`可以來自來源，包括本機檔案或內嵌資源、URI 或資料流程。 如需 `IconImageSource` 在中設定屬性和影像的詳細資訊 Xamarin.Forms ，請參閱[中 Xamarin.Forms 的影像](~/xamarin-forms/user-interface/images.md)。

## <a name="define-button-behavior"></a>定義按鈕行為

`ToolbarItem`類別會 `Clicked` 從類別繼承事件 `MenuItem` 。 事件處理常式可以附加至 `Clicked` 事件，以回應點擊或按一下 `ToolbarItem` XAML 中的實例：

```xaml
<ToolbarItem ...
             Clicked="OnItemClicked" />
```

事件處理常式也可以附加在程式碼中：

```csharp
ToolbarItem item = new ToolbarItem { ... }
item.Clicked += OnItemClicked;
```

先前的範例參考了 `OnItemClicked` 事件處理常式。 下列程式碼顯示範例的執行：

```csharp
void OnItemClicked(object sender, EventArgs e)
{
    ToolbarItem item = (ToolbarItem)sender;
    messageLabel.Text = $"You clicked the \"{item.Text}\" toolbar item.";
}
```

`ToolbarItem`物件也可以使用 `Command` 和 `CommandParameter` 屬性來回應使用者輸入，而不需要事件處理常式。 如需介面和 MVVM 資料系結的詳細資訊 `ICommand` ，請參閱[ Xamarin.Forms MenuItem MVVM 行為](~/xamarin-forms/user-interface/menuitem.md#define-menuitem-behavior-with-mvvm)。

## <a name="enable-or-disable-a-toolbaritem-at-runtime"></a>在執行時間啟用或停用 ToolbarItem

若要啟用 `ToolbarItem` 在執行時間停用，請將其屬性系結 `Command` 至 `ICommand` 執行，並確定 `canExecute` 委派會適當地啟用和停用 `ICommand` 。

如需詳細資訊，請參閱[在執行時間啟用或停用 MenuItem](menuitem.md#enable-or-disable-a-menuitem-at-runtime)。

## <a name="primary-and-secondary-menus"></a>主要和次要功能表

`ToolbarItemOrder`列舉有 `Default` 、 `Primary` 和 `Secondary` 值。

當 `Order` 屬性設定為時 `Primary` ， `ToolbarItem` 物件就會出現在所有平臺的主要導覽列中。 `ToolbarItem`物件的優先順序高於頁面標題，將會被截斷以騰出空間給專案。 下列螢幕擷取畫面顯示 `ToolbarItem` iOS 和 Android 上主要功能表中的物件：

![「ToolbarItem 主要功能表螢幕擷取畫面 Android 和 iOS」](toolbaritem-images/toolbaritem-primary-menu.png "ToolbarItem Android 和 iOS 上的主要功能表螢幕擷取畫面")

當 `Order` 屬性設定為時 `Secondary` ，行為會因平臺而異。 在 UWP 和 Android 上，[ `Secondary` 專案] 功能表會顯示為三個點，可點擊或按一下以顯示垂直清單中的專案。 在 iOS 上，[ `Secondary` 專案] 功能表會出現在巡覽列下方做為水準清單。 下列螢幕擷取畫面顯示 iOS 和 Android 上的次要功能表：

![「ToolbarItem 次要功能表螢幕擷取畫面 Android 和 iOS」](toolbaritem-images/toolbaritem-secondary-menu.png "在 Android 和 iOS 上 ToolbarItem 次要功能表螢幕擷取畫面")

> [!WARNING]
> 將 `ToolbarItem` 屬性設定為的物件中的圖示行為 `Order` ，在 `Secondary` 不同平臺之間不一致。 避免在 `IconImageSource` 出現于次要功能表中的專案上設定屬性。

## <a name="related-links"></a>相關連結

* [ToolbarItem 示範](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-toolbaritem/)
* [中的影像Xamarin.Forms](~/xamarin-forms/user-interface/images.md)
* [Xamarin.FormsMenuItem](~/xamarin-forms/user-interface/menuitem.md)
