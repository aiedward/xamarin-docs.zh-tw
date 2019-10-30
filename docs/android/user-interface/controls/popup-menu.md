---
title: 快顯功能表
description: 如何新增錨定至特定視圖的快顯功能表。
ms.prod: xamarin
ms.assetid: 1C58E12B-4634-4691-BF59-D5A3F6B0E6F7
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/31/2018
ms.openlocfilehash: a5370cfb8a5c4950b361e5f58b253c63f4f1e240
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029169"
---
# <a name="xamarinandroid-popup-menu"></a>[Xamarin] 快顯功能表

[PopupMenu](xref:Android.Widget.PopupMenu) （也稱為_快捷方式功能表_）是錨定至特定視圖的功能表。 在下列範例中，單一活動包含一個按鈕。 當使用者按下按鈕時，會顯示三個專案的快顯功能表：

[具有按鈕和三個專案快顯功能表的應用程式![範例](popup-menu-images/01-app-example-sml.png)](popup-menu-images/01-app-example.png#lightbox)

## <a name="creating-a-popup-menu"></a>建立快顯功能表

第一個步驟是建立功能表的功能表資源檔，並將它放在 [**資源]/[功能表**] 中。 例如，下列 XML 是在上一個螢幕擷取畫面（ **Resources/menu/popup_menu**）中顯示的三個專案功能表的程式碼：

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@+id/item1"
          android:title="item 1" />
    <item android:id="@+id/item1"
          android:title="item 2" />
    <item android:id="@+id/item1"
          android:title="item 3" />
</menu>
```

接下來，建立 `PopupMenu` 的實例，並將其錨定到其 view。 當您建立 `PopupMenu`的實例時，您會將參考傳遞給其 `Context`，以及要附加功能表的視圖。 因此，快顯視窗會在其結構中錨定到此視圖。

在下列範例中，會在按鈕的 click 事件處理常式（名為 `showPopupMenu`）中建立 `PopupMenu`。 此按鈕也是 `PopupMenu` 錨定的視圖，如下列程式碼範例所示：

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
};
```

最後，快顯功能表必須使用稍早建立的功能表資源來*放大*。 在下列範例中，會加入對功能表的擴充[方法的](xref:Android.Views.LayoutInflater.Inflate*)呼叫，並呼叫它的[Show](xref:Android.Widget.PopupMenu.Show)方法來顯示它：

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
    menu.Inflate (Resource.Menu.popup_menu);
    menu.Show ();
};
```

## <a name="handling-menu-events"></a>處理功能表事件

當使用者選取功能表項目時，將會引發[MenuItemClick](xref:Android.Widget.PopupMenu.MenuItemClick) click 事件，並會關閉功能表。 點擊功能表外部的任何位置，只要將它關閉即可。 不論是哪一種情況，當功能表關閉時，將會引發其[DismissEvent](xref:Android.Widget.PopupMenu.Dismiss) 。 下列程式碼會新增 `MenuItemClick` 和 `DismissEvent` 事件的事件處理常式：

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
    menu.Inflate (Resource.Menu.popup_menu);

    menu.MenuItemClick += (s1, arg1) => {
        Console.WriteLine ("{0} selected", arg1.Item.TitleFormatted);
    };

    menu.DismissEvent += (s2, arg2) => {
        Console.WriteLine ("menu dismissed");
    };
    menu.Show ();
};
```

## <a name="related-links"></a>相關連結

- [PopupMenuDemo （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/popupmenudemo)
