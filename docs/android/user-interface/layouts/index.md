---
title: 版面配置
description: 定義您的 Xamarin Android 應用程式的視覺化結構
ms.prod: xamarin
ms.assetid: 2BA72B0E-230D-4F98-B4D5-4EFB0D479789
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/18/2017
ms.openlocfilehash: 79183963e59a7ca167ffbc3c0cc4b3628595389e
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69522657"
---
# <a name="xamarinandroid-layouts"></a>Xamarin. Android 版面配置

版面配置是用來排列組成螢幕 UI 介面的元素 (例如活動)。 下列各節說明如何在 Xamarin Android 應用程式中使用最常使用的版面配置。

- [LinearLayout](~/android/user-interface/layouts/linear-layout.md)是一個視圖群組, 會以垂直或水準方式, 以線性方向顯示子視圖元素。

    ![線性版面配置範例](images/linear-layout.png)

- [RelativeLayout](~/android/user-interface/layouts/relative-layout.md)是 view 群組, 會在相對位置顯示子視圖專案。 View 的位置可以指定為相對於兄弟元素。

    ![範例相對版面配置](images/relative-layout.png)

- [TableLayout](~/android/user-interface/layouts/table-layout.md)是一個視圖群組, 會在資料列和資料行中顯示子視圖元素。

    ![範例資料表版面配置](images/table-layout.png)

- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)是一個 UI 元素, 會顯示清單或方格中的專案集合, 讓使用者可以在集合中進行滾動。

    ![範例 Recycler 視圖](images/recycler-view.png)

- [ListView](~/android/user-interface/layouts/list-view/index.md)是建立可滾動專案清單的視圖群組。 清單專案會使用清單介面卡自動插入清單中。 `ListView`是 Android 應用程式的重要 UI 元件, 因為它是從功能表選項的簡短清單中的任何位置使用, 到長清單的連絡人或網際網路我的最愛。 它提供了一種簡單的方式來呈現資料列的滾動清單, 您可以使用內建樣式或廣泛自訂來進行格式化。 ListView 實例需要介面卡以資料列視圖中包含的資料摘要。

    ![範例清單視圖](images/list-view.png)

- [GridView](~/android/user-interface/layouts/grid-view.md)是一個 UI 元素, 會在二維方格中顯示可以滾動的專案。

    ![方格視圖範例](images/grid-view.png)

- [GridLayout](~/android/user-interface/layouts/grid-layout.md)是一種視圖群組, 支援在2d 方格中配置視圖, 類似于 HTML 表格。

    ![範例方格版面配置](images/grid-layout.png)

- 索引標籤[式版面](~/android/user-interface/layouts/tab-layout/index.md)配置是行動應用程式中的熱門使用者介面模式, 因為其簡易性和可用性。 它們提供一致且簡單的方法, 在應用程式中的各種畫面之間流覽。

    ![範例索引標籤式版面配置](images/tabbed-layout.png)
 
