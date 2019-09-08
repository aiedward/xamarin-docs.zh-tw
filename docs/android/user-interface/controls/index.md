---
title: Android 控制項（widget）
description: 建立 Xamarin Android 使用者介面的建立區塊
ms.prod: xamarin
ms.assetid: B7A82166-B920-4672-B7A2-20DD5E0B5AEF
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/29/2018
ms.openlocfilehash: e3f6524f03612ee39c537f482b1db916ecf08a23
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70759090"
---
# <a name="xamarinandroid-controls-widgets"></a>Xamarin Android 控制項（widget）

Xamarin 會公開 Android 所提供的所有原生使用者介面控制項（widget）。 這些控制項可以使用 Android Designer 輕鬆地新增至 Xamarin Android 應用程式，或透過 XML 配置檔案以程式設計方式加入。 無論您選擇哪一種方法，Xamarin 都會公開中C#的所有使用者介面物件屬性和方法。 下列各節將介紹最常見的 Android 使用者介面控制項，並說明如何將它們併入 Xamarin Android 應用程式中。

## <a name="action-barandroiduser-interfacecontrolsaction-barmd"></a>[動作列](~/android/user-interface/controls/action-bar.md) 

`ActionBar`是顯示活動標題、導覽介面和其他互動式專案的工具列。 通常，動作列會出現在使用中視窗的頂端。

![範例 ActionBar](images/action-bar.png)

## <a name="auto-completeandroiduser-interfacecontrolsauto-completemd"></a>[自動完成](~/android/user-interface/controls/auto-complete.md)

`AutoCompleteTextView`是可編輯的文字 view 元素，會在使用者輸入時自動顯示完成建議。 建議清單會顯示在下拉式功能表中，使用者可以從中選擇專案，以取代編輯方塊的內容。

![自動完成的範例](images/auto-complete.png)

## <a name="buttonsandroiduser-interfacecontrolsbuttonsindexmd"></a>[按鈕](~/android/user-interface/controls/buttons/index.md)

按鈕是使用者用來執行動作的 UI 元素。

![範例按鈕](images/buttons.png)

## <a name="calendarandroiduser-interfacecontrolscalendarmd"></a>[Calendar](~/android/user-interface/controls/calendar.md)

`Calendar`類別是用來將時間內的特定實例（從 epoch 位移的毫秒值）轉換成值，例如年、月、小時、每月日期，以及下一周的日期。
`Calendar`支援許多與行事歷數據互動的選項，包括讀取和寫入事件、出席者和提醒的能力。 藉由在您的應用程式中使用行事曆提供者，您透過 API 新增的資料會出現在 Android 隨附的內建行事曆應用程式中。

![範例行事曆](images/calendar.png)

## <a name="cardviewandroiduser-interfacecontrolscard-viewmd"></a>[CardView](~/android/user-interface/controls/card-view.md)

`CardView`是 UI 元件，會在類似卡片的視圖中顯示文字和影像內容。 `CardView`會實作為`FrameLayout`具有圓角和陰影的 widget。 通常會使用來呈現`ListView`或`GridView` view 群組中的單一資料列專案。 `CardView`

![範例卡片視圖](images/cardview.png)

## <a name="edit-textandroiduser-interfacecontrolsedit-textmd"></a>[編輯文字](~/android/user-interface/controls/edit-text.md)

`EditText`是用來輸入和修改文字的 UI 元素。

![範例編輯文字](images/edit-text.png)

## <a name="galleryandroiduser-interfacecontrolsgallerymd"></a>[圖庫](~/android/user-interface/controls/gallery.md)

`Gallery`是用來在水準滾動清單中顯示專案的版面配置 widget;它會將目前的選取範圍置於視圖的中央。

![範例庫](images/gallery.png)

## <a name="navigation-barandroiduser-interfacecontrolsnavigation-barmd"></a>[導覽列](~/android/user-interface/controls/navigation-bar.md)

*導覽*列會在裝置上提供導覽控制項，其中不包含 [**首頁**]、[**上一頁**] 和 [**功能表**] 的硬體按鈕。

![範例導覽列](images/navigation-bar.png)

## <a name="pickersandroiduser-interfacecontrolspickersindexmd"></a>[選擇器](~/android/user-interface/controls/pickers/index.md)

「選擇器 *」是 UI 元素，可讓*使用者使用 Android 所提供的對話方塊來挑選日期或時間。

![範例選擇器](images/picker.png)

## <a name="popup-menuandroiduser-interfacecontrolspopup-menumd"></a>[快顯功能表](~/android/user-interface/controls/popup-menu.md)

`PopupMenu`用於顯示附加至特定視圖的快顯功能表。

![範例快顯功能表](images/popup-menu.png)

## <a name="ratingbarandroiduser-interfacecontrolsratingbarmd"></a>[RatingBar](~/android/user-interface/controls/ratingbar.md)

`RatingBar`是 UI 元素，會顯示星星的評等。

![RatingBar 的範例](ratingbar-images/01-ratingbar.png)

## <a name="spinnerandroiduser-interfacecontrolsspinnermd"></a>[微調按鈕](~/android/user-interface/controls/spinner.md)

`Spinner`是一個 UI 元素，可讓您快速地從集合中選取一個值。 它會 simmilar 到下拉式清單中。 

![範例微調](images/spinner.png)

## <a name="switchandroiduser-interfacecontrolsswitchmd"></a>[開關](~/android/user-interface/controls/switch.md)

`Switch`是一個 UI 元素，可讓使用者在兩個狀態之間切換，例如開啟或關閉。 `Switch`預設值為 OFF。

![範例切換](images/switch.png)

## <a name="textureviewandroiduser-interfacecontrolstexture-viewmd"></a>[TextureView](~/android/user-interface/controls/texture-view.md)

`TextureView`是使用硬體加速2D 轉譯的視圖，可顯示影片或 OpenGL 內容串流。

![範例材質視圖](images/texture-view.png)

## <a name="toolbarandroiduser-interfacecontrolstool-barindexmd"></a>[ToolBar](~/android/user-interface/controls/tool-bar/index.md)

Widget （在 Android 5.0 棒糖中引進）可視為動作列介面&ndash;的一般化，其目的是要取代動作列。 `Toolbar` 可以`Toolbar`在應用程式版面配置中的任何位置使用，而且比動作列更容易自訂。

![範例工具列](images/toolbar.png)

## <a name="viewpagerandroiduser-interfacecontrolsview-pagerindexmd"></a>[ViewPager](~/android/user-interface/controls/view-pager/index.md) 

`ViewPager`是一種版面建構管理員，可讓使用者在資料頁面中向左或向右翻轉。

![範例 ViewPager](images/viewpager.png)

## <a name="webviewandroiduser-interfacecontrolsweb-viewmd"></a>[WebView](~/android/user-interface/controls/web-view.md)

`WebView`是一個 UI 元素，可讓您建立自己的視窗來查看網頁（或甚至開發完整的瀏覽器）。

![範例 Web 視圖](images/web-view.png)
