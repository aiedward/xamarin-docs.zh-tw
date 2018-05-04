---
title: Android 的控制項 (Widget)
description: 用於建立 Xamarin.Android 使用者介面的建置組塊
ms.prod: xamarin
ms.assetid: B7A82166-B920-4672-B7A2-20DD5E0B5AEF
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 28418c3b3fedcd24963008eb3b59ffa782d791f1
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/03/2018
---
# <a name="android-controls-widgets"></a>Android 的控制項 (Widget)

Xamarin.Android 不僅會公開所有由 Android 提供原生使用者介面控制項 (widget)。 使用 Android 設計工具的 Xamarin.Android 應用程式或以程式設計方式透過 XML 配置檔案，就可以輕鬆地新增這些控制項。 不論您選擇的方法，Xamarin.Android 公開的所有使用者介面物件的屬性和 C# 中的方法。 下列各節介紹最常見的 Android 使用者介面控制項，並說明如何併入 Xamarin.Android 應用程式。

## <a name="action-barandroiduser-interfacecontrolsaction-barmd"></a>[動作列](~/android/user-interface/controls/action-bar.md) 

`ActionBar` 是工具列會顯示活動標題、 瀏覽介面與其他互動式項目。 一般而言，動作列會出現在活動視窗的頂端。

![範例項](images/action-bar.png)


## <a name="auto-completeandroiduser-interfacecontrolsauto-completemd"></a>[自動完成](~/android/user-interface/controls/auto-complete.md)

`AutoCompleteTextView` 會自動顯示完成的建議，而使用者輸入的可編輯的文字檢視項目。 建議清單會顯示在下拉式功能表，使用者可以從中選擇要取代的編輯方塊，與內容的項目。

![自動完成的範例](images/auto-complete.png)


## <a name="buttonsandroiduser-interfacecontrolsbuttonsindexmd"></a>[按鈕](~/android/user-interface/controls/buttons/index.md)

按鈕會在使用者點選執行動作的 UI 項目。

![範例按鈕](images/buttons.png)


## <a name="calendarandroiduser-interfacecontrolscalendarmd"></a>[Calendar](~/android/user-interface/controls/calendar.md)

`Calendar`類別用來將轉換中的特定執行個體的值，例如年、 月、 小時、 月、 日的日期和下一週的時間 （自 epoch 偏移毫秒值）。
`Calendar` 支援豐富的互動選項與行事曆資料，包括讀取和寫入事件、 出席者，以及提醒的能力。 使用行事曆提供者應用程式中，您將透過 API 的資料會出現在 Android 隨附內建的行事曆應用程式。

![範例行事曆](images/calendar.png)


## <a name="cardviewandroiduser-interfacecontrolscard-viewmd"></a>[CardView](~/android/user-interface/controls/card-view.md)

`CardView` 是類似卡片的檢視中顯示文字和映像內容的 UI 元件。 `CardView` 會實作為`FrameLayout`widget 具有圓的角和陰影。 一般而言，`CardView`用來顯示中的單一資料列項目`ListView`或`GridView`檢視群組。

![範例卡片檢視](images/cardview.png)


## <a name="edit-textandroiduser-interfacecontrolsedit-textmd"></a>[編輯文字](~/android/user-interface/controls/edit-text.md)

`EditText` 是用於輸入及修改文字的 UI 項目。

![範例編輯文字](images/edit-text.png)


## <a name="galleryandroiduser-interfacecontrolsgallerymd"></a>[圖庫](~/android/user-interface/controls/gallery.md)

`Gallery` 是一種配置 widget，用來水平捲動的清單，來顯示項目它會將目前的選取項目放在檢視的中心。

![範例組件庫](images/gallery.png)


## <a name="navigation-barandroiduser-interfacecontrolsnavigation-barmd"></a>[導覽列](~/android/user-interface/controls/navigation-bar.md)

*導覽列*提供不包含硬體按鈕的裝置上的導覽控制項**首頁**，**回**，和**功能表**。

![範例的導覽列](images/navigation-bar.png)


## <a name="pickersandroiduser-interfacecontrolspickersindexmd"></a>[選擇器](~/android/user-interface/controls/pickers/index.md)

*選擇器*UI 項目可讓使用者能夠使用 Android 所提供的對話方塊中選取日期或時間。

![範例選擇器](images/picker.png)


## <a name="popup-menuandroiduser-interfacecontrolspopup-menumd"></a>[快顯功能表](~/android/user-interface/controls/popup-menu.md)

`PopupMenu` 是用來顯示快顯功能表連接至特定的檢視。

![範例快顯功能表](images/popup-menu.png)


## <a name="spinnerandroiduser-interfacecontrolsspinnermd"></a>[微調按鈕](~/android/user-interface/controls/spinner.md)

`Spinner` 為提供快速的方法，從一組選取一個值的 UI 項目。 它是 simmilar 下拉式清單。 

![範例微調按鈕](images/spinner.png)


## <a name="switchandroiduser-interfacecontrolsswitchmd"></a>[開關](~/android/user-interface/controls/switch.md)

`Switch` 是可讓使用者切換兩種狀態，例如，在或關閉的 UI 項目。 `Switch`預設值是 OFF。

![範例參數](images/switch.png)


## <a name="textureviewandroiduser-interfacecontrolstexture-viewmd"></a>[TextureView](~/android/user-interface/controls/texture-view.md)

`TextureView` 是會使用硬體加速的 2D 轉譯，讓視訊或 OpenGL 內容資料流所要顯示的檢視。

![範例紋理檢視](images/texture-view.png)


## <a name="toolbarandroiduser-interfacecontrolstool-barindexmd"></a>[ToolBar](~/android/user-interface/controls/tool-bar/index.md)

`Toolbar` Widget （Android 5.0 棒棒糖符號中引進） 可以視為動作列介面的一般化&ndash;它用來取代動作列。 `Toolbar`可以用於應用程式版面配置的任何位置，而且它可更自訂比動作列。

![範例工具列](images/toolbar.png)


## <a name="viewpagerandroiduser-interfacecontrolsview-pagerindexmd"></a>[ViewPager](~/android/user-interface/controls/view-pager/index.md) 

`ViewPager`是可讓使用者翻轉 left 和 right 透過資料頁面的版面配置管理員。

![範例 ViewPager](images/viewpager.png)


## <a name="webviewandroiduser-interfacecontrolsweb-viewmd"></a>[WebView](~/android/user-interface/controls/web-view.md)

`WebView` 為 UI 項目，可讓您建立您自己的視窗，檢視網頁 （或甚至開發完整的瀏覽器）。

![範例 Web 檢視](images/web-view.png)

