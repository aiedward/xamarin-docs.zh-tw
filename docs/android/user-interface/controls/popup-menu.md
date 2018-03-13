---
title: "快顯功能表"
ms.topic: article
ms.prod: xamarin
ms.assetid: 1C58E12B-4634-4691-BF59-D5A3F6B0E6F7
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 08/18/2017
ms.openlocfilehash: f976d798ae1b1279fc8f82d3cf1d738bb2c93911
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="popup-menu"></a>快顯功能表

`PopupMenu`類別加入顯示快顯功能表連接至特定檢視的支援。 下圖顯示與第二個項目反白顯示就如同它選取顯示按鈕，從快顯功能表：

 [![三個項目具有三個 PopopMenu 的範例](popup-menu-images/20-popupmenu.png)](popup-menu-images/20-popupmenu.png#lightbox)

Android 4 中加入幾個新功能，可`PopupMenu`，讓比較容易使用，也就是：

-   **充氣**&ndash;擴大方法上現已提供直接 PopupMenu 類別。
-   **DismissEvent** &ndash; PopupMenu 類別現在具有 DismissEvent。

讓我們看看這些增強功能。 在此範例中，有包含按鈕的活動。 當使用者按一下按鈕時，快顯功能表會顯示如下所示：

 [![按鈕和 3 個項目的快顯功能表的模擬器中執行應用程式的範例](popup-menu-images/06-popupmenu.png)](popup-menu-images/06-popupmenu.png#lightbox)


## <a name="creating-a-popup-menu"></a>建立快顯功能表

當我們建立的執行個體`PopupMenu`，我們需要將其建構函式的參考`Context`，以及功能表附加至檢視。 在此案例中，我們建立`PopupMenu`在我們的按鈕 click 事件處理常式，它會命名為`showPopupMenu`。
此按鈕，也是我們會將附加的檢視`PopupMenu`，如下列程式碼所示：

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
}
```

在 Android 3 中，以擴大 XML 資源功能表的程式碼需要您先取得的參考`MenuInflator`，然後呼叫其`Inflate`XML 包含功能表和功能表的執行個體，以擴大到資源識別碼的方法。 這種方法仍適用於 Android 4 及更新版本的程式碼所示：

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
    menu.MenuInflater.Inflate (Resource.Menu.popup_menu, menu.Menu);
};
```

Android 4 為準，不過，您現在可以呼叫`Inflate`的執行個體上直接`PopupMenu`。 這會讓程式碼更精確，如下所示：

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
    menu.Inflate (Resource.Menu.popup_menu);
    menu.Show ();
};
```

上述程式碼，因而誇大功能表之後接著呼叫`menu.Show`顯示在螢幕上。


## <a name="handling-menu-events"></a>處理功能表事件

當使用者選取功能表項目，`MenuItemClick`會引發事件，並將關閉功能表。 點選功能表外的任何位置只會關閉它。 在任一情況下，Android 4，當功能表關閉時，根據其`DismissEvent`就會引發。 下列程式碼將兩個事件處理常式`MenuItemClick`和`DismissEvent`事件：

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
- [介紹的冰淇淋三明治](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 平台](http://developer.android.com/sdk/android-4.0.html)
