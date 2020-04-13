---
title: 內容提供者簡介
description: Android 操作系統使用內容提供者來方便訪問共享數據,如媒體文件、聯繫人和日曆資訊。 本文介紹了 ContentProvider 類,並提供了如何使用它的兩個範例。
ms.prod: xamarin
ms.assetid: 6E1810AA-EB70-9AD0-1B32-D9418908CC97
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 496e5c092c79f4f71bddaad30bea6acd1d58d375
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027547"
---
# <a name="intro-to-contentproviders"></a>內容提供者簡介

_Android 操作系統使用內容提供者來方便訪問共享數據,如媒體文件、聯繫人和日曆資訊。本文介紹了 ContentProvider 類,並提供了如何使用它的兩個範例。_

## <a name="content-providers-overview"></a>內容提供者概述

*Content Provider*封裝數據存儲庫並提供 API 來訪問它。 提供程式作為 Android 應用程式的一部分存在,該應用程式通常還提供用於顯示/管理數據的 UI。 使用內容提供程式的主要好處是使其他應用程式能夠使用提供程式客戶端物件(稱為*ContentResolver)* 輕鬆存取封裝的資料。 總之,內容提供程式和內容解析器為數據訪問提供了一致的應用程式間 API,易於構建和使用。 任何應用程式都可以選擇用於`ContentProviders`在內部管理數據,也可以將其公開給其他應用程式。

應用程式`ContentProvider`還需要提供自定義搜索建議,或者要提供從應用程式複製複雜數據以粘貼到其他應用程式的能力。 此文件展示如何使用 Xamarin.Android 存`ContentProviders`取與建置 。

本節的結構如下:

- **工作**&ndash;原理 概述`ContentProvider`設計物件 及其工作原理。

- **使用內容提供程式**&ndash;訪問連絡人清單的範例。

- **使用 ContentProvider 在同**&ndash;一應用程式`ContentProvider`中共用資料 寫入與使用 。

`ContentProviders`對其數據操作的遊標通常用於填充 ListViews。 有關如何使用這些類別的詳細資訊,請參閱[ListViews 和配接器指南](~/android/user-interface/layouts/list-view/index.md)。

`ContentProviders`由 Android(或其他應用程式)公開的一種簡單方法,用於將來自應用程式其他源的數據包含在應用程式中。 它們允許您從應用程式內訪問和顯示連絡人清單、照片或行事曆事件等數據,並允許使用者與該資料進行互動。

自訂`ContentProviders`是打包數據以在您自己的應用內使用或供其他應用程式使用(包括自定義搜索和複製/粘貼等特殊用途)的便捷方法。

本節中的主題提供了一些使用和編寫`ContentProvider`代碼的簡單示例。

## <a name="related-links"></a>相關連結

- [連絡人配接器示範(範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-contactsadapterdemo)
- [簡單內容提供者 (例如範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-simplecontentprovider)
- [內容提供者開發人員指南](https://developer.android.com/guide/topics/providers/content-providers.html)
- [內容提供者類別參考](xref:Android.Content.ContentProvider)
- [內容解析器類別引用](xref:Android.Content.ContentResolver)
- [清單檢視類別參考](xref:Android.Widget.ListView)
- [游標配接器類別引用](xref:Android.Widget.CursorAdapter)
- [UriMatcher 類別引用](xref:Android.Content.UriMatcher)
- [安卓.提供者](xref:Android.Provider)
- [聯絡人合約類別引用](xref:Android.Provider.ContactsContract)
