---
title: Xamarin.Forms ToolbarItem
description: ToolbarItem 類別是一種特殊類型的按鈕，用於應用程式的巡覽列中。
ms.prod: xamarin
ms.assetId: CC737D54-0280-46BD-A2BC-A0FB67DDD6A1
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/29/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c6aabe5931dce51889432e7d5dce2be40631ffe2
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556499"
---
# <a name="no-locxamarinforms-toolbaritem"></a>Xamarin.Forms ToolbarItem

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-toolbaritem/)

Xamarin.Forms [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) 類別是一種特殊類型的按鈕，可新增至 `Page` 物件的 `ToolbarItems` 集合。 每個 `ToolbarItem` 物件會顯示為應用程式巡覽列中的按鈕。 `ToolbarItem`實例可以有圖示，並顯示為主要或次要功能表項目。 `ToolbarItem`類別繼承自 [`MenuItem`](xref:Xamarin.Forms.MenuItem) 。

下列螢幕擷取畫面顯示 `ToolbarItem` iOS 和 Android 上巡覽列中的物件：

![「Android 和 iOS 上的 ToolbarItem 示範螢幕擷取畫面」](toolbaritem-images/toolbaritem-device-screenshot.png "Android 和 iOS 上的 ToolbarItem 示範螢幕擷取畫面")

`ToolbarItem`類別會定義下列屬性：

* [`Order`](xref:Xamarin.Forms.ToolbarItem.Order) 這是一個 `ToolbarItemOrder` 列舉值，可判斷 `ToolbarItem` 實例是否會顯示在主要或次要功能表中。
* [`Priority`](xref:Xamarin.Forms.ToolbarItem.Priority) 這是一個 `integer` 值，可決定物件的集合中專案的顯示順序 `Page` `ToolbarItems` 。

`ToolbarItem`類別會從類別繼承下列一般使用的屬性 `MenuItem` ：

* [`Command`](xref:Xamarin.Forms.MenuItem.Command) 是允許系結 `ICommand` 使用者動作（例如，點按或按一下）至 viewmodel 上所定義之命令的。
* [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter) 是 `object` ，指定應傳遞至的參數 `Command` 。
* [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource) 是一個 `ImageSource` 值，可決定物件上的顯示圖示 `ToolbarItem` 。
* [`Text`](xref:Xamarin.Forms.MenuItem.Text) 是 `string` ，可決定物件上的顯示文字 `ToolbarItem` 。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，因此 `ToolbarItem` 實例可以是資料系結的目標。

