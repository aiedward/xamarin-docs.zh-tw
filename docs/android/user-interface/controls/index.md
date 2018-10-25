---
title: Android 的控制項 (Widget)
description: 建立 Xamarin.Android 使用者介面的建置組塊
ms.prod: xamarin
ms.assetid: B7A82166-B920-4672-B7A2-20DD5E0B5AEF
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/29/2018
ms.openlocfilehash: 842fb1df2c9cc1aaf1a106687179a3730c2503bd
ms.sourcegitcommit: 7f6127c2f425fadc675b77d14de7a36103cff675
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2018
ms.locfileid: "32436709"
---
# <a name="android-controls-widgets"></a>Android 的控制項 (Widget)

Xamarin.Android 公開 （expose) 所有 Android 所提供之原生使用者介面控制項 (widget)。 使用 Android Designer 的 Xamarin.Android 應用程式或以程式設計方式透過 XML 版面配置檔，就可以輕鬆加入這些控制項。 不論您選擇哪一個方法，Xamarin.Android 會公開所有的使用者介面物件屬性和 C# 中的方法。 下列各節會介紹最常見的 Android 使用者介面控制項，並說明如何將它們合併到 Xamarin.Android 應用程式。

## <a name="action-barandroiduser-interfacecontrolsaction-barmd"></a>[動作列](~/android/user-interface/controls/action-bar.md) 

`ActionBar` 是一個工具列，會顯示活動標題、 瀏覽介面，以及其他互動式項目。 一般而言，動作列中會出現在活動視窗的頂端。

![範例 ActionBar](images/action-bar.png)


## <a name="auto-completeandroiduser-interfacecontrolsauto-completemd"></a>[自動完成](~/android/user-interface/controls/auto-complete.md)

`AutoCompleteTextView` 會自動顯示完成建議，而使用者輸入的可編輯的文字檢視項目。 建議清單會顯示在下拉式功能表，使用者可以從中選擇一個項目來取代使用 [編輯] 方塊的內容。

![自動完成的範例](images/auto-complete.png)


## <a name="buttonsandroiduser-interfacecontrolsbuttonsindexmd"></a>[按鈕](~/android/user-interface/controls/buttons/index.md)

按鈕是在使用者點選執行動作的 UI 項目。

![範例按鈕](images/buttons.png)


## <a name="calendarandroiduser-interfacecontrolscalendarmd"></a>[Calendar](~/android/user-interface/controls/calendar.md)

`Calendar`類別用來將轉換中的特定執行個體的值，例如年、 月、 小時、 天的月份，以及下一週的日期時間 （epoch 位移毫秒值）。
`Calendar` 支援豐富的行事曆資料，包括能夠讀取和寫入事件、 出席者，以及提醒互動選項。 在您的應用程式中使用的行事曆提供者，您將透過 API 的資料會出現在 Android 隨附內建的行事曆應用程式。

![範例行事曆](images/calendar.png)


## <a name="cardviewandroiduser-interfacecontrolscard-viewmd"></a>[CardView](~/android/user-interface/controls/card-view.md)

`CardView` 是類似於卡片的檢視中顯示的文字和影像內容的 UI 元件。 `CardView` 會實作為`FrameLayout`小工具具有圓的角和陰影。 通常`CardView`用來顯示中的單一資料列項目`ListView`或`GridView`檢視群組。

![範例 [卡片] 檢視](images/cardview.png)


## <a name="edit-textandroiduser-interfacecontrolsedit-textmd"></a>[編輯文字](~/android/user-interface/controls/edit-text.md)

`EditText` 是用於輸入，並修改文字的 UI 項目。

![範例編輯文字](images/edit-text.png)


## <a name="galleryandroiduser-interfacecontrolsgallerymd"></a>[圖庫](~/android/user-interface/controls/gallery.md)

`Gallery` 是一種版面配置 widget，用來水平捲動清單，以顯示項目它會將目前的選取範圍 檢視的中心。

![範例資源庫](images/gallery.png)


## <a name="navigation-barandroiduser-interfacecontrolsnavigation-barmd"></a>[導覽列](~/android/user-interface/controls/navigation-bar.md)

*導覽列*提供不包含硬體按鈕的裝置上的導覽控制項**首頁**，**回**，以及**功能表**。

![範例導覽列](images/navigation-bar.png)


## <a name="pickersandroiduser-interfacecontrolspickersindexmd"></a>[選擇器](~/android/user-interface/controls/pickers/index.md)

*選擇器*是 UI 項目可讓使用者能夠使用 Android 所提供的對話方塊中選取日期或時間。

![範例選擇器](images/picker.png)


## <a name="popup-menuandroiduser-interfacecontrolspopup-menumd"></a>[快顯功能表](~/android/user-interface/controls/popup-menu.md)

`PopupMenu` 是用來顯示快顯功能表連接至特定的檢視。

![範例快顯功能表](images/popup-menu.png)


## <a name="ratingbarandroiduser-interfacecontrolsratingbarmd"></a>[RatingBar](~/android/user-interface/controls/ratingbar.md)

A`RatingBar`是顯示在顆星的評等的 UI 項目。

![RatingBar 的範例](ratingbar-images/01-ratingbar.png)


## <a name="spinnerandroiduser-interfacecontrolsspinnermd"></a>[微調按鈕](~/android/user-interface/controls/spinner.md)

`Spinner` 是提供快速的方式，從一組選取一個值的 UI 項目。 它是 simmilar 下拉式清單。 

![範例微調按鈕](images/spinner.png)


## <a name="switchandroiduser-interfacecontrolsswitchmd"></a>[開關](~/android/user-interface/controls/switch.md)

`Switch` 是可讓兩個狀態，例如，在之間切換，或關閉使用者的 UI 項目。 `Switch`預設值是 OFF。

![範例參數](images/switch.png)


## <a name="textureviewandroiduser-interfacecontrolstexture-viewmd"></a>[TextureView](~/android/user-interface/controls/texture-view.md)

`TextureView` 是會使用硬體加速的 2D 轉譯，以便的影片或 OpenGL 要顯示的內容資料流的檢視。

![範例紋理檢視](images/texture-view.png)


## <a name="toolbarandroiduser-interfacecontrolstool-barindexmd"></a>[ToolBar](~/android/user-interface/controls/tool-bar/index.md)

`Toolbar`小工具 （Android 5.0 Lollipop 中引進） 可以視為動作列介面的概括&ndash;它要取代動作列。 `Toolbar`可用於應用程式版面配置的任何位置，而且比動作列更多自訂。

![範例工具列](images/toolbar.png)


## <a name="viewpagerandroiduser-interfacecontrolsview-pagerindexmd"></a>[ViewPager](~/android/user-interface/controls/view-pager/index.md) 

`ViewPager`是可讓使用者翻轉左右捲動的資料頁面的版面配置管理員。

![範例的 ViewPager](images/viewpager.png)


## <a name="webviewandroiduser-interfacecontrolsweb-viewmd"></a>[WebView](~/android/user-interface/controls/web-view.md)

`WebView` 是可讓您建立您自己的視窗來檢視網頁 （或甚至開發完整的瀏覽器） 的 UI 項目。

![範例網頁檢視](images/web-view.png)

