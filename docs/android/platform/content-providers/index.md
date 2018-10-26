---
title: ContentProviders 簡介
description: Android 作業系統使用內容提供者，以便存取共用的資料，例如媒體檔案，連絡人和行事曆資訊。 本文介紹 ContentProvider 類別，並提供如何使用它的兩個範例。
ms.prod: xamarin
ms.assetid: 6E1810AA-EB70-9AD0-1B32-D9418908CC97
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 4105200c48e41b142fc71e3a524023790b683cdb
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105983"
---
# <a name="intro-to-contentproviders"></a>ContentProviders 簡介

_Android 作業系統使用內容提供者，以便存取共用的資料，例如媒體檔案，連絡人和行事曆資訊。本文介紹 ContentProvider 類別，並提供如何使用它的兩個範例。_


## <a name="content-providers-overview"></a>內容提供者概觀

A *ContentProvider*封裝資料儲存機制，並提供 API 來存取它。 提供者存在於 Android 應用程式通常也有提供 UI 來顯示/管理資料的一部分。 使用內容提供者的主要優點讓其他應用程式輕鬆地存取封裝的資料使用的提供者用戶端物件 (稱為*ContentResolver*)。 在一起，內容提供者和內容解析程式提供一致的應用程式間 API，很容易建置和取用的資料存取。 任何應用程式可以選擇使用`ContentProviders`內部管理資料並將它公開給其他應用程式。

A`ContentProvider`也會需要您的應用程式，以提供自訂的搜尋建議，或如果您想要提供複雜的資料複製到其他應用程式貼上您的應用程式的能力。 本文件說明如何存取及建置`ContentProviders`Xamarin.Android 使用。

本章節的結構如下所示：

- **它的運作方式**&ndash;的功能概觀`ContentProvider`設計，並為它的運作方式。

- **使用內容提供者**&ndash;存取連絡人清單的範例。

- **使用共用資料的 ContentProvider** &ndash;書寫及取用`ContentProvider`相同的應用程式。

`ContentProviders` 並對其資料的資料指標通常用來填入 Listview。 請參閱[Listview 和 Adapter 指南](~/android/user-interface/layouts/list-view/index.md)如需有關如何使用這些類別。

`ContentProviders` 由 Android （或其他應用程式） 是簡單的方法，以在您的應用程式中包含來自其他來源的資料。 它們可讓您存取和呈現的資料，例如連絡人清單、 相片或從您的應用程式中的行事曆事件，並讓使用者與資料互動。

自訂`ContentProviders`是便利的方式來封裝您的資料，以使用您自己的應用程式，或用於其他應用程式 （包括自訂搜尋和複製/貼上等的特殊用途）。

在本節中的主題提供一些簡單的範例，使用和寫入`ContentProvider`程式碼。



## <a name="related-links"></a>相關連結

- [ContactsAdapter 示範 （範例）](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ContactsAdapterDemo/)
- [SimpleContentProvider （範例）](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/SimpleContentProvider)
- [內容提供者開發人員指南](http://developer.android.com/guide/topics/providers/content-providers.html)
- [ContentProvider 類別參考](https://developer.xamarin.com/api/type/Android.Content.ContentProvider/)
- [ContentResolver 類別參考](https://developer.xamarin.com/api/type/Android.Content.ContentResolver/)
- [ListView 類別參考](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
- [CursorAdapter 類別參考](https://developer.xamarin.com/api/type/Android.Widget.CursorAdapter/)
- [UriMatcher 類別參考](https://developer.xamarin.com/api/type/Android.Content.UriMatcher/)
- [Android.Provider](https://developer.xamarin.com/api/namespace/Android.Provider/)
- [ContactsContract 類別參考](https://developer.xamarin.com/api/type/Android.Provider.ContactsContract/)
