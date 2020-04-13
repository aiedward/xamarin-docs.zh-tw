---
title: 霜淇淋三明治功能
description: 本文介紹了一些可以使用 Android 4 API - 霜淇淋三明治的應用程式開發人員提供的新功能。 它涵蓋了幾種新的用戶介面技術,然後檢查了 Android 4 為在應用程式之間和設備之間共享數據提供的各種新功能。
ms.prod: xamarin
ms.assetid: 78E18A62-C12F-A699-37FA-44B9F6B44273
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 4fbbe1bec317e66166d5218ef0ed54247aa4f6dd
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "78292618"
---
# <a name="ice-cream-sandwich-features"></a>霜淇淋三明治功能

_本文介紹了一些可以使用 Android 4 API - 霜淇淋三明治的應用程式開發人員提供的新功能。它涵蓋了幾種新的用戶介面技術,然後檢查了 Android 4 為在應用程式之間和設備之間共享數據提供的各種新功能。_

## <a name="overview"></a>概觀

Android 操作系統版本 4.0 (API 14) 代表了 Android 操作系統的主要改版,包括許多重要的更改和升級,包括:

- **更新了使用者介面**– 幾個新的 UI 功能為開發人員創建應用程式使用者介面提供了更大的功能和靈活性。 這些新功能`GridLayout`包括: `PopupMenu` `Switch` 、`TextureView`、 小元件與 。
- **更好的硬體加速**– 2D 渲染現在發生在所有 Android 控制項的 GPU 上。 此外,默認情況下,在為 Android 4.0 開發的所有應用程式中,硬體加速處於打開狀態。
- **新的資料 API** – 對以前未正式訪問的數據(如行事曆資料和設備擁有者的使用者設定檔)有新的存取許可權。
- **應用資料共享**– 應用程式和設備之間的共享數據現在比以往更容易通過`ShareActionProvider`技術,如 , 這使得從操作欄創建共用操作, 和 Android*光束*近*場通信 (NFC)* , 這使得在彼此接近的設備之間共享數據變得一帆風順。

在本文中,我們將探討這些功能和對 Android 4.0 API 所做的其他更改,我們將解釋如何使用 Xamarin.Android 的每個功能。

## <a name="user-interface-features"></a>使用者介面功能

Android 4 提供各種新的使用者介面技術,包括:

- **[網格佈局](~/android/user-interface/layouts/grid-layout.md)**– 支援控制項的 2D 網格佈局。
- **[切換小部件](~/android/user-interface/controls/switch.md)**– 允許在打開或關閉之間切換。
- **[紋理檢視](~/android/user-interface/controls/texture-view.md)**– 在檢視中啟用視頻和 OpenGL 內容。
- **[導覽列](~/android/user-interface/controls/navigation-bar.md)**– 包含用於後台、家庭和多任務處理的虛擬按鈕。

此外,其他 UI 元素已得到增強`<a href"/guides/android/user_interface/popup_menus">PopupMenu</a>`,例如現在更易於處理的和選項卡,它們的外觀更加美觀。

## <a name="sharing-features"></a>共用功能

Android 4 包括幾項新技術,使我們能夠跨設備和跨應用程序共享數據。 它還提供對以前不可用的各種類型數據的訪問,例如日曆資訊和設備擁有者的使用者配置檔。 在本節中,我們將檢查 Android 4 提供的各種功能,這些功能可解決這些方面,包括:

- **[安卓光束](~/android/platform/android-beam.md)**– 允許通過 NFC 共享數據。
- **[共用操作提供者](~/android/user-interface/controls/action-bar.md)**– 創建允許開發人員從操作列指定共用操作的提供程式。
- **[使用者設定檔](~/android/user-interface/user-profile.md)**– 提供對設備擁有者的設定檔數據的訪問。
- **[日曆 API](~/android/user-interface/controls/calendar.md)** – 提供從行事曆提供程式存取行事曆資料。

## <a name="x86-emulators"></a>x86 模擬器

ICS 還不支援使用 x86 模擬器進行開發。 x86 模擬器僅支援 Android 2.3.3,API 級別 10。 有關詳細資訊[,請參閱設定 x86 模擬器](~/android/get-started/installation/android-emulator/index.md)。

## <a name="summary"></a>總結

本文介紹了現在Android 4提供的各種新技術。 我們查看了新的用戶介面功能,如*GridLayout、PopupMenu*和*Switch*小部件。 *PopupMenu* 我們還研究了一些用於控制系統 UI 的新支援,以及如何使用*TextureView*。 然後,我們討論了各種新的共享技術。 我們介紹了*Android Beam*如何讓你在使用*NFC*的設備之間共用資訊,討論了新的*日曆API,* 並展示了如何使用內置的*ShareActionProvider。*
最後,我們研究了如何使用*聯繫人合同*提供程式訪問使用者配置檔數據。

## <a name="related-links"></a>相關連結

- [紋理檢視示範(範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/textureviewdemo)
- [行事曆示範(範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/calendardemo)
- [選項卡佈局教學](~/android/user-interface/layouts/tab-layout/index.md)
- [霜淇淋三明治](https://developer.android.com/about/versions/android-4.0-highlights.html)
- [安卓 4.0 平臺](https://developer.android.com/about/versions/android-4.0.html)
