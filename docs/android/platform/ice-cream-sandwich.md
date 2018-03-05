---
title: "冰淇淋三明治功能"
description: "這篇文章描述一些可用的 Android 4 API-冰淇淋三明治的應用程式開發人員的新功能。 它涵蓋了數種新的使用者介面技術，然後檢查 各種 Android 4 提供共用資料之間的應用程式和裝置之間的新功能。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 78E18A62-C12F-A699-37FA-44B9F6B44273
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.openlocfilehash: 9726ec83cd38dd2f237152f0f8e7373f509a3e01
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="ice-cream-sandwich-features"></a>冰淇淋三明治功能

_這篇文章描述一些可用的 Android 4 API-冰淇淋三明治的應用程式開發人員的新功能。它涵蓋了數種新的使用者介面技術，然後檢查 各種 Android 4 提供共用資料之間的應用程式和裝置之間的新功能。_

## <a name="overview"></a>總覽

代表主要的縮短 Android 作業系統的 android OS 4.0 (API 層級 14) 版，並包含數個重要變更與升級，包括：

-   **更新使用者介面**– 數種新的 UI 功能讓開發人員更電源和彈性他們建立應用程式使用者介面。 這些新功能包括： `GridLayout` ， `PopupMenu` ， `Switch`  widget 中，和`TextureView`。 
-   **較佳的硬體加速**– 2D 轉譯現在將針對所有 Android 控制項 GPU 上的。 此外，硬體加速，是根據預設，所有針對 Android 4.0 開發的應用程式中。 
-   **新的資料 Api** – 沒有新的存取權，無法先前正式存取，例如行事曆資料和裝置擁有者的使用者設定檔的資料。 
-   **應用程式資料共用**– 應用程式和裝置之間共用的資料現在是更容易比以往透過技術例如`ShareActionProvider`，讓您輕鬆地在動作列中，建立共用動作和*Android 資料交換*如*附近欄位通訊 (NFC)* ，因此會很容易在彼此相近的裝置之間分享資料。 


在本文中，我們要瀏覽這些功能和其他 Android 4.0 api 中，所做的變更，我們將說明如何使用 Xamarin.Android 每項功能。

## <a name="user-interface-features"></a>使用者介面功能

有各種新的使用者介面技術是適用於 Android 4，包括：

-   **[格線](~/android/user-interface/layouts/grid-layout.md)** – 控制項支援 2D 格線版面配置。 
-   **[切換 widget](~/android/user-interface/controls/switch.md)**  – 允許切換為 ON 或 OFF。 
-   **[TextureView](~/android/user-interface/controls/texture-view.md)**  – 可讓視訊和檢視表中的 OpenGL 內容。 
-   **[導覽列](~/android/user-interface/controls/navigation-bar.md)** – 包含虛擬按鈕後，家用和多個工作。 


此外，其他 UI 項目已有所加強，例如`<a href"/guides/android/user_interface/popup_menus">PopupMenu</a>`，現在是容易使用，以及索引標籤，其具有更精美的外觀。

## <a name="sharing-features"></a>共用功能

Android 4 包含數種新的技術，可讓我們跨裝置和應用程式之間共用資料。 它也提供各種類型的資料未先前提供，例如行事曆資訊和裝置擁有者的使用者設定檔的存取。 本章節內容，我們將檢驗各種功能所提供 Android 4 該位址這些區域包括：

-  **[Android 資料交換](~/android/platform/android-beam.md)** – 允許資料透過 NFC 共用。
-   **[ShareActionProvider](~/android/user-interface/controls/action-bar.md)**  – 建立可讓開發人員指定共用的動作，在動作列中的提供者。 
-   **[使用者設定檔](~/android/user-interface/user-profile.md)** – 可讓您對裝置擁有者的分析資料的存取。 
-   **[行事曆 API](~/android/user-interface/controls/calendar.md)**  – 提供行事曆資料的存取，從行事曆提供者。 

## <a name="x86-emulators"></a>x86 模擬器

ICS 還不支援開發與 x86 模擬器。 x86 模擬器，只支援 Android 2.3.3，應用程式開發介面層級 10。 請參閱[設定 x86 模擬器](~/android/get-started/installation/android-emulator/index.md)如需詳細資訊。

## <a name="summary"></a>總結

本文涵蓋各種不同的是適用於 Android 4 的新技術。 我們檢閱新的使用者介面功能，例如*格線*， *PopupMenu*，和*交換器*widget。 我們也討論了一些新的支援來控制系統的 UI，及如何使用*TextureView*。 然後我們將討論各種不同的新共用技術。 我們涵蓋如何*Android 資料交換*我們跨裝置使用，來共用資訊*NFC*，討論新*行事曆 API*，也示範了如何使用此內建和*ShareActionProvider*。
最後，我們檢驗如何使用*ContactsContract*來存取使用者設定檔資料提供者。



## <a name="related-links"></a>相關連結

- [冰淇淋三明治範例](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ICS_Samples/)
- [TextureViewDemo （範例）](https://developer.xamarin.com/samples/monodroid/TextureViewDemo/)
- [CalendarDemo （範例）](https://developer.xamarin.com/samples/monodroid/CalendarDemo/)
- [教學課程中的版面配置 索引標籤](~/android/user-interface/layouts/tab-layout/index.md)
- [冰淇淋三明治](http://developer.android.com/about/versions/android-4.0-highlights.html)
- [Android 4.0 平台](http://developer.android.com/about/versions/android-4.0.html)
