---
title: "如何在內容提供者工作"
ms.topic: article
ms.prod: xamarin
ms.assetid: B9E2EF89-7EBE-45F5-1ED9-7D2C70BE792C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 142ef16606bbf47de073122791fa2509ed6b6353
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="how-content-providers-work"></a>如何在內容提供者工作

有兩種類別參與`ContentProvider`互動：

- **ContentProvider** &ndash;實作 API 會公開一組資料，以標準方式。 主要方法如下查詢、 插入、 更新和刪除。

- **ContentResolver** &ndash;與通訊的靜態 proxy`ContentProvider`存取其資料，或從相同的應用程式內從另一個應用程式。

內容提供者通常受到所支援 SQLite 資料庫中，但 API 表示，使用程式碼不需要有任何了解基礎 SQL。 查詢會透過 Uri 參考 （以減少的相依性的基礎資料結構） 的資料行名稱，使用常數和`ICursor`就會傳回使用的程式碼來反覆查看。

<a name="Consuming_a_ContentProvider" />

## <a name="consuming-a-contentprovider"></a>耗用 ContentProvider

`ContentProviders` 公開其功能，透過在中註冊的 Uri **AndroidManifest.xml**發行之資料的應用程式。 沒有一項慣例，其中 Uri 和公開的資料行應該是可做為常數，以便於繫結至資料。 Android 的內建`ContentProviders`所有提供方便類別具有參考中的資料結構的常數[ `Android.Providers` ](https://developer.xamarin.com/api/namespace/Android.Provider/)命名空間。


<a name="Built-In_Providers" />

### <a name="built-in-providers"></a>內建提供者

Android 提供存取各種系統和使用者資料使用`ContentProviders`:

- *瀏覽器*&ndash;書籤和瀏覽器歷程記錄 (需要使用權限`READ_HISTORY_BOOKMARKS`及/或`WRITE_HISTORY_BOOKMARKS`)。

- *CallLog* &ndash;最近電話或收到與裝置。

- *連絡人*&ndash;的詳細資訊，從使用者的連絡人清單，包括使用者、 電話、 相片和群組。

- *MediaStore* &ndash;使用者的裝置的內容: （專輯、 演出者、 內容類型，播放清單） 的音訊、 影像 （包含縮圖） （& s) 視訊。

- *設定*&ndash;整個系統的裝置設定與喜好設定。

- *UserDictionary* &ndash;用於預測的文字輸入使用者定義的字典內容。

- *語音信箱*&ndash;語音郵件訊息的歷程記錄。


<a name="Classes_Overview" />

## <a name="classes-overview"></a>類別概觀

使用時使用的主要類別`ContentProvider`如下所示：

[![內容提供者應用程式和 Consuming 應用程式互動的類別圖表](how-it-works-images/classdiagram1.png)](how-it-works-images/classdiagram1.png)

在此圖中，`ContentProvider`實作查詢，並註冊 URI 的其他應用程式用來尋找資料。 `ContentResolver`來作為 'proxy' `ContentProvider` （查詢、 插入、 更新和刪除的方法）。 `SQLiteOpenHelper`包含所使用的資料`ContentProvider`，但不是直接公開給使用應用程式。
`CursorAdapter`傳遞所傳回的資料指標`ContentResolver`中顯示`ListView`。 `UriMatcher`會處理查詢時，會剖析 Uri 的協助程式類別。

下面會描述每個類別的用途：

- **ContentProvider** &ndash;實作抽象類別的方法來公開資料。 應用程式開發介面可供其他類別和透過 Uri 屬性加入至類別定義的應用程式。

- **SQLiteOpenHelper** &ndash;協助實作 SQLite 資料存放區所公開`ContentProvider`。

- **UriMatcher** &ndash;使用`UriMatcher`中您`ContentProvider`實作，以協助管理可用來查詢內容的 Uri。

- **ContentResolver** &ndash;耗用程式碼會使用`ContentResolver`存取`ContentProvider`執行個體。 兩個類別一起負責處理序間通訊問題，讓資料輕鬆地在應用程式之間共用。 使用程式碼永遠不會建立`ContentProvider`類別明確; 相反地，建立根據 Uri 所公開的資料指標來存取此資料`ContentProvider`應用程式。

- **CursorAdapter** &ndash;使用`CursorAdapter`或`SimpleCursorAdapter`來顯示資料存取透過`ContentProvider`。

`ContentProvider` API 可讓取用者執行各種不同的作業資料，例如：

-  查詢以傳回清單或個別記錄的資料。
-  修改個別記錄。
-  加入新的記錄。
-  刪除記錄。

本文件包含使用系統提供的範例`ContentProvider`，以及實作自訂的簡單唯讀範例`ContentProvider`。