> [!NOTE]
> 從物件建立工具列的另一個替代方式 [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) 是將 [`NavigationPage.TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) 附加屬性設定為包含多個視圖的版面配置類別。 如需詳細資訊，請參閱 [在導覽列中顯示 Views](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md#displaying-views-in-the-navigation-bar)。

## <a name="create-a-toolbaritem"></a>建立 ToolbarItem

`ToolbarItem`物件可以在 XAML 中具現化。 您 `Text` `IconImageSource` 可以設定和屬性，以決定如何在導覽列中顯示按鈕。 下列範例示範如何 `ToolbarItem` 使用一些已設定的通用屬性來具現化，並將它新增至 `ContentPage` 的 `ToolbarItems` 集合：

```xaml
<ContentPage.ToolbarItems>
    <ToolbarItem Text="Example Item"
                 IconImageSource="example_icon.png"
                 Order="Primary"
                 Priority="0" />
</ContentPage.ToolbarItems>
```

此範例會產生 `ToolbarItem` 具有文字的物件（圖示），並在主要巡覽列區域中先出現。 您 `ToolbarItem` 也可以在程式碼中建立，並將其新增至 `ToolbarItems` 集合：

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

以屬性工作表示的檔案 `string` `IconImageSource` 必須存在於每個平臺專案中。

> [!NOTE]
> 映射資產在每個平臺上以不同的方式處理。 `ImageSource`可以來自來源，包括本機檔案或內嵌資源、URI 或串流。 如需有關 `IconImageSource` 在中設定屬性和影像的詳細資訊 Xamarin.Forms ，請參閱[中 Xamarin.Forms 的影像](~/xamarin-forms/user-interface/images.md)。

## <a name="define-button-behavior"></a>定義按鈕行為

`ToolbarItem`類別會繼承 `Clicked` 類別的事件 `MenuItem` 。 事件處理常式可以附加至 `Clicked` 事件，以回應在 XAML 中點按或按一下 `ToolbarItem` 實例：

```xaml
<ToolbarItem ...
             Clicked="OnItemClicked" />
```

事件處理常式也可以在程式碼中附加：

```csharp
ToolbarItem item = new ToolbarItem { ... }
item.Clicked += OnItemClicked;
```

先前的範例參考了 `OnItemClicked` 事件處理常式。 下列程式碼顯示範例執行：

```csharp
void OnItemClicked(object sender, EventArgs e)
{
    ToolbarItem item = (ToolbarItem)sender;
    messageLabel.Text = $"You clicked the \"{item.Text}\" toolbar item.";
}
```

`ToolbarItem` 物件也可以使用 `Command` 和 `CommandParameter` 屬性來回應使用者輸入，而不需要事件處理常式。 如需介面和 MVVM 資料系結的詳細資訊 `ICommand` ，請參閱[ Xamarin.Forms MenuItem MVVM 行為](~/xamarin-forms/user-interface/menuitem.md#define-menuitem-behavior-with-mvvm)。

## <a name="enable-or-disable-a-toolbaritem-at-runtime"></a>在執行時間啟用或停用 ToolbarItem

若要 `ToolbarItem` 在執行時間啟用停用，請將其屬性系結 `Command` 至 `ICommand` 實作為，並確定 `canExecute` 委派會適當地啟用和停用 `ICommand` 。

如需詳細資訊，請參閱 [在執行時間啟用或停用 MenuItem](menuitem.md#enable-or-disable-a-menuitem-at-runtime)。

## <a name="primary-and-secondary-menus"></a>主要和次要功能表

`ToolbarItemOrder`列舉具有 `Default` 、 `Primary` 和 `Secondary` 值。

當 `Order` 屬性設定為時 `Primary` ， `ToolbarItem` 物件會出現在所有平臺上的主要導覽列中。 `ToolbarItem` 物件會優先于頁面標題，這會被截斷以騰出空間給專案。 下列螢幕擷取畫面顯示 `ToolbarItem` iOS 和 Android 上主要功能表中的物件：

![「ToolbarItem 主要功能表螢幕擷取畫面： Android 和 iOS」](toolbaritem-images/toolbaritem-primary-menu.png "Android 和 iOS 上的 ToolbarItem 主要功能表螢幕擷取畫面")

當 `Order` 屬性設定為時 `Secondary` ，行為會因平臺而異。 在 UWP 和 Android 上，[ `Secondary` 專案] 功能表會顯示為三個點，可供您點擊或按一下以顯示垂直清單中的專案。 在 iOS 上，[ `Secondary` 專案] 功能表以水準清單的形式出現在巡覽列下方。 下列螢幕擷取畫面顯示 iOS 和 Android 上的次要功能表：

![「ToolbarItem 次要功能表螢幕擷取畫面： Android 和 iOS」](toolbaritem-images/toolbaritem-secondary-menu.png "Android 和 iOS 上的 ToolbarItem 次要功能表螢幕擷取畫面")

> [!WARNING]
> `ToolbarItem`將屬性設為的物件中的圖示行為 `Order` `Secondary` ，在各平臺之間不一致。 避免在 `IconImageSource` 出現在次要功能表中的專案上設定屬性。

## <a name="related-links"></a>相關連結

* [ToolbarItem 示範](/samples/xamarin/xamarin-forms-samples/userinterface-toolbaritem/)
* [中的影像 Xamarin.Forms](~/xamarin-forms/user-interface/images.md)
* [Xamarin.Forms MenuItem](~/xamarin-forms/user-interface/menuitem.md)