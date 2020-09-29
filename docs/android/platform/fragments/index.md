---
title: 片段
description: Android 3.0 引進了片段，顯示如何針對在手機和平板電腦上找到的許多不同螢幕大小，支援更有彈性的設計。 本文將說明如何使用片段來開發 Xamarin. Android 應用程式，以及如何在 Android 之前的 3.0 (API 層級 11) 裝置上支援片段。
ms.prod: xamarin
ms.assetid: 1AFB4242-A337-F8E0-83D9-B8D850D7F384
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/15/2018
ms.openlocfilehash: 960f64c05bcc474a7e197c06990840d5e9b88caf
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453840"
---
# <a name="fragments"></a>片段

_Android 3.0 引進了片段，顯示如何針對在手機和平板電腦上找到的許多不同螢幕大小，支援更有彈性的設計。本文將說明如何使用片段來開發 Xamarin. Android 應用程式，以及如何在 Android 之前的 3.0 (API 層級 11) 裝置上支援片段。_

## <a name="fragments-overview"></a>片段總覽

在大部分的平板電腦上找到較大的螢幕大小，在 Android 開發中增加了一層複雜度，而針對小型螢幕設計的版面配置也不一定適用于較大的螢幕，反之亦然。 為了減少這種情況所引進的複雜度，Android 3.0 新增了兩項新功能： *片段* 和 *支援封裝*。

您可以將片段視為使用者介面模組。 它們可讓開發人員將使用者介面分成隔離、可重複使用的元件，這些元件可在不同的活動中執行。 在執行時間，活動本身將決定要使用的片段。

支援套件最初稱為 *相容性程式庫* ，而允許的片段是在 android 3.0 (API Level 11) 之前，在執行 android 版本的裝置上使用。

例如，下圖說明單一應用程式如何在不同的裝置外型規格中使用片段。

[![在平板電腦和手機中如何使用片段的圖表](images/00.png)](images/00.png#lightbox)

*片段 A* 包含清單，而 *片段 B* 包含在該清單中選取之專案的詳細資料。 在平板電腦上執行應用程式時，它可以在相同活動上顯示這兩個片段。 當相同的應用程式在 (手機上執行時，如果有較小的螢幕大小) ，則會在兩個不同的活動中裝載這些片段。 片段 A 和片段 B 在兩個外型規格上都相同，但裝載它們的活動則不同。

為了協助活動協調並管理所有這些片段，Android 引進了稱為 *FragmentManager*的新類別。 每個活動都有自己的實例，可 `FragmentManager` 用於新增、刪除和尋找裝載的片段。 下圖說明片段和活動之間的關聯性：

[![說明活動、片段管理員和片段之間關聯性的圖表](images/01.png)](images/01.png#lightbox)

在某些方面，可以將片段視為複合控制項或迷你活動。 它們會將 UI 的一部分組合成可重複使用的模組，讓開發人員可以在活動中獨立使用這些模組。 片段有一個 view 階層（就像活動一樣），但不同于活動，它可以跨畫面共用。 不同于片段的視圖會有自己的生命週期;views 不是。

當活動是一或多個片段的主機時，不會直接察覺片段本身。 同樣地，片段也不會直接察覺裝載活動中的其他片段。 不過，片段和活動都知道 `FragmentManager` 其活動中的。 藉由使用 `FragmentManager` ，可能會有活動或片段取得片段之特定實例的參考，然後在該實例上呼叫方法。 如此一來，活動或片段就可以進行通訊，並與其他片段互動。

本指南包含如何使用片段的完整涵蓋範圍，包括：

- **建立片段** –如何建立必須執行的基本片段和主要方法。
- **片段管理和交易** –如何在執行時間操作片段。
- **Android 支援套件** –如何使用程式庫，讓您可以在較舊版本的 Android 上使用片段。

## <a name="requirements"></a>需求

Android SDK 從 API 層級 11 (Android 3.0) 開始提供片段，如下列螢幕擷取畫面所示：

[![選取 Android SDK 管理員中的 API 層級](images/02.png)](images/02.png#lightbox)

您可以在 Xamarin. Android 4.0 和更新版本中使用片段。 Xamarin. Android 應用程式必須至少以 API 層級11為目標， (Android 3.0) 或更高版本，才能使用片段。 目標 Framework 可以在專案屬性中設定，如下所示：

[![在專案選項中設定目標 Framework API 層級](images/03-sml.png)](images/03.png#lightbox)

您可以使用 Android 支援套件和 Xamarin. Android 4.2 或更高版本，在較舊版本的 Android 中使用片段。 本節的檔將詳細說明如何進行這項作業。

## <a name="related-links"></a>相關連結

- [蜂巢資源庫 (範例) ](/samples/xamarin/monodroid-samples/honeycombgallery)
- [片段](https://developer.android.com/guide/topics/fundamentals/fragments.html)
- [支援封裝](https://developer.android.com/sdk/compatibility-library.html)