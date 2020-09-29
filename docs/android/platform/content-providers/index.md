---
title: ContentProviders 簡介
description: Android 作業系統使用內容提供者來加速存取共用資料，例如媒體檔案、連絡人和行事曆資訊。 本文介紹 ContentProvider 類別，並提供兩個使用方法的範例。
ms.prod: xamarin
ms.assetid: 6E1810AA-EB70-9AD0-1B32-D9418908CC97
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 00da8b668ea26aa9146ff0c30d07a60cefe6695a
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454009"
---
# <a name="intro-to-contentproviders"></a>ContentProviders 簡介

_Android 作業系統使用內容提供者來加速存取共用資料，例如媒體檔案、連絡人和行事曆資訊。本文介紹 ContentProvider 類別，並提供兩個使用方法的範例。_

## <a name="content-providers-overview"></a>內容提供者總覽

*ContentProvider*會封裝資料存放庫，並提供 API 來存取它。 提供者是 Android 應用程式的一部分，通常也會提供 UI 來顯示/管理資料。 使用內容提供者的主要優點是，讓其他應用程式可以使用提供者用戶端物件 (稱為 *ContentResolver*) ，輕鬆地存取封裝的資料。 內容提供者和內容解析程式一起提供一致的應用程式間 API，可讓您輕鬆建立和取用資料存取。 任何應用程式都可以選擇使用 `ContentProviders` 在內部管理資料，也可以將它公開給其他應用程式。

`ContentProvider`您的應用程式也需要提供自訂搜尋建議，或者，如果您想要提供從應用程式複製複雜資料以貼到其他應用程式的功能。 本檔說明如何使用 Xamarin 存取和建立 `ContentProviders` 。

本節的結構如下所示：

- **運作方式** &ndash; 概述 `ContentProvider` 其設計用途，以及其運作方式。

- **使用內容提供者** &ndash; 存取連絡人清單的範例。

- **使用 ContentProvider 共用資料** &ndash;`ContentProvider`在相同的應用程式中撰寫和使用。

`ContentProviders` 而在其資料上操作的資料指標通常會用來填入 Listview。 如需如何使用這些類別的詳細資訊，請參閱 [listview 和介面卡指南](~/android/user-interface/layouts/list-view/index.md) 。

`ContentProviders` 由 Android (或其他應用程式公開) 可讓您輕鬆地在應用程式中包含來自其他來源的資料。 它們可讓您從您的應用程式中存取和呈現資料（例如連絡人清單、相片或行事曆事件），並讓使用者與該資料互動。

自訂 `ContentProviders` 是一種方便的方式，可將您的資料封裝在您自己的應用程式內，或供其他應用程式使用 (包括自訂搜尋和複製/貼上) 等特殊用途。

本節中的主題提供一些使用和撰寫程式碼的簡單範例 `ContentProvider` 。

## <a name="related-links"></a>相關連結

- [ContactsAdapter 示範 (範例) ](/samples/xamarin/monodroid-samples/platformfeatures-contactsadapterdemo)
- [SimpleContentProvider (範例) ](/samples/xamarin/monodroid-samples/platformfeatures-simplecontentprovider)
- [內容提供者開發人員指南](https://developer.android.com/guide/topics/providers/content-providers.html)
- [ContentProvider 類別參考](xref:Android.Content.ContentProvider)
- [ContentResolver 類別參考](xref:Android.Content.ContentResolver)
- [ListView 類別參考](xref:Android.Widget.ListView)
- [CursorAdapter 類別參考](xref:Android.Widget.CursorAdapter)
- [UriMatcher 類別參考](xref:Android.Content.UriMatcher)
- [Android. 提供者](xref:Android.Provider)
- [ContactsContract 類別參考](xref:Android.Provider.ContactsContract)