---
title: Xamarin. Forms ToolbarItem
description: ToolbarItem 類別是在應用程式的導覽列中使用的特殊按鈕類型。
ms.prod: xamarin
ms.assetId: CC737D54-0280-46BD-A2BC-A0FB67DDD6A1
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/29/2019
ms.openlocfilehash: 9bb041206dbafb68c1bb60bbbb3068f0a2001b55
ms.sourcegitcommit: 9178e2e689f027212ea3e623b556b312985d79fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2019
ms.locfileid: "69658051"
---
# <a name="xamarinforms-toolbaritem"></a>Xamarin. Forms ToolbarItem

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/en-us/samples/xamarin/xamarin-forms-samples/userinterface-toolbaritem/)

[Xamarin [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) ] 類別是一種特殊類型的按鈕, 可以加入`Page`物件的`ToolbarItems`集合中。 每`ToolbarItem`個物件會在應用程式的導覽列中顯示為按鈕。 `ToolbarItem`實例可以有圖示, 並顯示為主要或次要功能表項目。 類別繼承自[`MenuItem`。](xref:Xamarin.Forms.MenuItem) `ToolbarItem`

下列螢幕擷取畫面顯示`ToolbarItem` iOS 和 Android 上巡覽列中的物件:

「 ![Android 和 iOS 上的 ToolbarItem 示範螢幕擷取畫面]」(toolbaritem-images/toolbaritem-device-screenshot.png "Android 和 iOS 上的 ToolbarItem 示範螢幕擷取畫面")

`ToolbarItem`類別會定義下列屬性:

* [`Order`](xref:Xamarin.Forms.ToolbarItem.Order)這是`ToolbarItemOrder`一個列舉值, 可判斷`ToolbarItem`實例是否會顯示在主要或次要功能表中。
* [`Priority`](xref:Xamarin.Forms.ToolbarItem.Priority)這是`integer`一個值, 可決定`Page`物件`ToolbarItems`集合中專案的顯示順序。

`ToolbarItem`類別會`MenuItem`從類別繼承下列常用的屬性:

* [`Command`](xref:Xamarin.Forms.MenuItem.Command)`ICommand`是, 允許將使用者動作 (例如滑鼠點按或按一下) 系結至 viewmodel 上定義的命令。
* [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter)是, 它會指定應傳遞至的`Command`參數。 `object`
* [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource)這是`ImageSource`一個值, 可決定`ToolbarItem`物件上的顯示圖示。
* [`Text`](xref:Xamarin.Forms.MenuItem.Text)是, 可決定`ToolbarItem`物件上的顯示文字。 `string`

這些屬性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件所支援`ToolbarItem` , 因此實例可以是資料系結的目標。

> [!NOTE]
> 從[`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)物件建立工具列的替代方法是[`NavigationPage.TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty)將附加屬性設定為包含多個視圖的版面配置類別。 如需詳細資訊, 請參閱在[導覽列中顯示 Views](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md#displaying-views-in-the-navigation-bar)。

## <a name="create-a-toolbaritem"></a>建立 ToolbarItem

`ToolbarItem`物件可以在 XAML 中具現化。 您可以`IconImageSource`設定和屬性,以決定如何在巡覽列中顯示按鈕。`Text` 下列範例示範如何`ToolbarItem`具現化, 並設定一些通用屬性:

```xaml
<ToolbarItem Text="Example Item"
             IconImageSource="example_icon.png"
             Order="Primary"
             Priority="0" />
```

這個範例會產生一個`ToolbarItem`物件, 其中包含文字、圖示, 並且會先出現在主要導覽欄區域中。 也可以在程式碼中建立並加入`ToolbarItems`至集合: `ToolbarItem`

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

所代表`string`的檔案 ( `IconImageSource`以屬性形式提供) 必須存在於每個平臺專案中。

> [!NOTE]
> 映射資產在每個平臺上的處理方式不同。 `ImageSource`可以來自來源, 包括本機檔案或內嵌資源、URI 或資料流程。 如需在 [表單`IconImageSource` ] 中設定屬性和影像的詳細資訊, 請參閱[xamarin 中的影像](~/xamarin-forms/user-interface/images.md)。

## <a name="define-button-behavior"></a>定義按鈕行為

類別會`Clicked` 從`MenuItem`類別繼承事件。 `ToolbarItem` 事件處理常式可以附加至`Clicked`事件, 以回應點擊或按一下 XAML 中的`ToolbarItem`實例:

```xaml
<ToolbarItem ...
             Clicked="OnItemClicked" />
```

事件處理常式也可以附加在程式碼中:

```csharp
ToolbarItem item = new ToolbarItem { ... }
item.Clicked += OnItemClicked;
```

先前的範例參考`OnItemClicked`了事件處理常式。 下列程式碼顯示範例的執行:

```csharp
void OnItemClicked(object sender, EventArgs e)
{
    ToolbarItem item = (ToolbarItem)sender;
    messageLabel.Text = $"You clicked the \"{item.Text}\" toolbar item.";
}
```

`ToolbarItem`物件也可以使用`Command`和`CommandParameter`屬性來回應使用者輸入, 而不需要事件處理常式。 如需`ICommand`介面和 MVVM 資料系結的詳細資訊, 請參閱[Xamarin。表單 MenuItem MVVM 行為](~/xamarin-forms/user-interface/menuitem.md#define-menuitem-behavior-with-mvvm)。

## <a name="primary-and-secondary-menus"></a>主要和次要功能表

列舉有`Default` 、`Primary`和值`Secondary` 。 `ToolbarItemOrder`

當屬性設定為`Primary`時, `ToolbarItem`物件就會出現在所有平臺的主要導覽列中。 `Order` `ToolbarItem`物件的優先順序高於頁面標題, 將會被截斷以騰出空間給專案。 下列螢幕擷取畫面顯示`ToolbarItem` iOS 和 Android 上主要功能表中的物件:

「 ![ToolbarItem 主要功能表螢幕擷取畫面 Android 和 iOS] 」(toolbaritem-images/toolbaritem-primary-menu.png "ToolbarItem Android 和 iOS 上的主要功能表螢幕擷取畫面")

當屬性設定為`Secondary`時, 行為會因平臺而異。 `Order` 在 UWP 和 Android 上, `Secondary` [專案] 功能表會顯示為三個點, 可點擊或按一下以顯示垂直清單中的專案。 在 iOS 上, `Secondary` [專案] 功能表會出現在巡覽列下方做為水準清單。 下列螢幕擷取畫面顯示 iOS 和 Android 上的次要功能表:

「 ![ToolbarItem 次要功能表螢幕擷取畫面 Android 和 iOS] 」(toolbaritem-images/toolbaritem-secondary-menu.png "在 Android 和 iOS 上 ToolbarItem 次要功能表螢幕擷取畫面")

> [!WARNING]
> 將`Order`屬性設定`ToolbarItem`為`Secondary`的物件中的圖示行為, 在不同平臺之間不一致。 避免在出現`IconImageSource`于次要功能表中的專案上設定屬性。

## <a name="related-links"></a>相關連結

* [ToolbarItem 示範](https://docs.microsoft.com/en-us/samples/xamarin/xamarin-forms-samples/userinterface-toolbaritem/)
* [Xamarin 中的影像](~/xamarin-forms/user-interface/images.md)
* [Xamarin. 表單 MenuItem](~/xamarin-forms/user-interface/menuitem.md)