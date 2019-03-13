---
title: Ice Cream Sandwich 功能
description: 這篇文章描述一些可用 Android 4 API-Ice Cream Sandwich 應用程式開發人員的新功能。 它涵蓋了數個新的使用者介面技術，然後檢查 各種 Android 4 提供的資料之間的應用程式和裝置之間共用的新功能。
ms.prod: xamarin
ms.assetid: 78E18A62-C12F-A699-37FA-44B9F6B44273
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 00b553ae8de0dfcd86d57d1d5e3e2a892d6b5463
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57667278"
---
# <a name="ice-cream-sandwich-features"></a>Ice Cream Sandwich 功能

_這篇文章描述一些可用 Android 4 API-Ice Cream Sandwich 應用程式開發人員的新功能。它涵蓋了數個新的使用者介面技術，然後檢查 各種 Android 4 提供的資料之間的應用程式和裝置之間共用的新功能。_

## <a name="overview"></a>總覽

Android OS 版本 4.0 (API 層級 14) 代表主要他們的 Android 作業系統，並且包含數項重要變更與升級，包括：

-   **更新使用者介面**– 數個新的 UI 功能讓開發人員更電源和彈性，當他們建立應用程式使用者介面。 這些新功能包括： `GridLayout` ， `PopupMenu` ，`Switch`小工具，和`TextureView`。 
-   **更好的硬體加速**– 2D 轉譯現在會發生的所有 Android 控制項在 GPU 上。 此外，硬體加速功能，是根據預設，所有針對 Android 4.0 開發的應用程式中。 
-   **新的資料 Api** – 沒有新的存取權，不是先前正式可供存取，例如行事曆資料和裝置擁有者的使用者設定檔的資料。 
-   **應用程式資料共用**– 應用程式和裝置之間共用資料為現在比以往透過技術更容易狀況`ShareActionProvider`，讓您輕鬆地從動作列中，建立共用的動作和*Android 資料交換*針對*靠近欄位通訊 (NFC)* ，因此會迅速地，在彼此鄰近裝置之間共用資料。 


在本文中，我們將探討這些功能，以及其他 Android 4.0 API 中，對所做的變更，我們將說明如何使用 Xamarin.Android 中的每項功能。

## <a name="user-interface-features"></a>使用者介面功能

針對數種新的使用者介面技術可用於 Android 4，包括：

-   **[GridLayout](~/android/user-interface/layouts/grid-layout.md)**  – 控制項支援 2D 格線版面配置。 
-   **[切換 widget](~/android/user-interface/controls/switch.md)**  – 允許切換 ON 或 OFF。 
-   **[TextureView](~/android/user-interface/controls/texture-view.md)**  – 可讓視訊和 OpenGL 內容檢視中的。 
-   **[導覽列](~/android/user-interface/controls/navigation-bar.md)** – 包含虛擬按鈕後，家庭和多工。 


此外，其他 UI 項目已經過加強，例如`<a href"/guides/android/user_interface/popup_menus">PopupMenu</a>`，這現在是容易使用，而且索引標籤上，具有更完備的外觀。

## <a name="sharing-features"></a>共用功能

Android 4 包含數個新的技術，讓我們跨裝置和應用程式間共用資料。 它也提供各種類型的不是先前使用，例如行事曆資訊和裝置擁有者的使用者設定檔的資料存取。 在本節中我們將介紹各種不同的功能所提供 Android 4 該位址這些領域，包括：

-  **[Android 資料交換](~/android/platform/android-beam.md)** – 允許資料透過 NFC 共用。
-   **[ShareActionProvider](~/android/user-interface/controls/action-bar.md)**  – 建立可讓開發人員，以指定共用的動作，從動作列中的提供者。 
-   **[使用者設定檔](~/android/user-interface/user-profile.md)** – 可讓您的裝置擁有者的設定檔資料的存取。 
-   **[行事曆 API](~/android/user-interface/controls/calendar.md)**  – 可讓您存取行事曆資料從行事曆提供者。 

## <a name="x86-emulators"></a>x86 模擬器

ICS 尚不支援開發與 x86 模擬器。 x86 模擬器，只支援 Android 2.3.3，API 層級 10。 請參閱[設定的 x86 模擬器](~/android/get-started/installation/android-emulator/index.md)如需詳細資訊。

## <a name="summary"></a>總結

本文涵蓋各種不同的新技術，現在是適用於 Android 4。 我們檢閱新的使用者介面功能，例如*GridLayout*，*蹦現式功能表*，並*交換器*小工具。 我們也討論過幾個新的支援，來控制系統的 UI，以及如何使用*TextureView*。 然後，我們會討論各種不同的新共用的技術。 我們涵蓋如何*Android 資料交換*讓我們在使用的裝置來共用資訊*NFC*，討論新*行事曆 API*，並同時示範了如何使用內建*ShareActionProvider*。
最後中,，我們會檢查使用方式*ContactsContract*存取使用者設定檔資料的提供者。



## <a name="related-links"></a>相關連結

- [Ice Cream Sandwich 範例](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ICS_Samples/)
- [TextureViewDemo （範例）](https://developer.xamarin.com/samples/monodroid/TextureViewDemo/)
- [CalendarDemo （範例）](https://developer.xamarin.com/samples/monodroid/CalendarDemo/)
- [教學課程中的版面配置 索引標籤](~/android/user-interface/layouts/tab-layout/index.md)
- [Ice Cream Sandwich](https://developer.android.com/about/versions/android-4.0-highlights.html)
- [Android 4.0 平台](https://developer.android.com/about/versions/android-4.0.html)
