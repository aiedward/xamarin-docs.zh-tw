---
title: 內容提供者的工作方式
ms.prod: xamarin
ms.assetid: B9E2EF89-7EBE-45F5-1ED9-7D2C70BE792C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: e61be6f0189eb825c15fd75764a16706e588ebc9
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "73020522"
---
# <a name="how-content-providers-work"></a>內容提供者的工作方式

`ContentProvider` 互動中牽涉到兩個類別：

- **ContentProvider** &ndash; 會執行 API，以標準方式公開一組資料。 主要的方法包括查詢、插入、更新和刪除。

- **ContentResolver** &ndash; 與 `ContentProvider` 通訊的靜態 proxy，從相同的應用程式或從另一個應用程式中存取其資料。

內容提供者通常會受到 SQLite 資料庫的支援，但 API 表示使用程式碼不需要知道任何關於基礎 SQL 的事項。 查詢是透過使用常數來參考資料行名稱的 Uri 來完成（以減少基礎資料結構的相依性），而且會傳回 `ICursor`，供取用的程式碼反復使用。

## <a name="consuming-a-contentprovider"></a>使用 ContentProvider

`ContentProviders` 會透過在發行資料之應用程式的**androidmanifest.xml**中註冊的 Uri 來公開其功能。 有一個慣例，即公開的 Uri 和資料行應該以常數的形式提供，讓您可以輕鬆地系結至資料。 Android 的內建 `ContentProviders` 全都提供便利的類別，其中包含參考[`Android.Providers`](xref:Android.Provider)命名空間中之資料結構的常數。

### <a name="built-in-providers"></a>內建提供者

Android 使用 `ContentProviders`來提供各種系統和使用者資料的存取權：

- *瀏覽器*&ndash; 書簽和瀏覽器歷程記錄（需要許可權 `READ_HISTORY_BOOKMARKS` 和/或 `WRITE_HISTORY_BOOKMARKS`）。

- *CallLog* &ndash; 在裝置上進行或接收的最近呼叫。

- *連絡人*&ndash; 使用者連絡人清單中的詳細資訊，包括人員、手機、相片 & 群組。

- *MediaStore*使用者裝置的 &ndash; 內容：音訊（專輯、演出者、內容、播放清單）、影像（包括縮圖） & 影片。

- *設定*會 &ndash; 全系統裝置設定和喜好設定。

- *UserDictionary*用於預測文字輸入的使用者定義字典 &ndash; 內容。

- *語音信箱 &ndash; 語音*信箱訊息的歷程記錄。

## <a name="classes-overview"></a>類別總覽

使用 `ContentProvider` 時所使用的主要類別如下所示：

[內容提供者應用程式和取用應用程式互動的 ![類別圖表](how-it-works-images/classdiagram1.png)](how-it-works-images/classdiagram1.png#lightbox)

在此圖中，`ContentProvider` 會執行查詢，並註冊其他應用程式用來尋找資料的 URI。 `ContentResolver` 會做為 `ContentProvider` 的「proxy」（查詢、插入、更新和刪除方法）。 `SQLiteOpenHelper` 包含 `ContentProvider`所使用的資料，但不會直接公開給取用應用程式。
`CursorAdapter` 會傳遞 `ContentResolver` 所傳回的資料指標，以便在 `ListView`中顯示。 `UriMatcher` 是在處理查詢時剖析 Uri 的 helper 類別。

每個類別的用途如下所述：

- **ContentProvider** &ndash; 會執行此抽象類別的方法來公開資料。 API 可透過新增至類別定義的 Uri 屬性，提供給其他類別和應用程式使用。

- **SQLiteOpenHelper** &ndash; 可協助執行 `ContentProvider`所公開的 SQLite 資料存放區。

- **UriMatcher** &ndash; 在 `ContentProvider` 的執行中使用 `UriMatcher`，以協助管理用來查詢內容的 uri。

- **ContentResolver** &ndash; 取用程式碼會使用 `ContentResolver` 來存取 `ContentProvider` 實例。 這兩個類別會共同處理進程間的通訊問題，讓資料能夠輕鬆地在應用程式之間共用。 使用程式碼絕不會建立 `ContentProvider` 類別明確;相反地，您可以根據 `ContentProvider` 應用程式所公開的 Uri 來建立資料指標，來存取資料。

- **CursorAdapter** &ndash; 使用 `CursorAdapter` 或 `SimpleCursorAdapter` 來顯示透過 `ContentProvider`存取的資料。

`ContentProvider` API 可讓取用者對資料執行各種作業，例如：

- 查詢資料以傳回清單或個別記錄。
- 修改個別記錄。
- 加入新的記錄。
- 刪除記錄。

本檔包含的範例會使用系統提供的 `ContentProvider`，以及可執行自訂 `ContentProvider`的簡單唯讀範例。
