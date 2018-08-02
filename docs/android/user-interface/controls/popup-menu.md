---
title: 快顯功能表
description: 如何新增至特定檢視的錨定的快顯功能表。
ms.prod: xamarin
ms.assetid: 1C58E12B-4634-4691-BF59-D5A3F6B0E6F7
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 07/31/2018
ms.openlocfilehash: d7cadde88e9ae7ee30815ee9323785038dbb1a39
ms.sourcegitcommit: ecdc031e9e26bbbf9572885531ee1f2e623203f5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2018
ms.locfileid: "39393655"
---
# <a name="popup-menu"></a>快顯功能表

[蹦現式功能表](https://developer.xamarin.com/api/type/Android.Widget.PopupMenu/)(也稱為_快顯功能表_) 錨定至特定檢視的功能表。 在下列範例中，單一活動會包含一個按鈕。 當使用者按下按鈕時，會顯示三個項目快顯功能表：

[![使用按鈕和三個項目快顯功能表的應用程式範例](popup-menu-images/01-app-example-sml.png)](popup-menu-images/01-app-example.png#lightbox)


## <a name="creating-a-popup-menu"></a>建立快顯功能表

第一個步驟是建立功能表的功能表資源檔，並將它放在**資源/功能表**。 比方說，下列 XML 程式碼是在先前的螢幕擷取畫面，顯示的三個項目功能表的程式碼**Resources/menu/popup_menu.xml**:

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

接下來，建立的執行個體`PopupMenu`和錨定至其檢視它。 當您建立的執行個體`PopupMenu`，您將傳遞其建構函式的參考`Context`以及功能表會附加到檢視。 如此一來，快顯功能表錨定到此檢視在其建構期間。

在下列範例中，`PopupMenu`會在按鈕的 click 事件處理常式 (其名稱為`showPopupMenu`)。 此按鈕還有要檢視`PopupMenu`錨定，如下列程式碼範例所示：

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
};
```

最後，快顯功能表必須*擴大*與稍早建立的功能表資源。 在下列範例中，呼叫的功能表[充氣](https://developer.xamarin.com/api/member/Android.Views.LayoutInflater.Inflate/p/System.Int32/Android.Views.ViewGroup/)方法會加入並將其[顯示](https://developer.xamarin.com/api/member/Android.Widget.PopupMenu.Show%28%29/)方法呼叫，以顯示它：

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
    menu.Inflate (Resource.Menu.popup_menu);
    menu.Show ();
};
```


## <a name="handling-menu-events"></a>處理功能表事件

當使用者選取功能表項目[MenuItemClick](https://developer.xamarin.com/api/event/Android.Widget.PopupMenu.MenuItemClick/)按一下就會引發事件，並將關閉功能表。 點選功能表外的任何地方只會關閉它。 在任一情況下，當功能表關閉時，其[DismissEvent](https://developer.xamarin.com/api/member/Android.Widget.PopupMenu.Dismiss%28%29/)就會引發。 下列程式碼加入事件處理常式兩者`MenuItemClick`和`DismissEvent`事件：

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

- [PopupMenuDemo （範例）](https://developer.xamarin.com/samples/monodroid/PopupMenuDemo/)
