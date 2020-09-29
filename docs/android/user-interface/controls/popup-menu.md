---
title: 快顯功能表
description: 如何加入錨定至特定視圖的快顯功能表。
ms.prod: xamarin
ms.assetid: 1C58E12B-4634-4691-BF59-D5A3F6B0E6F7
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/31/2018
ms.openlocfilehash: 5d445f84b7634895c59120e905daaf6fee403ac9
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453596"
---
# <a name="xamarinandroid-popup-menu"></a>Xamarin. Android 快顯功能表

[PopupMenu](xref:Android.Widget.PopupMenu) (也稱為_快捷方式功能表_) 是錨定至特定視圖的功能表。 在下列範例中，單一活動包含按鈕。 當使用者按下按鈕時，會顯示三個專案的快顯功能表：

[![具有按鈕和三個專案快顯功能表的應用程式範例](popup-menu-images/01-app-example-sml.png)](popup-menu-images/01-app-example.png#lightbox)

## <a name="creating-a-popup-menu"></a>建立快顯功能表

第一個步驟是建立功能表的功能表資源檔，並將它放在 [ **資源]/[功能表**] 中。 例如，下列 XML 是在上一個螢幕擷取畫面、 **資源/功能表/popup_menu.xml**中顯示之三個專案功能表的程式碼：

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

接著，建立的實例 `PopupMenu` ，並將它錨定至其視圖。 當您建立的實例時 `PopupMenu` ，會將的參考傳遞給它的參考，以及要 `Context` 附加功能表的視圖。 如此一來，快顯視窗就會在其結構內錨定到此視圖。

在下列範例中， `PopupMenu` 會在名為)  (按鈕的 click 事件處理常式中建立 `showPopupMenu` 。 此按鈕也是錨定的目標視圖 `PopupMenu` ，如下列程式碼範例所示：

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
};
```

最後，您必須使用稍早建立的功能表資源來 *放大* 快顯功能表。 在下列範例中，會新增對功能表 [擴充方法的](xref:Android.Views.LayoutInflater.Inflate*) 呼叫，並呼叫其 [Show](xref:Android.Widget.PopupMenu.Show) 方法來顯示它：

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
    menu.Inflate (Resource.Menu.popup_menu);
    menu.Show ();
};
```

## <a name="handling-menu-events"></a>處理功能表事件

當使用者選取功能表項目時，將會引發 [MenuItemClick](xref:Android.Widget.PopupMenu.MenuItemClick) click 事件，並將關閉功能表。 點擊功能表外的任何位置，就會關閉它。 在任一種情況下，當功能表關閉時，就會引發其 [DismissEvent](xref:Android.Widget.PopupMenu.Dismiss) 。 下列程式碼會加入和事件的事件處理常式 `MenuItemClick` `DismissEvent` ：

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

- [PopupMenuDemo (範例) ](/samples/xamarin/monodroid-samples/popupmenudemo)