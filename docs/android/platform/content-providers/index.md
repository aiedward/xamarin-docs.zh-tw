---
title: ContentProviders 簡介
description: Android 作業系統使用內容提供者，以便存取共用的資料，例如媒體檔案、 連絡人和行事曆資訊。 本文介紹 ContentProvider 類別，並提供如何使用它的兩個範例。
ms.prod: xamarin
ms.assetid: 6E1810AA-EB70-9AD0-1B32-D9418908CC97
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: e534c02820bfeab3a5bc1211bf0cbb20b9821af3
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
ms.locfileid: "30764160"
---
# <a name="intro-to-contentproviders"></a>ContentProviders 簡介

_Android 作業系統使用內容提供者，以便存取共用的資料，例如媒體檔案、 連絡人和行事曆資訊。本文介紹 ContentProvider 類別，並提供如何使用它的兩個範例。_


## <a name="content-providers-overview"></a>內容提供者概觀

A *ContentProvider*封裝資料儲存機制，並提供 API 來存取它。 提供者是做為 Android 應用程式通常也有提供 UI 來顯示/管理資料的一部分。 使用內容的提供者的主要優點在於已啟用其他的應用程式輕鬆地存取封裝使用的提供者用戶端物件的資料 (稱為*ContentResolver*)。 在一起，內容提供者與內容解析程式都提供一個一致的應用程式間 API，以建置及使用簡單的資料存取。 任何應用程式可以選擇使用`ContentProviders`內部管理資料並將它公開給其他應用程式。

A`ContentProvider`也是必要的應用程式以提供自訂的搜尋建議，或如果您想要提供複雜的資料複製貼上到其他應用程式的應用程式的能力。 本文件示範如何存取及建置`ContentProviders`Xamarin.Android 與。

這個區段的結構如下所示：

- **它的運作方式**&ndash;的功能概觀`ContentProvider`設計用於和它的運作方式。

- **使用內容的提供者**&ndash;存取連絡人清單的範例。

- **使用共用資料 ContentProvider** &ndash;寫入和取用`ContentProvider`相同的應用程式中。

`ContentProviders` 並在其資料運作的資料指標通常用來填入 Listview。 請參閱[Listview 和配接器的指南](~/android/user-interface/layouts/list-view/index.md)如需有關如何使用這些類別。

`ContentProviders` 由 Android （或其他應用程式） 是用來在您的應用程式中包含來自其他來源的資料。 它們可讓您存取和呈現資料，例如連絡人清單、 相片或應用程式中的，從行事曆事件並可讓使用者與資料互動。

自訂`ContentProviders`是方便的方法來封裝您的資料，供您自己的應用程式，或使用其他應用程式 （包括自訂搜尋和複製/貼上等的特殊用途）。

本節中的主題提供一些簡單的範例，使用和寫入`ContentProvider`程式碼。



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
