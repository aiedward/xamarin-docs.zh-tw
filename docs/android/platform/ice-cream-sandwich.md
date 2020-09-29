---
title: 霜淇淋三明治功能
description: 本文說明使用 Android 4 API-霜淇淋三明治的應用程式開發人員可使用的數個新功能。 它涵蓋數個新的使用者介面技術，然後檢查 Android 4 提供的各種新功能，以便在應用程式之間和裝置之間共用資料。
ms.prod: xamarin
ms.assetid: 78E18A62-C12F-A699-37FA-44B9F6B44273
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 1e72a09acc08fc4db49da0e94eb64fbd523e9ecf
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453685"
---
# <a name="ice-cream-sandwich-features"></a>霜淇淋三明治功能

_本文說明使用 Android 4 API-霜淇淋三明治的應用程式開發人員可使用的數個新功能。它涵蓋數個新的使用者介面技術，然後檢查 Android 4 提供的各種新功能，以便在應用程式之間和裝置之間共用資料。_

## <a name="overview"></a>概觀

Android OS 版本 4.0 (API 層級 14) 代表 Android 作業系統的主要修改，並包含許多重要的變更和升級，包括：

- **更新消費者介面** –有幾個新的 UI 功能可在開發人員建立應用程式使用者介面時，提供更強大的功能和彈性。 這些新功能包括：  `GridLayout` 、  `PopupMenu` 、  `Switch` widget 和  `TextureView` 。
- **更好的硬體加速** –2d 轉譯現在會在所有 Android 控制項的 GPU 上進行。 此外，根據預設，硬體加速在針對 Android 4.0 開發的所有應用程式中都是開啟的。
- **新的資料 api** –有新的資料存取權，先前尚未正式存取，例如行事歷數據和裝置擁有者的使用者設定檔。
- **應用程式資料共用** –在應用程式與裝置之間共用資料的作業現在比以往更容易，因為這種技術可  `ShareActionProvider` 讓您輕鬆地從動作列建立共用動作，並可讓您輕鬆地從建立共用動作，而將  *Android 橫樑* 用於近距離的無線  *通信 (NFC) * ，讓它能夠在彼此接近的裝置之間共用資料。

在本文中，我們將探索這些功能以及對 Android 4.0 API 所做的其他變更，我們將說明如何搭配使用每項功能與 Xamarin. Android。

## <a name="user-interface-features"></a>消費者介面功能

Android 4 提供各種新的使用者介面技術，包括：

- **[GridLayout](~/android/user-interface/layouts/grid-layout.md)** –支援控制項的2d 格線版面配置。
- **[切換 widget](~/android/user-interface/controls/switch.md)** –允許在開啟或關閉之間切換。
- **[TextureView](~/android/user-interface/controls/texture-view.md)** –啟用視圖內的影片和 OpenGL 內容。
- **[導覽](~/android/user-interface/controls/navigation-bar.md)** 列–包含用於回溯、home 和多工的虛擬按鈕。

此外，其他的 UI 元素已經過增強，例如 `<a href"/guides/android/user_interface/popup_menus">PopupMenu</a>` ，現在更容易使用，以及索引標籤，其外觀更完善。

## <a name="sharing-features"></a>共用功能

Android 4 包含數個新技術，可讓我們跨裝置和跨應用程式共用資料。 它也可讓您存取先前未提供的各種資料類型，例如行事曆資訊和裝置擁有者的使用者設定檔。 在本節中，我們將探討 Android 4 所提供的各種功能，以處理這些領域，包括：

- **[Android 橫樑](~/android/platform/android-beam.md)** -允許透過 NFC 共用資料。
- **[ShareActionProvider](~/android/user-interface/controls/action-bar.md)** –建立可讓開發人員指定動作列共用動作的提供者。
- **[使用者設定檔](~/android/user-interface/user-profile.md)** -提供裝置擁有者的設定檔資料存取權。
- 行事**[曆 API](~/android/user-interface/controls/calendar.md)** –可讓您從行事曆提供者存取行事歷數據。

## <a name="x86-emulators"></a>x86 模擬器

ICS 尚未支援使用 x86 模擬器進行開發。 只有 Android 2.3.3 （API 層級10）支援 x86 模擬器。 如需詳細資訊，請參閱設定 [X86 模擬器](~/android/get-started/installation/android-emulator/index.md) 。

## <a name="summary"></a>摘要

本文涵蓋了 Android 4 現在提供的各種新技術。 我們已複習新的使用者介面功能，例如 *GridLayout*、 *PopupMenu*和 *Switch* widget。 我們也探討了一些控制系統 UI 的新支援，以及如何使用 *TextureView*。 然後我們討論了各種新的共用技術。 我們討論了 *Android 橫樑* 如何讓您在使用 *NFC*的裝置之間共用資訊、討論新的行事 *曆 API*，同時也示範如何使用內建的 *ShareActionProvider*。
最後，我們探討了如何使用 *ContactsContract* 提供者來存取使用者設定檔資料。

## <a name="related-links"></a>相關連結

- [TextureViewDemo (範例) ](/samples/xamarin/monodroid-samples/textureviewdemo)
- [CalendarDemo (範例) ](/samples/xamarin/monodroid-samples/calendardemo)
- [Tab 版面配置教學課程](~/android/user-interface/layouts/tab-layout/index.md)
- [霜淇淋三明治](https://developer.android.com/about/versions/android-4.0-highlights.html)
- [Android 4.0 平臺](https://developer.android.com/about/versions/android-4.0.html)