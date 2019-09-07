---
title: 內容提供者的工作方式
ms.prod: xamarin
ms.assetid: B9E2EF89-7EBE-45F5-1ED9-7D2C70BE792C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 9d8710b3b88b59871b88a1d42ec4f4bb3e515ff5
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70756511"
---
# <a name="how-content-providers-work"></a>內容提供者的工作方式

`ContentProvider`互動中牽涉到兩個類別：

- **ContentProvider**&ndash;執行以標準方式公開一組資料的 API。 主要的方法包括查詢、插入、更新和刪除。

- **ContentResolver**靜態 proxy，可從相同的`ContentProvider`應用程式或從另一個應用程式，與進行通訊以存取其資料。 &ndash;

內容提供者通常會受到 SQLite 資料庫的支援，但 API 表示使用程式碼不需要知道任何關於基礎 SQL 的事項。 查詢是透過使用常數來參考資料行名稱的 Uri 來完成（以減少基礎資料結構的相依性）， `ICursor`並傳回以供取用的程式碼反復查看。

## <a name="consuming-a-contentprovider"></a>使用 ContentProvider

`ContentProviders`透過在發行資料之應用程式的**androidmanifest.xml**中註冊的 Uri 來公開其功能。 有一個慣例，即公開的 Uri 和資料行應該以常數的形式提供，讓您可以輕鬆地系結至資料。 Android 的內`ContentProviders`建全都提供便利的類別，這些常數會參考[`Android.Providers`](xref:Android.Provider)命名空間中的資料結構。

### <a name="built-in-providers"></a>內建提供者

Android 可讓您使用`ContentProviders`下列專案來存取各種系統和使用者資料：

- *瀏覽器*書簽和瀏覽器歷程記錄（ `READ_HISTORY_BOOKMARKS`需要許可權和`WRITE_HISTORY_BOOKMARKS`/或）。 &ndash;

- *CallLog*&ndash;最近對裝置發出或收到的呼叫。

- *連絡人*&ndash;使用者連絡人清單中的詳細資訊，包括人員、手機、相片 & 群組。

- *MediaStore*&ndash;使用者裝置的內容：音訊（專輯、演出者、內容、播放清單）、影像（包括縮圖） & 影片。

- *設定*&ndash;全系統裝置的設定和喜好設定。

- *UserDictionary*&ndash;用於預測文字輸入之使用者定義字典的內容。

- *語音信箱*&ndash;語音信箱訊息的歷程記錄。

## <a name="classes-overview"></a>類別總覽

使用時`ContentProvider`所用的主要類別如下所示：

[![內容提供者應用程式和取用應用程式互動的類別圖表](how-it-works-images/classdiagram1.png)](how-it-works-images/classdiagram1.png#lightbox)

在此圖中， `ContentProvider`會執行查詢，並註冊其他應用程式用來尋找資料的 URI。 會做為`ContentProvider` （查詢、插入、更新和刪除方法）的「proxy」。 `ContentResolver` 包含所使用`ContentProvider`的資料，但不會直接公開給取用應用程式。 `SQLiteOpenHelper`
會傳遞所傳回`ContentResolver`的資料指標，以便在中`ListView`顯示。 `CursorAdapter` `UriMatcher`是在處理查詢時剖析 uri 的 helper 類別。

每個類別的用途如下所述：

- **ContentProvider**&ndash;執行這個抽象類別的方法來公開資料。 API 可透過新增至類別定義的 Uri 屬性，提供給其他類別和應用程式使用。

- **SQLiteOpenHelper**協助執行所公開`ContentProvider`的 SQLite 資料存放區。 &ndash;

- **UriMatcher**在您的執行`UriMatcher`中使用，以協助管理用來查詢內容的 uri。 `ContentProvider` &ndash;

- **ContentResolver**使用程式碼會使用`ContentProvider` 來存取實例。`ContentResolver` &ndash; 這兩個類別會共同處理進程間的通訊問題，讓資料能夠輕鬆地在應用程式之間共用。 使用程式碼絕不會`ContentProvider`建立類別明確，而是會藉由建立資料指標（根據應用程式所`ContentProvider`公開的 Uri）來存取資料。

- **CursorAdapter**&ndash; `ContentProvider`使用`CursorAdapter`或來顯示`SimpleCursorAdapter`透過存取的資料。

此`ContentProvider` API 可讓取用者對資料執行各種作業，例如：

- 查詢資料以傳回清單或個別記錄。
- 修改個別記錄。
- 加入新的記錄。
- 刪除記錄。

本檔包含的範例會使用系統提供`ContentProvider`的，以及可執行自訂`ContentProvider`的簡單唯讀範例。
