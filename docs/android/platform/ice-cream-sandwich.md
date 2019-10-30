---
title: 霜淇淋三明治功能
description: 本文說明一些可供應用程式開發人員使用 Android 4 API 霜淇淋三明治的新功能。 其中涵蓋數個新的使用者介面技術，然後檢查 Android 4 提供的各種新功能，以便在應用程式之間和裝置之間共用資料。
ms.prod: xamarin
ms.assetid: 78E18A62-C12F-A699-37FA-44B9F6B44273
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 382315f755102d7111db1a5c0f71d43bdea97a10
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73020178"
---
# <a name="ice-cream-sandwich-features"></a>霜淇淋三明治功能

_本文說明一些可供應用程式開發人員使用 Android 4 API 霜淇淋三明治的新功能。其中涵蓋數個新的使用者介面技術，然後檢查 Android 4 提供的各種新功能，以便在應用程式之間和裝置之間共用資料。_

## <a name="overview"></a>總覽

Android OS 版本4.0 （API 層級14）代表 Android 作業系統的主要修改，並包含許多重要的變更和升級，包括：

- **更新的使用者介面**–有幾個新的 UI 功能可讓開發人員在建立應用程式使用者介面時擁有更強大的能力和彈性。 這些新功能包括： `GridLayout`、`PopupMenu`、`Switch` widget 和 `TextureView`。 
- **更好的硬體加速**–在所有 Android 控制項的 GPU 上現在會進行2d 轉譯。 此外，根據預設，硬體加速在針對 Android 4.0 開發的所有應用程式中都是開啟的。 
- **新的資料 api** -先前尚未正式存取之資料的新存取權，例如行事歷數據和裝置擁有者的使用者設定檔。 
- **應用程式資料共用**–在應用程式和裝置之間共用資料比以往更容易透過 `ShareActionProvider` 之類的技術，這可讓您輕鬆地從動作列建立共用動作，以及使用*Android 橫樑*進行近距離無線*通信（NFC）* ，使其在靠近彼此的鄰近裝置之間共用資料。 

在本文中，我們將探索 Android 4.0 API 的這些功能和其他變更，並說明如何搭配使用每項功能與 Xamarin。

## <a name="user-interface-features"></a>使用者介面功能

Android 4 提供各種新的使用者介面技術，包括：

- **[GridLayout](~/android/user-interface/layouts/grid-layout.md)** –支援控制項的2d 格線版面配置。 
- **[切換 widget](~/android/user-interface/controls/switch.md)** –允許在開啟或關閉之間切換。 
- **[TextureView](~/android/user-interface/controls/texture-view.md)** –在視圖內啟用影片和 OpenGL 內容。 
- **[導覽](~/android/user-interface/controls/navigation-bar.md)** 列–包含 [上一頁]、[首頁] 和 [多工] 等虛擬按鈕。 

此外，其他的 UI 專案已經過增強，例如 `<a href"/guides/android/user_interface/popup_menus">PopupMenu</a>`，現在更容易使用，而索引標籤則具有更精美的外觀。

## <a name="sharing-features"></a>共用功能

Android 4 包含數個新技術，可讓我們跨裝置和跨應用程式共用資料。 它也可讓您存取先前未提供的各種資料類型，例如行事曆資訊和裝置擁有者的使用者設定檔。 在本節中，我們將探討 Android 4 提供的各種功能，以解決這些領域，包括：

- **[Android 橫樑](~/android/platform/android-beam.md)** –允許透過 NFC 共用資料。
- **[ShareActionProvider](~/android/user-interface/controls/action-bar.md)** –建立可讓開發人員從動作列指定共用動作的提供者。 
- **[使用者設定檔](~/android/user-interface/user-profile.md)** –提供裝置擁有者的設定檔資料存取。 
- 行事 **[曆 API](~/android/user-interface/controls/calendar.md)** -可讓您從行事曆提供者存取行事歷數據。 

## <a name="x86-emulators"></a>x86 模擬器

ICS 尚未支援使用 x86 模擬器進行開發。 只有 Android 2.3.3 （API 層級10）支援 x86 模擬器。 如需詳細資訊，請參閱設定[X86 模擬器](~/android/get-started/installation/android-emulator/index.md)。

## <a name="summary"></a>總結

本文涵蓋了 Android 4 現在提供的各種新技術。 我們已審查新的使用者介面功能，例如*GridLayout*、 *PopupMenu*和*交換器*widget。 我們也探討了控制系統 UI 的一些新支援，以及如何使用*TextureView*。 然後我們討論了各種新的共用技術。 我們涵蓋了*Android 橫樑*如何讓您在使用*NFC*的裝置之間共用資訊、討論新的行事*曆 API*，同時也示範如何使用內建的*ShareActionProvider*。
最後，我們已檢查如何使用*ContactsContract*提供者來存取使用者設定檔資料。

## <a name="related-links"></a>相關連結

- [霜淇淋三明治範例](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-ics-samples)
- [TextureViewDemo （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/textureviewdemo)
- [CalendarDemo （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/calendardemo)
- [索引標籤版面配置教學課程](~/android/user-interface/layouts/tab-layout/index.md)
- [霜淇淋三明治](https://developer.android.com/about/versions/android-4.0-highlights.html)
- [Android 4.0 平臺](https://developer.android.com/about/versions/android-4.0.html)
