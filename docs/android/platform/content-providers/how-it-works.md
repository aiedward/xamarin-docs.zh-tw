---
title: 如何內容提供者工作
ms.prod: xamarin
ms.assetid: B9E2EF89-7EBE-45F5-1ED9-7D2C70BE792C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: df4c2e10e34c308e4fadb44fba9c6a14714ae1b9
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60952843"
---
# <a name="how-content-providers-work"></a>如何內容提供者工作

有兩種類別參與`ContentProvider`互動：

- **ContentProvider** &ndash;實作標準的方式公開一組資料的 API。 主要的方式是查詢、 插入、 更新和刪除。

- **ContentResolver** &ndash;進行通訊的靜態 proxy`ContentProvider`存取其資料，或從相同的應用程式內從另一個應用程式。

內容提供者通常為後盾的 SQLite 資料庫中，但 API 可讓您表示，使用程式碼不需要知道任何關於基礎 SQL。 查詢已透過 Uri 參考 （以減少在基礎資料結構上的相依性） 的資料行名稱，使用常數和`ICursor`来重複使用的程式碼會傳回。


## <a name="consuming-a-contentprovider"></a>使用 ContentProvider

`ContentProviders` 公開其功能，透過在中註冊的 Uri **AndroidManifest.xml**將資料發佈的應用程式。 沒有一種命名慣例，Uri 和公開的資料行應該是可讓您輕鬆地繫結至資料的常數。 Android 的內建`ContentProviders`所有提供便利的類別具有參考中的資料結構的常數[ `Android.Providers` ](https://developer.xamarin.com/api/namespace/Android.Provider/)命名空間。



### <a name="built-in-providers"></a>內建提供者

Android 提供存取各種不同的系統和使用者資料使用`ContentProviders`:

- *瀏覽器*&ndash;書籤和瀏覽器歷程記錄 (需要權限`READ_HISTORY_BOOKMARKS`及/或`WRITE_HISTORY_BOOKMARKS`)。

- *CallLog* &ndash;最近電話或收到與裝置。

- *連絡人*&ndash;的詳細資訊，從使用者的連絡人清單，包括人員、 手機、 相片和群組。

- *MediaStore* &ndash;使用者的裝置的內容： 音訊 （專輯、 演出者、 內容類型、 播放清單）、 （包括縮圖） 的映像和影片。

- *設定*&ndash;全系統的裝置設定與喜好設定。

- *UserDictionary* &ndash;用於預測文字輸入的使用者自訂字典的內容。

- *語音信箱*&ndash;語音信箱訊息的歷程記錄。



## <a name="classes-overview"></a>類別概觀

使用時所使用的主要類別`ContentProvider`如下所示：

[![內容提供者應用程式和取用應用程式互動的類別圖表](how-it-works-images/classdiagram1.png)](how-it-works-images/classdiagram1.png#lightbox)

在此圖中，`ContentProvider`實作查詢，並註冊 URI 的其他應用程式用來找出資料。 `ContentResolver`來做為 'proxy' `ContentProvider` （查詢、 插入、 更新和刪除方法）。 `SQLiteOpenHelper`包含所使用的資料`ContentProvider`，但它不會直接對公開使用應用程式。
`CursorAdapter`傳遞所傳回的資料指標`ContentResolver`中顯示`ListView`。 `UriMatcher`會處理查詢時，會剖析 Uri 的協助程式類別。

每個類別的用途如下所述。

- **ContentProvider** &ndash;實作這個抽象類別的方法來公開資料。 API 可供其他類別和透過 Uri 屬性加入至類別定義的應用程式。

- **SQLiteOpenHelper** &ndash;可協助實作所公開的 SQLite 資料存放區`ContentProvider`。

- **UriMatcher** &ndash;使用`UriMatcher`中您`ContentProvider`實作，以協助您管理用來查詢內容的 Uri。

- **ContentResolver** &ndash;使用程式碼會使用`ContentResolver`存取`ContentProvider`執行個體。 一起的兩個類別會負責處理序間通訊問題，可讓應用程式之間輕鬆共用的資料。 使用程式碼永遠不會建立`ContentProvider`類別明確; 相反地，資料存取，藉由建立依據 Uri 所公開的資料指標`ContentProvider`應用程式。

- **CursorAdapter** &ndash;使用`CursorAdapter`或是`SimpleCursorAdapter`來顯示資料存取透過`ContentProvider`。

`ContentProvider` API 可讓取用者執行各種作業的資料，例如：

-  查詢以傳回清單或個別的記錄資料。
-  修改個別的記錄。
-  新增新的記錄。
-  刪除記錄。

本文件包含使用系統提供的範例`ContentProvider`，以及簡單的唯讀模式範例實作自訂`ContentProvider`。

